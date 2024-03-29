# JS Advance Notes
## 目录
- [数据类型](#数据类型)
- [原型链](#原型链)
- [原型链的继承](#原型链的继承)
- [变量提升与函数提升](#变量提升与函数提升)
- [执行上下文栈](#执行上下文栈)
- [闭包](#闭包)
- [内存溢出与内存泄漏](#内存溢出与内存泄漏)

## 数据类型
### 分类

- 基本
  - String
  - Number
  - Boolean
  - undefined
  - null
- 引用
  - Object
    - Function
    - Array

### 判断
#### `typeof`判断基本数据类型
>返回数据类型字符串
>
>可以判断`undefined/string/number/boolean/function`
>
> 不能判断`null与object`、`object与array`**注意`typeof null`返回`object`**

### `instanceof`判断引用数据类型的具体数据类型
> `A instanceof  B`判断A是不是B的一个实例，返回`true/false`
> 
> `Array、Function`属于`Object`

```js
const b = {
	b1: ['1', '2', '3'],
	b2: () => {
		console.log('b2');
	}
};

 b instanceof Object; // true
 b.b1 instanceof Array; // true
 b.b2 instanceof Function; // true


function Fn () { };
const fn = new Fn();

fn instanceof Fn; // true
fn instanceof Object; // true

```

### `===`判断`null/undefined`
```
NaN === NaN; // false
```
## 函数的`prototype`属性（显示原型）

- 每个函数都有`prototype`属性，默认指向一个Object空对象（原型对象）
- 原型对象中有一个`constructor`属性，指向函数对象
- 给函数的原型对象添加属性（一般是方法），函数的所有实例对象可直接调用（例如构造函数）

```
  console.log(Date.prototype.constructor === Date); // true
```

## 显示原型与隐式原型

- 显示原型：函数的`prototype`属性，在定义函数时自动添加
- 隐式原型：实例对象的`__proto__`属性，创建实例对象时自动添加，默认为构造函数的`prototype`属性值
- 实例对象隐式原型对应的值为构造函数显示原型的值，两者保存的为相同的地址值
```
Fn(){ // 内部语句：this.prototype = {}
};
const fn = new Fn(); // 内部语句：this.__ptoto__ = Fn.prototype

console.log(Fn.prototype === fn.__proto__); // true

// 内存存储
// 栈
Fn:0x123
fn:0x345

// 堆
Fn(0x123)中prototype:0x234
fn(0x345)中__proto__:0x234
Object对象(0x234)

```
## 原型链（隐式原型链）
- 访问对象的属性时
  - 先在自身对象中查找，找到并返回
  - 如果没有，再通过隐式原型`__proto__`属性继续查找，找到并返回
  - 直到找到原型链的尽头：`Object`的原型对象，`__proto__`属性为`null`，未找到返回`undefined`
  - ❗️️`Object created by Function`，`Object`的`__proto__`指向`Function`的`prototype`
  - ❗所有函数都是`Function`的实例，包含`Function`自己，`Function`的`__proto__`指向`Function`的`prototype`
  - ❗自定义构造函数的`__proto__`指向`Function`的`prototype`
  - ❗️`A instanceof B`️如果B构造函数的显示原型在A实例的原型链上则返回`true`，否则返回false
  
> 函数的显示原型默认指向`Object`的实例对象，但`Object`不满足
> 
> `Object`的原型对象是原型链的尽头
> 
  
```js
console.log(Object.prototype instanceof Object); // false
console.log(Function.prototype === Function.__proto__); // true
console.log(Object.prototype.__proto__); // null

Object instanceof Function; // true
Function instanceof Object; // true
Function instanceof Function; // true
Object instanceof Object; // true

function Fn(){};
Object instanceof Fn; // false


function A (){};
A.prototype.n = 1;
const b = new A(); // b的proto指向A的prototype
A.prototype = { // A.prototype重新指向新的地址
  n: 2,
  m: 3,
};
const c =  new A();
console.log(b.n, b.m, c.n, c.m); // 1,undefined,2,3

function F() {};
Object.prototype.a = function (){ console.log('a()') };
Function.prototype.b = function (){ console.log('b()') };
const f = new F();

f.a();
f.b(); // 报错
F.a();
F.b();

// 原型链__proto__
// f-F.prototype-Object.prototype
// F-Function.prototype-Object.prototype

```
## 原型链的继承
- 将子构造函数的原型设置为父构造函数的实例
```js
function P(n, a){
  this.name = n;
  this.age = a;
};
P.prototype.setName = function (n){
  this.name = n;
};

function S(n, a, p){
  P.call(this, n, a);// 设置属性
  this.price = p;
};
S.prototype.setPrice = function (p){
  this.price = p;
};
S.prototype = new P(); // S的实例可以访问P的方法
S.prototype.constructor = S; // 修正constructor，不写的话S.prototype.constructor = P

const s = new S('gs', 25, 18000);
s.setName('guoshuang');
s.setPrice(18500);

```



## 变量提升与函数提升
- 先变量提升，再函数提升
```js
var c = 1;
function c(c){
  console.log(c);
}
c(); // 报错 c is not a function

if(!(b in window)) { // b in window 返回true
  var b = 1;
}
console.log(b); // undefined
```

## 执行上下文
- 全局执行上下文
  - 在执行全局代码将`window`确定为全局上下文
  - 对全局对象进行预处理
    - `var`定义的全局变量->`undefined`，添加为`window`的属性
    - `function`声明的全局函数添加为`window`的方法
    - `this`赋值为`window`
- 函数执行上下文
  - 在调用函数时创建函数执行的上下文对象
  - 对局部数据进行预处理
    - 形参变量赋值实参，添加为执行的上下文的属性
    - `arguments`赋值实参列表，添加为执行的上下文的属性
    - `var`定义的局部变量->`undefined`，添加为执行的上下文的属性
    - `function`声明的函数添加为执行的上下文的方法
    - `this`指向调用函数的对象
  
## 执行上下文栈
- 在全局代码执行前，JS引擎会创建一个栈来管理所有的执行上下文对象
- 在全局执行上下文`window`确定后，将其添加到栈中
- 在函数执行执行上下文创建后，将其添加到栈中
- 在当前函数执行完后，将栈顶的对象移除
- 当所有代码执行完后，栈中只剩下`window`

> 栈：后进先出
> 
> 队列：先进先出

```js
console.log('gb', i);
var i = 1;
foo(1);
function foo(i) {
  if(i == 4) {
    return
  }
  console.log('fb', i);
  foo(i + 1);
  console.log('fe', i);// f(4)出栈后，会执行f(3)中后面未执行的代码
};
console.log('ge', i);

// 进栈顺序
/**
 window
 f(1)
 f(2)
 f(3)
 **/
f(4)

// 打印顺序
/**
 gb undefined
 fb 1
 fb 2
 fb 3
 fe 3
 fe 2
 fe 1
 ge 1
**/
```
## 闭包
### 闭包的理解
- 产生条件
  - 函数嵌套
  - 当一个嵌套函数的内部函数引用了外部函数的数据
- 理解
  - 是嵌套的内部函数
  - 包含被引用变量的对象，存在于嵌套的内部函数中
  - 调用几次**外部函数**就会产生几个闭包
  
```js
function fun1(){
  var a = 1;
  var b = 'abc'; // 闭包对象中没有b
  function fun2(){ // 执行函数就会产生闭包，无需调用内部函数，但是需要调用外部函数
    console.log(a);
  }
  fun2();
}
fun1();
```
### 常见的闭包
- 将函数作为另一个函数的返回值
- 将函数作为参数传递给另一个函数调用
### 闭包的作用
- 使用函数内部的变量，在内部函数在执行完后，仍然保存再内存中（延长了局部变量的生命周期）
- 可以在函数外部操作函数内部的数据
```js
function fn1(){
  var a = 1;
  function fn2(){
    a++;
    console.log(a);
  }
  return fn2;
}
fn1(); // 执行完后闭包不存在，没有引用指向，内部函数变为垃圾对象被回收
var f = fn1(); // 执行完后闭包还存在，f指向fn2的地址
f();
f();
```
### 闭包的生命周期
- 产生
  - 在内部函数定义执行完成后就产生了闭包，不是调用
- 死亡
  - 在嵌套的内部函数成为垃圾对象时

```js
function fn1(){
  // 此时闭包已经产生了（函数提升，内部函数已经创建了）
  var a = 2;
  function fn2(){
    a++;
    console.log(a);
  }
  return fn2;
}
var f = fn1();
f(); // 3
f(); // 4
f = null; // 闭包死亡，包含闭包的函数对象成为垃圾对象
```
### 闭包的应用
- 定义JS模块
- 将所有数据和功能封装在一个函数内部，向外暴露一个方法或对象
- 模块使用者通过暴露的对象调用方法实现对应功能

## 内存溢出与内存泄漏

- 内存溢出
  - 程序运行需要的内存超过了剩余内存时，抛出内存溢出错误
- 内存泄漏
  - 占用的内存没有及时释放
  - 内存泄漏积累多了会导致内存溢出
  - 常见的内存泄漏
    - 意外的全局变量
    - 没有及时清理定时器
    - 闭包

## 进程`process`与线程`thread`
- 进程：程序的一次执行，占有的一片独有的内存空间，可以通过任务管理器查看
- 线程：是进程内的独立执行单元，是程序执行的完整流程，是CPU最小的调度单元，代码在线程中执行
- 一个程序可以对应多个进程（多进程），一个进程也可以对应多个线程（多线程），一个进程对应一个线程称为单线程
- 应用程序必须运行在某个进程的某个线程上
- 一个程序中至少有一个运行的线程：主线程，进程启动后自动创建
- 一个进程内的数据可以供其他多个线程直接共享
- 多个进程之间的数据不能直接共享
- 线程池：保存多个线程对象的容器，实现线程对象的反复利用
- 多线程（共同执行，先执行第一个，然后第一个暂停，执行第二个，第二个暂停，再执行第一个）优缺点
  - 能有效提升CPU的利用率
  - 创建多线程开销，线程间切换开销
  - 死锁和状态同步问题
- 单线程（从上往下依次执行）优缺点
  - 顺序编程简单易懂
  - 效率低
  
## JS是单线程执行的
- `setTimeout()`的回调函数是在主线程中运行的
- 定时器的回调函数只有在运行栈中的代码全部执行完后才有可能执行
- JS的主要用途是与用户交互、操作DOM，使用单线程比较简单，否则会带来很多同步问题
- JS引擎是在主线程中执行，DOM/setTimeout/ajax是在分线程的模块中，等到回调函数要执行时候会放到回调队列`callback queue`中等待执行

## Web Workers 多线程
> Web Workers是HTML5提供的JS多线程解决方案，可以将大计算量的代码交给分线程运行而不冻结用户操作界面
> 
> 子线程完全受主线程控制，且不能操作DOM，新标准并没有改变JS单线程的本质
> 
> 不是所有浏览器都支持，比都在主线程运行慢，不能跨域加载js，分线程不能操作UI

```js
// 根据位置计算裴波那契数列
// 主线程
const input = document.getElementById('number');
document.getElementById('btn').onclick = function () { 
  const number = input.value;
  const worker = new Worker('worker.js');
  // 向分线程发送消息
  worker.postMessage(number);
  worker.onmessage = function (ev) { 
    console.log('分线程返回的数据---', ev.data);
  };
  console.log(this); // window
}

// worker.js
function fibonacci(n) {
  return n <= 2 ? 1 : fibonacci(n - 2) + fibonacci(n - 1);
};
const onmessage = function (ev) {
  const number = ev.data; // 接受主线程发送的数据
  const result = fibonacci(number);
  postMessage(result); // 向主线程返回数据
};
console.log(this); // 不是window,是WorkerGlobalScope全局对象,所以不能操作DOM

```
## 跨域问题
> 同源策略产生，协议、域名、端口号不同

解决方法

1.JSONP

- 非官方跨域解决方案，只支持`get`请求 
- 工作原理
  - 网页中有一些标签具有跨域能力，例如`img/link/iframe/script`
  - JSONP利用`script`标签的跨域能力发送请求
- 使用
  - 动态创建`script`标签
  - 将`src`属性设置为接口地址
  - 将`script`插入到文档中
  - 声明一个处理函数，参数为接口返回的数据
  - 接口返回的是页面处理函数的调用
  
2.CORS
- 后端通过设置响应头允许跨域


  



