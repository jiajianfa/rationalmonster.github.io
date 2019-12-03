# **一、简介**

虽然我们有很多工具可以让开发工作变得更容易，但是发现和排查线上问题的过程仍然在很多时候让我们觉得很痛苦。当生产系统中产生了一个bug时，我们如何快速地得到报警？如何评估它的影响和紧迫性？如何快速地找到问题的根源？当hotfix完修复程序后，又如何知道它是否解决了问题？

Sentry在帮助我们与现有流程集成时回答了这些问题。例如，线上有一个bug，代码的某处逻辑的NullPointerException造成了这个问题，Sentry会立即发现错误，并通过邮件或其他基于通知规则的集成通知到相关责任人员，这个通知可以把我们引入到一个指示板，这个指示板为我们提供了快速分类问题所需的上下文，如：频率、用户影响、代码那一部分受到影响以及那个团队可能是问题的所有者。

Sentry是一个实时事件的日志聚合平台。

Sentry支持的客户端SDK：

![1573726872645](../assets/sentry-1.png)

那么Sentry是如何实现实时日志监控报警的呢？

首先，Sentry是一个C/S架构，分为服务端和客户端 。SDK我们需要在自己应用中集成Sentry的SDK才能在应用发生错误是将错误信息发送给Sentry服务端。根据语言和框架的不同，我们可以选择自动或自定义设置特殊的错误类型报告给Sentry服务端。

而Sentry的服务端分为web、cron、worker这几个部分，应用（客户端）发生错误后将错误信息上报给web，web处理后放入消息队列或Redis内存队列，worker从队列中消费数据进行处理。

官方文档：https://docs.sentry.io/


# **二、部署**

Sentry 本身是基于 Django 开发的，需要Postgresql、 Redis

## **Kubernetes Helm Charts**

- **Redis Charts**: https://github.com/helm/charts/tree/master/stable/redis
- **Postgresql Charts**: https://github.com/helm/charts/tree/master/stable/postgresql
- **Sentry Charts**: https://github.com/helm/charts/tree/master/stable/sentry

## **OpenShift 资源声明文件部署**

[资源声明文件](https://github.com/RationalMonster/OKD/tree/master/MiddlewareOpenshiftTemplates/Sentry)



# **三、配置**

在Sentry完成一个项目的设置后，您将获得一个我们称之为DSN或数据源名称的值.它看起来很像一个标准的URL，但它实际上只是Sentry SDK所需的配置的标识.它由几个部分组成，包括协议，公共密钥和密钥，服务器地址和项目标识符。

```bash
'{PROTOCOL}://{PUBLIC_KEY}:{SECRET_KEY}@{HOST}/{PATH}{PROJECT_ID}'
```



# 四、Sentry集成LDAP认证登陆

由于Sentry没有集成LDAP认证的官方插件，所以推荐了第三方插件sentry-ldap-auth来实现。

官方说明文档：https://docs.sentry.io/server/plugins/#rd-party-plugins

Sentry-ldap-auth插件地址：https://github.com/Banno/getsentry-ldap-auth

第三方制作集成Sentry-ldap-auth插件Docker镜像的GitHub：https://github.com/locaweb/docker-sentry-ldap

## 1、修改默认镜像，添加插件

Dockerfile

```bash
FROM docker.io/sentry:9.1.2

RUN apt-get -qq update && DEBIAN_FRONTEND=noninteractive apt-get install -y -q libxslt1-dev libxml2-dev libpq-dev libldap2-dev libsasl2-dev libssl-dev

RUN echo "sentry-ldap-auth\npython-decouple==3.0\ndjango-auth-ldap <=1.2.17" > /tmp/req.txt && \
    pip install -r /tmp/req.txt && \
    apt-get remove -y -q libxslt1-dev libxml2-dev libpq-dev libldap2-dev libsasl2-dev libssl-dev && \
    rm -rf /var/lib/apt/lists/* /tmp/req.txt
```

Makefile

```bash
IMAGE_BASE = harbor.curiouser.cn
IMAGE_NAME = tools/sentry-9.1.2-ldap
IMAGE_VERSION = v1
all: build
build:
        docker build --rm -f Dockerfile -t ${IMAGE_BASE}/${IMAGE_NAME}:${IMAGE_VERSION} .
push:
        docker push ${IMAGE_BASE}/${IMAGE_NAME}:${IMAGE_VERSION}
```

## 2、修改部署charts中的configmap，添加插件认证代码

Sentry Charts目录下templates/configmap.yaml

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
# .....上文省略......
data:
  # .....上文省略......
  sentry.conf.py: |-
  # .....上文省略......
    ####################
    # LDAP settings ##
    ####################
    SENTRY_USE_LDAP = env('SENTRY_USE_LDAP', False)
    if SENTRY_USE_LDAP:
        import ldap
        from django_auth_ldap.config import LDAPSearch, GroupOfUniqueNamesType
 
        AUTH_LDAP_SERVER_URI = env('LDAP_SERVER', 'ldap://localhost：389')
        AUTH_LDAP_BIND_DN = env('LDAP_BIND_DN', '')
        AUTH_LDAP_BIND_PASSWORD = env('LDAP_BIND_PASSWORD', '')
 
        # 设置搜索用户配置
        AUTH_LDAP_USER_SEARCH = LDAPSearch(
            env('LDAP_USER_DN'),
            ldap.SCOPE_SUBTREE,
            env('LDAP_USER_FILTER', '(&(objectClass=inetOrgPerson)(cn=%(user)s))')
        )
 
        # 设置搜索用户组配置
        #AUTH_LDAP_GROUP_SEARCH = LDAPSearch(
        #    env('LDAP_GROUP_DN', ''),
        #    ldap.SCOPE_SUBTREE,
        #    env('LDAP_GROUP_FILTER', '(objectClass=groupOfUniqueNames)')
        #)
 
        if env('LDAP_GROUP_TYPE', '') == 'groupOfUniqueNames':
            AUTH_LDAP_GROUP_TYPE = GroupOfUniqueNamesType()
        elif env('LDAP_GROUP_TYPE', '') == 'groupOfNames':
            AUTH_LDAP_GROUP_TYPE = GroupOfNamesType()
        elif env('LDAP_GROUP_TYPE', '') == 'posixGroup':
            AUTH_LDAP_GROUP_TYPE = PosixGroupType()
        elif env('LDAP_GROUP_TYPE', '') == 'nestedGroupOfNames':
            AUTH_LDAP_GROUP_TYPE = NestedGroupOfNamesType()
 
        AUTH_LDAP_REQUIRE_GROUP = None
        AUTH_LDAP_DENY_GROUP = None
 
        # 用户属性Mapping
        AUTH_LDAP_USER_ATTR_MAP = {
            'username': env('LDAP_SENTRY_USER_FIELD', 'cn'),
            'name': env('LDAP_MAP_FULL_NAME', 'sn'),
            'email': env('LDAP_MAP_MAIL', 'mail')
        }
 
        # 用户搜索缓存
        AUTH_LDAP_FIND_GROUP_PERMS = env('LDAP_FIND_GROUP_PERMS', False)
        AUTH_LDAP_CACHE_GROUPS = env('LDAP_CACHE_GROUPS', True)
        AUTH_LDAP_GROUP_CACHE_TIMEOUT = env('LDAP_GROUP_CACHE_TIMEOUT', 3600)
 
        # 用户在Sentry中的对应关系
        AUTH_LDAP_DEFAULT_SENTRY_ORGANIZATION = env('LDAP_DEFAULT_SENTRY_ORGANIZATION','sentry')
        AUTH_LDAP_SENTRY_ORGANIZATION_ROLE_TYPE = 'manager'
        AUTH_LDAP_SENTRY_SUBSCRIBE_BY_DEFAULT = False
        AUTH_LDAP_SENTRY_ORGANIZATION_GLOBAL_ACCESS = True
        AUTH_LDAP_SENTRY_USERNAME_FIELD = env('LDAP_SENTRY_USERNAME_FIELD', 'cn')
        AUTHENTICATION_BACKENDS = AUTHENTICATION_BACKENDS + ('sentry_ldap_auth.backend.SentryLdapBackend',)
        ldap_is_active    = env('LDAP_GROUP_ACTIVE', '')
        ldap_is_superuser = env('LDAP_GROUP_SUPERUSER', '')
        ldap_is_staff     = env('LDAP_GROUP_STAFF', '')
 
        if ldap_is_active or ldap_is_superuser or ldap_is_staff:
            AUTH_LDAP_USER_FLAGS_BY_GROUP = {
                'is_active': ldap_is_active,
                'is_superuser': ldap_is_superuser,
                'is_staff': ldap_is_staff,
            }
        # django_auth_ldap日志输出
        import logging
        logger = logging.getLogger('django_auth_ldap')
        logger.addHandler(logging.StreamHandler())
        ldap_loglevel = getattr(logging, env('LDAP_LOGLEVEL', 'DEBUG'))
        logger.setLevel(ldap_loglevel)
        LOGGING['overridable'] = ['sentry', 'django_auth_ldap']
        LOGGING['loggers']['django_auth_ldap'] = {'handlers': ['console'],'level': env('LDAP_LOGLEVEL','DEBUG')}
 # .....下文省略......
```

## 3、在Values文件中添加Web组件部署时的环境变量

values-dev.yaml

```yaml
...
web:
  ...
  env:
    ...
    - name: TZ
      value: Asia/Shanghai
    - name: SENTRY_USE_LDAP
      value: 'True'
    - name: LDAP_SERVER
      value: 'ldap://openldap.openldap.svc:389'
    - name: LDAP_BIND_DN
      value: 'cn=readonly,dc=curiouser,dc=com'
    - name: LDAP_BIND_PASSWORD
      value: readonly
    - name: LDAP_USER_DN
      value: 'ou=employee,dc=curiouser,dc=com'
    - name: LDAP_USER_FILTER
      value: >-
        (&(memberOf=cn=sentry,ou=apps,dc=curiouser,dc=com)(cn=%(user)s))
    - name: LDAP_SENTRY_USER_FIELD
      value: 'cn'
    - name: LDAP_MAP_FULL_NAME
      value: 'sn'
    - name: LDAP_MAP_MAIL
      value: 'mail'
    - name: LDAP_GROUP_TYPE
      value: groupOfUniqueNames
    - name: LDAP_GROUP_CACHE_TIMEOUT
      value: '3600'
    - name: LDAP_CACHE_GROUPS
      value: 'True'
    - name: LDAP_DEFAULT_SENTRY_ORGANIZATION
      value: sentry
    - name: LDAP_FIND_GROUP_PERMS
      value: 'False'
    - name: LDAP_SENTRY_USERNAME_FIELD
      value: 'cn'
    - name: LDAP_LOGLEVEL
      value: INFO
```

# 五、Kubernetes event 客户端



镜像GitHub：https://github.com/getsentry/sentry-docs/issues/1330



# 六、Sentry-cli客户端

## 1. 下载安装

- 手动下载安装

  - github下载地址：https://github.com/getsentry/sentry-cli/releases/

- 脚本下载安装

  ```bash
  curl -sL https://sentry.io/get-cli/ | bash
  ```

- NPM下载安装

  ```bash
  # 全局安装
  npm install -g @sentry/cli --unsafe-perm 
  ```

- Docker Image

  ```bash
  $ docker pull getsentry/sentry-cli
  $ docker run --rm -v $(pwd):/work getsentry/sentry-cli --help
  ```

## 2. 配置

- 全局配置文件：~/.sentryclirc

  - INI语法格式

  ```properties
  [auth]
  token=your-auth-token
  ```

- 环境变量

  - 默认会读取当前`.env` 文件加载环境变量。可设置`SENTRY_LOAD_DOTENV=0`禁止

  ```bash
  export SENTRY_AUTH_TOKEN=your-auth-token
  ```

- 命令行参数

  ```bash
  sentry-cli --auth-token your-auth-token
  ```

- 项目配置文件

  - 支持加载`.properties`，也可通过环境变量`SENTRY_PROPERTIES`指定项目配置文件路径 

| 环境变量的形式                  | 配置文件中的形式          | 描述                                                         |
| ------------------------------- | ------------------------- | ------------------------------------------------------------ |
| **SENTRY_AUTH_TOKEN**           | **auth.token**            | 与Sentry服务端通信用的认证Token                              |
| **SENTRY_API_KEY**              | **auth.api_key**          | The legacy API key for authentication if you have one.       |
| **SENTRY_URL**                  | **defaults.url**          | The URL to use to connect to sentry. This defaults to `https://sentry.io/`. |
| **SENTRY_ORG**                  | **defaults.org**          | The slug of the organization to use for a command.           |
| **SENTRY_PROJECT**              | **defaults.project**      | The slug of the project to use for a command.                |
|                                 | **http.keepalive**        | This ini only setting is used to control the behavior of the SDK with regards to HTTP keepalives. The default is *true* but it can be set to *false* to disable keepalive support. |
| **http_proxy**                  | **http.proxy_url**        | The URL that should be used for the HTTP proxy. The standard `http_proxy` environment variable is also honored. Note that it is lowercase. |
|                                 | **http.proxy_username**   | This ini only setting sets the proxy username in case proxy authentication is required. |
|                                 | **http.proxy_password***  | This ini only setting sets the proxy password in case proxy authentication is required. |
|                                 | **http.verify_ssl**       | This can be used to disable SSL verification when set to false. You should never do that unless you are working with a known self signed server locally. |
|                                 | **http.check_ssl_revoke** | If this is set to false then SSL revocation checks are disabled on Windows. This can be useful when working with a corporate SSL MITM proxy that does not properly implement revocation checks. Do not use this unless absolutely necessary. |
| **SENTRY_HTTP_MAX_RETRIES**     | **http.max_retries**      | Sets the maximum number of retry attempts for upload operations (e.g., uploads of release files and debug symbols). The default is `5`. |
|                                 | **ui.show_notifications** | If this is set to false some operating system notifications are disabled. This currently primarily affects xcode builds which will not show notifications for background builds. |
| **SENTRY_LOG_LEVEL**            | **log.level**             | Configures the log level for the SDK. The default is `warning`. If you want to see what the library is doing you can set it to `info` which will spit out more information which might help to debug some issues with permissions. |
|                                 | **dsym.max_upload_size**  | Sets the maximum upload size in bytes (before compression) of debug symbols into one batch. The default is 35MB or 100MB (depending on the version of sentry-cli) which is suitable for sentry.io but if you are using a different sentry server you might want to change this limit if necessary. |
| **SENTRY_NO_PROGRESS_BAR**      |                           | If set to `1`, then `sentry-cli` will not display progress bars for any operations. |
| **SENTRY_DISABLE_UPDATE_CHECK** | **update.disable_check**  | If set to `true`, then the automatic update check in sentry-cli is disabled. This was introduced in 1.17. Versions before that did not include an update check. The update check is also not enabled for npm based installations of sentry-cli at the moment. |
| **DEVICE_FAMILY**               | **device.family**         | Device family value reported to Sentry.                      |
| **DEVICE_MODEL**                | **device.model**          | Device model value reported to Sentry.                       |

验证配置文件

```bash
sentry-cli info
```








