# Quick Sort (Sắp xếp nhanh) & Quick Select

## 1. Giới thiệu (Introduction)
Quick Sort là một thuật toán sắp xếp phân chia và trị (Divide and Conquer) cực kỳ hiệu quả, được phát triển bởi Tony Hoare vào năm 1959. Nó chọn một phần tử làm Pivot (chốt) và phân vùng (Partition) mảng xung quanh pivot này.

## 2. Cấu trúc dữ liệu và khái niệm cơ bản (Data Structures and Basic Concepts)
- **Pivot (Phần tử chốt):** Một phần tử được chọn từ mảng để làm mốc phân chia mảng thành hai phần.
- **Partition (Phân vùng):** Quá trình sắp xếp lại mảng sao cho tất cả các phần tử nhỏ hơn pivot đứng trước pivot, và các phần tử lớn hơn đứng sau pivot.
- **Divide and Conquer (Chia để trị):** Thuật toán chia mảng ban đầu thành hai mảng con dựa vào pivot, sau đó sắp xếp đệ quy hai mảng con đó.

## 3. Các thao tác cơ bản (Basic Operations)
- **Chọn Pivot (Pivot Selection):** Có thể chọn phần tử đầu, cuối, giữa, hoặc ngẫu nhiên (Randomized Pivot), hoặc Median-of-Three.
- **Partition Schemes:**
  - *Lomuto Partition scheme:* Chọn pivot ở cuối, duyệt bằng một con trỏ giữ vị trí của phần tử nhỏ hơn pivot. Đơn giản nhưng dễ rơi vào Worst Case với mảng đã sắp xếp.
  - *Hoare Partition scheme:* Sử dụng hai con trỏ chạy từ hai đầu mảng vào giữa. Thường nhanh hơn Lomuto và thực hiện ít phép swap hơn.
  - *Dutch National Flag scheme (3-way partition):* Phân vùng thành 3 phần: `< pivot`, `== pivot`, `> pivot`. Rất hiệu quả khi mảng có nhiều phần tử trùng lặp.
- **Quick Select:** Một biến thể của Quick Sort dùng để tìm phần tử lớn thứ K (K-th element) trong thời gian trung bình O(N). Thay vì đệ quy cả 2 mảng con, nó chỉ đệ quy vào mảng con chứa phần tử thứ K.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:**
  - *Best Case:* $O(N \log N)$ - Mỗi lần partition chia mảng thành 2 nửa bằng nhau.
  - *Average Case:* $O(N \log N)$
  - *Worst Case:* $O(N^2)$ - Khi pivot luôn là phần tử lớn nhất hoặc nhỏ nhất (ví dụ: dùng mảng đã sắp xếp mà chọn pivot là phần tử cuối/đầu).
- **Space Complexity:** $O(\log N)$ trung bình cho Call Stack đệ quy. Trong Worst Case, độ phức tạp không gian đệ quy lên tới $O(N)$.
- **Tính ổn định (Stability):** Unstable. Quick Sort không giữ nguyên thứ tự ban đầu của các phần tử có giá trị bằng nhau.
- **In-place:** Có, Quick Sort sắp xếp trực tiếp trên mảng mà không cần bộ nhớ phụ (trừ stack đệ quy).

## 5. Cách thức hoạt động (How it works)
1. **Choose Pivot:** Chọn phần tử $P$ từ mảng.
2. **Partitioning:** Đổi chỗ (Swap) các phần tử sao cho tất cả các phần tử $< P$ nằm bên trái, $\ge P$ nằm bên phải.
3. **Recursive calls:** Gọi đệ quy Quick Sort cho hai nửa bên trái và bên phải của mảng (không bao gồm pivot đã nằm đúng vị trí).

## 6. Triển khai mẫu (Sample Implementation)
```java
public class QuickSort {
    public void sort(int[] arr) {
        if (arr == null || arr.length == 0) return;
        quickSort(arr, 0, arr.length - 1);
    }

    private void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pivotIndex = partition(arr, low, high); // Lomuto partition
            quickSort(arr, low, pivotIndex - 1);
            quickSort(arr, pivotIndex + 1, high);
        }
    }

    private int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1; // Chỉ số của phần tử nhỏ hơn
        for (int j = low; j < high; j++) {
            if (arr[j] < pivot) {
                i++;
                swap(arr, i, j);
            }
        }
        swap(arr, i + 1, high);
        return i + 1;
    }

    private void swap(int[] arr, int i, int j) {
        int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }
}
```

## 7. Các biến thể và tối ưu hóa (Variants and Optimizations)
- **Randomized Quick Sort:** Chọn ngẫu nhiên một phần tử làm pivot và đổi chỗ nó với phần tử cuối cùng trước khi partition, giúp giảm khả năng bị O(N^2).
- **Median-of-Three:** Chọn pivot là giá trị trung vị của phần tử đầu, giữa và cuối.
- **Dual-Pivot Quick Sort:** Dùng 2 pivot thay vì 1, được dùng làm mặc định trong `Arrays.sort()` của Java cho primitive types.
- **Tail Call Optimization / Tối ưu độ sâu đệ quy:** Thay vì gọi đệ quy ngây thơ, luôn gọi đệ quy trên mảng con nhỏ hơn trước, và dùng vòng lặp cho mảng con lớn hơn, đảm bảo Max Stack Space luôn là $O(\log N)$.
- **Chuyển sang Insertion Sort cho mảng nhỏ:** Khi kích thước mảng con $\le 10$, Insertion Sort thường nhanh hơn do không có overhead đệ quy.

## 8. Các bài toán ứng dụng phổ biến (Common Applications)
- Sắp xếp dữ liệu nguyên thủy (Primitive sorting) trong các ngôn ngữ lập trình.
- Tìm phần tử K-th Largest / Smallest (Quick Select).
- Phân nhóm dữ liệu như bài toán Cờ Hà Lan (Sort Colors).
- Tính toán top K phần tử phổ biến nhất hoặc gần nhất.

## 9. Các chiến lược giải quyết (Problem-Solving Strategies)
- **Two Pointers:** Kỹ thuật chia vùng phụ thuộc rất nhiều vào 2 con trỏ, thường đi từ 2 đầu (Hoare) hoặc đi cùng chiều (Lomuto).
- **Phân vùng 3 phần (3-way partitioning):** Khi bài toán yêu cầu nhóm các phần tử có cùng tính chất hoặc đối mặt với mảng có nhiều phần tử trùng (Duplicates).
- **Thu hẹp khoảng không gian tìm kiếm (Pruning):** Trong Quick Select, dựa vào vị trí của pivot để bỏ qua cả một nửa mảng mà không cần đệ quy.

## 10. Ưu điểm và nhược điểm (Pros and Cons)
**Ưu điểm:**
- Nhanh trong thực tế (Hệ số hằng số nhỏ), Cache-friendly.
- Sắp xếp In-place, tiết kiệm bộ nhớ so với Merge Sort.
- Dễ dàng tối ưu hóa.

**Nhược điểm:**
- Worst case thời gian lên tới $O(N^2)$.
- Thuật toán sắp xếp không ổn định (Unstable).
- Đệ quy có thể tốn bộ nhớ stack và bị StackOverflow.

## 11. So sánh với các cấu trúc dữ liệu / thuật toán khác (Comparison)
- **So với Merge Sort:** Merge Sort ổn định (Stable) và có Worst case $O(N \log N)$ luôn được đảm bảo, nhưng cần $O(N)$ bộ nhớ phụ trợ (cho mảng). Quick Sort thường nhanh hơn do thao tác trong bộ nhớ liên tiếp thân thiện với Cache (Locality of Reference).
- **So với Heap Sort:** Heap Sort cũng In-place và Worst case $O(N \log N)$, nhưng hằng số lớn hơn và truy cập bộ nhớ nhảy cóc nhiều (ít Cache-friendly). Do đó Quick Sort vẫn nhanh hơn trong thực tế.

## 12. 30 Edge Cases (Các trường hợp đặc biệt)
1. Mảng rỗng (`[]`).
2. Mảng có 1 phần tử.
3. Mảng đã được sắp xếp tăng dần.
4. Mảng đã được sắp xếp giảm dần.
5. Mảng toàn các phần tử giống nhau (có thể kích hoạt $O(N^2)$ với phân vùng Lomuto).
6. Mảng đã sắp xếp một nửa.
7. Mảng lớn với phần lớn dữ liệu trùng lặp.
8. Mảng có K phần tử giống hệt nhau liên tiếp.
9. Pivot là số nhỏ nhất trong mảng.
10. Pivot là số lớn nhất trong mảng.
11. Số chẵn số lượng phần tử.
12. Số lẻ số lượng phần tử.
13. Mảng gồm các giá trị cực lớn và cực nhỏ (gần `Integer.MAX_VALUE`, `Integer.MIN_VALUE`).
14. Mảng có các giá trị âm và dương đan xen.
15. Mảng gồm các đối tượng phức tạp mà thao tác so sánh tốn kém.
16. Gọi Quick Select khi $K \le 0$ hoặc $K > N$.
17. Recursive Stack Overflow trên mảng kích thước cực lớn nếu dùng pivot tồi.
18. Các mảng zig-zag (lớn, nhỏ, lớn, nhỏ).
19. Mảng có số lượng phần tử là luỹ thừa của 2.
20. Mảng có cấu trúc gần giống phân phối chuẩn.
21. Thử thách về sự ổn định (có thể mảng trông vẫn đúng nhưng các object mất thứ tự gốc).
22. Pivot Random vô tình sinh ra thứ tự luôn rơi vào Worst Case.
23. Gặp ConcurrentModificationException nếu áp dụng trên Collections mà không copy.
24. Data skew: đa số thuộc về một bên nhánh partition.
25. Thuật toán Partition để con trỏ chạy quá biên (Index Out of Bounds).
26. Median of 3 lấy sai index (khi size mảng < 3).
27. Đệ quy vô hạn do điều kiện chia nhỏ mảng không đúng (vd: pivot không được loại trừ hoặc phân chia `low..pivot`, `pivot..high` sai trong Hoare).
28. Partitioning mảng Boolean (chỉ có true/false).
29. Tràn số khi tính chỉ số ngẫu nhiên hoặc trung bình.
30. QuickSelect gọi cho phần tử trùng nhau.

## 13. 20 Common Bugs (Các lỗi thường gặp)
1. Index Out of Bounds khi thực hiện 2 con trỏ trong Hoare partition.
2. Vòng lặp vô hạn do không thay đổi con trỏ đúng cách khi gặp các phần tử bằng pivot.
3. Chia đệ quy sai khoảng: đệ quy cả đoạn `[low, high]` gây lặp vô hạn.
4. Đệ quy `[low, pivot]` và `[pivot+1, high]` nhưng trả về sai index cho Lomuto.
5. Không kiểm tra `low < high` trước khi gọi partition.
6. Pivot lựa chọn cứng nhắc là phần tử cuối mà không swap phần tử ngẫu nhiên làm tăng rủi ro $O(N^2)$.
7. Lỗi StackOverflowError vì không giới hạn độ sâu đệ quy hoặc tối ưu đuôi đệ quy (Tail recursion).
8. Tính sai vị trí Median của 3 phần tử.
9. Trong Dutch National Flag: cập nhật sai con trỏ trung gian khiến các số bằng pivot bị dồn nhầm chỗ.
10. Lỗi +1/-1 khi tính toán khoảng phân chia trong Quick Select.
11. Bỏ qua việc hoán đổi giá trị pivot ở cuối phân vùng (Lomuto).
12. Lầm tưởng Quick Sort là stable, sử dụng cho bài toán yêu cầu tính ổn định.
13. So sánh object bằng `==` thay vì `.compareTo()` trong Java.
14. Mảng quá nhỏ vẫn gọi đệ quy thay vì Insertion Sort làm giảm performance.
15. Quên `return` trong trường hợp cơ sở của đệ quy.
16. Dùng `random.nextInt(high - low)` thiếu + 1.
17. Dùng phép chia có thể dẫn đến tràn số `(low + high) / 2` thay vì `low + (high - low) / 2`.
18. Ghi đè trực tiếp lên tham chiếu mảng mà không modify In-place đúng cách.
19. Khởi tạo mảng tĩnh hoặc biến toàn cục trong đệ quy làm hỏng trạng thái đa luồng.
20. So sánh phần tử với pivot nhưng quên kiểm tra giới hạn `i < high`, `j > low`.

## 14. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Hãy mô tả thuật toán Quick Sort và giải thích tại sao nó được gọi là "Quick".
2. Độ phức tạp Worst Case của Quick Sort là gì và cách phòng tránh?
3. Trình bày chi tiết thuật toán Lomuto Partition và Hoare Partition.
4. Phân vùng Cờ Hà Lan (Dutch National Flag) là gì? Tại sao nó hữu ích?
5. Làm thế nào để sửa Quick Sort sao cho có Space Complexity O(log N) kể cả trong Worst Case?
6. Quick Sort có ổn định (Stable) không? Có thể làm nó ổn định được không?
7. Sự khác biệt chính giữa Merge Sort và Quick Sort là gì? Khi nào nên dùng loại nào?
8. Median of 3 là gì?
9. Thuật toán Quick Select hoạt động như thế nào?
10. Dual Pivot Quick Sort hoạt động ra sao và ưu điểm của nó?
11. Hãy viết mã cho Quick Select tìm số lớn thứ K.
12. Có thể triển khai Quick Sort không dùng đệ quy (Iterative) không? Cách làm?
13. Bạn xử lý các phần tử trùng lặp nhiều như thế nào trong Quick Sort?
14. Quick Sort hoạt động trên Linked List như thế nào? Có nên dùng không?
15. Tối ưu hóa Tail Recursion trong Quick Sort thực hiện như thế nào?
16. Randomized Quick Sort hoạt động thế nào và nó ảnh hưởng thế nào đến kỳ vọng thời gian chạy?
17. Tại sao trong Java `Arrays.sort()` dùng Dual-Pivot Quick Sort cho primitive nhưng dùng TimSort cho objects?
18. Tại sao Heap Sort ít được dùng hơn Quick Sort mặc dù có thời gian Worse Case tốt hơn?
19. Giải thích Locality of Reference và tại sao nó giúp Quick Sort nhanh hơn?
20. Trình bày 3 trường hợp mảng có thể khiến Quick Sort thường rơi vào Worst Case.

## 15. Các Pattern thường gặp (Common Patterns)
- **Partitioning Array:** Mọi bài toán yêu cầu chia mảng thành 2 hoặc 3 phần theo một tiêu chí (chẵn/lẻ, âm/dương, >/< X).
- **K-th Element:** Sử dụng ý tưởng Quick Select cho bài toán tìm top K, K-th largest/smallest, trung vị (Median).
- **Two Pointers:** Kỹ thuật thao tác mảng in-place từ 2 đầu hoặc 1 đầu để thu gom các phần tử.

## 16. Mối liên hệ với các chủ đề khác (Relations to other topics)
- **Divide and Conquer:** Tư tưởng cốt lõi của thuật toán.
- **Merge Sort / Heap Sort:** Thường được so sánh về performance và memory.
- **Tree Structures:** Quá trình phân vùng Quick Sort giống với việc xây dựng cây tìm kiếm nhị phân (Binary Search Tree).
- **Selection Algorithms:** Bài toán k-th statistics.
- **IntroSort:** Kết hợp Quick Sort, Heap Sort và Insertion Sort (C++ `std::sort`).

## 17. Tài liệu tham khảo và đọc thêm (References)
- *Introduction to Algorithms (Cormen, Leiserson, Rivest, Stein)* - Chương 7: Quicksort.
- Tài liệu về Cờ Hà Lan (Dutch National Flag problem) của Edsger W. Dijkstra.
- Cấu trúc thư viện chuẩn Java (JDK source code) về `DualPivotQuicksort`.

## 18. Các ví dụ thực tế (Real-world Examples)
- Hệ thống cơ sở dữ liệu sắp xếp truy vấn in-memory với primitive types.
- Tính điểm xếp hạng học sinh, top 100 game thủ toàn cầu (sử dụng Quick Select/Top K).
- Bộ lọc spam phân loại email thành 2 nhóm: có vẻ là spam, không phải spam dựa trên score (Partition).

## 19. Lịch sử và nguồn gốc (History and Origin)
- Phát minh năm 1959 bởi Tony Hoare, khi đó đang học tiếng Nga tại Đại học Moscow, dùng để sắp xếp các từ trong tự điển Nga-Anh nhằm hỗ trợ dịch tự động. Hoare xuất bản thuật toán vào năm 1961.
- Lomuto partition được đặt tên theo Nico Lomuto.

## 20. Các thư viện hỗ trợ (Supported Libraries)
- Java: `Arrays.sort()` (với các kiểu nguyên thuỷ `int`, `long`, `double`...).
- C++: `std::sort` sử dụng IntroSort (Quick Sort làm nền tảng, chuyển sang Heap Sort nếu đệ quy quá sâu).
- Python: `.sort()` dùng TimSort, không phải Quick Sort (do yêu cầu ổn định).

## 21. Lời khuyên cho phỏng vấn (Interview Advice)
- Rất hay gặp câu hỏi yêu cầu **implement Quick Select** (chứ không chỉ Quick Sort), do đó hãy luyện viết hàm partition chuẩn xác.
- Nhớ rõ cách phân vùng 3 phần (Dutch National Flag) vì nó có ứng dụng giải quyết triệt để lỗi khi gặp nhiều dữ liệu trùng lặp.
- Đừng quên đề cập đến **Randomized Pivot** để chứng minh bạn am hiểu về phân tích Worst Case.

## 22. Các lỗi nhận thức thường gặp (Common Misconceptions)
- Sai lầm: "Quick Sort luôn là O(N log N)". Sự thật: Worst Case là O(N^2).
- Sai lầm: "Quick Sort và Merge Sort đều có Space Complexity O(1) hoặc O(N)". Sự thật: Quick Sort In-place nhưng cần bộ nhớ Stack O(log N), Merge Sort cần mảng tạm O(N).
- Sai lầm: "Quick Sort ổn định (Stable)". Sự thật: Nó không ổn định.

## 23. Tương lai và xu hướng (Future and Trends)
- Các thuật toán hiện đại chuyển dần sang lai tạo (Hybrid Sorting): IntroSort (Quick + Heap + Insertion), hoặc TimSort (Merge + Insertion) thay vì sử dụng Quick Sort thuần tuý.
- GPU Sorting và Multithreading song song hoá Quick Sort đang trở nên phổ biến trên dữ liệu lớn.

## 24. Tổng kết (Conclusion)
Quick Sort là "viên ngọc quý" của nhóm thuật toán Chia để trị, nó mang đến hiệu suất xuất sắc nhờ hằng số thời gian nhỏ, ít thao tác bộ nhớ và làm việc hoàn toàn in-place. Mặc dù cần tinh chỉnh để tránh các góc khuất (Worst Case), biến thể của nó như Quick Select và sự áp dụng linh hoạt Partition vẫn luôn là những kiến thức phải có của mọi kỹ sư phần mềm.
