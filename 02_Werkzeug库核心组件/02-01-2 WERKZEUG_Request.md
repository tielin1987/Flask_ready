##  BaseRequest
----

<font size=5 color="#797979">

__*This code is a bit of a head start.*__

</font>

<font size=2 color="#333">
这段代码有点上头！
</font>

----
<font size=4 color="#333">

__BaseRequest__ 
</font>

<font size=3 color="#333">

初始化Request请求中属性，通过from_values构建请求，并在application中传递请求.

</font>

<font size=2  color="#191970">

<b> Tag : Request属性 from_values application</b></font>
____

<font size=4 color="#333">

__BaseRequest属性__

</font>
<font size=3 color="#333">
Request只读属性主要通过cached_property进行设置
这段代码简单且长，不进行列举，其中下表中加粗的为常用方法.
</font>


| 方法 | 作用    | 
| --------  | --------   |  
| __form__ | 一个从POST和PUT请求参数| 
| __args__ | URL中提交的参数 GET请求| 
| __values__| values替代form和args    |   
| __data__  | 请求的数据，并转换为字符串| 
| __files__ | POST或PUT请求上传的文件    | 
| environ| WSGI隐含的环境配置| 
| method | 请求方法 | 
| path | 获取请求文件路径    |  
| base_url |获取域名与请求文件路径 | 
| url	 | URL|
| cookies |请求的cookies，类型是dict| 
| url_root | 获取域名    |  
| is_xhr | 一个从POST和PUT请求解析的 MultiDict | 
| blueprint | URL中提交的参数 GET请求 | 
| endpoint | endpoint匹配请求    |  
| json | 解析JSON数据 | 
| max_content_length | max_content_length | 
| url_rule | 内部规则匹配请求的URL    | 

<font size=4 color="#333">

__from_values__

</font>
<font size=3 color="#333">
from_values通过EnvironBuilder.get_request方法构造请求对象.
</font>
<pre>

    #构建请求对象
    @classmethod
    def from_values(cls, *args, **kwargs):
        from ..test import EnvironBuilder
        charset = kwargs.pop("charset", cls.charset)
        kwargs["charset"] = charset
        builder = EnvironBuilder(*args, **kwargs)
        try:
            return builder.get_request(cls)
        finally:
            builder.close()
</pre>
<font size=4 color="#333">

__BaseRequest属性__

</font>
<font size=3 color="#333">
application装饰器，生成response.
</font>

<pre>
 @classmethod
    def application(cls, f: Callable) -> Callable:
        from ..exceptions import HTTPException
        def application(*args):
            request = cls(args[-2])
            with request:
                try:
                    resp = f(*args[:-2] + (request,))
                except HTTPException as e:
                    resp = e.get_response(args[-2])
                return resp(*args[-2:])

        return update_wrapper(application, f)
</pre>