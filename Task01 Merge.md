1. #50  Pow(x,n) 

解题思路：首先考虑power是否为负，先对数据进行预处理一下，如果 n<0 ，我们要取x的倒数进行运算。（参考了一下答案感觉如果直接对结果去相反数会减少计算的复杂性）对数据处理后，开始构造递归。首先确定递归出口 也就是 power =1 的时候 直接输出base，之后 我们将问题分为两个子问题，比较简单就是根据奇偶性进行划分，对于偶次幂就不停二分，对于奇次幂，就想办法转化到偶次幂的情况（可以通过再乘以一次底数，或者除以一次底数） 我选择的办法是 除以一次底数 拆分为 n-1 和 1 。



编程过程中遇到的error：

1. runtime error: negation of -2147483648 cannot be represented in type 'int'; cast to an unsigned type to negate this value to itself

2. uint32_t** is a numeric type that guarantees 32 bits, the value is unsigned, **meaning** that the range of values goes from 0 to 2ˆ32 - 1.

   

3. Addresssanitizer: AddressSanitizer: stack-overflow on address 0x7ffdb06faff8 (pc 0x00000038d87c bp 0x7ffdb06fb010 sp 0x7ffdb06fb000 T0)

导致第三个错误的代码如下：

```cpp
class Solution {
public:

    double myPow(double x, int n) {

    if (n == 0 || x == 1.00000)
        return 1;

    else if(n<0)
    {
        long N = abs(n);
        return 1./myPow(x, N);
    }

    if (n == 1)
        return x;
    else {
        if (n%2 == 0) {
                double temp = myPow(x, n/2);
                return temp*temp;
            }
        else {
            double temp =myPow(x,(n-1)/2);
            return temp*temp*x;
        }
    }    

    }

```

对于第三个bug，参考了stackoverflow上的问答 感觉好像是递归太深了，所以就又定义了一个函数来帮忙。

```java
class Solution {
public:

    double intermediate(double x, uint32_t n)
    {
    if (n == 1)
        return x;

    else if (n%2 == 0)
        {
            double temp = intermediate(x,n/2);
            return temp*temp;
        }

        else {
            double temp = intermediate(x, (n-1)/2);
            return temp*temp*x;
        }
    }



    double myPow(double x, int n) {

    if (n == 0 || x == 1.00000)
        return 1;

    else if(n<0)
    {
        long N = abs(n);
        return 1./intermediate(x, N);
    }
    return intermediate(x,n);

    }

};

```



以下是学习笔记：

Brute-force  :  O(N)

首先需要处理n为负的情况， 把N变成long 是防止负数溢出的常见手段。(第一次写的时候问题也出在这里，其次进行转化。

Merge ：O(logN)

定义递归出口 根据奇偶性进行递归，要使用memorizer ，要处理边界情况，要考虑负数，double 数是否越界的情况。

要考虑 double 数溢出 负数越界

通过求解这题发现了几个问题：

就是 注意负数的问题，以及if statement超过两句必须用括号（可能太久没写过代码了，看到报错google搜了好久也没搜到，这个错误改到崩溃，后来才反应过来），以及unit32_t 的用法。



2. #53 Maximum Subarray

解题思路：第一个想到的就是brute-force， 但是尝试写了一下困绕于label不知道该怎么存，写得过于复杂，思考了一下时间复杂度大概是 O(nˆ2) 并不能达到题目要求。于是尝试将问题分解为更好解决的子问题，进而进行递归求解。将问题拆解成子问题并不会影响算法的正确性是因为全局最优也一定是局部最优。 

首先我们从中间开始将序列 分成左右两个子序列。分别求两个子序的最大子序和，同时考虑到构成最大子序和的子序列可能同时由左右两部分的元素构成。 

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
    
    int n = nums.size();
    int low = 0;
    int high = n-1;
    int maxSum = findMaxSubArray(nums,low,high);

    return maxSum;

    }

    int findMaxSubArray(vector<int>& nums, int low, int high){

        int left=0;
        int right=0;
        int cross =0;

        if ( low == high)
        {
            return nums[low];
        }
        else {     
            int mid = (low + high)>>1 ; 
    
            left = findMaxSubArray(nums,low,mid);
            right = findMaxSubArray(nums,mid+1,high);
            cross = findMaxCrossingSubArray(nums,low,mid,high);

            return max(cross,right,left);
            /*
            if(left>= right && left>=cross)
                return left;
            else { if (right >= left && right >= cross)
                    return right;
                else{
                    return cross;
                }

            }*/
        }

    
    }

    int findMaxCrossingSubArray(vector<int>& nums, int low, int mid, int high){

        int leftSum = -999;
        int rightSum = -999;
        int sum =0 ;




        for (int i=mid; i>= low ; i--)
        {
            sum = sum + nums[i];
            if (sum >= leftSum)
               { 
                   leftSum = sum;
               }
                
        }

        
        sum= leftSum;
        for(int i=mid +1; i< high +1 ; i++)
        {
            sum = sum +nums[i];
            if (sum >= rightSum)
            {
                rightSum =sum;
            }
        }

       
        return max(sum,rightSum);
        
    }

    int max(int a,int b){

        if (a>=b)
            return a;
        else 
            return b;
    }
    
    int max(int a, int b, int c){

        if (c > max(a,b))
            return c;
        else 
            return max(a,b);
    }
};


```

对照了一下官方的解答 可以学习的地方是 要学会使用struct 还有stack。

Solution

```cpp
class Solution {
public:
    struct Status {
        int lSum, rSum, mSum, iSum;
    };
  /*
 		 iSum 区间和
  
  */
    Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = max(l.lSum, l.iSum + r.lSum);
        int rSum = max(r.rSum, r.iSum + l.rSum);
        int mSum = max(max(l.mSum, r.mSum), l.rSum + r.lSum);
        return (Status) {lSum, rSum, mSum, iSum};
    };
  
  /*
 		 lSum rSum 中的max 分别考虑 crossing的情况 结合了crossing 和 left/right-max
 		 m 从下汇总到上面
  */

    Status get(vector<int> &a, int l, int r) {
        if (l == r) return (Status) {a[l], a[l], a[l], a[l]};
        int m = (l + r) >> 1;
        Status lSub = get(a, l, m);
        Status rSub = get(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

  /*
x >>= 1 means "set x to itself shifted by one bit to the right". drop the last bit, namely divided by 2.
  */
    int maxSubArray(vector<int>& nums) {
        return get(nums, 0, nums.size() - 1).mSum;
    }
};


```

Longest Common Increasing Subsequence

学到的新东西：

size( ): 用来获取字符串的长度，此外还可以获取vector类型的长度；

sizeof(): 用来求对象所占内存空间的大小。

搞混这两个容易导致heap buffer overflow。



3.#169 Majority Element

解题思路：首先考虑的是brute-force，遍历数组计数，再排序，可能需要的时间至少为 O(nˆ2)时间。于是我考虑用到了HashMap。

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {

        unordered_map<int, int> counts;
        int majority = 0, cnt =0;

        for(int num: nums){

            ++counts[num];

            if(counts[num] > cnt)
            {
                majority =num;
                cnt = counts[num];
            }

        }

        return majority;
    }

    
};

```

学到的东西：

HashMap(key,value) 

```cpp
#include <unordered_map>                // 0. include the library

int main() {
    // 1. initialize a hash map
    unordered_map<int, int> hashmap;
    // 2. insert a new (key, value) pair
    hashmap.insert(make_pair(0, 0));
    hashmap.insert(make_pair(2, 3));
    // 3. insert a new (key, value) pair or update the value of existed key
    hashmap[1] = 1;
    hashmap[1] = 2;
    // 4. get the value of a specific key
    cout << "The value of key 1 is: " << hashmap[1] << endl;
    // 5. delete a key
    hashmap.erase(2);
    // 6. check if a key is in the hash map
    if (hashmap.count(2) <= 0) {
        cout << "Key 2 is not in the hash map." << endl;
    }
    // 7. get the size of the hash map
    cout << "the size of hash map is: " << hashmap.size() << endl; 
    // 8. iterate the hash map
    for (auto it = hashmap.begin(); it != hashmap.end(); ++it) {
        cout << "(" << it->first << "," << it->second << ") ";
    }
    cout << "are in the hash map." << endl;
    // 9. clear the hash map
    hashmap.clear();
    // 10. check if the hash map is empty
    if (hashmap.empty()) {
        cout << "hash map is empty now!" << endl;
    }
}
```

