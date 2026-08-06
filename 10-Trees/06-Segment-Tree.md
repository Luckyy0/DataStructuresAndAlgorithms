# Segment Tree (Cây Phân Đoạn)

## 1. Tổng quan (Overview)
Segment Tree là một cấu trúc dữ liệu dạng cây nâng cao (advanced tree) được sử dụng rộng rãi để giải quyết các bài toán liên quan đến truy vấn trên một đoạn (Range Queries) và cập nhật phần tử hoặc đoạn (Point/Range Updates). Các bài toán phổ biến là tìm tổng (Sum), tìm giá trị nhỏ nhất (Min), lớn nhất (Max) trong một mảng con liên tiếp.

## 2. Lịch sử (History)
Ý tưởng về Segment Tree xuất hiện từ nhu cầu xử lý các bài toán hình học tính toán học và cơ sở dữ liệu không gian, sau đó lan rộng trong cộng đồng Competitive Programming để tối ưu thời gian phản hồi cho các truy vấn Range Query lặp đi lặp lại trên một mảng thay đổi.

## 3. Cấu trúc và Biểu diễn (Structure and Representation)
Segment Tree thường được biểu diễn dưới dạng một mảng (Array representation) mô phỏng một cây nhị phân đầy đủ (full binary tree) hoặc cây nhị phân hoàn chỉnh.
- Node gốc (root) ở chỉ số 1 (hoặc 0) biểu thị toàn bộ mảng `[0, N-1]`.
- Nếu một node ở chỉ số `i` biểu diễn đoạn `[L, R]`, con trái ở chỉ số `2*i` (hoặc `2*i+1`) biểu diễn `[L, mid]`, con phải ở chỉ số `2*i+1` (hoặc `2*i+2`) biểu diễn `[mid+1, R]`.
- Chiều cao của cây là $O(\log N)$.
- Kích thước mảng lưu trữ thường là $4N$ để đảm bảo đủ chỗ cho tất cả các node.

## 4. Các thuật toán cốt lõi (Core Algorithms)
### Build (Xây dựng cây)
Xây dựng từ dưới lên (Bottom-up) bằng đệ quy. Chia đôi đoạn đang xét đến khi L == R.

### Query (Truy vấn)
Kiểm tra xem đoạn của node hiện tại có nằm hoàn toàn trong khoảng truy vấn không. Nếu có, trả về kết quả node đó. Nếu không, chia nhỏ và gọi đệ quy trên hai con.

### Point Update (Cập nhật điểm)
Cập nhật một phần tử ở mảng gốc. Cập nhật lá tương ứng và lan truyền ngược lên gốc để cập nhật các node cha.

### Range Update & Lazy Propagation (Cập nhật đoạn)
Khi cần cộng thêm một lượng vào toàn bộ các phần tử trong đoạn `[L, R]`, cập nhật trực tiếp tới tất cả lá sẽ mất $O(N)$. Sử dụng **Lazy Propagation**: đánh dấu (tag) node đang xét nếu đoạn của nó nằm trọn trong `[L, R]` và hoãn việc cập nhật xuống các node con cho đến khi các node con đó được truy cập tới.

## 5. Ví dụ minh họa (Step-by-step Example)
Giả sử mảng $A = [1, 3, 5, 7, 9, 11]$.
Truy vấn: Tổng đoạn.
- Node gốc lưu tổng toàn bộ `[0, 5]` = 36.
- Con trái lưu `[0, 2]` = 9, con phải lưu `[3, 5]` = 27.
- Tiếp tục chia đến các lá là chính các phần tử.
Khi cập nhật `A[1] = 4`, ta tìm xuống lá của `A[1]`, cập nhật nó thành 4, rồi quay lui cập nhật lại các node cha `[0, 2]` và gốc `[0, 5]`.

## 6. Độ phức tạp (Complexity)
- **Space Complexity:** $O(N)$ (cụ thể là $4N$).
- **Build Time:** $O(N)$.
- **Point Update Time:** $O(\log N)$.
- **Range Query Time:** $O(\log N)$.
- **Range Update Time (with Lazy Propagation):** $O(\log N)$.

## 7. Ưu và nhược điểm (Pros & Cons)
**Ưu điểm:**
- Thực hiện các Range Query và Updates trong $O(\log N)$.
- Rất linh hoạt, có thể lưu trữ đa dạng thông tin (Sum, Min, Max, GCD, LCM, bitwise operations).

**Nhược điểm:**
- Tiêu tốn bộ nhớ ($4N$).
- Cài đặt có thể phức tạp, dễ xảy ra lỗi Off-by-one.
- Code khá dài so với Binary Indexed Tree (Fenwick Tree).

## 8. So sánh (Comparison)
| Cấu trúc dữ liệu | Point Update | Range Query | Range Update | Memory | Code Complexity |
|------------------|--------------|-------------|--------------|--------|-----------------|
| Prefix Sum       | $O(N)$       | $O(1)$      | $O(N)$       | $O(N)$ | Rất thấp        |
| Fenwick Tree     | $O(\log N)$  | $O(\log N)$ | $O(\log N)$* | $O(N)$ | Thấp            |
| Segment Tree     | $O(\log N)$  | $O(\log N)$ | $O(\log N)$  | $O(4N)$| Cao             |

*Fenwick Tree Range Update kết hợp Range Query phức tạp hơn.

## 9. Các biến thể (Variants)
- **Dynamic Segment Tree:** Không tạo đủ $4N$ phần tử mà chỉ tạo node khi cần thiết (giúp xử lý khoảng $N \le 10^9$).
- **2D Segment Tree:** Dùng cho truy vấn trên mảng 2 chiều.
- **Persistent Segment Tree:** Lưu lại các trạng thái lịch sử của Segment Tree.
- **Iterative Segment Tree:** Cài đặt bằng vòng lặp (bottom-up) thay vì đệ quy, tiết kiệm bộ nhớ stack và chạy nhanh hơn.

## 10. Ứng dụng (Applications)
- Computer graphics (tính diện tích hợp của các hình chữ nhật).
- Trò chơi điện tử (Game Development) xử lý va chạm hoặc truy vấn vùng tĩnh/động.
- Phân tích chuỗi thời gian (cổ phiếu Min/Max trong ngày).
- Cơ sở dữ liệu: thực hiện Range Query hiệu quả.

## 11. Hỗ trợ trong ngôn ngữ lập trình (Language Support)
Segment Tree hiếm khi có sẵn trong các thư viện chuẩn (Standard Libraries) của Java, C++, Python. Lập trình viên phải tự cài đặt dựa trên yêu cầu cụ thể của từng bài toán.

## 12. 20 Lỗi phổ biến (20 Common Bugs)
1. **Off-by-one Error ở kích thước mảng:** Dùng $2N$ thay vì $4N$.
2. **Lỗi tràn số (Integer Overflow):** Truy vấn Sum trên mảng số lớn nhưng không dùng `long`.
3. **Quên `lazy` tag reset:** Không xóa tag `lazy` sau khi push down.
4. **Sai logic `push_down`:** Cộng dồn `lazy` tag nhưng không nhân với độ dài đoạn con trong Range Sum.
5. **Sai điều kiện dừng truy vấn:** Dừng lại khi cắt ngang (partial overlap) thay vì trọn vẹn (complete overlap).
6. **Lỗi `mid` calculation:** Tràn số khi tính `mid = (L + R) / 2`, nên dùng `mid = L + (R - L) / 2`.
7. **Lỗi tham chiếu chỉ số:** Cập nhật nhầm lá `idx` thay vì giá trị thực của `A[idx]`.
8. **Nhầm lẫn mảng $1$-indexed và $0$-indexed:** Segment Tree build theo 1-indexed nhưng truy vấn lại truyền 0-indexed.
9. **Return sai giá trị mặc định:** Truy vấn Range Min trả về 0 thay vì `Integer.MAX_VALUE` khi ngoài vùng giao.
10. **Quên gọi đệ quy trên node con:** Logic thiếu nhánh đi vào con trái hoặc con phải.
11. **Push down sai vị trí:** Gọi `push_down` sau khi đã đệ quy xong con thay vì trước khi đệ quy con.
12. **Cập nhật node lá lặp:** Khi $L==R$, vẫn cố `push_down`.
13. **Chồng chéo biến toàn cục:** Dùng chung mảng `tree` cho nhiều testcase mà quên reset/xóa.
14. **Phối hợp Lazy tag sai:** Khi cập nhật giá trị mới (set) nhưng lại cộng dồn (add) vào `lazy` tag cũ.
15. **Sai công thức hợp nhất (merge):** Ở truy vấn đoạn không giao, cố tình merge kết quả với giá trị rác.
16. **Sai logic cập nhật Range Update:** Ở node lá `L==R`, vẫn nhân giá trị lazy với 2 hoặc sai hệ số.
17. **Cấp phát động rò rỉ bộ nhớ:** Trong Dynamic Segment Tree không giải phóng node không dùng (C/C++).
18. **Nhầm kích thước mảng gốc $N$ với $4N$:** Khởi tạo $N$ node nhưng index chạy tới $4N$.
19. **Lỗi Stack Overflow:** $N$ rất lớn gây sâu đệ quy, chưa cấu hình stack size hoặc nên dùng Iterative.
20. **Lỗi Range Query với L > R:** Truy vấn với tham số đảo ngược không được bao lỗi dẫn đến infinite loop.

## 13. 30 Trường hợp góc (30 Edge Cases)
1. Mảng gốc rỗng ($N=0$).
2. Mảng có 1 phần tử ($N=1$).
3. Toàn bộ phần tử là số âm (tìm Max, Range Sum).
4. Truy vấn ngoài khoảng $[0, N-1]$.
5. Truy vấn $L > R$.
6. Truy vấn vừa đúng 1 phần tử $L = R$.
7. Cập nhật Range Update cho toàn mảng.
8. Truy vấn Range Query cho toàn mảng.
9. Cập nhật giá trị về 0 trên toàn đoạn.
10. Lần lượt cập nhật từng phần tử một để giả lập Range Update.
11. N lớn nhưng các phần tử rất nhỏ.
12. Các phần tử rất lớn gây tràn `int` kể cả chưa query.
13. Truy vấn Min/Max trên mảng gồm các giá trị bằng nhau.
14. `L` và `R` chênh lệch 1 đơn vị.
15. Range Update cộng dồn liên tiếp cực nhiều lần.
16. Range Update và Point Update xen kẽ nhau trên cùng 1 phần tử.
17. Dùng Segment Tree với phép XOR.
18. Giao của truy vấn và node hiện tại là rỗng (hoàn toàn không giao).
19. Lazy tag lưu giá trị âm xen kẽ số dương.
20. Lazy tag gán (Set) giá trị 0.
21. Dynamic Segment tree trên miền giá trị cực lớn $[ -10^9, 10^9 ]$.
22. Cập nhật Range Update với lượng cộng thêm = 0.
23. Persistent Segment Tree với phiên bản truy xuất trùng phiên bản hiện tại.
24. Truy vấn mảng con tại 2 nửa biên của Segment.
25. Đoạn được cập nhật không trùng khớp ranh giới các node của cây.
26. Mảng ban đầu chưa khởi tạo (tất cả bằng 0).
27. Đỉnh cao nhất chứa `lazy` tag nhưng bị ghi đè nhiều lần trước khi push_down.
28. Query ngay lập tức sau khi Build (không có Update).
29. Cập nhật một nửa cây bên trái nhiều lần, sau đó query cây bên phải.
30. Tràn chỉ số mảng $4N$ khi $N = 2^k + 1$.

## 14. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Segment Tree khác Fenwick Tree (BIT) ở điểm nào? Khi nào dùng cấu trúc nào?
2. Hãy mô tả kĩ thuật Lazy Propagation và lý do tại sao nó giúp giảm độ phức tạp từ $O(N)$ xuống $O(\log N)$.
3. Giải thích kích thước $4N$ trong cài đặt Segment Tree dạng mảng.
4. Làm thế nào để cài đặt Iterative Segment Tree (không đệ quy)?
5. Bạn hãy nêu thuật toán tìm K-th zero trong một mảng nhị phân dùng Segment Tree.
6. Thế nào là Dynamic Segment Tree? Nêu tình huống bắt buộc phải dùng nó.
7. Bạn sẽ giải quyết thế nào nếu mảng ban đầu thay đổi chiều dài (thêm/xóa phần tử) liên tục?
8. Nêu cách lưu trữ hai giá trị Max và số lần xuất hiện của Max trong một node của Segment Tree.
9. Có thể thực hiện hai phép toán là `add` và `multiply` trong cùng một Segment tree bằng Lazy Propagation không? Cách xử lý tag như thế nào?
10. Trong truy vấn Range Sum, khi node chứa đoạn một phần nằm trong truy vấn, ta xử lý ra sao?
11. Persistent Segment Tree là gì? Nêu một ứng dụng của nó.
12. Giải thích sự chênh lệch hằng số thời gian giữa Fenwick Tree và Segment Tree.
13. Có thể dùng Segment Tree cho bài toán RMQ (Range Minimum Query) 2D không? Độ phức tạp là bao nhiêu?
14. Nếu phép toán hợp nhất (merge) không có tính giao hoán, Segment Tree có hoạt động được không? (Ví dụ: phép nhân ma trận).
15. Tại sao không thể áp dụng Lazy Propagation cho mọi loại bài toán? Cho một ví dụ không áp dụng được.
16. Mô tả quy trình gỡ lỗi khi nhận kết quả sai ở một truy vấn ngẫu nhiên trong bài Segment Tree.
17. Merge Sort Tree là gì? Dùng nó để làm gì?
18. Làm thế nào để tìm chỉ số phần tử đầu tiên $\ge X$ trong một đoạn bằng Segment Tree.
19. Cài đặt Segment tree khi chỉ số không bắt đầu từ 0 mà từ một số âm.
20. Phân tích ưu nhược điểm của việc biểu diễn cây phân đoạn bằng Node pointers (OOP) so với mảng tuyến tính.

## 15. Best Practices (Thực hành tốt nhất)
- Luôn sử dụng $4N$ cho kích thước mảng an toàn.
- Gom `mid = L + (R - L) / 2` để tránh tràn số.
- Tách riêng hàm `build`, `update`, `query` và `push_down` rõ ràng để dễ debug.
- Mảng `lazy` nên chứa cờ trạng thái rõ ràng (ví dụ hằng số `NOT_LAZY`).
- Kiểm tra cẩn thận giá trị mặc định trả về (neutral element: 0 cho tổng, MAX cho min, MIN cho max, 0 cho XOR).

## 16. Mẫu code cơ bản (Basic Code Templates)
```java
class SegmentTree {
    int[] tree;
    int n;

    public SegmentTree(int[] arr) {
        n = arr.length;
        tree = new int[4 * n];
        build(arr, 0, 0, n - 1);
    }

    private void build(int[] arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = (start + end) / 2;
            build(arr, 2 * node + 1, start, mid);
            build(arr, 2 * node + 2, mid + 1, end);
            tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
        }
    }
    // Update and Query methods...
}
```

## 17. Design Patterns
- Có thể trừu tượng hóa (Abstract) hàm merge thông qua interface để dùng lại 1 template cho nhiều dạng (Sum, Min, Max). `interface Combiner<T> { T combine(T a, T b); }`.

## 18. Tối ưu hóa (Optimization)
- Sử dụng **Iterative Segment Tree** (Segment tree mảng 1 chiều, cập nhật từ dưới lên) giúp vòng lặp nhanh hơn đệ quy, giảm bộ nhớ cache miss xuống còn $2N$.
- Không lưu `L`, `R` trong cấu trúc Node đối với OOP mà truyền qua tham số hàm để giảm tốn RAM.

## 19. Mối liên hệ với các cấu trúc dữ liệu/thuật toán khác (Relationships)
- **Binary Indexed Tree (Fenwick Tree):** Giải quyết tập con bài toán của Segment tree nhưng nhanh nhẹn và ít tốn bộ nhớ hơn.
- **Sparse Table:** Xử lý RMQ không có cập nhật trong thời gian truy vấn $O(1)$.
- **Treap / Splay Tree:** Nếu cần thao tác chèn, xóa phần tử, Balanced BST được ưu tiên hơn.

## 20. Câu hỏi trắc nghiệm (MCQs)
**Q1.** Chiều cao tối đa của Segment tree cho mảng N phần tử là:
A) $O(N)$
B) $O(\log N)$
C) $O(N \log N)$
D) $O(1)$
*Đáp án: B*

**Q2.** Lazy Propagation được dùng để:
A) Xóa node không dùng
B) Tăng tốc độ Build cây
C) Tối ưu hóa cập nhật trên một đoạn (Range Update)
D) Chèn phần tử mới vào mảng
*Đáp án: C*

## 21. Bài tập thực hành tự giải (Practice Exercises)
1. Cho mảng A, thực hiện các thao tác: đổi dấu toàn bộ các phần tử trong `[L, R]` và tìm tổng đoạn.
2. Tìm chuỗi con tăng dài nhất trong mảng bằng kỹ thuật Dynamic Segment Tree.
3. Cho mảng, tìm phần tử lớn thứ 2 trong một đoạn.

## 22. Debugging Tips (Mẹo gỡ lỗi)
- In ra cấu trúc cây dưới dạng mảng `[node, L, R, val, lazy]` sau mỗi truy vấn để quan sát.
- Viết thuật toán brute-force $O(N)$ kiểm chứng kết quả với các mảng nhỏ được sinh ngẫu nhiên (Fuzz testing).

## 23. Glossary (Thuật ngữ)
- **Range Query:** Truy vấn khoảng.
- **Lazy Propagation:** Lan truyền lười.
- **Point Update:** Cập nhật tại một điểm.
- **Partial Overlap:** Giao thoa một phần.

## 24. Tài liệu tham khảo (References)
- CP-Algorithms: Segment Tree
- Introduction to Algorithms (CLRS)
- LeetCode Discuss & Codeforces Tutorials.
