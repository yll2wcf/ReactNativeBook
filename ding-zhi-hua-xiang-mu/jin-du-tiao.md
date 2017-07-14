#进度条

错误解决

http://www.zhwios.cn/2016/07/28/reactnative-no-component-found-for-view-with-name-artsurfaceview/

```jsx
import * as Progress from 'react-native-progress';

{
    this.state.showProgress ?
         <View style={{
               position: "absolute",
               left: 0.46 * theme.screenWidth,
               top: 0.45 * theme.screenHeight
          }}>
               <Progress.CircleSnail style={{marginTop: px2dp(10)}}
                                       color={['red', 'green', 'blue']}/>
          </View>
              :
              null
      }
```