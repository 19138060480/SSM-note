# 一、SpringBoot快速入门

## Ⅰ.开发步骤：

### 步骤1：创建新模块:

点击新建模块

<img src="assets\image-20230115100827241.png" alt="image-20230115100827241" style="zoom: 33%;" />



选择**Spring Initializr**模板进行创建，名称随便起，类型要选Maven，jdk和java都是8，打包方式选jar，位置随便写，然后点击下一步.

<img src=" assets\image-20230115101119700.png" alt="image-20230115101119700" style="zoom: 45%;" />

这里选择这个**Web下的Spring Web**，Spring Boot版本应该是随便，视频里演示的是2.5.0，但是我最低版本就是2.7,所以我选择3.0.1

选择完毕后点击创建.

<img src=" assets\image-20230115101329368.png" alt="image-20230115101329368" style="zoom: 45%;" />

创建完以后大概长这个样子，.gitignore、HELP.md、mvnw、mvnw.cmd这四个文件可以选择删除掉，如果pom.xml显示的是黄色说明idea没有导入这个maven模块，需要手动导入一下。

<img src=" assets\image-20230115101646664.png" alt="image-20230115101646664" style="zoom:50%;" />

### 步骤2：创建 `Controller`

在  `com.isias.controller` 包下创建 `CardController` ，代码如下：

<img src=" assets\image-20230115103632348.png" alt="image-20230115103632348" style="zoom:50%;" />

```java
package com.isias.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/Cards")
public class CardController {
    @GetMapping("/{id}")
    public String SelectCardById(@PathVariable Integer id){
        System.out.println("id is :"+id);
        return "SelectCardById has been used";
    }
}
```

### 步骤3：启动服务器

运行 `SpringBoot` 工程不需要使用本地的 `Tomcat` 和 插件，只运行项目 `com.isias` 包下的 `SpringbootTestApplication` 类，我们就可以在控制台看出如下信息

![image-20230115104324135]( assets\image-20230115104324135.png)

### 步骤4：Postman进行测试

<img src=" assets\image-20230115104626241.png" alt="image-20230115104626241" style="zoom:40%;" />

控制台输出:

<img src=" assets\image-20230115104650444.png" alt="image-20230115104650444" style="zoom:40%;" />



通过上面的入门案例我们可以看到使用 `SpringBoot` 进行开发，使整个开发变得很简单，那它是如何做到的呢？

要研究这个问题，我们需要看看 `Application` 类和 `pom.xml` 都书写了什么。先看看 `Applicaion` 类，该类内容如下：

```java
@SpringBootApplication
public class SpringbootTestApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringbootTestApplication.class, args);
	}
}
```

这个类中的东西很简单，就在类上添加了一个 `@SpringBootApplication` 注解，而在主方法中就一行代码。我们在启动服务器时就是执行的该类中的主方法。

再看看 `pom.xml` 配置文件中的内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <!--指定了一个父工程，父工程中的东西在该工程中可以继承过来使用-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
    </parent>
    <groupId>com.itheima</groupId>
    <artifactId>springboot_01_quickstart</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!--JDK 的版本-->
    <properties>
        <java.version>8</java.version>
    </properties>
    
    <dependencies>
        <!--该依赖就是我们在创建 SpringBoot 工程勾选的那个 Spring Web 产生的-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
		<!--这个是单元测试的依赖，我们现在没有进行单元测试，所以这个依赖现在可以没有-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--这个插件是在打包时需要的，而这里暂时还没有用到-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

我们代码之所以能简化，就是因为指定的父工程和 `Spring Web` 依赖实现的。

### 对比

对比一下 `Spring` 程序和 `SpringBoot` 程序

![image-20230115105447239]( assets\image-20230115105447239.png)

* **坐标**

  `Spring` 程序中的坐标需要自己编写，而且坐标非常多

  `SpringBoot` 程序中的坐标是我们在创建工程时进行勾选自动生成的

* **web3.0配置类**

  `Spring` 程序需要自己编写这个配置类。这个配置类大家之前编写过，肯定感觉很复杂

  `SpringBoot` 程序不需要我们自己书写

* **配置类**

  `Spring/SpringMVC` 程序的配置类需要自己书写。而 `SpringBoot`  程序则不需要书写。

> **注意：基于Idea的 `Spring Initializr` 快速构建 `SpringBoot` 工程时需要联网。**

## Ⅱ.官网构建工程

### 1.进入SpringBoot官网

到官网以后点击Spring Boot

<img src=" assets\image-20230115112705238.png" alt="image-20230115112705238" style="zoom:50%;" />

### 2.选择Spring Initializr

拉到最下面，点击Quickstart Your Project里面的**Spring Initializr.**

<img src=" assets\image-20230115112754587.png" alt="image-20230115112754587" style="zoom:50%;" />

### 3.配置项目名称等，并选择依赖

进入到这个界面，与idea界面一样（颜色不一样），点击左边配置完以后点击右边的的ADD DEPENDENCIES，搜索Spring Web，第一个就是要创建的web工程

<img src=" assets\image-20230115112958403.png" alt="image-20230115112958403" style="zoom:50%;" />

<img src=" assets\image-20230115113134632.png" alt="image-20230115113134632" style="zoom:50%;" />

### 3.生成工程

以上步骤完成后就可以生成 `SpringBoot` 工程了。在页面的最下方点击 `GENERATE CTRL + 回车` 按钮生成工程并下载到本地，如下图所示

![image-20210911175222857]( assets\image-20210911175222857.png)

打开下载好的压缩包可以看到工程结构和使用 `Idea` 生成的一模一样

## Ⅲ.SpringBoot工程快速启动

我们后端可以将 `SpringBoot` 工程打成 `jar` 包，该 `jar` 包运行不依赖于 `Tomcat` 和 `Idea` 这些工具也可以正常运行，只是这个 `jar` 包在运行过程中连接和我们自己程序相同的 `Mysql` 数据库即可

### 步骤如下：

#### 1.打包

由于我们在构建 `SpringBoot` 工程时已经在 `pom.xml` 中配置了如下插件

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

所以我们只需要使用 `Maven` 的 `package` 指令打包就会在 `target` 目录下生成对应的 `Jar` 包。

> **注意：该插件必须配置，不然打好的 `jar` 包也是有问题的。**

#### 2.启动

进入 `jar` 包所在位置，在 `命令提示符` 中输入如下命令

```shell
jar -jar springboot_01_quickstart-0.0.1-SNAPSHOT.jar
```

执行上述命令就可以看到 `SpringBoot` 运行的日志信息

<img src=" assets\image-20210911182956629.png" alt="image-20210911182956629" style="zoom:60%;" />

## Ⅳ.SpringBoot概述

`SpringBoot` 是由Pivotal团队提供的全新框架，其设计目的是用来**简化**Spring应用的**初始搭建**以及**开发过程**。

大家已经感受了 `SpringBoot` 程序，回过头看看 `SpringBoot` 主要作用是什么，就是简化 `Spring` 的搭建过程和开发过程。

原始 `Spring` 环境搭建和开发存在以下问题：

* 配置繁琐
* 依赖设置繁琐

`SpringBoot` 程序优点恰巧就是针对 `Spring` 的缺点

* 自动配置。这个是用来解决 `Spring` 程序配置繁琐的问题
* 起步依赖。这个是用来解决 `Spring` 程序依赖设置繁琐的问题
* 辅助功能（内置服务器,...）。我们在启动 `SpringBoot` 程序时既没有使用本地的 `tomcat` 也没有使用 `tomcat` 插件，而是使用 `SpringBoot` 内置的服务器。

接下来我们来说一下 `SpringBoot` 的起步依赖

### 1. 起步依赖

我们使用 `Spring Initializr`  方式创建的 `Maven` 工程的的 `pom.xml` 配置文件中自动生成了很多包含 `starter` 的依赖，如下图

<img src=" assets\image-20210918220338109.png" alt="image-20210918220338109" style="zoom:70%;" />

这些依赖就是**启动依赖**，接下来我们探究一下他是如何实现的。

#### 探索父工程

从上面的文件中可以看到指定了一个父工程，我们进入到父工程，发现父工程中又指定了一个父工程，如下图所示

<img src=" assets\image-20210918220855024.png" alt="image-20210918220855024" style="zoom:80%;" />

再进入到该父工程中，在该工程中我们可以看到配置内容结构如下图所示

<img src=" assets\image-20210918221042947.png" alt="image-20210918221042947" style="zoom:80%;" />

上图中的 `properties` 标签中定义了各个技术软件依赖的版本，避免了我们在使用不同软件技术时考虑版本的兼容问题。在 `properties` 中我们找 `servlet`  和 `mysql` 的版本如下图

<img src=" assets\image-20210918221511249.png" alt="image-20210918221511249" style="zoom:80%;" />

`dependencyManagement` 标签是进行依赖版本锁定，但是并没有导入对应的依赖；如果我们工程需要那个依赖只需要引入依赖的 `groupid` 和 `artifactId` 不需要定义 `version`。

而 `build` 标签中也对插件的版本进行了锁定，如下图

<img src=" assets\image-20210918221942453.png" alt="image-20210918221942453" style="zoom:80%;" />

看完了父工程中 `pom.xml` 的配置后不难理解我们工程的的依赖为什么都没有配置 `version`。

#### 探索依赖

在我们创建的工程中的 `pom.xml` 中配置了如下依赖

<img src=" assets\image-20210918222321402.png" alt="image-20210918222321402" style="zoom:80%;" />

进入到该依赖，查看 `pom.xml` 的依赖会发现它引入了如下的依赖

<img src=" assets\image-20210918222607469.png" alt="image-20210918222607469" style="zoom:80%;" />

里面的引入了 `spring-web` 和 `spring-webmvc` 的依赖，这就是为什么我们的工程中没有依赖这两个包还能正常使用 `springMVC` 中的注解的原因。

而依赖 `spring-boot-starter-tomcat` ，从名字基本能确认内部依赖了 `tomcat`，所以我们的工程才能正常启动。

**结论：以后需要使用技术，只需要引入该技术对应的起步依赖即可**

#### 小结

**starter**

* `SpringBoot` 中常见项目名称，定义了当前项目使用的所有项目坐标，以达到减少依赖配置的目的

**parent**

* 所有 `SpringBoot` 项目要继承的项目，定义了若干个坐标版本号（依赖管理，而非依赖），以达到减少依赖冲突的目的

* `spring-boot-starter-parent`（2.5.0）与 `spring-boot-starter-parent`（2.4.6）共计57处坐标版本不同

**实际开发**

* 使用任意坐标时，仅书写GAV中的G和A，V由SpringBoot提供

  > G：groupid
  >
  > A：artifactId
  >
  > V：version

* 如发生坐标错误，再指定version（要小心版本冲突）

### 程序启动

创建的每一个 `SpringBoot` 程序时都包含一个类似于下面的类，我们将这个类称作引导类

```java
@SpringBootApplication
public class Springboot01QuickstartApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(Springboot01QuickstartApplication.class, args);
    }
}
```

**注意：**

* `SpringBoot` 在创建项目时，采用jar的打包方式

* `SpringBoot` 的引导类是项目的入口，运行 `main` 方法就可以启动项目

  因为我们在 `pom.xml` 中配置了 `spring-boot-starter-web` 依赖，而该依赖通过前面的学习知道它依赖 `tomcat` ，所以运行 `main` 方法就可以使用 `tomcat` 启动咱们的工程。

### 切换web服务器

现在我们启动工程使用的是 `tomcat` 服务器，那能不能不使用 `tomcat` 而使用 `jetty` 服务器，`jetty` 在我们 `maven` 高级时讲 `maven` 私服使用的服务器。而要切换 `web` 服务器就需要将默认的 `tomcat` 服务器给排除掉，怎么排除呢？使用 `exclusion` 标签

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!--排除掉tomcat服务器依赖-->
    <exclusions>
        <exclusion>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <groupId>org.springframework.boot</groupId>
        </exclusion>
    </exclusions>
</dependency>
```

现在我们运行引导类可以吗？运行一下试试，打印的日志信息如下

![image-20210918232512707]( assets\image-20210918232512707.png)

程序直接停止了，为什么呢？那是因为排除了 `tomcat` 服务器，程序中就没有服务器了。所以此时不光要排除 `tomcat` 服务器，还要引入 `jetty` 服务器。在 `pom.xml` 中因为 `jetty` 的起步依赖

```xml
<!--新建一个依赖项，写入jetty的坐标，springboot会帮我们自动导入其版本-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

接下来再次运行引导类，在日志信息中就可以看到使用的是 `jetty` 服务器

![image-20210918232904623]( assets\image-20210918232904623.png)

**小结：**

通过切换服务器，我们不难发现在使用 `SpringBoot` 换技术时只需要导入该技术的起步依赖即可。

# 二、配置文件

## Ⅰ.配置文件格式

我们现在启动服务器默认的端口号是 `8080`，访问路径可以书写为

```
http://localhost:8080/books/1
```

在线上环境我们还是希望将端口号改为 `80`，这样在访问的时候就可以不写端口号了，如下

```
http://localhost/books/1
```

而 `SpringBoot` 程序如何修改呢？`SpringBoot` 提供了多种属性配置方式

* `application.properties`

  ```
  server.port=80
  ```

* `application.yml`

  ```yaml
  server:
  	port: 80
  ```

* `application.yaml`

  ```yaml
  server:
  	port: 80
  ```

> **注意：`SpringBoot` 程序的配置文件名必须是 `application` ，只是后缀名不同而已。**

### 三种配合文件的优先级

**最常用的是.yml格式**，如果三个都写，那么application.properties>application.yml>application.yaml

> **注意：**
>
> * `SpringBoot` 核心配置文件名为 `application`
>
> * `SpringBoot` 内置属性过多，且所有属性集中在一起修改，在使用时，通过提示键+关键字修改属性
>
>   例如要设置日志的级别时，可以在配置文件中书写 `logging`，就会提示出来。配置内容如下
>
>   ```yaml
>   logging:
>     level:
>       root: info
>   ```

### 

### 解决无提示方法

**在配合文件中如果没有提示，可以使用一下方式解决**

* 点击 `File` 选中 `Project Structure`

<img src=" assets\image-20210917163557071.png" alt="image-20210917163557071" style="zoom:80%;" />

* 弹出如下窗口，按图中标记红框进行选择

<img src=" assets\image-20210917163736458.png" alt="image-20210917163736458" style="zoom:70%;" />

* 通过上述操作，会弹出如下窗口

<img src=" assets\image-20210917163818051.png" alt="image-20210917163818051" style="zoom:80%;" />

* 点击上图的 `+` 号，弹出选择该模块的配置文件

<img src=" assets\image-20210917163828518.png" alt="image-20210917163828518" style="zoom:80%;" />

* 通过上述几步后，就可以看到如下界面。`properties` 类型的配合文件有一个，`ymal` 类型的配置文件有两个

<img src=" assets\image-20210917163846243.png" alt="image-20210917163846243" style="zoom:80%;" />

## Ⅱ.yaml格式

**YAML（YAML Ain't Markup Language），一种数据序列化格式。**

**优点：**

* 容易阅读

  `yaml` 类型的配置文件比 `xml` 类型的配置文件更容易阅读，结构更加清晰

* 容易与脚本语言交互

* 以数据为核心，重数据轻格式

  `yaml` 更注重数据，而 `xml` 更注重格式

**YAML 文件扩展名：**

* `.yml` (主流)
* `.yaml`

上面两种后缀名都可以，以后使用更多的还是 `yml` 的。

### 1.语法规则

* 大小写敏感

* 属性层级关系使用多行描述，每行结尾使用冒号结束

* 使用缩进表示层级关系，同层级左侧对齐，只允许使用空格（不允许使用Tab键）

  空格的个数并不重要，只要保证同层级的左侧对齐即可。

* 属性值前面添加空格（属性名与属性值之间使用冒号+空格作为分隔）

* \# 表示注释

**核心规则：数据前面要加空格与冒号隔开 **

数组数据在数据书写位置的下方使用减号作为数据开始符号，每行书写一个数据，减号与数据间空格分隔，例如

```yaml
enterprise:
  name: itcast
  age: 16
  tel: 4006184000
  subject:
    - Java
    - 前端
    - 大数据
```

### 2.yaml配置文件数据读取

#### 1.使用 @Value注解

使用 `@Value("表达式")` 注解可以从配合文件中读取数据，注解中用于读取属性名引用方式是：`${一级属性名.二级属性名……}`

我们可以在 `BookController` 中使用 `@Value`  注解读取配合文件数据，如下

```java
@RestController
@RequestMapping("/books")
public class BookController {
    
    @Value("${lesson}")
    private String lesson;
    @Value("${server.port}")
    private Integer port;
    @Value("${enterprise.subject[0]}")
    private String subject_00;

    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id){
        System.out.println(lesson);
        System.out.println(port);
        System.out.println(subject_00);
        return "hello , spring boot!";
    }
}
```

#### 2.Environment对象(不常用)

上面方式读取到的数据特别零散，`SpringBoot` 还可以使用 `@Autowired` 注解注入 `Environment` 对象的方式读取数据。这种方式 `SpringBoot` 会将配置文件中所有的数据封装到 `Environment` 对象中，如果需要使用哪个数据只需要通过调用 `Environment` 对象的 `getProperty(String name)` 方法获取。具体代码如下：

```java
@RestController
@RequestMapping("/books")
public class BookController {
    
    @Autowired
    private Environment env;
    
    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id){
        System.out.println(env.getProperty("lesson"));
        System.out.println(env.getProperty("enterprise.name"));
        System.out.println(env.getProperty("enterprise.subject[0]"));
        return "hello , spring boot!";
    }
}
```

> **注意：这种方式，框架内容大量数据，而在开发中我们很少使用。**

#### 3.自定义对象(常用)

`SpringBoot` 还提供了将配置文件中的数据封装到我们自定义的实体类对象中的方式。具体操作如下：

* 将实体类 `bean` 的创建交给 `Spring` 管理。

  在类上添加 `@Component` 注解

* 使用 `@ConfigurationProperties` 注解表示加载配置文件

  在该注解中也可以使用 `prefix` 属性指定只加载指定前缀的数据

* 在 `BookController` 中进行注入

**具体代码如下：**

`Enterprise` 实体类内容如下：

```java
/*
@ConfigurationProperties作用：
将配置文件中配置的每一个属性的值，映射到这个组件中；
告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
参数 prefix = “enterprise” : 将配置文件中的enterprise下面的所有属性一一对应
*/
@Component			//一定要加上Component注解，声明为bean，这样才能被spring管理
@ConfigurationProperties(prefix = "enterprise")		//加载配置文件，prefix属性指定你要加载配置文件里指定前缀的数据
													//prefix标签里不能出现大写字母
public class Enterprise {
    private String name;
    private int age;
    private String tel;
    private String[] subject;
    
	/*get/set/toSting省略*/
}
```

`BookController` 内容如下：

```java
@RestController
@RequestMapping("/books")
public class BookController {
    
    @Autowired	
    private Enterprise enterprise;		//通过自动装配拿到对象

    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id){
        System.out.println(enterprise.getName());		//直接调用get来获取数据
        System.out.println(enterprise.getAge());
        System.out.println(enterprise.getSubject());
        System.out.println(enterprise.getTel());
        System.out.println(enterprise.getSubject()[0]);
        return "hello , spring boot!";
    }
}
```

**注意：**

使用第三种方式，在实体类上有如下警告提示

<img src=" assets\image-20210917180919390.png" alt="image-20210917180919390" style="zoom:70%;" />

这个警告提示解决是在 `pom.xml` 中添加如下依赖即可

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

## Ⅲ.多环境配置

### yaml文件

在 `application.yml` 中使用 `---` 来分割不同的配置，内容如下

```yaml
#开发
spring:
  profiles: dev #给开发环境起的名字
server:
  port: 80
---
#生产
spring:
  profiles: pro #给生产环境起的名字
server:
  port: 81
---
#测试
spring:
  profiles: test #给测试环境起的名字
server:
  port: 82
---
```

上面配置中 `spring.profiles` 是用来给不同的配置起名字的。而如何告知 `SpringBoot` 使用哪段配置呢？可以使用如下配置来启用都一段配置

```yaml
#设置启用的环境
spring:
  profiles:
    active: dev  #表示使用的是开发环境的配置
```

综上所述，`application.yml` 配置文件内容如下

```yaml
#设置启用的环境
spring:
  profiles:
    active: dev

---
#开发
spring:
  profiles: dev
server:
  port: 80
---
#生产
spring:
  profiles: pro
server:
  port: 81
---
#测试
spring:
  profiles: test
server:
  port: 82
---
```

**注意：**

在上面配置中给不同配置起名字的 `spring.profiles` 配置项已经过时。最新用来起名字的配置项是 

```yaml
#开发
spring:
  config:
    activate:
      on-profile: dev
```

但是作用一样，所以具体用哪种问题不大

### properties文件(了解即可)

`properties` 类型的配置文件配置多环境需要定义不同的配置文件

* `application-dev.properties` 是开发环境的配置文件。我们在该文件中配置端口号为 `80`

  ```properties
  server.port=80
  ```

* `application-test.properties` 是测试环境的配置文件。我们在该文件中配置端口号为 `81`

  ```properties
  server.port=81
  ```

* `application-pro.properties` 是生产环境的配置文件。我们在该文件中配置端口号为 `82`

  ```properties
  server.port=82
  ```

`SpringBoot` 只会默认加载名为 `application.properties` 的配置文件，所以需要在 `application.properties` 配置文件中设置启用哪个配置文件，配置如下:

```properties
spring.profiles.active=pro
```

### 命令行启动参数设置(前端修改端口号)

使用 `SpringBoot` 开发的程序以后都是打成 `jar` 包，通过 `java -jar xxx.jar` 的方式启动服务的。那么就存在一个问题，如何切换环境呢？因为配置文件打到的jar包中了。

我们知道 `jar` 包其实就是一个压缩包，可以解压缩，然后修改配置，最后再打成jar包就可以了。这种方式显然有点麻烦，而 `SpringBoot` 提供了**在运行 `jar` 时设置开启指定的环境的方式**，如下

```shell
java –jar xxx.jar –-spring.profiles.active=test
```

那么这种方式能不能**临时修改端口号**呢？也是可以的，可以通过如下方式

```shell
java –jar xxx.jar –-server.port=88
```

当然也可以同时设置多个配置，比如**即指定启用哪个环境配置，又临时指定端口**，如下

```shell
java –jar springboot.jar –-server.port=88 –-spring.profiles.active=test
```

大家进行测试后就会发现命令行设置的端口号优先级高（也就是使用的是命令行设置的端口号），配置的优先级其实 `SpringBoot` 官网已经进行了说明，参见 :[Spring官网](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config)

进入上面网站后会看到如下页面

![image-20210917193910191]( assets\image-20210917193910191.png)

如果使用了多种方式配合同一个配置项，优先级高的生效。

### 多环境开发兼容问题(Maven与Spring兼容问题)

如果Maven配有多环境开发，Springboot中也配有多环境开发，**Maven占主导地位**，因为Maven负责打包。

#### 步骤一：maven中设置多环境

![image-20230116221619077]( assets\image-20230116221619077.png)

#### 步骤二：boot中引用maven属性

![image-20230116221645082]( assets\image-20230116221645082.png)

#### 步骤三：执行maven打包操作

Maven指令执行完毕以后，生成了对应的包，其中类参与编译，但是配置文件并没有编译，而是复制到包中

![image-20230116221839751]( assets\image-20230116221839751.png)

解决思路：对于源码中非java类的操作要求加载maven对应的属性，解析${}占位符

#### 步骤四：开启占位符解析

![image-20230116221949445]( assets\image-20230116221949445.png)

### Ⅳ.配置文件分类(优先级)

SpringBoot 提供有多级配置文件，它们的本质还是配置文件，只是存放的位置不同。

`SpringBoot` 中4级配置文件放置位置：

* 1级：classpath：application.yml  
* 2级：classpath：config/application.yml
* 3级：file ：application.yml
* 4级：file ：config/application.yml 

> **说明：**级别越高优先级越高

- 下图展示类路径中是如何存放的。

![]( assets\bcbbb4debea64f71876466604a1f1267.png)

![在这里插入图片描述]( assets\e88020acc52c401484a8a6d7705b85b8.png)

- config 文件夹下有一个 yaml 配置文件。

# 三、SpringBoot整合第三方技术

## Ⅰ.SpringBoot整合Junit

回顾 `Spring` 整合 `junit`

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class UserServiceTest {
    
    @Autowired
    private BookService bookService;
    
    @Test
    public void testSave(){
        bookService.save();
    }
}
```

使用 `@RunWith` 注解指定运行器，使用 `@ContextConfiguration` 注解来指定配置类或者配置文件。而 `SpringBoot` 整合 `junit` 特别简单，分为以下三步完成(Springboot在创建的时候甚至已经完成了测试类的创建，他真的，我哭死)

* 在测试类上添加 `SpringBootTest` 注解
* 使用 `@Autowired` 注入要测试的资源
* 定义测试方法进行测试

<img src=" assets\image-20230116225815844.png" alt="image-20230116225815844" style="zoom:50%;" />

其实原来的配置类被写在了Springboot07TestApplication类里，Springboot07TestApplication会扫描当前包下的所有包，然后将带有注解的类加载成bean，Springboot07TestApplication此时就相当于配置类了（之前的SpringConfig），所以如果想要在测试类中加载该配置类，就需要加一句

```java
@SpringBootTest(classes = Springboot07TestApplication.class)
```

一般情况下boot都是直接给你配好的，如果按上图所示，com.itheima1包是扫不到com.itheima包的，所以在itheima1包下的测试类需要扫描com.itheima包下的Springboot07TestApplication类，此时itheima1下的测试类应该如下所示:

```java
//相当于spring整合junit中的@ContextConfiguration(classes = SpringConfig.class)注解
@SpringBootTest(classes = Springboot07TestApplication.class)	
class Springboot07TestApplicationTests {

    @Autowired
    private BookService bookService;	//自动注入

    @Test
    public void save() {		//测试方法
        bookService.save();
    }

}
```

最后再说一下，一般是不需要自己写的！！！！

## Ⅱ.Springboot整合mybatis

###  创建模块

* 创建新模块，选择 `Spring Initializr`，并配置模块相关基础信息

<img src=" assets\image-20210917215913779.png" alt="image-20210917215913779" style="zoom:80%;" />

* 选择当前模块需要使用的技术集（MyBatis、MySQL）

  <img src=" assets\image-20210917215958091.png" alt="image-20210917215958091" style="zoom:80%;" />

### 定义实体类

在 `com.itheima.domain` 包下定义实体类 `Book`，内容如下

```java
public class Book {
    private Integer id;
    private String name;
    private String type;
    private String description;
    //setter and  getter
    //toString
}
```

### 定义dao接口

在 `com.itheima.dao` 包下定义 `BookDao` 接口，内容如下

```java
public interface BookDao {
    @Select("select * from tbl_book where id = #{id}")
    public Book getById(Integer id);
}
```

### 定义测试类

在 `test/java` 下定义包 `com.itheima` ，在该包下测试类，内容如下

```java
@SpringBootTest
class Springboot08MybatisApplicationTests {

	@Autowired
	private BookDao bookDao;

	@Test
	void testGetById() {
		Book book = bookDao.getById(1);
		System.out.println(book);
	}
}
```

### 编写配置

我们代码中并没有指定连接哪儿个数据库，用户名是什么，密码是什么。所以这部分需要在 `SpringBoot` 的配置文件中进行配合。

在 `application.yml` 配置文件中配置如下内容

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/genshin?serverTimezone=UTC
    username: root
    password: 703791321
```

### 测试

运行测试方法，我们会看到如下错误信息

<img src=" assets\image-20210917221427930.png" alt="image-20210917221427930" style="zoom:70%;" />

错误信息显示在 `Spring` 容器中没有 `BookDao` 类型的 `bean`。为什么会出现这种情况呢？

原因是 `Mybatis` 会扫描接口并创建接口的代码对象交给 `Spring` 管理，但是现在并**没有告诉 `Mybatis` 哪个是 `dao` 接口**。而我们要解决这个问题需要**在`BookDao` 接口上使用 `@Mapper`** ，`BookDao` 接口改进为

```java
@Mapper
public interface BookDao {
    @Select("select * from tbl_book where id = #{id}")
    public Book getById(Integer id);
}
```

> **注意：**
>
> `SpringBoot` 版本低于2.4.3(不含)，Mysql驱动版本大于8.0时，需要在url连接串中配置时区 `jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC`，或在MySQL数据库端配置时区解决此问题

### 使用Druid数据源

现在我们并没有指定数据源，`SpringBoot` 有默认的数据源，我们也可以指定使用 `Druid` 数据源，按照以下步骤实现

* 导入 `Druid` 依赖

  ```xml
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
  </dependency>
  ```

* 在 `application.yml` 配置文件配置

  可以通过 `spring.datasource.type` 来配置使用什么数据源。配置文件内容可以改进为

  ```yaml
  spring:
    datasource:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/genshin?serverTimezone=UTC
      username: root
      password: 703791321
      type: com.alibaba.druid.pool.DruidDataSource
  ```

## Ⅲ.Spring放行静态页面

在 `SpringBoot` 程序中是没有 `webapp` 目录的，那么在 `SpringBoot` 程序中静态资源需要放在什么位置呢？

静态资源需要放在 `resources` 下的 `static` 下，如下图所示

<img src=" assets\image-20210917230702072.png" alt="image-20210917230702072" style="zoom:80%;" />