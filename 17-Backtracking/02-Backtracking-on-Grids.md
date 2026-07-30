# Backtracking on Grids (Quay lui trên Lưới/Ma trận)

## 1. Tổng quan (Introduction)
Backtracking (quay lui) trên grids (lưới/ma trận 2D) là một kỹ thuật mạnh mẽ để duyệt qua các cấu trúc dữ liệu không gian 2D, nhằm tìm kiếm một con đường (path), một tập hợp các ô, hoặc cấu hình thỏa mãn điều kiện nhất định.

## 2. Bản chất của Backtracking trên Grids
Duyệt ma trận thường sử dụng DFS (Depth-First Search). Khác với DFS truyền thống trên đồ thị chỉ cần đánh dấu `visited` và đi tiếp, Backtracking yêu cầu ta phải **quay lui** (undo state) sau khi đã khám phá xong một nhánh, để trạng thái của lưới trở về như cũ, cho phép các nhánh khác đi qua.

## 3. Cấu trúc dữ liệu và Trạng thái (Data Structures and State)
Để duyệt qua các ô kề cạnh, ta sử dụng mảng hướng (Direction arrays). Phổ biến nhất là 4 hướng (lên, xuống, trái, phải) hoặc 8 hướng.
```java
int[][] dirs = {{-1,0}, {1,0}, {0,-1}, {0,1}}; // Lên, Xuống, Trái, Phải
```

## 4. Quản lý trạng thái: Visited Matrices vs In-place Modification
Khi duyệt qua các ô, ta cần đánh dấu ô đó đã được thăm để tránh chu trình (cycle).
- **Visited Matrices**: Dùng mảng `boolean[][] visited` có cùng kích thước. An toàn, không thay đổi dữ liệu gốc, tốn O(N) space.
- **In-place Modification**: Thay đổi giá trị của ô (ví dụ `grid[i][j] = '#'`). Tiết kiệm space, nhưng làm thay đổi cấu trúc dữ liệu đầu vào.

## 5. Phục hồi trạng thái (State Restoration) - Yếu tố cốt lõi
Đây là cốt lõi của Backtracking. Sau khi gọi đệ quy khám phá các bước tiếp theo, ta phải trả lại giá trị ban đầu cho ô hiện tại (unmark visited).
```java
visited[r][c] = true;
// Explore...
visited[r][c] = false; // Phục hồi trạng thái
```

## 6. Các bài toán Constraint Cổ điển (N-Queens, Sudoku)
Backtracking rất hiệu quả trên các bài toán Constraint Satisfaction Problems (CSP).
- **N-Queens**: Đặt N quân Hậu trên bàn cờ N x N sao cho không quân nào tấn công quân nào. Constraint là các hàng, cột, đường chéo.
- **Sudoku**: Điền số 1-9 vào bảng 9x9. Constraint là hàng, cột và các block 3x3 phải có các số duy nhất.

## 7. Cắt tỉa nhánh (Pruning Techniques)
Pruning giúp loại bỏ sớm các nhánh không thể dẫn đến kết quả, giảm thiểu số lượng node cần duyệt. Ví dụ: kiểm tra tính hợp lệ trước khi đi sâu vào nhánh (valid move check), sử dụng heuristic.

## 8. Độ phức tạp Thời gian và Không gian
- **Time Complexity**: Khá cao, thường là Exponential O(b^d) với b là branching factor (số hướng đi), d là độ sâu tối đa (độ dài đường đi). Đối với N-Queens là O(N!), Word Search có thể là O(M * N * 3^L) (L là độ dài từ).
- **Space Complexity**: O(d) cho Call Stack đệ quy. O(M*N) nếu dùng mảng visited, O(1) phụ thêm nếu In-place.

## 9. Ưu điểm của phương pháp (Advantages)
Cấu trúc code trực quan, dễ dàng ánh xạ từ tư duy con người sang máy tính, đặc biệt hiệu quả khi cần tìm tất cả các cấu hình khả thi.

## 10. Nhược điểm và Thách thức (Disadvantages & Challenges)
Chi phí tính toán khổng lồ vì đi sâu vào không gian trạng thái, rất dễ bị Time Limit Exceeded (TLE) nếu không có các kỹ thuật prune đúng đắn.

## 11. Các dạng biến thể (Variations)
- Path Finding (Word Search, Rat in a Maze).
- Shape Building/Island Constraints.
- Placement Problems (N-Queens, Sudoku).

## 12. So sánh DFS Backtracking vs BFS trên Grid
- BFS tìm đường đi ngắn nhất, thường không cần backtracking vì nó lan tỏa đều.
- DFS Backtracking dễ cài đặt bằng đệ quy, tốn ít bộ nhớ stack hơn hàng đợi BFS trên lưới lớn, phù hợp hơn để duy trì trạng thái của một "con đường" duy nhất.

## 13. Backtracking vs Dynamic Programming trên Grid
Dynamic Programming sử dụng Memoization để tránh tính lại bài toán con, nhưng chỉ có thể thực hiện khi bài toán có thuộc tính Overlapping Subproblems và trạng thái không phụ thuộc vào toàn bộ đường đi (như Longest Increasing Path). Backtracking thì giải được những bài mà DP bó tay do state quá lớn.

## 14. Ứng dụng thực tế (Real-world Applications)
- Game AI (Giải Sudoku, Cờ vua, Mê cung).
- Path planning trong Robotics.
- Thiết kế mạch điện tử (Routing).
- Resource Allocation trên ma trận không gian.

## 15. Template Code chuẩn
```java
void backtrack(char[][] grid, int r, int c) {
    if (isBaseCase()) { processSolution(); return; }
    grid[r][c] = '#'; // Mark
    for (int[] d : dirs) {
        int nr = r + d[0], nc = c + d[1];
        if (isValid(grid, nr, nc)) backtrack(grid, nr, nc);
    }
    grid[r][c] = '.'; // Unmark (Backtrack)
}
```

## 16. Kỹ năng Debug
In ra grid ở mỗi bước, theo dõi call stack, kiểm tra base case và hàm kiểm tra tính hợp lệ (isValid). Log trạng thái trước và sau khi unmark.

## 17. 20 Câu hỏi Phỏng vấn (Interview Questions)
1. Sự khác biệt giữa DFS và Backtracking trên ma trận 2D?
2. Khi nào nên dùng visited matrix và khi nào nên modify in-place?
3. Viết thuật toán giải Sudoku 9x9.
4. Làm sao để tối ưu hoá bài toán Word Search II bằng Trie?
5. Độ phức tạp của bài N-Queens là bao nhiêu?
6. Viết code đếm số đường đi duy nhất (Unique Paths III).
7. Giải thích kỹ thuật pruning trong Rat in a Maze.
8. Làm thế nào để kiểm tra tính hợp lệ của đường chéo trong N-Queens bằng toán học?
9. Backtracking có thể giải được bài toán chu trình Hamilton trên Grid không?
10. Tại sao Number of Islands thường không cần gỡ đánh dấu (unmark)?
11. Trình bày cách dùng Bitmask cho bài N-Queens.
12. Có thể dùng BFS để thay thế Backtracking khi tìm tất cả đường đi không?
13. Cách xử lý khi grid chứa các ô vật cản (obstacles)?
14. Optimal branching factor của Word Search trên grid là bao nhiêu?
15. Giải thích cách lưu trữ trạng thái bằng String hoặc Hash trong Memoization của Grid.
16. Code bài Path with Maximum Gold.
17. Sự nguy hiểm của stack overflow trong grid lớn là gì? Cách khắc phục?
18. Viết template cơ bản của Backtracking 4 hướng.
19. Giải thích bài toán Robot Room Cleaner.
20. Bài toán tô màu bản đồ (Map Coloring) trên grid.

## 18. 20 Lỗi phổ biến (Common Bugs)
1. Quên unmark (phục hồi trạng thái) sau lệnh gọi đệ quy.
2. Kiểm tra `visited` sai logic, dẫn đến đi lùi vào ô cũ.
3. Không kiểm tra boundaries (Out of bounds `IndexOutOfBoundsException`).
4. Truy cập grid trước khi kiểm tra bounds (vd: `grid[r][c] == 1 && r >= 0`).
5. Base case không return, dẫn đến đệ quy vô hạn.
6. Return sai giá trị khi sử dụng logic OR (`||`) cho nhiều hướng nhánh.
7. Sai công thức tính hướng (Direction vector sai, thiếu hướng hoặc lặp hướng).
8. Dùng mảng global `visited` mà không clear giữa các lần chạy test case.
9. Quên xử lý ô bắt đầu (Start cell) khi mark visited.
10. Sửa đổi `grid` in-place nhưng quên trả lại giá trị gốc.
11. Tính toán đường chéo `r - c` và `r + c` bị sai index (âm) trong N-Queens.
12. Vô tình lặp vô hạn nếu ô vật cản và ô trống được xử lý không khéo.
13. Base case điều kiện sai (vd: đếm số bước đi lặp lại dư 1).
14. Trong Sudoku, gọi next ô sai nhưng không bọc qua dòng mới khi c == 9.
15. Không backtrack mảng/list chứa kết quả đường đi hiện tại (quên `list.remove(size-1)`).
16. Đặt visited true bên trong vòng lặp nhưng lại gọi backtrack bên ngoài.
17. Dùng shallow copy thay vì deep copy khi lưu các cấu hình bàn cờ vào kết quả.
18. Ghi đè biến state của frame trước trong call stack vì dùng biến toàn cục.
19. Khởi tạo sai kích thước của mảng `visited` hoặc mảng dp.
20. Missing logic kiểm tra char array matches dẫn đến NullPointerException.

## 19. 30 Trường hợp biên (Edge Cases)
1. Ma trận 1x1.
2. Ma trận 1xN (chỉ có một hàng).
3. Ma trận Nx1 (chỉ có một cột).
4. Ma trận hoàn toàn trống.
5. Ma trận hoàn toàn bị chặn (chứa vật cản).
6. Grid có chứa số lượng phần tử lẻ/chẵn tác động tới luật (vd Tiling).
7. Target string trong Word Search dài hơn tổng số ô trong ma trận.
8. Target string rỗng.
9. Bắt đầu từ ô cuối cùng của ma trận.
10. Đường đi là một đường ziczac lấp đầy toàn bộ bảng.
11. Ma trận có các ký tự trùng lặp liên tục.
12. Tìm đường nhưng bị bao vây ngay từ điểm xuất phát.
13. Điểm bắt đầu và kết thúc trùng nhau.
14. Số N trong N-Queens = 1.
15. Số N trong N-Queens = 2, 3.
16. Bảng Sudoku đã được giải sẵn.
17. Bảng Sudoku không hợp lệ ngay từ đầu.
18. Không có lời giải cho N-Queens hoặc Sudoku.
19. Không có bất kỳ vàng nào trong bài Path with Maximum Gold.
20. Mọi ô đều có vàng và đường đi vòng vèo phức tạp nhất.
21. Backtrack chạm đến độ sâu tối đa của Call Stack (~10^4).
22. Grid có mảng con bị rỗng (jagged array).
23. Đòi hỏi in ra đường đi có thứ tự từ điển (Lexicographical order).
24. Có nhiều hơn 1 đường đi ngắn nhất/tốt nhất.
25. Mọi đường đi đều có trọng số hoặc giá trị như nhau.
26. Di chuyển 8 hướng (kể cả chéo) gặp tình trạng cross-path.
27. Đếm số lượng đường thay vì chỉ in một đường (tràn số Integer).
28. Chỉ có 2 ô và phải bước qua lại 100 lần.
29. Cấu trúc grid quá lớn gây out of memory khi dùng visited matrix.
30. Dữ liệu mảng chứa null rows.

## 20. Best Practices
- Sử dụng direction array để tối giản code thay vì 4 khối if-else.
- Viết hàm `isValid(r, c)` riêng để code clean.
- Luôn cẩn thận với tham chiếu của Collection (List/Array) khi lưu kết quả.

## 21. Anti-patterns
- Copy toàn bộ grid/matrix trong mỗi lần đệ quy.
- Không phục hồi trạng thái đúng cách.
- Gom quá nhiều logic vào hàm đệ quy làm hàm quá phức tạp.

## 22. System Design và Backtracking
Trong thiết kế hệ thống, Backtracking có thể ứng dụng ở cấp độ tìm kiếm cấu hình tối ưu (Dependency resolution, config generation), nhưng thường chạy offline hoặc được tối ưu hóa bằng các heuristics (A*, SAT Solvers).

## 23. Roadmap luyện tập
- Bước 1: Nắm vững DFS cơ bản (Number of Islands).
- Bước 2: Học kỹ thuật Unmark / Backtrack (Word Search).
- Bước 3: Áp dụng State / List backtrack (Subsets, Permutations trên Grid).
- Bước 4: Giải các bài Constraint cực trị (N-Queens, Sudoku).
- Bước 5: Cắt tỉa nhánh nâng cao và kết hợp Trie (Word Search II).

## 24. Tổng kết
Backtracking trên Grids là chủ đề kinh điển và quan trọng. Cốt lõi của nó là sự khám phá toàn diện và "biết sai thì quay đầu" bằng kỹ thuật State Restoration.
