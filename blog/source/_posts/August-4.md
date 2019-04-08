title: LeetCode note Aug-4
date: 2018-08-23 14:16:00

categories: Algorithm

---

### 297. [H]Serialize and Deserialize Binary Tree

1. we could use BFS to serialize and deserialize the binary tree. 

   ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   public class Codec {
   
       // Encodes a tree to a single string.
       public String serialize(TreeNode root) {
           if (root == null) 
               return "null";
           StringBuilder sb = new StringBuilder();
           Queue<TreeNode> queue = new LinkedList<>();
           sb.append(root.val);
           queue.offer(root);
           while (!queue.isEmpty()) {
               TreeNode currNode = queue.poll();
               sb.append(",");
               if (currNode.left != null) {
                   queue.offer(currNode.left);
                   sb.append(currNode.left.val);
               } else {
                   sb.append("null");
               }
               sb.append(",");
               if (currNode.right != null) {
                   queue.offer(currNode.right);
                   sb.append(currNode.right.val);
               } else {
                   sb.append("null");
               }
           }
           return sb.toString();
       }
   
       // Decodes your encoded data to tree.
       public TreeNode deserialize(String data) {
           String[] dataArray = data.split(",");
           if (dataArray.length == 1 && dataArray[0] == "null") {
               return null;
           }
           TreeNode root = new TreeNode(Integer.valueOf(dataArray[0]));
           Queue<TreeNode> queue = new LinkedList<>();
           queue.offer(root);
           for (int index = 1; index < dataArray.length; index += 2) {
               TreeNode currRoot = queue.poll();
               if (!"null".equals(dataArray[index])) {
                   int val = Integer.valueOf(dataArray[index]);
                   TreeNode left = new TreeNode(val);
                   currRoot.left = left;
                   queue.offer(left);
               }
               if (index+1 < dataArray.length && !"null".equals(dataArray[index+1])) {
                   int val = Integer.valueOf(dataArray[index+1]);
                   TreeNode right = new TreeNode(val);
                   currRoot.right = right;
                   queue.offer(right);
               }            
           }
           return root;
       }
   }
   ```

2. Dfs pre-order tree traversal

3. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   public class Codec {
   
       // Encodes a tree to a single string.
       public String serialize(TreeNode root) {
           StringBuilder sb = new StringBuilder();
           traceTree(root, sb);
           return sb.toString();
       }
   
       private void traceTree(TreeNode root, StringBuilder sb) {
           if (root == null) {
               sb.append("null,");
               return;
           }
           sb.append(root.val);
           sb.append(",");
           traceTree(root.left, sb);
           traceTree(root.right, sb);
       }
       // Decodes your encoded data to tree.
       public TreeNode deserialize(String data) {
           String[] dataArray = data.split(",");
           Queue<String> queue = new LinkedList<>(Arrays.asList(dataArray));
           return deTraceTree(queue);
       }
       private TreeNode deTraceTree(Queue<String> queue) {
           String val = queue.poll();
           if ("null".equals(val)) {
               return null;
           }
           TreeNode curr = new TreeNode(Integer.valueOf(val));
           curr.left = deTraceTree(queue);
           curr.right = deTraceTree(queue);
           return curr;
       }
   }
   
   ```

3. Iterative dfs pre-order tree traversal

4. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   public class Codec {
   
       // Encodes a tree to a single string.
       public String serialize(TreeNode root) {
           if (root == null) {
               return "null,";
           }
           StringBuilder sb = new StringBuilder();
           Stack<TreeNode> stack = new Stack<>();
           TreeNode curr = root;
           while (curr != null || !stack.empty()) {
               if (curr != null) {
                   sb.append(curr.val);
                   sb.append(",");
                   stack.push(curr);
                   curr = curr.left;
               } else {
                   sb.append("null,");
                   curr = stack.pop();
                   curr = curr.right;
               }
           }
           return sb.toString();
       }
   
       // Decodes your encoded data to tree.
       public TreeNode deserialize(String data) {
           String[] dataArray = data.split(",");
           if (dataArray.length == 1 && "null".equals(dataArray[0])) {
               return null;
           }
           Stack<TreeNode> stack = new Stack<>();
           TreeNode root = new TreeNode(Integer.valueOf(dataArray[0]));
           stack.push(root);
           TreeNode curr = root;
           int len = dataArray.length;
           int index = 1;
           while (index < len) {
               while (index < len && !"null".equals(dataArray[index])) {
                   String val = dataArray[index];
                   curr.left  = new TreeNode(Integer.valueOf(val));
                   curr = curr.left;
                   stack.push(curr);
                   index++;
               }
               while (index < len && "null".equals(dataArray[index])) {
                   curr = stack.pop();
                   index++;
               }
               if (index < len) {
                   String val = dataArray[index];
                   curr.right = new TreeNode(Integer.valueOf(val));
                   curr = curr.right;
                   stack.push(curr);
                   index++;
               }
           }
           return root;
       }
     
   }
   ```


### 331. [M]Verify Preorder Serialization of a Binary Tree

1. we could use recursive to traverse a tree. In the same way we could get the value in traversing process. poll() method would return null when queue is empty. It could not be null in this process and when the traversing process finished, the queue should be empty.

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] data = preorder.split(",");
        Queue<String> queue = new LinkedList<>(Arrays.asList(data));
        return dfs(queue) && queue.isEmpty();
    }
    private boolean dfs(Queue<String> queue) {
        String val = queue.poll();
        if (val == null) {
            return false;
        }
        if ("#".equals(val)) {
            return true;
        } 
        return dfs(queue) && dfs(queue);
    }
}
```



2. we could think about this problem in a different way. As we take null node as the leaf, all null-node has 0 out-degree and 1 in-degree. All non-null nodes has  2 out-degree and 1 in-degree except root. So the whole out-degree - in-degree = 1. So we could first let the diff = 1 and keep track of the difference between out-degree - in-degree. The difference could never be negative in the process and when the process done, the difference should be 0.

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] data = preorder.split(",");
        int diff = 1;
        for (String val: data) {
            diff -= 1;
            if (diff < 0) {
                return false;
            }
            if (!"#".equals(val)) {
                diff += 2;
            }
        }
        return diff == 0;
    }
}
```

### 450. [M]Delete Node in a BST

1. Thinking about the root could be deleted, so I create a dummy node to represent the root and dummy node's left node will be the real node. So I could find and delete the node in the tree in the same way no matter it's root or not. Find the need-to-be-deleted node's father, find need-to-be-deleted node's right node's leftmost node. Connect the leftmost node with the left node.  

2. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   class Solution {
       public TreeNode deleteNode(TreeNode root, int key) {
           if (root == null) {
               return root;
           }
           TreeNode dummyNode = new TreeNode(-1);
           dummyNode.left = root;
           TreeNode delParNode = findNodeParent(dummyNode, key);
           if (delParNode == null) {
               return root;
           }
           if (delParNode.left != null && delParNode.left.val == key) {
               TreeNode delNode = delParNode.left;
               if (delNode.left != null && delNode.right != null) {
                   TreeNode leftmostNode = findleftmostNode(delNode.right);
                   leftmostNode.left = delNode.left;
                   delParNode.left = delNode.right;
                   delNode.left = null;
                   delNode.right = null;
               } else if (delNode.left == null) {
                   delParNode.left = delNode.right;
               } else {
                   delParNode.left = delNode.left;
               }
           } else if (delParNode.right != null && delParNode.right.val == key) {
               TreeNode delNode = delParNode.right;
               if (delNode.left != null && delNode.right != null) {
                   TreeNode leftmostNode = findleftmostNode(delNode.right);
                   leftmostNode.left = delNode.left;
                   delParNode.right = delNode.right;
                   delNode.left = null;
                   delNode.right = null;
               } else if (delNode.left == null) {
                   delParNode.right = delNode.right;
               } else {
                   delParNode.right = delNode.left;
               }
           }
           return dummyNode.left;
       }
       private TreeNode findNodeParent(TreeNode root, int key) {
           if (root == null) {
               return null;
           }
           if ((root.left != null && root.left.val == key) || (root.right != null && root.right.val == key)) {
               return root;
           }
           TreeNode left = findNodeParent(root.left, key);
           return left == null ? findNodeParent(root.right, key) : left;
       }
       private TreeNode findleftmostNode(TreeNode root) {
           if (root == null) {
               return null;
           }
           TreeNode curr = root;
           while (curr.left != null) {
               curr = curr.left;
           }
           return curr;
       }
   }
   ```



2. Using the feature of BST, we could recursively delete node. It's much more efficient and concise. The time complexity is only O(h)

3. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   class Solution {
       public TreeNode deleteNode(TreeNode root, int key) {
           if (root == null) {
               return root;
           }
           if (key < root.val) {
               root.left = deleteNode(root.left, key);
           } else if (key > root.val) {
               root.right = deleteNode(root.right, key);
           } else {
               if (root.left == null) {
                   return root.right;
               } else if (root.right == null) {
                   return root.left;
               } 
               TreeNode leftmost = findLeftmostNode(root.right);
               leftmost.left = root.left;
               return root.right;
           }
           return root;
       }
       private TreeNode findLeftmostNode(TreeNode root) {
           TreeNode curr = root;
           while (curr.left != null) {
               curr = curr.left;
           }
           return curr;
       }
   }
   ```


### 889. Construct Binary Tree from Preorder and Postorder Traversal

1. we can split the preorder array and postorder array into three parts and do it by recursion.  Think about it. The preorder array could be split like this: \[root]\[left part] \[right part]. The postorder array could be split like this: \[left part]\[right part]\[root]. Because the index of root is always so apparen, what we need to do is to find the index that split the right part and left part, Then split it. 

2. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   class Solution {
       public TreeNode constructFromPrePost(int[] pre, int[] post) {
           int len = pre.length;
           if (len == 0) {
               return null;
           }
           TreeNode root = new TreeNode(pre[0]);
           if (len == 1) {
               return root;
           }
           int left = 0;
           for(int index = 0; index < post.length; index++) {
               if (pre[1] == post[index]) {
                   left = index + 1;
                   break;
               }
           }
           root.left = constructFromPrePost(Arrays.copyOfRange(pre, 1, left+1), Arrays.copyOfRange(post, 0, left));
           root.right = constructFromPrePost(Arrays.copyOfRange(pre, left+1, len), Arrays.copyOfRange(post, left, len-1));
           return root;
       }
   }
   ```

   The time complexity would be O(N^2), space complexity would be O(N^2).

2. we could use index to mark the preorder sub-array and postorder sub-array so that we could save the space. 

3. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   class Solution {
       int[] pre, post;
       public TreeNode constructFromPrePost(int[] pre, int[] post) {
           this.pre = pre;
           this.post = post;
           return construct(0, 0, pre.length);
       }
       private TreeNode construct(int preHead, int postHead, int len) {
           if (len == 0) {
               return null;
           }
           TreeNode root = new TreeNode(pre[preHead]);
           if (len == 1) {
               return root;
           }
           int newLen = 0;
           for(int index = 0; index < len; index++) {
               if (pre[preHead+1] == post[postHead+index]) {
                   newLen = index + 1;
                   break;
               }
           }
           root.left = construct(preHead+1, postHead, newLen);
           root.right = construct(preHead+newLen+1, postHead+newLen, len-newLen-1);
           return root;
       }
   }
   ```

   In this way only O(n) space complexity. 


### 863. All Nodes Distance K in Binary Tree

1. First using dfs to find the path from root to the target node. Then for each node in the path, find the nodes in relevant distance by using bfs. There are many corner case need to watch out. When find the relevant distance nodes, we should cut the branch off which has already been searched for.

2. ```java
   /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
   class Solution {
       public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
           List<Integer> result = new ArrayList<>();
           if (root == null || target == null || K < 0) {
               return result;
           }
           Stack<TreeNode> stack = new Stack<>();
           stack.push(root);
           boolean isFind = dfs(stack, target);
           if (!isFind) {
               return result;
           }
           TreeNode curr = null, last = null;
           for (int distance = K; distance >=0; distance--) {
               if (stack.empty()) {
                   break;
               }
               curr = stack.pop();
               getNodeDistanceK(curr, last, distance, result);
               last = curr;
           }
           return result;
       }
       private boolean dfs(Stack<TreeNode> stack, TreeNode target) {
           if (stack.empty()) {
               return false;
           }
           TreeNode curr = stack.peek();
           if (curr == target) {
               return true;
           }
           if (curr.left == null && curr.right == null) {
               return false;
           } 
           boolean isFind = false;
           if (curr.left != null) {
               stack.push(curr.left);
               isFind |= dfs(stack, target);
               if (isFind) {
                   return isFind;
               } else {
                   stack.pop();
               }
           } 
           if (curr.right != null){
               stack.push(curr.right);
               isFind |= dfs(stack, target);
               if (isFind) {
                   return isFind;
               } else {
                   stack.pop();
               }
           }
           
           return false;
       }
       private void getNodeDistanceK(TreeNode root, TreeNode target, int K, List<Integer> result) {
           if (K < 0) {
               return;
           }
           int level = 0;
           Queue<TreeNode> queue = new LinkedList<>();
           queue.offer(root);
           while (!queue.isEmpty()) {
               if (level == K) {
                   break;
               }
               int size = queue.size();
               for (int it = 0; it < size; it++) {
                   TreeNode temp = queue.poll();
                   if (temp.left != null && temp.left != target) {
                       queue.offer(temp.left);
                   }
                   if (temp.right != null && temp.right != target) {
                       queue.offer(temp.right);
                   }
               }
               level++;
           }
           for (TreeNode node: queue) {
               result.add(node.val);
           }
       }
   }
   ```


### 215. Kth Largest Element in an Array

1. we could use partition which is used in quick sort to solve it. what we are doing is to find the k-th number, each time we use the first num as a pivot and find the index of this number as j, if it match k-th, stop sorting and return it. If it small than k, move low side to j + 1. Else move high side to j-1.

2. ```java
   class Solution {
       public int findKthLargest(int[] nums, int k) {
           int kth = nums.length - k;
           int low = 0, high = nums.length - 1;
           while(low < high) {
               int j = partition(nums, low, high);
               if (j == kth) {
                   break;
               } else if (j < kth) {
                   low = j + 1;
               } else {
                   high = j - 1;
               }
           }
           return nums[kth];
       }
       private int partition(int[] nums, int low, int high) {
           int li = low;
           int hi = high + 1;
           while(li <= hi) {
               while(hi > li && nums[low] < nums[--hi]);
               while(li < hi && nums[low] > nums[++li]);
               if (hi == li) {
                   break;
               }
               exchange(nums, li, hi);
           }
           exchange(nums, low, li);
           return li;
       }
       private void exchange(int[] nums, int index1, int index2) {
           int temp = nums[index1];
           nums[index1] = nums[index2];
           nums[index2] = temp;
       }
   }
   ```

   this method would run in O(N) best case / O(N^2) worst case running time + O(1) memory.

2. we need to improve the method to get O(n) guaranteed time complexity. Just Randomize the input so that it won't infect even if the worst case happen. 

   ```java
       private void shuffle(int a[]) {
   
           final Random random = new Random();
           for(int ind = 1; ind < a.length; ind++) {
               final int r = random.nextInt(ind + 1);
               exchange(a, ind, r);
           }
       }
   ```


### 324. Wiggle Sort II

1. In this problem, I need to use the function in 215 findKthLargest() to get the median number first in O(n) time and O(1) space. Then Because the nuns would be seperate two part by findKthLargest(), the first part is small than median, the last part is large than median.  For the regulation, we use S represent small, M represent equal, L represent large. So the array would be something like this: S, S, S, M, M, L, L, L. What we want is to change this arrage into this style: M, L, S, L, S, L, S, L, M. We could use the equation to map index. Real(i) = (i * 2 + 1) % (Len | 1). This equation could make the first part map to first odd place, and map the last part map to last even. So the median would be map into last odd part and first even part to be separated.  **(n | 1) calculates the nearest odd that is not less than n.**

2. ```java
   class Solution {
       public void wiggleSort(int[] nums) {
           int midValue = findKthLargest(nums, (nums.length+1)/2);
           System.out.println(midValue);
           int low = 0, bt = 0, high = nums.length - 1;
           int len = nums.length;
           while (bt <= high) {
               int realBt = getRealIndex(bt, len);
               int realLow = getRealIndex(low, len);
               int realHigh = getRealIndex(high, len);
               if (nums[realBt] > midValue){
                   exchange(nums, realBt, realLow);
                   bt++;
                   low++;
               } else if (nums[realBt] < midValue) {
                   exchange(nums, realBt, realHigh);
                   high--;
               } else {
                   bt++;
               }
           }
       }
       private int getRealIndex(int vi, int len) {
           return (1 + vi * 2) % (len | 1);
       }
   }
   ```

2. Or we could not use the virtual index, just follow the rules to swap the array. We need to put the value which is smaller than median into the last even slot, put the value which is larger than median into the first odd slot. 

   ```java
    public void wiggleSort(int[] nums) {
           int midValue = findKthLargest(nums, (nums.length+1)/2);
           System.out.println(midValue);
           int left = 1, right = (nums.length - 1) / 2 * 2;
           int mid = right;
           for (int i = 0; i < nums.length; i++) {
               if (nums[mid] > midValue){
                   exchange(nums, mid, left);
                   left += 2;
               } else if (nums[mid] < midValue) {
                   exchange(nums, mid, right);
                   right -= 2;
                   mid -= 2;
                   if (mid < 0) mid = nums.length  / 2 * 2 - 1;
               } else {
                   mid -= 2;
                   if (mid < 0) mid = nums.length / 2 * 2 - 1;
               }
           }
       }
   ```

### 109. Convert Sorted List to Binary Search Tree

1. we could use fast-slow pointer to find the root of each node, then recursively solve it.

2. ```java
   public TreeNode sortedListToBST(ListNode head) {
          
          return toBST(head, null);
      }
      private TreeNode toBST(ListNode head, ListNode tail) {
          ListNode slow = head;
          ListNode fast = head;
          if (head == tail) {
              return null;
          }
          while (fast != tail && fast.next != tail) {
              fast = fast.next.next;
              slow = slow.next;
          }
          TreeNode thead = new TreeNode(slow.val);
          thead.left = toBST(head, slow);
          thead.right = toBST(slow.next, tail);
          return thead;
      }
   ```
   ```
   
   ```

### 417. Pacific Atlantic Water Flow

1. we could use dfs to find the answer. As there are two destination we need to reach, we could use two boolean array to mark each way, every time reach one place, change it to visited. To initial it, there are the edges which are already satisfy the request and each new node which could get to these node are still satisfy the request.

2. ```java
   class Solution {
       public List<int[]> pacificAtlantic(int[][] matrix) {
           List<int[]> answer = new ArrayList<>();
           if(matrix == null || matrix.length == 0 || matrix[0].length == 0) {
               return answer;
           }
           int m = matrix.length;
           int n = matrix[0].length;
           boolean[][] pacific = new boolean[m][n];
           boolean[][] atlantic = new boolean[m][n];
           for (int i = 0; i < m; i++) {
               dfs(matrix, pacific, Integer.MIN_VALUE, i, 0);
               dfs(matrix, atlantic, Integer.MIN_VALUE, i, n-1);
           } 
           for (int i = 0; i < n; i++) {
               dfs(matrix, pacific, Integer.MIN_VALUE, 0, i);
               dfs(matrix, atlantic, Integer.MIN_VALUE, m-1, i);
           }
           
           for (int row = 0; row < m; row++) {
               for (int column = 0; column < n; column++) {
                   if (pacific[row][column] && atlantic[row][column]) {
                       int[] one = {row, column};
                       answer.add(one);
                   }
               }
           }
           return answer;
       }
       private void dfs(int[][] matrix, boolean[][] visited, int height, int row, int column) {
           if (row < 0 || row >= matrix.length || column < 0 || column >= matrix[0].length || visited[row][column] || matrix[row][column] < height) {
               return;
           }
           visited[row][column] = true;
           int[][] direction = {{1,0}, {-1, 0}, {0, 1}, {0, -1}};
           for (int index = 0; index < direction.length; index++) {
               dfs(matrix, visited, matrix[row][column], row+direction[index][0], column+direction[index][1]);
           }
       }
   }
   ```

3. we also could use bfs to achieve this goal. Just use queue to replace the recursion.

4. ```java
   class Solution {
       public List<int[]> pacificAtlantic(int[][] matrix) {
           List<int[]> answer = new ArrayList<>();
           if(matrix == null || matrix.length == 0 || matrix[0].length == 0) {
               return answer;
           }
           int m = matrix.length;
           int n = matrix[0].length;
           boolean[][] pacific = new boolean[m][n];
           boolean[][] atlantic = new boolean[m][n];
           Queue<int[]> pQueue = new LinkedList<>();
           Queue<int[]> aQueue = new LinkedList<>();
           for (int i = 0; i < m; i++) {
               int[] pStart = {i, 0};
               pQueue.offer(pStart);
               int[] aStart = {i, n-1};
               aQueue.offer(aStart);
           } 
           for (int i = 0; i < n; i++) {
               int[] pStart = {0, i};
               pQueue.offer(pStart);
               int[] aStart = {m-1, i};
               aQueue.offer(aStart);
           }
           bfs(matrix, pacific, pQueue);
           bfs(matrix, atlantic, aQueue);
           for (int row = 0; row < m; row++) {
               for (int column = 0; column < n; column++) {
                   if (pacific[row][column] && atlantic[row][column]) {
                       int[] one = {row, column};
                       answer.add(one);
                   }
               }
           }
           return answer;
       }
       private void bfs(int[][] matrix, boolean[][] visited, Queue<int[]> queue) {
           int[][] direction = {{1,0}, {-1, 0}, {0, 1}, {0, -1}};
           while (!queue.isEmpty()) {
               int[] curr = queue.poll();
               int row = curr[0];
               int column = curr[1];
               visited[row][column] = true;
               for (int[] dir: direction) {
                   int tRow = row + dir[0];
                   int tColumn = column + dir[1];
                   if (tRow < 0 || tRow >= matrix.length || tColumn < 0 || tColumn >= matrix[0].length || visited[tRow][tColumn] || matrix[row][column] > matrix[tRow][tColumn]) {
                       continue;
                   }
                   int[] temp = {tRow, tColumn};
                   queue.offer(temp);
                   
               }
           }
       }
   }
   ```


### 32. Longest Valid Parentheses

1. I use two stack to record the valid Parentheses. The first stack is to valid, the second one just record the index of each Parentheses and do the same push, pop as the first one. So when the second stack has elements left, which is exactly the invalid Parentheses. Finally i could pop this stack then calculate the length between each invalid Parentheses which shoud be the length of valid Parentheses. Time complexity O(n), space complexity O(n).

2. ```java
   class Solution {
       public int longestValidParentheses(String s) {
           char[] chars = s.toCharArray();
           Stack<Character> stack = new Stack<>();
           Stack<Integer> indexStack = new Stack<>();
           for (int i = 0; i < chars.length; i++) {
               while (i < chars.length && chars[i] == ')') {
                   if (!stack.empty() && stack.peek() == '(') {
                       stack.pop();
                       indexStack.pop();
                   } else {
                       stack.push(chars[i]);
                       indexStack.push(i);
                   }
                   i++;
               } 
               if (i >= chars.length){
                   break;
               }
               stack.push(chars[i]);   
               indexStack.push(i);
           }
           int max = 0;
           int last = chars.length;
           while (!indexStack.empty()) {
               int curr = indexStack.pop();
               max = Math.max(max, last-curr-1);
               last = curr;
           }
           return max > last ? max : last;
       }
   }
   ```

2. Using the same method as before, just try to save the space complexity, we could only use one stack to record. First put -1 into the stack so that if all index has been pop out, the length will be the last index -1, still right. Time complexity O(n), space complexity O(n)

3. ```java
   class Solution {
       public int longestValidParentheses(String s) {
           char[] chars = s.toCharArray();
           Stack<Integer> stack = new Stack<>();
           stack.push(-1);
           int max = 0;
           for (int i = 0; i < chars.length; i++) {
               if (chars[i] == '(') {
                   stack.push(i);
               } else {
                   stack.pop();
                   if (stack.empty()) {
                       stack.push(i);
                   } else {
                       max = Math.max(max, i-stack.peek());
                   }
               }
           }
          
           return max;
       }
   }
   ```

3. We could use dynamic programming method to solve this problem. Define the dp[i] represent the longest valid parentheses which end up with the index i. To avoid some corner case, we just define the dp array as dp[s.length+1] so that dp[i] represent which end up as index i-1. And the state transition equation would have two situation: if like this: (), the equation would be **dp[i+1] = dp[i-1] + 2**, if like (()), the equation would be like:    dp[i+1] = dp[i] + dp[i-dp[i]-1] + 2.  Time complexity O(n), space complexity O(n)

4. ```java
   class Solution {
       public int longestValidParentheses(String s) {
           char[] chars = s.toCharArray();
           int[] dp = new int[chars.length+1];
           int max = 0;
           for (int i = 1; i < chars.length; i++) {
               if (chars[i] == ')') {
                   if (chars[i-1] == '(') {
                       dp[i+1] = dp[i-1] + 2;
                       max = Math.max(dp[i+1], max);
                   } else if (dp[i] < i && chars[i-dp[i]-1] == '('){
                       dp[i+1] = dp[i] + dp[i-dp[i]-1] + 2;
                       max = Math.max(dp[i+1], max);
                   }
               }
           }
           
           return max;
       }
   }
   ```

### bitCount: count the one's number in a integer in O(1)

1. This is the jdk source code to implement it. The idea is based on a regular pattern。 



| Binary code for number | number | bit number | binary bit number |
| ---------------------- | ------ | ---------- | ----------------- |
| 00                     | 0      | 0          | 00 - 00           |
| 01                     | 1      | 1          | 01 - 00           |
| 10                     | 2      | 1          | 10 - 01           |
| 11                     | 3      | 2          | 11 - 01           |

   So for every two binary number n, its one's bit number is equal to **n - (n >>1)** 

   Then we just combine the every two number into every four number, then increase to 8, 16, Then clear the high address one's bit and got the answer.

```java
    public static int bitCount(int i) {
        // HD, Figure 5-2
        i = i - ((i >>> 1) & 0x55555555);
        i = (i & 0x33333333) + ((i >>> 2) & 0x33333333);
        i = (i + (i >>> 4)) & 0x0f0f0f0f;
        i = i + (i >>> 8);
        i = i + (i >>> 16);
        return i & 0x3f;
    }
```



