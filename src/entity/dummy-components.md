# 虚拟组件

**虚拟组件（Dummy Components）** 指一类不会对实体行为产生实质性影响，需要配合其他机制才能发挥作用的组件。

它们可将数据存储在实体上，并通过 Molang 查询来获得对应的数据。

还记着上一节最后的范例吗：

```json
{
  "minecraft:entity": {
    "component_groups": {
      "example:variant_1": {
        "minecraft:variant": {
          "value": 0
        }
      },
      "example:variant_2": {
        "minecraft:variant": {
          "value": 1
        }
      },
      "example:variant_3": {
        "minecraft:variant": {
          "value": 2
        }
      }
    },
    "events": {
      "minecraft:entity_spawned": {
        "sequence": [
          {
            "randomize": [
              {
                "trigger": "example:variant_1",
                "weight": 5
              },
              {
                "trigger": "example:variant_2",
                "weight": 3
              },
              {
                "trigger": "example:variant_3",
                "weight": 2
              }
            ]
          }
        ]
      }
    }
  }
}
```

这里的`minecraft:variant`就是一个虚拟组件，它被用于声明实体的变种类型，并可以被附加包的客户端部分所查询。

## 虚拟组件列表

> 这里的表格引用了 [Bedrock Wiki](https://wiki.bedrock.dev/entities/dummy-components)，按照原有的协议授权

| 类型       | 查询方法               | 组件                           | 说明                                                               |
| ---------- | ---------------------- | ------------------------------ | ------------------------------------------------------------------ |
| **整数**   | `q.variant`            | `minecraft:variant`            |                                                                    |
| **整数**   | `q.mark_variant`       | `minecraft:mark_variant`       |                                                                    |
| **整数**   | `q.skin_id`            | `minecraft:skin_id`            |                                                                    |
| **整数\*** |                        | `minecraft:color`              |                                                                    |
| **整数\*** |                        | `minecraft:color2`             |                                                                    |
| 布尔       | `q.is_illager_captain` | `minecraft:is_illager_captain` |                                                                    |
| 布尔       | `q.is_baby`            | `minecraft:is_baby`            | 禁用 `minecraft:breedable` 的使用                                  |
| 布尔       | `q.is_sheared`         | `minecraft:is_sheared`         |                                                                    |
| 布尔       | `q.is_saddled`         | `minecraft:is_saddled`         |                                                                    |
| 布尔       | `q.is_tamed`           | `minecraft:is_tamed`           |                                                                    |
| 布尔       | `q.is_chested`         | `minecraft:is_chested`         | 实体死亡时会掉落箱子                                               |
| 布尔       | `q.is_powered`         | `minecraft:is_charged`         |                                                                    |
| 布尔       | `q.is_stunned`         | `minecraft:is_stunned`         |                                                                    |
| 布尔       | `q.can_climb`          | `minecraft:can_climb`          | 允许实体攀爬梯子                                                   |
| 布尔       | `q.can_fly`            | `minecraft:can_fly`            | 将实体标记为可飞行，寻路器不再限制其只能走下方需要固体方块的路徑。 |
| 布尔       | `q.can_power_jump`     | `minecraft:can_power_jump`     | 允许实体像原版马一样进行蓄力跳跃。                                 |
| 布尔       | `q.is_ignited`         | `minecraft:is_ignited`         |                                                                    |
| 布尔       | `q.out_of_control`     | `minecraft:out_of_control`     |                                                                    |
