# 对象引用 可变性和垃圾回收



    l1 = [3, [66, 55, 44], (7, 8, 9)] 
    l2 = list(l1) #1
    l1.append(100) #2
    l1[1].remove(55) #3
    print('l1:', l1)
    print('l2:', l2) 
    l2[1] += [33, 22] #4
    l2[2] += (10, 11) #5
    print('l1:', l1) 
    print('l2:', l2)
    
输出：

    ('l1:', [3, [66, 44], (7, 8, 9), 100])
    ('l2:', [3, [66, 44], (7, 8, 9)])
    ('l1:', [3, [66, 44, 33, 22], (7, 8, 9), 100])
    ('l2:', [3, [66, 44, 33, 22], (7, 8, 9, 10)]）
    
解释：

     #1: l2是l1的浅复制
     #2 把100追加到l1中，对l2没有影响
     #3 把内部列表l1[1]中的55删除，这对l2有影响，因为l2[1]绑定的列表与l1[1]绑定的列表相同
     #4 对可变对象而言，如l2[1]引用的列表， += 运算符就地修改列表。
     #5 对元组而言， +=运算符创建一个新元组，然后重新绑定给变量l2[2],这等同于l2[12] = l2[2] + (10,11),现在l1和l2中绑定的元组不是同一个对象。
    
    
注：可以在http://www.pythontutor.com中查看代码交互式动画。


## 浅复制和深复制

可以使用copy模块提供的copy和deepcopy函数对任意对象做浅复制和深复制操作。


    class Bus(object):

        def __init__(self, passenger=None):
            if not passenger:
                self.passenger = []
            else:
            self.passenger = list(passenger)
        
        def pick(self,name):
            self.passenger.append(name)
        
        def drop(self, name):
            self.passenger.remove(name)


注意：不要使用可变值作为参数的默认值。