
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
class Solution {
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

