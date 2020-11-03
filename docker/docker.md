---
title: Docker
tags: docker,虚拟化
renderNumberedHeading: true
grammar_cjkRuby: true
---
## Docker的主要特点

 - 封装
	 - 将环境打包成镜像，确保运行环境一致
 - 标准化
	 - 运输的标准化
	 - 命令的标准化
 - 隔离性
	 - 运行镜像时，单独在Linux内核开辟一片空间


----------

## Docker的基本操作

 - 安装docker
	 - 国内dao云一键安装
		 - curl -sSL https://get.daocloud.io/docker | sh
 - 设置docker镜像源
	 - 创建目录
		 - mkdir /etc/docker
	 - 编辑文件
		 - vim /etc/docker/daemon.json
		 -   ``` 
				  {
						 "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"] 
				   } 
				 ``` 
		- systemctl restart docker

