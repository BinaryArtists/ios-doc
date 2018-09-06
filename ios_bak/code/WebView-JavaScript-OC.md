# WebView、JS、OC之间交互

## WebView执行JS代码

stringByEvaluatingJavaScriptFromString:

## JS回调OC

shouldStartLoadWithRequest:

'url' definition

## JavaScriptCore

* JavaScriptCore是webkit的一个重要组成部分，主要是对JS进行解析和提供执行环境。iOS7后苹果在iPhone平台推出，极大的方便了我们对js的操作。我们可以脱离webview直接运行我们的js。iOS7以前我们对JS的操作只有webview里面一个函数stringByEvaluatingJavaScriptFromString，JS对OC的回调都是基于URL的拦截进行的操作。大家用得比较多的是WebViewJavascriptBridge和EasyJSWebView这两个开源库，很多混合都采用的这种方式。

还没使用过，有待挖掘～
