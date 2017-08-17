---
title: 数据类型分类和基本类型
date: 2017-07-29 14:06:58
tags:
- 数据类型
- 基本类型
categories:
- 读书笔记
- JavaScript高级程序设计
---

``变量类型``    ``数据类型``    ``基本类型``    ``值类型``    ``对应「JavaScript高级程序设计」中部分第三章和部分第四章``

<!--more-->

***

「JavaScript高级程序设计」的第三章讲述的是JavaScript中最基本的语法概念，主要包括数据类型、操作符和语句三个部分。而第四章的变量部分介绍了值类型和引用类型的区别。因此这篇文章读书笔记就把第三章中的基本数据类型以及第四章变量部分介绍的基本数据类型存储方式做了整理，除此之外包含了慕课网上针对JavaScript数据类型课程的一些笔记整理。

# 变量类型

JavaScript的变量是松散类型的，与C或Java等语言不同，JavaScript的变量统一用var操作符加上一个变量名来定义。需要注意的是，用var操作符定义的变量将成为定义该变量的作用域中的局部变量。当需要创建全局变量时，可以把var省略。下面来看一个书上的例子：

```
function test() {
    var message = "hi";
}
test();
alert(message);
```
这里的 *alert(message)* 语句运行时将会产生undefined的错误，因为 *test()* 函数执行之后局部变量message就被销毁了。

```
function test() {
    message = "hi";
}
test();
alert(message);
```
这段代码则会成功运行并弹出写有"hi"的警告框，因为此时的message是一个全局变量。尽管如此，尽量不要使用省略var的方式在局部作用域下定义全局变量。

# 数据类型

JavaScript中的数据类型分为以下两个大类：

> **基本（简单）数据类型:**  Undefined, Null, Boolean, Number, String
> **复杂数据类型:**  Object

其中，基本（简单）数据类型又称为值类型。复杂数据类型又称为引用类型，具体又分为object、function、array等，但它们的本质都是对象。*值类型和引用类型的叫法是对于数据的存储方式而言的。* 关于引用类型「JavaScript高级程序设计」的作者特意花了整整一个章节来介绍，足以证明它的重要性。

不过，这里我们先从基本数据类型谈起。

# 基本数据类型

基本数据类型又称为值类型，它的存储方式非常好理解，看以下代码：

```
var a = 1.23
var b = a
```
a和b的存储方式如下图所示：
![](https://raw.githubusercontent.com/ForestCold/image/master/du-3-1.jpg)

因此，当改变b的值，如将b赋值为2时，a的值并不会发生相应的更改。
更多关于类型存储的知识比较请参照[方应杭的知乎文章](https://zhuanlan.zhihu.com/p/22400319)。

## **undefined**
  当一个以var声明的变量未经初始化时它的值就是undefined。需要注意的一点是，如果一个变量连声明都没有，那么浏览器会报错。不过，对于typeof操作符而言，无论是声明过还没初始化的变量，还是根本未经声明的变量，返回的值都是undefined。
```
var a;
//var b;
alert(a);
alert(b);
```
  语句 *alert(a)* 执行之后会弹出写有undefined的警告框，而语句 *alert(b)* 执行后则会报错。

## **Null**
  null值表示了一个空对象指针，用typeof操作符将会返回object。 一般而言，如果一个将要保存对象的变量还未真正保存对象，最好将它的值设置为null，这样可以直接检查变量是否为null来判断是否保存了对象，同时可以与undefined进行区分。

## **Boolean**
  只有true和false两个值（注意要小写），但是任意类型的值都可以通过JavaScript中的``内置函数``*Boolean()* 转化为一个boolean值。"", 0, NaN, null和undefined是常见的被转换为false的值。

>补充知识：内置函数是一种数据封装对象，是不考虑环境，内置在JavaScript语言中的函数。
>JavaScript中的内置函数包括： Object, Array, Boolean, Number, String, Function, Date, Regexp, Error
>*定义来自慕课网相关课程，不确定是否正确，之后发现不正确立即修改*

## **Number**
  ``进制``  默认十进制，八进制在数字前加0，十六进制在数字前加0x，如果碰到数字超出进制范围的情况（如08），第一个0将被自动忽略，依旧作为十进制。数字经过计算之后得到的值都为十进制。

  ``科学计数法`` x + e/E + y，表示x \* 10的y次方。例如3.125e7表示31250000。

  ``浮点数计算的误差`` 0.1 + 0.2得到的数字并不是0.3，而是一个很接近于0.3的小数。0.15 + 0.15则能够得到0.3。这是IEEE754的锅，不关JavaScript的事。

  ``最值`` 和JAVA中的最值不同JavaScript中的最大值用Number.MAX_VALUE表示，相应的，最小值用Number.MIN_VALUE表示。超过范围的值用Infinity（或Number.POSITIVE_INFINITY）和-Infinity(或Number.NEGATIVE_INFINITY)表示，这两个值不能参与计算。判断是否无穷，可以用 *isFinite()* 函数。

  ``NaN`` 表示本来要返回数值的操作数未返回数值的情况，不影响其它代码的执行。 isNaN函数对将所有不能转换为数值的输入返回true, 要注意的是数值字符串（如"10"）或boolean值（如true）是可以被转换为数值的。isNaN函数同样适用于对象，首先判断对象的 *valueOf()* 返回的值，不能转化为数值的话再调用 *toString()* 再测试一次。

  ``数值转换`` *Number()*, *parseInt()* 和 *parseFloat()* 三个函数可以进行字符串转换，它们之间的区别有些微妙，具体见下表：

  ![](https://raw.githubusercontent.com/ForestCold/image/master/du-3-3.png)

## **String**
   String是由双引号或单引号包起来的字符串。

  ``长度``  通过 *.length()* 方法可以返回字符串的长度（注意如果包含双字节字符，返回的长度不一定正确）。这里复习一下，Java中返回字符串长度需要用 *.length()*，返回数组长度时才用 *.length*，不要混淆。另外，包含转义字符的字符串计算长度的时候算的是转义后的字符串长度。

  ``不可变性``字符串一旦被创建即不可改变。当改变一个字符串变量时实际上创建了一个新字符串赋值给变量，再将原来的字符串销毁。

  ``转换为字符串``*text.toString()方* 法和 *String(text)* 方法可以将一个值转化为字符串。*toString()* 方法不能处理null和undefined, 不过可以传递一个参数作为进制数，就和前面提到的parseInt类似。而 *String()* 方法会将null转化为"null"，将undefined转化为"undefined"，其它与 *toString()* 方法相同，不过没有参数。

由于篇幅问题，复杂数据类型（即引用数据类型）将放到下一篇读书笔记中进行介绍。
