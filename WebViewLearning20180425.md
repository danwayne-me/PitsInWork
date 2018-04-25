###WebView 学习

**关键词:**

1. WebViewClient：处理网页加载过程中的相关通知和回调，如onPageStarted, onPageFinished等
2. WebChromeClient：处理网页和JS之间更细节的交互，以及获取网页Icon，标题等资源
3. DownloadListener：当用户点击网页触发下载之后的回调
4. WebView生命周期：
    1. onResume：正常响应网页交互
    2. onPause：暂停内核关于该WebView的所有操作，例如DOM解析，JS执行等
    3. onPauseTimer：全局暂停WebView内核，该方法应该在Application处于onPause时调用
    4. onResumeTimer：全局恢复WebView内核操作
    5. 销毁WebView：如果当前WebView中包含视频或音乐，且在Activity被销毁的时候播放没有停止的话，需要显示销毁WebView
        1. 从Activity的layout tree中调用removeView解除WebView和Activity的绑定
        2. 调用WebView的onDestroy
5. WebView Navigation：当WebView中包含页面间的跳转的时候，可以在WebView回调中处理跳转
    1. canGoBack和canGoForward用来标记该WebView中包含页面是否可以前进或者后退
    2. goBack和goForward执行具体的navigation操作
    3. 通常在Android H5页面中，需要对返回键处理逻辑进行修改，在无法返回的情况下再退出Activity(默认是直接退出)
6. WebView清除数据的操作(缓存，历史记录等等)均针对全局(static方法)
7. WebSettings：针对WebView进行一系列的配置
    1. setDomStorageEnabled：设置是否支持DOM Storage API (DOM Storage / Web Storage是一种<K, V>形式的本地缓存)
    2. setDatabaseStorageEnabled：设置是否支持Web SQL Database API (以上两种都属于H5规范)
    3. setAppCacheEnabled：Application Cache，用来缓存页面URL及页面内容的缓存策略
    4. WebView缓存加载策略
        1. LOAD_CACHE_ONLY：只加载缓存数据
        2. LOAD_DEFAULT ：根据HTTP头部cache-control来决定缓存使用策略
        3. LOAD_NO_CACHE：不适用缓存
        4. LOAD_CACHE_ELSE_NETWORK：只要缓存数据存在，不论是否过期，忽略cache-control直接用缓存
8. Android与JS交互
    1. Android调用JS方法(需要在WebViewClient.onPageFinished之后调用，否则无法生效)
        1. webview.loadUrl：会触发页面刷新，效率较低
        2. webview.evaluateJavascript：不会触发页面刷新，效率较高，但是只支持4.4及以上的版本
    2. JS调用Android方法
        1. webview.addJavascriptInterface：接入简单，但是在安卓4.2之前存在安全漏洞
        2. webviewClient.shouldOverrideUrlLoading：自定义schema进行判断，在获取Android方法返回值的时候只能通过webview.loadUrl的方式再将返回值回传到js，比较麻烦
        3. webchromeClient.onJsAlert等方法：接入方法同上，相对复杂且回传值比较麻烦
9. WebView Cookie管理
    1. CookieSyncManager已经deprecated，直接使用CookieManager进行Cookie管理
    2. 在登录成功之后，在CookieManager中setCookie记录session_id或者token
    3. 如果需要强制同步cookie，调用CookieManager.flush

[Reference Overview](https://www.jianshu.com/p/3c94ae673e2a)  
[Reference JS Interact](https://www.jianshu.com/p/345f4d8a5cfa)  
[Reference Cookie](https://www.jianshu.com/p/c9a9c4e1756d)  
[Reference Security](https://www.jianshu.com/p/3a345d27cd42)


###X5采坑

**要点:**

1. 如果涉及H5视频播放需求，注意configChanges配置，PixelFormat配置，windowIsTranslucent风格配置，同时禁止调用setLayerType， setDrawingCacheEnabled
2. 位置windowSoftInputMode以适配H5中的输入法弹出需求
3. Cookie相关设置一定要等X5内核初始化完成之后再进行，否则只能针对系统内核生效
4. X5内核不支持64位手机，只能在gradle中配置手机为32位运行(是否会和其他so文件冲突？)
5. 代码混淆时候不能再混淆TBS jar
6. 建议封装BaseWebView减少代码维护成本

[Reference here](https://blog.csdn.net/niubitianping/article/details/70919385)
