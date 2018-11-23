# JSNotes
JavaScript笔记

## 目录
* [js遍历方法](#js遍历方法)
* [call、apply、bind区别](#callapplybind区别)
* [箭头函数与普通function区别](#箭头函数与普通function区别)

## js遍历方法
### every

>数组中每一项都符合判断条件返回true
>
>`[].every(() => {})` 返回true

	const a = [1, 2, 3, 4, 5, 6]
	a.every(it => it > 0)  // true
   	a.every(it => it > 5 ) // false
### some
> 数组中某一项满足判断条件返回true
>
> `[].some(() => {})` 返回false

	const a = [1, 2, 3, 4, 5, 6]
	a.some(it => it > 0)  // true
   	a.some(it => it > 5 ) // true
### map
> 原数组中每个元素执行callback后返回新数组
>
> 可以通过callback改变原数组

   	const a = [1, 2, 3, 4, 5, 6]
	a.map(it => it * 2)  // [2, 4, 6, 8, 10, 12]
	a.map(it => it > 2)  // [false, false, true, true, true, true]

### filter
> 将原数组中符合判断条件的返回到新数组

	const a = [1, 2, 3, 4, 5, 6]
	a.filter(it => it > 2)   // [3, 4, 5, 6]
	a.filter(it => it > 6)   // []
### reduce
> 	对累加器和数组中的每个元素（从左到右）应用一个函数，将其减少为1个值
>
> 回调函数有两个必须的参数
>
> * total 累加器累加回调的返回值，上次回调的和
> * it 当前元素

 	const a = [1, 2, 3, 4, 5, 6]
	a.reduce((total, it) => total + it)  // 21

> 如果判断条件只操作it，不操作total，将会只处理数组最后一项

	const a = [1, 2, 3, 4, 5, 6]
	a.reduce((total, it) => it * 2)  // 12

### forEach
> 将原数组中的每个元素执行一次callback函数，无返回值，for循环的简化版
>
> 可以通过callback函数更改原数组

	const a = [1, 2, 3, 4, 5, 6]
	a.forEach((it, index) => a[index] = it * 2)
	console.log(a)  // [2, 4, 6, 8, 10, 12]

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












