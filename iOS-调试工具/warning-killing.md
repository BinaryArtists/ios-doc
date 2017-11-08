# 消除警告

## 主动忽略代码警告

http://blog.csdn.net/dream9393/article/details/53907241

```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "警告名称"

// 被夹在这中间的代码针对于此警告都会无视并且不显示出来

#pragma clang diagnostic pop
```


## 第三方库警告信息

https://zhuanlan.zhihu.com/p/24361970