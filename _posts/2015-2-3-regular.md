---
title: Objective-C-RegEx-Categories 正则表达式
layout: post
---

#NSRegularExpression
这是一个使用正则表达式解析字符串的类，下面是他的一些options

~~~
struct NSRegularExpressionOptions : RawOptionSetType {
    init(_ rawValue: UInt)
    init(rawValue: UInt)
    
    static var CaseInsensitive: NSRegularExpressionOptions { get } 
    /* Match letters in the pattern independent of case. */
    static var AllowCommentsAndWhitespace: NSRegularExpressionOptions { get } 
    /* Ignore whitespace and #-prefixed comments in the pattern. */
    static var IgnoreMetacharacters: NSRegularExpressionOptions { get } 
    /* Treat the entire pattern as a literal string. */
    static var DotMatchesLineSeparators: NSRegularExpressionOptions { get }
     /* Allow . to match any character, including line separators. */
    static var AnchorsMatchLines: NSRegularExpressionOptions { get } 
    /* Allow ^ and $ to match the start and end of lines. */
    static var UseUnixLineSeparators: NSRegularExpressionOptions { get } 
    /* Treat only \n as a line separator (otherwise, all standard line separators are used). */
    static var UseUnicodeWordBoundaries: NSRegularExpressionOptions { get }
     /* Use Unicode TR#29 to specify word boundaries (otherwise, traditional regular expression word boundaries are used). */
}
~~~

下面是每次扫描的options

~~~
struct NSMatchingOptions : RawOptionSetType {
    init(_ rawValue: UInt)
    init(rawValue: UInt)
    
    static var ReportProgress: NSMatchingOptions { get } 
    /* Call the block periodically during long-running match operations. */
    static var ReportCompletion: NSMatchingOptions { get } 
    /* Call the block once after the completion of any matching. */
    static var Anchored: NSMatchingOptions { get }
     /* Limit matches to those at the start of the search range. */
    static var WithTransparentBounds: NSMatchingOptions { get } 
    /* Allow matching to look beyond the bounds of the search range. */
    static var WithoutAnchoringBounds: NSMatchingOptions { get }
     /* Prevent ^ and $ from automatically matching the beginning and end of the search range. */
}

~~~

###NSTextCheckingResult
这是一个搜索返回的结果类

#Objective-C-RegEx-Categories
这个库就是NSRegularExpression的拓展类别  
它定义了两个用来设置搜索参数和返回搜索结果，主要是对NSTextCheckingResult的封装

~~~
@interface RxMatch : NSObject
@property (nonatomic, copy)     NSString* value;    /* The substring that matched the expression. */
@property (nonatomic, assign)   NSRange   range;    /* The range of the original string that was matched. */
@property (nonatomic, copy)     NSArray*  groups;   /* Each object is an RxMatchGroup. */
@property (nonatomic, copy)     NSString* original; /* The full original string that was matched against.  */
@end


@interface RxMatchGroup : NSObject
@property (nonatomic, copy)   NSString* value;
@property (nonatomic, assign) NSRange range;
@end
~~~
当让，这个库里面还有一个NSString的类别，几乎是与NSRegularExpression类别一一对应的

~~~
//直接匹配字符
- (BOOL) isMatch:(NSString*)matchee
//直接返回匹配位置
- (int) indexOf:(NSString*)matchee
//简单替换
- (NSString*) replace:(NSString*)string with:(NSString*)replacement
//返回第一个匹配字符串
- (NSString*) firstMatch:(NSString*)str
~~~

#正则表达式

[半小时正则](http://deerchao.net/tutorials/regex/regex.htm)

