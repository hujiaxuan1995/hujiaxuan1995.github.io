title: Sass入门总结
date: 2016-01-24 21:44:42
tags: css
categories: css
---
刚刚看了Sass，顿时觉得稍带程序思维的编码过程果然比一行行css代码的堆砌要优美许多，于是从官方文档上总结下这篇文章，忘记的时候可以来看看。
# 什么是Sass
一种css的预处理器。CSS基本上是设计师的工具，不是程序员的工具。在程序员眼里，CSS是一件很麻烦的东西。它没有变量，也没有条件语句，只是一行行单纯的描述，写起来相当费事。而Sass 扩展了 CSS3，增加了规则、变量、混入、选择器、继承等等特性，使sass成为一门简单的动态语言。
# Sass的安装
Sass的安装需要ruby([win](http://rubyinstaller.org/),[linux](https://www.ruby-lang.org/en/downloads/))环境，安装完成之后可以用 <span style="color: pink !important">`ruby -v`</span>查看是否安装成功。ruby安装完成之后使用命令行`gem install sass`安装Sass，Sass安装完成之后通过`sass -v`查看是否安装成功。

# Sass编译
### 单文件编译
sass <要编译的Sass文件路径>/style.scss:<要输出CSS文件路径>/style.css
### 多文件编译
sass sass/:css/
上面的命令表示将项目中“sass”文件夹中所有“.scss”(“.sass”)文件编译成“.css”文件，并且将这些 CSS 文件都放在项目中“css”文件夹中。
### 自动检测
sass --watch <要编译的Sass文件路径>/style.scss:<要输出CSS文件路径>/style.css
开启“watch”功能，这样只要你的代码进行任保修改，都能自动监测到代码的变化，并且给你直接编译出来。
### GUI界面工具编译
如果不用命令行的话，也有图形界面编译工具可以利用，如下

* Koala（[http://koala-app.com/](http://koala-app.com/)）
* Compass.app（[http://compass.kkbox.com/](http://compass.kkbox.com/)）
* Scout（[http://mhs.github.io/scout-app/](http://mhs.github.io/scout-app/)）
* CodeKit（[https://incident57.com/codekit/index.html](https://incident57.com/codekit/index.html)）
* Prepros（[https://prepros.io/](https://prepros.io/)）

### Sass的四种编译风格
* 嵌套输出方式nested
  <span style="color:dark !important;">`sass --watch test.scss:test.css --style nested`</span>
  编译出的CSS样式风格：
```
  div{
   position: relative;
   height :100px;
   width: 100px; }
```
  可见这样编译出的代码是相当整洁的，但是不利于存储和加载。

* 展开输出方式expanded
   <span style="color:dark !important;">`sass --watch test.scss:test.css --style expanded`</span>
    编译出的CSS样式风格：
```
   div{
        position: relative;
        height :100px;
        width: 100px; 
        }
```
    与nested类似。
    
* 紧凑输出方式 compact
    <span style="color:dark !important;">`sass --watch test.scss:test.css --style compact`</span>
    编译出的CSS样式风格：
```
     div1{position: relative;height :100px;width: 100px; }
     div2{position: relative;height :100px;width: 100px; }
```

* 压缩输出方式 compressed
  <span style="color:dark !important;">`sass --watch test.scss:test.css --style compressed`</span>
  编译出的CSS样式风格：
                    div1{position:relative;height:100px;width:100px;}div2{position:relative;height:100px;width:100px;}
  压缩输出方式会去掉标准的 Sass 和 CSS 注释及空格。


### Sass命令的一些参数如下
![sass](https://raw.githubusercontent.com/hujiaxuan1995/MyImage/master/sass.jpg)

# Sass语法


