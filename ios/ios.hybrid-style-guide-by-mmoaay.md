## [iOS代码规范（Swift 与 OC 混编版）](http://blog.csdn.net/mmoaay/article/details/46876915)


前言

按照自己的理解整理的 iOS 代码规范，部分规范参考了网上现有的一些资料，希望对大家有所帮助

编码规范

项目结构规范

项目结构图

|-业务1
|   |-业务1的Storyboard
|   |-子业务1
|   |   ｜-controller
|   |   ｜-view
|   |   `-model
|   |-子业务2
|   |   ｜-controller
|   |   ｜-views
|   |   `-models
|-业务2
|   |-业务2的Storyboard
|   |-子业务1
|   |   ｜-controller
|   |   ｜-view
|   |   `-model
|   |-子业务2
|   |   ｜-controller
|   |   ｜-view
|   |   `-model
|-util（工具）
|-lib（第三方库）
|-ui（自定义的一些基础view）
|-Images.xcassets（xcode自带图片管理文件）
`-Resources（资源文件，包括静态文件，本地化文件等）
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
原则

如果对应目录下有多个相关的类，则controller，view，model的名字相应变为controllers，views，models
Images.xcassets中的目录结构要与业务保持一致，从而方便查找和替换图片
注释

注释可以采用“/* */“和“//“两种注释符号，涉及到多行注释时，尽量使用“ /* */“
对于一行代码的注释可放在前一行及本行上，不允许放在下一行，更不允许在一行语句的中间加入注释
不必每行都加注释，在3~10行左右的段落做注释要好于每行都做注释，显而易见的代码不加注释。例如：

if (!returnValue){      //调用登录过程失败          ←无用的注释
    NSLog(@”登录失败”);
}
1
2
3
排版格式

代码的缩进应使用空格（SPACE），不能使用制表符（TAB），并且缩进以2个字符为单位
括弧遵循紧凑编码方式，如下：

for (int i = 0; i < 10 ; i++) {
}
1
2
空格的使用

关键字与其后的表达式之间要有空格，如：

if (expr)
或
for (expr)
1
2
3
单目操作符不应与它们的操作数分开（如“!“和“^“等）
除“,“外，其它双目操作符应与它们的操作数用空格隔开，如：

i=i+1;              //错误的写法，操作符两端没有空格
i = i + 1;          //正确的写法，
if(a>b)             //错误的写法，逻辑判断符号两端没有空格
if(a > b)           //正确的写法
1
2
3
4
.h中协议<>前面有一个空格
.h中成员声明时，类型与变量之间有至少1个空格。*号靠近变量，不靠近类型
@property后留1个空格，（）里面，逗号紧跟前一变量，与后一变量之间留1个空格。（）外面，先留1个空格，再声明属性
方法的+，-后面与（）之间留1个空格
返回类型与之间留1个空格，方法参数中返回类型与之间留1个空格
在多参数方法中，每个参数后面都有1个空格
每行只能有一个语句
关于空行 
.h中的空行 
文件说明与头文件包含(#import)之间空1行
头文件包含(#import)之间，如果需要分类区别，各类别之间空1行
头文件包含(#import)与@class之间空2行
@interface与@class之间空1行
头文件{}里面，空1行开始声明对象成员，如果需要分类区别，各类别之间空1行
头文件{}外，空1行书写属性，如果需要分类区别，各类别之间空1行
属性下面空1行开始写方法，如果需要分类区别，各类别之间空1行
方法完成后，空1行@end
如果需要声明protocol，空2行接着写。通常protocol写在@end后面，但是声明在@interface之前
.m中的空行 
文件说明与头文件包含(#import)之间空1行
头文件包含(#import)之间，如果需要分类区别，各类别之间空1行
@implementation和@synthesize之间空1行, 如果需要分类区别，各类别之间空1行
@synthesize与方法之间空1行
方法与方法之间空1行
方法里面的空行 
变量声明后需要空1行，如果需要分类区别，各类别之间空1行
条件、循环，选择语句，整个语句结束，需要空1行
各功能块之间空1行
最后一个括弧之前不空行
注释与代码之间不空行
#pragma mark 与方法之间空1行
每行代码最多不得操作100个字。设置如下：Xcode -> Preferences -> TextEditing 
-> Page Guide at column，输入 100即可
命名规范

文件命名规范

目录

目录的名字采用第一个单词首字母小写，其他单词首字母大写的格式，如：contactList、controller
目录主要用来对业务做区分，所以目录的名字要有意义。如： 
contactList：代表这是联系人列表的业务 
controller：代表这是MVC的控制部分
资源文件

资源文件的名字采用第一个单词首字母小写，其他单词首字母大写的格式，最后以文件类型结尾，如： 
contactImage：代表这是联系人图片 
contactInfoDB：代表这是联系人数据库文件 
contactLocalizable：代表这是联系人业务的本地化文件

代码命名规范

方法

方法的名称应全部使用有意义的单词组成，且以小写字母开头，多单词组合时，后面的单词首字母大写。如：

-(void)getContactInformation
1
设置类变量的内容的方法应使用set作为前缀，读取变量的内容的方法应使用get作为前缀。如：

-(void)getContactName;
-(void)setContactName:(NSString *)contactName;
1
2
方法中的参数：第一个参数名称要从函数名称上携带出来，第二个参数的首字母小写，多个单词组合时，后面单词首字母大写。参数有别名时，参数别名与参数名一致，但参数名前缀以_。参数别名与前一参数保留1个空格。参数无别名时，以有意义的字母命名。如：

-(void)myFunctionWithSizeA:(CGSize)sizeA sizeB:(CGSize)_sizeB;
1
变量

变量必须起有意义的名字，使其他组员可以很容易读懂变量所代表的意义，变量命名可以采用同义的英文命名，可使用几个英文单词，第一个单词首字母小写，其他单词首字母大写。如：

NSString  *contactName;
1
对于一些特殊类型的变量，命名时要带上类型，如NSArray 的变量命名为xxxArray，其他的如xxxDictionary，xxxSize等。这样就可以从名称上知道是什么类型的变量。千万不能将NSArray的变量命名为xxxDictionary。

对于要和interface builder关联的的输出口变量，命名时要后缀以特定的控件名。如：

IBOutlet UILabel *contactNameLabel;
1
对于使用c语言形式声明的变量，一些特定类型可采用一定的简写。如： 
指针类型：P 
结构体类型：Rec 
数组类型：Arr 
Core Graphic：CG 
等。 
循环控制变量通常使用单一的字符如：i、j、k等。使用有意义的名字，如objectIndex也是可以的

尽量避免使用全局变量，如果必须使用全局变量则必须加前缀“Pub_“，同时应在变量名称中体现变量的类型
私有实例变量前加一个下划线，如_myPrivateVarible
枚举变量也要有相应的前缀来区分不同的enum变量。比如苹果公司的一个enum。如：

typedef enum CGPathDrawingMode CGPathDrawingMode;
/* Drawing modes for text. */

enum CGTextDrawingMode 
{
     kCGTextFill,
     kCGTextStroke,
     kCGTextFillStroke,
     kCGTextInvisible,
     kCGTextFillClip,
     kCGTextStrokeClip,
     kCGTextFillStrokeClip,
     kCGTextClip
};
1
2
3
4
5
6
7
8
9
10
11
12
13
14
常量

避免在程序中直接出现常数，使用超过一次的应以宏定义的形式来替代
常数的宏定义应与它实际使用时的类型相一致。如以3.0来定义浮点类型，用3表示整型
常量的命名应当能够表达出它的用途，并且用大写字母表示。如：


#define PI 3.1415926
1
2
3
一些常量前加特殊前缀，可以作为不同常量的区分，如：

UserDefaultsKey的变量前加UDKEY_,
NotificationNameKey前面加NNKEY_,
DictionaryKey前面加DICTKEY_
1
2
3
类

所有的类名前加MB，以区分业务类和引入的第三方类
所有的类名，接口名(Protocol)均以大写字母开头，多单词组合时，后面的单词首字母大写。类，接口名必须是有意义的
继承自UIView的类以View结尾，如：MBContactInfoView，MBLabelView等
继承自UIViewController的类以ViewController结尾，如：MBContactInfoViewController，MBContactDetailViewController等。
架构规范

整体设计

遵循严格的MVC结构设计。
强化View层和Model层的功能，Control层只做调度： 
界面展示逻辑完全在View层控制，Control层通过View层提供的一个接口来将数据传递给View层，然后由View层来做具体的界面展示控制。
数据的加工在Model层实现，Model层通过重写setter或getter来对数据进行加工。
Control层负责响应事件和回调，然后发送网络请求等。
禁止出现功能不明确的类。类必须和具体业务对应
类设计

UIViewController

原则

UIViewController中尽量不要出现直接设置控件（UIButton，UITextField等）属性的代码，而是将控件封装在单独的自定义UIView类中，然后在自定义UIView类中设置控件的属性。如下的代码最好不要在UIViewController中出现：

self.textLabel = [[UILabel alloc] init];
self.textLabel.textColor = [UIColor blackColor];
self.textLabel ... ...
self.textLabel ... ...
self.textLabel ... ...
1
2
3
4
5
封装私有initContent和updateContent方法，前者在viewDidLoad中调用，用来初始化所有的自定义UIView，后者在viewWillAppear中调用，用来更新所有的自定义UIView。
所有的生命周期方法都必须调用super方法。
结构

import UIKit

class SwiftController: MBViewController {

    // 属性

    @IBOutlet weak var queryButton: UIButton!

    @IBOutlet weak var contentView: MBContentView!

    @IBOutlet weak var tableView: UITableView!

    var contactTitle:NSString?

    // 生命周期
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        // 与界面无关属性的初始化
        self.contactTitle = "This is Test"

        // 界面的初始化在统一的方法initContent中执行
        self.initContent()
    }

    override func viewWillAppear(animated: Bool) {
        super.viewWillAppear(animated)

        // 界面的更新在统一的方法updateCotent中执行
        self.updateCotent()
    }

    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)

        // 添加对通知事件的观察
        NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("queryFinished:"), name: "queryFinished", object: nil)
    }

    override func viewWillDisappear(animated: Bool) {
        super.viewWillDisappear(animated)
    }

    override func viewDidDisappear(animated: Bool) {
        super.viewDidDisappear(animated)

        // 移除对通知事件的观察
        NSNotificationCenter.defaultCenter().removeObserver(self, name: "queryFinished", object: nil)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

    // tableView协议
    func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        // #warning Potentially incomplete method implementation.
        // Return the number of sections.
        return 5
    }

    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // #warning Incomplete method implementation.
        // Return the number of rows in the section.
        return 10
    }

    func tableView(tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return String(format: "Section %d", section)
    }

    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    }

    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cellIdentifier : String = "cellIdentifier"
        var cell:UITableViewCell? = self.tableView.dequeueReusableCellWithIdentifier(cellIdentifier) as? UITableViewCell
        if cell == nil {
            cell = UITableViewCell(style: UITableViewCellStyle.Subtitle, reuseIdentifier: cellIdentifier)
        }
        cell?.textLabel?.text = String(format: "Cell %d", indexPath.row)
        cell?.detailTextLabel?.text = "Watch out!!! Something AWESOME is coming~~~"
        return cell!
    }

    // 事件响应
    @IBAction func queryPressed(sender: AnyObject) {

    }

    func queryFinished(sender: AnyObject) {

    }

    // 私有方法
    private func initContent() {

        // 如果需要设置多个属性则为每个View封装单独的方法来进行View的初始化
        self.initQueryButton()
    }

    private func updateCotent() {
    }

    private func initQueryButton () {
        self.queryButton.setTitle("Query", forState: UIControlState.Normal)
        self.queryButton.setTitle("Query...", forState: UIControlState.Disabled)
    }

    /*
    // MARK: - Navigation
    // In a storyboard-based application, you will often want to do a little preparation before navigation
    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        // Get the new view controller using segue.destinationViewController.
        // Pass the selected object to the new view controller.
    }
    */

}

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
UIView

原则

为每个自定义UIView建立xib（xib和UIView类关联的方式见DEMO），静态的约束在xib中直接添加，动态的约束使用Masonry在代码中添加。
尽量使用Autolayout做界面约束，只有在Autolayout不支持的情况下才使用设置frame的方法来做界面约束。
展示的逻辑在UIView内部控制，如果UIView中某几个控件的控制逻辑比较复杂，则需要封装单独的处理方法。
结构

import UIKit

class MBContentView: UIView {

    @IBOutlet var contentView: UIView!
    @IBOutlet var imageView: UIImageView!
    @IBOutlet var label: UILabel!

    /*
    // Only override drawRect: if you perform custom drawing.
    // An empty implementation adversely affects performance during animation.
    override func drawRect(rect: CGRect) {
        // Drawing code
    }
    */

    // 生命周期
    // 在该方法中初始化View
    required init(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)

        // 加载xib内容
        NSBundle.mainBundle().loadNibNamed("ContentView", owner: self, options: nil)
        self.addSubview(self.contentView)

        // 在统一的initConstraint方法中添加约束
        self.initConstraint()
    }

    override func layoutSubviews() {
        super.layoutSubviews()
    }

    // 私有方法
    private func initConstraint(){
        // OC 用 Masonry， Swift 用 SnapKit
        self.contentView.mas_makeConstraints { (MASConstraintMaker) -> Void in
            var make = MASConstraintMaker
            make.edges.equalTo().self
//            make.top.equalTo().self
//            make.bottom.equalTo().self
//            make.leading.equalTo().self
//            make.trailing.equalTo().self
        }
    }

    // 提供接口供ViewController调用
    func setQueryResult(queryResult:MBQueryResult?) {
        self.label.text = queryResult?.contactName
    }

}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
Form/Model

原则

数据的加工处理一般放在Form/Model中，如下：

-(void)setPoint:(NSString *)point{
    if ([point isEqualToString:@"null"]){
        _point = @"0";
    }else{
        _point = point;
    }
}
1
2
3
4
5
6
7
如果需要用到多个Model中的部分属性，则需要建立新的Model。

Category

原则

Category的命名要明确且跟具体的业务相关
能不用Category实现的功能就不用Category实现
Protocol

原则

Portocol的命名要明确且跟具体的业务相关
如果Portocol实现的方法不超过3个，可以直接定义在协议相关的类中，如下：

@protocol XXXDelegate <NSObject>
-(void)method1:(id)obj;
-(void)method2:(id)obj;
-(void)method3:(id)obj;
@end

@interface XXX : UIView<XXXDelegate>
@property (weak, nonatomic) IBOutlet id<XXXDelegate>delegate;
@end
1
2
3
4
5
6
7
8
9
超过3个则需要单独建立协议类

Manager

原则

如果Manager需要在应用的生命周期内一直存在，则实现成单例，单例实现方式如下：

class func shareInstance()->XXManager{
    struct MBSingleton{
        static var predicate:dispatch_once_t = 0
        static var instance:XXManager? = nil
    }
    dispatch_once(&MBSingleton.predicate,{
        MBSingleton.instance=XXManager()
        }
    )
    return MBSingleton.instance!
}
1
2
3
4
5
6
7
8
9
10
11
命名要和具体业务相关，禁止出现业务不明确的Manager

Manager不对数据进行加工处理，而是交由Form/Model来做，类似下面的代码不能写在Manager内部

if "zzz" == c.zzz {
    c.zzz = a.xxx+b.yyy
} else {
    c.zzz = a.xxx
}
1
2
3
4
5

