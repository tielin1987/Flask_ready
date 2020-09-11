##  FLASK流程
----

<font size=5 color="#797979">

__*Know what it is, and then know why.*__

</font>

<font size=2 color="#333">

先知其然，然后知其所以然.只有跑通Flask请求到返回响应全过程，才能对Flask框架有整体印象。

</font>

----

<font size=4 color="#333"> 

__1 Flask 对象初始化__ </font> 

<font size=3 color="#333"> 

Flask 对象初始化目的是为 Flask APP 运行做准备.核心材料有 Request Response static Rule Map Config.

</font>

<font size=4 color="#333"> 

__2 注册视图函数__ </font> 

<font size=3 color="#333"> 

Flask 用 route 注册视图函数. route 装饰器中 add_url_rule注册 Rule ; view_functions中注册视图函数.

</font>

<pre>
def route(self, rule, **options):
    def decorator(f):
        self.add_url_rule(rule, f.__name__, **options)
        self.view_functions[f.__name__] = f
        return f
    return decorator
</pre>

<font size=3 color="#333"> 

add_url_rule 函数中， option 添加 endpoint 和 methods.url_map 添加Rule规则.

</font>
<pre>
def add_url_rule(self, rule, endpoint, **options):
    options['endpoint'] = endpoint
    options.setdefault('methods', ('GET',))  #  默认监听GET方法
    self.url_map.add(Rule(rule, **options))
</pre>

<font size=4 color="#333"> 

__3 Flask 请求-响应__ 

</font> 
 
<font size=3 color="#333"> 

Flask 通过 wsgi_app 启用响应流程. with 语句触发 request_contex 中的__enter__方法，推送请求上下文到堆栈中。在经过请求预处理，请求分发，生成响应，响应处理后返回响应。 

</font>

<font size=3 color="#333"> 

__3.1 Flask 请求预处理__ 

</font> 
 
<font size=3 color="#333"> 

preprocess_request调用 before_request() 装饰的函数 

</font>

<pre>
def preprocess_request(self):
        for func in self.before_request_funcs:
            rv = func()
            if rv is not None:
                return rv
</pre>

<font size=3 color="#333"> 

__3.2 Flask 请求分发__ 

</font> 
 
<font size=3 color="#333"> 

dispatch_request 根据 url 返回对应的 endpoint 和 values. 

</font>

<pre>
def dispatch_request(self):
    try:
        endpoint, values = self.match_request()
        return self.view_functions[endpoint](**values)
    except HTTPException, e:
        handler = self.error_handlers.get(e.code)
        if handler is None:
            return e
        return handler(e)
    except Exception, e:
        handler = self.error_handlers.get(500)
        if self.debug or handler is None:
            raise
        return handler(e)
</pre>

<font size=3 color="#333"> 

__3.3 Flask 生成响应__ 

</font> 
 
<font size=3 color="#333"> 

make_response 对请求分发的返回值创建响应对象. 

</font>

<pre>
def make_response(self, rv):
    if isinstance(rv, self.response_class):
        return rv
    if isinstance(rv, basestring):
        return self.response_class(rv)
    if isinstance(rv, tuple):
        return self.response_class(*rv)
    return self.response_class.force_type(rv, request.environ)
</pre>

<font size=3 color="#333"> 

__3.4 Flask 响应处理__ 

</font> 
 
<font size=3 color="#333"> 

process_response 主要添加 session 及 后处理函数结果. 

</font>

<pre>
def process_response(self, response):
    session = _request_ctx_stack.top.session
    if session is not None:
        self.save_session(session, response)
    for handler in self.after_request_funcs:
        response = handler(response)
    return response
</pre>
