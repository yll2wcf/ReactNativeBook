#屏幕适配

前言：从开始接触React Native到现在终于能写出点东西了，的确得为自己好好地点个赞 ，不管咋样，学习还是得继续啊，废话少说了，在rn中我们也需要对屏幕进行适配，但是React Native中的适配貌似比Android原生容易很多（不得佩服facebook那些大神哈，对android原生控件封装的太屌！）。

我们先看看rn中的屏幕适配（作为一个android程序员去做rn确实比iOS程序员考虑的东西多一点点哈，嘻嘻～～）： 
结合android的一些适配经验，我在rn中也封装了一个工具类 
`ScreenUtils.js`：

```js
/**
 * 屏幕工具类
 * ui设计基准,iphone 6
 * width:750
 * height:1334
 */
var ReactNative = require('react-native');
var Dimensions = require('Dimensions');
export var screenW = Dimensions.get('window').width;
export var screenH = Dimensions.get('window').height;
var fontScale = ReactNative.PixelRatio.getFontScale();
export var pixelRatio = ReactNative.PixelRatio.get();
const r2=2;
const w2 = 750/r2;``
const h2 = 1334/r2;
/**
 * 设置text为sp
 * @param size  sp
 * @returns {Number} dp
 */
export const DEFAULT_DENSITY=2;
export function setSpText(size:Number) {
    var scaleWidth = screenW / w2;
    var scaleHeight = screenH / h2;
    var scale = Math.min(scaleWidth, scaleHeight);
    size = Math.round((size * scale + 0.5) * pixelRatio / fontScale);
    return size;
}
/**
 * 屏幕适配,缩放size
 * @param size
 * @returns {Number}
 * @constructor
 */
export function scaleSize(size:Number) {
    var scaleWidth = screenW / w2;
    var scaleHeight = screenH / h2;
    var scale = Math.min(scaleWidth, scaleHeight);
    size = Math.round((size * scale + 0.5));
    return size/DEFAULT_DENSITY;
}
```

搞过rn的童鞋知道，rn中直接写宽高都是dp的，所以我们要以一个美工设计的ui基准来计算我们的宽高，数学不好哈，不过大概是这样的： 
我们先定义好ui的设计基准，然后获取到我们自己手机的屏幕宽高，生成一个百分比，然后算出在iphone6上的100px，在我们手机上是多少px，最后转换成dp设置在在我们布局的style中：
```js
const styles = StyleSheet.create({
    container: {
        backgroundColor: 'white',
        justifyContent: 'space-between',
        flexDirection: 'row',
        paddingTop: ScreenUtils.scaleSize(22),
        paddingBottom: ScreenUtils.scaleSize(22),
        paddingRight: ScreenUtils.scaleSize(12),
        paddingLeft: ScreenUtils.scaleSize(12),
        alignItems: 'center'
    },
});
```

注: RN中默认1个px单位 等于实际图的2px