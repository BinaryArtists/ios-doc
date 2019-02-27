## [Swift - Realm数据库的使用详解（附样例）](http://www.hangge.com/blog/cache/detail_891.html)

### -----未整理－－－－

1，什么是Realm
Realm于2014 年7月发布，是一个跨平台的移动数据库引擎，专门为移动应用的数据持久化而生。其目的是要取代Core Data和SQLite。

2，关于Realm，你要知道下面几点：
（1）使用简单，大部分常用的功能（比如插入、查询等）都可以用一行简单的代码轻松完成，学习成本低。
（2）Realm不是基于Core Data，也不是基于SQLite封装构建的。它有自己的数据库存储引擎。
（3）Realm具有良好的跨平台特性，可以在iOS和Android平台上共同使用。代码可以使用 Swift 、 Objective-C 以及 Java 语言来编写。
（4）Realm 还提供了一个轻量级的数据库查看工具（Realm Browser）。你也可以用它进行一些简单的编辑操作（比如插入和删除操作）

3，支持的类型
（1）Realm支持以下的属性类型：Bool、Int8、Int16、Int32、Int64、Double、Float、String、NSDate（精度到秒）以及NSData.
（2）也可以使用List<object> 和Object来建立诸如一对多、一对一之类的关系模型，此外Object的子类也支持此功能。

4，Realm的安装配置
（1）先去Realm的官网去下载最新框架：http://static.realm.io/downloads/swift/latest
（2）拖拽RealmSwift.framework和Realm.framework文件到”Embedded Binaries”选项中。选中Copy items if needed并点击Finish
原文:Swift - Realm数据库的使用详解（附样例）


5，将数据插入到数据库中
下面代码判断默认数据库中是否有数据，如果没有的话将几个自定义对像插入到数据库中。
（1）这里以个人消费记录为例，我们先定义消费类别类，和具体消费记录类
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
import RealmSwift

//消费类型
class ConsumeType:Object {
    //类型名
    dynamic var name = ""
}

//消费条目
class ConsumeItem:Object {
    //条目名
    dynamic var name = ""
    //金额
    dynamic var cost = 0.00
    //时间
    dynamic var date = NSDate()
    //所属消费类别
    dynamic var type:ConsumeType?
}

（2）判断数据库记录是否为空，空的话则插入数据库（这里以默认数据库为例）
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
import UIKit
import RealmSwift

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        //使用默认的数据库
        let realm = try! Realm()
        //查询所有的消费记录
        let items = realm.objects(ConsumeItem)
        //已经有记录的话就不插入了
        if items.count>0 {
            return
        }

        //创建两个消费类型
        let type1 = ConsumeType()
        type1.name = "购物"
        let type2 = ConsumeType()
        type2.name = "娱乐"

        //创建三个消费记录
        let item1 = ConsumeItem(value: ["买一台电脑",5999.00,NSDate(),type1]) //可使用数组创建

        let item2 = ConsumeItem()
        item2.name = "看一场电影"
        item2.cost = 30.00
        item2.date = NSDate(timeIntervalSinceNow: -36000)
        item2.type = type2

        let item3 = ConsumeItem()
        item3.name = "买一包泡面"
        item3.cost = 2.50
        item3.date = NSDate(timeIntervalSinceNow: -72000)
        item3.type = type1

        // 数据持久化操作（类型记录也会自动添加的）
        try! realm.write {
            realm.add(item1)
            realm.add(item2)
            realm.add(item3)
        }

        //打印出数据库地址
        print(realm.path)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
}

6，使用Realm Browser查看数据库  
（1）默认数据库是应用的 Documents 文件夹下的一个名为“default.realm”。
1
2
//打印出数据库地址
print(realm.path)
（2）使用Realm Browser工具可以很方便的对.realm数据库进行读取和编辑（在App Store中搜索Realm Browser即可下载）。
可以看到，上面的几个对象已经成功的插入到数据库中来。

原文:Swift - Realm数据库的使用详解（附样例）


原文:Swift - Realm数据库的使用详解（附样例）


6，从数据库中读取记录并显示到表格中来
（1）通过查询操作，Realm 将会返回包含 Object 集合的Results实例。Results 的表现和 Array 十分相似，并且包含在 Results 中的对象能够通过索引下标进行访问。
（2）所有的查询（包括查询和属性访问）在 Realm 中都是延迟加载的，只有当属性被访问时，才能够读取相应的数据。
（3）查询结果并不是数据的拷贝：修改查询结果（在写入事务中）会直接修改硬盘上的数据。

下面我们把库里的数据加载出来，并通过表格显示出来。
效果图如下：
原文:Swift - Realm数据库的使用详解（附样例）


代码如下：
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
import UIKit
import RealmSwift

class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource  {

    @IBOutlet weak var tableView: UITableView!

    var dformatter = NSDateFormatter()

    //保存从数据库中查询出来的结果集
    var consumeItems:Results<ConsumeItem>?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.dformatter.dateFormat = "MM月dd日 HH:mm"

        self.tableView!.delegate = self
        self.tableView!.dataSource = self
        //创建一个重用的单元格
        self.tableView!.registerClass(UITableViewCell.self, forCellReuseIdentifier: "MyCell")

        //使用默认的数据库
        let realm = try! Realm()
        //查询所有的消费记录
        consumeItems = realm.objects(ConsumeItem)
    }

    //在本例中，只有一个分区
    func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        return 1;
    }

    //返回表格行数（也就是返回控件数）
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.consumeItems!.count
    }

    //创建各单元显示内容(创建参数indexPath指定的单元）
    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath)
        -> UITableViewCell
    {
        //同一形式的单元格重复使用，在声明时已注册
        let cell = UITableViewCell(style: .Value1, reuseIdentifier: "MyCell")
        let item = self.consumeItems![indexPath.row]
        cell.textLabel?.text = item.name + " ￥" + String(format: "%.1f", item.cost)
        cell.detailTextLabel?.text = self.dformatter.stringFromDate(item.date)
        return cell
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}

7，支持断言查询(Predicate)，这样可以通过条件查询特定数据
同时可以使用链式查询数据。
1
2
3
4
5
6
7
8
9
//查询花费超过10元的消费记录(使用断言字符串查询)
consumeItems = self.realm.objects(ConsumeItem).filter("cost > 10")

//查询花费超过10元的购物记录(使用 NSPredicate 查询)
let predicate = NSPredicate(format: "type.name = '购物' AND cost > 10")
consumeItems = self.realm.objects(ConsumeItem).filter(predicate)

//使用链式查询
consumeItems = self.realm.objects(ConsumeItem).filter("cost > 10").filter("type.name = '购物'")
支持的断言：
比较操作数(comparison operand)可以是属性名称或者某个常量，但至少有一个操作数必须是属性名称；
比较操作符 ==、<=、<、>=、>、!=, 以及 BETWEEN 支持 int、long、long long、float、double 以及 NSDate 属性类型的比较，比如说 age == 45；
相等比较 ==以及!=，比如说Results<Employee>().filter("company == %@", company)
比较操作符 == and != 支持布尔属性；
对于 NSString 和 NSData 属性来说，我们支持 ==、!=、BEGINSWITH、CONTAINS 以及 ENDSWITH 操作符，比如说 name CONTAINS ‘Ja’；
字符串支持忽略大小写的比较方式，比如说 name CONTAINS[c] ‘Ja’ ，注意到其中字符的大小写将被忽略；
Realm 支持以下复合操作符：“AND”、“OR” 以及 “NOT”。比如说 name BEGINSWITH ‘J’ AND age >= 32；
包含操作符 IN，比如说 name IN {‘Lisa’, ‘Spike’, ‘Hachi’}；
==、!=支持与 nil 比较，比如说 Results<Company>().filter("ceo == nil")。注意到这只适用于有关系的对象，这里 ceo 是 Company 模型的一个属性。
ANY 比较，比如说 ANY student.age < 21
注意，虽然我们不支持复合表达式类型(aggregate expression type)，但是我们支持对对象的值使用 BETWEEN 操作符类型。比如说，Results<Person>.filter("age BETWEEN %@", [42, 43]])。

8，查询结果的排序
1
2
//查询花费超过10元的消费记录,并按升序排列
consumeItems = self.realm.objects(ConsumeItem).filter("cost > 10").sorted("cost")

9，使用List实现一对多关系
List中可以包含简单类型的Object，表面上和可变的Array非常类似。
注意：List 只能够包含 Object 类型，不能包含诸如String之类的基础类型。
如果打算给我们的 Person 数据模型添加一个“dogs”属性，以便能够和多个“dogs”建立关系，也就是表明一个 Person 可以有多个 Dog，那么我们可以声明一个List类型的属性：
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
class Person: Object {
    ... // 其余的属性声明
    let dogs = List<Dog>()
}

// 这里我们就可以使用已存在的狗狗对象来完成初始化
let aPerson = Person(value: ["李四", 30, [aDog, anotherDog]])

// 还可以使用多重嵌套
let aPerson = Person(value: ["李四", 30, [["小黑", 5], ["旺财", 6]]])
可以和之前一样，对 List 属性进行访问和赋值：
1
2
3
let someDogs = Realm().objects(Dog).filter("name contains '小白'")
ZhangSan.dogs.extend(someDogs)
ZhangSan.dogs.append(dahuang)
反向关系(Inverse Relationship)
通过反向关系(也被称为反向链接(backlink))，您可以通过一个特定的属性获取和给定对象有关系的所有对象。比如说，对 Dog 的实例调用Object().linkingObjects(_:forProperty:) 会返回对应的与被调用实例所指定属性的类有关系的所有对象。通过在 Dog 中定义一个只读（计算）属性 owners 可以简化这个操作：
1
2
3
4
5
6
7
8
9
class Dog: Object {
    dynamic var name = ""
    dynamic var age = 0
    var owners: [Person] {
        // Realm 并不会存储这个属性，因为这个属性只定义了 getter
        // 定义“owners”，和 Person.dogs 建立反向关系
        return linkingObjects(Person.self, forProperty: "dogs")
    }
}

10，添加主键(Primary Keys)
重写 Object.primaryKey() 可以设置模型的主键。
声明主键之后，对象将被允许查询，更新速度更加高效，并且要求每个对象保持唯一性。
一旦带有主键的对象被添加到 Realm 之后，该对象的主键将不可修改。
1
2
3
4
5
6
7
8
class Person: Object {
  dynamic var id = 0
  dynamic var name = ""

  override static func primaryKey() -> String? {
    return "id"
  }
}

11，添加索引属性(Indexed Properties)
重写 Object.indexedProperties() 方法可以为数据模型中需要添加索引的属性建立索引：
1
2
3
4
5
6
7
8
class Book: Object {
  dynamic var price = 0
  dynamic var title = ""

  override static func indexedProperties() -> [String] {
    return ["title"]
  }
}

12，设置忽略属性(Ignored Properties)
重写 Object.ignoredProperties() 可以防止 Realm 存储数据模型的某个属性。Realm 将不会干涉这些属性的常规操作，它们将由成员变量(ivar)提供支持，并且您能够轻易重写它们的 setter 和 getter。
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
class Person: Object {
  dynamic var tmpID = 0
  var name: String { // 计算属性将被自动忽略
    return "\(firstName) \(lastName)"
  }
  dynamic var firstName = ""
  dynamic var lastName = ""

  override static func ignoredProperties() -> [String] {
    return ["tmpID"]
  }
}

13，修改更新数据
（1）直接更新内容
1
2
3
4
// 在一个事务中更新对象
realm.write {
  consumeItem.name = "去北京旅行"
}
（2）通过主键更新
如果您的数据模型中设置了主键的话，那么您可以使用Realm().add(_:update:)来更新对象（当对象不存在时也会自动插入新的对象。）
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
/****** 方式1 ***/
// 创建一个带有主键的“书籍”对象，作为事先存储的书籍
let cheeseBook = Book()
cheeseBook.title = "奶酪食谱"
cheeseBook.price = 9000
cheeseBook.id = 1

// 通过 id = 1 更新该书籍
realm.write {
  realm.add(cheeseBook, update: true)
}

/****** 方式2 ***/
// 假设带有主键值 `1` 的“书籍”对象已经存在
realm.write {
  realm.create(Book.self, value: ["id": 1, "price": 9000.0], update: true)
// 这本书的`title`属性不会被改变
}
（3）键值编码
这个是在运行时才能决定哪个属性需要更新的时候，这个对于大量更新的对象极为有用。
1
2
3
4
5
6
7
let persons = Realm().objects(Person)
Realm().write {
  // 更新第一个
  persons.first?.setValue(true, forKeyPath: "isFirst")
  // 将每个人的 planet 属性设置为“地球”
  persons.setValue("地球", forKeyPath: "planet")
}

14，删除数据
1
2
3
4
5
6
let cheeseBook = ... // 存储在 Realm 中的 Book 对象

// 在事务中删除一个对象
realm.write {
  realm.delete(cheeseBook)
}
也能够删除数据库中的所有数据
1
2
3
4
// 从 Realm 中删除所有数据
realm.write {
  realm.deleteAll()
}

15，Realm数据库配置
（1）修改默认的的数据库
通过调用 Realm() 来初始化以及访问我们的 realm 变量。其指向的是应用的 Documents 文件夹下的一个名为“default.realm”的文件。
通过对默认配置进行更改，我们可以使用不同的数据库。比如给每个用户帐号创建一个特有的 Realm 文件，通过切换配置，就可以直接使用默认的 Realm 数据库来直接访问各自数据库：
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
func setDefaultRealmForUser(username: String) {
  var config = Realm.Configuration()

  // 使用默认的目录，但是使用用户名来替换默认的文件名
  config.path = config.path.stringByDeletingLastPathComponent()
                           .stringByAppendingPathComponent(username)
                           .stringByAppendingPathExtension("realm")

  // 将这个配置应用到默认的 Realm 数据库当中
  Realm.Configuration.defaultConfiguration = config
}
（2）打包进项目里的数据库的使用
如果需要将应用的某些数据（比如配置信息，初始化信息等）打包到一个 Realm 文件中，作为主要 Realm 数据库的扩展，操作如下：
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
let config = Realm.Configuration(
    // 获取需要打包文件的路径
    path: NSBundle.mainBundle().pathForResource("MyBundledData", ofType:"realm"),
    // 以只读模式打开文件，因为应用数据包并不可写
    readOnly: true)

// 通过配置打开 Realm 数据库
let realm = Realm(configuration: config)

// 通过配置打开 Realm 数据库
let results = realm.objects(Dog).filter("age > 5")
（3）内存数据库
内存数据库在每次程序运行期间都不会保存数据。但是，这不会妨碍到 Realm 的其他功能，包括查询、关系以及线程安全。 假如您需要灵活的数据读写但又不想储存数据的话，那么内存数据库对您来说一定是一个不错的选择。
1
let realm = Realm(configuration: Realm.Configuration(inMemoryIdentifier: "MyInMemoryRealm"))

16，加密数据库
（1）加密后的 Realm文件不能跨平台使用（因为NSFileProtection 只有 iOS 才可以使用）
（2）Realm 文件不能在没有密码保护的 iOS 设备中进行加密。为了避免这些问题（或者您想构建一个 OS X 的应用），可以使用 Realm 提供的加密方法。
（3）加密过的 Realm 只会带来很少的额外资源占用（通常最多只会比平常慢10%）。
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
/*****   在创建 Realm 数据库时采用64位的密钥对数据库文件进行 AES-256+SHA2 加密   ****/
// 产生随机密钥
let key = NSMutableData(length: 64)!
SecRandomCopyBytes(kSecRandomDefault, UInt(key.length),
    UnsafeMutablePointer<UInt8>(key.mutableBytes))

// 打开加密文件
let config = Realm.Configuration(encryptionKey: key)
var error: NSError?
let realm = Realm(configuration: config, error: &error)
if realm == nil {
    // 如果密钥错误，`error` 会提示数据库不可访问
    println("Error opening realm: \(error)")
    return
}

// 和往常一样使用 Realm 即可
let dogs = realm.objects(Dog).filter("name contains 'Fido'")

17，数据迁移(Migration)
（1）为何要迁移
比如原来有如下 Person 模型：
1
2
3
4
5
class Person: Object {
    dynamic var firstName = ""
    dynamic var lastName = ""
    dynamic var age = 0
}
假如我们想要更新数据模型，给它添加一个 fullname 属性， 而不是将“姓”和“名”分离开来。
1
2
3
4
class Person: Object {
    dynamic var fullName = ""
    dynamic var age = 0
}
在这个时候如果您在数据模型更新之前就已经保存了数据的话，那么 Realm 就会注意到代码和硬盘上数据不匹配。 每当这时，您必须进行数据迁移，否则当您试图打开这个文件的话 Realm 就会抛出错误。

（2）如何进行数据迁移
假设我们想要把上面所声明 Person 数据模型进行迁移。如下所示是最简单的数据迁移的必需流程：
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
// 在(application:didFinishLaunchingWithOptions:)中进行配置

let config = Realm.Configuration(
  // 设置新的架构版本。这个版本号必须高于之前所用的版本号
  // （如果您之前从未设置过架构版本，那么这个版本号设置为 0）
  schemaVersion: 1,

  // 设置闭包，这个闭包将会在打开低于上面所设置版本号的 Realm 数据库的时候被自动调用
  migrationBlock: { migration, oldSchemaVersion in
    // 目前我们还未进行数据迁移，因此 oldSchemaVersion == 0
    if (oldSchemaVersion < 1) {
      // 什么都不要做！Realm 会自行检测新增和需要移除的属性，然后自动更新硬盘上的数据库架构
    }
  })

// 告诉 Realm 为默认的 Realm 数据库使用这个新的配置对象
Realm.Configuration.defaultConfiguration = config

// 现在我们已经告诉了 Realm 如何处理架构的变化，打开文件之后将会自动执行迁移
let realm = Realm()
虽然这个迁移操作是最精简的了，但是我们需要让这个闭包能够自行计算新的属性（这里指的是 fullName），这样才有意义。 在迁移闭包中，我们能够调用Migration().enumerate(_:_:) 来枚举特定类型的每个 Object 对象，然后执行必要的迁移逻辑。注意，对枚举中每个已存在的 Object 实例来说，应该是通过访问 oldObject 对象进行访问，而更新之后的实例应该通过 newObject 进行访问：
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
// 在 application(application:didFinishLaunchingWithOptions:) 中进行配置

Realm.Configuration.defaultConfiguration = Realm.Configuration(
  schemaVersion: 1,
  migrationBlock: { migration, oldSchemaVersion in
    if (oldSchemaVersion < 1) {
      // enumerate(_:_:) 方法遍历了存储在 Realm 文件中的每一个“Person”对象
      migration.enumerate(Person.className()) { oldObject, newObject in
        // 将名字进行合并，存放在 fullName 域中
        let firstName = oldObject!["firstName"] as! String
        let lastName = oldObject!["lastName"] as! String
        newObject!["fullName"] = "\(firstName) \(lastName)"
      }
    }
  })

18，使用带有 REST API 功能的 Realm 数据库示例
我们将从 豆瓣FM的API 那里获取一组 JSON 格式的频道数据，然后将它以 Realm Objects 的形式储存到默认的 Realm 数据库里。
（1）json数据格式如下：
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
{
 "channels": [
        {
            "name_en": "Personal Radio",
            "seq_id": 0,
            "abbr_en": "My",
            "name": "私人兆赫",
            "channel_id": 0
        },
        {
            "name": "华语",
            "seq_id": 0,
            "abbr_en": "",
            "channel_id": "1",
            "name_en": ""
        },
        {
            "name": "欧美",
            "seq_id": 1,
            "abbr_en": "",
            "channel_id": "2",
            "name_en": ""
        }
    ]
}
（2）我们将直接把 NSDictionary 插入到Realm中，然后让 Realm 自行快速地将其映射到 Object 上。
（从iOS9起，新特性要求App访问网络请求，要采用 HTTPS 协议。直接请求HTTP数据会报错，解决办法可以参照的我另一篇文章：Swift - 网络请求报App Transport Security has blocked a cleartext错）
为了确保示例能够成功，我们需要一个所有属性完全匹配 JSON 键结构 的Object 结构体。如果 JSON 的键结构不匹配 Object 结构体属性结构的话，那么就会在插入时被忽略。
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
import UIKit
import RealmSwift

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // 调用API
        let url = NSURL(string: "http://www.douban.com/j/app/radio/channels")!
        let response = NSData(contentsOfURL: url)!

        // 对 JSON 的回应数据进行反序列化操作
        let json = try! NSJSONSerialization.JSONObjectWithData(response,
            options: .AllowFragments)
        let channels = (json as! NSDictionary)["channels"] as! [NSDictionary]

        let realm = try! Realm()
        realm.write {
            // 为数组中的每个元素保存一个对象（以及其依赖对象）
            for channel in channels {
                if channel["seq_id"] as! Int == 0 {continue} //第一个频道数据有问题,丢弃三
                realm.create(DoubanChannel.self, value: channel, update: true)
            }
        }

        print(realm.path)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
}

//豆瓣频道
class DoubanChannel:Object {
    //频道id
    dynamic var channel_id = ""
    //频道名称
    dynamic var name = ""
    //频道英文名称
    dynamic var name_en = ""
    //排序
    dynamic var seq_id = 0
    dynamic var abbr_en = ""

    //设置主键
    override static func primaryKey() -> String? {
        return "channel_id"
    }
}

（3）可以看到数据已经成功插入到库中了

19，当前版本的限制
Realm 致力于平衡数据库读取的灵活性和性能。为了实现这个目标，在 Realm 中所存储的信息的各个方面都有基本的限制。例如：
（1）类名称的长度必须在 0 和 63 字节之间。支持 UTF8 字符。如果超出了这个限制，应用程序的初始化将抛出异常。
（2）属性名称的长度必须在 0 和 63 字节之间。支持 UTF8 字符。如果超出了这个限制，应用程序的初始化将抛出异常。
（3）NSData 不能保存超过 16 MB 大小的数据。如果要存储大量的数据，可通过将其分解为16MB 大小的块，或者直接存储在文件系统中，然后将文件路径存储在 Realm 中。如果您的应用试图存储一个大于 16MB 的单一属性，系统将在运行时抛出异常。
（4）NSDate 的存储精度只能到秒。参考NSDate 当前的局限可查看更多相关信息。
（5）任何一个 Realm 文件的大小也不能超过 iOS 应用所允许的内存空间总量。这个值会根据不同设备和当时的内存使用情况有所不同(这里又一个关于相关方面的讨论: rdar://17119975)如果您想存储更大的数据，请将数据映射到多个文件中。

原文出自：www.hangge.com  转载请保留原文链接：http://www.hangge.com/blog/cache/detail_891.html
