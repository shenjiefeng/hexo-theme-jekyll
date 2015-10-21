title: 辨析 Sass 中的 Map 和 List
date: 2015-10-21 10:34:12
tags:
- css
---

如果你使用过 Sass 3.3 之前的版本，那么你一定对那段时光颇有感触，那时候没有现如今这么好的条件，那时候的 Map 还只能用多重列表（lists of list）来模拟。多重列表可以实现复杂数据的嵌套定义，但却不是以键值对的形式实现的，所有当我们需要获取其中特定的某一项时就会比较麻烦。Map 这种数据类型天生就是基于键值对的形式，非常便于组织数据。

自从可以使用 Map 之后，开发者们开始毫无顾忌地定义 Map 存储数据，比如断点宽度、颜色值、栅格布局等等响应式排版的细节，都被一股脑的塞进了 Map 中。

那么，有了 Map 之后，我们还有必要使用 List 吗？可能某些人会觉得为了保持向后兼容应该继续使用多重列表模拟 Map，因为可能有些开发者仍然在使用老版本的 Sass 编译器，但实际上，这是多此一举了，Sass 的版本通常由 `package.json` 或者其他同类型的项目配置文件所控制，往往只需一条命令（`gem update sass`）即可更新 Sass 的版本，因此基本上无需考虑对老版本的兼容问题。

<!-- more -->

使用多重列表替代 Map 的优势之一就是减少代码量。下面让我们来比较一下多种列表和 Map 的语法结构以及遍历方式。

## 测试表格

Variable | Description
--- | ---
`site` | Sitewide information.
`page` | Page specific information and custom variables set in front-matter.
`config` | Site configuration
`theme` | Theme configuration. Inherits from site configuration.
`_` (single underscore) | [Lodash](http://lodash.com/) library
`path` | Path of current page
`url` | Full URL of current page
`env` | Environment variables


## 语法比较

<div class="note">
    <h5>测试标题</h5>
    <p>在下面的示例中，我创建了一个用于控制响应式布局的数据，该数据一共有四个断点，每一个断点都包含了 `min-width`、`max-width`、`font-size` 和 `line-height` 四个样式。</p>
</div>

#### Map 语法

下面就是使用 Map 存储的数据，具体来说，该 Map 中首先存储了四个用于标识断点的 Key，相对应的是保存具体属性值得 Value。虽然这种形式可读性更高，但是总体代码量却高达 26 行 450 个字符。

```sass
$breakpoint-map: (
    small: (
        min-width: null,
        max-width: 479px,
        base-font: 16px,
        vertical-rhythm: 1.3
    ),
    medium: (
        min-width: 480px,
        max-width: 959px,
        base-font: 18px,
        vertical-rhythm: 1.414
    ),
    large: (
        min-width: 960px,
        max-width: 1099px,
        base-font: 18px,
        vertical-rhythm: 1.5
    ),
    xlarge: (
        min-width: 1100px,
        max-width: null,
        base-font: 21px,
        vertical-rhythm: 1.618
    )
);
```

#### 多重列表语法

下面的多重列表存储了和上面 Map 同样的数据，在多重列表中没有 Key-Value 的对应关系，这意味着要想找到特定的值，必须使用遍历或 `nth()` 的方式来实现了。从另一个角度来看，多种列表又比 Map 的代码量小得多，总共只有六行 180 个字符。

```sass
$breakpoint-list: (
    (small, null, 479px, 16px, 1.3),
    (medium, 480px, 959px, 18px, 1.414),
    (large, 960px, 1099px, 18px, 1.5),
    (xlarge, 1100px, null, 21px, 1.618)
);
```

## 遍历比较

<div class="note info">
    <h5>测试标题</h5>
    <p>从上面简单地比较中可以粗略的看出，多种列表的代码量明显少于 Map。但是，如果我们需要遍历这些值得话，复杂度又是怎样的呢？</p>
</div>

#### 遍历 Map

我们可以使用如下的代码遍历 Map：

```sass
@each $label, $map in $breakpoint-map {}
```

这里的变量 `$label` 和 `$map` 会随着对 `$breakpoint-map` 的遍历被动态地赋值，`$label` 将会被赋值为 `$breakpoint-map` 的 Key，而 `$map` 会被赋值为 `$breakpoint-map` 的 Value。为了在遍历过程中获取特定值，我们就需要使用 Sass 原生的 `map-get()` 函数，使用该函数需要传入两个参数：Map 的名字和求取的 Key，最后返回该 Map 中匹配该 Key 的 Value。

具体的做法就是使用 `@each` 遍历 Map，然后使用 `map-get()` 获取特定值，最终只需要六行代码 220 个字符即可完成整个遍历：

```sass
@each $label, $map in $breakpoint-map {
    $min-width: map-get($map, min-width);
    $max-width: map-get($map, max-width);
    $base-font: map-get($map, base-font);
    $vertical-rhythm: map-get($map, vertical-rhythm);
}
```

#### 遍历多重列表

遍历多重列表不必像遍历 Map 一样动态获取到 Map 后再使用 `map-get()` 函数取特定值，直接遍历一遍即可获得特定值。

因为多种列表内层的每一个列表结构相同，都有按照相同顺序排列的五个值，所以我们可以持续遍历每个值并赋值给特定的变量。无需调用 `map-get()`，直接引用这些变量即可进行赋值等裸机操作。最终遍历多重列表只使用了两行代码 100 个字符：

```sass
@each $label, $min-width, $max-width, $base-font, $vertical-rhythm in $breakpoint-list {
}
```

## 慎用多重列表

<div class="note warning">
    <h5>测试标题</h5>
    <p>经过上述的比对，看起来多重列表各方面都在碾压 Map，实则不然，Sass 中添加 Map 有一条非常重要的原因就是：Key-Value 的映射关系。</p>
</div>

#### 遗漏键值

如果要使用多重列表，那么就必须保证自己非常熟悉多重列表内部的每一项所代表的意义。下面我们举个例子，来看看遗漏了某些值的情况：

```sass
$breakpoint-list: (
    (small, null, 479px, 16px, 1.3),
    (medium, 480px, 959px, 18px, 1.414),
    (large, 960px, 1099px, 18px, 1.5),
    (xlarge, 1100px, 21px, 1.618)
);

p {
    @each $label, $min-width, $max-width, $base-font, $vertical-rhythm in $breakpoint-list {
        @if $min-width {
            @include breakpoint( $min-width ) {
                font-size: $base-font;
                line-height: $vertical-rhythm;
            }
        } @else {
            font-size: $base-font;
            line-height: $vertical-rhythm;
        }
    }
}
```

当我们尝试运行这段代码时，结果肯定是错误地，因为在 `$breakpoint-list` 的最后一行，`xlarge` 被赋值给了 `$label`，`1100px` 被赋值给了 `$min-width`，`21px` 被赋值给了 `$max-width`, `1.618` 被赋值给了 `$base-font`，最终导致 `$vertical-rhythm` 没有被赋值，结果就是 `font-size` 的属性值是错的，`line-height` 的属性值是空的。此外，Sass 还不会对此抛出错误，导致我们无从知晓错误所在。

如果我们使用 Map 来代替这里的多重列表，那么使用 `map-get()` 函数即使遇见空值也能正确获得想要的结果。这就是值得我们慎重思考的地方：多种列表虽然简单快速，但是丧失了 Map 中的容错能力和快速取值能力。

#### 查找特定列表

在多重列表中查找特定列表简直就是一种折磨。如果使用 Map，那么配合 `map-get()` 函数可以快速定位到特定子 Map：

```sass
$medium-map: map-get($maps, medium);
```

但如果要获取多种列表 `medium` 列表，麻烦可就大了：

```sass
@function get-list($label) {
    @each $list in $breakpoint-list {
        @if nth($list, 1) == $label {
            @return $list;
        }
    }
    @return null;
}
$medium-list: get-list(medium);
```

这段代码的逻辑就是遍历整个多重列表，知道找到第一个匹配项，然后返回，如果一直没有找到匹配项，就一直遍历到末尾，然后返回 `null`。这实际上就是手工实现了 `map-get()` 的逻辑。

#### 缺少原生的 Map 函数

Sass 提供了诸多的原生函数用于处理 Map 数据类型，但是多重列表是没法调用这些函数的，比如，使用 `map-merge()` 可以合并两个 Map，如果两个 Map 有相同的值，则取第二个 Map 的值为最终值。当然你也可以在多重列表中使用 `join()` 或 `append()` 来增加新列表，从而模拟出 `map-merge()` 的效果。

另一个实用的 Map 函数就是 `map-has-key()`，对于依赖 `map-get()` 的自定义函数来说，`map-has-key()` 可以用来验证特定的 Key 是否存在。但在列表中是完全没有相似的方法。

## 总结

<div class="note unreleased">
    <h5>Test Title</h5>
    <p>相比起列表来说，Key-Value 模型的 Map 显然更有力量，原生的 Sass Map 函数更是提供了强力的数据查找和验证工具。</p>
</div>

虽然多重列表代码量少，但并不能像 Map 一样进行错误检查或验证参数。在大多数时候，相比较多重列表而言，我相信 Map 是更好的选择。如果是为了更少的代码量和其他简单地调用，那么我偶尔会用用多重列表，但是从项目的宏观控制和数据存储方面显然更优秀。
