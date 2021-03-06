### JS 的使用方式
##### 1、直接在HTML文件的head标签中script标签中使用
```html
	<script>
        // 运行的时候，弹窗输出
        alert("你好，世界")

        // 点击按钮的时候弹窗
        function f(string) {
            alert(string)
            console.log('this is my first output string')
        }

        console.log('我这个是在控制台输出')
    </script>

```

##### 2、通过引入外部js文件
```html
<script src="index.js"></script>
```

### JS中的数据类型
###### 字符串， 浮点型，整型， Bool， 空， 为定义类型
```JavaScript
 // JS中的基本数据类型
 // JS中的基本数据类型
 var name = 'jack';  			//string
 var money = 18.5;	 			//number
 var age = 18;					//number
 var flag = true;				//boolean
 var car = null;				//object
 var number = undefined;		//undefined
 
 // 判断变量的真实类型， typeof
 console.log(typeof name, typeof money, typeof age, typeof flag, typeof car, typeof number);
```

### 数组
```JavaScript
        // 创建一个数组，
        var arr1 = new Array()
        var arr2 = []

        //  注意在JS中，数组中可以放置任何的内容
        arr1 = ['jack yan', 18, false, 1.85];

        // 访问数组元素
        var obj1 = arr1[0];

        // 遍历数组中的元素
        for (var i = 0; i < arr1.length; i++) {
            console.log(arr1[i]);
        }

        // 数组的增加和删除
        // 删除一个元素
        arr1.pop();

        // 增加一个元素
        arr1.push('我是新增加的元素');

        // 修改一个元素
        arr1[0] = 'Jackey Yan';

        console.log(arr1); //.    ["Jackey Yan", 18, false, "我是新增加的元素"]
```


### 函数

```JavaScript
// 可以返回值，也可不返回值
function compute(paramter1, parameter2) {
	 return paramter1 + parameter2
}
        
 function hello(s) {
	console.log(s)
}

// 调用函数，可以直接调用，也可以通过事件出发
// 直接调用
 hello('hello, world');
        
// 通过按钮onclick事件调用也行
<input type="button" onclick="hello('hello, world')" value="我是一个按钮，你点我呀">

// 函数当中有一个内置数组，arguments
function add() {
	var result = 0;
	for (var i = 0; i < arguments.length; i++) {
		result += arguments[i];
	}
	return result;
}
        
// 匿名函数
var func = function () {
	console.log('我是一个匿名函数');
}
// 调用匿名函数
func();
```


### 对象
```javascript
	    // 传统对象的产生
        var person = {
            name : '刘亦菲',
            age : 18,
            height : 1.68,
            friends : ['Jackey Yan', '舒畅'],

            favorite : function () {
                console.log('我的爱好是拍戏呀');
            },

            run : function (num, place) {
                console.log('我参加了第' + num + place + '马拉松比赛');
            },

            introduce : function () {
                console.log('大家好，我是' + this.name + ',身高' + this.height + ',今年' + this.age + '岁啦');
            }
        }
        // 访问对象属性，修改年龄
        person.age = 19;
        console.log(person.name, person.age, person.height, person.friends);
        // 调用对象方法
        person.favorite()
        person.run(18, '上海');
        person.introduce()
```

### 通过类，批量产生对象
```javascript

	    // 批量产生对象，就是相当于创建一个类，然后通过类来创建实力对象, 通过new来创建一个对象
        function Person() {
            this.name = null;
            this.age = null;
            this.height = null;

            this.study = function (subject) {
                console.log('大家好， 我是' + this.name + '，身高' + this.height + '今年' + this.age + '岁.' + '我最喜欢的科目是' + subject);
            };

            this.favorite = function (favo) {
                console.log('我的爱好是' + favo);
            };
        }

        var person1 = new Person();
        person1.name = 'La Bron James';
        person1.age = 30;
        person1.height = 2.06;

        person1.favorite('篮球');
        person1.study('体育');		

```


### JS Dom操作
```html

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>JS内置对象Window</title>
    <script>
        /*
        *  window内置对象
        *   1、所有的全局变量都是window的属性;
        *   2、所有的全局函数都是window的方法
        * */
        var main = window.document.getElementById('main');
        var  input = window.document.getElementsByTagName('input');

        // 动态跳转
        function goBaidu() {
            window.location.href = 'https://www.baidu.com'
        }

        /*
        * 内置对象 document 的作用
        * 1、动态的获取当前页面中的所有的标签
        * 2、动态的对获取的标签进行CRUD（增删改查)操作
        * */

        // 动态的插入文字或者标签
        document.write('Hello World');
        document.write('<input type="button" value="我是插入的按钮" onclick="goBaidu()">');

        var flag = true

        function updateImage() {
            flag = !flag;
            // 先获取到图片的标签
            var imgs = document.getElementsByClassName('liuyifei');// 这个获取的是一个数组
            var img = imgs[0];
            if (flag) {
                img.src = 'fei/fei1.jpg';
            } else  {
                img.src = 'fei/fei0.jpg';
            }
        }
    </script>
</head>
<body>
    <div class="head"></div>
    <div id="main"></div>
    <div class="footer"></div>
    <input type="button" value="按钮" onclick="goBaidu()">
    <br>
    <img src="fei/fei0.jpg" alt="" class="liuyifei" width="300px">
    <input type="button" value="修改图片" onclick="updateImage()">
</body>
</html>

```

#### 闭包执行，Dom操作，切换图片
```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <img src="fei/fei0.jpg" id="fei" alt="刘亦菲" width="300" height="400">
    <br>
    <input type="button" class="pre" value="上一张">
    <input type="button" class="next" value="下一张">

    <script>
        // 闭包执行，放置外界修改
        /*
        (function() {
        
        })();
        * */
        (function () {
            var idx = 0;
            // 获取需要的标签元素
            var img = document.getElementById('fei');
            var pre = document.getElementsByClassName('pre')[0];
            var next = document.getElementsByClassName('next')[0];

            pre.onclick = function () {
                idx--;
                if (idx == -1) idx = 8;
                updateImage();
            }

            next.onclick = function () {
                idx++;
                if (idx ==9) idx = 0;
                updateImage();
            }

            function updateImage() {
                var imagename = 'fei/fei' + idx + '.jpg';
                img.src = imagename;
            }
        })();

    </script>
</body>
</html>

```


### 增删改查操作
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>增删改查操作</title>
    <style>
        div {
            float: left;
            margin: 50px;
            width: 200px;
            height: 200px;
            background: cyan;
            overflow: auto;
        }
    </style>
</head>
<body>
    <!--    增加-->
    <div id="create">
        <input id="createBtn" type="button" value="添加">
    </div>
    <!--        删除-->
    <div id="delete">
        <input id="deleteBtn" type="button" value="删除">
        <img id="deleteImage" src="fei/fei1.jpg" alt="">
    </div>
    <!--更新-->
    <div id="update">
        <input id="updateBtn" type="button" value="修改">
        <img id="updateImage" src="fei/fei1.jpg" alt="">
    </div>
    <!--查询-->
    <div id="read">
        <input id="readBtn" type="button" value="查询">
    </div>

    <script>
        // 在这里实现增删改查操作
        (function () {
            // 先获取到要操作的对应的元素div元素
            var createDiv = document.getElementById('create');
            var deleteDiv = document.getElementById('delete');
            var updateDiv = document.getElementById('update');
            var readDiv = document.getElementById('read');

            // 获取按钮元素
            var createBtn = document.getElementById('createBtn');
            var deleteBtn  = document.getElementById('deleteBtn');
            var updateBtn  = document.getElementById('updateBtn');
            var readBtn  = document.getElementById('readBtn');

            // 动态添加一个元素， 如果直接通过document.write('这里的文字会直接写入到body里面');
            createBtn.onclick = function () {
                // 先创建一个img元素
                var tempimage = document.createElement('img');
                // 赋值图片
                tempimage.src = 'fei/fei0.jpg';
                createDiv.appendChild(tempimage)
            }

            // 动态删除一个元素
            deleteBtn.onclick = function () {
                var deleteImg = document.getElementById('deleteImage');
                deleteImg.remove();
            }

            // 更改元素内容
            updateBtn.onclick = function () {
                var updateImage = document.getElementById('updateImage');
                updateImage.src = 'fei/fei3.jpg';
            }

            // 查询
            readBtn.onclick = function () {
                var read = document.getElementById('read');
                console.log(read.childNodes);
            }
        })();
    </script>
</body>
</html>
```