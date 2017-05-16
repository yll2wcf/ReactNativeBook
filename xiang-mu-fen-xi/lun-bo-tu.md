#轮播图 

https://github.com/leecade/react-native-swiper


##封装了 Banner
```js
/**
 * Created by yu on 2017/4/27.
 */
import React, {Component, PropTypes} from 'react';

import {
    TouchableOpacity,
    Platform
} from 'react-native';
import theme from '../config/theme';
import Swiper from 'react-native-swiper';
import ResponsiveImage from 'react-native-responsive-image';
export  default class Banner extends Component {
    // 构造
    constructor(props) {
        super(props);
        // 初始状态
        this.images = props.banners.map((banner) => banner.image);
        this.urls = props.banners.map((banner) => banner.url);
    }

    render() {
        let imageViews = this.images.map((image, index) => {
            return (
                <TouchableOpacity
                    activeOpacity={1}
                    style={{flex: 1}}
                    key={'b_image_'+index}
                    onPress={
                        () => {
                            this.props.intent && this.props.intent(index, this.banners);
                            // this.props.banners[index].intent && this.props.banners[index].intent(index);
                        }
                    }
                >
                    {Platform.OS == 'android' ?
                        <ResponsiveImage  /**保证本地图片和网络图片*/
                            style={{flex:1}}
                            source={require("../image/banner.png")}
                            resizeMode="cover"
                            children={<ResponsiveImage  style={{flex:1}} source={typeof(image) == 'string' ? {uri: image} : image}/>}/>
                        :
                        <ResponsiveImage  /**保证本地图片和网络图片*/
                            style={{flex:1}} defaultSource={require("../image/banner.png")} 
                            source={typeof(image) == 'string' ? {uri: image} : image}
                            resizeMode="cover"/>
                    }
                </TouchableOpacity>

            );
        });
        return (
            <Swiper
                bounces={true}
                /**数量为1的时候不显示页码**/
                autoplay={imageViews.length>1}
                showsPagination={imageViews.length>1}
                activeDotColor={theme.red}
                /**继承属性*/
                {...this.props}>
                {imageViews}
            </Swiper>
        )

    }
}
Banner.propTypes = {
    banners: PropTypes.array.isRequired, //banner对象 目前包含image和url
    intent: PropTypes.func, //点击事件 接收index和bannner
    onMomentumScrollEnd: PropTypes.func
};

```


图片会变大变小
react-native-kenburns-view


##处理android 加载显示默认图的问题

```jsx
{Platform.OS == 'android' ?
      <ResponsiveImage  /**保证本地图片和网络图片*/
             style={{flex:1}}
             source={require("../image/banner.png")}
             resizeMode="cover"
             children={<ResponsiveImage  style={{flex:1}} source={typeof(image) == 'string' ? {uri: image} : image}/>}/>
                        :
   <ResponsiveImage  /**保证本地图片和网络图片*/
          style={{flex:1}} 
          defaultSource{require("../image/banner.png")} 
          source={typeof(image) == 'string' ? {uri: image} : image}
          resizeMode="cover"/>
                    }
```

##图片缓存

https://github.com/jayesbe/react-native-cacheable-image