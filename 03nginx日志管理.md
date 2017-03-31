#### nginx日志管理

- 日志在nginx配置文件中的位置
```
#访问日志   存放位置         日志格式
access_log  logs/access.log  main

```
- main格式日志
```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"'; 
远程IP - 远程用户 - 用户时间 请求行[请求方法 路径 协议] 响应状态码 请求body体长度  referer来源信息 用户代理/蜘蛛 被转发的请求的原始来源IP
```
- 设置自己的日志格式
```
log_format  mylog  '$remote_addr -  [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';

#使用自己设置的日志格式
server {
    access_log  logs/access.log  mylog
}

```