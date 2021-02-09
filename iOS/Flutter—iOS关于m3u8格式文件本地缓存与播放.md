## Flutter—iOS关于m3u8格式文件本地缓存与播放

flutter处理m3u8格式流文件的本地存储与本地播放，flutter插件管理中暂无此块处理的插件，Android的插件我在GitHub上找到一个，亲测可用！地址如下：
https://github.com/lytian/m3u8_downloader，安装使用可查看其github

```dart

m3u8_downloader:
  git:
    url: https://github.com/lytian/m3u8_downloader.git
```

iOS处理使用交互处理，利用原生代码实现下载缓存到本地，在通过第三方库-'CocoaHTTPServer' 开启本地服务进行访问本地文件实现播放。

flutter中iOS部分代码为Swift 5.0

1、使用到的第三方库，以下是第三方podfile库配置：

```swift
platform :ios, '9.0' #手机的系统
use_frameworks!

# TestM3u8项目名称
target 'TestM3u8' do

pod 'WLM3U' # m3u8格式的下载与组装库
pod 'Alamofire' # 网络库
pod 'CocoaHTTPServer' #配置本地服务库，注意最低版本9.0不然可能会编译报错

end

```

m3u8格式的下载与组装库地址：https://github.com/WillieWangWei/WLM3U
配置本地服务：CocoaHTTPServer随便一搜就会有很多, 下面也会有我的配置

2、由于我将WLM3U库文件下载到本地了，直接将其文件导入到项目中，所以调用起方法不需要导入头文件（import WLM3U），所以下载方法调用的时候将会是直接调用，如果是pod的可以参考这个库提供的demo，使用他那样的调用方式。

**注意：** iOS开发账号要使用公司的或者自己能打包发布的账号，如果使用自己的开发者账号(未充钱的)，会出现无法下载成功！！！

由于CocoaHTTPServer库是Objective-C语言实现的，需要在桥接文件中导入其头文件（Runner-Bridging-Header.h）

```Swift
#import "HTTPServer.h"
```

以下是iOS项目中AppDelegate文件中的配置（包括下载、播放路径返回、本地服务配置）

```swift
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
    
    var httpServer : HTTPServer! = nil  // 本地服务
    var isOpenServer : Bool! = false  // 是否开启服务
    
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    GeneratedPluginRegistrant.register(with: self)
    
    // 开启本地服务
    if (!isOpenServer) {
        openServer()
    }
    
    //接收flutter发送来调用下载的消息(ios.download.file.m3u8)为消息名称两端必须一致
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
    let batteryChannel = FlutterMethodChannel.init(name: "ios.download.file.m3u8", binaryMessenger: controller.binaryMessenger)
    
    // downLoadFlie为监听调用的方法，flutter端定义的，这边判断
    batteryChannel.setMethodCallHandler({
      [weak self] (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
        guard call.method == "downLoadFlie" else {
          result(FlutterMethodNotImplemented)
          return
        }
        // 调用下载方法，call中包含传递过来的参数，result回调
        self?.downLoadFiles(call: call, result: result)
    })
    
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }

  override func application(_ application: UIApplication, handleOpen url: URL) -> Bool {
          return FlutterAlipayPlugin.handleOpen(url);
  }
    
  // 应用程序被杀死进程时，关闭本地服务
 override func applicationWillTerminate(_ application: UIApplication) {
    if isOpenServer {
        httpServer.stop()
    }
 }
    
    // 开启本地服务，不设置端口，由系统自动分配
    func openServer() -> Void {
        
        httpServer = HTTPServer()
        httpServer.setType("_http._tcp.")
        // 播放沙盒文件
        print("\(NSHomeDirectory())/Documents")
        // 设置http服务器根目录
        httpServer.setDocumentRoot("\(NSHomeDirectory())/Documents")
        
        do{
          // 服务开启成功
            try httpServer.start()
            isOpenServer = true
        } catch {
            isOpenServer = false
        }
    }
    
    // 下载文件，文件存在返回本地播放地址
    func downLoadFiles(call: FlutterMethodCall, result: FlutterResult) {
        // 将call转为字典进行取出参数值
        let tResult = call.arguments as![String:Any]
        
        let url : String = tResult["playerURL"] as! String
        let fileName : String = tResult["fileName"] as! String
        
      // 校验文件名是否存在，如果存在就直接返回本地文件访问地址
        let b = filesIsExist(fileName)
        if b {
            // 本地服务启动成功返回本地服务地址，启动失败返回空字符串
          // httpServer.listeningPort() 获取参数启动的端口号
            if isOpenServer {
                result("http://127.0.0.1:\(httpServer.listeningPort())/WLM3U/\(fileName)/file.m3u8")
            } else {
                result("")
            }
        } else {
            // 开线程执行 进行文件下载
            DispatchQueue.global().async {
                let url = URL(string: url)!
                do {
                    let workflow = try attach(url: url,
                                                    calculateSize: true,
                                                    completion: { (result) in
                                                        switch result {
                                                        case .success(let model):
                                                            print("attach success " + model.name!)
                                                        case .failure(let error):
                                                            print("attach failure " + error.localizedDescription)
                                                        }
                    })
                    
                   self.run(workflow: workflow)
                } catch  {
                    print(error.localizedDescription)
                }
            }
            result("")
        }
    }

    // 开始下载
  func run(workflow: Workflow) {
      workflow
      .download(progress: { (progress, completedCount) in
          print(Float(progress.fractionCompleted))
          let mb = Double(completedCount) / 1024 / 1024
          var text = ""
          if mb >= 0.1 {
              text = String(format: "%.1f", mb) + " M/s"
          } else {
              text = String(completedCount / 1024) + " K/s"
          }
        print(text)
      }, completion: { (result) in
          switch result {
          case .success(let url):
              print("download success " + url.path)
          case .failure(let error):
              print("download failure " + error.localizedDescription)
          }
      })
  }
    
    // 检查文件地址是否存在
    func filesIsExist(_ identifer: String) -> Bool {
        let filePath = getDocumentsDirectory().appendingPathComponent("WLM3U").appendingPathComponent(identifer)
        if FileManager.default.fileExists(atPath: filePath.path) {
            let files = findFiles(path: filePath.path, filterTypes: ["m3u8"])
            if files.count > 0 { // 本地m3u8文件已经存在
                print("文件已存在 -- filePath = \(filePath.path)")
                return true
            }
        }
        return false
    }
    
    func getDocumentsDirectory() -> URL {
        let paths = FileManager.default.urls(for: .documentDirectory, in:.userDomainMask)
        let documentsDirectory = paths[0]
        return documentsDirectory
    }
    
    func findFiles(path: String, filterTypes: [String]) -> [String] {
        do {
            let files = try FileManager.default.contentsOfDirectory(atPath: path)
            if filterTypes.count == 0 {
                return files
            } else {
                let filteredfiles = NSArray(array: files).pathsMatchingExtensions(filterTypes)
                return filteredfiles
            }
        } catch {
            return []
        }
    }
}

```

3、flutter调用下载方法进行文件本地缓存

在调用页面创建通信属性

```dart
// 用于与iOS通信实现下载功能,name(ios.download.file.m3u8)唯一性，并且双端一致
static const MethodChannel _methodChannel = const MethodChannel('ios.download.file.m3u8');

// 点击事件调用下载功能
String fileName = '文件名称';
String _playUrl = 'http://xxxxx.m3u8';
// downLoadFlie 为标注要调用的方法， 使用map进行传递参数
try {
     dynamic respose = await _methodChannel.invokeMethod('downLoadFlie', {'playerURL': _playUrl, 'fileName': fileName});
   // 文件存在就返回本地播放地址
   if (respose.toString().isNotEmpty) {
     _playUrl = respose.toString();
   }
} on PlatformException catch(e) {
   print(e);
}
```