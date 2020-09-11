## Decorator,Gen,Iter
----

<font size=5 color="#797979">

__*If the implementation is easy to explain, it may be a good idea*.__
</font>

<font size=4 color="#888"> __Decorator:__ Fourth method</font> 

----

+ 无参数  newfunc(inner)  两层嵌套
+ 有参数  newfunc(inner)
+ 外层函数有参数  newfunc(paramer)(inner) 三层嵌套

<font size=4> __无参数:__ </font> 

<pre>
from functools import wraps
def new_func(a_func):
    @wraps(a_func)
    def func_desc():
        print("---start---")
        t = a_func()
        print("---end---")
        return t
    return func_desc

@new_func
def old_func():
    print("---middle---")
    return "aaa"
</pre>


<font size=4> __有参数:__ </font> 

<pre>
from functools import wraps

def news_func(f):
    @wraps(f)
    def descortor(*args,**kwargs):
        print("---start---")
        s = f(*args,**kwargs)
        print("---end---")
        return s
    return descortor
@news_func
def subkill(a,b):
    print("--middle--")
    return a+b
</pre>

<font size=4> __外层有参数:__ </font> 

<pre>
def outer(pramer):
    print("---start----")
    print(pramer)
    def decso(f):
        print("---middle----")
        print(pramer)
        def inner(*args,**kwargs):
            t = f(*args,**kwargs)
            return t
        return inner
    return decso

@outer(123)
def funct(a,b):
    print("---end---")
    return a+b

if __name__ == '__main__':
    t = funct(5,9)
    print(t)
</pre>

<font size=4> __class装饰器:__ </font> 

<pre>
from functools import wraps
 
class logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile
 
    def __call__(self, func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            with open(self.logfile, 'a') as opened_file:
                opened_file.write(log_string + '\n')
            self.notify()
            return func(*args, **kwargs)
        return wrapped_function
 
    def notify(self):
        pass
</pre>

<font size=4 color="#888"> __iteror:__ 可迭代对象只实现iter,迭代器实现了 iter 和 next</font> 

----

<font size=4> __迭代器__ </font> 

<pre>
class Abs():
    def __init__(self,name):
        self.name = name
        self.index = 0
    def __iter__(self):
        return iter(self.name)
    def __next__(self):
        self.index += 1
        if self.index <= len(self.name):
            return self.name[self.index]
        else:
            raise StopIteration("111")
if __name__ == '__main__':
    t = Abs("abcd")
    for i in t:
        print(i)
</pre>

----

<font size=4> __生成器__ 函数产生系列结果采用生成器</font> 

+ 协程 send close throw
+ 生成消费者模型

<font size=4> __协程__ </font> 
<pre>
def chat_boot():
    '''
    char boot
    '''
    response = ""
    while True:
        receive = yield response
        if "age" in receive:
            response = "god"
        elif "name" in receive:
            response = "I am a boot"
        else:
            response = "I dont care"

robot = chat_boot()
next(robot)

while True:
    send_data = input("Please input the word:")
    if send_data == 'q' or send_data =='quit':
        print("goodbye")
        break
    response = robot.send(send_data)
    print('[Robot>>:]',response)
</pre>

<font size=4> __生产消费者模型__ </font> 
<pre>
def productor(c):
    c.send(None)
    n = 0
    while n < 3:
        n += 1
        #生产者发送数据
        r = c.send(n)
        print('[PRODUCTOR] Productor %s...' % n)
        print(r)
def comsumer():
    r = ""
    while True:
    #消费者接受生产者数据
        n = yield r
        if not n:
            return 
        else:
            print('[CONSUMER] Consuming %s...' % n)
            r = '200 OK'
            mylist.append(n+100)

if __name__ == '__main__':
    mylist = []
    c = comsumer()
    productor(c)
    print(mylist)
</pre>
