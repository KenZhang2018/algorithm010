学习心得

二分查找模板

二分查找面试考察点
1.为什么能用二分查找
    论证:
    a.证明查找的目标函数具有单调性
    b.数据存在上下边界
2.考察整个过程思维逻辑是否清晰
3.考察代码能力是否够强
4.考察Debug能力是否达标(边界特殊情况是否考虑在内,代码是否健壮)

二分查找本质就是双指针从外向里不断夹逼的过程
C语言运算符优先级问题
编码规范就有一项运算必须加括号， 说不要对自己的记忆太自信。。
代码review的时候，不加括号就会挨批的。。
编码规范也有一项，不准省略花括号
就是别人要是改你的代码，他忘了加，就会出问题
像go语言就直接规定必须有花括号

位运算的优先级是最低的
位运算因为比较底层，所以让人容易觉得优先级最高，但是还比加减法低。

# Java     int[] nums中查找目标值target
int low = 0, high = nums.length - 1;
while (low <= high) {
    int middle = low + ((high - low) >> 1);
    if (target == nums.[middle])
        return middle;
    else if (target > nums[middle])
        low = ++middle;
    else 
        high = --middle;
}
# Python
left, right = 0, len(array) - 1 
while left <= right: 
	  mid = (left + right) / 2 
	  if array[mid] == target: 
		    # find the target!! 
		    break or return result 
	  elif array[mid] < target: 
		    left = mid + 1 
	  else: 
		    right = mid - 1
使用二分查找，寻找一个半有序数组 [4, 5, 6, 7, 0, 1, 2] 中间无序的地方
说明：同学们可以将自己的思路、代码写在第 4 周的学习总结中
时间复杂度O(logn)

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

方位抽象成两个数组

int[] x_di = new int[]{0, 1, 0, -1};//前右后左
int[] y_di = new int[]{1, 0, -1, 0};
int di = 0; // 初识方向向前
// 方向运算
di = (di + 3) % 4;//左转 左的数组下标为3
di = (di + 1) % 4;//右转 右的数组下标为1
