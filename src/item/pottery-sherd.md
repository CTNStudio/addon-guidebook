**陶片（Pottery Sherd）** 是一类用于制作饰纹陶罐的物品[^1]，在本节教程中，我们将学习如何添加带有图案的自定义陶片。

## 开始之前

在开始之前，我们先创建一个没有任何功能的普通物品，并确保其格式版本在 1.21.0 以上，具体过程请参照第一章第三节。

完成后，物品行为部分的代码应该像下面这样：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:custom_pottery_sherd",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": "example.custom_pottery_sherd"
    }
  }
}
```

## 注册陶片

游戏使用`minecraft:decorated_pot_sherds`标签判断物品是否为陶片，所以我们需要自定义陶片添加这个标签：

```json
...
"components": {
  "minecraft:icon": "example.custom_pottery_sherd",
  "minecraft:tags": {
    "tags": [ "minecraft:decorated_pot_sherds" ]
  }
 }
 ...
```

这时，我们的自定义陶片已经可以合成饰纹陶罐了，接下来我们要为其添加图案。

## 添加陶片图案

要实现自定义陶片图案，我们要对饰纹陶罐的客户端实体文件进行编辑，如：

```json
{
    "format_version": "1.8.0",
    "minecraft:client_entity": {
		"description": {
            "identifier": "minecraft:decorated_pot",
            "textures": {
                "example:custom_pottery_sherd": "textures/blocks/custom_pottery_pattern"
            }
        }
    }
}
```

由于客户端实体文件在加载时会进行合并而非直接覆盖，所以我们可以放心编辑其客户端文件，无需担心附加包间的冲突。

## 添加客户端资源

接下来，我们要为自定义陶片添加客户端资源，其步骤与第一章第三节所述步骤完全相同，故这里不再赘述。

添加完客户端资源后，一个完整的自定义陶片就制作好了。

## 小结

在本节中，我们学习了如何添加自定义陶片以及陶片图案，完整的陶片行为代码如下所示：

```json
{

  "format_version": "1.21.0",
  "minecraft:item": {
    "description": {
      "identifier": "example:custom_pottery_sherd",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": "example.custom_pottery_sherd",
      "minecraft:tags": {
        "tags": [ "minecraft:decorated_pot_sherds" ]
      }
    }
  }
}
```

接下来，我们将会学习社区等待已久的自定义物品分组功能。


[^1]: [陶片 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%99%B6%E7%89%87)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
