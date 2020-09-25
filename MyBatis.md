# MyBatis

mybatis是--个sql映射框架，提供的数据库的操作能力。增强的JDBC,
使用mybatis让开发人员集中精神写sq1就可以了，不必关心Connection, statement，Resultset
的创建，销毁，sql的执行。





实现步骤:
1.新建的student表
2.加入maven的mybatis坐标，mysq1驱动的坐标
3.创建实体类，Student--保存表中的一行数据的 (**类要有空参构造以及Get方法--传参反射用get方法获取值**)
4.创建持久层的dao接口，定 义操作数据库的方法
5.创建一个mybatis使用的配置文件叫做sq1映射文件:写sq1语句的。一 般一个表一个sql映射文件。这个文件是        xml文件。
6.创建mybatis的主配置文件:一个项目就一个主配置文件。
主配置文件提供了数据库的连接信息和sql映射文件的位置信息
7.创建使用mybatis类 通过mybatis访问数据库

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912215329966.png" alt="image-20200912215329966" style="zoom:67%;" />



Mapper配置文件：

sq1映射文件:写sql语句的，mybatis会执行这些sql
1.指定约束文件
< !DOCTYPE mapper
PUBLIC "-/ /mybatis.org/ /DTD Mapper 3.0//EN"
"http:/ /mybatis。org/dtd/mybatis -3- mapper. dtd"> 
mybatis -3-mapper .dtd是约束文件的名称，扩展名是dtd的。
2.约束文件作用:限制，检查在当前文件中出现的标签，属性必须符合mybatis的要求。
3.mapper 是当前文件的根标签，必须的。
**namespace :叫做命名空间，唯一值的，可以是自定义的字符串。最好使用dao接口的全限定名称。**
4.在当前文件中，可以使用特定的标签，表示数据库的特定操作。
<seelect> ;表示执行查询，select语句
<update>:表示更新数据库的操作，就是 在<update>标签中写的是update sql语句
<insert>:表示插入，放的是insert语句
<delete>:表示删除，执行的delete语句

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912213110585.png" alt="image-20200912213110585" style="zoom:80%;" />

select标签 :表示查询操作。
**id:你要执行的sql语法的唯一标识** ，mybatis会 使用这个id的值来找到要执行的sql语句
**可以自定义**， 但是最好使用**接口中的方法名称**。
resultType:表示结果类型的，是sql语 句执行后得到ResultSet ,遍历这个ResultSet得到java对象的类型。
值写的类型的全限定名称

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912213030231.png" alt="image-20200912213030231" style="zoom:80%;" />





主配置文件：

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912213909773.png" alt="image-20200912213909773" style="zoom:67%;" />

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912213931739.png" alt="image-20200912213931739" style="zoom: 67%;" />

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912213951577.png" alt="image-20200912213951577" style="zoom:67%;" />

一个mapper一个文件位置，从类根路径下开始





加入build插件才能编译xml文件到target(Idea默认.class文件目录)



<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912214233517.png" alt="image-20200912214233517" style="zoom:50%;" />





**如何写一个测试程序：**

**openSession(true) 表示自动提交事务**

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912214956482.png" alt="image-20200912214956482" style="zoom:67%;" />

**sqlSession**是**非线程安全**，需要在方法里先openSession()获取，然后在方法里面close（）

该测试方法只有接口，是因为mybatis采用**动态代理**生成实现类（**代理Mapper**）  项目常用方式

![image-20200913192812004](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200913192812004.png)

如果下面没有xml文件，执行如下步骤： 1 -  清理maven编译缓存 2  maven重新编译

上面没有再点build的3

（4 . 也可以直接复制到target目录） 5.再不行就清理idea缓存

![image-20200912220254584](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912220254584.png)

 idea缓存  file下面的Invalidate Caches

![image-20200912220517794](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912220517794.png)





插入行数：

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221119074.png" alt="image-20200912221119074" style="zoom:67%;" />

插入mapper写法

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221041661.png" alt="image-20200912221041661" style="zoom:67%;" />

**mybatis默认不提交事务**

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221008424.png" alt="image-20200912221008424" style="zoom:67%;" />

**输出sql到控制台**

![image-20200912221317181](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221317181.png)





封装成工具类：

  写到类的静态块，加载配置文件



<span style="font-size:18px">**Mybatis的动态代理机制：(类要有空参构造)**</span>

   1.获取sqlSession对象

​    2.根据sqlSession对象的getMapper(接口.class)获取代理对象

   3.通过接口的代理对象调用接口的方法执行mapper的sql



**要求：**

​       1.接口和mapper文件在一起

​       2.Mapper和接口的类名一致

​       3.mapper的namespace和接口全限定名一致

​       4.mapper中sql标签（<select> <update>...）的Id值和接口的方法名一致

​       5.接口中不要使用重载方法



**parameterType :**
   **dao接口中方法参数的数据类型。**
   parameterType它的值是java的数据类型**全限定名**称或者是**mybatis定义的别名**
   例如: parameterType="java . lang . Integer"
           parameterType=" int '
注意: parameterType不是强制的，mybati s通过反射机制能够发现接口参数的数类型。所以可以没有。一 般我们也不写。



**多个参数-@Param 简单参数**：会根据接口全限定名找对应参数

![image-20200914003157552](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914003157552.png)

**多个参数--对象属性 （要有get方法以及空参）**

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914004723264.png" alt="image-20200914004723264" style="zoom: 67%;" />



<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914004604069.png" alt="image-20200914004604069" style="zoom:67%;" />





**多个参数--按位置传参**（不建议使用 ，位置错了或者增加参数比较麻烦 而且可读性较差）

![image-20200914005029792](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914005029792.png)



**多个参数--使用Map<String,Object>方式**（不建议使用）

接口：（接口中传map不知道参数有多少 传了什么 可读性很差 ）

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914005405719.png" alt="image-20200914005405719" style="zoom:80%;" />

Mapper：

![image-20200914010022736](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914010022736.png)

测试方法：

![image-20200914005858933](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914005858933.png)





### <span style="color:red;font-size:20px">**#和$占位符：**</span>

<span style="color:green">**#占位符：**</span>

​               在sql中使用 ? 进行替换，sql语句采用PreparedStatement方式执行。效率较$高。可以防止sql注入

eg:

​           select * from user where id = #{id}

执行语句如下：

​         select * from user where id = ?     

**为什么能防止sql注入?**

​         PreprasedStatement采用预编译方式，即在上面语句中，会直接到数据库的执行计划而不会到编译。已经在数据库编译了这条sql，整个sql会只当成一条sql。就算你注入了sql，也只会是这样的：(即整个文本框输入的字符作为一个参数值（双引号里面）去替代?)

​                                     **select * from user where id = "1;drop table user;"**

​      在SQL参数未注入之前，提前对SQL语句进行预编译，而其后注入的参数将不会再进行SQL编译。也就是说其后注入进来的参数系统将不会认为它会是一条SQL语句，而默认其是一条一个参数。

<span style="color:green">**$占位符：**</span>

​               在sql中使用字符拼接的方式，采用statement执行sql。可以用于列名的替换，不能预防sql注入

eg:

​                       select * from user where  id =${id} order by ${colname}

在接口中就可以传两个参数。一个是id，一个是列名。传列名可以传表中的任意字段，然后会自动替换排序

​                       select * from user where  id =1 order by id

注意的是，当传入的是其他类型的值，如：是字符，要加双引号：

​                       select * from user where username = '张三'

接口传参数要传  **" '张三' "**   注意单引号 sqlSelectList(" '张三' ");







### <span style="color:red;font-size:20px">**ResultType封装返回接口：**</span>



封装Java**对象**：  

```xml
                   <select id="selectUser" resultType="com.wenjie.pojo.User">
                       select id,username from user
                   </select>
```
只要里面的id和username能对应到resultType里面的对象的属性名就可以

resultType里面对应的类型是接口中返回值类型。 list集合的话，只要对应泛型的类型对应是接口返回类型就可以

例如接口中是

​       List<User> selectAllUser();

mapper中只要resultType是user就行

```xml
       <select id="selectUser" resultType="com.wenjie.pojo.User">
              select * from user
          </select>
```


如果是**简单类型**的话 ,接口方法

​         int countUser();

mapper中的resultType可以使用mybatis对应java类型的**别名**或者**全限定名**

```xml
       <select id="selectUser" resultType="int/java.lang.Integer">
              select count(*) from user
          </select>
```



当然，上面返回类型是对象的也可以**自定义别名**

在主配置文件中：

​    第一种

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914150532261.png" alt="image-20200914150532261" style="zoom: 67%;" />

然后可以直接在resultType="stu"



第二种：

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914150814432.png" alt="image-20200914150814432" style="zoom:67%;" />****

然后可以直接在resultType="Student" 使用上面包，包下面所有类的类名作为别名。当然，如果两个包下面的类有重复名的，那会有歧义，会抛异常。

resultType和resultMap不能同时用



### <span style="color:red;font-size:20px">**resultMap：封装返回接口：**</span>

返回java的Map，只会返回一行记录。因为在Map中是以列名做为key的，返回map肯定只有一行结果。key不能重复

![image-20200916134950189](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916134950189.png)



返回自定义映射map

![image-20200916134922978](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916134922978.png)





当对**类的属性名**和**表列名不一致**时，封装对象有两种方法。

 一. 自定义映射map(推荐使用)

 二. 给查询的字段取别名



使用Like查询：

第一种：  在占位符的值准备查询内容  （推荐）

   ![image-20200916135817597](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916135817597.png)

![image-20200916135854635](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916135854635.png)



第二种： 单独在Mapper中拼接：

![image-20200916140031897](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916140031897.png)





### <span style="color:red">**动态sql**</span>



<span style="font-size:20px">**<if> 标签:**</span>

语法：

```xml
 <select>
    <if test="java对象属性的值 判断条件">

​       部分sql语句

   </if>

</select>
```




eg:

```xml
<select id="selectstudentIf" resultType="com. bjipowernode . domain. student" >
      select id,name, age, email from student  where 1=1
  <if test="name !=null and name !='' ">
       name = #{name}
  </if>
  <if test="age > 0">
       or age > #{age}
  </if>
</select> 
```
以上会有一个问题：1=1条件是什么用，是用来解决if中条件不成立时，造成sql语法错误



<span style="font-size:20px">**<where>标签：**</span>

语法：

```xml
<select>

<where>

   <if test="java对象属性的值 判断条件">

​       部分sql语句

   </if>

</where>

</select>
```

用来解决拼接动态sql时，where后面多出的一些and  or where 之类的关键字。当有一个或者多个条件成立时，会自动拼接一个**WHERE**关键字。


```xml
 <select id="selectstudentIf" resultType="com. bjipowernode . domain. student" >
      select id,name, age, email from student 
   <where>
     <if test="name !=null and name !='' ">
       name = #{name}
   </if>
   <if test="age > 0">
       or age > #{age}
   </if>
 </where>
</select> 
```

当where 后面if的条件都不成立时 ：select id,name, age, email from student

当只有age条件成立时   select id,name, age, email from student  WHERE age > 参数值



<span style="font-size:20px">**<foreach>标签：**</span>

<foreach collection="" item="" open="" close= "" separator="">

collection: 接口传的集合对象名   item ： 循环中的临时变量名    open ： 开始拼接的符号   close ：结束拼接符号  separator ： 分隔的符号

![image-20200916144644764](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916144644764.png)



eg:     <!-- foreach使用1，List<Integer>-->


```xml
	<select id="selectForeachone" resultType=" com . bjpowernode . domain . student" >
	      select * from student where id in
	   <foreach collection="list" item="myid" open="(" close= ")" separator=",">
	         #{myid}
	    </foreach>
	</select>

​	<!-- foreach使用2，List<Student>   其实就是调用student.getId()-->
	<select id="selectForeachTwo" resultType=" com. bjpowernode . domain. student" >
	    select * from student where id in
	   <foreach collection="list" item="stu" open="(" close=")" separator=",">
	        #{stu.id}
	   </foreach>
	</select>
```





<span style="font-size:20px">**<sql>代码片段:**</span>

​    1.定义片段   <sq1 id="唯一标识">  sql片段  </sql>  

​    2.引入代码片段 ：  <include refid="<sql>的唯一标识"/>  

```xml
<!--定义sq1片段-->
<sq1 id=" studentSql">
 id, name，age,email from student
</sq1>
<!--引入代码片段-->
<select id="selectForeachTwo" resultType="com. bjpowernode.domain.student" >
    select 
  <include refid=" studentsql"/>  
     where id in (
  <foreach collectiqn="list" item="stu" >
      #{stu.id},
  </foreach>
   -1 )
</select>

```



主配置文件：



```xml
transactionManager :mybatis提交事务，回顾事务的方式
      type:事务的处理的类型
          1 ) JDBC :表示mybatis底层是调用JDBC中的Connection对象的，commit，rollback
          2 ) MANAGED :把mybatis的事务处理委托给其它的容器(一个服务器软件,一个框架( spring ) )
```
![image-20200917105707387](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200917105707387.png)







mybatis中添加属性配置文件配置数据库信息：

```xml
<!--指定properties文件的位置，从类路径根开始找文件-->
<properties resource= "jdbc . properties" />

jdbc.properties:
jdbc.url=jdbc:mysql:///zdy_mybatis
jdbc.username=root
jdbc.password=wj950421

```



多个Mapper文件方式：

```xml
 <!-- sql mapper(sq1映射文件)的位置-->
 <mappers>
    <!--第一种方式:指定多个mapper文件-->
   <mapper resource=" com/ bjpowernode/ dao/ studentDao . xml"/>
   <mapper resource=" com/ bjpowernode/ dao/orderDao .xml" />
   <!--第二种方式:使用包名
       name: xml文件 ( mapper文件)所在的包名，这个包中所有xml文件一次都能加载给mybatis
       使用package的要求:
       1. mapper文件名称需要和接口名称一样，区分大小写的-样
       2. mapper文件和dao接口需要在同一目录
    -->
 <package name=" com.bjpowernode.dao"/>
 <package name=" com.bipowernode.dao2"/>
</mappers>
```





### Mybatis实现分页的组件：PageHelper



1）maven依赖：

    ```xml
<dependency>
<groupId> com. gi thub. pagehelper</ groupId>
<artifactId> pagehelper< /artifactId>
<version>5.1.10</version>
</ dependency>

    ```



2）配置插件：

```xml
在<environments>之前加入
<plugins>
<plugin interceptor="com.github.pagehelper.PageInterceptor" />
</plugins>

```

>  3)PageHelper对象

   查询语句之前调用PageHelper. startPage静态 方法。
除了PageHelper. startPage方法外，还提供了类似用法的PageHelper. offsetPage方法。
在你需要进行分页的MyBatis 查询方法前调用PageHelper. startPage静 态方法即可，紧跟在这个
方法后的第一个MyBatis查询方法会被进行分页。

```java
@Test
pubLic void testSelect() throws I0Exception {
//获取第1页，3条内容
PageHelper.startPage(1,3);
List<Student> studentList = studentDao.selectStudents() ;
studentList. forEach( stu -> System.out.println(stu));
}

```

