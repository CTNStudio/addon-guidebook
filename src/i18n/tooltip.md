# 多行名称的物品与方块
有时，我们可能需要向物品添加一些描述，这时**多行名称**就很有用了。

## 无本地化
不论有无本地化，多行名称的物品与方块始终借助的是`minecraft:display_name`组件来实现的，我们先来了解一下无需本地化的情况。

在我们的物品组件中添加：

```json
{
  "format_version": "1.20.30",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_item",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": {
        "texture": "example.first_item"
      },
      "minecraft:display_name": { // [!code focus]
        "value": "物品名称\n换行显示的第二行名称" // [!code focus]
      } // [!code focus]
    }
  }
}
```

可以看到，在行与行之间加上`\n`即可实现不支持本地化的换行。

## 有本地化
接下来，我们了解一下需要本地化时的情况，在我们的物品组件中添加：

```json
{
  "format_version": "1.20.30",
  "minecraft:item": {
    "description": {
      "identifier": "example:first_item",
      "menu_category": {
        "category": "items"
      }
    },
    "components": {
      "minecraft:icon": {
        "texture": "example.first_item"
      },
      "minecraft:display_name": { // [!code focus]
        "value": "%item.example:first_item.name\n%item.example:first_item.desc" // [!code focus]
      } // [!code focus]
    }
  }
}
```

然后在本地化文件中加入：


```
item.example:first_item.name=物品名称
item.example:first_item.desc=换行显示的第二行名称
```

可以看到，有本地化与无本地化的换行名称类似，都是使用`\n`来实现换行，只不过我们需要在本地化键名前加上一个`%`。