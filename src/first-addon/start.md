# 创建项目

在本节中，我们将会学习创建一个没有任何内容的空壳附加包项目。

## 创建工作区

在任意适当的位置新建两个文件夹，将其分别命名为`<你的附加包名称>_BP`和`<你的附加包名称>_RP`，如：

```
my_addon_RP/ （资源包）
my_addon_BP/ （行为包）
```

## 创建清单文件

### 基本结构

**清单文件（Manifest File）** 是游戏用于识别附加包的关键文件，其按照 JSON 格式编写。

在我们的资源包和行为包中分别创建一个`manifest.json`，随后在其中写入：

```json
{
  "format_version": 2,
  "header": {
    "name": "..",
    "description": "...",
    "min_engine_version": [1, 21, 0],
    "uuid": "...",
    "version": "..."
  },
  "modules": [
    {
      "type": "resources/data",
      "uuid": "...",
      "version": "..."
    }
  ],
  "dependencies": [
    {
      "uuid": "...",
      "version": "..."
    }
  ]
}
```
> 此范例中为`...`的值需要我们手动填写

`format_version`设置了清单文件的格式版本，我们通常填2。

在`header`中：

- `name`和`description`分别是附加包的名称和描述，支持填入本地化字符串；
- `version`是附加包的版本，在旧版本中只能填形如`[x, y, z]`的向量，但在新版本中也可以填[SemVer 字符串](https://semver.org/lang/zh-CN/)；
- `min_engine_version`设置了可以读取附加包的最低引擎版本，它除了有设置最低游戏版本的作用外，还会影响一些函数和 Molang 的行为；
- `uuid`是附加包的通用唯一识别码，我们将在下文进一步讨论。

在`modules`中：

- `type`设置了附加包的类型：
	- `data`表示行为包；
	- `resource`表资源包；
- `uuid`是模块的通用唯一识别码，**不可以与header的重复**；
- `version`是模块的版本，格式与`header`的相同。

在`dependencies`数组的每一个元素中：

- `uuid`设置了**附加包依赖的附加包**清单文件的`header`中的通用唯一识别码；
	- 一般我们会把资源包设置为依赖行为包，行为包设置为依赖资源包，以防止玩家因漏加包体造成的问题；
- `version`设置了附加包所依赖的附加包的版本。

### 附加包的 UUID

**通用唯一识别码（Universally Unique Identifier, UUID）** 可以标识附加包，以供游戏识别，下面是一个范例性的 UUID：

```
df1ae3b4-4539-46f4-a6f5-caaed515a5d4
```

当游戏识别到有与已有附加包 UUID 重复的附加包导入时，若导入附加包版本高于已有附加包，则会正常导入，反之会拒绝导入，所以**永远不要重复使用 UUID**。

你可以在[这里](https://www.uuidgenerator.net/)自动生成 UUID。

### 完成填写

在了解了清单文件的相关知识后，我们应该把`manifest.json`范例中的`...`替换成你自己的内容。

最后，你的`manifest.json`应该像这样：

```json
{
  "format_version": 2,
  "header": {
    "name": "第一个附加包",
    "description": "我的第一个附加包！",
    "min_engine_version": [1, 21, 0],
    "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4",
    "version": "1.0.0"
  },
  "modules": [
    {
      "type": "data", // 如果是资源包就改成resource
      "uuid": "1ce8eeae-f1d6-4330-bda1-e9fd427406cd",
      "version": "1.0.0"
    }
  ],
  "dependencies": [
    {
      "uuid": "df1ae3b4-4539-46f4-a6f5-caaed515a5d4", // 应该与你资源包header中的uuid一致
      "version": "1.0.0" // 应该与你资源包header中的version一致
    }
  ]
}
```

在确保两个`manifest.json`都填入了正确的内容后，我们就可以进行下一步了。

## 包的图标

每个附加包都应该有一个图标，一般而言，这是一个正方形的`.png`文件，我们把它重命名为`pack_icon.png`，然后放到包的根目录下。

这时，我们的附加包就应该有图标了。

## 导入游戏

我们的附加包已经制作完成，那么我们该如何将其导入进游戏测试呢？

**`com.mojang`** 是游戏的内部数据文件夹。里面包含了游戏运行所需的文件，我们的附加包就应该被放在这里。

> 注意到`com.mojang`文件夹中有两种的附加包文件夹：正常的附加包文件夹和带`development`前缀的文件夹。
> 
> 如果我们把附加包导入进正常的附加包文件夹，附加包将会被保存到世界中，后续的调试将会变得困难，所以**请务必将附加包导入到带`development`前缀的文件夹中！**

### Windows

1. 新建一个快捷方式；
2. 将快捷方式指向的路径设置为`%localappdata%\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\LocalState\games\com.mojang`；
3. 打开快捷方式
4. 将我们的行为包放入`development_behavior_packs`文件夹中；
5. 将我们的资源包放入`development_resource_packs`文件夹中；
6. 大功告成！

### Android

1. 将游戏的 选项-存储-文件存储位置 调整为 外部；
2. 使用文件管理器打开`Android/data/com.mojang.minecraftpe/files/games/com.mojang/`文件夹；
3. 将我们的行为包放入`development_behavior_packs`文件夹中；
4. 将我们的资源包放入`development_resource_packs`文件夹中；
5. 大功告成！ 

## 打包

Minecraft 有自己的附加包格式——`.mcaddon`和`.mcpack`。

用解压缩软件/文件管理器将你的行为包和资源包文件分别打包成`.zip`文件，然后将`.zip`后缀改为`.mcpack`。

将这两个`.mcpack`文件打包进一个`.zip`文件中，然后将这个文件后缀改为`.mcaddon`，我们的附加包就打包完成了。

## 小结

在本节中，我们了解了：

- 清单文件的基本格式与填写方式；
- 如何为附加包添加图标；
- 如何将附加包导入游戏进行测试；
- 如何打包我们的附加包。


此时，我们的附加包结构应该如下所示：

```
my_addon_BP/
├─ manifest.json
├─ pack_icon.png
my_addon_RP/
├─ manifest.json
├─ pack_icon.png
```

接下来，我们将会了解本地化的相关知识。