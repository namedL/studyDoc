##   环境部署（Window）

   1，下载包：http://nginx.org/en/download.html

   2，解压到指定目录【注意此时不要直接点击ng.exe,否则后序的部分命令会失效】

   3, 按顺序执行如下命令：

​      \> start nginx    #启动，doc窗口一闪而过

​      \> tasklist /fi "imagename eq nginx.exe"

​        --若报错【信息: 没有运行的任务匹配指定标准。】

​        --因为nginx启动的端口被占用了，关闭端口

​          法1：cmd-->netstat -aon|findstr "80"   # 查看80端口指向的进程

​                  --C:\Windows\System32\ntoskrnl.exe #我指向的是这个system的进程（pid=4）

​              停止HTTP服务，c\system32\cmd.exe 【管理员身份】

​              cmd-->net stop http--> y  



​          法2：修改nginx.conf 

​          --listen 所监听的端口号



   4，检测是否安装成功：

​    \> nginx -v



   5，关闭nginx服务可以使用以下命令，同样也是一闪而过是正常的，看一下是否进程已消失即可

​    \> nginx -s stop  【快速停止】

​    \> nginx -s quit  【完整有序的关闭】



   6，重新加载nginx：

​    \> nginx -s reload  【修改配置后重新加载生效】

​    \> nginx -s reopen  【重新打开日志文件】

​    \> nginx -t -c /path/to/nginx.conf 【测试nginx配置文件是否正确】

​    \> nginx -t ###检测配置文件是否正常





## Linux基本环境配置

   确保基本库和基本工具的存在

​		yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake

​    	yum -y install wget httpd-tools vim

  初始化目录：

​			mkdir opt  

​			cd opt

​             mkdir app download logs work backup

  测试是否能连通公网： ping www.baidu.com

  测试yum源是否可用： yum list|grep gcc  【若有返回gcc列表，yum源可用 】

  查看iptables防火墙规则：iptables -L

​                         					  iptables -t nat -L

  关闭iptables规则： iptables -F

​                  				  iptables -t nat -F

  查看selinux状态：getenforce

  关闭selinux状态：setenforce 0



使用yum安装nginx：

​	1,在/etc/yum.repos.d下添加nginx.repo

​	2，拷贝nginx官网的[yum源配置](https://nginx.org/en/linux_packages.html#RHEL-CentOS)

​	3,  > yum install nginx

​	4，>nginx -v  #查看版本

​		  >nginx -V  #查看编译的参数

​    5，> rpm -ql nginx   #linux安装了的目录列表(yum 安装方式可用)

​			/etc/logrotate.d/nginx		配置文件	                 nginx日志轮转，用于logrotate服务的日志切割

​			/etc/nginx							 目录、配置文件		  nginx主配置文件



