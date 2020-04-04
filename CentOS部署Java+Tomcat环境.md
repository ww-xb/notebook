# CentOS部署Java+Tomcat环境

1.下载相应的安装包

从华为镜像站下载

```bash
wget https://mirrors.huaweicloud.com/java/jdk/13+33/jdk-13_linux-x64_bin.tar.gz
wget https://mirrors.huaweicloud.com/apache/tomcat/tomcat-9/v9.0.31/bin/apache-tomcat-9.0.31.tar.gz
```

2.解压

```bash
tar -zxvf jdk-13_linux-x64_bin.tar.gz
tar -zxvf apache-tomcat-9.0.31.tar.gz
mv jdk-13 /opt/
mv apache-tomcat-9.0.31 /opt/
```

3.配置环境变量

```bash
vim /etc/profile
```

将以下内容填入文本最后

```bash
JAVA_HOME=/opt/jdk-13
PATH=/opt/jdk-13/bin:/opt/apache-tomcat-9.0.31/bin:$PATH
CLASSPATH=.:/opt/jdk-13/lib/dt.jar:/opt/jdk-13/lib/tools.jar
CATALINA_HOME=/opt/apache-tomcat-9.0.31
export JAVA_HOME CLASSPATH CATALINA_HOME PATH
```

最后使环境变量生效

```bash
source /etc/profile
```

