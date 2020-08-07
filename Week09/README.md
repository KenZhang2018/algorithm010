

### [63. 不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)<br>
状态转移方程:<br>
二维数组方式<br>
dp[0][0] = obstacleGrid[0][0] == 1 ? 0 : 1;<br>
dp[i][0] = obstacleGrid[i][0] == 1 ? 0 : dp[i - 1][0]; (i > 0 && j == 0)<br>
dp[0][j] = obstacleGrid[0][j] == 1 ? 0 : dp[j - 1];(i == 0 && j > 0)<br>
dp[i][j] = obstacleGrid[i][j] == 1 ? 0 : dp[i - 1][j] + dp[i][j - 1]; (i > 0 && j > 0)<br>

一维数组方式<br>
dp[0] = obstacleGrid[0][0] == 1 ? 0 : 1;<br>
dp[0] = obstacleGrid[i][0] == 1 ? 0 : dp[0];(j > 0)<br>
dp[j] = obstacleGrid[i][j] == 1 ? 0 : dp[j] + dp[j - 1];(j > 0)<br>

时间复杂度O(mn) 遍历一遍二维数组,m为行数,n为列数<br>
空间复杂度O(n) n为原始二维数据的列数,一维数组dp损耗<br>
执行0ms,击败100%的用户<br>
```
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int[] dp = new int[obstacleGrid[0].length];
        dp[0] = obstacleGrid[0][0] == 0 ? 1 : 0;
        for (int i = 0; i < obstacleGrid.length; i++) {
            dp[0] = obstacleGrid[i][0] == 1 ? 0 : dp[0];
            for (int j = 1; j < obstacleGrid[0].length; j++) {
                dp[j] = obstacleGrid[i][j] == 1 ? 0 : dp[j] + dp[j - 1];
            }
        }
        return dp[obstacleGrid[0].length - 1];
    }
}
```



### [387. 字符串中的第一个唯一字符](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)<br>

暴力方式<br>
执行38ms,击败35.96%的用户<br>
```
class Solution {
    public int firstUniqChar(String s) {
        for (int i = 0; i < s.length(); i++) {
            boolean isExist = true;
            // 往后查
            for (int j = i + 1; j < s.length(); j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    isExist = false;
                    break;
                }
            }
            // 往前查
            for (int j = i - 1; j >= 0; j--) {
                if (s.charAt(i) == s.charAt(j)) {
                    isExist = false;
                    break;
                }
            }
            if (isExist) return i;
        }
        return -1;
    }
}
```

使用一维数组方式<br>
执行21ms,击败59.93%的用户<br>

```
class Solution {
    public int firstUniqChar(String s) {
        List<Integer>[] dp = new List[26];
        for (int i = 0; i < 26; i++) dp[i] = new ArrayList<>();
        for (int i = 0; i < s.length(); i++) {
            dp[s.charAt(i) - 'a'].add(i);
        }
        for (int i = 0; i < s.length(); i++) {
            if (dp[s.charAt(i) - 'a'].size() == 1) return dp[s.charAt(i) - 'a'].get(0);
        }
        return -1;
    }
}
```



### [541. 反转字符串 II](https://leetcode-cn.com/problems/reverse-string-ii/)<br>
时间复杂度O(n)<br>
空间复杂度O(n)<br>
执行1ms,击败100%的用户<br>

```
class Solution {
    public String reverseStr(String s, int k) {
        char[] chars = s.toCharArray();
        int length = chars.length;
        int times = length / (2 * k);
        int remainder = length % (2 * k);
        int left = 0, right = 0;
        for (int i = 0; i < times; i++) {
            left = i * 2 * k; right = left + k - 1;
            reverse(chars, left, right);
        }
        left = 2 * k * times;
        right = remainder < k ? length - 1 : left + k - 1;
        reverse(chars, left, right);
        return new String(chars);
    }

    private void reverse(char[] chars, int left, int right) {
        while (left < right) {
            char temp = chars[right]; chars[right] = chars[left]; chars[left] = temp;
            left++; right--;
        }
    }
}
```

官方精简写法<br>
```
class Solution {
    public String reverseStr(String s, int k) {
        char[] a = s.toCharArray();
        for (int start = 0; start < a.length; start += 2 * k) {
            int i = start, j = Math.min(start + k - 1, a.length - 1);
            while (i < j) {
                char tmp = a[i];
                a[i++] = a[j];
                a[j--] = tmp;
            }
        }
        return new String(a);
    }
}
```



### [8. 字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)<br>
时间复杂度O(n) n为str的长度<br>
空间复杂度O(1)<br>
执行2ms,击败99.69%的用户<br>
```
class Solution {
    public int myAtoi(String str) {
        // 1.去除首部空格
        // 2.处理正负号
        // 3.处理有效数字
        int i = 0; boolean is_negative = false;
        while (i < str.length() && str.charAt(i) == ' ') i++;
        if (i == str.length()) return 0;
        if (str.charAt(i) == '-') is_negative = true;
        if (str.charAt(i) == '-' || str.charAt(i) == '+') i++;
        int result = 0, toplimit = Integer.MAX_VALUE / 10, lowerlimit = Integer.MIN_VALUE / 10;
        while (i < str.length() && str.charAt(i) <= '9' && str.charAt(i) >= '0') {
            int temp = str.charAt(i++) - '0';
            if (!is_negative && (result > toplimit || (result == toplimit && temp >= 7))) return Integer.MAX_VALUE;
            if (is_negative && (-result < lowerlimit || (-result == lowerlimit && temp >= 8))) return Integer.MIN_VALUE;
            result = result * 10 + temp;
        }
        return !is_negative ? result : -result;
    }
}
```
