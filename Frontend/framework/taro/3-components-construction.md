# 组件

## 框架组成

https://nervjs.github.io/taro/docs/composition.html

重要切入点：
1. @tarojs/taro	taro 运行时框架
2. @tarojs/taro-alipay	taro 支付宝小程序运行时框架
3. @tarojs/redux	taro 小程序 Redux 支持
4. @tarojs/transformer-wx	taro 小程序转换器
5. @tarojs/taroize	taro 小程序编译器

次要关注点

@tarojs/plugin-babel	taro babel 编译插件
@tarojs/plugin-sass	taro sass 编译插件
@tarojs/plugin-less	taro less 编译插件
@tarojs/plugin-stylus	taro stylus 编译插件
@tarojs/plugin-csso	taro css 压缩插件
@tarojs/plugin-uglifyjs	taro js 压缩插件
eslint-config-taro	taro eslint 规则
eslint-plugin-taro	taro eslint 插件

## 开发工具

### @tarojs/cli

主要负责项目初始化、编译、构建等

> 安装

```
npm install -g @tarojs/cli
```

> 与 Taro 工程的关系

Taro工程的目录结构：[这里](https://github.com/fallending/venom/tree/master/packages)

Taro 项目主要是由一系列 NPM 包组成，位于工程的 Packages 目录下。它的包管理方式和 Babel 项目一样，将整个项目作为一个 monorepo 来进行管理，并且同样使用了包管理工具 [Lerna](vendor/lerna.md)

Packages 目录下十几个包中，最常用的项目初始化与构建的命令行工具 Taro CLI 就是其中一个。在 Taro 工程根目录运行 lerna publish 命令之后， lerna.json 里面配置好的所有的包会被发布到 NPM 上

> cli 开发目录



## 开发组件


### @tarojs/taro

### @tarojs/components

### @tarojs/async-await

https://nervjs.github.io/taro/docs/async-await.html

他做了什么？

### redux/mobx

https://nervjs.github.io/taro/docs/redux.html
https://nervjs.github.io/taro/docs/mobx.html

他做了什么？
