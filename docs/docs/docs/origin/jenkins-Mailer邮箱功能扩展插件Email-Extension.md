
# jenkins Mailer邮箱功能扩展插件Email-Extension
## 一、Context

Jenkins自带的邮件插件功能太弱，有个邮箱扩展插件。

官方文档WIKI：https://wiki.jenkins.io/display/JENKINS/Email-ext+plugin

优势：
* 邮件格式改为HTML，更美观
* 使用模板来配置邮件内容
* 为不同的Job配置不一样的收件人
* 为不同的事件配置不一样的trigger
* 在Jenkins pipeline中集成发送邮件通知功能

## 二、插件安装配置

1. 安装
   
    ![](/assets/jenkins-Mailer邮箱功能扩展插件Email-Extension-1.png)
2. 配置
   
    ![](/assets/jenkins-Mailer邮箱功能扩展插件Email-Extension-2.png)


## 三、使用

1. Jobs
   
    ![](/assets/jenkins-Mailer邮箱功能扩展插件Email-Extension-3.png)
    ![](/assets/jenkins-Mailer邮箱功能扩展插件Email-Extension-4.png)
2. Pipeline中
   
    ```bash
    pipeline{
        ...
        post {
            always {
                emailext attachLog: true, body: '''
                    构建任务的完整日志详见见附件,Jenkins查看链接: $BUILD_URL''', subject: '$PROJECT_NAME的第$BUILD_NUMBER次构建$BUILD_STATUS !', to: '*******@163.com'
            }
        }

    }
    ```
