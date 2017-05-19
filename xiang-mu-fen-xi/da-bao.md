#打包

http://blog.csdn.net/q617610589/article/details/51094248


```js
react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/
```


打包混淆失败需要加入
```
-keep class android.text {* ;}
-dontwarn android.text.*

```
http://blog.csdn.net/gyh790005156/article/details/62889522

