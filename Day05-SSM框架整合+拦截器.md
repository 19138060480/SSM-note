# 一、SSM整合步骤

## 1.整合配置

[黑马相关视频](https://www.bilibili.com/video/BV1Fi4y1S7ix/?p=59&spm_id_from=pageDriver&vd_source=7dc3fcd6bfc310bdacafa3198220e160)

### 步骤1：创建Maven的web项目

使用Maven的骨架创建

### 步骤2:添加依赖

pom.xml添加SSM所需要的依赖jar包和Tomcat7插件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
    
	<!--创建maven项目自带的-->
  <groupId>xxx</groupId>
  <artifactId>xxx</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
<!--  <name>Genshin_ssmTest Maven Webapp</name>
  <url>http://maven.apache.org</url> -->	
    
<!--    依赖项-->
  <dependencies>
<!--    springmvc连接web(自带有spring-context)-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
<!--        spring连接jdbc-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
<!--        mybatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>
<!--        mybatis连接spring-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>
<!--        mysql连接java-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
<!--        不用阿里巴巴的fastjson了，fastjson速度快，但是bug也多，用jackson来传json数据-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
  </dependencies>

<!--    tomcat7插件，默认端口可以设为80-->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
  </build>

</project>
```

### 步骤3:完善并创建项目包结构

<img src=".\assets\image-20230111173913149.png" alt="image-20230111173913149" style="zoom:50%;" />

* config目录存放的是相关的配置类
* controller编写的是Controller类
* dao存放的是Dao接口，因为使用的是Mapper接口代理方式，所以没有实现类包
* service存的是Service接口，impl存放的是Service实现类
* resources:存入的是配置文件，如Jdbc.properties
* webapp:目录可以存放静态资源
* test/java:存放的是测试类

### 步骤4:创建SpringConfig配置类

```java
package com.isias.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration          //声明该类为配置类
@ComponentScan({"com.isias.service"})       //扫描service包
@PropertySource("classpath:jdbc.properties")         //扫描properties文件,一定要加上classpath:
@Import({JdbcConfig.class,MybatisConfig.class})     //导入jdbc和mybatis配置类
@EnableTransactionManagement            //开启Spring事务注解
public class SpringConfig {
}

```

### 步骤5:创建JdbcConfig配置类

```java
package com.isias.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import javax.sql.DataSource;

public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource dataSource=new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){    //配置事务管理器，mybatis使用的是jdbc事务 3-7-1
        DataSourceTransactionManager ds = new DataSourceTransactionManager();
        ds.setDataSource(dataSource);
        return ds;
    }
}

```

### 步骤6:创建MybatisConfig配置类

```java
package com.isias.config;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.context.annotation.Bean;
import javax.sql.DataSource;
public class MybatisConfig {    //2-7-2 spring整合mybatis
    @Bean       //定义bean，SqlSessionFactoryBean，用于产生SqlSessionFactory对象
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setTypeAliasesPackage("com.isias.pojo");        //存放实体类的包
        return factoryBean;
    }

    @Bean       //定义bean，返回MapperScannerConfigurer对象
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.isias.dao");
        return msc;
    }
}
```

### 步骤7:创建jdbc.properties

在resources下提供jdbc.properties,设置数据库连接四要素

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/数据库名称
jdbc.username=root
jdbc.password=703791321
```

### 步骤8:创建SpringMVC配置类

```java
package com.isias.config;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration          //声明该类为配置类
@ComponentScan("com.isias.controller")       //扫描controller包
@EnableWebMvc       //开启json数据类型自动转换    4-5-3 开启SpringMVC注解支持
public class SpringMvcConfig {
}
```

### 步骤9:创建Web项目入口配置类

```java
package com.isias.config;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;
import javax.servlet.Filter;

public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    //加载Spring配置类
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }
    //加载SpringMVC配置类
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }
    //设置SpringMVC请求地址拦截规则
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
    //设置post请求中文乱码过滤器
    @Override		//4-3-请求中文乱码
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("utf-8");
        return new Filter[]{filter};
    }
}
```

## 2.功能模块开发

### 步骤1:创建表（以模拟抽卡表举例）

```sql
create database Genshin character set utf8;
use Genshin;
create table draw_cards(
  id int primary key auto_increment,
  star int,
  goods varchar(50),
  description varchar(255)
)

insert  into draw_cards(star,goods,description) values (3,'神射手之誓','垃圾'),(4,'祭礼残章','2星辉'),(5,'雷电将军','影祭天下人')

SELECT * FROM draw_cards;
```

### 步骤2:编写模型类(pojo)

```java
package com.isias.pojo;

public class Card {
    private int id;
    private int star;
    private String goods;
    private String description;
	/*  	toString	*/
    @Override
    public String toString() {
        return "Card{" +
                "id=" + id +
                ", star=" + star +
                ", goods='" + goods + '\'' +
                ", description='" + description + '\'' +
                '}';
    }
	/*	 get/set	*/
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public int getStar() {
        return star;
    }
    public void setStar(int star) {
        this.star = star;
    }
    public String getGoods() {
        return goods;
    }
    public void setGoods(String goods) {
        this.goods = goods;
    }
    public String getDescription() {
        return description;
    }
    public void setDescription(String description) {
        this.description = description;
    }
}
```

### 步骤3:编写Dao接口(注解形式)

```java
package com.isias.dao;
import com.isias.pojo.Card;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import java.util.List;

public interface CardDao {

    @Select("SELECT * FROM draw_cards;")
    List<Card> selectAll();     //查询全部

    @Select("SELECT COUNT(id) from draw_cards;")
    int CountNum();     //查询数据数量

    @Insert("insert into draw_cards(star,goods,description) values (#{star},#{goods},#{description});")
    void Add(Card card);        //添加数据

    @Delete("delete from draw_cards;")
    void DeleteAll();       //删除全部数据
}
```

### 步骤4:编写Service接口和实现类

CardService接口:

```java
package com.isias.service;
import com.isias.pojo.Card;
import java.util.List;

public interface CardService {

    public List<Card> selectAll();

    public int CountNum();

    public Boolean Add(Card card);

    public Boolean DeleteAll();
}
```

CardServiceImpl实现类:

```java
package com.isias.service.impl;

import com.isias.dao.CardDao;
import com.isias.pojo.Card;
import com.isias.service.CardService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
@Transactional      //开启事物 3-7-1
@Service
public class CardServiceImpl implements CardService {
    @Autowired      //SpringMVC自动装配
    private CardDao cardDao;
    

//    public CardAndNum selectAll() {
//        List<Card> cards = cardDao.selectAll();
//        List<CardForShow> cardForShows=new ArrayList<CardForShow>();   //创建arrayliset数组存放要展示的cards
//        CardAndNum cardAndNum=new CardAndNum();         //用来存放list集合和全部数据
//        for (Card card : cards) {        //迭代器加入到cardForShows里(主要是把数据库的star数据数字变成字符串形式)
//            CardForShow cardForShow=new CardForShow();
//            cardForShow.setCard(card);
//            cardForShow.setCStar(card);
//            cardForShows.add(cardForShow);
//        }
//        int num = CountNum();
//        cardAndNum.setCardForShows(cardForShows);
//        cardAndNum.setSum(num);
//        return cardAndNum;
//    }

    public List<Card> selectAll() {
        List<Card> list = cardDao.selectAll();
        return list;
    }

    public int CountNum() {
        int num = cardDao.CountNum();
        return num;
    }

    public Boolean Add(Card card) {
        cardDao.Add(card);
        return true;
    }

    public Boolean DeleteAll() {
        cardDao.DeleteAll();
        return true;
    }
}
```

**说明:**

* cardDao在Service中注入的会提示一个红线提示，为什么呢?

  * CardDao是一个接口，没有实现类，接口是不能创建对象的，所以最终注入的应该是代理对象
  * 代理对象是由Spring的IOC容器来创建管理的
  * IOC容器又是在Web服务器启动的时候才会创建
  * IDEA在检测依赖关系的时候，没有找到适合的类注入，所以会提示错误提示
  * 但是程序运行的时候，代理对象就会被创建，框架会使用DI进行注入，所以程序运行无影响。
* 如何解决上述问题?

  * 可以不用理会，因为运行是正常的
  * 设置错误提示级别
  * <img src=".\assets\image-20230111215216543.png" alt="image-20230111215216543" style="zoom:50%;" />
  * <img src=".\assets\image-20230111215359804.png" alt="image-20230111215359804" style="zoom: 50%;" />



### 步骤5:编写Contorller类

```java
package com.isias.controller;
import com.isias.pojo.Card;
import com.isias.pojo.CardAndNum;
import com.isias.pojo.CardForShow;
import com.isias.service.CardService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/Cards")
public class CardController {
    @Autowired
    CardService cardService;

    @GetMapping
    public CardAndNum selectAll() {
        return cardService.selectAll();
    }
    
//    public int CountNum() {
//        return cardService.CountNum();
//    }

    @PostMapping
    public void Add(@RequestBody Card card) {
        cardService.Add(card);
    }
//    @DeleteMapping("/{id}")	//一般是通过id来删除的，但是我这个案例比较特殊，是删库
//    public boolean delete(@PathVariable Integer id) {		//(@PathVariable是为了接受{id}   4-8
//        return cardService.delete(id);
//    }
    @DeleteMapping
    public void DeleteAll() {
        cardService.DeleteAll();
    }
}
```

## 3.单元测试

### 步骤1:新建测试类

```java
package com.isias;

import com.isias.config.SpringConfig;
import com.isias.service.CardService;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class daoTest {
    @Autowired      //注入CardService
    private CardService cardService;

}
```

### 步骤2:编写测试方法

```java
package com.isias;
import com.isias.config.SpringConfig;
import com.isias.pojo.Card;
import com.isias.service.CardService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class daoTest {
    @Autowired      //注入CardService
    private CardService cardService;

    @Test
    public void selectAll(){
        CardAndNum cardAndNum = cardService.selectAll();
        System.out.println(cardAndNum);
    }

    @Test
    public void CountNum(){
        int num = cardService.CountNum();
        System.out.println(num);
    }
    
    @Test
    public void Add(){
        Card card=new Card();
        card.setStar(4);
        card.setGoods("香菱");
        card.setDescription("厨师");
        cardService.Add(card);
    }
    
    @Test
    public void DeleteAll(){
        cardService.DeleteAll();
    }
}
```

## 4.PostMan测试

```json
{
    "star":"5",
    "goods":"神里绫人",
    "description":"苍流踏花"
}
```

![image-20230112101957101](.\assets\image-20230112101957101.png)

# 二、统一结果封装

## 1.表现层与前端数据传输协议定义

目前我们就已经有三种数据类型返回给前端，如果随着业务的增长，我们需要返回的数据类型会越来越多。对于前端开发人员在解析数据的时候就比较凌乱了，所以对于前端来说，如果后台能够返回一个统一的数据结果，前端在解析的时候就可以按照一种方式进行解析。开发就会变得更加简单。

所以我们就想能不能将返回结果的数据进行统一，具体如何来做，大体的思路为:

* 为了封装返回的结果数据:**创建结果模型类，封装数据到data属性中**
* 为了封装返回的数据是何种操作及是否操作成功:**封装操作结果到code属性中**
* 操作失败后为了封装返回的错误信息:**封装特殊消息到message(msg)属性中**

![1630654293972](.\assets\1630654293972.png)

根据分析，我们可以设置统一数据返回结果类

```java
public class Result{
	private Object data;
	private Integer code;
	private String msg;
}
```

**注意:**Result类名及类中的字段并不是固定的，可以根据需要自行增减提供若干个构造方法，方便操作。

## 2.表现层与前端数据传输协议实现

### 结果封装

对于结果封装，我们应该是在表现层进行处理，所以我们把结果类放在controller包下，当然你也可以放在domain包，这个都是可以的，具体如何实现结果封装，具体的步骤为:

#### 步骤1:创建Result类

```java
package com.isias.controller;

public class Result {
    //描述统一格式中的编码，用于区分操作，可以简化配置0或1表示成功失败
    private int code;
    //描述统一格式中的数据
    private Object data;
    //描述统一格式中的消息，可选属性
    private String msg;
    //封装上数据库中的数据量
    private  int sum;
    //构造方法是方便对象的创建
    public Result() {
    }
    public Result(int code, Object data,int sum) {
        this.code = code;
        this.data = data;
        this.sum=sum;
    }
    public Result(int code, Object data, String msg,int sum) {
        this.code = code;
        this.data = data;
        this.msg = msg;
        this.sum=sum;
    }
	//setter...getter...省略
}
```

#### 步骤2:定义返回码Code类

```java
package com.isias.controller;
//状态码
public class Code {
    public static final Integer Select_OK=20011;
    public static final Integer Add_OK=20021;
    public static final Integer Delete_OK=20031;

    public static final Integer Select_ERR=20010;
    public static final Integer Add_ERR=20020;
    public static final Integer Delete_ERR=20030;

}
```

**注意:**code类中的常量设计也不是固定的，可以根据需要自行增减，例如将查询再进行细分为GET_OK,GET_ALL_OK,GET_PAGE_OK等。

#### 步骤3:修改Controller类的返回值

```java
//统一每一个控制器方法返回值
package com.isias.controller;

import com.isias.pojo.Card;
import com.isias.service.CardService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/Cards")
@Controller
public class CardController {
    @Autowired
    CardService cardService;

    @GetMapping
    public Result selectAll() {
        List<Card> cardList = cardService.selectAll();
        Integer code= cardList != null ? Code.Select_OK : Code.Select_ERR;//通过判断集合是否为空，来判断查询成功或失败
        String msg=cardList != null ? "" : "查询失败!";
        int sum=CountNum();
        return new Result(code,cardList,msg,sum);
    }
    public int CountNum() {
        return cardService.CountNum();
    }
    
    @PostMapping
    public Result Add(@RequestBody Card card) {
        Boolean flag = cardService.Add(card);
        Integer code=flag ? Code.Add_OK : Code.Add_ERR;
        return new Result(code,flag);
    }
   
    @DeleteMapping
    public Result DeleteAll() {
        Boolean flag = cardService.DeleteAll();
        Integer code=flag ? Code.Add_OK : Code.Add_ERR;
        Result result=new Result();
        result.setCode(code);
        return result;
    }
}
```

#### 步骤4:启动服务测试

<img src=".\assets\image-20230112122654376.png" alt="image-20230112122654376" style="zoom:50%;" />

<img src=".\assets\image-20230112122741678.png" alt="image-20230112122741678" style="zoom:50%;" />

# 三、统一异常处理

异常的种类及出现异常的原因:

- 框架内部抛出的异常：因使用不合规导致
- 数据层抛出的异常：因外部服务器故障导致（例如：服务器访问超时）
- 业务层抛出的异常：因业务逻辑书写错误导致（例如：遍历业务书写操作，导致索引异常等）
- 表现层抛出的异常：因数据收集、校验等规则导致（例如：不匹配的数据类型间导致异常）
- 工具类抛出的异常：因工具类书写不严谨不够健壮导致（例如：必要释放的连接长期未释放等）

看完上面这些出现异常的位置，你会发现，在我们开发的任何一个位置都有可能出现异常，而且这些异常是不能避免的。所以我们就得将异常进行处理。

**思考**

1. 各个层级均出现异常，异常处理代码书写在哪一层?

   **所有的异常均抛出到表现层进行处理**

2. 异常的种类很多，表现层如何将所有的异常都处理到呢?

   **异常分类**

3. 表现层处理异常，每个方法中单独书写，代码书写量巨大且意义不强，如何解决?

   **AOP**

对于上面这些问题及解决方案，SpringMVC已经为我们提供了一套解决方案:

* 异常处理器:

  * 集中的、统一的处理项目中出现的异常。

    ![1630657791653](.\assets\1630657791653.png)

### 1.异常处理器的使用

#### 步骤1:创建异常处理器类

```java
package com.isias.controller;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
//@RestControllerAdvice用于标识当前类为REST风格对应的异常处理器
@RestControllerAdvice
public class ProjectExceptionAdvice {
    //除了自定义的异常处理器，保留对Exception类型的异常处理，用于处理非预期的异常
    @ExceptionHandler(Exception.class)
    public void doExpection(Exception exception){
        System.out.println("任何异常，终将绳之以法");
    }
}

```

**确保SpringMvcConfig能够扫描到异常处理器类**

#### 步骤2:让程序抛出异常

修改`CardController`的selectAll方法，添加`int i = 1/0`.

```java
    @GetMapping
    public Result selectAll() {
        int i=1/0;
        List<Card> cardList = cardService.selectAll();
        Integer code= cardList != null ? Code.Select_OK : Code.Select_ERR;
        String msg=cardList != null ? "" : "查询失败!";
        int sum=CountNum();
        return new Result(code,cardList,msg,sum);
    }
```

#### 步骤3:运行程序，测试

<img src=".\assets\image-20230112163741254.png" alt="image-20230112163741254" style="zoom:50%;" />

说明异常已经被拦截并执行了`doException`方法。

##### 异常处理器类返回结果给前端

```java
package com.isias.controller;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

//@RestControllerAdvice用于标识当前类为REST风格对应的异常处理器
@RestControllerAdvice
public class ProjectExceptionAdvice {
    //除了自定义的异常处理器，保留对Exception类型的异常处理，用于处理非预期的异常
    @ExceptionHandler(Exception.class)
    public Result doExpection(Exception exception){
        System.out.println("任何异常，终将绳之以法");
        return new Result(666,null,"任何异常，终将绳之以法");
    }
}
```

启动运行程序，测试

<img src=".\assets\image-20230112163926431.png" alt="image-20230112163926431" style="zoom:50%;" />

至此，就算后台执行的过程中抛出异常，最终也能按照我们和前端约定好的格式返回给前端。

#### 知识点1：@RestControllerAdvice

| 名称 | @RestControllerAdvice              |
| ---- | ---------------------------------- |
| 类型 | **类注解**                         |
| 位置 | Rest风格开发的控制器增强类定义上方 |
| 作用 | 为Rest风格开发的控制器类做增强     |

**说明:**此注解自带**@ResponseBody**注解与**@Component**注解，具备对应的功能

![1630659060451](.\assets\1630659060451.png)

#### 知识点2：@ExceptionHandler

| 名称 | @ExceptionHandler                                            |
| ---- | ------------------------------------------------------------ |
| 类型 | **方法注解**                                                 |
| 位置 | 专用于异常处理的控制器方法上方                               |
| 作用 | 设置指定异常的处理方案，功能等同于控制器方法，<br/>出现异常后终止原始控制器执行,并转入当前方法执行 |

**说明：**此类方法可以根据处理的异常不同，制作多个方法分别处理对应的异常

## 2.项目异常处理方案

#### 异常分类

因为异常的种类有很多，如果每一个异常都对应一个@ExceptionHandler，那得写多少个方法来处理各自的异常，所以我们在处理异常之前，需要对异常进行一个分类:

- 业务异常（BusinessException）

  - 规范的用户行为产生的异常

    - 用户在页面输入内容的时候未按照指定格式进行数据填写，如在年龄框输入的是字符串

      ![1630659599983](.\assets\1630659599983.png)

  - 不规范的用户行为操作产生的异常

    - 如用户故意传递错误数据

      ![1630659622958](.\assets\1630659622958.png)

- 系统异常（SystemException）

  - 项目运行过程中可预计但无法避免的异常
    - 比如数据库或服务器宕机

- 其他异常（Exception）

  - 编程人员未预期到的异常，如:用到的文件不存在

    ![1630659690341](.\assets\1630659690341.png)

将异常分类以后，针对不同类型的异常，要提供具体的解决方案:

#### 异常解决方案

- 业务异常（BusinessException）
  - 发送对应消息传递给用户，提醒规范操作
    - 大家常见的就是提示用户名已存在或密码格式不正确等
- 系统异常（SystemException）
  - 发送固定消息传递给用户，安抚用户
    - 系统繁忙，请稍后再试
    - 系统正在维护升级，请稍后再试
    - 系统出问题，请联系系统管理员等
  - 发送特定消息给运维人员，提醒维护
    - 可以发送短信、邮箱或者是公司内部通信软件
  - 记录日志
    - 发消息和记录日志对用户来说是不可见的，属于后台程序
- 其他异常（Exception）
  - 发送固定消息传递给用户，安抚用户
  - 发送特定消息给编程人员，提醒维护（纳入预期范围内）
    - 一般是程序没有考虑全，比如未做非空校验等
  - 记录日志

#### 异常解决方案的具体实现

> 思路:
>
> 1.先通过自定义异常，完成BusinessException和SystemException的定义
>
> 2.将其他异常包装成自定义异常类型
>
> 3.在异常处理器类中对不同的异常进行处理

##### 步骤1:自定义异常类

自定义异常处理器，用于封装异常信息，对异常进行分类

<img src=".\assets\image-20230112164840526.png" alt="image-20230112164840526" style="zoom:70%;" />

```java
package com.isias.Exception;
public class SystemException extends RuntimeException{
    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public SystemException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }
}
```

```java
package com.isias.Exception;

public class BusinessException extends RuntimeException{
    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public BusinessException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public BusinessException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }
}
```

**说明:**

* 让自定义异常类继承`RuntimeException`的好处是，后期在抛出这两个异常的时候，就不用在try...catch...或throws了
* 自定义异常类中添加`code`属性的原因是为了更好的区分异常是来自哪个业务的

##### 步骤2:将其他异常包成自定义异常

假如在CardkServiceImpl的selectAll方法抛异常了，该如何来包装呢?

在Code类中再新增需要的属性

```java
//状态码
public class Code {
    public static final Integer Select_OK=20011;
    public static final Integer Add_OK=20021;
    public static final Integer Delete_OK=20031;

    public static final Integer Select_ERR=20010;
    public static final Integer Add_ERR=20020;
    public static final Integer Delete_ERR=20030;
    
    //异常信息
    public static final Integer SYSTEM_ERR=50011;
    public static final Integer BUSSINESS_ERR=60099;
    public static final Integer OTHER_ERR=99999;

}

```

##### 

```java
    public List<Card> selectAll() {
        int id=1;
        //模拟业务异常，包装成自定义异常
        if(id == 1){
            throw new BusinessException(Code.BUSSINESS_ERR,"请不要使用你的技术挑战我的耐性!");
        }
        //模拟系统异常，将可能出现的异常进行包装，转换成自定义异常
        try{
            int i = 1/0;
        }catch (Exception e){
            throw new SystemException(Code.SYSTEM_ERR,"服务器访问超时，请重试!",e);
        }
        List<Card> list = cardDao.selectAll();
        return list;
    }
```

具体的包装方式有：

* 方式一:`try{}catch(){}`在catch中重新throw我们自定义异常即可。
* 方式二:直接throw自定义异常即可

##### 步骤3:处理器类中处理自定义异常

```java
package com.isias.controller;

import com.isias.Exception.BusinessException;
import com.isias.Exception.SystemException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

//@RestControllerAdvice用于标识当前类为REST风格对应的异常处理器
@RestControllerAdvice
public class ProjectExceptionAdvice {
    @ExceptionHandler(SystemException.class)
    public Result SystemExpection(SystemException e){
        //记录日志
        //发送消息给运维
        //发送邮件给开发人员,ex对象发送给开发人员
        return new Result(e.getCode(),null,e.getMessage());
    }

    @ExceptionHandler(BusinessException.class)
    public Result BusinessException(BusinessException e){
        return new Result(Code.BUSSINESS_ERR,null,e.getMessage());
    }

    //除了自定义的异常处理器，保留对Exception类型的异常处理，用于处理非预期的异常
    @ExceptionHandler(Exception.class)
    public Result OtherException(){
        //记录日志
        //发送消息给运维
        //发送邮件给开发人员,ex对象发送给开发人员
        return new Result(Code.OTHER_ERR,null,"任何异常，终将绳之以法！");
    }
}
```

##### 步骤4:运行程序

直接发送get请求，报错业务异常

<img src=".\assets\image-20230112171114733.png" alt="image-20230112171114733" style="zoom:50%;" />

将模拟的业务异常注释，重启服务器，再次发送get请求，模拟系统异常

<img src=".\assets\image-20230112171203389.png" alt="image-20230112171203389" style="zoom:50%;" />

<img src=".\assets\image-20230112171237507.png" alt="image-20230112171237507" style="zoom:50%;" />

对于异常我们就已经处理完成了，不管后台哪一层抛出异常，都会以我们与前端约定好的方式进行返回，前端只需要把信息获取到，根据返回的正确与否来展示不同的内容即可。

**小结**

以后项目中的异常处理方式为:

![1630658821746](.\assets\1630658821746.png)

# 四、前后台协议联调

1.前端建设

<img src=".\assets\image-20230112171950864.png" alt="image-20230112171950864" style="zoom:70%;" />

2.因为添加了静态资源，SpringMVC会拦截，所有需要在SpringConfig的配置类中将静态资源进行放行。

* 新建SpringMvcSupport

  ```java
  package com.isias.config;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
  @Configuration
  public class SpringMvcSupport extends WebMvcConfigurationSupport {
  
      @Override
      protected void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
          registry.addResourceHandler("/css/**").addResourceLocations("/css/");
          registry.addResourceHandler("/js/**").addResourceLocations("/js/");
          registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
      }
  }
  ```

* 在SpringMvcConfig中扫描SpringMvcSupport

  ```java
  package com.isias.config;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.EnableWebMvc;
  @Configuration
  @ComponentScan({"com.isias.controller","com.isias.config"})
  @EnableWebMvc
  public class SpringMvcConfig {
  }
  ```

后端的开发到此结束，接下来就是与前端页面的绑定；



# 五、拦截器Interceptor

## 1.拦截器概念

![1630676280170](.\assets\1630676280170.png)

(1)浏览器发送一个请求会先到Tomcat的web服务器

(2)Tomcat服务器接收到请求以后，会去判断请求的是静态资源还是动态资源

(3)如果是静态资源，会直接到Tomcat的项目部署目录下去直接访问

(4)如果是**动态资源**，就需要交给项目的后台代码进行处理

(5)在找到具体的方法之前，我们可以去配置**过滤器**(可以配置多个)，按照顺序进行执行

(6)**然后进入到到中央处理器(SpringMVC中的内容)**，SpringMVC会根据配置的规则进行拦截

(7)如果满足规则，则进行处理，找到其对应的controller类中的方法进行执行,完成后返回结果

(8)如果不满足规则，则不进行处理

(9)这个时候，如果我们需要在每个Controller方法执行的前后添加业务，具体该如何来实现?

这个就是拦截器要做的事。

* 拦截器（Interceptor）是一种动态拦截方法调用的机制，在SpringMVC中动态拦截控制器方法的执行
* 作用:
  * 在指定的方法调用前后执行预先设定的代码
  * 阻止原始方法的执行
  * 总结：**拦截器就是用来做增强**

看完以后，大家会发现

* 拦截器和过滤器在作用和执行顺序上也很相似

所以这个时候，就有一个问题需要思考:拦截器和过滤器之间的区别是什么?

- **归属不同：Filter属于Servlet技术，Interceptor属于SpringMVC技术**
- **拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强**

![1630676903190](.\assets\1630676903190.png)

## 2.拦截器开发

### 步骤1:创建拦截器类

让类实现**HandlerInterceptor**接口，重写接口中的三个方法。



```java
@Component		//注意当前类必须受Spring容器控制
//定义拦截器类，实现HandlerInterceptor接口
public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    //原始方法调用前执行的内容
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        return true;		//如果为true则放行，如果为false，则不放行；返回false就相当于把第一扇门关上了，后面的门也访问不了
    }

    @Override
    //原始方法调用后执行的内容
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }

    @Override
    //原始方法调用完成后执行的内容
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```

**注意:**拦截器类要被SpringMVC容器扫描到（SpringMvcConfig中扫描拦截器所在的包（**建议在controller包下新建Interceptor包，把拦截器放在里面**））。

### 步骤2:配置拦截器类

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {//与之前配置SpringMvc不要扫描到静态页面的接口一样
    @Autowired	//自动装配拦截器，为了给下面的addInterceptor配置拦截器
    private ProjectInterceptor projectInterceptor;

    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        //配置拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books" );//这里只拦截了访问/books这一个路径，步骤五有写配置多路径的
    }
}
```

### 步骤3:SpringMVC添加SpringMvcSupport包扫描

```java
@Configuration
@ComponentScan({"com.isias.controller","com.isias.config"})	//为了扫描到SpringMvcSupport
@EnableWebMvc
public class SpringMvcConfig{
   
}
```

### 步骤4:运行程序测试

使用PostMan发送`http://localhost/books`

![1630678114224](.\assets\1630678114224.png)

如果发送`http://localhost/books/100`会发现拦截器没有被执行，原因是拦截器的`addPathPatterns`方法配置的拦截路径是`/books`,我们现在发送的是`/books/100`，所以没有匹配上，因此没有拦截，拦截器就不会执行。

### 步骤5:修改拦截器拦截规则(配置多路径)

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        //配置拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books","/books/*" );	//可以配置多个路径
    }
}
```

这个时候，如果再次访问`http://localhost/books/100`，拦截器就会被执行。

最后说一件事，就是拦截器中的`preHandler`方法，如果返回true,则代表放行，会执行原始Controller类中要请求的方法，如果返回false，则代表拦截，后面的就不会再执行了。

### 步骤6:简化SpringMvcSupport的编写

直接卸载SpringMvcConfig配置类里，让配置类实现WebMvcConfigurer接口就可以直接配置拦截器了

```java
@Configuration
@ComponentScan({"com.isias.controller"})
@EnableWebMvc
//实现WebMvcConfigurer接口可以简化开发，但具有一定的侵入性,与SpringMVC容器强绑定了
public class SpringMvcConfig implements WebMvcConfigurer {
    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //配置多拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books","/books/*");
    }
}
```

此后咱们就不用再写`SpringMvcSupport`类了。

最后我们来看下拦截器的执行流程:

![1630679464294](.\assets\1630679464294.png)

当有拦截器后，请求会先进入preHandle方法，

​	如果方法返回true，则放行继续执行后面的handle[controller的方法]和后面的方法

​	如果返回false，则直接跳过后面方法的执行。

## 3.拦截器参数

#### 前置处理方法

原始方法之前运行preHandle

```java
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {
    System.out.println("preHandle");
    return true;
}
```

* request:请求对象
* response:响应对象
* handler:被调用的处理器对象，本质上是一个方法对象，对反射中的Method对象进行了再包装

使用request对象可以获取请求数据中的内容，如获取请求头的`Content-Type`

```java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    String contentType = request.getHeader("Content-Type");
    System.out.println("preHandle..."+contentType);
    return true;
}
```

使用handler参数，可以获取方法的相关信息

```java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    HandlerMethod hm = (HandlerMethod)handler;
    String methodName = hm.getMethod().getName();//可以获取方法的名称
    System.out.println("preHandle..."+methodName);
    return true;
}
```

#### 后置处理方法

原始方法运行后运行，如果原始方法被拦截，则不执行  

```java
public void postHandle(HttpServletRequest request,
                       HttpServletResponse response,
                       Object handler,
                       ModelAndView modelAndView) throws Exception {
    System.out.println("postHandle");
}
```

前三个参数和上面的是一致的。

modelAndView:如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整

因为咱们现在都是返回json数据，所以该参数的使用率不高。

#### 完成处理方法

拦截器最后执行的方法，无论原始方法是否执行

```java
public void afterCompletion(HttpServletRequest request,
                            HttpServletResponse response,
                            Object handler,
                            Exception ex) throws Exception {
    System.out.println("afterCompletion");
}
```

前三个参数与上面的是一致的。

ex:如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理  

因为我们现在已经有全局异常处理器类，所以该参数的使用率也不高。

这三个方法中，最常用的是**preHandle**,在这个方法中可以通过返回值来决定是否要进行放行，我们可以把业务逻辑放在该方法中，如果满足业务则返回true放行，不满足则返回false拦截。

## 拦截器链配置

目前，我们在项目中只添加了一个拦截器，如果有多个，该如何配置?配置多个后，执行顺序是什么?

#### 配置多个拦截器

##### 步骤1:创建拦截器类

实现接口，并重写接口中的方法

```java
@Component
public class ProjectInterceptor2 implements HandlerInterceptor {	//拦截器二号
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...222");
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...222");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...222");
    }
}
```

##### 步骤2:配置拦截器类

```java
@Configuration
@ComponentScan({"com.isias.controller"})
@EnableWebMvc
//实现WebMvcConfigurer接口可以简化开发，但具有一定的侵入性
public class SpringMvcConfig implements WebMvcConfigurer {
    @Autowired
    private ProjectInterceptor projectInterceptor;
    @Autowired
    private ProjectInterceptor2 projectInterceptor2;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //配置多拦截器
        registry.addInterceptor(projectInterceptor).addPathPatterns("/books","/books/*");//拦截器一号
        registry.addInterceptor(projectInterceptor2).addPathPatterns("/books","/books/*");//拦截器二号
    }
}
```

步骤3:运行程序，观察顺序

![1630680435269](.\assets\1630680435269.png)

拦截器**执行的顺序是和配置顺序有关**。就和前面所提到的运维人员进入机房的案例，先进后出。

* 当配置多个拦截器时，形成拦截器链
* 拦截器链的运行顺序参照拦截器添加顺序为准
* 当拦截器中出现对原始处理器的拦截，后面的拦截器均终止运行
* 当拦截器运行中断，仅运行配置在前面的拦截器的afterCompletion操作

![1630680579735](.\assets\1630680579735.png)

preHandle：与配置顺序相同，必定运行

postHandle:与配置顺序相反，可能不运行

afterCompletion:与配置顺序相反，可能不运行。

这个顺序不太好记，最终只需要把握住一个原则即可:**以最终的运行结果为准**