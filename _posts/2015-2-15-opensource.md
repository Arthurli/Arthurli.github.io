---
title: 2015/2/12开源库
layout: post
---

##FastImageCache
[戳这里](http://www.tuicool.com/articles/2EvuyqA)  
首先不得不说，这个库是一个质量非常高的，效果也是十分优秀的图片显示库，尤其是他的点击效果，更是一些像我这样的小白一直苦苦追寻的效果。  

##KMCGeigerCounter
这是一个计算iOS动画帧速计算类库

~~~
    [KMCGeigerCounter sharedGeigerCounter].enabled = YES;
~~~

这里用了两个属性控制开关，是 **running** 和 **enable**, **enable** 表示是否开启， **running** 表示是否正在运行， 然后通过通知控制 运行开关， 如下

~~~
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(applicationDidBecomeActive) name:UIApplicationDidBecomeActiveNotification object:nil];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(applicationWillResignActive) name:UIApplicationWillResignActiveNotification object:nil];
~~~

~~~
- (void)applicationDidBecomeActive
{
    self.running = self.enabled;
}

- (void)applicationWillResignActive
{
    self.running = NO;
}
~~~

 CADisplayLink Class，這個 Class 的功能類似於 Timer。由於能支援每秒高達 60 fps 的畫面同步功能，所以更適合用在製作遊戲動畫上面，相較之下 Timer 較常使用在背景處理層面，其基本使用方式如下
 
~~~
self.displayLink = [CADisplayLink displayLinkWithTarget:self selector:@selector(displayLinkWillDraw:)];
[self.displayLink addToRunLoop:[NSRunLoop mainRunLoop] forMode:NSRunLoopCommonModes];
[self clearLastSecondOfFrameTimes];
~~~

而他做失帧的原理就是 用一个数组记录60个时间 如果是正常的话 每次轮训的时间 能对应一个数组的位置 然后与这个位置的时间进行对比 如果差距大于1s，我们认为它失帧+1

##FLAnimatedImage

这是一个显示GIF的类库，分为两个部分 imageView 和 Image  

###FLAnimatedImageView
首先这个类也是用了CADisplayLink来做一个帧的timer，而且这里做了一个很重要的事情，是为了防止循环引用，使用了weak代理

~~~
// It is important to note the use of a weak proxy here to avoid a retain cycle. `-displayLinkWithTarget:selector:`
// will retain its target until it is invalidated. We use a weak proxy so that the image view will get deallocated
// independent of the display link's lifetime. Upon image view deallocation, we invalidate the display
// link which will lead to the deallocation of both the display link and the weak proxy.
FLWeakProxy *weakProxy = [FLWeakProxy weakProxyForObject:self];
self.displayLink = [CADisplayLink displayLinkWithTarget:weakProxy selector:@selector(displayDidRefresh:)];
  
NSString *mode = NSDefaultRunLoopMode;
// Enable playback during scrolling by allowing timer events (i.e. animation) with `NSRunLoopCommonModes`.
// But too keep scrolling smooth, only do this for hardware with more than one core and otherwise keep it at the default `NSDefaultRunLoopMode`.
// The only devices with single-core chips (supporting iOS 6+) are iPhone 3GS/4 and iPod Touch 4th gen.
// Key off `activeProcessorCount` (as opposed to `processorCount`) since the system could shut down cores in certain situations.
if ([NSProcessInfo processInfo].activeProcessorCount > 1) {
 mode = NSRunLoopCommonModes;
}
[self.displayLink addToRunLoop:[NSRunLoop mainRunLoop] forMode:mode];
~~~

而且这里做了一个很好的设计就是 把image的set和get方法都重写了，然后把 startAnimating 和 stopAnimating 也都进行了重写，让他们都能兼容imageView正常的使用和GIF的显示，这样既方便了GIF的使用，不用额外增加方法，也不会影响正常使用， 非常好

这里还有几个属性值得说一下 在下面的方法中

~~~
//这是在帧循环中调用的方法

self.currentFrame = image;

//当这个属性为YES的时候表示更换图片 需要刷新图片
if (self.needsDisplayWhenImageBecomesAvailable) {
  [self.layer setNeedsDisplay];
  self.needsDisplayWhenImageBecomesAvailable = NO;
}

//累计加上timer的时间 
self.accumulator += displayLink.duration;
   
// 当累计的时间达到了当前这张图片的显示时间的时候 清空累计器 然后更换图片
while (self.accumulator >= [self.animatedImage.delayTimes[self.currentFrameIndex] floatValue]) {
  self.accumulator -= [self.animatedImage.delayTimes[self.currentFrameIndex] floatValue];
  self.currentFrameIndex++;
  
  //如果完成一次轮询 进行下一次轮训 如果达到轮询最大次数 停止轮询
  if (self.currentFrameIndex >= self.animatedImage.frameCount) {

      self.loopCountdown--;
      if (self.loopCountdown == 0) {
          [self stopAnimating];
          return;
      }
      self.currentFrameIndex = 0;
  }
  // Calling `-setNeedsDisplay` will just paint the current frame, not the new frame that we may have moved to.
  // Instead, set `needsDisplayWhenImageBecomesAvailable` to `YES` -- this will paint the new image once loaded.
  self.needsDisplayWhenImageBecomesAvailable = YES;
}
~~~ 

虚基类[NSProxy](http://www.nexuspod.com/?p=789)为可以成为另一个对象的替身对象或是不存在的对象定义了一套API。使用 NSProxy 可以透明的进行消息转发或者延迟加载一个耗费巨大的对象。它接收到任何自己没有定义的方法时都会产生一个异常，所以子类必须重载 forwardInvocation: 和 methodSignatureForSelector: 来处理自己没有实现的消息（通常是把自己未实现的消息转发给另一个对象）。

###FLAnimatedImage

这里的FLAnimatedImage在怎对比较大的GIF的时候只会缓存一部分的Cache在内存中，关于这个缓存策略


这里要说到两个类
#####NSHashTable 和 NSMapTable
在iOS6和MAC OS X 10.5开始，提供了相对于NSSet和 NSDictionary 更通用的两个类 NSHashTable和 NSMapTable。

Set 和 HashTable 区别

+ NSSet/NSMutableSet 对其对象是强引用，使用isEqual方法去检查对象是否相等，使用方法hash去获取hash值。
+ NSHashTable是可变的，没有一个不变的和其对应。
+ NSHashTable 可以对其对象是weak 引用。
+ NSHashTable 可以在输入（加入）的时候 copy 对象。
+ NSHashTable 可以包含任意指针，使用指针去做相等或者hashing检查。

HashTable 可选项 

+ NSHashTable使用一个option去初始化，下面是可用的选项：
+ NSHashTableStrongMemory：和 NSPointerFunctionsStrongMemory相同，使用此选项为默认的行为，和NSSet的内存策略相同。
+ NSHashTableWeakMemory：和 NSPointerFunctionsWeakMemory相同，此选项使用weak存储对+ 象，当对象被销毁的时候自动将其从集合中移除。
+ NSHashTableCopyIn ：和 NSPointerFunctionsCopyIn 相同，此选项在对象被加入到集合之前copy它们。
+ NSHashTableObjectPointerPersonality：和 NSPointerFunctionsObjectPointerPersonality相同，此选项是直接使用指针进行isEqual:和 hash。

NSMapTable和NSDictionary相对应，相对于 NSDictionary/NSMutableDictionary，

+ NSMapTable有如下的特征：
+ NSDictionary/NSMutableDictionary会copy对应的key，强引用相应的value。
+ NSMapTable是可变的，没有一个不变的类与其对应。
+ NSMapTable 可以对其 key和 value弱引用，在这种情况下当key或者value被释放的时候，此entry会自动从NSMapTable中移除。
+ NSMapTable 在加入一个（key，value）的时候，可以对其value设置为copy。
+ NSMapTable可以包含任意指针，使用指针去做相等或者hashing检查。
+ 下面的NSMapTable例子中，key不是copy的（强引用的），value为弱引用。

+ NSMapTableStrongMemory：指定对应的key或者value为强引用。 
+ NSMapTableWeakMemory：指定对应的key或者value为弱引用。 
+ NSMapTableCopyIn：指定对应的key或者value在加入到集合中的时候为copy。 
+ NSMapTableObjectPointerPersonality：此选项是直接使用指针进行isEqual:和 hash 