
当我们手持一个方块时，我们实际上拿的是一个可以放置该方块的物品，这就是**方块物品**，一般而言，游戏会自动为我们的方块注册方块物品。

然而有的时候，我们想要自定义方块物品，本节将会讨论如何实现该功能。

## 操作步骤

为了替换方块物品，我们需要创建一个与方块具有相同 ID 的新物品，如：

```json
{
    "format_version": "1.21.60",
    "minecraft:item": {
        "description": {
            "identifier": "example:first_block",
            "menu_category": {
        "category": "nature"
            }
        },
        "components": {
            "minecraft:icon": "example:first_item",
            "minecraft:block_placer": {
                "block": "example:first_block",
                "replace_block_item": true
            }
        }
    }
}
```

`minecraft:block_placer`组件是实现该功能的必须组件，其可以让玩家使用物品时在对应位置放置方块：

- `block`属性设置了要放置方块的 ID，为了实现替换方块物品，方块与物品的 ID 必须完全一致；
- `replace_block_item`属性设置是否要替换方块物品，这里为`true`。

这样，我们就成功替换了一个方块物品。

## 小结

在本节中，我们了解了如何自定义一个方块的方块物品。

需要注意的是，当玩家使用自定义的方块物品放置方块时，不会触发 Script API 中有关