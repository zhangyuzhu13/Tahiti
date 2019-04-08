title: Unique Path Problem
date: 2018-12-17 14:16:00
categories: Algorithm

---

Unique path is classic problem solving by dynamic programming. There are many follow-ups for this problem. So I’m writting this blog trying to do some summary and have a better understanding of this kind of problem.

### First simple unique path problem

This problem is from [LeetCode 62](https://leetcode.com/problems/unique-paths/description/).

A robot is located at the top-left corner of a *m* x *n* grid.

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked ‘Finish’ in the diagram below).

How many possible unique paths are there?

**Note:** *m* and *n* will be at most 100.

```
Input: m = 7, n = 3
Output: 28
```

First Solution:

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] dp = new int[m+1][n+1];
        dp[0][1] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m][n];
    }
}
```

Then I could optimsize the space complexity from O(mn) to O(n).

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] dp = new int[2][n+1];
        dp[0][1] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                dp[i%2][j] = dp[(i-1)%2][j] + dp[i%2][j-1];
            }
        }
        return dp[m%2][n];
    }
}
```

### Unique Path II

This problem is from [LeetCode 63](https://leetcode.com/problems/unique-paths-ii/description/).

A robot is located at the top-left corner of a *m* x *n* grid (marked ‘Start’ in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked ‘Finish’ in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

**Note:** *m* and *n* will be at most 100.

```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

Solution:

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) return 0;
        int rows = obstacleGrid.length, columns = obstacleGrid[0].length;
        int[][] dp = new int[rows+1][columns+1];
        dp[0][1] = 1;
        for (int i = 1; i <= rows; ++i) {
            for (int j = 1; j <= columns; ++j) {
            	if (obstacleGrid[i-1][j-1] == 1) continue;
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[rows][columns];
    }
}
```

Then we also could optimsize the space complexity from O(mn) to O(n):

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0 || obstacleGrid[0].length == 0) return 0;
        int rows = obstacleGrid.length, columns = obstacleGrid[0].length;
        int[][] dp = new int[2][columns+1];
        dp[0][1] = 1;
        for (int i = 1; i <= rows; ++i) {
            for (int j = 1; j <= columns; ++j) {
                if (obstacleGrid[i-1][j-1] == 1) {
                    dp[i%2][j] = 0;
                } else {
                	dp[i%2][j] = dp[(i-1)%2][j] + dp[i%2][j-1];   
                }
            }
        }
        return dp[rows%2][columns];
    }
}
```

### Unique Path III

A robot is located at the top-left corner of a *m* x *n* grid.

The robot can only move either right or upper right or bottom right at any point in time. The robot is trying to reach the bottom-right corner of the grid.

How many possible unique paths are there?

```
Input: m = 3, n = 7
Output: 20
```

Solution:

Considering a grid could only come from upper left, bottom left or left, so we need to calculate the numbers for left first. So the traversal order should from left to right, then from top to bottom.

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] dp = new int[m+1][n+1];
        dp[0][0] = 1;
        for (int j = 1; j <= n; ++j) {
            for (int i = 1; i < m; ++i) {
                dp[i][j] = dp[i-1][j-1] + dp[i][j-1] + dp[i+1][j-1];
            }
            dp[m][j] = dp[m-1][j-1] + dp[m][j-1];
        }
        return dp[m][n];
    }
}
```

Then we could do some optimization on space complexity from O(mn) to O(m)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] dp = new int[m+1][2];
        dp[0][0] = 1;
        for (int j = 1; j <= n; ++j) {
            for (int i = 1; i < m; ++i) {
                dp[i][j%2] = dp[i-1][(j-1)%2] + dp[i][(j-1)%2] + dp[i+1][(j-1)%2];
            }
            dp[m][j%2] = dp[m-1][(j-1)%2] + dp[m][(j-1)%2];
            dp[0][j%2] = 0;
        }
        return dp[m][n%2];
    }
}
```

**Follow up 1**

Give three point, find if there is a path going through these three points.

```
input: m = 3, n = 7, points = {{1,1}, {2,2}, {3,3}}
output: true
```

Idea:

As we could only go towards right, so the most left point must be the first to o through. So we need to sort these three points first to know the traversal order.

The difference between column number should be bigger than row numbers. If this requirment isn’t satisfied, the result should be false;

Solution:

```java
class Solution {
    public int uniquePaths(int m, int n, int[][] points) {
        if (m < 1 || n < 1) return 0;
        Arrays.sort(points, (a,b)->a[1]-b[1]);
        if (points[1][1] - points[0][1] < math.abs(points[0][0] - points[1][0])) return false;
        if (points[2][1] - points[1][1] < math.abs(points[2][0] - points[1][0])) return false;
        return true;
    }
}
```

**Follow up 2**

Give three point, find the amount of path there is a path going through these three points.

Idea:

As we could only go towards right, so the most left point must be the first to o through. So we need to sort these three points first to know the traversal order.

And at the row number equals to one point, we only count the numbers of the target point, others will be kept 0.

```java
class Solution {
 public static int uniquePaths(int m, int n, int[][] points) {
        if (m < 1 || n < 1) return 0;
        Arrays.sort(points, (a,b)->a[1]-b[1]);
        int[][] dp = new int[m+1][n+1];
        dp[0][0] = 1;
        int index = 0;
        for (int j = 1; j <= n; ++j) {
            if (index < points.length && j == points[index][1]+1) {
                int x = points[index][0]+1;
                int y = points[index][1]+1;
                dp[x][y] = dp[x-1][y-1] + dp[x][y-1];
                if (x < m - 1) dp[x][y] += dp[x+1][y-1];
                ++index;
                continue;
            }
            for (int i = 1; i < m; ++i) {
                dp[i][j] = dp[i-1][j-1] + dp[i][j-1] + dp[i+1][j-1];
            }
            dp[m][j] = dp[m-1][j-1] + dp[m][j-1];
        }
        return dp[m][n];
    }
}
```

**Follow up 3**

Give a lower bound x == H, find all path that go through the lower bound(x >= H)

```
input: m = 3, n = 7, H = 2
output:
```

Idea:

First use the normal method to do the traverse and calculate the number, then modify the number to 0 if X < H. Then we traverse from x == H, and calculate the answer.

Solution:

```
class Solution {
    public int uniquePaths(int m, int n, int H) {
        
    }
}
```

**Follow up 4**

A robot is located at the top-left corner of a *m* x *n* grid.

The robot can only move either down or bottom left or bottom right at any point in time. The robot is trying to reach the bottom-left corner of the grid.

How many possible unique paths are there?

```
input: m = 3, n = 7
output: 2
```

Idea:

It’s basicly the same idea just we need to calculate the top level first. So the traversal order should from top to bottom, then from left to right.

Solution:

```java
class Solution {
    public int uniquePaths(int m, int n) {
        if (m < 1 || n < 1) return 0;
        int[][] dp = new int[m+1][n+1];
        dp[0][0] = 1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j] + dp[i-1][j+1];
            }
            dp[i][n] = dp[i-1][n-1] + dp[i-1][n];
        }
        return dp[m][1];
    }
}
```

