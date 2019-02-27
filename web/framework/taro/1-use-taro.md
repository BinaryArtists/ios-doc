# Taro

Taro 是一套遵循 React 语法规范的 多端开发 解决方案

## 用法

### 安装

```
$ npm install -g @tarojs/cli
$ yarn global add @tarojs/cli
```

### 创建

```
$ taro init myApp
```

npm 5.2+ 也可在不全局安装的情况下使用 npx 创建模板项目

```
$ npx @tarojs/cli init myApp
```

### 打包

*[微信小程序](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)*

```
# npm script
$ npm run dev:weapp
$ npm run build:weapp
# 仅限全局安装
$ taro build --type weapp --watch
$ taro build --type weapp
# npx 用户也可以使用
# 去掉 --watch 将不会监听文件修改，并会对代码进行压缩打包
$ npx taro build --type weapp --watch
$ npx taro build --type weapp
```

*[百度小程序](https://smartprogram.baidu.com/docs/develop/devtools/show_sur/)*

```
# npm script
$ npm run dev:swan
$ npm run build:swan
# 仅限全局安装
$ taro build --type swan --watch
$ taro build --type swan
# npx 用户也可以使用
$ npx taro build --type swan --watch
$ npx taro build --type swan
```

*[支付宝小程序](https://docs.alipay.com/mini/developer/getting-started/)*

```
# npm script
$ npm run dev:alipay
$ npm run build:alipay
# 仅限全局安装
$ taro build --type alipay --watch
$ taro build --type alipay
# npx 用户也可以使用
$ npx taro build --type alipay --watch
$ npx taro build --type alipay
```

*[字节跳动小程序](https://microapp.bytedance.com/docs/devtool/versionUpdate.html)*

```
# npm script
$ npm run dev:tt
$ npm run build:tt
# 仅限全局安装
$ taro build --type tt --watch
$ taro build --type tt
# npx 用户也可以使用
$ npx taro build --type tt --watch
$ npx taro build --type tt
```

*H5*

```
# Preview

# npm script
$ npm run dev:h5
# 仅限全局安装
$ taro build --type h5 --watch
# npx 用户也可以使用
$ npx taro build --type h5 --watch

# Build

# npm script
$ npm run build:h5
# 仅限全局安装
$ taro build --type h5
# npx 用户也可以使用
$ npx taro build --type h5
```

*[React Native](https://nervjs.github.io/taro/docs/react-native.html)*

```
# npm script
$ npm run dev:rn
# 仅限全局安装
$ taro build --type rn --watch
# npx 用户也可以使用
$ npx taro build --type rn --watch
```

### 命令

```
# taro
$ taro update self
# npm
npm i -g @tarojs/cli@latest
# yarn
yarn global add @tarojs/cli@latest

# 更新项目中 Taro 相关的依赖
$ taro update project
# 检测 Taro 环境及依赖的版本等信息
$ taro info
```

## [规范](https://nervjs.github.io/taro/docs/spec-for-taro.html)

请查看原文档

## [例程](https://github.com/fallending/taro-yanxuan)

请查看原文档

## 有限资源

* [官方网站](https://taro.aotu.io/)

## 人员架构

> [为何引用这部分内容？](/refs/arch/1-cost-of-architect-spliting.md)

* 督导小组

负责核心功能的监督把控，整体功能的规划设计，督导新功能的引入，制定开发计划，以及提交代码 Review

* 编译小组

负责代码文件编译到不同端的实现，微信小程序端 JSX 编译，快应用端 JSX 编译等，ESLint 插件维护

* 开发工具小组

负责 cli 工具的开发与优化工作

* 组件库小组

负责标准组件库的规范制定、文档编写，标准组件库在不同端的实现，以及标准组件库官网建设

* 运行框架小组

负责不同端运行框架的实现

* 端能力API小组 

负责端能力 API 的规范制定、文档编写，端能力 API 在不同端的实现

* UI 库小组 

负责 Taro UI 库的实现

* 社区维护小组 

负责社区内答疑解惑，GitHub ISSUE 维护，问题搜集，社区项目发起与引导，第三方库适配与生态管理等


------
> [下一篇](2-review-tool-chain.md)