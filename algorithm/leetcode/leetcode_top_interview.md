[LeetCode 精选 TOP 面试题](https://leetcode-cn.com/problemset/leetcode-top/)
<!-- TOC -->

- [1. 两数之和 @哈希](#1-两数之和-哈希)
- [15. 三数之和](#15-三数之和)
- [2. 两数相加](#2-两数相加)
- [3. 无重复字符的最长子串](#3-无重复字符的最长子串)
- [4. 寻找两个正序数组的中位数 #hard](#4-寻找两个正序数组的中位数-hard)
- [5. 最长回文子串](#5-最长回文子串)
- [7. 整数反转](#7-整数反转)
- [8. 字符串转换整数 (atoi)](#8-字符串转换整数-atoi)
- [10. 正则表达式匹配 #hard](#10-正则表达式匹配-hard)
- [11. 盛最多水的容器](#11-盛最多水的容器)
- [13. 罗马数字转整数](#13-罗马数字转整数)
- [14. 最长公共前缀](#14-最长公共前缀)
- [46. 全排列 @回溯](#46-全排列-回溯)
- [17. 电话号码的字母组合 @回溯](#17-电话号码的字母组合-回溯)
- [19. 删除链表的倒数第 N 个结点 @快慢指针](#19-删除链表的倒数第-n-个结点-快慢指针)
- [20. 有效的括号](#20-有效的括号)
- [21. 合并两个有序链表](#21-合并两个有序链表)
- [22. 括号生成 @回溯](#22-括号生成-回溯)
- [23. 合并 K 个升序链表](#23-合并-k-个升序链表)
- [78. 子集 @回溯](#78-子集-回溯)
- [26. 删除排序数组中的重复项](#26-删除排序数组中的重复项)
- [28. 实现 strStr ()](#28-实现-strstr-)
- [29. 两数相除](#29-两数相除)
- [33. 搜索旋转排序数组 @二分查找](#33-搜索旋转排序数组-二分查找)
- [34. 在排序数组中查找元素的第一个和最后一个位置 @二分查找](#34-在排序数组中查找元素的第一个和最后一个位置-二分查找)
- [69. x 的平方根 @二分查找](#69-x-的平方根-二分查找)
- [36. 有效的数独](#36-有效的数独)
- [38. 外观数列](#38-外观数列)
- [41. 缺失的第一个正数 #Hard](#41-缺失的第一个正数-hard)
- [42. 接雨水](#42-接雨水)
- [44. 通配符匹配](#44-通配符匹配)
- [48. 旋转图像](#48-旋转图像)
- [49. 字母异位词分组](#49-字母异位词分组)
- [50. Pow(x, n)](#50-powx-n)
- [53. 最大子序和](#53-最大子序和)
- [54. 螺旋矩阵](#54-螺旋矩阵)
- [55. 跳跃游戏 @贪心](#55-跳跃游戏-贪心)
- [45. 跳跃游戏 II](#45-跳跃游戏-ii)
- [56. 合并区间](#56-合并区间)
- [62. 不同路径](#62-不同路径)
- [66. 加一](#66-加一)
- [70. 爬楼梯](#70-爬楼梯)
- [73. 矩阵置零](#73-矩阵置零)
- [75. 颜色分类](#75-颜色分类)
- [76. 最小覆盖子串 #hard @滑动窗口](#76-最小覆盖子串-hard-滑动窗口)
- [79. 单词搜索 @回溯](#79-单词搜索-回溯)
- [88. 合并两个有序数组 @数组指针](#88-合并两个有序数组-数组指针)
- [91. 解码方法 @DP](#91-解码方法-dp)
- [94. 二叉树的中序遍历](#94-二叉树的中序遍历)

<!-- /TOC -->
#### 1. 两数之和 @哈希

[题目链接](https://leetcode-cn.com/problems/two-sum/)

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        // 将所有值存入哈希表
        for(int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        // 遍历数组寻找 twoSum
        for(int i = 0; i < nums.length; i++) {
            int other = target - nums[i];
            // 哈希表中有 other 且不是 nums[i] 本身
            if(map.containsKey(other) && map.get(other) != i) {
                return new int[] {i, map.get(other)};
            }
        }
        return new int[] {-1, -1};
    }
}
```

> [167. 两数之和 II - 输入有序数组 @双指针](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

```java
// 当数组有序时考虑双指针的方法
// 题目假设只有一个解
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        for(int i = 0; i < numbers.length; i++) {
            int sum = numbers[left] + numbers[right];
            if(sum == target)   return new int[] {left + 1, right + 1};
            else if(sum < target)   left++;
            else    right--;
        }
        return new int[] {-1, -1};
    }
}
```

#### 15. 三数之和

[题目链接](https://leetcode-cn.com/problems/3sum/)

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // 首先对数组排序
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        // 遍历数组，从左到右
        for(int i = 0; i < nums.length; i++) { 
            int target = 0 - nums[i];
            List<List<Integer>> tuples = new ArrayList<>();
            // 转换为 twoSum 问题
            tuples = twoSum(nums, i + 1, target);
            // 给每一个符合条件的结果加上当前的值凑成 3 个数
            for(List tuple : tuples) {
                tuple.add(nums[i]);
                res.add(tuple);
            }
            // 为了保证结果不重复，同一个数只能用一次
            while(i + 1 < nums.length && nums[i] == nums[i + 1])     i++;
        }
        return res;
    }

    public List<List<Integer>> twoSum(int[] nums, int start, int target) {
        List<List<Integer>> res = new ArrayList<>();
        // 返回所有可能的 twoSum 结果，结果不能有重复
        // 有序数组双指针，只不过 left 不一定是从 0 开始
        int left = start, right = nums.length - 1;
        while(left < right) {
            int sum = nums[left] + nums[right];
            // leftVal 和 rightVal 用于后面的比较（保证没有重复结果）
            int leftVal = nums[left], rightVal = nums[right];
            if(sum == target) {
                // Arrays.asList(T... a) 泛型
                // res.add() 中只有 new ArrayList 才行
                // 因为上面遍历 tuples 元素需要调用 add()
                res.add(new ArrayList<>(Arrays.asList(nums[left], nums[right])));
                // 为了保证结果不重复，同一个数只能用一次
                while(left < right && nums[left] == leftVal)    left++;
                while(left < right && nums[right] == rightVal)  right--;
            } else if(sum < target) {
                while(left < right && nums[left] == leftVal)    left++;
            } else {
                while(left < right && nums[right] == rightVal)  right--;
            }
        }
        return res;
    }

}
```

> 四数之和及 nSum 问题，之后需要单独整理一篇 nSum 问题模板，递归思路调用

#### 2. 两数相加

[题目链接](https://leetcode-cn.com/problems/add-two-numbers/)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        // head 作为哨兵
        // while 循环包含全部情况
        // 每个循环下 if 语句判断链表是否为空
        // cur 指针记得指向下一个节点
        ListNode head = new ListNode(0); 
        ListNode cur = head;
        int carry = 0;
        while(l1 != null || l2 != null || carry != 0) {
            int sum = carry;
            if(l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }
            if(l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            int val = sum % 10;
            carry = sum / 10;
            cur.next = new ListNode(val);
            cur = cur.next;
        }

        return head.next;
    }
}
```

#### 3. 无重复字符的最长子串

[题目链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

- 解法一

思路：记录并更新从第一个字符到当前字符的最长字串

注意：出现重复字符时更新起始位置（需判断重复字符索引和当前起始位置的大小）

- 解法二

使用滑动窗口模板，四个问题（right 向右更新什么？停止扩充条件？left 向右更新什么？结果在哪个阶段更新？）

[滑动窗口模板文章](https://labuladong.gitbook.io/algo/bi-du-wen-zhang/hua-dong-chuang-kou-ji-qiao-jin-jie)

```java
// 滑动窗口模板
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> window = new HashMap<>();
        int left = 0, right = 0;
        int length = 0;
        while(right < s.length()) {
            char c = s.charAt(right);
            right++;

            // 1. right 向右，更新 window
            window.put(c, window.getOrDefault(c, 0) + 1);

            // 2. 停止扩充条件
            while(window.get(c) > 1) {
                char d = s.charAt(left);
                left++;
                // 3. left 向右，更新 window
                window.put(d, window.get(d) - 1);
            }
            // 4. 结果更新
            if(right - left > length)   length = right - left;
        }
        return length;
    }
}
```

#### 4. 寻找两个正序数组的中位数 #hard

[题目链接](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

- 解法一

新建一个有序数组存储两个有序数组，然后取该数组的中位数

时间复杂度：O(m+n)

空间复杂度：O(m+n)

- 解法二

不新建数组，而是维护两个指针后移指向中位数

时间复杂度：O(m+n)

空间复杂度：O(1)

- 解法三

二分查找

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int length1 = nums1.length, length2 = nums2.length;
    int totalLength = length1 + length2;
    if (totalLength % 2 == 1) {
        int midIndex = totalLength / 2;
        double median = getKthElement(nums1, nums2, midIndex + 1); // 第 k 个，不是索引所以 +1
        return median;
    } else {
        int midIndex1 = totalLength / 2 - 1, midIndex2 = totalLength / 2;
        double median = (getKthElement(nums1, nums2, midIndex1 + 1) + getKthElement(nums1, nums2, midIndex2 + 1)) / 2.0;
        return median;
    }
}

public int getKthElement(int[] nums1, int[] nums2, int k) {
    /* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
         * 这里的 "/" 表示整除
         * nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
         * nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
         * 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
         * 这样 pivot 本身最大也只能是第 k-1 小的元素
         * 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
         * 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
         * 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
         */

    int length1 = nums1.length, length2 = nums2.length;
    int index1 = 0, index2 = 0;

    while (true) {
        // 边界情况
        if (index1 == length1) { // 考虑 nums1 = []
            return nums2[index2 + k - 1]; // 考虑 nums2 = [1]
        }
        if (index2 == length2) {
            return nums1[index1 + k - 1];
        }
        if (k == 1) {
            return Math.min(nums1[index1], nums2[index2]);
        }

        // 正常情况
        int half = k / 2;
        int newIndex1 = Math.min(index1 + half, length1) - 1; // 取不到 nums[length]
        int newIndex2 = Math.min(index2 + half, length2) - 1;
        int pivot1 = nums1[newIndex1], pivot2 = nums2[newIndex2];
        if (pivot1 <= pivot2) {
            k -= (newIndex1 - index1 + 1); // 考虑 nums1=[1] newIndex1==index1
            index1 = newIndex1 + 1; // 考虑 nums1=[1] index1==length1
        } else {
            k -= (newIndex2 - index2 + 1);
            index2 = newIndex2 + 1;
        }
    }
}
```

#### 5. 最长回文子串

[题目链接](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

```java
class Solution {
    public String longestPalindrome(String s) {
        // 遍历整个字符串
        // 对每个字符进行回文字串判定并记录长度
        // 考虑到回文串奇偶性，需要使用双指针技巧
        // 返回最长的回文字串
        String res = "";
        for(int i = 0; i < s.length(); i++) {
            String str1 = longestPalindrome(s, i, i);
            String str2 = longestPalindrome(s, i, i+1);
            String str = str1.length() > str2.length() ? str1 : str2;
            if(str.length() > res.length()) res = str;
        }
        return res;
    }

    public String longestPalindrome(String s, int left, int right) { // 双指针
        while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        return s.substring(left + 1, right); // [a, b)
    }
}
```

#### 7. 整数反转

[题目链接](https://leetcode-cn.com/problems/reverse-integer/)

```
输入：x = 123
输出：321
```

- 解法一

x 转换为 String，将 String 反转（需判断负号）后判断是否溢出

用 long 接收反转后的数值（不合题义）

- 解法二

2^31 - 1 = 2147483647

-2^31 = -2147483648

```java
class Solution {
    public int reverse(int x) {
        // 数学方法
        // 一次构建反转整数的一位数字，每次都需要判断溢出问题
        int res = 0;
        while(x != 0) {
            int mod = x % 10;
            x /= 10;
            if(res > Integer.MAX_VALUE / 10 || (res == Integer.MAX_VALUE / 10 && mod > Integer.MAX_VALUE % 10)) { // 大于大于
                return 0;
            }
            if(res < Integer.MIN_VALUE / 10 || (res == Integer.MIN_VALUE / 10 && mod < Integer.MIN_VALUE % 10)) { // 小于小于
                return 0;
            }

            res = res * 10 + mod;
        }
        return res;
    }
}
```

#### 8. 字符串转换整数 (atoi)

[题目链接](https://leetcode-cn.com/problems/string-to-integer-atoi/)

```
输入：s = "42"
输出：42
解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
第 1 步："42"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："42"（读入 "42"）
           ^
解析得到整数 42 。
由于 "42" 在范围 [-231, 231 - 1] 内，最终结果为 42 。
```

```java
public class Solution {

    public int myAtoi(String str) {
        int index = 0;
        char[] s = str.toCharArray();
        // 去除前导空格
        while(index < s.length && s[index] == ' ')  index++;

        if(index == s.length)   return 0; // "    "

        int sign = 1;
        if(s[index] == '-' || s[index] == '+') {
            sign = s[index] == '-' ? -1 : 1;
            index++;
        }

        int res = 0;
        while(index < s.length) {
            if(s[index] > '9' || s[index] < '0')    break; // 不是数字跳出

            int mod = s[index] - '0';
            // 溢出判断
            if(res > Integer.MAX_VALUE / 10 || (res == Integer.MAX_VALUE / 10 && mod > Integer.MAX_VALUE % 10)) {
                return Integer.MAX_VALUE;
            } 
            if(res < Integer.MIN_VALUE / 10 || (res == Integer.MIN_VALUE / 10 && mod > -(Integer.MIN_VALUE % 10))) {
                return Integer.MIN_VALUE;
            }

            res = res * 10 + sign * mod;
            index++;
        }
        return res;
    }

}
```

#### 10. 正则表达式匹配 #hard

[题目链接](https://leetcode-cn.com/problems/regular-expression-matching/)

```
输入：s = "aa" p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

- 解法一：“自顶向下”的带备忘录的递归解法（dp 函数）


`dp 函数定义`：dp(s, 0, p, 0) s[i:] 和 p[j:] 能否匹配

`base case`：p 到最后或 s 到最后

`choice`：s[i] 和 p[j] 匹配；s[i] 和 p[j] 不匹配。另外需要考虑 p[j + 1] 是否为 *

```java
class Solution {
    public boolean isMatch(String s, String p) {
        return dp(s, 0, p, 0);
    }

    Map<String, Boolean> memo = new HashMap<>(); // 备忘录

    // 1. dp() 含义：s[i:] 和 p[j:] 能否匹配
    public boolean dp(String s, int i, String p, int j) {
        int m = s.length(), n = p.length();
        // 2. base case
        if(j == n)  return i == m;  // p 到最后
        if(i == m) { // s 到最后，p 不一定
            if(j == n)  return true;
            else {
                if((n - j) % 2 != 0)    return false;
                else {
                    while(j + 1 < n) { // n 是长度，取不到
                        if(p.charAt(j + 1) != '*')  return false;
                        j += 2;
                    }
                    return true;
                }
            }
        }
        // 3. choice
        String key = i + "," + j;
        boolean res = false;
        if(memo.containsKey(key))   return memo.get(key);
        if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') { // s[i] match p[j]
            if(j + 1 < n && p.charAt(j + 1) == '*')     res = dp(s, i + 1, p, j) || dp(s, i, p, j + 2); // p 不变，s 后移；s 到头，p 移动 2 位 
            else    res = dp(s, i + 1, p, j + 1);
        } else { // s[i] dismatch p[j]
            if(j + 1 < n && p.charAt(j + 1) == '*')     res = dp(s, i, p, j + 2); // 不匹配后移 2 位
            else    res = false;
        }

        memo.put(key, res);
        return res;
    }
}
```

- 解法二：“自底向上”的迭代解法（dp 数组）

Todo

#### 11. 盛最多水的容器

[题目链接](https://leetcode-cn.com/problems/container-with-most-water/)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

```java
class Solution {
    public int maxArea(int[] height) {
        // 双指针向中间靠拢
        // 矩形面积 = (right - left) * min(height[left], height[right])
        // 左右两边较小的那个向中间靠拢
        int left = 0, right = height.length - 1;
        int res = 0;
        while(left < right) {
            int width = right - left;
            if(height[left] < height[right]) {
                int h = height[left];
                res = res > h * width ? res : h * width;
                left++;
            } else {
                int h = height[right];
                res = res > h * width ? res : h * width;
                right--;
            }
        }
        return res;
    }
}
```

#### 13. 罗马数字转整数

[题目链接](https://leetcode-cn.com/problems/roman-to-integer/)

```
输入: "IV"
输出: 4
```

```java
class Solution {
    public int romanToInt(String s) {
        Map<Character, Integer> map = new HashMap<>();
        map.put('I', 1);
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);

        // 前一个值小于后一个时做减法，否则做加法
        int sum = 0;

        for(int i = 0; i < s.length(); i++) {
            int curr = map.get(s.charAt(i));
            if(i + 1 < s.length()) {
                int next = map.get(s.charAt(i + 1));
                if(curr < next) {
                    sum -= curr;
                } else {
                    sum += curr;
                }
            }
        }

        sum += map.get(s.charAt(s.length() - 1));
        return sum;
    }
}
```

#### 14. 最长公共前缀

[题目链接](https://leetcode-cn.com/problems/longest-common-prefix/)

```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

取第一个字符串作为标准，遍历第一个字符串的每个字符，和剩下的所有字符串比较。
当字符串长度到头或当前位置字符不相等时返回 [0:该字符索引]

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs.length == 0)    return "";

        String s = strs[0];
        for(int i = 0; i < s.length(); i++) {
            for(int j = 1; j < strs.length; j++) {
                if(i >= strs[j].length() || strs[j].charAt(i) != s.charAt(i)) {
                    return s.substring(0, i);
                }
            }
        }
        return s;
    }
}
```

#### 46. 全排列 @回溯

[题目链接](https://leetcode-cn.com/problems/permutations/)

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> permute(int[] nums) {
        // 回溯：路径、选择列表、结束条件
        LinkedList<Integer> track = new LinkedList<>();
        backtrack(track, nums);
        return res;
    }

    public void backtrack(LinkedList<Integer> track, int[] nums) {
        // 结束条件
        if(track.size() == nums.length) {
            res.add(new LinkedList(track));
            return;
        }

        for(int i = 0; i < nums.length; i++) {
            // for 选择 in 选择列表:
            if(track.contains(nums[i]))     continue; 

            track.add(nums[i]); // 做选择
            backtrack(track, nums); // backtrack
            track.removeLast(); // 撤销选择
        }
    }
}
```

#### 17. 电话号码的字母组合 @回溯

[题目链接](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

```java
class Solution {
    // 路径、选择列表、结束条件
    List<String> res = new LinkedList<>();
    Map<Character, String> map = new HashMap<>();
    public List<String> letterCombinations(String digits) {
        if(digits.length() == 0)    return res;
        // 构建哈系表 
        map.put('2', "abc");
        map.put('3', "def");
        map.put('4', "ghi");
        map.put('5', "jkl");
        map.put('6', "mno");
        map.put('7', "pqrs");
        map.put('8', "tuv");
        map.put('9', "wxyz");

        StringBuilder track = new StringBuilder(); 
        backtrack(track, 0, digits); 
        return res;
    }
    
    // 路径：track 
    // 选择列表：map.get(digits.charAt(start))
    public void backtrack(StringBuilder track, int start, String digits) {
        // 结束条件
        if(start == digits.length()) {
            res.add(track.toString());
            return;
        }

        char digit = digits.charAt(start);
        String s = map.get(digit);
        // for 选择 in 选择列表 
        for(int i = 0; i < s.length(); i++) {
            track.append(s.charAt(i));
            backtrack(track, start + 1, digits);
            track.deleteCharAt(start);
        }
        
    }
}
```

#### 19. 删除链表的倒数第 N 个结点 @快慢指针

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

删除头节点时需要单独考虑，尾节点不用

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 边界条件： 链表不为 null 且 1 < n < list.size()
        ListNode slow = head, fast = head;
        // fast 先向前 n 个节点
        for(int i = 0; i < n; i++) {
            fast = fast.next;
        }
        // 删除头节点
        if(fast == null)    return slow.next;

        // fast 和 slow 同时前进
        // fast 指向最后一个节点时，slow 指向倒数第 n+1 个节点
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next;
        }

        slow.next = slow.next.next;
        return head;
    }
}
```

#### 20. 有效的括号

[题目链接](https://leetcode-cn.com/problems/valid-parentheses/)

```
输入：s = "()[]{}"
输出：true
```

```java
class Solution {
    public boolean isValid(String s) {
        // 哈希表存储对应的括号
        // 用栈存储左括号
        // 返回 true 的情况：
        // 左右括号完全匹配 && 栈最终为空
        Deque<Character> stack = new LinkedList<>();
        Map<Character, Character> map = new HashMap<>();
        map.put('(', ')');
        map.put('[', ']');
        map.put('{', '}');
        for(int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if(map.containsKey(c)) {
                stack.push(map.get(c));
            } else {
                if(stack.isEmpty() || c != stack.peek()) {
                    return false;
                }
                stack.pop();
            }
            
        }
        return stack.isEmpty();
    }
}
```

#### 21. 合并两个有序链表

[题目链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // 哨兵节点
        ListNode head = new ListNode(0);
        ListNode curr = head;
        // 两个链表都不空时比较节点的值并且后移
        while(l1 != null && l2 != null) {
            if(l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
                curr = curr.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
                curr = curr.next;
            }
        }
        // 不空的那个链表接在后面即可
        if(l1 != null)  curr.next = l1;
        if(l2 != null)  curr.next = l2;
        return head.next;
    }
}
```

#### 22. 括号生成 @回溯

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

```java
class Solution {
    List<String> res = new ArrayList<>();
    public List<String> generateParenthesis(int n) {
        StringBuilder track = new StringBuilder();
        backtrack(track, 0, 0, n);
        return res;
    }

    public void backtrack(StringBuilder track, int left, int right, int n) {
        // 结束条件
        if(track.length() == n * 2) {
            res.add(track.toString());
            return;
        }
        // for 选择 in 选择列表 
        // 这里的选择只有两个：添加左括号或右括号
        // 添加左括号前提：左括号数量 < n
        // 添加右括号前提：右括号数量 < 左括号数量
        if(left < n) {
            track.append('(');
            backtrack(track, left + 1, right, n);
            track.deleteCharAt(track.length() - 1);
        }
        if(right < left) {
            track.append(')');
            backtrack(track, left, right + 1, n);
            track.deleteCharAt(track.length() - 1);
        }
    }
}
```

#### 23. 合并 K 个升序链表

[题目链接](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // 布尔数组统计链表是否为空
        // isValid 标志是否所有链表都空
        // 比较非空链表的值并后移  min idx
        ListNode head = new ListNode(0);
        ListNode curr = head;
        int length = lists.length;
        // 初始条件下链表的状态
        boolean[] valid = new boolean[length];
        boolean isValid = false;
        for(int i = 0; i < length; i++) {
            if(lists[i] == null)    valid[i] = false;
            else    valid[i] = true;
        }
        isValid = validCount(valid);

        while(isValid) {
            int min = Integer.MAX_VALUE;
            int idx = -1;
            for(int i = 0; i < length; i++) {
                if(valid[i]) { // 该链表不空
                    if(lists[i].val < min) {
                        min = lists[i].val;
                        idx = i;
                    }
                }
            }
            curr.next = lists[idx];
            curr = curr.next;
            lists[idx] = lists[idx].next;
            valid[idx] = lists[idx] != null;

            isValid = validCount(valid);
        }
        return head.next;
    }

    public boolean validCount(boolean[] valid) {
        for(int i = 0; i < valid.length; i++) {
            // 有一个 true 就继续合并
            if(valid[i])    return true;
        }
        return false;
    } 
}
```

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // 使用优先队列存储链表的所有值，然后组成链表
        Queue<ListNode> queue = new PriorityQueue<>(            
            new Comparator<ListNode>() {
                @Override
                public int compare(ListNode l1, ListNode l2) {
                    return l1.val - l2.val;
                }
            });
        // Queue<ListNode> q = new PriorityQueue<>((x,y)->x.val-y.val);

        for(ListNode list : lists) {
            if(list != null)    queue.offer(list);
        }

        ListNode head = new ListNode(0);
        ListNode curr = head;
        while(!queue.isEmpty()) {
            curr.next = queue.poll();
            curr = curr.next;
            if(curr.next != null) {
                queue.offer(curr.next);
            }
        }
        return head.next;
    }
}
```

#### 78. 子集 @回溯

[题目链接](https://leetcode-cn.com/problems/subsets/)

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

```java
class Solution {
    public List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> subsets(int[] nums) {
        LinkedList<Integer> track = new LinkedList<>();
        backtrack(track, 0, nums);
        return res;
    }
    // 路径：track 
    // 选择列表：nums[start:]
    public void backtrack(LinkedList<Integer> track, int start, int[] nums) {
        // 结束条件
        res.add(new LinkedList(track));
        // for 选择 in 选择列表
        // start 只用在开头控制递归
        for(int i = start; i < nums.length; i++) { 
            track.add(nums[i]);
            backtrack(track, i + 1, nums);
            track.removeLast();
        }
    }
}
```

#### 26. 删除排序数组中的重复项

[题目链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

要尽量避免在中间删除元素，想办法把元素放到最后，删除的时间复杂度变成 O(1)

```
给定数组 nums = [1,1,2], 
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
你不需要考虑数组中超出新长度后面的元素。
```

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        // 快慢指针
        // slow 指向当前最后一个不重复的数
        // nums[slow] != nums[fast] 时 nums[++slow] = nums[fast];
        if(nums.length == 0)    return 0;
        int slow = 0, fast = 1;
        for(; fast < nums.length; fast++) {
            if(nums[slow] != nums[fast]) {
                slow++;
                nums[slow] = nums[fast];
            }
        }
        return slow + 1;
    }
}
```

#### 28. 实现 strStr ()

[题目链接](https://leetcode-cn.com/problems/implement-strstr/)

```
输入: haystack = "hello", needle = "ll"
输出: 2
```

```java
class Solution {
    public int strStr(String haystack, String needle) {
        // 字符串匹配问题
        // 双指针遍历两个数组
        // if(haystack.charAt(i + j) != needle.charAt(j))  break;
        if(haystack == null || needle == null)  return -1;
        int h = haystack.length(), n = needle.length();
        if(n == 0)  return 0;
        for(int i = 0; i < h - n + 1; i++) {
            for(int j = 0; j < n; j++) {
                if(haystack.charAt(i + j) != needle.charAt(j))  break;
                if(j + 1 == n)  return i;
            }
        }
        return -1;
    }
}
```

另解：KMP 算法

#### 29. 两数相除

[题目链接](https://leetcode-cn.com/problems/divide-two-integers/)

```
输入: dividend = 10, divisor = 3
输出: 3
解释: 10/3 = truncate(3.33333..) = truncate(3) = 3
```

```java
class Solution {
    // dividendL 右移减小
    // result 左移增大
    // dividendL - (divisorL << i) [10 - 3*3]
    // 转换为 long 是为了处理 dividend == Integer.MIN_VALUE
    public int divide(int dividend, int divisor) {
        if(dividend == 0)   return 0;
        // 边界溢出
        if(dividend == Integer.MIN_VALUE && divisor == -1)  return Integer.MAX_VALUE; 
        // 判断正负
        boolean sign = (dividend ^ divisor) >= 0; // 1 ^ 1 = 0
        long dividendL = Math.abs((long)dividend);
        long divisorL = Math.abs((long)divisor);
        int result = 0;
        for(int i = 31; i >= 0; i--) {
            if((dividendL >> i) >= divisorL) { // divisorL * 2^i <= dividendL
                result += 1 << i;
                dividendL -= divisorL << i;
            }
        }
        return sign ? result : -result;
    }
}
```

#### 33. 搜索旋转排序数组 @二分查找

[题目链接](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

旋转数组的思路为 `判断有序的那一部分`，由于不确定 target 和有序数组的关系，所以需要进一步判断 target 是否在有序数组内。

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] == target) {
                return mid;
            } 
            if(nums[left] <= nums[mid]) { // 前半部分有序 切记 <= 
                if(nums[left] <= target && nums[mid] > target) { // nums[left] <= target < nums[mid]
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else { // 后半部分有序
                if(nums[mid] < target && nums[right] >= target) { // nums[mid] < target <= nums[right]
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```

#### 34. 在排序数组中查找元素的第一个和最后一个位置 @二分查找

[题目链接](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = new int[] {-1, -1};
        if(nums.length == 0)    return res;
        int left = 0, right = nums.length - 1;
        // 第一个位置（即左侧边界）
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] > target)  right = mid - 1;
            else if(nums[mid] < target)     left = mid + 1;
            else if(nums[mid] == target)    right = mid - 1;
        }
        // right 指向小于 target 的最大值，left 指向大于等于 target 的最小值
        // 左侧边界用 left，右侧边界用 right
        if(left >= nums.length || nums[left] != target)  return res;
        res[0] = left;
        for(int i = left; i < nums.length; i++) {
            if(nums[i] == target)  res[1] = i;
            else break;
        }
        return res;
    }
}
```

#### 69. x 的平方根 @二分查找

[题目链接](https://leetcode-cn.com/problems/sqrtx/)

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

```java
class Solution {
    public int mySqrt(int x) {
        // 采用二分查找 0 <= k <= x
        // right <= k < left
        // 所以 return right
        int left = 0, right = x;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            long square = (long)mid * mid; // 注意刚开始 (x/2) 的平方可能会溢出
            if(square == x)     return mid;
            else if(square > x) right = mid - 1;
            else if(square < x) left = mid + 1;
        }  
        return right;
    }
}
```



#### 36. 有效的数独

[题目链接](https://leetcode-cn.com/problems/valid-sudoku/)

```
输入:
[
  ["5","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
输出: true
```

根据题目的规则

1. 数字 `1-9` 在每一行只能出现一次。
2. 数字 `1-9` 在每一列只能出现一次。
3. 数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。

每一行、每一列、每个 Box 记录数字出现的次数，不符合条件时返回 false，最后返回 true

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        // Java boolean 默认 false
        boolean[][] row = new boolean[9][9];
        boolean[][] col = new boolean[9][9];
        boolean[][] box = new boolean[9][9];

        for(int i = 0; i < 9; i++) {
            for(int j = 0; j < 9; j++) {
                if(board[i][j] == '.')  continue;
                int num = board[i][j] - '1';
                if(row[i][num])     return false;
                if(col[j][num])     return false;
                // idx = 行数 × 3 + 列数
                // 3 × 3 的九宫格
                // 行数 = i / 3；列数 = j / 3
                int boxIdx = j / 3 + (i / 3) * 3; 
                if(box[boxIdx][num])    return false;
                // 修改对应行、列、box 的值
                row[i][num] = true;
                col[j][num] = true;
                box[boxIdx][num] = true;
            }
        }
        return true;
    }
}
```

#### 38. 外观数列

[题目链接](https://leetcode-cn.com/problems/count-and-say/)

```java
class Solution {
    // 迭代
    // 计算出前第 n-1 个的答案
    // 按照规则计算第 n 个
    public String countAndSay(int n) {
        String res = "1";
        for(int i = 2; i <= n; i++) {
            StringBuilder sb = new StringBuilder();
            int count = 1;
            for(int j = 0; j < res.length(); j++) { 
                while(j + 1 < res.length() && res.charAt(j) == res.charAt(j + 1)) {
                    count++;
                    j++;
                }
                sb.append(count).append(res.charAt(j));
                count = 1; 
            }
            res = sb.toString();
        }
        return res;
    }
}
```

#### 41. 缺失的第一个正数 #Hard

[题目链接](https://leetcode-cn.com/problems/first-missing-positive/)

```
输入：nums = [1,2,0]
输出：3
```

- 解法一：Set
- 解法二：原地哈希（利用数组的索引）

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        // 负数和 0 转换为 n+1
        for(int i = 0; i < n; i++) {
            if(nums[i] <= 0)    nums[i] = n + 1;
        }
        // if abs(num) <= nums.length
        // 将该位置的值变为负数
        // 因为前面会改变后面的值，所以要用绝对值 ！！！
        for(int i = 0; i < n; i++) {
            int num = Math.abs(nums[i]);
            if(num <= n)    nums[num - 1] = -Math.abs(nums[num - 1]);
        }
        for(int i = 0; i < n; i++) {
            if(nums[i] > 0) return i + 1;
        }
        return n + 1;
    }
}
```

- 解法三：置换元素

```
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        // 置换 nums[i] = i + 1 
        for(int i = 0; i < n; i++) {
            // 位置 i 上的元素一直交换，直到不符合 while 条件
            while(nums[i] > 0 && nums[i] <=n && nums[i] != nums[nums[i]-1]) {
                int tmp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = tmp; 
            }
        }
        for(int i = 0; i < n; i++) {
            if(nums[i] != i + 1)    return i + 1;
        }
        return n + 1;
    }
}
```

#### 42. 接雨水

[题目链接](https://leetcode-cn.com/problems/trapping-rain-water/)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

题目的意思就是当前列能接的雨水 num = min(lmax, rmax) - height[i]

- 解法一：备忘录存储所有位置的 lmax 和 rmax

```java
class Solution {
    public int trap(int[] height) {
        if(height.length == 0)  return 0;
        int n = height.length;
        int res = 0;
        // 备忘录 
        // 用两个数组存储 当前位置左右两边的最大值
        int[] left = new int[n];
        int[] right = new int[n];
        left[0] = height[0];
        right[n - 1] = height[n - 1];
        for(int i = 1; i < n; i++)  left[i] = Math.max(left[i - 1], height[i]);
        for(int i = n - 2; i >= 0; i--) right[i] = Math.max(right[i + 1], height[i]);

        for(int i = 1; i < n - 1; i++) {
            res += Math.min(left[i], right[i]) - height[i];
        }
        return res;
    }
}
```

- 解法二：双指针法。lmax < rmax 就左移，反之右移

```java
class Solution {
    public int trap(int[] height) {
        if(height.length == 0)  return 0;
        int left = 0, right = height.length - 1;
        int lmax = height[0];
        int rmax = height[height.length - 1];
        int res = 0;
        while(left <= right) {
            lmax = Math.max(lmax, height[left]);
            rmax = Math.max(rmax, height[right]);
            if(lmax < rmax) {
                res += lmax - height[left];
                left++;
            } else {
                res += rmax - height[right];
                right--;
            }
        }
        return res;
    }
}
```

#### 44. 通配符匹配

[题目链接](https://leetcode-cn.com/problems/wildcard-matching/)

```
输入:
s = "aa"
p = "*"
输出: true
解释: '*' 可以匹配任意字符串。
```

该题和第 10 题正则表达式类似，比第 10 题简单一些。

- 解法一：递归 +　备忘录

```java
class Solution {
    Map<String, Boolean> memo = new HashMap<>();
    public boolean isMatch(String s, String p) {
        if(s == null || p == null)  return false;
        return dp(s, 0, p, 0);
    }

    // dp(s, i, p, j):s[i:] match p[j:]
    public boolean dp(String s, int i, String p, int j) {
        int m = s.length();
        int n = p.length();
        // base case
        if(j == n) return i == m;
        if(i == m) {
            if(j == n)  return true;
            while(j != n) {
                if(p.charAt(j) != '*')  return false;
                j++;
            }
            return true;
        }
        boolean res = true;
        // choice 
        String key = i + "," + j;
        if(memo.containsKey(key))  return memo.get(key);
        if(p.charAt(j) == '*') {  // 匹配 0 个或多个字符
            res = dp(s, i, p, j + 1) || dp(s, i + 1, p, j);
        } else { // 匹配单个字符
            if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '?') { // 匹配单个字符
                res = dp(s, i + 1, p, j + 1);
            } else { // 不匹配
                res = false;
            }
        }
        memo.put(key, res);
        return res;
    }
}
```

#### 48. 旋转图像

[题目链接](https://leetcode-cn.com/problems/rotate-image/)

- 解法一：辅助数组

翻转规律 `matrix[row][col] -> matrix[col][n-row-1]`

- 翻转替代旋转

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 水平翻转 row 0-> n/2
        // matrix[row][col] -> matrix[n-row-1][col]
        for(int i = 0; i < n / 2; i++) {
            for(int j = 0; j < n; j++) {
                int tmp = matrix[n - i - 1][j];
                matrix[n - i - 1][j] = matrix[i][j];
                matrix[i][j] = tmp;
            }
        }
        // 对角线翻转 col 0->row
        // matrix[row][col] -> matrix[col][row]
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int tmp = matrix[j][i];
                matrix[j][i] = matrix[i][j];
                matrix[i][j] = tmp;
            }
        }
    }
}
```

#### 49. 字母异位词分组

[题目链接](https://leetcode-cn.com/problems/group-anagrams/)

- 解法一：对字符串进行排序，用哈希表存储

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // 对字符串进行排序
        ArrayList<List<String>> res = new ArrayList<>();
        if(strs == null)    return res;

        Map<String, List<String>> map = new HashMap<>();
        for(String s : strs) {
            char[] arr = s.toCharArray();
            Arrays.sort(arr);
            String key = new String(arr);
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(s);
            map.put(key, list);
        }
        res.addAll(map.values());
        return res;
    }
}
```

> 注：不能使用 char[] 作为 key，计算哈希值的时候用的是内存地址而不是根据内容

- 解法二：计数

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // 对字符进行计数，key 为 26 个字符出现次数的拼接
        ArrayList<List<String>> res = new ArrayList<>();
        if(strs == null)    return res;

        Map<String, List<String>> map = new HashMap<>();
        for(String s : strs) {
            int[] arr = new int[26];
            Arrays.fill(arr, 0);
            for(int i = 0; i < s.length(); i++) {
                arr[s.charAt(i) - 'a']++;
            }
            String key = Arrays.toString(arr);
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(s);
            map.put(key, list);
        }
        res.addAll(map.values());
        return res;
    }
}
```

> Arrays.toString()：数组 -> String

#### 50. Pow(x, n)

[题目链接](https://leetcode-cn.com/problems/powx-n/)

```
输入：x = 2.00000, n = 10
输出：1024.00000
```

- 解法一：快速幂 + 递归

n < 0 时传入 -n，转换为 long 型避免 -n 溢出

```java
class Solution {
    public double myPow(double x, int n) {
        long N = n;
        // int MIN_VALUE 取负值会溢出
        return N > 0 ? quickMul(x, N) : 1 / quickMul(x, -N);
    }

    public double quickMul(double x, long n) {
        if(n == 0)  return 1.0;
        double y = quickMul(x, n / 2);
        return (n & 1) == 0 ? y * y : y * y * x;
    }
}
```

- 解法二：快速幂 + 迭代（不是很明白）

```java
class Solution {
    public double myPow(double x, int n) {
        long N = n;
        return N > 0 ? quickMul(x, N) : 1 / quickMul(x, -N);
    }

    public double quickMul(double x, long n) {
        double res = 1;
        double tmp = x;

        while(n > 0) {
            if(n % 2 == 1)  res *= tmp;
            tmp *=  tmp;
            n /= 2;
        }
        return res;
    }
}
```

#### 53. 最大子序和

[题目链接](https://leetcode-cn.com/problems/maximum-subarray/)

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

- 解法一：动态规划

```java
class Solution {
    public int maxSubArray(int[] nums) {
        // 当前子序列的最优解
        // 要么加前面的序列要么舍弃
        int curr = 0; 
        // dp[i] 当前位置的最优值
        int res = nums[0];
        for(int num : nums) {
            curr = Math.max(num, curr + num);
            res = Math.max(res, curr);
        }
        return res;
    }
}
```

- 解法二：贪心法

```java
class Solution {
    public int maxSubArray(int[] nums) {
        // curr 为每一次的当前最优解
        int curr = 0;
        int res = nums[0];
        for(int num : nums) {
            curr += num;
            res = Math.max(curr, res);
            if(curr < 0)    curr = 0;
        }
        return res;
    }
}
```

#### 54. 螺旋矩阵

[题目链接](https://leetcode-cn.com/problems/spiral-matrix/)

```
顺时针旋转
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

找到旋转的规律，然后按照规律来写就可以

右 `matrix[top][left]` -> `matrix[top][right]`

下 `matrix[top][right]` -> `matrix[bottom][right]`

if(left < right && top < bottom) 因为只有一行或一列的话是不会执行左和上的。左上规律同理可得

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if(matrix == null || matrix.length == 0 || matrix[0].length == 0)   return res;
        int rows = matrix.length, cols = matrix[0].length;
        int top = 0, bottom = rows - 1;
        int left = 0, right = cols - 1;
        while(left <= right && top <= bottom) {
            for(int i = left; i <= right; i++)  res.add(matrix[top][i]); // 右
            for(int j = top + 1; j <= bottom; j++)  res.add(matrix[j][right]); // 下
            if(left < right && top < bottom) {
                for(int i = right - 1; i >= left; i--)  res.add(matrix[bottom][i]); // 左
                for(int j = bottom - 1; j >= top + 1; j--)  res.add(matrix[j][left]); // 上
            }
            left++;
            right--;
            top++;
            bottom--;
        }
        return res;
    }
}
```

#### 55. 跳跃游戏 @贪心

[题目链接](https://leetcode-cn.com/problems/jump-game/)

```
判断能否跳到最后一个位置
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

贪心算法：每次选择当前最优解，通过每一步的最优解更新全局最优解

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        // 当前最优解
        int farthest = 0;
        for(int i = 0; i < n - 1; i++) {
            int curr = i + nums[i];
            farthest = Math.max(farthest, curr);
            // 当前位置的最优解没法再前进了
            // 也就无法到达最后一个位置
            if(farthest <= i)   return false;
        }
        return farthest >= n - 1;
    }
}
```

#### 45. 跳跃游戏 II

[题目链接](https://leetcode-cn.com/problems/jump-game-ii/)

```输入: [2,3,1,1,4]
一定能跳到最后，判断最少次数
输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```

- 解法一：动态规划 递归 + 记事本

```java
class Solution {
    public int jump(int[] nums) {
        int[] memo = new int[nums.length];
        // 最多跳跃 n - 1 次
        Arrays.fill(memo, nums.length);
        return dp(nums, 0, memo);
    }

    // dp(nums, p, memo)    p 位置到末尾的最小次数
    // 状态：p 所在位置
    // 状态转移：p -> p + i
    public int dp(int[] nums, int p, int[] memo) {
        int n = nums.length;
        // base case
        if(p >= n - 1)  return 0;
        // choice
        if(memo[p] != n)    return memo[p];

        int steps = nums[p];
        for(int i = 1; i <= steps; i++) {
            int subRes = dp(nums, p + i, memo);
            memo[p] = Math.min(memo[p], subRes + 1);
        }
        return memo[p];
    }
}
```

- 解法二：动态规划 迭代

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        if(n == 1 || n == 0)    return 0;
        // dp[i] i -> n-1 最小次数
        int[] dp = new int[n];
        // base case
        Arrays.fill(dp, n);
        dp[n - 1] = 0;
        // choice
        for(int i = n - 2; i >= 0; i--) {
            int steps = nums[i];
            for(int j = 1; j <= steps; j++) {
                // 超过最大范围就跳出
                if(i + j > n - 1)   break; 
                if(dp[i + j] != n)  dp[i] = Math.min(dp[i], 1 + dp[i + j]);
            }
        }
        return dp[0];
    }
}
```

上述两个方法都超时

- 解法三：贪心算法

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        // i -> end 每一步的局部最优解，最后就是全局最优解
        int i = 0;
        int end = 0;
        
        int jump = 0;
        int farthest = 0;
        while(end < n - 1) {
            farthest = Math.max(farthest, i + nums[i]);
            if(i == end) {
                jump++;
                end = farthest;
            }
            i++;
        }
        return jump;
    }
}
```

> - 时间复杂度比较
>
> DP：O(n^2)
>
> 贪心：O(n)
>
> - 分析
>
> 贪心没有像动态规划一样在每一步里比较所有可能的结果，而是在每一步直接选择最有可能的结果，所用时间更少。

#### 56. 合并区间

[题目链接](https://leetcode-cn.com/problems/merge-intervals/)

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

```java
// 先排序然后合并
class Solution {
    public int[][] merge(int[][] intervals) {
        // 对数组进行排序，按照第一个元素升序排列
        Arrays.sort(intervals, (arr1, arr2) -> {
            return arr1[0] - arr2[0];
        });

        List<int[]> res = new ArrayList<>();
        int[] curr = intervals[0];
        int i = 0;
        // 当前数组和后面比较
        // 重叠就更新数组
        // 不重叠就加到结果中并更新数组
        while(i + 1 < intervals.length) {
            if(curr[1] < intervals[i + 1][0]) { // [2,6] [8,10]
                res.add(curr);
                curr = intervals[i + 1];
            } else { // [1,3] [2,6]
                curr[1] = Math.max(curr[1], intervals[i + 1][1]); // [1,4] [2,3] 注意这种情况
            }
            i++;
        }
        res.add(curr); // 不管是否重叠最后都要加上
        
        int[][] ans = res.toArray(new int[res.size()][]);
        return ans;
    }
}
```

> Arrays.sort(array, (a, b) -> {return a - b;});

#### 62. 不同路径

[题目链接](https://leetcode-cn.com/problems/unique-paths/)

```java
// DP
class Solution {
    public int uniquePaths(int m, int n) {
        // dp[i][j] dp[0][0] -> dp[i][j] tracks
        int[][] dp = new int[m][n];
        // base case 
        for(int i = 0; i < m; i++)  dp[i][0] = 1;
        for(int i = 0; i < n; i++)  dp[0][i] = 1;

        // choice
        // dp[i][j] = dp[i-1][j] + dp[i][j-1]
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
}

// 状态压缩 去掉第一个维度
class Solution {
    public int uniquePaths(int m, int n) {
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                dp[j] += dp[j-1];
            }
        }
        return dp[n-1];
    }
}
```

#### 66. 加一

[题目链接](https://leetcode-cn.com/problems/plus-one/)

```
输入：digits = [1,2,3]
输出：[1,2,4]
解释：输入数组表示数字 123。
```

```java
class Solution {
    public int[] plusOne(int[] digits) {
        // 最后一位是不是 9
        // 是 9 的话一直进位
        // 超过数组容量新建数组
        int[] res = digits;
        int n = res.length;
        if(res[n - 1] != 9) {
            res[n - 1]++;
        } else {
            int i = n - 1;
            for(; i >= 0; i--) {
                if(res[i] == 9) res[i] = 0;
                else break;
            }
            if(i >= 0)  res[i]++;
            else {
                res = new int[n + 1];
                res[0] = 1;
            }
        }
        return res;
    }
}
```

#### 70. 爬楼梯

[题目链接](https://leetcode-cn.com/problems/climbing-stairs/)

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

```java
class Solution {
    // 迭代
    // dp[i] = dp[i - 1] + dp[i - 2]
    public int climbStairs(int n) {
        if(n == 1)  return 1;
        if(n == 2)  return 2;
        int pre1 = 1, pre2 = 2;
        int res = 0;
        for(int i = 3; i <= n; i++) {
            res = pre1 + pre2;
            pre1 = pre2;
            pre2 = res;
        }
        return res;
    }
}
```

#### 73. 矩阵置零

[题目链接](https://leetcode-cn.com/problems/set-matrix-zeroes/)

```
输入: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
输出: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```

- 解法一：记录元素所在行列

时间复杂度：O(m*n)

空间复杂度：O(m+n)

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        if(matrix == null)  return;

        Set<Integer> rows = new HashSet<>();
        Set<Integer> cols = new HashSet<>();

        int row = matrix.length;
        int col = matrix[0].length;
        for(int i = 0; i < row; i++) {
            for(int j = 0; j < col; j++) {
                if(matrix[i][j] == 0) {
                    rows.add(i);
                    cols.add(j);
                }
            }
        }

        for(int r : rows)   Arrays.fill(matrix[r], 0);
        for(int i = 0; i < row; i++) {
            for(int c : cols) {
                matrix[i][c] = 0;
            }
        }
    }
}
```

- 解法二：将元素所在行列赋值为范围外的值

时间复杂度：O(m*n)

空间复杂度：O(1)

- 解法三：第一行第一列单独讨论

因为 `matrix[0][0]` 关系到第一行和第一列，所以需要额外考虑。设置 isCol 标志该列。

时间复杂度：O(m*n)

空间复杂度：O(1)

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        if(matrix == null || matrix.length == 0)    return;

        int rows = matrix.length;
        int cols = matrix[0].length;
        boolean isCol = false;

        for(int i = 0; i < rows; i++) {
            // 第一列有 0，该列需要置 0
            // 要放在前面，否则可能该列值会被“污染“变为 0
            if(matrix[i][0] == 0)   isCol = true;

            for(int j = 1; j < cols; j++) {
                if(matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        for(int i = 1; i < rows; i++) {
            for(int j = 1; j < cols; j++) {
                if(matrix[i][0] == 0 || matrix[0][j] == 0)  matrix[i][j] = 0;
            }
        }

        if(matrix[0][0] == 0)   Arrays.fill(matrix[0], 0);
        if(isCol) {
            for(int i = 0; i < rows; i++)   matrix[i][0] = 0;
        }
    }
}
```

#### 75. 颜色分类

[题目链接](https://leetcode-cn.com/problems/sort-colors/)

```
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

```java
class Solution {
    // 双指针
    public void sortColors(int[] nums) {
        int p0 = 0, p2 = nums.length - 1;
        for(int i = 0; i <= p2; i++) {
            while(i <= p2 && nums[i] == 2) {
                swap(nums, i, p2);
                p2--;
            }

            if(nums[i] == 0) {
                swap(nums, i, p0);
                p0++;
            }
        }
    }

    public void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

#### 76. 最小覆盖子串 #hard @滑动窗口

[题目链接](https://leetcode-cn.com/problems/minimum-window-substring/)

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

滑动窗口的四个问题：

1. 当移动 right 扩大窗口，即加入字符时，应该更新哪些数据？

2. 什么条件下，窗口应该暂停扩大，开始移动 left 缩小窗口？

3. 当移动 left 缩小窗口，即移出字符时，应该更新哪些数据？

4. 我们要的结果应该在扩大窗口时还是缩小窗口时进行更新？

```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        int valid = 0;
        int start = 0, length = Integer.MAX_VALUE;

        for(char c : t.toCharArray())   need.put(c, need.getOrDefault(c, 0) + 1);

        int left = 0, right = 0;
        while(right < s.length()) {
            // 1. right -> update window valid
            char c = s.charAt(right);
            right++;
            if(need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if(window.get(c).equals(need.get(c)))   valid++;
            }

            // 2. stop -> condition
            while(valid == need.size()) {
                // result update
                if(right - left < length) {
                    start = left;
                    length = right - left;
                }

                // 3. left -> update window valid
                char d = s.charAt(left);
                left++;
                if(need.containsKey(d)) {
                    if(window.get(d).equals(need.get(d)))   valid--;
                    window.put(d, window.get(d) - 1);
                }
            }
        }

        return length == Integer.MAX_VALUE ? "" : s.substring(start, start + length);
    }
}
```

#### 79. 单词搜索 @回溯

[题目链接](https://leetcode-cn.com/problems/word-search/)

```
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
```

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        int rows = board.length;
        int cols = board[0].length;
        boolean[][] visited = new boolean[rows][cols];

        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                boolean flag = backtrack(board, i, j, visited, 0, word);
                if(flag)    return true;
            }
        }
        return false;
    }

    public boolean backtrack(char[][] board, int row, int col, boolean[][] visited, int start, String word) {
        // 跳出条件
        if(board[row][col] != word.charAt(start))   return false;
        else if(start == word.length() - 1)     return true;

        // 在选择列表做选择
        visited[row][col] = true;
        int[][] directions = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        for(int[] dire : directions) {
            int newRow = row + dire[0];
            int newCol = col + dire[1];
            // 移动后没有超过边界
            if(newRow >= 0 && newRow < board.length && newCol >= 0 && newCol < board[0].length) {
                if(!visited[newRow][newCol]) {
                    // backtrack
                    boolean flag = backtrack(board, newRow, newCol, visited, start + 1, word);
                    if(flag)    return true;
                }

            }
        }
        // 撤销选择
        visited[row][col] = false;
        return false;
    }
}
```

#### 88. 合并两个有序数组 @数组指针

[题目链接](https://leetcode-cn.com/problems/merge-sorted-array/)

```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 三指针：m-1 n-1 m+n-1 都左移
        // nums2 为空时返回，nums1 为空时继续给 nums2 赋值
        int p1 = m - 1, p2 = n - 1;
        int q = m + n - 1;
        while(p1 >= 0 && p2 >= 0) {
            if(nums2[p2] >= nums1[p1])   nums1[q--] = nums2[p2--];
            else    nums1[q--] = nums1[p1--];
        }

        if(p2 < 0)  return;
        if(p1 < 0) {
            while(p2 >= 0) {
                nums1[q--] = nums2[p2--];
            }
        }
        return;
    }
}
```

#### 91. 解码方法 @DP

之后可以考虑状态压缩

[题目链接](https://leetcode-cn.com/problems/decode-ways/)

```
输入：s = "12"
输出：2
解释：它可以解码为 "AB"（1 2）或者 "L"（12）。
```

```java
class Solution {
    public int numDecodings(String s) {
        // dp[i] s[:i] 的解码总数
        int n = s.length();
        int[] dp = new int[n+1];
        dp[0] = 1; dp[1] = 1;
        if(s.length() == 0 || s.charAt(0) == '0')    return 0; // 对应 d[0] dp[1]
        for(int i = 2; i <= n; i++) {
            // 当前位置为 0,看前面是不是 1 or 2
            if(s.charAt(i-1) == '0') {
                if(s.charAt(i-2) != '1' && s.charAt(i-2) != '2')    return 0;
                else    dp[i] = dp[i-2];
            } else { // 当前位置不是 0
                // 有两种组合的情形是：s[i-1]==1    s[i-1]==2 && 1<=s[i]<=6
                if(s.charAt(i-2) == '1' || (s.charAt(i-2) == '2' && s.charAt(i-1) >= '1' && s.charAt(i-1) <= '6'))  dp[i] = dp[i-1] + dp[i-2];
                else    dp[i] = dp[i-1];
            }
        }
        return dp[n];
    }
}
```

状态压缩

```java
class Solution {
    public int numDecodings(String s) {
        // dp[i] s[:i] 的解码总数
        int n = s.length();
        int res = 1;
        int pre2 = 1,  pre1 = 1; // pre2 和 pre1 分别对应 dp[i-2] dp[i-1]
        if(s.length() == 0 || s.charAt(0) == '0')    return 0; // 对应 d[0] dp[1]
        for(int i = 2; i <= n; i++) {
            // 当前位置为 0,看前面是不是 1 or 2
            if(s.charAt(i-1) == '0') {
                if(s.charAt(i-2) != '1' && s.charAt(i-2) != '2')    return 0;
                else res = pre2;
            } else { // 当前位置不是 0
                // 有两种组合的情形是：s[i-1]==1    s[i-1]==2 && 1<=s[i]<=6
                if(s.charAt(i-2) == '1' || (s.charAt(i-2) == '2' && s.charAt(i-1) >= '1' && s.charAt(i-1) <= '6'))  res = pre1 + pre2;
                else    res = pre1;
            }
            pre2 = pre1;
            pre1 = res;
        }
        return res;
    }
}
```

#### 94. 二叉树的中序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

```
输入：root = [1,null,2,3]
输出：[1,3,2]
```

```java
// 递归
class Solution {
    List<Integer> res = new ArrayList<>();
    public List<Integer> inorderTraversal(TreeNode root) {
        traverse(root);
        return res;
    }

    public void traverse(TreeNode root) {
        if(root == null)    return;
        traverse(root.left);
        res.add(root.val);
        traverse(root.right);
    }
}
```

```java
// 迭代
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        while(root != null || !stack.isEmpty()) {
            if(root != null) {
                stack.push(root);
                root = root.left;
            } else {
                root = stack.peek();
                stack.pop();
                res.add(root.val);
                root = root.right;
            }
        }
        return res;
    }
}
```

