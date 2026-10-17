# 子包

Minecraft 作为一款风靡世界的游戏，玩家之间的设备差异也十分显著，我们该如何让不同玩家都玩到最适合自己设备的内容呢？

**子包（Subpack）** 可以解决这个问题！它允许玩家在不同版本的资源包中随时切换，且无需创建单独的资源包。

通过子包，我们可以针对不同用户类型提供不同的内容，以适配玩家的需求和设备限制。

## 创建子包

想要创建一个子包，首先需要在资源包的根目录下面创建一个`subpacks`目录。

接下来，在`subpacks`目录中创建若干子目录，以代表不同的子包，例如`sp_1`、`sp_2`、`sp_114514`。

将子包的内容放入上述子目录中，在应用子包时就会自动应用子包中的内容覆盖原版的游戏内容了。

## 声明子包

此时，我们会发现子包并不能被游戏正常识别，这是因为子包需要在**清单文件**中被声明。

例如：

```json
{
  "subpacks": [
    {
      "folder_name": "sp_1",
      "name": "First Subpack",
      "memory_tier": 0
    },
    {
      "folder_name": "sp_2",
      "name": "Second Subpack",
      "memory_tier": 1
    },
    {
      "folder_name": "sp_114514",
      "name": "Third Subpack",
      "memory_tier": 3
    }
  ]
}
```

在上面的范例中：

- `name`是子包显示的名称，**不支持本地化**
- `memory_tier`指定了设备启用该子包必须拥有的内存容量
- `folder_name`是该子包的在`subpacks`目录下对应的文件夹名称

> 1 `memory_tier` = 0.25 GB

在格式版本为 3 的清单文件中，我们还可以使用`memory_performance_tier`属性设置子包所需的内存量：

```json
{
  "subpacks": [
    {
      "folder_name": "sp_1",
      "name": "Low-fi Assets",
      "memory_tier": 6,
      "memory_performance_tier": 1
    },
    {
      "folder_name": "sp_2",
      "name": "Standard Assets",
      "memory_tier": 12,
      "memory_performance_tier": 2
    },
    {
      "folder_name": "sp_3",
      "name": "Hi-fi Assets",
      "memory_tier": 64,
      "memory_performance_tier": 5
    }
  ]
}
```

在新的`memory_performance_tier`属性中，每个等级都代表不同的平台：

| `memory_performance_tier` | 平台                                             | 对应`memory_tier` | 实际内存 (GB) |
| ------------------------- | ------------------------------------------------ | ----------------- | ------------- |
| 1                         | Nintendo Switch                                  | ≤11               | 2-4           |
| 2                         | PS4; PS4 Pro; Xbox One; Xbox One S; 60% 的移动端 | 12-16             | 4-5           |
| 3                         | Xbox One X; Xbox Series S                        | 18-20             | 6-8           |
| 4                         | Xbox Series X; PS5                               | 24-28             | 8-12          |
| 5                         | PS5 Pro; 70% 的 PC 端                            | ≥32               | >12           |

声明好后，子包便可以正常工作了！
