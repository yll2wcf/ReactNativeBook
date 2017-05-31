# 轮播图

[https://github.com/leecade/react-native-swiper](https://github.com/leecade/react-native-swiper)

封装了 Banner

图片会变大变小  
react-native-kenburns-view

## 处理android 加载显示默认图的问题

```js
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

## 图片缓存

[https://github.com/jayesbe/react-native-cacheable-image](https://github.com/jayesbe/react-native-cacheable-image)

swiper在用于flatlist的header中（或TabNavigation等情况）会出现不渲染的情况（可以看见指示的原点，看不见图片），暂时的解决方法为：

https://github.com/react-community/react-navigation/issues/1572

```
constructor(props) {
   super(props);
   this.state = {
      visibleSwiper: false
   };
}

componentDidMount() {
   setTimeout(() => {
      this.setState({
        visibleSwiper: true
      });
   }, 100);
}

render() {
   let swiper = null;
   if (this.state.visibleSwiper) {
      swiper = <Swiper dotColor={'white'} 
         activeDotColor={'#FF0A0A'} 
         height={250} horizontal={true} 
         loop={false} bounces={true} 
         removeClippedSubviews={false}
      >
         <View style={styles.slide1}>
            <Image resizeMode='cover' style={styles.image} source={require('../img/1.jpg')} />
         </View>
         <View style={styles.slide1}>
            <Image resizeMode='cover' style={styles.image} source={require('../img/2.jpg')} />
         </View>
      </Swiper>;
   } else {
      swiper = <View></View>;
   }

   return (
      ...
      {swiper}
      ...
   );
}
```

给swiper的所有父组件添加属性：

```
removeClippedSubviews={false}
```



