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

## 升级Python 3.10

1. 官网下载新版本Python   
   wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tgz
   
2. 解压

   ```shell
   tar zxvf Python-3.10.5.tgz
   ```

3. 编译安装   
   
   ```shell
   cd Python-3.10.5/
   
   # 安装ssl
   ./configure --with-ssl --prefix=/usr/local/python3
   
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
   
   
