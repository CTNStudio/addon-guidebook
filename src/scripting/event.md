# 事件订阅
通过**事件订阅**，我们可以在特定事件发生时触发脚本，并执行一些操作。

本节教程将讨论如何订阅事件，并编写相应的回调函数。

## 前置知识
在开始之前，我们应该知道：

- 事件订阅是**异步**的，这意味着回调函数会在事件发生时被调用，而不是在订阅时；
- 事件订阅位于原生模块`@minecraft/server`的`world`对象中，可以分为`beforeEvents`和`afterEvents`，前者是会在事件发生之前触发，后者则会事件发生之后触发。

## 订阅事件
我们一般通过事件的`subscribe`方法来订阅事件，用`unsubscribe`方法来退订事件，如：

```javascript
import { world } from '@minecraft/server';

world.afterEvents.itemUse.subscribe();
```

在上面的范例中，我们订阅了`itemUse`事件，这意味着当**玩家使用物品后**，会触发回调函数。

接下来我们来了解一下回调函数如何编写。

## 回调函数
```javascript
import { world } from '@minecraft/server';

world.afterEvents.itemUse.subscribe(event => {
    event.source.sendMessage('你使用了物品！');
}
);
```
回调函数是当事件发生时被调用的函数，它接受一个参数，包含了与事件相关的信息。

在这里，我们可以通过`event.source`来获取到触发事件的玩家对象，通过`event.itemStack`来获取到玩家使用的物品对象。

::: details 拓展：箭头函数
范例中的回调函数是以**箭头函数表达式**的形式出现的，其的语法比传统的函数表达式更简洁：

```javascript
(param1, param2, ...) => expression;
```
箭头函数可以省略`function`关键字，箭头前的括号表示函数的参数，箭头后的部分为函数体。

在事件订阅中，我们一般使用箭头函数来编写回调函数。
:::

## 只读模式
尝试运行以下脚本：

```javascript
import { world } from '@minecraft/server';

world.beforeEvents.itemUseOn.subscribe(event => {
    event.cancel = true;
    world.setTimeOfDay(0);
});
```
如果不出意料，游戏抛出了以下错误：

```
[Scripting][error]-ReferenceError: Native function xxx does not have required privileges. 
```

这是因为，为了防止出现级联更改，一些可能对世界进行修改的函数在`beforeEvents`中是没有权限使用的，规避这种限制的方法也很简单：


```javascript
import { world, system } from '@minecraft/server';

world.beforeEvents.itemUseOn.subscribe(event => {
    event.cancel = true;
    system.run(()=>{
      world.setTimeOfDay(0);
    }) 
    system.runTimeout(() => {
        world.setTimeOfDay(0);
    }, 5);
});
```
在这里，我们使用了`system.run()`函数，其将会把我们要执行的操作延后一个游戏刻再执行，从而规避了只读模式的限制，而`system.runTimeOut()`函数与其类似，不过可以指定延后的时长，我们在这里设置为五个游戏刻。

## 事件列表
在游戏中有许多事件可供开发者订阅，我们可以在[官方文档](https://learn.microsoft.com/en-us/minecraft/creator/scriptapi/minecraft/server/minecraft-server)中找到所有事件的列表。