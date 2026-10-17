# 实体属性

**实体属性**可以用于保存数据或加载数据，且无需使用任何组件，其运作原理类似方块属性。

例如：

```json
{
  "minecraft:entity": {
    "description": {
      "identifier": "example:properties_entity",
      "properties": {
        "example:bool_property": {
          "type": "bool",
          "default": true
        },
        "example:enum_property": {
          "type": "enum",
          "values": ["apple", "wheat", "pork"],
          "default": "wheat"
        },
        "example:float_property": {
          "type": "float",
          "range": [1.14, 5.14],
          "default": 1.1981,
          "client_sync": true
        },
        "example:int_property": {
          "type": "int",
          "range": [2, 5],
          "default": 2
        }
      }
    }
  }
}
```

正如上面的范例所示，实体属性储存在`minecraft:entity.description.properties`对象下。每一个属性都会通过`type`属性来定义属性的具体类型，`default`属性来声明属性的默认值。

下面，我们将会了解实体属性的具体类型。

## 属性类型

### 布尔属性

```json
{
  "example:bool_property": {
    "type": "bool",
    "default": true
  }
}
```

最简单的实体属性，只有`true`和`false`两种状态。

### 整数、小数属性

```json
{
  "example:float_property": {
    "type": "float",
    "range": [1.14, 5.14],
    "default": 1.1981,
    "client_sync": true
  },
  "example:int_property": {
    "type": "int",
    "range": [2, 5],
    "default": 2
  }
}
```

这两种属性允许你将属性值设置为`range`范围内的任意小数或整数。

### 枚举类型

```json
{
  "example:enum_property": {
    "type": "enum",
    "values": ["apple", "wheat", "pork"],
    "default": "wheat"
  }
}
```

枚举类型的实体属性允许我们在属性内存储某些特定的字符串。

## 客户端同步

现在，我们的实体属性还只能在行为包中被读写，想要在资源包中读取或写入实体属性，需要我们启用实体属性中的`client_sync`选项：

```json
{
  "example:float_property": {
    "type": "float",
    "range": [1.14, 5.14],
    "default": 1.1981,
    "client_sync": true
  }
}
```

这样，我们的实体属性就会被同步的客户端中供资源包所读取。

## 读写实体属性

我们可以使用 Molang 来读取实体属性，例如：

```c
!q.property('example:float_property')
```

或者是在命令的目标选择器中利用实体属性进行选择：

```bash
kill @e[has_property={example:bool_property=false}]
```

在脚本中，我们也可以读写实体的实体属性：

```javascript
const entity = world.getEntity();
entity.getProperty("example:int_property") === 3;
entity.setProperty("example:int_property", 3);
```

当然，我们也可以使用实体事件来修改实体属性，详情请见上一节，这里不再赘述。

## 小结
在本节中，我们学习了：

- 如何创建实体属性
- 如何开启客户端同步
- 怎样读写实体属性