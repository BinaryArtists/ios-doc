# 版本管理

```
"dependencies": {
  "body-parser": "^1.13.2",
  "cookie-parser": "~1.3.5",
  "ejs": "*",
  "express": "~4.13.x",
}

// 主版本号/次版本号/修订版本号
main.minor.patch

~x.y.z: 匹配大于 x.y.z 的 z 的最新版
^x.y.z: 匹配大于 x.y.z 的 y.z 的最新版
当 x 为 0 时，^x.y.z 等价于 ~x.y.z，即只会安装z 的最新版本；
当 x 和 y 为 0 时，^x.y.z 等价于 x.y.z，即只会安装x.y.z 版本；

x.y.z 或 <=x.y.z: 大于 x.y.z 的最大版本或与 x.y.z 最接近的版本
x.y.z: 选择 x.y.z

*: 任意版本，一般是最后一次正式发布版本（包括非 latest tag），不是最大版本号版本    

```

### npm库版本

* 通过prefix控制

```
^1.5.1 【限制主版本号】允许安装版本号大于 1.5.1 但小于 2.0.0 版本的模块
npm默认采用这种方式

~1.5.1 【限制次要版本】 允许安装版本号大于 1.5.1 但小于 1.6.0 版本的模块
可通过 npm config set save-prefix="~"将默认的^修改为~

1.5.1 【精确控制】允许安装版本号大于 1.5.1
可通过npm config set save-exact true 将默认的^取除掉
```

* 通过package-lock.json控制

```
npm 5.0.x版本
不管package.json怎么变化，npm  install 都会根据package-lock.json 文件下载；

npm 5.1.0+版本
npm  install会无视package-lock.json， 根据package.json下载，也就是package.json的权限会高于package-lock.json；

npm 5.4.2+版本
npm  install时 如果package.json与package-lock.json不一致，则根据package.json的语法规则进行下载；如果一致时，则都会根据package-lock.json下载；
```