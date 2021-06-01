# 开放定址法

## 链地址法

> 第一种方式，就是采用下面的方式， 在插入元素时，如果table[i] == null; 就会先创建新的结点，
>
> ​	然后 table[i] = new Node();
>
> ​	然后创建新的结点，再次插入
>
> 第二种方式，就是数组中的元素一样插入。

![](data_structure_imgs\openaddress_1.png)

```java
public class OpenAddressingTest {

    /**
     * 链 地址 法
     *
     * @param <E>
     */
    static class Node<E> {
        E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
        Node() {

        }
        @Override
        public String toString() {
            return "\t-->" + item;
        }
    }
	/**
	* 记录链表的元素
	*/
    transient Node<Integer>[] table;

    public void getOpenAddress(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            int hash = arr[i] % 13;
            if ( table[hash] == null) { // 首先判断当前下标的元素是否为空
                table[hash] = new Node<>();

            }
            Node<Integer> currNode = table[hash];
            // 找到当前链表的尾指针
            while (currNode.next != null) {
                currNode = currNode.next;
            }
            // 创建新的结点，然后插入
            Node<Integer> newNode = new Node<>(arr[i]);
            currNode.next = newNode;
        }
    }
    public void printOpenAddress() {

        for (int i = 0; i < table.length; i++) {
            System.out.print("|   |");
            Node<Integer> currNode = table[i];
            // 输出结点，如果当前结点不为空，那么就直接获取下一个结点。
            if (currNode != null) {
                currNode = currNode.next;
                while (currNode != null) {
                    System.out.print(" | " + currNode + " | ");
                    currNode = currNode.next;
                }
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        int[] arr = {19, 14, 23, 01, 68, 20, 84, 27, 55, 11, 10, 79};
        OpenAddressingTest openAddress = new OpenAddressingTest();
        openAddress.table = new Node[13];
        openAddress.getOpenAddress(arr);
        openAddress.printOpenAddress();
    }

}
```



