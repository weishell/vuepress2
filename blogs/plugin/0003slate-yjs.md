---
title: slate-yjs
date: 2026/04/14
tags:
 - 插件库
categories:
 - plugin
---

# 基于slate的协同
需要仔细去确定版本，包括react/HocuspocusProvider等，否则很容易出现各种问题

## HocuspocusProvider
```js
 /**
    * WebSocketProvider 对应参数
    * url: 服务地址
    * messageReconnectTimeout: 消息重连超时时间
    * timeout: 连接超时时间
    * delay: 重连延迟时间
    * maxDelay: 最大重连延迟时间
    * jitter: 是否开启随机延迟，保证不同客户端的重连时间不一致，避免重连风暴
    * maxAttempts: 最大重连次数,0标识无限次
    * factor: 重连延迟倍数
    */
   const sharedWebSocket = new HocuspocusProviderWebsocket({
     url: xxxx,
     messageReconnectTimeout: 100000,
     timeout: 100000,
     delay: 4000,
     maxDelay: 40000,
     jitter: true,
     maxAttempts: 50,
     factor: 3,
   })

   /**
    * HocuspocusProvider 对应参数
    * 应用场景 配置HocuspocusProvider参数，包括文档ID、鉴权、事件回调等
    * websocketProvider: WebSocket连接实例，用于与协同服务建立WebSocket连接
    * name: 文档ID
    * token: 鉴权token
    * onConnect: 连接成功回调
    * onDisconnect: 断开连接回调
    * onClose: 关闭连接回调
    */
   const provider = new HocuspocusProvider({
     websocketProvider: sharedWebSocket,
     name: docUUID as string,
     token: xxxx,
     onConnect: () => {
     },
     onDisconnect: () => {
     },
     onClose: () => {
     }
   })
```

## slate-yjs

协同的数据获取后会先通过apply一次insert_nodes加入文档中，这就是初始化操作
```js
  // YjsEditor 源码解析
  const e =  YjsEditor
  // 将 Yjs 的共享根节点（通常是一个 Y.XmlText 或自定义 Y.Map）挂载到编辑器实例上
  e.sharedRoot = sharedRoot
    e.connect = () => {
    // 避免重复读多次链接
    if (YjsEditor.connected(e)) {
      throw new Error('already connected')
    }
    // 深观测拷贝 
    e.sharedRoot.observeDeep(handleYEvents)
    // 将 Yjs 的共享数据转换为 Slate 能理解的文档结构。
    const content = yTextToSlateElement(e.sharedRoot)
    // 直接替换编辑器的editor的children 数组
    // 初始化，将远程已存在的文档内容同步到slate编辑器中
    // 后续的增量同步通过 Yjs 事件机制处理，不需要直接替换整个 children
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
