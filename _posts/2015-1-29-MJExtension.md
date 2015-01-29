---
layout: post
title: MJExtension 解析
---

#MJExtension 介绍
这是一个把字典转换成为对象的库，按照作者的意思，它的效率是其他解决方案的数倍，那么，今天让我们来学习一下他是怎么来实现的



##类的解析
###MJFoundation  
这个类来判断传入类型是否可以被解析，也就是说是否来自 NSFoundation

###MJType  
是一种类型封装,每种Type都是唯一的存在，保存在字典中

###MJIvar   
是一个属性的封装,利用Ivar进行初始化，然后通过下面的方法存取和调用

~~~
MJIvar *ivarObject = objc_getAssociatedObject(self, ivar);
    if (ivarObject == nil) {
        ivarObject = [[self alloc] initWithIvar:ivar];
        objc_setAssociatedObject(self, ivar, ivarObject, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    }
~~~
这个类的功能就是通过Ivar获取一些信息

~~~
NSString *name = @(ivar_getName(ivar));
NSString *code = @(ivar_getTypeEncoding(ivar));
~~~

经过初试化之后，MJIvar就拥有了自己的属性名，这样就可以给把它作为KEY，再把传进来的字典进行设置或者提取属性  

###NSObject+MJIvar
这里面的方法是遍历当前类的属性，或者直接遍历父类的属性，然后通过block回调

###NSObject+MJCoding
这里面实现了遍历的编码和解码，使用NSObject+MJIvar中的方法会把所有属性都回调回来，我们就可以通过MJLvar来直接编码或者解码

###NSObject+MJKeyValue
首先这个类中定义了一些列协议，用作字典与对象的key不相同时候的转化，还有完成转换之后调用的代理  
然后就是一些转化方法，具体的转化方法就是先讲一切转化成字典，然后遍历类中的所有属性，然后再依次从字典中取出值，然后赋值

##总结
这个类库究竟帮我们做了什么呢，其实就是把我们平时需要手动的一个一个取得值，赋的值，通过运行时获取到这个类中的所有属性，然后帮我们依次去赋值
