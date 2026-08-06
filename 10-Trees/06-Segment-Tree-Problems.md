# 30 Bài tập Segment Tree

## 1. Range Sum Query - Mutable (LeetCode 307)
**Đề bài chi tiết:**
Cho một mảng số nguyên `nums` và xử lý hai loại truy vấn:
1. Cập nhật (Point Update): Thay đổi giá trị của `nums[i]` thành `val`.
2. Truy vấn tổng (Range Sum Query): Tính tổng các phần tử từ chỉ số `left` đến `right`.

**Phân tích thuật toán:**
Sử dụng Segment tree cơ bản (không cần lazy). Các node lưu tổng các đoạn. Cập nhật và truy vấn đều đạt độ phức tạp $O(\log N)$. Có thể dùng Iterative Segment Tree.

**Mã nguồn Java:**
```java
class NumArray {
    int[] tree;
    int n;

    public NumArray(int[] nums) {
        n = nums.length;
        tree = new int[n * 2];
        for (int i = 0; i < n; i++) tree[n + i] = nums[i];
        for (int i = n - 1; i > 0; --i) tree[i] = tree[i * 2] + tree[i * 2 + 1];
    }
    
    public void update(int index, int val) {
        index += n;
        tree[index] = val;
        while (index > 0) {
            int left = index;
            int right = index;
            if (index % 2 == 0) right = index + 1;
            else left = index - 1;
            tree[index / 2] = tree[left] + tree[right];
            index /= 2;
        }
    }
    
    public int sumRange(int left, int right) {
        left += n;
        right += n;
        int sum = 0;
        while (left <= right) {
            if ((left % 2) == 1) {
                sum += tree[left];
                left++;
            }
            if ((right % 2) == 0) {
                sum += tree[right];
                right--;
            }
            left /= 2;
            right /= 2;
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Khởi tạo: $O(N)$
- Update/Query: $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 2. Count of Smaller Numbers After Self (LeetCode 315)
**Đề bài chi tiết:**
Cho mảng số nguyên `nums`, trả về mảng `counts` trong đó `counts[i]` là số lượng các phần tử nhỏ hơn `nums[i]` nằm bên phải `nums[i]`.

**Phân tích thuật toán:**
Duyệt mảng ngược từ cuối lên. Dùng Segment Tree lưu số đếm (tần số) của các giá trị đã gặp. Do `nums[i]` có thể âm hoặc rất lớn, ta có thể Discretize (rời rạc hóa) các giá trị, hoặc cộng offset tĩnh. Sau đó truy vấn Range Sum từ $[0, nums[i] - 1]$.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<Integer> countSmaller(int[] nums) {
        int offset = 10000;
        int size = 20000 + 1;
        int[] tree = new int[size * 2];
        List<Integer> res = new ArrayList<>();
        
        for (int i = nums.length - 1; i >= 0; i--) {
            int smallerCount = query(0, nums[i] + offset - 1, tree, size);
            res.add(smallerCount);
            update(nums[i] + offset, 1, tree, size);
        }
        
        Collections.reverse(res);
        return res;
    }
    
    private void update(int index, int val, int[] tree, int size) {
        index += size;
        tree[index] += val;
        while (index > 1) {
            index /= 2;
            tree[index] = tree[index * 2] + tree[index * 2 + 1];
        }
    }
    
    private int query(int left, int right, int[] tree, int size) {
        left += size;
        right += size;
        int sum = 0;
        while (left <= right) {
            if (left % 2 == 1) sum += tree[left++];
            if (right % 2 == 0) sum += tree[right--];
            left /= 2;
            right /= 2;
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log M)$ với $M$ là dải giá trị ($20000$).
- Không gian bộ nhớ: $O(M)$

---

## 3. Reverse Pairs (LeetCode 493)
**Đề bài chi tiết:**
Cho một mảng `nums`. Tính số cặp `(i, j)` sao cho `i < j` và `nums[i] > 2 * (long)nums[j]`.

**Phân tích thuật toán:**
Tương tự bài Count Smaller, kết hợp với Rời rạc hóa (Discretization) vì giá trị $2 \times nums[j]$ có thể vượt `int`. Tuy nhiên ta sẽ duyệt từ trái qua phải, hoặc dùng Segment Tree đếm số lượng các phần tử đã duyệt. Ta cần truy vấn các phần tử trong Segment Tree lớn hơn $2 \times nums[j]$ hoặc nhỏ hơn.

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
        int rank = 0;
        for (long val : set) {
            map.put(val, rank++);
        }
        
        int[] tree = new int[rank * 2];
        int count = 0;
        
        for (int i = 0; i < nums.length; i++) {
            int left = map.get((long) nums[i] * 2) + 1;
            int right = rank - 1;
            count += query(left, right, tree, rank);
            update(map.get((long) nums[i]), tree, rank);
        }
        
        return count;
    }
    
    private void update(int index, int[] tree, int n) {
        index += n;
        tree[index]++;
        while (index > 1) {
            index /= 2;
            tree[index] = tree[2 * index] + tree[2 * index + 1];
        }
    }
    
    private int query(int left, int right, int[] tree, int n) {
        left += n;
        right += n;
        int sum = 0;
        while (left <= right) {
            if (left % 2 == 1) sum += tree[left++];
            if (right % 2 == 0) sum += tree[right--];
            left /= 2;
            right /= 2;
        }
        return sum;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log N)$
- Không gian bộ nhớ: $O(N)$

---

## 4. Range Module (LeetCode 715)
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu theo dõi các nửa đoạn $[left, right)$. Các thao tác:
- `addRange(left, right)`: thêm đoạn.
- `queryRange(left, right)`: kiểm tra xem toàn bộ các số thực trong đoạn đã được track hay chưa.
- `removeRange(left, right)`: gỡ bỏ đoạn.

**Phân tích thuật toán:**
Bài toán này đòi hỏi Dynamic Segment Tree do giá trị tọa độ có thể lên đến $10^9$. Node chứa trạng thái `boolean` (đã track toàn bộ). Sử dụng Lazy Propagation để thêm hoặc xóa khoảng.

**Mã nguồn Java:**
```java
class RangeModule {
    class Node {
        int left, right;
        boolean tracked;
        boolean lazy;
        Node leftChild, rightChild;
        
        public Node(int left, int right) {
            this.left = left;
            this.right = right;
        }
    }
    
    Node root;

    public RangeModule() {
        root = new Node(1, (int)1e9);
    }
    
    public void addRange(int left, int right) {
        update(root, left, right - 1, true);
    }
    
    public boolean queryRange(int left, int right) {
        return query(root, left, right - 1);
    }
    
    public void removeRange(int left, int right) {
        update(root, left, right - 1, false);
    }
    
    private void update(Node node, int L, int R, boolean val) {
        if (node.left >= L && node.right <= R) {
            node.tracked = val;
            node.lazy = true;
            return;
        }
        pushDown(node);
        int mid = node.left + (node.right - node.left) / 2;
        if (L <= mid) update(node.leftChild, L, R, val);
        if (R > mid) update(node.rightChild, L, R, val);
        node.tracked = node.leftChild.tracked && node.rightChild.tracked;
    }
    
    private boolean query(Node node, int L, int R) {
        if (node.left >= L && node.right <= R) {
            return node.tracked;
        }
        pushDown(node);
        int mid = node.left + (node.right - node.left) / 2;
        boolean res = true;
        if (L <= mid) res &= query(node.leftChild, L, R);
        if (R > mid) res &= query(node.rightChild, L, R);
        return res;
    }
    
    private void pushDown(Node node) {
        if (node.leftChild == null) {
            int mid = node.left + (node.right - node.left) / 2;
            node.leftChild = new Node(node.left, mid);
            node.rightChild = new Node(mid + 1, node.right);
        }
        if (node.lazy) {
            node.leftChild.tracked = node.tracked;
            node.leftChild.lazy = true;
            node.rightChild.tracked = node.tracked;
            node.rightChild.lazy = true;
            node.lazy = false;
        }
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(\log M)$ mỗi query/update, $M = 10^9$.
- Không gian bộ nhớ: $O(Q \log M)$, với $Q$ là số thao tác.

---

## 5. Falling Squares (LeetCode 699)
**Đề bài chi tiết:**
Cho các hình vuông rơi xuống trục tọa độ x. Hình vuông có thể chồng lên nhau. Trả về mảng lưu chiều cao lớn nhất hiện tại trên mặt đất sau khi mỗi hình vuông rơi xuống.

**Phân tích thuật toán:**
Dùng Segment tree và Rời rạc hóa tọa độ $X$. Tại mỗi lần một hình có đoạn $[L, R]$ rơi xuống, ta truy vấn chiều cao Max trong khoảng đó `cur_h = query(L, R-1)`. Cập nhật đoạn mới có độ cao `cur_h + size`. `Max` của toàn bộ cây chính là kết quả của bước đó.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class Node {
        int maxH;
        int lazy;
        Node left, right;
    }
    Node root = new Node();
    
    private void update(Node node, int l, int r, int L, int R, int val) {
        if (L <= l && r <= R) {
            node.maxH = val;
            node.lazy = val;
            return;
        }
        pushDown(node);
        int mid = l + (r - l) / 2;
        if (L <= mid) update(node.left, l, mid, L, R, val);
        if (R > mid) update(node.right, mid + 1, r, L, R, val);
        node.maxH = Math.max(node.left.maxH, node.right.maxH);
    }
    
    private int query(Node node, int l, int r, int L, int R) {
        if (L <= l && r <= R) return node.maxH;
        pushDown(node);
        int mid = l + (r - l) / 2;
        int max = 0;
        if (L <= mid) max = Math.max(max, query(node.left, l, mid, L, R));
        if (R > mid) max = Math.max(max, query(node.right, mid + 1, r, L, R));
        return max;
    }
    
    private void pushDown(Node node) {
        if (node.left == null) node.left = new Node();
        if (node.right == null) node.right = new Node();
        if (node.lazy > 0) {
            node.left.maxH = node.lazy;
            node.left.lazy = node.lazy;
            node.right.maxH = node.lazy;
            node.right.lazy = node.lazy;
            node.lazy = 0;
        }
    }
    
    public List<Integer> fallingSquares(int[][] positions) {
        List<Integer> res = new ArrayList<>();
        int N = (int)1e8;
        int maxAll = 0;
        for (int[] p : positions) {
            int left = p[0];
            int right = left + p[1] - 1;
            int h = query(root, 1, N, left, right);
            update(root, 1, N, left, right, h + p[1]);
            maxAll = Math.max(maxAll, h + p[1]);
            res.add(maxAll);
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log M)$ với $M=10^8$.
- Không gian: $O(N \log M)$

---

## 6. My Calendar III (LeetCode 732)
**Đề bài chi tiết:**
Lên lịch sự kiện nửa kín nửa hở $[start, end)$. Số lượng sự kiện giao nhau (k-booking) tối đa là bao nhiêu?

**Phân tích thuật toán:**
Dùng Dynamic Segment Tree kết hợp Lazy Propagation để lưu số lượng kiện trùng lặp. Update tăng dải $[start, end-1]$ thêm 1. Trả về `Max` giá trị node gốc mỗi lần.

**Mã nguồn Java:**
```java
class MyCalendarThree {
    class Node {
        int maxVal, lazy;
        Node left, right;
    }
    Node root;
    int MAX = (int) 1e9;

    public MyCalendarThree() {
        root = new Node();
    }
    
    public int book(int start, int end) {
        update(root, 0, MAX, start, end - 1, 1);
        return root.maxVal;
    }
    
    private void update(Node node, int l, int r, int L, int R, int val) {
        if (L <= l && r <= R) {
            node.maxVal += val;
            node.lazy += val;
            return;
        }
        pushDown(node);
        int mid = l + (r - l) / 2;
        if (L <= mid) update(node.left, l, mid, L, R, val);
        if (R > mid) update(node.right, mid + 1, r, L, R, val);
        node.maxVal = Math.max(node.left.maxVal, node.right.maxVal);
    }
    
    private void pushDown(Node node) {
        if (node.left == null) node.left = new Node();
        if (node.right == null) node.right = new Node();
        if (node.lazy != 0) {
            node.left.maxVal += node.lazy;
            node.left.lazy += node.lazy;
            node.right.maxVal += node.lazy;
            node.right.lazy += node.lazy;
            node.lazy = 0;
        }
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(\log M)$ mỗi lượt book ($M=10^9$).
- Không gian: $O(Q \log M)$

---

## 7. Number of Longest Increasing Subsequence (LeetCode 673)
**Đề bài chi tiết:**
Cho mảng `nums`, trả về số lượng dãy con tăng dài nhất (LIS).

**Phân tích thuật toán:**
Cần tìm max length và số lần xuất hiện. Sử dụng Segment Tree với thao tác kết hợp cả length và count. Giá trị của một node là cặp `(len, count)`.
Node quản lý khoảng giá trị của nums (cần rời rạc hóa).
Lặp từ trái qua phải, với mỗi $X = nums[i]$, tìm max length và count tương ứng trên đoạn nhỏ hơn $X$, cập nhật lại cho $X$.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class Node {
        int len, count;
        public Node(int l, int c) { len = l; count = c; }
    }
    
    class SegmentTree {
        Node[] tree;
        int n;
        public SegmentTree(int n) {
            this.n = n;
            tree = new Node[4 * n];
            for (int i=0; i<4*n; i++) tree[i] = new Node(0, 1);
        }
        
        private Node merge(Node a, Node b) {
            if (a.len == b.len) {
                if (a.len == 0) return new Node(0, 1);
                return new Node(a.len, a.count + b.count);
            }
            return a.len > b.len ? a : b;
        }
        
        public void update(int node, int start, int end, int idx, Node val) {
            if (start == end) {
                tree[node] = merge(tree[node], val);
                return;
            }
            int mid = start + (end - start) / 2;
            if (idx <= mid) update(2*node+1, start, mid, idx, val);
            else update(2*node+2, mid+1, end, idx, val);
            tree[node] = merge(tree[2*node+1], tree[2*node+2]);
        }
        
        public Node query(int node, int start, int end, int L, int R) {
            if (L > end || R < start) return new Node(0, 1);
            if (L <= start && end <= R) return tree[node];
            int mid = start + (end - start) / 2;
            return merge(query(2*node+1, start, mid, L, R), query(2*node+2, mid+1, end, L, R));
        }
    }
    
    public int findNumberOfLIS(int[] nums) {
        if (nums.length == 0) return 0;
        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;
        for (int num : nums) {
            min = Math.min(min, num);
            max = Math.max(max, num);
        }
        
        SegmentTree tree = new SegmentTree(max - min + 1);
        for (int num : nums) {
            int val = num - min;
            Node q = tree.query(0, 0, max - min, 0, val - 1);
            tree.update(0, 0, max - min, val, new Node(q.len + 1, q.count));
        }
        
        return tree.query(0, 0, max - min, 0, max - min).count;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log M)$ ($M$ là phạm vi min-max).
- Không gian: $O(M)$

---

## 8. The Skyline Problem (LeetCode 218)
**Đề bài chi tiết:**
Trả về tập các tọa độ hình bóng (skyline) của các tòa nhà. Tòa nhà có dạng `[left, right, height]`.

**Phân tích thuật toán:**
Bài toán này thường được giải bằng Max Heap/TreeMap quét trục $X$. Tuy nhiên, có thể giải bằng Segment Tree + Lazy Propagation để gán (Max Height) cho các khoảng x. Sau đó duyệt trên lá để lấy độ cao và kết xuất. Rời rạc hóa tọa độ $X$.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    int[] tree, lazy;
    
    private void pushDown(int node) {
        if (lazy[node] != 0) {
            tree[node*2] = Math.max(tree[node*2], lazy[node]);
            lazy[node*2] = Math.max(lazy[node*2], lazy[node]);
            
            tree[node*2+1] = Math.max(tree[node*2+1], lazy[node]);
            lazy[node*2+1] = Math.max(lazy[node*2+1], lazy[node]);
            
            lazy[node] = 0;
        }
    }
    
    private void update(int node, int l, int r, int L, int R, int h) {
        if (L <= l && r <= R) {
            tree[node] = Math.max(tree[node], h);
            lazy[node] = Math.max(lazy[node], h);
            return;
        }
        pushDown(node);
        int mid = l + (r - l) / 2;
        if (L <= mid) update(node*2, l, mid, L, R, h);
        if (R > mid) update(node*2+1, mid+1, r, L, R, h);
    }
    
    private int query(int node, int l, int r, int idx) {
        if (l == r) return tree[node];
        pushDown(node);
        int mid = l + (r - l) / 2;
        if (idx <= mid) return query(node*2, l, mid, idx);
        else return query(node*2+1, mid+1, r, idx);
    }
    
    public List<List<Integer>> getSkyline(int[][] buildings) {
        Set<Integer> set = new TreeSet<>();
        for (int[] b : buildings) {
            set.add(b[0]);
            set.add(b[1]);
        }
        Map<Integer, Integer> map = new HashMap<>();
        Map<Integer, Integer> revMap = new HashMap<>();
        int id = 0;
        for (int x : set) {
            map.put(x, id);
            revMap.put(id, x);
            id++;
        }
        
        int n = id;
        tree = new int[4 * n];
        lazy = new int[4 * n];
        
        for (int[] b : buildings) {
            update(1, 0, n - 1, map.get(b[0]), map.get(b[1]) - 1, b[2]);
        }
        
        List<List<Integer>> res = new ArrayList<>();
        int prevH = 0;
        for (int i = 0; i < n; i++) {
            int h = query(1, 0, n - 1, i);
            if (h != prevH) {
                res.add(Arrays.asList(revMap.get(i), h));
                prevH = h;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log N)$ (do thao tác map và cập nhật tree)
- Không gian: $O(N)$

---

## 9. Create Sorted Array through Instructions (LeetCode 1649)
**Đề bài chi tiết:**
Bạn có một mảng `instructions`. Với mỗi số nguyên, bạn có thể chèn nó vào một mảng mục tiêu được sắp xếp. Chi phí chèn bằng mức tối thiểu giữa số phần tử thực sự nhỏ hơn và số phần tử thực sự lớn hơn nó đang có mặt. Tìm tổng chi phí (modulo $10^9+7$).

**Phân tích thuật toán:**
Dùng Segment Tree (hoặc BIT) đếm số lượng các giá trị đã được chèn. Truy vấn tổng trên khoảng $[0, X-1]$ và $[X+1, MAX]$ và lấy `min` làm chi phí. Cập nhật đếm số $X$ tăng thêm 1.

**Mã nguồn Java:**
```java
class Solution {
    int[] tree;
    int MAX = 100000;
    
    private void update(int index) {
        index += MAX;
        tree[index]++;
        while (index > 1) {
            index /= 2;
            tree[index] = tree[2*index] + tree[2*index+1];
        }
    }
    
    private int query(int left, int right) {
        left += MAX;
        right += MAX;
        int sum = 0;
        while (left <= right) {
            if (left % 2 == 1) sum += tree[left++];
            if (right % 2 == 0) sum += tree[right--];
            left /= 2;
            right /= 2;
        }
        return sum;
    }

    public int createSortedArray(int[] instructions) {
        tree = new int[MAX * 2 + 2];
        long cost = 0;
        int MOD = (int)1e9 + 7;
        
        for (int val : instructions) {
            int less = query(0, val - 1);
            int greater = query(val + 1, MAX);
            cost = (cost + Math.min(less, greater)) % MOD;
            update(val);
        }
        return (int) cost;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log M)$ ($M=10^5$).
- Không gian: $O(M)$

---

## 10. Rectangle Area II (LeetCode 850)
**Đề bài chi tiết:**
Cho danh sách các hình chữ nhật trên tọa độ. Trả về tổng diện tích hợp của các hình chữ nhật, modulo $10^9+7$.

**Phân tích thuật toán:**
Dùng kỹ thuật quét trục (Line Sweep) + Segment Tree. Trục $X$ là thời gian. Các sự kiện: thêm viền trái hình chữ nhật (cộng độ phủ $Y$), bỏ viền phải (trừ độ phủ $Y$). Segment Tree quản lý độ phủ của các khoảng giá trị trên trục $Y$. Nút gốc luôn giữ độ phủ hiện tại. Diện tích += độ phủ $\times$ delta $X$.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class Node {
        int count;
        long len;
    }
    
    int[] yVals;
    Node[] tree;
    
    private void update(int node, int l, int r, int L, int R, int val) {
        if (L <= l && r <= R) {
            tree[node].count += val;
        } else {
            int mid = l + (r - l) / 2;
            if (L <= mid) update(2*node+1, l, mid, L, R, val);
            if (R > mid) update(2*node+2, mid+1, r, L, R, val);
        }
        
        if (tree[node].count > 0) {
            tree[node].len = yVals[r+1] - yVals[l];
        } else {
            tree[node].len = (l == r) ? 0 : tree[2*node+1].len + tree[2*node+2].len;
        }
    }
    
    public int rectangleArea(int[][] rectangles) {
        int MOD = (int)1e9 + 7;
        Set<Integer> ySet = new TreeSet<>();
        List<int[]> events = new ArrayList<>();
        
        for (int[] r : rectangles) {
            events.add(new int[]{r[0], r[1], r[3], 1});
            events.add(new int[]{r[2], r[1], r[3], -1});
            ySet.add(r[1]);
            ySet.add(r[3]);
        }
        
        events.sort((a, b) -> Integer.compare(a[0], b[0]));
        yVals = new int[ySet.size()];
        int idx = 0;
        for (int y : ySet) yVals[idx++] = y;
        
        tree = new Node[4 * yVals.length];
        for (int i=0; i<tree.length; i++) tree[i] = new Node();
        
        long ans = 0;
        long prevX = events.get(0)[0];
        
        for (int[] e : events) {
            ans = (ans + (e[0] - prevX) * tree[0].len) % MOD;
            
            int l = Arrays.binarySearch(yVals, e[1]);
            int r = Arrays.binarySearch(yVals, e[2]) - 1;
            
            if (l <= r) {
                update(0, 0, yVals.length - 2, l, r, e[3]);
            }
            prevX = e[0];
        }
        return (int) ans;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \log N)$
- Không gian: $O(N)$

---

## 11. K-th Empty Space (HackerRank)
**Đề bài chi tiết:**
Cho một mảng ban đầu chỉ toàn các vị trí trống (đánh dấu là chưa được lấp đầy). Các thao tác bao gồm việc điền vào một số vị trí và truy vấn tìm vị trí trống thứ $K$ từ trái sang phải.

**Phân tích thuật toán:**
Dùng Segment Tree lưu số lượng vị trí trống trong từng đoạn. Mỗi lần điền ta update số lượng ở vị trí đó thành 0 (mất đi). Khi truy vấn tìm vị trí trống thứ $K$, tại mỗi Node, ta so sánh $K$ với số lượng vị trí trống ở cây con trái (`left_count`). Nếu $K \le left\_count$, ta đi xuống cây con trái; nếu $K > left\_count$, ta đi xuống cây con phải và tìm vị trí trống thứ $K - left\_count$.

**Mã nguồn Java:**
```java
class KthEmptySpace {
    int[] tree;
    int n;

    public KthEmptySpace(int n) {
        this.n = n;
        tree = new int[4 * n];
        build(1, 0, n - 1);
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = 1; // 1 means empty
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = tree[2 * node] + tree[2 * node + 1];
        }
    }

    public void fill(int index) {
        update(1, 0, n - 1, index);
    }

    private void update(int node, int start, int end, int idx) {
        if (start == end) {
            tree[node] = 0; // filled
            return;
        }
        int mid = start + (end - start) / 2;
        if (idx <= mid) update(2 * node, start, mid, idx);
        else update(2 * node + 1, mid + 1, end, idx);
        tree[node] = tree[2 * node] + tree[2 * node + 1];
    }

    public int findKthEmpty(int k) {
        return query(1, 0, n - 1, k);
    }

    private int query(int node, int start, int end, int k) {
        if (start == end) {
            return start;
        }
        int mid = start + (end - start) / 2;
        if (tree[2 * node] >= k) {
            return query(2 * node, start, mid, k);
        } else {
            return query(2 * node + 1, mid + 1, end, k - tree[2 * node]);
        }
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 12. Range Minimum Query (SPOJ RMQSQ)
**Đề bài chi tiết:**
Cho một mảng $N$ phần tử không thay đổi. Cần thực hiện $Q$ truy vấn, mỗi truy vấn yêu cầu tìm giá trị nhỏ nhất trong đoạn $[L, R]$ của mảng.

**Phân tích thuật toán:**
Dùng Segment Tree cơ bản, trong đó mỗi Node quản lý giá trị `min` của đoạn mà nó đại diện. Hàm kết hợp (merge) khi xây dựng cây hoặc truy vấn chính là `Math.min(left_child, right_child)`. Vì mảng tĩnh, ta không cần cài đặt hàm `update`.

**Mã nguồn Java:**
```java
class RMQ {
    int[] tree;
    int[] arr;
    int n;

    public RMQ(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new int[4 * n];
        build(1, 0, n - 1);
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = Math.min(tree[2 * node], tree[2 * node + 1]);
        }
    }

    public int queryMin(int l, int r) {
        return query(1, 0, n - 1, l, r);
    }

    private int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return Integer.MAX_VALUE;
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        return Math.min(query(2 * node, start, mid, l, r), query(2 * node + 1, mid + 1, end, l, r));
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 13. Maximum Subarray Sum in Range (SPOJ GSS1)
**Đề bài chi tiết:**
Cho mảng $N$ phần tử. Có $Q$ truy vấn, mỗi truy vấn yêu cầu tìm tổng của dãy con liên tiếp có tổng lớn nhất nằm trọn trong đoạn $[L, R]$. Mảng không bị cập nhật.

**Phân tích thuật toán:**
Mỗi Node của Segment Tree lưu 4 giá trị: `sum` (tổng cả đoạn), `pref` (tổng prefix lớn nhất), `suff` (tổng suffix lớn nhất), và `ans` (tổng dãy con liên tiếp lớn nhất). Khi merge 2 đoạn, ta cập nhật các biến này tương ứng. Ví dụ `ans` lớn nhất sẽ là max của `left.ans`, `right.ans`, hoặc dãy con nằm vắt ngang giữa hai đoạn là `left.suff + right.pref`.

**Mã nguồn Java:**
```java
class GSS1 {
    static class Node {
        long sum, pref, suff, ans;
        Node() {}
        Node(long val) {
            sum = pref = suff = ans = val;
        }
    }
    
    Node[] tree;
    int[] arr;
    int n;

    public GSS1(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new Node[4 * n];
        build(1, 0, n - 1);
    }

    private Node merge(Node left, Node right) {
        Node res = new Node();
        res.sum = left.sum + right.sum;
        res.pref = Math.max(left.pref, left.sum + right.pref);
        res.suff = Math.max(right.suff, right.sum + left.suff);
        res.ans = Math.max(Math.max(left.ans, right.ans), left.suff + right.pref);
        return res;
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = new Node(arr[start]);
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
        }
    }

    public long queryMaxSubarray(int l, int r) {
        return query(1, 0, n - 1, l, r).ans;
    }

    private Node query(int node, int start, int end, int l, int r) {
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        if (r <= mid) return query(2 * node, start, mid, l, r);
        if (l > mid) return query(2 * node + 1, mid + 1, end, l, r);
        return merge(query(2 * node, start, mid, l, r), query(2 * node + 1, mid + 1, end, l, r));
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 14. Maximum Subarray Sum with Updates (SPOJ GSS3)
**Đề bài chi tiết:**
Giống hệt với bài 13 (GSS1), nhưng có thêm thao tác điểm (Point Update), cho phép thay đổi giá trị tại một vị trí $i$ thành $X$. Cần trả lời các truy vấn tìm mảng con có tổng lớn nhất trong đoạn $[L, R]$.

**Phân tích thuật toán:**
Cấu trúc Node và hàm `merge` hoàn toàn giống bài 13. Điểm khác biệt duy nhất là ta thêm vào hàm `update` để cập nhật Node lá tại chỉ số $i$, sau đó đi ngược lên cập nhật lại các Node tổ tiên thông qua hàm `merge`.

**Mã nguồn Java:**
```java
class GSS3 {
    static class Node {
        long sum, pref, suff, ans;
        Node() {}
        Node(long val) {
            sum = pref = suff = ans = val;
        }
    }
    
    Node[] tree;
    int[] arr;
    int n;

    public GSS3(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new Node[4 * n];
        build(1, 0, n - 1);
    }

    private Node merge(Node left, Node right) {
        Node res = new Node();
        res.sum = left.sum + right.sum;
        res.pref = Math.max(left.pref, left.sum + right.pref);
        res.suff = Math.max(right.suff, right.sum + left.suff);
        res.ans = Math.max(Math.max(left.ans, right.ans), left.suff + right.pref);
        return res;
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = new Node(arr[start]);
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
        }
    }
    
    public void update(int idx, int val) {
        updateTree(1, 0, n - 1, idx, val);
    }
    
    private void updateTree(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = new Node(val);
        } else {
            int mid = start + (end - start) / 2;
            if (idx <= mid) updateTree(2 * node, start, mid, idx, val);
            else updateTree(2 * node + 1, mid + 1, end, idx, val);
            tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
        }
    }

    public long queryMaxSubarray(int l, int r) {
        return query(1, 0, n - 1, l, r).ans;
    }

    private Node query(int node, int start, int end, int l, int r) {
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        if (r <= mid) return query(2 * node, start, mid, l, r);
        if (l > mid) return query(2 * node + 1, mid + 1, end, l, r);
        return merge(query(2 * node, start, mid, l, r), query(2 * node + 1, mid + 1, end, l, r));
    }
}
```
**Độ phức tạp:**
- Thời gian: Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 15. Matrix Queries (Codeforces)
**Đề bài chi tiết:**
Cho mảng các ma trận kích thước $2 \times 2$. Cần thực hiện các truy vấn tính tích của các ma trận liên tiếp trong đoạn $[L, R]$, kết quả các phần tử của ma trận được lấy theo modulo.

**Phân tích thuật toán:**
Mỗi Node của Segment Tree lưu trữ một ma trận $2 \times 2$. Phép kết hợp (merge) chính là phép nhân ma trận. Lưu ý phép nhân ma trận không có tính giao hoán, nên thứ tự nhân là đặc biệt quan trọng (nhân theo thứ tự `left_child * right_child`).

**Mã nguồn Java:**
```java
class MatrixQueries {
    static class Matrix {
        long mat[][] = new long[2][2];
        Matrix(long a, long b, long c, long d) {
            mat[0][0] = a; mat[0][1] = b;
            mat[1][0] = c; mat[1][1] = d;
        }
    }
    
    Matrix[] tree;
    Matrix[] arr;
    int n;
    long mod;

    public MatrixQueries(Matrix[] arr, long mod) {
        this.arr = arr;
        this.n = arr.length;
        this.mod = mod;
        tree = new Matrix[4 * n];
        build(1, 0, n - 1);
    }

    private Matrix multiply(Matrix A, Matrix B) {
        Matrix res = new Matrix(0, 0, 0, 0);
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                for (int k = 0; k < 2; k++) {
                    res.mat[i][j] = (res.mat[i][j] + A.mat[i][k] * B.mat[k][j]) % mod;
                }
            }
        }
        return res;
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = multiply(tree[2 * node], tree[2 * node + 1]);
        }
    }

    public Matrix query(int l, int r) {
        return queryTree(1, 0, n - 1, l, r);
    }

    private Matrix queryTree(int node, int start, int end, int l, int r) {
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        if (r <= mid) return queryTree(2 * node, start, mid, l, r);
        if (l > mid) return queryTree(2 * node + 1, mid + 1, end, l, r);
        return multiply(queryTree(2 * node, start, mid, l, r), queryTree(2 * node + 1, mid + 1, end, l, r));
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Query $O(\log N)$ (chậm hơn thông thường một hằng số nhỏ do nhân ma trận $2 \times 2$).
- Không gian bộ nhớ: $O(N)$

---

## 16. Number of Elements Greater Than K (SPOJ KQUERY)
**Đề bài chi tiết:**
Cho mảng $N$ phần tử. Cần trả lời các truy vấn tìm số lượng phần tử lớn hơn $K$ nằm trong đoạn $[L, R]$.

**Phân tích thuật toán:**
Sử dụng cấu trúc Merge Sort Tree (một dạng Segment Tree, trong đó mỗi Node lưu một mảng đã sắp xếp của tất cả các phần tử trong khoảng mà node đó quản lý). Khi truy vấn đoạn $[L, R]$, tại các Node bao phủ đoạn truy vấn, ta dùng tìm kiếm nhị phân (Upper Bound) trong mảng được lưu để đếm xem có bao nhiêu phần tử lớn hơn $K$.

**Mã nguồn Java:**
```java
import java.util.*;

class MergeSortTree {
    List<Integer>[] tree;
    int[] arr;
    int n;

    @SuppressWarnings("unchecked")
    public MergeSortTree(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new ArrayList[4 * n];
        build(1, 0, n - 1);
    }

    private void build(int node, int start, int end) {
        tree[node] = new ArrayList<>();
        if (start == end) {
            tree[node].add(arr[start]);
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            merge(tree[2 * node], tree[2 * node + 1], tree[node]);
        }
    }

    private void merge(List<Integer> left, List<Integer> right, List<Integer> res) {
        int i = 0, j = 0;
        while (i < left.size() && j < right.size()) {
            if (left.get(i) <= right.get(j)) res.add(left.get(i++));
            else res.add(right.get(j++));
        }
        while (i < left.size()) res.add(left.get(i++));
        while (j < right.size()) res.add(right.get(j++));
    }

    public int query(int l, int r, int k) {
        return queryTree(1, 0, n - 1, l, r, k);
    }

    private int queryTree(int node, int start, int end, int l, int r, int k) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) {
            return tree[node].size() - upperBound(tree[node], k);
        }
        int mid = start + (end - start) / 2;
        return queryTree(2 * node, start, mid, l, r, k) + queryTree(2 * node + 1, mid + 1, end, l, r, k);
    }
    
    private int upperBound(List<Integer> list, int k) {
        int low = 0, high = list.size() - 1, ans = list.size();
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (list.get(mid) > k) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \log N)$, Query $O(\log^2 N)$
- Không gian bộ nhớ: $O(N \log N)$

---

## 17. Balanced Brackets Range Query
**Đề bài chi tiết:**
Cho một chuỗi ngoặc tròn dài $N$. Cần trả lời các truy vấn kiểm tra số lượng dấu ngoặc tạo thành cặp hợp lệ tối đa có trong đoạn con $[L, R]$.

**Phân tích thuật toán:**
Mỗi Node lưu 3 thông tin: `open` (số lượng ngoặc mở chưa ghép), `close` (số lượng ngoặc đóng chưa ghép), `matches` (số cặp ngoặc hợp lệ đã ghép được trong đoạn này). Khi kết hợp 2 Node, số lượng cặp ngoặc mới được tạo thành là `newMatches = min(left.open, right.close)`. Khi đó `matches` tổng sẽ bằng tổng `matches` 2 bên cộng `newMatches`. Số ngoặc mở và đóng còn dư cũng được trừ đi phần đã được ghép cặp.

**Mã nguồn Java:**
```java
class BracketSegmentTree {
    static class Node {
        int open, close, matches;
        Node(int o, int c, int m) { open = o; close = c; matches = m; }
        Node() {}
    }

    Node[] tree;
    String s;
    int n;

    public BracketSegmentTree(String s) {
        this.s = s;
        this.n = s.length();
        tree = new Node[4 * n];
        build(1, 0, n - 1);
    }

    private Node merge(Node left, Node right) {
        Node res = new Node();
        int newMatches = Math.min(left.open, right.close);
        res.matches = left.matches + right.matches + newMatches;
        res.open = left.open + right.open - newMatches;
        res.close = left.close + right.close - newMatches;
        return res;
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = new Node(s.charAt(start) == '(' ? 1 : 0, s.charAt(start) == ')' ? 1 : 0, 0);
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
        }
    }
    
    public int getMaxBalancedLength(int l, int r) {
        return query(1, 0, n - 1, l, r).matches * 2;
    }

    private Node query(int node, int start, int end, int l, int r) {
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        if (r <= mid) return query(2 * node, start, mid, l, r);
        if (l > mid) return query(2 * node + 1, mid + 1, end, l, r);
        return merge(query(2 * node, start, mid, l, r), query(2 * node + 1, mid + 1, end, l, r));
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 18. Bitwise OR Range Query
**Đề bài chi tiết:**
Cho một mảng số nguyên. Cần xử lý các truy vấn tính kết quả Bitwise OR của các số nguyên trong đoạn $[L, R]$, đồng thời hỗ trợ thao tác cập nhật (Point Update) giá trị của một phần tử bất kỳ.

**Phân tích thuật toán:**
Đây là một biến thể cơ bản của Segment Tree, thay vì tính tổng (Sum) hay tìm Min/Max, phép toán ta sử dụng để gộp hai nhánh lại là toán tử OR (ký hiệu `|` trong Java): `tree[node] = tree[left] | tree[right]`.

**Mã nguồn Java:**
```java
class RangeBitwiseOR {
    int[] tree;
    int[] arr;
    int n;

    public RangeBitwiseOR(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new int[4 * n];
        build(1, 0, n - 1);
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = tree[2 * node] | tree[2 * node + 1];
        }
    }
    
    public void update(int idx, int val) {
        updateTree(1, 0, n - 1, idx, val);
    }
    
    private void updateTree(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
        } else {
            int mid = start + (end - start) / 2;
            if (idx <= mid) updateTree(2 * node, start, mid, idx, val);
            else updateTree(2 * node + 1, mid + 1, end, idx, val);
            tree[node] = tree[2 * node] | tree[2 * node + 1];
        }
    }

    public int queryOR(int l, int r) {
        return queryTree(1, 0, n - 1, l, r);
    }

    private int queryTree(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        return queryTree(2 * node, start, mid, l, r) | queryTree(2 * node + 1, mid + 1, end, l, r);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 19. Alternating Array Sum
**Đề bài chi tiết:**
Cho một mảng $N$ phần tử. Có các truy vấn tính tổng đan dấu trong đoạn $[L, R]$: $a[L] - a[L+1] + a[L+2] - a[L+3] + \dots$ và thao tác cập nhật một giá trị tại vị trí $i$.

**Phân tích thuật toán:**
Thay vì lưu một giá trị tổng, Segment tree sẽ lưu 2 giá trị tại mỗi Node: `sumEven` (tổng các phần tử ở vị trí chẵn) và `sumOdd` (tổng các phần tử ở vị trí lẻ). Khi được truy vấn tính tổng đan dấu trên đoạn $[L, R]$, nếu $L$ là chỉ số chẵn, kết quả sẽ là `sumEven - sumOdd` trên đoạn đó; nếu $L$ là chỉ số lẻ, kết quả ngược lại là `sumOdd - sumEven`.

**Mã nguồn Java:**
```java
class AlternatingSumTree {
    static class Node {
        long sumEven, sumOdd;
    }

    Node[] tree;
    int[] arr;
    int n;

    public AlternatingSumTree(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new Node[4 * n];
        for (int i = 0; i < tree.length; i++) tree[i] = new Node();
        build(1, 0, n - 1);
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            if (start % 2 == 0) tree[node].sumEven = arr[start];
            else tree[node].sumOdd = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node].sumEven = tree[2 * node].sumEven + tree[2 * node + 1].sumEven;
            tree[node].sumOdd = tree[2 * node].sumOdd + tree[2 * node + 1].sumOdd;
        }
    }
    
    public void update(int idx, int val) {
        updateTree(1, 0, n - 1, idx, val);
    }
    
    private void updateTree(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node].sumEven = tree[node].sumOdd = 0;
            if (start % 2 == 0) tree[node].sumEven = val;
            else tree[node].sumOdd = val;
        } else {
            int mid = start + (end - start) / 2;
            if (idx <= mid) updateTree(2 * node, start, mid, idx, val);
            else updateTree(2 * node + 1, mid + 1, end, idx, val);
            tree[node].sumEven = tree[2 * node].sumEven + tree[2 * node + 1].sumEven;
            tree[node].sumOdd = tree[2 * node].sumOdd + tree[2 * node + 1].sumOdd;
        }
    }

    public long queryAltSum(int l, int r) {
        Node res = queryTree(1, 0, n - 1, l, r);
        if (l % 2 == 0) return res.sumEven - res.sumOdd;
        else return res.sumOdd - res.sumEven;
    }

    private Node queryTree(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return new Node();
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        Node left = queryTree(2 * node, start, mid, l, r);
        Node right = queryTree(2 * node + 1, mid + 1, end, l, r);
        Node res = new Node();
        res.sumEven = left.sumEven + right.sumEven;
        res.sumOdd = left.sumOdd + right.sumOdd;
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 20. Subarray with Given GCD
**Đề bài chi tiết:**
Cho một mảng các số nguyên dương $N$. Cần trả lời các truy vấn tìm ước chung lớn nhất (GCD) của các phần tử trong đoạn $[L, R]$, hỗ trợ thêm thao tác cập nhật (Point Update) một phần tử.

**Phân tích thuật toán:**
Dùng Segment Tree để lưu trữ kết quả GCD của các đoạn. Khi merge hai node con để cập nhật cho Node cha, hàm kết hợp sẽ là `tree[node] = gcd(tree[left], tree[right])`. Khởi tạo hàm tính GCD dùng thuật toán Euclid cơ bản.

**Mã nguồn Java:**
```java
class GCDSegmentTree {
    int[] tree;
    int[] arr;
    int n;

    public GCDSegmentTree(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new int[4 * n];
        build(1, 0, n - 1);
    }

    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = gcd(tree[2 * node], tree[2 * node + 1]);
        }
    }
    
    public void update(int idx, int val) {
        updateTree(1, 0, n - 1, idx, val);
    }
    
    private void updateTree(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
        } else {
            int mid = start + (end - start) / 2;
            if (idx <= mid) updateTree(2 * node, start, mid, idx, val);
            else updateTree(2 * node + 1, mid + 1, end, idx, val);
            tree[node] = gcd(tree[2 * node], tree[2 * node + 1]);
        }
    }

    public int queryGCD(int l, int r) {
        return queryTree(1, 0, n - 1, l, r);
    }

    private int queryTree(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        int leftGCD = queryTree(2 * node, start, mid, l, r);
        int rightGCD = queryTree(2 * node + 1, mid + 1, end, l, r);
        if (leftGCD == 0) return rightGCD;
        if (rightGCD == 0) return leftGCD;
        return gcd(leftGCD, rightGCD);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \log (\text{max\_val}))$, Update/Query $O(\log N \cdot \log (\text{max\_val}))$
- Không gian bộ nhớ: $O(N)$

---

## 21. Lazy Propagation for AP (Arithmetic Progression) Addition
**Đề bài chi tiết:**
Thực hiện thao tác cộng một cấp số cộng vào các phần tử trong khoảng $[L, R]$. C cụ thể: $a[L] += a, a[L+1] += a + d, \dots, a[R] += a + (R-L) \times d$. Truy vấn yêu cầu tính tổng các phần tử trong đoạn bất kỳ.

**Phân tích thuật toán:**
Dùng Segment Tree với Lazy Propagation. Tại mỗi Node, lazy tag cần lưu 2 thông tin là `start_val` (giá trị bắt đầu của cấp số) và `diff` (công sai). Khi đẩy (push-down) tag xuống hai cây con: cây con trái kế thừa `start_val` và `diff`; cây con phải kế thừa `diff`, nhưng `start_val` mới sẽ là `start_val + len(left) * diff`. Khi áp dụng lazy, tổng (sum) của node sẽ tăng thêm một lượng bằng tổng cấp số cộng trên chiều dài `len`.

**Mã nguồn Java:**
```java
class SegmentTreeAP {
    static class Node {
        long sum, startVal, diff;
        boolean lazy;
    }
    
    Node[] tree;
    int n;
    
    public SegmentTreeAP(int n) {
        this.n = n;
        tree = new Node[4 * n];
        for (int i = 0; i < tree.length; i++) tree[i] = new Node();
    }
    
    private void applyLazy(int node, int len, long startVal, long diff) {
        tree[node].sum += len * (2 * startVal + (long)(len - 1) * diff) / 2;
        tree[node].startVal += startVal;
        tree[node].diff += diff;
        tree[node].lazy = true;
    }
    
    private void pushDown(int node, int start, int end) {
        if (tree[node].lazy) {
            int mid = start + (end - start) / 2;
            int leftLen = mid - start + 1;
            int rightLen = end - mid;
            
            applyLazy(2 * node, leftLen, tree[node].startVal, tree[node].diff);
            long rightStartVal = tree[node].startVal + leftLen * tree[node].diff;
            applyLazy(2 * node + 1, rightLen, rightStartVal, tree[node].diff);
            
            tree[node].startVal = 0;
            tree[node].diff = 0;
            tree[node].lazy = false;
        }
    }
    
    public void addAP(int node, int start, int end, int l, int r, long startVal, long diff) {
        if (r < start || end < l) return;
        if (l <= start && end <= r) {
            long currentStartVal = startVal + (start - l) * diff;
            applyLazy(node, end - start + 1, currentStartVal, diff);
            return;
        }
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        addAP(2 * node, start, mid, l, r, startVal, diff);
        addAP(2 * node + 1, mid + 1, end, l, r, startVal, diff);
        tree[node].sum = tree[2 * node].sum + tree[2 * node + 1].sum;
    }
    
    public long querySum(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node].sum;
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        return querySum(2 * node, start, mid, l, r) + querySum(2 * node + 1, mid + 1, end, l, r);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 22. Toggle Bits in Range
**Đề bài chi tiết:**
Cho mảng bit chỉ chứa 0 và 1. Hỗ trợ thao tác lật (Toggle/XOR) tất cả các bits trong một đoạn $[L, R]$ (biến 0 thành 1 và 1 thành 0) và truy vấn tính tổng số lượng số 1 trong đoạn $[L, R]$.

**Phân tích thuật toán:**
Dùng Segment Tree với Lazy Propagation. Giá trị lazy tag là cờ `boolean toggle`. Khi có lệnh toggle, ta đổi số lượng số 1 trong node thành `len - count_ones` (vì các số 0 biến thành 1 và ngược lại), và đảo trạng thái `toggle` tag (`lazy = !lazy`).

**Mã nguồn Java:**
```java
class ToggleSegmentTree {
    int[] tree;
    boolean[] lazy;
    int n;

    public ToggleSegmentTree(int n) {
        this.n = n;
        tree = new int[4 * n];
        lazy = new boolean[4 * n];
    }
    
    private void pushDown(int node, int start, int end) {
        if (lazy[node]) {
            int mid = start + (end - start) / 2;
            tree[2 * node] = (mid - start + 1) - tree[2 * node];
            lazy[2 * node] = !lazy[2 * node];
            
            tree[2 * node + 1] = (end - mid) - tree[2 * node + 1];
            lazy[2 * node + 1] = !lazy[2 * node + 1];
            
            lazy[node] = false;
        }
    }
    
    public void toggle(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return;
        if (l <= start && end <= r) {
            tree[node] = (end - start + 1) - tree[node];
            lazy[node] = !lazy[node];
            return;
        }
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        toggle(2 * node, start, mid, l, r);
        toggle(2 * node + 1, mid + 1, end, l, r);
        tree[node] = tree[2 * node] + tree[2 * node + 1];
    }
    
    public int queryOnes(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        return queryOnes(2 * node, start, mid, l, r) + queryOnes(2 * node + 1, mid + 1, end, l, r);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 23. Persistent Segment Tree for K-th Smallest (MKTHNUM)
**Đề bài chi tiết:**
Cho mảng $N$ phần tử tĩnh. Cần trả lời nhiều truy vấn trực tuyến: Tìm phần tử có giá trị nhỏ thứ $K$ trong đoạn $[L, R]$.

**Phân tích thuật toán:**
Dùng Persistent Segment Tree (cây phân đoạn bền vững). Cây lưu số lượng phần tử đã xuất hiện trong một khoảng giá trị. Mỗi phiên bản của cây (version $i$) lưu trạng thái khi đã xét các phần tử từ chỉ số $0$ đến $i$. Để truy vấn đoạn $[L, R]$, ta xét 2 phiên bản là phiên bản $R$ và phiên bản $L-1$. Số lượng phần tử nằm trong cây con trái của đoạn này chính là `count = RightVersion.left.count - LeftVersion.left.count`. Nếu $count \ge K$, đi sang cây con trái, ngược lại đi sang cây con phải và tìm phần tử nhỏ thứ $K - count$.

**Mã nguồn Java:**
```java
import java.util.*;

class PersistentSegmentTree {
    static class Node {
        int count;
        Node left, right;
        Node(int count, Node left, Node right) {
            this.count = count;
            this.left = left;
            this.right = right;
        }
    }
    
    Node[] versions;
    int[] sortedUniqueArr;
    Map<Integer, Integer> map;
    int n, M;
    
    public PersistentSegmentTree(int[] arr) {
        n = arr.length;
        versions = new Node[n + 1];
        
        sortedUniqueArr = Arrays.stream(arr).distinct().sorted().toArray();
        M = sortedUniqueArr.length;
        map = new HashMap<>();
        for (int i = 0; i < M; i++) map.put(sortedUniqueArr[i], i);
        
        versions[0] = build(0, M - 1);
        for (int i = 0; i < n; i++) {
            versions[i + 1] = update(versions[i], 0, M - 1, map.get(arr[i]));
        }
    }
    
    private Node build(int l, int r) {
        if (l == r) return new Node(0, null, null);
        int mid = l + (r - l) / 2;
        return new Node(0, build(l, mid), build(mid + 1, r));
    }
    
    private Node update(Node prev, int l, int r, int idx) {
        if (l == r) return new Node(prev.count + 1, null, null);
        int mid = l + (r - l) / 2;
        if (idx <= mid) {
            return new Node(prev.count + 1, update(prev.left, l, mid, idx), prev.right);
        } else {
            return new Node(prev.count + 1, prev.left, update(prev.right, mid + 1, r, idx));
        }
    }
    
    public int queryKth(int L, int R, int k) {
        int mappedIdx = queryKthNode(versions[L], versions[R + 1], 0, M - 1, k);
        return sortedUniqueArr[mappedIdx];
    }
    
    private int queryKthNode(Node leftVersion, Node rightVersion, int l, int r, int k) {
        if (l == r) return l;
        int mid = l + (r - l) / 2;
        int countLeft = rightVersion.left.count - leftVersion.left.count;
        if (countLeft >= k) {
            return queryKthNode(leftVersion.left, rightVersion.left, l, mid, k);
        } else {
            return queryKthNode(leftVersion.right, rightVersion.right, mid + 1, r, k - countLeft);
        }
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \log N)$, Query $O(\log N)$
- Không gian bộ nhớ: $O(N \log N)$

---

## 24. 2D Segment Tree (SPOJ MATSUM)
**Đề bài chi tiết:**
Cho một ma trận $2D$ kích thước $N \times N$. Cần thực hiện thao tác cập nhật giá trị của một ô $(r, c)$ thành $X$ và tính tổng các phần tử trong một hình chữ nhật con xác định bởi góc trái trên $(r1, c1)$ đến góc phải dưới $(r2, c2)$.

**Phân tích thuật toán:**
Sử dụng 2D Segment Tree (Cây phân đoạn 2 chiều). Cây bên ngoài (Outer Tree) quản lý các hàng của ma trận trên trục Y. Tại mỗi Node của Outer Tree sẽ được gắn một cây bên trong (Inner Tree) là một Segment Tree quản lý các cột trên trục X cho khoảng các hàng tương ứng. Mọi thao tác update hay query đều tốn chi phí của việc đi theo hai chiều $O(\log R \times \log C)$.

**Mã nguồn Java:**
```java
class SegmentTree2D {
    int[][] tree;
    int[][] mat;
    int R, C;

    public SegmentTree2D(int[][] mat) {
        if (mat.length == 0 || mat[0].length == 0) return;
        this.mat = mat;
        R = mat.length;
        C = mat[0].length;
        tree = new int[4 * R][4 * C];
        buildY(1, 0, R - 1, 1, 0, C - 1);
    }
    
    private void buildX(int nodeY, int startY, int endY, int nodeX, int startX, int endX) {
        if (startX == endX) {
            if (startY == endY) tree[nodeY][nodeX] = mat[startY][startX];
            else tree[nodeY][nodeX] = tree[2 * nodeY][nodeX] + tree[2 * nodeY + 1][nodeX];
        } else {
            int midX = startX + (endX - startX) / 2;
            buildX(nodeY, startY, endY, 2 * nodeX, startX, midX);
            buildX(nodeY, startY, endY, 2 * nodeX + 1, midX + 1, endX);
            tree[nodeY][nodeX] = tree[nodeY][2 * nodeX] + tree[nodeY][2 * nodeX + 1];
        }
    }

    private void buildY(int nodeY, int startY, int endY, int nodeX, int startX, int endX) {
        if (startY != endY) {
            int midY = startY + (endY - startY) / 2;
            buildY(2 * nodeY, startY, midY, nodeX, startX, endX);
            buildY(2 * nodeY + 1, midY + 1, endY, nodeX, startX, endX);
        }
        buildX(nodeY, startY, endY, nodeX, startX, endX);
    }

    public void update(int r, int c, int val) {
        updateY(1, 0, R - 1, r, c, val);
    }

    private void updateX(int nodeY, int startY, int endY, int nodeX, int startX, int endX, int c, int val) {
        if (startX == endX) {
            if (startY == endY) tree[nodeY][nodeX] = val;
            else tree[nodeY][nodeX] = tree[2 * nodeY][nodeX] + tree[2 * nodeY + 1][nodeX];
        } else {
            int midX = startX + (endX - startX) / 2;
            if (c <= midX) updateX(nodeY, startY, endY, 2 * nodeX, startX, midX, c, val);
            else updateX(nodeY, startY, endY, 2 * nodeX + 1, midX + 1, endX, c, val);
            tree[nodeY][nodeX] = tree[nodeY][2 * nodeX] + tree[nodeY][2 * nodeX + 1];
        }
    }

    private void updateY(int nodeY, int startY, int endY, int r, int c, int val) {
        if (startY != endY) {
            int midY = startY + (endY - startY) / 2;
            if (r <= midY) updateY(2 * nodeY, startY, midY, r, c, val);
            else updateY(2 * nodeY + 1, midY + 1, endY, r, c, val);
        }
        updateX(nodeY, startY, endY, 1, 0, C - 1, c, val);
    }

    public int query(int r1, int c1, int r2, int c2) {
        return queryY(1, 0, R - 1, r1, r2, c1, c2);
    }

    private int queryX(int nodeY, int nodeX, int startX, int endX, int c1, int c2) {
        if (c2 < startX || endX < c1) return 0;
        if (c1 <= startX && endX <= c2) return tree[nodeY][nodeX];
        int midX = startX + (endX - startX) / 2;
        return queryX(nodeY, 2 * nodeX, startX, midX, c1, c2) + queryX(nodeY, 2 * nodeX + 1, midX + 1, endX, c1, c2);
    }

    private int queryY(int nodeY, int startY, int endY, int r1, int r2, int c1, int c2) {
        if (r2 < startY || endY < r1) return 0;
        if (r1 <= startY && endY <= r2) return queryX(nodeY, 1, 0, C - 1, c1, c2);
        int midY = startY + (endY - startY) / 2;
        return queryY(2 * nodeY, startY, midY, r1, r2, c1, c2) + queryY(2 * nodeY + 1, midY + 1, endY, r1, r2, c1, c2);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(R \times C)$, Update/Query $O(\log R \times \log C)$
- Không gian bộ nhớ: $O(R \times C)$

---

## 25. LIS in Segment Tree (LIS on Array of Sequences)
**Đề bài chi tiết:**
Cho một tập hợp các số. Bạn được chèn liên tục các phần tử vào mảng và cần tìm độ dài dãy con tăng dài nhất (LIS) kết thúc bằng giá trị $X$ bất cứ lúc nào.

**Phân tích thuật toán:**
Thay vì xây Segment Tree trên các chỉ số mảng thông thường, ta xây Segment Tree quản lý trục giá trị (từ 0 đến `maxVal`). Mỗi node lưu chiều dài LIS lớn nhất kết thúc bằng một số trong đoạn giá trị mà node đó quản lý. Khi thêm giá trị $X$, ta query tìm giá trị max trên khoảng $[0, X-1]$, giả sử được $L$. Sau đó ta update (Point Update) giá trị $L+1$ cho tọa độ $X$.

**Mã nguồn Java:**
```java
class LIS_SegmentTree {
    int[] tree;
    int maxVal;

    public LIS_SegmentTree(int maxVal) {
        this.maxVal = maxVal;
        tree = new int[4 * maxVal];
    }
    
    public void insert(int val) {
        int maxPrefixLIS = query(1, 0, maxVal, 0, val - 1);
        update(1, 0, maxVal, val, maxPrefixLIS + 1);
    }
    
    private void update(int node, int start, int end, int idx, int length) {
        if (start == end) {
            tree[node] = Math.max(tree[node], length);
            return;
        }
        int mid = start + (end - start) / 2;
        if (idx <= mid) update(2 * node, start, mid, idx, length);
        else update(2 * node + 1, mid + 1, end, idx, length);
        tree[node] = Math.max(tree[2 * node], tree[2 * node + 1]);
    }

    private int query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        int mid = start + (end - start) / 2;
        return Math.max(query(2 * node, start, mid, l, r), query(2 * node + 1, mid + 1, end, l, r));
    }
    
    public int getLIS() {
        return tree[1];
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(\text{max\_val})$, Update/Query $O(\log (\text{max\_val}))$
- Không gian bộ nhớ: $O(\text{max\_val})$

---

## 26. Nearest Smaller to Left with Updates
**Đề bài chi tiết:**
Cho một mảng. Có các thao tác Point Update và truy vấn tìm phần tử gần nhất nằm bên trái của chỉ số $i$ (trong đoạn $[L, R]$ với $R = i$) mà có giá trị nhỏ hơn $X$.

**Phân tích thuật toán:**
Sử dụng kỹ thuật "Đi bộ trên cây" (Walking on Segment Tree). Segment Tree duy trì giá trị Minimum (min) trong từng đoạn. Để tìm phần tử gần nhất bên trái có giá trị nhỏ hơn $X$, ta bắt đầu từ gốc với vùng tìm kiếm là $[0, i]$. Nếu giá trị `min` của node hiện tại $\ge X$, chắc chắn không có phần tử thỏa mãn, bỏ qua nhánh này. Nếu không, ta ưu tiên duyệt xuống nhánh phải trước (vì cần tìm "gần nhất" với bên trái chỉ số $i$, nghĩa là cần vị trí $idx$ lớn nhất). Nếu nhánh phải không tìm được, mới duyệt xuống nhánh trái.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class NearestSmallerLeft {
    int[] tree;
    int n;

    public NearestSmallerLeft(int n) {
        this.n = n;
        tree = new int[4 * n];
        Arrays.fill(tree, Integer.MAX_VALUE);
    }
    
    public void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
            return;
        }
        int mid = start + (end - start) / 2;
        if (idx <= mid) update(2 * node, start, mid, idx, val);
        else update(2 * node + 1, mid + 1, end, idx, val);
        tree[node] = Math.min(tree[2 * node], tree[2 * node + 1]);
    }
    
    public int findNearestSmallerToLeft(int node, int start, int end, int l, int r, int x) {
        if (r < start || end < l || tree[node] >= x) return -1;
        if (start == end) return start;
        
        int mid = start + (end - start) / 2;
        // Đi sang cây con phải trước để tìm index lớn nhất
        int rightRes = findNearestSmallerToLeft(2 * node + 1, mid + 1, end, l, r, x);
        if (rightRes != -1) return rightRes;
        
        // Nếu không có, mới tìm ở nhánh trái
        return findNearestSmallerToLeft(2 * node, start, mid, l, r, x);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 27. Number of Inversions After Swaps
**Đề bài chi tiết:**
Tính số lượng nghịch thế (Inversions) trong một mảng sau khi thực hiện thao tác hoán đổi (swap) giá trị của hai phần tử ở chỉ số $L$ và $R$.

**Phân tích thuật toán:**
Để giải trọn vẹn trong $O(\log^2 N)$ người ta thường dùng Segment Tree of BITs hoặc 2D Fenwick Tree. Tuy nhiên, một phương pháp tiếp cận trực quan hơn là dùng BIT tính Inversion gốc. Khi đổi chỗ $a[L]$ và $a[R]$ (giả sử $L < R$), các phần tử ngoài khoảng $(L, R)$ không bị ảnh hưởng. Ta duyệt qua tất cả phần tử trong đoạn $(L, R)$ để cập nhật số lượng nghịch thế trực tiếp trong thời gian $O(|R - L|)$. Thích hợp cho mảng nhỏ hoặc truy vấn đổi chỗ gần. 

**Mã nguồn Java (Kỹ thuật Sweep $O(|R-L|)$ với Inversion gốc):**
```java
class InversionsAfterSwaps {
    int[] bit;
    int n;
    int[] arr;

    public InversionsAfterSwaps(int[] arr) {
        this.n = arr.length;
        this.arr = new int[n + 1];
        System.arraycopy(arr, 0, this.arr, 1, n);
        bit = new int[n + 1];
    }

    private void add(int idx, int val) {
        for (; idx <= n; idx += idx & -idx) bit[idx] += val;
    }

    private int query(int idx) {
        int sum = 0;
        for (; idx > 0; idx -= idx & -idx) sum += bit[idx];
        return sum;
    }

    public long getInitialInversions() {
        long inv = 0;
        for (int i = 1; i <= n; i++) {
            inv += query(n) - query(arr[i]);
            add(arr[i], 1);
        }
        return inv;
    }

    public long updateSwap(int l, int r, long currentInversions) {
        if (l == r) return currentInversions;
        if (l > r) { int temp = l; l = r; r = temp; }
        
        if (arr[l] < arr[r]) currentInversions++;
        else if (arr[l] > arr[r]) currentInversions--;
        
        for (int i = l + 1; i < r; i++) {
            if (arr[l] > arr[i]) currentInversions--; 
            if (arr[l] < arr[i]) currentInversions++; 
            
            if (arr[r] < arr[i]) currentInversions--; 
            if (arr[r] > arr[i]) currentInversions++; 
        }
        
        int temp = arr[l];
        arr[l] = arr[r];
        arr[r] = temp;
        
        return currentInversions;
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \log N)$, Swap $O(|R - L|)$
- Không gian bộ nhớ: $O(N)$

---

## 28. Find Zeroes in Array
**Đề bài chi tiết:**
Cho một mảng các số nguyên. Có 2 loại truy vấn: tính số lượng số 0 trong đoạn $[L, R]$, và gán giá trị của tất cả các phần tử trong đoạn $[L, R]$ bằng một hằng số $X$ (Range Set Update).

**Phân tích thuật toán:**
Dùng Segment Tree với Lazy Propagation để lưu trữ số lượng số 0. Khi gọi thao tác gán `Range Set` một mảng thành $X$, nếu $X == 0$, ta cập nhật số lượng số 0 trong cây bằng với độ dài của đoạn (`end - start + 1`). Nếu $X \neq 0$, số lượng số 0 bị đặt về 0.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class FindZeroesTree {
    int[] tree;
    int[] lazy;
    int n;

    public FindZeroesTree(int n) {
        this.n = n;
        tree = new int[4 * n];
        lazy = new int[4 * n];
        Arrays.fill(lazy, -1); // -1 nghĩa là không có update đang chờ
    }
    
    private void pushDown(int node, int start, int end) {
        if (lazy[node] != -1) {
            int mid = start + (end - start) / 2;
            lazy[2 * node] = lazy[node];
            tree[2 * node] = lazy[node] == 0 ? (mid - start + 1) : 0;
            
            lazy[2 * node + 1] = lazy[node];
            tree[2 * node + 1] = lazy[node] == 0 ? (end - mid) : 0;
            
            lazy[node] = -1;
        }
    }

    public void setRange(int node, int start, int end, int l, int r, int val) {
        if (r < start || end < l) return;
        if (l <= start && end <= r) {
            lazy[node] = val;
            tree[node] = val == 0 ? (end - start + 1) : 0;
            return;
        }
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        setRange(2 * node, start, mid, l, r, val);
        setRange(2 * node + 1, mid + 1, end, l, r, val);
        tree[node] = tree[2 * node] + tree[2 * node + 1];
    }

    public int queryZeroes(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;
        if (l <= start && end <= r) return tree[node];
        pushDown(node, start, end);
        int mid = start + (end - start) / 2;
        return queryZeroes(2 * node, start, mid, l, r) + queryZeroes(2 * node + 1, mid + 1, end, l, r);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 29. Longest Contiguous Ones
**Đề bài chi tiết:**
Cho mảng nhị phân. Các thao tác bao gồm cập nhật điểm (Toggle) biến $0$ thành $1$ và $1$ thành $0$, đồng thời tìm chiều dài dãy con liên tiếp toàn số 1 lớn nhất trong mảng.

**Phân tích thuật toán:**
Tương tự bài toán mảng con có tổng lớn nhất, tại mỗi Node, ta cần duy trì 3 giá trị: `pref` (chuỗi số 1 dài nhất bắt đầu từ biên trái), `suff` (chuỗi số 1 dài nhất kết thúc ở biên phải), và `maxOnes` (chuỗi 1 dài nhất bất kỳ nằm trong đoạn). Khi kết hợp 2 Node con, `maxOnes` lớn nhất sẽ là giá trị lớn nhất trong 3 số: `left.maxOnes`, `right.maxOnes`, hoặc kết nối giữa 2 cây `left.suff + right.pref`.

**Mã nguồn Java:**
```java
class LongestOnesTree {
    static class Node {
        int pref, suff, maxOnes, len;
        Node(int p, int s, int m, int l) { pref = p; suff = s; maxOnes = m; len = l; }
        Node() {}
    }

    Node[] tree;
    int[] arr;
    int n;

    public LongestOnesTree(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        tree = new Node[4 * n];
        build(1, 0, n - 1);
    }

    private Node merge(Node left, Node right) {
        Node res = new Node();
        res.len = left.len + right.len;
        res.pref = left.pref == left.len ? left.len + right.pref : left.pref;
        res.suff = right.suff == right.len ? right.len + left.suff : right.suff;
        res.maxOnes = Math.max(Math.max(left.maxOnes, right.maxOnes), left.suff + right.pref);
        return res;
    }

    private void build(int node, int start, int end) {
        if (start == end) {
            int val = arr[start];
            tree[node] = new Node(val, val, val, 1);
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
        }
    }
    
    public void update(int node, int start, int end, int idx) {
        if (start == end) {
            int val = 1 - tree[node].maxOnes; // toggle 0 <-> 1
            tree[node] = new Node(val, val, val, 1);
            return;
        }
        int mid = start + (end - start) / 2;
        if (idx <= mid) update(2 * node, start, mid, idx);
        else update(2 * node + 1, mid + 1, end, idx);
        tree[node] = merge(tree[2 * node], tree[2 * node + 1]);
    }

    public int queryMaxOnes() {
        return tree[1].maxOnes;
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N)$, Update/Query $O(\log N)$
- Không gian bộ nhớ: $O(N)$

---

## 30. Distinct Elements in Range (SPOJ DQUERY)
**Đề bài chi tiết:**
Cho một mảng $N$ phần tử. Có $Q$ truy vấn trực tuyến, yêu cầu đếm số lượng các phần tử phân biệt (distinct elements) nằm trong đoạn $[L, R]$.

**Phân tích thuật toán:**
Dùng Persistent Segment Tree (Cây phân đoạn bền vững) để lưu trạng thái của mảng. Khi duyệt mảng từ trái sang phải để xây dựng version mới của cây, ta chỉ giữ lại vị trí xuất hiện cuối cùng của một phần tử bằng cách xóa 1 ở vị trí cũ (update -1) và đánh dấu 1 tại vị trí mới (update +1). Cây version thứ $R$ sẽ đại diện cho mảng từ $0$ đến $R$. Tổng các phần tử trong khoảng $[L, R]$ tại version $R$ chính là số lượng phần tử phân biệt.

**Mã nguồn Java:**
```java
import java.util.*;

class PersistentSegmentTreeDQuery {
    static class Node {
        int sum;
        Node left, right;
        Node(int sum, Node left, Node right) {
            this.sum = sum;
            this.left = left;
            this.right = right;
        }
    }
    
    Node[] versions;
    int[] arr;
    int n;
    
    public PersistentSegmentTreeDQuery(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        versions = new Node[n + 1];
        versions[0] = build(0, n - 1);
        
        Map<Integer, Integer> lastVisit = new HashMap<>();
        for (int i = 0; i < n; i++) {
            Node current = versions[i];
            if (lastVisit.containsKey(arr[i])) {
                current = update(current, 0, n - 1, lastVisit.get(arr[i]), -1);
            }
            versions[i + 1] = update(current, 0, n - 1, i, 1);
            lastVisit.put(arr[i], i);
        }
    }
    
    private Node build(int l, int r) {
        if (l == r) return new Node(0, null, null);
        int mid = l + (r - l) / 2;
        return new Node(0, build(l, mid), build(mid + 1, r));
    }
    
    private Node update(Node prev, int l, int r, int idx, int val) {
        if (l == r) return new Node(prev.sum + val, null, null);
        int mid = l + (r - l) / 2;
        if (idx <= mid) {
            return new Node(prev.sum + val, update(prev.left, l, mid, idx, val), prev.right);
        } else {
            return new Node(prev.sum + val, prev.left, update(prev.right, mid + 1, r, idx, val));
        }
    }
    
    public int query(int L, int R) {
        return queryNode(versions[R + 1], 0, n - 1, L, R); // L and R are 0-indexed
    }
    
    private int queryNode(Node node, int start, int end, int l, int r) {
        if (r < start || end < l || node == null) return 0;
        if (l <= start && end <= r) return node.sum;
        int mid = start + (end - start) / 2;
        return queryNode(node.left, start, mid, l, r) + queryNode(node.right, mid + 1, end, l, r);
    }
}
```
**Độ phức tạp:**
- Thời gian: Khởi tạo $O(N \log N)$, Query $O(\log N)$
- Không gian bộ nhớ: $O(N \log N)$

