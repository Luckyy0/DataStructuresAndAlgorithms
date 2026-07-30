# Segment Tree (Cây Phân Đoạn)

## 1. Giới thiệu khái niệm (Introduction)
Segment Tree (Cây phân đoạn) là một cấu trúc dữ liệu cây (thường là cây nhị phân) được sử dụng để lưu trữ thông tin về các đoạn (segments) hoặc khoảng (intervals). Nó đặc biệt hiệu quả cho việc giải quyết các bài toán yêu cầu **Range Query** (Truy vấn trên một khoảng) và **Point Update** (Cập nhật một phần tử) hoặc **Range Update** (Cập nhật trên một khoảng, sử dụng Lazy Propagation) trên một mảng. 
Một Segment Tree cho mảng kích thước N thường được biểu diễn bằng một mảng có kích thước 4*N.

## 2. Ứng dụng thực tế (Real-world Applications)
- **Computer Graphics**: Xử lý các đối tượng đồ họa trong các khu vực hiển thị.
- **Database Systems**: Tối ưu hóa các truy vấn thống kê trên các khoảng dữ liệu.
- **GIS (Geographic Information Systems)**: Quản lý và truy vấn không gian.
- **Stock Market**: Tìm giá cao nhất, thấp nhất trong một khoảng thời gian cụ thể (Range Min/Max Query - RMQ).
- **Network Routing**: Cập nhật băng thông hoặc tìm nghẽn mạng trên các chặng.

## 3. Hoạt động (How it works)
Segment Tree hoạt động dựa trên nguyên lý Divide and Conquer (Chia để trị).
Mỗi Node trong cây đại diện cho một khoảng `[L, R]`.
- **Root Node**: Đại diện cho toàn bộ mảng `[0, N-1]`.
- **Leaf Nodes**: Đại diện cho từng phần tử đơn lẻ `[i, i]`.
- **Internal Nodes**: Đại diện cho khoảng `[L, R]`, có hai con là `[L, mid]` và `[mid+1, R]` với `mid = (L + R) / 2`.
Giá trị tại mỗi Node là kết quả của bài toán trên khoảng đó (ví dụ: tổng, min, max).

## 4. Đặc điểm cấu trúc dữ liệu (Data Structure Characteristics)
- **Cấu trúc**: Cây nhị phân đầy đủ (Full Binary Tree) được biểu diễn bằng mảng.
- **Kích thước**: Cần mảng kích thước khoảng 4*N để lưu trữ Segment Tree cho mảng kích thước N.
- **Chiều cao cây**: $O(\log N)$.
- **Tính chất**: Có thể kết hợp hai nửa đoạn trái phải để tạo ra giá trị của đoạn cha.

## 5. Độ phức tạp thời gian & không gian (Time & Space Complexity)
- **Build (Xây dựng)**: $O(N)$. Mỗi phần tử của cây tương ứng với một Node, và có khoảng $2N$ Node, mỗi Node mất $O(1)$ để tính.
- **Point Update (Cập nhật 1 điểm)**: $O(\log N)$. Cần đi từ root xuống leaf, độ sâu là $\log N$.
- **Range Query (Truy vấn khoảng)**: $O(\log N)$. Bằng việc gom nhóm các node, ta duyệt tối đa $4 \log N$ nodes.
- **Space Complexity**: $O(N)$. Mảng cần cấp phát có kích thước $4N$.

## 6. So sánh với các cấu trúc dữ liệu khác (Comparison)
| Cấu trúc dữ liệu | Build | Range Query | Point Update | Ghi chú |
| :--- | :--- | :--- | :--- | :--- |
| **Array** | $O(N)$ | $O(N)$ | $O(1)$ | Truy vấn chậm |
| **Prefix Sum Array** | $O(N)$ | $O(1)$ | $O(N)$ | Cập nhật chậm |
| **Fenwick Tree (BIT)** | $O(N)$ | $O(\log N)$ | $O(\log N)$ | Chỉ tốt cho tổng/phép toán khả nghịch, code ngắn hơn |
| **Segment Tree** | $O(N)$ | $O(\log N)$ | $O(\log N)$ | Hỗ trợ nhiều phép toán linh hoạt hơn (min, max, gcd) |

## 7. Các thao tác cơ bản (Basic Operations)
- **Build**: Xây dựng cây từ dưới lên (hoặc đệ quy chia đôi mảng).
- **Update**: Đi từ Root, tìm đường xuống Leaf chứa index cần update, cập nhật giá trị Leaf, sau đó trên đường quay lên cập nhật lại các Node cha.
- **Query**: Trả về giá trị của đoạn `[L, R]`. Nếu khoảng Node hiện tại nằm hoàn toàn trong `[L, R]`, trả về giá trị Node. Nếu rời nhau, trả về phần tử trung tính (ví dụ 0 cho tổng, $\infty$ cho min). Nếu giao nhau, gọi đệ quy xuống 2 con và gộp kết quả.

## 8. Cài đặt chi tiết (Detailed Implementation)
Cài đặt cho bài toán **Range Sum Query** và **Point Update**.
```java
class SegmentTree {
    private int[] tree;
    private int n;

    public SegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[4 * n];
        build(arr, 1, 0, n - 1);
    }

    private void build(int[] arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            int leftChild = 2 * node;
            int rightChild = 2 * node + 1;
            build(arr, leftChild, start, mid);
            build(arr, rightChild, mid + 1, end);
            tree[node] = tree[leftChild] + tree[rightChild];
        }
    }

    public void update(int idx, int val) {
        update(1, 0, n - 1, idx, val);
    }

    private void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
        } else {
            int mid = start + (end - start) / 2;
            int leftChild = 2 * node;
            int rightChild = 2 * node + 1;
            if (idx <= mid) {
                update(leftChild, start, mid, idx, val);
            } else {
                update(rightChild, mid + 1, end, idx, val);
            }
            tree[node] = tree[leftChild] + tree[rightChild];
        }
    }

    public int query(int L, int R) {
        return query(1, 0, n - 1, L, R);
    }

    private int query(int node, int start, int end, int L, int R) {
        if (R < start || end < L) return 0; // Out of range
        if (L <= start && end <= R) return tree[node]; // Completely inside
        
        int mid = start + (end - start) / 2;
        int leftChild = 2 * node;
        int rightChild = 2 * node + 1;
        int sumLeft = query(leftChild, start, mid, L, R);
        int sumRight = query(rightChild, mid + 1, end, L, R);
        
        return sumLeft + sumRight;
    }
}
```

## 9. Biến thể (Variants)
- **Range Min/Max Query (RMQ)**: Cây lưu giá trị Min/Max thay vì Sum.
- **Lazy Propagation**: Hỗ trợ Range Update (Cập nhật 1 mảng các giá trị) trong $O(\log N)$.
- **Dynamic Segment Tree**: Không tạo toàn bộ cây ban đầu, chỉ tạo các Node khi truy vấn/cập nhật tới nó, tiết kiệm bộ nhớ cho phạm vi rất lớn (VD: $1 \dots 10^9$).
- **Persistent Segment Tree**: Giữ lại các phiên bản cũ của Segment Tree sau mỗi lần update.
- **2D Segment Tree**: Dùng cho ma trận.

## 10. Mẫu code / Template chung (Code Templates)
Template chuẩn dùng đệ quy (Recursive 1-based indexing) đã trình bày ở trên. Một template mảng 0-based indexing iterative (Bottom-up) ngắn gọn hơn nhưng khó áp dụng Lazy:
```java
class IterativeSegmentTree {
    int n;
    int[] tree;

    public IterativeSegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[2 * n];
        for (int i = 0; i < n; i++) tree[n + i] = arr[i];
        for (int i = n - 1; i > 0; --i) tree[i] = tree[i * 2] + tree[i * 2 + 1];
    }

    public void update(int p, int value) {
        for (tree[p += n] = value; p > 1; p >>= 1)
            tree[p >> 1] = tree[p] + tree[p ^ 1];
    }

    public int query(int l, int r) {
        int res = 0;
        for (l += n, r += n + 1; l < r; l >>= 1, r >>= 1) {
            if ((l & 1) > 0) res += tree[l++];
            if ((r & 1) > 0) res += tree[--r];
        }
        return res;
    }
}
```

## 11. Các dạng bài toán phổ biến (Common Problem Types)
1. **Range Sum / Min / Max Query**: Cơ bản nhất.
2. **Finding the k-th zero / k-th element**: Áp dụng tìm kiếm nhị phân trên Segment Tree.
3. **Number of Inversions / Elements smaller/greater in a range**: Kết hợp nén tọa độ (Coordinate Compression) và Segment Tree.
4. **Range Updates**: Sử dụng Lazy Propagation.
5. **Giao các đoạn thẳng, độ dài phủ của các đoạn thẳng (Sweep Line + Segment Tree)**.

## 12. Phân tích độ phức tạp nâng cao (Advanced Complexity Analysis)
- Dù cấp phát $4N$ nhưng số node thực tế chỉ là $2N - 1$.
- Truy vấn giao nhau tối đa $2 \log N$ nodes tại mỗi level của cây. Do đó tổng số operations của mỗi query được guarantee bounded bởi $4 \log N$.
- Dynamic Segment Tree cho $N$ operations có Time là $O(Q \log (\max R))$ và Space là $O(Q \log (\max R))$, phù hợp cho sparse ranges.

## 13. Phương pháp tối ưu (Optimization Techniques)
- **Bitwise shift**: Dùng `x << 1` thay cho `2 * x`, `x >> 1` thay cho `x / 2` để tăng nhẹ tốc độ.
- **Pass Arrays/References**: Thay vì OOP object quá nhiều, hãy dùng mảng phẳng 1 chiều tĩnh.
- **Iterative vs Recursive**: Iterative Segment Tree nhanh hơn recursive tree rất nhiều về hằng số thời gian.

## 14. 20 Câu hỏi Phỏng vấn (Interview Questions)
1. Segment Tree là gì? Sự khác biệt so với Fenwick Tree?
2. Tại sao Segment Tree lại cần mảng kích thước 4N?
3. Giải thích Lazy Propagation trong Segment Tree.
4. Làm thế nào để tìm Min/Max trong một khoảng thay vì tính tổng?
5. Nếu mảng đầu vào có kích thước $10^9$, bạn có thể dùng Segment Tree không? Tại sao? (Trả lời: Dynamic Segment Tree).
6. Time complexity của Build, Update, Query trong Segment Tree là bao nhiêu?
7. Bạn có thể cài đặt Segment Tree không đệ quy (Iterative) không?
8. Persistent Segment Tree là gì và dùng trong trường hợp nào?
9. Làm thế nào để giải bài toán đếm số lượng Inversions bằng Segment Tree?
10. So sánh prefix-sum array và Segment Tree.
11. Thay vì Point Update, bài toán yêu cầu Range Add. Bạn làm thế nào?
12. Có thể update đoạn `[L, R]` bằng cách gán giá trị x cho mọi phần tử trong $O(\log N)$ không? (Trả lời: Dùng Lazy).
13. Nếu node chỉ lưu số lẻ trong đoạn, ta merge thế nào?
14. Áp dụng Segment Tree cho bài toán RMQ (Range Minimum Query), ngoài nó ra còn cấu trúc nào khác? (Sparse Table).
15. Segment Tree có thể lưu được GCD (Ước chung lớn nhất) không?
16. Nếu mảng có phép toán không có tính giao hoán (ví dụ: Matrix multiplication), Segment Tree có hoạt động được không? (Trả lời: Được, nhưng phải ghép trái và phải đúng thứ tự).
17. Dấu hiệu nào của một bài toán chỉ ra rằng ta nên dùng Segment Tree?
18. Cấu trúc của mỗi Node trong Segment Tree có thể là một Map hay một mảng khác không? (Merge Sort Tree).
19. Làm sao để tìm phần tử lớn thứ K trong mảng bằng Segment Tree?
20. Sweep Line algorithm kết hợp với Segment Tree như thế nào trong bài toán diện tích hình chữ nhật giao nhau?

## 15. 20 Lỗi phổ biến (Common Bugs)
1. **Quên `4*N`**: Khởi tạo mảng tree có kích thước `N` hoặc `2*N` dẫn đến IndexOutOfBounds.
2. **Lỗi off-by-one**: Truyền `n` thay vì `n-1` cho index cuối cùng khi Build.
3. **Quên gọi đệ quy với `leftChild` / `rightChild`**.
4. **Merge sai**: Không cộng/ghép đúng giá trị trả về từ 2 cây con trong bước Query.
5. **Base case sai trong Query**: Out of bound check nhầm `if (R < start || end < L) return 0;` (nếu là Min thì phải trả về `Integer.MAX_VALUE`).
6. **Cập nhật giá trị lá sai**: Trong Point update, nhầm lẫn gán bằng thay vì cộng dồn (tùy yêu cầu đề bài).
7. **Lỗi tính `mid`**: `(start + end) / 2` có thể tràn số với `start`, `end` lớn. Nên dùng `start + (end - start) / 2`.
8. **Gọi trùng lắp chỉ số Node**: Nhầm lẫn giữa index của mảng (`idx`) và index của Node (`node`).
9. **Lazy Propagation - quên đẩy giá trị**: Quên `pushDown(node)` trước khi gọi đệ quy xuống con.
10. **Lazy Propagation - cộng dồn sai**: Khi `range add`, update node cha phải nhân chiều dài đoạn `tree[node] += val * (end - start + 1)`.
11. **Giao của các đoạn (Intersection bounds)**: Nhầm lẫn `if (L <= start && end <= R)` với `if (start <= L && R <= end)`.
12. **Cập nhật Node cha quá sớm**: Trong Update, cập nhật `tree[node]` trước khi 2 cây con chạy xong.
13. **Không xử lý OutOfBounds của Array**: Khi `L > R`.
14. **Khởi tạo sai giá trị Identity**: Identity cho phép nhân là 1, phép OR là 0, phép AND là v.v.
15. **Không nén tọa độ**: Khi các giá trị của phần tử rất lớn (ví dụ $10^9$) mà muốn xây dựng Segment Tree theo giá trị, dễ bị MLE nếu không dùng mảng nén/Dynamic ST.
16. **Dynamic ST - Null Pointer**: Quên khởi tạo Node con khi node chưa tồn tại.
17. **Nhầm lẫn biến toàn cục và cục bộ**: Sử dụng `mid` toàn cục thay vì trong function.
18. **Iterative ST - Quên +n hoặc +1**: Các bounds thao tác trên mảng $2N$ yêu cầu điều chỉnh kĩ.
19. **Merge Sort Tree - memory limit**: Nếu mỗi Node là một List, cần cẩn thận vì tạo ra $O(N \log N)$ space.
20. **Không kiểm tra Node lá hợp lệ**: Query nhảy vào node không hợp lệ khi $start > end$.

## 16. 30 Edge Cases (Trường hợp góc)
1. `N = 1` (Mảng chỉ có 1 phần tử).
2. `L = R` (Query trên một điểm).
3. `L > L_max`, `R < R_min` (Query ngoài phạm vi mảng, nên throw error hoặc return default).
4. `L > R` (Query vô lý, trả về 0 hoặc default).
5. Truy vấn bao phủ toàn bộ mảng `[0, N-1]`.
6. Cập nhật nhiều lần trên cùng một vị trí.
7. Mảng chứa toàn số 0.
8. Mảng chứa toàn số âm (Cẩn thận khi Identity Min/Max/Sum).
9. Mảng có kích thước rất lớn ($N = 10^5$), cây độ sâu sâu có nguy cơ StackOverflow? (Thực tế $\log_2(10^5) \approx 17$, không sao).
10. Mảng có $N$ lẻ, $N$ chẵn.
11. $N$ không phải lũy thừa của 2. Segment Tree vẫn chạy đúng nhưng cây không hoàn hảo, một số nhánh null.
12. Các giá trị rất lớn cộng lại gây tràn `int`, phải dùng `long` cho `tree` array.
13. Range Update với số lượng update rất lớn (Lazy cần hoạt động tốt).
14. Range Query với `L = 0, R = 0`.
15. Truy vấn Range Min với mảng toàn `Integer.MAX_VALUE`.
16. Range Update giá trị âm và dương xen kẽ.
17. Phép Update là "Gán bằng" (`set`) thay vì "Cộng thêm" (`add`). Lazy cần phân biệt rõ cờ "đã update" với giá trị update là 0.
18. Mảng rỗng (`N = 0`), chương trình cần chặn ngay từ đầu.
19. Index Update ngoài phạm vi `[0, N-1]`.
20. Dynamic ST truy vấn một node chưa từng được update.
21. Truy vấn liên tiếp không có Update nào chen giữa.
22. Update rất nhiều lần ở cùng 1 half của cây (vd: chỉ update index 0).
23. Giao của Query và Cây không có (Out of Range branch trả về sớm).
24. Dynamic ST có node $L$ rất bé (ví dụ $-10^9$) và $R$ rất lớn ($10^9$).
25. Mảng đầu vào chưa được sort (trong bài toán Count Inversions cần cẩn thận).
26. Cập nhật bằng 0 trong Range Sum.
27. Đỉnh gốc root có index `node = 1` vs `node = 0` (Code 0-based root cần con là $2*i+1, 2*i+2$; code 1-based cần $2*i, 2*i+1$).
28. Quá trình Merge không giao hoán (Non-commutative).
29. Giá trị min có thể bằng 0. Nếu Identity = 0 thì sai bét.
30. Lazy Tree với phép Update `XOR`: Tính chất tự triệt tiêu khi update 2 lần chẵn.

## 17. Checklist Kiểm tra (Review Checklist)
- [ ] Mảng `tree` có được cấp phát `4*N` không?
- [ ] Chỉ số bắt đầu `node` là 1 hay 0? (Phải nhất quán với con trái/phải).
- [ ] Phép toán tính `mid = start + (end - start) / 2` chống tràn?
- [ ] Hàm `query` có xử lý trường hợp `out of bound` đúng Identity Value không?
- [ ] Range update có sử dụng Lazy (nếu bài toán yêu cầu) không? Chặn MLE/TLE.

## 18. Bài tập thực hành (Practice Problems)
(Xem chi tiết file Problems). Các bài phổ biến trên LeetCode/Codeforces:
- 307. Range Sum Query - Mutable
- 315. Count of Smaller Numbers After Self
- 493. Reverse Pairs
- 699. Falling Squares

## 19. Tài liệu tham khảo (References & Resources)
- CP-Algorithms: Segment Tree
- HackerEarth: Segment Trees
- GeeksforGeeks: Segment Tree Data Structure

## 20. Câu hỏi trắc nghiệm (Quizzes)
**Q1:** Mảng $tree$ của Segment tree cho mảng $N$ phần tử cần kích thước bao nhiêu?
A. N
B. 2*N
C. 4*N
D. $N^2$
**Đáp án:** C

**Q2:** Độ phức tạp cho 1 thao tác Range Query trên Segment Tree là?
A. O(1)
B. O(N)
C. O(log N)
D. O(N log N)
**Đáp án:** C

## 21. Thuật ngữ tiếng Anh (English Glossary)
- **Segment Tree**: Cây phân đoạn
- **Range Query**: Truy vấn trên một khoảng
- **Point Update**: Cập nhật một điểm
- **Lazy Propagation**: Cập nhật trễ / lan truyền trễ
- **Leaf Node**: Nút lá
- **Internal Node**: Nút trong
- **Identity Value**: Phần tử trung tính (0 cho tổng, INF cho Min)

## 22. Tóm tắt bài học (Cheat Sheet)
- Cấu trúc: Dùng mảng `4*N`.
- Build: Đệ quy chia `start, end`, `mid = (start+end)/2`, $O(N)$.
- Query: Nếu đoạn query nằm ngoài trả về Identity; nếu đoạn hiện tại nằm hoàn toàn trong đoạn query trả về `tree[node]`; còn lại đệ quy xuống con và gộp.
- Update: Đi tìm Node lá tương ứng, gán giá trị mới, quay lên update lại tổ tiên.
- Luôn kiểm tra `L, R` hợp lệ.

## 23. Mẹo phỏng vấn (Interview Tips)
- Đừng vội code Segment Tree nếu Fenwick Tree (BIT) hoặc Prefix Sum có thể giải bài đó (vd bài chỉ update, không cần prefix dynamic thì dùng BIT).
- Nếu được phép, hãy dùng Iterative Segment Tree vì nó chỉ tốn vài dòng code, ít bị bug và thể hiện bạn rất am hiểu.
- Giải thích rõ cho Interviewer vì sao cần `4*N`. (Bởi vì độ cao cây là $\lceil \log_2 N \rceil$, số node tối đa ở lá cuối cùng và lá kế cuối).

## 24. Góc kinh nghiệm (Developer's Corner)
Trong môi trường thực tế production, hiếm khi phải tự code Segment Tree tay, các database như PostgreSQL đã tối ưu range queries bằng các Tree-based indices (B-Trees) cực tốt. Tuy nhiên trong đồ hoạ (Graphics) thì bounding volume hierarchies khá tương đồng với lý thuyết của Segment Tree. Hãy hiểu cách nó chia nhỏ không gian ra làm 2 (Divide & Conquer), đó là mindset giải quyết mọi vấn đề data lớn.
