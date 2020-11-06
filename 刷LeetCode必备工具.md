# 刷LeetCode必备工具

## Content

 * [刷LeetCode必备工具](#刷leetcode必备工具)
      * [1. 排序](#1-排序)
      * [2. 遍历](#2-遍历)
      * [3. 树](#3-树)
         * [3.1 树与子树问题](#31-树与子树问题)
         * [3.2 树的构造](#32-树的构造)
         * [3.3 求树高](#33-求树高)
         * [3.4  层次遍历(非递归实现)](#34--层次遍历非递归实现)
         * [3.5 求树宽](#35-求树宽)
         * [3.6 树的先序、中序、后序遍历算法](#36-树的先序中序后序遍历算法)
            * [<strong>3.6.1 递归实现</strong>](#361-递归实现)
            * [<strong>3.6.2 非递归实现</strong>](#362-非递归实现)
         * [3.7 N叉树前序遍历](#37-n叉树前序遍历)
      * [4. 递归](#4-递归)
      * [5. 队列](#5-队列)
         * [创建队列](#创建队列)
         * [队列操作](#队列操作)
      * [6. 栈](#6-栈)
         * [创建栈](#创建栈)
         * [栈的操作](#栈的操作)
      * [7. 技巧](#7-技巧)
      * [8. 题型](#8-题型)
         * [跳跃问题](#跳跃问题)

---



## 1. 排序

- 简单选择排序

  ```c
  void Sort(int *array,int len)//从大到小
  {
      int i=0;
      int j=0;
      for(i=0;i<len;i++)
      {
         int index=i;
         int temp=array[i];
        for(j=i;j<len;j++)
        {
             if(array[index]<array[j])
             {
              index=j;
             }
        }
        array[i]=array[index];
        array[index]=temp;
      }  
  }
  ```



## 2. 遍历

- 前序遍历

  ```C
  void preorder(struct TreeNode* root, int* ret, int* returnSize)
  {
      if (root) {
          ret[(*returnSize)++] = root->val;
          preorder(root->left, ret, returnSize);
          preorder(root->right, ret, returnSize);
      }
  }
  int* preorderTraversal(struct TreeNode* root, int* returnSize)
  {
      *returnSize = 0;
      int* ret = (int*)malloc(100 * sizeof(int));
      preorder(root, ret, returnSize);
      return ret;
  }
  ```
  
  

- 中序遍历

  ```c
  void InOrder(struct TreeNode *node,int *count,int *array)
  {
     if(node==NULL)return;
     if(node->left!=NULL)
     {
         InOrder(node->left,count,array);
     }
      array[*count]=node->val;
       *count=*count+1;
      if(node->right!=NULL)
      {
          InOrder(node->right,count,array);
      }
  }
  //node:树根
  //count:节点计数
  //array：将节点存入数组（可选择）
  ```

- 
## 3. 树
###  3.1 树与子树问题

子树相关的题基本是一个套路，重点在于先写出满足条件的子树的递归函数。然后对自身调用这个递归函数，并且对左右子树递归调用原问题函数。
比如[另一个树的子树问题](https://leetcode-cn.com/problems/subtree-of-another-tree/)，要先能够判断树是否相同，写出isSameTree递归函数，然后在原问题中，对自身调用isSameTree进行判断，并对左右子树递归调用原函数。
代码如下：

```java
class Solution {
public:
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if(s==NULL){
            return false;
        }

        return isSameTree(s, t) || isSubtree(s->left, t) || isSubtree(s->right, t);
    }

    bool isSameTree(TreeNode* s, TreeNode* t){
        if(s==NULL && t==NULL){
            return true;
        }
        if(s==NULL || t==NULL){
            return false;
        }
        if(s->val != t->val){
            return false;
        }

        return isSameTree(s->left, t->left) && isSameTree(s->right, t->right);
    }
};
```

再如树的[子结构问题](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/comments/)，分析出我们需要解决的子问题是判断一棵树能否完全包含另一棵树，因此写出isTreeContain函数。然后在原问题中，先判断自身是否满足包含目标树，如果不满足，则对左右子树递归调用原函数。
代码如下：

```java
class Solution {
public:
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if(A==nullptr || B==nullptr){
            return false;
        }

        if(isTreeContain(A, B)){
            return true;
        }

        return isSubStructure(A->left, B) || isSubStructure(A->right, B);
    }

    bool isTreeContain(TreeNode* p, TreeNode* q){
        if(p==nullptr || q==nullptr){
            return false;
        }
        if(p->val != q->val){
            return false;
        }
        if(q->left && q->right){
            return isTreeContain(p->left, q->left) && isTreeContain(p->right, q->right);
        } else if(q->left){
            return isTreeContain(p->left, q->left);
        } else if(q->right){
            return isTreeContain(p->right, q->right);
        }
        return true;
    }
};
```

以及还有一个和判断子树很像的[1367. 二叉树中的列表](https://leetcode-cn.com/problems/linked-list-in-binary-tree/)
把另一个树换成了链表，代码如下：

```java
class Solution {
public:
    bool isSubPath(ListNode* head, TreeNode* root) {
        if(head==nullptr){
            return true;
        }
        if(root==nullptr){
            return false;
        }
        
        return treeContainList(head, root) || isSubPath(head, root->left) || isSubPath(head, root->right);
    }

    //检查树中是否包含链表
    bool treeContainList(ListNode* head, TreeNode* root){
        if(head==nullptr){
            return true;
        }
        if(root==nullptr){
            return false;
        }

        if(head->val != root->val){
            return false;
        }

        return treeContainList(head->next, root->left) || treeContainList(head->next, root->right);
    }
};
```

这种子树相关的问题的套路，我称为递归子问题，首先要找出子问题，然后在原问题上使用子问题解决，并且递归解决原问题。
OK，让我们看本题，二叉树剪枝，看上去是需要修改树，和上面的两个问题不太一样，但是思路是类似的，因为也是子树相关的。
首先我们看修剪掉的子树的特点，是不包含1的子树，那么我们的子问题就是判断子树是否不包含1。这个函数应该可以直接写出：

```java
bool containNoOne(TreeNode* root){
        if(root==nullptr){
            return true;
        }
        if(root->val == 1){
            return false;
        }
        return containNoOne(root->left) && containNoOne(root->right);
    }
```

如果树为空则肯定满足不包含1，如果树的根的值为1则不满足不包含1，否则进一步检查左右子树，需要同时成立。
子问题解决后，我们看原问题中如何处理。首先对于当前的root节点，如果满足不包含1，则该节点作为根的子树需要修剪掉。
那么怎么修剪呢？所谓修剪就是将指向该子树根节点的指针置空，如果修剪的是原树的根节点，则直接返回NULL即可。
如果修剪的是子树的根节点，那么子树修剪后要将修剪的结果赋值给left或right，这是通过递归调用原问题后赋值给left或right实现的。
具体代码如下。

```java
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* pruneTree(TreeNode* root) {
        if(root==nullptr){
            return nullptr;
        }

        if(containNoOne(root)){
            return nullptr;
        }

        root->left = pruneTree(root->left);
        root->right = pruneTree(root->right);
        return root;
    }

    //树root是否不包含1
    bool containNoOne(TreeNode* root){
        if(root==nullptr){
            return true;
        }
        if(root->val == 1){
            return false;
        }
        return containNoOne(root->left) && containNoOne(root->right);
    }
};
```

- 树的后序（递归形式）

  ```c
  int count(struct TreeNode* root)
  {
      if(root==NULL)return 0;
      return 1+count(root->left)+count(root->right);
  }
  void AfterOrder(struct TreeNode* node,int *array,int *index)
  {
    if(node==NULL)return ;
    AfterOrder(node->left,array, index);
    AfterOrder(node->right,array,index);
    array[(*index)++]=node->val;
  }
  int* postorderTraversal(struct TreeNode* root, int* returnSize){
      
      int len=count(root);
      *returnSize=len;
      if(len==0)return NULL;
      int *array=(int *)malloc(sizeof(int)*len*2);
      int index=0;
      memset(array,0,sizeof(array));
      AfterOrder(root,array,&index);
   
      return array;
  }
  ```


### 3.2 树的构造

- 测试用例(给出树的构造)

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
typedef int ElementType;
typedef struct TNode* BinTree; /* 二叉树类型 */
struct TNode { /* 树结点定义 */
	ElementType Data; /* 结点数据 */
	BinTree Left;     /* 指向左子树 */
	BinTree Right;    /* 指向右子树 */
};
void InorderTraversal(BinTree BT)
{
	if (BT) {
		InorderTraversal(BT->Left);
		/* 此处假设对BT结点的访问就是打印数据 */
		printf("%d ", BT->Data); /* 假设数据为整型 */
		InorderTraversal(BT->Right);
	}
}

void PreorderTraversal(BinTree BT)
{
	if (BT) {
		printf("%d ", BT->Data);
		PreorderTraversal(BT->Left);
		PreorderTraversal(BT->Right);
	}
}

void PostorderTraversal(BinTree BT)
{
	if (BT) {
		PostorderTraversal(BT->Left);
		PostorderTraversal(BT->Right);
		printf("%d ", BT->Data);
	}
}

BinTree CreateTree()
{
	BinTree Bt = (BinTree)malloc(sizeof(struct TNode));
	BinTree Bt1 = (BinTree)malloc(sizeof(struct TNode));
	BinTree Bt2 = (BinTree)malloc(sizeof(struct TNode));
	BinTree Bt3 = (BinTree)malloc(sizeof(struct TNode));
	BinTree Bt4 = (BinTree)malloc(sizeof(struct TNode));
	Bt->Data = 1;
	Bt1->Data = 2;
	Bt2->Data = 3;
	Bt3->Data = 4;
	Bt4->Data = 5;
	Bt->Left = Bt1;
	Bt->Right = Bt2;
	Bt1->Left = NULL;
	Bt1->Right = NULL;
	Bt2->Left = Bt3;
	Bt2->Right = Bt4;
	Bt3->Left = NULL;
	Bt3->Right = NULL;
	Bt4->Left = NULL;
	Bt4->Right = NULL;
	return Bt;
}
int main()
{
	BinTree head = CreateTree();
	printf("\nInorder:");
	InorderTraversal(head);
	printf("\nAfterorder:");
	PostorderTraversal(head);
	printf("\nPreorder:");
	PreorderTraversal(head);
	printf("\n");
	return 0;
}
```



###  3.3 求树高

- 问题描述

  ```
  给定一棵树求这棵树的树高
  ```

- 解决方案

  - 递归方案

    ```C
    #include<stdio.h>
    #include<stdlib.h>
    #include<string.h>
    typedef int ElementType;
    typedef struct TNode* BinTree; /* 二叉树类型 */
    struct TNode { /* 树结点定义 */
    	ElementType Data; /* 结点数据 */
    	BinTree Left;     /* 指向左子树 */
    	BinTree Right;    /* 指向右子树 */
    };
    BinTree CreateTree()     //创建二叉树
    {
    	BinTree Bt = (BinTree)malloc(sizeof(struct TNode));
    	BinTree Bt1 = (BinTree)malloc(sizeof(struct TNode));
    	BinTree Bt2 = (BinTree)malloc(sizeof(struct TNode));
    	BinTree Bt3 = (BinTree)malloc(sizeof(struct TNode));
    	BinTree Bt4 = (BinTree)malloc(sizeof(struct TNode));
    	Bt->Data = 1;
    	Bt1->Data = 2;
    	Bt2->Data = 3;
    	Bt3->Data = 4;
    	Bt4->Data = 5;
    	Bt->Left = Bt1;
    	Bt->Right = Bt2;
    	Bt1->Left = NULL;
    	Bt1->Right = NULL;
    	Bt2->Left = Bt3;
    	Bt2->Right = Bt4;
    	Bt3->Left = NULL;
    	Bt3->Right = NULL;
    	Bt4->Left = NULL;
    	Bt4->Right = NULL;
    	return Bt;
    }
    //求树高
    int GetDepth(BinTree root)
    {
    	int depthleft = 0;
    	int depthright = 0;
    	int depthcount = 0;
    	if (root == NULL) {
    		depthcount = 0;
    		return depthcount;
    	}
    	//求左子树的高度
    	depthleft = GetDepth(root->Left);
    	//求右子树的高度
    	depthright = GetDepth(root->Right);
    	//统计
    	depthcount = 1 + ((depthleft > depthright) ? depthleft : depthright);
    	return depthcount;
    }
    int main()
    {
        BinTree head = CreateTree();
    	int heaf = GetDepth(head);
    	printf("树的高度是：%d",heaf);
    	return 0; 
    }
    //运行结果是：树高为3
    ```

    

### 3.4  层次遍历(非递归实现)

- 问题描述：[Leetcode原题](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

  ```
  从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。
  ```

- 解决方案

  ```c
  int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes)
  {
      if(!root)
      {
          *returnSize=0;
               return 0;
      }
      int **res=(int **)malloc(sizeof(int)*2000);
      *returnColumnSizes=(int *)malloc(sizeof(int)*2000);
      int i=0;
      struct TreeNode *p=root;
      struct TreeNode *queue[2000];   //设置循环队列
      int rear=0;
      int front=0;
      queue[rear++]=p;
  
      while(rear!=front)
      {
          //设置每一行的大小
          int size=rear-front;
          res[i]=(int *)malloc(sizeof(int)*size);
          (*returnColumnSizes)[i]=size;
          //遍历每一层
          for(int j=0;j<size;j++)  //for循环保证遍历完整的一层
          {
            //出队
            p=queue[front];
            front=(front+1)%2000;
            //入队
            if(p->left)
            {
                queue[rear]=p->left;
                rear=(rear+1)%2000;
            }
             if(p->right)
            {
                queue[rear]=p->right;
                rear=(rear+1)%2000;
            }
            res[i][j]=p->val;
          }
          ++i;        //层数加1
      }
      *returnSize=i;  //记录总层数即二维数组的行数
      return res;
  }
  ```


### 3.5 求树宽

- **问题描述**

假设二叉树采用二叉链表存储结构，设计一个算法，求非空二叉树b的宽度(即具有节点数最多的那一层的节点数)

- **分析**

很明显采用层次遍历算法合适，采用队列+栈的方法可以实现，其中队列用来保存本层所有节点，栈用来计算下一层节点数

- **算法实现**

**方法一(队列+标志域法)**

```c
# define MAXSIZE   10000 
typedef
{
    BigTree data[MAXSIZE];   //保存队列中的节点指针
    int level[MAXSIZE];
    int rear;
    int front;
}Queue;
int BTWidth(BigTree b)
{
    BigTree p;
    int k,max,i,n;      //k做标记，方便后面的遍历
    Queue Qu;
    Qu.rear=Qu.front=-1;
    Qu.rear++;
    Qu.data[Qu.rear]=b;
    Qu.level[Qu.rear]=1;//将根节点设为第一层
    while(Qu.front<Qu.rear)
    {
        Qu.front++;
        p=Qu.data[Qu.front];    //出队
        k=Qu.level[Qu.front];   //出队节点的层次
        if(p->lchild!=NULL)
        {
            Qu.rear++;
            Qu.data[Qu.rear]=p->lchild;
            Qu.level[Qu.rear]=k+1;
        }
        if(p->rchild!=NULL)
        {
            Qu.rear++;
            Qu.data[Qu.rear]=p->rchild;
            Qu.level[Qu.rear]=k+1;
        }
        
    }
    max=0;i=1;   //保存最多的节点数
    k=1;         //表示从第1层开始寻找
    while(i<=Qu.rear)
    {
        n=0;
        while(i<Qu.rear && Qu.level[i]==k)
        {
            n++;
            i++;
        }
        k=Qu.level[i];
        if(n>max)max=n;
    }
    return max;
}
```

**方法二（队列+栈）**

```c
#define MAXSIZE  10000
typedef struct 
{
    BigTree data[MAXSIZE];
    int rear;
    int front;
}Qu;

typedef struct 
{
    int top;
    BigTree point[MAXSIZE];
}Stack;
//出栈
BigTree Pop(Stack *s)
{
    return s->point[s->top--];
}
//入栈
void  Push(Stack *s, BigTree p)
{
    s->top++;
    s->point[s->top]=p;
}
int BTWidth(BigTree b)
{
    BigTree p;
    Qu queue;
    queue.rear=queue.front=-1;
    queue.rear=0;
    queue.data[queue.rear]=b; //根节点入列
    Stack * stack;
    stack->top=-1;
    int max=0;
    int n=0;
    while(queue.rear==queue.front)
    {
        while(queue.rear !=queue.front)
        {
           queue.front++;           //出队计数
           p=queue.data[queue.front];
           Push(stack,p);
           n++;
        }
        if(n>max)max=n;
        n=0;
        BigTree q;
        while(stack->top!=-1)
         {
         q=Pop(stack);
         if(q->lchild !=NULL)
           {
             queue.data[queue.rear]=q->lchild;
             //根节点入列
             queue.rear++;
           }
          if(q->rchild !=NULL)
           {
             queue.data[queue.rear]=q->rchild;
             //根节点入列
             queue.rear++;
           }    
         }
    }
}




```

### 3.6 树的先序、中序、后序遍历算法

#### **3.6.1 递归实现**

**1. 先序遍历**

```c
void PreVisit(BigTree *b)
{
    if(b!=NULL)
    {
    visit(b)
    PreVisit(b->lchild);
    PreVisit(b->rchild);
    }
}
```

**2. 中序遍历**

```c
void PreVisit(BigTree *b)
{
    if(b!=NULL)
    {
    PreVisit(b->lchild);
    visit(b)
    PreVisit(b->rchild);
    }
}c
```

**3. 后序遍历**

```c
void PreVisit(BigTree *b)
{
    if(b!=NULL)
    {
    PreVisit(b->lchild);
    PreVisit(b->rchild);
    visit(b)
    }
}
```



#### **3.6.2 非递归实现**

- 分析：

  非递归实现需要用到栈来存储上一次经过的节点
  ==以中序遍历为例：== 先扫描(而非访问)根节点的所有左节点并将他们一一进栈，然后出栈一个节点p(显然节点p没有左孩子或者左孩子已经被访问过了)，并访问他。然后扫描该节点的右孩子节点，并将其进栈，再扫描该右孩子节点的所有左节点并一一进栈，如此继续，直到栈空为止

**1. 前序遍历**

```c
void PreVisit(BigTree *b)
{
    InitStack(S);
    BigTree p=b;
    while(p || !isEmpty(S))
    {
        if(p)
        {
            Push(S,p);
            Visit(p);
            p=p->lchild;
        }
        else
        {
            Pop(S,p);
            p=p->rchild;
        }
    }
}
```

**2. 中序遍历**
(注意:和递归遍历的顺序差异几乎一样)

```c
void InVisit(BigTree *b)
{
    InitStack(S);
    BigTree p=b;
    while(p || !isEmpty(S))
    {
    //先走到最左边
        if(p)
        {
            Push(S,p);
            p=p->lchild;
        }
        //开始访问
        else
        {
            Pop(S,p);
            Visit(p);
            p=p->rchild;
        }
    }
}
```

**3. 后序遍历**

1. 通过增加一个标志域flag

[CSDN分析](https://blog.csdn.net/Benja_K/article/details/88389039)

```c
void PostVisit(BigTree *b)
{
    int size=sizof(b);
    int count=0;
    InitStack(S);
    int flag[size];
    BigTree p=b;
    while(p || !isEmpty(S))
    {
    if(p)
     {
      Push(S,p);
      flag[count++]=1;
      p=p->lchild;
     }
    else
     {
     if(flag[count]==1)
      {
         p=GetTop(S,p);
         flag[count]==2;
         p=p->rchild;
      }
      else
      {
          Pop(S,p);
          Visit(p);
          p=NULL;
      }
     }
    }
    
}

```

2. 或通过增加辅助指针r

```c
void PostOrder(Bigtree bt)
{
    InitStack(S);
    r=NULL;  //记录最近访问的节点
    while(p || ! isEmpty(S)
    {
      if(p)
      {
      push(S,p);
      p=p->lchild;//先走到最左边
      }
      else
      {
          Gettop(S,p);
          if(p->rchild && p->rchild != r)
          {
              p=p->rchild;
              push(S,p);
              p=p->lchild;
          }
          else
          {
              pop(S,p);
              Visit(p->data);
              r=p;
              p=NULL;//使得不进入if而直接进入else
          }
      }
    }
}
```





**后序遍历对应的实操C语言代码**

- **题目描述**:

在二叉树中查找值为x的节点，试编写代码打印值为x的节点的所有祖先，假设值为x的节点不多于一个。

- **分析**：

有之前分析可知，需要用到二叉树的后序非递归遍历，当遍历到节点x时栈中的节点即为x的祖先打印输出。实现的时候可以对照以上对后序遍历的标志域法可得到
对于后序遍历需弄清楚节点是从哪回退回来。

- **算法实现**：

```c
typedef struct{
    BigTree t;
    int tag;
}stack;
//tag=0表示访问左孩子，tag=1表示访问右孩子
void Postsearch(BigTree *b,int x)
{
    stack s[];
    int top=0;
    while(b!=NULL || top>0)
 {
    while(b!=NULL && b->data!=x) //节点入栈
    {
        s[++top]=b;
        s[top].tag=0;
        b=b->lchild;
    }
    if(b->data==x)
    {
        printf("打印所有祖先节点：\n");
        for(int i=1;i<=top;i++)
        {
            printf("%d",s[i].t->data);
        }
        exit(1);
    }
    while(top!=0 && s[top].tag==1)
    {
        top--;   //退栈
    }
    if(top!=0)            //沿右支向下遍历
    {
        s[top].tag=1;
        b=s[top].t->rchild;
    }
  }
}
```





### 3.7 N叉树前序遍历

- 问题描述

  给定一个N叉树返回其对应的前序遍历序列数组

- 代码实现

  - 递归实现

  ```c++
  #define MAX_SIZE 10240
  void visit(struct Node* root, int* result, int* returnSize) {
    if (root)
      for (int i = 0; i < root->numChildren; i++) {
        result[(*returnSize)++] = root->children[i]->val;
        visit(root->children[i], result, returnSize);
      }
  }
  int* preorder(struct Node* root, int* returnSize) {
    int i, *result = (int*)calloc(MAX_SIZE, sizeof(int));
    *returnSize = 0;
    if (root == NULL) return result;
    result[(*returnSize)++] = root->val;
    visit(root, result, returnSize);
    return result;
  }
  ```

  



## 4. 递归

- 典例：

  ---

  

  请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

  例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

  ​    1

     / \
    2   2
   / \ / \
  3  4 4  3
  但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

  ​    1

     / \
    2   2
     \   \
     3    3

  来源：力扣（LeetCode）
  链接：https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof
  著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

  ---

  

- 思想

  1. 递归的函数要干什么？
     - 函数的作用是判断传入的两个树是否镜像。
     - 输入：TreeNode left, TreeNode right
     - 输出：是：true，不是：false
  2. 递归停止的条件是什么？
     - 左节点和右节点都为空 -> 到底了且都长得一样 ->true
     - 左节点为空的时候右节点不为空，或反之 -> 长得不一样-> false
     - 左右节点值不相等 -> 长得不一样 -> false
  3. 从某层到下一层的关系是什么？
     - 要想两棵树镜像，那么一棵树左边的左边要和二棵树右边的右边镜像，一棵树左边的右边要和二棵树右边的左边镜像
     - 调用递归函数传入左左和右右
     - 调用递归函数传入左右和右左
     - ==只有左左和右右镜像且左右和右左镜像的时候，我们才能说这两棵树是镜像的==
  4. 调用递归函数，我们想知道它的左右孩子是否镜像，传入的值是root的左孩子和右孩子。这之前记得判个root==null。

- 解决方案

  ```c
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     struct TreeNode *left;
   *     struct TreeNode *right;
   * };
   */
  
  bool DFS(struct TreeNode* left,struct TreeNode* right)
  {
      if(left==NULL && right==NULL)return true;  //判断1
      if(left==NULL || right==NULL)return false; //判断2
      if(left->val!=right->val)return  false;    //判断3
      return DFS(left->left,right->right) && DFS(left->right,right->left);//递归条件：左左-右右、左右-右左
  }
  bool isSymmetric(struct TreeNode* root){
    if(root==NULL)return true;
    return DFS(root->left,root->right);
  }
  ```




## 5. 队列

### 创建队列

- 定义

  ```C
  typedef struct {
      struct QelemType *treeNode[MAX_SIZE];
      int front;
      int rear;
  } Queue;
  //QelemType为你需要的类型
  ```

- 约定

  空队列 Q.front=Q.rear
  队满 Q.front=(Q.rear+1)%MAXSIZE
  入队 Q.rear++
  出队 Q.front++
  非空队列：front始终指向队头元素，rear始终指向队尾元素的下一位置
  每插入一个元素，rear=（rear+1）%（MAXSIZE）
  每删除一个元素，front=（front+1）%（MAXSIZE）

### 队列操作

- 初始化

  ```c++
  Queue *InitQueue()
  {
      Queue *q = (Queue *)malloc(sizeof(Queue));
      q->front = 0;
      q->rear = 0;
      return q;
  }
  ```

- 入队

  ```c++
  void EnQueue(Queue *q, struct QelemType *treeNode)
  {
      if ((q->rear + 1) % MAX_SIZE == q->front) {
          return;
      }
      q->treeNode[q->rear] = treeNode;
      q->rear = (q->rear + 1) % MAX_SIZE;
      return;
  }
  ```

- 出队

  ```c++
  void DeQueue(Queue *q, struct QelemType **treeNode)
  {
      if (q->rear == q->front) {
          return;
      }
      *treeNode = q->treeNode[q->front];
      q->front = (q->front + 1) % MAX_SIZE;
      return;
  }
  ```

- 队长度

  ```c++
  int GetQueueSize(Queue *q)
  {
      if (q == NULL) {
          return 0;
      }
      return q->rear - q->front;
  }	
  ```

- 判空

  ```c
  bool IsEmptyQueue(Queue *q)
  {
      if (q->rear == q->front) {
          return true;
      }
      return false;
  }
  ```

  

- 应用

  [^题目来源]: https://leetcode-cn.com/problems/list-of-depth-lcci/submissions/

  **问题描述**

  给定一棵二叉树，设计一个算法，创建含有某一深度上所有节点的链表（比如，若一棵树的深度为 `D`，则会创建出 `D` 个链表）。返回一个包含所有深度的链表的数组。

  ```
  输入：[1,2,3,4,5,null,7,8]
  
          1
         /  \ 
        2    3
       / \    \ 
      4   5    7
     /
    8
  
  输出：[[1],[2,3],[4,5,7],[8]]
  ```

  **实现代码**

  ```c
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     struct TreeNode *left;
   *     struct TreeNode *right;
   * };
   */
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     struct ListNode *next;
   * };
   */
  
  #define MAX_SIZE 100
  typedef struct {
      struct TreeNode *treeNode[MAX_SIZE];
      int front;
      int rear;
  } Queue;
  
  Queue *InitQueue()
  {
      Queue *q = (Queue *)malloc(sizeof(Queue));
    q->front = 0;
      q->rear = 0;
      return q;
  }
  
  void EnQueue(Queue *q, struct TreeNode *treeNode)
  {
      if ((q->rear + 1) % MAX_SIZE == q->front) {
          return;
      }
      q->treeNode[q->rear] = treeNode;
      q->rear = (q->rear + 1) % MAX_SIZE;
      return;
  }
  
  void DeQueue(Queue *q, struct TreeNode **treeNode)
  {
      if (q->rear == q->front) {
          return;
      } 
      *treeNode = q->treeNode[q->front];
      q->front = (q->front + 1) % MAX_SIZE;
      return;
  }
  
  int GetQueueSize(Queue *q)
  {
      if (q == NULL) {
          return 0;
      }
      return q->rear - q->front;
  }
  
  bool IsEmptyQueue(Queue *q)
  {
      if (q->rear == q->front) {
          return true;
      }
      return false;
  }
  struct ListNode** listOfDepth(struct TreeNode* tree, int* returnSize)
  {
      int i;
      int qSize;
      struct TreeNode* treeNode = NULL;
      struct ListNode* listNode = NULL;
      struct ListNode* head = NULL;
      struct ListNode* newNode = NULL;
      Queue *q = InitQueue();
      struct ListNode** result = (struct ListNode**)malloc(sizeof(struct ListNode*) * MAX_SIZE);
      *returnSize = 0;
      if (tree == NULL) {
          return result;
      }
      EnQueue(q, tree);
      while (!IsEmptyQueue(q)) {
          qSize = GetQueueSize(q);
          listNode = (struct ListNode *)malloc(sizeof(struct ListNode));
          listNode->next = NULL;
          head = listNode;
          for (i = 0; i < qSize; i++) {
             DeQueue(q, &treeNode);
             newNode = (struct ListNode *)malloc(sizeof(struct ListNode));
             newNode->val = treeNode->val;
             newNode->next = NULL;
             listNode->next = newNode;
             listNode = newNode;
              if (treeNode->left != NULL) {
                 EnQueue(q, treeNode->left);
              }
              if (treeNode->right != NULL) {
                 EnQueue(q, treeNode->right);
              }
         }
          result[(*returnSize)] = head->next;
          (*returnSize)++;
      }
      return result;
  }
  ```

```c
#define MAX_SIZE 100
typedef struct {
    struct TreeNode *treeNode[MAX_SIZE];
    int front;
    int rear;
} Queue;
Queue *InitQueue()
{
    Queue *q = (Queue *)malloc(sizeof(Queue));
    q->front = 0;
    q->rear = 0;
    return q;
}

void EnQueue(Queue *q, struct TreeNode *treeNode)
{
    if ((q->rear + 1) % MAX_SIZE == q->front) {
        return;
    }
    q->treeNode[q->rear] = treeNode;
    q->rear = (q->rear + 1) % MAX_SIZE;
    return;
}

void DeQueue(Queue *q, struct TreeNode **treeNode)
{
    if (q->rear == q->front) {
        return;
    } 
    *treeNode = q->treeNode[q->front];
    q->front = (q->front + 1) % MAX_SIZE;
    return;
}

int GetQueueSize(Queue *q)
{
    if (q == NULL) {
        return 0;
    }
    return q->rear - q->front;
}

bool IsEmptyQueue(Queue *q)
{
    if (q->rear == q->front) {
        return true;
    }
    return false;
}
int** levelOrder(struct TreeNode* root, int* returnSize, int** returnColumnSizes)
{
    int **ret=(int **)malloc(sizeof(int*)*MAX_SIZE);
    int i,qSize=0;
    struct TreeNode *treeNode=(struct TreeNode *)malloc(sizeof(struct TreeNode));
    *returnColumnSizes=(int*)malloc(sizeof(int*)*MAX_SIZE);   //指针的指针初始化方式
    int **ret=(int **)malloc(sizeof(int *)*MAX_SIZE);         //结果数组
    *returnSize=0;
     if ( root == NULL) {
        return result;
    }
    Queue *q = InitQueue();
    EnQueue(q,treeNode);
    while(!IsEmptyQueue(q))
    {
        qSize=GetQueueSize(q);
        *returnColumnSizes[*returnSize]=qSize;
        for(i=0;i<qSize;i++)
        {
            DeQueue(q,treeNode);
            ret[*returnSize][i]=treeNode->val;


            if (treeNode->left != NULL) {
               EnQueue(q, treeNode->left);
            }
            if (treeNode->right != NULL) {
               EnQueue(q, treeNode->right);
            }
        }
         (*returnSize)++;
    }
    return ret;
}
```



## 6. 栈

### 创建栈

```c
typedef struct node
{
    int * base;
    int * top;
    int stacksize;
}Sqstack;
void InitStack(Sqstack *S);
void Push(Sqstack *S,int elem);
int  Pop(Sqstack *S);
```

### 栈的操作

```c
void InitStack(Sqstack *S) //初始化栈
{
    S->base = (int *)malloc(sizeof(int)*maxsize);//给栈分配数组空间
    if(!S->base)
        exit(0);
    S->top=S->base;
    S->stacksize=maxsize;
}
void Push(Sqstack *S,int elem)//入栈
{

    *S->top++=elem;  //元素先入栈，指针后加一

}
int Pop(Sqstack *S)
{
    int elem ;
    elem=*--S->top; //栈顶指针先减一，再将栈顶元素出栈
    return elem;
}
//这里使用指针来返回函数值更为方便
/*void Pop(Sqstack *S,int*elem)
{
    *elem=*--S->top; //栈顶指针先减一，再将栈顶元素出栈
}
*/
```



## 7. 技巧

- 二进制计算

  - 题目描述

    ```java
    给你两个二进制字符串，返回它们的和（用二进制表示）。
    输入为 非空 字符串且只包含数字 1 和 0。
    //注意
    //每个字符串仅由字符 '0' 或 '1' 组成。
    //1 <= a.length, b.length <= 10^4
    //字符串如果不是 "0" ，就都不含前导零。
    ```

  - 解法

    ```java
    //字符转数字，方便进位计算
    
    
    char * addBinary(char * a, char * b){
        int alen=strlen(a);
        int blen=strlen(b);
        int binary=0;
        int len;
        len=alen>blen?alen:blen;
        int anum,bnum,sum;
        char* s=(char*) malloc ((len+2) * sizeof(char));
        s[len+1]='\0';
        for(--alen,--blen;len>=0;alen--,blen--,len--){
            if(alen<0)
            anum=0;
            else
            anum=a[alen]-'0';
            if(blen<0)
            bnum=0;
            else
            bnum=b[blen]-'0';    //字符转数字
            sum=anum+bnum+binary; 
            binary=sum/2;        //技巧：进位计算方法
            s[len]='0'+sum%2;    //二进制和的计算
        }
        if(s[0]=='0')
        return &s[1];
        else return &s[0];
    }
    
    ```

  - 反思

    ```java
    //对于N进制的计算方法：\
    //1. 进位计算
    // sum=anum+bnum+binary;
    //binary=sum/N;
    //2. 当前位值计算
    //number=sum%N;
    ```

    

- 数组去重

  ```c
  void swap(int *a, int *b)
  {
      if (a == b)
          return;
  	*a = *a ^ *b;
  	*b = *a ^ *b;
  	*a = *a ^ *b;
      return;
  }
  void deduplicate(int *nums, int *numsSize)
  {
      int i, j, len = *numsSize;
      for (i = 0, j = 1; j < len; j++)
      {
          if (nums[j] == nums[i])
              *numsSize = *numsSize - 1;
          else
          {
              swap(nums+i+1, nums+j);
              i++;
          }        
      }
      return;
  }
  ```

- [原题](https://leetcode-cn.com/problems/longest-well-performing-interval/)（该题可以抽象为给定一个序列，求序列当中满足条件X的子序列最长长度M）

  ```c
  
  
  #define MAX(a,b) a>b?a:b
  #define MAX_LEN 10001
  int longestWPI(int* hours, int hoursSize){
      int high = 0;
      int low = 0;
      int num = 0;
      int max = 0;
  
      if (hoursSize == 0) {
          return 0;
      }
  
      for (int i = 0; i < hoursSize; i++) {
          if (hours[i] > 8) {
              hours[i] = 1;
          } else {
              hours[i] = -1;
          }
      }
      int sum[MAX_LEN] = {0};
      sum[0] = hours[0];
  
      for (int i = 1; i < hoursSize; i++) {
          sum[i] = hours[i] + sum[i-1];
      }
  
       for (int i = 0; i < hoursSize; i++) {
           for (int j = 0; j <= i; j++) {
               if (sum[i] - sum[j] + hours[j] > 0) {
                   max = MAX(max, i-j+1);
                   break;
               }
           }
       }
  
      return max;
  }
  ```

  

- 


## 8. 题型

### 跳跃问题

**题目描述**

给定一个非负整数数组，你最初位于数组的第一个位置。数组中的每个元素代表你在该位置可以跳跃的最大长度,判断你是否能够到达最后一个位置。

```
示例 1:
输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。

示例 2:
输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
 
```

**题目分析**

从位置0 开始，**num[0]**值代表我们能跳跃最远的距离，最远的位置往左的所有位置我们也可以达到。基于上述分析，我们不断更新最远的距离，如果最后的最远距离恰好是数组的最后一个位置或者比最后一个位置更远表示我们可以到达最后一个位置，唯一判断的是如果不能达到我们如何判断？如果我们当前到达的最远的距离比我们遍历到达的位置还小，则表示我们无法达到最后一个位置。

**代码**

```c
int max(int a,int b)
{
    return a>b? a:b;
}
bool canJump(int* nums, int numsSize)
{
    int k=0;
    for(int i=0;i<numsSize;i++)
    {
        if(i>k)return false;//判断当前位置与遍历位置大小
        k=max(k,i+nums[i]); //更新最远距离
    }
    return true;
}
```







