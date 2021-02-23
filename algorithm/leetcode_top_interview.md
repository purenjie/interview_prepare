[LeetCode 精选 TOP 面试题](https://leetcode-cn.com/problemset/leetcode-top/)

#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        if(nums == null || nums.length == 0)    return null;
        // HashMap 存储，key 为 target - i， value 为 i 对应的索引，最后返回两个数的索引
        Map<Integer, Integer> map = new HashMap<>();
        int[] res = new int[2];
        for(int i = 0; i < nums.length; i++) {
            if(map.containsKey(nums[i])) {
                res[0] = map.get(nums[i]);
                res[1] = i;
                return res;
            }
            map.put(target - nums[i], i);
        }
        return null;
    }
}
```

#### [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

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

#### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

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

#### [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

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

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

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

#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

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

#### [8. 字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

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

#### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

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

Todo:

第 4 题解法 3 梳理

回文字串/子序列关联及解法