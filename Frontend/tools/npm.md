## 命令

#### -

```
npm ls
npm login
npm whoami
npm publish
npm get prefix
npm root -g 
npm adduser
```

#### control

```
npm start 启动模块
npm stop 停止模块
npm restart 重新启动模块
npm test 测试模块
```

#### Cache

```
npm cache clean -f
```

#### Config

```
npm config ls
npm config get registry
npm config set registry http://registry.npmjs.org 
```

#### Scope

```
// publish默认发布私有包，package的private属性无效，必须使用：

npm publish --access=public

// 组织包需要先创建团队（团队被删掉，包就没了！）
// 将自己添加进团队
```

## 问题

#### You must sign up for private packages

包名为@your-name/your-package时才会出现：

```
npm publish --access public
```
#### 

```
npm config set registry https://registry.npmjs.org/
```
