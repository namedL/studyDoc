## 静态资源web服务

### 1，什么是静态资源web服务

nginx接收客户端的jepg、html、flv等静态资源请求

### 2，静态资源类型

(非服务器动态运行生成的文件)

|     类型     |      种类      |
| :----------: | :------------: |
| 浏览器端渲染 | html、css、js  |
|     图片     | jepg、gif、png |
|     视频     |   flv、mpge    |
|     文件     |      txt       |

### 3，静态资源服务场景-CDN(内容分发网络)

​	为了传输延时最小化

### 4，配置语法

#### 4.1 文件读取(sendfile)

```
语法：sendfile on| off
默认：sendfile off;
模块：server, location, if in location
```

#### 4.2 tcp_nopush

​	在sendfile开启的情况下，提高网络包的传输效率（整合网络包，然后一次性发送）

```
语法：tcp_nopush on| off
默认：sendfile off;
模块：http, server, location
```

#### 4.3 tcp_nodelay

实时性发送，尽量不等待（用在实时性比较高的场景）

在keepalive连接下，提高网络包的传输实时性

```
语法：tcp_nodelay on| off
默认：tcp_nodelay off;
模块：http, server, location
```

#### 4.4 压缩(gzip)

```
1,
语法：gzip on| off
默认：gzip off;
模块：http, server, location, if in location

2,压缩比
语法：gzip_comp_level level
默认：gzip_comp_level 1;
模块：http, server, location

3,控制http协议版本
语法：gzip_http_version 1.0| 1.1
默认：gzip_http_version 1.1;
模块：http, server, location
```

#### 4.5 扩展nginx压缩模块

```
http_gzip_static_module - 预读gzip功能：会先去磁盘找*.gz文件，减少CUP压缩时间和请求压缩的损耗
http_gunzip_module - 应用支持gunzip的压缩
```

### 5，浏览器缓存

http协议定义的缓存机制（如Expires、Cache-control等）

本地缓存校验过期机制

| 说明                    | 头信息                          |
| ----------------------- | ------------------------------- |
| 校验是否过期            | Expires、Cache-control(max-age) |
| 协议中Etag头信息校验    | Etag                            |
| Last-Modified头信息校验 | Last-Modified                   |

#### 5.1 expires配置

添加Expires、Cache-control头

```
语法：expires [modified] time;
     expires epoch | max | off;
默认：expires off;
模块：http, server, location, if in location
例子：location / {
	root html;
	expires 24h; #客户端缓存24h
}
```

### 6, nginx 跨站访问

浏览器禁止跨站访问：不安全，容易出现CSRF攻击

处理Access-Control-Allow-Origin

```
语法：add_header name value [always];
默认：-
模块：http, server, location, if in location
例子：location / {
		add_header Access-Control-Allow-Origin http://cnblogs.com;
		add_header Access-Control-Allow-Methods GET,POST,PUT,DELETEOPTIONS
	}
```

### 7, 防盗链

防止资源被盗用

需要区别哪些请求是非正常的用户请求

基于http_refer防盗链配置模块

```
语法：valid_referers none| blocked| server_names| string...;
默认：-
模块：server, location
例子： location / {
		root html;
		#none:允许没有过代理的信息过来
		#blocked:非http://
		# 允许指定ip访问
		valid_referers none blocked 192.168.116.197;
		if ($invalid_referer) {
			return 403;
		}
	  }
```

### 8, 代理服务

 正向代理：代理的对象是客户端

反向代理：代理的对象是服务端

#### 8.1 配置语法(反向代理)

```
语法：proxy_pass URL
默认：-
模块：location, if in location, limit_except
例子：server {
	  listen 80;
	  server_name localhost;
	  location / {
	  	root html;
	  	index index.html index.htm;
	  }
	  location ~ /text_proxy.html$ {
	  	proxy_pass http:127.0.0.1:8080;
	  }
	}
	server {
	  listen 8080;
	  server_name localhost;
	  location / {
	  	root html;
	  	index index.html index.htm;
	  }
	}
	
可以访问：http://localhost/text_proxy.html -> 会去代理8080端口下的文件
```

#### 8.2 缓冲区 - proxy_buffering 

```
语法：proxy_buffering on| off
默认：proxy_buffering on
模块：http, server, location
```

#### 8.3 跳转重定向 - proxy_redirect default

```
语法：proxy_redirect default
	 proxy_redirect off; proxy_redirect redirect replacement
默认：proxy_redirect default
模块：http, server, location
```

#### 8.4 头信息 - proxy_set_header

```
语法：proxy_set_header field value
默认：proxy_set_header Host $proxy_host
	 proxy_set_header Connection close;
模块：http, server, location	 
```

#### 8.5 连接超时 - proxy_connect_timeout time

```
语法：proxy_connect_timeout time
默认：proxy_connect_timeout 60  #默认60秒
模块：http, server, location	
```

### 9 负载均衡

四层负载均衡和七层负载均衡（nginx就是一个典型的七层负载均衡的SLB【ServerLoadBalancer】）

#### 9.1 语法配置 - upstream

默认轮询

```
语法：upstream name {...}
默认：-
模块：http
例子1：http {
	   upstream custname {
	   	 server 192.168.116.197:8001;
	   	 server 192.168.116.197:8002;
	   	 server 192.168.116.197:8003;
	   }
	   server {
	   	listen 80;
	   	server_name localhost;
	   	location / {
	   		proxy_pass http://custname;
	   	}
	   }
	 }
	 
例子2： http {
	   upstream backend {
	   	 server 192.168.116.197:8001 weight=5; #配置权重
	   	 server 192.168.116.197:8002 down; #不参与轮询
	   	 server 192.168.116.197:8003 max_fails=10 fail_timeout=10s;
	   	 server 192.168.116.197:8002 backup; #备份
	   }
	   server {
	   	listen 80;
	   	server_name localhost;
	   	location / {
	   		proxy_pass http://custname;
	   	}
	   }
	 }
```

#### 9.2 后端服务器在负载均衡调度中的状态

|              |                                                |
| :----------: | :--------------------------------------------: |
|     down     |         当前的server暂时不参与负载均衡         |
|    backup    |                预留的备份服务器                |
|  max_fails   |               允许请求失败的次数               |
| fail_timeout | 经过max_fails失败后，服务暂停的时间（默认10s） |
|  max_conns   |             限制最大的接收的连接数             |

#### 9.3 调度算法

|              |                                                              |
| ------------ | ------------------------------------------------------------ |
| 轮询         | 按时间顺序逐一分配到不同的后端服务器                         |
| 加权轮询     | weight值越大，分配到的访问几率越高                           |
| ip_hash      | 每个请求按访问ip的hash结果分配，这样来自同一个ip的固定访问一个后端服务器 |
| url_hash     | 按照访问的url的hash结果来分配请求，是每个url定向到同一个后端服务器 |
| least_conn   | 最少链接数，哪个机器链接少就分发哪个                         |
| hash关键数值 | hash自定义的key                                              |

#### 9.4 url_hash 配置语法	

```
语法： hash key [consistnet]
默认：-
模块：upstream
例子：http {
	   upstream custname {
	   	 hash $request_uri;  #请求同一个url时，就会定位到同一台服务器上
	   	 server 192.168.116.197:8001;
	   	 server 192.168.116.197:8002;
	   	 server 192.168.116.197:8003;
	   }
	 }
```



```
例子：http {
	   upstream custname {
	   	 ip_hash;  #同一个ip会请求同一个服务器
	   	 server 192.168.116.197:8001;
	   	 server 192.168.116.197:8002;
	   	 server 192.168.116.197:8003;
	   }
	   server {
	   	listen 80;
	   	server_name localhost;
	   	location / {
	   		proxy_pass http://custname;
	   	}
	   }
	 }
```

### 10 缓存服务

#### 10.1 代理缓存 - proxy_cache

（在nginx上做缓存）

```
1,proxy_cache使用之前的配置,定义好空间大小及名字来缓存文件存放的路径
语法：proxy_cache_path path [levels=levels][use_temp_path=on|off]keys_zone=name:size
	 [inactive=time][max_size=size][manager_files=number][manager_sleep=time]			      [manager_threshoud=time][loader_files=time][loader_sleep=time]    
	 [loader_threshoud=time][purger=on|off][purger_files=number][purger_sleep=time]
	 [purger_threshoud=time];	 
默认：-
模块：http

2,
语法：proxy_cache zone | off;
默认：proxy_cache off;
模块：http, server, location
```

#### 10.2 缓存过期周期 - proxy_cache_valid

```
语法：proxy_cache_valid[code] time; #code为返回状态码
默认：-
模块：http, server, location
```

#### 10.3 缓存的维度 - proxy_cache_key

```
语法：proxy_cache_key string
默认：proxy_cache_key $scheme$proxy_host$request_uri #协议+主机+url
模块：http, server, location
```

#### 10.4 例子

```
http {
	upstream custname {
        server 192.168.116.197:8001;
        server 192.168.116.197:8002;
        server 192.168.116.197:8003;
	}
	# /cache 存放的缓存文件的路径
	# levels=1:2 配置文件夹下用两个目录的方式来进行分级
	# keys_zone 开辟空间名称及大小（1兆约存放8000个key，这里开辟10兆空间）
	# max_size 磁盘开辟10G空间
	# inactive 指定一定时间（60分钟）不活跃或者未被请求的文件会被清理掉
	# use_temp_file 临时文件存放目录，不建议开启，消耗性能
	proxY_chace_path /cache levels=1:2 keys_zone=imooc_cache:10m max_size=10g inactive=60m	use_temp_file=off;
	server {
    	listen 80;
   	  	server_name localhost;
    	location / {
    		proxy_pass http://custname;
    		proxy_cache imooc_cache;
    		proxy_cache_valid 200 304 12h; #针对200和304的状态码缓存12小时
    		peoxy_cache_valid any  10m #其他的状态码缓存10分钟
    		proxy_cache_key $host$uri$is_args$args;
    		add_header Nginx-Cache "$upstream_cache_status";
    		#后端返回500 502 503 504，错误及超时等信息时，让他跳过这一台，去访问下一台服务器
    		proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
    	}
	}
}
```

#### 10.5让部分页面不缓存 - proxy_no_cache 

```
语法：proxy_no_cache  string ... #string为缓存的地址
默认：-
模块：http, server, location
例子： 10.4的例子的省略版
	http {
		upstream xxx {
			...
		}
		server {
			...
			#匹配路径
			if ($request_uri ~ ^/(url3|login|register|passwword\/reset)) {
				set $cookie_nocache 1;
			}
			location / {
				...
				proxy_no_cache $cookie_nocache $arg_nocache $arg_comment;
				proxy_no_cache $http_pragma $http_authorization;
			}
		}
	}
```

#### 10.6 大文件分片请求 - slice

```
语法：slice zise
默认：slice 0
模块：http, server, location
```

### 11 动静分离

加载动态地址

```
http {
	upstream java_api {
		server 127.0.0.1:8080;
	}
	root /html;
	server {
		...
		location / {
			index index.html;
		}
		location ~ \.jsp$ {
			proxy_pass http://java_api;
			index index.html;
		}
	}
}
```

### 12 rewrite规则

实现url 重写和重定向

#### 12.1 使用场景

1. URL访问跳转、兼容性支持、路由展示效果
2. SEO优化
3. 维护（后台维护，流量转发）
4. 安全 - （实现伪静态）

#### 12.2 rewrite - 配置语法

```
语法：rewrite regex replacement[flag]
默认：-
模块：server, location, if条件
##表示所有的访问地址都会重定向到/page/maintain.html页面上
例子： ^(.*)$ /page/maintain.html break;
```

#### 12.3 正则表达式-括号

```
( ) ：  用于匹配括号之间的内容， 通过$1, $2调用
例子： if ($http_user_agent ~ MSIE) {  #判断客户端的头信息user-agent中是否有MSIE
		rewrite ^(.*)$ /msie/$1 break;
	  }
```

#### 12.4 flag说明

| flag选项  | 说明                                        |
| :-------: | ------------------------------------------- |
|   last    | 停止rewrite检测                             |
|   break   | 停止rewrite检测                             |
| redirect  | 返回302临时重定向，地址栏会显示跳转后的地址 |
| permanent | 返回301永久重定向，地址栏会显示跳转后的地址 |

```
例子1：last与break的区别
server {
	...
	root /html;
	location ~ ^/break {
		rewrite ^/break /test/ break; #停止后面的路径匹配，直接去root找对应的文件夹从而返回404
	}
	location ~ ^/last { 
		rewrite ^/last /test/ last;# 重新发起一个请求，用/test/去匹配路由
	}
	location /test/ {
		default_type application/json;
		return 200 '{"status":"success"}';
	}
}

例子2：redirect与permanent的区别
redirect相比于last，会发起两次请求
server {
	...
	location ~ ^/last { 
		#rewrite ^/last /test/ last;# 重新发起一个请求，用/test/去匹配路由
		rewrite ^/last /test/ redirect;  #会发起两次请求
	}
	location /test/ {
		default_type application/json;
		return 200 '{"status":"success"}';
	}
	location ^/bd {
		#rewrite ^/bd http://baidu.com permanent #服务关了后，同一个地址也会重新请求，返回404
		#rewrite ^/bd http://baidu.com redirect #服务关了后，同一个地址同样起效果
	}
}
```

12.5 实例

```
server {
	...
	location / {
		rewrite ^/course-(\d+)-(\d+)-(\d+)\.html$ /course/$1/$2/course_$3.html break;
	}
	#判断是否是谷歌浏览器打开的
	if ($http_user-agent ~* Chrome) {
		#匹配以nginx开头的地址，做跳转
		rewrite ^/nginx http://www.baidu.com redirect;
	}
	
	#请求的文件名是否存在(!-f  - 不存在)
	if (!-f $request_filename) {
		rewrite ^/(.*)$ http://www.baidu.com/$1 redirect;
	}
	index index.html;
}
此时，请求http://xxx/course-11-22-33.html 
      -->会去访问/course/11/22/_course_33.html这个真是页面
```



















