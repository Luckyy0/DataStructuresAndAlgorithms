# Minimum Spanning Tree và Disjoint Set Union

## 1. Mở đầu / Giới thiệu
Cây khung nhỏ nhất (Minimum Spanning Tree - MST) và Tập hợp rời rạc (Disjoint Set Union - DSU / Union-Find) là hai chủ đề quan trọng trong lý thuyết đồ thị.
- **MST** là một đồ thị con của một đồ thị liên thông, có trọng số, vô hướng, sao cho nó bao trùm tất cả các đỉnh, không có chu trình và tổng trọng số của các cạnh là nhỏ nhất.
- **DSU** là một cấu trúc dữ liệu mạnh mẽ giúp quản lý một tập hợp các phần tử được chia thành các tập con không giao nhau (disjoint sets), rất hữu ích trong việc tìm MST (đặc biệt là thuật toán Kruskal) và giải quyết các bài toán về tính liên thông động.

## 2. Khái niệm cơ bản & Thuật ngữ
- **Spanning Tree (Cây khung)**: Một đồ thị con liên thông không có chu trình chứa tất cả các đỉnh của đồ thị gốc.
- **Minimum Spanning Tree (MST)**: Cây khung có tổng trọng số các cạnh nhỏ nhất.
- **Disjoint Set (Tập hợp rời rạc)**: Một cấu trúc dữ liệu theo dõi một tập hợp các phần tử được chia thành một số lượng các tập con không giao nhau.
- **Find**: Phép toán tìm đại diện (root) của tập hợp chứa một phần tử nhất định.
- **Union**: Phép toán gộp hai tập hợp rời rạc thành một tập hợp duy nhất.
- **Path Compression**: Kỹ thuật tối ưu cho thao tác `Find`, làm phẳng cấu trúc cây bằng cách trỏ trực tiếp các node trên đường đi tới node gốc.
- **Union by Rank / Size**: Kỹ thuật tối ưu cho thao tác `Union`, luôn gán cây thấp hơn (hoặc nhỏ hơn) dưới cây cao hơn (hoặc lớn hơn).

## 3. Ứng dụng thực tế
- **Thiết kế mạng**: Thiết kế mạng viễn thông, mạng điện, hoặc mạng máy tính với chi phí dây cáp nhỏ nhất (MST).
- **Phân cụm dữ liệu (Clustering)**: Thuật toán phân cụm Single-linkage sử dụng Kruskal.
- **Xử lý ảnh**: Phân đoạn hình ảnh (Image segmentation).
- **Phát hiện chu trình**: DSU được dùng để phát hiện chu trình trong đồ thị vô hướng một cách nhanh chóng.
- **Mạng xã hội**: Xác định xem hai người dùng có nằm trong cùng một mạng lưới quan hệ hay không.

## 4. Phân tích chi tiết (Cấu trúc, Cơ chế, Các phép toán)
### Disjoint Set Union (DSU)
- Duy trì một mảng `parent[]` để lưu node cha của mỗi phần tử. Ban đầu `parent[i] = i`.
- Có thể kết hợp mảng `rank[]` hoặc `size[]` để tối ưu thao tác Union.

### Kruskal's Algorithm
1. Sắp xếp tất cả các cạnh theo thứ tự trọng số tăng dần.
2. Khởi tạo một DSU cho tất cả các đỉnh.
3. Lặp qua các cạnh đã sắp xếp: Nếu hai đỉnh của cạnh không thuộc cùng một tập hợp (không tạo thành chu trình), thêm cạnh đó vào MST và gộp hai tập hợp lại.

### Prim's Algorithm
1. Bắt đầu từ một đỉnh bất kỳ, thêm nó vào tập hợp MST.
2. Dùng một PriorityQueue (Min-Heap) để lưu các cạnh kết nối từ tập MST ra ngoài.
3. Liên tục lấy cạnh nhỏ nhất từ Min-Heap: Nếu đỉnh đích chưa thuộc MST, thêm nó vào MST, cập nhật tổng trọng số, và đẩy tất cả các cạnh kề của đỉnh mới vào Min-Heap.

## 5. Cài đặt chi tiết (Code Java)

### DSU (Union-Find) với Path Compression và Union by Rank
```java
class DSU {
    int[] parent;
    int[] rank;

    public DSU(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            rank[i] = 1;
        }
    }

    public int find(int i) {
        if (parent[i] == i)
            return i;
        // Path Compression
        return parent[i] = find(parent[i]);
    }

    public boolean union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        
        if (rootI == rootJ) return false;
        
        // Union by Rank
        if (rank[rootI] < rank[rootJ]) {
            parent[rootI] = rootJ;
        } else if (rank[rootI] > rank[rootJ]) {
            parent[rootJ] = rootI;
        } else {
            parent[rootJ] = rootI;
            rank[rootI]++;
        }
        return true;
    }
}
```

### Thuật toán Kruskal
```java
import java.util.*;

class Edge implements Comparable<Edge> {
    int src, dest, weight;
    public Edge(int src, int dest, int weight) {
        this.src = src; this.dest = dest; this.weight = weight;
    }
    public int compareTo(Edge compareEdge) {
        return this.weight - compareEdge.weight;
    }
}

public int kruskalMST(int vertices, List<Edge> edges) {
    Collections.sort(edges);
    DSU dsu = new DSU(vertices);
    int mstWeight = 0;
    int edgesTaken = 0;
    
    for (Edge edge : edges) {
        if (dsu.union(edge.src, edge.dest)) {
            mstWeight += edge.weight;
            edgesTaken++;
            if (edgesTaken == vertices - 1) break;
        }
    }
    return mstWeight;
}
```

### Thuật toán Prim
```java
import java.util.*;

public int primMST(int vertices, List<List<int[]>> adj) {
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    boolean[] inMST = new boolean[vertices];
    int mstWeight = 0;
    
    // [weight, node]
    pq.offer(new int[]{0, 0});
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int weight = curr[0];
        int u = curr[1];
        
        if (inMST[u]) continue;
        inMST[u] = true;
        mstWeight += weight;
        
        for (int[] neighbor : adj.get(u)) {
            int v = neighbor[0];
            int w = neighbor[1];
            if (!inMST[v]) {
                pq.offer(new int[]{w, v});
            }
        }
    }
    return mstWeight;
}
```

## 6. Các thuật toán & Kỹ thuật cốt lõi
- **Kruskal**: Dựa trên cách tiếp cận tham lam (Greedy) chọn cạnh nhỏ nhất trước, phù hợp cho Sparse Graph (Đồ thị thưa).
- **Prim**: Dựa trên cách tiếp cận tham lam phát triển từ một đỉnh, phù hợp cho Dense Graph (Đồ thị dày).
- **Path Compression**: Tối ưu DSU, làm giảm độ sâu của cây.
- **Union by Rank/Size**: Giữ cho cây cân bằng khi gộp.

## 7. Đánh giá độ phức tạp (Thời gian & Không gian)
**DSU**:
- **Time Complexity**: `O(α(N))` cho các thao tác Find/Union, trong đó `α` là hàm Inverse Ackermann, thực tế coi như `O(1)`.
- **Space Complexity**: `O(N)` cho mảng parent và rank.

**Kruskal**:
- **Time Complexity**: `O(E log E + E α(V))`. Sắp xếp cạnh mất `O(E log E)`.
- **Space Complexity**: `O(V + E)`.

**Prim**:
- **Time Complexity**: `O(E log V)`. Sử dụng Min-Heap (Priority Queue).
- **Space Complexity**: `O(V + E)` lưu trữ đồ thị và Heap.

## 8. So sánh với các Cấu trúc dữ liệu/Thuật toán khác
- **Kruskal vs Prim**: Kruskal sắp xếp toàn bộ cạnh và hoạt động tốt trên đồ thị thưa (ít cạnh). Prim dùng Heap và hiệu quả hơn trên đồ thị dày (nhiều cạnh), nhất là với Fibonacci Heap `O(E + V log V)`.
- **DSU vs DFS/BFS cho kiểm tra chu trình**: DSU chỉ dùng cho đồ thị vô hướng, nhưng có thể hoạt động hiệu quả khi các cạnh được thêm vào online (động). DFS tốt hơn cho phát hiện chu trình đồ thị có hướng.

## 9. Cạm bẫy & Lỗi thường gặp (20 Common Bugs)
1. Quên khởi tạo mảng `parent[i] = i`.
2. Trong thuật toán Kruskal, không kiểm tra điều kiện thoát sớm (đã lấy `V-1` cạnh).
3. Sử dụng DSU cho đồ thị có hướng để phát hiện chu trình (Sai cơ bản).
4. Khởi tạo nhầm biến `mstWeight` vượt giới hạn của `int` (Cần dùng `long`).
5. Hàm `find` cập nhật nhầm `parent[i]` mà không đệ quy lên root.
6. Lỗi IndexOutOfBounds do đồ thị bắt đầu bằng index 1 thay vì 0.
7. Thuật toán Prim: Quên đánh dấu mảng `inMST[u] = true` sau khi pop từ hàng đợi.
8. Thuật toán Prim: Đánh dấu `inMST[v] = true` khi đẩy vào PriorityQueue (phải đánh dấu khi pop ra).
9. Lỗi trong Union by Rank: Không tăng rank khi hai root có rank bằng nhau.
10. `compareTo` của các cạnh trong Kruskal trả về sai với số âm. Dùng `Integer.compare(a, b)` an toàn hơn `a - b`.
11. Truyền tham chiếu vào constructor DSU làm thay đổi dữ liệu từ bên ngoài.
12. Xóa cạnh gốc trong Graph và quên giảm bậc khi giải quyết bài toán Eulerian Path (ít liên quan MST, nhưng hay nhầm lặp DSU).
13. Đồ thị không liên thông: Thuật toán không thể tạo đủ `V-1` cạnh, nhưng không check điều kiện trả về thất bại (vd: `-1`).
14. Không clone object khi thay đổi trọng số làm ảnh hưởng đến Kruskal gốc.
15. Quên cộng trọng số vào `mstWeight` trong Kruskal.
16. Tìm tập cha trước khi thực hiện path compression có thể tạo chu trình.
17. Dùng Prim bằng mảng 2D cho ma trận thưa gây tốn `O(V^2)` thay vì `O(E log V)`.
18. Không cập nhật node cha ở level 2 trong Path compression, chỉ cập nhật 1 step.
19. Gộp node con vào node con khác thay vì gộp root vào root.
20. So sánh bằng tham chiếu `==` đối với root value có kiểu `Integer`.

## 10. Tối ưu hóa & Kỹ thuật nâng cao
- **Path Halving**: Biến thể của path compression, `parent[i] = parent[parent[i]]`, không dùng đệ quy, tránh tràn stack, tốc độ cực nhanh trong thực tế.
- **Fibonacci Heap**: Dùng trong thuật toán Prim để đạt `O(E + V log V)`. Dùng khi đồ thị có rất nhiều cạnh.
- **Rollback DSU**: DSU không sử dụng path compression (chỉ dùng Union by Rank/Size) để có thể rollback (hoàn tác) trạng thái, sử dụng trong các bài toán Divide and Conquer trên cây.

## 11. Edge Cases (30 Trường hợp góc)
1. Đồ thị chỉ có 1 đỉnh (trọng số 0).
2. Đồ thị chỉ có 2 đỉnh và 1 cạnh.
3. Đồ thị không liên thông (Kruskal/Prim sẽ không chọn đủ V-1 cạnh).
4. Tất cả trọng số bằng nhau.
5. Trọng số cạnh là số âm (Kruskal/Prim vẫn hoạt động đúng với đồ thị vô hướng).
6. Đồ thị có đa cạnh (Kruskal tự xử lý an toàn vì cạnh lớn hơn sẽ bị loại bỏ bởi DSU).
7. Đồ thị có khuyên (tự nối chính nó) - DSU sẽ loại ngay.
8. Trọng số cực lớn (gây tràn số Integer, cần dùng Long).
9. Đầu vào là mảng rỗng (0 đỉnh).
10. Các đỉnh đánh số không liên tục (ví dụ 1, 5, 100).
11. Trọng số 0 (cạnh không tốn chi phí).
12. Đồ thị là một đường thẳng dài (Line graph).
13. Đồ thị là hình sao (Star graph).
14. Complete graph (mọi đỉnh nối với nhau).
15. Nhiều MST khác nhau có cùng trọng số nhỏ nhất.
16. Trọng số bằng số thực (Float/Double).
17. Chỉ số của đỉnh là String (cần Map sang ID nguyên).
18. Yêu cầu bắt buộc phải chứa một cạnh cụ thể trong MST.
19. Yêu cầu loại bỏ một cạnh xem MST có thay đổi không.
20. Cần tìm độ dài cạnh lớn nhất trong MST.
21. Truy vấn DSU liên tục trên mạng bị ngắt kết nối dần dần (trick: làm ngược).
22. Đồ thị dạng lưới (Grid).
23. Gộp tất cả nhưng có đỉnh độc lập không liên kết với đồ thị.
24. Một đỉnh có quá nhiều bậc (ví dụ đỉnh trung tâm của đồ thị sao) có thể làm Prim queue quá tải.
25. Mạng đồ thị chia làm 2 thành phần độc lập hoàn toàn.
26. Khởi tạo mảng parent nhưng chừa số 0 nếu đầu vào dùng đỉnh 1-indexed.
27. Đồ thị có dạng chu trình đơn lớn (Ring graph).
28. Tổng trọng số cạnh có thể vừa khít giới hạn mảng của bộ nhớ.
29. Cây nhị phân coi như đồ thị vô hướng.
30. Tìm Second Best Minimum Spanning Tree.

## 12. Design Patterns / System Design Application
- **Observer Pattern**: Dùng báo cáo sự kiện khi 2 cluster lớn merge với nhau trong DSU.
- **Factory Pattern**: Tạo Edge hoặc Node object từ dữ liệu thô.
- Trong **System Design**, DSU dùng để quản lý connected servers trong Data Center, xác định phân vùng mạng (network partitions).

## 13. Các biến thể & Mở rộng
- **Second Best MST**: Tìm cây khung có tổng trọng số nhỏ thứ 2.
- **Steiner Tree Problem**: Một biến thể NP-Hard, tìm cây kết nối một tập hợp con các đỉnh.
- **Maximum Spanning Tree**: Đổi dấu trọng số cạnh hoặc lấy Max-Heap thay vì Min-Heap.
- **Dynamic Connectivity**: Xử lý chèn và xóa cạnh online (dùng Link/Cut Tree hoặc Divide and Conquer với Rollback DSU).

## 14. Câu hỏi phỏng vấn (20 Questions)
1. DSU là gì? Phân biệt Path Compression và Union by Rank?
2. Có cần phải dùng cả 2 kỹ thuật (Path Compression và Union by rank) không?
3. Tại sao DSU gọi là hàm Inverse Ackermann?
4. Trình bày Kruskal và phân tích độ phức tạp.
5. Sự khác biệt giữa Kruskal và Prim là gì?
6. Khi nào nên dùng Prim thay vì Kruskal?
7. DSU có dùng cho đồ thị có hướng không? Tại sao?
8. Làm sao để phát hiện chu trình trong đồ thị vô hướng bằng DSU?
9. Thuật toán Prim có hoạt động với cạnh trọng số âm không?
10. Làm sao để tìm cây khung lớn nhất (Maximum Spanning Tree)?
11. Giải thích bài toán "Số lượng thành phần liên thông" dùng DSU.
12. Có thể xóa một cạnh trong DSU được không?
13. Kruskal xử lý đồ thị không liên thông như thế nào?
14. PriorityQueue trong Prim lưu trữ gì?
15. Có thể cài đặt Prim mà không dùng Priority Queue không?
16. Làm sao để giải bài toán kết nối mạng với chi phí mua dây rẻ nhất?
17. DSU dùng mảng `size` hay `rank` tốt hơn?
18. Path compression có thể viết bằng Interative thay vì Recursive không?
19. Giải bài toán "Evaluate Division" dùng DSU như thế nào?
20. Nếu các cạnh đã được sắp xếp sẵn, độ phức tạp của Kruskal là bao nhiêu?

## 15. Tiêu chí chọn (Khi nào nên dùng?)
- **DSU**: Cần kiểm tra liên thông động (online queries), gộp các tập hợp, đếm số thành phần liên thông, phát hiện chu trình (vô hướng).
- **Kruskal**: Đồ thị thưa (ít cạnh, nhiều đỉnh), cần sắp xếp trước danh sách cạnh. Thích hợp khi input cho đồ thị dạng danh sách cạnh.
- **Prim**: Đồ thị dày (nhiều cạnh), input dạng ma trận kề hoặc danh sách kề.

## 16. Tích hợp với các Cấu trúc dữ liệu khác
- DSU có thể lưu trữ metadata cho mỗi tập hợp: Kích thước, Max value, Min value, Cạnh dài nhất.
- Kết hợp với Segment Tree trong các bài toán Divide and Conquer (Rollback DSU).
- Kết hợp Trie cho bài toán xử lý chuỗi (Similar String Groups).

## 17. Khía cạnh Đa luồng & Đồng thời
- Thuật toán Prim khó song song hóa do phụ thuộc tuần tự vào Priority Queue.
- **Concurrent Union-Find** tồn tại trong các hệ thống lớn bằng cách sử dụng các thao tác Compare-And-Swap (CAS) và Spin-locks.
- Kruskal: Giai đoạn sắp xếp cạnh có thể dùng Parallel Sort (Java `Arrays.parallelSort()`).

## 18. Các ví dụ minh họa từng bước (Trace/Dry-run)
Giả sử có đồ thị 4 đỉnh, 5 cạnh:
`E1(0,1,10), E2(0,2,6), E3(0,3,5), E4(1,3,15), E5(2,3,4)`
**Kruskal:**
- Sắp xếp: E5(4), E3(5), E2(6), E1(10), E4(15)
- Thêm E5(2,3): DSU gộp {2,3}. MST = 4.
- Thêm E3(0,3): DSU gộp {0,3}. Tập {0,2,3}. MST = 9.
- Thêm E2(0,2): DSU báo 0 và 2 cùng tập. Bỏ qua.
- Thêm E1(0,1): DSU gộp {1} vào {0,2,3}. MST = 19. Đủ 3 cạnh, dừng thuật toán.

## 19. Mẹo & Thủ thuật (Tips & Tricks)
- Luôn truyền kích thước mảng an toàn (N+1) nếu bài toán dùng 1-indexed.
- Biến đổi mảng 2D thành toạ độ 1D trong DSU: `index = row * C + col`.
- Để tránh bị StackOverflow trong path compression với test case ác ý, hãy dùng Path Halving vòng lặp `while(p[x] != x) { p[x] = p[p[x]]; x = p[x]; }`.
- Thay vì DSU boolean union, hãy trả về `false` nếu tạo thành chu trình, rất tiện để code trong 1 dòng.

## 20. Bài tập thực hành & Hướng dẫn
Xem chi tiết tại file: `04-Minimum-Spanning-Tree-Problems.md`

## 21. Tài liệu tham khảo & Đọc thêm
- Introduction to Algorithms (CLRS) - Chương 21 (Disjoint Sets) và Chương 23 (MST).
- T-414-AFLV: Competitive Programming Algorithms.
- CP-Algorithms: Disjoint Set Union, Minimum Spanning Tree (Kruskal, Prim).

## 22. Cheat Sheet / Tóm tắt
- **Kruskal**: Sort Edges + DSU. Time: `O(E log E)`.
- **Prim**: PriorityQueue + Visited Array. Time: `O(E log V)`.
- **DSU Operations**: 
  - `find(x)`: Đệ quy tìm root và cập nhật `parent`.
  - `union(x, y)`: Tìm `rootX`, `rootY`. Gộp root nhỏ vào root lớn theo `rank`/`size`.

## 23. Gỡ lỗi (Debugging Guide)
- In mảng `parent` sau mỗi thao tác union để xem các tập hợp đang gom đúng không.
- Nếu chi phí MST quá thấp, kiểm tra lại xem có cạnh nào kết nối nhầm không (đặc biệt khi xử lý cạnh không hợp lệ).
- Nếu PriorityQueue xuất hiện `NullPointerException`, kiểm tra lại điều kiện thêm neighbor (ví dụ mảng danh sách kề chưa được khởi tạo).

## 24. Câu hỏi tự kiểm tra (Self-Assessment)
1. Hãy viết lại template DSU trong 1 phút bằng tay.
2. Kruskal cần mảng cấu trúc để lưu đồ thị hay danh sách các cạnh?
3. Nếu tất cả các cạnh có chung trọng số, MST có duy nhất không?
4. Đồ thị có 100 đỉnh nhưng chỉ có 50 cạnh, Kruskal và Prim phản ứng thế nào?
5. Làm thế nào để tìm cạnh lớn nhất trên đường đi giữa đỉnh u và v thuộc MST?
