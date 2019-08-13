# Binary_Search

## easy

### [有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

> 给定一个正整数 num，编写一个函数，如果 num 是一个完全平方数，则返回 True，否则返回 False。
>
> 说明：==不要使用任何内置的库函数，如  sqrt。==
>
> 示例 1：
>
> 输入：16
> 输出：True
> 示例 2：
>
> 输入：14
> 输出：False

==这个比较有意思的是，不提倡使用 一些库函数，但是利用 一个数的平方，往往会造成数的溢出（double, long 都会溢出），所以那我们不利用 平方，而是利用 A/ B= C，比较B和C的值==

> double multMid = (double)num / mid;  这样就可以知道两个因子， 然后比较两个数的大小， 如果
>
> B 》 C，表示 B 过于大，需要 high 减少。
>
> 例如：16 / 8 == 2， B > C; () ,B 要变小， B = (high + low) / 2, 所以需要 high = mid - 1; 正常的二分法。

```java
public boolean isPerfectSquare(int num) {
     if(num == 1) {
          return true;
     }
     int low = 1;
     int high = num / 2;
     while(low <= high) {
          int mid = low + (high - low) / 2;
          double multMid = (double)num / mid;
          if(multMid < mid) {
               high = mid - 1;
          }else if(multMid == mid) {
               return true;
          }else {
               low = mid + 1;
          }
     }
     return false;        
}
```

### [第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

> 你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。
>
> 假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。
>
> 你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。
>
> 示例:
>
> 给定 n = 5，并且 version = 4 是第一个错误的版本。
>
> 调用 isBadVersion(3) -> false
> 调用 isBadVersion(5) -> true
> 调用 isBadVersion(4) -> true
>
> 所以，4 是第一个错误的版本。

==其实这道题就类似于 寻找 大于等于错误版本的第一个==,而我们下面的算法是求出小于第一个版本的，所以我么要在最后 high + 1

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
     /*
    其实这道题有点类似于 找出 大于 等于 第一个错误的版本。
    */
     public int firstBadVersion(int n) {
          int low = 0, high = n;
          while(low <= high) {
               int mid = low + (high - low) / 2;
               if(isBadVersion(mid)) {
                    high = mid - 1;
               }else{
                    low = mid + 1;
               }
          }
          return high + 1;
     }
}
```



