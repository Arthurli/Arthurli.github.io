---
layout: post
title: 重置iOS App请求推送授权请求
date: 2014-12-12
categories: cn
---

在iOS上给自己的App创建推送通并不容易，连测试用户给App进行授权推送通知也是件很麻烦的事情。这将直接关系到用户体验与应用流程的处理。

之前一直是有在模拟器上测试授权（比如通讯录，月历访问等），通过模拟器的`Reset Content and Setting`来重置整个模拟器系统来进行调试。

<!-- more -->

![](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/e2bf38e87f0c194df27a897c9f945f257bcf83b5/_postsImages/2014/12/1.png
)

相比其它授权，Push Token是不能只使用模拟器进行测试的，因为iOS的服务器是不会给模拟器下发Push Token的。所以这个流程最好是用真机进行测试。

![](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/e2bf38e87f0c194df27a897c9f945f257bcf83b5/_postsImages/2014/12/2.png)

但如果使用真机进行反复测试，在之前几乎只有一个办法:  

`通用`->`还原`->`抹掉所有内容和设置`

清空所有内容不说，整个过程可能还长达数十分钟。


昨天在和同事感叹这个问题的时候，搜索了一下stackoverflow.com上找到了答案：

http://stackoverflow.com/questions/2438400/reset-push-notification-settings-for-app

以及此答案指向的Apple官方解答：

https://developer.apple.com/library/ios/technotes/tn2010/tn2265.html

相关文档引用

>###Resetting the Push Notifications Permissions Alert on iOS

>The first time a push-enabled app registers for push notifications, iOS asks the user if they wish to receive notifications for that app. Once the user has responded to this alert it is not presented again unless the device is restored or the app has been uninstalled for at least a day.

>If you want to simulate a first-time run of your app, you can leave the app uninstalled for a day. You can achieve the latter without actually waiting a day by following these steps:

>* Delete your app from the device.
* Turn the device off completely and turn it back on.
* Go to Settings > General > Date & Time and set the date ahead a day or more.
* Turn the device off completely again and turn it back on.

翻译一下：

将App从设备上删除  
将设备完全关机再重新启动  
打开 设置->通用->日期与时间里 将设备时间拔快一天以上  
将设备再次完全关机再重新启动

此时再安装你的App可以像纯新的流程一样进行测试所有授权，

在设置中查看你的App授权选项也是全部重置。

分析：

由于之前没有仔细看过相关文档，但可以肯定iOS的授权设置会在设备重新启动时进行更新，而重置的周期需要在一天以上。以后再看到更细的说明再补充。