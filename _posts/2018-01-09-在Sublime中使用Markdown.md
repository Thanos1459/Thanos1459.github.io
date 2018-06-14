---
layout:     post
title:      在Sublime中使用Markdown
subtitle:   Sublime+Markdown+实时预览
date:       2017-07-10
author:     BY
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - 工具
---

## 引言
这两天想找一款比较好的markdown编辑器，找来找去觉得还是sublime比较给力,我们只需要庄上两个插件就可以很爽的使用它了，下面我们先介绍一下Sublime Text

## Sublime Text介绍
Sublime Text 是一款流行的代码编辑器软件，也是HTML和散文先进的文本编辑器，可运行在Linux，Windows和Mac OS X,同时也是我许多程序员最喜欢使用的一款文本编辑器软件，没有之一。他有强大的插件功能，我们可以按照自己的需求来添加各种插件。

## Sublimet Text3 使用 MarkDown 界面
![](https://raw.githubusercontent.com/cropwatchman/cropwatchman.github.io/master/img/Sublime%2BMarkDown.JPG)
## 安装过程

### 安装Package Control
   说白了就是一个类似插件管理包的小工具，我们可以通过使用它来搜索、安装和卸载等相关插件，Package Control的安装过程很简单，下面提供Sublime Text3 和 Sublime Text2 的安装方式。
   使用Ctrl+`快捷键或者通过View->Show Console菜单打开命令行，粘贴如下代码：
   
   1、Sublime Text3

   ```
    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
   ```
  
 2、Sublime Text2

```
import urllib2,os; pf='Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler( ))); open( os.path.join( ipp, pf), 'wb' ).write( urllib2.urlopen( 'http://sublime.wbond.net/' +pf.replace( ' ','%20' )).read()); print( 'Please restart Sublime Text to finish installation')
```
### 安装插件Markdown Editing + MarkdownLivePreview
在`Perference` → `Package Control` → `Install` Package中输入两款插件的名字，找到相应插件，点击即可自动完成安装，安装完重启Sublime；
简单设置：`Preferences` → `Package Settings` → `MarkdownLivePreview` → `Setting`，打开后将左边default的设置代码复制到右边User栏，找到
```
"markdown_live_preview_on_open": false
```
把false改为true，保存。

## 使用过程
使用Sublime新建一个文件，右下角选择编辑方式为“Markdown”，将其保存为.md文件，选择保存位置并单击确定，此时自动弹出Markdown文本编辑框（左）和预览框（右），然后就开始愉快地编辑吧。
