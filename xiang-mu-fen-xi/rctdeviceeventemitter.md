RCTDeviceEventEmitter这个组件本来是用于iOS源生与react native交互的，而由于react native没有类似onResume的方法导致在关闭某页面后更新其他页面的操作不好做，可以通过使用RCTDeviceEventEmitter这个组件完成。

```
import RCTDeviceEventEmitter from 'RCTDeviceEventEmitter';        //react native自带，需要时引用即可
```

在需要更新的页面，以及退出页面都要引用。

例如A页面跳转到B页面，关闭B页面后A页面更新某些部分：

```
//在A页面先引用
import RCTDeviceEventEmitter from 'RCTDeviceEventEmitter';
//组件加载后添加listener监听事件
componentDidMount() {
        RCTDeviceEventEmitter.addListener('valueChange', this._handleLoginState);
}
//组件即将卸载时删除监听事件
componentWillUnmount() {
        RCTDeviceEventEmitter.removeListener('valueChange', this._handleLoginState);
}
//回调方法，更新页面的操作放在这里，可以带参数
_handleLoginState() {

}
```

```
//在B页面也需要引用
import RCTDeviceEventEmitter from 'RCTDeviceEventEmitter';
//返回时发出事件以调用回调方法
_LogoutButtonCallback() {   //退出登录，调用回调函数更新MyPage
        RCTDeviceEventEmitter.emit('valueChange');    需要传参的话RCTDeviceEventEmitter.emit('valueChange', '需要传的参数');
        this._handleBack();  //退出的方法
    }
```

取消监听removeListener不能正确移除监听，正确写法：

```js
//组件加载后添加listener监听事件
componentDidMount() {
        this.listener = DeviceEventEmitter.addListener('valueChange', this._handleLoginState);
}
//组件即将卸载时删除监听事件
componentWillUnmount() {
        this.listener.remove();
}

```



