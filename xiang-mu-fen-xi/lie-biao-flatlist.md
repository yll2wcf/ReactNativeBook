## 列表FlatList

比以前的list组件更高效（尤其是大量数据的时候）

当某行滑出渲染区域之外后，其内部状态将不会保留。请确保你在行组件以外的地方保留了数据。

滑动特别快会出现短暂空白

默认情况下每行都需要提供一个不重复的key属性。你也可以提供一个keyExtractor函数来生成key。

#### keyExtractor:`(item: ItemT, index: number) => string`

此函数用于为给定的item生成一个不重复的key。Key的作用是使React能够区分同类元素的不同个体，以便在刷新时能够确定其变化的位置，减少重新渲染的开销。若不指定此函数，则默认抽取`item.key`作为key值。若`item.key`也不存在，则使用数组下标。

```
//Flatlist数据源为普通数组（没有datasource了），没有key报警告需要用keyExtractor函数，这里用index作key
<FlatList
                            data={this.state.listdata}
                            renderItem={this._renderItemComponent}
                            keyExtractor={(item,index) => index} />

_renderItemComponent = ({item,index}) => {
        return (
            <TouchableOpacity style={styles.record_item} activeOpacity={theme.btnActiveOpacity} onPress={this._ItemCallback.bind(this, index)}>
                <View style={{ flexDirection: 'row' }}>
                    <Text style={{ flex: 1, color: "gray", fontSize: theme.text.fontSize }}>提交日期：{item.date}</Text>
                    <Text style={{ color: "black", fontSize: theme.text.fontSize }}>{item.state}</Text>
                </View>
                <Text style={{ color: "black", fontSize: theme.text.fontSize }} numberOfLines={2}>{item.content}</Text>
            </TouchableOpacity>
        );
    };

_ItemCallback(index) {
        Alert.alert(
            'Message',
            "The function " + index + " currently isn't available for android",
            [{ text: 'OK', onPress: () => { } }]
        );
    }
```




