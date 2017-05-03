## 构建TabBar布局

许多项目需要使用TABBAR作为主要布局方式，推荐第三方控件：

REACT-NATIVE-TAB-NAVIGATOR

> https://js.coach/react-native/react-native-tab-navigator?search=react-native-tab-navigator

创建Tabbar控件：

```
render() {
        const {selectedColor} = this.props;
        const {tabName} = this.state;
        return (
            <TabNavigator
                hidesTabTouch={true}
                tabBarStyle={styles.tabbar}
                sceneStyle={{ paddingBottom: styles.tabbar.height }}>
                <TabNavigator.Item
                    tabStyle={styles.tabStyle}
                    title={tabName[0]}
                    selected={this.state.selectedTab === 'home'}
                    selectedTitleStyle={{ color: selectedColor }}
                    renderIcon={() => <Image style={styles.tab} source={require("../image/tab_home.png")} />}
                    renderSelectedIcon={() => <Image style={styles.tab} source={require("../image/tab_home_sg.png")} />}
                    onPress={() => this.setState({ selectedTab: 'home' })}>
                    {<HomePage navigator={this.props.navigator} />}
                </TabNavigator.Item>
                <TabNavigator.Item
                    tabStyle={styles.tabStyle}
                    title={tabName[1]}
                    selected={this.state.selectedTab === 'company'}
                    selectedTitleStyle={{ color: selectedColor }}
                    renderIcon={() => <Image style={styles.tab} source={require("../image/tab_appeal.png")} />}
                    renderSelectedIcon={() => <Image style={styles.tab} source={require("../image/tab_appeal_sg.png")}/>}
                    onPress={() => this.setState({ selectedTab: 'company' })}>
                    {<CompanyRequestPage navigator={this.props.navigator}/>}
                </TabNavigator.Item>
                <TabNavigator.Item
                    tabStyle={styles.tabStyle}
                    title={tabName[2]}
                    selected={this.state.selectedTab === 'myinfo'}
                    selectedTitleStyle={{ color: selectedColor }}
                    renderIcon={() => <Image style={styles.tab} source={require("../image/tab_person.png")} />}
                    renderSelectedIcon={() => <Image style={styles.tab} source={require("../image/tab_person_sg.png")} />}
                    onPress={() => this.setState({ selectedTab: 'myinfo' })}>
                    {<MyPageNavigator navigator={this.props.navigator} />}
                </TabNavigator.Item>
            </TabNavigator>
        );
    }
```

对于我们所关心的页面切换，在TabNavigator.Item中，可将其置于&lt;TabNavigator.Item&gt;{&lt;View/&gt;}&lt;/TabNavigator.Item&gt;之中，即作为Item的子元素存在，这里请注意：如果添加了一个Item，必须为其添加一个View，否则将无法运行！

这里还需要传入navigator，在入口文件（如果tabbar页面处于打开应用的第一个页面的话）创建navigator如下：

```
export default class Navigation extends Component {

    render() {
        return (
            <Navigator
                initialRoute={{ component: MainScene }}
                renderScene={(route, navigator) => {
                    return <route.component navigator={navigator} {...route.args} />
                }
                } />
        );
    }

    componentDidMount() {
    }
}
```

MainScene为初始页面，在此页面内加入Tabbar：

```
render() {
        return (
            <View style={styles.screen}>
                <TabBar navigator={this.props.navigator} />
            </View>
        )
    }
```

这样就完成了三个页面的TABBAR布局。

页面中需要跳转的话调用navigator.push的方法。这里有两种情况，第一种即跳转后页面不需要显示tabbar，直接调用即可：

```
_applyButtonCallback() {  //按钮点击的事件
        Keyboard.dismiss();  //关闭软键盘再跳转
        this.props.navigator.push({
            component: ApplyPage   //需要import
        });
    }

```

还有一种情况为跳转后希望tabbar依然显示，这种我们需要在tabbar的item页面里重新定义一个navigator，例如上面代码里第三个item：MyPageNavigator，其代码为：

```
export default class MyPageNavigator extends Component {


    render() {
        return (
            <Navigator
                initialRoute={{ component: MyPage }}
                renderScene={(route, navigator) => {
                    return <route.component navigator={navigator} {...route.args} />
                }
                } />
        );
    }

    componentDidMount() {
    }
}

```

MyPage就是我们点击第三个标签后显示的页面，在MyPage里我们应对两种跳转方式需要获取不同的navigator，因为MyPage已经处于新的navigator下，这时调用this.props.navigator获取的是MyPageNavigator（class MyPageNavigator里这个），这样在PUSH后tabbar依然显示。如果不想要tabbar显示，我们需要获取我们在入口文件里定义的navigator（class Navigation里那个），所以需要暴露一个方法，我们在mainScene这个页面里有这样的代码：&lt;TabBar navigator={this.props.navigator} /&gt;，这个this.props.navigator就是我们需要的，所以在同一文件下export一个方法返回这个navigator就行了：

```
'use strict';

import React, { Component } from 'react';
import { Text, View, BackAndroid, Platform, StyleSheet } from 'react-native';
import TabBar from '../component/TabBar';

var MainNavigator;  //定义变量用来保存navigator

export default class MainScene extends Component {
    constructor(props) {
        super(props);
    }

    componentDidMount() {  //在组件挂载的时候将需要的navigator传给MainNavigator
        MainNavigator= this.props.navigator;
        BackAndroid.addEventListener('hardwareBackPress', function () {  //顺带处理一下android回退按钮的点击事件
            BackAndroid.exitApp(0);
            return true;
        });
    }

    render() {
        return (
            <View style={styles.screen}>
                <TabBar navigator={this.props.navigator} />
            </View>
        )
    }
}

export function getNavigator(){  //将初始化后的MainNavigator暴露出来
    return MainNavigator;  
}

const styles = StyleSheet.create({
    screen:{
        flex:1, 
        justifyContent: 'flex-end'  //主轴
    }
});
```

在MyPage里首先import { getNavigator } from './MainScene'，然后使用这个方法拿到navigator后再push，这样新页面就没有tabbar了。

```
getNavigator().push({
                    component: LoginPage
                });
```



