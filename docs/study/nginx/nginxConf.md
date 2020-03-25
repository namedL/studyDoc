# 配置文件解释

```
###1,
###全局块： 配置影响nginx全局的指令。
###一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，
###允许生成worker process数等。

#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#user  nobody;  
#==允许生成的工作进程数，一般设置为cpu核心数，默认为1
worker_processes  1; 
 
###制定日志路径，级别。
###这个设置可以放入全局块，http块，server块，
###级别以此为：debug|info|notice|warn|error|crit|alert|emerg
#error_log  logs/error.log; 
#error_log  logs/error.log  notice; 
#error_log  logs/error.log  info; 
 
###指定nginx进程运行文件存放地址
#pid        logs/nginx.pid; 
 
###2,
###events块：配置影响nginx服务器或与用户的网络连接。
###有每个进程的最大连接数，
###选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等
events { 
    #accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on

    #multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off

    #use epoll;      #事件驱动模型（内核模型 ），select|poll|kqueue|epoll|resig|/dev/poll|eventport

#==每个进程允许的最大连接数，一般设置为cpu*2048， 默认为512
    worker_connections  1024; 
} 
 
###3,
###http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。
###如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，
###连接超时时间，单连接请求数等。 
http { 
    include       mime.types;     #文件扩展名与文件类型映射表
    default_type  application/octet-stream;   #默认文件类型，默认为text/plain
 
###自定义日志格式，log_format的定义位置，在http模块下 
#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" ' 
#                  '$status $body_bytes_sent "$http_referer" ' 
#                  '"$http_user_agent" "$http_x_forwarded_for"'; 
 
#access_log  logs/access.log  main;  #服务日志，并使用自定义日志格式
 
    sendfile        on;    #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
#tcp_nopush     on; 
 
#keepalive_timeout  0;  
     
#==客户端连接超时时间 ，默认为75s
    keepalive_timeout  65;  
 
#gzip  on; 
 
#当配置多个server节点时，默认server names的缓存区大小就不够了，需要手动设置大一点 
    server_names_hash_bucket_size 512; 

#如果被代理服务器返回的状态码为400或者大于400，设置的error_page配置起作用。默认为off。
    #proxy_intercept_errors on;    
###4,
###server表示虚拟主机可以理解为一个站点，可以配置多个server节点搭建多个站点 
###每一个请求进来确定使用哪个server由server_name确定 
    server { 
#站点监听端口 
        listen       8800; 
#站点访问域名  
        server_name  localhost; 
         
#编码格式，避免url参数乱码 
        charset utf-8; 
 
#access_log  logs/host.access.log  main; 

###5, 
###location用来匹配同一域名下多个URI的访问规则 
###比如动态资源如何跳转，静态资源如何跳转等 
###location后面跟着的/代表匹配规则 
        location / { 
#站点根目录，可以是相对路径，也可以使绝对路径 
            root   html; 
#默认主页 
            index  index.html index.htm; 
             
#转发后端站点地址，一般用于做软负载，轮询后端服务器 
#proxy_pass http://10.11.12.237:8080; 
 
#拒绝请求，返回403，一般用于某些目录禁止访问 
#deny all; 
             
#允许请求 
#allow all; 
             
            add_header 'Access-Control-Allow-Origin' '*'; 
            add_header 'Access-Control-Allow-Credentials' 'true'; 
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS'; 
            add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type'; 
#重新定义或者添加发往后端服务器的请求头 
#给请求头中添加客户请求主机名 
            proxy_set_header Host $host; 
#给请求头中添加客户端IP 
            proxy_set_header X-Real-IP $remote_addr; 
#将$remote_addr变量值添加在客户端“X-Forwarded-For”请求头的后面，并以逗号分隔。 如果客户端请求未携带“X-Forwarded-For”请求头，$proxy_add_x_forwarded_for变量值将与$remote_addr变量相同   
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
#给请求头中添加客户端的Cookie 
            proxy_set_header Cookie $http_cookie; 
#将使用代理服务器的主域名和端口号来替换。如果端口是80，可以不加。 
            proxy_redirect off; 
             
#浏览器对 Cookie 有很多限制，如果 Cookie 的 Domain 部分与当前页面的 Domain 不匹配就无法写入。 
#所以如果请求 A 域名，服务器 proxy_pass 到 B 域名，然后 B 服务器输出 Domian=B 的 Cookie， 
#前端的页面依然停留在 A 域名上，于是浏览器就无法将 Cookie 写入。 
             
　　         #不仅是域名，浏览器对 Path 也有限制。我们经常会 proxy_pass 到目标服务器的某个 Path 下， 
#不把这个 Path 暴露给浏览器。这时候如果目标服务器的 Cookie 写死了 Path 也会出现 Cookie 无法写入的问题。 
             
#设置“Set-Cookie”响应头中的domain属性的替换文本，其值可以为一个字符串、正则表达式的模式或一个引用的变量 
#转发后端服务器如果需要Cookie则需要将cookie domain也进行转换，否则前端域名与后端域名不一致cookie就会无法存取 
　　　　　　  #配置规则：proxy_cookie_domain serverDomain(后端服务器域) nginxDomain(nginx服务器域) 
            proxy_cookie_domain localhost .testcaigou800.com; 
             
#取消当前配置级别的所有proxy_cookie_domain指令 
#proxy_cookie_domain off; 
#与后端服务器建立连接的超时时间。一般不可能大于75秒； 
            proxy_connect_timeout 30; 
###如果你的nginx服务器给2台web服务器做代理，负载均衡算法采用轮询，
###那么当你的一台机器web程序iis关闭，也就是说web不能访问，
###那么nginx服务器分发请求还是会给这台不能访问的web服务器，
###如果这里的响应连接时间过长，就会导致客户端的页面一直在等待响应，
###对用户来说体验就打打折扣，这里我们怎么避免这样的情况发生呢
            #proxy_connect_timeout 1;   #nginx服务器与被代理的服务器建立连接的超时时间，默认60秒
            #proxy_read_timeout 1; #nginx服务器想被代理服务器组发出read请求后，等待响应的超时间，默认为60秒。
            #proxy_send_timeout 1; #nginx服务器想被代理服务器组发出write请求后，等待响应的超时间，默认为60秒。
            #proxy_ignore_client_abort on;  #客户端断网时，nginx服务器是否终端对被代理服务器的请求。默认为off。
        } 
 
#error_page  404              /404.html; 
 
# redirect server error pages to the static page /50x.html 
# error_page 中指定的 /50x.html 会指向下一个location定义的位置
        error_page   500 502 503 504  /50x.html; 
        location = /50x.html { 
            root   html; 
        } 
 
    } 
     
　　#当需要对同一端口监听多个域名时，使用如下配置，端口相同域名不同，server_name也可以使用正则进行配置 
　　#但要注意server过多需要手动扩大server_names_hash_bucket_size缓存区大小 
　　server { 
　　　　listen 80; 
　　　　server_name www.abc.com; 
　　　　charset utf-8; 
　　　　location / { 
　　　　　　proxy_pass http://localhost:10001; 
　　　　} 
　　} 
　　server { 
　　　　listen 80; 
　　　　server_name aaa.abc.com; 
　　　　charset utf-8; 
　　　　location / { 
　　　　　　proxy_pass http://localhost:20002; 
　　　　} 

        location /imgabc/ {
          proxy_pass https://img.abc.com/; 
          #代理时请求头没有携带，加上此句
            proxy_set_header Host $proxy_host;
        }
　　} 
}
```

