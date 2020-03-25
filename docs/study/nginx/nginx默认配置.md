## nginx进程和全局性配置

| 命令             | 说明                        |
| ---------------- | --------------------------- |
| user             | 设置nginx服务的系统使用用户 |
| worker_processes | 工作进程数(设置为cpu核心数) |
| error_log        | 错误日志                    |
| pid              | 服务启动时候pid             |

```
events {
	worker_connections 1024  #每个进程允许最大连接数
	use epoll #事件驱动模型
}
```

```
http {
	......
	server {
		listen 80
		server_name localhost 
		location / {
			root nginx/html;
			index index.html index.htm
		}
		error_page 500 502 503 504 /50x.html
		location = /50x.html {
			root nginx/html;
		}
	}
	server {
		......
	}
}
```

