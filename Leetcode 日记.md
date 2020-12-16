# Leetcode 日记

## 2020.11.25

- [每日一题](https://leetcode-cn.com/problems/increasing-decreasing-string/)

- 分析

  通过题目描述，简单来说就是从字符串当中找出字典排序形成的在最长“山脉”，每一轮找出剩余s中的最长“山”，依次添加在结果集之后，直到剩余s中不存在元素。因此，可以先给s字符串排序，从左到右找到山脉的最大上升序列，然后从右往左找到山脉的最大降序序列。此时由于字符串可以重复，而且字符串大小可以通过游标来表示，因此最佳排序方法是[桶排序](https://www.runoob.com/w3cnote/bucket-sort.html)

- 实现

  ```c
  char * sortString(char * s)
  {
        int nums[26];
        memset(nums,0,sizeof(nums));
        int len=strlen(s);//字符串的长度
        char* ret=(char *)malloc(sizeof(char)*(len+1));
        int count=0;
        for(int i=0;i<len;i++)
        {
            nums[s[i]-'a']++;
        }
      while(count<len)
        {
          for(int j=0;j<26;j++)    //顺序加入
           {
            if(nums[j])
            {
                  ret[count++]= j+'a';
                  nums[j]--;
            }
           }
  
          for(int m=25;m>=0;m--)
           {
            if(nums[m])
            {
                ret[count++]= m+'a';
                nums[m]--;
            }
           }
        }
        ret[count]=0;
        return ret;
  
  }
  ```



## 2020.11.27

- [每日一题](https://leetcode-cn.com/problems/4sum-ii/)

- 主题：哈希表

- 哈希表

  - 作用：**快速**判断一个元素是否在**集合**里
  - 关键字：键值对、哈希函数
  - [具体讲解](https://blog.csdn.net/whatday/article/details/95926766)

- 题目思路

  对于此题，由于直接暴力法时间复杂度太大，因此自然而然想到用哈希表去做。具体做法如下：

  1. 将A，B数组利用二重循环得到其两两之和的数组，以此作为哈希表的集合，其中key存放和（sum），value存放该和出现的次数。
  2. 定义count变量统计结果
  3. 同理，将C和D数组利用两重循环计算和，但是该和不存入空间， 加入该和为-sum则取负值在map中搜索取负之后的值在map中出现的次数，利用count进行统计。
  4. 返回结果

- 代码实现

  ```c
  struct hashTable
  {
      int key;   //键
      int value;  //值
      UT_hash_handle hh;
  };
  int fourSumCount(int* A, int ASize, int* B, int BSize, int* C, int CSize, int* D, int DSize){
    struct hashTable *hashtable=NULL;
    for(int i=0;i<ASize;i++)
    {
        for(int j=0;j<BSize;j++)
        {
            int ikey=A[i]+B[j];
            struct hashTable *temp;
            HASH_FIND_INT(hashtable,&ikey,temp);
            if(temp==NULL)
            {
                struct hashTable *temp=malloc(sizeof(struct hashTable));
                temp->key=ikey;
                temp->value=1;
                HASH_ADD_INT(hashtable,key,temp);  //判断键值对是否在map当中，如果不在则加入。
            }else{
                temp->value++;
            }
        }
    }
    int count=0;
    for(int i=0;i<CSize;i++)
    {
        for(int j=0;j<DSize;j++)
        {
           int ikey=-(C[i]+D[j]);
           struct hashTable *tmp;
           HASH_FIND_INT(hashtable,&ikey,tmp);   //判判断是否在map当中，如果在则将count加上对应的value值。
           if(tmp!=NULL)count+=tmp->value;
        }
    }
    return count;
  }
  ```



## 2020.11.28

- [每日一题](https://leetcode-cn.com/problems/reverse-pairs/)

- 主题：归并排序

- 归并排序

  [详解](https://zhuanlan.zhihu.com/p/124356219)

- 题目思路

  > 1. 想要更快地统计翻转对，就要去掉一些不必要的判断。
  >
  > 2. 找到一对翻转对 i、ji、j，如果因此能保证一些 “pair” 也一定是翻转对 
  >
  > 3. nums[i]>2*nums[j]，比nums[i]还大的数，且在nums[j]左边，就一定是翻转对
  >
  > 4. 而且这些数的数目，是要方便统计的。
  >
  > 想到两个升序的序列，nums[i]在左序列，nums[j]在右序列
  >
  > 1. 如果恰好满足：nums[i] > 2*nums[j]，则有：
  >
  >    左序列中nums[i]右侧的数，必然能和nums[j]组成翻转对，并且个数好统计。
  >
  >    并且，左序列的左侧（数小），右序列 j 的左侧（数小），j 的右侧（不满足 i < j），都不存在目标nums[j]
  >
  > 2. 归并排序，会不断二分，二分到不能二分，就有了有序的序列（单个元素有序），然后开始合并。
  >
  >    我们恰恰需要这个有序的左右序列：为右序列的 j找左序列的 i，统计翻转对。
  >
  >    对于nums[j]，它先是从规模小的左序列找 ii ，随着递归出栈，在规模更大的左序列中找 ii，随着递归结束，它把左边所有的可能都考察了。
  >
  >    因为每次都二分，找到了，就不用考察右侧，节省了很多判断。
  >
  > 翻转时机
  >
  > ​      是在『得到左右有序序列』之后，『合并左右有序序列』之前。
  >
  > 如何计数？
  >
  >      i、j 指针分别扫描左右序列，为当前的 nums[j] 寻找 nums[i]。
  > 找到了，那 i 到左序列的末尾，都能和nums[j]构成翻转对，通过索引之差统计个数。
  >
  > ```c
  > i := start   // i 指向左序列的开头
  > j := mid + 1 // j 指向右序列的开头
  > for i <= mid && j <= end { // i扫描左序列，j扫描右序列
  >     if nums[i] > 2*nums[j] { 
  >         count += mid - i + 1 // i到mid之间，都能和j构成翻转对
  >         j++                  // 考察下一个j，为它找i
  >     } else { // 考察下一个i，寻求和j构成翻转对
  >         i++
  >     }
  > }
  > ```
  >
  >  

- 实现代码

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
  ```

  



## 2020.12.2

- [每日一题](https://leetcode-cn.com/problems/create-maximum-number/)

- 主题：单调栈

- 单调栈

  >**单调栈就是栈里面存放的数据都是有序的，所以可以分为单调递增栈和单调递减栈两种。**
  >
  >单调递增栈：数据**出栈**的序列为单调递增序列
  >
  >单调递减栈：数据**出栈**的序列为单调递减序列

- 练手题

  - [最大矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

- 题目思路

  >为了找到长度为 kk 的最大数，需要从两个数组中分别选出最大的子序列，这两个子序列的长度之和为 k，然后将这两个子序列合并得到最大数。两个子序列的长度最小为 0，最大不能超过 k 且不能超过对应的数组长度。
  >
  >令数组 nums1的长度为 m,数组nums2的长度为 n，则需要从数组nums1中选出长度为 x 的子序列，以及从数组 nums2中选出长度为 y的子序列，其中 x+y=k，且满足0≤x≤m 和0≤y≤n。需要遍历所有可能的 x 和 y 的值，对于每一组 x 和 y 的值，得到最大数。在整个过程中维护可以通过拼接得到的最大数。
  >
  >对于每一组 xx 和 yy 的值，得到最大数的过程分成两步，第一步是分别从两个数组中得到指定长度的最大子序列，第二步是将两个最大子序列合并。
  >
  >第一步可以通过单调栈实现。单调栈满足从栈底到栈顶的元素单调递减，从左到右遍历数组，遍历过程中维护单调栈内的元素，需要保证遍历结束之后单调栈内的元素个数等于指定的最大子序列的长度。遍历结束之后，将从栈底到栈顶的元素依次拼接，即得到最大子序列。
  >
  >第二步需要自定义比较方法。首先比较两个子序列的当前元素，如果两个当前元素不同，则选其中较大的元素作为下一个合并的元素，否则需要比较后面的所有元素才能决定选哪个元素作为下一个合并的元素。
  >

- 实现代码

  ```c
  int compare(int* subseq1, int subseq1Size, int index1, int* subseq2, int subseq2Size, int index2) {
      while (index1 < subseq1Size && index2 < subseq2Size) {
          int difference = subseq1[index1] - subseq2[index2];
          if (difference != 0) 
          {
              return difference;
          }
          index1++;
          index2++;
      }
      return (subseq1Size - index1) - (subseq2Size - index2);
  }
  
  int* merge(int* subseq1, int subseq1Size, int* subseq2, int subseq2Size) {
      if (subseq1Size == 0) {
          return subseq2;
      }
      if (subseq2Size == 0) {
          return subseq1;
      }
      int mergeLength = subseq1Size + subseq2Size;
      int* merged = malloc(sizeof(int) * (subseq1Size + subseq2Size));
      int index1 = 0, index2 = 0;
      for (int i = 0; i < mergeLength; i++) {
          if (compare(subseq1, subseq1Size, index1, subseq2, subseq2Size, index2) > 0) {
              merged[i] = subseq1[index1++];
          } else {
              merged[i] = subseq2[index2++];
          }
      }
      return merged;
  }
  
  int* MaxSubsequence(int* nums, int numsSize, int k) {
      int* stack = malloc(sizeof(int) * k);
      memset(stack, 0, sizeof(int) * k);
      int top = -1;
      int remain = numsSize - k;
      for (int i = 0; i < numsSize; i++) {
          int num = nums[i];
          while (top >= 0 && stack[top] < num && remain > 0) {
              top--;
              remain--;
          }
          if (top < k - 1) {
              stack[++top] = num;
          } else {
              remain--;
          }
      }
      return stack;
  }
  
  void swap(int** a, int** b) {
      int* tmp = *a;
      *a = *b, *b = tmp;
  }
  
  int* maxNumber(int* nums1, int nums1Size, int* nums2, int nums2Size, int k, int* returnSize) {
      int* maxSubsequence = malloc(sizeof(int) * k);
      memset(maxSubsequence, 0, sizeof(int) * k);
      *returnSize = k;
      int start = fmax(0, k - nums2Size), end = fmin(k, nums1Size);
      for (int i = start; i <= end; i++) {
          int* subseq1 = MaxSubsequence(nums1, nums1Size, i);
          int* subseq2 = MaxSubsequence(nums2, nums2Size, k - i);
          int* curMaxSubsequence = merge(subseq1, i, subseq2, k - i);
          if (compare(curMaxSubsequence, k, 0, maxSubsequence, k, 0) > 0) {
              swap(&curMaxSubsequence, &maxSubsequence);
          }
      }
      return maxSubsequence;
  }
  ```




## 2020.12.3

- [每日一题](https://leetcode-cn.com/problems/count-primes/submissions/)

- 主题：质数个数的判断

- 方法

  - 方法一：**埃氏筛**

    基于这样的事实：如果x是质数，2x、3x、4x、......都是质数。

    因此我们所要做的就是先设立一个数组来记录所有数是否是质数，并遍历该数组。当从小到大遍历到数 x 时，倘若它是合数，则它一定是某个小于 x 的质数 y 的整数倍，故根据此方法的步骤，我们在遍历到 y 时，就一定会在此时将 x标isPrime[x]=0。因此，这种方法也不会将合数标记为质数。当然这里还可以继续优化，对于一个质数 x，如果按上文说的我们从 2x开始标记其实是冗余的，应该直接从 x⋅x 开始标记，因为 2x,3x,… 这些数一定在 x之前就被其他数的倍数标记过了，例如 2的所有倍数，3的所有倍数等。

  - 方法二：**线性筛**

    暂时不讲

- 实现

  ```c
  int countPrimes(int n){
     if(n<2)
     {
         return 0;
     }
     int isPrime[n];//标记是否合数
     int ret=0;
     memset(isPrime,0,sizeof(isPrime));
     for(int i=2;i<n;i++)
     {
         if(!isPrime[i])
         {
             ret++;
               if((long long)i*i<n){
                   for(int j=i*i;j<n;j+=i)
                   {
                       isPrime[j]=1;
                   }
               }
         }
     }
   return ret;
  }
  ```


## 2020.12.8

- [每日一题](https://leetcode-cn.com/problems/split-array-into-fibonacci-sequence/submissions/)

- 主题

  [回溯法](https://blog.csdn.net/weiyuefei/article/details/79316653)

- 回溯法

  > 简述：
  >
  > ​       把问题的解空间转化成了图或者树的结构表示，然后使用深度优先搜索策略进行遍历，遍历的过程中记录和寻找所有可行解或者最  优解。基本思想类同于：
  >
  > - 图的深度优先搜索
  > - 二叉树的后序遍历
  >
  > 详细描述：
  >
  > ​        回溯法按深度优先策略搜索问题的解空间树。首先从根节点出发搜索解空间树，当算法搜索至解空间树的某一节点时，先利用**剪枝函数**判断该节点是否可行（即能得到问题的解）。如果不可行，则跳过对该节点为根的子树的搜索，逐层向其祖先节点回溯；否则，进入该子树，继续按深度优先策略搜索。
  >
  > ​        回溯法的基本行为是搜索，搜索过程使用剪枝函数来为了避免无效的搜索。剪枝函数包括两类：1. 使用约束函数，剪去不满足约束条件的路径；2.使用限界函数，剪去不能得到最优解的路径。
  >
  > ​        问题的关键在于如何定义问题的解空间，转化成树（即解空间树）。解空间树分为两种：子集树和排列树。两种在算法结构和思路上大体相同。

  ​    

- 回溯法的解题思路

  >

- 实现思路

- 实现代码

  ```c
  /**
   * Note: The returned array must be malloced, assume caller calls free().
   */
  bool backtrack(int* list, int* listSize, char* S, int length, int index, long long sum, int prev) {
      if (index == length) 
      {
          return (*listSize) >= 3;
      }
      long long curr = 0;
      for (int i = index; i < length; i++) {
          if (i > index && S[index] == '0') {
              break;
          }
          curr = curr * 10 + S[i] - '0';
          if (curr > INT_MAX) {
              break;
          }
          if ((*listSize) >= 2) {
              if (curr < sum) {
                  continue;
              } else if (curr > sum) {
                  break;
              }
          }
          list[(*listSize)++] = curr;
          if (backtrack(list, listSize, S, length, i + 1, prev + curr, curr)) {
              return true;
          }
          (*listSize)--;
      }
      return false;
  }
  int* splitIntoFibonacci(char* S, int* returnSize) {
      int n = strlen(S);
      int* list = malloc(sizeof(int) * n);
      *returnSize = 0;
      backtrack(list, returnSize, S, strlen(S), 0, 0, 0);
      return list;
  }
  ```

  

  

## 2020.12.16

- [每日一题](https://leetcode-cn.com/problems/word-pattern/comments/)

- 哈希表

- 实现思路

  > 根据题意，建立一个双向哈希表：string_to_char  以及char_to_string，然后遍历s数组，找到矛盾，如果存在矛盾则返回错误。
  >
  > **注意：**以后遇见哈希表有关得问题以及字符相关的问题，优先考虑C++，因为C语言在这两方面做起来太复杂

- 实现代码、

  ```c++
  class Solution {
  public:
      bool wordPattern(string pattern, string str) {
          unordered_map<string, char> str2ch;
          unordered_map<char, string> ch2str;
          int m = str.length();
          int i = 0;
          for (auto ch : pattern) {
              if (i >= m) {
                  return false;
              }
              int j = i;
              while (j < m && str[j] != ' ') j++;
              const string &tmp = str.substr(i, j - i);
              if (str2ch.count(tmp) && str2ch[tmp] != ch) {
                  return false;
              }
              if (ch2str.count(ch) && ch2str[ch] != tmp) {
                  return false;
              }
              str2ch[tmp] = ch;
              ch2str[ch] = tmp;
              i = j + 1;
          }
          return i >= m;
      }
  };
  ```

  



## 2020.12.17







