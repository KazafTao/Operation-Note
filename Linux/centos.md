# Linux 笔记
----
 - **升级版本**   
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
----
 - **安装Mysql**   
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
 - **防火墙**   
  
	 - 查看开放的端口
		 - firewall-cmd --list-ports
	 - 开放端口
		 - firewall-cmd --zone=public --add-port=80/tcp --permanent
	 - 关闭端口
		 - firewall-cmd --zone=public --remove-port=80/tcp --permanent
	 - 开启防火墙
		 - systemctl start firewalld
	 - 关闭防火墙
		 - systemctl stop firewalld
	 - 重启防火墙
		 - systemctl restart firewalld
	 - 允许防火墙自启动
		 - systemctl enable firewalld

----------


