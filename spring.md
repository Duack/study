#                         spring



**核心概念：** AOP IoC 轻量级框架

**轻量级框架：**每个jar包只有几M或者几百kb，通常几个jar包就可以实现工程功能。很小，灵活。

**Ioc:** （ Inversion of Control）控制反转。一种思想，通过依赖注入（DI）方式实现了。控制，是控制对象的创建 属性赋值等。反转，是原先创建对象 赋值等权限是程序员的，现在交给容器实现。作用主要是解耦合。（符合开闭原则 ）程序员可以少因为需求变动修改代码

**|ioc其他体现**：tomcat容器中自动创建servlet对象等

**AOP:** 面向切面编程

**DI:** （Dependency Injection）ioc的技术实现。 程序中提供对象的名称，可以获取到容器中该实例对象。容器自动创建 属性赋值等.底层用反射创建对象。



**spring配置文件：**头标签的spring-beans.xsd是约束文件。http://www.springframework.org/schema/beans/spring-beans.xsd  这个在网上是可读的。即可以在地址栏访问

  **根标签:**<beans></beans>

  **对象标签：**<bean></bean> 声明容器要创建的bean 。一个bean声明一个对象，在spring中，存储的对象放在一个map中，key是id,value是对象

​                 **属性： **

​                   **id**    bean的自定义名称 需要具有唯一性。spring通过这个名称找到对象

​                  **class**  类的全限定名称，不能是接口等不能创建对象的。**spring底层是反射机制创建对象**



<span style='color:red;font-size:20px;font-family:微软雅黑;'>***spring会把创建好的对象放入一个Map中。key是id,value是对应创建好的对象\* **</span>



获取容器对象（会创建所有bean配置的对象）：一个bean是一个对象，不管类是否是同一个类

ApplicationContext  ClassPathXmlApplicationContext("xml配置文件的地址");

在获取容器对象时就会创建好bean对象,可以在构造方法加一个输出测试 ClassPath...会在class路径加载配置文件

![image-20200901142015784](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200901142015784.png)

​      

​                     Object ApplicationContext .getBean("id值");

bean对象在加载完成后，就会把配置文件的所有bean创建，并且一个ioc容器的同一个bean示实例对象只有一个



**spring创建对象默认使用无参构造**





**获取容器内的bean对象数量以及bean名api：**

 applicationContext.getDBeanDefinitionCount();

applicationContext.getDBeanDefinitionNames();



![image-20200906001837737](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200906001837737.png)

# <span style="color:red">IOC</span>

## Ioc---基于配置文件的DI:



**DI:**     依赖注入，表示创建对象，给属性赋值

**实现方式：**

​    一 基于xml文件DI实现，使用标签和属性完成

​    二  使用spring注解完成属性赋值，基于注解的DI实现

**语法分类：**

   1.set注入（设置注入）:通过调用属性的set方法，实现属性赋值

   2.构造注入:spring调用类的有参构造方法，创建对象，在构造方法中完成属性赋值


**简单类型: spring中 规定java的基本数据类型(包装类型)和string都是简单类型。**
**di:给属性赋值**

**1.set注入(设置注入) : spring调用类的set方法，你可以在set方法中完成属性赋值**
1 )简单类型的set注入
  <bean id="xx" class="yyy">
  <property name=" 属性名字”value= ”此属性的值"/>
   <!--一个property只能给一个属性赋值-->
   <property....>
  </bean>

1. 2)引用类型的set注入
     <bean id="xx" class="yyy">
     <property name=" 属性名字”ref= ”引用属性的Id"/>
      <!--一个property只能给一个属性赋值-->
      <property....>
     </bean>



**2.构造注入: spring调用类有参数构造方法， 在创建对象的同时，在构造方法中给属性赋值。**

 **构造注入**使用<constructor-arg> 标签
         <constructor-arg>标签: -个<constructor-arg>表示构造方法-个参数。
               <constructor-arg>标签属性:(name和index都表示形参  value和ref都表示参数值)
                         name:表示构造方法的形参名
                         index:表示构造方法的参数的位置，参数从左往右位置是 0    1，2的顺序
                         value :构造方法的形参类型是简单类型的，使用value
                         ref :构造方法的形参类型是引用类型的，使用ref

当采用省略index方式，标签的参数顺序不能变。（参数1，参数2）

  <constructor-arg value=参数1值>

 <constructor-arg value=参数2值>



**引用类型的自动注入:** 
**byName**

1. **byName(按名称注入)** : java类 中引用类型的属性名和spring容器中( 配置文件) <bean>的id名称-样
    且数据类型是一致的，这样的容器中的bean，spring能够赋值给引用类型。
    语法:
    <bean id="xx" class="yyy" autowire= "byName">
    简单类型属性赋值
    </bean>

  eg:

  

**byType**

2. **byType(按类型注入) :** java类中 引用类型的数据类型和spring容器中( 配置文件) <bean>的class属性
是同源关系的, 这样的bean能够赋值给引用类型。

**同源:**
   1.java类中引用类型的数据类型和bean的class的值是一样的。

2. java类中引用类型的数据类型和bean的class的值父子类关系的。
3. java类中引用类型的数据类型和bean的class的值接口和实现类关系的
语法:
<bean id="xx" class="yyy" autowire="byType">
简单类型属性赋值
</bean>

注意:在xml配置文件，通过byType注入时，只能有一个符合要求，要是有多个会抛异常



**多配置文件：**

   使用的类很多，放在同一个项目，配置文件过于臃肿，不便于维护。修改保存浪费时间。而且，很多人共用一个文件，也不便于同步。

**分配方法**：

​              **·**功能模块分。一个模块一个配置文件。

​              类的功能分，数据库相关一个配置文件，service相关一个配置文件等。

多文件其他配置文件正常写，主配置文件如下：

![image-20200907213105720](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200907213105720.png)

导入方式：

![image-20200907213252901](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200907213252901.png)

在加载配置文件时，只要加载主配置文件就可以。

 

在主配置文件导入其他配置文件时，也可以使用**通配符**的方式。

下列匹配所有ba06下面的spring-开头的配置文件。此时主配置文件不能也在这个路径下，而且叫spring-开头。<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200907213711619.png" alt="image-20200907213711619" style="zoom: 80%;" />





## Ioc---基于注解实现DI



使用注解步骤：

  1.加入依赖，spring-context,加入这个同时，会加入spring-aop依赖，使用注解必须要spring-aop。

  2.在类中加入注解

  3.在配置文件中加入组件扫描器标签，说明注解在你项目中的位置

基本注解：

<span style="color:red">  **@Component**</span>
	**创建对象的**，等同 于<bean>的功能
	属性:value就是对象的名称，也就是bean的id值,
	value的值是唯一的,创建的对象在整个spring容器中就个位置:在类的上面@component(value = "mystudent ")                                                               等同于  <bean id= "myStudent" class= "com.bipowernode.ba01.student"/>，当不指定名称时，默认为首字母小写的类名对象。此时需要在配置文件配置扫描位置,默认使用无参构造。

![image-20200907225131292](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200907225131292.png)

<context:component-scan >:context 前缀，表示component-scan标签来自哪个命名空间（了解即可）

**指定扫描多个包：**

![image-20200908143559557](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908143559557.png)

父包最好到上一层就可以，太多层影响效率，spring采用递归扫描目录下面所有。

 使用注解容器内会多出以下对象:

org.springframework.context.annotation.internalConfigurationAnnotationProcessor
 org.springframework.context.annotation.internalAutowiredAnnotationProcessor
 org.springframework.context.event.internalEventListenerProcessor
 org.springframework.context.event.internalEventListenerFactory



<span style="color:red">**@Respotory** </span>  持久层注解，创建持久层类对象

用法与component注解一样，放在dao类的上面。表示创建dao对象, dao对象是能访问数据库的。



<span style="color:red">**@Service**</span>  业务层注解  创建业务层类对象

用法与component注解一样，放在service类上面，创建service对象，service对象是做业务处理,可以有事务等功能的。



<span style="color:red">**@Controller**</span> 用在控制器 创建控制器类对象

用法与component注解一样，放在控制器(处理器)类的上面,创建控制器对象的，控制器对象,能够接受用户提交的参数,显示请求的处理结果。



**总结：**@Repository , @service , @controller是**给项目的对象分层的**。和component一样能创建对象，但是有额外特殊功能。例如，**@Respotory**的可以访问数据库。**@Service**有事务等功能，**@Controller** 能够接受用户提交的参数,显示请求的处理结果。

非持久层 业务层和控制器时，用@component是没问题的。





<span style="color:red">**@Value**   </span>  **简单类型的属性赋值**
 属性:    value 是string类型的 ,表示简单类型的属性值
 位置:  1. 在**属性定义的上面**，无需set方法,**推荐使用**。
           2.在set方法的上面（不常用，没有意义，已经有setter方法）

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908144135559.png" alt="image-20200908144135559" style="zoom: 50%;" />

*<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908144357010.png" alt="image-20200908144357010" style="zoom: 67%;" />*



<span style="color:red">**@Autowired**</span>    **引用类型属性赋值**
spring 框架提供的注解，实现引用类型的赋值。spring中通过注解给引用类型赋值,使用的是自动注入原理, 支持byName, byType

**属性**:required是一个boolean类型的,默认true
**required=true :表示**引用类型赋值失败,程序报错,并终止执行。

**required=false :表示**引用类型赋值失败,程序正常执行,引用类型赋值null。

**一般**推荐为**true**，一开始就把程序的错误抛出来，方面解决。为false，后面使用为null,不方便调试。



**@Autowired:默认**使用的是**byType**自动注入。
位置:1 )在属性定义的上面，无需set方法，推荐使用
         2 )在set方法的上面 （不常用）

![image-20200908150538814](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908150538814.png)

如果要使用**byName方式**,需要做的是:(两注解不区分上下位置)
1.在属性上面加入@Autowired
2.在属性上面加入@Qualifier(value="bean的id") : 表示找对应id的Bean,使用指定名称的bean完成赋值。

![image-20200908150740685](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908150740685.png)



<span style="color:red">**@Resource**     </span>**引用类型属性赋值**
@Resource:来自jdk中的注解, spring框架提供了对这个注解的功能支持,可以使用它给引用类型赋值。
使用的也是自动注入原理，支持byName，byType . **默认是byName**，当byName方式赋值失败，再使用byType方式赋值。
位置: 1. 在属性定义的上面，无需set方法，推荐使用。
          2.在set方法的上面

![image-20200908152259953](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908152259953.png)



@Resource只使用byName方式，需要增加一个属性name
                                             name的值是bean的id (名称)

![image-20200908152419748](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908152419748.png)

(引入以上注解需要加入依赖：)

```xml
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```



**配置文件以及 属性配置文件 + 注解  搭配使用${}解耦合。**

1.添加属性配置文件

![image-20200908153456757](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908153456757.png)

2.**spring加载属性配置文件** （需要加**classpath:**）

![image-20200908153359856](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908153359856.png)

3.类的属性上加入占位符${}解耦合

![image-20200908153610582](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200908153610582.png)





综上，如果经常改，那么使用配置文件比较好，但是代码较多，比较复杂一点。如果不常改动，使用注解，比较简洁，而且，配合源码，可读性好。但是注解会让代码结构乱一点。有侵入性。注解为主，配置文件为辅。

**ioc能实现的解耦合:**ioc能够实现业务对象之间的解耦合，例如service和dao对象之 间的解耦合。





# <span style="color:red">AOP</span>



**是一种动态实现的规范化。把动态代理的实现步骤，方式都定义好，让开发人员用统一方式。**

AOP (Aspect Orient Programming)，面向切面编程。面向切面编程是从动态角度考虑程序运行过程。
AOP底层，就是采用动态代理模式实现的。采用了两种代理: JDK 的动态代理，与CGLIB的动态代理。

**Aspect:切面**，给你的目标类增加的功能，就是切面。像上面用的日志，事务都是切面。
切面的特点:一般都是非业务方法，独立使用的。



**怎么理解面向切面编程?**
1)需要在分析项目功能时，找出切面。哪些功能需要做成切面等。
2)合理的安排切面的执行时间(在目标方法前，还是目标方法后)
3)合理的安排切面执行的位置，在哪个类，哪个方法**增加增强**



**JDK动态代理实现方式**: jdk动态代理，使用jdk中的Proxy, Method, Invocai tonHanderl创建代理对象。
jdk动态代理要求目标类**必须实现接口**

**cglib动态代理:**第三方的工具库，创建代理对象，原理是继承。**通过继承目标类，创建子类**。子类就是代理对象。要求 目标**类不能是final**的，**方法也不能是final**的

**cglib**执行**效率**较jdk方式稍微高一点。

**spring**有接口**默认**使用**Jdk代理方式**，**没有接口**使用cglib方式。如果有接口要**采用cglib**可以做如下配置：

![image-20200912131254799](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912131254799.png)





**术语:**
1) **Aspect:切面**，表示增强的功能，就是一 堆代码，完成某个一个功能。非业务功能,<span style="color:green;font-family:'微软宋体'">**常见的切面功能有日志，事务，统计信息，参数检查 ，权限验证。**</span>
2) **JoinPoint:连接点** ，连接业务方法和切面的位置。就是某类中的业务方法，一个方法
3) **Pointcut :切入点**，指多个连接点方法的集合。多个方法
4)**目标对象:** 给哪个 类的方法增加功能，这个类就是目标对象
5**)Advice:通知**，通知表示切面功能执行的时间，在方法前执行还是方法后等

![image-20200910101737191](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910101737191.png)

说一个**切面有三个关键的要素**:
1)切面的功能代码，切面干什么，**Aspect**
2)切面的执行位置，使用**Pointcut**表示切面执行的位置
3)切面的执行时间，使用**Advice**表示时间， 在目标方法之前，还是目标方法之_后。

**动态代理的实现**：

  动态代理:可以在程序的执行过程中 ，创建代理对象。
通过代理对象执行方法，给目标类的方法增加额外的功能(功能增强)

**动态代理的作用:**
1)在目标类源代码不改变的情况下，增加功能。
2)减少代码的重复
3)专注业务逻辑代码
4)解耦合，让你的业务功能和日志，事务非业务功能分离。



<span style="color:red;font-family: 'Arial'; font-size:18px;">**什么时候用aop？**</span>

**1.当你要给一个系统中存在的类修改功能 ,但是原有类的功能不完善,但是你还有源代码,使用aop就增加功能**
**2.你要给项目中的多个类,增加一一个相同的功能,使用aop**
**3.给业务方法增加事务，日志输出**



**jdk动态代理实现步骤:**
       1.创建目标类，SomeServiceImpl目标类 ，给它的doSomeThing增加 输出时间。
       2.创建InvocationHandler接口的实现类，在这 个类实现给目标方法增加功能。
       3.使用jdk中类proxy，创建代理对象。实现创建对象的能力。

![image-20200909154004150](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200909154004150.png)

2.

![image-20200909153916924](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200909153916924.png)

3.proxy对象代理实现



![image-20200909154051289](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200909154051289.png)



**aspectJ**:一个 开源的专门做aop的框架。spring框 架中集成了aspectj框架，通过spring就能使用aspectj的功能。而spring自己的aop实现过于笨重，所以很少使用。

**aspectJ框架实现aop有两种方式:**
1.使用xml的配置文件:_配置 全局事务
2.使用注解，我们在项目中要做aop功能，一般都使用注解，aspectj有5个注解。

**AspectJ五个注解**
**1）@Before**

​        属性value:切入点的表达式(要给哪个方法加前置通知，目标方法，不是当前切入功能的方法)  参考下面aspectJ表达式

**2) @AfterReturning**
**3) @Around**
**4) @AfterThrowing**
**5) @After**



![image-20200910101753378](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910101753378.png)

**AspectJ表达式：**

![image-20200910093642696](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910093642696.png)

上面service不包括子包。包括子包需要..



![image-20200910093943982](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910093943982.png)



**使用aspectj实现aop的基本步骤:**
1.新建maven项目
2.加入依赖
    1 ) spring依赖
    2 ) aspectj依赖

![image-20200910095255337](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910095255337.png)    

3)junit单元测试
3.创建目标类:接口和他的实现类。要做的是给类中的方法增加功能
4.创建切面类:普通类
     1 )在类的上面加入@Aspect
     2)在类中定义方法，方法就是切面要执行的功能代码在方法的上面加入aspectj中的通知注解，例如**@Before**
        有需要指定切入点表达式execution()

![image-20200910103031472](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910103031472.png)

5.创建spring的配置文件:声明对象，把对象交给容器统一管理 声明对象你可以使用注解生成xml配置文件<bean>

1)声明目标对象
2 )声明切面类对象
3 )**声明aspectj框架中的自动代理生成器标签**。
    **自动代理生成器:**用来完成代理对象的自动创建功能的。即代理里面Porxy

![image-20200910102859863](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200910102859863.png)

**切面定义方法**,方法是实现切面功能的。
**@Before方法的定义要求:**
1.公共方法public
2.方法没有返回值
3.方法名称自定义
4.方法可以有参数,也可以没有参数。如果有参数，参数不是自定义的，有几个参数类型可以使用。



<span style="color:green">**注解方法JoinPoint参数  :**</span>

指定通知方法中的参数: JoinPoint
JoinPoint:业务方法,要加入切面功能的业务方法
**作用**是:可以在通知方法中获取方法执行时的信息，例如方法名称 ,方法的实参等
如果你的切面功能中需要用到方法的信息,就加入JoinPoint.这个JoinPoint参数的值是由框架赋予，**必须是第 一个位置的参数**



![image-20200911203056399](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200911203056399.png)





**@AfterReturning**   后置通知

方法定义要求同**@Before**一样，不一样的是该注解必须要有参数，推荐是Object

**属性**: 

​           1.value切入点表达式
​          2.returning自定义的变量,表示目标方法的返回值的。自定义变量名必须和通知方法的形参名一样。
**位置:**

​          在方法定义的上面
**特点:**
​      1.在目标方法之后执行的。
​      2.能够获取到目标方法的返回值,可以根据这个返回值做不同的处理功能
​      3.可以修改这个返回值 （但是不会改变原目标方法里面的值，可以参考执行顺序）



**后置通知的执行顺序是：**

   首先相当于调用目标方法获取到返回值，

​        Object res = doOther();

然后获取到返回值传入到后置通知的方法执行：

  MyAfterReturning(res);

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200911223422735.png" alt="image-20200911223422735" style="zoom:80%;" />





**@Around**   **环绕通知**方法的定义格式

​      1.public
​      2.**必须有一个返回值，推荐使用object**
​      3.方法名称自定义
​      4.方法有参数,**固定的参数ProceedingJoinPoint**

@Around:环绕通知
         **属性**:value切入点表达式
         **位置**:在方法的定义什么
**特点:**
       1.它是**功能最强**的通知
       2.在目标方法的**前和后都能增强**功能。
       3.**控制目标方法是否被调用执行**    (调用ProceedingJoinPoint.proceed()执行方法时，加上判断就可以控制方法执行与否)
      4.**修改**原来的**目标方法**的**执行结果**。 影响最后的调用结果
**环绕通知**，**等同于jdk动态代理的 Invocat ionHandler接口**
              **参数**: ProceedingJoinPoint (继承了JoinPoint) 就等同于Method
              **作用**:执行目标方法的
              **返回值**:就是目标方法的执行结果 ,可以被修改



经常用做于事务管理，方法前开启事务，方法执行完毕提交事务。

![image-20200911223833746](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200911223833746.png)

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200911223559695.png" alt="image-20200911223559695" style="zoom:80%;" />

目标方法返回值已经修改，并且控制了目标方法的调用。可以获取到实参值。



**@AfterThrowing**

异常通知方法的定义格式
1.pubLic
2.没有返回值
3.方法名称自定义
4.方法有个参数Exception，如果还有是JoinPoint





@AfterThrowing:异常通知
**属性**: 

1. value切入点表达式
2. throwinng 自定义的变量,表示目标方法抛出的异常对象。变量名必须和方法的参数名一样
   特点:
   1.在目标方法抛出异常时执行的
   2.可以做异常的监控程序， 监控目标方法执行时是不是有异常。
   如果有异常,可以发送邮件，短信进行通知



执行可以看做：
try{
           SomeService. Impl. doSecond(..)
}catch(Exception e){
        myAfterThrowing(e);

}

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200911224619010.png" alt="image-20200911224619010" style="zoom:67%;" />

两个ex要保持一致





**@After** : 最终通知

方法定义和before一样



**属性**: value切入点表达式
位置:在方法的上面
**特点**
         1.总是会执行
         2.在目标方法之后执行的

**一般用作于资源释放 清除**

 

相当于在finally里执行的代码





**@Pointcut**:

​         定义和管理切入点，如果你的项目 中有多个切入点表达式是重复的,可以复用的。可以使用@Pointcut
**属性**:

​         value   切入点表达式
位置:

​           在自定义的方法上面
**特点**:
当使用@Pointcut定义在一个方法的上面，此时这 个方法的名称就是切入点表达式的别名。
其它的通知中, value属性就可以使用这个方法名称,代替切入点表达式了

![image-20200912130435045](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912130435045.png)

本类中使用@Pointcut （括号不要忘记）

![image-20200912130500157](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912130500157.png)



 

<span style="color:red;font-size:28px">**集成mybatis:**</span>



<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221802131.png" alt="image-20200912221802131" style="zoom:150%;" />![image-20200912221907227](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200912221907227.png)

通过以上的说明，我们需要让spring创建以下对象
   1.独立的连接池类的对象，使用阿里的druid连接池

2. SqlSess ionFactory对象
3. 创建出dao对象

使用xml的<bean/>标签创建。因为没有源码，不能用注解





**步骤说明：**

​               1.创建maven项目

​               2 .添加Maven依赖

​                   1）spring依赖

​                   2）mybatis依赖

​                   3）mysql驱动

​                   4）spring事务依赖

​                   5）mybatis和spring集成的依赖：mybatis官方提供的，用来在spring中创建sqlSessionFactory 

​                        dao对象

​               3.创建实体类

​              4.创建dao接口以及mapper配置文件

​              5.创建mybatis主配置文件

​              6.创建service接口和实现类 属性是dao，在service调用dao的方法（符合项目常用的方法）

​              7.**创建spring配置文件，声明mybatis对象交给spring创建**

​                   **1）数据源**

​                   **2）sqlSessionFactory**

​                   **3）Dao**

















![image-20200914190238435](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200914190238435.png)







<span style="color:red;font-size:28px">**事务**</span>

事务是指一组sql或者一条sql，或者一个程序。

事务具有**四个特性**：

          原子性：一个事务是一个不可分割的工作单位，事务中包括的操作要么都做，要么都不做。
    
          一致性：事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。
    
         隔离性：一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
    
         持久性：持久性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

事务应该放在service的业务方法上。



**spring的声明式事务：**

          把事务相关的资源和内容提供给spring,spring就能处理事务提交回滚等，几乎不用写代码。

**处理事务，需要怎么做,做什么**
     spring处理事务的模型，使用的步骤都是固定的。把事务使用的信息提供给spring就可以了
      1)事务内部提交，回滚事务，使用的事务管理器对象，代替你完成commit, rollback
      2）事务管理器是一个接口和他的众多实现类。
**接口**: **PlatformTransactionManager** ，定义了 事务重要方法commit ，rollback
**实现类**:spring把每一种数据库访问技术对应的事务处理类都创建好了。
mybatis访问数据库---spr ing创建好的是DataSourceTransactionManager
hibernate访问数据库----spring创建的是HibernateTransactionManager
**怎么使用:你需要告诉spring你用是那种数据库的访问技术，怎么告诉spring呢?**
声明数据库访问技术对于的事务管理器实现类，在spring的配置文件中使用<bean>声明就可以了
例如，你要使用mybatis访问数据库，你应该在xm1配置文件中
<bean id="xxx" class=" . . .DataSourceTransactionManager" />



**接口**: **PlatformTransactionManager** ，定义了 事务重要方法commit ，rollback
**实现类**:spring把每一种数据库访问技术对应的事务处理类都创建好了。
mybatis访问数据库---spr ing创建好的是DataSourceTransactionManager
hibernate访问数据库----spring创建的是HibernateTransactionManager

**怎么使用:你需要告诉spring你用是那种数据库的访问技术，怎么告诉spring呢?**
声明数据库访问技术对于的事务管理器实现类，在spring的配置文件中使用<bean/>声明就可以了
例如，你要使用mybatis访问数据库，你应该在xm1配置文件中
<bean id="xxx" class=" . . .DataSourceTransactionManager " />

**事务的隔离级别：4个常量值**

这些常量均是以ISOLATION_开头。即形如ISOLATION_XXX。
DEFAULT（**默认情况非隔离常量**）:采用DB默认的事务隔离级别。MySql 的默认为REPEATABLE__READ; Oracle
默认为READ__COMMITTED。
➢ **READ__UNCOMMITTED** :读未提交。未解决任何并发问题。
➢ **READ__COMMITTED** :读已提交。解决脏读，存在不可重复读与幻读。
➢**REPEATABLE__READ**: 可重复读。解决脏读、不可重复读，存在幻读
➢**SERIALIZABLE**:串行化。不存在并发问题。

事务超时时间：执行一个方法，超过时间自动回滚。单位 秒。整数型。（影响因素比较多，网络 数据库执行 等）一般不设置。



<span style="font-size:25px">**事务的传播行为:控制业务方法是不是有事务的，是什么样的事务的。**</span>
**7个传播行为**，表示你的业务方法调用时，事务在方法之间是如果使用的。
**PROPAGATION_REQUIRED**

​         指定的方法必须在事务内执行。若当前存在事务，就加入到当前事务中;若当前没有事
务，则创建-一个新事务。这种传播行为是最常见的选择，也是Spring默认的事务传播行为。

eg:

​        如该传播行为加在doOther()方法上。若doSome()方法在调用doOther()方法时就是在事
务内运行的，则doOther()方法的执行也加入到该事务内执行。若doSome()方 法在调用
doOther()方法时没有在事务内执行，则doOther()方法会创建一个事务，并在其中执行。

即，doSome本身有事务，doOther有传播行为，那么两个方法都在doSome的事务下运行。

如果doSome本身没有事务，则，doOther会新开一个事务执行自身方法

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915112656732.png" alt="image-20200915112656732" style="zoom:50%;" />



**PROPAGATION_REQUIRES_NEW**

​       指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。

![image-20200915113721878](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915113721878.png)

**PROPAGATION_SUPPORTS**

总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。

![image-20200915113708643](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915113708643.png)

   以七三个需要掌握的
PROPAGATION_MANDATORY
PROPAGATION_NESTED
PROPAGATION_NEVER
PROPAGATION_NOT_ SUPPORTED





 **总结spring的事务**
   1.管理事务的是事务管理和他的实现类

2. spring的事务是一个统一模型
1)指定要使用的事务管理器实现类，使用<bean>
2)指定哪些类，哪些方法需要加入事务的功能
3)指定方法需要的隔离级别，传播行为,T超时
你需要告诉spring,你的项目中类信息，方法的名称，方法的事务传播行为。





注解：

属性：

​       1）**propagation**:用于设置事务传播属性。该属性类型为Propagation 枚举，

​             默认值为PROPAGATION _REQUIRED

​       2)   **isolation**:用于设置事务的隔离级别。该属性类型为Isolation 枚举，默认值为Isolation.DEFAULT 

​       3）**readonly** ：设置该方法的操作对数据库是否只读。默认为false 。当是查询方法时可以设置为true。只读可以不用上锁等。

​       4) **timeout**:用于设置本操作与数据库连接的超时时限。单位为秒，类型为int，默认值为-1，即没有时限。

​       5) **noRollbackFor/rollbackFor**:指定需要**不回滚/回滚**的异常类。类型为Class[]， 默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

​        6) **noRollbackForClassName/rollbackForClassName**:指定需要**不回滚/回滚**的异常类类名。类型为String[],默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

rol lbackFor:表示发生指定的异常一定回滚.
处理逻辑是:
         1)  spring框架会首先检查方法抛出的异常是不是在rollbackFor的属性值中
                 如果异常在rollbackFor列表中，不管是什么类型的异常，一定回滚。
        2)  如果你的抛出的异常不在rollbackFor列表中, spring会判断异常是不是RuntimeException,
                 如果是-定回滚。



**spring框架中提供的事务处理方案**



**1.适合中小项目使用的**
 注解方案。
  spring框架自己用aop实现给业务方法增加事务的功能，使 用@Transactional注解增加事务。
  @Transactional注解是spring框架自己注解，放在**public方法的上面**，表示当前方法具有事务。
可以给注解的属性赋值，表示具体的隔离级别，传播行为，异常信息等等
使用**@Transactional的步骤**:
     **1.需要声明事务管理器对象**（配置文件实现）
            <bean id="xx" class="DataSourceTransactionManager">
     **2.开启事务注解驱动，告诉spring框架，我要使用注解的方式管理事务。**（配置文件实现）
         spring使用aop机制，创建eTransactiona1所在的类代理对象，给方法加入事务的功能。
         spring给业务方法加入事务:
        在你的业务方法执行之前，先开启事务，在业务方法之后提交或回滚事务，使用aop的环绕遁知
         @Around("你要增加的事务功能的业务方法名称")
           object myAround() {
                  开启事务，spring给 你开启
                 try {
                          buy (1001,10) ;
                          spring的事务管理. commit() ;
                     } catch (Exception e) {
                          spring的事务管理. rollback() ;
                    }



![image-20200915151205709](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915151205709.png)



​        **3.在方法上加入@Transactional注解**（主要是用在public方法上面，类上也可以，意义不大。）

<img src="C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915153444342.png" alt="image-20200915153444342" style="zoom:67%;" />

当然，以上注解属性不赋值也可以。全采用默认值，默认异常抛出是运行时异常回滚。

到底事务中的**回滚**有没有做可以从**自增长id**看出端倪。

这种会先提交，然后回滚的时候删除。可以从自增长的id看出。

![image-20200915214614968](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200915214614968.png)



<span style="color:red; font-size:25px">**大型项目使用事务:**</span>

适合天型项目，有很多的类，方法，需要大量的配置事务，便用aspectj框架功能，在spring配置文件中
声明类，方法需要的事务。这种方式业务方法和事务配置完全分离。
实现步骤:都是在xm1配置文件中实现。
      1)要使用的是aspectj框架，需要加入依赖
          <dependency>
                 <groupId>org.springframework</groupId>
                 <artifactId>spring-aspects</artifactId>
                 <version>5.2.5.RELEASE </version>
       </dependency>
   2)声明事务管理器对象
     <bean id="xx" class="DataSourceTransactionManager'">
   3) 声明 创建代理。|



![image-20200916105028424](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916105028424.png)





![image-20200916105549690](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916105549690.png)

当方法有很多需要配置时，不可能在项目中全单独配置。这时候要用通配符。（spring优先匹配全方法名的，然后是带有通配符的方法名，最后是只有通配符的）

所以，大型项目的方法名一般有统一命名规则。便于加事务管理。例如，修改是modifyxxxx   增加是addxxxx等

![image-20200916110041393](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916110041393.png)

指明是哪些类下面的方法

![image-20200916110814138](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916110814138.png)





在web中如何使用applicationContext:(启动tomcat只加载一次)

在监听器中会自动读取spring容器配置文件，创建容器对象。然后可以在servlet获取对象

加入Contextl oaderLi stener的依赖

```xml
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-web</artifactId>
<version>5.2.5.RELEASE</version>
</dependency>
</dependencies>
```



![image-20200916115832497](C:\Users\文杰\AppData\Roaming\Typora\typora-user-images\image-20200916115832497.png)



