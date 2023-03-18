# ansible

Ansible是一种自动化IT工具，可以自动配置、部署和管理计算机系统。它是一种轻量级、开源、基于SSH协议的自动化工具，可以实现复杂的IT任务自动化，包括应用部署、配置管理、云管理、容器编排等。

Ansible使用YAML语言来描述任务和配置，易于阅读和编写，同时具备可扩展性和可重用性。它具有以下特点：

1. 简单易用：Ansible不需要安装客户端，只需要在控制节点上安装即可。
2. 高效可靠：Ansible使用基于SSH协议的远程执行方式，可以快速高效地执行任务。
3. 可扩展性：Ansible可以通过插件和模块来扩展其功能，满足不同的需求。
4. 可重用性：Ansible可以将常用的任务和配置封装成模块，以便在不同的场景中重复使用。
5. 安全性：Ansible使用SSH协议进行通信，支持多种身份验证方式，保证了数据的安全性。

总之，Ansible是一种非常强大的自动化工具，可以大大提高IT工作效率和质量，减少人为错误，是现代IT运维中不可或缺的一部分。

## 安装部署

1、配置好阿里云的epel源

2、使用yum安装ansible

```shell
yum install -y ansible
```

## 基本概念

### 主机清单

Ansible主机清单（Inventory）是一个文本文件，用于定义被管理的主机和主机组，以便Ansible可以对它们进行管理。主机清单可以包含多个组，每个组可以包含多个主机，也可以包含其他组。

主机清单可以使用INI格式或YAML格式编写，两种格式的主机清单都支持以下配置项：

- 主机名：指定被管理主机的名称或IP地址。
- 连接方式：指定连接主机的方式，如SSH、WinRM等。
- 身份验证：指定身份验证方式，如密码、密钥等。
- 变量：可以为每个主机或组定义变量，用于指定主机或组的特定配置。

主机清单的常见配置项如下：

```ini
[web]
192.168.1.100
192.168.1.101

[db]
192.168.1.102

[web:vars]
http_port=80
max_clients=1000

[db:vars]
db_port=3306
db_user=root
db_password=123456
```

以上示例定义了两个组web和db，web组包含两个主机，db组包含一个主机。同时为web组和db组定义了一些变量，如web组的http_port和max_clients，db组的db_port、db_user和db_password。

在Ansible的Playbook中可以使用主机清单中定义的主机和组，对它们进行配置、部署和管理。

#### 主机认证

在Ansible中，主机认证是指控制节点与被管理节点之间建立信任关系，以便控制节点可以通过SSH或WinRM等协议连接被管理节点，并执行相应的任务。

主机认证有两种方式：基于密码的认证和基于密钥的认证。

1. 基于密码的认证

基于密码的认证是指在控制节点和被管理节点之间建立SSH或WinRM连接时，需要输入用户名和密码进行身份验证。这种认证方式简单易用，但安全性较低，容易受到暴力破解等攻击。

在Ansible中，可以使用用户名和密码进行主机认证，具体方法如下：

```shell
ansible -m ping -u username -k hostname
```

其中，-m ping表示使用ping模块进行测试，-u username指定用户名，-k表示使用密码进行认证，hostname指定被管理的主机名或IP地址。

1. 基于密钥的认证

基于密钥的认证是指在控制节点和被管理节点之间建立SSH连接时，使用公钥和私钥进行身份验证。这种认证方式安全性较高，不容易受到攻击。

在Ansible中，可以使用基于密钥的认证，具体方法如下：

1. 生成SSH密钥对：

```shell
ssh-keygen
```

1. 将公钥复制到被管理节点：

```shell
ssh-copy-id -i ~/.ssh/id_rsa.pub username@hostname
```

1. 测试连接：

```shell
ansible -m ping hostname
```

其中，-m ping表示使用ping模块进行测试，hostname指定被管理的主机名或IP地址。

以上是基于SSH协议的认证方式，如果使用WinRM协议进行认证，可以使用WinRM证书进行身份验证。

### 模式

在Ansible中，模式（Mode）是指执行任务的方式和范围。Ansible支持多种模式，包括Ad-hoc模式、Playbook模式等。

1. Ad-hoc模式

Ad-hoc模式是指在命令行中执行一系列任务或模块，可以快速完成一些简单的管理任务。Ad-hoc模式可以使用ansible命令进行执行，例如：

```shell
ansible all -i hosts -m shell -a "ls -l"
```

其中，all表示执行所有主机，-i指定主机清单文件，-m指定使用的模块，-a指定模块的参数。

2. Playbook模式

Playbook模式是指通过Playbook文件定义任务和执行流程，可以对多个主机进行复杂的配置和管理。Playbook模式可以使用ansible-playbook命令进行执行，例如：

```shell
ansible-playbook -i hosts playbook.yml
```

其中，-i指定主机清单文件，playbook.yml是Playbook文件的名称。

以上是Ansible中常用的模式，根据需要可以选择不同的模式来执行任务和管理主机。

## 模块

### command

在Ansible中，"command"模块用于在目标主机上执行一条命令，并返回命令的结果。它支持大量的通用操作系统命令和Shell命令，例如查看文件内容、创建目录、复制文件等。

"command"模块最基本的语法如下：

```yaml
- name: Execute a command on target host
  command: <command_to_execute>
```

其中，<command_to_execute>代表要在目标主机上执行的命令。

除了基本语法外，"command"模块还支持很多参数，可以根据实际情况进行配置。例如，可以使用"chdir"参数来指定命令执行的工作目录；使用"creates"参数来指定命令执行后要创建的文件；使用"removes"参数来指定命令执行后要删除的文件等等。

总的来说，"command"模块是Ansible的一个非常重要的模块，它可以帮助用户快速有效地执行各种命令，并简化管理操作。

### shell

ansible shell模块是ansible的一个核心模块之一，它允许用户在远程主机上执行shell命令。与其他模块不同，shell模块不需要在远程主机上安装任何软件，因为它使用的是远程主机上已经存在的shell。使用ansible shell模块可以轻松地在远程主机上执行命令，例如创建文件、修改文件权限、安装软件等。

以下是一个ansible shell模块的简单示例：

```yaml
- name: 在远程主机上执行shell命令
  hosts: webserver
  tasks:
    - name: 创建一个新的目录
      shell: mkdir /opt/mydir
      become: true
```

在这个示例中，我们使用ansible shell模块在远程主机上执行了一个shell命令，即创建一个新的目录。使用become参数可以让ansible在远程主机上以超级用户权限执行命令。

### copy

在Ansible中，copy模块用于在管理节点和远程节点之间复制文件。

使用copy模块，可以将本地文件复制到远程节点，也可以将远程节点上的文件复制到本地。

以下是copy模块的基本语法：

```yaml
- name: Copy a file from source to destination
  copy:
    src: /path/to/source/file
    dest: /path/to/destination/file

- name: Copy a directory from source to destination
  copy:
    src: /path/to/source/directory/
    dest: /path/to/destination/directory/
    recursive: yes
```

其中src表示源文件或目录路径，dest表示目标文件或目录路径。如果要复制目录，需添加recursive选项，以确保整个目录及其内容都被复制。

除此之外，copy模块还支持其他一些选项，例如设置权限、所有权和组、设置远程用户等。详情请参考官方文档。

### file

`file`模块是Ansible中的一个核心模块之一，它允许你在目标主机上创建、修改和删除文件和目录。

使用`file`模块，你可以完成以下操作：

- 创建临时文件或目录
- 修改文件的权限、所有者和组
- 创建符号链接
- 删除文件或目录
- 复制文件

以下是一些常见的`file`模块的示例：

1. 创建一个空白的文件：

   ```yaml
   - name: Create empty file
     file:
       path: /path/to/file.txt
       state: touch
   ```

2. 修改文件的权限：

   ```yaml
   - name: Change file permissions
     file:
       path: /path/to/file.txt
       mode: 0644
   ```

3. 创建一个目录：

   ```yaml
   - name: Create directory
     file:
       path: /path/to/directory
       state: directory
   ```

4. 删除一个文件：

   ```yaml
   - name: Remove file
     file:
       path: /path/to/file.txt
       state: absent
   ```

5. 复制一个文件：

   ```yaml
   - name: Copy file
     file:
       src: /path/to/source/file.txt
       dest: /path/to/destination/file.txt
       mode: 0644
   ```

这些只是`file`模块的一些基本用法，如果需要更多高级的功能，还可以参考Ansible的官方文档。

### service

Ansible的service模块用于管理系统服务的状态，例如启动、停止、重启服务。它可以在Linux和Unix系统上工作，并且支持大多数服务管理工具。

使用service模块需要指定服务名称和要执行的操作，例如：

```yaml
- name: Start Apache service
  service:
    name: httpd
    state: started
```

这个任务将启动名为httpd的Apache服务。如果服务已经运行，则不会有任何影响。

除了启动服务，service模块还支持停止、重启、重新加载和重新启动服务。例如：

```yaml
- name: Stop Apache service
  service:
    name: httpd
    state: stopped

- name: Restart Apache service
  service:
    name: httpd
    state: restarted

- name: Reload Apache service
  service:
    name: httpd
    state: reloaded
```

在这些任务中，state参数指定了要执行的操作，可以是started、stopped、restarted或reloaded。

除了基本的操作，service模块还支持更高级的用法，例如在服务启动后等待一段时间，或者在服务启动之前检查某些条件。这些用法可以通过添加其他参数来实现，例如：

```yaml
- name: Start Apache service and wait for it to be ready
  service:
    name: httpd
    state: started
    sleep: 5
    enabled: yes

- name: Start Apache service only if it is not already running
  service:
    name: httpd
    state: started
    enabled: yes
    only_if: "ps aux | grep httpd | grep -v grep"
```

在第一个任务中，sleep参数指定了等待服务启动的时间（以秒为单位），而enabled参数指定了在系统启动时启用服务。在第二个任务中，only_if参数指定了在启动服务之前检查的条件，如果条件不满足，则不会启动服务。

### package

Ansible的package模块用于在Linux和Unix系统上管理软件包。它支持多种包管理工具，例如yum、apt、dnf等。

使用package模块需要指定软件包名称和要执行的操作，例如：

```yaml
- name: Install Apache package
  package:
    name: httpd
    state: present
```

这个任务将安装名为httpd的Apache软件包。如果软件包已经安装，则不会有任何影响。

除了安装软件包，package模块还支持卸载、更新、安装指定版本的软件包。例如：

```yaml
- name: Uninstall Apache package
  package:
    name: httpd
    state: absent

- name: Update Apache package
  package:
    name: httpd
    state: latest

- name: Install specific version of Apache package
  package:
    name: httpd
    state: present
    version: 2.4.6-90.el7.centos
```

在这些任务中，state参数指定了要执行的操作，可以是present（安装）、absent（卸载）、latest（更新）或指定软件包的特定版本。

除了基本的操作，package模块还支持更高级的用法，例如从本地文件系统安装软件包、安装软件包时不检查依赖关系等。这些用法可以通过添加其他参数来实现，例如：

```yaml
- name: Install Apache package from local file
  package:
    name: /tmp/httpd.rpm
    state: present

- name: Install Apache package without checking dependencies
  package:
    name: httpd
    state: present
    skip_broken: yes
```

在第一个任务中，name参数指定了要安装的本地文件路径，而在第二个任务中，skip_broken参数指定了在安装软件包时不检查依赖关系。

### script

Ansible Script模块是一个通用模块，它允许您在目标主机上执行任何脚本或命令。这个模块是非常强大的，因为它允许您在Ansible中使用任何脚本语言，例如Python、Bash、Perl等。Script模块可以在目标主机上执行本地脚本或从控制节点复制脚本并在目标主机上执行。

下面是一些常见的用例：

1. 在目标主机上执行本地脚本：使用Script模块可以在目标主机上执行本地脚本。例如，您可以使用Script模块在目标主机上执行Bash脚本，以检查系统状态或执行一些特定的任务。

2. 从控制节点复制脚本并在目标主机上执行：使用Script模块，您可以将脚本从控制节点复制到目标主机并在目标主机上执行。这对于在目标主机上安装软件包或配置文件非常有用。

以下是Script模块的语法：

```yaml
- name: Execute script
  script: /path/to/script.py
```

在上面的例子中，Script模块将在目标主机上执行指定的Python脚本。

Script模块也支持一些选项，例如在脚本执行之前或之后运行特定的命令。例如：

```yaml
- name: Execute script
  script: /path/to/script.sh
  args:
    chdir: /path/to/script/directory
    executable: /bin/bash
```

在上面的例子中，Script模块将在指定的目录中执行Bash脚本，并使用/bin/bash作为解释器。

总之，Script模块是一个非常强大和灵活的模块，允许您在目标主机上执行任何脚本或命令。

### cron

Ansible的cron模块用于在Linux和Unix系统上管理cron作业。它允许用户在指定的时间执行命令或脚本。

使用cron模块需要指定cron作业的名称、要执行的命令或脚本、以及执行的时间。例如：

```yaml
- name: Add cron job to run script every hour
  cron:
    name: "Run script every hour"
    minute: "0"
    hour: "*"
    job: "/path/to/script.sh"
```

这个任务将在每个小时的0分钟运行名为“Run script every hour”的cron作业，该作业将执行位于“/path/to/script.sh”的脚本。

除了添加cron作业，cron模块还支持删除、更改、启用和禁用cron作业。例如：

```yaml
- name: Remove cron job
  cron:
    name: "Run script every hour"
    state: absent

- name: Change cron job time
  cron:
    name: "Run script every hour"
    minute: "30"
    hour: "*"
    job: "/path/to/script.sh"

- name: Enable cron job
  cron:
    name: "Run script every hour"
    state: present
    enabled: yes

- name: Disable cron job
  cron:
    name: "Run script every hour"
    state: present
    enabled: no
```

在这些任务中，state参数指定了要执行的操作，可以是present（添加或更改cron作业）或absent（删除cron作业），而enabled参数指定了是否启用cron作业。

除了基本的操作，cron模块还支持更高级的用法，例如在cron作业执行之前检查某些条件、指定特定的用户执行cron作业等。这些用法可以通过添加其他参数来实现，例如：

```yaml
- name: Add cron job to run script only if file exists
  cron:
    name: "Run script every hour"
    minute: "0"
    hour: "*"
    job: "/path/to/script.sh"
    user: myuser
    only_if: "test -e /path/to/file.txt"
```

在这个任务中，only_if参数指定了在执行cron作业之前要检查的条件，如果条件不满足，则不会执行cron作业。

## 例子

### 部署服务

```yaml
- name: 部署runner服务
  hosts: server
  become: true
  tasks:
    - name: 发送压缩包
      copy:
        src: /tmp/deploy.zip
        dest: /tmp/deploy.zip

    - name: 安装 EPEL 存储库
      yum:
        name: epel-release  #安装python需要先装这个
        state: present

    - name: 安装依赖
      yum:
        name:
          - unzip
          - python-setuptools  #使用pip安装需要装这个
          - python3
          - python3-setuptools
        state: present

    - name: 解压
      unarchive:
        src: /tmp/deploy.zip
        dest: /tmp
        copy: no

    - name: 安装flask依赖
      pip:
        requirements: /tmp/deploy/requirement.txt
        executable: /usr/bin/pip3

    - name: 启动flask
      command:
        python3 /tmp/deploy/main.py
```

