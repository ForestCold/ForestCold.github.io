---
title: JavaScript中的typeof和强制类型转换
date: 2017-07-30 11:06:59
tags:
- typeof
- 强制类型转换
categories:
- JavaScript小知识
- 数据类型
---

``typeof的用法``    ``强制类型转换``    ``"==" 和 "==="``

<!--more-->

***

这一部分整理了JavaScript中关于typeof和强制类型转换的一些注意点，内容来源于慕课网的相关课程。

# typeof操作符

typeof是一个操作符而不是函数，可以通过下面两种语法进行使用：

```
typeof a;
typeof(a);
```
typeof操作符对于基础类型和引用类型的操作结果不同，关于基础类型和引用类型的区别请参照「JavaScript高级程序设计」 读书笔记系列的第⑶到第⑹篇。

```
  typeof undefined         // undefined
	typeof 'abc'             // string
	typeof 123               // number
	typeof true              // boolean
	type of {} / [] / null   // object
  type console.log         // function
```
对于未经初始化的变量和声明过未赋值的变量，typeof都会返回undefined值。而基础类型除了Null之外，typeof都会返回它们对应的类型。引用类型的操作结果分为两种，所有的函数返回function，其它引用类型返回object。Null是一个特例，尽管它是基础类型，却返回Object。特别需要注意的是，诸如String、Number和Boolean这样的基础包装类型（关于基础包装类型的细节，参见[「JavaScript高级程序设计」 读书笔记⑹](http://www.hanfeilin.com/2017/07/28/%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0-JS%E9%AB%98%E7%BA%A7-6/)
返回的是Object。

# 强制类型转换

JavaScript在以下场景中会执行强制类型转换操作：

* **字符串拼接**
  ```
  var a = 100 + 10 ;   //110
  var b = 100 + '10';  //'10010'
  ```
* **双等于"=="**
  ```
  100 == '100';        //true
  0 == ' ';            //true
  null == undefined;   //true
  ```
  "=="会将操作数强制转换成布尔值进行比较，所以如果要进行严格的比较，请用"==="。

* **if语句**
  ```
  var b = 100
  if (b) {
    ...               //会运行
  }
  var c = ' '
  if(c){
    ...               //不会运行
  }
  ```
  同样是强制将参数转换为布尔值。

* **逻辑运算符**
  ```
  console.log(10 && 0);       //0
  console.log(' ' || 'abc');  //'abc'
  console.log(!window.abc);   // true， window.abc是undefined
  ```
  判断一个变量会被转化为 true 还是 false的方法，只需要：
  ```
  var a = 100;
  console.log(!!a);
  ```

# "==" 和 "==="的区别

上文提到过，"=="会将左右操作数强制转换为布尔值进行比较。下图展现了这两者的区别。

![](https://raw.githubusercontent.com/ForestCold/image/master/typeof.png)

* 红色：===
* 橙色：==
* 黄色：<= 和 >= 同时成立，== 不成立
* 蓝色：只有 >=
* 绿色：只有 <=



图片来源于[Belleve的知乎回答](https://www.zhihu.com/question/31442029)。
