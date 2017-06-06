# React-Navigation

### 参考文献

[react-navigation使用技巧](http://www.jianshu.com/p/2f575cc35780)

[react-native新导航组件react-navigation详解](http://blog.csdn.net/sinat_17775997/article/details/70176688)

[react-navigation Navigation使用](http://blog.csdn.net/u013147860/article/details/68926816)

### 示例代码:

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

自定义navigation的一些跳转方式

```js
const defaultGetStateForAction = MyApp.router.getStateForAction;

MyApp.router.getStateForAction = (passedAction, state) => {
    if (state && state.routes && state.routes.length > 2
        && passedAction.type === 'POP_TWO_ROUTES') {    //一次回跳两个页面
        let routes = state.routes.slice();
        routes.pop();
        routes.pop();

        return {
            index: routes.length - 1,
            routes: routes
        };
    } else if (state && state.routes && state.routes.length > 1
        && passedAction.type === 'POP_BEFORE_PUSH') {     //在跳转前关闭当前页
        let routes = state.routes.slice();
        routes.pop();
        routes.push({ key: passedAction.target, routeName: passedAction.target });

        return {
            index: routes.length - 1,
            routes: routes
        };
    } else if (state && state.routes && passedAction.type === 'AVOID_MULTI') {  //尝试避免多次点击但是会引发其他BUG，弃用
        let routes = state.routes.slice();                                   //目前没有很好的避免多次跳转的方法
        let canadd = true;                                                   //官方未来会就这一问题更新 2017/05/31 
        for (let i in routes) {
            if (passedAction.target == routes[i].routeName) {
                canadd = false;
            }
        }
        if (canadd) { routes.push({ key: passedAction.target, routeName: passedAction.target, params: passedAction.params }); }

        return {
            index: routes.length - 1,
            routes: routes
        };
    }
    // default behaviour for none custom types
    return defaultGetStateForAction(passedAction, state);
}
```

在使用时，通过下面方法调用

```
this.props.navigation.dispatch({
                        type: 'POP_TWO_ROUTES'
                    })
this.props.navigation.dispatch({
                    type: 'POP_BEFORE_PUSH',
                    target: 'DevelopPage'
                })
```



