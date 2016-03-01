## buddybuild 集成平台使用经验

参考链接：
  1. https://github.com/clarencenhuang/cordova-buddybuild有用吗？

### 准备信息

1. [官网](https://buddybuild.com)
2. 苹果开发账号：username，password
3. 准备好工程：https://github.com/BinaryArtists/FATodos
4. 测试buddybuild账号：fengzilijie@qq.com,fengzi


### 刚起步

1. Log in，我选择的 *try our demo app*
  > In this demo, you'll experience:
  > 1. Instant deployment to your device
  > 2. Sending annotated screenshots
  > 3. Crash Reporting & Source Context
  > 4. A mobile optimized CI(？？？？http://baike.baidu.com/link?url=FGaG2ANoW2eQisZGoKLWbzYoZ_yuS8XD55YMVWh5iVETuMcdAkjJTv9mzCC61VDIcfqAv4soR2UkzXQ1N-Px3_) Solution

2. Let's Get started：
  > 支持iOS
  > 支持android

3. 选择iOS后，接受邮件，收到后，有按钮显示：“install the 2048 demo app”［一个企业级应用］

4. 然后，转入safari中，显示“register your device”

5. 接着，转入设置，注册并安装配置描述文件：buddybuild.com Config ［？？？？咱不知道原理，需要研究］

6. 现在浏览器中显示，重要的提示：Buddybuild makes installing apps for new testers incredibly easy，这样新增的应用测试机器，就不必一一配置与重装。

7. 点击进入2048，截图，发送回执，等都是用户引导，跟着完成即可。

8. 浏览器，去面板（dashboard）查看crash记录，“Let's check out the feedback details”

9. 查看崩溃日志（Crash Reports），神奇的事情发生了～

10. 接下去是看，构建app

11. 最后，“Add your own app to buddy build”，添加自己的app到buddybuild

### 起飞之前

--------
*构建*
1. 选择 *Build Your App* ［假设你是用github，用gitlab是类似的］

2. 设置 git url地址（一定要是ssh的，如果你用的是github，那么在HTTP选项下拉选择SSH）

3. 当前页面会生成 ssh-rsa，然后编辑profile，在SSH keys中添加前面生成的key

4. 下一步就是 verify and build

5. 它先检查与辨认，git仓库下有哪些被认为是app的，你点击选择正确的那个

6. 构建开始，有错误你修正一下，重新来过

7. 直到你看到：The build was successful!

8. 点击，“goto the dashboard”
8.1. 如果需要持续构建：
  >Automate your Builds
  If you'd like us to kick off a build automatically when you push, register a ​Webhook​ with the following "Payload URL":
  https://dashboard.buddybuild.com/api/webhook?appID=56d55a0230e6c7010002ab97

9. 提示，Invite a few testers（略）［或者在？？？可以主动设置？？？］

10. 在dashboard中可以看到，几个标题栏：Builds、Deployments、Feedback、CrashReports，Builds的右侧友Build Settings［要特别关注］

--------
*部署*
11. 将要部署到一个设备上啦，

12. 上传证书，“Upload Certificates”

*后来就没有继续了，初步看来适用于：个人开发、小团队协作*

#### 起飞了

恭喜你，已经起飞了～

注意降落，避免入坑。

####
