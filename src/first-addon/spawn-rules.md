
在前一节中，我们添加了一个自定义实体，那么如何让它在世界中生成呢？这就是**生成规则（Spawn Rule）** 的作用了。

在我们的行为包下新建`spawn_rules`文件夹，这个文件夹将会存放我们的生成规则。

在`spawn_rules`中创建一个 JSON 文件，写入以下内容：

```json
{
  "format_version": "1.8.0",
  "minecraft:spawn_rules": {
    "description": {
      "identifier": "example:first_entity",
      "population_control": "monster"
    },
    "conditions": [
      {
        "minecraft:spawns_on_surface": {},
        "minecraft:brightness_filter": {
          "min": 0,
          "max": 7,
          "adjust_for_weather": true
        },
        "minecraft:difficulty_filter": {
          "min": "easy",
          "max": "hard"
        },
        "minecraft:weight": {
          "default": 80
        },
        "minecraft:herd": {
          "min_size": 1,
          "max_size": 3
        },
        "minecraft:biome_filter": {
          "test": "has_biome_tag",
          "operator": "==",
          "value": "plains"
        }
      }
    ]
  }
}
```

`format_version`是格式版本，不必多说。

在`description`中：

- `identifier`设置了生成规则对应的实体的命名空间ID；
- `population_control`设置了实体所述的种群，一旦该种群内实体数量达到上限，该种群的实体将不再生成。

在`conditions`中：

- `minecraft:spawns_on_surface`表示该实体只能在地面生成；
- `minecraft:brightness_filter`设置了生成时环境的光照强度，`min`为最低光照强度，`max`为最高光照强度，`adjust_for_weather`表示是否受天气影响；
- `minecraft:difficulty_filter`设置了生成时世界的难度，`min`为最低难度，`max`为最高难度；
- `minecraft:weight`设置了生成权重，权重越高，生成的概率越大；
- `minecraft:herd`设置了一次生成的实体数量，`min_size`为最低数量，`max_size`为最高数量；
- `minecraft:biome_filter`限制了实体生成的生物群系，这里设置为只能在平原生成。

## 小结
现在进入游戏，我们就可以看到自定义实体在平原中生成了！

接下来，我们将会了解如何添加自定义战利品表和配方。