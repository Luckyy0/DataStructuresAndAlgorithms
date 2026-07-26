# Bài Tập Thực Hành: Shortest Path

Tài liệu này bao gồm 30 bài tập thực hành về thuật toán đường đi ngắn nhất (Shortest Path), từ cơ bản đến nâng cao. 10 bài đầu tiên sẽ được cung cấp đầy đủ đề bài, phân tích, mã nguồn Java và độ phức tạp.

---

## 1. Network Delay Time (LeetCode 743)
**Đề bài chi tiết**: Cho một mạng lưới gồm `n` node, được đánh số từ `1` đến `n`. Bạn được cung cấp danh sách `times`, trong đó `times[i] = [u, v, w]` đại diện cho thời gian tín hiệu truyền từ node `u` sang node `v` mất `w` thời gian. Nếu phát tín hiệu từ một node `k`, mất bao lâu để tất cả các node đều nhận được tín hiệu? Nếu không thể, trả về `-1`.

**Phân tích thuật toán**: 
Bài toán yêu cầu tìm thời gian dài nhất để tín hiệu đến được một node tính từ `k`. Đây chính là bài toán tìm đường đi ngắn nhất từ `k` đến tất cả các đỉnh khác, sau đó lấy giá trị lớn nhất trong mảng khoảng cách đó. Ta dùng thuật toán Dijkstra với PriorityQueue.

**Mã nguồn Java**:
```java
class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        List<List<int[]>> adj = new ArrayList<>();
        for(int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        for(int[] t : times) adj.get(t[0]).add(new int[]{t[1], t[2]});
        
        int[] dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[k] = 0;
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        pq.offer(new int[]{k, 0});
        
        while(!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            if(d > dist[u]) continue;
            
            for(int[] edge : adj.get(u)) {
                int v = edge[0], weight = edge[1];
                if(dist[u] + weight < dist[v]) {
                    dist[v] = dist[u] + weight;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        
        int maxDist = 0;
        for(int i = 1; i <= n; i++) {
            if(dist[i] == Integer.MAX_VALUE) return -1;
            maxDist = Math.max(maxDist, dist[i]);
        }
        return maxDist;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V)$ với $E$ là số cạnh và $V$ là số đỉnh `n`.
- Space Complexity: $O(V + E)$ cho đồ thị và cấu trúc PQ.

---

## 2. Cheapest Flights Within K Stops (LeetCode 787)
**Đề bài chi tiết**: Cho `n` thành phố kết nối bởi một số chuyến bay. Cung cấp mảng `flights` với `flights[i] = [from, to, price]`. Tìm giá vé rẻ nhất từ điểm `src` đến `dst` sao cho trung chuyển tối đa `K` điểm dừng. Trả về `-1` nếu không có chuyến bay.

**Phân tích thuật toán**:
Dijkstra bình thường có thể thất bại vì đường đi rẻ hơn có thể có số điểm dừng lớn hơn `K`. Ở đây, ta dùng thuật toán Bellman-Ford (biến thể) lặp đúng `K + 1` lần, dùng một mảng phụ `temp` lưu khoảng cách tạm thời ở vòng trước để tránh cập nhật lố số bước trong cùng 1 vòng lặp. Hoặc dùng BFS / Dijkstra state `(cost, node, stops)`.

**Mã nguồn Java** (Bellman-Ford approach):
```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;
        
        for (int i = 0; i <= k; i++) {
            int[] temp = Arrays.copyOf(dist, n);
            for (int[] flight : flights) {
                int u = flight[0], v = flight[1], w = flight[2];
                if (dist[u] != Integer.MAX_VALUE && dist[u] + w < temp[v]) {
                    temp[v] = dist[u] + w;
                }
            }
            dist = temp;
        }
        return dist[dst] == Integer.MAX_VALUE ? -1 : dist[dst];
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(K \times E)$
- Space Complexity: $O(V)$ cho mảng khoảng cách `dist`.

---

## 3. Path with Maximum Probability (LeetCode 1514)
**Đề bài chi tiết**: Cho một đồ thị vô hướng của `n` node và các cạnh, cùng với danh sách `succProb`, nơi `succProb[i]` là xác suất thành công đi qua cạnh thứ `i`. Tìm đường đi từ `start` đến `end` sao cho tổng xác suất cao nhất (các đỉnh độc lập, xác suất đường đi = tích các xác suất cạnh).

**Phân tích thuật toán**:
Tương tự tìm đường đi ngắn nhất, ta tìm đường đi có "trọng số" (xác suất) tối đa. Sử dụng Dijkstra nhưng đổi PriorityQueue thành Max-Heap, thay vì cộng `+` trọng số, ta nhân `*` xác suất. Khởi tạo `prob[start] = 1.0` thay vì `0`.

**Mã nguồn Java**:
```java
class Solution {
    public double maxProbability(int n, int[][] edges, double[] succProb, int start, int end) {
        List<List<double[]>> adj = new ArrayList<>();
        for(int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for(int i = 0; i < edges.length; i++) {
            int u = edges[i][0], v = edges[i][1];
            adj.get(u).add(new double[]{v, succProb[i]});
            adj.get(v).add(new double[]{u, succProb[i]});
        }
        
        double[] prob = new double[n];
        prob[start] = 1.0;
        
        PriorityQueue<double[]> pq = new PriorityQueue<>((a, b) -> Double.compare(b[1], a[1]));
        pq.offer(new double[]{start, 1.0});
        
        while(!pq.isEmpty()) {
            double[] curr = pq.poll();
            int u = (int)curr[0];
            double p = curr[1];
            
            if (u == end) return p;
            if (p < prob[u]) continue;
            
            for(double[] edge : adj.get(u)) {
                int v = (int)edge[0];
                double nextProb = p * edge[1];
                if(nextProb > prob[v]) {
                    prob[v] = nextProb;
                    pq.offer(new double[]{v, prob[v]});
                }
            }
        }
        return 0d;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V)$
- Space Complexity: $O(V + E)$

---

## 4. Path With Minimum Effort (LeetCode 1631)
**Đề bài chi tiết**: Cho một ma trận `heights` biểu diễn độ cao của các ngọn núi. Bạn muốn đi từ ô `(0,0)` đến `(R-1, C-1)`. Độ nỗ lực của một con đường (Effort) là mức độ chênh lệch chiều cao tuyệt đối lớn nhất giữa hai ô kề nhau trên đường đi đó. Hãy tìm đường đi có độ nỗ lực nhỏ nhất (min-max path).

**Phân tích thuật toán**:
Đây là dạng bài "Bottleneck Shortest Path". Áp dụng thuật toán Dijkstra, mảng `dist` tại đỉnh `u` lưu độ nỗ lực nhỏ nhất cần để tới `u`. Khi thư giãn cạnh, `newEffort = max(currentEffort, abs(height[u] - height[v]))`.

**Mã nguồn Java**:
```java
class Solution {
    public int minimumEffortPath(int[][] heights) {
        int r = heights.length, c = heights[0].length;
        int[][] dist = new int[r][c];
        for(int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        dist[0][0] = 0;
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        pq.offer(new int[]{0, 0, 0}); // r, c, effort
        
        int[] dirX = {0, 0, 1, -1};
        int[] dirY = {1, -1, 0, 0};
        
        while(!pq.isEmpty()) {
            int[] curr = pq.poll();
            int x = curr[0], y = curr[1], effort = curr[2];
            
            if(x == r - 1 && y == c - 1) return effort;
            if(effort > dist[x][y]) continue;
            
            for(int i = 0; i < 4; i++) {
                int nx = x + dirX[i], ny = y + dirY[i];
                if(nx >= 0 && nx < r && ny >= 0 && ny < c) {
                    int nextEffort = Math.max(effort, Math.abs(heights[x][y] - heights[nx][ny]));
                    if(nextEffort < dist[nx][ny]) {
                        dist[nx][ny] = nextEffort;
                        pq.offer(new int[]{nx, ny, nextEffort});
                    }
                }
            }
        }
        return 0;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(V \log V) \approx O(R \times C \log(R \times C))$
- Space Complexity: $O(R \times C)$

---

## 5. Find the City With the Smallest Number of Neighbors at a Threshold Distance (LeetCode 1334)
**Đề bài chi tiết**: Có `n` thành phố, và các cạnh vô hướng trọng số. Tìm thành phố có số lượng thành phố có thể đến với khoảng cách bé hơn hoặc bằng `distanceThreshold` là ít nhất. Nếu có nhiều, chọn thành phố có id lớn hơn.

**Phân tích thuật toán**:
Bài toán này thuộc dạng All-Pairs Shortest Path vì ta cần khoảng cách từ *mọi* thành phố đến *mọi* thành phố khác, sau đó đếm số lượng node. Số đỉnh rất nhỏ ($n \le 100$), Floyd-Warshall Algorithm $O(N^3)$ là phù hợp nhất.

**Mã nguồn Java**:
```java
class Solution {
    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        int[][] dist = new int[n][n];
        for(int i = 0; i < n; i++) {
            Arrays.fill(dist[i], 10000000); // 1e7 để ko bị tràn
            dist[i][i] = 0;
        }
        for(int[] edge : edges) {
            dist[edge[0]][edge[1]] = edge[2];
            dist[edge[1]][edge[0]] = edge[2];
        }
        
        // Floyd-Warshall
        for(int k = 0; k < n; k++) {
            for(int i = 0; i < n; i++) {
                for(int j = 0; j < n; j++) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }
        
        int resCity = -1, minReachable = n + 1;
        for(int i = 0; i < n; i++) {
            int reachable = 0;
            for(int j = 0; j < n; j++) {
                if(i != j && dist[i][j] <= distanceThreshold) {
                    reachable++;
                }
            }
            if(reachable <= minReachable) {
                minReachable = reachable;
                resCity = i;
            }
        }
        return resCity;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N^3)$
- Space Complexity: $O(N^2)$

---

## 6. Minimum Cost to Make at Least One Valid Path in a Grid (LeetCode 1368)
**Đề bài chi tiết**: Cho lưới `grid` có mũi tên định hướng `1 (phải), 2 (trái), 3 (dưới), 4 (trên)`. Bạn có thể đi theo hướng mà không tốn phí, hoặc đổi hướng mất 1 chi phí. Tính chi phí nhỏ nhất để đi từ góc `(0,0)` tới góc `(m-1, n-1)`.

**Phân tích thuật toán**:
Đây là dạng đồ thị trọng số `0` hoặc `1` (cùng hướng = 0 cost, đổi hướng = 1 cost). Phương án hiệu quả nhất là 0-1 BFS sử dụng Deque. Đẩy phần tử với trọng số 0 vào đầu, phần tử tốn thêm phí 1 vào đuôi Queue.

**Mã nguồn Java**:
```java
class Solution {
    public int minCost(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        
        Deque<int[]> deque = new ArrayDeque<>();
        deque.offerFirst(new int[]{0, 0, 0});
        dist[0][0] = 0;
        
        int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}}; // match 1,2,3,4
        
        while(!deque.isEmpty()) {
            int[] curr = deque.pollFirst();
            int x = curr[0], y = curr[1], cost = curr[2];
            
            if (cost > dist[x][y]) continue;
            if (x == m - 1 && y == n - 1) return cost;
            
            for (int i = 0; i < 4; i++) {
                int nx = x + dirs[i][0];
                int ny = y + dirs[i][1];
                if (nx >= 0 && nx < m && ny >= 0 && ny < n) {
                    // cost thêm 0 nếu đi đúng mũi tên, ngược lại thêm 1
                    int nextCost = cost + (grid[x][y] == i + 1 ? 0 : 1);
                    if (nextCost < dist[nx][ny]) {
                        dist[nx][ny] = nextCost;
                        if (grid[x][y] == i + 1) deque.offerFirst(new int[]{nx, ny, nextCost});
                        else deque.offerLast(new int[]{nx, ny, nextCost});
                    }
                }
            }
        }
        return 0;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N)$
- Space Complexity: $O(M \times N)$

---

## 7. Design Graph With Shortest Path Calculator (LeetCode 2642)
**Đề bài chi tiết**: Thiết kế một class đại diện đồ thị có hướng. Chứa phương thức `addEdge` thêm cạnh vào và `shortestPath` để tìm khoảng cách ngắn nhất giữa 2 điểm. 

**Phân tích thuật toán**:
Vì phương thức `shortestPath` được gọi theo truy vấn, ta có thể đơn giản sử dụng thuật toán Dijkstra mỗi khi `shortestPath` được gọi. Do đồ thị có thể thưa và cập nhật online.

**Mã nguồn Java**:
```java
class Graph {
    List<List<int[]>> adj;
    int n;

    public Graph(int n, int[][] edges) {
        this.n = n;
        adj = new ArrayList<>();
        for(int i=0; i<n; i++) adj.add(new ArrayList<>());
        for(int[] edge : edges) addEdge(edge);
    }
    
    public void addEdge(int[] edge) {
        adj.get(edge[0]).add(new int[]{edge[1], edge[2]});
    }
    
    public int shortestPath(int node1, int node2) {
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[node1] = 0;
        
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
        pq.offer(new int[]{node1, 0});
        
        while(!pq.isEmpty()){
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            if(u == node2) return d;
            if(d > dist[u]) continue;
            
            for(int[] e : adj.get(u)){
                int v = e[0], w = e[1];
                if(dist[u] + w < dist[v]){
                    dist[v] = dist[u] + w;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: Constructor $O(V+E)$, addEdge $O(1)$, shortestPath $O(E \log V)$.
- Space Complexity: $O(V + E)$

---

## 8. Second Minimum Time to Reach Destination (LeetCode 2045)
**Đề bài chi tiết**: Tìm thời gian ngắn *thứ hai* chặt chẽ (strictly second minimum time) để tới node kết thúc trong đồ thị, xét thêm tín hiệu đèn xanh/đỏ (traffic light system).

**Phân tích thuật toán**:
Bỏ qua sự phức tạp của tín hiệu đèn (có công thức tính riêng), thuật toán trọng tâm là Dijkstra lưu mảng khoảng cách 2 chiều: `dist1` (ngắn nhất) và `dist2` (ngắn thứ hai). Khi gặp đỉnh, cập nhật nếu nó bé hơn `dist1`, đẩy giá trị cũ vào `dist2`. Hoặc lớn hơn `dist1` nhưng nhỏ hơn `dist2`.

**Mã nguồn Java**:
```java
class Solution {
    public int secondMinimum(int n, int[][] edges, int time, int change) {
        List<List<Integer>> adj = new ArrayList<>();
        for(int i=0; i<=n; i++) adj.add(new ArrayList<>());
        for(int[] e : edges) {
            adj.get(e[0]).add(e[1]);
            adj.get(e[1]).add(e[0]);
        }
        
        int[] d1 = new int[n + 1], d2 = new int[n + 1];
        Arrays.fill(d1, Integer.MAX_VALUE);
        Arrays.fill(d2, Integer.MAX_VALUE);
        d1[1] = 0;
        
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{1, 0}); // u, time
        
        while(!q.isEmpty()){
            int[] curr = q.poll();
            int u = curr[0], t = curr[1];
            
            int loops = t / change;
            if(loops % 2 == 1) { // Red light
                t = (loops + 1) * change;
            }
            t += time;
            
            for(int v : adj.get(u)){
                if(t < d1[v]){
                    d2[v] = d1[v];
                    d1[v] = t;
                    q.offer(new int[]{v, d1[v]});
                } else if(t > d1[v] && t < d2[v]){
                    d2[v] = t;
                    q.offer(new int[]{v, d2[v]});
                }
            }
        }
        return d2[n];
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(V + E)$ vì trọng số các cạnh giống nhau nên có thể dùng Queue bình thường giống BFS.
- Space Complexity: $O(V + E)$

---

## 9. Number of Restricted Paths From First to Last Node (LeetCode 1786)
**Đề bài chi tiết**: Cho khoảng cách nhỏ nhất từ node `u` tới node `n` là `d[u]`. Một restricted path từ node 1 tới `n` là đường đi thoả mãn `d[u] > d[v]` cho mọi cạnh `(u, v)` trên đường đi. Trả về số lượng đường đi modulo $10^9+7$.

**Phân tích thuật toán**:
- Bước 1: Dijkstra từ node `n` để tính mảng `dist` tới mọi điểm.
- Bước 2: Dùng DFS + Memoization (Top-Down DP) duyệt từ node 1 đến n. Chỉ đi vào node `v` khi `dist[u] > dist[v]`.

**Mã nguồn Java**:
```java
class Solution {
    int[] dist;
    Integer[] memo;
    int MOD = 1000000007;
    
    public int countRestrictedPaths(int n, int[][] edges) {
        List<List<int[]>> adj = new ArrayList<>();
        for(int i=0; i<=n; i++) adj.add(new ArrayList<>());
        for(int[] e: edges){
            adj.get(e[0]).add(new int[]{e[1], e[2]});
            adj.get(e[1]).add(new int[]{e[0], e[2]});
        }
        
        dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        dist[n] = 0;
        pq.offer(new int[]{n, 0});
        
        while(!pq.isEmpty()){
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            if(d > dist[u]) continue;
            for(int[] edge : adj.get(u)){
                int v = edge[0], w = edge[1];
                if(dist[u] + w < dist[v]){
                    dist[v] = dist[u] + w;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        
        memo = new Integer[n + 1];
        return dfs(1, n, adj);
    }
    
    private int dfs(int u, int target, List<List<int[]>> adj) {
        if(u == target) return 1;
        if(memo[u] != null) return memo[u];
        
        int count = 0;
        for(int[] edge : adj.get(u)){
            int v = edge[0];
            if(dist[u] > dist[v]){
                count = (count + dfs(v, target, adj)) % MOD;
            }
        }
        return memo[u] = count;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V + V + E)$
- Space Complexity: $O(V + E)$

---

## 10. Modify Graph Edge Weights (LeetCode 2699)
**Đề bài chi tiết**: Cho một số cạnh có trọng số `-1` cần được chỉnh sửa. Gán một số nguyên dương từ 1 đến $2 \times 10^9$ sao cho khoảng cách từ `source` tới `destination` chính xác bằng `target`. Nếu không, trả về rỗng.

**Phân tích thuật toán**:
Bài toán rất khó, yêu cầu chạy Dijkstra 2 lần.
1. Chạy với cạnh `-1` tạm gán bằng vô cực (bỏ qua), nếu khoảng cách ban đầu < `target` thì vô nghiệm (vì không thể sửa to thêm cạnh bình thường).
2. Chạy Dijkstra lần 2: khi xử lý 1 cạnh `-1`, ta chỉnh nó thành giá trị sao cho khoảng cách tại node `v` vừa khớp hướng tới `target`. Các cạnh `-1` dư chuyển thành vô cực (rất lớn).

**Mã nguồn Java**:
```java
class Solution {
    // Phiên bản thu gọn minh họa ý tưởng
    public int[][] modifiedGraphEdges(int n, int[][] edges, int source, int destination, int target) {
        int initialDist = dijkstra(edges, n, source, destination);
        if (initialDist < target) return new int[0][0]; // Khong the
        
        boolean matches = (initialDist == target);
        
        for (int[] edge : edges) {
            if (edge[2] > 0) continue;
            
            edge[2] = matches ? (int)2e9 : 1;
            if (!matches) {
                int newDist = dijkstra(edges, n, source, destination);
                if (newDist <= target) {
                    matches = true;
                    edge[2] += target - newDist; // bù thêm phần thiếu
                }
            }
        }
        
        return matches ? edges : new int[0][0];
    }
    
    private int dijkstra(int[][] edges, int n, int src, int dest) {
        List<List<int[]>> adj = new ArrayList<>();
        for(int i=0; i<n; i++) adj.add(new ArrayList<>());
        for(int[] e : edges) {
            if (e[2] == -1) continue;
            adj.get(e[0]).add(new int[]{e[1], e[2]});
            adj.get(e[1]).add(new int[]{e[0], e[2]});
        }
        
        int[] dist = new int[n];
        Arrays.fill(dist, (int)2e9);
        dist[src] = 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        pq.offer(new int[]{src, 0});
        
        while(!pq.isEmpty()) {
            int[] curr = pq.poll();
            if (curr[1] > dist[curr[0]]) continue;
            for (int[] e : adj.get(curr[0])) {
                if (dist[curr[0]] + e[1] < dist[e[0]]) {
                    dist[e[0]] = dist[curr[0]] + e[1];
                    pq.offer(new int[]{e[0], dist[e[0]]});
                }
            }
        }
        return dist[dest];
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \times E \log V)$ trong trường hợp xấu nhất (thuật toán rút gọn, có bản tối ưu hơn).
- Space Complexity: $O(V + E)$

---

## 11. The Maze II (LeetCode 505)
**Đề bài chi tiết**: Trong một mê cung có các khoảng trống (0) và tường (1), một quả bóng có thể lăn lên, xuống, trái, phải. Quả bóng sẽ không dừng lại cho đến khi chạm vào tường. Cho vị trí bắt đầu `start` và vị trí đích `destination`, tìm khoảng cách ngắn nhất để quả bóng có thể lăn đến đích. Khoảng cách được tính bằng số ô trống mà quả bóng đã đi qua. Nếu quả bóng không thể đến đích, trả về -1.

**Phân tích thuật toán**: Sử dụng thuật toán Dijkstra để tìm đường đi ngắn nhất. Trọng số của các đường đi là khoảng cách lăn từ điểm dừng này đến điểm dừng tiếp theo. PriorityQueue sẽ ưu tiên các điểm dừng có khoảng cách từ điểm xuất phát là nhỏ nhất. Khi lấy một điểm ra khỏi Queue, ta thử lăn bóng theo 4 hướng cho đến khi gặp tường, cộng khoảng cách và cập nhật vào mảng `dist`.

**Mã nguồn Java**:
```java
class Solution {
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int m = maze.length, n = maze[0].length;
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        pq.offer(new int[]{start[0], start[1], 0});
        dist[start[0]][start[1]] = 0;
        
        int[][] dirs = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int x = curr[0], y = curr[1], d = curr[2];
            
            if (d > dist[x][y]) continue;
            if (x == destination[0] && y == destination[1]) return d;
            
            for (int[] dir : dirs) {
                int nx = x, ny = y, step = 0;
                while (nx >= 0 && nx < m && ny >= 0 && ny < n && maze[nx][ny] == 0) {
                    nx += dir[0];
                    ny += dir[1];
                    step++;
                }
                // Backtrack one step as the loop terminates when hitting a wall or boundary
                nx -= dir[0];
                ny -= dir[1];
                step--;
                
                if (dist[x][y] + step < dist[nx][ny]) {
                    dist[nx][ny] = dist[x][y] + step;
                    pq.offer(new int[]{nx, ny, dist[nx][ny]});
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N \times \log(M \times N))$ trong trường hợp xấu nhất sử dụng Dijkstra.
- Space Complexity: $O(M \times N)$ cho mảng khoảng cách và Priority Queue.

---

## 12. The Maze III (LeetCode 499)
**Đề bài chi tiết**: Tương tự như The Maze II, nhưng quả bóng có thể rơi vào một cái hố (hole). Quả bóng sẽ lăn đến khi gặp tường hoặc rơi vào lỗ. Nếu có nhiều đường đi ngắn nhất đến lỗ, trả về chỉ dẫn đường đi theo thứ tự từ điển (ví dụ "dldr" ưu tiên hơn "drld"). Nếu không có đường đi, trả về "impossible". Hướng đi được quy ước: 'd' (xuống), 'l' (trái), 'r' (phải), 'u' (lên).

**Phân tích thuật toán**: Vẫn sử dụng Dijkstra, nhưng ngoài lưu trữ khoảng cách, ta cần lưu trữ thêm chuỗi đường đi (`String`). Trong class State hoặc mảng đưa vào PriorityQueue, ta so sánh theo khoảng cách ưu tiên trước, nếu khoảng cách bằng nhau thì so sánh chuỗi đường đi theo thứ tự từ điển (Lexicographical order).

**Mã nguồn Java**:
```java
class Solution {
    class Point implements Comparable<Point> {
        int r, c, dist;
        String path;
        public Point(int r, int c, int dist, String path) {
            this.r = r; this.c = c; this.dist = dist; this.path = path;
        }
        public int compareTo(Point other) {
            if (this.dist == other.dist) return this.path.compareTo(other.path);
            return this.dist - other.dist;
        }
    }
    
    public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
        int m = maze.length, n = maze[0].length;
        Point[][] dist = new Point[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dist[i][j] = new Point(i, j, Integer.MAX_VALUE, "");
            }
        }
        
        PriorityQueue<Point> pq = new PriorityQueue<>();
        Point start = new Point(ball[0], ball[1], 0, "");
        pq.offer(start);
        dist[ball[0]][ball[1]] = start;
        
        int[][] dirs = {{1, 0}, {0, -1}, {0, 1}, {-1, 0}};
        String[] dirStr = {"d", "l", "r", "u"};
        
        while (!pq.isEmpty()) {
            Point curr = pq.poll();
            if (curr.r == hole[0] && curr.c == hole[1]) return curr.path;
            
            for (int i = 0; i < 4; i++) {
                int r = curr.r, c = curr.c, d = curr.dist;
                while (r >= 0 && r < m && c >= 0 && c < n && maze[r][c] == 0 && (r != hole[0] || c != hole[1])) {
                    r += dirs[i][0];
                    c += dirs[i][1];
                    d++;
                }
                if (r != hole[0] || c != hole[1]) {
                    r -= dirs[i][0];
                    c -= dirs[i][1];
                    d--;
                }
                
                String newPath = curr.path + dirStr[i];
                Point next = new Point(r, c, d, newPath);
                
                if (next.compareTo(dist[r][c]) < 0) {
                    dist[r][c] = next;
                    pq.offer(next);
                }
            }
        }
        return "impossible";
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N \times \max(M, N) \times \log(M \times N))$
- Space Complexity: $O(M \times N)$ cho mảng lưu đối tượng Point và Priority Queue.

---

## 13. Minimum Weighted Subgraph With the Required Paths (LeetCode 2203)
**Đề bài chi tiết**: Cho số nguyên `n` đại diện cho đồ thị có hướng có trọng số. Tìm một subgraph có tổng trọng số nhỏ nhất sao cho tồn tại đường đi từ `src1` đến `dest` và từ `src2` đến `dest`. Trả về tổng trọng số, hoặc -1 nếu không thể.

**Phân tích thuật toán**: Vấn đề yêu cầu tìm một điểm trung gian `x` sao cho đường đi từ `src1 -> x`, `src2 -> x`, và `x -> dest` có tổng trọng số là nhỏ nhất. Ta cần chạy thuật toán Dijkstra 3 lần: 1 lần từ `src1`, 1 lần từ `src2`, và 1 lần từ `dest` trên đồ thị bị đảo ngược (reversed graph). Sau đó lặp qua tất cả đỉnh `x` và tìm giá trị nhỏ nhất của `dist1[x] + dist2[x] + distDest[x]`.

**Mã nguồn Java**:
```java
class Solution {
    public long minimumWeight(int n, int[][] edges, int src1, int src2, int dest) {
        List<List<int[]>> adj = new ArrayList<>();
        List<List<int[]>> revAdj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
            revAdj.add(new ArrayList<>());
        }
        for (int[] e : edges) {
            adj.get(e[0]).add(new int[]{e[1], e[2]});
            revAdj.get(e[1]).add(new int[]{e[0], e[2]});
        }
        
        long[] dist1 = dijkstra(n, adj, src1);
        long[] dist2 = dijkstra(n, adj, src2);
        long[] distDest = dijkstra(n, revAdj, dest);
        
        long minWeight = Long.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            if (dist1[i] != Long.MAX_VALUE && dist2[i] != Long.MAX_VALUE && distDest[i] != Long.MAX_VALUE) {
                minWeight = Math.min(minWeight, dist1[i] + dist2[i] + distDest[i]);
            }
        }
        return minWeight == Long.MAX_VALUE ? -1 : minWeight;
    }
    
    private long[] dijkstra(int n, List<List<int[]>> adj, int src) {
        long[] dist = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[src] = 0;
        PriorityQueue<long[]> pq = new PriorityQueue<>((a, b) -> Long.compare(a[1], b[1]));
        pq.offer(new long[]{src, 0});
        
        while (!pq.isEmpty()) {
            long[] curr = pq.poll();
            int u = (int) curr[0];
            long d = curr[1];
            
            if (d > dist[u]) continue;
            
            for (int[] edge : adj.get(u)) {
                int v = edge[0];
                long w = edge[1];
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.offer(new long[]{v, dist[v]});
                }
            }
        }
        return dist;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V)$ vì ta chạy thuật toán Dijkstra 3 lần.
- Space Complexity: $O(V + E)$ để biểu diễn 2 dạng đồ thị và lưu các mảng khoảng cách.

---

## 14. Maximum Path Quality of a Graph (LeetCode 2065)
**Đề bài chi tiết**: Cho một đồ thị vô hướng `n` đỉnh, mỗi đỉnh `i` có giá trị chất lượng `values[i]`. Có một khoảng thời gian tối đa `maxTime`. Bạn phải tìm một đường đi bắt đầu từ `0` và kết thúc tại `0` sao cho tổng thời gian các cạnh đi qua không vượt quá `maxTime`, và tổng chất lượng các đỉnh trên đường đi (mỗi đỉnh chỉ tính 1 lần) là lớn nhất.

**Phân tích thuật toán**: Do giới hạn bài toán tương đối nhỏ (`n <= 1000`, độ dài đường đi tối đa có giới hạn rất chặt chẽ vì `maxTime / min_edge_time <= 10`), ta có thể sử dụng giải thuật Quay lui (Backtracking/DFS) để duyệt tất cả các đường đi hợp lệ từ node 0. Ta dùng một mảng `visited` (hoặc bộ đếm) để theo dõi những đỉnh đã lấy giá trị.

**Mã nguồn Java**:
```java
class Solution {
    int maxQuality = 0;
    
    public int maximalPathQuality(int[] values, int[][] edges, int maxTime) {
        int n = values.length;
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) {
            adj.get(e[0]).add(new int[]{e[1], e[2]});
            adj.get(e[1]).add(new int[]{e[0], e[2]});
        }
        
        int[] visited = new int[n];
        visited[0] = 1;
        dfs(0, 0, values[0], values, adj, maxTime, visited);
        
        return maxQuality;
    }
    
    private void dfs(int u, int timeSpent, int currentQuality, int[] values, List<List<int[]>> adj, int maxTime, int[] visited) {
        if (u == 0) {
            maxQuality = Math.max(maxQuality, currentQuality);
        }
        
        for (int[] edge : adj.get(u)) {
            int v = edge[0];
            int t = edge[1];
            if (timeSpent + t <= maxTime) {
                if (visited[v] == 0) {
                    visited[v] = 1;
                    dfs(v, timeSpent + t, currentQuality + values[v], values, adj, maxTime, visited);
                    visited[v] = 0;
                } else {
                    visited[v]++;
                    dfs(v, timeSpent + t, currentQuality, values, adj, maxTime, visited);
                    visited[v]--;
                }
            }
        }
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(4^{10})$ hoặc $O(D^{max\_steps})$ trong đó D là bậc cao nhất của đỉnh. Thực tế thời gian chạy rất nhỏ vì `maxTime` bị giới hạn.
- Space Complexity: $O(V + E)$ cho đồ thị và Call Stack (tối đa bằng max_steps).

---

## 15. Get Watched Videos by Your Friends (LeetCode 1311)
**Đề bài chi tiết**: Bạn có danh sách `watchedVideos` là các bộ phim mỗi người xem, và `friends` là danh sách bạn bè của mỗi người. Hãy lấy tất cả video được xem bởi những người ở mức độ bạn bè thứ `level` (bạn của bạn của...). Sau đó sắp xếp các video theo tần suất xem tăng dần, nếu tần suất bằng nhau thì sắp xếp theo thứ tự từ điển.

**Phân tích thuật toán**: Đây là một bài toán tìm mức độ sâu (level) trong đồ thị không trọng số. Ta sử dụng thuật toán BFS bắt đầu từ `id` của bản thân, loang ra các mức đến khi đạt độ sâu `level`. Sau đó tổng hợp lại các video từ những người bạn tại độ sâu đó, đếm tần suất bằng `HashMap` và sắp xếp theo yêu cầu.

**Mã nguồn Java**:
```java
class Solution {
    public List<String> watchedVideosByFriends(List<List<String>> watchedVideos, int[][] friends, int id, int level) {
        int n = friends.length;
        boolean[] visited = new boolean[n];
        Queue<Integer> q = new LinkedList<>();
        
        q.offer(id);
        visited[id] = true;
        int currLevel = 0;
        
        while (!q.isEmpty() && currLevel < level) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int u = q.poll();
                for (int v : friends[u]) {
                    if (!visited[v]) {
                        visited[v] = true;
                        q.offer(v);
                    }
                }
            }
            currLevel++;
        }
        
        Map<String, Integer> countMap = new HashMap<>();
        while (!q.isEmpty()) {
            int friendId = q.poll();
            for (String video : watchedVideos.get(friendId)) {
                countMap.put(video, countMap.getOrDefault(video, 0) + 1);
            }
        }
        
        List<String> res = new ArrayList<>(countMap.keySet());
        res.sort((a, b) -> {
            int freqA = countMap.get(a);
            int freqB = countMap.get(b);
            if (freqA != freqB) return freqA - freqB;
            return a.compareTo(b);
        });
        
        return res;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(V + E + V_{videos} \log V_{videos})$, với $V_{videos}$ là số lượng video riêng biệt tại mức bạn bè được chọn.
- Space Complexity: $O(V + V_{videos})$

---

## 16. Word Ladder (LeetCode 127)
**Đề bài chi tiết**: Cho hai từ `beginWord` và `endWord`, cùng một từ điển `wordList`. Tìm chiều dài của chuỗi biến đổi ngắn nhất từ `beginWord` đến `endWord`, sao cho mỗi lần chỉ được đổi 1 kí tự và từ mới phải nằm trong `wordList`. Trả về 0 nếu không có đường đi.

**Phân tích thuật toán**: Bài toán là tìm đường đi ngắn nhất trên đồ thị không trọng số, nên ta áp dụng BFS. Để nhanh chóng tìm các trạng thái tiếp theo, thay vì lặp qua toàn bộ từ điển để so sánh, ta thay đổi từng ký tự của từ hiện tại từ 'a' đến 'z' và kiểm tra xem từ mới có nằm trong `HashSet` chứa `wordList` hay không.

**Mã nguồn Java**:
```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        if (!dict.contains(endWord)) return 0;
        
        Queue<String> q = new LinkedList<>();
        q.offer(beginWord);
        int level = 1;
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                String curr = q.poll();
                if (curr.equals(endWord)) return level;
                
                char[] chars = curr.toCharArray();
                for (int j = 0; j < chars.length; j++) {
                    char original = chars[j];
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (c == original) continue;
                        chars[j] = c;
                        String nextWord = new String(chars);
                        if (dict.contains(nextWord)) {
                            dict.remove(nextWord); // Đánh dấu đã thăm
                            q.offer(nextWord);
                        }
                    }
                    chars[j] = original;
                }
            }
            level++;
        }
        return 0;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M^2 \times N)$ trong đó $M$ là độ dài của từ, $N$ là số lượng từ trong danh sách (do tạo chuỗi chiếm $O(M)$).
- Space Complexity: $O(M \times N)$ cho Set và Queue.

---

## 17. Open the Lock (LeetCode 752)
**Đề bài chi tiết**: Một ổ khoá có 4 bánh xe tròn, mỗi bánh có các số từ '0' đến '9'. Mỗi lượt, bạn có thể xoay 1 bánh xe lên 1 số hoặc xuống 1 số (ví dụ '0' thành '9' hoặc '1'). Bạn bắt đầu tại trạng thái "0000". Cho danh sách `deadends`, nếu bạn xoay tới bất kì trạng thái nào trong đây, ổ khoá sẽ kẹt. Tìm số lượt xoay tối thiểu để tới `target`, nếu không thể trả về -1.

**Phân tích thuật toán**: Đây là một bài toán tìm đường đi ngắn nhất dạng không trọng số, sử dụng thuật toán BFS. Từ mỗi trạng thái, ta sinh ra được 8 trạng thái kế tiếp (4 vị trí $\times$ 2 chiều xoay). Dùng một `HashSet` cho các `deadends` và `visited` để tránh đi vào trạng thái tử thần cũng như tránh bị lặp lại.

**Mã nguồn Java**:
```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> dead = new HashSet<>(Arrays.asList(deadends));
        if (dead.contains("0000")) return -1;
        if (target.equals("0000")) return 0;
        
        Queue<String> q = new LinkedList<>();
        Set<String> visited = new HashSet<>();
        
        q.offer("0000");
        visited.add("0000");
        int steps = 0;
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                String curr = q.poll();
                if (curr.equals(target)) return steps;
                
                for (int j = 0; j < 4; j++) {
                    char[] chars = curr.toCharArray();
                    char original = chars[j];
                    
                    // Xoay lên
                    chars[j] = original == '9' ? '0' : (char)(original + 1);
                    String up = new String(chars);
                    if (!dead.contains(up) && !visited.contains(up)) {
                        visited.add(up);
                        q.offer(up);
                    }
                    
                    // Xoay xuống
                    chars[j] = original == '0' ? '9' : (char)(original - 1);
                    String down = new String(chars);
                    if (!dead.contains(down) && !visited.contains(down)) {
                        visited.add(down);
                        q.offer(down);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(10^4)$ vì có tối đa 10,000 trạng thái của ổ khoá.
- Space Complexity: $O(10^4)$ để lưu Queue và Set.

---

## 18. Swim in Rising Water (LeetCode 778)
**Đề bài chi tiết**: Cho một ma trận `grid` cỡ `N x N` biểu diễn độ cao. Bạn có thể bơi từ ô vuông này sang ô vuông kề cạnh. Tuy nhiên, thời gian bơi tại thời điểm `t` chỉ có thể đến các ô có độ cao $\le t$. Tìm thời gian nhỏ nhất `t` để có thể bơi từ `(0, 0)` đến `(N-1, N-1)`.

**Phân tích thuật toán**: Đây là một bài toán thuộc lớp Bottleneck Path, tương tự như bài Path With Minimum Effort. Mức thời gian tối thiểu để đến một ô là `max(time_to_reach_neighbor, grid[r][c])`. Có thể áp dụng thuật toán Dijkstra với PriorityQueue (Min-Heap). Hoặc cũng có thể giải bằng Binary Search + BFS / DFS.

**Mã nguồn Java** (Sử dụng Dijkstra):
```java
class Solution {
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        boolean[][] visited = new boolean[n][n];
        
        pq.offer(new int[]{0, 0, grid[0][0]});
        visited[0][0] = true;
        
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int x = curr[0], y = curr[1], maxTime = curr[2];
            
            if (x == n - 1 && y == n - 1) return maxTime;
            
            for (int[] dir : dirs) {
                int nx = x + dir[0];
                int ny = y + dir[1];
                if (nx >= 0 && nx < n && ny >= 0 && ny < n && !visited[nx][ny]) {
                    visited[nx][ny] = true;
                    int nextTime = Math.max(maxTime, grid[nx][ny]);
                    pq.offer(new int[]{nx, ny, nextTime});
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N^2 \log(N^2))$
- Space Complexity: $O(N^2)$

---

## 19. Reachable Nodes In Subdivided Graph (LeetCode 882)
**Đề bài chi tiết**: Cho một đồ thị vô hướng. `edges[i] = [u, v, cnt]` chỉ ra rằng có `cnt` node mới được chèn giữa node `u` và `v`. Cho số nguyên `maxMoves`, tìm tổng số lượng node (cả nguyên bản và mới chèn) có thể đến được từ node `0`.

**Phân tích thuật toán**:
1. Coi `cnt` như trọng số (khoảng cách) giữa `u` và `v` là `cnt + 1`. Dùng Dijkstra để tính khoảng cách ngắn nhất `dist[i]` từ đỉnh 0 tới các node ban đầu. Đỉnh có `dist[i] <= maxMoves` thì ta tính nó là có thể đến được.
2. Xét từng cạnh `[u, v, cnt]`, đếm số điểm trung gian có thể đến. Khả năng từ `u` đi vào là `max(0, maxMoves - dist[u])`. Khả năng từ `v` đi vào là `max(0, maxMoves - dist[v])`. Số điểm tối đa trên cạnh bị lấy là `min(cnt, from_u + from_v)`.

**Mã nguồn Java**:
```java
class Solution {
    public int reachableNodes(int[][] edges, int maxMoves, int n) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) {
            adj.get(e[0]).add(new int[]{e[1], e[2] + 1});
            adj.get(e[1]).add(new int[]{e[0], e[2] + 1});
        }
        
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[0] = 0;
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        pq.offer(new int[]{0, 0});
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            
            if (d > dist[u]) continue;
            
            for (int[] edge : adj.get(u)) {
                int v = edge[0], w = edge[1];
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        
        int res = 0;
        for (int i = 0; i < n; i++) {
            if (dist[i] <= maxMoves) res++;
        }
        
        for (int[] e : edges) {
            int u = e[0], v = e[1], cnt = e[2];
            int fromU = Math.max(0, maxMoves - dist[u]);
            int fromV = Math.max(0, maxMoves - dist[v]);
            res += Math.min(cnt, fromU + fromV);
        }
        
        return res;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V)$ vì gọi thuật toán Dijkstra.
- Space Complexity: $O(V + E)$

---

## 20. Find the Safest Path in a Grid (LeetCode 2812)
**Đề bài chi tiết**: Cho một lưới `grid` có giá trị `1` (kẻ cướp) và `0` (trống). Độ an toàn của một ô là khoảng cách Manhattan tối thiểu từ ô đó đến kẻ cướp gần nhất. Hãy tìm một con đường từ góc trên trái `(0, 0)` xuống góc dưới phải `(n-1, n-1)` sao cho "độ an toàn nhỏ nhất trên con đường đó" là lớn nhất (Maximum Safeness Factor).

**Phân tích thuật toán**:
1. Áp dụng Multi-source BFS từ tất cả vị trí kẻ cướp (giá trị 1) để tính khoảng cách (độ an toàn) cho mọi ô trống.
2. Sử dụng thuật toán Dijkstra với Max-Heap hoặc Binary Search + BFS để tìm đường đi tối đa hoá độ an toàn nhỏ nhất.

**Mã nguồn Java**:
```java
class Solution {
    public int maximumSafenessFactor(List<List<Integer>> grid) {
        int n = grid.size();
        if (grid.get(0).get(0) == 1 || grid.get(n-1).get(n-1) == 1) return 0;
        
        int[][] dist = new int[n][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        Queue<int[]> q = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid.get(i).get(j) == 1) {
                    q.offer(new int[]{i, j});
                    dist[i][j] = 0;
                }
            }
        }
        
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int r = curr[0], c = curr[1];
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && dist[nr][nc] == Integer.MAX_VALUE) {
                    dist[nr][nc] = dist[r][c] + 1;
                    q.offer(new int[]{nr, nc});
                }
            }
        }
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[2] - a[2]);
        pq.offer(new int[]{0, 0, dist[0][0]});
        boolean[][] visited = new boolean[n][n];
        visited[0][0] = true;
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int r = curr[0], c = curr[1], safe = curr[2];
            
            if (r == n - 1 && c == n - 1) return safe;
            
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    pq.offer(new int[]{nr, nc, Math.min(safe, dist[nr][nc])});
                }
            }
        }
        return 0;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N^2 \log(N^2))$ từ Multi-source BFS và Dijkstra.
- Space Complexity: $O(N^2)$

---

## 21. Shortest Path Visiting All Nodes (LeetCode 847)
**Đề bài chi tiết**: Cho một đồ thị vô hướng `graph` với `n` đỉnh (từ `0` đến `n-1`). Bạn có thể bắt đầu ở bất kỳ đỉnh nào và dừng lại ở bất kỳ đỉnh nào. Cạnh có thể được dùng lại và các đỉnh có thể được thăm nhiều lần. Tìm đường đi ngắn nhất để thăm tất cả các đỉnh.

**Phân tích thuật toán**: Vì $N \le 12$, bài này có thể giải bằng BFS trên một đồ thị các trạng thái. Mỗi trạng thái là một cặp `(node, bitmask)`, trong đó `node` là đỉnh hiện tại, và `bitmask` là một số nguyên lưu trạng thái các đỉnh đã thăm. Ta khởi tạo Queue với tất cả các đỉnh và chạy BFS cho đến khi `bitmask == (1 << n) - 1`.

**Mã nguồn Java**:
```java
class Solution {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        if (n == 1) return 0;
        
        Queue<int[]> q = new LinkedList<>();
        boolean[][] visited = new boolean[n][1 << n];
        
        for (int i = 0; i < n; i++) {
            q.offer(new int[]{i, 1 << i});
            visited[i][1 << i] = true;
        }
        
        int steps = 0;
        int target = (1 << n) - 1;
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int[] curr = q.poll();
                int u = curr[0];
                int mask = curr[1];
                
                if (mask == target) return steps;
                
                for (int v : graph[u]) {
                    int nextMask = mask | (1 << v);
                    if (!visited[v][nextMask]) {
                        visited[v][nextMask] = true;
                        q.offer(new int[]{v, nextMask});
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N \times 2^N)$ vì có $N \times 2^N$ trạng thái.
- Space Complexity: $O(N \times 2^N)$ để lưu Queue và mảng `visited`.

---

## 22. Distance to a Cycle in Directed Graph (LeetCode 2360 - Biến thể Longest Cycle)
**Đề bài chi tiết**: Cho đồ thị có hướng trong đó mỗi node có nhiều nhất 1 cạnh đi ra, thể hiện bởi mảng `edges` (đỉnh `i` trỏ đến `edges[i]`). Tìm chu trình dài nhất trong đồ thị. Nếu không có chu trình, trả về -1.

**Phân tích thuật toán**: Sử dụng thuật toán Duyệt đồ thị (DFS) hoặc thuật toán Kahn (Topological Sort). Ở đây dùng DFS, với mảng `visited` và một mảng `dist` để theo dõi khoảng cách từ điểm bắt đầu trong cùng một lần duyệt. Nếu ta gặp lại một điểm đang trong nhánh duyệt hiện tại, độ dài chu trình chính bằng `step - dist[node]`.

**Mã nguồn Java**:
```java
class Solution {
    public int longestCycle(int[] edges) {
        int n = edges.length;
        boolean[] visited = new boolean[n];
        int maxLen = -1;
        
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                Map<Integer, Integer> dist = new HashMap<>();
                int curr = i;
                int step = 0;
                
                while (curr != -1) {
                    if (dist.containsKey(curr)) {
                        maxLen = Math.max(maxLen, step - dist.get(curr));
                        break;
                    }
                    if (visited[curr]) break;
                    
                    visited[curr] = true;
                    dist.put(curr, step++);
                    curr = edges[curr];
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N)$ vì mỗi đỉnh được thăm và gán `visited` tối đa 1 lần.
- Space Complexity: $O(N)$ cho mảng `visited` và HashMap `dist`.

---

## 23. Graph Valid Tree (LeetCode 261)
**Đề bài chi tiết**: Cho `n` node được đánh số từ `0` đến `n-1` và một danh sách `edges` vô hướng. Viết một hàm kiểm tra xem các cạnh này có tạo thành một cây hợp lệ hay không.

**Phân tích thuật toán**: Một đồ thị là một cây (Tree) nếu thoả mãn 2 điều kiện: (1) Số lượng cạnh phải bằng chính xác `n - 1`. (2) Tất cả các đỉnh phải được kết nối với nhau (thành một thành phần liên thông). Ta có thể kiểm tra (1) bằng độ dài mảng `edges`, và kiểm tra (2) bằng BFS / DFS hoặc Disjoint Set Union (DSU) xem tất cả các đỉnh có được duyệt qua không.

**Mã nguồn Java**:
```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false;
        
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) {
            adj.get(e[0]).add(e[1]);
            adj.get(e[1]).add(e[0]);
        }
        
        boolean[] visited = new boolean[n];
        Queue<Integer> q = new LinkedList<>();
        q.offer(0);
        visited[0] = true;
        int count = 0;
        
        while (!q.isEmpty()) {
            int u = q.poll();
            count++;
            for (int v : adj.get(u)) {
                if (!visited[v]) {
                    visited[v] = true;
                    q.offer(v);
                }
            }
        }
        
        return count == n;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V + E)$

---

## 24. As Far from Land as Possible (LeetCode 1162)
**Đề bài chi tiết**: Cho một ma trận `grid` cỡ `n x n` chứa giá trị `0` (nước) và `1` (đất). Tìm một ô nước sao cho khoảng cách từ ô đó tới ô đất gần nhất là lớn nhất. Khoảng cách được tính bằng khoảng cách Manhattan: $|x0 - x1| + |y0 - y1|$. Nếu không có ô đất hoặc không có ô nước nào, trả về -1.

**Phân tích thuật toán**: Đây là dạng Multi-source BFS. Thay vì tìm đường từ ô nước đến ô đất (rất mất thời gian nếu làm nhiều lần), ta đẩy tất cả các ô đất (`1`) vào Queue ban đầu và thực hiện BFS mở rộng ra các ô nước (`0`). Ô nước cuối cùng được thăm chính là ô nước có khoảng cách lớn nhất.

**Mã nguồn Java**:
```java
class Solution {
    public int maxDistance(int[][] grid) {
        int n = grid.length;
        Queue<int[]> q = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    q.offer(new int[]{i, j});
                }
            }
        }
        
        if (q.size() == 0 || q.size() == n * n) return -1;
        
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        int distance = -1;
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int[] curr = q.poll();
                int x = curr[0], y = curr[1];
                for (int[] dir : dirs) {
                    int nx = x + dir[0];
                    int ny = y + dir[1];
                    if (nx >= 0 && nx < n && ny >= 0 && ny < n && grid[nx][ny] == 0) {
                        grid[nx][ny] = 2; // Đánh dấu đã thăm
                        q.offer(new int[]{nx, ny});
                    }
                }
            }
            distance++;
        }
        return distance;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(N^2)$ vì mỗi ô chỉ duyệt đúng 1 lần.
- Space Complexity: $O(N^2)$ cho Queue trong trường hợp xấu nhất.

---

## 25. Map of Highest Peak (LeetCode 1765)
**Đề bài chi tiết**: Bạn được cho một ma trận `isWater` kích thước `m x n`, trong đó `1` là ô nước và `0` là ô đất. Bạn phải gán chiều cao cho mỗi ô sao cho: các ô nước có chiều cao là `0`, và chiều cao giữa 2 ô kề nhau không chênh lệch quá `1`. Hãy tối đa hóa chiều cao của các ô đất. Trả về ma trận chiều cao.

**Phân tích thuật toán**: Tương tự như bài *As Far from Land as Possible*, yêu cầu này có thể được giải quyết bằng thuật toán Multi-source BFS. Các ô nước sẽ là điểm xuất phát với chiều cao `0`. Tại mỗi bước, ta tăng độ cao lên `1` cho những ô liền kề mà chưa được gán chiều cao.

**Mã nguồn Java**:
```java
class Solution {
    public int[][] highestPeak(int[][] isWater) {
        int m = isWater.length;
        int n = isWater[0].length;
        int[][] height = new int[m][n];
        for (int[] row : height) Arrays.fill(row, -1);
        
        Queue<int[]> q = new LinkedList<>();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (isWater[i][j] == 1) {
                    height[i][j] = 0;
                    q.offer(new int[]{i, j});
                }
            }
        }
        
        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int r = curr[0], c = curr[1];
            
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && height[nr][nc] == -1) {
                    height[nr][nc] = height[r][c] + 1;
                    q.offer(new int[]{nr, nc});
                }
            }
        }
        return height;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N)$
- Space Complexity: $O(M \times N)$

---

## 26. Nearest Exit from Entrance in Maze (LeetCode 1926)
**Đề bài chi tiết**: Cho một mê cung `maze` kích thước `m x n` chứa các ô trống `'.'` và các bức tường `'+'`. Bạn được cung cấp vị trí `entrance` là điểm bắt đầu. Lối ra (Exit) là bất kỳ ô trống nào nằm ở đường biên của mê cung (nhưng không phải `entrance`). Tìm số bước đi ngắn nhất từ `entrance` đến lối ra gần nhất. Nếu không có lối ra, trả về -1.

**Phân tích thuật toán**: Vấn đề đường đi ngắn nhất trong ma trận không trọng số có thể giải quyết nhanh chóng bằng BFS. Bắt đầu từ `entrance`, loang ra 4 hướng, khi gặp bất kỳ ô nào ở biên (hàng 0, cột 0, hàng m-1, cột n-1) và khác `entrance`, ta trả về số bước.

**Mã nguồn Java**:
```java
class Solution {
    public int nearestExit(char[][] maze, int[] entrance) {
        int m = maze.length, n = maze[0].length;
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{entrance[0], entrance[1], 0});
        
        maze[entrance[0]][entrance[1]] = '+'; // Đánh dấu đã thăm
        int[][] dirs = {{0,1}, {1,0}, {0,-1}, {-1,0}};
        
        while (!q.isEmpty()) {
            int[] curr = q.poll();
            int r = curr[0], c = curr[1], steps = curr[2];
            
            if ((r != entrance[0] || c != entrance[1]) && 
                (r == 0 || r == m - 1 || c == 0 || c == n - 1)) {
                return steps;
            }
            
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && maze[nr][nc] == '.') {
                    maze[nr][nc] = '+';
                    q.offer(new int[]{nr, nc, steps + 1});
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N)$
- Space Complexity: $O(M \times N)$ nếu Queue chứa nhiều phần tử, trung bình $O(\max(M, N))$.

---

## 27. Number of Ways to Arrive at Destination (LeetCode 1976)
**Đề bài chi tiết**: Cho `n` ngã tư được kết nối bởi các con đường 2 chiều. `roads[i] = [u, v, time]`. Bạn muốn đi từ nút `0` đến nút `n-1` trong khoảng thời gian ngắn nhất có thể. Đếm số lượng đường đi ngắn nhất đó. Trả về kết quả modulo $10^9 + 7$.

**Phân tích thuật toán**: Sử dụng thuật toán Dijkstra để tìm khoảng cách ngắn nhất, đồng thời duy trì một mảng `ways[]` lưu số cách đi đến mỗi node với khoảng cách tối ưu. Khi tìm được một đường đi có `dist = dist[v]`, ta cộng dồn số cách từ `u` vào `ways[v]`. Lưu ý dùng kiểu `long` cho mảng khoảng cách để tránh tràn số.

**Mã nguồn Java**:
```java
class Solution {
    public int countPaths(int n, int[][] roads) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] r : roads) {
            adj.get(r[0]).add(new int[]{r[1], r[2]});
            adj.get(r[1]).add(new int[]{r[0], r[2]});
        }
        
        long[] dist = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        int[] ways = new int[n];
        dist[0] = 0;
        ways[0] = 1;
        int MOD = 1000000007;
        
        PriorityQueue<long[]> pq = new PriorityQueue<>((a, b) -> Long.compare(a[1], b[1]));
        pq.offer(new long[]{0, 0});
        
        while (!pq.isEmpty()) {
            long[] curr = pq.poll();
            int u = (int) curr[0];
            long d = curr[1];
            
            if (d > dist[u]) continue;
            
            for (int[] edge : adj.get(u)) {
                int v = edge[0];
                long w = edge[1];
                
                if (d + w < dist[v]) {
                    dist[v] = d + w;
                    ways[v] = ways[u];
                    pq.offer(new long[]{v, dist[v]});
                } else if (d + w == dist[v]) {
                    ways[v] = (ways[v] + ways[u]) % MOD;
                }
            }
        }
        return ways[n - 1];
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(E \log V)$
- Space Complexity: $O(V + E)$

---

## 28. Course Schedule IV (LeetCode 1462)
**Đề bài chi tiết**: Có `numCourses` khoá học từ `0` đến `numCourses-1`. Cho một danh sách `prerequisites[i] = [a, b]` nghĩa là phải học khoá `a` trước khoá `b`. Bạn cũng được cho mảng `queries[j] = [u, v]`. Đối với mỗi query, trả về `true` nếu `u` là tiên quyết của `v` (trực tiếp hoặc gián tiếp), nếu không thì `false`.

**Phân tích thuật toán**: Vấn đề này thuộc dạng kiểm tra khả năng tiếp cận giữa tất cả các cặp đỉnh (All-Pairs Reachability). Do `numCourses <= 100`, ta có thể áp dụng thuật toán Floyd-Warshall với toán tử Logic (`OR` và `AND`). `reachable[i][j] = reachable[i][j] || (reachable[i][k] && reachable[k][j])`.

**Mã nguồn Java**:
```java
class Solution {
    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        boolean[][] reachable = new boolean[numCourses][numCourses];
        
        for (int[] pre : prerequisites) {
            reachable[pre[0]][pre[1]] = true;
        }
        
        for (int k = 0; k < numCourses; k++) {
            for (int i = 0; i < numCourses; i++) {
                for (int j = 0; j < numCourses; j++) {
                    if (reachable[i][k] && reachable[k][j]) {
                        reachable[i][j] = true;
                    }
                }
            }
        }
        
        List<Boolean> res = new ArrayList<>();
        for (int[] q : queries) {
            res.add(reachable[q[0]][q[1]]);
        }
        return res;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(V^3 + Q)$ với $V$ là số khoá học, $Q$ là số query.
- Space Complexity: $O(V^2)$ cho ma trận `reachable`.

---

## 29. Minimum Obstacle Removal to Reach Corner (LeetCode 2290)
**Đề bài chi tiết**: Bạn được cấp một lưới 2D `grid` gồm số `0` (ô trống) và số `1` (vật cản). Bạn có thể phá bỏ vật cản. Tìm số lượng tối thiểu vật cản cần phá để đi từ góc trái trên `(0, 0)` tới góc phải dưới `(m-1, n-1)`.

**Phân tích thuật toán**: Đồ thị này có trọng số cạnh là 0 (nếu đi vào ô trống) hoặc 1 (nếu đi vào vật cản). Vấn đề tìm đường đi ngắn nhất trên đồ thị trọng số 0-1 có thể được giải quyết cực kỳ hiệu quả bằng thuật toán 0-1 BFS sử dụng `Deque`. Nếu đi vào `0`, đẩy vào đầu Queue. Nếu đi vào `1`, đẩy vào cuối Queue.

**Mã nguồn Java**:
```java
class Solution {
    public int minimumObstacles(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        
        Deque<int[]> deque = new ArrayDeque<>();
        deque.offer(new int[]{0, 0});
        dist[0][0] = 0;
        
        int[][] dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        while (!deque.isEmpty()) {
            int[] curr = deque.pollFirst();
            int r = curr[0], c = curr[1];
            
            if (r == m - 1 && c == n - 1) return dist[r][c];
            
            for (int[] dir : dirs) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                    int weight = grid[nr][nc];
                    if (dist[r][c] + weight < dist[nr][nc]) {
                        dist[nr][nc] = dist[r][c] + weight;
                        if (weight == 0) {
                            deque.offerFirst(new int[]{nr, nc});
                        } else {
                            deque.offerLast(new int[]{nr, nc});
                        }
                    }
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time Complexity: $O(M \times N)$ nhờ vào cấu trúc 0-1 BFS.
- Space Complexity: $O(M \times N)$

---

## 30. Evaluate Division (LeetCode 399)
**Đề bài chi tiết**: Cho danh sách `equations` chứa các cặp chuỗi, và mảng `values` biểu diễn phương trình `equations[i][0] / equations[i][1] = values[i]`. Bạn được cho danh sách `queries` dạng `[C, D]`. Tìm kết quả của phép chia `C / D`. Nếu không xác định, trả về `-1.0`.

**Phân tích thuật toán**: Vấn đề có thể mô hình hoá thành đồ thị có hướng. Các chuỗi (biến) là đỉnh, `A -> B` có trọng số là `values[i]`, và `B -> A` có trọng số là `1 / values[i]`. Khi cần tính phép chia giữa `C` và `D`, bài toán trở thành tìm đường đi từ node `C` tới node `D` và nhân tất cả trọng số các cạnh trên đường đi. Ta có thể dùng DFS hoặc BFS trên đồ thị này.

**Mã nguồn Java**:
```java
class Solution {
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, Map<String, Double>> graph = new HashMap<>();
        
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            double val = values[i];
            
            graph.putIfAbsent(u, new HashMap<>());
            graph.putIfAbsent(v, new HashMap<>());
            graph.get(u).put(v, val);
            graph.get(v).put(u, 1.0 / val);
        }
        
        double[] res = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String c = queries.get(i).get(0);
            String d = queries.get(i).get(1);
            
            if (!graph.containsKey(c) || !graph.containsKey(d)) {
                res[i] = -1.0;
            } else if (c.equals(d)) {
                res[i] = 1.0;
            } else {
                Set<String> visited = new HashSet<>();
                res[i] = dfs(c, d, 1.0, graph, visited);
            }
        }
        return res;
    }
    
    private double dfs(String curr, String target, double accProd, Map<String, Map<String, Double>> graph, Set<String> visited) {
        visited.add(curr);
        if (curr.equals(target)) return accProd;
        
        for (Map.Entry<String, Double> neighbor : graph.get(curr).entrySet()) {
            if (!visited.contains(neighbor.getKey())) {
                double result = dfs(neighbor.getKey(), target, accProd * neighbor.getValue(), graph, visited);
                if (result != -1.0) return result;
            }
        }
        return -1.0;
    }
}
```
**Độ phức tạp**:
- Time Complexity: Xây dựng đồ thị $O(E)$. Mỗi truy vấn mất $O(V + E)$. Tổng cộng $O(Q \times (V + E))$.
- Space Complexity: $O(V + E)$ cho đồ thị và DFS Call Stack.
$

