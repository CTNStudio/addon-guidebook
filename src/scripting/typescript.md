
在本教程中，我们将学习如何使用TypeScript，并使用自动补全功能来提高开发效率。

VS Code内置了JavaScript/TypeScript的自动补全功能，并提供代码提示，因此我们强烈推荐使用其作为主力的开发工具。

## 自动补全
首先，我们需要在计算机上安装[Node.js](https://nodejs.cn/download/)与npm，然后打开PowerShell或终端，在我们项目路径运行如下命令：

```bash
npm i @minecraft/server
npm i @minecraft/server-ui
```

如果需要使用测试版模块，则需要运行：

```bash
npm i @minecraft/server@beta
npm i @minecraft/server-ui@beta
```

这时再打开我们的项目，VS Code就会检测到我们安装的模块，并启用自动补全功能。

::: details 找不到命令？
如果你的终端抛出了如下错误：

```
The term 'npm' is not recognized as a name of a cmdlet, function, script file, or executable program.
```

请检查你是否将Node.js的安装路径添加到了环境变量中，如果没有，请手动添加。
:::

::: details 无法安装？
如果你的终端抛出了如下错误：

```
npm ERR! network request to ... failed, reason: ...
```

请检查你的网络连接是否正常，或者尝试换源，在终端中运行：
```bash
npm config set registry https://registry.npmmirror.com
```
:::

## 使用TypeScript
**TypeScript**是JavaScript的一个超集,它为JavaScript添加了类型系统，并可以转换为JavaScript文件。

### 运行原理
游戏并不能直接读取TypeScript文件，因此我们需要使用[TypeScript编译器](https://www.npmjs.com/package/typescript)将`.ts`文件编译为`.js`文件，然后再交给游戏运行。

除了TypeScript编译器之外，我们还可以使用各种打包器，将我们的TypeScript文件打包为一个或多个JavaScript文件，如Webpack、Rollup、Esbuild等。

### 安装TypeScript编译器

在我们项目路径运行如下命令以安装TypeScript：

```bash
npm install -g typescript
```

### 初始化项目
安装TypeScript编译器后，我们需要初始化我们的项目，在终端中运行如下命令：

```bash
npm init -y
```

这将会在项目路径下生成一个`package.json`文件，这个文件用于描述我们的项目。

接下来，按照上一节所述的方法安装自动补全包，然后在项目的根目录创建一个`tsconfig.json`文件，在其中写入：

```json
{
  "compilerOptions": {
    "module": "ES2022",
    "target": "ES2022",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "rootDir": "./src",
    "outDir": "./<行为包文件夹>/scripts"
  },
  "exclude": [ "node_modules" ],
  "include": [ "src" ]
}
```

在这个范例中，我们将源代码文件夹设置为`src`，编译后的JavaScript文件将会被输出到`<行为包文件夹>/scripts`中，我们需要将`<行为包文件夹>`替换为你的行为包文件夹的名称。

### 编写TypeScript代码
在`src`目录下创建一个`main.ts`文件，并编写如下代码：

```typescript
import { world, Player } from "@minecraft/server";

world.beforeEvents.itemUse.subscribe((event) => {
    const player: Player = event.source; // 注意这里的类型注释
    player.sendMessage("你使用了物品！");
})
```

> [!IMPORTANT]
> 如果使用TypeScript编译器，请确保源代码文件与清单文件中所设置的`entry`值除了后缀名之外完全一致，否则游戏将无法找到编译后的JavaScript文件。

### 编译TypeScript代码
使用`tsc`命令即可编译TypeScript代码：

```bash
tsc
```

除此之外，你可以使用`--watch`让编译器实时编译你的代码：

```bash
tsc --watch
```

编译完成后，我们就可以在游戏内运行我们的行为包了。
