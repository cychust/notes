# curl命令



## curl命令笔记

```
$ curl www.sina.com
```

保存网页

```bash
curl -o [文件名] www.sina.com
```

有的网址是自动跳转的。使用`-L`参数，curl就会跳转到新的网址。

```bash
$ curl -L www.sina.com
```

`-i`参数可以显示http response的头信息，连同网页代码一起。

```bash
$ curl -i www.sina.com
```

`-I`参数则是只显示http response的头信息。



`-v`参数可以显示一次http通信的整个过程，包括端口连接和http request头信息。

```
$ curl -v www.sina.com
```



```bash
* Rebuilt URL to: www.sina.com/
*   Trying 101.71.100.123...
* TCP_NODELAY set
* Connected to www.sina.com (101.71.100.123) port 80 (#0)
> GET / HTTP/1.1
> Host: www.sina.com
> User-Agent: curl/7.58.0
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< Server: nginx
< Date: Sat, 30 Mar 2019 10:55:46 GMT
< Content-Type: text/html
< Content-Length: 178
< Connection: keep-alive
< Location: http://www.sina.com.cn/
< Expires: Sat, 30 Mar 2019 10:55:54 GMT
< Cache-Control: max-age=120
< X-Via-SSL: ssl.47.sinag1.jsl.lb.sinanode.com
< Age: 112
< Via: https/1.1 cnc.jinan.ha2ts4.50 (ApacheTrafficServer/6.2.1 [cRs f ]), https/1.1 cnc.ningbo.ha2ts4.26 (ApacheTrafficServer/6.2.1 [cRs f ])
< X-Via-Edge: 15539433468848a13133a7b6447657dabb849
< X-Cache: HIT.26
< X-Via-CDN: f=edge,s=cnc.ningbo.ha2ts4.27.nb.sinaedge.com,c=58.19.19.138;f=Edge,s=cnc.ningbo.ha2ts4.26,c=101.71.100.27
< 
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
* Connection #0 to host www.sina.com left intact
```



如果你觉得上面的信息还不够，那么下面的命令可以查看更详细的通信过程。

```
$ curl --trace output.txt www.sina.com
//或
$ curl --trace-ascii output.txt www.sina.com
```



#### 发送Json信息

```bash
curl -d '{}' -X POST -H "Content-Type: application/json"  www.sina.com
```





#### 发送表单信息

发送表单信息有GET和POST两种方法。GET方法相对简单，只要把数据附在网址后面就行。

> 　　$ curl example.com/form.cgi?data=xxx

POST方法必须把数据和网址分开，curl就要用到--data参数。

> 　　$ curl -X POST --data "data=xxx" example.com/form.cgi

如果你的数据没有经过表单编码，还可以让curl为你编码，参数是`--data-urlencode`。

> 　　$ curl -X POST--data-urlencode "date=April 1" example.com/form.cgi



curl默认的HTTP动词是GET，使用`-X`参数可以支持其他动词。

> 　　$ curl -X POST www.example.com

> 　　$ curl -X DELETE www.example.com



#### 文件上传

假定文件上传的表单是下面这样：

> 　　<form method="POST" enctype='multipart/form-data' action="upload.cgi">
> 　　　　<input type=file name=upload>
> 　　　　<input type=submit name=press value="OK">
> 　　</form>

你可以用curl这样上传文件：

> 　　$ curl --form upload=@localfilename --form press=OK [URL]

#### Referer字段

有时你需要在http request头信息中，提供一个referer字段，表示你是从哪里跳转过来的。

> $ curl --referer http://www.example.com http://www.example.com

#### User Agent字段**

这个字段是用来表示客户端的设备信息。服务器有时会根据这个字段，针对不同设备，返回不同格式的网页，比如手机版和桌面版。

iPhone4的User Agent是

> 　　Mozilla/5.0 (iPhone; U; CPU iPhone OS 4_0 like Mac OS X; en-us) AppleWebKit/532.9 (KHTML, like Gecko) Version/4.0.5 Mobile/8A293 Safari/6531.22.7

curl可以这样模拟：

> 　　$ curl --user-agent "[User Agent]" [URL]

#### cookie

使用`--cookie`参数，可以让curl发送cookie。

> 　　$ curl --cookie "name=xxx" www.example.com

至于具体的cookie的值，可以从http response头信息的`Set-Cookie`字段中得到。

`-c cookie-file`可以保存服务器返回的cookie到文件，`-b cookie-file`可以使用这个文件作为cookie信息，进行后续的请求。

> 　　$ curl -c cookies http://example.com
> 　　$ curl -b cookies http://example.com

#### 增加头信息

有时需要在http request之中，自行增加一个头信息。`--header`参数就可以起到这个作用。

> 　　$ curl --header "Content-Type:application/json" http://example.com

#### HTTP认证

有些网域需要HTTP认证，这时curl需要用到`--user`参数。

> 　　$ curl --user name:password example.com