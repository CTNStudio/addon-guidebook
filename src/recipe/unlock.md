# 配方解锁

当玩家符合某些标准时（例如收集材料、进入水体、具有一定数量的填充好的物品栏槽位等），配方会解锁，这个机制被称为**配方解锁（Recipe Unlocking）**。使用一个未解锁的合成配方也会解锁配方。游戏规则`/gamerule doLimitedCrafting`的值为`true`时，玩家无法使用未解锁的配方[^1]。

配方能够通过`/recipe`命令给予或剥夺。

在很长一段时间内，该特性仅存在于 Java 版本中。在 1.20.30 版本中，该特性被移植到了基岩版中。

本节将介绍如何为我们的配方启用配方解锁的机制。

## 启用机制

为了使配方使用配方解锁机制，清单文件的`min_engine_version`属性必须大于 1.20.11（推荐使用 1.20.30）

## 配方适配

接下来，我们需要修改配方文件，为其添加配方解锁的条件：

```json
{
  "minecraft:recipe_shapeless": {
    "unlock": [
      {
        "item": "example:first_item"
      },
      {
        "item": "minecraft:wool",
        "data": 1
      },
      {
        "context": "PlayerInWater"
      }
    ]
  }
}
```

可以看到，配方解锁的条件位于`unlock`数组内，条件可以是以下几种：

- `item`：玩家必须拥有指定物品才能解锁配方；
- `context`：玩家必须满足指定上下文才能解锁配方。

## 条件上下文

目前有三种可用的上下文：

- `AlwaysUnlocked`：始终解锁该配方；
- `PlayerInWater`：玩家进入水体时解锁该配方；
- `PlayerHasManyItems`：玩家拥有 10 个以上的物品时解锁该配方。

## 小结

本节介绍了配方解锁的机制，并介绍了如何为配方添加配方解锁的条件。

接下来，我们将会更加深入探索战利品表的世界。

[^1]: [配方解锁 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%85%8D%E6%96%B9%E4%B9%A6)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权
