---
title: Blockly的其他特性
date: 2019-04-22 21:59:21
tags: [Blockly]
categories: 前端
---

这篇文章介绍定义Blocks时的其他特性，作为上一篇笔记的补充。

# Block colour

Blockly推荐使用HSV颜色模型（0-360），当然也可以使用如 `#ff0000`

```
init: function() {
  // ...
  this.setColour(160);
}
```

Blockly包含字符串表中的九个颜色常量:

```
'%{BKY_LOGIC_HUE}'
'%{BKY_LOOPS_HUE}'
'%{BKY_MATH_HUE}'
'%{BKY_TEXTS_HUE}'
'%{BKY_LISTS_HUE}'
'%{BKY_COLOUR_HUE}'
'%{BKY_VARIABLES_HUE}'
'%{BKY_VARIABLES_DYNAMIC_HUE}'
'%{BKY_PROCEDURES_HUE}'
```

此外，也可以自己定义颜色常量,通过向 `Blockly.Msg` 中添加：

```
// Define the colour
Blockly.Msg.EVERYTHING_HUE = 42;
// Use in a block or block definition:
block.setColour('%{BKY_EVERYTHING_HUE}');
```

# Generating Code

大多数Blockly应用程序需要将块转换为代码以便执行。下面介绍如何将代码生成器添加到自定义块：

```
Blockly.JavaScript['text_indexOf'] = function(block) {
  // Search the text for a substring.
  var operator = block.getFieldValue('END') == 'FIRST' ? 'indexOf' : 'lastIndexOf';
  var subString = Blockly.JavaScript.valueToCode(block, 'FIND',
      Blockly.JavaScript.ORDER_NONE) || '\'\'';
  var text = Blockly.JavaScript.valueToCode(block, 'VALUE',
      Blockly.JavaScript.ORDER_MEMBER) || '\'\'';
  var code = text + '.' + operator + '(' + subString + ')';
  return [code, Blockly.JavaScript.ORDER_MEMBER];
};
```
## 收集参数

任何块代码生成器的第一个任务是收集所有参数和字段数据。此任务通常使用几种功能:

- getFieldValue

```
block.getFieldValue('END') // 返回指定名称的字段中的值
```

  - 对于文本字段，此函数返回键入的文本。
  - 对于下拉列表，此函数返回与所选选项关联的与语言无关的文本。getFieldValue函数将返回创建下拉列表时指定的文本（Blockly的核心块通常使用大写的英文单词，例如“FIRST”）。
  - 对于变量下拉列表，此函数返回变量下拉列表的面向用户的名称。请注意，此名称不一定与生成的代码中使用的变量名称相同。例如，变量名“for”在Blockly中是合法的，但在大多数语言中会与保留字冲突，因此会重命名为“for2”。同样，阿拉伯语变量名“متغير”在Blockly中是合法的，但在大多数语言中都是非法的，因此将重命名为“_D9_85_D8_AA_D8_BA_D9_8A_D8_B1”。要获取可能在生成的代码中使用的名称的Blockly变量名称，请使用以下调用：

  ```
  Blockly.JavaScript.variableDB_.getName(block.getFieldValue('VAR'), Blockly.Variables.NAME_TYPE);
  ```

- valueToCode

```
Blockly.JavaScript.valueToCode(block, 'FROM', Blockly.JavaScript.ORDER_ADDITION) || '0'
```

此函数查找连接到命名（'FROM'）的块，生成该块的代码，并将代码作为字符串返回。如果未连接输入，则此函数返回null，这就是为什么通常使用布尔'或'和默认值来跟随函数。因此，在上面的示例中，如果没有附加到名为'FROM'的输入的块，则此输入的默认代码将是字符串'0'。
第三个参数指定嵌入所需的操作信息的顺序。每个语言生成器都有一个有序的优先级列表。

- statementToCode

```
Blockly.JavaScript.statementToCode(block, 'DO')
```

此函数查找连接到指定输入的嵌套块堆栈，生成该堆栈的代码，缩进代码，并将代码作为字符串返回。如果未连接输入，则此函数返回空字符串。

## 组装代码

```
var code = 'while (' + argument0 + ') {\n' + branch0 + '}\n';
```

# 范式

## 配置

许多Blockly应用程序用于描述配置，而不是可执行程序。配置应用程序通常首先在工作区上初始化一个根级别块，如：

```
var xml = '<xml><block type="factory_base" deletable="false" movable="false"></block></xml>';
Blockly.Xml.domToWorkspace(Blockly.Xml.textToDom(xml), workspace);
```

这会创建一个不可删除的，不可移动的块，它可以保存所有用户的配置。可以随时将工作空间导出到XML以确定当前配置。

## 串行程序

用户将按顺序执行的块堆叠在一起。

```
var code = Blockly.JavaScript.workspaceToCode(workspace);
```

## 并行程序

实现并行执行的一种方法是使用无头工作区生成多个代码段：

```
var xml = Blockly.Xml.workspaceToDom(workspace);
// Find and remove all top blocks.
var topBlocks = [];
for (var i = xml.childNodes.length - 1, node; block = xml.childNodes[i]; i--) {
  if (block.tagName == 'BLOCK') {
    xml.removeChild(block);
    topBlocks.unshift(block);
  }
}
// Add each top block one by one and generate code.
var allCode = [];
for (var i = 0, block; block = topBlocks[i]; i++) {
  var headless = new Blockly.Workspace();
  xml.appendChild(block);
  Blockly.Xml.domToWorkspace(xml, headless);
  allCode.push(Blockly.JavaScript.workspaceToCode(headless));
  headless.dispose();
  xml.removeChild(block);
}
```

# 操作优先级

为了生成没有冗余括号的正确代码，每个语言生成器都提供有序的优先级列表。这是JavaScript的列表：

```
Blockly.JavaScript.ORDER_ATOMIC = 0;           // 0 "" ...
Blockly.JavaScript.ORDER_NEW = 1.1;            // new
Blockly.JavaScript.ORDER_MEMBER = 1.2;         // . []
Blockly.JavaScript.ORDER_FUNCTION_CALL = 2;    // ()
Blockly.JavaScript.ORDER_INCREMENT = 3;        // ++
Blockly.JavaScript.ORDER_DECREMENT = 3;        // --
Blockly.JavaScript.ORDER_BITWISE_NOT = 4.1;    // ~
Blockly.JavaScript.ORDER_UNARY_PLUS = 4.2;     // +
Blockly.JavaScript.ORDER_UNARY_NEGATION = 4.3; // -
Blockly.JavaScript.ORDER_LOGICAL_NOT = 4.4;    // !
Blockly.JavaScript.ORDER_TYPEOF = 4.5;         // typeof
Blockly.JavaScript.ORDER_VOID = 4.6;           // void
Blockly.JavaScript.ORDER_DELETE = 4.7;         // delete
Blockly.JavaScript.ORDER_AWAIT = 4.8;          // await
Blockly.JavaScript.ORDER_EXPONENTIATION = 5.0; // **
Blockly.JavaScript.ORDER_MULTIPLICATION = 5.1; // *
Blockly.JavaScript.ORDER_DIVISION = 5.2;       // /
Blockly.JavaScript.ORDER_MODULUS = 5.3;        // %
Blockly.JavaScript.ORDER_SUBTRACTION = 6.1;    // -
Blockly.JavaScript.ORDER_ADDITION = 6.2;       // +
Blockly.JavaScript.ORDER_BITWISE_SHIFT = 7;    // << >> >>>
Blockly.JavaScript.ORDER_RELATIONAL = 8;       // < <= > >=
Blockly.JavaScript.ORDER_IN = 8;               // in
Blockly.JavaScript.ORDER_INSTANCEOF = 8;       // instanceof
Blockly.JavaScript.ORDER_EQUALITY = 9;         // == != === !==
Blockly.JavaScript.ORDER_BITWISE_AND = 10;     // &
Blockly.JavaScript.ORDER_BITWISE_XOR = 11;     // ^
Blockly.JavaScript.ORDER_BITWISE_OR = 12;      // |
Blockly.JavaScript.ORDER_LOGICAL_AND = 13;     // &&
Blockly.JavaScript.ORDER_LOGICAL_OR = 14;      // ||
Blockly.JavaScript.ORDER_CONDITIONAL = 15;     // ?:
Blockly.JavaScript.ORDER_ASSIGNMENT = 16;      // = += -= **= *= /= %= <<= >>= ...
Blockly.JavaScript.ORDER_YIELD = 16.5;         // yield
Blockly.JavaScript.ORDER_COMMA = 17;           // ,
Blockly.JavaScript.ORDER_NONE = 99;            // (...)
```

# 缓存参数

## 临时变量

## 功能函数

# 类型检查

Blockly的每个连接（值输入/输出，下一个/上一个语句）都可以用类型信息标记，这样无效的连接将被拒绝。这为用户提供即时反馈并避免许多简单的错误。

## 值输入/出

```
// 例如一个数字块:指定输出时可以设置类型。
Blockly.Blocks['math_number'] = {
  init: function() {
    // ...
    this.setOutput(true, 'Number');
  }
};

// 也可以设置多个值：
this.appendValueInput('VALUE').setCheck(['String', 'Array']);
```

(未完待续...)
