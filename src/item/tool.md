
**工具（Tool）** 是一种能提升玩家采集能力和采集速度，帮助获得信息，或是辅助其他活动的物品，一些工具具有物品耐久，可被修复[^1]。

本节将会讨论如何利用纯数据驱动实现原版的斧、锄、锹、镐等工具，关于更复杂的效果，请见第十五章内容，由于只使用了数据驱动，所以部分功能会有缺失。

## 开始之前

由于自定义工具与自定义近战武器的步骤和用到的组件非常相似，因此为了方便，我们需要先将上一节中自定义的近战武器的行为文件复制**四份**，分别将其 ID 改为`example:first_axe`、`example:first_hoe`、`example:first_shovel`、`example:first_pickaxe`。

## 添加分组

首先，我们来为自定义工具添加创造模式物品栏分类，这里为斧为例：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_axe",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.axe"
      }
    }
     ...
}
```

在这个范例中，我们将自定义武器添加到了装备分类下的斧分组，对于锄、锹、镐，`group`属性应当分别为：

- `itemGroup.name.shovel`（锹）；
- `itemGroup.name.hoe`（锄）；
- `itemGroup.name.pickaxe`（镐）。

## 修改属性

### 一般组件

接下来，我们要分别修改这四个物品的组件属性使其符合工具的行为。

下面这些修改较为简单，无需详细说明：

- 将`minecraft:durability`组件的`max_durability`属性更换成我们想要的；
- 将`minecraft:enchantable`组件的`slot`属性根据工具类型更换为：
	- `shovel`（锹）；
	- `hoe`（锄）；
	- `pickaxe`（镐）；
	- `axe`（斧）。
- `minecraft:repairable`组件下的修复物品换成我们想要的。

关于这些组件的具体作用，这里不再赘述，请到上一节自行复习。

### 挖掘组件

> [!IMPORTANT]
> 对于工具挖掘方块消耗耐久值的特性，应该使用 Script API 实现，详细说明请见第十五章

接下来，我们将会对`minecraft:digger`这个关键组件进行修改，其中锹、锄、斧的写法大致相同，这里以斧为例：

```json
"minecraft:digger": {
   "use_efficiency": true,
   "destroy_speeds": [
         {
          "block": {
            "tags": "query.any_tag('minecraft:is_axe_item_destructible')"
         },
        "speed": 5
     }
  ]
}
```

注意到这里的`block`选项是一个带有`tags`属性的对象，这表示该武器可以挖掘所有带有`minecraft:is_axe_item_destructible`标签的方块。

对于锹、锄，这个标签应该改为：

- `minecraft:is_shovel_item_destructible`（锹）；
- `minecraft:is_hoe_item_destructible`（锄）。

而对于镐，情况则更加复杂，因为不同等级的镐可以挖掘的方块种类是不同的，例如，黑曜石可以通过钻石镐进行挖掘，而不能被木镐挖掘下来。

假定我们的镐可以挖掘的方块种类与石镐相同，则有：

```json
"minecraft:digger": {
  "use_efficiency": true,
  "destroy_speeds": [
    {
        "block": {
          "tags": "query.all_tags('minecraft:is_pickaxe_item_destructible', 'minecraft:stone_tier_destructible')"
         },
        "speed": 4
    },
    {
       "block": {
          "tags": "query.any_tag('stone')"
       },
       "speed": 4
    }
  ]
}
```

注意到`destroy_speeds`中含有两个元素，一个用`query.any_tag('stone')`表达式指定了该工具可以挖掘所有石质方块；而另一个则稍微复杂，其`tags`下的表达式为：

```
query.all_tags('minecraft:is_pickaxe_item_destructible', 'minecraft:stone_tier_destructible')
```

`query.all_tags`也是 Molang 表达式，与`query.any_tag`不同的是，其要求方块必须具有提供的所有标签。

在这里，我们向其传入了`minecraft:is_pickaxe_item_destructible`（适用工具为镐）和`minecraft:stone_tier_destructible`（最低需要石头工具进行挖掘）两个标签。如果要为其添加更多的可挖掘方块，可以按照以下的方法：

```json
"minecraft:digger": {
  "use_efficiency": true,
  "destroy_speeds": [
    {
        "block": {
          "tags": "query.all_tags('minecraft:is_pickaxe_item_destructible', 'minecraft:stone_tier_destructible')"
         },
        "speed": 4
    },
    {
        "block": {
          "tags": "query.all_tags('minecraft:is_pickaxe_item_destructible', 'minecraft:iron_tier_destructible')"
         },
        "speed": 4
    },
    {
       "block": {
          "tags": "query.any_tag('stone')"
       },
       "speed": 4
    }
  ]
}
```

这样，我们的镐子就可以挖掘铁质工具才可以挖掘下来的方块了。

## 添加标签

最后，让我们用`minecraft:tags`组件为物品添加标签，这里以斧为例：

```json
...
 "minecraft:tags": {
   "tags": [
      "minecraft:is_axe"
   ]
}
...
```

在这里，我们为自定义的武器添加了`minecraft:is_axe`标签， 给物品添加了为原木剥皮的功能，而对于锄、锹，则分别需要添加：

- `minecraft:is_hoe`为锄添加将泥土类方块等转化为耕地的功能；
- `minecraft:is_shovel`：为锹添加将泥土类方块转化为土径的功能。

## 添加客户端资源

接下来，我们要为自定义武器添加客户端资源，其步骤与第一章第三节所述步骤完全相同，故这里不再赘述。

添加完客户端资源后，一个完整的近战武器就制作好了。
## 小结

### 完整代码

斧的完整行为代码：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_axe",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.axe"
      }
    },
    "components": {
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:durability": {
        "max_durability": 200
      },
      "minecraft:damage": 5,
      "minecraft:enchantable": {
        "value": 8,
        "slot": "axe"
      },
      "minecraft:repairable": {
        "repair_items": [
          {
            "items": ["example:first_axe"],
            "repair_amount": "context.other->query.remaining_durability+0.04*context.other->query.max_durability"

          },
          {
            "items": ["example:first_item"],
            "repair_amount": "query.max_durability * 0.25"
          }
        ]
      },
      "minecraft:digger": {
        "use_efficiency": true,
        "destroy_speeds": [
          {
            "block": {
              "tags": "query.any_tag('minecraft:is_axe_item_destructible')"
            },
            "speed": 5
          }
        ]
      },
      "minecraft:icon": "example.first_axe",
      "minecraft:tags": {
        "tags": [
          "minecraft:is_axe"
        ]
      }
    }
  }
}
```

锄的完整行为代码：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_hoe",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.hoe"
      }
    },
    "components": {
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:durability": {
        "max_durability": 200
      },
      "minecraft:damage": 4,
      "minecraft:enchantable": {
        "value": 4,
        "slot": "hoe"
      },
      "minecraft:repairable": {
        "repair_items": [
          {
            "items": ["example:first_hoe"],
            "repair_amount": "context.other->query.remaining_durability+0.04*context.other->query.max_durability"
          },
          {
            "items": ["example:first_item"],
            "repair_amount": "query.max_durability * 0.25"
          }
        ]
      },
      "minecraft:icon": "example.first_hoe",
      "minecraft:digger": {
        "use_efficiency": true,
        "destroy_speeds": [
          {
            "block": {
              "tags": "query.any_tag('minecraft:is_hoe_item_destructible')"
            },
            "speed": 5
          }
        ]
      },
      "minecraft:tags": {
        "tags": [
          "minecraft:is_hoe"
        ]
      }
    }
  }
}
```

锹的完整行为代码：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_hoe",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.hoe"
      }
    },
    "components": {
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:durability": {
        "max_durability": 200
      },
      "minecraft:damage": 4,
      "minecraft:enchantable": {
        "value": 4,
        "slot": "hoe"
      },
      "minecraft:repairable": {
        "repair_items": [
          {
            "items": ["example:first_hoe"],
            "repair_amount": "context.other->query.remaining_durability+0.04*context.other->query.max_durability"
          },
          {
            "items": ["example:first_item"],
            "repair_amount": "query.max_durability * 0.25"
          }
        ]
      },
      "minecraft:icon": "example.first_hoe",
      "minecraft:digger": {
        "use_efficiency": true,
        "destroy_speeds": [
          {
            "block": {
              "tags": "query.any_tag('minecraft:is_hoe_item_destructible')"
            },
            "speed": 5
          }
        ]
      },
      "minecraft:tags": {
        "tags": [
          "minecraft:is_hoe"
        ]
      }
    }
  }
}
```

镐的完整行为代码：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_pickaxe",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.pickaxe"
      }
    },
    "components": {
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:durability": {
        "max_durability": 200
      },
      "minecraft:damage": 4,
      "minecraft:enchantable": {
        "value": 4,
        "slot": "pickaxe"
      },
      "minecraft:repairable": {
        "repair_items": [
          {
            "items": ["example:first_pickaxe"],
            "repair_amount": "context.other->query.remaining_durability+0.04*context.other->query.max_durability"
          },
          {
            "items": ["example:first_item"],
            "repair_amount": "query.max_durability * 0.25"
          }
        ]
      },
      "minecraft:icon": "example.first_pickaxe",
      "minecraft:digger": {
        "use_efficiency": true,
        "destroy_speeds": [
          {
            "block": {
              "tags": "query.all_tags('minecraft:is_pickaxe_item_destructible', 'minecraft:stone_tier_destructible')"
            },
            "speed": 4
          },
          {
            "block": {
              "tags": "query.any_tag('stone')"
            },
            "speed": 4
          }
        ]
      },
      "minecraft:tags": {
        "tags": [
          "minecraft:is_hoe"
        ]
      }
    }
  }
}
```

### 下一步？

接下来，我们将会了解如何利用数据驱动制作自定义盔甲。

[^1]: [工具 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E5%B7%A5%E5%85%B7)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
