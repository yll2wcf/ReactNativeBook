RN 0.55.2 版本BUG

1.textInput 设置value后，每次输入非英文字符页面刷新导致输入不连贯。需要默认值的情况，现在只能使用defaultValue。

典型场景登录页记录账号信息，在读取本地缓存后需要同时设置value和defaultValue

```js
async _getSavedInfo() {
        try {
            let info = await getItem('AccountInfo');
            this.setState({
                userName: info.userName,
                password: info.password,
                userNameDefault: info.userName,
                passwordDefault: info.password
            })
        } catch (e) {
            console.log(e)
        }
    }
```

2.滑动事件PanResponder在现有版本IOS系统上存在BUG，滑动后，滑动的view会莫名被挤压导致内容显示不全，大量三方库受影响（例如react-native-drawer，侧滑删除组件react-native-swipeout）

暂时的解决办法：

在滑动开始，滑动结束的回调方法中极小的调整view的宽度以强制页面刷新

```js
<Swipeout right={swipeButton} backgroundColor='#fff'
                      autoClose={true} 
                      onOpen={()=>this.setState({margin:px2dp(13)})} //打开关闭时调整了margin强制刷新页面
                      onClose={()=>this.setState({margin:px2dp(12)})}>
                <TouchableOpacity activeOpacity={0.6}
                                  style={{height: px2dp(100), flexDirection: 'row', alignItems: 'center',paddingHorizontal:px2dp(32)}}
                                  onPress={this._itemPress.bind(this, item)}>
                    <TextFix style={{
                        fontSize: px2dp(32),
                        color: '#333',
                        flex:1,
                        marginLeft:this.state.margin
                    }} numberOfLines={1}>{item.enterpriseName}</TextFix>
                    <TextFix style={{
                        fontSize: px2dp(32),
                        color: '#333'
                    }}>{item.visitTime}</TextFix>
                </TouchableOpacity>
            </Swipeout>
```



