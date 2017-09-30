#### 原生界面跳转到RN界面

1. 在原生应用如TianJinDL根目录下创建package.json文件，并cd到TianJinDL根目录下执行npm install 安装依赖配置。package.json文件中内容类似：

```
{
  "name": "TianJinDL",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "15.0.2",
    "react-native": "0.26.1",
    "react-navigation": "^1.0.0-beta.11"
  }
}
```

2.在原生应用TianJinDL的Podfile文件中添加所集成的RN需要的ReactNative依赖库，并cd到TianJinDL根目录下，执行pod install安装。Podfile文件内容如下（其中path为步骤1中安装的node\_modules中的react-native文件的路径）：

```
pod 'React',:path =>'/Users/wangna/Desktop/TianJinDL/node_modules/react-native',:subspecs =>
[
'Core',
'RCTText',
'RCTImage',
'RCTNetwork',
'RCTWebSocket',
]
```

3.在TianJinDL根目录下，添加要集成的RN界面的相关js文件

4.在原生应用TJDMeViewController控制器中，通过RCTRootView实现跳转到RN界面

```
 NSURL *jsCodeLocation;
 jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];
 RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                        moduleName:@"TianJinDL"
                                                 initialProperties:props
                                                     launchOptions:nil];
    rootView.frame = CGRectMake(0, 64, SCREEN_WIDTH, SCREEN_HEIGHT-50-64);
    [self.view addSubview:rootView];
```

#### RN界面跳转到原生界面

1.在原生应用中创建RCTModules类，继承自NSObject，使用通知的方法进行消息传送实现界面的跳转，RCTModules.m中

```
//定义导出的类名
RCT_EXPORT_MODULE(RTModule)
//定义导出的方法
RCT_EXPORT_METHOD(RNOpenOneVC:(NSString *)msg){
    NSLog(@"RN传入原生界面的数据为：%@",msg);
    //这里必须使用主线程发送，否则可能失效
    dispatch_async(dispatch_get_main_queue(), ^{
        [[NSNotificationCenter defaultCenter] postNotificationName:@"RNOpenOneVC" object:nil];       
    });
}
```

2.在集成了RN界面的控制器TJDMeViewController中添加观察者接受通知，实现界面的跳转

```
 [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(doPushNotification:) name:@"RNOpenOneVC" object:nil];
 -(void)doPushNotification:(NSNotification *)notification{
    RNToiOSController *one = [[RNToiOSController alloc]init];
    [self.navigationController pushViewController:one animated:YES];
}
```

3.js中调用

```
var RNMoodules = NativeModules.RTModule;
 <TouchableOpacity style={styles.touch1} onPress={()=>RNMoodules.RNOpenOneVC('测试')}>
 </TouchableOpacity>
```



