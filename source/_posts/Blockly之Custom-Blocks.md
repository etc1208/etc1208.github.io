---
title: Blockly之Define Blocks
date: 2019-04-22 11:06:58
tags: [Blockly]
categories: 前端
---

既上一篇关于Blockly基础入门之后，这里我们重点深入介绍如何自定义Blocks。Blockly自带了大量预先定义好的块，从数学函数到循环结构的一切。但是，往往我们的应用中需要创建自定义块以适应特殊场景。例如，在创建绘图程序时，可能需要创建“半径为R的绘制圆”块。

下面我们主要从三部分介绍：

- 1.定义块：指定其形状，字段和连接点。使用[Blockly Developer Tools](https://developers.google.com/blockly/guides/create-custom-blocks/blockly-developer-tools)是编写此代码的最简单方法。

- 2.代码生成：创建生成器代码以将新块导出为目标编程语言（例如JavaScript，Python，PHP，Lua或Dart）。

- 3.使用块：将新块添加到工具箱或在工作区中使用它。


# 定义块

## 定义方式

- JSON (推荐)
  - 跨平台
  - 简化本地化过程
  - 不支持高级特性（如存取、验证等）

- JS API

> 案例对比:

- JSON形式

```
{
  "type": "string_length",
  "message0": 'length of %1',
  "args0": [
    {
      "type": "input_value",
      "name": "VALUE",
      "check": "String"
    }
  ],
  "output": "Number",
  "colour": 160,
  "tooltip": "qqqq.",
  "helpUrl": "http://xxx"
}
```

- JS API形式

```
Blockly.Blocks['string_length'] = {
  init: function() {
    this.appendValueInput('VALUE')
      .setCheck('String')
      .appendField('length of');
    this.setOutput(true, 'Number');
    this.setColour(160);
    this.setTooltip('qqqq.');
    this.setHelpUrl('http://xxx');
  }
};
```

> 在Web上，使用 `initJson` 函数加载JSON格式的块定义，同时也允许混合使用这两种格式。最好尽可能使用JSON定义块，并仅将JavaScript用于JSON不支持的块定义部分。

```
// 案例：JSON定义块主要内容，JS扩展一个动态tootip

var mathChangeJson = {
  "message0": "change %1 by %2",
  "args0": [
    {"type": "field_variable", "name": "VAR", "variable": "item", "variableTypes": [""]},
    {"type": "input_value", "name": "DELTA", "check": "Number"}
  ],
  "previousStatement": null,
  "nextStatement": null,
  "colour": 230
};

Blockly.Blocks['math_change'] = {
  init: function() {
    this.jsonInit(mathChangeJson);
    // Assign 'this' to a variable for use in the tooltip closure below.
    var thisBlock = this;
    this.setTooltip(function() {
      return 'Add a number to variable "%1".'.replace('%1',
          thisBlock.getFieldValue('VAR'));
    });
  }
};
```

## 块颜色

- JSON的 `colour` 属性
- JS的 `block.setColour()` API

```
// JSON:
{
  // ...,
  "colour": 160,
}

// JS:
init: function() {
  // ...
  this.setColour(160);
}
```

## 声明连接

声明块没有值输出，一般具有前后连接。

可以使用 `nextStatement` 和 `previousStatement` 连接器创建块序列。在Blockly的标准布局中，这些连接位于顶部和底部，使块垂直堆叠。

- 后连接

```
// JSON:
{
  ...,
  "nextStatement": null,
}

// JS:
this.setNextStatement(true);
```

> 具有后连接但没有前连接的块通常表示事件

- 前连接

```
// JSON:
{
  ...,
  "previousStatement": null,
}

// JS:
this.setPreviousStatement(true);
```

## 块输出

块可以具有单个输出，表示为前缘上的拼图连接器。输出连接到值输入。具有输出的块通常称为值块

```
//JSON:
{
  // ...,
  "output": null,
}

// JS:
init: function() {
  // ...
  this.setOutput(true);
}
```

## 块输入

- 值输入：连接到值块的输出连接。 math_arithmetic块（加法，减法）是具有两个值输入的块的示例。

- 语句输入：连接到语句块的前连接。 while循环的嵌套部分是语句输入的示例。

- 虚拟输入：没有块连接。当块配置为使用外部值输入时，类似于换行符。


### JSON中的输入和字段

```
{
  "message0": "set %1 to %2",
  "args0": [
    {
      "type": "field_variable",
      "name": "VAR",
      "variable": "item",
      "variableTypes": [""]
    },
    {
      "type": "input_value",
      "name": "VALUE"
    }
  ]
}
```

> 每个消息字符串与相同数字的args数组配对。例如，message0与args0一起使用。插值标记（％1，％2，...）是指args数组的项目。每个对象都有一个type。其余参数因类型而异

### JavaScript中的输入和字段

JavaScript API包含每种输入类型的append方法：

```
this.appendDummyInput()
    .appendField('for each')
    .appendField('item')
    .appendField(new Blockly.FieldVariable());
this.appendValueInput('LIST')
    .setCheck('Array')
    .setAlign(Blockly.ALIGN_RIGHT)
    .appendField('in list');
this.appendStatementInput('DO')
    .appendField('do');
```

### 配置input API

- setCheck：用于连接输入的类型检查
- setAlign：用于对齐字段
- appendField：用作标签来描述每个输入的用途

### 内联 vs. 外联

块定义可以指定一个可选的布尔值，控制输入是否为内联。

如果没有定义，则Blockly将使用一些启发式方法来猜测哪种模式最佳。

```
// JSON:
{
  // ...,
  "inputsInline": true
}

// JS:
init: function() {
  // ...
  this.setInputsInline(true);
}
```

## Fields

定义块中的UI元素。包括字符串标签，图像和文字数据的输入，如字符串和数字

- Label
- Image
- Text Field
- Drop-down Field
- Checkbox Field
- Colour Picker Field
- Variable Field
- Number Field
- Angle Field
- Date Field

## Tooltips

当用户将鼠标悬停在块上时，工具提示提供即时帮助。如果文本很长，它将自动换行

```
init: function() {
  this.setTooltip("Tooltip text.");
}
```

也可以动态设置：

```
Blockly.Blocks['math_arithmetic'] = {
  init: function() {
    // ...

    // Assign 'this' to a variable for use in the tooltip closure below.
    var thisBlock = this;
    this.setTooltip(function() {
      var mode = thisBlock.getFieldValue('OP');
      var TOOLTIPS = {
        'ADD': Blockly.Msg.MATH_ARITHMETIC_TOOLTIP_ADD,
        'MINUS': Blockly.Msg.MATH_ARITHMETIC_TOOLTIP_MINUS,
        'MULTIPLY': Blockly.Msg.MATH_ARITHMETIC_TOOLTIP_MULTIPLY,
        'DIVIDE': Blockly.Msg.MATH_ARITHMETIC_TOOLTIP_DIVIDE,
        'POWER': Blockly.Msg.MATH_ARITHMETIC_TOOLTIP_POWER
      };
      return TOOLTIPS[mode];
    });
  }
};
```

## Help URL

块可以有一个与之关联的帮助页面。通过右键单击块并从上下文菜单中选择“帮助”，可以向Web用户使用此选项。如果此值为null，则菜单将显示为灰色。

## 更改监听器和验证器

块可以设置change事件的监听器，用来监听workspace的任何更改(包括与此块无关的)。主要用于设置块的警告文本或工作区外的类似用户通知。

```
Blockly.Blocks['block_type'] = {
  init: function() {
    // Example validation upon block change:
    this.setOnChange(function(changeEvent) {
      if (this.getInput('NUM').connection.targetBlock()) {
        this.setWarningText(null);
      } else {
        this.setWarningText('Must have an input block.');
      }
    });
  }
}
```

> 请注意，可编辑字段具有自己的事件侦听器，用于输入验证和副作用

## Per-block configuration

- Deletable State：默认情况下，用户可以删除可编辑工作区上的任何块（不是readOnly）。有时，使某些块永久固定装置是有用的。例如，教程框架代码

```
block.setDeletable(false);

// Any block, including those marked undeletable, may be deleted programmatically:

block.dispose();
```

- Editable State：设置为false时，用户将无法更改块的字段（例如，下拉列表和文本输入）。块在可编辑工作区上默认为可编辑。

```
block.setEditable(false);
```

- Movable State：设置为false时，用户将无法直接移动块。作为另一个块的子节点的不可移动块可能不会与该块断开连接，但如果移动父节点，它将与其父节点一起移动。

```
block.setMovable(false);
```

- Block data

```
this.data = '16dcb3a4-bd39-11e4-8dfc-aa07a5b093db';
```
