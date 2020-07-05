## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>
## 1.[柠檬水找零](https://leetcode-cn.com/problems/lemonade-change/description/)<br>
（亚马逊在半年内面试中考过））<br>
**递归--二叉树的后序遍历方式**<br>
时间复杂度O(n)<br>
空间复杂度O(1)<br>
执行时间4ms,击败12.07%的用户<br>

```
class Solution {
    public boolean lemonadeChange(int[] bills) {
        // 1.从小到大排序
        // 2.前面决策不会影响后面决策,可以用从前往后贪心
        // Arrays.sort(bills); 时间复杂度O(nlogn)（按账单 bills 支付的顺序）
        int[] changes = new int[3];
        for (int i = 0; i < bills.length; i++) {
            if (bills[i] == 5) {
                changes[0] = changes[0] + 5;
            } else if (bills[i] == 10) {
                if (changes[0] >= 5) {
                    changes[0] = changes[0] - 5;
                    changes[1] = changes[1] + 10;
                } else {
                    return false;
                }
            } else if (bills[i] == 20) {
                int debt = 15;
                // 找10元
                if (changes[1] >= 10) {
                    changes[1] = changes[1] - 10;
                    debt = debt - 10;
                }
                // 找5元
                if (changes[0] >= debt) {
                    changes[0] = changes[0] - debt;
                    debt = 0;
                }
                if (debt > 0) {
                    return false;
                }
                // changes[2] = changes[2] + 20;
            }
        }
        return true;
    }
}
```

执行2ms,击败99.96%的用户<br>
**注意:写代码时不要省略{}**<br>
```
class Solution {
    public boolean lemonadeChange(int[] bills) {
        // 1.不需要从小到大排序（按账单 bills 支付的顺序）
        // 2.前面决策不会影响后面决策,可以用从前往后贪心
        int five = 0, ten = 0;
        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) {
                    return false;
                }
                five--;
                ten++;
            } else if (bill == 20) {
                if (ten > 0 && five > 0) {
                    ten--;
                    five--;
                } else if (five < 3) {
                    return false;
                } else {
                    five -= 3;
                }
            }
        }
        return true;
    }
}
```

## 2.[买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/description/) <br>
（亚马逊、字节跳动、微软在半年内面试中考过）<br>
时间复杂度O(n)<br>
空间复杂度O(1)<br>

```
class Solution {
    public int maxProfit(int[] prices) {
        // 前面最优决策不影响后面最优决策,可以使用从前往后贪心
        // 明天比今天价高就卖出买入,累加利润
        int maxProfit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i - 1] < prices[i]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        return maxProfit;
    }
}
```

## 3.[分发饼干](https://leetcode-cn.com/problems/assign-cookies/description/)<br>
（亚马逊在半年内面试中考过）<br>
时间复杂度O(nlogn) 排序耗时nlogn<br>
空间复杂度O(1) 不需要额外存储空间<br>

```
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        // 1.对孩子胃口和饼干大小都进行从小到大排序
        // 2.最后胃口孩子吃满足最小大小的饼干,不影响后续最优决策,可以使用从前往后贪心
        Arrays.sort(g);
        Arrays.sort(s);
        int contentChildren = 0;
        for (int i = 0, j = 0; i < g.length; i++) {
            while (j < s.length && s[j] < g[i]) {
                j++;
            }
            if (j < s.length && s[j] >= g[i]) {
                contentChildren++;
                j++;
            }
        }
        return contentChildren;
    }
}
```

**精简写法**<br>
考虑了参数为null的特殊处理<br>
```
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        // 1.对孩子胃口和饼干大小都进行从小到大排序
        // 2.最后胃口孩子吃满足最小大小的饼干,不影响后续最优决策,可以使用从前往后贪心
        // 特殊边界处理
        if (g == null || s == null) return 0;
        Arrays.sort(g);
        Arrays.sort(s);
        int i = 0, j = 0;
        while (i < g.length && j < s.length) {
            if (s[j] >= g[i]) {
                // 满足的孩子数+1
                i++;
            }
            // 找更大的饼干
            j++;
        }
        return i;
    }
}
```

## 4.[模拟行走机器人](https://leetcode-cn.com/problems/walking-robot-simulation/description/)<br>
时间复杂度O(N+K) N,K分别为commands和obstacles的长度<br>
空间复杂度O(K) 存储障碍物的集合空间<br>
执行13ms,击败97.93%的用户<br>

```
class Solution {
    public int robotSim(int[] commands, int[][] obstacles) {
        // 1.构建方向盘
        // 2.初识化坐标/方向/最大欧式距离的平方
        // 3.构建障碍物集合
        // 4.运算指令
        int[] x_di = new int[]{0, 1, 0, -1};//前右后左
        int[] y_di = new int[]{1, 0, -1, 0};
        int x = 0, y = 0, di = 0, ans = 0;
        Set<Long> obstacleSet = new HashSet();
        for (int[] obstacle : obstacles) {
            obstacleSet.add(((long)obstacle[0] << 16) + (long)obstacle[1]);
        }
        for (int command : commands) {
            if (command == -2) {// left
                di = (di + 3) % 4;
            } else if (command == -1) {// right
                di = (di + 1) % 4;
            } else {
                for (int i = 0; i < command; i++) {
                    int tempx = x + x_di[di];
                    int tempy = y + y_di[di];
                    if (!obstacleSet.contains(((long)tempx << 16) + (long)tempy)) {
                        x = tempx;
                        y = tempy;
                        ans = Math.max(ans, x * x + y * y);
                    }
                }
            }
        }
        return ans;
    }
}
```

## 4.使用二分查找，寻找一个半有序数组 [4, 5, 6, 7, 0, 1, 2] 中间无序的地方<br>
说明：同学们可以将自己的思路、代码写在第 4 周的学习总结中<br>
时间复杂度O(logn)<br>
```
class Solution {
    public int search(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        // 二分查找，判断左右两边哪边有序
        // 在有序一边根据middle决定该往哪边逼近
        int low = 0, high = nums.length - 1, middle = 0;
        while (low <= high) {
            middle = low + ((high - low) >> 1);
            if (nums[low] <= nums[middle]) {
                // 左边有序
                if (nums[middle] > nums[middle + 1]) 
                    return middle + 1;
                low = middle + 1; 
            } else {
                // 右边有序
                if (nums[middle] > nums[middle - 1]) 
                    return middle;
                high = middle - 1; 
            }
        }
        return -1;
    }
}
```


## [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)<br>
时间复杂度O(logn)<br>
空间复杂度O(1)<br>
执行0ms,击败100%的用户<br>

```
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return -1;
        // 二分查找，左右判断哪边有序
        // 在有序一边根据范围判断是否存在目标值，存在就在有序中二分查找
        // 不存在则往无序的子数组中继续类似查找
        int low = 0, high = nums.length - 1, middle = 0;
        while (low <= high) {
            middle = low + ((high - low) >> 1);
            if (target == nums[middle]) return middle;
            if (nums[low] <= nums[middle]) {
                // 左边有序
                if (target >= nums[low] && target < nums[middle]) {
                    high = middle - 1;  
                } else {
                    low = middle + 1; 
                }
            } else {
                // 右边有序
                if (target <= nums[high] && target > nums[middle]) {
                    low = middle + 1;
                } else {
                    high = middle - 1; 
                }
            }
        }
        return -1;
    }
}
```

## 5.[单词接龙](https://leetcode-cn.com/problems/word-ladder/description/)<br>
（亚马逊在半年内面试常考）<br>
时间复杂度O(MN) M为wordList的个数,N为word的长度<br>
空间复杂度O(MN) <br>
时间复杂度：O(M×N)，其中 M 是单词的长度 N 是单词表中单词的总数。找到所有的变换需要对每个单词做 M 次操作。同时，最坏情况下广度优先搜索也要访问所有的 N 个单词。<br>
空间复杂度：O(M×N)，要在 combinationDictionary 字典中记录每个单词的 M 个通用状态。访问数组的大小是 N。广搜队列最坏情况下需要存储 N 个单词。<br>

难点:wordList抽象成无向无权图<br>

```
class Solution {
     public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 1.构建wordList的组合词的邻接表
        // 2.初始化计数节点/访问节点记录集合/广度搜索需要的队列
        // 3.用beginWord开始广度搜索,广度搜索过程中找到endWord返回查找次数
        Map<String, List<String>> combinationDictionary = new HashMap<>();
        wordList.forEach(word -> {
            for (int i = 0; i < word.length(); i++) {
                String dictionaryWord = word.substring(0, i) + "*" + word.substring(i + 1, word.length());
                List<String> stringList = combinationDictionary.getOrDefault(dictionaryWord, new ArrayList<>());
                stringList.add(word);
                combinationDictionary.put(dictionaryWord, stringList);
            }
        });
        Set<String> record = new HashSet<>();
        Queue<Pair<String, Integer>> queue = new LinkedList<>();
        queue.add(new Pair<>(beginWord, 1));
        record.add(beginWord);
        int result = 0;
        while (!queue.isEmpty()) {
            Pair<String, Integer> node = queue.remove();
            String nodeWord = node.getWord();
            Integer level = node.getLevel();
            for (int i = 0; i < nodeWord.length(); i++) {
                String dictionaryWord = nodeWord.substring(0, i) + "*" + nodeWord.substring(i + 1, nodeWord.length());
                List<String> stringList = combinationDictionary.get(dictionaryWord);
                if (stringList != null) {
                    for (String word : stringList) {
                        if (word.equals(endWord)) {
                            return  level + 1;
                        } else {
                            if (!record.contains(word)) {
                                record.add(word);
                                queue.add(new Pair<>(word, level + 1));
                            }
                        }
                    }
                }
            }
        }
        return result;
    }

    private class Pair<T, T1> {
        private T word;
        private T1 level;

        public Pair(T word, T1 level) {
            this.word = word;
            this.level = level;
        }

        public T getWord() {
            return word;
        }

        public void setWord(T word) {
            this.word = word;
        }

        public T1 getLevel() {
            return level;
        }

        public void setLevel(T1 level) {
            this.level = level;
        }
    }
}
```


## 6.[岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)<br>
（近半年内，亚马逊在面试中考查此题达到 350 次）<br>
**深度优先搜索方式**<br>
时间复杂度O(RC) R:行数,C:列数<br>
空间复杂度O(RC) 递归深度,整个全是岛屿<br>
执行2ms,击败97.44%的用户<br>
解题障碍:没想到用DFS去淹没岛屿<br>

```
class Solution {
    public int numIslands(char[][] grid) {
        // 1.遍历二维数组,当遇到为1时岛屿数量+1,并清理上下左右相连的陆地
        int islandNum = 0;
        for (int row = 0; row < grid.length; row++) {
            for (int column = 0; column < grid[row].length; column++) {
                if (grid[row][column] == '1') {
                    islandNum++;
                    // 清理相连陆地(必须也要清理下左的方向)
                    deepFirstSearch(grid, row, column);
                }
            }
        }
        return islandNum;
    }

    private void deepFirstSearch(char[][] grid, int row, int column) {
        // recursion terminator
        // process current level logic
        // drill down
        // reverse current states
        if (row < 0 || column < 0 || row >= grid.length || column >= grid[row].length || grid[row][column] == '0') {
            // 到边界或者遇到水'0'时终止
            return;
        }
        if (grid[row][column] == '1') {
            grid[row][column] = '0';
        }
        //right
        deepFirstSearch(grid, row, column + 1);
        //down
        deepFirstSearch(grid, row + 1, column);
        // [['1', '1', '1'], ['0', '1', '0'], ['1', '1', '1']]
        // left
        deepFirstSearch(grid, row, column - 1);
        // up
        deepFirstSearch(grid, row - 1, column);
    }
}
```

**广度优先搜索方式**<br>
时间复杂度O(RC) R:行数,C:列数<br>
空间复杂度O(RC) 递归深度,整个全是岛屿<br>
执行7ms,击败12.79%的用户<br>
解题障碍:如何使用队列<br>

```
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        // 1.遍历二维数组,当遇到为1时岛屿数量+1,并清理上下左右相连的陆地
        int islandNum = 0;
        int rc = grid.length * grid[0].length;
        for (int row = 0; row < grid.length; row++) {
            for (int column = 0; column < grid[row].length; column++) {
                LinkedList<Integer> queue = new LinkedList();
                if (grid[row][column] == '1') {
                    islandNum++;
                    grid[row][column] = '0';
                    queue.add(row * rc + column);
                    // 清理相连陆地
                    while (!queue.isEmpty()) {
                        int rowColumn = queue.remove();
                        int temprow = rowColumn / rc;
                        int tempcolumn = rowColumn % rc;
                        if (tempcolumn < grid[0].length - 1 && grid[temprow][tempcolumn + 1] == '1') {
                            grid[temprow][tempcolumn + 1] = '0';// 避免最外两层for循环重复计算
                            // right
                            queue.add(temprow * rc + tempcolumn + 1);
                        }
                        if (temprow < grid.length - 1 && grid[temprow + 1][tempcolumn] == '1') {
                            grid[temprow + 1][tempcolumn] = '0';
                            // down
                            queue.add((temprow + 1) * rc + tempcolumn);
                        }
                        if (tempcolumn - 1 >= 0 && grid[temprow][tempcolumn - 1] == '1') {
                            grid[temprow][tempcolumn - 1] = '0';
                            // left
                            queue.add(temprow * rc + tempcolumn - 1);
                        }
                        if (temprow - 1 >= 0 && grid[temprow - 1][tempcolumn] == '1') {
                            grid[temprow - 1][tempcolumn] = '0';
                            // up
                            queue.add((temprow - 1) * rc + tempcolumn);
                        }
                    }
                }
            }
        }
        return islandNum;
    }
}
```

官方广度优先搜索代码<br>
执行6ms,击败21.24%的用户<br>
```
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }

        int nr = grid.length;
        int nc = grid[0].length;
        int num_islands = 0;

        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {
                    ++num_islands;
                    grid[r][c] = '0';
                    Queue<Integer> neighbors = new LinkedList<>();
                    neighbors.add(r * nc + c);
                    while (!neighbors.isEmpty()) {
                        int id = neighbors.remove();
                        int row = id / nc;
                        int col = id % nc;
                        if (row - 1 >= 0 && grid[row-1][col] == '1') {
                            neighbors.add((row-1) * nc + col);
                            grid[row-1][col] = '0';
                        }
                        if (row + 1 < nr && grid[row+1][col] == '1') {
                            neighbors.add((row+1) * nc + col);
                            grid[row+1][col] = '0';
                        }
                        if (col - 1 >= 0 && grid[row][col-1] == '1') {
                            neighbors.add(row * nc + col-1);
                            grid[row][col-1] = '0';
                        }
                        if (col + 1 < nc && grid[row][col+1] == '1') {
                            neighbors.add(row * nc + col+1);
                            grid[row][col+1] = '0';
                        }
                    }
                }
            }
        }

        return num_islands;
    }
}
```



中等：


扫雷游戏<br>
（亚马逊、Facebook 在半年内面试中考过）<br>
跳跃游戏 <br>
（亚马逊、华为、Facebook 在半年内面试中考过）<br>
搜索旋转排序数组<br>
（Facebook、字节跳动、亚马逊在半年内面试常考）<br>
搜索二维矩阵<br>
（亚马逊、微软、Facebook 在半年内面试中考过）<br>
寻找旋转排序数组中的最小值<br>
（亚马逊、微软、字节跳动在半年内面试中考过）<br>
困难<br>
单词接龙 II <br>
（微软、亚马逊、Facebook 在半年内面试中考过）<br>
跳跃游戏 II <br>
（亚马逊、华为、字节跳动在半年内面试中考过）<br>
