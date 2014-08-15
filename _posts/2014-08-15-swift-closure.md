---
layout: post
title:  "Swift中的闭包"
date:   2014-08-15 16:09:27
categories: jekyll update
---

## 闭包引导

Swift中引入了闭包的概念，其实和Block是一样的，我们来简单介绍一下，看下面的函数：

```
func square(a: Float) -> Float {
	    return a * a
}
func cube(a: Float) -> Float {
	    return a * a * a
}
func averageSumOfSquares(a: Float, b: Float) -> Float {
	    return (square(a) + square(a)) / 2.0
}
func averageSumOfCubes(a: Float, b: Float) -> Float {
	    return (cube(a) + cube(b)) / 2.0
}
```

我们注意到求平均数的函数的不同点：计算参数的方法不同，一个是求平方，另一个是求立方。如果接下来又有其他的计算方法，我们需要写更多的函数。因此，我们用闭包作为参数代替计算方法来重新定义函数。

```
// 我写的版本
func averageOfFunction(f: (Float)->Float, s:Float...) ->Float {
	    var result:Float = 0.0
		for i in s {
			result += f(i)
		}
		return result / Float(countElements(s))
}


// 作者的版本
func averageOfFunction(a:Float,b:Float,f:(Float -> Float)) -> Float {
	    return (f(a) + f(b)) / 2
}
```

相比作者的版本，我的没有限制参数的个数。

```
//调用
averageOfFunction(3, 4, square)
averageOfFunction(3, 4, cube)
```

我们也可以利用闭包来直接作为参数。

```
averageOfFunction(3, 4, {(x: Float) -> Float in return x*x})
```

由于swift可以根据上下文推断类型，即它能推断出需要的参数类型是`(Float)->Float`的函数，所以可以这样写：

```
averageOfFunction(3, 4, {x in return x*x})
```

单表达式(例子中的`x*x`)闭包可以隐式返回，所以可以去掉`return`。

```
averageOfFunction(3, 4, {x in x*x})
```

Swift自动为内联函数提供了参数名称缩写功能，你可以使用`$0, $1`等来表示参数，如果超过一个，可以用`$(K-1)`表示第`K`个参数。

```
averageOfFunction(3, 4, {$0 * $0}
```


## 闭包在Swift中的使用

Swift中的`Array`支持三种高阶函数：`map`，`filter`，`reduce`。

#### Map

定义：将array中的每一个元素通过一个函数映射到另一个元素上。
比如：

```
[10,20,45,32] -> ["10€","20€","45€","32€"]

// 通常的作法
var stringsArray : Array<String> = [] 
for money in moneyArray {
	    stringsArray += "\(money)$"
}

// map
 stringsArray = moneyArray.map({"\($0)€"})
 ```

 我们看`map`的定义`func map<U>(transform: (T) -> U) -> [U]`，它接收一个函数，参数名叫做`transform`，主要用来将`T`类型的元素转换成`U`类型的元素，`map`返回`U`类型的数组。

#### Filter

定义：将array中的元素进行过滤
比如：

```
[10, 20, 45, 32] -> [45, 32] //钱数超过30的

// 通常的作法
var filteredArray : Array<Int> = [] 
for money in moneyArray {
	    if (money > 30) {
			filteredArray += money
		}
}

// filter
filteredArray = moneyArray.filter({$0 > 30})
```

我们看`filter`的定义`func filter(includeElement: (T) -> Bool) -> [T]`，它接收一个函数，参数名叫做`includeElement`，主要对`T`类型的元素做判断，`filter`返回判断为`true`的元素的数组。

#### Reduce

定义：将array中的元素合并成一个元素
比如计算所有钱数的总和

```
[10, 20, 45, 32] -> 107

// 钱数相加
var sum = 0
for money in moneyArray {
	    sum = sum + money
}
// 钱数相乘
var product = 1
for money in moneyArray {
	    product = product * money
}
//reduce
sum = moneyArray.reduce(0,{$0 + $1})
sum = moneyArray.reduce(0,+) //操作符也是函数，因此简化
```

我们注意到上面两个过程的不同点：初始值和计算方法（加和乘）。`reduce`定义了初始值和计算方法。`func reduce<U>(initial: U, combine: (U, T) -> U) -> U`，它接收一个初始值，参数名叫做`initial`，它接收一个函数，参数名叫做`combine`，主要是将`U`类型的初始元素和`T`类型的元素结合生成一个`U`类型的元素，`reduce`返回一个`U`类型的元素。

```
注意：
1. `reduce`函数的结果类型和数组的类型可能不同 
2. 高阶函数作用于大数组时比原始做法更快，因为它们是可以并行的。
```

下面列出了一些利用闭包难题，用Swift去实现一下吧！

- Write a function `applyTwice(f:(Float -> Float),x:Float) -> Float` that takes a function f and a float x and aplies f to x twice i.e. f(f(x))
- Write a function `applyKTimes(f:(Float -> Float),x:Float,k:Int) -> Float` that takes a function f and a float x and aplies f to x k times
- Using applyKTimes write a function that raises x to the kth power
- Given an array of Users which have properties name:String and age:Int write a map function that returns an array of strings consisting of the user’s names
- Given an array of of dictionaries containing keys for “name” and “age” write a map function that returns an array of users created from it
- Given an array of numbers write a filter method that only selects odd integers
- Given an array of strings write a filter function that selects only strings that can be converted to Ints
- Given an array of UIViews write a filter function that selects only those views that are a subclass of UILabel
- Write a reduce function that takes an array of strings and returns a single string consisting of the given strings separated by newlines
- Write a reduce function that finds the largest element in an array of Ints
- You could implement a mean function using the reduce operation {$0 + $1 / Float(array.count)}. Why is this a bad idea?
- There’s a problem you encounter when trying to implement a parallel version of reduce. What property should the operation have to make this easier ?
- Implement Church Numerals in Swift (This is a difficult and open ended exercise)

参考资料：http://www.weheartswift.com/higher-order-functions-map-filter-reduce-and-more/
