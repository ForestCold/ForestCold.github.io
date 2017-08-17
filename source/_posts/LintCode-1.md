---
title: LintCode做题整理 之 二分法
date: 2017-07-28 23:03:08
tags:
- 二分法
- 算法
categories:
- LintCode
---

``LintCode``    ``算法``    ``二分法``    

<!--more-->

***

二分法，顾名思义，就是在O(1)的时间复杂度内把一个范围为n的问题变成一个范围为n/2的问题，因此它的复杂度为O(logn)。

# 二分法模板
对于最简单的二分法题目，如:
>在一个排序数组中找一个数，返回该数出现的任意位置，如果不存在，返回-1[LintCode链接](http://www.lintcode.com/zh-cn/problem/classical-binary-search/)

我们可以直接敲模板：

```
while (start + 1 < end){
    int mid = start + (end - start) / 2;
    if (nums[mid] == target){
        return mid;
    } else if (nums[mid] < target) {
        start = mid;
    } else {
        end = mid;
    }
}

if (nums[start] == target){
    return start;
}

if (nums[end] == target){
    return end;
}
```

这个模板中有几个需要注意的细节：
* while循环中写成start + 1 < end可以避免复杂问题中的一系列错误，增加模板通用性。如后面提到的找lastpos的问题，如果写成start < end就会出现问题。具体出现什么问题在后面讨论lastpos的时候进行解释。
* 第二行中求mid的值最好按照模板的写法，如果写成mid = (start + end) / 2， 在极端情况下会造成overflow。
* 不要以为循环做完就万事大吉，此时如果没有返回mid，代表start = end - 1，并且start和end这两个位置还没进行过查询。所以在循环之后要补上对这两个位置的查询。

# firstpos和lastpos
熟悉模板之后，我们进阶一下，来看下面两道题：
>1.给一个升序数组，找到target第一次出现的位置，如果没出现过返回-1
>2.给一个升序数组，找到target最后一次出现的位置，如果没出现过返回-1 [LintCode链接](http://www.lintcode.com/problem/last-position-of-target)

这两道题就是最简单的firstpos和lastpos问题，我们只需要稍微修改模板即可。
对于firstpos问题，将模板第四行改成：
```
end = mid;
```
因为是查找第一个出现的位置，所以当mid匹配到target时，查询目标必然是它或存在于它的前面，因此把end变成mid。
对于lastpos问题同理，将模板第四行改成：
```
start = mid;
```
同时要将第12到14行和第16到18行进行对调。因为查找的是最后一个满足条件的target，所以当start = end - 1时，要先查询end的位置再查询start的位置。

这里说明一下为什么要写成start < end - 1。 因为整除是一个偏左运算，假设我们要在[2, X]这个数组中找到最后一个2，while中写成了start < end， 此时start为0，end为1，mid = start + (end - start) / 2 = 0 + (1 - 0) / 2 = 0，在if (nums[mid] == target)这个语句中我们按照道理要写start = mid，但是这么写start的值还是0，就造成了死循环。又不能写成start = mid + 1，因为假设X不是2，我们就错过了正确的解。而start < end - 1则避免了这个问题。

类似的问题还有 [错误代码版本](http://www.lintcode.com/problem/first-bad-version)（firstpos问题）
             [查找目标出现区间](http://www.lintcode.com/zh-cn/problem/search-for-a-range/)（firstpos和lastpos的结合）
             [目标出现次数](http://www.lintcode.com/zh-cn/problem/total-occurrence-of-target/)（同上）

# 查找最接近的值
更进一步，假设我们要在数组中找到最接近target的值，如：
>在一个排好序的数组 A 中找到 i 使得 A[i] 最接近 target [LintCode链接](http://www.lintcode.com/zh-cn/problem/closest-number-in-sorted-array/#)

那么我们要将模板的第三行改成：
```
if (Math.abs(A[mid] - target) <= Math.abs(A[mid - 1] - target)
    && Math.abs(A[mid] - target) <= Math.abs(A[mid + 1] - target)){
```
这句话的意思是当target距离mid的值比它距离mid - 1和mid + 1的值都来得小的时候，mid就是要找的值。之后第12行到第18行也要进行相应的修改。

类似问题还有 [查找最接近target的k个数](http://www.lintcode.com/zh-cn/problem/k-closest-numbers-in-sorted-array)（先查找到最接近的数，再用两个指针向左右扩展把k个数存入结果。）

# 在无限长度的数组中查找
本质和最简单的二分法没有区别，只需要先确定查找的上界。可以通过指数扩展的方法来确定，即设定一个K值，刚开始k = 1，之后每次k = k * 2，判断第K位与目标的大小。
类似问题 [在大数组中查找](http://www.lintcode.com/zh-cn/problem/search-in-a-big-sorted-array/)

其它的二分法问题都大同小异，这里就不赘述啦。
