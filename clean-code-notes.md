# JavaScript 代码风格

## 可读性

### 如何定义变量名

有明显意图

```JS
let elapsed
let daysSinceModification
```

有明显意义

```JS
let theUsers
let users
```

更好发音

```JS
let fName, lName
let firstName, lastName
```

### 如何定义函数

函数名应该是一个动词或者动词短语

```JS
// Invite a new user with its email address
function inv (user) {   }
function inviteUser (emailAddress) {   }
```

避免长的参数列表，使用一个对象代替（可以忽略传参顺序）

```JS
function getRegisteredUsers (fields, include, fromDate, toDate) {   }
function getRegisteredUsers ({ fields, include, fromDate, toDate }) {   }
```

在一个抽象层级上，函数应该只**做一件事**（更好修复、更好阅读、更好复用）

```JS
function getUserRouteHandler (req, res) {
  const { userId } = req.params
  knex('user')                      // 这里包含了完整的查询行为
    .where({ id: userId })          
    .first()
    .then((user) => res.json(user)) // 这里把查询结果反序列化
}

// User model (eg. models/user.js)
const tableName = 'user'
const User = {          // 将查询行为抽取出来
  getOne (userId) {
    return knex(tableName)
      .where({ id: userId })
      .first()
  }
}

// route handler (eg. server/routes/user/get.js)
function getUserRouteHandler (req, res) {
  const { userId } = req.params
  User.getOne(userId)               // 这里用一个方法查询了 userId
    .then((user) => res.json(user)) // 只做反序列化
}
```

尽量使用纯函数，减少副作用

```JS
function addItemToCart (cart, item, quantity = 1) {
  const alreadyInCart = cart.get(item.id) || 0
  cart.set(item.id, alreadyInCart + quantity)   // 直接修改了传入的购物车对象
  return cart                                   // 返回传入的购物车对象
}

function addItemToCart (cart, item, quantity = 1) {
  const cartCopy = new Map(cart)                // 深拷贝一份购物车对象
  const alreadyInCart = cartCopy.get(item.id) || 0
  cartCopy.set(item.id, alreadyInCart + quantity)
  return cartCopy                               // 返回拷贝的购物车对象
}
```

高层级的函数写在上面，低层级的组件写在下面

```JS
function getFullName (user) {
  return `${user.firstName} ${user.lastName}`
}

function renderEmailTemplate (user) {
  const fullName = getFullName(user)  // 调用的函数声明在了它的上面
  return `Dear ${fullName}, ...`
}
```

### 代码格式 formatting

一致的命名、缩进尺寸、空格位、分号

consistent naming, indentation size, whitespace placement and even semicolon

#### 编写异步代码

Node>4 使用 promise
Node>7 使用 async/await

```JS

```

```JS
```

# airbnb style
[airbnb style](https://github.com/airbnb/javascript)

不要直接使用对象原型上的方法

```JS
object.hasOwnProperty(key)

Object.prototype.hasOwnProperty.call(object, key)

const has = Object.prototype.hasOwnProperty;
import has from 'has'; // https://www.npmjs.com/package/has
```

浅拷贝对象

```JS
const copy = Object.assign(original, { c: 3 });

const copy = Object.assign({}, original, { c: 3 });

const copy = { ...original, c: 3 };
```

将可迭代对象转换成数组

```JS
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

将类数组 array-like 转换成数组

```JS
const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };
const arr = Array.prototype.slice.call(arrLike);  // 本职是浅拷贝数组

const arr = Array.from(arrLike);
```

避免使用展开运算符去遍历一个可迭代对象，可以少创建一个中间数组

```JS
const baz = [...foo].map(bar); 

const baz = Array.from(foo, bar);
```

所有其他情况的返回不要再包一层 else

```JS
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```

有多个元素数组的展开方式

```JS
// bad
const arr = [
  [0, 1], [2, 3], [4, 5],
];

const objectInArray = [{
  id: 1,
}, {
  id: 2,
}];

const numberInArray = [
  1, 2,
];

// good
const arr = [[0, 1], [2, 3], [4, 5]];

const objectInArray = [
  {
    id: 1,
  },
  {
    id: 2,
  },
];

const numberInArray = [
  1,
  2,
];
```

当使用一个对象的多个属性时，使用解构赋值（避免了重复访问对象，减少重复代码，减少出错可能）

```JS
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

返回多个值的使用使用对象，使用数组的话读取是需要考虑顺序问题和

```JS
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}

function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}
```

字符串不应该换行，这会使字符串变得不可查询

```JS
// bad
const errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.';

// bad
const errorMessage = 'This is a super long error that was thrown because ' +
  'of Batman. When you stop to think about how Batman had anything to do ' +
  'with this, you would get nowhere fast.';

// good
const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
```

返回组装字符串时使用模版字符串

```JS
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${ name }?`;   // 不要有空格
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

不要有不必要的 escape 字符出现在字符串里（反斜杠 Backslashes 是很难阅读的）

```JS
const foo = '\'this\' \i\s \"quoted\"';

const foo = '\'this\' is "quoted"';
const foo = `my name is '${name}'`;
```

使用函数表达式代替函数声明（函数声明的提升特性使它可以在定义前引用，这会导致代码难以阅读和维护）

如果文件的函数定义又多又复杂，阻碍你理解剩余的代码，那么考虑将它们提取到单独的模块 module

```JS
// bad
function foo() {
  // ...
}

// bad
const foo = function () {
  // ...
};

// good，长变量名随意命名，不会和任何声明冲突，注意不能通过这个名字调用它，会报 ReferenceError: xxx is not defined
const short = function longUniqueMoreDescriptiveLexicalFoo() {
  // ...
};
```

用括号包裹立即执行函数 immediately-invoked function expression (IIFE)

立即执行表达式是一个单独的执行单元，在其内部你可以干净的使用 this，多用于第三方库，避免变量污染

[深刻理解立即执行函数](https://blog.csdn.net/hzw2017/article/details/120669318)

```JS
(function () {
  console.log('Welcome to the Internet. Please follow me.');
}());
```

不要在非函数块内做函数声明，如 if, while，使用函数表达式代替（函数声明不属于块语句）

[语句块中的函数声明](https://blog.csdn.net/aimingoo/article/details/115270358)

```JS
if (currentUser) {
  function test() {
    console.log('Nope.');
  }
}

let test;
if (currentUser) {
  test = () => {
    console.log('Yup.');
  };
}
```

不要使用 arguments，使用剩余语法，这是一个真数组

```JS
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

function concatenateAll(...args) {
  return args.join('');
}
```

使用默认参数值代替改变函数的参数

```JS
// really bad
function handleThings(opts) {
  opts = opts || {};
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
}

// good
function handleThings(opts = {}) {
}
```

避免对默认参数产生副作用

```JS
var b = 1;
// bad
function count(a = b++) {
  console.log(a);
}
count();  // 1
count();  // 2
count(3); // 3
count();  // 3
```

永远把设置了默认值的参数放在最后

```JS
function handleThings(opts = {}, name) {
  // ...
}

function handleThings(name, opts = {}) {
  // ...
}
```

一致化函数签名，在圆括号前有个空格，在花括号前有个空格（修改添加名字时避免操作空格）

```JS
// bad
const f = function(){};
const g = function (){};
const h = function() {};

// good
const x = function () {};
const y = function a() {};
```


有多行参数的函数签名或者函数调用时，每个参数独占一行、缩进、逗号结尾

```JS
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```

[back to the top](#airbnb-style)

在使用匿名函数和行内的回调函数时，使用箭头函数，因为它可以使用执行上下文的 this，语法也更简洁

如果函数结构相当复杂则挪出去声明一个自己的函数表达式

永远给参数加上括号，保持一致性，也方便后续添加和移除参数

```JS
// good 隐式返回
[1, 2, 3].map((number) => `A string containing the ${number + 1}.`);

// good 显示返回
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  return `A string containing the ${nextNumber}.`;
});

// good 返回对象
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));

// 函数没有隐式返回，只产生一个副作用
function foo(callback) {
  const val = callback();
  if (val === true) {
    // Do something if callback returns true
  }
}

let bool = false

// bad 赋值操作，返回值是被赋值后最新的值
foo(() => bool = true);

// good 表达式用花括号括起来
foo(() => {
  bool = true;
});

// bad
['get', 'post', 'put'].map((httpMethod) => Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
);

// good 当返回表达式横跨多行时，使用圆括号包裹增加可读性
['get', 'post', 'put'].map((httpMethod) => (
  Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
));

// bad
(foo) =>
  bar;

(foo) =>
  (bar);

// good
(foo) => bar;
(foo) => (bar);
(foo) => (
   bar
)
```

避免直接操作原型，使用类是更简洁的、更好理解的

```JS
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};

// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```

使用类继承，避免原型继承

```JS
// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```

通过返回 this 实现链式操作，减少重复代码

```JS
// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);
```

避免不必要的构造函数

```JS
// bad
class Jedi {
  constructor() {}

  getName() {
    return this.name;
  }
}

// bad
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
  }
}

// good
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
    this.name = 'Rey';
  }
}
```

避免复制类成员，只有最后一个生效，这属于一个 bug

```JS
// bad
class Foo {
  bar() { return 1; }
  bar() { return 2; }
}

// good
class Foo {
  bar() { return 1; }
}

// good
class Foo {
  bar() { return 2; }
}
```

永远使用 import/export，这是未来，它还可以转成其他的模块规范

```JS
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;

// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

不要使用通配符 wildcard，这要求必须有一个 default export，不方便后续修改

```JS
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';
```

不要直接导出导入的模块，虽然很简洁，但是破坏了一致性

```JS
// bad
export { es6 as default } from './AirbnbStyleGuide';

// good
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

避免同一个路径的多次导入

```JS
// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';

// good
import foo, { named1, named2 } from 'foo';

// good 导入多个变量时记得换行
import foo, {
  named1,
  named2,
} from 'foo';
```

避免导出可以重新赋值的变量

```JS
// bad
let foo = 3;
export { foo };

// good
const foo = 3;
export { foo };
```

当只有一个导出内容时，使用默认导出，而不名字导出（鼓励文件什么时候都导出一个东西，这样更好阅读和维护）

```JS
// bad
export function foo() {}

// good
export default function foo() {}
```

不要包含文件拓展名，这将成为硬代码 hardcode 不方便重构

```JS
// bad
import foo from './foo.js';
import bar from './bar.jsx';
import baz from './baz/index.jsx';

// good
import foo from './foo';
import bar from './bar';
import baz from './baz';
```

使用 JS 内置的高阶函数代替 for-in for-of循环

迭代数组 map() / every() / filter() / find() / findIndex() / reduce() / some() 

生成数组 Object.keys() / Object.values() / Object.entries()

```JS
const numbers = [1, 2, 3, 4, 5];

// bad
let sum = 0;
for (let num of numbers) {
  sum += num;
}
sum === 15;

// good forEach 命令式的迭代了数组
let sum = 0;
numbers.forEach((num) => {
  sum += num;
});
sum === 15;

// best 函数式的迭代了数组
const sum = numbers.reduce((total, num) => total + num, 0);
sum === 15;

// bad
const increasedByOne = [];
for (let i = 0; i < numbers.length; i++) {
  increasedByOne.push(numbers[i] + 1);
}

// good
const increasedByOne = [];
numbers.forEach((num) => {
  increasedByOne.push(num + 1);
});

// best (keeping it functional)
const increasedByOne = numbers.map((num) => num + 1);
```

避免使用生成器函数，没必要

```JS
// good
function* foo() {
  // ...
}

// good
const foo = function* () {
  // ...
};
```

使用指数运算符

```JS
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

用 const let 声明变量，避免污染全局空间

使用 const let 声明每一个变量和赋值，不用担心切换符号`;` `,`，方便做断点测试

将所有 const let 分别组织在一起

```JS
// bad
const items = getItems(),
    goSportsTeam = true,  // 这里是逗号
    dragonBall = 'z';

// bad
const items = getItems(),
    goSportsTeam = true;  // 这里是分号
    dragonBall = 'z';

// good
const items = getItems();
const goSportsTeam = true;
const dragonBall = 'z';

// bad
let i;
const items = getItems();
let dragonBall;
const goSportsTeam = true;
let len;

// good
const goSportsTeam = true;
const items = getItems();
let dragonBall;
let i;
let length;
```

将变量声明在合理的位置，因为 const let 是块级作用域的

```JS
// bad
function checkName(hasName) {
  const name = getName();

  if (hasName === 'test') {
    return false;
  }

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}

// good
function checkName(hasName) {
  if (hasName === 'test') {
    return false;
  }

  const name = getName();

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}
```

不要使用链式赋值变量，会隐式的声明成全局变量

```JS
// bad
(function example() {
  let a = b = c = 1;
  // 等价于 let a = ( b = ( c = 1 ) ); 关键字只对 a 生效，b c 变成了全局变量
}());

console.log(a); // throws ReferenceError
console.log(b); // 1
console.log(c); // 1

// good
(function example() {
  let a = 1;
  let b = a;
  let c = a;
}());

console.log(a); // throws ReferenceError
console.log(b); // throws ReferenceError
console.log(c); // throws ReferenceError

// the same applies for `const`
```

禁止使用自增、自减操作符，使用 +=、-= 操作符代替（易受自动插入分号影响，+=、-= 更具表达力，避免了非故意的预先自增自减）

```JS
// bad

const array = [1, 2, 3];
let num = 1;
num++;
--num;

let sum = 0;
let truthyCount = 0;
for (let i = 0; i < array.length; i++) {
  let value = array[i];
  sum += value;
  if (value) {
    truthyCount++;
  }
}

// good

const array = [1, 2, 3];
let num = 1;
num += 1;
num -= 1;

const sum = array.reduce((a, b) => a + b, 0);
const truthyCount = array.filter(Boolean).length;
```

不要在赋值符号后面换行，必要时使用圆括号包裹

```JS
// bad 后面的代码会跟着缩进，这不是我们想要的
const foo =
  superLongLongLongLongLongLongLongLongFunctionName(); 

// good
const foo = (
  superLongLongLongLongLongLongLongLongFunctionName()
);

// bad
const foo
  = 'superLongLongLongLongLongLongLongLongString';

// good
const foo = 'superLongLongLongLongLongLongLongLongString';
```

不要存在未使用的变量在代码里，这会给阅读带来障碍

```JS
// bad

var some_unused_var = 42;

// Write-only variables are not considered as used.
var y = 10;
y = 5;

// A read for a modification of itself is not considered as used.
var z = 0;
z = z + 1;

// Unused function arguments.
function getX(x, y) {
    return x;
}

// good

function getXPlusY(x, y) {
  return x + y;
}

var x = 1;
var y = a + 2;

alert(getXPlusY(x, y));

// 'type' is ignored even if unused because it has a rest property sibling.
// This is a form of extracting an object that omits the specified keys.
var { type, ...coords } = data;
// 'coords' is now the 'data' object without its 'type' property.
```


Objects evaluate to true
Undefined evaluates to false
Null evaluates to false
Booleans evaluate to the value of the boolean
Numbers evaluate to false if +0, -0, or NaN, otherwise true
Strings evaluate to false if an empty string '', otherwise true


```JS
if ([0] && []) {
  // true
  // an array (even an empty one) is an object, objects will evaluate to true
}
```

布尔类型的值直接用，字符串和数字要明确的比较

```JS
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```

switch 的 case 和 default 子句需要用花括号包裹，形成一个块级作用域，否则多个字句定义相同的内容会出问题

```JS
// bad
switch (foo) {
  case 1:
    let x = 1;
    break;
  case 2:
    const y = 2;
    break;
  case 3:
    function f() {
      // ...
    }
    break;
  default:
    class C {}
}

// good
switch (foo) {
  case 1: {
    let x = 1;
    break;
  }
  case 2: {
    const y = 2;
    break;
  }
  case 3: {
    function f() {
      // ...
    }
    break;
  }
  case 4:
    bar();
    break;
  default: {
    class C {}
  }
}
```

三元运算符应该独占一行，不应嵌套

```JS
// bad
const foo = maybe1 > maybe2
  ? "bar"
  : value1 > value2 ? "baz" : null;

// split into 2 separated ternary expressions
const maybeNull = value1 > value2 ? 'baz' : null;

// better
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull;

// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
```

避免一个不需要的三元运算表达式

```JS
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```

有多个操作符运算时 / % * 建议用括号包裹，因为它们的优先级比较容易模糊

```JS
// bad
const foo = a && b < 0 || c > 0 || d + 1 === 0;

// bad
const bar = a ** b - 5 % d;

// bad
// one may be confused into thinking (a || b) && c
if (a || b && c) {
  return d;
}

// bad
const bar = a + b / c * d;

// good
const foo = (a && b < 0) || c > 0 || (d + 1 === 0);

// good
const bar = a ** b - (5 % d);

// good
if (a || (b && c)) {
  return d;
}

// good
const bar = a + (b / c) * d;
```

没有块时语句的位置

```JS
// bad
if (test)
  return false;

// good
if (test) return false;

// good
if (test) {
  return false;
}

// bad
function foo() { return false; }

// good
function bar() {
  return false;
}
```

if 的 else 要和它最后一个闭合花括号放在一行

```JS
// bad
if (test) {
  thing1();
  thing2();
}
else {
  thing3();
}

// good
if (test) {
  thing1();
  thing2();
} else {
  thing3();
}
```

如果 if 总是 return，那么它后续的 else 就是无用的

```JS
// bad
function foo() {
  if (x) {
    return x;
  } else {
    return y;
  }
}

// good
function foo() {
  if (x) {
    return x;
  }

  return y;
}

// bad
function cats() {
  if (x) {
    return x;
  } else if (y) {
    return y;
  }
}

// good
function cats() {
  if (x) {
    return x;
  }

  if (y) {
    return y;
  }
}

// bad
function dogs() {
  if (x) {
    return x;
  } else {
    if (y) {
      return y;
    }
  }
}

// good
function dogs(x) {
  if (x) {
    if (z) {
      return y;
    }
  } else {
    return z;
  }
}
```

当判断条件太长，换行时将逻辑操作符放在前面，增加可读性

```JS
// bad
if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
  thing1();
}

// good
if (
  (foo === 123 || bar === 'abc')
  && doesItLookGoodWhenItBecomesThatLong()
  && isThisReallyHappening()
) {
  thing1();
}

// bad
if (foo === 123 &&
  bar === 'abc') {
  thing1();
}

// bad
if (foo === 123
  && bar === 'abc') {
  thing1();
}

// bad
if (
  foo === 123 &&
  bar === 'abc'
) {
  thing1();
}

// good
if (
  foo === 123
  && bar === 'abc'
) {
  thing1();
}

// good
if (foo === 123 && bar === 'abc') {
  thing1();
}
```

不要使用选择操作符代替逻辑语句

```JS
// bad
!isRunning && startRunning();

// good
if (!isRunning) {
  startRunning();
}
```

多行注释请使用 /** ... */

```JS
// bad
// make() returns a new element
// based on the passed in tag name

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
```

当行注释放在目标的上面，除非是块内第一行，否则前面要空一行

所有的注释内容前都要有一个空格

```JS
// bad
const active = true;  // is current tab

// good
// is current tab
const active = true;

// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// good
function getType() {
  console.log('fetching type...');

  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}
```

有 FIXME TODO 前缀的注释指出需要重看的问题或者需要实现的内容

```JS
// FIXME: shouldn’t use a global here
// TODO: total should be configurable by an options param
```



```JS
```



```JS
```



```JS
```



```JS
```



```JS
```



```JS
```



```JS
```



```JS
```



```JS
```


