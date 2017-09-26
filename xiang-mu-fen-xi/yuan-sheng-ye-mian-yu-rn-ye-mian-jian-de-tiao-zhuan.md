1.从原生页面跳转RN页面  
基本流程按照官网教程[http://reactnative.cn/docs/0.48/integration-with-existing-apps.html\#content](http://reactnative.cn/docs/0.48/integration-with-existing-apps.html#content)  
依赖改为0.48版本, package.json为：

```js
{
  "name": "ReactTest",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "16.0.0-alpha.6",
    "react-native": "0.44.3"
  }
}
```

6.0以上版本权限控制：

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            if (!Settings.canDrawOverlays(this)) {
                Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
                        Uri.parse("package:" + getPackageName()));
                startActivityForResult(intent, OVERLAY_PERMISSION_REQ_CODE);
            }else{
                initView();
            }
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == OVERLAY_PERMISSION_REQ_CODE) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                if (!Settings.canDrawOverlays(this)) {
                    // SYSTEM_ALERT_WINDOW permission not granted...
                }else{
                    initView();
                }
            }
        }
    }

    public void initView(){
        mReactRootView = new ReactRootView(this);
        mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setBundleAssetName("indexY.android.bundle")
                .setJSMainModuleName("indexY.android")
                .addPackage(new MainReactPackage())
                .setUseDeveloperSupport(BuildConfig.DEBUG)
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();

        // 注意这里的MyReactNativeApp必须对应“index.android.js”中的
        // “AppRegistry.registerComponent()”的第一个参数
        mReactRootView.startReactApplication(mReactInstanceManager, "MyReactNativeSecApp", null);
        setContentView(mReactRootView);
    }
```

需要加载多个bundle时，修改setBundleAssetName\("indexY.android.bundle"\)与setJSMainModuleName\("indexY.android"\)，同一时间只能调试一个bundle的内容，多个在调试时会报错（Relaod即可解决），打包后运行正常。

启动时如果需要传递参数，添加一个bundle：

```java
        Bundle bundle=new Bundle();
        bundle.putString("KEY", "123");

        // 注意这里的MyReactNativeApp必须对应“index.android.js”中的
        // “AppRegistry.registerComponent()”的第一个参数
        mReactRootView.startReactApplication(mReactInstanceManager, "MyReactNativeApp", bundle);
        setContentView(mReactRootView);
```

JS里通过props调用：

```js
<Text style={styles.hello}>{this.props.KEY}</Text>
```



2.从RN页面跳转原生页面

新建Modules（包含需要执行的方法，这里既跳转Intent）：

```java
public class MyIntentModule extends ReactContextBaseJavaModule {
    public MyIntentModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Override
    public String getName() {
        return "IntentMoudle";
    }
    @ReactMethod
    public void startActivityFromJS(String name, String params){
        try{
            Activity currentActivity = getCurrentActivity();
            if(null!=currentActivity){
                Class toActivity = Class.forName(name);
                Intent intent = new Intent(currentActivity,toActivity);
                intent.putExtra("params", params);
                currentActivity.startActivity(intent);
            }
        }catch(Exception e){
            throw new JSApplicationIllegalArgumentException(
                    "不能打开Activity : "+e.getMessage());
        }
    }
}
```

新建Package并添加刚刚建好的modules：

```java
public class MyReactPackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        return Arrays.<NativeModule>asList(new MyIntentModule(reactContext));
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}
```

将Package添加到ReactInstance里：

```java
mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setBundleAssetName("indexX.android.bundle")
                .setJSMainModuleName("indexX.android")
                .addPackage(new MainReactPackage())
                .addPackage(new MyReactPackage())             //添加的Package
                .setUseDeveloperSupport(BuildConfig.DEBUG)
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();
```

在JS里调用：

```js
<TouchableOpacity onPress={() =>
    NativeModules.IntentMoudle.startActivityFromJS("com.example.mu.reacttest.SecondActivity","2345")}>
    <Text style={styles.hello}>Back</Text>
</TouchableOpacity>
```



