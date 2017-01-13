![Alamofire: Elegant Networking in Swift](https://raw.githubusercontent.com/Alamofire/Alamofire/assets/alamofire.png)

[![Build Status](https://travis-ci.org/Alamofire/Alamofire.svg?branch=master)](https://travis-ci.org/Alamofire/Alamofire)
[![CocoaPods Compatible](https://img.shields.io/cocoapods/v/Alamofire.svg)](https://img.shields.io/cocoapods/v/Alamofire.svg)
[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/cocoapods/p/Alamofire.svg?style=flat)](http://cocoadocs.org/docsets/Alamofire)
[![Twitter](https://img.shields.io/badge/twitter-@AlamofireSF-blue.svg?style=flat)](http://twitter.com/AlamofireSF)

##[English](README.md)

Alamofire 是 Swift 语言编写的 HTTP 网络库。

- [特性](#特性)
- [组件](#组件)
- [环境需求](#环境需求)
- [移植指南](#移植指南)
- [安装](#安装)
- [用法](#用法)
    - **简介 -** [发起请求](#发起请求), [响应回调](#响应回调), [响应验证](#响应验证), [响应缓存](#响应缓存)
        - **HTTP -** [HTTP 方法](#HTTP 方法), [请求参数编码](#请求参数编码), [HTTP Headers](#http-headers), [认证](#认证)
        - **大量数据 -** [下载数据到文件](#下载数据到文件), [上传数据到服务器](#上传数据到服务器)
        - **工具 -** [指标统计](#指标统计), [cURL 命令输出](#cURL 命令输出)
    - [高级用法](#高级用法)
        - **URL 会话 -** [会话管理](#会话管理), [会话代理](#会话代理), [请求](#请求)
        - **请求路由 -** [请求路由](#请求路由), [Adapting and Retrying Requests](#adapting-and-retrying-requests)
        - **模型对象 -** [自定义响应序列化器](#自定义响应序列化器)
        - **网络连接 -** [安全性](#安全), [网络可用性](#网络可用性)
- [Open Radars](#open-radars)
- [FAQ](#faq)
- [致谢](#致谢)
- [捐款](#捐款)
- [开源协议](#开源协议)

## 特性

- [x] 链式请求 / 响应方法调用
- [x] URL / JSON / plist 请求参数编码
- [x] 上传 File / Data / Stream / MultipartFormData
- [x] 文件下载和断点续传
- [x] URLCredential 认证方式
- [x] HTTP 响应验证
- [x] 上传下载进度
- [x] cURL 命令输出
- [x] Dynamically Adapt and Retry Requests
- [x] TLS Certificate and Public Key Pinning
- [x] 网络可用性
- [x] 测试单元和集成测试

## 组件

为了让 Alamofire 集中于核心网络操作的实现，[Alamofire Software Foundation](https://github.com/Alamofire/Foundation) 采用组件的形式为 Alamofire 添加额外的功能。

- [AlamofireImage](https://github.com/Alamofire/AlamofireImage) - 一个包含图片响应序列化, `UIImage` 和 `UIImageView` 的扩展，自定义图片过滤器，自动清理的内存缓存，基于优先级的图片下载的图片库。
- [AlamofireNetworkActivityIndicator](https://github.com/Alamofire/AlamofireNetworkActivityIndicator) - 控制网络活动指示器的显示。允许用户配置延迟时间来延缓活动指示器的显示，同时也支持独立创建的 `URLSession` 实例对象。

## 环境需求

- iOS 8.0+ / macOS 10.10+ / tvOS 9.0+ / watchOS 2.0+
- Xcode 8.1+
- Swift 3.0+

## 移植指南

- [Alamofire 4.0 移植指南](https://github.com/Alamofire/Alamofire/blob/master/Documentation/Alamofire%204.0%20Migration%20Guide.md)
- [Alamofire 3.0 移植指南](https://github.com/Alamofire/Alamofire/blob/master/Documentation/Alamofire%203.0%20Migration%20Guide.md)
- [Alamofire 2.0 移植指南](https://github.com/Alamofire/Alamofire/blob/master/Documentation/Alamofire%202.0%20Migration%20Guide.md)

##安装

### Cocoapods

[CocoaPods](http://cocoapods.org) 是一款 Cocoa 项目的依赖库管理工具。使用下面命令安装 cocoapods：

```bash
$ gem install cocoapods
```

> 编译 Alamofire 4.0.0+ 需要 1.1.0 以上版本的 cocoapods

在 `Podfile` 中进行声明来集成 Alamofire：

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '10.0'
use_frameworks!

target '<Your Target Name>' do
    pod 'Alamofire', '~> 4.0'
end
```

然后运行下面的命令进行安装：

```bash
$ pod install
```

### Carthage

[Carthage](https://github.com/Carthage/Carthage) 是一款编译管理依赖库的工具，可提供编译好的 frameworks。

通过 [Homebrew](http://brew.sh/) 使用一下命令安装 Carthage：

```bash
$ brew update
$ brew install carthage
```

在 `Cartfile` 中进行声明来集成 Alamofire：

```ogdl
github "Alamofire/Alamofire" ~> 4.0
```

运行 `carthage update` 命令进行编译，并把编译好的 `Alamofire.framework` 拖拽到你的 Xcode 项目中。

## 用法

### 发起请求

```swift
import Alamofire

Alamofire.request("https://httpbin.org/get")
```

### 响应回调

处理请求的响应只需在 `Request` 后面加上处理响应的回调。

```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
    print(response.request)  // original URL request
    print(response.response) // HTTP URL response
    print(response.data)     // server data
    print(response.result)   // result of response serialization

    if let JSON = response.result.value {
        print("JSON: \(JSON)")
    }
}
```

上面的例子中，`responseJSON` 回调拼接在 `Request` 后面，一旦网络请求完成便会执行该 `responseJSON`。这里没有阻塞线程来等待响应，而是采用了闭包形式的[回调](http://en.wikipedia.org/wiki/Callback_%28computer_programming%29)来异步接受响应。请求的结果只能在响应的闭包中进行处理。对响应或从服务器接收到的数据只能在响应闭包中处理。

> Alamofire 中网络请求是异步处理的。对异步编程相关的概念不太熟悉的话会让人一头雾水，但有太多的[理由](https://developer.apple.com/library/ios/qa/qa1693/_index.html)让我们采用异步编程。

Alamofire 默认包含五种响应回调：

```swift
// Response Handler - Unserialized Response
func response(
    queue: DispatchQueue?,
    completionHandler: @escaping (DefaultDataResponse) -> Void)
    -> Self

// Response Data Handler - Serialized into Data
func responseData(
    queue: DispatchQueue?,
    completionHandler: @escaping (DataResponse<Data>) -> Void)
    -> Self

// Response String Handler - Serialized into String
func responseString(
    queue: DispatchQueue?,
    encoding: String.Encoding?,
    completionHandler: @escaping (DataResponse<String>) -> Void)
    -> Self

// Response JSON Handler - Serialized into Any
func responseJSON(
    queue: DispatchQueue?,
    completionHandler: @escaping (DataResponse<Any>) -> Void)
    -> Self

// Response PropertyList (plist) Handler - Serialized into Any
func responsePropertyList(
    queue: DispatchQueue?,
    completionHandler: @escaping (DataResponse<Any>) -> Void))
    -> Self
```

这些回调都没有对 `HTTPURLResponse` 进行验证。

> 比如，`400..499` 和 `500..599` 之间的响应状态码不会自动触发 `Error`。Alamofire 采用 [Response Validation](#response-validation) 的链式方法来进行验证。

#### Response 回调

`response` 不对响应数据进行任何处理，直接把 URL session 代理中的数据交给后面的流程，与使用 `cURL` 执行请求效果一样。


```swift
Alamofire.request("https://httpbin.org/get").response { response in
    print("Request: \(response.request)")
    print("Response: \(response.response)")
    print("Error: \(response.error)")

    if let data = response.data, let utf8Text = String(data: data, encoding: .utf8) {
      print("Data: \(utf8Text)")
    }
}
```

强烈建议使用其他响应序列化器将数据变为更易使用的 `Response` 和 `Result` 类型

#### Response Data 回调

`responseData` 回调使用 `responseDataSerializer` (该对象用于将从服务器接收的数据序列化为其他类型) 处理服务器端返回的 `Data`。如果没有错误就返回 `Data`，response 的 `Result` 会被设置为 `.success` `value` 会被设置为 `Data` 类型。

```swift
Alamofire.request("https://httpbin.org/get").responseData { response in
    debugPrint("All Response Info: \(response)")

    if let data = response.result.value, let utf8Text = String(data: data, encoding: .utf8) {
      print("Data: \(utf8Text)")
    }
}
```

#### Response String 回调

`responseString` 回调使用 `responseStringSerializer` 根据指定的编码方式将从服务器接收到的 `Data` 转换为 `String` 类型。如果没有错误并且数据被成功序列化为 `String`，则 response 的 `Result` 会被设置为 `.success`，`value` 会被设置为 `String` 类型。

```swift
Alamofire.request("https://httpbin.org/get").responseString { response in
    print("Success: \(response.result.isSuccess)")
    print("Response String: \(response.result.value)")
}
```

> 如果未指定编码方式，Alamofire 会根据从服务器端接收到的 `HTTPURLResponse` 中的编码方式进行编码。如果响应中也未指定编码方式，默认使用 `.isoLatin1` 编码方式

#### Response JSON 回调

`responseJSON` 使用 `responseJSONSerializer` 根据指定的 `JSONSerialization.ReadingOptions` 将从服务器接收到的数据 `Data` 转换为 `Any` 类型。如果没有错误并且接收到的数据成功的序列化为 JSON 对象，则 response 的 `Result` 会被设置为 `.success` 并且 `value` 会被设置为 `Any` 类型。

```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
    debugPrint(response)

    if let json = response.result.value {
        print("JSON: \(json)")
    }
}
```

> JSON 的序列化由 `Foundation` 框架中的 `JSONSerialization` 接口完成。

#### 链式响应回调

响应回调也可以链式调用：

```swift
Alamofire.request("https://httpbin.org/get")
    .responseString { response in
        print("Response String: \(response.result.value)")
    }
    .responseJSON { response in
        print("Response JSON: \(response.result.value)")
    }
```

> 注意：对同一个请求使用多个响应回调需要服务器对数据进行多次序列化，每次序列化针对一个响应回调。

#### 响应回调的操作队列

响应回调默认是在主派发队列中执行。然而可以为响应回调指定自定义的操作队列。

```swift
let utilityQueue = DispatchQueue.global(qos: .utility)

Alamofire.request("https://httpbin.org/get").responseJSON(queue: utilityQueue) { response in
    print("Executing response handler on utility queue")
}
```

### 响应验证

默认情况下 Alamofire 会忽略响应内容是否正确，只要请求完成就标志着成功。在响应回调调用之前调用 `validata` 时，若响应中有错误的网络状态码或错误的 MIME 格式的数据则会抛出错误。

#### 手动验证


```swift
Alamofire.request("https://httpbin.org/get")
    .validate(statusCode: 200..<300)
    .validate(contentType: ["application/json"])
    .responseData { response in
      switch response.result {
      case .success:
          print("Validation Successful")
      case .failure(let error):
          print(error)
      }
    }
```

#### 自动验证

自动验证会验证 `200...299` 之间的状态码并验证响应数据的 `Content-Type` 是否和请求头的指定的 `Accept` 类型是否匹配。

```swift
Alamofire.request("https://httpbin.org/get").validate().responseJSON { response in
    switch response.result {
    case .success:
        print("Validation Successful")
    case .failure(let error):
        print(error)
    }
}
```

### 响应缓存

响应的缓存操作由系统级框架 [`URLCache`](https://developer.apple.com/reference/foundation/urlcache) 完成。其同时提供了内存，硬盘两种缓存方式并且用户可以设置可缓存的大小。

> Alamofire 默认会使用共享的 `URLCache`。查看 [Session Manager Configurations](#session-manager) 进行自定义。

### HTTP 方法

`HTTPMethod` 枚举出了 [RFC 7231 §4.3](http://tools.ietf.org/html/rfc7231#section-4.3) 中定义的 HTTP 方法：

```swift
public enum HTTPMethod: String {
    case options = "OPTIONS"
    case get     = "GET"
    case head    = "HEAD"
    case post    = "POST"
    case put     = "PUT"
    case patch   = "PATCH"
    case delete  = "DELETE"
    case trace   = "TRACE"
    case connect = "CONNECT"
}
```

可以为 `Alamofire.request` 接口的 `method` 参数设置这些值：

```swift
Alamofire.request("https://httpbin.org/get") // method defaults to `.get`

Alamofire.request("https://httpbin.org/post", method: .post)
Alamofire.request("https://httpbin.org/put", method: .put)
Alamofire.request("https://httpbin.org/delete", method: .delete)
```

> `Alamofire.request` 的 method 参数默认是 `.get`。

### 请求参数编码

Alamofire 默认提供了三种参数编码方式，包括 `URL`,`JSON`,`PropertyList`。同时也支持遵循了 `ParameterEncoding` 协议的编码方式。

#### URL 编码

`URLEncoding` 编码方式创建了 url 编码的查询字符串并将其拼接到存在的请求字符串后或者设置为 URL 请求的 HTTP body。对于编码后的查询字符串，是直接使用，拼接还是设置为 HTTP body 取决于编码的 `Destination`。`Destination` 包含三种方式：

- `.methodDependent` - 若请求方式是 `GET`,`HEAD`,`DELETE`,则将编码的查询字符串与存在的查询字符串进行拼接，对于其他请求方式则设置为请求的 HTTP body。
- `.queryString` - 将编码的查询字符串与存在的查询字符串进行拼接。
- `.httpBody` - 设置为请求的 HTTP body。

请求头中的 `Content-Type` 字段被设置为 `application/x-www-form-urlencoded; charset=utf-8。URL 编码中并没有规定集合类型该如何进行编码。我们约定，对数组类型将 `[]` 拼接到 key 后面(`foo[]=1&foo[]=2`)，对字典类型将中括号包围的 key 拼接在请求的键后(`foo[bar]=baz`)。

###### 获取使用 URL 编码参数的请求

```swift
let parameters: Parameters = ["foo": "bar"]

// All three of these calls are equivalent
Alamofire.request("https://httpbin.org/get", parameters: parameters) // encoding defaults to `URLEncoding.default`
Alamofire.request("https://httpbin.org/get", parameters: parameters, encoding: URLEncoding.default)
Alamofire.request("https://httpbin.org/get", parameters: parameters, encoding: URLEncoding(destination: .methodDependent))

// https://httpbin.org/get?foo=bar
```

##### 发起使用 URL 编码参数的请求

```swift
let parameters: Parameters = [
    "foo": "bar",
    "baz": ["a", 1],
    "qux": [
        "x": 1,
        "y": 2,
        "z": 3
    ]
]

// All three of these calls are equivalent
Alamofire.request("https://httpbin.org/post", parameters: parameters)
Alamofire.request("https://httpbin.org/post", parameters: parameters, encoding: URLEncoding.default)
Alamofire.request("https://httpbin.org/post", parameters: parameters, encoding: URLEncoding.httpBody)

// HTTP body: foo=bar&baz[]=a&baz[]=1&qux[x]=1&qux[y]=2&qux[z]=3
```

#### JSON 编码

`JSONEncoding` 的编码方式创建了 JSON 格式的请求参数，并设置为请求的 HTTP body。HTTP 请求头的 `Content-Type` 字段设置为 `applicatioin/json`。

##### 发起使用 JSON 编码参数的请求

```swift
let parameters: Parameters = [
    "foo": [1,2,3],
    "bar": [
        "baz": "qux"
    ]
]

// Both calls are equivalent
Alamofire.request("https://httpbin.org/post", method: .post, parameters: parameters, encoding: JSONEncoding.default)
Alamofire.request("https://httpbin.org/post", method: .post, parameters: parameters, encoding: JSONEncoding(options: []))

// HTTP body: {"foo": [1, 2, 3], "bar": {"baz": "qux"}}
```

#### 自定义编码

当 Alamofire 提供的参数编码方式不能满足需求时，可以创建自定义的编码方式。下面是一个自定义的 `JSONStringEncoding` 编码方式的例子，该方式将 string 数组的 JSON 对象编码到 `Request` 中。

```swift
struct JSONStringArrayEncoding: ParameterEncoding {
  private let array: [String]

    init(array: [String]) {
        self.array = array
    }

    func encode(_ urlRequest: URLRequestConvertible, with parameters: Parameters?) throws -> URLRequest {
        var urlRequest = urlRequest.urlRequest

        let data = try JSONSerialization.data(withJSONObject: array, options: [])

        if urlRequest.value(forHTTPHeaderField: "Content-Type") == nil {
            urlRequest.setValue("application/json", forHTTPHeaderField: "Content-Type")
        }

        urlRequest.httpBody = data

        return urlRequest
    }
}
```

#### 下载数据到文件

Alamofire 从服务器接收到的数据保存在缓存或硬盘上。到目前为止所有例子中使用 `Alamofire.request` 接口获取的数据都保存在缓存中。对于小数据这是很高效的，但对于较大的数据量可能会耗尽缓存。因此需要使用 `Alamofire.download` 接口将数据保存在硬盘的临时文件中。

```swift
Alamofire.download("https://httpbin.org/image/png").responseData { response in
  if let data = response.result.value {
      let image = UIImage(data: data)
  }
}
```

> 当需要在后台下载数据时也应该使用 `Alamofire.download` 接口。更多信息请查看 [Session Manager Configurations](#session-manager) 章节

#### 下载路径

你可以提供一个 `DownloadFileDestination` 闭包用于把临时文件移动到指定的路径下。在移动临时文件前会先执行闭包中指定的 `DownloadOptioins`。当前支持的两种 `DownloadOptions` 分别是：

- `.createIntermediateDirectories` - 为指定的路径创建完整的路径
- `.removePreviousFile` - 移除目标路径下存在的文件

```swift
let destination: DownloadRequest.DownloadFileDestination = { _, _ in
  let documentsURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
  let fileURL = documentsURL.appendPathComponent("pig.png")

    return (fileURL, [.removePreviousFile, .createIntermediateDirectories])
}

Alamofire.download(urlString, to: destination).response { response in
    print(response)

  if response.error == nil, let imagePath = response.destinationURL?.path {
      let image = UIImage(contentsOfFile: imagePath)
  }
}
```

也可以使用推荐下载路径 API。

```swift
let destination = DownloadRequest.suggestedDownloadDestination(directory: .documentDirectory)
Alamofire.download("https://httpbin.org/image/png", to: destination)
```

#### 下载进度

在下载时能够报告下载进度是非常有用的。任何 `DownloadRequest` 请求可以通过 `downloadProgress` 接口报告下载进度。

```swift
Alamofire.download("https://httpbin.org/image/png")
    .downloadProgress { progress in
        print("Download Progress: \(progress.fractionCompleted)")
    }
    .responseData { response in
      if let data = response.result.value {
          let image = UIImage(data: data)
      }
    }
```

也可以为 `downloadProgress` 接口指定下载进度闭包执行的派发队列。

```swift
let utilityQueue = DispatchQueue.global(qos: .utility)

Alamofire.download("https://httpbin.org/image/png")
    .downloadProgress(queue: utilityQueue) { progress in
        print("Download Progress: \(progress.fractionCompleted)")
    }
    .responseData { response in
      if let data = response.result.value {
          let image = UIImage(data: data)
      }
    }
```

#### 唤醒下载

如果一个 `DownloadRequest` 请求取消或中断了，URL 会话可能会为该请求生成恢复数据，该恢复数据可用于 `DownloadRequest` 请求从中断的地方恢复下载。恢复数据可以从下载响应中获取，然后用于恢复下载。


```swift
class ImageRequestor {
  private var resumeData: Data?
  private var image: UIImage?

    func fetchImage(completion: (UIImage?) -> Void) {
      guard image == nil else { completion(image) ; return }

    let destination: DownloadRequest.DownloadFileDestination = { _, _ in
      let documentsURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
      let fileURL = documentsURL.appendPathComponent("pig.png")

        return (fileURL, [.removePreviousFile, .createIntermediateDirectories])
    }

      let request: DownloadRequest

        if let resumeData = resumeData {
      request = Alamofire.download(resumingWith: resumeData)
    } else {
      request = Alamofire.download("https://httpbin.org/image/png")
        }

        request.responseData { response in
          switch response.result {
          case .success(let data):
            self.image = UIImage(data: data)
          case .failure:
            self.resumeData = response.resumeData
          }
        }
    }
}
```

### 上传数据到服务器

上传少量的数据到服务器可以采用 JSON 或者 URL 编码参数的方式进行，这时 `Alamofire.request` 接口通常很高效。当需要上传的数据较大，比如文件或者 `InputStream`，这时需要使用 `Alamofire.upload` 接口。

> 当需要在后台上传数据时也应该使用 `Alamofire.upload`，更多信息请查看 [Session Manager Configurations](#session-manager) 章节。

#### 上传数据

```swift
let imageData = UIPNGRepresentation(image)!

Alamofire.upload(imageData, to: "https://httpbin.org/post").responseJSON { response in
    debugPrint(response)
}
```

#### 上传文件

```swift
let fileURL = Bundle.main.url(forResource: "video", withExtension: "mov")

Alamofire.upload(fileURL, to: "https://httpbin.org/post").responseJSON { response in
    debugPrint(response)
}
```

#### 上传多格式表单数据


```swift
Alamofire.upload(
    multipartFormData: { multipartFormData in
        multipartFormData.append(unicornImageURL, withName: "unicorn")
        multipartFormData.append(rainbowImageURL, withName: "rainbow")
    },
    to: "https://httpbin.org/post",
    encodingCompletion: { encodingResult in
      switch encodingResult {
      case .success(let upload, _, _):
            upload.responseJSON { response in
                debugPrint(response)
            }
      case .failure(let encodingError):
          print(encodingError)
      }
    }
)
```

#### 上传进度

当用户在上传时能够显示上传进度是非常友好的。任何 `UploadRequest` 请求都能通过 `uploadProgress` 和 `doanloadProgress` 接口报告上传进度和下载进度。


```swift
let fileURL = Bundle.main.url(forResource: "video", withExtension: "mov")

Alamofire.upload(fileURL, to: "https://httpbin.org/post")
    .uploadProgress { progress in // main queue by default
        print("Upload Progress: \(progress.fractionCompleted)")
    }
    .downloadProgress { progress in // main queue by default
        print("Download Progress: \(progress.fractionCompleted)")
    }
    .responseJSON { response in
        debugPrint(response)
    }
```

### Statistical Metrics

#### Timeline

Alamofire collects timings throughout the lifecycle of a `Request` and creates a `Timeline` object exposed as a property on all response types.

```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
    print(response.timeline)
}
```

The above reports the following `Timeline` info:

- `Latency`: 0.428 seconds
- `Request Duration`: 0.428 seconds
- `Serialization Duration`: 0.001 seconds
- `Total Duration`: 0.429 seconds

#### URL Session Task Metrics

In iOS and tvOS 10 and macOS 10.12, Apple introduced the new [URLSessionTaskMetrics](https://developer.apple.com/reference/foundation/urlsessiontaskmetrics) APIs. The task metrics encapsulate some fantastic statistical information about the request and response execution. The API is very similar to the `Timeline`, but provides many more statistics that Alamofire doesn't have access to compute. The metrics can be accessed through any response type.

```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
  print(response.metrics)
}
```

注意，这些接口仅在 iOS，tvOS 10 和 macOS 10.12 三个平台上可用。因此，取决于您的部署环境，您需要做以下检测：

```swift
Alamofire.request("https://httpbin.org/get").responseJSON { response in
    if #available(iOS 10.0. *) {
    print(response.metrics)
    }
}
```

### cURL 命令输出

不好的调试平台会让工作变得很麻烦. 幸好, Alamofire `Request` 对象实现了 `CustomStringConvertible` 和 `CustomDebugStringConvertible` 协议，这为我们提供了很好的调试工具。

#### CustomStringConvertible

```swift
let request = Alamofire.request("https://httpbin.org/ip")

print(request)
// GET https://httpbin.org/ip (200)
```

#### CustomDebugStringConvertible

```swift
let request = Alamofire.request("https://httpbin.org/get", parameters: ["foo": "bar"])
debugPrint(request)
```

输出:

```bash
$ curl -i \
  -H "User-Agent: Alamofire/4.0.0" \
  -H "Accept-Encoding: gzip;q=1.0, compress;q=0.5" \
  -H "Accept-Language: en;q=1.0,fr;q=0.9,de;q=0.8,zh-Hans;q=0.7,zh-Hant;q=0.6,ja;q=0.5" \
  "https://httpbin.org/get?foo=bar"
```

---

## 高级用法

Alamofire 建立在 `URLSession` 和 URL 加载系统上。为了更好的使用该框架，强烈建议要非常熟悉底层网络栈的相关概念

**推荐阅读**

- [URL 加载系统编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html)
- [URLSession 参考文档](https://developer.apple.com/reference/foundation/nsurlsession)
- [URLCache 参考文档](https://developer.apple.com/reference/foundation/urlcache)
- [URLAuthenticationChallenge 参考文档](https://developer.apple.com/reference/foundation/urlauthenticationchallenge)

### 会话管理

顶层的 Alamofire 接口例如 `Alamofire.request` 使用了默认的 `Alamofire.SessionManager` 会话管理对象发起网络请求。该会话管理对象默认使用了 `URLSessionConfiguration` 进行配置。

因此下面两段代码的是等效的：

```swift
Alamofire.request("https://httpbin.org/get")
```

```swift
let sessionManager = Alamofire.SessionManager.default
sessionManager.request("https://httpbin.org/get")
```

您可以为应用创建会后台任务会话管理对象，临时会话管理对象，同时也可以修改默认的会话配置，比如默认的请求头 (`httpAdditionalHeaders`) 或者请求超时时间 (`timeoutIntervalForRequest`)。

#### 创建默认配置会话管理对象

```swift
let configuration = URLSessionConfiguration.default
let sessionManager = Alamofire.SessionManager(configuration: configuration)
```

#### 创建后台任务会话管理对象

```swift
let configuration = URLSessionConfiguration.background(withIdentifier: "com.example.app.background")
let sessionManager = Alamofire.SessionManager(configuration: configuration)
```
#### 创建临时配置会话管理对象

```swift
let configuration = URLSessionConfiguration.ephemeral
let sessionManager = Alamofire.SessionManager(configuration: configuration)
```

#### 修改会话配置

```swift
var defaultHeaders = Alamofire.SessionManager.default.defaultHTTPHeaders
defaultHeaders["DNT"] = "1 (Do Not Track Enabled)"

let configuration = URLSessionConfiguration.default
configuration.httpAdditionalHeaders = defaultHeaders

let sessionManager = Alamofire.SessionManager(configuration: configuration)
```

> **不**推荐使用这种方式修改 `Authorization` 和 `Content-Type` 等请求头信息。推荐使用 `Alamofire.request` 接口中的 `headers` 参数, `URLRequestConvertible` 和 `ParameterEncoding` 等方式修改请求头信息。

### 会话代理

Alamofire 的`会话管理对象`默认创建了一个`会话代理对象`来处理 `URLSession` 产生的各种代理回调事件。这些代理方法实现的功能能够应付绝大部分的使用场景并且为隐藏了复杂的内部调用为用户提供了简单的上层接口。然而，您仍有可能会因为各种各样的需求而重载这些代理方法的实现。

#### 重载闭包

第一种自定义 `SessionDelegate` 行为的方式是重载闭包。通过闭包您可以重载对应的 `SessionDelegate` 接口，并且其他接口的实现将保持不变。这让实现一个自定义的代理方法集合变得很容易。下面是一些可用的可重载的闭包： 

```swift
/// Overrides default behavior for URLSessionDelegate method `urlSession(_:didReceive:completionHandler:)`.
open var sessionDidReceiveChallenge: ((URLSession, URLAuthenticationChallenge) -> (URLSession.AuthChallengeDisposition, URLCredential?))?

/// Overrides default behavior for URLSessionDelegate method `urlSessionDidFinishEvents(forBackgroundURLSession:)`.
open var sessionDidFinishEventsForBackgroundURLSession: ((URLSession) -> Void)?

/// Overrides default behavior for URLSessionTaskDelegate method `urlSession(_:task:willPerformHTTPRedirection:newRequest:completionHandler:)`.
open var taskWillPerformHTTPRedirection: ((URLSession, URLSessionTask, HTTPURLResponse, URLRequest) -> URLRequest?)?

/// Overrides default behavior for URLSessionDataDelegate method `urlSession(_:dataTask:willCacheResponse:completionHandler:)`.
open var dataTaskWillCacheResponse: ((URLSession, URLSessionDataTask, CachedURLResponse) -> CachedURLResponse?)?
```

下面的例子通过重载 `taskWillPerformHTTPRedirection` 闭包来避免请求重定向到`apple.com`的域名。

```swift
let sessionManager = Alamofire.SessionManager(configuration: URLSessionConfiguration.default)
let delegate: Alamofire.SessionDelegate = sessionManager.delegate

delegate.taskWillPerformHTTPRedirection = { session, task, response, request in
    var finalRequest = request

    if
        let originalRequest = task.originalRequest,
        let urlString = originalRequest.url?.urlString,
        urlString.contains("apple.com")
    {
        finalRequest = originalRequest
    }

    return finalRequest
}
```

#### 继承

另一种重载 `SessionDelegate` 默认实现的方式是继承。通过继承您可以实现完全的自定义或者仍然使用默认实现仅为接口创建一个代理。通过为接口创建代理，您可以在调用接口默认实现的前后增加日志消息，派发通知等功能。下面的例子继承了 `SessionDelegate`，并且当发生重定向时打印消息日志。

```swift
class LoggingSessionDelegate: SessionDelegate {
    override func urlSession(
        _ session: URLSession,
        task: URLSessionTask,
        willPerformHTTPRedirection response: HTTPURLResponse,
        newRequest request: URLRequest,
        completionHandler: @escaping (URLRequest?) -> Void)
    {
        print("URLSession will perform HTTP redirection to request: \(request)")

        super.urlSession(
            session,
            task: task,
            willPerformHTTPRedirection: response,
            newRequest: request,
            completionHandler: completionHandler
        )
    }
}
```

### 请求

`request`,`download`,`upload`,`stream` 等方法的返回值  `DataRequest`, `DownloadRequest`, `UploadRequest` 和 `StreamRequest` 均是继承于 `Request`。所有的 `Request` 实例都是由自己的会话管理对象创建，并且不会直接初始化。

每个子类都有一些特殊的方法比如 `authenticate`, `validate`, `responseJSON` 和 `uploadProgress`，这些方法均返回调用者以便可以进行链式调用。

请求可以被挂起，恢复，取消：

- `suspend()`: 挂起底层任务和派发队列。
- `resume()`: 恢复任务和派发队列。如果会话管理对象没有设置 `startRequestsImmediately` 为 `true`，那么请求需要调用 `resume()` 才能开始。
- `cancel()`: 取消任务，产生错误信息并将错误信息传递到响应回调。

### 请求路由

随着 App 变得复杂，使用通用模式创建你自己的网络栈就变得非常重要了。其中一个重要的设计就是如何路由你的请求。遵循 `URLConvertible` 和 `URLRequestConvertible` 协议的 `Router` 就变得非常有用。

#### URLConvertible

遵循 `URLConvertible` 协议的类可以用来构造 URLs，然后将 URLs 用来构造 URL 请求。`String`, `URL`, 和 `URLComponents` 都遵循了 `URLConvertible` 协议，这三个类的对象均可以作为 `url` 参数传递给 `request`, `upload`, 和 `download` 方法:

```swift
let urlString = "https://httpbin.org/post"
Alamofire.request(urlString, method: .post)

let url = URL(string: urlString)!
Alamofire.request(url, method: .post)

let urlComponents = URLComponents(url: url, resolvingAgainstBaseURL: true)
Alamofire.request(.post, URLComponents)
```
与 web 服务器交互时推荐通过实现 `URLConvertible` 协议来做域名型模型与服务器资源的映射。

##### 类型安全路由

```swift
extension User: URLConvertible {
    static let baseURLString = "https://example.com"

    func asURL() throws -> URL {
      let urlString = User.baseURLString + "/users/\(username)/"
        return try urlString.asURL()
    }
}
```

```swift
let user = User(username: "mattt")
Alamofire.request(user) // https://example.com/users/mattt
```

#### URLRequestConvertible

实现了 `URLRequestConvertible` 协议的类型可以用来构造 URL 请求。`URLRequest` 默认实现了 `URLRequestConvertible` 协议，这使得 `URLRequest` 可直接传递给 `request`,`upload`,`download`等方法（推荐使用这种方式实现自定义 HTTP body）

```swift
let url = URL(string: "https://httpbin.org/post")!
var urlRequest = URLRequest(url: url)
urlRequest.httpMethod = "POST"

let parameters = ["foo": "bar"]

do {
    urlRequest.httpBody = try JSONSerialization.data(withJSONObject: parameters, options: [])
} catch {
    // No-op
}

urlRequest.setValue("application/json", forHTTPHeaderField: "Content-Type")

Alamofire.request(urlRequest)
```

与 web 服务器交互时推荐通过实现 `URLRequestConvertible` 协议以确保请求端点的一致性。这种方法可以用于抽象出服务器端不一致并提供类型安全路由，以及管理认证凭证和其他状态

##### API 抽象参数

```swift
enum Router: URLRequestConvertible {
    case search(query: String, page: Int)

    static let baseURLString = "https://example.com"
    static let perPage = 50

    // MARK: URLRequestConvertible

    func asURLRequest() throws -> URLRequest {
        let result: (path: String, parameters: Parameters) = {
            switch self {
            case let .search(query, page) where page > 0:
                return ("/search", ["q": query, "offset": Router.perPage * page])
            case let .search(query, _):
                return ("/search", ["q": query])
            }
        }()

        let url = try Router.baseURLString.asURL()
        let urlRequest = URLRequest(url: url.appendingPathComponent(result.path))

        return try URLEncoding.default.encode(urlRequest, with: result.parameters)
    }
}
```

```swift
Alamofire.request(Router.search(query: "foo bar", page: 1)) // https://example.com/search?q=foo%20bar&offset=50
```

##### CRUD & Authorization

```swift
import Alamofire

enum Router: URLRequestConvertible {
    case createUser(parameters: Parameters)
    case readUser(username: String)
    case updateUser(username: String, parameters: Parameters)
    case destroyUser(username: String)

    static let baseURLString = "https://example.com"

    var method: HTTPMethod {
        switch self {
        case .createUser:
            return .post
        case .readUser:
            return .get
        case .updateUser:
            return .put
        case .destroyUser:
            return .delete
        }
    }

    var path: String {
        switch self {
        case .createUser:
            return "/users"
        case .readUser(let username):
            return "/users/\(username)"
        case .updateUser(let username, _):
            return "/users/\(username)"
        case .destroyUser(let username):
            return "/users/\(username)"
        }
    }

    // MARK: URLRequestConvertible

    func asURLRequest() throws -> URLRequest {
      let url = try Router.baseURLString.asURL()

        var urlRequest = URLRequest(url: url.appendingPathComponent(path))
        urlRequest.httpMethod = method.rawValue

        switch self {
        case .createUser(let parameters):
            urlRequest = try URLEncoding.default.encode(urlRequest, with: parameters)
        case .updateUser(_, let parameters):
            urlRequest = try URLEncoding.default.encode(urlRequest, with: parameters)
        default:
            break
        }

        return urlRequest
    }
}
```

```swift
Alamofire.request(Router.readUser("mattt")) // GET https://example.com/users/mattt
```

### Adapting and Retrying Requests

当今的很多 web 服务都可以通过授权系统进行访问。其中最常用的是 OAuth。OAuth 会生成一个访问令牌来授权你的应用访问权限内的 web 服务。创建令牌可能会很麻烦，令牌过期需要考虑很多线程安全的问题，这会让情况变得更复杂。

`RequestAdapter` 和 `RequestRetrier` 协议让创建线程安全的授权系统变得容易。

#### RequestAdapter

`RequestAdapter` 协议允许 `SessionManager` 在创建 `Request` 前为 `Request` 做额外的检查和适配工作。比较常用的应用场景是为请求拼接`授权`参数。

```swift
class AccessTokenAdapter: RequestAdapter {
  private let accessToken: String

  init(accessToken: String) {
    self.accessToken = accessToken
  }

  func adapt(_ urlRequest: URLRequest) throws -> URLRequest {
      var urlRequest = urlRequest

      if urlRequest.urlString.hasPrefix("https://httpbin.org") {
        urlRequest.setValue("Bearer " + accessToken, forHTTPHeaderField: "Authorization")
      }

      return urlRequest
  }
}
```

```swift
let sessionManager = SessionManager()
sessionManager.adapter = AccessTokenAdapter(accessToken: "1234")

sessionManager.request("https://httpbin.org/get")
```

#### RequestRetrier

`RequestRetrier` 协议允许网络请求发生错误时重新发起请求。通过同时实现 `RequestAdapter` 和 `RequestRetrier` 协议，您可以为 OAuth1，OAuth2，基本授权，重试策略创建一个证书刷新系统。您能实现的功能不局限于此。下面的例子展示了 OAuth2 令牌的刷新流程。

> **免责声明：** 这**不是**一个全局的 `OAuth2` 解决方案。下面的代码仅作为简单示例展示了如何通过 `RequestAdapter` 和 `RequestRetrier` 协议来实现线程安全的刷新系统。

> 重申，**不要拷贝**下面的示例代码到您的产品中。该代码片段仅能作为示例。每一个授权系统应该基于平台和授权类型做相应的修改。

```swift
class OAuth2Handler: RequestAdapter, RequestRetrier {
    private typealias RefreshCompletion = (_ succeeded: Bool, _ accessToken: String?, _ refreshToken: String?) -> Void

    private let sessionManager: SessionManager = {
        let configuration = URLSessionConfiguration.default
        configuration.httpAdditionalHeaders = SessionManager.defaultHTTPHeaders

        return SessionManager(configuration: configuration)
    }()

    private let lock = NSLock()

    private var clientID: String
    private var baseURLString: String
    private var accessToken: String
    private var refreshToken: String

    private var isRefreshing = false
    private var requestsToRetry: [RequestRetryCompletion] = []

    // MARK: - Initialization

    public init(clientID: String, baseURLString: String, accessToken: String, refreshToken: String) {
        self.clientID = clientID
        self.baseURLString = baseURLString
        self.accessToken = accessToken
        self.refreshToken = refreshToken
    }

    // MARK: - RequestAdapter

    func adapt(_ urlRequest: URLRequest) throws -> URLRequest {
        if let url = urlRequest.url, url.urlString.hasPrefix(baseURLString) {
            var urlRequest = urlRequest
            urlRequest.setValue("Bearer " + accessToken, forHTTPHeaderField: "Authorization")
            return urlRequest
        }

        return urlRequest
    }

    // MARK: - RequestRetrier

    func should(_ manager: SessionManager, retry request: Request, with error: Error, completion: @escaping RequestRetryCompletion) {
        lock.lock() ; defer { lock.unlock() }

        if let response = request.task.response as? HTTPURLResponse, response.statusCode == 401 {
            requestsToRetry.append(completion)

            if !isRefreshing {
                refreshTokens { [weak self] succeeded, accessToken, refreshToken in
                    guard let strongSelf = self else { return }

                    strongSelf.lock.lock() ; defer { strongSelf.lock.unlock() }

                    if let accessToken = accessToken, let refreshToken = refreshToken {
                        strongSelf.accessToken = accessToken
                        strongSelf.refreshToken = refreshToken
                    }

                    strongSelf.requestsToRetry.forEach { $0(succeeded, 0.0) }
                    strongSelf.requestsToRetry.removeAll()
                }
            }
        } else {
            completion(false, 0.0)
        }
    }

    // MARK: - Private - Refresh Tokens

    private func refreshTokens(completion: @escaping RefreshCompletion) {
        guard !isRefreshing else { return }

        isRefreshing = true

        let urlString = "\(baseURLString)/oauth2/token"

        let parameters: [String: Any] = [
            "access_token": accessToken,
            "refresh_token": refreshToken,
            "client_id": clientID,
            "grant_type": "refresh_token"
        ]

        sessionManager.request(urlString, method: .post, parameters: parameters, encoding: JSONEncoding.default)
            .responseJSON { [weak self] response in
                guard let strongSelf = self else { return }

                if 
                    let json = response.result.value as? [String: Any], 
                    let accessToken = json["access_token"] as? String, 
                    let refreshToken = json["refresh_token"] as? String 
                {
                    completion(true, accessToken, refreshToken)
                } else {
                    completion(false, nil, nil)
                }

                strongSelf.isRefreshing = false
            }
    }
}
```

```swift
let baseURLString = "https://some.domain-behind-oauth2.com"

let oauthHandler = OAuth2Handler(
    clientID: "12345678",
    baseURLString: baseURLString,
    accessToken: "abcd1234",
    refreshToken: "ef56789a"
)

let sessionManager = SessionManager()
sessionManager.adapter = oauthHandler
sessionManager.retrier = oauthHandler

let urlString = "\(baseURLString)/some/endpoint"

sessionManager.request(urlString).validate().responseJSON { response in
    debugPrint(response)
}
```

`SessionManager` 的 `adapter` 和 `retrier` 被设置为 `OAuth2Handler`后，当令牌失效时，便会自动刷新令牌并尝试按失败的顺序重新发起请求。

> 如果您想按创建网络请求的顺序重新发起请求，您可以通过网络请求任务的 id 进行排序。

该示例仅检查了响应的 `401` 状态码，作为检测失效令牌的例子这已经足够。O在实际产品中，您应该还要检测响应头中的 `reaml` 和 `www-authenticate` 等字段。

还需要注意的是该授权系统可以在多个会话管理对象间共享。比如，您可以为同一个 web 服务集同时使用 `default` 和 `ephemeral` 会话配置。上面的例子允许 `oauthHandler` 实例对象在多个会话管理对象间共享并管理各自的刷新流程。

### 自定义响应序列化器

#### 错误处理

过去在实现自定义响应序列化器或对象序列化方法时着重考虑的是错误信息的处理。这里有两个可选项：对错误信息不做任何处理直接向下传递，由用户在响应回调处处理；或者为您的应用定义一个包含所有错误类型的 `Error` 枚举类。

下面的 `BackendError` 枚举类在后面的例子中也会出现：


```swift
enum BackendError: Error {
    case network(error: Error) // Capture any underlying Error from the URLSession API
    case dataSerialization(error: Error)
    case jsonSerialization(error: Error)
    case xmlSerialization(error: Error)
    case objectSerialization(reason: String)
}
```

#### 自定义响应序列化器

Alamofire 为 strings，JSON，property lsits 提供了内置的响应序列化器，您也可以为 `Alamofire.DataRequest` 和 `Alamofire.DownloadRequest` 进行扩展。

下面的例子展示了响应序列化器使用 [Ono](https://github.com/mattt/Ono) 的实现方式：

```swift
extension DataRequest {
    static func xmlResponseSerializer() -> DataResponseSerializer<ONOXMLDocument> {
        return DataResponseSerializer { request, response, data, error in
            // Pass through any underlying URLSession error to the .network case.
            guard error == nil else { return .failure(BackendError.network(error: error!)) }

            // Use Alamofire's existing data serializer to extract the data, passing the error as nil, as it has
            // already been handled.
            let result = Request.serializeResponseData(response: response, data: data, error: nil)

            guard case let .success(validData) = result else {
                return .failure(BackendError.dataSerialization(error: result.error! as! AFError))
            }

            do {
                let xml = try ONOXMLDocument(data: validData)
                return .success(xml)
            } catch {
                return .failure(BackendError.xmlSerialization(error: error))
            }
        }
    }

    @discardableResult
    func responseXMLDocument(
        queue: DispatchQueue? = nil,
        completionHandler: @escaping (DataResponse<ONOXMLDocument>) -> Void)
        -> Self
    {
        return response(
            queue: queue,
            responseSerializer: DataRequest.xmlResponseSerializer(),
            completionHandler: completionHandler
        )
    }
}
```

#### 通用响应对象序列化

通用序列化可以进行自动，类型安全的对象序列化。

```swift
protocol ResponseObjectSerializable {
    init?(response: HTTPURLResponse, representation: Any)
}

extension DataRequest {
    func responseObject<T: ResponseObjectSerializable>(
        queue: DispatchQueue? = nil,
        completionHandler: @escaping (DataResponse<T>) -> Void)
        -> Self
    {
        let responseSerializer = DataResponseSerializer<T> { request, response, data, error in
            guard error == nil else { return .failure(BackendError.network(error: error!)) }

            let jsonResponseSerializer = DataRequest.jsonResponseSerializer(options: .allowFragments)
            let result = jsonResponseSerializer.serializeResponse(request, response, data, nil)

            guard case let .success(jsonObject) = result else {
                return .failure(BackendError.jsonSerialization(error: result.error!))
            }

            guard let response = response, let responseObject = T(response: response, representation: jsonObject) else {
                return .failure(BackendError.objectSerialization(reason: "JSON could not be serialized: \(jsonObject)"))
            }

            return .success(responseObject)
        }

        return response(queue: queue, responseSerializer: responseSerializer, completionHandler: completionHandler)
    }
}
```

```swift
struct User: ResponseObjectSerializable, CustomStringConvertible {
    let username: String
    let name: String

    var description: String {
        return "User: { username: \(username), name: \(name) }"
    }

    init?(response: HTTPURLResponse, representation: Any) {
        guard
            let username = response.url?.lastPathComponent,
            let representation = representation as? [String: Any],
            let name = representation["name"] as? String
        else { return nil }

        self.username = username
        self.name = name
    }
}
```

```swift
Alamofire.request("https://example.com/users/mattt").responseObject { (response: DataResponse<User>) in
    debugPrint(response)

    if let user = response.result.value {
        print("User: { username: \(user.username), name: \(user.name) }")
    }
}
```

相同的方法也可以用于处理终端返回的对象集合：

```swift
protocol ResponseCollectionSerializable {
    static func collection(from response: HTTPURLResponse, withRepresentation representation: Any) -> [Self]
}

extension ResponseCollectionSerializable where Self: ResponseObjectSerializable {
    static func collection(from response: HTTPURLResponse, withRepresentation representation: Any) -> [Self] {
        var collection: [Self] = []

        if let representation = representation as? [[String: Any]] {
            for itemRepresentation in representation {
                if let item = Self(response: response, representation: itemRepresentation) {
                    collection.append(item)
                }
            }
        }

        return collection
    }
}
```

```swift
extension DataRequest {
    @discardableResult
    func responseCollection<T: ResponseCollectionSerializable>(
        queue: DispatchQueue? = nil,
        completionHandler: @escaping (DataResponse<[T]>) -> Void) -> Self
    {
        let responseSerializer = DataResponseSerializer<[T]> { request, response, data, error in
            guard error == nil else { return .failure(BackendError.network(error: error!)) }

            let jsonSerializer = DataRequest.jsonResponseSerializer(options: .allowFragments)
            let result = jsonSerializer.serializeResponse(request, response, data, nil)

            guard case let .success(jsonObject) = result else {
                return .failure(BackendError.jsonSerialization(error: result.error!))
            }

            guard let response = response else {
                let reason = "Response collection could not be serialized due to nil response."
                return .failure(BackendError.objectSerialization(reason: reason))
            }

            return .success(T.collection(from: response, withRepresentation: jsonObject))
        }

        return response(responseSerializer: responseSerializer, completionHandler: completionHandler)
    }
}
```

```swift
struct User: ResponseObjectSerializable, ResponseCollectionSerializable, CustomStringConvertible {
    let username: String
    let name: String

    var description: String {
        return "User: { username: \(username), name: \(name) }"
    }

    init?(response: HTTPURLResponse, representation: Any) {
        guard
            let username = response.url?.lastPathComponent,
            let representation = representation as? [String: Any],
            let name = representation["name"] as? String
        else { return nil }

        self.username = username
        self.name = name
    }
}
```

```swift
Alamofire.request("https://example.com/users").responseCollection { (response: DataResponse<[User]>) in
    debugPrint(response)

    if let users = response.result.value {
        users.forEach { print("- \($0)") }
    }
}
```

#### 请求适配器

### 安全性

在与 web 服务器交互传输敏感数据时应该使用安全的 HTTPS 连接。默认情况下，Alamofire 会使用苹果提供的 Security 框架对服务器提供的证书串进行验证。这样仅仅能确保服务器端证书是否有效，并不能防止中间人攻击 man-in-the-middle(MITM) 或其他潜在的漏洞。为了降低遭受中间人攻击的可能性，应用在处理敏感用户的数据或金融信息时应该配合使用证书或 `ServerTrustPolicy` 提供的公钥锁定

#### ServerTrustPolicy

#### Server Trust Policy Manager

##### 继承 Server Trust Policy Manager

#### 验证主机

##### 验证证书串

#### App Transport Security

```xml
<dict>
  <key>NSAppTransportSecurity</key>
  <dict>
    <key>NSExceptionDomains</key>
    <dict>
      <key>example.com</key>
      <dict>
        <key>NSExceptionAllowsInsecureHTTPLoads</key>
        <true/>
        <key>NSExceptionRequiresForwardSecrecy</key>
        <false/>
        <key>NSIncludesSubdomains</key>
        <true/>
        <!-- Optional: Specify minimum TLS version -->
        <key>NSTemporaryExceptionMinimumTLSVersion</key>
        <string>TLSv1.2</string>
      </dict>
    </dict>
  </dict>
</dict>
```

### 网络可用性

`NetworkReachabilityManager` 可用于监听 WWAN 和 WiFi 网络到指定主机或 IP 地址的连接状态。


```swift
let manager = NetworkReachabilityManager(host: "www.apple.com")

manager?.listener = { status in
    print("Network Status Changed: \(status)")
}

manager?.startListening()
```

> 请确保对 `网络状态监听对象` 有强引用，否则不会监听到任何网络状态。

在监听网络状态时需要注意以下几点：

- **不要**根据网络状态来决定是否发送网络请求。
    - **只管**发送就行
- 当网络恢复连接，对失败的网络请求重新发起请求。
    - 尽管重新发起请求仍有可能失败，但您仍应该尝试。
- 网络状态有助于分析出请求失败原因。
    - 如果网络请求失败，提示用户网络处于离线状态要比更具体的错误信息比如"请求超时"等更友好。

> 更多信息请参考 [WWDC 2012 Session 706, "Networking Best Practices"](https://developer.apple.com/videos/play/wwdc2012-706/) for more info.

---

## Open Radars

The following radars have some effect on the current implementation of Alamofire.

- [`rdar://21349340`](http://www.openradar.me/radar?id=5517037090635776) - Compiler throwing warning due to toll-free bridging issue in test case
- [`rdar://26761490`](http://www.openradar.me/radar?id=5010235949318144) - Swift string interpolation causing memory leak with common usage
- `rdar://26870455` - Background URL Session Configurations do not work in the simulator
- `rdar://26849668` - Some URLProtocol APIs do not properly handle `URLRequest`

## FAQ

### Alamofire 名字由来

[Alamofire 花](https://aggie-horticulture.tamu.edu/wildseed/alamofire.html)，矢车菊的一种，是德克萨斯州的官方州花。

### 请求路由和请求适配器的区别

资源路径，请求参数，公共请求头这些静态数据属于 `路由` 范畴。`认证` 头这类会随着认证系统发生变化的动态数据属于 `请求适配器` 范畴。



## 致谢

Alamofire 由 [Alamofire 软件基金会](http://alamofire.org) 所有并维护。您可以通过关注我们的 Twitter 官方账号 [@AlamofireSF](https://twitter.com/AlamofireSF) 来获取最新的更新发布消息。

## 捐款



## 开源协议

Alamofire 在 MIT 开源协议下发布。更多信息请查看 LICENSE 文件。
