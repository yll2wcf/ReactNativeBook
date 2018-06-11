经测试CodePush的热更新效果还是很稳定的，集成方法如下：

官网 https://github.com/Microsoft/react-native-code-push

注册地址 https://appcenter.ms/apps

参考文章 https://blog.csdn.net/qq\_33323251/article/details/79437932 部分内容有改动

Step1！

安装CodePush客户端

```
npm install -g code-push-cli
```

这个在一台计算机上运行一次就可以了

```
code-push -v
```

执行上面这个命令，如果看到版本证明安装成功

Step2！

CodePush注册

```
code-push register
```

输入后跳网页，登录后会返给一个KEY，控制台输入这个key即可。

```
code-push login
```

使用上述指令登录。

Step3！

注册APP

安卓IOS需要分开注册

```
code-push app add <appName> <os> <platform>
```

例如直播项目

```
code-push app add StreamProjectIOS ios react-native
code-push app add StreamProjectAndroid android react-native
```

注册完成后会返回一套deployment key

![](/assets/import.png)

Production为最终上线后的KEY，Staging是灰度测试的KEY，一般先用staging，测试成功后codePush有指令将灰度的内容推到正式版上。

Step4！

集成

建议采用rnpm集成，现在react-native已经集成了rnpm，使用非常简易

```
npm install --save react-native-code-push
react-native link react-native-code-push
```

link的时候会提示输入key，先使用注册APP时返回的staging即可，IOS安卓分别输入。

至此IOS集成完成，安卓按官网说也已经完成，但测试时发现Package没有导入，还需要一些步骤

MainApplication里

```
@Override
    protected List<ReactPackage> getPackages() {
      // 3. Instantiate an instance of the CodePush runtime and add it to the list of
      // existing packages, specifying the right deployment key. If you don't already
      // have it, you can run "code-push deployment ls <appName> -k" to retrieve your key.
      return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        new CodePush("deployment-key-here", MainApplication.this, BuildConfig.DEBUG)
      );
    }
```

具体参考开头链接╮\(╯▽╰\)╭

Step5！

RN代码更新

默认采取静默更新，打开APP检查是否有更新==》有的话下载安装==》下次启动即为更新后的

rn代码更新index.js文件（app根目录）

```
import codePush from "react-native-code-push";  //导包

class MyApp extends Component<{}>{
    //打印一些状态，optional
    codePushStatusDidChange(status) {
        switch(status) {
            case codePush.SyncStatus.CHECKING_FOR_UPDATE:
                console.log("Checking for updates.");
                break;
            case codePush.SyncStatus.DOWNLOADING_PACKAGE:
                console.log("Downloading package.");
                Alert.alert('Downloading package');
                break;
            case codePush.SyncStatus.INSTALLING_UPDATE:
                console.log("Installing update.");
                break;
            case codePush.SyncStatus.UP_TO_DATE:
                console.log("Up-to-date.");
                Alert.alert('Up-to-date');
                break;
            case codePush.SyncStatus.UPDATE_INSTALLED:
                console.log("Update installed.");
                break;
        }
    }
    //打印下载进度，optional
    codePushDownloadDidProgress(progress) {
        console.log(progress.receivedBytes + " of " + progress.totalBytes + " received.");
    }

    render(){
        return(
            <App/>  //APP是react-navigation导入的
        )
    }
}

MyApp=codePush(MyApp);  //就这一行是必须的

AppRegistry.registerComponent('ZhuoShopping', () => MyApp);

```

如果需要其他更新方式（例如弹出提示更新）在codePush\(MyApp\)方法中再传入Option的参数，具体参考官网。

至此全部准备工作完成。

Step 6！

发布热更新

修改了RN代码后，运行下面指令发布更新

```
code-push release-react StreamProjectIOS ios  //更新IOS包
code-push release-react StreamProjectAndroid android  //更新Android包
```

完事！



注意事项！！！！

android打包使用Build==》general signed Apk这样生成的APK热更新后再打开就回滚，所以打包时需要使用官网的打包方式：

https://facebook.github.io/react-native/docs/signed-apk-android.html

```
cd android && ./gradlew assembleRelease   //使用此命令打包
```



CodePush是微软的，服务器在国外，速度是个隐患（测试时还行）

构建自己的CodePushServer

https://github.com/lisong/code-push-server

