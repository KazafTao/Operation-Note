# Python 笔记
----
 - **升级Python 2.7.5**
1. 官网下载新版本Python   
wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz   
2. 解压   
tar zxvf Python-2.7.18.tgz   
3. 编译安装   
cd Python-2.7.18   
./configure --enable-optimizations   
```make altinstall用于防止替换默认的python二进制文件/usr/bin/python。```
make altinstall   
4. 备份老版本   
mv /usr/bin/python /usr/bin/python2.7.5   
5. 使用新版本   
ln -s /usr/local/bin/python2.7 /usr/bin/python   
6. 配置yum   
vi /usr/bin/yum   
将 #!/usr/bin/python 改为 #!/usr/bin/python2.7，保存退出即可   
