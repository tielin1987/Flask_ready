##  Request
----

<font size=5 color="#797979">

__*Complex is better than complicated.*__

</font>

<font size=2 color="#333">
Mixin模式采用最小接口原则,通过多继承的方式来实现.
</font>

----

<font size=4 color="#333">

__Request__
</font>

<font size=3 color="#333">

Request接受WSGI-Environment为参数进行初始化，并且提供了一系列的方法用来解析和利用environ中的信息，其功能实现向主要通过Mixin方式实现.Request绝大部分功能都由BaseRequest实现，比较常用.

+ BaseRequest ——接受environ信息
+ AcceptMixin类 ——请求报文中关于客户端接收的数据类型.
+ ETagRequestMixin类 ——请求报文中Etag和Cache.
+ UserAgentMixin类 ——请求报文中关于user_agent.
+ AuthorizationMixin类 ——请求报文中认证的类.
+ CommonRequestDescriptorsMixin类 ——获取请求首部中的相关信息.
+ CORSRequestMixin ——跨站攻击.

</font>

<font size=2  color="#191970">
<b> Tag : AcceptMixin ETagRequestMixin UserAgentMixin AuthorizationMixin CommonRequestDescriptorsMixin BaseRequest </b></font>

