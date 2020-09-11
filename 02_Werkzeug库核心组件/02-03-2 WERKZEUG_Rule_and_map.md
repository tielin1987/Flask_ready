##  Rule ,  Map , MapAdapter
----

<font size=5 color="#797979">

__*Beautiful is better than ugly.*__

</font>

<font size=2 color="#333">
一段优美清晰的代码
</font>

----

<font size=4 color="#333">

__Rule ,  Map , MapAdapter__

</font>

<font size=3 color="#333">

Rule类用于存储单个路由规则，定义了url和endpoint的映射，同时指定请求方法.
Map类存储了所有路由规则和全局配置.
MapAdapter分发请求并调用相应的视图函数.

</font>

<font size=2  color="#191970">
<b> Tag : Rule Map MapAdapter</b></font>

____

<font size=4 color="#333">

__Rule__ 
</font> 


+ empty() ——将Rule实例和Map实例解除绑定.

+ get_empty_kwargs() ——获得之前Rule实例的参数.

+ get_rules(map) ——返回Rule实例.

+ refresh() ——更新Rule实例和Map实例的绑定关系.

+ bind(map, rebind=False) ——将Rule实例和Map实例进行绑定.

+ complie()——根据Rule实例的URL模式，生成一个正则表达式，以便后续对请求的path进行匹配.

+ match(path) ——将Rule实例和给定的path进行匹配.

<pre>
class Rule():

def __init__(self, string, defaults=None, subdomain=None, methods=None,
    build_only=False, endpoint=None, strict_slashes=None,
             redirect_to=None, alias=False, host=None):
    if not string.startswith('/'):
        raise ValueError('urls must start with a leading slash')
    self.rule = string
    self.is_leaf = not string.endswith('/')
    self.map=None　　　　
    self.strict_slashes = strict_slashes                                                                                                                                    
def empty(self):
	return type(self)(self.rule, **self.get_empty_kwargs()

#生成器，调用后返回自身，submain首先获取**rule**,接着获取生成器
def get_rules(self, map):
	yield self   

def refresh(self):
	self.bind(self.map, rebind=True)

#bind方法将rule绑定到self.map上面，那么rule就可以使用map实例的属性
def bind(self, map, rebind=False):
	if self.map is not None and not rebind:
		raise RuntimeError('url rule %r already bound to map %r' %
						   (self, self.map))
	self.map = map                                      
	if self.strict_slashes is None:
		self.strict_slashes = map.strict_slashes
	if self.subdomain is None:
		self.subdomain = map.default_subdomain
	self.compile()

#调用self.map的的方法获得url转换的方法   
def get_converter(self, variable_name, converter_name, args, kwargs):
	if converter_name not in self.map.converters:　　　　　　　　　　　　　　　　　　　　　
		raise LookupError('the converter %r does not exist' % converter_name)
	return self.map.converters[converter_name](self.map, *args, **kwargs)       

</pre>
<font size=4 color="#333">  

__Map__ </font> 

+ add(rulefactory) ——构造Map实例的时候就会调用，将Map类中的Rule实例和该Map实例建立绑定关系.

+ bind方法 ——这个方法会生成MapAdapter实例，传入MapAdapter的包括一些请求信息，这样可以调用MapAdapter实例的方法匹配给定URL.

+ bind_to_environ方法 ——通过解析请求中的environ信息，生成MapAdapter实例.

<pre>
class Map():
def add(self, rulefactory):
    for rule in rulefactory.get_rules(self):
        rule.bind(self)
        self._rules.append(rule)
        self._rules_by_endpoint.setdefault(rule.endpoint, []).append(rule)
    self._remap = True

def bind(
        self,
        server_name: str,
        script_name: Optional[str] = None,
        subdomain: Optional[str] = None,
        url_scheme: str = "http",
        default_method: str = "GET",
        path_info: Optional[str] = None,
        query_args: Optional[str] = None,
    ) :
        server_name = server_name.lower()
        if self.host_matching:
            if subdomain is not None:
                raise RuntimeError("host matching enabled and a subdomain was provided")
        elif subdomain is None:
            subdomain = self.default_subdomain
        if script_name is None:
            script_name = "/"
        if path_info is None:
            path_info = "/"
        try:
            server_name = _encode_idna(server_name)  # type: ignore
        except UnicodeError:
            raise BadHost()
        return MapAdapter(
            self,
            server_name,
            script_name,
            subdomain,
            url_scheme,
            path_info,
            default_method,
            query_args,
        )

def bind_to_environ(
        self,
        environ: WSGIEnvironment,
        server_name: Optional[str] = None,
        subdomain: Optional[str] = None,
    ):
        environ = _get_environ(environ)
        wsgi_server_name = get_host(environ).lower()
        scheme = environ["wsgi.url_scheme"]

        if server_name is None:
            server_name = wsgi_server_name
        else:
            server_name = server_name.lower()

            if scheme == "http" and server_name.endswith(":80"):
                server_name = server_name[:-3]
            elif scheme == "https" and server_name.endswith(":443"):
                server_name = server_name[:-4]

        if subdomain is None and not self.host_matching:
            cur_server_name = wsgi_server_name.split(".")
            real_server_name = server_name.split(".")
            offset = -len(real_server_name)

            if cur_server_name[offset:] != real_server_name:
                warnings.warn(
                    f"Current server name {wsgi_server_name!r} doesn't match configured"
                    f" server name {server_name!r}",
                    stacklevel=2,
                )
                subdomain = "<invalid>"
            else:
                subdomain = ".".join(filter(None, cur_server_name[:offset]))

</pre>

<font size=5> __MapAdapter__ </font>

+ dispatch ——根据断点，返回对应的视图函数
+ match ——根据请求，返回断点及参数
<pre>
class MapAdapter:

    def __init__(
        self,
        map: Map,
        server_name: Union[str, bytes],
        script_name: str,
        subdomain: Optional[str],
        url_scheme: str,
        path_info: str,
        default_method: str,
        query_args: Optional[str] = None,
    ) :
        self.map = map
        self.server_name = _to_str(server_name)
        script_name = _to_str(script_name)
        if not script_name.endswith("/"):
            script_name += "/"
        self.script_name = script_name
        self.subdomain = _to_str(subdomain)
        self.url_scheme = _to_str(url_scheme)
        self.path_info = _to_str(path_info)
        self.default_method = _to_str(default_method)
        self.query_args = query_args
        self.websocket = self.url_scheme in {"ws", "wss"}

    def dispatch(
        self,
        view_func: Callable,
        path_info: Optional[str] = None,
        method: Optional[str] = None,
        catch_http_exceptions: bool = False,
    ):

    # 返回视图函数,
        try:
            try:
                endpoint, args = self.match(path_info, method)
            except RequestRedirect as e:
                return e
            return view_func(endpoint, args)
        except HTTPException as e:
            if catch_http_exceptions:
                return e
            raise

    def match(
        self,
        path_info: Optional[str] = None,
        method: Optional[str] = None,
        return_rule: bool = False,
        query_args: Optional[Union[str, Dict[str, str]]] = None,
        websocket: Optional[bool] = None,
    ):
        self.map.update()
        if path_info is None:
            path_info = self.path_info
        else:
            path_info = _to_str(path_info, self.map.charset)
        if query_args is None:
            query_args = self.query_args
        method = (method or self.default_method).upper()

        if websocket is None:
            websocket = self.websocket

        require_redirect = False

        domain_part = self.server_name if self.map.host_matching else self.subdomain
        path_part = f"/{path_info.lstrip('/')}" if path_info else ""
        path = f"{domain_part}|{path_part}"

        have_match_for = set()
        websocket_mismatch = False

        for rule in self.map._rules:
            try:
                rv = rule.match(path, method)
            except RequestPath as e:
                raise RequestRedirect(
                    self.make_redirect_url(
                        url_quote(e.path_info, self.map.charset, safe="/:|+"),
                        query_args,
                    )
                )
            except RequestAliasRedirect as e:
                raise RequestRedirect(
                    self.make_alias_redirect_url(
                        path,
                        rule.endpoint,
                        e.matched_values,
                        method,
                        query_args,  # type: ignore
                    )
                )
            if rv is None:
                continue
            if rule.methods is not None and method not in rule.methods:
                have_match_for.update(rule.methods)
                continue

            if rule.websocket != websocket:
                websocket_mismatch = True
                continue

            if self.map.redirect_defaults:
                redirect_url = self.get_default_redirect(
                    rule, method, rv, query_args  # type: ignore
                )
                if redirect_url is not None:
                    raise RequestRedirect(redirect_url)

            if rule.redirect_to is not None:
                if isinstance(rule.redirect_to, str):

                    def _handle_match(match):
                        value = rv[match.group(1)]
                        return rule._converters[match.group(1)].to_url(value)

                    redirect_url = _simple_rule_re.sub(_handle_match, rule.redirect_to)
                else:
                    redirect_url = rule.redirect_to(self, **rv)

                if self.subdomain:
                    netloc = f"{self.subdomain}.{self.server_name}"
                else:
                    netloc = self.server_name

                raise RequestRedirect(
                    url_join(
                        f"{self.url_scheme or 'http'}://{netloc}{self.script_name}",
                        redirect_url,
                    )
                )

            if require_redirect:
                raise RequestRedirect(
                    self.make_redirect_url(
                        url_quote(path_info, self.map.charset, safe="/:|+"), query_args,
                    )
                )

            if return_rule:
                return rule, rv
            else:
                return rule.endpoint, rv

        if have_match_for:
            raise MethodNotAllowed(valid_methods=list(have_match_for))

        if websocket_mismatch:
            raise WebsocketMismatch()

        raise NotFound()
</pre>
