# 普通知识

最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是`65535`，4个字节可以表示的最大整数是`4294967295`。

由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为`ASCII`编码，比如大写字母`A`的编码是`65`，小写字母`z`的编码是`122`。

但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，所以，中国制定了`GB2312`编码，用来把中文编进去。

你可以想得到的是，全世界有上百种语言，日本把日文编到`Shift_JIS`里，韩国把韩文编到`Euc-kr`里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。

因此，Unicode字符集应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。

最常用的是UCS-16编码，用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。现代操作系统和大多数编程语言都直接支持Unicode。

字母`A`用ASCII编码是十进制的`65`，二进制的`01000001`；

字符`0`用ASCII编码是十进制的`48`，二进制的`00110000`，注意字符`'0'`和整数`0`是不同的；

汉字`中`已经超出了ASCII编码的范围，用Unicode编码是十进制的`20013`，二进制的`01001110 00101101`。

如果把ASCII编码的`A`用Unicode编码，只需要在前面补0就可以，因此，`A`的Unicode编码是`00000000 01000001`。

新的问题又出现了：如果统一成Unicode编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。

本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码。UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间：

| 字符 | ASCII    | Unicode           | UTF-8                      |
| :--- | :------- | :---------------- | :------------------------- |
| A    | 01000001 | 00000000 01000001 | 01000001                   |
| 中   | x        | 01001110 00101101 | 11100100 10111000 10101101 |

UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。

浏览网页的时候，服务器会把动态生成的Unicode内容转换为UTF-8再传输到浏览器：

所以你看到很多网页的源码上会有类似`<meta charset="UTF-8" />`的信息，表示该网页正是用的UTF-8编码。

对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符：

```python
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'

# 如果知道字符的整数编码，还可以用十六进制这么写str：
>>> '\u4e2d\u6587'
'中文'

```

由于Python的字符串类型是`str`，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：`x = b'ABC'`

```python
# 要注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节。
# 以Unicode表示的str通过encode()方法可以编码为指定的bytes，例如：

>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
  
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'

# 如果bytes中包含无法解码的字节，decode()方法会报错：
>>> b'\xe4\xb8\xad\xff'.decode('utf-8')
Traceback (most recent call last):
  ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
  
# 如果bytes中只有一小部分无效的字节，可以传入errors='ignore'忽略错误的字节：
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'

# len()函数计算的是str的字符数
>>> len('ABC')
3
>>> len('中文')
2
# 如果换成bytes，len()函数就计算字节数
>>> len(b'ABC')
3
>>> len(b'\xe4\xb8\xad\xe6\x96\x87')
6
>>> len('中文'.encode('utf-8'))
6

```





## str, list, tuple, dict, set, Error



### str



```python
# 字符串可以被当作字符列表
"This is a string"[0]  # => ‘T’

# 用.format来格式化字符串
"{} can be {}".format("strings", "interpolated") # => "strings can be interpolated"

# 如果不想数参数，可以用关键字
"{name} wants to eat {food}".format(name="Bob", food="lasagna")
# => "Bob wants to eat lasagna"

# 如果你的Python3程序也要在Python2.5以下环境运行，也可以用老式的格式化语法
"%s can be %s the %s way" % ("strings", "interpolated", "old")

# None是一个对象
None # => None

# 当与None进行比较时不要用 ==，要用is。is是用来比较两个变量是否指向同一个对象。
"etc" is None # => False
None is None # => True

string.strip() # 去掉string前后的空隙

# 不可变对象

# str是不变对象，而list是可变对象。
# 字符串"xxx"也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串
```



### list

```python

# 用列表(list)储存序列
li = []
# 创建列表时也可以同时赋给元素
other_li = [4, 5, 6]

# 用append在列表最后追加元素
li.append(1) # li现在是[1]
li.append(2) # li现在是[1, 2]
li.append(4) # li现在是[1, 2, 4]
li.append(3) # li现在是[1, 2, 4, 3]
# 用pop从列表尾部删除
li.pop() # => 3 且li现在是[1, 2, 4]
# 把3再放回去
li.append(3) # li变回[1, 2, 4, 3]

# 列表存取跟数组一样
li[0] # => 1
# 取出最后一个元素 
li[-1] # => 3

li[-2:-1] # => [4]
li[-2:] # =>[4, 3]



# 列表有切割语法 切片
li[1:3] # => [2, 4]
# 取尾
li[2:] # => [4, 3]
# 取头
li[:3] # => [1, 2, 4]
# 隔一个取一个
li[::2] # =>[1, 4]
# 倒排列表
li[::-1] # => [3, 4, 2, 1]
# 可以用三个参数的任何组合来构建切割
# li[始:终:步伐]

# 用del删除任何一个元素
del li[2] # li is now [1, 2, 3]

# 列表可以相加
# 注意：li和other_li的值都不变
li + other_li # => [1, 2, 3, 4, 5, 6]

# 用extend拼接列表
li.extend(other_li) # li现在是[1, 2, 3, 4, 5, 6]

# 用in测试列表是否包含值
1 in li # => True

```



### tuple

```python
# tuple 元组是不可改变的序列(指向永远不变), 它也没有append()，insert()这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用tup[0]，tup[-1]，但不能赋值成另外的元素。
tup = (1, 2, 3)
tup[0] # => 1
tup[0] = 3 # 抛出TypeError

# 列表允许的操作元组大都可以
len(tup) # => 3
tup + (4, 5, 6) # => (1, 2, 3, 4, 5, 6)
tup[:2] # => (1, 2)
2 in tup # => True

# 可以把元组合列表解包，赋值给变量
a, b, c = (1, 2, 3) # 现在a是1，b是2，c是3
# 元组周围的括号是可以省略的
d, e, f = 4, 5, 6
# 交换两个变量的值就这么简单
e, d = d, e # 现在d是5，e是4

t = (1,) # 继续表示为元组 (1, )
t = (1) # 会被理解成 t = 1 这个数且不能修改
t = ('a', 'b', ['A', 'B']) # 三个元素：两个str 和 一个list
t[2][0] = 'X'
t[2][1] = 'Y'
t # ('a', 'b', ['X', 'Y']) 没有改变， 只是列表里的改变了
# tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple
```



### dict

```python
# 用字典表达映射关系
empty_dict = {}
# 初始化的字典
filled_dict = {“one”: 1, “two”: 2, “three”: 3}
# 用[]取值
filled_dict[“one”] # => 1
# 用 keys 获得所有的键。
# 因为 keys 返回一个可迭代对象，所以在这里把结果包在 list 里。我们下面会详细介绍可迭代。
# 注意：字典键的顺序是不定的，你得到的结果可能和以下不同。
list(filled_dict.keys()) # => [“three”, “two”, “one”]
# 用values获得所有的值。跟keys一样，要用list包起来，顺序也可能不同。
list(filled_dict.values()) # => [3, 2, 1]

# 用in测试一个字典是否包含一个键 key
"one" in filled_dict # => True
1 in filled_dict # => False
# 访问不存在的键会导致KeyError
filled_dict[“four”] # KeyError
# 用get来避免KeyError
filled_dict.get(“one”) # => 1
filled_dict.get(“four”) # => None
# 当键不存在的时候get方法可以返回默认值
filled_dict.get(“one”, 4) # => 1
filled_dict.get(“four”, 4) # => 4

# setdefault方法只有当键不存在的时候插入新值
filled_dict.setdefault(“five”, 5) # filled_dict[“five”]设为5
filled_dict.setdefault(“five”, 6) # filled_dict[“five”]还是5

# 字典赋值
filled_dict.update({“four”:4}) # => {“one”: 1, “two”: 2, “three”: 3, “four”: 4}
filled_dict[“four”] = 4 # 另一种赋值方法

# 用del删除
del filled_dict[“one”] # 从filled_dict中把one删除
```



### set

```python
# set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，同样不可以放入可变对象，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。试试把list放入set，看看是否会报错。

# 用set表达集合 set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。
empty_set = set()
# 初始化一个集合，语法跟字典相似。 
some_set = {1, 1, 2, 2, 3, 4} # some_set现在是{1, 2, 3, 4} 自动删掉重复的key
s = set([1, 2, 3]) # 可以理解成把list转为set，显示的顺序也不表示set是有序的
s = set([1, 1, 2, 2, 3, 3]) # = {1, 2, 3} 

# 可以把集合赋值于变量
filled_set = some_set

# 为集合添加元素
filled_set.add(5) # filled_set现在是{1, 2, 3, 4, 5}

# & 取交集
other_set = {3, 4, 5, 6}
filled_set & other_set # => {3, 4, 5}

# | 取并集
filled_set | other_set # => {1, 2, 3, 4, 5, 6}

# - 取补集
{1, 2, 3, 4} - {2, 3, 5} # => {1, 4}

# in 测试集合是否包含元素
2 in filled_set # => True
10 in filled_set # => False

```



### **Error**

```python
# 访问未赋值的变量会抛出异常
# 参考流程控制一段来学习异常处理
some_unknown_var # 抛出NameError
# 越界存取会造成IndexError
li = [1, 2, 3, 4]
li[4] # 抛出IndexError


# 用try/except块处理异常状况
try:
​ # 用raise抛出异常
​ raise IndexError("This is an index error")
except IndexError as e:
​ pass # pass是无操作，但是应该在这里处理错误
except (TypeError, NameError):
​ pass # 可以同时处理不同类的错误
else: # else语句是可选的，必须在所有的except之后
​ print(“All good!”) # 只有当try运行完没有错误的时候这句才会运行


```



### iterable 和 iterator 区别



```python
# Python提供一个叫做可迭代(iterable)的基本抽象。一个可迭代对象是可以被当作序列的对象。比如说上面range返回的对象就是可迭代的。

filled_dict = {“one”: 1, “two”: 2, “three”: 3}
our_iterable = filled_dict.keys()
print(our_iterable) # => dict_keys([‘one’, ‘two’, ‘three’])，是一个实现可迭代接口的对象

# 可迭代对象可以遍历
for i in our_iterable:
​ print(i) # 打印 one, two, three

# 但是不可以随机访问
our_iterable[1] # 抛出TypeError

# 可迭代对象知道怎么生成迭代器
our_iterator = iter(our_iterable)

# 迭代器是一个可以记住遍历的位置的对象
# 用__next__可以取得下一个元素
our_iterator.__next__() # => “one”

# 再一次调取__next__时会记得位置
our_iterator.__next__() # => “two”
our_iterator.__next__() # => “three”

# 当迭代器所有元素都取出后，会抛出StopIteration
our_iterator.__next__() # 抛出StopIteration

# 可以用list一次取出迭代器所有的元素
list(filled_dict.keys()) # => Returns [“one”, “two”, “three”]

# Python的for循环不仅可以用在list或tuple上，还可以作用在其他可迭代对象上。比如dict就可以迭代：
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
a
c
b
# 默认情况下，dict迭代的是key。如果要迭代value，可以用for value in d.values()，如果要同时迭代key和value，可以用for k, v in d.items()。

# 如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断：

>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False

# Python内置的enumerate函数可以把一个list变成 索引-元素对，这样就可以在for循环中同时迭代索引和元素本身：
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C

# 可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
# 可以使用isinstance()判断一个对象是否是Iterator对象：
>>> from collections.abc import Iterator
>>> isinstance((x for x in range(10)), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('abc', Iterator)
False

# 生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。把list、dict、str等Iterable变成Iterator可以使用iter()函数：

>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True

for x in [1, 2, 3, 4, 5]:
    pass
  
# 完全等价于

# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```



## **高级特性**

```python
# List Comprehensions

# 列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

>>> [m + n for m in 'ABC' for n in 'XYZ'] # 两层循环
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']

>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']

>>> L = ['Hello', 'World', 'IBM', 'Apple']
>>> [s.lower() for s in L]
['hello', 'world', 'ibm', 'apple']


>>> [x for x in range(1, 11) if x % 2 == 0]
[2, 4, 6, 8, 10]

# 另一些童鞋发现把if写在for前面必须加else，否则报错：这是因为for前面的部分是一个表达式，它必须根据x计算出一个结果。因此，考察表达式：x if x % 2 == 0，它无法根据x计算出结果，因为缺少else，必须加上else：

>>> [x if x % 2 == 0 else -x for x in range(1, 11)]
[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]

# 上述for前面的表达式x if x % 2 == 0 else -x才能根据x计算出确定的结果。
# 可见，在一个列表生成式中，for前面的if ... else是表达式，而for后面的if是过滤条件，不能带else。

# 在Python中，这种一边循环一边计算的机制，称为生成器：generator。节省内存空间
# 要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator：

>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
# L是一个list，而g是一个generator。

# 如果要一个一个打印出来，可以通过next()函数获得generator的下一个返回值 或者 通过for循环来迭代它
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'
# 上面的函数和generator仅一步之遥。要把fib函数变成generator，只需要把print(b)改为 yield b 就可以了：

def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b 
        a, b = b, a + b
        n = n + 1
    return 'done'
  
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>

# 但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中：

>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
...
g: 1
g: 1
g: 2
g: 3
g: 5
g: 8
Generator return value: done

# 这些可以直接作用于for循环的对象统称为可迭代对象：Iterable。
# 可以使用isinstance()判断一个对象是否是Iterable对象：

>>> from collections.abc import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance('abc', Iterable)
True
>>> isinstance((x for x in range(10)), Iterable)
True
>>> isinstance(100, Iterable)
False

# 而生成器不但可以作用于for循环，还可以被next()函数不断调用并返回下一个值，直到最后抛出StopIteration错误表示无法继续返回下一个值了。

```



# 函数 Function



```python
# 用def定义新函数
def add(x, y):
  print(“x is {} and y is {}”.format(x, y))
        return x + y # 用return语句返回
  
# 也可以用关键字参数来调用函数
add(y=6, x=5) # 关键字参数可以用任何顺序

# 我们可以定义一个可变参数函数
def varargs(*args):
    return args

varargs(1, 2, 3) # => (1, 2, 3)

# 我们也可以定义一个关键字可变参数函数
def keyword_args(**kwargs):
    return kwargs

# 我们来看看结果是什么：
keyword_args(big="foot", loch="ness") # => {"big": "foot", "loch": "ness"}

# 这两种可变参数可以混着用
def all_the_args(*args, **kwargs):
  print(args)
  print(kwargs)

all_the_args(1, 2, a=3, b=4) 

"""""
prints:
(1, 2)
{"a": 3, "b": 4}
""""

# 调用可变参数函数时可以做跟上面相反的，用展开序列，用**展开字典。
args = (1, 2, 3, 4)
kwargs = {"a": 3, "b": 4}
all_the_args(args) # 相当于 foo(1, 2, 3, 4)
all_the_args(kwargs) # 相当于 foo(a=3, b=4)
all_the_args(*args, kwargs) # 相当于 foo(1, 2, 3, 4, a=3, b=4)

# 函数作用域
x = 5
def setX(num):
# 局部作用域的x和全局域的x是不同的
​ x = num # => 43
​ print (x) # => 43

def setGlobalX(num):
​ global x
​ print (x) # => 5
​ x = num # 现在全局域的x被赋值
​ print (x) # => 6

setX(43)
setGlobalX(6)

# 函数在Python是一等公民
def create_adder(x):
​ def adder(y):
​ return x + y
​ return adder

add_10 = create_adder(10)
add_10(3) # => 13




# 也有匿名函数
(lambda x: x > 2)(3) # => True





# 内置的高阶函数
map(function, Iterable)
map(add_10, [1, 2, 3]) # => [11, 12, 13]
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r) # 这里要转成list
[1, 4, 9, 16, 25, 36, 49, 64, 81]
# map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回
# map()接收一个函数 f 和一个 list, 并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 Iterator 并返回

list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
['1', '2', '3', '4', '5', '6', '7', '8', '9']

# reduce()函数接收的参数和 map()类似，一个函数 f，一个list
# 但行为和 map()不同，reduce()传入的函数f必须接收两个参数，python3不用了
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

from functools import reduce

def str2float(s):
    DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
    def char2num(s):
        return DIGITS[s]
    s = s.split('.')
    float1 = reduce(lambda x, y: x * 10 +y, map(char2num, s[0]))
    float2 = reduce(lambda x, y: x * 0.1 +y, map(char2num, s[1][::-1]))
    return float1 + float2 * 0.1


list(filter(lambda x: x > 5, [3, 4, 5, 6, 7])) # => [6, 7]
# filter()函数接收一个函数 f 和一个list, 这个函数f的作用是对每个元素进行判断，返回 True或 False
# filter()根据判断结果 True 或者 False 自动过滤掉不符合条件的元素，返回由符合条件元素组成的新Iterator。

# 注意到filter()函数返回的是一个Iterator，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list。
def is_palindrome(n):
     s = str(n)
     if s == s[::-1]:
        return n


sorted(iterable, key = None, reverse = False)
# sort 与 sorted 区别：
# sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
# list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。
# key – 函数规则
# reverse – 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。
sorted([5, 2, 4, 3, 1]) #=> [1, 2, 3, 4, 5]
sorted({5:'D', 2 : 'A' , 4 : 'D', 3 : 'C', 1: 'D'}) #=> [1, 2, 3, 4, 5] 

>>> sorted([36, 5, -12, 9, -21], key=abs) # 按绝对值大小排序
[5, 9, -12, -21, 36]

>>> sorted(['bob', 'about', 'Zoo', 'Credit'])
# 默认情况下，对字符串排序，是按照ASCII的大小比较的，由于'Z' < 'a'，结果，大写字母Z会排在小写字母a的前面。
['Credit', 'Zoo', 'about', 'bob']

>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower) # 所有转换成小写
['about', 'bob', 'Credit', 'Zoo']


def by_name(t): # 按姓名首字母
    return t[0] 
def by_score(t): # 按分数从高到低
    return 100 - t[1]
L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
L2 = sorted(L, key=by_name)
L2 = sorted(L, key=by_score)
print(L2)




# 用列表推导式可以简化映射和过滤。列表推导式的返回值是另一个列表。
[add_10(i) for i in [1, 2, 3]] # => [11, 12, 13]
[x for x in [3, 4, 5, 6, 7] if x > 5] # => [6, 7]

# 函数对象有一个__name__属性，可以拿到函数的名字：

def now():
  print('2015-3-25')
  
f = now
now.__name_ # => "now"
f.__name_ # => "now"





# 递归函数 在函数内部调用自己
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
  
# 使用递归函数需要注意防止栈溢出。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。可以试试fact(1000)：RuntimeError: maximum recursion depth exceeded in comparison

# 解决 递归调用栈溢出 的方法是通过 尾递归优化 ，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。
# 尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

# 上面的fact(n)函数由于return n * fact(n - 1)引入了乘法表达式，所以就不是尾递归了。要改成尾递归方式，需要多一点代码，主要是要把每一步的乘积传入到递归函数中：

def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product) # 仅返回递归函数本身
  # num - 1 和 num * product 在函数调用前就会被计算，不影响函数调用。
  
# fact(5)对应的fact_iter(5, 1)的调用如下：
#===> fact_iter(5, 1)
#===> fact_iter(4, 5)
#===> fact_iter(3, 20)
#===> fact_iter(2, 60)
#===> fact_iter(1, 120)
#===> 120



# 返回函数 闭包

def calc_sum(*args):
    ax = 0
    for n in args:
        ax = ax + n
    return ax
  
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum  # 不需要立刻求和，而是在后面的代码中 返回函数
# 当我们调用lazy_sum()时，返回的并不是求和结果，而是求和函数：
>>> f = lazy_sum(1, 3, 5, 7, 9)# 参数和变量都保存在返回的函数中
>>> f
<function lazy_sum.<locals>.sum at 0x101c6ed90>
>>> f() # 只有真的调用函数才会求和
25
# 在这个例子中，我们在函数lazy_sum中又定义了函数sum，并且，内部函数sum可以引用外部函数lazy_sum的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力

# 请再注意一点，当我们调用lazy_sum()时，每次调用都会返回一个新的函数，即使传入相同的参数：
>>> f1 = lazy_sum(1, 3, 5, 7, 9)
>>> f2 = lazy_sum(1, 3, 5, 7, 9)
>>> f1==f2
False
# f1()和f2()的调用结果互不影响。

def count():
    fs = []
    for i in range(1, 4):
        def f():
             return i*i
        fs.append(f)
    return fs

f1, f2, f3 = count()


def createCounter():
    i = 0
    def counter():
        nonlocal i # 这里nonlocal声明之后，x的地址和前边x=0是一样的
        # x保存内函数counter每次作用后返回的值，比如第一次x=0，counter()后，x=0+1=1，counter（）后，x=1+1=2......以此类推
        i += 1
        return i
    return counter
  
def createCounter():

    def g():    #生成器生成有序数列1，2，3......

        n=0

        while True:

            n+=1

            yield n

    a=g()

    def counter():

        return next(a)  #每次调用next()函数获得生成器的下一个返回值

    return counter
  
# 偏函数
# Python的functools模块提供了很多有用的功能，其中一个就是偏函数（Partial function）。要注意，这里的偏函数和数学意义上的偏函数不一样。
# functools.partial把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

>>> import functools
>>> int2 = functools.partial(int, base=2) # 用已有的int()函数，改变base = 2 
>>> int2('1000000')
64
>>> int2('1010101')
85

```



# 类

面向对象最重要的概念就是类（Class）和实例（Instance），必须牢记类是抽象的模板，比如Student类，而实例是根据类创建出来的一个个具体的“对象”，每个对象都拥有相同的方法，但各自的数据可能不同。

创建实例是通过类名+()实现的

```python
>>> bart = Student()
>>> bart
<__main__.Student object at 0x10a67a590> # 变量bart指向的就是一个Student的实例，后面的0x10a67a590是内存地址，每个object的地址都不一样
>>> Student
<class '__main__.Student'> # 而Student本身则是一个类
```

可以自由地给一个实例变量绑定属性，比如，给实例`bart`绑定一个`name`属性：`bart.name = 'Bart Simpson'` 不用在class里定义

```python
# 定义一个继承object的类
class Human(object):
    # 类属性，被所有此类的实例共用。实例 instance 实例是根据类创建出来的一个个具体的 “对象” object。
    species = "H. sapiens"

    # 构造方法，当实例被初始化时被调用。
    # 注意名字前后的双下划线，这是表明这个属性或方法对Python有特殊意义，但是允许用户自行定义。
    # 你自己取名时不应该用这种格式。
    # 由于类可以起到模板的作用，因此，可以在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去。通过定义一个特殊的__init__方法，在创建实例的时候，就把name，score等属性绑上去：
    def __init__(self, name):
        # Assign the argument to the instance's name attribute
        #__init__ 方法的第一参数永远是 self 用来表示类(创建的实例)本身, self.name 是类的属性, = name 是外部赋值
        self.name = name

    # 类方法 class function，第一个参数总是self，就是这个类，也就是后面引用的实例
    def say(self, msg):
        return "{name}: {message}".format(name=self.name, message=msg) # 可以在方法内加新属性
                                       # name, message 这里是新的参数
    # 类方法，被所有此类的实例共用。第一个参数是这个类对象。
    @classmethod
    def get_species(cls):
        return cls.species # species = "H. sapiens"
    # classmethod 修饰符对应的函数不需要实例化，不需要 self 参数，但第一个参数需要是表示自身类的 cls 参数，可以来调用类的属性，类的方法，实例化对象等。 cls.species 还可以 cls().say() 
>>> Human.get_species() # 不需要实例化 => "H. sapiens"
    
    # 静态方法。调用时没有实例或类的绑定。该方法不强制要求传递参数
    @staticmethod
    def grunt():
        return "*grunt*"
      
      
# 构造一个实例
i = Human(name="Ian")
print(i.say("hi")) # 印出 “Ian: hi”

j = Human("Joel")
print(j.say("hello")) # 印出 “Joel: hello”

# 调用一个类方法
i.get_species() # => “H. sapiens”

# 改一个共用的类属性
Human.species = "H. neanderthalensis" # 外部可以改动公有属性
i.get_species() # => “H. neanderthalensis”
j.get_species() # => “H. neanderthalensis”

# 调用静态方法
Human.grunt() # => “*grunt*“

```





## Self 详解

```python
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score

## _init_() 是初始化 给类定义自己的属性变量 self这里指代类Student的实例
## 这里只是在声明变量


student = Student("Hugh", 99)
## 数据来自于外部
## 赋值一个类的对象
student.name
student.score

class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score
    def print_score(self):
        print("%s: %s" %(self.__name,self.__score))
        
student = Student('Hugh', 99)
student.__name
"""
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute '__name'
"""
## 在内部属性变量之前加上_ 外部无法再次访问属性变量

class Student(object):
    
""""
    ....
""""
  
    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
## 如果外部需要获取该变量 给Student类增加get_name和get_score function
## 需要注意的是，在Python中，变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。

class Student(object):
    ...
    def get_grade(self):
        if self.score >= 90:
            return 'A'
        elif self.score >= 60:
            return 'B'
        else:
            return 'C'
          
>>> student.get_grade()
'A'

>>> class Student(object):
...     name = 'Student'
...
>>> s = Student() # 创建实例s
>>> print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
Student
>>> print(Student.name) # 打印类的name属性
Student
>>> s.name = 'Michael' # 给实例绑定name属性
>>> print(s.name) # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
Michael
>>> print(Student.name) # 但是类属性并未消失，用Student.name仍然可以访问
Student
>>> del s.name # 如果删除实例的name属性
>>> print(s.name) # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
Student
```



## 继承和多态

在OOP程序设计中，当我们定义一个class的时候，可以从某个现有的class继承，新的class称为子类（Subclass），而被继承的class称为基类、父类或超类（Base class、Super class）。

```python
class Animal(object):
    def run(self):
        print('Animal is running...')
        
class Dog(Animal):

    def run(self):
        print('Dog is running...')

class Cat(Animal):

    def run(self):
        print('Cat is running...')
        
dog = Dog()
dog.run() # Dog is running...

cat = Cat()
cat.run() # Cat is running...

#当子类和父类都存在相同的run()方法时，我们说，子类的run()覆盖了父类的run()，在代码运行的时候，总是会调用子类的run()。这样，我们就获得了继承的另一个好处：多态。

# 当我们定义一个class的时候，我们实际上就定义了一种数据类型。我们定义的数据类型和Python自带的数据类型，比如str、list、dict没什么两样：
a = list() # a是list类型
b = Animal() # b是Animal类型
c = Dog() # c是Dog类型

>>> isinstance(a, list)
True
>>> isinstance(b, Animal)
True
>>> isinstance(c, Dog)
True
>>> isinstance(c, Animal) # c不仅是狗还是动物 但反过来不行 c = Animal()
True

# 我们还需要再编写一个函数，这个函数接受一个Animal类型的变量：animal = Animal()

def run_twice(animal):
    animal.run()
    animal.run()
    
>>> run_twice(Animal())
Animal is running...
Animal is running...

>>> run_twice(Dog()) # 就可以直接继承
Dog is running...
Dog is running...

class Tortoise(Animal): # 我们在定一个Animal的子类 就可以直接用
    def run(self):
        print('Tortoise is running slowly...')
        
>>> run_twice(Tortoise())
Tortoise is running slowly...
Tortoise is running slowly...

```

### 静态语言 vs 动态语言

对于静态语言（例如Java）来说，如果需要传入`Animal`类型，则传入的对象必须是`Animal`类型或者它的子类，否则，将无法调用`run()`方法。

对于Python这样的动态语言来说，则不一定需要传入`Animal`类型。我们只需要保证传入的对象有一个`run()`方法就可以了：

```python
class Timer(object): # 不用一定继承Animal 只要有run function
    def run(self):
        print('Start...')
```

这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

Python的“file-like object“就是一种鸭子类型。对真正的文件对象，它有一个`read()`方法，返回其内容。但是，许多对象，只要有`read()`方法，都被视为“file-like object“。许多函数接收的参数就是“file-like object“，你不一定要传入真正的文件对象，完全可以传入任何实现了`read()`方法的对象。



### 获取对象信息

首先，我们来判断对象类型，使用`type()`函数：

```python
>>> type(123)
<class 'int'>
>>> type('str')
<class 'str'>
>>> type(None)
<type(None) 'NoneType'>
# 如果一个变量指向函数或者类，也可以用type()判断：
>>> type(abs)
<class 'builtin_function_or_method'>
>>> type(a)
<class '__main__.Animal'> 

# 但是type()函数返回的是什么类型呢？它返回对应的Class类型。如果我们要在if语句中判断，就需要比较两个变量的type类型是否相同：
>>> type(123)==type(456)
True
>>> type(123)==int
True
>>> type('abc')==type('123')
True
>>> type('abc')==str
True
>>> type('abc')==type(123)
False

# 判断基本数据类型可以直接写int，str等，但如果要判断一个对象是否是函数怎么办？可以使用types模块中定义的常量：
>>> import types
>>> def fn():
...     pass
...
>>> type(fn)==types.FunctionType
True
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True

```

对于class的继承关系来说，使用`type()`就很不方便。我们要判断class的类型，可以使用`isinstance()`函数。

```python
# object -> Animal -> Dog -> Husky

>>> a = Animal()
>>> d = Dog()
>>> h = Husky()

>>> isinstance(h, Husky)
True
>>> isinstance(h, Dog)
True
# isinstance()判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。
>>> isinstance(d, Husky)
False # 反过来就不行

# 能用type()判断的基本类型也可以用isinstance()判断：
>>> isinstance('a', str)
True
>>> isinstance(123, int)
True
>>> isinstance(b'a', bytes)
True

# 并且还可以判断一个变量是否是某些类型中的一种，比如下面的代码就可以判断是否是list或者tuple：
>>> isinstance([1, 2, 3], list)
True
>>> isinstance((1, 2, 3), tuple)
True

```

如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：

```python
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```

类似`__xxx__`的属性和方法在Python中都是有特殊用途的，比如`__len__`方法返回长度。在Python中，如果你调用`len()`函数试图获取一个对象的长度，实际上，在`len()`函数内部，它自动去调用该对象的`__len__()`方法，所以，下面的代码是等价的：

```python
>>> len('ABC')
3
>>> 'ABC'.__len__()
3

# 我们自己写的类，如果也想用len(myObj)的话，就自己写一个__len__()方法：
>>> class MyDog(object):
...     def __len__(self): # 骗他！
...         return 100
...
>>> dog = MyDog()
>>> len(dog)
100
# 剩下的都是普通属性或方法，比如lower()返回小写的字符串：
>>> 'ABC'.lower()
'abc'
# 仅仅把属性和方法列出来是不够的，配合getattr()、setattr()以及hasattr()，我们可以直接操作一个对象的状态：
>>> class MyObject(object):
...     def __init__(self):
...         self.x = 9
...     def power(self):
...         return self.x * self.x
...
>>> obj = MyObject()

>>> hasattr(obj, 'x') # 有属性'x'吗？
True
>>> obj.x
9
>>> hasattr(obj, 'y') # 有属性'y'吗？
False
>>> setattr(obj, 'y', 19) # 设置一个属性'y'
>>> hasattr(obj, 'y') # 有属性'y'吗？
True
>>> getattr(obj, 'y') # 获取属性'y'
19
>>> obj.y # 获取属性'y'
19
# 如果试图获取不存在的属性，会抛出AttributeError的错误：
>>> getattr(obj, 'z') # 获取属性'z'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'MyObject' object has no attribute 'z'
  
# 可以传入一个default参数，如果属性不存在，就返回默认值：
>>> getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
404

# 也可以获得对象的方法：
>>> hasattr(obj, 'power') # 有属性'power'吗？
True
>>> getattr(obj, 'power') # 获取属性'power'
<bound method MyObject.power of <__main__.MyObject object at 0x10077a6a0>>
>>> fn = getattr(obj, 'power') # 获取属性'power'并赋值到变量fn
>>> fn # fn指向obj.power
<bound method MyObject.power of <__main__.MyObject object at 0x10077a6a0>>
>>> fn() # 调用fn()与调用obj.power()是一样的
81

# 通过内置的一系列函数，我们可以对任意一个Python对象进行剖析，拿到其内部的数据。要注意的是，只有在不知道对象信息的时候，我们才会去获取对象信息。如果可以直接写：
sum = obj.x + obj.y

def readImage(fp):
    if hasattr(fp, 'read'):
        return read(fp)
    return None
  
# 假设我们希望从文件流fp中读取图像，我们首先要判断该fp对象是否存在read方法，如果存在，则该对象是一个流，如果不存在，则无法读取。hasattr()就派上了用场。

# 请注意，在Python这类动态语言中，根据鸭子类型，有read()方法，不代表该fp对象就是一个文件流，它也可能是网络流，也可能是内存中的一个字节流，但只要read()方法返回的是有效的图像数据，就不影响读取图像的功能。
```

### 面向对象的高级编程



```python
# 正常情况下，当我们定义了一个class，创建了一个class的实例后，我们可以给该实例绑定任何属性和方法，这就是动态语言的灵活性。先定义class：
class Student(object):
    pass
  
# 然后，尝试给实例绑定一个属性：
>>> s = Student()
>>> s.name = 'Michael' # 动态给实例绑定一个属性
>>> print(s.name)
Michael
# 还可以尝试给实例绑定一个方法：
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
>>> s.set_age(25) # 调用实例方法
>>> s.age # 测试结果
25
# 但是，给一个实例绑定的方法，对另一个实例是不起作用的：
>>> s2 = Student() # 创建新的实例
>>> s2.set_age(25) # 尝试调用方法
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'set_age'

# 为了给所有实例都绑定方法，可以给class绑定方法：
>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score

# 给class绑定方法后，所有实例均可调用：
>>> s.set_score(100)
>>> s.score
100
>>> s2.set_score(99)
>>> s2.score
99

# 通常情况下，上面的set_score方法可以直接定义在class中，但动态绑定允许我们在程序运行的过程中动态给class加上功能，这在静态语言中很难实现。
# 但是，如果我们想要限制实例的属性怎么办？比如，只允许对Student实例添加name和age属性。
# 为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性：

class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
    
>>> s = Student() # 创建新的实例
>>> s.name = 'Michael' # 绑定属性'name'
>>> s.age = 25 # 绑定属性'age'
>>> s.score = 99 # 绑定属性'score'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'score'
  
# 由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误。
# 使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：

>>> class GraduateStudent(Student):
...     pass
...
>>> g = GraduateStudent()
>>> g.score = 9999

# 除非在子类中也定义__slots__，这样，子类实例允许定义的属性就是自身的__slots__加上父类的__slots__。


# 在绑定属性时，如果我们直接把属性暴露出去，虽然写起来很简单，但是，没办法检查参数，导致可以把成绩随便改
# 这显然不合逻辑。为了限制score的范围，可以通过一个set_score()方法来设置成绩，再通过一个get_score()来获取成绩，这样，在set_score()方法里，就可以检查参数：

class Student(object):
  
    def get_score(self):
         return self._score

    def set_score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value

# 现在，对任意的Student实例进行操作，就不能随心所欲地设置score了：

>>> s = Student()
>>> s.set_score(60) # ok!
>>> s.get_score()
60
>>> s.set_score(9999)
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
  

# Python内置的@property装饰器就是负责把一个方法变成属性调用的：

class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
# 把一个 getter方法 score(self) 变成属性，只需要加上@property就可以了，此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法 score(self, value) 变成属性赋值，于是，我们就拥有一个可控的属性操作：

>>> s = Student()
>>> s.score = 60 # OK，实际转化为s.set_score(60)
>>> s.score # OK，实际转化为s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
  
  
# 还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性：

class Student(object):

    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2021 - self._birth
# 上面的birth是可读写属性，而age就是一个只读属性，因为age可以根据birth和当前时间计算出来。
# 要特别注意：属性的方法名不要和实例变量重名。例如，以下的代码是错误的：
class Student(object):

    # 方法名称和实例变量均为birth:
    @property
    def birth(self):
        return self.birth

# 这是因为调用s.birth时，首先转换为方法调用，在执行return self.birth时，又视为访问self的属性，于是又转换为方法调用，造成无限递归，最终导致栈溢出报错RecursionError。

# 多重继承
# MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。
# Python自带的很多库也使用了MixIn。举个例子，Python自带了TCPServer和UDPServer这两类网络服务，而要同时服务多个用户就必须使用多进程或多线程模型，这两种模型由ForkingMixIn和ThreadingMixIn提供。通过组合，我们就可以创造出合适的服务来。

```







# **模块**

标准模块书写：

```python
#!/usr/bin/env python3 
# -*- coding: utf-8 -*- 

' a test module '

__author__ = 'Francis'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```

* 第1行和第2行是标准注释，第1行注释可以让这个`hello.py`文件直接在Unix/Linux/Mac上运行，Windows系统会忽略这个注释；第2行注释表示.py文件本身使用标准UTF-8编码；由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行。

* 第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；

* 第6行使用`__author__`变量把作者写进去，这样当你公开源代码后别人就可以瞻仰你的大名；

* `sys`模块有一个`argv`变量，用list存储了命令行的所有参数。`argv`至少有一个元素，因为第一个参数永远是该.py文件的名称，例如：

  运行`python3 hello.py`获得的`sys.argv`就是`['hello.py']`；

  运行`python3 hello.py Francis`获得的`sys.argv`就是`['hello.py', 'Francis']`。

用命令行：

```
$ python3 hello.py
Hello, world!
$ python3 hello.py Francis
Hello, Francis!
```



**作用域**

* 正常的函数和变量名是公开的（public），可以被直接引用，比如：`abc`，`x123`，`PI`等；

* 类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如上面的`__author__`，`__name__`就是特殊变量，`hello`模块定义的文档注释也可以用特殊变量`__doc__`访问，我们自己的变量一般不要用这种变量名；

* 类似`_xxx()`和`__xxx`这样的函数或变量就是非公开的（private），不应该被直接引用，比如`_abc()`，`__abc`等；



```python
# 用import导入模块
import math
print(math.sqrt(16)) # => 4.0

# 也可以从模块中导入个别值
from math import ceil, floor
print(ceil(3.7)) # => 4.0
print(floor(3.7)) # => 3.0

# 可以导入一个模块中所有值
# 警告：不建议这么做
from math import *

# 如此缩写模块名字
import math as m
math.sqrt(16) == m.sqrt(16) # => True

# Python模块其实就是普通的Python文件。你可以自己写，然后导入，
# 模块的名字就是文件的名字。

# 你可以这样列出一个模块里所有的值
import math
dir(math)
```



## **高级用法**

```python
# 用生成器(generators)方便地写惰性运算
def double_numbers(iterable):
  for i in iterable:
    yield i + i

# 生成器只有在需要时才计算下一个值。它们每一次循环只生成一个值，而不是把所有的值全部算好。
# range的返回值也是一个生成器，不然一个1到900000000的列表会花很多时间和内存。
# 如果你想用一个Python的关键字当作变量名，可以加一个下划线来区分。
range_ = range(1, 900000000)
# 当找到一个 >=30 的结果就会停
# 这意味着 `double_numbers` 不会生成大于30的数。
for i in double_numbers(range_):
  print(i)
  if i >= 30:
    break

    
    
    
    
    
    
    
    
   
# 装饰器(decorators)

def now():
  print('2015-3-25')
  
f = now
now.__name_ # => "now"
f.__name_ # => "now"

# 现在，假设我们要增强now()函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。

# 本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

def log(func): # 接受函数作为参数
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper # 并返回一个函数

# 我们要借助Python的@语法，把decorator置于函数的定义处：
@log
def now():
    print('2015-3-25')
    
# 调用now()函数，不仅会运行now()函数本身，还会在运行now()函数前打印一行日志：   
now() 
# output:
# call now():
# 2015-3-25

# 把@log放到now()函数的定义处，相当于执行了语句：now = log(now)
# 由于log()是一个decorator，返回一个函数，所以，原来的now()函数仍然存在，只是现在同名的now变量指向了新的函数，于是调用now()将执行新函数，即在log()函数中返回的wrapper()函数。
# wrapper()函数的参数定义是(*args, **kw)，因此，wrapper()函数可以接受任意参数的调用。在wrapper()函数内，首先打印日志，再紧接着调用原始函数。

# 如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__)) 
            return func(*args, **kw)
        return wrapper
    return decorator
  
@log('execute') 
def now():
    print('2015-3-25')
now()    # now = log('execute')(now)
# 先执行 log ('execute')
# return decorator(now) 并 调用函数
# return wrapper() 并 调用函数 但这个时候

# 以上两种decorator的定义都没有问题，但还差最后一步。因为我们讲了函数也是对象，它有__name__等属性，但你去看经过decorator装饰之后的函数，它们的__name__已经从原来的'now'变成了'wrapper'：now.__name__ = 'wrapper', 因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。

# 不需要编写wrapper.__name__ = func.__name__这样的代码，Python内置的functools.wraps就是干这个事的，所以，一个完整的decorator的写法如下：@functools.wraps(func)

#原函数改为：
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
  
# or 

def log(text):
    def decorator(func):
        @functools.wraps(func) # 等于加上 wrapper.__name__ = func.__name__
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator


# 这个例子中，beg装饰say
# beg会先调用say。如果返回的say_please为真，beg会改变返回的字符串。
from functools import wraps

def beg(target_function):
   @wraps(target_function)
   def wrapper(*args, **kwargs): # **kwargs 是可以导入任意属性 
       msg, say_please = target_function(*args, **kwargs)
       if say_please:
          return "{} {}".format(msg, "Please! I am poor :(")
       return msg
   return wrapper

@beg
def say(say_please=False):
    msg = "Can you buy me a beer?"
    return msg, say_please

print(say()) # Can you buy me a beer?
print(say(say_please=True)) # Can you buy me a beer? Please! I am poor :(
```

