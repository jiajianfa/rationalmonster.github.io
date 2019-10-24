

# Jenkins Pipeline共享库Shared Libraries

# 一、简介

随着Job的增多和pipeline的功能越来越复杂，Pipeline代码冗余度高。所以可以将一些公共的pipeline抽象做成模块代码，在各种项目pipeline之间共享核心实现，同时可以放到SVM中进行版本控制。以减少冗余并保证所有job在构建的时候会调用最新的共享库代码。这时就可以用到pipline的共享库Shared Libraries功能。



# 二、共享库的编写规则



- Shared Library通过库名称、代码检索方法（如SCM）、代码版本三个要素进行定义
- 库名称尽量简洁
- 每个*.groovy文件的基本名称应使用驼峰（camelCased）模式，*.txt（如果存在）可以包含格式化处理的文档。



```json
jenkins-shared-library(root)
|____vars
|____src               # src 目录是标准的Java源码结构，目录中的类被称为类库(Library class)
					   #而@Library('global-shared-library@master') 就是一次性静态加载src目录下所有代码到classpath中
|____resources				
```



## 示例：













# 三、Jenkins配置全局的共享库

可在Jenkins中的**`Manage Jenkins`**  –>  **`Configure System`**　–> **`Global Pipeline Libraries`** 添加一个或多个全局的共享库，同时也可以在构建过程中的任何位置使用`library` step动作动态地配置引用共享库，详见[动态引用共享库](# 2. 动态引用共享库 )

![1571906388403](../assets/jenkins-sharelibiaries-1.png)



# 四、引用共享库

`@Library('my-shared-library-1@$Branch/Tag','my-shared-library-1@$Branch/Tag') _`

## 1. 引用默认配置的、多个、指定分支/tag的全局共享库

```groovy
#!groovy
// 引用默认配置的共享库
@Library('demo-shared-library') _

// 引用指定分支、tag的共享库代码
@Library('demo-shared-library@1.0') _

// 引用多个指定分支tag的共享库
@Library('my-shared-library-1@$Branch/Tag','my-shared-library-1@$Branch/Tag') _
```

## 2. 动态配置引用共享库

2.7版本后的`Shared Groovy Libraries`插件，增加了一个`library`的`setp`,可以随时在构建过程中引用共享库

```groovy
#!groovy

library 'my-shared-library@$BRANCH_NAME'

library "my-shared-library@${params.LIB_VERSION}"
library('my-shared-library').com.mycorp.pipeline.Utils.someStaticMethod()

library identifier: 'custom-lib@master', retriever: modernSCM(
  [$class: 'GitSCMSource',
   remote: 'git@git.mycorp.com:my-jenkins-utils.git',
   credentialsId: 'my-private-key'])
```

## 3. 调用第三方Java库



```grooxy
@Grab('org.apache.commons:commons-math3:3.4.1')
import org.apache.commons.math3.primes.Primes
```

引用完的第三方Java库后会缓存在Jenkins Master节点的`~/.groovy/grapes/` 目录下

# 五、调用共享库中的类、方法

- 在共享库`根目录/var`路径下定义类中的类、方法调用

  ```groovy
  #!groovy
  @Library('demo-shared-library@1.0') _
  
  
  ```

- 在共享库`根目录/src`路径下定义类中的类、方法调用

  ```groovy
  #!groovy
  @Library('demo-shared-library@1.0') _
  import com.mycorp.pipeline.somelib.UsefulClass
  
  def z = new org.foo.Zot()
  z.checkOutFrom(repo)
  
  ```

- 动态掉用共享库中的方法

  ```groovy
  #!groovy
  @Library('demo-shared-library@1.0') _
  
  
  ```

  

# 六、使用Jenkins Pipeline生成器生成动态引用共享库的代码

![1571909795787](../assets/jenkins-sharelibiaries-2.png)