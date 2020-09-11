## Config
----

<font size=5 color="#333">

__*If the implementation is easy to explain, it may be a good idea*.__

</font>

----

<font size=4 color="#333"> 

__Config__ </font> 

<font size=3 color="#333">

Config 对象从不同数据源读取文件,ConfigAttribute 是 Config 动态属性.
</font>

<font size=2  color="#191970">
<b> 
Tag : from_envvarloads from_pyfile from_object from_file get_namespace
</b>
</font>

------
<font size=3>

 __ConfigAttribute:__ 
 </font> 

<pre>
   class ConfigAttribute:

    def __init__(self, name, get_converter=None):
        self.__name__ = name
        self.get_converter = get_converter

    def __get__(self, obj, type=None):
        if obj is None:
            return self
        rv = obj.config[self.__name__]
        if self.get_converter is not None:
            rv = self.get_converter(rv)
        return rv

    def __set__(self, obj, value):
        obj.config[self.__name__] = value
</pre>

<font size=3> __Config:__ </font> 
<pre>
class Config(dict):
    def __init__(self, root_path, defaults=None):
        dict.__init__(self, defaults or {})
        self.root_path = root_path
    
    #从环境文件进行加载 [On Linux and OS X]
    def from_envvar(self, variable_name, silent=False):

        rv = os.environ.get(variable_name)
        if not rv:
            if silent:
                return False
            raise RuntimeError(
                f"The environment variable {variable_name!r} is not set"
                " and as such configuration could not be loaded. Set"
                " this variable and make it point to a configuration"
                " file"
            )
        return self.from_pyfile(rv, silent=silent)
    
    #从PY文件进行加载 
    def from_pyfile(self, filename, silent=False):

        filename = os.path.join(self.root_path, filename)
        d = types.ModuleType("config")
        d.__file__ = filename
        try:
            with open(filename, mode="rb") as config_file:
                exec(compile(config_file.read(), filename, "exec"), d.__dict__)
        except OSError as e:
            if silent and e.errno in (errno.ENOENT, errno.EISDIR, errno.ENOTDIR):
                return False
            e.strerror = f"Unable to load configuration file ({e.strerror})"
            raise
        self.from_object(d)
        return True

    #对象   常用
    def from_object(self, obj):

        if isinstance(obj, str):
            obj = import_string(obj)
        for key in dir(obj):
            if key.isupper():
                self[key] = getattr(obj, key)

    #app.config.from_file("config.toml", load=toml.load)
    def from_file(self, filename, load, silent=False):
        
        filename = os.path.join(self.root_path, filename)

        try:
            with open(filename) as f:
                obj = load(f)
        except OSError as e:
            if silent and e.errno in (errno.ENOENT, errno.EISDIR):
                return False

            e.strerror = f"Unable to load configuration file ({e.strerror})"
            raise

        return self.from_mapping(obj)

    def from_mapping(self, *mapping, **kwargs):
      
        mappings = []
        if len(mapping) == 1:
            if hasattr(mapping[0], "items"):
                mappings.append(mapping[0].items())
            else:
                mappings.append(mapping[0])
        elif len(mapping) > 1:
            raise TypeError(
                f"expected at most 1 positional argument, got {len(mapping)}"
            )
        mappings.append(kwargs.items())
        for mapping in mappings:
            for (key, value) in mapping:
                if key.isupper():
                    self[key] = value
        return True

    #字典嵌套映射
    def get_namespace(self, namespace, lowercase=True, trim_namespace=True):
       
        rv = {}
        for k, v in self.items():
            if not k.startswith(namespace):
                continue
            if trim_namespace:
                key = k[len(namespace) :]
            else:
                key = k
            if lowercase:
                key = key.lower()
            rv[key] = v
        return rv
</pre>