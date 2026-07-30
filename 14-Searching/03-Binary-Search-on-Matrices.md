# Binary Search on Matrices (Tìm kiếm nhị phân trên Ma trận)

## 1. Giới thiệu (Introduction)
Binary Search on Matrices là kỹ thuật áp dụng thuật toán tìm kiếm nhị phân (Binary Search) trên mảng 2 chiều (2D arrays/matrices) đã được sắp xếp. Ma trận có thể được sắp xếp theo nhiều cách khác nhau (ví dụ: các hàng liên tiếp nhau tạo thành một mảng 1D đã sắp xếp, hoặc mỗi hàng và mỗi cột đều được sắp xếp độc lập). Kỹ thuật này giúp giảm độ phức tạp tìm kiếm từ $O(M \times N)$ xuống còn $O(\log(M \times N))$ hoặc $O(M \log N)$, thậm chí $O(M + N)$ tùy thuộc vào tính chất sắp xếp của ma trận.

## 2. Nhận diện (How to recognize)
- Bài toán yêu cầu tìm kiếm một phần tử (`target`) hoặc một giá trị thỏa mãn điều kiện trong một ma trận 2D.
- Ma trận có tính chất sắp xếp:
  - Kiểu 1: Hàng sau tiếp nối hàng trước (phần tử đầu hàng sau lớn hơn phần tử cuối hàng trước) và các phần tử trong hàng tăng dần.
  - Kiểu 2: Mỗi hàng và mỗi cột đều được sắp xếp tăng dần.
- Yêu cầu tìm kiếm phần tử nhỏ thứ K (Kth Smallest) hoặc trung vị (Median) trong ma trận sắp xếp theo hàng/cột.
- Độ phức tạp yêu cầu tốt hơn $O(M \times N)$, thường là $O(\log(M \times N))$, $O(M + N)$ hoặc $O(M \log(\text{max} - \text{min}))$.

## 3. Cấu trúc dữ liệu (Data Structures)
- **Matrix / 2D Array**: Mảng hai chiều kích thước $M \times N$ lưu trữ các phần tử.
- **Variables**: Các biến con trỏ `low`, `high`, `mid` để giới hạn phạm vi tìm kiếm (có thể là chỉ số index hoặc khoảng giá trị value range).

## 4. Ý tưởng chính (Main Idea)
Có 3 dạng áp dụng chính:
1. **Treating a 2D matrix as a 1D array**: Nếu ma trận có tính chất hàng sau tiếp nối hàng trước, ta coi nó như một mảng 1D độ dài $M \times N$. Với chỉ số 1D là `mid`, tọa độ 2D tương ứng là `row = mid / cols` và `col = mid % cols`. Sau đó áp dụng Binary Search chuẩn.
2. **Searching in row-wise and column-wise sorted matrices**: Nếu mỗi hàng/cột sắp xếp độc lập, ta không thể dùng Binary Search 1D. Ta bắt đầu từ góc trên cùng bên phải (Top-Right) hoặc dưới cùng bên trái (Bottom-Left). Tại Top-Right, di chuyển sang trái (giảm cột) nếu giá trị hiện tại > `target`, và xuống dưới (tăng hàng) nếu giá trị hiện tại < `target`. Phương pháp này có độ phức tạp $O(M + N)$. Có thể kết hợp Binary Search trên từng hàng/cột để tối ưu tùy trường hợp.
3. **Binary Search over value range (Tìm phần tử thứ K)**: Thay vì tìm trên không gian chỉ số (index), ta tìm trên không gian giá trị (value). Đặt `low = min_val`, `high = max_val`. Tìm `mid`, sau đó đếm số phần tử trong ma trận $\le mid$. Nếu số đếm $< K$, ta tăng `low = mid + 1`. Ngược lại `high = mid`.

## 5. Các bước thực hiện (Steps)
**Dạng 1: Ma trận như mảng 1D**
1. Đặt `low = 0`, `high = rows * cols - 1`.
2. Vòng lặp `while (low <= high)`:
3. Tính `mid = low + (high - low) / 2`.
4. Tìm giá trị tương ứng `val = matrix[mid / cols][mid % cols]`.
5. So sánh `val` với `target` để cập nhật `low` hoặc `high`.

**Dạng 2: Duyệt từ Top-Right (Mỗi hàng/cột đều tăng dần)**
1. Đặt `r = 0, c = cols - 1`.
2. Lặp `while (r < rows && c >= 0)`:
3. Nếu `matrix[r][c] == target`, trả về `true`.
4. Nếu `matrix[r][c] > target`, `c--` (loại bỏ cột).
5. Nếu `matrix[r][c] < target`, `r++` (loại bỏ hàng).

**Dạng 3: Binary Search trên Value Range (Tìm Kth Smallest)**
1. `low = matrix[0][0]`, `high = matrix[rows-1][cols-1]`.
2. Lặp `while (low < high)`:
3. Tính `mid = low + (high - low) / 2`.
4. Đếm số lượng phần tử $\le mid$ (có thể dùng upper_bound hoặc kỹ thuật duyệt tương tự Dạng 2).
5. Nếu đếm $< K$, `low = mid + 1`. Ngược lại `high = mid`.
6. Trả về `low`.

## 6. Phân tích độ phức tạp (Complexity Analysis)
- **Dạng 1 (2D as 1D)**:
  - Thời gian (Time): $O(\log(M \times N))$
  - Không gian (Space): $O(1)$
- **Dạng 2 (Top-Right Pointer)**:
  - Thời gian: $O(M + N)$ (Trường hợp xấu nhất duyệt hết 1 hàng và 1 cột)
  - Không gian: $O(1)$
- **Dạng 3 (Value Range)**:
  - Thời gian: $O((M+N) \log(MAX - MIN))$ hoặc $O(M \log N \log(MAX - MIN))$ tùy cách đếm.
  - Không gian: $O(1)$

## 7. Ưu điểm & Nhược điểm (Pros & Cons)
- **Ưu điểm**:
  - Khai thác tối đa tính chất đã sắp xếp của ma trận để đạt tốc độ vượt trội so với tìm kiếm tuyến tính $O(M \times N)$.
  - Không gian bộ nhớ phụ là $O(1)$, rất hiệu quả (in-place).
- **Nhược điểm**:
  - Dễ gặp lỗi (bug-prone) trong việc tính toán chỉ số (row/col) hoặc thiết lập ranh giới (bounds).
  - Khó khăn trong việc tìm hiểu ý tưởng tìm kiếm trên "không gian giá trị" với người mới.

## 8. So sánh với các thuật toán khác (Comparison)
- **Linear Search 2D**: $O(M \times N)$. Binary search tối ưu hơn đáng kể.
- **Dùng Mảng Phụ (Flatten to 1D Array)**: Chuyển toàn bộ ma trận thành mảng 1D, rồi sort (nếu cần) hoặc trả về Kth element. Tốn $O(M \times N)$ bộ nhớ phụ và thời gian, không được chấp nhận trong phỏng vấn khi cần tối ưu.
- **Priority Queue (Min/Max Heap) cho bài Kth Smallest**: Thời gian $O(K \log M)$ và không gian $O(M)$. Đôi khi heap tốt hơn khi $K$ rất nhỏ, nhưng Binary Search thường vượt trội khi $K$ lớn và không cần thêm bộ nhớ phụ.

## 9. Khi nào nên sử dụng (When to use)
- Dữ liệu đầu vào là ma trận 2D có tính chất sắp xếp một phần hoặc toàn phần.
- Các yêu cầu tìm kiếm phần tử, đếm số lượng phần tử nhỏ hơn mức cho trước, hoặc tìm trung vị/K-th element trên ma trận.

## 10. Biến thể (Variants)
- Tìm một đỉnh (Peak element) trong ma trận (có thể dùng binary search để cắt nửa số cột hoặc số hàng).
- Đếm số phần tử âm trong ma trận được sắp xếp giảm dần (Count negative numbers).
- Tìm kiếm trên ma trận không có giới hạn cố định về giá trị nhưng có sự đơn điệu trong hàm đánh giá.

## 11. Ứng dụng thực tế (Real-world Applications)
- **Computer Graphics / Image Processing**: Tìm điểm sáng, nhận dạng đặc trưng khi một vùng không gian màu được sắp xếp tăng/giảm tuyến tính.
- **Database Query**: Tối ưu hóa các truy vấn tìm kiếm trên dữ liệu dạng bảng có nhiều chỉ mục phức hợp đã được sắp xếp đồng thời theo nhiều tiêu chí.
- **Operations Research**: Tìm điểm tối ưu (Peak/Min/Max) trên lưới tọa độ khi hàm mục tiêu có tính đơn điệu (Monotonicity).

## 12. Các lỗi thường gặp (20 Common Bugs)
1. Dùng `left + right` gây tràn số (Integer Overflow). Luôn dùng `left + (right - left) / 2`.
2. Tính sai chỉ số: `row = mid % cols` thay vì `mid / cols`.
3. Tính sai chỉ số: `col = mid / cols` thay vì `mid % cols`.
4. Nhầm lẫn giữa `rows` và `cols` khi tính kích thước 1D `total = rows * cols`.
5. Đặt ranh giới sai trong vòng while: `while (low < high)` vs `while (low <= high)`.
6. Cập nhật `high = mid - 1` nhưng lại cần bảo toàn giá trị làm kết quả, dẫn tới lỡ mất phần tử.
7. Không kiểm tra trường hợp ma trận rỗng: `matrix.length == 0` hoặc `matrix[0].length == 0`.
8. Bắt đầu Dạng 2 tại Top-Left hoặc Bottom-Right (không thể phân biệt được hướng đi). Bắt buộc phải là Top-Right hoặc Bottom-Left.
9. Quên kiểm tra vượt quá mảng khi duyệt Top-Right (VD: không check `r < rows`).
10. Trong Dạng 3, đếm phần tử bằng hàm đếm tuyến tính $O(M \times N)$ làm mất đi ưu thế của Binary Search.
11. Bỏ qua các trường hợp có phần tử trùng lặp (Duplicates).
12. Cập nhật ranh giới trong Value Range Search: cập nhật sai `low = mid` gây vòng lặp vô hạn (Infinite Loop).
13. Đặt khởi tạo `low = 0` thay vì `min_val` trong Value Range search.
14. Khi tìm kiếm trong bài có chứa số âm, nhầm lẫn việc đếm và sắp xếp.
15. Không xử lý đúng khi `target` nhỏ hơn toàn bộ phần tử trong ma trận.
16. Không xử lý đúng khi `target` lớn hơn toàn bộ phần tử trong ma trận.
17. Sự kiện `row` index ra ngoài (Out of bounds) khi `mid / cols == rows`.
18. Không reset các giá trị pointer sau mỗi lần thử.
19. Tính sai phép tính Kth element khi $K > M \times N$.
20. Trộn lẫn kiểu 1 và kiểu 2 (Ví dụ ma trận chỉ sắp xếp từng hàng và từng cột, nhưng lại cố coi thành 1D array).

## 13. Các trường hợp góc (30 Edge Cases)
1. Ma trận 1x1.
2. Ma trận rỗng (0x0).
3. Ma trận có 1 hàng (1xN).
4. Ma trận có 1 cột (Mx1).
5. Phần tử cần tìm nằm ở vị trí đầu tiên (Top-Left).
6. Phần tử cần tìm nằm ở vị trí cuối cùng (Bottom-Right).
7. Target nhỏ hơn giá trị nhỏ nhất trong ma trận.
8. Target lớn hơn giá trị lớn nhất trong ma trận.
9. Mọi phần tử trong ma trận đều giống nhau (All elements identical).
10. Rất nhiều phần tử trùng lặp.
11. Các giá trị trong ma trận cực lớn (gây tràn số khi cộng).
12. Các giá trị trong ma trận cực nhỏ (Số âm).
13. Ma trận có số hàng lẻ, cột lẻ (Median tính chênh lệch).
14. Ma trận có số hàng chẵn, cột chẵn.
15. $K = 1$ trong bài Kth smallest.
16. $K = M \times N$ trong bài Kth smallest.
17. Giá trị target không tồn tại, nhưng nhỏ hơn max và lớn hơn min.
18. Hai hàng kề nhau nhưng phần tử đầu hàng sau nhỏ hơn phần tử cuối hàng trước (Phá vỡ tính chất 1D).
19. Mảng jagged (mỗi hàng có số cột khác nhau - không thể giải bằng công thức `mid / cols` tiêu chuẩn).
20. $K$ không hợp lệ ($K \le 0$).
21. Truy vấn nhiều target liên tiếp trên cùng một ma trận.
22. Có hàng chứa toàn phần tử trùng lặp với hàng tiếp theo.
23. Chỉ một giá trị duy nhất trong ma trận xen kẽ với vô số giá trị khác.
24. Cột chứa các giá trị âm dần chuyển sang dương (chuyển đổi dấu).
25. Mảng không thay đổi nhưng con trỏ truy cập bị giới hạn cache (cache misses lớn).
26. Median trong ma trận có số phần tử chẵn (thường yêu cầu định nghĩa lại bài toán).
27. Đếm số lượng phần tử tại biên khi target == ma trận max/min.
28. Binary Search bị lệch trái/phải do làm tròn nguyên số âm.
29. Khoảng giá trị (Value range) cực hẹp nhưng kích thước ma trận cực lớn.
30. Khoảng giá trị (Value range) cực rộng, ma trận nhỏ.

## 14. Câu hỏi phỏng vấn (20 Interview Questions)
1. Làm thế nào để áp dụng Binary Search trên một ma trận $M \times N$ mà mỗi hàng sắp xếp tăng dần, và phần tử đầu hàng sau lớn hơn phần tử cuối hàng trước?
2. Sự khác nhau giữa `Search a 2D Matrix I` và `Search a 2D Matrix II` là gì?
3. Tại sao trong Search 2D Matrix II, chúng ta thường bắt đầu tìm kiếm từ góc trên bên phải hoặc dưới bên trái?
4. Đánh giá độ phức tạp thuật toán khi tìm phần tử thứ K trong ma trận sắp xếp theo từng hàng và từng cột.
5. So sánh giải pháp dùng Heap và Binary Search cho bài toán tìm phần tử thứ K trong mảng 2D.
6. Làm thế nào để tìm trung vị (Median) trong một ma trận được sắp xếp theo hàng?
7. Bạn tính tọa độ 2D từ chỉ số 1D như thế nào trong bài Search a 2D Matrix? Giải thích lý do.
8. Nêu các edge cases khi số lượng cột $N = 1$ hoặc số lượng hàng $M = 1$.
9. Nếu ma trận không thể nạp hoàn toàn vào bộ nhớ RAM (bất đối xứng), bạn có cách duyệt nào tối ưu không?
10. Làm sao để đếm số lượng số âm trong ma trận sắp xếp giảm dần cả hai chiều với thời gian $O(M + N)$?
11. Đếm số phần tử nhỏ hơn hoặc bằng một giá trị `X` trong ma trận sắp xếp mất bao lâu?
12. Có thể tìm kiếm nhị phân trên ma trận zic-zac không? Nếu có thì làm thế nào?
13. Giải thích tại sao cập nhật `low = mid + 1` lại tránh được vòng lặp vô hạn trong Binary Search trên khoảng giá trị.
14. Đỉnh (Peak) trong ma trận 2D được định nghĩa như thế nào? Cách dùng Binary Search để tìm.
15. Tại sao không thể dùng chung giải thuật của ma trận mảng 1D (Tiếp nối) cho ma trận chỉ sắp xếp hàng/cột?
16. Nếu tìm ra được target, làm sao để tìm tất cả các vị trí chứa target đó trong ma trận?
17. Khi không có giới hạn ràng buộc (unbounded 2D grid), làm sao để cấp phát Binary Search?
18. Độ phức tạp không gian của các giải thuật Binary Search trên ma trận nói chung là bao nhiêu?
19. Giải thích sự tối ưu bộ đệm (cache optimization) khi duyệt ma trận theo hàng vs theo cột.
20. Hãy mô tả thuật toán tìm kiếm ma trận nếu dữ liệu được lưu trữ phân tán.

## 15. Các Pattern liên quan (Related Patterns)
- **Binary Search on Arrays**: Cơ sở cho mọi biến thể.
- **Binary Search on Answer (Value Range)**: Áp dụng trực tiếp vào các bài Kth Smallest, Median.
- **Two Pointers (Hai con trỏ)**: Kỹ thuật duyệt từ Top-Right đến Bottom-Left có bản chất gần với cấu trúc Two Pointers trên lưới 2D.
- **Top K Elements (Heap / Priority Queue)**: Thường là giải pháp thay thế.
- **Divide and Conquer**: Cách tiếp cận chiết trung để loại bỏ một phần ba / một nửa diện tích khảo sát.

## 16. Kinh nghiệm thực tế (Practical Experience)
- Khi code, luôn bắt đầu bằng check rỗng: `if(matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;`.
- Vẽ ma trận ra nháp với tọa độ (hàng, cột) để test logic chuyển đổi 1D sang 2D, đảm bảo không đảo lộn hàng và cột (đặc biệt khi $M \ne N$).
- Khi tìm Kth element, thuật toán đếm (count elements $\le mid$) là linh hồn. Hãy chắc chắn viết hàm này chạy trong $O(M \log N)$ hoặc $O(M+N)$ mà không có bug.

## 17. Mẹo tối ưu (Optimization Tips)
- Tránh phép chia modulo/chia chẵn nhiều lần nếu không cần thiết. Tuy nhiên, `mid / cols` và `mid % cols` thường đủ nhanh.
- Dùng dịch bit `>> 1` cho các ngôn ngữ bậc thấp để tính `mid` hoặc `low + ((high - low) >> 1)`.
- Trong hàm đếm phần tử $\le mid$, nếu duyệt từ Bottom-Left qua Top-Right, hãy dùng kiểu chạy chéo `O(M+N)` sẽ nhanh hơn gọi `Arrays.binarySearch` trên mỗi hàng `O(M \log N)`.

## 18. Bài tập thực hành (Practice Problems)
- [LeetCode 74] Search a 2D Matrix
- [LeetCode 240] Search a 2D Matrix II
- [LeetCode 378] Kth Smallest Element in a Sorted Matrix
- [LeetCode 1351] Count Negative Numbers in a Sorted Matrix
- [LeetCode 1901] Find a Peak Element II

## 19. Mã nguồn cơ bản (Basic Implementation)

**Dạng 1: Treating 2D as 1D (Java)**
```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0) return false;
    
    int m = matrix.length;
    int n = matrix[0].length;
    
    int low = 0;
    int high = m * n - 1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        int row = mid / n;
        int col = mid % n;
        int val = matrix[row][col];
        
        if (val == target) {
            return true;
        } else if (val < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    
    return false;
}
```

**Dạng 2: Row-wise and Column-wise Sorted (Java)**
```java
public boolean searchMatrixII(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0) return false;
    
    int row = 0;
    int col = matrix[0].length - 1; // Top-Right corner
    
    while (row < matrix.length && col >= 0) {
        if (matrix[row][col] == target) {
            return true;
        } else if (matrix[row][col] > target) {
            col--; // Move left
        } else {
            row++; // Move down
        }
    }
    
    return false;
}
```

## 20. Giải thích mã nguồn (Code Explanation)
- **Dạng 1**: `m` và `n` là kích thước. Chú ý ta chia cho `n` (số cột) vì mỗi hàng chứa `n` phần tử. `mid / n` cho biết `mid` vượt qua bao nhiêu hàng, `mid % n` cho biết phần dư chính là chỉ số cột.
- **Dạng 2**: Ta chọn Top-Right `(0, n - 1)`. Ở đây, toàn bộ các phần tử bên trái cùng hàng sẽ nhỏ hơn, toàn bộ các phần tử bên dưới cùng cột sẽ lớn hơn. Nhờ đó tại mỗi bước ta luôn có quyết định duy nhất: nếu giá trị lớn hơn `target`, ta không thể dịch xuống (vì sẽ càng lớn hơn), buộc phải dịch trái. Nếu nhỏ hơn `target`, ta không thể dịch trái, buộc phải dịch xuống.

## 21. Trực quan hóa (Visualization)
- **Ma trận 1D giả lập**:
  ```
  1  3  5  7
  10 11 16 20
  23 30 34 60
  => 1D: [1, 3, 5, 7, 10, 11, 16, 20, 23, 30, 34, 60]
  ```
- **Ma trận Top-Right**:
  ```
  1  4  7  11 15
  2  5  8  12 19
  ...
  Bắt đầu tại 15. Cần tìm 5.
  15 > 5 -> Cột cuối chứa toàn số > 5 -> Dịch trái sang 11.
  11 > 5 -> Dịch trái sang 7.
  7 > 5 -> Dịch trái sang 4.
  4 < 5 -> Dịch xuống 5. Đã tìm thấy!
  ```

## 22. Bài học rút ra (Key Takeaways)
- Không phải mọi ma trận 2D đều tìm kiếm giống nhau. Phải xác định rõ tính chất sắp xếp: toàn bộ (coi như 1D) hay từng phần (chạy từ góc).
- Khi tìm trung vị (Median) hay phần tử nhỏ thứ K, đừng quên Binary Search trên khoảng giá trị (Value Range) kết hợp với kỹ thuật đếm, không cần không gian bổ sung.
- Hãy thuộc nằm lòng công thức chuyển đổi index: `row = index / cols`, `col = index % cols`.

## 23. Tài liệu tham khảo (References)
- Grokking the Coding Interview (Pattern: Modified Binary Search).
- LeetCode Discuss sections on 2D matrix problems.
- CLRS, Introduction to Algorithms.

## 24. Checklist ôn tập (Review Checklist)
- [ ] Phân biệt được 2 loại sắp xếp trong ma trận 2D.
- [ ] Code không cần suy nghĩ công thức chia `row = mid / cols`, `col = mid % cols`.
- [ ] Nắm được cách duyệt mảng từ Top-Right hoặc Bottom-Left.
- [ ] Cài đặt được tìm Kth Smallest bằng Binary Search over value range + đếm $O(M+N)$.
- [ ] Hiểu rõ ranh giới tràn số và xử lý edge cases rỗng/1D.
