---
layout: post
title: Python函数
category: "编程"
tag: "Python"
---

##1 返回值
当没有返回值时，python会返回一个None对象，当返回值数量超过一个时，默认返回一个元组（可以不加圆括号）。如果将多个返回值使用[]包含，那么返回一个列表。

##2 函数创建
创建函数的语法如下：

```
def function_name(args):
    "function_documentation_string"#函数文档子串
    function_body_suite#函数体，如果函数啥都不执行，使用pass
```

在Python中，将函数的声明和定义视为一体。函数体中可以使用还没有定义的其他函数，只要在函数调用前定义这些函数就不会报错。

函数体中也可以定义其他函数，成为内部/内嵌函数，内嵌函数的作用于在外部函数之内，不能直接调用。

##3 函数装饰器
函数装饰器其实就是接收函数对象为参数的函数，从本质上来说，它可以包装函数，如在函数执行之前或者之后做一些工作，体现了AOP（面向方面编程）的概念。

```
@deco2
@deco1(deco_arg)#参数可选
def func(arg1,arg2,...):pass
```

等价于：

```
def func(arg1,arg2,...):pass
func=deco2(deco1(deco_arg)(func))
```

如何实现一个装饰器呢，其实很简单，就是实现一个函数，函数名和装饰器名相同，函数的输入参数是一个函数，输出参数也是一个函数即可。输出的这个函数一般是装饰器函数的内嵌函数。

##4 函数参数
###默认参数
没啥好说的，和C/C++里面的一样。
###关键字参数
对于函数的调用，可以通过关键字来区分形参和实参的对应关系，这样就允许参数却是或者不按照顺序。

```
def net_conn(host,port):
    net_conn_suite
```

调用函数：

```
net_conn('kappa',8080)
#或者
net_conn(port=8080,host='kappa')
```

当存在默认参数时，也可以使用关键词参数，这样就不根据顺序来确定默认参数了。
###参数组
Python允许通过把一个元组（非关键词参数）或者字典（关键词参数）作为参数组传递给函数。
`*tuple_args`表示元组参数组，
`**dict_args`表示字典参数组。

```
func(*tuple_args,**dict_args)
#当然也可以给出形参，但是为了形参和参数组语义歧义，应该把形参放在前面
func(arg1,arg2,*tuple_args,**dict_args)
```

##5 可变长度的参数
也就是刚才提到的参数组，当遇到函数处理可变数量参数的情况使用。变长参数在函数声明中不是显式命名的，在运行之前是未知的。
###非关键字可变长参数（元组）
可变长参数元组在位置参数和默认参数之后，定义语法如下：

```
def function_name([formal_args,] *vargs_tuple):
    "function_doc_string"
    function_body_suite
```

元组保存了所有传递给函数的“额外”的参数（批诶了所有位置的参数后剩余的）。如果没有额外的参数，元组为空。
###关键字变量参数（字典）
在调用函数时存在额外的关键字参数，参数将被放入一个字典中。定义的语法如下：

```
def function_name([formal_args,][*vargst,],**vargsd):
    "function_doc_string"
    function_body_suite
```

元组参数和字典参数可以在同一个函数中使用，只要字典参数是最后一个参数
###调用可变长参数对象函数
调用可变长参数对象不仅仅是可以把额外的关键词参数和普通参数变为元组和字典，还有其他调用方法。我们以newfoo函数为例：

```
def newfoo(arg1,arg2,*nkw,**kw):
    pass

```

- 在形参之后的非关键词参数作为元组，关键词参数作为字典。
`newfoo(10,20,30,40,foo=50,bar=60)`
- 将非关键词参数放在元组中，关键词参数放在字典中，而不是逐个列出变量参数。
`newfoo(2,4,*(6,8),**{'foo':10,'bar':12})`
- 函数之外创建元组和字典，同时混合关键词参数和非关键词参数的顺序


```
aTuple=(6,7,8)
aDict{'a':9}
newfoo(1,2,3,x=4,y=5,*aTuple,**aDict)
```

在最后一个例子中，传入到函数里面的元组有（3、6、7、8），字典有：{'x':4,'y':5,'a':9},由此可见，在调用函数时，匹配普通的参数以后剩余的参数，无论顺序和形式，都会放在元组和字典中传到函数体内。

##6 作用域
Python的作用域和C/C++类似，包括全局空间和局部空间，在全局定义的变量具有全局空间，在全局都可以被使用；在函数内定义的变量只能在函数内部使用，并且会屏蔽全局的同名变量。

因为Python没有变量声明/定义的概念，因此在函数内部如果想使用全局变量，那么需要golbal关键字“声明”这个变量是全局的。


## 7 嵌套函数和闭包
一个函数可以嵌套在另一个函数中，这东西一般没啥用，但是存在一个闭包的概念比较有用。

闭包：
如果在一个内部函数里，对在外部作用域（但不是在全局作用域）的变量进行引用，那么内部函数就被认为是闭包(closure)。

外部函数中嵌套内部函数，外部函数返回内部函数，内部函数中访问外部函数的作用域，调用外部函数就返回内部函数。

```
>>>def addx(x):  
>>>    def adder(y): return x + y  
>>>    return adder  
>>> c =  addx(8)  
>>> type(c)  
<type 'function'>  
>>> c.__name__  
'adder'  
>>> c(10)  
18  
```

结合这段简单的代码和定义来说明闭包：

- 如果在一个内部函数里：adder(y)就是这个内部函数，
- 对在外部作用域（但不是在全局作用域）的变量进行引用：x就是被引用的变量，x在外部作用域addx里面，但不在全局作用域里，
则这个内部函数adder就是一个闭包。

再稍微讲究一点的解释是，闭包=函数块+定义函数时的环境，adder就是函数块，x就是环境，当然这个环境可以有很多，不止一个简单的x。
注意：闭包不能修改外部函数中的变量，如果要修改，那将这个变量作为容器使用。
更多关于闭包的解释：http://blog.csdn.net/marty_fu/article/details/7679297

## 8 生成器
生成器是一个函数，它不使用return返回结果，而是使用yield返回一个结果。同时，在这个函数中一般存在多个yield语句，函数会记录当前返回结果的yield语句并暂停于此，当执行next后，就会执行到下一个yield语句并返回结果。

生成器不仅记录数据状态，还记录当前语句执行中的位置。

生成器和迭代器类似。

生成器的使用举例：

```
def simpleGen():
    yield 1
    yield 2
#调用：
>>myG=simpleGen();
>>myG.next()
1
>>myG.next()
2
>>myG.next()
ERR:StopIteration
>>for eachItem in simpleGen():
    print eachItem
```

还可以使用send()将值送给生成器，使用close()退出生成器。比较复杂，没有用过了解不深刻所以就不详细介绍了。





















