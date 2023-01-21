---
typora-root-url: ./assets
---

# Spring学习笔记-Day01 容器篇

## 一. 通过 Idea 创建 maven 项目

javaweb学过，不多赘述.

## 二. IoC入门案例

1.pom.xml中导入坐标

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
```

2.右键resources文件夹，新建xml文件，选择XML配置文件里的Spring配置

<img src="/image-20230105122633550.png" alt="image-20230105122633550" style="zoom:40%;" />

3.定义Spring管理的类（接口）

```java
public interface BookDao {
    public void save();
}
```

```java
public class BookDaoImpl implements BookDao {	//实现类
    public void save() {			//重写save方法
        System.out.println("book dao save ...");
    }
}
```

4.配置对应的类作为Spring管理的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">	
    <!--上面是idea自动创建的Spring配置文件-->
    
    <!--1.导入spring的坐标spring-context，对应版本是5.2.10.RELEASE,已经在pom.xml配置完成（第一步）-->

    <!--2.配置bean-->
    <!--bean标签标示配置bean
    id属性标示给bean起名字
    class属性表示给bean定义类型-->
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
    <bean id="bean的唯一标识" class="这里要填类对应的路径，注意要是实现类">
</beans>
```

5.初始化IoC容器(Spring核心容器/Spring容器),通过容器获取bean

```java
public class App {
    public static void main(String[] args) {
        //获取IoC容器
        ApplicationContext atc=new ClassPathXmlApplicationContext("applicationContext.xml");
        //获取bean（根据bean配置id获取）
        BookDao bookDao = (BookDao) atc.getBean("bookDao");
        bookDao.save();	//调用bookDao的成员方法:  book dao save ...
    }
}
```

### 1、知识点

#### 1. ApplicationContext的三个常用实现类

#####  ClassPathXmlApplicationContext
 它可以加载路径下的配置文件，要求配置文件必须在路径下，否则加载不了(**配置文件所在的目录必须要在IDEA中设置为任何类型的目录但是一般设置为Resource类型的目录**)

~~~java
ApplicationContext ac = new ClassPathXmlApplicationContext("beans.xml");
~~~

##### FileSyetemXmlApplicationContext
它可以加载磁盘下任意路径下的配置文件（必须有访问权限）

加载方式如下：
~~~java
ApplicationContext ac = new FileSystemXmlApplicationContex("C:\\user\\greyson\\...")
~~~

##### AnnotationConfigApplicationContext
它是用于读取注解创建容器的

### 2. 核心容器的两个接口引发出来的问题

- ApplicationContext：它在创建核心容器时，创建对象采取的策略是采用立即加载的方式，也就是说，只要一读取完配置文件就马上创建配置文件中配置的对象
    - 单例对象适用
    - 开发中常采用此接口
- BeanFactory: 它在构建核心容器时，创建对象的策略是采用延迟加载的方式，什么时候获取 id 对象了，什么时候就创建对象。
    - 多例对象适用

## 三.DI入门案例

1.定义Spring管理的类（接口）

```java
public interface BookService {
    public void save();
}
```

```java
public class BookServiceImpl implements BookService {	//实现接口类
    //1.删除业务层中使用new的方式创建的dao对象
    //private BookDao bookDao=new BookDao(); //弃用
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
    //2.提供bookDao的set方法,由容器执行，往里面传对象
    public void setBookDao1(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}

```

2.在applicationContext.xml中绑定关系

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
		<!--配置server与dao的关系-->
        <!--property标签表示配置当前bean的属性
        name属性表示配置哪一个具体的属性
        ref属性表示参照哪一个bean-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <property name="bookDao1" ref="bookDao"/>	<!--ref中的bookDao指的是下面那个bean中填的id对应的名字-->
    </bean>			<!--实测name中的bookDao1是BookServiceImpl实现类中定义的方法的setxxx的xxx名称
						如setBookDao，name的值就是bookDao,setBookDao1，name的值就是bookdao1	-->
    
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

</beans>
```

3.main方法中运行测试

```java
public class App2 {
    public static void main(String[] args) {

        ApplicationContext atc=new ClassPathXmlApplicationContext("applicationContext.xml");

        BookService bookService = (BookService) atc.getBean("bookService");

        bookService.save();	//

    }
}
```

## 四.bean的基础配置

### 1.bean的基础配置

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
```

### 2.bean的别名配置

1.在bean中设置name属性:

```xml
<!--name:为bean指定别名，别名可以有多个，使用逗号，分号，空格进行分隔-->
<bean id="bookService" name="service service4 bookEbi" class="com.itheima.service.impl.BookServiceImpl">
    <property name="bookDao" ref="bookDao"/>
</bean>
```

2.测试:

```java
public class AppForName {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        BookService bookService = (BookService) ctx.getBean("service4");	//使用别名service4

        bookService.save();		//book service save ...			book dao save ...
    }
}
```

### 3.bean作用范围配置

```xml
    <bean id="bookService" name="service service4 bookEbi" class="com.itheima.service.impl.BookServiceImpl">
        <property name="bookDao" ref="dao"/>		<!--设置别名后，ref中也可以使用别名了-->
    </bean>
    <!--scope：为bean设置作用范围，可选值为单例singloton，非单例prototype-->
    <bean id="bookDao" name="dao" class="com.itheima.dao.impl.BookDaoImpl" scope="prototype"/>
```

使用单例模式时（默认模式），spring给我们的对象地址相同（即相同的对象），使用非单例模式时，spring给我们的对象地址不同（不同的对象）。

测试（非单例模式）：

```java
public class AppForScope {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

        BookDao bookDao1 = (BookDao) ctx.getBean("bookDao");
        BookDao bookDao2 = (BookDao) ctx.getBean("bookDao");
        System.out.println(bookDao1);		//com.itheima.dao.impl.BookDaoImpl@56ef9176
        System.out.println(bookDao2);		//com.itheima.dao.impl.BookDaoImpl@4566e5bd
    }
}
```

- 适合交给容器进行管理的bean
  - 表现层对象
  - 业务层对象
  - 数据层对象
  - 工具对象

- 不适合交给容器管理的bean
  - 封装实体的域对象

我们需要用对象传递东西时，对象本身没有变化， 这时候只用一个对象即可，对象存在的目的就是为了传递数据，无需new许多对象，适合使用单例模式，交给容器管理；

有一些对象封装了数据，每个对象之间的数据唯一，那么这些对象就不适合使用单例模式，不适合交给容器管理。

## 五.bean的实例化

### 1. 调用构造函数创建Bean(常用)

 调用构造方法创建Bean是最常用的一种情况，Spring容器通过new关键字调用构造器来创建Bean实例，通过class属性指定Bean实例的实现类，也就是说，如果使用构造器创建Bean方法，则<bean/>元素必须指定class属性，其实Spring容器也就是相当于通过实现类new了一个Bean实例。调用构造方法创建Bean实例，通过名字也可以看出，我们需要为该Bean类提供**无参数**的构造器。

* 1.Bean实现类Person.java

  ```java
  package ioc.pojo;
  public class Person{
      private String name;
      
      public String getName() {
          return name;
      }
      
      public void setName(String name) {
          this.name = name;
      }
  }
  ```

  因为是通过构造函数创建Bean，因此我们需要提供无参数构造函数，另外我们定义了一个name属性，并提供了setter方法，Spring容器通过该方法为name属性注入参数。

* 2.配置文件applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
      <!-- 指定class属性，通过构造方法创建Bean实例 -->  
      <bean id="person" class="cn.edu.wtu.Person">  
        <!-- 通过构造方法赋值 -->  
       <constructor-arg name="name" value="魔术师"></constructor-arg>  
      </bean>  
  </beans>
  ```

  配置文件中，通过<bean>元素的id属性指定该bean的唯一名称，class属性指定该bean的实现类，可以理解成Spring容器就是通过该实现类new了一个Bean。通过<constructor-arg>标签的name属性和value属性指定了：构造方法赋值。

### 2. 调用静态工厂方法创建Bean(了解)

把创建Bean的任务交给了静态工厂，而不是构造函数，这个静态工厂就是一个Java类，我们同样需要在<bean/>元素内添加class属性，上面也说了，静态工厂是一个Java类，那么该**class属性指定的就是该工厂的实现类**，而不再是Bean的实现类，告诉Spring这个Bean应该由哪个静态工厂创建，另外我们还需要添加**factory-method属性来指定由工厂的哪个方法来创建Bean实例**，因此使用静态工厂方法创建Bean实例需要为<bean/>元素指定如下属性：

1. class：指定静态工厂的实现类，告诉Spring该Bean实例应该由哪个静态工厂创建（指定工厂地址）

2. factory-method:指定由静态工厂的哪个方法创建该Bean实例（指定由工厂的哪个车间创建Bean）

如果静态工厂方法需要参数，则使用<constructor-arg/>元素传入

~~~java
public class StaticFactory {
     public static IAccountService getAccountService() {
           return new AccountServiceImpl();
     }
 }
~~~

配置方式如下：

~~~xml
<bean id = "accountService" class = "com.isias.factory.StaticFactory" factory-method="getAccountService"></bean>
~~~

### 3. 调用实例工厂方法创建Bean(了解)

使用某个类中的静态方法创建对象，并存入spring容器，如下

~~~java
/**
 *模拟一个工厂类，该类可能存在于jar包中，无法通过修改源码的方式来提供默认构造函数
 */
public class UserDaoFactory {
    public UserDao getUserDao(){	//非静态方法
        return new UserDaoImpl();
    }
}
~~~

配置方式如下：

~~~xml
<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>    <!--先把工厂对象造出来-->
<bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>
~~~

#### 改良：使用FactoryBean实例化bean（实用，要求掌握）

实现FactoryBean<T>接口，<T>泛型写要创建的对象,例如我要写UserDao这个接口

```java
//FactoryBean创建对象
public class UserDaoFactoryBean implements FactoryBean<UserDao> {		//新建一个类，实现FactoryBean<T>接口
    //代替原始实例工厂中创建对象的方法
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();		//返回实例化对象
    }
    public Class<?> getObjectType() {
        return UserDao.class;		//传入UserDao的class文件
    }
}
```

如此一来，xml配置文件中就简化写为：

```xml
<bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```

测试：

```java
public class AppForInstanceUser {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

        UserDao userDao = (UserDao) ctx.getBean("userDao");
        userDao.save();		//user dao save...
    }
}
```

那么问题来了，通过这种方式创建的对象是单例还是非单例的？测试打印地址试一下

```java
public class AppForInstanceUser {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

        UserDao userDao1 = (UserDao) ctx.getBean("userDao");
        UserDao userDao2 = (UserDao) ctx.getBean("userDao");
        System.out.println(userDao1);	//com.itheima.dao.impl.UserDaoImpl@1a968a59
        System.out.println(userDao2);	//com.itheima.dao.impl.UserDaoImpl@1a968a59
    }
}
```

结果显示，是**单例**，如果我们要变为非单例，只需在UserDaoFactoryBean这个类中，实现FactoryBean<T>的isSingleton这个方法，并return false;

```java
public class UserDaoFactoryBean implements FactoryBean<UserDao> {
    //代替原始实例工厂中创建对象的方法
    public UserDao getObject() throws Exception {
        return new UserDaoImpl();
    }
    public Class<?> getObjectType() {
        return UserDao.class;
    }
    public boolean isSingleton() {	//变为非单例
        return false;
    }
}
```

## 六.bean对象的生命周期

单例对象：

- 出生：当容器创建时发生
- 活着：只要容器还在对象就一直活着
- 死亡：容器销毁，对象消亡

(容器启动)构造器 -------> 初始化方法 -------> (容器关闭)销毁方法

总结：单例对象的声明周期和容器相同

多例对象：

- 出生：当我们使用对象时 Spring 框架为我们创建
- 活着：对象只要是在使用过程中就活着
- 死亡：当对象长时间不用，且没有别的对象引用时，由 Java 的GC回收

获取bean(构造器 ------> 初始化方法)  ---------> 容器关闭不会关闭销毁方法

后置处理器：

(容器启动)构造器 -------> 后置处理器before.... ---------->初始化方法 ------------>后置处理器after.... --------->bean初始化完成

无论bean是否有初始化方法，后置处理器都会默认其有，还会继续工作。

#### 1.通过xml配置生命周期的方法：

```xml
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" init-method="init" destroy-method="destory"/>
<!--在bean中设置init-method（初始化）和destroy-method（摧毁）,后面跟自己设置的方法名-->
```

#### 2.通过InitializingBean和DisposableBean接口：

```java
public class BookServiceImpl implements BookService, InitializingBean, DisposableBean {
    
    public void save() {
        System.out.println("book service save ...");
    }

    public void destroy() throws Exception {
        System.out.println("service destroy");
    }

    public void afterPropertiesSet() throws Exception {
        System.out.println("service init");
    }
}
```

#### 关闭容器方式

##### 1.手动关闭容器:

ClassPathXmlApplicationContext的close()方法

##### 2.注册关闭钩子，在虚拟机退出前关闭容器再退出虚拟机

ClassPathXmlApplicationContext接口registerShutdownHook方法

```java
public class AppForLifeCycle {
    public static void main( String[] args ) {
        //ApplicationContext变为ClassPathXmlApplicationContext
        ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

        BookDao bookDao = (BookDao) ctx.getBean("bookDao");
        bookDao.save();
        //注册关闭钩子函数，在虚拟机退出之前回调此函数，关闭容器
        //ctx.registerShutdownHook();
        //关闭容器
        ctx.close();
    }
}
```

## 七.依赖注入（Dependency Injection）

### 1.概述

1. 能注入的数据：

   - 基本类型和 String
   - 其他 bean 类型（在配置文件中或者注解中配置过的bean）
   - 复杂类型/集合类型

2. IOC的作用：减低程序间的耦合（即依赖关系）

   在当前类需要用到其他类的对象，由 Spring 为我们提供，而我们在配置文件中说明依赖关系的维护，这种方式就称为依赖注入。

### 2.注入方式

#### 1.使用 set 方法提供（常用的方式）

使用的标签：property

出现的位置：bean 标签的内部

标签的属性：

name : 用于指定注入时所使用的 set 方法

value : 用于提供基本类型和 String 类型的数据

ref : 用于指定其他的bean类型数据，它指的就是在 Spring 容器中出现过的bean对象

优势：创建对象时没有明确的限制，可以直接使用默认构造函数

弊端：如果有某个成员必须有值，是有可能 set 方法没有执行

##### 1.setter注入-简单类型(基本类型和 String 的注入方式)

在bean中定义引用类型属性，并提供可访问的set方法

```java
public class BookDaoImpl implements BookDao {

    private int connectionNum;
    //setter注入需要提供要注入对象的set方法
    public void setConnectionNum(int connectionNum) {
        this.connectionNum = connectionNum;
    }
    public void save() {	//测试
        System.out.println("book dao save ..."+connectionNum);
    }
}
```

配置文件中使用property标签的value属性注入简单类型数据

```xml
<!--注入简单类型-->
<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
     <!--property标签：设置注入属性-->
     <!--name属性：设置注入的属性名，实际是set方法对应的名称-->
     <!--value属性：设置注入简单类型数据值-->
     <property name="connectionNum" value="100"/>
</bean>
```

##### 2.setter注入-引用类型

在bean中定义引用类型属性，并提供可访问的set方法

```java
public class BookServiceImpl implements BookService{
	private BookDao bookDao;
    
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
}
```

配置文件中使用property标签ref属性注入引用类型数据

```xml
    <!--注入引用类型-->
    <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
        <!--property标签：设置注入属性-->
        <!--name属性：设置注入的属性名，实际是set方法对应的名称-->
        <!--ref属性：设置注入引用类型bean的id或name-->
        <property name="bookDao" ref="bookDao"/>
        <property name="userDao" ref="userDao"/>
    </bean>
```

##### 3.复杂集合类型的注入方式

- 用于给 List 结构集合注入的标签
  - list
  - array
  - set
- 用于给map结构集合注入的标签
  - map
  - properties

结构相同，标签可以互换，因此开发中只要记住两组标签即可

```xml
   <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
        <!--数组注入-->
        <property name="array">
            <array>
                <value>100</value>
                <value>200</value>
                <value>300</value>
            </array>
        </property>
        <!--list集合注入-->
        <property name="list">
            <list>
                <value>itcast</value>
                <value>itheima</value>
                <value>boxuegu</value>
                <value>chuanzhihui</value>
            </list>
        </property>
        <!--set集合注入-->
        <property name="set">
            <set>
                <value>itcast</value>
                <value>itheima</value>
                <value>boxuegu</value>
                <value>boxuegu</value>
            </set>
        </property>
        <!--map集合注入-->
        <property name="map">
            <map>
                <entry key="country" value="china"/>
                <entry key="province" value="henan"/>
                <entry key="city" value="kaifeng"/>
            </map>
        </property>
        <!--Properties注入-->
        <property name="properties">
            <props>
                <prop key="country">china</prop>
                <prop key="province">henan</prop>
                <prop key="city">kaifeng</prop>
            </props>
        </property>
    </bean>
```

#### 2.构造器注入

使用的标签：constructor-arg

标签所在位置：bean 标签的内部

标签中的属性：

- type : 用于指定要注入的数据类型，该类型也是构造函数中某个或某些参数的类型
- index : 用于指定要注入的数据给构造函数中指定索引位置的参数赋值，索引的位置时从0开始
- name(常用) : 用于指定给构造函数中指定名称的参数赋值
- value : 用于提供基本类型和String类型的数据
- ref : 用于指定其他的bean类型数据。它指的就是在spring的IOC核心容器出现过的bean对象

特点：在获取 bean 对象时，注入数据是必须的操作，否则无法操作成功

弊端：改变了 bean 对象的实例化方式，使我们在用不到这些数据的情况下也必须提供带参构造函数，因此开发中较少使用此方法，除非避无可避

例：

~~~java
    public class AccountServiceImpl implements IAccountService {
        // 如果时经常变化的数据不适用于依赖注入，此处仅为演示
        private String name;
        private Integer age;
        private Date birthday;
    
        public AccountServiceImpl(String name, Integer age, Date birthday){
            this.name = name;
            this.age = age;
            this.birthday = birthday;
        }
    
        public void  saveAccount() {
            System.out.println("service中的saveaccount()执行了");
        }
    
    }
~~~

测试类：

~~~java
    public static void main(String[] args) {
            //1.获取核心容器对象
            ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
            //2.根据id获取Bean对象
            IAccountService as  = (IAccountService)ac.getBean("accountService");
            as.saveAccount();
        }
~~~

配置如下：

~~~xml
<bean id = "accountService" class="com.sias.service.impl.AccountServiceImpl">
    <constructor-arg name = "name" value="taylor"><constructor-arg>
    <constructor-arg name = "age" value = "23"></constructor-arg>
    <constructor-arg name = "birthday" ref = "now"></constructor-arg>
</bean>
    
<bean id = "now" class = "java.util.Date"></bean>
~~~



#### 3.依赖自动装配

使用的标签：autowire

标签所在位置：bean 标签的内部

标签中的属性：

- byType(推荐) : 必须保障容器中相同类型的bean唯一
- byName : 必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用
- constructor: 构造器
- default: 默认
- no: 不使用

特点：自动装配用于引用类型依赖注入，不能对简单类型进行操作。

弊端：自动装配优先级低于setter注入与构造器注入，同时出现时自动装配失效。

```xml
<bean class="com.itheima.dao.impl.BookDaoImpl"/>
<!--autowire属性：开启自动装配，通常使用按类型装配-->
<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byName"/>
```

## 八.案例-数据源对象管理

##### 1.导入druid坐标

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>
```

##### 2.配置数据源对象作为Spring管理的bean

一般会采用单独抽出一个文件的方式来写，而不是直接写在application.xml文件里，具体看   九.加载properties文件

```xml
<!--    管理DruidDataSource对象-->
	<bean class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/数据库名称"/>
        <property name="username" value="root"/>
        <property name="password" value="703791321"/>
    </bean>
```

##  九.加载properties文件

#### 1.先创建jdbc.properties的文件，文件内写jdbc的连接信息

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/数据库名称
jdbc.username=root
jdbc.password=703791321
```

#### 2.开启context命名空间

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
~~~

#### 3.使用context命名空间，加载指定properties文件

不加载系统属性：在context标签内加入属性  system-properties-mode="NEVER"    （如果名字与系统环境变量名字冲突会优先加载系统环境变量名称，例如username,如果不加该标签，无论你指定的username是什么，最终都会加载你电脑的名称）

加载多个properties文件：location="a.properties,b.properties"

加载所有properties文件：location="*.properties"

加载properties文件标准格式(classpath指从类路径中搜索)：

```xml
<context:property-placeholder location="classpath:*.properties" system-properties-mode="NEVER"/>
```

从类路径或jar包中搜索并加载properties文件(classpath后加*)：

```xml
<context:property-placeholder location="classpath*:*.properties" system-properties-mode="NEVER"/>
```

#### 4.使用${}读取加载的属性值

```xml
    <bean class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
```

## 十.容器总结

### 容器相关

- beanFactory是IoC容器的顶层接口，初始化beanFactory对象时，加载的bean延迟加载
- ApplicationConText接口是Spring容器的核心接口，初始化时bean立即加载
- ApplicationConText接口提供的基础的bean操作相关方法，通过其它接口扩展其功能
- ApplicationConText接口常用初始化类
  - ClassPathXmlApplicationContext
  - FileSystemXmlApplicationContext

### bean相关

<bean

​		id="bookDao"																			bean的id

​		name="dao siasdao bookdaoImp1"								      bean的别名

​		class="com.isias.dao.impl.BookDaoImp1"					   	bean类型，静态工厂类，FactoryBean类

​		scope="singleton"																     控制bean的实例数量（默认单例）

​		init-method="init"																     指定生命周期初始化方法

​		destroy-method="destory"													  指定生命周期摧毁方法

​		autowire="byType"																    自动装配类型

​		factory-method="getInstance"												bean工厂方法，应用于静态工厂或者实例工厂

​		factory-bean="com.isias.factory.BookDaoFactory" 			 实例工厂bean

​		lazy-init="ture"																   		 控制bean的延迟加载

​		/>

### 依赖注入相关

<bean id="bookService" class="com.isias.service.impl.BookServiceImpl">

​		<constructor-arg name="bookDao" ref="bookDao" />												构造器注入引用类型

​		<constructor-arg name="userDao" ref="userDao" />

​		<constructor-arg name="msg" value="WARN" />															构造器注入简单类型

​		<constructor-arg type="java.lang.String" index="3" value="WARN"/>		 类型匹配与索引匹配

​		<property name="bookDao" ref="bookDao" />																 setter注入引用类型

​		<property name="userDao" ref="userDao" />

​		<property name="msg" value="WARN" />																			 setter注入简单类型

​		<property name="names">																										   setter注入集合类型

​				<list>																																		   list集合

​						<value>itcast</value>																									集合注入简单类型

​						<ref bean="dataSource" />																					  集合注入引用类型

​				</list>

​		</ property>

</ bean>
