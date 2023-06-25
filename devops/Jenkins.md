# Jenkins

- [Jenkins](#jenkins)
  - [工作原理](#工作原理)
  - [部署](#部署)
    - [本地部署](#本地部署)
    - [容器部署](#容器部署)
  - [构建任务](#构建任务)
    - [构建方式](#构建方式)
  - [基础概念](#基础概念)
    - [job](#job)
    - [plugin](#plugin)
    - [workspace](#workspace)
  - [流水线](#流水线)
    - [语法](#语法)


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

## 构建任务

在Jenkins中配置和管理构建任务需要遵循以下步骤：

1. 登录到Jenkins服务器，并进入Jenkins控制台。
2. 点击“新建任务”按钮，然后选择“自由风格的软件项目”或其他合适的选项。 
3. 输入任务的名称并添加描述，然后点击“确定”按钮。
4. 在任务配置页面，配置源代码管理、构建触发器、构建环境等详细信息。
5. 在“构建”部分，添加构建步骤，例如编译、打包、测试等。
6. 配置构建后操作，例如邮件通知、构建报告等。
7. 点击“保存”按钮保存任务配置。

除了以上步骤外，还可以使用Jenkins提供的Pipeline功能来创建更为复杂的构建流程。可以通过编写Jenkinsfile文件来定义整个流程的各个阶段和步骤。

### 构建方式

Jenkins提供了多种构建方式，其中一些常见的包括：

1. 自由风格：这是Jenkins的默认构建方式，它允许用户在构建过程中自由地配置各种参数和构建步骤。

2. 流水线：流水线是一种使用Jenkinsfile定义的脚本化构建方式。通过Jenkinsfile，用户可以将构建过程分为多个阶段，并针对每个阶段定义不同的操作。

3. Maven构建：Jenkins可以与Maven集成，使用Maven构建项目并管理依赖项。(通过Maven Integration插件)

4. Gradle构建：Jenkins还可以与Gradle集成，使用Gradle构建项目并管理依赖项。

5. Ant构建：Jenkins也支持使用Apache Ant构建项目。

除了上述构建方式外，Jenkins还支持其他构建方式，如Shell脚本、Batch脚本等。用户可以根据自己的需要选择适合自己的构建方式。

## 基础概念

### job

在jenkins中，都是以job为单位去完成一件事

### plugin

jenkins提供平台，集成各种插件来完成一个job。想用jenkins平台做什么，先找找有没有相应的插件。如windows命令、Linux命令的支持、svn和git代码的获取，邮件发送，测试报告集成等都需要安装相应的插件才能在jenkins中使用这些功能。

### workspace

jenkins通过文件形式来存储和管理数据。jenkins所有数据都存放在jenkins_home目录下，包括配置和数据。每个job也有自己的workspace，存放本任务涉及到数据和文件。

jenkins_home目录可以在jenkins主页的系统管理  >  系统配置中查看。

## 流水线

### 语法

Jenkins Pipeline是一种可定义基于代码的语法，它允许您创建持续交付（CD）流水线。以下是Jenkins流水线语法的基本元素：

1. 声明Pipeline：使用`pipeline`关键字声明Pipeline。

2. 代理节点：使用`agent`关键字指定构建应该在哪个节点上运行。

3. 阶段（Stage）：使用`stage`关键字声明一个阶段，并给该阶段命名。阶段用于将构建过程分解为多个步骤，并且可以在Jenkins Pipeline中显示每个步骤所需的时间和状态。

4. 步骤（Step）：在阶段内使用各种构建步骤，如`sh`步骤（执行Shell命令）、`echo`步骤（输出消息到控制台）、`git`步骤（拉取代码）等。

5. 环境（Environment）：使用`environment`关键字设置环境变量。这些变量可以在流水线的任何步骤中使用。

6. 参数化（Parameterization）：使用`parameters`关键字将参数传递给Pipeline。这使得用户可以在运行Pipeline时更改某些值。

以下是一个简单的Jenkins Pipeline示例：

```
pipeline {
    agent any 
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello, World!"'
            }
        }
    }
}
```

此示例声明了一个基本的Pipeline，其中有一个阶段（Build），该阶段包含一个步骤（echo），以输出“Hello, World!”消息。

## 参与过的项目

### dataconnect 自动化构建

