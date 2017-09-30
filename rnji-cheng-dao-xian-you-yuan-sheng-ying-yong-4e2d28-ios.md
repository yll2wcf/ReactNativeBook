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

1.

