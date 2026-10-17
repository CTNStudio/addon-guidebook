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

在对话中包含了一系列**场景（Scene）**，每个场景都对应一段对话内容，这些场景都会被储存在`minecraft:npc_dialogue.scenes`数组下。

下面，我们来看一下场景的相关配置：

- `scene_tag`是场景的标签，使用`dialogue`命令打开一个场景时会用到
- `npc_name`指定了 NPC 在此场景下显示的名称
- `text`指定了将会被显示在文本框中的对话内容
- `on_open_commands`和`on_close_commands`设置了打开或关闭对话时触发的命令
- `buttons`数组表示显示在对话框下方的按钮

在对话中，我们可以使用`@initiator`目标选择器代指与 NPC 交互的玩家。

我们也可以按照如下的方式来对场景内容进行本地化：

```json
{
  "npc_name": {
    "rawtext": [
      {
        "translate": "entity.fanglimao.name" // 翻译键的名称
      }
    ]
  }
}
```

## 打开/转换对话

> 参见：[命令/dialogue - 中文 Minecraft Wiki](https://zh.minecraft.wiki/w/%E5%91%BD%E4%BB%A4/dialogue?variant=zh-cn)

通过`/dialogue`命令，我们可以打开或者转换 NPC 对话：

```
dialogue open <npc: target> <player: target> [sceneName: string]
dialogue change <npc: target> <sceneName: string> [players: target]
```

在其中：

- `npc`指定了要打开或改变的 NPC 实体（场景必须要依靠一个 NPC 才能展示）
- `sceneName`设置了将要打开的场景标签

## \*创建自定义 NPC 实体

在前文中，我们提到对话必须依赖一个 NPC 才能够被展示，但游戏判断一个实体为 NPC 的根据是`minecraft:npc`组件。

所以，我们可以将这个组件添加到我们自己的实体中，以伪造一个 NPC!

```JSON
{
  "minecraft:entity": {
    "components": {
      "minecraft:npc": {}
    }
  }
}
```

但事情并没有到此结束，`minecraft:npc`组件无法指定 NPC 打开时的默认场景，也就是说：此时我们的自定义 NPC 交互时会打开对话编辑页面或显示为空页面：

![](https://zh.minecraft.wiki/images/Npcpet.png?1cf48)

那有没有解决方案呢？当然有！我们可以使用脚本监听实体生成事件，并对与自定义 NPC 有关的事件进行拦截：

```js
import { GameMode, system, world } from "@minecraft/server";

world.afterEvents.entitySpawn.subscribe((event) => {
  const npc = "example:npc"; // 你自定义 NPC 的标识符
  if (event.entity.typeId !== npc) return;
  event.entity.runCommand(`dialogue change @s our_scene`);
});
```

到这一步，我们会发现创造模式的玩家与 NPC 进行交互时仍然会显示编辑界面，因此我们还需要对交互事件进行监听：

```js
import { GameMode, system, world } from "@minecraft/server";

world.beforeEvents.playerInteractWithEntity.subscribe((event) => {
  const npc = "example:npc"; // 你自定义 NPC 的标识符
  if (event.target.typeId !== npc) return;
  if (event.player.getGameMode() === GameMode.Creative) {
    event.cancel = true;
    system.run(() => {
      event.player.setGameMode(GameMode.Survival);
      event.target.runCommand(
        `dialogue open @s ${event.player.name} our_scene`,
      );
      event.player.setGameMode(GameMode.Creative);
    });
  }
});
```

在上面的范例中，当处于创造模式的玩家与 NPC 交互时，我们会取消这一交互事件，并将玩家切换为生存模式后再手动打开对话，并在打开对话后再恢复为创造模式。

## 小结