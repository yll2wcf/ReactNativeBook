## 页面跳转

### Tabbar

https://js.coach/react-native/react-native-tab-navigator?search=react-native-tab-navigator

https://reactnavigation.org/

https://wix.github.io/react-native-navigation/#/installation-android

###scrollable-tab-view
https://github.com/skv-headless/react-native-scrollable-tab-view


### Navigator

popToRoute\(\) not working when I pass in route object

一次跳回多个页面popToRoute\(\)报找不到路径错误

解决方法1：

```
this.props.navigator.popToRoute(this.props.navigator.getCurrentRoutes()[1]);
```

需要知道页面在第几层路径上

解决方法2：

```
var routes = this.props.navigator.state.routeStack;
for (var i = routes.length - 1; i >= 0; i--) {
  if(routes[i].name === "MyDestinationRoute"){
    var destinationRoute = this.props.navigator.getCurrentRoutes()[i]
    this.props.navigator.popToRoute(destinationRoute);
  }
}
```

通过名字跳转

http://stackoverflow.com/questions/33227181/poptoroute-not-working-when-i-pass-in-route-object

### TextInput多行从顶行开始

Style里设定

textAlignVertical: 'top'



