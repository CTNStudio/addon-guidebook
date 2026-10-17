
食品是 Minecraft 中非常重要的游戏内容，通过食用食物，我们可以恢复饥饿值、获得一些增益效果。

在附加包中，一般使用`minecraft:food`组件以及其他一些组件来创建一个自定义食品。

## 开始之前

在开始之前，我们先创建一个没有任何功能的普通物品，并确保其格式版本在 1.21.0 以上，具体过程请参照第一章第三节。

完成后，物品行为部分的代码应该像下面这样：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_food",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": "example.first_food"
    }
  }
}
```

## 添加分组

首先，我们来为这个自定义食品添加创造模式物品栏分类，例如：

```json
{
  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_food",
      "menu_category": {
        "category": "nature",
        "group": "itemGroup.name.crop"
      }
    }
     ...
}
```

在这个范例中，我们将自定义食品添加到了自然分类下的农作物分组，类似的分组还有：

- `equipment`分类下的`itemGroup.name.miscFood`分组；
- `nature`分类下的`itemGroup.name.rawFood`分组；
- `equipment`分类下的`itemGroup.name.cookedFood`分组。
## 添加组件

首先，我们为食物添加最核心的组件——`minecraft:food`组件：

```json
   ...
   "components": {
      "minecraft:icon": "example.first_food",
      "minecraft:food": {
        "nutrition": 6,
        "can_always_eat": true,
        "saturation_modifier": 0.6
      }
   ...
```

在其中：

- `nutrition`设置了食物食用后恢复的饥饿值，填正整数；
- `saturation_modifier`设置食物的饱和度修饰符；
- `can_always_eat`设置在饥饿值满时是否可以食用该食物。

> **关于饥饿机制**
> 
> 玩家的饥饿状态由以下3个变量组成：
>
> - **饥饿值（Hunger Value）**：取值范围是从0到20。初始值为20（![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)![](https://zh.minecraft.wiki/images/Hunger.svg?5c3ee)）。
> - **饱和度（Saturation）**：在饥饿值被消耗前会首先被消耗，其值不能超过目前的饥饿值，最低值为0，初始值为5。
> - **消耗度（Exhaustion）**：取值范围是从0到4，在玩家从事特定活动时会增加（见[§ 饥饿因素](https://zh.minecraft.wiki/w/%E9%A5%A5%E9%A5%BF?variant=zh-cn#饥饿因素)）。消耗度的初始数值为0，当消耗度到达4时，就会重置为0并扣除1点饥饿值或饱和度。[^1]

关于`saturation_modifier`的填写，可以参考下表[^2]：

| 等级名称 | 营养价值 | 食物                                                   |
| ---- | ---- | ---------------------------------------------------- |
| 超自然  | 1.2  | 金苹果、附魔金苹果、金胡萝卜                                       |
| 好    | 0.8  | 牛排、熟羊肉、熟猪肉、熟鲑鱼                                       |
| 普通   | 0.6  | 烤马铃薯、甜菜汤、甜菜根、熟鳕鱼、熟兔肉、蘑菇煲、谜之炖菜                        |
| 低    | 0.3  | 苹果、紫颂果、西瓜片、毒马铃薯、马铃薯、南瓜派、生牛肉、生鸡肉、生羊肉、生猪肉、生兔肉、甜浆果、发光浆果 |
| 差    | 0.1  | 蛋糕、曲奇、烤海带、蜂蜜、河豚、生鳕鱼、生鲑鱼、腐肉、热带鱼、蜘蛛眼                   |

此时进入游戏，我们的食物已经可以食用，但还缺少食用的音效以及动画，接下来我们将着手解决这一问题。

添加以下组件到我们的物品中：

```json
   ...
   "components": {
      "minecraft:icon": "example.first_food",
      "minecraft:use_animation": "eat",
      "minecraft:use_modifiers": {
        "use_duration": 1.6,
        "movement_modifier": 0.5
      },
      "minecraft:glint": true
      ...
```

`minecraft:use_animation`组件设置了物品的使用动画，这里为`eat`，即为食用。

`minecraft:use_modifiers`组件设置了物品的使用修饰符，在其中：

- `use_duration`设置了物品的使用时间；
- `movement_modifier`设置了使用物品对移动速度的影响效果。

这时进入游戏，我们的物品就应该具有了完整的行为。

## 添加客户端资源

接下来，我们要为自定义食品添加客户端资源，其步骤与第一章第三节所述步骤完全相同，故这里不再赘述。

添加完客户端资源后，一个完整的自定义食品就制作好了。

## 小结

通过本节的学习，我们学会了如何添加一个自定义食品，完整的物品行为代码应该如下所示：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_food",
      "menu_category": {
        "category": "nature",
        "group": "itemGroup.name.crop"
      }
    },
    "components": {
      "minecraft:icon": "example.first_food",
      "minecraft:use_animation": "eat",
      "minecraft:use_modifiers": {
        "use_duration": 1.6,
        "movement_modifier": 0.5
      },
      "minecraft:food": {
        "nutrition": 6,
        "can_always_eat": true,
        "saturation_modifier": 0.6
      },
      "minecraft:glint": true
    }
  }
}
```

[^1]: [饥饿 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%A5%A5%E9%A5%BF)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权

[^2]: [食物 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%A3%9F%E7%89%A9)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
