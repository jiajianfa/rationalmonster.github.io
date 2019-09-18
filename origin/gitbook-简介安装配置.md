# GitBook简介安装

# 一、GitBook简介

- gitbook 是一个基于node.js的命令行工具
- gitbook 支持markdown/asciiDoc语法格式构建书籍
- gitbook 支持输出静态网页和电子书等多种格式,其中默认输出静态网页格式
- gitbook 不仅支持本地构建书籍,还可以托管在gitbook 官网上，或者Github上

# 二、GitBook安装

## 1、安装NodeJs环境

NodeJs官网下载链接:https://nodejs.org/en/download/ 

### **`Linux`**：

以安装NodeJs 10.16.3为例

```bash
wget https://nodejs.org/dist/v10.16.3/node-v10.16.3-linux-x64.tar.xz && \
tar -xvf node-v10.16.3-linux-x64.tar.xz -C /opt/ && \
rm -rf node-v10.16.3-linux-x64.tar.xz && \
ln -s /opt/node-v10.16.3-linux-x64 /opt/nodejs && \
sed -i '$a export NODEJS_HOME=/opt/nodejs\nexport PATH=$PATH:$NODEJS_HOME/bin' /etc/profile && \
source /etc/profile && \
yum install gcc-c++ make -y && \
npm config set registry https://registry.npm.taobao.org && \
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/ && \
node -v && \
npm version
```

### **`Windows`**

直接在官网下载MSI格式的安装包进行安装

## 2、安装Gitbook CLI命令行工具

gitbook-cli 是 gitbook 的一个命令行工具, 通过它可以在电脑上安装和管理 gitbook 的多个版本.

```bash
npm install gitbook-cli -g
```

# 三、GitBook版本的管理

GitBook可以在本地安装多个版本并在执行命令的时候指定某个版本，如果指定的版本还没安装就会自动下载安装，下载后的GitBook会被放到~/.gitbook目录下。

```bash
$ gitbook --help
  Usage: gitbook [options] [command]
  Options:
    -v, --gitbook [version]  specify GitBook version to use
    -d, --debug              enable verbose error
    -V, --version            Display running versions of gitbook and gitbook-cli
    -h, --help               output usage information
  Commands:
    ls                        List versions installed locally
    current                   Display currently activated version
    ls-remote                 List remote versions available for install
    fetch [version]           Download and install a <version>
    alias [folder] [version]  Set an alias named <version> pointing to <folder>
    uninstall [version]       Uninstall a version
    update [tag]              Update to the latest version of GitBook
    help                      List commands for GitBook
    *                         run a command with a specific gitbook version

# 查看当前GitBook CLI版本
gitbook -V

# 列出本地安装版本
gitbook ls

# 列出当前使用版本
gitbook current

# 列出远程可用版本
gitbook ls-remote

# 安装指定版本
gitbook fetch [version]

# 卸载指定版本
gitbook uninstall [version]

# 更新指定版本
gitbook update [tag]
```

# 四、GitBook CLI命令

## 1、gitbook 可用命令

```bash
$ gitbook help

build [book] [output]       构建书籍
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
    --format                Format to build to (Default is website; Values are website, json, ebook)
    --[no-]timing           Print timing debug information (Default is false)
serve [book] [output]       serve the book as a website for testing
    --port                  指定监听端口(默认端口4000)
    --lrport                Port for livereload server to listen on (Default is 35729)
    --[no-]watch            Enable file watcher and live reloading (Default is true)
    --[no-]live             Enable live reloading (Default is true)
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
    --format                Format to build to (Default is website; Values are website, json, ebook)
install [book]              安装所有插件资源
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
parse [book]                parse and print debug information about a book
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
init [book]                 初始化创建书籍文件结构
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
pdf [book] [output]         构建书籍为ebook文件
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
epub [book] [output]        构建书籍为ebook文件
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
mobi [book] [output]        构建书籍为ebook文件
    --log                   指定日志输出级别(值为debug, info默认, warn, error, disabled)
```

## 2、gitbook init初始化创建书籍文件结构

```bash
gitbook init
# 在当前路径下自动生成README.md 和 SUMMARY.md。也可以先手动创建SUMMARY.md，再执行gitbook init，如果SUMMARY.md中配置的文件夹和文件不存在，就会自动创建文件夹和文件，已经存在的文件夹和文件不会被覆盖。
```

## 3、gitbook build构建gitbook书籍静态HTML资源

```bash
gitbook build [book] [output]
# 会在书籍的文件夹中生成一个 _book 的文件夹, 里面有生成的静态HTML资源。可将 _book 文件夹下的文件拷贝到nginx、httpd等web服务器内
```

## 4、gitbook serve启动本地预览书籍服务

```bash
gitbook serve [book] [output]
```

浏览器中打开： http://localhost:4000 预览GitBook书籍

## 5、输出书籍文件

**`Prerequisite`**：

- `ebook-convert`：GitBook在生成PDF的过程中使用到calibre的转换功能，没有安装Calibre或安装了Calibre没有配置环境变量都会导致转换PDF失败。Calibre下载地址：https://calibre-ebook.com/download
- `在 Typora 中安装 Pandoc 进行导出`

```bash
# 输出书籍为PDF格式文件
gitbook pdf [book] [output]

# 输出书籍为epub格式文件
gitbook epub [book] [output]

# 输出书籍为mobi格式文件
gitbook mobi [book] [output]
```

## 6、gitbook install安装插件样式资源

```bash
gitbook install [book]
#会在当前路径下生成node_modules文件夹，里面为插件的样式资源
```

## 7、gitbook parse 解析电子书

```bash
gitbook parse [book]
```

# 五、GitBook的文件结构

| 文件/文件夹        | 描述                      | 是否必须 |
| ------------------ | ------------------------- | -------- |
| README.md          | 书籍的简介                | 必须     |
| SUMMARY.md         | 书籍的目录结构            | 可选     |
| book.json          | GitBook的插件样式配置文件 | 可选     |
| GLOSSARY.md        | 词汇、术语列表            | 可选     |
| _book文件夹        | GitBook输出的静态HTML文件 |          |
| node_modules文件夹 | 插件的样式资源            |          |

# 六、SUMMARY.md编写规则

1. SUMMARY.md 的格式是一个链接列表。链接的标题将作为章节的标题，链接的目标是该章节文件的路径
2. 向父章节添加嵌套列表将创建子章节
3. 每章都有一个专用页面（part#/README.md），并分为子章节。
4. 目录中的章节可以使用锚点指向文件的特定部分。
5. 目录可以分为以标题或水平线 ---- 分隔的部分
6. Parts 只是章节组，没有专用页面，但根据主题，它将在导航中显示。

# 七、book.json编写规则


## 常规设置

| 变量          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `root`        | 包含所有图书文件的根文件夹的路径，除了 `book.json`           |
| `structure`   | 指定自述文件，摘要，词汇表等的路径，参考 [Structure paragraph](https://blog.csdn.net/stu059074244/article/details/77767835#structure). |
| `title`       | 您的书名，默认值是从 README 中提取出来的。在 GitBook.com 上，这个字段是预填的。 |
| `description` | 您的书籍的描述，默认值是从 README 中提取出来的。在 GitBook.com 上，这个字段是预填的。 |
| `author`      | 作者名。在GitBook.com上，这个字段是预填的。                  |
| `isbn`        | 国际标准书号 ISBN                                            |
| `language`    | 本书的语言类型 —— [ISO code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 。默认值是 `en` |
| `direction`   | 文本阅读顺序。可以是 `rtl` （从右向左）或 `ltr` （从左向右），默认值依赖于 `language` 的值。 |
| `gitbook`     | 应该使用的GitBook版本。使用 [SemVer](http://semver.org/) 规范，并接受类似于 `“> = 3.0.0”` 的条件。 |

## plugins

Gitbook 默认带有 5 个插件：

- highlight
- search
- sharing
- font-settings
- livereload

| 变量            | 描述             |
| :-------------- | :--------------- |
| `plugins`       | 要加载的插件列表 |
| `pluginsConfig` | 插件的配置       |



## 示例

```json
{
	"author": "Curiouser <rationalmonster@163.com>",
	"title": "Devops Roadmap",
	"plugins": [
		"-search",
		"-lunr",
		"-sharing",
        "-highlight",
		"search-pro",
		"splitter",
        "github",
		"popup",
        "sectionx",
        "expandable-chapters",
		"sharing-plus",
		"code",
        "auto-scroll-table",
		"theme-fexa",
        "tbfed-pagefooter",
        "back-to-top-button",
        "emphasize",
        "edit-link",
        "prism",
        "donate",
        "theme-comscore",
        "github-buttons",
        "github-issue-feedback"
	],
	"pluginsConfig": {
        "theme-default": {
            "showLevel": true
        },
        "github-issue-feedback": {
          "repo": "RationalMonster/rationalmonster.github.io"
        },
        "github": {
            "url": "https://github.com/RationalMonster"
        },
        "github-buttons": {
      		"buttons": [{
        		"user": "RationalMonster",
        		"repo": "rationalmonster.github.io",
        		"type": "star",
        		"size": "small",
        		"count": "true"
      			}]
    	},
        "sharing": {
        	"weibo": true,
        	"qq": "true",
        	"google": true,
        	"all": [
           		"facebook", "twitter"
           	]
        },
        "code": {
        	"copybuttons": "true"
        },
        "theme-fexa":{
            "search-placeholder":"搜索文章",
            "logo": "assets/logo.png"
        },
        "tbfed-pagefooter": {
            "copyright":"Copyright Curiouser",
            "modify_label": "该文件最后修改时间：",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        },
        "edit-link": {
          "base": "https://github.com/RationalMonster/rationalmonster.github.io/blob/master",
          "label": "ORIGIN In Github"
        },
        "prism": {
            "css": [
                "prismjs/themes/prism-tomorrow.css"
            ],
            "lang": {
                "flow": "typescript"
            },
            "ignore": [
                "mermaid",
                "eval-js"
            ]
        },
         "donate": {
            "wechat": "/assets/wechat-donate.jpg",
            "title": "",
            "button": "赏",
            "wechatText": "微信打赏"
        }
    }
}
```

# 八、GLOSSARY.md 编写规则

- GLOSSARY.md 的格式是 h2 标题的列表，以及描述段落

# 九、托管到 Github Pages
Github 有个功能： GitHub Pages 。它允许用户在 GitHub 仓库托管你的个人、组织或项目的静态页面（自动识别 html、css、javascript）。

## 1、建立 xxx.github.io 仓库

要使用这个功能，首先，你必须建立一个严格遵循以下命名要求的仓库：Github账号名.github.io  举例，我的 Github 账号为 dunwu，则这个仓库应该叫 dunwu.github.io。通常，这个仓库被用来作为个人或组织的博客。

## 2、建立 gh-pages 分支

xxx.github.io 仓库中建立一个名为 gh-pages 的分支。只要 gh-pages 中的内容符合一个静态站点要求，就可以在如下地址中进行访问：https://Github用户名.gitbooks.io/Github 仓库 。



# 参考链接

1. https://www.jianshu.com/p/f38d8ff999cb?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation

# 未完待整理更新
