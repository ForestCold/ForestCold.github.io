---
title: 对象的创建方法——构造函数和原型模式
date: 2017-07-31 15:04:34
tags:
- 工厂模式
- 构造函数
- 原型
- 面向对象
categories:
- 读书笔记
- JavaScript高级程序设计
---

``对象``    ``构造函数``    ``创建对象``    ``工厂模式``    ``原型模式``    ``组合模式``    ``动态原型模式``    ``寄生继承``    ``稳妥模式``    ``对应「JavaScript高级程序设计」中部分第六章``

<!--more-->

***

JavaScript中没有类和接口的概念，但它又是一门面向对象的语言，有自己的继承和派生方法。学习这些方法之前，首先要了解创建对象的基本概念。从发展历程上看，最早人们使用工厂模式来创建对象，之后发展到构造函数模式，最后原型模式。之后，由构造函数和原型组合而得到了组合模式，将组合模式进行进一步封装得到了动态原型模式，将工厂模式的函数变成构造函数得到了寄生模式，为了安全起见又创建了稳妥模式。

# 工厂模式

工厂模式这个词指是一种设计模式，抽象了创建具体对象的过程。
>**设计模式：** 这是软件工程领域的一个概念，表明开发人员用一种标准的方法去解决一类问题。

```
而在这里，工厂模式指的是如下代码的创建对象方式，这是最早的创建对象方法，也是后文将提到的寄生模式和稳妥模式的基础。
function createObject1(arg){   //叫Object1是为了和Object区分开来
  var o = new Object();
  o.att = arg;
  o.fun = function(){
    alert(this.att);
  }
  return o;
}

var inst = createObject1(extrArg);
```
从以上代码可以看出，创建对象的语句和给对象添加属性方法的语句被封装在了一个叫 *createObject1()* 的函数里。这么做的好处在于每次新建一个对象实例的时候，不用重复敲打 *var inst = new Object();* 这行代码。但是这个方法有个严重的缺陷，就是使用instanceof无法查询到正确的对象类型。

>*instanceof的工作方式* 	例如我们执行instanceof f这一条语句，其实系统做了以下两个步骤： 1.首先查找f的_proto_，看它对应了哪个构造函数的prototype 2.如果找不到，再试着判断它是否对应Object的prototype。来源于慕课网相关课程。

因此，我们使用instanceof inst的时候，实际上返回的是Object。

# 构造函数模式

构造函数模式解决了instanceof的问题。

```
function Object2(arg){
  o.att = arg;
  o.fun = function(){
    alert(this.att);
  }
}

var inst = new Object2(extrArg);
```

注意到和工厂模式不同的地方在于： ⑴new被放到了实例化的时候 ⑵函数没有返回值。这么做的好处在于使用instanceof inst时，能够正确返回Object2。对于实例inst而言，它有一个叫做constructor的属性，这个属性指向了它的构造函数Object2。

然而，构造函数模式也有自己的不足。我们假设要创建Object2对象的多个实例，每当创建一个实例的时候，构造函数Object2都会new一个新的fun函数出来（记得我们之前说过 *fun = function(){}* 是 *new function()* 的语法糖）。这些实例的 *fun()* 函数的代码是相同的，却占用了不同的空间，这样非常耗费资源。

尽管我们可以通过把 *new fun()* 放到构造函数的外面，也就是全局作用域中来避免这个问题，然而这样做不符合一般的编程规范。试想，如果有很多很多的对象的方法，它们就得遍布全局作用域，不仅代码混乱，还会让其它不是这些对象实例的东西访问到这些方法。因此，我们需要一个更高级的方式对方法进行继承。

# 原型模式

原型模式的思路是使用指针指向所有实例共享的属性和方法，以此避免构造函数的空间浪费问题。JavaScript规定每一个引用对象都包含两个属性，prototype（也叫显示原型）属性和_proto_（也叫隐式原型）属性。并且，每个对象的_proto_属性，都指向这个对象的构造函数的prototype属性（记得函数也是一个对象）。每个对象的prototype实际上也是一个对象，它拥有一个叫做constructor的属性指向它所对应的构造函数。

## 直接创建原型

```
function Object3(){

}

Object3.prototype.attrA = "attrA";
Object3.prototype.funA = function(){
  alert(this.attrA);
}

var inst1 = new Object3();
inst1.funA();                  //"attrA"

var inst2 = new Object3();
inst2.funA();                  //"attrA"
```

在这段代码中，Object3是一个构造函数，它包含一个显示原型属性Object3.prototype，我们显示地对这个属性指向的对象添加一个属性attrA和一个方法funA。然后，inst1和inst2作为构造函数Object3的实例，它们包含隐式原型属性_proto_，指向的是Object3.prototype。这一关系可以由下图表示：

![](https://raw.githubusercontent.com/ForestCold/image/master/du-7-1.jpg)

而JavaScript中还做了另外一个规定，当访问一个实例的某个属性或方法时，首先在这个实例中查找，找不到就到这个实例的_proto_所指向的地方继续查找，也就是到它的构造函数的prototype中查找。因此，在这个例子中，由于构造函数内部没有定义 *funA()* 这个方法，所以inst1和inst2内部没有 *funA()* 。我们得在这两个实例的_proto_中继续查找，在它所指向的Object3的prototype中，我们找到了这个 *funA()* 这个方法并执行。

这个例子还有一点需要注意，那就是this指代的是谁。我们之前说过，this指的对象的作用域，我们按照之前说的原型规则来看一遍。当执行了 *var inst1 = new Object3();* 这句话时，这个new出来的Object3中的this实际上就是inst1。而我们执行funA函数的时候，由于是通过指针的形式链接到该函数上的，所以this其实还是inst1。然而，在inst1中找不到attrA这个属性，所以我们到它的_proto_里，即Object3.prototype去找attrA。此时发现找到了，就把这个属性继承下来，于是输出的就是attrA。

为了验证这个想法是否正确，我们可以在Object3构造函数中添加一个同名属性attrA:
```
function Object3(){
  attrA = "attrB";
}
```
这样当inst1被创建的时候，它本身就拥有了attrA这个属性，这个属性的值是attrB，因此此时代码执行结果输出的是attrB。

## 通过字面量创建原型

```
function Object3(){

}

Object3.prototype = {
  attrA = "attrA";
  funA = function(){
    alert(this.attrA);
  }
}
```

通过字面量创建原型可以少打一点代码，比如我们不用再为每个属性和方法都敲打一遍Object3.prototype。但是通过这样的定义之后，Object3.prototype相当于是我们手动新建的一个对象，prototype中自带的constructor属性没了。因此我们还需要在字面量里手动加上constructor这个属性：
```
constructor: Object3,
```
不过，由于JavaScript规定手动创建的属性的Enumerable值为true，而constructor的Enumerable的值应该为false，因此我们可以用后文将会介绍的 *defineProperty()* 函数手动将其设置为true。

>Enumerable是属性的特性，表示能否通过for-in循环访问这个属性。其它属性的特性还包括Configurable（能否通过delet删除属性、能否修改属性的特性以及能否将数据属性变成访问器属性）、Writable（能否修改属性的值）、Value（包含属性的数据值）。

这种通过字面量创建原型的方法又称为重写原型对象，它的问题在于如果重写过程之前有一个实例已经通过这个原型的构造函数新建了出来，重写原型对象后这个实例的_proto_仍然指向原来的原型对象而不是这个新的原型对象。这是因为原型对象本身是一个对象，是通过地址访问的，实例的_proto_属性指向的是原来原型对象所在空间的地址，而重写原型对象相当于开辟的一块新的空间，并不会改变实例和原本远行对象的连接。

原型模式存在的问题是所有实例共享原型对象内的属性和方法，因为当一个实例改变某个引用类型的属性时，改变的实际上是这个原型对象内的属性的值，因此其它实例访问这个属性的时候也会得到修改后的值（基本类型不可改，所以不存在这个问题）。这样一来，实例就无法维护自己的引用类型的属性了。

# 组合使用构造函数和原型的模式

构造函数模式的问题在于每个实例的共享属性和方法独占一个空间，造成空间浪费。原型模式的问题在于没办法维护实例的非共享属性。因此，可以把这两种模式结合起来，共享属性和方法的继承通过原型实现，而非共享属性通过构造函数来实现。

```
function Object4(arg1, arg2) {
  this.attrA = arg1;
  this.attrB = [arg1, arg2];
}

Object4.prototype = {
  constructor: Object4,
  fun: function(){
    alert(this.attrA);
  }
}

var inst1 = new Object4(1, 2);
var inst2 = new Object4(3, 4);
alert(inst1.attrA === inst2.attrA); //false
alert(inst1.attrB === inst2.attrB); //false
alert(inst1.fun === inst2.fun); //true
```

# 其它创建对象的模式

除了以上几种方式之外，还有下面这些可以用来创建对象的模式。理解这些模式对后面理解原型链有很大的房租。

* **动态原型模式**

  ```
  function Object5(arg){
    this.attr = arg;
    if (typeof this.fun != "function"){
      Object.prototype.fun = function(){
        alert(this.attr);
      }
    }
  }
  ```
  这段代码在构造函数第一次被调用的时候会执行if语句内部的内容，为它的prototype创建一个名为fun的函数。之后的每次调用，由于fun函数已经存在了，因此if语句不会执行。这样写的好处在于将原型继承封装在构造函数内部实现，不过之后我们不能使用原型字面量来重写原型，否则已有的实例和新原型无法链接。

* **寄生(parasitic)构造函数模式**

  ```
  function Object6(arg){
    var o = new Object();
    o.attr = arg;
    o.fun = function(){
      alert(this.attr);
    }
    return o;
  }

  var inst = new Object6("attr");
  ```
  这种方式和工厂模式很像，所不同的是Object6此时是一个构造函数，实例需要用new来新建。它的好处在于如果我们想创建一个具有特殊属性或者方法的内置引用类型对象，例如创建一个包含新属性的数组，可以通过这种方法来实现。和工厂模式相同的是instanceof在寄生模式下也无法返回正确的值。

* **稳妥构造函数模式**
   > **稳妥对象(durable object)** 的概念由道格拉斯·克罗克福德发明，指的是没有公共属性，而且其方法也不引用this的对象。

  在一些对安全性有较高需求的环境下可以使用稳妥构造函数。
  ```
  function Object7(arg){
    var o = new Object();
    attr = arg;            //注意不要写o.attr = arg，否则inst.attr就能访问到它
    o.fun = function(){
      alert(attr);         //注意不要写this.attr = "..."，否则inst就可以通过fun修改它
    }
    return o;
  }

  var inst = Object7("attr");
  ```
  这种方式其实就是方法不使用this，以及私有变量不挂在返回的对象上的工厂模式（书上的例子少了一行变得很难理解）。除了调用fun函数，没有其它任何方法可以在外部访问对象的attr属性。

# 几个和对象属性和原型有关的函数

* *某个prototype.isPrototypeOf(某个实例)* 返回布尔值，表示某个原型是否是某个实例的构造函数的原型
* *Object.getPrototypeOf(某个实例)* 返回某个实例的原型
* *某个实例.hasOwnProperty(某个属性)* 返回布尔值，表示某个属性是否属于某个实例自己（属于原型则返回false）
* *for (var prop in 某个对象)* 循环遍历某个对象（包括从原型中访问到的）存在的可枚举属性
* *Object.keys(某个对象)* 返回这个对象（不属于原型）存在的可枚举属性组成的数组
* *Object.getOwnPropertyNames(某个对象)* 返回某个对象的所有属性
* *Object.defineProperty(某个对象, 某个属性, 属性特性的字面量)* 定义某个对象的某个属性的特性

由于篇幅关系，关于继承和原型链的部分放到下一篇博客中整理。
