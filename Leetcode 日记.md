# Leetcode 日记

- 2020.11.25

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

- 2020.11.26