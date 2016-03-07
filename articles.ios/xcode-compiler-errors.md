## 编译错误

### [Xcode 5.1 enable C++14](http://stackoverflow.com/questions/22952314/xcode-5-1-enable-c14)

To get this to work, you first set "C++ Language Dialect" to "compiler default". Then in "Other C++ flags" add "-std=c++1y".

This will allow Clang++ to compile with c++14 from within Xcode. I tested this with Xcode 5.1.1 using the new user defined literal for basic_string:
```
std::string word = "hello"s;
```
Update: As of Xcode 6, c++14 is available as a first-class language dialect.

### []()
