## nginx编译参数

```
D:\InstalledSoft\nginx-1.17.3>nginx -V
nginx version: nginx/1.17.3
built by cl 16.00.40219.01 for 80x86
built with OpenSSL 1.1.1c  28 May 2019
TLS SNI support enabled
configure arguments: --with-cc=cl --builddir=objs.msvc8 --with-debug --prefix= --conf-path=conf/nginx.conf --pid-path=logs/nginx.pid --http-log-path=logs/access.log --error-log-path=logs/error.log --sbin-path=nginx.exe --http-client-body-temp-path=temp/client_body_temp --http-proxy-temp-path=temp/proxy_temp --http-fastcgi-temp-path=temp/fastcgi_temp --http-scgi-temp-path=temp/scgi_temp --http-uwsgi-temp-path=temp/uwsgi_temp --with-cc-opt=-DFD_SETSIZE=1024 --with-pcre=objs.msvc8/lib/pcre-8.43 --with-zlib=objs.msvc8/lib/zlib-1.2.11 --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_stub_status_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module --with-http_slice_module --with-mail --with-stream --with-openssl=objs.msvc8/lib/openssl-1.1.1c --with-openssl-opt='no-asm no-tests -D_WIN32_WINNT=0x0501' --with-http_ssl_module --with-mail_ssl_module --with-stream_ssl_module
```

## 路径和目录编译选项

|               编译选项                |         作用         |
| :-----------------------------------: | :------------------: |
| --prefix= --conf-path=conf/nginx.conf | 安装的基础目录和路径 |
|         --sbin-path=nginx.exe         |                      |
|      --conf-path=conf/nginx.conf      |                      |
|    --error-log-path=logs/error.log    |                      |
|       --pid-path=logs/nginx.pid       |                      |

## 临时文件编译选项

|                      编译选项                      |                  作用                   |
| :------------------------------------------------: | :-------------------------------------: |
| --http-client-body-temp-path=temp/client_body_temp | 执行对应模块时，nginx所保留的临时性文件 |
|       --http-proxy-temp-path=temp/proxy_temp       |                                         |
|     --http-fastcgi-temp-path=temp/fastcgi_temp     |                                         |
|        --http-scgi-temp-path=temp/scgi_temp        |                                         |
|       --http-uwsgi-temp-path=temp/uwsgi_temp       |                                         |

## --with-http-stub-status-module

|            编译选项            |       作用        |
| :----------------------------: | :---------------: |
| --with-http-stub-status-module | nginx的客户端状态 |

```
语法：stub_status
默认：-
模块：server， location
例子：location /mystatus {
	 	stub_status;
	 }
效果：访问http://localhost:xxx/mystatus，页面会展示客户端状态
```

## --with-http_random_index_module

|            编译选项             |                   作用                   |
| :-----------------------------: | :--------------------------------------: |
| --with-http_random_index_module | 目录中选择一个随机主页(且不显示隐藏文件) |

```
语法：random_index on|off 
默认：random_index off; 
模块：location
例子：location / {
        root html;
        random_index on;
        #index index.html;
	 }
效果：访问http://localhost:xxx，页面会随机展示主页，并且不展示以.开头的页面(.3.html),这是隐藏文件
```

## --with-http_sub_module

|        编译选项        |     作用     |
| :--------------------: | :----------: |
| --with-http_sub_module | http内容替换 |

```
1,sub_filter
    语法：sub_filter string[要替换的内容] replacement[要替换后的内容]
    默认：-
    模块：http, server, location

2,sub_filter_last_modified用于缓存
    语法：sub_filter_last_modified on| off
    默认：sub_filter_last_modified off
    模块：http, server, location

3,sub_filter_once 只替换一次
    语法：sub_filter_once on| off
    默认：sub_filter_once on
    模块：http, server, location

例子：location / {
        root html;
        sub_filter '删除前的内容' '修改后的内容';
        sub_filter_once off;  #全局替换
        index index.html;
	 }
效果：访问http://localhost:xxx，页面会把主页中包含‘删除前的内容’的文本内容替换成‘修改后的内容’，并且sub_filter_once设置为关闭，表示会把页面中所有的‘删除前的内容’文本全部替换掉
```

