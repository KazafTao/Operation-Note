# Python 笔记
## 升级Python 2.7.5
1. 官网下载新版本Python   
    wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz   

2. 解压   
    tar zxvf Python-2.7.18.tgz   

3. 编译安装   
	1. cd Python-2.7.18   
	2. ./configure --enable-optimizations   
	```make altinstall用于防止替换默认的python二进制文件/usr/bin/python。```   
	3. make altinstall   
	4. 错误处理
		* zipimport.ZipImportError: can't decompress data; zlib not available
			* 安装zlib相关依赖包
			* yum -y install zlib* 
	
4. 备份老版本   
    mv /usr/bin/python /usr/bin/python2.7.5   

5. 使用新版本   
    ln -s /usr/local/bin/python2.7 /usr/bin/python   

6. 配置yum   
    vi /usr/bin/yum   
    将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可

7. 配置urlgrabber-ext-down

  vi /usr/libexec/urlgrabber-ext-down

  将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可 

  

## 升级Python 3.10

1. 官网下载新版本Python   
   wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tgz
   
2. 解压

   ```shell
   tar zxvf Python-3.10.5.tgz
   ```

3. 升级openssl
   
   openssl的作用：pip安装其他包时需要使用openssl
   
   ```shell
   #下载源码包
   wget https://www.openssl.org/source/openssl-1.1.1q.tar.gz
   #解压
   tar -zxvf openssl-1.1.11q.tar.gz
   #进入文件夹
   cd openssl-1.1.1q/
   #配置指定安装目录
   ./config --prefix=/usr/local/openssl
   #编译安装
   make && make install
   #备份老版本的openssl
   mv /usr/bin/openssl /usr/bin/openssl.old
   mv /usr/include/openssl /usr/include/openssl.old
   #新建连接
   ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
   ln -s /usr/local/openssl/include/openssl /usr/include/openssl
   #库类文件
   echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
   #重载配置
   ldconfig
   ```
   
3. 编译安装
   
      ```shell
      cd Python-3.10.5/
      
      # 安装ssl
      ./configure --prefix=/usr/local/python3 --with-openssl=/usr/local/openssl
      
      # 编译安装
      make
      
      make install
      
      # 备份老版本
      mv /usr/bin/python /usr/bin/python_old2
      
      #将python链接指向新安装的python3
      ln -s /usr/local/python3/bin/python3  /usr/bin/python
      
      #查看python版本
      python -V
      ```
   
3. 修复yum和urlgrabber-ext-down不兼容的问题
   
   ```shell
   vi /usr/bin/yum
   ```
   
   将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可
   
   ```shell
   vi /usr/libexec/urlgrabber-ext-down
   ```
   
   将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可
   
3. 配置pip3
   
   ```shell
   vim /etc/profile
   
   #添加下面两行，将/usr/local/python3/bin加入到path中
   #PATH=$PATH:/usr/local/python3/bin/
   #export PATH
   
   source /etc/profile
   ```
   
   
