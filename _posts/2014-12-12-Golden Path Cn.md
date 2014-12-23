---
layout: post
title: Golden Path
date: 2014-12-12
categories: cn
---

### 起源

上个月的时候，公司要我整理一份iOS的代码规范给大家一起学习和使用。当然自己整理是不太可能的，所以找了一份靠谱的并且自己详细阅读与注释后给大家进行了分享。

<!-- more -->

其中有一个章节的内容从分享那一天之后深刻的影响了。此章节很短，所以我直接粘贴在下面。

![](https://raw.githubusercontent.com/Wing-Of-War/wing-of-war.github.com/814f41347a8c6d711b04df58d0a40ae6f7d979ee/_postsImages/2014/12/3.png)

Golden Path, 非常好听名字。初次阅读此代码规范的时候我倒也没有太在意，想着只是有个好听的名字就花了三分钟到了下一章节。等到再和公司一有经验的同事一起商量的时候觉得这不仅仅是一个代码规范，更是写出好代码的Golden Key。

### 简单的理念与规则

如果你的代码开始处离你的左侧边框越来越远，整个代码像`>`变得越来越深的时候，就会像一个**深坑**一样散发出坏代码的气味。而好代码的应该是更倾向于`]`这种形状的。


当然，经过一个多月的执行与演化，我个人深化之后总结如下：

1. 每一个方法内，只处理一件最重要的事情`most important thing`；
2. 将与这个`most important thing`关系不紧密的部分，提炼成为一个或者多个独立方法进行引用，或者作为执行`most important thing`的条件；
3. 将执行此方法的判断条件放在前面，排除掉所有不能执行此方法的情况，最后执行`something important `。
4.  多个return的返回是可以接受的。当然，是做为不执行此方法的返回。

### 举一个实例

这是逻辑处理层对于UI与数据库建立搜索联系的方法：

~~~objc
- (void)searchContactByEmail:(NSString *email)email withCallback:(void (^)(ContactData *contact))callback{	
	if (emai.length>0 && [emall rangeOfString:@"@"].length > 0 ) {//1、判断查询的email有效性
		if (self.sharedDB) {//2、判断数据库存在
			ContactData *result =  [self.sharedDB getContactByEmail:email];//3、数据库中查询联系人
			if (callback) {//4、判断回调是否存在
                    callback(result);//5、进行回调
			}
		}
	}
}
~~~

根据之前描述，如此一个简单的方法在`callback(result);//5、进行回调 `处已经处于至少5个`Tab`的位置，20个`Space`的深度，感观上就非常差。逻辑判断也相互嵌套，重点位置不明确。

详细分析原代码：

1.  联系人email的有效性判断独立成方法`- (BOOL)checkEmailAvailable:(NSString *)email `，这样如果需要改进判断email有效性比如添加正则匹配的时候只用修改此一；
2.  判断数据库是否存在应该判断优先级比判断email方法高；
3.  在数据库中查询联系人应该是这个方法最重要的一步，应该将其放在此方法靠后的位置；
4.  回调方法应该是整个方法运行的前提条件，如果都不需要进行回调返回，那整个方法都不需要运行。
5.  回调的位置在最后不变。


经过整理后，代码应该至少是这个样子：

~~~objc
- (void)searchContactByEmail:(NSString *email)email withCallback:(void (^)(ContactData *contact))callback{
	if (!callback) {
		return;
	}
	if (!self.sharedDB) {
		return;
	}
	if (![self checkEmailAvailable:email]) {
		return;
	}
	ContactData *result = [self.sharedDB getContactByEmail:email];
	callback(result);
}

- (BOOL)checkEmailAvailable:(NSString *)email {
	if (!email.length) {
		return NO;
	}
	if (![email rangeOfString:@"@"].length) {
		return NO;
	}
	return YES;
}
~~~


### 再补充一点我个人在iOS开发上的理解：

一般的移动客户端上基本上也就是UI调用，逻辑运算，流程控制，网络请求访问等，那么就可以将每一个方法尽可能写得简单，也起到每一个方法注释了一部分的功能 。在引用起来起到方法既是注释的作用。

我自己之前见过一定要用到if大量嵌套的也就是一些算法，比如快速排序，冒泡排序等，或者是图片渲染，AI计算等有大量不可避免的条件判断。
