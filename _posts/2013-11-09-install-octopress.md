---
layout: post
title: Octopress介绍与安装
published: true
---

将自己的博客转向Octopress，利用Github托管并自定义域名。安装过程中也遇到一些问题，通过本篇文章记录下来，希望对要转向Octopress的朋友有帮助。

1.  什么是Octopress
2.  安装与发布Octopress
3.  安装中可能出现的问题
4.  通过Markdown语法写文章
5.  自定义域名
6.  导出wordpress文章到Octopress

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#1-octopress)1. 什么是Octopress

Octopress是使用Ruby语言编写的博客系统。相比wordpress，它拥有一些独特的特点：

-   运行生成Html静态页面，无需数据库支持
-   默认使用markdown语法撰写文章
-   通过Git方便地进行博客版本控制
-   利用Github Pages托管Octopress，无需服务器支持
-   Mash up免费服务，如Disqus , Google analysis , Delicious…
-   Geek Style

如果你想更具体的了解Octopress，可以阅读[官方介绍](http://octopress.org/)或者xdite的[Why Octopress?](http://blog.xdite.net/posts/2011/10/07/what-is-octopress/)

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#2-octopress)2. 安装与发布Octopress

如果你不了解Ruby以及Git，不建议急着上手安装Octopress。安装步骤你可以参考[官网文档](http://octopress.org/docs/setup/)，也可以按照以下步骤来安装。

​a. Octopress需要基于ruby 1.9.2版本环境，通过RVM切换

```
rvm install 1.9.2 && rvm use 1.9.2
```

​b. 通过git clone获取octopress源代码

```
git clone git://github.com/imathis/octopress.git octopress
```

​c. 进入octopress文件夹，将Gemfile的source改用[淘宝镜像](http://ruby.taobao.org/)

```
source "http://ruby.taobao.org"
```

​d. 运行命令进行安装

```
bundle install
rake install
```

​e. 建立github pages repo，假设你的用户名是username，则建立repo名为username.github.com。具体可参见[github pages](http://pages.github.com/)。

​f. 通过以下命令，自动建立github pages所需的配置

```
rake setup_github_pages
```

当提示输入github url时，请输入`git@github.com:username/username.github.com.git`，当然需要替换username。

该命令将自动建立两个branch，分别是master和source。master用来发布octopress生成的静态内容，在\_deploy文件夹里的静态文件。source用来发布除了\_deploy之外的所有文件，既octopress程序本身。

​g. 通过\_config.yml配置octopress博客信息，请注意冒号之后需要空格，具体详见[Octopress Configuring](http://octopress.org/docs/configuring/)。

​h. 生成以及预览功能

```
rake generate # generate your blog static pages content according to your input. 
rake preview # start a web server on "http://localhost:4000", you can preview your blog content.
```

第一条命令将生成静态页面到\_deploy文件夹；第二条命令通过http://localhost:4000来预览生成的页面，这时候还没有写文章，先看看是否config信息是否正确。

​i. 发布生成的静态内容到master branch

```
rake deploy # push your static pages content to your github pages repo ("master" branch)
```

​j. 发布程序本身发布到source branch

```
git add .
git commit -m 'install'
git push origin source
```

通过以上步骤就完成了安装与发布，这时候github会发邮件给你，github pages服务需要一会才能生效。生效后，可通过username.github.com来访问你的octopress blog。

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#3-)3. 安装中可能出现的问题

​a. rake版本错误

```
rake aborted!
You have already activated rake 0.9.2.2, but your Gemfile requires rake 0.9.2. U
sing bundle exec may solve this.
(See full trace by running task with --trace)
```

一种解决方案是将Gemfile里的改成`gem 'rake', '0.9.2.2'`，并重新执行bundle install；另一种方案可以执行`bundle exec rake`替代`rake`命令。

​b. \_config.yml配置中，冒号后面没有空格，语法错误。

```
psych. Rb: 148: in ` parse ': peasants' t parse YAML at line 12 column 0 (Psych: : SyntaxError)
```

​c. 解决中文编码问题

找到ruby环境下的`setup_environment.bat`文件，在文件最后加入2行代码

```
set LC_ALL=en_US.UTF-8
set LANG=en_US.UTF-8
```

​d. 还是中文编码的错误

```
gems/ruby-1.9.2-p290/gems/jekyll-0.11.0/lib/jekyll/convertible.rb:32:in `read_yaml': invalid byte sequence in US-ASCII (ArgumentError)
```

解决方案：将convertible.rb文件的第29行

```
self.content = File.read(File.join(base, name))
```

修改为：

```
self.content = File.read(File.join(base, name), :encoding => "utf-8")
```

​e. jekyll版本不兼容，请升级jekyll到0.11.2。（liquid \> 2.3）

```
uninitialized constant Jekyll::Layout::Convertible<NameError>
```

修改Gemfile如下，并重新进行`bundle install`

``` ruby
source "http://ruby.taobao.org"
group :development do
  gem 'rake', '0.9.2.2' 
  gem 'rack'
  gem 'jekyll', '0.11.2'
  gem 'rdiscount'
  gem 'pygments.rb'
  gem 'RedCloth'
  gem 'haml', '>= 3.1'
  gem 'compass', '>= 0.11'
  gem 'rubypants'
  gem 'rb-fsevent'
  gem 'stringex'
  gem 'liquid', '2.3'    # jekyll 0.11.2required liquid >= 2.3
end
gem 'sinatra', '1.2.6'
```

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#4-markdown)4. 通过Markdown语法写文章

[Markdown](http://daringfireball.net/projects/markdown/)语法是一种轻量级的标记语言，只需要10min就能掌握。它将使你更专注与写作。

#### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#41-markdown)4.1 Markdown编辑器

Mac OS 推荐使用 [mouapp](http://mouapp.com/) Linux 推荐使用 VIM 或 [Sublime](http://www.sublimetext.com/) 加上 [Markdown-Preview](http://lucifr.com/2012/02/20/sublime-text-2-markdown-%E9%A2%84%E8%A7%88%E6%8F%92%E4%BB%B6/#disqus_thread) Windows 推荐使用 Sublime 或[MarkdownPad](http://markdownpad.com/)

#### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#42-octopress)4.2 编写Octopress文章

​a. 新建文章

```
cd octopress
rake new_post["The Title of Your Article"]
```

该命令将在\*source/\_posts\*下生成\*.markdown\*文件，可通过上述的编辑器进行编辑。除了新建文章之后，也可以通过`rake new_page`来新建页面。

​b. 编写文章

```
---
layout: post
title: "The Title of Your Article"
date: 2012-03-08 2:59
comments: true
categories: 
---
#上方是配置信息，从在这里开始编写文章。
```

编写完成后，再次按照2.h步骤开始，进行生成、预览、发布等。具体你可以参考[这里](http://octopress.org/docs/blogging/)。

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#5-)5. 自定义域名

在项目里的\*source\*文件夹新建名为CNAME的文件，在CNAME里写上你的域名。也可通过命令外执行：

```
$ echo 'yourdomain.com' >> source/CNAME
```

修改域名的解析：让`A Record`指向`207.97.227.245`，并让`WWW CNAME`指向`username.github.com`。

解析需要一定时间生效，不要太着急。更详细的信息可参考[Github Pages](http://pages.github.com/)

### [](https://github.com/jokry/jokry.github.com/blob/source/_posts/2012-03-08-octopress.markdown#6-wordpressoctopress)6. 导出wordpress文章到Octopress

通过这个[wordpress blog -\> markdown](https://gist.github.com/1796343)可以将wordpress的导出文件转化为对应的markdown文件。将生成的文件放在`_deploy`文件夹下，再次生成发布。

好吧，终于写完了，希望能够帮到建立Octopress的朋友，如果还有其他的疑问，可以留言。
