---
title: 一次奇葩的SSH登录错误
date: 2022-03-18 16:51:05
tags:
  - SSH
  - tencentcloud
---
<style>
  /* 设置整个页面的字体 */
  html, body, .markdown-body {
    font-family:'Noto Serif', 'Noto Serif CJK SC', 'Noto Serif CJK', 'Source Han Serif SC', ‘Source Han Serif’, source-han-serif-sc, serif;
    font-size: 15px;
  }
</style>

之前把腾讯云的服务器从密码改为密钥登录，今天用winscp盘的时候，发现用密钥死活登录不上去，一直提示：`"Server refused our key"`。在网上查了半天资料，确保自己的各项设置都没问题，`authorized_keys`权限和`.ssh`文件夹权限也都正确，`/etc/ssh/sshd_config`文件也设置无误，但用密钥就是登录不上。

在腾讯云控制台反复绑定、解绑密钥，不行。用自己生成的密钥对，也不行。改成密码登录倒是可以登录，但是这样一点不优雅。

服务器重启了无数次，后来无奈死心，改成密码登录。上去了一顿查看，发现有个很神奇的事情，我的`/root`文件夹的owner居然变成了`lighthouse`。我一脸问号，这是什么时候、怎么发生的事情？难道我的密钥登录失败就是因为这个？

于是迅速更改了`/root`的owner，并重新检查了`.ssh`目录和`authorized_keys`文件的权限。然后用本地生成的密钥一顿操作后，正常如初！

之前查资料的时候，发现其它地方都没问题，我就怀疑是不是有什么机关还没发现，开始还以为腾讯云有什么特别的设置，后来看了官方文档，也没发现异样。果然有时候问题藏在不经意之间，到现在也没明白这个目录的所有人怎么会变掉，按说只有root权限才可以做到，但我肯定没有作过类似操作。

root操作系统真不是个好习惯，要改！
