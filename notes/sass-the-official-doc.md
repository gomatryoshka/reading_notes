# Sass官方文档阅读笔记

2015-02-07 by Mr.Raindrop

## 前言

网页的布局和样式是由css来决定的，当网站规模比较大元素比较多的时候，css的量级也会变得很庞大，css代码将变得难以维护，于是就出现了css预处理器，通过加入一些类似编程语言的特性，让编写css变得更高效更易于维护。和less一样，sass也是一种流行的css预处理器。sass总体上和less差不多，但是比less使用者更多，因为其对css兼容性更好。

## 运行sass

首先推荐一个[sass在线编译器](http://sassmeister.com/)，可以用这个做测试

#### 命令行

在本地命令行里运行sass，需要首先安装ruby（这里就不多做介绍了），ruby装好用gem安装sass包：

```bash
gem install sass
```

然后就可以使用sass了，比如：

```bash
sass foo.scss foo.css
```

上面的命令将``foo.scss``文件编译为``foo.css``样式文件；文件后缀名scss意思是sassy css，强调sass对css代码的完全兼容

sass的编译选项：``nested``（默认值）、``expanded``、``compact``、``compressed`` （可以望文生义一下）

设置编译选项：

```bash
sass --style compressed foo.sass foo.css
```

可以自动检测文件或文件夹变动并生成编译后的css

自动检测文件：

```bash
sass --watch foo.scss:foo.css
```

自动检测文件夹：


```bash
sass --watch app/sass:app/css
```

## 变量

从下面开始介绍sass的基本语法和特性，并和less进行对比

变量定义：``$my-color: #fff;`` （less用@定义和调用变量）

变量调用：

```sass
div {
	color: $my-color;
}
```

变量用于字符串，使用#{$var-name}调用，和less的``@{var-name}``类似：

```sass
$my-attr: color;

// 使用变量
div {
	#{$my-attr}: #fff; 
}
```







