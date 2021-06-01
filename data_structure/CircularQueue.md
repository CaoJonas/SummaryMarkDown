# Circular_Queue

## 循环队列特性

循环队列可以充分利用内存空间，

1. 元素初始化  head == tail

2. 队列为空  head == tail % maxLen

3. 队列已满

   1. 利用 尾指针始终指向当前插入的下一个元素

      head = (tail + 1) % maxLen  // 表示已满

   2. 利用一个标识，记录数组中元素的个数

## 利用 尾指针始终指向当前插入的下一个元素

```java
static class CycleQueue {
    int maxLen;
    int head;
    int tail;
    int[] item;
    CycleQueue(int[] item) {
        this.item = item;
        this.head = 0;
        this.tail = 0;
        this.maxLen = item.length;
    }
    Integer delItem() {
        if (tail == head) {
            return null;
        } else {
            Integer num = item[head];
            item[head] = num;
            if(++head == item.length) { // 如果头指针 == length ; 
                head = 0;
            }
            return num;
        }
    }
    boolean addItem(int addNum) {
        if ((tail + 1) % maxLen == head) {
            return false;
        } else {
            item[tail] = addNum;
            if(++tail == item.length) {  // 如果尾指针 == length
                tail = 0;
            }
            return true;
        }
    }
}
```

## 利用一个标识，记录数组中元素的个数

> 其实 该方法也是让 tail 指向插入元素的下一个位置， 但是不能利用 tail == take 判断是否包含元素，有可能是空；这也就是 count == item.length 的作用
>
> 这里面比较有意思的

```java
static class CycleQueue2 {
    int head;
    int tail;
    int count;
    int[] item;
    CycleQueue2(int[] item) {
        this.item = item;
        this.head = 0;
        this.tail = 0;
        this.count = 0;
    }
    // 怎样保证是否为空
    Integer delItem() {
        if (count == 0) {
            return null;
        } else {
            Integer num = item[head];
            item[head] = num;
            if(++head == item.length) {
                head = 0;
            }
            count--;
            return num;
        }
    }
    Integer removeAt(int removeAtIndex) {
        if(count == 0) {
            
        }
        // 按照元素下标删除一个元素
        // 如果当前下标是 数组的头元素
		if(removeAtIndex == head) {
            item[removeAtIndex] = null;
            if(++head == item.length) {
                head = 0;
            }
        }else{
            //首先记录 next; 如果 next == item.length ; 将 next = 0
            int next = removeAtIndex + 1;
            if(next == item.length) {
                next = 0;
            }
            // 因为 tail 始终指向最后一个元素的下一个位置，因此，如果 next == tail 说明 removeAtIndex 就是最后一个元素，因此，直接停止即可
            if(next == tail) {
                item[removeAtIndex] = null;
                tail = removeAtIndex;
                break;
            }else{
                // 如果不是，则需要一次循环，知道找到该下标位置
                item[removeAtIndex] = item[next];
                removeAtIndex = next;
            }
        }
    }
    boolean addItem(int addNum) {
        if (count == item.length) {
            return false;
        } else {
            item[tail] = addNum;
            if(++tail == item.length) {
                tail = 0;
            }
            count++;
            return true;
        }
    }
}
```