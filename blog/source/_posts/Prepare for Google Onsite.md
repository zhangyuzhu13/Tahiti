title: Prepare for Google Onsite
date: 2019-1-14 14:16:00
categories: Algorithm

---

### Exchange rate conversion(LeetCode 399)

Equations are given in the format `A / B = k`, where `A` and `B` are variables represented as strings, and `k` is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return `-1.0`.

**Example:**
Given `a / b = 2.0, b / c = 3.0.`
queries are: `a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .`
return `[6.0, 0.5, -1.0, 1.0, -1.0 ].`

**Solution1: Union-find**

By using union-find method, we could keep the relations. So now first we find out how many distinct variables there are and build a union-find set for each variable. Let each one be there parent and initialize the value as 1.0. Then we update the relation and value by these rules: keep the root value as 1, just update the children’s value. if a/c = 2, then we take a as root, and give c value as 2, so it keep a multiple relation between root and child. when union method work, we point one root to another and change its value to a multiple to the original value, so its the right multiple to root. Every time using find, we then compress its path to save time.

```java
class Solution {
    public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
        // keep variables distinct
        HashMap<String, Integer> variables = new HashMap<>();
        int len = 0;
        for (String[] equation: equations) {
            if (!variables.containsKey(equation[0])) {
                variables.put(equation[0], len++);
            }
            if (!variables.containsKey(equation[1])) {
                variables.put(equation[1], len++);
            }
        }
        // create the union-find array
        Node[] nodes = new Node[len];
        for (int i = 0; i < nodes.length; ++i) {
            nodes[i] = new Node(i);
        }
        // update the union-find array
        for (int i = 0; i < equations.length; ++i) {
            int index0 = variables.get(equations[i][0]);
            int index1 = variables.get(equations[i][1]);
            int root0 = find(nodes, index0);
            int root1 = find(nodes, index1);
            nodes[root1].parent = root0;
            nodes[root1].value = nodes[index0].value * values[i] / nodes[index1].value;
        }
        // query
        double[] result = new double[queries.length];
        for (int i = 0; i < queries.length; ++i) {
            if (!variables.containsKey(queries[i][0]) || !variables.containsKey(queries[i][1])) {
                result[i] = -1.0;
                continue;
            }
            int index0 = variables.get(queries[i][0]);
            int index1 = variables.get(queries[i][1]);
            int root0 = find(nodes, index0);
            int root1 = find(nodes, index1);
            if (root0 != root1) {
                result[i] = -1.0;
                continue;
            }
            result[i] = nodes[index1].value / nodes[index0].value;
        }
        return result;
    }
    private int find(Node[] nodes, int k) {
        int p = k;
        while (nodes[p].parent != p) {
            p = nodes[p].parent;
            nodes[k].value *= nodes[p].value;
        }
        nodes[k].parent = p;
        return p;
    }
    class Node {
        int parent;
        double value;
        public Node(int parent) {
            this.parent = parent;
            value = 1.0d;
        }
    }
}
```

**Solution 2: DFS**

By using dfs, we just need to use a visited set and each time if we find the query directly, we return the value. If not, we try to find the first part match, then change the query into a new query, and try to find the new query.

```java
class Solution {
    public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
        // keep variables distinct
        HashSet<String> variables = new HashSet<>();
        for (String[] equation: equations) {
            variables.add(equation[0]);
            variables.add(equation[1]);
        }
        double[] result = new double[queries.length];
        int index = 0;
        for (String[] query: queries) {
            if (!variables.contains(query[0]) || !variables.contains(query[1])) {
                result[index++] = -1.0;
                continue;
            }
            result[index++] = helper(equations, values, query, new HashSet<Integer>());
        }
        return result;
    }
    private double helper(String[][] equations, double[] values, String[] query, HashSet<Integer> visitedEquations) {
        // directly find query equation
        for (int i = 0; i < equations.length; ++i) {
            if (equations[i][0].equals(query[0]) && equations[i][1].equals(query[1])) {
                return values[i];
            } else if (equations[i][0].equals(query[1]) && equations[i][1].equals(query[0])) {
                return 1.0 / values[i];
            }
        }
        for (int i = 0; i < equations.length; ++i) {
            if (visitedEquations.contains(i)) continue;
            if (equations[i][0].equals(query[0])) {
                visitedEquations.add(i);
                double value = values[i] * helper(equations, values, new String[]{equations[i][1], query[1]}, visitedEquations);
                if (value > 0) {
                    return value;
                } else {
                    visitedEquations.remove(i);
                }
            }
            if (equations[i][1].equals(query[0])) {
                visitedEquations.add(i);
                double value = helper(equations, values, new String[]{equations[i][0], query[1]}, visitedEquations) / values[i];
                if (value > 0) {
                    return value;
                } else {
                    visitedEquations.remove(i);
                }
            }
        }
        return -1.0;
    }
}
```

### Split Array Largest Sum(LeetCode 410)

Given an array which consists of non-negative integers and an integer *m*, you can split the array into *m*non-empty continuous subarrays. Write an algorithm to minimize the largest sum among these *m*subarrays.

**Note:**
If *n* is the length of array, assume the following constraints are satisfied:

- 1 ≤ *n* ≤ 1000
- 1 ≤ *m* ≤ min(50, *n*)

**Examples:**

```
Input:
nums = [7,2,5,10,8]
m = 2

Output:
18

Explanation:
There are four ways to split nums into two subarrays.
The best way is to split it into [7,2,5] and [10,8],
where the largest sum among the two subarrays is only 18.
```

**Solution 1: DFS**

We create a array visited[len][m] which visited[start][m] means start from index start and split the array into m parts. Its like a memo method which could speed up.

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int n = nums.length;
        int[] presum = new int[n+1];
        presum[0] = 0;
        for (int i = 1; i <= n; i++) {
            presum[i] += nums[i-1] + presum[i-1];
        }
        int[][] visited = new int[n][m+1];
        return dfs(0, m, nums, presum, visited);
    }
    
    private int dfs(int start, int m, int[] nums, int[] presum, int[][] visited) {
        if (m == 1) {
            return presum[nums.length] - presum[start];
        }
        if (visited[start][m] != 0) {
            return visited[start][m];
        }
        int maxSum = Integer.MAX_VALUE;
        for (int i = start; i < nums.length-1; i++) {
            int l = presum[i+1] - presum[start];
            int rightIntervalMax = dfs(i+1, m-1, nums, presum, visited);
            maxSum = Math.min(maxSum, Math.max(l, rightIntervalMax));
            
        }
        visited[start][m] = maxSum;
        return maxSum;
    }
}
```

**Solution 2: DP**

dp[s][j] is the solution for splitting subarray nums[j] … nums[L-1] into s parts.

dp[s+1][i] = min(max(dp[s][j], nums[i]+nums[i+1]….+nums[j]-1))

```java
class Solution {
  public int splitArray(int[] nums, int m){
    int L = nums.length;
    int[] S = new int[L+1];
    S[0]=0;
    for(int i=0; i<L; i++)
        S[i+1] = S[i]+nums[i];

    int[] dp = new int[L];
    for(int i=0; i<L; i++)
        dp[i] = S[L]-S[i];

    for(int s=1; s<m; s++)
    {
        for(int i=0; i<L-s; i++)
        {
            dp[i]=Integer.MAX_VALUE;
            for(int j=i+1; j<=L-s; j++)
            {
                int t = Math.max(dp[j], S[j]-S[i]);
                if(t<=dp[i])
                    dp[i]=t;
                else
                    break;
            }
        }
    }

    return dp[0];
  }
}
```

**Solution 3: Binary Search**

We need to know that there is a max value in nums, and there will be a solution which is >= max, so the answer would be in [max, sum], then we just binary search in this range and check the answer is valid or not. By checking valid, we need to try to split the array into m part which each part is <= value, so when we could split and no elements left, we need to decrease to value, when we could not, it means the value is to small, we need to increase it. Finally we will get the answer.

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        if (nums == null || m < 1 || m > nums.length) return -1;
        int max = -1;
        long sum = 0;
        for (int num: nums) {
            max = max >= num ? max : num;
            sum += num;
        }
        long left = (long)max;
        long right = sum;
        while (left <= right) {
            long mid = (left + right) >>> 1;
            if (valid(mid, nums, m)) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return (int)left;
    }
    private boolean valid(long target, int[] nums, int m) {
        int count = 1;
        long sum = 0;
        for (int num: nums) {
            sum += num;
            if (sum > target) {
                sum = (long)num;
                ++count;
                if (count > m) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

### Read N Characters Given Read4(LeetCode 157)

The API: `int read4(char *buf)` reads 4 characters at a time from a file.

The return value is the actual number of characters read. For example, it returns 3 if there is only 3 characters left in the file.

By using the `read4` API, implement the function `int read(char *buf, int n)` that reads *n*characters from the file.

**Note:**
The `read` function will only be called once for each test case.

**Solution**

```java
/* The read4 API is defined in the parent class Reader4.
      int read4(char[] buf); */

public class Solution extends Reader4 {
    /**
     * @param buf Destination buffer
     * @param n   Maximum number of characters to read
     * @return    The number of characters read
     */
    public int read(char[] buf, int n) {
        char[] buf4 = new char[4];
        int length = 0;
        int num = 4;
        while (num == 4 && length < n) {
            num = read4(buf4);
            for (int i = length; i < n && i < length + num; ++i) {
                buf[i] = buf4[i-length];
            }
            length = Math.min(n, length+num);
        }
        return length;
    }
}
```

### Read N Characters Given Read4 II - Call multiple times

The API: `int read4(char *buf)` reads 4 characters at a time from a file.

The return value is the actual number of characters read. For example, it returns 3 if there is only 3 characters left in the file.

By using the `read4` API, implement the function `int read(char *buf, int n)` that reads *n*characters from the file.

**Note:**
The `read` function may be called multiple times.

**Solution**

There are two more corner case need to concern about.

1. File has been read out
2. file still left but the bug loaded last time and didn’t read.

```java
public class Solution extends Reader4 {
    /**
     * @param buf Destination buffer
     * @param n   Maximum number of characters to read
     * @return    The number of characters read
     */
    char[] buff = new char[4];
    int start = 0;
    int num = 0;
    public int read(char[] buf, int n) {
        int res = 0;
        while (res < n) {
            if (start == 0) {
                num = read4(buff);
            }
            if (num == 0) break;
            while (res < n && start < num) {
                buf[res++] = buff[start++];
            }
            if (start == num) {
                start == 0;
            } 
        }
        return res;
    }
}
```

### Basic Calculator

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only **non-negative** integers, `+`, `-`, `*`, `/` , `()` operators and empty spaces ``. The integer division should truncate toward zero.

**Example 1:**

```
Input: "3+2*(2+2)+3"
Output: 14
```

**Solution**

Based on the calculator with only `+-*\`, we just add a recursion to solve the parentheses.

```java
class Solution {
    public int calculate(String s) {
        int from = s.indexOf("(");
        int to = s.lastIndexOf(")");
        if (from >= 0 && to >= 0) {
            String sub = s.substring(from+1, to);
            int value = calculate(sub);
            s = s.substring(0, from)+value+s.substring(to+1);
        }
        ArrayDeque<Integer> numStack = new ArrayDeque<>();
        char sign = '+';
        int num = 0;
        for(int i = 0; i < s.length(); i++){
            char c = s.charAt(i);
            if (Character.isDigit(c)){
                num = num * 10 + (c - '0');
            }
            if (!Character.isDigit(c) && ' ' != c || i == s.length() - 1){
                switch(sign){
                    case '+':
                        numStack.push(num);
                        break;
                    case '-' :
                        numStack.push(-num);
                        break;
                    case '*':
                        numStack.push(numStack.pop()*num);
                        break;
                    case '/':
                        numStack.push(numStack.pop()/num);
                        break;
                }
                num = 0;
                sign = c;
            }
        }
        int answer = 0;
        for(int i : numStack){
            answer += i;
        }
        return answer;
    }
}
```