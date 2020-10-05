# Mysql 笔记

## 安装mysql server
1. 添加包
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm   
rpm -ivh mysql80-community-release-el7-3.noarch.rpm   
2. 更新 yum 命令  
yum clean all && yum makecache    
3. 安装    
yum install -y  mysql-community-server   
4. 配置  
vim /etc/my.cnf   
```
[mysqld]

port = 3306

character-set-server=utf8mb4
collation-server=utf8mb4_general_ci

# 表名不区分大小写(启动前配置)
lower_case_table_names=1

#设置日志时区和系统一致
log_timestamps=SYSTEM

[client]
default-character-set=utf8mb4
```  
5. 启动mysql   
systemctl start mysqld
systemctl status mysqld   
systemctl enable mysqld   
systemctl daemon-reload   

6. 修改密码   
grep "A temporary password" /var/log/mysqld.log   
mysql -u root -p 临时密码   
如果要设置简单密码的话，先屏蔽密码安全策略   
set global validate_password.policy=0;   
set global validate_password.length=1;   
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass!';   

----------


## 配置开发环境
1. yum install mysql-devel -y
2. pip3 install  mysqlclient
