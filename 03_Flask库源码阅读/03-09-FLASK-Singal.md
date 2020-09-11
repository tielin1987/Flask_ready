## Session
----

<font size=5 color="#797979">

__*Although practicality beats purity*.__
</font>


----

<font size=4 color="#333" > __signals__ </font> 
<font size=3 color="#333" > 信号函数 设计订阅模式</font> 

<pre>
from flask import Flask,flash
from flask.signals import _signals
app = Flask(__name__)

xinhao = _signals.signal("xinhao")#创建信号
#定义函数
def wahaha(*args,**kwargs):
    print("111",args,kwargs)

def sww(*args,**kwargs):
    print("222",args,kwargs)
# 将函数注册到信号中,添加到这个列表
xinhao.connect(wahaha)
xinhao.connect(sww)

@app.route("/zzz")
def zzz():
    xinhao.send(sender='xxx',a1=123,a2=456)  #触发这个信号，执行注册到这个信号列表中的所有函数,此处的参数个数需要与定义的函数中的参数一致
    return "发送信号成功"

if __name__ == '__main__':
    app.run(debug=True)
</pre>

