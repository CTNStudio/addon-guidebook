# 渲染控制器

想象一下这种情况：我们想要制作一个有数个变种的怪物，每一个变种都会有独特的纹理与模型，该如何实现呢？

你可能会说：直接每个变种都制作成一个独立的实体不久好了嘛！但是如果每种变种都只有外观上的差别（比如不同种类的猫）、或者是变种非常多，这样的做法未免过于「暴力」

**渲染控制器**就是为了解决这一问题而生的，它的使用非常容易和广泛！

## 短名称

要使用渲染控制器，首先要在实体的客户端文件中声明：

```json
{
  "format_version": "1.10.0",
  "minecraft:client_entity": {
    "description": {
      "identifier": "example:first_entity",
      "materials": {
        "default": "pillager"
      },
      "textures": {
        "default": "textures/entity/first_entity"
      },
      "geometry": {
        "default": "geometry.pillager"
      },
      "render_controllers": ["controller.render.first_entity"]
    }
  }
}
```

在`minecraft:client_entity.render_controllers`数组中，我们可以定义实体使用的渲染控制器。

注意`materials`、`textures`和`geometry`三个属性，他们都是用形如`"default": "xxx"`的方式来记录对应的材质、纹理和模型的。

其实在这里，我们就已经在定义**短名称**了。在上述三个属性中，键值对中的键对应的是短名称，而值对应的是短名称对应的客户端资源路径或标识符。

一个实体可以拥有多个模型、纹理或材质短名称，例如：

```json
{
  "format_version": "1.10.0",
  "minecraft:client_entity": {
    "description": {
      "identifier": "example:first_entity",
      "textures": {
        "default": "textures/entity/first_entity",
        "foo": "textures/entity/first_entity_foo",
        "bar": "textures/entity/first_entity_bar"
      }
    }
  }
}
```

在上面的范例中，我们的实体就拥有了三个不同的纹理短名称`default`、`foo`和`bar`。

而这些短名称，就是给渲染控制器使用的！

## 简单渲染控制器

客户端文件的配置好后，我们就可以制作渲染控制器了，下面展示了一个最简单的渲染控制器：

```json
{
  "format_version": "1.8.0",
  "render_controllers": {
    "controller.render.first_entity": {
      "geometry": "Geometry.default",
      "materials": [
        {
          "*": "Material.default"
        }
      ],
      "textures": ["Texture.default"]
    }
  }
}
```

渲染控制器在渲染时，会从实体客户端文件处获取短名称对应的资源并应用在实体上，例如：

```json
{
  "format_version": "1.8.0",
  "render_controllers": {
    "controller.render.first_entity": {
      "textures": ["Texture.default"]
    }
  }
}
```

上面的范例就代表：采用客户端实体文件定义的`default`纹理应用于实体。

## 多层纹理

在上面的范例中，你或许注意到了`textures`属性应该填入一个数组，我们可以通过这一功能来实现多层纹理的叠加：

```json
{
  "format_version": "1.8.0",
  "render_controllers": {
    "controller.render.first_entity": {
      "geometry": "Geometry.default",
      "materials": [
        {
          "*": "Material.default"
        }
      ],
      "textures": ["Texture.bottom", "Texture.mid", "Texture.top"]
    }
  }
}
```

在上面的范例中，我们为实体应用了三个纹理，这些纹理将会按照数组从前到后的顺序被应用在实体上。

需要注意的是，范例中的`bottom`、`mid`等都是纹理短名称，需要我们在实体客户端文件中手动定义。

## 可变纹理

到了这一步，相信你已经对渲染控制器的工作原理已经有了足够的理解。那么如何利用渲染控制器，实现在本节开头提到的可变纹理呢？

首先，我们需要在渲染控制器里添加一个纹理数组：

```json
{
  "format_version": "1.10.0",
  "render_controllers": {
    "controller.render.first_entity": {
      "arrays": {
        "textures": {
          // 指明这个数组中的元素都是实体的纹理
          "Array.skins": [
            // 数组的名称，可以是其他的名称
            "Texture.tex_1",
            "Texture.tex_2",
            "Texture.tex_3",
            "Texture.tex_4"
          ]
        }
      }
    }
  }
}
```

接下来，再改造实际的纹理引用：

```json
{
  "format_version": "1.10.0",
  "render_controllers": {
    "controller.render.first_entity": {
      "textures": ["Array.skins[q.variant]"]
    }
  }
}
```

其中的核心的就是`"Array.skins[q.variant]"`这行代码，我们来拆解一下：

- `Array.skins`代表了要引用我们上面定义的纹理数组作为实体渲染的纹理
- `[q.variant]`这里实际上使用了 Molang，如果读者有相关基础就可以知道，这代表了「引用数组中的第 `q.variant` 个元素作为实体的纹理」
  - 需要注意的是，这里的第 `q.variant` 个是从 0 开始计数的，也就是说`Texture.tex_1`将会被视作第 0 个纹理，以此类推。

至此，客户端部分的改造就完成了，但我们还需要对实体行为文件做出一定修改。

### 虚拟组件

我们可以为实体添加`minecraft:variant`组件来指定实体的变种值：

```json
{
  "minecraft:entity": {
    "components": {
      "minecraft:variant": {
        "value": 0
      }
    }
  }
}
```

这里的`value`就代表了`q.variant`会返回的值。

实际开发中，我们一般会把这个组件放在组件组中，再利用`minecraft:entity_spawned`内建事件随机为实体指定变种：

```json
{
  "minecraft:entity": {
    "component_groups": {
      "example:variant_1": {
        "minecraft:variant": {
          "value": 0
        }
      },
      "example:variant_2": {
        "minecraft:variant": {
          "value": 1
        }
      },
      "example:variant_3": {
        "minecraft:variant": {
          "value": 2
        }
      }
    },
    "events": {
      "minecraft:entity_spawned": {
        "sequence": [
          {
            "randomize": [
              {
                "trigger": "example:variant_1",
                "weight": 5
              },
              {
                "trigger": "example:variant_2",
                "weight": 3
              },
              {
                "trigger": "example:variant_3",
                "weight": 2
              }
            ]
          }
        ]
      }
    }
  }
}
```

这样，使用`q.variant`就可以查询到 0、1、2 三种变种值了。

## 小结

在本节中，我们学习了：

- 渲染控制器的工作原理
- 如何制作多层纹理
- 如何实现可变纹理