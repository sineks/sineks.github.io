---
title: SublimeText3配置Markdown
date: 2020-03-24 23:56:44
tags: markdown
---

参考一篇有价值的[安装说明](https://blog.csdn.net/qq_20011607/article/details/81370236)，记录如下：

### 0.安装Package Control
略

<!-- more -->

### 1.安装Markdown插件
#### 1.1 安装MarkdownEditing
`Ctrl+Shift+P`，输入`Install Package`，输入`MarkdownEditing`，安装。
安装后，我这里报错：
```
Error loading syntax file "Packages/Markdown/Markdown.sublime-syntax": Unable to read Packages/Markdown/Markdown.sublime-syntax
```
网上搜的办法：
打开`Preferences.sublime-settings`：
```
{
    "ignored_packages":[
        "Markdown"
    ]
}
```
把"Markdown"删除即可。

#### 1.2 安装MarkdownPreview
安装方式如上。安装后，配置快捷键，在`配置-快捷键`打开的文件的右侧栏的中括号中添加一行代码：
```
{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"}  }
```
其中，alt+m可以自己定义，parser后面的markdown可以写github，则调用github的渲染方式，需考虑连接github的速度，并且据说一天只能调用60次。我没配置，也没验证。

#### 1.3 安装配置LiveReload
首先，如前Markdown Preview安装成功后，设置前文所述的快捷键（如需），打开其配置文件 ```Preferences -> Package Settings -> Markdown Preview -> Settings```，检查左侧```enable_autoreload```条目是否为true，若是，跳过。若不是，右侧栏加一条下面这个后重启Sublime:
```
{
    "enable_autoreload": true
}
```
然后安装LiveReload。
安装成功后，再次在Package Control中安装`LiveReload: Enable/disable plug-ins`,并选择`Simple Reload with delay (400ms)`或者`Simple Reload with delay`，两者区别在于前者有400ms延迟。

**全部安装配置成功后，编辑md文件，保存，alt+m在浏览器中打开。继续编辑md文件，保存，浏览器中会自动刷新。**


