
---
title: 思想-设计
date: 2025/05/28
tags:
 - js
categories:
 - 思想
---

# 思想1

## 事件回滚思想

当给的数据中，有部分数据在操作中发现不符合规范，所有的操作都需要还原，如果先遍历一次判断是否有不符合的数据，则数据量较大时比较浪费时间，可考虑时间回滚的思维

方案 |	时间复杂度	|空间复杂度	|优点|	缺点
-|-|-|-|-
预检查方案|	O(2n) → O(n)|	O(1)|	数据安全，用户体验好	| 多一次遍历，仍为线性
事务回滚方案|	O(n)|	O(n) |	单次遍历	|需记录状态，实现复杂

```js
const updateFun = (params) => {
    const { editor, currentIndex, matchList, listId, indent, op, block } = params;
    const opIndentDelta = op === 'reduce' ? -2 : 2;
    const MAX_INDENT = 10;
    const MIN_INDENT = 0;

    // 事务管理对象
    const transaction = {
        backups: [] as Array<{ path: Path; originalIndent: number }>, // 备份原始缩进
        changes: [] as Array<() => void> // 记录变更操作
    };

    try {
        // 1. 收集所有需要修改的节点并预备份
        const currentPath = matchList[currentIndex][1];
        const targets = [currentPath]; // 当前节点必须处理

        // 收集后续子节点
        for (let i = currentIndex + 1; i < matchList.length; i++) {
            const [item, path] = matchList[i];
            if (item.style.indent <= indent) break;
            targets.push(path);
        }

        // 2. 备份原始状态（原子性读取）
        targets.forEach(path => {
            const node = Node.get(editor, path) as AdvancedRenderElement;
            transaction.backups.push({
                path,
                originalIndent: node.style?.indent || 0
            });
        });

        // 3. 生成变更操作（不立即执行）
        targets.forEach((path, index) => {
            const backup = transaction.backups[index];
            const newIndent = backup.originalIndent + opIndentDelta;

            // 立即检查合法性
            if (newIndent > MAX_INDENT || newIndent < MIN_INDENT) {
                throw new Error(`缩进值 ${newIndent} 超出允许范围`);
            }

            transaction.changes.push(() => {
                setBlockNode(editor, {
                    style: { ...backup.node.style, indent: newIndent }
                }, { at: path });
            });
        });

        // 4. 执行所有变更（原子性提交）
        Editor.withoutNormalizing(editor, () => {
            transaction.changes.forEach(change => change());
        });

        // 5. 触发列表重排序
        olulListSort([listId], editor);

    } catch (error) {
        // 6. 事务回滚：恢复所有备份
        Editor.withoutNormalizing(editor, () => {
            transaction.backups.forEach(({ path, originalIndent }) => {
                const node = Node.get(editor, path) as AdvancedRenderElement;
                setBlockNode(editor, {
                    style: { ...node.style, indent: originalIndent }
                }, { at: path });
            });
        });

    }
};
```
