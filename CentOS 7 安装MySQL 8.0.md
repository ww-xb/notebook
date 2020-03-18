# CentOS 7安装MySQL 8.0并开启远程访问
## 1.检查MariaDB

检查是否已安装MariaDB并删除

```shell
rpm -qa | grep mariadb
rpm -e --nodeps mariadb-libs-5.5.64-1.el7.x86_64
```



## 2.安装

下载安装包并解压

官方地址：https://dev.mysql.com/downloads/mysql/

华为镜像站：https://mirrors.huaweicloud.com/mysql/

依次下载rpm包

```bash
wget https://mirrors.huaweicloud.com/mysql/Downloads/MySQL-8.0/mysql-community-common-8.0.19-1.el7.x86_64.rpm
wget https://mirrors.huaweicloud.com/mysql/Downloads/MySQL-8.0/mysql-community-libs-8.0.19-1.el7.x86_64.rpm
wget https://mirrors.huaweicloud.com/mysql/Downloads/MySQL-8.0/mysql-community-client-8.0.19-1.el7.x86_64.rpm
wget https://mirrors.huaweicloud.com/mysql/Downloads/MySQL-8.0/mysql-community-server-8.0.19-1.el7.x86_64.rpm
```



下载完成后依次安装：

```bash
rpm -ivh mysql-community-common-8.0.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.19-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.19-1.el7.x86_64.rpm
```

安装完成

```
mysql --version
```



## 3.服务启停


启动服务

```bash
systemctl start mysqld
```

查看服务状态

```bash
systemctl status mysqld
```



## 4.数据库操作

查看临时密码

```bash
cat /var/log/mysqld.log
```

用临时密码登录数据库

```bash
mysql -u root -p
```

修改MySQL密码

```mysql
alter USER 'root'@'localhost' IDENTIFIED BY 'PASSWORD';
```

开启远程访问（适用于MySQL8.0以后版本）

```mysql
CREATE USER 'root'@'%' IDENTIFIED BY 'PASSWORD'; 
GRANT ALL ON *.* TO 'root'@'%'; 
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'PASSWORD';
FLUSH PRIVILEGES;
```

