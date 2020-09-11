## HTTP[Response]
----
<font size=5 color="#797979">

__*Namespaces are one honking great idea -- let's do more of those!*.__
</font>


----
<font size=4 color="#888"> __HTTP响应__</font>

+ 响应行
+ 响应头
+ 响应体

<font size=4> __响应行__</font>
<font size=3>由协议版本、状态码及其描述组成 比如 HTTP/1.1 200 OK议和协议的版本。</font>

<font size=3> __响应状态码__</font>
+ 1xx 消息，一般是告诉客户端，请求已经收到了，正在处理，别急...
+ 2xx 处理成功，一般表示：请求收悉、我明白你要的、请求已受理、已经处理完成等信息.
+ 3xx 重定向到其它地方。它让客户端再发起一个请求以完成整个处理。
+ 4xx 处理发生错误，责任在客户端。
+ 5xx 处理发生错误，责任在服务端。

<font size=4> __响应头__</font>
<font size=3>
响应头用于描述服务器的基本信息，以及数据的描述，服务器通过这些数据的描述信息，可以通知客户端如何处理等一会儿它回送的数据。

+ Allow：服务器支持哪些请求方法。
+ Content-Encoding：文档的编码(Encode)方法。
+ Content-Type头指定的内容类型。
+ Accept-Encoding检查浏览器是否支持gzip。
+ Content-Length：内容长度。
+ Content- Type：MIME类型。Servlet默认为text/plain，需指定为text/html。HttpServletResponse提供了一个专用的方法setContentType。
+ Date：当前的GMT时间。
+ Expires：告诉浏览器把回送的资源缓存多长时间。
+ Last-Modified：文档的最后改动时间。
+ Location：这个头配合302状态码使用，用于重定向接收者到一个新URI地址。
+ Refresh：告诉浏览器隔多久刷新一次，以秒计。
+ Server：服务器通过这个头告诉浏览器服务器的类型。
+ Set-Cookie：设置和页面关联的Cookie。
+ Transfer-Encoding：告诉浏览器数据的传送格式。
+ WWW-Authenticate：客户应该在Authorization头中提供授权信息。
</font>

<font size=4> __响应体__</font>
<font size=3>
服务器返回提信息
</font>


