##  Thread
----

<font size=5 color="#797979">

__*Invest your energy into something that is important.*__

</font>

<font size=2 color="#333">

线程（Thread）是操作系统能够进行运算调度的最小单位，它被包涵在进程之中，是进程中的实际运作单位。.

</font>

----
<font size=4 color="#333"> 

__Thread__  
</font>

<font size=3 color="#333"> 

__threading模块__  
</font>
<pre>
import threading

def go(t):
    print(t)

if __name__ == '__main__':
    t1 = threading.Thread(target=go,args = (5,))
    t1.start()
</pre>
<font size=3 color="#333"> 

__自定义线程__  
</font>
<pre>
import threading
import time

def run(n):
    print("task", n)
    time.sleep(1)       #此时子线程停1s
    print('3')
    time.sleep(1)
    print('2')
    time.sleep(1)
    print('1')

if __name__ == '__main__':
    t = threading.Thread(target=run, args=("t1",))
    t.setDaemon(True)   #把子进程设置为守护线程，必须在start()之前设置
    t.start()
    print("end")
</pre>

<font size=3 color="#333"> 

__setDaemon__  
</font>
<font size=3 color="#333"> 

当主进程结束后，子线程也会随之结束. 
t.setDaemon(True)  把子进程设置为守护线程，必须在start()之前设置
</font>

<font size=3 color="#333"> 

__join__  
</font>
<font size=3 color="#333"> 

为了让守护线程执行结束之后，主线程再结束，我们可以使用join方法，让主线程等待子线程执行
</font>

<font size=3 color="#333"> 

__多线程共享全局变量__  
</font>
<font size=3 color="#333"> 

global num
</font>

<font size=3 color="#333"> 

__互斥锁__  
</font>
<font size=3 color="#333"> 

线程锁同一时刻允许一个线程执行操作
</font>
<pre>
from threading import Thread,Lock
import os,time
def work():
    global n
    # 锁使用
    lock.acquire()
    temp=n
    time.sleep(0.1)
    n=temp-1
    # 锁释放
    lock.release()
if __name__ == '__main__':
    # 定义锁
    lock=Lock()
    n=100
    l=[]
    for i in range(100):
        p=Thread(target=work)
        l.append(p)
        p.start()
    for p in l:
        p.join()
</pre>

<font size=3 color="#333"> 

__信号量（BoundedSemaphore类)__  
</font>
<font size=3 color="#333"> 

Semaphore是同时允许一定数量的线程更改数据
</font>
<pre>
import threading
import time

def run(n, semaphore):
    semaphore.acquire()   #加锁
    time.sleep(1)
    print("run the thread:%s\n" % n)
    semaphore.release()     #释放

if __name__ == '__main__':
    num = 0
    # 最多允许5个线程同时运行
    semaphore = threading.BoundedSemaphore(5)  
    
    for i in range(22):
        t = threading.Thread(target=run, args=("t-%s" % i, semaphore))
        t.start()
</pre>

<font size=3 color="#333"> 

__Event__  
</font>
<font size=3 color="#333"> 

clear 将flag设置为“False”
set 将flag设置为“True”
is_set 判断是否设置了flag
wait 会一直监听flag，如果没有检测到flag就一直处于阻塞状态
事件处理的机制：全局定义了一个“Flag”，当flag值为“False”，那么event.wait()就会阻塞，当flag值为“True”，那么event.wait()便不再阻塞
</font>
<pre>
import threading
import time

event = threading.Event()


def lighter():
    count = 0
    event.set()     #初始值为绿灯
    while True:
        if 5 < count <=10 :
            event.clear()  # 红灯，清除标志位
            print("\33[41;1mred light is on...\033[0m")
        elif count > 10:
            event.set()  # 绿灯，设置标志位
            count = 0
        else:
            print("\33[42;1mgreen light is on...\033[0m")

        time.sleep(1)
        count += 1

def car(name):
    while True:
        if event.is_set():      #判断是否设置了标志位
            print("[%s] running..."%name)
            time.sleep(1)
        else:
            print("[%s] sees red light,waiting..."%name)
            event.wait()
            print("[%s] green light is on,start going..."%name)

light = threading.Thread(target=lighter,)
light.start()

car = threading.Thread(target=car,args=("MINI",))
car.start()
</pre>

<font size=3 color="#333"> 

__Condition__  
</font>
<font size=3 color="#333"> 

acquire(): 线程锁
release(): 释放锁
wait(timeout): 线程挂起
notify(n=1): 通知其他线程
notifyAll(): 通知所有线程

</font>

<pre>
import threading
import time

con = threading.Condition()

num = 0

# 生产者
class Producer(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        # 锁定线程
        global num
        con.acquire()
        while True:
            print "开始添加！！！"
            num += 1
            print "火锅里面鱼丸个数：%s" % str(num)
            time.sleep(1)
            if num >= 5:
                print "火锅里面里面鱼丸数量已经到达5个，无法添加了！"
                # 唤醒等待的线程
                con.notify()  # 唤醒小伙伴开吃啦
                # 等待通知
                con.wait()
        # 释放锁
        con.release()

# 消费者
class Consumers(threading.Thread):
    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        con.acquire()
        global num
        while True:
            print "开始吃啦！！！"
            num -= 1
            print "火锅里面剩余鱼丸数量：%s" %str(num)
            time.sleep(2)
            if num <= 0:
                print "锅底没货了，赶紧加鱼丸吧！"
                con.notify()  # 唤醒其它线程
                # 等待通知
                con.wait()
        con.release()

p = Producer()
c = Consumers()
p.start()
c.start()
</pre>
