#### gzip

* 语法
```shell
location ^~ /static/ {
    gzip on;
    gzip_buffers 4 16k;  #缓冲(压缩在内存中缓冲几块? 每块多大?)
    gzip_http_version 1.1;
    gzip_disable "MSIE [1~6]\."; #正则匹配UA 什么样的Uri不进行gzip
    gzip_min_length 200;
    gzip_comp_level 6;压缩级别(级别越高,压的越小,越浪费CPU计算资源)
    gzip_types text/plain application/xml application/x-javascript text/css text/application image/jpeg image/gif image/png image/jpg;
    gzip_vary off;#对哪些类型的文件用压缩
    #rewrite ^\/static\/images\/(\d*)\.jpg /504.html last;
    #try_files $request_filename /404.html;
}

```

#### expires
* 语法
```shell
方法一
expires 10d;
结果

Cache-Control:max-age=864000
Connection:keep-alive
Content-Encoding:gzip
Content-Type:text/html
Date:Thu, 30 Mar 2017 06:52:54 GMT
ETag:W/"58db4a51-142"
Expires:Sun, 09 Apr 2017 06:52:54 GMT
Last-Modified:Wed, 29 Mar 2017 05:46:57 GMT
Server:nginx/1.9.5
Transfer-Encoding:chunked

适用于长时间缓存在用户的客户端中

方法二
expires 304;
结果

Cache-Control:max-age=304
Connection:keep-alive
Content-Encoding:gzip
Content-Type:text/html
Date:Thu, 30 Mar 2017 06:56:42 GMT
ETag:W/"58db4a51-142"
Expires:Thu, 30 Mar 2017 07:01:46 GMT
Last-Modified:Wed, 29 Mar 2017 05:46:57 GMT
Server:nginx/1.9.5
Transfer-Encoding:chunked

整个流程
计算etag和last_modified_since两个标签值返回给客户端，
客户端下次请求时带上这两个参数，
服务器拿着两个参数检测文件有没有发生变化，
如无,直接头信息返回 etag,last_modified_since。
浏览器知道内容无改变,于是直接调用本地缓存


适用于缓存周期较短的缓存

```