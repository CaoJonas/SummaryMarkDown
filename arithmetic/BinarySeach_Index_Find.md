# Binary Search

1. ### 在数组有序时，我们可以利用二分查找

2. ### 在数组无序时，我们可以利用快速排序

## 利用快速排序

![](\arithmetic_image\quick_find_index.jpg)

> 快速排序主要针对无序的，如果希望找出无序数组中，小于等于当前数字的下标，就可以利用快速排序，将小于等于当前数字都放在数组的左面，否则放在右边。
>
> 但是由于 start和end 都不能为负数，因此，我们需要判断结尾
>
> 如果，在进行快速排序之后，`小于等于的都在 target 之前，大于 都在target 之后`，但是，如果 target < arr[0] 就说明数组中没有小于等于 target的元素。
>
> 同样，在进行快速排序之后，`小于等于的都在 target 之前，大于 都在target 之后`，但是，如果，target > arr[arr.length - 1] ? arr.length : start;  说明数组中没有大于 target 的元素

```java
/**
     * 查找小于等于 target 的元素的个数
     * @param arr
     * @param target
     * @param start
     * @param end
     * @return
     */
public static int quickSortFindTargetLET(int[] arr, int target, int start, int end) {
    while (start < end) {
        while (start < end && arr[end] > target) {
            end--;
        }
        int temp = arr[end];
        arr[end] = arr[start];
        arr[start] = temp;
        while (start < end && arr[start] <= target) {
            start++;
        }
        temp = arr[end];
        arr[end] = arr[start];
        arr[start] = temp;
    }
    return target < arr[0] ? -1 : start;
}

/**
     * 查找大于 target 的元素的个数
     * @param arr
     * @param target
     * @param start
     * @param end
     * @return
     */
public static int quickSortFindTargetGT(int[] arr, int target, int start, int end) {
    while (start < end) {
        while (start < end && arr[end] > target) {
            end--;
        }
        int temp = arr[end];
        arr[end] = arr[start];
        arr[start] = temp;
        while (start < end && arr[start] <= target) {
            start++;
        }
        temp = arr[end];
        arr[end] = arr[start];
        arr[start] = temp;
    }
    return target > arr[arr.length - 1] ? arr.length : start;
}
```

## 利用二分查找

> 当然，必须要求有序数据，才能查找

> 二分查找 的要求： 1. 顺序存储  2. 数组有序
>
> > 其实我们发现，如果希望查找 小于等于 target 的第一个下标，利用一下，`end` 就是第一个小于等于target 的下标（==当然有可能小于零==）
> >
> > ```java
> > if (arr[mid] <= target) {
> >     start = mid + 1;
> > } else {
> >     end = mid - 1;
> > }
> > return end;
> > ```
> >
> > 同样，在循环结束时，end 肯定 小于 start, 如果 end 是小于等于 target 的下标，那么 start 肯定就是大于 target 的第一个下标。
> >
> > ```java
> > if (arr[mid] <= target) {
> >     start = mid + 1;
> > } else {
> >     end = mid - 1;
> > }
> > return start;
> > ```

```java
/**
 * 小于等于 target
 *
 * @param arr
 * @param target
 * @return
 */
public static int findTargetLET(int[] arr, int target) {
    int start = 0, end = arr.length - 1;
    while (start <= end) {
        int mid = start + (end - start) / 2;
        if (arr[mid] <= target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    return end;
}

/**
 * 大于 target
 *
 * @param arr
 * @param target
 * @return
 */
public static int findTargetGT(int[] arr, int target) {
    int start = 0, end = arr.length - 1;
    while (start <= end) {
        int mid = start + (end - start) / 2;
        if (arr[mid] <= target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    return start;
}
```