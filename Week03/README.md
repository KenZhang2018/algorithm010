## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>
## 1.[二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)<br>
（Facebook 在半年内面试常考）<br>
**递归--二叉树的后序遍历方式**<br>
时间复杂度O(n)<br>
空间复杂度O(n) 取决于二叉树的深度,最坏情况二叉树退化为链表<br>
执行时间8ms,击败69.47%的用户<br>


```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    private TreeNode result = null;

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        boolean isExist = dfs(root, p, q);
        return result;
    }

    private boolean dfs(TreeNode root, TreeNode p, TreeNode q) {
        // terminator
        if (root == null) {
            return false;
        }
        // drill down
        boolean left = dfs(root.left, p, q);
        boolean right = dfs(root.right, p, q);
        // process current level logic
        // reverse states
        if ((root.val == p.val || root.val == q.val) && (left == true || right == true)) {
            result = root;
            return true;
        }
        if (root.val == p.val || root.val == q.val) {
            return true;
        }
        if (left && right) {
            result = root;
            return true;
        }
        if (left == true || right == true) {
            return true;
        }
        return false;
    }
}
```

**通过存储父节点关系的方式**<br>
时间复杂度O(n)<br>
空间复杂度O(n) map与set<br>
执行12ms,击败22.51%的用户<br>
==学习地方:构建存储当前节点与父节点的关系表,学习优秀写法==<br>

```
    //**** 优秀写法 ****
    public void dfs(TreeNode root) {
        if (root.left != null) {
            parent.put(root.left.val, root);
            dfs(root.left);
        }
        if (root.right != null) {
            parent.put(root.right.val, root);
            dfs(root.right);
        }
    }
```


```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    private Map<Integer, TreeNode> map = new HashMap<Integer, TreeNode>();
    private Set<Integer> set = new HashSet<Integer>();

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 节点数据存入Map中,并构建当前节点值与父节点的关系
        // 给p标记向上的路径
        // 检查q向上的路径与p向上路径的重合节点
        inOrder(root);
        while (p != null) {
            set.add(p.val);
            p = map.get(p.val);
        }
        while (q != null) {
            if (set.contains(q.val)) {
                return q;
            }
            q = map.get(q.val);
        }
        return null;
    }

    private void inOrder(TreeNode root) {
        if (root == null) return;
        // 二叉树中序遍历
        inOrder(root.left);
        if (root.left != null) {
            map.put(root.left.val, root);
        }
        if (root.right != null) {
            map.put(root.right.val, root);
        }
        inOrder(root.right);
    }
    
    private void preOrder(TreeNode root) {
        if (root == null) return;
        // 二叉树前序遍历
        if (root.left != null) {
            map.put(root.left.val, root);
        }
        if (root.right != null) {
            map.put(root.right.val, root);
        }
        preOrder(root.left);
        preOrder(root.right);
    }
    
    private void postOrder(TreeNode root) {
        if (root == null) return;
        preOrder(root.left);
        preOrder(root.right);
        // 二叉树前序遍历
        if (root.left != null) {
            map.put(root.left.val, root);
        }
        if (root.right != null) {
            map.put(root.right.val, root);
        }
    }
    
}    
   

```



## 2.[从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)<br>
（字节跳动、亚马逊、微软在半年内面试中考过）<br>
**递归方式**<br>
时间复杂度O(n)<br>
空间复杂度O(1)<br>
执行4ms，击败56.64%的用户<br>
==犯错地方：计算左子树节点的个数==<br>

```
public TreeNode buildTree(int[] preorder, int[] inorder) {
        TreeNode root = buildTreeHelper(preorder, 0, preorder.length, inorder, 0, inorder.length);
        return root;
    }

    private TreeNode buildTreeHelper(int[] preorder, int pStart, int pEnd, int[] inorder, int iStart, int iEnd) {
        // recursion terminator
        if (pStart == pEnd) return null;
        // process current level logic
        int rootValue = preorder[pStart];
        TreeNode root = new TreeNode(rootValue);
        // 可以优化为散列表
        int inorderRootIndex = 0;
        for (int i = iStart; i < iEnd; i++) {
            if (rootValue == inorder[i]) {
                inorderRootIndex = i;
                break;
            }
        }
        // my fault
        int leftTreeNum = inorderRootIndex - iStart;
        // drill down
        root.left = buildTreeHelper(preorder, pStart + 1, pStart + leftTreeNum + 1,
                inorder, iStart, inorderRootIndex);
        root.right = buildTreeHelper(preorder, pStart + leftTreeNum + 1, pEnd,
                inorder, inorderRootIndex + 1, iEnd);
        // reverse states
        return root;
    }
```

**迭代方式**<br>
时间复杂度O(n)<br>
空间复杂度O(n)<br>
执行2ms，击败98.28%的用户<br>
==犯错地方：出栈必须使用while==<br>

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 前序遍历 preorder = [3,9,20,15,7]
 中序遍历 inorder = [9,3,15,20,7]
 */
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        // 处理边界情况
        // 创建栈并放入根节点
        // 构建根节点和左子树
        // 依次出栈构建右子树
        if (preorder == null || preorder.length == 0) return null;
        Stack<TreeNode> stack = new Stack();
        int pre = 0, in = 0;
        TreeNode root = new TreeNode(preorder[pre++]);
        TreeNode curRoot = root;
        stack.push(curRoot);
        while (pre < preorder.length) {
            if (curRoot.val != inorder[in]) {
                curRoot.left = new TreeNode(preorder[pre++]);
                curRoot = curRoot.left;
                stack.push(curRoot);
            } else {
                while (!stack.isEmpty() && stack.peek().val == inorder[in++]) {
                    curRoot = stack.pop();
                }
                curRoot.right = new TreeNode(preorder[pre++]);
                curRoot = curRoot.right;
                stack.push(curRoot);
            }
        }
        return root;
    }
}
```



## 3.[组合](https://leetcode-cn.com/problems/combinations/)<br>
（微软、亚马逊、谷歌在半年内面试中考过）<br>
**回溯方式 + 优化剪枝**<br>

```math
时间复杂度 : O(k C_N^k)，其中 C_N^k = \frac{N!}{(N - k)! k!}是要构成的组合数
```

```math
空间复杂度 : O(C_N^k) ，用于保存全部组合数以输出.
```

递归树的深度也会损耗空间. <br>
执行33ms,击败24.59%的用户<br>

```
class Solution {

    List<List<Integer>> list = new ArrayList();

    public List<List<Integer>> combine(int n, int k) {
        // 1.clarification:corner case
        // 2.possible solution --> optimal(time&space)
        // 3.code
        // 4.test cases
        if (n == 0 || n < k) return null;
        findComination(n, k, 1, new Stack());
        return list;
    }

    private void findComination(int n, int k, int begin, Stack<Integer> stack) {
        // recursion terminator
        // proccess current level logic
        // drill down
        // reverse current states
        if (stack.size() == k) {
            list.add(new ArrayList(stack));
            return;
        }
        for (int i = begin; n - i  >= k - (stack.size() + 1); i++) {
        <!--for (int i = begin; i <= n; i++) {-->
            // proccess current level logic
            stack.push(i);
            // dirll down
            findComination(n, k, i + 1, stack);
            // reverse current states
            stack.pop();
        }
    }

}
```

**排列方式**<br>
```math
时间复杂度 : O(k C_N^k)，其中 C_N^k = \frac{N!}{(N - k)! k!}是要构成的组合数
```

```math
空间复杂度 : O(C_N^k) ，用于保存全部组合数以输出.
```
**学习地方**<br>
排列的解决思路以及编程技巧<br>
使用哨兵<br>
linkedList.set(j, j++ + 1);<br>
linkedList.set(j, linkedList.get(j) + 1);<br>

```
class Solution {
    // n = 4, k = 2
    // [1,2] [1,3] [2,3] [1,4] [2,4] [3,4] 
    public List<List<Integer>> combine(int n, int k) {
        // 1.构造模式k的LinkedList的子集
        // 2.构造输出集合
        // 3.循环将符合要求的结果放入输出集合中
        LinkedList<Integer> linkedList = new LinkedList();
        for (int i = 1; i <= k; i++) {
            linkedList.add(i);
        }
        // 哨兵
        linkedList.add(n + 1);
        List<List<Integer>> list = new ArrayList();
        int j = 0;
        while (j < k) {
            list.add(new ArrayList(linkedList.subList(0, k)));
            j = 0;
            while (j < k && (linkedList.get(j + 1) == linkedList.get(j) + 1)) {
                linkedList.set(j, j++ + 1);
            }
            linkedList.set(j, linkedList.get(j) + 1);
        }
        return list;
    }

}
```



## 4.[全排列](https://leetcode-cn.com/problems/permutations/)<br>
（字节跳动在半年内面试常考）<br>


## 5.[全排列 II](https://leetcode-cn.com/problems/permutations-ii/)<br>
（亚马逊、字节跳动、Facebook 在半年内面试中考过）<br>


# 练习题<br>
## 1.[爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)<br>
（阿里巴巴、腾讯、字节跳动在半年内面试常考）<br>

**动态规划方式一**<br>
时间复杂度O(n)<br>
空间复杂度O(n)<br>

```

class Solution {
    public int climbStairs(int n) {
        if (n == 1) {
            return 1;
        }
        int[] data = new int[n];
        data[0] = 1;
        data[1] = 2;
        for (int i = 2; i < n; i++) {
          data[i] = data[i - 1] + data[i - 2];  
        }
        return data[n - 1];
    }
}
```

**动态规划方式二**<br>
时间复杂度O(n)<br>
空间复杂度O(1)<br>
执行0ms,击败100%的用户

```
class Solution {
    public int climbStairs(int n) {
        // 三指针法 
        // 三指针同时不断向后移动,移动n次结束
        int p = 0, q = 0, r = 1;
        for (int i = 1; i <= n; i++) {
            p = q;
            q = r;
            r = q + p;
        }
        return r;
    }
}
```
