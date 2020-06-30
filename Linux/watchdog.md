# Watcchdog 笔记
----
 - **定义**
看门狗是一种监控系统的运行状况的手段，可以通过软件或者硬件的方式实现对系统运行状况的监控。   

 - **作用**
在程序或者服务异常崩溃挂掉的时候重新拉起服务，保证服务不中断   

 - **实现**
1. shell   
	- watchdog.sh : 
		1. monitor : 检查服务是否在服，如果服务退服，重新启动服务。
		2. start : 启动服务
		3. stop : 终止服务   
	- register.sh : 向crontab中写入 / 取消每分钟执行一次watchdog.sh   
2. Supervisor
	  - 强大的前台监控程序，能保证前台程序挂掉时自动重拉
	  - 安装
		  - pip install supervisor
	  - 配置
		  - echo_supervisord_conf > /etc/supervisord.conf
		  - vim /etc/supervisord.conf
		  - 将最后的内容修改为
			>  [include]    
			 				 files = /etc/supervisor/*.conf

		- 添加被监控程序
			- mkdir /etc/supervisor
			- vim /etc/supervisor/program.conf
			- 
			> [program:uwsgi]    
			> 					command=/usr/local/bin/uwsgi --ini /home/mysite_uwsgi/mysite.ini     
			> 					user=root     
			> 					autorestart=true    
			> 					autostart=true     
			> 					startretries=3     
			> 					redirect_stderr=true     
			> 					startsecs=5     
			> 					stdout_logfile=/var/log/django/supervisor.log     
			> 					stopasgroup=true     
			> 					killasgroup=true    
			> 					priority=999     

			- 说明
				- command：需要托管给supervisor执行的命令，这里是uwsgi的启动命令，这里需要按自己的情况更改
				- user：执行命令的用户，这里填root，你可以填其他的，只要有权限即可
				- autorestart：uwsgi关闭后是否自动重启
				- autostart：是否随supervisor启动而启动
				- startretries：启动失败自动重试次数，默认是 3
				- redirect_stderr：把 stderr 重定向到 stdout，默认 false
				- startsecs：启动 5 秒后没有异常退出，就当作已经正常启动了
				- stdout_logfile：日志文件存放目录
				- stopasgroup：是否干掉程序的所有进程(包括子进程)
				- killasgroup：作用同上
				- priority：程序启动优先级，若有多个进程管理时使用，默认-1
		- 启动
			- supervisord -c /etc/supervisord.conf
		- 管理
			- 查看进程状态
				- supervisorctl status  [进程名]
			- 启动进程
				- supervisorctl start (进程名 | all)
			- 停止进程
				- supervisorctl stop (进程名 | all)
			- 重启进程
				- supervisorctl restart (进程名 | all)
			- 添加新增进程
				- supervisorctl reload

