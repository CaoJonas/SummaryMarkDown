# Binary Search

## 利用快速排序

针对无序的数组， 并且只是执行一次的结果， 这个时候就没有必要用到数组的排序，因为其内部实现 也是通过集中排序方式实现的。而直接利用 searchNum(被查找的数)  把 数组分成两部分。

1- 然下面的方法也可以实现 ==小于== searchNum 的元素，就利用下面的函数， 把数组分成两部分。(如果我要查找所有==大于等于==searchNum 的前一个数字的下标)

>  searchNum = 25;
>
> [11, 9, 8, 16, 12, 5, ==13==, 90, 87, 78, 32, 25]
> 6
> [5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]
>
> 小于searchNum 的下标为 6 ---- 大于等于searchNum 的下标为 6 + 1

```java
public static int quickSearch(int []nums, int searchNum) {
     int low = 0;
     int high = nums.length - 1;
     int threshold = searchNum;
     int temp;
     while(low < high) {
          while(low < high && nums[high] >= threshold) {
               high --;
          }
          temp = nums[high];
          nums[high] = nums[low];
          nums[low] = temp;
          while(low < high && nums[low] < threshold) {
               low ++;
          }
          temp = nums[high];
          nums[high] = nums[low];
          nums[low] = temp;
     }     
     return searchNum <= nums[low] ? low - 1: low;
}
```

2- 如果我要查找所有下面的方法==小于 等于==searchNum 的元素，就利用下面的函数， 把数组分成两部分。（当然也可以实现 ==大于searchNum== 的元素的前一个元素（））

>searchNum = 25
>
>[11, 9, 8, 16, 25, 12, 5, ==13==, 87, 90, 78, 32]
>7
>[5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]
>
>==如果大于 25的元素那就是 7 + 1==

```java
public static int quickSearch2(int []nums, int searchNum) { // 小于 等于 searchNum 的最后一个数
     int low = 0;
     int high = nums.length - 1;
     int threshold = searchNum;
     int temp;
     while(low < high) {
          while(low < high && nums[high] > threshold) {
               high --;
          }
          temp = nums[high];
          nums[high] = nums[low];
          nums[low] = temp;
          while(low < high && nums[low] <= threshold) {
               low ++;
          }
          temp = nums[high];
          nums[high] = nums[low];
          nums[low] = temp;
     }
     System.out.println(high + "high\t");
     return searchNum <= nums[low] ? low - 1: low;   // 如果 searchNum <= nums[low], 这个判断必须做。
}
```

## 利用二分查找

面对有序的数组， 可以直接利用二分查找进行，但不同于 二分查找一个数字，如果包含，返回下标， 不过没有，返回 -1； 

1-这里常见的要求便是，返回最后一个小于 searchNum 的数的下标。也就是找（大于等于 searchNum）数的前一个数字的下标。

> [5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]
> 6
> [5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]

```java
public static int binary1(int nums[], int searchNum) {  // 小于 searchNum 的最后一个数字。 大于等于searchNum 的第一个数字
     int low = 0, high = nums.length - 1;
     if(searchNum < nums[0]) {
          return -1;
     }else if(searchNum > nums[nums.length - 1]){
          return nums.length - 1; //
     }
     while (low <= high) {
          int mid = low + (high - low) / 2;
          if (nums[mid] >= searchNum)
               high = mid - 1;
          else low = mid + 1;
     }
     return high;
}
```

2- 需要查找小于等于 searchNum 的最后一个数字的下标，也是求 大于 searchNum 的第一个数字的前一个数字下标

> [5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]
> 7
> [5, 8, 9, 11, 12, 13, 16, 25, 32, 78, 87, 90]

```java

public static int binary2(int nums[], int searchNum) { 
     // 小于等于  searchNum 的最后一个数字。 大于 searchNum 的第一个数字。
     int low = 0, high = nums.length - 1;
     if(searchNum < nums[0]) {
          return -1;
     }else if(searchNum > nums[nums.length - 1]){
          return nums.length - 1; //
     }
     while (low <= high) {
          int mid = low + (high - low) / 2;
          if (nums[mid] > searchNum)
               high = mid - 1;
          else low = mid + 1;
     }
     return high;
}
```

3-// 查找小于等于findNum 的数

```java
// 查找小于等于
public int binarySearch(int[] nums, int low, int high, int findNum) {
     if (low > high) {
          return high;
     }
     int mid = low + (high - low) / 2;
     if (nums[mid] <= findNum) {
          return binarySearch(nums, mid + 1, high, findNum);
     } else {
          return binarySearch(nums, low, mid - 1, findNum);
     }
}
```

