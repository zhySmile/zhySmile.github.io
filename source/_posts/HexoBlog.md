---
title: hexo 博客同步
categories: 博客
---
本文适用于：使用 github+hexo 方式搭建博客
<!-- more -->

# 默认生成文件结构：
    scaffolds：脚手架，也就是一个工具模板
    scripts：写文件的js，扩展hexo的功能
    source：存放博客正文内容
    source/_drafts：草稿箱
    source/_posts：文件箱
    themes：存放皮肤的目录
    themes/landscape：默认的皮肤
    _config.yml：全局的配置文件
    db.json：静态常量

1. 关于 _posts 目录：Hexo 是一个静态博客框架，因此没有数据库。文章内容都是以文本文件方式进行存储的，直接存储在 _posts 的目录。Hexo 天生集成了 markdown，我们可以直接使用 markdown 语法格式写博客，新增加一篇文章，就在 _posts 目录，新建一个 xxx.md 的文件。

2. 关于 _config.yml 文件：它是全局的配置文件，很多的网站配置都在这个文件中定义。

    站点信息: 定义标题，作者，语言
    URL: URL 访问路径
    文件目录: 正文的存储目录
    写博客配置：文章标题，文章类型，外部链接等
    目录和标签：默认分类，分类图，标签图
    归档设置：归档的类型
    服务器设置：IP，访问端口，日志输出
    时间和日期格式： 时间显示格式，日期显示格式
    分页设置：每页显示数量
    评论：外挂的 Disqus 评论系统
    插件和皮肤：换皮肤，安装插件
    Markdown 语言：markdown 的标准
    CSS 的 stylus 格式：是否允许压缩
    部署配置：github 发布

# 同步博客

第一步：原仓库备份博客
（注意：如果 themes/XXX(你使用的皮肤名字目录)下面有 .git，请删除这个 .git 文件夹。不然主题无法 push 到远程仓库，导致你发布的博客是一片空白。）

    # git init  //初始化本地仓库
    # git add source themes scaffolds _config.yml package.json //将必要的文件依次添加
    # git commit -m "同步博客" //提交
    # git checkout -b hexo  //新建并切换到 hexo 分支
    # git remote add origin git@github.com:user/user.github.io.git //将本地与Github项目对接
    # git push origin hexo  //push 到 Github 项目的 hexo 分支上

我们就拥有了两个远程的分支：master 和 hexo，其中 master 是部署成博客的分支；hexo 是我们可以 clone 到其他电脑或其他系统的 hexo 源文件的分支。
在 github 中将 hexo 的分支设为默认。
![](/img/hexo/hexo.png)  

第二步：在其他终端克隆和更新博客
此时在另一终端更新博客，只需要将 Github 的 hexo 分支 clone 下来，进行初次的相关配置
（前提：nodejs, git, hexo 已经安装好，并配置好环境变量。）

    # git clone -b hexo git@github.com:user/user.github.io.git  //将 Github 中 hexo 分支 clone 到本地
    # cd user.github.io //切到本地仓库根目录
    # npm install

此时，这个文件夹就是博客的本地副本了。

第三步：写新文章并备份和部署

    # git pull origin hexo //拉取更新，同步博客
    # hexo new post "new post name"  //写新文章
    # git add source
    # git commit -m "xxx"
    # git push origin hexo  //备份
    # hexo d -g  //部署

### 关于遇到的一些问题：

在同步博客 push hexo 分支的时候，推送不上去问题：
报错：error: RPC failed; curl 56 OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10053
原因：接受网络数据失败，被墙了。
解决办法：请使用科学上网，翻墙软件。