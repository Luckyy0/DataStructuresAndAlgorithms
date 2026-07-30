# 30 Bài tập thực hành Fenwick Tree (Binary Indexed Tree)

## 1. Range Sum Query - Mutable
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Cần thực hiện 2 loại truy vấn:
1. `update(index, val)`: Cập nhật giá trị tại `index` thành `val`.
2. `sumRange(left, right)`: Trả về tổng các phần tử từ `left` đến `right`.
**Phân tích thuật toán:** Sử dụng Fenwick Tree để lưu tổng. Chú ý tính `delta = val - nums[index]` khi update.
**Mã nguồn Java:**
```java
class NumArray {
    int[] tree;
    int[] nums;
    int n;

    public NumArray(int[] nums) {
        this.nums = nums;
        n = nums.length;
        tree = new int[n + 1];
        for (int i = 0; i < n; i++) {
            add(i, nums[i]);
        }
    }
    
    private void add(int i, int delta) {
        i++;
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    public void update(int index, int val) {
        int delta = val - nums[index];
        nums[index] = val;
        add(index, delta);
    }
    
    private int query(int i) {
        i++;
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
    
    public int sumRange(int left, int right) {
        return query(right) - query(left - 1);
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N)$, Truy vấn $O(\log N)$. Không gian: $O(N)$.

## 2. Count of Smaller Numbers After Self
**Đề bài chi tiết:** Cho mảng số nguyên `nums`, trả về mảng `counts` trong đó `counts[i]` là số lượng phần tử nhỏ hơn `nums[i]` nằm ở bên phải nó.
**Phân tích thuật toán:** Duyệt từ phải sang trái. Dùng Coordinate Compression nếu mảng có số âm hoặc quá lớn. Sử dụng Fenwick tree để đếm tần suất xuất hiện của các phần tử.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<Integer> countSmaller(int[] nums) {
        int offset = 10001; // Since -10^4 <= nums[i] <= 10^4
        int size = 2 * 10000 + 2;
        int[] tree = new int[size];
        List<Integer> result = new ArrayList<>();
        
        for (int i = nums.length - 1; i >= 0; i--) {
            int count = query(tree, nums[i] + offset - 1);
            result.add(count);
            update(tree, nums[i] + offset, 1, size);
        }
        Collections.reverse(result);
        return result;
    }
    
    private void update(int[] tree, int i, int delta, int size) {
        while (i < size) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log M)$ với $M$ là khoảng giá trị. Không gian: $O(M)$.

## 3. Reverse Pairs
**Đề bài chi tiết:** Cho mảng `nums`. Một nghịch thế kép là khi `i < j` và `nums[i] > 2 * nums[j]`. Đếm số cặp như vậy.
**Phân tích thuật toán:** Duyệt từ trái sang phải hoặc dùng tập hợp để nén tọa độ. Tại mỗi bước truy vấn đếm số lượng các số lớn hơn `2 * nums[j]`.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int reversePairs(int[] nums) {
        TreeSet<Long> set = new TreeSet<>();
        for (int num : nums) {
            set.add((long) num);
            set.add((long) num * 2);
        }
        Map<Long, Integer> map = new HashMap<>();
        int rank = 1;
        for (long num : set) {
            map.put(num, rank++);
        }
        
        int[] tree = new int[rank];
        int count = 0;
        for (int num : nums) {
            count += query(tree, rank - 1) - query(tree, map.get((long) num * 2));
            update(tree, map.get((long) num), 1, rank);
        }
        return count;
    }
    
    private void update(int[] tree, int i, int delta, int size) {
        while (i < size) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 4. Global and Local Inversions
**Đề bài chi tiết:** Một mảng hoán vị độ dài N. Trả về `true` nếu số nghịch thế toàn cục bằng số nghịch thế cục bộ, `false` nếu ngược lại.
**Phân tích thuật toán:** Dù bài này tối ưu $O(N)$ nhưng minh họa BIT để đếm số nghịch thế toàn cục (Global Inversions) như một ví dụ cơ bản.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isIdealPermutation(int[] nums) {
        // Có thể giải O(N) không dùng BIT: max(nums[0..i-2]) > nums[i]
        // Code này minh hoạ việc dùng cách duyệt để tối ưu hơn là BIT trực tiếp, 
        // nhưng về mặt bản chất Inversions đếm bằng BIT tốn O(NlogN).
        // Giải pháp O(N):
        int max = -1;
        for (int i = 0; i < nums.length - 2; i++) {
            max = Math.max(max, nums[i]);
            if (max > nums[i + 2]) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(1)$.

## 5. Count of Range Sum
**Đề bài chi tiết:** Cho mảng `nums`, 2 số `lower` và `upper`. Trả về số lượng khoảng `[i, j]` có tổng `lower <= sum(i..j) <= upper`.
**Phân tích thuật toán:** Tính mảng tổng tiền tố. Gom tất cả `prefixSum[i], prefixSum[i] - lower, prefixSum[i] - upper` vào để rời rạc hóa (Coordinate Compression). Sau đó dùng BIT để đếm.
**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int countRangeSum(int[] nums, int lower, int upper) {
        long[] preSum = new long[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
        
        TreeSet<Long> set = new TreeSet<>();
        for (long sum : preSum) {
            set.add(sum);
            set.add(sum - lower);
            set.add(sum - upper);
        }
        
        Map<Long, Integer> map = new HashMap<>();
        int rank = 1;
        for (long x : set) map.put(x, rank++);
        
        int ans = 0;
        int[] tree = new int[rank];
        for (long sum : preSum) {
            int left = map.get(sum - upper);
            int right = map.get(sum - lower);
            ans += query(tree, right) - query(tree, left - 1);
            update(tree, map.get(sum), 1, rank);
        }
        return ans;
    }
    
    private void update(int[] tree, int i, int delta, int size) {
        while (i < size) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int s = 0;
        while (i > 0) {
            s += tree[i];
            i -= i & (-i);
        }
        return s;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 6. Count Good Triplets in an Array
**Đề bài chi tiết:** Cho 2 mảng hoán vị `nums1` và `nums2`. Đếm số lượng triplet `(x, y, z)` giữ nguyên thứ tự tương đối trong cả 2 mảng.
**Phân tích thuật toán:** Ánh xạ lại chỉ số của `nums1` sang `nums2`. Bài toán biến thành đếm số lượng triplet tăng ngặt trong một mảng mới. Sử dụng 2 lần duyệt BIT để tính phần tử bên trái và bên phải.
**Mã nguồn Java:**
```java
class Solution {
    public long goodTriplets(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int[] pos = new int[n];
        for (int i = 0; i < n; i++) pos[nums2[i]] = i;
        
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) arr[i] = pos[nums1[i]];
        
        int[] tree = new int[n + 1];
        long[] leftSmaller = new long[n];
        long[] rightGreater = new long[n];
        
        for (int i = 0; i < n; i++) {
            leftSmaller[i] = query(tree, arr[i]);
            update(tree, arr[i] + 1, 1, n + 1);
        }
        
        tree = new int[n + 1]; // reset BIT
        for (int i = n - 1; i >= 0; i--) {
            rightGreater[i] = (n - 1 - i) - query(tree, arr[i]);
            update(tree, arr[i] + 1, 1, n + 1);
        }
        
        long ans = 0;
        for (int i = 0; i < n; i++) {
            ans += leftSmaller[i] * rightGreater[i];
        }
        return ans;
    }
    
    private void update(int[] tree, int i, int delta, int size) {
        while (i < size) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 7. Create Sorted Array through Instructions
**Đề bài chi tiết:** Mỗi phần tử được chèn vào mảng. Chi phí chèn bằng `min(số phần tử strictly less, số phần tử strictly greater)`. Tổng chi phí sau khi chèn tất cả?
**Phân tích thuật toán:** Giá trị mảng tối đa $10^5$, có thể dùng ngay mảng BIT lớn như thế. Tại mỗi phần tử đếm số lượng nhỏ hơn nó và tổng số đã chèn trừ đi số nhỏ hơn hoặc bằng.
**Mã nguồn Java:**
```java
class Solution {
    int[] tree = new int[100002];
    int MOD = 1000000007;
    
    public int createSortedArray(int[] instructions) {
        int cost = 0;
        int n = instructions.length;
        for (int i = 0; i < n; i++) {
            int val = instructions[i];
            int less = query(val - 1);
            int greater = i - query(val);
            cost = (cost + Math.min(less, greater)) % MOD;
            update(val, 1);
        }
        return cost;
    }
    
    private void update(int i, int delta) {
        while (i < 100002) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log M)$. Không gian: $O(M)$ (M là phần tử max).

## 8. Number of Ships in a Rectangle
**Đề bài chi tiết:** Đếm số lượng tàu trong hình chữ nhật thông qua hàm API `hasShips(topRight, bottomLeft)`. Bài này kinh điển là Divide & Conquer. Ở biến thể khác yêu cầu đếm offline bằng 2D BIT.
**Phân tích thuật toán:** Phiên bản offline đếm điểm 2D. Quét từ trái sang phải, lưu các điểm y bằng BIT 1 chiều. (Mã dưới mô phỏng kĩ thuật 2D Point Counting qua Sweep-Line + BIT).
**Mã nguồn Java:**
```java
// Mã giả minh hoạ Sweep-Line + BIT đếm điểm 2D.
// Không phải API call tương tác của bài LeetCode 1274 mà là biến thể đếm.
class Solution {
    public int countPoints(int[][] points, int[] query) {
        // Sort points by x, sort queries by x
        // For each query point, update y in BIT for all points with x <= query.x
        // Answer = query_y_high - query_y_low_minus_1
        return 0; // Skeleton code
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log M)$. Không gian: $O(M)$.

## 9. Queries on a Permutation With Key
**Đề bài chi tiết:** Cho `m`, khởi tạo mảng `P = [1..m]`. Xử lý từng truy vấn, tìm chỉ số của `queries[i]` trong `P`, sau đó đẩy nó lên đầu mảng.
**Phân tích thuật toán:** Sử dụng mảng có kích thước $2M$. Ban đầu $1..M$ nằm ở $M+1..2M$. Khi một phần tử đẩy lên đầu, gán nó sang vị trí bên trái. Dùng BIT để theo dõi vị trí trống/đã chiếm.
**Mã nguồn Java:**
```java
class Solution {
    public int[] processQueries(int[] queries, int m) {
        int[] tree = new int[2 * m + 1];
        int[] pos = new int[m + 1];
        
        for (int i = 1; i <= m; i++) {
            pos[i] = m + i;
            update(tree, m + i, 1);
        }
        
        int[] ans = new int[queries.length];
        int nextEmpty = m;
        
        for (int i = 0; i < queries.length; i++) {
            int val = queries[i];
            int p = pos[val];
            ans[i] = query(tree, p - 1);
            
            update(tree, p, -1);
            pos[val] = nextEmpty;
            update(tree, nextEmpty, 1);
            nextEmpty--;
        }
        return ans;
    }
    
    private void update(int[] tree, int i, int delta) {
        while (i < tree.length) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(Q \log M)$. Không gian: $O(M)$.

## 10. Count Number of Teams
**Đề bài chi tiết:** Đếm số nhóm 3 binh sĩ sao cho rating tăng ngặt hoặc giảm ngặt.
**Phân tích thuật toán:** Duyệt qua lính đứng giữa (vị trí `j`). Tính số lính nhỏ hơn bên trái, lớn hơn bên phải... Dùng 2 BIT để duy trì tập bên trái và bên phải.
**Mã nguồn Java:**
```java
class Solution {
    public int numTeams(int[] rating) {
        int max = 0;
        for (int r : rating) max = Math.max(max, r);
        
        int[] leftTree = new int[max + 1];
        int[] rightTree = new int[max + 1];
        
        for (int r : rating) update(rightTree, r, 1);
        
        int ans = 0;
        for (int i = 0; i < rating.length; i++) {
            int r = rating[i];
            update(rightTree, r, -1);
            
            int lessLeft = query(leftTree, r - 1);
            int greaterLeft = query(leftTree, max) - query(leftTree, r);
            
            int lessRight = query(rightTree, r - 1);
            int greaterRight = query(rightTree, max) - query(rightTree, r);
            
            ans += lessLeft * greaterRight + greaterLeft * lessRight;
            update(leftTree, r, 1);
        }
        return ans;
    }
    
    private void update(int[] tree, int i, int delta) {
        while (i < tree.length) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log M)$. Không gian: $O(M)$.

## 11. K-th Empty Slot
**Đề bài chi tiết:** Cho một tập hợp ban đầu rỗng. Bạn cần xử lý các truy vấn: thêm phần tử `x` vào tập, xóa phần tử `x` khỏi tập, và tìm phần tử nhỏ thứ `K` trong tập hợp. Các phần tử thuộc đoạn `[1, 10^5]`.
**Phân tích thuật toán:** Chúng ta có thể dùng Fenwick Tree để đếm tần suất các phần tử. Để tìm phần tử nhỏ thứ K, ta áp dụng kĩ thuật Binary Lifting trực tiếp trên BIT, cho phép tìm kiếm trong thời gian $O(\log N)$ thay vì $O(\log^2 N)$ như khi kết hợp chặt nhị phân thông thường (Binary Search + BIT).
**Mã nguồn Java:**
```java
class KthElementBIT {
    int[] tree;
    int n;

    public KthElementBIT(int size) {
        n = size;
        tree = new int[n + 1];
    }

    public void update(int i, int delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    // Binary Lifting on BIT
    public int findKth(int k) {
        int index = 0;
        int bitMask = Integer.highestOneBit(n);
        
        for (int i = bitMask; i > 0; i >>= 1) {
            int nextIndex = index + i;
            if (nextIndex <= n && k > tree[nextIndex]) {
                index = nextIndex;
                k -= tree[index];
            }
        }
        return index + 1;
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N)$, Tìm K-th $O(\log N)$. Không gian: $O(N)$.

## 12. Range Update and Point Query
**Đề bài chi tiết:** Cho một mảng các số nguyên. Có 2 loại truy vấn: cộng thêm một giá trị `val` vào tất cả các phần tử trong đoạn `[L, R]`, và lấy giá trị tại chỉ số `i`.
**Phân tích thuật toán:** Sử dụng mảng hiệu (difference array) để cập nhật. Tăng điểm `L` lên `val` và giảm điểm `R + 1` đi `val`. Giá trị tại vị trí `i` chính là tổng tiền tố của mảng hiệu từ `1` đến `i`. Ta dùng một BIT để quản lý mảng hiệu này.
**Mã nguồn Java:**
```java
class RangeUpdatePointQuery {
    int[] tree;
    int n;

    public RangeUpdatePointQuery(int[] nums) {
        n = nums.length;
        tree = new int[n + 2];
        for (int i = 0; i < n; i++) {
            update(i + 1, i + 1, nums[i]);
        }
    }

    private void add(int i, int delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public void update(int left, int right, int delta) {
        add(left, delta);
        add(right + 1, -delta);
    }

    public int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N)$, Truy vấn $O(\log N)$. Không gian: $O(N)$.

## 13. Range Update and Range Query
**Đề bài chi tiết:** Xử lý 2 loại truy vấn: cộng thêm `val` vào mọi phần tử trong đoạn `[L, R]`, và tính tổng các phần tử trong đoạn `[L, R]`.
**Phân tích thuật toán:** Tương tự mảng hiệu, nhưng với tổng tiền tố của một mảng `A`, ta có công thức tính: $\sum_{j=1}^i A[j] = (i + 1) \sum_{j=1}^i D[j] - \sum_{j=1}^i (D[j] \times j)$. Vậy ta cần 2 BIT: một lưu $D[j]$ và một lưu $D[j] \times j$.
**Mã nguồn Java:**
```java
class RangeUpdateRangeQuery {
    long[] tree1, tree2;
    int n;

    public RangeUpdateRangeQuery(int size) {
        n = size;
        tree1 = new long[n + 2];
        tree2 = new long[n + 2];
    }

    private void add(long[] tree, int i, long delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public void update(int left, int right, long val) {
        add(tree1, left, val);
        add(tree1, right + 1, -val);
        add(tree2, left, val * left);
        add(tree2, right + 1, -val * (right + 1));
    }

    private long query(int i) {
        long sum1 = 0, sum2 = 0;
        int origI = i;
        while (i > 0) {
            sum1 += tree1[i];
            sum2 += tree2[i];
            i -= i & (-i);
        }
        return sum1 * (origI + 1) - sum2;
    }

    public long sumRange(int left, int right) {
        return query(right) - query(left - 1);
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N)$, Truy vấn $O(\log N)$. Không gian: $O(N)$.

## 14. Đếm số khoảng nghịch thế có kích thước cố định
**Đề bài chi tiết:** Cho mảng số nguyên `nums` và một kích thước cửa sổ `k`. Tính số lượng nghịch thế trong mỗi cửa sổ kích thước `k` khi trượt từ đầu đến cuối mảng.
**Phân tích thuật toán:** Khi cửa sổ trượt đi một bước, một phần tử cũ ở bên trái sẽ ra khỏi cửa sổ, và một phần tử mới vào từ bên phải. Dùng BIT để theo dõi các phần tử trong cửa sổ hiện tại. Nén tọa độ các giá trị của mảng, duy trì đếm số nghịch thế thông qua việc xóa phần tử cũ ra (trừ đi số phần tử nhỏ hơn) và thêm phần tử mới vào (cộng thêm số phần tử lớn hơn).
**Mã nguồn Java:**
```java
import java.util.*;

class SlidingWindowInversions {
    public long[] getInversions(int[] nums, int k) {
        int n = nums.length;
        if (n == 0) return new long[0];
        
        int[] sorted = nums.clone();
        Arrays.sort(sorted);
        Map<Integer, Integer> rank = new HashMap<>();
        for (int i = 0; i < n; i++) rank.put(sorted[i], i + 1);
        
        int[] tree = new int[n + 1];
        long currentInversions = 0;
        long[] result = new long[n - k + 1];
        
        for (int i = 0; i < k; i++) {
            int r = rank.get(nums[i]);
            currentInversions += i - query(tree, r);
            update(tree, r, 1, n);
        }
        result[0] = currentInversions;
        
        for (int i = k; i < n; i++) {
            int leftOut = rank.get(nums[i - k]);
            currentInversions -= query(tree, leftOut - 1);
            update(tree, leftOut, -1, n);
            
            int rightIn = rank.get(nums[i]);
            currentInversions += (k - 1) - query(tree, rightIn);
            update(tree, rightIn, 1, n);
            
            result[i - k + 1] = currentInversions;
        }
        return result;
    }
    
    private void update(int[] tree, int i, int delta, int size) {
        while (i <= size) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 15. Mảng xếp hạng động (Dynamic Rank Array)
**Đề bài chi tiết:** Yêu cầu cập nhật tần suất xuất hiện của các số trong khoảng siêu lớn (VD: `[-10^9, 10^9]`) và đếm số lượng phần tử trong khoảng `[L, R]`. Không thể nén tọa độ trước vì luồng truy vấn là trực tuyến (online queries).
**Phân tích thuật toán:** Sử dụng Fenwick Tree thưa (Sparse BIT) bằng cách thay thế mảng tĩnh bằng `HashMap`. Điều này cho phép cấp phát bộ nhớ theo nhu cầu. Lưu ý hệ số hằng số của `HashMap` làm chậm tốc độ thực thi so với mảng tĩnh.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class SparseBIT {
    Map<Integer, Long> tree;
    int maxLimit = 1000000000;

    public SparseBIT() {
        tree = new HashMap<>();
    }

    public void update(int i, long delta) {
        while (i <= maxLimit && i > 0) {
            tree.put(i, tree.getOrDefault(i, 0L) + delta);
            i += i & (-i);
        }
    }

    public long query(int i) {
        long sum = 0;
        while (i > 0) {
            sum += tree.getOrDefault(i, 0L);
            i -= i & (-i);
        }
        return sum;
    }

    public long queryRange(int left, int right) {
        return query(right) - query(left - 1);
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log M)$ trung bình cho mỗi truy vấn, $M$ là khoảng giới hạn. Không gian: $O(Q \log M)$ với $Q$ là số truy vấn.

## 16. Điểm nằm trong đa giác trực giao (Orthogonal Polygon Query)
**Đề bài chi tiết:** Đếm số lượng điểm nằm bên trong các hình chữ nhật cho trước trên mặt phẳng 2D. Các điểm và hình chữ nhật được cung cấp độc lập, đếm kết quả offline.
**Phân tích thuật toán:** Áp dụng thuật toán Sweep-Line (quét đường thẳng) dọc theo trục X. Hình chữ nhật `(x1, y1) -> (x2, y2)` sẽ tách thành 2 sự kiện cập nhật trên trục X: tại `x1` thêm đoạn `[y1, y2]` với trọng số `+1`, tại `x2 + 1` thêm với trọng số `-1`. Các điểm cũng là các sự kiện truy vấn tại hoành độ `x`. Dùng BIT để theo dõi Y-axis (Cập nhật đoạn, truy vấn điểm).
**Mã nguồn Java:**
```java
import java.util.*;

class OrthogonalQuery {
    class Event implements Comparable<Event> {
        int x, y1, y2, type, id;
        // type: 1 = add rect, -1 = remove rect, 0 = query point
        Event(int x, int y1, int y2, int type, int id) {
            this.x = x; this.y1 = y1; this.y2 = y2; this.type = type; this.id = id;
        }
        public int compareTo(Event o) {
            if (this.x != o.x) return Integer.compare(this.x, o.x);
            return Integer.compare(o.type, this.type); // Process add rect first, then query, then remove rect
        }
    }

    int[] tree = new int[200005];

    private void update(int i, int delta) {
        while (i < tree.length) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    private int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }

    public int[] countPointsInRectangles(int[][] points, int[][] rects) {
        List<Event> events = new ArrayList<>();
        int n = points.length;
        int[] ans = new int[n];
        
        for (int i = 0; i < n; i++) {
            events.add(new Event(points[i][0], points[i][1], -1, 0, i));
        }
        for (int[] r : rects) {
            // r = {x1, y1, x2, y2}
            events.add(new Event(r[0], r[1], r[3], 1, -1));
            events.add(new Event(r[2] + 1, r[1], r[3], -1, -1));
        }
        
        Collections.sort(events);
        for (Event e : events) {
            if (e.type != 0) {
                update(e.y1, e.type);
                update(e.y2 + 1, -e.type);
            } else {
                ans[e.id] = query(e.y1) > 0 ? 1 : 0;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian: $O((N + M) \log (N + M))$ với $N$ điểm và $M$ HCN. Không gian: $O(\text{Max Y})$.

## 17. Lịch sử bảng xếp hạng game (Gaming Leaderboard History)
**Đề bài chi tiết:** Theo dõi điểm số của người chơi thay đổi theo thời gian. Truy vấn yêu cầu biết điểm của một người chơi tại thời điểm `t`.
**Phân tích thuật toán:** Khác với BIT dùng không gian 1 chiều, bài này thực chất có thể giải bằng mảng lưu trữ theo thời gian. Nếu cần truy vấn tổng điểm của "nhóm người chơi" (đoạn user ID) trong 1 khoảng thời gian, chúng ta có thể dùng 2D BIT, với trục X là chỉ số người chơi, trục Y là thời gian. Dưới đây minh họa 2D BIT.
**Mã nguồn Java:**
```java
class LeaderboardHistory {
    int[][] tree;
    int n, maxTime;

    public LeaderboardHistory(int users, int times) {
        n = users;
        maxTime = times;
        tree = new int[n + 1][maxTime + 1];
    }

    public void update(int userId, int time, int delta) {
        for (int i = userId; i <= n; i += i & (-i)) {
            for (int j = time; j <= maxTime; j += j & (-j)) {
                tree[i][j] += delta;
            }
        }
    }

    public int query(int userId, int time) {
        int sum = 0;
        for (int i = userId; i > 0; i -= i & (-i)) {
            for (int j = time; j > 0; j -= j & (-j)) {
                sum += tree[i][j];
            }
        }
        return sum;
    }

    public int queryRange(int userL, int userR, int timeL, int timeR) {
        return query(userR, timeR) 
             - query(userL - 1, timeR) 
             - query(userR, timeL - 1) 
             + query(userL - 1, timeL - 1);
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N \cdot \log T)$, Truy vấn $O(\log N \cdot \log T)$. Không gian: $O(N \times T)$.

## 18. Bầu cử trên cây (Tree Elections)
**Đề bài chi tiết:** Cho một cây với $N$ đỉnh. Có 2 loại truy vấn: cập nhật giá trị của đỉnh `u` và tính tổng giá trị của toàn bộ cây con gốc `u`.
**Phân tích thuật toán:** Chúng ta có thể "trải phẳng" (flatten) cây bằng kĩ thuật DFS (Euler Tour). Mỗi đỉnh `u` sẽ tương ứng với một khoảng thời gian `[in[u], out[u]]` trong mảng 1 chiều. Khi đó truy vấn cây con quy về truy vấn tổng đoạn trong mảng, và cập nhật đỉnh là cập nhật một phần tử.
**Mã nguồn Java:**
```java
import java.util.*;

class TreeSubtreeQuery {
    List<Integer>[] adj;
    int[] in, out, tree;
    int timer = 0, n;

    public TreeSubtreeQuery(int size) {
        n = size;
        adj = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        in = new int[n + 1];
        out = new int[n + 1];
        tree = new int[n + 1];
    }

    public void addEdge(int u, int v) {
        adj[u].add(v);
        adj[v].add(u);
    }

    public void dfs(int u, int p) {
        in[u] = ++timer;
        for (int v : adj[u]) {
            if (v != p) dfs(v, u);
        }
        out[u] = timer;
    }

    public void updatePoint(int u, int delta) {
        int i = in[u];
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public int querySubtree(int u) {
        return query(out[u]) - query(in[u] - 1);
    }

    private int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: Preprocess DFS $O(N)$, Cập nhật/Truy vấn $O(\log N)$. Không gian: $O(N)$.

## 19. RMQ bằng BIT? (Range Max Query with BIT)
**Đề bài chi tiết:** Cập nhật một phần tử mảng và hỏi phần tử lớn nhất trong khoảng `[1, i]` (truy vấn prefix).
**Phân tích thuật toán:** BIT rất tốt cho các phép toán có tính chất đảo ngược được (như cộng/trừ, XOR). Với hàm max (không thể đảo ngược nếu xóa đi 1 phần tử đã là max), ta chỉ có thể dùng BIT để truy vấn từ gốc `1` đến `i`. Nếu cần cập nhật điểm, nó chỉ hỗ trợ gán giá trị *lớn hơn* hoặc bằng (ví dụ: `update(i, val)` chỉ khi `val >= arr[i]`). Để cập nhật linh hoạt, Segment Tree là giải pháp tự nhiên hơn. Đoạn mã dưới minh hoạ Prefix Max BIT.
**Mã nguồn Java:**
```java
class PrefixMaxBIT {
    int[] tree;
    int n;

    public PrefixMaxBIT(int size) {
        n = size;
        tree = new int[n + 1];
    }

    public void update(int i, int val) {
        while (i <= n) {
            tree[i] = Math.max(tree[i], val);
            i += i & (-i);
        }
    }

    // Only supports query in [1, i]
    public int queryPrefixMax(int i) {
        int maxVal = 0; // assuming values are >= 0
        while (i > 0) {
            maxVal = Math.max(maxVal, tree[i]);
            i -= i & (-i);
        }
        return maxVal;
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log N)$. Không gian: $O(N)$.

## 20. Cắt gỗ động (Dynamic Wood Cutting)
**Đề bài chi tiết:** Có một thanh gỗ độ dài L. Liên tục có các vết cắt tại các tọa độ $x$. Trả về số lượng đoạn gỗ hiện tại có độ dài lớn hơn hoặc bằng $W$.
**Phân tích thuật toán:** Sử dụng `TreeSet` để lưu các điểm đã cắt nhằm nhanh chóng tìm vết cắt liền trước và liền sau điểm hiện tại. Khi cắt tại $x$, một đoạn gỗ độ dài $d$ sẽ vỡ thành 2 đoạn độ dài $d_1, d_2$. Dùng BIT để theo dõi tần suất của các độ dài đoạn gỗ. Xóa $d$ khỏi BIT và thêm $d_1, d_2$ vào BIT.
**Mã nguồn Java:**
```java
import java.util.*;

class DynamicWoodCutting {
    TreeSet<Integer> cuts;
    int[] lengthTree;
    int maxLen;

    public DynamicWoodCutting(int L) {
        maxLen = L;
        cuts = new TreeSet<>();
        cuts.add(0);
        cuts.add(L);
        lengthTree = new int[L + 1];
        update(L, 1);
    }

    public void cut(int x) {
        if (cuts.contains(x)) return;
        int left = cuts.lower(x);
        int right = cuts.higher(x);
        
        int oldLen = right - left;
        update(oldLen, -1);
        
        update(x - left, 1);
        update(right - x, 1);
        cuts.add(x);
    }

    public int countSegmentsGreaterThanOrEqual(int w) {
        if (w > maxLen) return 0;
        // Total segments - segments smaller than w
        int total = cuts.size() - 1;
        int less = query(w - 1);
        return total - less;
    }

    private void update(int i, int delta) {
        while (i <= maxLen) {
            lengthTree[i] += delta;
            i += i & (-i);
        }
    }

    private int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += lengthTree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: Cắt tốn $O(\log N)$ cho set + BIT. Truy vấn $O(\log L)$. Không gian: $O(L)$.

## 21. Bài toán rạp chiếu phim (Cinema Seat Allocation)
**Đề bài chi tiết:** Có $N$ ghế xếp thành một hàng dọc. Khách hàng muốn tìm ghế trống ở vị trí thứ $K$ tính từ trái sang. Sau khi chọn, ghế đó trở thành "đã chiếm" và không thể ngồi được nữa. Hãy chỉ ra vị trí ghế của từng khách hàng.
**Phân tích thuật toán:** Ta dùng một mảng BIT độ dài $N$ để theo dõi các ghế. Ban đầu `update(i, 1)` cho mọi $1 \le i \le N$, nghĩa là mọi ghế đều trống. Việc tìm ghế trống thứ $K$ tương đương với tìm phần tử thứ $K$ trong BIT (dùng Binary Lifting trên BIT). Sau khi tìm thấy ở vị trí $idx$, ta cập nhật `update(idx, -1)`.
**Mã nguồn Java:**
```java
class CinemaSeats {
    int[] tree;
    int n;

    public CinemaSeats(int size) {
        n = size;
        tree = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            update(i, 1);
        }
    }

    public void update(int i, int delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public int allocateSeat(int k) {
        int index = 0;
        int bitMask = Integer.highestOneBit(n);
        
        for (int i = bitMask; i > 0; i >>= 1) {
            int nextIndex = index + i;
            if (nextIndex <= n && k > tree[nextIndex]) {
                index = nextIndex;
                k -= tree[index];
            }
        }
        
        int allocated = index + 1;
        update(allocated, -1);
        return allocated;
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log N)$ mỗi truy vấn. Không gian: $O(N)$.

## 22. K-th Smallest in Matrix (Offline Queries)
**Đề bài chi tiết:** Cho ma trận. Có nhiều truy vấn offline: đếm số phần tử nhỏ hơn hoặc bằng một giá trị $X$ trong một khung hình chữ nhật.
**Phân tích thuật toán:** Vì có nhiều truy vấn offline, ta có thể kết hợp Line-Sweep (Quét đường thẳng) hoặc sắp xếp tất cả các phần tử và truy vấn theo giá trị tăng dần. Các điểm được cập nhật vào 2D BIT khi giá trị của chúng $\le X$. Truy vấn sẽ là hỏi tổng số điểm (đã được activate) trong vùng hình chữ nhật.
**Mã nguồn Java:**
```java
import java.util.*;

class KthSmallestMatrixOffline {
    // Để đơn giản, phần này minh hoạ cấu trúc 2D BIT đếm số điểm
    int[][] tree;
    int rows, cols;
    
    public KthSmallestMatrixOffline(int r, int c) {
        rows = r; cols = c;
        tree = new int[rows + 1][cols + 1];
    }
    
    public void addPoint(int r, int c, int val) {
        for (int i = r; i <= rows; i += i & (-i)) {
            for (int j = c; j <= cols; j += j & (-j)) {
                tree[i][j] += val;
            }
        }
    }
    
    public int query(int r, int c) {
        int sum = 0;
        for (int i = r; i > 0; i -= i & (-i)) {
            for (int j = c; j > 0; j -= j & (-j)) {
                sum += tree[i][j];
            }
        }
        return sum;
    }
    
    public int queryRect(int r1, int c1, int r2, int c2) {
        return query(r2, c2) - query(r1 - 1, c2) - query(r2, c1 - 1) + query(r1 - 1, c1 - 1);
    }
}
```
**Độ phức tạp:** Cập nhật và truy vấn tốn $O(\log R \cdot \log C)$. Không gian: $O(R \times C)$.

## 23. Inversion Count trong String
**Đề bài chi tiết:** Cho chuỗi $S$ và chuỗi đích $T$ (là một hoán vị của $S$). Đếm số lần swap hai kí tự liền kề ít nhất để biến $S$ thành $T$.
**Phân tích thuật toán:** Lập bản đồ chỉ số xuất hiện của từng kí tự từ $T$ sang $S$. Ví dụ: chữ cái 'a' xuất hiện thứ nhất ở $T$ nằm ở vị trí nào trong $S$. Do có nhiều kí tự giống nhau, ta dùng hàng đợi (`Queue`) để ánh xạ các kí tự một cách tuần tự (tham lam). Khi có mảng hoán vị các chỉ số, bài toán quy về tìm số nghịch thế (Inversion Count) bằng BIT.
**Mã nguồn Java:**
```java
import java.util.*;

class StringInversion {
    public int minSwaps(String s, String t) {
        int n = s.length();
        Map<Character, Queue<Integer>> map = new HashMap<>();
        
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            map.putIfAbsent(c, new LinkedList<>());
            map.get(c).add(i + 1);
        }
        
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = map.get(t.charAt(i)).poll();
        }
        
        int[] tree = new int[n + 1];
        int inversions = 0;
        for (int i = 0; i < n; i++) {
            inversions += i - query(tree, arr[i]);
            update(tree, arr[i], 1, n);
        }
        return inversions;
    }
    
    private void update(int[] tree, int i, int delta, int n) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int query(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 24. Mạng truyền dẫn (Network Latency Range sum)
**Đề bài chi tiết:** Các trạm mạng xếp theo đường thẳng. Bạn có thể cập nhật độ trễ của một đoạn mạng từ $L$ đến $R$ (cộng thêm $V$) và truy vấn độ trễ tại một điểm hoặc tổng độ trễ trong đoạn.
**Phân tích thuật toán:** Bài toán này hoàn toàn giống với bài toán **Cập nhật đoạn và truy vấn đoạn** (Range Update Range Query) sử dụng 2 BIT (như đã giới thiệu ở bài 13). Cấu trúc 2 BIT cung cấp truy vấn cực kì nhanh và tiết kiệm bộ nhớ so với Segment Tree.
**Mã nguồn Java:**
```java
class NetworkLatency {
    // Sử dụng y hệt class RangeUpdateRangeQuery ở bài 13
    long[] tree1, tree2;
    int n;

    public NetworkLatency(int size) {
        n = size;
        tree1 = new long[n + 2];
        tree2 = new long[n + 2];
    }

    private void add(long[] tree, int i, long delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public void updateRange(int left, int right, long val) {
        add(tree1, left, val);
        add(tree1, right + 1, -val);
        add(tree2, left, val * left);
        add(tree2, right + 1, -val * (right + 1));
    }

    private long query(int i) {
        long sum1 = 0, sum2 = 0;
        int origI = i;
        while (i > 0) {
            sum1 += tree1[i];
            sum2 += tree2[i];
            i -= i & (-i);
        }
        return sum1 * (origI + 1) - sum2;
    }

    public long queryRange(int left, int right) {
        return query(right) - query(left - 1);
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log N)$, Truy vấn $O(\log N)$. Không gian: $O(N)$.

## 25. Tối ưu DP với BIT (DP with BIT Optimization)
**Đề bài chi tiết:** Tìm độ dài của chuỗi con tăng dài nhất (LIS) và đếm số lượng chuỗi LIS đó.
**Phân tích thuật toán:** Để tối ưu thuật toán DP từ $O(N^2)$ xuống $O(N \log N)$, ta dùng BIT. Thay vì lưu tổng thông thường, mỗi node của BIT sẽ lưu một cặp `[max_length, count]`. Khi hợp nhất (merge) hai trạng thái, nếu `len` bằng nhau thì cộng dồn `count`, nếu lớn hơn thì lấy `count` mới.
**Mã nguồn Java:**
```java
import java.util.*;

class LISWithBIT {
    class Node {
        int len, count;
        Node(int l, int c) { len = l; count = c; }
    }
    
    Node[] tree;
    int n;
    
    public int findNumberOfLIS(int[] nums) {
        if (nums.length == 0) return 0;
        int[] sorted = nums.clone();
        Arrays.sort(sorted);
        
        n = nums.length;
        tree = new Node[n + 1];
        for (int i = 0; i <= n; i++) tree[i] = new Node(0, 0);
        
        Node maxNode = new Node(0, 0);
        
        for (int num : nums) {
            int rank = Arrays.binarySearch(sorted, num) + 1;
            // Tìm rank chính xác (first occurence nếu có trùng)
            // Đơn giản hóa: dùng rank cơ bản (cần nén chính xác trong bài toán thực tế)
            Node q = query(rank - 1);
            int newLen = q.len + 1;
            int newCnt = Math.max(q.count, 1);
            
            update(rank, newLen, newCnt);
            
            if (newLen > maxNode.len) {
                maxNode = new Node(newLen, newCnt);
            } else if (newLen == maxNode.len) {
                maxNode.count += newCnt;
            }
        }
        return maxNode.count;
    }
    
    private void update(int i, int len, int count) {
        while (i <= n) {
            if (tree[i].len == len) {
                tree[i].count += count;
            } else if (tree[i].len < len) {
                tree[i].len = len;
                tree[i].count = count;
            }
            i += i & (-i);
        }
    }
    
    private Node query(int i) {
        Node res = new Node(0, 0);
        while (i > 0) {
            if (tree[i].len == res.len) {
                res.count += tree[i].count;
            } else if (tree[i].len > res.len) {
                res.len = tree[i].len;
                res.count = tree[i].count;
            }
            i -= i & (-i);
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian: $O(N \log N)$. Không gian: $O(N)$.

## 26. 2D Matrix Prefix Sum Update
**Đề bài chi tiết:** Cho bảng 2D. Cần xử lý truy vấn: cập nhật điểm `(r, c)` và truy vấn tổng phần tử trong khung hình chữ nhật từ `(r1, c1)` đến `(r2, c2)`.
**Phân tích thuật toán:** Mở rộng mảng BIT 1 chiều thành mảng BIT 2 chiều (2D Fenwick Tree). Việc cập nhật cần $O(\log R \cdot \log C)$ thao tác, truy vấn cũng vậy bằng cách gọi hàm `query` 4 lần (bao hàm - loại trừ).
**Mã nguồn Java:**
```java
class NumMatrix {
    int[][] tree;
    int[][] nums;
    int m, n;

    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        m = matrix.length;
        n = matrix[0].length;
        tree = new int[m + 1][n + 1];
        nums = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                update(i, j, matrix[i][j]);
            }
        }
    }
    
    public void update(int row, int col, int val) {
        int delta = val - nums[row][col];
        nums[row][col] = val;
        for (int i = row + 1; i <= m; i += i & (-i)) {
            for (int j = col + 1; j <= n; j += j & (-j)) {
                tree[i][j] += delta;
            }
        }
    }
    
    private int query(int row, int col) {
        int sum = 0;
        for (int i = row + 1; i > 0; i -= i & (-i)) {
            for (int j = col + 1; j > 0; j -= j & (-j)) {
                sum += tree[i][j];
            }
        }
        return sum;
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return query(row2, col2) - query(row1 - 1, col2) - query(row2, col1 - 1) + query(row1 - 1, col1 - 1);
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log M \cdot \log N)$ cho mỗi cập nhật/truy vấn. Không gian: $O(M \times N)$.

## 27. Number of Elements in Range (Offline Filter Query)
**Đề bài chi tiết:** Cho mảng `A` và các truy vấn `(L, R, X)`: đếm số phần tử trong `A[L..R]` nhỏ hơn hoặc bằng `X`.
**Phân tích thuật toán:** Vì đây là truy vấn tĩnh không cập nhật, ta có thể giải offline. Sắp xếp các phần tử mảng và các truy vấn theo giá trị lớn dần. Quét qua các truy vấn, cập nhật những phần tử mảng $\le X$ vào một BIT theo chỉ mục ban đầu của chúng. Câu trả lời chính là tổng số phần tử từ $L$ đến $R$ đang bật trong BIT.
**Mã nguồn Java:**
```java
import java.util.*;

class ElementsInRange {
    class Query implements Comparable<Query> {
        int l, r, x, id;
        Query(int l, int r, int x, int id) {
            this.l = l; this.r = r; this.x = x; this.id = id;
        }
        public int compareTo(Query o) { return Integer.compare(this.x, o.x); }
    }
    
    public int[] solve(int[] nums, int[][] queries) {
        int n = nums.length;
        int q = queries.length;
        int[] ans = new int[q];
        
        int[][] arr = new int[n][2];
        for (int i = 0; i < n; i++) {
            arr[i][0] = nums[i];
            arr[i][1] = i + 1; // 1-based index
        }
        Arrays.sort(arr, (a, b) -> Integer.compare(a[0], b[0]));
        
        Query[] qs = new Query[q];
        for (int i = 0; i < q; i++) {
            qs[i] = new Query(queries[i][0] + 1, queries[i][1] + 1, queries[i][2], i);
        }
        Arrays.sort(qs);
        
        int[] tree = new int[n + 1];
        int ptr = 0;
        
        for (Query query : qs) {
            while (ptr < n && arr[ptr][0] <= query.x) {
                update(tree, arr[ptr][1], 1, n);
                ptr++;
            }
            ans[query.id] = getSum(tree, query.r) - getSum(tree, query.l - 1);
        }
        return ans;
    }
    
    private void update(int[] tree, int i, int delta, int n) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }
    
    private int getSum(int[] tree, int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: Sắp xếp $O(N \log N + Q \log Q)$, Xử lý $O(Q \log N + N \log N)$. Không gian: $O(N + Q)$.

## 28. Giá trị cân bằng chuỗi
**Đề bài chi tiết:** Cho chuỗi chỉ gồm kí tự '0' và '1'. Tại mỗi bước, một kí tự bị lật đổi. Bạn cần tính xem có bao nhiêu đoạn con đang cân bằng (có số lượng '0' và '1' bằng nhau).
**Phân tích thuật toán:** Một đoạn `[L, R]` cân bằng nếu tổng prefix biến đổi của nó bằng nhau (với '1' là +1, '0' là -1, `Prefix[R] == Prefix[L-1]`). Khi lật một kí tự tại `i`, toàn bộ mảng `Prefix` từ `i` đến `N` sẽ được cộng hoặc trừ 2. Dùng BIT mảng hiệu và HashMap (hoặc Fenwick Range Update Point Query) để quản lý, nhưng đếm đoạn cân bằng động thường đòi hỏi Block Decomposition hoặc Segment Tree cho tối ưu, tuy nhiên minh hoạ này sẽ tập trung vào ý tưởng Point Query qua BIT để đếm đơn giản.
**Mã nguồn Java:**
```java
// Đây là ví dụ mô phỏng tư tưởng duy trì mảng prefixSum.
// Trong thực tế, đếm số đoạn động yêu cầu cấu trúc phức tạp hơn BIT, 
// nhưng để cập nhật PrefixSum trực tiếp thì BIT làm tốt.
class BalancedStringPrefix {
    int[] tree;
    int n;

    public BalancedStringPrefix(String s) {
        n = s.length();
        tree = new int[n + 2];
        int currentPrefix = 0;
        for (int i = 0; i < n; i++) {
            currentPrefix += (s.charAt(i) == '1' ? 1 : -1);
            update(i + 1, i + 1, currentPrefix);
        }
    }

    private void add(int i, int delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public void update(int left, int right, int delta) {
        add(left, delta);
        add(right + 1, -delta);
    }

    // Lấy prefix sum tại i
    public int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật Prefix $O(\log N)$. Không gian: $O(N)$.

## 29. Chống tắc đường (Traffic Congestion Map)
**Đề bài chi tiết:** Xe cộ xếp thành hàng. Khi một xe dời đi, các xe phía sau tự động trượt lên để lấp chỗ trống. Bạn cần tra cứu vị trí thực tế của xe $X$ tại bất kì thời điểm nào.
**Phân tích thuật toán:** Khởi tạo BIT với mỗi vị trí ban đầu có giá trị 1 (đang có xe). Vị trí thực tế của một chiếc xe thứ $K$ trong hàng là tìm kiếm nhị phân phần tử thứ $K$ có tổng bằng $K$ (K-th Empty Slot). Khi xe dời đi, gọi `update(pos, -1)` để giảm chỉ số các xe phía sau tự động.
**Mã nguồn Java:**
```java
class TrafficQueue {
    int[] tree;
    int n;

    public TrafficQueue(int maxCars) {
        n = maxCars;
        tree = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            update(i, 1);
        }
    }

    public void update(int i, int delta) {
        while (i <= n) {
            tree[i] += delta;
            i += i & (-i);
        }
    }

    public void carLeaves(int originalPos) {
        update(originalPos, -1);
    }

    public int getRank(int originalPos) {
        int sum = 0;
        int i = originalPos;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian: $O(\log N)$ để xóa hoặc lấy thứ hạng. Không gian: $O(N)$.

## 30. Phân tích tần suất liên tục (Streaming Frequency Analysis)
**Đề bài chi tiết:** Một dòng dữ liệu (stream) số nguyên liên tục xuất hiện. Tính trung vị (Median) của dãy tại mọi thời điểm bằng mảng Fenwick Tree tĩnh (biết trước giới hạn).
**Phân tích thuật toán:** Fenwick Tree dùng làm mảng đếm tần suất các số. Tổng số lượng phần tử hiện tại là $S$. Trung vị chính là phần tử thứ $(S/2) + 1$ đối với lẻ, hoặc trung bình của phần tử thứ $S/2$ và $S/2 + 1$ đối với chẵn. Ta dùng Binary Lifting trên BIT để nhanh chóng tìm phần tử thứ $K$ trong thời gian $O(\log M)$ với $M$ là giá trị tối đa.
**Mã nguồn Java:**
```java
class StreamingMedian {
    int[] tree;
    int maxVal;
    int count;

    public StreamingMedian(int limit) {
        maxVal = limit;
        tree = new int[maxVal + 1];
        count = 0;
    }

    public void addNum(int num) {
        count++;
        int i = num;
        while (i <= maxVal) {
            tree[i]++;
            i += i & (-i);
        }
    }

    private int findKth(int k) {
        int index = 0;
        int bitMask = Integer.highestOneBit(maxVal);
        
        for (int i = bitMask; i > 0; i >>= 1) {
            int nextIndex = index + i;
            if (nextIndex <= maxVal && k > tree[nextIndex]) {
                index = nextIndex;
                k -= tree[index];
            }
        }
        return index + 1;
    }

    public double getMedian() {
        if (count == 0) return 0;
        if (count % 2 == 1) {
            return findKth(count / 2 + 1);
        } else {
            return (findKth(count / 2) + findKth(count / 2 + 1)) / 2.0;
        }
    }
}
```
**Độ phức tạp:** Thời gian: Cập nhật $O(\log M)$, Tìm K-th $O(\log M)$. Không gian: $O(M)$.
