# 事件与组件组

想想 Minecraft 中的狼：在未驯服时，它可能会攻击玩家；但当被驯服后，它又不会攻击玩家，并会跟随玩家。

我们知道，这些行为是通过不同的组件来实现的，可随着狼的驯服，原本「攻击玩家」的行为就理应被废弃，这个效果该如何实现呢？

通过事件与组件组，我们可以实现相当复杂的实体行为。

## 组件组

**组件组**可以将组件分组，就像一个储存着若干组件的大「文件夹」。

例如：

```json
{
  "minecraft:entity": {
    "component_groups": {
      // 组件组 1 的名称
      "example:variant_1": {
        "minecraft:variant": {
          "value": 0
        },
        "minecraft:type_family": {
          "family": ["maodie"]
        }
      },
      // 组件组 2 的名称
      "example:variant_1": {
        "minecraft:variant": {
          "value": 1
        },
        "minecraft:type_family": {
          "family": ["jueshihaomao"]
        }
      }
    }
  }
}
```

可以看到，组件组存储在`minecraft:entity.component_groups`下，这些组件默认不会生效，只有通过下文所述的**事件**才能被添加到实体上，从而真正生效。

## 事件响应​

**事件**允许我们对实体执行操作，例如添加或移除组件组、执行命令、设置实体属性，通过事件，我们可以改变实体的行为。

来看下面的例子：

```json
{
  "minecraft:entity": {
    "events": {
      "countdown_to_perish_event": {
        "remove": {
          "component_groups": [
            "collect_nectar",
            "track_attacker",
            "take_nearest_target",
            "look_for_food",
            "angry_bee",
            "hive_full",
            "find_hive",
            "escape_fire"
          ]
        },
        "add": {
          "component_groups": ["countdown_to_perish"]
        }
      }
    }
  }
}
```

我们发现，实体的事件储存在`minecraft:entity.events`对象下，并可以执行`add`或`remove`等操作。

下面我们来详细了解这一系列操作。

### 添加、移除组件组

事件最重要的操作是**添加、移除组件组**。

在下面的范例中，`example:ranged_attack`事件为实体添加了`ranged_attack`组件组，移除了`melee_attack`组件组：

```json
"example:ranged_attack": {
    "add": {
        "component_groups": [
            "ranged_attack"
        ]
    },
    "remove":{
        "component_groups": [
            "melee_attack"
        ]
    }
}
```

### 执行命令

通过事件，我们还可以**执行命令**。

```json
{
  "events": {
    "example:kill": {
      "queue_command": {
        "target": "self",
        "command": "kill @e"
      }
    },
    "example:hellp": {
      "queue_command": {
        "target": "self",
        "command": ["say Here's Johnny!", "kill @e[family=!illagers]"]
      }
    }
  }
}
```

可以发现，我们可以一次执行一条或多条命令。

### 触发器

我们还可以在事件中使用**触发器（Trigger）** 触发其他事件：

```json
{
  "example:event_1": {
    "trigger": {
      "filters": {
        "test": "is_family",
        "subject": "self",
        "value": "illager"
      },
      "event": "example:event_2",
      "target": "self"
    }
  }
}
```

在其中：

- `filters`是执行此触发器的条件
- `event`代表此触发器将会触发的事件
- `targer`代表触发事件的目标

### 随机​

我们还可以一次设置多组操作，通过权重指定每一组操作触发的概率：

```json
{
  "minecraft:entity_spawned": {
    "sequence": [
      {
        "randomize": [
          {
            "add": {
              "component_groups": ["minecraft:can_break_doors"]
            },
            "weight": 10
          },
          {
            "weight": 90
          }
        ]
      }
    ]
  }
}
```

`randomize`接受一个数组作为参数，数组中的每个元素都应该含有对应的操作和选中该元素的权重。

在上面的范例中，有 10% 的概率为实体添加`minecraft:can_break_doors`组件，有 90% 的概率什么也不发生。

### 序列

通过**序列**，我们可以按照顺序依次触发操作，并通过序列所带的**过滤器**来让操作在特定条件下才能被执行。

例如：

```json
{
  "minecraft:convert_to_drowned": {
    "sequence": [
      {
        "filters": {
          "test": "has_component",
          "operator": "!=",
          "value": "minecraft:is_baby"
        },
        "add": {
          "component_groups": ["minecraft:convert_to_drowned"]
        },
        "remove": {
          "component_groups": ["minecraft:start_drowned_transformation"]
        }
      },
      {
        "filters": {
          "test": "has_component",
          "value": "minecraft:is_baby"
        },
        "add": {
          "component_groups": ["minecraft:convert_to_baby_drowned"]
        },
        "remove": {
          "component_groups": ["minecraft:start_drowned_transformation"]
        }
      }
    ]
  }
}
```

在上面的范例中，只有实体满足`minecraft:is_baby`条件时，第二组操作才会被触发，而此时第一组操作由于不满足条件，所以不会被触发。

### 设置属性

我们还可以通过事件**设置实体属性**，它支持将 Molang 表达式作为属性值传入：

```json
{
  "example:set_property": {
    "set_property": {
      "example:property_1": 114,
      "example:property_2": "q.property('example:property_2') + 2"
    }
  }
}
```

## 触发事件

游戏大体提供了下面几种方式来触发事件：

- 组件
- 动画
- 动画控制器
- 另一个事件
- 命令与脚本

### 通过组件触发事件

一些组件带有`event`或者类似的参数，通过这种参数，我们可以触发事件，例如：

```json
{
  "minecraft:entity_sensor": {
    "relative_range": false,
    "subsensors": [
      {
        "event": "minecraft:on_not_riding_player", // 将要被触发的事件
        "event_filters": {
          "all_of": [
            {
              "test": "is_riding",
              "operator": "equals",
              "subject": "self",
              "value": false
            },
            {
              "test": "has_component",
              "operator": "not",
              "subject": "self",
              "value": "minecraft:behavior.look_at_player"
            }
          ]
        },
        "range": [2, 2]
      }
    ]
  }
}
```

在上面的范例中，在满足给定的两个条件（实体没有被骑乘、不能看向玩家）时，`minecraft:on_not_riding_player`事件将会被触发。

除此之外，一些组件中带有生成实体类型的参数，例如：

```json
{
  "minecraft:transformation": {
    "into": "minecraft:drowned<minecraft:as_adult>",
    "transformation_sound": "convert_to_drowned",
    "drop_equipment": true,
    "delay": {
      "value": 15
    }
  }
}
```

在这些参数中，我们可以按照`实体 ID<事件 ID>`的形式让新实体生成自动执行某一事件。

### 动画与动画控制器

在行为包动画中，我们可以使用`timeline`参数来控制在动画播放的什么时候触发事件：

```json
{
  "format_version": "1.10.0",
  "animations": {
    "animation.example_entity.spawn": {
      "loop": false,
      "animation_length": 0.5,
      "timeline": {
        "0.2": ["@s example:event_1"]
      }
    }
  }
}
```

类似地，行为包动画控制器也可以用于触发事件：

```json
{
  "controller.animation.example_entity.movement": {
    "initial_state": "walk",
    "states": {
      "walk": {
        "transitions": [
          {
            "run": "q.is_sheared"
          }
        ]
      },
      "run": {
        "on_entry": ["@s example:event_2"],
        "transitions": [
          {
            "walk": "!q.is_sheared"
          }
        ]
      }
    }
  }
}
```

### 在事件中触发事件

当然，我们也可以使用事件中的触发器来触发其他事件：

```json
"minecraft:entity_spawned": {
    "trigger": "entity_spwan"
}
```

> `minecraft:entity_spawned`是一个内置事件，我们将会在后文讲到

### 通过命令与脚本触发事件

在`summon`命令中，有`[spawnEvent: string]`这一可选参数，我们可以通过这一参数指定实体生成时自动触发的事件。

除此之外，我们可以通过`event`命令来在任何时候为实体触发事件，它的语法如下所示：

```bash
event entity <target: target> <eventName: string>
```

在其中：

- `<target: target>`指定了实体事件的作用对象
- `<eventName: string>`指定了命令触发的实体事件

在脚本中，我们也可以通过[`triggerEvent`方法](https://sapi.dogelake.cn/server/classes/Entity.html#triggerevent)为实体触发事件。

## 内置事件

原版游戏中内置了一些事件，我们只要将把自己的事件名称改为这些**内置事件（Built-in Event）** 的名称，游戏便会在恰当的时刻自动触发这些事件：

- `minecraft:entity_spawned`：当实体生成时自动触发
- `minecraft:entity_born`：当实体通过繁殖生成时自动触发
- `minecraft:entity_transformed`：当其他实体转换为此实体时触发
- `minecraft:on_prime`：当实体即将爆炸时触发

例如：

```json
{
  "minecraft:entity_spawned": {
    "sequence": [
      {
        "randomize": [
          {
            "trigger": "minecraft:as_adult",
            "weight": 9500
          },
          {
            "trigger": "minecraft:as_baby",
            "weight": 425
          },
          {
            "trigger": "minecraft:as_baby_jockey",
            "weight": 75
          }
        ]
      },
      {
        "randomize": [
          {
            "add": {
              "component_groups": ["minecraft:can_break_doors"]
            },
            "weight": 10
          },
          {
            "weight": 90
          }
        ]
      }
    ]
  }
}
```

## 小结

在本节中，我们学习了：

- 组件组的用法；
- 事件的创建与触发；
- 内置事件。

这一节的内容相当之多，对吧？
