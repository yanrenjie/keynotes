### 网络请求
注意在在POST请求的时候，URL地址中不能出现中文，应该对URL进行转码处理。

#### NSURLSession
使用步骤：使用NSURLSession对象创建Task，然后执行task</br>
Task的类型</br>

```objc
//NSURLSessionTask是父类，不会直接使用，应该使用其子类
NSURLSessionDataTask, NSURLSessionDownloadTask, NSURLSessionUploadTask, 
NSURLSessionUploadTask是NSURLSessionDataTask的子类，他们都是NSURLSessionTask的子类
```
当下载小文件的时候，可以使用block形式的NSURLSession，如下：</br>

```objc
	NSURL *url = [NSURL URLWithString:@"https://www.jackey.com/images"];
    // 创建一个可变的请求对象
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    request.HTTPMethod = @"POST";
    request.HTTPBody = [@"type="application/jpeg"&keyword=刘亦菲 dataUsingEncoding:NSUTF8StringEncoding];
    // 创建一个NSURLSession
    
    NSURLSession *session = [NSURLSession sharedSession];
    NSURLSessionDataTask *task = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        NSLog(@"data =======   %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
        NSLog(@"response ===   %@", response);
    }];
    [task resume];
```

如果要下载的数据是大数据，比如是音视频，好几个G的数据，就不能使用这种方式，应该使用代理的方法来实现：</br>

```objc
- (NSMutableData *)imageData {
    if (!_imageData) {
        _imageData = [NSMutableData data];
    }
    return _imageData;
}

- (void)downloadBigData {
    NSURL *url = [NSURL URLWithString:@"https://img.gzhuibei.com/images/img/150/4.jpg"];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request];
    [dataTask resume];
}

// 要实现三个代理方法，并监听下载进度
// 接收到响应的时候调用
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler {
    // 获取总的下载数据的大小
    self.totalSize = response.expectedContentLength;
    // 要允许请求
    completionHandler(NSURLSessionResponseAllow);
}

// 接受到数据就调用，这个方法会调用很多次
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data {
    [self.imageData appendData:data];
    self.currentSize += data.length;
    self.progressView.progress = 1.0 * self.currentSize / self.totalSize;
}

// 下载完成或者下载出错的时候调用
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error {
    if (error == nil) {
        UIImage *image = [UIImage imageWithData:self.imageData];
        self.imageView.image = image;
    }
}

```

但是如果要下载一个视频，并且进行保存，如果直接等到下载完成之后，在- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error ;方法中去写文件，那在写文件之前，数据一直会在内存当中，这样就出现一个内存暴涨的问题，严重影响app的用户体验，因为一个电影可能好几个G，手机内存就那么大，这里我们不能直接使用传统的直接将数据writeToFile的方法，我们采用新的方式来写入，NSFileHandle，文件句柄的方式来写入；

```objc
#pragma mark - 文件句柄的使用方法
/**
 NSFileHandle：文件句柄（指针）
 特点：在写数据的时候，边写数据边移动位置
 使用步骤：
 1、创建空文件
 2、创建文件句柄指针指向该文件
 3、当接受到数据的时候，使用该句柄来写数据
 4、当所有的数据写入完毕，应该关闭句柄指针
 */
```

### 下载写一个视频文件到缓存Cache下面
```objc
- (void)downloadBigData {
    NSURL *url = [NSURL URLWithString:@"http://vfx.mtime.cn/Video/2019/03/19/mp4/190319212559089721.mp4"];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request];
    [dataTask resume];
}

// 要实现三个代理方法，并监听下载进度
// 接收到响应的时候调用
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler {
    // 获取总的下载数据的大小
    self.totalSize = response.expectedContentLength;
    
    // [response suggestedFilename]， 获取URL最后一个节点的名称，作为文件名
    // 创建文件路径
    NSString *path = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).lastObject;
    NSString *filePath = [path stringByAppendingPathComponent:@"love.mp4"];
    NSLog(@"%@", filePath);
    // 根据路径创建文件
    // 此处要判断文件是否已经创建，如果已经存在，还重新创建，会覆盖以前的写入的数据，导致文件不完整
   if (![[NSFileManager defaultManager] fileExistsAtPath:filePath]) {
        [[NSFileManager defaultManager] createFileAtPath:filePath contents:nil attributes:nil];
    }
    // 创建一个文件b句柄
    NSFileHandle *handle = [NSFileHandle fileHandleForWritingAtPath:filePath];
    self.handle = handle;
    // 要允许请求
    completionHandler(NSURLSessionResponseAllow);
}

// 接受到数据就调用，这个方法会调用很多次
- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data {
    self.currentSize += data.length;
    CGFloat value = 1.0 * self.currentSize / self.totalSize;
    self.progressView.progressValue = value;
    [self.handle writeData:data];
    self.progressLabel.text = [NSString stringWithFormat:@"已下载%.2f%%", value * 100];
}

// 下载完成或者下载出错的时候调用
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error {
    [self.handle closeFile];
    NSLog(@"下载完毕，请尽情观看");
    [self playAction];
}
```

##### 如果要实现断点续传，则要靠NSFileHandle里面的- (unsigned long long)seekToEndOfFile；或者- (void)seekToFileOffset:(unsigned long long)offset；将句柄指针移动到指定的位置，然后从指定的位置开始写数据，要注意，判断文件是否会重复创建


### 如何解决session设置代理之后对代理对象的强引用问题？
```objc
1、NSURLSession对象在使用的时候，如果设置了代理，那么session对代理对象会保持一个强引用，
在合适的时候应该主动进行释放；
2、可以在控制器调用viewDidDisappear方法的时候来处理，可以通过调用invalidateAndCancel方法或者是finishTaskInvalidate方法释放对代理对象的强引用；
3、invalidateAndCancel方法可以直接取消请求然后释放对象，finishTaskAndInvalidate方法会等请求完成之后再释放对象
```

### 文件下载注意点
```objc
// NSURLSessionDataTask 

1、内存暴涨问题     NSFileHandle边接受数据边写入沙盒， 也可以使用NSOutputStream流来写数据
2、监听文件的下载进度 = 已经下载的数据(累加) / 请求的总数据的大小（从响应头里面获取）
3、常见的操作：开始下载\暂停下载\取消下载\继续下载
4、断点下载，在请求头里面设置“Range”
5、进度信息不正确（响应头中的expectedContentLength并不是文件的总大小，只是本次请求的文件数据的大小）
6、文件不完整
	6.1、判断只有第一次发送请求下载的时候才创建空的文件
	6.2、调整：移动文件句柄指针到文件的末尾
7、离线断电下载（断点续传）
8、输出流的基本使用
9、要监听下载进度，只能使用代理方式来进行监听

// NSURLSessionDownloadTask
1、block下载文件，已经解决了内存问题，但是无法监听下载进度问题，要想监听下载进度，还是要用代理方式
2、delegate下载，解决监听进度问题
3、常见的操作：开始下载\暂停下载\取消下载\继续下载
4、断点下载，resumeData
5、注意，使用NSURLSessionDownloadTask进行下载无法实现断点续传，只能使用NSURLSessionDataTask 

```


### AFNetworking 实现文件上传
```objc
- (void)uploadFile {
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    NSDictionary *dict = @{
        @"username" : @"jackey",
        @"password" : @"jackey"
    };
    [[manager POST:@"https://www.baidu.com/upload/images" parameters:dict constructingBodyWithBlock:^(id<AFMultipartFormData>  _Nonnull formData) {
        /**
         参数1: 要上传的文件的二进制数据
         参数2: 具体的参数值 固定值“file”，这个值是服务器规定的，必须要传file
         参数3:文件的名称，可以随便取，比如123.png
         参数4:文件的二进制数据类型
         */
        NSData *imageData = [NSData dataWithContentsOfFile:@"/Users/yanrenhao/Desktop/OC/TestCategary/TestCategary/liuyifei/1.png"];
        // 第一种方式，需要将图片转化为二进制数据
        [formData appendPartWithFileData:imageData name:@"file" fileName:@"123.png" mimeType:@"image/png"];
        
        // 第二种方式，不需要先转化为二进制数据，直接传文件的路径就行
        [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"/Users/yanrenhao/Desktop/OC/TestCategary/TestCategary/liuyifei/1.png"] name:@"file" fileName:@"123.png" mimeType:@"image/png" error:nil];
        
        // 第三种方式
        [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"/Users/yanrenhao/Desktop/OC/TestCategary/TestCategary/liuyifei/1.png"] name:@"file" error:nil];
    } progress:^(NSProgress * _Nonnull uploadProgress) {
        // 计算进度问题
        CGFloat progress = 1.0 * uploadProgress.completedUnitCount / uploadProgress.totalUnitCount;
        NSLog(@"%.2f", progress);
    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        // 上传成功回调
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        // 上传失败回调
    }] resume];
}
```

##### AFNetworking 默认解析方式是JSON解析，如果不是JSON，而是XML或者其他类型的数据，要设置解析方式
```objc
- (void)afnGET {
    // 创建会话管理者
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    
    // AFN 内部已经对f服务器的数据进行了JSON解析操作，AFJSONResponseSerializer
    // 如果返回的数据是XML类型，那么要调整manager对应响应的解析方式AFXMLParserResponseSerializer
    // 设置解析方式为XML
    manager.responseSerializer = [AFXMLParserResponseSerializer serializer];
    
    // 如果服务器返回的数据既不是JSON，也不是XML， 那我们就不做处理，
    // manager.responseSerializer = [AFHTTPResponseSerializer serializer];
    
    [manager GET:@"https://www.baidu.com/weather" parameters:@{@"city" : @"beijing"} progress:nil success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        // 获取解析器
        NSXMLParser *p = (NSXMLParser *)responseObject;
        // 设置代理
        p.delegate = self;
        // 开始解析，实现代理方法，去代理方法中，将XML数据转OC对象
        [p parse];
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        
    }];
}

```



### JSON 解析方案
1、第三方框架：JSONKit， SBJson,  TouchJSON(性能从左到右，越来越差)</br>
2、苹果原生：NSJSONSerialization(性能最好)</br>
3、并不是所有的OC对象都能转化为JSON数据，能转化为JSON数据必须满足以下条件</br>
	3.1、最外层的对象必须是NSArray或者NSDictionary</br>
	3.2、字典或者数组中的所有的元素只能是NSString、NSNumber、NSArray、NSDictionary、或者NSNull</br>
	3.3、字典中的所有的key都必须是NSString</br>
	3.4、NSNumber是标准的并且不是无穷大

```objc
// OC对象-->JSON数据。   序列化
+ (nullable NSData *)dataWithJSONObject:(id)obj options:(NSJSONWritingOptions)opt error:(NSError **)error;

// JSON数据--> OC对象。    反序列化
+ (nullable id)JSONObjectWithData:(NSData *)data options:(NSJSONReadingOptions)opt error:(NSError **)error;
```

### 原生XML解析代码
```objc
#pragma mark - XML 解析
- (void)xmlParser {
    // XML 解析分为DOM解析和SAX解析
    // DOM解析是一次性把所有的文件同时加载到内存当值，适合解析小文件
    // SAX接受是逐级一个元素一个元素解析，解析大文件就用SAX，苹果原生的XML解析NSXMLParser就是采用的SAX解析方式
    // GDataXML:DOM解析方法，是一个有Google开发的基于libxml2的DOML解析方式的三方库
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"school" ofType:@"xml"];
    NSData *xmlData = [NSData dataWithContentsOfFile:filePath];
    NSXMLParser *parser = [[NSXMLParser alloc] initWithData:xmlData];
    parser.delegate = self;
    // 下面这个方法是阻塞式的，需要等解析代理方法都执行完了，然后才能走后面的代码
    [parser parse];
    
    // 回到主线程，然后刷新数据，因为上面的阻塞式的解析，所以到这儿的时候，一定有数据
    [[NSOperationQueue mainQueue] addOperationWithBlock:^{
       // 刷新表哥数据
        
    }];
}

#pragma mark - NSXMLParserDelegate
// 开始解析
- (void)parserDidStartDocument:(NSXMLParser *)parser {
    NSLog(@"开始解析数据");
}

// 这个方法会调用很多次
- (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName attributes:(NSDictionary<NSString *,NSString *> *)attributeDict {
    NSLog(@"开始解析%@这个元素，它的值为%@\n", elementName, attributeDict);
    
    // attributeDict包含里所需要的数据字典，然后通过字典转模型，保存到数组中
}

- (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName {
    NSLog(@"%@这个元素解析完毕", elementName);
}

// 解析完毕
- (void)parserDidEndDocument:(NSXMLParser *)parser {
    NSLog(@"解析完毕");
}

```
