# 函数式响应编程 FRP

函数式风格的函数通过避免对状态做更改来消除副作用，在某些情况下，应用程序需要相应动态变量和状态的改变。例如我们需要在从服务器请求到新数据后刷新UI。我们如何在保持函数基于不可变和无状态的原则下同时实现对状态的维护？

响应式编程通过间接的监听和捕获状态的改变来出发响应函数进行处理。而FRP提供一种通过将函数式变成和响应式变成相结合的途径使得我们在既以写出函数式风格的代码（易于测试，无副作用）又可以处理好状态的改变。响应式编程通过特定的数据类型来出发响应，这种数据类型被叫做时间流或者事件流。

本章会结合响应式函数框架RxJava来实现一个时间管理的小程序，以此向大家介绍如何使用FRP将函数式编程运用到实际项目中。                                

## RxJava

RxJava是一套代码库通过对状态的观察产生异步的或者顺序的事件流，并传递给订阅者进行相应来完成逻辑。状态和数据的改变经由事件流流向处理函数，函数紧紧根据接收到的事件进行响应，并将结果通知到状态和数据对象。通过FRP可以将状态和双射函数进行组合

### 状态

很难定义状态，通过一个例子我们来认识一下状态。

当你启动笔记本后，开始一切都很好，但是当你使用了一段时间后几天甚至几周都不关机的时候，你发现界面的响应开始变慢，时不时的会有卡顿发生。笔记本中的硬件和刚开机时一样没有改变，而因此性能变化的原因就是状态的改变。当你重新启动机器后，一切又恢复正常。

笔记本当前的账号、保存在内存中的数据、磁盘中保存的文件、响应用户输入保存的临时变量和从云端同步下来的所有数据一起组成了你笔记本的状态。

伴随着不对对一个产品深入的开发，一次一次的迭代强化，管理好状态，尤其是在多线程异步组件之间安全的共享状态以及变更将会成为一个非常有挑战的任务。函数式风格的代码解决了外部状态改变对其的影响，另你的函数的行为更加的可预测简化了大部分代码实现的复杂度同时提升了可测试性，因为测试代码仅针对输入的参数进行测试就可以覆盖函数的所有代码。与此同时RxJava框架通过顺序事件流数据结构来传播状态的改变从而提供通用的方法来实现多线程间安全的共享状态。因此FRP在实现面向对象中的很多设计模式提供的可扩展性和代码可重用能力的同时，使得我们不用去花很多精力构造对象并维护依赖关系。

### 命令式编程 （Imperative programming）

面向对象使用命令式编程，命令式编程是一种编程范式通过执行语句不断改变程序的状态。有点像人类和自己的宠物在对话。“斑斑，转个圈。躺下。” 你使用命令语句来告诉app合适如何做某件事。


命令式代码更接近于计算机理解代码的方式。所有CPU都是顺序加载和执行简单的命令的。问题在于当人类用同样的方式编写非常复杂、异步运行的命令式代码时就会非常容易出错，尤其是在设计多线程异步共享状态的时候。

<pre><code>
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setupUI()
        connectUIControls()
        createDataSource()
        listenForChanges()
    }
</code></pre>

从代码中是没办法明确知道这几个函数调用的结果是什么，因为这些方法都根据类成员的状态来控制自身行为并修改类成员变量。在这个类中，几个函数实际上是在共享状态，同样由于这样的实现，对这几个看似不相关的函数的调用顺序进行调整也会影响onCreate最终的结果，即使我们没有改变过任何一个方法的实现。

我们曾经对项目中IM通信层进行重构，当时面临的最大的一个问题就是之前的代码对链接状态的控制被分散到了许多的lambda回调用中，任何一个你不知道的代码块中都可能会对一个类全局的状态进行变更，在网络好的情况下一切正常，由于开发的时候工程师紧紧考虑固定地点wifi的情况下（开发的时候不可能一遍拿着手机各处跑，一遍写代码）因此在网络好的时候，状态从断开链接迁移到了链接中，从连接中迁移到了链接成功，从连接成功状态迁移到开始同步数据，最终到巡航状态长链接通道使程序在线接受所有即时消息，而每个环节都是一个lambda，lambda通过改变状态来控制下一个lambda的行为，以此类推。

我们想像一下如果在网络状态频繁复杂变化的情况下，不断的超时，断网触发重新连接，同时在上面的任何一个状态下都可能发生链接中断，这个时候如果没有确保同时只有一个线程进行连接，程序的表现就不像是被设计好的逻辑代码控制，道像是靠一个概率模型来控制的。如果我们继续尝试在各个地方重置状态希望以此能修复问题，结果将必定是徒劳的，因为代码中的副作用依然存在。加之没有使用aop的方式输出必要的日志，因此，重构前负责这个模块的工程师分析了大量的日志，和进行过非常多的测试，但是每次能给出的结论只有连不上，这是结果，用户是不会接受这样的反馈的。


### 陈述性代码

陈述性代码像是这样表达一个逻辑“把这个集合中第一个满足年龄大于25岁的Person的name作为结果返回”，翻译成代码：

<pre><code>
data class Person(val name: String, val age: Int)

val people = listOf(Person("Leo", 35), Person("Lucy", 25))

// 把这个集合中第一个满足年龄大于25岁的Person的name作为结果返回
val result = people.find { it.age &gt; 25 }.map { it.name }


println(result)

// 输出 Leo
</code></pre>

而命令式风格的描述则显得有些凌乱“声明一个String类型的变量叫result，使用for 语句根据一个临时的变量i进行循环，知道i >= 集合的个数，每次循环里检测第i个对象的age属性判断是否大于25，如果大于25则将第i个对象的name赋值给result然后跳出for循环”

这个描述比陈述式风格的描述要长很多，同样，代码也会长很多。而且会增加很多缩进。

<pre><code>
// Java

//声明一个String类型的变量叫result，使用for 语句根据一个临时的变量i进行循环，知道i >= 集合的个数，每次循环里检测第i个对象的age属性判断是否大于25，如果大于25则将第i个对象的name赋值给result然后跳出for循环
String result = "";
for (int i = 0; i &lt; people.size(): i++) {
    Person person = people.get(i);
    if (person.age &gt; 25) {
        result = person.name
        break;
    }
}
</code></pre>

RxJava帮助我们在多线程环境下维护状态的同步即同一时刻仅有一个线程可以更新状态，与此同时因状态改变产生的事件响应的顺序与状态改变的顺序一致，即不发生后产生的状态改变的事件响应被提前执行而造成状态混乱。

### RxJava的响应体系

响应式系统具备一下特点：

- 迅速响应：App的UI应该永远反应应用程序最新的状态。
- 弹性：每一个事件的响应都是功能单一的相互隔离的，并且可以实现灵活的错误恢复逻辑。（这里应用弹簧的恢复特性，想象我们给弹簧压力造成其形变后，每当压力小时的时候，弹簧都会恢复原样）
- 伸缩性：响应框架实现很多基础特性例如lazy 数据拉取、事件截流、资源共享等特性，利用好这些特性我们可以确保我们的代码和所需要的资源紧紧在需要的时候才会被运行或者产生。
- 消息驱动：使用基于消息通信机制对组件进行集成可以帮助我们提高代码的重用能力、隔离性、以及类实现与生命周期控制的解偶合。
注意：弹到弹簧的特性，可以考虑一下好的弹簧床垫与次品的区别，就是好的弹簧床垫的每一个弹簧都。

### rx.Observable

Observable代表一个有序的数据流。你会一直使用这个抽象类来进行编程。因为数据流可以长时间不停的产生新的数据，因为可以将其看作事件流。通过对其调用subscribe函数来订阅数据的改变。日常有很多时间流的例子： 

- UI点击事件
- 网络层的数据传输
- 用户的商品订单
- 上传图片或者视频

Observable与Iterable非常详细，因此也支持filter、map等函数可以对Observable中传递的事件流进行类型转换、过滤和合并操作。FRP通过Observable来封装底层函数，因此Observable起到了集成和连接的作用，业务逻辑通过订阅需要的Observable来获取函数库的服务。由于Observable可以作为数据流的抽象层，同时支持对数据的转换，因此可以通过灵活的对Observable的事件流中的数据，应用不同的函数对数据进行封装处理，将我们各自独立的函数组合成新的模块来实现变化多端的用户需求。下面是一个简单的例子实现从数据库读取数据，页面会订阅数据并将结果加载到页面。

Observable中的事件分为三种类型
- onNext
- onError 
- onCompleted

<pre><code>
interface Observer<T> {
    void onNext(T t)
    void onError(Throwable t)
    void onCompleted()
}
</code></pre>

可以对Observable调用0到无限次onNext来传递事件流，当产生异常时调用onError，事件流结束时调用onComplete函数，onError和onComplete函数为终止命令。

<pre><code>

enum class TaskType {
    TASK,
    CHALLENGE,
    ACCUMULATION
}

// TaskItem
open class TaskItem(): RealmObject() {
    @PrimaryKey
    var rid = ""
    var title = ""
    var content = ""
    var type: Int = TaskType.TASK.ordinal
    var updateTime = Date()
    var createTime = Date()
    var extenData = ""

    constructor(title: String, type: TaskType, content: String) : this()
}

// 保存TaskItem到本地数据库，我们这里使用Realm对象型数据库来持久数据
fun getAllTaskFromRealm(realm: Realm): List&lt;TaskItem&gt; {
    return realm.where(TaskItem::class.java).findAll()
}

// 根据ID读取TaskItem
fun getTaskByIdFromRealm(rid: String, realm: Realm): TaskItem {
    return realm.where(TaskItem::class.java).equalTo("rid", rid).findFirst()
}

// 创建数据
fun realmCreateTaskItem(task: TaskItem, realm: Realm) {
    realm.executeTransaction {
        task.rid = UUID.randomUUID().toString()
        realm.insert(task)
    }
}
</code></pre>

上面代码先声明了TaskItem实体类，一个TaskItem是一个任务，任务类型分为积累、挑战和专注类型。同时定义了三个函数来对数据进行创建和读取。这三个函数虽然依赖Realm，但是因为我们都是传入的，因此只要测试的时候创建测试用的realm数据库，就可以单独进行测试了。下一步是使用Observable来封装这三个接口的调用和返回的数据。使用Observable的一个目的是实现依赖反转原则，即依赖抽象层编程。Observable的实现有很多种，但是对于订阅者，仅仅只针对Observable这个抽象类型进行编程。

下面代码对三个基础函数进行封装：

<pre><code>
fun getAllTask(realm: Realm): Observable&lt;List&lt;TaskItem&gt;&gt; {
    return Observable.defer {
        Observable.fromArray(getAllTaskFromRealm(realm))
    }
}

fun getTaskById(rid: String, realm: Realm): Observable&lt;TaskItem&gt; {
    return Observable.defer {
        Observable.just(getTaskByIdFromRealm(rid, realm))
    }
}

fun createTask(task: TaskItem, realm: Realm): Observable&lt;TaskItem&gt; {
    return Observable.defer {
        realmCreateTaskItem(task, realm)
        Observable.just(task)
    }
}
</code></pre>

Observable.fromArray构造函数根据一个数组作为事件源来构造一个Observable实例，并在数组数据流的结尾增加一个complete事件。

Observable.just函数与fromArray类似，just相当于onNext + onComplete的组合，即Observable仅仅只有一个信号，之后结束。

Observable.defer函数是一个构造器方法，defer的作用是延后运行，defer输入一个lambda作为唯一的参数，仅当对这个Observable调用subscribe函数进行订阅的时候，lambda内部的代码才会被运行。延时运行（按需调用）这个概念对函数式风格的代码非常有帮助。延时执行可以确保代码不会过早的一次性处理过多的任务、占用哪些资源、以及调用顺序。

注：在面向对象中引入IOC容器并利用Proxcy（动态代理）特性可以实现延时构造，就是为了解决由于对象之的依赖关系造成需要不断的调整对象的构造顺序的问题，由于容器为每个类先构造了一个Proxcy代理，这样所有对象都得到了对指定对象的引用，但其实只是得到了一个代理，这样系统的各个组件在构造阶段就不需要调整顺序了，而构造的顺序会自然的由调用的顺序来决定。函数式也需要处理类似的问题，同样也是使用lazy的方式解决。

最后一步是在需要数据的地方调用获取Observable的实例并订阅。

<pre><code>
// 创建一个任务记录
val task = TaskItem(name, type = type, content = content)
createTask(task, realm = realm)
        .doOnComplete { finish() }
        .doOnError {
            error -&gt; println(error)
            Toast.makeText(applicationContext, "任务创建失败", Toast.LENGTH_SHORT).show()
            finish()
        }.subscribe()


// 加载数据
val results = getAllTask(realm).toList().blockingGet().single()

// 根据ID加载TaskItem
getTaskById(rid, realm)
        .doOnNext { task = it }
        .doOnError {
            Toast.makeText(applicationContext, "任务数据加载失败", Toast.LENGTH_SHORT).show()
            finish()
        }
        .subscribe()
</code></pre>

doOnNext、doOnError、doOnComplete这三个函数分别处理Observable中的onNext、onError和onComplete这三种事件。处理好这三种事件，可以为程序提供良好的弹性（恢复能力）。
 
有了设定的目标，接下来就是增加日志模块来统计每天花在各个任务上的时间，并生成图标来形象的展示用户每天分配在各个任务上的时间的比例，应用的目的是帮助用户追踪自己的时间分配，这样用户可以均衡的分配时间到自己想完成的所有任务上，让自己的所有目标，每天都有进步，每天都得到积累。

<pre><code>
open class TaskLog() : RealmObject() {
    @PrimaryKey
    var rid = ""
    var refId: String = ""
    var startTime = Date()
    var cost: Long = 0

    constructor(rid: String = "",
                refId: String,
                startTime: Date,
                cost: Long) : this() {
        this.rid = rid
        this.refId = refId
        this.startTime = startTime
        this.cost = cost
    }
}

fun getWholeDayTaskLogByDate(dayDate: Date, realm: Realm): Observable&lt;List&lt;TaskLog&gt;&gt; {
    return Observable.defer {
        Observable.fromArray(findTaskLogsByDay(dayDate, realm))
    }
}

fun addTaskLog(taskLog: TaskLog, realm: Realm): Observable&lt;TaskLog&gt; {
    return Observable.defer {
        Observable.just(insertOrUpdateTaskLog(taskLog, realm))
    }
}

fun modifyTaskLogInTransaction(realm: Realm, updater: () -> Unit): Observable&lt;Unit&gt; {
    return Observable.defer {
        modifyInRealmTransaction(realm, updater)
        Observable.empty<Unit>()
    }
}


fun findTaskLogsByDay(dayDate: Date, realm: Realm): List&lt;TaskLog&gt; {
    val dateRange = getWholeDayTimes(dayDate)
    return realm
            .where(TaskLog::class.java)
            .findAll()
            .where().between("startTime", dateRange.first, dateRange.second)
            .findAll()
            .toList()
}

fun insertOrUpdateTaskLog(taskLog: TaskLog, realm: Realm): TaskLog {
    realm.executeTransaction {
        taskLog.rid = UUID.randomUUID().toString()
        realm.insertOrUpdate(taskLog)
    }
    return taskLog
}
</code></pre>

findTaskLogsByDay函数用来获取某一天的任务执行日志，当天的日志数据中记录了用户一天做了哪些任务，合适开始做的，合适结束的。程序还可以通过对这个数据集合的数据进行聚合，计算出用户在各个任务上花费的时间各是多少，最终可以统计处当天在各个任务下分配的时间的占比，统计占比的过程我们称之为归一化处理。

可以通过条形图或者饼图的形式来展现一天的进展，因此我们需要为图形控件提供所需要的数据，下一章会介绍如何用Kotlin集成D3 Chart来完成数据的可视化展现。

归一化的公式是:
    norm[i] = cost[i] / totalCost

实现的步骤是首先得出我们在每一个任务中分别花费的时间，再计算花费在所有任务的时间的综合。这样分别将每个Task下话费的时间除以总时间，就能得到我们分配在每一类任务的时间占比。App的目标是通过聚合数据告诉用户他是否在一定时期内，对某一个任务分配的时间过少了，该是时候调整一下，好确保所有的目标都会不断的取得进步而不是被放弃掉。

下面是实现的代码：

<pre><code>
fun normalizationTasks(tasklogs: List&lt;TaskLog&gt;): List&lt;Map&lt;String, Double&gt;&gt; {
    val cost = tasklogs.groupBy { it.refId }.map { it.key to it.value.sumByDouble { it.cost.toDouble() } }
    val totalCost = cost.sumByDouble { it.second }
    return cost.map { mapOf( it.first to it.second / totalCost ) }
}
</code></pre>

首先通过groupBy函数先将数据根据refId进行分组，调用后得到一个Map&lt;String, List&lt;TaskLog&gt;&gt;格式的数据，之后对新的集合调用map个函数来计算每一种task花费的时间的和，最终得到一个Map&lt;String, Double&gt;的集合。

接着计算进行所有任务的总耗时，直接对上面的分组数据使用sumByDouble来求和。

最后使用map函数将cost里面的每一个分组任务日志的时间除与总时间得到最后归一化的数据，类型为List&lt;Map&lt;String, Double&gt;&gt;.


上面三个函数是在控制层(Activity)中会被用到，通过对函数返回的Observable的订阅来得到期望的结果，Activity可以面向Observable这个抽象对象进行编程，