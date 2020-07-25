## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>
## 1.[208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)<br>
（亚马逊、微软、谷歌在半年内面试中考过）<br>



执行47ms,击败47.8%的用户<br>
```
public class Trie {

    private TreeNode root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new TreeNode();
    }

    /** Inserts a word into the trie. */
    // 时间复杂度O(m) 其中 mm 为键长
    // 空间复杂度O(m) 最坏的情况下，新插入的键和 Trie 树中已有的键没有公共前缀。此时需要添加 m个结点，使用 O(m) 空间。
    public void insert(String word) {
        TreeNode currentNode = root;
        for (int i = 0; i < word.length(); i++) {
            char letter = word.charAt(i);
            if (!currentNode.containsKey(letter)) {
                currentNode.put(letter, new TreeNode());
            }
            currentNode = currentNode.get(letter);
        }
        currentNode.setEnd(true);

    }

    /** Returns if the word is in the trie. */
    // 时间复杂度O(m)
    // 空间复杂度O(1)
    public boolean search(String word) {
        TreeNode currentNode = root;
        for (int i = 0; i < word.length(); i++) {
            if (currentNode.containsKey(word.charAt(i))) {
                currentNode = currentNode.get(word.charAt(i));
            } else {
                return false;
            }
        }
        return currentNode.isEnd();
    }

    /** Returns if there is any word in the trie that starts with the given prefix. */
    // 时间复杂度O(m)
    // 空间复杂度O(1)
    public boolean startsWith(String prefix) {
        TreeNode currentNode = root;
        for (int i = 0; i < prefix.length(); i++) {
            if (currentNode.containsKey(prefix.charAt(i))) {
                currentNode = currentNode.get(prefix.charAt(i));
            } else {
                return false;
            }
        }
        return true;
    }

    class TreeNode {
        final int SIZE = 26;
        private TreeNode[] links;
        private boolean isEnd;

        public TreeNode() {
            links = new TreeNode[SIZE];
        }

        public boolean containsKey(char letter) {
            return links[letter - 'a'] != null;
        }

        public TreeNode get(char letter) {
            return links[letter - 'a'];
        }

        public void put(char letter, TreeNode node) {
            links[letter - 'a'] = node;
        }

        public void setEnd (boolean isEnd) {
            this.isEnd = isEnd;
        }

        public boolean isEnd() {
            return isEnd;
        }
    }
}

```

### 2.[547. 朋友圈](https://leetcode-cn.com/problems/friend-circles/)<br>
（亚马逊、Facebook、字节跳动在半年内面试中考过）<br>
时间复杂度O(n^3) 两层for循环内套一次查找集合代表<br>
空间复杂度O(n) p数组和set集合损耗,set.size<=p.length<br>
执行2ms,击败50.30%的用户<br>

```
class Solution {
    public int findCircleNum(int[][] M) {
        if (M == null || M.length == 0) return 0;
        int n = M.length; 
        int[] p = new int[n];
        for (int i = 0; i < n; i++) {
            p[i] = i;
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) { // 给定一个 N * N 的矩阵 M
                if (M[i][j] == 1) {
                // 如果M[i][j] = 1，表示已知第 i 个和 j 个学生互为朋友关系，否则为不知道。
                    _union(p, i, j);
                }
            }
        }
        HashSet<Integer> set = new HashSet<>();
        for (int i = 0; i < n; i++) {
            set.add(_parent(p, i));
        }
        return set.size();
    }

    private void _union(int[] p, int i, int j) {
        int p1 = _parent(p, i);
        int p2 = _parent(p, j);
        p[p2] = p1;
    }

    private int _parent(int[] p, int i) {
        int root = i;
        while (root != p[root]) {
            root = p[root];
        }
        // 压缩路径
        while (i != p[i]) {
            int x = i;
            i = p[i];
            p[x] = root;
        }
        return root;
    }
}
```


### 3.[22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)<br>
（亚马逊、Facebook、字节跳动在半年内面试中考过）<br>
时间复杂度小于O(2^2n)<br>
时间复杂度：

```math
O(\dfrac{4^n}{\sqrt{n}})
```
，在回溯过程中，每个答案需要 O(n) 的时间复制到答案数组中。<br>
空间复杂度O(n) 实际递归深度为2n<br>
执行1ms,击败97.09%的用户<br>

```
public List<String> generateParenthesis(int n) {
        List<String> list = new ArrayList<>();
        dfs(0, 0, n, list, new StringBuilder());
        return list;
    }

    private void dfs(int left, int right, int n, List<String> list, StringBuilder parenthesis) {
        // recursion terminator
        // process current level logic
        // drill down
        // reverse states
        if (left == n && right == n) {
            list.add(parenthesis.toString());
            return;
        }
        if (left < n && left >= right) {
            dfs(left + 1, right, n, list, parenthesis.append("("));
            parenthesis.deleteCharAt(parenthesis.length() - 1);
        }
        if (right <= left) {
            dfs(left, right + 1, n, list, parenthesis.append(")"));
            parenthesis.deleteCharAt(parenthesis.length() - 1);
        }
    }
```

执行2ms,击败39.88%的用户<br>
```
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> list = new ArrayList<>();
        dfs(0, 2 * n, list, new StringBuilder());
        return list;
    }

    private void dfs(int level, int n, List<String> list, StringBuilder parenthesis) {
        // recursion terminator
        // process current level logic
        // drill down
        // reverse states
        if (level == n) {
            list.add(parenthesis.toString());
            return;
        }
        String temp = parenthesis.toString();
        int left = 0, right = 0;
        for (int i = 0; i < temp.length(); i++) {
            if (temp.charAt(i) == '(') {
                left++;
            } else if (temp.charAt(i) == ')') {
                right++;
            }
        }
        if (left < (n / 2) && left >= right) {
            dfs(level + 1, n, list, parenthesis.append("("));
            parenthesis.deleteCharAt(parenthesis.length() - 1);
        }
        if (right <= left) {
            dfs(level + 1, n, list, parenthesis.append(")"));
            parenthesis.deleteCharAt(parenthesis.length() - 1);
        }
    }
}
```


### 4.[51. N皇后](https://leetcode-cn.com/problems/n-queens/)<br>
（亚马逊、苹果、字节跳动在半年内面试中考过）<br>
时间复杂度O(n^3) 每行每列都要检查n次<br>
空间复杂度O(n^2) temp数组损耗<br>
执行3ms,击败86.35%的用户<br>
```
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> list = new ArrayList();
        int[][] temp = new int[n][n];
        dfs(n, 0, temp, list);
        return list;
    }

    public void dfs(int n, int level, int[][] temp, List<List<String>> list) {
        // recursion terminator
        // process current level logic
        // drill down
        // reverse current states
        if (level == n) {
            List<String> subList = new ArrayList();
            for (int i = 0; i < n; i ++) {
                StringBuilder sb = new StringBuilder();
                for (int j = 0; j < n; j++) {
                    if (temp[i][j] == 0) {
                        sb.append(".");
                    } else {
                        sb.append("Q");
                    }
                }
                subList.add(sb.toString());
            }
            list.add(subList);
            return;
        }
        for (int column = 0; column < n; column++) {
            temp[level][column] = 1;
            if (check(temp, level, column)) {
                dfs(n, level + 1, temp, list);
            }
            temp[level][column] = 0;
        }
    }

    public boolean check(int[][] temp, int level, int column) {
        int left = column, right = column;
        for (int i = level - 1; i >= 0; i--) {
            if (temp[i][column] == 1) {
                return false;
            }
            left--;
            if (left >= 0 && temp[i][left] == 1) {
                return false;
            }
            right++;
            if (right < temp.length && temp[i][right] == 1) {
                return false;
            }
        }
        return true;
    }
}
```

