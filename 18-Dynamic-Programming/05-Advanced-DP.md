# 05 - Advanced Dynamic Programming (Quy hoạch động nâng cao)

## 1. Giới thiệu (Introduction)
Quy hoạch động (Dynamic Programming - DP) nâng cao bao gồm các kỹ thuật mở rộng từ DP cơ bản để giải quyết các bài toán có không gian trạng thái phức tạp, cấu trúc dữ liệu không tuyến tính (như cây, đồ thị), hoặc cần biểu diễn tập hợp bằng bit. Các chuyên đề chính bao gồm DP trên cây (Tree DP), DP với mặt nạ bit (Bitmask DP), DP chữ số (Digit DP) và DP trên đồ thị/khoảng (Interval/Graph DP).

## 2. Cấu trúc dữ liệu & Thuật toán (Data Structures & Algorithms)
- **DP on Trees**: DFS/BFS, In-out DP, Post-order traversal.
- **Bitmask DP**: Các toán tử thao tác bit (AND, OR, XOR, SHIFT).
- **Digit DP**: State bao gồm `index` (vị trí hiện tại), `tight` (giới hạn chặn trên), `leading_zero` (số 0 vô nghĩa ở đầu), và đôi khi cần thêm các tính chất đặc thù (chia hết, tổng chữ số,...).
- **Interval DP**: Cấu trúc mảng 2D cho trạng thái `dp[i][j]` (giải bài toán từ vị trí i đến j).

## 3. Ứng dụng thực tế (Real-World Applications)
- **Mạng máy tính**: Tìm đường đi ngắn nhất qua mọi điểm định tuyến (TSP) ứng dụng Bitmask DP.
- **Trí tuệ nhân tạo**: Tối ưu hóa trò chơi nhiều lượt (Minimax với DP).
- **Tin sinh học**: Gióng hàng chuỗi gene nhiều trình tự, cấu trúc gập protein.
- **Toán học và Mật mã**: Đếm số lượng thỏa mãn thuộc tính số học lớn cực hạn bằng Digit DP.

## 4. Phân tích chi tiết (In-Depth Analysis)
### 4.1. DP on Trees (Quy hoạch động trên cây)
Giải quyết các bài toán yêu cầu kết hợp kết quả từ cây con lên gốc (Bottom-Up, In-DP) và từ gốc xuống lá (Top-Down, Out-DP). Trạng thái thường là `dp[u][state]`, đại diện cho kết quả tối ưu tại gốc `u` với một điều kiện cụ thể.

### 4.2. DP with Bitmasking (Mặt nạ bit)
Sử dụng một số nguyên để đại diện cho một tập hợp. Với 32 bit, ta có thể lưu trạng thái cho 32 phần tử. Kết hợp với DP cho phép giải các bài toán mang tính hoán vị, tổ hợp (O(2^N * N)).

### 4.3. Digit DP
Áp dụng trên các bài toán yêu cầu tìm/đếm lượng số nguyên trong đoạn `[L, R]` thỏa mãn thuộc tính nào đó. Khung code điển hình: xây dựng hàm đệ quy sinh các chữ số từ trái qua phải.

### 4.4. DP on Graphs and Intervals (Trên đồ thị và khoảng)
- **Interval DP**: Giải quyết bài toán bằng cách gộp các khoảng nhỏ hơn lại. Độ dài chuỗi/mảng thường là kích thước của bài toán con `L = 1 -> N`.
- **Graph DP**: Thường là tìm đường đi, đếm số chu trình, kết hợp với Topological Sort trong đồ thị DAG.

## 5. Hướng dẫn từng bước (Step-by-Step Guide)
1. Xác định không gian trạng thái (State Space).
2. Xây dựng phương trình chuyển đổi trạng thái (Transition).
3. Định nghĩa trạng thái cơ sở (Base Case).
4. Xác định thứ tự đánh giá để đảm bảo các bài toán con đều được tính trước.

## 6. Các thuật toán & Cấu trúc dữ liệu liên quan (Related DS & Algorithms)
- **Trie (Cây tiền tố)**: Ứng dụng cùng với DP on Trees hoặc Game Theory.
- **Segment Tree / Fenwick Tree**: Sử dụng để tối ưu hóa việc truy vấn trong DP.
- **Topological Sorting**: Sắp xếp thứ tự duyệt cho DAG DP.

## 7. So sánh & Đánh giá (Comparison & Evaluation)
- DP đệ quy (Top-down) thường tự nhiên hơn cho Tree DP và Digit DP, dễ kiểm soát state phức tạp.
- DP khử đệ quy (Bottom-up) tối ưu về hệ số (constant time) và tránh tràn stack, phổ biến trong Interval DP.

## 8. Các lỗi thường gặp (Common Bugs/Pitfalls)
1. Khởi tạo mảng Memoization sai giá trị (ví dụ khởi tạo `0` thay vì `-1` khi `0` là một kết quả hợp lệ).
2. Quên xử lý Base Case trong đệ quy có nhớ.
3. Chồng lấp trạng thái (State Overlapping) do thiết kế không đủ chiều (dimension) cho DP.
4. Tràn số nguyên (Integer Overflow) khi tính toán giá trị lớn (quên modulo).
5. Quên cộng thêm giá trị của node gốc vào kết quả trong DP on Trees.
6. Tính nhầm Index khi dịch bit trong Bitmask DP (`1 << i` thay vì `1 << (i-1)`).
7. Thứ tự duyệt trạng thái sai trong Bottom-up DP (sử dụng giá trị chưa được tính).
8. Không xử lý số 0 dẫn đầu (Leading zeros) trong Digit DP.
9. Quên cờ giới hạn (Tight bound flag) trong Digit DP, dẫn đến đếm thừa số lớn hơn N.
10. Sai lầm khi khởi tạo bitmask (nhầm `0` khác `1`).
11. Trộn lẫn kết quả từ các nhánh cây không độc lập trong Tree DP (quên độc lập thống kê).
12. Đánh dấu Visited sai lúc trong Interval DP (ví dụ: `len = 1` vs `len = 0`).
13. Gọi đệ quy lại chính trạng thái hiện tại gây Infinite Loop trong đồ thị có chu trình.
14. Phân bổ bộ nhớ mảng quá lớn gây Memory Limit Exceeded.
15. Không làm sạch (clear/reset) mảng Memoization giữa các test cases liên tiếp.
16. Tính toán lại những Subproblems có thể suy ra từ Subproblems nhỏ hơn bằng công thức O(1).
17. Dùng HashMap cho Memoization thay vì mảng 1D/2D gây TLE (Time Limit Exceeded).
18. Lỗi lệch biên 1 đơn vị (Off-by-one) như `i <= n` thay vì `i < n`.
19. Không chuyển đổi trạng thái phù hợp trên đồ thị có chu trình.
20. Nhầm lẫn giá trị khởi tạo Maximum và Minimum (khởi tạo MAX_VALUE cho bài cần tìm Max).

## 9. Các trường hợp góc & Ngoại lệ (Edge Cases & Exceptions)
1. `N = 0`, mảng hoặc tập hợp rỗng.
2. `N = 1`, cây chỉ có 1 node hoặc chuỗi chỉ có 1 ký tự.
3. Kích thước Bitmask lớn hơn 31 bit (vượt giới hạn int dương, cần `long`).
4. Các cạnh của đồ thị hoặc cây có trọng số âm.
5. Cây có dạng đường thẳng (Degenerate tree/Linked List).
6. Cây có dạng hình sao (Star graph/tree).
7. Digit DP: Input có giá trị bằng 0.
8. Digit DP: Số cực lớn, chạm giới hạn của `long` (18-19 chữ số).
9. Interval DP: Khoảng có độ dài bằng 1 hoặc bằng 0.
10. Interval DP: Khoảng rỗng hoặc start index > end index.
11. DP trên đồ thị: Đồ thị không liên thông, gồm nhiều thành phần rời rạc.
12. Cấu trúc cây ngẫu nhiên cực sâu, gây StackOverflow.
13. Trạng thái chỉ có một cấu hình duy nhất hợp lệ, không có lựa chọn nào khác.
14. Mảng đầu vào có tất cả các phần tử là số âm.
15. Mảng đầu vào có tất cả phần tử bằng 0.
16. Bitmask yêu cầu bao phủ tất cả `0` bit hoặc toàn `1` bit ngay từ đầu.
17. Cây có bậc cực lớn (ví dụ: 1 gốc có N-1 con).
18. Trọng số yêu cầu tính modulo âm (cần `(a % M + M) % M`).
19. Kích thước bộ nhớ cần thiết lớn hơn RAM giới hạn, cần tối ưu Memory.
20. Bài toán TSP với chỉ 1 hoặc 2 node.
21. Digit DP: Input là chuỗi số có độ dài 10^5 (phải lưu số dưới dạng String thay vì Long).
22. Digit DP: Các chữ số cho phép tạo thành số vô hạn (độ dài không giới hạn).
23. Interval DP với mảng đã được sắp xếp sẵn từ đầu.
24. Interval DP với mảng bị đảo ngược.
25. Matrix Chain Multiplication: Tất cả các ma trận đều có kích thước 1x1.
26. Mọi phần tử đầu vào giống hệt nhau (All elements identical).
27. Đỉnh gốc của cây không có đóng góp gì vào kết quả tổng.
28. Bitmask DP yêu cầu tập con k phần tử từ n phần tử (tổ hợp C(n, k) khổng lồ).
29. Các tập con có liên kết phụ thuộc vòng tròn (Cyclic Dependency) không giải được bằng DP DAG thường.
30. Tìm đường đi cực đại trên đồ thị có chu trình trọng số âm.

## 10. Tối ưu hóa hiệu suất (Performance Optimization)
- Sử dụng Memory Optimization (Sliding Window / Rolling Array).
- Knuth Optimization, Divide and Conquer Optimization hoặc Convex Hull Trick để giảm bậc thời gian.

## 11. Ví dụ thực hành cơ bản (Basic Practical Examples)
DP trên Cây tính đường kính cây lớn nhất. Tính khoảng cách lớn nhất giữa hai lá qua DP In/Out.

## 12. 20 câu hỏi phỏng vấn (20 Interview Questions)
1. Hãy giải thích nguyên lý của Bitmask DP và khi nào nên áp dụng nó?
2. Sự khác nhau giữa In-DP và Out-DP trong quy hoạch động trên cây là gì?
3. Digit DP hoạt động như thế nào và ba trạng thái cơ bản (`pos`, `tight`, `leading_zero`) đại diện cho điều kiện gì?
4. Làm thế nào để giải quyết bài toán Travelling Salesperson Problem (TSP) bằng Bitmask DP? Nêu độ phức tạp.
5. Trình bày cách tối ưu bộ nhớ cho một bài toán DP 2D chỉ phụ thuộc vào hàng/cột trước đó.
6. Interval DP là gì? Khởi tạo và thứ tự duyệt vòng lặp như thế nào cho đúng (theo độ dài đoạn)?
7. Trong Tree DP, làm sao để tính giá trị tối đa cho phép chọn/không chọn của các node lân cận?
8. Tại sao DP trên ma trận hoặc đồ thị có chu trình lại khó áp dụng chuẩn trạng thái và làm sao để khắc phục?
9. Trạng thái (State) trong DP on Graphs thường gồm những thông tin gì?
10. Phân tích bài toán Matrix Chain Multiplication và cách chia bài toán con.
11. Số Catalan có liên hệ gì với Interval DP qua các bài toán hình học / đa giác?
12. Có thể sử dụng BFS/DFS kết hợp với DP không? Cho ví dụ.
13. Tối ưu DP bằng bao lồi (Convex Hull Trick) là gì và khi nào nên dùng?
14. Giải thích Tối ưu hóa chia để trị (Divide and Conquer Optimization) trong quy hoạch động.
15. Knuth Optimization trong Interval DP được áp dụng như thế nào? Cải thiện độ phức tạp ra sao?
16. Các bài toán đếm với Digit DP yêu cầu điều kiện chia hết, trạng thái (state) sẽ thay đổi thế nào?
17. Hãy thiết kế State DP để giải bài Burst Balloons.
18. Làm sao để phát hiện một bài toán ưu tiên giải bằng Bitmask DP chứ không phải Backtracking thông thường?
19. Viết công thức chuyển trạng thái cho bài toán tìm chuỗi con chung dài nhất (LCS) với điều kiện.
20. Cách giải bài toán "Số lượng cách phân hoạch một tập hợp" bằng Bitmask DP?

## 13. Design Patterns liên quan (Related Design Patterns)
- **Memoization / Singleton**: Lưu trữ kết quả bộ nhớ trên phiên bản tĩnh.
- **State Pattern**: Chuyển đổi qua lại giữa các trạng thái khác nhau của DP.

## 14. Anti-Patterns cần tránh (Anti-Patterns to Avoid)
- Truyền quá nhiều tham số không cần thiết (không thay đổi) vào hàm đệ quy.
- Gọi đệ quy lại để tính các bài toán con trùng lặp mà không sử dụng mảng lưu (Memoization).

## 15. Tích hợp với hệ thống lớn (Integration with Large Systems)
Tính toán song song (Parallel DP) cho các trạng thái không phụ thuộc nhau bằng ThreadPools.

## 16. Kiểm thử & Debug (Testing & Debugging)
Xác minh bằng thuật toán Vét cạn (Brute Force/Backtracking) với bộ dữ liệu nhỏ.

## 17. Bảo mật (Security Considerations)
- Hạn chế sâu gọi đệ quy (Stack Depth Limit) ngăn tấn công DoS.
- Quản lý kích thước đầu vào an toàn (Input Validation).

## 18. Mở rộng (Scalability)
Tối ưu không gian bằng State Compression, hoặc Distributed Cache cho DP với State siêu lớn.

## 19. Tài liệu tham khảo (References)
- Competitive Programming 4.
- CLRS Algorithms.

## 20. Câu hỏi thường gặp (FAQs)
- DP vs DFS/Backtracking khác nhau thế nào? DP là Backtracking có Memoization + Overlapping subproblems.

## 21. Bài tập thực hành (Hands-On Exercises)
Tham khảo file bài tập để rèn luyện (Advanced-DP-Problems.md).

## 22. Lời khuyên cho người mới (Advice for Beginners)
Hãy vẽ ra bảng state hoặc cây đệ quy ra giấy trước khi code để hình dung sự chồng lấp của trạng thái.

## 23. Xu hướng tương lai (Future Trends)
Tối ưu quy hoạch động sử dụng Reinforcement Learning và Neural Networks cho bài toán NP-Hard cận tối ưu.

## 24. Tổng kết (Conclusion)
Advanced DP là cánh cửa bước vào các cuộc thi lập trình thi đấu và là trọng tâm của các vòng phỏng vấn khó. Nắm chắc chúng sẽ giúp bạn giải quyết hầu hết bài toán tối ưu.
