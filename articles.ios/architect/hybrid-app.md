## 混合架构

参考链接：
  1. [构建混合移动应用的流行框架优缺点对比](http://www.open-open.com/news/view/1de00ba)，提到了：Ionic、Onsen UI、AngularJS、Framework 7、React Native、jQuery Mobile、Native Script、Famous


### 准备工作

1. [Node.js是用来做什么的？](http://www.zhihu.com/question/33578075)中

再来看一个：NW.js 乍一看大家都不知道这东西是干嘛使得，但是如果你知道它原来叫做：node-webkit，可能就应该想起来了。我们都知道像chrome和safari的内核都是webkit，而node-webkit 相当于一个自定义的webkit内核浏览器中执行服务器端代码 node js。NW.js多用于跨平台开发，也就是Hybird App，可以用它配合ionic使用。

* 安装：[Node.js官网](http://nodejs.cn)

```
Node.js was installed at
   /usr/local/bin/node

npm was installed at
   /usr/local/bin/npm

Make sure that /usr/local/bin is in your $PATH.
```

[安装过成可参考](http://jingyan.baidu.com/article/a948d6515d4c850a2dcd2e18.html)

2. [npm](https://docs.npmjs.com)，是Node.js的包管理器，node package manager

装了node-v4.4.2.pkg就可以使用npm了

```

npm install webpack --save-dev

npm install html-webpack-plugin --save-dev

npm install html-loader --save-dev

```

3. cordova

[基于第一个PhoneGap(cordova)的应用详解](http://www.jb51.net/html5/80728.html)

  * Cordova是贡献给Apache后的开源项目，是从PhoneGap中抽出的核心代码，是驱动PhoneGap的核心引擎。你可以把它们的关系想象成类似于Webkit和Google Chrome的关系

  * 例子：[BelinChung/HiApp](https://github.com/BelinChung/HiApp)
  ```
  Requirements

    cordova ^5.0.0
    framework7 ^1.4.0

  Dependencies
    HiApp use npm to manage third-party packages now.
    Then install all dependencies, in repo's root:

    $ npm install

  PhoneGap App Guides
    Install the cordova module using npm utility of Node.js.

    $ npm install -g cordova

  Create App
    Go to the directory where you maintain your source code, and run a command such as the following:

    $ cordova create hiapp com.hiapp.hiapp HiApp

  Check out source code
    Because the PhoneGap app directory should not already exist, so check out the HiApp source code in this step.

    $ cd hiapp  
    $ git init   
    $ git remote add origin git@github.com:BelinChung/HiApp.git  
    $ git pull origin master  
    $ git reset --hard origin/master  

  Add Platforms
    Before you can build the project, you need to specify a set of target platforms.

    $ cordova platform add ios

  Add Plugins
    You need to add plugins that provide access to core Cordova APIs.

    $ cordova plugin add cordova-plugin-whitelist cordova-plugin-camera cordova-plugin-geolocation cordova-plugin-file-transfer cordova-plugin-inappbrowser cordova-plugin-network-information
  Build the App
    Run the following command to iteratively build the project:

    $ cordova build ios

  Test the App on an iOS Device with Xcode
    Double-click to open the platforms/ios/HiApp.xcodeproj file
    Press the Run button to deploy the application in the emulator

  Web App Preview
    HiApp use webpack browser sync server to develop, Just run it in repo's root:

    $ npm run dev

    WebApp will be available on http://localhost:3000/

  Web App Release / PhoneGap App Release

    $ npm run build

    The result is available in www/ folder.
  ```

4. Ionic

[使用Ionic + Apache Cordova开发跨平台混合型的移动应用](http://blog.csdn.net/zoutongyuan/article/details/41910903?utm_source=tuicool)

5. webpack

[WebPack 简明学习教程](http://www.jianshu.com/p/b95bbcfc590d)

[详解前端模块化工具-Webpack](https://segmentfault.com/a/1190000003970448)

6. Angular.js

[AngularJS中文](http://www.apjs.net)

7. Backbone.js


### 框架

1. [Framework7](http://www.f7cn.com/docs/custom-build.html#.VxLkFDZq2Oo)

[Framework7-github](https://github.com/nolimits4web/Framework7)

  * 安装
    通过bower安装
    ```
    bower install framework7
    ```

    如果要安装bower
    ```
    npm install -g bower
    ```

    源码安装
    ```
    npm install --global gulp

    npm install

    gulp build
    ```

    出现的错误：
    * [安装Zepto出错提示 license should be a valid SPDX license expression?](https://segmentfault.com/q/1010000003040821)，在dependencies前面加上了："license": "BSD-2-Clause",

  * 有关于framework7和codorva有个demo可以学习：[BelinChung/HiApp](https://github.com/BelinChung/HiApp)
  
2.
