# Javascript构造函数new的实现

什么是 new ？先来看一下MDN上的解释

> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。

```js
function Foo () {}
```

> 当代码 new Foo(...) 执行时，会发生以下事情：

> 1.一个继承自 Foo.prototype 的新对象被创建。

> 2.使用指定的参数调用构造函数 Foo ，并将 this 绑定到新创建的对象。new Foo 等同于 new Foo()，也就是没有指定参数列表，Foo 不带任何参数调用的情况。

> 3.由构造函数返回的对象就是 new 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤1创建的对象。（一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤）。


由此可见，new 构造函数需要返回对象（此处用1，2来表示，对象1表示显式返回的对象，对象2表示新创建的对象），若构造函数显式返回一个对象1，则返回的即是对象1，若未显式返回对象，则默认返回新创建的对象2；

根据原型和原型链的知识，我们知道实例的__proto__属性指向构造函数的 prototype 属性，因此实例得以访问得到构造函数原型（prototype）的属性，且我们需要在 new 构造函数内调用指定的构造函数，即 new 运算符后面的构造函数，将 this 绑定到新建的对象2上。

下面我们来开始吧：

```js
// 模仿 new 的构造函数
function objectFactory () {
  
  // 创建一个新对象2
  var obj = new Object();
  
  // 获取参数中我们传入的构造函数，此处 shift 函数会改变 arguments
  var Constructor = Array.prototype.shift.call(arguments);
  
  // 将新建对象2的__proto__属性指向传入的构造函数的原型
  obj.__proto__ = Constructor.prototype;
  
  // 用 apply 调用构造函数，改变传入的构造函数的 this 指向，使得 新建对象2 --> obj 可以访问构造函数的属性
  var ret = Constructor.apply(obj, arguments);
  
  // 判断调用传入的构造函数是否有显式返回对象1，若有则此函返回对象1，否则返回新建对象2 --> obj
  return typeof ret === 'object' ? ret : obj;
}
```

好了，new 模仿（chao xi）完毕了，下面我们来测试一哈。

``` js
function Person (name) {
  this.name = name;
  
  this.habit = 'basketball';
}

Person.prototype.eq = '250';

Person.prototype.sayName = function () {
  console.log('My name is ' + this.name);
}

// new 
var person1 = new Person('xiaoming');

// 我们实现的 objectFactory
var person2 = objectFactory(Person, 'xiaoming');

console.log(person1.name, person2.name); // 'xiaoming', 'xiaoming'
console.log(person1.habit, person2.habit); // 'basketball', 'basketball'
console.log(person1.eq, person2.eq); // '250', '250'
console.log(person1.sayName(), person2.sayName()); // 'My name is xiaoming', 'My name is xiaoming'

```

=。= ojbk，实现了我们的 new 构造函数，完成（chao xi）了自己的第一篇文章，好像也没想象中那么难。
