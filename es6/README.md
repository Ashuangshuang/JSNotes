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
let s3 = Symbol.for('1');
let s4 = Symbol.for('1');
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
参考html文件

## 对象扩展
- Object.is()判断两个值是否完全相等
```js
console.log(Object.is(120, 120)); // true
console.log(Object.is(NaN, NaN)); // true
```
- Object.assign() 对象合并
```js
const obj = { name: 'Linda', age: 13 };
const obj2 = { name: 'Alisha', age: 30, sex: 'woman' };
console.log(obj, obj2); // 相同的后面会把前面覆盖
```
- Object.setPrototypeOf() 设置原型对象
```js
const school = { name: '艾利斯顿' };
const city = {
  xiaoqu: ['北京', '上海'],
}
Object.setPrototypeOf(school, city); // school.__proto__.xiqoqu
Object.getPrototypeOf(school) // 得到{ xiaoqu: ... }
```

## 模块化
> 是将大的程序文件拆分成许多小的程序文件，然后将小文件组合起来
>
模块化好处
- 防止命名冲突 
- 代码复用
- 高维护性

模块化规范产品
> ES6之前JS无模块化规范，JS社区推出了模块化产品
- Common JS => Node.js、Browserify(前端代码打包)
- AMD => requireJS
- CMD => seaJS

ES6模块化语法
> 由 2 个命令组成，`export`和`import`
> 
> `export`用于规定模块的对外接口
> 
> `import`用于引入其他模块提供的功能
```js
// export 语法
// 分别暴露
export let school = '哈佛';
export const chnage = () => {
  console.log('lalala~');
}
// 统一暴露
export {
  school,
  chnage
}

// 默认暴露
export default { name: '123' };

// import 语法
// 通用语法 使用as可修改引入名字
import * as m1 from '...'; // 引入m1模块的内容
// 解构赋值
import { school, chnage } from '...';
// 默认引入 针对默认暴露
import { default as m3 } from '...';
import m3 from '...';

```
# ES7
## Array.prototype.includes()
> 检测数组中是否包含某个元素，返回布尔值
> 
> 原来可以使用indexOf判断，结果返回数值不方便，无返回-1

## `**` 指数操作符
> 实现幂运算，功能与Math.pow()相同
```js
console.log(2 ** 10); // 1024
console.log(Math.pow(2, 10)); // 1024
```

# ES8
## async 和 await
> async 和 await相结合可以让异步代码像同步代码一样，也是异步编程的解决方案

async函数
> 返回promise
> 
> promise结果由async函数返回的结果决定
```js
// 返回结果不是promise类型的对象，返回结果为成功状态的promise对象
// 返回结果是promise类型的对象，状态取决于返回的promise
async function fn () {
  // return '123';
  // throw new Error('出错了~');
  return new Promise((resolve, reject) => {
    resolve('成功的数据');
  });
}
const result = fn();
result.then(value => {
  console.log(value);
}, reason => {
  console.log(reason);

})

```
await 表达式
> await 必须写在async函数中
>
> await 右侧表达式一般为promise对象
> 
> await 返回的是promise成功的值
> 
> await 的promise失败了，会抛出异常，需要通过try...catch捕获处理
```js
const p = new Promise((resolve, reject) => {
  resolve('成功数据');
});

async function main () {
  const result = await p;
  console.log(result); // 成功数据
};

// 捕获错误
async function main () {
  try {
    const result = await p;
    console.log(result); // 成功数据
  } catch (e) {
    console.log(e); // promise 中 reject的数据
  }
};
main();
```
## 对象方法扩展
- Object.values() 获取对象的值，返回数组
- Object.entries() 获取对象的键和值，返回数组
```js
const obj = {
  name: '薛之谦',
  age: 30,
  cities: ['北京', '上海']
};
console.log(Object.entries(obj)); // [['name', '薛之谦'], ['cities', ['北京', '上海']]]

// 可以用于创建Map
const m = new Map(Object.entries(obj));
console.log(m.get('name')); // 薛之谦
```
- Object.getOwnPropertyDescriptors() 获取对象属性的描述对象
```js
console.log(Object.getOwnPropertyDescriptors(obj));
const obj = Object.create(null, {
  name: {
    value: '薛之谦',
    writable: true,
    configurable: true,
    enumerable: true,
  }
})
```
# ES9
## Rest参数
> rest参数和spread扩展运算符在ES6中已经引入，但是只针对数组
> 
> ES9中支持对象...的使用
```js
const { a, b, ...c } = obj; // rest 参数
// 扩展运算符
const obj = {
  ...a,
  ...b
};
```
## 正则扩展
### 命名捕获分组
```js

const str = `<a href='http://baidu.com'>百度</a>`;
const reg = /<a href='(?<url>.*)'>(?<text>.*)<\/a>/;
const result = reg.exec(str);
console.log(result.groups); // { url: http://baidu.com, text: 百度 }

// 之前写法
const reg = /<a href='(.*)'>(.*)<\/a>/;
const result = reg.exec(str);
console.log(result[1]); // http://baidu.com
console.log(result[2]); // 百度 result.groups为undefined

// 身份证号脱敏
console.log('23070619970920022X'.replace(/^(.{6})(?:\d+)(.{4})$/, '\$1****\$2')); // 230706****022X
```
### 反向断言
```js
// 提取555
const str = 'JS123455你知道么555啦啦啦';
// 正向断言
console.log(/\d+(?=啦)/.exec(str)); // 数字的后面是啦
// 反向断言
console.log(/?<=么\d+/.exec(str)); // 数字的前面是么

```
### dotAll模式
> dot . 元字符，除换行符以外的任意单个字符
```js
let s = `
  	<ul>
  		<li>
  			<a>肖申克的救赎</a>
  			<p>上映时间：1994-09-10</p>
			</li>
  		<li>
  			<a>阿甘正传</a>
  			<p>上映时间：1994-07-15</p>
			</li>
		</ul>
  `;
  // 之前写法
  // const r = /<li>\s+<a>(.*?)<\/a>\s+<p>(.*?)<\/p>/;

	// .*? 加的？表示不能贪婪匹配
  // 加上 s 匹配模式.能匹配任意字符，包含换行符
	// g 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）
	const r = /<li>.*?<a>(.*?)<\/a>.*?<p>(.*?)<\/p>/gs;
	let result;
	let data = [];
	while (result = r.exec(s)) { // 循环匹配
	  data.push({ title: result[1], time: result[2] });
	}
  console.log(data);
```
# ES10
## 对象扩展方法
- Object.fromEntries()
> 将二维数组转换为对象，是`Object.entries()`的逆运算
```js
console.log(Object.fromEntries([['name', '陈梦'], ['age', 26]])); // { name: '陈梦', age: 26 }
```
## 字符串扩展方法
- trimStart() 清除字符串左边空格
- trimEnd() 清除字符串右边空格
```js
console.log('   2 3 3  '.trimStart()); // "2 3 3  "
console.log('   2 3 3  '.trimEnd()); // "   2 3 3"

// ES5 trim() 清除前后空格
console.log('   2 3 3  '.trim()); // "2 3 3"
```
## 数组扩展方法
- flat() 将多维数组转化为低维数组
- flatMap() map和flat的结合，先map后flat
```js
console.log([1, [2, 3]].flat()); // [1, 2, 3]
console.log([1, [2, 3, [4]]].flat()); // [1, 2, 3, [4]]
// 参数为深度
console.log([1, [2, 3, [4]]].flat(2)); // [1, 2, 3, 4]
console.log([1, 2, 3].flatMap(it => [it * 10])); // [10, 20, 30]
```
## Symbol扩展
- Symbol.prototype.description 获取描述信息
```js
const s = Symbol('你好');
console.log(s.description); // 你好
```

# ES11
## 私有属性
```js
class Person {
  // 公有属性
  name;
  // 私有属性 变量前面添加 #
  #age;
  #weight;
  constructor(name, age, weight) {
    this.name = name;
    this.#age = age;
    this.#weight = weight;
  }
  intro() {
    console.log(this.name);
    console.log(this.#age);
    console.log(this.#weight);
  }
}
const p = new Person('Lina', 18, 90);
console.log(p.name);
// console.log(p.#age); // Private field '#age' must be declared in an enclosing class
p.intro();
```
## Promise.allSettled()
> 批量处理异步任务
> 
> 参数为promise数组，返回promise
> 
> 返回的promise永远是成功的状态，返回promise的结果是每一个promise的状态和值组成的数组
```js
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功数据1');
  }, 1000)
})
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    // resolve('成功数据2')
    reject('失败的数据2')
  }, 2000)
})

const res = Promise.allSettled([p1, p2]);
res.then((d) => {
  console.log(d); // [{status: "fulfilled", value: "成功数据1"}, {status: "rejected", reason: "失败的数据2"}]

})

// all()方法如果有reject则会返回失败的promise，失败的值则是出错的promise失败的值
// 都成功才会返回成功的promise

// 有一个失败的情况
const res = Promise.all([p1, p2]);
console.log(res); // 失败的promise
res.then((d) => {
  console.log(d); // 不打印
}, (reason) => {
  console.log(reason); // 打印 失败的数据2
})

// 都成功的情况
res.then((d) => {
  console.log(d); // ["成功数据1", "成功数据2"];
})
```
## String.prototype.mathAll()
```js
let s = `
  	<ul>
  		<li>
  			<a>肖申克的救赎</a>
  			<p>上映时间：1994-09-10</p>
			</li>
  		<li>
  			<a>阿甘正传</a>
  			<p>上映时间：1994-07-15</p>
			</li>
		</ul>
  `;
const reg = /<li>.*?<a>(.*?)<\/a>.*?<p>(.*?)<\/p>/gs;
const result = str.matchAll(reg); // 返回的是RegExpStringIterator
// 可以使用for of遍历，也可以使用扩展运算展开
console.log([...result]); // [['li...', '肖申克的救赎', '上映时间：1994-09-10'], [...]]
```
## 可选链操作符
> 用于判断对象层级取值
```js
function main(obj) {
  // const dbHost = obj && obj.db && obj.db.host;
  const dbHost = obj?.db?.host;
  console.log(dbHost); // 如果不传入不会报错 输出undefined
};
main({
  db: {
    host: '192.168.10.123',
  }
});
```
## BigInt数据类型
> 大整形，用于大数值运算

```js
// 声明 结尾加个 n
const n = 123n;
console.log(n, typeof n); // 123n bigint

const max = Number.MAX_SAFE_INTEGER;
console.log(max); // 9007199254740991
console.log(max + 1); // // 9007199254740992
console.log(max + 2); // // 9007199254740992  数值不会再增加了

// BigInt只能和BigInt进行运算，返回BigInt
console.log(BigInt(max) + BigInt(2));// 9007199254740993n
```
## globalThis
> 始终指向全局对象，无论执行环境是什么，浏览器或者nodejs
> 
> 浏览器中`globalThis`就是window
> 
> nodejs中是global对象














