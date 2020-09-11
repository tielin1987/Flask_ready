##  Werkzeug路由系统 

----
<font size=5 color="#797979">

__*There is nothing that one white eye can't solve. If there are, just two.*__

</font>

<font size=2 color="#333">
要命呐......
</font>

----
<font size=4  color="#333"> __路由系统过程__ </font> 

<font size=3 color="#333">

route是入口函数，提取request参数并传递.add_url_rule接收request参数，并对视图函数，断点及url进行映射.dispatch_request分发视图函数.

</font>

<font size=2  color="#191970">
<b> Tag : route add_url_rule dispatch_request</b></font>

____
<font size=4> __route__ </font> 
提取endpoint rule
<pre>

def route(self, rule, **options):
    def decorator(f):
        endpoint = options.pop('endpoint', None)
        self.add_url_rule(rule, endpoint, f, **options)
        return f
    return decorator

</pre>
<font size=4> __add_url_rule__ </font> 

+ 处理 endpoint: 由函数参数提供，或者默认为函数名称
+ 处理 methods （GET, HEAD, OPTIONS, POST 等）
+ 将Rule路由规则类 添加到 url_map
+ 将 endpoint 和 view function 的映射添加到 url_functions (dict）

<pre>
def add_url_rule(self, rule, endpoint=None, view_func=None, **options):

	# 如果没有指定endpoint，则默认为view functon的函数名
	if endpoint is None:
	    endpoint = _endpoint_from_view_func(view_func)	

	# 将 endpoint 加入到 options(dict)
	options["endpoint"] = endpoint
	
	# methods: GET,POST,PUT
	methods = options.pop("methods", None)

	# 从view function获取method，没有则为GET
	if methods is None:
        methods = getattr(view_func, "methods", None) or ("GET",) 

    # 将 methods改变为set类型
    methods = set(item.upper() for item in methods)
	
	# Rule 路由规则类
	rule = self.url_rule_class(rule, methods=methods, **options)

    # URL  映射类
    self.url_map.add(rule)
	
	# 添加view functions
	if view_func is not None:	   
	   self.view_functions[endpoint] = view_func
</pre>
<font size=4> __dispatch_request__ </font>
+ 分发URL
+ 分发view_func 
<pre>
def dispatch_request(self):

    #请求出栈
    req = _request_ctx_stack.top.request
    if req.routing_exception is not None:
        self.raise_routing_exception(req)

    #分发URL   
    rule = req.url_rule

    #请求对应的视图函数
    return self.view_functions[rule.endpoint](**req.view_args)
</pre>
