#图片选择器

https://github.com/react-community/react-native-image-picker

记得配置权限

```js
        ImagePicker.showImagePicker(options, (response) => {
                console.log('Response = ', response);

                if (response.didCancel) {
                    console.log('User cancelled image picker');
                }
                else if (response.error) {
                    console.log('ImagePicker Error: ', response.error);
                }
                else {
                    let source = response.uri;
                    let name = response.fileName + '.jpg';
                  
                    ／／上传相关
                    // You can also display the image using data:
                    // let source = { uri: 'data:image/jpeg;base64,' + response.data };

                    // this.setState({
                    //     portrait: source
                    // });
                }
            });
```