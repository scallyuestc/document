### python特性

关键特性：

- python是一种解释性语言，python不需要在运行之前进行编译，属于一边运行一边解释。
- python是动态类型化的，这意味着当你声明变量时，你不需要声明变量的类型。
- python适合面向对象编程，因为它允许定义类以及组合和继承
- 在python中函数是一等对象，这意味着它们可以在运行时动态创建，能赋值给变量或者作为参数传给函数，还能作为函数的返回值。
- python代码容易上手，开发速度快，但是运行速度比编译语言慢。但是python允许基于c的扩展，所以瓶颈可以被优化掉

### 赋值、深拷贝、浅拷贝

对象的赋值：python中的对象的赋值实际上是简单的对象引用，也就是说你创建一个对象，然后把它复制给另一个变量的时候，python并没有拷贝这个对象，而是拷贝了这个对象的引用。

![img](https://pic2.zhimg.com/80/v2-0bce9cb95526ed48e33cc3ae7ead430b_hd.jpg)



浅拷贝：有三种方式；一般使用copy.copy(),可以进行对象的浅拷贝.它复制了对象但对于对象中的元素，依然使用原始的引用.

![img](https://pic3.zhimg.com/80/v2-e8f484f3aab8f86058c69c83f0e9097d_hd.jpg)

深拷贝：深度拷贝需要用copy.deepcopy()进行深拷贝。它会复制一个容器对象，以及它里面的所有元素。![img](https://pic1.zhimg.com/80/v2-f258a7a2d0dcb6284e384079a3ac2355_hd.jpg)



### python中is和==的区别

is是判读对象标识符是否一致，而==是判读两个对象的内容是否相等！

x is y 相当于 id(x)==id(y)

==是检查两个对象的内容是否相等，会调用对象的内部__eq__().

### python中多线程问题

​	python有一个多线程包threading，可以使用多线程来加快你的代码。但是python有一个叫做（Global Interpreter Lock）（GIL)的构造。GIL确保只有一个“线程”可以在任何时候执行。

​	线程获取GIL，做一些工作，然后将GIL传递到下一个线程。这种情况发生的非常快，所以对于人眼而言，它可能看起来像你的线程并行执行，但它实际上只是轮流使用相同的CPU内核。因此GIL的存在使得python中多线程无法真正利用多核的优势来提高性能。

​	对于IO密集型操作，在等待操作系统返回的时候会释放GIL；再比如爬虫因为有等待的服务器的响应时间，可以利用多线程来加速！但是对于CPU密集型操作，只能通过多进程Multiprocess来加速。

### python中的猴子补丁

考虑下面例子：

![img](https://pic1.zhimg.com/80/v2-99bbd5e9cfe4a75928af3ae7eeed20a1_hd.jpg)

猴子补丁：是一种非常pythonic的用法，即函数在python中可以像使用变量一样对它进行赋值等操作，我们可以在运行时动态替换模块。例如：

![img](https://pic2.zhimg.com/80/v2-2337aeca7ff616dc70bda176a62558c1_hd.jpg)



### python中new和init区别

说说__new__和__init__的区别：

​	__init__为初始化方法，而__new__方法才是是真正的构造函数。只有继承了object的新式类才有__new__.

​	_new_至少要有一个参数cls，代表要实例化的类，此参数在实例化时由Python解释器自动提供，__new__必须要有返回值，返回实例化出来的实例。

__init__有一个参数self，就是这个__new__返回的实例， 先运行__new__ 然后才运行__init__.

__init__在__new__的基础上可以完成一些其它初始化的动作，__init__不需要返回值.

### python中*args和**kwargs

可变参数：可变参数就是传入的参数个数是可变的，可以是0个，1个，2个，……很多个。这些可变参数在函数调用时自动组装为一个tuple。



*args是可变参数，一般用来表示我们不能确定多少参数将被传递给函数，或者如果我们想用列表或元组的方式传递给函数.

![img](https://pic1.zhimg.com/80/v2-d615fbc8763c63f4b4de2866cae96247_hd.jpg)





可变关键字参数：而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。在调用函数时，可以只传入必选参数。含有参数名。

![img](https://pic1.zhimg.com/80/v2-a338597767e1c569b7308a5ac253202b_hd.jpg)

### import循环

from import和直接import区别：from import 创建到模块内部对象的引用，它的前提是import要成功，否则会报错；import创建到模块本身的引用，如果出现循环引用，import进来的模块可能暂时是空的。

```python
A.py
import B
a = B.a
```



```python
B.py
import A
print A.a
a = 123
```

先运行文件A：

- 首次导入，A先进入sys模块的module列表

- 进入A之后开始执行，import B 这句代码

- B是首次导入，进入sys的module列表

- 从B的第一句开始执行，import A，由于A已经在module表中，略过，

  然后执行print A.a，这时候A.py还没有执行到给a赋值的地方，所以异常

解决方法：

- 把import语句写在方法或函数里面，将他的作用限制在局部
- 将from xxx import yyy该成import xxx形式
- 代码布局有问题