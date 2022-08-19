# Jenkins

# 部署

## 容器部署

拉取镜像

```shell
docker pull jenkins/jenkins:lts 
```

运行容器

```shell
docker run --privileged -p 8080:8080 -v /var/jenkins:/var/jenkins_home jenkins/jenkins:lts
```

