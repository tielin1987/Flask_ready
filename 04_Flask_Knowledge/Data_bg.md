## Data struc
  ----
<font size=5 color="#797979">

__*Namespaces are one honking great idea -- let's do more of those!*.__
</font>

<font size=4 color="#888"> __DATA:__</font> <font size=4 >Set,List,Tuple and Dict</font>  

----
<font size=4> __Set__:</font><font size=4>
一种组合数据类型，支持in操作。[成员关系测试][文件去重]
| 协议 |  符号 |     
| :-----| :---- |
| __container__ | in |
| __iter__ | for | 
| __len__| size | 
| __s.add__ | + |
| __s.difference(t)__  | s-t | 
| __s.difference_update(t)__ | s-=t |
| __s.discard(x)__ | if x in s,discard(x) |
| __s.intersection(t)__  | s&t | 
| __s.union(t)__ | join  | 
| __s.update(t)__  | seq update set |
</font> 

<font size=4> __List__:</font>
<font size=4>可变序列结构。

| 协议 |  符号 |     
| :-----| :---- |
| __container__ | in |
| __iter__ | for | 
| __len__| size | 
| __getitem__| [] | 
| __setitem__| s[key] = value | 
| __s.append(x)__ | s尾部追加元素 |
| __s.count(x)__  | x在s中统计量 | 
| __s.index(x)__ | x在s中index |
| __s.pop()__ | 删除s最右边元素 |
| __s.remove(x)__  | 删除s最左边元素| 
| __s.reverse()__ | s翻转  | 
| __s.sort()__  |  排序  |
</font> 
<font size=2 color="222">
注：sort 与 sorted 区别：
(1)sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
(2)sort不保留原列表，sorted保留原列表。
</font> 

<font size=4> __Tuple__:</font>
<font size=4>不可变序列结构，不可删除和增加元素。
| 协议 |  符号 |     
| :-----| :---- |
| __container__ | in |
| __iter__ | for | 
| __len__| size | 
| __getitem__| [] | 
| __setitem__| s[key] = value | 
| __s.max()__ | max  | 
| __s.min()__  |  min  |
</font> 

<font size=4> __Dict__:</font>
<font size=4>一种映射结构，dict、defaultdict 和 OrderedDict.其中OrderedDict在添加键的时候会保持顺序；defaultdict当字典里的key不存在但被查找时，返回的不是keyError而是一个默认值，这个默认值是什么呢</font> 

<font size=4>__Dict创建方式__:
+ dict(a='a', b='b', t='t')     # 传入关键字
+ dict(zip(['one', 'two', 'three'], [1, 2, 3]))   # 映射函数方式来构造字典
+ dict([('one', 1), ('two', 2), ('three', 3)])    # 可迭代对象方式来构造字典
</font> <font size=4>

| 协议 |  符号 |     
| :-----| :---- |
| __container__ | in |
| __iter__ | for | 
| __len__| size | 
| __next__ | next(i) |
| __getitem__| [] | 
| __setitem__| s[key] = value | 
| __hash__| 哈希 | 
| __eq__| 比较哈希值 | 
| __s.clear__ | 清除所有键值对 | 
| ___s.copy__ | 浅复制 |
| __s.fromkeys（seq, val=None）__ | seq中的元素作为键创建字典，所有键的值都设为val|
| __s.get（key, default=None）__  | 读取字典中的键key，返回该键的值；如果找不到该键则返回default所设的值 | 
| __s.has_key（key）__ | 判断键key在字典中是否存在 | 
| __s.items(t)__  | 返回一个包含字典中（键，值）对元组的列表 |
| __s.keys()__  | 返回一个字典中所有键的列表 |
| __s.iteritems()__  | 返回对字典中所有（键，值）对的迭代器 |
| __s.iterkeys()__  | 返回对字典中所有键的迭代器 |
| __s.itervalues()__  | 返回对字典中所有值的迭代器 |
| __s.pop(key, default)__  | 返回一个包含字典中（键，值）对元组的列表 |
| __s.setdefault(key, default=None)__  | 设置字典中键key的值为default |
| __s.update(dict)__  | 合并字典 |
| __s.values()__  | 返回一个包含字典中所有值的列表 |
</font> 

<font size=4>__With__:上下文协议</font>
上下文管理器
| 协议 |  符号 |     
| :-----| :---- |
| __enter__ | open |
| __exit__ | close | 
<font size=4>__描述符协议__</font>
类属性
| 协议 |  符号 |     
| :-----| :---- |
| __get__ | open |
| __set__ | close | 
| __del__ | close | 