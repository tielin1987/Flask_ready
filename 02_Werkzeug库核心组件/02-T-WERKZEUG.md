##  Werkzeug 
----
<font size=5 color="#797979">

__*Bullshit attitude matches fucker's life.*__

</font>

<font size=2 color="#333">

操蛋的人生，扯淡的态度.

</font>

----


<font size=4 color="#333"> 

__WERKZEUG__ </font> 

<font size=3 color="#333"> 
Werkzeug 是一个 WSGI 工具包，它可以作为一个 Web 框架的底层库，主要提供了如下几种工具：

1 Request 和 Response 优美封装，经典的Mixin
2 强大的路由解析功能
3 健壮的上下文，实现不同线程间数据的隔离

</font>

<font size=4 color="#333"> 

__Environ__ </font> 

<font size=3 color="#333"> 
Environ 全局变量，贯穿于整个请求-响应过程，了解 Environ 有助于 Werkzeug框架理解.Environ 主要包括 WSGI 请求中属性，具体参照HTTP.    
</font> 
<pre>
environ['SERVER_NAME'] = 'neuedu-micro-server'
environ['GATEWAY_INTERFACE'] = 'CGI/1.1'
environ['SERVER_PORT'] = '8889'
environ['REMOTE_HOST'] = ''
environ['CONTENT_LENGTH'] = ''
environ['SCRIPT_NAME'] = ''
environ['HTTPS'] = 'off'
environ["wsgi.version"] = (1, 0)
environ["wsgi.input"] = self.body
environ["wsgi.error"] = None
environ["wsgi.multithread"] = False
environ["wsgi.multiprocess"] = False
environ["wsgi.run_once"] = False
environ["wsgi.url_scheme"] 
environ['SERVER_SOFTWARE'] = 'neuedu-wsgi'
environ['SERVER_PROTOCOL'] = self.version
environ['REQUEST_METHOD'] = self.commond
environ['PATH_INFO'] = self.path
environ['QUERY_STRING'] = self.query
environ['CONTENT_TYPE'] = self.headers.get('Content-Type', 'text/plain')
environ["CONTENT_LENGTH"] = self.headers.get('Content-Length')     
</pre>
