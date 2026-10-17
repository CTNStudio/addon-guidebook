# 自定义矿石

**矿石（Ores）** 是游戏中的重要元素，在本节中，我们将会创建一个自定义矿石方块，并使其可以在世界中生成。

## 战利品表

首先，我们需要准备一个战利品表，让玩家用正确的方式破坏矿石时掉落战利品：

```json
{
  "pools": [
    {
      "rolls": 1,
      "conditions": [
        {
          "condition": "match_tool",
          "minecraft:match_tool_filter_any": [
            "minecraft:iron_tier",
            "minecraft:golden_tier",
            "minecraft:diamond_tier"
          ],
          "minecraft:match_tool_filter_all": ["minecraft:is_pickaxe"]
        }
      ],
      "entries": [
        {
          "type": "item",
          "name": "example:first_mateiral",
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
      ]
    }
  ]
}
```

在这里，我们使用了`match_tool`条件，来确保只有使用铁、金或钻石级别的镐子时，战利品才会掉落。

## 方块行为

```admonish warning
别忘了为方块设置客户端资源！
```

接下来，我们需要设置自定义矿石方块的行为，下面是一个示例：

```json
{
  "format_version": "1.21.70",
  "minecraft:block": {
    "description": {
      "identifier": "example:example_ore",
      "menu_category": {
        "category": "nature",
        "group": "minecraft:itemGroup.name.ore"
      }
    },
    "components": {
      "minecraft:loot": "loot_tables/blocks/example_ore.json",
      "minecraft:destructible_by_mining": {
        "seconds_to_destroy": 15,
        "item_specific_speeds": [
          {
            "item": {
              "tags": "q.all_tags('minecraft:is_pickaxe', 'minecraft:iron_tier')"
            },
            "destroy_speed": 0.75
          },
          {
            "item": {
              "tags": "q.all_tags('minecraft:is_pickaxe', 'minecraft:diamond_tier')"
            },
            "destroy_speed": 0.6
          },
          {
            "item": {
              "tags": "q.all_tags('minecraft:is_pickaxe', 'minecraft:netherite_tier')"
            },
            "destroy_speed": 0.5
          },
          {
            "item": {
              "tags": "q.all_tags('minecraft:is_pickaxe', 'minecraft:golden_tier')"
            },
            "destroy_speed": 0.4
          }
        ]
      },
      "minecraft:destructible_by_explosion": {
        "explosion_resistance": 2.5
      },
      "minecraft:map_color": "#999999",
      "tag:minecraft:iron_tier_destructible": {},
      "tag:minecraft:is_pickaxe_item_destructible": {}
    }
  }
}
```
在上面的范例中：

- `minecraft:loot`组件设置了挖掘方块后触发的战利品表；
- `minecraft:destructible_by_mining`组件设置了挖掘速度，默认为 15 秒；
  - 通过`item_specific_speeds`属性，我们为不同等级的镐子设置了不同的挖掘速度；
  - 注意这里所有的挖掘速度均是以秒为单位；
- `minecraft:destructible_by_explosion`组件设置了方块的爆炸抗性；
- `minecraft:iron_tier_destructible`和`minecraft:is_pickaxe_item_destructible`标签标记方块可以被铁及以上等级的镐子破坏。


## 经验掉落*

要在线程块被破坏时生成经验球，可以使用自定义组件。在这里，我们使用[onPlayerDestroy](/blocks/block-events#player-destroy)事件钩子。如果你不希望你的方块生成经验值，可以忽略这一步。

与战利品表类似，我们检查玩家手中的物品，然后在方块的位置生成随机数量的经验球。


To spawn experience orbs when your ore block is destroyed, custom components can be used. Here, we use the [onPlayerDestroy](/blocks/block-events#player-destroy) event hook. If you don't want your block to spawn XP, this step can be ignored.

Similarly to the loot table, we check the item in the player's hand and then spawn a random number of XP orbs at the block's location.

<CodeHeader>BP/scripts/silver_ore.js</CodeHeader>

```js
import { world, EquipmentSlot } from "@minecraft/server";

/**
 * @param {number} min The minimum integer
 * @param {number} max The maximum integer
 * @returns {number} A random integer between the `min` and `max` parameters (inclusive)
 * */
const randomInt = (min, max) =>
  Math.floor(Math.random() * (max - min + 1)) + min;

// Register a custom component before the world is loaded
world.beforeEvents.worldInitialize.subscribe(({ blockComponentRegistry }) => {
  blockComponentRegistry.registerCustomComponent("wiki:silver_ore_xp_reward", {
    onPlayerDestroy({ block, dimension, player }) {
      // Check the tool in the player's hand
      const equippable = player?.getComponent("minecraft:equippable");
      if (!equippable) return; // Exit if the player or its equipment are undefined

      const itemStack = equippable.getEquipment(EquipmentSlot.Mainhand);
      if (itemStack?.typeId !== "minecraft:iron_pickaxe") return; // Exit if the player isn't holding an iron pickaxe

      // Specify enchantments
      const enchantable = itemStack.getComponent("minecraft:enchantable");
      const silkTouch = enchantable?.getEnchantment("silk_touch");
      if (silkTouch) return; // Exit if the iron pickaxe has the Silk Touch enchantment

      // Spawn the XP orbs
      const xpAmount = randomInt(0, 3); // Number of XP orbs to spawn

      for (let i = 0; i < xpAmount; i++) {
        dimension.spawnEntity("minecraft:xp_orb", block.location);
      }
    },
  });
});
```

## Block JSON

The following block behavior can be used as a template. Don't forget to set the block's texture using `terrain_texture.json`.

Here you need to do two things:

- Point to the new loot table with the `minecraft:loot` component.
- Add our experience reward custom component to the `minecraft:custom_components` array.

<CodeHeader>BP/blocks/silver_ore.json</CodeHeader>

`ore_feature`'s are basic but important features! They can form clusters of blocks by replacing blocks where they are generated. This tutorial will show you how to make mineral ores that naturally generate.

The use of features and feature rules requires Creation of Custom Biomes to be enabled in your world settings. If your block doesn't generate, make sure it's enabled!

:::tip
For this tutorial, I'll be using 2 custom blocks, Titanite Ore and Deepslate Titanite Ore. For how to make custom blocks, visit the [Blocks Intro](/blocks/blocks-intro) page.
:::

## The Feature File

<CodeHeader>BP/features/titanite_ore_feature.json</CodeHeader>

```json
{
  "format_version": "1.17.0",
  "minecraft:ore_feature": {
    "description": {
      "identifier": "wiki:titanite_ore_feature"
    },
    "count": 8, // Placement attempts
    "replace_rules": [
      {
        // Replace all stone variants (andesite, granite, and diorite) with titanite ore
        "places_block": "wiki:titanite_ore",
        "may_replace": ["minecraft:stone"]
      },
      {
        // Replace deepslate with deepslate titanite ore
        "places_block": "wiki:deepslate_titanite_ore",
        "may_replace": ["minecraft:deepslate"]
      }
    ]
  }
}
```

## The Feature Rule

<CodeHeader>BP/feature_rules/overworld_underground_titanite_ore_feature.json</CodeHeader>

```json
{
  "format_version": "1.13.0",
  "minecraft:feature_rules": {
    "description": {
      "identifier": "wiki:overworld_underground_titanite_ore_feature",
      "places_feature": "wiki:titanite_ore_feature" // Identifier from the feature file
    },
    "conditions": {
      "placement_pass": "underground_pass",
      "minecraft:biome_filter": [
        // Scatter the ore throughout the Overworld
        {
          "any_of": [
            {
              "test": "has_biome_tag",
              "operator": "==",
              "value": "overworld"
            },
            {
              "test": "has_biome_tag",
              "operator": "==",
              "value": "overworld_generation"
            }
          ]
        }
      ]
    },
    "distribution": {
      "iterations": 10, // Placement attempts of the cluster, not the ore blocks
      "coordinate_eval_order": "zyx",
      "x": {
        "distribution": "uniform",
        "extent": [0, 16]
      },
      "y": {
        "distribution": "uniform", // You can use "triangle" to make ores more common in the middle of the extent
        "extent": [
          0, // Minimum y level for the ore to generate
          62 // Maximum y level for the ore to generate
        ]
      },
      "z": {
        "distribution": "uniform",
        "extent": [0, 16]
      }
    }
  }
}
```

## 小结

```json
{
  "format_version": "1.20.30",
  "minecraft:ore_feature": {
    "description": {
      "identifier": "example:first_ore"
    },
    "count": 4,
    "replace_rules": [
      {
        "places_block": "example:first_ore",
        "may_replace": ["deepslate"]
      }
    ]
  }
}
```

```json
{
  "format_version": "1.13.0",
  "minecraft:feature_rules": {
    "description": {
      "identifier": "example:first_ore_rule",
      "places_feature": "example:first_ore"
    },
    "conditions": {
      "placement_pass": "underground_pass",
      "minecraft:biome_filter": [
        {
          "any_of": [
            {
              "test": "has_biome_tag",
              "operator": "==",
              "value": "overworld"
            },
            {
              "test": "has_biome_tag",
              "operator": "==",
              "value": "overworld_generation"
            }
          ]
        }
      ]
    },
    "distribution": {
      "iterations": 5,
      "coordinate_eval_order": "zyx",
      "x": {
        "distribution": "uniform",
        "extent": [0, 16]
      },
      "y": {
        "distribution": "uniform",
        "extent": [-64, -55]
      },
      "z": {
        "distribution": "uniform",
        "extent": [0, 16]
      }
    }
  }
}
```
