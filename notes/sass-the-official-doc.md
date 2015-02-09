# Sass官方文档阅读笔记

2015-02-07 by Mr.Raindrop

## 前言

网页的布局和样式是由css来决定的，当网站规模比较大元素比较多的时候，css的量级也会变得很庞大，css代码将变得难以维护，于是就出现了css预处理器，通过加入一些类似编程语言的特性，让编写css变得更高效更易于维护。和less一样，sass也是一种流行的css预处理器。sass总体上和less差不多，但是比less使用者更多，因为其对css兼容性更好。

以下结合sass官方文档和less做一个简单的对比，每一小节如果两者用法有区别则用表格列出方便对比。

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

sass语法和scss语法可以互相转换：

```bash
sass-convert style.sass style.scss
```

```bash
sass-convert style.scss style.sass
```

## sass和scss

sass语法写起来更简洁，去掉了大括号和分号，而使用缩进和换行来处理语义间隔；这点类似coffeeScript或者python，看下面的sass的例子：

```sass
.some-class
	color: red
	background-color: #fff
```

scss全称是sassy css，写起来跟更接近css，除此之外它和sass没有区别；上面的代码用scss写出来是这样的：

```css
.some-class {
	color: red;
	background-color: #fff;
}
```

可以看出，在没使用sass的扩展特性情况下，scss写法和css没有区别

**以下不做特别说明则sass均使用scss的书写方式**

## 变量

| 语言     | 声明   |  作为数值  | 作为字符串 |  字符串引号  |
| -------- | ------  | ---------- | ----------  | ------------- |
| less     |  @var   |    @var    |   @{var}    | 引号保持不变 |
| sass     |   $var  |   $var     |  #{$var}    | 自动去引号   |

变量定义：``$my-color: #fff;`` （less用@定义和调用变量）

变量调用：

```sass
div {
	color: $my-color;
}
```

#### 作为字符串

变量用于字符串，使用#{$var-name}调用，和less的``@{var-name}``类似：

```sass
$my-attr: "color";

// 使用变量
div {
	#{$my-attr}: #fff;
}
```

注意上面的``#{$my-attr}``会被编译为``color``，但如果是less则会保留``"color"``的双引号，变成``"color": #fff``

#### 变量作用域

变量作用域是和层级相关的，内嵌的变量不能被外层的变量看到；除非为内嵌的变量加上``!global``全局声明，如：

```sass
#mine {
  $width: 5em !global;		// 全局变量
  width: $width;
}

#div {
  width: $width;
}
```

#### 设置默认值

使用``!default``为变量设置默认值，这样如果前面有赋值则不会使用该值，如果没有赋值则使用该值作为变量的值

```sass
$content: '1st val';
$content: '2nd val' !default;
$new-content: '3rd val' !default;
```

## 注释

和less一样，可以使用css的块注释``/* comments */``，也可以使用一般编程语言中都用到的行注释``// commments``

块注释会被输出到css中，行注释不会

块注释不会被输出到compressed css中，除非块注释的第一个字符是``!``，如：

```sass
/*! this is final output */
```

## 运算

运算和less没有区别

```sass
div {
	padding: (5px * 2);
	width: (100px + 400px);
}
```

## 父元素和嵌套

| 语言     |        &的位置         |   合并值（如font） |
| -------- | ------------------------------------------- | --------------------------------------------- |
| less | 可以出现在名字的任意位置，如``my-&`` | \-\-\-\- |
| sass | 只能出现在名字的开头，如``&-mine``，但是前面可以出现其他选择器，如``.xx &-mine`` | 使用名字空间处理，即``font-weight``拆成``font { weight: xxx; }`` |

#### 父选择符&

和less一样，sass允许层级嵌套，并且可以引用父元素；在less中引用父元素使用``&``，在sass中引用父元素也是用``&``

&在less和sass中使用上的区别如上表所描述的，less可以将&插入到一个名字中的任何位置：

```less
.some-class {
	my-&-free: {
		color: red;
	}
}
```

上面这样的事情sass是做不到的，sass中&只能作为名字的前缀：``&-some-content``，而不能是``foo-&-bar``

#### 合并值

对于font这样的符合属性，可以使用namespace font来实现

```css
.some {
	font-family: fantasy;
	font-size: 30em;
	font-weight: bold;
}
```

用sass可以这样写：

```sass
.some {
	font {
		family: fantasy;
		size: 30em;
		weight: bold;
	}
}
```

名字空间也可以是已经赋了值的：

```sass
.some {
	font: fantasy, 30em {
		weight: bold;
	}
}
```

上面的形式编译成css是这样的（保留已赋值部分，块里面的属性则每条单独用font-xxx的方式添加）：

```css
.some {
  font: fantasy, 30em;
  font-weight: bold;
}
```

## 继承

| 语言     |         语法         |  仅用于继承的选择器 |
| -------- | -------------------- | --------------------- |
| less     |  :extend(.parent);   | 只有仅用于混入的mixin，没有仅用于继承的选择器 |
| sass     |  @extend:  .parent;  | 使用place holder selectors （%xxx） |

sass和less都包含选择器继承，但是用法上稍有区别；less使用``:extend()``伪类作为继承方式，而sass中使用``@extend xxx;``语句

#### place holder selectors

仅用于继承的选择器，不作为css输出（less中有仅用于继承的mixin）：

```sass
.some-ct %some-class {
	color: red;
}

.mine {
	@extend %some-class;
}
```

编译成css为：

```css
.some-ct .mine {
  color: red;
}

```

## mixin

| 语言     |         声明         |        调用       |
| -------- | -------------------- | ------------------ |
| less     | 不需要单独声明，类选择器和id选择器可以作为mixin | .mixin(); 或 .mixin; |
| sass     | @mixin xxx { ... }  |   @include xxx;    |

less中只要是简单类选择器或者id选择器就可以拿来作为mixin在别的地方调用，调用方式也是简单的``mixin();``，而sass中使用了两个关键字，``@mixin``和``@include``，其中``@mixin``用于声明一个mixin，而``@include``用于调用一个mixin

和less一样，sass也可以向mixin传递参数，也可以设置默认参数值

## 函数

| 语言     |         自定义函数         |
| -------- | -------------------- |
| less     | 无此功能，可使用mixin间接实现类似函数功能 |
| sass     | @function关键字 |

#### 预置函数

less和sass中都提供了各种预置函数，比如``lighten()``，``darken()``等，调用的方法也是一样的，直接调用就可以了，比如：

```sass
div {
	color: lighten(#000, 10%);
}
```

#### 自定义函数

less使用mixin达到和函数类似的效果，而sass包含@function关键字来定义自定义函数：

```sass
@function sum($a, $b) {
	@return $a + $b;
}

.some-class {
	$a: 10px;
	$b: 20px;
	width: sum($a, $b);
}
```

上面的例子在less中可以这样实现：

```less
.sum(@a, @b) {
	@sum: @a + @b;
}

.some-class {
	@a: 10px;
	@b: 20px;
	.sum(@a, @b);
	width: @sum;
}
```

#### 函数的参数

调用的时候使用命名参数方式调用，则参数顺序可以打乱，如``myFunc($var2: val2, $var1: val1);``；这点和less的mixin调用里明确参数名字的调用方式是一致的

## 导入（import）

和sass一样，也是使用``@import``来导入文件

## 条件语句

| 语言     |         条件判断         |  等于判断    |
| -------- | -------------------- | ----------------- |
| less     | 使用 when 关键字，如.class when( ... ) { ... } | 使用``=`` |
| sass     | 使用 @if 和 @else 关键字  | 使用``==`` |

less使用guards进行条件判断（包括mixin guards和css guards），sass使用``@if``、``@else``语句进行判断：

```sass
$test: true;

div {
	@if ($test) {
		color: green;
	} @else {
		color: red;
	} 
}
```

## 循环

| 语言     |         循环实现         |
| -------- | ------------------------- |
| less     | 使用 when 关键字并调用自身（mixin调用） |
| sass     | 使用 @for、@while、@each 关键字 |

less里的循环是使用when判断条件，在mixin里先更新条件然后继续调用自身来实现的，类似一般编程语言中的while结构；而sass里实现循环则使用@for、@while或者@each关键字：

#### @for

```sass
@for $i from 1 through 10 {
	.class-#{$i} {
		background-color: darken(#eee, 10% * $i);
	}
}
```

@for循环有两种格式：

* ``@for $var from <start> through <end> { ... }``
* ``@for $var from <start> to <end> { ... }``

两者区别在于through会包括最后的``<end>``而to则不会包含最后的``<end>``

#### @while

```sass
$i: 0;
@while $i < 10 {
	$i: $i + 1;
	.class-#{$i} {
		background-color: darken(#fff, 10% * $i);
	}
}
```

#### @each

```sass
@each $i in 1,2,3,4,5,6,7,8,9,10 {
	.class-#{$i} {
		background-color: darken(#fff, 10% * $i);
	}
}
```

语法：``@each $var in <list or map> { ... }``

这里``<list or map>``是SassScript的两个类型：

* list：``(item1, item2, item3, ...)``，也可以是多值列表：``(item1a, item1b, item1c), (item2a, item2b, item2c), ...``；list除了用逗号``,``分隔，还可以用空格分隔，如``0 2px 0 2px``，注意空格的优先级高于逗号，如``1 2, 3``相当于``(1 2), 3``

list可以使用list functions做处理

来个多值列表的例子：

```sass
@each $animal, $color, $cursor in (puma, black, default),
								(sea-slug, blue, pointer),
								(egret, white, move) {
	.#{$animal}-icon {
		background-image: url('/images/#{$animal}.png');
		border: 2px solid $color;
		cursor: $cursor;
	}
}
```

编译出来的css如下：

```css
.puma-icon {
  background-image: url("/images/puma.png");
  border: 2px solid black;
  cursor: default;
}

.sea-slug-icon {
  background-image: url("/images/sea-slug.png");
  border: 2px solid blue;
  cursor: pointer;
}

.egret-icon {
  background-image: url("/images/egret.png");
  border: 2px solid white;
  cursor: move;
}
```

* map：``(key1: val1, key2: val2, key3, val3)``；map可以用map functions，也可以当做``(key1 val1, key2 val2, key3 val3)``的list，调用list function

比如：

```sass
@each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em) {
	#{$header} {
		font-size: $size;
	}
}
```


