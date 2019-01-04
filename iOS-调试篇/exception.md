# 异常处理

## 不禁发问，为什么需要异常处理？

不可避免的：异常是程序中的一些错误，但并不是所有的错误都是异常，并且错误有时候是可以避免的

*错误处理*

C语言

```
double divide(double a, double b)
{
    const double delta = 0.000000000000001;
    double ret = 0;
    
    if(!((-delta < b) && (b < delta)))
    {
        ret = a / b;
    }
    else
    {
        longjmp(env, 1); //当错误发生时会跳转到setjmp那么的代码
                         //处，重新执行setjmp并把其的返回值设为1
    }
    return ret;
}

int main()
{
     
    if(setjmp(env) == 0) //先保存上下文环境，刚调用时返回值为0
    {
        double r = divide(1, 0);    
        cout << "r = " << r << endl;
    }
    else 
    {
        cout << "Divide by zero..." << endl;
    }
      
    return 0;
}
```

*异常处理，更面向对象*

*封装了异常包含的丰富的错误信息*

C++，Java，Javascript，python等语言

```
double divide(double a, double b)
{
    const double delta = 0.000000000000001;
    double ret = 0;
    
    if(!((-delta < b) && (b < delta)))
    {
        ret = a / b;
    }
    else
    {
        throw 0; //除0异常时，抛出整数0。
                 //因异常不在该函数处理，会向外层调用函数传递
    }
    return ret;
}

int main()
{    
    try     //处理正常模块
    {
        //除0时由于divide函数抛出异常，而在函数内部那里又没有处理该异常，因此异常被继续抛出，到了main函数中
        //调用函数的代码之处，而在这里异常被try-catch捕获会转向catch语句中，catch处理完后，会直接转向catch
        //语句的后面"return 0;"去执行。而不是转到调用函数的下一行语句（即cout << "r = " << r << endl;）
        double r = divide(1, 0);  
        
        cout << "r = " << r << endl;
    }
    catch(...)     //处理异常模块，...表示捕获所有类型的异常
    {
        cout << "Divide by zero..." << endl;
    }
      
    return 0;
}
```



