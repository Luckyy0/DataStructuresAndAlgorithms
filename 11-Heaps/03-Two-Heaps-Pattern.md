# Kỹ thuật Two Heaps (Two Heaps Pattern)

## 1. Giới thiệu (Introduction)
Mẫu **Two Heaps** là một kỹ thuật mạnh mẽ trong cấu trúc dữ liệu và thuật toán, thường được sử dụng để giải quyết các bài toán liên quan đến việc duy trì một tập hợp các số liệu và cần liên tục truy xuất các giá trị ở giữa (như Median - trung vị) hoặc chia dữ liệu thành hai phần riêng biệt với một số đặc điểm nhất định.

## 2. Khái niệm cốt lõi (Core Concepts)
- Sử dụng hai Heap (hàng đợi ưu tiên):
  - **Max-Heap**: Lưu trữ nửa dưới (lower half) của các phần tử. Phần tử lớn nhất của nửa dưới sẽ ở trên cùng.
  - **Min-Heap**: Lưu trữ nửa trên (upper half) của các phần tử. Phần tử nhỏ nhất của nửa trên sẽ ở trên cùng.
- **Balancing (Cân bằng)**: Để tìm Median dễ dàng, kích thước của Max-Heap và Min-Heap không được chênh lệch nhau quá 1 phần tử.
- Bất biến (Invariant): Mọi phần tử trong Max-Heap luôn nhỏ hơn hoặc bằng mọi phần tử trong Min-Heap.

## 3. Cấu trúc dữ liệu & Thuật toán (Data Structures & Algorithms)
**Thuật toán thêm phần tử (Add Element)**:
1. So sánh phần tử mới với gốc của Max-Heap. Nếu nhỏ hơn hoặc bằng, đẩy vào Max-Heap. Ngược lại, đẩy vào Min-Heap.
2. Cân bằng lại: Nếu kích thước một Heap lớn hơn Heap kia 2 phần tử, lấy phần tử gốc của Heap lớn hơn đẩy sang Heap nhỏ hơn.

**Thuật toán tìm trung vị (Find Median)**:
- Nếu kích thước hai Heap bằng nhau: Trung vị là trung bình cộng của gốc Max-Heap và gốc Min-Heap.
- Nếu không: Trung vị là gốc của Heap có nhiều phần tử hơn.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**:
  - Insert: `O(log N)` (thêm vào một Heap và có thể cân bằng).
  - Find Median: `O(1)` (chỉ lấy phần tử gốc của các Heaps).
- **Space Complexity**: `O(N)` để lưu trữ toàn bộ các phần tử trong hai Heaps.

## 5. So sánh với các kỹ thuật khác
- **Balanced Binary Search Tree (AVL/Red-Black Tree)**: BBST cũng có thể tìm Median và chèn với thời gian `O(log N)`. Tuy nhiên, Two Heaps thường dễ triển khai hơn, có hằng số thời gian nhỏ hơn và không cần tự cân bằng cấu trúc cây phức tạp, thư viện Heap/PriorityQueue có sẵn trong nhiều ngôn ngữ.
- **Sorting**: Sắp xếp lại mỗi khi thêm phần tử mất `O(N log N)`.
- **Insertion Sort**: Thêm mất `O(N)`, tìm Median `O(1)`.

## 6. Ứng dụng thực tế (Real-world Applications)
- **Stream Processing**: Tính toán các số liệu thống kê thời gian thực (như trung vị) từ một luồng dữ liệu (data stream) vô hạn.
- **Sliding Windows**: Phân tích dữ liệu cảm biến, mạng, hoặc giá chứng khoán trong các cửa sổ trượt.
- Hệ thống quản lý tác vụ ưu tiên động và cấp phát tài nguyên theo mức độ ưu tiên.

## 7. Các dạng bài toán phổ biến (Common Problem Types)
1. Cập nhật Median trong luồng dữ liệu liên tục.
2. Tìm Median trong mảng trượt (Sliding Window Median).
3. Lập lịch / Lựa chọn dự án dựa trên hai yếu tố trái ngược (ví dụ: tối đa hóa lợi nhuận với vốn hiện có).
4. Các bài toán cân bằng hai nhóm dữ liệu động.

## 8. Mẫu code cơ bản (Basic Code Templates)
```java
class MedianFinder {
    PriorityQueue<Integer> maxHeap; // Lưu nửa dưới
    PriorityQueue<Integer> minHeap; // Lưu nửa trên

    public MedianFinder() {
        maxHeap = new PriorityQueue<>((a, b) -> b - a);
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        if (maxHeap.isEmpty() || maxHeap.peek() >= num) {
            maxHeap.add(num);
        } else {
            minHeap.add(num);
        }
        
        // Cân bằng (Balance)
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.add(maxHeap.poll());
        } else if (minHeap.size() > maxHeap.size()) {
            maxHeap.add(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() == minHeap.size()) {
            return maxHeap.peek() / 2.0 + minHeap.peek() / 2.0;
        }
        return maxHeap.peek();
    }
}
```

## 9. 20 Sai lầm thường gặp (20 Common Bugs)
1. Khởi tạo sai Comparator cho Max-Heap (Quên `(a, b) -> b - a` hoặc dùng max comparator sai).
2. Tràn số (Integer Overflow) khi tính `(a + b) / 2.0`. Cách khắc phục: `a / 2.0 + b / 2.0`.
3. Không giữ vững tính chất bất biến (chèn vào Heap mà không kiểm tra điều kiện giá trị).
4. Quên cân bằng sau khi xóa phần tử trong các bài Sliding Window.
5. So sánh trực tiếp phần tử gốc khi Heap đang rỗng gây lỗi NullPointerException hoặc Exception.
6. So sánh `double` không dùng sai số `epsilon` nếu bài yêu cầu độ chính xác.
7. Xóa phần tử trong `PriorityQueue` mất `O(N)`, không nhận ra độ phức tạp này trong Sliding Window Median.
8. Thêm vào Min-Heap nhưng điều kiện size của Max-Heap không chuẩn gây mất cân bằng.
9. Đảo ngược vị trí của hai Heap khi lấy giá trị Median.
10. Viết hàm Custom Comparator sai với các kiểu dữ liệu Object (như Class, Array).
11. Bỏ qua trường hợp mảng có số âm (cấu hình Comparator sai với phép trừ: `b - a` có thể bị underflow/overflow). Cách đúng: `Integer.compare(b, a)`.
12. Lỗi mất dấu phần thực khi tính trung bình cộng: `(max + min) / 2` thay vì `/ 2.0`.
13. Gọi `poll()` khi Heap đang rỗng trong quá trình cân bằng.
14. Chỉ sử dụng 1 Heap và sắp xếp phần còn lại làm giảm hiệu suất xuống `O(N log N)`.
15. Không xử lý đúng cách khi Heap chứa các phần tử trùng lặp (duplicates).
16. Nhầm lẫn giữa size `+ 1` và size `+ 2` khi cân bằng.
17. Cho rằng chênh lệch kích thước của hai Heap chỉ có thể là 1, nhưng nếu thao tác sai có thể thành 2.
18. Không xem xét Lazy Deletion trong Sliding Window dẫn đến TLE.
19. Gán giá trị sai khi khởi tạo Heap với Collection.
20. Xóa phần tử khỏi Heap bằng `remove(Object)` mặc định trong vòng lặp rất chậm.

## 10. Tối ưu hóa (Optimization)
- **Lazy Deletion**: Khi dùng Sliding Window Median, thay vì gọi `remove(val)` mất `O(N)`, hãy lưu số lần xuất hiện của các phần tử cần xóa vào một `HashMap` (hoặc mảng đếm tần suất). Chỉ xóa bỏ phần tử khi nó nằm ở đỉnh của Heap.
- **Index Priority Queue (TreeSet / TreeMap)**: Sử dụng các CTDL cân bằng như `TreeSet` ở Java hoặc C++ `std::set` để thay thế nếu muốn xóa phần tử trong `O(log N)` thực sự.

## 11. Nâng cao (Advanced Topics)
- Kết hợp Heap với Hash Map để xử lý bài toán lịch trình phức tạp.
- Sử dụng K-Heaps (hơn 2 Heaps) cho các thuật toán phân vị bậc K (Quantiles).
- Phân tích Amortized (khấu hao) của các thao tác xóa lười (Lazy Deletion) trong Two Heaps.

## 12. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Kỹ thuật Two Heaps được sử dụng chủ yếu để làm gì?
2. Tại sao chúng ta cần Max-Heap và Min-Heap, mà không phải là hai Min-Heap?
3. Thời gian để tìm phần tử trung vị bằng kỹ thuật này là bao nhiêu?
4. Nếu số lượng phần tử là số chẵn, trung vị được tính như thế nào trong bài toán Two Heaps?
5. Làm thế nào để giữ cân bằng hai Heaps? Mức chênh lệch tối đa giữa hai Heaps là bao nhiêu?
6. Thay vì dùng Two Heaps, tôi có thể dùng mảng đã sắp xếp không? Giải thích ưu/nhược điểm.
7. Giải thích cách thực hiện thao tác xóa một phần tử cụ thể khỏi hệ thống Two Heaps.
8. So sánh Two Heaps với Balanced Binary Search Tree (như TreeMap ở Java) để tìm Median.
9. Phương pháp "Lazy Deletion" là gì và tại sao lại quan trọng với Two Heaps trong Sliding Window?
10. Làm sao để áp dụng Two Heaps vào bài toán IPO (Maximize Capital)?
11. Two Heaps xử lý mảng có chứa các phần tử bị lặp lại như thế nào?
12. Có thể sử dụng Priority Queue ở C++ hoặc Python để thiết lập Two Heaps không? Nêu cách khởi tạo cụ thể.
13. Hậu quả gì xảy ra nếu quên không cân bằng 2 Heaps sau thao tác insert?
14. Trong bài Median, nếu thay vì Median tôi muốn tìm phân vị thứ 25% (Quartile 1) thì làm sao?
15. Có thể áp dụng Two Heaps để tìm Mode (giá trị xuất hiện nhiều nhất) không?
16. Cách để tránh Integer Overflow khi Comparator sử dụng phép trừ (`a - b`) là gì?
17. Nếu luồng dữ liệu có số âm, số dương, có vấn đề gì với tính bất biến không?
18. Độ phức tạp không gian (Space Complexity) của mẫu Two Heaps là gì?
19. Giải thích làm thế nào thuật toán này có thể hữu ích trong luồng video/âm thanh thời gian thực.
20. Bài toán Course Schedule III sử dụng Heap như thế nào, nó có phải Two Heaps truyền thống không?

## 13. Các biến thể (Variations)
- **Top K Elements + Two Heaps**: Sử dụng Min-Heap để giữ K phần tử lớn nhất và Max-Heap để giữ các phần tử còn lại, sau đó di chuyển giữa hai nhóm.
- **K-th Smallest/Largest**: Dễ dàng chuyển đổi hệ thống Heaps để tìm phần tử lớn thứ K bằng cách giữ 1 Min-Heap size K.

## 14. Thiết kế hệ thống (System Design)
Trong thực tế, với dữ liệu cực lớn không nằm vừa RAM:
- Thay vì Heaps đơn giản, người ta dùng Approximate Quantiles, T-Digest, hoặc Histogram để tìm Median xấp xỉ trong luồng (Stream) hàng tỷ sự kiện.
- Nếu muốn tìm Median chính xác phân tán, có thể dùng Binary Search trên dải giá trị và Count phân tán.

## 15. 30 Các trường hợp biên (30 Edge Cases)
1. Thêm chỉ 1 phần tử.
2. Thêm 2 phần tử giống hệt nhau.
3. Thêm 2 phần tử với phần tử thứ 2 nhỏ hơn phần tử đầu tiên.
4. Thêm số lượng cực lớn các phần tử giống nhau (tất cả là 0).
5. Luồng gồm các số tăng dần liên tục.
6. Luồng gồm các số giảm dần liên tục.
7. Luồng có chứa số nguyên nhỏ nhất (`Integer.MIN_VALUE`).
8. Luồng có chứa số nguyên lớn nhất (`Integer.MAX_VALUE`).
9. Luồng có chứa đồng thời `MAX_VALUE` và `MIN_VALUE`.
10. Gọi `findMedian()` khi chưa có phần tử nào.
11. Gọi `findMedian()` sau khi đã thêm một số lẻ các phần tử.
12. Gọi `findMedian()` sau khi đã thêm một số chẵn các phần tử.
13. Thao tác thêm vào Heap và xóa phần tử có cùng giá trị cùng lúc (Sliding window).
14. Kích thước Window (K) lớn hơn mảng đầu vào (Sliding Window Median).
15. Kích thước Window K = 1.
16. Kích thước Window K = 2.
17. Các phần tử trong luồng xen kẽ lớn-nhỏ-lớn-nhỏ (zigzag).
18. Cân bằng bị lệch khi Heap rỗng.
19. Tràn số khi cộng hai phần tử gốc để chia trung bình.
20. Xóa phần tử không tồn tại trong Heap (do logic Sliding Window sai lệch).
21. Thêm số 0 vào nhiều lần.
22. Các số hạng âm rất lớn và số hạng dương rất lớn xen kẽ.
23. Sliding window khi tất cả phần tử trong cửa sổ bằng nhau.
24. Bất biến "max của MaxHeap <= min của MinHeap" bị phá vỡ nếu chỉ thêm vào heap nhỏ hơn mà không kiểm tra.
25. Mảng đầu vào rỗng (Empty stream).
26. Mảng đầu vào rất lớn dẫn đến OutOfMemoryError.
27. Đưa cùng một đối tượng (tham chiếu) vào Heap.
28. Sliding Window với K chẵn nhưng mảng đầu vào lại rất ngắn.
29. Cấu hình Comparator dẫn đến không ổn định (Unstable sort).
30. Tìm Median khi Heap chứa các số float có NaN.

## 16. Hướng dẫn gỡ lỗi (Debugging Guide)
- **Check Invariants**: Sau mỗi lần chèn, luôn in ra `maxHeap.peek()` và `minHeap.peek()` để đảm bảo bất biến `maxHeap.peek() <= minHeap.peek()`.
- **Size Check**: Kiểm tra in ra `maxHeap.size() - minHeap.size()`. Chênh lệch không bao giờ được lớn hơn 1 hoặc nhỏ hơn 0 (nếu ta chọn maxHeap luôn lưu nhiều hơn).
- **Tràn số**: Đặc biệt kiểm tra dòng `(maxHeap.peek() + minHeap.peek()) / 2.0`. Bẻ thành `maxHeap.peek() / 2.0 + minHeap.peek() / 2.0` để trace lỗi tràn.

## 17. Mô hình hóa bài toán (Problem Modeling)
Bài toán "Tìm cân bằng giữa hai phía của dữ liệu" có thể được mô hình hóa:
- **Tập Hạ (Lower Set)**: Chứa một nửa phần tử nhỏ hơn (dùng Max-Heap để dễ tiếp cận phần tử lớn nhất).
- **Tập Thượng (Upper Set)**: Chứa một nửa phần tử lớn hơn (dùng Min-Heap để dễ tiếp cận phần tử nhỏ nhất).

## 18. Công cụ & Thư viện (Tools & Libraries)
- **Java**: `java.util.PriorityQueue`
- **C++**: `std::priority_queue`
- **Python**: `heapq` (Lưu ý: Python chỉ hỗ trợ Min-Heap mặc định, cần nhân giá trị với `-1` để tạo Max-Heap).
- **C#**: `PriorityQueue<TElement, TPriority>` (.NET 6+)

## 19. Bài tập thực hành (Practice Problems)
- Tìm kiếm trung vị trong Stream (Leetcode 295)
- Sliding Window Median (Leetcode 480)
- IPO (Maximize Capital) (Leetcode 502)

## 20. Câu hỏi trắc nghiệm (Multiple Choice Questions)
**Câu 1:** Trong mẫu Two Heaps, Heap nào được sử dụng để lưu trữ nửa có giá trị nhỏ hơn của dữ liệu?
A) Min-Heap
B) Max-Heap
C) Fibonacci Heap
D) Binomial Heap
*Đáp án:* B

**Câu 2:** Độ phức tạp không gian lưu trữ của kỹ thuật Two Heaps là bao nhiêu?
A) O(1)
B) O(log N)
C) O(N)
D) O(N log N)
*Đáp án:* C

## 21. Tài liệu tham khảo (References)
- CLRS (Introduction to Algorithms)
- Giải thuật cơ bản trên GeeksforGeeks và LeetCode.

## 22. Tổng kết (Summary)
Kỹ thuật Two Heaps là chìa khóa để giải quyết các bài toán yêu cầu duy trì tính chất trung vị hoặc phân chia luồng dữ liệu làm hai phần một cách nhanh chóng và hiệu quả.

## 23. Ghi chú cá nhân (Personal Notes)
(Dành cho người học ghi chép thêm)
- Luôn nhớ cài đặt comparator cho Max-Heap vì mặc định ngôn ngữ là Min-Heap.

## 24. Phụ lục (Appendix)
Mã giả Python minh họa nhanh cách mô phỏng Max-Heap bằng `heapq`:
```python
import heapq

# max_heap: dùng mảng và push số âm
max_heap = []
heapq.heappush(max_heap, -val)
```
