在此记录一些Python中比较常用到的方法技巧（持续更新）

&emsp;

# 生成器
## 通过列表生成式构建一个生成器

&emsp;

```python
a = [x * x for x in range(10)]    # 列表生成式子生成一个list
print(a)
# 打印：
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

g = (x * x for x in range(10))    # 列表生成式生成一个生成器，只需将[]替换为()
print(g)
# 打印：
# <generator object <genexpr> at 0x1022ef630>
```

&emsp;

## 编写一个简单的生成器

&emsp;

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b    # 经过迭代操作后生成一个迭代值
        a, b = b, a + b
        n = n + 1
    return 'done'  # 如果已无迭代值，则输出'done'
    
f1 = fib(10)

# 最后一个迭代值出来后，将停止迭代，跳出for语句
for i in f1:
    print(i)
# 打印: 
# 1 1 2 3 5 8 13 21 34 55
	    
f2 = fib(10)

# 如果想单个迭代，可使用next函数
print(next(f2))
# 打印：
# 1

print(next(f2))    
# 打印：
# 1

print(next(f2))    
# 打印：
# 2

# 这里因为每次fib被next调用时都会生成一个临时的生成器对象，所以输出只会打印第一次的迭代值“1”。
print(next(fib(10)))
print(next(fib(10)))
print(next(fib(10)))
```

&emsp;

# map/reduce

&emsp;

## map

&emsp;

```python
def f(i):
	return i * i

x = [1, 2, 3, 4, 5, 6, 7, 8]

# map()函数接收两个参数，一个是函数，一个是Iterable。map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回
y = map(f, x)

# 需将Iterator转换为list才能打印出里面的元素
print(list(y))
# 打印：
# [1, 4, 9, 16, 25, 36, 49, 64]
```

&emsp;

## reduce

&emsp;

```python
# reduce包含在functools包中，使用时需导入
from functools import reduce    

def fn(x, y):
    return x * 10 + y

def str2int(x):
    strDict = {
        "0": 0,
        "1": 1,
        "2": 2,
        "3": 3,
        "4": 4,
        "5": 5,
        "6": 6,
        "7": 7,
        "8": 8,
        "9": 9,
    }
    return strDict[x]

# reduce接受两个参数，一个是执行函数（需2个参数），一个是Iterable，其大概运行逻辑是：将Iterable按顺序分别传入执行函数中进行操作，然后将返回值执行函数的一个参数后继续与Iterable后面的元素进行操作，直到Iterable没有元素可迭代为止。
ans = reduce(fn, map(str2int, "13579"))
print(ans)
# 打印：
# 13579
```

&emsp;

# filter

&emsp;

```python
def is_odd(n):
    return n % 2 == 1

# 和map()类似，filter()也接收一个函数和一个序列。不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。filter返回值是一个Iterator，不方便打印其中的元素，所以将其转换为一个Iterable
print(list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15])))
# 打印：
# [1, 5, 9, 15]
```

&emsp;

# 装饰器

&emsp;

```python
from functools import wraps

# 一、装饰器的基本语法

def log(f):
    # 如果需要打印真正执行操作的函数名而不是wrapper函数名，则需要导入functools里的wraps
    @wraps(f)
    
    # 如果需要添加装饰器的函数含有参数，则wrapper也需要填入参数（*args, **kw）。
    def wrapper(*args, **kw):
        print('call %s():' % f.__name__)
        return f(*args, **kw)
    return wrapper

# 这个函数用来筛出序列中的偶数，我们如果要针对这个操作插入一些测试/信息说明的话可以使用装饰器来实现。
@log
def func(nums: list) -> list:
    return list(filter(lambda x: x % 2 == 0, nums))

print(func.__name__)
print(func([x for x in range(10)]))
# 打印：
# call func():
# [0, 2, 4, 6, 8]

# 二、带参数的装饰器语法

# 如果装饰器想要接受参数，则需要额外嵌套一层函数。
def log2(text):
    def decorator(f):
        @wraps(f)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, f.__name__))
            return f(*args, **kw)
        return wrapper
    return decorator

@log2('execute')
def func(nums: list) -> list:
    return list(filter(lambda x: x % 2 == 0, nums))

print(func.__name__)
print(func([x for x in range(10)]))
# 打印：
# func
# execute func():
# [0, 2, 4, 6, 8]
```

&emsp;

# 偏函数

&emsp;

```python
from functools import partial

# 当函数的参数个数太多，需要简化时，使用functools.partial可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。

# 普通写法：
# def int2(x, base=2)
#     return int(x, base)

# 使用partial
int2 = partial(int, base=2)
print(int2('10010'))
# 打印：
# 18
```

&emsp;

# \_\_slots\_\_

&emsp;

```python
# python的灵活性可以使类中的属性能够随意添加，为了保证代码的工整和已读可在类中添加一个__slots__变量
class Student:
    __slots__ = ('name', 'age')
    

s = Student()

s.name = 'Sam'
s.age = 16
print(s.name, s.age)
# 打印：
# Sam 16

s.university = "CMU"
print(s.university)
# 报出AttributeError异常，提示Student类中没有"university"属性，也无法设置新属性
```

&emsp;

# @property

&emsp;

```python
# python中对于类的属性getter/setter方法可用@property来代替，可读性更高。
class Student(object):
    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

	# age属性没有设置setter，所以如果对其赋值会报错
    @property
    def age(self):
        return 2026 - self._birth

```

&emsp;

# 枚举类

&emsp;

```python
from enum import Enum

# 构建一个枚举类后，可以直接使用Month.Jan......来引用一个常量，也可以像字典一样遍历它们（需访问__members__）
Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))

for k, v in Month.__members__.items():
    print(k, v.value)
# 打印（value属性则是自动赋给成员的int常量，默认从1开始计数）：
# Jan 1
# Feb 2
# Mar 3
# Apr 4
# May 5
# Jun 6
# Jul 7
# Aug 8
# Sep 9
# Oct 10
# Nov 11
# Dec 12

print(Month.Jan)
# 打印:
# Month.Jan

print(Month['Feb'])
# 打印：
# Month.Feb

print(Month.Mar.value)
# 打印：
# 3

print(Month['Feb'] == Month.Feb)
# 打印：
# True
```

&emsp;

# StringIO和BytesIO

&emsp;

```Python
from io import StringIO, BytesIO

# 一、StringIO

f = StringIO()

# 向内存中写入值
f.write("Hello, World !")
print(f.getvalue())
# 打印：
# Hello, World !

# StringIO可设置初始值
f = StringIO("Hello!\nHi!\nGoodbye!")
while True:
    # readline遇到换行符停止读取
    s = f.readline()
    if s == '':
        break
    print(s.strip())
    # 打印：
    # Hello!
    # Hi!
    # Goodbye!
    
# 二、BytesIO

b = BytesIO()
b.write('中文'.encode('utf-8'))
print(b.getvalue())
# 打印：
# b'\xe4\xb8\xad\xe6\x96\x87'
```

&emsp;

# JSON

&emsp;

```python
# 如果我们要在不同的编程语言之间传递对象，就必须把对象序列化为标准格式，比如XML。但更好的方法是序列化为JSON，因为JSON表示出来就是一个字符串，可以被所有语言读取，也可以方便地存储到磁盘或者通过网络传输。JSON不仅是标准格式，并且比XML更快，而且可以直接在Web页面中读取，非常方便。

import json

# 一、序列化

d = dict(name="Bob", age=20, score=88)
print(json.dumps(d))
# 打印（输出字符串）：
# {"name": "Bob", "age": 20, "score": 88}


# 类的序列化需要自定义一个转换函数，否则json无法解析类的序列化操作，反序列化下同。
class Student(object):
    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score

# 类的序列化函数
def student2dict(stu: Student):
    return {"name": stu.name, "age": stu.age, "score": stu.score}


stu = Student("Sam", 20, 88)
print(json.dumps(stu, default=student2dict))
# 打印（输出字符串）：
# {"name": "Sam", "age": 20, "score": 88}

# 因为通常class的实例都有一个__dict__属性，它就是一个dict，用来存储实例变量。也有少数例外，比如定义了__slots__的class。
print(json.dumps(stu, default=lambda x: x.__dict__))
# 打印（输出字符串）：
# {"name": "Sam", "age": 20, "score": 88}

# 二、反序列化

# 注意在Python中进行反序列化操作时，反序列化字符串（即JSON对象）中的成员必须是双引号
d = json.loads('{"name": "Bob", "age": 20, "score": 88}')
print(d)
# 打印（输出字典）：
# {'name': 'Bob', 'age': 20, 'score': 88}

# 类的反序列化函数
def dict2Student(d: dict[str, any]):
    return Student(d["name"], d["age"], d["score"])

stu_dict = json.loads('{"name": "Bob", "age": 20, "score": 88}')
stu = dict2Student(stu_dict)
print(stu.name, stu.name, stu.score)
# 打印：
# Bob Bob 88
```

&emsp;