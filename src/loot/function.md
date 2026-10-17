# 战利品表函数

在本节中，我们将会了解所有的战利品表函数，以便于创建属于自己的战利品表。

## 基础函数

这些函数一般可以应用到所有物品上，以允许我们对生成的战利品进行一些修改。

### `set_count`

`set_count`函数用于设置物品的数量：

```json
{
  "functions": [
    {
      "function": "set_count",
      "count": {
        "min": 2,
        "max": 3
      }
    }
  ]
}
```

其中，`min`和`max`分别表示物品数量的最小值和最大值。

### `set_name`

`set_quality`函数用于设置物品的名称：

```json
{
  "functions": [
    {
      "function": "set_name",
      "name": "自定义名称"
    }
  ]
}
```

### `set_lore`

`set_lore`函数用于设置物品的 Lore：

```json
{
  "functions": [
    {
      "function": "set_lore",
      "lore": ["自定义 Lore"]
    }
  ]
}
```

Lore 将会显示在物品名称的下方，其支持格式化代码，但不支持本地化。

在这里，`lore`数组的每一个元素都将被渲染为独立的一行，除此之外，我们还可以在一条 Lore 字符串中使用`\n`换行。

### `set_data`

`set_data`函数设置物品的数据值：

```json
{
  "functions": [
    {
      "function": "set_data",
      "data": 1
    }
  ]
}
```

`data`属性物品的数据值，它也可以写成一个范围：

```json
{
  "functions": [
    {
      "function": "set_data",
      "data": {
        "min": 1,
        "max": 2
      }
    }
  ]
}
```

### `set_block_state`

`set_block_state`函数用于设置方块的状态：

```json
{
  "function": "random_block_state",
  "block_state": "example:state",
  "values": 1
}
```

`block_state`属性表示要设置的方块状态，`values`属性表示方块状态的值。

### `set_aux_value`

`set_aux_value`函数用于设置物品的辅助值：

```json
{
  "function": "random_aux_value",
  "values": {
    "min": 1,
    "max": 5
  }
}
```

### `set_durability`

`set_durability`函数用于设置物品的耐久度：

```json
{
  "function": "set_durability",
  "damage": {
    "min": 0.25,
    "max": 1
  }
}
```

`damage`属性设置了物品的剩余耐久度，其应介于 0 到 1 之间，其中 0 是物品可能的最小耐久度， 1 是未损坏。

## 附魔函数

附魔函数允许我们为战利品设置独特的附魔。

### `enchant_random_gear`

`enchant_random_gear`函数允许我们使用与原版生物装备相同的算法为一件物品附魔。 

```json
{
  "type": "item",
  "name": "minecraft:chainmail_boots",
  "weight": 1,
  "functions": [
    {
      "function": "enchant_random_gear",
      "chance": 0.25
    }
  ]
}
```

在这里，我们使用`chance`属性来设置被物品被附魔的几率。

> 游戏会综合考虑游戏难度和`chance`决定附魔概率，也就是说 **`chance`为`1.0`并不意味着装备有 100% 的几率会被附魔。**
> 
> 例如，在和平与简单难度下，附魔概率将始终为 0，在困难难度下，若`chance`为`1.0`，则附魔概率始终为 100%。

### `enchant_randomly`

`enchant_randomly`函数将会为战利品生成与其兼容的随机附魔，如：

```json
{
  "type": "item",
  "name": "minecraft:leather_helmet",
  "weight": 1,
  "functions": [
    {
      "function": "enchant_randomly",
      "treasure": true
    }
  ]
}
```

`treasure`属性设置了生成附魔时是否会生成**宝藏型魔咒**，即无法通过附魔台获得的附魔，包括经验修补、冰霜行者、绑定诅咒、消失诅咒、灵魂疾行、迅捷潜行与风爆。

### `enchant_with_levels`

`enchant_with_levels`函数将会为战利品生成与其兼容的随机附魔，并根据与给定等级设置魔咒等级，如：

```json
{
  "type": "item",
  "name": "minecraft:diamond_sword",
  "weight": 1,
  "functions": [
    {
      "function": "enchant_with_levels",
      "treasure": true,
      "levels": {
        "min": 10,
        "max": 25
      }
    }
  ]
}
```

类似地，`treasure`属性设置了生成附魔时是否会生成宝藏型魔咒。

### `specific_enchants`

`specific_enchants`函数允许我们对战利品应用特定魔咒，如：

```json
{
  "type": "item",
  "name": "minecraft:stick",
  "weight": 1,
  "functions": [
    {
      "function": "specific_enchants",
      "enchants": ["knockback", "fire_aspect"]
    }
  ]
}
```

我们还可以设置魔咒的等级，如：

```json
{
  "type": "item",
  "name": "minecraft:stick",
  "weight": 1,
  "functions": [
    {
      "function": "specific_enchants",
      "enchants": [
        {
          "id": "knockback",
          "level": 1
        },
        {
          "id": "unbreaking",
          "level": 3
        }
      ]
    }
  ]
}
```

## 专有函数

某些战利品表函数只能应用到特定的物品上，否则会报错或者失效。

### `furnace_smelt`

```admonish info
适用范围：具有熔炼配方的物品
```

如果要返回的战利品具有熔炼配方，并且战利品表是由被实体被烧死而触发的，则将会输出熔炼后的物品作为战利品。

因此，此函数仅在与`minecraft:loot`组件结合使用时才有效。

在下面的范例中，实体被火烧死时掉落会木炭，否则会掉落橡木原木。

```json
{
  "type": "item",
  "name": "minecraft:oak_log",
  "weight": 1,
  "functions": [
    {
      "function": "furnace_smelt"
    }
  ]
}
```

### `set_book_contents`

```admonish info
适用范围：书与笔、成书
```

`set_book_contents`函数用于设置书的内容，如：

```json
{
  "function": "set_book_contents",
  "title": "范例书籍",
  "author": "方漓猫",
  "pages": ["114\n514", "1919810"]
}
```

显而易见，`title`属性设置了书籍的标题，`author`属性设置了书籍的作者，`pages`属性设置了书籍的每一页内容。

`pages`属性最多填 50 个字符串，每个字符串最多 798 个字符，所有页面字符数限制为 12800 个字符[^1]。

`pages`属性支持格式化代码与`\n`。除此之外，我们还可以对书籍的内容进行本地化，来看一个官方的例子：

```json
{
  "type": "item",
  "name": "minecraft:written_book",
  "functions": [
    {
      "function": "set_book_contents",
      "author": "Steve",
      "title": "Creator Woes",
      "pages": [
        "{\"rawtext\":[ {\"translate\":\"custom.book.page.1\"}]}",
        "{\"rawtext\":[ {\"translate\":\"custom.book.page.2\"}]}"
      ]
    }
  ]
}
```

> 请务必对特殊字符（如 `"` 和 `\`）使用转义表示。

### `exploration_map`

```admonish info
适用范围：地图
```

`exploration_map`函数用于设置藏宝图的类型，如：

```json
{
  "type": "item",
  "name": "minecraft:map",
  "weight": 1,
  "functions": [
    {
      "function": "exploration_map",
      "destination": "mineshaft"
    }
  ]
}
```

`destination`属性设置了藏宝图的类型，可选值如下：

- `buriedtreasure`：埋藏的宝藏；
- `endcity`：末地城；
- `fortress`：堡垒遗迹；
- `mansion`：丛地府邸；
- `mineshaft`：废弃矿井；
- `monument`：纪念碑；
- `pillageroutpost`：掠夺者前哨站；
- `ruins`：海底废墟；
- `shipwreck`：沉船；
- `stronghold`：要塞；
- `temple`：神庙；
- `village`：村庄

### `set_banner_details`

```admonish info
适用范围：旗帜
```

`set_banner_details`函数用于设置旗帜的类型，如：

```json
{
  "type": "item",
  "name": "minecraft:banner",
  "weight": 1,
  "functions": [
    {
      "function": "set_banner_details",
      "type": 1
    }
  ]
}
```

`type`为 1 会生成不祥旗帜，0 会生成普通旗帜。

### `random_dye`

```admonish info
适用范围：皮革盔甲、皮革马铠
```

`random_dye`函数用于随机为皮革盔甲等可以被染料染色的物品染色，如：

```json
{
  "type": "item",
  "name": "minecraft:leather_chestplate",
  "weight": 1,
  "functions": [
    {
      "function": "random_dye"
    }
  ]
}
```

### `set_actor_id`

```admonish info
适用范围：刷怪蛋
```

`set_actor_id`函数用于设置刷怪蛋的对应的实体类型，如：

```json
{
  "type": "item",
  "name": "minecraft:spawn_egg",
  "weight": 1,
  "functions": [
    {
      "function": "set_actor_id",
      "id": "minecraft:zombie"
    }
  ]
}
```

可以看出，`id`值设置了刷怪蛋对应的实体类型，除此之外，我们也可以省略`id`，这将会令掉落的刷怪蛋继承与该战利品表关联的实体类型。

### `fill_container`

```admonish info
适用范围：容器
```

`fill_container`函数用于填充容器，如：

```json
{
  "type": "item",
  "name": "minecraft:chest",
  "functions": [
    {
      "function": "fill_container",
      "loot_table": "loot_tables/chests/simple_dungeon.json"
    }
  ]
}
```

当玩家放下这个箱子时，箱子会自动填充`loot_table`指定的的战利品，注意`loot_table`不可以指向当前战利品表。

> 建议在使用该函数时始终使用`set_name`函数为容器命名。
>
> 否则生成的容器将会看起来与普通的容器没有任何不同。

[^1]: [Item Functions | Bedrock Wiki](https://wiki.bedrock.dev/loot/item-functions)
