---
title: "LeetCode两数问题解法集合"
description: "包含两数之和的暴力解法与哈希表优化解法，以及两数相加链表操作的算法实现和时间复杂度分析。"
permalink: "/leetcode-two-number-problems"
tags: [LeetCode, 两数之和, 链表, 哈希表, 算法优化]
aliases: [LeetCode, 两数之和, 链表, 哈希表, 算法优化]
created: "2025-11-12"
modified: "2025-11-12"
published: "2025-11-12"
publish: false
draft: false
enableToc: true
lang: "zh-CN"
---

## 

## 1. 
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1660523775841-a1711614-1617-43d6-8a29-31097295ce5a.png#clientId=u88661d8f-d301-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=300&id=u51d83114&margin=%5Bobject%20Object%5D&name=image.png&originHeight=300&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&size=21180&status=done&style=none&taskId=ubdc47eaa-6324-4473-94a1-19b9d05c6b0&title=&width=786)
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for(int m = 0;m<=nums.size();m++){
            for(int n=m+1;n<nums.size();n++){
                if(nums[n]+nums[m]==target){
                    return {m,n};
                }
            }
        }
        return {};
    }
};
```

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> heap;
        for(int i=0;i<nums.size();i++){
            int r=target-nums[i];
            if(heap.count(r)) return {heap[r],i};
            heap[nums[i]]=i;
        }
        return {};
    }
};
```
**时间复杂度**

## 2.两数相加
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1661138962841-05aef73b-71a2-431d-a106-1a25983f4405.png#clientId=u77b5e573-5ac1-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=508&id=u36bb46fc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=508&originWidth=780&originalType=binary&ratio=1&rotation=0&showTitle=false&size=44993&status=done&style=none&taskId=ud27a1a4b-a9d3-4881-b526-32439841606&title=&width=780)<br />![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1661138983455-ec4a5884-05dd-4325-9044-cde7c5ee0068.png#clientId=u77b5e573-5ac1-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=426&id=u652dd56a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=426&originWidth=777&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18494&status=done&style=none&taskId=u4b65a015-8b3a-4747-9c50-dadf4afdff3&title=&width=777)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {

    }
};
```

![image.png](https://cdn.nlark.com/yuque/0/2022/png/2591540/1661416990626-5264546a-1769-44e8-8e02-03e8a1add67e.png#clientId=uf8796dbb-7645-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=299&id=u0b012917&margin=%5Bobject%20Object%5D&name=image.png&originHeight=299&originWidth=536&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18876&status=done&style=none&taskId=u42dfbfaa-6aef-4230-96bd-22fb8800578&title=&width=536)


