基础模块整理:
1. MGMainModule : 负责主界面初始化
2. MGAdvertisement: 开机广告模块
3. MGShare : 分享
4. MGRemoteNotity: 通知模块配置及事件处理
5. MGLocationManager : 定位管理器
6. MGLuaCache : 应该是提供lua文件缓存功能
7. MGCashierDesk : 提供内购付款相关的功能
8. MGPlayer : 播放器相关功能
9. MGDownload : 视频下载相关功能
10. MGMirrorKit : 应该是投屏
> 1 - 10基本功能项目比较通用 - 可以迁入
11. MGAnaliticsPointModule : 科升埋点设置
12. MGASRKit: 未知
13. MGDeeplink: 未知
14. MGFreeFlow: 控制免费流量
15. MGSDKAnalytics : 科升相关

### 核心中间件:MGRouter
管理所有基础模块组件管理和组件间通信

#### 模块(组件)生命周期管理器: MGAppDelegateModule
1. 设置各模块需要执行的生命周期的方法
2. 交换 `AppDelegate` 和 `AppDelegateRealize` 方法
3. `AppDelegateRealize` 中生命周期方法会调用各模块设置的生命周期方法

好处: 1. 将生命周期分散到各模块自己处理,不会全部累计在appDelegate中,实现解耦

#### 模块(组件)间通信

```
// 组件对外公开接口, m组件名, i接口名, p(arg)接收参数, c(callback)回调block
#define MGROUTER_EXTERN_METHOD(m,i,p,c) + (id) routerHandle_##m##_##i:(NSDictionary*)arg callback:(MGRouterCompletion)callback
```

```swift
/**
 *  @author steven, 16-09-12 18:10:30
 *
 *  组件通信（输入方）
 *
 *  参数规则:  URL中query与arg拼装出完整参数, query可不填，遵循URI get 参数规则, arg可为nil
 *
 *  @return 任意oc对象
 *
 *  @param urlString        - Scheme的URL,如 router://MyMG/userInfo?uid=123, 通过url query传入的参数获取为字典类型
 *
 *  @param arg              - arg 为任意oc对象, nil. 注意:如果arg为字典类型，
 拼装结果要优先于query中相同字段(query中相同字段会被替换), 不为字典类型query有参数时以key为MGRouterParamsOption获取
 *
 *  @param error            - error 通信过程异常
 *
 *  @param completion       - completion 通信完后相应的callback, 业务输出方自行维护该block
 *
 */
+ (nullable id)openURL:(nonnull NSString *)urlString arg:(nullable id)arg error:( NSError*__nullable *__nullable)error completion:(nullable MGRouterCompletion)completion;
```

好处:  组件之间不需要互相依赖,依赖Router即可,实现解耦
改进:  添加了MGRouterDocument : 作为 组件和组件方法 的说明文档
![GitHub Logo](/router_summary@2x.png)


#### 编程思想
面向协议编程: 调用者不需要知道具体协议(业务)方法实现的示例对象是什么, 只需要调用协议(业务)方法.
好处: 1.通过查看协议(接口)即可知道业务需求
      2.协议(业务)方便需要替换或者修改具体实现,客户端不需要修改代码,实现端需替换或者修改示例对象,遵循协议(业务)即可
使用方法: 
Class: MGProtocolManager: 协议(业务管理器) 
// 给 协议注册 具体的实现对象
```
OC:    [MGProtocolManager registerModuleProvider:[示例对象Class new] forProtocol:协议];
JAVA:  MGProtocolManage.registerModuleProvider(接口)
```
// 通过协议 找具体的 实现对象
```
+ (id)moduleProviderForProtocol:(Protocol *)protocol;
OC:   id<协议> someObject =  [MGProtocolManage moduleProviderForProtocol:协议];
java: Object<协议> someObject =  MGProtocolManage.moduleProviderForProtocol: 接口;
someObject.func();
```


综合: Router 



#### 主界面流程
1. `initLaunchGuideWindow` 加载启动引导图图 
> 本地版本号和最新版本号进行判断是否加载启动引导启动图
2. `initAdWindow` 加载广告图
> 不启动引导图则启动广告图
3. `initRootVC` 设置root视图

##### 设置root视图流程
1. 加载本地缓存Frame: Frame包含topBars 和bottomBars,设置RootViewContrlloer 为 MGTabBarController
2. 网络获取Frame,重新加载Frame,设置RootViewContrlloer 为 MGTabBarController
3. 
//字段确定









