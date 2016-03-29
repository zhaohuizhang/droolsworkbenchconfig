# 简介
Drools 6.3.0 workbench 是Drools的一个web端开发平台。下面我们用Tomcat部署。
# 准备
##  Tomcat：apache-tomcat-7.0.68  
* 下载地址：http://mirrors.hust.edu.cn/apache/tomcat/tomcat-7/v7.0.68/bin/apache-tomcat-7.0.68-windows-x64.zip

##  Drools Workbench：kie-drools-wb-6.3.0.Final-tomcat7
* 下载地址：http://download.jboss.org/drools/release/6.3.0.Final/kie-drools-wb-6.3.0.Final-tomcat7.war

## 环境准备：
> * 平台：windows 7
> * Java：JDK1.8

# 安装步骤
## 1,JBPM的事务管理器JAR包引入
复制到TOMCAT_HOME/lib下，包括：
* btm-2.1.4.jar
* btm-tomcat55-lifecycle-2.1.4.jar
* h2-1.3.161.jar
* jta-1.1.jar
* slf4j-api-1.7.2.jar
* slf4j-jdk14-1.7.2.jar
> * 下载地址：https://github.com/zhaohuizhang/droolsworkbenchconfig
## 2,在TOMCAT_HOME/conf创建配置文件 
``` 
    * btm-config.properties
    ************************ sample btm-config.properties *************************
         bitronix.tm.serverId=tomcat-btm-node0
         bitronix.tm.journal.disk.logPart1Filename=${btm.root}/work/btm1.tlog
         bitronix.tm.journal.disk.logPart2Filename=${btm.root}/work/btm2.tlog
         bitronix.tm.resource.configuration=${btm.root}/conf/resources.properties

    *******************************************************************************


    * resources.properties
    ************************ sample resources.properties **************************
         resource.ds1.className=bitronix.tm.resource.jdbc.lrc.LrcXADataSource
         resource.ds1.uniqueName=jdbc/jbpm
         resource.ds1.minPoolSize=10
         resource.ds1.maxPoolSize=20
         resource.ds1.driverProperties.driverClassName=org.h2.Driver
         resource.ds1.driverProperties.url=jdbc:h2:file:~/jbpm
         resource.ds1.driverProperties.user=sa
         resource.ds1.driverProperties.password=
         resource.ds1.allowLocalTransactions=true
    *******************************************************************************
```
* 下载地址：https://github.com/zhaohuizhang/droolsworkbenchconfig
## 3,定义系统环境变量btm.root, bitronix config file, JBoss logging provider 
在TOMCAT_HOME/bin下面添加setenv.bat
```
SET CATALINA_OPTS="-Xmx512M -XX:MaxPermSize=512m -Dbtm.root=$CATALINA_HOME \
-Dbitronix.tm.configuration=$CATALINA_HOME/conf/btm-config.properties \
-Djbpm.tsr.jndi.lookup=java:comp/env/TransactionSynchronizationRegistry \
-Djava.security.auth.login.config=$CATALINA_HOME/webapps/kie-drools-wb/WEB-INF/classes/login.config \
-Dorg.jboss.logging.provider=jdk"
```
> 注意：在windows系统中环境变量的命名和linux中不一样。需要修改。部署war包时，要把名字改成kie-drools-wb.war

> * 下载地址：https://github.com/zhaohuizhang/droolsworkbenchconfig

## 4,配置JEE security
在TOMCAT_HOME/conf/server.xml中最后面的<HOST/>中添加一行：
```
<Valve className="org.kie.integration.tomcat.JACCValve" />
```
## 5,配置登录用户和密码
在TOMCAT_HOME/conf/tomcat-users.xml中配置admin权限
