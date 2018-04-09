官方文档链接  https://reactnavigation.org/docs/navigation-prop.html

this.props.navigation新增4个Action：

1. Push  与navigate类似，向前跳转
   `navigation.push(routeName, params, action)`

2. Pop  栈里弹出一个页面，回到上级，参数n表示向上弹出多少个页面  
   `navigation.pop(n)`

3. PopToTop  返回第一个页面，关闭其他所有  
   `navigation.popToTop()`

4. Replace  将当前页面换成其他页面  
   `navigation.replace(routeName, params, action)`

新增addListener 监听页面生命周期：

`1.willBlur 即将失去焦点`

2.willFocus 即将获得焦点 

3.didFocus 获得焦点 

4.didBlur 失去焦点 

did的两个状态会在页面切换结束后发生

```js
const didBlurSubscription = this.props.navigation.addListener(
  'didBlur',     //失去焦点后打印信息
  payload => {
    console.debug('didBlur', payload);
  }
);

// 在完成后取消监听事件
didBlurSubscription.remove();
```

isFocused 判断当前页面是否获得焦点

```js
let isFocused = this.props.navigation.isFocused(); //true获取false未获取
```

state 返回当前navigation的状态，自定义Action里需要的key可以在这里获取

```js
{
  // 页面名称
  routeName: 'profile',
  // 页面的key值
  key: 'main0',
  // 参数
  params: { hello: 'world' }
}
```

setParams,getParm 设置/获取navigation的参数params

非页面（如自定义组件）现在提供了获取导航器的方法 withNavigation

```js
import React from 'react';
import { Button } 'react-native';
import { withNavigation } from 'react-navigation';

class MyBackButton extends React.Component {
  render() {
    return <Button title="Back" onPress={() => { this.props.navigation.goBack() }} />;
  }
}

// withNavigation returns a component that wraps MyBackButton and passes in the
// navigation prop
export default withNavigation(MyBackButton);    //调用withNavigation(Component)后，可以使用this.props.navigation获取导航器，此方法返回Component
```

withNavigationFocus除了为非页面组件提供navigation，还提供了isFocused属性判断组件是否获取焦点

```js
import React from 'react';
import { Text } 'react-native';
import { withNavigationFocus } from 'react-navigation';

class FocusStateLabel extends React.Component {
  render() {
    return <Text>{this.props.isFocused ? 'Focused' : 'Not focused'}</Text>;
  }
}

// withNavigationFocus returns a component that wraps FocusStateLabel and passes
// in the navigation prop
export default withNavigationFocus(FocusStateLabel);
```



