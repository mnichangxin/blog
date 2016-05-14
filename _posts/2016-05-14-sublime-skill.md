---
layout: post
title: "sublime常用快捷键和插件"
categories: Sublime
tags: 开发工具
---

无插件不神器，`sublime`作为当今前端开发者的必备工具，掌握其中的技巧可以提高效率，"工欲善其事，必先利其器"嘛！

## 常用快捷键

* ctrl + d

选中当前选中项的下一个匹配项

* alt + f3

选中当前选中项的所有匹配项

* ctrl + shift + '

选择当前内容的上一级包含标签(需要`Emmet`插件)

* ctrl + shift + a 

选择当前内容的上一级及更上一级的标签(需要`Emmet`插件)

* ctrl + shift + m

选择括号之间的一切

* ctrl + shift + ↑ 或 ctrl + shift + ↓

上移或下移行

* ctrl + shift + d 

如果已经选中文本，它会复制选中项；否则，把光标放在行上，会复制整行

* ctrl + [ 或 ctrl + ]

增加或减少缩进

* ctrl + shift + v

粘贴时保持缩进和排列

* ctrl + shift + ;

移除与光标相关的父标签

* ctrl + /

注释选中项

## 常用插件

* 插件安装方式一: 直接安装

> 安装Sublime text 3插件很方便，可以直接下载安装包解压缩到Packages目录（菜单->preferences->packages）。

* 插件安装方式二: 使用Package Control组件安装

> 按Ctrl+`调出console（注：安装有QQ输入法的这个快捷键会有冲突的，输入法属性设置-输入法管理-取消热键切换至QQ拼音）粘贴以下代码到底部命令行并回车：

```ruby
    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

> 重启Sublime Text 3。如果在Perferences->package settings中看到package control这一项，则安装成功。按下Ctrl+Shift+P调出命令面板输入install 调出 Install Package 选项并回车，然后在列表中选中要安装的插件。

### 插件推荐

* [MarkDown Editing](https://github.com/SublimeText-Markdown/MarkdownEditing)

SublimeText不仅仅是能够查看和编辑 Markdown 文件，但它会视它们为格式很糟糕的纯文本。这个插件通过适当的颜色高亮和其它功能来更好地完成这些任务

* [MarkDown Preview](https://github.com/revolunet/sublimetext-markdown-preview)

一个可以生成MarkDown文件预览效果的插件，支持 `Github` 的预览效果

* [SublimeTmpl](https://github.com/kairyou/SublimeTmpl)

支持绝大多数语言的模板，可以自定义模板和快捷键哟

* [ColorPicker](https://github.com/weslly/ColorPicker)

当做 `Sublime` 的内置颜色版使用，快捷键 `ctrl + shift + c`

* [SublimeREPL](https://github.com/wuub/SublimeREPL)

`SublimeREPL` 允许你在 `Sublime Text` 中运行各种语言（`NodeJS`， `Python`，`Ruby`， `Scala` 和 `Haskell` 等等）

* [SublimeLinter](https://github.com/SublimeLinter)

用于高亮提示用户编写的代码中存在的不规范和错误的写法，支持 `JavaScript`、`CSS`、`HTML`、`Java`、`PHP`、`Python`、`Ruby` 等十多种开发语言。这篇文章介绍如何在 ``Windows` 中配置 `SublimeLinter` 进行 `JS` & `CSS` 校验: [借助SublimeLinter编写高质量的JavaScript&CSS代码](http://www.cnblogs.com/lhb25/archive/2013/05/02/sublimelinter-for-js-css-coding.html)

* [CSScomb](https://github.com/csscomb/CSScomb-for-Sublime)

强迫症必备。快捷键 `ctrl + shift + c` ,可对 `CSS` 进行重排

## 参考文章

* [常用的16个sublime Tex快捷键](http://blog.jobbole.com/82527/)

* [如何优雅地使用Sublime Text3](http://www.jianshu.com/p/3cb5c6f2421c)