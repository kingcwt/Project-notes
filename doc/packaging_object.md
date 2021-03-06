
#### 包装对象

-----------------------------


定义
实例方法
valueOf()
toString()
原始类型与实例对象的自动转换
自定义方法
Boolean 对象
概述
Boolean 函数的类型转换作用

#### 定义
对象是 JavaScript 语言最主要的数据类型，三种原始类型的值——数值、字符串、布尔值——在一定条件下，也会自动转为对象，也就是原始类型的“包装对象”。

所谓“包装对象”，就是分别与数值、字符串、布尔值相对应的Number、String、Boolean三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。
```
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);
```
上面代码中，基于原始类型的值，生成了三个对应的包装对象。
```
typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```
包装对象的最大目的，首先是使得 JavaScript 的对象涵盖所有的值，其次使得原始类型的值可以方便地调用某些方法。

Number、String和Boolean如果不作为构造函数调用（即调用时不加new），常常用于将任意类型的值转为数值、字符串和布尔值。
```
Number(123) // 123
String('abc') // "abc"
Boolean(true) // true
```
上面这种数据类型的转换，详见《数据类型转换》一节。

总结一下，这三个对象作为构造函数使用（带有new）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有new），可以将任意类型的值，转为原始类型的值。

#### 实例方法
包装对象的实例可以使用Object对象提供的原生方法，主要是valueOf方法和toString方法。

##### valueOf()
valueOf方法返回包装对象实例对应的原始类型的值。
```
new Number(123).valueOf()  // 123
new String('abc').valueOf() // "abc"
new Boolean(true).valueOf() // true
```

##### toString()
toString方法返回对应的字符串形式。
```
new Number(123).toString() // "123"
new String('abc').toString() // "abc"
new Boolean(true).toString() // "true"
```
原始类型与实例对象的自动转换
原始类型的值，可以自动当作包装对象调用，即调用各种包装对象的属性和方法。这时，JavaScript 引擎会自动将原始类型的值转为包装对象实例，在使用后立刻销毁实例。

比如，字符串可以调用length属性，返回字符串的长度。
```
'abc'.length // 3
```
上面代码中，abc是一个字符串，本身不是对象，不能调用length属性。JavaScript 引擎自动将其转为包装对象，在这个对象上调用length属性。调用结束后，这个临时对象就会被销毁。这就叫原始类型与实例对象的自动转换。
```
var str = 'abc';
str.length // 3

// 等同于
var strObj = new String(str)
// String {
//   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
// }
strObj.length // 3
```
上面代码中，字符串abc的包装对象提供了多个属性。

自动转换生成的包装对象是只读的，无法修改。所以，字符串无法添加新属性。
```
var s = 'Hello World';
s.x = 123;
s.x // undefined
```
上面代码为字符串s添加了一个x属性，结果无效，总是返回undefined。

另一方面，调用结束后，包装对象实例会自动销毁。这意味着，下一次调用字符串的属性时，实际是调用一个新生成的对象，而不是上一次调用时生成的那个对象，所以取不到赋值在上一个对象的属性。如果要为字符串添加属性，只有在它的原型对象String.prototype上定义（参见《面向对象编程》章节）。

#### 自定义方法
三种包装对象除了提供很多原生的实例方法（详见后文的介绍），还可以在原型上添加自定义方法和属性，供原始类型的值直接调用。

比如，我们可以新增一个double方法，使得字符串和数字翻倍。
```
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double()
// 246
```
上面代码在123外面必须要加上圆括号，否则后面的点运算符（.）会被解释成小数点。

但是，这种自定义方法和属性的机制，只能定义在包装对象的原型上，如果直接对原始类型的变量添加属性，则无效。
```
var s = 'abc';

s.p = 123;
s.p // undefined
```
上面代码直接对字符串abc添加属性，结果无效。主要原因是上面说的，这里的包装对象是自动生成的，赋值后自动销毁，所以最后一行实际上调用的是一个新的包装对象。

#### Boolean 对象
概述
Boolean对象是 JavaScript 的三个包装对象之一。作为构造函数，它主要用于生成布尔值的包装对象实例。
```
var b = new Boolean(true);

typeof b // "object"
b.valueOf() // true
```
上面代码的变量b是一个Boolean对象的实例，它的类型是对象，值为布尔值true。

注意，false对应的包装对象实例，布尔运算结果也是true。
```
if (new Boolean(false)) {
  console.log('true');
} // true

if (new Boolean(false).valueOf()) {
  console.log('true');
} // 无输出
```
上面代码的第一个例子之所以得到true，是因为false对应的包装对象实例是一个对象，进行逻辑运算时，被自动转化成布尔值true（因为所有对象对应的布尔值都是true）。而实例的valueOf方法，则返回实例对应的原始值，本例为false。

Boolean 函数的类型转换作用
Boolean对象除了可以作为构造函数，还可以单独使用，将任意值转为布尔值。这时Boolean就是一个单纯的工具方法。
```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean('') // false
Boolean(NaN) // false

Boolean(1) // true
Boolean('false') // true
Boolean([]) // true
Boolean({}) // true
Boolean(function () {}) // true
Boolean(/foo/) // true
```
上面代码中几种得到true的情况，都值得认真记住。

顺便提一下，使用双重的否运算符（!）也可以将任意值转为对应的布尔值。
```
!!undefined // false
!!null // false
!!0 // false
!!'' // false
!!NaN // false
!!1 // true
!!'false' // true
!![] // true
!!{} // true
!!function(){} // true
!!/foo/ // true
```
最后，对于一些特殊值，Boolean对象前面加不加new，会得到完全相反的结果，必须小心。
```
if (Boolean(false)) {
  console.log('true');
} // 无输出

if (new Boolean(false)) {
  console.log('true');
} // true

if (Boolean(null)) {
  console.log('true');
} // 无输出

if (new Boolean(null)) {
  console.log('true');
} // true
```

##### 本文属于网摘，如有侵权请联系作者删除。

文章原地址：http://javascript.ruanyifeng.com