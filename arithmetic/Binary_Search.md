# Binary_Search

## [有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

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

