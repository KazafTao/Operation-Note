# Pip 笔记
----
## 设置pip源头地址 
1. 直接在pip时加入参数   
pip install -i https://pypi.douban.com/simple  mysqldb   
2. 修改默认设置   
在根目录下创建.pip目录，创建pip.conf,并写入   
```
[global]
index-url=https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```
## 查看哪些包需要更新

  ```shell
  pip3 list --outdated
  ```

## pip升级包

 ```shell
 pip install --upgrade 要升级的包名
 ```

 
