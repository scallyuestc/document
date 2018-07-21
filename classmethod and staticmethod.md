### classmethod

定义操作类，而不是操作实例的方法；classmethod改变了调用方法的方式，因此类方法的第一个参数是类本身，而不是实例。最常见用途是定义备选构造方法。

好处：调用classmethod不需要类实例化，直接调用就可以，如果要多次调用类中方法。

定义的时间类：

```python
class Data_test(object):
    day = 0
    month = 0
    year = 0
    def __init__(self,year=0,month=0,day=0):
        self.day=day
        self.month=month
        self.year=year
    def out_date(self):
        print "year"
        print self.year
        print "month"
        print self.month
        print "day"
        print self.day
t=Data_test(2016,8,1)
t.out_date()

```

输出

```python
year
2016
month
8
day
1
```

如果用户输入的是“2016-8-1”这样的字符串格式，那么在调用Date_test之前需要做一下处理。

```python
string_date = "2016-8-1"
year,month,day = map(int,string_date.split('_'))
s=Date_test(year,month,day)
```

若不做处理，则需要在类中添加函数，并改变其他函数，而使用classmethod对类进行重构，则不需要改变其他函数；

```
class Data_test(object):
    day = 0
    month = 0
    year = 0
    def __init__(self,year=0,month=0,day=0):
        self.day=day
        self.month=month
        self.year=year
    @classmethod
    def get_date(cls,string_date):
    	# 第一个参数是cls，表示调用当前类名
    	year,month,day = map(int,string_date.split('_'))
    	date1 = cls(year,month,day)
    	return date1
    def out_date(self):
        print "year"
        print self.year
        print "month"
        print self.month
        print "day"
        print self.day
t=Data_test(2016,8,1)
t.out_date()

r = Date_test.get_date("2016-8-6")
r.out_Date()
```

等于先调用了get_date()对字符串进行特殊处理，然后才使用类的构造函数初始化。这样的好处是不必要修改类的构造函数，只需要添加额外要处理的函数，然后使用装饰符@classmethod局可以。

### staticmethod

改变了方法的调用方式，但是第一个参数不是特殊的值，静态方法就是普通的函数，知识碰巧在类的定义体中，不是在模块层定义。