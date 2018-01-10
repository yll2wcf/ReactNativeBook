RN新版本 Image组件内不能再嵌套其他组件，所以要把Image作为背景需要使用ImageBackground组件。

ImageBackground组件属性同Image，需要注意的是style与imageStyle两个属性，style为外部组件view的属性，imageStyle才是图片的属性。

源码：

```
render() {
    const {children, style, imageStyle, imageRef, ...props} = this.props;

    return (
      <View style={style} ref={this._captureRef}>
        <Image
          {...props}
          style={[
            StyleSheet.absoluteFill,
            {
              // Temporary Workaround:
              // Current (imperfect yet) implementation of <Image> overwrites width and height styles
              // (which is not quite correct), and these styles conflict with explicitly set styles
              // of <ImageBackground> and with our internal layout model here.
              // So, we have to proxy/reapply these styles explicitly for actual <Image> component.
              // This workaround should be removed after implementing proper support of
              // intrinsic content size of the <Image>.
              width: style.width,
              height: style.height,
            },
            imageStyle,
          ]}
          ref={imageRef}
        />
        {children}
      </View>
    );
  }
```

官方解释为当前内部image属性被外部view的height，width属性覆盖，可能会导致大小并不准确，未来会修复这一问题（当前版本RN 5.1\)

