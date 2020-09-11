## HTTP[Request]
----
<font size=5 color="#797979">

__*Namespaces are one honking great idea -- let's do more of those!*.__
</font>

----

<font size=4 color="#888"> __HTTP请求__</font>

+ 请求方法URI协议/版本
+ 请求头(Request Header)
+ 请求正文

<font size=4> __请求方法URL议/版本__:</font>
<font size=3>Example： GET/sample.jsp HTTP/1.1
“GET”代表请求方法，“/sample.jsp”表示URI，“HTTP/1.1代表协议和协议的版本。</font>

<font size=3>__请求方法__
GET,通过请求URI得到资源
POST,用于添加新的内容
PUT用于修改某个内容
DELETE,删除某个内容
PATCH,部分文档更改</font>

<font size=4> __请求头(Request Header)__:请求头包含许多有关的客户端环境和请求正文的有用信息</font>
<font size=3>
Accept:image/gif.image/jpeg.*/*  浏览器可以接受的文件类型
Accept-Language:zh-cn  浏览语言
Accept-encoding:utf-8  数据编码
Connection:Keep-Alive
Cookie：用户相关的信息都存在 Cookie 里。
Host:localhost
User-Agent:Mozila/4.0(compatible:MSIE5.01:Windows NT5.0)
</font>

<font size=4> __请求正文__</font>
<font size=3>
客户提交的查询字符串信息
</font>


