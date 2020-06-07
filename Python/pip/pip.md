# Pip 笔记
----
 - **设置pip源头地址**   
1. 直接在pip时加入参数   
pip install -i https://pypi.douban.com/simple  mysqldb   
2. 修改默认设置   
在根目录下创建.pip目录，创建pip.conf,并写入   
```
[global]
index-url = https://pypi.douban.com/simple

[install]
trusted-host=pypi.douban.com
```
