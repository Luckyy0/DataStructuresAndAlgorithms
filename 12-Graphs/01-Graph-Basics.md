# Graph Basics (Cơ bản về Đồ thị)

## 1. Khái niệm cơ bản (Introduction)
Graph (Đồ thị) là một cấu trúc dữ liệu phi tuyến tính bao gồm các Node (hoặc đỉnh - Vertex) và các Edge (cạnh) kết nối các Node đó với nhau. Khác với Tree (là một dạng Graph đặc biệt), Graph có thể có chu trình (cycle) và một Node có thể kết nối với nhiều Node khác mà không phân cấp (hierarchical).

## 2. Cấu tạo (Anatomy)
Một Graph $G = (V, E)$ bao gồm:
- **V (Vertices)**: Tập hợp các đỉnh (Nodes).
- **E (Edges)**: Tập hợp các cạnh, mỗi cạnh nối một cặp đỉnh.

## 3. Ứng dụng thực tế (Real-world applications)
- **Mạng xã hội**: Biểu diễn người dùng (Nodes) và mối quan hệ (Edges).
- **Bản đồ trực tuyến**: Hệ thống GPS tìm đường đi ngắn nhất.
- **Web Crawler**: Biểu diễn các trang web và link liên kết.
- **Recommendation Systems**: Đề xuất sản phẩm hoặc bạn bè.

## 4. Phân loại (Types of Graphs)
- **Directed Graph (Đồ thị có hướng)**: Các cạnh có hướng, biểu diễn bằng mũi tên.
- **Undirected Graph (Đồ thị vô hướng)**: Các cạnh không có hướng, đường đi có tính chất hai chiều.
- **Weighted Graph (Đồ thị có trọng số)**: Mỗi cạnh có một trọng số (chi phí, khoảng cách).
- **Unweighted Graph (Đồ thị không trọng số)**: Các cạnh không có trọng số.
- **Cyclic Graph (Đồ thị có chu trình)**: Tồn tại ít nhất một chu trình (cycle).
- **Acyclic Graph (Đồ thị không chu trình)**: Không có chu trình nào. (VD: DAG - Directed Acyclic Graph).

## 5. Biểu diễn đồ thị (Representations)
### 5.1. Adjacency Matrix (Ma trận kề)
Ma trận 2D kích thước $V \times V$ trong đó `matrix[i][j]` mang giá trị 1 (hoặc trọng số) nếu có cạnh nối từ đỉnh `i` đến đỉnh `j`.
- Tốn $O(V^2)$ không gian bộ nhớ.
- Kiểm tra kết nối nhanh $O(1)$. Thích hợp cho Dense Graph (đồ thị dày đặc).

### 5.2. Adjacency List (Danh sách kề)
Một mảng hoặc Map, trong đó mỗi đỉnh lưu danh sách các đỉnh kề với nó.
- Tốn $O(V + E)$ không gian bộ nhớ.
- Thích hợp cho Sparse Graph (đồ thị thưa).

### 5.3. Edge List (Danh sách cạnh)
Lưu một danh sách các mảng con biểu diễn các cạnh `[u, v, weight]`.
- Tốn $O(E)$ không gian bộ nhớ.
- Thường dùng trong thuật toán Kruskal, Bellman-Ford.

## 6. Thuật toán duyệt BFS (Breadth-First Search)
Duyệt đồ thị theo chiều rộng, sử dụng **Queue**. BFS duyệt qua tất cả các đỉnh lân cận ở mức hiện tại trước khi chuyển sang mức tiếp theo. Phù hợp để tìm đường đi ngắn nhất (Shortest Path) trong Unweighted Graph.

## 7. Thuật toán duyệt DFS (Depth-First Search)
Duyệt đồ thị theo chiều sâu, sử dụng **Stack** (thường dùng Recursion). DFS đi sâu vào một nhánh hết mức có thể trước khi quay lui (Backtracking). Thường dùng để tìm chu trình, Topo Sort.

## 8. Cycle Detection (Phát hiện chu trình)
- **DFS trong Undirected Graph**: Trong lúc duyệt, nếu thăm lại một node đã được `visited` và node đó KHÔNG phải là `parent` của node hiện tại, thì đồ thị có chu trình.
- **BFS trong Undirected Graph**: Tương tự, nếu lấy một node ra khỏi Queue và xét các node kề, nếu một node kề đã được `visited` và không phải là `parent` node, thì có chu trình.

## 9. So sánh BFS và DFS (BFS vs DFS comparison)
- **Memory**: DFS thường tốn ít không gian hơn BFS vì DFS chỉ lưu trạng thái nhánh đang xét, BFS lưu toàn bộ node cùng cấp.
- **Tốc độ**: DFS tìm một solution nhanh hơn nếu target nằm sâu, BFS tìm shortest path trong unweighted graph nhanh hơn.

## 10. Ưu điểm (Advantages)
- Biểu diễn được các mối quan hệ phức tạp.
- Cấu trúc cốt lõi cho nhiều thuật toán tối ưu hóa (routing, networking).

## 11. Nhược điểm (Disadvantages)
- Độ phức tạp không gian (Space Complexity) lớn.
- Các thuật toán trên Graph có thể khó tối ưu nếu không chọn đúng Representation.

## 12. Độ phức tạp thời gian (Time Complexity)
- **DFS/BFS**: $O(V + E)$ với Adjacency List, $O(V^2)$ với Adjacency Matrix.

## 13. Độ phức tạp không gian (Space Complexity)
- **Adjacency List**: $O(V + E)$.
- **BFS Queue / DFS Stack**: $O(V)$ trong trường hợp xấu nhất.

## 14. Thiết kế hệ thống (System Design)
Trong System Design, Graph Models được dùng nhiều trong Recommendation Engines (Neo4j, GraphQL APIs) và Distributed Systems (Dependency Graphs).

## 15. Các mẫu phổ biến (Common Patterns)
- **Grid DFS/BFS**: Ma trận 2D được coi như đồ thị.
- **Topological Sorting**: Ứng dụng cho Directed Acyclic Graph (DAG) như bài toán Schedule, Build system.
- **Union Find (Disjoint Set)**: Quản lý các nhóm liên thông.

## 16. Bí kíp & Cảnh báo (Tips & Warnings)
- **Bí kíp**: Luôn luôn cẩn thận với **Cyclic Graphs**, nếu không xử lý mảng `visited` đúng cách sẽ bị Infinite Loop.
- **Cảnh báo**: Khởi tạo biến `visited` rất quan trọng. Grid đôi khi có thể thay thế `visited` bằng cách in-place modification.

## 17. 30 Edge Cases
1. Đồ thị không có đỉnh nào (Empty Graph).
2. Đồ thị chỉ có 1 đỉnh không có cạnh.
3. Graph bị ngắt kết nối hoàn toàn (Disconnected components).
4. Graph là một chuỗi đường thẳng đơn giản.
5. Đồ thị hình sao (Star graph).
6. Complete graph (Mọi đỉnh đều nối với mọi đỉnh khác).
7. Chu trình (Cycles) gồm 2 đỉnh kết nối qua lại.
8. Self-loops (cạnh nối đỉnh với chính nó).
9. Multiple edges giữa 2 đỉnh (Multigraph).
10. Đồ thị có số cạnh cực lớn (Dense Graph - Limit max memory).
11. Bipartite graph (Đồ thị hai phía).
12. Tree - Acyclic Connected Graph.
13. Đồ thị trọng số âm (Negative weight cycle).
14. Node label có giá trị trùng lặp.
15. Node IDs bắt đầu từ 0 hoặc 1 (0-indexed vs 1-indexed).
16. Khởi tạo mảng visited không đủ kích thước.
17. Start Node cho thuật toán không kết nối với bất kỳ node nào.
18. Target node không thể đến được.
19. Trọng số cạnh là 0 (Zero weight edges).
20. Đồ thị chu kỳ khép kín (Cycle graph).
21. Truy cập mảng Adjacency ngoài giới hạn biên.
22. Grid graphs với nhiều blockages (tất cả ô là tường).
23. Ma trận kề quá lớn vượt RAM (vd: 10^5 x 10^5).
24. Stack overflow khi dùng Recursion DFS cho chuỗi đồ thị quá sâu.
25. Mất kiểm soát memory khi BFS Queue nở quá lớn trên đồ thị rộng.
26. Phân tích Shortest Path trong mạng có chu trình âm.
27. Đồ thị có DAG nhưng đường đi dài nhất lại rất sâu.
28. Start Node và Target Node là trùng nhau.
29. Cấu trúc DAG lồng nhau.
30. Disconnected nodes trong Directed Graph.

## 18. 20 Common Bugs
1. **Quên mảng visited**: Dẫn đến vòng lặp vô hạn.
2. **Khởi tạo mảng kề bị null reference**.
3. **Thêm cạnh một chiều thay vì hai chiều** trong Undirected Graph.
4. **Đánh dấu visited sai vị trí trong BFS**: Đánh dấu sau khi pop thay vì lúc push.
5. **Dùng nhầm Stack cho BFS hoặc Queue cho DFS**.
6. **Lỗi ConcurrentModificationException** khi duyệt Graph.
7. **Không xét tất cả Connected Components**.
8. **Điều kiện biên sai trong Grid** (Out of bounds).
9. **Backtracking không bỏ đánh dấu (Un-visit)**.
10. **Lỗi StackOverflow với DFS đệ quy quá sâu**.
11. **Tính sai chiều dài/khoảng cách**: Khởi tạo khoảng cách là 1 thay vì 0.
12. **Tính toán số lượng level trong BFS sai cách**.
13. **Xử lý chu trình có hướng sai**: Cần mảng visited 3 trạng thái.
14. **So sánh object lầm với equal `==` thay vì `.equals()`**.
15. **Xây dựng Graph từ mảng cạnh sai kích thước Node max**.
16. **Dijkstra dùng HashSet thay vì PriorityQueue**.
17. **Không bắt lỗi khi danh sách kề rỗng**.
18. **Không làm sạch biến toàn cục** trong nhiều Testcases.
19. **Bỏ sót Self-Loop**.
20. **Lầm lẫn Adjacency Matrix với Grid BFS**.

## 19. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Sự khác biệt giữa Tree và Graph là gì?
2. Ứng dụng thực tế của Topological Sort là gì?
3. Khi nào thì dùng DFS, khi nào thì dùng BFS?
4. Thuật toán Dijkstra có làm việc với trọng số cạnh âm không? Tại sao?
5. Phát hiện chu trình trong Directed Graph như thế nào?
6. Phát hiện chu trình trong Undirected Graph như thế nào?
7. Bipartite Graph là gì và làm sao để kiểm tra?
8. Tại sao trong BFS, ta đánh dấu visited lúc thêm vào queue thay vì lúc lấy ra?
9. Phân biệt Adjacency List và Adjacency Matrix.
10. Hệ thống GPS tìm đường sử dụng thuật toán cơ bản nào?
11. Làm thế nào để sao chép (clone) một Graph?
12. DAG là gì?
13. Giải thích thuật toán Floyd-Warshall.
14. Union Find là gì và tại sao lại dùng để kiểm tra chu trình?
15. MST (Minimum Spanning Tree) là gì? Trình bày Prim và Kruskal.
16. Sự khác biệt giữa Eulerian Path và Hamiltonian Path.
17. Cấu trúc dữ liệu nào được sử dụng trong Topological Sort?
18. Tìm tất cả các Connected Components trong Unweighted Graph như thế nào?
19. Có thể triển khai DFS không dùng đệ quy không?
20. Flow Network là gì và làm thế nào để tìm Max Flow?

## 20. Cheat Sheet
- **Duyệt (Unweighted)**: BFS/DFS. Time: $O(V+E)$.
- **Shortest Path (Unweighted)**: BFS.
- **Tìm chu trình (Undirected)**: DFS/BFS hoặc Union Find.
- **Tìm chu trình (Directed)**: DFS visited states.
- **Dependencies**: Topological Sort.

## 21. Tài liệu tham khảo (References)
- CLRS
- Sedgewick Algorithms

## 22. Bài tập thực hành (Practice Problems)
Xem file `01-Graph-Basics-Problems.md` để áp dụng với 30 bài tập.

## 23. Ghi chú cá nhân (Personal Notes)
- Luôn kiểm tra tính liên thông khi xử lý đồ thị vô hướng.
- Grid BFS là dạng rất phổ biến trong phỏng vấn vòng 1, 2.

## 24. Phụ lục (Appendix)
- Graph API chuẩn.
