# 普通知识

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

# 列表有切割语法
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


# 不可变对象

# str是不变对象，而list是可变对象。

```



**Error**

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



**Dict**

```python
# Python提供一个叫做可迭代(iterable)的基本抽象。一个可迭代对象是可以被当作序列
# 的对象。比如说上面range返回的对象就是可迭代的。

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
```



**Function**

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

map(add_10, [1, 2, 3]) # => [11, 12, 13]
# map()接收一个函数 f 和一个 list, 并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 list 并返回

# reduce()函数接收的参数和 map()类似，一个函数 f，一个list
# 但行为和 map()不同，reduce()传入的函数f必须接收两个参数，python3不用了

filter(lambda x: x > 5, [3, 4, 5, 6, 7]) # => [6, 7]
# filter()函数接收一个函数 f 和一个list, 这个函数f的作用是对每个元素进行判断，返回 True或 False
# filter()根据判断结果自动过滤掉不符合条件的元素，返回由符合条件元素组成的新list。

sorted(iterable, key = None, reverse = False)
# sort 与 sorted 区别：
# sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
# list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。
# key – 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
# reverse – 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。
sorted([5, 2, 4, 3, 1]) #=> [1, 2, 3, 4, 5]
sorted({5:'D', 2 : 'A' , 4 : 'D', 3 : 'C', 1: 'D'}) #=> [1, 2, 3, 4, 5] 


# 用列表推导式可以简化映射和过滤。列表推导式的返回值是另一个列表。
[add_10(i) for i in [1, 2, 3]] # => [11, 12, 13]
[x for x in [3, 4, 5, 6, 7] if x > 5] # => [6, 7]

# 函数对象有一个__name__属性，可以拿到函数的名字：

def now():
  print('2015-3-25')
  
f = now
now.__name_ # => "now"
f.__name_ # => "now"

```



# 类

```python
# 定义一个继承object的类
class Human(object):
    # 类属性，被所有此类的实例共用。实例 instance 实例是根据类创建出来的一个个具体的 “对象” object。
    species = "H. sapiens"

    # 构造方法，当实例被初始化时被调用。
    # 注意名字前后的双下划线，这是表明这个属性或方法对Python有特殊意义，但是允许用户自行定义。
    # 你自己取名时不应该用这种格式。
    def __init__(self, name):
        # Assign the argument to the instance's name attribute
        #__init__ 方法的第一参数永远是 self，用来表示类创建的实例本身, self.name 是实例的属性, = name 是外部赋值
        self.name = name

    # 实例方法 instance function，第一个参数总是self，就是这个实例对象
    def say(self, msg):
        return "{name}: {message}".format(name=self.name, message=msg) # 可以在方法内加新属性

    # 类方法，被所有此类的实例共用。第一个参数是这个类对象。
    # @classmethod
    def get_species(cls):
        return cls.species # species = "H. sapiens"

    # 静态方法。调用时没有实例或类的绑定。
    # @staticmethod
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

## _init_() 是初始化 给类定义自己的属性变量 self这里指代类Student
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
```



**模块**

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



**高级用法**

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

