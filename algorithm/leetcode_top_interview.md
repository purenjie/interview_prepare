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

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s == null)   return -1; // 题目没有这种情况
        // 用哈系表存储当前 char 和最新的索引 index
        // 最大长度为 max(max_len, currlen)
        // cur_len = index - start + 1
        // start = start(未重复或重复 char 索引 < start) / 重复索引 + 1
        Map<Character, Integer> map = new HashMap<>();
        int maxLen = 0;
        int curLen = 0;
        int start = 0;
        for(int i = 0; i < s.length(); i++) {
            if(map.containsKey(s.charAt(i))) {
                start = start > map.get(s.charAt(i)) ? start : map.get(s.charAt(i)) + 1;
            } 
            map.put(s.charAt(i), i);
            curLen = i - start + 1;
            maxLen = maxLen > curLen ? maxLen : curLen;
        }
        return maxLen;
    }
}
```

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



