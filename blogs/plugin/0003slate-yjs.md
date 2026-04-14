---
title: slate-yjs
date: 2026/04/14
tags:
 - 插件库
categories:
 - plugin
---

## slate-yjs

```js
  // YjsEditor 源码解析
  const e = editor as T & YjsEditor
  // 将 Yjs 的共享根节点（通常是一个 Y.XmlText 或自定义 Y.Map）挂载到编辑器实例上
  e.sharedRoot = sharedRoot
    e.connect = () => {
    // 避免重复读哟次链接
    if (YjsEditor.connected(e)) {
      throw new Error('already connected')
    }
    // 深观测拷贝 
    e.sharedRoot.observeDeep(handleYEvents)
    // 将 Yjs 的共享数据转换为 Slate 能理解的文档结构。
    const content = yTextToSlateElement(e.sharedRoot)
    // 直接替换编辑器的根级 children 数组
    // 初始化操作，它将远程已存在的文档内容同步到本地编辑器视图中
    // 后续的增量同步通过 Yjs 事件机制处理，不再直接替换整个 children
    e.children = content.children;
    CONNECTED.add(e);

    Editor.normalize(editor, { force: true });
    if (!editor.operations.length) {
      editor.onChange();
    }
  };
```

```js
  useEffect(() => {
   // 如果有某些前置条件未满足，此时需要先阻止
    if (!xxx || !editor) return
    // 这里会去把数据提供给slatejs
    // e就是 YjsEditor
    // e.children = content.children;
    // 接下来就是apply操作，也就是insert_nodes中对评论等各操作的具体处理
    YjsEditor.connect(editor)
    return () => YjsEditor.disconnect(editor)
  }, [editor,xxx]) //xxx是可能需要的依赖变化，根据项目需求
```
