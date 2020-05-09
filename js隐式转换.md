### 问题记录

```
es6的proxy

async await 

vue 路由原理 原生js如何实现

vue3 的新特性新增的写法以及vue2对比的区别

node api 的express 框架和koa区别
node 反向代理
webpack 打包分离如何配置 项目和组件分开打包
vue 和react区别
 
```

#### 1.协商缓存与强缓存与PWA缓存



#### 2.js隐式转换

Number、Boolean、String这三种构造函数生成的基础值的对象形式，通过valueOf转换后会变成相应的原始值

Date这种特殊的对象，其原型Date.prototype上内置的valueOf函数将日期转换为日期的毫秒的形式的数值。

除此之外返回的都为this，即对象本身

```js
var a = new Array();
a.valueOf() === a; // true

var b = new Object({});
b.valueOf() === b; // true

[].toString() // ""
({}).toString() //相当于检测类型  "[object Object]"

//构造函数调 toString()方法会先读值,然后再转换 
var num = new Number('123sd');
num.toString(); // 'NaN'

var str = new String('12df');
str.toString(); // '12df'

var bool = new Boolean('fd');
bool.toString(); // 'true'

var arr = new Array(1,2);
arr.toString(); // '1,2'

var d = new Date();
d.toString(); // "Wed Oct 11 2017 08:00:00 GMT+0800 (中国标准时间)"

var func = function () {}
func.toString(); // "function () {}"

[]+![] //“false”
```



```
js的隐式转换

六种数据类型转Number规则：
1、Number转Number，本来多少就是多少；
2、String转Number：数字字符串转成对应数字，空字符串转为0，其他均为NaN；
3、Boolean转Number：true为1，false为0；
4、null为0，undefined为NaN；
5、Object（包括对象、函数、数组、正则等）转Number调用其valueof（）方法，如果为NaN，调用toString（）方法，如果还是NaN，则结果为NaN。

null与undefined在与其他数相等运算时不进行类型转换，
null与undefine单独与别的值比较都为false,但undefined为null的衍生对象，所以两个比较为true

```

1、js数据类型

js中有7种数据类型，可以分为两类：原始类型、对象类型：

基础类型(原始值)：

```js
Undefined、 Null、 String、 Number、 Boolean、 Symbol (es6新出的，本文不讨论这种类型)
```

复杂类型(对象值)：

```js
object
```

2、三种隐式转换类型

js中一个难点就是js隐形转换，因为js在一些操作符下其类型会做一些变化，所以js灵活，同时造成易出错，并且难以理解。

涉及隐式转换最多的两个运算符 + 和 ==。

+运算符即可数字相加，也可以字符串相加。所以转换时很麻烦。== 不同于===，故也存在隐式转换。- * / 这些运算符只会针对number类型，故转换的结果只能是转换成number类型。

既然要隐式转换，那到底怎么转换呢，应该有一套转换规则，才能追踪最终转换成什么了。

隐式转换中主要涉及到三种转换：

1、将值转为原始值，ToPrimitive()。

2、将值转为数字，ToNumber()。

3、将值转为字符串，ToString()。

### 2.1、通过ToPrimitive将值转换为原始值

js引擎内部的抽象操作ToPrimitive有着这样的签名：

ToPrimitive(input, PreferredType?)

input是要转换的值，PreferredType是可选参数，可以是Number或String类型。他只是一个转换标志，转化后的结果并不一定是这个参数所值的类型，但是转换结果一定是一个原始值（或者报错）。

#### 2.1.1、如果PreferredType被标记为Number，则会进行下面的操作流程来转换输入的值。

```
1、如果输入的值已经是一个原始值，则直接返回它
2、否则，如果输入的值是一个对象，则调用该对象的valueOf()方法，
   如果valueOf()方法的返回值是一个原始值，则返回这个原始值。
3、否则，调用这个对象的toString()方法，如果toString()方法返回的是一个原始值，则返回这个原始值。
4、否则，抛出TypeError异常。
```

#### 2.1.2、如果PreferredType被标记为String，则会进行下面的操作流程来转换输入的值。

```
1、如果输入的值已经是一个原始值，则直接返回它
2、否则，调用这个对象的toString()方法，如果toString()方法返回的是一个原始值，则返回这个原始值。
3、否则，如果输入的值是一个对象，则调用该对象的valueOf()方法，
   如果valueOf()方法的返回值是一个原始值，则返回这个原始值。
4、否则，抛出TypeError异常。
复制代码
```

既然PreferredType是可选参数，那么如果没有这个参数时，怎么转换呢？PreferredType的值会按照这样的规则来自动设置：

```
1、该对象为Date类型，则PreferredType被设置为String
2、否则，PreferredType被设置为Number
复制代码
```

#### 2.1.3、valueOf方法和toString方法解析

1、Number、Boolean、String这三种构造函数生成的基础值的对象形式，通过valueOf转换后会变成相应的原始值。如：

```js
var num = new Number('123');
num.valueOf(); // 123

var str = new String('12df');
str.valueOf(); // '12df'

var bool = new Boolean('fd');
bool.valueOf(); // true
```

2、Date这种特殊的对象，其原型Date.prototype上内置的valueOf函数将日期转换为日期的毫秒的形式的数值。

```js
var a = new Date();
a.valueOf(); // 1515143895500
```

3、除此之外返回的都为this，即对象本身：(有问题欢迎告知)

```js
var a = new Array();
a.valueOf() === a; // true

var b = new Object({});
b.valueOf() === b; // true
```

1、Number、Boolean、String、Array、Date、RegExp、Function这几种构造函数生成的对象，通过toString转换后会变成相应的字符串的形式，因为这些构造函数上封装了自己的toString方法。如：

```js
Number.prototype.hasOwnProperty('toString'); // true
Boolean.prototype.hasOwnProperty('toString'); // true
String.prototype.hasOwnProperty('toString'); // true
Array.prototype.hasOwnProperty('toString'); // true
Date.prototype.hasOwnProperty('toString'); // true
RegExp.prototype.hasOwnProperty('toString'); // true
Function.prototype.hasOwnProperty('toString'); // true

var num = new Number('123sd');
num.toString(); // 'NaN'

var str = new String('12df');
str.toString(); // '12df'

var bool = new Boolean('fd');
bool.toString(); // 'true'

var arr = new Array(1,2);
arr.toString(); // '1,2'

var d = new Date();
d.toString(); // "Wed Oct 11 2017 08:00:00 GMT+0800 (中国标准时间)"

var func = function () {}
func.toString(); // "function () {}"
```

除这些对象及其实例化对象之外，其他对象返回的都是该对象的类型，(有问题欢迎告知)，都是继承的Object.prototype.toString方法。

除这些对象及其实例化对象之外，其他对象返回的都是该对象的类型，(有问题欢迎告知)，都是继承的Object.prototype.toString方法。

```js
var obj = new Object({});
obj.toString(); // "[object Object]"

Math.toString(); // "[object Math]"
```

**通过ToNumber将值转换为数字**

根据参数类型进行下面转换：

| 参数      | 结果                                                         |
| --------- | ------------------------------------------------------------ |
| undefined | NaN                                                          |
| null      | +0                                                           |
| 布尔值    | true转换1，false转换为+0                                     |
| 数字      | 无须转换                                                     |
| 字符串    | 有字符串解析为数字，例如：‘324’转换为324，‘qwer’转换为NaN    |
| 对象(obj) | 先进行 ToPrimitive(obj, Number)转换得到原始值，在进行ToNumber转换为数字 |

**通过ToString将值转换为字符串**

根据参数类型进行下面转换：

| 参数      | 结果                                                         |
| --------- | ------------------------------------------------------------ |
| undefined | 'undefined'                                                  |
| null      | 'null'                                                       |
| 布尔值    | 转换为'true' 或 'false'                                      |
| 数字      | 数字转换字符串，比如：1.765转为'1.765'                       |
| 字符串    | 无须转换                                                     |
| 对象(obj) | 先进行 ToPrimitive(obj, String)转换得到原始值，在进行ToString转换为字符串 |

```js
({} + {}) = ?
两个对象的值进行+运算符，肯定要先进行隐式转换为原始类型才能进行计算。
1、进行ToPrimitive转换，由于没有指定PreferredType类型，{}会使默认值为Number，进行ToPrimitive(input, Number)运算。
2、所以会执行valueOf方法，({}).valueOf(),返回的还是{}对象，不是原始值。
3、继续执行toString方法，({}).toString(),返回"[object Object]"，是原始值。
故得到最终的结果，"[object Object]" + "[object Object]" = "[object Object][object Object]"

```

```js
2 * {} = ?
1、首先*运算符只能对number类型进行运算，故第一步就是对{}进行ToNumber类型转换。
2、由于{}是对象类型，故先进行原始类型转换，ToPrimitive(input, Number)运算。
3、所以会执行valueOf方法，({}).valueOf(),返回的还是{}对象，不是原始值。
4、继续执行toString方法，({}).toString(),返回"[object Object]"，是原始值。
5、转换为原始值后再进行ToNumber运算，"[object Object]"就转换为NaN。
故最终的结果为 2 * NaN = NaN

```

```js
[] == !{}
//
1、! 运算符优先级高于==，故先进行！运算。
2、!{}运算结果为false，结果变成 [] == false比较。
3、根据上面第7条，等式右边y = ToNumber(false) = 0。结果变成 [] == 0。
4、按照上面第9条，比较变成ToPrimitive([]) == 0。
    按照上面规则进行原始值转换，[]会先调用valueOf函数，返回this。
   不是原始值，继续调用toString方法，x = [].toString() = ''。
   故结果为 '' == 0比较。
5、根据上面第5条，等式左边x = ToNumber('') = 0。
   所以结果变为： 0 == 0，返回true，比较结束。

```



备注:第一步调用valueOf()方法,返回原始值,那么什么是原始值 ?

Undefined、 Null、 String、 Number、 Boolean、 Symbol (es6新出的，本文不讨论这种类型) 这些是原始值

除了Object 比如 [].valueOf 因为是数组,返回还是本身

但是Object 比如   ({}).valueOf()转化之后 返回还是{} 它不是原始值 所以还要继续转化调用 继续执行toString方法，({}).toString(),返回"[object Object]"，是原始值。



```js
1 + {} ==> '1[object Object]'
{} + 1 ==> 1
二者结果为何不一致？

回复 Rudy24lv-1: 自己回复下，
第一个 1 + {} 不用多解释，看了作者的文章基本上都知道了。
第二个，一直不理解，后面找到了解释 ，{} 这个空对象被解释成了代码块了，而 +1 等于1，所以就有了{} + 1 为 1.

```

