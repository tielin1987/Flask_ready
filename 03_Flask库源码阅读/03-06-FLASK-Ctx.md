## CTX
----

<font size=5 color="#333">

__*Namespaces are one honking great idea -- let's do more of those*.__

</font>

----


<font size=4 color="#333"> 

__CTX__</font> 

<font size=3 color="#333">

CTX 包括三部分 _AppCtxGlobals，Appcontext 和 Requestcontext.其中_AppCtxGlobals为Appcontext存储数据，类似namespace.通过上下文管理器实现了自动关闭功能.


current_app:当前应用程序上下文.

</font>

<font size=2  color="#191970">
<b> 
Tag : _AppCtxGlobals，Appcontext和Requestcontext
</b>
</font>

------
 
<font size=3 color="#333">

__应用上下文__
</font>
 <font size=3>

Appcontext生成应用上下文，主要对象包括current_app,g.
current_app:当前应用程序上下文.
g:全局上下文
 </font>
<font size=3>

 ___AppCtxGlobals__ 
 </font>
<pre>
class _AppCtxGlobals:

    def get(self, name, default=None):
        return self.__dict__.get(name, default)

    def pop(self, name, default=_sentinel):
        if default is _sentinel:
            return self.__dict__.pop(name)
        else:
            return self.__dict__.pop(name, default)

    def setdefault(self, name, default=None):
        return self.__dict__.setdefault(name, default)

    def __contains__(self, item):
        return item in self.__dict__

    def __iter__(self):
        return iter(self.__dict__)

    def __repr__(self):
        top = _app_ctx_stack.top
        if top is not None:
            return f"<flask.g of {top.app.name!r}>"
        return object.__repr__(self)
</pre>

<pre>
class AppContext:
    
    def __init__(self, app):
        self.app = app
        self.url_adapter = app.create_url_adapter(None)
        self.g = app.app_ctx_globals_class()

    def push(self):
        self._refcnt += 1
        _app_ctx_stack.push(self)
        appcontext_pushed.send(self.app)

    def pop(self, exc=_sentinel):
        
        try:
            self._refcnt -= 1
            if self._refcnt <= 0:
                if exc is _sentinel:
                    exc = sys.exc_info()[1]
                self.app.do_teardown_appcontext(exc)
        finally:
            rv = _app_ctx_stack.pop()
        assert rv is self, f"Popped wrong app context.  ({rv!r} instead of {self!r})"
        appcontext_popped.send(self.app)

    def __enter__(self):
        self.push()
        return self

    def __exit__(self, exc_type, exc_value, tb):
        self.pop(exc_value)

</pre>
<font size=3 color="#333">

__请求上下文__
</font>
 <font size=3>

Requestcontext：请求上下文，保存request对象数据据，请求上线文对象有：request、session.
request:封装了HTTP请求的内容，针对的是http请求;session:用来记录请求会话中的信息，针对的是用户信息.
 </font>
<font size=3>

 __RequestContext__ 
 </font>
  
<pre>
class RequestContext:

    def __init__(self, app, environ, request=None, session=None):

        self.app = app

        if request is None:
            request = app.request_class(environ)
        self.request = request

        self.url_adapter = None

        try:
            self.url_adapter = app.create_url_adapter(self.request)
        except HTTPException as e:
            self.request.routing_exception = e

        self.flashes = None
        self.session = session
        self._implicit_app_ctx_stack = []
        self.preserved = False
        self._preserved_exc = None
        self._after_request_functions = []

    @property
    def g(self):
        return _app_ctx_stack.top.g

    @g.setter
    def g(self, value):
        _app_ctx_stack.top.g = value

    def copy(self):
    
        return self.__class__(
            self.app,
            environ=self.request.environ,
            request=self.request,
            session=self.session,
        )

    def match_request(self):
        try:
            result = self.url_adapter.match(return_rule=True)
            self.request.url_rule, self.request.view_args = result
        except HTTPException as e:
            self.request.routing_exception = e

    def push(self):

        top = _request_ctx_stack.top
        if top is not None and top.preserved:
            top.pop(top._preserved_exc)

        app_ctx = _app_ctx_stack.top
        if app_ctx is None or app_ctx.app != self.app:
            app_ctx = self.app.app_context()
            app_ctx.push()
            self._implicit_app_ctx_stack.append(app_ctx)
        else:
            self._implicit_app_ctx_stack.append(None)

        _request_ctx_stack.push(self)

        if self.session is None:
            session_interface = self.app.session_interface
            self.session = session_interface.open_session(self.app, self.request)

            if self.session is None:
                self.session = session_interface.make_null_session(self.app)

        if self.url_adapter is not None:
            self.match_request()

    def pop(self, exc=_sentinel):
        
        app_ctx = self._implicit_app_ctx_stack.pop()
        clear_request = False

        try:
            if not self._implicit_app_ctx_stack:
                self.preserved = False
                self._preserved_exc = None
                if exc is _sentinel:
                    exc = sys.exc_info()[1]
                self.app.do_teardown_request(exc)

                request_close = getattr(self.request, "close", None)
                if request_close is not None:
                    request_close()
                clear_request = True
        finally:
            rv = _request_ctx_stack.pop()

            if clear_request:
                rv.request.environ["werkzeug.request"] = None

            if app_ctx is not None:
                app_ctx.pop(exc)

            assert (
                rv is self
            ), f"Popped wrong request context. ({rv!r} instead of {self!r})"

    def auto_pop(self, exc):
        if self.request.environ.get("flask._preserve_context") or (
            exc is not None and self.app.preserve_context_on_exception
        ):
            self.preserved = True
            self._preserved_exc = exc
        else:
            self.pop(exc)

    def __enter__(self):
        self.push()
        return self

    def __exit__(self, exc_type, exc_value, tb):
        self.auto_pop(exc_value)
  
</pre>
