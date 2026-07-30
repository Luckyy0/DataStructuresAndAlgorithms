# 01. Cơ bản về Tìm kiếm nhị phân (Binary Search Basics)

## 1. Giới thiệu (Introduction)
Binary Search (Tìm kiếm nhị phân) là một thuật toán tìm kiếm kinh điển và cực kỳ hiệu quả, hoạt động trên nguyên tắc chia để trị (divide and conquer). Thuật toán này được sử dụng để tìm kiếm một phần tử (`target`) trong một mảng đã được sắp xếp (sorted array).

## 2. Analogy thực tế (Real-world Analogy)
Giống như việc bạn tìm một từ trong một cuốn từ điển. Bạn không lật từng trang từ đầu đến cuối (Linear Search). Thay vào đó, bạn mở cuốn từ điển ra ở giữa. Nếu từ bạn cần tìm nằm ở nửa trước theo thứ tự alphabet, bạn sẽ tiếp tục mở giữa nửa trước đó, bỏ qua hoàn toàn nửa sau. Quá trình này lặp lại cho đến khi bạn tìm thấy từ đó.

## 3. Mô hình trực quan (Visual/Mental Model)
Tưởng tượng mảng là một thanh dài. Bạn xác định điểm đầu (`left`) và điểm cuối (`right`).
- Tìm điểm giữa: `mid = left + (right - left) / 2`.
- Kiểm tra phần tử tại `mid`.
- Nếu bằng `target`, trả về `mid`.
- Nếu nhỏ hơn `target`, dịch `left = mid + 1` (bỏ qua nửa trái).
- Nếu lớn hơn `target`, dịch `right = mid - 1` (bỏ qua nửa phải).

## 4. Đặc điểm cốt lõi (Core Characteristics)
- Yêu cầu tiên quyết: Không gian tìm kiếm phải được sắp xếp (sorted) hoặc có tính đơn điệu (monotonic).
- Không gian tìm kiếm (Search space) giảm đi một nửa sau mỗi lần kiểm tra.
- Là thuật toán tối ưu nhất cho việc tìm kiếm trong tập dữ liệu tĩnh (static data).

## 5. Tính chất và Ràng buộc (Properties & Constraints)
- Yêu cầu mảng cho phép truy cập ngẫu nhiên (random access) trong thời gian O(1) để có thể lấy giá trị tại `mid` (như Array, không áp dụng trực tiếp cho Linked List).
- Dữ liệu phải có tính thứ tự rõ ràng, cho phép so sánh lớn hơn/nhỏ hơn một cách nhất quán.

## 6. Phân loại và Biến thể (Types/Variations)
- **Standard Binary Search**: Tìm phần tử khớp chính xác (`exact match`).
- **Lower Bound (First Occurrence)**: Tìm vị trí xuất hiện đầu tiên của phần tử hoặc phần tử nhỏ nhất lớn hơn hoặc bằng `target`.
- **Upper Bound (Last Occurrence)**: Tìm vị trí xuất hiện cuối cùng của phần tử.
- **Search Insert Position**: Tìm vị trí chèn phần tử vào mảng sao cho vẫn giữ nguyên tính sắp xếp.
- **Binary Search on Answer**: Tìm kiếm nhị phân trên không gian kết quả.

## 7. Các thao tác cơ bản (Basic Operations)
- Khởi tạo hai con trỏ `left` và `right`.
- Vòng lặp `while(left <= right)` hoặc `while(left < right)`.
- Tính `mid` an toàn chống tràn số (overflow).
- Cập nhật ranh giới `left` và `right`.

## 8. Cài đặt từng bước (Step-by-step Implementation)

### Standard Binary Search
```java
public int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2; // Tránh integer overflow
        if (nums[mid] == target) {
            return mid; // Tìm thấy
        } else if (nums[mid] < target) {
            left = mid + 1; // target nằm ở nửa phải
        } else {
            right = mid - 1; // target nằm ở nửa trái
        }
    }
    return -1; // Không tìm thấy
}
```

## 9. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**: **O(log N)**. Ở mỗi bước, số lượng phần tử cần kiểm tra giảm đi một nửa ($N, N/2, N/4, \dots, 1$). Số bước tối đa là $\log_2(N)$.
- **Space Complexity**: **O(1)**. Chỉ sử dụng thêm một số biến nguyên (`left`, `right`, `mid`) cho phiên bản lặp (Iterative). Nếu dùng đệ quy (Recursive), Space Complexity sẽ là **O(log N)** do stack trace.

## 10. Đánh giá ưu nhược điểm (Trade-offs: Pros/Cons)
**Pros:**
- Tốc độ cực nhanh so với Linear Search, đặc biệt với dữ liệu lớn.
- Ít tốn bộ nhớ (O(1) auxiliary space).

**Cons:**
- Bắt buộc dữ liệu phải được sắp xếp trước. Nếu mảng chưa sắp xếp, việc sort tốn O(N log N) time, làm mất lợi thế nếu chỉ tìm kiếm một lần.
- Cần cấu trúc dữ liệu hỗ trợ Random Access (Array, không phải Linked List).

## 11. So sánh (Comparison with alternatives)
- **vs Linear Search**: Binary Search (O(log N)) vượt trội hoàn toàn về tốc độ so với Linear Search (O(N)) trên mảng lớn đã sắp xếp.
- **vs Hash Table**: Hash Table cho O(1) time complexity cho exact match, nhưng Binary Search không tốn O(N) space memory để build Hash Table và Binary Search có thể giải quyết các bài toán tìm `lower bound`, `upper bound` hay range query mà Hash Table không làm được.
- **vs BST (Binary Search Tree)**: BST hỗ trợ chèn và xóa linh hoạt hơn mảng tĩnh, nhưng array cache-friendly hơn và không tốn memory cho pointer.

## 12. Ứng dụng (Applications/Use cases)
- Tìm kiếm trong cơ sở dữ liệu đã được index.
- Tìm điểm chèn (Search Insert Position) trong các thuật toán duy trì thứ tự.
- Gỡ lỗi (Debugging) code bằng cách khoanh vùng lỗi qua Binary Search (như lệnh `git bisect`).
- Các bài toán tìm nghiệm gần đúng của hàm số đơn điệu.

## 13. 30 Edge Cases (Trường hợp ngoại lệ)
1. Mảng rỗng (`nums.length == 0`).
2. Mảng chỉ có 1 phần tử (`target` trùng với phần tử đó).
3. Mảng chỉ có 1 phần tử (`target` không trùng).
4. `target` nhỏ hơn tất cả các phần tử (out of bounds left).
5. `target` lớn hơn tất cả các phần tử (out of bounds right).
6. Mảng có 2 phần tử, `target` là phần tử đầu.
7. Mảng có 2 phần tử, `target` là phần tử cuối.
8. Mảng có tất cả các phần tử giống hệt nhau.
9. `target` có nhiều bản sao (multiple occurrences) trong mảng.
10. `target` không tồn tại, nhưng nằm giữa 2 phần tử kề nhau.
11. `left` và `right` đạt giá trị lớn nhất của Integer (`Integer.MAX_VALUE`), gây tràn số nếu dùng `(left + right) / 2`.
12. Tìm kiếm trong mảng chứa số âm.
13. Tìm kiếm trong mảng toàn số âm.
14. Mảng chứa giá trị `Integer.MIN_VALUE` và `Integer.MAX_VALUE`.
15. `target` là `Integer.MAX_VALUE` hoặc `Integer.MIN_VALUE`.
16. Tìm vị trí `First Occurrence` nhưng mảng toàn các phần tử bằng `target`.
17. Tìm vị trí `Last Occurrence` nhưng mảng toàn các phần tử bằng `target`.
18. Mảng cực lớn (kích thước sát giới hạn bộ nhớ).
19. Mảng có số lượng phần tử chẵn (ảnh hưởng đến cách `mid` làm tròn xuống).
20. Mảng có số lượng phần tử lẻ.
21. Cập nhật `left = mid` hoặc `right = mid` làm vòng lặp vô hạn nếu không xử lý điều kiện lặp đúng.
22. Tìm kiếm Lower Bound khi `target` lớn hơn tất cả phần tử (phải trả về `nums.length`).
23. Cần tìm `target` trong mảng xoay (Rotated Array) mà mảng lại không bị xoay.
24. Mảng xoay nhưng có nhiều phần tử trùng lặp (`nums[left] == nums[mid] == nums[right]`).
25. Mảng có phần tử `null` (trong mảng Object) dẫn đến `NullPointerException`.
26. Mảng chứa chuỗi (String) với các ký tự đặc biệt cần sắp xếp đúng locale.
27. Fractional/Float binary search: sai số dấu phẩy động (cần `epsilon` thay vì `left <= right`).
28. Floating point precision khiến `mid = (left + right) / 2.0` bị kẹt.
29. Khoảng tìm kiếm bị lật ngược (mảng sắp xếp giảm dần thay vì tăng dần).
30. Tìm kiếm với hàm kiểm tra (predicate) luôn trả về `true` hoặc luôn trả về `false`.

## 14. 20 Common Bugs/Pitfalls (Lỗi phổ biến)
1. **Integer Overflow**: Dùng `mid = (left + right) / 2` thay vì `left + (right - left) / 2` hoặc `left + ((right - left) >> 1)`.
2. **Infinite Loop (Kẹt vòng lặp)**: Dùng `while(left <= right)` nhưng cập nhật `left = mid` hoặc `right = mid` thay vì `mid + 1` và `mid - 1`.
3. **Off-by-one Error**: Trả về `left` khi lẽ ra cần trả về `left - 1`.
4. **Điều kiện lặp sai**: Dùng `while(left < right)` nhưng cần xét `left <= right`.
5. **Cập nhật sai phía**: Tìm phần tử đầu tiên (First Occurrence) nhưng lại tiếp tục tìm ở nửa phải khi đã thấy `target` (`left = mid + 1`).
6. **Quên kiểm tra mảng rỗng**: Trực tiếp truy cập `nums[right]` mà quên check mảng rỗng.
7. **Trả về giá trị mặc định sai**: Trả về `0` thay vì `-1` khi không tìm thấy, gây nhầm lẫn với index 0.
8. **Mảng chưa sắp xếp**: Gọi hàm tìm kiếm trên mảng lộn xộn.
9. **So sánh object bằng `==`**: Dùng toán tử `==` thay vì `.compareTo()` cho các kiểu Reference (như String, Integer object trong Java).
10. **Làm tròn `mid` sai**: Khi dùng Binary Search trên số thực (Float/Double), không có `mid + 1` mà dùng `mid = (left + right) / 2`.
11. **Dùng biến toàn cục sai cách**: Các biến `left`, `right` không được reset khi chạy nhiều test case.
12. **Tính toán `mid` bằng phép nhân/chia sai**: `mid = left + (right - left) >> 1` (Thiếu ngoặc, `>>` có độ ưu tiên thấp hơn `+`).
13. **Vượt mảng (Index Out of Bounds)**: Khi kết thúc vòng lặp `while(left < right)`, trả về `left` nhưng chưa kiểm tra `nums[left] == target` và `left` có thể là mảng `.length`.
14. **Tìm Last Occurrence bị vô hạn**: Dùng `mid = left + (right - left) / 2` khi tìm `Last Occurrence`, dẫn đến `left` và `mid` trùng nhau khi chỉ còn 2 phần tử, gây kẹt vòng lặp. Cần dùng `mid = left + (right - left + 1) / 2`.
15. **So sánh sai dấu**: Lộn `nums[mid] < target` thành `nums[mid] > target`.
16. **Nhầm lẫn giữa Index và Value**: Tính `mid` dựa trên giá trị của phần tử thay vì Index của chúng (thường xảy ra với người mới).
17. **Early exit sai chỗ**: Khi tìm kiếm dải (range), return ngay khi tìm thấy `target` làm mất cơ hội tìm `First` hoặc `Last Occurrence`.
18. **Lỗi khi gọi API hệ thống**: Dùng `Arrays.binarySearch()` không hiểu cơ chế trả về `-(insertion point) - 1` của Java khi không tìm thấy.
19. **Bỏ qua phần tử tiềm năng**: Cập nhật `right = mid - 1` khi đang tìm kiếm kết quả tối ưu (Binary search on Answer), vô tình loại bỏ chính `mid` dù `mid` thỏa mãn điều kiện.
20. **Không xét trường hợp `left` và `right` âm**: Rất hiếm nhưng xảy ra nếu không gian tìm kiếm là miền số âm.

## 15. Kỹ thuật tối ưu (Optimization techniques)
- Dùng dịch bit `(right - left) >>> 1` (trong Java, unsigned right shift) thay vì `/ 2` để có vi kiến trúc nhanh hơn.
- Nhận diện sớm nếu phần tử nằm ngoài khoảng bằng cách check `target < nums[0]` hoặc `target > nums[nums.length - 1]`.

## 16. Code Templates & Patterns

### Pattern 1: Tìm chính xác (Exact Match)
Dùng khi chỉ cần tìm một phần tử. Vòng lặp `while (left <= right)`, các nhánh độc lập.

### Pattern 2: Tìm phần tử đầu tiên (Lower Bound / First Occurrence)
```java
public int findFirst(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    int ans = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ans = mid; // Lưu kết quả, nhưng tiếp tục tìm sang bên trái
            right = mid - 1; 
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return ans;
}
```

### Pattern 3: Tìm phần tử cuối cùng (Upper Bound / Last Occurrence)
```java
public int findLast(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    int ans = -1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ans = mid; // Lưu kết quả, nhưng tiếp tục tìm sang bên phải
            left = mid + 1; 
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return ans;
}
```

### Pattern 4: Tìm điểm chèn (Search Insert Position)
```java
public int searchInsert(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return left; // Vị trí thích hợp để chèn phần tử
}
```

## 17. Chiến lược kiểm thử (Testing strategies)
Tạo bộ Test Cases bao trùm:
- Base cases: Array length 0, 1, 2.
- Normal cases: Target ở đầu, giữa, cuối mảng.
- Negative cases: Target không có trong mảng (nhỏ hơn, lớn hơn, ở giữa khoảng).
- Duplicates cases: Mảng có phần tử lặp.

## 18. Chiến lược phỏng vấn & 20 Interview Questions

**Chiến lược phỏng vấn:**
1. Nghe thấy từ "Sorted Array" -> Nghĩ ngay đến Binary Search.
2. Nghe thấy yêu cầu "Time complexity O(log N)" -> Chắc chắn là Binary Search.
3. Chú ý việc mảng có thể chứa bản sao hay không, có xoay (rotated) hay không.
4. Luôn làm rõ với người phỏng vấn về cách xử lý khi không tìm thấy kết quả.

**20 Câu hỏi phỏng vấn phổ biến:**
1. Giải thích Binary Search và viết thuật toán. (Easy)
2. Làm thế nào để tìm vị trí chèn trong mảng sắp xếp? (Easy)
3. Tìm phần tử đầu tiên và cuối cùng của `target` trong mảng. (Medium)
4. Mảng bị dịch vòng (Rotated), làm sao để tìm `target`? (Medium)
5. Tìm phần tử nhỏ nhất trong mảng bị dịch vòng. (Medium)
6. Giải thích lỗi tràn số khi tính `mid` và cách khắc phục. (Theory)
7. Làm thế nào để thực hiện Binary Search trên một mảng dài vô hạn? (Medium)
8. Có mảng 2D (mỗi hàng, cột tăng dần), làm sao tìm phần tử? (Medium)
9. So sánh Iterative và Recursive Binary Search. (Theory)
10. Tại sao Binary Search có độ phức tạp O(log N)? (Theory)
11. Đếm số lần xuất hiện của phần tử `x` trong mảng sắp xếp. (Easy)
12. Tìm số bị thiếu (Missing Number) trong chuỗi tăng dần cấp số cộng. (Easy)
13. Tìm phần tử có giá trị lớn nhất (Peak Element) trong mảng. (Medium)
14. Tính căn bậc hai nguyên của một số không dùng hàm thư viện (`sqrt(x)`). (Easy)
15. Guess Number Higher or Lower. (Easy)
16. Tìm K phần tử gần nhất (Find K Closest Elements). (Medium)
17. Tìm median của 2 mảng sắp xếp. (Hard)
18. Binary search có thể áp dụng cho Linked List không? Tại sao? (Theory)
19. Mảng có nhiều bản sao, tìm vị trí First Occurrence nhưng tối ưu số lần so sánh. (Medium)
20. Hashing vs Binary Search, khi nào chọn cái nào? (Theory)

## 19. Tài nguyên tham khảo (Recommended resources)
- CLRS (Introduction to Algorithms)
- LeetCode Binary Search Study Plan
- GeeksforGeeks: Binary Search Tutorial

## 20. Bài tập thực hành (Practice problems)
- Xem tệp `01-Binary-Search-Basics-Problems.md`

## 21. Cheat Sheet / Summary (Tóm tắt)
- **Mục đích**: Tìm kiếm trong tập dữ liệu sắp xếp.
- **Công thức cốt lõi**: `mid = left + (right - left) / 2`.
- **Cập nhật**:
  - `nums[mid] < target` $\Rightarrow$ `left = mid + 1`.
  - `nums[mid] > target` $\Rightarrow$ `right = mid - 1`.
- **Độ phức tạp**: O(log N) Time, O(1) Space.

## 22. Chủ đề nâng cao (Advanced topics)
- Binary Search trên tập kết quả (Binary Search on Answer).
- Fractional/Continuous Binary Search (với số thực `double`).
- Meta Binary Search / One-Sided Binary Search.
- Binary Lifting.

## 23. FAQ (Câu hỏi thường gặp)
- **Hỏi**: Tại sao phải cộng 1 hoặc trừ 1 khi cập nhật `left` và `right`?
  - **Đáp**: Vì ta đã kiểm tra phần tử `mid` rồi, nên không cần đưa `mid` vào khoảng tìm kiếm tiếp theo. Điều này cũng giúp tránh Infinite Loop.
- **Hỏi**: Khi nào dùng `while(left <= right)` và `while(left < right)`?
  - **Đáp**: `while(left <= right)` dùng cho Exact Match, lúc này không gian tìm kiếm thu hẹp đến tận 1 phần tử. `while(left < right)` hay dùng khi cập nhật `right = mid` thay vì `mid - 1`, thường gặp trong các bài Lower/Upper bound thiết kế theo kiểu khác.

## 24. Kết luận (Conclusion)
Binary Search không chỉ là một thuật toán, mà là một tư duy tiếp cận vấn đề cực kỳ quan trọng. Làm chủ được việc kiểm soát các con trỏ `left`, `right`, `mid` và điều kiện biên (edge cases) sẽ giúp bạn vượt qua rất nhiều vòng phỏng vấn kỹ thuật hóc búa.
