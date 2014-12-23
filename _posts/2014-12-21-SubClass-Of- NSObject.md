---
layout: post
title: 创建Objective-C数据模型的心得1
date: 2014-12-21
categories: cn
---

之前自己在公司写iOS应用主要是写的逻辑层与数据层，也算有些心得。所以决定在最近几篇Blog里将一些网上不常见的方法总结一下。

## 本期的一个关键方法

> -(BOOL)isEqual:(id)object;


## Basic数据模型


`Basic.h`定义了 

* index: 数据模型在App中的唯一ID  
* content: 数据模型装载的内容，用于显示  

~~~objc
@interface Basic : NSObject

@property (assign) NSInteger index;
@property (copy, nonatomic) NSString *content;

- (instancetype)initWithIndex:(NSInteger)index andContent:(NSString *)content;

@end
~~~

`Basic.m`中实现初始化方法`-initWithIndex:andContent;`

~~~objc
@implementation Basic
- (instancetype)initWithIndex:(NSInteger)index andContent:(NSString *)content {
  if (self = [super init]) {
    _index = index;
    _content = content;
  }
  return self;
}
@end
~~~

### 内存比较

常用的逻辑判断中，经常会使用到类似于数据库的`插入`、`删除`、`查找`这几个方法。
这些方法的关键则是对**两个数据模型的比较结果**，比如NSMutableArray中使用的排序方法
>`- (void)sortUsingComparator:(NSComparator)cmptr;`

其关键点就是对`NSComparator` Block的实现，定义如下：
>`typedef NSComparisonResult (^NSComparator)(id obj1, id obj2);`
返回两个对象的`关系`。

NSObject中定义的`- (BOOL)isEqual:(id)object;`就是对Objective-C中，对象内存地址的判断，如果两个对象所指向的地址相同，则判断为同一对象。在下面的代码中，我先创建了一组Basic对象数据，并且将index=2的对象赋值到了`preBasicObj2`。

~~~objc
  NSMutableArray *basics = [NSMutableArray array];
  Basic *preBasicObj2 = nil;
  for (NSInteger i = 0 ; i < 5 ; i++) {
    Basic *basic = [[Basic alloc]initWithIndex:i andContent:@"Temp"];
    [basics addObject:basic];
    if (i == 2) {
      preBasicObj2 = basic;
    }
  }
~~~

这样，再从数组中拿到index=2的对象跟`preBasicObj2`进行一系列的比较。

~~~objc
  Basic *arrayObj2 = basics[2];
  BOOL equalResult = [preBasicObj2 isEqual:arrayObj2];
  BOOL containResult = [basics containsObject:preBasicObj2];
  NSInteger index = [basics indexOfObject:preBasicObj2];
  NSLog(@"equalResult:%d \n\
        containResult:%d \n\
        index:%ld ",equalResult, containResult, index);
~~~

理所当然的从console中输出以下结果：

>equalResult:1  
>containResult:1  
>index:2


###对象比较1

不过在实际开发中，我们拿到的对象可能不只是在内存中操作，仅使用内存比较是不够的。同样一个对象可以由多个地方生成，比如：

* 用户输入
* 网络返回
* 数据库读取
* Copy方法

下面的测试方法中，我重新创建了index==2的数据对象用于模拟从其它地方读取或者生成的情况。

~~~objc
  Basic *buildObj2 = [[Basic alloc]initWithIndex:2 andContent:@"Temp"];
  BOOL equalResult = [buildObj2 isEqual:arrayObj2];
  BOOL containResult = [basics containsObject:buildObj2];
  NSInteger index = [basics indexOfObject:buildObj2];

  NSLog(@"equalResult:%d \n\
        containResult:%d \n\
        index:%ld ",equalResult, containResult, index);
        
//Output:
//equalResult:0
//containResult:0
//index:9223372036854775807
~~~

测试结果则如上所示，**不相等**，**不存在**，和**找不到**。所以曾经在相当长的一段时间里，都会使用`for循环`来进行判断。后来，我一般会改用

`- (void)enumerateObjectsUsingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block NS_AVAILABLE(10_6, 4_0);`

查找DEMO

~~~objc
  __block NSInteger index2 = NSNotFound;
  [basics enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    Basic *tempObj = (Basic *)obj;
    if (tempObj.index == buildObj2.index) {
      *stop = YES;
      index2 = idx;
    }
  }];
~~~


##Basic数据模型“改”

直至后来有一次偶然覆写了`NSObject`中的`-isEqual:`方法。此方法覆写的关键在于你需要知道你的数据模型里，哪一个是可以用于**判断的关键属性**，类似于数据库中的`PRIMARY KEY`。


将下列代码添加于`Basic.m`中。  
在`-isEqual:`具体实现中，还可以通过`==`来判断是否是同一地址和`isKindOfClass`判断是否是同一类型的数据模型来增加一些必要的限制。

~~~objc
- (BOOL)isEqual:(id)object {
  if (self == object) {
    return YES;
  }
  if (![object isKindOfClass:[self class]]) {
    return NO;
  }
  Basic *inputObject = (Basic *)object;
  if (inputObject.index == self.index) {
    return YES;
  }
  return NO;
}
~~~

###对象比较2

然后我们重新做一下不同来源的数组对象比较测试：

~~~obj
  Basic *buildObj2 = [[Basic alloc]initWithIndex:2 andContent:@"Temp"];
  BOOL equalResult = [buildObj2 isEqual:arrayObj2];
  BOOL containResult = [basics containsObject:buildObj2];
  NSInteger index = [basics indexOfObject:buildObj2];

  NSLog(@"equalResult:%d \n\
        containResult:%d \n\
        index:%ld ",equalResult, containResult, index);

//Output:
//equalResult:1
//containResult:1
//index:2
~~~

##总结

分析数据模型中的关键属性，找到其中的**唯一**的属性用于实现`-isEqual: `的方法。

通过`-isEqual:`方法的实现可以强化很多`集合`类对象中对于**对象存在**的问题进行强化。

可以参考自己数据库中的主键，网络服务返回回来的UID等
