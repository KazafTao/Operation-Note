# Jenkins

Jenkins是一个开源的持续集成和持续交付工具，用于自动化构建、测试和部署软件项目。它提供了可扩展的插件架构，可以与各种不同的工具和技术进行集成，从而实现自动化构建、测试、发布和部署。

Jenkins可以管理整个软件开发周期中的各个阶段，包括代码的检查、编译、测试、静态分析、文档生成、打包、部署等。通过使用Jenkins，开发团队可以快速有效地构建和交付高质量的软件产品，从而提高生产效率和软件质量。

## 工作原理

Jenkins是一种持续集成和持续交付的工具，它的主要工作原理如下：

1. 获取源代码：Jenkins通过支持各种版本控制系统和源码管理工具来获取源代码。
2. 构建任务执行：Jenkins根据预定义的构建任务执行构建过程。构建任务包括编译、打包、测试等步骤，并在构建结束时生成构建报告。
3. 触发器：Jenkins支持多种触发方式，例如定时触发、SCM变更触发、API触发等。当触发条件满足时，Jenkins会自动开始构建任务。
4. 插件：Jenkins有丰富的插件生态系统，可以通过插件实现对其功能的扩展，比如添加额外的构建任务、测试框架、通知工具等。
5. 集成：Jenkins可以与其他工具集成，比如构建工具、测试框架、部署工具等，从而实现自动化的构建、测试和部署流程。

总之，**Jenkins的工作原理就是通过不断地拉取源代码和执行构建任务来实现自动化的持续集成和交付流程**，从而提高软件开发过程中的效率和质量。

## 部署

### 本地部署

要在CentOS上部署Jenkins，您可以按照以下步骤进行操作：

1. 安装Java：Jenkins需要Java运行环境。您可以使用以下命令安装Java：

   ```shell
   #jenkins最低支持java 
   yum install fontconfig java-11-openjdk
   ```

2. 启用Jenkins YUM存储库：您可以使用以下命令启用Jenkins YUM存储库：

   ```shell
   sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo --no-check-certificate
   sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
   ```

3. 安装Jenkins：使用以下命令安装Jenkins：

   ```shell
   sudo yum install -y jenkins
   ```

4. 启动Jenkins服务：使用以下命令启动Jenkins服务并设置其随系统启动：

   ```shell
   # 防止jenkins因为ipv6启动超时
   sysctl -w net.ipv6.conf.all.disable_ipv6=1
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```

5. 访问Jenkins Web界面：在您的Web浏览器中输入以下地址访问Jenkins Web界面：

   ```
   http://your_server_ip_or_domain_name:8080/
   ```

6. 初始化Jenkins：在首次访问Jenkins Web界面时，您需要使用管理员密码初始化Jenkins。您可以使用以下命令查找管理员密码：

   ```shell
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

   复制密码并粘贴到Jenkins Web界面中，然后按照提示完成Jenkins的初始化。

这些步骤应该能够帮助您在CentOS上成功部署Jenkins。

### 容器部署

拉取镜像

```shell
docker pull jenkins/jenkins:lts 
```

运行容器

```shell
docker run --privileged -p 8080:8080 -v /var/jenkins:/var/jenkins_home jenkins/jenkins:lts
```

## 基础概念

### job

在jenkins中，都是以job为单位去完成一件事

### plugin

jenkins提供平台，集成各种插件来完成一个job。想用jenkins平台做什么，先找找有没有相应的插件。如windows命令、Linux命令的支持、svn和git代码的获取，邮件发送，测试报告集成等都需要安装相应的插件才能在jenkins中使用这些功能。

### workspace

jenkins通过文件形式来存储和管理数据。jenkins所有数据都存放在jenkins_home目录下，包括配置和数据。每个job也有自己的workspace，存放本任务涉及到数据和文件。

jenkins_home目录可以在jenkins主页的系统管理  >  系统配置中查看。

## 功能

### 创建job

在主页点击新建任务

![image-20220830170519555](C:\Users\wade\AppData\Roaming\Typora\typora-user-images\image-20220830170519555.png)

### 从git下载代码

首先要下载git插件

在主页点击新建任务，创建自由风格的软件项目，点击确定

在源码管理下，选择git，在Repository URL中填入git代码的地址，如https://github.com/KazafTao/AppStatus.git。在Credentials中选择对应的凭证，如github中选择github的账号。没有github凭证的需要先创建github凭证。(选择用户密码凭证，输入github的账号密码)

保存任务。

点击立即构建即可下载代码

### 定时构建

从job中点击配置，选择构建触发器，勾选**定时构建**，填写需要构建的时间。

jenkins定时构建遵循以下定时语法:( * 表示所有取值 )

分钟  小时  一月的天数 月份 一周的天数

0-59  0-23 1-31 1-12  0-7(0和7都代表周日) 

如每周一，周三，周五晚上8点构建一次的表达式为 0 20 * * 1,3,5 。一周内每两天，晚上8点执行一次构建，表达式为 0 20 * * */2

