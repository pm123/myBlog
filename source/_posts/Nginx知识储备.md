---
title: Nginx知识储备
date: 2022-08-23 10:22:53
tags:
---

#### location 匹配顺序

`=` 开头表示精确匹配 ,如 `location = / {} `中只匹配根目录结尾的请求，后面不能带任何字符串;

`^~` 开头表示uri以某个常规字符串开头，不是正则匹配; <匹配符合以后，停止往下搜索正则，采用这一条>f

`~` 开头表示区分大小写的正则匹配;

`~*` 开头表示不区分大小写的正则匹配;

`/` 通用匹配, 如果没有其它匹配,任何请求都会匹配到;

```undefined
正则location和普通location

正则location  “~”和“~*”：“~”表示区分大小写；“~*”表示不区分大小写

普通location:  除了上面其余全是(包括没有前缀) “=”，“^~”，“@”

“^~”中的“^”表示非，“~”表示正则，意思为不要继续匹配正则

“=”也表示阻止正则location，和“^~”的区别为：“^~”依然遵守“最大前缀”匹配；而“=”必须是严格匹配。

“@ ”是用来定义“Named Location ”的（可以理解为独立于“普通location”和“正则location”之外的第三种类型），这种“Named Location ”不是用来处理普通的HTTP 请求的，它是专门用来处理“内部重定向（internally redirected ）”请求的。

注意：这里说的“内部重定向（internally redirected ）”是不需要跟浏览器交互的，纯粹是服务端的一个转发行为。
```



```undefined
1.匹配的顺序是先匹配普通字符串，然后再匹配正则表达式。另外普通字符串匹配顺序是根据配置中字符长度从长到短，也就是说使用普通字符串配置的location顺序是无关紧要的，反正最后nginx会根据配置的长短来进行匹配，但是需要注意的是正则表达式按照配置文件里的顺序测试。找到第一个匹配的正则表达式将停止搜索。

2.一般情况下，匹配成功了普通字符串location后还会进行正则表达式location匹配。有两种方法改变这种行为，其一就是使用“=”前缀，这时执行的是严格匹配，并且匹配成功后立即停止其他匹配，同时处理这个请求；另外一种就是使用“^~”前缀，如果把这个前缀用于一个常规字符串那么告诉nginx 如果路径匹配那么不测试正则表达式。
```



```css
1.nginx匹配顺序是先匹配普通location，在匹配正则location；

2.普通匹配规则无顺序无论写在配置文件的那个地方都一样，但是正则匹配则是按照匹配配置文件中由上到下的先后顺序匹配；
```

##### 优先级

```go
(location `=` ) > (location `完整路径` ) > (location `^~` 路径) > (location `~`,`~*` 从上向下正则顺序，匹配在最后一条终止) > (location 部分起始路径) > (`/`)
```



正则表达式匹配，其中：

* ~ 为区分大小写匹配

* ~* 为不区分大小写匹配

* !~和!~*分别为区分大小写不匹配及不区分大小写不匹配

  

  文件及目录匹配，其中：

* -f和!-f用来判断是否存在文件

* -d和!-d用来判断是否存在目录

* -e和!-e用来判断是否存在文件或目录

* -x和!-x用来判断文件是否可执行

  

  flag标记有：

* last 相当于Apache里的[L]标记，表示完成rewrite

* break 终止匹配, 不再匹配后面的规则

* redirect 返回302临时重定向 地址栏会显示跳转后的地址

* permanent 返回301永久重定向 地址栏会显示跳转后的地址

  

  

  #### 全局变量

  $args, 请求中的参数;
  $content_length, HTTP请求信息里的"Content-Length";
  $content_type, 请求信息里的"Content-Type";
  $document_root, 针对当前请求的根路径设置值;
  $document_uri, 与$uri相同;
  $host, 请求信息中的"Host"，如果请求中没有Host行，则等于设置的服务器名;
  $limit_rate, 对连接速率的限制;
  $request_method, 请求的方法，比如"GET"、"POST"等;
  $remote_addr, 客户端地址;
  $remote_port, 客户端端口号;
  $remote_user, 客户端用户名，认证用;
  $request_filename, 当前请求的文件路径名
  $request_body_file
  $request_uri, 请求的URI，带查询字符串;
  $query_string, 与$args相同;
  $scheme, 所用的协议，比如http或者是https，比如rewrite  ^(.+)$  $scheme://example.com$1  redirect;
  $server_protocol, 请求的协议版本，"HTTP/1.0"或"HTTP/1.1";
  $server_addr, 服务器地址，如果没有用listen指明服务器地址，使用这个变量将发起一次系统调用以取得地址(造成资源浪费);
  $server_name, 请求到达的服务器名;
  $server_port, 请求到达的服务器端口号;
  $uri, 请求的URI，可能和最初的值有不同，比如经过重定向之类的。