## 配方

**配方（Recipe）** 是游戏中的基础，通过配方可以获得游戏中的各种物品，附加包允许我们通过数据驱动的方式添加自定义配方。

我们先来学习最基础的有序配方，在我们的行为包下新建`recipes`文件夹，这个文件夹将会存放我们的自定义配方。

在`recipes`中创建一个 JSON 文件，写入以下内容：


```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_shaped": {
    "description": {
      "identifier": "example:first_block",
    },
    "tags": ["crafting_table"],
    "pattern": ["###", "# #", "###"],
    "key": {
      "#": {
        "item": "example:first_item"
      }
    },
    "unlock": {
      {
        "item": "example:first_item"
      }
    },
    "result": {
      "item": "example:first_block"
    }
  }
}
```

在其中：

`format_version`是配方的格式版本，这里是`1.21.0`；

- `minecraft:recipe_shaped`是配方类型，这里我们使用的是有序配方；
- `identifier`设置了配方的命名空间ID，注意这里的命名空间是`example`，在实际的项目中不建议使用；
- `tags`设置了配方的标签，配方标签决定了配方适用于哪种合成方块，这里是`crafting_table`，即工作台；
- `pattern`设置了配方的排列样式，这是一个字符串类型的数组，一个元素代表合成方块中的一行，每个元素由1-3个字符组成，每个字符都代表着合成所需要的物品；
- `key`解释了在`pattern`中的字符所代表的具体物品，这里我们使用`#`代表`example:first_item`；
- `unlock`设置了配方解锁的条件，这里我们设置玩家需要拥有`example:first_item`才能解锁配方；
- `result`设置了配方合成的结果的物品，这里我们设置合成结果为`example:first_block`。

接下来，我们来学习一下无序配方的大致格式，其与有序配方相似，但更加简单：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_shapeless": {
    "description": {
      "identifier": "example:first_block_shapeless",
    },
    "tags": ["crafting_table"],
    "ingredients": [
      {
        "item": "example:first_item",
        "count": 9
      },
    ],  
    "unlock": [
      {
        "item": "example:first_item"
      }
    ],
    "result": {
      "item": "example:first_block"
    }
  }
}
```

可以看到，无序配方与有序配方差别不不大，我们只需要注意：

- 无序配方类型为`minecraft:recipe_shapeless`；
- 无序配方不需要`pattern`与`keys`，但需要`ingredients`设置合成的原料，`ingredients`是一个数组，每个元素代表合成所需要的物品以及数量，这里我们设置需要9个`example:first_item`来合成一个`example:first_block`。

## 战利品表
什么是战利品表呢？官方文档是这样解释的：

> **战利品表（Loot Table）** 用于定义游戏中物品的生成方式。
> 
> 它们可用于生成箱子的内容，定义实体死亡时掉落的物品，甚至实体装备的物品。
> 
> 在某些情况下，它甚至用于游戏机制，例如哞菇挤奶或钓鱼。

附加包允许我们通过数据驱动的方式添加自定义战利品表。

在我们的行为包下新建`loot_tables`文件夹，这个文件夹将会存放我们的自定义战利品表。

在`loot_tables`中创建一个 JSON 文件，写入以下内容：

```json
{
  "pools": [
    {
      "rolls": 1,
      "entries": [
        {
          "type": "item",
          "name": "example:first_item",
          "weight": 1,
          "functions": [
            {
              "function": "set_count",
              "count": {
                "min": 1,
                "max": 3
              }
            }
          ]
        }
      ]
    }
  ]
}
```
它看起来比配方复杂，但并不难理解：

- `pools`设置了战利品表中的**池**，每一个池都定义了不同的战利品，一个战利品表包含多个池，每个池包含`rolls`、`entries`和`conditions`；
- `entries`设置了池中的战利品，一个池中可以包含多种战利品，与`pools`不同的是，这里的多种战利品在游戏中只会生成一种，选中哪种的概率由`weight`决定，值越大，选择到的概率越大；
- `rolls`设置了池中随机选取战利品的次数，这里为1次；
- `types`设置了战利品的类型，这里为`item`，即物品，也可将其设置为`loot_table`，这样会让战利品表从其他表中生成战利品；
- `name`设置了战利品的命名空间ID，这里为`example:first_item`；
- `functions`设置了战利品的函数，其可以对战利品进行修改，这里我们使用了`set_count`函数，即设置战利品数量为1-3个。

## 小结
至此，我们学习了如何添加自定义简单的物品、方块、实体、配方与战利品表。

恭喜你，你已经完成了附加包入门部分的学习🎉
