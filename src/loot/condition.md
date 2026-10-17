# 战利品表条件

**战利品表条件**用于在生成战利品前检查是否符合条件，只有符合条件才会生成战利品。

所有条件都存储在`conditions`数组中，如果不符合数组中的任何一个条件，战利品将不会输出，数组中的剩余条件将会被忽略。

### `has_mark_variant`

`has_mark_variant`条件要求触发战利品表的生物具有特定标记变种值，如：

```json
{
  "conditions": [
    {
      "condition": "has_mark_variant",
      "value": 1
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

这里的标记变种值由`minecraft:mark_variant`组件设置。

### `has_variant`

`has_variant`条件要求触发战利品表的生物具有特定变种值，如：

```json
{
  "rolls": 1,
  "entries": [
    {
      "type": "item",
      "name": "minecraft:pearlescent_froglight",
      "weight": 1
    }
  ],
  "conditions": [
    {
      "condition": "has_variant",
      "value": 2
    },
    {
      "condition": "entity_killed",
      "entity_type": "minecraft:magma_cube"
    }
  ]
}
```

这里的变种值由`minecraft:variant`组件设置。

### `killed_by_player_or_pets`

`killed_by_player_or_pets`条件要求触发战利品表的生物必须被玩家或玩家的宠物杀死：

```json
 "conditions": [
    {
      "condition": "killed_by_player_or_pets"
    },
 ]
```

### `random_chance`

`random_chance`条件设置战利品掉落的概率，如：

示例：

```json
"conditions": [
    {
        "condition": "random_chance",
        "chance": 0.50
    }
]
```

### `random_chance_with_looting`

`random_chance_with_looting`条件类似于`random_chance`条件，但会受到掠夺附魔的影响：

```json
"conditions": [
    {
        "condition": "killed_by_player"
    },
    {
        "condition": "random_chance_with_looting",
        "chance": 0.11,
        "looting_multiplier": 0.02
    }
]
```

这里的`looting_multiplier`就是当触发战利品的物品具有掠夺附魔时，战利品掉落的概率。

### `random_difficulty_chance`

`random_difficulty_chance`条件根据难度级别控制战利品掉落的概率，如：

```json
{
  "conditions": [
    {
      "condition": "random_difficulty_chance",
      "default_chance": 0.2,
      "peaceful": 0.1,
      "hard": 0.5
    }
  ]
}
```

在上面的范例中：

- `default_chance`是默认难度下的战利品掉落概率；
- `peaceful`是和平难度下的战利品掉落概率；
- `hard`是困难难度下的战利品掉落概率。

### `random_regional_difficulty_chance`

`random_regional_difficulty_chance`条件根据区域难度确定战利品掉落概率，如：

```json
{
  "conditions": [
    {
      "condition": "random_regional_difficulty_chance",
      "max_chance": 0.9
    }
  ]
}
```

区域难度越大，战利品掉落几率越大，最大可以到达`max_chance`设置的概率。

### `match_tool`

`match_tool`条件检查触发战利品掉落的物品是否与提供的条件匹配，如：

```json
{
  "conditions": [
    {
      "condition": "match_tool",
      "enchantments": [
        {
          "enchantment": "sharpness",
          "levels": {
            "range_max": 6
          }
        }
      ],
      "item": "minecraft:diamond_sword",
      "count": 1,
      "durability": {
        "range_min": 1
      }
    }
  ]
}
```

目前可以检查物品的以下属性：

- 数量；
- 耐久度；
- 附魔；
- 物品的 ID 与标签。

> - **count:** 物品的数量
>   - range_max: 最大值
>   - range_min: 最小值
> - **durability:** 物品的耐久
>   - range_max: 最大值
>   - range_min: 最小值
> - **enchantments:** 附魔列表
>   - enchantment: 附魔 ID
>   - levels: 附魔等级
>   - range_max: 最大值
>   - range_min: 最小值
> - **item:** 物品 ID
> - **minecraft:match_tool_filter_any\:** 要求物品具有的任意标签列表
> - **minecraft:match_tool_filter_all\:** 要求物品必须具有的所有标签列表
> - **minecraft:match_tool_filter_none\:** 要求物品不能具有的标签列表

再来看一个判断物品标签的范例：

```json
{
  "conditions": [
    {
      "condition": "match_tool",
      "minecraft:match_tool_filter_any": [
        "minecraft:iron_tier",
        "minecraft:golden_tier",
        "minecraft:diamond_tier"
      ],
      "minecraft:match_tool_filter_all": ["minecraft:is_tool"],
      "minecraft:match_tool_filter_none": ["minecraft:is_shovel"]
    }
  ]
}
```

在这里，物品需要具有`minecraft:iron_tier`、`minecraft:golden_tier`或`minecraft:diamond_tier`中的任意一个，且必须具有`minecraft:is_tool`标签，并且不能具有`minecraft:is_shovel`标签。

### `killed_by_entity`

`killed_by_entity`条件要求触发战利品表的生物必须被特定实体杀死，如：

```json
{
  "conditions": [
    {
      "condition": "killed_by_entity",
      "entity_type": "minecraft:skeleton"
    }
  ]
}
```
