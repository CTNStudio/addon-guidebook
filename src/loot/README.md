# 战利品表进阶

**战利品表（Loot table）** 是一种技术性 JSON 文件，用于决定在何种情况下生成何种物品，比如自然生成的容器和可疑的方块内容物、破坏方块时的掉落物、杀死实体时的掉落物、钓鱼时可以钓上的物品、猪灵的以物易物等[^1]。

战利品表理论上可以放置在行为包内的任何位置，但建议将其置于`loot_tables`文件夹下，以便于管理。

## 基本结构

以下是一个战利品表的基本结构：

```json
{
  "pools": []
}
```

## 池

在`pools`数组下，我们可以写入不同的池，如：

```json
{
  "rolls": 1,
  "entries": [
    {
      "type": "item",
      "name": "example:first_item"
    }
  ]
}
```

一般而言，池分为**加权随机池（Weighted Random Pool）** 和**分层池（Tiered Pool）** 两种[^2]。

### 加权随机池

加权随机池是最常见的一种池，广泛用于游戏之中，如：

```json
{
  "rolls": {
    "min": 1,
    "max": 5
  },
  "entries": [
    {
      "type": "item",
      "name": "minecraft:golden_apple",
      "weight": 30
    },
    {
      "type": "item",
      "name": "minecraft:apple",
      "weight": 70
    }
  ]
}
```

在其中：

- `rolls`设置了池中物品的生成次数，可以是一个数字，也可以是一个对象；
  - `min`表示最小生成次数，`max`表示最大生成次数；
- `entries`设置了池中的战利品，一个池中可以包含多种战利品，选中哪种的概率由`weight`决定
  - 每种战利品被选中的具体概率为`weight` / `entries`中所有`weight`的总和；
- `quality`设置了战利品的品质，只有在使用带有海之眷顾的鱼竿钓鱼时才会生效，故这里没有体现。

### 分层池

> 所有分层池中的`entries`的每一个元素中的条件都将被忽略，但对池本身的条件仍然可用。

分层池一般用于从条目中精确选择一种战利品，如：

```json
{
  "tiers": {
    "initial_range": 1,
    "bonus_rolls": 3,
    "bonus_chance": 0.095
  },
  "entries": [
    {
      "type": "loot_table",
      "name": "loot_tables/entities/pig.json"
    },
    {
      "type": "loot_table",
      "name": "loot_tables/entities/sheep.json"
    },
    {
      "type": "loot_table",
      "name": "loot_tables/entities/cow.json"
    }
  ]
}
```

在分层池中，每一个`entries`中的战利品都会被按照定义的顺序被索引，从 1 开始，我们以上文出现的战利品为例：

```json
{
  "entries": [
    {
      "type": "loot_table",
      "name": "loot_tables/entities/pig.json" // 索引为 1
    },
    {
      "type": "loot_table",
      "name": "loot_tables/entities/sheep.json" // 索引为 2
    },
    {
      "type": "loot_table",
      "name": "loot_tables/entities/cow.json" // 索引为 3
    }
  ]
}
```

`tiers`对象是分层的关键：

```json
{
  "tiers": {
    "initial_range": 1,
    "bonus_rolls": 1,
    "bonus_chance": 0.25
  }
}
```

首先，游戏会决定战利品表的**起始索引**，这个索引是 1 到`initial_range`范围内的一个随机整数，如果没有提供提供`initial_range`，则强制起始索引为 1。

接下来，游戏会进行`bonus_rolls`次的尝试，每次尝试都会有`bonus_chance`的概率成功，成功则将输出索引设置为当前索引 + 1，否则保持不变。

尝试全部结束后，游戏会获取输出索引对应的战利品，并进行生成。

## 条目

条目位于`entries`数组中，每一个条目都对应一种战利品，目前有三种类型的条目。

### 物品条目

顾名思义，物品条目会生成一种物品，如：

```json
{
  "type": "item",
  "name": "minecraft:bread",
  "weight": 1
}
```

其中的`name`属性即为物品的 ID。

### 战利品表条目

战利品表条目会生成另一个战利品表，如：

```json
{
  "type": "loot_table",
  "name": "loot_tables/entity/pig.json",
  "weight": 1
}
```

其中的`name`属性即为要引用的战利品表的路径。

### 空条目

空条目不会生成任何战利品：

```json
{
  "type": "empty",
  "weight": 1
}
```

## 条件

条件位于`conditions`数组中，用于在生成战利品前检查是否符合条件，如：

```json
{
  "conditions": [
    {
      "condition": "random_chance_with_looting",
      "chance": 0.5,
      "looting_multiplier": 0.02
    }
  ],
  "rolls": 1,
  "entries": [
    {
      "type": "item",
      "name": "minecraft:apple",
      "weight": 1
    }
  ]
}
```

关于战利品表条件的具体介绍，请见[后文](./condition.md)。

## 函数

战利品表函数的功能非常强大，位于条目对象的`functions`数组下，允许我们对生成的战利品进行一些修改，如：

```json
{
  "type": "item",
  "name": "minecraft:diamond",
  "weight": 1,
  "functions": [
    {
      "function": "set_count",
      "count": {
        "min": 1,
        "max": 5
      }
    }
  ]
}
```
关于战利品表函数的具体介绍，请见[后文](./function.md)。

[^1]: [战利品表 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E6%88%98%E5%88%A9%E5%93%81%E8%A1%A8)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
[^2]: [Loot Table | Bedrock Wiki](https://wiki.bedrock.dev/loot/loot-tables)
