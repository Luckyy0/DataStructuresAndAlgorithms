# 02. Topological Sort (Sắp xếp Topo)

## 1. Giới thiệu (Introduction)
Topological Sort (Sắp xếp Topo) là một thuật toán áp dụng cho đồ thị có hướng không có chu trình (Directed Acyclic Graph - DAG). Thuật toán này sắp xếp các đỉnh của đồ thị sao cho với mọi cạnh có hướng từ đỉnh `u` đến đỉnh `v`, `u` sẽ luôn xuất hiện trước `v` trong thứ tự đã sắp xếp. Điều này rất hữu ích để giải quyết các bài toán liên quan đến sự phụ thuộc (dependencies), trong đó một số công việc phải được hoàn thành trước khi bắt đầu các công việc khác.

## 2. Directed Acyclic Graphs (DAG - Đồ thị có hướng không chu trình)
Sắp xếp Topo chỉ có thể thực hiện được trên một **DAG**.
- **Directed (Có hướng):** Mỗi cạnh biểu diễn một hướng rõ ràng từ một node này sang một node khác.
- **Acyclic (Không chu trình):** Không tồn tại bất kỳ một đường đi nào bắt đầu từ một node và quay trở lại chính node đó. Nếu có cycle (chu trình), nghĩa là có sự phụ thuộc vòng tròn, việc sắp xếp Topo là không khả thi.

## 3. Ứng dụng (Applications)
- Lên lịch công việc (Task scheduling).
- Build systems (như Make, Maven, Gradle) để xác định thứ tự biên dịch các module.
- Quản lý gói (Package dependency resolution).
- Xác định thứ tự lấy các môn học trong trường đại học (Prerequisite resolution).
- Phát hiện deadlock trong hệ điều hành (Cycle detection).

## 4. Cơ chế hoạt động (How it Works)
Sắp xếp Topo hoạt động dựa trên việc liên tục tìm các đỉnh không bị phụ thuộc bởi bất kỳ đỉnh nào khác chưa được xử lý (in-degree = 0) và loại bỏ chúng, hoặc dùng thuật toán duyệt theo chiều sâu (DFS) và đẩy các đỉnh vào một stack khi đã duyệt xong tất cả các đỉnh phụ thuộc vào nó.

## 5. Kahn's Algorithm (In-degree array + BFS Queue)
Kahn's Algorithm sử dụng phương pháp duyệt theo chiều rộng (BFS):
1. Tính **In-degree** (bán bậc vào) cho tất cả các đỉnh. (Số lượng cạnh hướng vào đỉnh đó).
2. Khởi tạo một hàng đợi (Queue) chứa tất cả các đỉnh có in-degree bằng 0.
3. Trong khi Queue không rỗng:
   - Lấy đỉnh `u` ra khỏi Queue và thêm vào mảng kết quả.
   - Với mỗi đỉnh kề `v` của `u` (tức là có cạnh `u -> v`), giảm in-degree của `v` đi 1.
   - Nếu in-degree của `v` trở thành 0, thêm `v` vào Queue.
4. Nếu kết quả chứa số đỉnh bằng tổng số đỉnh của đồ thị, ta có thứ tự Topo. Nếu ít hơn, đồ thị có chứa chu trình (cycle).

## 6. DFS Approach (Stack + Visiting States)
Cách tiếp cận DFS sử dụng đệ quy:
1. Duyệt qua từng đỉnh của đồ thị.
2. Nếu một đỉnh chưa được thăm (UNVISITED), ta gọi hàm DFS cho đỉnh đó.
3. Trong hàm DFS, đánh dấu đỉnh đang thăm (VISITING).
4. Đệ quy thăm tất cả các đỉnh kề của đỉnh hiện tại. Nếu gặp đỉnh kề đang trong trạng thái VISITING -> phát hiện cycle.
5. Sau khi thăm xong tất cả các kề, đánh dấu đỉnh là đã thăm (VISITED) và thêm đỉnh đó vào một Stack.
6. Kết quả Sắp xếp Topo chính là thứ tự pop các phần tử ra khỏi Stack.

## 7. Cycle Detection in Directed Graphs (Phát hiện chu trình)
Cycle detection cực kỳ quan trọng trong Sắp xếp Topo vì nó giúp phát hiện xem đồ thị có phải là DAG hay không.
- **Với Kahn's Algorithm:** Nếu sau khi thuật toán kết thúc, số lượng đỉnh trong mảng kết quả nhỏ hơn tổng số đỉnh ban đầu, nghĩa là có cycle.
- **Với DFS:** Sử dụng mảng trạng thái 3 màu (UNVISITED, VISITING, VISITED). Nếu đệ quy thăm phải một đỉnh đang ở trạng thái VISITING, đồ thị có cycle.

## 8. So sánh Kahn's Algorithm và DFS Approach
| Đặc điểm | Kahn's Algorithm (BFS) | DFS Approach |
|----------|------------------------|--------------|
| Phương pháp duyệt | BFS với Queue | DFS với Stack |
| Phát hiện cycle | Đếm số lượng đỉnh xử lý được | Dùng mảng trạng thái 3 màu |
| Trả về kết quả | Dễ dàng trả về thứ tự từ trái sang phải | Phải reverse hoặc dùng Stack để pop ra |
| Xử lý Lexicographical order| Rất dễ bằng cách thay Queue bằng PriorityQueue | Khó và tốn kém thời gian hơn |
| Không gian bổ sung | Queue và In-degree array | Stack và Call Stack |

## 9. Độ phức tạp (Complexity)
- **Thời gian:** $O(V + E)$ với $V$ là số đỉnh, $E$ là số cạnh. Chúng ta phải duyệt qua tất cả các đỉnh và cạnh một lần.
- **Không gian:** $O(V + E)$ cho adjacency list biểu diễn đồ thị, $O(V)$ cho in-degree array, Queue/Stack và mảng trạng thái (visited).

## 10. Implementations (Mã nguồn Java)
### Kahn's Algorithm
```java
public int[] topologicalSortKahn(int numNodes, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numNodes; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numNodes];
    
    for (int[] edge : edges) {
        adj.get(edge[0]).add(edge[1]);
        inDegree[edge[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numNodes; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int[] topoOrder = new int[numNodes];
    int idx = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        topoOrder[idx++] = u;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    
    return idx == numNodes ? topoOrder : new int[0]; // Trả về mảng rỗng nếu có cycle
}
```

### DFS Approach
```java
public int[] topologicalSortDFS(int numNodes, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numNodes; i++) adj.add(new ArrayList<>());
    for (int[] edge : edges) adj.get(edge[0]).add(edge[1]);
    
    int[] state = new int[numNodes]; // 0: UNVISITED, 1: VISITING, 2: VISITED
    Stack<Integer> stack = new Stack<>();
    
    for (int i = 0; i < numNodes; i++) {
        if (state[i] == 0) {
            if (hasCycleDFS(i, adj, state, stack)) return new int[0];
        }
    }
    
    int[] topoOrder = new int[numNodes];
    for (int i = 0; i < numNodes; i++) topoOrder[i] = stack.pop();
    return topoOrder;
}

private boolean hasCycleDFS(int u, List<List<Integer>> adj, int[] state, Stack<Integer> stack) {
    state[u] = 1; // Đang thăm
    for (int v : adj.get(u)) {
        if (state[v] == 1) return true; // Có cycle
        if (state[v] == 0 && hasCycleDFS(v, adj, state, stack)) return true;
    }
    state[u] = 2; // Đã thăm xong
    stack.push(u);
    return false;
}
```

## 11. Các biến thể (Variants)
- **Lexicographically Smallest Topological Sort:** Thay vì dùng Queue trong Kahn's, ta dùng Min Priority Queue.
- **All Topological Sorts:** Dùng Backtracking để sinh tất cả các cấu hình hợp lệ.
- **Topological Sort trên Matrix:** Khi bài toán được biểu diễn dưới dạng ma trận phụ thuộc thay vì list các cạnh.

## 12. 30 Edge Cases (Trường hợp biên)
1. Đồ thị trống (Không có node nào).
2. Đồ thị chỉ có 1 node, không có cạnh.
3. Đồ thị nhiều node nhưng không có cạnh nào (Fully disconnected).
4. Đồ thị có chu trình tự lặp (Self-loop cycle, cạnh nối 1 node về chính nó).
5. Đồ thị có chu trình bao gồm toàn bộ các node (Full cycle).
6. Đồ thị có một chu trình nhỏ nằm biệt lập, còn các phần khác là DAG.
7. Đồ thị là dạng chuỗi đường thẳng (Linear dependency / Linked list format).
8. Đồ thị hình sao (Star graph) với các cạnh hướng ra từ node trung tâm.
9. Đồ thị hình sao (Star graph) với các cạnh hướng vào một node trung tâm.
10. Rất nhiều components ngắt kết nối với nhau, mỗi component là 1 DAG.
11. Đồ thị phân đôi (Bipartite graph) có hướng hoàn chỉnh.
12. Các cạnh trùng lặp (Duplicate dependencies).
13. Đồ thị rất thưa (Sparse graph) nhưng số lượng node cực lớn.
14. Đồ thị rất dày đặc (Dense graph), số lượng cạnh xấp xỉ $O(V^2)$.
15. Không có node nào có in-degree = 0, toàn bộ đồ thị là tập hợp chu trình.
16. In-degree = 0 có nhiều nhưng out-degree toàn = 0 (bipartite 1 chiều).
17. Priority Queue được dùng nhưng lại có giá trị node âm (nếu label thay đổi).
18. Ma trận kề thay vì mảng cạnh được input.
19. Chu trình độ dài 2: node A -> node B và node B -> node A.
20. Danh sách dependencies rỗng mặc dù số node `N` rất lớn.
21. Tất cả các cạnh tạo thành hình cây có hướng (Directed Tree).
22. Node ID không liên tục (Ví dụ: 0, 3, 5 thay vì 0, 1, 2) hoặc string (Tên khóa học).
23. Đồ thị yêu cầu tạo cấu hình Topo sort dài nhất hoặc ngắn nhất có thể.
24. Danh sách các đỉnh bị cycle bao gồm cả node có in-degree = 0 không? (Không).
25. Mất cân bằng: một đỉnh phụ thuộc vào hàng ngàn đỉnh khác.
26. Mất cân bằng: hàng ngàn đỉnh phụ thuộc vào một đỉnh duy nhất.
27. Đỉnh độc lập (degree = 0) bị lẫn vào danh sách các component.
28. Cyclic dependencies phụ thuộc vào điều kiện (chẳng hạn như trọng số không quan trọng trong Topo sort).
29. Quá tải số nguyên nếu dùng in-degree array đếm sai do duplicate edges gây tràn.
30. Yêu cầu trả về *bất kỳ* cycle nào thay vì chỉ boolean có cycle hay không.

## 13. 20 Common Bugs (Lỗi thường gặp)
1. Quên không kiểm tra số đỉnh đã dequeue trong Kahn's (không nhận diện được cycle).
2. Sử dụng mảng `boolean visited` 2 trạng thái thay vì 3 trạng thái trong DFS, gây false positive về cycle.
3. Quên Push vào Stack sau vòng lặp DFS các cạnh kề.
4. Đẩy nhầm vào Queue các node có in-degree khác 0 do giảm in-degree sai.
5. Không tạo adjacency list cho các đỉnh không có trong mảng cạnh.
6. Lặp vô hạn trong DFS do thiếu base case hoặc không đánh dấu trạng thái.
7. Đọc nhầm hướng của cạnh: từ `v -> u` thay vì `u -> v`.
8. Quên giảm in-degree trong Kahn's.
9. Gặp `NullPointerException` khi truy cập node không tồn tại trong map (với string nodes).
10. Xử lý duplicate edges làm tăng sai in-degree (thêm 2 lần nhưng chỉ duyệt 1 lần).
11. Trong DFS, nếu mảng state chỉ có 2 màu (visited, unvisited) và graph dạng kim cương (A->B, A->C, B->D, C->D), DFS có thể nhầm D là cycle.
12. Đếm số node đã xử lý sai biến đếm dẫn đến kết luận sai về DAG.
13. Output array có kích thước không đúng khi có cycle (trả về full null hoặc array cũ).
14. Không xử lý được các nodes không có cạnh kết nối (Disconnected nodes bị bỏ qua).
15. Tính in-degree nhưng lại lấy giá trị từ out-degree (nhầm chiều `edge[0]` và `edge[1]`).
16. Thêm nhầm đỉnh kề vào Queue ngay cả khi in-degree của nó chưa bằng 0.
17. Dùng HashMap cho adjacency list nhưng quên khởi tạo list trống cho các node không có out-edge.
18. Dùng sai Stack pop order (pop trước thay vì reverse, hoặc pop không đúng thứ tự).
19. Concurrent Modification Exception khi xóa phần tử khỏi graph.
20. Trả về true/false ngược giữa `hasCycle` và `isPossible`.

## 14. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Hãy trình bày điểm khác biệt chính giữa việc phát hiện cycle bằng DFS so với BFS (Kahn's)?
2. Làm thế nào để tìm thứ tự Topological có thứ tự từ điển nhỏ nhất?
3. Topological Sort có hoạt động trên đồ thị vô hướng không? Tại sao?
4. Nếu đồ thị có cycle, Kahn's algorithm trả về cái gì?
5. Tại sao DFS Topological Sort cần 3 trạng thái (visiting, visited, unvisited) thay vì 2?
6. Bạn giải quyết bài toán Alias Resolution bằng Topological Sort thế nào?
7. Cấu trúc dữ liệu nào phù hợp nhất để biểu diễn graph cho Topological Sort?
8. Sắp xếp Topo có phải là duy nhất cho mọi DAG không? Khi nào thì nó duy nhất?
9. Nếu input là đồ thị rất lớn không chứa vừa trong memory, bạn sẽ dùng Topo sort như thế nào?
10. Ứng dụng thực tế của Sắp xếp Topo mà bạn từng áp dụng hoặc biết đến là gì?
11. Tính thời gian hoàn thành nhanh nhất (Longest Path) kết hợp với Topological sort ra sao?
12. Làm thế nào để sinh ra tất cả các Topological Sort hợp lệ?
13. Có thể dùng Topological sort để tìm Shortest Path trong DAG không?
14. Nếu đồ thị chứa multiple components (rời rạc), Topological Sort còn hoạt động không?
15. Có thể áp dụng Topological Sort trên cây (Tree) không?
16. Làm thế nào để loại bỏ các cạnh trùng lặp (duplicate edges) trước khi Topo Sort?
17. Bạn sẽ biểu diễn trạng thái của node trong DFS như thế nào nếu Node là String thay vì Integer?
18. Sự khác biệt giữa Topological Sort và thuật toán Dijkstra là gì?
19. Giải thích bài toán "Alien Dictionary" và làm thế nào để xây dựng Graph từ danh sách từ?
20. Nếu đồ thị có trọng số âm, ta có thể dùng Topological sort để giải quyết cái gì?

## 15. Best Practices (Thực hành tốt nhất)
- Hãy sử dụng **Kahn's Algorithm** (BFS) khi bài toán yêu cầu phát hiện cycle và chỉ đơn giản cần lấy Topo order, vì code rất dễ hiểu và không sợ tràn Stack (StackOverflowError).
- Sử dụng **DFS** khi bài toán có tính chất đệ quy nội tại hoặc cần tận dụng luôn thông tin đường đi (như Longest Path).
- Luôn cẩn thận **loại bỏ hoặc xử lý các duplicate edges** để in-degree được tính chính xác.
- Biểu diễn Graph bằng `List<List<Integer>>` cho các đỉnh liên tục $0 \to N-1$, và dùng `Map<String, List<String>>` nếu đỉnh là chuỗi ký tự.
- Chắc chắn đã xem xét các đỉnh rời rạc không có bất kỳ cạnh nào (isolated nodes).

## 16. Pattern kết hợp (Combined Patterns)
- **Topological Sort + DP (Quy hoạch động):** Áp dụng DP trên DAG để tìm longest path (Longest Increasing Path).
- **Topological Sort + Priority Queue:** Tìm thứ tự Topo lexicographically smallest (nhỏ nhất theo từ điển).
- **Topological Sort + Union Find:** Phân loại và gộp nhóm các đỉnh trước khi Topo sort (như bài Sort Items by Groups).

## 17. System Design Applications
- **Lập lịch Task (Task Scheduler):** Hadoop/Spark DAG scheduler phân rã và sắp xếp các stage thực thi.
- **Microservices Deployment:** Trình tự khởi động các dịch vụ phụ thuộc nhau (ví dụ: Auth Service phải khởi động sau Database, API Gateway khởi động cuối).
- **Data Pipeline Orchestration:** Airflow sử dụng DAG để xác định task nào trong pipeline ETL phải chạy sau.

## 18. Tài liệu tham khảo
- CLRS (Introduction to Algorithms) - Graph Algorithms.
- Khan Academy - Graphs.
- GeeksforGeeks - Topological Sorting.
- Nguồn bài tập: LeetCode Graph (Topological Sort).

## 19-24. (Reserved for detailed expansions in course lectures)
(Các phần Giảng viên trình bày thêm trên lớp bao gồm chi tiết chứng minh toán học, biểu diễn bộ nhớ, cách vẽ graph minh họa, các dạng bài thi ACM ICPC, tối ưu hóa mức độ chỉ thị cache, và tổng kết cuối khóa).
