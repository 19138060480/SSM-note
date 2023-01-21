# Spring学习笔记-Day02注解开发

## 一、注解开发定义bean

#### 使用@Component定义bean

```java
@Component("bookDao")
public class BookDaoImpl implements BookDao {
}
@Component
public class BookServiceImpl implements BookService {
}
```

#### 核心配置文件中通过组扫描加载bean

 <context:component-scan base-package="路径"/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
	<!--扫描com/isias/下的所有包-->
    <context:component-scan base-package="com.isias"/>

</beans>
```

#### @Component注解衍生注解

- Spring提供了@Component注解的三个衍生注解，作用与 `@Component` 完全一样，它们是 Spring 提供的更明确的划分，使三层对象更加清晰
  - @Controller:用于表现层bean定义
  - @Service:用于业务层bean定义
  - @Repository:用于数据层bean定义

```java
@Repository("bookDao")
public class BookDaoImpl implements BookDao {
}
@Service
public class BookServiceImpl implements BookService {
}
```

## 二、纯注解开发

Spring3.0开启了纯注解开发模式，使用java类替代配置文件，开启了Spring快速开发赛道

**@Configuration**注解用于设定当前类为配置类，替代了xml文件中的开头一大块

```xml
<!--以下内容被@Configuration注解替代-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

**@ComponentScan**注解替代扫描的context:component-scan标签，标签内base-package后的路径写在@ComponentScan内

```xml
<!--以下内容被@ComponentScan注解替代-->
<context:component-scan base-package="com.isias"/>
```

### java类代替Spring核心配置文件

```java
//声明当前类为Spring配置类
@Configuration
//设置bean扫描路径，多个路径书写为字符串数组格式
@ComponentScan("com.isias")
public class SpringConfig {
}
```

**@ComponentScan注解用于设定扫描路径，此注解只能添加一次，多个数据用数组格式**

```java
@ComponentScan({"com.isias.service","com.isias.dao"})
```

### 配置类初始化容器对象

读取Spring核心配置文件初始化容器对象切换为读取java配置类初始化容器对象，括号里面写配置类的.class文件

```java
//之前是加载配置文件初始化容器
//ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

//AnnotationConfigApplicationContext加载Spring配置类初始化Spring容器
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
```

## 三、bean管理

### 1.bean作用范围

- ​	使用**@Scope注解**控制bean的作用范围（单例or多例）
  - ​	单例(默认)：@Scope("singleton")
  - ​	多例：@Scope("prototype")

### 2.bean生命周期

​	使用**@PostConstruct**、**@PreDestroy**定义bean生命周期

```java
@Repository
//@Scope设置bean的作用范围
@Scope("singleton")
public class BookDaoImpl implements BookDao {

    public void save() {
        System.out.println("book dao save ...");
    }
    //@PostConstruct设置bean的初始化方法
    @PostConstruct
    public void init() {
        System.out.println("init ...");
    }
    //@PreDestroy设置bean的销毁方法
    @PreDestroy
    public void destroy() {
        System.out.println("destroy ...");
    }
}
```

## 四、注解开发依赖注入

### 1.注入引用类型

- 使用**@Autowired**注解开启自动装配模式（按类型）
  - 自动装配基于反射设计创建对象并暴力反射对应属性为私有属性初始化数据，因此无需提供setter方法
  - 自动装配建议使用**无参构造方法**创建对象（默认），如果不提供对应构造方法，请提供唯一的构造方法
  - 使用**@Qualifier**注解开启指定名称装配bean，@Qualifier无法单独使用，必须配合@Autowired注解使用

```java
@Service
public class BookServiceImpl implements BookService {
    //@Autowired：注入引用类型，自动装配模式，默认按类型装配
    @Autowired
    //@Qualifier：自动装配bean时按bean名称装配
    @Qualifier("bookDao")
    private BookDao bookDao;

    public void save() {
        System.out.println("book service save ...");
        bookDao.save();
    }
}
```

### 2.注入简单类型

使用**@Value**注解实现简单类型注入

```java
@Repository("bookDao")
public class BookDaoImpl implements BookDao {
    //@Value：注入简单类型（无需提供set方法）
    @Value("isias")
    private String name;

    public void save() {
        System.out.println("book dao save ..." + name);
    }
}
```

### 3.加载properties文件

使用**@PropertySource**注解加载properties文件

​	♦ 路径仅支持单一配置文件，多配置文件使用数组格式配置，**不允许使用通配符 ‘*’**

```java
@Configuration
@ComponentScan("com.itheima")
//@PropertySource加载properties配置文件
@PropertySource("jdbc.properties")
//加载多个properties配置文件
//@PropertySource({"a.properties","b.properties","c.properties"})
public class SpringConfig {
}
```

加载properties文件后，在注入简单类型时，可以读取properties文件内的数据，如下例：

1.properties文件内写入name的值

```properties
name=isias
```

2.在注入简单类型时直接引用

```java
@Component("bookDao")
public class BookDaoImpl implements BookDao {
    @Value("${name}")
    private String name;

    public void save() {
        System.out.println("book dao save ..." + name);
    }
}
```

## 五、注解开发管理第三方bean

### 1.第三方bean的管理

使用**@bean**注解修饰方法，管理第三方bean

```java
@Configuration
public class JdbcConfig {
    //定义一个方法获得要管理的对象
    //添加@Bean，表示当前方法的返回值是一个bean
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("com.mysql.jdbc.Driver");
        ds.setUrl("jdbc:mysql://localhost:3306/gjp");
        ds.setUsername("root");
        ds.setPassword("703791321");
        return ds;
    }
}
```

#### 将独立的配置类加入核心配置

- 使用独立的配置类来管理第三方bean

- 方式一：导入式

  - 使用**@Import**注解手动加入配置类到核心配置，此注解只能添加一次，多个数据用数组格式

    此时被导入的类（JdbcConfig）不用加@Configuration注解

```java
public class JdbcConfig {
    //@Bean修饰的方法，形参根据类型自动装配
    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        //set相关配置后return ds，set过程跳过
        return ds;
    }
}
```

```java
@Configuration
//@Import:导入配置信息
@Import({JdbcConfig.class})
public class SpringConfig {
}
```

- 方式二：扫描式(不推荐)
  -  使用**@ComponentScan**注解扫描配置类所在的包，加载对应的配置类信息

```java
@Configuration
//扫描com/isias下的所有文件，找到配置类
@ComponentScan("com.isias")
public class SpringConfig {
}
```

**导入式（Import）与扫描式（ComponentScan）区别**

@import
可以引入任何类，不一定是configuration类。这样就变得非常灵活。这里 的引入就是注入的意思。将该类注入到容器中，即使该类没有被注解@service, @controller,@repository, @component等所标注，也可以被spring管理。
@componentscan 这个注解主要是标注需要扫描哪些包路径。该路径下标注了@service, @controller,@repository, @component的类，都将被注入到容器中。除了这四个标签，包路径下标注了@configuration类，也会被注入到容器中。

### 2.注解开发实现为第三方bean注入资源

#### 	1.简单类型依赖注入

**@Value**注解可以读取properties配置文件中的配置信息，具体参照 4-3

```java
public class JdbcConfig {
    @Value("com.mysql.jdbc.Driver")
    private String driver;
    @Value("jdbc:mysql://localhost:3306/spring_db")
    private String url;
    @Value("root")
    private String userName;
    @Value("root")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(userName);
        ds.setPassword(password);
        return ds;
    }
}
```

#### 	2.引用类型依赖注入

引用类型注入**只需要为bean定义方法设置形参**即可，容器会根据类型自动装配对象，该形参必须是bean

```java
@Bean
public DataSource dataSource(BookDao bookDao){
    System.out.println(bookDao);
    DruidDataSource ds = new DruidDataSource();
    //set相关配置后return ds，set过程跳过
    return ds;
}
```

## 六、xml配置与注解配置对比

<img src=".\assets\image-20230107123748617.png" alt="image-20230107123748617" style="zoom:30%;" />

## 七、Spring整合Mybatis

### 1.pom.xml文件中新增jar包坐标

**Spring连接数据库的jar包坐标**

特别注意，spring-jdbc必须在spring-context加载到内存后在加载，不然会开启服务器时会爆红。

```xml
    <!--前面必须有spring-context的jar包坐标-->
	<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
```

**Mybatis连接Spring的jar包坐标**

mybatis-spring版本需要与mybatis包版本对应，如mybatis的版本为3.5.6，那么mybatis-spring应为1.3.0，可能换一个版本就对不上了

```xml
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>
```

**温馨提示**

记得导入如下jar包坐标

```xml
    <!--spring-context要在spring-jdbc之前-->
	<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.6</version>
    </dependency>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.12</version>
      <scope>compile</scope>
    </dependency>
```

### 2.相关配置类

- 创建spring核心配置文件类（@Configuration用于声明当前类为Spring配置类）
  - @ComponentScan用于扫描路径内的配置类			2-1
  - @PropertySource用于加载properties的文件，文件内写的有数据库连接信息（如jdbc.username=xxx） 4-3
  - @Import用于加入配置类到核心配置，与ComponentScan功能一样，ComponentScan是自动，Import是手动，二选一即可（推荐import）     5-1

```java
@Configuration
//@ComponentScan("com.itheima")
//@PropertySource：加载类路径jdbc.properties文件
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MybatisConfig.class})
public class SpringConfig {
}
```

- 创建jdbc配置文件类，将数据库信息存入dataSource中并将dataSource设置为bean。
  - 由于前面使用了@Import注解，并将JdbcConfig.class注入，所以此处无需添加Configuration注解来声明该类为配置类

```java
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String userName;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(userName);
        ds.setPassword(password);

        return ds;
    }
}
```

- 创建Mybatis配置文件类，拿到sqlSessionFactory对象
  - 格式比较固定，面对不同的连接信息，仅仅只用修改ssfb.setTypeAliasesPackage与msc.setBasePackage内的信息即可
  - ssfb.setTypeAliasesPackage扫描类型别名的包，写入pojo类所在的包名 (如brand、user等与数据库对应的类)
  - msc.setBasePackage映射mapper对象，写入mapper(dao)类所在的包名进行扫描

```java
public class MybatisConfig {
    //定义bean，SqlSessionFactoryBean，用于产生SqlSessionFactory对象
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        ssfb.setTypeAliasesPackage("com.isias.pojo");
        ssfb.setDataSource(dataSource);
        return ssfb;
    }
    //定义bean，返回MapperScannerConfigurer对象
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.isias.mapper");
        return msc;
    }
}
```

**温馨提示**，不要忘了装载核心配置类

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
AccountService accountService = ctx.getBean(AccountService.class);
```



## 八、Spring整合Junit

@RunWith注解设置类运行器，后面括号内填SpringJUnit4ClassRunner.class，**格式固定**

@ContextConfiguration设置Spring环境对应的配置类，后面括号内填： classes =  对应的spring核心配置文件类

```java
@RunWith(SpringJUnit4ClassRunner.class)
//设置Spring环境对应的配置类
@ContextConfiguration(classes = SpringConfig.class)
public class AccountServiceTest {
    //支持自动装配注入bean
    @Autowired
    private BookService bookService;

    @Test
    public void testsave(){
        bookService.save();

    }
}
```

