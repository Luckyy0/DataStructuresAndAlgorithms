# Danh sách bài tập Deque & Monotonic Deque

## 1. Design Front Middle Back Queue (LeetCode 1670)
### Đề bài chi tiết
Thiết kế một cấu trúc dữ liệu `FrontMiddleBackQueue` hỗ trợ các thao tác: `pushFront`, `pushMiddle`, `pushBack`, `popFront`, `popMiddle`, `popBack`. 
Mọi thao tác xóa trả về giá trị phần tử, nếu hàng đợi rỗng trả về -1. "Giữa" của mảng độ dài chẵn được tính là phần tử đứng gần đầu hơn (VD: mảng `[1,2,3,4]`, popMiddle trả về `2`).

### Phân tích thuật toán
- Sử dụng **2 Deque** (hoặc `LinkedList`) để duy trì nửa đầu (`left`) và nửa sau (`right`) của hàng đợi.
- Duy trì tính cân bằng: `left` có kích thước bằng `right` hoặc lớn hơn `right` đúng 1 phần tử.
- Hàm `balance()` được gọi sau mỗi thao tác để luân chuyển phần tử giữa `left` và `right` nếu mất cân bằng.

### Mã nguồn Java
```java
import java.util.*;

class FrontMiddleBackQueue {
    Deque<Integer> left, right;

    public FrontMiddleBackQueue() {
        left = new ArrayDeque<>();
        right = new ArrayDeque<>();
    }
    
    private void balance() {
        if (left.size() > right.size() + 1) {
            right.addFirst(left.pollLast());
        } else if (left.size() < right.size()) {
            left.addLast(right.pollFirst());
        }
    }
    
    public void pushFront(int val) {
        left.addFirst(val);
        balance();
    }
    
    public void pushMiddle(int val) {
        if (left.size() > right.size()) {
            right.addFirst(left.pollLast());
        }
        left.addLast(val);
        balance();
    }
    
    public void pushBack(int val) {
        right.addLast(val);
        balance();
    }
    
    public int popFront() {
        if (left.isEmpty() && right.isEmpty()) return -1;
        int res = left.isEmpty() ? right.pollFirst() : left.pollFirst();
        balance();
        return res;
    }
    
    public int popMiddle() {
        if (left.isEmpty() && right.isEmpty()) return -1;
        int res = left.pollLast();
        balance();
        return res;
    }
    
    public int popBack() {
        if (left.isEmpty() && right.isEmpty()) return -1;
        int res = right.isEmpty() ? left.pollLast() : right.pollLast();
        balance();
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho tất cả các thao tác.
- **Không gian (Space):** $O(N)$.

---

## 2. Sliding Window Maximum (LeetCode 239)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và một cửa sổ trượt kích thước `k`. Cửa sổ di chuyển từ trái sang phải mỗi lần 1 vị trí. Trả về mảng chứa phần tử lớn nhất trong từng cửa sổ.

### Phân tích thuật toán
- Sử dụng **Monotonic Deque (Hàng đợi đơn điệu)** lưu trữ các **chỉ số (index)**.
- Khi thêm một phần tử mới ở index `i`, loại bỏ ở cuối Deque những phần tử có giá trị nhỏ hơn hoặc bằng `nums[i]`.
- Loại bỏ ở đầu Deque những phần tử nằm ngoài khoảng cửa sổ (có index `<= i - k`).
- Phần tử lớn nhất trong cửa sổ hiện tại chính là giá trị tương ứng với chỉ số nằm ở đầu Deque.

### Mã nguồn Java
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] res = new int[n - k + 1];
        Deque<Integer> dq = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            // Loại bỏ các phần tử nằm ngoài cửa sổ hiện tại
            while (!dq.isEmpty() && dq.peekFirst() <= i - k) {
                dq.pollFirst();
            }
            // Duy trì Deque giảm dần
            while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[i]) {
                dq.pollLast();
            }
            dq.addLast(i);
            
            // Cửa sổ đã đủ kích thước
            if (i >= k - 1) {
                res[i - k + 1] = nums[dq.peekFirst()];
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ - mỗi index được đẩy vào và lấy ra khỏi Deque tối đa 1 lần.
- **Không gian:** $O(K)$ cho Deque.

---

## 3. Constrained Subsequence Sum (LeetCode 1425)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Tìm tổng lớn nhất của một mảng con không liền kề (subsequence) sao cho khoảng cách giữa hai chỉ số liên tiếp được chọn không vượt quá `k` (nghĩa là $j - i \le k$).

### Phân tích thuật toán
- Sử dụng Quy hoạch động (DP): `dp[i]` là tổng lớn nhất của subsequence kết thúc tại `i`.
- `dp[i] = nums[i] + max(0, max(dp[i-k], dp[i-k+1], ..., dp[i-1]))`.
- Để tìm max trong cửa sổ kích thước $k$ của mảng `dp`, ta áp dụng **Monotonic Deque** (giống hệt bài Sliding Window Maximum).

### Mã nguồn Java
```java
class Solution {
    public int constrainedSubsetSum(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        Deque<Integer> dq = new ArrayDeque<>();
        int maxSum = Integer.MIN_VALUE;
        
        for (int i = 0; i < n; i++) {
            if (!dq.isEmpty() && dq.peekFirst() < i - k) {
                dq.pollFirst();
            }
            
            dp[i] = nums[i];
            if (!dq.isEmpty() && dp[dq.peekFirst()] > 0) {
                dp[i] += dp[dq.peekFirst()];
            }
            
            maxSum = Math.max(maxSum, dp[i]);
            
            while (!dq.isEmpty() && dp[dq.peekLast()] <= dp[i]) {
                dq.pollLast();
            }
            dq.addLast(i);
        }
        return maxSum;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$ (có thể tối ưu không gian mảng dp nếu chỉ sửa trực tiếp trên nums, nhưng $O(N)$ cho Deque).

---

## 4. Shortest Subarray with Sum at Least K (LeetCode 862)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Tìm chiều dài ngắn nhất của một mảng con liên tiếp không rỗng có tổng lớn hơn hoặc bằng `k`. Trả về -1 nếu không tồn tại. Mảng có thể chứa số âm.

### Phân tích thuật toán
- Tính mảng cộng dồn Prefix Sum `P`. Tổng subarray `[i, j)` là `P[j] - P[i]`.
- Ta cần tìm $j > i$ sao cho `P[j] - P[i] >= k` và $j - i$ nhỏ nhất.
- Dùng **Monotonic Deque** lưu các chỉ số của `P` theo chiều **tăng dần** giá trị.
- Nếu `P[j] - P[dq.peekFirst()] >= k`, cập nhật kết quả và `pollFirst()` vì ta đã tìm được subarray hợp lệ và phần tử này không thể tạo subarray ngắn hơn ở các bước tiếp theo.
- Trước khi thêm `j`, loại bỏ các phần tử ở cuối Deque có `P[dq.peekLast()] >= P[j]` vì `P[j]` nhỏ hơn và ở vị trí gần hơn, sẽ luôn là lựa chọn tốt hơn cho các $j'$ sau này.

### Mã nguồn Java
```java
class Solution {
    public int shortestSubarray(int[] nums, int k) {
        int n = nums.length;
        long[] P = new long[n + 1];
        for (int i = 0; i < n; i++) P[i + 1] = P[i] + nums[i];
        
        int minLen = n + 1;
        Deque<Integer> dq = new ArrayDeque<>();
        
        for (int i = 0; i <= n; i++) {
            while (!dq.isEmpty() && P[i] - P[dq.peekFirst()] >= k) {
                minLen = Math.min(minLen, i - dq.pollFirst());
            }
            while (!dq.isEmpty() && P[dq.peekLast()] >= P[i]) {
                dq.pollLast();
            }
            dq.addLast(i);
        }
        return minLen <= n ? minLen : -1;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$

---

## 5. Maximum Absolute Sum of Any Subarray (LeetCode 1749)
### Đề bài chi tiết
Bạn được cung cấp một mảng số nguyên `nums`. Giá trị tuyệt đối của tổng subarray là `abs(sum(nums[i...j]))`. Trả về giá trị tổng tuyệt đối lớn nhất của bất kỳ subarray nào (có thể rỗng).

### Phân tích thuật toán
- Dù có thể dùng thuật toán Kadane, bài này có thể áp dụng tư duy Prefix Sum liên quan tới hàng đợi/phạm vi min-max.
- Gọi mảng prefix sum là `P`. Tổng subarray từ `i` đến `j` là `P[j] - P[i]`.
- `abs(P[j] - P[i])` đạt cực đại khi khoảng cách giữa giá trị lớn nhất và nhỏ nhất của mảng prefix sum là lớn nhất.
- Do đó, ta chỉ cần tìm `max(P) - min(P)`. Thậm chí không cần Deque!

### Mã nguồn Java
```java
class Solution {
    public int maxAbsoluteSum(int[] nums) {
        int minPref = 0, maxPref = 0;
        int currentSum = 0;
        for (int num : nums) {
            currentSum += num;
            minPref = Math.min(minPref, currentSum);
            maxPref = Math.max(maxPref, currentSum);
        }
        return maxPref - minPref;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(1)$

---

## 6. Continuous Subarray Sum (LeetCode 523)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Kiểm tra xem mảng có chứa mảng con liên tiếp độ dài ít nhất là 2 có tổng chia hết cho `k` hay không.

### Phân tích thuật toán
- Sử dụng logic mảng cộng dồn Prefix Sum và Hash Map (Có thể dùng tư duy tương tự lưu trữ giá trị gần nhất như Queue/Map).
- Nếu `(P[j] - P[i]) % k == 0` thì `P[j] % k == P[i] % k`.
- Dùng HashMap lưu phần dư của Prefix Sum chia cho `k` cùng với index của nó.

### Mã nguồn Java
```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            int mod = k == 0 ? sum : sum % k;
            if (map.containsKey(mod)) {
                if (i - map.get(mod) > 1) return true;
            } else {
                map.put(mod, i);
            }
        }
        return false;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(\min(N, K))$

---

## 7. Jump Game VI (LeetCode 1696)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Bạn bắt đầu tại chỉ số 0 và mỗi bước có thể nhảy tới một chỉ số từ `i + 1` đến `i + k`. Điểm số là tổng các `nums[j]` bạn ghé thăm. Trả về điểm số tối đa để đi đến chỉ số cuối cùng `n-1`.

### Phân tích thuật toán
- Quy hoạch động: `dp[i] = nums[i] + max(dp[i-k], dp[i-k+1], ..., dp[i-1])`.
- Tìm kiếm cực trị trong khoảng trượt $k$ -> Áp dụng **Monotonic Deque**.
- Lưu trữ các chỉ số trong Deque sao cho `dp[index]` giảm dần.

### Mã nguồn Java
```java
class Solution {
    public int maxResult(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        dp[0] = nums[0];
        Deque<Integer> dq = new ArrayDeque<>();
        dq.addLast(0);
        
        for (int i = 1; i < n; i++) {
            while (!dq.isEmpty() && dq.peekFirst() < i - k) {
                dq.pollFirst();
            }
            
            dp[i] = nums[i] + dp[dq.peekFirst()];
            
            while (!dq.isEmpty() && dp[dq.peekLast()] <= dp[i]) {
                dq.pollLast();
            }
            dq.addLast(i);
        }
        return dp[n - 1];
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$

---

## 8. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit (LeetCode 1438)
### Đề bài chi tiết
Cho mảng `nums` và số nguyên `limit`. Tìm độ dài lớn nhất của một mảng con liên tiếp sao cho chênh lệch tuyệt đối giữa bất kỳ hai phần tử nào trong mảng con đó không vượt quá `limit`.

### Phân tích thuật toán
- Độ lệch lớn nhất trong cửa sổ là `max - min`. Ta cần `max - min <= limit`.
- Sử dụng kỹ thuật Sliding Window. Dùng **2 Monotonic Deques**:
  - `maxDq` duy trì phần tử lớn nhất giảm dần.
  - `minDq` duy trì phần tử nhỏ nhất tăng dần.
- Nếu `maxDq.peekFirst() - minDq.peekFirst() > limit`, thu hẹp cửa sổ bằng cách tăng con trỏ `left`.

### Mã nguồn Java
```java
class Solution {
    public int longestSubarray(int[] nums, int limit) {
        Deque<Integer> maxDq = new ArrayDeque<>();
        Deque<Integer> minDq = new ArrayDeque<>();
        int left = 0, res = 0;
        
        for (int right = 0; right < nums.length; right++) {
            while (!maxDq.isEmpty() && maxDq.peekLast() < nums[right]) maxDq.pollLast();
            while (!minDq.isEmpty() && minDq.peekLast() > nums[right]) minDq.pollLast();
            
            maxDq.addLast(nums[right]);
            minDq.addLast(nums[right]);
            
            while (maxDq.peekFirst() - minDq.peekFirst() > limit) {
                if (maxDq.peekFirst() == nums[left]) maxDq.pollFirst();
                if (minDq.peekFirst() == nums[left]) minDq.pollFirst();
                left++;
            }
            res = Math.max(res, right - left + 1);
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$

---

## 9. Minimum Number of K Consecutive Bit Flips (LeetCode 995)
### Đề bài chi tiết
Cho mảng nhị phân `nums` và số `k`. Trong mỗi thao tác, có thể chọn một mảng con độ dài `k` và lật tất cả các bit (0 thành 1, 1 thành 0). Tìm số thao tác tối thiểu để toàn bộ mảng là 1. Trả về -1 nếu không thể.

### Phân tích thuật toán
- Chúng ta duyệt từ trái qua phải. Nếu gặp bit 0 (sau khi đã tính ảnh hưởng của các lần lật trước), ta buộc phải lật một khoảng $k$ bắt đầu từ đó.
- Sử dụng một Queue/Deque để lưu **các chỉ số giới hạn** của thao tác lật (chỉ số mà thao tác lật kết thúc hiệu lực).
- Kích thước của Queue cho biết số lần mảng đang bị lật tại vị trí hiện tại. Nếu lật lẻ lần, trạng thái bit bị đổi.

### Mã nguồn Java
```java
class Solution {
    public int minKBitFlips(int[] nums, int k) {
        Deque<Integer> dq = new ArrayDeque<>();
        int flips = 0;
        
        for (int i = 0; i < nums.length; i++) {
            if (!dq.isEmpty() && dq.peekFirst() <= i - k) {
                dq.pollFirst(); // Hết hiệu lực lật
            }
            
            // Nếu bit gốc là 0 và lật chẵn lần -> vẫn là 0. Lật lẻ lần bit gốc 1 -> thành 0.
            if ((nums[i] == 0 && dq.size() % 2 == 0) || (nums[i] == 1 && dq.size() % 2 != 0)) {
                if (i + k > nums.length) return -1; // Không đủ k phần tử để lật
                dq.addLast(i);
                flips++;
            }
        }
        return flips;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(K)$

---

## 10. Maximum Score of a Good Subarray (LeetCode 1793)
### Đề bài chi tiết
Cho mảng `nums` và một số nguyên `k`. Điểm của mảng con `(i, j)` được tính bằng `min(nums[i..j]) * (j - i + 1)`. Mảng con gọi là tốt (good) nếu `i <= k <= j`. Trả về điểm số lớn nhất của mảng con tốt.

### Phân tích thuật toán
- Bài toán liên quan tới Largest Rectangle in Histogram, kết hợp ràng buộc mảng con phải đi qua `k`.
- Giải thuật Two Pointers + Tham lam (Greedy) là phổ biến nhất và tối ưu. Xuất phát từ $i = j = k$, ta mở rộng cửa sổ về bên nào lớn hơn.
- Tại mỗi bước, giữ min hiện tại và cập nhật max điểm.

### Mã nguồn Java
```java
class Solution {
    public int maximumScore(int[] nums, int k) {
        int n = nums.length;
        int left = k, right = k;
        int minVal = nums[k];
        int maxScore = nums[k];
        
        while (left > 0 || right < n - 1) {
            if (left == 0) {
                right++;
            } else if (right == n - 1) {
                left--;
            } else if (nums[left - 1] < nums[right + 1]) {
                right++;
            } else {
                left--;
            }
            minVal = Math.min(minVal, Math.min(nums[left], nums[right]));
            maxScore = Math.max(maxScore, minVal * (right - left + 1));
        }
        return maxScore;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(1)$

---

## 11. Find Median from Data Stream (LeetCode 295)
### Đề bài chi tiết
Thiết kế một cấu trúc dữ liệu `MedianFinder` hỗ trợ thêm một số vào luồng dữ liệu và tìm phần tử trung vị (median) của tất cả các phần tử đã thêm. Nếu số lượng phần tử là chẵn, trung vị là trung bình cộng của hai phần tử ở giữa.

### Phân tích thuật toán
- Sử dụng **hai Hàng đợi ưu tiên (Priority Queue)** để chia dữ liệu thành hai nửa.
- `maxHeap` lưu trữ nửa các phần tử nhỏ hơn (đỉnh là phần tử lớn nhất).
- `minHeap` lưu trữ nửa các phần tử lớn hơn (đỉnh là phần tử nhỏ nhất).
- Cần duy trì tính chất: kích thước của `maxHeap` luôn bằng hoặc lớn hơn `minHeap` đúng 1 phần tử.
- Khi thêm phần tử, ta đưa vào `maxHeap`, sau đó chuyển phần tử lớn nhất của `maxHeap` sang `minHeap` để đảm bảo đúng thứ tự. Cuối cùng, cân bằng lại kích thước hai heap.

### Mã nguồn Java
```java
class MedianFinder {
    PriorityQueue<Integer> maxHeap;
    PriorityQueue<Integer> minHeap;

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        maxHeap.add(num);
        minHeap.add(maxHeap.poll());
        
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.add(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() == minHeap.size()) {
            return (maxHeap.peek() + minHeap.peek()) / 2.0;
        }
        return maxHeap.peek();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** $O(\log N)$ cho mỗi thao tác thêm mới. $O(1)$ cho thao tác tìm trung vị.
- **Không gian (Space):** $O(N)$ để lưu toàn bộ các phần tử.

---

## 12. Moving Average from Data Stream (LeetCode 346)
### Đề bài chi tiết
Thiết kế cấu trúc `MovingAverage` để tính trung bình cộng của tối đa `size` phần tử gần nhất được thêm vào từ một luồng dữ liệu.

### Phân tích thuật toán
- Sử dụng một **Queue** (hoặc `ArrayDeque`) để lưu các giá trị của cửa sổ trượt hiện tại.
- Cần duy trì một biến `windowSum` lưu tổng các phần tử trong Queue.
- Khi thêm phần tử mới, ta đưa phần tử đó vào cuối Queue và cộng vào `windowSum`.
- Nếu kích thước của Queue vượt quá `size`, ta loại bỏ phần tử ở đầu Queue và trừ nó khỏi `windowSum`.

### Mã nguồn Java
```java
class MovingAverage {
    int size;
    int windowSum;
    Deque<Integer> queue;

    public MovingAverage(int size) {
        this.size = size;
        this.windowSum = 0;
        this.queue = new ArrayDeque<>();
    }
    
    public double next(int val) {
        queue.addLast(val);
        windowSum += val;
        
        if (queue.size() > size) {
            windowSum -= queue.pollFirst();
        }
        
        return (double) windowSum / queue.size();
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(1)$ cho thao tác `next()`.
- **Không gian:** $O(K)$ với $K$ là kích thước `size` của cửa sổ trượt.

---

## 13. Minimum Size Subarray Sum (LeetCode 209)
### Đề bài chi tiết
Cho một mảng các số nguyên dương `nums` và một số nguyên dương `target`. Hãy tìm chiều dài nhỏ nhất của một mảng con liên tiếp sao cho tổng các phần tử lớn hơn hoặc bằng `target`. Nếu không tồn tại, trả về 0.

### Phân tích thuật toán
- Áp dụng kỹ thuật **Sliding Window** với hai con trỏ `left` và `right`. Có thể coi như đang đẩy các phần tử vào một Queue.
- Duyệt `right` từ đầu đến cuối mảng và cộng dồn vào `sum`.
- Khi `sum >= target`, ta cập nhật độ dài mảng con nhỏ nhất và cố gắng thu hẹp cửa sổ bằng cách đẩy `left` sang phải (giống như `poll` khỏi Queue) đồng thời trừ giá trị khỏi `sum`.

### Mã nguồn Java
```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0, sum = 0;
        int minLen = Integer.MAX_VALUE;
        
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            
            while (sum >= target) {
                minLen = Math.min(minLen, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ vì mỗi phần tử được duyệt qua và bị loại ra nhiều nhất 1 lần.
- **Không gian:** $O(1)$ chỉ sử dụng vài biến để theo dõi.

---

## 14. Design Circular Deque (LeetCode 641)
### Đề bài chi tiết
Triển khai một hàng đợi hai đầu vòng tròn (`Circular Deque`) hỗ trợ chèn và xóa ở cả hai đầu với dung lượng cố định `k`.

### Phân tích thuật toán
- Dùng một mảng 1 chiều kích thước `k` và hai con trỏ: `front` và `rear`.
- Để tránh di chuyển phần tử, ta thao tác với con trỏ theo kiểu module: `(index + k) % k`.
- Dùng một biến `size` để quản lý số lượng phần tử, giúp cho việc kiểm tra đầy/rỗng dễ dàng hơn.

### Mã nguồn Java
```java
class MyCircularDeque {
    int[] arr;
    int front, rear, size, capacity;

    public MyCircularDeque(int k) {
        arr = new int[k];
        capacity = k;
        front = 0;
        rear = 0;
        size = 0;
    }
    
    public boolean insertFront(int value) {
        if (isFull()) return false;
        front = (front - 1 + capacity) % capacity;
        arr[front] = value;
        size++;
        return true;
    }
    
    public boolean insertLast(int value) {
        if (isFull()) return false;
        arr[rear] = value;
        rear = (rear + 1) % capacity;
        size++;
        return true;
    }
    
    public boolean deleteFront() {
        if (isEmpty()) return false;
        front = (front + 1) % capacity;
        size--;
        return true;
    }
    
    public boolean deleteLast() {
        if (isEmpty()) return false;
        rear = (rear - 1 + capacity) % capacity;
        size--;
        return true;
    }
    
    public int getFront() {
        return isEmpty() ? -1 : arr[front];
    }
    
    public int getRear() {
        return isEmpty() ? -1 : arr[(rear - 1 + capacity) % capacity];
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == capacity;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(1)$ cho tất cả các thao tác.
- **Không gian:** $O(K)$ lưu trữ các phần tử.

---

## 15. The Skyline Problem (LeetCode 218)
### Đề bài chi tiết
Cho danh sách các tòa nhà với điểm đầu, điểm cuối và chiều cao. Hãy trả về đường viền bầu trời (Skyline) tạo ra bởi các tòa nhà khi nhìn từ xa, được biểu diễn bằng các điểm chuyển đổi độ cao.

### Phân tích thuật toán
- Chuyển mỗi tòa nhà thành hai sự kiện: "Bắt đầu tòa nhà" (lưu chiều cao âm) và "Kết thúc tòa nhà" (lưu chiều cao dương).
- Sắp xếp các sự kiện theo tọa độ `x`. Nếu cùng `x`, sắp xếp theo chiều cao sao cho sự kiện bắt đầu cao hơn được xử lý trước.
- Dùng **TreeMap** (như một Priority Queue có khả năng xóa tùy ý ở $O(\log N)$) để theo dõi các độ cao hiện tại.
- Nếu đỉnh cao nhất trong TreeMap thay đổi sau khi xử lý một sự kiện, ta thêm một điểm vào kết quả.

### Mã nguồn Java
```java
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<int[]> events = new ArrayList<>();
        for (int[] b : buildings) {
            events.add(new int[]{b[0], -b[2]});
            events.add(new int[]{b[1], b[2]});
        }
        
        events.sort((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });
        
        List<List<Integer>> res = new ArrayList<>();
        TreeMap<Integer, Integer> map = new TreeMap<>(Collections.reverseOrder());
        map.put(0, 1);
        int prevMaxHeight = 0;
        
        for (int[] event : events) {
            int x = event[0], h = event[1];
            if (h < 0) {
                map.put(-h, map.getOrDefault(-h, 0) + 1);
            } else {
                int count = map.get(h);
                if (count == 1) map.remove(h);
                else map.put(h, count - 1);
            }
            
            int currMaxHeight = map.firstKey();
            if (currMaxHeight != prevMaxHeight) {
                res.add(Arrays.asList(x, currMaxHeight));
                prevMaxHeight = currMaxHeight;
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N \log N)$ do sắp xếp và thao tác trên `TreeMap`.
- **Không gian:** $O(N)$ lưu sự kiện và TreeMap.

---

## 16. Sliding Window Median (LeetCode 480)
### Đề bài chi tiết
Cho mảng `nums` và một cửa sổ trượt kích thước `k`. Trả về một mảng chứa trung vị của mỗi cửa sổ trượt khi nó di chuyển từ trái sang phải.

### Phân tích thuật toán
- Đây là sự kết hợp giữa "Find Median from Data Stream" và "Sliding Window".
- Chúng ta dùng **hai TreeSet** lưu trữ `(index)` để vừa sắp xếp, vừa có thể xóa một giá trị bất kì trong $O(\log K)$ khi nó rời khỏi cửa sổ.
- Cần viết `Comparator` tuỳ chỉnh để so sánh giá trị mảng, nếu bằng nhau thì so sánh `index` để tránh mất dữ liệu trùng lặp.

### Mã nguồn Java
```java
class Solution {
    public double[] medianSlidingWindow(int[] nums, int k) {
        Comparator<Integer> comparator = (a, b) -> 
            nums[a] != nums[b] ? Integer.compare(nums[a], nums[b]) : Integer.compare(a, b);
            
        TreeSet<Integer> left = new TreeSet<>(comparator.reversed());
        TreeSet<Integer> right = new TreeSet<>(comparator);
        
        double[] res = new double[nums.length - k + 1];
        
        for (int i = 0; i < nums.length; i++) {
            left.add(i);
            right.add(left.pollFirst());
            if (right.size() > left.size()) {
                left.add(right.pollFirst());
            }
            
            if (i >= k - 1) {
                if (left.size() == right.size()) {
                    res[i - k + 1] = ((double) nums[left.first()] + nums[right.first()]) / 2.0;
                } else {
                    res[i - k + 1] = nums[left.first()];
                }
                
                int outIndex = i - k + 1;
                if (!left.remove(outIndex)) {
                    right.remove(outIndex);
                }
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N \log K)$ vì thêm và xóa trên TreeSet mất $O(\log K)$.
- **Không gian:** $O(K)$ cho kích thước của hai TreeSet.

---

## 17. Maximum Number of Robots Within Budget (LeetCode 2398)
### Đề bài chi tiết
Bạn có $n$ robot, mỗi robot có `chargeTimes` và `runningCosts`. Bạn chọn một mảng con liên tiếp các robot. Chi phí là: $\max(\text{chargeTimes}) + k \times \text{sum}(\text{runningCosts})$ với $k$ là số lượng robot. Trả về $k$ lớn nhất sao cho tổng chi phí không vượt quá `budget`.

### Phân tích thuật toán
- Áp dụng kỹ thuật **Sliding Window** mở rộng cửa sổ khi còn đủ ngân sách và thu hẹp bằng biến `left`.
- Sử dụng **Monotonic Deque** để liên tục cập nhật giá trị $\max(\text{chargeTimes})$ trong cửa sổ hiện tại trong thời gian $O(1)$.
- Deque lưu trữ các index và giữ giá trị phần tử giảm dần.

### Mã nguồn Java
```java
class Solution {
    public int maximumRobots(int[] chargeTimes, int[] runningCosts, long budget) {
        Deque<Integer> dq = new ArrayDeque<>();
        long currentSum = 0;
        int left = 0, maxRobots = 0;
        
        for (int right = 0; right < chargeTimes.length; right++) {
            currentSum += runningCosts[right];
            
            while (!dq.isEmpty() && chargeTimes[dq.peekLast()] <= chargeTimes[right]) {
                dq.pollLast();
            }
            dq.addLast(right);
            
            while (!dq.isEmpty() && chargeTimes[dq.peekFirst()] + (right - left + 1) * currentSum > budget) {
                if (dq.peekFirst() == left) {
                    dq.pollFirst();
                }
                currentSum -= runningCosts[left];
                left++;
            }
            maxRobots = Math.max(maxRobots, right - left + 1);
        }
        return maxRobots;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ vì mỗi chỉ số được đưa vào và loại khỏi Deque tối đa 1 lần.
- **Không gian:** $O(N)$ cho Monotonic Deque.

---

## 18. Number of Subarrays with Bounded Maximum (LeetCode 795)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và hai số `left`, `right`. Hãy đếm số lượng mảng con liên tiếp thỏa mãn điều kiện: phần tử lớn nhất trong mảng con đó nằm trong đoạn `[left, right]`.

### Phân tích thuật toán
- Bài này là một biến thể của Two Pointers/Greedy thay vì phải dùng cấu trúc Monotonic Queue nặng nề.
- Có thể dùng tư duy: Số mảng con có max $\le right$ trừ đi số mảng con có max $\le left - 1$.
- Bằng cách này ta dễ dàng đếm số mảng con chỉ chứa các phần tử thỏa mãn.

### Mã nguồn Java
```java
class Solution {
    public int numSubarrayBoundedMax(int[] nums, int left, int right) {
        return countValid(nums, right) - countValid(nums, left - 1);
    }
    
    private int countValid(int[] nums, int bound) {
        int res = 0, current = 0;
        for (int num : nums) {
            if (num <= bound) {
                current++;
                res += current;
            } else {
                current = 0;
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ quét mảng 2 lần.
- **Không gian:** $O(1)$ chỉ sử dụng vài biến tích lũy.

---

## 19. Defuse the Bomb (LeetCode 1652)
### Đề bài chi tiết
Cho một mảng tuần hoàn `code` và khóa `k`. Để giải mã:
- Nếu $k > 0$, mỗi số được thay bằng tổng $k$ số tiếp theo.
- Nếu $k < 0$, mỗi số được thay bằng tổng $k$ số trước đó.
- Nếu $k = 0$, thay tất cả bằng 0.

### Phân tích thuật toán
- Do mảng quay vòng tuần hoàn (Circular), ta có thể áp dụng modulo `(i % n)` khi truy cập.
- Sử dụng **Sliding Window (Queue Pattern)** để duy trì tổng của $k$ phần tử. Khi tiến lên 1 bước, cộng thêm phần tử mới và trừ phần tử cũ sẽ tối ưu hơn tính toán lại từ đầu.

### Mã nguồn Java
```java
class Solution {
    public int[] decrypt(int[] code, int k) {
        int n = code.length;
        int[] res = new int[n];
        if (k == 0) return res;
        
        int start = 1, end = k, sum = 0;
        if (k < 0) {
            start = n - Math.abs(k);
            end = n - 1;
        }
        
        for (int i = start; i <= end; i++) {
            sum += code[i % n];
        }
        
        for (int i = 0; i < n; i++) {
            res[i] = sum;
            sum -= code[start % n];
            start++;
            end++;
            sum += code[end % n];
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$ (không tính mảng kết quả).

---

## 20. Task Scheduler (LeetCode 621)
### Đề bài chi tiết
Cho mảng `tasks` thể hiện các công việc CPU và thời gian chờ `n`. Phải có ít nhất `n` đơn vị thời gian chờ giữa hai tác vụ cùng loại. Có thể thực hiện tác vụ khác hoặc nghỉ. Trả về thời gian tối thiểu để hoàn thành.

### Phân tích thuật toán
- Sử dụng **Max Heap** để ưu tiên thực hiện các tác vụ còn nhiều nhất.
- Dùng một **Queue** (Hàng đợi) để làm khu vực chờ (cooldown). Sau khi xử lý một tác vụ, nếu chưa hoàn thành, nó bị đẩy vào Queue với "thời gian sẵn sàng" là `time + n`.
- Khi đến thời điểm phù hợp, phần tử từ Queue được đưa lại vào Max Heap.

### Mã nguồn Java
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26];
        for (char c : tasks) counts[c - 'A']++;
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int count : counts) {
            if (count > 0) maxHeap.add(count);
        }
        
        // Lưu trữ {số lượng còn lại, thời gian khả dụng}
        Queue<int[]> queue = new ArrayDeque<>();
        int time = 0;
        
        while (!maxHeap.isEmpty() || !queue.isEmpty()) {
            time++;
            
            if (!maxHeap.isEmpty()) {
                int count = maxHeap.poll() - 1;
                if (count > 0) {
                    queue.add(new int[]{count, time + n});
                }
            }
            
            if (!queue.isEmpty() && queue.peek()[1] == time) {
                maxHeap.add(queue.poll()[0]);
            }
        }
        return time;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ khi $N$ là tổng số task. Thao tác trên Queue và Heap tốn $O(1)$ vì số loại task lớn nhất là 26.
- **Không gian:** $O(1)$ vì các bộ nhớ Heap, mảng count kích thước tối đa 26.

---

## 21. Number of Visible People in a Queue (LeetCode 1944)
### Đề bài chi tiết
Có $n$ người đang xếp hàng chờ đợi, cho mảng `heights` chứa chiều cao của từng người (từ trái qua phải). Người thứ $i$ có thể nhìn thấy người thứ $j$ nếu $i < j$ và `min(heights[i], heights[j]) > max(heights[i+1], ..., heights[j-1])`. Trả về mảng đếm số người mà mỗi người có thể nhìn thấy ở phía bên phải.

### Phân tích thuật toán
- Sử dụng **Monotonic Stack (Hàng đợi đơn điệu)** duyệt từ phải qua trái.
- Tại mỗi người, ta loại bỏ các phần tử trong Stack có chiều cao nhỏ hơn hoặc bằng chiều cao của họ. Mỗi lần loại bỏ, người này đã nhìn thấy người bị loại bỏ.
- Nếu Stack vẫn còn phần tử sau vòng lặp, tức là người này sẽ nhìn thấy thêm 1 người nữa (người cao hơn đầu tiên chặn tầm nhìn của họ).
- Sau đó đưa người hiện tại vào Stack để phục vụ các bước tiếp theo.

### Mã nguồn Java
```java
class Solution {
    public int[] canSeePersonsCount(int[] heights) {
        int n = heights.length;
        int[] res = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = n - 1; i >= 0; i--) {
            int count = 0;
            while (!stack.isEmpty() && heights[i] > stack.peekFirst()) {
                stack.pollFirst();
                count++;
            }
            if (!stack.isEmpty()) {
                count++;
            }
            res[i] = count;
            stack.addFirst(heights[i]); // Thêm vào đầu Deque (hoạt động như Stack)
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** $O(N)$ vì mỗi phần tử được đẩy vào và lấy ra khỏi Stack tối đa 1 lần.
- **Không gian (Space):** $O(N)$ cho Monotonic Stack.

---

## 22. Daily Temperatures (LeetCode 739)
### Đề bài chi tiết
Cho mảng số nguyên `temperatures` đại diện cho nhiệt độ hàng ngày. Trả về mảng `answer` sao cho `answer[i]` là số ngày phải chờ sau ngày thứ $i$ để có một ngày ấm hơn. Nếu không có ngày nào trong tương lai ấm hơn, hãy để `answer[i] == 0`.

### Phân tích thuật toán
- Đây là bài toán tìm **Next Greater Element**. 
- Dùng **Monotonic Stack** (lưu index) duy trì các phần tử giảm dần. Khi duyệt mảng, nếu gặp một ngày có nhiệt độ lớn hơn nhiệt độ của ngày đang ở trên đỉnh Stack, tức là ta đã tìm được "ngày ấm hơn gần nhất".
- Ta lấy index ở đỉnh Stack ra và tính khoảng cách số ngày.

### Mã nguồn Java
```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] res = new int[n];
        Deque<Integer> stack = new ArrayDeque<>(); // Lưu trữ index
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peekFirst()]) {
                int prevIndex = stack.pollFirst();
                res[prevIndex] = i - prevIndex;
            }
            stack.addFirst(i);
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$ vì mỗi ngày đẩy vào và lấy ra tối đa 1 lần.
- **Không gian:** $O(N)$ cho Stack chứa các index.

---

## 23. Stock Price Fluctuation (LeetCode 2034)
### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu để theo dõi dữ liệu cổ phiếu: cập nhật giá ở một thời điểm, lấy giá hiện tại (giá ở thời điểm muộn nhất), giá lớn nhất, và giá nhỏ nhất. Các cập nhật có thể điều chỉnh giá ở các mốc thời gian quá khứ.

### Phân tích thuật toán
- Dùng `HashMap` lưu trữ ánh xạ `timestamp -> price` để cập nhật giá trị.
- Dùng `TreeMap` lưu tần suất xuất hiện của các mức giá để lấy min/max trong $O(\log N)$.
- Duy trì một biến lưu trữ `timestamp` lớn nhất để trả về Current Price.

### Mã nguồn Java
```java
class StockPrice {
    int latestTime;
    Map<Integer, Integer> timeToPrice;
    TreeMap<Integer, Integer> priceCount;

    public StockPrice() {
        latestTime = 0;
        timeToPrice = new HashMap<>();
        priceCount = new TreeMap<>();
    }
    
    public void update(int timestamp, int price) {
        latestTime = Math.max(latestTime, timestamp);
        if (timeToPrice.containsKey(timestamp)) {
            int oldPrice = timeToPrice.get(timestamp);
            int count = priceCount.get(oldPrice);
            if (count == 1) {
                priceCount.remove(oldPrice);
            } else {
                priceCount.put(oldPrice, count - 1);
            }
        }
        timeToPrice.put(timestamp, price);
        priceCount.put(price, priceCount.getOrDefault(price, 0) + 1);
    }
    
    public int current() {
        return timeToPrice.get(latestTime);
    }
    
    public int maximum() {
        return priceCount.lastKey();
    }
    
    public int minimum() {
        return priceCount.firstKey();
    }
}
```
### Độ phức tạp
- **Thời gian:** `update` tốn $O(\log N)$, các thao tác khác $O(1)$.
- **Không gian:** $O(N)$.

---

## 24. Number of Valid Subarrays (LeetCode 1063)
### Đề bài chi tiết
Cho mảng số nguyên `nums`. Trả về số lượng mảng con liên tiếp không rỗng sao cho phần tử đầu tiên bên trái của mảng con đó cũng chính là phần tử nhỏ nhất trong mảng con đó.

### Phân tích thuật toán
- Sử dụng **Monotonic Stack (Hàng đợi đơn điệu)** lưu trữ các phần tử theo thứ tự tăng dần.
- Khi thêm một phần tử `nums[i]`, nếu nó phá vỡ tính đơn điệu (nhỏ hơn đỉnh), ta loại phần tử lớn hơn ra.
- Đối với mỗi phần tử được xét ở vị trí $i$, nó sẽ mở rộng hợp lệ tất cả các mảng con khởi đầu bởi những phần tử đang tồn tại trong Stack (vì chúng $\le nums[i]$). Do đó, `stack.size()` chính là số mảng con hợp lệ kết thúc tại vị trí $i$.

### Mã nguồn Java
```java
class Solution {
    public int validSubarrays(int[] nums) {
        int res = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < nums.length; i++) {
            while (!stack.isEmpty() && nums[i] < nums[stack.peekFirst()]) {
                stack.pollFirst();
            }
            stack.addFirst(i);
            res += stack.size();
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$

---

## 25. Maximum Earnings From Taxi (LeetCode 2008)
### Đề bài chi tiết
Có $n$ điểm đỗ xe taxi. Bạn nhận được mảng `rides` với thông tin `[start, end, tip]`. Lợi nhuận kiếm được của chuyến là `end - start + tip`. Hãy tính lợi nhuận lớn nhất bạn có thể kiếm được, biết rằng bạn không thể chở hai khách cùng lúc.

### Phân tích thuật toán
- Áp dụng **Quy hoạch động (DP)**. `dp[i]` là lợi nhuận lớn nhất đạt được tới điểm $i$.
- Sắp xếp các chuyến xe theo điểm kết thúc (`end`).
- Với mỗi $i$, ta cập nhật `dp[i] = dp[i-1]` (không chở khách). Sau đó, kiểm tra các chuyến kết thúc tại $i$, nếu nhận khách thì `dp[i] = max(dp[i], dp[start] + (end - start + tip))`.

### Mã nguồn Java
```java
class Solution {
    public long maxTaxiEarnings(int n, int[][] rides) {
        Arrays.sort(rides, (a, b) -> Integer.compare(a[1], b[1]));
        long[] dp = new long[n + 1];
        int j = 0;
        
        for (int i = 1; i <= n; i++) {
            dp[i] = dp[i - 1];
            while (j < rides.length && rides[j][1] == i) {
                int start = rides[j][0];
                int end = rides[j][1];
                int tip = rides[j][2];
                dp[i] = Math.max(dp[i], dp[start] + (end - start + tip));
                j++;
            }
        }
        return dp[n];
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N + M \log M)$ (với $N$ là số điểm và $M$ là số lượng chuyến).
- **Không gian:** $O(N)$.

---

## 26. Maximal Rectangle (LeetCode 85)
### Đề bài chi tiết
Cho ma trận nhị phân 2D gồm các kí tự `'0'` và `'1'`, hãy tìm diện tích của hình chữ nhật lớn nhất chỉ chứa `'1'` và trả về diện tích đó.

### Phân tích thuật toán
- Tính mảng độ cao `heights` cho từng dòng, chuyển bài toán thành tìm diện tích hình chữ nhật lớn nhất trong Histogram (tương tự **LeetCode 84**).
- Với mỗi dòng Histogram, ta dùng **Monotonic Stack** duy trì các độ cao tăng dần để xác định giới hạn mở rộng trái, phải của từng cột.

### Mã nguồn Java
```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int[] heights = new int[n];
        int maxArea = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') heights[j]++;
                else heights[j] = 0;
            }
            maxArea = Math.max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }
    
    private int largestRectangleArea(int[] heights) {
        int maxArea = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        int[] h = new int[heights.length + 1];
        System.arraycopy(heights, 0, h, 0, heights.length);
        
        for (int i = 0; i < h.length; i++) {
            while (!stack.isEmpty() && h[i] < h[stack.peekFirst()]) {
                int height = h[stack.pollFirst()];
                int width = stack.isEmpty() ? i : i - stack.peekFirst() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.addFirst(i);
        }
        return maxArea;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(M \times N)$ duyệt mọi ô, Monotonic Stack xử lý $O(N)$ mỗi dòng.
- **Không gian:** $O(N)$.

---

## 27. Maximum Subarray Min-Product (LeetCode 1856)
### Đề bài chi tiết
Min-product của mảng là giá trị nhỏ nhất của mảng nhân với tổng mảng. Cho mảng `nums`, trả về min-product lớn nhất của mọi mảng con liên tiếp không rỗng. Kết quả chia dư cho $10^9 + 7$.

### Phân tích thuật toán
- Dùng **Monotonic Stack** tìm ra vị trí nhỏ hơn gần nhất ở bên trái và bên phải để biết giới hạn phần tử $i$ làm cực tiểu.
- Dùng **Prefix Sum** lấy tổng đoạn $O(1)$.
- Xét qua từng vị trí, nhân $nums[i]$ với tổng đoạn hợp lệ lớn nhất.

### Mã nguồn Java
```java
class Solution {
    public int maxSumMinProduct(int[] nums) {
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + nums[i];
        
        Deque<Integer> stack = new ArrayDeque<>();
        int[] nextSmaller = new int[n];
        int[] prevSmaller = new int[n];
        Arrays.fill(nextSmaller, n);
        Arrays.fill(prevSmaller, -1);
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peekFirst()] > nums[i]) {
                nextSmaller[stack.pollFirst()] = i;
            }
            if (!stack.isEmpty()) {
                prevSmaller[i] = stack.peekFirst();
            }
            stack.addFirst(i);
        }
        
        long maxProd = 0;
        for (int i = 0; i < n; i++) {
            long sum = prefix[nextSmaller[i]] - prefix[prevSmaller[i] + 1];
            maxProd = Math.max(maxProd, sum * nums[i]);
        }
        return (int) (maxProd % 1_000_000_007);
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$
- **Không gian:** $O(N)$

---

## 28. Shortest Subarray to be Removed to Make Array Sorted (LeetCode 1574)
### Đề bài chi tiết
Cho mảng `arr`, xóa bỏ một mảng con liên tiếp (có thể rỗng) để mảng còn lại được sắp xếp không giảm. Tìm độ dài mảng con bị xóa ngắn nhất.

### Phân tích thuật toán
- Hai con trỏ: Xác định mảng con tiền tố tăng dần dài nhất (prefix) và hậu tố tăng dần dài nhất (suffix).
- Duyệt qua tiền tố, sử dụng con trỏ $j$ lướt trên hậu tố để tìm cách nối một đoạn ở tiền tố với đoạn hậu tố mà vẫn giữ tính sắp xếp.
- Tính min khoảng cách mảng con cần bỏ đi (ở giữa).

### Mã nguồn Java
```java
class Solution {
    public int findLengthOfShortestSubarray(int[] arr) {
        int n = arr.length;
        int left = 0, right = n - 1;
        
        while (left < n - 1 && arr[left] <= arr[left + 1]) left++;
        if (left == n - 1) return 0;
        
        while (right > 0 && arr[right - 1] <= arr[right]) right--;
        
        int minLen = Math.min(n - left - 1, right);
        
        int i = 0, j = right;
        while (i <= left && j < n) {
            if (arr[i] <= arr[j]) {
                minLen = Math.min(minLen, j - i - 1);
                i++;
            } else {
                j++;
            }
        }
        return minLen;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.

---

## 29. Count Number of Nice Subarrays (LeetCode 1248)
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Một mảng con gọi là "tốt" (nice) nếu nó có đúng `k` số lẻ. Trả về số lượng mảng con như vậy.

### Phân tích thuật toán
- Áp dụng kỹ thuật **Sliding Window**. Số lượng mảng con có đúng $k$ số lẻ bằng số mảng con có tối đa $k$ số lẻ trừ đi số mảng con có tối đa $k-1$ số lẻ.
- Sử dụng hàm `atMost(k)` đếm các subarray.

### Mã nguồn Java
```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    
    private int atMost(int[] nums, int k) {
        int res = 0, left = 0, count = 0;
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] % 2 != 0) count++;
            
            while (count > k) {
                if (nums[left] % 2 != 0) count--;
                left++;
            }
            res += right - left + 1;
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.

---

## 30. Maximum Points You Can Obtain from Cards (LeetCode 1423)
### Đề bài chi tiết
Bạn có một hàng bài `cardPoints`. Mỗi lần được chọn 1 lá từ 2 đầu mảng. Lấy đúng `k` lá. Trả về tổng điểm lớn nhất có thể đạt được.

### Phân tích thuật toán
- Lấy $k$ lá ở hai đầu tương đương với việc bỏ đi một cửa sổ liên tiếp có độ dài $n - k$ ở giữa mảng sao cho tổng cửa sổ đó là **nhỏ nhất**.
- Dùng **Sliding Window** độ dài $n - k$ trượt qua mảng để tìm tổng nhỏ nhất.
- Tổng điểm max bằng tổng tất cả các thẻ bài trừ đi giá trị cực tiểu của cửa sổ.

### Mã nguồn Java
```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int windowSize = n - k;
        int totalSum = 0, currentWindowSum = 0;
        
        for (int i = 0; i < windowSize; i++) {
            currentWindowSum += cardPoints[i];
            totalSum += cardPoints[i];
        }
        
        int minWindowSum = currentWindowSum;
        
        for (int i = windowSize; i < n; i++) {
            totalSum += cardPoints[i];
            currentWindowSum += cardPoints[i] - cardPoints[i - windowSize];
            minWindowSum = Math.min(minWindowSum, currentWindowSum);
        }
        
        return totalSum - minWindowSum;
    }
}
```
### Độ phức tạp
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.
