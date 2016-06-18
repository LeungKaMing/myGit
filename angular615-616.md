#我的angular
##零、MVVM (Model-View-viewModel)
  1、通过viewModel实现双向绑定，即DOM--view和$rootScope/$scope--Model的双向绑定
  2、利用Angular数据与视图双向绑定的特性，可以把控制器的结果（操作模型）直接用在我们的DOM上（展示在视图上）
  【所以控制器在angular中占了很大比重】
##一、备注
1、angular的$rootScope相当于JS的window全局作用域
2、习惯性用ng-model来接收input标签里的值，并且相当于给根作用域挂载了一个ng-model的属性
3、angular输出字符串一定要使用''
##二、一些常用的angular书写方式
1、单独输出时的格式:
```
//angular习惯用{{}}来获取angular变量
{{'angular变量'}}
//同样可以在{{}}里面写三元表达式
{{'angular变量'==true?'显示':'隐藏'}}
```
2、可以把angular变量放在标签里:
```
//注意:要是没有用{{}}包住，浏览器会把其当成元素的内容解析
<div>{{'angular变量'}}</div>
```
3、如何解决angular变量的闪烁问题
1)ng-bind 
给DOM元素加上ng-bind属性，只可以解决一个变量闪烁的问题【注意使用该属性后，该DOM元素不能有任何内容，否则还是会出现闪烁问题】
```
//这两者用法都是获取angular变量，只是后者要放在一个DOM元素内调用
{{'angular变量'}}
<div ng-bind="angular变量"></div>
```
2)ng-cloak
给DOM元素加上ng-cloak属性，同样可以解决闪烁问题【注意使用该属性，必须要同时引入angular-csp.css】
```
//伪代码
<head>
<link href="./angular-csp.css">
</head>
...
<body>
    <div ng-cloak>{{'angular变量'}}</div>
</body>
```
4、ng-non-bindable
取消该DOM元素的angular属性==>不做angular解析
```
	<div ng-non-bindable>{{'angular变量'}}</div> //没有进行解析，直接输出{{'angular变量'}}
```
5、ng-bind-template
待查，目前知道的是给DOM元素绑定多个model

##三、更多的angular属性
1、ng-init 
在任何位置进行初始化并声明angular变量
```	
<body ng-init="name='我是默认值';age=3">
    //因为已经初始化并声明了两个angular变量:name和age，所以可以在任何位置用angular的格式调用
   	<input type="text" ng-model="test">
   	<div ng-bind="name"></div>	//直接使用body的初始化angular值【这个就是初始化变量的好处，相当于JS的VAR】
   	<div ng-bind="test"></div>	//通过input标签获取ng-model的值
</body>
```
2、ng-repeat
2.1 作用:遍历angular变量 
1)在哪里添加ng-repeat就表示在哪里开始遍历
2)每遍历一次ng-repeat都会生成一个私有作用域
```
<!-- 初始化并创建angular变量 -->
<body ng-init="phones=[{name:'三星',age:'5'},{name:'华为',age:'3'},{name:'苹果',age:'10'}]">
    <!-- 遍历angular变量 ==> phones有3个对象项就生成3个div，每个div里面都可以调用对象项的属性:name和age，以及angular的自带属性:$index,$even,$odd...-->
    <div ng-repeat="p in phones">{{$index}}{{p.name}}{{p.age}}{{$even}}</div>
</body>
```
补充:
```
//输出当前索引，默认是从0开始;当用于表示序号的时候习惯加上1
{{$index}}
//判断是否为偶数，布尔类型
{{$even}}
//判断是否为奇数，布尔类型
{{$odd}}
```
扩展:
实现如下效果：
list[0][0]
list[0][1]
list[0][2]
list[1][0]
list[1][1]
list[1][2]
list[1][3]
list[2][0]
list[2][1]
```
<!DOCTYPE html>
<html ng-app>
<head>
	<title></title>
	<link rel="stylesheet" type="text/css" href="./angular-csp.css">
</head>
<body ng-init="phones=[{name:['a','b','c']},{name:['a','b','c','d']},{name:['a','b']}]">

	//这里是不能显示parent的，因为每一个ng-repeat都是私有作用域
	{{parent}}

	//遍历phones里面有多少项，并且初始化创建一个angular变量parent来装着每一项对应的索引，也就3项:0,1,2
	<div ng-repeat="phone in phones" ng-init="parent=$index">
		
			//遍历上面每一项的name属性，并且上面之所以创建一个angular变量parent来装着外层索引，是避免内层又用{{$index}}表示内层索引的时候引起冲突
			//2016/6/15 尝试在内层同样初始化创建一个angular变量son来装着内层索引，同样能成功显示
    		<div ng-repeat="p in phone.name" ng-init="son=$index">
			
			//我们要的效果
			list[{{parent}}][{{son}}]
		</div>
	</div>
</body>
<script type="text/javascript" src="./angular.js"></script>
</html>
```
总结:
ng-init除了可以用来初始化并创建angular变量，还可以用于保留变量==>完美解决同时使用公共变量导致的冲突

2.2 当遇到重复项的时候怎么处理 ==> 要把重复项也输出
1)在DOM元素的遍历后面紧跟:track by "以什么样的方式进行循环" =>phone in phones track by $index 以索引的方式进行循环
2)下面伪代码既用ng-cloak解决了闪烁问题，又用ng-repeat="x in xxx track by $index"解决了重复项不遍历的问题
```
<!DOCTYPE html>
<html ng-app>
<head>
	<title>遇到重复状况的时候还要遍历怎么处理</title>
	<link rel="stylesheet" type="text/css" href="./angular-csp.css">
</head>
<body ng-init="phones=['三星','华为','苹果','苹果']">
	<div ng-repeat="phone in phones track by $index" ng-cloak>
		<div style="background: green;width: 100px;heigh:100px;">{{phone}}</div>
	</div>
</body>
<script type="text/javascript" src="./angular.js"></script>
</html>
```
3、ng-if和ng-show/ng-hide
```
//1、ng-if 当条件为true时显示，为false时隐藏
<body ng-init="flag=true">
	<div ng-click="flag=!flag">
		{{flag?'展示':'隐藏'}}
	</div>
	<div ng-if="flag">
		{{flag}}
	</div>
</body>

//2、ng-show 当show为true时显示，为false时隐藏
<body ng-init="flag=true">
	<div ng-click="flag=!flag">
		{{flag?'展示':'隐藏'}}
	</div>
	<div ng-show="flag">
		{{flag}}
	</div>
</body>

//3、ng-hide 当hide为true时隐藏，为false时显示
<body ng-init="flag=true">
	<div ng-click="flag=!flag">
		{{flag?'展示':'隐藏'}}
	</div>
	<div ng-hide="flag">
		{{flag}}
	</div>
</body>

//4、这种方式只是为了提醒你，只要导入了angular变量，在哪个位置都能调用{{}}
<body ng-init="flag=true">
	<div ng-click="flag=!flag">
		{{flag?'展示':'隐藏'}}
	</div>
	<div class="{{flag?'block':'none'}}">
		{{flag}}
	</div>
</body>
```
	
DEMO:
```
<body ng-init="phones=[1,2]">
	<!-- 判断数组长度比判断判断是否存在要稳妥得多；同时，这里也解决了闪烁的问题 -->
	<!--如果条件为true，则遍历的内容能显示；否则，遍历的内容隐藏-->
	<div ng-if="phones.length" ng-cloak>
		<div ng-repeat="phone in phones">
			{{phone}}
		</div>
	</div>
</body>
```
三者之间区别:
ng-show/ng-hide 单纯操作CSS样式，还是走里面的指令，只是false时隐藏掉而已，但是并没有整个元素移除
ng-if 是false时，里面的代码不再执行，并且整个元素移除掉
结论:判断长度比判断是否存在要稳妥得多。

4、ng-switch
类似原生JS的switch...case...default...
```
//原生JS
switch(name){
    case:"hello":
        console.log("hello");
        break;
    case:"world":
        console.log("world");
        break;
    default:
        console.log("written by Ljm");
}
```

angular通过ng-switch来筛选对应的结果，用ng-switch-when来匹配值，ng-switch-default代表默认值
```
<body>
<!-- 专门用ng-model来获取input标签中用户输入的值 -->
	<input type="text" ng-model="name">
<!-- 根据检测到用户输入的值，来筛选对应的结果 -->
	<div ng-switch="name">
		<div ng-switch-when="hello">hello</div>
		<div ng-switch-when="world">world</div>
		<div ng-switch-default>written by Ljm</div>
	</div>
</body>
```

5、ng-class
动态添加样式用ng-class，静态添加样式用class。

ng-class有两种写法:
写法一:给一个DOM元素设置一个样式名(2选1)
```
ng-class={
    true:'样式名1',false:'样式名2'
}[true/false]
```
写法二:给一个DOM元素同时设置多个样式名
```
ng-class={
    '样式名1':true,'样式名2':true,'样式名3':false...'样式名n':true
}
```

DEMO:
```
<body ng-init="isActive=true;style1=true;style2=true">
	<!-- 动态添加样式用ng-class,静态添加样式用class -->
	<div ng-class="{true:'yellow',false:'red'}['isActive']">
		Hello,chenliyingBaBy.
	</div>
	<div class="static" ng-class="{'yellow':style1,'red':style2}">
		Hello,mix baby.
	</div>
</body>
```
结论:
写法1只能写两种样式，且存在决定性属性名，根据决定性属性isActive的值为true还是false来在两种样式之间进行切换
写法2能够写多种样式，但是不存在决定性属性名进行切换这么麻烦

扩展:
```
<html ng-app>
<head>
	<title></title>
	<style type="text/css">
		*{
			margin: 0;
			padding: 0;
		}
		.danger{
			background-color: red;
		}
		.warning{
			background-color: yellow;
		}
		.mix{
			color: lightgreen;
		}
	</style>
</head>
<!-- 默认是angular变量style存放的是danger样式 -->
<body ng-init="style='danger'">
	<input type="text" ng-model="style">

	<!-- 利用三元表达式和花括号内可以放字符串的特性来设置样式 -->
	<div class="{{style=='danger'?'danger':'warning'}}">{{style}}</div>

	<!-- 多种情况 -->
	<div ng-switch="style">
		<div ng-class="style" ng-switch-when="danger">{{style}}</div>
		<div ng-class="style" ng-switch-when="warning">{{style}}</div>
		<div ng-class="style" ng-switch-when="default">{{style}}</div>
	</div>

	<!-- 左边放样式名，右边放angular变量判断的布尔值==>实现一个DOM元素可以写多种样式名 -->
	<div class="mix" ng-class="{'danger':style=='danger','warning':style=='warning'}">一个DOM元素写多种样式mix</div>

	<!-- 单纯检测ng-class对样式字符串/对angular变量的反应 -->
	<div ng-class="danger">ng-class写对应样式的字符串也不会有效果</div>
	<div ng-class="style">ng-class写对应样式的angular变量就有效果</div>

</body>
<script type="text/javascript" src="./angular.js"></script>
<script type="text/javascript">
/*
	结论：
	1、ng-class里面只能够放代表样式名的angular变量style，如果放对应样式的字符串是不会生效的=>对应样式的字符串想要生效必须放在class里面，要是不想用字符串那么请用angular变量
	2、ng-switch跟ng-if一样，对于不成立的条件一样会移除所在位置的DOM元素
*/
</script>
</html>
```

6、ng-click
用于给DOM元素绑定Angular的点击事件

DEMO：思路：只要你点击，就肯定会动态添加一个样式active ==> 只有第一个和第四个默认是有颜色的
```
<body>
<!-- 初始化并创建angular变量init,值为home-->
<ul ng-init="init='home';isActive=true">
<!-- 用户点击哪个li，就执行对应li的事件==>实现点哪个就重写一遍init -->
     <li ng-click="init='home';isActive=true" ng-class="{'active':init=='home'}"><a>home</a></li>
    <li ng-click="init='contact';isActive=false" ng-class="{'active':init=='contact'}"><a>contact</a></li>
    <li ng-click="init='introduce';isActive=false" ng-class="{'active':init=='introduce'}"><a>introduce</a></li>
    <li ng-click="init='home';isActive=true" ng-class="{true:'active',false:'default'}[isActive]">home too</li>
</ul>
</body>
```

7、ng-include
用于动态引入页面tmp.html
```
<body>
	<div ng-include="'tmpl.html'"></div>
</body>
```

8、过滤器(angular变量+管道符实现简单过滤)
|管道符前面的是angular变量，以下省略:
| currency 默认是美元
| uppercase 转换为大写
| lowercase 转换为小写
| limitTo:n 限制显示的位数(从左往右数起)
| number:n 显示最后第几位小数
| json 转换为json格式
| date:'yy-MM-dd hh-mm-ss' 将时间字符串转换为时间格式
```
<body ng-init="name={aa:1}">
	{{123.123 | currency}}货币过滤器(默认是美元)<br>
	{{'abcdefg' | uppercase}}转换大写<br>
	{{'ABCDEFG' | lowercase}}转换小写<br>
	{{'1234567890' | limitTo:3}}限制显示的位数(从左到右)<br>
	{{'123456789.23' | number:1}}显示最后一位小数<br>
	{{name | json}}将对象{aa:1}转换为json格式<br>
	{{1466090816808 | date:'yyyy-MM-dd hh:mm:ss'}}将时间字符串转换为时间格式<br>
</body>
```

9、orderBy *
区别于track by:
1、两者都是加在ng-repeat的遍历后面，但是track by是直接加上即可；orderBy是需要在前面加上管道符|
2、track by是用于把重复的都全部排列出来；orderBy也能实现把重复的都全部排列出来
3、orderBy:'age':'reverse'/orderBy:'-age'可以进行倒序
补充：
orderBy的排序规则是由紧跟后面的值决定的
```
<!--orderBy后面可以继续加冒号进行排序规则的增加，但是决定权会在最后一个冒号处，要是最后一个冒号是可变的，那么元素的排序自然会跟着变化；要是最后一个冒号是不变的(就像单单orderBy:'age')，那么元素的排序只会有一种排列方式-->
<body>
	<div ng-init="phones=[{name:'苹果',age:5},{name:'三星',age:8},{name:'诺基亚',age:20},{name:'苹果',age:5}];flag=true">
		<button ng-click="flag=!flag"></button>
		<ul>
			<!-- <li ng-repeat="p in phones track by $index"></li> -->
			<li ng-repeat="p in phones | orderBy:'age':flag">
				{{p.name}}{{p.age}}
			</li>
		</ul>
	</div>
</body>
```

DEMO：
思路:实现点击年龄和语文分数进行【表格排序】
注意:不点击这两列的话，只遍历不排序->即orderBy会失效，只有前面的遍历生效
```
<body ng-init="phones=[{name:'苹果',age:5,chinese:60},{name:'三星',age:10,chinese:5},{name:'诺基亚',age:0,chinese:99}]">
	<table class="table">
		<tr>
			<th>序号</th>
			<th>姓名</th>
			<th ng-click="flag='age'">年龄</th>
			<th ng-click="flag='chinese'">语文分数</th>
		</tr>
		<!--三行四列-->
		<!--orderBy不是默认一定要生效才能写的-->
		<tr ng-repeat="p in phones | orderBy:flag">
			<td>{{$index+1}}</td>
			<td>{{p.name}}</td>
			<td>{{p.age}}</td>
			<td>{{p.chinese}}</td>
		</tr>
	</table>
</body>
```

10、filter 
筛选器:给用户筛选出某条数据出来。
注意:
    1、filter和orderBy是被|管道符隔开的，证明这两个功能是独立的。
    2、筛选器格式:filter:{xxx:xxx} ==>
DEMO:同样是表格排序，在其基础上加上筛选出指定的数据
```
<body>
	<input type="text" ng-model="query">
	<div ng-init="phones=[{name:'apple',age:3,chinese:60},{name:'pear',age:5,chinese:0},{name:'pineapple',age:1,chinese:3}];flag='null'">
		<table class="table">
			<tr>
				<th>序号</th>
				<th>姓名</th>
				<th ng-click="flag='age'">年龄</th>
				<th ng-click="flag='chinese'">分数</th>
			</tr>
			<!--筛选出语文分数值等于query的数据出来==>由于变量query是由input标签同步过来的，所以很炫酷哦-->
			<tr ng-repeat="p in phones | orderBy:flag | filter:{chinese:query}">
				<td>{{$index+1}}</td>
				<td>{{p.name}}</td>
				<td>{{p.age}}</td>
				<td>{{p.chinese}}</td>
			</tr>
		</table>
	</div>
</body>
```

##四、angular的一些方法
1、angular.element
将原生对象转化成具有少数功能的jq对象 ==> 意味着转换后可以直接使用jq的方法
注意:这样转换的jq对象存在部分缺陷，例如css()这个方法设置值的时候需要写单位，而jQuery是不需要的
```
angular.element("div1").html("Hello,I am using jQuery!");
angular.element("div1").css('fontSize','40px');
```

2、angular.uppercase()
转换成大写
```
	angular.uppercase('abcdefg');

```

3、angular.lowercase()
转换成小写
```
	angular.lowercase('ABCDEFG');

```

4、angular.equals()
注意:打破常规对JS的理解==>NaN==NaN,{}=={}... 这些例子在JS是都不相等的
```
	angular.equals({},{}); //true
	angular.equals(NaN,NaN); //true
```

5、angular.extend() 
对象的继承 => 继承过后，原来的对象并不受影响
```
	var a={'name':1};
	var b={'age':5};
	var c={};
	angular.extend(c,b,a);	//把后面的继承给前面
	console.log(a); //Object {name: 1}
	console.log(b); //Object {age: 5}
	console.log(c); //Object {age: 5, name: 1}
```

6、angular.fromJson() / angular.toJson()
类似原生JS的JSON.parse() / JSON.stringify()
```
	var json='{"name":"ljm","age":23}';

	//将JSON格式的字符串转换成JSON对象==>fromJson
	json=angular.fromJson(json); //Object {name: "ljm", age: 23}

	//将JSON对象转换成JSON格式的字符串
	json=angular.toJson(json); //{"name":"ljm","age":23}

	console.log(json);
```

7、angular.copy()
复制属性 => 将前面的值粘贴到后面，并且覆盖后面原来的值

注意:区别于继承angular.extend() 
=> 1、copy是会覆盖后面原来的值，extend不会覆盖后面原来的值 2、copy是会把前面覆盖后面的，extend是把后面的继承给前面

继承比复制要友好啊~
```
    var a={'name':'ljm'};
	var b={'age':23};
	//将前面的值黏贴到后面，并且覆盖后面原来的值，区分于angular.extend()，它是将后者继承到前面
	angular.copy(a,b); 
	console.log(a,b); // Object {name: "ljm"} Object {name: "ljm"} 证明后面b的age属性值消失了
```

8、angular.forEach()
angular中默认自带的遍历方法，类似原生JS的[].forEach(function(item,index,input){})
区别于ng-repeat这个angular的属性:
1、这个方法在遍历后还提供了回调函数；而ng-repeat这个属性就只能单纯遍历，做一些排序和筛选而已。相较之下，angular.forEach强大得多。
2、angular.forEach()有三个参数:第一个参数为数组，第二个参数为回调函数，第三个参数为this指向
```
	var ary=[{name:1},{name:2},{name:3}];
	var result=[];
	angular.forEach(ary,function(item){
		//这里的this指的是result
		this.push(item.name);
	},result);
```

9、angular.bind()
类似于原生JS的bind方法，只绑定this指向，但是不执行函数
```
	function cb(){
		console.log(this.name);
	}
	
	var obj={name:1};
	
	//为函数cb绑定this指向为obj这个对象
	var c=angular.bind(obj,cb);
	
	//执行函数
	c();
```

##五、一切从模块开始
备注：
    1）一个页面里只能有一个ng-app，并且在做模块化开发的时候必须指定ng-app的名字
    2）在引入angular.js后，默认会生成一个全局作用域$rootScope(相当于原生JS的window，jQuery的global)

1、创建模块
    1）第一个参数是模块的名字
    2）第二个参数是依赖的模块，如果没有的话需要用空数组占位
    3）第三个参数是配置项
```
    <!DOCTYPE html>
    <html ng-app="appModule">
    <head>
    	<title></title>
    </head>
    <body>
    
    </body>
    <script type="text/javascript" src="./angular.js"></script>
    <script type="text/javascript">
    	var app=angular.module('appModule',[]);
    </script>
    </html>
```

2、创建控制器
那么，创建模块后可以做什么呢？
==>首先，你要了解：一个模块可以创建多个控制器，每个控制器都操作DOM的不同数据。
==>其次：利用Angular数据与视图双向绑定的特性，可以把控制器的结果（操作模型）直接用在我们的DOM上（展示在视图上）。
==>最后：澄清一下，控制器是用指令来控制DOM的，并不是直接操作DOM的
DEMO：点击div令里面的值一直加下去。
```
<body>
<!-- 首先写个CSS属性嘛，然后阻止单个元素的闪烁问题，再者给该元素绑定控制器和angular的点击事件 ==> 点击的时候执行method这个只有在控制器 "myCtrl作用域下才有的方法" -->
	<div class="test" ng-controller="myCtrl" ng-click="method()" ng-cloak>
		<!-- 这里{{name}}表示的是angular变量，也是只有在myCtrl这个控制器内才能获取到的$scope.name -->
		{{name}}
	</div>
</body>
<script type="text/javascript" src="./angular.js"></script>
<script type="text/javascript">
	//Note:想要在控制器的作用域内获取name这个angular变量，必须加上{{}}

	var app=angular.module("myAngular",[]);
	
	// 使用控制器 -- 第一个参数是控制器的名字，第二个参数是回调函数 
	// 创建控制器后，会默认带上一个当前作用域做参数$scope；在DOM上，对要使用该控制器的DOM元素添加属性：ng-controller="控制器的名字"
	app.controller('myCtrl',function($scope){
		// 在当前作用域下声明变量name(这个值是私有的，只能在myCtrl这个私有作用域才能获取到)
		$scope.name=0;
		$scope.method=function(){
			$scope.name++;
		}
	})
</script>
```

3、代码进行压缩时，需要注意的问题
我们的代码通常都会进行压缩，所以避免过长的单词被压缩，所以要采用【数组】的方式。
下例在创建模块后配置全局变量$rootScope(run方法优先级最高)，并且根作用域是最高优先级的，所有在其下面的子作用域都能调用根作用域下的angular变量。
```
<body>
    <!-- 只要在控制器中，就可以随时用{{}}获取该控制器所在所用域下的变量 -->
	<div ng-controller="myCtrl">{{name}}</div>
</body>
<script type="text/javascript" src="./angular.js"></script>
<script type="text/javascript">
	var app=angular.module("myAngular",[]); 
	
	//run方法是引入后就马上执行的，我们给全局作用域设置了一个变量name
	app.run(['$rootScope',function($rootScope){
		$rootScope.name={name:2};
		console.log("angular模块创建后我们给根作用域的变量name的name属性设置了值"+$rootScope.name.name);
	}])

	//既然传了两个angular的默认变量进来，所以为了避免被压缩，两个参数都要用字符串格式保存起来
	app.controller('myCtrl',['$scope','$rootScope',function($scope,$rootScope){
		//让具有该控制器属性的DOM元素能够使用该控制器的作用域下的属性name 
		$rootScope.name={name:'hello'};
		console.log("此时的根作用域变量name的name属性值变为"+$rootScope.name.name);
		$scope.name={name:'ljm'};
	}])
</script>
```

4、定时器
原生js实现定时器是用setInterval / setTimeout ==> 其实在angular也能用，只是不能带window开头，类似：window.setInterval / window.setTimeout

DEMO:实现需求
```
写法一:
	setInterval(function(){
    	//但是在setInterval这个函数操作完后，angular并不知道，需要手动通知angular==>用$scope.$apply(),表示这个作用域的数据改变了，angular你刷新一下吧。
		$scope.name++;  
    },1000)
    $scope.$apply();

写法二:    
    setInterval(function(){
	//索性直接在$scope.$apply里面写。
        $scope.$apply(function(){
				$scope.name++;
		})
	},1000)

//PS：以上两种写法都是在我们改变数据后，需要手动通知angular刷新视图。

写法三:
       //直接使用angular提供的方法就不用了呗~~$interval/$timeoutA
       //$interval/$timeout相当于封装了setInterval/setTimeoue并且带上了$scope.$apply
       var timer=$interval(function(){
            $scope.name++;
            if($scope.name>=5){
                //相当于原生的clearInterval(timer)
                $interval.cancel(timer);
            }
       },1000)    

```













###案例
###1、实现点击某按钮，该按钮内容和另一个元素的内容同时改变【显示->隐藏 隐藏->显示】
思路:让点击不断改变，某个基准固定不变即可
```
<!-- 初始化并创建angular变量flag -->
<body ng-init="flag=true">

<!-- 第一次点击flag肯定会变false 这里巧妙的又运用了ng-init来存储变量flag，将其值true保存到pre了 满足条件flag!==pre 显示；
    再次点击后flag会变true 初始化已经将true保存到了pre了 不满足条件flag!==pre 隐藏 
	【就跟var obj =3,obj1 =obj,obj = 4 ==>obj1还是3的原理一样，ng-init只执行一次】
-->
	<div ng-click="flag=!flag" ng-init="pre=flag">
	    //不要忘记{{}}里面是可以放三元运算符的哦；并且在{{}}里面的字符串都会以字符串格式正常解析
		{{flag?'显示':'隐藏'}}
	</div>
	
	<!--判断当前flag的值是否还等于true-->
	<div class="{{flag!==pre?'block':'none'}}">
		content
	</div>
</body>
```
总结:正因为angular的这个特性，所以造就了<div class="{{flag?'none':'block'}}"></div>这些写法 ==> 相当于<div class="block"></div>

###2、angular通过ng-switch来筛选对应的结果，用ng-switch-when来匹配值，ng-switch-default代表默认值==》匹配成功则显示，匹配不成功则移除
```
     <body>
     <!-- 专门用ng-model来获取input标签中用户输入的值 -->
     	<input type="text" ng-model="name">
     <!-- 根据检测到用户输入的值，来筛选对应的结果 -->
     	<div ng-switch="name">
     		<div ng-switch-when="hello">hello</div>
     		<div ng-switch-when="world">world</div>
     		<div ng-switch-default>written by Ljm</div>
     	</div>
     </body>
```

###3、通过ng-class给一个元素动态添加样式
```
<body ng-init="isActive=true;style1=true;style2=true">
	<!-- 动态添加样式用ng-class,静态添加样式用class -->
	<div ng-class="{true:'yellow',false:'red'}['isActive']">
		Hello,chenliyingBaBy.
	</div>
	<div class="static" ng-class="{'yellow':style1,'red':style2}">
		Hello,mix baby.
	</div>
</body>
```

###4、 点击哪个元素就给哪个元素增加样式
思路：只要你点击，就肯定会动态添加一个样式active ==> 只有第一个和第四个默认是有颜色的
```
<body>
<!-- 初始化并创建angular变量init,值为home-->
<ul ng-init="init='home';isActive=true">
<!-- 用户点击哪个li，就执行对应li的事件==>实现点哪个就重写一遍init -->
     <li ng-click="init='home';isActive=true" ng-class="{'active':init=='home'}"><a>home</a></li>
    <li ng-click="init='contact';isActive=false" ng-class="{'active':init=='contact'}"><a>contact</a></li>
    <li ng-click="init='introduce';isActive=false" ng-class="{'active':init=='introduce'}"><a>introduce</a></li>
    <li ng-click="init='home';isActive=true" ng-class="{true:'active',false:'default'}[isActive]">home too</li>
</ul>
</body>
```

###5、表格排序
思路:实现点击年龄和语文分数进行【表格排序】
注意:不点击这两列的话，只遍历不排序->即orderBy会失效，只有前面的遍历生效
```
<body ng-init="phones=[{name:'苹果',age:5,chinese:60},{name:'三星',age:10,chinese:5},{name:'诺基亚',age:0,chinese:99}]">
	<table class="table">
		<tr>
			<th>序号</th>
			<th>姓名</th>
			<th ng-click="flag='age'">年龄</th>
			<th ng-click="flag='chinese'">语文分数</th>
		</tr>
		<!--三行四列-->
		<!--orderBy不是默认一定要生效才能写的-->
		<tr ng-repeat="p in phones | orderBy:flag">
			<td>{{$index+1}}</td>
			<td>{{p.name}}</td>
			<td>{{p.age}}</td>
			<td>{{p.chinese}}</td>
		</tr>
	</table>
</body>
```

###6、表格排序+筛选器
同样是表格排序，在其基础上加上筛选出指定的数据
```
<body>
	<input type="text" ng-model="query">
	<div ng-init="phones=[{name:'apple',age:3,chinese:60},{name:'pear',age:5,chinese:0},{name:'pineapple',age:1,chinese:3}];flag='null'">
		<table class="table">
			<tr>
				<th>序号</th>
				<th>姓名</th>
				<th ng-click="flag='age'">年龄</th>
				<th ng-click="flag='chinese'">分数</th>
			</tr>
			<!--筛选出语文分数值等于query的数据出来==>由于变量query是由input标签同步过来的，所以很炫酷哦-->
			<tr ng-repeat="p in phones | orderBy:flag | filter:{chinese:query}">
				<td>{{$index+1}}</td>
				<td>{{p.name}}</td>
				<td>{{p.age}}</td>
				<td>{{p.chinese}}</td>
			</tr>
		</table>
	</div>
</body>
```

###7、点击div，使其里面的值一直加下去
```
<body>
<!-- 首先写个CSS属性嘛，然后阻止单个元素的闪烁问题，再者给该元素绑定控制器和angular的点击事件 ==> 点击的时候执行method这个只有在控制器 "myCtrl作用域下才有的方法" -->
	<div class="test" ng-controller="myCtrl" ng-click="method()" ng-cloak>
		<!-- 这里{{name}}表示的是angular变量，也是只有在myCtrl这个控制器内才能获取到的$scope.name -->
		{{name}}
	</div>
</body>
<script type="text/javascript" src="./angular.js"></script>
<script type="text/javascript">
	//Note:想要在控制器的作用域内获取name这个angular变量，必须加上{{}}

	var app=angular.module("myAngular",[]);
	
	// 使用控制器 -- 第一个参数是控制器的名字，第二个参数是回调函数 
	// 创建控制器后，会默认带上一个当前作用域做参数$scope；在DOM上，对要使用该控制器的DOM元素添加属性：ng-controller="控制器的名字"
	app.controller('myCtrl',function($scope){
		// 在当前作用域下声明变量name(这个值是私有的，只能在myCtrl这个私有作用域才能获取到)
		$scope.name=0;
		$scope.method=function(){
			$scope.name++;
		}
	})
</script>
```





