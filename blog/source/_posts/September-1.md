title: LeetCode Note Sep-1
date: 2018-9-4 14:16:00
categories: Algorithm

---



### 93. Restore IP Addresses

1. I would use backtracking/dfs method to do this problem. There are some corner case need to care about. For example, "010010", the two 0 in the middle could not be put together, neither could not be cut into one 0. So when we calculate the num if its 0, it could not do the recursion again. The time complexity of this method would be O(1).

2. ```java
   class Solution {
       public List<String> restoreIpAddresses(String s) {
           List<String> result = new ArrayList<>();
           if (s == null) {
               return result;
           }
           int len = s.length();
           if (len > 12 || len < 4) {
               return result;
           }
           helper(result, new StringBuilder(), s.toCharArray(), 0, 0);
           return result;
       }
       private void helper(List<String> result, StringBuilder sb, char[] chars, int cIndex, int aIndex) {
           if (aIndex == 4 && cIndex == chars.length) {
               sb.deleteCharAt(sb.length()-1);
               result.add(sb.toString());
               return;
           } else if (aIndex == 4 || cIndex == chars.length) {
               return;
           }
           int num = 0;
           for (int i = cIndex; i < chars.length && i < cIndex + 3; i++) {
               StringBuilder currSB = new StringBuilder(sb.toString());
               num = num*10 + (chars[i] - '0');
               currSB.append(num+".");
               helper(result, currSB, chars, i+1, aIndex+1);
               if(num == 0 || num > 25 || (i+1 < chars.length && num == 25 && (chars[i+1] - '0') > 5)) {
                   break;
               }
           }
       }
   }
   ```



### 60. Permutation Sequence

1. We still use backtracking method to solve the permutation problem.  My method for this question is too slow even though it accepted. 

2. ```java
   class Solution {
       public String getPermutation(int n, int k) {
           if (n < 1) {
               return "";
           }
           int[] answer = new int[1];
           int[] numbers = new int[n+1];
           for (int i = 0; i < n+1; i++) {
               numbers[i] = i;
           }
           getKth(answer, numbers, 0, k, 0);
           return String.valueOf(answer[0]);
       }
       private int getKth(int[] answer, int[] numbers, int curr, int k, int count) {
           if (curr / Math.pow(10, numbers.length-2) >= 1) {
               count++;
               if (count == k) {
                   answer[0] = curr;
               }
               return count;
           }
           for (int i = 1; i < numbers.length; i++) {
               int num = curr;
               if (numbers[i] > 0) {
                   num = num * 10 + numbers[i];
                   numbers[i] = 0;
                   count = getKth(answer, numbers, num, k, count);
                   numbers[i] = i;
               }
               if (count >= k) {
                   return count;
               }
           }
           return count;
       }
   }
   ```

2. Optimize my method to save some time in the for loop place, reduce time from 1000+ms to 300+ms, but still too slow.

3. ```java
   class Solution {
       public String getPermutation(int n, int k) {
           if (n < 1) {
               return "";
           }
           int[] answer = new int[1];
           List<Integer> numbers = new ArrayList<>();
           for (int i = 1; i < n+1; i++) {
               numbers.add(i);
           }
           getKth(answer, numbers, 0, k, 0);
           return String.valueOf(answer[0]);
       }
       private int getKth(int[] answer, List<Integer> numbers, int curr, int k, int count) {
           if (numbers.size() == 0) {
               count++;
               if (count == k) {
                   answer[0] = curr;
               }
               return count;
           }
           for (int i = 0; i < numbers.size(); i++) {
               int num = numbers.get(i);
               numbers.remove(i);
               count = getKth(answer, numbers, curr*10+num, k, count);
               numbers.add(i, num);
               if (count >= k) {
                   return count;
               }
           }
           return count;
       }
   }
   ```

3. The new method based on a regulation: for the n length number, the permutation number are n! . So we could find the kth num by decrease the n to n-1 and find the first number, and so on. According to the k to find each number in each index. 

4. ```java
   class Solution {
       public String getPermutation(int n, int k) {
           if (n < 1) {
               return "";
           }
           int[] factorial = new int[n+1];
           int product = 1;
           factorial[0] = 1;
           for (int i = 1; i < n+1; i++) {
               product *= i;
               factorial[i] = product;
           }
           factorial[0] = 1;
           List<Integer> numbers = new ArrayList<>();
           for (int i = 1; i < n+1; i++) {
               numbers.add(i);
           }
           k--;
           StringBuilder sb = new StringBuilder();
           for (int i = n - 1; i >= 0; i--) {
               int index = k / factorial[i];
               sb.append(numbers.get(index));
               numbers.remove(index);
               k -= index * factorial[i];
           }
           return sb.toString();
       }
   }
   
   ```



### 22. Generate Parentheses

1. Still we use backtracking method to solve this problem. The key to keep the parentheses valid is to make sure the amount of left parenthese is larger than right parenthese.

2. ```java
   class Solution {
       public List<String> generateParenthesis(int n) {
           List<String> result = new ArrayList<>();
           helper(result, new StringBuilder(), n, n);
           return result;
       }
       private void helper(List<String> result, StringBuilder sb, int left, int right) {
           if (right < left || left < 0 || right < 0) {
               return;
           }
           if (left == 0 && right == 0) {
               result.add(sb.toString());
               return;
           } 
           if (left > 0) {
               sb.append('(');
               helper(result, sb, left-1, right);
               sb.deleteCharAt(sb.length()-1);
           } 
           if (left < right) {
               sb.append(')');
               helper(result, sb, left, right-1);
               sb.deleteCharAt(sb.length()-1);
           }
       }
   }
   ```


### 37. Sudoku Solver

1. The normal backtracking method, only could use one direction to traverse the two dimensional array. No pretreatment is much faster I think it could because the size of array is fixed and operate collection class is much slower.

2. ```java
   class Solution {
      public void solveSudoku(char[][] board) {
           dfs(board, 0);
       }
       private boolean dfs(char[][] board, int number) {
           if (number >= 81) {
               return true;
           }
           int row = number / 9;
           int column = number % 9;
           if (board[row][column] != '.') {
               return dfs(board, number+1);
           }
           for (int i = 1; i < 10; i++) {
               board[row][column] = (char)('0' + i);
               if (isValid(board, row, column, i) && dfs(board, number+1)) {
                   return true;
               }
               board[row][column] = '.';
           }
           return false;
       }
       private boolean isValid(char[][] board, int row, int column, int number) {
           for (int i = 0; i < 9; i++) {
               if (board[row][i] == '.') {
                   continue;
               }
               if (i != column && board[row][i] == '0' + number) {
                   return false;
               }
           }
           for (int i = 0; i < 9; i++) {
               if (board[i][column] == '.') {
                   continue;
               }
               if (i != row && board[i][column] == '0' + number){
                   return false;
               }
           }
           int row0 = row / 3 * 3;
           int column0 = column /3 * 3;
           for (int i = row0; i < row0 + 3; i++) {
               for (int j = column0; j < column0 + 3; j++) {
                   if (board[i][j] == '.') {
                       continue;
                   }
                   if (board[i][j] - '0' == number && !(i == row && j == column)) {
                       return false;
                   }
               }
           }
           return true;
       }
   }
   ```



### 36. Valid Sudoku

1. I would use bitmap to mark the element has appeared or not. Of course change it to a Hash table still works.

2. ```java
   class Solution {
       public boolean isValidSudoku(char[][] board) {
           int[][] bitmap = new int[3][9];
           for (int i = 0; i < 9; i++) {
               for (int j = 0; j < 9; j++) {
                   if (board[i][j] != '.') {
                       int num = 1 << (board[i][j] - '0' - 1);
                       if ((num & bitmap[0][i]) == num) {
                           return false;
                       }
                       bitmap[0][i] = num | bitmap[0][i];
                   }
                   if (board[j][i] != '.') {
                       int num = 1 << (board[j][i] - '0' - 1);
                       if ((num & bitmap[1][i]) == num) {
                           return false;
                       }
                       bitmap[1][i] = num | bitmap[1][i];
                   }
                   int row = i / 3 * 3 + j / 3;
                   int column = i % 3 * 3 + j % 3;
                   if (board[row][column] != '.') {
                       int num = 1 << (board[row][column] - '0' - 1);
                       if ((num & bitmap[2][i]) == num) {
                           return false;
                       }
                       bitmap[2][i] = num | bitmap[2][i];
                   }
   
               }
           }
           return true;
       }
   }
   ```



### 77. Combinations

1. We could use backtracking. The first way i solve is not so backtracking. I just put every number into it one time and recursively call the function and decrease the k number, when it arrive at 0, which means the list is satisfy the requirement, I put it into result list. when the index is not valid, just return.

2. ```java
   class Solution {
       public List<List<Integer>> combine(int n, int k) {
           List<List<Integer>> result = new ArrayList<>();
           List<Integer> curr = new ArrayList<>();
           dfs(result, curr, n, k, 1);
           return result;
       }
       private void dfs(List<List<Integer>> result, List<Integer> curr, int n, int k, int start) {
           if (n - start + 1 < k) {
               return;
           }
           if (k == 0) {
               result.add(curr);
           }
           for (int i = start; i <= n; i++) {
               List<Integer> next = new ArrayList<>(curr);
               next.add(i);
               dfs(result, next, n, k-1, i+1);
           }
       }
   }
   ```

1. The much classic backtracking is to add a new element and then call recursion, then after recursion, drop out the element which just add into it.

   ```java
   class Solution {
       public List<List<Integer>> combine(int n, int k) {
           List<List<Integer>> result = new ArrayList<>();
           dfs(result, new ArrayList<>(), n, k, 1);
           return result;
       }
       private void dfs(List<List<Integer>> result, List<Integer> curr, int n, int k, int start) {
           if (curr.size() == k) {
               result.add(new ArrayList<>(curr));
               return;
           }
           for (int i = start; i <= n; i++) {
               curr.add(i);
               dfs(result, curr, n, k, i+1);
               curr.remove(curr.size()-1);
           }
       }
   }
   ```



### 692. Top K Frequent Words

1. In this question, I would first implement a trie tree to save the words and its counts. Then for select the top k frequent words, I would use the heap to maintain the sequence of the words, override the comparator to sort it in the sequence we want. The time complexity is O(nlogK), space complexity is O(n)

2. ```java
   class Solution {
       public List<String> topKFrequent(String[] words, int k) {
           List<String> result = new ArrayList<>();
           if (words == null || words.length == 0 || k <= 0) {
               return result;
           }
           PriorityQueue<TreeNode> heap = new PriorityQueue<>(new Comparator<TreeNode>() {
               @Override
               public int compare(TreeNode node1, TreeNode node2) {
                   if (node1.count == node2.count) {
                       return node1.val.compareTo(node2.val);
                   }
                   return node2.count - node1.count;
               }
           });
           TrieTree trie = new TrieTree();
           for (String word: words) {
               TreeNode curr = trie.insert(word);
               if (heap.contains(curr)) {
                   heap.remove(curr);
               }
               heap.add(curr);
           }  
           System.out.println(heap.size());
           int size = heap.size();
           for (int i = 0; i < k && i < size; i++) {
               result.add(heap.poll().val);
           }
           return result;
       }
   }
   class TrieTree {
       private TreeNode root;
       
       public TrieTree() {
           this.root = new TreeNode();
       }
       
       public TreeNode insert(String s) {
           if (s == null || s.length() == 0) {
               return null;
           }
           TreeNode curr = this.root;
           for (char c: s.toCharArray()) {
               if (curr.children[c-'a'] == null) {
                   curr.children[c-'a'] = new TreeNode();
               }
               curr = curr.children[c-'a'];
           }
           curr.count = curr.count + 1;
           curr.val = s;
           return curr;
       }
   }
   class TreeNode {
           final int alphabet = 26;
           int count;
           TreeNode[] children;
           String val;
           public TreeNode() {
               children = new TreeNode[alphabet];
               count = 0;
               val = null;
           }
       }
   ```

2. Another method to count the frequency is to use map. The time complexity is O(nlogK), space complexity is O(n)

1. ```java
   class Solution {
       public List<String> topKFrequent(String[] words, int k) {
           List<String> result = new ArrayList<>();
           if (words == null || words.length == 0 || k <= 0) {
               return result;
           }
           Map<String, Integer> count = new HashMap<>();
           for (String word: words) {
               count.put(word, count.getOrDefault(word, 0)+1);
           }
           PriorityQueue<String> heap = new PriorityQueue<>(new Comparator<String>() {
               @Override
               public int compare(String s1, String s2) {
                   int count1 = count.getOrDefault(s1, 0);
                   int count2 = count.getOrDefault(s2, 0);
                   if (count1 == count2) {
                       return s1.compareTo(s2);
                   }
                   return count2 - count1;
               }
           });
           for (String word: words) {
               if (!heap.contains(word)){
                   heap.add(word);
               }
           }
           int size = heap.size();
           for (int i = 0; i < k && i < size; i++) {
               result.add(heap.poll());
           }
           return result;
       }
   }
   
   ```


3. We can use Collections.sort method to sort, so no need for heap. The time complexity is O(nlogn), space complexity is O(n)

1. ```java
   class Solution {
       public List<String> topKFrequent(String[] words, int k) {
           if (words == null || words.length == 0 || k <= 0) {
               return new ArrayList<String>();
           }
           Map<String, Integer> count = new HashMap<>();
           for (String word: words) {
               count.put(word, count.getOrDefault(word, 0)+1);
           }
           List<String> result = new ArrayList<>(count.keySet());
           Collections.sort(result, new Comparator<String>() {
               @Override
               public int compare(String s1, String s2) {
                   int count1 = count.getOrDefault(s1, 0);
                   int count2 = count.getOrDefault(s2, 0);
                   if (count1 == count2) {
                       return s1.compareTo(s2);
                   }
                   return count2 - count1;
               }
           });
           return result.subList(0, k);
       }
   }
   
   ```

4. We can use bucket sort to sort the sequence because the frequency is no more than the array length. Then we put the string with the same frequency into the same bucket and build trie tree in each bucket to keep the string sort according to the alphabet order. This should be in O(n) time complexity and O (n) space

5. ```java
   class Solution {
       public List<String> topKFrequent(String[] words, int k) {
           if (words == null || words.length == 0 || k <= 0) {
               return new ArrayList<String>();
           }
           Map<String, Integer> count = new HashMap<>();
           for (String word: words) {
               count.put(word, count.getOrDefault(word, 0)+1);
           }
           TreeNode[] trie = new TreeNode[words.length+1];
           for (Map.Entry<String, Integer> entry : count.entrySet()) {
               if (trie[entry.getValue()] == null) {
                   trie[entry.getValue()] = new TreeNode();
               }
               addWord(trie[entry.getValue()], entry.getKey());
           }
           List<String> result = new ArrayList<>();
           for (int i = trie.length - 1; i >= 0; i--) {
               if (trie[i] != null) {
                   getWords(trie[i], result, k);
               }
           }
           return result;
       }
       
       public void addWord(TreeNode root, String s) {
           if (s == null || s.length() == 0) {
               return;
           }
           TreeNode curr = root;
           for (char c: s.toCharArray()) {
               if (curr.children[c-'a'] == null) {
                   curr.children[c-'a'] = new TreeNode();
               }
               curr = curr.children[c-'a'];
           }
           curr.isEnd = true;
           curr.val = s;
           return;
       }
       public void getWords(TreeNode root, List<String> result, int k) {
           if (root == null) {
               return;
           }
           if (root.isEnd) {
               result.add(root.val);
           }
           if (result.size() == k) {
               return;
           }
           for (int i = 0; i < 26; i++) {
               if (root.children[i] != null) {
                   getWords(root.children[i], result, k);
               }
           }
       }
   }
   class TreeNode {
           final int alphabet = 26;
           boolean isEnd;
           TreeNode[] children;
           String val;
           public TreeNode() {
               children = new TreeNode[alphabet];
               isEnd = false;
               val = null;
           }
       }
   ```



### 130. Surrounded Regions

1. We can use dfs for the nodes in the edge, each O which chould constract a island in th edge should not be change, so we just change it into another sign '\*'. finally we for loop the whole board and change all the O to 'X', and change the '\*' back into 'O'.

2. ```java
   class Solution {
       public void solve(char[][] board) {
           if (board == null || board.length < 2 || board[0].length < 2) {
               return;
           }
           int rowNum = board.length;
           int columnNum = board[0].length;
           for (int i = 0; i < rowNum; i++) {
               if (board[i][0] == 'O') {
                   dfs(board, i, 0);
               }
               if (board[i][columnNum-1] == 'O') {
                   dfs(board, i, columnNum-1);
               }
           }
           for (int i = 0; i < columnNum; i++) {
               if (board[0][i] == 'O') {
                   dfs(board, 0, i);
               }
               if (board[rowNum-1][i] == 'O') {
                   dfs(board, rowNum-1, i);
               }
           }
           for (int row = 0; row < board.length; row++) {
               for (int column = 0; column < board[0].length; column++) {
                   if (board[row][column] == 'O') {
                       board[row][column] = 'X';
                   } else if (board[row][column] == '*') {
                       board[row][column] = 'O';
                   }
               }
           }
       }
       private void dfs(char[][] board, int row, int column) {
           if (board[row][column] == 'X') {
               return;
           }
           board[row][column] = '*';
           if (row - 1 >= 0 && board[row-1][column] == 'O') {
               dfs(board, row-1, column);
           }
           if (row + 1 < board.length && board[row+1][column] == 'O') {
               dfs(board, row+1, column);
           }
           if (column - 1 >= 0 && board[row][column-1] == 'O') {
               dfs(board, row, column-1);
           }
           if (column + 1 < board[0].length && board[row][column+1] == 'O') {
               dfs(board, row, column+1);
           }
       }
   }
   ```

2. Union-find method to solve this problem. first connect all the 'O' in the edge with a dummy parent and give this parent a big rank. Then for loop the board to connect each 'O' with the other. Then search if the 'O' is son of dummy parent. If not, change it to 'X'. The optimize on union-find method contains path compress and keep rank. The time could be O(n).

3. ```java
   class Union {
       int[] rank;
       int[] parents;
       int size;
       
       public Union (int size) {
           this.size = size;
           this.rank = new int[size];
           this.parents = new int[size];
           for (int i = 0; i < size; i++) {
               this.rank[i] = 1;
               this.parents[i] = i;
           }
       }
       public int find(int p) {
           if (parents[p] != p) {
               parents[p] = find(parents[p]);
           }
           return parents[p];
       }
       public void connect(int p, int q) {
           int pparent = find(p);
           int qparent = find(q);
           if (pparent == qparent) {
               return;\
           }
           if (rank[pparent] > rank[qparent]) {
               parents[qparent] = pparent;
               rank[pparent] += rank[qparent];
           } else {
               parents[pparent] = qparent;
               rank[qparent] += rank[pparent];
           }
       }
   }
   ```


### 502. IPO

1. use heap and greedy can solve this problem. 

2. ```java
   class Solution {
       public int findMaximizedCapital(int k, int W, int[] Profits, int[] Capital) {
           if (k == 0 || Profits.length == 0 || Capital.length == 0 || Profits.length != Capital.length) {
               return 0;
           }
           List<Node> nodes = new ArrayList<>();
           for (int i = 0; i < Profits.length; i++) {
               nodes.add(new Node(Capital[i], Profits[i]));
           }
           Collections.sort(nodes, new Comparator<Node>() {
               @Override
               public int compare(Node node1, Node node2) {
                   return node1.capital - node2.capital;
               }
           });
           PriorityQueue<Node> heap = new PriorityQueue<>(new Comparator<Node>() {
               @Override
               public int compare(Node node1, Node node2) {
                   return node2.profit - node1.profit;
               }
           });
           for (Node node: nodes) {
               if (node.capital <= W) {
                   heap.offer(node);
                   continue;
               } 
               while (!heap.isEmpty() && W < node.capital && k > 0) {
                   W += heap.poll().profit;
                   k--;
               }
               if (W < node.capital || k == 0) {
                   break;
               }
               heap.offer(node);
           }
           while (k > 0 && !heap.isEmpty()) {
               W += heap.poll().profit;
               k--;
           }
           return W;
       }
       class Node {
           int capital;
           int profit;
           public Node(int capital, int profit) {
               this.capital = capital;
               this.profit = profit;
           }
       }
   }
   ```

2. We could use two priorityqueue to sort. Time complexity would be O(nlogn)

3. ```java
   class Solution {
       public int findMaximizedCapital(int k, int W, int[] Profits, int[] Capital) {
           if (k == 0 || Profits.length == 0 || Capital.length == 0 || Profits.length != Capital.length) {
               return 0;
           }
           PriorityQueue<int[]> cHeap = new PriorityQueue<>((a, b) -> (a[0] - b[0]));
           PriorityQueue<int[]> pHeap = new PriorityQueue<>((a, b) -> (b[1] - a[1]));
           for (int i = 0; i < Profits.length; i++) {
               cHeap.offer(new int[]{Capital[i], Profits[i]});
           }
           for (int i = 0; i < k; i++) {
               while (!cHeap.isEmpty() && cHeap.peek()[0] <= W) {
                   pHeap.offer(cHeap.poll());
               }
               if (pHeap.isEmpty()) {
                   break;
               }
               W += pHeap.poll()[1];
           }
           return W;
       }
   }
   ```


### 313. Super Ugly Number

1. Just use the factor times the ugly numbers in order and get the minimum one. Keep track of each factor's index.

2. ```java
   class Solution {
       public int nthSuperUglyNumber(int n, int[] primes) {
           int len = primes.length;
           int[] index = new int[len];
           int[] answer = new int[n];
           answer[0] = 1;
           for (int i = 1; i < n; i++) {
               answer[i] = Integer.MAX_VALUE;
               int min = 0;
               for (int j = 0; j < len; j++) {
                   answer[i] = Math.min(answer[index[j]] * primes[j], answer[i]);
                   if (answer[i] == answer[index[j]] * primes[j]) {
                       min = j;
                   }
               }
               for (int j = 0; j < len; j++) {
                   if (answer[index[j]] * primes[j] == answer[i]) {
                       index[j]++;
                   }
               }
           }
           return answer[n-1];
       }
   }
   ```

2. Use space to save some time. We can keep record of the number for each prime and don't need to multiply every time.

3. ```java
   class Solution {
       public int nthSuperUglyNumber(int n, int[] primes) {
           int len = primes.length;
           int[] index = new int[len];
           int[] answer = new int[n];
           int[] val = new int[len];
           Arrays.fill(val, 1);
           int next = 1;
           for (int i = 0; i < n; i++) {
               answer[i] = next;
               next = Integer.MAX_VALUE;
               for (int j = 0; j < len; j++) {
                   if (val[j] == answer[i]) {
                       val[j] = answer[index[j]++] * primes[j];
                   }
                   next = Math.min(val[j], next);
               }
           }
           return answer[n-1];
       }
   }
   ```
