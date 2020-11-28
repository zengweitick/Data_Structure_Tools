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



## 2020.11.29

