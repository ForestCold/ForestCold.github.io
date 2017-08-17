---
title: 引用类型中的数组
date: 2017-07-29 14:07:00
tags:
- 数据类型
- 引用类型
- 数组
categories:
- 读书笔记
- JavaScript高级程序设计
---

``复杂类型``    ``引用类型``    ``数组Array``    ``对应「JavaScript高级程序设计」中部分第五章``

<!--more-->

***

还记得上一篇读书笔记整理的是关于对象Object和函数Array的相关知识吗？ 本篇笔记接着介绍另一个常用的引用类型————数组Array。数组类型包含非常多的属性和方法，因此单独开辟了一篇笔记来进行讲解。

# **Array**  
  ECMAScript中数组的每一项都可以保存任何类型的数据，并且大小可以动态调整。

## **定义数组**
  数组的创建方法和Object非常类似，第一种是通过构造函数来创建：
  ```
  var value = new Array();
  var value = new Array(3);
  var value = new Array("1","2","3");
  //new 操作符也可以被省略
  ```
  以上代码都很简单，这里不赘述。另一种方法是通过字面量来构建：
  ```
  var value = [1, 2, 3];
  var value = [1, 2, ]; //IE8以下版本会生成一个长度为3的数组，且第三个元素为undefined。其它浏览器生成只包含元素1和2的长度为2的数组。
  var value = [, , ,]; //IE8以下版本会生成一个长度为4的数组，其它浏览器生成长度为3的数组。
  ```

## **索引数组**
  JavaScript中的数组不会向上越界，如果索引中的值超过了数组的长度，则数组自动更新长度为索引值+1。如以下代码所示：
  ```
  var arr = [1, 2];
  console.log(arr.length); //2
  arr[5] = '9';
  console.log(arr.length); //6，其中第3到第5项的值为undefined
  ```
  数组的length属性不是只读的，因此可以通过改变length的值增加或移除数组末尾的项。如：
  ```
  var arr = [1, 2];
  arr.length = 1;
  console.log(arr[1]); //undefined
  ```

## **检测数组**
  使用instanceof操作符能够方便地检测某个对象是不是数组：
  ```
  if(value instanceof Array)
     ...
  }
  ```
  但是如果网页中包含多个框架，拥有多个Array构造函数，instanceof的使用会引起混乱。因此我们可以使用新增的 *Array.isArray()* 方法：
  ```
  if (Array.isArray(value)){
    ...
  }
  ```

## **转换数组**
  每个对象都有 *.toLocaleString()*（返回对象的字符串表示，该字符串与执行环境的地区相对应）、*.toString()*(返回对象的字符串表示)和 *.valueOf()* (返回对象的字符串、数字或布尔值表示，通常与 *toString()* 返回的值相同)三个方法。数组的每一项会被用逗号隔开，组成一个字符串。
  >*alert()* 函数会在后台调用 *toString()* 方法，所以打印出的是数组每一项用逗号隔开的值，而不是数组的地址。

  对于数组而言，需要注意的是 *toLocaleString()* 方法调用的是数组每一项的toLocaleString方法，而 *toString()* 方法调用的则是数组每一项的toString方法。
  *.join()* 方法可以更改数组项之间的分割方法（例如用"|"代替","进行分割）。
  >null项和undefined项在以上方法的返回结果中以空字符串表示。

## **栈方法**
  数组提供了 *push()* 和 *pop()* 方法来模拟栈的行为。别问我栈是什么→\_→。其中，*.push()* 方法可以有很多参数，一次放在数组末尾，其返回值为修改后的数组长度。

## **队列方法**
  队列沿用了栈的 *push()* 方法，向数组末尾添加元素。*.shift()* 方法可以移除数组中的第一个项并返回，同时数组长度减一。另外，数组还包含一个 *unshift()* 方法，和 *shift()* 方法相反，它向队列前端添加任意元素并返回修改后的数组长度。
  因此，使用 *push()*+*shift()* 的组合或 *pop()*+*unshift()* 的组合可以从头尾两端模拟队列。

## **重排序方法**
  数组中的 *.reverse()* 会反转数组，*.sort()* 方法按照升序对数组进行排序。需要特别注意的是，*sort()* 方法调用的是数组中每一项的 *toString()* 方法，因此实质上是对字符串类型的排序。
  ```
  var arr = [0, 1, 5, 10, 15];
  arr.sort();
  console.log(arr);//[0, 1, 10, 15, 5];
  ```
  这个例子中，为什么调用 *sort()* 方法之后顺序反而不对了呢？因为对于字符串"10"和字符串"5"而言，首先比较的是第一位的ASCII码，因为"1"是小于"5"的，所以"10"被排在了"5"的前面。因此，我们应该通过向 *sort()* 加入参数来定义它的排序方式。*sort()* 参数是一个比较函数，可以如下定义：
  ```
  /* 升序比较函数 */
  Function compare(value1, value2) {
    if (value1 < value2){
      return -1;
    } else if (value1 > value2){
      return 1;
    } else {
      return 0;
    }
  }
  ```
  对于数值类型或者 *valueOf()* 的返回值为数值类型的函数，比较函数的写法更为方便：
  ```
  Function compare (value1, value2){
    return value1 - value2;
  }
  ```

## **操作方法**
  *.concat()* 方法，参数为值或数组（也可以是值和数组混合），返回当前数组的副本，并在该副本后面连接上参数中的值或数组。
  ```
  var a = [1, 2, 3];
  var b = a.concat(4, [5, 6]);
  console.log(b); // [1, 2, 3, 4, 5, 6]
  ```
  *.slice(start, end)* 方法，如果只有start参数，则返回从start到数组结尾的副本。如果包含end参数，则返回从start到end-1的数组副本。如果start < end，返回一个空数组。如果start或end为负数，则加上数组长度直到值变为正数为止。
  ```
  var a = [1, 2, 3];
  var b = a.slice(-5, -1);
  console.log(b) // [2], 因为a.length = 3, -5 + 3 + 3 = 1, -1 + 3 = 2, 从第1位到第2 - 1位进行截取
  ```
  *.splice()* 这一方法可以删除、插入或替换数组中的任意项，并且返回被删除的所有项：
  ```
  var colors = ["red", "green", "blue"];

  /* 删除某几项，需要两个参数： 要删除的第一项位置和要删除的项数 */
  var removed = colors.splice(0, 1); //删除第一项
  console.log(colors); //["green", "blue"]
  console.log(removed); //["red"]

  /* 插入某几项，需要三个参数：起始位置、0（删除的项数）和要插入的项 */
  var inserted = colors.splice(1, 0, "yellow");//在第一项上插入
  console.log(colors); //["red", "yellow", "green", "blue"];
  console.log(inserted); //[]

  /* 替换某几项，需要三个参数：起始位置、要删除的项数和要插入的项 */
  var replaced = colors.splice(1, 1, "yellow");
  console.log(colors); //["red", "yellow", "blue"];
  ```

## **位置方法**
  *.indexOf()* 从前往后查找数组，lastIndexOf()从后往前查找数组，都返回要查找的项在数组中的第一个被查找到的位置。当找不到时返回-1。这两个函数都有两个参数，分别是待查找的项和查找起始点。

## **迭代方法**
  数组共有五个迭代方法，分别是 *every()*，*filter()*，*forEach()*，*map()*，和 *some()*。每个方法都有两个参数，分别是要在每项上运行的函数和运行该函数的作用域对象（第二个参数影响this的值，我们在后面学习原型的时候再详细讨论，现在先把第二个参数忽略）。对于第一个参数中的函数，它本身又有三个参数，分别是某项、该项的位置和数组本身。
  先来看最简单的 *every()* 和 *some()* 两个函数：
  ```
  var arr = [1, 2, 3, 4];
  /* 当每项都返回true时，every()返回true */
  var everyResult = arr.every(function(item, index, array){
    return item > 2;
  })
  console.log(everyResult); //false

  /* 当至少一项都返回true时，some()返回true */
  var someResult = arr.some(function(item, index, array){
    return item > 2;
  })
  console.log(someResult); //true
  ```
  *filter()* 返回所有符合要求的项组成的数组：
  ```
  var filterResult = arr.filter(function(item, index, array){
    return item > 2;
  })
  console.log(filterResult); //[3, 4]
  ```
  *map()* 返回的也是一个数组，是对每一项进行操作后取得的新的项组成的数组：
  ```
  var mapResult = arr.map(function(item, index, array){
    return item * 2;
  })
  console.log(mapResult); //[2, 4, 6, 8]
  ```
  *forEach()* 函数没有返回值，就相当于对数组每一项进行了一个for循环操作。

## **归并方法**  
  归并方法的语法和迭代方法有些类似，都是通过遍历数组的项来执行某些操作。JavaScript提供 *reduce()* 和 *reduceRight()* 两种归并方法，分别从左到右和从右到左进行归并。包含两个参数：在每一项上调用的函数和作为归并基础的初始值。其中调用函数又包含四个参数：前一个值，当前值，项的索引和数组对象。以下代码可以返回一个数组所有项的和：
  ```
  // prev最开始是第一项，即1，cur最开始是第二项，即2
  var reduceResult = arr.reduce(function(prev, cur, index, array){
    return prev + cur;
  })
  console.log(reduceResult); //10
  ```
在下一篇读书笔记中，将介绍剩余的一些引用类型。
