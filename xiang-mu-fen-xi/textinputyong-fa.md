TextInput适配问题：

TextInput在android端默认有一个padding值，有下划线，默认在高度里居中，而iOS没有padding值，必须要设定高度，默认从左上开始且不支持textAlignVertical这个属性，所有在适配两端时需要：

```
<View style={{ flex: 1, flexDirection: 'row', alignItems: 'center' }}>  //外层嵌套View使子组件垂直居中
     <TextInput style={styles.search_input}
         multiline={false}                                            //是否支持多行
         underlineColorAndroid={'transparent'}                         //取消下划线
         textAlign='left'                                              //字体左对齐（默认值也是left），水平方向
         placeholder='请输入您的手机号码'                               //输入提示
         keyboardType='phone-pad'                                     //弹出的输入键盘类型，这里为数字
         onChangeText={(phone) => this.setState({ phone })} />        //监听输入改变
</View>

search_input: {
        flex: 1,
        padding: px2dp(0),                                      //取消android默认padding
        height: px2dp(60),                                      //iOS必须有高度
        fontSize: px2dp(24),
        color: theme.darkColor,
}
```

另外在一个页面出现多个textInput时，大多数情况下我们都希望用户可以通过点击软键盘的下一项来跳转到下一个输入框上（Android源生默认就这样），组件的属性值：returnKeyLabel可以改变软键盘右下角的样式（默认为回车），但是这个属性并不能实现逻辑，我们还需要给组件定义一些属性：

```
<View style={styles.item_container}>
                <Text style={styles.title}>企业名称</Text>
                <View style={{ flex: 1 }}>
                    <TextInput style={styles.search_input}
                        multiline={false}
                        underlineColorAndroid={'transparent'}
                        returnKeyLabel='next'         //改变右下角按键样式
                        blurOnSubmit={false}          //提交后不取消焦点，否则会出现点击下一个软键盘关闭再打开的情况
                        onChangeText={(name) => this.setState({ name })}
                        onSubmitEditing={(event) => {     //提交输入的事件，让下一个（这里是andressInput）获得焦点（.focus（））
                            this.addressInput.focus();
                        }} />
                    {this.state.nameError ? <Text style={{ color: 'red', fontSize: px2dp(18) }}>企业名称不能为空</Text> : null}
                </View>
            </View>
            <View style={styles.item_container}>
                <Text style={styles.title}>企业地址</Text>
                <View style={{ flex: 1 }}>
                    <TextInput style={styles.search_input}
                        ref={(view) => this.addressInput = view}   //设定ref，类似于给这个textInput添加iD为addressInput
                        multiline={false}
                        underlineColorAndroid={'transparent'}
                        returnKeyLabel='next'
                        blurOnSubmit={false}
                        onSubmitEditing={(event) => {
                            this.personInput.focus();
                        }}
                        onChangeText={(address) => this.setState({ address })} />
                    {this.state.addressError ? <Text style={{ color: 'red', fontSize: px2dp(18) }}>企业地址不能为空</Text> : null}
            </View>
</View>
```





