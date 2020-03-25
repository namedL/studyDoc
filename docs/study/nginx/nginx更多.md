##  日志切割

​    1，使用默认日志配置，经过一段时间运行后，access.log和error.log文件会变得非常大，使维护和排查问题变得不便，所以非常有必要做日志切割



​    2，实现思路：

​       (1),使用nginx的-s reopen命令，结合linux系统的crontab定时任务命令，弄一个定时任务按时切割日志文件。【crontab：相当于Windows下的任务计划】

​       (2),每天定时执行脚本切割日志文件



##   请求处理流程

​    1，nginx可以处理来自web（http），Email，TCP/UDP的三类请求。

​    2，nginx底层使用非阻塞的事件驱动引擎，结合状态机来完成异步通知，其中处理Http请求的是HTTP状态机。



##   进程结构

​    master进程

​    worker进程

​    cache manager

​    cache loader



​    nginx是多进程结构，多进程结构设计是为了保证nginx的高可用高可靠，包含：

​      master进程：也是父进程，负责worker进程的管理。

​      worker进程：也是子进程，worker进程一般配置成与服务器的CPU核数相同，worker进程用来处理具体的请求的。

​      cache进程：也是子进程，包括cache manager和cache loader进程，主要是反向代理时做缓存使用。



  

##   处理HTTP请求的11个阶段

| 序号 |      阶段      |               指令               |           备注           |
| :--: | :------------: | :------------------------------: | :----------------------: |
|  1   |   POST_READ    |              realip              |     获取客户端真实IP     |
|  2   | SERVER_REWRITE |             rewrite              |                          |
|  3   |  FIND_CONFIG   |                                  |                          |
|  4   |    REWRITE     |                                  |                          |
|  5   |  POST_REWRITE  |                                  |                          |
|  6   |   PRE_ACCESS   |      limit_conn, limit_req       |                          |
|  7   |     ACCESS     | auth_basic, access, auth_request | auth_basic可以做访问限制 |
|  8   |  POST_ACCESS   |                                  |                          |
|  9   |  PRE_CONTENT   |                                  |                          |
|  10  |    CONTENT     |     index, autoindex, concat     |                          |
|  11  |      LOG       |            access_log            |  access_log记录请求日志  |

##   反向代理和负载均衡

​    nginx支持四层反向代理和七层反向代理

​    负载均衡是实现服务高性能和高可用的重要手段，而nginx是实现负载均衡的重要工具

​    负载均衡策略round-robin

​    对HTTP协议的反向代理proxy模块

​    proxy_pass指令