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
- 递归搜索
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
- 非递归（用栈模拟）
  ```cpp
  void WR_pre(int node,const vector<int>& l, const vector<int>& r){
      stack<int> s;
      s.push(node);
      while(!s.empty()) {
          int current = s.top();
          s.pop();
          cout << current << " ";
          if (r[current] != 0) {
              s.push(r[current]);
          }
          if (l[current] != 0) {
              s.push(l[current]);
          }
      }
  }
  ```
  ```cpp
  void WR_in(int node,const vector<int>& l, const vector<int>& r) {
      stack<int> s;
      int current = node;
      while (current != 0 || !s.empty()) {
          while (current != 0) {
              s.push(current);
              current = l[current];
          }
          current = s.top();
          s.pop();
          cout << current << " ";
          current = r[current];
      }
  }
  ```
  ```cpp
  void WR_post(int node,const vector<int>& l, const vector<int>& r) {
      stack<int> s;
      int current = node;
      int lastVisited = 0;
      while(current|| !s.empty()) {
          while(current) {
              s.push(current);
              current = l[current];
          }
          current = s.top();
          if(r[current] == 0 || r[current] == lastVisited) {
              cout << current << " ";
              s.pop();
              lastVisited = current; 
              current = 0; 
          } 
          else {
              current = r[current];
          }
      }   
  }
  ```

1.
# 0510 由中根序列和后根序列重建二叉树

## 题目描述

我们知道如何按照三种深度优先次序来周游一棵二叉树，来得到中根序列、前根序列和后根序列。反过来，如果给定二叉树的中根序列和后根序列，或者给定中根序列和前根序列，可以重建一二叉树。本题输入一棵二叉树的中根序列和后根序列，要求在内存中重建二叉树，最后输出这棵二叉树的前根序列。

## 输入格式

两行。第一行是二叉树的中根序列，第二行是后根序列。每个数字表示的结点之间用空格隔开。结点数字范围0～65535。暂不必考虑不合理的输入数据。

## 输出格式

一行。由输入中的中根序列和后根序列重建的二叉树的前根序列。每个数字表示的结点之间用空格隔开。

## 输入输出样例 #1

### 输入 #1

```
9 5 32 67
9 32 67 5
```

### 输出 #1

```
5 9 67 32
```


解：

法一：

a.全局初始化
```cpp
#include<iostream>
#include<vector>
#include<sstream>
#include<string>

using namespace std;

vector<int> in;
vector<int> post;
vector<int> pre;
vector<int> index;
vector<int> l;
vector<int> r;

int search(int x) {
	for (int i = 0; i < index.size(); i++) {
		if (index[i] == x) return i;
	}
	return -1;
}

int main() {
	input();
	build(0, in.size() - 1, 0, post.size() - 1);
	for (int i = 0; i < pre.size(); i++) {
		if (i > 0) cout << " ";
		cout << pre[i];
	}
	cout << endl;
	return 0;
}
```
b.输入处理
```cpp
void input() {
	string line;
	getline(cin, line);
	stringstream ss(line);
	int num;
	while (ss >> num) {
		in.push_back(num);
	}
	getline(cin, line);
	ss.clear();
	ss.str(line);
	while (ss >> num) {
		post.push_back(num);
		index.push_back(num);
	}
}
```
c.重建与前序遍历（惊艳🤩）
```cpp
void build(int in_start,int in_end,int post_start,int post_end) {
	if (in_start > in_end || post_start > post_end) return;
	int root_val = post[post_end];
	pre.push_back(root_val);
	int post_in = -1;
	for(int i = in_start; i <= in_end; i++) {
		if (in[i] == root_val) {
			post_in = i;
			break;
		}
	}
	int left_size = post_in - in_start;
	build(in_start, post_in - 1, post_start, post_start + left_size - 1);
	build(post_in + 1, in_end, post_start + left_size, post_end - 1);
}
```

法二：

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <sstream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

TreeNode* buildTreeHelper(vector<int>& inorder, vector<int>& postorder, int inStart, int inEnd, int postStart, int postEnd, unordered_map<int, int>& inMap) {
    if (inStart > inEnd || postStart > postEnd) 
        return nullptr;

    int rootVal = postorder[postEnd];
    TreeNode* root = new TreeNode(rootVal);

    int pos = inMap[rootVal];
    int leftSize = pos - inStart;

    root->left = buildTreeHelper(inorder, postorder, inStart, pos - 1, postStart, postStart + leftSize - 1, inMap);
    root->right = buildTreeHelper(inorder, postorder, pos + 1, inEnd, postStart + leftSize, postEnd - 1, inMap);

    return root;
}

TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
    if (inorder.empty() || postorder.empty()) 
        return nullptr;

    unordered_map<int, int> inMap;
    for (int i = 0; i < inorder.size(); i++) {
        inMap[inorder[i]] = i;
    }
    return buildTreeHelper(inorder, postorder, 0, inorder.size() - 1, 0, postorder.size() - 1, inMap);
}

void preorderTraversal(TreeNode* root, vector<int>& result) {
    if (root == nullptr) 
        return;
    result.push_back(root->val);
    preorderTraversal(root->left, result);
    preorderTraversal(root->right, result);
}

int main() {
    string inLine, postLine;
    getline(cin, inLine);
    getline(cin, postLine);

    vector<int> inorder, postorder;
    stringstream ssIn(inLine), ssPost(postLine);
    int num;

    while (ssIn >> num) {
        inorder.push_back(num);
    }
    while (ssPost >> num) {
        postorder.push_back(num);
    }

    TreeNode* root = buildTree(inorder, postorder);
    vector<int> preResult;
    preorderTraversal(root, preResult);

    for (int i = 0; i < preResult.size(); i++) {
        if (i > 0) 
            cout << " ";
        cout << preResult[i];
    }
    cout << endl;
    return 0;
}
```



  