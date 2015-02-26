---
title: NSCondition的用法
layout: post
---

这个东西简单实现了不同线程之间操作顺序的控制，也可以理解为生产者与消费者的控制  
简单来说就是一个线程 执行到某一步骤 需要其他线程的支援，那么需要调用 wait 方法，然后另一个线程在调用结束后 调用 signal 方法，之前 wait 处继续执行  
但是有一点需要注意的就是，不能使用两个同时执行的线程 必须拥有先后顺序 也就是先 wait 后 signal 的顺序 否则会出问题  
还有一点需要注意的就是，在使用它的时候需要加锁，当然是 Condition 自带的锁  
我们的app的使用场景就是 如果拥有多个相同的请求 那么我先判断是否正在请求 如果是wait 如果不是 去请求 并且在请求结束的时候调用 signal  

还有一点需要特别说明 就是如果有多个wait 一次 signal 也只能唤醒一个 wait  
如果想要广播 请使用 broadcast 方法

~~~
//点一下表示开始接受
- (IBAction)test1Button:(id)sender
{
    _a++;
    [NSThread detachNewThreadSelector:@selector(thread1) toTarget:self withObject:nil];
}

//点一下表示收到一个数据
- (IBAction)test2Button:(id)sender
{
    [NSThread detachNewThreadSelector:@selector(thread2) toTarget:self withObject:nil];
}

//发送线程
- (void)thread1
{
        NSLog(@"thread1：等待发送");
        [self.condition lock];
        NSInteger b = _a;
        [self.condition wait];
        
        NSLog(@"thread1:发送");
        NSLog(@"%ld",(long)b);

        [self.condition unlock];
}

//接收线程
- (void)thread2
{
    [self.condition lock];
    NSLog(@"thread2：收到数据");
    [self.condition broadcast];
    [self.condition unlock];
}
~~~