https://github.com/ascoders/react-native-image-viewer

```
import { Modal } from 'react-native';
import ImageViewer from 'react-native-image-zoom-viewer';

const images = [{
    url: 'https://avatars2.githubusercontent.com/u/7970947?v=3&s=460'
}, {
    url: 'https://avatars2.githubusercontent.com/u/7970947?v=3&s=460'
}, {
    url: 'https://avatars2.githubusercontent.com/u/7970947?v=3&s=460'
}]

export default class App extends React.Component {
    render: function() {
        return (
            <Modal visible={true} transparent={true}>
                <ImageViewer imageUrls={images}/>
            </Modal>
        )
    }
}
```





