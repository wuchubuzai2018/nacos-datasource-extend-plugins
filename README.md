# Nacos2.2版本数据库适配插件

## 一、插件概述

### 1.1、简介

从2022年12月14日发布的Nacos2.2正式版本开始，Nacos提供了数据源扩展插件，以便让需要进行其他数据库适配的用户自己编写插件来保存数据。当前项目插件目前已简单适配Postgresql，并抽象了一套常见的兼容项的方言类，开发者可以基于该设计，实现自定义分页逻辑即可。

如需Nacos2.1支持，请移步个人之前创建这个仓库，该仓库目前支持PostgreSQL、Oracle、达梦，简单的操作基本可以实现。

https://github.com/wuchubuzai2018/nacos-multidatasource

当前项目基于Nacos2.2版本的扩展插件口进行开发。

### 2.2、插件工程结构说明

nacos-datasource-plugin-ext工程主目录 

----nacos-datasource-plugin-ext-base工程设计为数据库插件操作的适配抽象。

----nacos-all-datasource-plugin-ext工程计划可打包所有适配的数据库插件

----nacos-postgresql-datasource-plugin-ext工程可打包适配Postgresql的数据库插件

## 二、下载和使用

IDEA导入时导入nacos-datasource-plugin-ext这个目录作为根目录即可。

### 2.1、插件引入

**方式一：源码方式**

使用postgresql作为依赖引入到Nacos主分支源码中，使用Maven提前将当前工程Install到Maven仓库，然后在Pom.xml中引入如下依赖：

```xml
<dependency>
            <groupId>com.alibaba.nacos</groupId>
            <artifactId>nacos-postgresql-datasource-plugin-ext</artifactId>
            <version>1.0.0-SNAPSHOT</version>
        </dependency>
```

或引入all模块。

**方式二：打包形式引入**

在Nacos2.2的发布包环境下，下载当前插件项目源码，打包为jar包，将该文件放到nacos主目录下的plugins文件夹中，默认会使用Nacos的loader.path机制指定该插件的路径，打包插件可选择nacos-postgresql-datasource-plugin-ext打包即可。postgresql模块的打包默认会包含postgresql的jdbc驱动类和base依赖。

### 2.2、修改数据库配置文件

在application.properties文件中声明postgresql的配置信息：

```java
spring.datasource.platform=postgresql
db.url.0=jdbc:postgresql://127.0.0.1:5432/nacos?tcpKeepAlive=true&reWriteBatchedInserts=true&ApplicationName=nacos_java
db.user=nacos
db.password=nacos
db.pool.config.driverClassName=org.postgresql.Driver
```

### 2.3、导入Postgresql的数据库脚本文件

导入nacos-postgresql的脚本文件，脚本文件在nacos-postgresql-datasource-plugin-ext/src/main/resources/schema文件夹下面.

上面操作完成后，启动Nacos即可。

## 三、其他数据库插件开发

可参考nacos-postgresql-datasource-plugin-ext工程，新创建Maven项目，实现AbstractDatabaseDialect类，重写相关的分页操作逻辑与方法，并创建相应的mapper实现，减少了适配的成本。

目前对于Oracle、达梦数据库，仍然需要修改Nacos2.2的主分支代码，因为要兼容默认的命名空间ID为空的查询情况，社区官网未处理。

最近有时间，将进行Nacos主分支源码上关于Nacos的Oracle的兼容性适配，目前可以用Nacos2.1的改造代码进行处理。
