---
layout: post
title: raywenderlich.com Objective-C代码规范中文简易注释
date: 2014-11-10
categories: cn
---

# The official raywenderlich.com Objective-C style guide.

This style guide outlines the coding conventions for raywenderlich.com.

*  这份代码规范由[raywenderlich.com](raywenderlich.com)编写，原文[Git](https://github.com/raywenderlich/objective-c-style-guide);
*  中文注释部分由 [wing.of.war.1980th@gmail](http://wing-of-war.github.io/) 编写于段落的加重部分;
*  中文主要是对代码规范要点解释，部分为补充说明。

<!-- more -->

## Introduction

The reason we made this style guide was so that we could keep the code in our books, tutorials, and starter kits nice and consistent - even though we have many different authors working on the books.

This style guide is different from other Objective-C style guides you may see, because the focus is centered on readability for print and the web. Many of the decisions were made with an eye toward conserving space for print, easy legibility, and tutorial writing.

## Credits 编写名单

The creation of this style guide was a collaborative effort from various raywenderlich.com team members under the direction of Nicholas Waynik.  The team includes: [Soheil Moayedi Azarpour](https://github.com/moayes), [Ricardo Rendon Cepeda](https://github.com/ricardo-rendoncepeda), [Tony Dahbura](https://github.com/tdahbura), [Colin Eberhardt](https://github.com/ColinEberhardt), [Matt Galloway](https://github.com/mattjgalloway), [Greg Heo](https://github.com/gregheo), [Matthijs Hollemans](https://github.com/hollance), [Christopher LaPollo](https://github.com/elephantronic), [Saul Mora](https://github.com/casademora), [Andy Pereira](https://github.com/macandyp), [Mic Pringle](https://github.com/micpringle), [Pietro Rea](https://github.com/pietrorea), [Cesare Rocchi](https://github.com/funkyboy), [Marin Todorov](https://github.com/icanzilb), [Nicholas Waynik](https://github.com/ndubbs), and [Ray Wenderlich](https://github.com/raywenderlich)

We would like to thank the creators of the [New York Times](https://github.com/NYTimes/objective-c-style-guide) and [Robots & Pencils'](https://github.com/RobotsAndPencils/objective-c-style-guide) Objective-C Style Guides.  These two style guides provided a solid starting point for this guide to be created and based upon.

## Background 参考

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Language](#language)
* [Code Organization](#code-organization)
* [Spacing](#spacing)
* [Comments](#comments)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Methods](#methods)
* [Variables](#variables)
* [Property Attributes](#property-attributes)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Literals](#literals)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Case Statements](#case-statements)
* [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Init Methods](#init-methods)
* [Class Constructor Methods](#class-constructor-methods)
* [CGRect Functions](#cgrect-functions)
* [Golden Path](#golden-path)
* [Error handling](#error-handling)
* [Singletons](#singletons)
* [Line Breaks](#line-breaks)
* [Smiley Face](#smiley-face)
* [Xcode Project](#xcode-project)


## Language

US English should be used.

* 使用美式英语和单词拼写，见过用法语和日语拼音的变量，识别困难。

**Preferred:**

~~~objc
UIColor *myColor = [UIColor whiteColor];
~~~

**Not Preferred:**

~~~objc
UIColor *myColour = [UIColor whiteColor];
~~~


## Code Organization 代码组织

Use `#pragma mark -` to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

* 使用 `#pragma mark -` 把相同功能的代码放入同一代码区中。

* 保持`.h`和`.m`文件内的代码分块一致与顺序一致。

示例：

~~~objc
#pragma mark - Lifecycle

- (instancetype)init {...}

- (void)dealloc {...}

- (void)viewDidLoad {...}

- (void)viewWillAppear:(BOOL)animated {...}

- (void)didReceiveMemoryWarning {...}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {...}

- (id)customProperty {...}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {...}

#pragma mark - Public

- (void)publicMethod {...}

#pragma mark - Private

- (void)privateMethod {...}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {...}

#pragma mark - NSObject

- (NSString *)description {...}

~~~

* 额外：如果有次级分组，使用 `#pragma mark `划分子代码区。

~~~objc

#pragma mark - Main Func

- (void)sync;

#pragma mark Add

- (void)add:(id)obj;
- (void)adds:(NSArray *)objs;

#pragma mark Update

- (void)update:(id)obj;
- (void)update:(NSArray *)objs;

~~~


## Spacing空格

* Indent using 2 spaces (this conserves space in print and makes line wrapping less likely). Never indent with tabs. Be sure to set this preference in Xcode.
    *  XCODE默认换行是4个空格，推 荐使用2个空格，让代码看起来更紧凑，
    *  设置方法:`xcode`->`preferences`->`Text Editing`->`Indentation`->`Indent width`。
   
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

    * 方法体`{}`的开始`{`与方法和`if`/`else`/`switch`/`while`控制流方法处于同一行，`}`则需要另起一行。

**Preferred:**

~~~objc
if (user.isHappy) {
  //Do something
} else {
  //Do something else
}
~~~

**Not Preferred:**

~~~objc
if (user.isHappy)
{
    //Do something
}
else {
    //Do something else
}
~~~

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
    * 方法之间应该保持一个空行，有助于保持清晰的结构。
    

* Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.
    * 推荐使用`auto-property-synthesis`自动生成属性的Set,Get方法；
    * 如果需要，每一个`@synthesize` 和 `@dynamic`都应该独占一行。
    
* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.
    * 多个回调模块，使用`{}`括号的对齐比使用 方法名中的`:`对齐要便于阅读。
    
**Preferred:**

~~~objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
  // something
} completion:^(BOOL finished) {
  // something
}];
~~~

**Not Preferred:**

~~~objc
// colon-aligning makes the block indentation hard to read
// 冒号对齐让block模块缩进难以阅读

[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
~~~

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. *Exception: This does not apply to those comments used to generate documentation.*

* 注释及时更新，或者直接删除；
* 只有在复杂的地方才需要；
* 代码如注释。

## Naming 命名

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

* 鼓励使用完整，并且有描述性的变量名和方法。

**Preferred:**

~~~objc
UIButton *settingsButton;
~~~

**Not Preferred:**

~~~objc
UIButton *setBut;
~~~

A three letter prefix should always be used for class names and constants, however may be omitted for Core Data entity names. For any official raywenderlich.com books, starter kits, or tutorials, the prefix 'RWT' should be used.

* 三个首字母的缩写仅用于类名和常量之中。（并且大写）

Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

* 驼峰命名。

**Preferred:**

~~~objc
static NSTimeInterval const RWTTutorialViewControllerNavigationFadeAnimationDuration = 0.3;
~~~

**Not Preferred:**

~~~objc
static NSTimeInterval const fadetime = 1.7;
~~~

Properties should be camel-case with the leading word being lowercase. Use auto-synthesis for properties rather than manual @synthesize statements unless you have good reason.

* 如果充分的理由，使用自动变量赋值方法，而不是自己实现`@synthesize`。

**Preferred:**

~~~objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
~~~

**Not Preferred:**

~~~objc
id varnm;
~~~

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

* 除了init初始化方法中使用`_variableName`来获取或者设置值之外，都应该使用`self.`来获取变量，实例中的值。

Local variables should not contain underscores.

* 本地变量不应该包含下划线。

## Methods

In method signatures, there should be a space after the method type (-/+ symbol). 

* 方法名和方法类型`（-/+）`之间有一个空格。

There should be a space between the method segments (matching Apple's style). 

* 方法片段间有空格。

Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

* 多个参数名前的方法要有描述性的关键字,并且不要包含**and**字段。

**Preferred:**

~~~objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
~~~

**Not Preferred:**

~~~objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
~~~

**Particular：参数名在方法的最后**

~~~objc
- (id)viewWithTag:(NSInteger)tag;
//Not Preferred
- (id)taggedView:(NSInteger)tag;
~~~



## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

* 除了在流控制方法中的临时变量，变量名应该是有一定描述性的。

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

* `*`与变量名紧靠在一起。

[Private properties](#private-properties) should be used in place of instance variables whenever possible. Although using instance variables is a valid way of doing things, by agreeing to prefer properties our code will be more consistent. 

Direct access to instance variables that 'back' properties should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

* 除`init`,`dealloc`，`set`,`get`等此类方法，应该尽量使用`setters`和`getters`。

**Preferred:**

~~~objc
@interface RWTTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
~~~

**Not Preferred:**

~~~objc
@interface RWTTutorial : NSObject {
  NSString *tutorialName;
}
~~~


## Property Attributes

Property attributes should be explicitly listed, and will help new programmers when reading the code.  The order of properties should be storage then atomicity, which is consistent with automatically generated code when connecting UI elements from Interface Builder.

* 变量申明时，原子属性在存储之后。

**Preferred:**

~~~objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
~~~

**Not Preferred:**

~~~objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
~~~

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`. 
Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

* 经常变更的变量更倾向于使用`copy`，而不是`strong`。

**Preferred:**

~~~objc
@property (copy, nonatomic) NSString *tutorialName;
~~~

**Not Preferred:**

~~~objc
@property (strong, nonatomic) NSString *tutorialName;
~~~

* 以下引用[NSString什么时候用copy，什么时候用strong](http://blog.csdn.net/itianyi/article/details/9018567)对此例进行说明。

~~~objc
@property (retain,nonatomic) NSString *rStr;
@property (copy, nonatomic)   NSString *cStr;

- (void)test:
{
    NSMutableString *mStr = [NSMutableStringstringWithFormat:@"abc"];
    self.rStr   = mStr;
    self.cStr     = mStr;
    NSLog(@"mStr:%p,%p",  mStr,&mStr);
    NSLog(@"retainStr:%p,%p", _rStr, &_rStr);
    NSLog(@"copyStr:%p,%p",   _cStr, &_cStr);
｝

/*
假如，mStr对象的地址为0x11，也就是0x11是@“abc”的首地址，mStr变量自身在内存中的地址为0x123；
当把mStr赋值给retain的rStr时，rStr对象的地址为0x11，rStr变量自身在内存中的地址为0x124；rStr与mStr指向同样的地址，他们指向的是同一个对象@“abc”，这个对象的地址为0x11，所以他们的值是一样的。
当把mStr赋值给copy的cStr时，cStr对象的地址为0x22，cStr变量自身在内存中的地址0x125；cStr与mStr指向的地址是不一样的，他们指向的是不同的对象，所以copy是深复制，一个新的对象，这个对象的地址为0x22，值为@“abc”。

如果现在改变mStr的值：
    [mStr appendString:@"de"];
    NSLog(@"retainStr:%@",  _rStr);
    NSLog(@"copyStr:%@",    _cStr);

结果，
使用retain的字串rStr的值：@"abcde",
而使用copy的字串cStr的值:@"abc",
所以，如果一般情况下，我们都不希望字串的值跟着mStr变化，所以我们一般用copy来设置string的属性。
如果希望字串的值跟着赋值的字串的值变化，可以使用strong，retain。
注意：上面的情况是针对于当把NSMutableString赋值给NSString的时候，才会有不同，如果是赋值是NSString对象，那么使用copy还是strong，结果都是一样的，因为NSString对象根本就不能改变自身的值，他是不可变的。

把一个对象赋值给一个属性变量，当这个对象变化了，如果希望属性变量变化就使用strong属性，如果希望属性变量不跟着变化，就是用copy属性。
*/
~~~



## Dot-Notation Syntax 点表达式 句法

Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Dot-notation should **always** be used for accessing and mutating properties, as it makes code more concise. Bracket notation is preferred in all other instances.

* 点表达式应该是用于获取或者改变属性

* 其它实例方法，都推荐使用`[]`**括号记法**。

**Preferred:**

~~~objc
NSInteger arrayCount = [self.array count];
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
~~~

**Not Preferred:**

~~~objc
NSInteger arrayCount = self.array.count;
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
~~~

## Literals 字面量

* Literals翻译成字面量或者字面值，就是直接被写到源代码中的值[Objective-C 之 Literals（字面量）](http://my.oschina.net/iamzkt/blog/127718)。

* 它的语法很简单，上面的代码就是通过在C字符串的前面加上@符号创建了一个NSString对象greeting，整段代码看起来简洁易懂，如果没有直接量语法，那么创建这个greeting可能就要使用下面的方法了：
~~~objc
NSString *greeting = [NSString stringWithUTF8String:"Hello World"];
~~~



`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values can not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Preferred:**

~~~objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
~~~

**Not Preferred:**

~~~objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
~~~

## Constants 常量

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

* 常量使用`static`与`const`创建，`#define`可以用于定义便利方法。

**Preferred:**

~~~objc
static NSString * const RWTAboutViewControllerCompanyName = @"RayWenderlich.com";

static CGFloat const RWTImageThumbnailHeight = 50.0;
~~~

**Not Preferred:**

~~~objc
#define CompanyName @"RayWenderlich.com"

#define thumbnailHeight 2
~~~

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`

* 推荐使用代码提示中宏定义的`NS_ENUM()`枚举模块来创建，它可以提供更严谨的类型检测和代码补完功能。

**For Example:**

~~~objc
typedef NS_ENUM(NSInteger, RWTLeftMenuTopItemType) {
  RWTLeftMenuTopItemMain,
  RWTLeftMenuTopItemShows,
  RWTLeftMenuTopItemSchedule
};
~~~

You can also make explicit value assignments (showing older k-style constant definition):

~~~objc
typedef NS_ENUM(NSInteger, RWTGlobalConstants) {
  RWTPinSizeMin = 1,
  RWTPinSizeMax = 5,
  RWTPinCountMin = 100,
  RWTPinCountMax = 500,
};
~~~

Older k-style constant definitions should be **avoided** unless writing CoreFoundation C code (unlikely).

* 传统的枚举则让代码看起来更像C。

**Not Preferred:**

~~~objc
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
~~~


## Case Statements 分支

Braces are not required for case statements, unless enforced by the complier.  
When a case contains more than one line, braces should be added.

* 每一个`case`分支下的执行语句，如果只有一行，不加`{}`；
* 如果`case`分支下的执行语句有多行，则使用`{}`划定范围。

~~~objc
switch (condition) {
  case 1:
    // ...
    break;
  case 2: {
    // ...
    // Multi-line example using braces
    break;
  }
  case 3:
    // ...
    break;
  default: 
    // ...
    break;
}

~~~

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

* 相同`case`分支的条件使用`条件继承 fall-through`，在上层`case`中不使用`break`跳出，直接继承下层`case`的执行语句。

~~~objc
switch (condition) {
  case 1:
    // ** fall-through! **
  case 2:
    // code executed for values 1 and 2
    break;
  default: 
    // ...
    break;
}

~~~

When using an enumerated type for a switch, 'default' is not needed.   For example:

* `default`条件非必需

~~~objc
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;

switch (menuType) {
  case RWTLeftMenuTopItemMain:
    // ...
    break;
  case RWTLeftMenuTopItemShows:
    // ...
    break;
  case RWTLeftMenuTopItemSchedule:
    // ...
    break;
}
~~~


## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `RWTPrivate` or `private`) should never be used unless extending another class.   The Anonymous category can be shared/exposed for testing using the <headerfile>+Private.h file naming convention.

* 私有变更应该在类的私有类别中，不需要加`private`等词语来进行修饰。

* 私有类别可以在命名为`<headerfile>+Private.h`的文件里提供

**For Example:**

~~~objc
@interface RWTDetailViewController ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
~~~

## Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. 

Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

* 不要把对象直接和`YES`进行比较，
* 可以覆写类继承自`NSOjbect`下的`-(BOOL)isEqual:(id)obj;`方法来类的比较。

This allows for more consistency across files and greater visual clarity.

**Preferred:**

~~~objc
if (someObject) {}
if (![anotherObject boolValue]) {}
~~~

**Not Preferred:**

~~~objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
~~~

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

* 如果`BOOL`变量是形容词，不需要**is**来进行修饰。

~~~objc
@property (assign, getter=isEditable) BOOL editable;
//**Not Preferred:**
//@property (assign, getter=isEditable) BOOL isEditable;

~~~

Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

* 一定使用`{}`来划定判断后的执行语句；

* 即使执行语非常简单，也不能与条件判断在一行。

**Preferred:**

~~~objc
if (!error) {
  return success;
}
~~~

**Not Preferred:**

~~~objc
if (!error)
  return success;
~~~

or

~~~objc
if (!error) return success;
~~~

### Ternary Operator 三元运算符

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. 

* 只有在确定能够促进代码整洁与清晰的前提下才使用。

A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an `if` statement, or refactored into instance variables. 

In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

* 三元运算符最好在赋值需要判断时使用。

Non-boolean variables should be compared against something, and parentheses are added for improved readability.  If the variable being compared is a boolean type, then no parentheses are needed.

* 非BOOL类型变量必须要与其类型变量做出判断后才能使用。

**Preferred:**

~~~objc
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
~~~

**Not Preferred:**

~~~objc
result = a > b ? x = c > d ? c : d : y;
~~~

## Init Methods

Init methods should follow the convention provided by Apple's generated code template.  A return type of 'instancetype' should also be used instead of 'id'.

* `init`方法中返回值使用`instancetype`取代`id`作为返回。

~~~objc
- (instancetype)init {
  self = [super init];
  if (self) {
    // ...
  }
  return self;
}
~~~

See [Class Constructor Methods](#class-constructor-methods) for link to article on instancetype.

## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

~~~objc
@interface Airplane
+ (instancetype)airplaneWithType:(RWTAirplaneType)type;
@end
~~~

More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

* 以下摘取自[Objective-C中的instancetype和id关键字](http://blog.csdn.net/wzzvictory/article/details/16994913)。

~~~
三、instancetype作用
1、作用
如果一个不是关联返回类型的方法，如下：

@interface NSArray  
+ (id)constructAnArray;  
@end  
当我们使用如下方式初始化NSArray时：


[NSArray constructAnArray];  
根据Cocoa的方法命名规范，得到的返回类型就和方法声明的返回类型一样，是id。
但是如果使用instancetype作为返回类型，如下：


@interface NSArray  
+ (instancetype)constructAnArray;  
@end  
当使用相同方式初始化NSArray时：

[NSArray constructAnArray];  
得到的返回类型和方法所在类的类型相同，是NSArray*!
总结一下，instancetype的作用，就是使那些非关联返回类型的方法返回所在类的类型！

2、好处
能够确定对象的类型，能够帮助编译器更好的为我们定位代码书写问题，比如：

[[[NSArray alloc] init] mediaPlaybackAllowsAirPlay]; //  "No visible @interface for `NSArray` declares the selector `mediaPlaybackAllowsAirPlay`"  
  
[[NSArray array] mediaPlaybackAllowsAirPlay]; // (No error)  
上例中第一行代码，由于[[NSArray alloc]init]的结果是NSArray*，这样编译器就能够根据返回的数据类型检测出NSArray是否实现mediaPlaybackAllowsAirPlay方法。有利于开发者在编译阶段发现错误。
第二行代码，由于array不属于关联返回类型方法，[NSArray array]返回的是id类型，编译器不知道id类型的对象是否实现了mediaPlaybackAllowsAirPlay方法，也就不能够替开发者及时发现错误。
~~~

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

* 使用`CGGeometry`方法来获取`CGRect`结构下的长宽位置值。

**Preferred:**

~~~objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
~~~

**Not Preferred:**

~~~objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
~~~

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

* 条件判断的左侧空间被称为**黄金路径**或者**幸福路径**；

* 减少`if`的条件的嵌套，扁平化多个返回条件。

* 函数的多个返回条件是可以接受的。

**Preferred:**

~~~objc
- (void)someMethod {
  if (![someOther boolValue]) {
	return;
  }

  //Do something important
}
~~~

**Not Preferred:**

~~~objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
~~~

**Demo:**

~~~objc
    if (text.length > 0) {
        WVContact* contact = [[WVContact alloc] initContact:text combName:text withLastModify:nil];
        if (contact) {
            [_sentTo addObject:contact];
            [self setSearchTableShow:NO];
            tokenField.inputText = @"";
            [tokenField reloadData];
       }
    } else {
        [_subjectTokenField becomeFirstResponder];
    }

//Rebuild:

    if (text.length <= 0) {
        [_subjectTokenField becomeFirstResponder];
        return;
    }
    WVContact* contact = [[WVContact alloc] initContact:text combName:text withLastModify:nil];
    if (!contact) {
        return;
    }
    [_sentTo addObject:contact];
    [self setSearchTableShow:NO];
    tokenField.inputText = @"";
    [tokenField reloadData];

~~~



## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

* 条件判断错误时，应该是错误变量本身。

**Preferred:**

~~~objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
~~~

**Not Preferred:**

~~~objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
~~~

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.

* 单例必须线程安全。


~~~objc
+ (instancetype)sharedInstance {
  static id sharedInstance = nil;

  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
~~~
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).


## Line Breaks

Line breaks are an important topic since this style guide is focused for print and online readability.

For example:

~~~objc
self.productsRequest = [[SKProductsRequest alloc] initWithProductIdentifiers:productIdentifiers];
~~~
A long line of code like this should be carried on to the second line adhering to this style guide's Spacing section (two spaces).

* 因为代码太长需要换行时，与首行有两个空格间隔；
* 设置方法：`xcode`->`preferences`->`Text Editing`->`Indentation`->`Line Wrapping` 手动设置为`2`。


~~~objc
self.productsRequest = [[SKProductsRequest alloc] 
  initWithProductIdentifiers:productIdentifiers];
~~~


## Smiley Face

Smiley faces are a very prominent style feature of the raywenderlich.com site!  It is very important to have the correct smile signifying the immense amount of happiness and excitement for the coding topic.  The end square bracket is used because it represents the largest smile able to be captured using ascii art.  A half-hearted smile is represented if an end parenthesis is used, and thus not preferred.

* 使用`]`看起来比`)`笑得更开心

**Preferred:**

~~~objc
:]
~~~

**Not Preferred:**

~~~objc
:)
~~~  


## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [Robots & Pencils](https://github.com/RobotsAndPencils/objective-c-style-guide)
* [New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
