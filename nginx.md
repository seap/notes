# 开启zgip

```
gzip on;
gzip_min_length 1k;
gzip_buffers 16 64k;
gzip_http_version 1.1;
gzip_comp_level 6;
gzip_types text/plain application/x-javascript text/css application/xml;
gzip_vary on;
```

#net::ERR_CONTENT_LENGTH_MISMATCH

系统：CentOS，Nginx，proxy到后端的node express
nginx会缓存大文件到proxy_temp目录中，然而对这个目录没有读写权限

解决方法：
1. 用root身份运行nginx
2. 将proxy_temp目录的owner设置为nginx
3. 将proxy_temp目录的group设置为nginx，并赋予rwx权限
4. 禁用cache

```
#nginx.conf
user root;
```
