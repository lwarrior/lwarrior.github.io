---
layout: post
title:  "Install jekyll and build your blog"
date:   2016-03-16 13:35:00 +0800
categories: jekyll update
---
利用jekyll部署自己的blog。以下是一些简单的记录；
环境CentOS 6.5 x86_64.

### 1.安装Ruby ###
首先下载源代码：
源地址http://cache.ruby-lang.org/pub/ruby/上有最新的，也可以从国内的网站上下载：
推荐一下淘宝网的 https://ruby.taobao.org/
我选择了该页面上贴出的最新的版本；

    wget https://ruby.taobao.org/mirrors/ruby/ruby-2.2.2.tar.gz
    tar zvf ruby-2.2.2.tar.gz
    cd ruby-2.2.2
    ./configure
    make
    make install

### 2.修改默认的gem源 ###

查看gem源命令：

    gem sources -l

会有如下输出：
*** CURRENT SOURCES ***
https://rubygems.org/
这个源对于绝大部分用户来说可能无法访问；可将源修改为taobao提供的RubyGem镜像地址：

    gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/

查看是否修改成功

    gem sources -l

输出如下：

    *** CURRENT SOURCES ***
    https://ruby.taobao.org/

表明修改成功！

### 3.安装jekyll ###

    gem install jekyll

如没有报错，表明安装成功；

### 4.生成一个默认的web站点 ###

    jekyll new blog

执行命令后，会在/home/username/目录下生成一个blog目录，这个就是静态网站的目录；

### 5.启动web服务 ###

    jekyll serve

输出：
{% highlight ruby %}
Configuration file: /home/username/blog/_config.yml
            Source: /home/username/blog
       Destination: /home/username/blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.406 seconds.
 Auto-regeneration: disabled when running server detached.
Configuration file: /home/username/blog/_config.yml
    Server address: http://127.0.0.1:4000/
{% endhighlight %}

看到以上输出表明服务正常启动，本机浏览器访问http://127.0.0.1:4000/即可；

如果linux上没有安装图形用户界面，需要修改访问的url供网络访问；

修改配置文件_config.yml
注释掉：baseurl 和 url两行；
增加如下几行：
{% highlight ruby %}
# Serving
detach:  false
port:    4000
host:    192.168.1.100
baseurl: "" # does not include hostname
{% endhighlight %}

修改完毕后就可以通过网络访问该该静态网站了，http://192.168.1.100:4000

_config.yml中的更多参数配置可以参考[这里](https://jekyllrb.com/docs/configuration/)

另外GitHup Pages使用的markdown解析器是[kramdown](http://kramdown.gettalong.org/syntax.html)。

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/