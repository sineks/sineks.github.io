---
title: 局域网安装配置Git服务器并实现往外访问和工作区同步到服务器
date: 2020-03-28 23:56:44
tags:
  - git
---

最近在折腾Git，想尝试自己建一个Git服务器。闲置的笔记本正好可以用来做Server，建立过程中也遇到一些问题，故在此记录。

首先，整个环境为：
- 局域网内笔记本安装Ubuntu Server 18.0.4
- 有外网IP并已做好域名解析
- 已做好端口映射，因为是通过SSH服务，端口映射为内网22，外网出于安全选了一个其它端口，因此外网访问时地址为：`ssh://domain:proxy/directory/to/githubrepo`

建立Git服务器过程主要参考[廖雪峰教程](https://www.liaoxuefeng.com/wiki/896043488029600/899998870925664)

<!-- more -->

## 1 安装Git

Ubuntu Server自带了Git，如果没有，请用命令：

```
sudo apt install git
```

进行安装

## 2 创建一个git用户，用来运行git服务

```
sudo adduser git
```

其中`git`可以换成你喜欢的任何名字

创建时需要设置用户`git`密码，随便写一个，后面我们用key来登录，密码基本用不到。`git`用户主要使用使用git服务，因此出于安全禁止git用户登录shell，编辑`/etc/passwd`:

```
sudo vim /etc/passwd
```

找到`git:x:1001:1001:,,,:/home/git:/bin/bash`，改为`git​:x:1001:1001:,,,:/home/git:/usr/bin/git-shell`

这样，`git`用户就不可以登录shell，但是可以使用ssh访问git服务

## 3 创建数字证书，用于登录

在本地电脑（我的是windows）的git bash下：

```
ssh -t rsa -C "your_email"
```

生成证书，因为本地电脑上有多个git仓库，生成证书时指定名称。

把name.pub里面的公钥导入到服务器上`/home/git/.ssh/authorized_keys`中，`.ssh`文件夹和其下的`authorized_keys`文件需要手动创建。公钥一行一个。

## 4 建立Git仓库并初始化

选定一个目录作为Git仓库，假定为`/home/gitrepos/test`，在`/home/gitrepos`下输入命令：

```
sudo git init --bare test.git
```

`Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。`

这里是引用教程的说法，老实说一开始我很懵逼。我以为Git服务器就是像Github一样，`git push`后会把版本信息和文件都同步到服务器上。后来查了许多资料才搞明白，加上`--bare`参数生成的裸仓库的意思，大概就是只做版本控制，不上传文件。工作区就是指我们的文件和文件夹，要同步工作区，后面细说。

建立Git仓库后，将仓库所有者改为用户`git`：

```
sudo chown -R git:git /home/gitrepos/test.git
```

## 5 克隆服务器上的仓库到本地

在本地电脑上用命令：

```
git clone git@serveraddress/home/gitrepos/test.git
```

这里的`serveraddress`，如果只是在局域网内使用，可以直接用服务器的IP地址。但前文讲过，我们需要进行外网访问，并且我的外网访问端口不是22，那么这个命令可以写成：

```
git clone git@ssh://domian:proxy/home/gitrepos/test.git
```

这样就可以通过外网来clone仓库。

我用了另外一种方式，即在windows的`c:/用户/username/.ssh`下用`config`文件来配置`serveraddress`，因为我把证书全部放到这个文件夹下面了，有多个git仓库时，用`config`文件配置比较方便。`config`文件中配置这个git仓库的代码为：

```
// 自建的git Server，域名访问
Host **Domain**
HostName //**DomainName**
port //自己定义的端口，例如1234
User git //服务器上的git用户名
IdentityFile ~/.ssh/生成的证书名字
```

这样通过第一条命令也可以正常clone下来git仓库。

## 6 同步工作区到服务器上

我一直很纳闷我在本地Git文件夹中的文件在服务器上怎么找到，毕竟对于我一个菜鸟选手，看到文件才比较放心。查阅资料后，总结的方法如下：

- 在服务器上把git仓库clone一份：

  ```
  git clone /home/gitrepos/test.git /home/workspace
  ```

  克隆后，在`/home/workspace`目录下会有一个`test`文件夹，跟本地克隆一样，文件夹里就会有工作区（文件和目录了）。

- 实现每次本地push后，服务上自动把工作区同步到/home/workspace/test中：

  首先要把`home/workspace/test`目录的所有者给用户`git`：

  ```
  sudo chown -R git:git /home/workspace/test
  ```

  然后在git仓库中（/home/gitrepos/test.git），建立一个钩子：

  ```
  cd /home/gitrepos/test.git/hooks
  sudo touch post-receive
  chmod -R 777 post-receive
  sudo vim post-receive
  ```

  在`post-receive`中输入以下内容：

  ```
  #!/bin/sh
  export LANG=zh_CN.UTF-8
  cd /home/workspace/test  //这个是你每次要同步的文件夹
  unset GIT_DIR  //git执行自动脚本的时候有执行一些自定义变量,所以我们在这里unset一下
  git pull
  ```

  **注意**，这里因为生成和编辑`post-receive`时用的是`sudo`命令，`post-receive`所有者是`root`用户，要改成`git`用户才能生效。我在这里琢磨了半天才搞明白，改之前一直不能同步。

- 做完上面操作后，在本地添加文件，`git push`后，在`/home/workspace/test`目录中就能看到自己添加的文件了。

## 7 完结

通过以上步骤后，能够实现外网访问局域网内的git服务器，并同步工作区到服务器上。整体其实很简单，但因为自己对Git的原理了解不多，很多都是照着网上的教程照虎画猫，因为无法解释为什么要这么干。Git是一个很有意思的工具，我认为廖雪峰的教程是入门最佳选择，想深入了解的话也可以看看[这个教程](https://git-scm.com/book)。

当然，想要让Git服务器更强大，还有很多工作要做，比如添加证书管理功能（很多用户的时候很使用）、权限管理等等，这些跟我这个个人用户关系不大，加上才疏学浅，也不想再进一步折腾。到这个程度，已经可以让自己很好的使用了。