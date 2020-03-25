## 前言：HTTP请求

​	request - 包括请求行、请求头、请求数据

​	response - 包括状态行、消息报头、响应正文



​	http请求建立在一次TCP连接基础上

​	一次TCP请求至少产生一次HTTP请求

```
使用curl命令模拟http请求 
curl  -v http://www.baidu.com   #-v可以查看请求头和响应头
```

## 1 日志类型

### 	1.1 包括 error.log 、 access.log

### 	1.2 log_format 配置

```
语法：log_format name [escape=default|json]string...;
默认：log_format combined '...';
模块：http
```

access.log配置参数 [查看](https://nginx.org/en/docs/http/ngx_http_log_module.html#access_log)

## 2 编译参数讲解

编译参数详情，请查阅[nginx编译参数](study/nginx/nginx编译参数)

## 3 nginx请求限制

### 3.1 连接频率限制 - limit-conn-module

```
1，
语法：limit_conn_zone key zone=name:size
默认：-
模块：http

2，
语法：limit_conn zone number;
默认：-
模块：http，server，location
```

### 3.2 请求频率限制 - limit-req-module

```
1,
语法：limit_req_zone key zone=name:size rate:rate
默认：-
模块：http

2，
语法：limit_req zone=name [burst=number] [nodelay];
默认：-
模块：http，server，location
```

```
例子：
http {
	#对于同一个客户端进来的，每秒只能有一个请求连接
	limit_req_zone $binary_remote_attr zone=req_zone:1m rate=1r/s;
	#同一时刻只允许一个请求连接过来
	limit_conn_zone $binary_remote_attr zone=conn_zone:1m;
	server {
		......
		location / {
			root 
			#limit_req zone=req_zone;
			###留3个请求在下一秒执行，其他的马上执行
			#limit_req zone=req_zone burst=3 nodelay;
			#limit_conn zone=conn_zone 1; 
			index index.html
		}
	}
}

可使用[ab]工具测试
```

> [ab【ApacheBench】下载地址](https://www.apachelounge.com/download/)
>
> 无需安装，直接使用
>
> ab -n xxx -c xxx xxx
>
> -n后面一个参数是要发送的总的请求数，-c后面第一个参数是并发数，即每次要并发发送多少个请求，最后一个参数是你要请求的接口地址，就是url

## 4 nginx访问控制

### 4.1基于IP的访问控制

#### 4.1.1 http-access-module

```
1,
语法：allow address | CIDR(网段) | unix:(socket访问) | all
默认：-
模块：http, server, location, limit_except 

2,
语法：deny address | CIDR(网段) | unix:(socket访问) | all
默认：-
模块：http, server, location, limit_except

例子： 
	  location ~ ^/2.html {
          root   html;
          deny 192.168.117.58;  #这个ip不能访问2.html
          allow all;            #其他的可以访问这个页面2.html
          index  index.html index.htm;
	  }
	  
	  location ~ ^/2.html {
		root html;
		index index.html;
		# 允许指定ip段允许访问，其他禁止访问
		allow 192.168.116.0/25;
		deny all;
	  }
```

#### 4.1.2 http-access-module的局限性

若客户端通过一层代理来访问服务端时候，http-access-module无法限制真正的客户端（用$remote_addr携带最近的那一级ip）

```
处理方法1： 采用别的http头信息控制访问，如http_x_forwarded_for
处理方法2：结合geo模块
处理方法3：通过http自定义变量传递

处理方法1详解：http_x_forwarded_for
	因为remote_addr只会携带一级的ip
	http_x_forwarded_for会携带所有的过程涉及的ip
	此时：http_x_forwarded_for = client ip, Proxy(1)IP, Proxy(2)IP,...
```

### 4.2 基于用户的信任登录 - http-auth-basic-module

#### 4.2.1http-auth-basic-module

```
1,
语法：auth_basic string| off
默认：auth_basic off;
模块：http, server, location, limit_except

2,配置存放密码文件，加密方式参考官网，例如htpasswd【可自行安装】
语法：auth_basic_user_file file
默认：-
模块：http, server, location, limit_except

例子：1，首先添加密码文件
	 2，location /limitpage {
         root html;
         index index.html;
         auth_basic "test auth acess";
         auth_basic_user_file 加密文件路径
       }
```

#### http-auth-basic-module局限性

```
1,用户信息依赖文件方式
2，操作管理机械，效率低下

解决方案：
1,nginx结合LUA实现高效验证
2,nginx和LDAP打通，利用nginx-auth-ldap模块
```

