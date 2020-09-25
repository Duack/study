# Linux

## 操作系统简介

```txt

```



### Linux编辑器vi和vim

```txt
vi和vim是Linux的编辑器，早期用vi，vim是vim的增强版。可以用vim命令进行文本文件的创建、编辑查看等。
```

#### 编辑

```bash
vim test.txt   #如果文件存在则打开 不存在则创建打开
```

![image-20200920172102720](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200920172102720.png)

**一般模式（默认）**： 打开后通过文件的↑↓←→移动光标查看文件内容。

**编辑模式**：在**一般模式下**，按 **i ** 或者 **a** 键进入编辑模式，对文件内容进行编辑。**Esc退出**编辑模式，回到

​                     **一般模式**。（不会自动保存）

**命令模式：**在**一般模式**下，按shift+":"【实则就是冒号键 因为冒号和分号在一个按键】进入命令模式，下方会有冒号。输入命令。

```txt
    命令： q+!    强制退出vim编辑器，并且不保存更改
​         w+q    保存文件并退出编辑器
​          q    （没有更改文件时）退出编辑器
```

![image-20200920172223376](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200920172223376.png)

#### 常用快捷键

```txt
复制当前行 ： 【一般模式】下，按 【y+y】键复制光标行到剪切板，按【p】键把剪切板内容粘贴到光标所在下一行
复制当前行往下5行： 【一般模式】下，按 【5+y+y】键复制光标行和往下共五行到剪切板（只有4行就只会复制四                       行），按【p】键把剪切板内容粘贴到光标所在下一行.当然任意行也可以，改下数字键
搜索文件关键字： 【命令模式】下，输入【/+关键字】，回车进行查找。查找后，按【n】可以可以搜索下一个匹配的关                   键字
删除当前行 ： 【一般模式】下，按【d+d】删除光标所在行
删除当前和向下5行： 【一般模式】下，按【5+d+d】删除，任意行也可以
撤销上次编辑内容：  【一般模式】下，按【u】撤销 可以多次撤销，类似win ctrl+z
快速移动光标到第十行：    【一般模式】下，【10+shift+g】 回车
显示/隐藏行号：  【命令模式】下，set + nu  显示行号   set+nonu   不显示行号
```







## 用户管理

Linux默认创建的是root，另一个是安装linux时需要手动创建的。其他账号则用root去创建。



创建一个用户 （需要有创建权限一般是root）

```bash
useradd 用户名 
   -> 在用户列表创建添加了用户
   -> 在/home 下创建了用户的目录，名称和用户名相同
   -> 默认创建了一个组（如果指定了就不创建），组名和用户名相同，因为用户必须要存在于一个组
useradd -d /home/ss lisi   #创建一个用户lisi 指定并创建用户根目录在home下的ss（不推荐）

设置用户密码
passwd 用户名   #密码需要一定的复杂度

```



删除用户(也需要权限)

```bash
userdel 用户名  #删除用户 不删目录
userdel -r 用户名  #删除用户同时删除用户的根目录
```



查看用户信息

```bash
id 用户名
[root@iZ8vb34cqkia5utb5hiex2Z home]# id wenjie
uid=1000(wenjie) gid=1000(wenjie) groups=1000(wenjie)
# gid用户的主组 创建后不可改，有且仅有一个  groups附加组 可以多个 后期通过组做权限控制
```



切换用户

```bash
su 用户名   #高权限到低权限无需密码  低权限到高权限需要密码 基于系统安全性 高权限有权限了切低无所谓
```





## 组

```txt
linux中的组相当于角色的概念，可以对有共性的用户进行统一管理;
每一个用户至少属于一个组，不能独立于组存在，也可以属于多个组;
新建用户时如果不指定组，则会新建一个组，组名跟用户名相同，并且把该用户添加到该组中。
```

  添加组

```bash
groupadd   组名
```

 删除组

```bash
groupdel   组名
```

把用户添加到组中

```bash
gpasswd -a 用户名 组名
```

把用户从组中移除

```bash
gpasswd -d 用户名 组名
```

添加用户时，指定所属的组（主组）

```bash
useradd -g 组名 用户名
```



### linux中的帮助命令:

1)、用来查看linux系统手册上的帮助信息: man命令  
```bash
  man ls
```

​    分屏显示、按回车翻一行、按空格翻一页、按q退出查看。
2)、用来查看命名的内置帮助信息（类似于程序里面程序员写的注释信息）: help命令  不常用

```bash
  help cd
```



## 目录

**查看当前所在的目录**

```bash
psd 
```

**查看指定目录下的所有子目录和文件列表**

```bash
ls [-l]  [指定目录名（默认当前目录）]
```

```bash
ls -l /home  #列表的形式显示
[root@iZ8vb34cqkia5utb5hiex2Z bin]# ls -l /usr/local/bin
total 18188
-rwxr-xr-x 1 root root     388 Feb 18  2020 chardetect
-rwxr-xr-x 1 root root     396 Feb 18  2020 cloud-id
-rwxr-xr-x 1 root root     400 Feb 18  2020 cloud-init
-rwxr-xr-x 1 root root    2108 Feb 18  2020 cloud-init-per
-rw-r--r-- 1 root root     485 Sep 21 15:00 dump.rdb
-rwxr-xr-x 1 root root     404 Feb 18  2020 easy_install
-rwxr-xr-x 1 root root     236 Feb 18  2020 easy_install-3.6
-rwxr-xr-x 1 root root     412 Feb 18  2020 easy_install-3.8
-rwxr-xr-x 1 root root    1005 Feb 18  2020 jsondiff
-rwxr-xr-x 1 root root    3663 Feb 18  2020 jsonpatch
-rwxr-xr-x 1 root root    1839 Feb 18  2020 jsonpointer
-rwxr-xr-x 1 root root     397 Feb 18  2020 jsonschema
-rwxr-xr-x 1 root root  790184 Sep 20 12:32 redis-benchmark
-rwxr-xr-x 1 root root 5559640 Sep 20 12:32 redis-check-aof
-rwxr-xr-x 1 root root 5559640 Sep 20 12:32 redis-check-rdb
-rwxr-xr-x 1 root root 1095800 Sep 20 12:32 redis-cli
lrwxrwxrwx 1 root root      12 Sep 20 12:32 redis-sentinel -> redis-server
-rwxr-xr-x 1 root root 5559640 Sep 20 12:32 redis-server
drwxr-xr-x 2 root root      24 Sep 20 12:29 wenjieconfig

```



**显示指定目录下所有的子目录和文件(包括虚拟的目录)即 ..  .这种**

```bash
 ls -a [目录名]
[root@iZ8vb34cqkia5utb5hiex2Z bin]# ls -l -a   #像下面的 .这种虚拟目录也显示了 或者 ls -al/-la
total 18192
drwxr-xr-x.  3 root root    4096 Sep 21 15:00 .
drwxr-xr-x. 13 root root     144 Feb 18  2020 ..
-rwxr-xr-x   1 root root     388 Feb 18  2020 chardetect
-rwxr-xr-x   1 root root     396 Feb 18  2020 cloud-id
-rwxr-xr-x   1 root root     400 Feb 18  2020 cloud-init
-rwxr-xr-x   1 root root    2108 Feb 18  2020 cloud-init-per
-rw-r--r--   1 root root     485 Sep 21 15:00 dump.rdb
-rwxr-xr-x   1 root root     404 Feb 18  2020 easy_install
-rwxr-xr-x   1 root root     236 Feb 18  2020 easy_install-3.6
-rwxr-xr-x   1 root root     412 Feb 18  2020 easy_install-3.8
-rwxr-xr-x   1 root root    1005 Feb 18  2020 jsondiff
-rwxr-xr-x   1 root root    3663 Feb 18  2020 jsonpatch
-rwxr-xr-x   1 root root    1839 Feb 18  2020 jsonpointer
-rwxr-xr-x   1 root root     397 Feb 18  2020 jsonschema
-rwxr-xr-x   1 root root  790184 Sep 20 12:32 redis-benchmark
-rwxr-xr-x   1 root root 5559640 Sep 20 12:32 redis-check-aof
-rwxr-xr-x   1 root root 5559640 Sep 20 12:32 redis-check-rdb
-rwxr-xr-x   1 root root 1095800 Sep 20 12:32 redis-cli
lrwxrwxrwx   1 root root      12 Sep 20 12:32 redis-sentinel -> redis-server
-rwxr-xr-x   1 root root 5559640 Sep 20 12:32 redis-server
drwxr-xr-x   2 root root      24 Sep 20 12:29 wenjieconfig
```



**切换目录: cd  目录名**

```txt
|->绝对目录:以盘符开始的目录叫绝对目录，从盘符开始查找目标目录
      cd /opt/testDir
      ~:当前用户的根目录。在任何目录下执行:cd ~,进入当前用户的根目录。
|->相对目录:以目录名开始的目录叫相对目录，从当前目录开始查找目标目录
     cd testDir 
     ..:当前目录的上一-级目录，从的当前目录开始查找它的.上一级目录。
     .:当前目录
      XXx.sh====>./XXX. sh
```



**创建目录 mkdir  [选项]  目录名**

```bash
创建目录: mkdir 目录名  (只能创建一级目录)
     1->绝对目录
        mkdir  /opt/testDir/test1  #在/opt/testDir目录 下创建- - 个目录test1 (使用绝对目录)
     1->相对目录
        mkdir test2                #在/optl/testDir目录 下创建-一个目录test2(使用相对目录)
创建多级目录: mkdir -p 目录名 
  mkdir -p test/test1
```



**删除一个空目录  rmdir  目录名** 

```bash
rmdir  test1
rmdir  test3
```





## 文件

 **创建文件**    **touch** 创建一个或者多个**空文件**

```bash
vi/vim 可以创建文件 这个是创建文件并且直接打开，有个缺点是 如果要使用脚本创建文件这个会打开文件导致脚本不能正常执行

touch 文件名或者文件名列表 （文件与文件之间用空格隔间开）  文件可以带路径创建
[root@iZ8vb34cqkia5utb5hiex2Z bin]# touch wenjieconfig/test.txt  test01.txt
```



**复制文件/目录 cp  [选项] source   dest** 

```bash
cp 源文件地址  目标文件地址  (只能复制一个文件和一个目录到目标地址)
cp -r 源文件地址  目标文件地址  （递归复制 即下面的子目录文件都会复制过去）
[root@iZ8vb34cqkia5utb5hiex2Z usr]# cp local/bin/wenjieconfig/redis.conf /home/wenjie
[root@iZ8vb34cqkia5utb5hiex2Z usr]# ls /home/wenjie/
redis.conf  soft

```



**删除文件/目录  rm  [选项]**

```bash
rm 文件名  （会有提示 只删除一个）
rm -f 文件名   （没有提示 强制删除文件）  force强制
rm -d 目录名   （没有提示强制删除目录）
rm -r 目录名  （提示  递归的方式去删除）
rm -rf 文件名/目录名  （强制递归删除）
```



**移动文件或者目录  mv  [选项] source   dest**

```bash
mv 文件名/目录名   目标的地址
如果在当前目录
mv t1.txt t1_new.txt  #这个类似于给t1文件重命名为t1_new，如果t1_new存在则提示是否覆盖
```





