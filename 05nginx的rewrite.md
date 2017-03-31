## rewrite


#### 目的
使用nginx提供的==全局变量==或==自己设置的变量==，结合正则表达式和标志位实现==url重写以及重定向==

#### 范围
rewrite只能放在server{},location{},if{}中，并且只能对域名后边的除去传递的参数外的字符串起作用，例如 http://xxx.com/a/b/index.php?id=x&u=y 只对/a/b/index.php重写

如果相对域名或参数字符串起作用，可以使用全局变量匹配，也可以使用proxy_pass反向代理

#### 区别点
rewrite和location功能有点像，都能实现跳转，主要区别在于rewrite是在同一域名内更改获取资源的路径，而location是对一类路径做控制访问或反向代理，可以proxy_pass到其他机器

#### 执行顺序
1. 执行server块中的rewrite
2. 执行location匹配
3. 执行选定的location中的rewrite

如果其中某步URI被重写，则重新循环执行1-3，直到找到真实存在的文件；循环超过10次，则返回500 Internal Server Error错误


#### 语法
```shell
rewrite reg replacement [flag]

flag标志位
    last : 相当于Apache的[L]标记，表示完成rewrite
    break : 停止执行当前虚拟主机的后续rewrite指令集
    redirect : 返回302临时重定向，地址栏会显示跳转后的地址
    permanent : 返回301永久重定向，地址栏会显示跳转后的地址


last和break的区别
    1. last一般写在server和if中，而break一般使用在location中,last后面的代码不会执行。
    2. last重新将rewrite后的地址在server标签中执行， break将rewrite后的地址在当前location标签中执行
```

#### 用到的指令
* break
```shell
# 该指令的作用是完成当前的规则集，不再处理rewrite指令
Syntax:	break;
Default:—
Context:server, location, if
```
* if
```shell
# 用于检查一个条件是否符合，如果条件符合，则执行大括号内的语句。If指令不支持嵌套，不支持多个条件&&和||处理
Syntax:	if (condition) { ... }
Default:—
Context:server, location
 * 当表达式只是一个变量时，如果值为空或任何以0开头的字符串都会当做false
 * 直接比较变量和内容时，使用=或!=
 * ~正则表达式匹配，~*不区分大小写的匹配，!~区分大小写的不匹配
 
 -f和!-f用来判断是否存在文件
-d和!-d用来判断是否存在目录
-e和!-e用来判断是否存在文件或目录
-x和!-x用来判断文件是否可执行
```
* set
```shell
# 用于定义一个变量，并给变量赋值。变量的值可以为文本、变量以及文本变量的联合
Syntax:	set variable value;
Default:—
Context:server, location, if
```
* return
```shell
# 用于结束规则的执行并返回状态码给客户端
Syntax:	return code [text];
return code URL;
return URL;
Default:—
Context:server, location, if
```
* rewrite 
```shell
# 根据表达式来重定向URI，或者修改字符串。
Syntax:	rewrite regex replacement [last|break|redirect|permanent];
Default:—
Context:server, location, if
```
* rewrite_log  
```shell
Syntax:	rewrite_log on | off;
Default:	
rewrite_log off;
Context:http, server, location, if
```
* uninitialized_variable_warn  
```shell
Syntax:	uninitialized_variable_warn on | off;
Default:	
uninitialized_variable_warn on;
Context:http, server, location, if
```

#### 全局变量
```html
以get请求方式请求下面地址为例
http://192.168.1.222/static/index.html?a=x&b=y
```
|变量名 |               解释               |     example|
|-------|----------------------------------|------------|
|$args  |请求行中的参数，等同于$query_string| a=x&b=y|
|$content_length  |请求头中的Content-length字段|          |
|$content_type  |请求头中的Content-Type字段|          |
|$document_root  |当前请求在root指令中指定的值| /usr/local/nginx1.9/html|
|$host  |请求主机头字段，否则为服务器名称| 192.168.1.222|
|$http_user_agent  |客户端agent信息| Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36 |
|$http_cookie  |客户端cookie信息|          |
|$limit_rate  |这个变量可以限制连接速率| 0|
|$request_method  |客户端请求的动作，通常为GET或POST| GET |
|$remote_addr  |客户端ip| 192.168.1.27|
|$remote_port  |客户端端口| 57672 |
|$remote_user  |已经经过Auth Basic Module验证的用户名|          |
|$request_filename  |当前请求的文件路径，由root或alias指令与URI请求生成|/usr/local/nginx1.9/html/static/index.html|
|$scheme  |HTTP方法（如http，https)| http|
|$server_protocol  |请求使用的协议，通常是HTTP/1.0或HTTP/1.1| HTTP/1.1 |
|$server_addr  |服务器地址|192.168.1.222|
|$server_name  |服务器名称| localhost |
|$server_port  |服务端口| 80 |
|$request_uri  |包含请求参数的原始URI，不包含主机名|/static/index.html?a=x&b=y|
|$uri  |不带请求参数的当前URI，$uri不包含主机名|/static/index.html|
|$document_uri  |与$uri相同|/static/index.html |

#### 常用正则
```shell
* . ： 匹配除换行符以外的任意字符
* ? ： 重复0次或1次
* + ： 重复1次或更多次
* * ： 重复0次或更多次
* \d ：匹配数字
* ^ ： 匹配字符串的开始
* $ ： 匹配字符串的结束
* {n} ： 重复n次
* {n,} ： 重复n次或更多次
* [c] ： 匹配单个字符c
* [a-z] ： 匹配a-z小写字母的任意一个

小括号()之间匹配的内容，可以在后面通过$1来引用，$2表示的是前面第二个()里的内容。正则里面容易让人困惑的是\转义特殊字符。
```


参考
http://seanlook.com/2015/05/17/nginx-location-rewrite/



