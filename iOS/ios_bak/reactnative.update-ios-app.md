## [AppHub：绕过苹果审核机制更新iOS App](http://www.infoq.com/cn/news/2015/08/apphub?utm_campaign=infoq_content&)

主要依赖：
	1. [React Native](http://www.reactnative.com/)
	2. [AppHub](https://apphub.io/)

### 摘文

为了保证应用程序的合法性和安全性，苹果公司对每一款应用程序设定了严格的审查制度。每一个软件在发布之前都会经历一段时间的机器或人工审核阶段。这个时间短则几天，多达数周。对于敏捷软件开发团队而言，他们经常面临上一个版本还在审查中，一个新的版本已经完成修改的情况。为了解决如此尴尬的局面，毕业于MIT的Matt Arbesfeld联合其好友设计了AppHub，使得面向iOS系统的React Native应用能够快速更新。接下来，本文就对AppHub进行简要介绍。

首先，作为Facebook在React.js Conf 2015会议上推出的项目，React Native利用开源JavaScript库和React.js进行iOS和Android原生应用的开发。其优势在于提高了代码和模块的可重用性，并使得代码从服务器端动态更新成为可能。目前，Facebook已经将React Native用于生产环境，并将其进行开源，代码依托在GitHub中。

正是基于React Native的诸多特性，AppHub实现了快速更新React Native应用的功能。那么，究竟如何利用AppHub服务实现应用的快速更新呢？首先，应用开发人员需要下载并解压用于iOS的AppHub SDK，并将其添加到应用程序中。具体做法为：把下载的AppHub.framework放入到Xcode项目；选择“Copy items to destination's group folder”复选框；在Link Binary With Library的编译阶段添加libz.dylib；把应用连接到AppHub。接下来，开发人员就可以不断的使用dashboard或者REST API把更新发送到AppHub服务器中。

在AppHub中，开发人员为每一个移动应用程序都创建了一个App。这个App拥有唯一的应用ID，用来配置SDK。此外，该App会保留移动应用的多个版本。开发人员利用AppHub的dashboard可以对这些版本进行配置和部署。服务器自动探测App的配置，把新的代码替换到到应用中，从而实现客户端应用的自动更新。目前，AppHub所支持客户端操作系统为iOS 8.0+，所支持React Native库的版本为v0.7.0+。另外，就开发者所关心的AppHub是否是否被苹果公司所允许这一问题，AppHub官网表示，苹果公司在开发者协议里特别允许了这种做法。本质上，AppHub应用和React Native应用基本相同。而React Native应用已被证明是可以被苹果所接受的。因此，AppHub这种做法肯定也会被苹果公司所接受


