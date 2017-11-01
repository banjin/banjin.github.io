---
layout: post
title:  " 装饰器和闭包"
date:   2017-10-23 15:04:23
categories: [jekyll]
tags: [Python]
---



# 装饰器和闭包

#### 作用域

    LEGB: L > E > G > B
    
    L: local函数内部作用域
    E: enclosing函数内部与内嵌函数之间
    G: global 全局作用域
    B: build-in 内置作用域


示例1:

    passline = 60
    
    #1 def func(val):
        if val >= passline:
            print "pass"
        else:
        print "failed"
    
    #2 def func(val):
        passline = 90
        if val >= passline:
            print "pass"
        else:
        print "failed"
        
        def in_func():
            print val
        in_func()
        
    def max(val1, val2):
        return Max(val1,val2)
        
     func(89)
     print max(90,200)
 
 
 闭包概念：Closure,内部函数中对enclosing作用域的变量进行引用
 
 函数实质与属性:
 
 1.函数是一个对象
 
 2.函数属性
 
 3.函数返回值
 
 4.函数执行完成后内部变量回收
    
        passline = 60
        def func(val):
        print "%x" %(val)
            passline = 90
            if val >= passline:
                print "pass"
            else:
            print "failed"
            
            def in_func():
                print val
            in_func()
            return in_func
        f = func(90)
        f()
        print f.__closure__
 
 
 
语法糖 @deco
装饰器是在模块加载时候运行。要调用的函数是在运行时候运行。

 
    def deco(func):
        def in_deco():
            print ('in deco')
            func()
        print "call deco"
        
        return in_deco
    
    @deco
    def bar():
        print "XXxx"
    
    
    ######################## 
    
    不带参数的装饰器
    
    def log(func):
        def wrap(*args,**kw):
            print "before call func"
            func(*args, **kw)
            
        return wrap
        
        
    @log
    def echo():
        print "echo"
        
    ######################## 
    带参数的装饰器
    
    def log(text):
        def decorator(func):
            def wrap(*args, **kw):
                print '%s %s():' % (text, func.__name__)
                return func(*args, **kw)
            return wrapper
        return decorator
        
    @log('execute')
    def now():
        print '2013-12-25'
        
     ######################## 
     
        import functools

        def log(func):
            @functools.wraps(func)
            def wrapper(*args, **kw):
                print 'call %s():' % func.__name__
                return func(*args, **kw)
            return wrapper
            
     
     ######################## 
     
    import functools

    def log(text):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kw):
                print '%s %s():' % (text, func.__name__)
                return func(*args, **kw)
            return wrapper
        return decorator
        
    ######################## 
    promos = []
    def promotion(promo_func):
        promos.append(promo_func)
        return prono_func
        
    @promotion
    def fidelity(order):
        """为积分为1000或以上的顾客提供5%折扣"""
        return order.tital()* .05 if order.customer.fidlity >=1000 else 0
        
    def bulk_item(order):
        """单个商品为20或以上的提供10%折扣"""
        discount = 0
        for item in oeder.cart:
            if items.quantity >= 20:
                discount += item.total() * .1
            return discount
            
    def large_order(order):
        """订单中不同商品超过10个或以上时候提供7%折扣"""
        distinct_items = {item.product for item in order.cart}
        if len(distinct_items) >= 10:
            return order.total() * .07
        return 0
        
    def best_promo(order):
        return max(promo(order) for promo in promos)
        
        
        
     >>> b =6
    >>> def f(a):
    ...     print a
    ...     print b
    ...     b = 9
    ... 
    >>> f(3)
    3
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "<stdin>", line 3, in f
    UnboundLocalError: local variable 'b' referenced before assignment
    
    python编译函数的定义体时候，它判断b是局部变量，因为在函数中给它赋值了
    这不是缺陷，而是设计选择:python不要求声明变量，但是假定在函数定义体中赋值的变量是局部变量。
    
         
     
     
    