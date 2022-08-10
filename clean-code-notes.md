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


