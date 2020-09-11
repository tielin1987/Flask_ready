## APP
----

<font size=5 color="#797979">

__*Although that way may not be obvious at first unless you're Dutch*.__
</font>




----

<font size=4 color="#888"> __Jinja2简介:__ 模板引擎，负责HTML文件渲染.</font> 


<font size=4 color="#888"> __Jinja2流程:__ </font>

```mermaid
   graph LR
    A(Jinja2)--FileSystemLoader-->B(Loader)
    B--Environment-->C(Environment)
    C--get_template-->D(加载模板)
    D--render-->E(渲染模板)
    style A stroke:#666,stroke-width:2px
    style B stroke:#666,stroke-width:2px
    style C stroke:#666,stroke-width:2px
    style D stroke:#666,stroke-width:2px
    style E stroke:#666,stroke-width:2px
```
 __注:__ 
 + FileSystemLoader:加载当前工作目录下的templates目录下的模板文件

+ EnvironmentP:存储配置信息, 全局对象, 从文件系统加载模板.

+ get_template():加载模板.

+ render():渲染模板.




