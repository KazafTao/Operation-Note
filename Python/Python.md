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

  

## 升级Python 3.11

1. ```shell
      cd /root
      #只是将python3.11的安装包下载到 /root目录下
      wget https://www.python.org/ftp/python/3.11.0/Python-3.11.0.tgz
      #下载最新的软件安装包
      tar -zxvf Python-3.11.0.tgz
      #解压缩安装包
      yum -y install gcc zlib zlib-devel libffi libffi-devel
      #安装源码编译需要的编译环境
      yum install readline-devel
      #可以解决后期出现的方向键、删除键乱码问题，这里提前避免。
      yum install openssl-devel openssl11 openssl11-devel
      #安装openssl11，后期的pip3安装网络相关模块需要用到ssl模块。
      export CFLAGS=$(pkg-config --cflags openssl11)
      export LDFLAGS=$(pkg-config --libs openssl11)
      #设置编译FLAG，以便使用最新的openssl库
      cd /root/Python-3.11.0
      #进入刚解压缩的目录
      ./configure --prefix=/usr/python --with-ssl --with-sqlite3 --with-threads
      #指定python3的安装目录为 /usr/python 并使用ssl模块，指定目录好处是
      #后期删除此文件夹就可以完全删除软件了。
      make && make install
      #就是源码编译并安装了，时间会持续几分钟。
      ln -s /usr/python/bin/python3 /usr/bin/python3
      ln -s /usr/python/bin/pip3 /usr/bin/pip3
      #指定链接，此后我们系统的任何地方输入python3就是我们安装的
      #这个最新版python3了
      ```
      
1. 修复yum和urlgrabber-ext-down不兼容的问题
   
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
   
   
