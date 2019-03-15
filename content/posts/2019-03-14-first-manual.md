---
date: 2019-03-14
title: "如何在github.io搭建Hugo博客站"
tags:
    - 教程
    - github
categories:
    - github
comment: true
---

# 摘要

在[前面的博文](Jekyll blog)中介绍了如何在`github.io`上搭建自己的博客站，基于的是[Jekyll](https://jekyllrb.com/)，这也是github中比较常用的方式，好处是github可以自动调用`Jekyll`来帮你及时生成、更新你的博客站。本来一切还是挺美好的，随着使用的深入，博主发现有几点实在是不能忍：


* github上面的`Jekyll`版本实在太低了，对于code block渲染问题严重。最受不了的一点就是很多种类型的code在里面被显示成一行
* `Jekyll`对文章的文件名有着严格的定义，且不能够放到不同的目录，这个在博文比较多的时候极为不便

博主平时喜欢关注各种新技术，较早的时候就曾关注过一个的博客建站的工具`Hugo`，用Go语言（博主的最爱之一）写的，当时就觉得挺不错。经过这几年的发展已经非常成熟，Github上面的Stars截止本文已经24250了。在经过一段时间的`Jekyll`的折磨之后，终于乘着最近稍微闲了一点，将`Jekyll`彻底换成了`Hugo`。本文就简单介绍一下，如何在github.io上部署你的`Hugo`博客站。

# 准备工作

跟基于`Jekyll`一样，如果需要在github.io上部署的话，必须在github上创建你自己的`<username>.github.io`的repo，详细过程请参见[前面的博文](Jekyll blog)。

# 搭建`Hugo`博客站

## 安装`Hugo`

`Hugo`的安装非常简单，在MAC上执行：

    
    brew install hugo
    

其它安装方式，可以参见[官网教程](https://gohugo.io/getting-started/installing/)。

## 创建你的博客站

1. 在github上面创建博客文章的repo

    `Hugo`的建站部署方式跟`Jekyll`不同，github会自动的识别`Jekyll`并调用`Jekyll`工具帮你自动生成、更新博客站，所以我们只需要直接在自己的`<username>.github.io`中编写文章并上传即可。但是github并不能直接识别`Hugo`的内容，因此，这个博客站的生成、更新的工作得由你自己完成，`<username>.github.io`只是用来托管你生成后的静态网站的内容（github支持托管静态网站，请参见[这里](https://help.github.com/articles/user-organization-and-project-pages/#user--organization-pages))，因此，你还需要另一个git repo来真正的编辑、保存、管理你的博文。为此，博主创建了一个名为[blogs](https://github.com/keysaim/blogs)的repo，以下说到博文管理repo指的就是这个repo。

2. 用`Hugo`建站

    * 初始化博客站

        ```sh
        hugo new site blogs
        ```

    * git init

        ```sh
        cd blogs
        git init
        ```

    * 指定github源

        ```sh
        git remote add origin git@github.com:keysaim/blogs.git
        ```

    这样，你本地的初始化的`Hugo`博客站已经准备好了。

3. 添加一个主题

    `Hugo`官方有非常多的免费开源的主题，[猛击这里](https://themes.gohugo.io/)。博主选用的是国人开发的一款主题[Jane](https://themes.gohugo.io/hugo-theme-jane/)。

    ```
    git submodule add -b master https://github.com/xianmin/hugo-theme-jane.git themes/jane
    cp -r themes/jane/exampleSite/content ./
    cp themes/jane/exampleSite/config.toml ./
    ```

4. 本地测试

    在部署之前，先本地测试一下：

    ```sh
    hugo server
    ```

    然后本地浏览器打开[http://localhost:1313](http://localhost:1313)，没有意外的话你将看到类似这样的：

    <div style="text-align:center">
        <img src="https://d33wubrfki0l68.cloudfront.net/9a581e943c0732d9c9852138ff0ad94f10b710a8/b6bf8/hugo-theme-jane/hugo-theme-jane/screenshot-hugo-theme-jane_huda38615ea8fd917fd31d36ba7a275d34_226834_1500x1000_fill_catmullrom_top_2.png" alt="" width="100%"/>
    </div>

    这是`Jane`主题默认提供的样式跟内容，说明你的本地`Hugo`站可以正常工作了。

## 修改默认的配置

`Hugo`的默认配置文件是`config.toml`，这里还是`Jane`提供的默认配置，需要修改为你自己的信息，比如名字等，很多配置显而易见，这里不详述，只是着重指出几个有点困难或者比较重要的配置：

1. 修改`baseURL`

    `baseURL`是你博客最终部署的网站的url，基于github.io的话就应该是这样的[https://keysaim.github.io/](https://keysaim.github.io/)。

2. 启用gitment

    关于gitment的详细教程，请参见[这篇博文](https://keysaim.github.io/post/2017-08-16-how-to-add-comments/)，按照教程准备好github的几个token之后，只需要修改`Hugo`配置中的这几行：

    ```toml
    [params.gitment]          # Gitment is a comment system based on GitHub issues. see https://github.com/imsun/gitment
        owner = "keysaim"              # Your GitHub ID
        repo = "comments"               # The repo to store comments
        clientId = "xxxx"           # Your client ID
        clientSecret = "xxxx"       # Your client secret
    ```

    其中，repo就是教程中所说的github上面为保存comments而创建的repo，clientId跟clientSecret就是你自己创建的oauth授权信息。

3. 修改页面博文数量显示

    有几个参数会影响页面博文数的显示：

    ```toml
    paginate = 15               # 首页每页显示的文章数
    archive-paginate = 50       # 归档、标签、分类每页显示的文章数目
    ```

4. 修改语言

    如果你需要将博客站显示成中文，那么请这么修改：

    ```toml
    defaultContentLanguage = "zh-cn"
    [Languages.zh-cn]
      languageCode = "zh-cn"
    ```

## 编辑博文

默认情况下，`Jane`主题将博文放在`content/post/`下面，你需要在这下面编辑你的博文。`Hugo`是支持分目录的，这点非常好，比如博主可以这么分：

```console
$ ll
total 4
drwxr-xr-x 3 nbaoping staff  102 Mar 21 16:22 arch
drwxr-xr-x 6 nbaoping staff  204 Mar 22 10:50 blog
drwxr-xr-x 5 nbaoping staff  170 Mar 22 10:33 docker
drwxr-xr-x 4 nbaoping staff  136 Mar 21 16:21 git
drwxr-xr-x 9 nbaoping staff  306 Mar 21 16:20 linux
drwxr-xr-x 3 nbaoping staff  102 Mar 21 16:22 note
drwxr-xr-x 3 nbaoping staff  102 Mar 21 16:24 others
```

还有一点非常不错的就是`Hugo`可以识别出`Jekyll`的博文，包括里面的标题，时间跟标签等信息。这样博主非常方便的将之前的`Jekyll`的所有博文直接迁移过来了，而无需做什么修改。当然，如果你新写博文的话，在博文的头部可以这样编写：

```yaml
---
date: 2018-03-22
title: "如何在github.io搭建Hugo博客"
tags:
    - 教程
    - github
categories:
    - github
comment: true
---
```

编写完成之后，本地查看一下你的博文，没有问题的话先提交到你的博文管理的repo：

```sh
git add xxxx
git commit -m "YOUR COMMIT MESSAGE"
git push origin master
```

## 部署博客站

这一步很关键了，咱们需要将自己的`Hugo`博文站部署到github.io。

1. 添加你的github.io

    ```sh
    git submodule add -b master git@github.com:keysaim/keysaim.github.io.git public
    ```

    这里将其作为submodule添加进来，且放到`public`目录下面，`public`目录是`Hugo`生成静态文件的地方，这样的话咱们就可以把生成出来的静态文件直接上传到你的`<username>.github.io`的repo里面了

2. 生成静态博客站

    ```sh
    hugo
    ```

    直接运行`hugo`命令，就会在`public`目录下生成静态博客站。

3. 提交

    ```sh
    cd public
    git add .
    git commit -m "YOUR COMMIT MESSAGE"
    git push origin master
    ```

4. 查看你的github.io博客站

    正常情况下，过一会你就可以看到你基于`Hugo`的github.io博客站了，博主的是[https://keysaim.github.io/](https://keysaim.github.io/)。

## 自动部署脚本

为了方便，`Hugo`提供了一个[自动部署的脚本](https://gohugo.io/hosting-and-deployment/hosting-on-github/#put-it-into-a-script)，这里博主稍微修改了一点点用来同时提交这两个repo：

```sh
#!/bin/bash

if [ $# -lt  1 ]; then
    echo "$0 <commit message>"
    exit 1
fi

msg="$1"
git commit -m "$msg"
if [ $? -ne 0 ]; then
    echo "Commit failed"
    exit 1
fi
git push origin master
if [ $? -ne 0 ]; then
    echo "Push failed"
fi

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public
# Add changes to git.
git add .

# Commit changes.
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back up to the Project Root
cd ..
```

至此，恭喜你已经完成了你的`Hugo`博客站了。

[Jekyll blog]: https://keysaim.github.io/post/2017-08-15-how-to-setup-your-github-io-blog/
