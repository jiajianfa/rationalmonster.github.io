

# Jenkins Pipeline共享库Shared Libraries

# 一、简介

随着Job的增多和pipeline的功能越来越复杂，Pipeline代码冗余度高。所以可以将一些公共的pipeline抽象做成模块代码，在各种项目pipeline之间共享核心实现，以减少冗余并保证所有job在构建的时候会调用最新的共享库代码。这时就可以用到pipline的共享库Shared Libraries功能



# 二、编写规则

- Shared Library通过库名称、代码检索方法（如SCM）、代码版本三个要素进行定义
- 库名称尽量简洁
- 每个*.groovy文件的基本名称应使用驼峰（camelCased）模式，*.txt（如果存在）可以包含格式化处理的文档。



```json
(root)
jenkins-shared-library
|____vars
|____src
|____resources
```





全局 Shared Library的方式,通过Manage Jenkins » Configure System » Global Pipeline Libraries 的方式可以添加一个或多个共享库





library 'my-shared-library@master'

library "my-shared-library@$BRANCH_NAME"