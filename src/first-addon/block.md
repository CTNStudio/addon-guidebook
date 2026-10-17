附加包也允许我们通过数据驱动的方式添加一个方块，与物品类似，方块的数据驱动文件也由两部分构成：

- 行为文件
- 资源文件

## 行为文件
在我们的行为包下新建`blocks`文件夹，然后创建一个`<方块ID>.json`文件。

> [!TIP]
> 文件不一定必须按照`<方块ID>.json`的格式进行命名，这里这么做是为了方便后续查找；
> 
> 我们也可以把这个文件创建在`blocks`的子文件夹下。

在这个文件中写入以下内容：

```json
{
  "format_version": "1.21.40",
  "minecraft:block": {
    "description": {
      "identifier": "example:first_block",
      "menu_category": {
        "category": "construction"
      }
    },
    "components": {}
  }
}
```

`format_version`是文件使用的格式版本，这里是1.20.40；

在`description`中：

- `identifier`设置了方块的命名空间ID，注意这里的命名空间是`example`，在实际的项目中不建议使用；
- `menu_category`设置了方块在创造模式物品栏的位置，`category`会设置物品的分类，`group`会设置物品的分组；
  - 一般地，`category`可以填`nature`、`equipment`、`construction`或`items`；
  - 一般地，`group`可以填任意原版分组的本地化字符串，注意设置的分组要与分类相对应；
  - 我们也可以通过`item_catalog`添加自定义分组。

在`components`中，我们可以为方块添加**组件**，每一个组件都可以为方块添加相应的功能，接下来我们就为其添加一些基础组件。

### 添加组件
向`components`对象下添加如下内容：

```json
"minecraft:destructible_by_mining": {
    "seconds_to_destroy": 3
  },
  "minecraft:destructible_by_explosion": {
    "explosion_resistance": 3
  },
"minecraft:map_color": "#ffffff",
"minecraft:loot": "loot_tables/blocks/first_block.json"
```

这些组件的功能为：

- `minecraft:destructible_by_mining`组件设置了挖掘方块所需要的时间，这里统一设置为 3；
- `minecraft:destructible_by_explosion`组件设置了方块的爆炸抗性，这里设置为3；
- `minecraft:map_color`设置了方块在地图上显示的颜色；
- `minecraft:loot`设置了方块被挖掘后掉落的战利品。 

现在，我们的文件应该是这样的了：

```json
{
  "format_version": "1.21.40",
  "minecraft:block": {
    "description": {
      "identifier": "example:first_block",
      "menu_category": {
        "category": "construction"
      }
    },
    "components": {
      "minecraft:destructible_by_mining": {
        "seconds_to_destroy": 3
      },
      "minecraft:destructible_by_explosion": {
        "explosion_resistance": 3
      },
      "minecraft:map_color": "#ffffff",
      "minecraft:loot": "loot_tables/blocks/first_block.json"
    }
  }
}
```

## 资源文件

进行到这一步进入游戏，与自定义物品类似，我们就可以在创造模式物品栏的「建筑」分类看到我们的自定义物品了，然而这时它使用了默认纹理，并且名称为一大串英文字符。

接下来，我们将会学习如何为自定义方块的添加客户端资源。

### 添加纹理
因为我们的方块没有应用自定义模型，所以在资源包根目录下的`blocks.json`下定义纹理即可：

```json
{
  "format_version": "1.21.40",
  "example:first_block": {
    "textures": "example.first_block", // 将纹理名设置为`example.first_block`
    "sound": "stone"
  }
}
```

我们将方块的纹理名设置为了`example.first_block`，为了使游戏正常读取纹理，我们需要在资源包的`textures`文件夹创建一个`terrain_texture`，映射纹理名和纹理路径。

假定我们的纹理为`textures/blocks/first_block.png`，则应该在其中写入：


```json
{
  "texture_name": "atlas.terrain",
  "resource_pack_name": "第一个附加包",
  "padding": 8,
  "num_mip_levels": 4,
  "texture_data": {
    "example.first_block": {
      "textures": "textures/blocks/first_block"
    }
  }
}
```

其中：

- `resource_pack_name`设置了资源包的名称；
- `texture_name`设置纹理类型，因为要定义方块纹理名，因此只能填写`atlas.terrain`；
- `texture_data`对象中定义了所有方块的纹理名，其按照如下格式：

```json
"texture_data": {
  "<纹理名1>": {
    "textures": "<纹理路径1>"
  },
  "<纹理名2>": {
    "textures": "<纹理路径2>"
  },
  "<纹理名3>": {
    "textures": "<纹理路径3>"
  }
}
```

> [!IMPORTANT]
> 纹理路径中不要出现后缀名

### 每一面都不同！

我们可以为方块的每一个面分配不同的纹理，如：

```json
{
  "format_version": "1.21.40",
  "example:first_block": {
    "textures": {
      "down": "...",
      "up": "...",
      "north": "...",
      "east": "...",
      "south": "...",
      "west": "..."
    },
    "sound": "stone"
  }
}
```

### 方块名称

在语言文件中添加如下内容：

```
tile.example:first_block.name=第一个方块
```

这时，将附加包导入进游戏，我们就可以看到新添加的自定义方块了拥有了完整的行为与资源了。

## 小结

在本节中，我们了解了：

- 如何加入自定义方块；
- 如何为自定义方块添加资源。

接下来，我们将会了解最困难的一部分——自定义实体。