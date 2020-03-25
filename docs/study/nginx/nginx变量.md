# nginx变量

## 1. http请求变量

 1. arg_PARAMETER

 2. http_HEADER

 3. send_http_HEADER

```
案例：在日志中请求User_Agent信息
log_format 'mainlog' '$http_user_agent'   ###'$' + 'http_' + [HEADER](请求头信息)
```

​    



## 2. 内置变量

access_log 可使用的内置变量 [查看](https://nginx.org/en/docs/http/ngx_http_log_module.html#access_log)

## 3. 自定义变量