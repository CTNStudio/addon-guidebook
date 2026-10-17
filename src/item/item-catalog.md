> [!IMPORTANT]
> 该功能仅 1.21.60+ 版本可用

在一些大型附加包中，通常有上百个物品，如果它们全都堆在一起而没有分组，那看起来必然非常凌乱。

在 1.21.60.23 中，官方加入了**物品目录（Item Catalog）** 功能，允许我们创建自定义的物品分组并将物品添加到其中，本节教程将会讨论如何使用这一功能。

## 创建物品目录

在行为包根目录下创建`item_catalog`文件夹，这个文件夹将会用于存放我们的物品目录，然后创建一个`crafting_item_catalog.json` ，写入如下内容：

```json
{
  "format_version": "1.21.60",
  "minecraft:crafting_items_catalog": {
    "categories": []
  }
}
```

这就是一个最基础的、没有任何内容的物品目录，接下来我们将学习如何创建自定义物品分组并将物品添加到其中。

## 创建自定义物品分组

在上一小节的`crafting_item_catalog.json`文件写入如下内容：

```json
{
  "format_version": "1.21.60",
  "minecraft:crafting_items_catalog": {
    "categories": [
      {
        "category_name": "items",
        "groups": [
          {
            "group_identifier": {
              "icon": "example:first_item",
              "name": "example:itemGroup.name.firstGroup"
            },
            "items": [
              "example:first_item",
              "example:first_food"
            ]
          }
        ]
      }
    ]
  }
}
```

注意到`categories`是一个数组，其中包含若干个元素，每一个元素都对指定的创造模式物品栏分页进行了修改，`category_name`设置了要修改分页的名称，目前有以下分页可用：

- `construction`（建筑）；
- `equipment`（装备）；
- `items`（物品）；
- `nature`（自然）

而`groups`数组定义了我们要添加的自定义物品分组，在`group_identifier`对象中：

- `icon`属性设置了物品分组的图标，填写物品 ID；
- `name`属性设置了物品分组的本地化字符串，如果填写原版分组的本地化字符串，那么会将`items`中定义的物品按照顺序添加到原版分组的末尾。

在`items`数组中，我们设置了这个物品分组所属的物品，注意**一个物品只能被添加到一个物品分组*，自定义物品分组所对应的物品分页要和物品描述中`menu_category.category`保持一致**。

## 添加分组名称

接下来，在`RP/texts/zh_CN.lang`写入如下内容：

```
example:itemGroup.name.firstGroup=第一个物品分组！
```

自定义物品分组就被加入进游戏了。

## 小结

在本节中，我们学习了物品目录与自定义物品分组的相关知识。

接下来，我们将会学习如何制作自定义的投掷物。



