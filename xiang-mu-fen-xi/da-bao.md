# 打包

[http://blog.csdn.net/q617610589/article/details/51094248](http://blog.csdn.net/q617610589/article/details/51094248)

```js
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/
```

打包混淆失败需要加入

```py
-keep class android.text {* ;}
-dontwarn android.text.*
```

[http://blog.csdn.net/gyh790005156/article/details/62889522](http://blog.csdn.net/gyh790005156/article/details/62889522)







react-native bundle --entry-file index.js --bundle-output ./ios/bundle/index.ios.jsbundle --platform ios --assets-dest ./ios/bundle --dev false