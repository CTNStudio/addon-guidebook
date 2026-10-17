
**武器（Weapon）** 是 Minecraft 中非常重要的游戏内容，自从 1.16.100 以后，制作一把自定义的近战武器变得非常简单。

本节将会讨论如何利用纯数据驱动实现一把近战武器，关于更复杂的效果，请见第十五章内容。

## 开始之前

在开始之前，我们先创建一个没有任何功能的普通物品，并确保其格式版本在 1.21.0 以上，具体过程请参照第一章第三节。

完成后，物品行为部分的代码应该像下面这样：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_weapon",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": "example.first_weapon"
    }
  }
}
```

## 添加分组

首先，我们来为这个自定义武器添加创造模式物品栏分类，例如：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_weapon",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.sword"
      }
    }
     ...
}
```

在这个范例中，我们将自定义武器添加到了装备分类下的剑分组。

## 添加组件

### 基本行为

我们先为这个自定义武器添加一些基本的行为，如：

- 最大堆叠为1；
- 纹理竖直渲染；
- 有额外的攻击伤害；
- 无法在创造模式破坏方块。

向自定义武器添加如下组件：

```json
   ...
   "components": {
      "minecraft:icon": "example.first_sword",
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:damage": 7,
      "minecraft:can_destroy_in_creative": false,
      ...   
```

在其中：

- `minecraft:max_stack_size`组件设置了物品的最大堆叠；
- `minecraft:hand_equipped`组件将物品纹理竖直渲染；
- `minecraft:damage`组件设置物品的伤害为7；
- `minecraft:can_destroy_in_creative`组件令手持该物品的玩家无法在创造模式破坏方块。

### 耐久与修复

通过向自定义武器添加`minecraft:durability`组件，我们可以设置其最大耐久：

```json
...
"minecraft:durability": {
  "max_durability": 500
}
...
```

在这里，我们将物品的最大耐久值设置为了 500。

同时，我们可以通过`minecraft:repairable`组件使自定义武器可以被修复：

```json
...
"minecraft:repairable": {
    "repair_items": [
        {
          "items": ["example:first_item"],
          "repair_amount": "query.max_durability * 0.25"
        },
        {
          "items": ["example:first_sword"],
          "repair_amount": "context.other->query.remaining_durability + 0.2 * context.other->query.max_durability"
        }
      ]
 }
 ...
```

在这里，`items`数组设置了可以修复该武器的物品，而`repair_amount`设置了修复后恢复的耐久值，可以填整数。

注意到这里的`repair_amount`并非整数，而是 **Molang 表达式** ，这里的表达式对于初学者比较难懂，我们无需了解过深。

### 附魔

通过为自定义武器添加`minecraft:enchantable`组件，我们的武器就可以被附魔了：

```json
 "minecraft:enchantable": {
   "value": 15,
   "slot": "sword"
}
```

在其中，`slot`设置了武器可以被附上的魔咒类型，这里是`sword`，即剑的魔咒。、

而`value`则设置了武器的**附魔能力** ，附魔能力越高，魔咒的质量就越好，如果一个物品的附魔能力为 0，则无法附魔[^1]。

## 挖掘方块

剑可以加速一些方块的挖掘，这个功能将会通过`minecraft:digger`组件实现：

```json
...
"minecraft:digger": {
  "use_efficiency": true,
  "destroy_speeds": [
     {
       "block": {
         "tags": "query.any_tag('minecraft:is_sword_item_destructible', 'pumpkin')"
         },
         "speed": 6
       }
   ]
}
...
```

在其中，`use_efficiency`设置了此物品挖掘时是否应受到效率附魔的影响，`destroy_speeds`数组设置了物品可以挖掘的方块以及对应的速度。

注意到这里的`block`选项是一个带有`tags`属性的对象，这表示该武器可以挖掘所有带有`minecraft:is_sword_item_destructible`标签的方块。

## 添加标签

最后，让我们用`minecraft:tags`组件为物品添加标签：

```json
...
 "minecraft:tags": {
   "tags": [
      "minecraft:is_sword"
   ]
}
...
```

在这里，我们为自定义的武器添加了`minecraft:is_sword`标签。

## 添加客户端资源

接下来，我们要为自定义武器添加客户端资源，其步骤与第一章第三节所述步骤完全相同，故这里不再赘述。

添加完客户端资源后，一个完整的近战武器就制作好了。

## 小结

通过本节的学习，我们学会了如何添加一个自定义近战武器，完整的物品行为代码应该如下所示：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.sword"
      },
      "identifier": "example:first_sword"
    },
    "components": {
      "minecraft:icon": "example.first_sword",
      "minecraft:max_stack_size": 1,
      "minecraft:hand_equipped": true,
      "minecraft:damage": 7,
      "minecraft:can_destroy_in_creative": false,
      "minecraft:durability": {
        "max_durability": 500
      },
      "minecraft:repairable": {
        "repair_items": [
          {
            "items": ["example:first_item"],
            "repair_amount": "query.max_durability * 0.25"
          },
          {
            "items": ["example:first_sword"],
            "repair_amount": "context.other->query.remaining_durability + 0.2 * context.other->query.max_durability"
          }
        ]
      },
      "minecraft:enchantable": {
        "value": 15,
        "slot": "sword"
      },
      "minecraft:digger": {
        "use_efficiency": true,
        "destroy_speeds": [
          {
            "block": {
              "tags": "query.any_tag('minecraft:is_sword_item_destructible', 'pumpkin')"
            },
            "speed": 6
          }
        ]
      },
      "minecraft:tags": {
        "tags": [
          "minecraft:is_sword"
        ]
      }
    }
  }
}
```

[^1]: [附魔（物品修饰） - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%99%84%E9%AD%94%EF%BC%88%E7%89%A9%E5%93%81%E4%BF%AE%E9%A5%B0%EF%BC%89)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
