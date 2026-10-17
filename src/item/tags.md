**物品标签（Item Tag）** 允许我们将自定义物品分组，一些标签还可以为物品添加一些特殊的行为。

本节将会讨论如何为自定义物品添加标签，并了解一些原版标签。

## 添加标签

在 1.20.50 之前，我们应该向物品添加形如`tag:<标签名称>`的空组件来实现添加标签，如：

```json
{
  "format_version": "1.20.30",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_item",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": {
        "texture": "example.first_item"
      },
      "tag:example:tag1": {},
      "tag:example:tag2": {}
    }
  }
}
```

在上面的范例中，我们为物品添加了`example:tag1`和`example:tag1`两个标签。

而在 1.20.50 后，添加物品标签的方式发生了变化，所有标签应当在`minecraft:tags`组件中声明，如：

```json
{
  "format_version": "1.20.60",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_item",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": {
        "texture": "example.first_item"
      },
      "minecraft:tags": {
        "tags": [
          "example:tag1",
          "example:tag2"
        ]
      }
    }
  }
}
```

在上面的范例中，我们同样为物品添加了`example:tag1`和`example:tag1`两个标签。

## 检测标签

目前有以下的 Molang 表达式可用于检测物品的标签：

- `q.all_tags()`：检测物品是否含有提供的所有标签；
- `q.any_tag()`：检测物品是否含有提供的任一标签；
- `q.equipped_item_all_tags`：检测手持物品是否含有提供的所有标签；
- `q.equipped_item_any_tag`：检测手持物品是否含有提供的任一标签。

在 Script API 中，有以下关于物品标签的函数：

- `ItemStack.hasTag(tag: string): boolean`：返回该物品是否具有给定的标签；
- `ItemStack.getTags(): string[]`：返回该物品具有的所有标签。



## 原版标签

接下来，我们来了解一下原版游戏都存在着哪些标签以及它们的作用。

限于篇幅，这里只能列举出一些常用的标签的名称以及其已知作用，更多信息请见本节末尾。

- `minecraft:<材料名称>_tier`：标记该物品的材料类型，如`minecraft:diamond_tier`；
- `minecraft:digger`：标记该物品可以用来挖掘方块；
- `minecraft:is_armor`：标记该物品为盔甲；
- `minecraft:is_axe`：标记物品为斧，使其可以为原木剥皮；
- `minecraft:is_hoe`：标记物品为锄，使其可以将泥土类方块等转化为耕地；
- `minecraft:is_shovel`：标记物品为锹，使其可以将泥土类方块转化为土径；
-  `minecraft:trimmable_armors`将物品标记为可锻造的盔甲。

## 小结

在本节中，我们了解了如何为自定义物品添加标签，并了解了一些原版标签。

以下是一些可供参考的资料：

- [Item Tags - Bedrock Wiki](http://wiki.bedrock.dev/items/item-tags)；
- [BedrockData](https://github.com/pmmp/BedrockData)：其中的`item-tags.json`提供了全部的原版标签及其对应的物品。

接下来将会进入实战，学习自定义各种游戏道具——我们将会从最简单的自定义食物开始。
