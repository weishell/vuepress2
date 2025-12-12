---
title: 算法
date: 2025/12/11
tags:
 - js
categories:
 - 算法
---

## 复杂度

1. 算法的时间复杂度，定性（数量级）的描述算法运行的时间，用 O 符号表示。

常见的时间复杂度

- O(1) 常数级，无循环
- O(n) 线性，单层循环
- O(logn) 二分算法
- O(n*logn) 单层循环，嵌套二分算法
- O(n^2) 两层循环
- O(n^3) 三层循环，实际不可用

2. 空间复杂度：同时间复杂度，只是把时间换成空间。时间是 CPU 的消耗，空间是内存的消耗。



## 算法常见案例

#### 两数之和
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

1. 对于大型数组，递归可能导致调用栈溢出。
   
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    let index = 0;
    return findValue(nums, index, target);
};

function findValue(nums, index, target) {
    // 如果已经遍历完数组还没找到，返回空数组或null
    if (index >= nums.length - 1) {
        return null;
    }
    
    for(let j = index + 1; j < nums.length; j++) {
        if(nums[index] + nums[j] === target) {
            return [index, j];
        }
    }
    
    // 递归搜索下一个元素
    return findValue(nums, index + 1, target);
}

// 测试
const arr = twoSum([3, 2, 4], 6);
console.log(arr); // 应该输出 [1, 2]
```

