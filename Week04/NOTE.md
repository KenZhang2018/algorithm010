# 学习心得<br>

## 二分查找模板<br>
位运算优先级与*/相同,优先级高于+-<br>

```
# Java     int[] nums中查找目标值target
int low = 0, high = nums.length - 1;
while (low <= high) {
    int middle = low + (high - low) >> 1;
    if (target == nums.[middle])
        return middle;
    else if (target > nums[middle])
        low = ++middle;
    else 
        high = --middle;
}
```

```
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
```

使用二分查找，寻找一个半有序数组 [4, 5, 6, 7, 0, 1, 2] 中间无序的地方<br>
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

<br>
<br>
