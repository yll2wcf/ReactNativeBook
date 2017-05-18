#React-Navigation 

###参考文献 
[react-navigation使用技巧](http://www.jianshu.com/p/2f575cc35780)

[react-native新导航组件react-navigation详解](http://blog.csdn.net/sinat_17775997/article/details/70176688)

[react-navigation Navigation使用](http://blog.csdn.net/u013147860/article/details/68926816)

###示例代码:

```js
   const { navigate } = this.props.navigation;
        navigate("ProgramDetailPage",{projectid: item.projectid});
```
跳转到 `ProgramDetailPage`界面 后面是传递的参数

`ProgramDetailPage`需要在 `App.js` 中声明

```js
const MyApp = StackNavigator{
      ...
      ProgramDetailPage: {
        screen: ProgramDetailPage,
        navigationOptions: {
            gesturesEnabled:true,
            header:null
        }
    }
}
```

取数据
```js
    componentWillMount() {
        const {params} = this.props.navigation.state;
        var projectid =params.projectid;
        console.log("projectid:"+projectid);
    }
```


Reset: Reset方法会清除原来的路由记录，添加上新设置的路由信息, 可以指定多个action，index是指定默认显示的那个路由页面, 注意不要越界了
```js
  import { NavigationActions } from 'react-navigation'

  const resetAction = NavigationActions.reset({
    index: 0,
    actions: [
      NavigationActions.navigate({ routeName: 'Profile'}),
      NavigationActions.navigate({ routeName: 'Two'})
    ]
  })
  this.props.navigation.dispatch(resetAction)
```