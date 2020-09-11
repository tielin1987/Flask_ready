##  APP
----

<font size=5 color="#797979">

__*The shortest answer is doing.*__

</font>

<font size=2 color="#333">

run 方法完成 server 启动,调用 wsgi_app 最后full_dispatch_request 分发请求.

</font>

----

<font size=4 color="#333"> 

__Run__ </font> 

<pre>

def run(self, host=None, port=None, debug=None, load_dotenv=True, **options):
        .......
        if server_name:
            sn_host, _, sn_port = server_name.partition(":")

        if not host:
            if sn_host:
                host = sn_host
            else:
                host = "127.0.0.1"

        if port or port == 0:
            port = int(port)
        elif sn_port:
            port = int(sn_port)
        else:
            port = 5000

        ........
        from werkzeug.serving import run_simple

        try:
            run_simple(host, port, self, **options)
        finally:
            self._got_first_request = False
</pre>

<font size=3 color="#333"> 

__wsgi_app__  
</font>
<pre>

def wsgi_app(self, environ, start_response):
    # 创建请求上下文
    ctx = self.request_context(environ)
    ctx.push()
    error = None

    try:
        try:
            # 路由分发
            response = self.full_dispatch_request()
        except Exception as e:
            # 错误处理，默认是 InternalServerError 错误处理函数，客户端会看到服务器 500 异常
            error = e
            response = self.handle_exception(e)
        return response(environ, start_response)
    finally:
        if self.should_ignore_error(error):
            error = None
        # 不管处理是否发生异常，都需要把栈中的请求 pop 出来
        ctx.auto_pop(error)
</pre>

<font size=3 color="#333"> 

__full_dispatch_request__  
</font>
<pre>
def full_dispatch_request(self):

    #请求触发
    self.try_trigger_before_first_request_functions()
    try:
        request_started.send(self)

        #请求处理
        rv = self.preprocess_request()
        if rv is None:
            rv = self.dispatch_request()
    except Exception as e:
        rv = self.handle_user_exception(e)
        
        #完成请求开始响应
    return self.finalize_request(rv)
</pre>
