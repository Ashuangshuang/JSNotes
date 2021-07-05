# JS Advance Notes
## 目录
- [数据类型](#数据类型)

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

```
const b = {
	b1: ['1', '2', '3'],
	b2: () => {
		console.log('b2');
	}
};

 b instanceof Object; // true
 b.b1 instanceof Array; // true
 b.b2 instanceof Function; // true

```

### `===`判断`null/undefined`
```
NaN === NaN; // false
```
