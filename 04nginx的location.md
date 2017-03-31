## location

* 语法

```shell
syntax: location [=|~|~*|^~|@] /uri/ { … }
default: no
context: server
```
* 优先级

精准匹配 > 字符串匹配最大长度(^~如果完全匹配，停止后续正则匹配，提升最大长度优先级) > 正则匹配(分先后顺序) > /

#### ==notice==
字符串匹配优先搜索，但是==只是记录下最长的匹配== ，然后继续搜索正则匹配，如果有正则匹配，则命中正则匹配，如果没有正则匹配，则命中最长的字符串匹配


##### exmaple

* 精准匹配
```shell
location / {
    root   html;
    index  index.html index.htm;
    echo "this is /";
}

location = / {
    root /home/wwww;
    index index.html index.htm;
    echo "this is = /";
}


http://xxxxx/                       this is = /
http://xxxxx/index.html             this is /
```
* 精准匹配
```shell
location = /static/ {
    echo 'config1';
}

location /static/index.html {
    echo 'config2';
}
location ~ \/static\/ {
    echo 'config5';
}
location ~ \/static\/index.html {
    echo 'config4';
}

http://192.168.1.222/static/index.html         config5
config2只做记录，往下匹配正则，匹配了config5后立即停止后面的正则匹配(规则顺序关乎正则匹配结果)
```
* 字符串匹配优先级的提升
```shell
location /static/ {
    echo 'config2';
}

location ^~ /static/index.html {
    echo 'config3';
}
location ~ \/static {
    echo 'config5';
}

location ~ \/static\/index.html {
    echo 'config4';
}

http://192.168.1.222/static/index.html     config3
因为字符串匹配是优先搜索的，此时发现config3 为最长的字符串匹配且为^~匹配方式，所以停止搜索正则，直接命中！
```



