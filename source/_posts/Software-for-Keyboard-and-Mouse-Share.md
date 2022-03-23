---
title: Software-for-Keyboard-and-Mouse-Share
date: 2022-03-23 19:55:15
tags:
---
有多台电脑同时使用时，能用一套键盘鼠标同时操作，是一件十分愉快的事情。我在网上找了两款免费好用的软件，记录在此。

## Mouse without Borders
这是微软官方出品的软件，免费使用。很显然，这款软件只能在Windows上使用。

官网地址：[Download Microsoft Garage Mouse without Borders from Official Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=35460)

软件的使用十分简单，在两台Windows电脑上下载安装好软件后，先设置其中一台电脑，生成`Security Key`。然后在另一台电脑上填写上这个Key，点击`Apply`一般就可以连接了。

这个软件比较好的一点，是不需要设置`主机`、`客户机`，任意一台都可以做主机，其余的机器只要填写上正确的`Security Key`即可。当然，所有电脑需要在同一局域网下。
![](https://cdn.jsdelivr.net/gh/sineks/sineks.github.io@hexoimgs//img/202203232005183.png)

有个小技巧是，在软件的`IP Mappings`标签下，我们可以自己定义电脑跟IP的映射，这样找起电脑来很快。我没有自己设置IP映射时，连接过程很慢，甚至会失败。
![](https://cdn.jsdelivr.net/gh/sineks/sineks.github.io@hexoimgs//img/202203232005845.png)

Mouse without Borders支持在两台机器间复制粘贴，即A机器上复制的内容可以到B机器上粘贴，张总的心愿又实现了。

## Barrier
一款开源的软件，同时支持`Windows`和`MacOS`，基于老牌软件`Synergy`开发。`Synergy`原本也是开源软件，后来商业化了。

官网地址：[debauchee/barrier: Open-source KVM software](https://github.com/debauchee/barrier)

这款软件的使用就更简单了，设置好`服务端`(就是连接了键鼠，打算共享给其它机器用的电脑)，在其它机器上选择客户端，软件就会自动寻找。找到后分别启动服务端和客户端即可。

我在使用时去掉了SSL功能，因为我看日志里提示我找不到证书，反正在局域网内使用，这个证书意义感觉不大。`Barrier`同样支持跨机器复制粘贴。

## 其它软件
这一类软件里还有两个比较知名的，一个是前面提到的[Synergy](https://symless.com/synergy)，一个是[Share Mouse](https://www.sharemouse.com/)，因为都有收费才能使用的功能，对我来讲没有必要，也就没有进一步研究了。

像这样的神器软件，让人身心愉悦又不用花钱，实在是宅家必备，多多益善！
