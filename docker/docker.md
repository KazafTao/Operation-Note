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

## 目录挂载

### 现存问题

- 运行容器后，改变镜像配置并不能立即生效，必须重新build和run，很是麻烦
- 容器里面产生的数据，例如log文件，数据库备份文件，容器删除后就丢失了

目录挂载可以解决以上问题

### 挂载方式

#### bind mount

直接将宿主机目录映射到容器里，适合挂代码和配置文件。可挂在多个容器上

在创建容器的时候使用 -v 参数指定宿主机和容器目录的映射关系，如 -v /var/jenkins_home:/var/jenkins_home

#### volume

由容器创建和管理，创建在宿主机，所以删容器不会丢失，官方推荐，更高效，Linux文件系统，适合存储数据库数据。

 在创建容器的时候使用--mount来指定数据卷和容器目录的映射关系，如--mount source=my-vol,target=/usr/share/nginx/html

数据卷

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

# 多容器通信

项目往往不是独立运行的，需要web服务器，数据库，缓存这些组件相互配合。组件容器之间需要相互通信。

## 创建虚拟网络

要想多容器之间互通，从web容器访问redis容器，只需要把这两个容器放到同一个网络里就可以了。

### 创建虚拟网络

```shell
docker network create test-net  
```

### 将容器运行在虚拟网络中

```shell
docker run -d --name redis --network test-net --network-alias redis redis:latest # --network参数指定容器运行的虚拟网络名
docker run -p 8080:8080 --name web_server  --network test-net -d wordpress:latest  # 将web容器与redis运行在同一个虚拟网络中
```

# Docker-compose

将多个不同配置的服务集合在一起，一键运行。容器网络会自动创建。

## 常用命令

### 运行docker-compose

```shell
docker-compose up
```

### 查看运行状态

```shell
docker-compose ps
```

### 停止运行

```shell
docker-compose stop
```

### 重启

```shell
docker-compose restart
```

### 重启单个服务

```shell
docker-compose restart service-name
```

### 进入容器命令行

```shell
docker-compose exec service-name sh
```

### 查看容器运行log

```shell
docker-compose logs [service-name]
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

