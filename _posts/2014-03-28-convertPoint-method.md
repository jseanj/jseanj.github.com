---
layout: post
title:  "convertPoint:fromNode:和convertPoint:toNode的区别"
date:   2014-03-28 23:09:27
categories: jekyll update
---

一直没有搞明白这两个方法在sprite kit中如何使用，今天在so上看到了详细讲解，在此记录一下。

`- (CGPoint)convertPoint:(CGPoint)point fromNode:(SKNode *)node`
> 将一个节点坐标系中的坐标convert到另一个节点（调用者）坐标系中。**关键**要看这个坐标是在哪个坐标系里表示的。比如，如果你将精灵的位置作为坐标传进来，那么你就需要将精灵的父节点也传进来；如果你传进来的是[0,0]，那么说明你是以该精灵本身作为坐标系，所以你需要将该精灵传进来。

![Alt text](http://i.stack.imgur.com/PL5cO.png)
> 比如，为了得到红色节点在scene坐标系中的位置时：

```
CGPoint positionInScene = [self convertPoint:[redSprite position] fromNode:[self blueSprite]];
// [redSprite position] = [5, 5]
// positionInScene = [105, 205]
```
`- (CGPoint)convertPoint:(CGPoint)point toNode:(SKNode *)node`
> 该方法和上一个方法正好相反，它是将一个节点（调用者）坐标系中的坐标convert到另一个节点坐标系中，**关键**要看这个坐标是在调用者节点坐标系中是如何表示的。

> 比如，当你在scene的[125, 255]坐标处点击了一下，你想在此处添加一个精灵，此时恰好是在redSprite的边界里，因此你可以直接添加一个精灵作为redSprite的子节点，你需要获得在redSprite坐标系中的点击坐标：

```
CGPoint positionInRedSprite = [self convertPoint:touchLocation toNode:[self redSprite]];
// touchLocation = [125, 225]
// positionInRedSprite = [20, 20]
```

> 出处：http://stackoverflow.com/questions/21863918/sknode-convertpoint-tonode-fromnode-confusion
