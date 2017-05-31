```
'use strict';

//验证手机号11位1开头
export function checkMobile(str) {
    var re = /^1\d{10}$/
    if (re.test(str)) {
        return true;
    } else {
        return false;
    }
}

//验证电话
export function checkPhone(str) {
    var re = /^\d{3,4}-?\d{7,8}$/;
    if (re.test(str)) {
        return true;
    } else {
        return false;
    }
}

//验证邮箱
export function checkEmail(str) {
    var re = /^(\w-*\.*)+@(\w-?)+(\.\w{2,})+$/
    if (re.test(str)) {
        return true;
    } else {
        return false;
    }
}

export function parseTime(time) {
    var now = new Date(time);
    var year = now.getFullYear();
    var month = now.getMonth() + 1;
    var date = now.getDate();
    var hour = now.getHours();
    var minute = now.getMinutes();
    var second = now.getSeconds();
    return year + "-" + month + "-" + date + " " + hour + ":" + minute + ":" + second;
}
```



