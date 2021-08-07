# ES6

## Symbol
> ES6引入的新的原始数据类型，表示独一无二的值，是JS语言的第七种数据类型，类似于字符串的数据类型
> 
> 特点：值唯一，不能与其他运算符进行运算，定义的对象属性不能使用`for in`遍历
> 
> 使用：给对象添加属性和方法
> 

```js
// 创建Symbol
let s = Symbol();
console.log(s,typeof s); // Symbol() symbol

let s1 = Symbol('1');
let s2 = Symbol('1');
console.log(s1 === s2); // false

// Symbol.for()创建
let s3 = Symbol('1');
let s4 = Symbol('1');
console.log(s3 === s4); // true


// 给对象添加Sambol类型的属性
let youxi = {
  name: '狼人杀',
  [Saymol('say')]: function (){
    console.log(say);
  },
  [Saymol('write')]: function (){
    console.log(write);
  },
};

// Sambol内置属性，整体作为对象的属性去设置，来改变对象在特定场景下面的表现
const arr = [1,2,3];
const arr2 = [4,5,6];
arr2[Symbol.isConcatSpreadable] = false; // Symbol.isConcatSpreadable 等于布尔值，表示对象用于Array.prototype.concat()时是否可以展开
console.log(arr.concat(arr2));  // [1,2,3,[4,5,6]]

```
## 迭代器Iterator
> 是一种接口，为任何数据结构提供统一访问机制，只要有Iterator接口，就可以完成遍历操作
> 
> 主要为遍历命令`for of`服务
> 
> 原生具备Iterator接口的数据：Array/Arguments/Set/Map/String/TypedArray/NodeList，可以通过查看对象的__proto__是否有Symbol(Symbol.iterator)
> 
> 工作原理：
> 
> 1、创建一个指针对象，指向当前数据结构的起始位置
> 
> 2、第一次调用next()方法，指针自动指向数据结构的第一个成员
> 
> 3、接下来不断调用next()方法，指针自动往后移，知道指向最后一个成员
> 
> 4、每调用next()方法返回一个包含value和done属性的对象

```js
const banji = {
  name: '一班',
  stus: ['Dawei', 'Tina', 'Linda'],
  [Symbol(Symbol.iterator)]() {
    let index = 0;
    let _this = this;
    return {
      next: function () {
        if(index < _this.stus.length) {
          index ++;
          return { value: _this.stus[index], done: false }
        }
        return { value: undefined, done: true }
      }
    }
  }
};
for (let v of banji) { // 不写[Symbol(Symbol.iterator)]方法不能遍历
  console.log(v);//  'Dawei', 'Tina', 'Linda'
}

```

## 生成器Generator函数
> Generator 函数是 ES6 提供的一种异步编程解决方案
> 
> function关键字与函数名之间有一个星号 *
> 
> 函数体内部使用yield（产出）表达式，定义不同的内部状态
> 
> 调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象-遍历器对象（Iterator Object）
```js
// 每调用一次next()会执行一个yield语句，并返回yield的值
function * gen (arg) {
  console.log(arg);
  console.log(Generator);
  yield 'a';
  yield 'b';
}
const iterator = gen('1'); // 并不会打印
iterator.next(); // 打印 返回{ value: 'a', done: false }

// 传参 调用next()传入的参数是上一个yield语句的返回值
iterator.next('2'); // 返回{ value: 'b', done: false } yield 'a';返回的是2
iterator.next(); // 返回{ value: undefined, done: true }

// 遍历
for (let v of gen()) {
  console.log(v); // a/b yield 的结果
}

```

## Promise
> ES6 引入的异步编程解决方案，语法上 Promise 是一个构造函数
> 
> then()方法返回 Promise ，所以可以链式时调用，避免回调嵌套

```js
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    let data = '数据库中的数据';
    let err = '操作错误';
    resolve(data);
    reject(err);
  }, 1000)
})

// 第一个参数调用resolve执行，第二个参数调用reject执行
p.then(() => {
  console.log(data); // '数据库中的数据'; 
},(reason) => {
  console.log(reason); // '操作错误';
})

// then()方法中的回调如果返回promise，那么then()返回的promise状态取决于回调中的promise状态
// catch()方法可以得到reject返回的错误
p.catch((reason) => {
  console.log(reason); // '操作错误';
});
```

## Set
> 新的数据结构 Set(集合)，类似于数组，但是成员值都是唯一的
> 
> 实现了iterator接口，可以使用扩展运算符...和for of 遍历

```js
// 声明
const set = new Set();
const set2 = new Set(['1', '2', '3', '4', '1']);

console.log(typeof set); // object
console.log(set2); // 自动去重,没有1
// 元素个数
console.log(set2.size); // 4
// 添加新元素
set2.add('5');
// 删除元素
set2.delete('1');
// 检测
set2.has('5'); // 返回true/false
// 清空集合
set2.clear();

// 应用
const arr = [1,2,3,4,5,3,2,1];
const arr2 = [4,5,6]

// 去重
const uniq = [...new Set(arr)]; // 通过...将Set转换为数组
// 交集（先内部去重在求交集）
const intersect = [...new Set(arr)].filter(it => new Set(arr2).has(it));
// 并集
const union = [...new Set([...arr, ...arr2])];
// 差集（求arr2的差集）
const diff = [...new Set(arr)].filter(it => !(new Set(arr2).has(it))); // [1,2,3]

```

## Map
> 类似于对象，键值对的集合，但是"键"的范围不限于字符串，各种类型（包括对象）的值都可以当做键。
> 
> 实现了 iterator接口，可以使用扩展运算符...和for of遍历

```js
// 声明
const map = new Map();
// 添加元素
map.set('name', 'Tita');
map.set('change', function () {
  console.log('lalala----');
});

let key = { school: '学校' };
map.set(key, ['一中','二中'])
// size
map.size(); // 3
// 删除
map.delete('name');
// 获取
map.get(key);
// 清空
map.clear();

// for of
for (let v of map) {
  console.log(v); // v是数组，第一个值时键值，第二个是值
}


```
## Class类
> 更接近传统语言（java/C）的写法，引入Class的概念，作为对象模板，通过Class可以定义类
> 
> 可以看做是语法糖，大多数功能，ES5都可以实现，Class写法是让原型的写法更清晰，更像面向对象编程的语法
>

```js
// 传统写法
function Phone(brand, price) {
  this.brand = brand;
  this.price = price;
}
Phone.prototype.call = function (){
  console.log('我可以打电话');
}
let iPhone = new Phone('iphone 11 pro', 8999);
iPhone.call();

// Class 写法
class Shouji {
  // 定义构造函数初始化
  constructor (brand, price) {
    this.brand = brand;
    this.price = price;
  }
  call() { // 语法固定，不可写成call: function(){} 形式
    console.log('我可以打电话');
  }
}
let iPhone = new Phone('iphone 11 pro', 8999);
iPhone.call();

// 静态成员 static 定义的属性和方法属于类，不属于实例对象
class Dog {
  static name = '狗';
  static change() {
    console.log('change----');
  };
};

const dog = new Dog();
console.log(dog.name); // undefined
console.log(Dog.name); // 狗

```
extends继承
```js
// ES5写法
function Phone(brand, price) {
  this.brand = brand;
  this.price = price;
}
Phone.prototype.call = function (){
  console.log('我可以打电话');
}
function SmartPhone(brand, price, color, size) {
  Phone.call(this, brand, price); // 改变this指向，属性继承
  this.color = color;
  this.size = size;
};
// 方法继承
SmartPhone.prototype = new Phone(); // 设置后就可在原型链上访问到父级的方法
SmartPhone.prototype.constructor = SmartPhone; // 修正constructor
// 子类的方法
SmartPhone.prototype.photo = function (){
  console.log('我可以拍照');
}

const chuizi = new SmartPhone('锤子', 2499, '黑色', '5.5inch')

// class写法
// 父类
class Phone {
  constructor (brand, price) {
    this.brand = brand;
    this.price = price;
  }
  call() {
    console.log('我可以打电话');
  }
}
// 子类继承父类使用 extends 关键字，super相当于父类的constructor方法
class SmartPhone extends Phone {
  constructor (brand, price, color, size) {
    super(brand, price); // 相当于ES5 Phone.call(this, brand, price); 父类的方法也可直接调用
    this.color = color;
    this.size = size;
  }
  photo() {
    console.log('我可以拍照');
  }
  call() { // 对父类方法的重写，目前子类中无法直接调用父类的方法，不可使用super
    console.log('我可以视频通话');
  }
}
const xiaomi = new SmartPhone('xiaomi', 799, '白色', '5.0inch');
```
get 和 set
```js
class Phone {
  get price() {
    console.log('价格属性被读取');
    return 'price900';
  }
  set price(newV) { // 必须传入一个参数
    console.log('价格属性被修改');
  }
}
const p = new Phone();
console.log(p.price); // price900 并且打印 价格属性被读取
p.price = 1200; // 打印 价格属性被修改

```
## Number扩展






