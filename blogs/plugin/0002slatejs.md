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
特性	|Transform.delete	|Transform.remove（或 removeNodes）
-|-|-
操作目标	|文本内容（字符）|	节点（如段落、图片）
参数	|范围（Range）|	路径（Path）
影响范围	|修改文本，不删除节点|	直接移除节点
合并相邻节点|	可能合并文本节点|	不合并，直接删除

示例场景：

+ 删除文字：选中一段文字并按退格键 → 使用 Transform.delete。
+ 删除整个段落：右键删除某个段落块 → 使用 Transform.removeNodes。

  :::tip 提示
  总结：delete 针对文本，remove 针对节点。
  :::
