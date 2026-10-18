# 闪烁标语文件

**闪烁标语（Splash）** 是主菜单界面中显示在标题下方的黄色动态文本。其内容从下文表格中随机选取，多来自流行文化（包括网络迷因、经典台词、影视游戏梗等）[^1]。

我们可以通过修改资源包根目录下的`splashes.json`实现对闪烁标语的追加与修改：

```json
{
    "canMerge": true,
    "splashes": [
        "This is a splash text!",
        "This is another splash text!"
    ]
}
```

在其中：

- `splashes`数组是我们要添加的闪烁标语
- `canMerge`参数决定了游戏是否会将列表中的闪烁标语与原版闪烁标语合并，如果为`false`，那么游戏只会显示我们添加的闪烁标语

[^1]: [闪烁标语 - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E9%97%AA%E7%83%81%E6%A0%87%E8%AF%AD)，按照 CC BY-NC-SA 3.0 授权。