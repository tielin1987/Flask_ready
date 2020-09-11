## 文件操作
----
<font size=5 color="#797979">

__*Namespaces are one honking great idea -- let's do more of those!*.__
</font>

<font size=4 color="#888"> __File__</font> 

----

+ 获取文件属性
+ 创建目录
+ 文件名模式匹配
+ 遍历目录树
+ 创建临时文件和目录
+ 删除文件和目录
+ 复制、移动和重命名文件和目录
+ 创建和解压ZIP和TAR档案
+ 使用fileinput 模块打开多个文件

<font size=4> __文件数据的读和写__ </font> 
<pre>
with open('data.txt','r') as f:
    data = f.read()
with open('data.txt','w') as f:
    data = 'some datsa to be written'
    f.write(data)
</pre>

<font size=4> __目录列表__ </font> 

| 协议 |  作用 |     
| :-----| :---- |
| os.listdir() | 以列表的方式返回目录中所有的文件和文件夹 |
| os.scandir() | 返回一个迭代器包含目录中所有的对象，对象包含文件属性信息 | 
| pathlib.Path().iterdir() |  返回一个迭代器包含目录中所有的对象，对象包含文件属性信息 |
<pre>
import os
with os.scandir('my_directory') as entries:
    for entry in entries:
        print(entry.name)

import os
basepath = 'my_directory'
with os.scandir(basepath) as entries:
    for entry in entries:
        if entry.is_file():
            print(entry.name)
</pre>

<font size=4> __子目录列表__ </font> 
<pre>
from pathlib import Path

basepath = Path('my_directory')
for entry in basepath.iterdir():
    if entry.is_dir():
        print(entry.name)
</pre>

<font size=4> __创建目录__ </font> 
<pre>
import os
os.mkdir('example_directory')
</pre>

<font size=4> __遍历目录树__ </font> 
<pre>
import os 
image_path = '/home/yqw/Project/yt-image-classify/Picture/training20180501/dirty'
for root, dirs, files in os.walk(image_path):
    print("root:"+root)
    print(dirs)
    print(files)
</pre>

<font size=4> __删除文件__ </font> 
<pre>
import os

data_file = 'home/data.txt'
# 使用异常处理
try:
    os.remove(data_file)
except OSError as e:
    print(f'Error: {data_file} : {e.strerror}')
</pre>
