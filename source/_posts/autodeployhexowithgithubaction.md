---
title: Gihub Actions自动部署Hexo后推送至VPS
date: 2021-12-09 11:15:57
tags:
  - hexo
  - github
  - githubactions
  - VPS
---
## 前言
之前研究过通过[Github Actions自动部署Hexo博客](https://blog.muzy.cc/2021/DeployHexoBlogWithGithubAction/)，现在更进一步，将Github Actions生成的博客页面推送到自己的VPS上。

当我们在本地使用`hexo g`命令的时候，就是在本地完成了博客的编译工作，即将markdown文件转成html文件并构建好博客的布局、主题等等要素，再使用`hexo d`命令，就是将本地的静态页面push到Github上。现在使用Github Actions，其实就是将`hexo g`的过程放在Github服务器上完成了，这样一来，本地就只需要编写好markdown文件并推送到对应的Github仓库即可。

同时，Github上编译好整个静态网站后，网站文件也可以推送到其它任何服务器上，比如自己的VPS。方法也很简单，使用`rsyc`工具通过`SSH`的方式将网站文件推送到VPS即可。

## VPS上的准备工作
- 安装rsync。有的服务器上没有默认安装，需要自行安装，我的Debian上使用`apt install rsync`即可。使用`rsync --version`命令可以检查有无安装及安装的版本；
- 如果VPS上只有`root`用户，出于安全考虑，可以新建一个用户专门用于接收推送；
- 生成一对SSH Key，用于SSH认证。用哪个用户进行SSH，SSH Key就要放在那个用户的home目录里，同时将公钥的内容输入到`~./.ssh/authorized_keys`中；
- 在VPS上指定一个目录用于接收推送过来的网站文件，安装Nginx，并配置好网站；

## Github上的操作
在仓库的`setting`-`secrets`中添加一个key，名称可以是`BLOG_DEPLOY_KEY`，内容为前述创建的私钥。再建立一个`SSH_HOST`的key，内容为VPS的IP或域名。

然后在Github Actions的`mian.yml`文件中最后添加以下内容：
```yaml
- uses: webfactory/ssh-agent@v0.4.1
  with:
    ssh-private-key: |
      ${{ secrets.BLOG_DEPLOY_KEY }}

- name: Scan Public keys
  run: |
    ssh-keyscan ${{ secrets.SSH_HOST }} >> ~/.ssh/known_hosts

- name: Deploy To VPS
  run: |
    rsync -av --delete public/ username@${{secrets.SSH_HOST}}:path/to/html
```
当仓库有更新时，Github Actions会执行，最终能在VPS上看到自己的静态博客站点文件。
如果发生错误，根据错误提示进行纠正即可。
