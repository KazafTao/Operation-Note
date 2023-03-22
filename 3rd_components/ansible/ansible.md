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

### playbook

Ansible Playbook是一种用于自动化IT任务的描述性语言，它基于YAML格式，与Ansible Engine配合使用。Playbook中包含了一系列任务，每个任务定义了一组操作和参数，以及执行这些操作的主机和用户信息。与单独执行Ansible模块不同，Playbook将一系列操作集成到一个文件中，并提供展示、注释和调试的功能。

以下是Ansible Playbook的一些特点：

1. 剧本（Play）：Play是一个包含一组任务的有序列表，使您能够计划需要在清单（Inventory）上执行的操作

2. 任务（Task）：Task是Play的最小单位，表示单个操作。任务是以模块的形式呈现的，每个模块都会执行特定的操作

3. 模块（Module）：Ansible的核心部分就是模块，它们是执行任务的工具，例如控制服务、更新软件包或配置文件

4. 变量（Variable）：变量可以用于在不同的Playbook和任务之间共享和重复使用值

5. 注释（Comment）：使用注释可以帮助解释和组织剧本，对于日后的维护很有帮助

6. 循环（Loop）：可以使用循环来重复执行一组任务，可以减少编写代码的工作量

7. 错误处理：Playbook可以包含条件语句，根据特定条件自动选择任务和操作，以便在出现错误时进行处理。

通过使用Ansible Playbook，可以实现自动化配置、软件安装、服务部署、监控等任务，减轻管理员的压力，提升效率和可靠性。并且Playbook对于故障排查也非常有用，因为操作步骤都是事先定义好的，方便追踪问题并进行记录。

### 模块

Ansible的模块（Modules）是用于执行操作的基本工具。通过Ansible的模块，可以在目标系统上执行各种操作，例如安装软件包、配置文件、启动服务等。

以下是Ansible的模块特点：

1. 结构化输出：每个模块都会返回结构化的输出，方便管理者快速查看该操作的结果。

2. 封装功能：每个模块都封装了特定功能，使得管理员能够更为高效地部署和管理系统。

3. 可扩展性：Ansible允许自定义和编写新的模块，并且社区提供了大量的可用模块来满足不同的需求。

4. 幂等性：大多数模块都支持幂等性操作，即重复执行对于系统状态没有影响。

5. 跨平台性：Ansible的模块可以跨越不同的操作系统和云服务平台使用。

6. YAML格式：模块是基于YAML格式定义的，这种简单易懂的语法使得模块的编写和调试变得更加简单。

Ansible模块不需要在远程主机上安装任何额外软件，它们只需要python解释器的支持，这也是Ansible与其他自动化工具的一个显著区别。Ansible模块可以通过SSH、WinRM或其他通信模块，在远程系统上直接执行任务，这保证了其安全性和可靠性。

总之，Ansible的模块是实现自动化任务和系统部署的核心组件，通过使用各种不同的模块，管理者可以轻松自动化大部分的系统管理任务。

## 常用模块

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

## Playbook编写

编写符合最佳实践的Ansible Playbook需要严谨的思维和清晰的逻辑，以下是一个简单的示例来帮助您理解如何编写Ansible Playbook：

### 第一步：定义变量

在Playbook开始之前，需要定义变量，以便在任务中使用。变量可以定义为全局变量或限定于特定主机组的变量。例如：

```yaml
---
- name: Playbook 示例
  hosts: webservers
  become: true
  vars:
    app_dir: /var/www/myapp
    db_host: localhost
    db_port: 3306
```

### 第二步：定义任务

在Playbook中的任务可以使用系统内置的模块，也可以使用自定义的模块来执行各种操作。任务可以按顺序执行，并且可以在不同的主机或主机组上执行不同的任务。例如：

```yaml
---
- name: Playbook 示例
  hosts: webservers
  become: true
  vars:
    app_dir: /var/www/myapp
    db_host: localhost
    db_port: 3306
  tasks:
    - name: 安装 Apache2
      apt:
        name: apache2
        state: present
  
    - name: 备份 Apache 配置文件
      command: cp /etc/apache2/httpd.conf /etc/apache2/httpd.conf.bak

    - name: 配置 Apache2 虚拟主机
      template:
        src: templates/vhost.conf.j2
        dest: /etc/apache2/sites-available/myapp.conf
  
    # 在另外一个主机上执行脚本
    - name: 在数据库服务器上创建数据库
      script: create_db.sh
      delegate_to: "{{ db_host }}"
  
    - name: 启动 Apache2
      service:
        name: apache2
        state: started
        enabled: true
```

在上述示例中，我们首先定义了两个变量，然后定义了多个任务，这些任务分别安装并配置Apache、备份Apache配置文件、创建一个虚拟主机、在远程主机上执行脚本、启动Apache服务。

### 第三步：传递变量

在任务中，可以使用变量来实现对主机属性的引用，也可以使用变量传递值给其他任务。例如：

```yaml
---
- name: Playbook 示例
  hosts: webservers
  become: true
  vars:
    app_dir: /var/www/myapp
    db_host: localhost
    db_port: 3306
  tasks:
    # 安装 PHP-fpm
    - name: 安装 PHP-fpm 
      apt:
        name: php-fpm
        state: present
  
    # 配置 Nginx 虚拟主机
    - name: 配置 Nginx 虚拟主机
      template:
        src: templates/nginx_vhost.conf.j2
        dest: /etc/nginx/sites-available/myapp.conf
      notify: 重启 Nginx 服务
  
    # 启动 PHP-fpm
    - name: 启动 PHP-fpm
      service:
        name: php-fpm
        state: started
        enabled: true
  
  handlers:
    - name: 重启 Nginx 服务
      service:
        name: nginx
        state: restarted
```

在上述示例中，我们使用变量`app_dir`和`db_host`来配置Nginx虚拟主机和启动PHP-fpm服务，使用任务通知机制重新启动Nginx服务。

### 第四步：条件判断

在Ansible Playbook中，可以使用条件判断来根据特定的条件选择要执行的任务。常用的条件判断语句包括when和failed_when。例如：

```yaml
---
- name: Playbook 示例
  hosts: webservers
  become: true
  vars:
    app_dir: /var/www/myapp
    db_host: localhost
    db_port: 3306
  tasks:
    # 安装 PHP-fpm
    - name: 安装 PHP-fpm 
      apt:
        name: php-fpm
        state: present
  
    # 部署应用程序
    - name: 部署应用程序
      copy:
        src: /path/to/app
        dest: "{{ app_dir }}"
      when: deploy_app | default(true)
  
    # 启动 PHP-fpm
    - name: 启动 PHP-fpm
      service:
        name: php-fpm
        state: started
        enabled: true
  
  vars:
    deploy_app: false
```

在上述示例中，我们设置了一个变量`deploy_app`，如果该变量被设置为false，则任务“部署应用程序”将不会被执行。

### 第五步：循环操作

在Ansible Playbook中，可以使用循环来重复执行一组任务，并缩短代码的长度。循环操作可以遍历列表、字典、文件、查询等数据类型。例如：

```yaml
---
- name: Playbook 示例
  hosts: webservers
  become: true
  vars:
    apps:
      - name: myapp1
        repo: https://github.com/myapp1.git
        path: /var/www/myapp1
      - name: myapp2
        repo: https://github.com/myapp2.git
        path: /var/www/myapp2
  tasks:
    # 克隆 Git 仓库
    - name: 克隆 Git 仓库
      git:
        repo: "{{ item.repo }}"
        dest: "{{ item.path }}"
      with_items: "{{ apps }}"
  
    # 启动 PHP-fpm
    - name: 启动 PHP-fpm
      service:
        name: php-fpm
        state: started
        enabled: true
```

在上述示例中，我们使用循环操作遍历列表`apps`，克隆Git仓库，并启动PHP-fpm服务。

以上是一个简单的Ansible Playbook示例，通过任务定义、变量传递、条件判断、循环操作等实现了自动化部署和管理任务。当然，在实际工作中，还需要根据具体的需求进行更加复杂的操作和处理。

## Inventory管理

Ansible主机清单是指向Ansible提供要管理的目标主机或主机群组列表。它通常包含主机名、主机别名、IP地址、登录凭据、变量和其他有关目标主机的信息。Ansible可以通过多种方式管理主机清单，包括文件格式、变量设置和动态清单等。

### 主机清单文件格式

主机清单可以按照INI或YAML格式编写。INI格式使用`[组名]`来定义主机组，而YAML格式使用连字号（`-`）来分隔不同的主机组。以下是示例：

#### INI 格式

```INI
[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com
db2.example.com
```

#### YAML 格式

```yaml
---
webservers:
  hosts:
    web1.example.com:
    web2.example.com:

databases:
  hosts:
    db1.example.com:
    db2.example.com:
```

### 变量设置

主机清单可以包含变量，这些变量可以分配给单个主机、整个主机组或所有目标主机。您可以在主机清单中为变量赋值，也可以在外部文件或目录中定义变量信息。最常用的方法是从外部文件中加载变量到Ansible中，例如：

```INI
[webservers]
web1.example.com http_port=80 max_clients=200
web2.example.com http_port=8080 max_clients=1000
```

在上面的例子中，我们为`web1.example.com`和`web2.example.com`定义了http_port和max_clients变量，并将其附加到主机名称后面，以便在使用Ansible执行命令时访问这些变量。

### 动态清单

动态清单使Ansible能够在运行时动态地生成主机清单，以便根据特定规则自动选择目标主机。因此，可以基于存储桶内容、云环境、CMDB 等动态产生主机清单。使用动态清单需要编写一个可调用脚本，它可以返回JSON格式的主机列表，Ansible可以使用该列表来执行任务。例如：

```yaml
---
plugin: aws_ec2
regions:
  - us-west-2
keyed_groups:
  - key: tags.Name
    separator: ''
```

在上面的示例中，我们使用aws_ec2插件实现动态主机清单的创建。该插件获取AWS账户下位于`us-west-2`区域的EC2实例列表，并将它们分为不同的命名群组，如以标签"Name"分组。

总之，正确管理主机清单对于Ansible运行非常重要，它是使Ansible可以管理目标主机、应用配置和执行操作的必要条件。没有正确的清单，Ansible将无法识别哪些主机处于哪些角色的状态，从而导致执行失败或更糟糕的情况。因此，任何使用Ansible的人都应该花费足够的时间来完善主机清单的细节。

## Roles使用

Ansible的Roles机制是一种将任务和配置文件组织在一起以便于管理和复用的方法。Roles是可以在不同项目中共享的，这使得在多个项目中使用相同的配置和任务变得更加容易和高效。

Roles通常包含以下目录和文件：

1. tasks：包含主要执行任务的YAML文件。

2. handlers：包含响应任务结果的处理器文件。

3. templates：包含需要渲染的Jinja2模板文件。

4. files：包含需要传输到远程主机上的文件。

5. vars：包含变量定义的YAML文件。

6. defaults：包含默认变量的YAML文件。

7. meta：包含描述角色的元数据文件。

为了使用Roles，需要将其放置在Ansible playbook所在的目录中。在playbook中，可以通过include_role或roles关键字来使用Role。include_role不需要指定角色的路径，而roles则需要指定角色的相对或绝对路径。

共享Roles的最简单方法是将它们放置在共享存储区域中，如Git仓库、Nexus等，并让不同的项目从该存储区域中引用Roles。这样，多个项目可以共享相同的Roles，并能够更方便地更新和维护。

总之，Ansible的Roles机制提供了一种有效的方式来组织和复用Ansible任务和配置文件。通过正确使用Roles，可以大大减少编写和维护Ansible playbook的工作量。

## Ansible Tower

Ansible Tower是一个基于web的平台，用于管理和执行Ansible任务和工作流程。它包括以下几个基本概念和功能：

1. 项目：Ansible playbook和其他文件的集合，可以通过版本控制系统进行托管。

2. 工作流程：由多个任务组成的自动化过程，可以在不同主机之间传递数据和结果。

3. 模板：用于执行单个任务或工作流程的配置文件。

4. 认证：指定用于连接远程主机的凭据。

5. 主机清单：指定要连接和管理的主机列表。

6. 角色：允许对不同的Ansible Tower用户分配不同的操作权限。

7. 日志和审核：记录执行任务的详细信息，并提供审计功能，以满足合规性要求。

8. API：允许使用编程方式与Ansible Tower交互。

使用Ansible Tower可以简化Ansible playbook的管理和执行。可以通过Tower的web界面或API来创建、编辑和运行任务和工作流程。此外，还可以设置工作流程中任务的依赖关系和参数，以及将多个任务分组为一个单独的模板。通过这些功能，可以使Ansible playbook的管理和执行更加简单、高效和可靠。

要配置Ansible Tower的API和插件，可以按照以下步骤操作：

1. 登录到Ansible Tower的web界面中。

2. 点击左上角的“设置”图标，然后选择“系统”。

3. 向下滚动页面，找到“API”和“扩展”两个部分。

4. 在“API”部分下，可以配置API密钥和其他安全设置。

5. 在“扩展”部分下，可以启用和配置各种Ansible Tower插件，包括消息通知、用户验证等。

总之，Ansible Tower提供了一种集中式的方式来管理和执行Ansible任务和工作流程，并提供了丰富的API和插件来扩展其功能。当正确使用时，Ansible Tower可以大大提高工作效率和可靠性。

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

