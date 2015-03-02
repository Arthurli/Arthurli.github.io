---
title: Swift 网络
layout: post
---
#SwiftHTTP

这个类库是一个极端轻量级的HTTP链接库，最大的缺点就是还有很多功能没有实现，但是如果只是用作最简单的网络操作，我想也应该是够了   
这个HTTP类库只能实现Session的网络链接方法  

首先拥有一个HTTPTask的管理类，来调用不同的网络请求  
它实现异步的方法就是封装一个NSOperation,里面拥有一个NSURLSessionDataTask来具体执行网络请求

它使用了HTTPRequestSerializer和HTTPResponseSerializer来生成reuqest和解析response  

在这里标记一个实用的解析MIME的方法

~~~
func updateMimeType() {
        if mimeType == nil && fileUrl != nil {
            var UTI = UTTypeCreatePreferredIdentifierForTag(kUTTagClassFilenameExtension, fileUrl?.pathExtension as NSString?, nil);
            var str = UTTypeCopyPreferredTagWithClass(UTI.takeUnretainedValue(), kUTTagClassMIMEType);
            if (str == nil) {
                mimeType = "application/octet-stream";
            } else {
                mimeType = str.takeUnretainedValue() as NSString
            }
        }
    }

~~~
值得一说的就是生成request的配置类，里面主要针对传入的参数和Method生成不同的request，比较简单清晰

#Alamofire
其实这是我很久之前就开头的，但是这么久就一直没有时间再继续写了，但是今天我想着把它简答的补全，当然是把最重要的类库写上，偶，还记得AFNetwork吗，就是那位大神，他在Swift上又写了一个极其简单实用的库 Alamofire， 我今天会相对详细的去解析一下这个库， 并且会补充一些遇到的swift问题.  

在类库最上方就定义了两个对整个类库都十分重要的枚举类

~~~
public enum Method: String 
//这个枚举定义了不同的请求方法

public enum ParameterEncoding 
//这个枚举定义了不同的编码方式
//这个枚举中还拥有几个方法 实现了转义 编码 生成NSURLRequest的方法
//很重要

public func encode(URLRequest: URLRequestConvertible, parameters: [String: AnyObject]?) -> (NSURLRequest, NSError?)
//这里的URLRequestConvertible是一个协议，如果传进来的是一个request那么会根据原来的method返回，否则默认为POST
    
~~~

照例，会有一个manager管理类，但是swift的单例说实话，看起来很怪

~~~
 public class var sharedInstance: Manager {
        struct Singleton {
            static var configuration: NSURLSessionConfiguration = {
                var configuration = NSURLSessionConfiguration.defaultSessionConfiguration()
                configuration.HTTPAdditionalHeaders = Manager.defaultHTTPHeaders()

                return configuration
            }()

            static let instance = Manager(configuration: configuration)
        }

        return Singleton.instance
    }
~~~

在Swift中定义私有和共有是这么做的

~~~
private let delegate: SessionDelegate

private let queue = dispatch_queue_create(nil, DISPATCH_QUEUE_SERIAL)

/// The underlying session.
public let session: NSURLSession

/// Whether to start requests immediately after being constructed. `true` by default.
public var startRequestsImmediately: Bool = true

~~~

在函数中写默认参数是这么写的

~~~
public func request(method: Method, _ URLString: URLStringConvertible, parameters: [String: AnyObject]? = nil, encoding: ParameterEncoding = .URL) -> Request
~~~

在manager中定义了一个挺重要的内部类 `SessionDelegate`  
这个类主要实现了以下几个协议的代理 ` NSURLSessionDelegate, NSURLSessionTaskDelegate, NSURLSessionDataDelegate, NSURLSessionDownloadDelegate` 这些代理都是由manager的私有属性 实现的

我们要在这里面实现我们自己的方法 就需要设置代理的方法 还有就是如果我们没有设置方法，delegate在实现的时候是这样的  

~~~
downloadTaskDidResumeAtOffset?(session, downloadTask, fileOffset, expectedTotalBytes)

~~~

当我们想设置下角标语法的时候需要这么做

~~~
private subscript(task: NSURLSessionTask) -> Request.TaskDelegate? {
       get {
           var subdelegate: Request.TaskDelegate?
           dispatch_sync(subdelegateQueue) {
               subdelegate = self.subdelegates[task.taskIdentifier]
           }

           return subdelegate
       }
       
       set {
           dispatch_barrier_async(subdelegateQueue) {
               self.subdelegates[task.taskIdentifier] = newValue
           }
       }
   }
~~~


这里还有一个技巧 就是实现log协议，对打印的内容进行设置  

~~~
extension Request: Printable {

}

extension Request: DebugPrintable {

}
~~~

这个类库针对download和upload还做了一些列的优化，简化和区别的操作