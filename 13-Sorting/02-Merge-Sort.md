# Merge Sort (Sắp xếp trộn)

## 1. Giới thiệu (Introduction)
Merge Sort là một trong những thuật toán sắp xếp kinh điển và hiệu quả nhất, dựa trên tư tưởng Divide and Conquer (Chia để trị). Thuật toán này liên tục chia mảng dữ liệu ra làm các nửa bằng nhau cho đến khi mỗi mảng con chỉ còn 1 phần tử (mảng có 1 phần tử được xem là đã sắp xếp), sau đó trộn (merge) các mảng con lại với nhau theo thứ tự để tạo ra mảng đã sắp xếp.

## 2. Divide and Conquer Paradigm (Chia để trị)
Thuật toán Merge Sort áp dụng triệt để mô hình Divide and Conquer gồm 3 bước:
- **Divide (Chia):** Chia mảng dữ liệu ban đầu thành 2 nửa (nửa trái và nửa phải).
- **Conquer (Trị):** Gọi đệ quy hàm Merge Sort cho cả hai nửa để sắp xếp chúng.
- **Combine (Kết hợp):** Gộp (Merge) hai nửa đã được sắp xếp lại với nhau thành một mảng duy nhất có thứ tự.

## 3. Nguyên lý hoạt động (Working Principle)
Tại mỗi bước gộp (Merge), chúng ta có hai mảng con đã được sắp xếp. Ta sử dụng hai con trỏ, mỗi con trỏ duyệt qua một mảng con. So sánh phần tử tại hai con trỏ, phần tử nhỏ hơn sẽ được đưa vào một mảng tạm (temp array) và tịnh tiến con trỏ tương ứng. Quá trình lặp lại cho đến khi duyệt hết cả hai mảng con, tạo ra một mảng đã sắp xếp hoàn toàn.

## 4. Merge Sort Algorithm (Top-Down)
Phương pháp Top-Down thực hiện theo mô hình đệ quy từ trên xuống dưới.
- Hàm `mergeSort(arr, left, right)` kiểm tra điều kiện cơ sở: nếu `left >= right` thì dừng.
- Tính `mid = left + (right - left) / 2`.
- Gọi đệ quy `mergeSort` cho đoạn từ `left` đến `mid` và từ `mid + 1` đến `right`.
- Sau cùng gọi hàm `merge(arr, left, mid, right)` để gộp.

## 5. Merge Sort Algorithm (Bottom-Up)
Phương pháp Bottom-Up lặp tuyến tính (iterative) từ dưới lên trên.
- Ban đầu coi mảng gồm N mảng con, mỗi mảng kích thước 1.
- Duyệt qua các mảng con, gộp các mảng con kích thước 1 thành mảng con kích thước 2.
- Tiếp tục tăng kích thước gấp đôi (2 lên 4, 4 lên 8...) cho đến khi kích thước vượt quá hoặc bằng độ dài mảng.

## 6. Độ phức tạp thời gian (Time Complexity)
- **Best Case:** O(N log N). Quá trình chia diễn ra liên tục mất O(log N) cấp, mỗi cấp việc gộp mất O(N).
- **Average Case:** O(N log N). 
- **Worst Case:** O(N log N).
Hiệu năng thời gian rất ổn định trong mọi trường hợp do thuật toán luôn chia đôi mảng dữ liệu.

## 7. Độ phức tạp không gian (Space Complexity)
- **Space Complexity:** O(N). Quá trình Merge cần một mảng phụ trợ (temp array) có kích thước tương đương mảng ban đầu để lưu tạm dữ liệu trước khi copy trả lại mảng chính.
- *Lưu ý:* Nếu sử dụng Merge Sort cho Linked List (Danh sách liên kết), ta có thể cập nhật con trỏ mà không cần tạo cấu trúc dữ liệu mới, khi đó Space Complexity có thể giảm xuống O(1) hoặc O(log N) cho call stack.

## 8. Tính ổn định (Stability)
Merge Sort là một **Stable Sort** (Thuật toán sắp xếp ổn định). Nếu hai phần tử có cùng giá trị, thứ tự của chúng trong mảng sau khi sắp xếp sẽ giống y hệt như mảng ban đầu. Điều kiện gộp `if (leftArr[i] <= rightArr[j])` là yếu tố cốt lõi giữ lại tính ổn định này.

## 9. Counting Inversions bằng Merge Sort
Merge Sort là thuật toán tối ưu nhất để giải bài toán Đếm nghịch thế (Counting Inversions) trong thời gian O(N log N).
Một Inversion (nghịch thế) xảy ra khi `i < j` nhưng `arr[i] > arr[j]`.
- Trong quá trình gộp 2 nửa (nửa trái và nửa phải đã sắp xếp): Nếu phần tử ở mảng bên phải (vị trí `j`) nhỏ hơn phần tử ở mảng bên trái (vị trí `i`), tức là nó cũng nhỏ hơn tất cả các phần tử còn lại của mảng bên trái. Suy ra, số lượng Inversions sẽ cộng thêm bằng số phần tử còn lại của nửa trái (`mid - i + 1`).

## 10. Các biến thể (Variants)
- **In-place Merge Sort:** Tối ưu hóa không gian xuống O(1) nhưng tăng độ phức tạp thời gian lên một chút (thường O(N log^2 N)) và khó cài đặt hơn.
- **K-way Merge Sort:** Chia mảng thành K phần thay vì 2, thường dùng trong External Sorting.
- **Timsort:** Thuật toán lai (hybrid) kết hợp Merge Sort và Insertion Sort, được tối ưu cho các bộ dữ liệu có sẵn một phần đã sắp xếp. Được dùng rộng rãi trong Python và Java.

## 11. Cài đặt cơ bản - Top Down (Basic Implementation)

```java
public class MergeSort {
    public void sort(int[] arr) {
        if (arr == null || arr.length <= 1) return;
        int[] temp = new int[arr.length];
        mergeSort(arr, temp, 0, arr.length - 1);
    }
    
    private void mergeSort(int[] arr, int[] temp, int left, int right) {
        if (left >= right) return;
        
        int mid = left + (right - left) / 2;
        mergeSort(arr, temp, left, mid);
        mergeSort(arr, temp, mid + 1, right);
        merge(arr, temp, left, mid, right);
    }
    
    private void merge(int[] arr, int[] temp, int left, int mid, int right) {
        for (int i = left; i <= right; i++) {
            temp[i] = arr[i];
        }
        
        int i = left;
        int j = mid + 1;
        int k = left;
        
        while (i <= mid && j <= right) {
            if (temp[i] <= temp[j]) {
                arr[k++] = temp[i++];
            } else {
                arr[k++] = temp[j++];
            }
        }
        
        while (i <= mid) {
            arr[k++] = temp[i++];
        }
    }
}
```

## 12. Cài đặt cơ bản - Bottom Up (Iterative Implementation)

```java
public class BottomUpMergeSort {
    public void sort(int[] arr) {
        if (arr == null || arr.length <= 1) return;
        int n = arr.length;
        int[] temp = new int[n];
        
        for (int size = 1; size < n; size *= 2) {
            for (int left = 0; left < n - size; left += 2 * size) {
                int mid = left + size - 1;
                int right = Math.min(left + 2 * size - 1, n - 1);
                merge(arr, temp, left, mid, right);
            }
        }
    }
    
    private void merge(int[] arr, int[] temp, int left, int mid, int right) {
        // Cùng hàm merge như ở phần 11
        for (int i = left; i <= right; i++) temp[i] = arr[i];
        int i = left, j = mid + 1, k = left;
        while (i <= mid && j <= right) {
            if (temp[i] <= temp[j]) arr[k++] = temp[i++];
            else arr[k++] = temp[j++];
        }
        while (i <= mid) arr[k++] = temp[i++];
    }
}
```

## 13. So sánh với các cấu trúc/thuật toán khác
- **So với Quick Sort:** Quick Sort có chi phí hằng số nhỏ hơn, cache-friendly hơn và là In-place sort, tuy nhiên bị O(N^2) trong worst-case. Merge Sort ổn định thời gian O(N log N) bất chấp đầu vào nhưng tốn thêm O(N) bộ nhớ.
- **So với Heap Sort:** Heap Sort đạt O(N log N) time và O(1) space, nhưng Heap Sort không Stable và thường chậm hơn Quick Sort hay Merge Sort ở thế giới thực vì CPU cache miss cao (do nhảy bước index lớn).

## 14. Ứng dụng thực tế (Real-world Applications)
- Sắp xếp các danh sách liên kết (Linked List).
- Dùng làm gốc trong thư viện `Collections.sort()` trên Object (Timsort).
- **External Sorting:** Xử lý file dữ liệu khổng lồ (vài Terabyte) bằng cách đưa từng block vào RAM, sắp xếp, ghi ra file tạm, sau đó trộn (Merge) các file lại.

## 15. 30 Edge Cases (Các trường hợp biên)
1. Mảng rỗng (`[]`).
2. Mảng có 1 phần tử (`[5]`).
3. Mảng đã được sắp xếp tăng dần.
4. Mảng đã được sắp xếp giảm dần.
5. Mảng có tất cả các phần tử giống nhau (`[2, 2, 2, 2]`).
6. Mảng có kích thước rất lớn.
7. Mảng chứa số âm.
8. Mảng chứa xen kẽ âm, dương và số 0.
9. Mảng có số lẻ phần tử.
10. Mảng có số chẵn phần tử.
11. Mảng chứa giá trị cực đại/cực tiểu (Integer.MAX_VALUE, Integer.MIN_VALUE).
12. Các phần tử bị trùng lặp ngẫu nhiên, xuất hiện theo cụm.
13. Kiểu mảng có giá trị kiểu số thực và chứa giá trị NaN.
14. Mảng chứa đối tượng, có các phần tử bị Null (Cần custom comparator).
15. Kích thước mảng đúng bằng một lũy thừa của 2.
16. Kích thước mảng là một số nguyên tố.
17. Nửa trái của mảng lớn hoàn toàn so với nửa phải.
18. Nửa phải của mảng lớn hoàn toàn so với nửa trái (Có thể bỏ qua merge để tối ưu).
19. Trộn mảng mà nửa trái cạn sớm.
20. Trộn mảng mà nửa phải cạn sớm.
21. Mảng gần như đã sắp xếp, chỉ sai lệch 1 vài vị trí.
22. Mảng chỉ bao gồm hai loại giá trị xếp xen kẽ (Ví dụ: `[1, 2, 1, 2, 1, 2]`).
23. Đệ quy với các mảng gây tràn Stack nếu không tối ưu.
24. Quá trình tính Inversions vượt qua giới hạn `Integer.MAX_VALUE`, cần kiểu `long`.
25. Thuật toán gọi trên biến thể mảng của đối tượng String.
26. Gọi đệ quy nhưng gặp Linked List đứt gãy hoặc có chu trình vòng.
27. Đếm khoảng cách từ biến thể của mảng mà mảng con rỗng.
28. Thao tác trên Subarray thay vì thay đổi toàn mảng.
29. Gọi Bottom-up khi mảng có size = MAX_ARRAY_SIZE có thể làm tràn biến `size`.
30. Race conditions khi chạy Merge Sort đa luồng (Parallel Merge Sort).

## 16. 20 Common Bugs (Lỗi thường gặp)
1. Lỗi Off-by-one khi chia `mid` (ví dụ `(left + right) / 2` thay vì `left + (right - left) / 2` để tránh tràn số).
2. Viết sai điều kiện dừng trong đệ quy (thường là quên `left >= right`).
3. Khởi tạo mảng `temp` bên trong hàm đệ quy `mergeSort` dẫn tới sinh ra quá nhiều mảng con gây tốn O(N log N) không gian và chi phí GC.
4. Lỗi IndexOutOfBounds do vòng lặp copy mảng tạm.
5. Quên copy nốt các phần tử còn thừa của nửa trái (hoặc nửa phải) vào mảng chính.
6. Sai điều kiện <= (Ví dụ dùng `<` thay vì `<=`) làm mất đi tính Stable (ổn định) của thuật toán.
7. Gọi đệ quy với `(left, mid - 1)` thay vì `(left, mid)`.
8. Quên không khởi tạo mảng `temp`.
9. Cập nhật nhầm index cho mảng phụ và mảng gốc trong vòng lặp While.
10. Ghi đè (Overwrite) lên phần tử của mảng khi không sử dụng mảng phụ.
11. Bỏ qua bước kiểm tra con trỏ cạn kiệt trong khi merge.
12. Vòng lặp Bottom-up tính sai cận trên cho `right` (`Math.min(left + 2 * size - 1, n - 1)`).
13. Hàm trộn cập nhật trực tiếp vào array mà không có bước chuẩn bị.
14. Không sử dụng Long cho các bài toán Counting Inversions của số liệu lớn.
15. Không quản lý đúng tham chiếu cho `temp` và `arr` dẫn tới sao chép sai vị trí.
16. Thao tác so sánh của mảng String dùng nhầm `==` thay vì `compareTo`.
17. Để vòng lặp của mảng tạm copy toàn bộ kích thước N thay vì chỉ từ `left` tới `right`.
18. Trả về mảng rỗng do thao tác swap nhầm logic.
19. Mất đồng bộ hóa trong Parallel Merge Sort.
20. So sánh con trỏ `i < j` thay vì giá trị `arr[i] < arr[j]`.

## 17. 20 Interview Questions (Câu hỏi phỏng vấn)
1. Giải thích hoạt động của Merge Sort? Time and Space complexity của nó là gì?
2. Tại sao Merge Sort là thuật toán Stable? 
3. Phân biệt Merge Sort và Quick Sort. Dùng loại nào cho Linked List và loại nào cho Array?
4. Làm cách nào để tránh tràn bộ nhớ Stack (StackOverflow) trong đệ quy Merge Sort?
5. External Sorting là gì? Vai trò của thuật toán K-Way Merge Sort?
6. Bạn có thể sử dụng Merge Sort để đếm số lượng Inversions trong mảng không?
7. In-place Merge Sort hoạt động như thế nào?
8. Tại sao Timsort lại sử dụng Insertion Sort thay vì Merge Sort tiếp khi mảng con có kích thước nhỏ?
9. Bạn sẽ thiết kế giải pháp phân tán (Distributed System) để sắp xếp 1 Petabyte dữ liệu như thế nào bằng Merge Sort?
10. Làm sao để tối ưu quá trình gộp nếu mảng đã được sắp xếp sẵn? (Gợi ý: Kiểm tra `arr[mid] <= arr[mid+1]`).
11. So sánh Top-Down và Bottom-Up Merge Sort.
12. Thuật toán `Arrays.sort()` trong Java đối với các Object dựa trên thuật toán nào? Tại sao không dùng Quick Sort?
13. Merge Sort có phải là lựa chọn tốt cho bộ dữ liệu có bộ nhớ RAM hạn chế (Ví dụ Embedded Systems)?
14. Phân tích CPU Cache-miss của Merge Sort so với Quick Sort.
15. Việc cấp phát mảng tạm nhiều lần trong mỗi đệ quy sẽ gây hậu quả gì?
16. Viết mã nguồn của hàm Merge mà không xài bất kỳ không gian mảng tạm nào đối với Linked List.
17. Time complexity của Merge Sort thay đổi ra sao nếu tôi chia mảng làm 3 đoạn thay vì 2?
18. Viết Merge Sort đa luồng (Multi-threading).
19. Trong trường hợp có nhiều phần tử lặp lại, thuật toán có hoạt động tốt không?
20. Khác biệt giữa `Stable Sort` và `Unstable Sort` khi hiển thị kết quả truy vấn Database là gì?

## 18. Tối ưu hiệu suất (Performance Tuning)
- Tối ưu 1: Nếu kích thước mảng con dưới mức Threshold (ví dụ 7 hoặc 15), sử dụng Insertion Sort sẽ mang lại hiệu suất tốt hơn do hằng số ẩn thấp.
- Tối ưu 2: Kiểm tra `if (arr[mid] <= arr[mid+1]) return;`. Nếu hai nửa đã được sắp xếp, bỏ qua bước Merge, giảm thiểu thao tác mảng.
- Tối ưu 3: Thay vì copy từ mảng này sang mảng kia liên tục, dùng hai mảng (original và auxiliary) luân phiên gán vai trò để tiết kiệm thời gian copy dữ liệu.

## 19. Các mẫu thiết kế (Design Patterns liên quan)
Tư tưởng Divide and Conquer là một pattern lớn. Bạn cũng có thể áp dụng Strategy Pattern để đóng gói các loại Merge Sort khác nhau vào interface chuẩn cho việc sử dụng động.

## 20. Trực quan hóa (Visualization)
Tree Representation của một mảng 8 phần tử:
```text
           [38, 27, 43, 3, 9, 82, 10, 19]
          /                              \
   [38, 27, 43, 3]                  [9, 82, 10, 19]
    /           \                    /             \
[38, 27]      [43, 3]            [9, 82]        [10, 19]
 /     \       /     \            /    \         /      \
[38]  [27]   [43]   [3]         [9]   [82]     [10]    [19]
 \     /       \     /            \    /         \      /
[27, 38]      [3, 43]            [9, 82]        [10, 19]
    \           /                    \             /
   [3, 27, 38, 43]                  [9, 10, 19, 82]
          \                              /
           [3, 9, 10, 19, 27, 38, 43, 82]
```

## 21. Các chủ đề liên quan (Related Topics)
- Timsort, Quick Sort, External Sorting.
- K-Way Merge Algorithm (Priority Queue/Heap).
- Binary Search for finding splitting points.

## 22. Mã nguồn mở / Thực tế (Open Source Examples)
- Python's `sort()` method (Timsort).
- `java.util.TimSort` for sorting reference types in Java.

## 23. Test Cases mẫu
- Input: `[12, 11, 13, 5, 6, 7]`. Output: `[5, 6, 7, 11, 12, 13]`
- Input: `[]`. Output: `[]`
- Input: `[5, 5, 5, 5]`. Output: `[5, 5, 5, 5]`
- Input: `[1, 2, 3, 4, 5]`. Output: `[1, 2, 3, 4, 5]`

## 24. Tổng kết (Conclusion)
Merge Sort là một cột mốc bắt buộc phải nắm trong Computer Science. Sự ổn định thời gian và tính Stable đã giúp nó là trụ cột trong các ngôn ngữ lập trình cho nhu cầu phân tách và sắp xếp Object. Mặc dù bộ nhớ tốn kém hơn, sự kết hợp linh hoạt (như Timsort) đã che lấp khuyết điểm này xuất sắc.
