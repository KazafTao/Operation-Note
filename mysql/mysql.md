# Mysql 笔记

## 安装mysql server
1. 下载Mysql
wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.21-1.el8.x86_64.rpm-bundle.tar   
2. 解压  
tar -xvf mysql-8.0.21-1.el8.x86_64.rpm-bundle.tar    
3. 卸载mariadb-lib    
rpm -qa | grep mariadb        
rpm -e --nodeps {grep 到的mariabd-lib}   
4. 安装  
rpm -ivh mysql-community-common-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-libs-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-client-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-server-5.7.29-1.el7.x86_64.rpm  
如果出现这个错 ``` error: Failed dependencies:   
	libaio.so.1()(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64   
	libaio.so.1(LIBAIO_0.1)(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64   
	libaio.so.1(LIBAIO_0.4)(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64 ```      
则执行yum install  -y libaio-devel.x86_64     
5. 配置数据库       
vim /etc/my.cnf           
在[mysqld]下添加这三行   
#跳过登录验证   
skip-grant-tables   
#设置默认字符集UTF-8   
character_set_server=utf8   
#设置默认字符集UTF-8   
init_connect='SET NAMES utf8'   
6. 启动mysql   
systemctl start mysqld.service


----------
## 安装 mysql-client
1. rpm -ivh https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm
2. yum install mysql-community-client.x86_643

## 配置开发环境
1. yum install mysql-devel -y
2. pip3 install  mysqlclient
