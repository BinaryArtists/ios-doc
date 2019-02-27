## 编译错误

### [Xcode 5.1 enable C++14](http://stackoverflow.com/questions/22952314/xcode-5-1-enable-c14)

To get this to work, you first set "C++ Language Dialect" to "compiler default". Then in "Other C++ flags" add "-std=c++1y".

This will allow Clang++ to compile with c++14 from within Xcode. I tested this with Xcode 5.1.1 using the new user defined literal for basic_string:
```
std::string word = "hello"s;
```
Update: As of Xcode 6, c++14 is available as a first-class language dialect.

### Member initializer 'bits_' does not name a non-static data member or base class

里面说到将 ZXingWidget - BuildSettings - Other Warning Flags 里的 -Werror 和 -Wno-unused-parameter 去掉,
但其实只要去掉 -Werror 就可以了, 如果 -Wno-unused-parameter  这个也去掉了会产生很多变量没有使用的警告.
此时可以编译通过了,烦恼的是  也有少许警告.
