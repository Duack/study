# Redis

## 概念：

​         NoSQL  ：not only sql .

​    **多看官网**

## Linux安装Redis

> #### 1.下载、解压、编译Redis

```bash
$ wget http://download.redis.io/releases/redis-6.0.6.tar.gz
mv 文件名
[root@ home]# cd /opt
[root@ opt]# tar -zxvf redis-6.0.6.tar.gz  #解压

```

> #### 2.基本环境配置(redis是c语言写的  需要一个环境去编译)

```bash
[root@ redis-6.0.6]# yum install gcc-c++ #安装redis基本环境 在redis目录下
[root@ redis-6.0.6]# make     #执行配置命令 自动配置所有要配置的
```

> make时报错：

```bash
make[1]: Entering directory '/opt/redis-6.0.6/src'
    CC Makefile.dep
    CC adlist.o
In file included from adlist.c:34:
zmalloc.h:50:10: fatal error: jemalloc/jemalloc.h: No such file or directory
 #include <jemalloc/jemalloc.h>
          ^~~~~~~~~~~~~~~~~~~~~
compilation terminated.
```

原因：原因是jemalloc重载了Linux下的ANSIC的malloc和free函数。

解决办法：make时添加参数。执行如下命令：

```bash
make MALLOC=libc
```

```bash
$  make test   
You need tcl 8.5 or newer in order to run the Redis test #错误信息
```

> #### 3.安装tcl

```bash
$ yum install tcl    
```

> 默认安装在 `usr/local/bin`



> #### 4.复制配置文件 用新配置文件启动 不改动源文件便于还原  （也可以使用于单机多redis）到usr/local/bin下面

```bash
[root@ bin]# mkdir wenjieconfig   #创建目录  /usr/local/bin/wenjieconfig
[root@ bin]# cp /opt/redis-6.0.6/redis.conf wenjieconfig  #复制配置文件到目录下
```

修改为yes 保证启动后可以持续访问 不关闭

![image-20200920122613667](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200920122613667.png)

> #### 5.修改上述文件 

```bash
[root@ bin]#vim /wenjieconfig/redis.conf     #进入文件编辑模式
#找到对应的修改处  按i编辑 编辑完之后 esc退出保存  再按 shift+":"   输入 wq 报存退出
```

> #### 6.通过指定配置文件 启动redis服务

```bash
[root@ bin]# redis-server wenjieconfig/redis.conf 

31331:C 20 Sep 2020 12:33:03.964 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
31331:C 20 Sep 2020 12:33:03.964 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=31331, just started
31331:C 20 Sep 2020 12:33:03.964 # Configuration loaded
```

> #### 7.连接客户端 

```bash
[root@ bin]# redis-cli [-p 6379]  #指定端口或者不指定6379
127.0.0.1:6379> 
```

> #### 8.查看进程

```bash
[root@ /]# ps -ef|grep redis   #通过管道过滤redis进程信息
```

> #### 9.退出redis

```bash
127.0.0.1:6379> SHUTDOWN save [NOSAVE|SAVE]  #是否保存 默认也行
not connected> exit   #进程会关闭
```



**总结：多配置文件可以使用于单机多redis**



## 性能测试

> ###  redis-benchmark



```bash
[root@ bin]# redis-benchmark -h localhost -p 6379 -c 100 -n 100000 #-h 主机 100并发 10万数据

```

![image-20200920130018619](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200920130018619.png)





## 基础知识

###   **redis有16个数据库，默认使用第0个**

可以使用  select 数字   切换

```bash
127.0.0.1:6379> select 4  #切换数据库
OK
127.0.0.1:6379[4]> dbsize  #查看大小
(integer) 0
```



### **查看所有的key **  keys * 

```bash
127.0.0.1:6379> keys *
1) "myhash:{tag}:__rand_int__"
2) "counter:{tag}:__rand_int__"
3) "key:{tag}:__rand_int__"
4) "mylist:{tag}"
```



### **清空数据库**

```bash
127.0.0.1:6379> flushdb  #清空当前数据库
OK
127.0.0.1:6379> FLUSHALL  #清空所有数据库
OK
```



### redis单线程

明白Redis是很快的,官方表示, Redis是基于内存操作, CPU不是Redis性能瓶颈, Redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现,就使用单线程了! 所有就使用了单线程了!



### Redis为什么单线程还这么快?

1、误区1 :高性能的服务器一 定是多线程的? 
2、误区2:多线程( CPU上下文会切换! ) -定比单线程效率高!
先去CPU>内存>硬盘的速度要有所了解!
核心: redis 是将所有的数据全部放在内存中的,所以说使用单线程去操作效率就是最高的,多线程( CPU上下文会切换:耗时的操作! ! ! ) , 对于内存系统来说,如果没有上下文切换效率就是最高的!多次读写都是在一个CPU 上的,在内存情况下,这个就是最佳的方案!





## 五种基本类型数据

### 关于key

```txt
Redis key值是二进制安全的，这意味着可以用任何二进制序列作为key值，从形如”foo”的简单字符串到一个JPEG文件的内容都可以。空字符串也是有效key值。
关于key的几条规则：
太长的键值不是个好主意，例如1024字节的键值就不是个好主意，不仅因为消耗内存，而且在数据中查找这类键值的计算成本很高。
太短的键值通常也不是好主意，如果你要用”u:1000:pwd”来代替”user:1000:password”，这没有什么问题，但后者更易阅读，并且由此增加的空间消耗相对于key object和value object本身来说很小。当然，没人阻止您一定要用更短的键值节省一丁点儿空间。
最好坚持一种模式。例如：”object-type:id:field”就是个不错的注意，像这样”user:1000:password”。我喜欢对多单词的字段名中加上一个点，就像这样：”comment:1234:reply.to”。
```





## String

> **时间复杂度：**O(1)。均摊时间复杂度是O(1)， 因为redis用的动态字符串的库在每次分配空间的时候会增加一倍的可用空闲空间，所以在添加的value较小而且已经存在的 value是任意大小的情况下，均摊时间复杂度是O(1) 。



####  命令

- ##### [APPEND](http://www.redis.cn/commands/append.html)    追加字符

- ##### [BITCOUNT](http://www.redis.cn/commands/bitcount.html)

- ##### [BITFIELD](http://www.redis.cn/commands/bitfield.html)

- ##### [BITOP](http://www.redis.cn/commands/bitop.html)

- ##### [BITPOS](http://www.redis.cn/commands/bitpos.html)

- ##### [DECR](http://www.redis.cn/commands/decr.html)

- ##### [DECRBY](http://www.redis.cn/commands/decrby.html)

- ##### [GET](http://www.redis.cn/commands/get.html)

- ##### [GETBIT](http://www.redis.cn/commands/getbit.html)

- ##### [GETRANGE](http://www.redis.cn/commands/getrange.html)

- ##### [GETSET](http://www.redis.cn/commands/getset.html)

- ##### [INCR](http://www.redis.cn/commands/incr.html)

- ##### [INCRBY](http://www.redis.cn/commands/incrby.html)

- ##### [INCRBYFLOAT](http://www.redis.cn/commands/incrbyfloat.html)

- ##### [MGET](http://www.redis.cn/commands/mget.html)

- ##### [MSET](http://www.redis.cn/commands/mset.html)

- ##### [MSETNX](http://www.redis.cn/commands/msetnx.html)

- ##### [PSETEX](http://www.redis.cn/commands/psetex.html)

- ##### [SET](http://www.redis.cn/commands/set.html)

- ##### [SETBIT](http://www.redis.cn/commands/setbit.html)

- ##### [SETEX](http://www.redis.cn/commands/setex.html)

- ##### [SETNX](http://www.redis.cn/commands/setnx.html)

- ##### [SETRANGE](http://www.redis.cn/commands/setrange.html)

- ##### [STRLEN](http://www.redis.cn/commands/strlen.html)



#### SET 

```bash
SET key value [EX seconds] [PX milliseconds] [NX|XX]  #将键key设定为指定的“字符串”值。如果 key 已经保存了一个值，那么这个操作会直接覆盖原来的值，并且忽略原始类型。当set命令执行成功之后，之前设置的过期时间都将失效。
EX seconds – 设置键key的过期时间，单位时秒
PX milliseconds – 设置键key的过期时间，单位时毫秒
NX – 只有键key不存在的时候才会设置key的值
XX – 只有键key存在的时候才会设置key的值
```



```
redis> SET mykey "Hello"
OK
redis> GET mykey
"Hello"
redis> 
```

##### 设计模式

**注意:** 下面这种设计模式并不推荐用来实现redis分布式锁。应该参考[the Redlock algorithm](http://redis.io/topics/distlock)的实现，因为这个方法只是复杂一点，但是却能保证更好的使用效果。

命令 `SET resource-name anystring NX EX max-lock-time` 是一种用 Redis 来实现锁机制的简单方法。

如果上述命令返回`OK`，那么客户端就可以获得锁（如果上述命令返回Nil，那么客户端可以在一段时间之后重新尝试），并且可以通过[DEL](http://www.redis.cn/commands/del.html)命令来释放锁。

客户端加锁之后，如果没有主动释放，会在过期时间之后自动释放。

可以通过如下优化使得上面的锁系统变得更加健壮：

- 不要设置固定的字符串，而是设置为随机的大字符串，可以称为token。
- 通过脚步删除指定锁的key，而不是[DEL](http://www.redis.cn/commands/del.html)命令。

上述优化方法会避免下述场景：a客户端获得的锁（键key）已经由于过期时间到了被redis服务器删除，但是这个时候a客户端还去执行[DEL](http://www.redis.cn/commands/del.html)命令。而b客户端已经在a设置的过期时间之后重新获取了这个同样key的锁，那么a执行[DEL](http://www.redis.cn/commands/del.html)就会释放了b客户端加好的锁。

解锁脚本的一个例子将类似于以下：

```
if redis.call("get",KEYS[1]) == ARGV[1]
then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

这个脚本执行方式如下：

EVAL …script… 1 resource-name token-value



#### GET 

>**时间复杂度：**O(1)

```bash
GET key   #返回key的value。如果key不存在，返回特殊值nil。如果key的value不是string，就返回错误，因为GET只处理string类型的values。
```

```
redis> GET nonexisting
(nil)
redis> SET mykey "Hello"
OK
redis> GET mykey
"Hello"
redis> 
```



#### GETRANGE

> **时间复杂度：**O(N) N是字符串长度，复杂度由最终返回长度决定，但由于通过一个字符串创建子字符串是很容易的，它可以被认为是O(1)。

```bash
GETRANGE key start end  #警告：这个命令是被改成GETRANGE的，在小于2.0的Redis版本中叫SUBSTR。 返回key对应的字符串value的子串，这个子串是由start和end位移决定的（两者都在string内）。可以用负的位移来表示从string尾部开始数的下标。所以-1就是最后一个字符，-2就是倒数第二个，以此类推。
这个函数处理超出范围的请求时，都把结果限制在string内。
```

```bash
redis> SET mykey "This is a string"
OK
redis> GETRANGE mykey 0 3             #下标从0开始
"This"
redis> GETRANGE mykey -3 -1           #-3 表示倒数第三个
"ing"
redis> GETRANGE mykey 0 -1           
"This is a string"
redis> GETRANGE mykey 10 100        #第10到范围之外 即 从第十个到末尾  
"string"
redis>GETRANGE mykey 0 100          #超过最大范围 返回字符串本身
"This is a string"
```



#### SETRANGE

> 时间复杂度：O（1），不计算将新字符串复制到位所需的时间。 通常，此字符串非常小，因此摊销后的复杂度为O（1）。 否则，复杂度为O（M），其中M为值参数的长度。

```bash
SETRANGE key offset value  #这个命令的作用是覆盖key对应的string的一部分，从指定的offset处开始，覆盖value的长度。如果offset比当前key对应string还要长，那这个string后面就补0以达到offset。不存在的keys被认为是空字符串，所以这个命令可以确保key有一个足够大的字符串，能在offset处设置value。注意，offset最大可以是229-1(536870911),因为redis字符串限制在512M大小。如果你需要超过这个大小，你可以用多个keys。
     # 警告：当set最后一个字节并且key还没有一个字符串value或者其value是个比较小的字符串时，Redis需要立即分配所有内存，这有可能会导致服务阻塞一会。在一台2010MacBook Pro上，set536870911字节（分配512MB）需要～300ms，set134217728字节(分配128MB)需要～80ms，set33554432比特位（分配32MB）需要～30ms，set8388608比特（分配8MB）需要8ms。注意，一旦第一次内存分配完，后面对同一个key调用SETRANGE就不会预先得到内存分配。
```

```bash
redis> SET key1 "Hello World"
OK
redis> SETRANGE key1 6 "Redis"      #下标从0开始 替换第6个往后的字符为Redis
(integer) 11
redis> GET key1
"Hello Redis"
redis> 
```

**补0的例子:**

```bash
redis> SETRANGE key2 6 "Redis"         #key2 不存在 会在前面补充0使的setrange命令可以从6开始替换
(integer) 11
redis> GET key2
"\x00\x00\x00\x00\x00\x00Redis"     # \x00表示2位的十六进制，在ASCII十进制中是码值为 0 的字符
redis> 
```



#### STRLEN

>**时间复杂度：**O(1)

```bash
STRLEN key   #返回key的string类型value的长度。如果key对应的非string类型，就返回错误。
```



```bash
redis> SET mykey "Hello world"
OK
redis> STRLEN mykey
(integer) 11
redis> STRLEN nonexisting
(integer) 0
redis> 
```







#### APPEND 

 ```bash
APPEND key value   #如果 key 已经存在，并且值为字符串，那么这个命令会把 value 追加到原来值（value）                     的结尾。 如果 key 不存在，那么它将首先创建一个空字符串的key，再执行追加操作
 ```



##### 节拍序列(Time series)

[APPEND](http://www.redis.cn/ommands/append.html) 命令可以用来连接一系列固定长度的样例,与使用列表相比这样更加紧凑. 通常会用来记录节拍序列. 每收到一个新的节拍样例就可以这样记录:

```
APPEND timeseries "fixed-size sample"
```

在节拍序列里, 可以很容易地访问序列中的每个元素:

- [STRLEN](http://www.redis.cn/commands/commands/strlen.html) 可以用来计算样例个数.
- [GETRANGE](http://www.redis.cn/commands/getrange.html) 允许随机访问序列中的各个元素. 如果序列中有明确的节拍信息, 在Redis 2.6中就可以使用[GETRANGE](http://www.redis.cn/commands/getrange.html)配合Lua脚本来实现一个二分查找算法.
- [SETRANGE](http://www.redis.cn/commands/setrange.html) 可以用来覆写已有的节拍序列.

该模式的局限在于只能做追加操作. Redis目前缺少剪裁字符串的命令, 所以无法方便地把序列剪裁成指定的尺寸. 但是, 节拍序列在空间占用上效率极好.

小贴士: 在键值中组合Unix时间戳, 可以在构建一系列相关键值时缩短键值长度,更优雅地分配Redis实例.

使用定长字符串进行温度采样的例子(在实际使用时,采用二进制格式会更好).

```bash
redis> APPEND ts "0043"
(integer) 4
redis> APPEND ts "0035"
(integer) 8
redis> GETRANGE ts 0 3
"0043"
redis> GETRANGE ts 4 7
"0035"
redis>
```



#### BITCOUNT 

> **时间复杂度：**O(N)

```bash
BITCOUNT key [start end]   #统计字符串被设置为1的bit数.
   一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start或 end参数，可以让计数只在特定的位上进行。 start 和 end 参数的设置和 GETRANGE 命令类似，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，以此类推。不存在的 key 被当成是空字符串来处理，因此对一个不存在的 key 进行 BITCOUNT 操作，结果为 0 。
```

关于统计字符串被设置为1的bit数，举个例子

```bash
127.0.0.1:6379> setbit k1 1 1 #第1位上设置为1，即01000000。 按ASCII码表 对应@
(integer) 0
127.0.0.1:6379> get k1       
"@"

```

<span style="color:red">所以bitcount统计，只统计二进制中，值为1的bit数量。即应用的时候可以统计只有两种情况的像用户活跃 不活跃，员工打卡、未打卡等。这些只需要一位就可以。用 0 和 1 表示。0  二进制对应=> 00000000  1  => 00000001   用bitcount刚好可以统计</span>



```bash
redis> SET mykey "foobar"       
OK
redis> BITCOUNT mykey
(integer) 26
redis> BITCOUNT mykey 0 0
(integer) 4
redis> BITCOUNT mykey 1 1
(integer) 6
redis>
```



应用：模式：使用 bitmap 实现用户上线次数统计。Bitmap 对于一些特定类型的计算非常有效。具体可以参考下面的Bitmap



#### DECR 

> **时间复杂度：**O(1)

```bash
DECR key   #对key对应的数字做减1操作。如果key不存在，那么在操作之前，这个key对应的值会被置为0。如果key有一个错误类型的value或者是一个不能表示成数字的字符串，就返回错误。这个操作最大支持在64位有符号的整型数字。
```



```bash
redis> SET mykey "10"
OK
redis> DECR mykey   #返回减一结果
(integer) 9
redis> SET mykey "234293482390480948029348230948"  #超过了最大限制
OK
redis> DECR mykey
ERR value is not an integer or out of range
redis> 
```



#### DECRBY 

> **时间复杂度：**O(1)

```bash
DECRBY key decrement  #将key对应的数字减decrement。如果key不存在，操作之前，key就会被置为0。如果key的value类型错误或者是个不能表示成数字的字符串，就返回错误。这个操作最多支持64位有符号的正型数字
```



```bash
redis> SET mykey "10"
OK
redis> DECRBY mykey 5   #把mykey的值减5
(integer) 5
redis> 
```



#### SETBIT key offset value

```bash
设置或者清空key的value(字符串)在offset处的bit值。

   那个位置的bit要么被设置，要么被清空，这个由value（只能是0或者1）来决定。当key不存在的时候，就创建一个新的字符串value。要确保这个字符串大到在offset处有bit值。
offset 表示bit的位置数，从0开始计，1字节的bit数组最大为7 。
SETBIT K1 1 1  ：第1位上设置为1，即01000000。 按ASCII码表 对应@。  --》GET K1  --》@
SETBIT K1 7 1 ：第7位设为1，即01000001。 按ASCII码表 对应A  --》GET K1  --》A
--》STRLEN K1　　--》1
SETBIT K1 9 1 ： 第9位设为1。超出分1字节连接。即01000001 01000000 。分字节来按ASCII码表 对应 A@ --》 GET K1  --》A@
--》STRLEN K1  --》2
SETBIT K1 26 1 ： 第26位设为1。超出分1字节连接。即01000001 01000000 00000000 00100000。
分字节来按ASCII码表  00000000对应（空字符）十六进0x00 ；00100000 对应空格
 --》 GET K1 　　 --》"A@\x00 "
--》STRLEN K1 　　--》4
SETBIT K1 31 1 ： 第32位设为1。超出分1字节连接。即01000001 01000000 00000000 00100001。
分字节来按ASCII码表 十六进0x00 ；00100001 对应!
 --》 GET K1 　　 --》"A@\x00!"
--》STRLEN K1 　　--》4
```



#### GETBIT

> 时间复杂度：O(1)

```bash
GETBIT key offset   #返回key对应的string在offset处的bit值 当offset超出了字符串长度的时候，这个字符串就被假定为由0比特填充的连续空间。当key不存在的时候，它就认为是一个空字符串，所以offset总是超出范围，然后value也被认为是由0比特填充的连续空间。到内存分配。
```



```bash
redis> SETBIT mykey 7 1   #设置为 00000001  
(integer) 0               # 0  设置成功
redis> GETBIT mykey 0     #获取这个字节第0个二进制数  即0
(integer) 0
redis> GETBIT mykey 7     
(integer) 1
redis> GETBIT mykey 100  #超过当前最大则默认它是0填充的 返回0
(integer) 0
redis> 
```





#### String类似的使用场景

value除了是我们的字符串还可以是我们的数字!

●计数器
●统计多单位的数量
●粉丝数
●对象缓存存储

## List



## Hash



## Set



## Zset









其与的一些API ,通过我们的学习吗,你们剩下的如果工作中有需要,这个时候你可以去查查看官方文档!

#### Zset应用案例思路:

set排序存储班级成绩表，工资表排序!

普通消息，1，重要消息2 ,带权重进行判断!
排行榜应用实现,取Top N测试!



## 地理位置 地理空间（geospatial）



### **geoadd**  添加地理位置

> **时间复杂度：**每一个元素添加是O(log(N)) ，N是sorted set的元素数量。

```txt
该命令以采用标准格式的参数x,y,所以经度必须在纬度之前。这些坐标的限制是可以被编入索引的，区域面积可以很接近极点但是不能索引。具体的限制，由EPSG:900913 / EPSG:3785 / OSGEO:41001 规定如下：
有效的经度从-180度到180度。
有效的纬度从-85.05112878度到85.05112878度。
当坐标位置超出上述指定范围时，该命令将会返回一个错误。
```



```bash
#参数key值()
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
```

### **GEOPOS    获取经度纬度**

> **时间复杂度：** O(log(N)) 对于每个成员的请求，其中N是排序集中的元素数。

```txt
从`key`里返回所有给定位置元素的位置（经度和纬度）
```



```bash
127.0.0.1:6379> GEOPOS china:city beijing
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> GEOPOS china:city beijing shanghai
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "121.47000163793563843"
   2) "31.22999903975783553"
```

```bash
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania" #添加2城市
(integer) 2
redis> GEOPOS Sicily Palermo Catania NonExisting  #获取
1) 1) "13.361389338970184"
   2) "38.115556395496299"
2) 1) "15.087267458438873"
   2) "37.50266842333162"
3) (nil)
redis> 
```



### GEODIST 返回两个给定位置之间的距离。

```txt
返回两个给定位置之间的距离。
如果两个位置之间的其中一个不存在， 那么命令返回空值。
指定单位的参数 unit 必须是以下单位的其中一个：
·m 表示单位为米。
·km 表示单位为千米。
·mi 表示单位为英里。
·ft 表示单位为英尺。
如果用户没有显式地指定单位参数， 那么 GEODIST 默认使用米作为单位。
GEODIST 命令在计算距离时会假设地球为完美的球形， 在极限情况下， 这一假设最大会造成 0.5% 的误差。
```



```bash
127.0.0.1:6379> GEODIST china:city beijing shanghai km #获取两地的直线距离
"1067.3788"
```



### GEORADIUS 附近的人 

>  通过半径查询

```txt
GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
```

```txt
以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。
范围可以使用以下其中一个单位：
m 表示单位为米。
km 表示单位为千米。
mi 表示单位为英里。
ft 表示单位为英尺。
在给定以下可选项时， 命令会返回额外的信息：
WITHDIST: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保           持一致。
WITHCOORD: 将位置元素的经度和维度也一并返回。
WITHHASH: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。
命令默认返回未排序的位置元素。 通过以下两个参数， 用户可以指定被返回位置元素的排序方式：
ASC: 根据中心的位置， 按照从近到远的方式返回位置元素。
DESC: 根据中心的位置， 按照从远到近的方式返回位置元素。

```



```bash
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqi 114.05 22.52 shengzhen #添加几个城市
(integer) 2
127.0.0.1:6379> geoadd china:city 120.16 30.24 hangzhou 108.96 34.26 xian
(integer) 2
127.0.0.1:6379> GEORADIUS china:city 115 32 1000 km #以经纬度 115 32点找半径1000km内的城市
1) "beijing"
2) "hangzhou"
3) "shanghai"
4) "xian"
5) "chongqi"
127.0.0.1:6379> GEORADIUS china:city 115 32 1000 km withcoord withdist desc 
                #以经纬度 115 32点找半径1000km内的城市并且将位置元素的经度和维度，距离降序返回
1) 1) "beijing"
   2) "887.6471"
   3) 1) "116.39999896287918091"
      2) "39.90000009167092543"
2) 1) "chongqi"
   2) "857.2653"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
3) 1) "shanghai"
   2) "618.6903"
   3) 1) "121.47000163793563843"
      2) "31.22999903975783553"
4) 1) "xian"
   2) "616.0497"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"
5) 1) "hangzhou"
   2) "528.8147"
   3) 1) "120.1600000262260437"
      2) "30.2400003229490224"
127.0.0.1:6379> GEORADIUS china:city 115 32 1000 km count 3 desc 
                #以经纬度 115 32点找半径1000km内的城市 并且只返回降序排列前三的城市
1) "beijing"
2) "chongqi"
3) "shanghai"
```



### GEORADIUSBYMEMBER 附近的人(城市名)



```txt
这个命令和 GEORADIUS 命令一样， 都可以找出位于指定范围内的元素， 但是 GEORADIUSBYMEMBER 的中心点是由给定的位置元素决定的， 而不是像 GEORADIUS 那样， 使用输入的经度和纬度来决定中心点指定成员的位置被用作查询的中心。
```



```bash
127.0.0.1:6379> georadiusbymember china:city shanghai 400 km 
1) "hangzhou"
2) "shanghai"
```





### GEOHASH 该命令将返回11个字符的Geohash字符串

```txt
返回一个或多个位置元素的 Geohash 表示。

通常使用表示位置的元素使用不同的技术，使用Geohash位置52点整数编码。由于编码和解码过程中所使用的初始最小和最大坐标不同，编码的编码也不同于标准。此命令返回一个标准的Geohash，在维基百科和geohash.org网站都有相关描述
```



```bash
127.0.0.1:6379> GEOHASH china:city shanghai 
1) "wtw3sj5zbj0"
127.0.0.1:6379> GEOHASH china:city shanghai hangzhou #当两个字符串越接近表示距离越近
1) "wtw3sj5zbj0"
2) "wtmkn31bfb0"
```



> **GEO底层的实现原理其实就是Zset !我们可以使用Zset命令来操作geo !**



```bash
127.0.0.1:6379> zrange china:city 0 -1  #输出所有geospatial 地理位置
1) "chongqi"
2) "xian"
3) "shengzhen"
4) "hangzhou"
5) "shanghai"
6) "beijing"
127.0.0.1:6379> zrem china:city xian  #移除xian这个城市
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1  
1) "chongqi"
2) "shengzhen"
3) "hangzhou"
4) "shanghai"
5) "beijing"
```

> 其他命令也可以用







## Hyperloglog

什么是基数?
A{1,3,5,7,8,7}     B{1,3,5,7,8}      基数(不重复的元素=> 1   3   5   7   8)=5,可以接受误差!

```txt
简介
Redis 2.8.9版本就更新了Hyperloglog 数据结构!
Redis Hyperloglog基数统计的算法!
```

优点:占用的内存是固定, 2^64不同的元素的基数,只需要费12KB内存!从内存来看，Hyperloglog是首选。
**应用场景：** 网页的UV (一个人访问一个网站多次,但是还是算作一个人! )
          传统的方式，set 保存用户的id ,然后就可以统计set中的元素数量作为标准判断!
          这个方式如果保存大量的用户id ,就会比较麻烦!我们的目的是为了计数,而不是保存用户id ;

**0.81%错误率**，统计uv任务，可以忽略不计

允许错误率用它。不允许还是用set等



```bash
127.0.0.1:6379> PFadd mykey a b c d e f g h i j  #给mykey的hyperloglog添加元素
(integer) 1
127.0.0.1:6379> PFCOUNT mykey       #统计mykey的元素 不统计重复的
(integer) 10 
127.0.0.1:6379> PFadd mykey2 i j z x c v b n m
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2  #给mykey mykey2取并集 并集为mykey3
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 15
```





## Bitmaps

```txt
位存储
应用场景 ： 统计用户信息，活跃,不活跃!登录、未登录!打卡, 365打卡!两个状态的,都可以使用Bitmaps !
Bitmaps位图,数据结构!都是操作二进制位来进行记录,就只有0和1两个状态!
365天= 365bit 1字节=8bit 46个字节左右!
```

设置李四打卡

```bash
127.0.0.1:6379> setbit lisi 1 1   #周一打卡 1是
(integer) 0
127.0.0.1:6379> setbit lisi 2 1   #周二打卡 1是
(integer) 0
127.0.0.1:6379> setbit lisi 3 1
(integer) 0
127.0.0.1:6379> setbit lisi 4 1
(integer) 0
127.0.0.1:6379> setbit lisi 5 1
(integer) 0
127.0.0.1:6379> setbit lisi 6 0  #周六打卡 0 否
(integer) 0
127.0.0.1:6379> setbit lisi 7 0
(integer) 0
127.0.0.1:6379> getbit lisi 4  #获取周四是否打卡 1 是
(integer) 1
127.0.0.1:6379> BITCOUNT lisi  #统计lisi总打卡次数
(integer) 5
127.0.0.1:6379> bitcount lisi 0 4 #统计lisi 周一到周五打卡次数 （当统计 1 5 时结果会是0 可能底层是位运算 涉及 0 1 运算）
(integer) 5
```







## 事务

```txt
redis的事务是一组命令的集合，一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行。
```

因为命令是一次性按照顺序执行完，不允许打断。 **一次性 顺序性 排他性**。执行一些列的命令。



**redis没有隔离级别的概念**，所有的命令在事务汇中，并没有直接执行，只有**发起执行命令(exec)**才会**执行**。

**redis单挑命令式保存具有原子性，但是事务不保证原子性**



#### Redis事务

  开启事务 （mutil）

 命令入队   (正常编写redis需要执行的命令)

 执行事务   （exec）

> **正常执行事务**

```bash
127.0.0.1:6379> multi         #开启事务
OK  
127.0.0.1:6379> set s1 qq     #命令入队
QUEUED
127.0.0.1:6379> set s2 ww     #命令入队
QUEUED
127.0.0.1:6379> get s2         #命令入队
QUEUED
127.0.0.1:6379> set s3 ee      #命令入队
QUEUED
127.0.0.1:6379> exec            #执行事务 依次按顺序执行
1) OK
2) OK
3) "ww"
4) OK
```



> **撤回事务**

```bash
127.0.0.1:6379> multi          
OK
127.0.0.1:6379> set s4 rr
QUEUED
127.0.0.1:6379> set s5 tt
QUEUED
127.0.0.1:6379> get s5
QUEUED
127.0.0.1:6379> discard           #取消事务 所有入队的命令都不会执行
OK 
```



> 当入队的命令有编译异常时，事务中所有的命令都不会执行

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set string ww
QUEUED
127.0.0.1:6379> set string qq
QUEUED
127.0.0.1:6379> get string ww      #命令编译时出现错误       
(error) ERR wrong number of arguments for 'get' command
127.0.0.1:6379> get string
QUEUED
127.0.0.1:6379> set string2 ww
QUEUED
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get string2
(nil)
```





> 当命令出现的是运行异常，不影响其他命令执行 



```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr s3      #字符串不能加1  运行时的错误
QUEUED
127.0.0.1:6379> set s5 tt
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range
2) OK
127.0.0.1:6379> get s5
"tt"
```





### 监控  Watch （类乐观锁 面试常问）

 悲观锁：很悲观，认为什么时候都会出问题，所以干什么都会加锁，然后再解锁，耗费资源

乐观锁：很乐观，认为什么时候都不会出问题，所以不会上锁。更新数据时，会先判断数据是否被修改过。类似于一个version,更新时获取比较version

 redis的watch可以用作于redis的乐观锁

```bash
127.0.0.1:6379> watch money   #监控money这个key  （类似于给money加了乐观锁）
OK
127.0.0.1:6379> multi        #开启事务
OK
127.0.0.1:6379> decrby money 20   #money减20 
QUEUED
127.0.0.1:6379> incrby outmoney 20  #outmoney加20 
QUEUED 
127.0.0.1:6379> exec            #exec执行事务之前,会先去对比监控的money值，有改变，事务汇执行失败 
(nil)
127.0.0.1:6379> unwatch     #先解除监控money   （类似于先解锁）
OK
127.0.0.1:6379> watch money  #再次监控money这个key
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby outmoney 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 180
2) (integer) 20
127.0.0.1:6379> 
```

在watch money之后，exec之前，开启一个窗口，连接redis去修改了money的值

```bash
127.0.0.1:6379> get money
"100"
127.0.0.1:6379> set money 200
OK
127.0.0.1:6379> 
```





## Jedis



导入依赖

```xml
<!--导入jedis的包-->
<dependencies>
<!--https://mvnrepository.com/artifact/redis. clients/jedis -->
<dependency>
<groupId>redis.clients</groupId>
<artifactId> jedis </artifactId>
<version>3.2.0</version>
</dependency>
<!--fastjson-->
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>fastjson</artifactId>
<version>1.2.62</version>
</dependency>
</dependencies>

```



```java
Jedis jedis = new  Jedis();  //构造方法创建连接对象
jedis.close();     //关闭redis
Transation multi = jedis.multi(); //开启redis事务
```



**阿里云上部署好redis后连接**

- 先添加阿里云的访问规则，配置redis的6379端口可以访问。对象为 0

![image-20200922161014464](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922161014464.png)

![image-20200922161329503](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922161329503.png)

- **配置redis.conf**

1）设置的访问白名单IP，把下面的bind 127.0.0.1 注释掉，就可以额允许其他访问了，不注释的话就是默认只允许本地访问    

![image-20200922161118869](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922161118869.png)

2）将保护模式改成no

![image-20200922161159366](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922161159366.png)

![image-20200922161252158](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922161252158.png)

### Spring boot 集成redis

SpringBoot操作数据: spring-data jpa jdbc mongodb redis !
SpringData也是和SpringBoot齐名的项目!
**说明:在SpringBoot2.x之后,原来使用的jedis被替换为了Lettuce**
jedis :采用的直连,多个线程操作的话,是不安全的,如果想要避免不安全的,使用jedis pool 连接池!更像BIO 模式
lettuce :采用netty ,实例可以再多个线程中进行共享,不存在线程不安全的情况!可以减少线程数据了,更像NIO 模式
源码分析:

```java
@Bean
//我们可以自己定义一个redisTemplate来替换这个默认的!
@ConditionalonMissingBean(name = "redisTemplate") 
pub1ic Redi sTemp 1ate <object，object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
//默认的RedisTemplate 没有过多的设置，redis 对象都是需要序列化!
//两个泛型都是object, object 的类型，我们后使用需要强制转换<String， object>
RedisTemplate<object，object> template = new RedisTemplate<>();
template.setConnectionFactory (redisConnectionFactory);
return template;
}
@Bean
@ConditionalonMissingBean // 由于String 是redis中最常使用的类型，所以说单独提出来了一个bean !
pub1ic string RedisTemplate stringRedisTemplate (RedisConnectionFactory redisConnectionFactory)
throws UnknownHostException
StringRedisTemplate template = new StringRedisTemplate() ;
tempTate.setConnectionFactory(redisConnectionFactory);
return temp1ate;
}
```





1.导入依赖

```xml
<!--操作redis -->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

```

2.配置redis

```properties
#配置redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
#配置连接池的时候用spring.redis.lettuce 因为在spring boot2.x之后源码里默认没有导入jedis的包     

```

  3.测试类

```java
@SpringBootTest
class Redis02SpringbootApplicationTests {
@Autowired 
private RedisTemplate redisTemplate ;
@Test
void contextLoads() {
    // redisTemplate操作不同的数 据类型，api和我们的指令是一样的
   // opsForValue操作字 符串类似String
   // opsForList 操作List类似ist
   // opsForSet
   // opsForHash
   // opsForZSet
   // opsForGeo
   // opsForHyperLogLog
  //除了进本的操作，我们常用的方法都可以直接通过redisTemplate操作，比如事务，和基本的CRID
  //获取redis的连接对象
  //RedisConnection connection = redisTemplate.getConnectionFactory().getConnection() ;
  //connection.fLushDb();
  //connection.fLushAll();
  redisTemplate.opsForValue().set("mykey","kuangshen");
  System.out.println(redisTemplate.opsForValue().get("mykey"));
}

```



![image-20200922164042004](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922164042004.png)



往下找赋值

![image-20200922164203219](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200922164203219.png)





#### 类序列化

![image-20200923192903260](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200923192903260.png)





**有@Validated注解的类不能正常通过实现serializable序列化，下面的person类去掉注解就可以正常序列化**

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person implements Serializable {

    private String name;
    private Integer age;
    private Boolean happy;
    /**
     * 默认的消息提示也可以
     */
    @Email(message = "邮箱格式错误!")
    private String email;
    private Date birth ;
    private Dog dog;

    public Person() {
    }
    ...
```









#### 自定义redisTemplate



```java
@Bean
@SuppressWarnings("a11")
public RedisTemplate<String, object> redisTemplate( RedisConnectionFactory factory) {
// 我们为了 自己开发方便，一般直接使用 <string, object>
  RedisTemplate<String, 0bject> template = new RedisTemplate<String, 0bject>();
  template. setConnectionFactory(factory);
  // Json序列化配置
   Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new    Jackson2JsonRedisSerializer(Object.class);
    ObjectMapper om = new objectMapper(); 
    om.setVisibility (PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    om.enableDefaultTyping (objectMapper.DefaultTyping.NON_ FINAL);
    jackson2JsonRedisSerializer.setObjectMapper(om);
    // string 的序列化
    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
    // key采用string的序列化方式
    template.setKeySerializer(stringRedisSerializer);
    // hash 的key也来用string的序列化方式
    template.setHashKeySerializer(stringRedisSerializer);
    // value序列化方式采用jackson
    template.setValueSerializer(jackson2JsonRedisSerializer);
    // hash的value序列化方式采用jackson
    template.setHashValueSerializer(jackson2JsonRedisSerializer);
    template.afterPropertiesSet();
}

```



#### ReidsUtils工具类

[D:\学习资源资料总结\【遇见狂神说】Redis视频素材\redis-study\springboot-redis\src\main\java\com\kuang\utils\RedisUtils.java]()



## 配置文件

> #### 配置文件  unit单位对大小写不敏感

  ![image-20200924135211264](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200924135211264.png)



> #### 可以导入其他配置文件



![image-20200924135354075](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200924135354075.png)



> #### 网络配置

```bash
bind 127.0.0.1       #绑定的访问地址 注释掉即允许任意ip访问
protected-mode no    #保护模式yes/no  yes:只有配置的ip可以访问或者设置访问密码 no:外网可以直接访问
port 6379            #配置redis的端口号
```



>#### 通用配置GENERAL



```bash
daemonize yes          #是否以守护进程开启 默认是no yes启动后可以再后台运行

pidfile /var/run/redis_6379.pid       #如果是以守护进程方式 我们需要指定一个进程的pid文件

# Specify the server verbosity level.  #日志级别设置
# This can be one of:
# debug (a lot of information, useful for development/testing)            
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)       #生产环境
# warning (only very important / critical messages are logged)
loglevel notice             #一般不修改
logfile ""            #指定日志文件位置

databases 16              #数据库数量

always-show-logo yes      #是否显示开启redis服务时的logo   

```



> #### 快照

持久化，在规定的时间内,执行了多少次操作,则会持久化到文件 .rdb  . aof
redis是内存数据库,如果没有持久化,那么数据断电就会丢失

```bash
save 900 1                 #900s内至少有1 个key进行修改 我们就进行持久化操作
save 300 10                #300s内至少有10 个key进行修改 我们就进行持久化操作
save 60 10000              #60s内至少有10000 个key进行修改 我们就进行持久化操作
#当然 之后我们要自己去定义这个测试

stop-writes-on-bgsave-error yes   #持久化出错之后是否继续工作

rdbcompression yes                #是否压缩rdb文件 需要消耗一定的cpu资源

rdbchecksum yes                   #保存rdb文件的时候 进行错误检查校验 出错可以自动修复

dir ./                            #rdb文件保存目录
```



> #### REPLICATION 主从复制



![image-20200925105010312](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925105010312.png)

> #### SECURITY  安全

![image-20200924141917538](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200924141917538.png)



设置密码    可以加一行 

```bash
requirepass 123456       #在配置文件中设置密码为123456  一般我们在redis中设置 不在配置文件
```

```bash
127.0.0.1:6379>config get requirepass        #查看密码 "" 没有设置密码
1)"requirepass"
2)""
127.0.0.1:6379>config set requirepass  "123456"      #设置密码 123456
127.0.0.1:6379>config get requirepass        
(error) NOAUTH Authentication required.
127.0.0.1:6379>auth 123456        #登陆 验证账户
OK
127.0.0.1:6379>config get requirepass        #查看密码
1)"requirepass"
2)"123456"
```



> #### CLIENTS 客户端一些限制

```bash
maxclients 10000  #能连接客户端的最大数量
```



> #### MEMORY MANAGEMENT  内存设置



```bash

maxmemory <bytes>  #配置redis最大内存

maxmemory-policy noeviction     #内存达到最大上限的处理策略  eg:移除过期key  报错...
1、volatile-1ru: 只对设置了过期时间的key进行LRU (默认值)
2、al1keys-1ru :删除1ru算法的key
3、volatile-random: 随机删除即将过期key
4、al1keys-random:随机删除
5、volatile-tt1 :删除即将过期的
6、noeviction :永不过期， 返回错误

```



> APPEND ONLY MODE aof模式配置



```bash
appendon1y no    #默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用!
appendfilename "appendonly.aof"  # 持久化的文件的名字

# appendfsync always     #每次修改都会同步。 消耗性能
appendfsync everysec     #每秒执行一次同步，可能会丢失这1s的数据!
# appendfsync no         #不执行同步，这个时候操作系统自己同步数据，速度最快!

```







## RDB**快照模式**

面试和工作,持久化都是重点!
Redis是内存数据库,如果不将内存中的数据库状态保存到磁盘,那么一旦服务器进程退出,服务器中的数据库状态也会消失。所以Redis提供了持久化功能!

> ### RDB ( Redis DataBase ) 

![image-20200924161903185](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200924161903185.png)



![img](https://img2018.cnblogs.com/blog/1465200/201904/1465200-20190413150710994-650893113.png) 

```txt
1.redis执行bgsave命令，Redis判断当前存在正在进行执行的子进程，如RDB/AOF子进程，存在bgsave命令直接返回

2.fork出子进程，fork操作中Redis父进程会阻塞

3.fork完成返回　　59117:M 13 Apr 13:44:40.312 * Background saving started by pid 59180

4.子进程进程对内存数据生成快找文件

5.子进程告诉父进程处理完成
```



​      在指定的时间间隔内将内存中的数据集快照写入磁盘,也就是行话讲的Snapshot快照,它恢复时是将快照文件直接读到内存里。

```txt
Redis会单独创建(fork) 一个子进程来进行持久化,会先将内存中的数据写入到一个临时文件中,待持久化过程都结束了,再用这个临时文件替换上次持久化好的文件。（非增量）
整个过程中,主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复,且对于数据恢复的完整性不是非常敏感,那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是RDB ,一般情况下不需要修改这个配置!
```



#### 触发机制

> 手动触发

- <span style="color:red">**执行save命令 ：**</span>>此命令会使用Redis的主线程进程同步存储，阻塞当前的Redis服务器，造成服务不可用，直到RDB过程完成。无论当前服务器数据量大小，线上不要用。

```bash
127.0.0.1:6379> save
OK(1.14s)
59117:M 13 Apr 13:34:51.948 * DB saved on disk
```

- <span style="color:red">**使用bgsave命令：**</span>>此命令会通过**fork()**创建**子进程**，在后台进程存储。只有**fork阶段会阻塞当前Redis服务器**，不必到整个RDB过程结束，一般时间很短。因此Redis内部涉及到RDB都采用bgsave命令。这里注意一点，无论RDB还是AOF，**由于使用了写时复制，fork出来的子进程不需要拷贝父进程的物理内存空间**，但是会复制父进程的空间内存页表。

```bash
127.0.0.1:6379> bgsave
Background saving started
59117:M 13 Apr 13:44:40.312 * Background saving started by pid 59180
59180:C 13 Apr 13:44:40.314 * DB saved on disk
59117:M 13 Apr 13:44:40.317 * Background saving terminated with success
```

- <span style="color:red">**自动触发**</span>

​        1）在redis.conf配置了save规则 即 save m n   m秒内对至少n个key有修改

```bash
save 900 1            #900s内对至少一个key有修改时
save 300 10           
save 60 10000
#stop-writes-on-bgsave-error：如果是yes，当bgsave命令失败时Redis将停止写入操作。
#rdbcompression：是否对RDB文件进行压缩，但是在LZF压缩消耗更多CPU
#rdbchecksum：是否对RDB文件进程校验
#dbfilename：配置文件名称，默认dump.rdb
#dir：配置rdb文件存放的路劲，这个参数比较重要。
```

​        2）flushdball的操作**ps:执行执行 flushall 命令，也会产生dump.rdb文件，但里面是空的.**

​       3）执行shutdown

​       4)主从复制场景下，如果从节点执行全量复制操作，则主节点会执行bgsave命令，并将rdb文件发送给从节点



#### 如何恢复数据？

**redis没有专门加载rdb命令**。因为RDB文件会在redis**服务启动**时**自动扫描加载**（优先加载aof文件），所以字需要把**rdb文件放入启动目录**就可以。服务器载入RDB文件期间处于阻塞状态，直到载入完成为止。
载入RDB文件时，会对RDB文件进行校验，如果文件损坏，日志中会打印错误，Redis启动失败(redis-check-rdb)

```bash
127.0.0.1:6379> config get dir        #获取安装目录 
1) "dir"
2) "/usr/local/bin"
```



#### 停用持久化

```bash
redis-cli config set save " " #命令行执行或者注释配置文件
```



<span style="color:red">**rdb默认保存的文件名是dump.rdb,所以我们在生产环境服务器一般都会备份这个文件**</span>

#### 优缺点：

优势

　　1.RDB是一个非常紧凑(compact)的文件，它保存了redis 在某个时间点上的数据集。这种文件非常适合用于进行备份和灾难恢复。

　　2.生成RDB文件的时候，redis主进程会fork()一个子进程来处理所有保存工作，主进程不需要进行任何磁盘IO操作。

　　3.RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

　劣势

　　1、RDB方式数据没办法做到实时持久化/秒级持久化。因为bgsave每次运行都要执行fork操作创建子进程，属于重量级操作，如果不采用压缩算法(内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑)，频繁执行成本过高(影响性能)

　　2、RDB文件使用特定二进制格式保存，Redis版本演进过程中有多个格式的RDB版本，存在老版本Redis服务无法兼容新版RDB格式的问题(版本不兼容)

　　3、在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改(数据有丢失) 不适合对数据要求过高的



前面我们在 redis.conf 配置文件中进行了关于save 的配置：

```txt
save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存
save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存
save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存
```

　　那么服务器状态中的saveparam 数组将会是如下的样子：

　　![img](https://images2018.cnblogs.com/blog/1120165/201806/1120165-20180605205542608-719758439.png)

　　②、dirty 计数器和lastsave 属性

　　dirty 计数器记录距离上一次成功执行 save 命令或者 bgsave 命令之后，Redis服务器进行了多少次修改（包括写入、删除、更新等操作）。

　　lastsave 属性是一个时间戳，记录上一次成功执行 save 命令或者 bgsave 命令的时间。

　　通过这两个命令，当服务器成功执行一次修改操作，那么dirty 计数器就会加 1，而lastsave 属性记录上一次执行save或bgsave的时间，Redis 服务器还有一个周期性操作函数 severCron ,默认每隔 100 毫秒就会执行一次，该函数会遍历并检查 saveparams 数组中的所有保存条件，只要有一个条件被满足，那么就会执行 bgsave 命令。

　　执行完成之后，dirty 计数器更新为 0 ，lastsave 也更新为执行命令的完成时间。



## 发布订阅







Redis 客户端可以订阅任意数量的频道。

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)





下表列出了 redis 发布订阅常用命令：

| 序号 |                          命令及描述                          |
| :--- | :----------------------------------------------------------: |
| 1    | [PSUBSCRIBE pattern [pattern ...\] 订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]]查看订阅与发布系统状态。 |
| 3    |      [PUBLISH channel message]将信息发送到指定的频道。       |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]] 退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]]订阅给定的一个或多个频道的信息。 |
| 6    |   [UNSUBSCRIBE [channel [channel ...\]]]指退订给定的频道。   |

订阅频道：

```bash
redis 127.0.0.1:6379> SUBSCRIBE redisChat         #订阅一个redisChat频道
Reading messages... (press Ctrl-C to quit)
1) "subscribe"         #订阅
2) "redisChat"         #频道名
3) (integer) 1         #订阅数
#等待消息
#下面发送后这里会结收到
1) "message"                               #类型：消息
2) "redisChat"                             #订阅频道 redisChat
3) "Redis is a great caching technique"    # 消息内容
1) "message"
2) "redisChat"
3) "Learn redis by runoob.com"
```

我们先重新开启个 redis 客户端，然后在同一个频道 redisChat 发布两次消息，订阅者就能接收到消息。

```bash
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"  #推送消息
(integer) 1
redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by runoob.com"
(integer) 1

# 订阅者的客户端会显示如下消息     
1) "message"                               #类型：消息
2) "redisChat"                             #订阅频道 redisChat
3) "Redis is a great caching technique"    # 消息内容
1) "message"
2) "redisChat"
3) "Learn redis by runoob.com"
```



### 原理

**订阅频道结构原理解析**

```txt
说明：每个 Redis 服务器进程都维持着一个表示服务器状态的 redis.h/redisServer 结构， 结构的pubsub_channels 属性是一个字典， 这个字典就用于保存订阅频道的信息，其中，字典的键为正在被订阅的频道， 而字典的值则是一个链表， 链表中保存了所有订阅这个频道的客户端。例子示意图：在下图展示的这个pubsub_channels 示例中， client2 、 client5 和 client1 就订阅了 channel1 ， 而其他频道也分别被别的客户端所订阅。
```

![img](https://img2018.cnblogs.com/blog/999593/201907/999593-20190722115529080-190360594.png)

```
操作：当客户端调用 SUBSCRIBE 命令时， 程序就将客户端和要订阅的频道在 pubsub_channels 字典中关联起来。
示意图：如果客户端 client10086 执行命令 SUBSCRIBE channel1 channel2 channel3 ，那么前面展示的 pubsub_channels 将变成下面这个样子，通过遍历所有输入频道。
```

![img](https://img2018.cnblogs.com/blog/999593/201907/999593-20190722115737561-1282013618.png)

```
结论：通过 pubsub_channels 字典， 程序只要检查某个频道是否为字典的键， 就可以知道该频道是否正在被客户端订阅； 只要取出某个键的值， 就可以得到所有订阅该频道的客户端的信息。
```

 

 **三、发布信息到频道结构解析**

```
原理说明：当调用 PUBLISH channel message 命令， 程序首先根据 channel 定位到字典的键， 然后将信息发送给字典值链表中的所有客户端。
例子示意图：对于以下这个 pubsub_channels 实例， 如果某个客户端执行命令 PUBLISH channel1 "hello moto" ，那么 client2 、 client5 和 client1 三个客户端都将接收到 "hello moto" 信息，通过遍历订阅频道的所有客户端。
```

![img](https://img2018.cnblogs.com/blog/999593/201907/999593-20190722120719019-733971705.png)

 

**四、退订频道**

```
原理：使用 UNSUBSCRIBE 命令可以退订指定的频道， 这个命令执行的是订阅的反操作： 它从 pubsub_channels 字典的给定频道（键）中， 删除关于当前客户端的信息， 这样被退订频道的信息就不会再发送给这个客户端。
```

 







## 主从复制

### 概念

- 主从复制,是指将一台Redis服务器的数据 ,复制到其他的Redis服务器。前者称为主节点(master/leader) ,后者称为从节点(slave/follower) ;**数据的复制是单向的,只能由主节点到从节点**。Master以写为主 , Slave以读为主。
- 默认情况下,**每台Redis服务器都是主节点**;且一个主节点可以有多个从节点(或没有从节点) ,但**一个从节点只能有一个主节点**。
  **主从复制**的作用主要包括:
  1、**数据冗余**:主从复制实现了数据的热备份,是持久化之外的一种数据冗余方式。
  2、**故障恢复**:当主节点出现问题时,可以由从节点提供服务,实现快速的故障恢复;实际上是一种服务的冗余。
  3、**负载均衡**:在主从复制的基础上,配合读写分离,可以由主节点提供写服务,由从节点提供读服务(即写Redis数据时应用连接主节点,读Redis数据时应用连接从节点) ,分担服务器负载;尤其是在写少读多的场景下,通过多个从节点分担读负载,可以大大提高Redis服务器的并发量。
  4、**高可用(集群)基石**:除了上述作用以外,主从复制还是哨兵和集群能够实施的基础,因此说主从复制是Redis高可用的基础。

一般来说,要将Redis运用于工程项目中,只使用一台Redis是万万不能的（服务宕机，一主二从）,原因如下:
1、从结构上,单个Redis服务器会发生单点故障,并且-台服务器需要处理所有的请求负载,压力较大;
2、从容量上,单个Redis服务器内存容量有限,就算一台Redis服务器内存容 量为256G ,也不能将所有内存用作Redis存储内存,一般来说， **单台Redis最大使用内存不应该超过20G**。
电商网站上的商品,一般都是一 次 上传,无数次浏览的, 说专业点也就是"多读少写"。
对于这种场景,我们可以使如下这种架构:（一主三从）

![image-20200925101430714](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925101430714.png)

主从复制,读写分离! 80% 的情况下都是在进行读操作!减缓服务器的压力。 架构中经常使用。一主二 二从。
只要在公司中,主从复制就是必须要使用的,因为在真实的项目中不可能单机使用Redis !



环境配置

只配置从库，不配置主库，默认就是主库不用配置

```bash
127.0.0.1:6379> info replication       #查看配置信息
# Replication
role:master                #角色 master
connected_slaves:0         #没有从机
master_replid:5c3c1b6ea2a1ba40bf05bce8ed7b5bb09c608e43
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```



- 复制3个配置文件,然后修改对应的信息
  1、端口
  2、pid 名字
  3、log文件名字
  4、dump.rdb 名字

其他配置按正常单机版的配置好。例如 ![image-20200925102800079](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925102800079.png)

### 一主二从

默认情况下,每台Redis服务器都是主节点;我们一般情况下只用配置从机就好了。

认老大。主(79)二从(80,81 )

slaveof ip port 

```bash
127.0.0.1:6380>slaveof 127.0.0.1 6379  #在其他redis服务上设置当前redis的主机 
                                       #命令的方式配置之后 重启服务配置就没有了
```



真实的从主配置应该在配置文件中配置,这样的话是永久的,我们这里使用的是命令,暂时的。配置文件才是永久

![image-20200925104955670](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925104955670.png)

> 细节



主机可以写,从机不能写只能读!主机中的所有信息和数据,都会自动被从机保存!
主机写:

```bash
127.0.0.1:6379>set k1 v1
OK
```

从机写：

![image-20200925105146454](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925105146454.png)



测试:**主机断开**连接,**从机依旧连接**到主机的,但是没有写操作,这个时候,**主机重连**了,**从机依旧可以**直接**获取到主机写的信息**。
如果是**使用命令行**,来**配置的主从**,这个时候**从机**如果**重启**了,就会**变回主机**。只要变为从机，立马就会从主机中获取值。

#### 复制原理

Slave启动成功连接到master后会发送一个sync命令
Master接到命令,启动后台的存盘进程,同时收集所有接收到的用于修改数据集命令,在后台进程执行完毕之后, **master将传送整个数据文件到slave ,并完成一次完全同步**。
**全量复制**:而slave服务在接收到数据库文件数据后,将其存盘并加载到内存中。
**增量复制**: 没有断开连接，Master继续将新的所有收集到的修改命令依次传给slave （即在主机写入一次key就会同步到从机）,完成同步
但是**只要是重新连接master** ,一次完全同步(**全量复制**)，将被自动执行。



> #### 类链表方式 层层链路



![image-20200925113353550](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925113353550.png)



上一个从机配置为下一个从机的主机。也可以完成主从复制。



> 如果主机断开连接，怎么手动选择主机？（**命令模式**）

主机断开连接，在其他连接中，执行命令 **谋朝篡位**

```bash
127.0.0.1:6380>slaveof no one   #谁执行的这个命令 谁就没有了主机，自己为主机
     #而且如果是采用上面的多主方式配置的，那么80执行后 变成主机 从机81还是在80下面
     #在执行完命令后 就算79重新连接，79下面也是没有任何从机的 只能重新配置              
```



## 哨兵模式

主从切换技术的方法是:当主服务器宕机后,需要手动把一台从服务器切换为主服务器,这就需要人工干预,费事费力,还会造成一段时间内服务不可用。这不是一种推荐的方式 ,更多时候,我们优先考虑哨兵模式。Redis从2.8开始正式提供 了Sentinel (哨兵)架构来解决这个问题。
谋朝篡位的自动版,能够后台监控主机是否故障,如果故障了根据投票数**自动将从库转换为主库**。
哨兵模式是一种特殊的模式 ,首先Redis提供了哨兵的命令,哨兵是一-个独立的进程,作为进程,它会独立运行。其原理是哨兵通过发送命令,等待Redis服务器响应,从而监控运行的多个Redis实例。









假设主服务器宕机,哨兵1先检测到这个结果,系统并不会马上进行failover过程,仅仅是哨兵1主观的认为主服务器不可用,这个现象成为主观下线。当后面的哨兵也检测到主服务器不可用,并且数量达到一-定值时,那么哨兵之间就会进行一次投票 ,投票的结果由一个哨兵发起,进行failover[故障转移]操作。切换成功后,就会通过发布订阅模式,让各个哨兵把自己监控的从服务器实现切换主机,这个过程称为客观下线。



#### 测试

我们目前的状态是一主二从!
1、配置哨兵配置文件sentinel.conf (和redis.conf放一起就可以)

```bash
#sentinel monitor 被监控的名称 host port 1
sentinel monitor myredis 127.0.0.1 6379 1
```

后面的这个数字1 ,代表主机挂了, slave投票看让谁接替成为主机,票数最多的,就会成为主机!
2、启动哨兵

```bash
[root@iZ8vb34cqkia5utb5hiex2Z bin]#redis-sentinel wenjieconfig/sentinel.conf #启用哨兵进程监控

24623:X 25 Sep 2020 15:11:05.513 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
24623:X 25 Sep 2020 15:11:05.513 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=24623, just started
24623:X 25 Sep 2020 15:11:05.513 # Configuration loaded
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 6.0.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 24623
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

24623:X 25 Sep 2020 15:11:05.514 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
24623:X 25 Sep 2020 15:11:05.516 # Sentinel ID is 3b7c5a4147f03f89f644d5ac96a19028fef8bda8
24623:X 25 Sep 2020 15:11:05.517 # +monitor master redis79 127.0.0.1 6379 quorum 1

##当把6379停用后哨兵会自动检测到然后投票选举新主机
24623:X 25 Sep 2020 15:11:05.517 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.474 # +sdown master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.474 # +odown master redis79 127.0.0.1 6379 #quorum 1/1
24623:X 25 Sep 2020 15:11:54.474 # +new-epoch 1
24623:X 25 Sep 2020 15:11:54.474 # +try-failover master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.477 # +vote-for-leader 3b7c5a4147f03f89f644d5ac96a19028fef8bda8 1
24623:X 25 Sep 2020 15:11:54.477 # +elected-leader master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.477 # +failover-state-select-slave master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.529 # +selected-slave slave 127.0.0.1:6380 127.0.0.1 6380 @ redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.529 * +failover-state-send-slaveof-noone slave 127.0.0.1:6380 127.0.0.1 6380 @ redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.591 * +failover-state-wait-promotion slave 127.0.0.1:6380 127.0.0.1 6380 @ redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.656 # +promoted-slave slave 127.0.0.1:6380 127.0.0.1 6380 @ redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.656 # +failover-state-reconf-slaves master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.713 # +failover-end master redis79 127.0.0.1 6379
24623:X 25 Sep 2020 15:11:54.713 # +switch-master redis79 127.0.0.1 6379 127.0.0.1 6380
24623:X 25 Sep 2020 15:11:54.713 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ redis79 127.0.0.1 6380
24623:X 25 Sep 2020 15:11:55.531 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ redis79 127.0.0.1 6380
24623:X 25 Sep 2020 15:12:24.726 # +sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ redis79 127.0.0.1 6380
```



当把6379停用后，哨兵会自动检测到。然后在从机中投票选举新主机。（投票选举算法）

日志信息：

![image-20200925152812416](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200925152812416.png)



此时就算6379这个主机重连，会被哨兵分配为新主机的从机。

```bash
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6380
master_link_status:up
master_last_io_seconds_ago:1
master_sync_in_progress:0
slave_repl_offset:88953
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:459fb10f31696591ccb60b0650cd95baf9b21c56
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:88953
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:88125
repl_backlog_histlen:829
127.0.0.1:6379> 
```



#### 优缺点

优点:
1、哨兵集群,基于主从复制模式,所有的主从配置优点，它全有
2、主从可以切换,故障可以转移,系统的可用性就会更好
3、哨兵模式就是主从模式的升级,手动到自动,更加健壮!
缺点:
1、Redis不好啊在线扩容的,集群容量- -旦到达上限,在线扩容就十分麻烦!
2、实现哨兵模式的配置其实是很麻烦的,里面有很多选择!



#### 哨兵模式配置

```bash
# Examp1e sentinel. conf
#哨兵sentine1实例运行的端口默认26379
port 26379

#哨兵sentine1的工作目录
dir /tmp 

#哨兵sentinel 监控的redis主节点的ipport
#master-name    可以自己命名的主节点名字只能由字母A-Z、数字0-9、这三个字符".-_"组成。
# quorum        配置多少个sentine1哨兵统一认为master主节点失联 那么这时客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2

#当在Redis实例中开启了requirepass foobared 授权密码这样所有连接Redis实例的客户端都要提供密码
#设置哨兵sentinel连接主从的密码注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passwOrd

#指定多少毫秒之后主节点没有应答哨兵sentinel此时哨兵主观上认为主节点下线默认30秒
# sentinel down-after-milliseconds <master-name> <mi 1 liseconds>
sentinel down-after-milliseconds mymaster 30000

#这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越多的slave因为replicati on而不可用。
可以通过将这个值设为1来保证每次只有一个slave处于不能处理命令请求的状态。
# sentine1 para1lel-syncs <master-name> <nums laves>
sentine1 para11e1-syncs mymaster 1

#故障转移的超时时间failover-timeout 可以用在以下这些方面:
#1.同一个sentine1对同一 个master两次failover之间的间隔时间。
#2.当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves 依然会被正确配置为指向master,但是就不按paralle1-syncs所配置的规则来了
#默认三分钟
# sentine1 failover-timeout <master-name> <mi11iseconds>
sentine1 failover-timeout mymaster 180000

# SCRIPTS EXECUTION

#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则:
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10 
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被- -个SIGKILL信号终止，之后重新执行。

#通知型脚本:当sentine1有任何警告级别的事件发生时(比如说redis实例的主观失效和客观失效等等)，将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一.个是事件的类型，个是事件的描述。如果sentine1. conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentine1无法正常启动成功。
#通知脚本
# sentine1 notificati on-script <master-name> <scri pt-path>
sentine1 notification-script mymaster /var/redis/notify.sh

#客户端重新配置主节点参 数脚本
#当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
#以下参数将会在调用脚本时传给脚本:
#<master-name> <ro1e> <state> <from-ip> <from-port> <to-ip> <to-port>
#目前<state>总是“failover”,
# <role> 是“Teader"或者“observer"中的- 一个。
#参数from-ip， from-port， to-ip， to-port是用来和旧的master和新的master (即旧的s7ave)通信的
#这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentine1 client- reconfig-script <master-name> <script-path>
sentine1 client-reconfig-script mymaster/var/redis/reconfig.sh

```



