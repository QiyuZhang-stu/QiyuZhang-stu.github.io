---
title: "Practice:BinaryTree"
date: 2025-08-05T11:19:00+08:00
categories: 
  - blog
tags:
  - study 
---

一·经典例题

1.
# B3642 二叉树的遍历

## 题目描述

有一个 n(n<=10^6) 个结点的二叉树。给出每个结点的两个子结点编号（均不超过 n），建立一棵二叉树（根节点的编号为 1），如果是叶子结点，则输入 `0 0`。

建好树这棵二叉树之后，依次求出它的前序、中序、后序列遍历。

## 输入格式

第一行一个整数 n，表示结点数。

之后 n 行，第 i 行两个整数 l、r，分别表示结点 i 的左右子结点编号。若 l=0 则表示无左子结点，r=0 同理。

## 输出格式

输出三行，每行 n 个数字，用空格隔开。

第一行是这个二叉树的前序遍历。

第二行是这个二叉树的中序遍历。

第三行是这个二叉树的后序遍历。

## 输入输出样例 #1

### 输入 #1

```
7
2 7
4 0
0 0
0 3
0 0
0 5
6 0
```

### 输出 #1

```
1 2 4 3 7 6 5
4 3 2 1 6 5 7
3 4 2 5 6 7 1
```


解：

a.首先进行前期处理
```cpp
#include<iostream>
#include<vector>

using namespace std;

int main(){
    int n;
    vector<int> l;
    vector<int> r;
    cin >> n;
    l.push_back(0);
    r.push_back(0);
    for(int i=1;i<=n;i++){
        int x,y;
        cin>>x>>y;
        l.push_back(x);
        r.push_back(y);
    }

    return 0;
}
```
b.遍历策略
- 搜索
  - DFS
  ```cpp
  void DFS_pre(int node,const vector<int>& l,const vector<int>& r){
      if(node==0) return;
      cout<<node<<" ";
      DFS_pre(l[node],l,r);
      DFS_pre(r[node],l,r);
  }
  ```
  ```cpp
  void DFS_in(int node,const vector<int>& l,const vector<int>& r){
      if(node==0) return;
      DFS_in(l[node],l,r);
      cout<<node<<" ";
      DFS_in(r[node],l,r);
  }
  ```
  ```cpp
  void DFS_post(int node,const vector<int>& l,vector<int>& r){
      if(node==0) return;
      DFS_post(l[node],l,r);
      DFS_post(r[node],l,r);
      cout<<node<<" ";
  }
  ```

  