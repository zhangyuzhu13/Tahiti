title: LeetCode Note Sep-2
date: 2018-9-11 14:16:00
categories: Algorithm

---



### 218. The Skyline Problem

1. According to the scan line principle, we should keep one dimension sorted and traverse in this dimension to check the other dimension.  We consider the rectangle as two point in x dimension. For distinguishing the left and right to consider the influence of height, we change left height to negative. So when two point have same x value, we take the minimum first, also means the left highest first to add, and right lowest to remove which could cut the corner case about the height in the same x points. This method mainly use heap and scan line, runs in O(nlogn) time and O(n) space.

2. ```java
   class Solution {
       public List<int[]> getSkyline(int[][] buildings) {
           List<int[]> answer = new ArrayList<>();
           List<int[]> height = new ArrayList<>();
           for (int[] array: buildings) {
               height.add(new int[] {array[0], -array[2]});
               height.add(new int[] {array[1], array[2]});
           }
           Collections.sort(height, (a, b) -> {
               if (a[0] == b[0]) {
                   return a[1] - b[1];
               }
               return a[0] - b[0];
           });
           int prev = 0;
           PriorityQueue<Integer> queue = new PriorityQueue<>((a, b) -> (b - a));
           queue.offer(0);
           for (int[] he: height) {
               if (he[1] < 0) {
                   queue.offer(-he[1]);
               } else {
                   queue.remove(he[1]);
               }
               int curr = queue.peek();
               if (prev != curr) {
                   answer.add(new int[] {he[0], curr});
                   prev = curr;
               }
           } 
           return answer;
       }
   }
   
   ```

2. We could also use segment tree to solve this problem. Separate the segment tree into three methods: buildTree, add height, search and get the height. For the segment tree, we keep  the minimum height of buildings value in the non-leaf nodes. So every time update the height for the leaf, when we find the new height is smaller than a non-leaf node, all nodes belongs to this node are not able to update to new value and this could save mjuch more time. After all heights update into the segment tree, we just traverse the leaf nodes and when the height change, add a new result into answer list. Caution：don't forget the last point which should be at the 0 height. This method would be run in O(n^2)

3. ```java
   class Solution {
       public List<int[]> getSkyline(int[][] buildings) {
           List<int[]> answer = new ArrayList<>();
           Set<Integer> set = new HashSet<>();
           for (int[] building: buildings) {
               set.add(building[0]);
               set.add(building[1]);
           }
           List<Integer> endpoints = new ArrayList<>(set);
           Collections.sort(endpoints, (a, b) -> (a - b));
           Map<Integer, Integer> map = new HashMap<>();
           for (int i = 0; i < endpoints.size(); i++) {
               map.put(endpoints.get(i), i);
           }
           Node root = buildNode(0, endpoints.size()-1);
           for (int[] building: buildings) {
               add(root, map.get(building[0]), map.get(building[1]), building[2]);
           }
           search(answer, endpoints, root);
           if (endpoints.size() > 0) {
               answer.add(new int[] {endpoints.get(endpoints.size()-1), 0});
           }
           return answer;
       }
       class Node {
           int start, end;
           Node left, right;
           int height;
           public Node(int start, int end) {
               this.start = start;
               this.end = end;
           }
       }
       private Node buildNode(int start, int end) {
           if (start > end) {
               return null;
           }
           Node root = new Node(start, end);
           if (start + 1 < end) {
               int mid = (end - start) / 2 + start;
               root.left = buildNode(start, mid);
               root.right = buildNode(mid, end);            
           }
           return root;
       }
       
       private void add(Node root, int start, int end, int height) {
           if (root == null || start >= root.end || end <= root.start || root.height > height) {
               return;
           }
           if (root.left == null && root.right == null) {
               root.height = Math.max(root.height, height);
           } else {
               add(root.left, start, end, height);
               add(root.right, start, end, height);
               root.height = Math.min(root.left.height, root.right.height);
           }
       }
       private void search(List<int[]> answer, List<Integer> endpoints, Node root) {
           if (root == null) {
               return;
           } else {
               if (root.left == null && root.right == null && (answer.isEmpty() || answer.get(answer.size()-1)[1] != root.height)) {
                   answer.add(new int[]{endpoints.get(root.start), root.height});
               } else {
                   search(answer, endpoints, root.left);
                   search(answer, endpoints, root.right);
               }
           }
       }
   }
   
   ```

4. 

###  307 Range Sum Query - Mutable

1. The first method I think out is sum array. In each index in the sum array, it represent the sum of the numbers before this index. So update in O(n) time and getSum in O(1) time. 

```java
class NumArray {
    private int[] sum;
    private int[] nums;
    public NumArray(int[] nums) {
        this.nums = Arrays.copyOf(nums, nums.length);
        this.sum = new int[nums.length+1];
        for(int i = 0; i < nums.length; i++) {
            sum[i+1] = sum[i] + nums[i];
        }
    }
    
    public void update(int i, int val) {
        int diff = val - this.nums[i];
        this.nums[i] = val;
        for (int index = i + 1; index < this.sum.length; index++) {
            this.sum[index] += diff;
        }
    }
    
    public int sumRange(int i, int j) {
        if (i > j) {
            return -1;
        }
        j = j > this.sum.length - 2 ? this.sum.length - 2 : j;
        i = i < 0 ? 0 : i;
        return this.sum[j+1] - this.sum[i];
    }
}
```



2. we could use segment tree to solve this problem. Extend the tree array with double size of the original nuns length. Calculate the sum. When update, just update the numbers in the path. When search the sum for a range, just to calculate the right part of the left end point and the left part of the right end point.

3. ```java
   class NumArray {
       private int[] tree;
       public NumArray(int[] nums) {
           this.tree = new int[nums.length*2];
           buildTree(nums);
       }
       
       private void buildTree(int[] nums) {
           int len = nums.length;
           for (int i = len; i < len * 2; i++) {
               tree[i] = nums[i-len];
           }
           for (int i = len - 1; i > 0; i--) {
               tree[i] = tree[i*2] + tree[i*2+1];
           }
       }
       public void update(int i, int val) {
           int pos = this.tree.length / 2 + i;
           this.tree[pos] = val;
           while(pos > 1) {
               int left = pos;
               int right = pos;
               if (pos % 2 == 0) {
                   right = pos + 1;
               } else {
                   left = pos - 1;
               }
               tree[pos/2] = tree[left] + tree[right];
               pos /= 2;
           }
       }
       
       public int sumRange(int i, int j) {
           int left = i + this.tree.length / 2;
           int right = j + this.tree.length / 2;
           int sum = 0;
           while (left <= right) {
               if (left % 2 == 1) {
                   sum += tree[left];
                   left++;
               }
               if (right % 2 == 0) {
                   sum += tree[right];
                   right--;
               }
               left /= 2;
               right /= 2;
           }
           return sum;
       }
   }
   
   ```


### 253. Meeting Rooms II

1. this problem could be transformed to find the max num of interval that overlap together. So we could use heap and scan line.

```java
    public static int maxRoom(int[][] times) {
        Arrays.sort(times, (a, b) -> {
           if (a[0] == b[0]){
               return a[1] - b[1];
           }
           return a[0] - b[0];
        });
        PriorityQueue<int[]> heap = new PriorityQueue<>( (a, b) -> (a[1] - b[1]));
        int max = 0;
        for (int[] time: times) {
            while (!heap.isEmpty() && heap.peek()[1] < time[0]) {
                heap.poll();
            }
            heap.offer(time);
            max = Math.max(max, heap.size());
        }
        return max;
    }
```



### 152. Maximum Product Subarray

1. we need to record the max and the min product value because there could be possible that two negative value's product will be the max. So every step we update the max, min and the result. 

2. ```java
   class Solution {
       public int maxProduct(int[] nums) {
           if (nums == null || nums.length == 0) {
               return 0;
           }
           int min = nums[0], max = nums[0], result = nums[0];
           for (int i = 1; i < nums.length; i++) {
               int temp = max;
               max = Math.max(Math.max(min*nums[i], max*nums[i]), nums[i]);
               min = Math.min(Math.min(min*nums[i], temp*nums[i]), nums[i]);
               if (max > result) {
                   result = max;
               } 
           } 
           return result;
       }
   }
   ```


2. The negative number would effect the result depending on odd or even number of negative number. So we can scan from left for one time and fro right for one time to gain the max value in which way the influence will disappear.

3. ```java
   class Solution {
       public int maxProduct(int[] nums) {
           if (nums == null || nums.length == 0) {
               return 0;
           }
           int max = nums[0];
           int product = 1;
           for (int i = 0; i < nums.length; i++) {
               max = Math.max(product*=nums[i], max);
               if (nums[i] == 0) {
                   product = 1;
               }
           } 
           product = 1;
           for (int i = nums.length - 1; i >= 0; i--) {
               max = Math.max(product*=nums[i], max);
               if (nums[i] == 0) {
                   product = 1;
               }
           }
           return max;
       }
   }
   ```



### 54. Spiral Matrix

1. we could define a direction array to move the step and when hit the wall or some place already go through, change the direction in order. 

2. ```java
   class Solution {
       public List<Integer> spiralOrder(int[][] matrix) {
           if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
               return new ArrayList<>();
           }
           int rows = matrix.length;
           int columns = matrix[0].length;
           boolean[][] visited = new boolean[rows][columns];
           int[][] direction = {{0,1}, {1, 0}, {0, -1}, {-1, 0}};
           int dir = 0;
           int row = 0, column = 0;
           List<Integer> answer = new ArrayList<>(rows*columns);
           for (int count = 0; count < rows * columns; count++) {
               answer.add(matrix[row][column]);
               visited[row][column] = true;
               int tempRow = row + direction[dir%4][0];
               int tempColumn = column + direction[dir%4][1];
               if (tempRow < 0 || tempRow >= rows || tempColumn < 0 || tempColumn >= columns || visited[tempRow][tempColumn]) {
                   dir++;
                   tempRow = row + direction[dir%4][0];
                   tempColumn = column + direction[dir%4][1];
               }
               row = tempRow;
               column = tempColumn;
           }
           return answer;
       }
   }
   ```




### 648

1. We could use trie tree to solve this problem. It will run in O(n)

2. ```java
   class Solution {
       public String replaceWords(List<String> dict, String sentence) {
           if (dict == null || dict.size() == 0) {
               return sentence;
           }
           TrieTree root = new TrieTree();
           for (String str: dict) {
               TrieTree curr = root;
               for (char c: str.toCharArray()) {
                   if (curr.children[c-'a'] == null) {
                       curr.children[c-'a'] = new TrieTree();
                   }
                   curr = curr.children[c-'a'];
               }
               curr.val = str;
               curr.isWord = true;
           }
           
           StringBuilder sb = new StringBuilder();
           for (String str: sentence.split("\\s+")) {
               TrieTree curr = root;
               for (char c:  str.toCharArray()) {
                   if (curr.isWord || curr.children[c-'a'] == null) {
                       break;
                   } 
                   curr = curr.children[c-'a'];
               }
               sb.append(curr.isWord ? curr.val : str);
               sb.append(" ");
           }
           return sb.toString().trim();
       }
   }
   
   class TrieTree {
       boolean isWord;
       TrieTree[] children;
       String val;
       
       public TrieTree() {
           children = new TrieTree[26];
       }
   }
   ```



### 636. Exclusive Time of Functions

1. First I use two stack to solve this problem, one is used to record the function id, the other is used to record the interval sum. This method would run in O(n) time.

2. ```java
   class Solution {
       public int[] exclusiveTime(int n, List<String> logs) {
           int[] answer = new int[n];
           if (logs == null || logs.size() == 0) {
               return answer;
           }
           Stack<int[]> fstack = new Stack<>();
           Stack<Integer> istack = new Stack<>();
           for (String log: logs) {
               String[] info = log.split(":");
               int fid = Integer.valueOf(info[0]);
               int time = Integer.valueOf(info[2]);
               if ("start".equals(info[1])) {
                   fstack.push(new int[]{fid, time});
                   istack.push(0);
               } else if ("end".equals(info[1])) {
                   int temp, sum = 0;
                   while (!istack.empty() && (temp = istack.pop()) != 0) {
                       sum += temp;
                   }
                   int[] start = fstack.pop();
                   answer[fid] += time - start[1] + 1 - sum;
                   istack.push(time - start[1] + 1);
               }
           }
           return answer;
       }
   }
   ```

2. Actually, we could only use one stack and one integer variable to solve this problem. The stack could keep track of the function id, and the variable could used to calculate the sum. The method would run in O(n) where n is the list size.

3. ```java
   class Solution {
       public int[] exclusiveTime(int n, List<String> logs) {
           int[] answer = new int[n];
           if (logs == null || logs.size() == 0) {
               return answer;
           }
           String[] first = logs.get(0).split(":");
           Stack<Integer> fstack = new Stack<>();
           fstack.push(Integer.valueOf(first[0]));
           int i = 1, prev = Integer.valueOf(first[2]);
           while (i < logs.size()) {
               String[] log = logs.get(i).split(":");
               int fid = Integer.valueOf(log[0]);
               int time = Integer.valueOf(log[2]);
               if ("start".equals(log[1])) {
                   if (!fstack.empty()) {
                       answer[fstack.peek()] += time - prev;
                   }
                   prev = time;
                   fstack.push(fid);
               } else {
                   fstack.pop();
                   answer[fid] += time - prev + 1;
                   prev = time + 1;
               }
               i++;
           }
           return answer;
       }
   }
   ```

4. 