# 地物

**地物（Feature）** 是在地形生成后在每个区块生成的装饰性方块结构[^1]，矿石团簇、地牢、化石、树木等均属于地物。

其数据驱动文件包含两种：地物文件与地物规则文件。地物文件设置了某个地物的组成，而地物规则文件则设置了地物该以何种条件生成。

## 文件结构

地物文件和地物规则文件分别位于行为包根目录下的`features`和`feature_rules`文件夹，其均支持嵌套文件夹。

> 地物文件的 ID 与其他数据驱动文件不同，**其必须与文件路径完全相同**，如：
>
> 一个名为`<行为包>/features/geocentric_ores/diamond.json`的地物文件，其 ID 必须为`geocentric_ores/diamond`（可以有命名空间）。

## 地物类型

地物分为不同的类型，如单方块地物、树木地物、矿石地物等。

本章将会针对比较常用的几部分地物类型进行详细地介绍，同时在最后也会列出所有地物类型以及其简要介绍。

[^1]: [地物 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E5%9C%B0%E7%89%A9)，采用[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)授权