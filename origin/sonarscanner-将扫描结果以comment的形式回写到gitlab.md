# 一. Context

在Jenkins中做CI过程中,有一个步骤是代码编译完,使用sonar scanner扫描代码,检查静态代码中的语法错误,然后将代码发送到sonarqube,供项目经理查看代码质量.
sonarqube可以安装插件gitlab,让sonarscanner扫描完代码,将结果以gitlab注释的方式回写到提交的commit中.方便开发人员排查代码.  

以下操作过程各组件的版本
- **sonarqube**: 7.3 (build 15553)
- **sonarscanner**: 3.3.0.1492
- **sonarqube gitlab插件**: 4.0.0
    
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-1.png)
- **gitlab**: 10.8.4 ce
- **jenkins**:  2.150.2
 
Jenkins CI流水线是在使用Jenkins Slave(Kubernetes插件动态生成Slave POD)节点中来运行的,所以Sonarscanner,Maven等工具都是在Kubernetes Jenkins Slave镜像中已经安装好的.
 
# 二.操作
 
1. sonarqube 安装sonar-gitlab-plugin插件
   
   插件Github:https://github.com/gabrie-allaigre/sonar-gitlab-plugin/
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-2.png)

2. Sonarqube生成用户访问Token
   
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-3.png)

3. gitlab创建sonarscanner的用户,并生成AccessKey
   
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-4.png)

4. 在gitlab中将sonarqube加入到对应项目仓库的Members中
   
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-5.png)

5. 在Jenkins Pipeline中使用sonarscanner扫描代码

    ```
    stage("代码扫描"){
        steps{
            sh "sonar-scanner \
                    -Dsonar.projectKey=demo-springboot2 \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://sonarqube-sonarqube.apps.okd311.curiouser.com \
                    -Dsonar.login=058f4d4b905cba6123ffb093fa14b8f1fd9a75b \
                    -Dsonar.java.binaries=. \
                    -Dsonar.sourceEncoding=UTF-8 \
                    -Dsonar.gitlab.project_id=4 \
                    -Dsonar.analysis.mode=preview \
                    -Dsonar.issuesReport.html.enable=true \
                    -Dsonar.gitlab.commit_sha=$GIT_COMMIT \
                    -Dsonar.gitlab.ref_name=$GIT_BRANCH \
                    -Dsonar.sources=src \
                    -Dsonar.gitlab.user_token=e5D1Zo2132ikhGUcmSZZ \
                    -Dsonar.gitlab.url=http://gitlab.apps.okd311.curiouser.com/ \
                    -Dsonar.gitlab.ignore_certificate=true \
                    -Dsonar.gitlab.comment_no_issue=true \
                    -Dsonar.gitlab.max_global_issues=1000 \
                    -Dsonar.gitlab.unique_issue_per_inline=true"
        }
    }
    ```

# 三. 效果

![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-6.png)
![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-7.png)


# 四. soanarscanner参数详解

| Variable                                 | Comment                                                      | Type                              | Version  |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- | -------- |
| sonar.gitlab.url                         | GitLab url                                                   | Administration, Variable          | >= 1.6.6 |
| sonar.gitlab.max_global_issues           | Maximum number of anomalies to be displayed in the global comment | Administration, Variable          | >= 1.6.6 |
| sonar.gitlab.user_token                  | Token of the user who can make reports on the project, either global or per project | Administration, Project, Variable | >= 1.6.6 |
| sonar.gitlab.project_id                  | Project ID in GitLab or internal id or namespace + name or namespace + path or url http or ssh url or url or web | Project, Variable                 | >= 1.6.6 |
| sonar.gitlab.commit_sha                  | SHA of the commit comment                                    | Variable                          | >= 1.6.6 |
| sonar.gitlab.ref                         | Branch name or reference of the commit                       | Variable                          | < 3.0.0  |
| sonar.gitlab.ref_name                    | Branch name or reference of the commit                       | Variable                          | >= 1.6.6 |
| sonar.gitlab.max_blocker_issues_gate     | Max blocker issue for build failed (default 0). Note: only for preview mode | Project, Variable                 | >= 2.0.0 |
| sonar.gitlab.max_critical_issues_gate    | Max critical issues for build failed (default 0). Note: only for preview mode | Project, Variable                 | >= 2.0.0 |
| sonar.gitlab.max_major_issues_gate       | Max major issues for build failed (default -1 no fail). Note: only for preview mode | Project, Variable                 | >= 2.0.0 |
| sonar.gitlab.max_minor_issues_gate       | Max minor issues for build failed (default -1 no fail). Note: only for preview mode | Project, Variable                 | >= 2.0.0 |
| sonar.gitlab.max_info_issues_gate        | Max info issues for build failed (default -1 no fail). Note: only for preview mode | Project, Variable                 | >= 2.0.0 |
| sonar.gitlab.ignore_certificate          | Ignore Certificate for access GitLab, use for auto-signing cert (default false) | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.comment_no_issue            | Add a comment even when there is no new issue (default false) | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.disable_inline_comments     | Disable issue reporting as inline comments (default false)   | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.only_issue_from_commit_file | Show issue for commit file only (default false)              | Variable                          | >= 2.0.0 |
| sonar.gitlab.only_issue_from_commit_line | Show issue for commit line only (default false)              | Variable                          | >= 2.1.0 |
| sonar.gitlab.build_init_state            | State that should be the first when build commit status update is called (default pending) | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.disable_global_comment      | Disable global comment, report only inline (default false)   | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.failure_notification_mode   | Notification is in current build (exit-code) or in commit status (commit-status) (default commit-status) | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.global_template             | Template for global comment in commit                        | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.ping_user                   | Ping the user who made an issue by @ mentioning. Only for default comment (default false) | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.unique_issue_per_inline     | Unique issue per inline comment (default false)              | Administration, Variable          | >= 2.0.0 |
| sonar.gitlab.prefix_directory            | Add prefix when create link for GitLab                       | Variable                          | >= 2.1.0 |
| sonar.gitlab.api_version                 | GitLab API version (default `v4` or `v3`)                    | Administration, Variable          | >= 2.1.0 |
| sonar.gitlab.all_issues                  | All issues new and old (default false, only new)             | Administration, Variable          | >= 2.1.0 |
| sonar.gitlab.json_mode                   | Create a json report in root for GitLab EE (codeclimate.json or gl-sast-report.json) | Project, Variable                 | >= 3.0.0 |
| sonar.gitlab.query_max_retry             | Max retry for wait finish analyse for publish mode           | Administration, Variable          | >= 3.0.0 |
| sonar.gitlab.query_wait                  | Max retry for wait finish analyse for publish mode           | Administration, Variable          | >= 3.0.0 |
| sonar.gitlab.quality_gate_fail_mode      | Quality gate fail mode: error, warn or none (default error)  | Administration, Variable          | >= 3.0.0 |
| sonar.gitlab.issue_filter                | Filter on issue, if MAJOR then show only MAJOR, CRITICAL and BLOCKER (default INFO) | Administration, Variable          | >= 3.0.0 |
| sonar.gitlab.load_rules                  | Load rules for all issues (default false)                    | Administration, Variable          | >= 3.0.0 |
| sonar.gitlab.disable_proxy               | Disable proxy if system contains proxy config (default false) | Administration, Variable          | >= 4.0.0 |
| sonar.gitlab.merge_request_discussion    | Allows to post the comments as discussions (default false)   | Project, Variable                 | >= 4.0.0 |
| sonar.gitlab.ci_merge_request_iid        | The IID of the merge request if it’s pipelines for merge requests | Project, Variable                 | >= 4.0.0 |

# 五. 问题

1. 当项目是私有仓库时

    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-8.png)

2. 获取项目仓库的ProjectID

    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-9.png)
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-10.png)
    ![](/assets/sonarscanner-将扫描结果以comment的形式回写到gitlab-11.png)



# 参考链接：

1. https://gitlab.com/gitlab-org/gitlab-ce/issues/28342
2. https://www.cnblogs.com/amyzhu/p/8988519.html
