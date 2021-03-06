### SDWebImage 二级缓存原理
1、当需要加载一个图片的时候，首先尝试去内存缓存中去获取图片，如果能获取到图片，则直接显示图片；</br>
2、如果内存缓存中没有图片，则尝试去磁盘缓存中获取图片，如果能获取到图片，则将图片进行显示，并且将图片添加到内存中；</br>
3、如果内存中没有，磁盘当中也没有，则去网络上下载图片，将图片进行显示，并将图片做内存缓存和磁盘缓存操作；</br>
4、注意当下载图片的时候，比如说在UITableView的cell中加载图片，要创建一个队列，将图片异步下载任务添加到队列当中，还要判断是否重复下载，因为下载图片是一个耗时操作，在下载之前，先判断，是否已经存在一个下载任务，如果存在，就不必添加新任务啦，下载完图片之后，要在主线程刷新UI界面。因为图片是异步下载的，所以进行显示的时候，因为Cell的重用机制，会先直接使用缓存池的cell，但是图片不对，需要等当前的图片下载完成之后，才能进行刷新，所以这个时候，最好有一个占位图。


### RunLoop和线程之间的关系
1、每一条线程都有唯一的一个与之对应的RunLoop对象；</br>
2、主线程的RunLoop已经自动创建好了，子线程的RunLoop需要主动创建</br>
3、RunLoop在第一次获取的时候创建，在现场结束时销毁；</br>

```objc
- (void)test1 {
    // 开启一个子线程
    [self performSelectorInBackground:@selector(test2) withObject:nil];
}


- (void)test2 {
    // 在子线程里面开启了一个定时器
    NSTimer *timer = [NSTimer timerWithTimeInterval:2.0 target:self selector:@selector(run) userInfo:nil repeats:YES];
    
    // 子线程里面的定时器需要自己手动开启一个runloop
    NSRunLoop *runloop = [NSRunLoop currentRunLoop];
    [runloop addTimer:timer forMode:NSRunLoopCommonModes];
    [runloop run];
}

- (void)run {
    NSLog(@"%s", __FUNCTION__);
}
```

NSTimer中的定时器工作会收到runloop的运行模式的影响，而GCD中的定时器是精准的，不受影响；

### GCD 定时器
```objc
- (void)GCD_Timer {
    // 参数1: 指定source类型为定时器类型
    // 参数4: 指定定时执行的任务在哪个队列中进行
    dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, dispatch_get_global_queue(0, 0));
    // 参数3: 间隔执行时间
    // 参数4: 精准度
    dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, 3.0 * NSEC_PER_SEC, 0 * NSEC_PER_SEC);
    dispatch_source_set_event_handler(timer, ^{
        NSLog(@"这儿就是定时执行的任务");
    });
    // 启动定时器
    dispatch_resume(timer);
    
    
    // 如果直接调用这个方法，会不执行定时器任务，因为定时器对象是局部变量，方法执行完毕后都释放了，所以需要一个属性强引用
    self.timer = timer;
}
```

### runloop知识点
```objc
1、概念：运行循环（死循环）
2、作用：
	2.1、保持程序的运行；
	2.2、处理app的各种事件；
	2.3、节省CPU的资源，提升性能；
3、runloop和线程之间的关系：
	3.1、runloop和线程是一一对应的关系,是使用字典就行存储的；
	3.3、当线程销毁的时候，那么对应的runloop也要销毁；
	3.2、主线程对应的runloop已经创建并且开启了，子线程对应的runloop需要手动创建并且开启；
4、runloop对象
	4.1、C语言
	4.2、OC语言
5、相关类
	5.1、runloop｜mode | source | timer | observer
	5.2、runloop启动的时候，需要选择一个运行模式，检查运行模式是否为空（检查是否存在“source”｜"timer"）,如果为空，那么就直接退出，不为空则开启运行循环。
	
source 事件有急于端口的port事件，自定义事件custom，还有selector事件
```

### CFRunLoopObserverRef
```objc
CFRunLoopObserverRef是观察者，能够监听RunLoop的状态的改变
可以监听的时间点有以下几个:
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    kCFRunLoopEntry = (1UL << 0),			 // 即将进入runloop
    kCFRunLoopBeforeTimers = (1UL << 1),		//即将处理timer
    kCFRunLoopBeforeSources = (1UL << 2),	// 即将处理source
    kCFRunLoopBeforeWaiting = (1UL << 5),	// 即将进入休眠
    kCFRunLoopAfterWaiting = (1UL << 6),		// 刚从休眠中唤醒
    kCFRunLoopExit = (1UL << 7),				// 即将推出runloop
    kCFRunLoopAllActivities = 0x0FFFFFFFU
};


- (void)runloopObserver {
    /*
    typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
        kCFRunLoopEntry = (1UL << 0),             // 即将进入runloop
        kCFRunLoopBeforeTimers = (1UL << 1),        //即将处理timer
        kCFRunLoopBeforeSources = (1UL << 2),    // 即将处理source
        kCFRunLoopBeforeWaiting = (1UL << 5),    // 即将进入休眠
        kCFRunLoopAfterWaiting = (1UL << 6),        // 刚从休眠中唤醒
        kCFRunLoopExit = (1UL << 7),                // 即将推出runloop
        kCFRunLoopAllActivities = 0x0FFFFFFFU
    };
     */

    // 参数1: 分配内存，不知道如何分配，使用默认的分配方式
    // 参数2: 要监听的状态
    // 创建一个observer
    CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler(CFAllocatorGetDefault(), kCFRunLoopAllActivities, YES, 0, ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {
        // 根据状态作出对应的处理
        switch (activity) {
            case kCFRunLoopEntry:
                NSLog(@"即将进入runloop");
                break;
            case kCFRunLoopBeforeTimers:
                NSLog(@"即将处理timer");
                break;
            case kCFRunLoopBeforeSources:
                NSLog(@"即将处理source");
                break;
            case kCFRunLoopBeforeWaiting:
                NSLog(@"即将进入休眠");
                break;
            case kCFRunLoopAfterWaiting:
                NSLog(@"刚从休眠中唤醒");
                break;
            case kCFRunLoopExit:
                NSLog(@"即将推出runloop");
                break;
                
            default:
                break;
        }
    });
    
    // 参数1:要监听哪一个runloop对象
    // 参数2：哪个监听者去监听
    // 在那种模式下进行监听
    CFRunLoopAddObserver(CFRunLoopGetMain(), observer, kCFRunLoopDefaultMode);
    
    [NSTimer scheduledTimerWithTimeInterval:4.0 target:self selector:@selector(run) userInfo:nil repeats:YES];
}

```

### RunLoop的应用, 开启一条常驻线程

```objc
#pragma mark - 开启一条常驻线程
// 使用runloop开启一条常驻线程
// 说明：当点击一个按钮的时候，创建一个线程，让线程执行一个方法run1, 当点击另外一个按钮的时候，让之前开启的线程继续执行任务
// 线程都是在执行完任务之后就进入死亡状态，不能在重新开启, 不论线程是否是强引用，只要没有source事件或者timer事件，执行完任务就挂掉了
- (IBAction)createThread:(id)sender {
    NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run1) object:nil];
    self.thread = thread;
    [thread start];
}

// 让之前创建的线程继续执行run2任务
- (IBAction)continueWork:(id)sender {
    // 解决方式1：失败，因为线程已死亡
    // [self.thread start]; //重新启动一个死亡的线程，不行
    
     [self performSelector:@selector(run2) onThread:self.thread withObject:nil waitUntilDone:YES];
}


- (void)run1 {
    NSLog(@"run1----------%@", [NSThread currentThread]);
    
    // 解决方式2: 失败，因为while死循环执行不完，此线程永远不能执行接下来的其他任务；
    // 开启一个死循环让线程或者，也不可行，因为当前线程上的任务执行不完，以后不能再继续让这个线程做其他的事，只能做当前的死循环
//    while (1) {
//
//    }
    
    // 解决方式3: 成功：添加一个timer事件，因为有timer事件，所以线程会一直存活下来，但是这个方式不好，多了让定时器工作，虽然可行，但不推荐
    // [NSTimer timerWithTimeInterval:2.0 target:self selector:@selector(run3) userInfo:nil repeats:YES];
    
    // 解决方式4: 成功：添加一个source，port端口事件，因为有source事件，所以线程也会一直存活下来
    NSRunLoop *runloop = [NSRunLoop currentRunLoop];
    [runloop addPort:[NSPort port] forMode:NSDefaultRunLoopMode];
    // 自己创建的runloop，需要自己开启
    [runloop run];
    
    NSLog(@"因为runloop一直存在，这里的代码将一直不执行");
}

// 让常驻线程继续干活
- (void)run2 {
    NSLog(@"run2-----------%@", [NSThread currentThread]);
}

// 定时器事件
- (void)run3 {
    NSLog(@"run3-----------这个是定时器干的活，不是runloop执行的线程干的活");
}

```


