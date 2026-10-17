附加包也允许我们通过数据驱动的方式来创建自定义实体，实体也由两部分组成：

- 行为文件；
- 资源文件（客户端文件、动画、动画控制器、渲染控制器……）。

与物品、方块不同的是，数据驱动的自定义实体接口更加丰富，而且原版的实体已经实现了全面数据驱动，我们可以对其进行覆盖与修改。

在本节中，我们会学习如何制作一个简单的自定义实体，该实体将类似于原版的灾厄村民，会攻击周围的玩家。

## 行为文件
在我们的行为包下新建`entities`文件夹，然后创建一个`<实体ID>.json`文件。

> [!TIP]
> 文件不一定必须按照`<实体ID>.json`的格式进行命名，这里这么做是为了方便后续查找；
> 
> 我们也可以把这个文件创建在`entities`的子文件夹下。

在这个文件中写入以下内容：

```json
{
  "format_version": "1.20.60",
  "minecraft:entity": {
    "description": {
      "identifier": "example:first_entity",
      "is_spawnable": true,
      "is_summonable": true,
      "is_experimental": false
    },
    "components": {}
  }
}
```

`format_version`是文件使用的格式版本，这里是1.20.60；

在`description`中：

- `identifier`设置了实体的命名空间ID，注意这里的命名空间是`example`，在实际的项目中不建议使用；
- `is_summonable`设置了实体是否可以使用`/summon`命令召唤；
- `is_spawnable`设置了实体是否可以使用刷怪蛋或生成规则在世界中生成；
- `is_experimental`设置了实体是否为实验性实体。

在`components`中，我们可以为实体添加**组件**，每一个组件都可以为实体添加相应的功能，接下来我们就为其添加一些基础组件。

### 添加组件
#### 基本属性
以下组件为实体的基本组件，我们的实体一般都需要下列组件：

```json
"minecraft:type_family": {
  "family": ["monster", "ruby", "mob"]
},
"minecraft:health": {
  "value": 15,
  "max": 15
},
"minecraft:movement": {
  "value": 0.35
},
"minecraft:collision_box": {
  "width": 0.6,
  "height": 1.9
},
"minecraft:loot": {
  "table": "loot_tables/entities/first_entity.json"
}
```

其中：

- `health`、`movement`、`collision_box`组件分别设置了实体的生命值、移动速度和碰撞箱；
- `type_family`组件为实体添加了[族](https://zh.minecraft.example/w/%E6%97%8F)；
- `loot`组件设置了实体死亡后的战利品。

除此以外，实体还需要**寻路和移动组件**来使其能够在世界中正常移动：

```json
"minecraft:physics": {},
"minecraft:jump.static": {},
"minecraft:movement.basic": {},
"minecraft:navigation.walk": {
  "can_path_over_water": true
}
```

其中：

- `physics`组件设置实体的物理属性，包括其是否受重力影响或与其他物体发生碰撞，这里使用默认值；
- `jump.static`组件使得实体可以进行跳跃以便于通行，这里使用默认值；
- `movement.basic`组件为实体添加最基本的移动能力；
- `navigation.walk`是一个寻路组件，它让实体通过四处走动和上下跳跃方块进行移动。

#### 行为意向
以`minecraft:behavior`开头的组件决定了实体在何时做什么，所有行为都包含`priority`属性，用于判断同时可以执行多个行为时，具体执行哪个行为，越小的值优先级越高。

以下是我们添加的第一部分行为组件，它们让实体可以随机移动并张望：

```json
"minecraft:behavior.random_stroll": { // 令实体向某一随机方向行走
  "priority": 7,
  "speed_multiplier": 1
},
"minecraft:behavior.look_at_player": { // 令实体向玩家方向看去
  "priority": 8,
  "look_distance": 8
},
"minecraft:behavior.random_look_around": { // 令实体四处张望
  "priority": 8
}
```

第二部分组件将会使我们的实体可以攻击玩家和对其造成伤害的实体：

```json
"minecraft:behavior.hurt_by_target": {  // 当实体被其他实体击中时，对其进行反击
  "priority": 1,
  "entity_types": {
      "filters": {
        "test": "is_family",
        "subject": "other",
        "operator": "!=",
        "value": "ruby"
      },
      "max_dist": 64
  }
},
"minecraft:behavior.nearest_attackable_target": { // 寻找最近的可攻击生物
  "priority": 1,
  "entity_types": [
      {
        "filters": {
          "test": "is_family",
          "subject": "other",
          "value": "player" // 令实体与玩家为敌
        }
      }
    ],
  "must_see": true
},
"minecraft:behavior.melee_attack": { // 允许实体进行近战攻击
    "priority": 4,
    "speed_multiplier": 1,
    "track_target": true
},
"minecraft:attack": {  // 该组件不是行为组件，可以设置实体造成的伤害值
  "damage": 2
}
```

### 大功告成？

此时，完整的实体文件如下：
```json
{
  "format_version": "1.20.60",
  "minecraft:entity": {
    "description": {
      "identifier": "example:first_entity",
      "is_spawnable": true,
      "is_summonable": true,
      "is_experimental": false
    },
    "components": {
      "minecraft:type_family": {
        "family": ["monster", "ruby", "mob"]
      },
      "minecraft:health": {
        "value": 15,
        "max": 15
      },
      "minecraft:movement": {
        "value": 0.35
      },
      "minecraft:collision_box": {
        "width": 0.6,
        "height": 1.9
      },
      "minecraft:loot": {
        "table": "loot_tables/entities/first_entity.json"
      },
      "minecraft:physics": {},
      "minecraft:jump.static": {},
      "minecraft:movement.basic": {},
      "minecraft:navigation.walk": {
        "can_path_over_water": true
      },
      "minecraft:behavior.random_stroll": {
        // 令实体向某一随机方向行走
        "priority": 7,
        "speed_multiplier": 1
      },
      "minecraft:behavior.look_at_player": {
        // 令实体向玩家方向看去
        "priority": 8,
        "look_distance": 8
      },
      "minecraft:behavior.random_look_around": {
        // 令实体四处张望
        "priority": 8
      },
      "minecraft:behavior.hurt_by_target": {
        // 当实体被其他实体击中时，对其进行反击
        "priority": 1,
        "entity_types": {
          "filters": {
            "test": "is_family",
            "subject": "other",
            "operator": "!=",
            "value": "ruby"
          },
          "max_dist": 64
        }
      },
      "minecraft:behavior.nearest_attackable_target": {
        // 寻找最近的可攻击生物
        "priority": 1,
        "entity_types": [
          {
            "filters": {
              "test": "is_family",
              "subject": "other",
              "value": "player" // 令实体与玩家为敌
            }
          }
        ],
        "must_see": true
      },
      "minecraft:behavior.melee_attack": {
        // 允许实体进行近战攻击
        "priority": 4,
        "speed_multiplier": 1,
        "track_target": true
      },
      "minecraft:attack": {
        // 该组件不是行为组件，可以设置实体造成的伤害值
        "damage": 2
      }
    }
  }
}
```

但如果我们使用`/summon`命令尝试召唤该实体，就会发现地面上只出现了一个阴影，这是因为我们还没有定义其资源文件。

## 资源文件

实体的资源文件定义了实体的模型、动画、渲染、客户端、本地化以及音效等，其与方块与物品的资源文件类似，但**更加复杂**。

### 模型与纹理

> [!TIP]
> 因为我们的目标是制作一个类似于灾厄村民的实体，所以会直接引用其几何体文件，因此这里仅作粗略的介绍

实体的**模型**描述了实体的形状，又称为 **「几何体」** ，其作为JSON文件存储在资源包根目录的`models`文件夹下，而实体的几何体文件则存储在`models/entity`文件夹下。

几何体的大致格式如下，得益于 BlockBench 等工具，我们无需专门学习其语法：

```json
{
  "format_version": "1.12.0",
  "minecraft:geometry": [
    {
      "description": {
        "identifier": "geometry.first_entity",
        "texture_width": 64,
        "texture_height": 64
      },
      "bones": [...]
}
```

我们唯独需要注意的是`identifier`，这是模型的 ID，在后续的客户端实体文件中会用到。

我们的实体现在有了模型，但还需要纹理。实体的纹理应该放在资源包的`textures/entity`文件夹下，一般在制作模型时在 Blockbench 中创建。

### 动画

> [!TIP]
> 因为我们的目标是制作一个类似于灾厄村民的实体，所以这里直接引用其动画文件，因此仅作粗略的介绍

动画可以让我们的实体具有生命力，有效地防止了实体出现「战术平移」的状态，其作为 JSON 文件存储在资源包根目录的`animations`文件夹下。

动画文件的大致格式如下，得益于 BlockBench 等工具，我们也无需专门学习其语法：

```json
{
  "format_version": "1.8.0",
  "animations": {
    "animation.first_entity.idle": {...}, // 待机动画
    "animation.first_entity.attack": {...}, // 攻击动画
    "animation.first_entity.move": {...} // 移动动画
  }
}
```
只有动画还不够，游戏这时并不知道该何时播放这些动画，这就是**动画控制器**的作用了。

动画控制器作为JSON文件存储在资源包根目录的`animation_controllers`文件夹下，一般而言，动画控制器包含与动画相对应的**状态（states）**，以及状态之间的**转换（transitions）**，如下面的范例：

```json
{
  "format_version": "1.10.0",
  "animation_controllers": {
    "controller.animation.first_entity.attack": {
      "initial_state": "default",
      "states": {
        "attacking": {
          "animations": ["attack"],
          "transitions": [
            {
              "default": "variable.attack_time < 0.0"
            }
          ]
        },
        "default": {
          "animations": ["idle"],
          "transitions": [
            {
              "attacking": "variable.attack_time >= 0.0"
            }
          ]
        }
      }
    }
  }
}
```

这个控制器定义了两个状态：`attacking`和`default`，而状态中的`animations`数组决定了在该状态下始终播放的动画，`initial_state`则决定了动画的初始状态。

> [!IMPORTANT]
> 此处使用了动画的短名称，其需要在实体客户端文件中声明，否则动画将不会播放

其中`default`状态会播放待机动画；而`attacking`状态会播放攻击动画，`transitions`下的条件决定了何时从`default`状态转换到`attacking`状态：

```json
"transitions": [
  {
    "attacking": "variable.attack_time >= 0.0"
  }
]
```

这里使用了Molang进行查询，当攻击时间大于0（即处于攻击中）时，就会从`attacking`状态转换到`default`状态，反之亦然。

同样，我们也可以使用动画控制器来控制实体移动时的动画：

```json
{
  "controller.animation.first_entity.move": {
    "initial_state": "default",
    "states": {
      "default": {
        "animations": ["idle"],
        "transitions": [
          {
            "move": "query.modified_move_speed > 0" // 当实体移动速度大于0时，从default状态转换到move状态
          }
        ]
      },
      "move": {
        "animations": ["move"],
        "transitions": [
          {
            "default": "query.all_animations_finished" // 当所有动画播放完毕时，从move状态转换到default状态
          }
        ]
      }
    }
  }
}
```
### 客户端
现在我们有了模型、纹理、动画和动画控制器，但游戏并不知道这些资源要给我们的实体应用，这就是**客户端实体文件**的作用。

客户端实体文件作为JSON文件存储在资源包根目录的`entity`文件夹下，基本格式如下：

```json
{
  "format_version": "1.10.0",
  "minecraft:client_entity": {
    "description": {
      "identifier": "example:first_entity", // 实体的命名空间ID，注意要与行为包的实体命名空间ID一致
      "materials": {
        "default": "pillager"
      },
      "textures": {
        "default": "textures/entity/first_entity" // 纹理路径
      },
      "geometry": {
        "default": "geometry.pillager" // 模型ID，当然我们也可以使用自己的模型！
      },
      "render_controllers": ["controller.render.first_entity"],
      "spawn_egg": {},
      "animations": {
        "walk_controller": "controller.animation.first_entity.walk",
        "attack_controller": "controller.animation.first_entity.attack",
        "attack": "animation.first_entity.attack",
        "idle": "animation.first_entity.idle",
        "move": "animation.first_entity.move"
      },
      "scripts": {
        "animate": ["walk_controller", "attack_controller"]
      }
    }
  }
}

```

#### 刷怪蛋

`spawn_egg`字段指定了实体刷怪蛋的纹理，有两种可用的写法：

```json
"spawn_egg": {
  "overlay_color": "...",
  "base_color": "..."
}
```

这种方法不用单独的物品纹理，而是通过指定的十六进制代码来为其着色，而另一种方法则是直接指定纹理：

```json
"spawn_egg": {
  "texture": "egg_first_entity",// 这里填的是纹理名，请按照物品教程中的所述方法创建纹理名映射
  "texture_index": 0
}
```

#### 动画与脚本
```json
"animations": {
  "walk_controller": "controller.animation.first_entity.walk",
  "attack_controller": "controller.animation.first_entity.attack",
  "attack": "animation.first_entity.attack",
  "idle": "animation.first_entity.idle",
  "move": "animation.first_entity.move"
}
```

`animations`字段定义了动画控制器和动画的短名称，以便游戏使用，正如前文所述，**想要在动画控制器中引用动画，都必须填写在这里定义的短名称**。

```json
"scripts": {
  "animate": ["walk_controller", "attack_controller"]
}
```

`scripts`字段定义了实体在特定时间执行的动作，而`animate`属性则设置了每游戏刻都要运行的动画或动画控制器，这里我们定义了两个控制器，分别是`walk_controller`和`attack_controller`，分别对应了移动和攻击的动画控制器。

### 渲染
我们需要告诉游戏渲染实体的哪些资源，这将由**渲染控制器**控制。

渲染控制器作为 JSON 文件存储在资源包根目录的`render_controllers`文件夹下，基本格式如下：

```json
{
  "format_version": "1.10.0",
  "render_controllers": {
    "controller.render.first_entity": { // 渲染控制器ID，应该与客户端实体文件中的render_controllers字段一致
      "geometry": "geometry.default",
      "materials": [
        {
          "*": "material.default"
        }
      ],
      "textures": ["texture.default"]
    }
  }
}
```
在这里的`geometry.default`、`material.default`和`texture.default`分别代表渲染标识符为`default`的模型、材质和纹理，而`material.default`前的`*`则表示所有骨骼都使用`default`材质。

至此，我们的实体应该在游戏中可见了，但还缺少音效以及名称。

### 本地化

在语言文件中添加如下内容：

```
entity.example:first_entity.name=第一个实体
item.spawn_egg.entity.example:first_entity.name=第一个实体的刷怪蛋
```

### 音效

资源包根目录下的`sounds.json`允许我们向自定义实体添加音效：

```json
{
  "example:first_entity": {
        "volume": 1.0,
        "pitch": [0.8, 1.2],
        "events": {
          "ambient": "mob.pillager.idle",
          "hurt": "mob.pillager.hurt",
          "death": "mob.pillager.death"
        }
  }
}
```
其中：
- `volume`设置了声音的音量；
- `pitch`设置了声音的音高；
- `events`设置了具体的声音事件，我们在这里定义了`ambient`（随机播放的环境音效）、`hurt`（受伤）以及`death`（死亡）的声音事件，其全部使用原版灾厄村民的音效。

## 小结

芜湖！如果我们啃下来了这一节内容，那么接下来的内容将会非常简单易懂。

接下来我们将会了解添加自定义战利品表和配方与生成规则。