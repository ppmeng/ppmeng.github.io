---
title: "使用jekyll + github 搭建博客"
categories: "jekyll"
tags: ["jekyll", "github", "domain name"]
---

## github Pages + 域名配置
github注册方法很多博客都有讲到，随便搜都可以找到很详细的步骤，不再赘述，下面是经常会被看到的博客以及我觉得比较重要的部分：

- [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html),这个是基于github pages搭建的静态博客，主要要点在于必须把文件上传到github的一个分支gh-pages上面，这样也可以，而且可以在不同的repo里面都使用这样的分支来生成可分享链接的页面，依附于其所属的项目。但是我使用的是直接创建一个ppmeng.github.io(ppmeng是我的github用户名，对的，repo name格式必须是username.github.io), [使用Github Pages建独立博客](http://beiyuu.com/github-pages)这篇博客User & Organization Pages部分讲解的比较细致可以参考一下
- 配置好git以及远程仓库之后我依照[使用Github Pages建独立博客](http://beiyuu.com/github-pages)这篇博客购买了域名，原因是为了督促自己继续搭建以及坚持写博客，恩，毕竟花钱了就会比较有推动力，嘻嘻，域名是ppmenghome.com，一年55人民币，感觉很划算。不想购买域名的话可以直接看jekyll配置部分
- 关于域名的配置：
1. 购买是在[GoDaddy](https://sso.godaddy.com/)上面购买的，然后在这个购买网站上修改域名服务器，如图所示,-----------添加图片
2. 选择[DNSPod](https://www.dnspod.cn)解析域名，参考[帮助文档](https://www.dnspod.cn/Support)以及[详细步骤](http://blog.csdn.net/u013009839/article/details/43742901),主要就是添加两个A记录
3. 最后就是在博客的根目录（eg：ppmeng.github.io）下增加一个新的文件CNAME，内容为你购买的域名，比如我购买的是ppmenghome.com（注意文件名千万不要写错，恩，我第一次写错了 ，然后折腾半天才发现。。）

- 若有域名绑定有问题可以参考的链接： 
1. [Customizing GitHub Pages / Troubleshooting custom domains](https://help.github.com/articles/troubleshooting-custom-domains/ )
2. [浅谈github页面域名绑定](http://www.cnblogs.com/imsoft/p/5043206.html)

- 打开后发现提示链接不安全，偶尔还会打不开，博客是为了分享内容的，这样不友好怎么可以，然后我就试图给网站申请个ssl，最好是免费的，可是很多人推荐的Kloudse要暂停服务了，而我这个托管于github的博客，别的方法也比较麻烦，对了有个博客，[申请"小绿锁"HTTPS](http://www.jianshu.com/p/9a6bc31d329d)我当时看到了, 写的倒是挺详细的，当时看错了，gitlab看成了github，折腾半天进行不下去了才发现。。现在寻找其他的办法感觉也不太可信，所以最后我采用的方法就是放弃了https，改用http，有的jekyll模板的_config.yml文件里面会有force-https，记得要改成false

## jekyll配置

- 简单了解下[Jekyll](http://jekyll.bootcss.com/docs/installation/)
- 英语好的话直接看下面的链接就可以了
[jekyll](http://jekyllrb.com/)
[Run Jekyll on Windows](http://jekyll-windows.juthilo.com/)
[jekyll issues](https://github.com/jekyll/jekyll/issues)

### Ruby环境搭建

 1. 下载安装[ruby](https://www.ruby-lang.org/en/downloads/)
 2. 下载安装devkit，注意版本要和安装的ruby匹配，解压后假设解压后的文件夹名称为devkit，则在这个文件夹下（cd devkit）运行
   
    ```
    ruby dk.rb init
    //运行结果
    Initialization complete! Please review and modify the auto-generated 'config.yml' file to ensure it contains the root directories to all of the installed Rubies you want enhanced by the DevKit.
    ```

    在ruby dk.rb init之后会发现提示让修改config.yml配置，打开该文件夹里面注释有提示，就是将ruby的安装位置模仿提示的格式写出来，注意前面的横线不能省略,我是直接安装在C盘下了，所以就写成下面的格式

    ```
    - C:\Ruby22-x64 //发现一些博客里面会说这个路径需要写两次，其实是不需要的，格式正确就可以
    ```

    然后继续在devkit下运行运行
    
    ```
    ruby dk.rb install
    [INFO] Skipping existing gem override for 'C:/Ruby22-x64'
    [WARN] Skipping existing DevKit helper library for 'C:/Ruby22-x64'
    ```

    发现有一个WARN，意思是在ruby的安装路径下有devkit helper library，于是我到ruby目录下，如（C:/Ruby22-x64\lib\ruby\site_ruby）将devkit.rb文件删除，然后重新执行ruby dk.rb install命令WARN不见了。（[参考链接](http://blog.csdn.net/chenleicpp/article/details/45147839)） （后来发现Ruby2.3.1没有WARN）

### 下载Jekyll

 1. 安装或者升级gem（在ruby dk.rb install运行之后INFO里面提示了已经安装好了gem，所以我直接运行gem update了一下看是否有更新的版本以及gem -v查看gem 版本，发现没有就继续了）
 2. gem默认的源有点慢，一般都推荐使用[淘宝RubyGems 镜像
](https://ruby.taobao.org/)的源，但是淘宝源不在维护了，所以会不稳定（https://ruby-china.org/topics/29250），故改用[Ruby China的 RubyGems 镜像](https://ruby-china.org/),有个问题是，在我添加Ruby China的gem源时，会出现错误

    ```
    $ gem source -a https://gems.ruby-china.org
    Error fetching https://gems.ruby-china.org:SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (https://gems.ruby-china.org/specs.4.8.gz)
    ```

   是SSL证书的问题嘛？然后我就改成了http试了一下，等了一会儿可以了。在https://github.com/ruby-china/rubygems-mirror/issues/5里面看到有人提出可以下载证书然后添加到ruby中
   下面是参考链接：

   链接1：https://github.com/ruby-china/rubygems-mirror/issues/5

   链接2：https://ruby-china.org/topics/24840

   我的解决办法：参考链接1中hantsy的办法，开始时我安装的ruby版本是2.2.5，结果发现依旧不能添加https的源，后来升级到2.3.1，发现可以了，这个办法比较简单，简单说就是运行gem which rubygems找到rubygems的位置，然后在rubygems文件夹下找到ssl-cert文件夹后在里面在手动添加一个文件夹gems.ruby-china.org，将下载下来的新的http://curl.haxx.se/ca/cacert.pem证书放在这个文件夹下就可以了。参考链接2，应该是证书失效所以手动添加一个新的证书

3. jekyll配置

```
gem install jekyll // 安装jekyll
gem install kramdown // markdown语言解析包
gem install pygments.rb // 代码高亮包or gem install rouge---highlighter: rouge
gem install wdm
```

第一次搭建博客我个人觉得还是使用模板比较方便，[jekyllthemes](http://jekyllthemes.org/) 里面有很多，可以选择一个喜欢的使用，fork然后clone

下面是我遇到的一些问题：

```
问题1：
$ jekyll serve
Configuration file: C:/Users/wjl/mywork/jekyll/_config.yml
Dependency Error: Yikes! It looks like you don't have jekyll-paginate or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jekyll-paginate' If you run into trouble, you can find helpful resources at http://jekyllrb.com/help/!
jekyll 3.2.1 | Error:  jekyll-paginate
解决办法： gem install jekyll-paginate

问题2：
$ jekyll serve
Configuration file: C:/Users/wjl/mywork/jekyll/_config.yml
  Dependency Error: Yikes! It looks like you don't have jemoji or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jemoji' If you run into trouble, you can find helpful resources at http://jekyllrb.com/help/!
jekyll 3.2.1 | Error:  jemoji

解决办法： gem install jemoji

问题3：
 Liquid Warning: Liquid syntax error (line 21): Expected id but found number in "{{ site.404-img }}" in /Users/wjl/mywork/jekyll/_layouts/error.html
done in 0.875 seconds 
Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?

问题4：
 Liquid Warning: Liquid syntax error (line 21): Expected id but found number in "{{ site.404-img }}" in /Users/wjl/mywork/jekyll/_layouts/error.html
这个应该是我这个模板的个例，不影响网站的显示但是看着warning也不爽
解决办法:将_config.yml里面的404-img改为404_img, 然后把{{ site.404-img }}改为“{{site.404_img}}”
```

也许会遇到的问题

- [在Windows下使用jekyll如何避免出现中文字符集错误](http://yanping.me/cn/blog/2012/10/09/chinese-charset-problems-with-jekyll/)
- [Jekyll 本地调试之若干问题](http://chxt6896.github.io/blog/2012/02/13/blog-jekyll-native.html)
- [Jekyll在github上构建免费的Web应用](http://blog.fens.me/jekyll-bootstarp-github/)