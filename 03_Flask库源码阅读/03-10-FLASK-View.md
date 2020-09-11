## View
----

<font size=5 color="#797979">

__*Black vitality*.__
</font>

<font size=2  color="#333">
黑色生命力，元类编程.
</font>

----

<font size=4 color="#333"> __View__ </font>

<font size=3 color="#333"> 

View 包括标注视图类 view 基于request method 的 MethodView.

</font>

<font size=2  color="#191970">
<b> 
Tag : View MethodView
</b></font>

----

<font size=3 color="#333" > __View__ </font> 

<font size=3 color="#333" > View 需要重写 def dispatch_request(self)</font> 


<pre>
http_method_funcs = frozenset(
    ["get", "post", "head", "options", "delete", "put", "trace", "patch"]
)
class View:

    methods = None
    provide_automatic_options = None
    装饰器
    decorators = ()

    #重写分发请求
    def dispatch_request(self):
        raise NotImplementedError()

    @classmethod
    def as_view(cls, name, *class_args, **class_kwargs):

        def view(*args, **kwargs):
            self = view.view_class(*class_args, **class_kwargs)
            return self.dispatch_request(*args, **kwargs)

        if cls.decorators:
            view.__name__ = name
            view.__module__ = cls.__module__
            for decorator in cls.decorators:
                view = decorator(view)

        view.view_class = cls
        view.__name__ = name
        view.__doc__ = cls.__doc__
        view.__module__ = cls.__module__
        view.methods = cls.methods
        view.provide_automatic_options = cls.provide_automatic_options
        return view
</pre>

<font size=3 color="#333" > __View装饰器__ </font> 
<pre>
from flask import session

定义装饰器
def login_required(func):
    def wrapper(*args,**kwargs):
        if not session.get("user_id"):
            return 'auth failure'
        return func(*args,**kwargs)
    return wrapper

class UserView(views.MethodView):
    decorators = [user_required]
...
</pre>
<font size=3 color="#333" > __MethodView__ </font> 
<pre>
class MethodViewType(type):
    def __init__(cls, name, bases, d):
        super().__init__(name, bases, d)

        if "methods" not in d:
            methods = set()

            for base in bases:
                if getattr(base, "methods", None):
                    methods.update(base.methods)

            for key in http_method_funcs:
                if hasattr(cls, key):
                    methods.add(key.upper())

            if methods:
                cls.methods = methods

class MethodView(View, metaclass=MethodViewType):
    def dispatch_request(self, *args, **kwargs):
        meth = getattr(self, request.method.lower(), None)
        if meth is None and request.method == "HEAD":
            meth = getattr(self, "get", None)

        assert meth is not None, f"Unimplemented method {request.method!r}"
        return meth(*args, **kwargs)
</pre>
<font size=3 color="#333" > __MethodView 的技术__ </font> 
<pre>
class CounterAPI(MethodView):
    def get(self):
        return session.get('counter', 0)
    def post(self):
        session['counter'] = session.get('counter', 0) + 1
        return 'OK'
app.add_url_rule('/counter', view_func=CounterAPI.as_view('counter'))

</pre>