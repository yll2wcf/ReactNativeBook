#fetch超时时间

http://blog.csdn.net/vv_bug/article/details/61920337


修改源码
```js
    self.fetch = function (input, init) {
        return new Promise(function (resolve, reject) {
            var request = new Request(input, init)
            var xhr = new XMLHttpRequest()

            xhr.onload = function () {
                var options = {
                    status: xhr.status,
                    statusText: xhr.statusText,
                    headers: parseHeaders(xhr.getAllResponseHeaders() || '')
                }
                options.url = 'responseURL' in xhr ? xhr.responseURL : options.headers.get('X-Request-URL')
                var body = 'response' in xhr ? xhr.response : xhr.responseText
                resolve(new Response(body, options))
            }

            xhr.onerror = function () {
                reject(new TypeError('Network request failed'))
            }

            xhr.ontimeout = function () {
                reject(new TypeError('Network request failed'))
            }

            xhr.open(request.method, request.url, true)

            if (request.credentials === 'include') {
                xhr.withCredentials = true
            }

            if ('responseType' in xhr && support.blob) {
                xhr.responseType = 'blob'
            }

            request.headers.forEach(function (value, name) {
                xhr.setRequestHeader(name, value)
            })
            //我们只需要加上下面这段代码即可
            if (init != null && init.timeout != null) {
                xhr.timeout = init.timeout;
            }
            xhr.send(typeof request._bodyInit === 'undefined' ? null : request._bodyInit)
        })
    }
    self.fetch.polyfill = true
})(typeof self !== 'undefined' ? self : this);

```

改配置
```js
...
            let response = await fetch(newUrl, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8'//key-value形式
                },
                body: params,
                timeout:5000
            });
...
```