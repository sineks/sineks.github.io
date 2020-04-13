---
title: Hexo博客Next主题添加Gitalk评论插件
date: 2020-04-12 20:29:35
tags:
    - hexo
---
为了给这个博客加个评论功能，网上搜索一番，找到N个教程，做了M个尝试，最后发现自己蠢死了，我用的Next主题版本自带gitalk功能，简单配置一下就行了。

## 申请Github的OAuth应用程序

[申请地址](https://github.com/settings/developers)
登陆后点击右上的`New OAuth APP`按钮，看下图填写(盗一个知乎的图）：
![newoauthapp](https://pic2.zhimg.com/80/v2-054ceed11a9a35e0855136ef21061b81_720w.jpg)
需要注意的是，如果绑定了自己的域名，这里`主页网址`和`授权回调网址`请填写自己的域名地址，比如我填写的就是`https://blog.muzy.cc`。

注册后会得到`Client ID`和`Client Secret`两个参数。

## 配置Next主题

我的Next主题版本是`NexT.Muse v7.7.1`，可以直接配置gitalk。打开位于`/themes/next/`下的`_config.yml`文件，找到`gitalk`模块，按照以下配置：
```
# Gitalk
# For more information: https://gitalk.github.io, https://github.com/gitalk/gitalk
gitalk:
  enable: true
  github_id: sineks # GitHub repo owner
  repo: sineks.github.io # Repository name to store issues
  client_id: xxxxxxxxxxx # 申请的Client ID
  client_secret: xxxxxxxxxxxxxxxxxxxxxx # 申请的Client Secret
  admin_user: sineks # GitHub repo owner and collaborators, only these guys can initialize gitHub issues
  distraction_free_mode: true # Facebook-like distraction free mode
  # Gitalk's display language depends on user's browser or system environment
  # If you want everyone visiting your site to see a uniform language, you can set a force language value
  # Available values: en | es-ES | fr | ru | zh-CN | zh-TW
  language:
```

填好配置后保存。然后：
```
hexo clean
hexo g
hexo s //本地测试一下，没问题再deploy
hexo d
```
不出意外在文章尾部应该就能看到gitalk模块了。第一次gitalk模块还不能用，提示什么`no issue`，需要自己登陆github后评论一下，相当于激活了，忘记截图，大概就这么回事吧。

整个过程简单方便，不用什么新建这个文件修改那个配置的。搞定手工！

