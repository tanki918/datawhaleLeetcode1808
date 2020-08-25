1. #35 SearchInsert

解题思路： 想到了二分法，这样可以减少一半的操作数量。

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0;
      	int right = n - 1;
      	int pos = n;
        while (left <= right) {
            int mid = ((right - left) >> 1) + left;
            if (target <= nums[mid]) {
                pos = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return pos;
    }
};

```



2. #202 HappyNumber

解题思路：首先需要考虑的问题是如何表示各个位 因为是十进制 可以通过除以10来获得某一位置上的数字。

：（ 太难了 写不出来。。。





3. #205 isomorphic string 

解题思路：不失一般性得，我们可以假设两个字符串长度相同，否则他们没有机会是同构的。

find(char) 返回char 第一次出现的位置。 如果位置不相同那么则说明两个字符串并不同构。

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) 
    {   
        for(int i=0;i<s.size();i++)
        {
          if( s.find(s[i])!=t.find(t[i])) 
          return false;
        }
        return true;
       
    }
};

```



4. #242 Valid Anagram

解题思路： 开始尝试利用substring比较 结果发现在__cxx11中并没有这个定义这个函数。参考了一下答案。



```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size() != t.size()) return false;
        int hash_s[26]={0};
        int hash_t[26]={0};
        for(auto i:s)
        {
            ++hash_s[i-'a'];
        }
        for(auto j:t)
        {
            ++hash_t[j-'a'];
        }
        for(int k=0;k<26;++k)
        {
            if(hash_s[k] != hash_t[k]) return false;
        }
        return true;
    }
};


```

