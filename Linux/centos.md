# Linux 笔记
 ## 升级版本   
 - 检查主机Cent OS 版本   
cat /etc/centos-release   
 - 备份重要的数据   
    如/etc中的各种配置文件    
 - 使用yum升级    
    yum clean all    
    yum update    
 - 重启    
   reboot    
 - 查看Cent OS版本,确认升级成功    
   cat /etc/centos-release    
## 安装Mysql
 - 下载Mysql
wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.29-1.el7.x86_64.rpm-bundle.tar   
 - 解压  
tar -xvf mysql-5.7.29-1.el7.x86_64.rpm-bundle.tar    
 - 卸载mariadb-lib    
rpm -qa | grep mariadb        
rpm -e --nodeps {grep 到的mariabd-lib}   
 - 安装  
rpm -ivh mysql-community-common-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-libs-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-client-5.7.29-1.el7.x86_64.rpm   
rpm -ivh mysql-community-server-5.7.29-1.el7.x86_64.rpm  
如果出现这个错 ``` error: Failed dependencies:   
	libaio.so.1()(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64   
	libaio.so.1(LIBAIO_0.1)(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64   
	libaio.so.1(LIBAIO_0.4)(64bit) is needed by mysql-community-server-5.7.29-1.el7.x86_64 ```      
则执行yum install  -y libaio-devel.x86_64     
 - 配置数据库       
vim /etc/my.cnf           
在[mysqld]下添加这三行   
#跳过登录验证   
skip-grant-tables   
#设置默认字符集UTF-8   
character_set_server=utf8   
#设置默认字符集UTF-8   
init_connect='SET NAMES utf8'   
 - 启动mysql   
systemctl start mysqld.service          
----
## 防火墙
### 查看开放的端口

```shell
firewall-cmd --list-ports
```

### 开放端口
```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

### 关闭端口
```shell
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

### 开启防火墙
```shell
systemctl start firewalld
```

### 关闭防火墙
```shell
systemctl stop firewalld
```

### 重启防火墙
```shell
systemctl restart firewalld
```

### 允许防火墙自启动
```shell
systemctl enable firewalld
```

## 设置静态ip

编辑网络配置

```shell
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

修改 `BOOTPROTO=static`

```conf
#如果没有该配置项，则添加，有则修改
BOOTPROTO="static"
#设置网卡启动方式为 开机启动 并且可以通过系统服务管理器 systemctl 控制网卡
DNBOOT="yes"
#要配置的ip地址
IPADDR="192.168.152.11"
NETMASK="255.255.255.0"
#你自己的网关地址
GATEWAY="192.168.152.2"
# DNS服务器地址，选谷歌的8.8.8.8
DNS1="8.8.8.8"
```

