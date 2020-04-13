---
title: NextCloud升级后一直处于维护的解决办法
date: 2020-03-18 20:43:02
tags: nextcloud
---

近期登陆nextcloud，提示可以升级到18.0.2（当前时18.0.0），于是点击升级。经历了漫长的等待后（没有梯子的情况下下载nextcloud包非常慢，我记得好像也就100多MB的东西，能下几个小时）直到出现Done，再打nextcloud网站却一直提示处于维护状态，网上搜索，解决办法如下：

在nextcloud目录下（我的是/var/www/nextcloud)，输入：`sudo -u www-data php occ maintenance:mode --off`

或者可以直接修改nextcloud的`config/config.php`文件，找到`maintenance => true`，改为`maintenance => false`，保存重新访问即可。

