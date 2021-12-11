---
title: 设置Github图床并在Picgo中配置jsDelivrCDN
date: 2021-12-09 15:06:44
tags:
  - github
  - picgo
  - cdn
---

<style>
  /* 设置整个页面的字体 */
  html, body, .markdown-body {
    font-family:'Noto Serif', 'Noto Serif CJK SC', 'Noto Serif CJK', 'Source Han Serif SC', ‘Source Han Serif’, source-han-serif-sc, serif;
    font-size: 15px;
  }
</style>

没有什么比把一个博客所有东西都放一块更规整的做法了。所以我选择用Github做这个博客的图床，并采用**Niubility**的`jsDelivr`作为CDN，享受飞一般的访问速度。本地当然是用同样**Niubility**的`Picgo`作为图床上传管理工具。

配置也非常简单，我是在博客仓库下新建了一个`hexoimgs`的分支，这样文件就不会到处散落。然后在Github上新建一个Token。

在`Picgo`中设置Github图床：
- 仓库名为：username/reponame
- 分支名为：hexoimgs
- Token：上述建立的Token
- 储存路径：我放在img下，所以这里写`img/`
- 自定义域名：因为要用`jsDelivr`加速，所以这里要自定义域名格式，方便图片上传后直接引用，我的设定是：`https://cdn.jsdelivr.net/gh/sineks/sineks.github.io@hexoimgs/`，主要是仓库名后面`@`了分支名。这里不设置自定义域名，也一样可以在上传后将地址转成`jsDelivr`加速的地址。

Picgo官网：[PicGo](https://molunerfinn.com/PicGo/)

jsDelivr官网：[jsDelivr - A free, fast, and reliable CDN for open source](https://www.jsdelivr.com/)
