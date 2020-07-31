## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>


# 作业题<br>

### 1.[191.位 1 的个数](https://leetcode-cn.com/problems/number-of-1-bits/)<br>
（Facebook、苹果在半年内面试中考过）<br>
时间复杂度O(1) 1为数的二进制长度<br>
空间复杂度O(1)<br>
执行1ms,击败99.13%的用户<br>
```
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int number = 0;
        for (int i = 0; i < 32; i++) {
            if (((n >> i) & 1) == 1) {
                number++;
            }
        }
        return number;
    }
}
```

### 2.[231.2 的幂](https://leetcode-cn.com/problems/power-of-two/)<br>
（谷歌、亚马逊、苹果在半年内面试中考过）<br>
时间复杂度O(1)<br>
空间复杂度O(1)<br>
执行1ms,击败100%的用户<br>
```
class Solution {
    public boolean isPowerOfTwo(int n) {
        if (n == 0) return false;
        long opposite = ~n; // int型不能处理int型极端值,所以需要更大长度
        return (n & (opposite + 1)) == n;
    }
}
```

执行1ms,击败100%的用户<br>
```
class Solution {
    public boolean isPowerOfTwo(int n) {
        if (n == 0) return false;
        if (n == -2147483648) return false; // int型极端值的处理
        int opposite = ~n;
        return (n & (opposite + 1)) == n;
    }
}
```


超时不通过
```
class Solution {
    public boolean isPowerOfTwo(int n) {
        int power = 1;
        while (power < n) {
            power = power << 1;
        }
        return power == n;
    }
}
```

### 3.[190.颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)<br>
（苹果在半年内面试中考过）<br>
时间复杂度O(1)<br>
空间复杂度O(1)<br>
执行1ms,击败100%的用户<br>

```
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int result = 0;
        for (int i = 0; i < 32; i++) {
            int temp = (n >> i) & 1;
            result = (temp << (31 - i)) | result;
        }
        return result;
    }
}
```

### 4.[1122.数组的相对排序](https://leetcode-cn.com/problems/relative-sort-array/)<br>
（谷歌在半年内面试中考过）<br>
暴力方式
时间复杂度O(nlogn) 最后情况arr2中只有1个元素,需要对arr1中剩余的元素排序<br>
空间复杂度O(n) n为arr1的长度,tempList和map的损耗<br>
执行3ms,击败37.25%的用户<br>

```
class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int[] result = new int[arr1.length];
        List<Integer> tempList = new ArrayList();
        // 1.遍历arr1将不在arr2中的元素放入tempList中,并统计arr2中元素在arr1中出现的个数
        // 2.对tempList排序
        Map<Integer, Integer> map = new HashMap();
        for (int i = 0; i < arr2.length; i++) {
            map.put(arr2[i], 0);
        }
        for (int i = 0; i < arr1.length; i++) {
            if (map.containsKey(arr1[i])) {
                map.put(arr1[i], map.get(arr1[i]) + 1);
            } else {
                tempList.add(arr1[i]);
            }
        }
        Collections.sort(tempList);
        // 3.组装数据
        int size = 0;
        for (int i = 0; i < arr2.length; i++) {
            for (int j = 0; j < map.get(arr2[i]); j++) {
                result[size] = arr2[i];
                size++;
            }
        }
        for (int i = 0; i < tempList.size(); i++) {
            result[size] = tempList.get(i);
            size++;
        }
        return result;
    }
}
```

### 5.[242.有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)<br>
（Facebook、亚马逊、谷歌在半年内面试中考过）<br>
哈希表方式<br>
时间复杂度O(n) n为s的长度<br>
空间复杂度O(1) 字符集的范围是26个字母<br>
执行5ms,击败49.32%的用户<br>
```
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        int[] alphm = new int[26];
        for (int i = 0; i < s.length(); i++) {
            alphm[s.charAt(i) - 'a'] ++;
            alphm[t.charAt(i) - 'a'] --;
        }
        for (int i = 0; i < 26; i++) {
            if (alphm[i] != 0) {
                return false;
            }
        }
        return true;
    }
}
```

排序方式<br>
时间复杂度O(nlogn)<br>
空间复杂度O(1)<br>
执行时间3ms,击败86.91%的用户<br>
```
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        char[] char1 = s.toCharArray();
        char[] char2 = t.toCharArray();
        Arrays.sort(char1);
        Arrays.sort(char2);
        return Arrays.equals(char1, char2);
    }
}
```

### 6.[146.LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/#/)<br>
（亚马逊、字节跳动、Facebook、微软在半年内面试中常考）<br>

执行24ms,击败28.72%的用户<br>
```
class LRUCache {

    Map<Integer, Node> cache;
    private Node head;
    private Node tail;
    private int size;
    private int capacity;

    class Node {
        int key;
        int value;
        Node previous;
        Node next;

        public Node() {
        }

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }

    }

    public LRUCache(int capacity) {
        this.capacity = capacity;
        cache = new HashMap<>(capacity);
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.previous = head;
    }

    public int get(int key) {
        Node node = cache.get(key);
        if (node == null) return -1;
        // 移动到头结点
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        Node node = cache.get(key);
        if (node == null) {
            // 插入头部元素
            insertNewNode(key, value);
            if (size < capacity) {
                // 头部插入
                size++;
            } else {
                // 头部插入并删除末端元素
                cache.remove(tail.previous.key);
                tail.previous.previous.next = tail;
                tail.previous = tail.previous.previous;
            }
        } else {
            node.value = value;
            moveToHead(node);
        }
    }

    private void moveToHead(Node node) {
        // 删除该元素并在头部插入
        node.previous.next = node.next;
        node.next.previous = node.previous;
        
        node.next = head.next;
        head.next.previous = node;

        node.previous = head;
        head.next = node;
    }

    private void insertNewNode(int key, int value) {
        Node node = new Node(key, value);
        head.next.previous = node;
        node.next = head.next;
        node.previous = head;
        head.next = node;
        cache.put(key, node);
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```


### 7.[力扣排行榜](https://leetcode-cn.com/problems/design-a-leaderboard/)<br>
（Bloomberg 在半年内面试中考过）<br>

### 8.[56.合并区间](https://leetcode-cn.com/problems/merge-intervals/)<br>
（Facebook、字节跳动、亚马逊在半年内面试中常考）<br>
暴力方式<br>
执行302ms,击败5.01%的用户<br>
```
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals == null || intervals.length == 0) return new int[0][0];
        // 1.intervals中各数组按照首元素先排序
        // 2.遍历排序后的数组合并数组
        int length = intervals.length;
        for (int i = 0; i < intervals.length; i++) {
            for (int j = 0; j < intervals.length - i - 1; j++) {
                if (intervals[j][0] > intervals[j + 1][0]) {
                    int[] tt = intervals[j + 1];
                    intervals[j + 1] = intervals[j];
                    intervals[j] = tt;
                }
            }
        }
        List<int[]> list = new ArrayList();
        int[] temp = intervals[0];
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] <= temp[1] && intervals[i][1] >= temp[1]) {
                // temp[0] = Math.min(intervals[i][0], temp[0]);
                temp[1] = Math.max(temp[1], intervals[i][1]);
            } else if (intervals[i][0] > temp[1]) {
                list.add(temp);
                temp = intervals[i];
            }
        }
        list.add(temp);
        int[][] result = new int[list.size()][2];
        for (int i = 0; i < list.size(); i++) {
            result[i] = list.get(i);
        }
        return result;
    }
}
```


