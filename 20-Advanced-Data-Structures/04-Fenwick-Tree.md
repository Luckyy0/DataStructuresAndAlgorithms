# Cấu trúc dữ liệu nâng cao: Fenwick Tree (Binary Indexed Tree)

## 1. Tổng quan (Introduction)
Fenwick Tree, hay còn gọi là Binary Indexed Tree (BIT), là một cấu trúc dữ liệu hiệu quả để tính toán tổng tiền tố (prefix sum) và cập nhật các phần tử của một mảng số trong thời gian logarit.

## 2. Khái niệm cốt lõi (Core Concepts)
BIT sử dụng biểu diễn nhị phân của chỉ số (index) để quản lý một tập hợp các phần tử, cho phép các thao tác Range Query và Point Update chạy mượt mà.

## 3. Logic Lowbit `i & (-i)`
Phép toán `i & (-i)` lấy ra bit 1 thấp nhất (Least Significant Bit) của số nguyên `i`. Ví dụ, `10 & (-10)` (`1010 & 0110`) sẽ cho `2` (`0010`). Độ dài đoạn mà phần tử ở chỉ số `i` quản lý chính bằng `i & (-i)`.

## 4. Các thao tác cơ bản (Basic Operations)
- **Point Update:** Cập nhật giá trị tại một vị trí, ta nhảy tiến tới các nút cha bằng cách `i += i & (-i)`.
- **Prefix Sum Query:** Lấy tổng tiền tố từ đầu đến `i`, ta nhảy lùi bằng cách `i -= i & (-i)`.

## 5. Cấu trúc phân cấp (Hierarchical Structure)
Khác với cây nhị phân thông thường, BIT lưu trữ dưới dạng mảng 1 chiều, trong đó mỗi chỉ số lưu trữ tổng của một khối dữ liệu cụ thể.

## 6. Phân tích độ phức tạp (Time & Space Complexity)
- **Time Complexity:** 
  - Point Update: $O(\log N)$
  - Prefix Sum Query: $O(\log N)$
  - Cấu dựng (Build): $O(N)$ (nếu dùng thuật toán tối ưu) hoặc $O(N \log N)$ (nếu insert tuần tự).
- **Space Complexity:** $O(N)$, chỉ cần thêm một mảng cùng kích thước mảng gốc.

## 7. Ưu điểm (Advantages)
- Tốn rất ít bộ nhớ, hằng số cực nhỏ.
- Cài đặt vô cùng ngắn gọn (chỉ vài dòng code).
- Rất dễ viết, ít bị lỗi khi thi đấu thuật toán (Competitive Programming).

## 8. Nhược điểm (Disadvantages)
- Không hỗ trợ tự nhiên nhiều phép toán phức tạp (như tìm max/min trong khoảng bất kỳ) tốt như Segment Tree.
- Chỉ số phải lớn hơn 0 (1-based index).

## 9. So sánh với Segment Tree (Segment Tree Comparison)
- BIT nhanh hơn và tốn ít bộ nhớ hơn Segment Tree.
- Segment Tree linh hoạt hơn, giải quyết được nhiều loại truy vấn hơn (RMQ - Range Minimum Query).

## 10. Cài đặt chi tiết (Detailed Implementation in Java)
```java
public class FenwickTree {
    private int[] tree;
    
    public FenwickTree(int size) {
        // 1-based indexing
        tree = new int[size + 1];
    }
    
    public void update(int i, int delta) {
        while (i < tree.length) {
            tree[i] += delta;
            i += i & (-i); // Di chuyển đến node cha
        }
    }
    
    public int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += tree[i];
            i -= i & (-i); // Di chuyển ngược lên
        }
        return sum;
    }
}
```

## 11. Các biến thể (Variants)
- Range Update, Point Query (sử dụng mảng hiệu - difference array).
- Range Update, Range Query (cần duy trì 2 BIT).
- 2D Fenwick Tree cho ma trận.

## 12. Ứng dụng thực tế (Real-world Applications)
- Đếm số nghịch thế (Inversion Counting).
- Thống kê dữ liệu động, hệ thống bảng xếp hạng thời gian thực.
- Thuật toán mã hóa số học (Arithmetic coding).

## 13. 20 Lỗi phổ biến (20 Common Bugs)
1. Dùng 0-based index thay vì 1-based index (Gây lặp vô hạn ở `query(0)` hoặc `update(0)`).
2. Lỗi tràn số (Integer Overflow) khi cộng dồn tổng tiền tố.
3. Không khởi tạo lại mảng BIT trước mỗi test case.
4. Quên thêm kích thước `+1` khi khởi tạo mảng (`new int[n]`).
5. Ở `update`, nhầm lẫn giữa giá trị mới và độ lệch (delta).
6. Viết sai logic `i & (-i)` thành `i & (~i)`.
7. Dùng `<=` trong khi điều kiện đúng phải là `<` ở vài cấu trúc kích thước động.
8. Ở thao tác Point Query khi chỉ có 1 mảng BIT (phải tính `query(i) - query(i-1)` thay vì `query(i)`).
9. Trong biến thể 2D, vòng lặp lồng nhau sai biến (`i += i & -i`, `j += i & -i` sai ở `j`).
10. Nhầm lẫn khi khởi tạo BIT trong $O(N)$ tuyến tính (truyền xuống thay vì lan truyền lên đúng cha).
11. Bỏ qua các giá trị chỉ số âm (nếu đầu vào có giá trị âm cần rank mapping).
12. Vòng lặp vô tận do điều kiện `i != 0` thành `i > 0` bị bỏ sót khi update.
13. Không nén tọa độ (Coordinate Compression) khi index quá lớn ($> 10^7$).
14. Không xử lý đúng Range Update (nhầm phép cộng `+delta` tại `left`, `-delta` tại `right` thay vì `right + 1`).
15. Không chú ý dấu trừ của delta khi xóa một phần tử.
16. Dùng Range Update, Range Query nhưng sai hệ số $i \times \text{delta}$.
17. Thiếu điều kiện giới hạn cây (Index out of bounds) ở hàm `update`.
18. Không ép kiểu (Type Casting) `long` khi `delta` lớn.
19. Gắn BIT cho Interval Trees một cách sai lầm thay vì đếm điểm.
20. Khởi tạo sai cách bằng cách chạy `update` bằng giá trị `tree[i]` thay vì `array[i]`.

## 14. 30 Trường hợp biên (30 Edge Cases)
1. Mảng gốc có kích thước $N = 1$.
2. Mảng gốc rỗng ($N = 0$).
3. Query(0) trả về gì (nếu có gọi nhầm)?
4. Tất cả các phần tử đều là số 0.
5. Cập nhật các phần tử bằng 0.
6. Cập nhật bằng số âm dẫn đến tổng âm.
7. Truy vấn khoảng $[L, R]$ với $L = R$.
8. Truy vấn khoảng $[L, R]$ với $L > R$ (Cần xử lý ném lỗi hoặc trả 0).
9. Mảng có nhiều phần tử giống nhau (khi đếm nghịch thế).
10. Các phần tử lớn bất thường $10^9$ gây tràn mảng chỉ số (Coordinate compression cần thiết).
11. $N$ đạt tối đa giới hạn của Java Heap (khoảng $2 \cdot 10^8$).
12. `update` liên tục tại cùng một vị trí.
13. Truy vấn dải từ $1$ đến $N$.
14. Chỉ số `i` tiến sát $2^{31}-1$.
15. Khởi tạo mảng ban đầu với các số âm.
16. Toàn bộ mảng chỉ cập nhật ở một nửa trước hoặc nửa sau.
17. Tần suất truy vấn gấp 100 lần tần suất cập nhật.
18. Độ lệch `delta` cực nhỏ và tích lũy do lỗi dấu phẩy động (nếu dùng double).
19. Cập nhật phần tử cuối cùng của mảng (`i = N`).
20. Mảng chỉ bao gồm hai giá trị phân biệt.
21. Rank array có các phần tử rank từ 1 thay vì 0 (tốt cho BIT).
22. Trục trặc về đồng bộ (Concurrency) nếu nhiều luồng gọi `update`.
23. Sử dụng BIT cho các chuỗi thay vì số nguyên (Hashing kết hợp).
24. Dữ liệu online streaming chưa biết $N$ (Dynamic BIT bằng HashMap).
25. Cập nhật với `delta = 0` (Thừa thãi nhưng không sai).
26. Khởi tạo bằng dữ liệu có kích thước ngẫu nhiên lẻ $N = 3, 7, 15$.
27. Đếm số nhỏ hơn bằng chính phần tử đó.
28. BIT 2D với mảng là hình chữ nhật chứ không phải hình vuông ($M \neq N$).
29. Cập nhật một dải bao trùm toàn bộ $[1, N]$.
30. Giá trị âm khi bitwise AND trong lowbit (Java sử dụng biểu diễn bù 2).

## 15. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Fenwick Tree khác Segment Tree như thế nào? Khi nào chọn cái nào?
2. Tại sao thao tác `update` lại di chuyển bằng `i += i & (-i)`?
3. Mô tả cách làm Range Update, Point Query bằng Fenwick Tree.
4. Làm thế nào để khởi tạo Fenwick Tree từ một mảng trong thời gian $O(N)$?
5. Cách tìm phần tử thứ $K$ lớn nhất (K-th order statistic) bằng Fenwick Tree?
6. Hãy mở rộng Fenwick Tree lên 2 chiều. Độ phức tạp là bao nhiêu?
7. Cấu trúc dữ liệu nào đếm số nghịch thế nhanh hơn: Merge Sort hay Fenwick Tree? So sánh.
8. Lowbit `i & (-i)` hoạt động như thế nào ở mức bitwise?
9. Nếu ta có chỉ số lên đến $10^9$, làm sao áp dụng Fenwick Tree? (Coordinate Compression / Dynamic BIT).
10. Tại sao 0-based index lại làm chết thuật toán Fenwick Tree?
11. Làm sao tính Range Update và Range Query bằng Fenwick Tree?
12. Bạn có thể sử dụng Fenwick Tree để lấy giá trị Max/Min của một khoảng thay vì Tổng không? Nếu có thì có giới hạn gì?
13. Viết code đếm số lượng các phần tử nhỏ hơn ở bên phải của mảng.
14. Fenwick Tree biểu diễn đồ thị cấu trúc dạng cây như thế nào? (Ai là node cha của ai).
15. Phân tích độ phức tạp không gian lưu trữ thực tế so với Segment Tree.
16. Có thể giảm chiều sâu cây Fenwick trong cấu trúc tĩnh bằng mảng gộp không?
17. Viết hàm lấy bit 1 quan trọng nhất (Highest Significant Bit) khác gì với LSB.
18. Thế nào là Coordinate Compression và triển khai nó bằng Java để kết hợp với BIT.
19. Nếu một hệ thống có 99% thao tác là Update và 1% là Query, có nên dùng BIT không?
20. Trình bày thuật toán đếm số nghịch thế toàn cục và nghịch thế cục bộ.

## 16. Thread Safety & Concurrency
Mặc định mảng `tree` không thread-safe. Nếu nhiều thread gọi `update`, cần sử dụng `AtomicIntegerArray` hoặc lock/synchronization để tránh Race Condition.

## 17. Quản lý bộ nhớ (Memory Management)
Do chỉ sử dụng một mảng một chiều $O(N)$, BIT cực kì thân thiện với Cache-line (Memory Locality tốt hơn nhiều cấu trúc phân nhánh dạng con trỏ).

## 18. Tối ưu hóa (Optimizations)
Khởi tạo cấu trúc trong thời gian tuyến tính $O(N)$ thay vì $O(N \log N)$ bằng cách duyệt $i$ từ $1$ đến $N$, cộng giá trị của nó cho cha của nó `i + (i & -i)` ngay lập tức nếu cha nằm trong khoảng hợp lệ.

## 19. Lịch sử hình thành (History)
Peter Fenwick đề xuất cấu trúc này vào năm 1994 trong một bài báo về nén dữ liệu liên quan đến thuật toán mã hóa số học, từ đó nó mang tên Fenwick Tree.

## 20. Các mẫu thiết kế (Design Patterns)
BIT hiếm khi áp dụng mẫu thiết kế hướng đối tượng phức tạp, vì điểm mạnh của nó là cực kì nhẹ và nhanh gọn. Nó thường là Private Helper Class hoặc Utility Module.

## 21. Hướng dẫn gỡ lỗi (Debugging Guide)
In toàn bộ mảng `tree` và ánh xạ chỉ số nhị phân. Dùng một mảng mộc (Brute-force array) chạy song song và đối chiếu kết quả Query tại các điểm ngẫu nhiên (Fuzz Testing).

## 22. Ví dụ từng bước (Step-by-step Example)
Giả sử có 4 phần tử. BIT khởi tạo ban đầu: $0, 0, 0, 0, 0$.
Thêm $3$ vào vị trí $1$: `tree[1]+=3`, sau đó nhảy lên cha $1 + 1 = 2 \to$ `tree[2]+=3`, nhảy lên cha $2 + 2 = 4 \to$ `tree[4]+=3`.

## 23. Các tài liệu tham khảo (References)
- CP-Algorithms: Fenwick Tree
- Cẩm nang Competitive Programming của Steven Halim (Chương Cấu trúc dữ liệu).
- "A new data structure for cumulative frequency tables" - Peter M. Fenwick, 1994.

## 24. Tổng kết (Conclusion)
Fenwick Tree là cấu trúc "nhỏ mà có võ". Mỗi dòng code của nó đều tinh gọn. Đây là kiến thức bắt buộc phải nằm lòng đối với các kỹ sư phần mềm khi tham gia các hệ thống có yếu tố xếp hạng, dữ liệu tiền tố thay đổi liên tục.
