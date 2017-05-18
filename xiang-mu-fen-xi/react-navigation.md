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

```
const MyApp = StackNavigator{

}
```