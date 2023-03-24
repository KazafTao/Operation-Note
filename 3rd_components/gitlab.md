# gitlab

- [gitlab](#gitlab)
  - [安装部署](#安装部署)


## 安装部署

要在CentOS上部署GitLab，可以按照以下步骤进行操作：

1. 在CentOS中安装必要的软件包：GitLab需要安装一些必要的软件包，如curl、policycoreutils-python、及PostgreSQL等。可通过以下命令来安装：

```shell
sudo yum install -y curl policycoreutils-python openssh-server wget curl cronie
sudo yum install postfix
sudo systemctl enable postfix && sudo systemctl start postfix
sudo firewall-cmd --permanent --add-service=http --add-service=https
sudo systemctl reload firewalld
```

2. 下载并安装GitLab：可以从GitLab官网下载最新版本的GitLab Community Edition。请确认您选择了正确的GitLab版本，并正确设置路径和文件名。

```shell
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

#EXTERNAL_URL="http://your-gitlab-domain.com" 可以改为EXTERNAL_URL="http://192.168.98.138"
sudo EXTERNAL_URL="http://your-gitlab-domain.com" yum -y install gitlab-ee
```

3. 配置GitLab：首次启动GitLab时，需要进行一些基本配置。这包括设置管理员帐号密码、电子邮件通知设置、备份设置等。您可以按照屏幕提示进行操作，或者在后续时间内进入您的GitLab管理页面进行更改。

4. 启动GitLab：完成配置后，可以通过以下命令来启动GitLab：

```shell
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

5. 访问GitLab：您现在应该可以通过web浏览器访问您的GitLab实例了。在浏览器中输入您的服务器IP地址或域名，并输入您在第三步中设置的管理员帐号密码以登录您的GitLab。

以上就是在CentOS上部署GitLab的基本步骤。如果您遇到任何问题，请查阅GitLab文档或寻求技术支持。

安装成功后，root初始密码在/etc/gitlab/initial_root_password这个文件上

```shell
cat /etc/gitlab/initial_root_password
```

