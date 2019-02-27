# 消除警告

## 主动忽略代码警告

http://blog.csdn.net/dream9393/article/details/53907241

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "警告名称"

// 被夹在这中间的代码针对于此警告都会无视并且不显示出来

#pragma clang diagnostic pop
```

## 枚举一些警告清除

* [Warning: The Copy Bundle Resources build phase contains this target's Info.plist file](https://stackoverflow.com/questions/3095612/warning-the-copy-bundle-resources-build-phase-contains-this-targets-info-plist)
	> [引用 官方说明](https://developer.apple.com/library/content/qa/qa1649/_index.html)


## 第三方库警告信息

https://zhuanlan.zhihu.com/p/24361970