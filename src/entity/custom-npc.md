# 自定义 NPC

## 自定义对话

每个 NPC 都会有**对话（Dialogue）** ，这些对话以 JSON 格式储存在行为包的`dialogue`目录下：

```json
{
  "format_version": "1.17.0",
  "minecraft:npc_dialogue": {
    "scenes": [
      {
        "scene_tag": "intro",
        "npc_name": "Fang Limao",
        "text": "Hello. My name is Fang Limao",
        "on_open_commands": ["/clear @p"],
        "on_close_commands": ["/say Goodbye! "],
        "buttons": [
          {
            "name": "Get Diamond",
            "commands": ["/give @initiator diamond"]
          }
        ]
      }
    ]
  }
}
```
