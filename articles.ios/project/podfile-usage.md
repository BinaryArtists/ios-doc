## CocoPods


## Option 'use_frameworks!'
http://blog.cocoapods.org/CocoaPods-0.36/: use_frameworks! enables Swift support by building your code in the new dynamic frameworks. Dynamic frameworks only support iOS 8

##  [CocoaPods pod install/pod update更新慢的问题](http://blog.csdn.net/ralbatr/article/details/39082937)

无论是执行pod install还是pod update都卡在了Analyzing dependencies不动：原因在于当执行以上两个命令的时候会升级CocoaPods的spec仓库，加一个参数可以省略这一步，然后速度就会提升不少。加参数的命令如下：
pod install --verbose --no-repo-update
pod update --verbose --no-repo-update

## [cocoapods:常见错误总结](http://blog.csdn.net/wangyanchang21/article/details/51437934)

1. in `to_specs': Could not find 'cocoapods' (>= 0) among 68 total gem(s) (Gem::MissingSpecError)``
>

2. 出现这个问题：```
/Users/wangzz/.rvm/rubies/ruby-1.9.3-p448/lib/ruby/site_ruby/1.9.1/rubygems/dependency.rb:298:in `to_specs': Could not find 'cocoapods' (>= 0) among 6 total gem(s) (Gem::LoadError)  
    from /Users/wangzz/.rvm/rubies/ruby-1.9.3-p448/lib/ruby/site_ruby/1.9.1/rubygems/dependency.rb:309:in `to_spec'  
    from /Users/wangzz/.rvm/rubies/ruby-1.9.3-p448/lib/ruby/site_ruby/1.9.1/rubygems/core_ext/kernel_gem.rb:53:in `gem'  
    from /Users/wangzz/.rvm/rubies/ruby-1.9.3-p448/bin/pod:22:in `<main>'  
```

参考：(http://blog.csdn.net/wzzvictory/article/details/18737437)

```
rvm use ruby-1.9.3-p448  
```

安装rvm，看 [macosx zsh下安装rvm和ruby](http://jingpin.jikexueyuan.com/article/58150.html)

安装ruby时，出了问题：```
Error running 'requirements_osx_brew_update_system ruby-2.0.0-p648', showing last 15 lines of /Users
```

参考：[Mac 用RVM安装ruby 2.0.0的时候出现错误](http://blog.csdn.net/luohancc/article/details/46897247)

其中还提到：*Xcode->homebrew->RVM->Ruby->CocoaPods* ，这是基本的依赖关系，一定要知道，一开始我没注意，哭～（安装ruby时出问题，就是因为这个）

其中安装HomeBrew：http://brew.sh/index_zh-cn.html

解决HomeBrew更新问题：[更换Homebrew更新源](http://www.jianshu.com/p/61634c8ed302)

如果遇到：```
error: could not lock config file .git/config: Permission denied
```

则
```
sudo chgrp -R admin /usr/local
sudo chmod -R g+w /usr/local
```

至此，HomeBrew更新OK。。。开始安装ruby

安装OK！！

如果安装 pod的时候，ruby版本低了，就rvm install 对应版本，并切到对应版本：rvm use ruby-2.3.0-p0

然后执行：sudo gem install -n /usr/local/bin cocoapods
再执行：pod setup
OK。

3. gem 保存问题：While executing gem ... (Gem::RemoteFetcher::FetchError)bad response Not Found 404 (http://ruby.taobao.org/specs.4.8.gz)
> gem sources -a https://ruby.taobao.org/
> gem sources -r http://ruby.taobao.org/
> sudo gem update --system // 这样就OK了

## [cocoapods:为新项目添加第三方类库时出错](http://blog.csdn.net/wangyanchang21/article/details/51307021)
