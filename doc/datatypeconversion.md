
####  数据类型转换

--------------------------------------

目录
强制转换
Number()
String()
Boolean()
自动转换
自动转换为布尔值
自动转换为字符串
自动转换为数值


JavaScript 是一种动态类型语言，变量没有类型限制，可以随时赋予任意值。
```
var x = y ? 1 : 'a';
```
上面代码中，变量x到底是数值还是字符串，取决于另一个变量y的值。y为true时，x是一个数值；y为false时，x是一个字符串。这意味着，x的类型没法在编译阶段就知道，必须等到运行时才能知道。

虽然变量的数据类型是不确定的，但是各种运算符对数据类型是有要求的。如果运算符发现，运算子的类型与预期不符，就会自动转换类型。比如，减法运算符预期左右两侧的运算子应该是数值，如果不是，就会自动将它们转为数值。
```
'4' - '3' // 1
```
上面代码中，虽然是两个字符串相减，但是依然会得到结果数值1，原因就在于 JavaScript 将运算子自动转为了数值。

本章讲解数据类型自动转换的规则。在此之前，先讲解如何手动强制转换数据类型。

#### 强制转换
强制转换主要指使用Number、String和Boolean三个函数，手动将各种类型的值，分布转换成数字、字符串或者布尔值。

##### Number()
使用Number函数，可以将任意类型的值转化成数值。

下面分成两种情况讨论，一种是参数是原始类型的值，另一种是参数是对象。

（1）原始类型值

原始类型值的转换规则如下。
```
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
```
Number函数将字符串转为数值，要比parseInt函数严格很多。基本上，只要有一个字符无法转成数值，整个字符串就会被转为NaN。
```
parseInt('42 cats') // 42
Number('42 cats') // NaN
```
上面代码中，parseInt逐个解析字符，而Number函数整体转换字符串的类型。

另外，parseInt和Number函数都会自动过滤一个字符串前导和后缀的空格。
```
parseInt('\t\v\r12.34\n') // 12
Number('\t\v\r12.34\n') // 12.34
```
（2）对象

简单的规则是，Number方法的参数是对象时，将返回NaN，除非是包含单个数值的数组。
```
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
```
之所以会这样，是因为Number背后的转换规则比较复杂。

第一步，调用对象自身的valueOf方法。如果返回原始类型的值，则直接对该值使用Number函数，不再进行后续步骤。

第二步，如果valueOf方法返回的还是对象，则改为调用对象自身的toString方法。如果toString方法返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。

第三步，如果toString方法返回的是对象，就报错。


请看下面的例子。
```
var obj = {x: 1};
Number(obj) // NaN

// 等同于
if (typeof obj.valueOf() === 'object') {
  Number(obj.toString());
} else {
  Number(obj.valueOf());
}
```
上面代码中，Number函数将obj对象转为数值。背后发生了一连串的操作，首先调用obj.valueOf方法, 结果返回对象本身；于是，继续调用obj.toString方法，这时返回字符串[object Object]，对这个字符串使用Number函数，得到NaN。

默认情况下，对象的valueOf方法返回对象本身，所以一般总是会调用toString方法，而toString方法返回对象的类型字符串（比如[object Object]）。所以，会有下面的结果。
```
Number({}) // NaN
```
如果toString方法返回的不是原始类型的值，结果就会报错。
```
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

Number(obj)
// TypeError: Cannot convert object to primitive value
```
上面代码的valueOf和toString方法，返回的都是对象，所以转成数值时会报错。

从上例还可以看到，valueOf和toString方法，都是可以自定义的。
```
Number({
  valueOf: function () {
    return 2;
  }
})
// 2

Number({
  toString: function () {
    return 3;
  }
})
// 3

Number({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// 2
```
上面代码对三个对象使用Number函数。第一个对象返回valueOf方法的值，第二个对象返回toString方法的值，第三个对象表示valueOf方法先于toString方法执行。

##### String()
String函数可以将任意类型的值转化成字符串，转换规则如下。

（1）原始类型值
```
数值：转为相应的字符串。
字符串：转换后还是原来的值。
布尔值：true转为字符串"true"，false转为字符串"false"。
undefined：转为字符串"undefined"。
null：转为字符串"null"。
String(123) // "123"
String('abc') // "abc"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```
（2）对象

String方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。
```
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```
String方法背后的转换规则，与Number方法基本相同，只是互换了valueOf方法和toString方法的执行顺序。

先调用对象自身的toString方法。如果返回原始类型的值，则对该值使用String函数，不再进行以下步骤。

如果toString方法返回的是对象，再调用原对象的valueOf方法。如果valueOf方法返回原始类型的值，则对该值使用String函数，不再进行以下步骤。

如果valueOf方法返回的是对象，就报错。

下面是一个例子。
```
String({a: 1})
// "[object Object]"

// 等同于
String({a: 1}.toString())
// "[object Object]"
```
上面代码先调用对象的toString方法，发现返回的是字符串[object Object]，就不再调用valueOf方法了。

如果toString法和valueOf方法，返回的都是对象，就会报错。
```
var obj = {
  valueOf: function () {
    return {};
  },
  toString: function () {
    return {};
  }
};

String(obj)
// TypeError: Cannot convert object to primitive value
```
下面是通过自定义toString方法，改变返回值的例子。
```
String({
  toString: function () {
    return 3;
  }
})
// "3"

String({
  valueOf: function () {
    return 2;
  }
})
// "[object Object]"

String({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  }
})
// "3"
```
上面代码对三个对象使用String函数。第一个对象返回toString方法的值（数值3），第二个对象返回的还是toString方法的值（[object Object]），第三个对象表示toString方法先于valueOf方法执行。

##### Boolean()
Boolean函数可以将任意类型的值转为布尔值。

它的转换规则相对简单：除了以下五个值的转换结果为false，其他的值全部为true。
```
undefined
null
-0或+0
NaN
''（空字符串）
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```
注意，所有对象（包括空对象）的转换结果都是true，甚至连false对应的布尔对象new Boolean(false)也是true（详见《原始类型值的包装对象》一章）。
```
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```
所有对象的布尔值都是true，这是因为 JavaScript 语言设计的时候，出于性能的考虑，如果对象需要计算才能得到布尔值，对于obj1 && obj2这样的场景，可能会需要较多的计算。为了保证性能，就统一规定，对象的布尔值为true。

#### 自动转换
下面介绍自动转换，它是以强制转换为基础的。

遇到以下三种情况时，JavaScript 会自动转换数据类型，即转换是自动完成的，用户不可见。

第一种情况，不同类型的数据互相运算。
```
123 + 'abc' // "123abc"
```
第二种情况，对非布尔值类型的数据求布尔值。
```
if ('abc') {
  console.log('hello')
}  // "hello"
```
第三种情况，对非数值类型的值使用一元运算符（即+和-）。
```
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```
自动转换的规则是这样的：预期什么类型的值，就调用该类型的转换函数。比如，某个位置预期为字符串，就调用String函数进行转换。如果该位置即可以是字符串，也可能是数值，那么默认转为数值。

由于自动转换具有不确定性，而且不易除错，建议在预期为布尔值、数值、字符串的地方，全部使用Boolean、Number和String函数进行显式转换。

##### 自动转换为布尔值
JavaScript 遇到预期为布尔值的地方（比如if语句的条件部分），就会将非布尔值的参数自动转换为布尔值。系统内部会自动调用Boolean函数。

因此除了以下五个值，其他都是自动转为true。
```
undefined
null
+0或-0
NaN
''（空字符串）
```
下面这个例子中，条件部分的每个值都相当于false，使用否定运算符后，就变成了true。


```
if ( !undefined
  && !null
  && !0
  && !NaN
  && !''
) {
  console.log('true');
} // true
```
下面两种写法，有时也用于将一个表达式转为布尔值。它们内部调用的也是Boolean函数。
```
// 写法一
expression ? true : false

// 写法二
!! expression
```
自动转换为字符串
JavaScript 遇到预期为字符串的地方，就会将非字符串的值自动转为字符串。具体规则是，先将复合类型的值转为原始类型的值，再将原始类型的值转为字符串。

字符串的自动转换，主要发生在字符串的加法运算时。当一个值为字符串，另一个值为非字符串，则后者转为字符串。
```
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"
```
这种自动转换很容易出错。
```
var obj = {
  width: '100'
};

obj.width + 20 // "10020"
```
上面代码中，开发者可能期望返回120，但是由于自动转换，实际上返回了一个字符10020。

##### 自动转换为数值
JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。系统内部会自动调用Number函数。

除了加法运算符（+）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。
```
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN
```
上面代码中，运算符两侧的运算子，都被转成了数值。

注意：null转为数值时为0，而undefined转为数值时为NaN。

一元运算符也会把运算子转成数值。
```
+'abc' // NaN
-'abc' // NaN
+true // 1
-false // 0
```

##### 本文属于网摘，如有侵权请联系作者删除。

文章原地址：http://javascript.ruanyifeng.com
