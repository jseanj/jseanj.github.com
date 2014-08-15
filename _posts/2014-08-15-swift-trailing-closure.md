---
layout: post
title:  "Swift中的尾闭包"
date:   2014-08-15 23:09:27
categories: jekyll update
---

- 应用的前提条件：函数的最后一个参数是闭包
- 为什么使用：当闭包非常长以至于不能在一行中进行书写时，尾闭包变得非常有用，增强函数可读性
- 如何使用：

```
func someFunctionThatTakesAClosure(closure: () -> ()) {
	// 函数体部分
}

// 以下是不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure({
	// 闭包主体部分
})

// 以下是使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
	// 闭包主体部分
}
```

```
注意： 如果函数只需要闭包表达式一个参数，当您使用尾随闭包时，您甚至可以把()省略掉。
```

考虑这样一个函数，参数是一个闭包，主要作用是异步任务完成时进行回调。

```
func loadWithCompletion(completion: (NSURLResponse!, NSData!, NSError!) -> Void)
```

函数调用：

```
loadWithCompletion({  
	(response: NSURLResponse!, data: NSData!, error: NSError!) -> Void in
	// process response
})
```

看起来有些晦涩，因为该闭包作为函数的最后一个参数，因而我们可以用尾闭包来写：

```
loadWithCompletion() {  
	(response: NSURLResponse!, data: NSData!, error: NSError!) -> Void in
	// process response
}
```

这样看起来清爽了很多，很容易看到闭包的开始和结束。
由于该闭包是函数的唯一参数，所以我们可以把`()`去掉：

```
loadWithCompletion {  
	(response: NSURLResponse!, data: NSData!, error: NSError!) -> Void in
	// process response
}
```

我们可以利用类型推导，闭包表达式的参数类型和返回类型可以去掉，进一步简化为：

```
loadWithCompletion { response, data, error in  
    // process response
}
```

参考资料：
http://numbbbbb.gitbooks.io/-the-swift-programming-language-/chapter2/07_Closures.html#trailing_closures
http://freecake.yayuhh.com/trailing-closures-in-swift/
