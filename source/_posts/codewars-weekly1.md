title: 【CodeWars 总结1】 超长数字计算
tags:
  - codewars
id: 231
categories:
  - JavaScript
date: 2014-09-30 13:21:45
---

[CodeWars](http://www.codewars.com/)（[附带我邀请码的地址](www.codewars.com/r/D2Ns0Q)） 是一个在线做题的网站，提供一些问题，然后让你用代码（支持 JS、Java、Ruby、Python 等）来解决这个问题。当你编写好函数并 submit，它会为代码做单元测试判断是否正确。如果正确就可以提交代码，这时候可以看到别人写的代码。

<!-- more -->

一般的来说，等你 submit 之后，看到排名第一的答案，会**真真正正**的感觉自己写了一坨翔。而仔细思考学习别人的代码，就是一种进步。决定每过一段时间刷一下 CodeWars 然后总结，记录一些精彩的技巧（Tips，一些技巧）和解决方案（Solution，一个思路）。

##Tips

### 精确判断 JavaScript 值的类型

提到判断类型，立马想到 `typeof` 但是这种方式判断出来的特别不准，例如：

```
var a = {'b':2};
typeof a; //-> "object"

a = ['b','c'];
typeof a; //-> "object"
```

可以使用 `toString` 方法：

```
var a = ['b','c'];
Object.prototype.toString.call(a);
```
之后会返回 `"[object Array]"` 这个值是绝对准确的，详情请看：[Example: Using toString() to detect object class](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)。

此外，配合使用 `typeof` 和 `instanceof` 也可以判断某些特定的类型，前提是你对它们俩足够了解，不然可能会判断出错。

### String 也有 length 属性及善用三目运算

这个有点丢人，做题才知道的。有道题是将溢出字符截掉替换成 `...` ，我的版本是：

```
function solution(string,limit){

    var newStr;

    if ( string.split('').length <= limit ) {
    	newStr = string;
    }else{
    	newStr = string.substring(0,limit)+'...';
    }

    return newStr;
}
```
先把字符串切割成了数组，再用 length 属性来判断位数，然后就是常规的 if 语句。太过于臃肿，而最佳答案简单明了：

```
function solution(string,limit){
    return string.length > limit ? string.substr(0,limit) + "..." : string;
}
```

### 善用 Array 的函数

遇到数组有关操作，脑子第一反应不要再是嵌套 for 循环了，[Array 类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)提供了一些遍历有关的函数，好好使用可以省下很多麻烦，主要有：

* [Array.prototype.forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) ： 把数组每个元素丢到一个处理 function 里面，相当于 for 循环。
* [Array.prototype.every() ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) ： 所有数组元素处理后全部 return true，那么就 return true，有点像 &&。
* [Array.prototype.some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) ： 只要一个数组元素处理后 return true，那么就 return true，有点像 ||。
* [Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) ： 将处理时 return true 的数组元素拿出来组成一个新数组。
* [Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ： 对每个数组元素进行处理，并组成一个新数组。
* [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) ： 像一个累加器一样，把数组元素全部加起来（从左向右）。
* [Array.prototype.reduceRight()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight) ： 同上，顺序从右向左。

有这么多辅助函数，处理 Array 几乎用不到 for 语句，例如有道题是要求判断某个值是否存在与一个多层数组（locate(['a','b',['c','d',['e']]],'e'); // should return true），一开始的思路就是常规的，先把多重数组递归创建一个单层的数组，然后 for 循环比对返回，于是拉出了这么一坨：

```
var locate = function(arr,value){
  var tempArr = [],
    flag = false;

  fullArr(arr);
  function fullArr(arr){
    for( var i = 0; i < arr.length; i++ ){
      if( typeof arr[i] !== 'object' ){
        tempArr.push(arr[i]);
      }else{
        fullArr(arr[i]);
      }
    }
  }

  for( var j = 0; j < tempArr.length; j++ ){
    if( !flag && value === tempArr[j] ){
      flag = true;
      break;
    }else{
      flag = false;
    }
  }
  
  return flag;
  
}
```
看到排名第一的答案，当场尿了：

```
var locate = function(arr, v) {
  return arr.some(function(e) { return Array.isArray(e) ? locate(e, v) : e === v; });
}
```
一个 some ，判断如果有一个 return true 就表示有这个值，将每一个值传递进去，如果还是数组，递归调用这个，否则就返回，简直帅爆！

再来一个统计字符串每个字母出现次数的函数：

```
function count (string) {  
  var count = {};
  string.split('').forEach(function(s) {
     count[s] ? count[s]++ : count[s] = 1;
  });
  return count;
}
```

善用这些函数还可以创建一些辅助函数：

```
Array.prototype.square  = function () { return this.map(function(n) { return n*n; }); }
Array.prototype.average = function () { return this.sum() / this.length; }
Array.prototype.sum     = function () { return this.reduce(function(a, b) { return a + b; }, 0); }
Array.prototype.even    = function () { return this.filter(function(item) { return 0 == item % 2; }); }
Array.prototype.odd     = function () { return this.filter(function(item) { return 0 != item % 2; }); }
```
扔掉 for 吧！

## Solution

有个问题需要将字符串（大数字）计算相加并转换成字符串，传递的参数是字符串，马上想到的思路就是用 parseInt 这类函数转换类型相加，但遇到了大数字就不能直接这么办了。所以本次 Solution 就来解决 JS 大数字计算。

JS 的数字计算坑比较多的，浮点型的计算带有精度问题，所以通常先将浮点型变成整数进行计算，然后再以字符串的形式，变成浮点型输出。大数字计算会变成科学计数法，JS 中，数字超过21位就会变成科学计数法，例如：`8100824045303269669937 -> 8.100824045303269e+21` ，这样会损失一些精度。

那么大数字计算要怎么精确实现？JS 怎么避免出现大数字科学计数法？还是得靠字符串。

首先先说明一点，搜了很多资料，JS 数值型好像没法避免科学计数法，只要超过 21 位，Number 类型的数值自动抹掉低位数，使用科学计数法。这样精度必然有损失。

我的思路大体就是：把两个大数字以字符串的形式分割成小位数数字数组，然后分别相加，如果位数超过分割位数，则向上一个分段和加一，最后再 join 成字符串。可以解决问题，但感觉不够完美，关键代码如下：

```
function sumStrings(a,b) {

	var result = '';

	var arrA = splitNum(a);
	var arrB = splitNum(b);
	var forNum = ( arrA.length > arrB.length ) ? arrA.length : arrB.length;
	var tempArr = [];

	//循环计算分段和
	for( var i = 0; i < forNum; i++ ){
		tempArr.push(cal(arrA[i], arrB[i]));
	}
	tempArr = overflow(tempArr);
	result = tempArr.reverse().join('');

	return result;
}
//字符串切割小数组计算
function splitNum (numStr) {
	var numLength = numStr.length;
	//这里统一将大数字切割成 8 位
	var numLengthTime = Math.ceil(numLength / 8);
	var tempArr = []
	//把数字倒过来，然后切割后再倒过来，就可以优先计算小段位数字
	numStr = numStr.split('').reverse().join('');
	for( var i = 0; i < numLengthTime; i++ ){
		//循环切割八位数字，并倒过来恢复正常数字顺序
		tempArr[i] = numStr.substring(i*8,(i+1)*8);
		tempArr[i] = tempArr[i].split('').reverse().join('');
	}

	return tempArr;
}
//计算两个数值
function cal (a, b) {
	var num = 0;
	//两个大数字位数不一定相同，如果 undefined 表示该数字高位没有数字
	var a = ( typeof a === 'undefined' ) ? 0 : parseFloat(a);
	var b = ( typeof b === 'undefined' ) ? 0 : parseFloat(b);

	num = a + b;

	return num.toString();
}
//判断溢出添加上一个数组
function overflow (tempArr) {
	for( var i = 0; i < tempArr.length; i++ ){
		if ( tempArr[i].length > 8 ) {
			//如果超出 8 位，则向后一个数值进一
			tempArr[i+1] = parseInt(tempArr[i+1]) + 1;
			tempArr[i+1] = tempArr[i+1].toString();
			tempArr[i] = tempArr[i].substring(1);
		}
	}

	return tempArr;
}
```
用了太多的逻辑和代码，不够简洁，实现起来不够爽快。分析了一下排名第一的代码：

```
String.prototype.reverse = function() {
  return this.split('').reverse().join('');
}

function sumStrings(a,b) {
  a = a.reverse(); b = b.reverse();
  var carry = 0;
  var index = 0;
  var sumDigits = [];
  while (index < a.length || index < b.length || carry != 0) {
    var aDigit = index < a.length ? parseInt(a[index]) : 0;
    var bDigit = index < b.length ? parseInt(b[index]) : 0;
    var digitSum = aDigit + bDigit + carry;
    sumDigits.push((digitSum % 10).toString());
    carry = Math.floor(digitSum / 10);
    index++;
  }
  sumDigits.reverse();
  while (sumDigits[0] == '0') sumDigits.shift();
  return sumDigits.join('');
}
```
大体思路比较巧妙，先把数字倒过来然后按位计算，while 循环的条件用的很神。设置三个变量分别作为位数（index）、进位（carry）、结果（sumDigits），每次循环用 index 拿到对应位的数字加上上一个循环的进位，然后取10的余数以字符串形式存入结果数字，然后再取到本次运算的进位，下一个循环继续。最后处理输出即可。

除此之外，对于大数字的操作可以扩展阅读：

* [How to deal with big numbers in javascript [duplicate]](http://stackoverflow.com/questions/4288821/how-to-deal-with-big-numbers-in-javascript)
* [MikeMcl/big.js](https://github.com/MikeMcl/big.js/)