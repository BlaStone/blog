# Javascript浅拷贝与深拷贝

如果你也是小白，还傻傻分不清什么是浅拷贝，什么是深拷贝，以及怎么实现它们，不防花几分钟往下看。

### 什么是浅拷贝，什么是深拷贝？

> 浅拷贝：浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据。

> 深拷贝：深拷贝是对对象以及对象的所有子对象进行拷贝。

在上我们的主菜之前，我们先来两个前菜：

### 前菜一之js变量在内存中的储存方式

首先我们先来看个会涉及到的知识点。
栈（stack）为自动分配的内存空间，它由系统自动释放；而堆（heap）则是动态分配的内存，大小不定也不会自动释放。
JavaScript的数据类型包含 string， number，boolean，null，undefined 五种基本数据类型以及 object 引用数据类型。
其中，基本数据类型变量的 key，value 都储存在栈内存中，而引用类型变量它的 key 也同样储存在栈内存中，但 value 却是储存在堆内存中，栈内存中 key 对应储存的只是 value 在堆内存中的地址。

### 前菜二之赋值

接下来我们再了解下赋值，先来看段代码：

```js

var a = 1;
var b = a;
a = 2;

console.log(a, b); // 2, 1

var arr = [1, 2, { age: 23 }];
var arr1 = arr;
arr1[0] = 3;

console.log(arr, arr1); // [3, 2, { age: 23 }], [3, 2, { age: 23 }]

```
可能会有人翻白眼认为呵呵这不很正常吗，这不就是浅拷贝吗，那可就真是图样图森破==。我一开始也是这样认为的，然后就被啪啪打脸了。注意看浅拷贝的描述，只复制一层对象的属性，不包括对象里为引用类型的数据。

Talk is cheap.Show me the code.接下来我们先来实现一个浅拷贝再比较一下就比较明了了。

### 浅拷贝的实现

首先我们要判断拷贝对象类型，若是基本数据类型则直接赋值；若是引用数据类型，是数组则创建一个新数组，是对象则创建一个新对象，遍历将属性添加给新创建的变量。好像还挺简单的，我们来看看：

```js

function shallowCopy (obj) {
  var ret;
  
  // 若不是 object 类型直接返回，且如果 obj 是 null 的话直接返回
  if (typeof obj !== 'object' || obj === null) {
    return ret = obj;
  }
  
  // 判断是数组还是对象
  ret = obj instanceof Array ? [] : {};
  
  for (var i in obj) {
    
    // 只循环自身属性，不包含原型链上的属性
    if (obj.hasOwnProperty(i)) {
      ret[i] = obj[i];
    }
  }
  
  return ret;
}

```

### 浅拷贝与赋值的区别

OJBK，我们来试一发：

```js

// 赋值基本类型
var a = 1;
var b = a;
a = 2;
console.log(a, b); // 2, 1

// 赋值引用类型
var arr = [1, 2, { age: 23 }];
var arr1 = arr;
arr1[0] = 5;
arr1[2].age = 10;

console.log(arr, arr1); // [5, 2, { age: 10 }], [5, 2, { age: 10 }]

// 浅拷贝
var arr2 = [1, 2, { age: 23 }];
var arr3 = shallowCopy(arr2);
arr3[0] = 5;
arr3[2].age = 10;

console.log(ar2, arr3); // [5, 2, { age: 10 }], [1, 2, { age: 10 }]

```

如此看官可有恍然大明白的感觉，赋值基本类型数据，双方互不影响；赋值引用类型数据，双方共用一个指针，互相影响；而浅拷贝则不同，基本类型数据，也是互不影响，但引用类型数据，一层的属性互不影响，若出现引用类型的子对象则互相影响。

### 深拷贝的实现

要实现一个深拷贝，它与浅拷贝的区别即子对象为引用类型的情况无法进行深层复制，那我们只需要在循环时，判断子对象的类型是否为引用类型，若是则再调用自身，即递归的思想，直到子对象中不再有引用类型数据为止。

```js

function deepCopy (obj) {
  var ret;
  
  if (typeof obj !== 'object' || obj === null) {
    return ret = obj;
  }
  
  ret = obj instanceof Array ? [] : {};
  
  for (var i in obj) {
    
    // 只循环自身属性，不包含原型链上的属性
    if (obj.hasOwnProperty(i)) {
      
      // 若子对象为引用类型则递归调用自身
      ret[i] = typeof obj[i] === 'object' ? arguments.callee(obj[i]) : obj[i];
    }
  }
  
  return ret;
}

var arr4 = [1, 2, { age: 23 }];
var arr5 = deepCopy(arr4);
arr5[0] = 5;
arr5[2].age = 10;

console.log(arr4, arr5); // [1, 2, { age: 23 }], [5, 2, { age: 10 }]

```
### 其他相关实现方式

> 浅拷贝：数组的 concat, slice, underscore 中的 clone 都是浅拷贝
> 深拷贝：JQ的 extend; lodash 的 cloneDeep; JSON.stringify(),再用JSON.parse(),不过这种方法虽简便却有所缺陷，JSON.stringify()在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在数组中则会返回 null。大家可以自行去了解一哈。

终于叭叭完了==，新人写文章，若有不对的地方欢迎大家指点交流。写文不易，，若觉得有所顿悟亦可来个star鼓励哈。


