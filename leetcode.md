344. Reverse String

题目：

```question
Write a function that takes a string as input and returns the string reversed.

Example:
Given s = "hello", return "olleh".
```

思路：可以采用列表翻转的方法，也可以采用递归的方法

解决方法一：列表翻转

```python
class Solution(object):
    def reverseString(self, s):
        """
        采用列表翻转的方法
        :type s: str
        :rtype: str
        """
        s_tmp = list(s)
        l = len(s)
        k = (l+1)/2
        for i in xrange(k):
            s_tmp[i],s_tmp[l-i-1] = s_tmp[l-i-1],s_tmp[i]
        result = "".join(s_tmp)
        return result

    def reverseString(self, s):
        """
        采用递归的方法
        :type s: str
        :rtype: str
        """
        l = len(s)
        if l <= 1:
            return s
        return self.reverseString(s[l/2:]) + self.reverseString(s[:l/2])
```



### 283. move zeros

题目：

```python
Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].
```

思路1：遍历数组中所有0，总共有num个0，对每个0，每次都将0与0后面的数进行置换，一直最后一个数为止

code：

```python
class Solution(object):
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        zero_num = 0
        l = len(nums)
        for i in range(l):
            if nums[i] == 0:
                zero_num += 1
        print zero_num
        
        for i in range(zero_num):
            print i, l - i - 1, nums
            for j in range(0, l - i - 1):
                if nums[j] == 0:
                    nums[j], nums[j + 1] = nums[j + 1], nums[j]
        print nums
```

思路2：对nums数组中的每个数nums[i]，判断nums[i]是否是0，若nums[i]不是0，则将nums[i]与nums[[k]](k=0)进行置换,则nums[[k]](k=0)再也不是0，将k加1，k=1循环选择下一个不是0的数nums[i]，则将nums[i]与nums[[k]](k=1)进行置换，则nums[[k]](k=1)再也不是0，将k加1，k=2一直向下循环...（zeros=k)

code:

```python
class Solution(object):
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        zero_num = 0
        l = len(nums)
        for i in range(l):
            if nums[i] != 0:
                nums[i], nums[zero_num] = nums[zero_num], nums[i]
                zero_num += 1
```

### 104. Maximun Depth of Binary Tree

问题：

```python
Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

For example:
Given binary tree [3,9,20,null,null,15,7]
```

思路1：使用递归的方法实现

code：

```python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if root is None:
            return 0
        return 1+ max(self.maxDepth(root.left), self.maxDepth(root.right))
```

思路2：使用广度优先搜索（使用队列）,非递归实现

code:

```python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if root is None:
            return 0
        from collections import deque
        queue = deque([(root, 1)])
        while queue:
            curr,val = queue.popleft()
            if curr.left:
                queue.append((curr.left, val+1))
            if curr.right:
                queue.append((curr.right, val+1))
        return val
```

思路3：使用深度优先搜索（堆栈），非递归实现，深度优先又分为先序遍历、中序遍历、后序遍历

code:

```python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if root is None:
            return 0
        stack = [(root, 1)]
        level = 0
        while stack:
            curr, val = stack.pop()
            level = max(level, val)
            # print level
            print curr.val
            if curr.right:
                stack.append((curr.right, val+1))
            if curr.left:
                stack.append((curr.left, val+1))
        return level
```

思路4：深度优先递归实现

code:

```python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        level = 0
        res = [0]
        """
        level 记录到达的层数
        """
        self.dfs(root, level,res)
        return max(res)
    
    def dfs(self,node, level, res):
        if node==None:
            return
        res.append(level)
        self.dfs(node.left, level+1, res)
        self.dfs(node.right, level+1, res)
```



### 371.Sum of Two Integers

题目：

```python
Calculate the sum of two integers a and b, but you are not allowed to use the operator + and -.

Example:
Given a = 1 and b = 2, return 3.
```

思路1：

```
对于数a和b：
a与b的与(&)操作: c = a&b
a 1 0 1 0
b 1 1 0 0
c 1 0 0 0
a与b的或(|)操作: d = a|b
a 1 0 1 0
b 1 1 0 0
d 1 1 1 0
d + c = a +b，
则d和c相加...
可以看成递归操作，若a和b的与操作全为0，即没有进位，则a + b = a|b(结束条件）

想法错误：每次与之后的数不会改变，永远是c与d的与,会进入死循环
```

code:

```python
class Solution(object):
    def getSum(self, a, b):
        """
        非递归实现
        :type a: int
        :type b: int
        :rtype: int
        """
        print a&b

        while a:
            c = a&b
            d = a|b
            a = c
            b = d
            # print a
        return b
```

思路2：

```
对于数a和b：
a与b的与(&)操作: c = a&b
a 1 0 1 0
b 1 1 0 0
c 1 0 0 0

a与b的或(|)操作: d = a|b
a 1 0 1 0
b 1 1 0 0
d 1 1 1 0

a与b的异或(^)操作: e= a^b
a 1 0 1 0
b 1 1 0 0
e 0 1 1 0
则可以将a加b看成2*c 加上e（2*c表示a加b的进位）即 a+b = 2*c + e
可以看成递归操作，若a和b的与操作全为0，即没有进位，则a + b = a|b(结束条件）

证明：为什么不会进入死循环,因为每次都会将与操作的结果左移以为，所以每次相加的结果都不一样，不会永远陷入死循环
```

code:递归实现

```python
class Solution(object):
    def getSum(self, a, b):
        """
        递归实现
        :type a: int
        :type b: int
        :rtype: int
        """
        if a==0:
            return b

        c = (a&b)<<1
        d = a^b

        return self.getSum(c,d)
```

code:非递归实现

```python
class Solution(object):
    def getSum(self, a, b):
        """
        非递归实现
        :type a: int
        :type b: int
        :rtype: int
        """
        while a:
            c = a&b
            d = a^b
            a = c<<1
            b = d
        return b
```





### 169. Majority Element

题目：

Given an array of size *n*, find the majority element. The majority element is the element that appears **more than** `⌊ n/2 ⌋` times.

You may assume that the array is non-empty and the majority element always exist in the array.

```python
"""
各种方法时间复杂度分析：
1.暴力破解法：O(N**2)
2.hash：O(n)
3.sorting:O(nlogn)
4.randomization:平均O(n)，最坏无穷大
5.分治法(divide and conquer)：O(nlogn)
6.moore voting algorthm: O(n)
7. bit manipulation: O(n)
"""
"""
思路1：摩尔投票算法，前提条件一定存在majority
算法在局部变量中定义一个序列元素m和计数器i，初始情况下计数器为0，算法一次扫描序列中的元素，
当处理元素x的时候，如果计数器为0，那么僵x赋值给m，然后将计数器设置为1，
如果计数器不为0，那么将序列元素m和x比较，如果相等，那么计数器加1，如果不等，那么计数器减1
处理之后，最后存储的序列元素m，就是序列中最多的元素
perudocode
Initialize an element m and a counter i with i=0
For each element x of the input sequence:
    if i=0, then assign m=x and i=1
    else if m=x,then assign i= i + 1
    else assign i = i - 1
解释：
每次去掉多对单词，在这个多对单词中，出现次数最大的单词不超过一半，
如果存在majority，那么剩下的一定是majority
注意：
如果一个元素超过一半，那么它一定会留下，但是留下的不一定查过一半
思想：每次都找出一对不同的元素，从数组中删掉，直到数组为空或只有一种元素。
不难证明，如果存在元素e出现频率超过半数，那么数组中最后剩下的就只有e。
"""


class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        m = nums[0]
        count = 0
        for i in nums:
            if count==0:
                m = i
                count = 1
            elif m==i:
                count += 1
            else:
                count -= 1
        return m


"""
思路2：分治法
解释：若k是nums中的majority，则将nums分为两部分，其中必有一部分，在这部分中k也是majority，
在另一部分中的majority是m(m有可能也是k），则判断k和m哪个出现次数多，则可以将出现次数最多的找出来
每次将数组分成左右两半，在两半中分别找出现次数最多的元素，若找到的两个元素相同则此元素即为所求，
否则在整个数组中分别计算这两个元素出现的次数，取较大的那个。
"""
class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return None
        if len(nums)==1:
            return nums[0]
        a = self.majorityElement(nums[:len(nums)/2])
        b = self.majorityElement(nums[len(nums)/2:])
        if a == b:
            return a
        # 若a出现的次数占nums中一半以上，则返回a，否则返回b
        k = [b, a][nums.count(a) > len(nums)/2]
        return k


"""
思路3：位操作法：
思想：既然存在所谓大量元素，那么如果我们将所有的数写成二进制，每个数写一行进行位对齐，
那么每一位出现最多的0或1就是大量元素在该位的值。
考虑题目说的是整数，我们统计每一位的出现次数（总共32位）最多的0或1，组合即是majority
例如：[3,4,6,6,6]
位对齐:
3   0  1  1
4   1  0  0
6   1  1  0
6   1  1  0
6   1  1  0
在第一列和第二列1出现最多，第三列0出现最多，组合即是6
注意：python在处理负数时需要注意二进制第一位代表负数
"""

class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        major = 0
        mask = 1
        for i in xrange(0, 32):
            count = 0
            for j in nums:
                k = j&mask
                if j&mask:
                    count += 1
                    if count > len(nums)/2:
                        major |= mask
                        break
            mask <<= 1
        return major if major >> 31 == 0 else major - (1<<32)


"""
思路4：基于哈希的方法
"""
class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        d = {}
        for i in nums:
            if i not in d:
                d[i] = 1
            else:
                d[i] += 1
            if d[i] > len(nums)/2:
                return i

"""
思路5：Randomization
平均复杂度O(N),最差复杂度无穷大，但实际运行还是挺稳定的。
其方法是，随机抽一个元素，检查是否是多数元素，由于多数元素个数占大半，期望查找次数<2
"""

class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        import random
        while True:
            r = random.choice(nums)
            if nums.count(r) > len(nums)/2:
                return r

"""
思路6：排序法
先对数组进行排序，取最中间的一个数
"""

res = Solution().majorityElement([3,4, 6, 6, 6])
print res
```

### 最长公共子序列

子序列：一个序列A=a1,a2,.......an,中任意删除若干项，剩余的序列叫做A的一个子序列。也可以认为是从序列A按原顺序保留若干项得到的序列。对一个长度为n的序列，它一共有2^n个子序列，有(2^n-1)个非空子序列。子序列不是子集合，它和原始序列的元素顺序是相关的。

**公共子序列**：如果序列C既是序列A的子序列，同时也是序列B的子序列，则称它为序列A和序列B的公共子序列。例如序列1,3,5,4,2,6,8,7和序列1,4,8,6,7,5，他们的最长公共子序列是：1,4,8,7       1,4,6,7

公共子序列中长度最长的序列叫做A和B的公共子序列

### 最大连续子序列和53. maximum subarray

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







