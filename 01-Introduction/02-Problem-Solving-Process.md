# Metadata
- **Document ID**: DSA-01-02
- **Version**: 1.0
- **Prerequisites**: Cơ bản về lập trình (Basic Programming), DSA-01-01
- **Learning Objectives**: Nắm vững phương pháp luận và quy trình từng bước để tiếp cận, phân tích và giải quyết bất kỳ bài toán thuật toán nào từ đơn giản đến phức tạp.
- **Estimated Reading Time**: 60 phút
- **Difficulty**: Beginner to Intermediate
- **Dependencies**: Không có (None)
- **Keywords**: Problem Solving, Polya, Pseudo-code, Trade-offs, Edge Cases, Test-Driven

---

# 1 Purpose
Mục đích của tài liệu này là cung cấp một khuôn khổ (Framework) tư duy có hệ thống cho kỹ sư phần mềm khi đối mặt với một bài toán mới. Nó giúp chuyển đổi từ trạng thái "không biết bắt đầu từ đâu" sang một kế hoạch hành động rõ ràng và mã nguồn có thể thực thi được.

---

# 2 Motivation
Trong các cuộc phỏng vấn kỹ thuật (Technical Interviews) tại các công ty công nghệ lớn, ứng viên thường thất bại không phải vì họ không biết code, mà vì họ thiếu một quy trình giải quyết vấn đề có cấu trúc. Việc nhảy ngay vào viết mã (Jumping to code) mà không phân tích kỹ thường dẫn đến các giải pháp kém tối ưu, lỗi Logic và không xử lý được các Edge Cases.

---

# 3 Mathematical Foundation
## Logic và Chứng minh toán học
Quy trình giải quyết vấn đề dựa trên nền tảng của tư duy logic:
- **Proof by Contradiction (Chứng minh phản chứng)**: Giả sử một mệnh đề là sai và chứng minh rằng điều đó dẫn đến một mâu thuẫn. Hữu ích trong việc tìm kiếm cận dưới (Lower bounds) của thuật toán.
- **Mathematical Induction (Quy nạp toán học)**: Nếu thuật toán đúng với Input kích thước $k$, và việc giải quyết Input $k+1$ có thể được xây dựng dựa trên Input $k$, thì thuật toán là đúng với mọi $N$. Đây là cơ sở của Dynamic Programming và Recursion.

---

# 4 Core Theory
Chúng ta áp dụng 4 nguyên tắc giải toán của **George Pólya**:
1. **Understand the Problem (Hiểu vấn đề)**:
   - Input là gì? Định dạng, giới hạn (Constraints)?
   - Output mong muốn là gì?
   - Có dữ liệu ẩn (Implicit constraints) không?
2. **Devise a Plan (Lập kế hoạch)**:
   - Tìm kiếm Pattern (Mẫu). Có bài toán nào tương tự không?
   - Brainstorm các cấu trúc dữ liệu tiềm năng.
   - Viết Pseudo-code (Mã giả).
3. **Execute the Plan (Thực thi)**:
   - Chuyển Pseudo-code thành Java.
   - Xử lý các Edge Cases ngay từ đầu.
4. **Review and Extend (Đánh giá và mở rộng)**:
   - Dry-run với một vài Test cases.
   - Phân tích Time/Space Complexity. Có thể tối ưu hơn không?

---

# 5 Visual Explanation

```mermaid
graph TD
    A[Nhận bài toán] --> B[Clarify Requirements]
    B --> C[Xác định Constraints]
    C --> D[Nghĩ ra Brute Force]
    D --> E[Tối ưu hóa: O(N^2) -> O(N)]
    E --> F[Viết Mã giả / Pseudo-code]
    F --> G[Code bằng Java]
    G --> H[Test với Edge Cases]
    H --> I[Review Complexity]
```

---

# 6 Java Implementation
Hãy thử áp dụng quy trình trên với bài toán kinh điển: **Two Sum**.
*Cho mảng số nguyên và một mục tiêu `target`. Tìm chỉ số của 2 số có tổng bằng `target`.*

```java
import java.util.HashMap;
import java.util.Map;

public class ProblemSolving {
    
    /**
     * Thuật toán Two Sum sử dụng Hash Map để đạt O(N) Time Complexity.
     */
    public int[] twoSum(int[] nums, int target) {
        // Validation (Bảo vệ thuật toán)
        if (nums == null || nums.length < 2) {
            throw new IllegalArgumentException("Input must contain at least 2 elements");
        }
        
        // Khởi tạo HashMap để lưu trữ <Giá trị phần tử, Chỉ số>
        Map<Integer, Integer> complementMap = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int current = nums[i];
            int complement = target - current;
            
            // Nếu đã thấy phần bù trước đó, chúng ta có đáp án
            if (complementMap.containsKey(complement)) {
                return new int[] { complementMap.get(complement), i };
            }
            
            // Nếu không, lưu phần tử hiện tại vào Map
            complementMap.put(current, i);
        }
        
        throw new IllegalArgumentException("No two sum solution found");
    }
}
```

---

# 7 Step-by-Step Execution
Thực thi `twoSum([2, 7, 11, 15], 9)`:
1. **Khởi tạo**: `complementMap` rỗng.
2. **Loop i=0**: `current` = 2, `complement` = 7. `Map` không chứa 7. Put (2, 0) vào `Map`.
3. **Loop i=1**: `current` = 7, `complement` = 2. `Map` có chứa 2 (tại index 0). Trả về `[0, 1]`.
*(Thuật toán kết thúc ngay lập tức, tiết kiệm tài nguyên).*

---

# 8 Complexity Analysis
- **Worst-case Time Complexity**: $\mathcal{O}(N)$. Mỗi phần tử được duyệt đúng 1 lần. Thao tác tìm kiếm trong Hash Map mất $\mathcal{O}(1)$ trung bình.
- **Average-case Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$ do sử dụng `HashMap` lưu trữ tối đa $N$ phần tử.
- **Memory Overhead**: Mỗi Entry trong `HashMap` tạo thêm Object Allocation overhead (Memory), lớn hơn nhiều so với việc duyệt mảng thuần túy.

---

# 9 JVM Analysis
Trong phương pháp O(N) ở trên, chúng ta sử dụng `HashMap<Integer, Integer>`.
- **Autoboxing/Unboxing**: Các biến `int` nguyên thủy bị ép kiểu tự động thành `Integer` object khi lưu vào Hash Map. Điều này gây tốn Heap Memory và kích hoạt Garbage Collector (GC) liên tục nếu mảng quá lớn (ví dụ: 10 triệu phần tử).
- Nếu cần High Performance tuyệt đối, việc dùng mảng nguyên thủy sau khi Sort (Two Pointers, $\mathcal{O}(N \log N)$ Time, $\mathcal{O}(1)$ Space) có thể thân thiện với Cache Line của CPU hơn và tránh GC.

---

# 10 OpenJDK Analysis
OpenJDK xử lý ngoại lệ `IllegalArgumentException` bằng cách pop stack frames. Việc lạm dụng Exceptions cho luồng kiểm soát (Control Flow) là một Anti-pattern vì khởi tạo Exception yêu cầu JVM phải chụp (Capture) toàn bộ Call Stack (`Throwable.fillInStackTrace()`), gây tổn hao tài nguyên cực kỳ lớn.

---

# 11 Production Usage
- Quy trình giải quyết vấn đề này được chuẩn hóa tại **Amazon** thành mô hình "Working Backwards".
- Khi giải quyết các ticket (Incidents) ở Production, kỹ sư áp dụng Root Cause Analysis (phân tích nguyên nhân gốc rễ), tương tự như bước "Understand the Problem" trước khi thay đổi bất kỳ dòng code nào.

---

# 12 Design Decisions
Khi giải quyết "Two Sum":
1. **Brute Force (Lồng 2 vòng lặp)**: Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(1)$. Dễ hiểu, nhưng không thể scale.
2. **Sort + Two Pointers**: Time $\mathcal{O}(N \log N)$, Space $\mathcal{O}(1)$. Hữu ích nếu Memory bị hạn chế.
3. **Hash Map**: Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. Phù hợp với bài toán chung nhất, trade-off Memory lấy Speed.

---

# 13 Common Bugs
20 lỗi phổ biến trong quy trình Problem Solving:
1. Nhảy vào viết code khi chưa hiểu rõ bài toán.
2. Không hỏi về các giới hạn của dữ liệu (Data constraints).
3. Không xử lý mảng đầu vào bị `null`.
4. Không xử lý mảng đầu vào bị rỗng.
5. Giả định sai về kiểu dữ liệu trả về (ví dụ, bài toán yêu cầu mảng các giá trị nhưng lại trả về mảng các chỉ số).
6. Sử dụng biến toàn cục (Global variables) gây lỗi khi chạy song song.
7. Quên khởi tạo cấu trúc dữ liệu trước khi dùng.
8. Đặt sai điều kiện dừng (Base case) của vòng lặp.
9. Truy xuất `nums[nums.length]` dẫn đến `ArrayIndexOutOfBoundsException`.
10. Biến bị tràn số (Integer Overflow) khi Input quá lớn.
11. Bỏ qua trường hợp Input có chứa số âm.
12. Quên return kết quả tại điểm kết thúc hàm.
13. Dùng sai toán tử `==` để so sánh chuỗi thay vì `.equals()`.
14. Không xử lý Duplicate elements khi bài toán yêu cầu kết quả duy nhất.
15. Không làm sạch (Clear) trạng thái sau mỗi test case nếu dùng biến Static.
16. Code quá phức tạp thay vì chia nhỏ thành các hàm Helper.
17. Dùng tên biến tối nghĩa như `a`, `b`, `c`.
18. Không tối ưu hóa những phép tính lặp lại (Recomputing the same state).
19. Thao tác trên Object ban đầu thay vì tạo bản sao (Mutating parameters).
20. Hardcode đáp án để vượt qua Test case.

---

# 14 Edge Cases
Phải bao phủ 30 Edge Cases sau khi giải toán:
1. Input là `null`.
2. Input có độ dài 0.
3. Input chỉ có 1 phần tử.
4. Input cực lớn (Maximum capacity).
5. Input chứa số âm.
6. Input chỉ chứa toàn số 0.
7. Tất cả phần tử trong Input đều giống nhau.
8. Input đã được sắp xếp tăng dần.
9. Input đã được sắp xếp giảm dần.
10. Bài toán yêu cầu Multiple outputs nhưng Input không có giải pháp.
11. Bài toán yêu cầu Multiple outputs và Input có rất nhiều giải pháp hợp lệ.
12. Kết quả phép toán vượt quá $2^{31}-1$.
13. Kết quả phép toán nhỏ hơn $-2^{31}$.
14. Input có ký tự khoảng trắng (Space) hoặc Tab ở đầu và cuối (nếu là String).
15. Input chứa ký tự đặc biệt không mong muốn.
16. Hai phần tử thỏa mãn điều kiện ở vị trí liền kề nhau.
17. Hai phần tử thỏa mãn nằm ở vị trí cách xa nhau nhất (đầu và cuối).
18. Input chứa số thực (Floating point) thay vì số nguyên.
19. Mảng có số lượng phần tử chẵn.
20. Mảng có số lượng phần tử lẻ.
21. Input là một đồ thị bị ngắt kết nối.
22. Input là một ma trận bị lệch (Không phải N x N).
23. Tham số đầu vào vượt quá giới hạn của đệ quy (Gây StackOverflow).
24. File Input quá lớn để có thể lưu vừa trong RAM.
25. Mạng bị ngắt trong lúc hàm đang thực thi (Nếu phụ thuộc API).
26. Kết quả có dạng tuần hoàn vô hạn (Ví dụ: `1/3`).
27. Đồ thị có chứa chu trình âm.
28. Input vi phạm Logic nghiệp vụ nhưng không vi phạm kiểu dữ liệu.
29. Lỗi liên quan đến sự phân biệt chữ hoa, chữ thường.
30. Lỗi Unicode khi so sánh chuỗi đa ngôn ngữ.

---

# 15 Optimization Techniques
- **Space Optimization**: Thay đổi Input in-place nếu đề bài cho phép, thay vì cấp phát Memory mới.
- **Time Optimization**: Caching các kết quả đã tính bằng Memoization.

---

# 16 Best Practices
- Giao tiếp liên tục với người phỏng vấn trong quá trình lên kế hoạch.
- Viết mã sạch, tuân thủ DRY (Don't Repeat Yourself).
- Tách riêng biệt logic kiểm tra Input hợp lệ lên đầu hàm (Guard Clauses).

---

# 17 Benchmark
JMH Benchmark minh họa so sánh Brute Force ($\mathcal{O}(N^2)$) và Hash Map ($\mathcal{O}(N)$) trên mảng 10,000 phần tử. Mặc dù Hash Map có Overhead, ở quy mô này Hash Map sẽ chiến thắng áp đảo.

---

# 18 Unit Testing
Ví dụ kiểm tra Two Sum bằng JUnit 5:
```java
@Test
public void testTwoSumNormal() {
    int[] result = problem.twoSum(new int[]{2, 7, 11, 15}, 9);
    assertArrayEquals(new int[]{0, 1}, result);
}
@Test
public void testTwoSumException() {
    assertThrows(IllegalArgumentException.class, () -> {
        problem.twoSum(new int[]{1, 2}, 10);
    });
}
```

---

# 19 Interview Questions
20 câu hỏi về kỹ năng Problem Solving:

**Easy**
1. Giải thích quy trình 4 bước của Polya.
2. Tại sao việc đặt câu hỏi (Clarification) lại quan trọng trước khi code?
3. Trade-off là gì?
4. Đọc hiểu một đoạn mã giả (Pseudo-code) đơn giản.
5. Brute-force có phải luôn luôn là một cách tiếp cận tồi không? (Không, nó là bước đệm tốt).

**Medium**
6. Bạn xử lý thế nào khi quên mất một thuật toán cụ thể trong buổi phỏng vấn?
7. Làm sao để phát hiện ra khi một bài toán cần dùng Hash Map?
8. Kể tên một tình huống bạn phải hy sinh Time để cứu Space Memory.
9. Phương pháp "Dry-run" hoạt động như thế nào?
10. Tại sao Guard Clauses lại tốt hơn các khối lệnh IF lồng nhau?
11. Giải thích chi phí Memory của một Hash Map.
12. Khi nào nên áp dụng Two Pointers thay vì Hash Map trong bài toán tìm cặp?
13. Bạn xử lý các Edge Cases liên quan đến tràn số nguyên (Overflow) ra sao?
14. Phân tích nguyên nhân gây ra `NullPointerException`.
15. Sự khác biệt giữa kiểm tra điều kiện `null` và mảng rỗng `length == 0`.

**Hard & Senior**
16. Làm thế nào để giải quyết một hệ thống yêu cầu Throughput lớn mà thuật toán cốt lõi là $\mathcal{O}(N^2)$?
17. Giải thích khái niệm "Working Backwards" trong thiết kế hệ thống và thuật toán.
18. Nếu bạn phải thiết kế một API mã nguồn mở cho Two Sum, bạn sẽ thiết kế Interface như thế nào?
19. Giải thích tác động của Autoboxing lên Garbage Collector khi sử dụng Collection Framework.
20. Bạn sẽ review code thuật toán của đồng nghiệp như thế nào để đảm bảo tính đúng đắn và hiệu năng?

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán thực hành kèm giải pháp chi tiết tại: [02-Problem-Solving-Process-Problems.md](02-Problem-Solving-Process-Problems.md).

---

# 21 Pattern Recognition
Khi nhận một bài toán mới:
- Nếu thấy **"Tìm tất cả các tổ hợp"** -> Backtracking.
- Nếu thấy **"Tìm đường ngắn nhất trên lưới không trọng số"** -> BFS.
- Nếu thấy **"Tìm lớn nhất/nhỏ nhất với ràng buộc"** -> Dynamic Programming.
- Nếu thấy **"Dữ liệu đã sắp xếp"** -> Binary Search / Two Pointers.

---

# 22 Real Case Study
**Production Incident**: Hệ thống xử lý file CSV của ngân hàng bị treo cứng. Kỹ sư trước đó không lường trước được Edge Case là File có chứa ký tự phân cách (comma) nằm bên trong dấu ngoặc kép (ví dụ: `"Nguyen, Van A"`). Do thiếu bước "Understand the Problem" liên quan đến tiêu chuẩn của file CSV (RFC 4180), thuật toán split chuỗi cơ bản đã tạo ra kết quả sai lệch, dẫn đến sai sót dữ liệu giao dịch hàng tỷ đồng.

---

# 23 Summary
Quy trình Problem Solving có cấu trúc giúp kỹ sư lập trình không bị hoảng loạn. Bằng cách hiểu rõ bài toán, vạch ra các Edge Cases, viết mã giả, phân tích Complexity trước khi thực thi, mã nguồn được tạo ra sẽ có chất lượng Production-ready ngay từ lần viết đầu tiên.

---

# 24 Checklist
- [ ] Tôi không bao giờ viết code trước khi hỏi rõ yêu cầu.
- [ ] Tôi luôn viết mã giả hoặc chia sẻ thuật toán với người phỏng vấn trước.
- [ ] Tôi chủ động liệt kê các Edge Cases trước khi implement.
- [ ] Tôi luôn tự kiểm tra (Dry-run) mã nguồn bằng tay trước khi chạy thử.
- [ ] Tôi hiểu rõ Trade-offs về Time và Space Complexity của giải pháp do tôi chọn.
