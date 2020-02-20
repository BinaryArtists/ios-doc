### 依赖关系

*package-lock.json*

[有什么npm包可以分析项目package.json的依赖关系吗？](https://segmentfault.com/q/1010000007661630/a-1020000007681620)

生成中间文件
```
webpack --profile --json > stats.json
```

上传到[这个网站](http://webpack.github.io/analyse/)分析依赖关系，[例子](https://github.com/webpack/webpack/issues/690)

### dependencies与peerDependencies区别

peerDependencies的目的是提示宿主环境去安装满足插件peerDependencies所指定依赖的包，然后在插件import或者require所依赖的包的时候，永远都是引用宿主环境统一安装的npm包，最终解决插件与所依赖包不一致的问题。

#### 具体一点？待补充