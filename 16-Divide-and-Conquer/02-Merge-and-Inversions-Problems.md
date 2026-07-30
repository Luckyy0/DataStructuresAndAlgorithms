# 02. Merge and Inversions - Problems (Bài tập thực hành)

Tài liệu này bao gồm 30 bài tập về kỹ thuật Merge and Inversions (Trộn và đếm). 10 bài đầu tiên cung cấp chi tiết đề bài, phân tích, mã nguồn Java và độ phức tạp. Các bài từ 11-30 được tóm tắt.

## 1. Count of Smaller Numbers After Self (LeetCode 315)
**Đề bài chi tiết:** Cho một mảng nguyên `nums`. Trả về một mảng `counts` mới trong đó `counts[i]` là số lượng phần tử nhỏ hơn `nums[i]` nằm về phía bên phải của `nums[i]`.
**Phân tích thuật toán:** Sử dụng Merge Sort. Mảng ban đầu được gắn với chỉ số index để theo dõi vị trí gốc của mỗi phần tử trong quá trình phân chia. Khi gộp (merge) nửa trái và nửa phải (đã sắp xếp), đối với mỗi phần tử ở nửa trái, số phần tử ở nửa phải nhỏ hơn nó chính là số lượng phần tử nửa phải đã được đẩy vào mảng gộp trước nó.
**Mã nguồn Java:**
```java
import java.util.*;

public class CountSmallerAfterSelf {
    class Item {
        int val, index;
        public Item(int val, int index) {
            this.val = val; this.index = index;
        }
    }
    
    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        Item[] items = new Item[n];
        for (int i = 0; i < n; i++) items[i] = new Item(nums[i], i);
        int[] counts = new int[n];
        mergeSort(items, 0, n - 1, counts);
        List<Integer> res = new ArrayList<>();
        for (int i : counts) res.add(i);
        return res;
    }
    
    private void mergeSort(Item[] items, int left, int right, int[] counts) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(items, left, mid, counts);
        mergeSort(items, mid + 1, right, counts);
        merge(items, left, mid, right, counts);
    }
    
    private void merge(Item[] items, int left, int mid, int right, int[] counts) {
        Item[] temp = new Item[right - left + 1];
        int i = left, j = mid + 1, k = 0, rightCounter = 0;
        
        while (i <= mid && j <= right) {
            if (items[i].val <= items[j].val) {
                counts[items[i].index] += rightCounter;
                temp[k++] = items[i++];
            } else {
                rightCounter++;
                temp[k++] = items[j++];
            }
        }
        while (i <= mid) {
            counts[items[i].index] += rightCounter;
            temp[k++] = items[i++];
        }
        while (j <= right) {
            temp[k++] = items[j++];
        }
        for (int p = 0; p < temp.length; p++) {
            items[left + p] = temp[p];
        }
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 2. Reverse Pairs (LeetCode 493)
**Đề bài chi tiết:** Cho một mảng `nums`. Một reverse pair (nghịch thế đảo) là một cặp `(i, j)` thỏa mãn `i < j` và `nums[i] > 2 * nums[j]`. Đếm số lượng reverse pairs.
**Phân tích thuật toán:** Tương tự Inversion Counting. Tuy nhiên, điều kiện `nums[i] > 2 * nums[j]` không trực tiếp ảnh hưởng đến quá trình gộp bình thường. Do đó, ta duyệt qua nửa trái và nửa phải để đếm trước, sau đó mới thực hiện bước merge chuẩn của Merge Sort.
**Mã nguồn Java:**
```java
public class ReversePairs {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        return mergeSort(nums, 0, nums.length - 1);
    }
    
    private int mergeSort(int[] nums, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, left, mid) + mergeSort(nums, mid + 1, right);
        
        // Đếm reverse pairs
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && nums[i] / 2.0 > nums[j]) {
                j++;
            }
            count += j - (mid + 1);
        }
        
        // Trộn 2 mảng
        merge(nums, left, mid, right);
        return count;
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[k++] = nums[i++];
            else temp[k++] = nums[j++];
        }
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        for (int p = 0; p < temp.length; p++) {
            nums[left + p] = temp[p];
        }
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 3. Global and Local Inversions (LeetCode 775)
**Đề bài chi tiết:** Một Global Inversion (Nghịch thế toàn cục) là số cặp `i < j` sao cho `A[i] > A[j]`. Một Local Inversion (Nghịch thế cục bộ) là số cặp `A[i] > A[i+1]`. Trả về true nếu số lượng 2 loại này bằng nhau.
**Phân tích thuật toán:** Bài này không nhất thiết phải đếm toàn bộ, vì mọi Local đều là Global. Để hai số bằng nhau, mảng không được tồn tại cặp Global nào mà không phải Local (tức là không được có `A[i] > A[j]` với `j > i + 1`). Ta chỉ cần kiểm tra `max(A[0..i-2]) > A[i]` trong mảng.
**Mã nguồn Java:**
```java
public class GlobalAndLocalInversions {
    public boolean isIdealPermutation(int[] nums) {
        int max = -1;
        for (int i = 0; i < nums.length - 2; i++) {
            max = Math.max(max, nums[i]);
            if (max > nums[i + 2]) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(1)$. (Dù là bài đếm nghịch thế, cách tối ưu nhất lại là phân tích tính chất).

## 4. Count of Range Sum (LeetCode 327)
**Đề bài chi tiết:** Cho một mảng `nums` nguyên. Trả về số lượng mảng con có tổng nằm trong khoảng `[lower, upper]`.
**Phân tích thuật toán:** Tính mảng prefix sum trước. Tổng con `nums[i..j] = prefix[j+1] - prefix[i]`. Điều kiện trở thành `lower <= prefix[j+1] - prefix[i] <= upper`. Dùng kỹ thuật chia để trị để đếm chéo qua 2 mảng con `left` và `right` của prefix sum đã sắp xếp, bằng kỹ thuật 2 con trỏ.
**Mã nguồn Java:**
```java
public class CountRangeSum {
    public int countRangeSum(int[] nums, int lower, int upper) {
        long[] prefix = new long[nums.length + 1];
        for (int i = 0; i < nums.length; i++) prefix[i + 1] = prefix[i] + nums[i];
        return mergeSort(prefix, 0, prefix.length - 1, lower, upper);
    }
    
    private int mergeSort(long[] sum, int left, int right, int lower, int upper) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(sum, left, mid, lower, upper) + mergeSort(sum, mid + 1, right, lower, upper);
        
        int j = mid + 1, k = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && sum[j] - sum[i] < lower) j++;
            while (k <= right && sum[k] - sum[i] <= upper) k++;
            count += k - j;
        }
        
        merge(sum, left, mid, right);
        return count;
    }
    
    private void merge(long[] sum, int left, int mid, int right) {
        long[] temp = new long[right - left + 1];
        int i = left, j = mid + 1, t = 0;
        while (i <= mid && j <= right) {
            if (sum[i] <= sum[j]) temp[t++] = sum[i++];
            else temp[t++] = sum[j++];
        }
        while (i <= mid) temp[t++] = sum[i++];
        while (j <= right) temp[t++] = sum[j++];
        System.arraycopy(temp, 0, sum, left, temp.length);
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 5. Create Sorted Array through Instructions (LeetCode 1649)
**Đề bài chi tiết:** Với mỗi phần tử chèn vào mảng sắp xếp từ `instructions`, chi phí là `min(số phần tử strictly less, số phần tử strictly greater)`. Trả về tổng chi phí lấy dư cho $10^9+7$.
**Phân tích thuật toán:** Đây là bài toán có thể giải bằng Merge Sort (đếm số lượng phần tử nhỏ hơn và đếm số lượng phần tử lớn hơn hiện đang nằm bên trái) hoặc BIT/Segment Tree. Ở đây cấu trúc Fenwick (BIT) thường dễ code hơn, nhưng biến thể Divide & Conquer cũng khả thi bằng cách đếm tương tự bài 1. Dưới đây biểu diễn cách dùng BIT vì nó hiệu quả cho bài này.
**Mã nguồn Java:**
```java
public class CreateSortedArray {
    int[] bit;
    public int createSortedArray(int[] instructions) {
        int max = 0;
        for (int x : instructions) max = Math.max(max, x);
        bit = new int[max + 1];
        
        long cost = 0;
        int mod = 1000000007;
        for (int i = 0; i < instructions.length; i++) {
            int x = instructions[i];
            int less = query(x - 1);
            int greater = i - query(x);
            cost = (cost + Math.min(less, greater)) % mod;
            update(x, 1, max);
        }
        return (int) cost;
    }
    
    private void update(int x, int val, int max) {
        for (; x <= max; x += x & -x) bit[x] += val;
    }
    
    private int query(int x) {
        int sum = 0;
        for (; x > 0; x -= x & -x) sum += bit[x];
        return sum;
    }
}
```
**Độ phức tạp:** Time: $O(N \log M)$ ($M$ là giá trị lớn nhất), Space: $O(M)$.

## 6. Merge k Sorted Lists (LeetCode 23)
**Đề bài chi tiết:** Trộn `k` danh sách liên kết đã sắp xếp thành 1 danh sách đã sắp xếp.
**Phân tích thuật toán:** Sử dụng Divide and Conquer. Trộn đệ quy hai nửa danh sách. Tận dụng `merge2Lists`.
**Mã nguồn Java:**
```java
public class MergeKSortedLists {
    public class ListNode {
        int val; ListNode next;
        ListNode(int x) { val = x; }
    }
    
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        return merge(lists, 0, lists.length - 1);
    }
    
    private ListNode merge(ListNode[] lists, int left, int right) {
        if (left == right) return lists[left];
        int mid = left + (right - left) / 2;
        ListNode l1 = merge(lists, left, mid);
        ListNode l2 = merge(lists, mid + 1, right);
        return merge2Lists(l1, l2);
    }
    
    private ListNode merge2Lists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) { curr.next = l1; l1 = l1.next; } 
            else { curr.next = l2; l2 = l2.next; }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```
**Độ phức tạp:** Time: $O(N \log K)$ với $N$ là tổng số node, Space: $O(\log K)$ cho call stack.

## 7. Sort an Array (LeetCode 912)
**Đề bài chi tiết:** Sắp xếp một mảng (Sử dụng Merge Sort cơ bản).
**Phân tích thuật toán:** Bài thực hành tiêu chuẩn để cài đặt Merge Sort với $O(N \log N)$.
**Mã nguồn Java:**
```java
public class SortArray {
    public int[] sortArray(int[] nums) {
        int[] temp = new int[nums.length];
        mergeSort(nums, temp, 0, nums.length - 1);
        return nums;
    }
    
    private void mergeSort(int[] nums, int[] temp, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(nums, temp, left, mid);
        mergeSort(nums, temp, mid + 1, right);
        
        int i = left, j = mid + 1, k = left;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[k++] = nums[i++];
            else temp[k++] = nums[j++];
        }
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        for (int p = left; p <= right; p++) nums[p] = temp[p];
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 8. Sort List (LeetCode 148)
**Đề bài chi tiết:** Sắp xếp một Linked List với thời gian $O(N \log N)$ và không gian $O(1)$ (nếu dùng bottom-up) hoặc $O(\log N)$ đệ quy.
**Phân tích thuật toán:** Tìm `mid` bằng Fast/Slow pointer, tách thành 2 List, đệ quy sắp xếp và `Merge 2 Sorted Lists`.
**Mã nguồn Java:**
```java
public class SortList {
    public class ListNode {
        int val; ListNode next;
        ListNode(int x) { val = x; }
    }
    
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode mid = getMid(head);
        ListNode left = sortList(head);
        ListNode right = sortList(mid);
        return merge(left, right);
    }
    
    private ListNode getMid(ListNode head) {
        ListNode slow = head, fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode mid = slow.next;
        slow.next = null;
        return mid;
    }
    
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) { tail.next = l1; l1 = l1.next; }
            else { tail.next = l2; l2 = l2.next; }
            tail = tail.next;
        }
        tail.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(\log N)$ trên memory stack đệ quy.

## 9. Number of Ships in a Rectangle (LeetCode 1274)
**Đề bài chi tiết:** (Interactive problem) Bạn có API `hasShips(topRight, bottomLeft)`. Đếm tổng số thuyền, biết max 10 thuyền.
**Phân tích thuật toán:** Sử dụng Quad-Tree Divide and Conquer (chia 4 chiều trên ma trận). Giống với Merge (trộn kết quả của các vùng miền). Nếu API trả về false, không có thuyền, ngắt.
**Mã nguồn Java:**
```java
public class NumberOfShips {
    interface Sea {
        boolean hasShips(int[] topRight, int[] bottomLeft);
    }
    
    public int countShips(Sea sea, int[] topRight, int[] bottomLeft) {
        if (bottomLeft[0] > topRight[0] || bottomLeft[1] > topRight[1]) return 0;
        if (!sea.hasShips(topRight, bottomLeft)) return 0;
        
        if (topRight[0] == bottomLeft[0] && topRight[1] == bottomLeft[1]) return 1;
        
        int midX = bottomLeft[0] + (topRight[0] - bottomLeft[0]) / 2;
        int midY = bottomLeft[1] + (topRight[1] - bottomLeft[1]) / 2;
        
        return countShips(sea, new int[]{midX, midY}, bottomLeft) +
               countShips(sea, topRight, new int[]{midX + 1, midY + 1}) +
               countShips(sea, new int[]{midX, topRight[1]}, new int[]{bottomLeft[0], midY + 1}) +
               countShips(sea, new int[]{topRight[0], midY}, new int[]{midX + 1, bottomLeft[1]});
    }
}
```
**Độ phức tạp:** Time: Tùy thuộc vào số thuyền phân bố, Worst Case $O(K \log(L))$ với $K \le 10$, Space: $O(\log(\text{Max coordinate}))$.

## 10. Kth Smallest Element in a Sorted Matrix (LeetCode 378)
**Đề bài chi tiết:** Tìm phần tử nhỏ thứ `k` trong ma trận vuông $N \times N$, trong đó các hàng và cột đều đã được sắp xếp tăng dần.
**Phân tích thuật toán:** Một góc nhìn từ Divide & Conquer là dùng Binary Search theo Value (chọn mid = (min + max)/2), sau đó đếm số lượng phần tử nhỏ hơn `mid`. Hàm đếm có hình dáng giống như việc chạy chéo (cross-boundary count) từ góc trái dưới lên góc phải trên.
**Mã nguồn Java:**
```java
public class KthSmallestSortedMatrix {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0], right = matrix[n-1][n-1];
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            int count = countLessOrEqual(matrix, mid, n);
            if (count < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
    
    private int countLessOrEqual(int[][] matrix, int target, int n) {
        int count = 0;
        int r = n - 1, c = 0;
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= target) {
                count += r + 1;
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time: $O(N \log(\text{Max} - \text{Min}))$, Space: $O(1)$.

---

## 11. Wiggle Sort II (LeetCode 324)
**Đề bài chi tiết:** Cho một mảng nguyên `nums`, sắp xếp lại nó theo thứ tự `nums[0] < nums[1] > nums[2] < nums[3]...`.
**Phân tích thuật toán:** Có thể giải quyết bài toán bằng cách tìm trung vị (median) rồi dùng kỹ thuật phân hoạch 3 phần (3-way partitioning). Một cách tiếp cận đơn giản hơn là sắp xếp mảng trước, sau đó chèn nửa mảng chứa các giá trị lớn hơn vào các vị trí lẻ và nửa mảng chứa các giá trị nhỏ hơn vào các vị trí chẵn.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class WiggleSortII {
    public void wiggleSort(int[] nums) {
        int[] copy = Arrays.copyOf(nums, nums.length);
        Arrays.sort(copy);
        int n = nums.length;
        int left = (n + 1) / 2 - 1;
        int right = n - 1;
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                nums[i] = copy[left--];
            } else {
                nums[i] = copy[right--];
            }
        }
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$ cho thao tác sắp xếp (có thể tối ưu xuống $O(N)$ bằng Quick Select), Space: $O(N)$.

## 12. Longest Substring with At Least K Repeating Characters (LeetCode 395)
**Đề bài chi tiết:** Cho một chuỗi `s` và số nguyên `k`, tìm độ dài chuỗi con dài nhất của `s` sao cho mỗi ký tự trong chuỗi con đó xuất hiện ít nhất `k` lần.
**Phân tích thuật toán:** Sử dụng Divide and Conquer. Đếm tần suất xuất hiện của các ký tự trong chuỗi. Nếu có một ký tự xuất hiện ít hơn `k` lần, ký tự đó không thể thuộc về chuỗi con thoả mãn yêu cầu. Ta chia chuỗi tại các vị trí xuất hiện của ký tự này và gọi đệ quy trên các chuỗi con để tìm độ dài lớn nhất.
**Mã nguồn Java:**
```java
public class LongestSubstringKRepeating {
    public int longestSubstring(String s, int k) {
        if (s == null || s.length() == 0) return 0;
        return helper(s.toCharArray(), 0, s.length(), k);
    }
    
    private int helper(char[] chs, int start, int end, int k) {
        if (end - start < k) return 0;
        int[] count = new int[26];
        for (int i = start; i < end; i++) {
            count[chs[i] - 'a']++;
        }
        for (int i = start; i < end; i++) {
            if (count[chs[i] - 'a'] < k) {
                int left = helper(chs, start, i, k);
                int right = helper(chs, i + 1, end, k);
                return Math.max(left, right);
            }
        }
        return end - start;
    }
}
```
**Độ phức tạp:** Time: $O(N^2)$ trong trường hợp xấu nhất (mỗi lần cắt bỏ 1 ký tự), Space: $O(N)$ cho call stack đệ quy.

## 13. Maximum Subarray (LeetCode 53)
**Đề bài chi tiết:** Cho một mảng nguyên `nums`, tìm mảng con liên tiếp có tổng lớn nhất và trả về giá trị tổng đó.
**Phân tích thuật toán:** Mặc dù thuật toán Kadane ($O(N)$) là phổ biến nhất, bài toán cũng là bài tập tiêu chuẩn cho Divide and Conquer. Bằng cách chia mảng thành hai nửa, mảng con có tổng lớn nhất sẽ nằm ở: toàn bộ nửa trái, toàn bộ nửa phải, hoặc một mảng con bắt chéo qua điểm giữa của hai nửa.
**Mã nguồn Java:**
```java
public class MaximumSubarray {
    public int maxSubArray(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }
    
    private int helper(int[] nums, int left, int right) {
        if (left == right) return nums[left];
        
        int mid = left + (right - left) / 2;
        int leftSum = helper(nums, left, mid);
        int rightSum = helper(nums, mid + 1, right);
        int crossSum = crossSum(nums, left, right, mid);
        
        return Math.max(Math.max(leftSum, rightSum), crossSum);
    }
    
    private int crossSum(int[] nums, int left, int right, int mid) {
        if (left == right) return nums[left];
        
        int leftSubSum = Integer.MIN_VALUE, currSum = 0;
        for (int i = mid; i >= left; i--) {
            currSum += nums[i];
            leftSubSum = Math.max(leftSubSum, currSum);
        }
        
        int rightSubSum = Integer.MIN_VALUE; currSum = 0;
        for (int i = mid + 1; i <= right; i++) {
            currSum += nums[i];
            rightSubSum = Math.max(rightSubSum, currSum);
        }
        
        return leftSubSum + rightSubSum;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(\log N)$ trên stack đệ quy.

## 14. Different Ways to Add Parentheses (LeetCode 241)
**Đề bài chi tiết:** Cho một biểu thức chuỗi chứa các con số và phép toán `+`, `-`, `*`. Hãy tính tất cả các kết quả có thể nhận được từ việc nhóm các số và toán tử lại bằng ngoặc đơn.
**Phân tích thuật toán:** Áp dụng Divide and Conquer, với mỗi toán tử được tìm thấy trong chuỗi, ta phân tách chuỗi thành toán hạng trái và toán hạng phải. Gọi đệ quy giải cho từng bên, sau đó áp dụng toán tử hiện tại để ghép từng kết quả của bên trái với bên phải. Sử dụng Memoization (bảng băm) để tránh tính toán trùng lặp.
**Mã nguồn Java:**
```java
import java.util.*;

public class DifferentWaysToAddParentheses {
    Map<String, List<Integer>> memo = new HashMap<>();
    
    public List<Integer> diffWaysToCompute(String expression) {
        if (memo.containsKey(expression)) return memo.get(expression);
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                String leftPart = expression.substring(0, i);
                String rightPart = expression.substring(i + 1);
                
                List<Integer> leftRes = diffWaysToCompute(leftPart);
                List<Integer> rightRes = diffWaysToCompute(rightPart);
                
                for (int x : leftRes) {
                    for (int y : rightRes) {
                        if (c == '+') res.add(x + y);
                        else if (c == '-') res.add(x - y);
                        else res.add(x * y);
                    }
                }
            }
        }
        
        if (res.isEmpty()) { // Không chứa phép tính nào, chuỗi là một con số
            res.add(Integer.parseInt(expression));
        }
        
        memo.put(expression, res);
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(C_n)$ với $C_n$ là số Catalan ứng với số phép tính, Space: $O(C_n)$ để lưu trữ danh sách kết quả.

## 15. Burst Balloons (LeetCode 312)
**Đề bài chi tiết:** Cho `n` quả bóng bay, mỗi quả được gán một điểm số `nums[i]`. Khi làm nổ bóng `i`, bạn nhận được `nums[i - 1] * nums[i] * nums[i + 1]` điểm. Tính số điểm lớn nhất nhận được nếu bạn lần lượt bắn nổ toàn bộ bóng. (Bóng ngoài biên luôn có giá trị 1).
**Phân tích thuật toán:** Bài toán này là sự kết hợp giữa Divide & Conquer và Quy hoạch động. Thay vì suy nghĩ xem bóng nào nổ đầu tiên (làm phá vỡ mảng), ta chọn bóng nổ **cuối cùng** trong đoạn `(left, right)`. Việc bóng nổ cuối cùng giúp bài toán được phân rã thành hai bài toán con độc lập không ảnh hưởng lẫn nhau.
**Mã nguồn Java:**
```java
public class BurstBalloons {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int[] arr = new int[n + 2];
        arr[0] = 1; arr[n + 1] = 1;
        for (int i = 0; i < n; i++) arr[i + 1] = nums[i];
        
        int[][] memo = new int[n + 2][n + 2];
        return dfs(arr, 0, n + 1, memo);
    }
    
    private int dfs(int[] arr, int left, int right, int[][] memo) {
        if (left + 1 == right) return 0;
        if (memo[left][right] > 0) return memo[left][right];
        
        int max = 0;
        for (int i = left + 1; i < right; i++) {
            // i là bóng nổ cuối cùng trong khoảng (left, right)
            int coins = arr[left] * arr[i] * arr[right];
            coins += dfs(arr, left, i, memo) + dfs(arr, i, right, memo);
            max = Math.max(max, coins);
        }
        
        memo[left][right] = max;
        return max;
    }
}
```
**Độ phức tạp:** Time: $O(N^3)$ cho các vòng lặp đệ quy trên từng đoạn mảng, Space: $O(N^2)$ để lưu memoization.

## 16. Expression Add Operators (LeetCode 282)
**Đề bài chi tiết:** Cho chuỗi `num` chứa các chữ số và số nguyên `target`. Trả về tất cả các biểu thức hợp lệ bằng cách chèn `+`, `-`, `*` vào giữa các chữ số sao cho tổng giá trị bằng `target`.
**Phân tích thuật toán:** Áp dụng Backtracking sinh tổ hợp (cũng là một luồng xử lý Divide and Conquer cho chuỗi). Thách thức của bài này là toán tử nhân `*` có độ ưu tiên cao hơn `+` và `-`. Vì thế khi gặp `*`, ta cần khôi phục lại giá trị của biểu thức ngay trước đó bằng cách trừ đi hạng tử cũ, và cộng vào tích của hạng tử cũ với số hiện tại.
**Mã nguồn Java:**
```java
import java.util.*;

public class ExpressionAddOperators {
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<>();
        if (num == null || num.length() == 0) return res;
        backtrack(res, "", num, target, 0, 0, 0);
        return res;
    }
    
    private void backtrack(List<String> res, String path, String num, int target, int pos, long eval, long multed) {
        if (pos == num.length()) {
            if (eval == target) res.add(path);
            return;
        }
        for (int i = pos; i < num.length(); i++) {
            if (i != pos && num.charAt(pos) == '0') break; // Bỏ qua số 0 ở đầu
            long cur = Long.parseLong(num.substring(pos, i + 1));
            if (pos == 0) {
                backtrack(res, path + cur, num, target, i + 1, cur, cur);
            } else {
                backtrack(res, path + "+" + cur, num, target, i + 1, eval + cur, cur);
                backtrack(res, path + "-" + cur, num, target, i + 1, eval - cur, -cur);
                backtrack(res, path + "*" + cur, num, target, i + 1, eval - multed + multed * cur, multed * cur);
            }
        }
    }
}
```
**Độ phức tạp:** Time: $O(4^N)$ vì mỗi vị trí chèn ta có 4 cách (không chèn, `+`, `-`, `*`), Space: $O(N)$ lưu vết.

## 17. Super Egg Drop (LeetCode 887)
**Đề bài chi tiết:** Bạn có `k` quả trứng và toà nhà `n` tầng. Hãy xác định số lần thử tối thiểu trong trường hợp tồi tệ nhất (worst-case) để tìm ra tầng an toàn cao nhất mà quả trứng thả không bị vỡ.
**Phân tích thuật toán:** Bài toán quy hoạch động kinh điển, nhưng độ phức tạp ngây thơ là $O(K \cdot N^2)$. Dựa vào tính đơn điệu của hàm trạng thái, ta có thể tối ưu việc tìm tầng chia đệ quy tốt nhất `X` bằng kỹ thuật Divide and Conquer kết hợp Binary Search, giảm thời gian tra cứu từ $O(N)$ xuống $O(\log N)$.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class SuperEggDrop {
    int[][] memo;
    public int superEggDrop(int k, int n) {
        memo = new int[k + 1][n + 1];
        for (int[] row : memo) Arrays.fill(row, -1);
        return dp(k, n);
    }
    
    private int dp(int k, int n) {
        if (k == 1) return n;
        if (n == 0) return 0;
        if (memo[k][n] != -1) return memo[k][n];
        
        int left = 1, right = n, res = n;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int broken = dp(k - 1, mid - 1);
            int notBroken = dp(k, n - mid);
            
            if (broken > notBroken) {
                right = mid - 1;
                res = Math.min(res, broken + 1);
            } else {
                left = mid + 1;
                res = Math.min(res, notBroken + 1);
            }
        }
        
        return memo[k][n] = res;
    }
}
```
**Độ phức tạp:** Time: $O(K \cdot N \log N)$, Space: $O(K \cdot N)$ để chứa DP array.

## 18. Find the Kth Largest Integer in the Array (LeetCode 215)
**Đề bài chi tiết:** Tìm phần tử lớn thứ `k` trong một mảng số nguyên không được sắp xếp.
**Phân tích thuật toán:** Quick Select là một thuật toán thuộc họ Divide and Conquer. Thuật toán chọn một điểm neo `pivot` rồi phân chia mảng thành phần nhỏ hơn và phần lớn hơn. Khác với Quick Sort (gọi đệ quy cả hai nửa), Quick Select chỉ gọi đệ quy một nửa mảng mà ta biết chắc chứa phần tử lớn thứ `k`, đưa thời gian trung bình xuống $O(N)$.
**Mã nguồn Java:**
```java
import java.util.Random;

public class KthLargestElement {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, nums.length - 1, nums.length - k);
    }
    
    private int quickSelect(int[] nums, int left, int right, int kSmallest) {
        if (left == right) return nums[left];
        Random rand = new Random();
        int pivotIndex = left + rand.nextInt(right - left + 1);
        pivotIndex = partition(nums, left, right, pivotIndex);
        
        if (kSmallest == pivotIndex) {
            return nums[kSmallest];
        } else if (kSmallest < pivotIndex) {
            return quickSelect(nums, left, pivotIndex - 1, kSmallest);
        } else {
            return quickSelect(nums, pivotIndex + 1, right, kSmallest);
        }
    }
    
    private int partition(int[] nums, int left, int right, int pivotIndex) {
        int pivot = nums[pivotIndex];
        swap(nums, pivotIndex, right);
        int storeIndex = left;
        for (int i = left; i <= right; i++) {
            if (nums[i] < pivot) {
                swap(nums, storeIndex, i);
                storeIndex++;
            }
        }
        swap(nums, storeIndex, right);
        return storeIndex;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:** Time: $O(N)$ trong trường hợp trung bình, $O(N^2)$ trong trường hợp xấu nhất, Space: $O(\log N)$ trên memory stack.

## 19. Sort Colors (LeetCode 75)
**Đề bài chi tiết:** Cho một mảng `nums` gồm các giá trị 0, 1, 2. Sắp xếp lại tại chỗ (in-place) sao cho các giá trị cùng loại đứng cạnh nhau, theo thứ tự từ bé đến lớn.
**Phân tích thuật toán:** Ứng dụng bài toán quốc kỳ Hà Lan (Dutch National Flag) của Dijkstra, cũng là cốt lõi của quá trình 3-way Partition trong Quick Sort nâng cao. Ý tưởng là chia không gian thành 3 vùng và gán các con trỏ tương ứng chạy đan chéo nhau, đổi chỗ các phần tử sai vị trí để dần lấp đầy các vùng.
**Mã nguồn Java:**
```java
public class SortColors {
    public void sortColors(int[] nums) {
        int p0 = 0, curr = 0;
        int p2 = nums.length - 1;
        
        while (curr <= p2) {
            if (nums[curr] == 0) {
                swap(nums, curr++, p0++);
            } else if (nums[curr] == 2) {
                swap(nums, curr, p2--); // Sau khi đổi với p2, không tăng curr ngay vì nums[curr] mới có thể là 0 hoặc 2
            } else {
                curr++;
            }
        }
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(1)$.

## 20. Search a 2D Matrix II (LeetCode 240)
**Đề bài chi tiết:** Tìm kiếm một giá trị `target` trong một ma trận 2D kích thước $M \times N$, mà cả hàng và cột đều được sắp xếp theo thứ tự tăng dần. Trả về `true` nếu tìm thấy.
**Phân tích thuật toán:** Mặc dù bài toán có thể giải quyết bằng không gian Quad-Tree Divide & Conquer, một hướng tiếp cận chia để trị tối ưu trên ma trận là thuật toán Search-Space Reduction. Khởi tạo ở đỉnh trên cùng bên phải. Nếu phần tử lớn hơn `target`, ta loại toàn bộ cột. Nhỏ hơn `target`, ta loại toàn bộ hàng. Điều này liên tục "cắt nhỏ" hình chữ nhật không gian tìm kiếm mỗi lần.
**Mã nguồn Java:**
```java
public class Search2DMatrixII {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        int row = 0, col = matrix[0].length - 1;
        
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--; // Loại bỏ cột hiện tại vì toàn bộ cột phía dưới lớn hơn
            } else {
                row++; // Loại bỏ hàng hiện tại vì toàn bộ hàng phía trước nhỏ hơn
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Time: $O(M + N)$, Space: $O(1)$.

---

## 21. Median of Two Sorted Arrays (LeetCode 4)
**Đề bài chi tiết:** Cho hai mảng đã được sắp xếp `nums1` và `nums2` có kích thước lần lượt là `m` và `n`. Trả về trung vị (median) của hai mảng này. Độ phức tạp tổng thời gian phải là $O(\log(m+n))$.
**Phân tích thuật toán:** Sử dụng Divide & Conquer (tìm kiếm nhị phân). Ta chia mảng có kích thước nhỏ hơn để tìm vách ngăn (partition). Bằng cách so sánh các phần tử kề vách ngăn của hai mảng, ta thu hẹp không gian tìm kiếm làm đôi mỗi lần cho đến khi tìm được vách ngăn đúng, chia tổng hai mảng thành 2 phần bằng nhau về số lượng phần tử, thỏa mãn nửa trái luôn nhỏ hơn hoặc bằng nửa phải.
**Mã nguồn Java:**
```java
public class MedianOfTwoSortedArrays {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }
        int x = nums1.length, y = nums2.length;
        int low = 0, high = x;
        
        while (low <= high) {
            int partitionX = (low + high) / 2;
            int partitionY = (x + y + 1) / 2 - partitionX;
            
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minRightX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
            
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minRightY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];
            
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                if ((x + y) % 2 == 0) {
                    return ((double)Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2;
                } else {
                    return (double)Math.max(maxLeftX, maxLeftY);
                }
            } else if (maxLeftX > minRightY) {
                high = partitionX - 1;
            } else {
                low = partitionX + 1;
            }
        }
        return 0.0;
    }
}
```
**Độ phức tạp:** Time: $O(\log(\min(M, N)))$, Space: $O(1)$.

## 22. Merge Intervals (LeetCode 56)
**Đề bài chi tiết:** Cho mảng các đoạn (intervals), ghép tất cả các đoạn có sự chồng chéo lại với nhau và trả về mảng các đoạn đã được gộp bao trùm toàn bộ không gian của khoảng ban đầu.
**Phân tích thuật toán:** Trước tiên sắp xếp các đoạn theo điểm bắt đầu. Mặc dù là bài toán dạng đường quét (Sweep Line), bên dưới nó vẫn là thao tác sắp xếp (Merge Sort nhánh D&C). Sau khi sắp xếp, khởi tạo một đoạn hiện tại, duyệt qua các đoạn, nếu đoạn tiếp theo đè lên đoạn hiện tại thì gộp lại bằng cách lấy `max` điểm kết thúc.
**Mã nguồn Java:**
```java
import java.util.*;

public class MergeIntervals {
    public int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) return intervals;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        
        List<int[]> res = new ArrayList<>();
        int[] current = intervals[0];
        res.add(current);
        
        for (int[] interval : intervals) {
            if (interval[0] <= current[1]) {
                current[1] = Math.max(current[1], interval[1]);
            } else {
                current = interval;
                res.add(current);
            }
        }
        
        return res.toArray(new int[res.size()][]);
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$ (do thao tác sắp xếp mảng), Space: $O(\log N)$ bộ nhớ bổ trợ cho QuickSort/MergeSort trong Java hoặc $O(N)$ lưu danh sách mảng trả về.

## 23. The Skyline Problem (LeetCode 218)
**Đề bài chi tiết:** Có nhiều toà nhà hình chữ nhật được biểu diễn bởi tọa độ `[left, right, height]`. Trả về đường viền (skyline) của tổng thể các toà nhà chiếu theo không gian 2D.
**Phân tích thuật toán:** Áp dụng Divide and Conquer theo khuôn mẫu của Merge Sort. Chia danh sách toà nhà làm hai nửa, giải đệ quy cho mỗi nửa để có đường viền con. Tại bước "Trộn" (Merge), ta duy trì độ cao lớn nhất của mảng trái và phải tại tọa độ `x` hiện tại, chèn vào kết quả mảng tổng hợp nếu độ cao `max` thay đổi.
**Mã nguồn Java:**
```java
import java.util.*;

public class SkylineProblem {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        if (buildings.length == 0) return new ArrayList<>();
        return mergeSkyline(buildings, 0, buildings.length - 1);
    }
    
    private List<List<Integer>> mergeSkyline(int[][] b, int left, int right) {
        List<List<Integer>> res = new ArrayList<>();
        if (left == right) {
            res.add(Arrays.asList(b[left][0], b[left][2]));
            res.add(Arrays.asList(b[left][1], 0));
            return res;
        }
        
        int mid = left + (right - left) / 2;
        List<List<Integer>> leftSkyline = mergeSkyline(b, left, mid);
        List<List<Integer>> rightSkyline = mergeSkyline(b, mid + 1, right);
        return merge(leftSkyline, rightSkyline);
    }
    
    private List<List<Integer>> merge(List<List<Integer>> l1, List<List<Integer>> l2) {
        List<List<Integer>> res = new ArrayList<>();
        int h1 = 0, h2 = 0;
        int i = 0, j = 0;
        
        while (i < l1.size() && j < l2.size()) {
            int x = 0, h = 0;
            if (l1.get(i).get(0) < l2.get(j).get(0)) {
                x = l1.get(i).get(0);
                h1 = l1.get(i).get(1);
                h = Math.max(h1, h2);
                i++;
            } else if (l1.get(i).get(0) > l2.get(j).get(0)) {
                x = l2.get(j).get(0);
                h2 = l2.get(j).get(1);
                h = Math.max(h1, h2);
                j++;
            } else {
                x = l1.get(i).get(0);
                h1 = l1.get(i).get(1);
                h2 = l2.get(j).get(1);
                h = Math.max(h1, h2);
                i++; j++;
            }
            if (res.isEmpty() || res.get(res.size() - 1).get(1) != h) {
                res.add(Arrays.asList(x, h));
            }
        }
        while (i < l1.size()) res.add(l1.get(i++));
        while (j < l2.size()) res.add(l2.get(j++));
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$ lưu tạm cấu trúc đường viền.

## 24. Number of Inversions in 1D Array (HackerRank)
**Đề bài chi tiết:** Đếm số nghịch thế `i < j` sao cho `arr[i] > arr[j]` trong một mảng. Chú ý số lượng nghịch thế có thể vượt qua giới hạn số nguyên 32-bit (int), bắt buộc phải dùng kiểu dữ liệu `long`.
**Phân tích thuật toán:** Bài toán Inversion counting thuần tuý sử dụng Merge Sort. Đếm số lượng phần tử nửa trái lớn hơn phần tử nửa phải trong khi gộp mảng. Nếu `arr[i] > arr[j]`, thì mọi phần tử từ `arr[i]` đến `arr[mid]` đều lớn hơn `arr[j]`.
**Mã nguồn Java:**
```java
public class InversionCountingHR {
    public long countInversions(int[] arr) {
        int[] temp = new int[arr.length];
        return mergeSort(arr, temp, 0, arr.length - 1);
    }
    
    private long mergeSort(int[] arr, int[] temp, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        long inv = 0;
        inv += mergeSort(arr, temp, left, mid);
        inv += mergeSort(arr, temp, mid + 1, right);
        inv += merge(arr, temp, left, mid, right);
        return inv;
    }
    
    private long merge(int[] arr, int[] temp, int left, int mid, int right) {
        int i = left, j = mid + 1, k = left;
        long count = 0;
        
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
                count += (mid - i + 1); // Phần tử arr[j] nhỏ hơn toàn bộ đoạn trái từ arr[i] đến arr[mid]
            }
        }
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= right) temp[k++] = arr[j++];
        for (i = left; i <= right; i++) arr[i] = temp[i];
        
        return count;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 25. Shuffling Parities (Codeforces)
**Đề bài chi tiết:** Cho một mảng, hãy đếm số lượng cặp nghịch thế (inversions) `i < j` sao cho `A[i] > A[j]` và `A[i]` có tính chẵn lẻ khác với `A[j]` (một số chẵn, một số lẻ).
**Phân tích thuật toán:** Một biến thể của Merge Sort. Khi trộn hai mảng con `leftArr` và `rightArr`, ta duy trì số lượng số chẵn và số lẻ đã đưa từ `rightArr` vào mảng kết quả `temp`. Khi lấy một phần tử từ `leftArr` xuống `temp`, phần tử đó đã nghịch thế với mọi phần tử của `rightArr` đã được duyệt trước đó, ta chỉ cần cộng vào bộ đếm chẵn lẻ tương ứng.
**Mã nguồn Java:**
```java
public class ParityInversions {
    public long countParityInversions(int[] arr) {
        int[] temp = new int[arr.length];
        return mergeSort(arr, temp, 0, arr.length - 1);
    }
    
    private long mergeSort(int[] arr, int[] temp, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        long ans = mergeSort(arr, temp, left, mid) + mergeSort(arr, temp, mid + 1, right);
        
        int i = left, j = mid + 1, k = left;
        int oddsFromRight = 0, evensFromRight = 0;
        
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                if (arr[i] % 2 == 0) ans += oddsFromRight;
                else ans += evensFromRight;
                temp[k++] = arr[i++];
            } else {
                if (arr[j] % 2 == 0) evensFromRight++;
                else oddsFromRight++;
                temp[k++] = arr[j++];
            }
        }
        while (i <= mid) {
            if (arr[i] % 2 == 0) ans += oddsFromRight;
            else ans += evensFromRight;
            temp[k++] = arr[i++];
        }
        while (j <= right) {
            temp[k++] = arr[j++];
        }
        for (i = left; i <= right; i++) arr[i] = temp[i];
        
        return ans;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 26. K-diff Pairs in an Array (LeetCode 532)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và số `k`, đếm số lượng các cặp giá trị độc nhất `(nums[i], nums[j])` sao cho $|nums[i] - nums[j]| == k$.
**Phân tích thuật toán:** Bài này có thể làm bằng Hash Map trong $O(N)$, tuy nhiên cách tiếp cận D&C kết hợp mảng (thông qua Sorting) tiết kiệm không gian phụ. Sắp xếp mảng thành $O(N \log N)$ và dùng hai con trỏ (Two Pointers) duyệt. Cẩn thận loại bỏ trùng lặp khi di chuyển con trỏ.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class KDiffPairs {
    public int findPairs(int[] nums, int k) {
        Arrays.sort(nums);
        int left = 0, right = 1;
        int count = 0;
        
        while (left < nums.length && right < nums.length) {
            if (left == right || nums[right] - nums[left] < k) {
                right++;
            } else if (nums[right] - nums[left] > k) {
                left++;
            } else {
                count++;
                left++;
                // Bỏ qua giá trị trùng lặp của nums[left] để tránh đếm lại cùng cặp giá trị
                while (left < nums.length && nums[left] == nums[left - 1]) {
                    left++;
                }
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(1)$.

## 27. Smaller Numbers with Condition (Codeforces)
**Đề bài chi tiết:** Cho mảng số nguyên, tính số lượng cặp $i < j$ sao cho $A[i] > A[j] + K$.
**Phân tích thuật toán:** Cách phân tích hoàn toàn giống bài toán Reverse Pairs (LeetCode 493). Thay vì kiểm tra điều kiện nhân hai, ta kiểm tra điều kiện cộng $K$. Thuật toán sẽ đếm độc lập số lượng phần tử đáp ứng điều kiện trước khi trộn hai nửa lại thành một mảng có thứ tự.
**Mã nguồn Java:**
```java
public class InversionsWithCondition {
    public int countPairs(int[] nums, int k) {
        return mergeSort(nums, 0, nums.length - 1, k);
    }
    
    private int mergeSort(int[] nums, int left, int right, int k) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, left, mid, k) + mergeSort(nums, mid + 1, right, k);
        
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long)nums[i] > (long)nums[j] + k) {
                j++;
            }
            count += (j - (mid + 1));
        }
        
        merge(nums, left, mid, right);
        return count;
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, t = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[t++] = nums[i++];
            else temp[t++] = nums[j++];
        }
        while (i <= mid) temp[t++] = nums[i++];
        while (j <= right) temp[t++] = nums[j++];
        System.arraycopy(temp, 0, nums, left, temp.length);
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 28. Inversion Counting trên String (Anagram Inversion)
**Đề bài chi tiết:** Cho hai chuỗi anagram `A` và `B`. Tìm số lần Swap (đổi chỗ 2 ký tự kề nhau) tối thiểu để biến chuỗi `A` thành chuỗi `B`.
**Phân tích thuật toán:** Đánh chỉ số cho mỗi ký tự trong chuỗi `B` dựa vào thứ tự xuất hiện, sau đó ánh xạ các chỉ số này sang ký tự tương ứng trên chuỗi `A`. Số bước Swap kề nhau tối thiểu chính là số Inversions (nghịch thế) của mảng chỉ số ánh xạ thu được từ `A`.
**Mã nguồn Java:**
```java
import java.util.*;

public class StringInversions {
    public long minSwaps(String A, String B) {
        Map<Character, Queue<Integer>> map = new HashMap<>();
        for (int i = 0; i < B.length(); i++) {
            map.putIfAbsent(B.charAt(i), new LinkedList<>());
            map.get(B.charAt(i)).add(i);
        }
        
        int[] arr = new int[A.length()];
        for (int i = 0; i < A.length(); i++) {
            arr[i] = map.get(A.charAt(i)).poll();
        }
        
        return countInversions(arr);
    }
    
    private long countInversions(int[] arr) {
        return mergeSort(arr, new int[arr.length], 0, arr.length - 1);
    }
    
    private long mergeSort(int[] arr, int[] temp, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        long inv = mergeSort(arr, temp, left, mid) + mergeSort(arr, temp, mid + 1, right);
        
        int i = left, j = mid + 1, k = left;
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
                inv += (mid - i + 1);
            }
        }
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= right) temp[k++] = arr[j++];
        for (i = left; i <= right; i++) arr[i] = temp[i];
        
        return inv;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$ (thời gian Sort và ánh xạ Hash Map), Space: $O(N)$.

## 29. Count the Number of Important Reverse Pairs (Codeforces)
**Đề bài chi tiết:** Đếm số nghịch thế `i < j` thoả mãn `nums[i] > K * nums[j]` với $K$ là hệ số trọng lượng.
**Phân tích thuật toán:** Một biến thể có trọng số của Reverse Pairs. Ta dùng vòng lặp độc lập đếm nghịch thế trọng số `K` trước khi trộn hai mảng như Merge Sort bình thường. Chú ý ngăn chặn việc tràn số nguyên (Overflow) bằng cách ép kiểu sang biến `long` khi thực hiện phép nhân.
**Mã nguồn Java:**
```java
public class ImportantReversePairs {
    public int countReversePairs(int[] nums, int k) {
        return mergeSort(nums, 0, nums.length - 1, k);
    }
    
    private int mergeSort(int[] nums, int left, int right, int K_weight) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, left, mid, K_weight) + mergeSort(nums, mid + 1, right, K_weight);
        
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long)nums[i] > (long)K_weight * nums[j]) {
                j++;
            }
            count += (j - (mid + 1));
        }
        
        merge(nums, left, mid, right);
        return count;
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, t = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[t++] = nums[i++];
            else temp[t++] = nums[j++];
        }
        while (i <= mid) temp[t++] = nums[i++];
        while (j <= right) temp[t++] = nums[j++];
        System.arraycopy(temp, 0, nums, left, temp.length);
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$, Space: $O(N)$.

## 30. Tree Inversions (LCA logic / Graph DFS)
**Đề bài chi tiết:** Cho một cây phân cấp có `N` node, mỗi node có một số nguyên. Một nghịch thế trên cây được tính dọc theo đường đi trực tiếp từ Root (node gốc) đến Leaf (node lá), trong đó tổ tiên (ancestor) có giá trị lớn hơn thế hệ sau (descendant). Đếm tổng số nghịch thế như vậy trên toàn bộ cây.
**Phân tích thuật toán:** Nếu đường thẳng từ root xuống leaf là 1 mảng tĩnh, ta có thể dùng Merge Sort. Do trên Tree có rất nhiều nhánh rẽ ngang, ta cần dùng cấu trúc dữ liệu khôi phục trạng thái (như Binary Indexed Tree - BIT) truyền vào DFS đệ quy. Khi DFS lùi ngược (backtrack) qua nhánh khác, ta phải rollback (undo) lại trạng thái phần tử vừa xét trong BIT. Đây là một dạng Divide & Conquer trên Đồ thị.
**Mã nguồn Java:**
```java
import java.util.*;

public class TreeInversions {
    long totalInversions = 0;
    int[] bit;
    int maxVal;
    
    public long countTreeInversions(int n, List<List<Integer>> tree, int[] values, int maxV) {
        this.maxVal = maxV;
        bit = new int[maxVal + 1];
        totalInversions = 0;
        dfs(0, -1, tree, values); // Bắt đầu duyệt DFS tại Root node = 0
        return totalInversions;
    }
    
    private void dfs(int node, int parent, List<List<Integer>> tree, int[] values) {
        int val = values[node];
        // Tính số lượng phần tử có giá trị lớn hơn val đã xuất hiện từ Root đến node hiện tại
        totalInversions += (query(maxVal) - query(val));
        
        // Thêm giá trị val vào trạng thái BIT của đường đi
        update(val, 1);
        
        // Đệ quy DFS vào các nhánh con
        for (int child : tree.get(node)) {
            if (child != parent) {
                dfs(child, node, tree, values);
            }
        }
        
        // Rollback (undo) val khỏi BIT khi dời khỏi node, để không gây nhiễu cho các nhánh cây khác
        update(val, -1);
    }
    
    private void update(int x, int val) {
        for (; x <= maxVal; x += x & -x) {
            bit[x] += val;
        }
    }
    
    private int query(int x) {
        int sum = 0;
        for (; x > 0; x -= x & -x) {
            sum += bit[x];
        }
        return sum;
    }
}
```
**Độ phức tạp:** Time: $O(N \log M)$ (với `M` là giá trị max của node để xây BIT), Space: $O(M)$ cho mảng BIT và $O(N)$ bộ nhớ stack đệ quy.
