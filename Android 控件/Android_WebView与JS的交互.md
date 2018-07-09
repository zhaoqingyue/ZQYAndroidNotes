### Android WebView 与 JS 的交互

----WebView是Android 与 JS沟通的桥梁。

**1 交互方式总结**

Android与JS通过WebView互相调用方法，实际上是：

- Android去调用JS的代码

- JS去调用Android的代码

**a. Android调用JS代码的方法有2种：**

- 通过WebView的loadUrl（）

- 通过WebView的evaluateJavascript（）

**b. JS调用Android代码的方法有3种：**

- 通过WebView的addJavascriptInterface（）进行对象映射

- 通过 WebViewClient 的shouldOverrideUrlLoading ()方法回调拦截 url

- 通过 WebChromeClient的onJsAlert()、onJsConfirm()、onJsPrompt（）方法回调拦截JS对话框alert()、confirm()、prompt（）消息。
- 

**2. 具体分析**