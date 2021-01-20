# 第1章 Web请求过程

- B/S架构统一性带来的优点
  - 从用户层面来说, B/S架构统一了用户获得服务的方式, 不管时访问什么网站, 都是通过浏览器来进行, 减小了用户获取服务的成本(不用下载新的客户端, 不用学习新软件的使用)
  - 从服务提供者层面来说, B/S采用了统一的HTTP协议, 对于不同的请求有很多相同的处理流程, 增强了代码的可复用性, 催生出各种开发框架, 减少了开发成本
- 浏览器资源缓存机制
  - 为了加快网页的访问, 浏览器会将上一次获得的资源缓存起来, 当下一次再次访问相同资源时直接返回缓存的资源
  - 缓存控制
    - 在响应头中有多个key可以控制浏览器的缓存: Pragma, Cache-Control, Expires, Last-Modified, Etag
    - 若有多个控制缓存的key出现, 则根据优先级Pragma > Cache-Control > Expires
    - 当Pragma/Cache-Control的value为no-cache时表示禁用缓存
      - 除此之外, 请求头中也可以添加Pragma和Cache-Control, 当其值为no-cache时表示告知代理服务器不使用缓存, 而是直接向原服务器请求资源
    - Expires指定缓存过期的时间
    - Last-Modified/Etag指定当前资源最后修改的时间或是编号, 在请求资源前浏览器会先向服务器询问缓存的资源是否过期, 若过期则重新请求

- DNS
  
  - DNS解析大致过程
    
    <img src="https://s3.ax1x.com/2020/11/19/DKi5SU.png" alt="DNS" style="zoom:50%;" />
  
- CDN
  - ![CDN](https://s3.ax1x.com/2020/11/19/DKkLRK.png)
  - 采用了CDN架构后的B/S架构, 其在域名解析时与传统的域名解析不同
  - 在图中第5步, 传统DNS解析就已经得到了IP地址, 而CDN架构中得到的是CNAME(另一个域名), 然后再通过6, 7步解析得到CNAME的IP地址(根据负载均衡会解析得到不同的IP), 再由LocalDNS将该IP地址返给用户, 用户根据IP访问指定的CDN节点

# 第2章 Java I/O的工作机制

## 文件I/O

- 用户地址空间&内核地址空间
  - OS为了保护系统本身的运行安全, 将内核程序运行使用的内存空间和用户程序运行使用的内存空间进行隔离
  - 用户程序如果需要访问内核空间的数据, 需要调用操作系统的接口, 让操作系统将内核空间中的数据复制到用户空间, 或将用户空间的数据复制到内核空间
  - 用户空间和内核空间的分配
    - 在Windows系统中, 默认的分配比例是内核空间:用户空间 = 1:1​
    - 在Linux系统中, 默认的分配比例是内核空间:用户空间 = 1:3

- 软件操作本地文件
  - 在标准访问文件的方式中, 软件不直接操作文件, 而是通过获取和修改内核空间中由操作系统读取并缓存的文件信息(高速缓存)来达到操作文件的目的
    - 即, 软件 <-> 内核空间的高速缓存 <-> 文件
  - 用户软件和可以选择直接绕过OS的高速缓存, 直接操作文件
    - 即, 软件 <-> 文件
- File类
  - 在不必要的情况下, File只是通过操作字符串(path属性)来达到目的
    - 如: 获得文件名, 获得父路径
  - 在真正需要对文件进行修改或者获取信息时, File是通过其内部的FileSystem对象来完成的
    - 如: 获得文件全路径, 删除文件
  - FileSystem&FileDescriptor
    - FileSystem只能对文件的整体进行操作, 如删除文件
    - FileDescriptor用于操作文件的内容, 如获得/修改文件内容, FileInputStream/FileOutStream就是使用FileDescriptor来操作文件的

## 网络I/O

- 网络I/O
  - 当Socket建立成功时, OS会在用户空间为InputStream和OutputStream分配一定大小的缓存区(InputStream对应RecvQ, OutputStream对应SendQ), 数据的读取和写入都是通过这个缓存区完成的
  - Java的io包中的流对象会直接向缓存区输入和读取, 而nio包下的Channel通过Buffer操作缓存区
  - 网络I/O的流程为: 软件 <-> Socket缓存区 <-> 硬件 <-> Internet
- TCP状态转化
  - <img src="https://s3.ax1x.com/2020/11/22/DGAn29.jpg" alt="TCP" style="zoom: 67%;" />
- 响应时间
  - 将一份数据从一个地方传输到另一个地方所需的时间成为响应时间
  - 影响响应时间的因素有: 网络带宽, 传输距离, TCP拥塞控制
  - TCP拥塞控制
    - TCP需要传输方和接收方的步调一致, 这就需要TCP拥塞控制来调节
    - TCP在传输时会设定一个窗口(BDP, Bandwidth Delay Product), BDP = 带宽(b/s) * RTT(s), RTT(Round-Trip Time)是数据在两端来回的时间
- BIO带来的问题
  - BIO(Blocking I/O)在数据的写出时若对方的RecvQ被填满会使线程阻塞, 在数据的读入时若本方的SendQ为空也会使线程阻塞, 这使得一个线程难以应付大规模的访问
  - 常见的异步解决方案是为每一个客户端开启一个线程来处理, 但是会遇到以下问题: 
    - 线程的创建和销毁成本很高
      - 在Linux这样的操作系统中，线程本质上就是一个进程。创建和销毁都是重量级的系统函数。
    - 线程本身占用较大内存
      - 像Java的线程栈，一般至少分配512K～1M的空间
    - 线程的切换成本很高
      - 操作系统发生线程切换的时候，需要保留线程的上下文，然后执行系统调用。
      - 如果线程数过高，可能执行线程切换的时间甚至会大于线程执行的时间
      - 这时候带来的表现往往是系统load偏高、CPU sy使用率特别高（超过20%以上)，导致系统几乎陷入不可用的状态
    - 容易造成锯齿状的系统负载
      - 一旦线程数量高但外部网络环境不是很稳定，就很容易造成大量请求的结果同时返回，激活大量阻塞线程从而使系统负载压力过大
- NIO
  - NIO在JDK1.4时被引入
  - NIO(Non-blocking I/O)通过Selector轮询Channel的方式消除了阻塞等待的问题, 因此可以在一个线程中处理批量的请求

# 第3章 Java Web中的中文编码问题

## 常见编码

- <img src="https://s3.ax1x.com/2020/12/01/DhVuRI.png" alt="DhVuRI.png" style="zoom: 50%;" />
- ISO-8859-1
  - ISO-8859-1是对ASCII的一个拓展
  - 支持256个字符
  - 一个字符大小: 1个字节
- GB2312
  - GB2312是对ASCII的一个中文拓展
  - 包括6763个汉字
  - 一个字符大小: 2个字节
- GBK
  - GBK是对GB2312的拓展, 编码兼容GB2312
  - 支持21886个汉字和图形符号
  - 包括BIG5(繁体中文常用编码)中的全部汉字
  - 一个字符大小: 2个字节
- GB18030
  - GB18030兼容GBK
  - 包括70244个汉字
  - GB18030是变长字符集, 一个字符长度可以是: 1, 2, 4个字节
- UTF-8
  - UTF-8和UTF-16定义了Unicode字符在计算机中的存储方法
  - UTF-8是可变字符集, 一个字符长度为: 1-6个字节, 其中汉字为3个字节
- UTF-16
  - UTF-16是不可变字符集, 一个字符长度: 2个字节

## 编码的选择

- GBK
  - 在GB2312和GBK中, 应选择GBK
  - 由于GBK和GB2312在编解码时需要查码表, 因此效率低于UTF-8和UTF-16
- UTF-16
  - 适合在本地磁盘和内存间使用
    - UTF-16由于是定长, 所以编解码速度较高, 
  - 不合适用于网络传输
    - UTF-16的字节流若在网络传输时丢失了一部分, 则会影响其后面的所有编码
    - UTF-16中只需要单字节编码的字符依然占用两个字节, 增加了网络传输时的数据量
- UTF-8
  - 合适用于网络传输
    - UTF-8采用变长存储字符, 相同的字符序列占用更小的空间
    - 当UTF-8的字节流在网络传输时丢失了一部分, 不会影响其后面的编码
  - 在本地磁盘和内存间使用时不如UTF-16
    - UTF-8由于是变长, 所以编码速度不如UTF-16

## Java Web中常涉及的编解码

- 在HTTP请求中涉及的编解码
  - URL的编解码
  - 请求实体的编解码
- 在HTTP响应中涉及的编解码
  - 返回的HTML文件的编解码
  - 返回的HTML文件外部引入的JS文件的编解码
  - 返回的JSON等其他基于字符的数据的编解码

### URL的编解码

- URL组成
  - 例: `http://host:port/contextPath/servlets/servlet/pathInfo?key=value&key1=value1`
  - 第一部分: `http://host:port/`
    - 此部分通常不涉及编解码问题
  - 第二部分: `contextPath/servlets/servlet/pathInfo`
    - 该部分为`request.getRequestURI()`获得的部分
  - 第三部分: `?key=value&key1=value1`
    - 该部分为queryString
- 浏览器对URL的编码
  - 根据RFC3986标准, 浏览器对URL的编码是将非ASCII字符按照某些编码格式转换成16进制数字后再将每个16进制表示的字节前面加上"%"
  - 在URL中, 常需要转换(出现非ASCII码)的地方是上述的第二部分和queryString, 且两者的编码格式可以不同
- Tomcat对URL的解码
  - 对上述URL的第二部分的解码
    - 在Tomcat的server.xml中的`<Connector URIEncoding="UTF-8"/>`可以配置第二部分的解码, 若没有配置`URIEncoding`属性, 则默认通过ISO-8859-1进行解码
  - 对queryString的解码
    - 对queryString的解码是在`request.getParameter()`第一次被调用时进行的
    - 在配置了`<Connector useBodyEncodingForURI="true">`后, queryString的解码字符集采用请求头中`ContentType`指定的字符集, 注意`useBodyEncodingForURI`这个属性只决定queryString的字符集, 而不是整个URI的字符集
    - 若没有指定queryString的编码, 则默认采用ISO-8859-1

### Header的编解码

- 请求头的解码是在`request.getHeader()`第一次被调用时进行的
- Header的编解码字符集是ISO-8859-1, 一般不能设置其为其他编码

### 请求体的编解码

- 与queryString相同, 请求体(POST表单数据)的编解码也是在`request.getParameter()`第一次被调用时进行
- 请求体的编解码会根据请求头的`ContentType`指定的字符集进行编解码, 通常不易出现乱码问题
- 在`request.getParameter()`第一次被调用前调用`request.setCharacterEncoding(charset)`可以控制请求体的编码字符集, 但是注意这可能会影响到queryString的编码

### 响应体的编解码

- 服务端通过`response.setCharacterEncoding(charset)`来指定响应头的`Content-Type`, 浏览器通过`Content-Type`指定的字符集来解码响应体
- 若没有指定`Content-Type`, 则浏览器通过其默认的字符集来解码
- 对于HTML而言, 若没有指定`Content-Type`, 但其`<meta content="text/html; charset=UTF-8">`指定了charset, 则根据该charset来解码该HTML文件

## JS中常见编码问题

### 外部引入JS文件的编解码

- 在前端开发中, 常遇到HTML外部引入JS文件
  - 如: `<script src="statics/main.js" charset="UTF-8"></script>`
- 该外部引入的JS文件的解码由script标签指定, 若script没有charset属性, 则该JS文件的解码字符集为当前HTML页面的默认字符集

### JS的URL编码

- 在使用JS的异步访问时, 通常需要JS自己处理URL, 会涉及到URL的编解码
- `encodeURI`/`decodeURI`函数
  - `encodeURI`和`decodeURI`可以对URL中非ASCII部分进行编解码
  - 其效果与浏览器对URL的编解码相同
- `encodeURIComponent`/`decodeURIComponent`函数
  - `encodeURIComponent`对URL的编码比`encodeURI`更彻底, 被`encodeURIComponent`编码后的URL不能被识别为一个URL(其`:`, `\`等URL必要的符号都会被编码)
  - 其一般用于将一个URL编码后作为另一个URL的参数

# 第4章 Javac编译原理

- Java和JVM
  - Java语言和JVM执行的字节码文件都有各自的语法规范
  - 通过Javac将.java文件转为.class文件的过程其实就是Java语法转为JVM语法的过程
  - Javac就将Java和Java的执行解了耦
- 不止Java
  - 因为JVM执行的是.class文件, 则不止是Java语言, 其他语言只要也能通过一定方法将其语法规范转为JVM的语法规范(即编译生成.class文件), 则这些语言也可以在JVM上运行
  - 这些语言包括但不限于: groove, scale, jython
- 词法分析器
  - 词法分析器依次扫描Java源码, 根据Java语法生成一个Token流
- 语法分析器
  - 语法分析器根据生成的Token流生成一棵语法树
- 语义分析器
  - 语义分析器将语法分析器生成的语法树做进一步处理, 得到一棵更接近字节码的语法树
- 代码生成器
  - 代码生成器将会遍历语义分析器处理后的语法树, 生成字节码
  - JVM的操作都是基于栈的, 所有的Java操作都是通过出栈和入栈来实现的

## 语义分析器常见的处理

- 给没有定义构造函数的类创建一个默认的构造函数

- 常量折叠

  - 如`String str = "hello" + "world"`会被处理为: `String str = "helloworld"`

- 去掉永远为假的代码

  ```java
  // 处理前:
  if (false) {
      System.out.println("false");
  } else {
      System.out.println("true")
  }
  // 处理后:
  { // 注意作用域会被保留
      System.out.println("true");
  }
  ```

- 将变量进行必要的装箱和拆箱

- 将foreach转化为for循环

- 将assert断言转为一条判断语句

- 将内部类转为一个外部类并做相关处理

# 第5章 class文件结构

- 常量池

- 方法中
  - 用4个字节表示方法的代码长度(最大4GB)
  - 用2个字节表示方法的起始行(最长65535行)
- 类中
  - 用2个字节表示属性个数

# 第6章 ClassLoader工作机制

## ClassLoader等级结构

- ClassLoader的等级加载机制
  - ClassLoader在加载类时, 会逐级委托父级ClassLoader来加载该类, 只有当所有的父级, 父级的父级...的ClassLoader都没有加载该类时, 才会轮到本ClassLoader来加载该类

- <img src="https://s3.ax1x.com/2020/12/13/re2uZj.png" alt="re2uZj.png" style="zoom:50%;" />
- 其中BootstrpClassLoader, ExtClassLoader, AppClassLoader为JDK中定义的ClassLoader, 会在JVM启动时自动被加载
- BootstrpClassLoader
  - 负责加载JVM自身工作所需要的类
  - 其没有父ClassLoader, 也没有子ClassLoader
- ExtClassLoader
  - 在JDK9及以后被废除, 由PlatformClassLoader取代
  - 负责加载`System.getProperty("java.ext.dirs")`目录下的类
- AppClassLoader
  - 其父ClassLoader为ExtClassLoader(或PlatformClassLoader)
  - 负责加载`System.getProperty("java.class.path")`, 也就是`classpath`目录下的类
  - 调用`ClassLoader.getSystemClassLoader()`时一般情况下会得到AppClassLoader, 该方法在ClassLoader进行无参构造时作为默认的父ClassLoader
- 用户自定义ClassLoader
  - 其父类一般选用AppClassLoader, 或在有多层自定义ClassLoader时逐级延伸至AppClassLoader

## loadClass

- ClassLoader类在加载类时涉及的主要方法

  - `Class<?> loadClass(String, boolean)`
  - `Class<?> findClass(String)`
  - `Class<?> defineClass(byte[])`
    - `defineClass`在`findClass`内部调用
  - `void resolveClass(Class<?>)`

- `loadClass`方法体

  ```java
  Class<?> loadClass(String name, boolean resolve) {
      Class<?> c = null;
      if (已经加载了该类) {
          c = 已经加载了的该类;
      }
      if (有父ClassLoader) {
          c = 父ClassLoader.loadClass(name, false);
      } else {
          c = findBootstrapClassOrNull(name);
      }
      if (c == null) { // 父ClassLoader和BootstrapClassLoader都没有加载到
          c = findClass(name); // 自己来加载该类
      }
      if (resolve) {
          resolveClass(c);
      }
      return c;
  }
  ```

- `findClass`方法体

  ```java
  Class<?> findClass(String name) {
      byte[] 字节码流 = 得到字节码流(name);
      Class<?> c = defineClass(字节码流); // 通过字节码流生成Class对象
      return c;
  }
  ```

## 类加载流程

- .class -> findClass+defineClass -> Linking -> 静态属性初始化赋值 -> Class对象
- Linking过程包括:
  - 字节码验证
  - Class类数据结构分析及相应的内存分配
  - 符号表链接
- 静态属性初始化赋值的执行时机
  - 使用ClassLoader.loadClass加载类时
    - 延后到需要使用该类时由JVM自动执行
  - 使用Class.forName加载类时
    - 加载后立即执行

## 类加载方式

- 隐式加载
  - 当一个被加载后的类中引用了其他没有被加载的类时, 这些类会被隐式地加载进JVM
- 显式加载
  - 使用ClassLoader.loadClass或Class.forName加载

# 第7章 JVM体系结构与工作方式

## JVM的基本结构

- 类加载器
  - 在JVM启动时或者在类运行时将需要的class加载到JVM中
- 执行引擎
  - 执行class文件中的字节码指令
  - 每个线程都有一个独立的执行引擎实例
- 内存区
  - 方法区
    - 存储类的结构信息, 包括类所属字节码文件的常量池, 域, 方法数据, 方法体, 构造方法等
    - 方法区实际存储在Java堆的永久区中
    - 一般情况下, 当JVM运行一段时间后, 方法区的大小就基本固定了(需要加载的类都加载进JVM了), 但是要注意如果项目中有对类的动态编译, 则方法区可能会持续增大
  - Java堆
    - 存储实例化对象
  - Java栈
    - 每个线程都有一个独立的Java栈
  - 本地方法栈
    - 本地方法(以及JIT生成的本地代码)的执行所需要的栈
  - PC寄存器
    - 每个线程都有一个独立的PC寄存器
    - PC寄存器存储了该线程下一条需要执行的字节码指令的地址
  - 运行时常量池
    - 运行时常量池就是方法区中的常量池
- 本地方法调用
- <img src="https://s3.ax1x.com/2020/12/17/r8BrDK.png" alt="JVM结构图示" style="zoom: 33%;" />

- Java中的栈帧
  - 每一个栈帧都对应一次方法调用
  - 栈帧中存储了本次方法执行所需要的局部变量表、操作栈(执行引擎在执行字节码指令时需要使用的栈), 以及方法返回值等信息
  - 局部变量表和操作栈的大小在Java源码的编译时就已确定, 在栈帧的创建时一次性分配完毕

## JIT技术

- JIT(Just in time)技术, 即即使编译技术, 是为了提高代码的执行效率而出现的

- JVM在运行时会记录方法(或语句块)的执行次数, 当执行次数超过一定的阈值后, 就会使用JIT编译器将该方法(或语句块)编译为本地代码
- 当程序再次执行这个方法(或语句块)时, 就不需要解释执行, 而是直接执行本地代码

# 第8章 JVM内存管理

## 计算机内存相关概念

- 物理内存
  - 物理内存就是RAM(随机存储器)
  - 用户程序通常通过调用操作系统的接口来访问内存
  - 每个进程都拥有一段独立的内存空间, 且每个进程只能访问自己的内存空间
- 寄存器
  - 除了RAM, 计算机中的另一个存储单元是寄存器
  - 寄存器用于存储计算单元执行指令的中间结果
- 地址总线
  - 地址总线的宽度决定了处理器一次可以从寄存器或内存中获取多少个bit
  - 地址总线的宽度也决定了处理器最大可以寻址的地址空间
    - 如: 32位地址总线可以寻址的地址访问为0x0000 0000~0xffff ffff, 即可以支持4GB的内存空间
- 虚拟内存
  - 虚拟内存用于提高内存利用率和扩展内存空间
  - 虚拟内存可以映射到一段物理内存、文件或者其他可以寻址的存储上
  - 虚拟内存映射到文件
    - 一个进程在不活动的情况下, 操作系统其物理内存中的数据移到一个磁盘文件中, 将高效的物理内存留给正在活动的进程使用
    - 当重新唤醒该进程时, 操作系统将会把磁盘中的数据重新读到物理内存中

- 用户地址&内核地址
  - 详见本笔记第2章

## JVM堆外内存

- 在Java1.4添加的NIO中, 提供了Java分配堆外内存的方法: `ByteBuffer::allocateDirect()`
- 通过`ByteBuffer::allocateDirect()`方法得到的对象, 其承载byte数据的那部分内存在JVM的Java堆之外, 也被称作NIO Direct Memory
- 堆外内存的释放
  - 只有当`ByteBuffer::allocateDirect()`方法得到的对象被GC清理时, 其占用的堆外内存才会被释放

## Garbage Collection

- 可回收对象的检测
  - 从堆的根对象集合向下遍历, 不能被达到的对象就是可回收对象
  - 根对象集合所包含的对象
    - 根对象集合与JVM的具体实现有关, 但是大多会包含以下对象:
    - 方法的局部变量表中引用的对象
    - 方法的操作栈中引用的对象
    - 常量池中引用的对象
    - 本地方法中持有的对象
    - Class对象

### 基于分代的垃圾回收机制

- 分代垃圾回收机制将对象划分为三代, 将堆划分为三个区域:
  - 年轻代的对象存放在Young区
    - 其中年轻区又分为Eden区、Survivor1区、Survivor2区
    - 通常Young区占总堆大小的1/4, 两个Survivor区一共占Young区的1/4
  - 年老代的对象存放在Old区
  - 持久代的对象存放在Perm区
- 垃圾回收基本思路
  - 新创建的对象放在Eden区
  - 当Eden区满了, 则触发MinorGC, 将Eden区和存放了对象的Survivor区的可回收对象回收后, 将这两个区的存活对象放入另一个没有存放对象的Survivor区
  - 并且将经历了一定次数的MinorGC后仍然存活的对象放入Old区, 或当Survivor区满了时将一部分对象放入Old区
  - 当Old区满了, 则触发FullGC, 清理Young区、Old区、Perm区中的所有可回收对象
- Perm区
  - Perm区中存储了方法区的内容, 以及Class对象

- 具体的垃圾回收器及其算法
  - Serial Collector
  - Parallel Collector
    - ParNewGC
    - ParallelGC
    - ParallelOldGC
  - CMS Collector

# 第9章 Servlet工作原理

- Servlet与Servlet容器
  - Servlet与Servlet容器是独立发展的, 将他们沟通起来的是标准化接口
- Tomcat容器模型
  - Tomcat的容器分为4个等级, 真正管理Servlet的容器是Context容器
  - <img src="https://s3.ax1x.com/2020/12/22/rsS3Bn.png" alt="rsS3Bn.png" style="zoom:50%;" />
  - Tomcat中一个Context对应一个Web应用
  - 每个Context可以有多个Wrapper, 一个Wrapper对应一个Servlet(Wrapper包装了Servlet)
- Tomcat中的Web应用初始化
  - 一个Web应用的初始化工作是在`ContextConfig::configureStart()`中完成的
  - 该方法首先会查找并解析web.xml, 并将其中的信息保存在WebXml对象中
  - web.xml的搜索范围及次序:
    - Tomcat会先找globalWebXml, 搜索路径是engine的工作目录下的`org/apache/catalina/startup/NO_DEFAULT_XML`或Tomcat根目录下的`conf/web.xml`
    - 然后是寻找hostWebXml, 搜索路径是`System.getProperty("catalina.base")/conf/${EngineName}/${HostName}/web.xml.default`
    - 最后是Web应用的配置文件, 搜索路径是Tomcat根目录下的`webapps/${虚拟项目名}/WEB-INF/web.xml`
  - 接下来会调用`WebXml::configureContext`方法, 将WebXml对象中的属性设置到Context容器中, 并创建Servlet(的包装)、Listener、Filter等
- Tomcat中Servlet对象的创建
  - Servlet对象创建时机
    - 当Servlet配置的load-on-startup的值大于0时, Servlet对象会在Context容器启动时创建
    - 否则, 会在该Servlet第一次被访问时创建
  - Servlet的创建是由`Wrapper.loadServlet()`完成的, 该方法会先获取Servlet的Class对象, 并将其交给InstanceManager创建一个基于该Class对象的对象
- Tomcat中Servlet对象的初始化
  - Servlet对象的初始化由`StandardWrapper::initServlet()`方法完成
  - 该方法会先调用其包装的Servlet对象的`init`方法, 再将包装了StandardWrapper的StandardWrapperFacade对象作为ServletConfig对象传给Servlet对象
- Servlet体系结构
  - 与Servlet直接关联的是ServletConfig、ServletRequest、ServletResponse, 而ServletContext可由ServletConfig获得
  - 在Tomcat容器中上述四个类的具体实现类
    - ServletConfig的具体实现类是Tomcat的StandardWrapperFacade对象, 该对象包装了Tomcat的StandardWrapper
    - ServletContext的具体实现类是ApplicationContextFacade对象, 该对象包装了ApplicationContext
    - ServletRequest的具体实现类是RequestFacade对象, 该对象包装了org.apache.catalina.connector.Request对象
    - ServletResponse的具体实现类是ResponseFacade对象, 该对象包装了org.apache.catalina.connector.Response对象

- url-pattern匹配规则
  - url-pattern有三种匹配
    - 精确匹配: 如`/foo.html`, 只会匹配一个URL
    - 路径匹配: 如`/foo/*`, 会匹配以foo为前缀的所有URL
    - 后缀匹配: 如`*.js`, 会匹配所有以.js为后缀的URL
    - 而其他的写法如`/foo/`, `/foo/*.js`等都是不对的
  - 若一个URL符合多个Servlet的匹配规则, 则会按以下规则进行匹配
    - 若Servlet中有精准匹配通过的, 则让该Servlet处理该请求
    - 若没有精准匹配通过的, 则按照"最长路径匹配"原则, 选择处理该请求的Servlet
      - 如Servlet1的url-pattern为`/foo/*`, Servlet2的url-pattern为`/*`, 则选择Servlet1来处理请求

# 第10章 Session与Cookie

## Cookie

- Cookie版本

  - Cookie有两个版本: Version 0和Version 1
  - 设置不同版本的Cookie, 其响应头的Key不同: 
    - Version 0是`Set-Cookie: ...`
    - Version 1是`Set-Cookie2: ...`

- Cookie Version 0 属性项

  | 属性项       | 描述                                                |
  | ------------ | --------------------------------------------------- |
  | NAME = VALUE | 键值对, 需要保存的信息, 其键不能与其他属性项重复    |
  | Expires      | 过期时间                                            |
  | Domain       | 在访问什么域名时需要携带该Cookie                    |
  | Path         | 在访问什么路径时需要携带该Cookie                    |
  | Secure       | 如果设置了该属性, 则只会在SSH连接时才会携带该Cookie |

- Cookie Version 1 属性项

  | 属性项       | 描述                                                    |
  | ------------ | ------------------------------------------------------- |
  | NAME = VALUE | 同Version 0                                             |
  | Version      | 取值为0或1, 默认为0, 当为1时该Cookie需要符合RFC2109规范 |
  | Comment      | 注释项, 对该Cookie的描述                                |
  | CommentURL   | 服务器为此Cookie提供的URI注释                           |
  | Discard      | 是否在会话结束后丢弃该Cookie, 默认为false               |
  | Domain       | 同Version 0                                             |
  | Max-Age      | 最大失效时间, 表示多少秒后失效                          |
  | Path         | 同Version 0                                             |
  | Port         | 在访问什么端口时需要携带该Cookie, 多个端口逗号隔开      |
  | Secure       | 同Version 0                                             |

- Cookie版本实际的使用

  - Java的Servlet规范中不支持`Set-Cookie2`的响应头
  - 在实际应用中, 浏览器支持在`Set-Cookie`的响应头下使用Cookie Version 1的属性项

## Session

- Session的识别
  - Session是通过从客户端传来的Session唯一标识来识别的
  - 这个唯一标识可以来自: 
    - URL Path Parameter
    - Cookie
    - SSL
- URL Path Parameter
  - 当浏览器不支持Cookie功能时, 会将Session的标识放到URL中
  - 其格式如: `/path/Servlet;name=value;name2=value2?Name3=value3`
  - 其中`Servlet;`后面的键值对就是要传递的Path Parameters, 服务器会从这些Path Parameters中拿到Session标识(键为SessionCookieName的键值对的值)
- Cookie
  - 大多数情况下Session的标识都存放在Cookie中
  - 其标识为键为SessionCookieName的键值对的值
- SessionCookieName
  - 上述的SessionCookieName为web.xml中配置的值(在`<session-config>`下配置)
  - 若没有配置则默认为`JSESSIONID`
- Session的管理
  - 所有的Session都保存在`org.apache.catalina.Manager`的sessions容器中, 由Manager统一管理
  - 当tomcat关闭时, StandardManager(Manager接口的实现类)会将Session持久化到SESSIONS.ser文件中, 当tomcat启动时会将持久化的Session重新读到sessions中

# 第11章 Tomcat系统架构

## 总体架构

- <img src="https://s3.ax1x.com/2021/01/18/s6vcK1.png" alt="Tomcat总体架构图" style="zoom: 80%;" />

- Server
  - Server中可以有多个Service
  - 整个Tomcat的生命周期由Server管理
  - Tomcat中StandardServer实现了Server接口、Lifecycle接口(用于管理子组件的生命周期)、MbeanRegistration接口
- Service
  - Service封装并管理Connector与Container, 一个Service可以有多个Connector, 但能只有一个Container
  - 除了Connector与Container, Service下还有其他的组件(如Jasper), 这些组件用于为Connector和Container提供必要的服务
  - Tomcat中StandardService类实现了Service接口, 以及Lifecycle接口
- Connector组件
  - Connector组件接收请求, 并将请求封装为Request和Response对象
  - 一个Service中可以有多个具体的Connector
- Container组件
  - Container组件根据Connector组件封装的Request和Response对象处理请求
  - Container组件不是真实存在的组件, 其职责由Engine、Host、Context、Wrapper四个组件来承担

## Lifecycle接口

- 生命周期管理
  - Tomcat中组件的生命周期通过Lifecycle接口来控制
  - 只要组件继承了Lifecycle接口, 就可以被其父组件统一管理生命周期
  - 最上层的组件就是Server
- 观测者设计模式
  - Lifecycle接口管理组件生命周期的一个重要意义就是为了能够在组件的状态(如BEFORE_START_EVENT, STOP_EVENT)发生改变时通知观察者, 以便做一些额外的事情
  - 其中发布者为继承了Lifecycle的类, 观察者为继承了LifecycleListener的类

## Container组件

- Engine
  - Container组件中的顶层容器

- Host
  - 一个Host在Engine中代表一个虚拟主机, 负责安装、运行Web应用
- Context
  - Context具备了Servlet运行的基本环境, 这个环境的配置是在Context的start方法中进行的
  - Context中可以包含并管理多个Wrapper对象
  - 热部署
    - Context中有一个reloadable属性, 该属性为true时, war被修改后Tomcat会自动重新加载这个Web应用
    - 这个功能是在StandardContext的backgroundProcess方法中实现的, 由ContainerBase类中定义的内部类ContrainerBackgroundProcessor周期性调用

- Wrapper
  - Container组件中的最底层容器
  - 一个Wrapper管理一个Servlet, 包括Servlet的装载、初始化、执行、资源回收

# 第12章 Jetty工作原理解析

pass

# 第13章 Spring框架设计理念

- Spring核心组件
  - Core、Context、Bean
  - Spring的其余组件都是基于这三个组件的扩展
- Bean组件的主要职责
  - 定义Bean的接口和实现类
    - Bean封装了开发者配置的对象
  - 定义BeanFactory的接口和实现类
    - BeanFactory用于创建Bean对象
  - 定义`<bean>`节点的解析类
- Context组件的主要职责
  - 定义Ioc容器的接口和实现类
    - Ioc容器的顶级接口是ApplicationContext, 其继承了Bean组件中的BeanFactory接口
- Core组件的主要职责
  - 为Context组件提供支持(如提供ResourceLoader便于加载资源)
- FactoryBean
  - 在ApplicationContext获取Bean对象时, 一部分Bean是由FactoryBean的getObject方法生成的
  - 通过重写FactoryBean的getObject方法, 可以在生成Bean对象时做一些额外的事情(如Spring就是通过重写getObject方法, 返回一个代理类来实现其AOP特性的)

# 第14章 SpringMVC工作机制

- SpringMVC的核心组件
  - HandleMapping
    - 定义了用户设置的请求映射关系, 将URL映射成Handle对象
  - HandleAdapter
    - 根据Handle对象的类型定义不同的处理规则
  - ViewResolver
    - 将View渲染成页面
- SpringMVC的其他组件
  - MultipartResolver
    - 处理文件上传服务
    - 若该次请求有文件上传, 则将HttpServletRequest包装成DefaultMultipartHttpServletRequest, 将每个上传的内容封装成CommonsMultipartFile
  - HandlerExceptionResolver
    - 当Handle处理出错时, 进行统一处理
  - LocaleResolve
    - 处理应用的国际化问题
  - ThemeResolve

- SpringMVC的初始化
  - SpringMVC的初始化是从DispatcherServlet的init方法开始的
  - 在init方法中会创建WebApplicationContext, 并初始化SpringMVC的组件