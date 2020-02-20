# [lerna](https://lerna.js.org/)

**基于 GIT(主要是 commit) 和 NPM(工具链) 来辅助和优化开发者管理多包。**

* lerna-changelog

### Monorepo vs Multirepo

Monorepo 的全称是 monolithic repository，即单体式仓库，与之对应的是 Multirepo(multiple repository)，这里的“单”和“多”是指每个仓库中所管理的模块数量。

Multirepo 是比较传统的做法，即每一个 package 都单独用一个仓库来进行管理。例如：Rollup, ...

Monorep 是把所有相关的 package 都放在一个仓库里进行管理，每个 package 独立发布。例如：React, Angular, Babel, Jest, Umijs, Vue ...

![](./res/monovsmulti.jpg)

### 安装

```
npm i lerna -g
```

### 初始化

```
# 默认是 fixed 模式
$ lerna init
# 要采用 independent 模式的话
$ lerna init -i # lerna init --independent
```

生成的目录：

```
└── lerna-pro/
   ├── packages/
   ├── lerna.json
   └── package.json
```

### lerna.json 说明

```
{
    "useWorkspaces": true, // 使用 workspaces 配置。此项为 true 的话，将使用 package.json 的 "workspaces"，下面的 "packages" 字段将不生效
    "version": "0.1.0", // 所有包版本号，独立模式-"independent"
    "npmClient": "cnpm", // npm client，可设置为 cnpm、yarn 等
    "packages": [ // 包所在目录，可指定多个
        "packages/*"
    ],
    "command": { // lerna 命令相关配置
        "publish": { // 发布相关
            "ignoreChanges": [ // 指定文件或目录的变更，不触发 publish
                ".gitignore",
                "*.log",
                "*.md"
            ]
        },
        "bootstrap": { // bootstrap 相关
            "ignore": "npm-*",  // 不受 bootstrap 影响的包
            "npmClientArgs": [ // bootstr 执行参数
                "--no-package-lock"
            ]
        }
    }
}
```

### lerna 命令

```
/// Init
// 创建一个新的 lerna 仓库或将现有的仓库升级到 lerna 的当前版本。
lerna init # -i/--independent


/// Publish
// 发版本。提示输入新版本和更新所有的包在 git 和 npm。
lerna publish # --npm-tag [tagname] | -c | --skip-git | --force-publish [packages]

/// Bootstrap
// 把所有包的依赖安装到根 node_modules
lerna bootstrap

/// Run
lerna run < script > -- [..args]

/// Exec
// 在每个包运行任意命令。
lerna exec -- < command > [..args]

# example
lerna exec -- rm -rf ./node_modules
lerna exec -- protractor conf.js

/// Add
// 如果添加的是子项目，则会通过link软连接到对应的项目中

// 在package-1和package-2下安装babel
lerna add babel
// 在package-1下安装react
lerna add react --scope=package-1
// 在package-1下安装package-2
lerna add package-2 --scope=package-1

/// Create
// 创建一个子项目，并会根据交互提示生成对应的package.json
lerna create <name>

```

#### Link

https://github.com/lerna/lerna/tree/master/commands/link#readme

### 进阶使用

https://www.dazhuanlan.com/2019/10/02/5d939b682e3a2/

Lerna-changelog
lerna-changelog基于pr来为项目生成changelog

可参考repo

使用步骤

* 从master分支切换出feature/bugfix等分支，参考git-flow。
* 完成开发后进行commit，推荐使用commitizen来规范commit msg，同时有助于对后续子项目生成changelog。
将新分支push到remote端。
* 创建pr，并打上label，此处一定要打上label，learn-changelog就是根据label来确定该pr属于feature/bugfix/document等。
* 切记要在merge之前打上label。
* 进行merge pr操作。
* 本地切换到master分支并进行pull操作。
* 执行lerna-changelog，既可得到一份changeling。


### 问题

* @somescope/somepackage 这样的包如何发布？

进入 packages/你的包目录，执行：npm publish --access=public，发布完以后，下次你就可以在主项目里面使用：lerna publish来管理