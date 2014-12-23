---
layout: post
title: Cocoapods进阶教程
date: 2014-08-10
categories: cn
---

作为第一篇正式技术性Blog，主要是为了记录最近研究的Cocoapods在项目中的使用（虽然也还不是特别懂：P）。

<!-- more -->

####文章说明：
* 可能会有点啰嗦，想详细点，个人比较健忘，重点会标明
* 有些问题我也没研究明白，但以可用为主
* 欢迎指教，Email: wing.of.war.1980th@gmail.com


Cocoapods的教程一般都是基本的入门（大约是如我下图所示的Step2)，官方文档感觉对于新手也不够友好（至少像我这样的半桶水研究了两个晚上），所以一半教学一半记录吧。

###此文的结构如下图步骤所示：

![Cocoapods article  structure](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/Cocoapods%20article%20%20structure.jpg)




## 正文——像一个普通工程开始
* Step1: 创建一个新工程
* Step2: 通过CocoaPods导入几个常见框架（当然，说明项目中不会导入那么多，将以AFNetworking为例）
**Step 2.2**将说明如果卸载CocoaPods**（非常有用）**
* Step3: 创建一个个人类库（这将不会出现于Cocoapods的官方类库列表）。
**Step 3.1** 是通过Cocoapods创建的一个类库，保存与管理于Github上，在自己的项目中进行引用。便于团队的协作开发。
**Step 3.2**是在项目中建立一个私有类库，引用方式为相对路径，**好处是如果项目是私有的，将可以一起同项目保密和同步**
* Step4：工程需要进行扩展，在一个workspace下建立了多个target或者是导入了多个project，**重点是Podfile的配置**

之后本文的内容就会以上面的步骤的逐步进行，读者可以根据自身的需要来选择章节。

###几个关键点
* 一个很好的[安装教程](http://code4app.com/article/cocoapods-install-usage)
* 多个项目工程使用同一个Cocoapods的Podfile的编写
* *.podspec文件的编写
* 使用CocoaPoads对自身项目中的私有类库进行管理
* 从Github导入个人类库
* [Demo项目](https://github.com/Wing-Of-War/CocoaPods_Demo)，[Demo框架](https://github.com/Wing-Of-War/MyPrivateLibs)


## Step2 工程导入CocoaPoads

虽然是进阶教程，但我还是把基本的操作和常用的方法写一下，如果大家有更简便的方法可以我指点一下。

在桌面上，我新建了一个文件夹为将来的整体项目

~~~
mkdir CocoaPods_Demo
~~~

在此文件夹下，我建立了一个iPhone项目，命名为MyProject
进入到此项目下，pod初始化。

~~~
cd MyProject
pod init
~~~

或者直接在此文件夹下创建一个podfile文件，编辑podfile的内容如下，添加一个AFNetworking框架（非常好的类库示例，研究了很多）:

~~~
# Uncomment this line to define a global platform for your project
# platform :ios, "6.0"

target "MyProject" do
pod "AFNetworking" 
end


target "MyProjectTests" do

end
~~~

打开MyProject，此后使用的将是MyProject.xcworkspace这种以.xcworkspace为后缀的工作空间。在ViewController中我们可以尝试导入一下<AFNetworking/...>，如下图所示提示相关的头文件，则导入的框架AFNetworking工作正常。
![AFNetworking import](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/AFNetworking%20import.png)

***[补充:pod install和pod update命令的区别](https://github.com/CocoaPods/guides.cocoapods.org/issues/14)***
* pod install作为初次安装
* pod update为变更podfile和导入类库升级时使用


### Step2.1 类库移除&CocoaPods卸载

####类库移除

* 在podfile文件中移除相应的类库引入行，然后执行

~~~
pod install
~~~

将自动移除多余的框架

####CocoaPods卸载

转自[《从工程中删除Cocoapods》](http://blog.csdn.net/freedom2028/article/details/10244819):
* 删除工程文件夹下的Podfile、Podfile.lock及Pods文件夹
* 删除xcworkspace文件
* ***使用xcodeproj文件打开工程，删除Frameworks组下的Pods.xcconfig及libPods.a引用***
* 在工程设置中的Build Phases下删除Check Pods Manifest.lock及Copy Pods Resources

![CocoaPods Delete](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/CocoaPods%20Delete.png)

以上几点中，第三点中删除xcconfig应该是核心点，它是Cocoapods链接各个类库的关键。经常有可能Podfile写错后，生成多个xcconfig造成工程的头文件引入编译错误。此时可以先用此方法进行一次卸载后，重新初始化Cocoapods。

## Step3 创建个人类库

创建类库的pod命令

~~~
pod lib create "MyPrivateLibs"
~~~


![CocoaPods library create](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MyPrivateLibs%20Create.png)

创建是使用的Cocoapods的模板类，几个选项默认就好。进入此文件夹，
![MyPrivateLibs files](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MyPrivateLibs%20files.png)

* Example是模板中创建的测试工程项目，Example下已经自带podfile并且在运行install命令后，会自动导入这个类库，可以很方便的进行编写与测试；
* MyPrivateLibs.podspec**非常重要**，几乎是整个类库用于导入与存储结构的核心，不过目前步骤使用默认即可；
* Pod在默认包含Assets和Classes两个文件夹，一个用于存放图片，一个用于存放框架文件，在使用黑夜MyPrivateLibs.podspec的情况下，按此分类放好类库文件。
* LICENSE和README.md顾名思义

现在我们在Example里的项目中，添加一个类别（可以向类别扩展属性），命名为AssociatedObjects
.h文件

~~~
@interface NSObject (AssociatedObjects)
- (void)associateValue:(id)value withKey:(void *)key;
- (id)associatedValueForKey:(void *)key;
@end
~~~

.m文件

~~~
#import "NSObject+AssociatedObjects.h"
#import <objc/runtime.h>
@implementation NSObject (AssociatedObjects)
- (void)associateValue:(id)value withKey:(void *)key
{
    objc_setAssociatedObject(self, key, value, OBJC_ASSOCIATION_RETAIN);
}
- (id)associatedValueForKey:(void *)key
{
    return objc_getAssociatedObject(self, key);
}
@end
~~~

写一个简单的测试方法，向一个NSArray中关联了一个日期，十秒后取出，一切正常工作～

![MyPrivateLibs Test](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MyPrivateLibs%20Test.png)

以下是我目前自己的方法

1、把`NSObject+AssociatedObjects.h`和`NSObject+AssociatedObjects.m`两个文件移动到`/MyPrivateLibs/Pod/Classes`下
2、在Example中运行
~~~
pod update命令
~~~
3、打开Example中的测试项目，删除原项目中的.h和.m文件（当然，当前已经显示为文件无法找寻状态）然后在引用地方导入`<MyPrivateLibs/NSObject+AssociatedObjects.h>`
4、如果测试正常，则说明此类库建立正常

### Step3.1 导入Github类库

根据之前的步骤，将MyPrivateLibs提交到Github，我的
[MyPrivateLibs](https://github.com/Wing-Of-War/MyPrivateLibs):https://github.com/Wing-Of-War/MyPrivateLibs
如果为了方便，则可以直接引用我这个框架

* 如果是要直接引用Github上的类库，则务必将 .podspec文件放在类库的最上层目录中

回到`CocoaPods_Demo/MyProject/`中，修改Podfile如下

~~~
target "MyProject" do
pod "AFNetworking" 
pod "MyPrivateLibs", github => "https://github.com/Wing-Of-War/MyPrivateLibs"
end


target "MyProjectTests" do

end
~~~

然后install一下

![MyPrivateLibs import](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MyPrivateLibs%20import.png)

看见Installing MyPrivateLibs (0.1.0）已经正常安装，打开MyProject.xcworkspace，在Pod项目中，MyPrivateLibs和两个类库文件出现。然后测试一下：

![After Import MyPrivateLibs Test](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/After%20Import%20MyPrivateLibs%20Test.png)

一切工作正常，说明导入和类库都是正常的。

OK，接下来更多干货马上出现～


### Step3.2 导入本地类库

**需求的来源**———我们公司的项目是保存于Github上的一个私有repositories中，所以此项目中写的一些框架当然不能随便公开啦。
但为了管理方便和自己使用方法（只有我一个人在弄这个项目），我希望把其中的一部分**固定核心代码**从原项目中独立出来，做为一个私有内部框架，这也是为Step4做准备，所以折腾这个步骤了很久。

####创建简单的框架

在MyProject中，我创建了一个用于转换RFC3339的时间与字符串的转换方法类。

MyTimeTool.h

~~~
@interface MyTimeTool : NSObject

+ (NSDate *)rfc3339DateFormatterParse:(NSString *)string isExtend:(BOOL)isExtend;
+ (NSString *)rfc3339DateFormatterGenerate:(NSDate *)date isExtend:(BOOL)isExtend;

@end
~~~

MyTimeTool.m，本来是自己写的方法，后来根据Apple官方文档改的。

~~~

+ (NSString *)rfc3339DateFormatterGenerate:(NSDate *)date isExtend:(BOOL)isExtend
{
    static NSDateFormatter *dateFormatter = nil;
    if (!dateFormatter) {
        dateFormatter = [[NSDateFormatter alloc] init];
        NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
        [dateFormatter setLocale:enUSPOSIXLocale];
        [dateFormatter setTimeZone:[NSTimeZone timeZoneWithAbbreviation:@"UTC"]];
        [dateFormatter setDateFormat:@"yyyyMMdd'T'HHmmss'Z'"];
    }
    //    NSLog(@"rfc3339DateFormatterGenerate is %@, originDate is %@",[dateFormatter stringFromDate:date] , date);
    return [dateFormatter stringFromDate:date];
}


+ (NSDateFormatter *)getRfc3339ExtendFormatter
{
    static NSDateFormatter *rfc3339ExtendFormatter = nil;
    if (!rfc3339ExtendFormatter) {
        rfc3339ExtendFormatter = [[NSDateFormatter alloc] init];
        NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
        [rfc3339ExtendFormatter setLocale:enUSPOSIXLocale];
        [rfc3339ExtendFormatter setDateFormat:@"yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'SSS'Z'"];
        [rfc3339ExtendFormatter setTimeZone:[NSTimeZone timeZoneForSecondsFromGMT:0]];
    }
    return rfc3339ExtendFormatter;
}

+ (NSDateFormatter *)getRfc3339ShortFormatter
{
    static NSDateFormatter *rfc3339ShortFormatter = nil;
    if (!rfc3339ShortFormatter) {
        rfc3339ShortFormatter = [[NSDateFormatter alloc] init];
        NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
        [rfc3339ShortFormatter setLocale:enUSPOSIXLocale];
        [rfc3339ShortFormatter setDateFormat:@"yyyyMMdd'T'HHmmss'Z'"];
        [rfc3339ShortFormatter setTimeZone:[NSTimeZone timeZoneForSecondsFromGMT:0]];
    }
    return rfc3339ShortFormatter;    
}

+ (NSDate *)rfc3339DateFormatterParse:(NSString *)string isExtend:(BOOL)isExtend
{
    NSDateFormatter *parseFormatter = nil;
    if (isExtend) {
        parseFormatter = [self getRfc3339ExtendFormatter];
    }else
    {
        parseFormatter = [self getRfc3339ShortFormatter];
    }
    NSDate *result = [parseFormatter dateFromString:string];
    //    NSLog(@"rfc3339DateFormatterParse result is %@, origin stirng is %@", result, string );
    return result;
}
~~~

就以这两个文件为例创建一个本地类库

从终端进入到CocoaPods_Demo文件下

~~~
pod lib create MyLocalLib

..............................

Would you like to have a demo for your library? [ Yes / No ]
 > NO

Which testing frameworks will you use? [ Specta / Kiwi ]
 > 
specta
Would you like to do view based testing? [ Yes / No ]
 > NO

~~~

与之前的过程是类似的，但我们可以简单点，移植过来的类库都是已经在原有项目中测试过的。

为了丰富一下这个本地类库，我们将会把MyPrivate项目里的框架也一并移植过来。

进入`MyLocalLib`目录下，分别创建一个`MyTimeTool`和`MyCategory`两个文件夹。将MyPrivate项目中的两个文件放入`MyCategory`中，将`MyTimeTool.h`和`MyTimeTool.m`文件放入MyTimeTool中，并且可以删除`Pod`和`Example`两个文件夹，让我们的Lib看起来更纯粹一点。

####编写. podspec文件
因为是ruby的语法，虽然我不懂，但在参考了其它几个比较成熟的Cocoapods后，写出来个配置文件也是能够work的。先把写好的配置文件粘上来，再解释一下相关的字段。

* 请注意所有的编写一定要不要Mac系统自带的文本处理器，经常会将`'`打成`‘`。推荐使用`Sumblime`

~~~
Pod::Spec.new do |s|
  s.name             = "MyLocalLib"
  s.version          = "0.1.0"
  s.summary          = "A short description of MyLocalLib."
  s.description      = <<-DESC
                       An optional longer description of MyLocalLib

                       * Markdown format.
                       * Don't worry about the indent, we strip it!
                       DESC
  s.homepage         = "https://github.com/<GITHUB_USERNAME>/MyLocalLib"
  # s.screenshots     = "www.example.com/screenshots_1", "www.example.com/screenshots_2"
  s.license          = 'MIT'
  s.author           = { "wing.of.war.1980th" => "wing.of.war.1980th@gmail.com" }
  s.source           = { :git => "https://github.com/<GITHUB_USERNAME>/MyLocalLib.git", :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'
~~~

这部分是此类库的对外信息，很容易修改，*s.name*里的类库名称不要拼错，是与podfile中的引用名称所对应的。

~~~
  s.ios.deployment_target = '7.0'
  s.osx.deployment_target = '10.9'
  s.requires_arc = true
~~~

此部分是类库的使用平台信息，我这里不仅设置了iOS平台，也设置了Mac OS平台。

~~~
  s.public_header_files = '*.h'
  s.source_files = 'MyLocalLibHeader.h'
~~~

如果有此文件，将会做为此类库的公共头文件。这样在框架导入的时候，使用者可以在自己`MyProject-Prefix.pch`文件中直接引入此文件。

#### 重点部分

~~~
  s.subspec 'MyCategory' do |ss|
        ss.source_files = 'MyCategory/*.{h,m}'
        ss.dependency 'FMDB'
        # s.frameworks = 'UIKit', 'MapKit'
  end

  s.subspec 'DataCenter' do |ss|
        ss.source_files = 'MyTimeTool/*.{h,m}'
        # ss.dependency 'AFNetworking'
  end

  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'
  # s.dependency 'AFNetworking', '~> 2.3'
end
~~~

* s.subspec 'MyCategory' do |ss|
 应该是创建一个子模块，在项目中的体现是创建了一个文件夹，在此模块下的文件将保存于此文件夹下
* ss.source_files = 'MyCategory/*.{h,m}' 指定这些源文件的路径，这个是MyCategory模块，所以我们指向`MyCategory/`文件夹下的所有源文件，当然也可以直接指定具体文件，但肯定不如这种遍历的形式方便； 
* ss.dependency是依赖其它的Cocoapods的类库，这里为了演示，依赖了`FMDB`这个Sqlite处理框架
* s.frameworks应该是依赖的apple框架，我们这里都写的简单类库，就不再引用

OK，准备工作做完后，开始导入。这是我当前项目的文件夹结构。

![file struct](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/file%20struct.png)

***如果是用路径引用，则podfile中的路径一定要能指向类库的.podspec文件***
所以，将podfile更改为如下
~~~
target "MyProject" do
	pod "AFNetworking" 
    pod "MyLocalLib", :path => "../MyLocalLib"
end
~~~

`../MyLocalLib`表示的路径为上一级目录下的`MyLocalLib`文件，此文件下包括了.podspec

![import LocalLib](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/import%20LocalLib.png)

不仅安装了MyLocalLib框架，并且通过`ss.dependency 'FMDB'`导入了`FMDB`类库。打开工程文件，如下图所示

![Mylocallib insatll](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/Mylocallib%20insatll.png)

* 通过GitHub安装的MyPrivateLib框架已经移除
* MyLocalLib已经导入可以正常使用
* MyLocalLib保持了其原文件结构

## Step4 工作空间使用CocoaPoads

最后一个部分，既然我项目的核心功能部分独立出来，那我在多个平台上都可以用。所以我们在`MyProject\MyProject.xcworkspace`的工作空间下添加了一个Mac项目`MyMacProject`,此项目也将引用我们之前使用的`MyLocalLib`和`AFNetworking`

为了增加一点配置文件编写难度和保持项目的平级性，我们的文件结构是如下所示：

![MacProject file structure](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MacProject%20file%20structure.png)

`Podfile`的配置文件是位于`MyProject`下的。所以根据之前在导入`MyLocalLib`时所写的配置也会有些技巧。根据多处查找资料和研究别人的类库，是需要根据每一个工程中的Targe来建立与CocoaPods的关联，每一个target都要把相应的`*.xcodeproj`的位置在其中进行描述。同时我也对`target`所使用的平台进行了描述，免得在导入一些类库时引起一些版本上的限制。

~~~
workspace 'MyProject'

target 'MyProject' do
    platform :ios, '7.0'
	pod "AFNetworking" 
    pod "MyLocalLib", :path => "../MyLocalLib"
	xcodeproj 'MyProject.xcodeproj'
end

target 'MyMacProject' do
    platform :osx, '10.9'
	pod "AFNetworking" 
    pod "MyLocalLib", :path => "../MyLocalLib"
	xcodeproj '../MyMacProject/MyMacProject.xcodeproj'
end

target "MyProjectTests" do

end
~~~

在`MyProject\`目录下运行

~~~
pod install
~~~

命令。完成后，再打开`MyProject.xcworkspace`工作空间，已经如下图所示，在MyMacProject中导入`MyLocalLib`类库，一切正常～

![MacProject import MyLocalLib](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/master/_postsImages/2014/08/MacProject%20import%20MyLocalLib.png)
