# PHP与MySQL程序设计（第四版）读书笔记

2015-01-20 by Mr.Raindrop

### PHP配置的三种途径

* php.ini
* httpd.conf（php_value, php_flag, php_admin_value, php_admin_flag，如禁用短标签：``php_admin_flag short_open_tag off``）
* 在执行脚本中：ini_set()函数，如：

```php
ini_set('max_execution_time', '60');
```

### 输入输出

echo 后可跟多个字符串，用逗号分隔，如

```php
echo "a", "b", "c";
```

#### 双引号和单引号的区别？

* 在双引号内直接包含变量，会**对$符号做处理**，打印出来是变量的值，如``echo "pre $val after.";``
* 在单引号内直接包含变量，**不对$符号做处理**，即当做普通字符。

#### print和echo哪个执行更快？

``print("xxx")`` 总是返回1（写成``print "xxx"``也一样）
而``echo``不返回任何值，所以**echo比print更快**

**printf**可以实现和c一样的格式化输出，如``printf("%d is a number.", 1000)``;
（这里单双引号的区别和print/echo里的一样）

**sprintf**和printf一样，但是不直接打印，而是返回一个字符串。

精确控制小数位数：

```php
printf("<br />%.2f is a float.", 3.1415926);
```

### 类型


#### 数据类型转换

**强制类型转换**用``(type)``——类似C/C++， 如：``(array)``

* php里居然可以把一个标量类型（非数组、非对象）强制转换为一个**数组**(array)：将这个类型做为该数组的第一个元素

* php可以把对象类型转换成一个**关联数组**

* php里可以把一个标量类型（非数组非对象类型）转换成一个**对象**(object)：将该值作为对象**"scalar"**这个key的值，即通过``obj->scalar``可以访问到原来的标量值

* 把**数组转换成对象**：抛弃数字索引的元素，保留字符串key索引的元素

其他可以通过``(type_name)``强制转换的类型：

* ``bool`` 或 ``boolean`` 布尔
* ``int`` 或 ``integer`` 整型
* ``object`` 对象
* ``real`` 或 ``double`` 或 ``float`` 浮点数
* ``string`` 字符串

* 通过``gettype()``函数获取类型名称：

* 还能使用``settype()``设置数据类型

* 用echo输出``xxx['abc']``这样的数组或对象变量时，应用大括号括起来，如：

```php
echo "{$_SERVER['REMOTE_ADDR']}";
```

### 模块化

``include``, ``include_once``, ``require``, ``require_once``

#### include 和 require的两个区别：（php5.5是一个区别）

1. include如果在if块的某个分支中，未执行该分支则不引用，而require始终会引用；（**注：php5.5实验验证这两者在if-else中表现一致了，即如果不符合条件，没有执行该分支的话，则均不引用**）

2. require**出错**则停止执行，而include**出错**脚本仍然继续执行；

### 函数

php函数定义和js类似，也是使用``function``关键字

**php里函数的调用可以在函数的定义之前。**

#### 局部与全局？

在局部作用域了访问全局作用域的变量：在使用之前声明一下``global``关键字，如在函数内部声明``global $xxx;`` 或者不用声明，直接使用``$GLOBALS["xxx"]``

#### 静态变量

``static $xxx;`` 效果和c/c++的static效果一样

#### PHP的超级全局变量

* ``$GLOBALS``
* ``$_SERVER``
* ``$_GET``
* ``$_POST``
* ``$_COOKIE``
* ``$_FILES``（多维数组，如``$_FILES['upload-name']['type']``、``$_FILES['upload-name']['size']``、``$_FILES['upload-name']['name']``等，另外除了``type``、``size``、``name``还有``tmp_name``和``error``） (``$_FIELS['upload-name']['error']``在上传成功之后也有值，其值 === UPLOAD_ERR_OK)
* ``\$_ENV``
* ``\$_REQUEST``

**为什么说使用``$GLOBALS``超级成员变量代替``$_GET``/``$_POST``/``$_COOKIE``不安全？**

#### 使用define()定义常量

```php
define('PI', 3.14);
// 如果对PI赋值：PI = 2; 会直接报错。
```

#### 指定函数的参数类型

**只能指定为array或者自定义的类类型**，不能指定为标量类型，如integer, boolean, string, double等

#### 为函数传递默认参数

```php
function calc($price, $price2="", $price3="") {
    echo $price + $price2 + $price3;
}
```


### 数组（关联数组）

* 和js类似，数组是一种关联数组的概念，元素可以是任何类型，可以指定字符串的key.

* Array可保存不是同一类型的元素

* 关联数组的定义方式：

```php
$arr = array("xx"=>"x", "yy"=>"y")``;
```

* php的数组不检查越界，即可以这样：

```php
$arr[0] = "a";
$arr[1] = "b";
```

相当于直接定义了：
```php
$arr = array("a", "b");
```

* 使用``list()``构造获取函数返回的数组：

```php
function t() {
    return array(1, 2, 3);
}
list($a, $b, $c) = t();
```

( 用list()提取数组 )

* 使用数值范围定义数组``range()``

```php
// 等同于$die = array(1, 2, 3, 4, 5, 6);
$die = range(1, 6);
```

还可设置增长步长:
```php
// 等同于$even = array(0, 2, 4, 6, 8, 10);
$even = range(0, 10, 2);
```

还可用于字符数组：
```php
$letters = range("A", "Z");
```

#### 数组操作

* 头：``array_unshift()``, ``array_shift()``, 
* 尾：``array_push()``, ``array_pop()``

#### 打印数组

* ``vprintf()``
* ``print_r($arr)`` 也可以用于打印对象

```php
$str = print_r($arr, TRUE);
```

#### 定位数组元素

* 是否包含：``in_array($val, $arr)``
* 搜索关联数组键：``array_key_exists($key, $arr)`` 返回boolean
* 搜索关联数组值：``array_search($val, $arr)``  返回的是$key，如果没找到则返回false（应该用`` === false``来检测）

#### 获取所有数组键

* ``array_keys($arr)``

#### 迭代数组

* ``key()``, ``current()`` 获取当前key或value
* ``end()`` 移动指针到最后一个元素，并返回该元素
* ``next()`` 移动指针到下一个
* ``prev()`` 向前移动指针
* ``each()`` 返回key和val的数组，并移动指针到下一个

注意：``each``和``key``/``current``不同的是会自动移动指针到下一个而无需调用``next``

* ``reset()`` 将指针移动到第一个元素
* ``array_walk($arr, "func_name")`` 使用自定义函数遍历每个元素

```php
// 也可以这样
array_walk($arr, function($value, $key) {
     // ........
});
```

* ``array_walk_recursive()`` 当涉及到多维数组时可递归遍历
* ``count($var[, $mod])`` 元素个数
* ``sizeof($var[, $mod])`` count()的别名

注意：``count``如果设置了第二个参数mode设置为COUNT_RECURSIVE或者1，则对于数组里内嵌的数组，除了内嵌数组的元素个数会算进来，数组本身也会算作一个元素。如果没设置即保持默认，则内嵌数组记为一个元素。

#### 统计元素出现频度

* ``array_count_values($arr)`` 返回一个关联数组

#### 确定唯一元素

* ``array_unique()`` 会删除数组中重复的元素

#### 置换数组键和值

* ``array_flip($arr)``

#### 反向重置数组

* ``array_reverse($arr[, preserve_keys])`` 逆序
可以设置``priserve_keys``为true，这样键可以保留和值的映射对不变（关联键则不受影响，只有数字键才受影响，对于关联键总是保持映射对不变）。

#### 数组排序

* ``sort($arr, sort_flags)`` 可选标记：``SORT_NUMERIC``, ``SORT_REGULAR``, ``SORT_STRING``， 升序排列
* ``rsort($arr, flags)`` 降序排列
* ``asort($arr, flags)`` 保持键值关联的排序
* ``arsort($arr, flags)`` 保持键值关联的逆序排序
* ``natsort($arr)`` 自然排序
* ``natcasesort($arr)`` 不区分大小写的自然排序
* ``ksort($arr, flags)`` 按键给数组排序
* ``krsort($arr, flags)`` 按键给数组降序排序
* ``usort($arr, 'function_name')`` 按自定义比较方法排序

#### 合并数组

* ``array_merge($arr1, $arr2, ...)`` 对于非数字键，相同键的情况下后面的覆盖前面的，对于数字键，则会一直累加。
* ``array_merge_recursive()`` 相同键的情况会将值合并为一个数组

#### 随机打乱数组

* ``shuffle($arr)``

#### 合并key数组和value数组成关联数组

* ``array_combine($keys, $values)``

注意``array_merge``和``array_combine``的区别

#### 拆分数组

* ``array_slice($arr, offset, length, preseve_keys)`` ``length``和``preserve_keys``为可选参数

#### 接合数组

* ``array_splice($arr, offset, length, array_replacement)`` 和js数组的``splice``方法有点类似

#### 求数组交集

* ``array_intersect($arr1, $arr2, ...)`` 求值的交集，会保留键。当有多个键对应时，取最前面的那个键.
* ``array_intersect_key()`` 求键的交集
* ``array_intersect_ukey()`` 由用户定义比较键的算法
* ``array_intersect_assoc()`` 针对关联数组，即同时考虑键/值对的交集

#### 求数组差集

* ``array_diff($arr, $arr1, $arr2, ...)`` 求$arr中在其他$arr1, $arr2...等中都没出现过的值，和intersect正相反
* ``array_diff_key()`` 比较键
* ``array_diff_ukey()`` 用户定义比较键的算法
* ``array_diff_assoc()`` 求关联数组的差集
另其他自定义方式： ``array_udiff_assoc``, ``array_udiff_uassoc``, ``array_diff_uassoc``

#### 数组求和

* ``array_sum()`` 如果元素中有非数值类型则忽略该元素

#### 数组划分

* ``array_chunk($arr, $size)`` 按每份``$size``个元素划分成多个二维数组

### 面向对象：

``final``关键字 加载属性定义前，防止属性在子类中覆盖

#### 关于__get和__set
``__get($propName)``、``__set($propName, $propValue)`` 只在访问不存在的属性名时才会被调用

**什么情况下会调用``__get``和``__set``，什么情况下不会？**

1. 如果属性声明为``private``，则__get和__set都会调用（一个做为访问器，一个做为修改器）,而且每次访问/修改时都会调用

2. 如果属性声明为``public``，则都不调用

3. 如果属性声明为``var``，效果和``public``一样，都不调用

4. 如果根本没有声明某个属性，只在第一次设置该值时调用__set，以后无论设置还是修改都不调用

#### const关键字

在类中的常量直接用const关键字定义：

```php
const PI = '3.1415...';
```

这样使用类中的常量：
```php
echo mathClass::PI;
```

const也可以用在全局作用域下。

``public function``定义公开方法可以省略public关键字

#### abstract关键字

包含abstract方法的类必须声明为``abstract class``

#### 构造函数

使用``__construct``或者使用类名同名函数，两者都存在的话优先使用前者

调用基类的构造函数： ``parent::__construct()``

指定调用哪个基类的构造函数：``baseClassName::__construct()``

#### 析构函数

``__destruct``

也可以用通过``prarent::__destruct()``调用基类的析构函数

#### 继承：

``extends``关键字

#### 使用父类方法

``parent::methodName()``

比如使用父类的构造函数：``parent::__construct()``

#### 静态属性

``static xxx``

不像c/java，不是非要在static方法里才能访问static属性

访问static属性方法：``self::$xxx``（注意和``$this->xxx``的访问方式的区别，注意$号的位置）

#### 静态方法：

``static function xxx``

调用方式：``ClassName::xxx()``

#### 动态类型检查

``instanceof``

#### 和类有关的辅助函数：

* ``class_alias(orginalName, aliasName)`` 创建类别名
* ``class_exists(className)``
* ``interface_exists(interfaceName)``
* ``method_exists(object, methodName)``
* ``get_class(object)`` 返回类名，若不是类对象则返回false
* ``get_class_methods(object/class_name)`` 获取某个类的所有方法（返回字符串数组）
* ``get_class_vars(class_name)`` 获取某个类的所有属性（返回关联数组）
* ``get_declared_calsses()``
* ``get_parent_class(object)`` 参数用类名或者对象 都可以
* ``is_a(object, calssName)`` 5.00-5.3.0删除了这个函数，返回E_STRICT警告

#### 自动加载包含类定义的的文件：

实现__autoload($class)函数

```php
// 这里假定类文件路径为'classes/ClassName.class.php'
function __autoload($class) {
    require_once("classes/$class.class.php");
}
```

### PHP高级OOP特性：

**对象克隆**，**继承**，**接口**，**抽象类**，**命名空间**

#### PHP不支持的高级OOP特性：

方法重载，操作符重载，多重继承（但支持**实现多个接口**）

#### 对象克隆

因为所有的对象都是引用，而不是值，所以需要对象克隆

使用``clone``关键字，如：

```php
destObj = clone targetObj;
```

可以通过修改类的``__clone``方法来修改clone的行为，如：

```php
function __clone() {
     $this->attr = "xxx";
}
```

#### 继承

``extends``

如果基类有构造函数，子类没有构造函数，则子类实例化时执行基类的构造函数；如果子类定义了构造函数，则执行子类的构造函数

**构造函数执行规则：在继承链上向上查找的最近的定义的构造函数**

**self关键词是在编译时确定作用域而非运行时确定作用域**，导致：

子类调用基类Base的里某个方法，里面通过self访问了某个静态属性``self::xxx``，则这个self因为存在于Base基类的方法定义里面，所以使用的是基类的``Base::xxx``属性，如果子类里重新定义了这个xxx属性，那么并不会访问子类的这个xxx属性

**PHP5.3对这个问题进行了补救，使用static代替self关键字即可**，如：

```php
class Base {
     public static $favSport = "Football";
     public static function watchTV() {
          // echo "<br />Watching " . self::$favSport;
          echo "<br />Watching " . static::$favSport;
     }
}

class Child extends Base {
     public static $favSport = "Basketball";
}

echo Child::watchTV();
```

#### 实现多个接口

```php
class ClassName implements Interface1, Interface2, Interface3 {
  //...
}
```


#### 命名空间：

关键字：``namespace``, ``use ... as ...`` , 分别为定义和通过别名使用命名空间

例如，在类库文件开头加上一句： 

```php
namespace Com\Wjglmore\Library;
```

在使用该类的文件中写上：

```php
use Com\Wjglmore\Library as WJG;
```

使用其中类时：

```php
$xxx = new WJG\ClassName();
```

### 错误和异常处理：

#### 配置指令：

* error_reporting = E_ALL | E_STRICT
* display_errors
* display_startup_errors

等等...

#### 错误日志：

``define_syslog_variables()`` 初始化syslog一些属性，比如``openlog()``, ``closelog()``, ``syslog()``等函数（5.3以后已经弃用）

``openlog(ident, option, facility)`` 打开系统平台日志器的连接，如：

```php
openlog("PHP", LOG_ODELAY | LOG_PERROR | LOG_PID, LOG_USER);
```

``closelog(void)`` 关闭日志连接

``syslog(prority, message)``  向日志发送消息，如：

```php
syslog(LOG_WARNING, "example log message.");
```

#### 异常处理：

``new Exception(message, code, previous)`` 参数都为可选，如：

```php
new Exception("a error occured.", 4, $e);
```

``previous``参数的作用：传入导致抛出当前异常的异常，此特性为异常链（异常嵌套），可用此参数创建栈轨迹。

#### 异常类的方法：

* ``getCode``
* ``getFile``
* ``getLine``
* ``getMessage``
* ``getPrevious``
* ``getTrace()``
* ``getTraceAsString()``

**可以扩展异常类Exception，但是不能覆盖以上这些方法**，因为都是标为``final``

**SPL**（Standard PHP Library 标准PHP库）定义的异常类：**SPL异常类**

``LogicException``, ``RuntimeException``

使用除Exception以外的异常类的意义：通过使用适当命名的异常类来提高程序的可读性

### 正则表达式和字符串：

#### 正则表达式

PHP支持两种正则实现：perl正则表达式和POSIX正则表达式（后者在PHP 5.3.0中已废弃）

PHP perl正则所包含的处理函数：

* ``preg_filter``
* ``preg_grep``
* ``preg_match``
* ``preg_match_all``
* ``preg_quote``
* ``preg_replace``
* ``preg_replace_callback``
* ``preg_split``

**用于字符串搜索正则表达式函数**

ereg(string pattern, string str);  返回TRUE或FALSE, 如ereg("([^a-z])", $username);

**注**：5.3.0已经废弃该函数，应使用``preg_match()``

**字符串替换**

``preg_replace(mixed pattern, mixed replacement, mixed subject[, limit, count]);``

注意``subject``可以是string，也可以是包含字符串的数组；另外``pattern``， ``replacement``都可以是数组；``limit``默认为-1（即不限制替换的个数）

**创建定制的替换（使用回调函数）**

``preg_replace_callback(pattern, callback, subject[, limit, count]);``

其中callback的定义方式如下：

```php
functon xxxcallback($matches) {
    // 传入的参数$matches为匹配到的项的数组
    if() { ... return ... ; }
    else { ... return ... ; }
}
```

**``preg_filter``**

和``preg_replace``接受的参数一样，但是不是替换，而是返回匹配的项

**分隔**

``split(pattern, string[, limit])``

注意，split在5.3.0已弃用，如果用字符串去分割用``explode``, 用正则分割用``preg_split()``（``preg_split``和``explode``的区别是第一个参数，前者是正则表达式，后者是字符串）；limit默认为-1

**搜索数组**

``preg_grep(pattern, array input[, flags]);``

如果flags设置为``PREG_GREP_INVERT``则返回的是不匹配的数组元素

为表示正则表达式的字符串加上转义（即该用'\'进行转义的地方加上'\'符号）：

``preg_quote(string)``

#### 字符串

和js一样，字符串可以进行下标操作（PHP中字符串是当做数组处理的）

点号（.）可以连接字符串

``explode``方法（和js的``split``类似，返回数组，但多一个参数）：``explode(delimiter, string, limit)``

参数``limit``的含义：

* 如果是正数，则返回limit个元素，其中最后一个包含后面剩下的字符串不做分割
* 如果是负数，则全部做分隔，并返回除最后limit个元素外所有的元素
* 如果是0，则当做1来处理

**确定字符串长度**

``strlen()``

**比较字符**

* ``strcmp``
* ``strcasecmp``     // 不区分大小写
* ``strspn(str1, str2[, start, length])`` 计算字符串str1中全部字符都存在于指定字符集合str2中的第一段子串的长度
* ``strcspn(str1, str2[, start, length])`` 和strspn正好相反，是所有字符都不存在于str2 范围的起始子字符串的长度

**处理大小写**

* ``strtolower``
* ``strtoupper``
* ``ucfirst`` 第一个字母大写
* ``ucwords`` 将每个单词的首字母都大写

**字符串与HTML的相互转换**

* ``nl2br`` 将\n转换成``</br>``
* ``htmlentities()`` 将类似©的字符转换成``&copy;``的形式
* ``htmlspecialchars()`` 将``<>``这样的特殊字符转换成html实体形式如``&lt;``和``&gt;``

* ``get_html_translation_table()``和``strtr()``配合转换成``htmlspecialchars``或者``htmlentities``，如``get_html_translation_table(HTML_ENTITIES)``（也可以是``HTML_SPECIALCHARS``）
* ``strtr()`` 将文本转换``成htmlspecialchars``或``htmlentities``的形式

``get_html_translation_table`` + ``strtr``的用法：

```php
$translation = get_html_translation_table(HTML_ENTITIES);
strstr("sfjljsdjfkjfa<><>&&&", $translation);
```

* ``strstr()``第二个参数还可以使用一个自定义的转换清单，只要定义为联户数组即可
* ``strip_tags(str[, allowable_tags])``去掉标签。可以指定排除某些标签，如``strip_tags($str, "<a>");``


**正则表达式函数的替代函数（考虑到性能不使用正则）**

* ``strtok(str, tokens)`` 根据tokens对字符串进行分割(但是需要自己去用while去做迭代)
* ``explode()`` 根据字符串定界符分割字符串 （**explode比preg_split快得多**）
* ``implode()`` 将数组连接成字符串
* ``strpos(str, substr[, offset])``  找到substr在str中首次出现的位置
* ``substr(str, int start, length);``
* ``str_replace(string occurence, mixed replacement, mixed str[, int count]);`` 可以替换某个字符串的所有实例（和``grep_replace``做比对）
* ``str_ireplace()`` 和``str_replace``一样，只不过不区分大小写
* ``strstr(str, occurence[, bool before needle]); `` 获取字符的一部分（根据``occurence``/``needle``的位置）
* ``substr_count()`` 参数和``substr``一样，返回``substr``出现次数
* ``substr_replace(str, replacement, start[, length]);``  替换另一个字符串的一部分（``substr_replace``和``str_replace``的区别：前者按位置替换，后者是按匹配替换）

**填充和剔除字符串**

* ``ltrim``
* ``rtrim``
* ``trim``
* ``str_pad(str, length[, pad_string, pad_type])``  填充字符串

**统计字符和单词个数**

* ``count_chars(str[, mode])``  ``mode``值从0到4代表不同意义
* ``str_word_count(str[, int format])`` 如果``format``不填则返回总数，否则根据``format``代表不同意义

**PEAR Validate_US包**：（验证常用信息项的语法包）

1. 首先安装pear，见《wamp上安装pear》这篇文章
2. 再安装Validate_US包：``pear install Validate_US-beta``
3. 使用：

```php
include "Validate/US.php";
$validator = new Validate_US();
$validator->phoneNumber("xxx");
```

### 使用文件和操作系统：

#### 文件和目录：

* ``basename(path[, suffix])`` 从路径中提取获取文件名
* ``dirname(path)`` 获取目录
* ``pathinfo()`` 返回联合数组
* ``filesize``
* ``disk_free_space``
* ``disk_total_space``

#### 查看目录大小：

```php
function directorySize($directory) {
     $directorySize = 0;
     if ($dh = @opendir($directory)) {
          while ($filename = readdir($dh)) {
               if ($filename != "." && $filename != "..") {
                    if (is_file($directory."/".$filename))
                         $directorySize += filesize($directory."/".$filename);
                    if (is_dir($directory."/".$filename))
                         $directorySize += directorySize($directory."/".$filename);
               }
          }
     }
     @closedir($dh);
     return $directorySize;
}
```


* ``fileatime`` 最后访问时间
* ``filectime`` 最后更改时间（涉及inode数据更改，如权限、所有者、组或其他inode信息）
* ``filemtime`` 最后更新时间（只涉及修改内容）

#### 文件I/O：

* ``fopen`` 打开文件，第2个参数为打开模式，有”r, r+, w, w+, a, a+, x, x+“这几种模式；第3个参数为1则可以在include_path中查找文件
* ``fclose``
* ``fgets``
* ``feof`` 测试是否到达EOF

#### 读取文件：

* ``file()`` 按换行符分割读取文件到数组中，和其他读写函数不同的是这个不需要文件句柄
* ``file_get_contents`` 这个也不需要文件句柄，读出来是一个字符串.
* ``fgetcsv(handle[, length, delimiter, enclosure])`` 需要文件句柄，读取csv文件；``delimiter``默认逗号，``enclosure``默认双引号
* ``fgets(handle[, length])`` 读取指定数目的字符（或者遇到换行符或**EOF**），省略``length``则默认为1024
* ``fgetss(handle[, length, allowable_tags])`` 和``fgets``一样，区别是剔除掉所有html和php标签；``allowable_tags``允许不剔除的标签，可以是多个，如``<h2><h3><b><a>``
* ``fgetc(handle)`` 按字符读取，遇到EOF返回FALSE
* ``fread(handle, length)`` 读length个字符，或者遇到EOF。它不考虑换行，所以适合二进制文件
* ``readfile(string filename[, int use_include_path])`` 读取整个文件到缓冲区并返回读取的字节数
* ``fscanf(handle, string format[, string var1])`` 根据预定义格式进行读取


#### 写入文件：

* ``fwrite(handle, string[, length])``
* ``fseek(handle, offset[, int whence])`` 将文件指针移动到指定偏移位置；``whence``的取值有：SEEK_CUR, SEEK_END（此时``offset``必须为负值）, SEEK_SET（默认） 
* ``ftell(handle)`` 获取当前偏移量
* ``rewind(handle)`` 指针移至开头

#### 目录内容：

* ``opendir(path[, resource context])`` 打开目录流
* ``closedir(directory_handle)`` 关闭目录流 
* ``readdir(handle)`` 解析目录内容，返回string
* ``scandir(dirname[, sort_order, context])`` PHP5的新函数，返回目录里文件和目录的名字数组

#### shell命令：

可以通过``exec()``或者``system()``来执行（但是一般出于安全考虑会在配置中对这两个函数做限制）

另外php预置了一些shell函数：

* ``rmdir(dirname)`` 不成功（比如执行此脚本的用户对该目录没有写权限）则返回FALSE
     删除文件：unlink(firename)
* ``rename(oldname, newname[, context])`` 重命名文件。失败返回false
* ``touch(filename[, int time, int atime])`` 修改最后修改时间和最后访问时间；**注**：如果文件不存在且有权限则创建该文件

#### 系统级程序执行：

清理输入：

* ``escapeshellarg(string arg)`` 用单引号对整个参数做处理
* ``escapeshellcmd(string command)`` 对shell命令字符做转义
* ``exec(cmd[, array output, int return_var])`` 输出转向到output参数中
* ``system(cmd[, int return_var])`` 输出作为结果直接返回
* ``passthru(cmd[, int return_var])`` 和system一样，只不过向调用者返回二进制输出（直接向浏览器返回二进制数据的场景下使用）
**`cmd`** 符号 **\`** 可以用来执行命令，相当于``shell_exec()``，返回输出
* ``shell_exec(cmd)`` 执行shell命令并返回输出

### 11章 PEAR

新的包安装器：**Pyrus**

包的组织规则：按类别，如``Numbers_Roman``包，类别为Numbers，引用时从Numbers文件夹中引入：

```php
include_once("Numbers/Roman.php");
```

更新包：

* ``pear upgrade [package name]``
* ``pear upgrade-all`` （处于兼容性考虑不推荐使用此命令升级所有包）

卸载包：

* ``pear uninstall [options] package_name``

### 12章 日期和时间

* ``checkdate(int month, int dat, int year)`` 查看日期是否合法
* ``date(string format[, int timestamp])`` 返回格式化的当前时间（或某个时间）
* ``time()`` 返回当前时间戳
* ``mktime([hour, minute, second, month, day, year])`` 由参数创建时间戳
* ``gettimeofday([boolean return_float])`` 返回关联数组（dsttime, minuteswest, sec, usec）
* ``getdate(int timestamp)`` 将时间戳解析成一个关联数组
* ``setlocale(category, string locale)`` 设置本地化
* ``strftime(string format[, int timestamp])`` 获取本地化时间
* ``strtotime(string time)`` 将格式化的时间字符串转换为timestamp，如``strtotime("+45 days");``

``number_format()`` 给number加上","千位分割，也可对小数点后位数加以限制

使用日期时间类：``DateTime``

```php
// 获取指定时间
new DateTime("2013-2-12 14:22:12");
// 获取当前时间
$date = new DateTime()
```

* ``$date->format()``
* ``$date->setDate(int year, int month, int day)``
* ``$date->setTime(int hour, int minutes[, int seconds])``
* ``$date->modify("+27 hours")``

### 13章 处理表单

``$_POST``中可以取到post字段

将表单的action设置为``action="<?php echo $_SERVER['PHP_SELF']; ?>"``；其中``$_SERVER['PHP_SELF']``可以获取当前的php文件

防范注入的四个函数：

* ``escapdeshellarg()`` 当传入一个用于shell命令exec()或者system()；做为执行参数的字符串，通过转义单引号作为单个参数，防止传入危险的额外shell命令如``'rm -rf *'``等;
* ``escapeshellcmd()`` 转义shell元字符
* ``htmlentites()``
* ``strip_tags()``

PHP5.2.0起还可以使用内置的Filter扩展

* ``filter_var(str, FILTER_VALIDATE_EMAIL)`` 除了EMAIL还有BOOLEAN, FLOAT, INT, IP, REGEXP, URL；另外还有第三个参数，如``filter_var($str, FILTER_VALIDATE_IP, FILTER_FLAG_IPV4);``

还可以使用filter清理输入数据：

* ``filter_var($input, FILTER_SANITIZE_STRING)`` 除了STRING，还有其他的(9种)，参见PHP文档

**多值控件，如select multiple="multiple"的情况，name设为"xxx[]"即可（注意要加中括号[]），在``$_POST``里获取到的xxx是数组。**

pear包：**HTML_QuickForm2**

因为依赖HTML_Common2包，所以安装时要加上自动安装依赖选项：（会自动安装依赖的包）

```php
pear install --onlyreqdeps HTML_QuickForm2
```

### 14章 身份验证

``header(string header[, boolean replace, int http_reponse_code])`` 发送首部；header必须放在返回任何输出之前执行

用``isset``检查``$_SERVER['PHP_AUTH_USER']``和``$_SERVER['PHP_AUTH_PW']``

如果没有以上两个值，可以要求用户输入，通过发送特定头和**状态码401**

```php
header('WWW-Authenticate: Basic Ream="Secret Stash"');
header('HTTP/1.0 401 Unauthorized');
```

使用**Auth_HTTP**通过数据库认证用户名密码

设置一次性url：``uniqid(rand(), 1)`` 或者短一点的``uniqid(rand())``;

使用``md5()``做密码验证

### 15章 文件上传

表单编码``enctype="multipart/form-data"``

php.ini里一些关于文件上传的配置

``$_FILES``超级全局变量：

* ``$_FILES['fileName']['error']``
* ``$_FILES['fileName']['name']``
* ``$_FILES['fileName']['size']``
* ``$_FILES['fileName']['tmp_name']`` 这是存储在``upload_temp_dir``指定的文件夹中的临时文件名
* ``$_FILES['fileName']['type']`` 指定的MIME类型，如image/png或appication/pdf

PHP处理文件上传的函数：

``boolean is_uploaded_file(string filename)`` 指定的文件是否已用POST上传；配合``copy()``使用，先检测，再把文件从临时文件夹复制到指定目录

``boolean move_uploaded_file(string filename, string destination)``

PHP获取当前文件路径名称：``__FILE__``

获取当前路径：``__DIR__``

**注**： ``__FILE__`` ``__DIR__`` 都是5.3之后才有的

获取当前的路径方法：

* ``__DIR__``
* ``getcwd()``
* ``dirname(__FILE__)``

上传错误消息：

``UPLOAD_ERROR_...``

使用**HTTP_Upload**包

### 16章 网络

DNS、服务器和服务：

#### 1. DNS：

* ``checkdnsrr(domain, type)`` 查看域名是否存在DNS记录，``type``可以是：A, MX, NS, SOA, PTR, CNAME, AAAA, A6, SRV, NAPTR, TXT or ANY.
* ``dns_get_record(hostname[, type, array &authns, array &addtl])`` 获取``hostname``对应所有dns记录；``type``可以是：DNS_A, DNS_CNAME, DNS_HINFO,DNS_MX, DNS_NS, DNS_PTR, DNS_SOA, DNS_TXT, DNS_AAAA, DNS_SRV, DNS_NAPTR, DNS_A6, DNS_ALL or DNS_ANY.
* ``getmxrr(hostname, array &mxhosts[, array &weight])`` 主机名对应的mx记录将返回到``mxhosts``数组中。

#### 2. 服务：

* ``int getservbyname(string service, string protocol)`` 其中``protocol``可指定是"tcp"还是"udp"
* ``string getservbyport(int port, string protocol)``

例子：创建端口扫描器

#### 3. 建立网络套接字：

* ``fsockopen(hostname[, port, ...])`` 打开一个套接字，返回的是handle（file pointer）
* ``fputs(handle, string)`` 即``fwrite()``
* ``feof`` 和之前读文件的``feof``一样
* ``fclose(handle)`` 可以用来关闭打开的socket（file pointer）

#### 4. 通过PHP发送电子邮件：

配置指令：

* ``SMTP=localhost`` 设置windows下PHP邮件函数的MTA（邮件传输代理）
* ``smtp_port=25``
* ``sendmail_from`` 设置消息首部的from字段
* ``sendmail_path``
* ``mail.force_extra_parameters``

使用PHP脚本发送电子邮件：

``boolean mail(to, subject, message[, addl_headers, addl_params])`` 如：
```php
mail("test@ex.com", "This is a subject", "This is the mai body", "From:admin@ex.com\r\n");
```

使用pear的**Mail**包和**Mail_Mime**包

#### 5. 常见网络任务：

连接服务器：
``system('ping ...')``

扫描网络端口、测试网络速度、计算子网地址等。

### 17章 PHP和LDAP

...

### 18章 Session会话

#### 1. 配置指令：

* ``session.save_handler = files|mm（共享内存）|sqlite|user（用户定义函数）`` 默认files
* ``session.auto_start = 0|1`` 设置为1后可不用手动调用``session_start()``开启会话
* ``session.name = string`` 默认为PHPSESSID
* ``session.use_cookie = 0|1`` 启用后就不需要手动调用cookie设置函数``set_cookie()``

如果设置了``use_cookie``，即用cookie来管理SID，则还需要配置下面参数：

设置为0时，sid必须放在url后面，设置``session.use_trans_sid``配置自动完成此项工作

* ``session.cookie_lifetime = interger`` 设置会话cookie生命周期
* ``session.cookie_path = string`` 设置会话cookie有效路径，如/books
* ``session.cookie_domain = string``

* ``session.referer_check = string``
* ``session.cache_limiter = string``
* ``session.cache_expire = integer``
* ``session.gc_maxlifetime = integer`` 会话信息的销毁时间

#### 2. 处理会话：

* ``session_start()`` 创建新会话或继续当前会话，返回的是boolean，不是sid；应在任何浏览器输出之前调用，否则错误“headers already sent.”；配置auto_start的副作用：每个php页面都会加载
* ``session_unset()`` 清空session中的变量
* ``session_destroy()`` 销毁session

* ``session_id([sid])`` 获取或设置sid
* ``session_regenerate_id([booean delete_old_session])`` 每次页面刷新都将重新生成新的sid，但是session内容不变

#### 3. 定制会话处理程序

将``session.save_handler=user``就可以自己定制session处理函数的行为了

* ``session_open($session_save_path, $session_name)`` 两个参数的值是在php.ini中对应的配置；也可以使用``get_cfg_var()``来获取php.ini中的配置项
* ``session_close()`` close不销毁会话，只释放open打开的资源，销毁会话由destroy来完成
* ``session_read($sessionID)``
* ``session_write($sessionID, $value)``
* ``session_destroy($sessionID)`` 可能是脚本中调用最后一个函数，销毁会话和所有相关变量
* ``session_garbage_collect($lifetime)`` 删除到期的会话，``$lifetime``是在php.ini中配置的``session.gc_maxlifetime``配置项的值

将上面的自定义函数加入到PHP会话处理逻辑中：

使用``session_set_save_handler("session_open", "session_close", "session_read", "session_write", "session_destroy", "session_garbage_collect")`` 注意参数传入顺序：打开、关闭、读取、写入、销毁、垃圾回收。

一个MySQLSessionHandler的例子（gist）[使用mysql处理session](https://gist.github.com/MrRaindrop/facf5ee0819d5dbf3a04)，代码如下：

{% gist MrRaindrop/facf5ee0819d5dbf3a04 %}

### 19章 用Smarty模板化

定界符： ``{ }``

显示变量：``{ $xx }``





