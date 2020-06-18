# 学习心得<br>
## 做题步骤<br>
#### 1.clarification:corner case<br>
#### 2.possible solution ---> optimal(time & space)<br>
#### 3.code<br>
#### 4.test cases<br>
<br>

## 强调:<br>
**代码模板本身的话一定要死记硬背下来,刷题时变成一种条件反射.**<br>
不要再花五分钟时间思考怎么写,然后在纸上慢慢调试,最后才把这代码弄出来,这样是不能被接受的.<br>
**代码模板:**<br>
BFS代码/DFS代码-递归写法/<br>
树的前中后序遍历<br>

<br>

## 相关链接<br>
数据结构中数据的变化展示:https://visualgo.net/zh/bst<br>
数据结构时间复杂度查询:https://www.bigocheatsheet.com/<br>
HashMap解析文章:https://juejin.im/post/5dedb448f265da33b071716a

# [算法知识树梳理](http://note.youdao.com/noteshare?id=39068496ec76452129afae7589764371&sub=1B0E068D2A7645B5B4F37AF45A8E4BD1)<br>

# 作业题<br>
## 1.写一个关于 HashMap 的小总结<br>
Constructs an empty <tt>HashMap</tt> with the default initial capacity (16) and the default load factor (0.75).<br>
HashMap默认容量为16,装载因子为0.75F.<br>
DEFAULT_LOAD_FACTOR = 0.75f;<br>
DEFAULT_INITIAL_CAPACITY = 1 << 4; <br>
<br>


```
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    /**
     * The bin count threshold for using a tree rather than list for a
     * bin.  Bins are converted to trees when adding an element to a
     * bin with at least this many nodes. The value must be greater
     * than 2 and should be at least 8 to mesh with assumptions in
     * tree removal about conversion back to plain bins upon
     * shrinkage.
     * **单个拉链达到阈值时使用TreeNode实现拉链**
     */
    static final int TREEIFY_THRESHOLD = 8;
    /**
     *  When allocated, length is always a power of two.
     *  分配时长度总是2的幂.
     */
    transient Node<K,V>[] table;
    transient int size;
    /**
     * The number of times this HashMap has been structurally modified
     * Structural modifications are those that change the number of mappings in
     * the HashMap or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the HashMap fail-fast.  (See ConcurrentModificationException).
     * HashMap被结构性修改的次数
     */
    transient int modCount;
    /**
     * The next size value at which to resize (capacity * load factor).
     */
    int threshold;
    ...
}
```


HashMap基于链表解决散列冲突,链表有两种实现,一种是单链表Node实现,另一种是红黑树TreeNode实现.<br>


```
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        ...
}
```


```
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;
        ...
}

public class LinkedHashMap<K,V> extends HashMap<K,V>
    implements Map<K,V> {
    static class Entry<K,V> extends HashMap.Node<K,V> {
            Entry<K,V> before, after;
            ...
    }
}
```
<br>
<b>put</b><br>

```
/**
 * Associates the specified value with the specified key in this map.
 * If the map previously contained a mapping for the key, the old
 * value is replaced.
 *插入元素key相同时,后者key会替换前者key.
 */
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
}

/**
 * Implements Map.put and related methods
 *
 * @param hash hash for key
 * @param key the key
 * @param value the value to put
 * @param onlyIfAbsent if true, don't change existing value
 * @param evict if false, the table is in creation mode.
 * @return previous value, or null if none
 */
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 槽位没有元素时就直接插入
        tab[i] = newNode(hash, key, value, null);
    else {
        // 槽位已存在元素
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            // 新插入元素与已有元素的key相同时
            e = p;
        else if (p instanceof TreeNode)
            // 槽位元素已是TreeNode类型,就在红黑树中直接插入
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                    // 拉链个数>8时,将拉链的链表转为红黑树
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            // onlyIfAbsent=false, key元素已存在时新插入元素的值直接覆盖旧值
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        // threshold容量阈值,默认为16
        // 大于容量时启动扩容
        resize();
    afterNodeInsertion(evict);
    return null;
}

/**
 * Replaces all linked nodes in bin at index for given hash unless
 * table is too small, in which case resizes instead.
 * 替换bin中给定哈希下标的所有的链接节点
 */
final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        ...
}

/**
 * Tree version of putVal.
 */
final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab,
                                       int h, K k, V v) {
                                       ...

}

    /**
     * Initializes or doubles table size.  If null, allocates in
     * accord with initial capacity target held in field threshold.
     * Otherwise, because we are using power-of-two expansion, the
     * elements from each bin must either stay at same index, or move
     * with a power of two offset in the new table.
     *
     * @return the table
     */
    final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                // 扩容一倍
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        // 设置扩容后新的阈值
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                // 依次搬移所有已存在的数据
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        // 槽位只有一个元素时重新计算新槽位并放入
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            // 按位与(&)计算原有数据元素落入新数组中的哪个槽内
                            // 例如原容量为4时
                            // 1111 & 0011 ==> 0011:3
                            // 1011 & 0011 ==> 0011:3
                            // 扩容为8
                            // 1111 & 0111 ==> 0111:7
                            // 1011 & 0111 ==> 0011:3
                            // ((e.hash & oldCap) == 0) 当遍历到原始的第3个槽时
                            // 1111 & 0100 ==> 0100: 3+4(oldCap) 
                            // 1011 & 0100 ==> 0000: 3
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

<b>get</b><br>
```
/**
 * Returns the value to which the specified key is mapped,
 * or {@code null} if this map contains no mapping for the key.
 */
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
}

/**
 * Implements Map.get and related methods
 */
final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        ...
}

/**
 * Calls find for root node.
 */
final TreeNode<K,V> getTreeNode(int h, Object k) {
    return ((parent != null) ? root() : this).find(h, k, null);
}
```




## 2.[有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/description/)<br>
（亚马逊、Facebook、谷歌在半年内面试中考过）<br>
**排序方式**<br>
时间复杂度O(nlogn) 排序耗时最多<br>
空间复杂度O(n) 使用了额外的两个数组<br>
执行时间3ms,击败88.16%用户<br>

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

**哈希表法**<br>
时间复杂度O(n)<br>
空间复杂度O(1)<br>
执行时间5ms,击败52.39%的用户<br>
理解:可能受数据规模小的影响.<br>
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



## 3.[两数之和](https://leetcode-cn.com/problems/two-sum/description/)<br>
（近半年内，亚马逊考查此题达到 216 次、字节跳动 147 次、谷歌 104 次，Facebook、苹果、微软、腾讯也在近半年内面试常考）<br>
**暴力法**<br>
时间复杂度O(n^2),空间复杂度O(1)<br>

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            int temp = target - nums[i];
            for (int j = i + 1; j < nums.length; j++) {
                if (temp == nums[j]) {
                    return new int[] {i, j};
                }
            }
        }
        return null;
    }
}
```

**一遍hash表法**<br>
时间复杂度O(n),空间复杂度O(n)<br>

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap();
        for (int i = 0; i < nums.length; i++) {
            int temp = target - nums[i];
            if (map.containsKey(temp)) {
                return new int[] {map.get(temp), i};
            }
            map.put(nums[i], i);
        }
        return null;
    }
}
```



## 4.[N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/description/)<br>
（亚马逊在半年内面试中考过）<br>
**树的前序遍历--递归法**<br>
时间复杂度O(n),空间复杂度O(n)<br>
执行时间1ms,击败96.88%的用户<br>

```
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    private List<Integer> list = new ArrayList();
    public List<Integer> preorder(Node root) {
        if (root == null) {
            return list;
        }
        list.add(root.val);
        for (int i = 0; i < root.children.size(); i++) {
            preorder(root.children.get(i));
        }
        return list;
    }
}
```

**基于栈的实现方式**<br>
时间复杂度O(n) n为树中节点的个数<br>
空间复杂度O(n) 最坏情况树只有两层,除根节点外其余全在第二层<br>
执行时间6ms,击败7.89%的用户<br>

```
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> list = new ArrayList();
        if (root == null) {
            return list;
        }
        Stack<Node> stack = new Stack();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node temp = stack.pop();
            list.add(temp.val);
            for (int i = temp.children.size() - 1; i >= 0; i--) {
                stack.push(temp.children.get(i));
            }
        }
        return list;
    }
}
```

```
class Solution {
    public List<Integer> preorder(Node root) {
        LinkedList<Node> stack = new LinkedList<>();
        LinkedList<Integer> output = new LinkedList<>();
        if (root == null) {
            return output;
        }
        stack.add(root);
        while (!stack.isEmpty()) {
            Node node = stack.pollLast();
            output.add(node.val);
            Collections.reverse(node.children);
            for (Node item : node.children) {
                stack.add(item);
            }
        }
        return output;
    }
}
```



## 5.自学[HeapSort](https://www.geeksforgeeks.org/heap-sort/)<br>
1.建堆O(n),建堆有两种方式:自顶向下和自底向上,自底向上从第一个非叶子节点开始,堆化次数要少<br>
2.堆顶取数据,堆化O(logn),不断重复该过程<br>
时间复杂度为O(nlogn),空间复杂度O(1)<br>
堆排序是原地排序,排序只需要额外个别的存储空间<br>
**堆排序不是稳定的排序算法**,因为涉及堆尾元素与堆顶元素交换的操作<br>
**堆排序的缓存友好性不好**,因为数据是跳着访问的<br>

## 6.[字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)<br>
（亚马逊在半年内面试中常考）<br>
散列表方式<br>
时间复杂度O()<br>
空间复杂度O()<br>
执行11ms,击败48.18%的用户<br>

```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // 基于散列表,key为strs中排序后的字符串,list为value来存放
        List<List<String>> list = new ArrayList();
        if (strs == null) {
            return list;
        }
        Map<String, List<String>> map = new HashMap();
        for (int i = 0; i < strs.length; i++) {
            char[] chars = strs[i].toCharArray();
            Arrays.sort(chars);
            String key = String.valueOf(chars);
            if (!map.containsKey(key)) {
                map.put(key, new ArrayList());
            }
            map.get(key).add(strs[i]);
        }
        list.addAll(map.values());
        return list;
    }
}
```


## 7.[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)<br>
（亚马逊、字节跳动、微软在半年内面试中考过）<br>
**递归方式**<br>
时间复杂度O(n)<br>
空间复杂度最坏情况O(n)退化为单链表<br>
空间复杂度平均情况O(logn) logn即为栈的深度<br>

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    private List<Integer> list = new ArrayList();

    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return list;
        }
        inorderTraversal(root.left);
        list.add(root.val);
        inorderTraversal(root.right);
        return list;
    }
}
```

**通过栈的方式**<br>
时间复杂度O(n)<br>
空间复杂度O(n):n为树的高度<br>

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList();
        Stack<TreeNode> stack = new Stack();
        TreeNode current = root;
        while (current != null || !stack.isEmpty()) {
            while (current != null) {
                stack.push(current);
                current = current.left;
            }
            current = stack.pop();
            list.add(current.val);
            current = current.right;
        } 
        return list;
    }
}
```


## 8.[二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)<br>
（字节跳动、谷歌、腾讯在半年内面试中考过）<br>
**递归方式**<br>
时间复杂度O(n)<br>
空间复杂度最坏情况O(n)退化为单链表<br>
空间复杂度平均情况O(logn) logn即为栈的深度<br>

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private List<Integer> list = new ArrayList();
    public List<Integer> preorderTraversal(TreeNode root) {
        if (root == null) {
            return list;
        }
        list.add(root.val);
        preorderTraversal(root.left);
        preorderTraversal(root.right);
        return list;
    }
}
```

**广度优先搜索方式**<br>
时间复杂度O(n)<br>
空间复杂度O(n)<br>
最好情况退化为单链表,理想情况为(第0层节点个数的一半)n/4+1<br>
执行时间1ms,击败48.79%的用户<br>
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList();
        if (root == null) {
            return list;
        }
        Stack<TreeNode> stack = new Stack();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode temp = stack.pop();
            list.add(temp.val);
            if (temp.right != null) {
                stack.push(temp.right);
            }
            if (temp.left != null) {
                stack.push(temp.left);
            }
        }
        return list;
    }
}
```



## 9.[N 叉树的层序遍历](https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/)<br>
（亚马逊在半年内面试中考过）<br>


## 10.[丑数](https://leetcode-cn.com/problems/chou-shu-lcof/)<br>
（字节跳动在半年内面试中考过）<br>


## 11.[前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)<br>
（亚马逊在半年内面试中常考）<br>



# 解析题<br>
## 1.[N 叉树的后序遍历](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)<br>
（亚马逊在半年内面试中考过）<br>


## 2.[N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/description/)<br>
（亚马逊在半年内面试中考过）<br>


## 3.[最小的 k 个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)<br>
（字节跳动在半年内面试中考过）<br>


## 4.[滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)<br>
（亚马逊在半年内面试中常考）<br>


# 思考题<br>
## 1.树的面试题解法一般都是递归，为什么？<br>
因为用循环写法实现起来比较麻烦<br>

## 2.自己画一下有向有权图<br>

![邻接矩阵有向有权图](https://github.com/KenZhang2018/algorithm010/blob/master/Week02/%E9%82%BB%E6%8E%A5%E7%9F%A9%E9%98%B5%E6%9C%89%E5%90%91%E6%9C%89%E6%9D%83%E5%9B%BE.jpg)<br>

参考链接<br>
[连通图个数](https://leetcode-cn.com/problems/number-of-islands/)<br>
[拓扑排序（Topological Sorting）](https://zhuanlan.zhihu.com/p/34871092)<br>
[最短路径（Shortest Path）：Dijkstra](https://www.bilibili.com/video/av25829980?from=search&seid=13391343514095937158)<br> 
[最小生成树（Minimum Spanning Tree）](https://www.bilibili.com/video/av84820276?from=search&seid=17476598104352152051)<br> 





