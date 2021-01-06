<!-- toc -->
[toc]
<!-- toc -->

# GitBook 使用

## 安装

> 先安装[nodejs](https://nodejs.org/en/)，用npm工具安装gitbook

``` bash
npm install -g gitbook-cli
```
[gitbook init 初始化失败](https://www.cnblogs.com/cyxroot/p/13754475.html)处理，报错信息：TypeError: cb.apply is not a function 

## 运行

``` bash
gitbook build  #生成静态网页，放入_book文件夹，可以上传

gitbook serve [--port 2333] #可指定端口  #生成静态网页，并运行服务器，预览书籍地址http://localhost:4000/ 
```
[gitbook build/serve 失败处理](https://www.cnblogs.com/wenhui92/p/9482629.html) 报错信息：Error: ENOENT: no such file or directory

## 目录文件

> README.md  包含电子书的简介
> SUMMARY.md 包含电子书目录结构

``` markdown
# Summary
* [Introduction](README.md)
* [Part I](part1/README.md)
    * [Writing is nice](part1/writing.md)
    * [GitBook is nice](part1/gitbook.md)
* [Part II](part2/README.md)
    * [We love feedback](part2/feedback_please.md)
    * [Better tools for authors](part2/better_tools.md)
```



## GitBook 命令

``` bash
gitbook init         //初始化目录文件
gitbook help         //列出gitbook所有的命令
gitbook --help      //输出gitbook-cli的帮助信息
gitbook build        //生成静态网页
gitbook serve       //生成静态网页并运行服务器
gitbook build --gitbook=3.2.3     //生成时指定gitbook的版本, 本地没有会先下载
gitbook ls                        //列出本地所有的gitbook版本
gitbook ls-remote                //列出远程可用的gitbook版本
gitbook fetch 3.2.3          //安装对应的gitbook版本 
gitbook update               //更新到gitbook的最新版本
gitbook uninstall 3.2.3       //卸载对应的gitbook版本
gitbook build --log=debug     //指定log的级别
gitbook builid --debug        //输出错误信息
```



## GitBook 配置

> 在初始化目录创建book.json文件，对GitBook进行配置

``` json
{
    "title": "Note",  //设置书本的标题
    "author": "LXJ",  //作者的相关信息
    "description": "笔记整理",  //本书的简单描述
    "language": "zh-hans",  //配置语言,使用简体中文,或en
    "gitbook": "3.2.3",  // 指定版本，或 "gitbook":">=3.1.0"
    "root": ".", //指定存放 GitBook 文件（除了 book.json）的根目录     
    "links": { //在左侧导航栏添加链接信息
        "sidebar": {
            "Home": "https://app.gitbook.com/@lxianjin/spaces"
        }
    },
    "styles": " ", //自定义页面样式
    "plugins": [
        "chapter-fold"
    ], //插件
    "pluginsConfig": { //配置插件的属性
    }
}
```



# 插件

> 插件添加到`book.json`文件`plugins`中，执行`gitbook install`进行安装

>  Gitbook默认带有5个插件：highlight, search, sharing, font-settings, livereload 



## 目录导航

### 根据文件自动生成目录。
> "summary"
> 如果需要自定义，生成一遍之后，建议去掉该插件。

### 将书籍分成几个部分

> "multipart"

### 左侧导航栏目录折叠

> "chapter-fold"
> 折叠左侧导航栏目录，无法知道整本书的结构，有利有弊

### 使左侧目录默认关闭

> "expandable-chapters"

### 使左侧章节目录可以折叠图标变小

> "expandable-chapters-small"

### 侧边导航栏宽度可调节

> "splitter"

### 添加页面内右侧导航

> "atoc" 
> 页首需添加 **<`！--toc--`>**,toc前后各一个空格

```json
{
    "plugins": [
        "atoc"
    ],
    "pluginsConfig": {
        "atoc": {
            "addClass": true,
            "className": "atoc"
        }
    }
}
```



## 页面操作

### 待办事项

> "todo"

### 隐藏元素

> "hide-element"

> 如下，隐藏左侧导航栏的Link：Published with GitBook

``` json
{
    "plugins": [
        "hide-element"
    ],
    "pluginsConfig": {
        "hide-element": {
            "elements": [
                ".gitbook-link"
            ]
        }
    }
}
```

### 高级搜索

> "search-pro"

> 支持中英文,需将内置搜索插件禁用

``` josn
{
    plugins: [
    "-lunr",
    "-search",
    "search-pro"
    ]
}
```

### 回到顶部按钮

> "back-to-top-button"

> 在右下角添加一个按钮，可回到顶部

### 阅读量计数

> "pageview-count"

> 记录每个文章页面被访问的次数

### 复制代码

> "code"

> 可使代码区域的右上角添加一个复制按钮

### 弹出大图

> "popup"

> 点击可在新窗口展示图片



## 页面信息修改

### 标题颜色

> "theme-comscore"

> ComScore 是一个彩色主题，可以为各级标题添加不同的颜色，更容易区分各级标题。

### github地址添加

> "github"

> 在页面右上角有个 github 小图标 ，点击小图标跳转到对应的Github地址。

```json
{
    "plugins": [
        "github"
    ],
    "pluginsConfig": {
        "github": {
            "url": "https://github.com/lxj4713/testpages"
        }
    }
}
```

### 插入logo

> "insert-logo"

### 修改标题栏图标

> "custom-favicon"

### 添加页脚版权

> "tbfed-pagefooter"

> 在每个文章下面标注版权信息和文章时间

``` json
{
    "plugins": [
        "tbfed-pagefooter"
    ],
    "pluginsConfig": {
        "tbfed-pagefooter": {
            "copyright": "",  //写版权信息
            "modify_label": "该文章的修订时间",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        }
    }
}
```



## 分享文档

> "sharing-plus"

``` json
{
    "plugins": [
        "-sharing",
        "sharing-plus"
    ],
    "pluginsConfig": {
        "sharing": {
            "douban": true,
            "facebook": true,
            "google": true,
            "pocket": true,
            "qq": true,
            "qzone": true,
            "twitter": true,
            "weibo": true,
            "all": [
                "douban",
                "facebook",
                "google",
                "instapaper",
                "linkedin",
                "twitter",
                "weibo",
                "messenger",
                "qq",
                "qzone",
                "viber",
                "whatsapp"
            ]
        }
    }
}
```



##  文档及帮助

- [ ] TODO

### 制作知识库或者帮助中心

> "theme-faq"

### GitBook写API文档

> "theme-api"

``` json
{
    "plugins": ["theme-api"],
    "pluginsConfig": {
        "theme-api": {
            "theme": "dark"
        }
    }
}
```





## book.json 参考

``` json
{
    "title": "Note",
    "author": "LXJ",
    "language": "zh-hans",
    "plugins": [
        "summary",
        "atoc",
        "multipart",
        "back-to-top-button",
        "chapter-fold",
        "expandable-chapters",
        "expandable-chapters-small",
        "hide-element",
        "code",
        "splitter",
        "-lunr",
        "-search",
        "search-pro",
        "popup",
        "-sharing",
        "sharing-plus",
        "todo",
        "github",
        "theme-comscore"
    ],
    "pluginsConfig": {
        "hide-element": {
            "elements": [
                ".gitbook-link"
            ]
        },
        "atoc": {
            "addClass": true,
            "className": "atoc"
        },
        "github": {
            "url": "https://github.com/lxj4713/testpages"
        }
    }
}
```



# 发布

## github pages

> 托管到github pages上

``` bash
# 生成静态网站
$ gitbook build

# 复制到项目根目录
$ cp -r _book/* .

# 添加到本地版本库
$ git add .
$ git commit -m "publish"

# 推送到远程仓库
$ git push origin master
```





## 个人网站



## GitBook插件列表

``` bash
- `mygitalk` 基于gitalk的评论插件 ➡️ https://github.com/snowdreams1006/gitbook-plugin-mygitalk
- `theme-default` GitBook的默认主题 ➡️ https://github.com/GitbookIO/theme-default
- `autotheme` 自动换肤插件 ➡️ https://github.com/willin/gitbook-plugin-autotheme
- `sharing` 默认的分享插件 ➡️ https://github.com/GitbookIO/plugin-sharing
- `fontsettings` 默认的字体、字号、颜色设置插件 ➡️ https://github.com/GitbookIO/plugin-fontsettings
- `highlight` 默认的代码高亮插件，通常会使用 prism 来替换 ➡️ https://github.com/GitbookIO/plugin-highlight
- `search` 默认搜索插件 ➡️ https://github.com/GitbookIO/plugin-search
- `search-plus` 支持中文搜索插件 ➡️ https://github.com/lwdgit/gitbook-plugin-search-plus
- `prism` 基于 [Prism](http://prismjs.com/) 的代码高亮 ➡️ https://github.com/gaearon/gitbook-plugin-prism
- `favicon` 更改网站的 favicon.ico ➡️ https://github.com/menduo/gitbook-plugin-favicon
- `github` 在右上角显示 github 仓库的图标链接 ➡️ https://github.com/GitbookIO/plugin-github
- `github-buttons` 显示 github 仓库的star和fork按钮 ➡️ https://github.com/azu/gitbook-plugin-github-buttons
- `splitter` 在左侧目录和右侧内容之间添加一个可以拖拽的栏，用来调整两边的宽度 ➡️ https://github.com/yoshidax/gitbook-plugin-splitter
- `copy-code-button` 为代码块添加复制的按钮 ➡️ https://github.com/WebEngage/gitbook-plugin-copy-code-button
- `tbfed-pagefooter` 自定义页脚，显示版权和最后修订时间 ➡️ https://github.com/zhj3618/gitbook-plugin-tbfed-pagefooter
- `expandable-chapters` 收起或展开章节目录中的父节点 ➡️ https://github.com/DomainDrivenArchitecture/gitbook-plugin-expandable-chapters
- `expandable-chapters-small` 比较好的折叠侧边栏 ➡️ https://github.com/lookdczar/gitbook-plugin-expandable-chapters-small-auto
- `book-summary-scroll-position-saver` 自动保存左侧目录区域导航条的位置 ➡️ https://github.com/yoshidax/gitbook-plugin-book-summary-scroll-position-saver
- `ga` 添加 Google 统计代码 ➡️ https://github.com/GitbookIO/plugin-ga
- `sitemap` 生成站点地图 ➡️ https://github.com/GitbookIO/plugin-sitemap
- `baidu` 使用百度统计 ➡️ https://github.com/poppinlp/gitbook-plugin-baidu
- `Donate` Gitbook 捐赠打赏插件 ➡️ https://github.com/willin/gitbook-plugin-donate
- `anchors` 标题带有 github 样式的锚点 ➡️ https://github.com/rlmv/gitbook-plugin-anchors
- `anchor-navigation-ex` 插件锚导航-EX ➡️ https://github.com/zq99299/gitbook-plugin-anchor-navigation-ex
- `theme-api` 编写 API 文档 ➡️ https://github.com/GitbookIO/theme-api
- `katex` 使用KaTex进行数学排版 ➡️ https://github.com/GitbookIO/plugin-katex
- `editlink` 内容顶部显示编辑本页链接 ➡️ https://github.com/zhaoda/gitbook-plugin-editlink
- `ad` 在每个页面顶部和底部添加广告或任何自定义内容 ➡️ https://github.com/zhaoda/gitbook-plugin-ad
- `image-captions` 抓取内容中图片的`alt`或`title`属性，在图片下面显示标题 ➡️ https://github.com/todvora/gitbook-plugin-image-captions
- `chart` 使用 C3.js 图表 ➡️ https://github.com/csbun/gitbook-plugin-chart
- `styles-sass` 使用 SASS 替换 CSS ➡️ https://github.com/GitbookIO/plugin-styles-sass
- `styles-less` 使用 LESS 替换 CSS ➡️ https://github.com/GitbookIO/plugin-styles-less
- `disqus` 添加 disqus 评论插件 ➡️ https://github.com/GitbookIO/plugin-disqus
- `latex-codecogs` 使用数学方程式 ➡️ https://github.com/GitbookIO/plugin-latex-codecogs
- `mermaid` 使用流程图 ➡️ https://github.com/JozoVilcek/gitbook-plugin-mermaid
- `atoc` 插入 TOC 目录 ➡️ https://github.com/willin/gitbook-plugin-atoc
- `ace` 插入代码高亮编辑器 ➡️ https://github.com/ymcatar/gitbook-plugin-ace
- `sectionx` 分离各个段落，并提供一个展开收起的按钮 ➡️ https://github.com/ymcatar/gitbook-plugin-sectionx
- `mcqx` 交互式多选插件 ➡️ https://github.com/ymcatar/gitbook-plugin-mcqx
- `include-codeblock` 通过引用文件插入代码 ➡️ https://github.com/azu/gitbook-plugin-include-codeblock
- `fbqx` 使用填空题 ➡️ https://github.com/Erwin-Chan/gitbook-plugin-fbqx
- `spoiler` 隐藏答案，当鼠标划过时才显示 ➡️ https://github.com/ymcatar/gitbook-plugin-spoiler
- `anchor-navigation` 锚点导航 ➡️ https://github.com/yaneryou/gitbook-plugin-anchor-navigation
- `youtubex` 插入 YouTube 视频 ➡️ https://github.com/ymcatar/gitbook-plugin-youtubex
- `redirect` 重定向页面跳转 ➡️ https://github.com/ketan/gitbook-plugin-redirect
- `duoshuo` 使用多说评论 ➡️ https://github.com/codepiano/gitbook-plugin-duoshuo
- `jsfiddle` 插入 JSFiddle 组件 ➡️ https://github.com/Mavrin/gitbook-plugin-jsfiddle
- `jsbin` 插入 JSBin 组件 ➡️ https://github.com/jcouyang/gitbook-plugin-jsbin
- `Advanced Emoji` 支持emoji表情 ➡️ https://github.com/codeclou/gitbook-plugin-advanced-emoji
- `Puml` 使用 PlantUML 展示 uml 图 ➡️ https://github.com/GitbookIO/plugin-puml
- `Graph` 使用 function-plot 绘制数学函数图 ➡️ https://github.com/cjam/gitbook-plugin-graph
- `Todo` 添加 Todo 功能 ➡️ https://github.com/ly-tools/gitbook-plugin-todo
- `include-csv` 展示 csv 文件内容 ➡️ https://github.com/TakuroFukamizu/gitbook-plugin-include-csv
- `musicxml` 支持 musicxml 格式的乐谱渲染 ➡️ https://github.com/ymcatar/gitbook-plugin-musicxml
- `versions-select` 添加版本选择的下拉菜单，针对文档有多个版本的情况 ➡️ https://github.com/prescottprue/gitbook-plugin-versions-select
- `rss` 添加 rss 订阅功能 ➡️ https://github.com/denysdovhan/gitbook-plugin-rss
- `multipart` 将书籍分成几个部分 ➡️ https://github.com/citizenmatt/gitbook-plugin-multipart
- `url-embed` 嵌入动态内容 ➡️ https://github.com/basilvetas/gitbook-plugin-url-embed
```







参考：[gitbook 码谱教程:https://www.mapull.com/gitbook/default/](https://www.mapull.com/gitbook/default/)

[12个插件 https://segmentfault.com/a/1190000019473512](https://segmentfault.com/a/1190000019473512)

https://www.bookstack.cn/read/yangzh-gitbook/1799c1a766dfe1fe.md

https://www.bookstack.cn/read/gitbook-use-3.2.3/plugins.md

