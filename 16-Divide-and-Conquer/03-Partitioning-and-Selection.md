# 03 - Partitioning and Selection (Phân Hoạch và Lựa Chọn)

## 1. Giới thiệu (Introduction)
Phân hoạch (Partitioning) và Lựa chọn (Selection) là các kỹ thuật cơ bản trong khoa học máy tính, đóng vai trò cốt lõi trong nhiều thuật toán như Quick Sort và Quick Select. Partitioning chia một mảng thành các phần dựa trên một phần tử chốt (pivot). Selection là bài toán tìm phần tử lớn thứ k hoặc nhỏ thứ k trong mảng chưa sắp xếp. Thuật toán Quick Select và Median of Medians là các kỹ thuật Divide and Conquer điển hình giải quyết bài toán Selection này với thời gian trung bình $O(N)$ hoặc đảm bảo $O(N)$ trong trường hợp xấu nhất.

## 2. Các khái niệm cơ bản (Core Concepts)
- **Partitioning Logic**:
  - **Lomuto Partition Scheme**: Chọn phần tử cuối làm pivot, duy trì một con trỏ chỉ vị trí cuối cùng của mảng con chứa các phần tử nhỏ hơn pivot. Dễ cài đặt, ít bug nhưng thực hiện nhiều phép swap hơn.
  - **Hoare Partition Scheme**: Sử dụng hai con trỏ di chuyển từ hai đầu mảng lại gần nhau. Thực hiện ít phép swap hơn Lomuto, nhanh hơn trong thực tế, nhưng khó cài đặt chính xác hơn (đặc biệt khi xử lý các phần tử bằng pivot).
  - **Dutch National Flag Partition (3-way Partitioning)**: Chia mảng thành ba phần: nhỏ hơn pivot, bằng pivot, và lớn hơn pivot. Rất hiệu quả cho mảng có nhiều phần tử trùng lặp.
- **Quick Select Algorithm**: Biến thể của Quick Sort dùng để tìm phần tử thứ k. Thay vì đệ quy cả hai phía, nó chỉ đệ quy vào một phía chứa phần tử k, đạt thời gian trung bình $O(N)$.
- **Median of Medians (Deterministic Linear Time Selection)**: Một thuật toán chọn pivot thông minh để đảm bảo Quick Select luôn chia mảng theo tỷ lệ tốt, từ đó đạt độ phức tạp $O(N)$ ngay cả trong trường hợp xấu nhất. Thuật toán chia mảng thành các nhóm 5 phần tử, tìm median của mỗi nhóm, sau đó đệ quy tìm median của các medians để làm pivot.

## 3. Ứng dụng thực tế (Real-world Applications)
- **Database Querying**: Tìm "Top K" records hoặc percentile (ví dụ tìm giá trị trung vị của độ trễ hệ thống P50, P90, P99).
- **Computer Graphics**: Thuật toán Z-buffer, phân tích không gian với K-D Trees, BSP trees.
- **Data Analytics**: Tìm các phần tử thống kê quan trọng, loại bỏ outlier trong dữ liệu.
- **Network Routing**: Quản lý hàng đợi ưu tiên, chọn luồng có băng thông lớn thứ k.

## 4. Các biến thể (Variants)
- **Introselect**: Thuật toán kết hợp Quick Select với Median of Medians hoặc Heap Select. Nó bắt đầu với Quick Select để chạy nhanh trong thực tế, nhưng nếu độ sâu đệ quy vượt quá một ngưỡng, nó chuyển sang Median of Medians để đảm bảo $O(N)$ hoặc Heap Select $O(N \log K)$. Được sử dụng trong hàm `std::nth_element` của C++.
- **Floyd-Rivest Algorithm**: Tối ưu hóa Quick Select bằng cách sử dụng lấy mẫu để chọn pivot sát với vị trí k cần tìm.
- **K-selection on Streams**: Khi dữ liệu không nằm vừa bộ nhớ (streaming data), sử dụng Min/Max Heap để tìm Top K.

## 5. Chi tiết cài đặt (Implementation Details)
### Quick Select
```java
public int quickSelect(int[] nums, int k) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == k) return nums[k];
        else if (pivotIndex < k) left = pivotIndex + 1;
        else right = pivotIndex - 1;
    }
    return -1;
}

private int partition(int[] nums, int left, int right) {
    int pivot = nums[right];
    int i = left;
    for (int j = left; j < right; j++) {
        if (nums[j] <= pivot) {
            swap(nums, i++, j);
        }
    }
    swap(nums, i, right);
    return i;
}
```

## 6. Đánh giá độ phức tạp (Complexity Analysis)
- **Quick Select (Randomized pivot)**:
  - Time: Trung bình $O(N)$, Tốt nhất $O(N)$, Xấu nhất $O(N^2)$ (khi mảng đã sắp xếp và chọn pivot tồi).
  - Space: $O(1)$ nếu viết vòng lặp (iterative), $O(\log N)$ nếu dùng đệ quy.
- **Median of Medians**:
  - Time: Đảm bảo $O(N)$ trong mọi trường hợp (Xấu nhất).
  - Space: $O(\log N)$ call stack.

## 7. So sánh (Comparison)
- **Quick Select** vs **Sorting**: Sorting mất $O(N \log N)$ để tìm phần tử thứ K, trong khi Quick Select chỉ mất trung bình $O(N)$.
- **Quick Select** vs **Min-Heap (size K)**: Heap tốn $O(N \log K)$ thời gian và $O(K)$ không gian. Quick Select tốn $O(N)$ thời gian và $O(1)$ không gian, tốt hơn khi mảng dữ liệu đã có sẵn trong bộ nhớ tĩnh. Tuy nhiên, Heap hợp lý hơn cho Data Stream.

## 8. Ưu điểm và Nhược điểm (Pros & Cons)
**Ưu điểm**:
- Nhanh trong thực tế với các hằng số siêu nhỏ (Quick Select).
- In-place (không dùng bộ nhớ phụ đáng kể).
- $O(N)$ time complexity trung bình (rất tối ưu).

**Nhược điểm**:
- Worst-case time $O(N^2)$ nếu cài đặt không khéo (Quick Select).
- Phải biến đổi mảng gốc (mutates array). Nếu dữ liệu read-only, ta phải clone data mất $O(N)$ space.
- Khó code chính xác (nhất là partition của Hoare).

## 9. Tối ưu hóa (Optimization)
- **Randomized Pivot**: Tránh worst-case cho các input bị phá bĩnh (adversarial input).
- **Median of 3**: Chọn pivot là phần tử giữa trung vị của (đầu, giữa, cuối) mảng.
- **3-way Partitioning**: Để tối ưu khi mảng có rất nhiều phần tử giống nhau (tránh $O(N^2)$).
- **Khử đệ quy**: Dùng vòng lặp (Tail call optimization).

## 10. Patterns thường gặp (Common Patterns)
- "Find K-th (Largest/Smallest/Most Frequent)...": Bài toán tìm Top K.
- "Group elements by...": Phân hoạch dựa trên cờ (Dutch National Flag).
- Cấu trúc "Smaller, Equal, Larger" khi xử lý mảng trùng.

## 11. Các chủ đề liên quan (Related Topics)
- Divide and Conquer
- Sorting (Quick Sort, Merge Sort)
- Heap / Priority Queue (thay thế cho việc lựa chọn)
- Randomized Algorithms

## 12. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Quick Select hoạt động như thế nào? Phân tích Time Complexity.
2. Tại sao Quick Select là Divide and Conquer mặc dù nó chỉ đệ quy một nửa?
3. Mô tả sự khác biệt giữa Lomuto và Hoare Partition.
4. Làm thế nào để giải bài toán K-th Largest Element mà không làm đổi mảng ban đầu?
5. Trình bày thuật toán Median of Medians. Tại sao chia nhóm 5 mà không phải 3?
6. Mảng có tất cả các phần tử giống nhau thì thuật toán Lomuto Partition có độ phức tạp gì?
7. Cài đặt 3-way partitioning (Dutch National Flag) như thế nào?
8. Tại sao Introselect lại tốt hơn Quick Select chuẩn trong thực tế?
9. Sử dụng Heap vs Quick Select để tìm Top K - khi nào dùng gì?
10. Tại sao $O(N \log K)$ của Heap có thể chậm hơn $O(N)$ Quick Select trong thực tế?
11. Trình bày ứng dụng K-D Tree trong việc tìm điểm gần nhất và cách nó dùng partition.
12. Có thể áp dụng Quick Select trên Linked List được không? Nếu có thì độ phức tạp ra sao?
13. Nếu dùng Quick Select để tìm phần tử trung vị của tỷ phú mảng thì có khả thi không?
14. Phân tích worst-case space complexity của Quick Select đệ quy.
15. Randomized Pivot có đảm bảo 100% $O(N)$ không?
16. Code tìm điểm gần $K$ gốc tọa độ nhất sử dụng Quick Select.
17. Phân hoạch Wiggle Sort cần logic Partition gì?
18. Trình bày cách thực hiện Quick Select không dùng đệ quy (Iterative).
19. Giải thích "Median of 3" Pivot.
20. Nhược điểm khi dùng Quick Select trên SSD / File phân tán là gì?

## 13. 20 Lỗi thường gặp (Common Bugs)
1. `IndexOutOfBounds` khi con trỏ di chuyển qua mảng (đặc biệt Hoare).
2. Lặp vô hạn khi mảng có toàn phần tử giống nhau (do không cập nhật con trỏ).
3. Đệ quy vô hạn khi partition trả về index sai hoặc không giảm size mảng con.
4. Lomuto: Không đổi chỗ pivot về đúng vị trí ở bước cuối cùng.
5. Truyền sai giá trị K (Tìm lớn thứ K lại code tìm nhỏ thứ K).
6. Random pivot sinh ra index ngoài khoảng `[left, right]`.
7. `k` sử dụng indexing 1-based, nhưng code mảng dùng 0-based.
8. Hoare: Trả về sai index của pivot khiến mất phần tử khi chia mảng.
9. Quên swap lại pivot xuống cuối mảng khi sử dụng Randomized pivot trong Lomuto.
10. `left` và `right` không hội tụ khi dùng vòng lặp `while(left < right)`.
11. Tràn số integer khi tính điểm giữa: `(left + right) / 2`.
12. Code 3-way partition: Nhầm lẫn điều kiện `>=` thay vì `>`.
13. Truy cập mảng null hoặc rỗng.
14. Trong Dutch National Flag, con trỏ `i` tăng không đúng khi swap với `right`.
15. Quên điều kiện dừng đệ quy.
16. Median of Medians: Không xử lý nhóm phần tử cuối (có thể < 5 phần tử).
17. Median of Medians: Lỗi khi tìm trung vị của medians trên mảng rỗng.
18. Sử dụng Quick Select trên mảng read-only, gây Exception (như mảng const trong C++).
19. Tính sai công thức chuyển đổi từ 2D matrix sang 1D array.
20. Swap sai đối tượng khi làm việc với object (thay vì swap ref).

## 14. 30 Trường hợp đặc biệt (Edge Cases)
1. Mảng rỗng (`length == 0`).
2. Mảng có 1 phần tử.
3. K = 1 (Lớn nhất / Nhỏ nhất).
4. K = N (Lớn nhất / Nhỏ nhất đầu kia).
5. Mảng đã được sắp xếp tăng dần (Tồi tệ cho Quick Select nếu dùng pivot cuối).
6. Mảng đã được sắp xếp giảm dần.
7. Mảng có tất cả các phần tử bằng nhau (Tồi tệ cho Lomuto).
8. K ngoài phạm vi (K < 1 hoặc K > N).
9. Mảng có 2 phần tử (ví dụ `[2, 1]`, K = 1).
10. Mảng chứa số âm và số dương đan xen.
11. Mảng có kích thước rất lớn (Vấn đề Memory/Call stack).
12. Các phần tử lặp lại ngẫu nhiên nhiều lần.
13. Mảng chỉ gồm 2 giá trị lặp lại (Ví dụ: `[1, 2, 1, 1, 2]`).
14. Kích thước mảng là số chẵn (Tìm Median).
15. Kích thước mảng là số lẻ (Tìm Median).
16. Pivot luôn là phần tử nhỏ nhất.
17. Pivot luôn là phần tử lớn nhất.
18. Randomized pivot rơi đúng vào các vị trí xấu nhất.
19. Quick Select với Object array (Phải xử lý `NullPointerException`).
20. Mảng toàn Integer.MAX_VALUE và Integer.MIN_VALUE.
21. Phần tử cần tìm (Top K) đều giống hệt nhau.
22. Hàm random trả về 0 gây chia cho zero nếu module sai.
23. Nhóm 5 trong Median of Medians ở cuối chỉ có 1 phần tử.
24. Cửa sổ `[left, right]` rất nhỏ (như = 3 phần tử).
25. 3-way partition khi mảng không có phần tử bằng pivot.
26. Mảng toàn `0`.
27. Đệ quy quá sâu gây `StackOverflowError` trong worst-case.
28. Median of Medians đệ quy trên mảng con medians trùng.
29. Cấu trúc array biến đổi concurrent (Thread safety issues).
30. Dữ liệu đầu vào ở định dạng List thay vì Array, tốn phí access bằng Index.

## 15. Đặc trưng ngôn ngữ (Language Specifics)
- **C++**: `std::nth_element` triển khai Introselect, tốn $O(N)$ thời gian, hiệu suất cực kì tốt. `std::partition` và `std::stable_partition` cũng có sẵn.
- **Java**: Java không có hàm Quick Select tích hợp. `Arrays.sort()` mất $O(N \log N)$. Dev thường dùng `PriorityQueue` (Min/Max Heap) mất $O(N \log K)$.
- **Python**: Thư viện `heapq` có hàm `nlargest` và `nsmallest` chạy tốt, hoặc sử dụng `list.sort()` do Timsort rất nhanh trong thực tế với dữ liệu partially sorted. Có thuật toán `partition` trong một số module nhưng thường manual implementation.

## 16. Thiết kế hệ thống (System Design)
- Khi tìm kiếm Top K Trends (Twitter trending), dữ liệu quá lớn không thể dùng Quick Select trên một máy.
- Giải pháp: MapReduce. Mỗi máy worker tính Top K của phân mảnh mình quản lý (dùng Heap/Quick Select). Máy Master nhận tập hợp Top K từ các worker và gộp lại để tìm Top K cuối cùng (K-way merge).
- Partitioning cũng là khái niệm then chốt trong Database (Database Partitioning / Sharding) chia dữ liệu theo khoảng (Range Partition) hoặc băm (Hash Partition).

## 17. Kiểm thử (Testing)
- Viết Unit Tests bao phủ các Edge Cases: Mảng rỗng, đã sắp xếp, bằng nhau toàn bộ.
- So sánh kết quả của Quick Select với mảng sau khi gọi hàm Sort chuẩn (`Arrays.sort()`).
- Stress testing với mảng $10^6$ phần tử ngẫu nhiên để đánh giá Runtime.
- Kiểm tra lỗi tràn bộ nhớ (OOM) / Call Stack Limit (StackOverflow).

## 18. Thực hành tốt nhất (Best Practices)
- Luôn sử dụng Randomized Pivot hoặc Median of 3 để tránh worst-case.
- Khử đệ quy bằng Iterative approach nếu mảng quá lớn.
- Dùng 3-way partitioning khi không chắc chắn về lượng duplicate trong mảng.
- Không sửa đổi input array nếu yêu cầu bài toán/hệ thống là immutable, nhớ clone array.

## 19. Các mẫu sai lầm (Anti-patterns)
- Dùng Lomuto Partition ngây thơ trên dữ liệu thực tế (rất dễ dính $O(N^2)$ vì dữ liệu thường có mẫu đặc biệt).
- Lạm dụng Heap thay vì Quick Select khi cần tìm phần tử thứ K và có sẵn toàn bộ dữ liệu trong mảng in-memory.
- Quên validate input `k` (phải đảm bảo $1 \le k \le length$).

## 20. Lịch sử và bối cảnh (History)
- Quick Select được sáng tạo bởi **Tony Hoare** vào năm 1961 (cùng tác giả của Quick Sort).
- Median of Medians (hay còn gọi là thuật toán BFPRT) được đặt tên theo 5 nhà khoa học lừng danh: Blum, Floyd, Pratt, Rivest, và Tarjan (phát minh năm 1973) để chứng minh tồn tại Selection $O(N)$ deterministic.

## 21. Tài nguyên học tập (Recommended Resources)
- Sách "Introduction to Algorithms" (CLRS) - Chương 9: Medians and Order Statistics.
- Bài giảng MIT 6.046J: Divide & Conquer, Quickselect.
- Các bài tập Top K trên LeetCode.

## 22. Bảng tóm tắt (Cheat Sheet)
| Thuật toán | Trung bình Time | Xấu nhất Time | Không gian |
| --- | --- | --- | --- |
| Sort | $O(N \log N)$ | $O(N \log N)$ | $O(1)$ đến $O(N)$ |
| Max/Min Heap | $O(N \log K)$ | $O(N \log K)$ | $O(K)$ |
| Quick Select | $O(N)$ | $O(N^2)$ | $O(1)$ |
| BFPRT (MoM) | $O(N)$ | $O(N)$ | $O(\log N)$ |

## 23. Kết luận (Conclusion)
Partitioning và Selection không chỉ là những bài toán cốt lõi để luyện tập giải thuật mảng, mà còn cung cấp mindset phân chia để trị (Divide and Conquer). Hiểu rõ Quick Select và các chiến lược Partition giúp lập trình viên tự tin làm việc với tập dữ liệu lớn cần trích xuất thông tin đặc thù (Top K, Median) một cách tối ưu nhất.

## 24. Thuật ngữ (Glossary)
- **Pivot**: Chốt, phần tử được chọn để chia mảng.
- **Partition**: Phân hoạch, quá trình chia mảng dựa trên pivot.
- **Selection**: Bài toán chọn lọc (thứ K).
- **In-place**: Thuật toán không sử dụng bộ nhớ phụ lớn ($O(1)$).
- **Deterministic**: Tất định (kết quả và thời gian không phụ thuộc vào Random).
- **Median**: Số trung vị (phần tử đứng giữa khi mảng đã sắp xếp).
