[https://github.com/razor1895/react-native-refreshable-flatlist](https://github.com/razor1895/react-native-refreshable-flatlist)

这个库对flatlist进行了封装，使其在保留flatlist属性的前提下额外多了一些属性：

showTopIndicator: bool  是否显示顶部加载器

showBottomIndicator: bool 是否显示底部加载器

topIndicatorComponent: oneOfType\(\[func, element\]\) 顶部加载器组件

bottomIndicatorComponent: oneOfType\(\[func, element\]\) 底部加载器组件

minDisplayTime: number 最小显示时间

minPullDownDistance: number 最小下拉距离

minPullUpDistance: number 最小上拉距离

onRefreshing: func   onLoadMore: func  不同于flatlist需要返回一个Promise对象

```js
onRefreshing={() => new Promise((r) => {         //必须是promise否则没有加载动画
                        setTimeout(() => {
                            r();
                        }, 3000);
                    })}
```

对于网络请求，因为fetch Api本身就返回Promise对象，所以可以直接用

```js
onRefreshing={() => fetch(url, options).then(res => {
  //...do sth with the response
})}
```

常用的自定义加载器代码，使用了一张“箭头”图片，然后根据不同状态旋转不同角度，加载中使用ActivityIndicator效果

```js
import React, { Component } from 'react';
import {
    View,
    Text,
    StyleSheet,
    Animated,
    ActivityIndicator,
    Easing
} from 'react-native';
import PropTypes from 'prop-types';

export default class CustomIndicator extends Component {
    static propTypes = {
        refreshing: PropTypes.bool.isRequired,
        scrollStatus: PropTypes.oneOf([
            'NOT_EXCEEDED_MIN_PULL_DOWN_DISTANCE',
            'NOT_EXCEEDED_MIN_PULL_UP_DISTANCE',
            'EXCEEDED_MIN_PULL_DOWN_DISTANCE',
            'EXCEEDED_MIN_PULL_UP_DISTANCE'
        ]).isRequired,
        styles: PropTypes.object.isRequired,
        topPullingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
        topHoldingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
        topRefreshingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
        bottomPullingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
        bottomHoldingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
        bottomRefreshingPrompt: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
    };

    constructor() {
        super();
        this.state = {
            topArrowDeg: new Animated.Value(0),
            bottomArrowDeg: new Animated.Value(0),
        };
        this.topArrowTransform = [{
            rotate: this.state.topArrowDeg.interpolate({
                inputRange: [0, 1],
                outputRange: ['0deg', '-180deg']
            })
        }];
        this.bottomArrowTransform = [{
            rotate: this.state.bottomArrowDeg.interpolate({
                inputRange: [0, 1],
                outputRange: ['-180deg', '0deg']
            })
        }];
    }

    renderIndicatorIcon() {
        let indicator;
        const { scrollStatus } = this.props;

        if (this.props.refreshing && (scrollStatus === 'EXCEEDED_MIN_PULL_DOWN_DISTANCE' || scrollStatus === 'EXCEEDED_MIN_PULL_UP_DISTANCE')) {
            indicator = <ActivityIndicator size="small" color="gray" style={styles.indicator} />;
        } else if (scrollStatus.indexOf('DOWN') > -1) {
            indicator = (
                <Animated.Image
                    style={[styles.indicator, { transform: this.topArrowTransform }]}
                    resizeMode={'cover'}
                    source={require('../images/arrow_down.png')}    //这里传入了一张“箭头”图片
                />
            );
        } else {
            indicator = (
                <Animated.Image
                    style={[styles.indicator, { transform: this.bottomArrowTransform }]}
                    resizeMode={'cover'}
                    source={require('../images/arrow_down.png')}
                />
            );
        }

        if (scrollStatus === 'EXCEEDED_MIN_PULL_DOWN_DISTANCE') {
            Animated.timing(this.state.topArrowDeg, {
                toValue: 1,
                duration: 100,
                easing: Easing.linear
            }).start();
        } else if (scrollStatus === 'NOT_EXCEEDED_MIN_PULL_DOWN_DISTANCE') {
            Animated.timing(this.state.topArrowDeg, {
                toValue: 0,
                duration: 100,
                easing: Easing.linear
            }).start();
        } else if (scrollStatus === 'EXCEEDED_MIN_PULL_UP_DISTANCE') {
            Animated.timing(this.state.bottomArrowDeg, {
                toValue: 1,
                duration: 100,
                easing: Easing.linear
            }).start();
        } else if (scrollStatus === 'NOT_EXCEEDED_MIN_PULL_UP_DISTANCE') {
            Animated.timing(this.state.bottomArrowDeg, {
                toValue: 0,
                duration: 100,
                easing: Easing.linear
            }).start();
        }

        return indicator;
    }

    renderPrompt() {
        if (this.props.refreshing && this.props.scrollStatus === 'EXCEEDED_MIN_PULL_DOWN_DISTANCE') {
            return this.props.topRefreshingPrompt;
        } else if (this.props.refreshing && this.props.scrollStatus === 'EXCEEDED_MIN_PULL_UP_DISTANCE') {
            return this.props.bottomRefreshingPrompt;
        } else if (this.props.scrollStatus === 'EXCEEDED_MIN_PULL_DOWN_DISTANCE') {
            return this.props.topHoldingPrompt;
        } else if (this.props.scrollStatus === 'EXCEEDED_MIN_PULL_UP_DISTANCE') {
            return this.props.bottomHoldingPrompt;
        } else if (this.props.scrollStatus === 'NOT_EXCEEDED_MIN_PULL_UP_DISTANCE') {
            return this.props.bottomPullingPrompt;
        } else if (this.props.scrollStatus === 'NOT_EXCEEDED_MIN_PULL_DOWN_DISTANCE') {
            return this.props.topPullingPrompt;
        }

        return null;
    }

    render() {
        return (
            <View style={[styles.indicatorContainer, this.props.styles.container]}>
                {this.renderIndicatorIcon()}
                <Text style={[styles.prompt, this.props.styles.prompt]}>{this.renderPrompt()}</Text>
            </View>
        );
    }
}

const styles = StyleSheet.create({
    indicatorContainer: {
        alignItems: 'center',
        flexDirection: 'row',
        height: 54,
        paddingLeft: 125
    },
    indicator: {
        width: 24,
        height: 24,
        marginRight: 15,
    },
    prompt: {
        color: '#6e6e6e',
        fontSize: 14,
    }
});
```



