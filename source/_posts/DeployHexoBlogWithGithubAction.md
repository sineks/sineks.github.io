---
title: 通过Github Actions自动部署Hexo博客
date: 2021-10-26 16:56:50
tags:
  - hexo
  - github
  - githubactions
---
把Hexo托管到Github，通过Github Actions自动部署博客，算是使用Hexo的一种比较方便的方案，用户只需要在本地用趁手的Markdown书写工具，写好文章并推送至Github仓库即可。

# Github Actions工作流设置
完成该工作仅需要两步：
  - 部署密钥
  - 编写Workflow

## 弄清仓库结构
我的Hexo仓库放置在sineks/sineks.github.io仓库中，该仓库有两个分支：`master`分支存放生成的博客文件，`hexosource`存放hexo博客源文件，同时仓库设置的默认分支是`hexosource`。
网络上有一些教程，博客文件和源文件是分别放在不同的仓库中的，这种情况下Github Actions的Workflow文件要放在源码仓库中。

## 生成并部署密钥
生成密钥命令：
```bash
ssh-keygen -f hexo-deploy-key -C "sineks.github.io"
```
在`git bash`中运行该命令，会在当前文件夹下生成`hexo-deploy-key`和`hexo-deploy-key.pub`一对密钥。`-f`参数是指定生成密钥的名字`hexo-deploy-key`

然后将生成的密钥文件部署到仓库中，首先是部署公钥：
![](https://cdn.jsdelivr.net/gh/sineks/sineks.github.io@hexoimgs/img/202110262136329.png)
如图所示，点击`Add Deploy Key`，将`hexo-deploy-key.pub`中的内容复制进去，并命名为`hexo-deploy-key`

部署私钥：
![](https://cdn.jsdelivr.net/gh/sineks/sineks.github.io@hexoimgs/img/202110262135035.png)
如上图所示，点击`New repository secret`，将`hexo-deploy-key`中的内容粘贴进去，命名为`DEPLOY_SECRET`(注意，后面workflow中的名字要跟这里的名字一样)

## 编写Workflow
在hexo博客的源文件目录中新建`.github/workflows`文件夹，并在文件夹中新建`main.yaml`文件。（也可以在Github仓库中的`Actions`标签下操作。
`main.yaml`文件内容如下：
```yaml
name: hexo deploy

on:
  push:
    branches:
      - hexosource

env:
  GIT_USER: sineks
  GIT_EMAIL: greatubuntu@gmail.com
  DEPLOY_REPO: sineks/sineks.github.io
  DEPLOY_BRANCH: master

jobs:
  build:
    name: Build on node ${{ matrix.node_version }} and ${{ matrix.os }}
    runs-on: ubuntu-latest
    strategy:
      matrix:
        os: [ubuntu-latest]
        node_version: [14.x]

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Checkout deploy repo
        uses: actions/checkout@v2
        with:
          repository: ${{ env.DEPLOY_REPO }}
          ref: ${{ env.DEPLOY_BRANCH }}
          path: .deploy_git

      - name: Use Node.js ${{ matrix.node_version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node_version }}

      - name: Configuration environment
        env:
          DEPLOY_SECRET: ${{secrets.DEPLOY_SECRET}}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh/
          echo "$DEPLOY_SECRET" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name $GIT_USER
          git config --global user.email $GIT_EMAIL

      - name: Install dependencies
        run: |
          npm install

      - name: Deploy hexo
        run: |
          npm run deploy
```
这个配置基本上也是我照着网上抄来的，注意借鉴的是这篇文章：[利用 Github Actions 自动部署 Hexo 博客 | Sanonz](https://sanonz.github.io/2020/deploy-a-hexo-blog-from-github-actions/#Workflow-%E6%A8%A1%E7%89%88)

# 使用
通过以上步骤配置好Github的Actions后，只要分支`hexosource`中的文件发生变化，Actions就会自动运行，很快就能在博客地址上看到结果。
为了方便使用，我在本地使用`Atom`来书写文章和发布到Github仓库，这个软件是Github出品，对Github的集成做的很好。
我的Atom界面：
![](https://raw.githubusercontent.com/sineks/sineks.github.io/hexoimgs/img/202110262128009.png)
各部分说明：
  - A：Project，将hexo源目录添加到这里，方便进行文件树方式的管理；
  - B：编辑区
  - C：预览区，这里安装了插件`Markdown Preview Enhanced`
  - D：Git区，在Git标签下可以看到当前git仓库的变更情况，在这里可以进行`git add`和`git commit`操作，然后在右下角状态栏里`Fecth`字样处，如果有需要Push的操作时，这里会变成`Push`。
  - E：命令行，这里安装了插件`platformio-ide-termianl`，我在这里主要是进行`hexo new post`操作，这样生成的markdown文件会包含hexo博客所需的源信息。
