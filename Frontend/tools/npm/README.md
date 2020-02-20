## 命令

* [版本管理](./VERSION.md)
* [依赖关系](./DEPEND.md)

### Basic

```
npm whoami
npm get prefix
npm root -g 
```

### Account

```
npm login
npm adduser
```

### Package

```
npm ls
npm publish
npm publish --access=public
npm unpublish
npm i/install
npm i -S/--save
npm i -D/--save-dev
npm uninstall
npm view <package-name> version

cd npm-link-module
npm link
cd npm-link-example
npm link npm-link-module
```


### Control

```
npm start
npm stop
npm restart
npm test
```

### Cache

```
npm cache clean -f
```

### Config

```
npm config ls
npm config get registry
npm config set registry http://registry.npmjs.org 
```

### Scope

**如何发布scope包呢，如：@<org-name>/<package-name>？**

1. 登录 npmjs.com
2. 创建组织/团队/成员
3. 确认发布者是团队成员
```
npm publish --access=public
```

## 问题

#### You must sign up for private packages

包名为@your-name/your-package时才会出现：

```
npm publish --access public
```
### 

```
npm config set registry https://registry.npmjs.org/
```
