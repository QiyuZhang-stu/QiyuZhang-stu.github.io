---
title: "Practice:BinaryTree"
date: 2025-08-05T11:19:00+08:00
categories: 
  - blog
tags:
  - study 
---

一·基础题目

1.
# 二叉树的 BFS 遍历

## 题目描述
给定一棵二叉树（以结点编号和左右孩子编号的形式输入），请输出这棵树的 **层序遍历（BFS 遍历）** 序列。

## 输入格式
- 第一行：一个整数 `n` (1 ≤ n ≤ 1000)，表示结点数。  
- 接下来 `n` 行：每行三个整数 `val left right`，分别表示：  
  - 当前结点的值  
  - 左孩子的编号  
  - 右孩子的编号  
  其中 `-1` 表示没有对应的孩子。  
- 结点编号从 `1` 开始，第 `1` 个结点为根结点。  

## 输出格式
输出二叉树的层序遍历结果，数字之间用空格分隔。

## 输入样例

```
5
1 2 3
2 4 -1
3 -1 5
4 -1 -1
5 -1 -1
```

## 输出样例

```
1 2 3 4 5
```


解：

```cpp
#include<iostream>
#include<vector>
#include<queue>

using namespace std;

int n;
vector<int> l,r,final,val;

void input(){
    l.push_back(-1);
    r.push_back(-1);
    val.push_back(-1);
    cin>>n;
    for(int i=1;i<=n;i++){
        int temp,L,R;
        cin>>temp>>L>>R;
        val.push_back(temp);
        l.push_back(L);
        r.push_back(R);
    }
}

void BFS(int start){
    queue<int> q;
    q.push(start);
    while(!q.empty()){
        int cur=q.front();
        q.pop();
        final.push_back(val[cur]);
        if(l[cur]!=-1){
            q.push(l[cur]);
        }
        if(r[cur]!=-1){
            q.push(r[cur]);
        }
    }
}

int main(){
    input();
    BFS(1);
    for(int i=0;i<final.size();i++){
        if(i>0) cout<<" ";
        cout<<final[i];
    }
    return 0;
}
```

2.
# 二叉搜索树的基本操作

## 题目描述
请你实现一个二叉搜索树（BST），支持以下四种操作：

1. **建立**：给定一系列整数，依次插入到 BST 中，建立一棵二叉搜索树。
2. **检索**：查询某个值是否存在于 BST 中。
3. **插入**：将一个新的值插入 BST。
4. **删除**：删除 BST 中的某个值（若不存在则忽略）。

请在所有操作完成后，输出这棵 BST 的中序遍历结果。

## 输入格式
- 第一行：两个整数 `n m`，分别表示初始插入的元素数量和操作数量 (1 ≤ n, m ≤ 1000)。  
- 第二行：`n` 个互不相同的整数，表示依次插入 BST 的结点值。  
- 接下来 `m` 行，每行表示一次操作，格式如下：
  - `Q x`：检索值 `x` 是否在 BST 中（输出 `Found` 或 `Not Found`）。  
  - `I x`：向 BST 中插入值 `x`（若已存在则忽略）。  
  - `D x`：从 BST 中删除值 `x`（若不存在则忽略）。  

## 输出格式
- 对于每个检索操作 `Q`，输出一行 `Found` 或 `Not Found`。  
- 最后一行输出 BST 的中序遍历结果。

## 输入样例

```
5 5
4 2 6 1 3
Q 3
Q 5
I 5
D 2
Q 2
```

## 输出样例

```
Found
Not Found
Not Found
1 3 4 5 6
```


解：

```cpp
#include<iostream>
#include<vector>

using namespace std;

struct Node{
    int v;
    Node* l;
    Node* r;
    Node(int x):v(x),l(NULL),r(NULL){}
};

Node* insertBST(Node* root,int x){
    if(root==NULL) return new Node(x);
    if(x<root->v) root->l=insertBST(root->l,x);
    else if(x>root->v) root->r=insertBST(root->r,x);
    return root;
}

bool searchBST(Node* root,int x){
    if(root==NULL) return false;
    if(root->v==x) return true;
    else if(x<root->v) return searchBST(root->l,x);
    else return searchBST(root->r, x);
}

Node* findmin(Node* root){
    while(root->l!=NULL) root=root->l;
    return root;
}

Node* deleteBST(Node* root,int x){
    if(root==NULL) return NULL;
    if(x<root->v){
        root->l=deleteBST(root->l,x);
    }
    else if(x>root->v){
        root->r=deleteBST(root->r,x);
    }
    else{
        if(root->l==NULL&&root->r==NULL){
            delete root;
            return NULL;
        }
        else if(root->l==NULL){
            Node* tmp=root->r;
            delete root;
            return tmp;
        }
        else if(root->r==NULL){
            Node* tmp=root->l;
            delete root;
            return tmp;
        }
        else{
            Node* successor=findmin(root->r);
            root->v=successor->v;//用它右子树的最小结点（中序后继）替代它
            root->r=deleteBST(root->r,successor->v);//再在右子树中删除这个后继结点
        }
    }
    return root;
}

void inorder(Node* root,vector<int>& result){
    if(root==NULL) return;
    inorder(root->l,result);
    result.push_back(root->v);
    inorder(root->r,result);
}

int main(){
    int n,m;
    cin>>n>>m;
    Node* root=NULL;
    for(int i=0;i<n;i++){
        int x;
        cin>>x;
        root=insertBST(root,x);
    }
    for(int i=0;i<m;i++){
        char op;
        int x;
        cin>>op>>x;
        if(op=='Q'){
            if(searchBST(root,x)) cout<<"Found\n";
            else cout<<"Not Found\n";
        }
        else if(op=='I'){
            root=insertBST(root,x);
        }
        else if(op=='D'){
            root=deleteBST(root,x);
        }
    }
    vector<int> result;
    inorder(root,result);
    for(int i=0;i<result.size();i++){
        if(i) cout<<" ";
        cout<<result[i];
    }
    cout<<"\n";
    return 0;
}
```

3.


二·经典例题

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

2.
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



  