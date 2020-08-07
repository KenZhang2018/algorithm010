学习笔记

# [算法知识树梳理](http://note.youdao.com/noteshare?id=39068496ec76452129afae7589764371&sub=1B0E068D2A7645B5B4F37AF45A8E4BD1)<br>

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

