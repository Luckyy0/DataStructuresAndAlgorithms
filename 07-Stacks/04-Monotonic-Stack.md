# Monotonic Stack (Stack Đơn Điệu)

## 1. Metadata
- **Chủ đề**: Monotonic Stack (Stack đơn điệu)
- **Cấu trúc dữ liệu**: Stack, Array
- **Ngôn ngữ**: Java 21
- **Phiên bản**: 1.0

## 2. Purpose (Mục đích)
Tài liệu này trình bày toàn diện về **Monotonic Stack**, một dạng đặc biệt của Stack trong đó các phần tử luôn được duy trì theo một thứ tự đơn điệu (tăng dần hoặc giảm dần). Mục đích là để giải quyết hiệu quả các bài toán tìm **Next Greater Element** (Phần tử lớn hơn tiếp theo) hoặc **Previous Smaller Element** (Phần tử nhỏ hơn trước đó).

## 3. Motivation (Động lực)
Trong nhiều bài toán mảng, việc tìm kiếm phần tử thỏa mãn điều kiện nhất định bằng cách sử dụng hai vòng lặp lồng nhau sẽ dẫn đến độ phức tạp $O(N^2)$. Sử dụng Monotonic Stack cho phép loại bỏ các phép so sánh dư thừa, đưa độ phức tạp về $O(N)$, tối ưu hóa đáng kể cho các hệ thống yêu cầu real-time hoặc mảng dữ liệu cực lớn.

## 4. Mathematical Foundation (Nền tảng toán học)
**Chứng minh độ phức tạp O(N):**
- Mọi phần tử trong mảng có kích thước $N$ sẽ được `push` (thêm vào) stack đúng 1 lần.
- Mọi phần tử chỉ có thể được `pop` (lấy ra) khỏi stack tối đa 1 lần.
- Mặc dù vòng lặp `while` nằm bên trong vòng lặp `for`, tổng số lần thực thi của thao tác `push` và `pop` trên toàn bộ thuật toán không vượt quá $2N$.
- Do đó, Time Complexity = $\Theta(2N) = O(N)$.

## 5. Core Theory (Lý thuyết cốt lõi)
**Monotonic Stack** được chia thành 2 loại:
1. **Monotonically Increasing Stack (Stack tăng dần)**: Các phần tử từ đáy lên đỉnh stack luôn tăng dần (hoặc bằng nhau). Dùng để tìm **Next Smaller Element** hoặc **Previous Smaller Element**. Khi gặp phần tử nhỏ hơn đỉnh stack, ta phải `pop`.
2. **Monotonically Decreasing Stack (Stack giảm dần)**: Các phần tử từ đáy lên đỉnh stack luôn giảm dần (hoặc bằng nhau). Dùng để tìm **Next Greater Element** hoặc **Previous Greater Element**. Khi gặp phần tử lớn hơn đỉnh stack, ta phải `pop`.

*Quy tắc vàng*: 
- Lưu **Index** (chỉ số) vào stack thay vì lưu giá trị, để có thể tính toán khoảng cách hoặc xác định vị trí trong mảng ban đầu.

## 6. Visual Explanation (Giải thích trực quan)

```mermaid
flowchart TD
    Start([Bắt đầu duyệt mảng]) --> CheckEmpty{Stack rỗng?}
    CheckEmpty -->|Đúng| Push[Push index hiện tại vào Stack]
    CheckEmpty -->|Sai| Compare{So sánh arr[i] với arr[Stack.peek()]?}
    Compare -->|Phá vỡ tính đơn điệu| Pop[Pop đỉnh Stack và cập nhật kết quả]
    Pop --> CheckEmpty
    Compare -->|Duy trì tính đơn điệu| Push
    Push --> NextItem[Tiếp tục phần tử tiếp theo]
    NextItem --> Start
```

## 7. Java Implementation (Triển khai Java)
Mã nguồn Java 21 tiêu chuẩn sử dụng `ArrayDeque` thay cho `Stack` cổ điển.

```java
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;

public class MonotonicStack {
    /**
     * Tìm phần tử lớn hơn tiếp theo (Next Greater Element)
     */
    public int[] nextGreaterElement(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        // Lưu trữ index, stack duy trì tính đơn điệu giảm dần (Decreasing)
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            // Trong khi stack không rỗng và phần tử hiện tại lớn hơn phần tử ở đỉnh stack
            while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
                int poppedIndex = stack.pop();
                result[poppedIndex] = nums[i]; // Cập nhật kết quả
            }
            stack.push(i);
        }
        return result;
    }
}
```

## 8. Step-by-Step (Từng bước)
Ví dụ tìm Next Greater Element cho mảng `[2, 1, 2, 4, 3]`:
1. `i=0, num=2`: Stack rỗng -> `Push index 0`. Stack: `[0]`
2. `i=1, num=1`: `1 < 2` (Duy trì giảm dần) -> `Push index 1`. Stack: `[1, 0]`
3. `i=2, num=2`: `2 > 1` -> `Pop 1`. Cập nhật `result[1] = 2`. `2 = 2` -> `Push index 2`. Stack: `[2, 0]`
4. `i=3, num=4`: `4 > 2` -> `Pop 2`. `result[2] = 4`. `4 > 2` -> `Pop 0`. `result[0] = 4`. `Push index 3`. Stack: `[3]`
5. `i=4, num=3`: `3 < 4` -> `Push index 4`. Stack: `[4, 3]`
6. Kết quả: `[4, 2, 4, -1, -1]`.

## 9. Complexity Analysis (Phân tích độ phức tạp)
- **Time Complexity**: $O(N)$ vì mỗi phần tử được push và pop đúng một lần.
- **Space Complexity**: $O(N)$ cho Stack trong trường hợp xấu nhất (mảng đã được sắp xếp tăng dần/giảm dần theo yêu cầu duy trì).

## 10. JVM Analysis (Phân tích JVM)
Sử dụng `ArrayDeque` mang lại Local Cache (Tối ưu L1/L2 cache) tốt hơn vì bộ nhớ dùng mảng liên tục, thay vì sử dụng Linked List (như `LinkedList`) gây ra hiện tượng Cache Miss cao do cấp phát Heap rời rạc.

## 11. OpenJDK Analysis (Phân tích OpenJDK)
Lớp `java.util.Stack` trong JDK extends `Vector`, tất cả phương thức đều bị `synchronized`, gây lãng phí hiệu năng do Thread Contention (tranh chấp luồng) không cần thiết. `ArrayDeque` không đồng bộ hóa, là Standard Standard Collections thay thế hoàn hảo cho Monotonic Stack trong single-thread.

## 12. Production Usage (Sử dụng trong thực tế)
- **Hệ thống cảnh báo giá**: Phân tích biểu đồ nến (candlestick chart) trong giao dịch chứng khoán (như bài Online Stock Span).
- **Phân tích hình học**: Thuật toán vẽ đa giác lớn nhất (Histogram/Rectangle) cho xử lý ảnh.
- **Tính toán lượng nước/khí**: Mô phỏng hệ thống thoát nước (Trapping Rain Water).

## 13. Design Decisions (Quyết định thiết kế)
- **Khởi tạo mảng kết quả với giá trị -1**: Giúp tiết kiệm thời gian vì nếu stack vẫn còn phần tử sau vòng lặp, ta không cần phải có thêm vòng lặp để gán giá trị mặc định (như không có Next Greater Element).
- **Sử dụng Index thay vì Value**: Đặc biệt bắt buộc với các bài toán như Histogram, vì cần tính width (chiều rộng) dựa trên index.

## 14. Common Bugs (20 Lỗi phổ biến)
1. Dùng `java.util.Stack` thay vì `Deque<Integer>`.
2. Push giá trị (value) thay vì chỉ số (index) vào stack.
3. Nhầm lẫn giữa `>=` và `>` khi duy trì đơn điệu tăng/giảm ngặt.
4. Quên xử lý các phần tử còn lại trong stack sau khi duyệt hết mảng.
5. So sánh phần tử hiện tại với index thay vì giá trị `nums[stack.peek()]`.
6. Tràn bộ nhớ (OutOfMemoryError) nếu push không giới hạn.
7. Tràn số (Integer Overflow) khi tính diện tích (ví dụ trong Histogram).
8. Gọi `pop()` trên stack rỗng, ném ra `NoSuchElementException`.
9. Khởi tạo mảng result nhưng quên `Arrays.fill` bằng `-1`.
10. Nhầm lẫn logic giữa Increasing và Decreasing stack.
11. Bỏ qua việc so sánh 2 phần tử bằng nhau (xử lý sai strict monotonicity).
12. Lặp vô hạn nếu quên `pop()` khi điều kiện while thỏa mãn.
13. Không check `!stack.isEmpty()` trước khi gọi `peek()`.
14. Tính chiều rộng (width) sai trong Histogram (`i - stack.peek() - 1` bị tính thành `i - stack.peek()`).
15. Không xử lý mảng có kích thước bằng 0.
16. Thay đổi trực tiếp mảng input gây side-effect không mong muốn.
17. Dùng `LinkedList` gây giảm hiệu năng.
18. Xử lý Circular Array (Mảng vòng) sai cách (không duyệt đến `2n-1`).
19. Gán giá trị sai cho `result` khi pop.
20. Pop quá đà khiến phá vỡ thông tin cần thiết của phần tử trước đó.

## 15. Edge Cases (30 Trường hợp biên)
1. Mảng rỗng (`[]`).
2. Mảng chỉ có 1 phần tử.
3. Mảng có 2 phần tử tăng dần.
4. Mảng có 2 phần tử giảm dần.
5. Mảng tất cả phần tử bằng nhau (`[2, 2, 2, 2]`).
6. Mảng được sắp xếp tăng dần nghiêm ngặt.
7. Mảng được sắp xếp giảm dần nghiêm ngặt.
8. Mảng xen kẽ (zigzag: `[1, 3, 2, 4, 3]`).
9. Mảng có giá trị âm (`[-1, -5, -3]`).
10. Mảng chứa số 0 (`[0, 0, 0]`).
11. Mảng có số âm lớn nhất (`Integer.MIN_VALUE`).
12. Mảng có số dương lớn nhất (`Integer.MAX_VALUE`).
13. Bắt đầu bằng phần tử lớn nhất.
14. Bắt đầu bằng phần tử nhỏ nhất.
15. Kết thúc bằng phần tử lớn nhất.
16. Kết thúc bằng phần tử nhỏ nhất.
17. Hai phần tử lớn nhất nằm ở giữa.
18. Phần tử lớn nhất bị lặp lại nhiều lần.
19. Khối lượng dữ liệu cực lớn (10^5 phần tử).
20. Phần tử đỉnh núi (một đỉnh duy nhất).
21. Phần tử thung lũng (một đáy duy nhất).
22. Circular array nhưng tất cả bằng nhau.
23. Subarray minimums cho mảng có các dãy con phẳng.
24. Các bước sóng hình sin.
25. Mảng có nhiều hơn hai giá trị lặp lại liên tiếp.
26. Kết quả trả về kích thước mảng vượt quá `Integer.MAX_VALUE` (nếu trả về list).
27. Đỉnh stack cập nhật sai chiều rộng khi pop giá trị cuối cùng ra (Stack rỗng sau pop).
28. Giá trị null ném vào đầu vào (nếu mảng là Object `Integer[]`).
29. Circular array có chu kỳ lẻ.
30. Dùng mảng không đổi `final int[]`.

## 16. Optimization (Tối ưu hóa)
- Khởi tạo kích thước ban đầu của `ArrayDeque` nếu biết trước dung lượng.
- Có thể không cần mảng cho Stack mà trực tiếp cấp phát một mảng phụ làm Stack array thủ công (`int[] stack = new int[n]; int top = -1;`). Kỹ thuật này đánh bại `ArrayDeque` về thời gian chạy do tránh Object Overhead.

## 17. Best Practices (Thực hành tốt nhất)
- Đặt tên biến stack rõ ràng: `increasingStack` hoặc `decreasingStack` hoặc `indexStack` để không bị nhầm.
- Luôn kiểm tra `null` ở đầu hàm.
- Prefer `int[] stack = new int[n];` trong Competitive Programming, dùng `Deque<Integer>` trong Production Code.

## 18. Benchmark (Đo lường hiệu suất)
So với thuật toán ngây thơ (Brute-force $O(N^2)$):
- N = 10,000: O(N) mất ~1ms, O(N^2) mất ~150ms.
- N = 100,000: O(N) mất ~5ms, O(N^2) mất ~15s (Chênh lệch khổng lồ).

## 19. Unit Testing (Kiểm thử đơn vị)
```java
@Test
void testNextGreaterElement() {
    MonotonicStack ms = new MonotonicStack();
    assertArrayEquals(new int[]{4, 2, 4, -1, -1}, ms.nextGreaterElement(new int[]{2, 1, 2, 4, 3}));
    assertArrayEquals(new int[]{-1}, ms.nextGreaterElement(new int[]{1}));
    assertArrayEquals(new int[]{-1, -1, -1}, ms.nextGreaterElement(new int[]{3, 2, 1}));
}
```

## 20. Interview Questions (20 Câu hỏi phỏng vấn)
1. Monotonic Stack là gì và khác gì so với Stack thông thường?
2. Khi nào nên dùng Increasing Stack và khi nào dùng Decreasing Stack?
3. Tại sao độ phức tạp lại là O(N) dù có vòng lặp while lồng trong for?
4. Bạn có thể giải quyết bài Trapping Rain Water bằng Two Pointers thay vì Monotonic Stack không? Ưu nhược điểm?
5. Làm sao để giải bài Next Greater Element cho Circular Array?
6. Bạn nên push value hay index vào Stack? Tại sao?
7. Sự khác nhau giữa `>` và `>=` trong điều kiện của Monotonic Stack?
8. Tại sao JDK Stack không được khuyên dùng?
9. Thuật toán của bài Largest Rectangle in Histogram hoạt động như thế nào?
10. Làm sao để tìm Previous Smaller Element?
11. Giải thích bước tính diện tích khi pop phần tử trong bài Histogram.
12. Có thể triển khai Monotonic Queue (hàng đợi đơn điệu) thay vì Stack được không?
13. Ứng dụng thực tế của Monotonic Stack là gì?
14. Online Stock Span giải như thế nào?
15. Khác biệt giữa bài toán tìm dãy con tăng lớn nhất (LIS) và Monotonic Stack.
16. Code lại Monotonic Stack bằng Array thuần tủy không dùng Collection.
17. Cách xử lý khi các phần tử trùng lặp xuất hiện nhiều lần?
18. Remove Duplicate Letters dùng logic Monotonic Stack như thế nào?
19. Nếu yêu cầu bộ nhớ O(1) thì có dùng được Monotonic Stack không?
20. Sum of Subarray Minimums ứng dụng Monotonic Stack ra sao?

## 21. Practice Problems Link (Liên kết bài tập)
- LeetCode 496: Next Greater Element I
- LeetCode 739: Daily Temperatures
- LeetCode 84: Largest Rectangle in Histogram
- LeetCode 42: Trapping Rain Water

## 22. Pattern Recognition (Nhận diện mẫu)
- Có các từ khóa: "Next Greater", "Next Smaller", "Gần nhất", "Bao nhiêu ngày nữa".
- Yêu cầu tìm các cực trị cục bộ trong mảng với O(N).
- Cần tối ưu thuật toán sử dụng vòng lặp lồng nhau $O(N^2)$ có cấu trúc `for i -> n`, `for j = i+1 -> n`.

## 23. Real Case Study (Nghiên cứu tình huống)
Hệ thống **Stock Trading System** (giao dịch chứng khoán) của công ty X cần cung cấp chỉ báo (indicator) cho hàng triệu trader về chuỗi ngày mà cổ phiếu có giá thấp hơn hiện tại. Sử dụng `Monotonic Stack` được nạp vào Memory giúp giảm tải database và trả lời API request với thời gian dưới 10ms.

## 24. Summary & Checklist (Tóm tắt & Danh sách kiểm tra)
- [x] Hiểu bản chất tăng/giảm đơn điệu.
- [x] Phân biệt rõ khi nào cần push, khi nào cần pop.
- [x] Nắm vững việc thao tác với Index.
- [x] Xử lý tốt giá trị phần tử cuối sau khi mảng đã duyệt xong (Empty stack handle).
- [x] Biết cách tính khoảng cách `i - stack.peek() - 1`.
