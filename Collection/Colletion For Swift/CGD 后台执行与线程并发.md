### 同步与异步

同步函数只有在任务完成后才会返回。

异步函数会立即返回，不会等待任务完成。因此异步函数不会阻塞当前线程。

### **临界区（Critical Section）**

这是一段不能并发执行的代码，也就是说两个线程不可以同时执行它。这通常是因为这段代码会修改共享的资源。否则，并发的进程同时修改同一个变量会导致错误。

### **线程安全**

线程安全的代码是可以被多个线程或并发任务安全调用的，他不会造成任何问题（数据错误，崩溃等）。非线程安全的代码在同一时间只能单独执行。一段线程安全的代码如let a = ["thread-safe"]。由于数组是只读的，它可以被多个线程同时使用而不会引发问题。另一方面，var a = ["thread-unsafe"]是可变数组。这意味着它不是线程安全的，因为多个线程可以同时获取并修改这个数组，会得到不可预料的结果。非线程安全的变量和可变的数据结构在同一时刻应该只能被一个线程获取。

### **上下文切换 **

上下文切换是在进程中切换不同线程时保存和恢复程序执行状态的过程。这一过程在编写多任务app时相当常见，但是会造成一些额外开支。

### **并发 vs 并行**

并发和并行经常会被同时提起，所以值得通过简短的解释来区分彼此。

并发代码中的单独部分可以同时执行。然而，这要由系统来决定并发怎样发生或是否发生。

多核设备通过并行来同时执行多个线程；然而，在单核设备中，必须要通过上下文切换来运行另一个线程或进程。

### **队列**

GCD提供了调度队列（dispatch queues）来处理提交的任务；这些队列管理着你向GCD提交的任务并且以先进先出（FIFO）的顺序来执行任务。这保证了第一个加入队列的任务第一个被执行，第二个加入的任务第二个开始执行，以此类推。关键是选择正确的调度队列种类和正确的调度函数（dispatching function）来提交你的任务。

**1.顺序队列：****Serial Queue**

顺序队列中的任务同一时间只执行一件任务，每件任务只有在先前的任务完成后才开始。同时，你并不知道一个任务完成到另一个任务开始之间的间隔时间, 任务的执行是在GCD掌控之下的；你唯一确定的就是GCD在同一时刻只执行一件任务并且按任务加入队列的顺序执行。因为不会在顺序队列中同时执行两件任务，所以没有多个任务同时进入临界区的危险；这保证了临界区不会出现竞态条件。因此如果进入临界区的唯一途径就是通过向调度队列提交任务，那么可以保证临界区是安全的。

**2.并发队列：Concurrent Queue **

并发队列中的任务可以保证按进入队列的顺序被执行…仅此而已！任务可能以任意顺序完成而且你不知道何时下一个任务会开始，或是任一时刻有多少任务在运行。再一次，这完全取决于GCD。何时开始一个任务完全取决于GCD。如果一个任务的执行时间和另一个的发生重叠，将由GCD来决定是否要将任务运行在另一个可用的核上或是通过上下文切换来运行另一个程序。有趣的是，GCD为每种队列类型提供了至少5种特别的队列。

### **队列类型**

首先，系统提供了一种特殊的顺序队列main queue。和其他的顺序队列一样，在这个队列里的任务同一时刻只有一个在执行。然而，这个队列保证了所有任务会在主线程中执行，主线程是唯一一个允许更新UI的线程。这个队列用来向UIView对象发消息或发通知。

系统同时提供了几种并发队列。这些队列和它们自身的QoS等级相关。QoS等级表示了提交任务的意图，使得GCD可以决定如何制定优先级。

1.QOS\_CLASS\_USER\_INTERACTIVE： user interactive等级表示任务需要被立即执行以提供好的用户体验。使用它来更新UI，响应事件以及需要低延时的小工作量任务。这个等级的工作总量应该保持较小规模。

2.QOS\_CLASS\_USER\_INITIATED：user initiated等级表示任务由UI发起并且可以异步执行。它应该用在用户需要即时的结果同时又要求可以继续交互的任务。

3.QOS\_CLASS\_UTILITY：utility等级表示需要长时间运行的任务，常常伴随有用户可见的进度指示器。使用它来做计算，I/O，网络，持续的数据填充等任务。这个等级被设计成节能的。

4.QOS\_CLASS\_BACKGROUND：background等级表示那些用户不会察觉的任务。使用它来执行预加载，维护或是其它不需用户交互和对时间不敏感的任务。

要清楚Apple的API同时也使用了全局调度队列（global dispatch queue），所以你添加的任何任务都不是这些队列中的唯一任务。

最后，你可以创建自定义的顺序或并发队列。意味着你至少有5种队列：主队列（main queue），四种通用调度队列，加上任意你自己定制的队列！

### **何时使用何种队列类型快速指南：**

 – 自定义顺序队列：当你想顺序执行后台任务并追踪它时，这是一个很好的选择。因为同时只有一个任务在执行，因此消除了资源竞争。注意如果需要从方法中获取数据，你必须内置另一个闭包来得到它或者考虑使用dispatch\_sync。

 – 主队列（顺序main queue）：当并发队列中的任务完成需要更新UI的时候，这是一个通常的选择。为达此目的，需要在一个闭包中嵌入另一个闭包。同时，如果在主队列中调用dispatch\_async来返回主队列，能保证新的任务会在当前方法完成后再执行。 

– 并发队列：通常用来执行与UI无关的后台任务。

```swift
var GlobalMainQueue: dispatch_queue_t {
  return dispatch_get_main_queue()
}
  
var GlobalUserInteractiveQueue: dispatch_queue_t {
  return dispatch_get_global_queue(Int(QOS_CLASS_USER_INTERACTIVE.value), 0)
}
  
var GlobalUserInitiatedQueue: dispatch_queue_t {
  return dispatch_get_global_queue(Int(QOS_CLASS_USER_INITIATED.value), 0)
}
  
var GlobalUtilityQueue: dispatch_queue_t {
  return dispatch_get_global_queue(Int(QOS_CLASS_UTILITY.value), 0)
}
  
var GlobalBackgroundQueue: dispatch_queue_t {
  return dispatch_get_global_queue(Int(QOS_CLASS_BACKGROUND.value), 0)
}

```

dispatch\_get\_global\_queue是把耗时的放入一个队列中，若是有关于UI更新的操作，就要放到dispatch\_get\_main\_queue()中做

### **
**

### **dispatch\_after推迟任务**

dispatch\_after的工作原理就像推迟的dispatch\_async。一旦dispatch\_after返回，你还是无法掌握实际的执行时间抑或是取消任务。

想知道何时使用dispatch\_after？

自定义顺序队列：慎用。在自定义顺序队列中慎用dispatch\_after。你最好留在主队列中。

主队列（顺序）：好主意。在主队列中使用dispatch\_after是一个好主意；Xcode对此有自动补全模板。

并发队列：慎用。很少会这样使用，最好留在主队列中。

### **单例和线程安全**

初始化代码被视为临界区从而保证了初始化在其他线程使用全局变量之前就完成了。Swift是怎么做到的？其实，Swift在幕后使用

了GCD中的dispatch\_once 。dispatch\_once以线程安全的方式执行且仅执行一次闭包。如果一个线程正处于临界区中 — 被提交

给dispatch\_once的任务 — 其他线程会阻塞直到它完成。并且一旦它完成，其他线程不会再执行临界区中的代码。用let将单例定

义为全局常量，我们可以进一步保证变量在初始化后不会发生变化。从某种意义上说，所有Swift全局常亮量都天生是单例，并且

线程安全地初始化。

### **屏障函数：**

注意队列开始就像普通的并发队列一样工作。但当屏障执行的时候，队列变成像顺序队列一样。就是说，屏障是唯一一个在执行的任务。在屏障完成后，队列恢复成普通的并发队列。

自定义顺序队列：坏选择。因为顺序队列本身就是顺序执行，屏障不会起到任何帮助作用。

全局并发队列：慎用。其他系统可能也在使用队列，你不应该出于自身目的而独占队列。

自定义并发队列：最佳选择。用于原子操作或是临界区代码。任何需要线程安全的设置和初始化都可以使用屏障。

```swift
private let concurrentPhotoQueue = dispatch_queue_create(
    "com.raywenderlich.GooglyPuff.photoQueue", DISPATCH_QUEUE_CONCURRENT)

func addPhoto(photo: Photo) {
  dispatch_barrier_async(concurrentPhotoQueue) { // 1
    self._photos.append(photo) // 2
    dispatch_async(GlobalMainQueue) { // 3
      self.postContentAddedNotification()
    }
  }
}
```

1\. 将写操作加入自定义的队列中。当临界区被执行时，这是队列中唯一一个在执行的任务。

2\. 将对象加入数组。因为是屏障闭包，这个闭包不会和concurrentPhotoQueue中的其他任务同时执行。

 3\. 最终发送一个添加了图片的通知。这个通知应该在主线程中发送因为这涉及到UI，所以这里分派另一个异步任务到主队列中。

### **dispatch\_sync**

dispatch\_sync同步提交任务并等到任务完成后才返回。使用dispatch\_sync和调度屏障一起来跟踪任务；或是在需要等待返回数据时使用dispatch\_sync。

仍需小心。设想你调用dispatch\_sync到当前队列中。这会造成死锁。因为调用在等待闭包完成，但是闭包无法完成（甚至根本没开始！），直到当前在执行的任务结束，但当前任务没法结束（因为阻塞的闭包还没完成）！这就要求你必须清醒的认识到你从哪个队列调用了闭包，以及你将任务提交到哪个队列。

概述一下何时何地使用dispatch\_sync： 

– 自定义顺序队列：非常小心；如果你在运行一个队列时调用dispatch\_sync调度任务到同一个队列，你显然会制造死锁。

 – 主队列（顺序）：非常小心，原理同上。 

– 并发队列：好选择。用在和调度屏障同步或是等待任务完成以继续后续处理。

**dispatch_sync**

![i icon](http://cc.cocimg.com/api/uploads/20150129/1422497476526948.gif)

```swift
override func viewDidLoad() {
  super.viewDidLoad()
  
  dispatch_sync(dispatch_get_global_queue(
      Int(QOS_CLASS_USER_INTERACTIVE.value), 0)) {
  
    NSLog("First Log")
  
  }
  
  NSLog("Second Log")
}
```

1\. 主队列按部就班的执行任务 —— 紧接着的任务是实例化包含viewDidLoad的UIViewController类。

2\. viewDidLoad在主线程中执行。

3\. dispatch\_sync闭包被加入到全局队列中稍后执行。主线程停下来等待闭包完成。同时，全局队列正在并发执行任务；记住闭包以FIFO的顺序从全局队列中取出，但是会并发地执行。全局队列首先处理dispatch\_sync闭包加入前已经存在队列中的任务。

4\. 最后，轮到dispatch\_sync闭包执行。 

5\. 闭包执行完毕，主线程得以继续。 

6\. viewDidLoad方法完成，主队列接着处理其它任务。

dispatch\_sync把任务加入队列并一直等待其完成。dispatch\_async做了差不多的工作，只是它不会等待任务完成，而是转而去继续其他工作。

**dispatch_async
**

![i icon](http://cc.cocimg.com/api/uploads/20150129/1422497555309360.gif)

```swift
override func viewDidLoad() {
  super.viewDidLoad()
  
  dispatch_async(dispatch_get_global_queue(
      Int(QOS_CLASS_USER_INTERACTIVE.value), 0)) {
  
    NSLog("First Log")
  
  }
  
  NSLog("Second Log")
}
```

1.主队列按部就班的执行任务 —— 紧接着的任务是实例化包含viewDidLoad的UIViewController类。

2.viewDidLoad在主线程中执行。 

3.dispatch\_async闭包被加入到全局队列中稍后执行。

4.viewDidLoad在dispatch\_async后继续向下执行，主线程继续其他任务。同时，全局队列正在并发执行任务；记住闭包以FIFO的顺序从全局队列中取出，但是会并发地执行。

5.执行dispatch\_async所添加的闭包。

6.dispatch\_async闭包完成，NSLog输出到控制台。

在这个特别的例子中，第一个NSLog在第二个NSLog后执行。事实并非总是如此——这取决于硬件在彼时正在做什么，你无法控制或知晓哪个语句会先执行。“第一个”NSLog在某种调用情况下可能会先执行。

### **调度组（Dispatch Groups）**

调度组在一组任务都完成后会发出通知。这些任务可以是异步或同步的，甚至可以分布在不同的队列。调度组还可以通过同步或异步的方式来通知。因为任务在不同的队列中，disptch\_group\_t实例用来追踪队列中的不同任务。

在组内所有事件都完成时，GCD API提供了两种方式发送通知。

第一种是dispatch\_group\_wait，它会阻塞当前进程，直到所有任务都完成或是等待超时。这正是我们的例子中需要的方式。

```swift
func downloadPhotosWithCompletion(completion: BatchPhotoDownloadingCompletionClosure?) {
  dispatch_async(GlobalUserInitiatedQueue) { // 1
    var storedError: NSError!
    var downloadGroup = dispatch_group_create() // 2
  
    for address in [OverlyAttachedGirlfriendURLString,
                    SuccessKidURLString,
                    LotsOfFacesURLString]
    {
      let url = NSURL(string: address)
      dispatch_group_enter(downloadGroup) // 3
      let photo = DownloadPhoto(url: url!) {
        image, error in
        if let error = error {
          storedError = error
        }
        dispatch_group_leave(downloadGroup) // 4
      }
      PhotoManager.sharedManager.addPhoto(photo)
    }
  
    dispatch_group_wait(downloadGroup, DISPATCH_TIME_FOREVER) // 5
    dispatch_async(GlobalMainQueue) { // 6
      if let completion = completion { // 7
        completion(error: storedError)
      }
    }
  }
}

```

1、因为使用dispatch\_group\_wait阻塞了当前进程，要用dispatch\_async将整个方法放到后台队列，才能保证主线程不被阻塞。

2、创建一个调度组，作用好比未完成任务的计数器。

3、dispatch\_group\_enter通知调度组一个任务已经开始。你必须保证dispatch\_group\_enter和dispatch\_group\_leave是成对调用的，否则程序会崩溃。

4、通知任务已经完成。再一次，这里保持进和出相匹配。

5、dispatch\_group\_wait等待所有任务都完成直到超时。如果在任务完成前就超时了，函数会返回一个非零值。可以通过返回值来判断是否等待超时；不过，这里你用DISPATCH\_TIME\_FOREVER来表示一直等待。这意味着，它会永远等待！没关系，因为图片总是会下载完的。

6、此时，你可以保证所有图片任务都完成或是超时了。接下来在主队列中加入完成闭包。闭包晚些时候会在主线程中执行。

7、执行闭包。

自定义顺序队列：好选择。当一组任务完成时用它发送通知。

主队列（顺序）：在当前情景下是不错的选择。但你要谨慎地在主队列中使用，因为同步等待所有任务会阻塞主线程。然而，当一个需要较长时间的任务（比如网络请求）完成时，异步更新UI是很好的选择。

并发队列：好选择。用于调度组和通知。

第二种，dispatch\_group\_notify

```swift
func downloadPhotosWithCompletion(completion: BatchPhotoDownloadingCompletionClosure?) {
  // 1
  var storedError: NSError!
  var downloadGroup = dispatch_group_create()
  
  for address in [OverlyAttachedGirlfriendURLString,
                  SuccessKidURLString,
                  LotsOfFacesURLString]
  {
    let url = NSURL(string: address)
    dispatch_group_enter(downloadGroup)
    let photo = DownloadPhoto(url: url!) {
      image, error in
      if let error = error {
        storedError = error
      }
      dispatch_group_leave(downloadGroup)
    }
    PhotoManager.sharedManager.addPhoto(photo)
  }
  
  dispatch_group_notify(downloadGroup, GlobalMainQueue) { // 2
    if let completion = completion {
      completion(error: storedError)
    }
  }
}

```

新的实现不需要把方法放进dispatch\_async中，因为你并没有阻塞主线程。

dispatch\_group\_notify异步执行闭包。当调度组内没有剩余任务的时候闭包才执行。同样要指明在哪个队列中执行闭包。当下，你需要在主队列中执行闭包。

这是更优雅的方法，并且不会阻塞任何进程。

### **通过并发执行循环来提速：**dispatch\_apply

dispatch\_apply像for循环一样，只不过它会并发地执行循环过程。这个函数是同步的，所以像普通的for循环一样，dispatch\_apply在所有工作都完成后才返回。

要注意循环的最佳次数，如果有太多循环但每个循环内只有很小的工作量，那么额外的开销会抹杀掉并发带来的好处。 步进 (striding)可以帮助到你。它让你在每次循环中做多件工作。

什么时候用dispatch\_apply合适？

自定义顺序队列：在顺序队列中使用dispatch\_apply完全无意义；它的效果和for循环一样。

主队列（顺序）：理由同上，用for循环就可以了。

并发队列：明智之选，尤其是你需要追踪任务进度时。

```swift
func downloadPhotosWithCompletion(completion: BatchPhotoDownloadingCompletionClosure?) {
  var storedError: NSError!
  var downloadGroup = dispatch_group_create()
  let addresses = [OverlyAttachedGirlfriendURLString,
                   SuccessKidURLString,
                   LotsOfFacesURLString]
  
  dispatch_apply(UInt(addresses.count), GlobalUserInitiatedQueue) {
    i in
    let index = Int(i)
    let address = addresses[index]
    let url = NSURL(string: address)
    dispatch_group_enter(downloadGroup)
    let photo = DownloadPhoto(url: url!) {
      image, error in
      if let error = error {
        storedError = error
      }
      dispatch_group_leave(downloadGroup)
    }
    PhotoManager.sharedManager.addPhoto(photo)
  }
  
  dispatch_group_notify(downloadGroup, GlobalMainQueue) {
    if let completion = completion {
      completion(error: storedError)
    }
  }
}

```

现在你的循环可以并发执行了；调用 dispatch\_apply 时，第一个参数是循环的次数，第二个参数是执行任务的队列，第三个参数是闭包。

尽管你的代码在添加图片时是线程安全的，但是图片的顺序取决于线程完成的顺序。

运行app，用 Le Internet 添加一些图片，发现不同了吗？

在真机上运行新的代码会发现 些许 的速度提升。但是这值得吗？

 实际上，在这里并不值得这么做。原因如下：

你很可能因为并行而花费了比for循环更多的开销。你应该结合合适的步长对 非常大 的集合使用dispatch\_apply。

开发app的时间有限——不要花时间过早优化。如果你想优化，那么就优化那些值得优化的东西。用Instruments测试app以找到最耗时间的方法。如何使用Instruments。

一般说来，代码优化会让你的代码变得更复杂。你要确定带来的好处值得你增加复杂性。

记住，不要痴迷于优化。否则只会让你自己为难，也让看你代码的人抓狂。

### **取消调度块**

iOS 8 和 OS X Yosemite引入了 调度对象块 （dispatch block object）。它们实现起来就像对闭包再包装一层。调度对象块可以做到很多事情，比如为队列中的对象设置QoS等级来决定优先级，但最显著的能力是可以取消块的执行。要明白对象块只有在轮到它执行之前才可以取消（一旦开始执行就不能取消了）。

```swift
func downloadPhotosWithCompletion(completion: BatchPhotoDownloadingCompletionClosure?) {
  var storedError: NSError!
  let downloadGroup = dispatch_group_create()
  var addresses = [OverlyAttachedGirlfriendURLString,
                   SuccessKidURLString,
                   LotsOfFacesURLString]
  addresses += addresses + addresses // 1
  var blocks: [dispatch_block_t] = [] // 2
  
  for i in 0 ..< addresses.count {
    dispatch_group_enter(downloadGroup)
    let block = dispatch_block_create(DISPATCH_BLOCK_INHERIT_QOS_CLASS) { // 3
      let index = Int(i)
      let address = addresses[index]
      let url = NSURL(string: address)
      let photo = DownloadPhoto(url: url!) {
        image, error in
        if let error = error {
          storedError = error
        }
        dispatch_group_leave(downloadGroup)
      }
      PhotoManager.sharedManager.addPhoto(photo)
    }
    blocks.append(block)
    dispatch_async(GlobalMainQueue, block) // 4
  }
  
  for block in blocks[3 ..< blocks.count] { // 5
    let cancel = arc4random_uniform(2) // 6
    if cancel == 1 {
      dispatch_block_cancel(block) // 7
      dispatch_group_leave(downloadGroup) // 8
    }
  }
  
  dispatch_group_notify(downloadGroup, GlobalMainQueue) {
    if let completion = completion {
      completion(error: storedError)
    }
  }
}

```

1、扩展addresses数组，将每个地址复制3份。

2、这个数组用来保存接下来创建的对象块。

3、dispatch\_block\_create创建一个对象块。第一个参数是一个表明了块特征的标志。此处的标志让块从它进入的队列那里继承QoS等级。第二个参数是闭包形式的块定义。

4、块被异步的调度到全局主队列。这里用全局主队列是因为它是一个顺序队列，可以方便我们取消对象块。当前代码已经在主线程中执行着，所以你可以保证下载任务将在此之后才执行（也就是这个downloadPhotosWithCompletion返回后才轮到下载任务执行）。

5、取数组中第三个到结尾的部分。

6、arc4random\_uniform会随机返回一个0到上界之间（不含上界）的整数。以2为上界会得到0或1，像投硬币一样。

7、如果随机数是1，则取消块。前提是，块还在队列中并且没开始。块在执行的过程中是不可以取消的。

8、因为所有块都加入调度组了，不要忘记移除被取消的那些块。

运行，从 Le Internet 添加图片。你会看到app下载3张图片，以及随机数量的额外图片。那些没下载的图片是因为在加入队列 后 9、被取消了。这是一个刻意设计的例子，但是很好的演示了怎样使用调度对象块以及如何取消它。

### **测试异步代码**

一旦给定的测试方法返回了，XCTest 会认为这个测试完成了而去做下一个测试。这就是说，在下一个测试执行过程中，前一个测试中的异步代码也在继续执行。

网路请求通常是异步的，因为你不想阻塞主线程。一旦测试方法返回，测试也就结束了，因此很难对网络请求做测试。

我们简单看一下两种普遍的测试异步代码的方法：**信号量（****semaphores）和 期望（expectations）。**

**信号量**

信号量让你控制多个消费者对有限资源的获取。例如，如果你创建一个信号量来控制拥有2个资源的资源池，那么同一时刻最多有两个线程可以进入临界区。其它也想使用资源的线程必须在FIFO队列中等待。

```swift
func downloadImageURLWithString(urlString: String) {
  let url = NSURL(string: urlString)
  let semaphore = dispatch_semaphore_create(0) // 1
  let photo = DownloadPhoto(url: url!) {
    image, error in
    if let error = error {
      XCTFail("\(urlString) failed. \(error.localizedDescription)")
    }
    dispatch_semaphore_signal(semaphore) // 2
  }
  
  let timeout = dispatch_time(DISPATCH_TIME_NOW, DefaultTimeoutLengthInNanoSeconds)
  if dispatch_semaphore_wait(semaphore, timeout) != 0 { // 3
    XCTFail("\(urlString) timed out")
  }
}
```

Product/Test 或 cmd+U 运行测试。测试应该成功。

**期望（expectations）**

这种特性让你首先设置你的期望——你希望发生的事——然后再开始异步任务。接下来测试会一直等待，直到异步任务将期望标记为 已完成 。

```swift
func downloadImageURLWithString(urlString: String) {
  let url = NSURL(string: urlString)
  let downloadExpectation = expectationWithDescription("Image downloaded from \(urlString)") // 1
  let photo = DownloadPhoto(url: url!) {
    image, error in
    if let error = error {
      XCTFail("\(urlString) failed. \(error.localizedDescription)")
    }
    downloadExpectation.fulfill() // 2
  }
  
  waitForExpectationsWithTimeout(10) { // 3
    error in
    if let error = error {
      XCTFail(error.localizedDescription)
    }
  }
}
```

工作原理： 

1\. 用expectationWithDescription生成期望。测试会在日志上显示其中的字符串参数，所以请描述你期望发生的事。 

2\. 在异步执行的闭包中调用fulfill来标记期望已达成。 

3\. 调用线程用waitForExpectationsWithTimeout等待期望达成。如果等待超时会视为出错。

运行测试。结果和使用信号量没什么不同，但使用XCTest框架是更清晰易读的方案。

### **调度源（Dispatch Sources）**

GCD中存在一个特别有趣的特性叫调度源，它是一个包含底层功能的百宝囊，帮助你响应或监控Unix信号，文件描述符（file descriptors），Mach端口，VFS Nodes，以及其他复杂的东西。所有这些都超出了本教程的范围，但是你可以尝试着使用一下调度源对象。

第一次使用调度源的用户可能会迷失其中，所以你首先要理解dispatch\_source\_create的工作原理。下面是创建它的函数原型：

```swift
func dispatch_source_create(
  type: dispatch_source_type_t,
  handle: UInt,
  mask: UInt,
  queue: dispatch_queue_t!) -> dispatch_source_t!
```

第一个参数type: dispatch\_source\_type\_t是最重要的参数，因为它描述了句柄（handle）和掩码（mask）参数。你需要查看Xcode文档来弄清楚dispatch\_source\_type\_t的参数有哪些可选项。

<http://www.cocoachina.com/swift/20150129/11057.html>

<http://www.cocoachina.com/swift/20150130/11054.html>

<http://www.henishuo.com/ios-multithread-detail/>

