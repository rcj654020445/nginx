#### proxy
```shell

location ^~ /static/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #proxy_pass http://www.test.com/
    #proxy_pass http://www.test.com
}

例如访问
http://192.168.1.222:8080/static/html/index.html

带/的proxy访问的
http://www.test.com/html/index.html


不带/的proxy的访问
http://www.test.com/static/html/index.html



详情配置
http://nginx.org/en/docs/http/ngx_http_proxy_module.html
```

#### upstream
```shell
Syntax:	upstream name { ... }
Default:	—
Context:	http




upstream backend {

  #ip_hash;
  server 192.168.1.251;
  server 192.168.1.252;
  server 192.168.1.247;

}

详情配置
http://nginx.org/en/docs/http/ngx_http_upstream_module.html
```