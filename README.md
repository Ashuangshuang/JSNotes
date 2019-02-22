# JSNotes
JavaScript笔记

## 目录
* [ES5中Array方法](#es5中array方法)
* [call、apply、bind区别](#callapplybind区别)
* [箭头函数与普通function区别](#箭头函数与普通function区别)
* [作用域](#作用域)
* [闭包（Closure）](#闭包（closure）)

## ES5中Array方法
### Array.isArray()

> 返回布尔值，可以弥补typeof运算符的不足

	const arr = [1, 2, 3];
	typeof arr // "object"
	Array.isArray(arr) // true
	
### valueOf()

> 所有对象都拥有的方法，对该对象求值，返回数组本身
	
	const arr = [1, 2, 3];
	arr.valueOf() // [1, 2, 3]

### toString()

> 所有对象都拥有的方法，返回数组的字符串形式
	
	const arr = [1, 2, 3];
	arr.toString() // "1,2,3"
	
	var arr = [1, 2, 3, [4, 5, 6]];
	arr.toString() // "1,2,3,4,5,6"
	
### push()

> 在数组的末端添加一个或多个元素，返回添加新元素后的数组长度
> 
> **该方法会改变原数组**

	var arr = [];
	arr.push(1) // 1
	arr.push('a') // 2
	arr.push(true, {}) // 4
	arr // [1, 'a', true, {}]
	
### pop()

> 删除数组的最后一个元素，并返回该元素
> 
> **该方法会改变原数组**
> 
> 空数组使用pop方法，不会报错，而是返回undefined
> 
> push和pop结合使用，就构成了“后进先出”的栈结构（stack）

	
	var arr = ['a', 'b', 'c'];
	arr.pop() // 'c'
	arr // ['a', 'b']
	[].pop() // undefined

### shift()

> 删除数组的第一个元素，返回该元素
> 
> **该方法会改变原数组**
> 
> 可以遍历并清空一个数组
> 
> push和shift结合使用，就构成了“先进先出”的队列结构（queue）

	const a = ['a', 'b', 'c'];
	a.shift()  // 'a'
	a  // ['b', 'c']
	
	const list = [1, 2, 3, 4, 5, 6];
	const item;
	
	while (item = list.shift()) {
	  console.log(item);
	}
	list // []

### unshift()

> 在数组的第一个位置添加元素，返回添加新元素后的数组长度
> 
> 可以接受多个参数，参数都会添加到目标数组头部 
> 
> **该方法会改变原数组**

	const a = ['a', 'b', 'c'];
	a.unshift('x'); // 4
	a // ['x', 'a', 'b', 'c']
	
	const arr = [ 'c', 'd' ];
	arr.unshift('a', 'b') // 4
	arr // [ 'a', 'b', 'c', 'd' ]
	
### join()
> 指定参数作为分隔符，将数组转换为字符串
> 
> 不提供参数，默认用`,`分隔，与`toString()`类似
>
> 如果数组成员是undefined或null或空位，会被转成空字符串

	const a = [1, 2, 3, 4];
	a.join(' ')  // '1 2 3 4'
	a.join(' | ')  // "1 | 2 | 3 | 4"
	a.join()  // "1,2,3,4"
	
	[undefined, null].join('#')  // '#'
	['a',, 'b'].join('-')  // 'a--b'
	
### concat()

> 多个数组合并，将新数组添加到原数组后面，返回新数组，原数组不变
> 
> 参数类型可以为array，object，string，number，参数可以是多个

	['hello'].concat(['world'])  // ["hello", "world"]
	['hello'].concat(['world'], ['!'])  // ["hello", "world", "!"]
	[].concat({a: 1}, {b: 2})  // [{ a: 1 }, { b: 2 }]
	[2].concat({a: 1})  // [2, {a: 1}]
	[1, 2, 3].concat(4, 5, 6)  // [1, 2, 3, 4, 5, 6]

**==== 敲黑板👏👏👏划重点❗️❗️❗️====**
> 如果数组成员包括对象，concat方法返回当前数组的一个浅拷贝
> 
> 浅拷贝指的是新数组返回的是对象的引用，原对象改变，新数组也会变

	const obj = { a: 1 };
	const oldArray = [obj];
	let newArray = oldArray.concat();
	obj.a = 2;
	newArray[0].a  // 2 而不是1

### reverse()

> 将数组元素倒序排列，返回改变后的数组
> 
> **该方法会改变原数组**
	
	const a = ['a', 'b', 'c'];
	a.reverse() // ["c", "b", "a"]
	a // ["c", "b", "a"]
	
### slice()
	
	arr.slice(start, end);

> 提取数组中某一部分，前包后不包，原数组不变
> 
> 第一个参数为起始位置（从0开始），第二个参数为终止位置
> 
> 参数是负数，从后面计算位置

	const a = ['a', 'b', 'c'];
	a.slice(0)  // ["a", "b", "c"]
	a.slice(1)  // ["b", "c"]
	a.slice(1, 2)  // ["b"]
	a.slice(2, 6)  // ["c"]
	a.slice()  // ["a", "b", "c"]
	
	// -2表示倒数计算的第二个位置
	a.slice(-2) // ["b", "c"]
	a.slice(-2, -1) // ["b"]
	a.slice(4) // []

> slice方法的一个重要应用，是将类似数组的对象转为真正的数组

	Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })  // ['a', 'b']

	Array.prototype.slice.call(document.querySelectorAll("div"));
	Array.prototype.slice.call(arguments);

### splice()
	
	arr.splice(start, count, addElement1, addElement2, ...);
	// 从某个位置开始，删除元素个数，添加元素...

> 删除数组中某一部分成员，并可以在删除的位置添加新的数组成员
> 
> 返回被删除的元素
> 
> **该方法会改变原数组**

	
	const a = ['a', 'b', 'c', 'd', 'e', 'f'];
	a.splice(4, 2)  // ["e", "f"]
	a  // ["a", "b", "c", "d"]
	
	a.splice(4, 2, 1, 2)  // ["e", "f"]
	a  // ["a", "b", "c", "d", 1, 2]
	
	a.splice(-4, 2)  // ["c", "d"]
	
	// 插入元素，count设置为0
	const a = [1, 1, 1];
	a.splice(1, 0, 2)  // []
	a  // [1, 2, 1, 1]
	
	// 只有1个参数，将原数组在指定位置拆分成两个数组
	const a = [1, 2, 3, 4];
	a.splice(2) // [3, 4]
	a // [1, 2]
			
### sort()

> 对数组成员进行排序，默认按照字典顺序排
> 
> **该方法会改变原数组**

	['d', 'c', 'b', 'a'].sort()  // ['a', 'b', 'c', 'd']
	[4, 3, 2, 1].sort()  // [1, 2, 3, 4]
	[11, 101].sort()  // [101, 11]
	[10111, 1101, 111].sort()  // [10111, 1101, 111]
	
	[10111, 1101, 111].sort(function (a, b) {
	  return a - b;
	})
	// [111, 1101, 10111]
	
	[
	  { name: "张三", age: 30 },
	  { name: "李四", age: 24 },
	  { name: "王五", age: 28  }
	].sort(function (o1, o2) {
	  return o1.age - o2.age;
	})
	// [
	//   { name: "李四", age: 24 },
	//   { name: "王五", age: 28  },
	//   { name: "张三", age: 30 }
	// ]

### every
> 数组中每一项都符合判断条件返回true
>
> `[].every(() => {})` 返回true，回调函数不会执行
> 
> 可以接受第二个参数，用来绑定参数函数内部的this变量

	const a = [1, 2, 3, 4, 5, 6]
	a.every(it => it > 0)  // true
   	a.every(it => it > 5 ) // false

### some
> 数组中某一项满足判断条件返回true
>
> `[].some(() => {})` 返回false，回调函数不会执行
> 
> 可以接受第二个参数，用来绑定参数函数内部的this变量

	const a = [1, 2, 3, 4, 5, 6]
	a.some(it => it > 0)  // true
   	a.some(it => it > 5 ) // true

### map

	 arr1.map((it, index, arr1) => {}, arr2)

> 原数组中每个元素执行callback后返回新数组
>
> 可以通过callback改变原数组

   	const a = [1, 2, 3, 4, 5, 6]
	a.map(it => it * 2)  // [2, 4, 6, 8, 10, 12]
	a.map(it => it > 2)  // [false, false, true, true, true, true]

> 第二个参数（arr2），用来绑定回调函数内部的this变量

	const arr = ['a', 'b', 'c'];

	[1, 2].map(function (e) {
	  return this[e];
	}, arr)
	// ['b', 'c']  将回调函数内部的this对象，指向arr数组

> 如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位
> 
> 不会跳过`undefined`和`null`
	
	const f = n => 'a';
	[1, undefined, 2].map(f)  // ["a", "a", "a"]
	[1, null, 2].map(f)  // ["a", "a", "a"]
	[1, , 2].map(f)  // ["a", , "a"]

### forEach
	 与map用法相似
	 arr1.forEach((it, index, arr1) => {}, arr2)

> 将原数组中的每个元素执行一次callback函数，无返回值，for循环的简化版
>
> 可以通过callback函数更改原数组

	const a = [1, 2, 3, 4, 5, 6]
	a.forEach((it, index) => a[index] = it * 2)
	console.log(a)  // [2, 4, 6, 8, 10, 12] 

	const out = [];
	[1, 2, 3].forEach(function(it) {
	  this.push(it * it);
	}, out);
	out // [1, 4, 9]  回调函数内部的this关键字就指向out
	
	// 跳过数组的空位，不会跳过undefined和null
	const log = n => console.log(n + 1);
	  	
	[1, undefined, 2].forEach(log)
	// 2
	// NaN
	// 3
	
	[1, null, 2].forEach(log)
	// 2
	// 1
	// 3
	
	[1, , 2].forEach(log)
	// 2
	// 3

**==== 敲黑板👏👏👏划重点❗️❗️❗️====**

> forEach方法无法中断执行，会将所有成员遍历完
> 
> 如果希望符合某种条件时，就中断遍历，要使用for循环

	const arr = [1, 2, 3];

	for (let i = 0; i < arr.length; i++) {
	  if (arr[i] === 2) break;
	  console.log(arr[i]);
	}
	// 1  forEach无法实现	

### filter
 
	 arr1.map((it, index, arr1) => {}, arr2)

> 将原数组中符合判断条件的返回到新数组
> 
> arr2改变this绑定

	const a = [1, 2, 3, 4, 5, 6]
	a.filter(it => it > 2)   // [3, 4, 5, 6]
	a.filter(it => it > 6)   // []
	
	// 返回数组里所有布尔值为true的成员
	[0, 1, 'a', false].filter(Boolean)  // [1, "a"]
	
	// 改变this绑定
	const obj = { MAX: 3 };
	const myFilter = item => {
	  if (item > this.MAX) return true;
	};
	const arr = [2, 8, 3, 4, 1, 3, 2, 9];
	arr.filter(myFilter, obj)  // [8, 4, 9]
	
### reduce()
	arr.reduce((total, it, index, arr) => {}, initTotal)
> 将数组中的每个元素（从左到右）应用一个函数，将其减少为1个值
>
> 回调函数有4个参数
>
> * total 累加器累加回调的返回值，上次回调的和，必须
> * it 当前元素，必须
> * index 索引
> * arr 原数组
> 
> reduce第二个参数
> 
> * 累计变量的初始值，it会从数组第一个开始，处理空数组时有用

 	const a = [1, 2, 3, 4, 5, 6]
	a.reduce((total, it) => total + it)  // 21
	
	[1, 2, 3, 4, 5].reduce((a, b) => a + b, 10);  // 25

	function add(prev, cur) {
	  return prev + cur;
	}
	
	[].reduce(add)
	// TypeError: Reduce of empty array with no initial value
	[].reduce(add, 1)
	// 1
	
> 如果判断条件只操作it，不操作total，将会只处理数组最后一项

	const a = [1, 2, 3, 4, 5, 6]
	a.reduce((total, it) => it * 2)  // 12

### reduceRight()

> 将数组中的每个元素（从右到左）应用一个函数，将其减少为1个值，其余和reduce用法一样

	const fun = (prev, cur) => prev - cur;
	
	[3, 2, 1].reduce(fun)  // 0  相当于3-2-1
	[3, 2, 1].reduceRight(fun)  // -4  相当于 1-2-3

### indexOf()	
> 1个参数时，找到返回索引值，未找到返回`-1`
> 
> 2个参数时，从索引位置开始查找，未找到返回`-1`

	const a = [1, 2, 3, 4, 5, 1]
	a.indexOf(1)  // 0
	a.indexOf(5)  // 4
	
### lastIndexOf()	

> 返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1
	
	const a = [2, 5, 9, 2];
	a.lastIndexOf(2)  // 3
	a.lastIndexOf(7)  // -1
**==== 敲黑板👏👏👏划重点❗️❗️❗️====**
> `indexOf()``lastIndexOf()`不能用来搜索NaN的位置，它们无法确定数组成员是否包含NaN
> 
> 方法内部，使用严格相等运算符（===）进行比较，NaN是唯一一个不等于自身的值

	[NaN].indexOf(NaN) // -1
	[NaN].lastIndexOf(NaN) // -1
	
## call、apply、bind区别
#### 相同点
> 改变函数this对象的指向
>
> 第一个参数都是this要指向的对象
>
> 都可以利用后续参数传参

#### 区别
> 返回值
>
> * call和apply都是对函数的直接调用
> * bind返回函数，后面还需要加()来进行调用
>
> 参数
>
> * call的后续参数与调用的一一对应，call(a,b,c)
> * apply的后续参数为数组，数组中的值与调用的一一对应，apply(a,[b,c])
> * bind需要加（）才调用，bind(a)(b,c)

#### 无参数调用

	const xw = {
    name: "小王",
    gender: "男",
    age: 24,
    say: function(){
        alert(this.name+" , "+this.gender+" ,今年"+this.age);
    	}
	}
	const xg = {
	    name: "小郭",
	    gender: "女",
	    age: 18
	}
	xw.say();  // 小王,男,今年24

	// 用xw的say()显示xg的数据
	xw.say.call(xg);
	xw.say.apply(xg);
	xw.say.bind(xg)();

#### 传参数调用
	const xw = {
    name: "小王",
    gender: "男",
    age: 24,
    say: function(school, grade){
        alert(this.name+" , "+this.gender+" ,今年"+this.age + this. school + this. grade);
    	}
	}

	const xg = {
	    name: "小郭",
	    gender: "女",
	    age: 18
	}

	// 用xw的say()显示xg的数据
	xw.say.call(xg, '美国斯坦福', '研一');
	xw.say.apply(xg, ['美国斯坦福', '研一']);
	xw.say.bind(xg,"实验小学","六年级")();
	xw.say.bind(xg)('美国斯坦福', '研一');

## 箭头函数与普通function区别
#### 1、写法简洁
> 方法只有一个参数可省略小括号()
>
> 方法体只有一行可省略大括号{}

	const a = [1, 2, 3]
	const b = arr.map(function (it) {
	  return it;
	});

	const b = arr.map(it => it);  // 相当于 (it) => { return it; }

#### 2、❗️不绑定this
> 在箭头函数出现之前，每个新定义的函数都有其自己的 this 值（例如，构造函数的 this 指向了一个新的对象；
>
> <font color="red">严格模式</font>下的函数的 this 值为 undefined，如果函数是作为对象的方法被调用的，则其 this 指向了那个调用它的对象）
>
> **箭头函数会捕获所在上下文的 this 值，作为自己的 this 值**
>
> **使用 call、apply 调用函数时，只是传入参数，对 this 无影响**

	const adder = {
	  base : 1,

	  add : function(a) {
	    const f = v => v + this.base;
	    return f(a);
	  },

	  addCall: function(a) {
	    const f = v => v + this.base;
	    const b = {
	      base : 2
	    };

	    return f.call(b, a);
	  }
	};

	console.log(adder.add(1)); // 输出 2
	console.log(adder.addCall(1)); // 仍然输出 2（而不是3，call传入的this无效）

#### 3、不绑定<font color="red">arguments对象</font>

> 箭头函数不会在其内部暴露出 arguments 对象，
>
> arguments.length, arguments[0], arguments[1] 等等都不会指向箭头函数的 arguments ，而是指向了箭头函数所在作用域的一个名为 arguments 的值
>
> 箭头函数所在作用域如果没有名为 arguments 的值，返回undefined

	function fun() {
	  return arguments[1];
	}

	fun(2) // undefined
	fun(2, 3)  // 3

	const arguments = 1;
	const fun = () => arguments;
	fun(3)  // 1 (指向作用域中的 arguments)

	function foo() {
	  const f = () => arguments[0];
	  return f(2);
	}
	foo(1); // 1 (指向作用域中的 arguments)

#### 4、不会变量提升

	console.log(fun1());  // 1
	function fun1() {
		return 1;
	}

	console.log(fun2());  // 报错 fun2 is not defined
	const fun2 = () => 2


## 作用域
### 全局作用域
> 全局变量： 会挂载到 window 对象上
> 
> 生命周期将存在于整个程序之内。
> 
> 能被程序中任何函数或者方法访问。
> 
> 在 JavaScript 内默认是可以被修改的。
> 
> 函数如果不经过封装，也会是全局变量，他的生命周期也就是全局作用域

#### 1、显示声明
> 带有关键字 var 的声明

	var testValue = 123;
   	var testFunc = function () { console.log('just test') };

    console.log(window.testFunc)		// ƒ () { console.log('just test') }
    console.log(window.testValue)		// 123
    
#### 2、隐式声明
> 不带有声明关键字的变量，JS 会默认帮你声明一个全局变量！！！

	function foo(value) {
      result = value + 1;	 // 没有用 var 修饰
      return result;
    };

    foo(123);				// 124
    console.log(window.result);  // 124 <=  挂在了 window全局对象上 
### 函数作用域
> 函数作用域内，对外是封闭的，从外层的作用域无法直接访问函数内部的作用域

	function bar() {
	  var testValue = 'inner';
	}
	console.log(testValue);		// 报错：ReferenceError: testValue is not 

#### 1、通过 return 访问函数内部变量
	
	function bar(value) {
	  var testValue = 'inner';
	  return testValue + value;
	}
	
	console.log(bar('fun'));	// "innerfun"
	
#### 2、通过 闭包 访问函数内部变量
	
	function bar(value) {
	  var testValue = 'inner';
	  
	  var rusult = testValue + value;
	  
	  function innser() {
	     return rusult;
	  };
	  
	  return innser();
	}
	
	console.log(bar('fun'));		// "innerfun"
	
#### 3、自执行函数（创造一个函数作用域）
	
	(function() {
      var testValue = 123;
      var testFunc = function () { console.log('just test'); };

    })();

    console.log(window.testValue);		// undefined
    console.log(window.testFunc);		// undefined

### 块级作用域
> 用const let关键字声明，创建块级作用域的条件是必须有一个 { } 包裹

	for(var i = 0; i < 5; i++) {
	  // ...
	}
	
	console.log(i)	// 5   
	
	// 用 var 关键字声明的变量，在 for 循环之后仍然被保存这个作用域里
	for() { }仍然在，全局作用域里，并没有产生像函数作用域一样的封闭效果 
	
	如果想要实现 块级作用域 用 let 关键字声明
	for(let i = 0; i < 5; i++) {
	  // ...
	}
	
	console.log(i)	// 报错：ReferenceError: i is not defined

### 词法作用域
> 在你写代码时将变量和块作用域写在哪里来决定，也就是词法作用域是静态的作用域，在你书写代码时就确定了
> 
> eval()和with可以通过其特殊性用来“欺骗”词法作用域，不过正常情况下都不建议使用，会产生性能问题。

### ===举栗子🌰====
#### 1、声明提升
	
	function testOrder(arg) {
	    console.log(arg); // arg是形参，不会被重新定义
	    console.log(a); // 因为函数声明比变量声明优先级高，所以这里a是函数
	    var arg = 'hello'; // var arg;变量声明被忽略， arg = 'hello'被执行
	    var a = 10; // var a;被忽视; a = 10被执行，a变成number
	    function a() {
	        console.log('fun');
	    } // 被提升到作用域顶部
	    console.log(a); // 输出10
	    console.log(arg); // 输出hello
	}; 
	
	testOrder('hi');
	
	/* 输出：
	hi 
	function a() { console.log('fun');}
	10 
	hello 
	*/	
	
	// 下面两种写法等同
	function test(arg){
        // 1. 形参 arg 是 "hi"
        // 2. function arg声明提升，arg 是 function
		console.log(arg);  // [function : arg]
		var arg = 'hello';
	        // 3. arg 此时是 "hello"
		function arg(){
			console.log('hello world') 
		}
		console.log(arg);    // hello
	}
	test('hi');
	
	// 更容易理解
	function test(arg){
	  var arg;
	  arg = function(){
	    console.log('hello world');
	  }
	  console.log(arg);
	  arg = 'hello';
	  console.log(arg);
	}
	test('hi');
		
#### 2、块级作用域
	
要求对代码进行修改，使其输出'0 1 2 3 4'

	for (var i = 0; i < 5; i++) {
	    setTimeout(function() {
	        console.log(i);
	    }, 1000);
	}
	// 5 5 5 5 5
	
	这里的 i 是在全局作用域里面的，只存在 1 个值，等到回调函数执行时，用词法作用域捕获的 i 就	只能是 5；

1、	let 创建块级作用域

	for(let i = 0; i < 5; i++) {
	    setTimeout(function() {
	      console.log(i);
	    }, 200);
	};
2、 采用自执行函数，创建函数作用域
	
	// 以下2种都可以
	for (var i = 0; i < 5; i++) {
	    setTimeout(function() {
	        console.log(i);
	    }(i), 1000);
	}
	
	for(var i = 0; i < 5; i++) {
	  (function(j) {
	    setTimeout(function() {
	      console.log(j);
	    }, 200);
	  })(i);
	};

3、	调用函数，创建函数作用域
> 相当于创建了5个函数作用域来保存 i 值

	for(var i = 0; i < 5; i++) {
	  abc(i);
	};
	
	function abc(i) {
	  setTimeout(function() {
	     console.log(i);			// 0 1 2 3 4 
	  }, 200); 
	}

## 闭包（Closure）

> 闭包就是指有权访问另一个函数作用域中的变量的函数

### 产生闭包
> 创建闭包最常见方式，就是在一个函数内部创建另一个函数。下面例子中的 closure 就是一个闭包
> 
> 闭包的作用域链包含着它自己的作用域，以及包含它的函数的作用域和全局作用域。

	function func(){
		var a = 1,b = 2;
		function closure(){
			return a+b;
	  	}
		return closure;
	}

### 闭包注意事项
> 1、通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。但是，在创建了一个闭包以后，这个函数的作用域就会一直保存到闭包不存在为止。
> 
> 2、闭包只能取得包含函数中任何变量的最后一个值，这是因为闭包所保存的是整个变量对象，而不是某个特殊的变量
> 

3、闭包中的 this 对象
	
	var name = "The Window";

	var obj = {
	  name: "My Object",
	  
	  getName: function(){
	    return function(){
	      return this.name;
	    };
	  }
	};
	
	console.log(obj.getName()());  // The Window
	
	obj.getName()()实际上是在全局作用域中调用了匿名函数，this指向了window。匿名函数的执行环境具有全局性，因此其 this 对象通常指向 window。

### 闭包的应用

1、设计私有的方法和变量、定义模块

> 任何在函数中定义的变量，都可以认为是私有变量，因为不能在函数外部访问这些变量。私有变量包括函数的参数、局部变量和函数内定义的其他函数
> 
> 把有权访问私有变量的公有方法称为特权方法（privileged method）

	function Animal(){
	  // 私有变量
	  var series = "哺乳动物";
	  function run(){
	    console.log("Run!!!");
	  }
	  
	  // 特权方法
	  this.getSeries = function(){
	    return series;
	  };
	}

2、匿名函数最大的用途是创建闭包，并且还可以构建命名空间，以减少全局变量的使用。从而使用闭包模块化代码，长久保存变量，减少全局变量的污染

	var objEvent = objEvent || {};
	(function(){ 
	var addEvent = function(){ 
	// some code
	};
	function removeEvent(){
	// some code
	}
		
	objEvent.addEvent = addEvent;
	objEvent.removeEvent = removeEvent;
	})();
	
	addEvent 和 removeEvent 都是局部变量，但我们可以通过全局变量 objEvent 使用它，这就大大减少了全局变量的使用，增强了网页的安全性。

3、闭包计数器

	var countNumber = (function(){
	  var num = 0;
	  return function(){
	    return ++num;
	  };
	})();	

### 闭包的缺陷
* 常驻内存会增大内存使用量，并且使用不当很容易造成内存泄露。
* 如果不是因为某些特殊任务而需要闭包，在没有必要的情况下，在其它函数中创建函数是不明智的，因为闭包对脚本性能具有负面影响，包括处理速度和内存消耗

	




	
	
	


	
	


    


    

	








