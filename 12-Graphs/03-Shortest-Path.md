# Chuyên đề: Shortest Path Algorithms (Thuật Toán Đường Đi Ngắn Nhất)

## 1. Lời nói đầu
Bài toán tìm đường đi ngắn nhất (Shortest Path) là một trong những chủ đề quan trọng và phổ biến nhất trong Lý thuyết đồ thị (Graph Theory). Nó không chỉ xuất hiện dày đặc trong các cuộc thi lập trình, phỏng vấn kỹ thuật mà còn là nền tảng của vô số hệ thống thực tế như bản đồ định vị, định tuyến mạng internet, và tối ưu hóa chuỗi cung ứng. 

## 2. Khái niệm cơ bản
- **Đường đi ngắn nhất (Shortest Path)**: Là đường đi giữa hai đỉnh sao cho tổng trọng số của các cạnh trên đường đi là nhỏ nhất.
- **Trọng số (Weight)**: Chi phí đi qua một cạnh, có thể là khoảng cách, thời gian, tiền bạc, hoặc năng lượng. Trọng số có thể dương, âm hoặc bằng 0.
- **Chu trình âm (Negative Cycle)**: Là một chu trình trong đồ thị có tổng trọng số các cạnh là một số âm. Nếu tồn tại chu trình âm trên đường đi, khoảng cách ngắn nhất có thể tiến tới âm vô cực.
- **Relaxation (Thư giãn cạnh)**: Là thao tác kiểm tra xem liệu việc đi qua một cạnh $(u, v)$ có làm giảm khoảng cách ngắn nhất từ đỉnh nguồn đến $v$ hay không. Dựa trên công thức: `if (dist[u] + weight(u, v) < dist[v]) -> dist[v] = dist[u] + weight(u, v)`.

## 3. Ứng dụng thực tế
- **Hệ thống định vị (GPS & Navigation)**: Google Maps sử dụng các biến thể của Dijkstra và A* để tìm đường đi nhanh nhất giữa hai địa điểm.
- **Mạng máy tính (Computer Networking)**: Giao thức định tuyến như OSPF và BGP dựa trên các thuật toán Shortest Path để truyền dữ liệu.
- **Tối ưu hóa tài chính**: Tìm kiếm cơ hội arbitrage (kinh doanh chênh lệch giá) trên thị trường ngoại hối bằng cách phát hiện chu trình âm.
- **Hệ thống hàng không**: Tìm các chặng bay rẻ nhất có ít điểm dừng nhất.

## 4. Phân loại
Các bài toán Shortest Path thường được chia làm hai loại chính:
1. **Single-Source Shortest Path (SSSP)**: Tìm đường đi ngắn nhất từ một đỉnh nguồn (source node) đến tất cả các đỉnh còn lại. Thuật toán tiêu biểu: BFS (không trọng số), Dijkstra, Bellman-Ford, SPFA.
2. **All-Pairs Shortest Path (APSP)**: Tìm đường đi ngắn nhất giữa mọi cặp đỉnh trong đồ thị. Thuật toán tiêu biểu: Floyd-Warshall, Johnson's Algorithm.

## 5. Cấu trúc dữ liệu
- **Adjacency List (Danh sách kề)**: Phù hợp nhất cho SSSP (Dijkstra, Bellman-Ford). Thường biểu diễn bằng `List<List<int[]>>` trong Java, hoặc mảng các `List`.
- **Adjacency Matrix (Ma trận kề)**: Phù hợp cho đồ thị dày (Dense Graph) và thuật toán Floyd-Warshall.
- **Priority Queue (Hàng đợi ưu tiên)**: Cần thiết để tối ưu thuật toán Dijkstra (Min-Heap).
- **Queue**: Sử dụng trong thuật toán SPFA hoặc BFS.

## 6. Thuật toán Dijkstra (Priority Queue)
- **Đặc điểm**: Chỉ hoạt động đúng với đồ thị có trọng số không âm (non-negative weights). Khá giống với BFS nhưng dùng Min-Heap thay vì Queue thông thường.
- **Độ phức tạp**: $O(E \log V)$ nếu dùng Binary Heap (PriorityQueue trong Java). $O(V \log V + E)$ nếu dùng Fibonacci Heap.
- **Cơ chế**: Tại mỗi bước, chọn đỉnh có khoảng cách tạm tính nhỏ nhất từ nguồn (thông qua PriorityQueue) và thực hiện relaxation cho các đỉnh kề của nó.

## 7. Thuật toán Bellman-Ford
- **Đặc điểm**: Hoạt động với cả đồ thị có trọng số âm, và quan trọng nhất là có thể **phát hiện chu trình âm** (detect negative cycles).
- **Độ phức tạp**: Thời gian $O(V \times E)$, Không gian $O(V)$.
- **Cơ chế**: Lặp $V-1$ lần việc thực hiện relaxation trên *tất cả* các cạnh của đồ thị. Nếu ở lần lặp thứ $V$ mà vẫn có cạnh được thư giãn, chứng tỏ đồ thị có chu trình âm.

## 8. Thuật toán Floyd-Warshall
- **Đặc điểm**: Là thuật toán Quy hoạch động (Dynamic Programming) để giải bài toán APSP. Rất dễ cài đặt.
- **Độ phức tạp**: Thời gian $O(V^3)$, Không gian $O(V^2)$.
- **Cơ chế**: Sử dụng một đỉnh trung gian $k$ để kiểm tra xem có đường đi từ $i$ đến $j$ qua $k$ ngắn hơn đường đi trực tiếp hay không: `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`.

## 9. Thuật toán SPFA (Shortest Path Faster Algorithm)
- **Đặc điểm**: Là sự cải tiến của Bellman-Ford bằng cách sử dụng một Queue để chỉ xét các đỉnh vừa có sự thay đổi khoảng cách. Thường hoạt động rất nhanh trong thực tế ($O(E)$ trung bình), nhưng worst-case vẫn là $O(V \times E)$.
- **Ứng dụng**: Phù hợp cho đồ thị thưa có trọng số âm, thay thế cho Bellman-Ford thông thường.
- **Phát hiện chu trình âm**: Đếm số lần một đỉnh được đẩy vào Queue, nếu lớn hơn $V-1$ thì có chu trình âm.

## 10. So sánh các thuật toán

| Thuật toán | Loại bài toán | Trọng số âm | Phát hiện chu trình âm | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- | --- |
| BFS | SSSP (không trọng số) | Không | Không | $O(V + E)$ | $O(V)$ |
| Dijkstra | SSSP | Không | Không | $O(E \log V)$ | $O(V + E)$ |
| Bellman-Ford | SSSP | Có | Có | $O(V \times E)$ | $O(V)$ |
| SPFA | SSSP | Có | Có | Avg $O(E)$, Worst $O(V \times E)$ | $O(V)$ |
| Floyd-Warshall| APSP | Có | Có | $O(V^3)$ | $O(V^2)$ |

## 11. Code minh họa cơ bản

**Dijkstra Algorithm (Java):**
```java
public int[] dijkstra(int V, List<List<int[]>> adj, int src) {
    int[] dist = new int[V];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[src] = 0;
    
    // Đỉnh {đỉnh, khoảng cách từ src}
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
    pq.offer(new int[]{src, 0});
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int u = curr[0];
        int d = curr[1];
        
        // Bỏ qua nếu tìm thấy đường cũ dài hơn đã có
        if (d > dist[u]) continue;
        
        for (int[] edge : adj.get(u)) {
            int v = edge[0];
            int weight = edge[1];
            
            if (dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                pq.offer(new int[]{v, dist[v]});
            }
        }
    }
    return dist;
}
```

## 12. Các biến thể (Variations)
1. **0-1 BFS**: Dùng cho đồ thị chỉ có trọng số cạnh là 0 hoặc 1. Dùng Deque thay cho PriorityQueue (cạnh 0 đưa lên đầu, cạnh 1 đưa xuống cuối), độ phức tạp là $O(V+E)$.
2. **Multi-Source Shortest Path**: Đẩy tất cả các đỉnh nguồn vào Queue/PQ ban đầu với khoảng cách 0 (cách hoạt động như BFS đa nguồn).
3. **A* Search**: Dijkstra kết hợp với hàm Heuristic để ưu tiên tìm kiếm về hướng đích, thường dùng trong bản đồ, grid (đường mê cung).
4. **Shortest Path with States**: Dijkstra kết hợp DP, state không chỉ là đỉnh hiện tại mà còn thêm ràng buộc (vd: số trạm trung chuyển, lượng nhiên liệu).

## 13. Edge Cases (30 trường hợp cần lưu ý)
1. Đồ thị trống (Empty graph - 0 đỉnh, 0 cạnh).
2. Đồ thị chỉ có 1 đỉnh duy nhất.
3. Không tồn tại đường đi từ node nguồn tới node đích (Disconnect).
4. Đồ thị chứa chu trình âm (Negative Weight Cycle).
5. Đồ thị có các cạnh trọng số âm nhưng không tạo thành chu trình âm.
6. Đồ thị vô hướng (Undirected Graph) có chứa cạnh trọng số âm.
7. Tất cả các cạnh đều có trọng số là 0.
8. Đồ thị ngắt kết nối gồm nhiều thành phần riêng biệt.
9. Nguồn (Source) và đích (Destination) là cùng một đỉnh.
10. Đa đồ thị (Multigraph): Tồn tại nhiều cạnh nối trực tiếp giữa cùng một cặp đỉnh.
11. Đồ thị có chứa vòng khuyên (Self-loops) tại các đỉnh.
12. Tổng chi phí đường đi quá lớn gây tràn số kiểu nguyên (Integer Overflow).
13. Đồ thị đầy đủ (Complete Graph), số cạnh đạt tới $V^2$.
14. Đồ thị thưa (Sparse Graph), số cạnh xấp xỉ số đỉnh $O(V)$.
15. Đồ thị dạng chuỗi (Line graph).
16. Đồ thị dạng vòng (Ring graph).
17. Đồ thị hình sao (Star graph).
18. Đồ thị hai phía (Bipartite Graph).
19. Trọng số cực lớn nhưng không bị tràn (cần cân nhắc kiểu `long`).
20. Mạng lưới dạng lưới vuông (Grid graph) có chứa vật cản.
21. Cập nhật PriorityQueue liên tục gây rác (Memory / TLE nếu không có mảng visited/khoảng cách check).
22. Node đích không tồn tại (Out of bound index).
23. Đồ thị dạng cây (Tree) - chỉ cần BFS/DFS hoặc tìm LCA là đủ.
24. Yêu cầu in ra tất cả các đường đi ngắn nhất thay vì chỉ tính khoảng cách.
25. Mọi trọng số đều bằng nhau (Có thể hạ cấp từ Dijkstra xuống BFS).
26. Quá nhiều node trùng lặp có cùng khoảng cách vào hàng đợi PriorityQueue.
27. Đỉnh độc lập không có cạnh đi tới hoặc đi ra.
28. Chỉ định max stops / giới hạn cạnh (như k stops) khiến Dijkstra không tìm ra đáp án đúng ngay.
29. Khoảng cách bắt đầu cho thuật toán DP lớn hơn mức cho phép nhưng nhỏ hơn MAX_VALUE gây lỗi tràn khi cộng.
30. Yêu cầu tính theo tích (Path with Maximum Probability) thay vì tính tổng trọng số.

## 14. Common Bugs (20 lỗi phổ biến)
1. **Integer Overflow**: Lấy `Integer.MAX_VALUE` đem cộng với một trọng số dương dẫn đến tràn số thành số âm. Khắc phục: Khởi tạo giá trị vô cực là một số đủ lớn như `1e9` hoặc kiểm tra `dist[u] != Integer.MAX_VALUE`.
2. **Quên `continue` trong Dijkstra**: Nếu bỏ qua bước kiểm tra `d > dist[u]`, chương trình sẽ vẫn chạy nhưng có thể bị TLE trong trường hợp đồ thị lớn vì xử lý dư thừa các phiên bản cũ của đỉnh.
3. **Quên đánh dấu visited hoặc update state**: Dẫn đến vòng lặp vô hạn nếu đồ thị có chu trình.
4. **Dùng nhầm cấu trúc dữ liệu**: Ví dụ, xài Max-Heap thay vì Min-Heap trong Java (`PriorityQueue` mặc định là Min-Heap, nhưng dễ nhầm khi tự implement Comparator).
5. **Bellman-Ford chạy vòng lặp sai**: Cần duyệt đúng V-1 lần cho tất cả các cạnh. Không duyệt đủ sẽ dẫn đến sai khoảng cách.
6. **Thứ tự sai trong Floyd-Warshall**: Phải luôn duyệt k (trung gian) ở vòng lặp ngoài cùng, i và j ở trong. Duyệt i, j ở ngoài sẽ cho kết quả sai.
7. **Khởi tạo sai ma trận cho Floyd-Warshall**: Quên khởi tạo đường chéo chính `dist[i][i] = 0`, hoặc khởi tạo sai trọng số các cặp đỉnh không có cạnh là vô cực nhưng lại quên xử lý tránh tràn số.
8. **SPFA quên reset `inQueue`**: Khi pop một node ra khỏi Queue, phải set `inQueue[u] = false`.
9. **Khởi tạo mảng khoảng cách bằng 0**: Phải khởi tạo bằng vô cực (ngoại trừ source = 0).
10. **Đồ thị vô hướng quên add chiều ngược lại**: Đồ thị vô hướng `u - v`, cần add cả `(u, v)` và `(v, u)` vào danh sách kề.
11. **Xử lý Multigraph sai**: Giữa 2 node có 2 cạnh `(u, v, 5)` và `(u, v, 10)`. Cần giữ cạnh có trọng số 5 thay vì ghi đè bằng 10.
12. **Dijkstra không reset cấu trúc dữ liệu cho nhiều testcase**: Quên clear danh sách kề hoặc mảng visited.
13. **Tìm đường đi bằng BFS trên đồ thị có trọng số**: BFS thông thường chỉ đúng khi trọng số tất cả các cạnh bằng 1.
14. **Quên truy vết ngược**: Khi cần in ra đường đi, truy vết qua mảng `parent[]` thường được từ Đích về Nguồn, phải nhớ đảo ngược list lại.
15. **Bellman-Ford giới hạn bước (K Stops)**: Khi giải bài Cheapest Flights within K Stops, nếu cập nhật trực tiếp trên một mảng `dist`, sẽ lấy khoảng cách đã cập nhật ở cùng bước. Cần có một mảng clone tạm để lưu giá trị của bước trước.
16. **Dijkstra trên đồ thị có trọng số âm**: Dijkstra sẽ hoạt động sai nếu đồ thị có cạnh trọng số âm (dù không có chu trình âm).
17. **SPFA đếm chu trình âm sai logic**: Đếm số lần vào queue của một node vượt quá V thì báo có chu trình âm (cần đếm chính xác tại node đó).
18. **Nhầm lẫn giữa 0-indexed và 1-indexed**: Rất dễ gây ArrayIndexOutOfBoundsException.
19. **Thất bại trong đồ thị không liên thông**: Floyd Warshall đôi khi sẽ tính khoảng cách thành `INF - xyz` nếu không check điều kiện vô cực.
20. **Không kiểm tra đồ thị rỗng/null**: Crash ngay từ bước đọc đồ thị.

## 15. Interview Questions (20 câu hỏi)
1. Hãy mô tả ý tưởng đằng sau thuật toán Dijkstra và lý do tại sao nó lại là thuật toán Greedy (Tham lam)?
2. Tại sao Dijkstra không hoạt động đúng trên đồ thị có trọng số âm?
3. Thuật toán Bellman-Ford xử lý trọng số âm như thế nào? Giải thích chi tiết bước Relaxation.
4. Trình bày cách phát hiện chu trình âm bằng Bellman-Ford và thuật toán SPFA.
5. Sự khác biệt chính giữa Floyd-Warshall và hai thuật toán SSSP còn lại là gì? Tại sao Floyd-Warshall dùng Quy hoạch động?
6. Tại sao thứ tự vòng lặp của Floyd-Warshall lại bắt buộc là K, I, J?
7. Bạn sẽ chọn thuật toán nào cho một đồ thị vô hướng, trọng số cạnh đều là 1? (Gợi ý: BFS).
8. Bài toán tìm đường đi an toàn nhất (Path with Maximum Probability) có thể được giải quyết bằng thuật toán nào và cần biến đổi gì?
9. "Cheapest Flights Within K Stops" - Tại sao lại phải biến tấu thuật toán Dijkstra hoặc Bellman-Ford để giải quyết bài này?
10. Làm thế nào để giải quyết bài toán Multi-source Shortest Path (Tìm đường ngắn nhất từ nhiều nguồn cùng lúc đến mọi nơi)?
11. Thuật toán A* khác biệt với Dijkstra ở điểm nào? Vai trò của hàm Heuristic là gì?
12. Làm sao để tìm đường đi ngắn thứ hai (Second shortest path) trong đồ thị?
13. Nếu bạn được cho một ma trận Grid, bạn có coi nó là đồ thị không? Làm thế nào để duyệt nó hiệu quả nhất?
14. Thuật toán 0-1 BFS được ứng dụng trong trường hợp nào và cấu trúc dữ liệu nào được sử dụng thay thế PriorityQueue?
15. Cách tốt nhất để đếm số lượng đường đi ngắn nhất từ Source tới Destination là gì?
16. Làm thế nào để in ra chu trình âm nếu Bellman-Ford phát hiện ra nó?
17. Sự khác biệt giữa PriorityQueue và TreeSet khi cài đặt thuật toán Dijkstra trong Java?
18. Thuật toán Johnson's Algorithm hoạt động như thế nào cho All-Pairs Shortest Path với đồ thị thưa?
19. Giải thích khái niệm "Shortest Path Tree".
20. Tại sao DFS hiếm khi được sử dụng cho việc tìm đường đi ngắn nhất?

## 16. Kinh nghiệm thực chiến
- **Đọc kỹ đề**: Luôn kiểm tra xem đồ thị có hướng (Directed) hay vô hướng (Undirected), trọng số có âm hay không. Trọng số có lớn gây tràn `Integer` không.
- **Hạ cấp bài toán**: Nếu đề bài chỉ hỏi đường đi có số cạnh nhỏ nhất -> BFS. Nếu đồ thị là DAG (Directed Acyclic Graph) -> Dùng Topological Sort + Relaxation $O(V+E)$, nhanh hơn Dijkstra.
- **Dijkstra với PriorityQueue**: Trong Java, thêm thẳng vào PriorityQueue mà không cần phải dùng `decrease-key` (do Java không hỗ trợ `decrease-key` trong `PriorityQueue` hiệu quả), chỉ cần xử lý node bị thừa bằng check `visited` hoặc `d > dist[u]`.
- **Khai báo hằng số Vô cực**: Khuyên dùng `int INF = (int) 1e9;` thay vì `Integer.MAX_VALUE` để đề phòng cộng vào bị tràn số âm.

## 17. Tối ưu hóa (Optimization)
- **Bidirectional Dijkstra**: Bắt đầu duyệt Dijkstra cùng lúc từ Cả Nguồn (Source) và Đích (Destination). Gặp nhau ở giữa sẽ giúp tiết kiệm một nửa số đỉnh phải thăm.
- **A* Search**: Khi đã biết tọa độ thực tế trên Grid, kết hợp Manhattan/Euclidean distance để điều hướng.
- **Mảng tĩnh**: Nếu số đỉnh nhỏ (< 10^5), dùng mảng cho `head, to, next, weight` (Forward Star / Chuỗi kề tĩnh) thay vì `ArrayList` để giảm memory overhead và Cache Miss.

## 18. Mở rộng (Extensions)
- **Johnson's Algorithm**: Kết hợp Bellman-Ford và Dijkstra. Được dùng cho đồ thị thưa, bài toán APSP, có thể xử lý trọng số âm tốt hơn Floyd-Warshall.
- **Suurballe's Algorithm**: Tìm hai (hoặc nhiều hơn) đường đi ngắn nhất không giao nhau (Disjoint paths).
- **Min-Max Path / Bottleneck Path**: Tìm đường đi mà cạnh lớn nhất trên đường đi đó là nhỏ nhất (hoặc ngược lại). (Có thể dùng Dijkstra hoặc Kruskal MST).

## 19. Debugging
- **Vẽ đồ thị tay**: Khi test fail, tạo một test case đồ thị nhỏ (3-4 đỉnh) và theo dõi sự thay đổi của mảng `dist` sau mỗi vòng lặp.
- **Kiểm tra đồ thị ban đầu**: Thêm lệnh in toàn bộ Adjacency List ra để kiểm tra xem đã tạo cạnh hai chiều chưa, có cạnh nào bị sót trọng số không.
- **In giá trị của Queue/PriorityQueue**: Cẩn thận theo dõi những đỉnh nào được pop ra và khi nào chúng bị từ chối do lớn hơn `dist` hiện tại.

## 20. Best Practices
1. Luôn sử dụng kiểu Dữ liệu `long` cho mảng `dist` nếu cận trên trọng số là $10^9$ và số lượng đỉnh lớn.
2. Tách riêng logic tạo đồ thị (Graph Builder) và logic thuật toán để dễ bảo trì và đọc.
3. Sử dụng Object Node (ví dụ `class State { int node, int cost; }`) thay vì mảng `int[]` để code rõ ràng và ít lỗi typo hơn khi dùng PriorityQueue.

## 21. Tài liệu tham khảo
- Introduction to Algorithms (CLRS) - Chương 24, 25.
- CP-Algorithms (Shortest Paths)
- GeeksforGeeks, LeetCode Discuss System.

## 22. Cheat Sheet
- SSSP, No Negative Weights -> **Dijkstra** $O(E \log V)$
- SSSP, Negative Weights -> **Bellman-Ford** $O(VE)$ hoặc **SPFA** $O(kE)$
- APSP, Dense Graph -> **Floyd-Warshall** $O(V^3)$
- SSSP, Unweighted -> **BFS** $O(V+E)$
- SSSP, DAG -> **Topological Sort + DP** $O(V+E)$
- Grid, Cạnh Trọng Số 0 & 1 -> **0-1 BFS** $O(V+E)$

## 23. Mindmap/Tóm tắt
- **Bài toán**: Tìm chi phí nhỏ nhất / tối ưu nhất giữa 2 điểm mạng lưới.
- Xác định kiểu trọng số -> Chọn đúng Thuật Toán.
- Nắm chắc Dijkstra vì nó được dùng trong 80% các bài phỏng vấn về Shortest Path.
- Hiểu State/Dynamic Programming kết hợp Dijkstra.

## 24. Lời kết
Nắm vững thuật toán Shortest Path tương đương với việc bạn đã trang bị một trong những công cụ mạnh mẽ nhất trong giải thuật. Cần luyện tập nhuần nhuyễn trên các biến thể (states, grid) hơn là chỉ nhớ công thức cơ bản, bởi bài tập phỏng vấn thường giấu Dijkstra dưới nhiều lớp ngụy trang khó đoán.
