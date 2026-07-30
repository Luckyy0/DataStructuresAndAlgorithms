# Chuyên đề: Binary Search on Rotated Arrays (Tìm kiếm nhị phân trên mảng xoay vòng)

## 1. Giới thiệu (Introduction)
Một mảng xoay vòng (Rotated Array) là mảng đã được sắp xếp nhưng bị dịch chuyển các phần tử một số lần nhất định. Việc áp dụng **Binary Search** trên loại mảng này là một trong những dạng toán phổ biến nhất trong các bài phỏng vấn thuật toán, yêu cầu kỹ năng biến đổi điều kiện tìm kiếm.

## 2. Khái niệm cơ bản (Basic Concepts)
- **Rotated Sorted Array**: Mảng có dạng `[4, 5, 6, 7, 0, 1, 2]`. 
- **Pivot (Điểm xoay)**: Phần tử nhỏ nhất trong mảng, hoặc điểm mà tại đó mảng bị gãy (từ lớn nhất giảm đột ngột xuống nhỏ nhất).
- **Target**: Giá trị cần tìm kiếm.

## 3. Tìm điểm xoay (Finding the Pivot Point / Minimum Element)
Để tìm phần tử nhỏ nhất (pivot), ta dựa vào đặc điểm: nếu `nums[mid] > nums[right]`, điểm xoay chắc chắn nằm ở nửa phải (bỏ qua `mid`). Nếu `nums[mid] <= nums[right]`, điểm xoay nằm ở nửa trái (bao gồm cả `mid`).
- Điều kiện lặp: `while (left < right)`
- Rút gọn không gian: `left = mid + 1` hoặc `right = mid`.

## 4. Xác định nửa đã được sắp xếp (Identifying the Sorted Half)
Khi chia mảng tại `mid`, ít nhất một trong hai nửa (trái hoặc phải) sẽ **luôn luôn được sắp xếp hợp lệ**.
- **Nửa trái được sắp xếp** nếu: `nums[left] <= nums[mid]`
- **Nửa phải được sắp xếp** nếu: `nums[mid] <= nums[right]`
Khi đã xác định được nửa nào đã sắp xếp, ta kiểm tra xem `target` có nằm trong khoảng của nửa đó hay không để quyết định di chuyển `left` hay `right`.

## 5. Xử lý phần tử trùng lặp (Handling Duplicates in Rotated Arrays)
Khi mảng có chứa các phần tử trùng lặp (ví dụ `[3, 1, 2, 3, 3, 3, 3]`), ta có thể gặp tình huống `nums[left] == nums[mid] == nums[right]`. Lúc này, không thể xác định được nửa nào đã được sắp xếp. 
**Cách giải quyết**: Thu hẹp không gian tìm kiếm bằng cách di chuyển cả hai con trỏ `left++` và `right--` (hoặc chỉ `right--` tùy bài toán) cho đến khi chúng khác nhau, sau đó tiếp tục thực hiện Binary Search.

## 6. Độ phức tạp (Time Complexity Nuances - O(N) Worst Case)
- **Mảng không trùng lặp**: $O(\log N)$ do không gian tìm kiếm luôn giảm một nửa sau mỗi bước.
- **Mảng có trùng lặp**: Thời gian trung bình là $O(\log N)$, nhưng **Worst Case là $O(N)$**. Điều này xảy ra khi hầu hết các phần tử đều giống nhau, buộc thuật toán phải thu hẹp mảng bằng cách dịch chuyển `left` hoặc `right` từng bước một ($O(1)$ phần tử bị loại bỏ mỗi lần lặp).

## 7. Cấu trúc dữ liệu (Data Structures)
- **Array**: Array 1D. Không cần cấu trúc dữ liệu phụ trợ phức tạp.

## 8. Mẫu thuật toán (Algorithm Template)
```java
int left = 0, right = nums.length - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) return mid;
    
    if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
        left++; right--; continue; // Xử lý trùng lặp
    }
    
    if (nums[left] <= nums[mid]) { // Nửa trái sắp xếp
        if (target >= nums[left] && target < nums[mid]) right = mid - 1;
        else left = mid + 1;
    } else { // Nửa phải sắp xếp
        if (target > nums[mid] && target <= nums[right]) left = mid + 1;
        else right = mid - 1;
    }
}
return -1;
```

## 9. Các bước triển khai (Implementation Steps)
1. Khởi tạo `left = 0`, `right = nums.length - 1`.
2. Kiểm tra điều kiện `while(left <= right)`.
3. Tính `mid = left + (right - left) / 2`.
4. So sánh `nums[mid]` với `target`.
5. Loại bỏ trùng lặp nếu có.
6. Xác định nửa sắp xếp và cập nhật `left`, `right`.

## 10. Kỹ thuật nâng cao (Advanced Techniques)
- **Binary Search on Answer**: Kết hợp Binary Search trên dải giá trị với logic mảng xoay vòng.
- **Tìm kiếm đa chiều (2D)**: Tương đương mảng xoay vòng trong ma trận xoay.

## 11. Phân tích không gian (Space Complexity)
- **Độ phức tạp không gian**: $O(1)$ do chỉ sử dụng các biến hằng số `left, right, mid`.

## 12. Các lỗi thường gặp (20 Common Bugs)
1. Dùng `while(left < right)` thay vì `while(left <= right)` khi tìm `target`.
2. Gán `left = mid` thay vì `left = mid + 1` gây vòng lặp vô hạn.
3. Tràn số nguyên `(left + right) / 2`.
4. Không xử lý đúng tình huống `nums[left] == nums[mid] == nums[right]`.
5. Bỏ quên dấu bằng: so sánh `nums[left] < nums[mid]` thay vì `<=`.
6. Sai điều kiện kiểm tra target: `target >= nums[left]` mà bỏ quên `target < nums[mid]`.
7. Lỗi lặp vô hạn khi mảng chỉ có 2 phần tử.
8. Truy cập ngoài giới hạn (Array Out of Bounds) khi kiểm tra `mid + 1`.
9. Thuật toán sụp đổ khi mảng không hề bị xoay (rotated at 0).
10. Trả về `left` thay vì `nums[left]` khi được yêu cầu tìm giá trị.
11. Gán `right = nums.length` (ngoài biên).
12. Mặc định các phần tử trong mảng đều duy nhất (unique).
13. Quên cập nhật biến khi trùng lặp (`left++` / `right--`).
14. Trả về `-1` quá sớm bên trong vòng lặp.
15. Tính `mid` sai công thức dẫn tới offset.
16. Nhầm lẫn giữa tìm kiếm vị trí (index) và tìm kiếm giá trị (value).
17. Thực hiện thêm các kiểm tra không cần thiết trong `while`.
18. Thay đổi trực tiếp (mutate) mảng gốc thay vì chỉ đọc.
19. So sánh nhầm `target` với `nums[left]` thay vì `nums[mid]`.
20. Trả về sai Index nếu phần tử trùng lặp được tìm thấy không phải phần tử đầu tiên.

## 13. Các trường hợp biên (30 Edge Cases)
1. Mảng rỗng (Empty array)
2. Mảng chỉ có 1 phần tử
3. Mảng có 2 phần tử đã sắp xếp
4. Mảng có 2 phần tử bị xoay
5. Mảng lớn đã sắp xếp không bị xoay
6. Mảng xoay đúng ở phần tử giữa
7. Mảng xoay ở phần tử đầu tiên
8. Mảng xoay ở phần tử cuối cùng
9. Mảng toàn các phần tử giống nhau (vd: `[2, 2, 2, 2, 2]`)
10. Mảng gần giống nhau, `target` ở đầu
11. Mảng gần giống nhau, `target` ở cuối
12. `Target` chính là phần tử nhỏ nhất (Pivot)
13. `Target` chính là phần tử lớn nhất
14. `Target` không tồn tại, nằm giữa 2 phần tử
15. `Target` nhỏ hơn phần tử nhỏ nhất
16. `Target` lớn hơn phần tử lớn nhất
17. Mảng có chứa số âm
18. Mảng chứa hoàn toàn là số âm
19. Mảng mix giữa số âm và số dương
20. Kích thước mảng là số lẻ
21. Kích thước mảng là số chẵn
22. `left` và `right` trỏ về cùng một giá trị
23. Phần tử trùng lặp xuất hiện ở cả hai đầu
24. Phần tử nhỏ nhất bị trùng lặp
25. Phần tử lớn nhất bị trùng lặp
26. `Target` bị trùng lặp nhiều lần
27. Đỉnh (Peak) nằm ở ngay sát cuối
28. Đỉnh (Peak) nằm ở ngay sát đầu
29. Các phần tử trùng lặp nằm ở giữa mảng nhưng không nằm ở 2 đầu
30. Mảng có kích thước rất lớn chứa các giá trị đạt cực đại của `Integer.MAX_VALUE`.

## 14. Câu hỏi phỏng vấn (20 Interview Questions)
1. Find Minimum in Rotated Sorted Array
2. Find Minimum in Rotated Sorted Array II
3. Search in Rotated Sorted Array
4. Search in Rotated Sorted Array II
5. Find the rotation count of a rotated sorted array
6. Find max element in rotated sorted array
7. Search for a range of a target in rotated sorted array
8. Find rotation point without duplicates
9. Find rotation point with duplicates
10. Check if a given array is a rotated sorted array
11. Find the median of a rotated sorted array
12. Find peak element in a rotated array
13. Find K-th smallest element in a rotated sorted array
14. Search a target in multiple concatenated rotated sorted arrays
15. Find number of times a string is rotated (chuyển qua dạng mảng)
16. Circular array search using binary search
17. Binary search in an array with some elements as null
18. Find rotation direction (left or right)
19. Find the smallest missing positive integer in a rotated array
20. Find the first missing positive in a rotated array using Binary Search properties

## 15. Ứng dụng thực tế (Real-world Applications)
- **Ring Buffers / Circular Queues**: Truy xuất dữ liệu trong hàng đợi xoay vòng nơi dữ liệu cũ bị ghi đè.
- **Data Stream Offsets**: Tìm kiếm mốc thời gian (timestamp) trong các bản ghi log được cuộn lại (log rotation).
- **Time Series Anomalies**: Tìm sự sụt giảm đột ngột hoặc mốc chuyển ngày trong dữ liệu nhiệt độ, chứng khoán (các dữ liệu có tính chu kỳ).

## 16. Debugging
- Hãy luôn theo dõi các biến `left`, `right`, `mid`, `nums[left]`, `nums[mid]`, `nums[right]`.
- Sử dụng các breakpoint tại những chỗ rẽ nhánh (if/else) phân định nửa sắp xếp.
- Chú ý những vòng lặp cuối cùng khi `right - left <= 1`.

## 17. Testing
Viết test case đặc biệt cho các trường hợp: Mảng đã được sắp xếp hoàn toàn, Mảng gồm 2 phần tử, và Mảng chứa phần tử giống hệt nhau ở cả 2 đầu.

## 18. So sánh với các thuật toán khác (Comparisons)
- **Linear Search**: $O(N)$, an toàn cho mọi trường hợp, nhưng chậm với mảng lớn.
- **Binary Search chuẩn**: Không thể dùng trực tiếp, vì mảng không đơn điệu (monotonic) trên toàn bộ phạm vi.

## 19. Mẹo & Thủ thuật (Tips & Tricks)
- Nếu bài toán yêu cầu **tìm Min/Max**, thì điều kiện lặp thường là `while(left < right)` và khi kết thúc, `nums[left]` là kết quả.
- Nếu bài toán yêu cầu **tìm Target**, thì điều kiện lặp thường là `while(left <= right)` và kiểm tra `nums[mid] == target`.

## 20. Hệ thống bài tập (System of Problems)
Tham khảo danh sách 30 bài tập trong file đính kèm. Khởi đầu với bài toán "Find Minimum", sau đó nâng cấp lên "Search Target" và cuối cùng là các bài có phần tử trùng lặp (II).

## 21. Sơ đồ tư duy (Mindmap)
- Rotated Array -> Binary Search
  - Find Pivot -> (mid > right) ? left = mid + 1 : right = mid
  - Search Target -> nums[left] <= nums[mid] ? (Left is sorted) : (Right is sorted)
  - Duplicates -> nums[left] == nums[mid] == nums[right] -> left++, right--

## 22. Câu hỏi thường gặp (FAQ)
**Hỏi**: Tại sao phải dùng `nums[left] <= nums[mid]` mà không phải `nums[left] < nums[mid]`?
**Đáp**: Dấu `=` rất quan trọng khi mảng thu hẹp chỉ còn 2 phần tử. Lúc này `mid` và `left` có thể trùng nhau, việc thiếu `=` sẽ làm sai logic.

## 23. Tài liệu tham khảo (References)
- LeetCode Discussions (Search in Rotated Sorted Array I & II)
- CLRS: Introduction to Algorithms
- GeeksforGeeks: Binary Search Applications

## 24. Tổng kết (Conclusion)
Tìm kiếm nhị phân trên mảng xoay vòng đòi hỏi sự tỉnh táo trong việc xác định nửa mảng có tính đơn điệu (đã sắp xếp). Bằng cách áp dụng chính xác các bước xử lý Pivot, xác định nửa sắp xếp và xử lý phần tử trùng lặp, ta có thể giải quyết dứt điểm toàn bộ họ bài tập này với độ phức tạp tối ưu nhất.
