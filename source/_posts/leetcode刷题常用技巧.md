---
title: leetcode刷题常用技巧
tags:
  - leetcode
categories:
  - leetcode
updated: 2022-09-27 11:42:15
date: 2022-09-27 11:42:15
---



编程语言为js。


## 字符操作
### 编码转换
1. 转换为Unicode：```stringObject.charCodeAt([index])```，返回字符串中特定位置的字符的Unicode值。如果不提供index参数，则 charCodeAt() 方法将使用 0 作为默认值。常结合```var basecode = "a".charCodeAt()```一起使用。


## 数值操作
### 取整
1. 截取整数+强制类型转换：```parseInt(a)```。将一个字符串中的 ***有效*** 整数拿出。可以用来取整。依次将字符串中从左到右的数字部分转为整数,一旦转换失败，返回NaN。正数向下取整，负数向上。
可以接收第二个参数，为进制数。
2. 移位运算符：```>>```或```<<```。以```>>```为例，```(right - left) >> 1```以为在二进制下右移一位并取整。正数向下取整。
### 溢出
1. 用```mid = left + ((right - left) / 2)```代替```mid = (left + right) / 2)```。
2. 
### 常量
1. js的最大安全整数：```Number.MAX_SAFE_INTEGER```。
2. js的无穷数：```Infinity```，类型为number。它比任何有限数都大，比如最大安全整数。

### 其它类型转换为Number




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
使用```for(xxx of xxx)```遍历数组。
> 尽量不要使用for in遍历数组！原因如下：
> for in的index索引为字符串型数字，不能直接进行几何运算；遍历顺序有可能不是按照实际数组的内部顺序；使用for in会遍历数组**所有的可枚举属性**，包括**原型**。
> for in更适合遍历对象。

1. 方法```array.reduce(function(prev,cur,index,arr){...},init)```：对数组每个元素逐个执行一个reduce函数，返回值为结果。循环遍历能做的，reduce都可以做，比如数组求和、数组求积、数组中元素出现的次数、数组去重等等。
> prev：必需。累计器累计回调的返回值; 表示上一次调用回调时的返回值，或者初始值 init
> cur：必需。表示当前正在处理的数组元素
> index可选，表示当前正在处理的数组元素的索引，若提供 init 值，则起始索引为- 0，否则起始索引为1；arr可选，表示原数组；init可选，表示初始值。


### 提取指定位置的元素
1. 方法```array.alice(start,end)```：返回值为下标[start，end)位置上的新数组。改变原数组。**注意包括start，但不包括end。**
### 数组与其他数据类型互相转化
1. 方法```Array.from()```：从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。
2. 方法```str.split(" ")```：字符串转数组。可以传一个参数作为切割标志，不传则默认切割每一个字符。
3. 方法```str.join("")```：数组转字符串。
3. 方法```xxx.toString()```或```a = String(a)```：数值转对应进制的数字字符串。

## map方法
> 关键词：key个数不确定；哈希值比较少、特别分散、跨度非常大（此时使用数组作为哈希表会产生很大浪费）；
> 还可以使用数组、字符串等类型作为map的key值。

通过```new Map()```等方法进行初始化。可以通过```map[key]```获得value。
1. 查看key的value值/key是否存在：```map.set(tchar, map.has(tchar) ? map.get(tchar) + 1 : 1)```。

### 遍历操作
1. forEach遍历：第一个参数是属性值，第二个参数是属性
```javascript
map.forEach(function(value,key){
    console.log(value,key);
})
```
2. for-of遍历
```javascript
for(let item of map){  //遍历结果是数组，数组的每一项都是长为2的数组[key,value]
                       //此时需要获取key就用item[0],value就是item[1]
}
for(let item of map.values()){  //遍历属性值
} 
for(let item of map.keys()){  //遍历属性
}
```
3. entries遍历
```javascript
for(let item of map.entries()){  //遍历结果同forEach
}
```



## 字符串方法
### 遍历操作
1. 方法```for(const i of str)```：遍历。



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

> 优势：可以使原链表中的所有节点按统一的方式进行处理，包括头结点。
> 注意return的是```dummthead.next```。

### 环形链表
常涉及到数学运算，如判断是否有环和环入口时。必要时可以考虑将环形链表展开为直线再运算。
常用方法：快慢双指针。根据是否相遇、何时相遇进行判断。



## 算法
### 二分查找
> 关键字：有序数组、无重复元素、复杂度O(logn)


注意区间开闭（```while (left < right)```、```while(left <= right)```、```right = middle```、```right = middle - 1```？）。
一般来说有两种常见选择，左闭右闭```[left,right]；while (left <= right)```和左闭右开```[left,right)；while (left < right)```。

### 双指针
> 关键字：数组/链表/字符串、不占用额外空间、保持相对顺序、复杂度O(n)
> 反转问题、反转链表等，判断链表是否有环时也使用了快慢两个指针。
> 很多数组填充类的问题也可以用双指针。预先给数组扩容带填充后的大小，然后在从后向前进行操作。
> N数之和：通过前后两个指针不算向中间逼近，在一个for循环下完成两个for循环的工作。N变大，则在三数之和的基础上再套一层for循环，依然是使用双指针法。

通过两个指针在一个for循环下完成两个for循环的工作。
必须明确**快慢指针各自的具体含义**。比如快指针是在遍历中寻找满足条件的元素，慢指针是指向结果数组中存放满足条件的元素的下标（位置）。
### 滑动窗口
> 双指针的一种
> 关键字：不占用额外空间、保持相对顺序、复杂度O(n)

明确：是以滑动窗口的**起始位置还是终止位置**为for循环的标识。

### 哈希表

使用哈希法的标志：查询一个元素是否出现过、一个元素是否在集合里。
#### 数组
> 关键字：key个数确定（如26个英文字母、指定较小字符集）；复杂度O(n)
> ``` const resSet = new Array(26).fill(0);```
>
> 缺点：数组大小有限，受到系统栈空间（不是数据结构的栈）的限制；如果数组空间够大但哈希值比较少、特别分散、跨度非常大，数组就会造成空间的极大浪费。

#### Map
> 关键词：key个数不确定；除了知道是否出现，还需要知道出现位置等其他信息（存在value里）；哈希值比较少、特别分散、跨度非常大；
> 特殊：无限**循环**（快乐数）
> 可以说是万能的哈希结构。但也存在更适合用Set或者数组的情况。
>
> 缺点： 占用空间比数组大，速度比数组慢

#### Set
Set中的数只会出现一次。
> 关键词：只需要知道key值是否重复出现；**快速判断一个元素是否出现在集合里**
>
> 缺点：set是一个集合，里面放的元素只能是一个key，而类似两数之和的题目，不仅要判断y是否存在而且还要记录y的下标位置，所以set 也不能用。
> 

### KMP
字符串匹配算法之一。当出现字符串不匹配时，通过记录已经匹配的文本内容，利用这些信息避免从头再去做匹配。
使用KMP算法，一定要构造next数组。
1. next数组
本质是前缀表。当在某个字符出发现模式串与主串(文本串)不匹配的时候，它会告诉我们下一步匹配中** 模式串 **应该跳到哪个位置。
前缀表：长度等于模式串的长度，模式串与前缀表对应位置的数字的含义即为记录该下标i之前（包括i）的字符串中，**相同**前缀后缀的** 长度 **。
2. 前缀/后缀
字符串的前缀：所有以第一个字符开头的连续子串，但**不包含最后一个字符**。
字符串的后缀：所有以最后一个字符结尾的连续子串，但**不包含第一个字符**。
当匹配过程中遇到了失配字符，考虑**模式串**在失配字符前（不包括失配字符）的字符串；失配字符在后缀子串的后面，所以找到与其相同的前缀的后面从新匹配就可以了。
也即，遇到了失配字符， 此时我们要看它的**前一个字符的前缀表的数值**是多少。
此外，根据前缀表的定义，如果前一个字符的前缀表的数值是k， 可以直接把指针跳到模式串下标为k的位置继续比较。这里不需要进行额外的数学计算（比如用当前位置的下标减去k+1什么的）。 
3. next数组与前缀表的两种关系
第一种情况，next数组完全就是前缀表。
第二种情况，把前缀表统一减一（右移一位，初始位置为-1）之后作为next数组。
它们的差别只有具体实现上略微不同，而不涉及到KMP的原理。
4. next数组的构造getNext（前缀表的计算）
  以下是next数组完全就是前缀表的写法。

  + 初始化：定义两个指针i和j，j指向前缀末尾位置，i指向后缀末尾位置。同时进行初始化操作```int j = 0;next[0] = 0;```。遍历方法为``` for(int i = 1; i < s.size(); i++)```。这里j在循环外，对应循环中的下标为i-1的元素所对应的next值(长度）；也是这个next值所对应的从字符串开始位置开始的前缀末尾的下一个位置的下标.
  + 处理前后缀不相同的情况：向前回退。next[j]就是记录着j（包括j）之前的子串的相同前后缀的长度。那么 s[i] 与 s[j] 不相同，就要找 j前一个元素在next数组里的值（就是next[j-1]）。
  + 处理前后缀相同的情况：如果 s[i] 与 s[j + ] 相同，那么就同时向后移动i和j 说明找到了相同的前后缀，同时还要将移动后j（前缀的长度）赋给next[i], 因为next[i]要记录相同前后缀的长度。
5. 使用Next数组进行匹配

力扣 28.实现strStr 题目的整体代码如下：
```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var getNext = function (str) {
    let next = []
    let j = 0 
    // 这里j在循环外，对应循环中的下标为i-1的元素所对应的next值(长度）；也是这个next值所对应的从字符串开始位置开始的前缀末尾的下一个位置的下标.
    next.push(j)
    for (let i = 1; i < str.length; i++) {
    // 每次循环都有next.push(j)，这里相当于有：j = next[i-1]。
        while (j > 0 && str[i] !== str[j]) {
            j = next[j - 1] 
            // j是结尾下标为i-1（长度i）的字符串的最长公共前后缀【长度】。j-1是这个最长公共前后缀的【结尾下标】。next[j-1]是结尾下标为j-1（长度j）的字符串的最长公共前后缀【长度】。注意上一行是【i】，这一行是【j】。
            //所以再进入while时，str[j]就是上面注释提到的结尾下标为j-1（长度j）的字符串的最长公共前后缀【的下一个元素】。比较的逻辑和if是一样的了。同时注意j不能为0.（第一行中的最长公共前后缀长度如果为0，j都不用更新直接push进去就行）
        }
        if (str[i] === str[j]) {
            j += 1
        }
        next.push(j)
    }
    return next
}
var strStr = function (haystack, needle) {
    if (needle == '') {
        return 0
    }
    var next = getNext(needle)
    var currlength = 0
    for (let i = 0; i < haystack.length; i++) {
        while (currlength > 0 && haystack[i] != needle[currlength]) {
            currlength = next[currlength - 1]
        }
        if (haystack[i] == needle[currlength]) {
            currlength++
        }
        if (currlength == needle.length) {
            return (i - needle.length + 1)
        }
    }
    return -1
};

```

### 反转系列
> 关键词：原地修改；不能申请额外空间，只能在本串上操作。
> 常用方法：先整体反转再局部反转

