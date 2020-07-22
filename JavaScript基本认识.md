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