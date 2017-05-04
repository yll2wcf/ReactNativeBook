#轮播图 

https://github.com/leecade/react-native-swiper


封装了 Banner

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