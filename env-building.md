# 案例准备

## 案例准备

### 技术选型

| 技术                     | 说明            | 官网                                                   |
|------------------------|---------------|------------------------------------------------------|
| Spring Cloud           | 微服务框架         | https://spring.io/projects/spring-cloud              |
| Spring Cloud Alibaba   | 微服务框架         | https://github.com/alibaba/spring-cloud-alibaba      |
| Spring Boot            | 容器+MVC框架      | https://spring.io/projects/spring-boot               |
| Spring Security Oauth2 | 认证和授权框架       | https://spring.io/projects/spring-security-oauth     |
| MyBatis                | ORM框架         | http://www.mybatis.org/mybatis-3/zh/index.html       |
| MyBatisGenerator       | 数据层代码生成       | http://www.mybatis.org/generator/index.html          |
| PageHelper             | MyBatis物理分页插件 | http://git.oschina.net/free/Mybatis_PageHelper       |
| Knife4j                | 文档生产工具        | https://github.com/xiaoymin/swagger-bootstrap-ui     |
| Elasticsearch          | 搜索引擎          | https://github.com/elastic/elasticsearch             |
| RabbitMq               | 消息队列          | https://www.rabbitmq.com/                            |
| Redis                  | 分布式缓存         | https://redis.io/                                    |
| MongoDB                | NoSql数据库      | https://www.mongodb.com/                             |
| Docker                 | 应用容器引擎        | https://www.docker.com/                              |
| Druid                  | 数据库连接池        | https://github.com/alibaba/druid                     |
| OSS                    | 对象存储          | https://github.com/aliyun/aliyun-oss-java-sdk        |
| MinIO                  | 对象存储          | https://github.com/minio/minio                       |
| JWT                    | JWT登录支持       | https://github.com/jwtk/jjwt                         |
| LogStash               | 日志收集          | https://github.com/logstash/logstash-logback-encoder |
| Lombok                 | 简化对象封装工具      | https://github.com/rzwitserloot/lombok               |
| Seata                  | 全局事务管理框架      | https://github.com/seata/seata                       |
| Portainer              | 可视化Docker容器管理 | https://github.com/portainer/portainer               |
| Jenkins                | 自动化部署工具       | https://github.com/jenkinsci/jenkins                 |
| Kubernetes             | 应用容器管理平台      | https://kubernetes.io/                               |

### 模块设计

springcloud-alibaba 父工程
shop-common 公共模块【实体类】
shop-user 用户微服务 【端口: 807x】
shop-product 商品微服务 【端口: 808x】
shop-order 订单微服务 【端口: 809x】
![微服务框架](_image/模块设计.png)

### 微服务调用

在微服务架构中，最常见的场景就是微服务之间的相互调用。我们以电商系统中常见的用户下单为例来
演示微服务的调用：客户向订单微服务发起一个下单的请求，在进行保存订单之前需要调用商品微服务
查询商品的信息。
我们一般把服务的主动调用方称为服务消费者，把服务的被调用方称为服务提供者

![微服务框架](_image/微服务调用.png)

在这种场景下，订单微服务就是一个服务消费者， 商品微服务就是一个服务提供者。

## 创建父工程

创建一个maven工程，然后在pom.xml文件中添加下面内容

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.ruichen-only</groupId>
    <artifactId>springcloud-alibaba</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <artifactId>spring-boot-starter-parent</artifactId>
        <groupId>org.springframework.boot</groupId>
        <version>2.4.4</version>
    </parent>

    <modules>
        <module>shop-common</module>
    </modules>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <spring-cloud.version>Hoxton.SR5</spring-cloud.version>
        <spring-cloud-alibaba.version>2.2.0.RELEASE</spring-cloud-alibaba.version>
        <mybatis-generator.version>1.4.0</mybatis-generator.version>
        <mybatis.version>3.5.5</mybatis.version>
        <pagehelper-starter.version>1.3.0</pagehelper-starter.version>
        <pagehelper.version>5.2.0</pagehelper.version>
        <druid.version>1.1.23</druid.version>
        <hutool.version>5.4.0</hutool.version>
        <mysql-connector.version>8.0.20</mysql-connector.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <!--Spring Cloud 相关依赖-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--Spring Cloud Alibaba 相关依赖-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--MyBatis分页插件starter-->
            <dependency>
                <groupId>com.github.pagehelper</groupId>
                <artifactId>pagehelper-spring-boot-starter</artifactId>
                <version>${pagehelper-starter.version}</version>
            </dependency>
            <!--MyBatis分页插件-->
            <dependency>
                <groupId>com.github.pagehelper</groupId>
                <artifactId>pagehelper</artifactId>
                <version>${pagehelper.version}</version>
            </dependency>
            <!--集成druid连接池-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!--Hutool Java工具包-->
            <dependency>
                <groupId>cn.hutool</groupId>
                <artifactId>hutool-all</artifactId>
                <version>${hutool.version}</version>
            </dependency>
            <!-- MyBatis 生成器 -->
            <dependency>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-core</artifactId>
                <version>${mybatis-generator.version}</version>
            </dependency>
            <!-- MyBatis-->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <!--Mysql数据库驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

</project>
```

删除项目目录下的src
![微服务框架](_image/删除父工程src目录.jpg)

## 创建基础模块

1.创建 shop-common 模块，在pom.xml中添加依赖
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud-alibaba</artifactId>
        <groupId>com.ruichen-only</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>shop-common</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```
2.创建实体类

> **ShopUser**
```java
package com.rea.entity;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

/**
 * @author CRR
 */
@Data
@Entity(name = "shop_user")
public class ShopUser {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer uid;
    private String username;
    private String password;
    private String telephone;
}
```
> **ShopProduct**
```java
package com.rea.entity;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

/**
 * @author CRR
 */
@Data
@Entity(name = "shop_product")
public class ShopProduct {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer pid;
    private String name;
    private Double price;
    private Integer stock;
}
```

> **ShopOrder**
```java
package com.rea.entity;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

/**
 * @author CRR
 */
@Data
@Entity(name = "shop_product")
public class ShopOrder {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer oid;
    private Integer uid;
    private String username;
}
```

## 创建用户微服务

> **步骤:**
1. 创建模块 导入依赖
2. 创建SpringBoot主类
3. 加入配置文件
4. 创建必要的接口和实现类(controller service dao)

> 新建一个 shop-user 模块，然后进行下面操作
1. 创建pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud-alibaba</artifactId>
        <groupId>com.rea</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>shop-user</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>com.rea</groupId>
            <artifactId>shop-common</artifactId>
        </dependency>
    </dependencies>

</project>
```
2. 编写入口类

```java
package com.rea.user;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

/**
 * @author CRR
 */
@SpringBootApplication
@EnableDiscoveryClient
public class UserApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserApplication.class, args);
    }
}
```