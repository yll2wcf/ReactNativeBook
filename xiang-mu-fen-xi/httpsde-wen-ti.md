## HTTPS 请求证书问题

在react-native中默认是http请求方式，如果在我们项目中需要配置HTTPS请求，翻了翻源码我们可以看到其实react-native中的网络请求用的是OkHttp，react-native 0.55.0版本更新日志上可以看到,Android特性上添加自定义OkHttp客户端功能

    Add back ability to customise OkHttp client

    Summary:
    Prior to 0a71f48, users could customise the OkHttp client used by React Native on Android by calling replaceOkHttpClient in OkHttpClientProvider.

    This functionality has a variety of legitimate applications from changing connection timeouts or pool size to Stetho integration. The challenge is to add back support for replacing the client without causing a breaking change or reintroducing the problems olegbl sought to address in his original commit.

    Introducing a client factory archives these aims, it adds a new, backwards compatible interface and is called each time a client is requested rather than re-using the same instance (unless you explicitly want this behaviour, in which case you could replicate it using a static class property inside your custom factory).

    A number of PRs have been opened to add this functionality: #14675, #14068.

    I don't have a lot of Java experience so I'm open to better/more idiomatic ways to achieve this :)

    Create React Native application and set a custom factory in the constructor, e.g.  `OkHttpClientProvider.setOkHttpClientFactory(new CustomNetworkModule());`

    Where a custom factory would look like:

    class CustomNetworkModule implements OkHttpClientFactory {
        public OkHttpClient createNewNetworkModuleClient() {
            return new OkHttpClient.Builder().build();
        }
    }

##### Android解决方案如下：

0.55.0版本以下可以参考文章：[https://blog.csdn.net/vv\_bug/article/details/77100113](https://blog.csdn.net/vv_bug/article/details/77100113)

下面我们介绍针对0.55.0版本及以上配置，我们首先找到项目中的Application文件，然后在onCreate方法中设置一个自己的OkHttpClientProvider

```java
 @Override
    public void onCreate() {
        super.onCreate();
        OkHttpClientProvider.setOkHttpClientFactory(new CustomNetworkModule(this));
    }
```

```java
public class CustomNetworkModule implements OkHttpClientFactory {
    private Context context;

    public CustomNetworkModule(Context context) {
        this.context = context;
    }

    @Override
    public OkHttpClient createNewNetworkModuleClient() {
        return OkHttpUtils.getInstance(context);
    }
}
```

我们这里自定义一个OkHttpClient,用这个替换react-native中默认的。

```java
public class OkHttpUtils {
    private static OkHttpClient singleton;

    public static OkHttpClient getInstance(Context context) {
        if (singleton == null) {
            synchronized (OkHttpUtils.class) {
                if (singleton == null) {
                    OkHttpClient.Builder builder = new OkHttpClient.Builder();
                    builder.connectTimeout(10, TimeUnit.SECONDS);//设置超时时间为10s。
                    builder.writeTimeout(10, TimeUnit.SECONDS);
                    builder.readTimeout(30, TimeUnit.SECONDS);
                    builder .cookieJar(new ReactCookieJarContainer());

                    //忽略全部证书
                    builder.hostnameVerifier(new HostnameVerifier() {
                        @Override
                        public boolean verify(String s, SSLSession sslSession) {
                            return true;
                        }
                    });
                    HttpsUtils.SSLParams sslParams = HttpsUtils.getSslSocketFactory(null,null, null);
                    builder.sslSocketFactory(sslParams.sSLSocketFactory, sslParams.trustManager);

                    builder.addInterceptor(new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = chain.request();
//                            request.newBuilder()
//                                    .addHeader("Content-Type", "text/json")
//                                    .addHeader("Content-Type", "text/plain")
//                                    .addHeader("Content-Type", "text/html");

                            return response;
                        }
                    });
                    singleton = builder.build();
                }
            }
        }
        return singleton;
    }
}
```

在HttpsUtils类封装了对证书的一些操作，这里根据需要我们信任了全部证书，如果需要真实证书可以在HttpsUtils.getSslSocketFactory\(certificates,bksFile, password\)中设置

```java
    //忽略全部证书
    builder.hostnameVerifier(new HostnameVerifier() {
        @Override
        public boolean verify(String s, SSLSession sslSession) {
            return true;
        }
    });
    HttpsUtils.SSLParams sslParams = HttpsUtils.getSslSocketFactory(null,null, null);
    builder.sslSocketFactory(sslParams.sSLSocketFactory, sslParams.trustManager);
```

HttpsUtils类源码

```java
public class HttpsUtils {
    public static class SSLParams {
        public SSLSocketFactory sSLSocketFactory;
        public X509TrustManager trustManager;
    }

    //获取这个SSLSocketFactory
    public static SSLSocketFactory getSSLSocketFactory() {
        try {
            SSLContext sslContext = SSLContext.getInstance("SSL");
            sslContext.init(null, getTrustManager(), new SecureRandom());
            return sslContext.getSocketFactory();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    //获取TrustManager
    private static TrustManager[] getTrustManager() {
        TrustManager[] trustAllCerts = new TrustManager[]{
                new X509TrustManager() {
                    @Override
                    public void checkClientTrusted(X509Certificate[] chain, String authType) {
                    }

                    @Override
                    public void checkServerTrusted(X509Certificate[] chain, String authType) {
                    }

                    @Override
                    public X509Certificate[] getAcceptedIssuers() {
                        return new X509Certificate[]{};
                    }
                }
        };
        return trustAllCerts;
    }

    public static SSLParams getSslSocketFactory(InputStream[] certificates, InputStream bksFile, String password) {
        SSLParams sslParams = new SSLParams();
        try {
            TrustManager[] trustManagers = prepareTrustManager(certificates);
            KeyManager[] keyManagers = prepareKeyManager(bksFile, password);
            SSLContext sslContext = SSLContext.getInstance("TLS");
            X509TrustManager trustManager = null;
            if (trustManagers != null) {
                trustManager = new MyTrustManager(chooseTrustManager(trustManagers));
            } else {
                trustManager = new UnSafeTrustManager();
            }
            sslContext.init(keyManagers, new TrustManager[]{trustManager}, null);
            sslParams.sSLSocketFactory = sslContext.getSocketFactory();
            sslParams.trustManager = trustManager;
            return sslParams;
        } catch (NoSuchAlgorithmException e) {
            throw new AssertionError(e);
        } catch (KeyManagementException e) {
            throw new AssertionError(e);
        } catch (KeyStoreException e) {
            throw new AssertionError(e);
        }
    }

    private class UnSafeHostnameVerifier implements HostnameVerifier {
        @Override
        public boolean verify(String hostname, SSLSession session) {
            return true;
        }
    }

    private static class UnSafeTrustManager implements X509TrustManager {
        @Override
        public void checkClientTrusted(X509Certificate[] chain, String authType)
                throws CertificateException {
        }

        @Override
        public void checkServerTrusted(X509Certificate[] chain, String authType)
                throws CertificateException {
        }

        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return new java.security.cert.X509Certificate[]{};
        }
    }

    private static TrustManager[] prepareTrustManager(InputStream... certificates) {
        if (certificates == null || certificates.length <= 0) return null;
        try {

            CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
            keyStore.load(null);
            int index = 0;
            for (InputStream certificate : certificates) {
                String certificateAlias = Integer.toString(index++);
                keyStore.setCertificateEntry(certificateAlias, certificateFactory.generateCertificate(certificate));
                try {
                    if (certificate != null)
                        certificate.close();
                } catch (IOException e)

                {
                }
            }
            TrustManagerFactory trustManagerFactory = null;

            trustManagerFactory = TrustManagerFactory.
                    getInstance(TrustManagerFactory.getDefaultAlgorithm());
            trustManagerFactory.init(keyStore);

            TrustManager[] trustManagers = trustManagerFactory.getTrustManagers();

            return trustManagers;
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (CertificateException e) {
            e.printStackTrace();
        } catch (KeyStoreException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;

    }

    private static KeyManager[] prepareKeyManager(InputStream bksFile, String password) {
        try {
            if (bksFile == null || password == null) return null;

            KeyStore clientKeyStore = KeyStore.getInstance("BKS");
            clientKeyStore.load(bksFile, password.toCharArray());
            KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
            keyManagerFactory.init(clientKeyStore, password.toCharArray());
            return keyManagerFactory.getKeyManagers();

        } catch (KeyStoreException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnrecoverableKeyException e) {
            e.printStackTrace();
        } catch (CertificateException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    private static X509TrustManager chooseTrustManager(TrustManager[] trustManagers) {
        for (TrustManager trustManager : trustManagers) {
            if (trustManager instanceof X509TrustManager) {
                return (X509TrustManager) trustManager;
            }
        }
        return null;
    }


    private static class MyTrustManager implements X509TrustManager {
        private X509TrustManager defaultTrustManager;
        private X509TrustManager localTrustManager;

        public MyTrustManager(X509TrustManager localTrustManager) throws NoSuchAlgorithmException, KeyStoreException {
            TrustManagerFactory var4 = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
            var4.init((KeyStore) null);
            defaultTrustManager = chooseTrustManager(var4.getTrustManagers());
            this.localTrustManager = localTrustManager;
        }


        @Override
        public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {

        }

        @Override
        public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
            try {
                defaultTrustManager.checkServerTrusted(chain, authType);
            } catch (CertificateException ce) {
                localTrustManager.checkServerTrusted(chain, authType);
            }
        }


        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return new X509Certificate[0];
        }
    }
}
```



