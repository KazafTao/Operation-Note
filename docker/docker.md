# Docker的主要特点

 - 封装
	 - 将环境打包成镜像，确保运行环境一致
 - 标准化
	 - 运输的标准化
	 - 命令的标准化
 - 隔离性
	 - 运行镜像时，单独在Linux内核开辟一片空间


# Docker的基本操作
## 安装
- 安装docker
	- 国内dao云一键安装
	
	  ```shell
	  curl -sSL https://get.daocloud.io/docker | sh
	  ```
	
	-  测试docker是否安装成功
	
	  ```shell
	   docker run hello-world
	  ```
	
## 设置docker镜像源

1. 创建目录
   
   ```shell
   mkdir /etc/docker
   ```
   
2. 编辑文件
   
   ```shell
   vim /etc/docker/daemon.json
   {
   	"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"] 
   }
   ```

3. 重启docker

     ```shell
     systemctl restart docker 
     ```

## 镜像相关操作
### 拉取镜像
```shell
docker pull 镜像名[:tag]
```
### 查看镜像
```shell
docker images
```
### 删除本地镜像
```shell
docker rmi 镜像id
```
### 镜像的导入导出 (网络质量差时使用，会丢失原镜像的标签)
#### 导出
```shell
docker save -o 导出镜像文件路径 镜像id
```
#### 导入
```shell
docker load -i 镜像文件路径
```
### 修改镜像名
```shell
docker tag 镜像id 镜像名:标签
```

## 容器相关操作
### 查看正在运行的容器
```shell
docker ps 
```
- 常用参数
  - -a 所有容器
  - -q 只查看容器标识

### 进入容器

```shell
docker exec -it 容器id bash
```
### 运行容器
```shell
docker run 镜像id / 镜像名    	
```
- 常用参数
        	- -d 后台运行
        - -p 宿主机端口:容器端口  端口映射
        - -name 容器名称
        - -v 数据卷名称 / 路径 : 容器内路径
        - --privileged 给容器提权，容器内的root拥有真正的root权限

### 查看容器日志

```shell
docker logs -f 容器id  #-f 表示可以滚动查看日志的最后几行
```
### 删除容器

```shell
docker stop $(docker ps -qa 容器id)
docker remove $(docker ps -qa 容器id)
```
### 启动容器
    docker start 容器id

## 数据卷相关操作
### 创建数据卷
```shell
docker volume create 数据卷名
```
### 查看数据卷
```shell
docker volume ls
```
### 查看某个数据卷的详细信息
```shell
docker volume inspect 数据卷名
```
### 删除数据卷
```shell
docker volume rm 数据卷名
```

# Dockerfile
from 原镜像
copy 宿主机路径:容器内路径

## Docker的中央仓库

 - 国内镜像
	 - 网易蜂巢
	 - daocloud
		 - hub.daocloud.io (免登录)
	 - 阿里镜像
 - 公司内私服

