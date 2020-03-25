# 高级模块

## 1 secure-link-module

```
安全连接模块 - secure-link-module
作用：1，制定并允许检查请求的链接的真实性，以及保护资源免遭未经授权的访问
	 2，限制链接生效周期
```

配置语法：

```
1,
语法：secure_link expression
默认：-
模块：http, server, location

2,
语法：secure_link_md5 expression
默认：-
模块：http, server, location
```

