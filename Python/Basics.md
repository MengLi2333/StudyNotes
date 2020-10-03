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

- set的快速生成

  ```python
  s1 = {i*j for i in range(3) if i % 2 == 0 for j in range(3) if j % 2 == 1}
  ```

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

- dict的快速生成

  - `dict2 = {k:v for k,v in dict1.items() if v % 2 == 0}`

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
  2. 默认参数
  3. 一个`*`的收集参数
  4. 两个`*`的收集参数

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

  - `setitem`方法

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