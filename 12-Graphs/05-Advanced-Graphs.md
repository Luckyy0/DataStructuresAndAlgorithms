# Cấu trúc dữ liệu và Giải thuật: Advanced Graphs (Đồ thị nâng cao)

## 1. Giới thiệu (Introduction)
Advanced Graphs bao gồm các chủ đề phức tạp hơn trong lý thuyết đồ thị (Graph Theory), tập trung vào các đặc tính cấu trúc sâu hơn của đồ thị như tính phân đôi (Bipartite Graph), các thành phần liên thông mạnh (Strongly Connected Components), đường đi và chu trình Euler (Eulerian Path/Circuit), cũng như các điểm khớp (Articulation Points) và cầu (Bridges).

## 2. Đặc điểm và Định nghĩa (Characteristics & Definitions)
- **Bipartite Graph (Đồ thị hai phía):** Đồ thị có thể chia các đỉnh thành hai tập hợp rời rạc $U$ và $V$ sao cho mọi cạnh đều nối một đỉnh trong $U$ với một đỉnh trong $V$.
- **Strongly Connected Components - SCCs (Thành phần liên thông mạnh):** Trong đồ thị có hướng (Directed Graph), một SCC là một đồ thị con tối đại mà từ mọi đỉnh đều có đường đi đến mọi đỉnh khác.
- **Eulerian Path/Circuit (Đường đi/Chu trình Euler):** Đường đi (Path) đi qua mọi cạnh của đồ thị chính xác một lần. Chu trình (Circuit) là một đường đi Euler bắt đầu và kết thúc tại cùng một đỉnh.
- **Articulation Points & Bridges (Điểm khớp và Cầu):** Điểm khớp là đỉnh mà khi xóa nó sẽ làm tăng số thành phần liên thông của đồ thị. Cầu là cạnh mà khi xóa nó sẽ làm tăng số thành phần liên thông.

## 3. Các thuật toán chính (Core Algorithms)
### 3.1. Bipartite Graph checking (Coloring via BFS/DFS)
Sử dụng kỹ thuật tô màu (Graph Coloring). Bắt đầu từ một đỉnh, tô màu 0. Các đỉnh kề tô màu 1, và kề của kề lại tô màu 0. Nếu trong quá trình duyệt qua đồ thị bằng BFS hoặc DFS phát hiện hai đỉnh kề nhau có cùng màu, đồ thị không phải Bipartite.

### 3.2. Strongly Connected Components (SCCs)
- **Kosaraju's Algorithm:** Chạy DFS lần 1 để lấy thứ tự hoàn thành (finishing time) của các đỉnh. Đảo chiều các cạnh đồ thị (Transpose Graph). Chạy DFS lần 2 theo thứ tự hoàn thành giảm dần. Mỗi cây DFS trong lần chạy 2 là một SCC.
- **Tarjan's Algorithm:** Chỉ cần 1 lần DFS. Duy trì mảng `discovery_time` và `lowest_time` để theo dõi thời gian thăm và khả năng quay lui. Các đỉnh thuộc cùng một SCC sẽ có cùng gốc SCC. Sử dụng Stack để lưu các đỉnh của SCC hiện tại.

### 3.3. Eulerian Path and Circuit
- **Hierholzer's Algorithm:** Dùng cho đồ thị có hướng hoặc vô hướng. Điều kiện tồn tại:
  - Vô hướng: Mọi đỉnh có bậc chẵn (Euler Circuit), hoặc đúng 2 đỉnh có bậc lẻ (Euler Path).
  - Có hướng: Mọi đỉnh có `in-degree == out-degree` (Circuit), hoặc đúng 1 đỉnh có `out-degree - in-degree = 1` và 1 đỉnh có `in-degree - out-degree = 1` (Path).
  - Thuật toán: Dùng DFS từ đỉnh bắt đầu, duyệt qua tất cả các cạnh chưa thăm, xóa cạnh đã thăm. Khi hết cạnh từ đỉnh hiện tại, thêm đỉnh vào kết quả (post-order). Cuối cùng đảo ngược kết quả.

### 3.4. Articulation Points and Bridges
Dựa trên DFS, duy trì mảng `discovery_time` (thời gian thăm) và `lowest_time` (thời gian nhỏ nhất có thể quay lại được từ nhánh DFS).
- **Bridge:** Cạnh $(u, v)$ là cầu nếu `lowest_time[v] > discovery_time[u]`.
- **Articulation Point:** Đỉnh $u$ là điểm khớp nếu có con $v$ trong cây DFS sao cho `lowest_time[v] >= discovery_time[u]` (với $u$ không phải là gốc DFS) hoặc $u$ là gốc DFS và có nhiều hơn 1 con.

## 4. Các biến thể (Variants)
- Directed vs Undirected Graphs trong việc tìm Bridges/Articulation points (thường chỉ áp dụng cho undirected graph, nhưng có biến thể cho directed).
- Bipartite Matching (Kuhn's Algorithm, Hopcroft-Karp) - Ứng dụng Bipartite graph để tìm cặp ghép cực đại.
- 2-SAT Problem: Biến đổi bài toán logic thành đồ thị có hướng và giải bằng SCC.

## 5. Triển khai Mã nguồn (Implementation - Java)
*(Xem file bài tập để có mã nguồn chi tiết cho các thuật toán)*

## 6. Phân tích Độ phức tạp (Complexity Analysis)
- **Bipartite Check:** Time: $O(V + E)$, Space: $O(V)$.
- **Kosaraju / Tarjan:** Time: $O(V + E)$, Space: $O(V)$.
- **Hierholzer's:** Time: $O(V + E)$, Space: $O(V + E)$ (do stack gọi đệ quy hoặc ngăn xếp).
- **Articulation Points & Bridges:** Time: $O(V + E)$, Space: $O(V)$.

## 7. Ứng dụng thực tế (Real-world Applications)
- **Bipartite:** Phân chia team, hệ thống gợi ý phim cho user (đồ thị User-Item).
- **SCC:** Phân tích cấu trúc mạng xã hội, phát hiện vòng lặp phụ thuộc (dependency cycles) trong package manager.
- **Eulerian Path:** Lắp ráp DNA sinh học, tối ưu đường đi thu gom rác (Routing problems).
- **Bridges & Articulation Points:** Xác định các điểm yếu (Single Point of Failure) trong hệ thống mạng máy tính hoặc giao thông.

## 8. Ưu điểm và Nhược điểm (Pros & Cons)
- **Kosaraju vs Tarjan:** Kosaraju dễ hiểu hơn nhưng cần 2 lượt DFS và cấp phát đồ thị đảo ngược. Tarjan nhanh hơn thực tế (chỉ 1 lượt DFS) nhưng logic phức tạp hơn.
- **Hierholzer:** Rất tối ưu nhưng cần phá hủy đồ thị hoặc tốn thêm cấu trúc dữ liệu đánh dấu cạnh đã thăm.

## 9. So sánh (Comparison)
- **Hamiltonian Path vs Eulerian Path:** Hamiltonian đi qua mọi ĐỈNH đúng 1 lần (NP-Hard). Eulerian đi qua mọi CẠNH đúng 1 lần (Có thuật toán đa thức $O(V+E)$).

## 10. Các mẫu thuật toán (Algorithmic Patterns)
- **Graph Coloring:** Tô màu đỉnh với số lượng màu giới hạn để kiểm tra tính chất cấu trúc.
- **DFS Tree Properties:** Sử dụng `discovery_time` và `lowest_time` để theo dõi back-edges.
- **Post-order Traversal trong Graph:** Áp dụng cho Hierholzer và Topological Sort.

## 11. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Bipartite Graph là gì? Điều kiện cần và đủ của nó là gì?
2. Làm sao kiểm tra đồ thị là Bipartite dùng BFS/DFS?
3. Nếu đồ thị có chu trình lẻ (odd cycle) thì nó có phải là Bipartite không? Tại sao?
4. Định nghĩa Strongly Connected Component (SCC).
5. Mô tả thuật toán Kosaraju để tìm SCC.
6. Sự khác biệt giữa thuật toán Kosaraju và Tarjan là gì?
7. Thuật toán Tarjan sử dụng mảng `low` như thế nào?
8. Eulerian Path và Eulerian Circuit khác nhau thế nào?
9. Điều kiện tồn tại Eulerian Path trong đồ thị vô hướng là gì?
10. Điều kiện tồn tại Eulerian Circuit trong đồ thị có hướng là gì?
11. Trình bày thuật toán Hierholzer.
12. Articulation Point (Điểm khớp) là gì?
13. Bridge (Cầu) là gì?
14. Thuật toán tìm Bridge dùng DFS hoạt động như thế nào?
15. Sự khác biệt giữa `low[v] > disc[u]` và `low[v] >= disc[u]` trong thuật toán tìm Bridge và Articulation Point.
16. Nếu gốc của cây DFS có đúng 1 con, nó có phải là Articulation Point không?
17. Giải bài toán 2-SAT bằng Graph như thế nào?
18. Maximum Bipartite Matching là gì? Thuật toán nào giải quyết nó?
19. Giải thích De Bruijn Sequence và ứng dụng của Eulerian Path vào nó.
20. Tại sao Hamiltonian Path khó giải (NP-Hard) trong khi Eulerian Path dễ (P)?

## 12. 20 Lỗi thường gặp (20 Common Bugs)
1. **Bipartite check:** Quên không kiểm tra các thành phần liên thông rời rạc (disconnected components).
2. **Bipartite check:** Gán nhầm màu khi dùng BFS làm sai lệch kết quả đồ thị chẵn.
3. **Tarjan:** Quên gán `onStack[u] = false` khi pop các đỉnh thuộc SCC.
4. **Tarjan:** Cập nhật `low[u] = Math.min(low[u], low[v])` thay vì `disc[v]` đối với back-edge khi $v$ đã ở trong stack.
5. **Kosaraju:** Quên lưu lại đồ thị đảo chiều, hoặc duyệt DFS lần 2 sai danh sách kề.
6. **Kosaraju:** Duyệt theo thứ tự bảng chữ cái thay vì pop từ Stack lưu finishing time.
7. **Eulerian Path:** Không xét điều kiện liên thông trước khi tìm Eulerian Path. Đồ thị có Eulerian Path phải là một đồ thị liên thông (bỏ qua đỉnh cô lập).
8. **Eulerian Path:** Quên thêm đỉnh vào kết quả theo post-order (phải thêm sau khi thăm hết các cạnh từ nó).
9. **Eulerian Path:** Dùng adjacency matrix gây time limit exceeded cho đồ thị thưa. Dùng adjacency list nhưng không xóa cạnh hiệu quả $O(1)$.
10. **Bridges:** Không xử lý đúng trường hợp back-edge nối về parent trực tiếp trong vô hướng (nếu là parent trực tiếp thì không tính là back edge làm giảm `low`).
11. **Bridges:** Sai dấu so sánh `low[v] >= disc[u]` thay vì `low[v] > disc[u]`.
12. **Articulation Point:** Quên logic đặc biệt cho root node của cây DFS (phải kiểm tra số lượng `children > 1`).
13. **Articulation Point:** Đếm trùng điểm khớp nếu 1 điểm khớp có nhiều con thỏa mãn điều kiện (nên dùng boolean array hoặc Set để đánh dấu).
14. **Tarjan/Bridges:** Bắt đầu index của `disc` từ 0 có thể gây nhầm lẫn nếu không cẩn thận khi check unvisited. Khuyên dùng 1-based hoặc kiểm tra != -1.
15. **Eulerian Path:** Khi đảo ngược kết quả path bị sai do danh sách lưu kết quả có thứ tự chèn không chuẩn.
16. **Bipartite:** Trả về đồ thị Bipartite hợp lệ ngay khi tìm thấy đồ thị con Bipartite nhưng quên loop qua tất cả đỉnh.
17. **SCC:** Trộn lẫn thuật toán Topo sort thông thường với thuật toán Tarjan (Topo sort chỉ đúng cho DAG, SCC dùng cho Directed có chu trình).
18. **Eulerian Path đồ thị vô hướng:** Quên xóa chiều ngược lại của cạnh khi dùng adjacency list, dẫn đến thăm lặp lại vô hạn.
19. **Eulerian Path:** Khởi tạo điểm bắt đầu (start node) sai. Phải bắt đầu từ đỉnh có `out - in = 1` (nếu có path), hoặc bất kỳ đỉnh nào `out > 0` (nếu là circuit).
20. **Kosaraju:** Tràn stack đệ quy (StackOverflowError) khi DFS trên đồ thị dạng đường thẳng với $N = 10^5$.

## 13. 30 Trường hợp đặc biệt (30 Edge Cases)
1. Đồ thị rỗng (0 đỉnh, 0 cạnh).
2. Đồ thị có 1 đỉnh không cạnh.
3. Đồ thị có 1 đỉnh với 1 self-loop (cạnh khuyên).
4. Đồ thị có 2 đỉnh, 1 cạnh vô hướng.
5. Bipartite: Đồ thị hoàn toàn ngắt kết nối (mọi đỉnh cô lập) -> Luôn là Bipartite.
6. Bipartite: Đồ thị có chu trình chẵn ($C_4$).
7. Bipartite: Đồ thị có chu trình lẻ ($C_3$).
8. SCC: Đồ thị là DAG (Directed Acyclic Graph) -> Mỗi đỉnh là 1 SCC.
9. SCC: Đồ thị là 1 chu trình đơn hướng duy nhất -> Chỉ có 1 SCC chứa tất cả.
10. SCC: Đồ thị đầy đủ có hướng (Complete directed graph).
11. Eulerian: Đồ thị có đỉnh cô lập, nhưng các đỉnh có cạnh vẫn thỏa mãn điều kiện bậc chẵn -> Tồn tại Eulerian Circuit (bỏ qua đỉnh cô lập).
12. Eulerian: Đồ thị có nhiều thành phần liên thông có cạnh -> Không tồn tại Eulerian.
13. Eulerian: Các self-loops có làm thay đổi bậc chẵn/lẻ không? (Mỗi self-loop tăng in-degree 1 và out-degree 1 -> không ảnh hưởng).
14. Bridges: Đồ thị dạng chu trình (Cycle) -> Không có cầu nào.
15. Bridges: Đồ thị dạng cây (Tree) -> Mọi cạnh đều là cầu.
16. Articulation Point: Đồ thị dạng cây hình sao (Star graph) -> Nút trung tâm là điểm khớp duy nhất.
17. Articulation Point: Graph có 2 đỉnh, 1 cạnh -> Không có điểm khớp nào (xóa 1 đỉnh thì đỉnh kia vẫn liên thông).
18. Đồ thị có multi-edges (cạnh lặp) trong thuật toán Bridges: Phải đếm id của cạnh để không bỏ qua back-edge nếu đó là 2 cạnh song song giữa u và v.
19. Eulerian có hướng: `out - in = 1` ở nhiều hơn 1 đỉnh -> Không có Eulerian Path.
20. Kosaraju trên đồ thị không liên thông mạnh, có nhiều nhánh nối.
21. Đồ thị Bipartite dạng cây, một bên $U$ có 1 đỉnh, bên $V$ có $N-1$ đỉnh (Star graph).
22. Tìm điểm khớp trên đồ thị có nhiều thành phần liên thông (Phải DFS qua mọi component).
23. Cấu trúc lồng nhau của SCC: SCC A chỉ đi được tới SCC B, SCC B đi tới SCC C.
24. Eulerian Path bắt buộc ưu tiên thứ tự từ điển (Lexicographical order) khi có nhiều nhánh (e.g. Reconstruct Itinerary). Cần dùng PriorityQueue thay cho LinkedList.
25. Mảng `low` và `disc` ở đồ thị có cạnh khuyên: Cạnh khuyên không sinh ra cầu.
26. Thuật toán tìm Bridge khi $N, M$ rất lớn, giới hạn Memory Limit.
27. Đồ thị đồ thị vô hướng trong đó 2 đỉnh nối nhau bởi 3 đường độc lập -> Không có điểm khớp.
28. Hierholzer nhưng bắt đầu DFS từ đỉnh sai do ngẫu nhiên lấy đỉnh đầu tiên.
29. Cấu trúc "Barbell" (hai clique nối bởi 1 cạnh cầu): Một test kinh điển để check Bridge và Articulation Point.
30. Đồ thị có $N=10^5, M=10^5$, thuật toán đệ quy DFS quá sâu (xử lý bằng DFS dùng Stack vòng lặp thủ công).

## 14. Thiết kế hệ thống (System Design)
- Đồ thị lớn như mạng xã hội (Facebook/Twitter) phân tích SCC hoặc bạn chung (Bipartite relation) phải sử dụng MapReduce hoặc các hệ thống Graph Processing như Apache Giraph, Pregel, GraphX. Không thể fit đồ thị vào RAM của 1 máy.

## 15. Ví dụ thực tế (Real-world Examples)
- **Kiểm tra Bipartite:** Phân công sinh viên và dự án (Sinh viên - Dự án), kiểm tra xem có xung đột lợi ích.
- **Eulerian Path:** Lộ trình du lịch qua tất cả các con đường nổi tiếng mà không đi lặp lại.

## 16. Code Templates
```java
// Bipartite Check (BFS)
public boolean isBipartite(int[][] graph) {
    int n = graph.length;
    int[] colors = new int[n]; 
    for (int i = 0; i < n; i++) {
        if (colors[i] == 0) {
            Queue<Integer> q = new LinkedList<>();
            q.add(i);
            colors[i] = 1;
            while (!q.isEmpty()) {
                int curr = q.poll();
                for (int next : graph[curr]) {
                    if (colors[next] == 0) {
                        colors[next] = -colors[curr];
                        q.add(next);
                    } else if (colors[next] == colors[curr]) {
                        return false;
                    }
                }
            }
        }
    }
    return true;
}
```

## 17. Trực quan hóa (Visualization)
*(Có thể dùng Mermaid để vẽ đồ thị SCC, Bipartite).*

## 18. Thực hành tốt nhất (Best Practices)
- Trong bài toán Eulerian Path, nếu có nhiều cạnh bị trùng hoặc đồ thị thưa, lưu cấu trúc kề bằng `Map<Integer, PriorityQueue<Integer>>` nếu yêu cầu thứ tự từ điển, hoặc vòng lặp xóa trực tiếp qua iterator / last index array để đạt độ phức tạp $O(E)$.

## 19. Các chủ đề liên quan (Related Topics)
- Minimum Spanning Tree (Prim, Kruskal).
- Shortest Path (Dijkstra, Bellman-Ford).
- Flow Networks (Ford-Fulkerson).

## 20. Lịch sử và Nguồn gốc (History & Background)
- Thuật toán Hierholzer do Carl Hierholzer xuất bản năm 1873.
- Cầu Königsberg (bài toán 7 cây cầu) của Euler năm 1736 là nền tảng khởi nguồn cho lý thuyết đồ thị và khái niệm Eulerian Path.

## 21. Cơ sở Toán học (Mathematical Foundations)
- Tổng bậc của tất cả các đỉnh trong đồ thị luôn là một số chẵn bằng $2|E|$ (Định lý Handshaking Lemma). Điều này giải thích tại sao số lượng đỉnh bậc lẻ luôn phải là một số chẵn.

## 22. Tối ưu hóa nâng cao (Advanced Optimizations)
- Khi code thuật toán Tarjan có thể đóng gói toàn bộ trạng thái mảng visited, disc, low vào chung cấu trúc State hoặc mảng phẳng `int[]` để tối ưu hóa CPU Cache.
- Khử đệ quy DFS cho các ngôn ngữ giới hạn call stack bằng cách dùng `Stack` riêng biệt.

## 23. Công cụ và Thư viện (Tools & Libraries)
- Python: `networkx`
- Java: `JGraphT`
- C++: `Boost Graph Library (BGL)`

## 24. Tổng kết (Conclusion)
Advanced Graphs đem lại các giải thuật cổ điển nhưng vô cùng tinh vi. Hiểu rõ sự kết nối giữa các mảng `low`/`discovery` trong Tarjan hay quá trình post-order của Hierholzer giúp giải quyết các bài khó trong thời gian tối ưu, thể hiện sự thành thạo thuật toán mức độ Senior / Competitive Programming.
