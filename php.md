## SOAP
SOAP 是基于 XML 的简易协议，可使应用程序在 HTTP 之上进行信息交换。

或者更简单地说：SOAP 是用于访问网络服务的协议。

## PHP的api安全防护

## lvs和NGINX的区别
lvs好像是直接转发吧，不做任何处理

NGINX是反向代理，可以负载均衡。

## 网关了解过么？
1. 就是负载均衡，流量控制，权限控制吧

## 自己擅长什么技术。
1. 我基础扎实
2. 代码规范意识不错。

##  以下会输出什么？
```
<?php
$a = [];
$a[2] = 'baidu';
$a[1] = 'hello';
$a[3] = 'world';

var_dump($a);

array(3) {
  [2]=>
  string(5) "baidu"
  [1]=>
  string(5) "hello"
  [3]=>
  string(5) "world"
}
```


##  以下会输出什么？
```
<?php
$a = array(1,2,3,4);
foreach($a as &$v){
     $v *= $v;
} 
foreach($a as $v){
     echo $v; // 1,4,9,9
}

```

## MySQL 注入
1. 使用PDO
2. 使用htmlspecialchars 对字符串转义

## csrf 
1. 使用http_only
2. token, token有过期的一个缺点

## xss
1. 对输入进行转义
2. 对输出进行过滤


## 针对直播这种突然大流量的情况，该怎么设计。
1. NGINX加机器
2. cdn缓存静态页面
3. redis队列，让用户慢点进来。
4. 加缓存。缓存用户数据，比如用户信息。
5. 数据库使用主从
6. 弹性扩容
7. 限流熔断

1. 解决缓存穿透问题，只有主播请求的时候，才将数据缓存到内存里面，避免大量用户来直接请求数据库。
2. redis 使用集群，防止redis不可用。和哨兵

## 分布式事务
分段提交

## 如何设计一个大的网站
1. 减少http请求（比如使用雪碧图）
1. 优化数据库（范式、SQL语句、索引、配置、读写分离）
1. 缓存使用（Memcache、Redis）
1. 负载均衡
1. 动态内容静态化+CDN
1. 禁止外部盗链（refer、图片添加水印）
1. 控制大文件下载
1. 使用集群

## 听说过yaf么？



## 流程我们也接入了CICD啊啊。。会有代码检查啊。

## 分析一个问题:php-fpm 的日志正常，但客户端却超时了，你认为可能是哪里出了问题，怎么排查？
检查 nginx log，请求是否达到 nginx 和是否正常转发给 php-fpm

## 无限极分类
递归和引用

## 根据 access.log 文件统计最近 5 秒的 qps，并以如下格式显示，01 1000（难点在 01 序号）
tail -f access.log | awk -F '[' '{print $2}' | awk '{print $1}' | uniq -c


##  文件，用 php 查看它的行数
粗暴一点的方法 ini_set ('memory_limit','-1'); 先把当前内存限制解除了 然后直接逐行统计。时间会非常的久。
有更好的方法请留言。
##  10 亿条订单数据，属于 1000 个司机的，请取出订单量前 20 的司机（TIPS）不要中招。不要用常用思路来处理，10 亿数据 你再怎么优化，全表求和，都是要死人的。
我们从设计上解决这个问题。只有一千个司机。我们可以做个简单哈希，分库分表，% 求余数。保证这一千个司机分在一千个表里，每个人有每个人的单独表。引擎用 MYSAIM，求表中数据的总数，效率飞快，遍历一千张表，求最大前二十即可。

## PHP超时
xphrof

脚本执行时间
## php预处理函数
1. htmlspecialchars_decode()	把一些预定义的 HTML 实体转换为字符。
1. htmlspecialchars()	把一些预定义的字符转换为 HTML 实体。
1. strip_tags()  剥去字符串中的 HTML 和 PHP 标签。

## 设计一个秒杀系统#
思路：用redis的队列

```
$ttl = 4;
$random = mt_rand(1,1000).'-'.gettimeofday(true).'-'.mt_rand(1,1000);

$lock = fasle;
while (!$lock) {
    $lock = $redis->set('lock', $random, array('nx', 'ex' => $ttl));
}

if ($redis->get('goods.num') <= 0) {
    echo ("秒杀已经结束");
    //删除锁
    if ($redis->get('lock') == $random) {
        $redis->del('lock');
    }
    return false;
}

$redis->decr('goods.num');
echo ("秒杀成功");
//删除锁
if ($redis->get('lock') == $random) {
    $redis->del('lock');
}
return true;
```


## include与require没有本质上的区别，
唯一的不同在于错误级别，当文件无法被正常加载时include会抛出warning警告，而require则会抛出error错误，

require() 语句的性能与 include() 相类似，都是包括并运行指定文件。不同之处在于：对 include() 语句来说，在执行文件时每次都要进行读取和评估；而对于 require() 来说，文件只处理一次（实际上，文件内容替换 require() 语句）。这就意味着如果可能执行多次的代码，则使用 require() 效率比较高

## 内存泄漏
因为，apache这种是伴随着操作系统 长时间来运行的，如果内存不回收的话，可能会导致操作系统不能主动的回收内存，导致严重的内存泄漏错误。
频繁的内核态和用户态的切换会导致性能问题。

## PHP的垃圾回收
首先由一个引用计数的概念，当计数为0 的时候就会收回。
如果是循环引用的话，当本身被回收之后，引用计数为1，还是大于0的，这个时候，会变成垃圾，可能会导致内存泄漏。这个时候需要垃圾回收期，弄到缓冲区，然后进行下一步的处理。

缓冲区，到了**10000**个以后，会进行处理。这种垃圾只会出现在数组和对象的情况下， 因为自己引用自己。所以垃圾处理，只会处理这两种类型的垃圾。
会循环遍历缓冲区，然后依次减一，因为基本减一后，就为0了，表明都是自己引用本身，别人不会用到它，可以被删除。

https://learnku.com/articles/33451

(1) 从缓冲区链表的 roots 开始遍历，把当前 value 标为灰色 (zend_refcounted_h.gc_info 置为 GC_GREY)，然后对当前 value 的成员进行深度优先遍历，把成员 value 的 refcount 减 1，并且也标为灰色；

(2) 重复遍历缓冲区链表，检查当前 value 引用是否为 0，为 0 则表示确实是垃圾，把它标为白色 (GC_WHITE)，如果不为 0 则排除了引用全部来自自身成员的可能，表示还有外部的引用，并不是垃圾，这时候因为步骤 (1) 对成员进行了 refcount 减 1 操作，需要再还原回去，对所有成员进行深度遍历，把成员 refcount 加 1，同时标为黑色；

(3) 再次遍历缓冲区链表，将非 GC_WHITE 的节点从 roots 链表中移出，最终 roots 链表中全部为真正的垃圾，最后将这些垃圾清除。【这个时候，剩下的就是array和object这种类型的垃圾了】

https://github.com/pangudashu/php7-internal/blob/master/5/gc.md

如果一个value的refcount为0的话，可以释放掉，不属于垃圾。
如果value的recount大于0的话，不能释放，属于垃圾。
只有IS_TYPE_COLLECTABLE 才会被回收。
只会被插入一次，不会被重复插入，会被标记为GC_PURPLE

## // 缓存的更新模式


## 浏览器访问页面源码如下，后端执行 php reload,页面会出现什么情况

```
<?php
sleep(10);
echo '123123';
```


会发生502，暂时不可用。

## 以下会输出什么？

```
<?php
$a = [];
$a[2] = 'baidu';
$a[1] = 'hello';
$a[3] = 'world';

var_dump($a);

array(3) {
  [2]=>
  string(5) "baidu"
  [1]=>
  string(5) "hello"
  [3]=>
  string(5) "world"
}
```


## 以下会输出什么？

```
<?php
$a = array(1,2,3,4);
foreach($a as &$v){
     $v *= $v;
} 
foreach($a as $v){
     echo $v; // 1,4,9,9
}
```


## copy on write 

```
//定义一个变量 
$a= range(8,1988) 
//定义变量b,将a变量的值赋值给b // COW机制 Copy On Write 
$b=$a; 
//此时, $b和$a指向同一个内存地址,只有当$a改变时,$b才指向新的地址 //对a进行修改 
$a= range(8,1999)
```


## php7 的新特性
1. 使用连续大块内存
2. 优化底层数据结构
3. 减少内存分配次数
## 写出10个常用的array函数和string函数
10个数组

```
array_push 
array_pop
array_shift
array_unshift
count
sort
array_map
array_filter
array_diff
array_walk
array_column
array_key_exists()
array_count_values()

```
10个字符串

```
str_replace
substr
strstr
trim
ltrim
rtrim
explode
implode
md5
strlen
ucwords
lcwords
ucfirst
lcfirst
strtolower
strtoupper
substr_count 计数。
die
md5
strrev
htmlspecialchars
htmlspecialchars()	把一些预定义的字符转换为 HTML 实体。
```
## php和NGINX的通信
websrever  在启动时，载入fastcgi管理器。

fastcgi会完成初始化，启动多个cgi解释器。并等待客户端的连接
当客户端连接的时候，fasycgi管理器会连接其中的一个cgi解释器。webserver 将标准输入和cgi配置发送给cgi。

fsatcgi将标准输出和错误输出返回给web server。当fastcgi关闭时，意味着本次请求完成。然后fastcgi等待着fastcgi管理器给他提供的下一次请求。

php请求过来的时候，NGINX会将请求发送给fastcgi的master，发送给worker。将编译后的结果发送个NGINX，然后返回给客户端。
## PHP执行过程
1. Scanning(Lexing) ,将PHP代码转换为语言片段(Tokens)
2. Parsing, 将Tokens转换成简单而有意义的表达式
3. Compilation, 将表达式编译成Opocdes
4. Execution, 顺次执行Opcodes，每次一条，从而实现PHP脚本的功能。

## 如何防止内存被打满。
set_int('memery_limit', '-1');
## PHP的四种工作模式
1. cgi 通用网关接口（Common Gateway Interface）
1. fast-cgi 常驻（long-live）型的 CGI
1. cli  命令行运行   （Command Line Interface）
1. mod_php模式 （apache等web服务器运行的模块模式）
## for循环
每次从$i开始，每次循环都需要判断$i是否小于count，这占用了很大一部分时间
小于继续，否则终止循环
foreach遍历

foreach 依赖 IEnumerable.
第一次 var a in GetList() 时 调用 GetEnumerator 返回第一个对象 并 赋给a,
以后每次再执行 var a in GetList() 的时候 
调用 MoveNext.直到循环结束.
期间GetList()方法只执行一次

通过这段源码可以看出来，如果是foreach的话，可以直接通过_Bucket里的next获取到下一个值，而如果是for循环，$array['key']这样子获取数据，就会需要做一次hash才会知道bucket的位置，所以foreach比for循环效率更高一些。
## php7新特性
1. 标量类型声明
2.返回值类型声明 
3. null合并运算符 
4。 太空船操作符（组合比较符） 

```
// 字符串
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
通过 define() 定义常量数组 
<?php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // 输出 "cat"
?>
```


5.匿名类 
现在支持通过new class 来实例化一个匿名类，这可以用来替代一些“用后即焚”的完整类定义。

```
<?php
interface Logger {
    public function log(string $msg);
}

class Application {
    private $logger;

    public function getLogger(): Logger {
         return $this->logger;
    }

    public function setLogger(Logger $logger) {
         $this->logger = $logger;
    }
}

$app = new Application;
$app->setLogger(new class implements Logger {
    public function log(string $msg) {
        echo $msg;
    }
});

var_dump($app->getLogger());
?>
以上例程会输出：

object(class@anonymous)#2 (0) {
}
```


new class实例化
Closure::call() ¶
Closure::call() 现在有着更好的性能，简短干练的暂时绑定一个方法到对象上闭包并调用它。

```
<?php
class A {private $x = 1;}

// PHP 7 之前版本的代码
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, 'A'); // 中间层闭包
echo $getX();

// PHP 7+ 及更高版本的代码
$getX = function() {return $this->x;};
echo $getX->call(new A);
以上例程会输出：
1
1
```


## 字符串”\r”,”\n”,”\t”,”\x20”分别代表什么
答案： “\r”代表的含义是：
在Linux、unix 中表示返回到当行的最开始位置，在Mac OS 中表示换行且返回到下一行的最开始位置，相当于Windows 里的 \n 的效果。
“\n”代表的含义是：
在Windows 中表示换行且回到下一行的最开始位置。相当于Mac OS 里的 \r 的效果，在Linux、unix 中只表示换行，但不会回到下一行的开始位置。
“\t”所代表的含义是：
键盘上的“TAB”键，跳格（移至下一列）。
“\x20”所代表的含义是：是32在ASCII表中16进制的表示。
## 以下语句输出的结果是什么
$a = 3;
echo "$a",'$a',"\\\$a","${a}","$a"."$a","$a"+"$a";
 得到的结果是：
3$a\$a3336

var_dump(empty(array(array())));

##  以下语句输出的结果是什么
setcookie("a","value");
print $_COOKIE['a'];

得到的结果是：
value(若只是这两段编码运行，则会提示PHP Notice: Undefined index: a)
##  php中将当前页面重定向到另一个页面怎么写？
header();
## .什么是魔术引号(magic_quotes_gpc)?
魔术引号（Magic Quotes）是一个自动将进入 PHP 脚本的数据进行转义的过程。提示：最好在编码时不要转义而在运行时根据需要而转义。
##  在类的方法中，如何调用其父类的同名方法？
parent::方法名
##  如何取得客户端的ip(要求取得一个int)
$_SERVER["REMOTE_ADDR"];
ip2long进行转换
##  include和require的区别
require:出现错误后直接终止退出，程序不再执行，读取后会成为文件的一部分。
include:包含一个不存在的文件，会提示警告程序会继续执行。每次都会读取
##  extends的作用是什么
类的继承
##  php中如何取得get，post参数，和上传的文件
$_GET,
$_POST,
$_FILES
##  @test()和&test()的区别
@test()的作用是屏蔽test()方法中警告的作用
&test()引用test()方法
##  array+array与array_merge()的区别
二者之间的区别是：
1 键名为数字时，array_merge()不会覆盖掉原来的值，但＋合并数组则会把最先出现的值作为最终结果返回，而把后面的数组拥有相同键名的那些值“抛弃”掉（不是覆盖）

2 键名为字符时，＋仍然把最先出现的值作为最终结果返回，而把后面的数组拥有相同键名的那些值“抛弃”掉，但array_merge()此时会覆盖掉前面相同键名的值
##  请列举最少3个php对象的魔术方法和说明它们的用途
- __construct() :实例化对象时被调用；
- __destruct()：当删除一个对象或者对象操作终止是被执行；
- __call()：调用对象不存在方法时被调用；
- __get()：调用对象不存在的属性时被调用；
- __set()：设置对象不存在的属性时被调用；
- __toString()：打印一个对象时被调用，比如echo $obj,print($obj);
- __clone():克隆对象时被调用，比如$t = new Test();$tt = clone $t;
- __sleep():serialize之前被调用，若对象比较大，想做一些删除在序列化，可以考虑使用该方法；
- __wakeup()：unserialize之前被调用，做些对象的初始化；
- __isset()：检测对象是否存在属性的时候被调用，如 isset($c->name)；
- __unset():unset一个对象属性时被调用，如：unset($c->name);
- __set_state()：调用var_export时被调用，用__set_state的返回值作为 var_export的返回值；
- __autoload()：实例化一个对象时，如果对应的类不存在，在该方法被调用。
##  echo intval(0.58*100) 输出的结果是57，试分析这是为什么？
原因就是浮点数精度的问题。
简单的十进制分数如同 0.1 或 0.7 不能在不丢失一点点精度的情况下转换为内部二进制的格式。这就会造成混乱的结果：例如，floor((0.1+0.7)*10) 通常会返回 7 而不是预期中的 8，因为该结果内部的表示其实是类似 7.9999999999…。这和一个事实有关，那就是不可能精确的用有限位数表达某些十进制分数。例如，十进制的 1/3 变成了 0.3333333…。所以永远不要相信浮点数结果精确到了最后一位，也永远不要比较两个浮点数是否相等。如果确实需要更高的精度，应该使用任意精度数学函数或者 gmp 函数。
##  内存回收机制
1. 一个zval变量容器，除了包含变量的类型和值，还包括两个字节的额外信息。第一个是"is_ref"，是个bool值，用来标识这个变量是否是属于引用集合(reference set)。通过这个字节，php引擎才能把普通变量和引用变量区分开来
zval变量容器中还有一个内部引用计数机制，来优化内存使用。第二个额外字节是"refcount"，用以表示指向这个zval变量容器的变量(也称符号即symbol)个数。所有的符号存在一个符号表中，其中每个符号都有作用域(scope)，那些主脚本(比如：通过浏览器请求的的脚本)和每个函数或者方法也都有作用域。 
$c=$b=$a; refcount就是等于3.unset之后  次数就减1.  为0时，就进行回收。 为0时，这个类型和容器就从内存中删除。
##  回收周期
首先，我们先要建立一些基本规则，如果一个引用计数增加，它将继续被使用，当然就不再在垃圾中。如果引用计数减少到零，所在变量容器将被清除(free)。就是说，仅仅在引用计数减少到非零值时，才会产生垃圾周期(garbage cycle)。

其次，在一个垃圾周期中，通过检查引用计数是否减1，并且检查哪些变量容器的引用次数是零，来发现哪部分是垃圾。（通过检查是否为0，其实就是开启了回收周期）
## .php中web上传文件的原理是什么，如何限制上传文件的大小？
上传文件的表单使用 post 方式，并且要在 form 中添加 enctype='multipart/form-data'。
pHp上传文件默认大小为2M，设置上传大小的配置项是upload_max_filesize, post_max_size设置一次pOST中pHp能接收的最大数据量，应该比upload_max_filesize大。

**client_max_body_size** 10M  这个是配置NGINX的的配置。

##  列举出PHP中一些正则函数（至少2个）
- preg_match(),  执行一个正则表达式匹配
- preg_match_all(), 执行一个全局正则表达式匹配
- preg_quote()  转义正则表达式字符
- preg_replace(),  执行并替换
- preg_split(),  	通过一个正则表达式分隔字符串
- preg_replace_callback()
##  UTC是什么？
每个地区都有自己的本地时间，在网上以及无线电通信中，时间的转换问题就显得格外突出。整个地球分为二十四个时区，每个时区都有自己的本地时间。在国际无线电或网络通信场合，为了统一起见，使用一个统一的时间，称为通用协调时（UTC，Universal Time Coordinated），是由世界时间标准设定的全球标准时间。
## .解释下php://input 叫什么？
答：php输入流
## .请说明在php.ini中safe_mode开启之后对于php系统函数的影响（很多系统函数就不能用了）
答：safe_mode是提供一个基本安全的共享环境。在一个多用户共享的phpweb服务器上，当这台服务器开启了safe_mode模式，有以下函数将会受到影响。首先，一下尝试访问文件系统的函数将会被限制，运行服务器的用户id，如果想要尝试操作某个文件，必须要用户该文件的读取或者写入的访问权限。
因此，在safe_mode打开的情况下，下列函数将会收到限制：

```
ckdir，move_uploaded_file,
chgrp,
parse_ini_file,
chown
,rmdir
,copy
,rename,
fopen,
require,
highlight_file,
show_source,
include,
symlink,
link,
touch,
mkdir
,unlink
```


以上都是跟操作文件系统有关的函数，除此之外，一些php扩展的函数也会受到限制，不能在程序里面直接加载扩展，只能到php.ini里加载，而且php如果需要执行操作系统的程序时，必须在safe_mode_exec_dir中指定程序的路径，否则执行将失败。此外还有exec,
shell_exec,
pasathru,
system
,popen等函数会收到限制

## .魔术常量
- __LINE__：返回当前行号；
- __FILE__：返回文件的完整路径和文件名，如果用在包含文件里面，则返回包含文件名，自 php4.0.2开始，__FILE__总是包含一个绝对路径，而在此前的版本有时候会包含一个相对路径
- __FUNCTION__：返回函数名称（自 php4.3.0新加的）。自php5起本常量返回该函数被定义时的名称，区分大小写，在php4中该值总是小写；
- __CLASS__：返回类的名称，自 php4.3.0新加的，自php5起本常量返回该类被定义时的名称，区分大小写，在php4中该值总是小写的；
- __METHOD__：返回类的方法名。 php5新加的
##  你用什么方法检查php脚本的执行效率（通常是脚本执行时间）和数据库SQL的效率（通常是数据库Query时间），并定位和分析脚本执行和数据库查询的瓶颈所在？
1. php脚本的执行效率
1. 代码脚本里计时。
1. xdebug统计函数执行次数和具体时间进行分析。,最好使用工具winCacheGrind分析
1. 在线系统用strace跟踪相关进程的具体系统调用。
1. 数据库SQL的效率
1. sql的explain(mysql),启用slow query log记录慢查询。
1. 通常还要看数据库设计是否合理，需求是否合理等。
## .请写一段php代码，确保多个进程同时写入同一个文件成功 （这个很难，可以不考）

```
function writeData($path, $mode, $data){
    $fp = fopen($path, $mode);
    $retries = 0;
    $max_retries = 100;
    do {
        if ($retries > 0) {
        usleep(rand(1, 10000));
    }
    $retries += 1;
    }while (!flock($fp, LOCK_EX) and $retries <= $max_retries);

    if ($retries == $max_retries) {
        return false;
    }

    fwrite($fp, "$data\\n");
    flock($fp, LOCK_UN);
    fclose($fp);
    return true;
}
```


## .如何在命令行下运行php脚本（写出两种方式）同时向php脚本传递参数？
• window下，假设php安装目录为c:\\program files\\php5\\，那么使用命令窗口进入到该路径下，敲入php hello.php回车，则会执行当前路径下的hello.php文件，

• 如果要指向其他路径下php文件，可以在php 路径/hello.php ，这种形式称为CLI模式，我们平时通过浏览器看到的那种成为CGI模式，至于传递参数，php文件在cli模式下，直接通过在文件名称后面接参数，多个参数中间用空格隔开，在php文件里面是通过两个变量来获取参数的，一个是$argv，一个是$argc，前者是传递参数的数组，默认第一个为php文件的名称；后者为$argv的数组个数。

• linux下，一般程序安装都会安装在/usr/bin/php下面，可以通过man php查看一下，如果有信息说明可以使用，使用方法类似于window下。如果前面这步成立，那么你可以直接 php php文件 来运行php文件，如果man php没有信息，则说明当前php执行文件没有在环境路径里面，可以修改环境路径包含php路径，也可以类似于window进入php路径，在执行 php php文件。其他类似于window下。
##  使对象可以像数组一样进行foreach循环，要求属性必须是私有
• php5里面已经有了iterator接口，只要实现该接口，即可以实现对象私有属性被foreach遍历
<?
class Sample implements iterator{
    private $var = array(1,2,3,4,5);
    public function __construct(){}
    public function rewind(){ reset($this->var);}
    public function current(){return current($this->var);}
    public function key(){return key($this->var);}
    public function next(){return next($this->var);}
    public function valid(){return ($this->current()!==false);}
}
$s = new Sample();
foreach($s as $k=>$v){ echo $k.\=\.$v.\<br/>\;}
?>

##  用PHP构建一个链表

```
class ListNode {
    public $val = 0;
    public $next = null;
    function __construct($val) { 
        $this->val = $val; 
     }
}
```



##  php保存序列化对象到session中，并可以从session中获取序列化对象的值，用什么序列化函数？

• serialize()函数序列化对象; unserialize()函数还原序列化对象。
## .以下php程序的结果

```
8%-3=?; =>2
$a="hello";
$b=&$a;
unset($b);
$b="world";
$a=? ; =>"hello"
```
## .JWT
用户B 你好，用户A，我想要操作jwt内容，这是我的凭证。
## .php中的文件读写操作，读取文件test.txt中前300字节的内容？
```
$handle=fopen("test.txt","r");
$contents=fread($handle,300);
fclose($handle);
```
## .PHP获取客户端和服务器端IP客户端IP相关的变量
$_SERVER['REMOTE_ADDR'] 客户端IP，有可能是用户的IP，也可能是代理的IP。(这个就是获取客户端的ip)

$_SERVER['HTTP_CLIENT_IP'] 代理端的IP，可能存在可伪造。

 $_SERVER['HTTP_X_FORWARDER_FOR'] 用户是在哪个IP使用的代理，可能存在，可以伪造。（这个应该是客户端的真是IP）

 $_SERVER['SERVER_ADDR'] 获取服务器端IP（获取服务器端的IP）

## .PHP中对象的深拷贝与浅拷贝
先说一下深拷贝和浅拷贝通俗理解

深拷贝：赋值时值完全复制，完全的copy，对其中一个作出改变，不会影响另一个

浅拷贝：赋值时，引用赋值，相当于取了一个别名。对其中一个修改，会影响另一个

PHP中， = 赋值时，普通对象是深拷贝，但对对象来说，是浅拷贝。也就是说，对象的赋值是引用赋值。（对象作为参数传递时，也是引用传递，无论函数定义时参数前面是否有&符号）

https://yq.aliyun.com/articles/514030

## PHP7比PHP5性能提升的原因？
1、存储变量的结构体变小，尽量使结构体里成员共用内存空间，减少引用，这样内存占用降低，变量的操作速度得到提升。

2、字符串结构体的改变，字符串信息和数据本身原来是分成两个独立内存块存放，php7尽量将它们存入同一块内存，提升了cpu缓存命中率。(zend_string)

3、数组结构的改变，数组元素和hash映射表在php5中会存入多个内存块，php7尽量将它们分配在同一块内存里，降低了内存占用、提升了cpu缓存命中率。

4、 **改进了函数的调用机制** ，通过对参数传递环节的优化，减少一些指令操作，提高了执行效率。

## null 在 == 相等的值。
'' "" 0 '0' array() false 

## PHP的数据，key的 数字也好，string也好，是如何实现的。
存放记录的数组称做散列表，这个数组用来存储value，而value具体在数组中的存储位置由映射函数根据key计算确定，映射函数可以采用取模的方式，key可以通过一些譬如**“times 33”**的算法得到一个整形值，然后与数组总大小**取模**得到在散列表中的存储位置。这是一个普通散列表的实现，PHP散列表的实现整体也是这个思路，只是有几个特殊的地方，下面就是PHP中HashTable的数据结构：
```
//Bucket：散列表中存储的元素
typedef struct _Bucket {
    zval              val; //存储的具体value，这里嵌入了一个zval，而不是一个指针
    zend_ulong        h;   //key根据times 33计算得到的哈希值，或者是数值索引编号
    zend_string      *key; //存储元素的key
} Bucket;

//HashTable结构
typedef struct _zend_array HashTable;
struct _zend_array {
    zend_refcounted_h gc;
    union {
        struct {
            ZEND_ENDIAN_LOHI_4(
                    zend_uchar    flags,
                    zend_uchar    nApplyCount,
                    zend_uchar    nIteratorsCount,
                    zend_uchar    reserve)
        } v;
        uint32_t flags;
    } u;
    uint32_t          nTableMask; //哈希值计算掩码，等于nTableSize的负值(nTableMask = -nTableSize)
    Bucket           *arData;     //存储元素数组，指向第一个Bucket
    uint32_t          nNumUsed;   //已用Bucket数
    uint32_t          nNumOfElements; //哈希表有效元素数
    uint32_t          nTableSize;     //哈希表总大小，为2的n次方
    uint32_t          nInternalPointer;
    zend_long         nNextFreeElement; //下一个可用的数值索引,如:arr[] = 1;arr["a"] = 2;arr[] = 3;  则nNextFreeElement = 2;
    dtor_func_t       pDestructor;
};
```
HashTable中有两个非常相近的值:nNumUsed、nNumOfElements，nNumOfElements表示哈希表已有元素数，那这个值不跟nNumUsed一样吗？为什么要定义两个呢？实际上它们有不同的含义，当将一个元素从哈希表删除时并不会将对应的Bucket移除，而是将Bucket存储的zval修改为IS_UNDEF，只有扩容时发现nNumOfElements与nNumUsed相差达到一定数量(这个数量是:ht->nNumUsed - ht->nNumOfElements > (ht->nNumOfElements >> 5))时才会将已删除的元素全部移除，重新构建哈希表。所以nNumUsed>=nNumOfElements。

HashTable中另外一个非常重要的值arData，这个值指向存储元素数组的第一个Bucket，插入元素时按顺序 依次插入 数组，比如第一个元素在arData[0]、第二个在arData[1]...arData[nNumUsed]。PHP数组的有序性正是通过arData保证的，这是第一个与普通散列表实现不同的地方。

既然arData并不是按key映射的散列表，那么映射函数是如何将key与arData中的value建立映射关系的呢？

实际上这个散列表也在arData中，比较特别的是散列表在ht->arData内存之前，分配内存时这个散列表与Bucket数组一起分配，arData向后移动到了Bucket数组的起始位置，并不是申请内存的起始位置，这样散列表可以由arData指针向前移动访问到，即arData[-1]、arData[-2]、arData[-3]......散列表的结构是uint32_t，它保存的是value在Bucket数组中的位置。

所以，整体来看HashTable主要依赖arData实现元素的存储、索引。插入一个元素时先将元素按先后顺序插入Bucket数组，位置是idx，再根据key的哈希值映射到散列表中的某个位置nIndex，将idx存入这个位置；查找时先在散列表中映射到nIndex，得到value在Bucket数组的位置idx，再从Bucket数组中取出元素。

## 数组通过key 查询比较快，还是通过value？
key。具体原因，待补充。

## php 的数据类型。
基本数据结构  bool string int float

两种特殊结构 array object

两种特殊类型 resource null

## 项目中常用的算法？
1. 我觉得我没有用过。
2. 递归可能用一些。

# 单点登录
1. 登录的时候，去验证中心获得一个唯一的ticket，然后保存在cookie里面。
2. 登录其他子网站的时候，需要获取本地的cookie然后去验证中心去验证一下。
3. 退出的时候，清除登录状态和本地唯一的ticket。

// 删除session  '$_SESSION = array();'；
empty($_SESSION) ? true : false;

# array_key_exists()和isset()

array_key_exists() 会检查键值的存在. 这个函数会返回TRUE，只要键值存在，即使值为NULL.
````
$arr = array( "one"=>"1", "two"=>"2", "three"=>null ); 
array_key_exists("one", $arr); // true 
array_key_exists("two", $arr); // true 
array_key_exists("three", $arr); // true
````
 isset()和arrry_key_exitst()不同，isset()会同时检查键和值，只有当健存在，对应的变量不为NUll的时候才会返回TURE。
````
$arr = array( "one"=>"1", "two"=>"2", "three"=>null );
isset($arr["one"]); // true 
isset($arr["two"]); // true 
isset($arr["three"]); // false
````
# 数组的底层实现
数组是PHP中非常强大、灵活的一种数据类型，它的底层实现为散列表(HashTable，也称作：哈希表)。
![](images/php/php.png)
https://github.com/huqinlou0123/php-internals-extended-development-course/blob/master/1-3-2:%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84-%E6%95%B0%E7%BB%84%E5%92%8C%E5%AD%97%E7%AC%A6%E4%B8%B2.md

https://github.com/pangudashu/php7-internal/blob/master/2/zend_ht.md

# 

