
## 设置清单文件
一般而言，我们行为包的清单文件应该是这样的：

```json
{
  "format_version": 2,
  "header": {
    "name": "第一个附加包",
    "description": "我的第一个附加包！",
    "min_engine_version": [1, 21, 0],
    "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4",
    "version": "1.0.0"
  },
  "modules": [
    {
      "type": "data",
      "uuid": "1ce8eeae-f1d6-4330-bda1-e9fd427406cd",
      "version": "1.0.0"
    }
  ],
  "dependencies": [
    {
      "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4",
      "version": "1.0.0"
    }
  ]
}
```

为了激活脚本功能，我们需要在`modules`中添加一个`type`为`script`的模块，如下所示：

```json
{
    "uuid": "...",
    "version": [1, 0, 0],
    "type": "script",
    "language": "javascript",
    "entry": "scripts/main.js"
}
```
在其中：

- `uuid`设置了模块的UUID，必须与清单文件中的其他UUID不同；
- `version`设置了模块的版本；
- `type`设置了模块的类型，为了激活脚本功能，需要填`script`；
- `language`设置了脚本使用的语言，目前只能是`javascript`；
- `entry`设置了脚本入口文件，这里是`scripts/main.js`。


如果想要在脚本中使用`eval()`函数或`Function()`构造函数，需要向清单文件中添加：

```json
{
  "capabilities": ["script_eval"]
}
```

为了让脚本与游戏进行交互，我们还需要通过设置`dependencies`来加载原生脚本模块，如下所示：

```json
"dependencies": [
  {
    "module_name": "@minecraft/server",
    "version": "1.16.0"
  }
]
```

::: details 完整的清单文件代码
```json
{
  "format_version": 2,
  "header": {
    "name": "第一个附加包",
    "description": "我的第一个附加包！",
    "min_engine_version": [1, 21, 0],
    "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4",
    "version": "1.0.0"
  },
  "modules": [
    {
      "type": "data",
      "uuid": "1ce8eeae-f1d6-4330-bda1-e9fd427406cd",
      "version": "1.0.0"
    },
    {
    "uuid": "2ba6a25c-fb0b-40c8-b2d6-3a9176053391",
    "version": [1, 0, 0],
    "type": "script",
    "language": "javascript",
    "entry": "scripts/main.js"
    }
  ],
  "capabilities": ["script_eval"],
  "dependencies": [
    {
      "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4",
      "version": "1.0.0"
    },
    {
      "module_name": "@minecraft/server",
      "version": "1.16.0"
    }
  ]
}
```
:::

### 脚本模块版本
**脚本模块版本**可以用于指定游戏使用对应版本的接口来加载脚本文件，在范例中我们指定为`1.16.0`。

值得注意的是，测试版的模块（即版本号带`beta`标识的版本，如`2.0.0-beta`）通常会有更多的功能，但其将**会在对应的正式版模块推出后被移除**，因此如果使用了测试版模块，在游戏版本更新时要注意更新版本号。

## 编写脚本
在行为包的根目录下创建`scripts`文件夹，这里将会用来存放我们的脚本文件，在其中创建`main.js`，写入如下内容：

```javascript
import { world } from "@minecraft/server"; // 引入原生脚本模块

// 监听玩家生成后事件
world.afterEvents.playerSpawn.subscribe((event) => { 
  event.player.sendMessage("Hello, world!")
})
```
在上面的实例中，我们订阅了`playerSpawn`事件（即当玩家生成后事件），该事件触发后会调用订阅时提供的回调函数，在这里该函数会向玩家发送一条消息。

事件监听是脚本API中非常重要的一项功能，在后续我们会继续讲解这一部分的内容。

## 小结
至此，我们学会了：

- 如何设置清单文件以激活脚本功能；
- 如何编写脚本文件；
- 如何使用原生脚本模块。

在后续的教程中，我们会继续讲解脚本API的其他内容。