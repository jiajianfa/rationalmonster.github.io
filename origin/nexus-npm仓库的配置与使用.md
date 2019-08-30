# NPM仓库的配置与私用

# 一、npm仓库的配置信息

- **Group类型仓库**
    - NPM
      - `npm-taobao`
      - `npm-cnpm`
      - `npm-cnpm`
- **Proxy类型仓库**
    - `npm-taobao`：http://registry.npm.taobao.org/
    - `npm-cnpm`：http://registry.cnpmjs.org/
- **Hosted类型仓库**
    - `npm-hosted`

# 二、使用NPM仓库

```bash
npm config set registry http://Nexus-IP地址:8081/repository/NPM/
```

# 三、发布制品到NPM的Hosted仓库

```bash
echo "hello" >> test
npm init
#   package name: (test) sadsada
#   version: (1.0.0)
#   description:
#   entry point: (index.js) test
#   test command:
#   git repository:
#   keywords:
#   author:
#   license: (ISC)
#   About to write to /root/test/package.json:
#   {
#     "name": "sadsada",
#     "version": "1.0.0",
#     "description": "",
#     "main": "f",
#     "scripts": {
#       "test": "echo \"Error: no test specified\" && exit 1"
#     },
#     "author": "",
#     "license": "ISC"
#   }
npm login --registry http://Nexus-IP地址:8081/repository/NPM-Hosted/
# Username: admin
#Password: *****
# Email: (this IS public) asdad@sada.com
npm publish -registry http://Nexus-IP地址:8081/repository/NPM-Hosted/
```

# 四、使用nrm工具切换npm仓库

Github地址：https://github.com/Pana/nrm
帮助快速切换npm仓库源。默认已经配置了npm,yarn,taobao,cnpm,nj,npmMirror,edunpm等常见的仓库源。

## 1. 安装

```bash
npm install nrm -g
```

## 2. 命令详解

```bash
$ nrm -h
Usage: nrm [options] [command]
Options:
  -V, --version output the version number
  -h, --help output usage information
Commands:
  ls List all the registries
  current Show current registry name
  use <registry> Change registry to registry
  add <registry> <url> [home] Add one custom registry
  set-auth [options] <registry> [value] Set authorize information for a custom registry with a base64 encoded string or username and pasword
  set-email <registry> <value> Set email for a custom registry
  set-hosted-repo <registry> <value> Set hosted npm repository for a custom registry to publish packages
  del <registry> Delete one custom registry
  home <registry> [browser] Open the homepage of registry with optional browser
  publish [options] [<tarball>|<folder>] Publish package to current registry if current registry is a custom registry.
   if you're not using custom registry, this command will run npm publish directly
  test [registry] Show response time for specific or all registries
  help Print this help
```

## 3. 常用命令

查看默认支持的npm 仓库

```bash
$ nrm ls
* npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
  taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/
# "*"编注的仓库代表当前使用的仓库
```

添加私有的npm仓库

```bash
nrm add curiouser http://nexus.apps.okd311.curiouser.com/repository/NPM
```

切换npm仓库

```bash
 nrm use 仓库名
```

删除仓库

```bash
 nrm del 仓库名
```

测试仓库速度

```bash
nrm test
```
