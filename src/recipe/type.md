# 配方类型

> 若无特殊说明，本节代码实例均省略配方解锁相关字段。

在本节中，我们将会详细了解游戏中所有的配方类型。

## 合成配方

合成配方分为有序配方和无序配方两种，有序配方需要将原料按照一定的顺序排列进行合成，而无序配方则不需要。

合成配方在[前文](./README.md)已经有所介绍，这里不再赘述。

## 熔炼配方

熔炼配方旨在使用热源方块（如熔炉、高炉、篝火等）将物品进行加工与转换，如：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_furnace": {
    "description": {
      "identifier": "example:meat"
    },
    "tags": ["soul_campfire"],
    "input": "example:raw_meat",
    "output": {
      "item": "example:cooked_meat",
      "count": 1
    }
  }
}
```

在其中：

- `tags`属性设置了熔炼的配方标签；
- `input`属性设置了熔炼的原料；
- `output`属性设置了熔炼后的产物，支持[物品描述](./index.html#物品描述)。

下面是可用于熔炼配方的配方标签：

- `furnace`；
- `blast_furnace`；
- `smoker`；
- `campfire`；
- `soul_campfire`。

## 酿造配方

酿造配方旨在使用一个物品作为催化剂来转换一个物品，目前有两种可用的酿造配方：令药水效果发生转换的**酿造混合配方（Brewing Mix Recipe）** ，以及令药水容器发生转换的**酿造容器配方（Brewing Container Recipe）**。

> 什么是容器转换和药水效果转换？
>
> 容器转换指的是将一个药水容器转换为另一个药水容器，如原版将一般药水转换成喷溅药水的配方就属于容器转换；
>
> 而药水效果转换则指将一个药水容器中的效果转换为另一个效果，如原版将粗制药水转换为迅捷药水的配方就属于药水效果转换。

目前只有`brewing_stand`标签支持酿造配方。

> 注意：**如果酿造配方中的原料的最大堆叠大于 1，输入的所有物品将在一次转换过程中被全部消耗。**

### 混合配方

我们先来学习酿造混合配方：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_brewing_mix": {
    "description": {
      "identifier": "minecraft:brew_awkward_blaze_powder"
    },
    "tags": ["brewing_stand"],
    "input": "minecraft:potion_type:awkward",
    "reagent": "minecraft:blaze_powder",
    "output": "minecraft:potion_type:strength"
  }
}
```

这是原版粗制药水用烈焰粉转换为力量药水的数驱文件，在其中：

- `tags`属性设置了酿造混合的配方标签；
- `input`属性设置了酿造的原料；
- `reagent`属性设置了酿造的催化剂；
- `output`属性设置了酿造后的产物。

> 不幸的是，酿造混合配方中的数据值无法正常工作，除非原料是以下之一：
>
> - `minecraft:potion`；
> - `minecraft:splash_potion`；
> - `minecraft:lingering_potion`；
> - [附加标识符](./index.html#附加标识符)。

### 容器配方

接下来我们来学习酿造容器配方：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_brewing_container": {
    "description": {
      "identifier": "minecraft:brew_potion_sulphur"
    },
    "tags": ["brewing_stand"],
    "input": "minecraft:potion",
    "reagent": "minecraft:gunpowder",
    "output": "minecraft:splash_potion"
  }
}
```

这是原版药水通过火药转换为喷溅型药水的数驱文件，可以看到，酿造容器配方与酿造混合配方非常相似，但是其输出物品将会继承输入物品的数据值（如药水效果）。

酿造容器对原料的要求非常严格，其仅允许以下物品作为原料：

- `minecraft:potion`；
- `minecraft:splash_potion`；
- `minecraft:lingering_potion`；
- [附加标识符](./index.html#附加标识符)。

因为原料的数据值在酿造容器配方中会被输出物品继承，所以 `input` 和 `output` 中指定的数据值将会被忽略。

## 锻造配方

锻造配方分为**锻造转换配方（Smithing Transform Recipe）**和**锻造纹饰配方（Smithing Trim Recipe）**。

### 转换配方

锻造转换配方旨在使用一个物品作为材料，一个物品作为模板，以及一个物品作为附加材料，将它们进行融合，从而生成一个新的物品，如：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_smithing_transform": {
    "description": {
      "identifier": "minecraft:smithing_netherite_boots"
    },
    "tags": ["smithing_table"],
    "template": "minecraft:netherite_upgrade_smithing_template",
    "base": "minecraft:diamond_boots",
    "addition": "minecraft:netherite_ingot",
    "result": "minecraft:netherite_boots"
  }
}
```

这是原版使用钻石靴子和下界合金锭锻造下界合金靴子的数驱文件，在其中：

- `tags`属性设置了配方标签；
- `template`属性设置了锻造模板，其需要有`minecraft:transform_templates`标签；
- `base`属性设置了锻造的原料，其需要有`minecraft:transformable_items`标签；
- `addition`属性设置了锻造所需的材料，其需要有`minecraft:transform_materials`标签；
- `result`属性设置了锻造后的产物。

> 锻造转换配方中的`addition`属性被硬编码为仅允许`minecraft:netherite_ingot`（即下界合金锭）作为锻造材料，详情请见[MCPE-191386](https://bugs.mojang.com/browse/MCPE/issues/MCPE-191386)。

### 纹饰配方

锻造纹饰配方与转换配方类似，如：

```json
{
  "format_version": "1.21.0",
  "minecraft:recipe_smithing_trim": {
    "description": {
      "identifier": "minecraft:smithing_diamond_boots_jungle_quartz_trim"
    },
    "tags": ["smithing_table"],
    "template": "minecraft:jungle_temple_smithing_template",
    "base": "minecraft:diamond_boots",
    "addition": "minecraft:quartz"
  }
}
```

可以看到，纹饰配方与转换配方比较明显的区别在于，我们无需手动指定`result`属性，而由游戏自动生成锻造后产物。

除此之外，纹饰配方对输入物品所具有的标签也有要求：

- `template`属性对应的物品需要有`minecraft:trim_templates`标签；
- `base`属性对应的物品需要有`minecraft:trimmable_armors`标签；
- `addition`属性对应的物品需要有`minecraft:trim_materials`标签。

## 小结

到现在，我们已经了解了所有类型的配方，其可以用一张表来概括：

| 配方类型     | 描述                                                   |
| ------------ | ------------------------------------------------------ |
| 熔炼配方     | 用于热源的配方，原料被加热并转化为输出物品             |
| 有序配方     | 用于工作台、且原料有一定排列顺序的配方                 |
| 无序配方     | 用于工作台、但原料无排列顺序的配方                     |
| 酿造容器配方 | 用于酿造台，将一种容器的药水转换为另一种容器药水的配方 |
| 酿造混合配方 | 用于酿造台，将一种效果的药水转换为另一种效果药水的配方 |
| 锻造转换配方 | 用于锻造台的锻造转换配方                               |
| 锻造纹饰配方 | 用于锻造台的锻造纹饰配方                               |

在下一节，我们将会学习一个重要特性——配方解锁。