---
title: 'Blockly: A JavaScript library for building visual programming editors'
date: 2019-04-16 21:38:44
tags: [Blockly]
categories: 前端
---

最近在线教育领域比较火热的当属少儿编程，主要针对6-16岁青少年儿童提供在线编程教育。恰好最近经手一个少儿编程项目，里面的可视化编程部分使用了谷歌的Blockly库。Blockly库可以为web&移动应用添加一个可视代码编辑器，它使用互锁的图形块来表示代码概念，如变量，逻辑表达式，循环等；通过拖拽组合这些块达到组合代码逻辑的效果，最终它会以我们选定的编程语言输出语法正确的代码。

[Blockly](https://developers.google.com/blockly/)旨在轻松安装到我们的Web应用程序中。用户拖动块，Blockly生成代码，我们的应用程序使用该代码执行某些操作。从应用程序的角度来看，Blockly只是一个文本区域，用户可以在其中键入语法上完美的编程语言。

# 特点 & 优势

- 纯JS库，大小150kb以下。
- 100％客户端,没有服务器端依赖项。
- 兼容所有主流浏览器：Chrome，Firefox，Safari，Opera和IE。 
- 高度可定制和可扩展。
- 开源。可以在Web和Android应用程序中使用它。
- 可导出。用户可以将基于块的程序提取到通用编程语言，平滑过渡到基于文本的编程。
- 可扩展。添加自定义块或删除不需要的块和功能来调整Blockly以满足您的需求。

# 支持语言

- JavaScript
- Python
- PHP
- Lua
- Dart

# 构建Blockly应用

- 1.集成Blockly编辑器：最简单的Blockly编辑器包含一个用于存储块类型的工具箱(`toolbox`)和一个用于排列块的工作空间(`workspace`)。

- 2.创建应用程序块:在程序中安装了Blockly后，需要为用户创建用于编码的块，然后将其添加到Blockly工具箱中。

- 3.构建其余部分: Blockly本身就是一种生成代码的方法,程序的核心在于决定如何处理该代码。

# Get Started (Web)

## Get the Code

- [ZIP File](https://github.com/google/blockly/zipball/master)
- [TAR File](https://github.com/google/blockly/tarball/master)
- [GitHub](https://github.com/google/blockly)

## Injecting Blockly

### 固定大小的Workspace

将Blockly放入网页的最简单方法是将其注入空的“div”标签。

- 1.引入核心Blockly脚本和核心块集

```
<script src="blockly_compressed.js"></script>
<script src="blocks_compressed.js"></script>
```

- 2.引入用户语言

```
<script src="msg/js/en.js"></script>
```

- 3.在页面添加一个空div并设置其大小

```
<div id="blocklyDiv" style="height: 480px; width: 600px;"></div>
```

- 4.在页面的任何位置添加toolbox的结构

```
<xml id="toolbox" style="display: none">
  <block type="controls_if"></block>
  <block type="controls_repeat_ext"></block>
  <block type="logic_compare"></block>
  <block type="math_number"></block>
  <block type="math_arithmetic"></block>
  <block type="text"></block>
  <block type="text_print"></block>
</xml>
```

- 5.将Blockly注入空div

```
// workspace暂时未用到，但稍后当想要保存块或生成代码时需要依靠它
var workspace = Blockly.inject('blocklyDiv', {toolbox: document.getElementById('toolbox')});
```

### 可变大小的Workspace

使用iframe，CSS和JavaScript定位等方式，而不是固定宽高的div来承载blockly

## Configuration

`Blockly.inject` 的第二个参数是一个对象，用来对Blockly进行配置，支持以下配置项：

|Name|Type|Description|
| :-: | :-: |
|collapse:|	boolean|	允许折叠或展开块。如果 `toolbox` 具有类别，则默认为true，否则为false
|comments:|	boolean|	允许块有注释。如果 `toolbox` 具有类别，则默认为true，否则为false
|css:|	boolean|	如果为false，请不要注入CSS（提供CSS成为document的责任）。默认为true
|disable:|	boolean|	允许禁用块。如果 `toolbox` 具有类别，则默认为true，否则为false
|grid:|object|配置工作区网格
|horizontalLayout:|	boolean|	如果true: `toolbox` 是水平的，false是垂直的。默认为false
|maxBlocks:|	number|	可以创建的最大块数,默认为无限
|maxInstances:|	object|	从块类型映射到可以创建的该类型的最大块数。未声明的类型默认为Infinity
|media:|	string|	从页面（或框架）到Blockly媒体目录的路径。默认为“https://blockly-demo.appspot.com/static/media/”
|oneBasedIndex:|	boolean|	若为true：list和string 操作索引从1开始；false:索引从0开始。默认true
|readOnly:|	boolean| true:禁止用户编辑。默认false
|rtl:|	boolean|	true：镜像编辑器. 默认false
|scrollbars:|	boolean|	设置工作空间是否可滚动。如果 `toolbox` 具有类别，则默认为true，否则为false
|sounds:|	boolean|	如果为false，则不播放声音（例如，单击和删除）。默认为true
|theme:|	Blockly.Theme|	如果未提供主题，则默认为经典主题. See [Themes](https://developers.google.com/blockly/guides/configure/web/themes)
|`toolbox`:|	XML nodes or string|	可用的类别和块的树结构(`重点！！！！！！`)
|toolboxPosition:|	string|	toolbox的方向，两种取值`start` or `end`。"start"： on top (if horizontal) or left (if vertical and LTR) or right (if vertical and RTL)。 "end" ：toolbox is on opposite side. 默认 "start".
|trashcan:|	boolean|	显示或隐藏垃圾桶。如果 `toolbox` 具有类别，则默认为true，否则为false
|maxTrashcanContents:|	number|	将显示在垃圾箱弹出窗口中的最大已删除项目数。'0'禁用该功能。默认为'32'
|zoom:|	object|	配置缩放行为. See [Zoom](https://developers.google.com/blockly/guides/configure/web/zoom)

> 最重要的选项是：toolbox。这是一个XML树，它指定工具箱中可用的块、分组方式以及是否有类别。

> 除了Blockly附带的默认块之外，可能需要构建自定义块来调用Web应用程序的API。

## Toolbox

Toolbox是用户可以创建新块的侧边菜单。Toolbox的结构使用XML指定，XML可以是节点树，也可以是字符串表示。将此XML注入到页面中时，会将其传递给Blockly。如果您不想手动输入XML，建议使用Blockly Developer Tools，可以构建一个Toolbox并使用可视化界面自动生成其XML

```
// using a tree of nodes:
<xml id="toolbox" style="display: none">
  <block type="controls_if"></block>
  <block type="controls_whileUntil"></block>
</xml>
<script>
  var workspace = Blockly.inject('blocklyDiv',
      {toolbox: document.getElementById('toolbox')});
</script>
```

```
// using a string representation:
<script>
  var toolbox = '<xml>';
  toolbox += '  <block type="controls_if"></block>';
  toolbox += '  <block type="controls_whileUntil"></block>';
  toolbox += '</xml>';
  var workspace = Blockly.inject('blocklyDiv', {toolbox: toolbox});
</script>
```

> 如果只存在少量blocks，可以没有任何类别（如上例）。在此简单模式下，所有可用块都显示在工具箱中，主工作区上没有滚动条，并且不需要垃圾桶

### Categories

- Toolbox中的块可以按类别（Category）组织

```
<xml id="toolbox" style="display: none">
  <category name="Control">
    <block type="controls_if"></block>
    <block type="controls_whileUntil"></block>
    <block type="controls_for">
  </category>
  <category name="Logic">
    <block type="logic_compare"></block>
    <block type="logic_operation"></block>
    <block type="logic_boolean"></block>
  </category>
</xml>
```

> Category的存在改变了Blockly的UI以支持更大的应用程序。出现滚动条、允许无限大的工作空间、垃圾桶出现、上下文菜单包含更多高级选项，例如添加注释或折叠块等；当然这些都是可配置的。

- 可以使用可选的颜色属性为每个类别指定颜色：

```
<xml id="toolbox" style="display: none">
  <category name="Logic" colour="210">...</category>
  <category name="Loops" colour="120">...</category>
</xml>
```

### Dynamic categories

类别中无内容定义，但具有 `custom` 属性。这些类别将使用适当的块自动填充。
```
<category name="Variables" colour="330" custom="VARIABLE"></category>
```

### Tree of Categories

```
// 类别可以嵌套在其他类别中
<xml id="toolbox" style="display: none">
  <category name="Core">
    <category name="Control">
      <block type="controls_if"></block>
      <block type="controls_whileUntil"></block>
    </category>
    <category name="Logic">
      <block type="logic_compare"></block>
      <block type="logic_operation"></block>
      <block type="logic_boolean"></block>
    </category>
  </category>
  <category name="Custom">
    <block type="start"></block>
    <category name="Turn">
      <block type="turn_left"></block>
      <block type="turn_right"></block>
    </category>
  </category>
</xml>
```

> 类别可以包含子类别和块

### Block Groups

```
<xml id="toolbox" style="display: none">
  <block type="math_number">
    <field name="NUM">42</field>
  </block>

  <block type="controls_for">
    <value name="BY">
      <block type="math_number">
        <field name="NUM">1</field>
      </block>
    </value>
  </block>
</xml>
```

> 建议使用 [Code application](https://blockly-demo.appspot.com/static/demos/code/index.html)构建blocks,复制生成的xml结果

### Separators

在任意两个类别之间添加 `<sep></sep>` 标记将创建一个分隔符。

> 默认情况下，每个块与其下邻居分开24px。可以使用'gap'属性更改此分隔，该属性将替换默认间隙

```
<xml id="toolbox" style="display: none">
  <block type="math_number"></block>
  <sep gap="32"></sep>
  <block type="math_arithmetic">...</block>
  <sep gap="8"></sep>
  <block type="math_arithmetic">...</block>
</xml>
```

### Buttons and Labels

可以在任何可以将block放入toolbox的位置放置按钮或标签,并可为其添加CSS样式类名

```
<xml id="toolbox" style="display: none">
  <block type="logic_operation"></block>
  <label text="A label" web-class="myLabelStyle"></label>
</xml>

<style>
.myLabelStyle>.blocklyFlyoutLabelText {
  font-style: italic;
  fill: green;
}
</style>
```

按钮应该有回调函数;标签不应该。为给定按钮设置单击时回调如下：

```
<button text="A button" callbackKey="myFirstButtonPressed"></button>

// js
yourWorkspace.registerButtonCallback(yourCallbackKey, yourFunction)
```

### Disabled

可以使用可选的 `disabled` 属性单独禁用toolbox中的block:

```
<xml id="toolbox" style="display: none">
  <block type="math_number"></block>
  <block type="math_single" disabled="true"></block>
</xml>
```

### Changing the Toolbox

应用程序可以通过函数调用随时更改toolbox中可用的块：

```
workspace.updateToolbox(newTree);
```

> 与初始配置一样，newTree可以是节点树或字符串表示。唯一的限制是模式不能改变;也就是说:如果最初定义的toolbox中有category，则新toolbox也必须具有category（尽管类别可能会更改）；反之亦然

更改toolbox会导致一些小的UI重置：
 
- 在具有类别的toolbox中，弹出按钮在打开时将关闭。 
- 在没有类别的toolbox中，用户更改的任何字段将恢复为默认值。 
- 任何toolbox如过长以至于它超出页面将使其滚动条跳到顶部。

## Add Custom Blocks

虽然Blockly定义了许多标准块，但大多数应用程序需要定义和实现至少一些域相关块

Block由三部分组成：

- 1.Block definition object：定义块的外观和行为，包括文本，颜色，字段和连接。

Blockly通过JS文件加载各种Blocks。`blocks/` 目录包括标准块的几个示例。假设您的块不适合现有类别，请创建一个新的JS文件并在HTML文件的`<script>`中引入

> 注意：大多数Block都可以使用[Blockly Developer Tools](https://developers.google.com/blockly/guides/create-custom-blocks/blockly-developer-tools)定义，而不是手动创建下面的代码。

```
// JSON
Blockly.Blocks['string_length'] = {
  init: function() {
    this.jsonInit({
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
      "tooltip": "Returns number of letters in the provided text.",
      "helpUrl": "http://www.w3schools.com/jsref/jsref_length_string.asp"
    });
  }
};

// or JS:
Blockly.Blocks['string_length'] = {
  init: function() {
    this.appendValueInput('VALUE')
        .setCheck('String')
        .appendField('length of');
    this.setOutput(true, 'Number');
    this.setColour(160);
    this.setTooltip('Returns number of letters in the provided text.');
    this.setHelpUrl('http://www.w3schools.com/jsref/jsref_length_string.asp');
  }
};
```

+ `string_length`：这是块的类型名称。由于所有块共享相同的命名空间，因此最好用类别(string)+方法名(length)命名。
+ `init`：此函数定义块的外观


- 2.Toolbox reference：toolbox XML中对块类型的引用，用户可以将其添加到工作区。

```
// 定义后，使用类型名称将块引用到toolbox：
<xml id="toolbox" style="display: none">
  <category name="Text">
    <block type="string_length"></block>
  </category>
  ...
</xml>
```
- 3.Generator function：生成此块的代码字符串。它总是用JavaScript编写，即使目标语言不是JavaScript，甚至是针对Android的Blockly。

```
Blockly.JavaScript['text_length'] = function(block) {
  // String or array length.
  var argument0 = Blockly.JavaScript.valueToCode(block, 'VALUE',
      Blockly.JavaScript.ORDER_FUNCTION_CALL) || '\'\'';
  return [argument0 + '.length', Blockly.JavaScript.ORDER_MEMBER];
};
```

## Code Generators

Blockly不是一种编程语言，不能“运行”一个Blockly程序。可以将用户的程序转换为JavaScript，Python，PHP，Dart或其他语言。

### Generating Code

- 1.引入相关语言生成器

  - [javascript_compressed.js](https://raw.githubusercontent.com/google/blockly/master/javascript_compressed.js)
  - [python_compressed.js](https://raw.githubusercontent.com/google/blockly/master/python_compressed.js)
  - [php_compressed.js](https://raw.githubusercontent.com/google/blockly/master/php_compressed.js)
  - [lua_compressed.js](https://raw.githubusercontent.com/google/blockly/master/lua_compressed.js)
  - [dart_compressed.js](https://raw.githubusercontent.com/google/blockly/master/dart_compressed.js)

```
// 在blockly_compressed.js之后引入，如：
<script src="blockly_compressed.js"></script>
<script src="javascript_compressed.js"></script>
```

```
// 通过此调用，用户的块可以随时从您的应用程序导出到代码：
var code = Blockly.JavaScript.workspaceToCode(workspace);
```

> 在上面的示例中用Python，PHP，Lua或Dart替换JavaScript以切换生成的语言。

### Realtime Generation

生成代码是一种非常快速的操作，因此频繁调用此函数没有任何害处。一个常见的策略是通过向Blockly的 `change事件` 添加一个监听器来实时生成和显示代码：

```
function myUpdateFunction(event) {
  var code = Blockly.JavaScript.workspaceToCode(workspace);
  document.getElementById('textarea').value = code;
}
workspace.addChangeListener(myUpdateFunction);
```

## Events

工作区(Workspace)上的每个更改都会触发一个事件。这些事件完整地描述了每个变化的前后状态。

### Listening to Events

工作区(Workspace)具有 `addChangeListener` 和 `removeChangeListener` 方法，可用于侦听事件流

```
function onFirstComment(event) {
  if (event.type == Blockly.Events.CHANGE &&
      event.element == 'comment' &&
      !event.oldValue && event.newValue) {
    alert('Congratulations on creating your first comment!')
    workspace.removeChangeListener(onFirstComment);
  }
}
workspace.addChangeListener(onFirstComment);
```

## Generating and Running Javascript

```
Blockly.JavaScript.addReservedWords('code'); // 将`code`添加到保留字列表
var code = Blockly.JavaScript.workspaceToCode(workspace);

// The resulting code can be executed
try {
  eval(code);
} catch (e) {
  alert(e);
}
```

- 改进1: `eval` 被包装在 `try / catch`中，因此任何运行时错误都是可见的。
- 改进2: 将`code`添加到保留字列表中，这样如果用户的代码包含该名称的变量，它将自动重命名而不是冲突。应以这种方式保留任何局部变量。

## Importing and Exporting Blocks

如果您的应用程序需要保存并存储用户的blocks并在以后访问时恢复它们，请使用此调用导出到XML：

```
// 从blocks导出xml字符串
var xml = Blockly.Xml.workspaceToDom(workspace);
var xml_text = Blockly.Xml.domToText(xml);
```

> 这将生成一个包含用户块的XML的最小（但很难看）的字符串。如果希望获得更可读（但更大）的字符串，请改用 `Blockly.Xml.domToPrettyText`

```
// 从XML字符串恢复到blocks:
var xml = Blockly.Xml.textToDom(xml_text);
Blockly.Xml.domToWorkspace(xml, workspace);
```

> 这将在工作区生成xml字符串所代表的blocks

# 最佳实践

- "C"型块在内部顶侧有一个连接器，没有底部连接器；声明块内部有上下两个连接器，这样嵌套在"C"型块内的声明块会有空隙，为了提示用户还可以继续堆叠更多声明块；

- Blockly使用紧密贴合的拼图形状进行值连接；使用视觉上独特的对齐凹槽进行声明堆叠；

- 忽略大小写，可以随意命名；

- 程序可视化部分放在toolbox旁边；

# 其他推荐

- [Scratch Blocks](https://scratch.mit.edu/developers)：由麻省理工学院Scratch背后的人员设计并基于Blockly代码库，Scratch Blocks提供了一种简化的编程模型，非常适合年轻学习者。 
- [PXT](https://github.com/Microsoft/pxt)：为Microsoft MakeCode编辑器提供支持的可视化和基于文本的编程环境。 PXT将Blocks，TypeScript，模拟器和编译器捆绑在一个库中。 
- [Droplet](https://github.com/PencilCode/droplet)：为Pencil Code提供支持的图形化编程编辑器，其独特之处在于能够将代码转换为块。 
- [Snap](https://github.com/jmoenig/Snap--Build-Your-Own-Blocks)：一种Scratch风格的图形编程语言，它不是一个库，而是一个带有集成执行环境的完整应用程序。
