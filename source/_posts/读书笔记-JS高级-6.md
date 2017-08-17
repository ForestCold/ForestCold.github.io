---
title: 引用类型中的Date，RegExp，基本包装类型和单体内置对象
date: 2017-07-29 20:06:59
tags:
- 数据类型
- 引用类型
- 正则表达式
- 基本包装类型
categories:
- 读书笔记
- JavaScript高级程序设计
---

``复杂类型``    ``引用类型``    ``日期Date``    ``正则表达式RegExp``    ``基本包装类型``    ``String``    ``Boolean``    ``Number``    ``单体内置对象``    ``对应「JavaScript高级程序设计」中部分第四章和第五章``

<!--more-->

***

接着上一篇笔记中提到的的对象Object、数组Array和函数Function，这篇笔记开始分析日期Date、正则表达式RegExp和其它一些复杂数据类型。

# **Date**
Date数据类型在前端的编写中非常常用，所以一定要理解它的内涵。Date数据类型保存的实际上是从1970年1月1日零时开始所经历的毫秒数，后台的计算过程也是将日期转化为毫秒数进行计算。构造一个Date对象和之前构造Object或Array对象类似：
```
var now = new Date() //参数为空时自动传入当前时间（打这行代码的时间，不是执行这行代码的时间）
```
其中，参数要求传入的是毫秒数，而不是其它时间格式。将其它时间格式转换为毫秒数有两种方法： *Date.parse()* 和 *Date.UTC()* 。前者的输入参数为一个表示时间的字符串，接收以下几种格式：
  * **月/日/年**, 如3/31/1994
  * **英文月名 日， 年**, 如March 31, 1994
  * **英文星期 英文月名 日 年 时：分：秒 时区**, 如Thur March 31 1994 13:52:00 GMT+0800
  * **YYYY-MM-DDTHH：mm:ss.sssZ**, 如1994-03-31T13:52:00

而 *Date.UTC()* 接收的参数分别是年份，基于0的月份，天数，小时数（0到23），分钟数，秒及毫秒。
获得执行某行代码时的时间，可以用 *Date.new()* 函数，注意和构造函数相区别：
```
var time = Date.now();
var time = +new Date(); //在不支持Date.now()函数的浏览器中可以替代使用
```
*Date.now()* 可以用来计算某个函数的执行时间，代码很简单就不写了。
要将毫秒数转换为字符串格式，可以用每个对象都有的 *toString()* 和 *toLocaleString()* 函数，它们在转换时间上的效果基本相同。
> 要注意的是 *valueOf()* 函数返回的是时间的毫秒数，因此可以用来比较两个时间的大小。

除了 *toString()* 和 *toLocaleString()* 两个函数之外， Date还提供了其它一系列函数来按照不同的格式将毫秒数转化为字符串，包括 *toDateString()*，*toTimeString()*，*toLocaleDateString()*，*toLocaleTimeString()* 和 *toUTCString()*。这些方法的输出因浏览器而异，所以这里没法给出统一的表格，要用的时候可以自己试试需要什么样的格式。
除此之外，Date还提供其它一些方法让用户方便地对时间进行操作，篇幅关系这里不一一列举，请参照[W3CSchool上对于Date的说明](http://www.w3school.com.cn/jsref/jsref_obj_date.asp)。

# **RegExp**
在了解RegExp类型之前，首先需要了解``正则表达式``。
> **正则表达式** 通常被用来检索或替换符合某个规则的文本。在编写编译器的词法分析器部分时，我们也需要用到正则表达式。常见的正则表达式元字符、量词等参见[W3CSchool上对于正则表达式的说明](https://www.w3cschool.cn/regexp/zoxa1pq7.html)。

JavaScript中的正则表达式有两种写法，分别是字面量方法：
```
/* 其中/[bc]a/部分被称作pattern，代表正则表达式，i被称作flags，代表标志位。标志位包含g（表示全局模式）、i（表示不区分大小写）和m（表示多行模式）三种模式。*/
var expression = /[bc]at/i;
```
以及本部分阐述的使用RegExp构造函数的方法：
```
/* RegExp的两个参数分别对应字面量表示法的pattern和flags */
var expression = new RegExp("[bc]at", "i");
```
以上两句话构造出来的正则表达式是相同的，不同的地方在于以下两点：
  * RegExp传入的正则表达式参数如果包含元字符，需要进行双重转义，而字面量模式只要转义一次。例如，``/\[bc\]at/`` 的字面量模式，作为 *RegExp()* 的参数时，需要转义成 ``"\\[bc\\]at"`` 。
  * 字面量模式构造的正则表达式始终共享一个RegExp实例，而RegExp构造函数每次创建的实例都是一个新的实例。书上给了一个"catastrophe"的例子，：
  ```
  var re = null;

  for (var i = 0; i < 10; i++){
    re = /cat/g;
    re.test("catastrophe");
  }

  for (var i = 0; i < 10; i++){
    re = new RegExp("cat", "g");
    re.test("catastrophe");
  }
  ```
  书上描述的是在第一个循环体中，第一次循环能够打印出true， 而第二次循环是从第一次找到的字符末尾开始往后查询的，所以结果为false。第三次又从头查询，结果又为true......而在第二个循环中，每次打印的结果都为true，因为每次re都是一个新建的对象，都从头开始进行查询。然而我发现这个例子在chrome55.0上运行的结果并不像书中说的那样，查阅资料得知:
  > 在ECMAScript 5中规定：使用正则表达式字面量跟使用RegExp构造函数一样，每次调用都创建新的实例。所以两个循环在现代浏览器中都弹出true。

  所以实际上在支持ECMAScript5的浏览器中，两个循环输出都是true。怎么才能达到书中作者所描述的效果呢？我们可以更改一下代码：
  ```
  var re = null,
      result = null;

  re = /cat/g;
  for (var i = 0; i < 10; i++){
    result = re.test("catastrophe");
    console.log([result, re.lastIndex]);
  }
  ```
  这个例子在chrome55.0中的运行结果如下图所示：
  ![](https://raw.githubusercontent.com/ForestCold/image/master/du-5-1.png)
  从这个结果中可以看出，由于re只有一个实例，因此在奇数次循环中都是从第0位开始查询，找到第一个"cat"即"catastrophe"的前三个字母，返回true，此时的lastIndex属性为3。而偶数次循环都是从lastIndex属性 + 1向后查询，直到"catastrophe"的末尾都没有单词"cat"出现，所以返回false，此时lastIndex又更新为了0，代表又回到了"catastrophe"单词的头部。

RegExp主要包含 *exec()* 和 *test()* 两个常用函数。
  * *exec()* 函数专门为捕获组而设计。其接受一个字符串参数，返回一个数组，包含index和input两个额外属性。其中index表示项在字符串中的位置，input表示应用正则表达式的字符串(就是exec的输入参数)。
  > **什么是捕获组？** 在正则表达式中，可以进行“分组”，用一对圆弧括号括起来的表达式(a)就是一个分组。将这样的分组保存下来，可以方便后续的操作。有了捕获组就有非捕获组，在左括号后面加上?:所构成的表达式(?:a)为非捕获组，即不能被捕获输出。

  *exec()* 返回的数组的第0项表示与模式匹配的字符串，之后的每一项表示一个匹配组。请看以下例子：
  ```
  var text = "mom and dad and baby";
  var pattern = /mom( and dad( and baby)?)?/gi;
  var matches = pattern.exec(text);

  console.log(matches.index); //0
  console.log(matches.input); //"mom and dad and baby"
  console.log(matches); //["mom and dad and baby", "and dad and baby", "and baby"]
  ```
  这个例子中，text字符串包含两个被圆括号括起来的捕获组，分别是"and dad and baby"和"and baby"。因此 *exec()* 函数返回的数组中第二项和第三项分别存放了这两个捕获组。而数组第一项存放的是和这个模式所匹配的第一个字符串。
  当设置全局模式g时，正则表达式的lastIndex属性会被更新，意味着每次都从上一次的检测结尾开始检测。而当不设置全局模式时，lastIndex属性始终为0，意味着每次都从头开始检测。
  >**lastIndex属性：** 这是正则表达式模式的一个属性，和被检测的字符串无关，表示下一次开始检测的位置。如果某一次检测到字符串结尾仍旧没有匹配项，那么将其更新为0。对于不设置g模式的正则表达式而言，这个属性没有意义。除了lastIndex属性之外，正则表达式还包含其它一些记录匹配信息的属性，详细内容请见[W3CSchool对正则表达式的介绍](http://www.w3school.com.cn/jsref/jsref_obj_regexp.asp)（第四版书上的很多属性现在都没有了，还是以w3cschool的属性为准）。

  * *test()* 接受一个字符串参数，模式与该参数匹配返回true，否则返回false。这个函数相对而言比较简单，这里不贴代码。
   另外，*toLocaleString()* 和 *toString()* 方法都返回正则表达式的字面量，而 *valueOf()* 则返回其本身。

# **基本包装类型**
  >基本包装类型是一种特殊的引用类型，同时也具有和基本类型相对应的特殊行为。JavaScript中的基本包装类型包括Boolean，Number和String。每当读取一个基本类型时，后台都会创建一个基本包装类型，因此基本类型能够使用基本包装类型的方法。建议永远不要使用new去显示地创建基本包装类型，因为会引起混乱，让别人不知道你到底想建个基本类型还是对象。

  * **基本包装类型和基本类型有什么区别？**
  基本类型也可以使用基本包装类型的方法，并且在被读取时会自动生成一个基本包装类型。然而，使用typeof测试基本类型时返回的是对应的基本类型，测试基本包装类型的时候返回的是Object。并且，所有基本包装类型的对象在转换为布尔值时都为true。另外，基本类型不能添加自定义属性，基本包装类型由于属于引用类型，所以可以添加自定义属性。

  * **基本包装类型和普通的引用类型有什么区别？**
  书上的描述是引用类型和基本包装类型的主要区别在于对象的生存期，然而我觉得这个描述并不准确。这里的基本包装类型指的是在读取基本类型时自动创建的基本包装类型，而不是我们显示地new出来的基本包装类型。对于显示new出来的基本包装类型，实际上对象的生存期和引用类型并没有区别。而对于自动创建的基本包装类型，其实代码表现还是基本类型，所以对象在执行的瞬间被销毁。

## **Boolean类型**
  理论上永远不要使用Boolean类型，原因在于它被转换为布尔值时永远为true，这样就丧失了它原本的逻辑意义。

## **Number类型**
  Number类型包含三个和精度有关的方法，分别是 *toFixed()*（显示几位小数，注意当数字是基本类型的时候是不能原处更改的），*toExponential()*（以指数形式表示）和 *toPrecision()* （参数表示不包括指数部分的位数，后台会自动调用最合适的函数）。

## **String类型**
  String类型和Array类型有不少相似的属性和方法。String类型也有表示长度的length属性、用作拼接的 *concate()* 方法（普遍用+代替）、获取下标的方括号方法（也可以用等价的 *charAt()* 函数或者返回字符编码的 *charCodeAt()* 函数，*fromCharCode()* 函数的过程相反：将一连串的字符编码作为输入，返回对应的字符串）、获取子字符串的 *slice()* 方法（*subString()* 方法和它很像，只不过在处理负数参数时 *subString()* 会将负数转为0。另一个和它类似的方法是 *subStr()* 方法，它的第二个参数为要截取的字符串长度， 并且在遇到负数参数时会将第一个负参数加上字符串长度，第二个负参数转换为0）、查找子字符串的 *indexOf()* 和 *lastIndexOf()* 方法。 以上这些方法的具体细节请参照我上一篇博客[「JavaScript高级程序设计」 读书笔记⑸](http://www.hanfeilin.com/2017/07/28/%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0-JS%E9%AB%98%E7%BA%A7-5/)的内容。

  除此之外， String类型还包含能够将头尾的空格都去掉的 *trim()* 方法、进行大小写转换的 *toLowerCase()* 和 *toUpperCase()* 方法，用来进行比较的 *localeCompare()* 方法（返回0代表相等，正数代表字符串参数排在调用者前面，负数反之）。

  在String中，有几个方法专门用来进行模式匹配。*text.match(pattern)* 实质上和 *pattern.exec(text)* 是相对应的，返回值也相同。*search(pattern)* 方法可以返回字符串中第一个匹配项的索引。

  比较复杂的是 *replace()* 方法，它可以替换符合条件的子字符串。*replace()* 方法有两个参数，第一个参数是字符串或正则表达式，如果是字符串那么只替换第一个找到的子串，要替换所有符合条件的子串，需要将参数设置为模式为g（全局）的正则表达式。第二个参数可以是要替换成的字符串或一个函数，这个函数提供更加精确的替换法则，如下所示：
  ```
  /*HTML代码中对<>"&这四个符号的转义*/
  Function htmlEscape(text) {
    return text.replace(/[<>*&]/g, function(match, pos, originalText)){ //function的三个参数分别是模式的匹配项、匹配项的位置和原始字符串
      switch(match) {
        case "<" :
          return "&lt";
        case ">" :
          return "&gt";
        case "&" :
          return "&amp";
        case "\"":
          return "&quot";
      }
    }
  }
  ```
  最后也是我们最常用的是String的 *split()* 方法，用来将字符串按照某种规则分割，并返回包含所有分割项的数组。 *split()* 方法可以使用字符串或正则表达式作为输入。

# **单体内置对象**
> **单体内置对象** 是由ECMAScript实现提供的、不依赖与宿主环境的对象，这些对象在ECMAScript程序执行之前就已经存在了。

JavaScript中的单体内置对象包括Globe对象和Math对象。Globe对象正如它的名字而言，是个全局对象。JavaScript中没有全局变量或全局函数概念，因此所有看起来像是全局变量或全局函数的东西全部都是Globe对象的属性和方法。我们常说的window对象实际上实现了Globe对象的功能，但它还包含很多其它功能和任务，可以说Globe对象是window对象的一个部分。我们不能直接访问Globe对象，但我们可以直接访问window对象。

ECMAScript中最强大的一个方法 *eval()* 能够将参数解析为ECMAScript脚本来执行，尽管强大，但是尽量不要随意使用它，否则容易遭遇恶意用户的代码注入。

Math对象中的技巧主要包含两点。首先是求一个数组的最大或最小值，可以使用如下代码：
```
var max = Math.max.apply(Math, array);
```
还记得我们在[「JavaScript高级程序设计」 读书笔记⑷](http://www.hanfeilin.com/2017/07/28/%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0-JS%E9%AB%98%E7%BA%A7-4/)中提到的 *apply()* 方法吗？这个方法接收两个参数，第一个参数为函数的作用域，第二个参数为一个参数数组。因此这么编写代码巧妙地将数组传入了 *max()* 函数中。如果直接使用 *Math.max(array)* ，则会返回NaN的结果。

第二个技巧在于求一组范围内的随机数，在编写可视化项目时，这个方法对于颜色的选择十分有用。假设我们要从lowerValue和upperValue之间选取一个随机数，那么我们可以编写如下代码：
```
function selectFrom(lowerValue, upperValue) {
  var choices = upperValue - lowerValue;
  return Math.floor(Math.random() * choices + lowerValue);
}
```
其中， *Math.floor()* 表示向下取整，对应的还有 *Math.ceil()* 表示向上取整，*Math.round()* 表示四舍五入。而 *Math.random()* 表示在0和1之间选取一个随机数。  
