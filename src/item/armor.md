
**盔甲（Armor）** 是可由生物穿戴的物品，盔甲在被装备后能给予穿戴者不同等级的保护，减少常见类型的伤害，并呈现在穿戴者的外观上[^1]。

附加包允许我们通过数据驱动的方式来创建属于自己的自定义盔甲。

## 开始之前

在开始之前，我们先创建**四个**没有任何功能的普通物品 *（因为盔甲分为头盔 Helmet、胸甲 Chestplate、护腿 Leggings 和靴子 Boots）*，并确保其格式版本在 1.21.0 以上，具体过程请参照第一章第三节。

完成后，四个物品行为部分的代码应该像下面这样：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_helmet", // 或 first_chestplate、first_leggings、first_boots
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": "example.first_helmet" // 或 first_chestplate、first_leggings、first_boots
    }
  }
}

```

## 添加分组

首先，我们来为这些自定义盔甲添加创造模式物品栏分类，例如：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_helmet",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.helmet"
      }
    }
     ...
}
```

在这个范例中，我们将自定义武器添加到了装备分类下的头盔分组，而对于胸甲、护腿、靴子而言，其`group`值应该分别填入：

- `itemGroup.name.chestplate`（胸甲）；
- `itemGroup.name.leggings`（护腿）；
- `itemGroup.name.boots`（靴子）。

而关于`category`值，则应该保持为`equipment`不变。

## 添加组件

### 基础组件

我们还是以头盔为例，首先添加一些关于基本属性以及耐久、修复方面的组件：

```json
...
"components": {
  "minecraft:icon": "example.first_helmet",
  "minecraft:max_stack_size": 1,
  "minecraft:enchantable": {
    "value": 10,
    "slot": "armor_head"
  },
  "minecraft:repairable": {
    "repair_items": [
        {
         "items": ["example:first_hemlet"],
         "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
        }
    ]
  },
  "minecraft:durability": {
    "max_durability": 200
  }
  ...
```

我们可能会对其中的一些组件感到熟悉，因为其中大部分组件我们都已经在上一节接触过了，因此这里不再赘述。

注意到`minecraft:enchantable`组件下的`slot`值为`armor_head`，在为胸甲、护腿以及靴子添加组件时，这里的值应该改为：

- `armor_torso`（胸甲）；
- `armor_legs`（护腿）；
- `armor_feet`（靴子）

除此之外，这些基本组件的代码是通用的，我们在复制时只需要注意将 ID 以及纹理名改成对应的即可。

### 穿上盔甲！

这时，我们的自定义盔甲应该已经存在于游戏了，然而其无法被穿到身上，这是因为我们没有添加一个重要的`minecraft:wearable`组件：

```json
...
"minecraft:wearable": {
  "protection": 5,
  "slot": "slot.armor.head"
}
```

注意到这个组件的`protection`属性设置了盔甲给予玩家的护甲值，而`slot`属性则设置了盔甲可以被安装在哪个槽位，除此之外，对于其他盔甲，我们应该把这个值改为：

- `slot.armor.chest`（胸甲）；
- `slot.armor.legs`（护腿）；
- `slot.armor.feet`（靴子）
`
## 添加客户端资源

接下来，我们要为自定义盔甲添加客户端资源，**其设置物品纹理短名称、添加本地化名称步骤与第一章第三节所述步骤基本相同**，故这里不再赘述。

然而，我们还需要为自定义盔甲添加**附着物（Attachable）** ，以设置玩家穿着盔甲时的外观。

### 添加附着物

首先我们要准备附着物纹理，这些纹理位于 `RP/textures/models/armor`下，分为主纹理和腿部纹理两张。

在绘制时，我们可以参考原版附加包范例的`RP/textures/models/armor/diamond_1.png`和`RP/textures/models/armor/diamond_2.png`，作为初学者，我们甚至可以直接复制粘贴其到我们自己的包！

接下来就是正式添加附着物了，在资源包的根目录下面创建一个`attachables`文件夹，这个文件夹将会用于存储我们的附着物文件。

创建一个 JSON 文件，写入如下内容：

```json
{
   "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "example:first_helmet",
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                "default": "textures/models/armor/custom_1",
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            "geometry": {
                "default": "geometry.player.armor.helmet"
            },
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            "render_controllers": ["controller.render.armor"]
        }
    }
}
```

至于胸甲、护腿以及靴子的附着物写法与之类似，但需要注意：

- `geometry`对象下的`default`属性设置了盔甲使用的模型，制作其他类型的盔甲附着物时，我们要将其分别改为：
	- `geometry.player.armor.chestplate`（胸甲）；
	- `geometry.player.armor.leggings`（护腿）；
	- `geometry.player.armor.boots`（靴子）
- `textures`对象下的`default`属性设置了盔甲的附着物纹理：
	- 在头盔、胸甲、靴子中，我们要使用主纹理（即命名格式为`<armor_type>_1.png`的纹理）；
	- 在护腿中，我们要使用腿部纹理（即命名格式为`<armor_type>_2.png`的纹理）
- 不要忘记改`identifier`！

添加完客户端资源后，一套完整的自定义盔甲就制作好了。

## 小结

### 代码范例

#### 头盔

完整的头盔行为如下所示：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_helmet",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.helmet"
      }
    },
    "components": {
     "minecraft:icon": "example.first_helmet",
     "minecraft:max_stack_size": 1,
     "minecraft:enchantable": {
      "value": 10,
      "slot": "armor_head"
     },
     "minecraft:repairable": {
       "repair_items": [
        {
         "items": ["example:first_hemlet"],
         "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
        }
       ]
      },
     "minecraft:durability": {
       "max_durability": 200
     },
     "minecraft:wearable": {
       "protection": 5,
       "slot": "slot.armor.head"
    }
}
```

完整的头盔附着物如下所示：

```json
{
   "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "example:first_helmet",
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                "default": "textures/models/armor/custom_1",
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            "geometry": {
                "default": "geometry.player.armor.helmet"
            },
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            "render_controllers": ["controller.render.armor"]
        }
    }
}

```

#### 胸甲

完整的胸甲行为如下所示：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_chestplate",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.chestplate"
      }
    },
    "components": {
     "minecraft:icon": "example.first_chestplate",
     "minecraft:max_stack_size": 1,
     "minecraft:enchantable": {
      "value": 10,
      "slot": "armor_torso"
     },
     "minecraft:repairable": {
       "repair_items": [
        {
         "items": ["example:first_chestplate"],
         "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
        }
       ]
      },
     "minecraft:durability": {
       "max_durability": 500
     },
     "minecraft:wearable": {
       "protection": 8,
       "slot": "slot.armor.chest"
    }
}
```

完整的头盔附着物如下所示：

```json
{
   "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "example:first_chestplate",
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                "default": "textures/models/armor/custom_1",
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            "geometry": {
                "default": "geometry.player.armor.chestplate"
            },
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            "render_controllers": ["controller.render.armor"]
        }
    }
}

```

#### 护腿

完整的护腿行为如下所示：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_leggings",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.leggings"
      }
    },
    "components": {
     "minecraft:icon": "example.first_leggings",
     "minecraft:max_stack_size": 1,
     "minecraft:enchantable": {
      "value": 10,
      "slot": "armor_legs"
     },
     "minecraft:repairable": {
       "repair_items": [
        {
         "items": ["example:first_leggings"],
         "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
        }
       ]
      },
     "minecraft:durability": {
       "max_durability": 200
     },
     "minecraft:wearable": {
       "protection": 2,
       "slot": "slot.armor.legs"
    }
}
```

完整的护腿附着物如下所示：

```json
{
   "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "example:first_leggings",
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                "default": "textures/models/armor/custom_2",
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            "geometry": {
                "default": "geometry.player.armor.leggings"
            },
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            "render_controllers": ["controller.render.armor"]
        }
    }
}

```

#### 靴子

完整的靴子行为如下所示：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_boots",
      "menu_category": {
        "category": "equipment",
        "group": "itemGroup.name.boots"
      }
    },
    "components": {
     "minecraft:icon": "example.first_boots",
     "minecraft:max_stack_size": 1,
     "minecraft:enchantable": {
      "value": 10,
      "slot": "armor_feet"
     },
     "minecraft:repairable": {
       "repair_items": [
        {
         "items": ["example:first_boots"],
         "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
        }
       ]
      },
     "minecraft:durability": {
       "max_durability": 100
     },
     "minecraft:wearable": {
       "protection": 3,
       "slot": "slot.armor.feet"
    }
}
```

完整的头盔附着物如下所示：

```json
{
   "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "example:first_boots",
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                "default": "textures/models/armor/custom_1",
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            "geometry": {
                "default": "geometry.player.armor.boots"
            },
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            "render_controllers": ["controller.render.armor"]
        }
    }
}

```

### 结语

恭喜你，学会了创建属于自己的自定盔甲！

接下来，我们将会学习一项新技术——自定义陶片。


[^1]: [盔甲 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E7%9B%94%E7%94%B2)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
