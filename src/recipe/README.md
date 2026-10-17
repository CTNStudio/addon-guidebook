# 配方进阶

**配方（Recipes）** 是一种引导新玩家游玩 Minecraft 的方式，通过帮助玩家了解合成、烧炼以及其他的方块和物品转化方式来使玩家熟悉游戏，命令`/recipe`可给予或剥夺玩家的配方。 [^1]。

所有配方文件都按照 JSON 格式编写，存储在行为包根目录下的`recipes`文件夹以及其子文件夹中。

在当前版本中，除了铁砧的行为由物品数据文件设置、织布机配方为硬编码之外，其余所有配方都实现了数据驱动，我们可以自如地对其进行修改。

## 温习旧知

首先来复习一下之前学习过的有序配方和无序配方：

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
- `identifier`设置了配方的命名空间 ID，注意这里的命名空间是`example`，在实际的项目中不建议使用；
- `tags`设置了配方的标签，配方标签决定了配方适用于哪种合成方块，这里是`crafting_table`，即工作台；
- `pattern`设置了配方的排列样式，这是一个字符串类型的数组，一个元素代表合成方块中的一行，每个元素由 1-3 个字符组成，每个字符都代表着合成所需要的物品；
- `key`解释了在`pattern`中的字符所代表的具体物品，这里我们使用`#`代表`example:first_item`；
- `unlock`设置了配方解锁的条件，这里我们设置玩家需要拥有`example:first_item`才能解锁配方；
- `result`设置了配方合成的结果的物品，这里我们设置合成结果为`example:first_block`。

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_shapeless": {
    "description": {
      "identifier": "example:first_block_shapeless"
    },
    "tags": ["crafting_table"],
    "ingredients": [
      {
        "item": "example:first_item",
        "count": 9
      }
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

- 无序配方类型为`minecraft:recipe_shapeless`；
- 无序配方不需要`pattern`与`keys`，但需要`ingredients`设置合成的原料，`ingredients`是一个数组，每个元素代表合成所需要的物品以及数量，这里我们设置需要 9 个`example:first_item`来合成一个`example:first_block`。

## 配方类型

目前一共存在以下类型的配方：

- 有序配方；
- 无序配方；
- 熔炼配方；
- 酿造容器配方；
- 酿造混合配方；
- 锻造转换配方；
- 锻造纹饰配方。

我们将会在[下一节](./type.md)中详细学习这些配方。

## 配方标签

配方标签决定了配方适用于哪种合成方块。

下面是可用于有序或无序配方的配方标签：

- `crafting_table`；
- `stonecutter`；
- `smithing_table`。

下面是可用于熔炼配方的配方标签：

- `furnace`；
- `blast_furnace`；
- `smoker`；
- `campfire`；
- `soul_campfire`。

除此之外，还有一个`brewing_stand`标签可用于酿造配方。

## 物品描述

在配方文件中，我们可以使用**物品描述（Item Descriptors）** 来描述配方中的原料以及输出物品，例如：

```json
"minecraft:iron_ingot"
```

这是最简单的一种物品描述：直接使用物品的 ID，除此之外，我们还可以将其写成 JSON 对象的格式：

```json
{
  "item": "minecraft:stone",
  "data": 1,
  "count": 3
}
```

在这里：

- `item`属性表示物品的 ID；
- `data`属性表示物品的数据值；
- `count`属性表示物品的数量。

在上面的例子中，我们描述了一个 ID 为`minecraft:stone`，数据值为 1（即为花岗岩），数量为 3 的物品。

> `count`属性仅在有序配方和熔炉配方的输出物品，以及无序配方的原料和输出物品中有效，而在其他类型提供该属性并没有用处。

当然，`data`不仅可以填整数，还可以填写 Molang 表达式，这些表达式在世界加载时只评估一次。目前唯一被证明有效的 Molang 表达式为`q.get_actor_info_id`，其用于根据所提供的实体 ID 查找对应的刷怪蛋数据值[^3]，如：

```json
{
  "item": "minecraft:spawn_egg",
  "data": "q.get_actor_info_id('minecraft:chicken')"
}
```

### 物品标签

除此之外，我们还可以用物品标签表示所有属于该标签的物品，如：

```json
{
  "tag": "minecraft:planks"
}
```

### 附加标识符

**附加标识符（Additional Identifier）** 用于在酿造配方中描述原版的基本药水，其只支持字符串表示法，如：

```json
"minecraft:potion_type:strength"
```

可以发现，这类标识符的格式为`minecraft:potion_type:<effect>`，其中`<effect>`可以是以下之一：

- `water`
- `awkward`
- `mundane`
- `thick`
- `healing`
- `regeneration`
- `swiftness`
- `strength`
- `harming`
- `poison`
- `slowness`
- `weakness`
- `water_breathing`
- `fire_resistance`
- `nightvision`
- `invisibility`
- `leaping`
- `slow_falling`
- `turtle_master`
- `wither`

除此之外，我们可以使用`long_`和`strong_`前缀表示延长版药水和二级药水，如`minecraft:potion_type:long_poison`。

## 配方优先级

想象一种情况：如果有两个配方，它们的原料以及排列方式完全相同，但输出的物品不同，那么合成时应该选择哪一个配方呢？

一般地，游戏会采用下面的方式来决定：

- 世界行为包设置中位于较高位置的包会优先于较低位置的包；
- 有序配方优先于无序配方；
- 配方优先级高的配方会优先于配方优先级低的配方。

在这里，我们引入了「配方优先级」的概念，在多个配方可能适用于给定情况时，**配方优先级（Recipe Priority）** 将决定输出的物品，如：

```json
{
  "minecraft:recipe_shaped": {
    "priority": 2
  }
}
```

`priority`即为配方的优先级，其是一个整数，数值越小，优先级越高，默认为 0。

## 忽略对称

在有序配方中，如果玩家按照正确排列样式的镜像样式排列原料，工作台依然会输出物品，那么`assume_symmetry`来取消这个行为：

```json
{
  "minecraft:recipe_shaped": {
    "assume_symmetry": true
  }
}
```

例如，在开启`assume_symmetry`前，这两种排列方式将会被视为相同的排列方式：

```plaintext
##
 ##
```

```plaintext
 ##
##
```

而开启之后，我们就可以为其设置不同的输出物品了[^2]：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_shaped": {
    "description": {
      "identifier": "example:zig"
    },
    "tags": ["crafting_table"],
    "assume_symmetry": false,
    "pattern": ["##", " ##"],
    "key": {
      "#": {
        "item": "minecraft:planks"
      }
    },
    "result": {
      "item": "example:zig"
    }
  }
}
```

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_shaped": {
    "description": {
      "identifier": "example:zag"
    },
    "tags": ["crafting_table"],
    "assume_symmetry": false,
    "pattern": [" ##", "##"],
    "key": {
      "#": {
        "item": "minecraft:planks"
      }
    },
    "result": {
      "item": "example:zag"
    }
  }
}
```

> 为方便说明，上方的配方文件省略了配方解锁相关内容

[^1]: [配方 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%85%8D%E6%96%B9)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
[^2]: [Recipe Documentation - Shaped Recipe](https://learn.microsoft.com/zh-cn/minecraft/creator/reference/content/recipereference/examples/recipedefinitions/minecraftrecipe_shaped?view=minecraft-bedrock-stable)
[^3]: [Recipes | Bedrock Wiki](https://wiki.bedrock.dev/loot/recipes#item-descriptors)
