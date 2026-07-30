# Optimization and Pruning (Tối ưu và Cắt tỉa)

## 1. Giới thiệu (Introduction)
Tối ưu hóa (Optimization) và Cắt tỉa (Pruning) là các kỹ thuật không thể thiếu khi sử dụng thuật toán quay lui (Backtracking). Việc duyệt toàn bộ không gian trạng thái thường dẫn đến Time Limit Exceeded (TLE) do độ phức tạp hàm mũ hoặc giai thừa. Pruning giúp loại bỏ sớm các nhánh không thể chứa nghiệm hoặc chứa nghiệm không tối ưu, từ đó giảm đáng kể số lượng trạng thái cần duyệt. Các bài toán kinh điển như chuỗi Combination Sum hay phân chia mảng thành các tập con có tổng bằng nhau (Partitioning arrays) phụ thuộc rất nhiều vào pruning.

## 2. Khái niệm cốt lõi (Core Concepts)
- **Search Tree (Cây tìm kiếm):** Biểu diễn toàn bộ không gian trạng thái của thuật toán backtracking.
- **State (Trạng thái):** Một đỉnh trong search tree.
- **Pruning (Cắt tỉa):** Đánh giá một trạng thái hiện tại; nếu trạng thái này không thể dẫn đến kết quả mong muốn, dừng ngay việc phân nhánh từ trạng thái đó và quay lui.
- **Feasibility Check (Kiểm tra tính khả thi):** Cắt tỉa khi ràng buộc bài toán bị vi phạm (VD: tổng hiện tại vượt quá target).
- **Optimality Check (Kiểm tra tính tối ưu):** Cắt tỉa khi nhánh hiện tại chắc chắn kém hơn giải pháp tốt nhất đã tìm thấy (trong bài toán tối ưu).
- **Symmetry Breaking (Phá vỡ tính đối xứng):** Cắt tỉa các nhánh tạo ra kết quả lặp lại do tính đối xứng của bài toán (VD: phần tử trùng lặp trong mảng).

## 3. Cấu trúc dữ liệu & Thuật toán (Data Structures & Algorithms)
- **Sorting (Sắp xếp):** Thường là bước đầu tiên để thực hiện pruning hiệu quả (đặc biệt khi xử lý phần tử trùng lặp hoặc khi muốn vượt qua threshold nhanh nhất).
- **Visited Array / Bitmask:** Lưu trạng thái đã thăm để tránh tính toán lại.
- **Memoization:** Kết hợp Backtracking với Memoization để tránh duyệt lại trạng thái trùng lặp.

## 4. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity:** Thông thường Backtracking là $O(2^N)$, $O(N!)$, hoặc $O(b^d)$ (với b là hệ số phân nhánh, d là độ sâu). Nhờ pruning, hệ số b giảm đáng kể trong thực tế, nhưng trường hợp xấu nhất (worst-case) thường vẫn là hàm mũ.
- **Space Complexity:** $O(N)$ hoặc $O(d)$ cho call stack của đệ quy.

## 5. Các kiểu cài đặt (Implementation Variants)
- **Early return:** Đặt các câu lệnh điều kiện ở đầu hàm đệ quy để cắt tỉa ngay.
- **Loop pruning:** Trong vòng lặp sinh các lựa chọn, có lệnh `break` hoặc `continue` để bỏ qua các lựa chọn không hợp lệ.

## 6. Các phép toán cơ bản (Basic Operations)
- Khởi tạo: Sắp xếp mảng đầu vào.
- Pruning 1: `if (current_sum > target) return;` (Cắt tỉa tính khả thi).
- Pruning 2: `if (i > start_index && arr[i] == arr[i-1]) continue;` (Loại bỏ trùng lặp).

## 7. Mô hình hóa bài toán (Problem Modeling)
- Xác định mục tiêu (Target).
- Tìm điều kiện dừng sớm.
- Tìm điều kiện tránh lặp lại (Duplicates).

## 8. Các mẫu phổ biến (Common Patterns)
- **Combination Sum Series:** Bài toán chọn phần tử sao cho tổng bằng `target`.
- **Partitioning Arrays:** Chia mảng thành $K$ tập con có tổng bằng nhau. Target mỗi tập là $sum/K$. Cần sắp xếp giảm dần để gán phần tử lớn trước (Greedy optimization).

## 9. Kỹ thuật tối ưu (Optimization Techniques)
- Sắp xếp mảng giảm dần (Descending Sort) khi phân chia mảng để các phần tử lớn nhanh chóng đạt `target` hoặc làm fail nhánh sớm.
- Đánh dấu đã dùng phần tử (Used Boolean array).
- Nếu tổng các phần tử còn lại không đủ để đạt target, dừng sớm.
- Bỏ qua các giá trị giống nhau bằng cách so sánh phần tử liền trước đã bị skip.

## 10. So sánh với các cấu trúc/thuật toán khác (Comparison)
- **Backtracking vs DP:** Khi chỉ cần tìm số lượng nghiệm hoặc nghiệm tối ưu toàn cục, DP nhanh hơn. Nếu cần liệt kê mọi cấu hình, phải dùng Backtracking.
- **Backtracking vs Greedy:** Greedy chọn lựa chọn tốt nhất hiện tại và không quay lui. Backtracking thử mọi lựa chọn, có quay lui.

## 11. Ứng dụng thực tế (Real-world Applications)
- Giải Sudoku, N-Queens.
- Lập lịch (Scheduling), phân chia tài nguyên.
- Trí tuệ nhân tạo cơ bản (Minimax với Alpha-Beta Pruning).

## 12. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Tại sao phải sắp xếp đầu vào trước khi dùng backtracking trong bài Combination Sum II?
2. Sự khác biệt giữa `break` và `continue` trong quá trình cắt tỉa nhánh là gì?
3. Khi phân chia mảng thành $K$ phần bằng nhau, tại sao sắp xếp giảm dần lại hiệu quả hơn tăng dần?
4. Alpha-beta pruning là gì và nó liên quan như thế nào đến backtracking thông thường?
5. Làm thế nào để tránh in ra các tập hợp con trùng lặp khi input có các số giống nhau?
6. Khi nào backtracking không thể tránh khỏi TLE dù đã pruning?
7. Giải thích cách memoization giúp giảm độ phức tạp của backtracking?
8. Kỹ thuật "Bitmask" có thể thay thế mảng `visited` như thế nào để tối ưu không gian?
9. Trong bài N-Queens, làm sao để kiểm tra 2 đường chéo trong $O(1)$?
10. Sự khác nhau giữa việc truyền tham chiếu và truyền bản sao của cấu trúc dữ liệu trong đệ quy?
11. Giải thích ý tưởng của bài Matchsticks to Square.
12. Có thể áp dụng pruning cho các bài toán đồ thị không? (VD: DFS)
13. Nếu $K$ lớn hơn số phần tử mảng, bạn xử lý bài Partition to K Equal Sum Subsets thế nào?
14. Trong trường hợp nào việc kiểm tra điều kiện cắt tỉa lại tốn nhiều thời gian hơn cả việc duyệt?
15. Làm sao để áp dụng backtracking tìm chuỗi Fibonacci?
16. Đối với bài Target Sum, so sánh cách giải bằng Backtracking và Dynamic Programming.
17. Khi nào nên dùng BFS thay vì Backtracking (DFS)?
18. Thuật toán Branch and Bound khác với Backtracking ở điểm nào?
19. Cắt tỉa theo giới hạn trên/dưới (Upper/Lower bounds) hoạt động ra sao?
20. Nếu có số âm trong mảng của Combination Sum, điều gì xảy ra với kỹ thuật pruning `sum > target`?

## 13. 20 Lỗi thường gặp (20 Common Bugs)
1. Quên backtracking (xóa trạng thái) sau lệnh gọi đệ quy.
2. Sắp xếp mảng nhưng vẫn quên lệnh `if (i > start && arr[i] == arr[i-1]) continue;`.
3. Kiểm tra điều kiện cắt tỉa sai thứ tự dẫn đến Index Out of Bounds.
4. Truyền sai `start_index` vào lệnh gọi đệ quy (dùng `0` thay vì `i` hoặc `i+1`).
5. Không kiểm tra trường hợp cơ sở của bài toán Partition (vd: `sum % k != 0`).
6. Dùng `break` thay cho `continue` làm cắt luôn toàn bộ các giá trị hợp lệ lớn hơn.
7. Điều kiện dừng sớm sai cho mảng chứa số âm.
8. Quên đánh dấu mảng `visited` thành `false` ở bước quay lui.
9. Kiểm tra base case chậm làm overhead cao.
10. Truyền list bằng reference vào list kết quả tổng (chỉ sao chép pointer, kết quả bị rỗng).
11. Cắt tỉa điều kiện `i-1` mà không đảm bảo `i > start`.
12. Gán mảng bị off-by-one error.
13. Tính toán lại biến `sum` bên trong đệ quy thay vì truyền qua parameter.
14. StackOverflowError do thiếu base case.
15. Gọi hàm sắp xếp nhiều lần bên trong hàm đệ quy.
16. Xử lý thiếu trường hợp target bằng 0.
17. Điều kiện pruning sai khi mảng chứa cả giá trị 0.
18. Không tối ưu được vòng lặp với $K$ túi trong bài toán Partition.
19. Quên loại bỏ túi rỗng tương đương trong bài toán chia đồ.
20. Dùng đối tượng String nối chuỗi liên tục tạo ra nhiều object rác thay vì dùng StringBuilder.

## 14. 30 Trường hợp kiểm thử/Edge cases (30 Edge Cases)
1. Mảng rỗng.
2. Mảng có 1 phần tử.
3. Tất cả phần tử trong mảng giống nhau.
4. Mảng chứa số âm, 0, dương.
5. Target bằng 0.
6. Target rất lớn (vượt quá tổng các phần tử).
7. Target số âm.
8. Các phần tử đã được sắp xếp sẵn.
9. Các phần tử sắp xếp ngược (giảm dần).
10. Số lượng $K = 1$.
11. Số lượng $K$ bằng độ dài mảng.
12. Tổng mảng không chia hết cho $K$.
13. Có một phần tử lớn hơn target của tập con.
14. Nhiều giá trị 0 làm kết quả trùng lặp.
15. Tối đa số lượng phần tử (VD: mảng 15-20 phần tử, dễ TLE).
16. Phần tử lớn nhất nằm cuối mảng.
17. Kết hợp các phần tử chẵn và lẻ để ra target chẵn.
18. Kết quả đòi hỏi tất cả phần tử của mảng.
19. Không có kết quả nào thỏa mãn.
20. Mảng có 2 phần tử cực lớn và n phần tử nhỏ.
21. Trùng lặp phần tử ở vị trí không kề nhau (nếu chưa sort).
22. Bài chia hình vuông: Tổng chia hết cho 4 nhưng không ghép được.
23. TargetSum: Độ lệch chênh lệch quá xa.
24. Backtracking chuỗi Fibonacci với độ dài số nguyên vượt quá giới hạn Integer.MAX_VALUE.
25. Mảng có nhiều kết quả hợp lệ, cần lưu tất cả.
26. Mảng có cực nhiều kết quả hợp lệ, tràn bộ nhớ Heap.
27. Đếm số lượng cấu hình, kết quả là 0.
28. Kiểm tra thời gian thực thi (TLE edge case).
29. Giá trị phần tử rất nhỏ bé (VD: `[1,1,1...,1]` 30 lần).
30. Cắt tỉa vòng lặp khi túi đang xét trùng dung lượng với túi trước đó.

## 15. Thực hành tốt nhất (Best Practices)
- Luôn kiểm tra base case và edge cases ở đầu hàm wrapper, trước khi gọi backtracking.
- Sắp xếp input để thực hiện pruning (nếu cần thiết).
- Sử dụng các biến truyền tham số (như `current_sum`) để tránh tính toán lại bên trong hàm.
- Với Java, thêm phần tử vào `List` hoặc mảng primitive để track trạng thái thay vì String concatenation.

## 16. Các biến thể (Variants)
- Tìm một nghiệm vs Tìm tất cả các nghiệm vs Tìm đường đi ngắn nhất.
- Đếm số lượng nghiệm.
- Trả về nghiệm tối ưu theo một hàm đánh giá (Cost function).

## 17. Mối liên hệ với các chủ đề khác (Relationship with other topics)
- **Dynamic Programming:** Bài toán đếm số nghiệm thường giải bằng DP (như Subset Sum, Coin Change).
- **Graph Traversal:** Backtracking thực chất là duyệt DFS trên cây trạng thái.
- **Bit Manipulation:** Lưu trạng thái bằng bitmask nhanh hơn dùng mảng boolean.

## 18. Công cụ & Thư viện (Tools & Libraries)
- Trình gỡ lỗi (Debugger) của IDE (IntelliJ, Eclipse) với tính năng "Step Into" và "Drop Frame".
- Profiler / Flame Graph để đo thời gian đệ quy và phát hiện bottleneck.

## 19. Mẹo gỡ lỗi (Debugging Tips)
- In ra trace của hàm đệ quy (Ví dụ thụt lề `indent` theo độ sâu).
- Theo dõi các biến trạng thái tại điểm vào và ra của hàm đệ quy.
- Kiểm tra xem điều kiện pruning có vô tình loại bỏ mất nghiệm đúng không (False positive pruning).

## 20. Câu hỏi trắc nghiệm (Multiple-choice Questions)
1. Trong bài Combination Sum II, tại sao cần lệnh `if (i > start && nums[i] == nums[i-1]) continue;`?
A. Để tối ưu thời gian. 
B. Tránh trùng lặp tập con (Symmetry breaking). 
C. Để đệ quy dừng.
(Đáp án: B)
2. Độ phức tạp không gian (Space Complexity) của backtracking do đệ quy thường được xác định bởi:
A. Số lượng node lá. 
B. Chiều sâu lớn nhất của cây. 
C. Hệ số phân nhánh.
(Đáp án: B)

## 21. Bài tập thực hành (Practice Exercises)
- Tập bài Combination Sum (I, II, III, IV).
- Tập bài Subsets (I, II).
- Sudoku Solver.
- Matchsticks to Square.

## 22. Lịch sử & Nguồn gốc (History & Origin)
Backtracking được sử dụng từ rất sớm trong khoa học máy tính, tiêu biểu là việc giải bài toán 8 quân hậu (N-Queens) do Max Bezzel đề xuất năm 1848, và các thuật toán máy tính đầu tiên cài đặt nó vào những năm 1950 (do D. H. Lehmer mô tả).

## 23. Tài liệu tham khảo (References)
- Introduction to Algorithms (CLRS).
- Các bài giảng về trí tuệ nhân tạo (Alpha-beta pruning, Constraint Satisfaction Problems).

## 24. Kết luận (Conclusion)
Tối ưu hóa và cắt tỉa (Optimization and Pruning) là chìa khóa biến backtracking từ một thuật toán vét cạn "chậm chạp" thành một công cụ mạnh mẽ trong thực tế. Bằng cách thiết kế các chiến lược nhận diện sớm nhánh vô ích, chúng ta có thể mở rộng giới hạn không gian tìm kiếm lên rất nhiều trước khi phải chuyển sang các phương pháp phức tạp hơn như Dynamic Programming.
