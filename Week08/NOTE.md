学习笔记

# [算法知识树梳理](http://note.youdao.com/noteshare?id=39068496ec76452129afae7589764371&sub=1B0E068D2A7645B5B4F37AF45A8E4BD1)<br>


插入排序<br>
```
public int[] insertionSort(int[] array) {
    for (int i = 1; i < array.length; i++) {
        int temp = array[i], insertIndex = i;
//            for (int j = i; j > 0; j-- ) {
//                if (array[j - 1] > temp) {
//                    array[j] = array[j - 1];
//                    if (j == 1) {
//                        array[j - 1] = temp;
//                    }
//                } else if (array[j - 1] <= temp) {
//                    array[j] = temp;
//                    break;
//                }
//            }
        while (insertIndex >= 1 && array[insertIndex - 1] > temp) {
            array[insertIndex] = array[insertIndex - 1];
            insertIndex--;
        }
        array[insertIndex] = temp;
    }
    return array;
}
```

选择排序<br>
```
public int[] selectionSort(int[] array) {
    for (int i = 0; i < array.length; i++) {
        int minValueIndex = i;
        for (int j = i + 1; j < array.length; j++) {
            if (array[j] < array[minValueIndex]) minValueIndex = j;
        }
        int temp = array[i]; array[i] = array[minValueIndex]; array[minValueIndex] = temp;
    }
    return array;
}
```

冒泡排序<br>
```
public int[] bubbleSort(int[] array) {
    for (int i = 0; i < array.length; i++) {
        boolean isExchange = false;
        for (int j = 0; j < array.length - i - 1; j++) {
            if (array[j] > array[j + 1]) {
                int temp = array[j]; array[j] = array[j + 1]; array[j + 1] = temp;
                isExchange = true;
            }
        }
        if (!isExchange) break;
    }
    return array;
}
```

快速排序<br>

```
public int[] quickSort(int[] array, int begin, int end) {
    // recursion terminator
    // process current level logic
    // drill down
    // reverse current level states
    if (end <= begin) return array;
    int pivot = partition(array, begin, end);
    quickSort(array, begin, pivot - 1);
    quickSort(array,pivot + 1, end);
    return array;
}

private int partition(int[] array, int begin, int end) {
    int pivot = end, counter = begin;
    for (int i = begin; i < end; i++) {
        if (array[i] < array[pivot]) {
            int temp = array[i]; array[i] = array[counter]; array[counter++] = temp;
        }
    }
    int temp = array[pivot]; array[pivot] = array[counter]; array[counter] = temp;
    return counter;
}
```

归并排序<br>

```
public int[] mergeSort(int[] array, int begin, int end) {
    // recursion terminator
    // process current level logic
    // drill down
    // reverse current level states
    if (end <= begin) return array;
    int middle = begin + ((end - begin) >> 1);
    mergeSort(array, begin, middle);
    mergeSort(array, middle + 1, end);
    array = merge(array, begin, middle, end);
    return array;
}

private int[] merge(int[] array, int begin, int middle, int end) {
    int l = begin, r = middle + 1, t = 0;
    int[] temp = new int[end - begin + 1];

    while (l <= middle && r <= end) {
//            if (array[l] <= array[r]) {
//                temp[t++] = array[l++];
//            } else {
//                temp[t++] = array[r++];
//            }
        temp[t++] = array[l] <= array[r] ? array[l++] : array[r++];
    }

    while (l <= middle) temp[t++] = array[l++];
    while (r <= end) temp[t++] = array[r++];
    System.arraycopy(temp, 0, array, begin, temp.length);
    return array;
}
```

堆排序<br>

```
public int[] heapSort(int[] array) {
    if (array == null || array.length == 0) return array;
    for (int i = array.length / 2 - 1; i >= 0; i--) // 建堆
        heapify(array, array.length, i);
    for (int i = array.length - 1; i >= 0; i--) { // 排序
        int temp = array[0]; array[0] = array[i]; array[i] = temp;
        heapify(array, i,0);
    }
    return array;
}

private void heapify(int[] array, int length, int i) {
    while (true) {
        int maxPos = i, left = i * 2 + 1, right = i * 2 + 2;
        if (left < length && array[left] > array[maxPos]) maxPos = left;
        if (right < length && array[right] > array[maxPos]) maxPos = right;
        if (maxPos == i) break;
        int temp = array[i]; array[i] = array[maxPos]; array[maxPos] = temp;
        i = maxPos;
    }
}
```


```
// Java
static void heapify(int[] array, int length, int i) {
    int left = 2 * i + 1, right = 2 * i + 2；
    int largest = i;
    if (left < length && array[left] > array[largest]) {
        largest = left;
    }
    if (right < length && array[right] > array[largest]) {
        largest = right;
    }
    if (largest != i) {
        int temp = array[i]; array[i] = array[largest]; array[largest] = temp;
        heapify(array, length, largest);
    }
}
public static void heapSort(int[] array) {
    if (array.length == 0) return;
    int length = array.length;
    for (int i = length / 2-1; i >= 0; i-) // 构建大顶堆
        heapify(array, length, i);
    for (int i = length - 1; i >= 0; i--) { // 移出大顶堆堆顶元素到数组尾部
        int temp = array[0]; array[0] = array[i]; array[i] = temp;
        heapify(array, i, 0);
    }
}
```
