---
layout:     post
title:      leetcode 1155 - Number of Dice Rolls With Target Sum
subtitle:   动态规划学习笔记
date:       2019-08-21
author:     yangwf
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - leetcode
    - DP

---
# leetcode 1155 : Number of Dice Rolls With Target Sum

# Description

You have `d` dice, and each die has `f` faces numbered `1, 2, ..., f`.

Return the number of possible ways (out of $f^d$ total ways) **modulo 10^9 + 7** to roll the dice so the sum of the face up numbers equals `target`.

# Solution

- 特殊情况
  - d * f < target 时，无解
  - d * f = target 时，唯一解

- 一般性分析

  - 只有一个骰子时，解是平凡的
  - 两个骰子时，固定一个骰子，考虑另一个
  - 三个骰子时，固定第三个骰子，考虑另外两个骰子和为剩余和的数目
  - ......

  由上可知，当有n个骰子时，我们固定第n个骰子的点数 k ，然后问题转化为计算剩下n-1个骰子点数和为target - k 情况。显然这是一个**递归** OR **动态规划**的问题。下面用DP的方式求解。

- DP求解

  依次考虑骰子为 1～d 的情况，在考虑骰子个数为 i 时，依次考虑点数总和为 1～target 的情况。具体地，在考虑 i 个骰子点数和为 j 的组合方式时，要依次分析第 i 个骰子点数为 k ，剩下 i-1个骰子点数和为 j-k 的数目。需要注意的是，我们要保证 k 在 [1, f] 区间内。显然算法核心部分有三层循环，首先初始化骰子为1的情况，接下来依次计算即可。代码如下：

  ```c++
  class Solution {
  public:
      int numRollsToTarget(int d, int f, int target) {
          if(d*f < target)
              return 0;
          if(d*f == target)
              return 1;
          long long res[35][1010];
          memset(res, 0, sizeof(res));
          for(int i = 1; i <= f; ++i)
              res[1][i] = 1;
          for(int i = 2; i <= d; ++i)
              for(int j = 1; j <= target; ++j)
                  for(int k = 1; k <= f; ++k)
                      if(j - k > 0)
                          res[i][j] += res[i-1][j-k]%(1000000007);
          return res[d][target]%(1000000007);
      }
  };
  ```

- 算法优化

  上述算法时间复杂度为O(d * target * f)，其实是还可以优化的。主要是在第三层循环的优化，可以看出相邻两个 j 在累加 res[i-1\][j-k] 时就会有重复，有重复工作的地方就会有优化。具体来讲，在计算 res[i\][j] 时，需要累加res[i-1\][j-f]到res[i-1\][j-1]。因此，当递增 j 时，只需较 j-1 增加 res[i-1\][j-1] 再减去一个res[i-1\][j-f-1]即可。代码如下：

  ```c++
  class Solution {
  public:
      int numRollsToTarget(int d, int f, int target) {
          if(d*f < target)
              return 0;
          if(d*f == target)
              return 1;
          long long res[35][1010];
          memset(res, 0, sizeof(res));
          for(int i = 1; i <= f; ++i)
              res[1][i] = 1;
          for(int i = 2; i <= d; ++i){
              long long sum = 0;
              for(int j = 1; j <= target; ++j){
                  if(j > f)
                      sum += (res[i-1][j-1] - res[i-1][j-f-1]);
                  else
                      sum += res[i-1][j-1];
                  res[i][j] = sum%(1000000007);
              }           
          }
          return res[d][target]%(1000000007);
      }
  };
  ```

  以上，时间复杂度优化为O(d * target)，另外注意数据类型，如果sum设置为 int 会因数据过大而出错，还有就是取模，否则long long也无法保存。

  另外，空间复杂度也是可以优化的。因为计算骰子个数为 d 时，只需要用到 d-1 时的数据，所以只需两个一维数据即可，在这里就不具体coding了。

# Summary

- 特殊情况要首先考虑，这可以节省很多时间
- 代码优化方面k在取值 [1,j] 和 [1,f] 时不一样的，注意转换思维
- 第三层循环的优化值得学习
- 练习清晰的DP思维











