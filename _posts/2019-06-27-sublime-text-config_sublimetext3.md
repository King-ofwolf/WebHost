---
layout: post
title:  "基于常用的package的sublime text3的快速自定义配置"
date:   2019-06-27 09:49:29
categories: SublimeText3
tags: sublimetext3 package config
excerpt: 新安装的sublimetext的功能有限，需要安装各种插件才能使我们在编写代码时能够得心应手。俗话说，磨刀不误砍柴工，配置好了一款经常使用的插件，能够大大提升我们coding的效率。
mathjax: true
---
* content
{:toc}



## Packages list

我常用的包：

| package  | infomation |
| :------- | :--------- |
| All Autocomplete|代码自动补全(搜索所有打开的文件来寻找匹配的提示词)|
|BracketHighlighter|标签括号自动匹配高亮|
|Git|实现git功能|
|GitGutter|git状态侧边栏|
|SideBarEnhancements|文件侧边栏右键菜单拓展|
|SublimeCodeIntel|代码自动完成引擎，函数跳转|
|SublimeLinter|代码校验主插件，需辅助SublimeLinter-语言进行检测|
|FileHeader|自动加入文件头部|
|ChineseLocalizations|汉化|

感觉会有用的包：

| package  | infomation|
| :------- | :-------- |
|AutoFileName|快速帮助你在文件中写路径|
|BufferScroll|可以轻松书写一个文件多个位置|
|Color Highlighter|颜色功能|
|DocBlockr|自动补全注释|
|Emmet|使用仿CSS选择器的语法来生成HTML/CSS代码|
|SublimeTmpl|创建常用文件初始模板|
|Tag|HTML/XML标签缩进、补全和校验|
|Alignment|代码对齐|

更多常用好用的包参见<https://www.cnblogs.com/qingkong/p/5039527.html>

> 下面是部分package的自定义配置
> 
> ps:基本所有的package的自定义属性都在`Preferences` > `Package Settings`中，配置文件分为`Default`和`User`两种，`User`是我们进行配置的配置文件，而`Default`是默认配置，一般不进行更改，`User`文件中的同类配置会覆盖`Default`的配置，因此恢复默认配置即为删除`User`的配置项。配置文件使用的是类似json的格式，即`option:value`，并在最外部有个大括号。

## 自定义编译器

SublimeText自带的有大部分常用编程语言的编译器，在`Tools` > `Build System`里能看到，但是默认的编译器如C、Java在运行的过程中，是直接在SublimeText本身的console里运行的，编译指令也是固定的编译当前文件，但是这样会出现有时候C运行完之后黑框闪一下就没了，还需要手动加上`scanf("",&a)`才能让程序等我看完输出结果再手动关闭窗口，还有某些时候编译需要加入外部的lib；于此同时，java在编译的过程中也会出现各种各样的问题，因此，自定义的编译指令还是非常有必要的。

SublimeText提供自定义的编译方式，在`Tools` > `Build System` > `New Build System`，在新建的文件中输入编译配置，并保存为`Build_System_Name.sublime-build`，重启sublime即可。文件路径一般为`/Packages/User/`。

以下是我个人使用的编译配置：
* C语言编译配置
```json
{
 "cmd": ["gcc","-Wall", "${file}", "-o", "${file_path}/${file_base_name}"],
 "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
 "working_dir": "${file_path}",
 "selector": "source.c, source.c++",
 "encoding":"cp936",
 "variants":
 [
 /*{
  "name": "Run",
  "cmd": ["${file_path}/${file_base_name}"]
 },*/
 {
 "name": "Run-Command",
 "cmd": ["cmd", "/c", "gcc", "-Wall","${file}", "-o", "${file_path}/${file_base_name}", "&&", "start", "cmd", "/c", "${file_path}/${file_base_name} & echo.&pause"]
 },/*
 {
 "name": "Run-Shell",
 "shell_cmd": " start cmd /c \"\"${file_path}/${file_base_name}\"&pause\" "
 }*/
 ]
 }
```
* JAVA编译配置
```json
{
	"cmd": ["cmd", "/c","javac","${file}", "&&", "start", "cmd", "/k", "java","${file_base_name}"],
	"file_regex": "^(...*?):([0-9]*):?([0-9]*)",
	"working_dir": "${file_path}",
	"selector": "source.java",
	"encoding":"cp936"
}
```

## 自定义文件头部

SublimeText3的package中，FileHeader需要自定义配置才能够针对不同的文件自动插入对应的文件头。

* 配置自定义文件头部

打开`Preferences` > `Browse Packages`，进入`FileHeader/template/header/`，这里的文件都是对应语言的文件头，但都是空的，因此对应的文件插入的文件头也是空。比如我们需要在新建Python文件的时候自动生成以下头部：
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# @Author: kingofwolf
# @Date:   2019-06-27 10:20:11
# @Last Modified by:   kingofwolf
# @Last Modified time: 2019-06-27 10:20:11
# @Email:	wangshenglingQQ@163.com
'Info: a Python file '
__author__ = 'Wang'
__version__= '2019-06-27 10:20:11'
```
只需要打开`Python.tmpl`文件,并写入以下内容：
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# @Author: {{author}}
# @Date:   {{create_time}}
# @Last Modified by:   {{last_modified_by}}
# @Last Modified time: {{last_modified_time}}
# @Email:	{{email}}
'Info: a Python file '
__author__ = 'Wang'
__version__= '{{last_modified_time}}'
```
其中，`{{author}}`等被两个大括号包围的是变量名，我们可以自定义变量名和变量值，后面将会讲到，而`author`,`create_time`,`last_modified_by`等都是系统默认变量。除了变量外，其他字符
都将原原本本的插入到文件头部。

* 配置变量

同时打开`Preferences` > `Package Settings` > `FileHeader` > `Default`和`Preferences` > `Package Settings` > `FileHeader` > `User`，从`FileHeader.sublime-settings - FileHeader`中复制所需要配置的配置项，复制粘贴到`FileHeader.sublime-User`中，如：
```json
{
	"Default":{
		"email" : "email_address@163.com",
	}
}
```
凡是配置过的变量，在`tmpl`文件中都能够使用`{{value_name}}`的形式进行使用

* 我使用的配置

> Python.tmpl

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# @Author: {{author}}
# @Date:   {{create_time}}
# @Last Modified by:   {{last_modified_by}}
# @Last Modified time: {{last_modified_time}}
# @Email:	{{email}}
'Info: a Python file '
__author__ = 'Wang'
__version__= '{{last_modified_time}}'
```
> Markdown.tmpl

```Markdown
---
layout: post
title:  ""
date:   {{create_time}}
categories: 
tags: 
excerpt: 
mathjax: true
---
* content
{:toc}
```
