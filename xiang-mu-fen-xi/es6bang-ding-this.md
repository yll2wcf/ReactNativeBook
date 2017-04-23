#es6绑定this
在使用React中 如果使用ES6的`Class extends`写法 如果onClick绑定一个方法 需要bind(this), 
而使用`React.createClass`方法 就不需要. 
请问这是为什么呢

解释:`React.createClass` 是es5的写法默认是绑定了bind方法，而es6中 新增加了class，绑定的方法需要绑定this，如果是箭头函数就不需要绑定this，用箭头的方式

```js
第一种写法:
_handleClick(e) {
    console.log(this);
}
render() {
    return (
        <div>
            <h1 onClick={this._handleClick.bind(this)}>点击</h1>
        </div>
    );
}
```

```js
第二种写法：
constructor(props) {
    super(props);
    this._handleClick = this._handleClick.bind(this)
}
_handleClick(e) {
    console.log(this);
}
render() {
    return (
        <div>
            <h1 onClick={this._handleClick}>点击</h1>
        </div>
    );
}
```

```js
第三种写法：
_handleClick = (e) => {
    // 使用箭头函数(arrow function)
    console.log(this);
}
render() {
    return (
        <div>
            <h1 onClick={this._handleClick}>点击</h1>
        </div>
    );
}
```