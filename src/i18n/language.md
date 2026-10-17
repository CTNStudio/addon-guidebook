
> [!IMPORTANT]
> 此文中所述的做法不受官方支持，因此无法保证所做的更改会在未来依旧能正常工作。 

## 改写languages.json

首先，在我们资源包的`texts`文件夹下的`languages.json`中添加如下内容：

```json
[
    "lg_CU", // 新语言的文件名
    "zh_CN",
    "en_US"
]
```

## 添加language_names.json
在`languages.json`同级目录下新建名为`language_names.json`的文件，这个文件决定了我们的自定义语言在游戏中的显示名称，格式如下：

```json
[
  [ "<新语言的文件名>", "<新语言的名称>" ]
]
```

因此，我们要在其中写入如下内容：

```json
[
  [ "lg_CU", "自定义语言" ]  
]
```

## 添加语言文件

下载[原版资源包](https://aka.ms/resourcepacktemplate)，将`texts/zh_CN.lang`复制进我们附加包的`texts`文件夹，重命名为`lg_CU.lang`。

此时，我们可以通过修改键名的方法来修改该语言的翻译，如：

```json
item.apple.name=苹果 // [!code --]
item.apple.name=果苹 // [!code ++]
``` 

大功告成！将我们的附加包导入进游戏，加载至全局资源，便可以使用我们的自定义语言了。