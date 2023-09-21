# bloggen

# 依赖
## hugo版本
```
root@wwhvw:~/go/src/bloggen# hugo version
hugo v0.92.2+extended linux/amd64 BuildDate=2023-01-31T11:11:57Z VendorInfo=ubuntu:0.92.2-1ubuntu0.1
```

# 新建项目后操作
## 安装网站文件夹
1. 进入项目`cd ./bloggen`
2. `hugo new site . --force` 创建网站，将生成以下文件
* archetypes：存放用hugo命令新建的md文件应用的front matter模版
* content：存放内容页面，如Blog
* layouts：存放定义网站的样式，写在layouts文件下的样式会覆盖安装的主题中的 layouts文件同名的样式
* static：存放所有静态文件，如图片
* data：存放创建站点时Hugo使用的其他数据
* public：存放Hugo生成的静态网页
* themes：存放主题文件
* config.toml：网站配置文件

## 安装KeepIt主题
1. 进入themes/目录
2. `git submodule add https://github.com/Fastbyte01/KeepIt.git` 下载代码. 使用git
submodule保持KeepIt代码仓库的独立性。
3. 拷贝`KeepIt/exampleSite/*`到`bloggen/`目录下
4. 修改config.toml
```
baseURL = "https://wangweihong.github.io"
languageCode = "zh-cn"
defaultContentLanguage = "en"
title = "摸鱼佬"
theme = "KeepIt"
...
```
5. 本地运行测试`hugo server --bind 0.0.0.0`

## 添加文档
1. `hugo new helloworld.md`
2. 将生成content/helloworld.md中草稿标识`draft: true`移除或者设置为false
3. 本地运行测试`hugo server --bind 0.0.0.0`

## 生成静态HTML文件
1. 在`.gitignore`文件中设置添加`public/`来忽略掉该文件的变更。
2. 在代码根目录执行`hugo`,在`public/`生成静态HTML文件
3. `git init`创建博客仓库,`git add`并且`git commit -m "my first blog"`
4. 在github新建`wangweihong.github.io`项目。 **注意，必须要和github账号同名。**
5. `git remote set-url origin git@github.com:wangweihong/wangweihong.github.io.git`指向仓库
6. `git push -u origin master`
7. 网页访问wangweihong.github.io

# 问题
## 本地运行`hugo server`失败
```
root@wwhvw:~/go/src/bloggen# hugo server --bind 0.0.0.0
Start building sites …
hugo v0.92.2+extended linux/amd64 BuildDate=2023-01-31T11:11:57Z VendorInfo=ubuntu:0.92.2-1ubuntu0.1
Error: Error building site: failed to render pages: render of "home" failed: execute of template failed: template: index.html:3:3: executing "content" at <partial "home_post.html" .>: error calling partial: "/root/go/src/bloggen/themes/KeepIt/layouts/partials/home_post.html:8:27": execute of template failed: template: partials/home_post.html:8:27: executing "partials/home_post.html" at <.Site.Params.DefaultPage>: can't evaluate field Site in type string
```
这个是KeepIt主题的问题
见https://github.com/Fastbyte01/KeepIt/issues/73. 修复方法还未合并到主线

修复方法
```
--- a/layouts/partials/home_post.html
+++ b/layouts/partials/home_post.html
@@ -1,11 +1,11 @@
 {{ $cdn_url := .Scratch.Get "cdn_url" }}
-
+{{ $default_page := .Site.Params.DefaultPage }}
 <div class="post-warp">
     <div class="intro">
         {{ with .Site.Params.avatar}}
         {{ $avatar := .}}
         <div class="avatar">
-          <a href="{{ .Site.Params.DefaultPage }}"> <img src="{{ (printf "%s%s" $cdn_url $avatar)}}"> </a>
+          <a href="{{ $default_page }}"> <img src="{{ (printf "%s%s" $cdn_url $avatar)}}"> </a>
         </div>
         {{ end }}
         {{ if or .Params.gravatar.Email (and .Site.Params.gravatar.Email (ne .Params.gravatar.Email false)) }}


--- a/layouts/partials/home_post.html
+++ b/layouts/partials/home_post.html
@@ -1,11 +1,11 @@
 {{ $cdn_url := .Scratch.Get "cdn_url" }}
-
+{{ $default_page := .Site.Params.DefaultPage }}
 <div class="post-warp">
     <div class="intro">
         {{ with .Site.Params.avatar}}
         {{ $avatar := .}}
         <div class="avatar">
-          <a href="{{ .Site.Params.DefaultPage }}"> <img src="{{ (printf "%s%s" $cdn_url $avatar)}}"> </a>
+          <a href="{{ $default_page }}"> <img src="{{ (printf "%s%s" $cdn_url $avatar)}}"> </a>
         </div>
         {{ end }}
         {{ if or .Params.gravatar.Email (and .Site.Params.gravatar.Email (ne .Params.gravatar.Email false)) }}

```

# 参考
* [如何用 GitHub Pages + Hugo 搭建个人博客](https://cuttontail.blog/blog/create-a-wesite-using-github-pages-and-hugo/)