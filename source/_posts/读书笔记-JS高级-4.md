---
title: 引用类型中的对象和函数
date: 2017-07-29 14:06:59
tags:
- 数据类型
- 引用类型
- 对象
- 函数
categories:
- 读书笔记
- JavaScript高级程序设计
---

``复杂类型``    ``引用类型``    ``对象Object``    ``函数Function``    ``对应「JavaScript高级程序设计」中部分第四章和部分第五章``

<!--more-->

***

上一篇读书笔记谈到了基本数据类型，本篇笔记接着上一篇的思路，重点介绍最常用的两个个复杂数据类型，即对象Object和函数Function，对应本书中第四章的变量部分以及第五章的对象和函数部分。

# 复杂数据类型

复杂数据类型又称为引用类型，引用类型在内存中存储的是对象地址，请看以下代码：
```
var a = {name: 'a'}
var b = a
```
a和b的存储方式如下图所示：
![](https://raw.githubusercontent.com/ForestCold/image/master/du-3-2.jpg)

其中，引用类型的地址存储在大小固定的栈内存中，引用类型的内容存储在大小可变的堆类型中。因此，引用类型的值是按照引用访问的。要特别注意的是，很多语言中String是通过引用进行访问的，而JavaScript中String被归类为了基础数据类型。
除了访问方式的不同之外，引用类型可以随时扩展属性（不仅仅是对象，就连数组或者函数这样的引用类型也可以），而基础类型则不行。

更多关于类型存储的知识比较请参照[方应杭的知乎文章](https://zhuanlan.zhihu.com/p/22400319)。

> **对象的定义：** 对象是某个特定的引用类型的实例。引用类型和类的概念很像，但二者并不一样，不要混淆。

## **Object**

  对象类型是最基本的引用类型，也是最好理解的引用类型，其它所有引用类型都从对象中继承了基本行为。因此我们从对象类型开始说起。

### **定义对象**
  定义对象有两种方式，第一种方式是通过构造函数来定义：
  ```
  var person = new Object();
  person.name = "Magic Yummy";
  ```
  另一种方式是通过``对象字面量``来表示：
  ```
  var person = {
    name : "Magic Yummy"
  };
  ```
  这两种方法定义出的对象person是等价的。实际上，第一种方法还拥有其语法糖（第三种方法）：
  ```
  var person = {};
  person.name = "Magic Yummy"；
  ```
  >无论是从易读性还是性能方面而言，实际开发中都推荐采用第二种或第三种定义方法。来源于慕课网相关课程。

  对于需要传入大量参数的函数，对象字面量可以用来封装这些参数。

### **访问对象**
  访问对象时，可以通过点表示法或方括号两种方式，如：
  ```
  console.log(person["name"])   // "Magic Yummy"
  console.log(person.name)      // "Magic Yummy"
  ```
  使用方括号表示法的优点是可以通过变量来访问属性，即在方括号中书写字符串变量。另外，如果属性名中有空格，或者属性名是保留字，也可以使用方括号的写法。不过，点表示法更符合现在程序员的编程习惯，因此应该尽量使用点表示法。

## **Function**
  JavaScript中的函数与C++或JAVA中的函数有不小的区别，包含了很多巧妙的设计细节。函数部分跟包含一大堆属性方法的数组部分相比，读起来心旷神怡。

  首先需要牢记的概念是，函数是一个对象，函数名是指向这个对象的指针，和这个对象本身不存在绑定关系。因此，同一个函数可以包含多个函数名，修改某个函数名使它指向新的函数对象，不会对原本被指向的那个函数对象产生任何影响。
### **定义函数**
  函数可以通过``函数声明``或``函数表达式``来定义（还可以用Function构造函数来定义，不过不推荐，这里就不列出了）：
  ```
  /* 函数声明 */
  function sum (num1, num2){
    return num1 + num2;
  }
  /*函数表达式*/
  var sum = function(num1, num2){
    return num1 + num2;
  }
  ```
  > 这两种定义方式的结果没有区别。但需要注意的一点是，JavaScript的解析器在执行环境中加载数据时，会先读取函数声明，然后再执行其它任何代码。而函数表达式则没有这个待遇，它需要等到解析器执行到它所在的行，才会被解释执行。因此，如果在用函数表达式定义的函数之前调用它，会产生意外标识符的错误，而函数声明没有这个问题。

### **作为值的函数**
  一个函数可以作为其它任意函数的参数，其中一个最常用的例子就是为数组的 *sort()* 方法定义比较函数，使之根据不同的属性进行排序。可以定义如下比较函数：
  ```
  function createComparisonFunction(prototypeName) {
    return function(object1, object2){
      var value1 = object1[prototypeName];
      var value2 = object2[prototypeName];
      if (value1 < value2){
        return -1;
      } else if (value1 > value2){
        return 1;
      }else {
        return 0;
      }
    }
  }
  ```
  将这个函数作为 *sort()* 排序函数的输入，并且将想要排序的属性作为这个函数的输入，就可以按照某个属性对数组进行排序。

### **函数的参数**
  JavaScript的函数不要求提供特定数量和类型的参数，而函数内部维护了一个叫arguments的参数数组，用来存储传入该函数的所有参数。值得注意的是，arguments的值和对应的参数存储的值不共享一个内存空间，但它们相互绑定。如以下代码：
  ```
  function doAdd(num1, num2) {
    arguments[1] = 10;
    alert(arguments[0] + num2);
  }
  ```
  在这个函数中，如果调用时输入了两个以上的参数，那么第二个参数会被设置为10，之后的所有num2也都是10而不是起始传进来的num2值。但是如果只有一个参数，那么第二个参数的值仍然是undefined，这是因为arguments不能对超出自身长度的部分进行赋值，而它的长度又是由真正的传入的参数个数决定的。
  另外一点需要注意的是，JavaScript函数没有``重载``。
  >重载指的是相同的函数名拥有不同的参数类型，根据不同参数类型函数做不同的事。由于JavaScript的函数没有对参数类型进行硬性规定，因此也就不存在重载的说法。

### **callee和caller**
  callee和caller是函数的两个内部属性。callee是arguments的属性，指的是arguments对象所在的函数。caller则是函数自身的属性，指的是调用这个函数的函数。这么命名是为了将它们进行区分。由于函数名和函数对象不是绑定关系， 因此当我们需要在函数内进行递归的时候，使用callee属性可以实现函数名与函数内容的低耦合：
  ```
  function factorial(num) {
    if (num <= 1){
      return 1;
    } else {
      return num * arguments.callee(num - 1); //return num * factorial(num - 1);
    }
  }
  ```
  例如在以上阶乘函数中，如果按照常规的写法，函数名factorial会出现在函数内容中，如果我们用另一个函数名指向这个函数对象，就得修改相应的函数内容。但是使用arguments.callee属性就不存在这个问题。而caller也是为了实现更松散的耦合，如：
  ```
  function outer() {
    inner();
  }

  function inner() {
    alert(arguments.callee.caller); //alert(inner.caller);
  }

  outer();
  ```
  这段代码会在警告框中打印出outer()函数的源码。

### **this对象**
  函数的this对象引用的是函数执行的环境对象，也就是调用这个函数的对象。如果这个函数是被直接执行的（相当于在全局作用域中执行），this对象引用的就是window。
  ```
  window.color = "red";
  var o = {color: "blue"};
  function sayColor() {
    alert(this.color);
  }

  sayColor();            //没人调用它，this指向window，打印出的值为red
  o.sayColor = sayColor;
  o.sayColor();          //对象o调用它，this指向o，打印出的值为blue
  ```

### **apply(), call()和bind()方法**
  *apply()* 方法和 *call()* 方法都能够执行函数，它们的第一个参数都为函数执行的作用域。*apply()* 函数的第二个参数为参数数组，而 *call()* 函数需要把所有参数拆开放入第二个以及之后的参数中。这两个方法的用处在于可以在不同的作用域中运行同样的函数，这一点在实际编程中非常有用。
  ```
  window.color = "red";
  var o = {color : "blue"};
  function sayColor() {
    alert(this.color);
  }

  sayColor();            //red

  sayColor.call(this);   //red
  sayColor.call(window); //red
  sayColor.call(o);      //blue
  ```
  *bind()* 方法创建一个函数实例，它的参数作为函数作用域绑定到这个实例上。因此就算在全局变量中调用这个函数实例，返回结果仍然基于被绑定的作用域。
  ```
  var objectSayColor = sayColor.bind(o);
  objectSayColor();      //blue
  ```
  这种方法的好处将会在很后面的高级技巧部分进行更为详细的讨论。
  由于引用类型数组Array的内容实在太多，篇幅关系放到下一篇读书笔记进行整理。
