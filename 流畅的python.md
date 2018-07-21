# 流畅的python

### 列表推导

推导式是python的一种独有特性。推导式是可以从一个数据构建另一个新的数据序列的结构体。共有三种推导：列表推导式、字典推导式、集合推导式。

​	生成器表达式是更好地选择：生成器表达式可以逐个地产出元素，而不是建立一个完整地列表，然后再把这个列表传递到某个构造函数里。生成器表达式能够节省内存。

### 元祖

元祖拆包：

```python
l =(33,45)
lattitude, longitude = l  # 元祖拆包（含有位置信息）
```

### 切片

为什么切片和区间会忽略最后一个元素？

- 可以快速看出切片中有几个元素，例如range(3)有三个元素。
- 当起止位置信息都可见时，我们可以快速计算出切片和区间的长度，用后一个数减去第一个下标（stop-start）即可
- 可以利用任意一个下标把序列分割成不重叠的两部分，例如list[:3]与list[3:]

### 序列+和*

​	python默认支持+和*操作的。通常+号两侧的序列由相同类型的数据所构成，在拼接过程中，两个被操作的序列都不会被修改，python会新建一个包含同样类型数据的序列作为拼接的结果。

​	如果想要把一个序列复制积分然后拼接起来，快捷做法用*，会构造一个新序列。注意：在a**n中序列a里的元素是对其他可变对象的引用的话，n个引用指向同一个元素a。对于可变元素应该用列表推导

### sort

list.sort方法会就地排序列表，也就是说不会把原列表复制一份，这也是这个方法的返回值是None的原因。sorted会新建一个列表作为返回值。

### 散列

python里所有的不可变类型都是可散列的？不对，比如元祖本身是不可变序列，它里面的元素可能是其他可变类型的引用。

### 集合set

集合中的元素必须是可散列的，set类型本身是不可散列的

### 高阶函数

接受函数为参数，或者把函数作为结果返回的函数是高阶函数

### 匿名函数

lambda 关键字在python表达式内创建匿名函数

### 可调用对象

用户自定义的函数：使用def语句或lambda表达式创建

内置函数：使用c语言实现的函数，如len或time.strftime

内置方法：使用C语言实现的方法，如dict.get

方法：在类的定义体中定义的函数

生成器函数：使用yield关键字的函数或方法。调用生成器函数返回的是生成器对象。

### 闭包

​	闭包指延伸了作用域的函数，其中包含函数定义体中引用、但是不在定义提中定义的非全局变量。他能访问定义提之外定义的非全局变量。

​	假如有个名为avg的函数，它的作用是计算不断增加的系列值的均值；假如，整个历史中某个商品的平均收盘价，每天都会增加新价格，因此，平均值要考虑截止到目前为止所有的价格。

第一种：使用类

```python
class Average():
	def __init__(self):
		self.series = []
	def __call__(self, new_value):
		self.series.append(new_value)
		total = sum(self.series)
		return total/len(self.series)
```

Average的实例是可调用对象：

```python
avg = Averagr()
avg(10)
avg(11)
avg(12)
```

第二种：使用高阶函数make_averager

```python
def make_average():
	series = []
	def average(new_value):
		series.append(new_value)
		total = sum(series)
		return total/len(series)
	return average
```

调用make_averager时，返回一个averager函数对象。每次调用average时，它会把参数添加到系列值中，然后计算平均值。在averager函数中series是自由变量，averager的闭包延伸到那个函数的作用域之外，包含自由变量series的绑定。只有嵌套在其他函数中的函数才可能需要处理不在全局作用域中的外部变量。

![ce5b2f585b3115fd4b5dd644d342321](C:\Users\scall\AppData\Local\Temp\WeChat Files\ce5b2f585b3115fd4b5dd644d342321.png)

第三种：错误解法

```python
def make_averager():
	count = 0
	total = 0
	def averager(new_value):
		count += 1
		total += new_value
		return total/count
	return averager
		
```

错误原因：当count是数字或不可变类型时，count += 1语句的作用其实与count = count + 1一样。因此，我们在averager的定义体中为count赋值了，则count变成了局部变量，total变量也守这个问题影响，即隐式创建局部变量。

​	

第四种：正确实现

```
def make_averager():
	count = 0
	total = 0
	def averager(new_value):
		nonlocal count,total
		count += 1
		total += new_value
		return total/count
	return averager
		
```

​	python3引入了弄local、声明，它的作用是把变量标记为自由变量，即时在函数中为变量赋予新值，也会变成自由变量。如果为nonlocal声明的变量赋予新值，则闭包中保存的绑定会更新。

### 函数装饰器

​	用于在源码中“标记”函数，以某种方式增强函数的行为。

​	装饰器是可调用的对象，其参数是另一个函数（被装饰的函数）。装饰器可能会处理被装饰的函数，然后把它返回，或者将其替换成另一个函数或可调用对象。

​	假如有个decorate的装饰器：

```python
@decorate
def target():
	print('running target()')
```

```python
def target():
	print('running target()')
target = decorate(target)
```

​	两种写法的最终结果一样：被装饰的target函数会被替换。

装饰器：他们在被装饰的函数定义之后立即执行。

函数装饰器在导入模块的时候立即执行，而被装饰的函数只在明确调用时运行。

装饰器的典型行为：把被装饰的函数替换成新函数，二者接受相同的参数，而且（通常）返回被装饰的函数本该返回的值，同时还做些额外的操作。

python内置了三个用于修饰方法的函数：property、classmethod和sataticmethod。另一个装饰器是functools.wrap。

### functools.lru_cache装饰器（备忘）

functools.lru_cache实现备忘的功能，将耗时的函数结果保存起来，避免传入相同的参数时重复计算，一段时间不用的缓存条目会被扔掉。

生成第n个斐波那契数列

第一种：递归

```python
from clock import clock
@clock
def fibonacci(n):
	if n<2:
		return n
	return fibonacci(n-2) + fibonacci(n-1)
if __name__=='__main__':
	print(fibonacci(6))
```

运行结果：重复调用了很多次

![ca81b72f1383ca1b3885ed5097ffecd](C:\Users\scall\AppData\Local\Temp\WeChat Files\ca81b72f1383ca1b3885ed5097ffecd.png)

第二种：使用lru_cache

```python
import functools
from checkdeco import clock

@functools.lru_cache()
@clock
def fibonacci(n):
	if n<2:
		return n
	return fibonacci(n-2) + fibonacci(n-1)
if __name__=='__main__':
    print(fibonacci(6))
```

运行结果：

![d87160ba36b111fda6e84c61689c54e](C:\Users\scall\AppData\Local\Temp\WeChat Files\d87160ba36b111fda6e84c61689c54e.png)

### 叠放装饰器

```python
@d1
@d2
def f():
	print('f')
```

等同于

```
def f():
	print('f')
f=d1(d2(f))
```

### 参数化装饰器

​	创建一个装饰器工厂函数，把参数传给他，返回一个装饰器，然后再把它应用到要装饰的函数上。

