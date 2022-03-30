

# OC面试题 
### 初级
作者：张文广
#### 1、深拷贝和浅拷贝的理解？
 >深拷贝拷贝的是内容，浅拷贝拷贝的是指针。深拷贝和浅拷贝最大的区别就是子类对象的地址是否改变，如果子类对象的地址改变那么就是深拷贝。
 
#### 2、Objective-C的类可以多重继承么？可以实现多个接口么？Category是什么？重写一个类的方式用继承好还是分类好？为什么？
 >Objective-C的类不可以多重继承；可以实现多个接口（协议）；Category是类别；一般情况用分类好，用Category去重写类的方法，仅对本Category有效，不会影响到其他类与原有类的关系。

#### 3、属性关键字 readwrite，readonly，assign，retain，copy，nonatomic 各是什么作用，在
>那种情况下用？
1、readwrite 是可读可写特性。需要生成getter方法和setter方法。
2、readonly 是只读特性。只会生成getter方法，不会生成setter方法，不希望属性在类外改变。
3、assign 是赋值特性。setter方法将传入参数赋值给实例变量;仅设置变量时,assign用于基本数据类型。
4、retain(MRC)/strong(ARC) 表示持有特性。setter方法将传入参数先保留，再赋值，传入参数的retaincount会+1。
5、copy 表示拷贝特性。setter方法将传入对象复制一份，需要完全一份新的变量时。
6、nonatomic 非原子操作。决定编译器生成的setter和getter方法是否是原子操作，atomic表示多线程安全，一般使用nonatomic，效率高。 

#### 4、什么情况使用 weak 关键字，相比 assign 有什么不同？
>1、在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性。
2、自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。
IBOutlet连出来的视图属性为什么可以被设置成weak?
    因为父控件的subViews数组已经对它有一个强引用。
不同点：
assign 可以用非 OC 对象，而 weak 必须用于 OC 对象。
weak 表明该属性定义了一种“非拥有关系”。在属性所指的对象销毁时，属性值会自动清空(nil)。

#### 5、常见的 Objective-C 的数据类型有那些，和C的基本数据类型有什么区别？如：NSInteger和int
>Objective-C的数据类型有NSString，NSNumber，NSArray，NSMutableArray，NSData等等，这些都是class，创建后便是对象，而C语言的基本数据类型int，只是一定字节的内存空间，用于存放数值;NSInteger是基本数据类型，并不是NSNumber的子类，当然也不是NSObject的子类。NSInteger是基本数据类型Int或者Long的别名(NSInteger的定义typedef long NSInteger)，它的区别在于，NSInteger会根据系统是32位还是64位来决定是本身是int还是long。

#### 6、讲讲OC中的反射机制？简单聊一下概念和使用
>1、class反射
    通过类名的字符串形式实例化对象。
    将类名变为字符串。
2、SEL的反射
    通过方法的字符串形式实例化方法。
    将方法变成字符串。
#### 7、delegate 和 notification 的区别
>1、二者都用于传递消息，不同之处主要在于一个是一对一的，另一个是一对多的。
2、notification通过维护一个array，实现一对多消息的转发。
3、delegate需要两者之间必须建立联系，不然没法调用代理的方法；notification不需要两者之间有联系。

#### 8、RunLoop的作用是什么？它的内部工作机制是什么  *

>字面意思是“消息循环、运行循环”，runloop内部实际上就是一个do-while循环，它在循环监听着各种事件源、消息，对他们进行管理并分发给线程来执行。
>1、通知观察者将要进入运行循环。线程和 RunLoop 之间是一一对应的
>2、通知观察者将要处理计时器。
3、通知观察者任何非基于端口的输入源即将触发。
4、触发任何准备触发的基于非端口的输入源。
5、如果基于端口的输入源准备就绪并等待触发，请立即处理该事件。转到第9步。
6、通知观察者线程即将睡眠。
7、将线程置于睡眠状态，直到发生以下事件之一:事件到达基于端口的输入源。计时器运行。为运行循环设置的超时值到期。运行循环被明确唤醒。
8、通知观察者线程被唤醒。
9、处理待处理事件 如果触发了用户定义的计时器，则处理计时器事件并重新启、循环。转到第2步。如果输入源被触发，则传递事件。如果运行循环被明确唤醒但尚未超时，请重新启动循环。转到第2步。
>10、 通知观察者运行循环已退出。

#### 9、讲一下oc的消息机制

> 1、当向一个对象发送消息时，objc_msgSend 方法根据对象的 isa 指针找到对象的类，然后在类的调度表（dispatch table）中查找 selector。2、如果无法找到 selector，objc_msgSend 通过指向父类的指针找到父类，并在父类的调度表（dispatch table）中查找 selector，以此类推直到 NSObject 类。3、一旦查找到 selector，objc_msgSend 方法根据调度表的内存地址调用该实现。4、通过这种方式，message 与方法的真正实现才在执行阶段进行绑定。5、为了保证消息发送与执行的效率，系统会将全部 selector 和使用过的方法的内存地址缓存起来。6、每个类都有一个独立的缓存，缓存包含有当前类自己的 selector 以及继承自父类的 selector。7、查找调度表（dispatch table）前，消息发送系统首先检查 receiver 对象的缓存；缓存命中的情况下，消息发送（messaging）比直接调用方法（function call）只慢一点点

#### 10、动态绑定

> 1、在运行时确定要调用的方法，动态绑定将调用方法的确定也推迟到运行时。2、在编译时，方法的调用并不和代码绑定在一起，只有在消实发送出来之后，才确定被调用的代码。3、通过动态类型和动态绑定技术，您的代码每次执行都可以得到不同的结果。4、运行时负责确定消息的接收者和被调用的方法；运行时的消息分发机制为动态绑定提供支持。5、当您向一个动态类型确定了的对象发送消息时，运行环境系统会通过接收者的 isa 指针定位对象的类，并以此为起点确定被调用的方法，方法和消息是动态绑定的。而且，您不必在 Objective-C 代码中做任何工作，就可以自动获取动态绑定的好处。6、您在每次发送消息时，特别是当消息的接收者是动态类型已经确定的对象时，动态绑定就会例行而透明地发生。
> 
#### 11、怎么实现多个网络请求完成后执行下一步
>1、使用GCD的dispatch_group_t
每次网络请求前先dispatch_group_enter（进入）,请求回调后再dispatch_group_leave（离开），enter和leave必须配合使用，有几次enter就要有几次leave，否则group会一直存在。
当所有enter的block都leave后，会执行dispatch_group_notify的block。
2、使用GCD的信号量dispatch_semaphore_t
#### 12、链表和数组有什么区别 

>存储形式：数组是一块连续的空间，声明时就要确定长度。链表是一块可不连续的动态空间，长度可变，每个节点要保存相邻结点指针；
2、数据查找：数组的线性查找速度快，查找操作直接使用偏移地址。链表需要按顺序检索结点，效率低；
3、数据插入或删除：链表可以快速插入和删除结点，而数组则可能需要大量数据移动；
4、越界问题：链表不存在越界问题，数组有越界问题。
数组便于查询，链表便于插入删除。数组节省空间但是长度固定，链表虽然变长但是占了更多的存储空间。

#### 13、layoutsubviews是在什么时机调用的？

>1、init初始化不会触发。
2、addSubview时。
3、设置frame且前后值变化，frame为zero且不添加到指定视图不会触发。
4、旋转Screen会触发父视图的layoutSubviews。
5、滚动UIScrollView引起View重新布局时会触发layoutSubviews。
	
	
#### 14、id类型, nil , Nil ,NULL和NSNULL的区别?

> 1、id类型: 是一个独特的数据类型，可以转换为任何数据类型，id类型的变量可以存放任何数据类型的对象，在内部处理上，这种类型被定义为指向对象的指针，实际上是一个指向这种对象的实例变量的指针; id 声明的对象具有运行时特性，既可以指向任意类型的对象
2、nil 是一个实例对象值;如果我们要把一个对象设置为空的时候,就用nil
3、Nil 是一个类对象的值,如果我们要把一个class的对象设置为空的时候,就用Nil
4、NULL 指向基本数据类型的空指针(C语言的变量的指针为空)
5、NSNull 是一个对象,它用在不能使用nil的场合

#### 15、sprintf,strcpy,memcpy使用上有什么要注意的地方 

>strcpy是一个字符串拷贝的函数，它的函数原型为strcpy(char *dst, c*****t char *src); 
将 src开始的一段字符串拷贝到dst开始的内存中去，结束的标志符号为'\0'，由于拷贝的长度不是由我们自己控制的，所以这个字符串拷贝很容易出错。具 备字符串拷贝功能的函数有memcpy，这是一个内存拷贝函数，它的函数原型为memcpy(char *dst, c*****t char* src, unsigned int len); 
将长度为len的一段内存，从src拷贝到dst中去，这个函数的长度可控。但是会有内存叠加的问题。 
sprintf是格式化函数。将一段数据通过特定的格式，格式化到一个字符串缓冲区中去。sprintf格式化的函数的长度不可控，有可能格式化后的字符串会超出缓冲区的大小，造成溢出。

#### 16、指针与地址的区别? 

> 1指针意味着已经有一个指针变量存在,他的值是一个地址,指针变量本身也存放在一个长度为四个字节的地址当中,而地址概念本身并不代表有任何变量存在、 
2 指针的值,如果没有限制,通常是可以变化的,也可以指向另外一个地址、 
   地址表示内存空间的一个位置点,他是用来赋给指针的,地址本身是没有大小概念,指针指向变量的大小,取决于地址后面存放的变量类型、 

#### 17、 指针的类型转换?

> 指针转换通常是指针类型和void * 类型之前进行强制转换,从而与期望或返回void指针的函数进行正确的交接、 

#### 18、#import和#include的区别是什么？＃import<> 跟 #import""有什么区别？

>#import能避免头文件被重复包含的问题:
1、一般来说，导入objective c的头文件时用#import，包含c/c++头文件时用#include。
使用include要注意重复引用的问题：
class A，class B都引用了class C，class D若引用class A与class B,就会报重复引用的错误。
2、#import 确定一个文件只能被导入一次，这使你在递归包含中不会出现问题。
所以，#import比起#include的好处就是它避免了重复引用的问题。所以在OC中我们基本用的都是import。
＃import<> 包含iOS框架类库里的类，#import""包含项目里自定义的类。

#### 19、 id声明的对象有什么特性？
 >没有 * 号
  动态数据类型
  可以指向任何类的对象(设置是nil)，而不关心其具体类型
  在运行时检查其具体类型
  可以对其发送任何（存在的）消息

#### 20、内存管理的几条原则是什么？按照默认法则，哪些关键字生成的对象需要手动释放？哪些情况下不需要手动释放，会直接进入自动释放池？
>1、当使用new、alloc或copy方法创建一个对象时，该对象引用计数器为1。如果不需要使用该对象，可以向其发送release或autorelease消息，在其使用完毕时被销毁。
>2、 如果通过其他方法获取一个对象，则可以假设这个对象引用计数为1，并且被设置为autorelease，不需要对该对象进行清理，如果确实需要retain这个对象，则需要使用完毕后release。
>3、如果retain了某个对象，需要release或autorelease该对象，保持retain方法和release方法使用次数相等。
使用new、alloc、copy关键字生成的对象和retain了的对象需要手动释放。设置为autorelease的对象不需要手动释放，会直接进入自动释放池。

#### 21、iOS中有哪些回调机制，并作简单的比较。
>各种回调机制的比较：
1、目标动作对：当两个对象之间有比较紧密的关系时，如视图控制器与其下的某个视图。
2、代理：也叫委托，当某个对象收到多个事件，并要求同一个对象来处理所有事件时。委托机制依赖于某个协议定义的方法来发送消息。
3、通告机制：当需要多个对象或两个无关对象处理同一个事件时。
4、Block：适用于回调只发生一次的简单任务。

#### 22、列举几种进程的同步机制，并比较其优缺点。
>答案：原子操作、信号量机制、自旋锁、管程、会合、分布式系统
进程之间通信的途径
答案：共享存储系统消息传递系统管道：以文件系统为基础
进程死锁的原因
答案：资源竞争及进程推进顺序非法
死锁的4个必要条件
答案：互斥、请求保持、不可剥夺、环路
死锁的处理
答案：鸵鸟策略、预防策略、避免策略、检测与解除死锁

#### 23、c和obj-c如何混用
>1、obj-c的编译器处理后缀为m的文件时，可以识别obj-c和c的代码，处理mm文件可以识别obj-c,c,c++代码，但cpp文件必须只能用c/c++代码，而且cpp文件include的头文件中，也不能出现obj-c的代码，因为cpp只是cpp
>2、 在mm文件中混用cpp直接使用即可，所以obj-c混cpp不是问题
>3、在cpp中混用obj-c其实就是使用obj-c编写的模块是我们想要的。 如果模块以类实现，那么要按照cpp class的标准写类的定义，头文件中不能出现obj-c的东西，包括#import cocoa的。实现文件中，即类的实现代码中可以使用obj-c的东西，可以import,只是后缀是mm。 如果模块以函数实现，那么头文件要按c的格式声明函数，实现文件中，c++函数内部可以用obj-c，但后缀还是mm或m。
总结：只要cpp文件和cpp include的文件中不包含obj-c的东西就可以用了，cpp混用obj-c的关键是使用接口，而不能直接使用实现代码，实际上cpp混用的是obj-c编译后的o文件，这个东西其实是无差别的，所以可以用。obj-c的编译器支持cpp、

#### 24、原子(atomic)跟非原子(non-atomic)属性有什么区别?

>1、 atomic提供多线程安全。是防止在写未完成的时候被另外一个线程读取，造成数据错误
>2、 non-atomic:在自己管理内存的环境中，解析的访问器保留并自动释放返回的值，如果指定了 nonatomic ，那么访问器只是简单地返回这个值。

#### 25、请简要说明viewDidLoad和viewDidUnload何时调用
>viewDidLoad在view从nib文件初始化时调用，loadView在controller的view为nil时调用。此方法在编程实现view时调用，view控制器默认会注册memory warning notification，当view controller的任何view没有用的时候，viewDidUnload会被调用，在这里实现将retain的view release，如果是retain的IBOutlet view 属性则不要在这里release，IBOutlet会负责release 。

#### 26、 _block和__weak修饰符的区别？
>__block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。
__weak只能在ARC模式下使用，也只能修饰对象，不能修饰基本数据类型。
__block对象可以在block中被重新赋值，__weak不可以。
#### 27、进程间通信方式？线程间通信？

>1、URL scheme  这个是iOS APP通信最常用到的通信方式，APP1通过openURL的方法跳转到APP2，并且在URL中带上想要的参数，有点类似HTTP的get请求那样进行参数传递。这种方式是使用最多的最常见的，使用方法也很简单只需要源APP1在info、plist中配置LSApplicationQueriesSchemes,指定目标App2的scheme；然后再目标App2的info、plist 中配置好URLtypes，表示该App接受何种URL scheme的唤起。
2、Keychain  iOS 系统的keychain是一个安全的存储容器，它本质上就是一个sqlite数据库，它的位置存储在/private/var/Keychains/keychain-2、db,不过它索八坪村的所有数据都是经过加密的，可以用来为不同的APP保存敏感信息，比如用户名，密码等。iOS系统自己也用keychain来保存VPN凭证和WiFi密码。它是独立于每个APP的沙盒之外的，所以即使APP被删除之后，keychain里面的信息依然存在

#### 28、block数据结构和变量捕获

>1、对于全局变量，block不会捕获，通过全局变量访问。
2、对于局部变量，auto自动变量将会捕获，且是值传递。
3、对于局部变量，static变量将会捕获，且是指针传递。

#### 29、load与initialize在分类、继承链的调用顺序

> 一、load方法调用顺序
父类->主类->分类
1、主类的 +load 方法会在它的所有父类的 +load 方法之后执行。如果主类没有实现 +load 方法，当它被runtime加载时 是不会去调用父类的 +load 方法的。
 2、分类的 +load 方法会在它的主类的 +load 方法之后执行,当一个类和它的分类都实现了 +load 方法时，两个方法都会被调用。当有多个分类时，根据编译顺序（Build Phases->Complie Sources中的顺序）依次执行。
3、在类的+load方法调用的时候，可以调用category中声明的方法么？ 可以调用，因为附加category到类的工作会先于+load方法的执行

>二、initialize的调用顺序
+initialize 方法的调用与普通方法的调用是一样的，走的都是消息发送的流程。如果子类没有现+initialize 方法，那么继承自父类的实现会被调用；如果一个类的分类实现了+initialize 方法，那么就会对这个类中的实现造成覆盖。
3、确保在load和initialize的调用只执行一次
由于initialize可能会调用多次，所以在这两个方法里面做的初始化操作需要保证只初始化一次，用dispatch_once来控制

#### [OC面试题及知识点](https://github、com/iOS-Mayday/heji)

# Swift 面试题
## 语言
### 高级
#### 1、OC与Swift混编

   * OC调用swift：import "工程名-swift、h” @objc
   * Swift调用oc：桥接文件

#### 2、下面代码有什么问题 雷哥更新
```swift
    public class Node {
         public var value: Int
         public var prev: Node?
         public var post: Node?
     
        public init(_ value: Int) {
             self、value = value
        }
    }
```
答案：应该在 var prev 或者 var post 前面加上 weak。 或者换成struct

原因：表面上看，以上代码毫无问题。但是我这样一写，问题就来了：
```swift
    let head = Node(0)
    let tail = Node(1)
    head、post = tail
    tail、prev = head
```

此时，head 和 tail 互相指向，形成循环引用（retain cycle）。
### 中级

#### 1、Swift在protocol中定义方法，参数可以有默认值么？

可以通过扩展实现

#### 2、Swift中， 怎么实现一个递归枚举？

关键字 indirect

```swift
    enum List1<T> {
        case end
        indirect case Node(T, List1)
    }
```
#### 3、Swift中， 怎么实现逆序遍历？

1）while 循环
2）stride 
如： 
```swift
     for i in stride(from: 3, through: 0, by: -1) {
             print(i)
     }
```

#### 4、Swift中， 集合延迟序列

使用 lazy

```swift
    var nums = [1, 2, 3]
    var result = nums、lazy、map { String($0) }
```

#### 5、Swift 怎么协议合成

有时候需要同时遵循多个协议，例如一个函数希望某个参数同时满足ProtocolA和ProtocolB，我们可以采用 ProtocolA & ProtocolB 这样的格式进行组合，称为 协议合成（protocol composition）。

#### 6、inout 的作用?
在Swift中，除了class是默认引用传递外，其他数据类型如float，struct等等都属于值传递。如果我们在对其进行处理的时候希望能够在函数中直接对其原值进行修改直接修改，那么最好的方法就是直接使用inout来修饰传入参数，值得注意的是inout 无法修饰带有默认值的参数且经过inout修饰之后，无法再被let和var修饰。

Swift 怎么实现一个可变参数函数?
如果想要一个可变参数的函数只需要在声明参数时在类型后面加上 、、、 就可以了。
如：
```swift
    func sum(inout input: Int、、、) -> Int {
        return input、reduce(0, +)
    }
    
    print(sum(input: 1,2,3,4,5))
```

#### 7、 dynamic 的作用

由于 swift 是一个静态语言, 所以没有 Objective-C 中的消息发送这些动态机制, dynamic 的作用就是让 swift 代码也能有 Objective-C 中的动态机制, 常用的地方就是 KVO 了, 如果要监控一个属性, 则必须要标记为 dynamic

#### 8、 throws 和 rethrows 的用法与作用

* throws 用在函数上, 表示这个函数会抛出错误、有两种情况会抛出错误, 一种是直接使用 throw 抛出, 另一种是调用其他抛出异常的函数时, 直接使用 try xx 没有处理异常、
* rethrows 与 throws 类似, 不过只适用于参数中有函数, 且函数会抛出异常的情况, rethrows 可以用 throws 替换, 反过来不行

#### 9、Swift内存管理

跟OC一样，Swift也是采取基于引用计数的ARC内存管理方案(针对堆空间) 
Swift的ARC中有3种引用 
* 强引用(strong reference):默认情况下，引用都是强引用 
* 弱引用(weak reference):通过weak定义弱引用必须是可选类型的var，因为实例销毁后，ARC会自动将弱引用设置为nil ，ARC自动给弱引用设置nil时，不会触发属性观察器 
* 无主引用(unowned reference):通过unowned定义无主引用不会产生强引用，实例销毁后仍然存储着实例的内存地址(类似于OC中的unsafe_unretained)，试图在实例销毁后访问无主引用，会产生运行时错误(野指针)

#### 10、减少使用Any/AnyObject的意义

因为Any/AnyObject缺少明确的类型信息，编译器无法进行类型检查，会带来一些问题：
编译器无法检查类型是否正确保证类型安全
代码中大量的as?转换
类型的缺失导致编译器无法做一些潜在的编译优化

#### 11、项目中错误处理方式有哪些

*  可选值 - 调用方并不关注内部可能会发生错误，当发生错误时返回nil
*   try/catch - 明确提示调用方需要处理异常，需要实现Error协议定义明确的错误类型
*   assert - 断言。只能在Debug模式下生效
*  precondition - 和assert类似，可以再Debug/Release模式下生效
*   fatalError - 产生运行时崩溃会导致Crash，应避免使用
*   Result - 通常用于闭包异步回调返回值

#### 12、 避免使用Objc类型的意义

尽可能避免在Swift中使用NSString/NSArray/NSDictionary等ObjC基础类型。以Dictionary为例，虽然Swift Runtime可以在NSArray和Array之间进行隐式桥接需要O(1)的时间。但是字典当Key和Value既不是类也不是@objc协议时，需要对每个值进行桥接，可能会导致消耗O(n)时间。

#### 13、减少添加@objc标识的意义

@objc标识虽然不会强制使用消息转发的方式来调用方法/属性，但是他会默认ObjC是可见的会生成和ObjC一样的ro_data_t结构。

#### 14、避免使用@objcMembers的意义

使用@objcMembers修饰的类，默认会为类/属性/方法/扩展都加上@objc标识。
你也可以使用@nonobjc取消支持ObjC。

#### 15、CoreData栈的组成部分

* 托管对象 (managed objects)(NSManagedObject)，
* 托管对象上下文 (managed object context)(NSManagedObjectContext)，
* 持久化存储协调器 (persistent store coordinator)(NSPersistentStoreCoordinator)
* 持久化存储 (persistent store) (NSPersistentStore)

#### 16、实现俩个对象的交换（考察元组）

普通写法
```swift
    func swapMe1<T>( a: inout T, b: inout T) {
                 let temp = a
                 a=b 
                 b = temp 
    } 
```

元组写法
```swift
    func swapMe2<T>( a: inout T, b: inout T) {
        (a,b) = (b,a)
    }
```
#### 17、iOS中调试p和po的区别

* p 命令是 print 命令的简写，使用p 命令可以查看基本数据类型的值，但是如果 使用 p 命令 查看的是对象，那么只会返回对象的指针地址。
* po 命令可以理解为打印对象。功能与 p 命令类似，所以也是可以打印 常量、变量，打印表达式返回的对象等

#### 18、实现一个两数：求0~100（包括0和 100）中为偶数并且恰好是其他数字平方的数字
最简单、粗暴的写法如下：
```swift
func evenSquareNums (from: Int, to: Int) -> [Int] {
    var res = [Int]()
    for num in from、、、to where num % 2 == O {
        if (from、、、to)、contains (num * num) {
        res、 append (num * num)
        }
    }
    return res
}
evenSquareNums (from: 0, to: 100)
```
用函数式编程的思路，
一行代码即可解决问题：
```swift
(0、、、10)、map{ $0 * $0 }、filter{ $0 % 2 == 0}
```

#### 19、如何将一个结构体和类存储到数据库？（codable）

   使用codable协议 进行编解码实现数据存储

#### 20、讲讲Swift的派发机制

 * 函数的派发机制：静态派发（直接派发）、函数表派发、消息派发
 * Swift派发机制总结：

    Swift中所有ValueType（值类型：Struct、Enum）使用直接派发；
    Swift中协议的Extensions使用直接派发，初始声明函数使用函数表派发；
    Swift中Class中Extensions使用直接派发，初始声明函数使用函数表派发，dynamic修饰的函数使用消息派发；
    Swift中NSObject的子类用@nonobjc或final修饰的函数使用直接派发，初始声明函数使用函数表派发，dynamic修饰的Extensions使用消息派发；

 * Swift中函数派发查看方式: 可将Swift代码转换为SIL（中间码）
swiftc -emit-silgen -O example、swift

#### 21、Swift如何显式指定派发方式？

 * 添加final关键字的函数使用直接派发
 * 添加static关键字函数使用直接派发
 * 添加dynamic关键字函数使用消息派发
 * 添加@objc关键字的函数使用消息派发
 * 添加@inline关键字的函数会告诉编译器可以使用直接派发

#### 22、实现一个函数，任意输入两个整数实现返回两个数相加的结果或相减的结果。
理想写法（函数嵌套）：
```swift
    func operate(with symbol:String) -> (Int, Int) -> Int {
        func add(num1:Int, num2:Int) -> Int {
            return num1 + num2
        }
        func subtract(num1:Int, num2:Int) -> Int {
            return num1 - num2
        }
        let operation = (symbol == "+") ?  add : subtract
        return operation
    }
    let operation = operate(with: "+")
    let result = operation(10, 20)
    print(result)
```
精简一下写法
```swift
    func divide(dividend: Double?, by divisor: Double?) -> Double? { 
          if dividend == nil { 
            return nil 
          }  
          if divisor == nil { 
            return nil 
          } 
          if divisor == 0 { 
            return nil
          }  
          return dividend! / divisor!
    }
```

这题考察的是guard let语句以及optional chaining，最佳答案是:
```swift
    func divide(dividend: Double?, by divisor: Double?) -> Double? { 
        guard let dividend = dividend, let divisor = divisor, divisor != 0 else {
            return nil
        }
    
        return dividend / divisor
    }
```

#### 23、以下函数会打印出什么？
```swift
    var car = "Benz" 
    let closure = { [car] in 
      print("I drive \(car)")
    } 
    car = "Tesla" 
    closure()
```
因为 clousre已经申明将car 复制进去了（[car]），此时clousre 里的 car是个局部变量，不再与外面的car有关，所以会打印出”I drive Benz”。
如果将题目略作修改如下会输出什么：
```swift
    var car = "Benz" 
    let closure = {
      print("I drive \(car)")
    } 
    car = "Tesla" 
    closure()
```
此时 closure没有申明复制拷贝car，所以clousre 用的还是全局的 car 变量，此时将会打印出 “I drive Tesla”

#### 24、以下代码会打印出什么？
```swift
    protocol Pizzeria { 
      func makePizza(_ ingredients: [String])
      func makeMargherita()
    } 
    
    extension Pizzeria { 
      func makeMargherita() { 
        return makePizza(["tomato", "mozzarella"]) 
      }
    }
    
    struct Lombardis: Pizzeria { 
      func makePizza(_ ingredients: [String]) { 
        print(ingredients)
      } 
      func makeMargherita() {
        return makePizza(["tomato", "basil", "mozzarella"]) 
      }
    }
    
    let lombardis1: Pizzeria = Lombardis()
    let lombardis2: Lombardis = Lombardis() 
    lombardis1、makeMargherita()
    lombardis2、makeMargherita()
```
答案：打印出如下两行
```swift
    ["tomato", "basil","mozzarella"]
    ["tomato", "basil", "mozzarella"]
```
在Lombardis的代码中，重写了makeMargherita的代码，所以永远调用的是Lombardis中的 makeMargherita。
再进一步，我们把 protocol Pizzeria中的 func makeMargherita()删掉，代码变为
```swift
    protocol Pizzeria {
      func makePizza(_ ingredients: [String])
    }
    
    extension Pizzeria {
      func makeMargherita() {
        return makePizza(["tomato", "mozzarella"])
      }
    }
    
    struct Lombardis: Pizzeria {
      func makePizza(_ ingredients: [String]) {
        print(ingredients)
      }
      func makeMargherita() {
        return makePizza(["tomato", "basil", "mozzarella"])
      }
    }
    
    let lombardis1: Pizzeria = Lombardis()
    let lombardis2: Lombardis = Lombardis()
    lombardis1、makeMargherita()
    lombardis2、makeMargherita()

```

这时候打印出如下结果：
```swift
    ["tomato", "mozzarella"]
    ["tomato", "basil", "mozzarella"]
```
因为lombardis1是Pizzeria，而 makeMargherita()有默认实现，这时候我们调用默认实现

#### 25、快速排序
```swift
    func quickSort(array:[Int])->[Int] {
        if array、count < 2 {
            return array
        } else {
            let midValue = array[0]
            let lessArr:[Int] =  array、filter { $0 < midValue }
            let moreArr:[Int] =  array、filter { $0 > midValue }
            return quickSort(array: lessArr) + [midValue] + quickSort(array:moreArr)
        }
    }
```
quickSort(array: list);

### 初级
#### 1、Swift的单例写法

* 非继承NSObject
不继承自 NSObject 的类没有 copy()、mutableCopy() 方法，不需要重载。
如: public class Test
注意点：
```swift
    // 确保类只有一个实例,不应该在外部初始化
    private init() {}
``` 
* 严格单例模式
如: public class Test
注意点：
```swift
    // 确保类只有一个实例,不应该在外部初始化
     
    private init() {}
    
    override func copy() -> Any {
        return self 
    }
    
    override func mutableCopy() -> Any {
        return self 
    }
```
#### 2、Swift如何定义可选的protocol属性或者方法？
```swift
     @objc protocol Refreshable {
        @objc optional func test()
     }
``` 
#### 3、Swift如何在protocol中定义属性？

protocol中定义属性，必须明确指定该属性支持的操作：只读（get）或者是可读写（get set）
如：
```swift
    var index: Int { set get }
```
#### 4、Swift中 weak和unowned 的区别

两者都是用来解决循环引用的。
* weak 必须设置为可选值， 如果修饰self已释放不会 Crash
* unowned 可以设置为可选值，如果修饰self已释放会 Crash

#### 5、怎么实现一个 外部只读/内部可读写属性

使用private(set)
如：
```swift
    private(set) var num = 1
```
#### 6、某些方法使用方并不一定会处理返回值，怎么 Xcode 不进行warning提示。

使用关键字 @discardableResult

#### 7、Swift中，元组的写法

Swift支持把多个值组合成一个复合值，称为元组。元组内的值可以是任意类型，并不要求是相同类型
(Int, Int, Int)

#### 8、 什么是属性观察?

属性观察是指在当前类型内对特性属性进行监测,并作出响应,属性观察是 swift 中的特性,具有2种, willset 和 didset

```swift
    var title: String {
        willSet {
            print("willSet", newValue)
    
        }
        didSet {
            print("didSet", oldValue, title)
        }
    }
```
willSet会传递新值，默认叫newValue
didSet会传递旧值，默认叫oldValue
在初始化器中设置属性值不会触发willSet和didSet

#### 9、 swift 和OC 中的自省 有什么区别?

自省在OC中就是判断某一对象是否属于某一个类的操作,有以下2中方式

[obj iskinOfClass:[SomeClass class]]
[obj isMemberOfClass:[SomeClass class]]

在 Swift 中由于很多 class 并非继承自 NSObject, 故而 Swift 使用 is 来判断是否属于某一类型, is 不仅可以作用于class, 还是作用于enum和struct

#### 10、什么是函数重载? swift 支不支持函数重载?

函数重载是指: 函数名称相同,函数的参数个数不同, 或者参数类型不同,或参数标签不同, 返回值类型与函数重载无关
swift 支持函数重载

#### 11、什么是 Swift 枚举的关联值

如： 
```swift
    enum Date {
        case string(String)
    }
```
#### 12、什么是逃逸闭包?

当闭包作为一个实际参数传递给一个函数或者变量的时候，我们就说这个闭包逃逸了，可以在形式参数前写 @escaping 来明确闭包是允许逃逸的。

#### 13、访问控制关键字 open, public, internal, fileprivate, private 的区别?

Swift 中有个5个级别的访问控制权限,从高到低依次是 open, public, internal,fileprivate, private 它们遵循的基本规则: 高级别的变量不允许被定义为低级别变量的成员变量,比如一个 private 的 class 内部允许包含 public的 String值,反之低级变量可以定义在高级别变量中;
* open: 具备最高访问权限,其修饰的类可以和方法,可以在任意 模块中被访问和重写、
* public: 权限仅次于 open，和 open 唯一的区别是: 不允许其他模块进行继承、重写
* internal: 默认权限, 只允许在当前的模块中访问，可以继承和重写,不允许在其他模块中访问
* fileprivate: 修饰的对象只允许在当前的文件中访问;
* private: 最低级别访问权限,只允许在定义的作用域内访问

#### 14、popLast 和 removeLast 的区别

* removeLast() -  删除最后一个元素，在当前数组基础上操作，不返回结果，数组为空的时候直接崩溃
* popLast() - 删除最后一个元素，在当前数组基础上操作,在数组不为空时删除最后一个元素并返回它，在数组为空时，它将不执行 任何操作，直接返回 nil 
* 
#### 15、 Swift高级函数

* filter函数，过滤（筛选）不满足条件的元素，返回满足条件的元素组成数组。
* map可以对数组中的每一个元素做一次处理, 返回一个泛型的数组。
* reduce 把集合中所有的值结合起来返回一个新的值。

#### 16、泛型 T 与 Any及AnyObject有什么区别？该如何使用？

通常来讲,泛型为类或者方法提供一个类型参数,以方便某个参数类型保持前后的一致性。
概括来说AnyObject用于任何类(class)的实例,而Any可以用于表示任何变量,包括各种基本类型、值类型以及实例。而在swift中,枚举类型和结构体(例如Array和Dictionary)都属于值类型,因而不能用AnyObject来进行修饰。

#### 17、 String 与 NSString 的关系与区别

NSString 与 String 之间可以随意转换,
String 是结构体, 值类型, NSString 是类, 引用类型、
通常, 没必要使用 NSString 类, 除非你要使用一些特有方法, 例如使用 pathExtension 属性

#### 18、 where 关键字的常用使用场景

在Swift语法里where关键字的作用跟SQL的where一样， 即附加条件判断。
* 条件筛选
* 协议选择
* guard let 判断

#### 19、Swift实现可选协议

当协议中某些方法或属性不需要遵守协议的类型实现时，使用关键字optional来指明；协议和可选需求都必须用@objc属性标记；@objc协议只能由继承自Objective-C类或其他@objc类的类使用，结构体或枚举不能使用。
可以对协议进行扩展，在扩展中完成协议的实现，达到可选协议的效果

#### 20、 Swift extension

Swift中的扩展，有点类似于OC中的分类(Category) n 扩展可以为枚举、结构体、类、协议添加新功能 ,可以添加方法、计算属性、下标、(便捷)初始化器、嵌套类型、协议等等 

扩展不能办到的事情
* 不能覆盖原有的功能 
* 不能添加存储属性，不能向已有的属性添加属性观察器 
* 不能添加父类
* 不能添加指定初始化器，不能添加反初始化器

#### 21、 defer 使用场景

defer 语句块中的代码, 会在当前作用域结束前调用, 常用场景如异常退出后, 关闭数据库连接
需要注意的是, 如果有多个 defer, 那么后加入的先执行

#### 22、使用as?有可能带来的问题

当使用Any/AnyObject时会频繁使用as?进行类型转换。这好像没什么问题因为使用as?并不会导致程序Crash。不过代码错误至少应该分为两类，一类是程序本身的错误通常会引发Crash，另外一种是业务逻辑错误。使用as?只是避免了程序错误Crash，但是并不能防止业务逻辑错误。

#### 23、减少使用!进行强解包的意义

使用!强解包会在值不存在时产生运行时异常导致Crash。建议只在小范围的局部代码段使用!强解包。

#### 24、避免使用try!进行错误处理

使用try!会在方法抛出异常时产生运行时异常导致Crash。

#### 25、使用weak/unowned避免循环引用

* unowned在值不存在时会产生运行时异常导致Crash，只有在确定self一定会存在时才使用unowned。
* unowned/weak区别：weak - 必须设置为可选值，会进行弱引用处理性能更差。会自动设置为nil
unowned - 可以不设置为可选值，不会进行弱引用处理性能更好。但是不会自动设置为nil, 如果self已释放会触发错误、

#### 26、如何截取 String 的某段字符串

swift 中, 有三个取子串函数,
substring:to , substring:from, substring:with、

#### 27、Swift中常用的数据结构有哪些？

* 数组
* 字典
* 集合
* 字符串

#### 28、谈一谈值类型和引用类型

值类型在传递和赋值时将进行复制，而引用类型则只会使用引用对象的一个 "指向"。
值类型好处：相较于传统的引用类型来说，一个很显而易⻅的优势就是减少了堆上内存分配和回收的次数

#### 29、Swift 常用的高阶函数

* map
* filter
* reduce

#### 30、讲一讲 ！的使用时机

 * 当你能确定你的某个值不可能是 nil 时可以使用叹号。
 * 当它意外是 nil 的话，你希望程序直接挂掉。
 
#### 31、Swift 是面向对象还是函数式的编程语言

* Swift 既是面向对象的编程语言，又是函数式的编程语言。
* 说Swift是面向对象的编程语言，是因为 Swift 支持类的封装、继承和多态，从这一点来看，Swift 与 Java 这类纯面向对象的编程语言几乎毫无差别。
* 说Swift是的数式的编程语言，是因为Swift 支挂map、 reduce, filter, flatmap 这类去除中间状态、数学的函数式的方法，更加强调运算结果而不是中间过程。
 
#### 32、在Swift中如何理解copy-on-write

当值类型（比如struct）在复制时，复制的对象和原对象实际上在内存中指向同一个对象。
当且仅当修改复制后的对象时，才会在内存中重新创建一个新的对象。下面举一个例子：
```swift
    // arrayA 是一个数组，为值类型
    let arrayA=[1,2,3]
    //arrayB 这个时候与 arrayA 在内存中是同一个数组，内存中并没有生成新的数组
    let arrayB = arrayA
    // arrayB 被修改了，此时 arrayB 在内存中变成了一个新的数组，而不是原来的 arrayA
    arrayB、append(4)
```
从上面的代码中可以看出，复制的数组和原数组共享同一个地址，直到其中之一发生改变。
这样设计使得值类型可以被多次复制而无须耗费多余的内存，只有变化的时候才会增加开
销。因此内存的使用更加高效。

#### 33、如何给约束添加动画

约束更新后，使用layoutIfNeeded

#### 34、Swift 为什么将String,Array,Dictionary 设计成值类型

* 值类型相比引用类型，最大的优势在于可以高效地使用内存。值类型在栈上操作，引用类型在堆上操作。栈上的操作仅仅是单个指针的上下移动，而堆上的操作则牵涉合并、移位、重新链接等。也就是说，Swift 这样设计大幅减少了堆上的内存分配和回收的次数。同时，copy-on-write 又将值传递和复制的开销降到最低
* Swift 将 String, Array 和 Dictionary 设计成值类型也是为了线程安全。通过 swift 的let设置，使得这些数据达到了真正意义上的“不变”，也从根本上解决了多线程中内存访问和操作顺序的问题。
* Swift将 String， Array 和 Dictionary 设计成值类型还可以提升 API 的灵话度。例如，通过实现 Collection这样的协议，可以遍历 String，使得整个开发更加灵活、高效。

#### 35、结构体和类的使用场景

基本原则：要共享一个实例的所有权的话，我们必须使用类。否则，我们可以使用结构体。

#### 36、如何列举枚举值（）
使用CaseIterable，例： 
```swift
   enum City : CaseIterable {
        case : beijing = "Beijing"
        case : shanghai = "Shanghai"
        case : shenzhen = "Shenzhen"
   }
   City、allCases
```
#### 37、如何合并两个字符串数组 
   ```swift
   ["1","2"] + ["hello","swift","ok"]  
   ```
   考察Swift 运算符重载
#### 38、在Swift 中，什么是可选型 (Optional)？

在Swift中，可选型是为了表达当一个变量值为空的情况。当一个变量值为空时，它就是nil。 在Swift中，无论变量是引用类型还是值类型，都可以是可选型变量。下面举一个例子：
```swift
    // 值类型为 Float, value 默认值为 37、0
    var value: Float? = 37、0
    // 值类型为 String, key 默认值为 nil
    var key: String? = nil
    //引用类型为 UIImage, image 默认值为 nil
    let image: UIImage?
```
加分回答：
在Objective-C 中没有明确提出可选型的概念，然而，其引用类型却可以为nil，以此来标志其变量值为空的情况。而 Swift将这一理念扩大到值类型，并且明确提出了可选型的概念。

#### 39、在结构体中如何修改成员变量的方法？

请问下面的代码存在什么问题？
```swift
protocol Pet {
    var name: String { get set }
}
struct MyDog: Pet {
    var name: String
    func changeName (name: String) {
        self、name = name
    }
}
```
应该在 func changeName(name: String)的前面加上关键词mutating，表示该方法会修改结
构体中自己的成员变量。注意，在设计协议的时候，由于protocol可以被class 和struct 或enum 实现，故而要考虑是否用mutating 来修饰方法。类中不存在这个问题，因为类可以随意修改自己的成员变量。

#### 40、guard let 和if let 的区别

   作用域不同

#### 41、Swift和Objective-C有什么区别？

* Swift是强类型（静态）语言，有类型推断，Objective-C弱类型（动态）语言
* Swift面向协议编程，Objective-C面向对象编程
* Swift注重值类型，Objective-C注重引用类型
* Swift支持泛型，Objective-C只支持轻量泛型（给集合添加泛型）
* Swift支持静态派发（效率高）、动态派发（函数表派发、消息派发）方式，Objective-C支持动态派发（消息派发）方式
* Swift支持函数式编程（高阶函数）
* Swift的协议不仅可以被类实现，也可以被Struct和Enum实现
* Swift有元组类型、支持运算符重载
* Swift支持命名空间
* Swift支持默认参数
* Swift比Objective-C代码更简洁

#### 42、Struct和Class的区别？

 * Struct不支持继承，Class支持继承
 * Struct是值类型，Class是引用类型
 * Struct使用let创建不可变，Class使用let创建可变
 * Struct无法修改自身属性值，函数需要添加mutating关键字
 * Struct不需要deinit方法，因为值类型不关系引用计数，Class需要deinit方法
 * Struct初始化方法是基于属性的

#### 43、Swift中的常量和Objective-C中的常量有啥区别？

Objective-C中的常量(const)是编译期决定的，Swift中的常量(let)是运行时确定的

#### 44、?，??的区别

 * ？用来声明可选值，如果变量未初始化则自动初始化nil；在操作可选值时，如果可选值时nil则不响应后续的操作；使用as?进行向下转型操作；
 * ?? 用来判断左侧可选值非空（not nil）时返回左侧值可选值，左侧可选值为空（nil）则返回右侧的值。

#### 45、Swift中mutating的作用

Swift中协议是可以被Struct和Enum实现的，mutating关键字是为了能在被修饰的函数中修改Struct或Enum的变量值，对Class完全透明。

#### 46、Set（集合类型）的使用场景

Set存储值类型相同、无序、去重

#### 47、final关键词的用法

final关键词的作用：它修饰的类、方法、变量是不能被继承或重写的，编译器会报错。另外，通过它可以显示的指定函数的派发机制。

#### 48、lazy关键词的用法

 * lazy关键词的作用：指定延时加载（懒加载），懒加载存储属性只会在首次使用时才会计算初始值属性。懒加载属性必须声明（var）为变量，因为常量属性（let）初始化之前会有值。
 * lazy修饰的属性非线程安全的。

#### 49、Swift 是一门安全语言吗？

Swift是一门类型安全的语言，Optionals就是代表。Swift能帮助你在类型安全的环境下工作，如果你的代码中需要使用String类型，Swift的安全机制能阻止你错误的将Int值传递过来，这使你在开发阶段就能及时发现并修正问题。

#### 50、Swift 支持面向过程编程吗？

 它采用了 Objective-C 的命名参数以及动态对象模型，可以无缝对接到现有的 Cocoa 框架，并且可以兼容 Objective-C 代码，支持面向过程编程和面向对象编程

#### 51、Swift的内存管理是怎样的？

  Swift 使用自动引用计数（Automatic Reference Counting, ARC）来简化内存管理

#### 52、try、try?与try!

   * try：手动捕捉异常
   * try?：系统帮我们处理，出现异常返回nil；没有异常返回对应的对象
   * try!：直接告诉系统，该方法没有异常。如果出现异常程序会crash

#### 53、guard与defer

   * guard用于提前处理错误数据，else退出程序，提高代码可读性
   * defer延迟执行，回收资源。多个defer反序执行，嵌套defer先执行外层，后执行内层

#### 54、 map、filter、reduce 的作用

   * map: 映射,将一个元素根据某个函数 映射 成另一个元素（可以是同类型，也可以是不同类型）
   * filter: 过滤,将一个元素传入闭包中，如果返回的是false ， 就过滤掉
   * reduce : 先映射后融合,将数组中的所有元素映射融合在一起。

#### 55、给一个数组，要求写一个函数，交换数组中的两个元素
简单写法：
```swift
    func swap(_ nums: inout [Int], _ p: Int, _ q: Int) {
         let temp = nums[p]
         nums[p] = nums[q]
         nums[q] = temp 
    }
```
满意写法：
```swift
    func swap<T>(_ nums: inout [T], _ p: Int, _ q: Int) {
         let temp = nums[p]
         nums[p] = nums[q]
         nums[q] = temp 
    }
```
加分写法：
```swift
    func swap<T>(_ nums: inout [T], _ p: Int, _ q: Int) {
         (nums[p], nums[q]) = (nums[q], nums[p])
    }
```

## Foundtion 面试题  
作者：高恒伟
### 初级
#### 1、 堆和栈的区别和联系?

  > 从管理方式来讲对于栈来讲，是由编译器自动管理，无需我们手工控制；对于堆来说，释放工作由程序员控制，容易产生内存泄露(memory leak)，从申请大小大小方面讲，栈空间比较小，堆空间比较大，从数据存储方面来讲，栈空间中一般存储基本类型，对象的地址，堆空间一般存放对象本身，block的copy等
  
#### 2、 runtime 如何实现 weak 属性?

 >runtime 对注册的类，会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指针(obj1、objA)指向的对象(obj0)内存地址作为 key，当此对象的引用计数为0的时候会反向找到 weak 指针(obj1、objA) 并 dealloc。假如 weak 指针(obj1、objA)指向的对象(obj0)内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象(obj1、objA)，从而设置为 nil。
 
#### 3、 一个objc对象如何进行内存布局？（考虑有父类的情况）

 > 所有父类的成员变量和自己的成员变量都会存放在该对象所对应的存储空间中，每一个对象内部都有一个isa指针,指向他的类对象,类对象中存放着本对象的对象方法列表（对象能够接收的消息列表，保存在它所对应的类对象中）
成员变量的列表,属性列表，它内部也有一个isa指针指向元对象(meta class),元对象内部存放的是类方法列表,类对象内部还有一个superclass的指针,指向他的父类对象。

#### 4、 runloop和线程有什么关系？

 > Runloop 和线程是绑定在一起的。每个线程（包括主线程）都有一个对应的 Runloop 对象。我们并不能自己创建Runloop 对象，但是可以获取到系统提供的 Runloop 对象。主线程的 Runloop 会在应用启动的时候完成启动，其他线程的 Runloop 默认并不会启动，需要我们手动启动。在任何一个Cocoa程序的线程中，都可以通过以下代码来获取到当前线程的运行循环。
NSRunLoop * runloop = [ NSRunLoop currentRunLoop ];
model主要是用来指定事件在运行循环中的优先级的，分为：
1、kCFRunLoopDefaultMode: App的默认 Mode，通常主线程是在这个 Mode 下运行的。
2、UITrackingRunLoopMode: 界面跟踪 Mode，用于 ScrollView 追踪触摸滑动，保证界面滑动时不受其他 Mode 影响。

#### 5、 什么情况使用 weak 关键字，相比 assign 有什么不同？

  > 什么情况使用 weak 关键字？
在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性
自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong

 > 不同点：
weak 修饰符表明该属性定义了一种“非拥有关系” (nonowning relationship)。在为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此行为与 assign 类似，不同之处在于，在 weak 属性所指的对象遭到销毁、释放时，该属性值也会清空(nil out)。而 assign 的“设置方法”只会执行针对“纯量类型/基本数据类型” (scalar type，例如 CGFloat 或 NSInteger 等)的简单赋值操作。
assign 可以用非 OC 对象,而 weak 必须用于 OC 对象 

#### 6、 什么时候会报unrecognized selector的异常？
 >objc在向一个对象发送消息时，runtime库会根据对象的isa指针找到该对象实际所属的类，然后在该类中的方法列表以及其父类方法列表中寻找方法运行，如果，在最顶层的父类中依然找不到相应的方法时，程序在运行时会挂掉并抛出异常unrecognized selector sent to XXX 。但是在这之前，objc的运行时会给出三次拯救程序崩溃的机会：
Method resolution
objc运行时会调用+resolveInstanceMethod:或者 +resolveClassMethod:，让你有机会提供一个函数实现。如果你添加了函数，那运行时系统就会重新启动一次消息发送的过程，否则 ，运行时就会移到下一步，消息转发（Message Forwarding）。
Fast forwarding
如果目标对象实现了 -forwardingTargetForSelector:，Runtime 这时就会调用这个方法，给你把这个消息转发给其他对象的机会。 只要这个方法返回的不是nil和self，整个消息发送的过程就会被重启，当然发送的对象会变成你返回的那个对象。否则，就会继续Normal Fowarding。 这里叫Fast，只是为了区别下一步的转发机制。因为这一步不会创建任何新的对象，但下一步转发会创建一个NSInvocation对象，所以相对更快点。 3、 Normal forwarding
这一步是Runtime最后一次给你挽救的机会。首先它会发送 -methodSignatureForSelector: 消息获得函数的参数和返回值类型。如果 -methodSignatureForSelector: 返回nil，Runtime则会发出 -doesNotRecognizeSelector: 消息，程序这时也就挂掉了。如果返回了一个函数签名，Runtime就会创建一个NSInvocation对象并发送 -forwardInvocation: 消息给目标对象
#### 7、 下面的代码输出什么？
````
   @implementation Son : Father
   - (id)init
   {
       self = [super init];
       if (self) {
           NSLog(@"%@", NSStringFromClass([self class]));
           NSLog(@"%@", NSStringFromClass([super class]));
       }
       return self;
   }
   @end
````
>receiver还是当前类对象，而不是父类对象；
super这里的含义就是优先去父类的方法列表中去查实现，很多问题都是父类中其实也没有实现，还是去根类里 去找实现，这种情况下时，其实跟直接调用self的效果是一致的

#### 8、 这个写法会出什么问题： @property (copy) NSMutableArray *array; ?
>两个问题：1、添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃、因为 copy 就是复制一个不可变 NSArray 的对象；2、使用了 atomic 属性会严重影响性能。
#### 9、 property属性的修饰符有什么样的作用?
>1、线程安全的： atomic, nonatomic
2、访问权限的： readonly， readwrite
3、内存管理（ARC） assign，strong，weak，copy
4、指定方法名称： setter= getter=
如果使用assign（一般用于非OC对象），那么将直接执行赋值操作；如果使用retain（一般用于OC对象）,那么将retain新值，release旧值；如果使用copy，那么将release旧值，copy新值。
#### 10、 NSNull的作用?
> 把nil包装成对象放入数组或者dictionary中,以解决数组或者dictionary不能存放nil的情况。

## UIGestureRecognizer面试题          
作者：赵勇
### 初级
#### 1、 手势识别的过程?
> 当_UIApplicationHandleEventQueue() 识别了一个手势时，其首先会调用 Cancel > 将当前的 touchesBegin/Move/End 系列回调打断。随后系统将对应的 UIGestureRecognizer 标记为待处理。  苹果注册了一个 Observer 监测 BeforeWaiting (Loop即将进入休眠) 事件，这个Observer的回调函数是 _UIGestureRecognizerUpdateObserver()，其内部会获取所有刚被标记为待> 处理的 GestureRecognizer，并执行GestureRecognizer的回调，当有 UIGestureRecognizer 的变化(创建/销毁/状态改变)时，这个回调都会进行相应处理。
#### 2、 如何修改A ,B两个手势的优先级?
> 我们就可以用requireGestureRecognizerToFail：
这个方法可以帮助解决手势冲突问题，这个方法可以指定某个手势执行的前提是另一个手势失败才会识别执行。
#### 3、 UIGestureRecognizer手势分为哪两种类型？
>手势分为离散型手势（discrete gestures）和持续型手势（continuous gesture）。系统提供的离散型手势包括点按手势（UITapGestureRecognizer）和轻扫手势（UISwipeGestureRecognizer。
#### 4、 UIGestureRecognizer的子类有哪些?   
 >点按手势：UITapGestureRecognizer
捏合手势：UIPinchGestureRecognizer
推动手势：UIPanGestureRecognizer
轻扫手势：UISwipeGestureRecognizer
旋转手势：UIRotationGestureRecognizer
长按手势：UILongPressGestureRecognizer

#### 5、 如果系统提供的手势不能满足你的需求，怎么自定义手势？
> 自定义手势需要继承：UIGestrureRecognizer,并且需要导入头文件#import <UIKit/UIGestureRecognizerSubclass、h>，实现以下四个方法：
– touchesBegan:withEvent:  
– touchesMoved:withEvent:  
– touchesEnded:withEvent:  
– touchesCancelled:withEvent:  

## CoreGraphics 面试题 
作者：赵勇
#### 1、 如何使用CoreGraphics 自定义绘图？
> // 获取图片上下文
UIGraphicsBeginImageContextWithOptions(CGSizeMake(100,100), NO, 0);
// 绘图
CGContextRef con = UIGraphicsGetCurrentContext();
CGContextAddEllipseInRect(con, CGRectMake(0,0,100,100));
CGContextSetFillColorWithColor(con, [UIColor blueColor]、CGColor);
CGContextFillPath(con);
// 从图片上下文中获取绘制的图片
UIImage* im = UIGraphicsGetImageFromCurrentImageContext();
// 关闭图片上下文
UIGraphicsEndImageContext();
#### 2、 CoreGraphics提供了哪几种图形上下文？
> 位图图形上下文：Bitmap Graphics Context 
PDF图形上下文：PDF Graphics Context 
window图形上下文：Window Graphics Context
 layer层图形上下文：Layer Graphics Context 
打印图形上下文：Printer Graphics Context  
#### 3、 图形上下文（Graphics Context）有什么作用？
 > 保存绘图信息、绘图状态。
 决定绘制的输出目标（绘制到什么地⽅去，输出目标可以是UIView的layer层、PDF文件、Bitmap或者显示器的窗口）。
#### 4、 CoreGraphics提供了哪几种图形上下文？
> 位图图形上下文：Bitmap Graphics Context 
PDF图形上下文：PDF Graphics Context 
window图形上下文：Window Graphics Context
 layer层图形上下文：Layer Graphics Context 
打印图形上下文：Printer Graphics Context  

## Quartz Core 面试题 
### 中级
作者：邵瑞波
#### 1、 UView是如何禁用掉其管理的layer（CALayer或其子类）中可动画属性的隐式动画的？
> 图层首先检测它是否有委托，并且是否实现CALayerDelegate协议指定的-actionForLayer:forKey方法。如果有，直接调用并返回结果。如果没有委托，或者委托没有实现-actionForLayer:forKey方法，图层接着检查包含属性名称对应行为映射的actions字典。如果actions字典没有包含对应的属性，那么图层接着在它的style字典接着搜索属性名。最后，如果在style里面也找不到对应的行为，那么图层将会直接调用定义了每个属性的标准行为的-defaultActionForKey:方法。所以一轮完整的搜索结束之后，-actionForKey:要么返回空（这种情况下将不会有动画发生），要么是CAAction协议对应的对象，最后CALayer拿这个结果去对先前和当前的值做动画。于是这就解释了UIKit是如何禁用隐式动画的：每个UIView对它关联的图层都扮演了一个委托，并且提供了-actionForLayer:forKey的实现方法。当不在一个动画块的实现中，UIView对所有图层行为返回nil，所以就禁用掉了layer的隐式动画，但是在动画block范围之内，它就返回一个非空值。
#### 2、 当layer在执行动画过程中时，如何查询layer的实时状态，例如layer的坐标，大小，颜色值等属性？
>通过layer的`func presentation() -> Self?`（OC 是这个方法 `- (instancetype)presentationLayer;`） 方法获取当前layer的呈现模型，再通过获取该返回对象中的对应属性值，即为要查询的实时状态值。例如：layer、presentation()、frame 即为当该layer动画时，某一时刻该layer的实时frame。
#### 3、 CALayer 的 contentsRect 有什么作用？
> 通过设置contentsRect 属性，可以裁剪layer的contents（寄宿图）的特定子区域，例如设置contentsRect = {0, 0, 0、5, 0、5}会裁剪显示contents图片对象的左上1/4分之一区域。contestRect为相对值，0~1之间，相对于寄宿图的尺寸。
#### 4、 如何取消正在执行的动画？
 >为了终止一个指定的动画，你可以用如下方法把它从图层移除掉：
–  (void)removeAnimationForKey:(NSString *)key;
或者移除所有动画：
–  (void)removeAllAnimations;
动画一旦被移除，图层的外观就立刻更新到当前的模型图层的值。一般说来，动画在结束之后被自动移除，除非设置removedOnCompletion为NO，如果你设置动画在结束之后不被自动移除，那么当它不需要的时候你要手动移除它；否则它会一直存在于内存中，直到图层被销毁。
#### 5、 真正影响视图或图层显示顺序的是哪个属性？
> CALayer的zPosition属性，值越大，显示层级越靠上层，越不会被遮挡，如果zPosition值都相同，也会受视图原始显示层级影响（仅和同处一个父节点的兄弟节点相比）
#### 6、 Layer对象执行仿射变换旋转时，具体是参照哪个属性来旋转？例如指定layer绕自身的左上角旋转，应该如何指定参数？
> 旋转是参照视图锚点来处理的。让layer绕自身左上角旋转，应该指定 layer、anchorPoint = CGPoint(x: 0, y: 0)
## AVFoundation 面试题
### 中级
作者：邵瑞波
#### 1、 AVAudioPlayer 支持播放网络音频文件吗？如果要播放在线音频可以使用原生哪个播放器？
> 不支持，可以使用AVPlayer来播放在线音频
#### 2、 AVPlayer 支持流媒体播放吗？如果要播放实时流，可以使用哪些播放器？
> 不支持，三方如 ijkplayer
#### 3、 如何知道当前AVPlayer播放器的播放状态？
> 通过对AVPlayer的AVPlayerItem的status 属性添加 KVO 监听来获取当前AVPlayer播放器的各种状态
#### 4、 AVPlayer 如何切换播放地址？
```swift
let item = AVPlayerItem(url: mediaUrl)
player.replaceCurrentItem(with: item) 
```
#### 5、 AudioToolBox 框架主要提供哪些功能？
> 录制或播放音频、转换格式、解析音频流及管理音频会话
#### 6、 对iOS中的音频单元（Audio Units）了解那些，音频单元可以处理哪些场景任务?
> 1、低延迟的同步音频I/O(输入和输出)，如VoIP (Voice over Internet Protocol)应用程序
2、对合成声音的响应式播放，如音乐游戏或合成乐器
3、使用一种特定的音频单元功能，如回声消除、混音或音调均衡
#### 7、 AVCaptureSession 主要负责处理哪些逻辑？
> AVCaptureSession 是管理捕获活动并协调从输入设备到捕获输出的数据流的对象。 AVCaptureSession 用于连接输入和输出的资源，从物理设备如摄像头和麦克风等获取数据流，输出到一个或多个目的地。 AVCaptureSession 可以额外配置一个会话预设值（session preset），用于控制捕捉数据的格式和质量，预设值默认值为 AVCaptureSessionPresetHigh。
#### 8、 AVCaptureSession 使用时有哪些注意事项？
> 1、AVCaptureSession 的 startRunning() 方法执行时可能需要较长时间，为避免阻塞UI线程，应该在非UI串行队列中执行。
2、采集会话期间可能会发生通话中断、其他应用的通知和音乐播放等中断，需要通过添加观察者来监听AVCaptureInterrupted处理这些中断。
3、如果设备承受系统压力，例如过热，捕获会话也可能停止。相机不会自行降低捕获质量或掉落帧；如果达到临界点，相机停止工作，或设备关闭。为了避免给用户带来不良体验，您可能希望您的应用程序根据AVCapture、System的反馈手动降低帧速率、关闭深度或调制性能，具体可以通过KVO监听AVCaptureDevice的systemPressureState属性来做出相应处理。
4、对于AVCaptureSession的多个配置要放到使用 beginConfiguration() 和 commitConfiguration() 的原子事务更新中处理，具体先调用 beginConfiguration() 后，然后您可以添加或删除输出、更改 sessionPreset 或配置单个捕获输入或输出属性，在调用 commitConfiguration() 之前，实际上不会进行任何更改，此时这些更改将一起应用。
#### 9、 使用 AVPlayerViewController 或 AVPlayerLayer 播放视频时，假如已经配置支持后台播放模式，当应用进入后台时，还需如何处理才能保证音频的继续播放?
> 因为在播放视频时，进入后台视频相关资源无法正常渲染，所以系统会将播放视频资源的AVPlayer暂停播放，如果要能在后台继续音频资源，则应将AVPlayer和其依附主体断开连接，及将 avplayerViewController、player = nil （avplayerLayer、player = nil）,当然要继续持有对应的player对象，避免其释放。为保证进入应用前台时可以继续正常播放视频，还在在进入前台时，将player重新与其依附主体连接，即 avplayerViewController、player = player （avplayerLayer、player = player）。
## 数据存储面试题 
作者：曹原赫
### 中级
#### 1、 如何使用CoreData？ 
> 创建工程时，需要勾选CoreData选项，创建模型文件，可以在模型文件里进行添加实体对象；
然后新建一个NSManagedObject cubclass文件；
工程创建完之后，它会在AppDelegate、h里自动生成了3个属性；
然后在需要使用的类里面添加CoreData头文件，再使用它的属性，就可以对数据进行增删改查操作了。
### 初级
#### 1、 持久化存储方案有哪些？
> plist，NSUserDefaults，NSCoding，SQLite，Core Data
#### 2、 什么是序列化和反序列化，用来做什么？
> 序列化- 把对象转化为字节序列的过程
反序列- 化把直接序列恢复成对象
作用- 把对象写到文件或者数据库中，并且读取出来
#### 3、 如何实现对复杂对象的存储？
> 采用归档的形式保存数据，该数据对象需要遵守NSCoding协议，
并且该对象对应的类必须提供2个方法:对象进行编码的方法encodeWithCoder:,对象进行解码的方法initWithCoder:。
然后创建沙盒，设置归档路径，使用NSKeyedArchiver序列化进行编码，使用NSKeyedUnarchiver反序列化进行解码。

#### 5、 如何对自定义的对象进行CoreData保存？ **
> ①引入CoreData框架
②创建数据模型文件、xcdatamodel
③初始化NSManagedObjectModel对象，加载数据模型文件，读取app中所有实体信息。
④初始化NSPersistentStoreCoordinator 对象，添加持久化库
⑤初始化NSManagedObjectContext对象，拿到上下文对象操作实体。

#### 8、 简述下FMDB， 如何保证线程安全? **
> 1：对sqlite的封装
2： FMDatabase线程不安全。 使用FMDatabaseQueue来保证线程安全。串行队列
#### 9、 简述数据库事务，FMDB和CoreData是如何支持事务的? **
>事务是一个并发控制的基本单元，所谓的事务，它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位。
sqlite3：sql语句前加上“begin transaction;”执行完之后执行“commit transaction;”或者“rollback transaction;”进行提交或回滚即可
FMDatabase有beginTransaction、commit、rollback三个方法进行开启事务、提交事务和回滚事务、
Core Data: 增、删、改操作之后必须调用上下文的保存方法，其实本身就提供了事务的支持，只要不调用保存方法，之前所有的操作是不会提交的
#### 10、 沙盒结构?
>Application
Documents
Library：Caches，Preference
tmp
#### 11、 Application包含什么？数字签名的时机？
> 包含了所有的资源文件和和可执行文件，上架前经过数字签名，上架后不可修改。
#### 12、 Documents是否会被备份？是否可以缓存文件，如果不能，为什么？
>文档目录，要保存程序生成的数据，会自动备份到iCloud中。iTunes同步设备时会备份该目录。
这里不能存缓存文件,否则上架不被通过
#### 13、 偏好设置存储在哪个文件夹？是否会被备份？
> Lirary/Preference：保存应用的所有偏好设置，iCloud会备份设置信息、
#### 14、 写NSUserDefault 后，立即读NSUserDefault ，有什么后果？如何避免?
> 写NSUserDefault 是异步行为，可能读不到值。使用synchronize、
#### 15、 Tmp什么时候会被清理？是否会被同步？ **
> 清理时机： 系统自动清理，手机重启清空，系统磁盘不足被清空。
itunes不会同步
#### 16、 钥匙串存储在哪，有什么作用?
> keychain 存储在硬盘上。删除了应用，保存的数据还在
#### 17、 不同app间能否实现keychain共享？需要什么条件？
> 每个APP的keychain相对来说是独立的，但是也可以实现APP之间keychain数据的共享，前提是同一个TeamID下、且设置了数据共享。
#### 18、 简单描述下客户端的缓存机制？
> 1、缓存可以分为：内存数据缓存、数据库缓存、文件缓存
2、每次想获取数据的时候，先检测内存中有无缓存
3、再检测本地有无缓存(数据库\文件)、最终发送网络请求
4、将服务器返回的网络数据进行缓存（内存、数据库、文件），以便下次读取
#### 19、 NSCache本质是什么？是否是线程安全？
> NSCache是一个类似于集合的容器，是一个key-Value对。
NSCache类结合了各种自动删除策略，以确保不会占用过多的系统内存，系统自动执行这些策略
NSCache是线程安全的，我们可以在不同的线程中添加、删除和查询缓存中的对象，而不需要锁定缓存区域。
#### 20、 NSCache超过最大缓存，丢弃的顺序是什么？
> 丢弃顺序无法保证。系统会持续丢弃对象，知道低于最大缓存限制
#### 21、 如果NSCache设置了 totalCostLimit 属性来限定缓存能维持的最大内存，此时使用  setObject(obj: , forKey: )  存储一个大于缓存的对象， NSCache会删除对象吗?
> 如果我们设置了 totalCostLimit 属性来限定缓存能维持的最大内存
然后使用 setObject(obj: , forKey: ) 来添加对象
那么就算所有缓存对象的总字节数超过了我们所设置的值
NSCache 也不会帮我们删除对象
要使用 setObject(obj: , forKey: , cost: ) 来添加缓存对象
并设置 cost 才可以
#### 22、 iOS中，如何加载pdf文件？ 
> 方案一：UIWebView/WKWebView
方案二：自定义UIView，通过CoreGraphics框架底层接口绘制PDF
方案三：通过QuickLook框架中的QLPreviewController进行展示
方案四：对PDF数据流进行处理，提取PDF数据流中的元素将其转化为HTML标签，从而将PDF转为HTML格式，通过webView加载进行展示，需要用到第三方开源库pdf、js。
## UItouch 事件传递 
### 初级
作者：邹丽莹
#### 1、 如何寻找最合适的view?
> 1, 首先判断主窗口（keyWindow）自己是否能接受触摸事件
2, 判断触摸点是否在自己身上
3, 子控件数组中从后往前遍历子控件，重复前面的两个步骤（所谓从后往前遍历子控件，就是首先查找子控件数组中最后一个元素，然后执行1、2步骤）
4, view，比如叫做fitView，那么会把这个事件交给这个fitView，再遍历这个fitView的子控件，直至没有更合适的view为止。
5, 如果没有符合条件的子控件，那么就认为自己最合适处理这个事件，也就是自己是最合适的view。
#### 2、 UIView不能接收触摸事件的三种情况?
> 1, 不允许交互：userInteractionEnabled = NO
2, 隐藏：如果把父控件隐藏，那么子控件也会隐藏，隐藏的控件不能接受事件
3, 透明度：如果设置一个控件的透明度<0、01，会直接影响子控件的透明度。alpha：0、0~0、01为透明。
#### 3、 寻找最合适的view的底层实现（hitTest:withEvent:底层实现）？
```
   //  Converted to Swift 5.5 by Swiftify v5.5.24623 - https://swiftify.com/
// 什么时候调用:只要事件一传递给一个控件，那么这个控件就会调用自己的这个方法
//作用:寻找并返回最合适的view
//UIApplication -> [UIWindow hitTest:withEvent:]寻找最合适的view告诉系统
//point:当前手指触摸的点
//point:是方法调用者坐标系上的点
func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
    //1、判断下窗口能否接收事件
    if isUserInteractionEnabled == false || hidden == true || alpha <= 0.01 {
        return nil
    }
    // 2、判断下点在不在窗口上
    //不在窗口上
    if self.point(inside: point, with: event) == false {
        return nil
    }
    //3、从后往前遍历子控件数组
    let count = Int(subviews.count)
    var i = count - 1
    while i >= 0 {
        //获取子控件
        let childView = subviews[i] as? UIView
        //坐标系的转换,把窗口上的点转换为子控件上的点
        //把自己控件上的点转换成子控件上的点
        let childP = convert(point, to: childView)
        let fitView = childView?.hitTest(childP, with: event)
        if let fitView = fitView {
            // 如果能找到最合适的view
            return fitView
        }
        i—
    }
    // 4、没有找到更合适的view，也就是没有比自己更合适的view
    return self
}
```
## ViewController 面试题  
### 初级
作者：邹丽莹
#### 1、单个viewController的生命周期？
> - initWithCoder:(NSCoder *)aDecoder：（如果使用storyboard或者xib）
> - loadView：加载view
> - viewDidLoad：view加载完毕
> - viewWillAppear：控制器的view将要显示
> - viewWillLayoutSubviews：控制器的view将要布局子控件
> - viewDidLayoutSubviews：控制器的view布局子控件完成  
> - viewDidAppear:控制器的view完全显示
> - viewWillDisappear：控制器的view即将消失的时候
> - viewDidDisappear：控制器的view完全消失的时候
> - dealloc 控制器销毁
#### 2、 接收到内存警告调用的方法？
```swift

func didReceiveMemoryWarning() {

    super.didReceiveMemoryWarning()

    print("\(#function)")
}

````
#### 3、 切换rootViewController时,销毁之前的控制器？

> 一、iOS在切换根控制器时，如何销毁之前的控制器？(切换rootViewController时注意的内存泄漏)首先、在iOS的ARC机制下，任何对象，当没有其他对象对他进行强引用时，都会被自动释放。而控制器的切换主要为两种方式 push和present modally。
01, push出来的子控制器，导航栏控制器会对该子控制器进行强引用（本质就是入栈，在入栈的过程中，可能不止是导航栏控制器对子控制器有强引用）其中 push 对应着 pop 则控制器会被销毁。其push的本质是入栈，所有的子控制器放在数组中，先进后出，如果导航栏作为根控制器。此时需要更该根控制器，不管push了多少子控制器入栈了。其最后只需要在当前显示的子控制器中对根控制器重新赋值即可。即原先的导航栏控制器没有对象强引用它，它会被释放，其所有的子控制器自然都会被释放了（前提是你本身代码不存在其他内存泄漏）
```
 UIApplication、shared、keyWindow?、rootViewController = newRootVC
 ```
 > 02谁来调用这消失presented VC的这个方法：正确的做法是“谁污染谁治理”,对于弹出模态，系统指定了销毁的方法dismiss,其中动画效果暂时关闭（原因在后面）
```
 dismiss(animated: false, completion: nil)
```
> eg：在我们的项目中假如需要在firstVC2控制器进行根控制器的切换。firstVC2控制器是这样来的：
ViewController  push -—>firstVC1  present—>firstVC2 。则如何在firstVC2中切换根控制器，并同时销毁之前的控制器。则必须先dismiss掉 firstVC2 。实现如下：
dismiss(animated: false) {
UIApplication、shared、keyWindow?、rootViewController = newRootVC
}
总结：如果不dismiss掉 firstVC2，则firstVC2不会被释放。firstVC2无法释放，必然导致firstVC1无法释放，而栈顶元素无法被释放，栈顶下面的控制器都无法释放
## UItouch 面试题  
### 初级
作者：邹丽莹
#### 1、如何做到一个事件多个对象处理？
```swift

func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent) {
    // 1、自己先处理事件、、、
    print("do somthing、、、")
    // 2、再调用系统的默认做法，再把事件交给上一个响应者处理
    super.touchesBegan(touches, with: event)
}

```
#### 2、 iOS UIEvent对象的作用与常见属性？
> 每产生一个事件，就会产生一个UIEvent对象
UIEvent : 称为事件对象，记录事件产生的时刻和类型
常见属性 :
//事件类型 //@property(nonatomic,readonly) UIEventType type; //@property(nonatomic,readonly) UIEventSubtype subtype; //事件产生的时间 @property(nonatomic,readonly) NSTimeInterval timestamp; UIEvent还提供了相应的方法可以获得在某个view上面的触摸对象（UITouch）
#### 3、 iOS UITouch对象的作用与常见属性？
> 当用户用一根手指触摸屏幕时，会创建一个与手指相关联的UITouch对象 一根手指对应一个UITouch对象
UITouch的作用：
保存着跟手指相关的信息，比如触摸的位置、时间、阶段
当手指移动时，系统会更新同一个UITouch对象，使之能够一直保存该手指在的触摸位置
当手指离开屏幕时，系统会销毁相应的UITouch对象
UITouch的常见属性
//触摸产生时所处的窗口 @property(nonatomic,readonly,retain) UIWindow *window;
//触摸产生时所处的视图 @property(nonatomic,readonly,retain) UIView *view; 
//短时间内点按屏幕的次数，可以根据tapCount判断单击、双击或更多的点击@property(nonatomic,readonly) NSUInteger tapCount; 
//记录了触摸事件产生或变化时的时间，单位是秒 @property(nonatomic,readonly) NSTimeInterval timestamp; 
//当前触摸事件所处的状态 @property(nonatomic,readonly) UITouchPhase phase;
UITouch的常见方法
//返回值表示触摸在view上的位置 
//这里返回的位置是针对view的坐标系的（以view的左上角为原点(0, 0)） //调用时传入的view参数为nil的话，返回的是触摸点在UIWindow的位置
```swift

func location(in view: UIView?) -> CGPoint {
} // 该方法记录了前一个触摸点的位置

func previousLocation(in view: UIView?) -> CGPoint {
}

```



## UI类：
### 中级
#### 1、使用 drawRect 有什么影响？

- drawRect 方法依赖 Core Graphics 框架来进行自定义的绘制
- 缺点：它处理 touch 事件时每次按钮被点击后，都会用 setNeddsDisplay 进行强制重绘；而且不止一次，每次单点事件触发两次执行。这样的话从性能的角度来说，对 CPU 和内存来说都是欠佳的。特别是如果在我们的界面上有多个这样的 UIButton 实例，那就会很糟糕了
- 这个方法的调用机制也是非常特别、 当你调用 setNeedsDisplay 方法时, UIKit 将会把当前图层标记为 dirty, 但还是会显示原来的内容, 直到下一次的视图渲染周期, 才会将标记为 dirty 的图层重新建立 Core Graphics 上下文, 然后将内存中的数据恢复出来, 再使用 CGContextRef 进行绘制

#### 2、iOS 为什么必须在主线程中操作UI

- UIKit不是线程安全的(多个线程访问修改,可能一个线程已经释放了,另一个线程会访问,以及资源抢夺问题等)
- 主线程上默认是开始 runloop 的,子线程没有 runloop 也无法监听一些事件,手势刷新UI等操作
- 在子线程更新UI可能会无效,也可能会崩溃

#### 3、什么是离屏渲染？

如果要在显示屏上显示内容，我们至少需要一块与屏幕像素数据量一样大的frame buffer，作为像素数据存储区域，而这也是GPU存储渲染结果的地方。如果有时因为面临一些限制，无法把渲染结果直接写入frame buffer，而是先暂存在另外的内存区域，之后再写入frame buffer，那么这个过程被称之为离屏渲染。 

![](https://camo、githubusercontent、com/966780ee85124c880942dde41ed8f0e485e47e0a00d31ad8c93e14cc15a01565/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f32323837373939322d663538653861386463373437326564362e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970253743696d61676556696577322f322f772f31323430 "")

- 在 OpenGL 中，GPU 屏幕渲染有以下两种方式： 一、On-Screen Rendering 即当前屏幕渲染，在用于显示的屏幕缓冲区中进行，不需要额外创建新的缓存，也不需要开启新的上下文，所以性能较好，但是受到缓存大小限制等因素，一些复杂的操作无法完成。 二、Off-Screen Rendering 即离屏渲染，指的是在GPU的当前屏幕缓冲区外开辟新的缓冲区进行操作。 相比于当前屏幕渲染，离屏渲染的代价是很高的，主要体现在如下两个方面： 1、创建新的缓冲区 2、上下文切换。离屏渲染的整个过程，需要多次切换上下文环境：先从当前屏幕切换到离屏，等待离屏渲染结束后，将离屏缓冲区的渲染结果显示到到屏幕上，这又需要将上下文环境从离屏切换到当前屏幕。
- CPU 渲染和离屏渲染的区别 由于GPU的浮点运算能力比CPU强，CPU渲染的效率可能不如离屏渲染。但如果仅仅是实现一个简单的效果，直接使用 CPU 渲染的效率又可能比离屏渲染好，毕竟普通的离屏渲染要涉及到缓冲区创建和上下文切换等耗时操作。对一些简单的绘制过程来说，这个过程有可能用CoreGraphics，全部用CPU来完成反而会比GPU做得更好。一个常见的 CPU 渲染的例子是：重写 drawRect 方法，并且使用任何 Core Graphics 的技术进行了绘制操作，就涉及到了 CPU 渲染。整个渲染过程由 CPU 在 App 内同步地完成，渲染得到的bitmap最后再交由GPU用于显示。总之，具体使用 CPU 渲染还是使用 GPU 离屏渲染更多的时候需要进行性能上的具体比较才可以。
- iOS 9、0 之前UIimageView跟UIButton设置圆角都会触发离屏渲染。 iOS 9、0 之后UIButton设置圆角会触发离屏渲染，而UIImageView里png图片设置圆角不会触发离屏渲染了，如果设置其他阴影效果之类的还是会触发离屏渲染的。

#### 4、造成离屏渲染原因

- shouldRasterize（光栅化）。
- masks（遮罩）。
- shadows（阴影）。
- edge antialiasing（抗锯齿）。
- group opacity（不透明）
- clearColor、alpha等操作。

#### 5、离屏渲染的解决方案

- clearColor可以通过直接设置颜色来解决。
- alpha为0时候用hidden替换。
- 圆角、边框解决方案：1、UIBezierPath 2、使用Core Graphics为UIView加圆角 3、直接处理图片为圆角 4、后台处理圆角
- 阴影解决方案：shadowPath替换。
- 尝试开启CALayer、shouldRasterize。
- 对于不透明的View，设置opaque为YES，这样在绘制该View时，就不需要考虑被View覆盖的其他内容（尽量设置Cell的view为opaque，避免GPU对Cell下面的内容也进行绘制）

#### 6、图片子线程预加载及预处理

- 图片子线程异步下载。
- 图片子线程处理。比如对于圆角图片，可以让后台传圆角图片，也可以在子线程生成圆角图片，也可以用UIBezierPath生成圆角；在子线程缩放图片然后加载到图片控件上。
- 图片按需下载。只下载显示的cell的图片。


#### 7、Main函数之前做了什么？ 

1、 动态库链接库
2、 ImageLoader加载可执行文件，里面是被编译过的符号、代码等
3、 runtime与+load

#### 8、APP的启动过程，从main开始

1、main 函数

2、UIApplicationMain

*创建UIApplication对象    

*创建UIApplication的delegate对象

3、如果有storyboard，根据info、plist获得Main、storyboard的文件名，加载Main、storyboard；如果没有storyboard，AppDelegate对象开始处理（监听）系统事件，程序启动完毕的时候，在application:didFinishLaunchingWithOptions:中

*创建UIWindow

*创建和设置UIWindow的rootViewController 

*显示窗口

### 初级
#### 1、UIView 和 CALayer 是什么关系?

- UIView 显示在屏幕上归功于 CALayer，通过调用 drawRect 方法来渲染自身的内容，调节 CALayer 属性可以调整 UIView 的外观，
- UIView 继承自 UIResponder，比起 CALayer 可以响应用户事件，Xcode6 之后可以方便的通过视图调试功能查看图层之间的关系
- UIView 是 iOS 系统中界面元素的基础，所有的界面元素都继承自它。它内部是由 Core Animation 来实现的，它真正的绘图部分，是由一个叫 CALayer(Core Animation Layer) 的类来管理。UIView 本身，更像是一个 CALayer 的管理器，访问它的跟绘图和坐标有关的属性，如 frame，bounds 等，实际上内部都是访问它所在 CALayer 的相关属性
- UIView 有个 layer 属性，可以返回它的主 CALayer 实例，UIView 有一个 layerClass 方法，返回主 layer 所使用的类，UIView 的子类，可以通过重载这个方法，来让 UIView 使用不同的 CALayer 来显示，如：


```swift

override class var layerClass: AnyClass {
        CAEAGLLayer.self
    }

```


- UIView 的 CALayer 类似 UIView 的子 View 树形结构，也可以向它的 layer 上添加子 layer，来完成某些特殊的显示。例如下面的代码会在目标 View 上敷上一层黑色的透明薄膜。

```swift
let grayCover = CALayer()
grayCover.backgroudColor = UIColor.black.withAlphaComponent(0.2).cgColor
layer.addSubLayer(grayCover)
```




#### 2、loadView 的作用？

- loadView 用来自定义 view，只要实现了这个方法，其他通过 xib 或 storyboard 创建的 view 都不会被加载
- 看懂控制器 view 创建的这个图就行

![](https://cdn、jsdelivr、net/gh/uclort/file@master/uPic/image、png)

#### 3、IBOutlet 连出来的视图属性为什么可以被设置成 weak?

- 因为父控件的 subViews 数组已经对它有一个强引用

#### 4、沙盒目录结构是怎样的？各自用于那些场景？

- Application：存放程序源文件，上架前经过数字签名，上架后不可修改
- Documents：常用目录，iCloud 备份目录，存放数据
- Library
	- Caches：存放体积大又不需要备份的数据
	- Preference：设置目录，iCloud 会备份设置信息
- tmp：存放临时文件，不会被备份，而且这个文件下的数据有可能随时被清除的可能

#### 5、pushViewController 和 presentViewController 有什么区别

- 两者都是在多个试图控制器间跳转的函数
- presentViewController 提供的是一个模态视图控制器 (modal)
- pushViewController 提供一个栈控制器数组，push/pop

#### 6、请简述 UITableView 的复用机制

- 每次创建 cell 的时候通过 dequeueReusableCellWithIdentifier: 方法创建 cell，它先到缓存池中找指定标识的 cell，如果没有就直接返回 nil
- 如果没有找到指定标识的 cell，那么会通过 initWithStyle:reuseIdentifier: 创建一个 cell
- 当 cell 离开界面就会被放到缓存池中，以供下次复用



#### 8、描述下 SDWebImage 里面给 UIImageView 加载图片的逻辑

- SDWebImage 中为 UIImageView 提供了一个分类 UIImageView+WebCache、h, 这个分类中有一个最常用的接口 sd_setImageWithURL:placeholderImage:，会在真实图片出现前会先显示占位图片，当真实图片被加载出来后在替换占位图片
- 加载图片的过程大致如下：
	- 首先会在 SDWebImageCache 中寻找图片是否有对应的缓存, 它会以 url 作为数据的索引先在内存中寻找是否有对应的缓存
	- 如果缓存未找到就会利用通过 MD5 处理过的 key 来继续在磁盘中查询对应的数据, 如果找到了, 就会把磁盘中的数据加载到内存中，并将图片显示出来
	- 如果在内存和磁盘缓存中都没有找到，就会向远程服务器发送请求，开始下载图片
	- 下载后的图片会加入缓存中，并写入磁盘中
	- 整个获取图片的过程都是在子线程中执行，获取到图片后回到主线程将图片显示出来

#### 9、VC生命周期

考察viewDidLoad、viewWillAppear、ViewDidAppear等方法的执行顺序。 假设现在有一个 AViewController(简称 Avc) 和 BViewController (简称 Bvc)，通过 navigationController 的push 实现 Avc 到 Bvc 的跳转，调用顺序如下： 

1、A viewDidLoad  

2、A viewWillAppear 

 3、A viewDidAppear  

4、B viewDidLoad  

5、A viewWillDisappear  

6、B viewWillAppear 

 7、A viewDidDisappear  

8、B viewDidAppear 

如果再从 Bvc 跳回 Avc，调用顺序如下： 

1、B viewWillDisappear  

2、A viewWillAppear  

3、B viewDidDisappear  

4、A viewDidAppear



#### 11、如何处理UITableVier 中Cell 动态计算高度的问题，都有哪些方案

- 你的Cell要使用AutoLayout来布局约束这是必须的；设置tableview的estimatedRowHeight为一个非零值，这个属性是设置一个预估的高度值，不用太精确。 设置tableview的rowHeight属性为UITableViewAutomaticDimension
- 第三方 UITableView+FDTemplateLayoutCell(计算布局高度缓存的)
- 手动计算每个控件的 高度并相加,最后缓存高度



#### 16、Bounds 和 Frame 的区别?

Bounds：一般是相对于自身来说的，是控件的内部尺寸。如果你修改了 Bounds，那么子控件的相对位置也会发生改变。

Frame ：是相对于父控件来说的，是控件的外部尺寸。

#### 17、setNeedsDisplay 和 layoutIfNeeded 两者是什么关系？

UIView的setNeedsDisplay和setNeedsLayout两个方法都是异步执行的。而setNeedsDisplay会自动调用drawRect方法，这样可以拿到UIGraphicsGetCurrentContext进行绘制；而setNeedsLayout会默认调用layoutSubViews，给当前的视图做了标记；layoutIfNeeded 查找是否有标记，如果有标记及立刻刷新。  
只有setNeedsLayout和layoutIfNeeded这二者合起来使用，才会起到立刻刷新的效果。

#### 18、谈谈对 UIResponder 的理解

UIResponder 类是专门用来响应用户的操作处理各种事件的，包括触摸事件 (Touch Events)、运动事件 (Motion Events)、远程控制事件 (Remote Control Events)。我们知道 UIApplication、UIView、UIViewController 这几个类是直接继承自 UIResponder，所以这些类都可以响应事件。当然我们自定义的继承自 UIView 的 View 以及自定义的继承自 UIViewController 的控制器都可以响应事件。

#### 21、手机适配方案

1、 使用宏,针对不同的设备抽取导航,状态栏,以及 tabbar 高度信息
2、 宽高等比适配(X 的特殊处理)
3、 图片美工需要提供@2x,@3x进行适配
4、 字体根据屏幕大小适配
5、 权限针对不同系统进行适配
6、 api 适配

## 多线程：
### 中级
#### 1、dispatch_group_t (组调度)的使用?

```swift

let group = DispatchGroup()
group.async(group: DispatchQueue.global(qos: .default), execute: {
    //请求1
})
group.async(group: DispatchQueue.global(qos: .default), execute: {
    //请求2
})
group.async(group: DispatchQueue.global(qos: .default), execute: {
    //请求3
})
dispatch_group_notify(group, DispatchQueue.main, {
    //界面刷新
    print("任务均完成，刷新界面")
})

```


#### 2、dispatch_semaphore (信号量)如何使用?

- 用于控制最大并发数
- 可以防止资源抢夺

与他相关的共有三个函数，分别是

```swift
dispatch_semaphore_create，  // 创建最大并发数

dispatch_semaphore_wait。    // -1 开始执行 (0则等待)

dispatch_semaphore_signal，  // +1 
```


#### 3、NSOperation如何实现操作依赖?

```swift

let queue = OperationQueue()
//创建操作
let operation1 = BlockOperation(block: {
    print("执行第1次操作，线程：\(Thread.current)")
})
let operation2 = BlockOperation(block: {
    print("执行第2次操作，线程：\(Thread.current)")
})
let operation3 = BlockOperation(block: {
    print("执行第3次操作，线程：\(Thread.current)")
})
//添加依赖
operation1.addDependency(operation2)
operation2.addDependency(operation3)
//将操作添加到队列中去
queue.addOperation(operation1)
queue.addOperation(operation2)
queue.addOperation(operation3)

```

#### 4、什么是线程安全?

- 1块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源
- 比如多个线程访问同一个对象、同一个变量、同一个文件
- 当多个线程访问同一块资源时，很容易引发数据错乱和数据安全问题

#### 5、自旋锁和互斥锁的是什么?

- 自旋锁会忙等: 所谓忙等，即在访问被锁资源时，调用者线程不会休眠，而是不停循环在那里，直到被锁资源释放锁。
- 互斥锁会休眠: 所谓休眠，即在访问被锁资源时，调用者线程会休眠，此时cpu可以调度其他线程工作。直到被锁资源释放锁。此时会唤醒休眠线程。

### 初级

#### 1、iOS的多线程方案有哪几种？

![](https://upload-images、jianshu、io/upload_images/1696952-514db7679a605c70、png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp "")

#### 2、GCD 的队列类型?

GCD的队列可以分为2大类型

- 并发队列（`Concurrent Dispatch Queue`）  
	可以让多个任务并发（同时）执行（自动开启多个线程同时执行任务）  
	并发功能只有在异步（`dispatch_async`）函数下才有效
- 串行队列（`Serial Dispatch Queue`）  
	让任务一个接着一个地执行（一个任务执行完毕后，再执行下一个任务）,按照FIFO顺序执行、




#### 3、是否可以把比较耗时的操作放在 NSNotification中?

- 如果在异步线程发的通知，那么可以执行比较耗时的操作;
- 如果在主线程发的通知，那么就不可以执行比较耗时的操作

#### 4、说几个你在工作中使用到的线程安全的例子?

UIKit(必须在主线程)
FMDBDataBaseQueue(串行队列)
等等、、

#### 5、dispatch_barrier_(a)sync使用?
一个dispatch barrier 允许在一个并发队列中创建一个同步点。当在并发队列中遇到一个barrier, 他会延迟执行barrier的block,等待所有在barrier之前提交的blocks执行结束。 这时，barrier block自己开始执行。 之后， 队列继续正常的执行操作。

#### 6、在项目什么时候选择使用 GCD，什么时候选 择 NSOperation?

- 项目中使用 NSOperation 的优点是 NSOperation 是对线程的高度抽象，在项目中使 用它，会使项目的程序结构更好，子类化 NSOperation 的设计思路，是具有面向对 象的优点(复用、封装)，使得实现是多线程支持，而接口简单，建议在复杂项目中 使用。
- 项目中使用 GCD 的优点是 GCD 本身非常简单、易用，对于不复杂的多线程操 作，会节省代码量，而 Block 参数的使用，会是代码更为易读，建议在简单项目中 使用。

#### 7、说一下 OperationQueue 和 GCD 的区别，以及各自的优势

1、 GCD是纯C语⾔言的API，NSOperationQueue是基于GCD的OC版本封装
2、 GCD只⽀支持FIFO的队列列，NSOperationQueue可以很⽅方便便地调整执⾏行行顺 序、设 置最⼤大并发数量量
3、 NSOperationQueue可以在轻松在Operation间设置依赖关系，⽽而GCD 需要写很 多的代码才能实现
4、 NSOperationQueue⽀支持KVO，可以监测operation是否正在执⾏行行 (isExecuted)、 是否结束(isFinished)，是否取消(isCanceld)
5、 GCD的执⾏行行速度⽐比NSOperationQueue快 任务之间不不太互相依赖:GCD 任务之间 有依赖\或者要监听任务的执⾏行行情况:NSOperationQueue

#### 8、GCD如何取消线程?

`dispatch_block_cancel`类似NSOperation一样，可以取消还未执行的线程。但是没办法做到取消一个正在执行的线程。

```swift

let queue = DispatchQueue.global(qos: .default)
let block1 = dispatch_block_create(0, {
    print("block1")
})
let block2 = dispatch_block_create(0, {
    print("block2")
})

let block3 = dispatch_block_create(0, {
    print("block3")
})

queue.async(execute: block1)
queue.async(execute: block2)
queue.async(execute: block3)
dispatch_block_cancel(block3) // 取消 block3

```


使用`临时变量+return` 方式取消 正在执行的Block

```swift

var gcdFlag = false // 临时变量
DispatchQueue.global(qos: .default).async(execute: {
    for i in 0..<1000 {
        print(String(format: "正在执行第i次:%ld", i))
        sleep(1)
        if gcdFlag == true {
            // 判断并终止
            print("终止")
            return
        }
    }
})
DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + Double(Int64(10 * Double(NSEC_PER_SEC))) / Double(NSEC_PER_SEC), execute: {
    print("我要停止啦")
    gcdFlag = true
})

```


#### 9、NSOperation取消线程方式?

、通过 cancel 取消未执行的单个操作

```swift

//  Converted to Swift 5.5 by Swiftify v5.5.24623 - https://swiftify.com/
let queue1 = OperationQueue()
let block1 = BlockOperation(block: {
    print("block11")
})
let block2 = BlockOperation(block: {
    print("block22")
})
let block3 = BlockOperation(block: {
    print("block33")
})
block3.cancel()
queue1.addOperations([block1, block2, block3], waitUntilFinished: true)


// 移除队列里面所有的操作，但正在执行的操作无法移除
queue1.cancelAllOperations()



// 挂起队列，使队列任务不再执行，但正在执行的操作无法挂起

queue1.isSuspended = true
// 我们可以自定义NSOperation，实现取消正在执行的操作。其实就是拦截main方法。
// main方法：
// 1、任何操作在执行时，首先会调用start方法，start方法会更新操作的状态（过滤操作,如过滤掉处于“取消”状态的操作）。
// 2、经start方法过滤后，只有正常可执行的操作，就会调用main方法。
// 3、重写操作的入口方法(main)，就可以在这个方法里面指定操作执行的任务。
//4、main方法默认是在子线程异步执行的。

```




#### 10、线程安全的处理手段有哪些？

- 加锁
- 同步执行

#### 11、如何理解GCD死锁?

- 所谓死锁、通常是指2个操作相互等待对方完成,造成死循环,于是2个操作都无法进行,就产生了死锁;

#### 12、什么情况使用自旋锁比较划算？

- 预计线程等待锁的时间很短
- 加锁的代码（临界区）经常被调用，但竞争情况很少发生
- CPU资源不紧张
- 多核处理器

#### 13、什么情况使用互斥锁比较划算？

- 预计线程等待锁的时间较长
- 单核处理器
- 临界区有IO操作
- 临界区代码复杂或者循环量大
- 临界区竞争非常激烈

## 三方库：
### 中级
#### 1、RxSwift基本概念与使用

RxSwift 属于函数响应式编程。它是通过构建函数操作数据序列，然后对这些序列做出响应的编程方式。它结合了函数式编程以及响应式编程。

##### 函数式编程

函数式编程需要我们将函数作为参数传递，或者作为返回值返回。我们可以通过组合不同的函数来得到想要的结果。

##### 响应式编程

响应式编程是使用异步数据流进行编程。任何事物都可以是一个数据流，如用户输入、点击事件、定时器、网络请求等，监听数据流并相应的作出响应。

#### 2、RxSwift 核心

- [Observable](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/rxswift_core/observable、html) - 产生事件
- [Observer](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/rxswift_core/observer、html) - 响应事件
- [Operator](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/rxswift_core/operator、html) - 创建变化组合事件
- [Disposable](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/rxswift_core/disposable、html) - 管理绑定（订阅）的生命周期
- [Schedulers](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/rxswift_core/schedulers、html) - 线程队列调配

#### 3、如何选择操作符？

[https://beeth0ven、github、io/RxSwift-Chinese-Documentation/content/decision_tree、html](https://beeth0ven、github、io/RxSwift-Chinese-Documentation/content/decision_tree、html)

#### 4、RxSwift 生态系统

- [RxDataSources](https://github、com/RxSwiftCommunity/RxDataSources) - UITableView 和 UICollectionView 数据源
- [RxGesture](https://github、com/RxSwiftCommunity/RxGesture) - 页面手势
- [RxMKMapView](https://github、com/RxSwiftCommunity/RxMKMapView) - 地图
- [RxCoreMotion](https://github、com/RxSwiftCommunity/RxCoreMotion) - 陀螺仪
- [RxAlamofire](https://github、com/RxSwiftCommunity/RxAlamofire) - 网络请求
- [RxCoreData](https://github、com/RxSwiftCommunity/RxCoreData) - CoreData 数据库
- [RxRealm](https://github、com/RxSwiftCommunity/RxRealm) - Realm 数据库
- [RxMediaPicker](https://github、com/RxSwiftCommunity/RxMediaPicker) - 图片选择器
- [Action](https://github、com/RxSwiftCommunity/Action) - 行为
- [RxWebKit](https://github、com/RxSwiftCommunity/RxWebKit) - WebView
- [RxEventHub](https://github、com/RxSwiftCommunity/RxEventHub) - 全局通知
- [RxSwiftExt](https://github、com/RxSwiftCommunity/RxSwiftExt) - 添加一些有用的操作符

### 初级
#### 1、IGListKit 的优点

1、 不要再次调用performBatchUpdates（_ :, completion :)或reloadData（）  
2、 更好的架构与可重复使用的单元和组件
3、 创建具有多种数据类型的Collections
4、 解耦差分算法
5、 完全单元测试
6、 定制您的模型的差异行为
7、 简单的UICollectionView是其核心
8、 可扩展API
9、 使用Objective-C语言，同时全面支持Swift

#### 2、swinject的功能

iOS 依赖注入工具，[https://github、com/Swinject/Swinject](https://github、com/Swinject/Swinject) git链接

#### 3、SnapKit的使用、注意事项与疑惑

#### 使用SnapKit前，一定要先将子控件添加到父视图中

#### leading和left、trailing和right

其实在目前国内App中使用leading与left，trailing与right在正常情况下是等价的，这是因为国内的阅读习惯是从左到右的，不过如果你的App需要在阿拉伯国家上架，他们的布局是从右至左时(比如阿拉伯文) 则会对调。

#### 4、SnapKit优先级

SnapKit为我们提供了三个默认的方法，required、high、medium、low，优先级最大数值是1000

自己设置优先级的值，可以通过priority()方法来设置

#### 5、swift数据库GRDB框架使用

swift数据库，[https://github、com/groue/GRDB、swift](https://github、com/groue/GRDB、swift) git链接

## 内存：
作者：李亚斌
### 初级
#### 1、什么是内存泄漏?什么是内存溢出?

- 内存泄漏指动态分配内存的对象在使用完后没有被系统回收内存,导致对象始终占有着内存,属于内存管理出错, (例如一个对象或者变量使用完成后没有释放,这个对象一直占用着内存)，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光。

- 当程序在申请内存时，没有足够的内存空间供其使用，出现out of memory;比如申请了一个int,但给它存了long才能存下的数，那就是内存溢出。

#### 2、 什么是僵尸对象?什么事野指针？什么是空指针?

- 已经被销毁的对象(不能再使用的对象),内存已经被回收的对象。一个引用计数器为0对象被释放后就变为了僵尸对象;

- 野指针又叫做'悬挂指针', 野指针出现的原因是因为指针没有赋值,或者指针指向的对象已经释放了, 比如指向僵尸对象;野指针可能会指向一块垃圾内存,给野指针发送消息会导致程序崩溃

- 空指针不同于野指针,他是一个没有指向任何内存的指针,空指针是有效指针,值为nil,NULL,Nil,0等,给空指针发送消息不会报错,不会响应消息;

#### 3、 OC对象的内存管理机制?

在iOS中，使用引用计数来管理OC对象的内存

- 一个新创建的OC对象引用计数默认是1，当引用计数减为0，OC对象就会销毁，释放其占用的内存空间

- 调用retain会让OC对象的引用计数+1，调用release会让OC对象的引用计数-1

内存管理的经验总结

- 当调用alloc、new、copy、mutableCopy方法返回了一个对象，在不需要这个对象时，要调用release或者autorelease来释放它

- 想拥有某个对象，就让它的引用计数+1；不想再拥有某个对象，就让它的引用计数-1

可以通过以下私有函数来查看自动释放池的情况

- extern void _objc_autoreleasePoolPrint(void);

#### 4、内存区域分布

在iOS开发过程中，为了合理的分配有限的内存空间，将内存区域分为五个区，由低地址向高地址分类分别是：代码区、常量区、全局静态区、堆、栈。

- 代码段 -- 程序编译产生的二进制的数据

- 常量区 -- 存储常量数据，通常程序结束后由系统自动释放

- 全局静态区 -- 全局区又可分为未初始化全局区：、bss段和初始化全局区：data段。全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域，在程序结束后有系统释放。

- 堆（heap) -- 程序运行过程中,动态分配的内存

- 栈（stack） -- 存放局部变量，临时变量

#### 5、堆区和栈取的区别

按管理方式分

对于栈来讲，是由系统编译器自动管理，不需要程序员手动管理

对于堆来讲，释放工作由程序员手动管理，不及时回收容易产生内存泄露

按分配方式分

堆是动态分配和回收内存的，没有静态分配的堆

栈有两种分配方式：静态分配和动态分配

静态分配是系统编译器完成的，比如局部变量的分配

动态分配是有alloc函数进行分配的，但是栈的动态分配和堆是不同的，它的动 态分配也由系统编译器进行释放，不需要程序员手动管理

#### 6、block在ARC中和MRC中的用法有什么区别,需要注意什么?

1、 对于没有引用外部变量的Block，无论在ARC还是非ARC下，类型都是 NSGlobalBlock，这种类型的block可以理解成一种全局的block，不 需要考虑作用域问题。同时，对他进行Copy或者Retain操作也是无效的
2、 都需要应注意避免循环引用,ARC 下使用__weak 来解决,MRC下使用__Block 来解决;

#### 7、 内存泄漏可能会出现的几种原因？

- 第一种可能：第三方框架不当使用；

- 第二种可能：block循环引用；

- 第三种可能：delegate循环引用；

- 第四种可能：NSTimer循环引用

- 第五种可能：非OC对象内存处理

- 第六种可能：地图类处理

- 第七种可能：大次数循环内存暴涨

#### 8、什么情况使用weak关键字，相比assign有什么不同？

什么情况使用 weak 关键字？

1、在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性

2、自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。在下文也有论述：《IBOutlet连出来的视图属性为什么可以被设置成weak?》

不同点：

1、weak 此特质表明该属性定义了一种“非拥有关系” (nonowning relationship)。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 而 assign 的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。

2、assign 可以用非 OC 对象,而 weak 必须用于 OC 对象

#### 10、weak指针的实现原理

- Runtime维护了一个weak表，用于存储指向某个对象的所有weak指针。weak表其实是一个hash（哈希）表，Key是所指对象的地址，Value是weak指针的地址（这个地址的值是所指对象的地址）数组。

- runtime对注册的类， 会进行布局，对于weak对象会放入一个hash表中。 用weak指向的对象内存地址作为key，当此对象的引用计数为0的时候会dealloc，假如weak指向的对象内存地址是a，那么就会以a为键， 在这个weak表中搜索，找到所有以a为键的weak对象，从而设置为nil。

#### 11、循环引用

#### 1、 概述

iOS内存中的分区有：堆、栈、静态区。其中，栈和静态区是操作系统自己管理回收，不会造成循环引用。在堆中的相互引用无法回收，有可能造成循环引用。

循环引用的实质：多个对象相互之间有强引用，不能施放让系统回收。

解决循环引用一般是将 strong 引用改为 weak 引用。

#### 2、 循环引用场景分析及解决方法

##### 1）父类与子类
```
如：在使用UITableView 的时候，将 UITableView 给 Cell 使用，cell 中的 strong 引用会造成循环引用。

// controller
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

TestTableViewCell *cell =[tableView dequeueReusableCellWithIdentifier:@"UITableViewCellId" forIndexPath:indexPath];

cell.tableView = tableView;

return cell;
}

// cell
@interface TestTableViewCell : UITableViewCell
@property (nonatomic, strong) UITableView *tableView; // strong 造成循环引用
@end

解决：strong 改为 weak

// cell
@interface TestTableViewCell : UITableViewCell
@property (nonatomic, weak) UITableView *tableView; // strong 改为 weak
@end
```
##### 2）block

block在copy时都会对block内部用到的对象进行强引用的。
```
OC:
        self.testObject.testCircleBlock = ^{
            [self doSomething];
        };

Swift:
        self.testObject.testCircleBlock = { _ in
            self.doSomething()
        }

self将block作为自己的属性变量，而在block的方法体里面又引用了 self 本身，此时就很简单的形成了一个循环引用。

应该将 self 改为弱引用

OC:
        __weak typeof(self) weakSelf = self;

        self.testObject.testCircleBlock = ^{
            __strong typeof (weakSelf) strongSelf = weakSelf;
            [strongSelf doSomething];
        };
Swift：
        self.testObject.testCircleBlock = { [weak self] _ in
            guard let `self` = self else { return }
            self.doSomething()
        }
在 ARC 中，在被拷贝的 block 中无论是直接引用 self 还是通过引用 self 的成员变量间接引用 self，该 block 都会 retain self。

- 快速定义宏

// weak obj

/#define WEAK_OBJ(type) __weak typeof(type) weak####type = type;

// strong obj

/#define STRONG_OBJ(type) __strong typeof(type) str####type = weak####type;
```
##### 3）Delegate
```

delegate 属性的声明如下：

@property (nonatomic, weak) id <TestDelegate> delegate;

如果将 weak 改为 strong，则会造成循环引用

// self -> AViewController

BViewController *bVc = [BViewController new];

bVc = self; 
[self.navigationController pushViewController: bVc animated:YES];

// 假如是 strong 的情况

// bVc、delegate ===> AViewController (也就是 A 的引用计数 + 1)

// AViewController 本身又是引用了 <BViewControllerDelegate> ===> delegate 引用计数 + 1

// 导致： AViewController <======> Delegate ，也就循环引用啦
```
##### 4）NSTimer
```

NSTimer 的 target 对传入的参数都是强引用（即使是 weak 对象）

解决办法: 《Effective Objective-C 》中的52条方法

#import <Foundation/Foundation、h>

@interface NSTimer (YPQBlocksSupport)

- (NSTimer *)ypq_scheduledTimeWithTimeInterval:(NSTimeInterval)interval block:(void(^)())block repeats:(BOOL)repeats;

@end

#import "NSTimer+YPQBlocksSupport.h"

@implementation NSTimer (YPQBlocksSupport)

- (NSTimer *)ypq_scheduledTimeWithTimeInterval:(NSTimeInterval)interval
	block:(void(^)())block repeats:(BOOL)repeats{
	return [self scheduledTimerWithTimeInterval:interval target:self
	selector:@selector(ypq_blockInvoke:) userInfo:[block copy]
	repeats:repeats];}
- (void)ypq_blockInvoke:(NSTimer *)timer{
	void (^block)() = timer.userInfo;
	if(block) {
	    block();
	}}

@end

使用方式：

__weak ViewController * weakSelf = self;
[NSTimer ypq_scheduledTimeWithTimeInterval:4、0f block:^{
    ViewController * strongSelf = weakSelf;
    [strongSelf afterThreeSecondBeginAction];
} repeats:YES];
```
计时器保留其目标对象，反复执行任务导致的循环，确实要注意，另外在dealloc的时候，不要忘了调用计时器中的 invalidate方法。

#### 12、Swift循环引用解决方法?

1、 转换为值类型, 只有类会存在引用循环, 所以如果能不用类, 是可以解引用循环的,
2、 delegate 使用 weak 属性、
3、 闭包中, 对有可能发生循环引用的对象, 使用 weak 或者 unowned, 修饰

## 数据结构

#### 1、Q: 数组和链表的区别  

A: 链表是链式的存储结构;数组是顺序的存储结构。 链表通过指针来连接元素与元素,数组则是把所有元素按次序依次存储。  

#### 2、Q: 数组和链表的优缺点  

A: 链表的插入删除元素相对数组较为简单,不需要移动元素,且较为容易实现长度扩充,但是寻找某个元素较为困难; 数组寻找某个元素较为简单,但插入与删除比较复杂,由于最大长度需要再编程一开始时指定,故当达到最大长度时,扩充长度不如链表方便。  

#### 3、Q: 双向链表和单向链表的区别  

A: 双向链表的每个数据结点中都有两个指针，分别指向直接后继和直接前驱。所以，从双向链表中的任意一个结点开始，都可以很方便地访问它的前驱结点和后继结点。  

#### 4、Q: 单向链表反转的几种算法  

A: 三指针法:三个指针记录修改节点，一步步往前反转。递归法：通过校验后节点非空条件递归，实现从后到前操作链表。  

#### 5Q: 单向链表两两反转算法  

A: 思路，在三指针算法基础上加一个指针记录上一组反转后的后元素。
[新页面](%E6%96%B0%E9%A1%B5%E9%9D%A2/%E6%96%B0%E9%A1%B5%E9%9D%A2、md)

## 组件库
作者：郑涤新
#### 1、 pod update 和pod install 两个命令有什么区别？
> update命令会将相应的库更新到最新的或指定的版本
Install是加理添加或移除库的依赖关系，并遵守Podfile、lock中的版本锁定

#### 2、 cocoapods创建一个组件库的命令是什么？
> pod lib create pod name

#### 3、静态库能不能依赖动态库，反之，动态库能不能依赖静态库？
>静态库可以依赖动态库和静态库，动态库可以依赖动态库,不能依赖静态库

#### 4、组件化方案？组件化的优点有什么？
>注：该题没有标准答案
基本分为基础层(网络，数据库，工具类)，中间层（功能实现、剥离UI的业务实现），业务层（登录）
优点：1、模块间解耦 2、模块重用 3、便于单元测试 4、提高团队协作开发效率

#### 5、组件间建通信有哪些？
>主流有两种，一种是基于URL的统跳方案，例如MGJRouter。一种基于反射的调用封装，如CTMediator

#### 6、在模块化/组件化的项目中，Swift 和 ObjC 怎么混编？
>Swift 调用 Objective-C
正确的做法是将 Build Settings 中的 Defines Module 选项设置为 YES，然后新建一个 umbrella header，再将需要暴露给（内部的） Swift 调用的 ObjC 的头文件在这个 umbrella header 中导入

>Objective-C 调用 Swift
同样也要将 Build Settings 中的 Defines Module 选项设置为 YES，然后在要引用 Swift 代码的 ObjC 文件中导入编译器生成的头文件 #import <ProductName/ProductModuleName-Swift、h>
  •  Swift 类中将需要暴露给 Objective-C 模块引用的类，用 public 申明
  •  Swift 类中需要暴露给 Objective-C 的方法要用关键字 @objc


#### 7、有没有使用过fastlane，简述使用的操作步骤。项目多证书多环境打包如何做？
>在项目根目录执行fastlane init初始化，然后打开fastfile编写配置。
、xcconfig就是解决不同证书不同环境的复杂问题

>有没有使用、搭建Jenkins，以及打包脚本的编写

#### 8、iOS自动化测试方案有哪些？
>Xcode自带的XCTest,UITest。流行框架Appium，Kiwi，OCMock

#### 9、简述iOS编译过程



## 代码质量 
### 中级
#### 1、如果自定义类继承某个超类或者要遵从某个协议的话，该如何处理 

如果自定义的类继承于某个超类，则必须引入定义那个超类的头文件，如果要遵从某个协议，尽量把该类遵循某协议的声明移到分类中。如果不行的话，就把协议单独放在一个头文件中，然后将其引入。

#### 2、字面量语法和等价语法的声明区别 ，字面量语法相对于等价语法的好处

_//_等价语法

NSNumber *intNumber = [NSNumber numberWithInt:1];

_//_字面量

NSNumber *intNum = @3.14;

复制代码

使用字面量的好处：假如创建一个含有3个对象的数组，第二个对象为nil，其他两个对象都有效，如果用字面量语法创建，则在运行时会抛出异常，可以更快找到错误。而如果用arrayWithObjects：方法，则不会抛出异常，但创建的数组会只包含第一个对象，因为该方法会依次处理各个参数，直到发现nil为止。这样会导致错误不容易被发现。

#### 3、类型常量声明有什么注意事项

答 变量一定要同时用static于const来声明。const修饰符可以保护变量不被修改。static修饰符则意味着仅在定义此变量的编译单元中可见。如果不加static，在另一个编译单元也声明了同名变量就会报错。这样创建的常量是不公开的。 如果需要对外公开某个常量，就需要常量放在全局符号表中，以便可以在定义该常量的编译单元之外

@interface xxx : xxx

// ...

@end

在.m文件里引入该类#import "QiShareClass.h" 

多用字面量语法，少用与之等价的语法

多用类型常量，少用#define预处理器指令

用枚举表示状态、选项、状态码

#### 4、 codeReview 注意事项

答：主要根据团队设定的代码规范，来review团队成员的代码，大致有以下几个方面：

1）代码有没有不符合代码规范的

比如：命名、注释

2）代码有没有（业务/算法）逻辑错误

比如：功能与需求有偏差；参数传递顺序出错；方法的边界条件有没有考虑等

3）代码有没有回归错误

比如：之前的功能回归测试不通过

4）代码有没有潜在性能问题

比如：考虑大数据量、大并发量下的性能下sql是否有问题？是否会有内存泄露？死锁等

5）代码有没有其他待改进的地方

比如可扩展性/过度设计

####  4、规范检测   - Swift lint   （中级） 

 A  问 Swift lint  是什么

答SwiftLint 是 realm 公司开发的一个插件，专门用于管理 Swift 代码的规范

SwiftLint 的工作原理是检查 Swift 代码编译过程中的 AST 和 SourceKit 环节，从而可以摆脱不同版本 Swift 语法变化的影响。

### 初级
#### 1、 为了减少编译时间，从代码层考虑，如何做处理？

为了减少编译时间，.h文件中尽量少引入其他头文件，必要时可以考虑在.h文件里“向前声明”该类 @class QiShareClass;
####  2、代码规范的要点 
答 代码规范主要分为风格规范与设计规范两大类

## 监控体系 
### 高级
####  1、卡顿监控   
卡顿的监控和处理 ： [www.jianshu.com/p/399a661b3…](https://link.juejin.cn/?target=https://www.jianshu.com/p/399a661b3f43)

####  2、耗时监控   （推荐iOS 性能监控工具  [QiLagMonitor ]

([links.jianshu.com/go?to=https…](https://link.juejin.cn/?target=https://links.jianshu.com/go?to=https://github.com/QiShare/Qi_ObjcMsgHook/tree/master/Qi_ObjcMsgHook/Qi_ObjcMsgHook/QiLagMonitor))

 A  问 什么是 hook ？

答 定义：hook是指在原有方法开始执行时，换成你指定的方法。或在原有方法的执行前后，添加执行你指定的方法。从而达到改变指定方法的目的。

例如：

使用runtime 的 Method Swizzle。

使用Facebook所开源的[fishhook](https://link.juejin.cn/?target=https://links.jianshu.com/go?to=https://github.com/facebook/fishhook)框架。

 B  问  fishhook 的大致实现思路是什么 

答 动态链接器dyld会根据Mach-O二进制可执行文件的符号表来绑定符号。而通过符号表及符号名就可以知道指针访问的地址，再通过更改指针访问的地址就能替换指定的方法实现了。

 C  问为什么 hook 了 objc_msgSend 就可以掌握所有 objc 方法的耗时？ 
答 因为objc_msgSend是所有Objective-C方法调用的必经之路，所有的Objective-C方法都会调用到运行时底层的objc_msgSend方法。所以只要我们可以hook objc_msgSend，我们就可以掌握所有objc方法的耗时。

 D  问如何分析 iOS 启动耗时 

答 启动耗时分析，一般我们会以main函数作为分割点，main之前和main之后main之前称为per-main 阶段。这个由dyld给你反馈应用的耗时。main之后由开发者自己检测。我们可以从main开始打点，到第一个页面显示为止。

各个函数的调用顺序如下：

启动页

main() UIApplicationMain()

willFinishLaunchingWithOptions()

didFinishLaunchingWithOptions()

loadView()

viewDidLoad()

applicationDidBecomeActive()

启动页是在main()函数调用之前出来的，main()是程序的入口，里面调用了UIApplicationMain()。当App从didFinishLaunchingWithOptions()返回的时候，实际的UI立刻开始加载，但是在applicationDidBecomeActive()这个回调完成之前，UI即使已经初始化，但仍旧被阻塞着。

总的启动时间T包括main()调用之前的pre-main timeT0，

加上从main()到applicationDidBecomeActive()的时间T1。

 E iOS 启动优化 

[www.jianshu.com/p/e6b194ddd…](https://link.juejin.cn/?target=https://www.jianshu.com/p/e6b194dddefb)

 F iOS  异常处理 

I 奔溃处理 系统崩溃

对于系统崩溃而引起的程序异常退出，可以通过NSSetUncaughtExceptionHandler机制捕获，代码：实现一个用于处理异常的方法

II 热更新

常用热更新框架 JSPatch lua脚本 Weex React Native  Hybrid  DynamicCocoa

### 中级
####  1、crash   

 A  问 符号表的意义

答：1）概念：符号表就是指在Xcode项目编译后，在编译生成的.app的同级目录下生成的同名的.dSYM文件。

.dSYM文件其实是一个目录，在子目录中包含了一个16进制的保存函数地址映射信息的中转文件，所有Debug的symbols都在这个文件中(包括文件名、函数名、行号等)，所以也称之为调试符号信息文件。

2）作用：符号表就是用来符号化 crash log（崩溃日志）。crash log中有一些方法16进制的内存地址等，通过符号表就能找到对应的能够直观看到的方法名之类。

3）获取途径：在Archive的时候会生成.xcarchive文件，然后显示包内容就能够在里面找到.dsYM文件和.app文件。

 B  问 Crash 如何捕获

答：iOS端的crash分为两类，一类是NSException异常，另外一类是Signal信号异常。这两类异常我们都可以通过注册相关函数来捕获。[www.jianshu.com/p/84b842ce9…](https://link.juejin.cn/?target=https://www.jianshu.com/p/84b842ce9b3c)

 C  问  bugly  检测卡顿的原理

答 Runloop的两次source[kCFRunLoopBeforeSources 和 kCFRunLoopAfterWaiting]的监控 创建信号量的方式 

渲染界面的频率来监控帧率

 D  问  bugly 检测崩溃的原理

答 一类是NSException异常，另外一类是Signal信号异常。这两类异常我们都可以通过注册相关函数来捕获

 E  问符号化的概念

答：就是我们要获取到其他的堆栈信息的时候 我们就需要符号化数据了 通过 dSYM 文件提取地址和符号的对应关系，进行符号还原

####  2、错误码监控 

 A iOS 开发常见错误码

答 AVFoundation 播放错误码—AVError ，[NSURLError](https://link.juejin.cn/?target=https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc) 链接错误码 

附：[www.jianshu.com/p/c5438478c…](https://link.juejin.cn/?target=https://www.jianshu.com/p/c5438478cf0d)




## 安全 
### 中级
####  1、数据加密 

 A  问数据加密算法有哪些 

答 编码方式 Base64  Base58

哈希(散列)函数 MD5（消息摘要算法） SHA1  SHA256  SHA512

对称加密算法 DES   AES

 B  问 MD5 加密是否可逆 为什么

答：MD5不可逆的原因是由于它是一种散列函数(也叫哈希函数,杂凑函数,他是一个单向密码体制,即从明文到密文的不可逆映射,只有加密过程没有解密过程)。

 C  问如何进行签名校验 

答 [www.jianshu.com/p/1554dc3e6…](https://link.juejin.cn/?target=https://www.jianshu.com/p/1554dc3e6b02)
### 初级

####  1、 沙盒 / 域 

 A  问 iOS 沙盒机制介绍 

答 iOS中的沙盒机制是一种安全体系。为了保证系统安全，iOS每个应用程序在安装时，会创建属于自己的沙盒文件（存储空间）。应用程序只能访问自身的沙盒文件，不能访问其他应用程序的沙盒文件，当应用程序需要向外部请求或接收数据时，都需要经过权限认证，否则，无法获取到数据。所有的非代码文件都要保存在此，例如属性文件plist、文本文件、图像、图标、媒体资源等，其原理是通过重定向技术，把程序生成和修改的文件定向到自身文件夹中。

 B  问 iOS  什么数据保存在 Cache 

答：1.缓存数据应该保存在/Library/Caches目录下.  
2.缓存数据在设备低存储空间时可能会被删除，iTunes或iCloud不会对其进行备份。  
3.可以保存重新下载或生成的数据，而且没有这些数据也不会妨碍用户离线使用应用的功能。  
4.当访问网络时系统自动会把访问的url,以数据库的方式存放在此目录下面.

####  2、网络安全 

 A  问网络安全的原则 

答 1）在网络上不允许传输用户隐私数据的明文。

2）在本地不允许保存用户隐私数据的明文。

 B  问网络请求方法选择 

答 1.一定要使用POST请求提交用户的隐私数据。

2.GET请求的所有参数都直接暴露在URL中。

3.请求的URL一般会记录在服务器的访问日志中。

4.服务器的访问日志是黑客攻击的重点对象之一。

## iOS、路由、组件化、Target-Action 
### 高级
#### 1、Target-Action  路由方案的技术实现。

- 通过Target-Action的方式，创建一个Target类，Target类里面定义一些Action方法，这些方法的结果是返回一个Controller或其它Object对象。
- 在每个组件中，给Mediator中介类创建一个对应这个组件的分类，来保证Mediator中介类的代码纯净性。
- 在每个组件Mediator的分类中，定义组件外部可调用的接口方法。接口方法内部，通过统一调用Mediator中介类的“performTarget: action: params: shouldCacheTarget:”方法，实现组件间解耦。（即调用者即使不导入其它组件的头文件，也能调用其它组件。
- 在Mediator中介类的“performTarget: action: params: shouldCacheTarget:”方法中，主要通过Runtime中的NSClassFromString获取Target类，通过NSSelectorFromString获取Target类中的Action方法名。
- 路由方案最终实现是通过获取到的Target类和Target类中的Action方法名，去匹配已经创建好的Target类，由Target类中的Action方法，真正的实现创建需要的控制器或对象。将这些对象作为方法的返回值，传递给调用者。（即在Target类中的Action方法中，才能真正调用当前组件控制器的功能。）

#### 2、你是如何组件化解耦的？

- 分层基础功能组件：按功能分库，不涉及产品业务需求，跟库Library类似，通过良好的接口拱上层业务组件调用；不写入产品定制逻辑，通过扩展接口完成定制；基础UI组件：各个业务模块依赖使用，但需要保持好定制扩展的设计业务组件：业务功能间相对独立，相互间没有Model共享的依赖；业务之间的页面调用只能通过UIBus进行跳转；业务之间的逻辑Action调用只能通过服务提供；  
- 中间件：target-action，url-block，protocol-class  

#### 3、为什么 CTMediator 方案优于基于 Router 的方案？ 

Router的缺点：

- 在组件化的实施过程中，注册URL并不是充分必要条件。组件是不需要向组件管理器注册URL的，注册了URL之后，会造成不必要的内存常驻。注册URL的目的其实是一个服务发现的过程，在iOS领域中，服务发现的方式是不需要通过主动注册的，使用runtime就可以了。另外，注册部分的代码的维护是一个相对麻烦的事情，每一次支持新调用时，都要去维护一次注册列表。如果有调用被弃用了，是经常会忘记删项目的。runtime由于不存在注册过程，那就也不会产生维护的操作，维护成本就降低了。 由于通过runtime做到了服务的自动发现，拓展调用接口的任务就仅在于各自的模块，任何一次新接口添加，新业务添加，都不必去主工程做操作，十分透明。  
- 在iOS领域里，一定是组件化的中间件为openURL提供服务，而不是openURL方式为组件化提供服务。如果在给App实施组件化方案的过程中是基于openURL的方案的话，有一个致命缺陷：非常规对象(不能被字符串化到URL中的对象，例如UIImage)无法参与本地组件间调度。 在本地调用中使用URL的方式其实是不必要的，如果业务工程师在本地间调度时需要给出URL，那么就不可避免要提供params，在调用时要提供哪些params是业务工程师很容易懵逼的地方。  
- 为了支持传递非常规参数，蘑菇街的方案采用了protocol，这个会侵入业务。由于业务中的某个对象需要被调用，因此必须要符合某个可被调用的protocol，然而这个protocol又不存在于当前业务领域，于是当前业务就不得不依赖public Protocol。这对于将来的业务迁移是有非常大的影响的。  

CTMediator的优点：

- 调用时，区分了本地应用调用和远程应用调用。本地应用调用为远程应用调用提供服务。  
- 组件仅通过Action暴露可调用接口，模块与模块之间的接口被固化在了Target-Action这一层，避免了实施组件化的改造过程中，对Business的侵入，同时也提高了组件化接口的可维护性。  
- 方便传递各种类型的参数。  

#### 4、抓包 

 A  问抓包原理 

答：一般抓包都是通过代理服务来冒充你的服务器，客户端真正交互的是这个假冒的代理服务，这个假冒的服务再和我们真正的服务交互，这个代理就是一个中间者 ，我们所有的数据都会通过这个中间者，所以我们的数据就会被抓取。HTTPS 也同样会被这个中间者伪造的证书来获取我们加密的数据

 B  问如何防止抓包

[juejin.cn/post/696401…](https://juejin.cn/post/6964011870554439693)

### 中级
####  1、iOS路由、组件化、Target-Action、方案优缺点 

Target-Action方案的优点

- Target-Action方案，只存在组件依赖Mediator中介的这一层依赖关系。  
- 在每个组件中创建Mediator的Category分类，针对维护Mediator的Category分类即可。  
- 每个组件的Category对应一个Target类，Categroy中的Action方法对应Target类中的Action场景。  
- Target-Action方案统一了所有组件间调用入口。都是调用“performTarget: action: params: shouldCacheTarget:”方法。第三个参数是一个字典，这个字典里面可以传很多参数，只要Key-Value写好就可以了。  
- Target-Action方案处理错误的方式也统一在一个地方了，Target没有，或者是Target无法响应的Action方法，都可以在Mediator这里进行统一出错处理。  
- Target-Action方案也能有一定的安全保证，它对URL中的Native前缀进行安全验证。  
- 因此，Target-Action方案不管从维护性、可读性、扩展性来说，都是一个比较完美的方案。  
- 充分的利用Runtime的特性，实现了组件间服务的自动发现，无需注册即可实现组件间的调用。  

Target-Action方案的缺点

- Target_Action在Category中将常规参数打包成字典，在Target处再把字典拆包成常规参数，这就造成了一部分的硬编码。
#### 2、WKWebView优势缺点
	优势
- 运行速度更快，占用内存低，大概是UIwebView 的四分之一到三分之一  
- 多进程，在APP的主进程之外执行；为空webView为多进程组件，他会从APP内存中分离内存到单独的进程中。当内存超过了系统分配给为空webView的内存时候，会导致为空webView浏览器崩溃白屏，但是APP不会crash。（APP会收到系统通知，并且尝试去重新加载页面）  
- 相反的UIwebView 和APP同一个进程，内存不够用时就会crash ，从而导致APP crash  
- wkwebview 使用和手机Safari浏览器一样的nitro JavaScript引擎，相比于UIwebView的javaScript 引擎有非常大的性能提升  
- wkwebview 是异步处理APP原生代码与JavaScript之间的通信，因此普遍上执行速度会更快  
- 消除了触摸延迟  
	缺点：
- 不能截屏  
- 不支持记录webkit 的请求  
- APP退出会清湖HTML5的本地存储数据  
- 不支持accept cookies 的设置  

#### 3、提高小程序的应用速度的方法（小程序） 

- 减少默认data的大小  
- 组件化方案，公用的如弹框等写个自定义的组件，然后调用 

#### 4、MVVM和 MVC  的区别  
[www.jianshu.com/p/d0bc12a63…](https://link.juejin.cn/?target=https://www.jianshu.com/p/d0bc12a63ccf)

#### 5、iOS   三种工厂模式  ( 简单工厂模式、工厂模式、抽象工厂模式 ) 
[www.jianshu.com/p/847af218b…](https://link.juejin.cn/?target=https://www.jianshu.com/p/847af218b1f0)

#### 6、设计模式的基本原则  

设计原则和规矩一样，是用来规范也是用来打破的。并非所有的时候我们都需要完全准守设计原则，准守设计原则可以减少遇到不必要的麻烦，但是有时候如果完全准守原则，你可能寸步难行。灵活变通，才是王道。

单一职责原则：

也就是我们常说的一个类负责一个职责，虽然这个对于程序猿们是一个常识，但是事件经常会发展到我们无法控制的地步。

比如刚开始的时候类T只负责一个职责P。后来由于某种原因，也许是需求变更了，需要将职责P细分为粒度更细的职责P1，P2，这时如果要使程序遵循单一职责原则，需要将类T也分解为两个类T1和T2，分别负责P1、P2两个职责。但是在程序已经写好的情况下，这样做简直太费时间了。所以，简单的修改类T，用它来负责两个职责是一个比较不错的选择，虽然这样做有悖于单一职责原则。（这样做的风险在于职责扩散的不确定性，因为我们不会想到这个职责P，在未来可能会扩散为P1，P2，P3，P4……Pn。所以记住，在职责扩散到我们无法控制的程度之前，立刻对代码进行重构。）

里氏替换原则：

这项原则最早是在1988年，由麻省理工学院的Barbara Liskov女士提出来的。

定义1：如果对每一个类型为 T1的对象 o1，都有类型为 T2 的对象o2，使得以 T1定义的所有程序 P 在所有的对象 o1 都代换成 o2 时，程序 P 的行为没有发生变化，那么类型 T2 是类型 T1 的子类型。

定义2：所有引用基类的地方必须能透明地使用其子类的对象。

看完定义大致也知道这个是个和继承相关的定义。其实就是我们通常使用继承时子类尽量不要去修改父类的功能。这也是为什么我们在继承父类方法进行扩展的时候通常都会代用super的原因。

依赖倒置原则：

定义：高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。（抽象指的是接口或者抽象类，细节就是具体的实现。）

在面向过程的开发，上层调用下层，上层依赖于下层，当下层剧烈变动时上层也要跟着变动，这就会导致模块的复用性降低而且大大提高了开发的成本。

而我们iOS是面向对象的开发，很好的解决了这个问题，一般情况下抽象的变化概率很小，让用户程序依赖于抽象，实现的细节也依赖于抽象。即使实现细节不断变动，只要抽象不变，客户程序就不需要变化。这大大降低了客户程序与实现细节的耦合度。

简而言之就是某些可能改变的参数不要在底层的模块进行实现，而是通过传递参数，block块等方式进行传递。

接口隔离原则：

定义：客户端不应该依赖它不需要的接口；一个类对另一个类的依赖应该建立在最小的接口上。

还是以苹果的UITableViewDelegate为例，在设计时定义了部分必须实现的基本方法（最小接口）。那么其他的接口只需要在必要时才实现。而不是需要实现所有接口才能使用某个功能。

迪米特法则：

定义：一个对象应该对其他对象保持最少的了解。

问题由来：类与类之间的关系越密切，耦合度越大，当一个类发生改变时，对另一个类的影响也越大。

解决方案：尽量降低类与类之间的耦合。

软件编程的总的原则：低耦合，高内聚。无论是面向过程编程还是面向对象编程，只有使各个模块之间的耦合尽量的低，才能提高代码的复用率。低耦合的优点不言而喻，但是怎么样编程才能做到低耦合呢？那正是迪米特法则要去完成的。

迪米特法则又叫最少知道原则，最早是在1987年由美国Northeastern University的Ian Holland提出。通俗的来讲，就是一个类对自己依赖的类知道的越少越好。也就是说，对于被依赖的类来说，无论逻辑多么复杂，都尽量地的将逻辑封装在类的内部，对外除了提供的public方法，不对外泄漏任何信息。迪米特法则还有一个更简单的定义：只与直接的朋友通信。首先来解释一下什么是直接的朋友：每个对象都会与其他对象有耦合关系，只要两个对象之间有耦合关系，我们就说这两个对象之间是朋友关系。耦合的方式很多，依赖、关联、组合、聚合等。其中，我们称出现成员变量、方法参数、方法返回值中的类为直接的朋友，而出现在局部变量中的类则不是直接的朋友。也就是说，陌生的类最好不要作为局部变量的形式出现在类的内部。

开闭原则：

开闭原则中“开”，是指对于组件功能的扩展是开放的，是允许对其进行功能扩展的；开闭原则中“闭”，是指对于原有代码的修改是封闭的，即修改原有的代码对外部的使用是透明的。

#### 7、iOS   单例实现方式与优缺点 

[www.jianshu.com/p/d1856ad6b…](https://link.juejin.cn/?target=https://www.jianshu.com/p/d1856ad6b4fb)

#### 8、组件化有什么好处？

- 业务分层、解耦，使代码变得可维护；  
- 有效的拆分、组织日益庞大的工程代码，使工程目录变得可维护；  
- 便于各业务功能拆分、抽离，实现真正的功能复用；  
- 业务隔离，跨团队开发代码控制和版本风险控制的实现；  
- 模块化对代码的封装性、合理性都有一定的要求，提升开发同学的设计能力；  
- 在维护好各级组件的情况下，随意组合满足不同客户需求；（只需要将之前的多个业务组件模块在新的主App中进行组装即可快速迭代出下一个全新App）


#### 9、iOS皮肤切换方案   换肤的实现 

[www.jianshu.com/p/676bfde0c…](https://link.juejin.cn/?target=https://www.jianshu.com/p/676bfde0c02c)

#### 10、iOS编译速度优化 

swift 类型检查耗时

对于 swift 来说，编译耗时的主要就是类型检查 在 xcode => build settings => Other Swift Flags 添加下面设置，可以看到 swift 的表达式和函数的 类型检查的时长。并给出警告。

项目设置优化

使用 New Xcode Build System

dSYM

可以设置在 debug 不生成 dSYM，只在 release 生成。

模块化

可以把 pod 等第三方库先打包成 .a 文件，然后再放入项目里面，这样 pod 的第三方库就编译时间就会减少。但是这样一来 调试就不方便了，所以这是个取舍问题。

代码优化

- 闭包和 lazy不推荐

#### 11、iOS国际化都涉及到哪些，怎样实现 

一、项目内的语言国际化

二、app名字国际化设置

[blog.csdn.net/shanghaibao…](https://link.juejin.cn/?target=https://blog.csdn.net/shanghaibao123/article/details/107323395/) 

### 初级
#### 1、main()和 runApp() 函数在 flutter 的作用分别是什么？有什么关系吗？（flutter ）
- main函数是类似于java语言的程序运行入口函数  
- runApp函数是渲染根widget树的函数  
- 一般情况下runApp函数会在main函数里执行  

#### 2、什么是  widget?   在flutter 里有几种类型的 widget ？分别有什么区别？能分别说一下生命周期吗？  （flutter ）
- widget在flutter里基本是一些UI组件  
- 有两种类型的widget，分别是statefulWidget 和 statelessWidget两种  
- statelessWidget不会自己重新构建自己，但是statefulWidget会   

#### 3、Hot Restart   和   Hot Reload   有什么区别吗？（flutter ）

- Hot Reload比Hot Restart快，Hot Reload会编译我们文件里新加的代码并发送给dart虚拟机，dart会更新widgets来改变UI，而Hot Restart会让dart 虚拟机重新编译应用。另一方面也是因为这样， Hot Reload会保留之前的state，而Hot Restart回你重置所有的state回到初始值
 
#### 4、生命周期函数（小程序） 

- onLoad——页面加载，调一次  
- onShow——页面显示，每次打开页面都调用  
- onReady——初次渲染完成，调一次  
- onHide——页面隐藏，当navigateTo或底部tab切换时调用  
- onUnload——页面卸载，当redirectTo或navigateBack时调用  

#### 5、基于CTMediator的组件化方案，有哪些核心组成？

- CTMediator中间件：集成就可以了  
- 模块Target_%@：模块的实现及提供对外的方法调用Action_methodName，需要传参数时，都统一以NSDictionary*的形式传入。  
- CTMediator+%@扩展：扩展里声明了模块业务的对外接口，参数明确，这样外部调用者可以很容易理解如何调用接口。  

#### 6、iOS中的函数响应式编程思想

响应式编程是一种和事件流有关的编程模式，关注导致状态值改变的行为事件，一系列事件组成了事件流。一系列事件是导致属性值发生变化的原因。FRP非常类似于设计模式里的观察者模式。如果使用FRP，c的值将会随着b的值改变而改变，所以叫做「响应式编程」。比较直观的例子就是Excel，当改变某一个单元格的内容时，该单元格相关的计算结果也会随之改变。 FRP提供了一种信号机制来实现这样的效果，通过信号来记录值的变化。信号可以被叠加、分割或合并。通过对信号的组合，就不需要去监听某个值或事件。

## iOS 派发机制面试
### 中级
#### 1、 Swift是怎样派发的，区别是什么？

   值类型总是会使用直接派发, 简单易懂

   而协议和类的 extension 都会使用直接派发

   NSObject 的 extension 会使用消息机制进行派发

   NSObject 声明作用域里的函数都会使用函数表进行派发.

   协议里声明的, 并且带有默认实现的函数会使用函数表进行派发

#### 2、swift里面常用的指定派发方式?

   - 添加final关键字的函数使用直接派发

   - 添加static关键字函数使用直接派发

   - 添加dynamic关键字函数使用消息派发

   - 添加@objc关键字的函数使用消息派发

   - 添加@inline关键字的函数会告诉编译器可以使用直接派发
### 初级
#### 1、 Swift派发机制有哪几种
   静态派发（直接派发）、函数表派发、消息派发
   
#### 2、谈谈你对派发机制的理解？为什么oc的消息转发也很快？

   Swift多用结构体，多用协议，意思就是多直接派发，提高性能。

   Oc的消息转发机制：逐层指针查找，然后会有个缓存过程，所以后边也不会慢很多

#### 3、 Objective-C 消息转发

   NSObject 是由 meta class 创建的对象。向对象发送某个消息的时候，编译器会调用底层的 obj_msgSend() C函数，从缓存和 方法列表中，寻找对象的函数指针（IMP），如果找到，则执行。否则继续根据一定的策略或者方式继续找，最终如果没有找到，则让程序 Crash， 报一个异常：unrecognized selector sent to instance

#### 4、 Objective-C 消息寻找顺序

   1. 类自身方法实现
   2. 父类的方法实现
   3. 动态模式
   4. 快速前向模式
   5. 常规前向模式
      - 动态模式：在当前类，将原来的方法替换掉或者延迟加载；
      - 快速前向模式：使用一个代理对象，实现具体的方法，不局限于当前类。
      - 常规前向模式：可以使用多个不同代理对象，实现不同的方法。不局限于一个代理对象。

#### 5、 iOS中的crash防护 unrecognized selector sent to instance

   消息转发流程中hooc  [解析](https://www.jianshu.com/p/5db9f556f204)
   ## iOS 网络通讯面试题
### 高级
#### 1、 什么是对称加密、什么是非对称加密

   - 对称加密是最快速、最简单的一种加密方式，加密（encryption）与解密（decryption）用的是同样的密钥（secret key）。对称加密有很多种算法，由于它效率很高，所以被广泛使用在很多加密协议的核心当中。 常见的有AES,DES,3DES等
   - 非对称加密为数据的加密与解密提供了一个非常安全的方法，它使用了一对密钥，公钥（public key）和私钥（private key）。私钥只能由一方安全保管，不能外泄，而公钥则可以发给任何请求它的人。非对称加密使用这对密钥中的一个进行加密，而解密则需要另一个密钥。常见的: RSA算法

#### 2、 iOS中 https证书验证
- NSURLSession 实现 HTTPS
   具体到使用 NSURLSession 走 HTTPS 访问网站，`-URLSession:didReceiveChallenge:completionHandler:` 回调中会收到一个 challenge，也就是质询，需要你提供认证信息才能完成连接。这时候可以通过 `challenge.protectionSpace.authenticationMethod` 取得保护空间要求我们认证的方式，如果这个值是 `NSURLAuthenticationMethodServerTrust` 的话，我们就可以插手 TLS 握手中“验证数字证书有效性”这一步

   [详细解析](https://www.jianshu.com/p/31bcddf44b8d) 

   [iOS Alamofire：Https身份验证](https://zhuanlan.zhihu.com/p/344139496)

#### 3、 http常见的状态码，以及图片缓存框架中处理后端不更换url直接替换图片，APP怎么更新已缓存图片
   1xx	Informational（信息性状态码）	接受的请求正在处理
   2xx	Success（成功状态码）	请求正常处理完毕
   3xx	Redirection（重定向）	需要进行附加操作以完成请求
   4xx	Client error（客户端错误）	客户端请求出错，服务器无法处理请求
   5xx	Server Error（服务器错误）	服务器处理请求出错
   304状态码可判断服务器资源是否有更新
#### 4、alamofire、moya源码看过么，简单说说
### 中级
#### 1 什么是三次握手
所谓三次握手（Three-Way Handshake）即建立TCP连接，是指建立一个TCP连接时，需要客户端和服务端总共发送3个包以确认连接的建立。

   1. 第一次握手:客户端发送 syn 包(syn=j)到服务器，并进入 SYN_SEND 状态，等待服 务器确认;
   2. 第二次握手:服务器收到 syn 包，必须确认客户的 SYN(ack=j+1)，同时自己也发 送一个 SYN 包(syn=k)，即 SYN+ACK 包，此时服务器进入 SYN_RECV 状态;
   3. 第三次握手:客户端收到服务器的 SYN+ACK 包，向服务器发送确认包 ACK(ack=k+1)， 此包发送完毕，客户端和服务器进入 ESTABLISHED 状态，完成三次握手。 握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始 传送数据
#### 2、 什么是四次挥手
   由于TCP连接是全双工的，因此每个方向都必须单独进行关闭。这原则是当一方完成它的数据发送任务后就能发送一个FIN来终止这个方向的连接。收到一个 FIN只意味着这一方向上没有数据流动，一个TCP连接在收到一个FIN后仍能发送数据。首先进行关闭的一方将执行主动关闭，而另一方执行被动关闭。

   1. TCP客户端发送一个FIN，用来关闭客户到服务器的数据传送。
   2. 服务器收到这个FIN，它发回一个ACK，确认序号为收到的序号加1。和SYN一样，一个FIN将占用一个序号。
   3. 服务器关闭客户端的连接，发送一个FIN给客户端。
   4. 客户端发回ACK报文确认，并将确认序号设置为收到序号加1。

#### 3、 断点续传如何实现

   通过HTTP，可以非常方便的实现断点续传。

   - 断点续传主要依赖于HTTP头部定义的Range，应用可以通过HTTP请求曾经获取失败的资源的某一个返回或者部分来恢复下载该资源。当然并不是所有风服务器都支持Range，所以不支持Range的不在我们考虑之内。Range是以字节计算的，请求的时候不比给我结尾字节数，因为请求方并不一定知道资源的大小。

   通过这个关键字可以告诉服务器返回哪些数据给我。
    比如:

   - bytes=500-999 表示第500-第999字节
   - bytes=500- 表示从第500字节往后的所有字节
   - 然后我们再根据服务器返回的数据，将得到的data数据拼接到文件后面,就可以实现断点续传了。
### 初级
#### 1、 说一下http中get和post请求的区别？

   - get 一般用于从服务端获取数据,post 用于向服务端发送数据
   - get 参数拼接在 url 地址里面, post 参数则放在其包体里,post 比 get 稍安全,隐秘
   - get 可以被缓存,可以存储在浏览器浏览历史中

#### 2、 常用的http请求方法有哪些？

   - GET 请求指定的页面信息，并返回实体主体。
   - HEAD 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头

   - POST 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
   - PUT 从客户端向服务器传送的数据取代指定的文档的内容。
   - DELETE 请求服务器删除指定的页面。
   - CONNECT HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
   - OPTIONS 允许客户端查看服务器的性能。
   - TRACE 回显服务器收到的请求，主要用于测试或诊断。

#### 3、 http与https的区别 

   - https协议需要到ca申请证书，一般免费证书很少，需要交费。

   - http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。
   - http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
   - http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全




    






