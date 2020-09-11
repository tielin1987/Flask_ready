##  Process
----

<font size=5 color="#797979">

__*Invest your energy into something that is important.*__

</font>

<font size=2 color="#333">
Python多进程包multiprocessing，支持子进程、通信和共享数据、执行不同形式的同步，提供了 Process
Lock Semaphore Event Queue Pipe Pool组件。
</font>

----
<font size=4 color="#333"> 

__Process__  
</font>

<font size=3 color="#333"> 

__Process__  
</font>
<pre>
import multiprocessing
import time

def worker(interval):
    n = 5
    while n > 0:
        print("The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1

if __name__ == "__main__":
    p = multiprocessing.Process(target = worker, args = (3,))
    p.start()
    print "p.pid:", p.pid
    print "p.name:", p.name
    print "p.is_alive:", p.is_alive()
</pre>
<font size=4 color="#333"> 

__自定义进程__  
</font>
<pre>
import multiprocessing
import time

class ClockProcess(multiprocessing.Process):
    def __init__(self, interval):
        multiprocessing.Process.__init__(self)
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print("the time is {0}".format(time.ctime()))
            time.sleep(self.interval)
            n -= 1

if __name__ == '__main__':
    p = ClockProcess(3)
    p.start() 
</pre>

<font size=4 color="#333"> 

__Lock__  
</font>
<font size=3 color="#333"> 

当多个进程需要访问共享资源的时候，Lock可以用来避免访问的冲突
</font>
<pre>
import multiprocessing
import sys

def worker_with(lock, f):
    with lock:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lockd acquired via with\n")
            n -= 1
        fs.close()
        
def worker_no_with(lock, f):
    lock.acquire()
    try:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lock acquired directly\n")
            n -= 1
        fs.close()
    finally:
        lock.release()
    
if __name__ == "__main__":
    lock = multiprocessing.Lock()
    f = "file.txt"
    w = multiprocessing.Process(target = worker_with, args=(lock, f))
    nw = multiprocessing.Process(target = worker_no_with, args=(lock, f))
    w.start()
    nw.start()
    print "end"

</pre>
<font size=4 color="#333"> 

__Semaphore__  
</font>
<font size=3 color="#333"> 

Semaphore用来控制对共享资源的访问数量，例如池的最大连接数。

</font>

<pre>
import time

def worker(s, i):
    s.acquire()
    print(multiprocessing.current_process().name + "acquire");
    time.sleep(i)
    print(multiprocessing.current_process().name + "release\n");
    s.release()

if __name__ == "__main__":
    s = multiprocessing.Semaphore(2)
    for i in range(5):
        p = multiprocessing.Process(target = worker, args=(s, i*2))
        p.start()

</pre>
<font size=4 color="#333"> 

__Event__  
</font>
<font size=3 color="#333"> 

1.  Event().wait()    插入在进程中插入一个标记（flag）  默认为 false  然后flag为false时  程序会停止运行  进入阻塞状态  

2.  Event().set()     使flag为Ture  然后程序会停止运行 进入运行状态  

3.  Event().clear()      使flag为false  然后程序会停止运行 进入阻塞状态  

4.  Event().is_set()   判断flag  是否为True  是的话 返回True  不是 返回false

</font>
<pre>
from multiprocessing import Event,Process
import time
from datetime import datetime
from psutil import cpu_count


def work(e,):
    # 子进程
    while True:
        #print('我是子进程,我先休息!')
        e.wait() #阻塞等待信号  这里插入了一个flag  默认为 false
        print('[S] 这是我的定时任务！',datetime.now().second)
        time.sleep(1)
      

#信号：
    #某一些用户链接的时候，Nginx就会做出相应
    #Epoll: 信号|事件

#21点51分
def main():
    e = Event()
    a = int()
    p = Process(target=work,name='子进程',args=(e,))
    p.start()
    while True:
        if datetime.now().second > 10:
            print('[F] 子进程取消阻塞状态')
            e.set() #这里 等秒钟大于10S  就开启子进程 工作 使插入的flag为 True  然后继续执行
            break
    while True:
        if datetime.now().second > 30:
            print('[F] 子进程结束阻塞状态')
            e.clear() #这里 等秒钟大于30S  就停止子进程工作  使插入的flag为false  就会停止子进程工作
            break
    time.sleep(5)#休息5s
    e.set()#继续使flag 为真 然后执行子进程
    print('[F] 又开启了')
    time.sleep(5)#休息5s
    e.clear()#继续使flag 为假 然后停止子进程
    print('[F] 又停止了')

if __name__ == '__main__':
    main()

</pre>


<font size=4 color="#333"> 

__Queue__  
</font>
<font size=3 color="#333"> 

Queue是多进程安全的队列，可以使用Queue实现多进程之间的数据传递。
put方法用以插入数据到队列中，put方法还有两个可选参数：blocked和timeout。如果blocked为True（默认值），并且timeout为正值，该方法会阻塞timeout指定的时间，直到该队列有剩余的空间。
 
get方法可以从队列读取并且删除一个元素。同样，get方法有两个可选参数：blocked和timeout。如果blocked为True（默认值），并且timeout为正值，那么在等待时间内没有取到任何元素，会抛出Queue.Empty异常。

</font>

<pre>
import multiprocessing

def writer_proc(q):      
    try:         
        q.put(1, block = False) 
    except:         
        pass   

def reader_proc(q):      
    try:         
        print q.get(block = False) 
    except:         
        pass

if __name__ == "__main__":
    q = multiprocessing.Queue()
    writer = multiprocessing.Process(target=writer_proc, args=(q,))  
    writer.start()   

    reader = multiprocessing.Process(target=reader_proc, args=(q,))  
    reader.start()  

    reader.join()  
    writer.join()
</pre>
