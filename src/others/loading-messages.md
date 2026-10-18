# 加载消息文件

![](https://zh.minecraft.wiki/images/Bedrock_Loading_Screen_Simplified.png?59db1)

**加载提示（Loading Tips）** 或**加载消息**是在创建和加载世界时显示的一段提示语[^1]。

在 1.20.50 版本，加载消息文件发生了较大的变化，本节将主要介绍新的格式。

加载消息文件储存在资源包根目录下的`loading_messages.json`文件下，例如：

```json
{
  "beginner_loading_messages": ["onlinePlay.message", "beginnerTips.1"],
  "mid_game_loading_messages": [
    "onlinePlay.message",
    "beginnerTips.1",
    "beginnerTips.2"
  ],
  "late_game_loading_messages": [
    "onlinePlay.message",
    "beginnerTips.1",
    "beginnerTips.2",
    "beginnerTips.3"
  ],
  "creative_loading_messages": [
    "onlinePlay.message",
    "tips.creative.1",
    "tips.creative.2"
  ],
  "editor_loading_messages": ["beginnerTips.1", "beginnerTips.2"],
  "realms_loading_messages": [],
  "addons_loading_messages": [],
  "store_progress_tooltips": []
}
```

可以看到，加载消息文件包含以下属性：

- `beginner_loading_messages`、`mid_game_loading_messages`和`late_game_loading_messages`分别对应了游戏的新手、中期和后期提示，这些提示语会根据玩家在游戏中游玩的时间来展示
- `creative_loading_messages`会在默认游戏模式为创造模式的玩家加载游戏时显示
- `addons_loading_messages`会在世界加载了附加包时显示
- `realms_loading_messages`是 Realms 显示的加载提示
- `store_progress_tooltips`是商城加载界面的提示语

[^1]: [加载提示-中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E5%8A%A0%E8%BD%BD%E6%8F%90%E7%A4%BA)，按照 CC BY-NC-SA 3.0 授权。
