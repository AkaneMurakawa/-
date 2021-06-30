# 2 JS基础

## 2 JS基础

#### 1 开始

```text
var i = 10;
console.log(i++); //10 ，先引用原值，然后加1

等价于：

 var i= 10;
 console.log(i);   //先输出i
 i++;              //然后f加i

区别于：
 var i = 10;
console.log(++i);  //11 ， 这次是先加1，然后输出

只要你记住一句口诀：++在前下自加后运算；++在后先运算后自加；
```

通常来说，有前导0的数值会被视为八进制，但是如果前导0后面有数字8和9，则该数值被视为十进制。

* +0或-0当作分母，返回的值是不相等的。

```text
(1 / +0) === (1 / -0) // false
//上面的代码之所以出现这样结果，是因为除以正零得到+Infinity，除以负零得到-Infinity，这两者是不相等的（关于Infinity详见下文）。
```

#### NaN

（1）含义

NaN是 JavaScript 的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合。

5 - 'x' // NaN 上面代码运行时，会自动将字符串x转为数值，但是由于x不是数值，所以最后得到结果为NaN，表示它是“非数字”（NaN）。

另外，一些数学函数的运算结果会出现NaN。

另外，一些数学函数的运算结果会出现NaN。

```text
Math.acos(2) // NaN
Math.log(-1) // NaN
Math.sqrt(-1) // NaN
//0除以0也会得到NaN。

0 / 0 // NaN
```

需要注意的是，NaN不是独立的数据类型，而是一个特殊数值，它的数据类型依然属于Number，使用typeof运算符可以看得很清楚。

typeof NaN // 'number'

（2）运算规则

NaN不等于任何值，包括它本身。

NaN === NaN // false 数组的indexOf方法内部使用的是严格相等运算符，所以该方法对NaN不成立。

\[NaN\].indexOf\(NaN\) // -1 NaN在布尔运算时被当作false。

Boolean\(NaN\) // false NaN与任何数（包括它自己）的运算，得到的都是NaN。

```text
NaN + 32 // NaN
NaN - 32 // NaN
NaN * 32 // NaN
NaN / 32 // NaN
```

0乘以Infinity，返回NaN；0除以Infinity，返回0；Infinity除以0，返回Infinity。

```text
0 * Infinity // NaN
0 / Infinity // 0
Infinity / 0 // Infinity
Infinity加上或乘以Infinity，返回的还是Infinity。

Infinity + Infinity // Infinity
Infinity * Infinity // Infinity
Infinity减去或除以Infinity，得到NaN。

Infinity - Infinity // NaN
Infinity / Infinity // NaN
Infinity与null计算时，null会转成0，等同于与0的计算。

null * Infinity // NaN
null / Infinity // 0
Infinity / null // Infinity
Infinity与undefined计算，返回的都是NaN。

undefined + Infinity // NaN
undefined - Infinity // NaN
undefined * Infinity // NaN
undefined / Infinity // NaN
Infinity / undefined // NaN
```

#### 与数值相关的全局方法

1）基本用法

parseInt方法用于将字符串转为整数。

```text
parseInt('123') // 123
如果字符串头部有空格，空格会被自动去除。

parseInt('   81') // 81
如果parseInt的参数不是字符串，则会先转为字符串再转换。

parseInt(1.23) // 1
// 等同于
parseInt('1.23') // 1
字符串转为整数的时候，是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分。

parseInt('8a') // 8
parseInt('12**') // 12
parseInt('12.34') // 12
parseInt('15e2') // 15
parseInt('15px') // 15
上面代码中，parseInt的参数都是字符串，结果只返回字符串头部可以转为数字的部分。

如果字符串的第一个字符不能转化为数字（后面跟着数字的正负号除外），返回NaN。

parseInt('abc') // NaN
parseInt('.3') // NaN
parseInt('') // NaN
parseInt('+') // NaN
parseInt('+1') // 1
```

所以，parseInt的返回值只有两种可能，要么是一个十进制整数，要么是NaN。

#### parseFloat\(\)

parseFloat方法用于将一个字符串转为浮点数。 如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回NaN。

```text
parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN
//上面代码中，尤其值得注意，parseFloat会将空字符串转为NaN。

//这些特点使得parseFloat的转换结果不同于Number函数。

parseFloat(true)  // NaN
Number(true) // 1

parseFloat(null) // NaN
Number(null) // 0

parseFloat('') // NaN
Number('') // 0

parseFloat('123.45#') // 123.45
Number('123.45#') // NaN
```

#### isNaN\(\)

isNaN方法可以用来判断一个值是否为NaN。

isNaN\(NaN\) // true isNaN\(123\) // false 但是，isNaN只对数值有效，如果传入其他值，会被先转成数值。比如，传入字符串的时候，字符串会被先转成NaN，所以最后返回true，这一点要特别引起注意。也就是说，isNaN为true的值，有可能不是NaN，而是一个字符串。

```text
isNaN('Hello') // true
// 相当于
isNaN(Number('Hello')) // true
出于同样的原因，对于对象和数组，isNaN也返回true。

isNaN({}) // true
// 等同于
isNaN(Number({})) // true

isNaN(['xzy']) // true
// 等同于
isNaN(Number(['xzy'])) // true
//但是，对于空数组和只有一个数值成员的数组，isNaN返回false。

isNaN([]) // false
isNaN([123]) // false
isNaN(['123']) // false
```

上面代码之所以返回false，原因是这些数组能被Number函数转成数值

因此，使用isNaN之前，最好判断一下数据类型。

```text
function myIsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

判断NaN更可靠的方法是，利用NaN为唯一不等于自身的值的这个特点，进行判断。

```text
function myIsNaN(value) {
  return value !== value;
}
```

### 字符串

如果要在单引号字符串的内部，使用单引号，就必须在内部的单引号前面加上反斜杠，用来转义。双引号字符串内部使用双引号，也是如此。

```text
'Did she say \'Hello\'?'
// "Did she say 'Hello'?"
```

如果长字符串必须分成多行，可以在每一行的尾部使用反斜杠。

```text
ar longString = 'Long \
long \
long \
string';

longString
// "Long long long string"
```

#### 转义

反斜杠（\）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。

需要用反斜杠转义的特殊字符，主要有下面这些。

```text
\0 ：null（\u0000）
\b ：后退键（\u0008）
\f ：换页符（\u000C）
\n ：换行符（\u000A）
\r ：回车键（\u000D）
\t ：制表符（\u0009）
\v ：垂直制表符（\u000B）
\' ：单引号（\u0027）
\" ：双引号（\u0022）
\\ ：反斜杠（\u005C）

//上面这些字符前面加上反斜杠，都表示特殊含义。

console.log('1\n2')
// 1
// 2


'\251' // "©"
'\xA9' // "©"
'\u00A9' // "©"

'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true


//如果字符串的正常内容之中，需要包含反斜杠，则反斜杠前面需要再加一个反斜杠，用来对自身转义。

"Prev \\ Next"
// "Prev \ Next"
```

上面代码中，\n表示换行，输出的时候就分成了两行。

#### Base64 转码

有时，文本里面包含一些不可打印的符号，比如 ASCII 码0到31的符号都无法打印出来，这时可以使用 Base64 编码，将它们转成可以打印的字符。另一个场景是，有时需要以文本格式传递二进制数据，那么也可以使用 Base64 编码。

所谓 Base64 就是一种编码方法，可以将任意值转成 0～9、A～Z、a-z、+和/这64个字符组成的可打印字符。使用它的主要目的，不是为了加密，而是为了不出现特殊字符，简化程序的处理。

JavaScript 原生提供两个 Base64 相关的方法。

btoa\(\)：任意值转为 Base64 编码 atob\(\)：Base64 编码转为原来的值

```text
var string = 'Hello World!';
btoa(string) // "SGVsbG8gV29ybGQh"
atob('SGVsbG8gV29ybGQh') // "Hello World!"
```

注意，这两个方法不适合非 ASCII 码的字符，会报错。

```text
btoa('你好') // 报错
```

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。

```text
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

js 图片与base64互相转换

```text
var img = "imgurl";//imgurl 就是你的图片路径  

function getBase64Image(img) {  
     var canvas = document.createElement("canvas");  
     canvas.width = img.width;  
     canvas.height = img.height;  
     var ctx = canvas.getContext("2d");  
     ctx.drawImage(img, 0, 0, img.width, img.height);  
     var ext = img.src.substring(img.src.lastIndexOf(".")+1).toLowerCase();  
     var dataURL = canvas.toDataURL("image/"+ext);  
     return dataURL;  
}  

var image = new Image();  
image.src = img;  
image.onload = function(){  
  var base64 = getBase64Image(image);  
  console.log(base64);  
}
```

### 对象

对象（object）是 JavaScript 语言的核心概念，也是最重要的数据类型。

什么是对象？简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

如果两个变量指向同一个原始类型的值。那么，变量这时都是值的拷贝。

```text
var x = 1;
var y = x;

x = 2;
y // 1
```

上面的代码中，当x的值发生变化后，y的值并不变，这就表示y和x并不是指向同一个内存地址。

属性的查看 查看一个对象本身的所有属性，可以使用Object.keys方法。

```text
var obj = {
  key1: 1,
  key2: 2
};

Object.keys(obj);
// ['key1', 'key2']
```

属性的删除：delete 命令 delete命令用于删除对象的属性，删除成功后返回true。

```text
var obj = { p: 1 };
Object.keys(obj) // ["p"]

delete obj.p // true
obj.p // undefined
Object.keys(obj) // []
```

上面代码中，delete命令删除对象obj的p属性。删除后，再读取p属性就会返回undefined，而且Object.keys方法的返回值也不再包括该属性。

注意，删除一个不存在的属性，delete不报错，而且返回true。

var obj = {}; delete obj.p // true 上面代码中，对象obj并没有p属性，但是delete命令照样返回true。因此，不能根据delete命令的结果，认定某个属性是存在的。

只有一种情况，delete命令会返回false，那就是该属性存在，且不得删除。

```text
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
});

obj.p // 123
delete obj.p // false
```

另外，需要注意的是，delete命令只能删除对象本身的属性，无法删除继承的属性（关于继承参见《面向对象编程》章节）。

```text
var obj = {};
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

上面代码中，toString是对象obj继承的属性，虽然delete命令返回true，但该属性并没有被删除，依然存在。这个例子还说明，即使delete返回true，该属性依然可能读取到值。

#### 属性是否存在：in 运算符

in运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回true，否则返回false。它的左边是一个字符串，表示属性名，右边是一个对象。

```text
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
```

in运算符的一个问题是，它不能识别哪些属性是对象自身的，哪些属性是继承的。就像上面代码中，对象obj本身并没有toString属性，但是in运算符会返回true，因为这个属性是继承的。

这时，可以使用对象的hasOwnProperty方法判断一下，是否为对象自身的属性。

```text
var obj = {};
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')) // false
}
```

如果继承的属性是可遍历的，那么就会被for...in循环遍历到。但是，一般情况下，都是只想遍历对象自身的属性，所以使用for...in的时候，应该结合使用hasOwnProperty方法，在循环内部判断一下，某个属性是否为对象自身的属性。

```text
var person = { name: '老张' };

for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
// name
```

### 函数的声明

JavaScript 有三种声明函数的方法。

（1）function 命令

function命令声明的代码区块，就是一个函数。function命令后面是函数名，函数名后面是一对圆括号，里面是传入函数的参数。函数体放在大括号里面。

```text
function print(s) {
  console.log(s);
}
```

上面的代码命名了一个print函数，以后使用print\(\)这种形式，就可以调用相应的代码。这叫做函数的声明（Function Declaration）。

（2）函数表达式

除了用function命令声明函数，还可以采用变量赋值的写法。

```text
var print = function(s) {
  console.log(s);
};
```

这种写法将一个匿名函数赋值给变量。这时，这个匿名函数又称函数表达式（Function Expression），因为赋值语句的等号右侧只能放表达式。

采用函数表达式声明函数时，function命令后面不带有函数名。如果加上函数名，该函数名只在函数体内部有效，在函数体外部无效。

```text
var print = function x(){
  console.log(typeof x);
};

x
// ReferenceError: x is not defined

print()
// function
```

（3）Function 构造函数

第三种声明函数的方式是Function构造函数。

```text
var add = new Function(
  'x',
  'y',
  'return x + y'
);

// 等同于
function add(x, y) {
  return x + y;
}
```

函数可以调用自身，这就是递归（recursion）。下面就是通过递归，计算斐波那契数列的代码。

```text
function fib(num) {
  if (num === 0) return 0;
  if (num === 1) return 1;
  return fib(num - 2) + fib(num - 1);
}

fib(6) // 8
```

name 属性 函数的name属性返回函数的名字。

```text
function f1() {}
f1.name // "f1"
```

如果是通过变量赋值定义的函数，那么name属性返回变量名。

```text
var f2 = function () {};
f2.name // "f2"
```

length 属性 函数的length属性返回函数预期传入的参数个数，即函数定义之中的参数个数。

```text
function f(a, b) {}
f.length // 2
```

上面代码定义了空函数f，它的length属性就是定义时的参数个数。不管调用时输入了多少个参数，length属性始终等于2。

length属性提供了一种机制，判断定义时和调用时参数的差异，以便实现面向对象编程的“方法重载”（overload）。

### 函数内部的变量提升

与全局作用域一样，函数作用域内部也会产生“变量提升”现象。var命令声明的变量，不管在什么位置，变量声明都会被提升到函数体的头部。

```text
function foo(x) {
  if (x > 100) {
    var tmp = x - 100;
  }
}

// 等同于
function foo(x) {
  var tmp;
  if (x > 100) {
    tmp = x - 100;
  };
}
```

函数本身的作用域 函数本身也是一个值，也有自己的作用域。它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关。

```text
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f() // 1
```

上面代码中，函数x是在函数f的外部声明的，所以它的作用域绑定外层，内部变量a不会到函数f体内取值，所以输出1，而不是2。

总之，函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。

很容易犯错的一点是，如果函数A调用函数B，却没考虑到函数B不会引用函数A的内部变量。

```text
var x = function () {
  console.log(a);
};

function y(f) {
  var a = 2;
  f();
}

y(x)
// ReferenceError: a is not defined
```

上面代码将函数x作为参数，传入函数y。但是，函数x是在函数y体外声明的，作用域绑定外层，因此找不到函数y的内部变量a，导致报错。

同样的，函数体内部声明的函数，作用域绑定函数体内部。

```text
function foo() {
  var x = 1;
  function bar() {
    console.log(x);
  }
  return bar;
}

var x = 2;
var f = foo();
f() // 1
```

上面代码中，函数foo内部声明了一个函数bar，bar的作用域绑定foo。当我们在foo外部取出bar执行时，变量x指向的是foo内部的x，而不是foo外部的x。正是这种机制，构成了下文要讲解的“闭包”现象。

#### 传递方式

函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递（passes by value）。这意味着，在函数体内修改参数值，不会影响到函数外部。

```text
var p = 2;

function f(p) {
  p = 3;
}
f(p);

p // 2
```

上面代码中，变量p是一个原始类型的值，传入函数f的方式是传值传递。因此，在函数内部，p的值是原始值的拷贝，无论怎么修改，都不会影响到原始值。

但是，如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是传址传递（pass by reference）。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值。

```text
var obj = { p: 1 };

function f(o) {
  o.p = 2;
}
f(obj);

obj.p // 2
```

上面代码中，传入函数f的是参数对象obj的地址。因此，在函数内部修改obj的属性p，会影响到原始值。

注意，如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值。

```text
var obj = [1, 2, 3];

function f(o) {
  o = [2, 3, 4];
}
f(obj);

obj // [1, 2, 3]
```

上面代码中，在函数f内部，参数对象obj被整个替换成另一个值。这时不会影响到原始值。这是因为，形式参数（o）的值实际是参数obj的地址，重新对o赋值导致o指向另一个地址，保存在原地址上的值当然不受影响

#### 同名参数

如果有同名的参数，则取最后出现的那个值。

```text
function f(a, a) {
  console.log(a);
}

f(1, 2) // 2
```

上面代码中，函数f有两个参数，且参数名都是a。取值的时候，以后面的a为准，即使后面的a没有值或被省略，也是以其为准。

```text
function f(a, a) {
  console.log(a);
}

f(1) // undefined
```

调用函数f的时候，没有提供第二个参数，a的取值就变成了undefined。这时，如果要获得第一个a的值，可以使用arguments对象。

```text
function f(a, a) {
  console.log(arguments[0]);
}

f(1) // 1
```

#### arguments 对象

（1）定义

由于 JavaScript 允许函数有不定数目的参数，所以需要一种机制，可以在函数体内部读取所有参数。这就是arguments对象的由来。

arguments对象包含了函数运行时的所有参数，arguments\[0\]就是第一个参数，arguments\[1\]就是第二个参数，以此类推。这个对象只有在函数体内部，才可以使用。

```text
var f = function (one) {
  console.log(arguments[0]);
  console.log(arguments[1]);
  console.log(arguments[2]);
}

f(1, 2, 3)
// 1
// 2
// 3
//正常模式下，arguments对象可以在运行时修改。

var f = function(a, b) {
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 5
```

上面代码中，函数f调用时传入的参数，在函数内部被修改成3和2。

严格模式下，arguments对象与函数参数不具有联动关系。也就是说，修改arguments对象不会影响到实际的函数参数。

```text
var f = function(a, b) {
  'use strict'; // 开启严格模式
  arguments[0] = 3;
  arguments[1] = 2;
  return a + b;
}

f(1, 1) // 2
```

上面代码中，函数体内是严格模式，这时修改arguments对象，不会影响到真实参数a和b。

通过arguments对象的length属性，可以判断函数调用时到底带几个参数。

```text
function f() {
  return arguments.length;
}

f(1, 2, 3) // 3
f(1) // 1
f() // 0
```

（2）与数组的关系

需要注意的是，虽然arguments很像数组，但它是一个对象。数组专有的方法（比如slice和forEach），不能在arguments对象上直接使用。

如果要让arguments对象使用数组方法，真正的解决方法是将arguments转为真正的数组。下面是两种常用的转换方法：slice方法和逐一填入新数组。

```text
var args = Array.prototype.slice.call(arguments);


// 或者
var args = [];
for (var i = 0; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```

（3）callee 属性

arguments对象带有一个callee属性，返回它所对应的原函数。

```text
var f = function () {
  console.log(arguments.callee === f);
}

f() // true
```

可以通过arguments.callee，达到调用函数自身的目的。这个属性在严格模式里面是禁用的，因此不建议使用。

#### apply  call

apply：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.apply\(A, arguments\);即A对象应用B对象的方法。 （2）实现继承

复制代码

```text
function Animal(name){
  this.name = name;
  this.showName = function(){
        alert(this.name);    
    }    
}

function Cat(name){
  Animal.apply(this,[name]);    
}

var cat = new Cat("咕咕");
cat.showName();

/*call的用法*/
Animal.call(this,name);
```

## 闭包

如果出于种种原因，需要得到函数内的局部变量。正常情况下，这是办不到的，只有通过变通方法才能实现。那就是在函数的内部，再定义一个函数。

```text
function f1() {
  var n = 999;
  function f2() {
　　console.log(n); // 999
  }
}
```

上面代码中，函数f2就在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是 JavaScript 语言特有的"链式作用域"结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

既然f2可以读取f1的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！

```text
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

上面代码中，函数f1的返回值就是函数f2，由于f2可以读取f1的内部变量，所以就可以在外部获得f1的内部变量了。

* 闭包就是函数f2，即能够读取其他函数内部变量的函数。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。闭包最大的特点，就是它可以“记住”诞生的环境，比如f2记住了它诞生的环境f1，所以从f2可以得到f1的内部变量。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。
* 闭包的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在。请看下面的例子，闭包使得内部变量记住上一次调用时的运算结果。

```text
function createIncrementor(start) {
  return function () {
    return start++;
  };
}

var inc = createIncrementor(5);

inc() // 5
inc() // 6
inc() // 7
```

上面代码中，start是函数createIncrementor的内部变量。通过闭包，start的状态被保留了，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包inc使得函数createIncrementor的内部环境，一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

为什么会这样呢？原因就在于inc始终在内存中，而inc的存在依赖于createIncrementor，因此也始终在内存中，不会在调用结束后，被垃圾回收机制回收。

闭包的另一个用处，是封装对象的私有属性和私有方法。

```text
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge
  };
}

var p1 = Person('张三');
p1.setAge(25);
p1.getAge() // 25
```

上面代码中，函数Person的内部变量\_age，通过闭包getAge和setAge，变成了返回对象p1的私有变量。

注意，外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，所以内存消耗很大。因此不能滥用闭包，否则会造成网页的性能问题。

#### eval 命令

基本用法 eval命令接受一个字符串作为参数，并将这个字符串当作语句执行。

eval\('var a = 1;'\); a // 1 上面代码将字符串当作语句运行，生成了变量a。

如果参数字符串无法当作语句运行，那么就会报错。

eval\('3x'\) // Uncaught SyntaxError: Invalid or unexpected token 放在eval中的字符串，应该有独自存在的意义，不能用来与eval以外的命令配合使用。举例来说，下面的代码将会报错。

eval\('return;'\); // Uncaught SyntaxError: Illegal return statement 上面代码会报错，因为return不能单独使用，必须在函数中使用。

如果eval的参数不是字符串，那么会原样返回。

eval\(123\) // 123 eval没有自己的作用域，都在当前作用域内执行，因此可能会修改当前作用域的变量的值，造成安全问题。

```text
var a = 1;
eval('a = 2');

a // 2
```

上面代码中，eval命令修改了外部变量a的值。由于这个原因，eval有安全风险。

为了防止这种风险，JavaScript 规定，如果使用严格模式，eval内部声明的变量，不会影响到外部作用域。

```text
(function f() {
  'use strict';
  eval('var foo = 123');
  console.log(foo);  // ReferenceError: foo is not defined
})()
```

上面代码中，函数f内部是严格模式，这时eval内部声明的foo变量，就不会影响到外部。

不过，即使在严格模式下，eval依然可以读写当前作用域的变量。

```text
(function f() {
  'use strict';
  var foo = 1;
  eval('foo = 2');
  console.log(foo);  // 2
})()
```

上面代码中，严格模式下，eval内部还是改写了外部变量，可见安全风险依然存在。

总之，eval的本质是在当前作用域之中，注入代码。由于安全风险和不利于 JavaScript 引擎优化执行速度，所以一般不推荐使用。通常情况下，eval最常见的场合是解析 JSON 数据的字符串，不过正确的做法应该是使用原生的JSON.parse方法。

#### 数组

数组的本质 本质上，数组属于一种特殊的对象。typeof运算符会返回数组的类型是object。

typeof \[1, 2, 3\] // "object" 上面代码表明，typeof运算符认为数组的类型就是对象。

数组的特殊性体现在，它的键名是按次序排列的一组整数（0，1，2...）。

```text
var arr = ['a', 'b', 'c'];

Object.keys(arr)
// ["0", "1", "2"]
```

上面代码中，Object.keys方法返回数组的所有键名。可以看到数组的键名就是整数0、1、2。

由于数组成员的键名是固定的（默认总是0、1、2...），因此数组不用为每个元素指定键名，而对象的每个成员都必须指定键名。JavaScript 语言规定，对象的键名一律为字符串，所以，数组的键名其实也是字符串。之所以可以用数值读取，是因为非字符串的键名会被转为字符串。

```text
var arr = ['a', 'b', 'c'];

arr['0'] // 'a'
arr[0] // 'a'
```

上面代码分别用数值和字符串作为键名，结果都能读取数组。原因是数值键名被自动转为了字符串。

注意，这点在赋值时也成立。一个值总是先转成字符串，再作为键名进行赋值。

```text
var a = [];

a[1.00] = 6;
a[1] // 6
```

上面代码中，由于1.00转成字符串是1，所以通过数字键1可以读取值

> in 运算符 检查某个键名是否存在的运算符in，适用于对象，也适用于数组。

```text
var arr = [ 'a', 'b', 'c' ];
2 in arr  // true
'2' in arr // true
4 in arr // false
```

上面代码表明，数组存在键名为2的键。由于键名都是字符串，所以数值2会自动转成字符串。

注意，如果数组的某个位置是空位，in运算符返回false。

```text
var arr = [];
arr[100] = 'a';

100 in arr // true
1 in arr // false
```

上面代码中，数组arr只有一个成员arr\[100\]，其他位置的键名都会返回false。

> for...in不仅会遍历数组所有的数字键，还会遍历非数字键。

```text
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```

上面代码在遍历数组时，也遍历到了非整数键foo。所以，不推荐使用for...in遍历数组。

数组的遍历可以考虑使用for循环或while循环。

```text
var a = [1, 2, 3];

// for循环
for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}

// while循环
var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}

var l = a.length;
while (l--) {
  console.log(a[l]);
}
```

上面代码是三种遍历数组的写法。最后一种写法是逆向遍历，即从最后一个元素向第一个元素遍历。

> 数组的空位是可以读取的，返回undefined。

```text
var a = [, , ,];
a[1] // undefined
使用delete命令删除一个数组成员，会形成空位，并且不会影响length属性。

var a = [1, 2, 3];
delete a[1];

a[1] // undefined
a.length // 3
```

上面代码用delete命令删除了数组的第二个元素，这个位置就形成了空位，但是对length属性没有影响。也就是说，length属性不过滤空位。所以，使用length属性进行数组遍历，一定要非常小心。

数组的某个位置是空位，与某个位置是undefined，是不一样的。如果是空位，使用数组的forEach方法、for...in结构、以及Object.keys方法进行遍历，空位都会被跳过。

```text
var a = [, , ,];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
})
// 不产生任何输出

for (var i in a) {
  console.log(i);
}
// 不产生任何输出

Object.keys(a)
// []
如果某个位置是undefined，遍历的时候就不会被跳过。

var a = [undefined, undefined, undefined];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
});
// 0. undefined
// 1. undefined
// 2. undefined

for (var i in a) {
  console.log(i);
}
// 0
// 1
// 2

Object.keys(a)
// ['0', '1', '2']
```

这就是说，空位就是数组没有这个元素，所以不会被遍历到，而undefined则表示数组有这个元素，值是undefined，所以遍历不会跳过。

#### 类数组

类似数组的对象 如果一个对象的所有键名都是正整数或零，并且有length属性，那么这个对象就很像数组，语法上称为“类似数组的对象”（array-like object）。

```text
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3
};

obj[0] // 'a'
obj[1] // 'b'
obj.length // 3
obj.push('d') // TypeError: obj.push is not a function
```

上面代码中，对象obj就是一个类似数组的对象。但是，“类似数组的对象”并不是数组，因为它们不具备数组特有的方法。对象obj没有数组的push方法，使用该方法就会报错。

“类似数组的对象”的根本特征，就是具有length属性。只要有length属性，就可以认为这个对象类似于数组。但是有一个问题，这种length属性不是动态值，不会随着成员的变化而变化。

var obj = { length: 0 }; obj\[3\] = 'd'; obj.length // 0 上面代码为对象obj添加了一个数字键，但是length属性没变。这就说明了obj不是数组。

典型的“类似数组的对象”是函数的arguments对象，以及大多数 DOM 元素集，还有字符串。

```text
// arguments对象
function args() { return arguments }
var arrayLike = args('a', 'b');

arrayLike[0] // 'a'
arrayLike.length // 2
arrayLike instanceof Array // false

// DOM元素集
var elts = document.getElementsByTagName('h3');
elts.length // 3
elts instanceof Array // false

// 字符串
'abc'[1] // 'b'
'abc'.length // 3
'abc' instanceof Array // false
```

上面代码包含三个例子，它们都不是数组（instanceof运算符返回false），但是看上去都非常像数组。

> 数组的slice方法可以将“类似数组的对象”变成真正的数组

```text
var arr = Array.prototype.slice.call(arrayLike);
```

除了转为真正的数组，“类似数组的对象”还有一个办法可以使用数组的方法，就是通过call\(\)把数组的方法放到对象上面。

function print\(value, index\) { console.log\(index + ' : ' + value\); }

Array.prototype.forEach.call\(arrayLike, print\); 上面代码中，arrayLike代表一个类似数组的对象，本来是不可以使用数组的forEach\(\)方法的，但是通过call\(\)，可以把forEach\(\)嫁接到arrayLike上面调用。

下面的例子就是通过这种方法，在arguments对象上面调用forEach方法。

```text
// forEach 方法
function logArgs() {
  Array.prototype.forEach.call(arguments, function (elem, i) {
    console.log(i + '. ' + elem);
  });
}

// 等同于 for 循环
function logArgs() {
  for (var i = 0; i < arguments.length; i++) {
    console.log(i + '. ' + arguments[i]);
  }
}
```

字符串也是类似数组的对象，所以也可以用Array.prototype.forEach.call遍历。

```text
Array.prototype.forEach.call('abc', function (chr) {
  console.log(chr);
});
// a
// b
// c
```

注意，这种方法比直接使用数组原生的forEach要慢，所以最好还是先将“类似数组的对象”转为真正的数组，然后再直接调用数组的forEach方法。

```text
var arr = Array.prototype.slice.call('abc');
arr.forEach(function (chr) {
  console.log(chr);
});
// a
// b
// c
```

### 运算符

> 加法运算符是在运行时决定，到底是执行相加，还是执行连接。也就是说，运算子的不同，导致了不同的语法行为，这种现象称为“重载”（overload）。由于加法运算符存在重载，可能执行两种运算，使用的时候必须很小心。

```text
'3' + 4 + 5 // "345"
3 + 4 + '5' // "75"
```

> 除了加法运算符，其他算术运算符（比如减法、除法和乘法）都不会发生重载。它们的规则是：所有运算子一律转为数值，再进行相应的数学运算。

```text
1 - '2' // -1
1 * '2' // 2
1 / '2' // 0.5
```

> 对象的相加 如果运算子是对象，必须先转成原始类型的值，然后再相加。

```text
var obj = { p: 1 };
obj + 2 // "[object Object]2"
```

上面代码中，对象obj转成原始类型的值是\[object Object\]，再加2就得到了上面的结果。

对象转成原始类型的值，规则如下。

首先，自动调用对象的valueOf方法。

```text
var obj = { p: 1 };
obj.valueOf() // { p: 1 }
//一般来说，对象的valueOf方法总是返回对象自身，这时再自动调用对象的toString方法，将其转为字符串。

var obj = { p: 1 };
obj.valueOf().toString() // "[object Object]"
```

对象的toString方法默认返回\[object Object\]，所以就得到了最前面那个例子的结果。

知道了这个规则以后，就可以自己定义valueOf方法或toString方法，得到想要的结果。

```text
var obj = {
  valueOf: function () {
    return 1;
  }
};

obj + 2 // 3
```

上面代码中，我们定义obj对象的valueOf方法返回1，于是obj + 2就得到了3。这个例子中，由于valueOf方法直接返回一个原始类型的值，所以不再调用toString方法。

下面是自定义toString方法的例子。

```text
var obj = {
  toString: function () {
    return 'hello';
  }
};

obj + 2 // "hello2"
```

上面代码中，对象obj的toString方法返回字符串hello。前面说过，只要有一个运算子是字符串，加法运算符就变成连接运算符，返回连接后的字符串。

这里有一个特例，如果运算子是一个Date对象的实例，那么会优先执行toString方法。

```text
var obj = new Date();
obj.valueOf = function () { return 1 };
obj.toString = function () { return 'hello' };

obj + 2 // "hello2"
```

上面代码中，对象obj是一个Date对象的实例，并且自定义了valueOf方法和toString方法，结果toString方法优先执行。

> 余数运算符 余数运算符（%）返回前一个运算子被后一个运算子除，所得的余数。

```text
12 % 5 // 2
需要注意的是，运算结果的正负号由第一个运算子的正负号决定。

-1 % 2 // -1
1 % -2 // 1
所以，为了得到负数的正确余数值，可以先使用绝对值函数。

// 错误的写法
function isOdd(n) {
  return n % 2 === 1;
}
isOdd(-5) // false
isOdd(-4) // false

// 正确的写法
function isOdd(n) {
  return Math.abs(n % 2) === 1;
}
isOdd(-5) // true
isOdd(-4) // false
余数运算符还可以用于浮点数的运算。但是，由于浮点数不是精确的值，无法得到完全准确的结果。

6.5 % 2.1
// 0.19999999999999973
```

> 严格不相等运算符 严格相等运算符有一个对应的“严格不相等运算符”（!==），它的算法就是先求严格相等运算符的结果，然后返回相反值。

```text
1 !== '1' // true
// 等同于
!(1 === '1')
```

> undefined 和 null

undefined和null与其他类型的值比较时，结果都为false，它们互相比较时结果为true。

```text
false == null // false
false == undefined // false

0 == null // false
0 == undefined // false

undefined == null // true
```

> 不相等运算符 相等运算符有一个对应的“不相等运算符”（!=），它的算法就是先求相等运算符的结果，然后返回相反值。

```text
1 != '1' // false

// 等同于
!(1 == '1')
```

### Boolean

如果对一个值连续做两次取反运算，等于将其转为对应的布尔值，与Boolean函数的作用相同。这是一种常用的类型转换的写法。

```text
!!x
// 等同于
Boolean(x)
```

上面代码中，不管x是什么类型的值，经过两次取反运算后，变成了与Boolean函数结果相同的布尔值。所以，两次取反就是将一个值转为布尔值的简便写法。

> 且运算符（&&） 且运算符（&&）往往用于多个表达式的求值。

它的运算规则是：如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值。

```text
't' && '' // ""
't' && 'f' // "f"
't' && (1 + 2) // 3
'' && 'f' // ""
'' && '' // ""

var x = 1;
(1 - 1) && ( x += 1) // 0
x // 1
```

上面代码的最后一个例子，由于且运算符的第一个运算子的布尔值为false，则直接返回它的值0，而不再对第二个运算子求值，所以变量x的值没变。

> 且运算符可以多个连用，这时返回第一个布尔值为false的表达式的值。如果所有表达式的布尔值都为true，则返回最后一个表达式的值。

```text
true && 'foo' && '' && 4 && 'foo' && true
// ''

1 && 2 && 3
// 3
```

> 或运算符（\|\|） 或运算符（\|\|）也用于多个表达式的求值。它的运算规则是：如果第一个运算子的布尔值为true，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为false，则返回第二个运算子的值。

```text
't' || '' // "t"
't' || 'f' // "t"
'' || 'f' // "f"
'' || '' // ""
//短路规则对这个运算符也适用。

var x = 1;
true || (x = 2) // true
x // 1
```

上面代码中，或运算符的第一个运算子为true，所以直接返回true，不再运行第二个运算子。所以，x的值没有改变。这种只通过第一个表达式的值，控制是否运行第二个表达式的机制，就称为“短路”（short-cut）。

或运算符可以多个连用，这时返回第一个布尔值为true的表达式的值。如果所有表达式都为false，则返回最后一个表达式的值。

```text
false || 0 || '' || 4 || 'foo' || true
// 4

false || 0 || ''
// ''
```

### 二进制位运算符

> 概述 二进制位运算符用于直接对二进制位进行计算，一共有7个。

* 二进制或运算符（or）：符号为\|，表示若两个二进制位都为0，则结果为0，否则为1。
* 二进制与运算符（and）：符号为&，表示若两个二进制位都为1，则结果为1，否则为0。
* 二进制否运算符（not）：符号为~，表示对一个二进制位取反。
* 异或运算符（xor）：符号为^，表示若两个二进制位不相同，则结果为1，否则为0。
* 左移运算符（left shift）：符号为&lt;&lt;，详见下文解释。
* 右移运算符（right shift）：符号为&gt;&gt;，详见下文解释。
* 头部补零的右移运算符（zero filled right shift）：符号为&gt;&gt;&gt;，详见下文解释。
* 这些位运算符直接处理每一个比特位（bit），所以是非常底层的运算，好处是速度极快，缺点是很不直观，许多场合不能使用它们，否则会使代码难以理解和查错。

有一点需要特别注意，位运算符只对整数起作用，如果一个运算子不是整数，会自动转为整数后再执行。另外，虽然在 JavaScript 内部，数值都是以64位浮点数的形式储存，但是做位运算的时候，是以32位带符号的整数进行运算的，并且返回值也是一个32位带符号的整数。

```text
i = i | 0;
```

上面这行代码的意思，就是将i（不管是整数或小数）转为32位整数。

利用这个特性，可以写出一个函数，将任意数值转为32位整数。

```text
function toInt32(x) {
  return x | 0;
}
```

上面这个函数将任意值与0进行一次或运算，这个位运算会自动将一个值转为32位整数。下面是这个函数的用法。

```text
toInt32(1.001) // 1
toInt32(1.999) // 1
toInt32(1) // 1
toInt32(-1) // -1
toInt32(Math.pow(2, 32) + 1) // 1
toInt32(Math.pow(2, 32) - 1) // -1
```

上面代码中，toInt32可以将小数转为整数。对于一般的整数，返回值不会有任何变化。对于大于或等于2的32次方的整数，大于32位的数位都会被舍去。

> 二进制或运算符 二进制或运算符（\|）逐位比较两个运算子，两个二进制位之中只要有一个为1，就返回1，否则返回0。

```text
0 | 3 // 3
```

上面代码中，0和3的二进制形式分别是00和11，所以进行二进制或运算会得到11（即3）。

位运算只对整数有效，遇到小数时，会将小数部分舍去，只保留整数部分。所以，将一个小数与0进行二进制或运算，等同于对该数去除小数部分，即取整数位。

```text
2.9 | 0 // 2
-2.9 | 0 // -2
```

需要注意的是，这种取整方法不适用超过32位整数最大值2147483647的数。

```text
2147483649.4 | 0;
// -2147483647
```

> 二进制与运算符 二进制与运算符（&）的规则是逐位比较两个运算子，两个二进制位之中只要有一个位为0，就返回0，否则返回1。

```text
0 & 3 // 0
```

上面代码中，0（二进制00）和3（二进制11）进行二进制与运算会得到00（即0）。

> 二进制否运算符 二进制否运算符（~）将每个二进制位都变为相反值（0变为1，1变为0）。它的返回结果有时比较难理解，因为涉及到计算机内部的数值表示机制。

```text
~ 3 // -4
```

上面表达式对3进行二进制否运算，得到-4。之所以会有这样的结果，是因为位运算时，JavaScript 内部将所有的运算子都转为32位的二进制整数再进行运算。

3的32位整数形式是00000000000000000000000000000011，二进制否运算以后得到11111111111111111111111111111100。由于第一位（符号位）是1，所以这个数是一个负数。JavaScript 内部采用补码形式表示负数，即需要将这个数减去1，再取一次反，然后加上负号，才能得到这个负数对应的10进制值。这个数减去1等于11111111111111111111111111111011，再取一次反得到00000000000000000000000000000100，再加上负号就是-4。考虑到这样的过程比较麻烦，可以简单记忆成，一个数与自身的取反值相加，等于-1。

```text
~ -3 // 2
```

上面表达式可以这样算，-3的取反值等于-1减去-3，结果为2。

对一个整数连续两次二进制否运算，得到它自身。

```text
~~3 // 3
```

所有的位运算都只对整数有效。二进制否运算遇到小数时，也会将小数部分舍去，只保留整数部分。所以，对一个小数连续进行两次二进制否运算，能达到取整效果。

```text
~~2.9 // 2
~~47.11 // 47
~~1.9999 // 1
~~3 // 3
```

使用二进制否运算取整，是所有取整方法中最快的一种。

对字符串进行二进制否运算，JavaScript 引擎会先调用Number函数，将字符串转为数值。

```text
// 相当于~Number('011')
~'011'  // -12

// 相当于~Number('42 cats')
~'42 cats' // -1

// 相当于~Number('0xcafebabe')
~'0xcafebabe' // 889275713

// 相当于~Number('deadbeef')
~'deadbeef' // -1
```

Number函数将字符串转为数值的规则，参见《数据的类型转换》一章。

对于其他类型的值，二进制否运算也是先用Number转为数值，然后再进行处理。

```text
// 相当于 ~Number([])
~[] // -1

// 相当于 ~Number(NaN)
~NaN // -1

// 相当于 ~Number(null)
~null // -1
```

> 异或运算符 异或运算（^）在两个二进制位不同时返回1，相同时返回0。

```text
0 ^ 3 // 3
```

上面表达式中，0（二进制00）与3（二进制11）进行异或运算，它们每一个二进制位都不同，所以得到11（即3）。

“异或运算”有一个特殊运用，连续对两个数a和b进行三次异或运算，a^=b; b^=a; a^=b;，可以互换它们的值。这意味着，使用“异或运算”可以在不引入临时变量的前提下，互换两个变量的值。

```text
var a = 10;
var b = 99;

a ^= b, b ^= a, a ^= b;

a // 99
b // 10
```

这是互换两个变量的值的最快方法。

异或运算也可以用来取整。

```text
12.9 ^ 0 // 12
```

> 左移运算符 左移运算符（&lt;&lt;）表示将一个数的二进制值向左移动指定的位数，尾部补0，即乘以2的指定次方。向左移动的时候，最高位的符号位是一起移动的。

```text
// 4 的二进制形式为100，
// 左移一位为1000（即十进制的8）
// 相当于乘以2的1次方
4 << 1
// 8

-4 << 1
// -8
```

上面代码中，-4左移一位得到-8，是因为-4的二进制形式是11111111111111111111111111111100，左移一位后得到11111111111111111111111111111000，该数转为十进制（减去1后取反，再加上负号）即为-8。

如果左移0位，就相当于将该数值转为32位整数，等同于取整，对于正数和负数都有效。

```text
13.5 << 0
// 13

-13.5 << 0
// -13
```

左移运算符用于二进制数值非常方便。

```text
var color = {r: 186, g: 218, b: 85};

// RGB to HEX
// (1 << 24)的作用为保证结果是6位数
var rgb2hex = function(r, g, b) {
  return '#' + ((1 << 24) + (r << 16) + (g << 8) + b)
    .toString(16) // 先转成十六进制，然后返回字符串
    .substr(1);   // 去除字符串的最高位，返回后面六个字符串
}

rgb2hex(color.r, color.g, color.b)
// "#bada55"
```

上面代码使用左移运算符，将颜色的 RGB 值转为 HEX 值。

> 右移运算符 右移运算符（&gt;&gt;）表示将一个数的二进制值向右移动指定的位数。如果是正数，头部全部补0；如果是负数，头部全部补1。右移运算符基本上相当于除以2的指定次方（最高位即符号位参与移动）。

```text
4 >> 1
// 2
/*
// 因为4的二进制形式为 00000000000000000000000000000100，
// 右移一位得到 00000000000000000000000000000010，
// 即为十进制的2
*/

-4 >> 1
// -2
/*
// 因为-4的二进制形式为 11111111111111111111111111111100，
// 右移一位，头部补1，得到 11111111111111111111111111111110,
// 即为十进制的-2
*/
右移运算可以模拟 2 的整除运算。

5 >> 1
// 2
// 相当于 5 / 2 = 2

21 >> 2
// 5
// 相当于 21 / 4 = 5

21 >> 3
// 2
// 相当于 21 / 8 = 2

21 >> 4
// 1
// 相当于 21 / 16 = 1
```

> 头部补零的右移运算符 头部补零的右移运算符（&gt;&gt;&gt;）与右移运算符（&gt;&gt;）只有一个差别，就是一个数的二进制形式向右移动时，头部一律补零，而不考虑符号位。所以，该运算总是得到正值。对于正数，该运算的结果与右移运算符（&gt;&gt;）完全一致，区别主要在于负数。

```text
4 >>> 1
// 2

-4 >>> 1
// 2147483646
/*
// 因为-4的二进制形式为11111111111111111111111111111100，
// 带符号位的右移一位，得到01111111111111111111111111111110，
// 即为十进制的2147483646。
*/
```

这个运算实际上将一个值转为32位无符号整数。

查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符。

-1 &gt;&gt;&gt; 0 // 4294967295 上面代码表示，-1作为32位整数时，内部的储存形式使用无符号整数格式解读，值为 4294967295（即\(2^32\)-1，等于11111111111111111111111111111111）。

开关作用 位运算符可以用作设置对象属性的开关。

假定某个对象有四个开关，每个开关都是一个变量。那么，可以设置一个四位的二进制数，它的每个位对应一个开关。

var FLAG\_A = 1; // 0001 var FLAG\_B = 2; // 0010 var FLAG\_C = 4; // 0100 var FLAG\_D = 8; // 1000 上面代码设置 A、B、C、D 四个开关，每个开关分别占有一个二进制位。

然后，就可以用二进制与运算检验，当前设置是否打开了指定开关。

var flags = 5; // 二进制的0101

if \(flags & FLAG\_C\) { // ... } // 0101 & 0100 =&gt; 0100 =&gt; true 上面代码检验是否打开了开关C。如果打开，会返回true，否则返回false。

现在假设需要打开A、B、D三个开关，我们可以构造一个掩码变量。

var mask = FLAG\_A \| FLAG\_B \| FLAG\_D; // 0001 \| 0010 \| 1000 =&gt; 1011 上面代码对A、B、D三个变量进行二进制或运算，得到掩码值为二进制的1011。

有了掩码，二进制或运算可以确保打开指定的开关。

flags = flags \| mask; 二进制与运算可以将当前设置中凡是与开关设置不一样的项，全部关闭。

flags = flags & mask; 异或运算可以切换（toggle）当前设置，即第一次执行可以得到当前设置的相反值，再执行一次又得到原来的值。

flags = flags ^ mask; 二进制否运算可以翻转当前设置，即原设置为0，运算后变为1；原设置为1，运算后变为0。

flags = ~flags;

> 逗号运算符 逗号运算符用于对两个表达式求值，并返回后一个表达式的值。

```text
'a', 'b' // "b"

var x = 0;
var y = (x++, 10);
x // 1
y // 10
```

上面代码中，逗号运算符返回后一个表达式的值。

逗号运算符的一个用途是，在返回一个值之前，进行一些辅助操作。

```text
var value = (console.log('Hi!'), true);
// Hi!

value // true
```

上面代码中，先执行逗号之前的操作，然后返回逗号后面的值。

> 函数放在圆括号中，会返回函数本身。如果圆括号紧跟在函数的后面，就表示调用函数。

```text
function f() {
  return 1;
}

(f) // function f(){return 1;}
f() // 1
```

上面代码中，函数放在圆括号之中会返回函数本身，圆括号跟在函数后面则是调用函数。

圆括号之中，只能放置表达式，如果将语句放在圆括号之中，就会报错。

\(var a = 1\) // SyntaxError: Unexpected token var

> 少数运算符的计算顺序是从右到左，即从右边开始计算，这叫做运算符的“右结合”（right-to-left associativity）。其中，最主要的是赋值运算符（=）和三元条件运算符（?:）。

```text
w = x = y = z;
q = a ? b : c ? d : e ? f : g;
```

上面代码的运算结果，相当于下面的样子。

```text
w = (x = (y = z));
q = a ? b : (c ? d : (e ? f : g));
```

上面的两行代码，各有三个等号运算符和三个三元运算符，都是先计算最右边的那个运算符。

指数运算符（\*\*）也是右结合的。

```text
// 相当于 2 ** (3 ** 2)
2 ** 3 ** 2
// 512
```

### 数据类型的转换

#### 强制转换

> 强制转换主要指使用Number\(\)、String\(\)和Boolean\(\)三个函数，手动将各种类型的值，分别转换成数字、字符串或者布尔值。

Number\(\) 使用Number函数，可以将任意类型的值转化成数值。

下面分成两种情况讨论，一种是参数是原始类型的值，另一种是参数是对象。

（1）原始类型值

原始类型值的转换规则如下。

```text
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

```text
parseInt('42 cats') // 42
Number('42 cats') // NaN
```

上面代码中，parseInt逐个解析字符，而Number函数整体转换字符串的类型。

另外，parseInt和Number函数都会自动过滤一个字符串前导和后缀的空格。

```text
parseInt('\t\v\r12.34\n') // 12
Number('\t\v\r12.34\n') // 12.34
```

> 对象

简单的规则是，Number方法的参数是对象时，将返回NaN，除非是包含单个数值的数组。

```text
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
```

之所以会这样，是因为Number背后的转换规则比较复杂。

第一步，调用对象自身的valueOf方法。如果返回原始类型的值，则直接对该值使用Number函数，不再进行后续步骤。

第二步，如果valueOf方法返回的还是对象，则改为调用对象自身的toString方法。如果toString方法返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。

第三步，如果toString方法返回的是对象，就报错。

请看下面的例子。

```text
var obj = {x: 1};
Number(obj) // NaN

// 等同于
if (typeof obj.valueOf() === 'object') {
  Number(obj.toString());
} else {
  Number(obj.valueOf());
}
```

上面代码中，Number函数将obj对象转为数值。背后发生了一连串的操作，首先调用obj.valueOf方法, 结果返回对象本身；于是，继续调用obj.toString方法，这时返回字符串\[object Object\]，对这个字符串使用Number函数，得到NaN。

默认情况下，对象的valueOf方法返回对象本身，所以一般总是会调用toString方法，而toString方法返回对象的类型字符串（比如\[object Object\]）。所以，会有下面的结果。

Number\({}\) // NaN 如果toString方法返回的不是原始类型的值，结果就会报错。

```text
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

```text
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

```text
var obj = {
  width: '100'
};
obj.width + 20 // "10020"


null + 1 // 1
undefined + 1 // NaN
```

上面代码中，开发者可能期望返回120，但是由于自动转换，实际上返回了一个字符10020。

> 对于某些复合类型的数据，console.table方法可以将其转为表格显示。

```text
var languages = [
  { name: "JavaScript", fileExtension: ".js" },
  { name: "TypeScript", fileExtension: ".ts" },
  { name: "CoffeeScript", fileExtension: ".coffee" }
];

console.table(languages);
```

控制台命令行 API 浏览器控制台中，除了使用console对象，还可以使用一些控制台自带的命令行方法。

（1）$\_

$\_属性返回上一个表达式的值。

```text
2 + 2
// 4
$_
// 4
（2）$0 - $4
```

控制台保存了最近5个在 Elements 面板选中的 DOM 元素，$0代表倒数第一个（最近一个），$1代表倒数第二个，以此类推直到$4。

（3）$\(selector\)

$\(selector\)返回第一个匹配的元素，等同于document.querySelector\(\)。注意，如果页面脚本对$有定义，则会覆盖原始的定义。比如，页面里面有 jQuery，控制台执行$\(selector\)就会采用 jQuery 的实现，返回一个数组。

（4）$$\(selector\)

$$\(selector\)返回选中的 DOM 对象，等同于document.querySelectorAll。

（5）$x\(path\)

$x\(path\)方法返回一个数组，包含匹配特定 XPath 表达式的所有 DOM 元素。

$x\("//p\[a\]"\) 上面代码返回所有包含a元素的p元素。

### Object

如果参数是原始类型的值，Object方法将其转为对应的包装对象的实例（参见《原始类型的包装对象》一章）。

```text
var obj = Object(1);
obj instanceof Object // true
obj instanceof Number // true

var obj = Object('foo');
obj instanceof Object // true
obj instanceof String // true

var obj = Object(true);
obj instanceof Object // true
obj instanceof Boolean // true
```

上面代码中，Object函数的参数是各种原始类型的值，转换成对象就是原始类型值对应的包装对象。

如果Object方法的参数是一个对象，它总是返回该对象，即不用转换。

```text
var arr = [];
var obj = Object(arr); // 返回原数组
obj === arr // true

var value = {};
var obj = Object(value) // 返回原对象
obj === value // true

var fn = function () {};
var obj = Object(fn); // 返回原函数
obj === fn // true
```

利用这一点，可以写一个判断变量是否为对象的函数。

```text
function isObject(value) {
  return value === Object(value);
}

isObject([]) // true
isObject(true) // false
```

> Object 构造函数 Object不仅可以当作工具函数使用，还可以当作构造函数使用，即前面可以使用new命令。

Object构造函数的首要用途，是直接通过它来生成新对象。

var obj = new Object\(\); 注意，通过var obj = new Object\(\)的写法生成新对象，与字面量的写法var obj = {}是等价的。或者说，后者只是前者的一种简便写法。

Object构造函数的用法与工具方法很相似，几乎一模一样。使用时，可以接受一个参数，如果该参数是一个对象，则直接返回这个对象；如果是一个原始类型的值，则返回该值对应的包装对象（详见《包装对象》一章）。

```text
var o1 = {a: 1};
var o2 = new Object(o1);
o1 === o2 // true

var obj = new Object(123);
obj instanceof Number // true
```

虽然用法相似，但是Object\(value\)与new Object\(value\)两者的语义是不同的，Object\(value\)表示将value转成一个对象，new Object\(value\)则表示新生成一个对象，它的值是value。

> Object 的静态方法 所谓“静态方法”，是指部署在Object对象自身的方法。

Object.keys\(\)，Object.getOwnPropertyNames\(\) Object.keys方法和Object.getOwnPropertyNames方法都用来遍历对象的属性。

Object.keys方法的参数是一个对象，返回一个数组。该数组的成员都是该对象自身的（而不是继承的）所有属性名。

```text
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj) // ["p1", "p2"]
```

Object.getOwnPropertyNames方法与Object.keys类似，也是接受一个对象作为参数，返回一个数组，包含了该对象自身的所有属性名。

```text
var obj = {
  p1: 123,
  p2: 456
};
```

Object.getOwnPropertyNames\(obj\) // \["p1", "p2"\] 对于一般的对象来说，Object.keys\(\)和Object.getOwnPropertyNames\(\)返回的结果是一样的。只有涉及不可枚举属性时，才会有不一样的结果。Object.keys方法只返回可枚举的属性（详见《对象属性的描述对象》一章），Object.getOwnPropertyNames方法还返回不可枚举的属性名。

```text
var a = ['Hello', 'World'];

Object.keys(a) // ["0", "1"]
Object.getOwnPropertyNames(a) // ["0", "1", "length"]
```

上面代码中，数组的length属性是不可枚举的属性，所以只出现在Object.getOwnPropertyNames方法的返回结果中。

由于 JavaScript 没有提供计算对象属性个数的方法，所以可以用这两个方法代替。

```text
var obj = {
  p1: 123,
  p2: 456
};

Object.keys(obj).length // 2
Object.getOwnPropertyNames(obj).length // 2
```

一般情况下，几乎总是使用Object.keys方法，遍历对象的属性。

其他方法 除了上面提到的两个方法，Object还有不少其他静态方法，将在后文逐一详细介绍。

（1）对象属性模型的相关方法

* Object.getOwnPropertyDescriptor\(\)：获取某个属性的描述对象。
* Object.defineProperty\(\)：通过描述对象，定义某个属性。
* Object.defineProperties\(\)：通过描述对象，定义多个属性。 （2）控制对象状态的方法
* Object.preventExtensions\(\)：防止对象扩展。
* Object.isExtensible\(\)：判断对象是否可扩展。
* Object.seal\(\)：禁止对象配置。
* Object.isSealed\(\)：判断一个对象是否可配置。
* Object.freeze\(\)：冻结一个对象。
* Object.isFrozen\(\)：判断一个对象是否被冻结。 （3）原型链相关方法
* Object.create\(\)：该方法可以指定原型对象和属性，返回一个新的对象。
* Object.getPrototypeOf\(\)：获取对象的Prototype对象。 Object 的实例方法 除了静态方法，还有不少方法定义在Object.prototype对象。它们称为实例方法，所有Object的实例对象都继承了这些方法。

Object实例对象的方法，主要有以下六个。

* Object.prototype.valueOf\(\)：返回当前对象对应的值。
* Object.prototype.toString\(\)：返回当前对象对应的字符串形式。
* Object.prototype.toLocaleString\(\)：返回当前对象对应的本地字符串形式。
* Object.prototype.hasOwnProperty\(\)：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
* Object.prototype.isPrototypeOf\(\)：判断当前对象是否为另一个对象的原型。
* Object.prototype.propertyIsEnumerable\(\)：判断某个属性是否可枚举

> Object.prototype.toString可以看出一个值到底是什么类型。

```text
Object.prototype.toString.call(2) // "[object Number]"
Object.prototype.toString.call('') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(Math) // "[object Math]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call([]) // "[object Array]"
```

> Object.prototype.hasOwnProperty\(\) Object.prototype.hasOwnProperty方法接受一个字符串作为参数，返回一个布尔值，表示该实例对象自身是否具有该属性。

```text
var obj = {
  p: 123
};

obj.hasOwnProperty('p') // true
obj.hasOwnProperty('toString') // false
```

上面代码中，对象obj自身具有p属性，所以返回true。toString属性是继承的，所以返回false。

### 属性描述对象

> 概述 JavaScript 提供了一个内部数据结构，用来描述对象的属性，控制它的行为，比如该属性是否可写、可遍历等等。这个内部数据结构称为“属性描述对象”（attributes object）。每个属性都有自己对应的属性描述对象，保存该属性的一些元信息。

下面是属性描述对象的一个例子。

```text
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```

属性描述对象提供6个元属性。

（1）value

value是该属性的属性值，默认为undefined。

（2）writable

writable是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为true。

（3）enumerable

enumerable是一个布尔值，表示该属性是否可遍历，默认为true。如果设为false，会使得某些操作（比如for...in循环、Object.keys\(\)）跳过该属性。

（4）configurable

configurable是一个布尔值，表示可配置性，默认为true。如果设为false，将阻止某些操作改写该属性，比如无法删除该属性，也不得改变该属性的属性描述对象（value属性除外）。也就是说，configurable属性控制了属性描述对象的可写性。

（5）get

get是一个函数，表示该属性的取值函数（getter），默认为undefined。

（6）set

set是一个函数，表示该属性的存值函数（setter），默认为undefined。

> Object.defineProperty\(\)，Object.defineProperties\(\) Object.defineProperty\(\)方法允许通过属性描述对象，定义或修改一个属性，然后返回修改后的对象，它的用法如下。

Object.defineProperty\(object, propertyName, attributesObject\) Object.defineProperty方法接受三个参数，依次如下。

object：属性所在的对象 propertyName：字符串，表示属性名 attributesObject：属性描述对象 举例来说，定义obj.p可以写成下面这样。

```text
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false
});

obj.p // 123

obj.p = 246;
obj.p // 123
```

上面代码中，Object.defineProperty\(\)方法定义了obj.p属性。由于属性描述对象的writable属性为false，所以obj.p属性不可写。注意，这里的Object.defineProperty方法的第一个参数是{}（一个新建的空对象），p属性直接定义在这个空对象上面，然后返回这个对象，这是Object.defineProperty\(\)的常见用法。

如果属性已经存在，Object.defineProperty\(\)方法相当于更新该属性的属性描述对象。

如果一次性定义或修改多个属性，可以使用Object.defineProperties\(\)方法。

```text
var obj = Object.defineProperties({}, {
  p1: { value: 123, enumerable: true },
  p2: { value: 'abc', enumerable: true },
  p3: { get: function () { return this.p1 + this.p2 },
    enumerable:true,
    configurable:true
  }
});

obj.p1 // 123
obj.p2 // "abc"
obj.p3 // "123abc"
```

上面代码中，Object.defineProperties\(\)同时定义了obj对象的三个属性。其中，p3属性定义了取值函数get，即每次读取该属性，都会调用这个取值函数。

注意，一旦定义了取值函数get（或存值函数set），就不能将writable属性设为true，或者同时定义value属性，否则会报错。

```text
var obj = {};

Object.defineProperty(obj, 'p', {
  value: 123,
  get: function() { return 456; }
});
// TypeError: Invalid property.
// A property cannot both have accessors and be writable or have a value

Object.defineProperty(obj, 'p', {
  writable: true,
  get: function() { return 456; }
});
// TypeError: Invalid property descriptor.
// Cannot both specify accessors and a value or writable attribute
```

上面代码中，同时定义了get属性和value属性，以及将writable属性设为true，就会报错。

Object.defineProperty\(\)和Object.defineProperties\(\)参数里面的属性描述对象，writable、configurable、enumerable这三个属性的默认值都为false。

```text
var obj = {};
Object.defineProperty(obj, 'foo', {});
Object.getOwnPropertyDescriptor(obj, 'foo')
// {
//   value: undefined,
//   writable: false,
//   enumerable: false,
//   configurable: false
// }
```

上面代码中，定义obj.foo时用了一个空的属性描述对象，就可以看到各个元属性的默认值。

#### 存取器

除了直接定义以外，属性还可以用存取器（accessor）定义。其中，存值函数称为setter，使用属性描述对象的set属性；取值函数称为getter，使用属性描述对象的get属性。

一旦对目标属性定义了存取器，那么存取的时候，都将执行对应的函数。利用这个功能，可以实现许多高级特性，比如某个属性禁止赋值。

```text
var obj = Object.defineProperty({}, 'p', {
  get: function () {
    return 'getter';
  },
  set: function (value) {
    console.log('setter: ' + value);
  }
});

obj.p // "getter"
obj.p = 123 // "setter: 123"
```

> 存取器往往用于，属性的值依赖对象内部数据的场合。

```text
var obj ={
  $n : 5,
  get next() { return this.$n++ },
  set next(n) {
    if (n >= this.$n) this.$n = n;
    else throw new Error('新的值必须大于当前值');
  }
};

obj.next // 5

obj.next = 10;
obj.next // 10

obj.next = 5;
// Uncaught Error: 新的值必须大于当前值
```

上面代码中，next属性的存值函数和取值函数，都依赖于内部属性$n。

> 对象的拷贝 有时，我们需要将一个对象的所有属性，拷贝到另一个对象，可以用下面的方法实现。

```text
var extend = function (to, from) {
  for (var property in from) {
    to[property] = from[property];
  }

  return to;
}

extend({}, {
  a: 1
})
// {a: 1}
```

上面这个方法的问题在于，如果遇到存取器定义的属性，会只拷贝值。

```text
extend({}, {
  get a() { return 1 }
})
// {a: 1}
为了解决这个问题，我们可以通过Object.defineProperty方法来拷贝属性。

var extend = function (to, from) {
  for (var property in from) {
    if (!from.hasOwnProperty(property)) continue;
    Object.defineProperty(
      to,
      property,
      Object.getOwnPropertyDescriptor(from, property)
    );
  }

  return to;
}

extend({}, { get a(){ return 1 } })
// { get a(){ return 1 } })
```

上面代码中，hasOwnProperty那一行用来过滤掉继承的属性，否则可能会报错，因为Object.getOwnPropertyDescriptor读不到继承属性的属性描述对象。

### 数组

> 静态方法 Array.isArray\(\) Array.isArray方法返回一个布尔值，表示参数是否为数组。它可以弥补typeof运算符的不足。

```text
var arr = [1, 2, 3];

typeof arr // "object"
Array.isArray(arr) // true
```

上面代码中，typeof运算符只能显示数组的类型是Object，而Array.isArray方法可以识别数组。

实例方法 valueOf\(\)，toString\(\) valueOf方法是一个所有对象都拥有的方法，表示对该对象求值。不同对象的valueOf方法不尽一致，数组的valueOf方法返回数组本身。

```text
var arr = [1, 2, 3];
arr.valueOf() // [1, 2, 3]
toString方法也是对象的通用方法，数组的toString方法返回数组的字符串形式。

var arr = [1, 2, 3];
arr.toString() // "1,2,3"

var arr = [1, 2, 3, [4, 5, 6]];
arr.toString() // "1,2,3,4,5,6"
```

对空数组使用pop方法，不会报错，而是返回undefined。

\[\].pop\(\) // undefined push和pop结合使用，就构成了“后进先出”的栈结构（stack）。

```text
var arr = [];
arr.push(1, 2);
arr.push(3);
arr.pop();
arr // [1, 2]
```

上面代码中，3是最后进入数组的，但是最早离开数组。

shift\(\)，unshift\(\) shift\(\)方法用于删除数组的第一个元素，并返回该元素。注意，该方法会改变原数组。

```text
var a = ['a', 'b', 'c'];

a.shift() // 'a'
a // ['b', 'c']
```

上面代码中，使用shift\(\)方法以后，原数组就变了。

shift\(\)方法可以遍历并清空一个数组。

```text
var list = [1, 2, 3, 4];
var item;

while (item = list.shift()) {
  console.log(item);
}

list // []
```

上面代码通过list.shift\(\)方法每次取出一个元素，从而遍历数组。它的前提是数组元素不能是0或任何布尔值等于false的元素，因此这样的遍历不是很可靠。

push\(\)和shift\(\)结合使用，就构成了“先进先出”的队列结构（queue）。

unshift\(\)方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。

```text
var a = ['a', 'b', 'c'];

a.unshift('x'); // 4
a // ['x', 'a', 'b', 'c']
```

unshift\(\)方法可以接受多个参数，这些参数都会添加到目标数组头部。

```text
var arr = [ 'c', 'd' ];
arr.unshift('a', 'b') // 4
arr // [ 'a', 'b', 'c', 'd' ]
```

> slice\(\) slice方法用于提取目标数组的一部分，返回一个新数组，原数组不变。

arr.slice\(start, end\); 它的第一个参数为起始位置（从0开始），第二个参数为终止位置（但该位置的元素本身不包括在内）。如果省略第二个参数，则一直返回到原数组的最后一个成员。

```text
var a = ['a', 'b', 'c'];

a.slice(0) // ["a", "b", "c"]
a.slice(1) // ["b", "c"]
a.slice(1, 2) // ["b"]
a.slice(2, 6) // ["c"]
a.slice() // ["a", "b", "c"]
```

上面代码中，最后一个例子slice没有参数，实际上等于返回一个原数组的拷贝。

如果slice方法的参数是负数，则表示倒数计算的位置。

```text
var a = ['a', 'b', 'c'];
a.slice(-2) // ["b", "c"]
a.slice(-2, -1) // ["b"]
```

上面代码中，-2表示倒数计算的第二个位置，-1表示倒数计算的第一个位置。

如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组。

```text
var a = ['a', 'b', 'c'];
a.slice(4) // []
a.slice(2, 1) // []
```

slice方法的一个重要应用，是将类似数组的对象转为真正的数组。

```text
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```

上面代码的参数都不是数组，但是通过call方法，在它们上面调用slice方法，就可以把它们转为真正的数组。

> splice\(\) splice方法用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。

arr.splice\(start, count, addElement1, addElement2, ...\); splice的第一个参数是删除的起始位置（从0开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。

```text
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2) // ["e", "f"]
a // ["a", "b", "c", "d"]
```

上面代码从原数组4号位置，删除了两个数组成员。

```text
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(4, 2, 1, 2) // ["e", "f"]
a // ["a", "b", "c", "d", 1, 2]
```

上面代码除了删除成员，还插入了两个新成员。

起始位置如果是负数，就表示从倒数位置开始删除。

```text
var a = ['a', 'b', 'c', 'd', 'e', 'f'];
a.splice(-4, 2) // ["c", "d"]
```

上面代码表示，从倒数第四个位置c开始删除两个成员。

如果只是单纯地插入元素，splice方法的第二个参数可以设为0。

```text
var a = [1, 1, 1];

a.splice(1, 0, 2) // []
a // [1, 2, 1, 1]
```

如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。

```text
var a = [1, 2, 3, 4];
a.splice(2) // [3, 4]
a // [1, 2]
```

> sort 如果想让sort方法按照自定义方式排序，可以传入一个函数作为参数。

```text
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]
```

上面代码中，sort的参数函数本身接受两个参数，表示进行比较的两个数组成员。如果该函数的返回值大于0，表示第一个成员排在第二个成员后面；其他情况下，都是第一个元素排在第二个元素前面。

```text
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
```

#### map\(\)

> map方法将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。 map方法接受一个函数作为参数。该函数调用时，map方法向它传入三个参数：当前成员、当前位置和数组本身。

```text
[1, 2, 3].map(function(elem, index, arr) {
  return elem * index;
});
// [0, 2, 6]
```

上面代码中，map方法的回调函数有三个参数，elem为当前成员的值，index为当前成员的位置，arr为原数组（\[1, 2, 3\]）。

map方法还可以接受第二个参数，用来绑定回调函数内部的this变量（详见《this 变量》一章）。

```text
var arr = ['a', 'b', 'c'];

[1, 2].map(function (e) {
  return this[e];
}, arr)
// ['b', 'c']
```

上面代码通过map方法的第二个参数，将回调函数内部的this对象，指向arr数组。

如果数组有空位，map方法的回调函数在这个位置不会执行，会跳过数组的空位。

```text
var f = function (n) { return 'a' };

[1, undefined, 2].map(f) // ["a", "a", "a"]
[1, null, 2].map(f) // ["a", "a", "a"]
[1, , 2].map(f) // ["a", , "a"]
```

上面代码中，map方法不会跳过undefined和null，但是会跳过空位。

#### forEach\(\)

> forEach方法与map方法很相似，也是对数组的所有成员依次执行参数函数。但是，forEach方法不返回值，只用来操作数据。这就是说，如果数组遍历的目的是为了得到返回值，那么使用map方法，否则使用forEach方法。

forEach的用法与map方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组。

```text
function log(element, index, array) {
  console.log('[' + index + '] = ' + element);
}

[2, 5, 9].forEach(log);
// [0] = 2
// [1] = 5
// [2] = 9
```

上面代码中，forEach遍历数组不是为了得到返回值，而是为了在屏幕输出内容，所以不必使用map方法。

forEach方法也可以接受第二个参数，绑定参数函数的this变量。

```text
var out = [];

[1, 2, 3].forEach(function(elem) {
  this.push(elem * elem);
}, out);

out // [1, 4, 9]
```

上面代码中，空数组out是forEach方法的第二个参数，结果，回调函数内部的this关键字就指向out。

注意，forEach方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用for循环。

```text
var arr = [1, 2, 3];

for (var i = 0; i < arr.length; i++) {
  if (arr[i] === 2) break;
  console.log(arr[i]);
}
// 1
```

上面代码中，执行到数组的第二个成员时，就会中断执行。forEach方法做不到这一点。

forEach方法也会跳过数组的空位。

```text
var log = function (n) {
  console.log(n + 1);
};

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
```

上面代码中，forEach方法不会跳过undefined和null，但会跳过空位。

#### filter\(\)

> filter方法用于过滤数组成员，满足条件的成员组成一个新数组返回。

它的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。该方法不会改变原数组。

```text
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]
```

上面代码将大于3的数组成员，作为一个新数组返回。

```text
var arr = [0, 1, 'a', false];

arr.filter(Boolean)
// [1, "a"]
```

上面代码中，filter方法返回数组arr里面所有布尔值为true的成员。

filter方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组。

```text
[1, 2, 3, 4, 5].filter(function (elem, index, arr) {
  return index % 2 === 0;
});
// [1, 3, 5]
```

上面代码返回偶数位置的成员组成的新数组。

filter方法还可以接受第二个参数，用来绑定参数函数内部的this变量。

```text
var obj = { MAX: 3 };
var myFilter = function (item) {
  if (item > this.MAX) return true;
};

var arr = [2, 8, 3, 4, 1, 3, 2, 9];
arr.filter(myFilter, obj) // [8, 4, 9]
```

上面代码中，过滤器myFilter内部有this变量，它可以被filter方法的第二个参数obj绑定，返回大于3的成员。

> some方法是只要一个成员的返回值是true，则整个some方法的返回值就是true，否则返回false。

```text
var arr = [1, 2, 3, 4, 5];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true
```

上面代码中，如果数组arr有一个成员大于等于3，some方法就返回true。

every方法是所有成员的返回值都是true，整个every方法才返回true，否则返回false。

```text
var arr = [1, 2, 3, 4, 5];
arr.every(function (elem, index, arr) {
  return elem >= 3;
});
// false
```

上面代码中，数组arr并非所有成员大于等于3，所以返回false。

注意，对于空数组，some方法返回false，every方法返回true，回调函数都不会执行。

```text
function isEven(x) { return x % 2 === 0 }

[].some(isEven) // false
[].every(isEven) // true
```

some和every方法还可以接受第二个参数，用来绑定参数函数内部的this变量。

#### reduce\(\)，reduceRight\(\)

> reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。它们的差别是，reduce是从左到右处理（从第一个成员到最后一个成员），reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。

```text
[1, 2, 3, 4, 5].reduce(function (a, b) {
  console.log(a, b);
  return a + b;
})
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
```

上面代码中，reduce方法求出数组所有成员的和。第一次执行，a是数组的第一个成员1，b是数组的第二个成员2。第二次执行，a为上一轮的返回值3，b为第三个成员3。第三次执行，a为上一轮的返回值6，b为第四个成员4。第四次执行，a为上一轮返回值10，b为第五个成员5。至此所有成员遍历完成，整个方法的返回值就是最后一轮的返回值15。

reduce方法和reduceRight方法的第一个参数都是一个函数。该函数接受以下四个参数。

累积变量，默认为数组的第一个成员 当前变量，默认为数组的第二个成员 当前位置（从0开始） 原数组 这四个参数之中，只有前两个是必须的，后两个则是可选的。

如果要对累积变量指定初值，可以把它放在reduce方法和reduceRight方法的第二个参数。

```text
[1, 2, 3, 4, 5].reduce(function (a, b) {
  return a + b;
}, 10);
// 25
```

上面代码指定参数a的初值为10，所以数组从10开始累加，最终结果为25。注意，这时b是从数组的第一个成员开始遍历。

上面的第二个参数相当于设定了默认值，处理空数组时尤其有用。

```text
function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
```

上面代码中，由于空数组取不到初始值，reduce方法会报错。这时，加上第二个参数，就能保证总是会返回一个值。

下面是一个reduceRight方法的例子。

```text
function subtract(prev, cur) {
  return prev - cur;
}

[3, 2, 1].reduce(subtract) // 0
[3, 2, 1].reduceRight(subtract) // -4
```

上面代码中，reduce方法相当于3减去2再减去1，reduceRight方法相当于1减去2再减去3。

由于这两个方法会遍历数组，所以实际上还可以用来做一些遍历相关的操作。比如，找出字符长度最长的数组成员。

```text
function findLongest(entries) {
  return entries.reduce(function (longest, entry) {
    return entry.length > longest.length ? entry : longest;
  }, '');
}

findLongest(['aaa', 'bb', 'c']) // "aaa"
```

上面代码中，reduce的参数函数会将字符长度较长的那个数组成员，作为累积值。这导致遍历所有成员之后，累积值就是字符长度最长的那个成员。

#### indexOf\(\)，lastIndexOf\(\)

> indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。

```text
var a = ['a', 'b', 'c'];

a.indexOf('b') // 1
a.indexOf('y') // -1
```

indexOf方法还可以接受第二个参数，表示搜索的开始位置。

\['a', 'b', 'c'\].indexOf\('a', 1\) // -1 上面代码从1号位置开始搜索字符a，结果为-1，表示没有搜索到。

lastIndexOf方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。

```text
var a = [2, 5, 9, 2];
a.lastIndexOf(2) // 3
a.lastIndexOf(7) // -1
```

注意，这两个方法不能用来搜索NaN的位置，即它们无法确定数组成员是否包含NaN。

```text
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```

这是因为这两个方法内部，使用严格相等运算符（===）进行比较，而NaN是唯一一个不等于自身的值。

#### 包装对象

定义 对象是 JavaScript 语言最主要的数据类型，三种原始类型的值——数值、字符串、布尔值——在一定条件下，也会自动转为对象，也就是原始类型的“包装对象”（wrapper）。

所谓“包装对象”，指的是与数值、字符串、布尔值分别相对应的Number、String、Boolean三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。

```text
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);

typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```

上面代码中，基于原始类型的值，生成了三个对应的包装对象。可以看到，v1、v2、v3都是对象，且与对应的简单类型值不相等。

包装对象的设计目的，首先是使得“对象”这种类型可以覆盖 JavaScript 所有的值，整门语言有一个通用的数据模型，其次是使得原始类型的值也有办法调用自己的方法。

Number、String和Boolean这三个原生对象，如果不作为构造函数调用（即调用时不加new），而是作为普通函数调用，常常用于将任意类型的值转为数值、字符串和布尔值。

```text
// 字符串转为数值
Number('123') // 123

// 数值转为字符串
String(123) // "123"

// 数值转为布尔值
Boolean(123) // true
```

上面这种数据类型的转换，详见《数据类型转换》一节。

总结一下，这三个对象作为构造函数使用（带有new）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有new），可以将任意类型的值，转为原始类型的值。

#### Boolean 对象

Boolean对象是 JavaScript 的三个包装对象之一。作为构造函数，它主要用于生成布尔值的包装对象实例。

```text
var b = new Boolean(true);

typeof b // "object"
b.valueOf() // true
```

上面代码的变量b是一个Boolean对象的实例，它的类型是对象，值为布尔值true。

注意，false对应的包装对象实例，布尔运算结果也是true。

```text
if (new Boolean(false)) {
  console.log('true');
} // true

if (new Boolean(false).valueOf()) {
  console.log('true');
} // 无输出
```

上面代码的第一个例子之所以得到true，是因为false对应的包装对象实例是一个对象，进行逻辑运算时，被自动转化成布尔值true（因为所有对象对应的布尔值都是true）。而实例的valueOf方法，则返回实例对应的原始值，本例为false。

#### Number.prototype.toString\(\)

Number对象部署了自己的toString方法，用来将一个数值转为字符串形式。

\(10\).toString\(\) // "10" toString方法可以接受一个参数，表示输出的进制。如果省略这个参数，默认将数值先转为十进制，再输出字符串；否则，就根据参数指定的进制，将一个数字转化成某个进制的字符串。

```text
(10).toString(2) // "1010"
(10).toString(8) // "12"
(10).toString(16) // "a"
```

上面代码中，10一定要放在括号里，这样表明后面的点表示调用对象属性。如果不加括号，这个点会被 JavaScript 引擎解释成小数点，从而报错。

```text
10.toString(2)
// SyntaxError: Unexpected token ILLEGAL
```

只要能够让 JavaScript 引擎不混淆小数点和对象的点运算符，各种写法都能用。除了为10加上括号，还可以在10后面加两个点，JavaScript 会把第一个点理解成小数点（即10.0），把第二个点理解成调用对象属性，从而得到正确结果。

```text
10..toString(2)
// "1010"
```

// 其他方法还包括

```text
10 .toString(2) // "1010"
10.0.toString(2) // "1010"
```

这实际上意味着，可以直接对一个小数使用toString方法。

```text
10.5.toString() // "10.5"
10.5.toString(2) // "1010.1"
10.5.toString(8) // "12.4"
10.5.toString(16) // "a.8"
```

通过方括号运算符也可以调用toString方法。

```text
10['toString'](d0c7b5d39604d2b2b1e94fe62f5b6e05) // "1010"
```

toString方法只能将十进制的数，转为其他进制的字符串。如果要将其他进制的数，转回十进制，需要使用parseInt方法。

> Number.prototype.toFixed\(\) toFixed\(\)方法先将一个数转为指定位数的小数，然后返回这个小数对应的字符串。

```text
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01"
```

上面代码中，10和10.005先转成2位小数，然后转成字符串。其中10必须放在括号里，否则后面的点会被处理成小数点。

toFixed\(\)方法的参数为小数位数，有效范围为0到20，超出这个范围将抛出 RangeError 错误。

由于浮点数的原因，小数5的四舍五入是不确定的，使用的时候必须小心。

```text
(10.055).toFixed(2) // 10.05
(10.005).toFixed(2) // 10.01
```

> 与其他对象一样，Number.prototype对象上面可以自定义方法，被Number的实例继承。

```text
Number.prototype.add = function (x) {
  return this + x;
};

8['add'](d0c7b5d39604d2b2b1e94fe62f5b6e05) // 10
```

上面代码为Number对象实例定义了一个add方法。在数值上调用某个方法，数值会自动转为Number的实例对象，所以就可以调用add方法了。由于add方法返回的还是数值，所以可以链式运算。

```text
Number.prototype.subtract = function (x) {
  return this - x;
};

(8).add(2).subtract(4)
// 6
```

上面代码在Number对象的实例上部署了subtract方法，它可以与add方法链式调用。

我们还可以部署更复杂的方法。

```text
Number.prototype.iterate = function () {
  var result = [];
  for (var i = 0; i <= this; i++) {
    result.push(i);
  }
  return result;
};

(8).iterate()
// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

上面代码在Number对象的原型上部署了iterate方法，将一个数值自动遍历为一个数组。

注意，数值的自定义方法，只能定义在它的原型对象Number.prototype上面，数值本身是无法自定义属性的。

```text
var n = 1;
n.x = 1;
n.x // undefined
```

上面代码中，n是一个原始类型的数值。直接在它上面新增一个属性x，不会报错，但毫无作用，总是返回undefined。这是因为一旦被调用属性，n就自动转为Number的实例对象，调用结束后，该对象自动销毁。所以，下一次调用n的属性时，实际取到的是另一个对象，属性x当然就读不出来。

### String 对象

String对象是 JavaScript 原生提供的三个包装对象之一，用来生成字符串对象。

```text
var s1 = 'abc';
var s2 = new String('abc');

typeof s1 // "string"
typeof s2 // "object"

s2.valueOf() // "abc"
```

上面代码中，变量s1是字符串，s2是对象。由于s2是字符串对象，s2.valueOf方法返回的就是它所对应的原始字符串。

字符串对象是一个类似数组的对象（很像数组，但不是数组）。

```text
new String('abc')
// String {0: "a", 1: "b", 2: "c", length: 3}

(new String('abc'))[1] // "b"
```

上面代码中，字符串abc对应的字符串对象，有数值键（0、1、2）和length属性，所以可以像数组那样取值。

除了用作构造函数，String对象还可以当作工具方法使用，将任意类型的值转为字符串。

```text
String(true) // "true"
String(5) // "5"
```

上面代码将布尔值true和数值5，分别转换为字符串。

> 静态方法 String.fromCharCode\(\) String对象提供的静态方法（即定义在对象本身，而不是定义在对象实例的方法），主要是String.fromCharCode\(\)。该方法的参数是一个或多个数值，代表 Unicode 码点，返回值是这些码点组成的字符串。

```text
String.fromCharCode() // ""
String.fromCharCode(97) // "a"
String.fromCharCode(104, 101, 108, 108, 111)
// "hello"
```

上面代码中，String.fromCharCode方法的参数为空，就返回空字符串；否则，返回参数对应的 Unicode 字符串。

注意，该方法不支持 Unicode 码点大于0xFFFF的字符，即传入的参数不能大于0xFFFF（即十进制的 65535）。

```text
String.fromCharCode(0x20BB7)
// "ஷ"
String.fromCharCode(0x20BB7) === String.fromCharCode(0x0BB7)
// true
```

上面代码中，String.fromCharCode参数0x20BB7大于0xFFFF，导致返回结果出错。0x20BB7对应的字符是汉字𠮷，但是返回结果却是另一个字符（码点0x0BB7）。这是因为String.fromCharCode发现参数值大于0xFFFF，就会忽略多出的位（即忽略0x20BB7里面的2）。

这种现象的根本原因在于，码点大于0xFFFF的字符占用四个字节，而 JavaScript 默认支持两个字节的字符。这种情况下，必须把0x20BB7拆成两个字符表示。

```text
String.fromCharCode(0xD842, 0xDFB7)
// "𠮷"
```

上面代码中，0x20BB7拆成两个字符0xD842和0xDFB7（即两个两字节字符，合成一个四字节字符），就能得到正确的结果。码点大于0xFFFF的字符的四字节表示法，由 UTF-16 编码方法决定

> 实例方法 String.prototype.charAt\(\) charAt方法返回指定位置的字符，参数是从0开始编号的位置。

```text
var s = new String('abc');

s.charAt(1) // "b"
s.charAt(s.length - 1) // "c"
```

这个方法完全可以用数组下标替代。

```text
'abc'.charAt(1) // "b"
'abc'[1] // "b"
```

如果参数为负数，或大于等于字符串的长度，charAt返回空字符串。

```text
'abc'.charAt(-1) // ""
'abc'.charAt(3) // ""
String.prototype.charCodeAt()
```

charCodeAt方法返回字符串指定位置的 Unicode 码点（十进制表示），相当于String.fromCharCode\(\)的逆操作。

'abc'.charCodeAt\(1\) // 98 上面代码中，abc的1号位置的字符是b，它的 Unicode 码点是98。

如果没有任何参数，charCodeAt返回首字符的 Unicode 码点。

'abc'.charCodeAt\(\) // 97 如果参数为负数，或大于等于字符串的长度，charCodeAt返回NaN。

```text
'abc'.charCodeAt(-1) // NaN
'abc'.charCodeAt(4) // NaN
```

注意，charCodeAt方法返回的 Unicode 码点不会大于65536（0xFFFF），也就是说，只返回两个字节的字符的码点。如果遇到码点大于 65536 的字符（四个字节的字符），必需连续使用两次charCodeAt，不仅读入charCodeAt\(i\)，还要读入charCodeAt\(i+1\)，将两个值放在一起，才能得到准确的字符。

> String.prototype.substr\(\) substr方法用于从原字符串取出子字符串并返回，不改变原字符串，跟slice和substring方法的作用相同。

* substr方法的第一个参数是子字符串的开始位置（从0开始计算），第二个参数是子字符串的长度。

```text
'JavaScript'.substr(4, 6) // "Script"
```

如果省略第二个参数，则表示子字符串一直到原字符串的结束。

```text
'JavaScript'.substr(4) // "Script"
```

如果第一个参数是负数，表示倒数计算的字符位置。如果第二个参数是负数，将被自动转为0，因此会返回空字符串。

```text
'JavaScript'.substr(-6) // "Script"
'JavaScript'.substr(4, -1) // ""
```

上面代码中，第二个例子的参数-1自动转为0，表示子字符串长度为0，所以返回空字符串。

> String.prototype.indexOf\(\)，String.prototype.lastIndexOf\(\) indexOf方法用于确定一个字符串在另一个字符串中第一次出现的位置，返回结果是匹配开始的位置。如果返回-1，就表示不匹配。

```text
'hello world'.indexOf('o') // 4
'JavaScript'.indexOf('script') // -1
```

indexOf方法还可以接受第二个参数，表示从该位置开始向后匹配。

```text
'hello world'.indexOf('o', 6) // 7
```

lastIndexOf方法的用法跟indexOf方法一致，主要的区别是lastIndexOf从尾部开始匹配，indexOf则是从头部开始匹配。

```text
'hello world'.lastIndexOf('o') // 7
```

另外，lastIndexOf的第二个参数表示从该位置起向前匹配。

```text
'hello world'.lastIndexOf('o', 6) // 4
```

> String.prototype.search\(\)，String.prototype.replace\(\) search方法的用法基本等同于match，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回-1。

```text
'cat, bat, sat, fat'.search('at') // 1
```

search方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

replace方法用于替换匹配的子字符串，一般情况下只替换第一个匹配（除非使用带有g修饰符的正则表达式）。

```text
'aaa'.replace('a', 'b') // "baa"
```

replace方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

String.prototype.split\(\) split方法按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组。

```text
'a|b|c'.split('|') // ["a", "b", "c"]
```

如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。

```text
'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
```

如果省略参数，则返回数组的唯一成员就是原字符串。

```text
'a|b|c'.split() // ["a|b|c"]
```

如果满足分割规则的两个部分紧邻着（即两个分割符中间没有其他字符），则返回数组之中会有一个空字符串。

```text
'a||c'.split('|') // ['a', '', 'c']
```

如果满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串。

```text
'|b|c'.split('|') // ["", "b", "c"]
'a|b|'.split('|') // ["a", "b", ""]
```

split方法还可以接受第二个参数，限定返回数组的最大成员数。

```text
'a|b|c'.split('|', 0) // []
'a|b|c'.split('|', 1) // ["a"]
'a|b|c'.split('|', 2) // ["a", "b"]
'a|b|c'.split('|', 3) // ["a", "b", "c"]
'a|b|c'.split('|', 4) // ["a", "b", "c"]
```

上面代码中，split方法的第二个参数，决定了返回数组的成员数。

### Math

Math对象提供以下一些静态方法。

* Math.abs\(\)：绝对值
* Math.ceil\(\)：向上取整
* Math.floor\(\)：向下取整
* Math.max\(\)：最大值
* Math.min\(\)：最小值
* Math.pow\(\)：指数运算
* Math.sqrt\(\)：平方根
* Math.log\(\)：自然对数
* Math.exp\(\)：e的指数
* Math.round\(\)：四舍五入
* Math.random\(\)：随机数
* 
### Math.random\(\)

Math.random\(\)返回0到1之间的一个伪随机数，可能等于0，但是一定小于1。

```text
Math.random() // 0.7151307314634323
```

任意范围的随机数生成函数如下。

```text
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

getRandomArbitrary(1.5, 6.5)
// 2.4942810038223864
```

任意范围的随机整数生成函数如下。

```text
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

getRandomInt(1, 6) // 5
```

返回随机字符的例子如下。

```text
function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i = 0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"
```

上面代码中，random\_str函数接受一个整数作为参数，返回变量ALPHABET内的随机字符所组成的指定长度的字符串。

#### Date

Date实例有一个独特的地方。其他对象求值的时候，都是默认调用.valueOf\(\)方法，但是Date实例求值的时候，默认调用的是toString\(\)方法。这导致对Date实例求值，返回的是一个字符串，代表该实例对应的时间。

```text
var today = new Date();

today
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"

// 等同于
today.toString()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

上面代码中，today是Date的实例，直接求值等同于调用toString方法。

作为构造函数时，Date对象可以接受多种格式的参数，返回一个该参数对应的时间实例。

```text
// 参数为时间零点开始计算的毫秒数
new Date(1378218728000)
// Tue Sep 03 2013 22:32:08 GMT+0800 (CST)

// 参数为日期字符串
new Date('January 6, 2013');
// Sun Jan 06 2013 00:00:00 GMT+0800 (CST)

// 参数为多个整数，
// 代表年、月、日、小时、分钟、秒、毫秒
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```

关于Date构造函数的参数，有几点说明。

第一点，参数可以是负整数，代表1970年元旦之前的时间。

```text
new Date(-1378218728000)
// Fri Apr 30 1926 17:27:52 GMT+0800 (CST)
```

第二点，只要是能被Date.parse\(\)方法解析的字符串，都可以当作参数。

```text
new Date('2013-2-15')
new Date('2013/2/15')
new Date('02/15/2013')
new Date('2013-FEB-15')
new Date('FEB, 15, 2013')
new Date('FEB 15, 2013')
new Date('February, 15, 2013')
new Date('February 15, 2013')
new Date('15 Feb 2013')
new Date('15, February, 2013')
// Fri Feb 15 2013 00:00:00 GMT+0800 (CST)
```

上面多种日期字符串的写法，返回的都是同一个时间。

第三，参数为年、月、日等多个整数时，年和月是不能省略的，其他参数都可以省略的。也就是说，这时至少需要两个参数，因为如果只使用“年”这一个参数，Date会将其解释为毫秒数。

```text
new Date(2013)
// Thu Jan 01 1970 08:00:02 GMT+0800 (CST)
```

上面代码中，2013被解释为毫秒数，而不是年份。

```text
new Date(2013, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 1, 0, 0, 0, 0)
// Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
```

上面代码中，不管有几个参数，返回的都是2013年1月1日零点。

最后，各个参数的取值范围如下。

年：使用四位数年份，比如2000。如果写成两位数或个位数，则加上1900，即10代表1910年。如果是负数，表示公元前。 月：0表示一月，依次类推，11表示12月。 日：1到31。 小时：0到23。 分钟：0到59。 秒：0到59 毫秒：0到999。 注意，月份从0开始计算，但是，天数从1开始计算。另外，除了日期的默认值为1，小时、分钟、秒钟和毫秒的默认值都是0。

这些参数如果超出了正常范围，会被自动折算。比如，如果月设为15，就折算为下一年的4月。

```text
new Date(2013, 15)
// Tue Apr 01 2014 00:00:00 GMT+0800 (CST)
new Date(2013, 0, 0)
// Mon Dec 31 2012 00:00:00 GMT+0800 (CST)
```

上面代码的第二个例子，日期设为0，就代表上个月的最后一天。

参数还可以使用负数，表示扣去的时间。

```text
new Date(2013, -1)
// Sat Dec 01 2012 00:00:00 GMT+0800 (CST)
new Date(2013, 0, -1)
// Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
```

上面代码中，分别对月和日使用了负数，表示从基准日扣去相应的时间。

日期的运算 类型自动转换时，Date实例如果转为数值，则等于对应的毫秒数；如果转为字符串，则等于对应的日期字符串。所以，两个日期实例对象进行减法运算时，返回的是它们间隔的毫秒数；进行加法运算时，返回的是两个字符串连接而成的新字符串。

```text
var d1 = new Date(2000, 2, 1);
var d2 = new Date(2000, 3, 1);

d2 - d1
// 2678400000
d2 + d1
// "Sat Apr 01 2000 00:00:00 GMT+0800 (CST)Wed Mar 01 2000 00:00:00 GMT+0800 (CST)"
```

静态方法 Date.now\(\) Date.now方法返回当前时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数，相当于 Unix 时间戳乘以1000。

```text
Date.now() // 1364026285194
Date.parse()
```

Date.parse方法用来解析日期字符串，返回该时间距离时间零点（1970年1月1日 00:00:00）的毫秒数。

日期字符串应该符合 RFC 2822 和 ISO 8061 这两个标准，即YYYY-MM-DDTHH:mm:ss.sssZ格式，其中最后的Z表示时区。但是，其他格式也可以被解析，请看下面的例子。

```text
Date.parse('Aug 9, 1995')
Date.parse('January 26, 2011 13:51:50')
Date.parse('Mon, 25 Dec 1995 13:30:00 GMT')
Date.parse('Mon, 25 Dec 1995 13:30:00 +0430')
Date.parse('2011-10-10')
Date.parse('2011-10-10T14:48:00')
```

上面的日期字符串都可以解析。

如果解析失败，返回NaN。

```text
Date.parse('xxx') // NaN
```

> Date.UTC\(\) Date.UTC方法接受年、月、日等变量作为参数，返回该时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数。

```text
// 格式
Date.UTC(year, month[, date[, hrs[, min[, sec[, ms]]]]])
// 用法
Date.UTC(2011, 0, 1, 2, 3, 4, 567)
// 1293847384567
```

该方法的参数用法与Date构造函数完全一致，比如月从0开始计算，日期从1开始计算。区别在于Date.UTC方法的参数，会被解释为 UTC 时间（世界标准时间），Date构造函数的参数会被解释为当前时区的时间。

> get 类方法 Date对象提供了一系列get\*方法，用来获取实例对象某个方面的值。

* getTime\(\)：返回实例距离1970年1月1日00:00:00的毫秒数，等同于valueOf方法。
* getDate\(\)：返回实例对象对应每个月的几号（从1开始）。
* getDay\(\)：返回星期几，星期日为0，星期一为1，以此类推。
* getFullYear\(\)：返回四位的年份。
* getMonth\(\)：返回月份（0表示1月，11表示12月）。
* getHours\(\)：返回小时（0-23）。
* getMilliseconds\(\)：返回毫秒（0-999）。
* getMinutes\(\)：返回分钟（0-59）。
* getSeconds\(\)：返回秒（0-59）。
* getTimezoneOffset\(\)：返回当前时间与 UTC 的时区差异，以分钟表示，返回结果考虑到了夏令时因素。 所有这些get\*方法返回的都是整数，不同方法返回值的范围不一样。

分钟和秒：0 到 59 小时：0 到 23 星期：0（星期天）到 6（星期六） 日期：1 到 31 月份：0（一月）到 11（十二月）

```text
var d = new Date('January 6, 2013');

d.getDate() // 6
d.getMonth() // 0
d.getFullYear() // 2013
d.getTimezoneOffset() // -480
```

上面代码中，最后一行返回-480，即 UTC 时间减去当前时间，单位是分钟。-480表示 UTC 比当前时间少480分钟，即当前时区比 UTC 早8个小时。

下面是一个例子，计算本年度还剩下多少天。

```text
function leftDays() {
  var today = new Date();
  var endYear = new Date(today.getFullYear(), 11, 31, 23, 59, 59, 999);
  var msPerDay = 24 * 60 * 60 * 1000;
  return Math.round((endYear.getTime() - today.getTime()) / msPerDay);
}
```

#### RegExp 对象

新建正则表达式有两种方法。一种是使用字面量，以斜杠表示开始和结束。

```text
var regex = /xyz/;
```

另一种是使用RegExp构造函数。

```text
var regex = new RegExp('xyz');
```

上面两种写法是等价的，都新建了一个内容为xyz的正则表达式对象。它们的主要区别是，第一种方法在引擎编译代码时，就会新建正则表达式，第二种方法在运行时新建正则表达式，所以前者的效率较高。而且，前者比较便利和直观，所以实际应用中，基本上都采用字面量定义正则表达式。

RegExp构造函数还可以接受第二个参数，表示修饰符（详细解释见下文）。

```text
var regex = new RegExp('xyz', 'i');
// 等价于
var regex = /xyz/i;
```

上面代码中，正则表达式/xyz/有一个修饰符i。

实例属性 正则对象的实例属性分成两类。

一类是修饰符相关，用于了解设置了什么修饰符。

* RegExp.prototype.ignoreCase：返回一个布尔值，表示是否设置了i修饰符。
* RegExp.prototype.global：返回一个布尔值，表示是否设置了g修饰符。
* RegExp.prototype.multiline：返回一个布尔值，表示是否设置了m修饰符。
* RegExp.prototype.flags：返回一个字符串，包含了已经设置的所有修饰符，按字母排序。 上面四个属性都是只读的。

```text
var r = /abc/igm;

r.ignoreCase // true
r.global // true
r.multiline // true
r.flags // 'gim'
```

另一类是与修饰符无关的属性，主要是下面两个。

RegExp.prototype.lastIndex：返回一个整数，表示下一次开始搜索的位置。该属性可读写，但是只在进行连续搜索时有意义，详细介绍请看后文。 RegExp.prototype.source：返回正则表达式的字符串形式（不包括反斜杠），该属性只读。

```text
var r = /abc/igm;

r.lastIndex // 0
r.source // "abc"
```

> 实例方法 RegExp.prototype.test\(\) 正则实例对象的test方法返回一个布尔值，表示当前模式是否能匹配参数字符串。

```text
/cat/.test('cats and dogs') // true
```

上面代码验证参数字符串之中是否包含cat，结果返回true。

如果正则表达式带有g修饰符，则每一次test方法都从上一次结束的位置开始向后匹配。

```text
var r = /x/g;
var s = '_x_x';

r.lastIndex // 0
r.test(s) // true

r.lastIndex // 2
r.test(s) // true

r.lastIndex // 4
r.test(s) // false
```

上面代码的正则表达式使用了g修饰符，表示是全局搜索，会有多个结果。接着，三次使用test方法，每一次开始搜索的位置都是上一次匹配的后一个位置。

带有g修饰符时，可以通过正则对象的lastIndex属性指定开始搜索的位置。

```text
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false

r.lastIndex // 0
r.test(s)
```

上面代码指定从字符串的第五个位置开始搜索，这个位置为空，所以返回false。同时，lastIndex属性重置为0，所以第二次执行r.test\(s\)会返回true。

注意，带有g修饰符时，正则表达式内部会记住上一次的lastIndex属性，这时不应该更换所要匹配的字符串，否则会有一些难以察觉的错误。

```text
var r = /bb/g;
r.test('bb') // true
r.test('-bb-') // false
```

上面代码中，由于正则表达式r是从上一次的lastIndex位置开始匹配，导致第二次执行test方法时出现预期以外的结果。

lastIndex属性只对同一个正则表达式有效，所以下面这样写是错误的。

```text
var count = 0;
while (/a/g.test('babaa')) count++;
```

上面代码会导致无限循环，因为while循环的每次匹配条件都是一个新的正则表达式，导致lastIndex属性总是等于0。

如果正则模式是一个空字符串，则匹配所有字符串。

```text
new RegExp('').test('abc')
// true
```

> RegExp.prototype.exec\(\) 正则实例对象的exec方法，用来返回匹配结果。如果发现匹配，就返回一个数组，成员是匹配成功的子字符串，否则返回null。

```text
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

r1.exec(s) // ["x"]
r2.exec(s) // null
```

上面代码中，正则对象r1匹配成功，返回一个数组，成员是匹配结果；正则对象r2匹配失败，返回null。

如果正则表示式包含圆括号（即含有“组匹配”），则返回的数组会包括多个成员。第一个成员是整个匹配成功的结果，后面的成员就是圆括号对应的匹配成功的组。也就是说，第二个成员对应第一个括号，第三个成员对应第二个括号，以此类推。整个数组的length属性等于组匹配的数量再加1。

```text
var s = '_x_x';
var r = /_(x)/;

r.exec(s) // ["_x", "x"]
```

上面代码的exec方法，返回一个数组。第一个成员是整个匹配的结果，第二个成员是圆括号匹配的结果。

exec方法的返回数组还包含以下两个属性：

input：整个原字符串。 index：整个模式匹配成功的开始位置（从0开始计数）。

```text
var r = /a(b+)a/;
var arr = r.exec('_abbba_aba_');

arr // ["abbba", "bbb"]

arr.index // 1
arr.input // "_abbba_aba_"
```

上面代码中的index属性等于1，是因为从原字符串的第二个位置开始匹配成功。

如果正则表达式加上g修饰符，则可以使用多次exec方法，下一次搜索的位置从上一次匹配成功结束的位置开始。

```text
var reg = /a/g;
var str = 'abc_abc_abc'

var r1 = reg.exec(str);
r1 // ["a"]
r1.index // 0
reg.lastIndex // 1

var r2 = reg.exec(str);
r2 // ["a"]
r2.index // 4
reg.lastIndex // 5

var r3 = reg.exec(str);
r3 // ["a"]
r3.index // 8
reg.lastIndex // 9

var r4 = reg.exec(str);
r4 // null
reg.lastIndex // 0
```

上面代码连续用了四次exec方法，前三次都是从上一次匹配结束的位置向后匹配。当第三次匹配结束以后，整个字符串已经到达尾部，匹配结果返回null，正则实例对象的lastIndex属性也重置为0，意味着第四次匹配将从头开始。

利用g修饰符允许多次匹配的特点，可以用一个循环完成全部匹配。

```text
var reg = /a/g;
var str = 'abc_abc_abc'

while(true) {
  var match = reg.exec(str);
  if (!match) break;
  console.log('#' + match.index + ':' + match[0]);
}
// #0:a
// #4:a
// #8:a
```

上面代码中，只要exec方法不返回null，就会一直循环下去，每次输出匹配的位置和匹配的文本。

正则实例对象的lastIndex属性不仅可读，还可写。设置了g修饰符的时候，只要手动设置了lastIndex的值，就会从指定位置开始匹配。

字符串的实例方法 字符串的实例方法之中，有4种与正则表达式有关。

* String.prototype.match\(\)：返回一个数组，成员是所有匹配的子字符串。

  String.prototype.search\(\)：按照给定的正则表达式进行搜索，返回一个整数，表示匹配开始的位置。

  String.prototype.replace\(\)：按照给定的正则表达式进行替换，返回替换后的字符串。

  String.prototype.split\(\)：按照给定规则进行字符串分割，返回一个数组，包含分割后的各个成员。

  String.prototype.match\(\)

  字符串实例对象的match方法对字符串进行正则匹配，返回匹配结果。

```text
var s = '_x_x';
var r1 = /x/;
var r2 = /y/;

s.match(r1) // ["x"]
s.match(r2) // null
```

从上面代码可以看到，字符串的match方法与正则对象的exec方法非常类似：匹配成功返回一个数组，匹配失败返回null。

如果正则表达式带有g修饰符，则该方法与正则对象的exec方法行为不同，会一次性返回所有匹配成功的结果。

```text
var s = 'abba';
var r = /a/g;

s.match(r) // ["a", "a"]
r.exec(s) // ["a"]
```

设置正则表达式的lastIndex属性，对match方法无效，匹配总是从字符串的第一个字符开始。

```text
var r = /a|b/g;
r.lastIndex = 7;
'xaxb'.match(r) // ['a', 'b']
r.lastIndex // 0
```

上面代码表示，设置正则对象的lastIndex属性是无效的。

String.prototype.search\(\) 字符串对象的search方法，返回第一个满足条件的匹配结果在整个字符串中的位置。如果没有任何匹配，则返回-1。

```text
'_x_x'.search(/x/)
// 1
```

上面代码中，第一个匹配结果出现在字符串的1号位置。

String.prototype.replace\(\) 字符串对象的replace方法可以替换匹配的值。它接受两个参数，第一个是正则表达式，表示搜索模式，第二个是替换的内容。

str.replace\(search, replacement\) 正则表达式如果不加g修饰符，就替换第一个匹配成功的值，否则替换所有匹配成功的值。

```text
'aaa'.replace('a', 'b') // "baa"
'aaa'.replace(/a/, 'b') // "baa"
'aaa'.replace(/a/g, 'b') // "bbb"
```

上面代码中，最后一个正则表达式使用了g修饰符，导致所有的b都被替换掉了。

replace方法的一个应用，就是消除字符串首尾两端的空格。

```text
var str = '  #id div.class  ';

str.replace(/^\s+|\s+$/g, '')
// "#id div.class"
```

replace方法的第二个参数可以使用美元符号$，用来指代所替换的内容。

$&：匹配的子字符串。 $\`：匹配结果前面的文本。 $'：匹配结果后面的文本。 $n：匹配成功的第n组内容，n是从1开始的自然数。 $$：指代美元符号$。

```text
'hello world'.replace(/(\w+)\s(\w+)/, '$2 $1')
// "world hello"

'abc'.replace('b', '[$`-$&-$\']')
// "a[a-b-c]c"
```

上面代码中，第一个例子是将匹配的组互换位置，第二个例子是改写匹配的值。

replace方法的第二个参数还可以是一个函数，将每一个匹配内容替换为函数返回值。

```text
'3 and 5'.replace(/[0-9]+/g, function (match) {
  return 2 * match;
})
// "6 and 10"

var a = 'The quick brown fox jumped over the lazy dog.';
var pattern = /quick|brown|lazy/ig;

a.replace(pattern, function replacer(match) {
  return match.toUpperCase();
});
```

// The QUICK BROWN fox jumped over the LAZY dog. 作为replace方法第二个参数的替换函数，可以接受多个参数。其中，第一个参数是捕捉到的内容，第二个参数是捕捉到的组匹配（有多少个组匹配，就有多少个对应的参数）。此外，最后还可以添加两个参数，倒数第二个参数是捕捉到的内容在整个字符串中的位置（比如从第五个位置开始），最后一个参数是原字符串。下面是一个网页模板替换的例子。

```text
var prices = {
  'p1': '$1.99',
  'p2': '$9.99',
  'p3': '$5.00'
};

var template = '<span id="p1"></span>'
  + '<span id="p2"></span>'
  + '<span id="p3"></span>';

template.replace(
  /(<span id=")(.*?)(">)(<\/span>)/g,
  function(match, $1, $2, $3, $4){
    return $1 + $2 + $3 + prices[$2] + $4;
  }
);
// "<span id="p1">$1.99</span><span id="p2">$9.99</span><span id="p3">$5.00</span>"
```

上面代码的捕捉模式中，有四个括号，所以会产生四个组匹配，在匹配函数中用$1到$4表示。匹配函数的作用是将价格插入模板中。

#### 匹配规则

正则表达式的规则很复杂，下面一一介绍这些规则。

> 字面量字符和元字符 大部分字符在正则表达式中，就是字面的含义，比如/a/匹配a，/b/匹配b。如果在正则表达式之中，某个字符只表示它字面的含义（就像前面的a和b），那么它们就叫做“字面量字符”（literal characters）。

```text
/dog/.test('old dog') // true
```

上面代码中正则表达式的dog，就是字面量字符，所以/dog/匹配old dog，因为它就表示d、o、g三个字母连在一起。

除了字面量字符以外，还有一部分字符有特殊含义，不代表字面的意思。它们叫做“元字符”（metacharacters），主要有以下几个。

* （1）点字符（.\)

点字符（.）匹配除回车（\r）、换行\(\n\) 、行分隔符（\u2028）和段分隔符（\u2029）以外的所有字符。注意，对于码点大于0xFFFF字符，点字符不能正确匹配，会认为这是两个字符。

/c.t/ 上面代码中，c.t匹配c和t之间包含任意一个字符的情况，只要这三个字符在同一行，比如cat、c2t、c-t等等，但是不匹配coot。

* （2）位置字符

位置字符用来提示字符所处的位置，主要有两个字符。

> ^ 表示字符串的开始位置 $ 表示字符串的结束位置

```text
// test必须出现在开始位置
/^test/.test('test123') // true

// test必须出现在结束位置
/test$/.test('new test') // true

// 从开始位置到结束位置只有test
/^test$/.test('test') // true
/^test$/.test('test test') // false
```

（3）选择符（\|）

竖线符号（\|）在正则表达式中表示“或关系”（OR），即cat\|dog表示匹配cat或dog。

/11\|22/.test\('911'\) // true 上面代码中，正则表达式指定必须匹配11或22。

多个选择符可以联合使用。

// 匹配fred、barney、betty之中的一个 /fred\|barney\|betty/ 选择符会包括它前后的多个字符，比如/ab\|cd/指的是匹配ab或者cd，而不是指匹配b或者c。如果想修改这个行为，可以使用圆括号。

/a\( \|\t\)b/.test\('a\tb'\) // true 上面代码指的是，a和b之间有一个空格或者一个制表符。

其他的元字符还包括\、\*、+、?、\(\)、\[\]、{}等，将在下文解释。

转义符 正则表达式中那些有特殊含义的元字符，如果要匹配它们本身，就需要在它们前面要加上反斜杠。比如要匹配+，就要写成+。

/1+1/.test\('1+1'\) // false

/1+1/.test\('1+1'\) // true 上面代码中，第一个正则表达式之所以不匹配，因为加号是元字符，不代表自身。第二个正则表达式使用反斜杠对加号转义，就能匹配成功。

正则表达式中，需要反斜杠转义的，一共有12个字符：^、.、\[、$、\(、\)、\|、\*、+、?、{和\。需要特别注意的是，如果使用RegExp方法生成正则对象，转义需要使用两个斜杠，因为字符串内部会先转义一次。

\(new RegExp\('1+1'\)\).test\('1+1'\) // false

\(new RegExp\('1+1'\)\).test\('1+1'\) // true 上面代码中，RegExp作为构造函数，参数是一个字符串。但是，在字符串内部，反斜杠也是转义字符，所以它会先被反斜杠转义一次，然后再被正则表达式转义一次，因此需要两个反斜杠转义。

特殊字符 正则表达式对一些不能打印的特殊字符，提供了表达方法。

\cX 表示Ctrl-\[X\]，其中的X是A-Z之中任一个英文字母，用来匹配控制字符。 \[\b\] 匹配退格键\(U+0008\)，不要与\b混淆。 \n 匹配换行键。 \r 匹配回车键。 \t 匹配制表符 tab（U+0009）。 \v 匹配垂直制表符（U+000B）。 \f 匹配换页符（U+000C）。 \0 匹配null字符（U+0000）。 \xhh 匹配一个以两位十六进制数（\x00-\xFF）表示的字符。 \uhhhh 匹配一个以四位十六进制数（\u0000-\uFFFF）表示的 Unicode 字符。 字符类 字符类（class）表示有一系列字符可供选择，只要匹配其中一个就可以了。所有可供选择的字符都放在方括号内，比如\[xyz\] 表示x、y、z之中任选一个匹配。

/\[abc\]/.test\('hello world'\) // false /\[abc\]/.test\('apple'\) // true 上面代码中，字符串hello world不包含a、b、c这三个字母中的任一个，所以返回false；字符串apple包含字母a，所以返回true。

有两个字符在字符类中有特殊含义。

（1）脱字符（^）

如果方括号内的第一个字符是\[\]，则表示除了字符类之中的字符，其他字符都可以匹配。比如，\[xyz\]表示除了x、y、z之外都可以匹配。

//.test\('hello world'\) // true //.test\('bbc'\) // false 上面代码中，字符串hello world不包含字母a、b、c中的任一个，所以返回true；字符串bbc不包含a、b、c以外的字母，所以返回false。

如果方括号内没有其他字符，即只有，就表示匹配一切字符，其中包括换行符。相比之下，点号作为元字符（.）是不包括换行符的。

var s = 'Please yes\nmake my day!';

s.match\(/yes._day/\) // null s.match\(/yes_day/\) // \[ 'yes\nmake my day'\] 上面代码中，字符串s含有一个换行符，点号不包括换行符，所以第一个正则表达式匹配失败；第二个正则表达式包含一切字符，所以匹配成功。

注意，脱字符只有在字符类的第一个位置才有特殊含义，否则就是字面含义。

（2）连字符（-）

某些情况下，对于连续序列的字符，连字符（-）用来提供简写形式，表示字符的连续范围。比如，\[abc\]可以写成\[a-c\]，\[0123456789\]可以写成\[0-9\]，同理\[A-Z\]表示26个大写字母。

/a-z/.test\('b'\) // false /\[a-z\]/.test\('b'\) // true 上面代码中，当连字号（dash）不出现在方括号之中，就不具备简写的作用，只代表字面的含义，所以不匹配字符b。只有当连字号用在方括号之中，才表示连续的字符序列。

以下都是合法的字符类简写形式。

\[0-9.,\] \[0-9a-fA-F\] \[a-zA-Z0-9-\] \[1-31\] 上面代码中最后一个字符类\[1-31\]，不代表1到31，只代表1到3。

连字符还可以用来指定 Unicode 字符的范围。

var str = "\u0130\u0131\u0132"; /\[\u0128-\uFFFF\]/.test\(str\) // true 上面代码中，\u0128-\uFFFF表示匹配码点在0128到FFFF之间的所有字符。

另外，不要过分使用连字符，设定一个很大的范围，否则很可能选中意料之外的字符。最典型的例子就是\[A-z\]，表面上它是选中从大写的A到小写的z之间52个字母，但是由于在 ASCII 编码之中，大写字母与小写字母之间还有其他字符，结果就会出现意料之外的结果。

/\[A-z\]/.test\('\'\) // true 上面代码中，由于反斜杠（''）的ASCII码在大写字母与小写字母之间，结果会被选中。

预定义模式 预定义模式指的是某些常见模式的简写方式。

\d 匹配0-9之间的任一数字，相当于\[0-9\]。 \D 匹配所有0-9以外的字符，相当于。 \w 匹配任意的字母、数字和下划线，相当于\[A-Za-z0-9_\]。 \W 除所有字母、数字和下划线以外的字符，相当于\[^A-Za-z0-9_\]。 \s 匹配空格（包括换行符、制表符、空格符等），相等于\[ \t\r\n\v\f\]。 \S 匹配非空格的字符，相当于。 \b 匹配词的边界。 \B 匹配非词边界，即在词的内部。 下面是一些例子。

// \s 的例子 /\s\w\*/.exec\('hello world'\) // \[" world"\]

// \b 的例子 /\bworld/.test\('hello world'\) // true /\bworld/.test\('hello-world'\) // true /\bworld/.test\('helloworld'\) // false

// \B 的例子 /\Bworld/.test\('hello-world'\) // false /\Bworld/.test\('helloworld'\) // true 上面代码中，\s表示空格，所以匹配结果会包括空格。\b表示词的边界，所以world的词首必须独立（词尾是否独立未指定），才会匹配。同理，\B表示非词的边界，只有world的词首不独立，才会匹配。

通常，正则表达式遇到换行符（\n）就会停止匹配。

var html = "**Hello**\nworld!";

/._/.exec\(html\)\[0\] // "\*Hello_" 上面代码中，字符串html包含一个换行符，结果点字符（.）不匹配换行符，导致匹配结果可能不符合原意。这时使用\s字符类，就能包括换行符。

var html = "**Hello**\nworld!";

/\[\S\s\]_/.exec\(html\)\[0\] // "\*Hello_\nworld!" 上面代码中，\[\S\s\]指代一切字符。

重复类 模式的精确匹配次数，使用大括号（{}）表示。{n}表示恰好重复n次，{n,}表示至少重复n次，{n,m}表示重复不少于n次，不多于m次。

/lo{2}k/.test\('look'\) // true /lo{2,5}k/.test\('looook'\) // true 上面代码中，第一个模式指定o连续出现2次，第二个模式指定o连续出现2次到5次之间。

量词符 量词符用来设定某个模式出现的次数。

? 问号表示某个模式出现0次或1次，等同于{0, 1}。

* 星号表示某个模式出现0次或多次，等同于{0,}。
* 加号表示某个模式出现1次或多次，等同于{1,}。

  // t 出现0次或1次

  /t?est/.test\('test'\) // true

  /t?est/.test\('est'\) // true

// t 出现1次或多次 /t+est/.test\('test'\) // true /t+est/.test\('ttest'\) // true /t+est/.test\('est'\) // false

// t 出现0次或多次 /t_est/.test\('test'\) // true /t_est/.test\('ttest'\) // true /t_est/.test\('tttest'\) // true /t_est/.test\('est'\) // true 贪婪模式 上一小节的三个量词符，默认情况下都是最大可能匹配，即匹配直到下一个字符不满足匹配规则为止。这被称为贪婪模式。

var s = 'aaa'; s.match\(/a+/\) // \["aaa"\] 上面代码中，模式是/a+/，表示匹配1个a或多个a，那么到底会匹配几个a呢？因为默认是贪婪模式，会一直匹配到字符a不出现为止，所以匹配结果是3个a。

如果想将贪婪模式改为非贪婪模式，可以在量词符后面加一个问号。

var s = 'aaa'; s.match\(/a+?/\) // \["a"\] 上面代码中，模式结尾添加了一个问号/a+?/，这时就改为非贪婪模式，一旦条件满足，就不再往下匹配。

除了非贪婪模式的加号，还有非贪婪模式的星号（\*）和非贪婪模式的问号（?）。

+?：表示某个模式出现1次或多次，匹配时采用非贪婪模式。 _?：表示某个模式出现0次或多次，匹配时采用非贪婪模式。 ??：表格某个模式出现0次或1次，匹配时采用非贪婪模式。 'abb'.match\(/ab_b/\) // \["abb"\] 'abb'.match\(/ab\*?b/\) // \["ab"\]

'abb'.match\(/ab?b/\) // \["abb"\] 'abb'.match\(/ab??b/\) // \["ab"\] 修饰符 修饰符（modifier）表示模式的附加规则，放在正则模式的最尾部。

修饰符可以单个使用，也可以多个一起使用。

// 单个修饰符 var regex = /test/i;

// 多个修饰符 var regex = /test/ig; （1）g 修饰符

默认情况下，第一次匹配成功后，正则对象就停止向下匹配了。g修饰符表示全局匹配（global），加上它以后，正则对象将匹配全部符合条件的结果，主要用于搜索和替换。

var regex = /b/; var str = 'abba';

regex.test\(str\); // true regex.test\(str\); // true regex.test\(str\); // true 上面代码中，正则模式不含g修饰符，每次都是从字符串头部开始匹配。所以，连续做了三次匹配，都返回true。

var regex = /b/g; var str = 'abba';

regex.test\(str\); // true regex.test\(str\); // true regex.test\(str\); // false 上面代码中，正则模式含有g修饰符，每次都是从上一次匹配成功处，开始向后匹配。因为字符串abba只有两个b，所以前两次匹配结果为true，第三次匹配结果为false。

（2）i 修饰符

默认情况下，正则对象区分字母的大小写，加上i修饰符以后表示忽略大小写（ignoreCase）。

/abc/.test\('ABC'\) // false /abc/i.test\('ABC'\) // true 上面代码表示，加了i修饰符以后，不考虑大小写，所以模式abc匹配字符串ABC。

（3）m 修饰符

m修饰符表示多行模式（multiline），会修改和$的行为。默认情况下（即不加m修饰符时），和$匹配字符串的开始处和结尾处，加上m修饰符以后，和$还会匹配行首和行尾，即和$会识别换行符（\n）。

/world$/.test\('hello world\n'\) // false /world$/m.test\('hello world\n'\) // true 上面的代码中，字符串结尾处有一个换行符。如果不加m修饰符，匹配不成功，因为字符串的结尾不是world；加上以后，$可以匹配行尾。

/^b/m.test\('a\nb'\) // true 上面代码要求匹配行首的b，如果不加m修饰符，就相当于b只能处在字符串的开始处。加上b修饰符以后，换行符\n也会被认为是一行的开始。

组匹配 （1）概述

正则表达式的括号表示分组匹配，括号中的模式可以用来匹配分组的内容。

/fred+/.test\('fredd'\) // true /\(fred\)+/.test\('fredfred'\) // true 上面代码中，第一个模式没有括号，结果+只表示重复字母d，第二个模式有括号，结果+就表示匹配fred这个词。

下面是另外一个分组捕获的例子。

var m = 'abcabc'.match\(/\(.\)b\(.\)/\); m // \['abc', 'a', 'c'\] 上面代码中，正则表达式/\(.\)b\(.\)/一共使用两个括号，第一个括号捕获a，第二个括号捕获c。

注意，使用组匹配时，不宜同时使用g修饰符，否则match方法不会捕获分组的内容。

var m = 'abcabc'.match\(/\(.\)b\(.\)/g\); m // \['abc', 'abc'\] 上面代码使用带g修饰符的正则表达式，结果match方法只捕获了匹配整个表达式的部分。这时必须使用正则表达式的exec方法，配合循环，才能读到每一轮匹配的组捕获。

var str = 'abcabc'; var reg = /\(.\)b\(.\)/g; while \(true\) { var result = reg.exec\(str\); if \(!result\) break; console.log\(result\); } // \["abc", "a", "c"\] // \["abc", "a", "c"\] 正则表达式内部，还可以用\n引用括号匹配的内容，n是从1开始的自然数，表示对应顺序的括号。

/\(.\)b\(.\)\1b\2/.test\("abcabc"\) // true 上面的代码中，\1表示第一个括号匹配的内容（即a），\2表示第二个括号匹配的内容（即c）。

下面是另外一个例子。

/y\(..\)\(.\)\2\1/.test\('yabccab'\) // true 括号还可以嵌套。

/y\(\(..\)\2\)\1/.test\('yabababab'\) // true 上面代码中，\1指向外层括号，\2指向内层括号。

组匹配非常有用，下面是一个匹配网页标签的例子。

var tagName = /&lt;\(\[&gt;\]+\)&gt;\[&lt;\]\*&lt;/\1&gt;/;

tagName.exec\("**bold**"\)\[1\] // 'b' 上面代码中，圆括号匹配尖括号之中的标签，而\1就表示对应的闭合标签。

上面代码略加修改，就能捕获带有属性的标签。

var html = '**Hello**world'; var tag = /&lt;\(\w+\)\(_\)&gt;\(._?\)&lt;/\1&gt;/g;

var match = tag.exec\(html\);

match\[1\] // "b" match\[2\] // " class="hello"" match\[3\] // "Hello"

match = tag.exec\(html\);

match\[1\] // "i" match\[2\] // "" match\[3\] // "world" （2）非捕获组

\(?:x\)称为非捕获组（Non-capturing group），表示不返回该组匹配的内容，即匹配的结果中不计入这个括号。

非捕获组的作用请考虑这样一个场景，假定需要匹配foo或者foofoo，正则表达式就应该写成/\(foo\){1, 2}/，但是这样会占用一个组匹配。这时，就可以使用非捕获组，将正则表达式改为/\(?:foo\){1, 2}/，它的作用与前一个正则是一样的，但是不会单独输出括号内部的内容。

请看下面的例子。

var m = 'abc'.match\(/\(?:.\)b\(.\)/\); m // \["abc", "c"\] 上面代码中的模式，一共使用了两个括号。其中第一个括号是非捕获组，所以最后返回的结果中没有第一个括号，只有第二个括号匹配的内容。

下面是用来分解网址的正则表达式。

// 正常匹配 var url = /\(http\|ftp\)://\(\[/\r\n\]+\)\(/\[\r\n\]\*\)?/;

url.exec\('[http://google.com/](http://google.com/)'\); // \["[http://google.com/](http://google.com/)", "http", "google.com", "/"\]

// 非捕获组匹配 var url = /\(?:http\|ftp\)://\(\[/\r\n\]+\)\(/\[\r\n\]\*\)?/;

url.exec\('[http://google.com/](http://google.com/)'\); // \["[http://google.com/](http://google.com/)", "google.com", "/"\] 上面的代码中，前一个正则表达式是正常匹配，第一个括号返回网络协议；后一个正则表达式是非捕获匹配，返回结果中不包括网络协议。

（3）先行断言

x\(?=y\)称为先行断言（Positive look-ahead），x只有在y前面才匹配，y不会被计入返回结果。比如，要匹配后面跟着百分号的数字，可以写成/\d+\(?=%\)/。

“先行断言”中，括号里的部分是不会返回的。

var m = 'abc'.match\(/b\(?=c\)/\); m // \["b"\] 上面的代码使用了先行断言，b在c前面所以被匹配，但是括号对应的c不会被返回。

（4）先行否定断言

x\(?!y\)称为先行否定断言（Negative look-ahead），x只有不在y前面才匹配，y不会被计入返回结果。比如，要匹配后面跟的不是百分号的数字，就要写成/\d+\(?!%\)/。

/\d+\(?!.\)/.exec\('3.14'\) // \["14"\] 上面代码中，正则表达式指定，只有不在小数点前面的数字才会被匹配，因此返回的结果就是14。

“先行否定断言”中，括号里的部分是不会返回的。

var m = 'abd'.match\(/b\(?!c\)/\); m // \['b'\] 上面的代码使用了先行否定断言，b不在c前面所以被匹配，而且括号对应的d不会被返回。

