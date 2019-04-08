title: LeetCode Note Sep-4
date: 2018-9-25 14:16:00
categories: Algorithm

---



### 376. Wiggle Subsequence

1. first we could use dfs to traverse all situations. In this way it could cost O(n!) time complexity.

2. ```java
   class Solution {
       public int wiggleMaxLength(int[] nums) {
           int len = nums.length;
           if (nums == null ||nums.length == 0) {
               return 0;
           }
           return 1 + Math.max(calculate(nums, 0, true), calculate(nums, 0, false));
       }
       private int calculate(int[] nums, int index, boolean isUp) {
           int answer = 0;
           for (int i = index+1; i < nums.length; i++) {
               if (isUp && nums[i] > nums[i-1] || (!isUp && nums[i] < nums[i-1])) {
                   answer = Math.max(answer, 1 + calculate(nums, i, !isUp));
               }
           }
           return answer;
       }
   }
   ```

2. we could use dp to record the status and cut time complexity. In this way, we update the up[] and down[]  whcih represent the i-th element as the ending element and up or down represent the direction. So you know the last element will always be count in because we always could put it in any situation. 

3. ```java
   class Solution {
       public int wiggleMaxLength(int[] nums) {
           int len = nums.length;
           if (nums == null ||nums.length == 0) {
               return 0;
           }
           int[] up = new int[len];
           int[] down = new int[len];
           Arrays.fill(up, 1);
           Arrays.fill(down, 1);
           for (int i = 0; i < len-1; i++) {
               for (int j = i+1; j < len; j++) {
                   if (nums[i] < nums[j]) {
                       up[j] = Math.max(up[j], down[i]+1);
                   } else if (nums[i] > nums[j]) {
                       down[j] = Math.max(down[j], up[i]+1);
                   }
               }
           }
           
           return Math.max(up[len-1], down[len-1]);
       }
   }
   ```

3. we also could optimsize the dp into O(n). update up[] and down[] in this way:

   if nums[i] > nums[i-1] : up[i] = down[i-1] + 1, down[i] = down[i-1]

   if nums[i] < nums[i-1] : down[i] = up[i-1] + 1, up[i] = up[i-1]

   if nums[i] == nums[i-1] : up[i] = up[i-1], down[i] = down[i-1]

   Every time we both update up[] and down[] because for the continuous one direction elements we could use the back to change the front one. So keep the update the same.

4. ```java
   class Solution {
       public int wiggleMaxLength(int[] nums) {
           int len = nums.length;
           if (nums == null ||nums.length == 0) {
               return 0;
           }
           int[] up = new int[len];
           int[] down = new int[len];
           Arrays.fill(up, 1);
           Arrays.fill(down, 1);
           for (int i = 1; i < len; i++) {
               if (nums[i] > nums[i-1]) {
                   up[i] = down[i-1] + 1;
                   down[i] = down[i-1];
               } else if (nums[i] < nums[i-1]) {
                   down[i] = up[i-1] + 1;
                   up[i] = up[i-1];
               } else {
                   up[i] = up[i-1];
                   down[i] = down[i-1];
               }
           }
           
           return Math.max(up[len-1], down[len-1]);
       }
   }
   ```

4. Last optimsize the sapce complexity.

5. ```java
   class Solution {
       public int wiggleMaxLength(int[] nums) {
           int len = nums.length;
           if (nums == null ||nums.length == 0) {
               return 0;
           }
           int up = 1;
           int down = 1;
           for (int i = 1; i < len; i++) {
               if (nums[i] > nums[i-1]) {
                   up = down + 1;
               } else if (nums[i] < nums[i-1]) {
                   down = up + 1;
               }
           }
           return Math.max(up, down);
       }
   }
   ```

### 467. Unique Substrings in Wraparound String

1. First I want to use brute force to solve this problem. And Apparently time limited exceeded.

2. ```java
   class Solution {
       public int findSubstringInWraproundString(String p) {
           char[] chars = p.toCharArray();
           Set<String> stringSet = new HashSet<>();
           for (int i = 0; i < chars.length; i++) {
               for (int j = i; j < chars.length; j++) {
                   if (i == j || (chars[j] - chars[j-1] + 26) % 26== 1) {
                       stringSet.add(p.substring(i, j+1));
                   } else {
                       break;
                   }
               }
           }
           return stringSet.size();
       }
   }
   ```

2. Then we try to optimsize the brute force method and find out that we could use dp[] method  and dp\[i]\[j] represent the length of string which end with char (i+'a') at the index j. Also we keep a max[26] to record the max value that end with each char. Finally we just sum up all the max value and get the answer.

3. ```java
   class Solution {
       public int findSubstringInWraproundString(String p) {
           char[] chars = p.toCharArray();
           int[][] dp = new int[26][p.length()+1];
           int[] max = new int[26];
           for (int i = 0; i < chars.length; i++) {
               int curr = (chars[i] - 'a' + 26) % 26;
               int prev = (chars[i] - 'a' - 1 + 26) % 26;
               dp[curr][i+1] = dp[prev][i] + 1;
               max[curr] = Math.max(max[curr], dp[curr][i+1]);
           }
           int answer  = 0;
           for (int i = 0; i < 26; i++) {
               answer += max[i];
           }
           return answer;
       }
   }
   ```

3. Base on the method above, try to save some space, we optimsize the dp\[26]\[p.length()+1] to dp\[2]\[26]. Just remember to clean the array before using.

4. ```java
   class Solution {
       public int findSubstringInWraproundString(String p) {
           char[] chars = p.toCharArray();
           int[][] dp = new int[2][26];
           int[] max = new int[26];
           for (int i = 0; i < chars.length; i++) {
               Arrays.fill(dp[(i+1)%2], 0);
               int curr = (chars[i] - 'a' + 26) % 26;
               int prev = (chars[i] - 'a' - 1 + 26) % 26;
               dp[(i+1)%2][curr] = dp[i%2][prev] + 1;
               max[curr] = Math.max(max[curr], dp[(i+1)%2][curr]);
           }
           int answer  = 0;
           for (int i = 0; i < 26; i++) {
               answer += max[i];
           }
           return answer;
       }
   }
   ```

4. Again try to optimsize the space complexity, we only need the max array, and use a variable to record the length and update the max every time.

8. ```java
   class Solution {
       public int findSubstringInWraproundString(String p) {
           char[] chars = p.toCharArray();
           int[] max = new int[26];
           int currLen = 0;
           for (int i = 0; i < chars.length; i++) {
               if (i == 0 || chars[i] - chars[i-1] == 1 || chars[i] - chars[i-1] == -25) {
                   currLen++;
               } else {
                   currLen = 1;
               }
               int curr = (chars[i] - 'a' + 26) % 26;
               max[curr] = Math.max(max[curr], currLen);
           }
           int answer  = 0;
           for (int i = 0; i < 26; i++) {
               answer += max[i];
           }
           return answer;
       }
   }
   ```



### 221. Maximal Square

1. We use dp to solve this problem. dp\[i][j] represent the edge of square which right down corner is at row i , column j. The transaction equation is dp\[i][j] = min(min(dp\[i-1][j], dp\[i][j-1]), dp\[i-1][j-1]) + 1; O(m*n)

2. ```java
   class Solution {
       public int maximalSquare(char[][] matrix) {
           if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
               return 0;
           }
           int row = matrix.length;
           int column = matrix[0].length;
           int[][] dp = new int[row][column];
           int maxArea = 0;
           // initialize the first row and column
           for (int r = 0; r < row; r++) {
               dp[r][0] = matrix[r][0] - '0';
               if (dp[r][0] == 1) {
                   maxArea = 1;
               }
           }
           for (int c = 0; c < column; c++) {
               dp[0][c] = matrix[0][c] - '0';
               if (dp[0][c] == 1) {
                   maxArea = 1;
               }
           }
           for (int i = 1; i < row; i++) {
               for (int j = 1; j < column; j++) {
                   if (matrix[i][j] == '0') {
                       dp[i][j] = 0;
                   } else {
                       dp[i][j] = Math.min(Math.min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1]) + 1;
                       maxArea = Math.max(maxArea, dp[i][j]*dp[i][j]);
                   }
               }
           }
           return maxArea;
       }
   }
   ```

2. optimsize the space complexity.

3. ```java
   class Solution {
       public int maximalSquare(char[][] matrix) {
           if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
               return 0;
           }
           int row = matrix.length;
           int column = matrix[0].length;
           int[] dp = new int[column+1];
           int maxArea = 0;
           int prev = 0;
           for (int i = 1; i < row + 1; i++) {
               for (int j = 1; j < column + 1; j++) {
                   int temp = dp[j];
                   if (matrix[i-1][j-1] == '1') {
                       dp[j] = Math.min(Math.min(dp[j], dp[j-1]), prev) + 1;
                       maxArea = Math.max(maxArea, dp[j]*dp[j]);
                   } else {
                       // clean the dirty data
                       dp[j] = 0;
                   }
                   prev = temp;
               }
           }
           return maxArea;
       }
   }
   ```



### 646. Maximum Length of Pair Chain

1. when i < j, pairs\[i][1] < pairs\[i][1] < pairs\[j][0] < pairs\[j][1], then i could be followed by j. We first use dfs to solve it and then try to optimsize it by dynamic programming.

2. ```java
   class Solution {
       public int findLongestChain(int[][] pairs) {
           if (pairs == null || pairs.length == 0) {
               return 0;
           }
           if (pairs.length == 1) 
               return 1;
           Arrays.sort(pairs, (a, b) -> {
               if (a[1] == b[1]) {
                   return a[0] - b[0];
               }
               return a[1] - b[1];
           });
           int len = pairs.length;
           return dfs(pairs, Integer.MIN_VALUE, 0);
       }
       private int dfs(int[][] pairs, int base, int index) {
           if (index == pairs.length) {
               return 0;
           }
           int answer = 0;
           for (int i = index; i < pairs.length; i++) {
               if (pairs[i][0] > base) {
                   answer = Math.max(dfs(pairs, pairs[i][1], i+1)+1, answer);
               }
           }
           return answer;
       }
   }
   ```


2. Using the dynamic programming will save much time, just need one dimension array dp[], dp[i] represent the max length end with pairs[i]. Finally, we search to find the max length in dp[] which is the answer;

3. ```java
   class Solution {
       public int findLongestChain(int[][] pairs) {
           if (pairs == null || pairs.length == 0) {
               return 0;
           }
           if (pairs.length == 1) 
               return 1;
           Arrays.sort(pairs, (a, b) -> {
               if (a[1] == b[1]) {
                   return a[0] - b[0];
               }
               return a[1] - b[1];
           });
           int len = pairs.length;
           int[] dp = new int[len];
           Arrays.fill(dp, 1);
           int max = 0;
           for (int i = 1; i < len; i++) {
               for (int j = 0; j < i; j++) {
                   if (pairs[j][1] < pairs[i][0]) {
                       dp[i] = Math.max(dp[i], dp[j]+1);
                   }
               }
               max = Math.max(dp[i], max);
           }
           return max;
       }
      
   }
   ```



### 638. Shopping Offers

1. We use dfs first to solve ths problem. Thinking that we are not required to only use special offer, i just calculate the things left with its price then add the special offer's price. 

2. ```java
   class Solution {
       public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
           
           return dfs(price, special, needs);
       }
       private int dfs(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
           int curr = sum(price, needs);
           int index = 0;
           for (List<Integer> offer: special) {
               List<Integer> newNeed = new ArrayList<>(needs);
               for (index = 0; index < needs.size(); index++) {
                   int update = newNeed.get(index) - offer.get(index);
                   if (update < 0) {
                       break;
                   }
                   newNeed.set(index, update);
               }
               if (index == needs.size()) {
                   curr = Math.min(curr, offer.get(index) + dfs(price, special, newNeed));
               }
           }
           return curr;
       }
       private int sum (List<Integer> price, List<Integer> needs) {
           int answer = 0;
           for (int index = 0; index < price.size(); index++) {
               answer += price.get(index) * needs.get(index);
           }
           return answer;
       }
   }
   ```

2. There may be duplicate calculation occurs so we use a hash map to record the result. It's some kind like a cache using for speed up by sacrificing space.

3. ```java
   class Solution {
       public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
           Map<List<Integer>, Integer> cache = new HashMap<>();
           return dfs(cache, price, special, needs);
       }
       private int dfs(Map<List<Integer>, Integer> cache, List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
           if (cache.containsKey(needs)) {
               return cache.get(needs);
           }
           int curr = sum(price, needs);
           int index = 0;
           for (List<Integer> offer: special) {
               List<Integer> newNeed = new ArrayList<>(needs);
               for (index = 0; index < needs.size(); index++) {
                   int update = newNeed.get(index) - offer.get(index);
                   if (update < 0) {
                       break;
                   }
                   newNeed.set(index, update);
               }
               if (index == needs.size()) {
                   curr = Math.min(curr, offer.get(index) + dfs(cache, price, special, newNeed));
                   cache.put(newNeed, curr);
               }
           }
           return curr;
       }
       private int sum (List<Integer> price, List<Integer> needs) {
           int answer = 0;
           for (int index = 0; index < price.size(); index++) {
               answer += price.get(index) * needs.get(index);
           }
           return answer;
       }
   }
   ```

### 650. 2 Keys Keyboard

1.  We could use dp to solve this problem. As we know, for each kind of past, we need to add one copy operation. For example, we have x and want y which y % x == 0, so the number of operation should be (y / x - 1 + 1) . Base on this observation, we find the dp solution.

2. ```java
   class Solution {
       public int minSteps(int n) {
           if (n <= 1) {
               return 0;
           }
           int[] dp = new int[n+1];
           for (int i = 2; i <= n; i++) {
               dp[i] = i;
               for (int j = i / 2; j > 0; j--) {
                   if(i % j == 0) {
                       dp[i] = dp[j] + i/j;
                       break;
                   }
               }
           }
           return dp[n];
       }
   }
   ```

2. There is a math way to solve it. The answer could be seen as the sum of all the prime  factorization

3. ```java
   class Solution {
       public int minSteps(int n) {
           if (n <= 1) {
               return 0;
           }
           int answer = 0;
           int index = 2;
           while (n > 1){
               while (n % index == 0) {
                   answer += index;
                   n = n / index;
               }
               index++;
           }
           return answer;
       }
   }
   ```



