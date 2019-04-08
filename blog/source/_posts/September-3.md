title: LeetCode Note Sep-3
date: 2018-9-18 14:16:00
categories: Algorithm

---



### 416. Partition Equal Subset Sum

Classic 0-1 Knapsack probles. Use two dimension array dp\[i]\[j] to represent that end at i-th element can find the sum j.

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num: nums) {
            sum += num;
        }
        if (sum % 2 == 1) {
            return false;
        }
        int len = nums.length;
        // dp[i][j] represent end with i-th we could find a sum == j.
        boolean[][] dp = new boolean[len+1][sum/2+1];
        for (int i = 0; i < len + 1; i++) {
            dp[i][0] = true;
        }
        for (int j = 1; j < sum/2+1; j++) {
            dp[0][j] = false;
        }
        for (int i = 1; i < len + 1; i++) {
            for (int j = 1; j < sum/2+1; j++) {
                dp[i][j] = dp[i-1][j];
                if (j >= nums[i-1]) {
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]];
                }
            }
        }
        return dp[len][sum/2];
    }
   
}
```

we can optimize the space complexity by using one-dimension array dp and update from end to start.

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num: nums) {
            sum += num;
        }
        if (sum % 2 == 1) {
            return false;
        }
        int len = nums.length;
        // dp[i][j] represent end with i-th we could find a sum == j.
        boolean[] dp = new boolean[sum/2+1];
        dp[0] = true;
        for (int num: nums) {
            for (int j = sum/2; j >= num; j--) {
                dp[j] = dp[j] || dp[j-num];
            }
        }
        return dp[sum/2];
    }
   
}
```

