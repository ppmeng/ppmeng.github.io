## 说明

1. 基于[minimal-mistakes](https://github.com/mmistakes/minimal-mistakes)创建
2. 由于gem默认的源有些慢，我将Gemfile改成了http://gems.ruby-china.org (有时候会因为SSL出错所以将https改为http)
3. 适应国内用户习惯做了部分删减以及增加多说等

## 使用步骤

1. fork / clone / download, 注意repo name必须为username.github.io，此时可以使用 https://username.github.io 查看博客页面
2. 接下来就是准备github pages本地环境预览改动，避免每次改动完都要push之后才可以看到页面情况
3. 首先确定系统已经准备好ruby环境——我目前使用的是win10，需要下载ruby和devkit，可参考https://www.ruby-lang.org/en/documentation/installation/
4. gem install bundler
5. bundle install(避免因为本地github-pages引擎版本太旧或存在一些bug可以使用bundle update重置)


