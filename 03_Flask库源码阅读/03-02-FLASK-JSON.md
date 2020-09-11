##  JSON
----

<font size=5 color="#797979">

__*The shortest answer is doing.*__

</font>

<font size=2 color="#333">

就是干

</font>

----

<font size=4 color="#333"> 

__JSON__ </font> 

<font size=3 color="#333"> 

编码和解码放在最外围来做, [Effective Python]. JSON 模块讲解数据序列化; JSON.Tag 对 SecureCookieSession 序列化. JSON 模块主要方法: dump,dumps,load,loads,jsonfy.

+ json.dumps()函数是将一个Python数据类型列表进行json格式, 返回类型为Content-Type:text/html
+ json.loads()函数是将json格式数据转换为字典
+ json.dump()和json.load()主要用来读写json文件函数
+ jsonify(): 字典转成json字符串, 返回到前台界面时的相应类型为Content-Type: application/json

</font>

<font size=2  color="#191970">
<b> Tag : dumps loads dump load jsonify
</b></font>

-----

<font size=4 color="#333"> 

__JSONTag__  
</font>

<font size=3 color="#333"> 
代码采用工厂+模板方法组织, 由 TaggedJSONSerializer 数据检查及操作.(小技巧 类变量Key当做Flag)

+ 工厂类:JSONTag
+ 子类：dict tuple bytes Markup uuid datetime
+ 系统处理类：TaggedJSONSerializer
</font>  

<font size=2  color="#191970">
<b> Tag : JSONTag TaggedJSONSerializer
</b></font>

-----
<font size=3 color="#333"> 

__工厂类 JSONTag__  
</font>
<pre>

class JSONTag:
    __slots__ = ("serializer",)
    key = None

    #数据初始化
    def __init__(self, serializer):
        self.serializer = serializer

    #数据检查
    def check(self, value):
        raise NotImplementedError

    #目的Target
    def to_json(self, value):
        raise NotImplementedError
    def to_python(self, value):
        raise NotImplementedError

    #模板方法
    def tag(self, value):
        return {self.key: self.to_json(value)}
</pre>

<font size=3 color="#333"> 

__子类 JSONTag__  
</font>
<pre>

.......
class TagDict(JSONTag):
    __slots__ = ()
    key = " di"
    def check(self, value):
        return (
            isinstance(value, dict)
            and len(value) == 1
            and next(iter(value)) in self.serializer.tags
        )
    def to_json(self, value):
        key = next(iter(value))
        return {f"{key}__": self.serializer.tag(value[key])}
    def to_python(self, value):
        key = next(iter(value))
        return {key[:-2]: value[key]}
.......

</pre>

<font size=3 color="#333"> 

__系统类 TaggedJSONSerializer__  
</font>
<pre>

class TaggedJSONSerializer:
    __slots__ = ("tags", "order")

    #注册子类对象
    default_tags = [
        TagDict,
        PassDict,
        TagTuple,
        PassList,
        TagBytes,
        TagMarkup,
        TagUUID,
        TagDateTime,
    ]

    #数据初始化
    def __init__(self):
        self.tags = {}
        self.order = []
        for cls in self.default_tags:
            self.register(cls)

    #子类注册
    def register(self, tag_class, force=False, index=None):
        #子类对象及key
        tag = tag_class(self)
        key = tag.key

        if key is not None:
            if not force and key in self.tags:
                raise KeyError(f"Tag '{key}' is already registered.")
            self.tags[key] = tag

        if index is None:
            self.order.append(tag)
        else:
            self.order.insert(index, tag)

    #类型判断
    def tag(self, value):
        for tag in self.order:
            if tag.check(value):
                return tag.tag(value)
        return value
    def untag(self, value):
        if len(value) != 1:
            return value
        key = next(iter(value))
        if key not in self.tags:
            return value
        return self.tags[key].to_python(value[key])
        
    #JSON字符串互换操作
    def dumps(self, value):
        return dumps(self.tag(value), separators=(",", ":"))
    def loads(self, value):
        return loads(value, object_hook=self.untag)
</pre>