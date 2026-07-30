# Non-Comparison Sorting (Thuật toán sắp xếp không so sánh)

## 1. Khái niệm cơ bản (Basic Concepts)
Thuật toán sắp xếp không so sánh (Non-Comparison Sorting) là các thuật toán sắp xếp các phần tử không dựa trên việc so sánh trực tiếp các giá trị với nhau (không dùng các phép toán `<`, `>`, `==`). Các thuật toán này thường sử dụng các tính chất toán học, sự phân phối của dữ liệu, hoặc cấu trúc bit để sắp xếp.
Các thuật toán phổ biến bao gồm:
- **Counting Sort (Sắp xếp đếm)**: Đếm tần suất xuất hiện của từng phần tử.
- **Radix Sort (Sắp xếp theo cơ số)**: Sắp xếp theo từng chữ số từ thấp đến cao (LSD) hoặc từ cao xuống thấp (MSD).
- **Bucket Sort (Sắp xếp theo xô)**: Phân phối các phần tử vào các xô (buckets) riêng biệt, sau đó sắp xếp từng xô một.

## 2. Cách thức hoạt động (How it works)
- **Counting Sort**: Dựa trên giả định rằng các dữ liệu đầu vào nằm trong một khoảng hẹp `[0, K]`. Thuật toán tạo một mảng đếm có kích thước `K+1`, đếm số lần xuất hiện của từng giá trị, tính tổng tiền tố (prefix sum) để xác định vị trí cuối cùng của mỗi phần tử trong mảng kết quả.
- **Radix Sort**: Hoạt động bằng cách xử lý từng chữ số của các số. Với cơ số $B$ (thường là 10 hoặc 2), Radix Sort sử dụng một thuật toán sắp xếp ổn định (Stable Sort), thường là Counting Sort, để sắp xếp các số theo từng chữ số từ hàng đơn vị trở lên (LSD).
- **Bucket Sort**: Chia đều mảng giá trị của đầu vào thành một số lượng các "xô". Phân bổ từng phần tử vào xô tương ứng dựa trên giá trị của nó. Sắp xếp các phần tử bên trong từng xô (có thể dùng Insertion Sort), và cuối cùng gộp các xô lại với nhau theo thứ tự.

## 3. Các đặc điểm nhận dạng (Identification characteristics)
- Dữ liệu đầu vào thường là các số nguyên, chuỗi kí tự có độ dài bằng nhau, hoặc các đối tượng có thuộc tính có thể ánh xạ sang số nguyên.
- Dữ liệu có giới hạn trong một khoảng xác định (range `K` không quá lớn đối với Counting Sort).
- Cần sắp xếp trong thời gian tuyến tính $O(N)$.
- Khi có yêu cầu thuật toán sắp xếp ổn định (Stable Sort).

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Counting Sort**:
  - Time Complexity: $O(N + K)$ với $N$ là số lượng phần tử, $K$ là khoảng giá trị.
  - Space Complexity: $O(N + K)$ để lưu mảng đếm và mảng kết quả.
- **Radix Sort**:
  - Time Complexity: $O(d \times (N + K))$ với $d$ là số chữ số (độ dài lớn nhất), $K$ là cơ số (thường là 10).
  - Space Complexity: $O(N + K)$ do sử dụng Counting Sort bên trong.
- **Bucket Sort**:
  - Time Complexity: $O(N + K)$ trung bình, nhưng có thể tệ nhất là $O(N^2)$ nếu tất cả phần tử rơi vào cùng một xô (nếu dùng Insertion Sort để sắp xếp xô).
  - Space Complexity: $O(N + K)$ để lưu trữ các xô.

Tất cả các thuật toán này đều là **Stable Sorts** (khi được triển khai đúng cách).

## 5. So sánh với các cấu trúc dữ liệu / thuật toán khác (Comparison)
- **Với Comparison Sorts (Quick Sort, Merge Sort)**: Các thuật toán Comparison Sort có giới hạn dưới về thời gian là $O(N \log N)$, trong khi Non-Comparison Sort có thể đạt $O(N)$ trong các điều kiện cụ thể. Tuy nhiên, Non-Comparison Sort tốn nhiều bộ nhớ hơn (Space Complexity cao hơn) và bị giới hạn bởi kiểu dữ liệu.
- **Counting Sort vs Radix Sort**: Counting Sort tốt khi range $K$ nhỏ tương đương $N$. Radix Sort tốt hơn khi $K$ lớn nhưng số chữ số $d$ nhỏ.
- **Bucket Sort**: Đặc biệt hiệu quả với các số thực (floating point numbers) phân bố đều đặn trong một khoảng (ví dụ: `[0.0, 1.0)`).

## 6. Các dạng bài tập phổ biến (Common problem types)
- Sắp xếp một mảng khi biết trước các phần tử nằm trong khoảng cố định.
- Tìm sự chênh lệch lớn nhất (Maximum Gap) giữa các phần tử liên tiếp sau khi sắp xếp (Linear Time).
- Sắp xếp các chuỗi theo tần suất xuất hiện của các kí tự.
- Gom nhóm các phần tử có cùng một tính chất cụ thể.
- Đếm số lượng các phần tử nhỏ hơn/lớn hơn trong một mảng.

## 7. Các mẫu code cơ bản (Basic code templates)

**Counting Sort (Java):**
```java
public int[] countingSort(int[] arr) {
    if (arr == null || arr.length <= 1) return arr;
    int max = arr[0], min = arr[0];
    for (int num : arr) {
        max = Math.max(max, num);
        min = Math.min(min, num);
    }
    int range = max - min + 1;
    int[] count = new int[range];
    int[] output = new int[arr.length];
    
    // Store counts
    for (int num : arr) {
        count[num - min]++;
    }
    
    // Prefix sum
    for (int i = 1; i < count.length; i++) {
        count[i] += count[i - 1];
    }
    
    // Build output array (backward for stability)
    for (int i = arr.length - 1; i >= 0; i--) {
        output[count[arr[i] - min] - 1] = arr[i];
        count[arr[i] - min]--;
    }
    return output;
}
```

**Radix Sort (LSD) (Java):**
```java
public void radixSort(int[] arr) {
    if (arr == null || arr.length == 0) return;
    int max = Arrays.stream(arr).max().getAsInt();
    for (int exp = 1; max / exp > 0; exp *= 10) {
        countSortForRadix(arr, exp);
    }
}

private void countSortForRadix(int[] arr, int exp) {
    int n = arr.length;
    int[] output = new int[n];
    int[] count = new int[10];
    
    for (int num : arr) count[(num / exp) % 10]++;
    for (int i = 1; i < 10; i++) count[i] += count[i - 1];
    for (int i = n - 1; i >= 0; i--) {
        output[count[(arr[i] / exp) % 10] - 1] = arr[i];
        count[(arr[i] / exp) % 10]--;
    }
    System.arraycopy(output, 0, arr, 0, n);
}
```

## 8. Các kĩ thuật tối ưu (Optimization techniques)
- **Chuyển đổi số âm**: Trong Radix Sort hoặc Counting Sort, nếu có số âm, có thể cộng thêm một giá trị offset vào tất cả phần tử để đưa về số không âm, sắp xếp, sau đó trừ đi offset đó. Hoặc chia mảng làm hai phần âm và dương rồi xử lý riêng.
- **In-place logic**: Dù khó đạt được in-place hoàn toàn mà vẫn duy trì tính ổn định (stability), có những kĩ thuật giảm thiểu không gian cấp phát bằng cách tái sử dụng mảng đếm.
- **Lựa chọn cơ số (Radix)**: Trong Radix Sort, thay vì dùng cơ số 10, dùng cơ số 256 hoặc lớn hơn (ví dụ phép toán bit `shift`) để giảm số lượng vòng lặp (số lượng chữ số $d$).

## 9. Các lỗi thường gặp (Common Bugs)
1. **Lỗi `ArrayIndexOutOfBoundsException` trong Counting Sort**: Không trừ đi giá trị `min` khi chỉ mục mảng đếm (ví dụ mảng từ 100 đến 105).
2. **Không duy trì tính ổn định (Stability)**: Quên duyệt ngược từ cuối mảng lên đầu khi build mảng kết quả trong Counting Sort.
3. **Lỗi tràn số (Overflow)**: Khi tính $max - min + 1$ trong Java có thể vượt quá `Integer.MAX_VALUE`.
4. **Sai điều kiện kết thúc của Radix Sort**: Điều kiện `max / exp > 0` bị lỗi với số $0$ hoặc tràn số khi $exp$ nhân 10.
5. **Cấp phát bộ nhớ quá lớn (OutOfMemoryError)**: Khi range $K$ của Counting Sort quá lớn (ví dụ $10^9$).
6. **Xử lý số âm sai cách**: Sử dụng modulo hoặc phép chia trên số âm trong Radix Sort mà chưa chuyển đổi.
7. **Bucket kích thước cố định dẫn đến rỗng**: Mảng Bucket Sort không bao phủ hết dải giá trị.
8. **Duyệt mảng Bucket sai thứ tự**: Nối các phần tử của Bucket bị ngược.
9. **Sử dụng thuật toán không ổn định bên trong Radix Sort**: Làm sai hoàn toàn kết quả sắp xếp của Radix.
10. **Chia sai cơ số `exp` trong Radix Sort**: Viết nhầm `num % exp` thay vì `(num / exp) % 10`.
11. **Không cập nhật lại mảng ban đầu**: Quên `System.arraycopy` để chép kết quả về mảng chính trong Radix Sort.
12. **Prefix sum sai**: `count[i] += count[i-1]` bị bỏ sót hoặc tính sai.
13. **Truyền tham chiếu mảng xô sai**: Dùng một đối tượng `ArrayList` duy nhất thay vì tạo mới cho từng xô trong Bucket Sort.
14. **Bỏ quên phần tử có giá trị 0**: Không xem xét $0$ trong Counting Sort khi `min` khác 0.
15. **Lỗi logic khi sắp xếp chuỗi (String Radix Sort)**: Không xử lý được chuỗi độ dài khác nhau.
16. **Sai lầm về Space Complexity**: Nghĩ rằng Radix Sort dùng $O(1)$ space.
17. **Cộng bù (Offset) không nhất quán**: Cộng `offset` vào khi sort nhưng quên trừ ra ở kết quả.
18. **Tạo mảng Bucket kích thước $N+1$ nhưng quên xử lý cực đại**: Công thức ánh xạ vào bucket bị tràn vị trí cuối.
19. **Sắp xếp từng phần tử trong xô sai**: Dùng mảng không co giãn hoặc sắp xếp lỗi.
20. **Không kiểm tra mảng rỗng đầu vào**: Quên điều kiện `arr == null || arr.length == 0`.

## 10. Các trường hợp biên (Edge Cases)
1. Mảng rỗng (`[]`).
2. Mảng chỉ có 1 phần tử.
3. Mảng đã được sắp xếp tăng dần.
4. Mảng đã được sắp xếp giảm dần.
5. Mảng có tất cả phần tử bằng nhau.
6. Mảng chứa số âm và số dương đan xen.
7. Mảng có chỉ chứa số âm.
8. `Integer.MAX_VALUE` và `Integer.MIN_VALUE` (Gây tràn số lượng Range).
9. $K$ rất lớn, $N$ nhỏ (Counting Sort sẽ bị OutOfMemory).
10. Dải giá trị $N$ lớn, $K$ nhỏ (Lợi thế tối đa của Counting Sort).
11. Chuỗi ký tự trống rỗng trong Radix Sort.
12. Các chuỗi ký tự có độ dài không bằng nhau trong MSD Radix Sort.
13. Bucket Sort với số lượng Bucket là 1.
14. Bucket Sort mà tất cả phần tử rơi vào 1 xô duy nhất.
15. Radix Sort với dải số 0.
16. Bucket Sort cho dãy số thực có chứa `NaN` (Cần cẩn thận).
17. Tính Prefix sum trên mảng đếm có toàn giá trị 0.
18. Không tồn tại giá trị `max` khi khởi tạo (do mảng rỗng, cần handle).
19. Counting Sort cho mảng String ký tự Unicode mở rộng.
20. Mảng có phần tử âm lẻ tẻ trong một mảng số dương rất lớn.
21. Radix Sort dùng cơ số hệ 2 (Binary) thay vì 10.
22. Radix Sort dùng cơ số 16 (Hex).
23. Sử dụng Non-Comparison cho dữ liệu có cấu trúc (ví dụ: sinh viên theo điểm).
24. Có nhiều Object giống nhau cần bảo tồn tính Stable (ví dụ: các giao dịch cùng timestamp).
25. Thuật toán bị gọi đệ quy sâu (MSD Radix Sort) với chuỗi cực dài.
26. Mảng có phần tử là kiểu `short` hoặc `byte`.
27. Lỗi làm tròn số thực trong Bucket Sort dẫn đến ánh xạ sai xô.
28. Sắp xếp mảng có nhiều chữ số tận cùng bằng 0.
29. Cấu hình số xô (Buckets) linh hoạt dựa trên `sqrt(N)`.
30. Cấu hình xô (Buckets) dựa trên chênh lệch `max - min / N`.

## 11. 20 câu hỏi phỏng vấn (20 Interview Questions)
1. Hãy giải thích tại sao Counting Sort lại là thuật toán ổn định (Stable)?
2. Làm thế nào để áp dụng Radix Sort cho số âm?
3. Ưu và nhược điểm của Bucket Sort so với Quick Sort là gì?
4. Tại sao người ta lại chọn Counting Sort bên trong Radix Sort mà không chọn Merge Sort?
5. Khi nào Counting Sort thực thi chậm hơn cả $O(N \log N)$ của Merge Sort?
6. Viết mã Counting Sort trong đó duy trì độ phức tạp bộ nhớ thấp nhất có thể.
7. Bạn sẽ sửa đổi Bucket Sort như thế nào nếu dữ liệu đầu vào không phân phối đều?
8. Khác biệt giữa LSD (Least Significant Digit) và MSD (Most Significant Digit) Radix Sort?
9. MSD Radix Sort tốt hơn LSD trong trường hợp nào?
10. Làm sao để giải bài toán Maximum Gap trong mảng không sắp xếp với độ phức tạp tuyến tính?
11. Số lượng bucket tối ưu trong Bucket Sort nên là bao nhiêu?
12. Có thể sử dụng Radix Sort để sắp xếp một mảng các chuỗi (String) không?
13. Phân tích độ phức tạp thời gian khi dùng Radix Sort sắp xếp một mảng chứa chữ số lên tới 32-bit.
14. Counting Sort hoạt động như thế nào trên mảng có rất nhiều phần tử trùng lặp?
15. Hãy thiết kế một hệ thống sắp xếp hàng tỷ lượt log phân tán bằng Bucket Sort.
16. Nếu bạn dùng Hash Map thay vì Array để làm "bucket" hoặc "count array", độ phức tạp sẽ thay đổi ra sao?
17. Radix Sort có phải in-place algorithm không? Tại sao?
18. Có thể dùng Non-Comparison Sort để sắp xếp một chuỗi các số thực (Floating point) không?
19. Giải thích khái niệm "Prefix sum" trong Counting Sort và tác dụng của nó.
20. Mối liên hệ giữa Counting Sort và Bucket Sort?

## 12. Design Patterns liên quan (Related Design Patterns)
- **Strategy Pattern**: Có thể sử dụng Strategy Pattern để đóng gói các thuật toán sắp xếp (Comparison vs Non-Comparison) và linh hoạt chuyển đổi ở runtime tùy thuộc vào Range và Input size.
- **Factory Pattern**: Dùng để khởi tạo các cấu trúc Bucket linh hoạt tùy theo kiểu dữ liệu.

## 13. Ứng dụng thực tế (Real-world Applications)
- **Sắp xếp kí tự ASCII**: Các thuật toán mã hóa, nén dữ liệu như BWT, Suffix Array thường dùng Counting Sort vì số kí tự ASCII rất nhỏ.
- **Routing trong mạng viễn thông**: Sắp xếp các gói tin theo IP (32-bit) dùng Radix Sort rất nhanh.
- **Hệ thống phân tán**: Bucket Sort được áp dụng tự nhiên trong mô hình MapReduce (Map phase chia dữ liệu vào các buckets/shards).

## 14. Hệ thống phân tán và Cloud (Distributed Systems and Cloud)
Trong phân tán (ví dụ Hadoop, Spark), Bucket Sort là hạt nhân của quá trình Shuffle and Sort. Dữ liệu (ví dụ Logs theo khoảng thời gian) được partition gửi về các node theo cơ chế xô (buckets). Sau đó, mỗi node sắp xếp dữ liệu cục bộ.

## 15. Các chủ đề nâng cao (Advanced Topics)
- **Suffix Array Construction**: Sử dụng Radix Sort / Counting Sort để xây dựng Suffix Array trong thời gian $O(N)$.
- **Trie-based Sorting**: Mối liên hệ sâu sắc giữa MSD Radix Sort và cấu trúc dữ liệu Trie.

## 16. Tích hợp với cơ sở dữ liệu (Database Integration)
- Cơ sở dữ liệu dùng Radix/Bucket sort (hoặc Hash) để thực hiện Group By hoặc các phép join phân tán (Hash Join có một số triết lý tương đồng với Bucket Sort phân mảnh).

## 17. Mối quan hệ với AI/ML (Relationship with AI/ML)
- Trong các mô hình K-Nearest Neighbors, hoặc xử lý ngôn ngữ tự nhiên (NLP) khi cần đếm tần suất n-grams, Counting Sort và Bucket Sort cho phép phân loại và xếp hạng top-K từ vựng cực kỳ nhanh chóng.

## 18. Các bài toán NP-Hard liên quan (Related NP-Hard Problems)
Sắp xếp không trực tiếp giải quyết các bài toán NP-Hard, nhưng kĩ thuật nhóm/xô (bucketing/bin packing) có liên quan trực tiếp đến các thuật toán xấp xỉ (Approximation Algorithms) cho bài toán Bin Packing.

## 19. Lịch sử và sự phát triển (History and Evolution)
Radix Sort có nguồn gốc từ rất lâu, liên quan đến các máy lập bảng thẻ đục lỗ (Punched card tabulating machines) do Herman Hollerith phát minh cuối thế kỉ 19.

## 20. Các thư viện phổ biến (Popular Libraries)
Các ngôn ngữ lập trình đa phần sử dụng Timsort hoặc Introsort làm mặc định, nhưng trong các thư viện xử lý String chuyên biệt hoặc Big Data, có các implement của Radix Sort (ví dụ: Google Guava hoặc các hàm trong C++ Boost).

## 21. Công cụ debug và profiler (Debugging and Profiling Tools)
- JProfiler, VisualVM giúp phân tích bộ nhớ cấp phát mảng đếm/xô xem có bị OOM không.
- Breakpoint vào bước xây dựng Prefix Sum để xem liệu vị trí tính đúng hay sai.

## 22. Các tài liệu tham khảo (References)
- "Introduction to Algorithms" (CLRS) - Chương Sắp xếp thời gian tuyến tính.
- "Algorithms" by Robert Sedgewick - Có phân tích chi tiết về String Radix Sort.

## 23. Roadmap học tập (Learning Roadmap)
1. Nắm chắc Array, Prefix Sum, Modulo Math.
2. Học Counting Sort, hiểu rõ tính ổn định (stability).
3. Học LSD Radix Sort với số dương, rồi đến số âm.
4. Học Bucket Sort và thử nghiệm với số thực.
5. Giải các bài toán LeetCode: Maximum Gap, H-Index, Top K Frequent Elements.

## 24. Lời khuyên từ chuyên gia (Expert Advice)
"Trong thực tế, bạn hiếm khi phải tự implement các thuật toán phân lớp phi so sánh từ con số 0. Tuy nhiên, việc nhận ra khi nào có thể loại bỏ nút thắt $O(N \log N)$ bằng cách sử dụng thêm $O(N)$ bộ nhớ qua Counting/Bucket Sort là dấu hiệu của một kỹ sư tối ưu hệ thống giỏi. Đừng lạm dụng nó khi K quá lớn, hãy luôn kiểm tra biên dữ liệu của bạn."
