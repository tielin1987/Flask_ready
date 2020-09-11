## Local，Localstack，Localproxy
  ----
<font size=5 color="#797979">

__*Sparse is better than dense*.__
</font>

<font size=2 color="#333"> 
富有层次，代码结构疏密相间。</font> 

----
<font size=4 color="#333">

__Local，Localstack，Localproxy__

</font>

<font size=3 color="#888">

Local对象获取greenlet id(或者线程id)，通过name key就可以获取到真正的存储的对象。LocalStack数据结构是栈的形式，全局存储Local对象，先进后出。Localproxy通过获取当前对象，实现动态绑定。

</font>
<font size=2  color="#191970">
<b> Tag : 
Local LocalStack Localproxy</b></font>

----
<font size=4 color="#333"> 

__Local__
 </font> 
<font size=3 color="#333">使用线程局部变量,隔离多个线程冲突 </font> 

<pre>
try:
    from greenlet import getcurrent as get_ident
except ImportError:
    try:
        from thread import get_ident
    except ImportError:
        from _thread import get_ident

class Local(object):

    #指定对象属性，保证安全
    __slots__ = ('__storage__', '__ident_func__')

    #属性初始化
    def __init__(self):
        object.__setattr__(self, '__storage__', {})
        object.__setattr__(self, '__ident_func__', get_ident)

    def __iter__(self):
        return iter(self.__storage__.items())

    #当调用Local对象时，返回对应的LocalProxy
    def __call__(self, proxy):
        return LocalProxy(self, proxy)

    #释放local数据的内存
    def __release_local__(self):
        self.__storage__.pop(self.__ident_func__(), None)

    #获取到真正的存储的对象。
    def __getattr__(self, name):
        try:
            return self.__storage__[self.__ident_func__()][name]
        except KeyError:
            raise AttributeError(name)

    def __setattr__(self, name, value):
        ident = self.__ident_func__()
        storage = self.__storage__
        try:
            storage[ident][name] = value
        except KeyError:
            storage[ident] = {name: value}

    def __delattr__(self, name):
        try:
            del self.__storage__[self.__ident_func__()][name]
        except KeyError:
            raise AttributeError(name)
</pre>
<font size=4 color="#333"> 

 __LocalStack__
 </font> 
<font size=3 color="#333">先进后出</font> 
<pre>
class LocalStack:

    #_local初始化local对象
    def __init__(self) -> None:
        self._local = Local()

    #释放local对象
    def __release_local__(self) -> None:
        self._local.__release_local__()
    
    #线程id
    @property
    def __ident_func__(self):
        return self._local.__ident_func__

    @__ident_func__.setter
    def __ident_func__(self, value):
        object.__setattr__(self._local, "__ident_func__", value)

    def __call__(self):
        def _lookup():
            rv = self.top
            if rv is None:
                raise RuntimeError("object unbound")
            return rv

        return LocalProxy(_lookup)

    #对象压入
    def push(self, obj: Any):
        rv = getattr(self._local, "stack", None)
        if rv is None:
            self._local.stack = rv = []  # type: ignore
        rv.append(obj)
        return rv

    #对象弹出
    def pop(self):
        stack = getattr(self._local, "stack", None)
        if stack is None:
            return None
        elif len(stack) == 1:
            release_local(self._local)
            return stack[-1]
        else:
            return stack.pop()

    @property
    def top(self):
        try:
            return self._local.stack[-1]
        except (AttributeError, IndexError):
            return None
</pre>
<font size=4 color="#333"> 

  __LocalManager__
 </font> 
<font size=3 color="#333">Local对象执行后销毁</font> 
<pre>
class LocalManager:

    #类型检查及初始化
    def __init__(
        self,
        locals: Optional[List[Union[Local, LocalStack]]] = None,
        ident_func: Optional[Callable] = None,
    ):
        if locals is None:
            self.locals = []
        elif isinstance(locals, Local):
            self.locals = [locals]
        else:
            self.locals = list(locals)  # type: ignore
        if ident_func is not None:
            self.ident_func = ident_func
            for local in self.locals:
                object.__setattr__(local, "__ident_func__", ident_func)
        else:
            self.ident_func = get_ident

    #线程ID
    def get_ident(self) -> Any:
        return self.ident_func()

    #清除线程
    def cleanup(self):
        for local in self.locals:
            release_local(local)

    #request响应后销毁requestcontext 及 appcontext
    def make_middleware(self, app):  
        def application(environ, start_response):
            return ClosingIterator(app(environ, start_response), self.cleanup)
        return application

    #装饰器函数
    def middleware(self, func):
        return update_wrapper(self.make_middleware(func), func)
</pre>
<font size=4 color="#333"> 

__LocalProxy__ 
 </font> 
<font size=3 color="#333">获取当前对象及属性值</font>
<pre>
class LocalProxy:
    
    __slots__ = ("__local", "__dict__", "__name__", "__wrapped__")

    def __init__(
        self, local: Union[Any, "LocalProxy", "LocalStack"], name: Optional[str] = None,
    ):
        object.__setattr__(self, "_LocalProxy__local", local)
        object.__setattr__(self, "__name__", name)
        if callable(local) and not hasattr(local, "__release_local__"):
            object.__setattr__(self, "__wrapped__", local)
    
    #获取当前对象
    def _get_current_object(self,):

        if not hasattr(self.__local, "__release_local__"):
            return self.__local()
        try:
            return getattr(self.__local, self.__name__)
        except AttributeError:
            raise RuntimeError(f"no object bound to {self.__name__}")
    
    #重写localproxy的魔法方法
    @property
    def __dict__(self):
        try:
            return self._get_current_object().__dict__
        except RuntimeError:
            raise AttributeError("__dict__")

    def __repr__(self) -> str:
        try:
            obj = self._get_current_object()
        except RuntimeError:
            return f"<{type(self).__name__} unbound>"
        return repr(obj)

    def __bool__(self) -> bool:
        try:
            return bool(self._get_current_object())
        except RuntimeError:
            return False

    def __dir__(self):
        try:
            return dir(self._get_current_object())
        except RuntimeError:
            return []

    def __getattr__(self, name: str):
        if name == "__members__":
            return dir(self._get_current_object())
        return getattr(self._get_current_object(), name)

    def __setitem__(self, key: Any, value: Any)
        self._get_current_object()[key] = value

    def __delitem__(self, key):
        del self._get_current_object()[key]
</pre>