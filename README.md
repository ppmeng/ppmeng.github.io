## 说明

1. 基于[minimal-mistakes](https://github.com/mmistakes/minimal-mistakes)创建
2. 由于gem默认的源有些慢，我将Gemfile改成了http://gems.ruby-china.org (有时候会因为SSL出错所以将https改为http)
3. 适应国内用户习惯做了部分删减以及增加多说等
4. 目前可见的是一共有Home，About，Portfolio, Categories, Tags这五个页面，主页分页显示最近发布的新文章， About页面主要是一些说明以及简介，接下来分别是作品以及按照类别和标签显示博客列表。需要注意的是虽然我的页面中分类写的是Categories，默认是可以写多个的，但是在面面包屑导航栏里面默认只显示第一个。

## 接下来会做的改进

1. 增加一个页面专门用来写一些生活感想之类的文章或者分享，与技术页面分开，写为collections的形式
2. 目前双栏结构空白地方太大————改进双栏布局的结构，删掉冗余的代码
3. 增加搜索框
4. 增加日历————高亮提交代码的日期
5. 现有的文章结构导航栏布局和样式不协调所以没有加上去————加导航栏

## 使用步骤

1. fork / clone / download, 注意repo name必须为username.github.io，此时可以使用 https://username.github.io 查看博客页面
2. 接下来就是准备github pages本地环境预览改动，避免每次改动完都要push之后才可以看到页面情况
3. 首先确定系统已经准备好ruby环境——我目前使用的是win10，需要下载ruby和devkit，可参考https://www.ruby-lang.org/en/documentation/installation/
4. gem install bundler
5. bundle install(避免因为本地github-pages引擎版本太旧或存在一些bug可以使用bundle update重置)

## 文件说明

1. .github文件夹里面的文件是有关提交issue时可以看到的一些提示（目前我还没做任何修改）
2. _data
   - navigation.yml 导航栏各个标题以及链接位置
   - ui-text.yml 设置博客里面一些文字的显示（保留了英文，增加了中文）
3. _includes
   - analytics-providers ———— 提供网站分析支持
     - custom.html
     - google.html
     - google-universal.html
   - comments-providers ———— 提供评论支持
     - custom.html
     - discourse.html
     - disqus.html
     - facebook.html
     - google-plus.html
     - scripts.html
     - staticman.html
   - footer ———— 尾部，默认为footer.html， 可在此文件夹下自定义
     - custom.html
   - head ———— head部分, 默认为head.html，可在此文件夹下自定义
     - custom.html
   - analytics.html ———— 选择设置的网站分析代码嵌入
   - archive-single.html
   - author-profile.html ———— 博客作者部分
   - base_path ———— url的生成
   - breadcrumbs.html ———— 博客导航栏下方的面包屑导航
   - browser-upgrade.html ———— IE9 以下显示网站升级提示
   - category-list.html
   - comment.html ———— 评论为staticman模板时，提交过的评论显示部分
   - comments.html ———— 评论部分
   - feature_row ———— feature row helper
   - footer.html ———— custom snippets to add to site footer
   - gallery ———— image gallery helper
   - group-by-array ———— group by array helper for archives
   - head.html ———— custom snippets to add to site head
   - masthead.html ———— 导航部分
   - nav-list ———— navigation list helper， 一系列文章时sidebar-left
   - page-hero.html ———— 设置header的image后显示图片部分
   - page__taxonomy.html ———— 页面分类
   - paginator.html ———— 分页
   - post_pagination.html ———— 前一页后一页
   - read-time.html ———— 估计并显示阅读时间
   - scripts.html ———— 
   - seo.html ———— seo部分
   - sidebar.html
   - social-share.html
   - tag-list.html
   - toc ———— 页面文章部分快捷导航-sidebar-right  http://kramdown.gettalong.org/converter/html.html#toc
4. _layouts
   - archive.html
   - archive-taxonomy.html
   - compress.html
   - default.html
   - single.html
   - splash.html
5. _pages
   - 404.md
   - 导航栏各个页面内容
6. _posts ———— 对应各个文章
7. _sass 所有样式
8. assets 博客设置layout后引用的样式（真实引用样式）
   - css/main.scss ———— main stylesheet, loads SCSS partials from _sass
   - fonts/fontawesome-webfont ———— Font Awesome webfonts
   - js
     - plugins     ————     jQuery plugins
     - vendor      ————     vendor scripts
     - _main.js    ————     plugin settings and other scripts to load after jQuery
     - main.min.js ————     optimized and concatenated script file loaded before `</body>`
9. images ———— image assets for posts/pages/collections/etc.
10. .editorconfig ———— 简单讲就是使代码在不同的编译器中都有同样的缩进等样式，参考http://editorconfig.org/
11. .gitattributes ———— https://git-scm.com/docs/gitattributes 和 https://git-scm.com/book/zh/v1/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git%E5%B1%9E%E6%80%A7
12. .gitignore ———— 不提交的文件
13. CHANGELOG.md ———— 模板更新记录
14. CNAME ———— 域名设置
15. Gemfile 和 Gemfile.lock ———— gem file dependencies
16. LICENSE ———— 版权声明
17. README.md
18. _config.yml ———— 设置文件
19. index.html ———— 首页
20. package.json ———— NPM build scripts