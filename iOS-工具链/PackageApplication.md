# PackageApplication

原文章：http://blog.csdn.net/itiapp_home/article/details/70241011

```
xcrun: error: unable to find utility "PackageApplication", not a developer tool or in PATH
```

### 目标目录	

/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/

```
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer/
```

```
chmod +x /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/PackageApplication
```