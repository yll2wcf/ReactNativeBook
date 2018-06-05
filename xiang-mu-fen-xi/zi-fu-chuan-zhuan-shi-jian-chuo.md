JS字符串转时间戳一般直接使用如下方法：

```js
export function getTotalTime(time) {   //time格式为‘2018-01-01 00：00’的字符串
    let date=new Date(time);
    return date.getTime()
}
```

这种写法在IOS上会返回null因为IOS原生就不支持这种转换方式

正确写法：

```js
export function getTotalTime(time) {
    let date=new Date(...getParsedDate(time));
    return date.getTime()
}

function getParsedDate(date){
    date = String(date).split(' ');
    let days = String(date[0]).split('-');
    let hours = String(date[1]).split(':');
    return [parseInt(days[0]), parseInt(days[1])-1, parseInt(days[2]), parseInt(hours[0]), parseInt(hours[1])];
}
```



