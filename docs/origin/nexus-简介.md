# 一、Nexus简介

常见搭建私服的软件有：

- Apache基金会的Archiva——开源
- JFrog的Artifatory——核心开源
- Sonatype的Nexus——核心开源，具有社区版本免费使用。最流行的Maven仓库管理软件。

Nexus是一个强大的Maven仓库管理器，它极大地简化了本地内部仓库的维护和外部仓库的访问。如果使用了公共的Maven仓库服务器，可以从Maven中央仓库下载所需要的构件（Artifact），但这通常不是一个好的做法正常做法是在本地架设一个Maven仓库服务器，即利用Nexus私服可以只在一个地方就能够完全控制访问和部署在你所维护仓库中的每个Artifact。 Nexus在代理远程仓库的同时维护本地仓库，以降低中央仓库的负荷,节省外网带宽和时间，Nexus私服就可以满足这样的需要。

Nexus常用功能就是：指定私服的中央地址、将自己的Maven项目指定到私服地址、从私服下载中央库的项目索引、从私服仓库下载依赖组件、将第三方项目jar上传到私服供其他项目组使用。

- Nexus是一套“开箱即用”的系统不需要数据库，它使用文件系统加Lucene来组织数据。 
- Nexus使用ExtJS来开发界面，利用Restlet来提供完整的REST APIs，通过m2eclipse与Eclipse集成使用。 
- Nexus支持WebDAV与LDAP安全身份认证。 
- Nexus还提供了强大的仓库管理功能，构件搜索功能，它基于REST，友好的UI是一个extjs的REST客户端，它占用较少的内存，基于简单文件系统而非数据库。

**Nexus仓库种类**

- Hosted 类型的仓库，内部项目的发布仓库
- Releases 内部的模块中release模块的发布仓库
- Snapshots 发布内部的SNAPSHOT模块的仓库
- 3rd party 第三方依赖的仓库，这个数据通常是由内部人员自行下载之后发布上去
- Proxy 类型的仓库，从远程中央仓库中寻找数据的仓库
- Group 类型的仓库，组仓库用来方便我们开发人员进行设置的仓库

**Nexus版本**
- NEXUS OSS [OSS = Open Source Software，开源软件——免费]
- NEXUS PROFESSIONAL -FREE TRIAL [专业版本——收费]。

**Nexus安装**

Bundle包下载地址：https://www.sonatype.com/download-oss-sonatype

历史安装包下载：https://help.sonatype.com/repomanager3/download/download-archives---repository-manager-3

Nexus提供了两种安装方式，一种是内嵌Jetty的bundle，只要你有JRE就能直接运行。第二种方式是WAR，需要一个Servlet容器来运行,你只须简单的将其发布到web容器中即可使用。

**Nexus升级与数据迁移**

小版本可以直接升级，同样数据迁移也是可以的。（拷贝旧Nexus安装目录下的sonatype-work目录下的所有文件到新的Nexus安装目录下的sonatype-work目录中（也可以使用软连接））
