# Profile

- 静态语言&脚本语言
  - 静态语言
    - 源代码通过编译器编译成计算机可执行的二进制码(机器码)
    - 静态语言是强类型语言: 变量的数据类型一旦确定就不可改变
  - 动态语言(脚本语言)
    - 运行时由解释器逐条解释逐条执行
    - 动态语言是弱类型语言: 变量的数据类型可以改变
- Python默认采用UTF-8编码
- 万物皆对象
  - Python中的所有变量都是对象, 没有基本数据类型
  - 其中number, str为不可变对象
- Python程序的内存结构
  - Python内存结构大致由heap和stack构成
  - heap中存储对象
  - stack中存储对象的引用(变量实际的值)
- 续行符(在行尾): `\`
- Python中的范围通常都是: `[范围1, 范围2)`
- *在不特别指定时, 本笔记所说的方法与函数不做区别*

# 变量

## 变量的声明

- 变量的声明以及初始化是同时进行的

- 声明的三种形式

  ```python
  a1 = "hello world"        #第一种
  a2 = a3 = "hello world"   #第二种
  a4, a5 = "hello", "world" #第三种, 其实质是tuple的解构
  ```

  

- 变量名的组成
  - 变量名可由数字、字母、下划线和汉字（不推荐）构成
  - 变量名不能以数字开头
- 命名规范
  - 类名使用大驼峰
  - 变量名, 方法名使用全小写 + 下划线分割

## 变量的类型

### number

- Python中数字变量没有限定大小
- int
  - 二进制: 0b
  - 八进制: 0o
  - 十六进制: 0x
  - 科学计数法: x.xxex
- float
- bool
  - True
  - False
- complex
  - i + xj

### str

- 包裹字符串的引号

  - 单引号
  - 双引号
  - 三引号: 可用于储存多段文字信息

- 字符串的运算

  - +
  - *

- 字符串的索引和切片

  - `str[索引]`
  - `str[索引1:索引2]`
  - `str[索引1:索引2:步长]`
  - 索引允许超出范围

- 字符串的赋值是传址操作, 切片不是

- 字符串的%格式化

  - %s 字符串
  - %d 整数
  - %f 浮点数
  - %% 输出%号
  - %e 指数

- 字符串的format格式化

  - `{}`中不加数字: format中参数顺序填充

  - `{}`中加数字: format中参数按数字顺序填充(其余`{}`也需要加数字)

  - dict解包

    ```python
    d = {'a': 1, 'b': 2}
    str = "{a}{a}{b}".format(**d)
    ```

  

### list

- 列表的索引和切片

  - 操作与字符串的索引和切片一致

  - list的元素可以通过索引和切片语法修改

    ```python
    L = [1, 2, 3]
    L[2] = "三"
    L[0:2] = "一", "二"
    ```

    

### tuple

- 元组可以理解为不能改变内容的list
- 当元组只有一个元素时, 需要在末尾打一个逗号, 有多个元素时可打可不打

### set

- set的内容无序(元素没有索引), 内容无重复

- set的运算

  - `-`: 表示差集

- set的元素必须是hashable(可哈希的)

### dict

- dict的键必须是hashable

- dict的增加/修改

  - `dict[key] = value`

- dict的查找

  - `dict[key]`
  - 没有值时报错

- dict的删除

  - `del(dict[key])`

- dict的遍历

  ```python
  for k in dict:
  	print(k, dict[k])
  for k in dict.keys():
      print(k, dict[k])
  for v in dict.values():
      print(v)
  for k,v in dict.items():
      print(k, v)
  ```


### None

- 类似于Java中的null

### 解包

- 表达式中的解包
  - 如: `a, b, c = (1, 2, 3)`
- 函数调用的解包
  - 对列表、元组、字符串：用一个星号
  - 对字典：用一个星号时解包出的是键，用两个星号解包出的是关键字参数(key = value)

## 变量作用域

- 全局变量

  - 在函数体外定义的变量都是全局变量

  - 在方法体内定义全局变量

    ```python
    def func():
      global a       # 提升为全局变量
      a = 123        # 赋值必不可少
      return None
    ```

    

- 局部变量

  - 在函数体(类和对象的方法)内定义的变量

- 可见性

  - 内部可以访问外部变量(全局变量, 以及在本函数体外部的局部变量)
  - 当内部声明了与外部变量同名的变量, 则外部变量不可见

# 运算符

- 算数运算符
  - `+, -, *, **, /, //, %`
  - Python中没有自增和自减
- 比较运算符
  - `>, >=, <, <=, ==, !=`
  - Python中`==`为值比对, `is`为地址比对
- 赋值运算符
  - `=, +=, -=, *=, /=, //=, %=, **=`
- 逻辑运算符
  - `and, or, not`
- 成员运算符
  - `in, not in`
- 身份运算符
  - `is, is not`

# 程序结构

- 顺序结构

- 分支结构

  - Python中的分支结构只有if, 没有switch

  ```python
  if 条件表达式:
      语句1
      语句2
      # ...
  ```

- 循环结构

  - for

    - Python中的for循环只有for-each

    ```python
    for 变量 in 序列:
        语句1
        语句2
        # ...
    ```
  
  - while
  
    ```python
    while 条件表达式:
        语句1
        语句2
        # ...
    ```
  
  - break, continue

# 函数

- 函数的定义

  - 函数在定义时不需要指定返回值类型
  - 函数的参数列表不需要指定参数的类型

  ```python
  def <函数名>(参数列表):    #注意小括号不能省略
      语句1
      语句2
      # ...
  ```

- return

  - 函数中可以有return, 也可以没有
  - 没有return时函数返回None
  - 当函数需要返回多个值时, 通常的处理方式是将这些值组成一个元组返回

## 函数的参数

- 普通参数(位置参数)

  - 调用函数时必须在普通参数定义的位置传入实参, 未传入则报错

  ```python
  def func(a, b):
      # 函数体
  ```

  

- 默认参数

  - 给形参一个默认值, 在调用函数时可以不用传入实参

  ```python
  def func(a, b, c=100):
      # 函数体
  ```

- 关键字参数

  - 调用函数时通过指定参数的名字来指定该实参是对应的哪个形参

  ```python
  def func(a, b):
      print(a)
      print(b)
  if __name__ == '__main__':
      func(b=1, a=2)
  ```

- 收集参数

  - 收集参数类似于c中的不定参数, 一个参数可以收集多个实参
  - 在参数名前加一个或两个`*`表示其为收集参数
    - 一个`*`放在元组中
    - 两个`*`放在字典中

  ```python
  def func(*args):
      # 函数体
  def func2(**kwargs):
      # 函数体
  ```

- 参数排列顺序

  1. 普通参数(关键字参数)
  3. 一个`*`的收集参数
  4. 两个`*`的收集参数
  
  - 其中默认参数可在除了普通参数之前的任何位置

# 类与对象

## 方法

- 对象方法

  - 对象方法中必须有一个self参数(指向对象本身), 且这个参数必须在参数列表的最前面, 且方法在被调用时self参数会被自动传入
  - 对象方法的self参数可以访问对象属性

  ```python
  class A:
      def func(self, a):
          print(a)
          
  if __name__ == '__main__':
      a = A()
      a.func("I'm param a")
  ```

- 特殊的对象方法

  - `__init__`方法

    - init方法类似于Java中的构造方法, 在对象实例化时自动调用

    ```python
    class A:
        def __init__(self, name):
            self.name = name
        
        def func(self):
            print(self.name)
    
    if __name__ == '__main__':
        a = A('MengLi2333')
        a.func()
    ```

  - `__new__`方法

    - new方法是对象真正的构造器, 在对象实例化时, new方法被自动调用, 并返回被创建的对象
    - new方法一般不需要自己定义

  - `__del__`方法

    - 当对象被gc回收时自动调用

  - `__call__`方法

  - `__int__`方法

    - 在对象被`int()`包裹的时候会被执行

  - `__str__`方法

    - 当对象被`str()`包裹的时候会被执行

  - `__add__`方法

    - 在两个对象相加的时候，调用第一个对象的add方法，将第二个对象传递进来
    - add方法需要两个参数, 第一个是self, 第二个是与之相加的对象

    ```python
    class A:
        def __init__(self, num):
            self.num = num
            
        def __add__(self, obj):
            return self.num + obj.num
        
    if __name__ == '__main__':
        a = A(1)
        b = A(2)
        print(a + b)
    ```

  - `__dict__`方法

    - dict方法在对象中有, 在类中也有, 通常不需要自己定义
    - dict方法以字典的形式列出类或对象中的所有成员

    ```python
    class A:
        pass
    
    if __name__ == '__main__':
        a = A()
        print(a.__dict__)
        print(A.__dict__)
    ```

  - `__getitem__`方法

    - 当遇到`对象[key]`或者`对象[key1:key2]`语法时调用

  - `__setitem__`方法

    - 当遇到`对象[key] = value`或`对象[key1:key2] = value1, value2, ...`语法时调用

  - `__delitem__`方法

    - 当遇到`del 对象[key]`或`del 对象[key1:key2]`语法时调用

  - `__iter__`方法

    - 定义了iter方法的类对象是Iterable的, 可被for-each遍历

- 静态方法

  - 静态方法需要用装饰器`@staticmethod`修饰, 并且不需要self参数(如果在定义时在参数列表中加入self参数, 该参数也会被当做普通参数处理)
  - 静态方法通过类或对象来调用

  ```python
  class A:
      @staticmethod
      def func():
          print("I'm static method")
          
  if __name__ == '__main__':
      A.func()
  ```

- 类方法

  - 类方法需要用装饰器`@classmethod`修饰, 需要self参数(指向本类)
  - 类方法的self参数只能访问类属性

## 属性

- 类属性

  - 在类体中定义的属性为类属性
  - 类属性可以通过类方法访问

  ```python
  class A:
      name = 'class attribute'
      @classmethod
      def func(self):
          print(self.name)
          
  if __name__ == '__main__':
      A.func()
  ```

- 特殊的类属性

  - `__metaclass__`
    - Python中的类也是对象, 在Python2.2之后类默认都是type类型的对象, 由type类进行实例化
    - `__metaclass__`属性可以指定该类由哪个类进行实例化

- 属性方法

  - 使用装饰器`@property`修饰的方法为属性方法
  - 属性方法为对象的属性, 调用时不需要加`()`

  ```python
  class A:
      def __init__(self, name):
          self.name = name
      
      @property
      def func(self):
          print(self.name)
          
  if __name__ == '__main__':
      a = A('MengLi2333')
      a.func
  ```

- 对象的属性

  - 对象的属性由init方法间接定义
  - 对象中所有的属性默认为public, 可以通过对象直接访问
  - 在属性名前加`__`的为private属性, 只能通过对象方法访问
  - Python中没有protected或者default属性

  ```python
  class A:
      def __init__(self):
          self.username = 'username' # public属性
          self.__password = 'password' # private属性
          
      def func(self):
          return self.__password
  
  if __name__ == '__main__':
      a = A()
      print(a.username)
      print(a.__password) # 报错
      print(a.func())
  ```

  

## 类的继承

- Python允许多继承

- super
  - `super()`: 获取MRO(MethodResolutionOrder)列表中的第一个父类
  - super().xxx 只能用来调用方法，不能用来属性

```python
class A(B): # A类继承B类
    # 类体
```

## isinstance和issubclass

- isinstance判断一个对象是否为一个类的实例
- issubclass判断一个类是否为另一个类的子类

# 模块与包

## 模块

- 定义
  
  - 模块是一个包含Python代码的文件(.py)
  
- 命名空间
  - 每个模块都具有自己的命名空间
  - 在使用模块中的内容时需要在内容前注明其所属命名空间
  
- 模块中可以包括的内容
  - 函数
  - 类
  - 测试代码
  
- 模块名

  - 模块名就是文件名

- import
  - `import <模块名>`: 引入模块, 通过`<模块名>.<内容>`使用模块
  - `import <模块名> as <别名>`: 引入模块, 通过`<别名>.<内容>`使用模块
  - `from <模块名> import <某内容>`: 只引入模块的某个内容, 引入后该内容进入当前命名空间
  
- `if __name__ == '__main__'`
  - 引入模块相当于在解释语句时将该模块的代码全部解释一遍, 因此除了声明代码, 非声明的代码(如调用print函数打印一句话)也会被解释, 而这通常是我们不希望的
  - 可使用`if __name__ == '__main__'`来判断正在解释的模块是否为主模块, 若不是则不执行模块中非声明的代码
  
- import非法命名的模块

  - 因为模块其本身是一个文件, 因此命名只需要遵循操作系统的命名规范, 这就有可能出现模块名在Python的命名规范中非法的情况

  - import此类模块可以借助importlib包

    ```python
    import importlib
    # import 233 非法的模块名 as my_module
    my_module = importlib.import_module('233 非法的模块名')
    ```

- 模块的搜索路径与加载顺序

  - 模块的搜索路径

    - 模块的搜索路径在sys模块中可以获取

    ```python
    import sys
    
    print(sys.path) # 查看模块的搜索路径
    sys.path.append('路径') # 添加新的搜索路径
    ```

    

  - 模块的加载顺序

    1. 内存中已经加载好的
    2. Python内置模块
    3. 搜索sys.path中的路径

## 包

- 定义
  - 包是存放模块的文件夹
- 包可以嵌套
- `__init__.py`
  - 每个包中都必须包含`__init__.py`文件(只有当文件夹中存在`__init__.py`时才会被认为是一个包)
  - 当`import <包名>`时, 只会执行该包中的`__init__.py`
  - 因此通常通过在`__init__.py`中添加该包中的子包和模块的导入代码来达到简化导入的目的
- `__all__`
  - 在进行`from <包> import *`时, 若在该包的`__init__.py`中定义了`__all__`, 则导入会根据`__all__`的内容来进行

## import总结

- 当import的主体是包时
  - `import <包>`
    - 执行该包中的`__init__.py`
  - `from <包> import *`
    - 根据`__init__.py`中的`__all__`来import
  - `from <包> import <模块1>, <模块2>, ...`
- 当import的主体是模块时
  - `import <包>.<模块> [as <别名>]`
  - `from <模块> import *`
  - `from <模块> import <内容1>, <内容2>, ...`
- 当import的主体是模块中的某个内容时
  - `import <包>.<模块>.<内容>`

# 文件

- open

  - `open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)`

  - 其中file可以是字符串

  - mode

    >```
    >========= ===============================================================
    >Character Meaning
    >--------- ---------------------------------------------------------------
    >'r'       open for reading (default)
    >'w'       open for writing, truncating the file first 注：会覆盖以前的内容，且当该文件不存在时，自动新建一个文件
    >'x'       create a new file and open it for writing 注：当文件存在时，会报错
    >'a'       open for writing, appending to the end of the file if it exists 注：且当该文件不存在时，自动新建一个文件
    >'b'       binary mode
    >'t'       text mode (default)
    >'+'       open a disk file for updating (reading and writing)
    >'U'       universal newline mode (deprecated)
    >========= ===============================================================
    >```

  - open函数在打开文件后会返回一个stream对象(根据不同的mode返回的对象不同), stream对象在使用完毕后需要close

- TextIO

  - 当调用open函数, 并且传入的mode为非binary读取时, 得到的返回值为TextIO对象
  - `read(self, size=-1, /)`
  - `readline(self, size=-1, /)`
  - `readlines(self, hint=-1, /)`
  - `write(self, text, /)`
  - `writelines(self, lines, /)`
  - `close(self, /)`
  - encoding
    - Encoding of the text stream

- with语句

  - with语句可以帮助管理open函数返回的stream对象(在使用完毕后自动close, 类似于Java的try-with-resource)

  ```python
  with open('文件路径', 'r') as f: # 将open的返回值赋值给f
      # 操作...
  # 当with中的语句块执行完毕后自动关闭stream对象
  ```

# 序列化

- pickle模块
  - `dump(obj, file, protocol=None, *, fix_imports=True)`
  - `load(file, *, fix_imports=True, encoding='ASCII', errors='strict')`

- shelve模块

  - shelve存储的是键值对

  - `open(filename, flag='c', protocol=None, writeback=False)`

    - flag
      - c: 读写
      - r: 只读
      - w: 只写
    - writeback
      - 当writeback为True时, 通过shelve读入的对象在被修改后会自动写回
      - 前提是open时允许写操作, 且写回时文件未关闭

  - shelve.open的返回值

    - 返回值的读写操作和dict的操作相同
    - 读取时key若不存在, 则抛出KeyError异常

  - shelve并发

    - shelve不支持对一个文件并发写入
    - 但是支持对一个文件并发读取

  - 案例

    ```python
    import shelve
    
    if __name__ == '__main__':
        with shelve.open('文件路径') as db:
        	db['one'] = 1
        	db['two'] = 2
        with shelve.open('文件路径') as db:
            print(db['one'])
            print(db['two'])
    ```

# 异常/警告

## 异常

- 命名
  - Python中的异常并不是绝大多数都以Exception结尾
  - 多数异常以Exception/Error结尾, 还有一些异常类并没有一个特定的结尾
- BaseException
  - 所有异常的基类
- Exception
  - 常规错误的基类

## 警告

- 命名
  
- 多数异常以Warning结尾
  
- Warning
  
- 所有警告的基类
  
- 警告不能通过try-catch捕捉, 且打印Warning后也不会使程序终止

- 抑制警告的方法

  ```python
  import warnings
  
  warnings.filterwarnings("ignore")
  ```

# 多线程

## threading.Thread

- threading.Thread
  - `__init__(self, group=None, target=None, name=None,args=(), kwargs=None, *, daemon=None)`
    - target
      - 指定多线程调用的函数对象
    - args/kwargs/*
      - 函数的参数
  - `run(self)`
  - `start(self)`
  - `join(self, timeout=None)`
  - `getName(self)`
  - `setName(self, name)`
  - `is_alive(self)`
  - `isDaemon(self)`
  - `setDaemon(self, daemonic)`
  - daemon
  - name

## 线程同步

- threading.Lock
  - threading.Lock为不可重入锁
  - `acquire(blocking=True, timeout=-1) -> bool`
  - `release()`
  - `locked() -> bool`
    - test whether the lock is currently locked
- threading.RLock
  - threading.RLock为可重入锁
  - RLock的操作与Lock相同
- threading.Semaphore
  - threading.Semaphore允许至多n个线程处于运行状态
  - `__init__(self, value=1)`
    - value: 允许至多value个线程处于运行状态
  - `acquire(self, blocking=True, timeout=None)`
  - `release(self)`

## multiprocessing

- multiprocessing不是多线程, 而是为主进程创建一个新的子进程

- multiprocessing.Process

  - `__init__(self, group=None, target=None, name=None, args=(), kwargs={}, *, daemon=None)`
  - `run(self)`
  - `start(self)`
  - `terminate(self)`
  - `is_alive(self)`
  - `join(self, timeout=None)`

  - daemon
  - exitcode
    - Return exit code of process or `None` if it has yet to stop

# 函数式编程

## 常用的高级函数

- `map(func, *iterables) --> map object`
  - func可以有一个参数, 为这一次的遍历的值
  - map的返回值map object是一个可迭代对象(iterable), 可以直接传给list等容器的构造器

- `functools.reduce(function, sequence[, initial]) -> value`
  - reduce函数在functools模块中
  - function可以有两个参数, 第一个为上一次的结果, 第二次参数为这一次的遍历的值
  - 当reduce没有传入initial时, 会将序列的前两个值作为第一次调用function的值

- `filter(function or None, iterable) --> filter object`
  - function可以有一个参数, 为这一次的遍历的值
  - filter object是一个可迭代对象

- `sorted(iterable, /, *, key=None, reverse=False)`

  - key解决比较大小的标准(将比较大小的对象转为某个类型后再比较)
    - 如`key=int`, `key=abs`

- `functools.partial(func, *args, **keywords)`

  - 当函数参数过多, 需简化时, 使用`functools.partial()`可以创建一个新的函数, 这个函数可以固定住原函数的部分参数
  - 这个新的函数被称为偏函数

  ```python
  import functools
  
  def func(a, b):
      return a ** b
  
  if __name__ == "__main__":
      func2 = functools.partial(func, b=2)
      print(func2(5)) # 只需要传入a的值, b的值已经固定为2
  ```

  

## 装饰器

- 装饰器可以在不修改原函数的情况下, 为函数增加功能

- 装饰器其实就是一个高级函数, 其输入和输出都是一个函数

- 案例

  ```python
  def my_decorator(f):
      def decorate(*args, **kwargs):
          print(233)
          return f(*args, **kwargs)
      return decorate
  
  @my_decorator
  def func(i):
      return i**2
  
  if __name__ == "__main__":
      print(func(5))
  ```


# Iterable/Iterator

- 可迭代对象
  - 可以使用for遍历
  - `iter(iterable) -> iterator`
    - 获得可迭代对象的迭代器

- 迭代器

  - 迭代器一定是可迭代对象

  - `next(iterator[, default])`

    >Return the next item from the iterator. If default is given and the iterator is exhausted, it is returned instead of raising StopIteration.

# Generator生成器

- Generator的本质是Iterator

- 时间换空间

  - Generator每次获得的下一个值都是在被调用时通过特点算法获得的, 不会存储以前的值, 也不会提前计算并存储后面的值
  - 使用Generator替代容器, 可以使容器O(n)的空间复杂度降为O(1)

- 自定义Generator

  - 使用yield
  - 当Generator执行到return语句(或语句执行完毕)时, 抛出StopIteration异常

- Generator简化写法

  - `(<返回值> <for语句> <for语句2> ...)`

  - 得到的等价Generator的定义为

    ```python
    def gen():
        for语句1:
            for语句2:
                ...:
                    yield 返回值
    ```

  - 如`(str(i)+' x '+str(j)+' = '+str(i*j) for i in range(1, 10) for j in range(i, 10))`的等价Generator的定义为

    ```python
    def gen():
        for i in range(1, 10):
            for j in range(i, 10):
                yield str(i)+' x '+str(j)+' = '+str(i*j)
    ```

- list生成器

  - `[<返回值> <for语句> <for语句2> ...]`
  - 得到的list等价于`list((<返回值> <for语句> <for语句2> ...))`

- set生成器

  - `{<返回值> <for语句> <for语句2> ...}`

- dict生成器

  - `{<key>: <value> for <key> in...}`
  - 由于dict的定义(不能存在值相同的key), 当key和value都有一次循环时, 得到的效果为只取内层循环的键值对(前面n-1个key相同的键值对被覆盖)

- Coroutine协程
  
  - 协程借助yield语句可以使函数暂停的特性, 可以将一个线程运行出多个线程的效果