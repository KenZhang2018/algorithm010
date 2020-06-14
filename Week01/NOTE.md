学习笔记

数组和链表是最基础的数据结构

心得:<br>
1.梳理思路<br>
2.代码实现落地还有段距离,需要考个各种情况和边界情况的处理<br>

# 作业题<br>

## 1. [删除排序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)<br>
（Facebook、字节跳动、微软在半年内面试中考过）<br>
&nbsp;&nbsp;<b>有序数组,隐含重复数据是相邻的</b><br>
&nbsp;&nbsp;<b>画出图中的变化,画图写代码</b><br>
&nbsp;&nbsp;双指针中快指针从1开始遍历<br>

```
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums == null || nums.length == 0) {
            return 0;
        }
        int i = 0;
        for (int j = 1; j < nums.length; j++) {
            if(nums[i] != nums[j]) {
                nums[++i] = nums[j];
            } 
        }
        return ++i;
    }
}
```

<br>

## 2.[旋转数组](https://leetcode-cn.com/problems/rotate-array/)<br>
（微软、亚马逊、PayPal 在半年内面试中考过）<br>

&nbsp;&nbsp;<b>暴力方法 时间复杂度O(k*n)，空间复杂度O(1)</b><br>
```
class Solution {
    public void rotate(int[] nums, int k) {
        for (int i = 1; i <= k; i++) {
            int temp = nums[nums.length -1];
            for (int j = nums.length - 1; j > 0; j--) {
                nums[j] = nums[j-1];
            }
            nums[0] = temp;
        }
    }
}
```
&nbsp;&nbsp;<b>使用额外数组方式 时间复杂度O(n),空间复杂度O(k)</b><br>
&nbsp;&nbsp;直接对比寻找最后移动的k位后下标的对应关系<br>
&nbsp;&nbsp;&nbsp;&nbsp;a.移动位数>数组长度<br>
&nbsp;&nbsp;&nbsp;&nbsp;b.移动位数<=数组长度<br>

```
class Solution {
    public void rotate(int[] nums, int k) {
        int bits = k % nums.length;
        if(bits > 0) {
            int[] tempNums = new int[bits];
            int j = 0;
            for (int i = nums.length - bits; i < nums.length; i++) {
                tempNums[j++] = nums[i];
            }
            for (int i = nums.length -1; i >= bits; i--) {
                nums[i] = nums[i-bits];
            }
            for (int i = 0; i < bits; i++) {
                nums[i] = tempNums[i];
            }
        }
    }
}
```

<br>
&nbsp;&nbsp;<b>环状替换 时间复杂度O(n),空间复杂度O(1)</b>

&nbsp;&nbsp;do while循环写法<br>
&nbsp;&nbsp;减少变量的定义 k = k % nums.length;<br>
&nbsp;&nbsp;下标0移动多次k位后还会返回到原地
```
public class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 0;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int prev = nums[start];
            do {
                int next = (current + k) % nums.length;
                int temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
            } while (start != current);
        }
    }
}
```

&nbsp;&nbsp;while循环写法
```
public class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 1;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int next = (current + k) % nums.length;
            int prev = nums[start];
            int temp = nums[next];
            while (start != next) {
                temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
                next = (current + k) % nums.length;
            }
            nums[start] = temp; 
        }
    }
}
```
<br>
&nbsp;&nbsp;<b>反转数组方式 时间复杂度O(n),空间复杂度O(1)</b><br>
&nbsp;&nbsp;反转数组

```
class Solution {
    public void rotate(int[] nums, int k) {
        // 3次数组反转
        // 1次全部反转
        // 2次反转下标0至k-1的数组
        // 3次反转下标k至nums.length-1的数组
        k = k % nums.length;
        reverse(nums, 0, nums.length-1);
        reverse(nums, 0, k-1);
        reverse(nums, k, nums.length-1);
    }

    public void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 3.[合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)<br>
（亚马逊、字节跳动在半年内面试常考）<br>
**迭代法**<br>
时间复杂度O(n+m) 空间复杂度O(1)<br>
犯错地方:<br>
1.边界处理思路未捋清楚
2.三元运算符写错<br>

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // 1.添加哨兵节点
        // 2.合并两个链表 处理剩下集合非null的节点
        // 3.剔除哨兵节点
        ListNode prehead = new ListNode();
        ListNode temphead = prehead;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                prehead.next = l1;
                l1 = l1.next;
            } else {
                prehead.next = l2;
                l2 = l2.next;
            }
            prehead = prehead.next;
        }
        prehead.next = (l1 != null ? l1 : l2);
        temphead = temphead.next;
        return temphead;
    }
}
```

**递归法**<br>
时间复杂度O(n+m) 空间复杂度O(n+m)<br>

```
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // 1.终止条件
        // 2.递推公式
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }
        if (l1.val <= l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
        
    }
}
```

## 4.[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)<br>
（Facebook 在半年内面试常考）<br>
**暴力方式**<br>
Arrays.sort为快速排序 所以时间复杂度O((n+m)log(n+m)) 空间复杂度O(1)

```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 暴力方式
        // 1.nums2并入nums1尾部
        // 2.nums1排序
        System.arraycopy(nums2, 0, nums1, m, n);
        Arrays.sort(nums1);
    }
}
```

**双指针方式 从前往后**<br>
时间复杂度O(m+n) 空间复杂度O(m)<br>
==学习之处:==<br>
1.并入数组的下标与两个数组下标的对应关系<br>
2.学习熟练使用系统库System.arraycopy

```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 使用额外数组 双指针从前往后遍历
        // 1.新建一个数组nums1_copy来存放nums1
        // 2.while循环将nums1_copy和nums2并入nums1
        // 3.处理为剩余数组中的元素并入nums1
        int[] nums1_copy = new int[m];
        System.arraycopy(nums1, 0, nums1_copy, 0, m);
        int p1 = 0, p2 = 0;
        while ((p1 < m) && (p2 < n)) {
            nums1[p1 + p2] = (nums1_copy[p1] <= nums2[p2]) ? nums1_copy[p1++] : nums2[p2++];
        }
        if (p1 < m) {
            System.arraycopy(nums1_copy, p1, nums1, p1 + p2, m + n - p1 - p2);
        } else {
            System.arraycopy(nums2, p2, nums1, p1 + p2, m + n - p1 - p2);
        }
    }
}
```

**双指针 从后往前的方式**<br>
时间复杂度O(m+n),时间复杂度O(1)<br>

```
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 双指针 从后往前方式 
        // 1.while循环把nums1和nums2从大到小放入nums1的尾部
        // 2.考虑最后可能遗漏的nums2中的元素
        int p1 = m-1, p2 = n-1, p = nums1.length -1;
        while ((p1 >= 0) && (p2 >= 0)) {
            nums1[p--] = (nums1[p1] <= nums2[p2]) ? nums2[p2--] : nums1[p1--];
        }
        System.arraycopy(nums2, 0, nums1, 0, p2 + 1);
    }
}
```

## 5.[两数之和](https://leetcode-cn.com/problems/two-sum/)<br>
（亚马逊、字节跳动、谷歌、Facebook、苹果、微软在半年内面试中高频常考）<br>
**暴力解法**<br>
时间复杂度O(n^2),时间复杂度O(1)<br>
==学习两遍for循环的写法==<br>
```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        // 暴力方式
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == target - nums[i]) {
                    return new int[] {i, j};
                }
            }
        }
        return null;
    }
}
```

**两遍哈希表法**<br>
时间复杂度O(n),空间复杂度O(n)<br>

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        // 两遍哈希表法
        // 1遍存入哈希表
        // 2遍查找哈希表
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement) && i != map.get(complement)) {
                return new int[] {i, map.get(complement)};
            }
        }
        return null;
    }
}
```

**一遍哈希表法**<br>
时间复杂度O(n)，空间复杂度O(n)<br>

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        // 一遍哈希表法
        // 1遍查找哈希表
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[] {i, map.get(complement)};
            }
            map.put(nums[i], i);
        }
        return null;
    }
}
```

## 6.[移动零](https://leetcode-cn.com/problems/move-zeroes/)<br>
（Facebook、亚马逊、苹果在半年内面试中考过）<br>
标记删除法<br>

**双指针法**<br>
时间复杂度O(n), 空间复杂度O(1)<br>
犯错处:<br>
未及时考虑到数组为null,或者数据个数为1的情况

```
class Solution {
    public void moveZeroes(int[] nums) {
        // 双指针法
        // 遍历一遍
        if (nums == null || nums.length < 2) {
            return;
        }
        int j = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                int temp = nums[j];
                nums[j++] = nums[i];
                nums[i] = temp;
            }
        }
    }
}
```

**暴力解法:使用额外数组**<br>
时间复杂度O(n),空间复杂度O(n)<br>

```
class Solution {
    public void moveZeroes(int[] nums) {
        // 暴力解法
        // 使用额外数组
        int[] result = new int[nums.length];
        int j = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                result[j++] = nums[i];
            }
        }
        for (; j < nums.length; j++) {
            result[j] = 0;
        }
        System.arraycopy(result, 0, nums, 0, nums.length);
        // nums = result;
    }
}
```

## 7.[加一](https://leetcode-cn.com/problems/plus-one/)<br>
（谷歌、字节跳动、Facebook 在半年内面试中考过）<br>
题意都没理解<br>
==学习到:int[]初始化时会填充0==<br>

```
class Solution {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            digits[i]++;
            digits[i] %= 10;
            if (digits[i] != 0) {
                return digits;
            }
        }
        int[] result = new int[digits.length + 1];
        result[0] = 1;
        // int[] 初始化时会填充0,所以可以省略搬移操作
        // System.arraycopy(digits, 0, result, 1, digits.length);
        return result;
    }
}
```

## 8.[设计循环双端队列](https://leetcode-cn.com/problems/design-circular-deque/)<br>
（Facebook 在 1 年内面试中考过）<br>




## 9.[==接雨水==](https://leetcode-cn.com/problems/trapping-rain-water/)<br>
（亚马逊、字节跳动、高盛集团、Facebook 在半年内面试常考）<br>


# 解析题<br>
## 1.[LRU Cache - Linked list： LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)<br>


## 2.[盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)<br>
（腾讯、百度、字节跳动在近半年内面试常考）<br>


## 3.[爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)<br>
（阿里巴巴、腾讯、字节跳动在半年内面试常考）<br>


## 4.[==三数之和==](https://leetcode-cn.com/problems/3sum/)<br>
（国内、国际大厂历年面试高频老题）<br>


## 5.[反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)<br>
（字节跳动、亚马逊在半年内面试常考）<br>


## 6.[两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)<br>
（阿里巴巴、字节跳动在半年内面试常考）<br>


## 7.[环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)<br>
（阿里巴巴、字节跳动、腾讯在半年内面试常考）<br>


## 8.[环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)<br>


## 9.[K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)<br>
（字节跳动、猿辅导在半年内面试常考）<br>


## 10.[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)<br>
（亚马逊、JPMorgan 在半年内面试常考）<br>


## 11.[最小栈](https://leetcode-cn.com/problems/min-stack/)<br>
（亚马逊在半年内面试常考）<br>


## 12.[==柱状图中最大的矩形==](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)<br>
（亚马逊、微软、字节跳动在半年内面试中考过）<br>


## 13.[==滑动窗口最大值==](https://leetcode-cn.com/problems/sliding-window-maximum/)<br>
（亚马逊在半年内面试常考）<br>


# 预习题<br>

## 1.[有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/description/)<br>




## 2.[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)<br>



## 3.[最小的 k 个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)<br>








