
<!-- TOC -->

- [98. 验证二叉搜索树 @递归 @中序遍历](#98-验证二叉搜索树-递归-中序遍历)
- [101. 对称二叉树](#101-对称二叉树)
- [102. 二叉树的层序遍历 @层序遍历](#102-二叉树的层序遍历-层序遍历)
- [103. 二叉树的锯齿形层序遍历 @层序遍历 @队列 @栈](#103-二叉树的锯齿形层序遍历-层序遍历-队列-栈)

<!-- /TOC -->

#### 98. 验证二叉搜索树 @递归 @中序遍历

[题目链接](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```
输入:
    2
   / \
  1   3
输出: true
```

```java
// 递归
class Solution {
    public boolean isValidBST(TreeNode root) {
        return helper(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    public boolean helper(TreeNode root, long min, long max) {
        if(root == null)    return true;
        if(root.val <= min || root.val >= max)  return false;
        return helper(root.left, min, root.val) && helper(root.right, root.val, max);
    }
}

// 二叉搜索树的中序遍历是递增序列
class Solution {
    long num = Long.MIN_VALUE; // int 会出错，有 Integer.MIN_VALUE
    boolean res = true;
    public boolean isValidBST(TreeNode root) {
        traverse(root);
        return res;
    }

    public void traverse(TreeNode root) {
        if(root == null)    return;
        traverse(root.left);
        if(root.val > num)  num = root.val;
        else {
            res = false;
            return;
        }
        traverse(root.right);
    }
}

class Solution {
    public boolean isValidBST(TreeNode root) {
        TreeNode tmp = root;
        Deque<TreeNode> stack = new LinkedList<>();
        long num = Long.MIN_VALUE;
        while(tmp != null || !stack.isEmpty()) {
            if(tmp != null) {
                stack.push(tmp);
                tmp = tmp.left;
            } else {
                tmp = stack.peek();
                stack.pop();
                if(tmp.val <= num)  return false;
                num = tmp.val;
                tmp = tmp.right;
            }
        }
        return true;
    }
}
```

#### 101. 对称二叉树

[题目链接](https://leetcode-cn.com/problems/symmetric-tree/)

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

```java
// 递归
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSymmetric(root, root);
    }
	
    // 函数的意义：left 和 right 两棵树是对称的
    public boolean isSymmetric(TreeNode left, TreeNode right) {
        if(left == null && right == null)   return true;
        if(left == null || right == null)   return false;
        return left.val == right.val && isSymmetric(left.left, right.right) && isSymmetric(left.right, right.left);
    }
}

// 迭代
// 处理的过程中根节点确实很难处理，这样的话把根节点看做子节点会更好
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null)    return true;
        Queue<TreeNode> q1 = new LinkedList<>();
        Queue<TreeNode> q2 = new LinkedList<>();
        q1.offer(root);
        q2.offer(root);
        while(!q1.isEmpty()) {
            int size = q1.size();
            TreeNode node1, node2;
            StringBuilder sb1 = new StringBuilder();
            StringBuilder sb2 = new StringBuilder();
            for(int i = 0; i < size; i++) {
                node1 = q1.poll();
                node2 = q2.poll();
                if(node1.left != null) {
                    q1.offer(node1.left);
                    sb1.append(node1.left.val);
                } else  sb1.append("#");
                if(node1.right != null) {
                    q1.offer(node1.right);
                    sb1.append(node1.right.val);
                } else  sb1.append("#");
                if(node2.right != null) {
                    q2.offer(node2.right);
                    sb2.append(node2.right.val);
                } else  sb2.append("#");
                if(node2.left != null) {
                    q2.offer(node2.left);
                    sb2.append(node2.left.val);
                } else  sb2.append("#");
            }
            if(!sb1.toString().equals(sb2.toString()))    return false;
        }
        return true;
    }
}

// 更好的解法
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSymmetric(root, root);
    }

    public boolean isSymmetric(TreeNode left, TreeNode right) {
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(left);
        q.offer(right);
        while(!q.isEmpty()) {
            left = q.poll();
            right = q.poll();

            if(left == null && right == null)   continue;
            if(left == null || right == null || left.val != right.val)  return false;

            q.offer(left.left);
            q.offer(right.right);
            q.offer(left.right);
            q.offer(right.left);
        }
        return true;
    }
}
```

#### 102. 二叉树的层序遍历 @层序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```
[
  [3],
  [9,20],
  [15,7]
]
```

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if(root == null)    return res;
        
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while(!q.isEmpty()) {
            int size = q.size();
            List<Integer> list = new ArrayList<>();
            for(int i = 0; i < size; i++) {
                root = q.poll();
                list.add(root.val);

                if(root.left != null)   q.offer(root.left);
                if(root.right != null)  q.offer(root.right);
            }
            res.add(new ArrayList(list));
        }
        return res;
    }
}
```

#### 103. 二叉树的锯齿形层序遍历 @层序遍历 @队列 @栈

[题目链接](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

```
[
  [3],
  [20,9],
  [15,7]
]
```

```java
class Solution { // self
    // 当反向遍历的时候，用栈存储然后弹出
    // 正向遍历按照正常层序遍历
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if(root == null)    return res;
        Queue<TreeNode> q = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        int flag = 1;
        q.offer(root);
        while(!q.isEmpty()) {
            int size = q.size();
            List<Integer> list = new ArrayList<>();
            if(flag == -1) {
                for(int i = 0; i < size; i++) {
                    root = q.poll();
                    stack.push(root);
                    if(root.left != null)   q.offer(root.left);
                    if(root.right != null)  q.offer(root.right);
                } 
                while(!stack.isEmpty()) {
                    list.add(stack.pop().val);
                }
            }
            else {
                for(int i = 0; i < size; i++) {
                    root = q.poll();
                    list.add(root.val);
                    if(root.left != null)   q.offer(root.left);
                    if(root.right != null)  q.offer(root.right);          
                }
            }
            res.add(new ArrayList(list));
            flag *= -1;
        }
        return res;
    }
}
```

```java
// 双端队列的思想，正向用队列，反向用栈
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if(root == null)    return res;
        Queue<TreeNode> q = new LinkedList<>();
        int flag = 1;
        q.offer(root);
        while(!q.isEmpty()) {
            int size = q.size();
            Deque<Integer> list = new LinkedList<>();
            for(int i = 0; i < size; i++) {
                root = q.poll();
                if(flag == 1)   list.offerLast(root.val);
                else    list.offerFirst(root.val);
                if(root.left != null)   q.offer(root.left);
                if(root.right != null)  q.offer(root.right);
            }
            res.add(new LinkedList(list));
            flag *= -1;
        }
        return res;
    }
}
```

#### 104. 二叉树的最大深度 @递归

[题目链接](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```java
class Solution {// self
    public int maxDepth(TreeNode root) {
        if(root == null)    return 0;
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```

#### 105. 从前序与中序遍历序列构造二叉树 @递归

[题目链接](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```java
// 递归
class Solution { // self
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        // 1. new TreeNode(root)
        // 2. root.left = buildTree(preorder, s1, e1, inorder, s2, e2)
        return buildTree(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1);
    }

    public TreeNode buildTree(int[] preorder, int s1, int e1, int[] inorder, int s2, int e2) {
        if(s1 > e1 || s2 > e2)  return null;
        if(s1 == e1)    return new TreeNode(preorder[s1]);
        int val = preorder[s1];
        TreeNode root = new TreeNode(val);
        int idx = 0;
        for(int i = s2; i <= e2; i++) {
            if(inorder[i] == val) {
                idx = i;
                break;
            }
        }
        int length = idx - s2;
        root.left = buildTree(preorder, s1 + 1, s1 + length, inorder, s2, s2 + length - 1);
        root.right = buildTree(preorder, s1 + length + 1, e1, inorder, s2 + length + 1, e2);
        return root;
    }
}

// 优化思路：
// 1. 用 HashMap 存储每个节点的位置，节省遍历中序数组查找时间
// 2. 递归终止条件只要 s1>e1 即可，s1==e1 多余
```

#### 108. 将有序数组转换为二叉搜索树 @递归

[题目链接](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
```

```java
class Solution {// self
    public TreeNode sortedArrayToBST(int[] nums) {
        return sortedArrayToBST(nums, 0, nums.length - 1);
    }
	// 二分查找找到根节点递归
    public TreeNode sortedArrayToBST(int[] nums, int left, int right) {
        if(left > right)    return null;
        int mid = left + (right - left) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = sortedArrayToBST(nums, left, mid - 1);
        root.right = sortedArrayToBST(nums, mid + 1, right);
        return root;
    }
}
```

#### 116. 填充每个节点的下一个右侧节点指针 @递归 @层序遍历

[题目链接](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```
输入：root = [1,2,3,4,5,6,7]
输出：[1,#,2,3,#,4,5,6,7,#]
```

```java
// 递归
// 当前节点做什么？
// 让下一行的字节点连起来，当前节点的右字节点和右边的左字节点怎么连起来是关键
// root.right.next = root.next == null ? null : root.next.left;
class Solution {
    public Node connect(Node root) {
        if(root == null)    return null;
        if(root.left != null) {
            root.left.next = root.right;
            root.right.next = root.next == null ? null : root.next.left;
            root.left = connect(root.left);
            root.right = connect(root.right);
        }
        return root;
    }
}
```

```java
// 层需遍历
class Solution {
    public Node connect(Node root) {
        if(root == null)    return null;
        Queue<Node> q = new LinkedList<>();
        q.offer(root);
        while(!q.isEmpty()) {
            int size = q.size();
            for(int i = 0; i < size; i++) {
                Node node = q.poll();
                if(i < size - 1)    node.next = q.peek(); // 一行2个节点，指针指1次
                if(node.left != null)   q.offer(node.left);
                if(node.right != null)  q.offer(node.right);
            }
            // Node tmp = q.poll();
            // Node pre = tmp, curr = null;
            // if(pre.left != null)    q.offer(pre.left);
            // if(pre.right != null)   q.offer(pre.right);
            // for(int i = 1; i < size; i++) {
            //     curr = q.poll();
            //     pre.next = curr;
            //     pre = pre.next;

            //     if(curr.left != null)   q.offer(curr.left);
            //     if(curr.right != null)  q.offer(curr.right);
            // }
            // pre.next = null;
        }
        return root;
    }
}

// poll 弹出队头元素，peek 查看队头后面的元素
```

#### 118. 杨辉三角 @迭代

[题目链接](https://leetcode-cn.com/problems/pascals-triangle/)

```
输入: 5
输出:
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

```java
// 迭代
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> res = new ArrayList<>();
        for(int i = 0; i < numRows; i++) {
            List<Integer> list = new ArrayList<>();
            for(int j = 0; j < i + 1; j++) {
                if(j == 0 || j == i)    list.add(1); // 第一行和第二行都在这里执行
                else    list.add(res.get(i - 1).get(j - 1) + res.get(i - 1).get(j));
            }
            res.add(list);
        }
        return res;
    }
}
```

#### 121. 买卖股票的最佳时机 @动态规划

[题目链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        // 动态规划思想，dp[i] 和 dp[i-1] 相关
        int min = Integer.MAX_VALUE;
        int max = 0;
        for(int price : prices) {
            max = Math.max(price - min, max);
            min = Math.min(price, min);
        }
        return max;
    }
}
```

#### 122. 买卖股票的最佳时机 II @贪心 @动态规划

[题目链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

```
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```

```java
// 贪心
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 1; i < prices.length; i++) {
            if(prices[i] > prices[i - 1]) {
                res += prices[i] - prices[i-1];
            }
        }
        return res;
    }
}
```

```java
// 动态规划
// 状态：   有股票   / 没有股票
// 选择： 不动、卖出 / 不动、买入
class Solution {
    public int maxProfit(int[] prices) {
        // dp[i][0]:今天没有股票的最大利润
        // dp[i][1]:今日有股票的最大利润
        // int n = prices.length;
        // int[][] dp = new int[n][2];
        // dp[0][0] = 0;
        // dp[0][1] = -prices[0];
        // for(int i = 1; i < n; i++) {
        //     dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
        //     dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - prices[i]);
        // }
        // return Math.max(dp[n-1][0], dp[n-1][1]);

        int hasStock = 0;
        int noStock = 0;
        int tmp1 = 0, tmp2 = -prices[0];
        for(int i = 1; i < prices.length; i++) {
            noStock = Math.max(tmp1, tmp2 + prices[i]);
            hasStock = Math.max(tmp2, tmp1 - prices[i]);
            tmp1 = noStock;
            tmp2 = hasStock;
        }
        return Math.max(hasStock, noStock);
    }
}
```

#### 392. 判断子序列 @双指针 @动态规划

[题目链接](https://leetcode-cn.com/problems/is-subsequence/)

```
输入：s = "abc", t = "ahbgdc"
输出：true
```

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        // 双指针，t 中有 s 对应的值才将 s 指针向后移
        int i = 0, j = 0;
        while(i < s.length() && j < t.length()) {
            if(s.charAt(i) == t.charAt(j))  i++;
            j++;
        }
        return i == s.length();
    }
}
```

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        // // dp[i][j]: s[:j] t[:i] 公共子序列的数量
        // int m = s.length();
        // int n = t.length();
        // int[][] dp = new int[m+1][n+1];
        // // base case dp[i][0] = 0;dp[0][j] = 0
        // for(int i = 1; i <= m; i++) {
        //     for(int j = 1; j <= n; j++) {
        //         if(s.charAt(i-1) == t.charAt(j-1))  dp[i][j] = dp[i-1][j-1] + 1;
        //         else    dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
        //     }
        // }
        // return dp[m][n] == m;

        int m = s.length();
        int n = t.length();
        int[] dp = new int[n+1];
        for(int i = 1; i <= m; i++) {
            // pre 初始为 dp[i-1][0] 后面表示 dp[i-1][j-1]
            int pre = 0; // pre -> dp[i-1][j-1] (每一行的第一个数都是 0)
            for(int j = 1; j <= n; j++) {
                int tmp = dp[j]; // tmp -> dp[i-1][j](刚进循环，其实是上一层的值)
                if(s.charAt(i-1) == t.charAt(j-1))  dp[j] = pre + 1;
                else    dp[j] = Math.max(dp[j-1], tmp); // dp[j-1] -> dp[i][j-1]
                pre = tmp;
            }
        }
        return dp[n] == m;
    }
}
```

#### 125. 验证回文串 @双指针

[题目链接](https://leetcode-cn.com/problems/valid-palindrome/)

```
只考虑字母和数字字符
输入: "A man, a plan, a canal: Panama"
输出: true
```

1. 筛选 + 判断：用另外一个字符串存储有效的字符，然后 `字符串反转比较是否相同` 或 `双指针向中间收缩`
2. 在原数组上判断
```java
class Solution {
    public boolean isPalindrome(String s) {
        char[] cArray = s.toLowerCase().toCharArray();
        int i = 0, j = s.length() - 1;
        while(i < j) {
            while(i < j && !isValid(cArray[i]))  i++;
            while(i < j && !isValid(cArray[j]))  j--;
            if(cArray[i] != cArray[j])  return false;
            i++;
            j--;
        }
        return true;
    }

    public boolean isValid(char c) {
        return (c >= 'a' && c <= 'z') || (c >= '0' && c <= '9');
    }
}
```

