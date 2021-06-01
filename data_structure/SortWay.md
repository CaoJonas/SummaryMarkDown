#### 冒泡排序

[**冒泡排序及其改进**](https://www.cnblogs.com/mistermoney/p/9550590.html)

- 正常冒泡

> 思想就是每次把最大数选择出来，放在最后一个位置上。
>
> j < nums.length - i - 1	// 因为要比较 nums[j] 和 nums[j + 1],

```java
public static void bubbleSort(int []nums) {

     for(int i = 0; i < nums.length - 1; i++) {
          for(int j = 0; j < nums.length - i - 1; j++) {
               if(nums[j] > nums[j + 1]) {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
               }
          }
     }

}
```

- 冒泡改进一

> 如果每次排序的时候，再拍第 i 次的时候， 经过前面元素的交换， 数组现在已经有序了， 如果原先的方法 10 个数就是要比较 9 次。
>
> 如果我们这次记录下来，在进行 第 i 次排序的时候，我们用字段 isSort （boolean)记录前面的元素是否有交换， 如果没有，则说明数组已经有序，可以提前终止 比较。

```java
public static void bubbleSortIm1(int []nums) {

     for(int i = 0; i < nums.length - 1; i++) {
          boolean isSort = false;	// 
          for(int j = 0; j < nums.length - i - 1; j++) {
               if(nums[j] > nums[j + 1]) {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                    isSort = true;
               }
          }
          if(!isSort) {	// false 表示已经 无元素需要比较，提前终止
               break;
          }
     }

}
```

- 冒泡改进二

> 我们刚才的方法只是考虑了数组前面的元素， 如果我们在排序时。发现 虽然 10 个元素我们要比较 9 次， 但是有可能只交换了 5次， 所以，如果我们记录下来最后一次交换元素的下标， 也就是说 这之后，数组就是有序的。
>
> 并且， 我们结合改进方法一， 如果前面的元素也有序，我们终止循环。

```java
public static void bubbleSortIm2(int []nums) {

     int borderIndex = nums.length - 1;
     for(int i = 0; i < nums.length - 1; i++) {
          boolean isSort = false;
          int tempIndex = 0;
          for(int j = 0; j < borderIndex; j++) {
               if(nums[j] > nums[j + 1]) {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                    tempIndex = j + 1;
                    isSort = true;
               }
          }
          borderIndex = tempIndex + 1;	// 交换的下标是取到 j + 1; 但是又因为 j < borderIndex ,所以我们取元素  tempIndex + 1
          if(!isSort) {	
               break;
          }
     }
}
```

#### 选择排序

##### 稳定方法

> 如果，我们每次比较，然后进行交换，就不会改变两个相同位置

```java
for(int i = 0; i < arr.length - 1; i++) {
    for(int j = i + 1; j < arr.length; j++) {
        if(arr[i] > arr[j]) {
            int temp = arr[j];
            arr[j] = arr[i];
            arr[i] = temp;
        }
    }
}
```

##### 不稳定方法

> 如果找出最小的下标 再交换，就会改变相同数字的位置

```java
for(int i = 0; i < arr.length - 1; i++) {
    int minIndex = i;
    for(int j = i + 1; j < arr.length; j++) {
        if(arr[minIndex] > arr[j]) {
            minIndex = j;
        }
    }
    int temp = arr[minIndex];
    arr[minIndex] = arr[i];
    arr[i] = temp;
}
```

#### 希尔排序

> 希尔排序就是有点类似于 插入排序。第三层内 第 i 个 元素 要和 第 i - h 个元素比较， 如果 i > i - h, 则终止比较， 因为 前 i - h, i - 2h 。。。已经有序。

```java
public static void shellSort(int []nums) {
     int temp;
     int h = 1;
     while(h < (nums.length / 3)) {
          h = 3 * h + 1;
     }
     while(h >= 1) {
          for(int i = h; i < nums.length; i++) {
               for(int j = i; j >= h; j -= h) {
                    if(nums[j] < nums[j - h]) {
                         temp = nums[j];
                         nums[j] = nums[j - h];
                         nums[j - h] = temp;
                    }
               }
          }
          h = h / 3;
     }
}
```

#### 插入排序

> 插入排序的思想就是当前元素是 第 i 个元素， 前 i - 1个元素已经有序。现在要做的就是 把 第 i 个 元素 和 之前的元素相比较， 如果 第 i - 1 个元素 小于 第 i 个元素。我们就可以认为 前 i 个元素已经有序。

```java
public static void insertSort(int []nums) {
        int temp;
        for(int i = 1; i < nums.length; i++) {
            for(int j = i; j > 0; j--) {
                if(nums[j] < nums[j - 1]) {
                    temp = nums[j];
                    nums[j] = nums[j - 1];
                    nums[j - 1] = temp;
                }
            }
        }
    }
```

> 这种插入排序比较有意思， 就是每次利用 当前 currValue 和 之前的数比较， 如果小， 则交换， 否则终止， 再循环结束后， nums[j] = currValue.

```java
public void insertSort(int[] arr) {
    int temp = 0;
    for(int i = 1; i < arr.length; i++) {
        temp = arr[i];
        int j = i - 1;
        for(; j >= 0 && temp < arr[j]; j--) {  // j >= 0
            arr[j + 1] = arr[j];
        }
        arr[j + 1] = temp;
    }
    System.out.println(Arrays.toString(arr));
}
```

> ==这种比较有意思， 就是如果 我的前面一些是有序的， 我就跳过这些， 直接从非有序开始==

```java
public static void insertSortOp3(int []arr) {
    if (arr == null || arr.length == 0) {
        return;
    }
    int start = 1;
    while(start < arr.length && arr[start] > arr[start - 1]) {
        start++;
    }
    for(int i = start; i < arr.length; i++) {
        int currNum = arr[i], j = i;
        while(j > 0 && currNum < arr[j - 1]) {
            arr[j] = arr[j - 1];
            j--;
        }
        arr[j] = currNum;
    }
}
```

> 这种方法时利用二分，首先找到 第一个大于 arr[i] 的数字，然后，将数字后移，然后替换
>
> 记住，其实这里就是用 二分查找 找出最后一个小于等于 arr[i] 的数字，这个时候， end 指向 大于 nums[i] 的前一个位置，因此，需要end + 1;

```java
public static void insertSortOptm(int []arr) {
    long startTime = System.currentTimeMillis();
    if(arr == null || arr.length <= 1) {
        return;
    }
    for(int i = 1; i < arr.length; i++) {
        int start = 0, end = i - 1;
        while (start <= end) {
            int mid = start + (end - start) / 2;
            if(arr[mid] <= arr[i]) {
                start = mid + 1;
            }else {
                end = mid - 1;
            }
        }
        int temp = arr[i];
        for(int n = i; n > end + 1; n--) {
            arr[n] = arr[n - 1];
        }
        arr[end + 1] = temp;
    }
    System.out.println(System.currentTimeMillis() - startTime);
}
```

#### ==保证奇偶有序==

```java
public void insertSort(int[] arr, int end) {
    int temp = arr[end];
    int j = end - 2;
    for (; j >= 0 && temp < arr[j]; j -= 2) {
        arr[j + 2] = arr[j];
    }
    arr[j + 2] = temp;
}

public void insertSortMain(int[] arr) {
    int evenStart = 0, oddStart = 1;
    int arrLen = arr.length - 1;
    int count = 0;
    while (evenStart <= arrLen && oddStart <= arrLen) {
        count += 1;
        int temp = arr[arrLen];
        if ((temp & 1) == 1) {
            arr[arrLen] = arr[oddStart];
            arr[oddStart] = temp;
            insertSort(arr, oddStart);
            oddStart += 2;
        } else {
            arr[arrLen] = arr[evenStart];
            arr[evenStart] = temp;
            insertSort(arr, evenStart);
            evenStart += 2;
        }
    }
    insertSort(arr, arrLen);
    System.out.println(Arrays.toString(arr));
}

public static void main(String[] args) {
    int[] arr = {100, 10, 1, 5, 7, 3, 10, 8, 12, 2, 11, 3};
    Practice practice = new Practice();
    practice.insertSortMain(arr);
}
```

#### 快速排序

![](data_structure_imgs/quickSort_2.jpg)

> 快速排序的思想就是首先找到一个能把 数组分成两部分的一个关键字 keyNote 的下标（==最好是能评分整个数组为两部分==）然后对 low - (keyNoteIndex  - 1)和 keyNoteIndex + 1 - high 两部分进行上面的操作，也是一个递归操作。
>
> 唯一的退出条件就是 low = high, 所以最后的返回值 low 和 返回 high 相同

1. 快速排序的递归次数与元素的初始排列有关，如果每一次的划分后分区比较平衡，则递归次数比较少。
2. 快速排序的递归次数与分区处理顺序无关，既先处理较长的分区或先处理较短的分区都不影响递归次数

```java
public static void swap(int []nums, int low, int high) { // 交换函数
     int temp;
     temp = nums[high];
     nums[high] = nums[low];
     nums[low] = temp;
}

public static int quickSort(int []nums, int low, int high) {
     int keyNote = nums[low];

     while(low < high) {	// 唯一的退出条件就是 low = high, 所以最后的返回值 low 和 返回 high 相同
          while(low < high && nums[high] >= keyNote) {
               high --;
          }
          swap(nums, low, high);
          while(low < high && nums[low] <= keyNote) {
               low ++;
          }
          swap(nums, low, high);
     }
     System.out.println(low + " = low\t" + "high = " + high);
     return low;
}
public static void quickSortMain(int []nums, int low, int high) {
     if(low >= high) {
          return;
     }
     int keyNoteIndex = quickSort(nums, low, high);
     quickSortMain(nums, low, keyNoteIndex - 1);
     quickSortMain(nums, keyNoteIndex + 1, high);
}
```

#### 2路归并排序

> 这里一定要注意的几点：
>
> 1. 数组长度为 Nums.length; 而进行排序的下标为 0 - (Nums.length - 1)
> 2. 在进行归并时， 一定要 start < end(不需要相等)
> 3. 在最后归并时， 一定要注意： firstStart <= end, 而不是 <

```java
public static void merge(int []num, int low, int mid, int high) {
     int []temp = new int[high - low + 1 ];	 // 也可以直接写成 nums.length;一般传过来的数组为 
      //(1, 3) high - low + 1 三个元素
     int firstStart = low;
     int secondStart = mid + 1;	
	// 要知道第二次循环的时候是  sort(nums, low, mid) 和 sort(nums, mid + 1, high)
     int tempStart = 0;	// 这是暂时数组的下标
     while(firstStart <= mid && secondStart <= high) {
          if(num[firstStart] <= num[secondStart]) {
               temp[tempStart] = num[firstStart++];
          }else{
               temp[tempStart] = num[secondStart++];
          }
          tempStart ++;
     }
     if(firstStart <= mid ){	// 如果A数组没有完全比较
          for(int start = firstStart; start <= mid; start ++) {
               temp[tempStart ++] = num[start];
          }
     }

     if(secondStart <= high ){	// 如果B数组没有完全比较
          for(int start = secondStart; start <= high; start ++) {
               temp[tempStart ++] = num[start];
          }
     }

     for(int i = 0; i < temp.length; i++) {	
          num[low++] = temp[i];
     }
}
public static void sort(int []num, int low, int high) {
     if(high <= low) { // 递归的判断
          return;
     }				//因为是递归，千万不要再循环
     int mid = (low + high) / 2;
     sort(num, low, mid);
     sort(num, mid + 1, high);
     merge(num, low, mid, high);
}
public static void main(String[] args) {
     int []temp = new int[] {1, 3, 2, 4, 9, 0, 1, 3, 10, 5, 7, 9, 0};
     sort(temp, 0, temp.length - 1); // 这里比较有意思的是， 0， temp.length - 1, 是为了放置数组越界， 因为后面的比较都是 <= high的。
}
```

#### 堆排序

[完全二叉树和满二叉树的介绍](https://blog.csdn.net/CTO_1649900265/article/details/87913192)

[堆排序详解代码](https://www.cnblogs.com/skywang12345/p/3602162.html)

==堆分为"最大堆"和"最小堆"。最大堆通常被用来进行"升序"排序，而最小堆通常被用来进行"降序"排序==

> 堆排序的思想：
>
> ​	（1）、首先创建堆。 （2）、堆进行排序
>
> - 首先创建堆
>
>   从 array.length / 2 - 1 开始， 到 0 结束， 这里比较有意思的是： 左子树： 2 * i + 1 ， 右子树： 2 * i + 2
>
>   ```java
>   for(int start = array.length / 2 - 1; start >= 0; start --) {	// 堆创建
>        heapSortSmall(array, start, array.length - 1);
>   }
>   ```
>
> - 堆排序
>
>   > 这里比较有意思的是， childIndex < end, 因为后面要比较 
>   >
>   > array[childIndex] > array[childIndex + 1])， 所以 这里要设置  childIndex < end (childIndex 表示左子树)
>
>   ```java
>   if(childIndex < end && array[childIndex] > array[childIndex + 1]) {
>        childIndex += 1;
>   }
>   ```
>
>   > i - 1 是因为 创建堆的时候，已经创建好了 最大堆或最小堆， 经过上面的交换， 可以 0 - arrayLength - 2;
>   >
>   > 虽然建好堆，已经有最大或最小位于根节点，但是我们不交换，为的后面的循环更好写
> >
>   > for(int start = arr.length - 1; start >= 0; start --) {
>   >
>   > ​	heapSort(arr, 0, start);
>   >
>   > }
> 
>   ```java
>   public static void dumpSortMain(int []array) {
>       for(int start = array.length / 2 - 1; start >= 0; start --) {
>         heapSortMax(array, start, array.length - 1);
>       }
>       for(int i = array.length - 1; i >= 0; i--) {
>           heapSortMax(array, 0, i);
>           int temp = array[0];
>           array[0] = array[i];
>           array[i] = temp;
>       }
>       System.out.println(Arrays.toString(array));
>   }
>   ```
> 
>   

##### 最小堆(降序)

```java
public static void heapSortSmall(int []array, int start, int end) {

     int currIndex = start;
     int childIndex = start * 2 + 1;
     for(; childIndex <= end; currIndex = childIndex,childIndex = childIndex * 2 + 1) {
          if(childIndex < end && array[childIndex] > array[childIndex + 1]) {
               childIndex += 1;
          }
          if(array[currIndex] <= array[childIndex]) {	// 停止循环
               break; // 这里表示 左右子树 都小于 根节点
          }else{		// 交换元素
               int temp = array[childIndex];
               array[childIndex] = array[currIndex];
               array[currIndex] = temp;
          }
     }
}

public static void dumpSortMain(int []array) {
    for(int start = array.length / 2 - 1; start >= 0; start --) {
        heapSortMax(array, start, array.length - 1);
    }
    for(int i = array.length - 1; i >= 0; i--) {
        heapSortMax(array, 0, i);
        int temp = array[0];
        array[0] = array[i];
        array[i] = temp;
    }
    System.out.println(Arrays.toString(array));
}
```

```java
[5, 2, 4, 6, 3, 7, 1]
[7, 3, 4, 6, 5, 2, 1]
[7, 5, 4, 6, 3, 2, 1]
[6, 5, 7, 4, 3, 2, 1]
[7, 6, 5, 4, 3, 2, 1]
[7, 6, 5, 4, 3, 2, 1]
```

##### 最大堆(升序)

```java
public static void heapSortMax(int []arr, int start, int end) {
    int currIndex = start;
    int childIndex = start * 2 + 1;
    for(; childIndex <= end; currIndex = childIndex, childIndex = childIndex * 2 + 1) {
        if(childIndex < end && arr[childIndex] < arr[childIndex + 1]) {
            childIndex += 1;
        }
        if(arr[currIndex] < arr[childIndex]) {
            int temp = arr[childIndex];
            arr[childIndex] = arr[currIndex];
            arr[currIndex] = temp;
        }else{
            break;
        }
    }
}

public static void dumpSortMain(int []array) {
    for(int start = array.length / 2 - 1; start >= 0; start --) {
        heapSortMax(array, start, array.length - 1);
    }
    for(int i = array.length - 1; i >= 0; i--) {
        heapSortMax(array, 0, i);
        int temp = array[0];
        array[0] = array[i];
        array[i] = temp;
    }
    System.out.println(Arrays.toString(array));
}
```

```java
[1, 6, 5, 2, 3, 4, 7]
[4, 3, 5, 2, 1, 6, 7]
[1, 3, 4, 2, 5, 6, 7]
[2, 3, 1, 4, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7]
```

#### 基数排序

```java
final static int[] sizeTable = {9, 99, 999, 9999, 99999, 999999, 9999999,
                                99999999, 999999999, Integer.MAX_VALUE};
final static int[] baseNum = {1, 10, 100, 1000, 10000, 100000, 1000000, 10000000, 100000000, 1000000000};

// Requires positive x;  计算一个数的位数
private static int stringSize(int x) {
    for (int i = 0; ; i++)
        if (x <= sizeTable[i])
            return i + 1;
}
// 获取数字 第 k 位上面的数字
private static int getBase(int currNum, int k) {
    return (currNum / baseNum[k - 1]) % 10;
}

/**
    * 基数排序
    * @param nums
    */
public static void baseNum(int[] nums) {
    // 找出最大值
    int maxNum = Integer.MIN_VALUE;
    for(int i = 0; i < nums.length; i++) {
        if(maxNum < nums[i]) {
            maxNum = nums[i];
        }
    }
    // 判断最大值的位数
    int maxLen = stringSize(maxNum);
    for(int i = 1; i <= maxLen; i++) {
        int [] count = new int[11];
        int []tempArr = new int[nums.length];
        // 统计
        for(int j = 0; j < nums.length; j++) {
            // 首先获取 当前位上面的数字
            count[getBase(nums[j], i) + 1]++;
        }
        // 计算索引
        for(int j = 1; j < count.length; j++) {
            count[j] = count[j] + count[j - 1];
        }
        // 数据分类
        for(int j = 0; j < nums.length; j++) {
            tempArr[count[getBase(nums[j], i)]++] = nums[j];
        }
        // 回写
        for(int j = 0; j < nums.length; j++) {
            nums[j] = tempArr[j];
        }
    }
}
```

#### 计数排序

> 计数排序可以看成是特殊的桶排序。待排序序列有n个数据元素且数值范围不大，最大值为k，我们可以划分为k个桶，每个桶内的数据元素值相同，节省了桶内排序的时间。
>
> 2.2.1 时间复杂度
>
> `计数排序的实现过程中，需要遍历两次序列，第一次从前往后划分数据元素，第二次从后往前重新排序，所以计数排序的时间复杂度为O(n)。`
>
> 2.2.2 空间复杂度
>
> `计数排序过程中，首先需要长度为k+1的数组存储每个数值的元素个数，然后重新排序过程中，需要临时数组存放排序结果，所以计数排序的空间复杂度为O(n)。`
>
> 计数排序的实现过程中只有将原序列中数据元素存放到临时数组时会发生位置改变，但是实现使得并不会改变相同元素的相对位置，所以计数排序是稳定的。
>
> 2.3 适用场景
>
> 1）计数排序适合数据范围不大的场景；
>
> 2）计数排序只适合于非负整数排序，如果是其他数据类型，要在不改变其相对大小的情况下，将其转换为非负整数再进行排序。

