##  APP
----

<font size=5 color="#797979">

__*Invest your energy into something that is important.*__

</font>

<font size=2 color="#333">

APP 是 FLASK 框架运行核心，代码段长，因此分为 FLASK 属性及 RUN 两部分进行解读.

</font>

----
<font size=4 color="#333"> 

__FLASK 属性__  
</font>

<font size=3 color="#333"> 

FLASK 核心类属性由 Request Response Environment g Config Session Map Rule 对象组成. Flask 核心对象属性主要围绕类属性展开.
</font>

<pre>
    #请求CLS
    request_class = Request

    #响应CLS
    response_class = Response

    #环境CLS
    jinja_environment = Environment

    #全局对象 g
    app_ctx_globals_class = _AppCtxGlobals

    #配置
    config_class = Config

    #安全key
    secret_key = ConfigAttribute("SECRET_KEY")

    #cookiename
    session_cookie_name = ConfigAttribute("SESSION_COOKIE_NAME")

    #URL CLS
    url_rule_class = Rule

    #路由映射CLS
    url_map_class = Map

    #session接口
    session_interface = SecureCookieSessionInterface()

    #模板文档
    template_folder = None

    #文档路径
    root_path = None    
</pre>

<font size=3 color="#333"> 

FLASK 对象属性.
</font>
<pre>
     
        #config属性 从文件中读取
        self.config = self.make_config(instance_relative_config)

        #url_for error 
        self.url_build_error_handlers = []

        #第一次请求发起
        self.before_first_request_funcs = []

        #应用完成后
        self.teardown_appcontext_funcs = []


        #蓝图
        self.blueprints = {}
        self._blueprint_order = []

        #应用扩展
        self.extensions = {}

        #url转换器
        self.url_map = self.url_map_class()
        self.url_map.host_matching = host_matching
        self.subdomain_matching = subdomain_matching

        self._got_first_request = False
        self._before_request_lock = Lock()
</pre>