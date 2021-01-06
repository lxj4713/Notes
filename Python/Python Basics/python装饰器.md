<!-- toc -->
[toc]
<!-- toc -->



参考：https://mp.weixin.qq.com/s/XTHZ-K1yDNPIoejNFvk6rw

https://iswbm.com/47.html

https://mp.weixin.qq.com/s?__biz=MzA4MjEyNTA5Mw==&mid=2652569579&idx=1&sn=bc432ac7e7b0013b27cf4712080c328f&chksm=8464d7a1b3135eb7e89b83241cd844200e3ee724ed3e6e2e11472dabbd929ac24c4fe44a044b&mpshare=1&scene=1&srcid=#rd



https://mp.weixin.qq.com/s?__biz=MzA4MjEyNTA5Mw==&mid=2652569441&idx=1&sn=aaf6b48116aad898b07e8e8085b0d154&chksm=8464d72bb3135e3daa964ff1722b43c00fa624c3d7bd5561897ca477d908b8cf5f7c8997e67f&mpshare=1&scene=1&srcid=#rd

[**Python自动化开发 - 装饰器**](https://www.cnblogs.com/jonathan1314/p/6391115.html)





### 装饰器Ddcorator



``` python
def func_out(func):
    def func_in(*args,**kwargs):
        # do something
        return func(*args,**kwargs)
   	return func_in

@funcOut
# test = func_out(test)
def test(*args):
    return *args
```



#### 闭包

用途:可以在不修改源代码的前提下,添加新功能,如添加日志功能

可将单方法的类转换为函数

> 1. **要有函数的嵌套**
> 2. **内部函数要使用外部函数作用域内的变量**
> 3. **外部函数必须有返回值,返回内部函数名**

#### 多重装饰器使用

挨着功能函数，从下至上依次装饰

并且执行依次先执行装饰器外层函数，后依次执行内层函数



#### 类装饰器（一般不用）

``` python
class TestDecorator():
    def __init__(self,func):
        self.func = func
        print('class - test')
        self.func()   # 调用
    
    # 解决报错 TypeError: 'NoneType' object is not callable
    def __call__(self,*args,**kwargs):
        pass

        
@TestDecorator        
def test():
    print('test')
```







### 几种装饰器示例：

#### 装饰含有返回值的函数

``` python

from functools import wraps
def mywork(func):
    task_name = func.__name__   #此处为获得被装饰的函数名 a3
    
    @warps(func)     #防止被装饰的函数的函数名被改变
    def inner(*args,**kwargs):
        print("添加1")
        aa=func(*args,**kwargs)
        print("添加2")
        return aa
    return inner
@mywork
def a3(arg1,arg2):
    print('i am zhangsan',arg1,arg2)
    li=[1,2,3,4,5,6]
    return li   #返回一个列表

list=a3("参数1","参数2")    #list等于inner的返回值
print(list)
#li列表是a3的返回值，所以给在inner函数里执行的func()赋给aa，在通过inner的返回值就能拿到列表

#输出结果
添加1
i am zhangsan 参数1 参数2
添加2
[1, 2, 3, 4, 5, 6]
```

#### 装饰器实现登录验证简单原理

```python

def login():
    name =input("输入用户名：")
    if name == "zhangsan":
        return  True
    else:
        return False
def mywork(func):
    def inner(*args,**kwargs):
        lo_login = login()
        if not lo_login:    #如果login()返回的是False
            return "用户名错误!"
        aa=func(*args,**kwargs)
        return aa
    return inner
@mywork
def a3(arg1,arg2):
    print('i am zhangsan',arg1,arg2)
    li=[1,2,3,4,5,6]
    return li
list=a3("参数1","参数2")    #list等于inner的返回值
print(list)

#输出结果一
输入用户名：zs
用户名错误!

#输出结果二：
输入用户名：zhangsan
i am zhangsan 参数1 参数2
[1, 2, 3, 4, 5, 6]
```



#### 多个装饰器装饰一个函数 一

``` python
def newwork1(func):
    def inner():
        print("newwork1前")
        func()
        print("newwork1后")
    return inner
def newwork2(func):
    def inner():
        print("newwork2前")
        func()
        print("newwork2后")
    return inner

@newwork2
@newwork1
def f1():
    prin( 'i am zhangsan')

f1()


#输出结果：
newwork2前
newwork1前
i am zhangsan
newwork1后
newwork2后
```

#### 多个装饰器装饰一个函数 

``` python

def wrapper1(func):
    def inner1():
        print('wrapper1 ,before func')
        ret = func()
        print('wrapper1 ,after func')
        return ret
    return inner1

def wrapper2(func):
    def inner2():
        print('wrapper2 ,before func')
        ret = func()
        print('wrapper2 ,after func')
        return ret
    return inner2

def wrapper3(func):
    def inner3():
        print('wrapper3 ,before func')
        ret = func()
        print('wrapper3 ,after func')
        return ret
    return inner3

@wrapper3
@wrapper2
@wrapper1
def f():
    print('in f')
    return 'i am zhangsan'

print(f())

#输出结果：
wrapper3 ,before func
wrapper2 ,before func
wrapper1 ,before func
in f
wrapper1 ,after func
wrapper2 ,after func
wrapper3 ,after func
i am zhangsan
```

#### 3层装饰器

``` python

def Filter(a1,a2):
    def outer(func):
        def wrapper(request,kargs):
            print(a1)
            result=func(request,kargs)
            print(a2)
            return result
        return wrapper
    return  outer
aa=11
bb=22
@Filter(aa,bb)
def Index(request,kargs):
    print(request,kargs)

Index("zhangsan","lisi")
#@Filter(aa,bb)会先执行Filter(aa,bb)函数，获取到返回值outer后拼接成@outer，之后就变成普通的装饰器了
#wrapper函数内可以使用a1,a2,request,kargs 4个参数

#输出结果 
11
zhangsan lisi
22
```

