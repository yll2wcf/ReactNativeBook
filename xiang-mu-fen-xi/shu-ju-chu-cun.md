AsyncStorage是一个简单的、异步的、持久化的Key-Value存储系统，它对于App来说是全局性的。它用来代替LocalStorage。

我们推荐您在AsyncStorage的基础上做一层抽象封装，而不是直接使用AsyncStorage。

**译注**：推荐由`React Native中文网`封装维护的[`react-native-storage`](https://github.com/sunnylqm/react-native-storage/blob/master/README-CHN.md)模块，提供了较多便利功能。

基本用法：

```
//必须在使用storage前注册，建议放在入口文件里，例如index.android.js
import Storage from 'react-native-storage';

var storage = new Storage({
    // 最大容量，默认值1000条数据循环存储
    size: 1000,

    // 存储引擎：对于RN使用AsyncStorage，对于web使用window.localStorage
    // 如果不指定则数据只会保存在内存中，重启后即丢失
    storageBackend: AsyncStorage,

    // 数据过期时间，默认一整天（1000 * 3600 * 24 毫秒），设为null则永不过期
    defaultExpires: null,

    // 读写时在内存中缓存数据。默认启用。
    enableCache: true,
})

global.storage = storage;   //全局变量
```

储存：

```
storage.save({
                    key: 'loginState',  // 注意:请不要在key中使用_下划线符号!
                    rawData: {
                        userid: userid,
                        token: token
                    }
                });
```

读取：

```
storage.load({
            key: 'loginState',
        }).then(ret => {
            // 如果找到数据，则在then方法中返回
            // 注意：这是异步返回的结果（不了解异步请自行搜索学习）
            // 你只能在then这个方法内继续处理ret数据
            // 而不能在then以外处理
            // 也没有办法“变成”同步返回
            // 你也可以使用“看似”同步的async/await语法
            this.setState({
                logined: true,
                username: ret.loginName,
                imageurl: ret.imageUrl
            });
        }).catch(err => {
            //如果没有找到数据且没有sync方法，
            //或者有其他异常，则在catch中返回
            console.log('未登录')
            this.setState({
                logined: false
            });
        })
```

修改：

```
//暂时未提供update的方法，只能读出来再存
storage.load({
            key: 'loginState',
        }).then(ret => {
            ret.bindMail = mail;
            storage.save({
                key: 'loginState',  // 注意:请不要在key中使用_下划线符号!
                rawData: ret
            })
        }).catch(err => {
            //如果没有找到数据且没有sync方法，
            //或者有其他异常，则在catch中返回
            console.log(err);
            Toast.show('获取个人信息失败，请重新登录', Toast.SHORT);
        })
```

删除：

```
storage.remove({
            key: 'loginState'
        });
```



