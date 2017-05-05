#网络请求封装

###使用方式

```js
NetUtils.get("gept/api/project/getProjectData",null,(result)=>{
            console.log(result);
        })
```


###NetUtils代码 


```js
import Toast from 'react-native-simple-toast';

const BASE_URL = "http://172.27.103.192:8080/";
import {
    Platform
} from 'react-native';
export default class NetUtils {
    /**
     * 普通的get请求
     * @param {*} url 地址  不以"/"开头
     * @param {*} params  参数 用&拼装 例子: user=1&password=2
     * @param {*} callback  成功后的回调
     */
    static get(url,params, callback) {
        let newUrl=BASE_URL+url;
        //添加公共参数
        var newParams = this.getNewParams(params);//接口自身的规范，可以忽略
        if(newParams!==null){
            newUrl = BASE_URL + url + "?" + newParams;
        }
        console.log(newUrl);
        fetch(newUrl, {
            method: 'GET'
        })
            .then((response) => {
                if (response.ok) {//如果相应码为200
                    return response.json(); //将字符串转换为json对象
                }
            })
            .then((json) => {
                console.log(json);
                //根据接口规范在此判断是否成功，成功后则回调
                if (json.header.code === "1"||json.header.code==="success") {
                    callback(json);
                } else {
                    //否则不正确，则进行消息提示
                    Toast.show(json.header.message, Toast.SHORT);
                }
            }).catch(error => {
                console.log(error);
                Toast.show("netword error", Toast.SHORT);
        });
    };

    /**
     * post key-value 形式 hader为'Content-Type': 'application/x-www-form-urlencoded'
     * @param {*} url
     * @param {*} params
     * @param {*} callback
     */
    static post(url, params, callback) {
        let newUrl=BASE_URL+url;
        console.log(newUrl);
        //添加公共参数
        var newParams = this.getNewParams(params);//接口自身的规范，可以忽略

        fetch(newUrl, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'//key-value形式
            },
            body: newParams
        })
            .then((response) => {
                if (response.ok) {//如果相应码为200
                    return response.json(); //将字符串转换为json对象
                }
            })
            .then((json) => {
                console.log(json);
                //根据接口规范在此判断是否成功，成功后则回调
                if (json.header.code === "1"||json.header.code==="success") {
                    callback(json);
                } else {
                    //否则不正确，则进行消息提示
                    Toast.show(json.header.message, Toast.SHORT);
                }
            }).catch(error => {
            console.log(error);
            Toast.show("netword error", Toast.SHORT);
        });
    };

    /**
     * post json形式  header为'Content-Type': 'application/json'
     * @param {*} url
     * @param {*} jsonObj
     * @param {*} callback
     */
    static postJson(url, jsonObj, callback) {
        let newUrl=BASE_URL+url;
        fetch(newUrl, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json;charset=UTF-8'
            },
            body: JSON.stringify(jsonObj),//json对象转换为string
        })
            .then((response) => {
                if (response.ok) {//如果相应码为200
                    return response.json(); //将字符串转换为json对象
                }
            })
            .then((json) => {
                console.log(json);
                //根据接口规范在此判断是否成功，成功后则回调
                if (json.header.code === "1"||json.header.code==="success") {
                    callback(json);
                } else {
                    //否则不正确，则进行消息提示
                    Toast.show(json.header.message, Toast.SHORT);
                }
            }).catch(error => {
            console.log(error);
            Toast.show("netword error", Toast.SHORT);
        });

    };

    /**
     * 设置公共参数
     * @param {*} oldParams 参数 key-value形式的字符串
     * @return 新的参数
     */
    static getNewParams(oldParams) {
        let device_type=Platform.OS==='android'?"1":"2";
        let userid="";
        let token="";
        if(oldParams!==null){
            newParams = oldParams+"&sysid=emc&device_type="+device_type+"userid="+userid+"token"+token;
        }else{
            newParams="sysid=emc&device_type="+device_type+"userid="+userid+"token="+token;
        }
        return newParams;
    };
}

```