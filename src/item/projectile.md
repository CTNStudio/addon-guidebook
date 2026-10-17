**弹射物（Projectile）** 是一类沿轨迹飞行并可以命中各类物体的实体，可以造成伤害等多种效果[^1]。 

而投掷物则是弹射物的一种，所有的投掷物都有完全相同的运动方式，区别只有它们破裂后会造成的效果[^1]，游戏中的雪球、鸡蛋、末影珍珠等内容都属于投掷物。

在本节中，我们将会讨论如何制作自定义的投掷物。

## 物品部分

我们需要知道，投掷物是由实体和物品两部分组成的，所以我们先创建一个没有任何功能的物品，并确保其格式版本在 1.21.0 以上，具体过程请参照第一章第三节。

完成后，物品行为部分的代码应该像下面这样：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_projectile",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:max_stack_size": 16,
      "minecraft:icon": "example.first_projectile"
    }
  }
}
```

### 飞起来！

接下来，我们将会为这个物品添加投掷物相关的组件：

```json
...
"minecraft:throwable": {
  "do_swing_animation": true,
  "launch_power_scale": 2
},
"minecraft:projectile": {
   "projectile_entity": "example:thrown_first_projectile"
}
...
```

`minecraft:throwable`组件让物品可以被投掷出去，在其中：

- `do_swing_animation`设置了物品抛出时是否播放摆动动画；
- `launch_power_scale`设置了投掷时力量增加的比例。

除此之外，我们还可以通过`max_draw_duration`和`min_draw_duration`设置物品蓄力的最长或最短时间。

而`minecraft:projectile`中的`projectile_entity`设置了丢出后生成的实体。

## 制作实体

首先创建一个实体的行为文件，写入如下内容：

```json
{

    "format_version": "1.21.0",
    "minecraft:entity": {
        "description": {
            "identifier": "example:thrown_first_projectile",
            "is_spawnable": false,
            "is_summonable": true,
            "is_experimental": false,
            "runtime_identifier": "minecraft:snowball"
        },
        "components": {
            "minecraft:collision_box": {
                "width": 0.25,
                "height": 0.25
            },
            "minecraft:projectile": {
                "on_hit": {
                    "impact_damage": {
                        "damage": 3,
                        "knockback": true
                    },
                    "remove_on_hit": {},
                    "particle_on_hit": {
                        "particle_type": "snowballpoof",
                        "num_particles": 6,
                        "on_entity_hit": true,
                        "on_other_hit": true
                    }
                },
                "anchor": 1,
                "power": 1.5,
                "gravity": 0.03,
                "angle_offset": 0,
                "offset": [
                    0,
                    -0.1,
                    0
                ]
            },
            "minecraft:physics": {},
            "minecraft:pushable": {
                "is_pushable": true,
                "is_pushable_by_piston": true
            }
        }

    }

}
```

其中的`minecraft:projectile`组件是这个实体的核心，其比较复杂：

- `on_hit`属性设置了当投掷物击中实体时的事件，例如这里设置击中实体时造成 3 点伤害，并生成雪球粒子；
- `power`属性设置了投掷物的速度；
- `gravity`属性设置了投掷物被施加的重力，值越大，投掷物下落越快。

关于该组件的详细信息，可以在 [Microsoft Learn](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/entityreference/examples/entitycomponents/minecraftcomponent_projectile?view=minecraft-bedrock-stable) 中找到，这里限于篇幅不再赘述。


## 添加客户端资源

接下来，我们要为自定义投掷物添加客户端资源。

物品部分步骤与第一章第三节所述步骤完全相同，故这里不再赘述；实体部分与第一章第五节所述步骤也大致相同，下面是一个举例：

```json
{
    "format_version": "1.20.0",
    "minecraft:client_entity": {
        "description": {
            "identifier": "example:thrown_first_projectile",
            "materials": {
                "default": "snowball"
            },
            "textures": {
                "default": "textures/items/first_projectile"
            },
            "geometry": {
                "default": "geometry.item_sprite"
            },
            "render_controllers": [
                "controller.render.item_sprite"
            ],
            "animations": {
                "flying": "animation.actor.billboard"
            },
            "scripts": {
                "animate": [
                    "flying"
                ]
            }
        }
    }
}
```

添加完客户端资源后，一个完整的投掷物就制作好了。


[^1]: [弹射物 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E5%BC%B9%E5%B0%84%E7%89%A9?variant=zh-cn#%E6%8A%95%E6%8E%B7%E7%89%A9)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
