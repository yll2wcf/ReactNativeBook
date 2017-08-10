ScrollView包裹在Touchable组件下无法滚动

```js
<TouchableWithoutFeedback onPress={this.handlePress.bind(this)}>
<View>
<ScrollView>
<Text>
Hello, here is a very long text that needs scrolling.
</Text>
<ScrollView>
</View>
</TouchableWithoutFeedback>
```

解决办法在需要滚动部分外层再套一层TouchableOpacity

```js
<TouchableWithoutFeedback onPress={this.handlePress.bind(this)}>
<View>
<ScrollView>
<TouchableOpacity>
<Text>
Hello, here is a very long text that needs scrolling.
</Text>
</TouchableOpacity>
<ScrollView>
</View>
</TouchableWithoutFeedback>
```



