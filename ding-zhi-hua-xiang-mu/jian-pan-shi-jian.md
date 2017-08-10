# 解决React Native中ScrollView中控件获得焦点及点击空白处键盘消失的问题

[http://blog.csdn.net/wwt831208/article/details/54705978](http://blog.csdn.net/wwt831208/article/details/54705978)



```jsx
            <KeyboardAwareScrollView
                extraHeight={150}
                keyboardShouldPersistTaps="always"
                style={{backgroundColor: '#fff'}}
                resetScrollToCoords={{x: 0, y: 0}}
                contentContainerStyle={{alignItems: 'stretch',
                    flexDirection: 'column'}}
                scrollEnabled={false}>
                <TouchableOpacity activeOpacity={1.0} style={styles.container} onPress={this._dismissKeyboardClick.bind(this)}>
  
                </TouchableOpacity>
            </KeyboardAwareScrollView>
            
   _dismissKeyboardClick(){
        Keyboard.dismiss();
    }
```

主要属性 `keyboardShouldPersistTaps="always" `  需要嵌套`TouchableOpacity`组件用来处理空白处键盘收起的事件

