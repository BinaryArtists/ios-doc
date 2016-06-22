## 单元测试

  1. [iOS单元测试(作用及入门提升)](http://www.jianshu.com/p/8bbec078cabe)
    * [OCUnit](http://blog.csdn.net/jymn_chen/article/details/21552941)
    * [GHUnit](http://blog.csdn.net/jymn_chen/article/details/21562869)
    * [OCMock](http://blog.csdn.net/jymn_chen/article/details/21562869)
    * [UITests](http://www.cocoachina.com/ios/20150702/12253.html)
  2. [iOS中异步函数的单元测试](http://blog.csdn.net/diyagoanyhacker/article/details/8540239)

todo：
  1. [李智维的自动化单元测试的直播录影](http://pan.baidu.com/s/1skt0wNF)
  2. [李智维的演示github](https://github.com/lzwjava/LZAlbum)

### XCTest

  1. 同步过程case
    > 没啥好说的

  2. 异步过程case
    * 方法 1

    ```
    // waitForExpectationsWithTimeout是等待时间，超过了就不再等待往下执行。
    #define WAIT do {\
      [self expectationForNotification:@"RSBaseTest" object:nil handler:nil];\
      [self waitForExpectationsWithTimeout:30 handler:nil];\
    } while (0)

    #define NOTIFY \
    [[NSNotificationCenter defaultCenter]postNotificationName:@"RSBaseTest" object:nil]

    - (void)testRequest{
      // 1.获得请求管理者
      AFHTTPRequestOperationManager *mgr = [AFHTTPRequestOperationManager manager];
      mgr.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"text/html",nil];

      // 2.发送GET请求
      [mgr GET:@"http://www.weather.com.cn/adat/sk/101110101.html" parameters:nil success:^(AFHTTPRequestOperation *operation, id responseObject) {
        NSLog(@"responseObject:%@",responseObject);
        XCTAssertNotNil(responseObject, @"返回出错");
        NOTIFY //继续执行
      } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
        NSLog(@"error:%@",error);
        XCTAssertNil(error, @"请求出错");
        NOTIFY //继续执行
      }];

      WAIT  //暂停
    }    
    ```

    * 方法 2

    ```
    #define wait \
    CFRunLoopRun();

    #define notify \
    CFRunLoopRef runLoopRef = CFRunLoopGetCurrent();\
    CFRunLoopStop(runLoopRef);

    - (void)testSmscodeSignin {
      __block SmsResponse *response = nil;

      // 测试验证吗
      [self.signin sendSmsWithUsername:@"13817964625" success:^(SmsResponse *obj){
          notify

          response = obj;
      } failure:^(NSError *error) {
          XCTFail(@"send sms failed at \"%s\"", __PRETTY_FUNCTION__);
      }];

      wait

      XCTAssert(response, @"test sms code failed");

      // 测试验证吗登录
      [self.signin loginWithUsername:@"13817964625" sms:response.smsData.smsCode success:^(id obj) {
          notify
      } failure:^(NSError *error) {
          XCTFail(@"login by username sms, failed with error = %@", error);
      }];

      wait
    }
    ```

    * 结论：我用了方案1

### 其他
