## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>
## 1.[二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)<br>
（Facebook 在半年内面试常考）<br>


## 2.[从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)<br>
（字节跳动、亚马逊、微软在半年内面试中考过）<br>


## 3.[组合](https://leetcode-cn.com/problems/combinations/)<br>
（微软、亚马逊、谷歌在半年内面试中考过）<br>


## 4.[全排列](https://leetcode-cn.com/problems/permutations/)<br>
（字节跳动在半年内面试常考）<br>


## 5.[全排列 II](https://leetcode-cn.com/problems/permutations-ii/)<br>
（亚马逊、字节跳动、Facebook 在半年内面试中考过）<br>


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

