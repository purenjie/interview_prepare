[LeetCode 精选 TOP 面试题](https://leetcode-cn.com/problemset/leetcode-top/)
<!-- TOC -->

- [1. 两数之和](#1-两数之和)
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

<!-- /TOC -->
#### 1. 两数之和

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
        // 将所有值存入哈系表
        for(int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        // 遍历数组寻找 twoSum
        for(int i = 0; i < nums.length; i++) {
            int other = target - nums[i];
            // 哈系表中有 other 且不是 nums[i] 本身
            if(map.containsKey(other) && map.get(other) != i) {
                return new int[] {i, map.get(other)};
            }
        }
        return new int[] {-1, -1};
    }
}
```

> [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

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
- 

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

Todo:

第 4 题解法 3 梳理

回文字串/子序列关联及解法