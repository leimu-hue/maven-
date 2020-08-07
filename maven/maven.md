# Maven

## 1、可以解决的问题：

​	1、可以自动帮我们导入jar包

​	2、可以进行全部的单元测试

​	3、可以轻松整合代码与各种配置文件、资源

​	4、可以使得总体的工程大小小很多【因为传统的web工程的jar包在项目中。而maven开发的jar包是在仓库中】【使得代码可重用】

## 2、下面是maven下载资源的两个镜像地址【一个是阿里云、一个是国外的】

`<!--<mirror>
		<id>aliyunmaven</id>
		<mirrorOf>central</mirrorOf>
		<name>aliyun maven</name>
		<url>https://maven.aliyun.com/repository/public </url>
    </mirror>-->
	<mirror>
        <id>nexus</id>
        <name>internal nexus repository</name>
        <url>https://repo.maven.apache.org/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>`

## 3、项目的一键构建

​	1、一个项目，需要经过编译、测试、运行、打包、安装、部署等一系列过程

##### 		2、什么是构建呢：

​		是指项目从编译、测试、运行、打包、安装、部署整个过程都交给maven进行管理，这个就称为构建

​		maven构建流程如下：	![maven构建](\image\maven构建.png)

## 4、maven仓库

##### 	1、maven仓库有三种种类：

​		1）本地仓库

​		2）中央仓库

​		3）远程仓库

##### 	2、寻找过程：

maven工程需要找jar包的坐标--》系统盘找本地仓库--》远程仓库[私服]--》中央仓库

## 5、maven标准目录结构

##### 	1）传统项目的目录：

​		项目名【虽然可以自己添加资源文件，但是不统一】

​			src

##### 	2）maven项目标准目录结构：

​		项目名：

​			src/main/java 目录：核心代码部分

​			src/main/resources目录：配置文件部分

​			src/main/webapp目录：放置一些页面资源  js、css、图片等等

​			src/test/java 目录  测试代码部分

​			src/test/resources  测试代码的配置文件部分

## 6、Maven声明周期

清理声明周期

​	mvn clean

下面被称为默认声明周期	

​	1、编译：mvc compile

​	2、测试：mvn test

​	3、打包：mvn package

​	4、安装：mvn install

​	5、发布：mvn deploy

## 7、Maven常用的命令：

1、mvn clean：

​	用于将项目的target目录移除，准备进行重新编译的工作[用于清除在其他电脑上项目的编译信息]

2、mvn compile

​	编译核心代码文件，即java目录里面的代码，生成target目录

3、mvn test

​	用于编译test目录下的代码，生成classes文件

4、mvn package

​	用于打包。那会根据<packaging>标签来判断应该打包为jar包还是war包【注意：这个命令还会将项目放置在本地仓库中】

![打包方式](\image\打包方式.png)

​	5、**需要注意的是**：如果执行test命令，那么执行test期间也会执行compile命令，可以看作package包含test test包含compile。它们在执行自己期间也会将包含的命令一起执行

## 8、Maven进行版本统一

##### 	1、为什么需要版本统一

​		在进行项目开发，我们会使用maven来进行项目的构建和管理。为了方便各个模块的复用，项目通常由多个模块构成。为了防止不同模块之间出现jar包的冲突。

​		还为了版本的统一管理和升级维护，防止部分模块的依赖升级，导致项目中多个版本的同名jar

##### 	2、进行演示：

​		1）首先构建一个子模块：

![子模块](image\子模块.png)

​			2）声明父模块的类型：

​				在构建完成之后，父模块会自动生成子模块的名字，在<modules>标签下

```java
<modules>
    <module>Maven-child</module>
</modules>
并且制定父模块的类型
<packaging>pom</packaging>
```

​			3）使用properties定义版本依赖号

```java
<properties>
   <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   <junit-version>3.8.1</junit-version>
</properties>
    注意：上述名称可以随意，但是最好还是要见名知意
```
​			4）使用dependencyManagement标签进行版本依赖的管理

```java
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit-version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
    注意：需要使用${}来进行版本的引用，名字需要与定义的名字一致
    管理的jar包并不会去引入，而是当子模块使用的时候才会去引用
```

​		5）之后，maven的子模块只需要写出artifactId和groupId标签就可以引用jar包了

```java
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
</dependencies>
    这样，就不需要在子模块中使用<version>标签标识版本了
    还需要注意的是：在子模块中有一个parent
    <parent>
        <artifactId>MavenTest</artifactId>
        <groupId>com.test.group</groupId>
        <version>0.0.1-SNAPSHOT</version>
    	<relativePath />   //告诉子模块，父模块所在版本路径【相对路径】
    </parent>
    这个标签的作用是告诉子模块，父模块所在的位置
```

​		6）注意：子模块会继承父模块的设定

## 9、Pom.xml详解：

​	1、parent标签：

```java
//父项目坐标，如果项目中没有规定某个元素的值，那么父项目中的对应值就为项目的默认值
<parent>
    //被继承的父项目的构建标识符【项目名】
    <artifactId>MavenTest</artifactId>
    //构建父项目时，设置的groupId
    <groupId>com.test.group</groupId>
    //被继承的父项目的版本
    <version>0.0.1-SNAPSHOT</version>
    //父项目的pom.xml文件的相对路径，默认值为../pom.xml
    //搜索路径：在构建当前项目的地方寻找父项目的pom--》在此标签指定的路径寻找pom--》然后去本地仓库寻找--》最后去远程仓库寻找pom文件
    <relativePath />
</parent>
```

​	2、modelVersion

​	`<modelVersion>4.0.0</modelVersion>`

​	声明项目描述符遵循哪一个pom模型版本，主要是为了当maven引入新的特性或者模型变更时，确保稳定性

​	3、packaging

```java
//项目构建的类型
//部分类型为：jar、war、eer、pom。插件也可以构建自己的构件类型
<packaging>pom</packaging>
//如果类型为pom，表示它不做任何代码逻辑判断，只是作为一个依赖模块来进行版本管理
```

​		4、name和url

```java
//项目的名称  maven产生文档的时候使用
<name>MavenTest</name>
//项目主页的URL，maven产生文档时使用
<url>http://maven.apache.org</url>
```

​		5、其他具体可以观看：

​		[https://www.cnblogs.com/hafiz/p/5360195.html](https://www.cnblogs.com/hafiz/p/5360195.html)

## 10、使用Maven构建项目的时候可能遇到的错误：

##### 	1、如果maven导入项目出现sun.security.validator.ValidatorException: PKIX path building failed等错误

​		那么就可以使用以下命令忽略证书检查-Dmaven.multiModuleProjectDirectory=$MAVEN_HOME -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true

##### 	2、获取去官网下载证书，将证书导入java对应文件夹。使用以下命令导入：

keytool -import -alias 别名 -file 文件路径 -keystore "%JAVA_HOME%/jre/lib/security/cacerts" -storepass changeit -trustcacerts

或者

 keytool -importcert -noprompt -trustcacerts -alias 别名 -file 文件路径 -keystore "%JAVA_HOME%\jre\lib\security\cacerts" -storepass changeit

##### 3、出现下面错误：

https://repo.maven.apache.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of nexus has elapsed or updates are forced. Original error: Could not transfer artifact

可以使用**mvn compile**命令进行清空，重新下载即可解决问题