# 一台电脑多个Github账号配置

### 前言
果然是折腾无止境。我原本是在github上用hexo建了一个博客，建设过程不复杂，网上很多详细教程。然后问题在于：
- 1、我新建了一个Github账号（新账号）来部署这个博客；
- 2、我是在工作电脑上安装的虚拟机上部署的Lubuntu然后搭建的hexo环境并上传到github；
- 3、我想换家里的PC来写博客，而且家里的PC之前配置过另一个Git账号（旧账号）。
我蛋疼的一比！
所以现在我需要解决的问题有：
1. 怎么把写博客的环境迁移到家里的PC上？
2. 家里的PC已经配置过旧Github账号，如果添加新账号？

### Q1：怎么迁移博客环境？
这个问题网上也有详细教程，后面再说

### Q2：一台电脑多个Github账号的配置

#### 0. 关于账号的问题
网上有的教程是说要删除电脑上之前配置的Global Username，但我测试下来可以对单独的项目指定单独的Username，指定后该项目会优先使用项目指定的Username。关于怎么指定项目的Username，可参考另一篇博文。

#### 1. 首先创建不同的SSH Key
创建ssh key的命令为：
```
ssh-keygen -t rsa -C "your email"
```

在旧账号上创建的时候，输入命令一路回车，就会创建`id_rsa`和`id_rsa.pub`两个文件，其中`.pub`结尾的文件是公钥，打开可以看到文件中的内容，就是要填写在Github上的。Windows下密钥会默认创建在`C:\User\用户名\.ssh`文件夹中。
创建新的ssh key，使用以上命令后，不能一路回车，在出现`Enter file in which to save the key (~/.ssh/id_rsa):`时需要输入文件名和保存位置，如果不输入保存位置则会在当前目录下生成，生成后把文件移至`C:\User\用户名\.ssh`即可。我以生成`sineks`文件名为例。

#### 2. 识别新的密钥
生成密钥后，需要执行`ssh-agent`让`ssh`识别新的密钥：
```
$ ssh-agent bash
$ ssh-add ~/.ssh/sineks
```
*如果直接执行上面第二条命令，我这里提示`Could not open a connection to your authentication agent `，所以需要先输入第一条命令。*

**这里需要注意的是，我在操作过程中，生成新的ssh key后并执行`ssh-agent`后，旧的ssh key似乎被新key替换掉了，所以需要把旧的ssh key也用`ssh-agent`执行一次，这样两个key就都能识别了。**

#### 3. 配置/.ssh/config文件
我的Windows下.ssh文件夹是没有config文件的，新建一个，并在其中输入：

```
# 该文件用于配置私钥对应的服务器

# Default github user(kakabryan@gmail.com)
Host github.com
HostName github.com
User ranba
IdentityFile ~/.ssh/id_rsa

# another github user(greatubuntu@gmail.com)
Host sineks.github.com
HostName github.com
User sineks
IdentityFile ~/.ssh/sineks
```

上面两段语句说明了对不同的账号，设置不同的Host、User、IdentityFile(ssh key)。下面这段是我的新账号的设置，这里的Host我们定义了一个`sineks.github.com`，看网上的教程也可以定义成其它的，应该只是告诉git这里的名字不一样。具体后面再说。

#### 4. 测试是否成功
输入：
```
$ ssh -T git@github.com
$ ssh -T git@sineks.github.com
```
来分别测试两个账号是否连接成功。如果成功，两个账号应分别显示各自的User Name：

![](/images/Snipaste_2020-02-18_15-52-36.png)

![](/images/Snipaste_2020-02-18_15-55-49.png)

#### 5. 最后配置git上传
我在本地有一个仓库（假定已有`.git`文件夹需要上传到github新账号上，首先需要在这个仓库里git bash，然后输入以下命令：
```
$ git remote rm origin
$ git remote add origin git@sineks.github.com:sineks/xxx.git 
```
正如之前我们定义/.ssh/config文件时提到的Host，这两条命令的作用应该是修改本仓库上传时使用的Host信息，如果不修改的话，我这里会发生仍然上传到旧账号上面去的情况，并提示没有在旧账号上找到对应的仓库。

我们也可以修改.git文件夹下的cofnig文件：

![](/images/Snipaste_2020-02-18_16-03-49.png)

作用应该是一样的。我是通过修改config文件实现的。

修改完成后git add、git commit、git push就顺利完成了。




