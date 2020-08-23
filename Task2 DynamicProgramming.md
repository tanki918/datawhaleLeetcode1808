1. #5 Longest Palindrome 

String 类 :

	- copy operation : str2 = str1; ( copy str1 to str2)
	- concatenation operation : str3 = str2 + str1; (str 3 is the resulting string which is concatenating str2 and str1)
	- length : str3.size();

2.#198 House Robber & 213 House Robber2

解题思路： 首先考虑边界条件就是 数组中只包含一个元素，和只包含两个元素的情况。 其次，我们的决策条件是 是否 nums[i-2]+nums[i] > nums[i-1].

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        if (size == 1) {
            return nums[0];
        }
        vector<int> dp = vector<int>(size, 0);
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[size - 1];
    }
};

```

#213  House Robber2

解题思路： 这个题跟上一道的区别就是在于位置为0 和 size-1的房子不能被同时偷，我们可以转化成两个子问题，比较结果 也就是 从0到n-2， 从1到n-1；

```cpp
class Solution {
public:

    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();

        if (size == 1) {
            return nums[0];
        }
        if (size == 2)
        {
            return max(nums[0],nums[1]);
        }
        if (size == 3)
        {
            return max(nums[0],max(nums[2],nums[1]));
        }
        
        return max(robHelper(nums,0,size-1),robHelper(nums,1,size));
    }
    
    int robHelper(vector<int>& nums, int startIndex, int endIndex){
        
        int size = nums.size();
        vector<int> dp = vector<int>(size,0);
        dp[0] = nums[startIndex];
        dp[1] = max(nums[startIndex],nums[startIndex+1]);
        dp[2] = max(dp[1],dp[0]+nums[startIndex+2]);
        for (int i=startIndex+2; i< endIndex; i++){
            dp[i] = max(dp[i-2]+nums[i], dp[i-1]);
        }
        return dp[size-2];
    }


};
    


```







6. #674  Longest Continuous Increasing Subsequence

解题思路：首先明确这个数组是一个unsorted的数组，那么就需要考虑这个最长连续子数组可能从数据的任意位置开始。我们还需要特意考虑一下数组为空的情况单列出来。代码如下：

```cpp
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {

        int n = nums.size();
        int maxNum =0 ;
        int count =1;

        if(n>=1)
        {
            for(int i=1; i<n; i++)
            {
                if(nums[i]> nums[i-1])
                    count= count +1;
             else {
                    if (count>=maxNum)
                    {
                      maxNum = count;
                 }
                    count = 1;
                    }
            }

            return max(maxNum,count);
        }

        else return 0;
            
        }
    
    int max(int a,int b){
        if(a>=b)
             return a;
        else
            return b;
    }
};


```

