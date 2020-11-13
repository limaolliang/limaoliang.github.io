---
layout:     post
title:      Spring Boot 从零单排
subtitle:   
date:       2020-11-13
author:     Maomao
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - 后台开发
---


## Spring Boot 从零单排


#### 1.项目背景
最近需要将一些相关的项目数据管理起来，搭建一个简单的服务，需要能够快速上手，本身的学习成本也比较低，正好比较熟悉java，在了解了之后准备使用Springboot + vue的方案来做。
#### 2.Springboot是什么
 ![enter image description here](https://raw.githubusercontent.com/limaolliang/limaolliang.github.io/master/img/post-springboot/21.png)
> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run".


这是Spring Boot官网的第一句话，简单来说，就是 Pivotal 团队提供了一套基于Spring应用开发的框架，将一些之前需要自己做的东西，都包装好了，尽大可能的减少了开发者的开发工作和配置工具


#### 3.创建一个Springboot项目
##### 3.1 环境部署
工欲善其事必先利其器，在开始启动一个springboot的项目之前，需要提前准备一下相关的开发环境
**1）JAVA环境**
JAVA环境自不用说，这里需要说的一点是，创建Sprintboot项目貌似最低需要1.8以上的java版本支持。
**2）IDE** 
一个好用的IDE是开发效率提升的一半，一般可以使用IDEA IntelliJ 或者My Eclipse。其实直接用subline Text也挺好用的。
**3）Maven 编译环境**
根据我的经验，IDE自带的maven编译一般容易遇到坑，所以最好自己部署一套maven的编译器，方法也比较简单，相对比较好控制

##### 3.2 从零单排的第一步
简单来说，你有两种方法迈入从零单排的第一步，看你是否有IDE。
如果没有IDE的话，直接在Spring Boot的官网Get Start即可
[Sprint Boot官网Quick Start入口](https://start.spring.io/)
界面大概长这样
 ![enter image description here](https://raw.githubusercontent.com/limaolliang/limaolliang.github.io/master/img/post-springboot/22.png)
按照自己项目的需求把对应的名字修改了就好，右边的话有依赖库可以直接在创建的时候选择，这里选不选两可，后续在pom.xml文件里面自行添加也都非常方便，这里可以大概钩上Spring Web，因为这个是最基础的服务，也可以看情况钩上MySQL之类的组件。

如果有IDE的话，操作界面其实类似
New Project -> Spring Initializer -> 直接选择default ，选定java 版本就能进去了，大概长这样。
 ![enter image description here](https://raw.githubusercontent.com/limaolliang/limaolliang.github.io/master/img/post-springboot/23.png)
名字取好之后到选择对应的依赖库
 ![enter image description here](https://raw.githubusercontent.com/limaolliang/limaolliang.github.io/master/img/post-springboot/24.png)
钩上即可。

3.3 那么就跑起来吧！
创建好的项目的项目有几个关键的文件需要关注
1）DemoApplication
这个文件是Spring应用的入口，文件一般已经加上了@SpringBootApplication的注释，这个注释包含了一些通用需求的注释，不用修改。
2）application.propoties
这个文件用来存放一些系统级别的变量或者配置，一般可以将数据库的连接，系统环境变量放在这里。还有端口的配置也可以放在这里。
3）pom.xml
这个文件是用来管理Spring项目中所有依赖的套件，在3.2中勾选的依赖都会在这里体现，当然这里添加依赖，然后mvn compile编译一下把依赖拉下来即可。

搞一个最简单的hello world熟悉一下服务怎么来的。
在Spring Boot里面，对请求的响应都是在Controller里面来管理的。下面是一个最简单的Controller的代码，响应的是对根目录的请求响应

    @RequestMapping("/")
    public String Helloworld(){
        return "Hello world";
    }

使用IDE的启动应用，服务就可以直接启动返回String了。

#### 4.数据库连接和代码生成
项目基本的架子有了，没有几个趁手的武器怎么行！
##### 4.1 项目架构
Spring项目遵循MVC的架构分层方式，使用Service曾来提供对外的接口，对内的操作在DAO层。如下
 ![enter image description here](https://raw.githubusercontent.com/limaolliang/limaolliang.github.io/master/img/post-springboot/25.png)
##### 4.2 MySQL数据库连接
在application.propoties文件中将数据库连接的地址，用户名密码配置上之后即可快速的连接数据库。
一般数据库的实现可以基于jdbc的接口来说，也可以基于mybatis来做，笔者本次使用了code generator，这里使用的是mybatis的方案。

    spring.datasource.url=jdbc:mysql://*database address*:*port*/*database name*?serverTimezone=Hongkong&useUnicode=true&characterEncoding=utf8&useSSL=false&allowMultiQueries=true
    spring.datasource.username=*name*
    spring.datasource.password=*pwd*
    spring.datasource.driver-class-name=com.mysql.jdbc.Driver

##### 4.3 MyBatis Code Generator代码生成器
你是否有数据库表太多，不想自己些映射模型的想法？你是否有字段太多，不想自己些get set方法的想法？
你是否有增删查改代码太low，不想自己写的想法？
那么用code generator，解决你这些痛苦吧。
使用方法也比较简单，第一步，准备好你的generatorConfig文件

    <?xml version="1.0" encoding="UTF-8"?><!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

    <generatorConfiguration>
    <context id="alchemy" targetRuntime="MyBatis3">
        <property name="autoDelimitKeywords" value="true"/>
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://*database address*:*port*/*database name*" userId="username"
                        password="password"/>

        <javaModelGenerator targetPackage="com.tencent.benchmark.model"
                            targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <sqlMapGenerator targetPackage="mapper"
                         targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.tencent.benchmark.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <table domainObjectName="Benchmark" tableName="t_device_benchmark">
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>

    </context>

</generatorConfiguration>

第二步，在pom.xml中引入generator的套件

    <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.30</version>
                        <type>jar</type>
                        <scope>compile</scope>
                    </dependency>
                </dependencies>
            </plugin>

引入完成之后build，在IDE的右侧的plugins里面就可以看到generator了，直接点击运行就能得到一份自动生成的model，mapper接口以及mapper.xml（mapper的实现）

这里说一个比较坑的问题，查了好几天才查到的问题，到现在也不清楚是哪一步没有做好。
在mapper自动生成完成，自己写好service，在controller里面调用的时候，遇到了一个Invalid bound statement (not found)的问题，这个问题的根因也比较简单，就是mapper接口里面的方法，找不到对应的实现。这个问题网上解决方法一搜一大把，但大多数是让我自查文件名字，类名，方法名是否有对应上但是都没有解决我的问题，因为使用generator生成的代码，这些名字的对应应该都是没有问题的。我最终的解决也没有对应上具体是哪一步做了或者那几步做了搞定的，这里列举一下我修复这个问题修改的地方。
1）application.properties里面的增加的mapper xml文件的include

    mybatis.mapper-locations=classpath*:/mapper/**Mapper.xml
    
2）启动的application 文件中间增加mapper的扫描的注释

    @MapperScan("com.tencent.benchmark.mapper")

3）pom.xml中增加对resource文件夹的include

    <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>


4）Service文件中，增加@Service注释，Mapper接口文件中，增加@Mapper注释

##### 4.4 一个简单的数据库读取案例
在mapper接口生成好之后，我们在service中定义一个业务逻辑接口

    @Service
    public interface BenchmarkServices {
    public Benchmark getBenchmark(int id);
    }
在对应写一个实现，调用mapper的接口

    @Service
public class BechmarkServicesImpl implements BenchmarkServices {

    @Autowired
    BenchmarkMapper benchmarkMapper;


    @Override
    public Benchmark getBenchmark(int id){
        Benchmark bm = benchmarkMapper.selectByPrimaryKey(id);
        return bm;
    }
    }

最后我们在Controller中调用service接口就完成了

        @RequestMapping("/")
    public String Benchmark(){
        Benchmark bm = benchmarkServices.getBenchmark(14);

        return bm.getCarName();
    }
    }

#### 5.那就上线吧
在项目的根目录下使用的mvn package就可以将项目打成一个jar包部署，当然直接在IDE里面启动也可以做本地调试用
执行命令后target目录下面应该生成了一个项目名字的jar包，比如demo-0.0.1-SNAPSHOT.jar
我们在机器上使用

    nohup java -jar benchmark-0.0.1-SNAPSHOT.jar > log.txt &
命令启动就完成了！


#### 6.事情还远没结束
当然，上面也只是一个最基础的从零单排，想要实现多表查询，多数据库连接，更复杂的业务逻辑，肯定还需第二次第三次更多次的实践～

未完待续...