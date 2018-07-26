## [纯IPv6环境App适配的坑](http://mrpeak.cn/blog/ipv6/)

苹果从2016年6月1号开始，强制所有app必须支持纯IPv6的网络环境。这项举措将对IPv6的普及起到一定的推动作用，也体现了Apple作为国际大厂的担当。大部分App由于使用的是高层API，并不需要做任何的更改就能自然度过这次IPv6的考核，只是对于少部分手写IPv4地址连server的同学，生活有一点点影响。

### 请好好学习TCP/IP协议

之前Apple默认开启ATS，推动HTTPS的使用，ATS可以手动关闭，但据说到2017年所有的HTTP流量都必须带S了，不然无法通过审核。这很make sense，我知道到HTTPS部署已经变得相当简易的今天，还有很多很多的App在侥幸走HTTP。

再后来有Apple将APN悄悄过渡到了HTTP2.0通道，HTTP2.0慢慢地走入寻常百姓家。

这次IPv6的推进，是一次对IP layer的复习机会。下图当中最重要的两个概念是DNS64和NAT64。

![ipv-6](https://github.com/BinaryArtists/not-just-code/blob/master/ios/resource/IPv6.001.png)

DNS64
DNS64说白了是用来帮助host获取IPv6地址的，传统的DNS服务器可以把域名转换成IPv4地址，但我们的iPhone设备如果处于IPv6环境下，只能去获取IPv6的地址。DNS64就像一个中间代理，把传统服务器返回的IPv4地址通过特殊的映射方式转换成一个看着像IPv6地址的地址（IPv4的核，IPv6的壳），转换其实很简单，用公式可以这样表达：

64:ff9b::IPv4 = IPv6
NAT64
DNS64帮助拿到IPv6的地址后，接下来就是NAT64登场，帮助IPv6的Packet顺利接入IPv4的公网当中。IPv4的公网环境路由器只认识IPv4的地址，所有这里当然也需要一个中间设备来做协议转换。NAT64就扮演这个角色。

我在上面的流程图当中已经比较清晰的画出了NAT64的工作方式。其实就是内部同时有IPv4和IPv6的网卡，IPv4的网卡配了一个IPv4的地址池子，再通过端口映射的方式将IPv4地址和IPv6地址对应，同时再做一些协议的转换，毕竟IPv4和IPv6的header完全不同。说白了就是一个内部路由的功能，将奔向IPv4公网的包做了地址和协议的转换。

一个深坑
博主公司所做的App就属于Apple重点点名，手写IPv4地址的典型。socket迁移过程当中踩了一个不大不小的坑，在尝试使用DNS64服务器将IPv4地址转换成IPv6地址的过程当中，发现数字端口号会导致奇异bug。

getaddrinfo(ipv4_str, @(port).stringValue.UTF8String, &hints, &res0);
我们一般使用getaddrinfo函数来解析host，如果端口号部分的入参是数字类型，会导致返回的结果里端口号被修改，当然socket就没法连成功啦。修改办法是在DNS query结果里手动将端口号再改回。

另一个办法是跳过DNS查询，直接自己将IPv4的地址转化成IPv6的地址。类似这样：

const char* ipv4mapped_str ="64:ff9b::121.43.xx.xxx";