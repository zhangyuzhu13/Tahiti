title: LeetCode Note Oct-2
date: 2018-10-10 14:16:00
categories: Algorithm

---



### 473. Matchsticks to Square

1. DFS, use a sum array to count the sum of each. Make sure you start visite from the big end, because when little number left, they could be composed to a big number, but when big number left, they could not. 

2. ```java
   class Solution {
       public boolean makesquare(int[] nums) {
           if (nums == null || nums.length < 4) {
               return false;
           }
           int len = 0;
           for (int num: nums) {
               len += num;
           }
           if (len % 4 != 0) {
               return false;
           }
           Arrays.sort(nums);
           return find(nums, new int[4], nums.length-1, len / 4);
       }
       private boolean find(int[] nums, int[] sum, int index, int target) {
           if (index < 0) {
               for (int i = 0; i < 4; i++) {
                   if (sum[i] != target) {
                       return false;
                   }
               }
               return true;
           }
           for (int i = 0; i < 4; i++) {
               if (sum[i] + nums[index] > target) {
                   continue;
               }
               sum[i] += nums[index];
               if (find(nums, sum, index-1, target)) {
                   return true;
               }
               sum[i] -= nums[index];
           }
           return false;
       }
   }
   ```

