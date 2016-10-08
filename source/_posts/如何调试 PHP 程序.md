---
title: 如何调试 PHP 程序
date: 2016-05-02 15:54:00
tags: php-CodeIgniter
---
  
## 一.PHP 自带的调试功能

#### 1.修改 php.ini

###### （1）开发环境
需要打开报错输出显示，方便开发者调试。
```
display_errors = On
```
###### （2）生产环境
不能直接将错误输出，而是记入日志，以免透露路径、数据库连接、数据表等信息遭到黑客攻击。
```
display_errors = Off
log_errors = On
error_log = /path/
```
> | | 路径方式   |  是否 append  | 如果目录指向的文件不存在 |
> | --------   | :----:  | :----:  | :----:  |
> |error_log = php_errors.log|相对路径|√|自动创建|
> |error_log = E:/phpStudy/php53n/php_errors.log|绝对路径|√| 自动创建|

#### 2.利用 PHP file_put_contents() 函数
```
$a = 123;
file_put_contents("error.log", "a is : " . $a . PHP_EOL, FILE_APPEND);
```

> 1.如果目录指向的文件不存在怎么办？
>   会自动创建。
> 
> 2.PHP_EOL 是什么？
>   换行符。
>   unix 系列用 \n
>   windows 系列用 \r\n
>   mac 用 \r
>   PHP 中可以用 PHP_EOL 来替代，以提高代码的源代码级可移植性。
> 
> 3.Linux 下可能有读写权限，建议先放开权限
>   chmod 777 文件名
>   chmod -R 777 文件夹 （递归该文件夹下所有文件）


#### 3.利用 PHP file_put_contents() 函数

###### （1）少用错误控制运算符"@"

PHP 支持一个错误控制运算符：@ 。当将其放置在一个 PHP 表达式之前，该表达式可能产生的任何错误信息都被忽略掉。

如果一个缺陷发生在这个表达式中，从PHP的输出中看不到任何错误，这增加了调试的难度。所以能不用则不用。

> @ 运算符只对表达式有效。对新手来说一个简单的规则就是：如果能从某处得到值，就能在它前面加上 @ 运算符。
```
 $a = 123;
 echo @$b; //不报错
```
>  

###### （2）有些函数自带有 debug 功能

例如：

```
$fp = fsockopen("www.example.com", 80, $errno, $errstr, 30);
```
一般这样的函数（主要是网络通信类的），会自己提供调试参数：$errno 和 $errstr 。

你可以加一句：
```
if (!$fp) echo "$errstr ($errno)<br />\n";
```
就能看到连接失败的原因了。

这些函数有：fsockopen，pfsockopen，stream_socket_server，stream_socket_client 等。

还有些函数是调试一个功能用的，比如：mysql_errno，socket_last_error，socket_strerror 等。

## 二.引进调试工具
如：Xdebug 。
 
