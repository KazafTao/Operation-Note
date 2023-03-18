# Mysql 笔记

## 安装mysql server
1. 添加包

  ```shell
  wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
  rpm -ivh mysql80-community-release-el7-3.noarch.rpm
  ```

2. 更新 yum 命令  

  ```shell
  yum clean all && yum makecache
  ```

3. 安装    

  ```shell
  yum install -y  mysql-community-server
  ```

4. 配置  

  ```shell
  vim /etc/my.cnf
  ```
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

## 重置Mysql8密码
1. 允许免密登录   
vim /etc/my.cnf   
在 [mysqld] 最后加上skip-grant-tables，保存并退出   
2. 重启mysql   
service mysqld restart   
3.  置空root密码   
mysql   
use mysql   
update user set authentication_string='' where user='root';   
退出   
4. 重置新密码   
mysql   
ALTER user 'root'@'localhost' IDENTIFIED BY 'NewPassword'   

----------

## 配置开发环境
1. yum install mysql-devel -y

2. pip3 install  mysqlclient

## 数据备份

### 为什么要备份

- **灾难恢复**，数据库在运行过程中，终会遇到各种各样的问题: 硬件故障、Bug 导致数据损坏、由于服务器宕机或者其他原因造成的数据库不可用。除此以外还有人为操作：`DELETE` 语句忘加条件、`ALTER TABLE` 执行错表、`DROP TABLE` 执行错表、黑客攻击，即使这些问题你都还没遇到，但是根据墨菲定律，总会有遇上的时候。
- **回滚**，由于某种Bug或系统被黑造成大量的损失，这个时候就需要回滚到某个状态。常见的有区块链交易所被黑然后回滚，游戏漏洞被利用然后整体回滚。
- **审计**，有时候有这样的需求：需要知道某一个时间点的数据是怎么样的，可能是年末审计，也可能是因为官司。
- **测试**，一个基本的测试需求是，定时拉取线上数据到测试环境，如果有备份，就可以非常方便地拉取数据。
### 怎么备份

#### 全量备份

InnoDB全库备份

```shell
mysqldump --opt --single-transaction --master-data=2 --default-character-set=utf8 -h<host> -u<user> -p<password> -A > backup.sql
```

- `--opt` 如果有这个参数表示同时激活了mysqldump命令的quick，add-drop-table，add-locks，extended-insert，lock-tables参数，它可以给出很快的转储操作并产生一个可以很快装入MySQL服务器的转储文件。当备份大表时，这个参数可以防止占用过多内存
- `--single-transaction` 设置事务的隔离级别为可重复读，然后备份的时候开启事务，这样能保证在一个事务中所有相同的查询读取到同样的数据。注意，这个参数只对支持事务的引擎有效，如果有 `MyISAM` 的数据表，并不能保证数据一致性
- `-A` 导出全部数据库
- `–-default-character-set=charset` 指定导出数据时采用何种字符集
- `--master-data=2` 表示在备份过程中记录主库的 `binlog` 和 `pos` 点，并在dump文件中注释掉这一行，在使用备份文件做新备库时会用到

## 数据恢复

导入备份进行恢复

```shell
mysql -h<host> -u<user> -p<password> < backup.sql
```



