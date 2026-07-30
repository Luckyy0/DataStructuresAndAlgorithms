# 05 - Advanced Backtracking (Quay lui Nâng cao)

## 1. Giới thiệu (Introduction)
Advanced Backtracking (Quay lui nâng cao) là một kỹ thuật mạnh mẽ giải quyết các bài toán tìm kiếm toàn duyệt (exhaustive search), đòi hỏi quản lý trạng thái phức tạp, tối ưu hóa không gian và thời gian bằng Bitmask, cũng như giải quyết các bài toán đồ thị NP-hard như tô màu đồ thị (Graph Coloring) hay đường đi Hamilton (Hamiltonian Paths).

## 2. Khái niệm cơ bản (Basic Concepts)
- **Complex State Management**: Trong các bài toán phức tạp, trạng thái không chỉ là một mảng hoặc danh sách, mà có thể là trạng thái nhiều chiều, chuỗi, hoặc đồ thị thay đổi liên tục.
- **Bitmasking trong Backtracking**: Dùng số nguyên (integer) để biểu diễn trạng thái của một tập hợp nhằm tiết kiệm không gian và tối ưu thời gian thao tác bit (O(1)) thay vì duyệt mảng (O(N)).
- **State Pruning (Cắt tỉa trạng thái)**: Sử dụng kỹ thuật Branch and Bound (Nhánh và Cận) để loại bỏ sớm các nhánh không thể dẫn tới kết quả tối ưu.

## 3. Ứng dụng thực tế (Real-world Applications)
- **Lập lịch (Scheduling)**: Phân bổ tài nguyên, xếp thời khóa biểu.
- **Trí tuệ nhân tạo (AI)**: Các game giải đố như Sudoku, Cờ vua (Tìm kiếm nước đi).
- **Mạng máy tính (Computer Networks)**: Định tuyến, tối ưu băng thông.
- **Sinh học điện toán (Computational Biology)**: Lắp ráp DNA, so khớp chuỗi gen.

## 4. Cấu trúc dữ liệu liên quan (Related Data Structures)
- **Bitmask (Integer)**: Tối ưu bộ nhớ.
- **Đồ thị (Graph/Adjacency List)**: Trình bày bài toán đường đi.
- **Hash Map/Hash Set**: Lưu trữ trạng thái đã đi qua (Memoization).

## 5. Thuật toán cơ bản (Basic Algorithms)
- **Backtracking tiêu chuẩn**: Thử - Sai - Khôi phục trạng thái.
- **Branch and Bound**: Giới hạn lại không gian tìm kiếm.
- **Tô màu đồ thị (Graph Coloring)**: Dùng ít màu nhất sao cho không có 2 đỉnh kề nhau cùng màu.
- **Chu trình Hamilton (Hamiltonian Cycle)**: Đi qua tất cả các đỉnh mỗi đỉnh đúng 1 lần.

## 6. Phân tích độ phức tạp (Complexity Analysis)
- **Thời gian (Time Complexity)**: Thường là O(2^N), O(N!) hoặc O(K^N). Rất lớn nếu không có pruning.
- **Không gian (Space Complexity)**: O(N) với N là độ sâu của Call Stack đệ quy. Nếu dùng HashMap lưu trạng thái có thể lên đến O(2^N).

## 7. So sánh với các cấu trúc dữ liệu và thuật toán khác (Comparison)
- **Dynamic Programming (DP)**: Backtracking duyệt toàn bộ (kể cả không có cấu trúc đệ quy chồng chéo), DP lưu trữ kết quả và chỉ áp dụng khi có Overlapping Subproblems. Có thể kết hợp thành DP with Bitmask.
- **Greedy**: Tham lam chọn tốt nhất cục bộ, có thể không ra tối ưu toàn cục. Backtracking đảm bảo kết quả chính xác nhưng chậm hơn.

## 8. Các biến thể (Variations)
- **State Optimization with Bitmask**: Ví dụ bài toán N-Queens sử dụng Bitmask thay vì mảng 2D.
- **Complex State Management**: Quản lý nhiều đối tượng di chuyển cùng lúc trên lưới (ví dụ: Robot Room Cleaner).
- **Graph Coloring**: Tìm số màu nhỏ nhất (Chromatic number).
- **Hamiltonian Path/Cycle**: Biến thể của Traveling Salesperson Problem (TSP).

## 9. Hướng dẫn cài đặt cơ bản (Basic Implementation Guide)
Mô hình chung của Backtracking Nâng cao:
```java
void backtrack(int state, int cost) {
    if (isGoal(state)) {
        updateResult(cost);
        return;
    }
    if (cost >= currentBestCost) return; // Pruning
    
    for (int nextState : getNextStates(state)) {
        if (isValid(nextState)) {
            applyState(nextState);
            backtrack(nextState, cost + 1);
            revertState(nextState); // Backtrack
        }
    }
}
```

## 10. Tối ưu hóa hiệu suất (Performance Optimization)
- **Sử dụng Bitmask**: Thay thế boolean arrays. Thao tác `&`, `|`, `^`, `~` nhanh hơn vòng lặp.
- **Heuristics Sorting**: Sắp xếp các lựa chọn tiếp theo sao cho nhánh có khả năng chứa nghiệm tối ưu (hoặc nhánh dễ bị fail nhất) được duyệt trước.
- **Symmetry Breaking**: Loại bỏ các cấu hình đối xứng (ví dụ trong bài toán N-Queens).

## 11. Lỗi thường gặp (Common Bugs - 20 Bugs)
1. Quên khôi phục trạng thái (Un-do state) sau lệnh đệ quy.
2. Sửa đổi nhầm cấu trúc dữ liệu toàn cục (Global Variable) dẫn đến sai lệch.
3. Không copy list/array khi lưu kết quả (`res.add(new ArrayList<>(path))` thay vì `res.add(path)`).
4. Sử dụng Bitmask vượt quá 32-bit cho `int` (Cần dùng `long` cho `N > 32`).
5. Vòng lặp vô hạn (Infinite Recursion) do thiếu Base Case.
6. Cắt tỉa (Pruning) quá mức làm mất nghiệm tối ưu.
7. Đánh dấu Visited sai thời điểm (trước/sau đệ quy).
8. Truyền tham chiếu biến đếm sai lệch trong đệ quy.
9. Lỗi ArrayIndexOutOfBounds khi thao tác bit: dịch bit quá giới hạn (`1 << 35` trên `int`).
10. StackOverflow do đệ quy quá sâu (Không gian tìm kiếm quá lớn).
11. Khởi tạo sai trạng thái ban đầu của Bitmask (0 thay vì -1 hoặc ngược lại).
12. Bỏ qua trường hợp bằng không (`N=0`) dẫn đến lỗi Logic.
13. Viết sai logic phép XOR `^` thành phép OR `|` khi khôi phục trạng thái bit.
14. Không xử lý được các đỉnh cô lập trong Graph Coloring.
15. Không nhận diện đúng chu trình (Cycle) trong Hamiltonian Path.
16. Thêm nhầm đỉnh đúp (Duplicate Vertices) trong quá trình duyệt Graph.
17. Sử dụng Object Equality (`==`) thay vì `.equals()` khi so sánh trạng thái chuỗi/object.
18. Không xem xét giá trị âm hoặc giá trị `null` của input.
19. Gán giá trị kết quả sớm nhưng không dừng nhánh sớm (thiếu lệnh `return`).
20. Trộn lẫn (mix up) giữa BFS Queue và DFS Stack khi cài đặt khử đệ quy Backtracking.

## 12. Các trường hợp biên (Edge Cases - 30 Edge Cases)
1. Đồ thị rỗng (Graph with 0 vertices).
2. Đồ thị chỉ có 1 đỉnh (Single node graph).
3. Đồ thị là dạng sao (Star graph).
4. Đồ thị dạng đường thẳng (Line graph).
5. Đồ thị đầy đủ (Complete graph - N node, N*(N-1)/2 cạnh).
6. Mảng đầu vào rỗng.
7. Bàn cờ kích thước `1x1` (N-Queens, N=1).
8. Bàn cờ không có giải pháp (N-Queens, N=2, N=3).
9. Đầu vào chứa các phần tử trùng lặp toàn bộ (All elements duplicate).
10. Bài toán yêu cầu kết quả duy nhất (Unique subsets/permutations) nhưng input có trùng lặp.
11. Giới hạn thời gian siêu khắt khe (Cần Pruning tuyệt đối).
12. String đầu vào rỗng (trong các bài toán match chuỗi).
13. String đầu vào chỉ chứa ký tự đặc biệt/wildcard.
14. Lưới chứa toàn chướng ngại vật (All obstacles in grid).
15. Khởi đầu nằm ngay tại đích đến (Start == Target).
16. Robot nằm trong phòng không có cửa (Trapped in a 1x1 cell).
17. Giới hạn số lượng phần tử N lên tới 64 (Bitmask phải đổi sang `long`).
18. Cấu hình ban đầu đã thỏa mãn mục tiêu.
19. Trạng thái không thể tới đích (Unreachable states).
20. Đầu vào là cây (Tree) thay vì đồ thị vòng.
21. Đồ thị phân tách thành nhiều thành phần liên thông (Disconnected components).
22. Bài toán Coloring với K màu mà K = 1.
23. Bài toán Coloring với Graph bipartite (K=2 luôn giải được).
24. Tồn tại self-loops trong đồ thị.
25. Tồn tại multiple edges giữa 2 đỉnh.
26. Mảng/Trạng thái đã được sắp xếp giảm dần ngược hoàn toàn so với yêu cầu.
27. Đích nằm ngoài biên của ma trận (Out of bounds).
28. Tổng các phần tử lớn hơn ngưỡng kiểu `int`, gây Overflow.
29. Lời giải yêu cầu số lượng bộ nhớ quá lớn, cạn kiệt Heap Space.
30. Dữ liệu mảng chứa toàn số 0.

## 13. Mẫu thiết kế (Design Patterns)
- **State Pattern**: Gói gọn việc chuyển đổi trạng thái thành một object để tránh truyền quá nhiều tham số.
- **Command Pattern**: Ứng dụng cho việc Undo/Redo các thao tác khi quay lui.
- **Template Method**: Tách biệt logic Base Case, Validation và Recursion ra các method abstract.

## 14. Câu hỏi phỏng vấn (Interview Questions - 20 Questions)
1. Phân biệt Backtracking và DFS?
2. Khi nào nên dùng Bitmask trong Backtracking?
3. Trình bày bài toán N-Queens sử dụng Bitmask tối ưu hóa không gian.
4. Làm sao để giải bài toán Đảo chữ (Anagrams) sinh các hoán vị duy nhất?
5. Làm thế nào để pruning (cắt tỉa) trong bài toán Hamiltonian Cycle?
6. Chromatic Number là gì? Cách tìm Chromatic Number bằng Backtracking?
7. Sự khác biệt giữa nhánh và cận (Branch and Bound) và Backtracking cơ bản?
8. Tại sao độ phức tạp của bài toán Sudoku Solver lại được cho là O(9^(N^N))?
9. Viết thuật toán cho Robot Room Cleaner khi bạn không có bản đồ.
10. Làm sao để loại bỏ các kết quả bị lặp lại trong Combination Sum II?
11. Đánh giá tính khả thi khi giải TSP (Traveling Salesman Problem) bằng Backtracking so với DP.
12. Ứng dụng của thuật toán Knuth's Algorithm X (Dancing Links) là gì?
13. Nếu không gian trạng thái quá lớn khiến đệ quy gây StackOverflow, bạn sẽ xử lý thế nào?
14. Trong bài toán Regular Expression Matching, tại sao Backtracking có thể rơi vào TLE?
15. Lợi ích của việc sắp xếp mảng đầu vào trước khi thực hiện Backtracking?
16. Hậu quả của việc quên `revert` (khôi phục) trạng thái trong Backtracking?
17. Mô tả cách kiểm tra trạng thái an toàn trong N-Queens bằng 3 biến bitmask: `col`, `diag1`, `diag2`.
18. Thuật toán quay lui có thể được song song hóa (Parallelization) không?
19. Giải thích khái niệm "Exact Cover Problem".
20. Có thể dùng Queue thay vì Stack để mô phỏng lại quá trình Backtracking không? Giải thích.

## 15. Bài tập thực hành (Practice Problems)
- N-Queens II (Đếm số giải pháp)
- Sudoku Solver
- Graph Coloring Problem
- Hamiltonian Path in Directed Graph
- Remove Invalid Parentheses

## 16. Mẹo gỡ lỗi (Debugging Tips)
- **Log đường đi (Print Path)**: In ra `path` hoặc `state` hiện tại ở đầu hàm và trước khi `revert`.
- **Giới hạn độ sâu (Depth Limit)**: Dùng biến `depth` để chặn đệ quy vô hạn trong quá trình debug.
- **Vẽ cây đệ quy (Draw Recursion Tree)**: Ra giấy với N nhỏ (VD: N=3) để trace logic.

## 17. Tiêu chuẩn mã nguồn (Coding Standards)
- Đặt tên hàm đệ quy rõ ràng (VD: `backtrack`, `solve`, `findPath`).
- Nhóm các thao tác chuyển trạng thái (Do) và khôi phục (Undo) ở gần nhau để dễ theo dõi.
- Sử dụng các hằng số hoặc enum cho trạng thái đặc biệt thay vì "Magic Numbers".

## 18. Công cụ và thư viện (Tools and Libraries)
- Trình gỡ lỗi (Debugger) của IDE (IntelliJ, Eclipse) với tính năng "Drop Frame" cực hữu ích khi debug đệ quy.
- Java `BitSet` nếu cần Bitmask vượt quá 64 bit (so với `long`).

## 19. Tài liệu tham khảo (References)
- "Introduction to Algorithms" (Cormen et al.) - Chương liên quan đến NP-Completeness.
- Thuật toán Dancing Links (DLX) của Donald Knuth.

## 20. Câu hỏi trắc nghiệm (Quizzes)
1. Thao tác `state |= (1 << i)` trong Bitmask có ý nghĩa gì?
   - A. Bật bit thứ i thành 1. (Đúng)
   - B. Tắt bit thứ i thành 0.
   - C. Đảo ngược bit thứ i.
2. Backtracking luôn tìm ra nghiệm tối ưu nhanh hơn thuật toán Vét cạn (Brute Force)?
   - A. Đúng (nếu có pruning).
   - B. Sai (Trong worst case vẫn là vét cạn O(2^N)). (Đúng)

## 21. Bài tập dự án (Project Ideas)
- Xây dựng một AI giải Sudoku bằng OpenCV (nhận diện hình ảnh) và thuật toán Backtracking/Dancing Links.
- Ứng dụng xếp lịch học cho sinh viên bằng Graph Coloring Algorithm.

## 22. Lộ trình học tiếp theo (Learning Roadmap)
- Tiếp tục với Dynamic Programming (Quy hoạch động), đặc biệt là DP with Bitmask để tối ưu hóa thời gian của các bài toán Backtracking (giảm từ O(N!) xuống O(2^N * N)).
- Chuyển sang các bài toán luồng mạng (Network Flow).

## 23. Ghi chú cá nhân (Personal Notes)
- Tập trung vào kỹ thuật Pruning (cắt tỉa) nhánh, đây là yếu tố phân loại giữa ứng viên bình thường và ứng viên giỏi trong các bài toán Backtracking hóc búa.

## 24. Phụ lục (Appendix)
- **Cheat Sheet Thao tác Bit**:
  - Lấy bit thứ i: `(state >> i) & 1`
  - Bật bit thứ i: `state | (1 << i)`
  - Tắt bit thứ i: `state & ~(1 << i)`
  - Đảo bit thứ i: `state ^ (1 << i)`
  - Xóa bit 1 thấp nhất (Lowest set bit): `state & (state - 1)`
