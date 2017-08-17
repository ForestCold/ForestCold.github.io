---
title: 原型链和寄生组合继承
date: 2017-07-31 20:04:34
tags:
- 继承
- 原型
- 面向对象
categories:
- 读书笔记
- JavaScript高级程序设计
---

``继承``    ``原型链``    ``组合继承``    ``原型式继承``    ``寄生式继承``    ``寄生组合继承``    ``对应「JavaScript高级程序设计」中部分第六章``

<!--more-->

***

上一篇博文讲到了原型，这一篇博文在此基础上分析原型链。顾名思义，原型链就是像链条一样环环相扣的原型继承方式，通过原型链和构造函数继承，我们得到了组合继承。原型式继承是对原型链继承方式的封装，寄生式继承是模拟工厂模式的继承方式，而寄生组合继承是组合继承的升级版，被开发人员成为最完美的继承方式。

# 原型链

假设我们有一个构造函数Foo，由它实例化了一个对象f，f的_proto_属性将指向Foo.prototype。这时，如果我们将Foo.prototype设为另一个构造函数Foooo()的实例，当我们在f中查新一个方法时，假设查找不到就在Foo.prototype中查找，再查找不到就在它的构造函数Foooo()的原型中查找，这样就形成了一个三层结构的原型链。
对于最上层的Foooo，它的构造函数是Object，因此Object作为所有原型链的顶端。

```
var Foooo() = function{      //Foooo的构造函数
  ...
}

var Foo() = function{         //Foo的构造函数
  ...
}

Foo.prototype = new Foooo();  //Foo的原型等于Foooo的一个实例
f = new Foo();
```
通过以上代码构建出了一条f -> Foo -> Foooo -> Object的原型链，如下图所示：

![](https://raw.githubusercontent.com/ForestCold/image/master/du-8-1.jpg)

虽然原型链很方便，但是它和上一篇博文提到的原型创建对象一样有一个问题，就是所有的属性和方法都是共享的。

# 原型链的一个例子

下面代码是慕课网相关课程提供的实际应用中的一个原型链例子，在DOM中做查询操作：
```
function Elem(id) {
  this.elem = document.getElementById(id);     //找到id为输入参数的元素
}

Elem.prototype.html = function(val) {
  var elem = this.elem;
  if(val) {                                    //如果传入了参数，把elem内部的html换成参数
    elem.innerHTML = val;
    return this;                               //方便链式操作
  }
  else {
    return elem.innerHTML;                     //否则返回elem内部的html代码
  }
}

Elem.prototype.on = function (type, fn){       //绑定一个事件监听器
  var elem = this.elem;
  elem.addEventListener(type, fn);
}

var div1 = new Elem('div1');
console.log(div1.html());

div1.html('<p>hello</p>');
div1.on('click', function() {
  alert('click');
})
```
这段代码只有div1 -> Elem -> Object三层，并且Object层还是隐式的，因此我不知道算不算真正意义上的原型链。不过确实是原型继承在实际应用中的一个好例子。

# 原型式继承

原型式继承又是那个很牛逼的道格拉斯·克罗克福德提出来的，其本质是：
```
function object(o) {
    function F() {};     //创建一个临时的构造函数
    F.prototype = o;  
    return new F();      //返回它的实例
}

var protoo = {
  ...
}

var inst = object(protoo);
```

实际上就是我们把原型链中构造函数和原型链接的过程封装了起来。ECMAScript5定义了 *Object.create()* 方法来标准化这一过程。 这个方法有两个参数，第一个参数就是上述代码中的o，第二个参数是实例新增的属性。

```
var inst = Object.create(o, {
  attr :{
    value: "attr"
  }
});
```

# 构造函数继承

构造函数继承使用 *call()* 或 *apply()* 函数进行：
```
function Foooo(attr){
  this.attr = attr;
}

function Foo(){
  Foooo.call(this);
}

var inst = new Foo("attr");
```
使用构造函数的优势是实例可以向祖先类中传递参数。不过，它的缺点和上一篇博文中的构造函数创建对象模式相同，就是无法做到复用祖先类中的方法。

# 组合继承

组合继承（combination inheritance），又叫伪经典继承，组合了原型继承和构造函数继承的优点，参考上一篇博文中的组合模式。这是JavaScript中最常用的集成模式，并且instanceof和 *isPropertyOf()* 都能识别出正确的实例类型。
```
function Foooo(args) {
  this.attr = args;
}

Foooo.prototype.funA = function(){
  alert(this.attr);
}

function Foo(args){                   //通过构造函数继承属性
  Foooo.call(this, args);
}

Foo.prototype = new Foooo();          //通过原型链继承方法
Foo.prototype.constructor = Foo;

var inst = new Foo("attr");
```
这么做，就可以把属性和方法的继承区分开来了。不过，这种方式有一个问题，当调用 *Foo()* 构造函数时，我们实际上调用了一次 *Foooo()* 构造函数，而Foo在进行原型继承的时候又通过new调用了一次 *Foooo()* 构造函数。这样Foo.prototype中其实包含了Foooo构造函数中的属性（如attr），但这些属性是永远访问不到的，因为当我们在inst中查找attr属性的时候，我们首先查找的是实例内部是否包含这些属性。由于属性是通过构造函数继承的，所以我们一定能找到attr属性，而不需要通过Foo的原型。

# 寄生继承

这种方式和原型式继承以及寄生模式创建对象相类似：创建一个用于封装继承过程的函数，在函数内部以某种方式增强对象，最后再将这个对象返回。
```
function createObject(args) {
  var clone = object(args);        //这里的object函数就是原型式继承中的object函数
  clone.fun = function(){
    ...
  }
  return clone;
}

var protoo = {
  ...
}

var inst = createObject(protoo);
```
这种方式的问题和构造函数类似，每新建一个实例就产生一个新的fun函数，造成内存空间利用率底下。

# 寄生组合继承

寄生组合继承是所有继承方式中的压轴Boss，被认为是最理想的继承方式。它基于组合继承，只不过prototype内部不再存在冗余属性。这点是如何做到的呢？还记得组合继承里我们是通过这个方式继承原型链的：
```
Foo.prototype = new Foooo();
```
正是这句该死的代码造成了Foo.prototype的属性冗余，因为它包含了Foooo构造函数中的属性，而这些属性由于构造函数继承的存在永远不会被访问。改进思路就是让Foo.prototype只包含Foooo.prototype中的属性，而不包含Foooo构造函数中的属性。因此我们可以新建一个原型拷贝函数：
```
function inheritPrototype(subType, superType){
  var prototype = object(superType.prototype);
  prototype.constructor = subType;
  subType.prototype = prototype;
}
```
这个函数手动创建了subType的原型，其中object函数和前文原型式继承中的object函数是一样的。首先新建一个prototype实例，它是superType.prototype的一个实例，因此它继承了superType.prototype中所有的属性和方法。再将这个实例的constructor属性指向subType，并将subType的prototype属性指向它，就完成了一次从superType到subType的深拷贝。

之后，我们把上面那句该死的代码改成：
```
inheritPrototype(Foo, Foooo);
```
就可以了。这样Foo.prototype中就不再包含Foooo构造函数的attr属性，最大限度地节省了时间和空间开销。
