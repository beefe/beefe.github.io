---
layout: post
title: Android WebView相关的一些笔记
author: shexiaoheng
categories: tech_note
tag: Android
---

当Android项目使用WebView去加载网页的时候，很多时候要与JS进行一些交互，所以就会遇到的一些问题，
比如alert在WebView中弹不出来，比如JS调用Native方法报错，假如你也恰好遇到了这些问题，不妨看下我的解决方法，
也许正好能帮上你的忙。

<!-- more -->

### 首先Android使用WebView最基本的就是

```Java
webView.loadUrl(url);
```

### 为了让用户浏览网页完全在WebView中进行，我们需要增加如下代码

##### 不增加的后果就是当用户点击一个跳转链接时，会调用系统浏览器从而跳出你的程序


```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
        return true;
    }
});
```

### 让WebView支持JS

##### 这个不用解释了吧，不打开JS支持怎么与JS交互呢 (- -#)

```java
webView.getSettings().setJavaScriptEnabled(true);
```

### 让WebView支持JS的alert

```java
webView.setWebChromeClient(new WebChromeClient());
```

为什么加上一个WebChromeClient就支持alert了呢？因为WebChromeClient里有这个方法

```java
@Override
public boolean onJsAlert(WebView view, String url, String message, JsResult result) {
    return super.onJsAlert(view, url, message, result);
}
```

### 给JS提供一个Native方法

```java
webView.addJavascriptInterface(new JSCallbackInterface(), "MyJSBridge");
```

```java
private class JSCallbackInterface {
    @JavascriptInterface
    public void myMethod() {
    	// native do something
    }
}
```

##### JS调用Native方法可以这样调用

```javascript
if(window.MyJSBridge){
    window.MyJSBridge.myMethod();
}
```

当然，"Bridge"叫什么名字,Native Method叫什么名字，需要什么参数，你们自己来定，但是一定要让JS和Android Native的名字参数保持一致


### 当JS调用Native方法报"NPObject deleted"错误时，解决方案如下

```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onPageFinished(WebView view, String url) {
        super.onPageFinished(view, url);
        view.addJavascriptInterface(new JSCallbackInterface(), "StagingPayBridge");
    }
});
```

"NPObject deleted"错误，根据字面来理解应该是Native Object被删除了，这种情况出现在Web页面点击一个链接跳转后调用Android Native方法出现，每次Web页面跳转都会调用"onPageStarted"和"onPageFinished"方法，我们只需在"onPageFinished"方法中再次把Android Native Object添加即可


### 让JS支持https

##### WebView加载一些Web页加载不出来，比如支付宝的支付网页，因为是https (≧﹏ ≦) 

```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
        handler.proceed();
    }
});
```

## 好了，我们把上面的代码整理下

```java
...
private void setWebView(String url){

    webView.loadUrl(url);

    webView.getSettings().setJavaScriptEnabled(true);

    webView.addJavascriptInterface(new JSCallbackInterface(), "MyJSBridge");
    
    webView.setWebChromeClient(new WebChromeClient());

    webView.setWebViewClient(new WebViewClient() {
    
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, String url) {
            view.loadUrl(url);
            return true;
        }
    
        @Override
        public void onPageFinished(WebView view, String url) {
            super.onPageFinished(view, url);
            view.addJavascriptInterface(new JSCallbackInterface(), "StagingPayBridge");
        }

        @Override
        public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
            handler.proceed();
        }
    });
}
...
```

```java
...
private class JSCallbackInterface {
    @JavascriptInterface
    public void myMethod() {
        // native do something
    }
}
...
```

以上就是我今天开发中遇到的一些关于Android WebView的问题，分享给大家。 ^ ~ ^ 
