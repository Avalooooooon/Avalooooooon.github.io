---
title: leetcode刷题常用技巧
updated: 2022-09-27 11:42:15
date: 2022-09-27 11:42:15
tags: [leetcode]
categories: [leetcode]
---

编程语言为js。
## 数值操作
### 取整
1. 截取整数+强制类型转换：```parseInt(a)```。依次将字符串中从左到右的数字部分转为整数,一旦转换失败，返回NaN。正数向下取整，负数向上。
可以接收第二个参数，为进制数。
2. 移位运算符：```>>```或```<<```。以```>>```为例，```(right - left) >> 1```以为在二进制下右移一位并取整。正数向下取整。

### 防止溢出
1. 用```mid = left + ((right - left) / 2)```代替```mid = (left + right) / 2)```。
2. 

### 常用常量
1. js的最大安全整数：```Number.MAX_SAFE_INTEGER```。


## 数组方法
一种方法可以有很多种使用方式，这里只列出最常用的。
数组使用扩展运算符```foo = { ...['a', 'b', 'c'] }```后的结果为```{0: "a", 1: "b", 2: "c"}```。如果扩展运算符后面是字符串，它会自动转成一个类似数组的对象。
通过```new Array(n).fill(value)```等方法进行初始化。

### 删除元素
1. 方法```array.splice(index,num)```：删除指定位置的元素，第一个参数为要删除第一项的位置，第二个参数为要删除的项数。返回值为删除内容，array为结果值。改变原数组。
2. 方法```array.shift()```：删除数组的第一个元素。返回值为第一个元素的值。改变原数组。

### 增加元素
1. 方法```array.fill(value, start, end)```：将一个固定值替换数组的元素。后两个参数可选，不写的话默认给整个数组填充。
2. 方法```array.push(item1, item2, ..., itemX)```：添加元素到末尾。改变原数组。
3. 方法```array.unshift(item1, item2, ..., itemX)```：添加元素到开头。改变原数组。
4. 方法```Array.concat(arr2,arr3,num)```：将数组或值添加到结尾。不改变原数组。
5. 使用扩展运算符```...```，如```arr2 = [...arr1,4,5,6]```：将一个数组（和其他元素）插入到另一个数组中。

### 查找元素
1. 方法```array.includes('a')```：判断数组是否包含某元素，返回true 或者false。

### 遍历元素
1. 方法```array.reduce(function(prev,cur,index,arr){...},init)```：对数组每个元素逐个执行一个reduce函数，返回值为结果。循环遍历能做的，reduce都可以做，比如数组求和、数组求积、数组中元素出现的次数、数组去重等等。
> prev：必需。累计器累计回调的返回值; 表示上一次调用回调时的返回值，或者初始值 init
> cur：必需。表示当前正在处理的数组元素
> index可选，表示当前正在处理的数组元素的索引，若提供 init 值，则起始索引为- 0，否则起始索引为1；arr可选，表示原数组；init可选，表示初始值。

2. 



### 提取指定位置的元素
1. 方法```array.alice(start,end)```：返回值为下标[start，end)位置上的新数组。改变原数组。**注意包括start，但不包括end。**

### 数组与字符串互相转化
1. 方法```str.split(" ")```：把字符串分割成字符串数组。可以传一个参数作为切割标志，不传则默认切割每一个字符。


## map方法
通过```new Map()```等方法进行初始化。
1. 查看key的value值/key是否存在：```map.set(tchar, map.has(tchar) ? map.get(tchar) + 1 : 1)```。


## 链表
注意———需要操作的节点是头节点时如何处理；链表结尾的判定：```currnode.next !== null```；链表中只有一个元素时如何处理；涉及到不容易想清楚的位置变换时记得画图，否则指针一多很容易乱。

使用js时，单链表可以如下定义：

```javascript
function ListNode(val, next) {
    this.val = (val===undefined ? 0 : val)
    this.next = (next===undefined ? null : next)
}
```
初始化：
```javascript
class Node {
    constructor(val, next) {
        this.val = val
        this.next = next
    }
}
var MyLinkedList = function () {
    this._size = 0
    this._head = null
    this._tail = null
};
```

### 虚拟头节点dummyhead
常定义一个指向真正头节点的虚拟头节点：```var dummyhead = new ListNode(0,head);dummyhead.next = head```。

### 环形链表
常涉及到数学运算，如判断是否有环和环入口时。
常用方法：快慢双指针。根据是否相遇、何时相遇进行判断。


## 算法
### 二分查找
> 关键字：有序数组、无重复元素、复杂度O(logn)

注意区间开闭。

### 双指针
> 关键字：数组链表字符串、不占用额外空间、保持相对顺序、复杂度O(n)

必须明确**快慢指针各自的具体含义**。比如快指针是在遍历中寻找满足条件的元素，慢指针是指向结果数组中存放满足条件的元素的下标（位置）。

### 滑动窗口
> 双指针的一种
> 关键字：不占用额外空间、保持相对顺序、复杂度O(n)

明确：是以滑动窗口的**起始位置还是终止位置**为for循环的标识。

### 