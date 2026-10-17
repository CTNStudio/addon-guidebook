**方块标签（Block Tag）** 允许我们将自定义物方块分组。

本节将会讨论如何为自定义物品添加标签，并了解一些原版标签。

## 添加标签


与在 1.20.50 之前的物品类似，我们应该向方块添加形如`tag:<标签名称>`的空组件来实现添加标签，如：

```json
{
  "format_version": "1.21.40",
  "minecraft:block": {
    "description": {
      "identifier": "example:first_block",
      "menu_category": {
        "category": "construction"
      }
    },
    "components": {
      "minecraft:destructible_by_mining": {
        "seconds_to_destroy": 3
      },
      "minecraft:destructible_by_explosion": {
        "explosion_resistance": 3
      },
      "minecraft:map_color": "#ffffff",
      "minecraft:loot": "loot_tables/blocks/first_block.json",
       "tag:example:tag1": {},
       "tag:example:tag2": {}
    }
  }
}
```

在上面的范例中，我们为方块添加了`example:tag1`和`example:tag1`两个标签。

> [!IMPORTANT]
> 截至本书成稿时，方块暂时没有像物品的`minecraft:tags`组件

## 检测标签

目前有以下的 Molang 表达式可用于方块行为文件中，检测方块的标签：

- `q.all_tags()`：检测方块是否含有提供的所有标签；
- `q.any_tag()`：检测方块是否含有提供的任一标签。

在实体上，我们可以使用以下 Molang 表达式对方块标签进行检测：

- `q.relative_block_has_all_tags`
- `q.relative_block_has_any_tag`
- `q.block_has_all_tags`
- `q.block_has_any_tag`

在 Script API 中，有以下关于物品标签的函数：

- `Block.hasTag(tag: string): boolean`：返回该方块是否具有给定的标签；
- `Block.getTags(): string[]`：返回该方块具有的所有标签。

## 原版标签

接下来，我们来了解一下原版游戏都存在着哪些标签以及它们的作用。

限于篇幅，这里只能列举出一些常用的标签的名称以及其已知作用，更多信息请见本节末尾。

- `minecraft:<材料名>_tier_destructible`：设置该方块最低可以由何种等级的工具挖掘，如`minecraft:diamond_tier_destructible`；
- `minecraft:is_<工具类型>_item_destructible`，设置该方块可以由何种类型的工具挖掘，如`minecraft:is_pickaxe_item_destructible`。

## 小结

在本节中，我们了解了如何为自定义方块添加标签，并了解了一些原版标签。

以下是一些可供参考的资料：

- [Block Tags - Bedrock Wiki](http://wiki.bedrock.dev/blocks/block-tags)


