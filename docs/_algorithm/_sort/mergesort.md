### 1. 归并

归并排序应用分治的思想，基本操作为合并两个有序数组使整体有序

```java
public void Merge(int arr[], int l, int r, int rightEnd) {
    int[] tmpArr = new int[rightEnd - l];
    int leftEnd = r, i = 0;
    while (l < leftEnd && r < rightEnd) {
        if (arr[l] < arr[r])
            tmpArr[i++] = arr[l++];
        else
            tmpArr[i++] = arr[r++];
    }
    while (l < leftEnd)
        tmpArr[i++] = arr[l++];
    while (r < rightEnd)
        tmpArr[i++] = arr[r++];
    for (int k = tmpArr.length - 1; k >= 0; --k)
        arr[rightEnd - k - 1] = tmpArr[tmpArr.length - 1 - k]; // Never
}
```

### 2. 递归版本

二分，左右排好序后再归并

```java
public void MergeSort(int arr[], int l, int r) {
    if (l >= r - 1)
        return;
    int mid = (l + r) / 2;
    MergeSort(arr, l, mid);
    MergeSort(arr, mid, r);
    Merge(arr, l, mid, r);
}
```

### 3. 非递归版本

从 `length=1` 开始，把数组视为连续的归并段，每一轮 (Pass) 两两归并

```java
public void MergeSort(int arr[]) {
    int length = 1;
    while (length < arr.length) {
        MergePass(arr, length);
        length *= 2;
    }
}

public void MergePass(int arr[], int length) {
    int i;
    for (i = 0; i <= arr.length - 2 * length; i += 2 * length)
        Merge(arr, i, i + length, i + length * 2);
    if (i + length <= arr.length - 1)
        Merge(arr, i, i + length, arr.length);
}
```
