title: LeetCode Note Oct-1
date: 2018-10-3 14:16:00
categories: Algorithm

---



### 4. Median of Two Sorted Arrays

1. First we need to make sure what is the use of median. there are two features: *1. the numbers before median are all less than or equal to the numbers after the median. 2. the amount before median are equal to the amount after median.* So we need to satisfy the two features for searching the median. There are two array A[] and B[], which length are m and n. So for i-th in A and j-th in B, we need to make sure that : `i + j == m - i + n - j ` and `A[i-1] < B[j] and B[j-1] < A[i] ` that's some kind we split the two array into four parts, leftA +leftB are amount equal to rightA + rightB. We could just binary search A using i, j should be equal to `(m+n+1)/2 - i`.  We need to keep j positive so m should < n. Also we need to make the medium as the next one in the two so every time times 2, we need first +1. The final numbers we care about is just A[i-1], A[i], B[j-1], B[j]. Sometimes some of them are not appeared, then we don't need to check them. 

2. ```java
   class Solution {
       public double findMedianSortedArrays(int[] nums1, int[] nums2) {
           if (nums1.length > nums2.length) {
               int[] temp = nums1;
               nums1 = nums2;
               nums2 = temp;
           }
           int len = nums1.length + nums2.length;
           int left1 = 0, left2 = 0, right1 = nums1.length, right2 = nums2.length;
           while (left1 <= right1) {
               int mid1 = (right1 - left1) / 2 + left1;
               int mid2 = (len + 1) / 2 - mid1;
               if (mid1 < right1 && nums1[mid1] < nums2[mid2-1]) {
                   left1 = mid1 + 1;
               } else if (mid1 > left1 && nums1[mid1-1] > nums2[mid2]) {
                   right1 = mid1 - 1;
               } else {
                   int leftMax = 0;
                   if (mid1 == 0) {
                       leftMax = nums2[mid2-1];
                   } else if (mid2 == 0) {
                       leftMax = nums1[mid1-1];
                   } else {
                       leftMax = Math.max(nums1[mid1-1], nums2[mid2-1]);
                   }
                   if (len % 2 == 1) {
                       return leftMax;
                   }
                   
                   int rightMin = 0;
                   if (mid1 == nums1.length) {
                       rightMin = nums2[mid2];
                   } else if (mid2 == nums2.length) {
                       rightMin = nums1[mid1];
                   } else {
                       rightMin = Math.min(nums2[mid2], nums1[mid1]);
                   }
                   return (leftMax + rightMin) / 2.0;
               }
               
           }
           return 0.0;
       }
   }
   ```

2. Optimsize the step. We could add the next node into the first place to implement the reverse features.

3. ```java
   /**
    * Definition for singly-linked list.
    * public class ListNode {
    *     int val;
    *     ListNode next;
    *     ListNode(int x) { val = x; }
    * }
    */
   class Solution {
       public ListNode reverseBetween(ListNode head, int m, int n) {
           if (head == null || head.next == null) {
               return head;
           }
           ListNode dummy = new ListNode(0);
           dummy.next = head;
           ListNode prev = dummy;
           for (int i = 1; i < m; i++) {
               prev = prev.next;
           }
           ListNode reHead = prev, reTail = prev.next; 
           ListNode  next, curr = reTail.next;
           for (int i = 0; i < n-m && curr != null; i++) {
               next = curr.next;
               curr.next = reHead.next;
               reHead.next = curr;
               curr = next;
           }
           reTail.next = curr;
           return dummy.next;
       }
   }
   ```



### 131. Palindrome Partitioning

1. For find the real final result, most time we need to use dfs which is backtracking to get the result.

2. ```java
   class Solution {
       public List<List<String>> partition(String s) {
           int len = s.length();
           List<List<String>> result = new ArrayList<>();
           List<String> curr = new ArrayList<>();
           dfs(result, curr, s, 0);
           return result;
       }
       private void dfs(List<List<String>> result, List<String> curr, String s, int index) {
           if (index == s.length()) {
               result.add(new ArrayList<>(curr));
               return;
           }
           char[] chars = s.toCharArray();
           for (int i = index; i < chars.length; i++) {
               int left = index, right = i;
               while(left < right && chars[left] == chars[right]) { 
                   left++;
                   right--;
               }
               if (left >= right) {
                   curr.add(s.substring(index,i+1));
                   dfs(result, curr, s, i+1);
                   curr.remove(curr.size()-1);
               }
           }
       }
   }
   ```


