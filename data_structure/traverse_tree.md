# 二叉树

### 结点

```java
private static class Node<E> {
     E nodeValue;
     Node<E> lChild;
     Node<E> rChild;
     Node(Node<E> lChild, E nodeValue, Node<E> rChild) {
          this.nodeValue = nodeValue;
          this.lChild = lChild;
          this.rChild = rChild;
     }
     Node(E nodeValue) {
          this.nodeValue = nodeValue;
     }
     @Override
     public String toString() {
          return "Node{" +
               "nodeValue=" + nodeValue +
               '}';
     }
}
```

### 创建二叉排序树

>外层循环是每个结点都要加入二叉树。内层循环则是通过比较结点， 插入到合适的位置。但是这里并没有去重， 自己可以设置相等的插入到左子树，或右子树

```java
public static Node<Integer> buildTree(int[] nums) {
     if (nums.length == 0) {
          return null;
     }
     boolean flag = true;
     Node<Integer> rootNode = new Node<>(nums[0]);
     for (int i = 1; i < nums.length; i++) { // 遍历数组
          // 每次都要从根结点开始遍历。beforeNode 是当前结点，因为currNode = currNode.rChild，是需要记		 // 录当前结点的。
          Node<Integer> currNode = rootNode, beforeNode = null;
          while (currNode != null) {	// 遍历 当前树。
               beforeNode = currNode;
               if (currNode.nodeValue < nums[i]) {
                    currNode = currNode.rChild;
               } else  {
                    currNode = currNode.lChild;
               } 
          }
          Node<Integer> newNode = new Node<>(nums[i]);
          if (beforeNode.nodeValue < nums[i]) {	// 这儿就是与当前路径的最后一个叶子结点作比较
               beforeNode.rChild = newNode;
          } else {
               beforeNode.lChild = newNode;
          }
     }
     return rootNode;
}
```

> 而该方法这是实现了去重，通过利用 flag 表示，数组的当前元素在数中存在相同的值，则跳过插入，直接循环下一个元素。

```java
public static Node<Integer> buildNode2(int[] nums) {
     if (nums.length == 0) {
          return null;
     }
     boolean flag = true;
     Node<Integer> rootNode = new Node<>(nums[0]);
     for (int i = 1; i < nums.length; i++) {
          Node<Integer> currNode = rootNode, beforeNode = null;
          while (currNode != null) {
               beforeNode = currNode;
               if (currNode.nodeValue < nums[i]) {
                    currNode = currNode.rChild;
               } else if (currNode.nodeValue > nums[i]) {
                    currNode = currNode.lChild;
               } else {
                    flag = false;
                    break;
               }
          }
          if(flag) {
               Node<Integer> newNode = new Node<>(nums[i]);
               if (beforeNode.nodeValue < nums[i]) {
                    beforeNode.rChild = newNode;
               } else {
                    beforeNode.lChild = newNode;
               }
          }
          flag = true;
     }
     return rootNode;
}
```

> 这儿是顺序创建二叉树， 并且数组中的元素为基本数据类型对应的封装类型， 所以， 元素可以 为 null, 如果，父元素为null, 则子元素在数组中也为 null
>
> Integer[] nums = {4, 2, null, 3, 6, null, null, 5, null, 7, 9};
>
> [	4]
> [	2]
> [	3	6]
> [	5	7	9]
>
> 思想： 
>
> 1- 如果 数组的长度为 0， 或者是 数组的第一个元素 为 null， 都说明 该 二叉树 为 空
>
> 2-使用队列
>
> 3-创建二叉树， 实际遍历  nums.length / 2个元素即可。
>
> 4-每次循环， 弹出元素， （这里并没有判断 当前结点为 null还是非null）
>
> 5-查看当前元素对应的左孩子的下标 是否越界， 如果不越界， 说明 左孩子存在， 如果左孩子下标对应的元素 为null, （要知道 ， 在创建结点的时候， 左右结点都为 空）， 进入 队列， 如果非空， 则创建 新节点， 成为当前结点的左子树。有孩子同理。

```java
public static Node<Integer> buildTree4(Integer []nums) {
     
     if(nums.length == 0 || nums[0] == null) { // 如果 数组的长度为 0， 或者是 数组的第一个元素 为 null， 都说明 该 二叉树 为 空
          return null;
     }

     LinkedList<Node<Integer>> queue = new LinkedList<>();
     Node<Integer> rootNode = new Node<>(nums[0]);
     queue.addLast(rootNode);
     
     for(int i = 0; i < nums.length / 2; i++) {
          Node<Integer> currNode = queue.pollFirst();
          
          if(i * 2 + 1 < nums.length) {
               Node<Integer> newNode = null;
               if(nums[i * 2 + 1] != null) {
                    newNode = new Node<>(nums[i * 2 + 1]);
                    currNode.lChild = newNode;
               }
               queue.addLast(newNode);
          }else{
               break;
          }
          
          if(i * 2 + 2 < nums.length) {
               Node<Integer> newNode = null;
               if(nums[i * 2 + 2] != null) {
                    newNode = new Node<>(nums[i * 2 + 2]);
                    currNode.rChild = newNode;
               }
               queue.addLast(newNode);
          }else{
               break;
          }
     }
     
     return rootNode;
     
}
```

### 二叉树的后序遍历

#### 1-两个栈

#### 2-一个栈

#### 3-既不用递归，也不用栈

```java
class GFG 
{ 
     static class Node  // 比以前结点多了一个 visited 成员变量， 表示当前变量是否被访问过。
     { 
          int data; 
          Node left, right; 
          boolean visited; 
     } 

     static void postorder( Node head) 
     { 
          Node temp = head; 
          while (temp != null &&  
                 temp.visited == false) 
          { 

               // Visited left subtree 		
               // 访问左子树
               /*
               	左子树 非空， 并且没有被访问过
               */
               if (temp.left != null &&  
                   temp.left.visited == false) 
                    temp = temp.left; 

               // Visited right subtree 
                // 访问右子树
               /*
               	右子树 非空， 并且没有被访问过
               */
               else if (temp.right != null &&  
                        temp.right.visited == false) 
                    temp = temp.right; 

               // Print node 
               else // 输出当前结点
               { 
                    System.out.printf("%d ", temp.data); 
                    temp.visited = true; 
                    temp = head; 	// 再次从头开始。
               } 
          } 
     } 

     static Node newNode(int data) 
     { 
          Node node = new Node(); 
          node.data = data; 
          node.left = null; 
          node.right = null; 
          node.visited = false; 
          return (node); 
     } 

     /* Driver code*/
     public static void main(String []args) 
     { 
          Node root = newNode(8); 
          root.left = newNode(3); 
          root.right = newNode(10); 
          root.left.left = newNode(1); 
          root.left.right = newNode(6); 
          root.left.right.left = newNode(4); 
          root.left.right.right = newNode(7); 
          root.right.right = newNode(14); 
          root.right.right.left = newNode(13); 
          postorder(root); 
     } 
} 
```

#### 4-利用一个 栈， 和非递归方式，

》  添加成员变量 visited(表示有节点是否被访问)

```java
private static class Node<E> {
     E nodeValue;
     Node<E> lChild;
     Node<E> rChild;
     boolean visited;

     Node(Node<E> lChild, E nodeValue, Node<E> rChild) {
          this.nodeValue = nodeValue;
          this.lChild = lChild;
          this.rChild = rChild;
     }

     Node(E nodeValue) {
          this.nodeValue = nodeValue;
     }

     @Override
     public String toString() {
          return "Node{" +
               "nodeValue=" + nodeValue +
               '}';
     }
}
public static void postTraversal4(Node<Integer> rootNode) {

     if(rootNode == null) {
          return;
     }
     Stack<Node<Integer>> stack = new Stack<>();
     Node<Integer> currNode = rootNode;
     while(currNode != null || !stack.isEmpty()) {
          if(currNode != null) {
               stack.push(currNode);
               currNode = currNode.lChild;
          }else {
               Node<Integer> newNode = stack.pop();
               currNode = newNode.rChild;
               if(currNode != null && !currNode.visited) {
                    stack.push(newNode);
               }else{
                    System.out.print(newNode.nodeValue + "\t") ;
                    newNode.visited = true;
                    currNode = null;
               }
          }
     }
     System.out.println();
}
```

### 获取二叉树的最大宽度（非递归）

> 并不是完全二叉树或者满二叉树， 因为创建的 为二叉排序树
>
> 利用队列， 每次把 同一深度的结点加入队列，这样就可以利用 queue.Size() 获取宽度，比较有意思的是，内层的循环为 同一深度的结点出队列， 然后把该结点的子结点插入队列。

```java
public static int getMaxWidth(Node<Integer> rootNode){
     int width = 0;
     LinkedList<Node<Integer>> queue = new LinkedList<>();
     if(rootNode == null) {
          return 0;
     }
     queue.addLast(rootNode);
     while(!queue.isEmpty()) {
          int queueSize = queue.size();
          width = Math.max(width, queueSize);
          for(int i = 0; i < queueSize; i++) {
               Node<Integer> currNode = queue.pollFirst();
               if(currNode.lChild != null) {
                    queue.addLast(currNode.lChild);
               }
               if(currNode.rChild != null) {
                    queue.addLast(currNode.rChild);
               }
          }
     }
     return width;
}
```

### 二叉树的最大深度

#### 1-递归

```java
public int maxDepth(TreeNode root) {
        return root == null ? 0 : Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }	
```

#### 2-非递归

```java
public int maxDepth(TreeNode root) {
     /*if(root == null) {
            return 0;
        }
        LinkedList<TreeNode> queue = new LinkedList();
        int deepLevel = 0;
        queue.addLast(root);
        while(!queue.isEmpty()) {
            deepLevel ++;
            int queueSize = queue.size();
            for(int i =0; i < queueSize; i++) {
                TreeNode currNode = queue.pollFirst();   
                if(currNode.left != null) {
                    queue.addLast(currNode.left);
                }
                if(currNode.right != null) {
                    queue.addLast(currNode.right);
                }
            }
        }
        return deepLevel;*/
}	      
```

### 二叉树的最小深度

#### 1-递归

> 求最小深度时将Math.max换成Math.min即可，但要注意如果根节点的左或右子树为空的话是构不成子树的。而最小深度是要求从根节点到子树的。当左或右子树为空时，不符合要求。

```java
public int minDepth(TreeNode root) {
     if (root == null) {
          return 0;
     }
     // null节点不参与比较
     if (root.left == null && root.right != null) {
          return 1 + minDepth(root.right);
     }
     // null节点不参与比较
     if (root.right == null && root.left != null) {
          return 1 + minDepth(root.left);
     }

     return 1 + Math.min(minDepth(root.left), minDepth(root.right));
}
```

#### 2-非递归

> 和求最大深度类似， 不过要知道最小深度， 也就是第一个叶子结点出现的深度。（root.left == root.right && root.left == null） 

```java
public int minDepth(TreeNode root) {
     if(root == null) {
          return 0;
     }
     LinkedList<TreeNode> queue = new LinkedList<>();
     int minDepthLevel = 0;
     queue.addLast(root);
     while(!queue.isEmpty()) {
          minDepthLevel ++;
          int queueSize = queue.size();
          for(int i = 0; i < queueSize; i++) {
               TreeNode currNode = queue.pollFirst();
               if(currNode.left == null && currNode.right == null) {
                    return minDepthLevel;
               }
               if(currNode.left != null) {
                    queue.addLast(currNode.left);
               }
               if(currNode.right != null) {
                    queue.addLast(currNode.right);
               }
          }
     }     
     return minDepthLevel; 
}
```

### 获取二叉树的所有路径（递归）

> 如果， （当然也可以单纯的只是打印路径，就可以把参数中的ArrayList 集合去掉就可以了， 直接利用print函数打印）
>
> 1.  root = null // 路径结束
> 2.  左右字符为空， 路径结束
> 3. 获取当前结点的左右子树的路径
>
> 这里比较有意思的是， 利用 长度最大的容量为 1000 的数组，而不用ArrayList等集合，是因为这样可以控制，第三层的结点只会在 pathLen = 3 添加元素， 如果

![all_path_recurrent_1](E:\Node\github_project\SummaryMarkDown\data_structure\data_structure_imgs\all_path_recurrent_1.jpg)

```java
public static void getAllPath(Node<Integer> root, int []nums, int pathLen, ArrayList<ArrayList<Integer>> list) {

     if(root == null) {
          return;
     }
     nums[pathLen ++] = root.nodeValue;
     if(root.lChild == null && root.rChild == null) {
          printInfo(nums, pathLen, list);
          return;
     }
     getAllPath(root.lChild, nums, pathLen, list);
     getAllPath(root.rChild, nums, pathLen, list);
}

// 把路径加入 arraylist
public static void printInfo(int nums[], int pathLen,ArrayList<ArrayList<Integer>> list) {
     ArrayList<Integer> arrayList = new ArrayList<>();
     for(int i = 0; i < pathLen; i++) {
          arrayList.add(nums[i]);
     }
     list.add(arrayList);
}

public static void main(String[] args) {
     int[] nums2 = {5, 4, 2, 1, 3, 9, 7, 6, 11, 10, 12, 13, 15, 14,16};
     ArrayList<ArrayList<Integer>> list = new ArrayList<>();	// 利用 arraylist 存储不同的路径
     Node<Integer> rootNode = buildNode2(nums2);	// 构建二叉树
     int nodeValue [] = new int[100];
     getAllPath(rootNode, nums2, 0, list);	
     for(int i = 0; i < list.size(); i++) { // 打印所有路径
          System.out.println(list.get(i));
     }

}
```

### 左叶子之和

> 计算给定二叉树的所有左叶子之和。
>
> 示例：
>
> ​	3
>
>    / \
>   9  20
>     /  \
>    15   7
>
> [3,9,20,null,null,15,7]
>
> 在这个二叉树中，有两个左叶子，分别是 9 和 15，所以返回 24
>
> [1,2,3,4,5]
>
> 4

思想就是， 首先必须是左子树， root.left != null , 表示判断左子树， 利用 root.left.left == null 和 root.left.right == null

表示该结点为 左叶子结点。然后再递归求出 左子树中 左叶子 和 右子树中的左叶子。

```java
public int sumOfLeftLeaves(TreeNode root) {
     if(root == null) {
          return 0;
     }
     return (root.left != null && root.left.left == null && root.left.right == null ? root.left.val : 0)  + sumOfLeftLeaves(root.left) + sumOfLeftLeaves(root.right);
}
```

### 所有左结点之和

> 这题与原先一题不同的是， 这儿只是要求 每个结点的左孩子的和， 只是不用 比较 是否 该结点是否为 叶子结点。

```java
public int sumOfLeftLeaves(TreeNode root) {
     if(root == null) {
          return 0;
     }
     return (root.left != null ? root.left.val : 0)  + sumOfLeftLeaves(root.left) + sumOfLeftLeaves(root.right);
}
```

### 二叉树层级遍历（广度）

#### 1-

> 利用队列，记录每次循环，队列的长度，作为每一层级的循环次数

```java
public static void levelTraversal(Node<Integer> rootNode) {

     if (rootNode == null) {
          return;
     }
     LinkedList<Node<Integer>> queue = new LinkedList();
     queue.addLast(rootNode);
     while (!queue.isEmpty()) {
          int queueSize = queue.size();
          System.out.print("[");
          for (int i = 0; i < queueSize; i++) {
               Node<Integer> currNode = queue.pollFirst();
               System.out.print("\t" + currNode.nodeValue);
               if (currNode.lChild != null) {
                    queue.addLast(currNode.lChild);
               }
               if (currNode.rChild != null) {
                    queue.addLast(currNode.rChild);
               }
          }
          System.out.println("]");
     }
     System.out.println();
}
```

#### 2- 螺旋输出 层级二叉树

> 利用ArrayList 集合， 正常遍历， 如果是 奇数层， 就倒数输出， 如果偶数层， 正常输出， 但是必须在 输出完之后，移除上一层级的元素
>
> ```java
> for(int i = 0; i < queueSize; i++) {
>      queue.remove(0);
> }
> ```

```java
// 螺旋输出 树
public static void levelTraversal4(Node<Integer> rootNode) {
     if (rootNode == null) {
          return;
     }
     List<Node<Integer>> queue = new ArrayList<>();
     queue.add(rootNode);
     int depthLevel = 1;
     while (!queue.isEmpty()) {
          int queueSize = queue.size();
          System.out.print("[");
          for (int i = 0; i < queueSize; i++) {
               Node<Integer> currNode = queue.get(i);
               if(depthLevel % 2 == 1) {
                    System.out.print("\t" + queue.get(queueSize - i - 1).nodeValue);
               }else{
                    System.out.print("\t" + queue.get(i).nodeValue);
               }
               if (currNode.lChild != null) {
                    queue.add(currNode.lChild);
               }
               if (currNode.rChild != null) {
                    queue.add(currNode.rChild);
               }
          }
          for(int i = 0; i < queueSize; i++) {
               queue.remove(0);
          }
          depthLevel++;
          System.out.println("]");
     }
     System.out.println();
}
```

#### 3-层级遍历

> 同样使用队列， 只是不利用循环， 而是在每一层的最后一个位置添加 null, 在每次弹出元素时候，判断是否为 null, 并且 几何中元素不为空， 继续执行，并添加 Null（为 下一层级添加 null）
>
> ```java
> Node<Integer> currNode = queue.pollFirst();
> if(currNode == null) {
>      if(!queue.isEmpty()) {
>           queue.addLast(null);
>           System.out.println();
>      }
> }
> ```

```java
// 不用队列
public static void levelTraversal5(Node<Integer> rootNode) {
     if (rootNode == null) {
          return;
     }
     LinkedList<Node<Integer>> queue = new LinkedList<>();
     queue.add(rootNode);
     queue.add(null);
     while (!queue.isEmpty()) {
          Node<Integer> currNode = queue.pollFirst();
          if(currNode == null) {
               if(!queue.isEmpty()) {
                    queue.addLast(null);
                    System.out.println();
               }
          }else{
               System.out.print(currNode.nodeValue+"\t");
               if(currNode.lChild != null) {
                    queue.addLast(currNode.lChild);
               }
               if(currNode.rChild != null) {
                    queue.addLast(currNode.rChild);
               }
          }
     }
     System.out.println();
}
```

### 中序遍历为每个结点添加 next

> 首先创建 一个空结点， 作为第一个结点。返回 firstNode.next 作为链表的开始。

```java
// 获取中序遍历每个元素的next 结点的指向（像一个结点）
public static Node<Integer> getNextNode(Node<Integer> rootNode) {

     if(rootNode == null || rootNode.lChild == null && rootNode.rChild == null) {
          return rootNode;
     }
     Node<Integer> beforeNode = new Node<>(-1), firstNode = beforeNode;
     Stack<Node<Integer>> stack = new Stack<>();
     Node<Integer> currNode = rootNode;
     while (currNode != null || !stack.isEmpty()) {
          if (currNode != null) {
               stack.push(currNode);
               currNode = currNode.lChild;
          } else {
               currNode = stack.pop();
               beforeNode.next = currNode;
               beforeNode = currNode;
               currNode = currNode.rChild;
          }
     }
     return firstNode.next;
}

public static void printNode(Node<Integer> firstNode) {
     Node<Integer> currNode = firstNode;
     while(currNode != null) {
          System.out.print(currNode.nodeValue+ " -> ");
          if(currNode.next != null) {
               System.out.print(currNode.next.nodeValue+"\n");
          }else{
               System.out.print("-1\n");
          }
          currNode = currNode.next;
     }
}
```

