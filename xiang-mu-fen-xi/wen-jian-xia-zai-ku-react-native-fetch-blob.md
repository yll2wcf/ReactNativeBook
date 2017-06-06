对于react native里的文件下载问题，可以使用react-native-fetch-blob这个库

> [https://github.com/wkh237/react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob)

WHY?

> [https://github.com/facebook/react-native/issues/854](https://github.com/facebook/react-native/issues/854)

When you use`.blob()`on the response from`fetch`, is the data actually marshaled across the bridge? Or is the **blob**

a handle on the data on the native side?

大概的意思就是react native 在处理二进制数据传递时会出问题，讨论的做法是数据传回源生处理，所以官方给的解决方法就是使用这个库。。官方回答是：（意思就是这种小问题你们用第三方就好了别让我们改╮\(╯▽╰\)╭）

I think it's okay if the best answers are libraries like react-native-fetch-blob, for what it's worth - we don't need to solve every single thing inside the core library.

用法：

对于比较小的文件：

```js
RNFetchBlob.fetch('GET', 'http://www.example.com/images/img1.png', {
    Authorization : 'Bearer access-token...',
    // more headers  ..
  })
  // when response status code is 200
  .then((res) => {
    // the conversion is done in native code
    let base64Str = res.base64()
    // the following conversions are done in js, it's SYNC
    let text = res.text()
    let json = res.json()

  })
  // Status code is not 200
  .catch((errorMessage, statusCode) => {
    // error handling
  })
```

这个用的是BASE64，但是对于大文件就不太合适，可以把文件下载到storage

```js
let dirs = RNFetchBlob.fs.dirs
RNFetchBlob
  .config({
    // add this option that makes response data to be stored as a file,
    // this is much more performant.
    fileCache : true,
    //appendExt : 'png'  //添加后缀，默认下载的文件是没有后缀的，名字为随机数组字母组合
    //path : dirs.DocumentDir + '/path-to-file.anything'  //设定特定的路径，可以传文件名后缀，设置了path的话filecache与appendExt就不生效了
  })
  .fetch('GET', 'http://www.example.com/file/example.zip', {
    //some headers ..
  })
  .then((res) => {
    // the temp file path
    console.log('The file saved to ', res.path())
    // Beware that when using a file path as Image source on Android,
    // you must prepend "file://"" before the file path   
    //返回的路径前不带file：//，对于android需要自己添加
    imageView = <Image source={{ uri : Platform.OS === 'android' ? 'file://' + res.path()  : '' + res.path() }}/>
  })
```

DocumentDir为下载路径的前半部分，其他支持的路径有：

```js
const dirs = {
    DocumentDir :  RNFetchBlob.DocumentDir,
    CacheDir : RNFetchBlob.CacheDir,
    PictureDir : RNFetchBlob.PictureDir,                
    MusicDir : RNFetchBlob.MusicDir,
    MovieDir : RNFetchBlob.MovieDir,
    DownloadDir : RNFetchBlob.DownloadDir,
    DCIMDir : RNFetchBlob.DCIMDir,
    SDCardDir : RNFetchBlob.SDCardDir,
    SDCardApplicationDir : RNFetchBlob.SDCardApplicationDir,
    MainBundleDir : RNFetchBlob.MainBundleDir,
    LibraryDir : RNFetchBlob.LibraryDir
}
```

代码里已经演示了如何使用下载的文件（imageView加载下载图片），但很多情况下我们需要其他软件打卡下载文件，例如pdf，word文档或android上使用下载apk文件，对于IOS可以直接使用方法openDocument：

```js
RNFetchBlob.ios.openDocument(res.path())
                        .then(() => console.log('Previewing document', res.path()))
                        .catch((err) => console.error('Error opening document', err))
```

对于Android端，库作者给的建议是使用androiddownloadmanager下载然后用intent打开，使用方法actionViewIntent

```js
RNFetchBlob.config({
                    //fileCache: true,
                    // by adding this option, the temp files will have a file extension
                    //appendExt: 'png'
                    addAndroidDownloads: {
                        useDownloadManager: true,
                        title: filename + suffix,   //filename文件名，suffix后缀，filepathshow为下载url
                        // mime: 'image/png',
                        path: RNFetchBlob.fs.dirs.DownloadDir + '/' + filename + suffix
                    }
                })
                .fetch('GET', filepathshow, {
                    //some headers ..
                })
                .then((res) => {
                    let url = res.path();
                    RNFetchBlob.android.actionViewIntent(url, 'image/png')
                         .then((success) => {
                             console.log('success: ', success)
                         })
                         .catch((err) => {
                             console.log('err:', err)
                         })
                    //NativeModules.OpenDownloadModule.openDownload(url);
                })
```

不过这种方法在android7.0限制了外部程序访问根目录后会报错

> [https://github.com/wkh237/react-native-fetch-blob/issues/358](https://github.com/wkh237/react-native-fetch-blob/issues/358)

exposed beyond app through Intent.getData\(\)

熟悉android开发的话这个问题的解决方法就是配置fileprovider，具体参考：

> [http://blog.csdn.net/yulianlin/article/details/52775160](http://blog.csdn.net/yulianlin/article/details/52775160)

react native调用源生代码方法：

> http://blog.csdn.net/qq\_25827845/article/details/52862892

这里的OpenDownloadModule代码为：

```java
public class OpenDownloadModule extends ReactContextBaseJavaModule {

    private Context mContext;

    private final String[][] MIME_MapTable = {   //{后缀名，MIME类型}
            {".3gp", "video/3gpp"},
            {".apk", "application/vnd.android.package-archive"},
            {".asf", "video/x-ms-asf"},
            {".avi", "video/x-msvideo"},
            {".bin", "application/octet-stream"},
            {".bmp", "image/bmp"},
            {".c", "text/plain"},
            {".class", "application/octet-stream"},
            {".conf", "text/plain"},
            {".cpp", "text/plain"},
            {".doc", "application/msword"},
            {".docx", "application/vnd.openxmlformats-officedocument.wordprocessingml.document"},
            {".xls", "application/vnd.ms-excel"},
            {".xlsx", "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"},
            {".exe", "application/octet-stream"},
            {".gif", "image/gif"},
            {".gtar", "application/x-gtar"},
            {".gz", "application/x-gzip"},
            {".h", "text/plain"},
            {".htm", "text/html"},
            {".html", "text/html"},
            {".jar", "application/java-archive"},
            {".java", "text/plain"},
            {".jpeg", "image/jpeg"},
            {".jpg", "image/jpeg"},
            {".js", "application/x-JavaScript"},
            {".log", "text/plain"},
            {".m3u", "audio/x-mpegurl"},
            {".m4a", "audio/mp4a-latm"},
            {".m4b", "audio/mp4a-latm"},
            {".m4p", "audio/mp4a-latm"},
            {".m4u", "video/vnd.mpegurl"},
            {".m4v", "video/x-m4v"},
            {".mov", "video/quicktime"},
            {".mp2", "audio/x-mpeg"},
            {".mp3", "audio/x-mpeg"},
            {".mp4", "video/mp4"},
            {".mpc", "application/vnd.mpohun.certificate"},
            {".mpe", "video/mpeg"},
            {".mpeg", "video/mpeg"},
            {".mpg", "video/mpeg"},
            {".mpg4", "video/mp4"},
            {".mpga", "audio/mpeg"},
            {".msg", "application/vnd.ms-outlook"},
            {".ogg", "audio/ogg"},
            {".pdf", "application/pdf"},
            {".png", "image/png"},
            {".pps", "application/vnd.ms-powerpoint"},
            {".ppt", "application/vnd.ms-powerpoint"},
            {".pptx", "application/vnd.openxmlformats-officedocument.presentationml.presentation"},
            {".prop", "text/plain"},
            {".rc", "text/plain"},
            {".rmvb", "audio/x-pn-realaudio"},
            {".rtf", "application/rtf"},
            {".sh", "text/plain"},
            {".tar", "application/x-tar"},
            {".tgz", "application/x-compressed"},
            {".txt", "text/plain"},
            {".wav", "audio/x-wav"},
            {".wma", "audio/x-ms-wma"},
            {".wmv", "audio/x-ms-wmv"},
            {".wps", "application/vnd.ms-works"},
            {".xml", "text/plain"},
            {".z", "application/x-compress"},
            {".zip", "application/x-zip-compressed"},
            {"", "*/*"}
    };

    public OpenDownloadModule(ReactApplicationContext reactContext) {
        super(reactContext);

        mContext = reactContext;
    }

    @Override
    public String getName() {
        return "OpenDownloadModule";
    }

    @ReactMethod
    public void openDownload(String url) {
        //Toast.makeText(mContext,url,Toast.LENGTH_SHORT).show();
        File file = new File(url);
        Intent intent = new Intent(Intent.ACTION_VIEW);
        // 由于没有在Activity环境下启动Activity,设置下面的标签
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        String type = getMIMEType(file);
        if (Build.VERSION.SDK_INT >= 24) { //判读版本是否在7.0以上
            //添加这一句表示对目标应用临时授权该Uri所代表的文件
            Uri uri = FileProvider.getUriForFile(mContext, "app.troila.zqpt.qy.provider", file);
            //添加这一句表示对目标应用临时授权该Uri所代表的文件
            intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
            intent.setDataAndType(uri, type);
        } else {
            intent.setDataAndType(Uri.fromFile(file),
                    type);
        }
        try{
            mContext.startActivity(intent);
        }catch (Exception e){
            Toast.makeText(mContext,"您的手机上没有可以打开该类型文件的软件",Toast.LENGTH_SHORT).show();
        }
    }

    private String getMIMEType(File file) {

        String type = "*/*";
        String fName = file.getName();  //获取后缀名前的分隔符"."在fName中的位置。
        int dotIndex = fName.lastIndexOf(".");
        if (dotIndex < 0) {
            return type;
        }/* 获取文件的后缀名*/
        String end = fName.substring(dotIndex, fName.length()).toLowerCase();
        if (end.equals("")) return type;
        for (int i = 0; i < MIME_MapTable.length; i++) {
            if (end.equals(MIME_MapTable[i][0]))
                type = MIME_MapTable[i][1];
        }
        return type;
    }
}
```
这里还需要注意如果你使用的其他第三方库也使用了fileprovider，会报Manifest merger failed错误，可以使用一个provider，把你需要的paths(xml里定义那个）放到其他三方定义的paths里即可。


