# 闭包

标签（空格分隔）：javascript-magic

---

## 1. 概念
>闭包是指那些能够访问独立(自由)变量的函数 (变量在本地使用，但定义在一个封闭的作用域中)。换句话说，这些函数可以“记忆”它被创建时候的环境。  

## 2.词法作用域  
首先看下面的代码：
```
function init() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  displayName();
}
init();
```
这里的函数执行结果是'Mozilla',原因是当函数执行到alert(name)的时候，首先在函数内部查找变量name，如果没有则会按照作用域链一层一层向上查找次序是这样的
```
function displayName ->function init -> global
```
所以，init函数执行的时候会弹出'Mozilla'，因为init函数中定义了变量`name`  

## 2.闭包
接下来来看这个例子
```
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```

这段代码特殊的地方在于，函数调用后变量`name`没有被垃圾回收机制回收。 原因是`myFunc` 变成一个 **闭包** 了。  
闭包是一种特殊的对象。它由两部分构成：函数，以及创建该函数的环境。环境由闭包创建时在作用域中的任何局部变量组成。在我们的例子中，myFunc 是一个闭包，由 displayName 函数和闭包创建时存在的 "Mozilla" 字符串形成。  

为什么myFunc中的name在函数执行后没有被销毁？ 这个就要从JS的垃圾回收机制来说起了

## 3.垃圾回收机制
目前JS的垃圾回收机制无非就是两种：
1.标记清除（make-and-sweep）
2.引用计数（reference counting）  

1.标记清除：标记清除简单的来说就是给各个变量名打上 YES or NO的标签以供JS引擎进行处理（当然打什么标签自己理解即可）。在和执行上下文类似的的环境中当变量名称进入环境的时候，那么变量会被打上 YES。一般来说是绝对不会释放被打上 YES 标签的变量内存的，一旦变量在出了该环境时，变会被打上 NO 标签（和作用域貌似有点像），JS引擎会在一定时间间隔或者设置的时间来进行扫描，对NO标签的进行剔除以释放其内存。  

2.引用计数
一般来说，引用计数的含义是跟踪记录每个值被引用的次数。当声明一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数便是1，如果同一个值又被赋给另一个变量，则该值的引用次数加1，相反，如果包含对这个值引用的变量又取得了另一个值，则这个值的引用次数减1。当这个值的引用次数为0时，说明没有办法访问到它了，因而可以将其占用的内存空间回收

在Javascript中，如果一个对象不再被引用，那么这个对象就会被回收（打上了NO标记）。如果两个对象互相引用，而不再被第3者所引用，那么这两个互相引用的对象也会被回收。因为函数a被b引用，b又被a外的c引用，这就是为什么函数a执行后不会被回收的原因。

## 4 继续闭包
依然是这个例子
```
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```
实际上上面的函数表达式可以看做
```
makeFunc()();
```
在调用函数makeFunc的时候，由于他的返回值是函数displayName，那么函数displayName并不会在执行完毕之后直接销毁，而是被JS认为一直处于被调用状态。

来看下面一个例子：
```
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```
实际上，最后的两个console.log()依然可以看做
```
makeAdder(5)(2);
makeAdder(10)(2);
```
在调用makeAdder的同时它的return返回的函数也在被调用。

## 5.闭包的神奇用法
### 5.1 一些设置
```
function setSize(num){
    //在这里把计算函数return出去
    return function(){
        doucment.body.style.fontSize = size + "px";
    }
}

//调用一下试试？
var fontSet12 = makeAdder(12);
makeAdder(14);
makeAdder(16);

//于是你可以这样绑定函数了
var Btn12 = document.getElementById('btn-12');
btn12.addEventListener('click',fontSet12);
```

### 5.2 私有方法
你可以用这个方法模拟面向对象中的私有方法，怕不怕！
```
var count = function(){
    //首先定义一个自执行函数
     var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }   
}；

//或许我们见过另外一种形式的写法

var count = function(){
    var privateCounter = 0;

    function changeBy(val) {
        privateCounter += val;
    }

    function increment (){
        changeBy(1);
    }

    function decrement () {
        changeBy(-1);
    }

    function value() {
        return privateCounter;
    }

    return {
        increment:increment,
        decrement:decrement,
        value: value
    }
};
```
然后我们做一些操作
```
var con1 = count();
var con2 = count();
```
查看count的value返回值
```
console.log(con1.value()),con2.value();     //0,0
```
分别执行不同的操作
```
con1.decrement();
con2.increment();
```
再次查看
```
console.log(con1.value(),con2.value());     //-1，1
```

那么函数和对象都是引用类型，函数调用之后返回值是对象的时候，多次调用这个函数的时候他们的返回值不是指向同一个地址的么？
