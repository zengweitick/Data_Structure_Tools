# 刷LeetCode必备工具

## Content

[toc]



---

### 前言

> 有关于在刷Leetcode过程当中的各种参数讲解（看着有点头痛）

```c
/**
 * Return an array of arrays of size *returnSize.
 * The sizes of the arrays are returned as *returnColumnSizes array.
 * Note: Both returned array and *columnSizes array must be malloced, assume caller calls free().
 */
int** transpose(int** A, int ASize, int* AColSize, int* returnSize, int** returnColumnSizes){
    int col_len=ASize;
    int row_len=*AColSize;

    int **ret=(int **)malloc(sizeof(int *)*row_len);//返回值初始化
    for(int i=0;i<row_len;i++)
    {
        ret[i]=(int *)malloc(sizeof(int)*col_len);
    }
     //遍历赋值
    for(int i=0;i<row_len;i++)
    {
        for(int j=0;j<col_len;j++)
        {
            ret[i][j]=A[j][i];
        }
    }
    *returnSize=row_len;  //返回二维数组的行数
    *returnColumnSizes = (int *)malloc(sizeof(int) * row_len);
    for(int j=0;j<row_len;j++)
    {
       (*returnColumnSizes)[j]=col_len; //赋予每一行的数量
    }
    return ret;
}
//综上：
//1. 返回的二维数组先要以行数来分配整个二维空间的大小，其次在进行赋值的时候要为每一行分配空间以列数为大小。
//2. *returnSize: 返回二维数组的行数
//3. **returnColumnSizes：记录的是每一行元素的个数，因此先以行数大小来决定分配空间的大小。其次在进行赋值过程当中最好采用            (*returnColumnSizes)[j]形式来。
```



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

  

- 采用leetcode内部排序算法也行，不过先指定升序还是降序

  ```c
  //升序
  int cmp(const void *a, const void *b)
  {
      return *(int*)a-*(int*)b;
  }
  //降序
  int cmp(const void *a, const void *b)
  {
      return *(int*)b-*(int*)a;
  }
  //在程序当中使用
   qsort(array,count,sizeof(int),cmp);
  //array:要排序的数组
  //count:数组大小
  //sizeof(int):单个元素大小
  //cmp:指定的排序方式
  //例子如下：
  //-------------------------------------------------------------------------------------------------------
  void intoArray(struct ListNode* head,int *array,int *count)
  {
      struct ListNode* node=head;
      while(node!=NULL)
      {
         array[*count]=node->val;
         *count=*count+1;
         node=node->next;
      }
  }
  int cmp(const void *a, const void *b)
  {
      return *(int*)a-*(int*)b;
  }
  void intoListNode(struct ListNode *ret,int *array,int len)
  {
      struct ListNode* p=ret;
      
      for(int i=0;i<len;i++)
      {
          p->val=array[i];
          p=p->next;
      }
  }
  struct ListNode* sortList(struct ListNode* head)
  {
      if(head==NULL||head->next==NULL)return head;
       int array[50000]={0};
      if(array==NULL)return NULL;
      
      int count=0;
      intoArray(head,array,&count);
      qsort(array,count,sizeof(int),cmp);
      intoListNode(head,array,count);
      return  head;
  }
  
  ```

  

- 归并排序

  > 思路：
  > 1. 将数组分解成单个元素
  > 2. 两两合并，然后相邻的四个合并，以此递增直到整个数组
  >
  > 算法复杂度分析
  >
  > 1. 平均时间复杂度：O(nlogn)
  > 2. 最佳时间复杂度：O(n)
  > 3. 最差时间复杂度：O(nlogn)
  > 4. 空间复杂度：O(n)
  > 5. 排序方式：In-place
  > 6. 稳定性：稳定

  ```c
  //递归版本---好理解
  int *b=(int *)malloc(sizeof(int)*(n+1));        //辅助数组
  void merge(int a[],int low,int mid,int high)    //看着参数就知道需要调用递归实现[low,mid]和[mid+1,high]各自数组都是有序的
  {
     for(int k=low;k<=high;k++)
     {
         b[k]=a[k]       //将a[low,high]范围内的数复制到b当中
     }
     for(int i=low ,int j=mid+1,k=i;i<mid && j<=high;k++)
     {
     //将较小的值复制到a当中去
         if(b[i]<b[j])
           a[k]=b[i++];   
         else
           a[k]=b[j++];
     }
     while(i<=mid)a[k++]=b[i++];   //若第一个数组比较完之后还剩下数，则直接复制进去
     while(j<=high)a[k++]=b[j++];  //同理
  }
  
  
  
  //调用函数
  void MergeSort(int a[],int low,int high)
  {
      if(low<high)
      {
          int mid=(low+high)/2;
          //进行递归划分
          MergeSort(a,low,mid);
          MergeSort(a,mid+1,high);
          //进行比较
          merge(a,low,mid,high);
      }
  }
  
  
  // 归并排序（C-迭代版）------不好理解
  int min(int x, int y) {
      return x < y ? x : y;
  }//返回最小值
  void merge_sort(int arr[], int len) {
      int* a = arr;
      int* b = (int*) malloc(len * sizeof(int));
      int seg, start;
      for (seg = 1; seg < len; seg += seg) { //粒度1，2，4，8，.....
          for (start = 0; start < len; start += seg + seg) {
              int low = start, mid = min(start + seg, len), high = min(start + seg + seg, len);
              int k = low;
              int start1 = low, end1 = mid;
              int start2 = mid, end2 = high;
              while (start1 < end1 && start2 < end2)
                  b[k++] = a[start1] < a[start2] ? a[start1++] : a[start2++];
              while (start1 < end1)
                  b[k++] = a[start1++];
              while (start2 < end2)
                  b[k++] = a[start2++];
          }
          int* temp = a;
          a = b;
          b = temp;
      }
      if (a != arr) {
          int i;
          for (i = 0; i < len; i++)
              b[i] = a[i];
          b = a;
      }
      free(b);
  }
  ```

- 

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

  ```c
  //给定一棵树求这棵树的树高
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




### 3.8 BFS && DFS

#### 3.8.1 BFS



#### 3.8.2 DFS







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

## 7. 哈希表

来自[博客](https://blog.csdn.net/whatday/article/details/95926766)

- 结构

  ```c
  #include"uthash.h"  
  struct my_struct {  
      int id;                    /* key */  
      char name[10];             /* value */  
      UT_hash_handle hh;         /* makes this structure hashable */  
  };  
  //hh是内部使用的hash处理句柄，在使用过程中，只需要在结构体中定义一个UT_hash_handle类型的变量即可，不需要为该句柄变量赋值，但必须在该结构体中定义该变量。
  ```

- 操作

  - 查找

    ```c
    struct my_struct *find_user(int ikey) {  
    struct my_struct *s;  
    HASH_FIND_INT(g_users, &ikey, s );  
    return s;  
    }
    //查找是利用宏来实现的，先声明hashTable变量来存储查找结果。
    //第一个参数：查找的hash表
    //第二个参数：投入查找的键值
    //第三个参数：刚刚声明的结构体，原来存放结果。
    ```

  - 添加

    ```c
    void add_user(int ikey, char *value_buf) {  
        struct my_struct *s;  
        HASH_FIND_INT(g_users, &ikey, s);  /* 插入前先查看key值是否已经在hash表g_users里面了 */  
        if (s==NULL) {  
          s = (struct my_struct*)malloc(sizeof(struct my_struct));  
          s->ikey = ikey;  
          HASH_ADD_INT(g_users, ikey, s );  /* 这里必须明确告诉插入函数，自己定义的hash结构体中键变量的名字 */  
        }  
        strcpy(s-> value, value_buf);  
    }  
    //同理，添加操作也是通过宏来实现
    //由于uthash要求键（key）必须唯一，而uthash内部未对key值得唯一性进行很好的处理，因此它要求外部在插入操作时要确保其key值不在当前的hash表中，这就需要，在插入操作时，先查找hash表看其值是否已经存在，不存在在时再进行插入操作，在这里需要特别注意以下两点：
    
    ```

    

  - 删除

    ```c
    void delete_user(int ikey) {  
        struct my_struct *s = NULL;  
        HASH_FIND_INT(g_users, &ikey, s);  
        if (s!=NULL) {  
          HASH_DEL(g_users, s);   
          free(s);              
        }  
    }  
    ```

  - 清空

    ```c
    void delete_all() {  
      struct my_struct *current_user, *tmp;  
      HASH_ITER(hh, users, current_user, tmp) {  
      HASH_DEL(g_users,current_user);    
    free(current_user);              
      }  
    }  
    
    //或者这里需要注意：uthash内部提供了另外一个清空函数:
    HASH_CLEAR(hh, g_users);
    ```

  - **统计hash表中的已经存在的元素数**

    ```c
    unsigned int num_users;  
    num_users = HASH_COUNT(g_users);  
    printf("there are %u items\n", num_users);  
    ```

- 注意
  -  在定义hash结构体时不要忘记定义**UT_hash_handle**的变量
  -  需确保key值唯一，如果插入key-value对时，key值已经存在，再插入的时候就会出错。
  - **不同的key值，其增加和查找调用的接口函数不一样，具体可见第4节。一般情况下，不通类型的key，其插入和查找接口函数是不一样的，删除、遍历、元素统计接口是通用的，特殊情况下，字符数组和字符串作为key值时，其插入接口函数不一样，但是查找接口是一样的。**

- 完整例子

  ```c
  //key类型为int的完整的例子
  #include <stdio.h>   /* gets */  
  #include <stdlib.h>  /* atoi, malloc */  
  #include <string.h>  /* strcpy */  
  #include "uthash.h"  
    
  struct my_struct {  
      int ikey;                    /* key */  
      char value[10];  
      UT_hash_handle hh;         /* makes this structure hashable */  
  };  
    
  static struct my_struct *g_users = NULL;  
    
  void add_user(int mykey, char *value) {  
      struct my_struct *s;  
    
      HASH_FIND_INT(users, &mykey, s);  /* mykey already in the hash? */  
      if (s==NULL) {  
        s = (struct my_struct*)malloc(sizeof(struct my_struct));  
        s->ikey = mykey;  
        HASH_ADD_INT( users, ikey, s );  /* ikey: name of key field */  
      }  
      strcpy(s->value, value);  
  }  
    
  struct my_struct *find_user(int mykey) {  
      struct my_struct *s;  
    
      HASH_FIND_INT( users, &mykey, s );  /* s: output pointer */  
      return s;  
  }  
    
  void delete_user(struct my_struct *user) {  
      HASH_DEL( users, user);  /* user: pointer to deletee */  
      free(user);  
  }  
    
  void delete_all() {  
    struct my_struct *current_user, *tmp;  
    
    HASH_ITER(hh, users, current_user, tmp) {  
      HASH_DEL(users,current_user);  /* delete it (users advances to next) */  
      free(current_user);            /* free it */  
    }  
  }  
    
  void print_users() {  
      struct my_struct *s;  
    
      for(s=users; s != NULL; s=(struct my_struct*)(s->hh.next)) {  
          printf("user ikey %d: value %s\n", s->ikey, s->value);  
      }  
  }  
    
  int name_sort(struct my_struct *a, struct my_struct *b) {  
      return strcmp(a->value,b->value);  
  }  
    
  int id_sort(struct my_struct *a, struct my_struct *b) {  
      return (a->ikey - b->ikey);  
  }  
    
  void sort_by_name() {  
      HASH_SORT(users, name_sort);  
  }  
    
  void sort_by_id() {  
      HASH_SORT(users, id_sort);  
  }  
    
  int main(int argc, char *argv[]) {  
      char in[10];  
      int ikey=1, running=1;  
      struct my_struct *s;  
      unsigned num_users;  
    
      while (running) {  
          printf(" 1. add user\n");  
          printf(" 2. add/rename user by id\n");  
          printf(" 3. find user\n");  
          printf(" 4. delete user\n");  
          printf(" 5. delete all users\n");  
          printf(" 6. sort items by name\n");  
          printf(" 7. sort items by id\n");  
          printf(" 8. print users\n");  
          printf(" 9. count users\n");  
          printf("10. quit\n");  
          gets(in);  
          switch(atoi(in)) {  
              case 1:  
                  printf("name?\n");  
                  add_user(ikey++, gets(in));  
                  break;  
              case 2:  
                  printf("id?\n");  
                  gets(in); ikey = atoi(in);  
                  printf("name?\n");  
                  add_user(ikey, gets(in));  
                  break;  
              case 3:  
                  printf("id?\n");  
                  s = find_user(atoi(gets(in)));  
                  printf("user: %s\n", s ? s->value : "unknown");  
                  break;  
              case 4:  
                  printf("id?\n");  
                  s = find_user(atoi(gets(in)));  
                  if (s) delete_user(s);  
                  else printf("id unknown\n");  
                  break;  
              case 5:  
                  delete_all();  
                  break;  
              case 6:  
                  sort_by_name();  
                  break;  
              case 7:  
                  sort_by_id();  
                  break;  
              case 8:  
                  print_users();  
                  break;  
              case 9:  
                  num_users=HASH_COUNT(users);  
                  printf("there are %u users\n", num_users);  
                  break;  
              case 10:  
                  running=0;  
                  break;  
          }  
      }  
    
      delete_all();  /* free any structures */  
      return 0;  
  }  
  ```

  ```c
  //key类型为字符数组的完整的例子
  #include <string.h>  /* strcpy */  
  #include <stdlib.h>  /* malloc */  
  #include <stdio.h>   /* printf */  
  #include "uthash.h"  
    
  struct my_struct {  
      char name[10];             /* key (string is WITHIN the structure) */  
      int id;  
      UT_hash_handle hh;         /* makes this structure hashable */  
  };  
    
    
  int main(int argc, char *argv[]) {  
      const char **n, *names[] = { "joe", "bob", "betty", NULL };  
      struct my_struct *s, *tmp, *users = NULL;  
      int i=0;  
    
      for (n = names; *n != NULL; n++) {  
          s = (struct my_struct*)malloc(sizeof(struct my_struct));  
          strncpy(s->name, *n,10);  
          s->id = i++;  
          HASH_ADD_STR( users, name, s );  
      }  
    
      HASH_FIND_STR( users, "betty", s);  
      if (s) printf("betty's id is %d\n", s->id);  
    
      /* free the hash table contents */  
      HASH_ITER(hh, users, s, tmp) {  
        HASH_DEL(users, s);  
        free(s);  
      }  
      return 0;  
  }  
  ```
  
  ```c
  //key类型为字符指针的完整的例子
  #include <string.h>  /* strcpy */  
  #include <stdlib.h>  /* malloc */  
  #include <stdio.h>   /* printf */  
  #include "uthash.h"  
    
  struct my_struct {  
      const char *name;          /* key */  
      int id;  
      UT_hash_handle hh;         /* makes this structure hashable */  
  };  
    
    
  int main(int argc, char *argv[]) {  
      const char **n, *names[] = { "joe", "bob", "betty", NULL };  
      struct my_struct *s, *tmp, *users = NULL;  
      int i=0;  
    
      for (n = names; *n != NULL; n++) {  
          s = (struct my_struct*)malloc(sizeof(struct my_struct));  
          s->name = *n;  
          s->id = i++;  
          HASH_ADD_KEYPTR( hh, users, s->name, strlen(s->name), s );  
      }  
    
      HASH_FIND_STR( users, "betty", s);  
      if (s) printf("betty's id is %d\n", s->id);  
    
      /* free the hash table contents */  
      HASH_ITER(hh, users, s, tmp) {  
        HASH_DEL(users, s);  
        free(s);  
      }  
      return 0;  
  }  
  ```
  
  

## 技巧

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

  

- 利用二分法与位运算计算完全二叉树的节点个数（年轻人要讲武德系列）

  - [题目描述](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

  - 实现方法

    - 暴力法

      ```c
      /*
      执行用时: 32 ms
      内存消耗: 16.4 MB
      */
      int countNodes(struct TreeNode* root)
      {
          if(root==NULL)return 0;
         return countNodes(root->left)+countNodes(root->right)+1;
      }
      ```

    - DFS

      ```c
      /*
      执行用时: 24 ms
      内存消耗: 16.6 MB
      */
      void InOrder(struct TreeNode *node,int *count);
      int countNodes(struct TreeNode* root)
      {
          int count=0;
          InOrder(root,&count);
           return count;
      }
      void InOrder(struct TreeNode *node,int *count)
      {
        if(node==NULL)return;
         if(node->left!=NULL)
         {
             InOrder(node->left,count);
         }
           *count=*count+1;
          if(node->right!=NULL)
          {
              InOrder(node->right,count);
          }
      }
      
      ```

      

    - 二分法+位运算

      ```c
      /*
      执行用时: 20 ms
      内存消耗: 16.5 MB
      */
      bool exists(struct TreeNode* root, int level, int k) {
          int bits = 1 << (level - 1);
          struct TreeNode* node = root;
          while (node != NULL && bits > 0) {
              if (!(bits & k)) {
                  node = node->left;
              } else {
                  node = node->right;
              }
              bits >>= 1;
          }
          return node != NULL;
      }
      
      int countNodes(struct TreeNode* root) {
          if (root == NULL) {
              return 0;
          }
          int level = 0;
          struct TreeNode* node = root;
          while (node->left != NULL) {
              level++;
              node = node->left;
          }
          int low = 1 << level, high = (1 << (level + 1)) - 1;
          while (low < high) {
              int mid = (high - low + 1) / 2 + low;
              if (exists(root, level, mid)) {
                  low = mid;
              } else {
                  high = mid - 1;
              }
          }
          return low;
      }
      ```

- 


## 题型

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

### 组合数学问题

[题目](https://leetcode-cn.com/problems/count-sorted-vowel-strings/)

- 题目分析

```c++
这个问题的数学解法，可以把问题转换成将 n 个小球放到 5 个盒子里，盒子可以为空。

我们可以想象成把 n 个字符分配给五个元音所代表的盒子中。一旦每个盒子中的字符个数定了，那么这个字符串也固定下来了。因为题目要求必须是字典序，所以一定 a 字符在最前；其次是 e 字符；其次是 i 字符；其次是 o 字符；其次是 u 字符。
下面问题的关键就是，n 个小球放到 5 个盒子里，盒子可以为空，一共有多少种方法？
------------------------------------------------------------------------------------------------------------
这是经典的中学数学问题。更一般的，我们来探讨，将 n 个小球放到 m 个盒子里，有多少种方法？
首先，我们考虑问题的简单版本，即盒子不能为空的情况。
此时，我们只需要在 n 个小球排成一排，中间放 m - 1 个隔板，放好以后，相当于把 n 个小球分成了 m 份。每一份对应一个盒子里的小球数量。
因为盒子不能为空，所以两个小球之间不可能放多个隔板，左右两端也不可能放隔板。因此，放隔板的位置有 n - 1 个，我们要放 m - 1 个隔板。答案为 C(n - 1, m - 1)。
有了这个结论，再来讨论问题的复杂版本，就简单了，即盒子可以为空的情况。
 
此时，我们只需要先拿 m 个新的小球，在 m 个盒子里，每个盒子中扔进去一个小球。之后，再分配原来的这 n 个小球，得到的分配结果，肯定 m 个盒子里都不为空。但此时，我们使用了 n + m 个小球。换句话说，把 n 个小球放到 m 个盒子里，盒子可以为空，等价于：把 n + m 个小球放到 m 个盒子里，盒子不能为空。大家也可以想成是：我们先把 n + m 个小球放到 m 个盒子里，盒子不能为空，然后再在每个盒子里拿走 1 个小球，总共拿走了 m 个小球，得到的结果，就是把 n 个小球放到 m 个盒子里，盒子可以为空的解。把 n + m 个小球放到 m 个盒子里，盒子不能为空的分法，带入上面的公式，就是 C(n + m - 1, m - 1),所以，把 n 个小球放到 m 个盒子里，盒子可以为空，答案为 C(n + m - 1, m - 1)。
//总结：
将 n 个小球放到 m 个盒子里，盒子不为空：C(n - 1, m - 1)；
将 n 个小球放到 m 个盒子里，盒子可以空：C(n + m - 1, m - 1)；
```

- 代码实现

  ```c
  int countVowelStrings(int n)
  {
     return (n+4)*(n+3)*(n+2)*(n+1)/24;
  }
  ```

### 回溯算法

- 算法核心

  1. 解空间

     当算法运行到某一步时，解空间是已知的。

  2. 回溯并搜索

     当走到某一步出现错误时，算法保存了上一步的结果，并能返回上一步。

- 算法步骤

  1. 针对所给问题，定义问题的**解空间**

  2. 确定易搜索的解空间结构

  3. 以**深度优先**方式搜索解空间，并在搜索过程中用剪枝函数避免无效搜索。

  两个常用的剪枝函数：

  1. 约束函数：在扩展结点中剪去不满足约束的子树

  2.  限界函数：剪去得不到最优解的子树

- 算法模板

  ```c++
  result = []
  def backtrack(路径, 选择列表):
      if 满足结束条件:
          result.add(路径)
          return
      for 选择 in 选择列表:
          做选择
          backtrack(路径, 选择列表)
          撤销选择
  ```

  ```c++
  //其核心就是 for 循环里面的递归，在递归调用之前「做选择」，在递归调用之后「撤销选择」，特别简单。我们定义的 backtrack 函数其实就像一个指针，在这棵树上游走，同时要正确维护每个节点的属性，每当走到树的底层，其「路径」就是一个全排列。
  ```

  

  

- 题型（已在leetcode写了解答）

  - [全排列I](https://leetcode-cn.com/problems/permutations/)
  - 全排列II



## 链表

### 基础练习题

1. [反链表II](https://leetcode-cn.com/problems/reverse-linked-list-ii/submissions/)

