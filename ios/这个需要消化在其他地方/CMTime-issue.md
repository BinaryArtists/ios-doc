## CMTime


### CMTimeMake(a, b), a为当前第几帧，b为每秒播放多少帧，当前时间为a/b;

### CMTimeMakeWithSeconds(a, b), a为当前秒数，b为每秒播放多少帧，当前时间为a；

CMTimeMake顧名思義就是用來建立CMTime用的,
但是千萬別誤會他是拿來用在一般時間用的,
CMTime可是專門用來表示影片時間用的類別,
他的用法為: CMTimeMake(time, timeScale)

time指的就是時間(不是秒),
而時間要換算成秒就要看第二個參數timeScale了.
timeScale指的是1秒需要由幾個frame構成(可以視為fps),
因此真正要表達的時間就會是 time / timeScale 才會是秒.

簡單的舉個例子

CMTimeMake(60, 30);
CMTimeMake(30, 15);
在這兩個例子中所表達在影片中的時間都皆為2秒鐘,
但是影隔播放速率則不同, 相差了有兩倍.

