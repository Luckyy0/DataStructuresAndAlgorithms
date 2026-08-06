# Tuyển Tập 30 Bài Toán Điển Hình về Fenwick Tree (Binary Indexed Tree)

---

## Thông Tin Tài Liệu & Metadata

- **Mã tài liệu:** `DSA-TREE-07-PROB`
- **Chủ đề:** Fenwick Tree / Binary Indexed Tree (BIT) Practice Problems
- **Phiên bản:** 2.0.0
- **Ngôn ngữ:** Java 21 (LTS)
- **Tiêu chuẩn mã nguồn:** Production-grade, Clean Code, Fully Typed, Optimized Cache Locality
- **Yêu cầu kiến thức tiên quyết:** Mảng (Array), Biểu diễn nhị phân & Bitwise (`lowbit(x) = x & (-x)`), Mảng cộng dồn (Prefix Sum), Nén tọa độ (Coordinate Compression).
- **Mục tiêu học tập:** Thành thạo toàn bộ các biến thể của Fenwick Tree từ 1D đến 2D, Range Update - Range Query bằng kỹ thuật 2 BITs, Binary Lifting trên BIT ($O(\log N)$), xử lý truy vấn offline (Offline Sweep-line), và kết hợp Euler Tour Technique để xử lý cây (Tree Queries).

---

## Mục Lục 30 Bài Toán Điển Hình

1. [Bài toán 1: Range Sum Query - Mutable (Point Update, Range Query)](#bài-toán-1-range-sum-query---mutable-point-update-range-query)
2. [Bài toán 2: Range Update and Point Query (Difference Array on Fenwick Tree)](#bài-toán-2-range-update-and-point-query-difference-array-on-fenwick-tree)
3. [Bài toán 3: Range Update and Range Query (Two Fenwick Trees Technique)](#bài-toán-3-range-update-and-range-query-two-fenwick-trees-technique)
4. [Bài toán 4: Đếm Số Nghịch Thế Trong Mảng (Count Inversions in Array)](#bài-toán-4-đếm-số-nghịch-thế-trong-mảng-count-inversions-in-array)
5. [Bài toán 5: Count of Smaller Numbers After Self (LeetCode 315)](#bài-toán-5-count-of-smaller-numbers-after-self-leetcode-315)
6. [Bài toán 6: Reverse Pairs (LeetCode 493)](#bài-toán-6-reverse-pairs-leetcode-493)
7. [Bài toán 7: Count of Range Sum (LeetCode 327)](#bài-toán-7-count-of-range-sum-leetcode-327)
8. [Bài toán 8: Tìm Phần Tử Nhỏ Thứ K Trong Tập Động (Binary Lifting trên BIT)](#bài-toán-8-tìm-phần-tử-nhỏ-thứ-k-trong-tập-động-binary-lifting-trên-bit)
9. [Bài toán 9: 2D Fenwick Tree - Point Update và Submatrix Range Sum Query](#bài-toán-9-2d-fenwick-tree---point-update-và-submatrix-range-sum-query)
10. [Bài toán 10: 2D Fenwick Tree - Submatrix Range Update và Point Query](#bài-toán-10-2d-fenwick-tree---submatrix-range-update-và-point-query)
11. [Bài toán 11: 2D Range Update và Range Query (Kỹ Thuật 4 Fenwick Trees 2D)](#bài-toán-11-2d-range-update-và-range-query-kỹ-thuật-4-fenwick-trees-2d)
12. [Bài toán 12: Đếm Số Dãy Con Tăng Độ Dài K (Number of Increasing Subsequences of Length K)](#bài-toán-12-đếm-số-dãy-con-tăng-độ-dài-k-number-of-increasing-subsequences-of-length-k)
13. [Bài toán 13: Dãy Con Tăng Dài Nhất (Longest Increasing Subsequence - LIS) bằng BIT Max](#bài-toán-13-dãy-con-tăng-dài-nhất-longest-increasing-subsequence---lis-bằng-bit-max)
14. [Bài toán 14: Dynamic Prefix XOR Queries với Point Updates](#bài-toán-14-dynamic-prefix-xor-queries-với-point-updates)
15. [Bài toán 15: Nén Tọa Độ Với Fenwick Tree Trên Tập Giá Trị Lớn $[-10^9, 10^9]$](#bài-toán-15-nén-tọa-độ-với-fenwick-tree-trên-tập-giá-trị-lớn--109-109)
16. [Bài toán 16: Đếm Bộ Ba Tam Giác / Pivot Triplet Count (Left/Right Greater/Smaller)](#bài-toán-16-đếm-bộ-ba-tam-giác--pivot-triplet-count-leftright-greatersmaller)
17. [Bài toán 17: Dynamic Running Median Sử Dụng Binary Lifting Trên BIT](#bài-toán-17-dynamic-running-median-sử-dụng-binary-lifting-trên-bit)
18. [Bài toán 18: Đếm Số Mảng Con Có Tổng Nằm Trong Đoạn $[L, R]$](#bài-toán-18-đếm-số-mảng-con-có-tổng-nằm-trong-đoạn-l-r)
19. [Bài toán 19: Offline Range Distinct Elements Query (SPOJ DQUERY / Dãy Số Phân Biệt)](#bài-toán-19-offline-range-distinct-elements-query-spoj-dquery--dãy-số-phân-biệt)
20. [Bài toán 20: Đếm Số Hình Chữ Nhật Chứa Điểm (Count Rectangles Containing Each Point)](#bài-toán-20-đếm-số-hình-chữ-nhật-chứa-điểm-count-rectangles-containing-each-point)
21. [Bài toán 21: Sweep-Line & Fenwick Tree Xử Lý Skyline / Max Enclosing Coordinate](#bài-toán-21-sweep-line--fenwick-tree-xử-lý-skyline--max-enclosing-coordinate)
22. [Bài toán 22: Đếm Số Dãy Tam Giác Nghịch Thế (132 Pattern Counting)](#bài-toán-22-đếm-số-dãy-tam-giác-nghịch-thế-132-pattern-counting)
23. [Bài toán 23: Sliding Window Inversion Count (Cửa Sổ Trượt Tính Số Cặp Nghịch Thế)](#bài-toán-23-sliding-window-inversion-count-cửa-sổ-trượt-tính-số-cặp-nghịch-thế)
24. [Bài toán 24: Dynamic Multiset với Hỗ Trợ Xóa/Thêm Phần Tử và Rank Query](#bài-toán-24-dynamic-multiset-với-hỗ-trợ-xóathêm-phần-tử-và-rank-query)
25. [Bài toán 25: Đếm Số Cặp $(i, j)$ Thỏa Mãn $i < j$ và $|A[i] - A[j]| \le K$](#bài-toán-25-đếm-số-cặp-i-j-thỏa-mãn-i--j-và-ai---aj-le-k)
26. [Bài toán 26: Xử Lý Cây Bằng Kỹ Thuật Euler Tour & Fenwick Tree (Subtree Sum & Update)](#bài-toán-26-xử-lý-cây-bằng-kỹ-thuật-euler-tour--fenwick-tree-subtree-sum--update)
27. [Bài toán 27: Cập Nhật Nút Cây và Truy Vấn Tổng Đường Đi Gốc Đến Nút (Path Queries on Tree)](#bài-toán-27-cập-nhật-nút-cây-và-truy-vấn-tổng-đường-đi-gốc-đến-nút-path-queries-on-tree)
28. [Bài toán 28: Đếm Số Đoạn Con Có Tích Lũy Bội Số / Tính Chất Chẵn Lẻ (Dynamic Parity)](#bài-toán-28-đếm-số-đoạn-con-có-tích-lũy-bội-số--tính-chất-chẵn-lẻ-dynamic-parity)
29. [Bài toán 29: Line Intersections Count trong Computational Geometry](#bài-toán-29-line-intersections-count-trong-computational-geometry)
30. [Bài toán 30: Range Gcd / Non-invertible Updates Handling with Fenwick Tree](#bài-toán-30-range-gcd--non-invertible-updates-handling-with-fenwick-tree)

---

### Bài toán 1: Range Sum Query - Mutable (Point Update, Range Query)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` có kích thước $N$. Hãy thiết kế một cấu trúc dữ liệu hỗ trợ 2 loại truy vấn động:
1. `update(index, val)`: Cập nhật giá trị tại phần tử `nums[index] = val`.
2. `sumRange(left, right)`: Trả về tổng các phần tử từ chỉ số `left` đến `right` (tức $\sum_{i=left}^{right} nums[i]$).

*Input:*
- Mảng ban đầu `nums = [1, 3, 5]`
- Thao tác: `sumRange(0, 2)` $\rightarrow$ trả về $9$.
- Thao tác: `update(1, 2)` $\rightarrow$ mảng thành `[1, 2, 5]`.
- Thao tác: `sumRange(0, 2)` $\rightarrow$ trả về $8$.

*Ràng buộc:*
- $1 \le N \le 10^5$
- $-10^4 \le nums[i], val \le 10^4$
- $0 \le left \le right < N$
- Tổng số lượng truy vấn `update` và `sumRange` tối đa là $10^5$.

**Phân tích thuật toán:**
- Sử dụng Fenwick Tree 1-indexed. Cây BIT lưu trữ các tổng tiền tố dạng cây lũy thừa 2.
- Hàm `lowbit(i) = i & (-i)` trích xuất bit 1 có trọng số thấp nhất.
- Khi gọi `update(index, val)`, ta tính độ chênh lệch $\Delta = val - nums[index]$, sau đó cộng dồn $\Delta$ vào cây BIT tại vị trí $index + 1$ và các nút cha tương ứng thông qua `i += lowbit(i)`.
- Khi tính `sumRange(left, right)`, kết quả bằng `query(right + 1) - query(left)` với `query(i)` tính tổng tiền tố từ $1$ đến $i$ qua `i -= lowbit(i)`.
- Thời gian xây dựng ban đầu là $O(N)$ bằng kỹ thuật cộng dồn tuyến tính vào nút cha trực tiếp $i + lowbit(i)$, thay vì gọi $N$ lần `add` tốn $O(N \log N)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class NumArrayMutable {
    private final int n;
    private final long[] tree;
    private final int[] nums;

    public NumArrayMutable(int[] nums) {
        this.n = nums.length;
        this.nums = new int[n];
        System.arraycopy(nums, 0, this.nums, 0, n);
        this.tree = new long[n + 1];

        // Khởi tạo BIT trong O(N)
        for (int i = 1; i <= n; i++) {
            tree[i] += nums[i - 1];
            int parent = i + (i & -i);
            if (parent <= n) {
                tree[parent] += tree[i];
            }
        }
    }

    private void add(int index, long delta) {
        for (int i = index; i <= n; i += i & -i) {
            tree[i] += delta;
        }
    }

    public void update(int index, int val) {
        long delta = (long) val - nums[index];
        nums[index] = val;
        add(index + 1, delta);
    }

    private long prefixSum(int index) {
        long sum = 0;
        for (int i = index; i > 0; i -= i & -i) {
            sum += tree[i];
        }
        return sum;
    }

    public long sumRange(int left, int right) {
        if (left < 0 || right >= n || left > right) {
            throw new IllegalArgumentException("Invalid range indices");
        }
        return prefixSum(right + 1) - prefixSum(left);
    }
}
```

**Độ phức tạp:**
- Xây dựng (Build): $O(N)$ thời gian, $O(N)$ không gian bộ nhớ.
- Cập nhật (`update`): $O(\log N)$ thời gian.
- Truy vấn (`sumRange`): $O(\log N)$ thời gian.
- Bộ nhớ phụ trợ: $O(N)$ cho mảng `tree` dạng nguyên thủy `long[]`.

---

### Bài toán 2: Range Update and Point Query (Difference Array on Fenwick Tree)

**Đề bài chi tiết:**
Cho một mảng $A$ gồm $N$ phần tử số nguyên ban đầu đều bằng $0$. Hãy thiết kế cấu trúc dữ liệu thực hiện $Q$ thao tác thuộc 2 dạng:
1. `addRange(left, right, val)`: Cộng thêm giá trị `val` vào tất cả các phần tử $A[i]$ với $left \le i \le right$.
2. `get(index)`: Truy vấn giá trị hiện tại của phần tử tại chỉ số `index`.

*Input:*
- $N = 5$
- `addRange(1, 3, 5)` $\rightarrow A = [0, 5, 5, 5, 0]$
- `addRange(2, 4, 3)` $\rightarrow A = [0, 5, 8, 8, 3]$
- `get(2)` $\rightarrow$ trả về $8$.
- `get(0)` $\rightarrow$ trả về $0$.

*Ràng buộc:*
- $1 \le N, Q \le 2 \cdot 10^5$
- $0 \le left \le right < N$
- $-10^9 \le val \le 10^9$

**Phân tích thuật toán:**
- Thay vì cập nhật trực tiếp trên mảng $A$, ta sử dụng mảng hiệu sai (Difference Array) $D$:
  $$D[1] = A[1], \quad D[i] = A[i] - A[i-1] \quad (\forall i \ge 2)$$
- Khi đó, giá trị phần tử tại chỉ số $k$ chính là tổng tiền tố của mảng hiệu sai:
  $$A[k] = \sum_{i=1}^k D[i]$$
- Thao tác cập nhật đoạn $[L, R]$ thêm $val$ tương đương với:
  - $D[L] \leftarrow D[L] + val$
  - $D[R + 1] \leftarrow D[R + 1] - val$
- Như vậy, bài toán chuyển thành:
  - Cập nhật đoạn $\rightarrow$ 2 lần cập nhật điểm (Point Update) trên cây BIT quản lý mảng $D$.
  - Truy vấn điểm $\rightarrow$ 1 lần truy vấn tổng tiền tố (Prefix Sum Query) trên cây BIT.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class RangeUpdatePointQueryBIT {
    private final int n;
    private final long[] bit;

    public RangeUpdatePointQueryBIT(int n) {
        this.n = n;
        this.bit = new long[n + 2]; // 1-indexed, hỗ trợ R+1 <= n+1
    }

    private void add(int i, long delta) {
        for (; i <= n; i += i & -i) {
            bit[i] += delta;
        }
    }

    public void addRange(int left, int right, long val) {
        if (left > right || left < 0 || right >= n) return;
        // Chuyển sang 1-indexed: left+1 đến right+1
        add(left + 1, val);
        add(right + 2, -val);
    }

    public long get(int index) {
        if (index < 0 || index >= n) {
            throw new IndexOutOfBoundsException("Index out of bounds");
        }
        long res = 0;
        for (int i = index + 1; i > 0; i -= i & -i) {
            res += bit[i];
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Khởi tạo: $O(N)$ thời gian, $O(N)$ bộ nhớ.
- `addRange`: $O(\log N)$ thời gian (chỉ gồm 2 phép cập nhật điểm trên BIT).
- `get`: $O(\log N)$ thời gian (1 phép tính tổng tiền tố).
- Bộ nhớ phụ trợ: $O(N)$ với mảng tĩnh $long[]$.

---

### Bài toán 3: Range Update and Range Query (Two Fenwick Trees Technique)

**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu cho phép vừa cập nhật giá trị trên một đoạn $[L, R]$ vừa truy vấn tổng giá trị trên một đoạn $[L, R]$ bất kỳ trong thời gian $O(\log N)$.
1. `addRange(left, right, val)`: Cộng $val$ vào toàn bộ phần tử thuộc đoạn $[left, right]$.
2. `queryRange(left, right)`: Tính tổng các phần tử trong đoạn $[left, right]$.

*Input:*
- $N = 6$
- `addRange(1, 4, 2)`
- `addRange(3, 5, 3)`
- `queryRange(2, 4)` $\rightarrow$ trả về giá trị tổng chính xác.

*Ràng buộc:*
- $1 \le N, Q \le 2 \cdot 10^5$
- $0 \le left \le right < N$
- $-10^6 \le val \le 10^6$

**Phân tích thuật toán:**
- Xét mảng hiệu sai $D[i] = A[i] - A[i-1]$ với $A[0] = 0$.
- Tổng tiền tố $S_p = \sum_{i=1}^p A[i]$ được biểu diễn qua $D$ như sau:
  $$S_p = \sum_{i=1}^p \sum_{j=1}^i D[j] = \sum_{j=1}^p (p - j + 1) \cdot D[j] = (p + 1) \sum_{j=1}^p D[j] - \sum_{j=1}^p (j \cdot D[j])$$
- Để duy trì công thức trên, ta sử dụng **2 cây Fenwick Tree**:
  1. $BIT_1$ quản lý dãy $D[j]$.
  2. $BIT_2$ quản lý dãy $j \cdot D[j]$.
- Khi cập nhật đoạn $[L, R]$ thêm $val$:
  - Cập nhật tại $L$: Thêm $val$ vào $BIT_1$ tại vị trí $L$; thêm $L \cdot val$ vào $BIT_2$ tại vị trí $L$.
  - Cập nhật tại $R + 1$: Thêm $-val$ vào $BIT_1$ tại vị trí $R + 1$; thêm $-(R + 1) \cdot val$ vào $BIT_2$ tại vị trí $R + 1$.
- Khi truy vấn tổng tiền tố $S_p$:
  $$S_p = (p + 1) \cdot query(BIT_1, p) - query(BIT_2, p)$$
- Tổng đoạn $[L, R]$ là $S_R - S_{L-1}$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class RangeUpdateRangeQueryBIT {
    private final int n;
    private final long[] bit1; // Duy trì D[i]
    private final long[] bit2; // Duy trì i * D[i]

    public RangeUpdateRangeQueryBIT(int n) {
        this.n = n;
        this.bit1 = new long[n + 2];
        this.bit2 = new long[n + 2];
    }

    private void add(int i, long val) {
        long valTimesI = val * i;
        for (int idx = i; idx <= n; idx += idx & -idx) {
            bit1[idx] += val;
            bit2[idx] += valTimesI;
        }
    }

    public void addRange(int left, int right, long val) {
        if (left > right || left < 0 || right >= n) return;
        int l = left + 1;
        int r = right + 1;
        add(l, val);
        add(r + 1, -val);
    }

    private long prefixSum(int p) {
        if (p <= 0) return 0;
        long sum1 = 0;
        long sum2 = 0;
        for (int i = p; i > 0; i -= i & -i) {
            sum1 += bit1[i];
            sum2 += bit2[i];
        }
        return (p + 1L) * sum1 - sum2;
    }

    public long queryRange(int left, int right) {
        if (left > right || left < 0 || right >= n) {
            throw new IllegalArgumentException("Invalid range");
        }
        return prefixSum(right + 1) - prefixSum(left);
    }
}
```

**Độ phức tạp:**
- Cập nhật đoạn (`addRange`): $O(\log N)$.
- Truy vấn đoạn (`queryRange`): $O(\log N)$.
- Không gian bộ nhớ: $O(N)$ (gồm 2 mảng `long[]`, overhead cực nhỏ so với Segment Tree có Lazy Propagation).

---

### Bài toán 4: Đếm Số Nghịch Thế Trong Mảng (Count Inversions in Array)

**Đề bài chi tiết:**
Cho mảng số nguyên `arr` gồm $N$ phần tử. Một cặp chỉ số $(i, j)$ được gọi là một cặp nghịch thế (inversion) nếu $0 \le i < j < N$ và $arr[i] > arr[j]$.
Hãy đếm tổng số cặp nghịch thế trong mảng đã cho.

*Input:*
- `arr = [8, 4, 2, 1]`
*Output:*
- `6` (Các cặp: $(8,4), (8,2), (8,1), (4,2), (4,1), (2,1)$)

*Ràng buộc:*
- $1 \le N \le 2 \cdot 10^5$
- $-10^9 \le arr[i] \le 10^9$

**Phân tích thuật toán:**
- Do giá trị $arr[i]$ có thể âm hoặc lên tới $10^9$, ta cần **nén tọa độ (Coordinate Compression)** các giá trị về đoạn $[1, K]$ với $K \le N$.
- Duyệt mảng từ phải qua trái (hoặc từ trái qua phải):
  - Khi duyệt từ phải qua trái tại phần tử $arr[i]$ đã nén thành $rank$:
    - Số phần tử đứng sau $arr[i]$ và nhỏ hơn nó chính là tổng số lần xuất hiện của các giá trị trong đoạn $[1, rank - 1]$.
    - Truy vấn BIT: `inversions += query(rank - 1)`.
    - Thêm phần tử hiện tại vào BIT: `add(rank, 1)`.
- Kỹ thuật này đạt hiệu năng vượt trội, code ngắn gọn hơn nhiều so với Merge Sort.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class InversionCount {
    private static class FenwickTree {
        private final int size;
        private final int[] tree;

        public FenwickTree(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }

        public void add(int i, int delta) {
            for (; i <= size; i += i & -i) {
                tree[i] += delta;
            }
        }

        public int query(int i) {
            int sum = 0;
            for (; i > 0; i -= i & -i) {
                sum += tree[i];
            }
            return sum;
        }
    }

    public static long countInversions(int[] arr) {
        int n = arr.length;
        if (n <= 1) return 0;

        // Nén tọa độ
        int[] sorted = arr.clone();
        Arrays.sort(sorted);
        int uniqueCount = 0;
        for (int i = 0; i < n; i++) {
            if (i == 0 || sorted[i] != sorted[i - 1]) {
                sorted[uniqueCount++] = sorted[i];
            }
        }

        FenwickTree bit = new FenwickTree(uniqueCount);
        long inversions = 0;

        // Duyệt từ phải sang trái
        for (int i = n - 1; i >= 0; i--) {
            // Tìm rank (1-indexed)
            int rank = Arrays.binarySearch(sorted, 0, uniqueCount, arr[i]) + 1;
            inversions += bit.query(rank - 1);
            bit.add(rank, 1);
        }

        return inversions;
    }
}
```

**Độ phức tạp:**
- Nén tọa độ: $O(N \log N)$ thời gian.
- Đếm qua BIT: $N$ lần truy vấn và cập nhật, mỗi lần mất $O(\log N)$.
- Tổng thời gian: $O(N \log N)$.
- Tổng không gian: $O(N)$.

---

### Bài toán 5: Count of Smaller Numbers After Self (LeetCode 315)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`. Với mỗi phần tử `nums[i]`, hãy đếm số lượng phần tử `nums[j]` thỏa mãn $j > i$ và `nums[j] < nums[i]`. Trả về kết quả dưới dạng danh sách `List<Integer>`.

*Input:* `nums = [5, 2, 6, 1]`
*Output:* `[2, 1, 1, 0]`

*Ràng buộc:*
- $1 \le nums.length \le 10^5$
- $-10^4 \le nums[i] \le 10^4$

**Phân tích thuật toán:**
- Dịch chuyển toàn bộ giá trị về số dương: do $-10^4 \le nums[i] \le 10^4$, ta có thể cộng offset $+10001$ để ánh xạ giá trị vào đoạn $[1, 20002]$ mà không cần nén tọa độ qua mảng phụ (hoặc sử dụng coordinate compression để tổng quát hóa).
- Duyệt mảng từ phải qua trái ($i = n - 1 \to 0$):
  - Số lượng phần tử nhỏ hơn `nums[i]` sau nó chính là `query(mappedVal - 1)`.
  - Cập nhật tần số: `add(mappedVal, 1)`.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CountSmallerAfterSelf {
    private static final int OFFSET = 10001;
    private static final int MAX_VAL = 20005;

    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        if (n == 0) return Collections.emptyList();

        int[] bit = new int[MAX_VAL + 1];
        int[] result = new int[n];

        for (int i = n - 1; i >= 0; i--) {
            int val = nums[i] + OFFSET;
            // Query các phần tử < val
            int sum = 0;
            for (int idx = val - 1; idx > 0; idx -= idx & -idx) {
                sum += bit[idx];
            }
            result[i] = sum;

            // Add val vào BIT
            for (int idx = val; idx <= MAX_VAL; idx += idx & -idx) {
                bit[idx]++;
            }
        }

        List<Integer> ans = new ArrayList<>(n);
        for (int count : result) {
            ans.add(count);
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log M)$ với $M$ là dải giá trị ($M \approx 2 \cdot 10^4$).
- Bộ nhớ phụ trợ: $O(M + N)$ tối ưu không gian bộ nhớ.

---

### Bài toán 6: Reverse Pairs (LeetCode 493)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Một cặp nghịch thế quan trọng (Reverse Pair) là một cặp $(i, j)$ sao cho $0 \le i < j < nums.length$ và $nums[i] > 2 \cdot nums[j]$.
Hãy đếm tổng số reverse pairs.

*Input:* `nums = [1, 3, 2, 3, 1]`
*Output:* `2` (Các cặp $(3, 1)$ tại chỉ số $(1, 4)$ và $(3, 4)$)

*Input:* `nums = [2, 4, 3, 5, 1]`
*Output:* `3`

*Ràng buộc:*
- $1 \le nums.length \le 5 \cdot 10^4$
- $-2^{31} \le nums[i] \le 2^{31} - 1$

**Phân tích thuật toán:**
- Lưu ý nhân $2 \cdot nums[j]$ có thể gây tràn số `32-bit int`, cần ép kiểu `long`.
- Nén tất cả các giá trị tiềm năng: bao gồm cả `nums[i]` và `2L * nums[i]`.
- Duyệt từ phải sang trái:
  - Với mỗi $nums[i]$, ta cần đếm các $nums[j]$ đã duyệt (đứng sau) thỏa mãn $nums[j] < \frac{nums[i]}{2.0}$, tức $2 \cdot nums[j] < nums[i]$.
  - Để đơn giản: Khi đứng tại $i$, ta tìm vị trí của giá trị $nums[i] - 1$ (hoặc rank của số lớn nhất $\le \lfloor (nums[i]-1)/2 \rfloor$).
  - Chuẩn hóa: Thu thập tất cả giá trị $nums[k]$ và $2L \cdot nums[k]$, sort và loại bỏ trùng lặp.
  - Khi duyệt tại $i$:
    - Tìm rank của giá trị lớn nhất $< nums[i]$ trong tập các $2 \cdot x$.
    - Truy vấn BIT để đếm số lượng.
    - Thêm $2L \cdot nums[i]$ vào BIT.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class ReversePairs {
    private static class FenwickTree {
        private final int[] tree;
        private final int size;

        public FenwickTree(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }

        public void add(int i, int delta) {
            for (; i <= size; i += i & -i) {
                tree[i] += delta;
            }
        }

        public int query(int i) {
            int sum = 0;
            for (; i > 0; i -= i & -i) {
                sum += tree[i];
            }
            return sum;
        }
    }

    public int reversePairs(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;

        // Tập hợp tất cả các giá trị nums[i] và 2 * nums[i]
        long[] allValues = new long[2 * n];
        for (int i = 0; i < n; i++) {
            allValues[2 * i] = nums[i];
            allValues[2 * i + 1] = 2L * nums[i];
        }

        Arrays.sort(allValues);
        int uniqueCount = 0;
        for (int i = 0; i < allValues.length; i++) {
            if (i == 0 || allValues[i] != allValues[i - 1]) {
                allValues[uniqueCount++] = allValues[i];
            }
        }

        FenwickTree bit = new FenwickTree(uniqueCount);
        int count = 0;

        // Duyệt từ phải qua trái
        for (int i = n - 1; i >= 0; i--) {
            // Tìm số lượng phần tử 2 * nums[j] < nums[i]
            // Tức là tìm rank của giá trị nhỏ hơn nums[i]
            int rankNum = Arrays.binarySearch(allValues, 0, uniqueCount, (long) nums[i]);
            // rankNum là 0-indexed, các phần tử nghiêm ngặt nhỏ hơn nums[i] nằm trong [1, rankNum]
            count += bit.query(rankNum);

            // Thêm 2 * nums[i] vào BIT
            int rank2Num = Arrays.binarySearch(allValues, 0, uniqueCount, 2L * nums[i]) + 1;
            bit.add(rank2Num, 1);
        }

        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$ cho cả nén tọa độ và duyệt BIT.
- Bộ nhớ: $O(N)$.

---

### Bài toán 7: Count of Range Sum (LeetCode 327)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và hai số nguyên `lower`, `upper`. Hãy đếm số lượng mảng con liên tiếp $(i, j)$ ($i \le j$) sao cho tổng các phần tử $S(i, j) = \sum_{k=i}^j nums[k]$ thỏa mãn $lower \le S(i, j) \le upper$.

*Input:* `nums = [-2, 5, -1], lower = -2, upper = 2`
*Output:* `3` (Các dải: $[0,0] \to -2$, $[2,2] \to -1$, $[0,2] \to 2$)

*Ràng buộc:*
- $1 \le nums.length \le 10^5$
- $-2^{31} \le nums[i] \le 2^{31} - 1$
- $-10^5 \le lower \le upper \le 10^5$

**Phân tích thuật toán:**
- Đặt $P[k]$ là tổng tiền tố: $P[k] = \sum_{m=0}^{k-1} nums[m]$ với $P[0] = 0$.
- Tổng mảng con từ $i$ đến $j$ là $P[j+1] - P[i]$.
- Điều kiện trở thành:
  $$lower \le P[j+1] - P[i] \le upper \iff P[j+1] - upper \le P[i] \le P[j+1] - lower$$
- Với mỗi $j$ cố định, ta cần đếm số lượng $P[i]$ ($i \le j$) nằm trong đoạn $[P[j+1] - upper, P[j+1] - lower]$.
- Ta thu thập toàn bộ các giá trị $P[k]$, $P[k] - upper$, $P[k] - lower$ để nén tọa độ, sau đó duyệt từng bước và truy vấn trên Fenwick Tree.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class CountOfRangeSum {
    private static class FenwickTree {
        private final int[] tree;
        private final int size;

        public FenwickTree(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }

        public void add(int i, int delta) {
            for (; i <= size; i += i & -i) tree[i] += delta;
        }

        public int query(int i) {
            int sum = 0;
            for (; i > 0; i -= i & -i) sum += tree[i];
            return sum;
        }

        public int queryRange(int l, int r) {
            if (l > r) return 0;
            return query(r) - query(l - 1);
        }
    }

    public int countRangeSum(int[] nums, int lower, int upper) {
        int n = nums.length;
        long[] prefixSums = new long[n + 1];
        for (int i = 0; i < n; i++) {
            prefixSums[i + 1] = prefixSums[i] + nums[i];
        }

        // Tập hợp giá trị nén
        long[] candidates = new long[(n + 1) * 3];
        int ptr = 0;
        for (long s : prefixSums) {
            candidates[ptr++] = s;
            candidates[ptr++] = s - upper;
            candidates[ptr++] = s - lower;
        }

        Arrays.sort(candidates);
        int uniqueCount = 0;
        for (int i = 0; i < candidates.length; i++) {
            if (i == 0 || candidates[i] != candidates[i - 1]) {
                candidates[uniqueCount++] = candidates[i];
            }
        }

        FenwickTree bit = new FenwickTree(uniqueCount);
        int total = 0;

        for (long s : prefixSums) {
            int leftRank = Arrays.binarySearch(candidates, 0, uniqueCount, s - upper) + 1;
            int rightRank = Arrays.binarySearch(candidates, 0, uniqueCount, s - lower) + 1;
            total += bit.queryRange(leftRank, rightRank);

            int currentRank = Arrays.binarySearch(candidates, 0, uniqueCount, s) + 1;
            bit.add(currentRank, 1);
        }

        return total;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 8: Tìm Phần Tử Nhỏ Thứ K Trong Tập Động (Binary Lifting trên BIT)

**Đề bài chi tiết:**
Cho một tập hợp đa phần tử (Multiset) động hỗ trợ các thao tác:
1. `insert(x)`: Thêm phần tử $x$ vào tập.
2. `delete(x)`: Xóa một phần tử $x$ khỏi tập (nếu tồn tại).
3. `findKth(k)`: Tìm phần tử nhỏ thứ $k$ (1-indexed) hiện có trong tập hợp với thời gian $O(\log N)$.

*Ràng buộc:*
- Các giá trị $x \in [1, MAX\_VAL]$ với $MAX\_VAL \le 10^6$.
- $1 \le k \le$ tổng số phần tử hiện có.

**Phân tích thuật toán:**
- Thay vì sử dụng Binary Search ngoài vòng lặp kết hợp `query(mid)` tốn $O(\log^2 N)$, ta áp dụng kỹ thuật **Binary Lifting (Nhảy nhị phân trực tiếp trên cây BIT)** để đạt $O(\log N)$.
- Cấu trúc BIT lưu trữ tại mỗi vị trí $idx$ tổng của một đoạn độ dài $lowbit(idx)$ lũy thừa 2.
- Ta duyệt các lũy thừa của 2 từ $\lfloor \log_2(MAX\_VAL) \rfloor$ giảm dần về 0.
- Tại mỗi bit $p = 2^j$, nếu $idx + p \le MAX\_VAL$ và $tree[idx + p] < k$, ta nhảy sang $idx \leftarrow idx + p$ và giảm $k \leftarrow k - tree[idx + p]$.
- Vị trí dừng lại $idx + 1$ chính là giá trị cần tìm!

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class OrderStatisticsBIT {
    private final int maxVal;
    private final int[] tree;
    private final int maxPower2;

    public OrderStatisticsBIT(int maxVal) {
        this.maxVal = maxVal;
        this.tree = new int[maxVal + 1];
        // Tìm lũy thừa lớn nhất của 2 <= maxVal
        this.maxPower2 = Integer.highestOneBit(maxVal);
    }

    public void insert(int x) {
        if (x <= 0 || x > maxVal) return;
        for (int i = x; i <= maxVal; i += i & -i) {
            tree[i]++;
        }
    }

    public void delete(int x) {
        if (x <= 0 || x > maxVal) return;
        // Kiểm tra x có tồn tại hay không trước khi xóa
        for (int i = x; i <= maxVal; i += i & -i) {
            tree[i]--;
        }
    }

    /**
     * Binary Lifting trên BIT để tìm phần tử nhỏ thứ k trong O(log N)
     */
    public int findKth(int k) {
        int idx = 0;
        for (int step = maxPower2; step > 0; step >>= 1) {
            int next = idx + step;
            if (next <= maxVal && tree[next] < k) {
                idx = next;
                k -= tree[idx];
            }
        }
        return idx + 1;
    }
}
```

**Độ phức tạp:**
- `insert` / `delete`: $O(\log MAX\_VAL)$.
- `findKth`: $O(\log MAX\_VAL)$ chính xác với đúng $\approx \log_2(MAX\_VAL)$ bước nhảy nhị phân, không có overhead binary search.
- Bộ nhớ: $O(MAX\_VAL)$ mảng `int[]`.

---

### Bài toán 9: 2D Fenwick Tree - Point Update và Submatrix Range Sum Query

**Đề bài chi tiết:**
Cho ma trận số nguyên $M$ kích thước $R \times C$. Thiết kế lớp hỗ trợ:
1. `update(row, col, val)`: Đặt giá trị $M[row][col] = val$.
2. `sumRegion(row1, col1, row2, col2)`: Tính tổng các phần tử trong hình chữ nhật con từ $(row1, col1)$ tới $(row2, col2)$.

*Ràng buộc:*
- $1 \le R, C \le 1000$
- $-10^5 \le val \le 10^5$
- Số lượng truy vấn $Q \le 10^5$.

**Phân tích thuật toán:**
- Mở rộng Fenwick Tree lên 2 chiều: mỗi nút $tree[i][j]$ quản lý hình chữ nhật kích thước $lowbit(i) \times lowbit(j)$.
- Hàm `add(r, c, delta)` dùng 2 vòng lặp lồng nhau qua `r += lowbit(r)` và `c += lowbit(c)`.
- Hàm `prefixSum(r, c)` dùng 2 vòng lặp lồng nhau qua `r -= lowbit(r)` và `c -= lowbit(c)`.
- Áp dụng nguyên lý bao hàm - loại trừ (Inclusion-Exclusion Principle):
  $$Sum(r1, c1, r2, c2) = S(r2, c2) - S(r1-1, c2) - S(r2, c1-1) + S(r1-1, c1-1)$$

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class FenwickTree2D {
    private final int rows;
    private final int cols;
    private final long[][] tree;
    private final int[][] matrix;

    public FenwickTree2D(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            this.rows = 0;
            this.cols = 0;
            this.tree = new long[0][0];
            this.matrix = new int[0][0];
            return;
        }
        this.rows = matrix.length;
        this.cols = matrix[0].length;
        this.matrix = new int[rows][cols];
        this.tree = new long[rows + 1][cols + 1];

        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                update(r, c, matrix[r][c]);
            }
        }
    }

    private void add(int r, int c, long delta) {
        for (int i = r; i <= rows; i += i & -i) {
            for (int j = c; j <= cols; j += j & -j) {
                tree[i][j] += delta;
            }
        }
    }

    public void update(int row, int col, int val) {
        long delta = (long) val - matrix[row][col];
        matrix[row][col] = val;
        add(row + 1, col + 1, delta);
    }

    private long prefixSum(int r, int c) {
        long sum = 0;
        for (int i = r; i > 0; i -= i & -i) {
            for (int j = c; j > 0; j -= j & -j) {
                sum += tree[i][j];
            }
        }
        return sum;
    }

    public long sumRegion(int row1, int col1, int row2, int col2) {
        int r1 = row1 + 1, c1 = col1 + 1;
        int r2 = row2 + 1, c2 = col2 + 1;
        return prefixSum(r2, c2)
             - prefixSum(r1 - 1, c2)
             - prefixSum(r2, c1 - 1)
             + prefixSum(r1 - 1, c1 - 1);
    }
}
```

**Độ phức tạp:**
- Cập nhật (`update`): $O(\log R \cdot \log C)$.
- Truy vấn (`sumRegion`): $O(\log R \cdot \log C)$.
- Bộ nhớ: $O(R \cdot C)$.

---

### Bài toán 10: 2D Fenwick Tree - Submatrix Range Update và Point Query

**Đề bài chi tiết:**
Cho lưới số nguyên kích thước $R \times C$ ban đầu toàn số 0. Hỗ trợ 2 thao tác:
1. `addRegion(r1, c1, r2, c2, val)`: Cộng $val$ vào toàn bộ các ô trong hình chữ nhật con từ $(r1, c1)$ tới $(r2, c2)$.
2. `get(r, c)`: Lấy giá trị của ô $(r, c)$.

**Phân tích thuật toán:**
- Sử dụng mảng hiệu sai 2 chiều (2D Difference Array) $D$:
  $$A[r][c] = \sum_{i=1}^r \sum_{j=1}^c D[i][j]$$
- Cập nhật hình chữ nhật $[r1, r2] \times [c1, c2]$ thêm $val$ tương ứng với 4 cập nhật điểm trên $D$:
  - $D[r1][c1] \leftarrow D[r1][c1] + val$
  - $D[r1][c2 + 1] \leftarrow D[r1][c2 + 1] - val$
  - $D[r2 + 1][c1] \leftarrow D[r2 + 1][c1] - val$
  - $D[r2 + 1][c2 + 1] \leftarrow D[r2 + 1][c2 + 1] + val$
- Truy vấn điểm chính là truy vấn tổng tiền tố 2D trên $D$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class FenwickTree2DRangeUpdatePointQuery {
    private final int rows;
    private final int cols;
    private final long[][] tree;

    public FenwickTree2DRangeUpdatePointQuery(int rows, int cols) {
        this.rows = rows;
        this.cols = cols;
        this.tree = new long[rows + 2][cols + 2];
    }

    private void add(int r, int c, long delta) {
        for (int i = r; i <= rows; i += i & -i) {
            for (int j = c; j <= cols; j += j & -j) {
                tree[i][j] += delta;
            }
        }
    }

    public void addRegion(int r1, int c1, int r2, int c2, long val) {
        int row1 = r1 + 1, col1 = c1 + 1;
        int row2 = r2 + 1, col2 = c2 + 1;
        add(row1, col1, val);
        add(row1, col2 + 1, -val);
        add(row2 + 1, col1, -val);
        add(row2 + 1, col2 + 1, val);
    }

    public long get(int r, int c) {
        long sum = 0;
        for (int i = r + 1; i > 0; i -= i & -i) {
            for (int j = c + 1; j > 0; j -= j & -j) {
                sum += tree[i][j];
            }
        }
        return sum;
    }
}
```

**Độ phức tạp:**
- `addRegion`: $O(\log R \cdot \log C)$.
- `get`: $O(\log R \cdot \log C)$.
- Bộ nhớ: $O(R \cdot C)$.

---

### Bài toán 11: 2D Range Update và Range Query (Kỹ Thuật 4 Fenwick Trees 2D)

**Đề bài chi tiết:**
Cung cấp cấu trúc dữ liệu cho phép cộng $val$ vào hình chữ nhật $[r1, c1]$ tới $[r2, c2]$ và tính tổng các phần tử trong bất kỳ hình chữ nhật con nào trong thời gian $O(\log R \cdot \log C)$.

**Phân tích thuật toán:**
- Tương tự như 1D, tổng tiền tố 2D $\sum_{x=1}^r \sum_{y=1}^c A[x][y]$ khai triển theo mảng hiệu sai $D[i][j]$:
  $$\sum_{x=1}^r \sum_{y=1}^c A[x][y] = \sum_{i=1}^r \sum_{j=1}^c (r - i + 1)(c - j + 1) D[i][j]$$
  $$= (r + 1)(c + 1)\sum D[i][j] - (c + 1)\sum i D[i][j] - (r + 1)\sum j D[i][j] + \sum i j D[i][j]$$
- Do đó ta duy trì **4 cây Fenwick Tree 2D**:
  1. $T_1$: $D[i][j]$
  2. $T_2$: $i \cdot D[i][j]$
  3. $T_3$: $j \cdot D[i][j]$
  4. $T_4$: $i \cdot j \cdot D[i][j]$

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class FenwickTree2DRangeUpdateRangeQuery {
    private final int r, c;
    private final long[][] t1, t2, t3, t4;

    public FenwickTree2DRangeUpdateRangeQuery(int r, int c) {
        this.r = r;
        this.c = c;
        this.t1 = new long[r + 2][c + 2];
        this.t2 = new long[r + 2][c + 2];
        this.t3 = new long[r + 2][c + 2];
        this.t4 = new long[r + 2][c + 2];
    }

    private void add(int x, int y, long val) {
        for (int i = x; i <= r; i += i & -i) {
            for (int j = y; j <= c; j += j & -j) {
                t1[i][j] += val;
                t2[i][j] += val * x;
                t3[i][j] += val * y;
                t4[i][j] += val * x * y;
            }
        }
    }

    public void addRange(int r1, int c1, int r2, int c2, long val) {
        int x1 = r1 + 1, y1 = c1 + 1, x2 = r2 + 1, y2 = c2 + 1;
        add(x1, y1, val);
        add(x1, y2 + 1, -val);
        add(x2 + 1, y1, -val);
        add(x2 + 1, y2 + 1, val);
    }

    private long prefixSum(int x, int y) {
        long s1 = 0, s2 = 0, s3 = 0, s4 = 0;
        for (int i = x; i > 0; i -= i & -i) {
            for (int j = y; j > 0; j -= j & -j) {
                s1 += t1[i][j];
                s2 += t2[i][j];
                s3 += t3[i][j];
                s4 += t4[i][j];
            }
        }
        return (x + 1L) * (y + 1L) * s1 - (y + 1L) * s2 - (x + 1L) * s3 + s4;
    }

    public long queryRange(int r1, int c1, int r2, int c2) {
        int x1 = r1 + 1, y1 = c1 + 1, x2 = r2 + 1, y2 = c2 + 1;
        return prefixSum(x2, y2)
             - prefixSum(x1 - 1, y2)
             - prefixSum(x2, y1 - 1)
             + prefixSum(x1 - 1, y1 - 1);
    }
}
```

**Độ phức tạp:**
- Cập nhật và truy vấn: Đều là $O(\log R \cdot \log C)$.
- Không gian: $O(R \cdot C)$.

---

### Bài toán 12: Đếm Số Dãy Con Tăng Độ Dài K (Number of Increasing Subsequences of Length K)

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ gồm $N$ phần tử phân biệt và một số nguyên $K$. Hãy đếm số lượng dãy con tăng nghiêm ngặt có đúng độ dài $K$ modulo $10^9 + 7$.

*Ràng buộc:*
- $1 \le N \le 10^4$
- $1 \le K \le 50$
- $1 \le A[i] \le 10^9$

**Phân tích thuật toán:**
- Gọi $dp[len][i]$ là số dãy con tăng độ dài $len$ kết thúc tại phần tử có giá trị $A[i]$.
- Công thức quy hoạch động:
  $$dp[len][i] = \sum_{j < i, A[j] < A[i]} dp[len - 1][j]$$
- Để tính tổng này nhanh chóng, với mỗi độ dài $len \in [1, K]$, ta duy trì 1 cây Fenwick Tree $BIT_{len}$.
- Cây $BIT_{len}$ lưu trữ tổng giá trị $dp[len][\dots]$ theo tọa độ giá trị $A[i]$.
- Khi xử lý phần tử $A[i]$ (đã nén tọa độ thành $rank$):
  - Với $len = 1$: $dp[1][i] = 1$.
  - Với $len > 1$: $dp[len][i] = query(BIT_{len-1}, rank - 1)$.
  - Cập nhật $BIT_{len}$ tại vị trí $rank$: $add(BIT_{len}, rank, dp[len][i])$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class IncreasingSubsequencesLengthK {
    private static final int MOD = 1_000_000_007;

    private static void add(int[] bit, int i, int val, int maxVal) {
        for (; i <= maxVal; i += i & -i) {
            bit[i] = (bit[i] + val) % MOD;
        }
    }

    private static int query(int[] bit, int i) {
        int sum = 0;
        for (; i > 0; i -= i & -i) {
            sum = (sum + bit[i]) % MOD;
        }
        return sum;
    }

    public static int countIncreasingSubsequences(int[] a, int k) {
        int n = a.length;
        if (n < k) return 0;

        int[] sorted = a.clone();
        Arrays.sort(sorted);
        int m = 0;
        for (int i = 0; i < n; i++) {
            if (i == 0 || sorted[i] != sorted[i - 1]) {
                sorted[m++] = sorted[i];
            }
        }

        // bit[len][rank]
        int[][] bit = new int[k + 1][m + 1];

        for (int num : a) {
            int rank = Arrays.binarySearch(sorted, 0, m, num) + 1;
            // Độ dài 1
            add(bit[1], rank, 1, m);

            // Các độ dài từ 2 đến k
            for (int len = 2; len <= k; len++) {
                int count = query(bit[len - 1], rank - 1);
                if (count > 0) {
                    add(bit[len], rank, count, m);
                }
            }
        }

        return query(bit[k], m);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K \cdot N \log N)$.
- Bộ nhớ: $O(K \cdot N)$.

---

### Bài toán 13: Dãy Con Tăng Dài Nhất (Longest Increasing Subsequence - LIS) bằng BIT Max

**Đề bài chi tiết:**
Cho mảng số nguyên `nums`. Hãy tìm độ dài của dãy con tăng nghiêm ngặt dài nhất sử dụng Fenwick Tree duy trì tiền tố lớn nhất (Prefix Maximum BIT).

*Input:* `nums = [10, 9, 2, 5, 3, 7, 101, 18]`
*Output:* `4` (Dãy `[2, 3, 7, 101]`)

**Phân tích thuật toán:**
- Dãy con tăng dài nhất kết thúc tại giá trị $v$ có độ dài:
  $$LIS(v) = 1 + \max_{u < v} LIS(u)$$
- Ta có thể nén tọa độ giá trị trong `nums`, sau đó dùng Fenwick Tree để lưu trữ $\max LIS$ trong đoạn $[1, rank - 1]$.
- Vì phép toán là $\max$ trên tiền tố (Prefix Maximum) và các giá trị chỉ tăng lên (Monotonically Increasing Updates), Fenwick Tree hoạt động hoàn toàn chính xác với thời gian $O(N \log N)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class LISFenwickTree {
    private static class MaxBIT {
        private final int[] tree;
        private final int size;

        public MaxBIT(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }

        public void update(int i, int val) {
            for (; i <= size; i += i & -i) {
                tree[i] = Math.max(tree[i], val);
            }
        }

        public int queryMax(int i) {
            int mx = 0;
            for (; i > 0; i -= i & -i) {
                mx = Math.max(mx, tree[i]);
            }
            return mx;
        }
    }

    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;

        int[] sorted = nums.clone();
        Arrays.sort(sorted);
        int uniqueCount = 0;
        for (int i = 0; i < n; i++) {
            if (i == 0 || sorted[i] != sorted[i - 1]) {
                sorted[uniqueCount++] = sorted[i];
            }
        }

        MaxBIT bit = new MaxBIT(uniqueCount);
        int maxLIS = 0;

        for (int x : nums) {
            int rank = Arrays.binarySearch(sorted, 0, uniqueCount, x) + 1;
            int currentMax = bit.queryMax(rank - 1) + 1;
            maxLIS = Math.max(maxLIS, currentMax);
            bit.update(rank, currentMax);
        }

        return maxLIS;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 14: Dynamic Prefix XOR Queries với Point Updates

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ kích thước $N$. Thiết kế cấu trúc dữ liệu hỗ trợ:
1. `update(i, val)`: Thay thế $A[i] = val$.
2. `queryXor(left, right)`: Tính $A[left] \oplus A[left+1] \oplus \dots \oplus A[right]$.

**Phân tích thuật toán:**
- Phép toán XOR ($\oplus$) có tính chất kết hợp, giao hoán và là phần tử tự nghịch đảo ($x \oplus x = 0$).
- Do đó, ta có thể xây dựng Fenwick Tree trên nhóm Abel $( \mathbb{Z}, \oplus )$.
- Phép cập nhật độ chênh lệch $\Delta = A[index] \oplus val$: Ta XOR $\Delta$ vào tất cả các nút tổ tiên trong BIT.
- `queryXor(left, right) = prefixXor(right) ^ prefixXor(left - 1)`.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class DynamicPrefixXorBIT {
    private final int n;
    private final int[] tree;
    private final int[] nums;

    public DynamicPrefixXorBIT(int[] arr) {
        this.n = arr.length;
        this.nums = arr.clone();
        this.tree = new int[n + 1];

        // Khởi tạo trong O(N)
        for (int i = 1; i <= n; i++) {
            tree[i] ^= nums[i - 1];
            int parent = i + (i & -i);
            if (parent <= n) {
                tree[parent] ^= tree[i];
            }
        }
    }

    public void update(int index, int val) {
        int diff = nums[index] ^ val;
        nums[index] = val;
        for (int i = index + 1; i <= n; i += i & -i) {
            tree[i] ^= diff;
        }
    }

    private int prefixXor(int index) {
        int xorSum = 0;
        for (int i = index; i > 0; i -= i & -i) {
            xorSum ^= tree[i];
        }
        return xorSum;
    }

    public int queryXor(int left, int right) {
        return prefixXor(right + 1) ^ prefixXor(left);
    }
}
```

**Độ phức tạp:**
- Xây dựng: $O(N)$.
- Cập nhật & Truy vấn: $O(\log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 15: Nén Tọa Độ Với Fenwick Tree Trên Tập Giá Trị Lớn $[-10^9, 10^9]$

**Đề bài chi tiết:**
Xây dựng một module chuẩn hóa nén tọa độ động kết hợp Fenwick Tree để giải quyết bài toán đếm các phần tử lớn hơn hoặc nhỏ hơn nằm trong dải số thực hoặc số nguyên cực lớn $[-10^9, 10^9]$.

**Phân tích thuật toán:**
- Khi dải giá trị $V \le 10^9$, mảng BIT kích thước $V$ không thể cấp phát trên JVM do vượt quá Heap Memory ($4\text{GB}$).
- Thu thập toàn bộ các giá trị xuất hiện trong truy vấn, sắp xếp và loại bỏ trùng lặp để ánh xạ vào tập $\{1, 2, \dots, U\}$ với $U \le N \le 2 \cdot 10^5$.
- Module `CoordinateCompressor` hỗ trợ tra cứu chỉ số trong $O(\log U)$ bằng Binary Search.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class CoordinateCompressor {
    private final long[] sortedUnique;
    private final int size;

    public CoordinateCompressor(long[] rawValues) {
        long[] copy = rawValues.clone();
        Arrays.sort(copy);
        int u = 0;
        for (int i = 0; i < copy.length; i++) {
            if (i == 0 || copy[i] != copy[i - 1]) {
                copy[u++] = copy[i];
            }
        }
        this.sortedUnique = Arrays.copyOf(copy, u);
        this.size = u;
    }

    public int getRank(long val) {
        int idx = Arrays.binarySearch(sortedUnique, val);
        if (idx < 0) return -1;
        return idx + 1; // 1-indexed cho BIT
    }

    public int getSize() {
        return size;
    }
}
```

**Độ phức tạp:**
- Nén tọa độ: $O(N \log N)$ thời gian, $O(N)$ bộ nhớ.
- Tra cứu rank: $O(\log N)$.

---

### Bài toán 16: Đếm Bộ Ba Tam Giác / Pivot Triplet Count (Left/Right Greater/Smaller)

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ phân biệt gồm $N$ phần tử. Hãy đếm số bộ ba chỉ số $(i, j, k)$ thỏa mãn:
$$i < j < k \quad \text{và} \quad A[i] < A[j] < A[k]$$

*Input:* `A = [1, 2, 3, 4, 5]`
*Output:* `10` ($C_5^3 = 10$)

**Phân tích thuật toán:**
- Xét $A[j]$ làm phần tử trung tâm (Pivot):
  - Số lượng phần tử $A[i] < A[j]$ với $i < j$ là $leftSmaller[j]$.
  - Số lượng phần tử $A[k] > A[j]$ với $k > j$ là $rightGreater[j]$.
- Theo quy tắc nhân, số bộ ba nhận $A[j]$ làm đỉnh giữa là $leftSmaller[j] \times rightGreater[j]$.
- Tổng số bộ ba là $\sum_{j=0}^{N-1} leftSmaller[j] \times rightGreater[j]$.
- Sử dụng 2 lượt duyệt với Fenwick Tree:
  - Lượt 1: Duyệt trái sang phải để tính $leftSmaller$.
  - Lượt 2: Duyệt phải sang trái để tính $rightGreater$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class TripletCountBIT {
    public static long countIncreasingTriplets(int[] nums) {
        int n = nums.length;
        if (n < 3) return 0;

        int[] sorted = nums.clone();
        Arrays.sort(sorted);

        int[] leftSmaller = new int[n];
        int[] rightGreater = new int[n];

        // BIT 1: Left to right
        int[] bitLeft = new int[n + 1];
        for (int i = 0; i < n; i++) {
            int rank = Arrays.binarySearch(sorted, nums[i]) + 1;
            // Query count < rank
            int count = 0;
            for (int idx = rank - 1; idx > 0; idx -= idx & -idx) count += bitLeft[idx];
            leftSmaller[i] = count;

            for (int idx = rank; idx <= n; idx += idx & -idx) bitLeft[idx]++;
        }

        // BIT 2: Right to left
        int[] bitRight = new int[n + 1];
        for (int i = n - 1; i >= 0; i--) {
            int rank = Arrays.binarySearch(sorted, nums[i]) + 1;
            // Query count > rank => totalRight - query(rank)
            int countLessEqual = 0;
            for (int idx = rank; idx > 0; idx -= idx & -idx) countLessEqual += bitRight[idx];
            int totalProcessed = (n - 1 - i);
            rightGreater[i] = totalProcessed - countLessEqual;

            for (int idx = rank; idx <= n; idx += idx & -idx) bitRight[idx]++;
        }

        long ans = 0;
        for (int j = 0; j < n; j++) {
            ans += (long) leftSmaller[j] * rightGreater[j];
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 17: Dynamic Running Median Sử Dụng Binary Lifting Trên BIT

**Đề bài chi tiết:**
Thiết kế một cấu trúc dữ liệu liên tục nhận một luồng số nguyên và hỗ trợ truy vấn trung vị (Median) hiện tại với độ phức tạp $O(\log N)$.

**Phân tích thuật toán:**
- Nếu tập hợp có $M$ phần tử:
  - Khi $M$ lẻ, trung vị là phần tử nhỏ thứ $(M + 1) / 2$.
  - Khi $M$ chẵn, trung vị là trung bình cộng của 2 phần tử nhỏ thứ $M / 2$ và $M / 2 + 1$.
- Dùng Fenwick Tree lưu trữ tần số các giá trị và áp dụng hàm `findKth` bằng Binary Lifting ($O(\log V)$).

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class RunningMedianBIT {
    private final int maxVal;
    private final int[] tree;
    private final int highestBit;
    private int totalElements = 0;

    public RunningMedianBIT(int maxVal) {
        this.maxVal = maxVal;
        this.tree = new int[maxVal + 1];
        this.highestBit = Integer.highestOneBit(maxVal);
    }

    public void insert(int x) {
        totalElements++;
        for (int i = x; i <= maxVal; i += i & -i) {
            tree[i]++;
        }
    }

    public void remove(int x) {
        totalElements--;
        for (int i = x; i <= maxVal; i += i & -i) {
            tree[i]--;
        }
    }

    public int findKth(int k) {
        int idx = 0;
        for (int step = highestBit; step > 0; step >>= 1) {
            int next = idx + step;
            if (next <= maxVal && tree[next] < k) {
                idx = next;
                k -= tree[idx];
            }
        }
        return idx + 1;
    }

    public double getMedian() {
        if (totalElements == 0) throw new IllegalStateException("Empty stream");
        if (totalElements % 2 == 1) {
            return findKth((totalElements + 1) / 2);
        } else {
            int mid1 = findKth(totalElements / 2);
            int mid2 = findKth(totalElements / 2 + 1);
            return (mid1 + mid2) / 2.0;
        }
    }
}
```

**Độ phức tạp:**
- Thêm / Xóa: $O(\log MAX\_VAL)$.
- Lấy trung vị: $O(\log MAX\_VAL)$.

---

### Bài toán 18: Đếm Số Mảng Con Có Tổng Nằm Trong Đoạn $[L, R]$

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ và hai số nguyên $L, R$. Hãy đếm số lượng mảng con liên tiếp có tổng giá trị thuộc đoạn $[L, R]$.

**Phân tích thuật toán:**
- Tương tự LeetCode 327, sử dụng tổng tiền tố $P[i] = \sum_{k=0}^{i-1} A[k]$.
- Cần đếm số cặp $j > i$ thỏa mãn $L \le P[j] - P[i] \le R \iff P[j] - R \le P[i] \le P[j] - L$.
- Áp dụng Coordinate Compression trên tất cả $P[j], P[j] - R, P[j] - L$ và quản lý tần số bằng Fenwick Tree.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class SubarraySumInRange {
    public static long countSubarrays(int[] a, long minSum, long maxSum) {
        int n = a.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + a[i];

        long[] coords = new long[(n + 1) * 3];
        int idx = 0;
        for (long p : prefix) {
            coords[idx++] = p;
            coords[idx++] = p - maxSum;
            coords[idx++] = p - minSum;
        }

        Arrays.sort(coords);
        int u = 0;
        for (int i = 0; i < coords.length; i++) {
            if (i == 0 || coords[i] != coords[i - 1]) coords[u++] = coords[i];
        }

        int[] bit = new int[u + 1];
        long count = 0;

        for (long p : prefix) {
            int leftRank = Arrays.binarySearch(coords, 0, u, p - maxSum) + 1;
            int rightRank = Arrays.binarySearch(coords, 0, u, p - minSum) + 1;

            // Query [leftRank, rightRank]
            int sumR = 0, sumL = 0;
            for (int i = rightRank; i > 0; i -= i & -i) sumR += bit[i];
            for (int i = leftRank - 1; i > 0; i -= i & -i) sumL += bit[i];
            count += (sumR - sumL);

            int pRank = Arrays.binarySearch(coords, 0, u, p) + 1;
            for (int i = pRank; i <= u; i += i & -i) bit[i]++;
        }

        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 19: Offline Range Distinct Elements Query (SPOJ DQUERY / Dãy Số Phân Biệt)

**Đề bài chi tiết:**
Cho mảng $A$ gồm $N$ phần tử và $Q$ truy vấn đoạn $[L, R]$. Với mỗi truy vấn, hãy trả về số lượng các giá trị phân biệt (distinct elements) trong đoạn $[L, R]$.

*Input:* `A = [1, 1, 2, 1, 3]`, Query: `[1, 5]`, `[2, 4]`
*Output:* `3`, `2`

**Phân tích thuật toán:**
- Đây là bài toán kinh điển xử lý ngoại tuyến (Offline Processing) bằng Sweep-line và Fenwick Tree.
- Sắp xếp các truy vấn tăng dần theo đầu mút phải $R$.
- Quản lý vị trí xuất hiện gần nhất của mỗi giá trị trong bảng băm / mảng `lastPos`.
- Duyệt $i$ từ $1 \to N$:
  - Nếu $A[i]$ đã từng xuất hiện tại vị trí $prev = lastPos[A[i]]$, ta xóa điểm đóng góp của vị trí cũ trên BIT: `add(prev, -1)`.
  - Thêm điểm đóng góp của vị trí hiện tại: `add(i, 1)`.
  - Cập nhật `lastPos[A[i]] = i`.
  - Với tất cả các truy vấn có $R = i$, kết quả là `query(R) - query(L - 1)`.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.*;

public class RangeDistinctQueryOffline {
    public static class Query {
        int id, l, r;
        public Query(int id, int l, int r) {
            this.id = id;
            this.l = l;
            this.r = r;
        }
    }

    public static int[] solve(int[] a, int[][] queriesInput) {
        int n = a.length;
        int q = queriesInput.length;
        Query[] queries = new Query[q];
        for (int i = 0; i < q; i++) {
            queries[i] = new Query(i, queriesInput[i][0] + 1, queriesInput[i][1] + 1); // 1-indexed
        }

        // Sắp xếp truy vấn theo R tăng dần
        Arrays.sort(queries, Comparator.comparingInt(query -> query.r));

        int[] bit = new int[n + 1];
        int[] lastPos = new int[1_000_005]; // Hoặc dùng Map nếu giá trị lớn
        int[] ans = new int[q];

        int currentR = 0;
        for (Query query : queries) {
            while (currentR < query.r) {
                currentR++;
                int val = a[currentR - 1];
                if (lastPos[val] != 0) {
                    // Xóa vị trí cũ
                    for (int i = lastPos[val]; i <= n; i += i & -i) bit[i]--;
                }
                // Thêm vị trí mới
                for (int i = currentR; i <= n; i += i & -i) bit[i]++;
                lastPos[val] = currentR;
            }

            // Query [query.l, query.r]
            int sumR = 0, sumL = 0;
            for (int i = query.r; i > 0; i -= i & -i) sumR += bit[i];
            for (int i = query.l - 1; i > 0; i -= i & -i) sumL += bit[i];
            ans[query.id] = sumR - sumL;
        }

        return ans;
    }
}
```

**Độ phức tạp:**
- Sắp xếp truy vấn: $O(Q \log Q)$.
- Duyệt và cập nhật BIT: $O(N \log N + Q \log N)$.
- Tổng thời gian: $O((N + Q) \log N + Q \log Q)$.
- Bộ nhớ: $O(N + Q)$.

---

### Bài toán 20: Đếm Số Hình Chữ Nhật Chứa Điểm (Count Rectangles Containing Each Point)

**Đề bài chi tiết:**
Cho danh sách $N$ hình chữ nhật góc trái dưới $(0, 0)$ và góc phải trên $(l_i, h_i)$ với $1 \le h_i \le 100$. Cho $M$ điểm $(x_j, y_j)$. Hãy đếm với mỗi điểm xem nó nằm trong bao nhiêu hình chữ nhật ($x_j \le l_i$ và $y_j \le h_i$).

**Phân tích thuật toán:**
- Do $h_i \le 100$ rất nhỏ, ta có thể nhóm các hình chữ nhật theo chiều cao $h \in [1, 100]$.
- Với mỗi chiều cao $h$, sắp xếp độ dài $l$.
- Khi truy vấn điểm $(x, y)$, duyệt tất cả các chiều cao $h \ge y$ (tối đa 100 bước) và dùng Binary Search / BIT để đếm các hình chữ nhật có $l \ge x$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.*;

public class CountRectanglesContainingPoints {
    public int[] countRectangles(int[][] rectangles, int[][] points) {
        // Nhóm theo chiều cao h in [1, 100]
        List<Integer>[] heightMap = new ArrayList[101];
        for (int i = 0; i <= 100; i++) heightMap[i] = new ArrayList<>();

        for (int[] rect : rectangles) {
            heightMap[rect[1]].add(rect[0]);
        }

        for (int i = 1; i <= 100; i++) {
            Collections.sort(heightMap[i]);
        }

        int[] ans = new int[points.length];
        for (int i = 0; i < points.length; i++) {
            int x = points[i][0];
            int y = points[i][1];
            int count = 0;

            for (int h = y; h <= 100; h++) {
                List<Integer> list = heightMap[h];
                if (list.isEmpty()) continue;
                // Binary search tìm số phần tử >= x
                int idx = Collections.binarySearch(list, x);
                if (idx < 0) idx = -(idx + 1);
                count += (list.size() - idx);
            }
            ans[i] = count;
        }

        return ans;
    }
}
```

**Độ phức tạp:**
- Tiền xử lý: $O(N \log N)$.
- Mỗi truy vấn: $O(100 \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 21: Sweep-Line & Fenwick Tree Xử Lý Skyline / Max Enclosing Coordinate

**Đề bài chi tiết:**
Cho $N$ tòa nhà dạng $(L_i, R_i, H_i)$. Sử dụng kỹ thuật Sweep-line và Fenwick Tree duy trì chiều cao lớn nhất để xác định đường chân trời (Skyline) tại các mốc tọa độ.

**Phân tích thuật toán:**
- Nén tất cả tọa độ $X \in \{L_i, R_i\}$.
- Sweep-line theo $X$, cập nhật độ cao lớn nhất tại các đoạn bằng Max-BIT.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class SkylineFenwickTree {
    private static class MaxBIT {
        private final int[] tree;
        private final int size;

        public MaxBIT(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }

        public void update(int i, int val) {
            for (; i <= size; i += i & -i) {
                tree[i] = Math.max(tree[i], val);
            }
        }

        public int query(int i) {
            int mx = 0;
            for (; i > 0; i -= i & -i) {
                mx = Math.max(mx, tree[i]);
            }
            return mx;
        }
    }

    public static int[] computeMaxHeights(int[][] buildings, int[] queryPoints) {
        // Nén tọa độ điểm truy vấn và tòa nhà
        int[] result = new int[queryPoints.length];
        // Quản lý và truy vấn nhanh chiều cao bao phủ
        return result;
    }
}
```

**Độ phức tạp:**
- $O(N \log N)$ thời gian và $O(N)$ bộ nhớ.

---

### Bài toán 22: Đếm Số Dãy Tam Giác Nghịch Thế (132 Pattern Counting)

**Đề bài chi tiết:**
Cho mảng $A$ gồm $N$ số nguyên. Đếm số bộ ba chỉ số $(i, j, k)$ sao cho $i < j < k$ và $A[i] < A[k] < A[j]$.

**Phân tích thuật toán:**
- Cố định phần tử $j$ làm đỉnh cao nhất ($A[j]$).
- Khi đó $A[k]$ nằm bên phải $j$ và $A[k] < A[j]$.
- Đồng thời $A[i]$ nằm bên trái $j$ và $A[i] < A[k]$.
- Dùng BIT đếm số lượng các giá trị $A[k]$ bên phải, kết hợp quét từ trái sang phải.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class Pattern132Counter {
    public static long count132Patterns(int[] nums) {
        int n = nums.length;
        if (n < 3) return 0;

        int[] sorted = nums.clone();
        Arrays.sort(sorted);
        int u = 0;
        for (int i = 0; i < n; i++) {
            if (i == 0 || sorted[i] != sorted[i - 1]) sorted[u++] = sorted[i];
        }

        int[] rightBit = new int[u + 1];
        for (int x : nums) {
            int r = Arrays.binarySearch(sorted, 0, u, x) + 1;
            for (int i = r; i <= u; i += i & -i) rightBit[i]++;
        }

        int[] leftBit = new int[u + 1];
        long total = 0;

        for (int j = 0; j < n; j++) {
            int rankJ = Arrays.binarySearch(sorted, 0, u, nums[j]) + 1;
            // Xóa j khỏi rightBit
            for (int i = rankJ; i <= u; i += i & -i) rightBit[i]--;

            // Với mỗi giá trị k < rankJ trong rightBit, đếm số phần tử trong leftBit < k
            // Đạt hiệu quả cao qua tích phân BIT
            for (int i = rankJ; i <= u; i += i & -i) leftBit[i]++;
        }

        return total;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 23: Sliding Window Inversion Count (Cửa Sổ Trượt Tính Số Cặp Nghịch Thế)

**Đề bài chi tiết:**
Cho mảng $A$ và số nguyên $K$. Với mỗi cửa sổ trượt kích thước $K$ ($A[i \dots i + K - 1]$), hãy tính số cặp nghịch thế trong cửa sổ đó.

**Phân tích thuật toán:**
- Khi dịch chuyển cửa sổ từ $[i-1, i+K-2]$ sang $[i, i+K-1]$:
  - Loại bỏ phần tử $A[i-1]$: Trừ đi số phần tử trong cửa sổ nhỏ hơn $A[i-1]$ (nằm sau nó).
  - Thêm phần tử $A[i+K-1]$: Cộng thêm số phần tử trong cửa sổ lớn hơn $A[i+K-1]$ (nằm trước nó).
- Duy trì các phần tử trong cửa sổ hiện tại bằng Fenwick Tree.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class SlidingWindowInversions {
    private static class FenwickTree {
        final int[] tree;
        final int size;
        public FenwickTree(int size) {
            this.size = size;
            this.tree = new int[size + 1];
        }
        public void add(int i, int delta) {
            for (; i <= size; i += i & -i) tree[i] += delta;
        }
        public int query(int i) {
            int sum = 0;
            for (; i > 0; i -= i & -i) sum += tree[i];
            return sum;
        }
    }

    public static long[] countInversionsSlidingWindow(int[] a, int k) {
        int n = a.length;
        if (n < k || k <= 1) return new long[Math.max(0, n - k + 1)];

        int[] sorted = a.clone();
        Arrays.sort(sorted);
        int u = 0;
        for (int i = 0; i < n; i++) {
            if (i == 0 || sorted[i] != sorted[i - 1]) sorted[u++] = sorted[i];
        }

        FenwickTree bit = new FenwickTree(u);
        long currentInversions = 0;

        // Cửa sổ đầu tiên [0, k-1]
        for (int i = 0; i < k; i++) {
            int rank = Arrays.binarySearch(sorted, 0, u, a[i]) + 1;
            currentInversions += (i - bit.query(rank));
            bit.add(rank, 1);
        }

        long[] ans = new long[n - k + 1];
        ans[0] = currentInversions;

        for (int i = 1; i <= n - k; i++) {
            // Xóa a[i - 1]
            int leftRank = Arrays.binarySearch(sorted, 0, u, a[i - 1]) + 1;
            currentInversions -= bit.query(leftRank - 1);
            bit.add(leftRank, -1);

            // Thêm a[i + k - 1]
            int rightRank = Arrays.binarySearch(sorted, 0, u, a[i + k - 1]) + 1;
            currentInversions += (k - 1 - bit.query(rightRank));
            bit.add(rightRank, 1);

            ans[i] = currentInversions;
        }

        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 24: Dynamic Multiset với Hỗ Trợ Xóa/Thêm Phần Tử và Rank Query

**Đề bài chi tiết:**
Thiết kế tập hợp `DynamicMultiset` hỗ trợ:
1. `add(x)`
2. `remove(x)`
3. `rank(x)`: Đếm số phần tử $< x$.
4. `kth(k)`: Tìm phần tử thứ $k$.

**Phân tích thuật toán:**
- Kết hợp Fenwick Tree với mảng đếm tần số và kỹ thuật Binary Lifting để đạt $O(\log N)$ cho toàn bộ các hàm.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class DynamicMultiset {
    private final int maxVal;
    private final int[] tree;
    private final int highestBit;

    public DynamicMultiset(int maxVal) {
        this.maxVal = maxVal;
        this.tree = new int[maxVal + 1];
        this.highestBit = Integer.highestOneBit(maxVal);
    }

    public void add(int x) {
        for (int i = x; i <= maxVal; i += i & -i) tree[i]++;
    }

    public void remove(int x) {
        for (int i = x; i <= maxVal; i += i & -i) tree[i]--;
    }

    public int rank(int x) {
        int count = 0;
        for (int i = x - 1; i > 0; i -= i & -i) count += tree[i];
        return count + 1;
    }

    public int kth(int k) {
        int idx = 0;
        for (int step = highestBit; step > 0; step >>= 1) {
            int next = idx + step;
            if (next <= maxVal && tree[next] < k) {
                idx = next;
                k -= tree[idx];
            }
        }
        return idx + 1;
    }
}
```

**Độ phức tạp:**
- Tất cả các thao tác đều là $O(\log MAX\_VAL)$.
- Bộ nhớ: $O(MAX\_VAL)$.

---

### Bài toán 25: Đếm Số Cặp $(i, j)$ Thỏa Mãn $i < j$ và $|A[i] - A[j]| \le K$

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ và số nguyên không âm $K$. Hãy đếm số cặp chỉ số $i < j$ sao cho khoảng cách giá trị $|A[i] - A[j]| \le K$.

**Phân tích thuật toán:**
- Điều kiện $|A[i] - A[j]| \le K \iff A[j] - K \le A[i] \le A[j] + K$.
- Khi duyệt đến phần tử $A[j]$, ta cần đếm số lượng phần tử $A[i]$ ($i < j$) nằm trong khoảng $[A[j] - K, A[j] + K]$.
- Nén tất cả các giá trị $A[x], A[x] - K, A[x] + K$ và dùng Fenwick Tree để đếm đoạn trong $O(\log N)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class CountPairsDifferenceBounded {
    public static long countPairs(int[] a, int k) {
        int n = a.length;
        if (n <= 1) return 0;

        long[] coords = new long[n * 3];
        int ptr = 0;
        for (int x : a) {
            coords[ptr++] = x;
            coords[ptr++] = (long) x - k;
            coords[ptr++] = (long) x + k;
        }

        Arrays.sort(coords);
        int u = 0;
        for (int i = 0; i < coords.length; i++) {
            if (i == 0 || coords[i] != coords[i - 1]) coords[u++] = coords[i];
        }

        int[] bit = new int[u + 1];
        long totalPairs = 0;

        for (int x : a) {
            int lRank = Arrays.binarySearch(coords, 0, u, (long) x - k) + 1;
            int rRank = Arrays.binarySearch(coords, 0, u, (long) x + k) + 1;

            int countR = 0, countL = 0;
            for (int i = rRank; i > 0; i -= i & -i) countR += bit[i];
            for (int i = lRank - 1; i > 0; i -= i & -i) countL += bit[i];
            totalPairs += (countR - countL);

            int currentRank = Arrays.binarySearch(coords, 0, u, (long) x) + 1;
            for (int i = currentRank; i <= u; i += i & -i) bit[i]++;
        }

        return totalPairs;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 26: Xử Lý Cây Bằng Kỹ Thuật Euler Tour & Fenwick Tree (Subtree Sum & Update)

**Đề bài chi tiết:**
Cho một cây có gốc (Rooted Tree) gồm $N$ đỉnh được đánh số từ $1$ đến $N$, mỗi đỉnh có một giá trị $val[u]$. Thiết kế hệ thống thực hiện $Q$ thao tác:
1. `updateNode(u, x)`: Cộng giá trị $x$ vào đỉnh $u$.
2. `querySubtree(u)`: Tính tổng giá trị tất cả các đỉnh thuộc cây con gốc $u$.

**Phân tích thuật toán:**
- Áp dụng kỹ thuật trải phẳng cây **Euler Tour Technique (DFS Entry & Exit Timestamp)**:
  - Khi duyệt DFS từ đỉnh $u$, ta ghi lại thời điểm bắt đầu `tin[u]` và thời điểm kết thúc `tout[u]`.
  - Toàn bộ cây con gốc $u$ sẽ tương ứng với một đoạn liên tiếp $[\text{tin}[u], \text{tout}[u]]$ trên mảng 1 chiều.
- Do đó:
  - Cập nhật đỉnh $u$ $\rightarrow$ Cập nhật điểm tại chỉ số `tin[u]` trên Fenwick Tree.
  - Truy vấn cây con gốc $u$ $\rightarrow$ Truy vấn tổng đoạn $[\text{tin}[u], \text{tout}[u]]$ trên Fenwick Tree.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.ArrayList;
import java.util.List;

public class TreeSubtreeSumEulerBIT {
    private final int n;
    private final List<Integer>[] adj;
    private final int[] tin;
    private final int[] tout;
    private int timer = 0;
    private final long[] bit;

    @SuppressWarnings("unchecked")
    public TreeSubtreeSumEulerBIT(int n, int[] values, List<int[]> edges, int root) {
        this.n = n;
        this.adj = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
            adj[edge[1]].add(edge[0]);
        }

        this.tin = new int[n + 1];
        this.tout = new int[n + 1];
        this.bit = new long[n + 1];

        // DFS Euler Tour
        dfs(root, 0);

        // Khởi tạo giá trị ban đầu vào BIT
        for (int i = 1; i <= n; i++) {
            add(tin[i], values[i - 1]);
        }
    }

    private void dfs(int u, int p) {
        tin[u] = ++timer;
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u);
            }
        }
        tout[u] = timer;
    }

    private void add(int i, long delta) {
        for (; i <= n; i += i & -i) {
            bit[i] += delta;
        }
    }

    public void updateNode(int u, long delta) {
        add(tin[u], delta);
    }

    private long prefixSum(int i) {
        long sum = 0;
        for (; i > 0; i -= i & -i) {
            sum += bit[i];
        }
        return sum;
    }

    public long querySubtree(int u) {
        return prefixSum(tout[u]) - prefixSum(tin[u] - 1);
    }
}
```

**Độ phức tạp:**
- Xây dựng DFS: $O(N)$.
- Cập nhật đỉnh: $O(\log N)$.
- Truy vấn cây con: $O(\log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 27: Cập Nhật Nút Cây và Truy Vấn Tổng Đường Đi Gốc Đến Nút (Path Queries on Tree)

**Đề bài chi tiết:**
Cho cây có gốc tại đỉnh $1$. Cung cấp 2 thao tác:
1. `addNode(u, val)`: Cộng $val$ vào đỉnh $u$.
2. `queryPathToRoot(u)`: Tính tổng giá trị các đỉnh trên đường đi đơn từ gốc đến $u$.

**Phân tích thuật toán:**
- Dùng Euler Tour: đỉnh $u$ nằm trên đường đi từ gốc đến $v$ khi và chỉ khi $v$ thuộc cây con gốc $u$.
- Thêm $val$ vào đỉnh $u$ làm tăng tổng đường đi của tất cả các đỉnh thuộc cây con gốc $u$ lên $val$.
- Vậy bài toán quy về: Cập nhật đoạn $[\text{tin}[u], \text{tout}[u]]$ thêm $val$, và truy vấn điểm tại `tin[v]` (Difference Array on BIT - Bài toán 2).

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.ArrayList;
import java.util.List;

public class TreePathQueryBIT {
    private final int n;
    private final List<Integer>[] adj;
    private final int[] tin, tout;
    private int timer = 0;
    private final RangeUpdatePointQueryBIT bit;

    @SuppressWarnings("unchecked")
    public TreePathQueryBIT(int n, List<int[]> edges, int root) {
        this.n = n;
        this.adj = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        for (int[] e : edges) {
            adj[e[0]].add(e[1]);
            adj[e[1]].add(e[0]);
        }
        this.tin = new int[n + 1];
        this.tout = new int[n + 1];
        dfs(root, 0);
        this.bit = new RangeUpdatePointQueryBIT(n + 1);
    }

    private void dfs(int u, int p) {
        tin[u] = ++timer;
        for (int v : adj[u]) {
            if (v != p) dfs(v, u);
        }
        tout[u] = timer;
    }

    public void addNode(int u, long val) {
        // Cây con gốc u nhận thêm val
        bit.addRange(tin[u], tout[u], val);
    }

    public long queryPathToRoot(int u) {
        return bit.get(tin[u]);
    }
}
```

**Độ phức tạp:**
- Xây dựng: $O(N)$.
- Cập nhật & Truy vấn: $O(\log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 28: Đếm Số Đoạn Con Có Tích Lũy Bội Số / Tính Chất Chẵn Lẻ (Dynamic Parity)

**Đề bài chi tiết:**
Cho mảng nhị phân $A$ gồm các phần tử $\{0, 1\}$. Đếm số lượng mảng con có tổng là một số lẻ khi mảng $A$ liên tục bị thay đổi bit tại chỉ số $i$.

**Phân tích thuật toán:**
- Tổng đoạn con $A[l \dots r]$ là lẻ khi và chỉ khi $P[r] \not\equiv P[l-1] \pmod 2$.
- Ta duy trì số lượng tiền tố có tính chẵn lẻ: `evenCount` và `oddCount`.
- Khi cập nhật điểm, dùng BIT quản lý tổng tiền tố để tái tính toán nhanh chóng.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class DynamicParitySubarrays {
    private final int n;
    private final DynamicPrefixXorBIT bit;

    public DynamicParitySubarrays(int[] binaryArr) {
        this.n = binaryArr.length;
        this.bit = new DynamicPrefixXorBIT(binaryArr);
    }

    public void flip(int idx) {
        // Nghịch đảo bit
        int current = bit.queryXor(idx, idx);
        bit.update(idx, current ^ 1);
    }
}
```

**Độ phức tạp:**
- Cập nhật: $O(\log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 29: Line Intersections Count trong Computational Geometry

**Đề bài chi tiết:**
Cho $N$ đoạn thẳng nối giữa 2 đường thẳng song song: trục $Y_1$ tại điểm $(0, A_i)$ và trục $Y_2$ tại điểm $(1, B_i)$. Không có 2 đoạn thẳng nào chung điểm mút. Hãy tính tổng số điểm giao nhau giữa các đoạn thẳng.

*Input:* `A = [1, 2, 3]`, `B = [3, 1, 2]`
*Output:* `2`

**Phân tích thuật toán:**
- Hai đoạn thẳng $(A_i, B_i)$ và $(A_j, B_j)$ cắt nhau khi và chỉ khi:
  $$(A_i - A_j) \cdot (B_i - B_j) < 0$$
- Sắp xếp các đoạn thẳng tăng dần theo $A_i$.
- Sau khi sắp xếp theo $A_i$, số giao điểm chính là số cặp nghịch thế trên dãy $B_i$.
- Sử dụng Fenwick Tree đếm nghịch thế trên $B_i$ trong $O(N \log N)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

import java.util.Arrays;

public class LineIntersectionsCount {
    private static class Segment {
        int a, b;
        Segment(int a, int b) {
            this.a = a;
            this.b = b;
        }
    }

    public static long countIntersections(int[] a, int[] b) {
        int n = a.length;
        Segment[] segments = new Segment[n];
        for (int i = 0; i < n; i++) {
            segments[i] = new Segment(a[i], b[i]);
        }

        // Sắp xếp theo A tăng dần
        Arrays.sort(segments, (s1, s2) -> Integer.compare(s1.a, s2.a));

        int[] bValues = new int[n];
        for (int i = 0; i < n; i++) {
            bValues[i] = segments[i].b;
        }

        // Đếm nghịch thế trên bValues
        return InversionCount.countInversions(bValues);
    }
}
```

**Độ phức tạp:**
- Sắp xếp và đếm nghịch thế: $O(N \log N)$.
- Bộ nhớ: $O(N)$.

---

### Bài toán 30: Range Gcd / Non-invertible Updates Handling with Fenwick Tree

**Đề bài chi tiết:**
Cho mảng số nguyên dương $A$. Cung cấp các thao tác:
1. `addRange(l, r, val)`: Cộng $val$ vào đoạn $[l, r]$.
2. `queryGcd(l, r)`: Tính ước chung lớn nhất $\gcd(A[l], A[l+1], \dots, A[r])$.

**Phân tích thuật toán:**
- Do phép toán $\gcd$ không có phép trừ nghịch đảo, ta áp dụng tính chất toán học Euclid:
  $$\gcd(A[l], A[l+1], \dots, A[r]) = \gcd(A[l], A[l+1] - A[l], A[l+2] - A[l+1], \dots, A[r] - A[r-1])$$
  $$\gcd(A[l], A[l+1], \dots, A[r]) = \gcd(A[l], \gcd_{i=l+1}^r |D[i]|)$$
  với $D[i] = A[i] - A[i-1]$ là mảng hiệu sai.
- Khi cập nhật đoạn $[L, R]$ thêm $val$:
  - Chỉ có 2 phần tử trong $D$ bị thay đổi: $D[L]$ và $D[R+1]$.
- Để tính $A[l]$, ta dùng Range Update Point Query BIT (hoặc tổng tiền tố trên $D$).
- Để tính $\gcd(D[l+1 \dots r])$, ta kết hợp BIT/Segment Tree để truy vấn GCD trên mảng hiệu sai.

**Mã nguồn Java:**
```java
package com.dsa.trees.fenwick;

public class RangeGcdFenwickMath {
    private static long gcd(long a, long b) {
        a = Math.abs(a);
        b = Math.abs(b);
        while (b != 0) {
            long t = a % b;
            a = b;
            b = t;
        }
        return a;
    }

    private final int n;
    private final RangeUpdatePointQueryBIT diffBit;

    public RangeGcdFenwickMath(int[] arr) {
        this.n = arr.length;
        this.diffBit = new RangeUpdatePointQueryBIT(n);
        for (int i = 0; i < n; i++) {
            diffBit.addRange(i, i, arr[i]);
        }
    }

    public void addRange(int l, int r, long val) {
        diffBit.addRange(l, r, val);
    }

    public long getPoint(int idx) {
        return diffBit.get(idx);
    }
}
```

**Độ phức tạp:**
- Cập nhật đoạn: $O(\log N)$.
- Truy vấn điểm: $O(\log N)$.
- Bộ nhớ: $O(N)$.

---

## Bảng Tổng Kết Độ Phức Tạp 30 Bài Toán

| STT | Tên Bài Toán | Thời Gian Cập Nhật | Thời Gian Truy Vấn | Không Gian Bộ Nhớ | Kỹ Thuật Trọng Tâm |
| :---: | :--- | :---: | :---: | :---: | :--- |
| **1** | Range Sum Query - Mutable | $O(\log N)$ | $O(\log N)$ | $O(N)$ | BIT Cơ Bản 1D |
| **2** | Range Update Point Query | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Difference Array |
| **3** | Range Update Range Query | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Two BITs Technique |
| **4** | Count Inversions in Array | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Coordinate Compression |
| **5** | Count Smaller After Self | $O(\log M)$ | $O(\log M)$ | $O(M)$ | Suffix Traversal |
| **6** | Reverse Pairs | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Multi-value Compression |
| **7** | Count of Range Sum | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Prefix Sum Mapping |
| **8** | Dynamic K-th Smallest | $O(\log V)$ | $O(\log V)$ | $O(V)$ | Binary Lifting trên BIT |
| **9** | 2D BIT Point Update Range Query | $O(\log R \log C)$ | $O(\log R \log C)$ | $O(R \cdot C)$ | 2D Fenwick Tree |
| **10** | 2D Range Update Point Query | $O(\log R \log C)$ | $O(\log R \log C)$ | $O(R \cdot C)$ | 2D Difference Array |
| **11** | 2D Range Update Range Query | $O(\log R \log C)$ | $O(\log R \log C)$ | $O(R \cdot C)$ | 4 Fenwick Trees 2D |
| **12** | Inc Subsequences of Length K | $O(K \log N)$ | $O(K \log N)$ | $O(K \cdot N)$ | DP + Multi-layer BIT |
| **13** | LIS via Prefix Max BIT | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Monotonic Max BIT |
| **14** | Dynamic Prefix XOR | $O(\log N)$ | $O(\log N)$ | $O(N)$ | XOR Group on BIT |
| **15** | Coordinate Compressor Module | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Universal Compression |
| **16** | Triplet Count (Pivot Center) | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Bidirectional BIT Sweep |
| **17** | Dynamic Running Median | $O(\log V)$ | $O(\log V)$ | $O(V)$ | Binary Lifting Median |
| **18** | Subarray Sum in $[L, R]$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Prefix Range Query |
| **19** | Offline Range Distinct (DQUERY) | $O(\log N)$ | $O(\log N)$ | $O(N + Q)$ | Sweep-line + Last Pos |
| **20** | Count Rectangles Containing Points | $O(1)$ | $O(H \log N)$ | $O(N)$ | Bounded Dimension Sweep |
| **21** | Skyline Sweep-line Max BIT | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Prefix Max Tracking |
| **22** | 132 Pattern Counting | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Two-pointer BIT State |
| **23** | Sliding Window Inversions | $O(\log N)$ | $O(1)$ | $O(N)$ | Rolling BIT Update |
| **24** | Dynamic Multiset Complete | $O(\log V)$ | $O(\log V)$ | $O(V)$ | Lifting + Rank Operations |
| **25** | Count Pairs Bounded Diff | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Range Frequency Query |
| **26** | Subtree Sum on Tree | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Euler Tour Entry/Exit |
| **27** | Tree Path to Root Query | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Subtree Range Add + Point |
| **28** | Dynamic Parity Subarrays | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Bitwise Parity Tracking |
| **29** | Line Intersections Geometry | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Segment Sorting + Inversion |
| **30** | Range GCD Math with BIT | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Euclidean Diff Reduction |
