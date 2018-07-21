### 53. maximum subarray

问题描述：给定一个整数序列，a0,a1,a2...,an(项可以为负数)，求其中最大的子序列和。如果所有证书都是负数，那么最大子序列和为0.

测试用例：

```java
public class Maximum_Subarray {
    public static void main(String[] args)
    {
        Solution_53_1 test1 = new Solution_53_1();
        Solution_53_2 test2 = new Solution_53_2();
        Solution_53_3 test3 = new Solution_53_3();

        int[] b = {-2,1,-3,4,-1,2,1,-5,4};
        int a;
        a = test3.maxSubArray(b);
        System.out.println(a);
    }

}
```

思路1：

暴力破解：加入序列总共有n个元素，最大连续子序列和的起点一定是其中一个，终点一定其中一个；对序列中任意一个起点后的子序列（包括起点）进行考虑，将起点后的序列值逐渐与前面序列值相加，记录其中出现的最大值；比较n个起点中的最大值即为最大连续子序列和。

code：

```java
class Solution_53_1 {
    public int maxSubArray(int[] nums) {

        int thissum, maxsum, i, j, k;
        int N = nums.length;
        maxsum = nums[0];
        for(i=0;i<N;i++)
        {
            thissum = 0;
            for(j=i;j<N;j++)
            {
                thissum += nums[j];
                if(thissum>maxsum){
                    maxsum = thissum;
                }
            }
        }
        return maxsum;
    }
}
```



思路2：分治法

分治法：取序列中间的元素点，将序列分为两部分，左序列包含中间元素点，右序列不包含中间元素点；求出

1.左序列的最大连续子序列和，

2.右序列的最大连续子序列和，

3.然而数组的最大连续子序列也有可能出现在左序列和右序列的中间，

如果出现第三种情况，则中间元素一定出现在最大连续子序列中，此时可以以中间元素作为起点，分别向左和向右连续进行拓展，记录向左拓展的最大连续子序列和，向右拓展的最大连续子序列和，两者相加为第三种情况的最大连续子序列和。

第三种情况如下图：

![X8D45XG8}9NDV$R_KY0CWO](E:\Download\QQ\MobileFile\Image\X8D45XG8}9NDV$R[_KY0CWO.png)

code：

```java
class Solution_53_2 {
    public int maxSubArray(int[] nums) {
        int a = 0;
        a = divide(nums, 0, nums.length - 1);
        return a;
    }

    public int divide(int[] nums, int left, int right) {
        if (left == right) {
            return nums[left];
        }
        int leftsum, rightsum, center;
        center = (left + right) / 2;
        leftsum = divide(nums, left, center);
        rightsum = divide(nums, center + 1, right);
        int maxleftsum = Integer.MIN_VALUE;
        int maxrightsum = Integer.MIN_VALUE;
        int topsum1 = 0;
        for (int i = center; i >= left; i--) {
            topsum1 = topsum1 + nums[i];
            if (topsum1 > maxleftsum) {
                maxleftsum = topsum1;
            }
        }
        int topsum2 = 0;
        for (int i = center + 1; i <= right; i++) {
            topsum2 = topsum2 + nums[i];
            if (topsum2 > maxrightsum) {
                maxrightsum = topsum2;
            }
        }
        return Math.max(Math.max(leftsum, rightsum), maxleftsum + maxrightsum);
    }
}
```

思路3：动态规划

动态规划：最大连续子序列和的序列最后一个点必然是n个元素其中一个，

令A[i]为以nums[i]为结尾（包括nums[i]）的序列中最大连续序列和，对A[i+1]来说，若A[i]是一个负数，则A[i]对nums[i+1]没有贡献，A[i+1]等于nums[i+1]，否则A[i+1]等于nums[i+1]+A[i].

公式：A[i] = max(A[i-1]+nums[i], nums[i])

A[-1] = 0(表示没有元素，0对后面不造成影响)

code：

```java
class Solution_53_3 {
    public int maxSubArray(int[] nums) {
        int maxsum = Integer.MIN_VALUE;
        int thissum =0;
        int i =0;
        while(i<nums.length)
        {
            thissum = Math.max(thissum + nums[i],nums[i]);
            maxsum = Math.max(maxsum,thissum);
            i++;
        }
        return maxsum;
    }
}
```



### 801.Minimum Swaps To Make Sequences Increasing

问题：

We have two integer sequences `A` and `B` of the same non-zero length.

We are allowed to swap elements `A[i]` and `B[i]`.  Note that both elements are in the same index position in their respective sequences.

At the end of some number of swaps, `A` and `B` are both strictly increasing.  (A sequence is *strictly increasing* if and only if `A[0] < A[1] < A[2] < ... < A[A.length - 1]`.)

Given A and B, return the minimum number of swaps to make both sequences strictly increasing.  It is guaranteed that the given input always makes it possible.

```spreadsheet
Example:
Input: A = [1,3,5,4], B = [1,2,3,7]
Output: 1
Explanation: 
Swap A[3] and B[3].  Then the sequences are:
A = [1, 3, 5, 7] and B = [1, 2, 3, 4]
which are both strictly increasing.
```

思路：

对于数组A和B位置i处，dp(i)(0)表示在第i处A[i]和B[i]不需要交换，A和B已经交换的次数；dp(i)(1)表示在第i处A[i]和B[i]不需要交换，A和B已经交换的次数；对于A[i]和B[i]、A[i+1]和B[i+1]存在两种情况：

- A[i+1]>A[i]  B[i+1]>B[i]：若A[i+1]和B[i+1]在第i+1处没有交换，则A[i]和B[i]在第i处也不需要交换；若A[i+1]和B[i+1]在第i+1处有交换，则A[i]和B[i]在第i处也需要交换。

  dp(i+1)(0) = min( dp(i)(0),  dp(i+1)(0) ),

  dp(i+1)(0) = min( dp(i)(1)+1,  dp(i+1)(1) )

- A[i+1]>B[i]  B[i+1]>A[i]：若A[i+1]和B[i+1]在第i+1处没有交换，则A[i]和B[i]在第i处需要交换；若A[i+1]和B[i+1]在第i+1处有交换，则A[i]和B[i]在第i处不需要交换。

  dp(i+1)(0) = min( dp(i)(1),  dp(i+1)(0) ),

  dp(i+1)(0) = min( dp(i)(0)+1 ,  dp(i+1)(1) )



code：

```java
class Solution {
    public int minSwap(int[] A, int[] B) {
        if(A.length==0){
            return 0;
        }
        int[] dp = {0,1};
        
        for(int i=0;i<A.length-1;i++)
        {   
            int[] dp_new = {Integer.MAX_VALUE,Integer.MAX_VALUE};
            if(A[i+1]>A[i] && B[i+1]>B[i])
            {
                dp_new[0] = Math.min(dp[0],dp_new[0]);
                dp_new[1] = Math.min(dp[1]+1,dp_new[1]);
            }
            if(A[i+1]>B[i] && B[i+1]>A[i])
            {
                dp_new[0] = Math.min(dp[1],dp_new[0]);
                dp_new[1] = Math.min(dp[0]+1,dp_new[1]);
            }
            dp = dp_new;
        }
        return Math.min(dp[0],dp[1]);
        
    }
}
```

code:python

```python
class Solution(object):
    def minSwap(self, A, B):
        """
        :type A: List[int]
        :type B: List[int]
        :rtype: int
        """
        n1 = 0
        n2 = 1
        for i in range(len(A)-1):
            s1 = sys.maxint
            s2 = sys.maxint
            if A[i+1] > A[i] and B[i+1]>B[i]:
                s1 = min(n1,s1)
                s2 = min(n2+1,s2)
            if A[i+1] > B[i] and  B[i+1] > A[i]:
                s1 = min(n2,s1)
                s2 = min(n1+1,s2)
            n1 = s1
            n2 = s2
        return min(n1,n2)
```

### Decode Ways

问题：A message containing letters from `A-Z` is being encoded to numbers using the following mapping:Given an encoded message containing digits, determine the total number of ways to decode it.

For example,
Given encoded message `"12"`, it could be decoded as `"AB"` (1 2) or `"L"` (12).

The number of ways decoding `"12"` is 2.

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

思路：采用动态规划：对于a[i],a[i+1],a[i+2]解码方式分别为dp[i], dp[i+1], dp[i+2]；若a[i+2]处为1-9之间的数，则dp[i+2] = dp[i+2] + dp[i+1]，若a[i+1]a[i+2]组成的二位数在10-26之间,则dp[i+2] = dp[i+2] + dp[i]。

注意：注意初始值的问题，在只有一个元素的时候，如果该元素为0，则初始值为0，否则初始值为1.

在没有元素时的初始值为1.因为第二个元素要和之前元素保持关系；

code

```java
class Solution {
    public int numDecodings(String s) {
        if(s == null || s.length() == 0) {
            return 0;
        }
        int[] dp= new int[s.length+1];
        dp[0] = 1;
        dp[1] = s.charAt(0) != '0' ? 1 : 0;
        
        for(int i=2;i<=s.length;i++){
            int b= Integer.valueOf(s.substring(i-1,i));
            int c= Integer.valueOf(s.substring(i-2,i));
            if(b>=1 &&b<=9){
                dp[i] += dp[i-1];
            }
            if(c>=10&&c<=26){
                dp[i]+=dp[i-2];
            }
        }
        return dp[s.length];
    }
}
```

代码注意：dp[i]表示从a[0]...a[i-1]有多少种解码方式，可以得到dp[0] = 1

code：python

```python
class Solution(object):
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """
        length = len(s)
        dp = [0]*(length+1)
        dp[0] = 1
        dp[-1] = 1
        if length==0 or s[0]=='0':
            return 0
        
        for i in range(1,length):
            s1 = s[i:i+1]
            s2 = s[i-1:i+1]
            if int(s1)>=1 and int(s1)<=9:
                dp[i] += dp[i-1]

            if int(s2)>=10 and int(s2)<=26:
                dp[i] += dp[i-2]


        return dp[length-1]
```

### 44. Wildcard Matching

问题：Implement wildcard pattern matching with support for `'?'` and `'*'`

```java
'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "*") → true
isMatch("aa", "a*") → true
isMatch("ab", "?*") → true
isMatch("aab", "c*a*b") → false
```

思路1：贪婪的思路

code：





### 32.Longest Valid Parentheses

问题：Given a string containing just the characters `'('` and `')'`, find the length of the longest valid (well-formed) parentheses substring.

For `"(()"`, the longest valid parentheses substring is `"()"`, which has length = 2.

Another example is `")()())"`, where the longest valid parentheses substring is `"()()"`, which has length = 4.

思路：使用堆栈，找出连续最长的括号序列

code：

```python
class Solution(object):
    def longestValidParentheses(self, s):
        """
        :type s: str
        :rtype: int
        """
        length = len(s)
        stack = []
        dp = [0]*len(s)
        ans = 0
        num = 0
        res = 0
        for i in range(length):
            stack.append(i)
            ans += 1
            if ans >= 2 and s[stack[-1]] == ")" and s[stack[-2]] == "(":
                dp[stack.pop()] = 1
                dp[stack.pop()] = 1
                ans -= 2

        for i in dp:
            if i==0:
                num = 0
            else:
                num += 1
                res = max(res,num)

        return res
        
```
### 120. Triangle

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle

```python
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

The minimum path sum from top to bottom is `11` (i.e., 2 + 3 + 5 + 1 = 11).

**Note:**
Bonus point if you are able to do this using only *O*(*n*) extra space, where *n* is the total number of rows in the triangle.

思路1：从上往下进行考虑：

res(i)(j) = min( res(i-1)(j-1), res(i-1)(j+1)) + triangle(i)(j)

最终求的数是min(res(3)(0), res(3)(1), res(3)(2), res(3)(3))，占用的空间是坐标点的个数。



思路2：从下往上进行考虑，只更新最下一层的数组res(j)

对于第i层：res(j) = min( res(j),res(j+1) ) + triangle(i)(j)

对于第i层，第i层第一个值采用第i+1层第一个值和第i+1层第二个值进行更新；第i层第二个值采用第i+1层第二个值和第i+1层第三个值进行更新；第i层第k个值采用第i+1层第k个值和第i+1层第k+1个值进行更新...

```python
class Solution(object):
    def minimumTotal(self, triangle):
        """
        :type triangle: List[List[int]]
        :rtype: int
        """
        length1 = len(triangle)
        length2 = len(triangle[-1])
        res = triangle[-1]
        for i in range(length1-2,-1,-1):
            for j in range(i+1):
                res[j] = min(res[j],res[j+1]) + triangle[i][j] 
        return res[0]
```



### 523.Continuous Subarray Sum

问题：Given a list of **non-negative** numbers and a target **integer** k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to the multiple of **k**, that is, sums up to n*k where n is also an **integer**.

**Example 1:**

```python
Input: [23, 2, 4, 6, 7],  k=6
Output: True
Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
```

**Example 2:**

```python
Input: [23, 2, 6, 4, 7],  k=6
Output: True
Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
```

**Note:**

1. The length of the array won't exceed 10,000.
2. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.

思路1：暴力破解，计算所有的连续子序列，复杂度O(N^2)

思路2：技巧：若数字a和数字b分别除以数字c，若得到的余数相同，那么(a-b)必定能够整除c。对数组nums = [23,2,6,4,7] 从第一个数进行累加，每次累加都除以c得到余数，则总共有五个余数，如果其中有两个余数相同，则一定存在连续子序列是c的倍数。

注意：初始值在索引-1处余数为0，因为若在数组中出现余数为0，也存在符合条件的连续子序列，满足两个余数相同，两个和之差能够整除6.

注意：如果k是0，则序列中如果存在两个连续的0，则也满足条件。

code:

```python
class Solution(object):
    def checkSubarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        dp={0:-1}
        sum = 0
        for i in xrange(len(nums)):
            sum += nums[i]
            sum = sum % k if k else sum
            if sum not in dp:
                dp[sum]=i
            else:
                if i - dp[sum]>1:
                    return True
        return False
```

### 152.  Maximum Product Subarray

问题：Find the contiguous subarray within an array (containing at least one number) which has the largest product.

For example, given the array `[2,3,-2,4]`,
the contiguous subarray `[2,3]` has the largest product = `6`.

思路1：对第k个数，在第k个数处存在最大值和最小值，第k个数处的最大值和最小值在第k-1个数的最大数乘以nums[k], 第k-1个数的最小数乘以nums[k]，nums[k]三个数中间。在1-n每个位置都存在一个最大值，在所有的最大值中选择最大值即是最大值。

```python
class Solution(object):
    def maxProduct(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        start = 0
        min_temp = nums[0]
        max_temp = nums[0]
        sum_temp = nums[0]

        for i in xrange(len(nums)):
            if i == 0:
                continue
            a = max_temp * nums[i]
            b = min_temp * nums[i]
            max_temp = max(a,b,nums[i])
            min_temp = min(a,b,nums[i])
            sum_temp = max(max_temp,sum_temp)


        return sum_temp
```



### 741. Cherry Pickup