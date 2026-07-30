# Bài tập Advanced Divide and Conquer

## 1. The Skyline Problem
**Đề bài chi tiết:**
Cho tọa độ một số tòa nhà hình chữ nhật nằm trên mặt đất phẳng `[left, right, height]`. Trả về đường viền bầu trời (skyline) được tạo bởi các tòa nhà này nhìn từ xa, biểu diễn dưới dạng danh sách các điểm ngoặt `[x, y]`.

**Phân tích thuật toán:**
Dùng Divide and Conquer:
1. Chia danh sách các tòa nhà thành hai nửa.
2. Đệ quy tìm skyline của mỗi nửa.
3. Gộp (Merge) hai skyline lại. Quá trình gộp dùng 2 con trỏ, duy trì độ cao lớn nhất tại mỗi thời điểm x từ cả hai skyline.

**Mã nguồn Java:**
```java
import java.util.*;

public class Skyline {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        return mergeSkyline(buildings, 0, buildings.length - 1);
    }
    
    private List<List<Integer>> mergeSkyline(int[][] buildings, int left, int right) {
        List<List<Integer>> res = new ArrayList<>();
        if (left > right) return res;
        if (left == right) {
            res.add(Arrays.asList(buildings[left][0], buildings[left][2]));
            res.add(Arrays.asList(buildings[left][1], 0));
            return res;
        }
        
        int mid = left + (right - left) / 2;
        List<List<Integer>> leftSkyline = mergeSkyline(buildings, left, mid);
        List<List<Integer>> rightSkyline = mergeSkyline(buildings, mid + 1, right);
        return merge(leftSkyline, rightSkyline);
    }
    
    private List<List<Integer>> merge(List<List<Integer>> left, List<List<Integer>> right) {
        List<List<Integer>> res = new ArrayList<>();
        int h1 = 0, h2 = 0;
        int i = 0, j = 0;
        
        while (i < left.size() && j < right.size()) {
            int x, h;
            List<Integer> point1 = left.get(i);
            List<Integer> point2 = right.get(j);
            
            if (point1.get(0) < point2.get(0)) {
                x = point1.get(0);
                h1 = point1.get(1);
                h = Math.max(h1, h2);
                i++;
            } else if (point1.get(0) > point2.get(0)) {
                x = point2.get(0);
                h2 = point2.get(1);
                h = Math.max(h1, h2);
                j++;
            } else {
                x = point1.get(0);
                h1 = point1.get(1);
                h2 = point2.get(1);
                h = Math.max(h1, h2);
                i++; j++;
            }
            if (res.isEmpty() || res.get(res.size() - 1).get(1) != h) {
                res.add(Arrays.asList(x, h));
            }
        }
        
        while (i < left.size()) res.add(left.get(i++));
        while (j < right.size()) res.add(right.get(j++));
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(N)$.

---

## 2. Beautiful Array
**Đề bài chi tiết:**
Với mảng gồm các số từ `1` đến `N`, tìm một hoán vị sao cho mọi `i < j`, không tồn tại `k` (với `i < k < j`) sao cho `A[k] * 2 = A[i] + A[j]`.

**Phân tích thuật toán:**
D&C dựa trên tính chất: Nếu `A` là Beautiful Array, thì `2*A` và `2*A - 1` cũng vậy. Bằng cách để toàn bộ số lẻ ở nửa trái, số chẵn ở nửa phải, thì một phần tử bất kỳ ở giữa (nếu so $i$ lẻ, $j$ chẵn) có tổng $A[i]+A[j]$ luôn lẻ, nên $2*A[k]$ (chẵn) sẽ không bao giờ bằng tổng đó.

**Mã nguồn Java:**
```java
import java.util.*;

public class BeautifulArray {
    Map<Integer, int[]> memo = new HashMap<>();
    
    public int[] beautifulArray(int N) {
        if (memo.containsKey(N)) return memo.get(N);
        int[] res = new int[N];
        if (N == 1) {
            res[0] = 1;
        } else {
            int t = 0;
            for (int x : beautifulArray((N + 1) / 2)) {
                res[t++] = 2 * x - 1;
            }
            for (int x : beautifulArray(N / 2)) {
                res[t++] = 2 * x;
            }
        }
        memo.put(N, res);
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(N \log N)$ (lưu cache).

---

## 3. Different Ways to Add Parentheses
**Đề bài chi tiết:**
Cho biểu thức toán học dưới dạng chuỗi gồm số và toán tử `+, -, *`. Trả về tất cả các kết quả khả thi có thể đạt được bằng cách nhóm số và toán tử theo nhiều cách khác nhau.

**Phân tích thuật toán:**
Duyệt qua chuỗi. Mỗi khi gặp toán tử, chia chuỗi làm hai: bên trái toán tử và bên phải toán tử. Đệ quy giải bài toán cho từng phần. Sau khi có kết quả hai bên, tổ hợp từng cặp kết quả với toán tử tương ứng. Dùng Memoization (Map) để tránh tính lại các chuỗi con chồng chéo.

**Mã nguồn Java:**
```java
import java.util.*;

public class DiffWaysToCompute {
    Map<String, List<Integer>> memo = new HashMap<>();
    
    public List<Integer> diffWaysToCompute(String expression) {
        if (memo.containsKey(expression)) return memo.get(expression);
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                String p1 = expression.substring(0, i);
                String p2 = expression.substring(i + 1);
                
                List<Integer> part1Res = diffWaysToCompute(p1);
                List<Integer> part2Res = diffWaysToCompute(p2);
                
                for (int p1Val : part1Res) {
                    for (int p2Val : part2Res) {
                        if (c == '+') res.add(p1Val + p2Val);
                        else if (c == '-') res.add(p1Val - p2Val);
                        else if (c == '*') res.add(p1Val * p2Val);
                    }
                }
            }
        }
        if (res.isEmpty()) {
            res.add(Integer.valueOf(expression));
        }
        memo.put(expression, res);
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: Xấp xỉ số Catalan $O(4^n / \sqrt{n})$, Không gian: tương đương để lưu trữ.

---

## 4. Burst Balloons (Divide and Conquer perspective)
**Đề bài chi tiết:**
Có $N$ quả bóng chứa số $nums[i]$. Nổ quả $i$ sẽ nhận được $nums[i-1] \times nums[i] \times nums[i+1]$ xu. Tìm số xu tối đa. 

**Phân tích thuật toán:**
Góc nhìn D&C: Thay vì chọn quả bóng nổ đầu tiên (làm mảng bị thay đổi khó chia), ta chọn quả bóng nổ CÙNG (cuối cùng) trên khoảng `(left, right)`. Khi chọn $i$ là quả cuối, bài toán được chia hoàn toàn độc lập thành `(left, i)` và `(i, right)`.

**Mã nguồn Java:**
```java
public class BurstBalloons {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int[] arr = new int[n + 2];
        arr[0] = 1; arr[n + 1] = 1;
        for (int i = 0; i < n; i++) arr[i + 1] = nums[i];
        
        int[][] memo = new int[n + 2][n + 2];
        return dc(arr, memo, 0, n + 1);
    }
    
    private int dc(int[] arr, int[][] memo, int left, int right) {
        if (left + 1 == right) return 0;
        if (memo[left][right] > 0) return memo[left][right];
        
        int max = 0;
        for (int i = left + 1; i < right; i++) {
            max = Math.max(max, arr[left] * arr[i] * arr[right] + 
                                dc(arr, memo, left, i) + dc(arr, memo, i, right));
        }
        memo[left][right] = max;
        return max;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^3)$, Không gian: $O(N^2)$.

---

## 5. Closest Pair of Points
**Đề bài chi tiết:**
Tìm khoảng cách ngắn nhất giữa hai điểm trong hệ tọa độ 2D.

**Phân tích thuật toán:**
Sắp xếp theo trục X. Cắt đôi tập điểm. Giải tìm khoảng cách nhỏ nhất ở bên trái ($dL$) và bên phải ($dR$), gọi nhỏ hơn là $d$. Merge: chỉ xét các điểm nằm cách đường phân chia $X \le d$. Lọc điểm, sắp xếp theo Y, duyệt so sánh 7 điểm lân cận.

**Mã nguồn Java:**
```java
import java.util.*;

class Point {
    double x, y;
    Point(double x, double y) { this.x = x; this.y = y; }
}

public class ClosestPair {
    public double findClosest(Point[] points) {
        Arrays.sort(points, (a, b) -> Double.compare(a.x, b.x));
        Point[] temp = new Point[points.length];
        return closestUtil(points, 0, points.length - 1, temp);
    }

    private double closestUtil(Point[] points, int left, int right, Point[] temp) {
        if (right <= left) return Double.MAX_VALUE;
        if (right - left == 1) return dist(points[left], points[right]);

        int mid = left + (right - left) / 2;
        double midX = points[mid].x;
        double dl = closestUtil(points, left, mid, temp);
        double dr = closestUtil(points, mid + 1, right, temp);
        double d = Math.min(dl, dr);

        int count = 0;
        for (int i = left; i <= right; i++) {
            if (Math.abs(points[i].x - midX) < d) {
                temp[count++] = points[i];
            }
        }
        Arrays.sort(temp, 0, count, (a, b) -> Double.compare(a.y, b.y));
        for (int i = 0; i < count; ++i) {
            for (int j = i + 1; j < count && (temp[j].y - temp[i].y) < d; ++j) {
                d = Math.min(d, dist(temp[i], temp[j]));
            }
        }
        return d;
    }

    private double dist(Point p1, Point p2) {
        return Math.sqrt((p1.x - p2.x)*(p1.x - p2.x) + (p1.y - p2.y)*(p1.y - p2.y));
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log^2 N)$ (Do dùng sort trong merge, nếu merge sort kết hợp thì được $O(N \log N)$), Không gian: $O(N)$.

---

## 6. Expression Add Operators
**Đề bài chi tiết:**
Cho một chuỗi số và mục tiêu, thêm các toán tử `+`, `-`, `*` vào giữa các chữ số sao cho biểu thức đạt giá trị mục tiêu.

**Phân tích thuật toán:**
Dùng Backtracking kết hợp tư tưởng D&C. Ở mỗi bước, tách một phần tiền tố (prefix) thành một số, phần còn lại (suffix) tiếp tục được chia cắt đệ quy. Trong quá trình đó ta tính toán liên tục biểu thức để so khớp với target.

**Mã nguồn Java:**
```java
import java.util.*;

public class AddOperators {
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<>();
        if (num == null || num.length() == 0) return res;
        dfs(res, new StringBuilder(), num, target, 0, 0, 0);
        return res;
    }
    
    private void dfs(List<String> res, StringBuilder path, String num, int target, int pos, long eval, long multed) {
        if (pos == num.length()) {
            if (eval == target) res.add(path.toString());
            return;
        }
        for (int i = pos; i < num.length(); i++) {
            if (i != pos && num.charAt(pos) == '0') break; 
            long cur = Long.parseLong(num.substring(pos, i + 1));
            int len = path.length();
            if (pos == 0) {
                dfs(res, path.append(cur), num, target, i + 1, cur, cur);
                path.setLength(len);
            } else {
                dfs(res, path.append("+").append(cur), num, target, i + 1, eval + cur, cur);
                path.setLength(len);
                dfs(res, path.append("-").append(cur), num, target, i + 1, eval - cur, -cur);
                path.setLength(len);
                dfs(res, path.append("*").append(cur), num, target, i + 1, eval - multed + multed * cur, multed * cur);
                path.setLength(len);
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(4^N)$, Không gian: $O(N)$.

---

## 7. Longest Substring with At Least K Repeating Characters
**Đề bài chi tiết:**
Tìm độ dài chuỗi con dài nhất mà trong đó mỗi ký tự xuất hiện ít nhất $K$ lần.

**Phân tích thuật toán:**
Dùng D&C: Đếm tần suất các ký tự. Nếu mọi ký tự đều $\ge K$ thì chuỗi này là hợp lệ. Nếu có ký tự nào xuất hiện $< K$, nó không thể nằm trong chuỗi con thỏa mãn. Ta "chia" chuỗi tại vị trí các ký tự không hợp lệ này và giải đệ quy cho các chuỗi con. 

**Mã nguồn Java:**
```java
public class LongestSubstring {
    public int longestSubstring(String s, int k) {
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
                int j = i + 1;
                while (j < end && count[chs[j] - 'a'] < k) j++;
                return Math.max(helper(chs, start, i, k), helper(chs, j, end, k));
            }
        }
        return end - start;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$ (Trong trường hợp xấu nhất là $O(N^2)$ nếu mỗi lần chỉ chia 1 ký tự, nhưng bảng chữ cái giới hạn 26), Không gian: $O(N)$.

---

## 8. Super Egg Drop
**Đề bài chi tiết:**
Có $K$ quả trứng và $N$ tầng, tìm số lần thả tối thiểu (trong trường hợp xấu nhất) để tìm ra tầng $F$ mà thả trứng từ đó không vỡ.

**Phân tích thuật toán:**
Kết hợp DP với D&C (Binary Search). Tại tầng $X$, trứng có thể vỡ (còn $K-1$ trứng, thử $X-1$ tầng dưới) hoặc không vỡ (còn $K$ trứng, thử $N-X$ tầng trên). Để tối ưu tìm $X$ sao cho `max(vỡ, không vỡ)` là nhỏ nhất, ta chia nhị phân vì hàm số đơn điệu.

**Mã nguồn Java:**
```java
import java.util.*;

public class SuperEggDrop {
    Map<Integer, Integer> memo = new HashMap<>();
    
    public int superEggDrop(int k, int n) {
        return dp(k, n);
    }
    
    private int dp(int k, int n) {
        if (n == 0) return 0;
        if (k == 1) return n;
        
        int key = n * 1000 + k;
        if (memo.containsKey(key)) return memo.get(key);
        
        int low = 1, high = n, res = n;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int broken = dp(k - 1, mid - 1);
            int notBroken = dp(k, n - mid);
            
            if (broken > notBroken) {
                high = mid - 1;
                res = Math.min(res, broken + 1);
            } else {
                low = mid + 1;
                res = Math.min(res, notBroken + 1);
            }
        }
        memo.put(key, res);
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(K \times N \log N)$, Không gian: $O(K \times N)$.

---

## 9. Guess Number Higher or Lower II
**Đề bài chi tiết:**
Trò chơi đoán số từ $1$ đến $N$. Mỗi lần đoán sai số $X$, bạn mất $X$ đô. Tìm số tiền tối thiểu để đảm bảo luôn thắng bất kể số bí mật là gì.

**Phân tích thuật toán:**
Dùng Min-Max kết hợp D&C + DP. Tại khoảng `[left, right]`, nếu chọn số $i$, trường hợp xấu nhất là ta phải trả $i + \max(\text{tiền tìm trong } [left, i-1], \text{tiền tìm trong } [i+1, right])$. Ta cần tìm $i$ để toàn bộ cục này là nhỏ nhất (Min-Max).

**Mã nguồn Java:**
```java
public class GuessNumberII {
    public int getMoneyAmount(int n) {
        int[][] memo = new int[n + 1][n + 1];
        return dp(1, n, memo);
    }
    
    private int dp(int left, int right, int[][] memo) {
        if (left >= right) return 0;
        if (memo[left][right] != 0) return memo[left][right];
        
        int res = Integer.MAX_VALUE;
        for (int i = left; i <= right; i++) {
            int cost = i + Math.max(dp(left, i - 1, memo), dp(i + 1, right, memo));
            res = Math.min(res, cost);
        }
        memo[left][right] = res;
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^3)$, Không gian: $O(N^2)$.

---

## 10. Find the Kth Smallest Sum of a Matrix With Sorted Rows (D&C approach)
**Đề bài chi tiết:**
Cho ma trận `mat` cỡ `m x n` với các hàng được sắp xếp tăng dần. Chọn 1 phần tử từ mỗi hàng, tạo thành tổng. Trả về tổng nhỏ thứ $K$.

**Phân tích thuật toán:**
Ta có thể giải bằng cách dùng D&C: Thay vì xử lý nguyên ma trận, chia ma trận thành hai nửa hàng trên và hàng dưới. Gộp (Merge) 2 danh sách gồm $K$ phần tử tổng nhỏ nhất của nửa trên với nửa dưới. Ở bài toán quy mô nhỏ, ta gộp dần 2 hàng liên tiếp là cách đơn giản nhất.

**Mã nguồn Java:**
```java
import java.util.*;

public class KthSmallestSum {
    public int kthSmallest(int[][] mat, int k) {
        int m = mat.length;
        List<Integer> res = new ArrayList<>();
        res.add(0);
        
        for (int i = 0; i < m; i++) {
            res = merge(res, mat[i], k);
        }
        return res.get(k - 1);
    }
    
    private List<Integer> merge(List<Integer> list, int[] row, int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        for (int i : list) {
            for (int j : row) {
                pq.add(i + j);
                if (pq.size() > k) {
                    pq.poll();
                }
            }
        }
        List<Integer> next = new ArrayList<>(pq);
        Collections.sort(next);
        return next;
    }
}
```
**Độ phức tạp:** Thời gian: $O(m \times k \times n \log k)$, Không gian: $O(k)$.

---

## 11. Merge K Sorted Lists (D&C)
**Đề bài chi tiết:**
Cho mảng gồm $K$ danh sách liên kết (linked lists), trong đó mỗi danh sách đã được sắp xếp tăng dần. Hãy trộn tất cả các danh sách liên kết này thành một danh sách liên kết duy nhất được sắp xếp tăng dần và trả về nó.

**Phân tích thuật toán:**
Sử dụng phương pháp Chia để trị (Divide and Conquer):
1. Chia mảng chứa $K$ danh sách thành hai nửa, lặp lại cho đến khi chỉ còn 1 hoặc 2 danh sách trong mỗi phần.
2. Trộn (Merge) 2 danh sách đã sắp xếp thành 1.
3. Gộp ngược trở lại lên trên để tạo thành danh sách hoàn chỉnh.
Phương pháp này giúp giảm thiểu số lượng danh sách phải duyệt, đưa chi phí trộn về tối ưu.

**Mã nguồn Java:**
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public class MergeKSortedLists {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        return merge(lists, 0, lists.length - 1);
    }
    
    private ListNode merge(ListNode[] lists, int left, int right) {
        if (left == right) return lists[left];
        int mid = left + (right - left) / 2;
        ListNode l1 = merge(lists, left, mid);
        ListNode l2 = merge(lists, mid + 1, right);
        return mergeTwoLists(l1, l2);
    }
    
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log K)$, Không gian: $O(\log K)$ (do đệ quy). $N$ là tổng số node của tất cả các danh sách, $K$ là số lượng danh sách.

---

## 12. Kth Largest Element in an Array (QuickSelect)
**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` chưa được sắp xếp và một số nguyên `k`. Hãy tìm phần tử lớn thứ `k` trong mảng đó. Chú ý rằng đây là phần tử lớn thứ `k` trong thứ tự đã sắp xếp, chứ không phải phần tử phân biệt thứ `k`.

**Phân tích thuật toán:**
Dùng thuật toán QuickSelect (một biến thể của Divide and Conquer dựa trên QuickSort):
1. Chọn một phần tử làm chốt (pivot).
2. Phân tách (partition) mảng sao cho các phần tử lớn hơn pivot nằm bên trái, nhỏ hơn nằm bên phải (để tìm K lớn nhất).
3. Nếu vị trí của pivot bằng `k - 1`, ta tìm được đáp án. Nếu vị trí lớn hơn `k - 1`, tiếp tục đệ quy bên trái. Nếu nhỏ hơn, tiếp tục đệ quy bên phải.

**Mã nguồn Java:**
```java
public class KthLargestElement {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, nums.length - 1, k - 1);
    }
    
    private int quickSelect(int[] nums, int left, int right, int k) {
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) {
            return nums[pivotIndex];
        } else if (pivotIndex < k) {
            return quickSelect(nums, left, pivotIndex - 1, k);
        } else {
            return quickSelect(nums, pivotIndex + 1, right, k);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] > pivot) { // Sắp xếp giảm dần
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
**Độ phức tạp:** Thời gian: Trung bình $O(N)$, Tồi tệ nhất $O(N^2)$. Không gian: $O(1)$ (nếu không tính stack đệ quy).

---

## 13. Count Inversions in an Array
**Đề bài chi tiết:**
Cho một mảng các số nguyên. Đếm số lượng nghịch thế (inversions) trong mảng. Một nghịch thế là một cặp $(i, j)$ sao cho $i < j$ và $nums[i] > nums[j]$.

**Phân tích thuật toán:**
Biến thể của thuật toán Merge Sort (Chia để trị):
1. Chia mảng thành hai nửa, đệ quy đếm số nghịch thế của nửa trái và nửa phải.
2. Quá trình trộn (Merge): nếu một phần tử ở nửa trái `nums[i]` lớn hơn phần tử ở nửa phải `nums[j]`, thì tất cả các phần tử từ `i` đến cuối nửa trái cũng sẽ lớn hơn `nums[j]` vì các nửa đã được sắp xếp. Ta cộng thêm `(mid - i + 1)` vào tổng số nghịch thế.

**Mã nguồn Java:**
```java
public class CountInversions {
    public long countInversions(int[] arr) {
        int[] temp = new int[arr.length];
        return mergeSortAndCount(arr, temp, 0, arr.length - 1);
    }
    
    private long mergeSortAndCount(int[] arr, int[] temp, int left, int right) {
        long count = 0;
        if (left < right) {
            int mid = left + (right - left) / 2;
            count += mergeSortAndCount(arr, temp, left, mid);
            count += mergeSortAndCount(arr, temp, mid + 1, right);
            count += mergeAndCount(arr, temp, left, mid, right);
        }
        return count;
    }
    
    private long mergeAndCount(int[] arr, int[] temp, int left, int mid, int right) {
        int i = left, j = mid + 1, k = left;
        long invCount = 0;
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
                invCount += (mid - i + 1);
            }
        }
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= right) temp[k++] = arr[j++];
        for (i = left; i <= right; i++) arr[i] = temp[i];
        return invCount;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(N)$.

---

## 14. Maximum Subarray Sum
**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Tìm một mảng con liên tiếp có tổng lớn nhất và trả về tổng đó. (Yêu cầu áp dụng phương pháp Divide and Conquer).

**Phân tích thuật toán:**
Chia để trị:
1. Chia mảng thành 2 nửa: trái và phải.
2. Tổng mảng con lớn nhất có thể nằm trọn ở nửa trái, nằm trọn ở nửa phải, hoặc bắt ngang qua vị trí chính giữa (crossing mid).
3. Tìm giá trị max ở 3 trường hợp đó và trả về.

**Mã nguồn Java:**
```java
public class MaximumSubarray {
    public int maxSubArray(int[] nums) {
        return maxSubArrayHelper(nums, 0, nums.length - 1);
    }
    
    private int maxSubArrayHelper(int[] nums, int left, int right) {
        if (left == right) return nums[left];
        int mid = left + (right - left) / 2;
        int leftSum = maxSubArrayHelper(nums, left, mid);
        int rightSum = maxSubArrayHelper(nums, mid + 1, right);
        int crossSum = crossSubarraySum(nums, left, mid, right);
        return Math.max(Math.max(leftSum, rightSum), crossSum);
    }
    
    private int crossSubarraySum(int[] nums, int left, int mid, int right) {
        int leftMax = Integer.MIN_VALUE, currentSum = 0;
        for (int i = mid; i >= left; i--) {
            currentSum += nums[i];
            leftMax = Math.max(leftMax, currentSum);
        }
        int rightMax = Integer.MIN_VALUE;
        currentSum = 0;
        for (int i = mid + 1; i <= right; i++) {
            currentSum += nums[i];
            rightMax = Math.max(rightMax, currentSum);
        }
        return leftMax + rightMax;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(\log N)$ (call stack).

---

## 15. Search a 2D Matrix II
**Đề bài chi tiết:**
Viết một thuật toán hiệu quả để tìm kiếm một giá trị `target` trong một ma trận 2D kích thước `m x n`. Ma trận có tính chất: các phần tử trong mỗi hàng sắp xếp tăng dần từ trái sang phải, các phần tử trong mỗi cột sắp xếp tăng dần từ trên xuống dưới.

**Phân tích thuật toán:**
Dùng Divide and Conquer không gian tìm kiếm: 
Tại một ma trận con, chọn phần tử ở giữa `mid`. Nếu `target == mid` thì tìm thấy. Nếu `target < mid`, target có thể nằm ở góc trên-trái, dưới-trái, hoặc trên-phải (loại bỏ góc dưới-phải vì mọi phần tử ở đó đều $\ge mid > target$). Ta đệ quy tìm ở các không gian còn lại. (Lưu ý, tuy có cách tuyến tính $O(M+N)$ xuất phát từ góc, đây là cách triển khai Divide and Conquer thuần túy).

**Mã nguồn Java:**
```java
public class SearchMatrixII {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return false;
        return searchRec(matrix, target, 0, matrix[0].length - 1, 0, matrix.length - 1);
    }
    
    private boolean searchRec(int[][] matrix, int target, int left, int right, int up, int down) {
        if (left > right || up > down) return false;
        if (target < matrix[up][left] || target > matrix[down][right]) return false;
        
        int midRow = up + (down - up) / 2;
        int midCol = left + (right - left) / 2;
        
        if (matrix[midRow][midCol] == target) return true;
        else if (matrix[midRow][midCol] > target) {
            // Loại bỏ góc dưới-phải
            return searchRec(matrix, target, left, midCol - 1, up, down) ||
                   searchRec(matrix, target, midCol, right, up, midRow - 1);
        } else {
            // Loại bỏ góc trên-trái
            return searchRec(matrix, target, midCol + 1, right, up, down) ||
                   searchRec(matrix, target, left, midCol, midRow + 1, down);
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O((MN)^{\log_4 3})$, Không gian: $O(\log(MN))$.

---

## 16. Majority Element
**Đề bài chi tiết:**
Cho mảng `nums` kích thước $N$, tìm phần tử chiếm đa số. Phần tử chiếm đa số là phần tử xuất hiện nhiều hơn $\lfloor N/2 \rfloor$ lần.

**Phân tích thuật toán:**
Dùng thuật toán Chia để trị:
Chia mảng làm hai nửa trái và phải, đệ quy tìm phần tử đa số trong hai nửa này. Nếu cả hai nửa trả về cùng một phần tử, đó chính là phần tử đa số chung. Nếu khác nhau, ta đếm số lần xuất hiện của hai phần tử này trong toàn bộ đoạn mảng từ `left` đến `right` để xem phần tử nào thực sự chiếm đa số và trả về.

**Mã nguồn Java:**
```java
public class MajorityElement {
    public int majorityElement(int[] nums) {
        return majorityElementRec(nums, 0, nums.length - 1);
    }
    
    private int majorityElementRec(int[] nums, int left, int right) {
        if (left == right) return nums[left];
        
        int mid = left + (right - left) / 2;
        int leftMajority = majorityElementRec(nums, left, mid);
        int rightMajority = majorityElementRec(nums, mid + 1, right);
        
        if (leftMajority == rightMajority) return leftMajority;
        
        int leftCount = countInRange(nums, leftMajority, left, right);
        int rightCount = countInRange(nums, rightMajority, left, right);
        
        return leftCount > rightCount ? leftMajority : rightMajority;
    }
    
    private int countInRange(int[] nums, int target, int left, int right) {
        int count = 0;
        for (int i = left; i <= right; i++) {
            if (nums[i] == target) count++;
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(\log N)$.

---

## 17. Median of Two Sorted Arrays
**Đề bài chi tiết:**
Cho 2 mảng đã được sắp xếp `nums1` và `nums2` có kích thước $m$ và $n$. Tìm trung vị (median) của 2 mảng này. Yêu cầu thời gian chạy $O(\log(m+n))$.

**Phân tích thuật toán:**
Sử dụng tìm kiếm nhị phân chia để trị trên mảng: 
Tìm vị trí cắt (partition) ở hai mảng sao cho số phần tử nửa trái bằng số phần tử nửa phải (hoặc nhiều hơn 1 nếu tổng lẻ) và mọi phần tử nửa trái $\le$ mọi phần tử nửa phải. Ta có: `partition1 + partition2 = (m + n + 1) / 2`. Áp dụng binary search trên mảng ngắn hơn để tìm `partition1`.

**Mã nguồn Java:**
```java
public class MedianTwoSortedArrays {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1); // Luôn nhị phân trên mảng ngắn
        }
        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m;
        
        while (left <= right) {
            int partition1 = left + (right - left) / 2;
            int partition2 = (m + n + 1) / 2 - partition1;
            
            int maxLeft1 = (partition1 == 0) ? Integer.MIN_VALUE : nums1[partition1 - 1];
            int minRight1 = (partition1 == m) ? Integer.MAX_VALUE : nums1[partition1];
            
            int maxLeft2 = (partition2 == 0) ? Integer.MIN_VALUE : nums2[partition2 - 1];
            int minRight2 = (partition2 == n) ? Integer.MAX_VALUE : nums2[partition2];
            
            if (maxLeft1 <= minRight2 && maxLeft2 <= minRight1) {
                if ((m + n) % 2 == 0) {
                    return (Math.max(maxLeft1, maxLeft2) + Math.min(minRight1, minRight2)) / 2.0;
                } else {
                    return Math.max(maxLeft1, maxLeft2);
                }
            } else if (maxLeft1 > minRight2) {
                right = partition1 - 1;
            } else {
                left = partition1 + 1;
            }
        }
        throw new IllegalArgumentException();
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log(\min(m, n)))$, Không gian: $O(1)$.

---

## 18. Reverse Pairs
**Đề bài chi tiết:**
Cho một mảng `nums`. Gọi nghịch thế đặc biệt là một cặp $(i, j)$ sao cho $i < j$ và $nums[i] > 2 \times nums[j]$. Hãy đếm số lượng nghịch thế đặc biệt này.

**Phân tích thuật toán:**
Cải tiến của Inversion Count dùng Merge Sort (Divide and Conquer):
Trong quá trình trộn 2 mảng con `left` và `right` đã được sắp xếp tăng dần, trước khi tiến hành trộn, ta đếm số lượng cặp thỏa mãn `nums[i] > 2L * nums[j]`. Do hai mảng con đều tăng dần, ta có thể dùng 2 con trỏ dịch chuyển theo một chiều duy nhất để đếm một cách tuyến tính.

**Mã nguồn Java:**
```java
public class ReversePairs {
    public int reversePairs(int[] nums) {
        return mergeSort(nums, 0, nums.length - 1);
    }
    
    private int mergeSort(int[] nums, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, left, mid) + mergeSort(nums, mid + 1, right);
        
        // Đếm cặp thỏa mãn
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && nums[i] > 2L * nums[j]) {
                j++;
            }
            count += (j - (mid + 1));
        }
        
        // Trộn hai nửa
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
        for (i = 0; i < k; i++) {
            nums[left + i] = temp[i];
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(N)$.

---

## 19. Count of Smaller Numbers After Self
**Đề bài chi tiết:**
Cho mảng số nguyên `nums`, trả về một mảng đếm `counts` mới, trong đó `counts[i]` là số lượng các phần tử nằm bên phải của `nums[i]` có giá trị bé hơn `nums[i]`.

**Phân tích thuật toán:**
Áp dụng Merge Sort Chia để trị, đi kèm mảng phụ lưu giá trị cùng chỉ số (index) ban đầu. Trong bước merge trộn 2 mảng tăng dần, khi một phần tử của nửa trái được chọn (do nó nhỏ hơn phần tử nửa phải), ta đã biết có bao nhiêu phần tử ở nửa phải nhỏ hơn nó (bằng số phần tử ở mảng phải đã đi qua `rightCount`). Ta cộng dồn số này vào `counts` tại index ban đầu.

**Mã nguồn Java:**
```java
import java.util.*;

public class CountSmallerNumbers {
    class Pair {
        int val;
        int index;
        Pair(int val, int index) { this.val = val; this.index = index; }
    }
    
    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Pair[] arr = new Pair[n];
        for (int i = 0; i < n; i++) {
            arr[i] = new Pair(nums[i], i);
        }
        mergeSort(arr, 0, n - 1, res);
        
        List<Integer> list = new ArrayList<>();
        for (int r : res) list.add(r);
        return list;
    }
    
    private void mergeSort(Pair[] arr, int left, int right, int[] res) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid, res);
        mergeSort(arr, mid + 1, right, res);
        
        Pair[] temp = new Pair[right - left + 1];
        int i = left, j = mid + 1, k = 0, rightCount = 0;
        
        while (i <= mid && j <= right) {
            if (arr[i].val <= arr[j].val) {
                res[arr[i].index] += rightCount;
                temp[k++] = arr[i++];
            } else {
                rightCount++;
                temp[k++] = arr[j++];
            }
        }
        while (i <= mid) {
            res[arr[i].index] += rightCount;
            temp[k++] = arr[i++];
        }
        while (j <= right) {
            temp[k++] = arr[j++];
        }
        for (int p = 0; p < k; p++) {
            arr[left + p] = temp[p];
        }
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(N)$.

---

## 20. Construct Binary Tree from Inorder and Postorder Traversal
**Đề bài chi tiết:**
Cho hai mảng `inorder` (duyệt trung thứ tự) và `postorder` (duyệt hậu thứ tự) của một cây nhị phân. Hãy xây dựng lại và trả về gốc của cây nhị phân ban đầu đó.

**Phân tích thuật toán:**
Dùng Divide and Conquer:
1. Nút gốc (root) của toàn bộ cây là phần tử cuối cùng của mảng `postorder`.
2. Tìm vị trí của gốc này trong mảng `inorder`. Vị trí này phân chia `inorder` thành 2 phần: danh sách các nút của nhánh trái và nhánh phải.
3. Dựa vào số lượng nút của nhánh trái, xác định được ranh giới tương ứng trong mảng `postorder`.
4. Đệ quy xây dựng cây con trái và phải, gán chúng cho gốc hiện tại.

**Mã nguồn Java:**
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) { this.val = val; }
}

public class ConstructBinaryTree {
    Map<Integer, Integer> inorderMap = new HashMap<>();
    
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int i = 0; i < inorder.length; i++) {
            inorderMap.put(inorder[i], i);
        }
        return build(inorder, postorder, 0, inorder.length - 1, 0, postorder.length - 1);
    }
    
    private TreeNode build(int[] inorder, int[] postorder, int inStart, int inEnd, int postStart, int postEnd) {
        if (inStart > inEnd || postStart > postEnd) return null;
        
        int rootVal = postorder[postEnd];
        TreeNode root = new TreeNode(rootVal);
        
        int inRootIndex = inorderMap.get(rootVal);
        int leftTreeSize = inRootIndex - inStart;
        
        root.left = build(inorder, postorder, inStart, inRootIndex - 1, postStart, postStart + leftTreeSize - 1);
        root.right = build(inorder, postorder, inRootIndex + 1, inEnd, postStart + leftTreeSize, postEnd - 1);
        
        return root;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$, Không gian: $O(N)$ (lưu Hash Map và call stack đệ quy).

---

## 21. Convert Sorted List to Binary Search Tree
**Đề bài chi tiết:**
Cho phần tử đầu tiên (head) của một danh sách liên kết đơn đã được sắp xếp tăng dần. Hãy chuyển đổi danh sách liên kết này thành một cây tìm kiếm nhị phân (BST) cân bằng độ cao. Một cây được coi là cân bằng độ cao nếu độ sâu của 2 cây con của mỗi nút không lệch nhau quá 1.

**Phân tích thuật toán:**
Sử dụng phương pháp Chia để trị (Divide and Conquer):
1. Vì danh sách đã được sắp xếp, nút giữa (median) sẽ là gốc của cây (đảm bảo cân bằng).
2. Dùng kỹ thuật hai con trỏ (slow và fast) để tìm nút chính giữa của danh sách liên kết.
3. Cắt đứt kết nối giữa nửa trái và nửa phải tại nút giữa.
4. Đệ quy xây dựng cây con trái bằng nửa trái của danh sách, và cây con phải bằng nửa phải.

**Mã nguồn Java:**
```java
// class ListNode { int val; ListNode next; ListNode(int val) { this.val = val; } }
// class TreeNode { int val; TreeNode left; TreeNode right; TreeNode(int val) { this.val = val; } }

public class ConvertSortedListToBST {
    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) return null;
        if (head.next == null) return new TreeNode(head.val);
        
        ListNode prev = null;
        ListNode slow = head;
        ListNode fast = head;
        
        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // Ngắt danh sách làm đôi
        if (prev != null) prev.next = null;
        
        TreeNode root = new TreeNode(slow.val);
        root.left = sortedListToBST(head);
        root.right = sortedListToBST(slow.next);
        
        return root;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$ (do tìm kiếm node giữa tốn $O(N)$ ở mỗi tầng đệ quy). Không gian: $O(\log N)$ (call stack).

---

## 22. Sort List
**Đề bài chi tiết:**
Cho `head` của một danh sách liên kết, hãy sắp xếp danh sách đó theo thứ tự tăng dần và trả về. Yêu cầu thực hiện trong độ phức tạp thời gian $O(N \log N)$ và không gian $O(1)$ (nếu không tính bộ nhớ đệ quy).

**Phân tích thuật toán:**
Sử dụng Merge Sort (Divide and Conquer):
1. Dùng thuật toán rùa và thỏ (slow và fast pointers) để tìm phần tử giữa danh sách, cắt danh sách làm hai phần.
2. Đệ quy sắp xếp từng phần (`sortList`).
3. Dùng thao tác trộn 2 danh sách liên kết đã sắp xếp (merge) để gộp lại.

**Mã nguồn Java:**
```java
public class SortList {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        
        ListNode mid = getMid(head);
        ListNode rightHead = mid.next;
        mid.next = null;
        
        ListNode left = sortList(head);
        ListNode right = sortList(rightHead);
        
        return merge(left, right);
    }
    
    private ListNode getMid(ListNode head) {
        ListNode slow = head;
        ListNode fast = head.next; // fast đi trước 1 bước để slow chốt ở cuối nửa trái
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
    
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        curr.next = (l1 != null) ? l1 : l2;
        return dummy.next;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$, Không gian: $O(\log N)$ (cho call stack đệ quy).

---

## 23. The K-th Lexicographical String of All Happy Strings
**Đề bài chi tiết:**
Chuỗi vui vẻ (happy string) chỉ gồm các ký tự `['a', 'b', 'c']` và không có hai ký tự kề nhau nào giống nhau. Cho số nguyên `n` và `k`, trả về chuỗi vui vẻ thứ `k` có độ dài `n` theo thứ tự từ điển, hoặc chuỗi rỗng nếu số lượng chuỗi vui vẻ có độ dài `n` ít hơn `k`.

**Phân tích thuật toán:**
Dùng D&C toán học (Toán tổ hợp & Không gian trạng thái): 
Tổng số chuỗi vui vẻ độ dài $n$ là $3 \times 2^{n-1}$. Nếu $k$ lớn hơn số này thì không tồn tại. Nếu không, ta chia 3 khoảng đều nhau cho vị trí đầu tiên ('a', 'b', 'c'). Sau khi xác định ký tự đầu, với mỗi ký tự tiếp theo ta chỉ có 2 sự lựa chọn (vì không được lặp lại). Ta chia đôi không gian $2^{n-i}$ tại mỗi bước để xác định ký tự nào sẽ được chọn dựa trên giá trị của $k$.

**Mã nguồn Java:**
```java
public class HappyString {
    public String getHappyString(int n, int k) {
        int total = 3 * (1 << (n - 1));
        if (k > total) return "";
        
        StringBuilder sb = new StringBuilder();
        int blockSize = 1 << (n - 1);
        k--; // Đưa k về zero-based index
        
        char[] firstChars = {'a', 'b', 'c'};
        sb.append(firstChars[k / blockSize]);
        k %= blockSize;
        
        for (int i = 1; i < n; i++) {
            blockSize /= 2;
            char prev = sb.charAt(i - 1);
            char[] nextChars = getNextChars(prev);
            sb.append(nextChars[k / blockSize]);
            k %= blockSize;
        }
        
        return sb.toString();
    }
    
    private char[] getNextChars(char c) {
        if (c == 'a') return new char[]{'b', 'c'};
        if (c == 'b') return new char[]{'a', 'c'};
        return new char[]{'a', 'b'};
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$, Không gian: $O(N)$ (để lưu chuỗi kết quả).

---

## 24. Falling Squares
**Đề bài chi tiết:**
Cho mảng 2D biểu diễn các hình vuông rơi xuống mặt đất vô tận dọc theo trục hoành (tọa độ trục hoành và cạnh hình vuông). Mỗi hình vuông sẽ rơi cho tới khi chạm mặt đất hoặc chạm vào cạnh trên của một hình vuông khác đã rơi xuống. Tìm độ cao cực đại của đường viền (skyline) sau khi thả mỗi hình vuông.

**Phân tích thuật toán:**
Một cách mô phỏng chia để trị bằng đệ quy Sweep Line (đoạn giao nhau). Ta lưu các đoạn `(left, right, height)`. Khi thả một khối `[left, right, len]`, ta quét các đoạn cũ cắt ngang qua đoạn mới để tìm độ cao lớn nhất hiện tại ở đoạn bị cắt, sau đó tính độ cao mới `maxH = max(current_h) + len`. Thêm khối mới này vào danh sách và cập nhật mảng max global. (Có thể dùng Segment Tree cho bài toán lớn hơn).

**Mã nguồn Java:**
```java
import java.util.*;

public class FallingSquares {
    class Interval {
        int left, right, height;
        Interval(int left, int right, int height) {
            this.left = left;
            this.right = right;
            this.height = height;
        }
    }
    
    public List<Integer> fallingSquares(int[][] positions) {
        List<Interval> intervals = new ArrayList<>();
        List<Integer> res = new ArrayList<>();
        int maxHeight = 0;
        
        for (int[] pos : positions) {
            int left = pos[0];
            int size = pos[1];
            int right = left + size;
            
            int baseHeight = 0;
            for (Interval interval : intervals) {
                // Kiểm tra xem có giao nhau không
                if (interval.right > left && interval.left < right) {
                    baseHeight = Math.max(baseHeight, interval.height);
                }
            }
            
            int newHeight = baseHeight + size;
            intervals.add(new Interval(left, right, newHeight));
            maxHeight = Math.max(maxHeight, newHeight);
            res.add(maxHeight);
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2)$ (có thể tối ưu $O(N \log N)$ bằng Segment tree), Không gian: $O(N)$.

---

## 25. Balance a Binary Search Tree
**Đề bài chi tiết:**
Cho `root` của một cây tìm kiếm nhị phân (BST). Cây này có thể không cân bằng. Hãy xây dựng lại và trả về một BST cân bằng từ các phần tử của cây gốc. Cây được xem là cân bằng nếu độ cao của 2 nhánh không lệch quá 1.

**Phân tích thuật toán:**
Dùng D&C sau khi "duỗi" cây:
1. Duyệt trung thứ tự (Inorder Traversal) để lấy ra một mảng đã được sắp xếp chứa tất cả các phần tử trong cây ban đầu.
2. Từ mảng đã sắp xếp này, sử dụng Divide and Conquer giống như bài tập "Convert Sorted Array to Binary Search Tree": chọn phần tử chính giữa làm root, phần nửa trái đệ quy làm nhánh trái, nửa phải đệ quy làm nhánh phải.

**Mã nguồn Java:**
```java
import java.util.*;

public class BalanceBST {
    List<TreeNode> sortedNodes = new ArrayList<>();
    
    public TreeNode balanceBST(TreeNode root) {
        inorderTraverse(root);
        return buildBalancedBST(0, sortedNodes.size() - 1);
    }
    
    private void inorderTraverse(TreeNode root) {
        if (root == null) return;
        inorderTraverse(root.left);
        sortedNodes.add(root);
        inorderTraverse(root.right);
    }
    
    private TreeNode buildBalancedBST(int left, int right) {
        if (left > right) return null;
        int mid = left + (right - left) / 2;
        TreeNode root = sortedNodes.get(mid);
        
        // Cắt đứt các liên kết cũ để tránh tạo vòng lặp
        root.left = buildBalancedBST(left, mid - 1);
        root.right = buildBalancedBST(mid + 1, right);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$, Không gian: $O(N)$.

---

## 26. K Closest Points to Origin
**Đề bài chi tiết:**
Cho mảng tọa độ các điểm 2D `points` và số `k`. Tìm $K$ điểm có khoảng cách Euclid tới gốc tọa độ $(0, 0)$ nhỏ nhất.

**Phân tích thuật toán:**
Áp dụng thuật toán QuickSelect (Divide and Conquer):
1. Tính bình phương khoảng cách của mỗi điểm.
2. Chọn pivot và phân tách mảng điểm thành 2 phần: bên trái gần gốc hơn pivot, bên phải xa gốc hơn.
3. Nếu vị trí phần tử chia cắt bằng $K$, ta lấy $K$ phần tử đầu tiên. Nếu lớn hơn, đệ quy chia nửa trái. Nếu nhỏ hơn, đệ quy nửa phải.

**Mã nguồn Java:**
```java
import java.util.*;

public class KClosestPoints {
    public int[][] kClosest(int[][] points, int k) {
        quickSelect(points, 0, points.length - 1, k);
        return Arrays.copyOfRange(points, 0, k);
    }
    
    private void quickSelect(int[][] points, int left, int right, int k) {
        if (left >= right) return;
        int pivotIndex = partition(points, left, right);
        if (pivotIndex == k - 1) {
            return;
        } else if (pivotIndex < k - 1) {
            quickSelect(points, left, pivotIndex - 1, k);
        } else {
            quickSelect(points, pivotIndex + 1, right, k);
        }
    }
    
    private int partition(int[][] points, int left, int right) {
        int[] pivot = points[right];
        int pivotDist = dist(pivot);
        int i = left;
        for (int j = left; j < right; j++) {
            if (dist(points[j]) < pivotDist) {
                swap(points, i, j);
                i++;
            }
        }
        swap(points, i, right);
        return i;
    }
    
    private int dist(int[] p) {
        return p[0]*p[0] + p[1]*p[1];
    }
    
    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i];
        points[i] = points[j];
        points[j] = temp;
    }
}
```
**Độ phức tạp:** Thời gian: Trung bình $O(N)$, Tồi tệ nhất $O(N^2)$, Không gian: $O(1)$ hoặc $O(\log N)$ đệ quy.

---

## 27. Eliminate Maximum Number of Monsters
**Đề bài chi tiết:**
Bạn đang chơi một trò chơi phòng thủ với một thành phố và $N$ con quái vật. Mảng `dist` chứa khoảng cách ban đầu và `speed` chứa tốc độ của từng con. Bạn dùng súng mỗi phút bắn hạ được 1 con (tại phút 0, 1, 2...). Trò chơi kết thúc nếu một quái vật chạm tới thành phố (khoảng cách $\le 0$). Tìm số lượng quái vật tối đa bạn có thể diệt.

**Phân tích thuật toán:**
Chia mỗi phần tử `time = ceil(dist / speed)`. Sắp xếp các mảng thời gian này, ở mỗi phút ta kiểm tra `time[i] <= i` (quái vật đã đến nơi trước khi bị tiêu diệt). Sắp xếp thực tế là ứng dụng của thuật toán chia để trị (Merge Sort). Bài toán thực chất chỉ cần mô phỏng thời gian đến.

**Mã nguồn Java:**
```java
import java.util.*;

public class EliminateMonsters {
    public int eliminateMaximum(int[] dist, int[] speed) {
        int n = dist.length;
        double[] time = new double[n];
        for (int i = 0; i < n; i++) {
            time[i] = (double) dist[i] / speed[i];
        }
        
        Arrays.sort(time); // Divide and conquer sort
        
        int count = 0;
        for (int i = 0; i < n; i++) {
            if (time[i] <= i) {
                break;
            }
            count++;
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$ (do thuật toán sắp xếp), Không gian: $O(N)$.

---

## 28. Find K-th Smallest Pair Distance
**Đề bài chi tiết:**
Cho một mảng `nums`, tìm khoảng cách nhỏ thứ $K$ giữa hai phần tử bất kỳ. Khoảng cách được định nghĩa là giá trị tuyệt đối $|nums[i] - nums[j]|$.

**Phân tích thuật toán:**
Biến thể D&C trên tập giá trị (Binary Search on Answer):
1. Khoảng cách nhỏ nhất là $0$, lớn nhất là $max - min$. Ta lấy khoảng từ $low = 0$ đến $high = max - min$.
2. Chọn `mid` = khoảng cách trung bình. Chia vùng không gian đáp án bằng cách đếm xem có bao nhiêu cặp có khoảng cách $\le mid$. Việc đếm này được thực hiện hiệu quả bằng Sliding Window sau khi mảng đã sắp xếp.
3. Nếu số cặp $\ge K$, thu nhỏ `high = mid`. Ngược lại tăng `low = mid + 1`.

**Mã nguồn Java:**
```java
import java.util.*;

public class KthSmallestPairDistance {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int low = 0;
        int high = nums[n - 1] - nums[0];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countPairs(nums, mid);
            
            if (count >= k) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
    
    private int countPairs(int[] nums, int mid) {
        int count = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > mid) {
                left++;
            }
            count += right - left;
        }
        return count;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N + N \log(\max - \min))$, Không gian: $O(1)$.

---

## 29. Quad Tree (Construct Quad Tree)
**Đề bài chi tiết:**
Cho ma trận `grid` cỡ $N \times N$ chỉ chứa các số 0 và 1 (trong đó N là lũy thừa của 2). Hãy biểu diễn lưới này dưới dạng một Quad-Tree (cây tứ phân). Mỗi nút lưu 2 thuộc tính: `isLeaf` (là nút lá nếu tất cả các ô trong khối đều cùng giá trị) và `val` (giá trị của toàn bộ ô nếu là nút lá).

**Phân tích thuật toán:**
Áp dụng trực tiếp Divide and Conquer 2D:
1. Kiểm tra xem vùng đang xét có đồng nhất toàn số 0 hoặc toàn số 1 hay không. Nếu có, tạo nút lá và trả về.
2. Nếu không đồng nhất, chia cắt vùng đó thành 4 vùng vuông nhỏ hơn ở 4 góc: Top-Left, Top-Right, Bottom-Left, Bottom-Right.
3. Gọi đệ quy xây dựng Quad-Tree cho 4 vùng đó.

**Mã nguồn Java:**
```java
class Node {
    public boolean val;
    public boolean isLeaf;
    public Node topLeft, topRight, bottomLeft, bottomRight;

    public Node(boolean val, boolean isLeaf) {
        this.val = val;
        this.isLeaf = isLeaf;
        this.topLeft = null;
        this.topRight = null;
        this.bottomLeft = null;
        this.bottomRight = null;
    }
}

public class ConstructQuadTree {
    public Node construct(int[][] grid) {
        return build(grid, 0, 0, grid.length);
    }
    
    private Node build(int[][] grid, int r, int c, int size) {
        if (isUniform(grid, r, c, size)) {
            return new Node(grid[r][c] == 1, true);
        }
        
        Node node = new Node(true, false);
        int half = size / 2;
        node.topLeft = build(grid, r, c, half);
        node.topRight = build(grid, r, c + half, half);
        node.bottomLeft = build(grid, r + half, c, half);
        node.bottomRight = build(grid, r + half, c + half, half);
        return node;
    }
    
    private boolean isUniform(int[][] grid, int r, int c, int size) {
        int val = grid[r][c];
        for (int i = r; i < r + size; i++) {
            for (int j = c; j < c + size; j++) {
                if (grid[i][j] != val) return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N^2 \log N)$ (kiểm tra toàn bộ ma trận ở các tầng log). Có thể tối ưu lên $O(N^2)$ dùng ma trận tổng tiền tố 2D. Không gian: $O(\log N)$ đệ quy.

---

## 30. Serialize and Deserialize BST
**Đề bài chi tiết:**
Viết thuật toán mã hóa (serialize) một BST thành chuỗi và giải mã (deserialize) chuỗi đó ngược lại thành cây BST ban đầu, mà không dùng cấu trúc lưu thêm như cây nhị phân thông thường (chỉ lưu 1 mảng dữ liệu).

**Phân tích thuật toán:**
Dùng mảng duyệt tiền thứ tự (Pre-order Traversal) để serialize. 
Khi giải mã (Deserialize), ta dùng Divide and Conquer đi kèm với giới hạn phạm vi `(lower, upper)`.
Với phần tử đầu tiên (root), các phần tử tiếp theo thuộc nhánh trái sẽ phải nhỏ hơn root, phần tử thuộc nhánh phải sẽ phải lớn hơn root. Ta đệ quy chia luồng mảng dựa trên mốc không gian giá trị.

**Mã nguồn Java:**
```java
import java.util.*;

public class CodecBST {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }
    
    private void serializeHelper(TreeNode root, StringBuilder sb) {
        if (root == null) return;
        sb.append(root.val).append(",");
        serializeHelper(root.left, sb);
        serializeHelper(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data == null || data.isEmpty()) return null;
        Queue<String> q = new LinkedList<>(Arrays.asList(data.split(",")));
        return deserializeHelper(q, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    
    private TreeNode deserializeHelper(Queue<String> q, int lower, int upper) {
        if (q.isEmpty() || q.peek().isEmpty()) return null;
        int val = Integer.parseInt(q.peek());
        if (val < lower || val > upper) return null;
        
        q.poll();
        TreeNode root = new TreeNode(val);
        root.left = deserializeHelper(q, lower, val);
        root.right = deserializeHelper(q, val, upper);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$ cho cả hai tác vụ. Không gian: $O(N)$ cho chuỗi và Call Stack.
