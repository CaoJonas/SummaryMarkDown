<center style="font-size: 45px;">二分法</center>
二分法

```java
public int binarySearch(int n) {
     int low = 0;
     int high = nums.length - 1;
     int midNum;
     while(low <= high) {
          midNum = (low + high) / 2;
          if(nums[midNum] == target) {
               return midNum;
          }else if(nums[midNum] > target) {
               high = midNum - 1;
          }else{
               low = midNum + 1;
          }
     }
     return -1;
}
```

另类二分法

```java
public static int[] twoSum3(int[] numbers, int target) {
    int []indexArr = {0, 1};
    int left, right, mid, findNum;
    left = 0;
    right = numbers.length - 1;

    while(left <= right) {

        if(numbers[left] + numbers[right] > target) {
            right --;
        }else if(numbers[left] + numbers[right] < target) {
            left ++;
        }else {
            indexArr[0] = left + 1;
            indexArr[1] = right + 1;
            break;
        }
    }

    return indexArr;
}
```

另类二分法

> 只是用一次二分法， 如果
>
> - target < nums[mid], right = nums[mid]
> - target > nums[mid], left = nums[mid],
> - 这算节省一半时间

```java
public static int searchInsert(int[] nums, int target) {
     int left = 0;
     int right = nums.length;
     int mid = (left + right ) / 2;
     if(target <= nums[mid]) {
          right = mid;
     }else{
          left = mid;
     }
     return  0;
}
```

