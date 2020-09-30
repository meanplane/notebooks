## 1 数据模型

### 1.1 特殊方法

+  __ init __
+  __ len __
+ __ getitem __   
+  __ repr __
+  __ abs __
+  __ bool __
+  __ add __
+  __ mul __



**跟运算符无关的特殊方法**

| 类型                      | 方法名                                                       |
| ------------------------- | ------------------------------------------------------------ |
| 字符串 / 字节序列表示形式 | `__repr__`、`__str__`、`__format__`、`__bytes__`             |
| 数值转换                  | `__abs__`、`__bool__`、`__complex__`、`__int__`、`__float__`、`__hash__`、`__index__` |
| 集合模拟                  | `__len__`、`__getitem__`、`__setitem__`、`__delitem__`、`__contains__` |
| 迭代枚举                  | `__iter__`、`__reversed__`、`__next__`                       |
| 可调用模拟                | `__call__`                                                   |
| 上下文管理                | `__enter__`、`__exit__`                                      |
| 实例创建和销毁            | `__new__`、`__init__`、`__del__`                             |
| 属性管理                  | `__getattr__`、`__getattribute__`、`__setattr__`、`__delattr__`、`__dir__` |
| 属性描述符                | `__get__`、`__set__`、`__delete__`                           |
| 跟类相关的服务            | `__prepare__`、`__instancecheck__`、`__subclasscheck__`      |



**跟运算符相关的特殊方法**

| 类型               | 方法名                                                       |
| ------------------ | ------------------------------------------------------------ |
| 一元运算符         | `__neg__ -`、`__pos__ +`、`__abs__ abs()`                    |
| 众多比较运算符     | `__lt__ <`、`__le__ <=`、`__eq__ ==`、`__ne__ !=`、`__gt__ >`、`__ge__ >=` |
| 算术运算符         | `__add__ +`、`__sub__ -`、`__mul__ *`、`__truediv__ /`、`__floordiv__ //`、`__mod__ %`、`__divmod__ divmod()`、`__pow__ **` 或`pow()`、`__round__ round()` |
| 反向算术运算符     | `__ radd __`、`__ rsub __`、`__ rmul __`、`__ rtruediv __`、`__ rfloordiv __`、` __ rmod __`、`__ rdivmod __`、`__rpow__` |
| 增量赋值算术运算符 | `__iadd__`、`__isub__`、`__imul__`、 `__itruediv__`、`__ifloordiv__`、`__imod__`、`__ipow__` |
| 位运算符           | `__invert__ ~`、`__lshift__ <<`、`__rshift__ >>`、`__and__ &`、`__or__ |`、`__xor__ ^` |
| 反向位运算符       | `__rlshift__`、`__rrshift__`、`__rand__`、`__rxor__`、`__ror__` |
| 增量赋值位运算符   | `__ilshift__`、`__irshift__`、`__iand__`、`__ixor__`、`__ior__` |



### 1.2 内置函数

**通用内建函数：**

　　　　id() 函数：		查看对象的内存地址；

　　　　help()函数：	查看帮助信息；

　　　　type()函数：	查看对象的类型；不会认为子类是一种父类类型；

　　　　isinstance()函数：查看对象类型；会认为子类是一种父类类型；

　　　　dir()函数：		查看对象中的属性、方法等；　

　　　　ord():				得到一个字符所对应的数字编码；

  　　 	 chr():				实现由数字编码向字符的转化；



**数类型的内建函数：**

| abs(x)          | 返回数字的绝对值，如abs(-10) 返回 10。                       |
| --------------- | ------------------------------------------------------------ |
| ceil(x)         | 返回数字的上入整数，如math.ceil(4.1) 返回 5。                |
| cmp(x, y)       | 如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1。Python 3 已废弃 。使用 使用 (x>y)-(x<y) 替换。 |
| exp(x)          | 返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045         |
| fabs(x)         | 返回数字的绝对值，如math.fabs(-10) 返回10.0。                |
| floor(x)        | 返回数字的下舍整数，如math.floor(4.9)返回 4。                |
| log(x)          | 如math.log(math.e)返回1.0,math.log(100,10)返回2.0。          |
| log10(x)        | 返回以10为基数的x的对数，如math.log10(100)返回 2.0。         |
| max(x1, x2,...) | 返回给定参数的最大值，参数可以为序列。                       |
| min(x1, x2,...) | 返回给定参数的最小值，参数可以为序列。                       |
| modf(x)         | 返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示。 |
| pow(x, y)       | `x**y` 运算后的值。                                          |
| round(x [,n])   | 返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。 |
| sqrt(x)         | 返回数字x的平方根。                                          |

**随机数函数：**

| choice(seq)                       | 从序列的元素中随机挑选一个元素，比如random.choice(range(10))，从0到9中随机挑选一个整数。 |
| --------------------------------- | ------------------------------------------------------------ |
| randrange ([start,] stop [,step]) | 从指定范围内，按指定基数递增的集合中获取一个随机数，基数缺省值为1。。 |
| random()                          | 随机生成下一个实数，它在[0,1)范围内。                        |
| seed([x])                         | 改变随机数生成器的种子seed。如果你不了解其原理，你不必特别去设定seed，Python会帮你选择seed。 |
| shuffle(lst)                      | 将序列的所有元素随机排序。                                   |
| uniform(x, y)                     | 随机生成下一个实数，它在[x,y]范围内。                        |

**三角函数：**

| acos(x)     | 返回x的反余弦弧度值。                               |
| ----------- | --------------------------------------------------- |
| asin(x)     | 返回x的反正弦弧度值。                               |
| atan(x)     | 返回x的反正切弧度值。                               |
| atan2(y, x) | 返回给定的 X 及 Y 坐标值的反正切值。                |
| cos(x)      | 返回x的弧度的余弦值。                               |
| hypot(x, y) | 返回欧几里德范数 sqrt(x*x + y*y)。                  |
| sin(x)      | 返回的x弧度的正弦值。                               |
| tan(x)      | 返回x弧度的正切值。                                 |
| degrees(x)  | 将弧度转换为角度,如degrees(math.pi/2) ， 返回90.0。 |
| radians(x)  | 将角度转换为弧度。                                  |



## 2 序列结构

+ 容器序列

  `list`、`tuple` 和 `collections.deque` 这些序列能存放不同类型的数据。

+ 扁平序列

  `str`、`bytes`、`bytearray`、`memoryview` 和 `array.array`，这类序列只能容纳一种类型。

  > **容器序列**存放的是它们所包含的任意类型的对象的引用，而**扁平序列**里存放的是值而不是引用

+ 可变序列

  `list`、`bytearray`、`array.array`、`collections.deque` 和 `memoryview`。

+ 不可变序列

  `tuple`、`str` 和 `bytes`。



### 2.1 列表推导

```python
>>> x = 'ABC'
>>> dummy = [ord(x) for x in x]
>>> x ➊
'ABC'
>>> dummy ➋
[65, 66, 67]
>>>

#filter
>>> symbols = '$¢£¥€¤'
>>> beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]
>>> beyond_ascii
[162, 163, 165, 8364, 164]

# 笛卡尔积
>>> colors = ['black', 'white']
>>> sizes = ['S', 'M', 'L']
>>> tshirts = [(color, size) for color in colors for size in sizes]


```



### 2.2 元祖拆包

```python
>>> lax_coordinates = (33.9425, -118.408056)
>>> latitude, longitude = lax_coordinates # 元组拆包
>>> latitude
33.9425
>>> longitude
-118.408056

# 交换变量
>>> b, a = a, b

# 用*来处理剩下的元素
>>> a, b, *rest = range(5)
>>> a, b, rest
(0, 1, [2, 3, 4])
>>> a, b, *rest = range(3)
>>> a, b, rest

# 嵌套拆包
metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),  # ➊
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

print('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))

fmt = '{:15} | {:9.4f} | {:9.4f}'
for name, cc, pop, (latitude, longitude) in metro_areas:  # ➋
    if longitude <= 0:  # ➌
        print(fmt.format(name, latitude, longitude))
        
>>>
                |   lat.    |   long.  
Mexico City     |   19.4333 |  -99.1333
New York-Newark |   40.8086 |  -74.0204
Sao Paulo       |  -23.5478 |  -46.6358

```



### 2.3 元祖命名

> collections.namedtuple

```python
>>> from collections import namedtuple
>>> City = namedtuple('City', 'name country population coordinates')  ➊
>>> tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667))  ➋
>>> tokyo
City(name='Tokyo', country='JP', population=36.933, coordinates=(35.689722,
139.691667))
>>> tokyo.population  ➌
36.933
>>> tokyo.coordinates
(35.689722, 139.691667)
>>> tokyo[1]
'JP'

#_fields 类属性,类方法
>>> City._fields  ➊
('name', 'country', 'population', 'coordinates')

>>> LatLong = namedtuple('LatLong', 'lat long')
>>> delhi_data = ('Delhi NCR', 'IN', 21.935, LatLong(28.613889, 77.208889))

# 用 _make() 通过接受一个可迭代对象来生成这个类的一个实例，它的作用跟 City(*delhi_data) 是一样的。
>>> delhi = City._make(delhi_data)  ➋
# _asdict() 把具名元组以 collections.OrderedDict 的形式返回，我们可以利用它来把元组里的信息友好地呈现出来。
>>> delhi._asdict()  ➌
OrderedDict([('name', 'Delhi NCR'), ('country', 'IN'), ('population',
21.935), ('coordinates', LatLong(lat=28.613889, long=77.208889))])
>>> for key, value in delhi._asdict().items():
        print(key + ':', value)

name: Delhi NCR
country: IN
population: 21.935
coordinates: LatLong(lat=28.613889, long=77.208889)
>>>

```



|                     | 列表 | 元组 |                      |
| :-----------------: | :--: | :--: | :------------------: |
|   `s.__add__(s2)`   |  •   |  •   |    `s + s2`，拼接    |
|  `s.__iadd__(s2)`   |  •   |      | `s += s2`，就地拼接  |
|    `s.append(e)`    |  •   |      | 在尾部添加一个新元素 |
|     `s.clear()`     |  •   |      |     删除所有元素     |
| `s.__contains__(e)` |  •   |  •   |   `s` 是否包含 `e`   |
| `s.copy()`           | •    |      | 列表的浅复制                               |
| `s.count(e)`         | •    | •    | `e` 在 `s` 中出现的次数                        |
| `s.__delitem__(p)`   | •    |      | 把位于 `p` 的元素删除                                 |
| `s.extend(it)`       | •    |      | 把可迭代对象 `it` 追加给 `s`                          |
| `s.__getitem__(p)`   | •    | •    | `s[p]`，获取位置 `p` 的元素                           |
| `s.__getnewargs__()` |      | •    | 在 `pickle` 中支持更加优化的序列化                    |
| `s.index(e)`         | •    | •    | 在 `s` 中找到元素 `e` 第一次出现的位置                |
| `s.insert(p, e)`     | •    |      | 在位置 `p` 之前插入元素e                              |
| `s.__iter__()`       | •    | •    | 获取 `s` 的迭代器                                     |
| `s.__len__()`        | •    | •    | `len(s)`，元素的数量                                  |
| `s.__mul__(n)`       | •    | •    | `s * n`，`n` 个 `s` 的重复拼接                        |
| `s.__imul__(n)`      | •    |      | `s *= n`，就地重复拼接                                |
| `s.__rmul__(n)`      | •    | •    | `n * s`，反向拼接 *                                   |
| `s.pop([p])`         | •    |      | 删除最后或者是（可选的）位于 `p` 的元素，并返回它的值 |
| `s.remove(e)`        | •    |      | 删除 `s` 中的第一次出现的 `e`                         |
| `s.reverse()`              | •    |      | 就地把 `s` 的元素倒序排列                                    |
| `s.__reversed__()`         | •    |      | 返回 `s` 的倒序迭代器                                        |
| `s.__setitem__(p, e)`      | •    |      | `s[p] = e`，把元素 `e` 放在位置p，替代已经在那个位置的元素   |
| `s.sort([key], [reverse])` | •    |      | 就地对 `s` 中的元素进行排序，可选的参数有键（`key`）和是否倒序（`reverse`） |



### 2.4 切片

> 像列表（`list`）、元组（`tuple`）和字符串（`str`）这类序列类型都支持切片操作

**反向切片**

```python
>>> s = 'bicycle'
>>> s[::3]
'bye'
>>> s[::-1]
'elcycib'
>>> s[::-2]
'eccb'
```
**给切片赋值**

```python

>>> l = list(range(10))
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> l[2:5] = [20, 30]
>>> l
[0, 1, 20, 30, 5, 6, 7, 8, 9]
>>> del l[5:7]
>>> ls
[0, 1, 20, 30, 5, 8, 9]
>>> l[3::2] = [11, 22]
>>> l
[0, 1, 20, 11, 5, 22, 9]
>>> l[2:5] = 100  ➊
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only assign an iterable
>>> l[2:5] = [100]
>>> l
[0, 1, 100, 22, 9]
```

**对序列使用 + 和 *   **

```python
>>> l = [1, 2, 3]
>>> l * 5
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3]
>>> 5 * 'abcd'
'abcdabcdabcdabcdabcd'


```



**+=   -=  *=**

> 如果实现了 `__iadd__` `__imul__` 就会调用这个方法 , 可变序列都实现了这个方法



### 2.5 list.sort 和 内置方法 sorted

> List.sort 直接修改 列表 ,sorted 返回修改后的列表
>
> Random.shuffle 直接修改列表



#### Bisect (二分查找)

```python
# 查找index
bisect.bisect(list,needle)

# 1.找到分数对应的区间
# 2.根据区间返回打分
>>> def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
...     i = bisect.bisect(breakpoints, score)
...     return grades[i]
...
>>> [grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]
['F', 'A', 'C', 'C', 'B', 'A', 'A']

# 插入
bisect.insort(list,item)


```



### 2.6 数组 array.array

> 如果我们需要一个只包含数字的列表，那么 `array.array` 比 `list` 更高效。
>
> 包括 `.pop`、`.insert` 和 `.extend`。另外，数组还提供从文件读取和存入文件的更快的方法，如 `.frombytes` 和 `.tofile`。
>
> 数组类型必须一致

```python
from array import array
from random import random

floats = array('d', (random() for i in range(10 ** 7)))

print(floats[-1])

# 写文件
fp = open('floats.bin', 'wb')
floats.tofile(fp)
fp.close()

# 读文件
floats2 = array('d')
fp = open('floats.bin', 'rb')
floats2.fromfile(fp, 10 ** 7)
fp.close()

print(floats2[-1])
print(floats == floats2)
```



**列表和数组的属性和方法**

|      列表       | 数组 |      |                |
| :-------------: | :--: | :--: | -------------- |
| `s.__add(s2)__` |  •   |  •   | `s + s2`，拼接 |
| `s.__iadd(s2)__`    | •    | •    | `s += s2`，就地拼接                                          |
| `s.append(e)`       | •    | •    | 在尾部添加一个元素                                           |
| `s.byteswap`        |      | •    | 翻转数组内每个元素的字节序列，转换字节序                     |
| `s.clear()`         | •    |      | 删除所有元素                                                 |
| `s.__contains__(e)` | •    | •    | `s` 是否含有 `e`                                             |
| `s.copy()`          | •    |      | 对列表浅复制                                                 |
| `s.__copy__()`      |      | •    | 对 `copy.copy` 的支持                                        |
| `s.count(e)`        | •    | •    | `s` 中 `e` 出现的次数                                        |
| `s.__deepcopy__()`  |      | •    | 对 `copy.deepcopy` 的支持                                    |
| `s.__delitem__(p)`  | •    | •    | 删除位置 `p` 的元素                                          |
| `s.extend(it)`      | •    | •    | 将可迭代对象 `it` 里的元素添加到尾部                         |
| `s.frombytes(b)`    |      | •    | 将压缩成机器值的字节序列读出来添加到尾部                     |
| `s.fromfile(f, n)`  |      | •    | 将二进制文件 `f` 内含有机器值读出来添加到尾部，最多添加 `n`项 |

| `s.fromlist(l)`    |      | •    | 将列表里的元素添加到尾部，如果其中任何一个元素导致了 `TypeError` 异常，那么所有的添加都会取消 |
| ------------------ | ---- | ---- | ------------------------------------------------------------ |
| `s.__getitem__(p)` | •    | •    | `s[p]`，读取位置 `p` 的元素                                  |
| `s.index(e)`       | •    | •    | 找到 `e` 在序列中第一次出现的位置                            |
| `s.insert(p, e)`   | •    | •    | 在位于 `p` 的元素之前插入元素 `e`                            |
| `s.itemsize`       |      | •    | 数组中每个元素的长度是几个字节                               |
| `s.__iter__()`     | •    | •    | 返回迭代器                                                   |
| `s.__len__()`      | •    | •    | `len(s)`，序列的长度                                         |
| `s.__mul__(n)`     | •    | •    | `s * n`，重复拼接                                            |
| `s.__imul__(n)`    | •    | •    | `s *= n`，就地重复拼接                                       |
| `s.__rmul__(n)`    | •    | •    | `n * s`，反向重复拼接*                                       |
| `s.pop([p])`       | •    | •    | 删除位于 `p` 的值并返回这个值，`p` 的默认值是最后一个元素的位置 |
| `s.remove(e)`      | •    | •    | 删除序列里第一次出现的 `e` 元素                              |
| `s.reverse()`      | •    | •    | 就地调转序列中元素的位置                                     |
| `s.__reversed__()` | •    |      | 返回一个从尾部开始扫描元素的迭代器                           |

| `_()`                     |      |      |                                                         |
| ------------------------- | ---- | ---- | ------------------------------------------------------- |
| `s.__setitem__(p, e)`     | •    | •    | `s[p] = e`，把位于 `p` 位置的元素替换成 `e`             |
| `s.sort([key], [revers])` | •    |      | 就地排序序列，可选参数有 `key` 和 `reverse`             |
| `s.tobytes()`             |      | •    | 把所有元素的机器值用 `bytes` 对象的形式返回             |
| `s.tofile(f)`             |      | •    | 把所有元素以机器值的形式写入一个文件                    |
| `s.tolist()`              |      | •    | 把数组转换成列表，列表里的元素类型是数字对象            |
| `s.typecode`              |      | •    | 返回只有一个字符的字符串，代表数组元素在 C 语言中的类型 |

### 2.7 双向队列

> `collections.deque` 类（双向队列）是一个线程安全、可以快速从两端添加或者删除元素的数据类型

```python
>>> from collections import deque
>>> dq = deque(range(10), maxlen=10)  # maxlen 是一个可选参数，代表这个队列可以容纳的元素的数量，而且一旦设定，这个属性就不能修改了
>>> dq
deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
>>> dq.rotate(3)  # 队列的旋转操作接受一个参数 n，当 n > 0 时，队列的最右边的 n 个元素会被移动到队列的左边。当 n < 0 时，最左边的 n 个元素会被移动到右边
>>> dq
deque([7, 8, 9, 0, 1, 2, 3, 4, 5, 6], maxlen=10)
>>> dq.rotate(-4)
>>> dq
deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 0], maxlen=10)
>>> dq.appendleft(-1)  # 当试图对一个已满（len(d) == d.maxlen）的队列做尾部添加操作的时候，它头部的元素会被删除掉。注意在下一行里，元素 0 被删除了
>>> dq
deque([-1, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
>>> dq.extend([11, 22, 33])  # 在尾部添加 3 个元素的操作会挤掉 -1、1 和 2。
>>> dq
deque([3, 4, 5, 6, 7, 8, 9, 11, 22, 33], maxlen=10)
>>> dq.extendleft([10, 20, 30, 40])  # extendleft(iter) 方法会把迭代器里的元素逐个添加到双向队列的左边，因此迭代器里的元素会逆序出现在队列里。
>>> dq
deque([40, 30, 20, 10, 3, 4, 5, 6, 7, 8], maxlen=10)

## append 和 popleft 都是原子操作，也就说是 deque 可以在多线程程序中安全地当作先进先出的栈使用，而使用者不需要担心资源锁的问题。
```



## 3 字典和集合





## 0 demo

### 1 视频处理

```python
# 安装 moviepy
# pip3 install moviepy

import os
from moviepy.video.io.ffmpeg_tools import ffmpeg_extract_subclip
from moviepy.video.io.ffmpeg_reader import ffmpeg_parse_infos

source_dir = "./source"
target_dir = "./target"

files = os.listdir(source_dir)

for f in files:
    # ffmpeg_extract_subclip(f, 100, 100)
    fileName = source_dir + '/' + f
    res = ffmpeg_parse_infos(fileName)
    print("{} {}".format(f, res.get('duration')))
    ffmpeg_extract_subclip(fileName, 100, res.get('duration'), target_dir + '/' + f)
    
    
```









