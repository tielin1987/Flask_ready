## Session and Cookie
----
<font size=5 color="#797979">

__*Namespaces are one honking great idea -- let's do more of those!*.__
</font>

<font size=4 color="#888"> __Session and Cookie__</font>

+ Web会话跟踪技术是Cookie与Session
+ Cookie通过在客户端记录信息确定用户身份
+ Session通过在服务器端记录信息确定用户身份

----
<font size=4 color="#888"> __Cookie__</font>

<pre>

#获取cookie
@app.route('/get_cookie')
def get_cookie():
      name = request.cookies.get('passwd')
      return  name
 
#删除cookie
@app.route('/del_cookie')
def del_cookie():
      resp = make_response('delete_cookie')
      resp.delete_cookie('passwd')
      return resp 

#设置cookie
@app.route('/set_cookie')
def set_cookie():
      resp = make_response('set_cookie')
      resp.set_cookie('passwd', '123456')
      return resp
</pre>

<font size=4 color="#888"> __Session__</font>
Session是通过检查服务器上的“客户明细表”来确认客户身份
<pre>

#获取cookie
@app.route('/get_cookie')
def get_cookie():
      name = request.cookies.get('passwd')
      return  name
 
#删除cookie
@app.route('/del_cookie')
def del_cookie():
      resp = make_response('delete_cookie')
      resp.delete_cookie('passwd')
      return resp 

#设置cookie
@app.route('/set_cookie')
def set_cookie():
      resp = make_response('set_cookie')
      resp.set_cookie('passwd', '123456')
      return resp
</pre>
