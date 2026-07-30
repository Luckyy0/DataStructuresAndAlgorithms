# Advanced Divide and Conquer

## 1. Giới thiệu (Introduction)
Trong khoa học máy tính, **Divide and Conquer** (Chia để trị) là một mô hình thuật toán quan trọng dựa trên việc chia đệ quy một bài toán thành hai hoặc nhiều bài toán con cùng loại (hoặc có liên quan), cho đến khi các bài toán con trở nên đủ đơn giản để có thể giải quyết trực tiếp. Ở mức độ nâng cao (Advanced), Divide and Conquer kết hợp với các kỹ thuật như Sweep Line, Geometry, hoặc Memoization để giải quyết các bài toán phức tạp.

## 2. Cấu trúc và Nguyên lý của Advanced Divide and Conquer
Nguyên lý cốt lõi vẫn bao gồm 3 bước:
1. **Divide**: Chia bài toán thành các tập con nhỏ hơn. Thường không chia ngẫu nhiên mà chia theo đặc tính hình học, giá trị hoặc tọa độ.
2. **Conquer**: Giải quyết các bài toán con.
3. **Merge**: Kết hợp kết quả (phần khó nhất trong Advanced Divide and Conquer).

## 3. Bài toán The Skyline Problem
The Skyline Problem yêu cầu chúng ta tìm đường viền (skyline) của một thành phố khi nhìn từ xa, cho trước một mảng các tòa nhà (với tọa độ trái, phải và chiều cao).
- **Logic**: Chia tập hợp các tòa nhà thành hai nửa. Tìm skyline cho từng nửa (Conquer). Sau đó kết hợp (Merge) hai skyline lại bằng cách duyệt qua cả hai với con trỏ (Two Pointers), giữ lại độ cao cao nhất tại các điểm thay đổi.
- **Merge step**: Đòi hỏi theo dõi chiều cao hiện tại của cả hai skyline.

## 4. Bài toán Beautiful Array
Một mảng là Beautiful Array nếu với mọi `i < j`, không tồn tại `k` (với `i < k < j`) sao cho `A[k] * 2 = A[i] + A[j]`.
- **Logic**: Sử dụng thuộc tính tuyến tính. Nếu mảng `A` đẹp, thì `A * 2` cũng đẹp, `A * 2 - 1` cũng đẹp. Có thể chia mảng thành phần tử lẻ (bên trái) và phần tử chẵn (bên phải). Sự kết hợp giữa hai phần (trái là lẻ, phải là chẵn) sẽ đảm bảo không có phần tử ở giữa `k` thỏa mãn điều kiện `A[k] * 2 = A[i] + A[j]` với `i` chẵn và `j` lẻ.

## 5. Thuật toán Closest Pair of Points (O(N log N))
Tìm cặp điểm gần nhau nhất trong một tập hợp các điểm trên mặt phẳng 2D.
- **Logic**: Sắp xếp các điểm theo tọa độ X. Chia đôi danh sách điểm bằng một đường thẳng đứng. Tìm khoảng cách nhỏ nhất ở bên trái (`dL`) và bên phải (`dR`). Gọi `d = min(dL, dR)`.
- **Merge**: Kiểm tra các điểm nằm trong dải `[mid_x - d, mid_x + d]`. Sắp xếp các điểm này theo tọa độ Y và so sánh mỗi điểm với tối đa 7 điểm tiếp theo.

## 6. Bài toán Different Ways to Add Parentheses
Cho một biểu thức toán học dưới dạng chuỗi, tìm tất cả các kết quả có thể có khi thêm dấu ngoặc đơn theo những cách khác nhau.
- **Logic**: Duyệt qua chuỗi, với mỗi toán tử `+`, `-`, `*`, chia chuỗi thành hai phần bên trái và bên phải của toán tử. Giải quyết đệ quy cho cả hai phần, sau đó kết hợp mọi kết quả từ bên trái và bên phải với toán tử tương ứng.

## 7. Ứng dụng thực tế (Real-world Applications)
1. **Computer Graphics**: Bài toán Skyline ứng dụng trong kết xuất đồ họa (rendering) và hidden surface removal.
2. **Computational Geometry**: Closest Pair of Points ứng dụng trong Collision Detection, tìm kiếm trong bản đồ.
3. **Compiler Design**: Phân tích cú pháp (Parsing) sử dụng các logic chia biểu thức (như Adding Parentheses).
4. **Signal Processing**: Fast Fourier Transform (FFT) là đỉnh cao của Divide and Conquer.

## 8. Khi nào nên sử dụng
- Khi bài toán có thể chia thành các tập nhỏ không giao nhau và lời giải của chúng có thể kết hợp nhanh chóng.
- Khi cần tối ưu hóa từ $O(N^2)$ xuống $O(N \log N)$ đối với các bài toán quét hoặc mảng.
- Khi bài toán liên quan đến hình học hoặc có tính đệ quy cấu trúc.

## 9. Khi nào không nên sử dụng
- Khi các bài toán con chồng chéo lên nhau quá nhiều (Nên dùng Dynamic Programming).
- Khi bước Merge (kết hợp) mất chi phí quá lớn (VD: $O(N^2)$ hoặc hơn), dẫn đến tổng thời gian không giảm.
- Khi bài toán có thể giải quyết nhanh gọn hơn bằng Greedy hoặc Stack/Queue trong một lần quét $O(N)$.

## 10. So sánh với các kỹ thuật khác
- **Vs DP**: D&C thường không có overlapping subproblems, DP thì có. (Một số bài như Different Ways to Add Parentheses có thể kết hợp Memoization thành DP).
- **Vs Sweep Line**: Skyline có thể giải bằng Sweep Line với Max Heap / TreeMap $O(N \log N)$. Sweep Line dễ code hơn nhưng D&C cung cấp cách tiếp cận tự nhiên hơn cho xử lý song song.

## 11. Độ phức tạp (Complexity Analysis)
- **Skyline**: Chia đôi $T(N) = 2T(N/2) + O(N) \implies O(N \log N)$ thời gian. Không gian $O(N)$ cho mảng lưu kết quả.
- **Closest Pair**: $T(N) = 2T(N/2) + O(N \log N)$ (nếu sort ở trong) hoặc $O(N)$ (nếu merge sort) $\implies O(N \log N)$ hoặc $O(N \log^2 N)$.
- **Beautiful Array**: $O(N \log N)$ để xây dựng.

## 12. Template Code cơ bản (Closest Pair of Points)
```java
// Giả mã (Pseudocode structure) cho Closest Pair
public double closest(Point[] points) {
    Arrays.sort(points, (a, b) -> Double.compare(a.x, b.x));
    return closestUtil(points, 0, points.length - 1);
}
private double closestUtil(Point[] points, int left, int right) {
    if (right - left <= 3) return bruteForce(points, left, right);
    int mid = left + (right - left) / 2;
    double dl = closestUtil(points, left, mid);
    double dr = closestUtil(points, mid + 1, right);
    double d = Math.min(dl, dr);
    return stripClosest(points, left, right, mid, d);
}
```

## 13. Phân tích từng dòng code
- `Arrays.sort(points, (a, b) -> Double.compare(a.x, b.x));`: Tiền xử lý, sắp xếp theo trục X.
- `if (right - left <= 3)`: Điều kiện dừng cơ sở, tính brute-force $O(1)$.
- `double dl = ..., dr = ...`: Bước Divide, chia đôi tập điểm.
- `double d = Math.min(dl, dr);`: Tìm khoảng cách min hiện tại.
- `stripClosest(...)`: Bước Conquer & Merge, chỉ kiểm tra dải có bề rộng $2d$.

## 14. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Giải thích cách The Skyline Problem hoạt động bằng D&C.
2. The Skyline Problem có thể làm bằng Sweep Line không? So sánh 2 cách.
3. Trong Closest Pair of Points, tại sao vòng lặp so sánh Y chỉ chạy tối đa 7 lần?
4. Chứng minh thuật toán Beautiful Array là đúng.
5. Complexity của Different Ways to Add Parentheses là bao nhiêu?
6. Tại sao Closest Pair of Points có thể đạt $O(N \log N)$?
7. Làm thế nào để cải thiện Closest Pair từ $O(N \log^2 N)$ xuống $O(N \log N)$?
8. Divide and Conquer khác thuật toán Sweep Line ở điểm nào trong bài toán hình học?
9. Thuật toán D&C áp dụng thế nào với cây nhị phân?
10. Burst Balloons có thể giải bằng D&C kèm Memoization như thế nào?
11. Phân biệt D&C thuần túy và Memoized D&C.
12. Có thể áp dụng D&C cho Linked List không? Làm sao để tìm trung điểm?
13. Merge k Sorted Lists phân tích theo D&C như thế nào?
14. Áp dụng D&C để tìm Inversion Count của mảng.
15. Majority Element sử dụng D&C (Thuật toán Boyer-Moore dễ hơn, nhưng nếu bắt dùng D&C thì sao).
16. Tìm Max Subarray Sum qua D&C $O(N \log N)$.
17. Longest Substring with At Least K Repeating Characters - tại sao bước chia lại chia tại ký tự xuất hiện $< K$ lần?
18. Super Egg Drop với D&C tối ưu như thế nào?
19. Giải thích Find the Kth Smallest Sum of a Matrix bằng D&C.
20. Expression Add Operators: Backtracking hay D&C? Tại sao?

## 15. 20 Lỗi phổ biến (Common Bugs)
1. Lỗi Stack Overflow do không có điều kiện dừng base case.
2. Base case sai (VD: Closest Pair bỏ sót trường hợp `left == right`).
3. Dùng mảng con (subarray) mà không truyền chỉ số `left`, `right`, tạo mảng mới gây tốn không gian, tăng Time Complexity.
4. Lỗi Off-by-one ở chỉ số `mid` (VD: `mid` và `mid+1` chia không đều gây infinite loop).
5. Tính sai khoảng cách (quên lấy căn bậc 2 trong tính khoảng cách, hoặc so sánh square thay vì distance nhưng type cast sai).
6. Trong Skyline: Merge hai skyline bỏ sót các tòa nhà ở một trong hai phía.
7. Skyline: Xử lý hai tòa nhà có cùng tọa độ X sai (phải gộp chung trước khi xuất kết quả).
8. Skyline: Giữ lại các điểm dư thừa trên cùng một đường thẳng ngang.
9. Trong Closest Pair: Quên không sắp xếp theo tọa độ Y ở bước `stripClosest`.
10. Trong Closest Pair: Tính nhầm kích thước của strip (lấy nhỏ hơn $d$ hoặc không bao trùm hết $d$).
11. Beautiful Array: Xử lý số lượng phần tử lẻ/chẵn khi kích thước là số lẻ.
12. Different Ways to Add Parentheses: Không memoize dẫn đến TLE (Time Limit Exceeded).
13. Truyền nhầm tham số khi đệ quy (truyền `right` thay vì `mid`).
14. Merge Sorted Array trong D&C: quên copy các phần tử còn dư.
15. Inversion Count: Cộng dồn biến đếm sai do tràn số kiểu `int`.
16. Không tối ưu được Heap trong bài Sweep Line (làm D&C thành giải pháp thay thế tốt hơn).
17. Mảng có nhiều phần tử trùng lặp khiến thuật toán D&C vô tình thoái hóa thành $O(N^2)$.
18. Max Subarray Sum: Bỏ quên phần tử cắt ngang qua `mid`.
19. Longest Substring với K repeating: Không kiểm tra nếu chuỗi hiện tại không chứa bất kỳ ký tự nào không hợp lệ.
20. Quên giải phóng bộ nhớ khi D&C ở các ngôn ngữ manual memory management (tuy Java có GC nhưng vẫn chú ý Memory Leak khi giữ mảng lớn).

## 16. 30 Trường hợp góc (Edge Cases)
1. Mảng/Danh sách rỗng.
2. Mảng chỉ có 1 phần tử.
3. Mảng có 2 phần tử giống hệt nhau.
4. (Skyline) Tòa nhà có độ cao bằng 0.
5. (Skyline) Các tòa nhà hoàn toàn đè lên nhau.
6. (Skyline) Các tòa nhà tiếp xúc nhau tại mép (chung trục X).
7. (Skyline) Một tòa nhà bao trùm toàn bộ các tòa nhà khác.
8. (Skyline) Các tòa nhà đứng rời rạc hoàn toàn, không đè lấp.
9. (Skyline) Có chiều cao bằng giá trị max của `Integer`.
10. (Closest Pair) Tất cả các điểm cùng nằm trên một trục.
11. (Closest Pair) Tất cả các điểm là duy nhất nhưng tụm lại ở 1 góc siêu nhỏ.
12. (Closest Pair) Tất cả điểm trùng tọa độ 100%.
13. (Closest Pair) Khoảng cách điểm rất lớn dẫn đến Overflow `double`.
14. (Beautiful Array) $N = 1$.
15. (Beautiful Array) $N = 1000$, thời gian tạo có kịp giới hạn không?
16. (Diff Ways Parentheses) Biểu thức chỉ có số, không có toán tử.
17. (Diff Ways Parentheses) Biểu thức có nhiều toán tử liên tiếp (nếu không được validate).
18. (Diff Ways Parentheses) Số âm hoặc số có nhiều chữ số (như `11`).
19. (Diff Ways Parentheses) Kết quả tràn kiểu `int`, cần `long` hoặc `BigInteger`.
20. Mảng đã sắp xếp giảm dần (Inversion count lớn nhất).
21. Mảng đã sắp xếp tăng dần.
22. Độ sâu đệ quy lớn ($N = 10^5$) ở môi trường recursion limit hẹp.
23. (Longest Substring) $K = 1$.
24. (Longest Substring) Chuỗi gồm tất cả các ký tự đều thỏa mãn.
25. (Longest Substring) Không có ký tự nào xuất hiện đủ $K$ lần.
26. (Max Subarray Sum) Toàn bộ mảng là số âm.
27. (Burst Balloons) $N = 1$ (Chỉ có 1 bong bóng).
28. (Burst Balloons) Giá trị mảng chứa số 0.
29. Cây tìm kiếm kích thước quá lớn, `mid = left + right` bị tràn `int`.
30. Giá trị tọa độ $X, Y$ là số âm.

## 17. Các biến thể của thuật toán
- **Divide and Conquer with Memoization**: Lưu các trạng thái đã giải của cây đệ quy.
- **Multithreaded D&C**: Sử dụng ForkJoinPool trong Java để giải song song các bài toán con (Merge Sort song song, Ray tracing song song).
- **Randomized D&C**: Điển hình là QuickSelect.

## 18. Mẹo tối ưu hóa
- Ngừng đệ quy sớm khi $N$ nhỏ (ví dụ $N \le 16$) và dùng thuật toán Brute-Force hoặc Insertion Sort để tránh overhead của các function calls đệ quy.
- Tái sử dụng mảng phụ trong quá trình đệ quy để tiết kiệm bộ nhớ (tránh tạo mới mảng `int[]` liên tục).
- Pass-by-reference (hoặc giữ các object mutable ngoài hàm đệ quy) để tránh chép chuỗi liên tục.

## 19. Cách gỡ lỗi (Debugging tips)
- In ra tham số `left` và `right` ở mỗi đầu bước đệ quy để vẽ cây gọi đệ quy (recursion tree).
- Theo dõi bước **Merge**: Đây là nơi phần lớn bugs xảy ra, hãy test code cho bước Merge một cách độc lập với vài ví dụ nhỏ.
- Chú ý các điều kiện cận như `left < right` hay `left <= right` ở base cases.

## 20. Bài tập thực hành nhỏ (Mini-exercises)
- Viết lại hàm chia một chuỗi toán học tại một index cho trước.
- Cài đặt hàm trộn hai mảng Skyline đã biết (ví dụ `A = [[0,2], [3,0]]`, `B = [[1,5], [4,0]]`).
- Xây dựng Beautiful Array cho $N = 5$ bằng tay trên giấy.

## 21. Mối liên hệ với các chủ đề khác
- **Tree Algorithms**: Cây nhị phân và Cây phân đoạn (Segment Tree) chia nửa vùng dữ liệu, bản chất là D&C.
- **Sorting**: Merge Sort, Quick Sort.
- **Dynamic Programming**: Khi D&C gặp Overlapping Subproblems, ta bổ sung bảng lưu trữ $\implies$ DP.

## 22. Các dự án mã nguồn mở sử dụng kỹ thuật này
- Các Engine đồ họa như **Blender** sử dụng Quad-Trees/Oct-Trees (Cấu trúc dữ liệu dựa trên D&C) cho Ray Tracing.
- Thư viện giải toán như **SymPy** chia nhỏ các biểu thức đại số theo D&C.
- **Apache Hadoop** MapReduce hoạt động theo mô hình Divide (Map) and Conquer (Reduce).

## 23. Cheatsheet / Tóm tắt
- **Công thức tính Complexity**: $T(N) = a T(N/b) + O(N^d)$. (Master Theorem).
- **Luôn tự hỏi**: Làm sao để chia? Làm sao để gộp? Gộp có mất quá nhiều thời gian không?
- Khi gặp bài liên quan đến tọa độ (2D), nghĩ ngay đến Sweep Line và Divide & Conquer (chia mặt phẳng).

## 24. Lời kết
Advanced Divide and Conquer đòi hỏi tư duy quy nạp sắc bén. Việc giải bài không chỉ dừng ở bước "chia đôi" mà quan trọng nhất là chiến lược kết hợp (Merge) sao cho hiệu quả. Hiểu sâu D&C sẽ giúp bạn chinh phục hầu hết các bài toán hình học tính toán và mảng phức tạp.
