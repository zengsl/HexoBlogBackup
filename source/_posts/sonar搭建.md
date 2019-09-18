---
title: sonar搭建
date: 2019-09-01 17:17:56
tags:
	- sonar
	- 代码检测
---


##  1、下载sonar 

官网：https://www.sonarqube.org/downloads/   

<!--more-->

①选择对应的版本，这里我下载的是 SonarQube 5.6.6 (LTS *)
②解压到本地目录（最好不使用中文目录，避免产生问题）
③配置环境变量SONAR_QUBE，JAVA_HOME并加入PATH中，SonarQube的版本对jdk也有要求，这里的5.6.6需要安装jdk1.8
④ 默认管理员帐号admin admin
⑤配置wrapper.conf
#wrapper.java.initmemory=3
#wrapper.java.maxmemory=3
wrapper.java.command=D:/iriswork/tools/jdk/JDK8/bin/java
⑥配置sonar.properties
数据库连接
sonar.jdbc.username=sonar 
sonar.jdbc.password=sonar 
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance
sonar.jdbc.driverClassName=com.mysql.jdbc.Driver 

启动：conf/目录下找对应系统环境的启动脚本 如win64的 StartSonar.bat
配置好之后通过localhost:9000访问sonar



##  2、安装配置数据库

这里使用的是mysql（如果使用oracle，需要另外安装sonar插件）
CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci; 
CREATE USER 'sonar' IDENTIFIED BY 'sonar';
GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
FLUSH PRIVILEGES;

## 3、安装sonar-runner

①选择对应版本，这里下载的是sonar-runner-2.4
高版本的sonar需要sonar-runner-2.4（sonarqube版本在4.4以上，就要下载sonar-runner2.4版本否则会有一定问题）
②解压到本地目录
③配置环境变量 SONAR_RUNNER_HOME,加入PATH
④配置 sonar-runner.properties
sonar.host.url=http://localhost:9000
sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&amp;characterEncoding=utf8
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.sourceEncoding=UTF-8
sonar.login=admin
sonar.password=admin
⑤在项目主目录创建sonar-project.properties并配置
sonar.projectKey=ahkjt-egrantweb-sonarqube-runner 
sonar.projectName=ahkjt-egrantweb project analyzed with the SonarQube Runner 
sonar.projectVersion=1.0 
sonar.sources=src 
sonar.binaries=target/classes 
sonar.language=java 
sonar.sourceEncoding=UTF-8 
⑥cmd进入项目主目录执行 sonar-runner命令

## 4、sonar中文包

sonar5.6.6对应的中文包sonar-l10n-zh-plugin-1.11
下载之后放在sonar对应的D:\Program Files\sonarqube\sonarqube-5.6.6\extensions\plugins中即可。


sonar与maven的结合使用

1、 在maven中配置sonar

~~~ xml
<profile>
 <id>sonar</id>
 <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
 <properties>
    <sonar.jdbc.url>
           jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8
    </sonar.jdbc.url>
    <sonar.jdbc.driver>com.mysql.jdbc.Driver</sonar.jdbc.driver>
    <sonar.jdbc.username>sonar</sonar.jdbc.username>
    <sonar.jdbc.password>sonar</sonar.jdbc.password>
    <sonar.host.url>http://localhost:9000</sonar.host.url>
  </properties>
</profile>
~~~

执行mvn sonar:sonar命令
