---
title: slatejs
date: 2025/04/17
tags:
 - 插件库
categories:
 - plugin
---

## slatejs


### slatejs常见问题


#### Transform.delete与removeNodes的区别

关键区别
特性	|Transform.delete	|Transform.removeNodes
-|-|-
操作目标	|文本内容（字符）|	节点（如段落、图片）
参数	|范围（Range）|	路径（Path）
影响范围	|修改文本，不删除节点|	直接移除节点
合并相邻节点|	可能合并文本节点|	不合并，直接删除

示例场景：

+ 删除文字：选中一段文字并按退格键 → 使用 Transform.delete。
+ 删除整个段落：右键删除某个段落块 → 使用 Transform.removeNodes。

  :::tip 提示
  总结：delete 针对**文本**，removeNodes针对**节点**。
  :::

#### Editor.node 和 Node.get
```js
type NodeEntry<T extends Node = Node> = [T, Path]
type Location = Path | Point | Range
Editor.node(editor: Editor, at: Location, options?) => NodeEntry
// 获取某个位置的节点。
```
```js
Node.get(root: Node, path: Path) => Node
// 获取特定 所引用的后代节点path。如果路径为空数组，则获取根节点本身。
Node.getIf(root: Node, path: Path) => Node | undefined
// 获取指定路径的后代节点，undefined如果该节点不存在则返回。这是一种更安全的替代方案，Node.get()因为即使路径无效也不会抛出错误。
```

```js
const [node, path1] = Editor.node(editor, path0);
const node  = Node.get(editor, path0)
```

> 当创建的实力editor上符合Node的构造，也可以指定Node.get(editor,xxx)此时结果和Editor.node一致
> path0可以是相对路径，所以path1最后相当于对路径规整，二者可能相等，也可能不一致
