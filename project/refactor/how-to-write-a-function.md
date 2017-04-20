## 如何写好函数？

目标：如何写好一个可读性高、易维护、易测试的函数。

*这个问题涵盖了什么内容？*

  * 1. 命名
  * 2. 函数参数（入参、出参）
  * 3. 合理建议：减少代码嵌套，提取复杂逻辑、语义化
  * 4. 老生常谈：编程原则，如“面向对象设计五大、六大，到底几大原则！草”，还有人说这是“设计模式之六大原则”，出处到底是什么？

### 命名

关于这点，我觉得《代码整洁之道》一书中阐述的很好。

  * 选用一个命名规则，统一使用
    1. 帕斯卡命名法：多个单词组成一个名称时，每个单词的首字母大写
    ```
    public void SendMessage ();
    public void CalculatePrice ();
    ```
    在C#中，这种命名法常用于类、属性，函数等等，在JS中，构造函数也推荐采用这种方式命名。

    2. 驼峰命名法：多个单词组成一个名称时，第一个单词全部小写，后面单词首字母大写
    ```
    var sendMessage = function () {};
    var calculatePrice = function () {};
    ```
    驼峰命名法一般用于字段、局部变量、函数参数等等。，在JS中，函数也常用此方法命名。

  * 尽可能完整地描述函数所做的所有事情（面向用途命名、面向原理或具体实现命名，前者用于定义接口，后者用于模块内阐述）
  ```
  // 描述不够完整的函数名
  var count = function addComment() {};

  // 描述完整的函数名
  var count = function addCommentAndReturnCount() {};
  ```

  * 采用准确的描述动词
  下面是整理的一些常用的对仗词，大家可以参考使用
  ```
  add/remove        increment/decrement       open/close
  begin/end            insert/delete                      show/hide
  create/destory    lock/unlock                        source/target
  first/last              min/max                             star/stop
  get/put                next/previous                     up/down     
  get/set                old/new
  ```

  * 根据不同项目和需求制定好命名规则
  通常采用的命名规则是动宾结构，也就是动词在前，名词灾后。但是有一些项目，比如数据接口等项目中，有的团队会采用名字在前，动词在后的形式，例如：
  ```
  public static Product[] ProductsGet(){};
  public static Product[] ProductsDel(){};
  public static Customer[] CustomerDel(){};
  public static Customer[] CustomerDel(){};
  ```

### 函数参数
  * 参数数量
  * 尽量不要使用bool类型作为参数
  ```
    var getProduct = function(finished) {
      if(finished) {
      } else {
      }
    }

    // 调用
    getProduct(true);
  ```
    如果没有注释，使用者看到这样的代码：getProduct(true)，他肯定搞不清楚true是代表什么意思，还要去查看函数定义才能明白这个函数是如何使用的。这就意味着这个函数不够清晰，就应该考虑去优化它。通常有两种方式去优化它：

    （1）将函数一分为二，分成两个函数getFinishedProduct和getUnFinishedProduct

    （2）将bool转换成有意义的枚举getProduct(ProductStatus)

  * 不要修改输入参数
  * 尽量不要使用输出参数
    使用输出参数说明这个函数不只做了一件事情，而且使用者使用的时候可能还会感到困惑。正确的方式应该是分解函数，让函数只做一件事。

### 编写函数体
  * 相关操作放在一起
  * 尽量减少代码嵌套，if-else，for
  * 提取复杂逻辑，语义化
    ```
    if (age > 18 && gender == "man") {
      //doSth
    }
    ```
    变成了：
    ```
    var canDoSth = function (age, gender){
      return age > 18 && gender == "man";
    }
    ...
    ...
    ...
    if(canDoSth(age, gender)){
      //doSth
    }
    ```

### Reference

  * [提高代码质量：如何编写函数](http://www.tuicool.com/articles/FVf2qyY)
