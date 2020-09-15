# HTML 常用标签

###### 1、指定编码
```html
<meta charset="utf-8">
```

###### 2、 标题标签，H家族
```html
<h1>1</1>
<h2>2</2>
<h3>3</3>
<h4>4</4>
<h5>5</5>
<h6>6</6>

<!--分割线-->
<hr>
```

###### 3、输入标签, type类型比较常用的类型：text, date, color, file, button, checkbox, radiobox
```html
<input placeholder="我是占位文字">
<input value="我是默认文字">
<input type="">
```

###### 4、段落标签
```html
<p>我是一个段落标签，这里面的是一个段落</p>
```

###### 5、图片标签，如果设置了高度，不设置宽度，就是等比例适配宽度，同理一样的等比例适配高度，alt的值可以设置也可不设置, 还可以设置百分比来设置宽高
```html
<img src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1595169990354&di=3b13f3815b76df6a80da9220d08a47e0&imgtype=0&src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201705%2F11%2F20170511151045_aezTw.thumb.700_0.png" height=“400” alt="图片显示不出来的时候的占位文字">
<img src="images/1.jpg" width="=30% alt="我是占位文字呀">
```

###### 6、超链接标签，a标签, 除了给文字包装a标签，也可以给图片包装a标签， target属性在移动端用不到，因为只有一个窗口
```html
<a href="https://www.baidu.com">百度一下，你就知道啦！</a>
<a href="https://www.taobao.com"><img src="images/1.jpg"></a>
<a href="https://www.baidu.com" target="_black">在空白的窗口打开标签</a>
<a href="https://www.baidu.com" target="_self">在当前窗口打开标签</a>
```

###### 7、换行
```html
 <br>
```

###### 8、无序列表
```html
 <ul> unorder list
```

###### 9、有序列表
```html
 <ol> order list 
```

###### 10、每一行元素
```html
 <li> list item
```

###### 11、div标签， 容器标签


###### 12、article

###### 13、header

###### 14、footer

###### 15、section

###### 16、nav

######17、meter， 进度条，特定范围内的数值，如工资、数量、百分比
```html
<meter value="50" max="100"><meter>
```

######18、progress ,也是进度条，和meter差不多
```html
<progress value="30" max="100"></progress>
```

###### 19、video 视频

###### 20、audio 音频



# 常见的选择器
```html
<div class="one">
    <div class="two">
        <p class="five">我是一段文本段落</p>
        <p class="five">我是一段文本段落</p>
        <p id="six">我是一段文本段落</p>
        <div>
            <p>我也是一个文本段落</p>
        </div>
    </div>
    <div class="three">
        <p class="five">我是一段文本段落</p>
        <p class="five">我是一段文本段落</p>
        <p id="seven">我是一段文本段落</p>
        <div>
            <p>我也是一个文本段落</p>
        </div>
    </div>
    <div class="four">
        这是一个文本
        <ul>
            <li>我也不知道是什么</li>
            <li>我也不知道是什么</li>
            <li>我也不知道是什么</li>
            <li>我也不知道是什么</li>
            <li>我也不知道是什么</li>
        </ul>
    </div>
</div>
```

#### 1、标签选择器	  		div { color: orange;}
#### 2、类选择器		.		.five { color: orange;}
#### 3、ID选择器		#	#seven{ color: orange;}
#### 4、并列选择器		,		 eg: div, .five, #seven {  color: orange;}
#### 5、后代选择器	,空格	.two div p { color: orange; }
#### 6、复合选择器  div.three div p { color: orange;}	注意，div.three是复合的，选中的是类为three的div标签，然后接着找下面的为div 的子元素，然后再找下面的p标签
#### 7、直接后代选择器 > 		.three>p    这个只能找到直接后代，不能找到后代的后代
#### 8、伪类选择器

### 盒子模型
让一个容器固定在浏览器的某一个位置，position属性<br>
设置定位元素的位置，规律，
##### “父相子绝”

#### 块级元素在父元素中水平居中显示
margin： 0 auto;
#### 行内标签和行内块级标签如果要在父元素中水平居中，直接使用text-align:center即可
#### 如果让元素垂直居中，可以设置行高为父元素的高度，line-height:100px;

### W3C 盒子模型和微软提出来的盒子模型计算是不一样的，所以要适配盒子模型，需要修改盒子的计算模式
box-sizing:border-box;

#### 设置阴影
box-shadow: 5px 5px red;
#### 设置透明度
opacity: 0.5;

##### 图片垂直居中： vertical-align：middle；

# 兼容移动端的代码
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```


