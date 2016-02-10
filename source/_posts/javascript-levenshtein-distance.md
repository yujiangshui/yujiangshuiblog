title: 使用 JavaScript 实现简单候选项推荐功能（模糊搜索）
date: 2015-01-21 16:50:26
id: 239
categories:
	- JavaScript
tags:
---

当我们使用 Google 等搜索功能时，会出现与搜索内容有关的候选项。使用 JavaScript 搜索字符串，通常会使用 `indexOf` 或者 `search` 函数，但是非常僵硬，只能搜索匹配特定词语。比如使用关键词 `今天是星期几` 想要检索 `今天是星期五` 这个内容，就无法实现，虽然它们只有很小的差别。

本文就来介绍一个有趣的算法 [编辑距离（Levenshtein Distance）](http://baike.baidu.com/view/2020247.htm)，然后用它来实现一个简单的候选项推荐（模糊搜索）功能。

## 编辑距离（Levenshtein Distance）

简单的说，编辑距离就是把一个字符串修改变成另一个字符串的修改次数。如果修改的次数越小，我们可以简单的认为这两个字符串之间的关系越紧密。比如 `今天是星期几` 对于 `今天是星期五` 和 `明天是星期五`比较，跟 `今天是星期五` 更加紧密一些，因为前者的编辑距离是 1，后者的编辑距离是 2。

更详细的百度百科已经说的很清楚了，这里不再赘述，主要给出 JavaScript 的实现方法：

按照自然语言表达的算法，我们先需要根据两个字符串的长度创建一个二维表：

```
function levenshtein(a, b) {

	var al = a.length + 1;
	var bl = b.length + 1;
	var result = [];
	var temp = 0;

	// 创建一个二维数组
	for (var i = 0; i < al; result[i] = [i++]) {}
	for (var i = 0; i < bl; result[0][i] = i++) {}

}

```

之后就需要遍历这个二位数组，按照如下的规则取得三个值的最小值：

* 如果最上方的字符等于最左方的字符，则为左上方的数字。否则为左上方的数字 + 1。
* 左方数字 + 1
* 上方数字 + 1

需要判断两个值是否相等来决定左上方数字是否 + 1，所以引入 temp 变量。我们可以写出如下遍历代码：

```
for (i = 1; i < al; i++) {
	for (var j = 1; j < bl; j++) {
		// 判断最上方和最左方数字是否相等
		temp = a[i - 1] == b[j - 1] ? 0 : 1;
		// result[i - 1][j] + 1 左方数字
		// result[i][j - 1] + 1 上方数字
		// result[i - 1][j - 1] + temp 左上方数字
		result[i][j] = Math.min(result[i - 1][j] + 1, result[i][j - 1] + 1, result[i - 1][j - 1] + temp);
	}
}
```

最后将二维数组最后一个值返回，该值就是编辑距离：

```
return result[i-1][j-1];
```

这个函数就完成了：

```
function levenshtein(a, b) {

	var al = a.length + 1;
	var bl = b.length + 1;
	var result = [];
	var temp = 0;

	// 创建一个二维数组
	for (var i = 0; i < al; result[i] = [i++]) {}
	for (var i = 0; i < bl; result[0][i] = i++) {}		

	for (i = 1; i < al; i++) {
		for (var j = 1; j < bl; j++) {
			// 判断最上方和最左方数字是否相等
			temp = a[i - 1] == b[j - 1] ? 0 : 1;
			// result[i - 1][j] + 1 左方数字
			// result[i][j - 1] + 1 上方数字
			// result[i - 1][j - 1] + temp 左上方数字
			result[i][j] = Math.min(result[i - 1][j] + 1, result[i][j - 1] + 1, result[i - 1][j - 1] + temp);
		}
	}

	return result[i-1][j-1];
	
}
```

## 实际应用

那么我们现在就来实现一个简单的搜索功能。

大体思路就是将数据与要搜索的字符串计算编辑距离，然后进行排序，将编辑距离小的放在上面显示。具体 Demo 做在 jsfiddle 上面了：

<iframe width="100%" height="300" src="http://jsfiddle.net/yujiangshui/ds6ztf8d/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

也可以[点击这里](http://jsfiddle.net/yujiangshui/ds6ztf8d/)查看。

使用起来是有点效果的，比如：

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/ed0.webp)

和

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/ed1.webp)

但是也有很大的偏差，比如要搜索的关键词和相似结果编辑距离太大，超过了同等长度的不同字符，这时候就会出现错误的推荐：

![](http://jiangshui.b0.upaiyun.com/blog/2015/01/ed3.webp)

如果数据足够多，各种情况都具备，那么推荐准确的可能性更大些。如果要改善这个功能，可能需要结合中文分词对关键词进行匹配综合等等，超出本文范畴这里不再赘述。

如果你有更好的方法和思路，欢迎留言讨论。