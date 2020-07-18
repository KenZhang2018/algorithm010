## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>
## 1.[64.最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)<br>
（亚马逊、高盛集团、谷歌在半年内面试中考过）<br>
时间复杂度O(m*n) 主要消耗在两层for循环处<br>
空间复杂度O(m*n) 主要消耗在dp二维数组上<br>
执行4ms,击败30.29%的用户<br>

```
class Solution {
    public int minPathSum(int[][] grid) {
        int rows = grid.length;
        if (grid == null || rows == 0) return 0;
        int columns = grid[0].length;
        int[][] dp = new int[rows][columns];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < columns; i++) {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }
        for (int i = 1; i <rows; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0] ;
            for (int j = 1; j < columns; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        PriorityQueue<Integer> heap = new PriorityQueue<>();
        heap.add(dp[rows - 1][columns - 1]);
        for (int i = 0; i < dp[rows - 1].length; i ++) {
            int temp = dp[rows - 1][i];
            for (int j = i + 1; j < columns; j++) {
                temp += grid[rows - 1][j];
                if (j == columns - 1) {
                    heap.add(temp);
                }
            }
        }
        return heap.remove();
    }
}
```


### 2.[72.编辑距离](https://leetcode-cn.com/problems/edit-distance/)<br>
（字节跳动、亚马逊、谷歌在半年内面试中考过）<br>

**回溯方式**<br>
时间复杂度O() 复杂度太高<br>
空间复杂度O()<br>
执行超出时间限制<br>

```
class Solution {
    private int min = Integer.MAX_VALUE;

    public int minDistance(String word1, String word2) {
        // 暴力回溯方式
        // 1.当字符相同时,同时移动两个指针往后比
        // 2.当字符不相同时
        //  a.a[i]删除,然后比对a[i+1]与b[j]
        //  b.a[i]替换成b[i],然后比对a[i+1] b[j+1]
        //  c.a[i]前新增b[i],然后比对a[i] b[j+1]
        //  d.b[j]删除,比对a[i] b[j+1]
        //  e.b[j]换成a[i] 然后比对a[i+1] b[j]
        //  f.b[j]前新增a[i] 然后比对a[i+1] b[j+1]
        char[] a = word1.toCharArray();
        char[] b = word2.toCharArray();
        dfs(a, b, 0, 0, 0);
        return min;
    }

    private void dfs(char[] a, char[] b, int i, int j, int distance) {
        // recursion terminator
        // process current level logic
        // drill down
        // reverse current states
        if (i >= a.length || j >= b.length) {
            if (i == a.length) {
                distance += (b.length - j);
            }
            if (j == b.length) {
                distance += (a.length - i);
            }
            min = Math.min(min, distance);
            return;
        }
        if (a[i] == b[j]) {
            dfs(a, b, i + 1, j + 1, distance);
        } else {
            // 删除a[i]
            dfs(a, b, i + 1, j, distance + 1);
            // a[i]前新增b[j]
            dfs(a, b, i, j + 1, distance + 1);
            // a[i]替换成b[j+1]
            dfs(a, b, i + 1, j + 1, distance + 1);
            // 删除b[j]  ==  a[i]前新增b[j[
    //        dfs(a, b, i, j + 1, distance + 1);
            // b[j]前新增a[i]   ==  删除a[i]
    //        dfs(a, b, i + 1, j, distance + 1);
            // b[j]替换成a[i]
    //        dfs(a, b, i + 1, j + 1, distance + 1);
        }
    }

}
```

**动态规划方式**<br>
时间复杂度O(m n) m为word1的长度, n为word2的长度<br>
空间复杂度O(m n) dp二维数组<br>
执行5ms,击败97.59%的用户<br>
```
class Solution {
    public int minDistance(String word1, String word2) {
    if (word1 == null && word2 == null) return 0;
        if (word1 != null && (word2 == null || word2.length() == 0)) return word1.length();
        if ((word1 == null || word1.length() == 0) && word2 != null) return word2.length();
        char[] a = word1.toCharArray();
        char[] b = word2.toCharArray();
        int[][] dp = new int[a.length][b.length];
        boolean isMarked = false;
        for (int j = 0; j < b.length; j++) {
            if (j == 0) {
                if (a[0] == b[j]) {
                    dp[0][j] = 0;
                    isMarked = true;
                } else {
                    dp[0][j] = 1;
                }
            } else {
                if (a[0] == b[j] && !isMarked) {
                    isMarked = true;
                    dp[0][j] = dp[0][j - 1];
                } else {
                    dp[0][j] = dp[0][j - 1] + 1;
                }
            }
        }
        if (dp[0][0] == 0) {
            isMarked = true;
        } else {
            isMarked = false;
        }
        for (int i = 1; i < a.length; i++) {
            if (a[i] == b[0] && !isMarked) {
                if (!isMarked) {

                }
                dp[i][0] = dp[i - 1][0];
                isMarked = true;
            } else {
                dp[i][0] = dp[i - 1][0] + 1;
            }
        }
        for (int i = 1; i < a.length; i++) {
            for (int j = 1; j < b.length; j++) {
                if (a[i] == b[j]) {
                    dp[i][j] = Math.min(Math.min(dp[i][j - 1], dp[i - 1][j]) + 1, dp[i - 1][j - 1]);
                } else {
                    dp[i][j] = Math.min(Math.min(dp[i][j - 1], dp[i - 1][j]), dp[i - 1][j - 1]) + 1;
                }
            }
        }
        return dp[a.length - 1][b.length - 1];
    }
}
```


