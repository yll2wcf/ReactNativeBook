APP内大量使用react-native-simple-toast组件，短时间连续弹出多个体验不好

使用工具类

```js
import SimpleToast from 'react-native-simple-toast';

let lastToastTime=0;

let Toast = {
    show: function (message, duration) {
        let currentTime = new Date().getTime();
        if(currentTime-lastToastTime>2000){
            SimpleToast.show(message, duration);
            lastToastTime=currentTime
        }
    }
};

export default Toast;
```

 调用方式与原组件相同

