# 02. Topological Sort - Bài Tập (Problems)

## 1. Course Schedule
**Đề bài chi tiết:** Có tổng cộng `numCourses` khóa học từ `0` đến `numCourses - 1`. Bạn được cung cấp một mảng `prerequisites` trong đó `prerequisites[i] = [a, b]` có nghĩa là bạn phải hoàn thành khóa học `b` trước khi lấy khóa học `a`. Trả về `true` nếu bạn có thể hoàn thành tất cả các khóa học, ngược lại trả về `false`.
**Phân tích thuật toán:** Đây là bài toán tìm xem đồ thị có hướng có chứa chu trình (cycle) hay không. Ta dùng Kahn's Algorithm (BFS) tính In-degree. Nếu tổng số khóa học xử lý được bằng `numCourses` thì trả về `true` (không có cycle).
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numCourses];
    
    for (int[] pre : prerequisites) {
        adj.get(pre[1]).add(pre[0]);
        inDegree[pre[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int count = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        count++;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return count == numCourses;
}
```

## 2. Course Schedule II
**Đề bài chi tiết:** Tương tự như Course Schedule I, nhưng thay vì trả về true/false, hãy trả về mảng chứa thứ tự các khóa học để hoàn thành tất cả. Nếu không thể hoàn thành, trả về mảng rỗng.
**Phân tích thuật toán:** Dùng Kahn's Algorithm lưu kết quả vào mảng output. Nếu phát hiện cycle, trả về mảng rỗng.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int[] findOrder(int numCourses, int[][] prerequisites) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numCourses];
    
    for (int[] pre : prerequisites) {
        adj.get(pre[1]).add(pre[0]);
        inDegree[pre[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int[] order = new int[numCourses];
    int idx = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        order[idx++] = u;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return idx == numCourses ? order : new int[0];
}
```

## 3. Alien Dictionary
**Đề bài chi tiết:** Bạn nhận được một danh sách các từ vựng của một ngôn ngữ người ngoài hành tinh, các từ này được sắp xếp theo thứ tự từ điển của ngôn ngữ đó. Hãy trả về thứ tự các chữ cái của ngôn ngữ này dưới dạng chuỗi (nếu hợp lệ), hoặc chuỗi rỗng nếu không thể (có cycle).
**Phân tích thuật toán:** Xây dựng đồ thị có hướng bằng cách so sánh 2 từ kề nhau, ký tự khác biệt đầu tiên sẽ định hình cạnh nối `char1 -> char2`. Sau đó áp dụng BFS Kahn để Sắp xếp Topo.
**Độ phức tạp:** Thời gian: $O(C)$ với C là tổng số ký tự, Không gian: $O(U)$ với U là số lượng ký tự unique.
**Mã nguồn Java:**
```java
public String alienOrder(String[] words) {
    Map<Character, List<Character>> adj = new HashMap<>();
    Map<Character, Integer> inDegree = new HashMap<>();
    for (String w : words) {
        for (char c : w.toCharArray()) {
            inDegree.put(c, 0);
            adj.put(c, new ArrayList<>());
        }
    }
    for (int i = 0; i < words.length - 1; i++) {
        String w1 = words[i], w2 = words[i + 1];
        if (w1.length() > w2.length() && w1.startsWith(w2)) return "";
        for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
            char p = w1.charAt(j), c = w2.charAt(j);
            if (p != c) {
                adj.get(p).add(c);
                inDegree.put(c, inDegree.get(c) + 1);
                break;
            }
        }
    }
    Queue<Character> q = new LinkedList<>();
    for (char c : inDegree.keySet()) {
        if (inDegree.get(c) == 0) q.offer(c);
    }
    StringBuilder sb = new StringBuilder();
    while (!q.isEmpty()) {
        char u = q.poll();
        sb.append(u);
        for (char v : adj.get(u)) {
            inDegree.put(v, inDegree.get(v) - 1);
            if (inDegree.get(v) == 0) q.offer(v);
        }
    }
    return sb.length() == inDegree.size() ? sb.toString() : "";
}
```

## 4. Minimum Height Trees
**Đề bài chi tiết:** Cho đồ thị vô hướng hình cây. Chọn đỉnh root sao cho chiều cao của cây là nhỏ nhất. Trả về list các root hợp lệ.
**Phân tích thuật toán:** Bản chất đây là thuật toán xóa lá dần dần từ ngoài vào trong giống Topological Sort. Ta xóa các node có độ đếm bậc (degree) = 1. Cho đến khi số node còn lại <= 2.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<Integer> findMinHeightTrees(int n, int[][] edges) {
    if (n == 1) return Collections.singletonList(0);
    List<Set<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; ++i) adj.add(new HashSet<>());
    for (int[] edge : edges) {
        adj.get(edge[0]).add(edge[1]);
        adj.get(edge[1]).add(edge[0]);
    }
    List<Integer> leaves = new ArrayList<>();
    for (int i = 0; i < n; ++i)
        if (adj.get(i).size() == 1) leaves.add(i);
    while (n > 2) {
        n -= leaves.size();
        List<Integer> newLeaves = new ArrayList<>();
        for (int i : leaves) {
            int j = adj.get(i).iterator().next();
            adj.get(j).remove(i);
            if (adj.get(j).size() == 1) newLeaves.add(j);
        }
        leaves = newLeaves;
    }
    return leaves;
}
```

## 5. Sequence Reconstruction
**Đề bài chi tiết:** Kiểm tra xem mảng `nums` có phải là chuỗi gốc duy nhất có thể được tái tạo lại từ các mảng con `sequences` hay không.
**Phân tích thuật toán:** Dùng Topo sort. Điều kiện tiên quyết là tại mọi bước, trong Queue chỉ được phép có duy nhất 1 đỉnh. Nếu Queue có size > 1, có nghĩa là có nhiều đường đi, => không phải duy nhất.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
    Map<Integer, List<Integer>> adj = new HashMap<>();
    Map<Integer, Integer> inDegree = new HashMap<>();
    for (List<Integer> seq : sequences) {
        for (int num : seq) {
            adj.putIfAbsent(num, new ArrayList<>());
            inDegree.putIfAbsent(num, 0);
        }
    }
    for (List<Integer> seq : sequences) {
        for (int i = 0; i < seq.size() - 1; i++) {
            int u = seq.get(i), v = seq.get(i+1);
            adj.get(u).add(v);
            inDegree.put(v, inDegree.get(v) + 1);
        }
    }
    Queue<Integer> q = new LinkedList<>();
    for (int k : inDegree.keySet()) if (inDegree.get(k) == 0) q.offer(k);
    int idx = 0;
    while (!q.isEmpty()) {
        if (q.size() > 1) return false;
        int u = q.poll();
        if (idx == nums.length || nums[idx++] != u) return false;
        for (int v : adj.get(u)) {
            inDegree.put(v, inDegree.get(v) - 1);
            if (inDegree.get(v) == 0) q.offer(v);
        }
    }
    return idx == nums.length && idx == inDegree.size();
}
```

## 6. Build a Matrix With Conditions
**Đề bài chi tiết:** Bạn được cấp 1 số K và 2 mảng điều kiện hàng (rowConditions) và cột (colConditions). Tạo một ma trận KxK chứa các số từ 1 đến K sao cho thỏa mãn điều kiện trước sau trên hàng và trên cột.
**Phân tích thuật toán:** Tiến hành Topological Sort 2 lần, một lần cho vị trí cột, một lần cho vị trí hàng. Lấy ra tọa độ (row, col) cho mỗi số từ 1 đến K để điền vào Matrix.
**Độ phức tạp:** Thời gian: $O(K + E)$, Không gian: $O(K + E)$.
**Mã nguồn Java:**
```java
public int[][] buildMatrix(int k, int[][] rowConditions, int[][] colConditions) {
    int[] rowOrder = topoSort(k, rowConditions);
    int[] colOrder = topoSort(k, colConditions);
    if (rowOrder.length == 0 || colOrder.length == 0) return new int[0][0];
    int[][] res = new int[k][k];
    Map<Integer, Integer> colMap = new HashMap<>();
    for (int i = 0; i < k; i++) colMap.put(colOrder[i], i);
    for (int i = 0; i < k; i++) {
        res[i][colMap.get(rowOrder[i])] = rowOrder[i];
    }
    return res;
}
private int[] topoSort(int k, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i <= k; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[k + 1];
    for (int[] e : edges) {
        adj.get(e[0]).add(e[1]);
        inDegree[e[1]]++;
    }
    Queue<Integer> q = new LinkedList<>();
    for (int i = 1; i <= k; i++) if (inDegree[i] == 0) q.offer(i);
    int[] order = new int[k];
    int idx = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        order[idx++] = u;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return idx == k ? order : new int[0];
}
```

## 7. Find Eventual Safe States
**Đề bài chi tiết:** Một node là "safe" nếu tất cả các đường đi từ nó đều dẫn đến các terminal nodes (node không có đỉnh kề ngoài). Trả về danh sách các safe nodes tăng dần.
**Phân tích thuật toán:** Đảo ngược toàn bộ các cạnh của đồ thị. Khi đó các terminal nodes trở thành các node có in-degree = 0. Chạy Kahn's thuật toán trên đồ thị đảo ngược này.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<Integer> eventualSafeNodes(int[][] graph) {
    int n = graph.length;
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int i = 0; i < n; i++) {
        for (int v : graph[i]) {
            adj.get(v).add(i); // Đảo ngược cạnh
            inDegree[i]++;
        }
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) if (inDegree[i] == 0) q.offer(i);
    
    boolean[] safe = new boolean[n];
    while (!q.isEmpty()) {
        int u = q.poll();
        safe[u] = true;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    
    List<Integer> res = new ArrayList<>();
    for (int i = 0; i < n; i++) if (safe[i]) res.add(i);
    return res;
}
```

## 8. Parallel Courses
**Đề bài chi tiết:** Có N khóa học. Mỗi học kỳ bạn có thể lấy bao nhiêu khóa học tùy ý. Số kỳ học ít nhất cần để hoàn thành N khóa là bao nhiêu?
**Phân tích thuật toán:** Tương tự BFS level-order traversal sử dụng Kahn's algorithm. Mỗi level là 1 học kỳ. Đếm số level.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int minimumSemesters(int n, int[][] relations) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n + 1];
    
    for (int[] r : relations) {
        adj.get(r[0]).add(r[1]);
        inDegree[r[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 1; i <= n; i++) if (inDegree[i] == 0) q.offer(i);
    
    int semesters = 0, count = 0;
    while (!q.isEmpty()) {
        int size = q.size();
        semesters++;
        while (size-- > 0) {
            int u = q.poll();
            count++;
            for (int v : adj.get(u)) {
                inDegree[v]--;
                if (inDegree[v] == 0) q.offer(v);
            }
        }
    }
    return count == n ? semesters : -1;
}
```

## 9. Sort Items by Groups Respecting Dependencies
**Đề bài chi tiết:** Có N items và M groups. Các item có dependency. Sắp xếp sao cho items của cùng group đứng cạnh nhau, và thỏa mãn cả dependency của items lẫn groups.
**Phân tích thuật toán:** Hai lớp Topological sort: 1 cho groups và 1 cho items. Gán các group độc lập thành các group ids riêng biệt, xây graph rồi sort đồng thời.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int[] sortItems(int n, int m, int[] group, List<List<Integer>> beforeItems) {
    for (int i = 0; i < n; i++) if (group[i] == -1) group[i] = m++;
    List<List<Integer>> itemGraph = new ArrayList<>(), groupGraph = new ArrayList<>();
    for (int i = 0; i < n; i++) itemGraph.add(new ArrayList<>());
    for (int i = 0; i < m; i++) groupGraph.add(new ArrayList<>());
    int[] itemInDegree = new int[n], groupInDegree = new int[m];
    
    for (int i = 0; i < n; i++) {
        for (int pre : beforeItems.get(i)) {
            itemGraph.get(pre).add(i);
            itemInDegree[i]++;
            if (group[i] != group[pre]) {
                groupGraph.get(group[pre]).add(group[i]);
                groupInDegree[group[i]]++;
            }
        }
    }
    
    List<Integer> itemOrder = topoSort(itemGraph, itemInDegree, n);
    List<Integer> groupOrder = topoSort(groupGraph, groupInDegree, m);
    if (itemOrder.isEmpty() || groupOrder.isEmpty()) return new int[0];
    
    Map<Integer, List<Integer>> orderedGroups = new HashMap<>();
    for (int item : itemOrder) {
        orderedGroups.computeIfAbsent(group[item], k -> new ArrayList<>()).add(item);
    }
    
    int[] res = new int[n];
    int idx = 0;
    for (int g : groupOrder) {
        if (orderedGroups.containsKey(g)) {
            for (int item : orderedGroups.get(g)) res[idx++] = item;
        }
    }
    return res;
}
private List<Integer> topoSort(List<List<Integer>> graph, int[] inDegree, int size) {
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < size; i++) if (inDegree[i] == 0) q.offer(i);
    List<Integer> order = new ArrayList<>();
    while (!q.isEmpty()) {
        int u = q.poll();
        order.add(u);
        for (int v : graph.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return order.size() == size ? order : new ArrayList<>();
}
```

## 10. Longest Increasing Path in a Matrix
**Đề bài chi tiết:** Tìm độ dài của đường dẫn tăng dần liên tục dài nhất trong ma trận.
**Phân tích thuật toán:** Có thể giải bằng DFS + Memoization, nhưng ở góc nhìn đồ thị, đây là DAG nơi cạnh trỏ từ ô nhỏ hơn sang ô lớn hơn kề nó. Có thể dùng Topo Sort để tính khoảng cách (longest path).
**Độ phức tạp:** Thời gian: $O(M \times N)$, Không gian: $O(M \times N)$.
**Mã nguồn Java:**
```java
// Cách tiếp cận DFS + Memoization (cũng tương đương với Topological Sort ẩn)
public int longestIncreasingPath(int[][] matrix) {
    if (matrix == null || matrix.length == 0) return 0;
    int m = matrix.length, n = matrix[0].length;
    int[][] memo = new int[m][n];
    int maxPath = 1;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            maxPath = Math.max(maxPath, dfs(matrix, i, j, memo));
        }
    }
    return maxPath;
}
private int dfs(int[][] matrix, int i, int j, int[][] memo) {
    if (memo[i][j] != 0) return memo[i][j];
    int[] dirs = {-1, 0, 1, 0, -1};
    int max = 1;
    for (int k = 0; k < 4; k++) {
        int r = i + dirs[k], c = j + dirs[k+1];
        if (r >= 0 && r < matrix.length && c >= 0 && c < matrix[0].length 
            && matrix[r][c] > matrix[i][j]) {
            max = Math.max(max, 1 + dfs(matrix, r, c, memo));
        }
    }
    memo[i][j] = max;
    return max;
}
```

## 11. Verifying an Alien Dictionary (Alien Dictionary II)
**Đề bài chi tiết:** Trong một ngôn ngữ người ngoài hành tinh, họ cũng sử dụng các chữ cái tiếng Anh nhưng có thứ tự bảng chữ cái khác biệt (được cung cấp thông qua một chuỗi `order`). Cho một mảng các từ `words`, hãy kiểm tra xem các từ này có được sắp xếp theo đúng thứ tự từ điển của ngôn ngữ đó hay không.
**Phân tích thuật toán:** Mặc dù không trực tiếp là Topological Sort, nhưng bài toán này liên quan chặt chẽ đến việc xác minh (validation) một cấu trúc từ điển đã được định nghĩa. Ta sẽ dùng bảng Hash để lưu chỉ số của từng ký tự trong `order`, sau đó so sánh từng cặp từ liền kề trong mảng `words`. Nếu có một từ đứng trước dài hơn từ đứng sau và là tiền tố (ví dụ "apple" đứng trước "app"), thì mảng không hợp lệ. Nếu gặp ký tự khác biệt đầu tiên, kiểm tra chỉ số của chúng.
**Độ phức tạp:** Thời gian: $O(M)$ với M là tổng độ dài các từ, Không gian: $O(1)$ (bảng chữ cái chỉ có 26 ký tự).
**Mã nguồn Java:**
```java
public boolean isAlienSorted(String[] words, String order) {
    int[] index = new int[26];
    for (int i = 0; i < order.length(); i++) {
        index[order.charAt(i) - 'a'] = i;
    }
    for (int i = 0; i < words.length - 1; i++) {
        String w1 = words[i];
        String w2 = words[i + 1];
        boolean diff = false;
        for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
            if (w1.charAt(j) != w2.charAt(j)) {
                if (index[w1.charAt(j) - 'a'] > index[w2.charAt(j) - 'a']) {
                    return false;
                }
                diff = true;
                break;
            }
        }
        if (!diff && w1.length() > w2.length()) {
            return false;
        }
    }
    return true;
}
```

## 12. Recipe Creation
**Đề bài chi tiết:** Bạn có một số nguyên liệu cơ bản và các công thức để tạo ra các món ăn mới. Mỗi công thức chỉ ra rằng món `A` cần các nguyên liệu `B, C, ...`. Hãy xác định xem bạn có thể tạo ra tất cả các món ăn được yêu cầu hay không. Nếu phát hiện ra có một chu trình phụ thuộc lẫn nhau (món `A` cần món `B`, món `B` lại cần món `A`), trả về `false`.
**Phân tích thuật toán:** Bài toán này tương đương với việc tìm chu trình trong đồ thị có hướng. Ta có thể sử dụng Kahn's Algorithm (Topological Sort). Đếm số bậc vào (in-degree) của mỗi món ăn. Các nguyên liệu cơ bản có in-degree bằng 0. Ta bắt đầu với các món có in-degree bằng 0 và giảm bậc của các món bị phụ thuộc. Cuối cùng, nếu tất cả các món có thể được tạo ra, số lượng nút được xử lý sẽ bằng tổng số nút.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public boolean canCreateAllRecipes(int numRecipes, int[][] dependencies) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numRecipes; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numRecipes];
    
    for (int[] dep : dependencies) {
        adj.get(dep[1]).add(dep[0]);
        inDegree[dep[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numRecipes; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int count = 0;
    while (!q.isEmpty()) {
        int curr = q.poll();
        count++;
        for (int next : adj.get(curr)) {
            inDegree[next]--;
            if (inDegree[next] == 0) {
                q.offer(next);
            }
        }
    }
    return count == numRecipes;
}
```

## 13. Check If Cycle Exists (Directed)
**Đề bài chi tiết:** Cho một đồ thị có hướng với `n` đỉnh và một danh sách các cạnh `edges`. Hãy kiểm tra xem đồ thị này có chứa chu trình hay không. Trả về `true` nếu có chu trình và `false` nếu không.
**Phân tích thuật toán:** Sử dụng Kahn's Algorithm. Đếm in-degree của tất cả các đỉnh. Đưa các đỉnh có in-degree = 0 vào Queue và thực hiện duyệt BFS. Nếu tổng số đỉnh lấy ra từ Queue bằng `n` thì đồ thị không có chu trình. Ngược lại, nếu bé hơn `n` thì đồ thị có chu trình.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public boolean hasCycle(int n, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] edge : edges) {
        adj.get(edge[0]).add(edge[1]);
        inDegree[edge[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int visitedCount = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        visitedCount++;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return visitedCount != n;
}
```

## 14. Maximum Independent Set on DAG
**Đề bài chi tiết:** Cho một đồ thị có hướng không chu trình (DAG) và trọng số của mỗi đỉnh. Hãy tìm tổng trọng số lớn nhất của một tập hợp độc lập (không có bất kỳ hai đỉnh nào trong tập có cạnh nối với nhau).
*Ghi chú: Bài toán tập độc lập lớn nhất trên đồ thị tổng quát là NP-Hard, nhưng trên một số cấu trúc dạng cây hoặc ràng buộc đặc biệt, ta có thể giải bằng DP.*
**Phân tích thuật toán:** Tính toán thứ tự Topological Sort của DAG. Sử dụng quy hoạch động (DP) duyệt qua các đỉnh theo thứ tự này. Tại mỗi đỉnh, ta có thể chọn hoặc không chọn đỉnh đó, nhưng nếu chọn, ta không thể chọn các đỉnh kề trực tiếp phía sau (hoặc phía trước). Để đơn giản hóa cho DAG cấu trúc đặc biệt, ta có thể tối ưu bằng DP kết hợp Memoization.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int maxIndependentSetDAG(int n, int[][] edges, int[] weights) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    for (int[] e : edges) adj.get(e[0]).add(e[1]);
    
    Integer[][] memo = new Integer[n][2];
    int res = 0;
    for (int i = 0; i < n; i++) {
        res = Math.max(res, dfs(i, 1, adj, weights, memo));
    }
    return res;
}

private int dfs(int u, int canTake, List<List<Integer>> adj, int[] weights, Integer[][] memo) {
    if (memo[u][canTake] != null) return memo[u][canTake];
    
    int notTake = 0;
    for (int v : adj.get(u)) {
        notTake += dfs(v, 1, adj, weights, memo);
    }
    
    int take = 0;
    if (canTake == 1) {
        take = weights[u];
        for (int v : adj.get(u)) {
            take += dfs(v, 0, adj, weights, memo);
        }
    }
    
    return memo[u][canTake] = Math.max(take, notTake);
}
```

## 15. Find All Possible Recipes from Given Supplies
**Đề bài chi tiết:** Cho mảng `recipes` chứa tên các công thức, mảng `ingredients` chứa danh sách nguyên liệu để làm từng công thức đó, và mảng `supplies` chứa các nguyên liệu bạn đang có. Trả về danh sách tất cả các công thức bạn có thể làm ra. (Có thể dùng công thức đã làm ra như một nguyên liệu cho công thức khác).
**Phân tích thuật toán:** Bài toán rất phù hợp với Topological Sort (Kahn's Algorithm). Ta xây dựng đồ thị với các cạnh từ nguyên liệu tới công thức. Ta khởi tạo Queue chứa các nguyên liệu ban đầu từ `supplies`. Các món có trong `recipes` sẽ được theo dõi `inDegree`. Mỗi khi `inDegree` của một món bằng 0, nghĩa là món đó có thể nấu được, ta thêm nó vào kết quả và đẩy vào Queue để tiếp tục làm nguyên liệu cho các món khác.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<String> findAllRecipes(String[] recipes, List<List<String>> ingredients, String[] supplies) {
    Map<String, List<String>> adj = new HashMap<>();
    Map<String, Integer> inDegree = new HashMap<>();
    
    for (String r : recipes) inDegree.put(r, 0);
    
    for (int i = 0; i < recipes.length; i++) {
        for (String ing : ingredients.get(i)) {
            adj.putIfAbsent(ing, new ArrayList<>());
            adj.get(ing).add(recipes[i]);
            inDegree.put(recipes[i], inDegree.getOrDefault(recipes[i], 0) + 1);
        }
    }
    
    Queue<String> q = new LinkedList<>();
    for (String s : supplies) q.offer(s);
    
    List<String> res = new ArrayList<>();
    while (!q.isEmpty()) {
        String curr = q.poll();
        if (adj.containsKey(curr)) {
            for (String next : adj.get(curr)) {
                inDegree.put(next, inDegree.get(next) - 1);
                if (inDegree.get(next) == 0) {
                    q.offer(next);
                    res.add(next);
                }
            }
        }
    }
    return res;
}
```

## 16. Number of Ways to Arrive at Destination
**Đề bài chi tiết:** Cho đồ thị vô hướng có $n$ đỉnh và một mảng $roads$. Bạn muốn biết có bao nhiêu đường đi ngắn nhất từ đỉnh 0 đến đỉnh $n-1$. Trả về kết quả modulo $10^9 + 7$.
**Phân tích thuật toán:** Mặc dù đồ thị ban đầu vô hướng, quá trình tìm đường đi ngắn nhất sẽ hình thành một DAG (Directed Acyclic Graph). Sử dụng thuật toán Dijkstra cải tiến, kết hợp thêm mảng `ways[]` để lưu số lượng cách đi đến một đỉnh. Khi tìm thấy một đường đi đến đỉnh $v$ có chi phí bằng chi phí ngắn nhất hiện tại, ta cộng dồn số cách từ $u$ sang $v$.
**Độ phức tạp:** Thời gian: $O(E \log V)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int countPaths(int n, int[][] roads) {
    List<List<long[]>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    for (int[] r : roads) {
        adj.get(r[0]).add(new long[]{r[1], r[2]});
        adj.get(r[1]).add(new long[]{r[0], r[2]});
    }
    
    long[] dist = new long[n];
    Arrays.fill(dist, Long.MAX_VALUE);
    dist[0] = 0;
    
    int[] ways = new int[n];
    ways[0] = 1;
    int MOD = 1_000_000_007;
    
    PriorityQueue<long[]> pq = new PriorityQueue<>((a, b) -> Long.compare(a[1], b[1]));
    pq.offer(new long[]{0, 0});
    
    while (!pq.isEmpty()) {
        long[] curr = pq.poll();
        int u = (int) curr[0];
        long d = curr[1];
        
        if (d > dist[u]) continue;
        
        for (long[] edge : adj.get(u)) {
            int v = (int) edge[0];
            long w = edge[1];
            
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                ways[v] = ways[u];
                pq.offer(new long[]{v, dist[v]});
            } else if (dist[u] + w == dist[v]) {
                ways[v] = (ways[v] + ways[u]) % MOD;
            }
        }
    }
    return ways[n - 1];
}
```

## 17. Compile Dependencies
**Đề bài chi tiết:** Bạn có $N$ file mã nguồn, được đánh số từ 0 đến $N-1$. Bạn được cho mảng `dependencies` nơi `dependencies[i] = [A, B]` có nghĩa là file B phải được biên dịch xong thì mới có thể biên dịch file A. Hãy trả về thứ tự biên dịch sao cho thời gian hợp lệ, nếu không thể hãy trả về mảng rỗng.
**Phân tích thuật toán:** Bài toán tương tự hoàn toàn **Course Schedule II**. Dùng Kahn's Algorithm hoặc DFS để thực hiện sắp xếp Topo cho các file.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int[] findCompileOrder(int numFiles, int[][] dependencies) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numFiles; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numFiles];
    
    for (int[] dep : dependencies) {
        adj.get(dep[1]).add(dep[0]);
        inDegree[dep[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numFiles; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int[] order = new int[numFiles];
    int idx = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        order[idx++] = u;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return idx == numFiles ? order : new int[0];
}
```

## 18. Task Completion Time
**Đề bài chi tiết:** Cho danh sách các công việc $N$. Mỗi công việc có một thời gian để hoàn thành `time[i]`. Một số công việc phải được thực hiện sau các công việc khác (có dependency). Nếu bạn có vô hạn nhân công để có thể chạy song song các công việc, tính thời gian nhỏ nhất để hoàn thành toàn bộ $N$ công việc.
**Phân tích thuật toán:** Sử dụng Topological Sort kết hợp với Quy hoạch động (DP). Ta duy trì một mảng `maxTime` lưu trữ thời gian hoàn thành sớm nhất cho từng công việc. Mỗi khi giảm `inDegree` của công việc $v$ kề với $u$, ta cập nhật `maxTime[v] = Math.max(maxTime[v], maxTime[u] + time[v])`. Giá trị lớn nhất trong `maxTime` sẽ là thời gian tối thiểu cho cả dự án.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int minTimeCompleteTasks(int n, int[] time, int[][] dependencies) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] dep : dependencies) {
        adj.get(dep[0]).add(dep[1]); // dep[0] -> dep[1]
        inDegree[dep[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    int[] maxTime = new int[n];
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) {
            q.offer(i);
            maxTime[i] = time[i];
        }
    }
    
    int totalTime = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        totalTime = Math.max(totalTime, maxTime[u]);
        
        for (int v : adj.get(u)) {
            maxTime[v] = Math.max(maxTime[v], maxTime[u] + time[v]);
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return totalTime;
}
```

## 19. Parallel Courses II
**Đề bài chi tiết:** Cho $n$ khóa học (đánh số $1 \to n$). Mảng `relations` xác định môn học tiên quyết. Số lượng tối đa môn học bạn có thể đăng ký trong một học kỳ là $k$. Hãy tìm số học kỳ tối thiểu để hoàn thành tất cả.
**Phân tích thuật toán:** Do $n$ khá nhỏ ($n \le 15$), bài toán này không thể chỉ dùng Greedy Topological Sort vì việc chọn học môn nào trước khi có nhiều hơn $k$ môn khả dụng có thể ảnh hưởng kết quả. Thay vào đó, ta phải dùng **Bitmask DP**. Sử dụng trạng thái nguyên là bitmask biểu diễn các môn đã học. Từ một trạng thái $mask$, tính toán các môn học tiếp theo có thể học (thỏa mãn tiên quyết) và duyệt qua các tổ hợp chập $k$ của chúng.
**Độ phức tạp:** Thời gian: $O(3^n)$, Không gian: $O(2^n)$.
**Mã nguồn Java:**
```java
public int minNumberOfSemesters(int n, int[][] relations, int k) {
    int[] pre = new int[n];
    for (int[] r : relations) {
        pre[r[1] - 1] |= (1 << (r[0] - 1));
    }
    
    int[] dp = new int[1 << n];
    Arrays.fill(dp, n);
    dp[0] = 0;
    
    for (int mask = 0; mask < (1 << n); mask++) {
        int available = 0;
        for (int i = 0; i < n; i++) {
            if ((mask & (1 << i)) == 0 && (mask & pre[i]) == pre[i]) {
                available |= (1 << i);
            }
        }
        
        for (int submask = available; submask > 0; submask = (submask - 1) & available) {
            if (Integer.bitCount(submask) <= k) {
                dp[mask | submask] = Math.min(dp[mask | submask], dp[mask] + 1);
            }
        }
    }
    return dp[(1 << n) - 1];
}
```

## 20. Parallel Courses III
**Đề bài chi tiết:** Bạn có $n$ khóa học, mảng `relations` xác định tiên quyết, và mảng `time` xác định thời gian tính bằng tháng cho mỗi khóa. Mọi khóa học có thể học song song nếu đã xong tiên quyết. Tính thời gian tối thiểu để hoàn thành toàn bộ khóa học.
**Phân tích thuật toán:** Đây là biến thể đồ thị có hướng (DAG) cho bài toán tìm đường đi dài nhất (Critical Path). Ta dùng Kahn's Algorithm, và tạo một mảng `dist[]` để lưu thời gian lớn nhất để hoàn thành một khóa học. `dist[v] = Math.max(dist[v], dist[u] + time[v])` với `u \to v`. Cuối cùng, giá trị lớn nhất trong mảng `dist` chính là kết quả.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int minimumTime(int n, int[][] relations, int[] time) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n + 1];
    
    for (int[] r : relations) {
        adj.get(r[0]).add(r[1]);
        inDegree[r[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    int[] dist = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        if (inDegree[i] == 0) {
            q.offer(i);
            dist[i] = time[i - 1];
        }
    }
    
    int maxTime = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        maxTime = Math.max(maxTime, dist[u]);
        
        for (int v : adj.get(u)) {
            dist[v] = Math.max(dist[v], dist[u] + time[v - 1]);
            inDegree[v]--;
            if (inDegree[v] == 0) {
                q.offer(v);
            }
        }
    }
    return maxTime;
}
```

## 21. Reconstruct Itinerary
**Đề bài chi tiết:** (LeetCode 332) Bạn được cung cấp một danh sách các vé máy bay nơi `tickets[i] = [from_i, to_i]`. Hãy xây dựng lại hành trình theo thứ tự bắt đầu từ sân bay `"JFK"`. Nếu có nhiều hành trình hợp lệ, bạn nên trả về hành trình có thứ tự từ điển nhỏ nhất. Tất cả các vé phải được sử dụng chính xác một lần.
**Phân tích thuật toán:** Bài toán này là tìm đường đi Euler (Eulerian Path) trên đồ thị có hướng. Mặc dù không phải là sắp xếp Topo tiêu chuẩn, thuật toán Hierholzer cho đồ thị Euler rất giống với DFS dùng trong Topo Sort (thêm đỉnh vào mảng kết quả sau khi duyệt xong tất cả các cạnh rời khỏi nó, và cuối cùng đảo ngược mảng). Ta dùng một PriorityQueue để tự động lấy các điểm đến theo thứ tự từ điển.
**Độ phức tạp:** Thời gian: $O(E \log(E/V))$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<String> findItinerary(List<List<String>> tickets) {
    Map<String, PriorityQueue<String>> flights = new HashMap<>();
    for (List<String> ticket : tickets) {
        flights.putIfAbsent(ticket.get(0), new PriorityQueue<>());
        flights.get(ticket.get(0)).add(ticket.get(1));
    }
    
    LinkedList<String> itinerary = new LinkedList<>();
    dfs("JFK", flights, itinerary);
    return itinerary;
}

private void dfs(String departure, Map<String, PriorityQueue<String>> flights, LinkedList<String> itinerary) {
    PriorityQueue<String> arrivals = flights.get(departure);
    while (arrivals != null && !arrivals.isEmpty()) {
        dfs(arrivals.poll(), flights, itinerary);
    }
    itinerary.addFirst(departure);
}
```

## 22. Software Package Installation
**Đề bài chi tiết:** Trong một hệ thống quản lý gói phần mềm, việc cài đặt một gói có thể yêu cầu cài đặt các gói khác trước (dependencies). Cho một danh sách các gói và danh sách các phụ thuộc `dependencies` dưới dạng `[gói_A, gói_B]` nghĩa là `gói_A` phụ thuộc vào `gói_B`. Hãy viết thuật toán để trả về thứ tự cài đặt hợp lệ. Nếu phát hiện ra cấu hình có vòng lặp (conflict/circular dependency), trả về một mảng rỗng.
**Phân tích thuật toán:** Bài toán rất đặc trưng cho Topological Sort (tương tự như Course Schedule II). Sử dụng Kahn's Algorithm hoặc DFS để phát hiện chu trình và xuất ra thứ tự Topo. Ta xây dựng đồ thị có hướng từ gói phụ thuộc tới gói cần nó (gói B $\to$ gói A).
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<Integer> installPackages(int n, int[][] dependencies) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] dep : dependencies) {
        adj.get(dep[1]).add(dep[0]);
        inDegree[dep[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    List<Integer> order = new ArrayList<>();
    while (!q.isEmpty()) {
        int u = q.poll();
        order.add(u);
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return order.size() == n ? order : new ArrayList<>();
}
```

## 23. Lowest Common Ancestor in a DAG
**Đề bài chi tiết:** Cho một đồ thị có hướng không chu trình (DAG) và hai đỉnh $u, v$. Hãy tìm Tổ tiên chung gần nhất (Lowest Common Ancestor - LCA) của hai đỉnh này. Trong DAG, một tổ tiên $x$ được gọi là gần nhất nếu không có bất kỳ tổ tiên chung $y$ nào khác của $u$ và $v$ mà $y$ lại là hậu duệ của $x$.
**Phân tích thuật toán:** Không giống như trên cây (Tree), một DAG có thể có nhiều LCA. Ta có thể giải bằng cách duyệt ngược từ $u$ và $v$ (nếu đồ thị lưu ngược), hoặc duyệt Topo Sort từ các nguồn (sources). Một cách đơn giản là tìm tất cả tổ tiên của $u$ và $v$, tìm phần giao của hai tập tổ tiên này. Trong tập giao đó, đỉnh nào có độ sâu hoặc thứ tự Topo trễ nhất (không có đường dẫn nào từ nó tới một đỉnh khác trong tập) chính là LCA.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V)$.
**Mã nguồn Java:**
```java
public List<Integer> findLCA(int n, int[][] edges, int u, int v) {
    List<List<Integer>> parentAdj = new ArrayList<>();
    for (int i = 0; i < n; i++) parentAdj.add(new ArrayList<>());
    for (int[] e : edges) parentAdj.get(e[1]).add(e[0]);
    
    Set<Integer> ancestorsU = getAncestors(u, parentAdj);
    Set<Integer> ancestorsV = getAncestors(v, parentAdj);
    
    Set<Integer> common = new HashSet<>(ancestorsU);
    common.retainAll(ancestorsV);
    
    List<Integer> lca = new ArrayList<>();
    for (int node : common) {
        boolean isLCA = true;
        for (int child : parentAdj.get(node)) {
            if (common.contains(child)) {
                isLCA = false;
                break;
            }
        }
        if (isLCA) lca.add(node);
    }
    return lca;
}

private Set<Integer> getAncestors(int start, List<List<Integer>> parentAdj) {
    Set<Integer> res = new HashSet<>();
    Queue<Integer> q = new LinkedList<>();
    q.offer(start);
    res.add(start);
    while (!q.isEmpty()) {
        int curr = q.poll();
        for (int p : parentAdj.get(curr)) {
            if (res.add(p)) q.offer(p);
        }
    }
    return res;
}
```

## 24. Strongly Connected Components (Kosaraju)
**Đề bài chi tiết:** Cho một đồ thị có hướng, hãy tìm tất cả các thành phần liên thông mạnh (Strongly Connected Components - SCC). SCC là một tập hợp các đỉnh con lớn nhất sao cho từ bất kỳ đỉnh nào trong tập cũng có thể đi đến bất kỳ đỉnh nào khác trong tập.
**Phân tích thuật toán:** Thuật toán Kosaraju sử dụng hai lần duyệt DFS, trong đó lần duyệt DFS đầu tiên cực kỳ giống với Topological Sort (DFS và lưu đỉnh vào Stack lúc kết thúc). Lần DFS thứ hai duyệt đồ thị chuyển vị (đảo ngược các cạnh) theo thứ tự lấy ra từ Stack. Mỗi lần gọi DFS ở bước 2 sẽ cho ra một SCC.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public List<List<Integer>> getSCCs(int n, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    List<List<Integer>> revAdj = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        adj.add(new ArrayList<>());
        revAdj.add(new ArrayList<>());
    }
    for (int[] e : edges) {
        adj.get(e[0]).add(e[1]);
        revAdj.get(e[1]).add(e[0]);
    }
    
    Stack<Integer> stack = new Stack<>();
    boolean[] visited = new boolean[n];
    for (int i = 0; i < n; i++) {
        if (!visited[i]) dfs1(i, adj, visited, stack);
    }
    
    Arrays.fill(visited, false);
    List<List<Integer>> sccs = new ArrayList<>();
    while (!stack.isEmpty()) {
        int u = stack.pop();
        if (!visited[u]) {
            List<Integer> component = new ArrayList<>();
            dfs2(u, revAdj, visited, component);
            sccs.add(component);
        }
    }
    return sccs;
}

private void dfs1(int u, List<List<Integer>> adj, boolean[] visited, Stack<Integer> stack) {
    visited[u] = true;
    for (int v : adj.get(u)) {
        if (!visited[v]) dfs1(v, adj, visited, stack);
    }
    stack.push(u);
}

private void dfs2(int u, List<List<Integer>> revAdj, boolean[] visited, List<Integer> component) {
    visited[u] = true;
    component.add(u);
    for (int v : revAdj.get(u)) {
        if (!visited[v]) dfs2(v, revAdj, visited, component);
    }
}
```

## 25. Detecting Deadlock in OS Processes
**Đề bài chi tiết:** Trong hệ điều hành, các tiến trình (processes) yêu cầu các tài nguyên (resources) và có thể giữ các tài nguyên trong khi chờ đợi tài nguyên khác. Điều này tạo ra Đồ thị Cấp phát Tài nguyên (Resource Allocation Graph). Hãy kiểm tra xem hệ thống có đang ở trạng thái Deadlock (tức là có chu trình trong đồ thị phụ thuộc tiến trình-tài nguyên) hay không.
**Phân tích thuật toán:** Đây thực chất là bài toán phát hiện chu trình trên đồ thị có hướng. Có thể sử dụng Topological Sort (Kahn's) để xem tất cả các tiến trình và tài nguyên có thể được xử lý hoàn toàn không. Nếu có một số tiến trình bị kẹt lại (in-degree > 0), thì có Deadlock.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public boolean isDeadlock(int numNodes, int[][] requestsAndAllocations) {
    // numNodes = số processes + số resources
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numNodes; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[numNodes];
    
    for (int[] edge : requestsAndAllocations) {
        adj.get(edge[0]).add(edge[1]);
        inDegree[edge[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < numNodes; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int count = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        count++;
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    // Trả về true nếu có deadlock (tức là đồ thị CÓ chu trình)
    return count != numNodes;
}
```

## 26. Game Skill Tree Progression
**Đề bài chi tiết:** Trong một trò chơi nhập vai, bạn có một cây kỹ năng (Skill Tree) dạng đồ thị DAG, trong đó bạn cần học kỹ năng A trước khi học kỹ năng B. Mọi kỹ năng đều tốn 1 điểm kỹ năng. Bạn có $K$ điểm kỹ năng. Hãy xác định xem bạn có thể học một kỹ năng mục tiêu `targetSkill` với tối đa $K$ điểm hay không (được phép học các kỹ năng điều kiện cần một cách tối ưu).
**Phân tích thuật toán:** Bài toán quy về việc tìm tập hợp nhỏ nhất các đỉnh cần phải thăm để đến được đỉnh `targetSkill`. Do mỗi node có thể cần nhiều điều kiện, ta cần thực hiện đếm tất cả tổ tiên của node đích. Thay vì Topo Sort toàn bộ, ta duyệt BFS/DFS ngược từ đỉnh mục tiêu. Tổng số đỉnh tìm được (bao gồm chính nó) chính là số điểm tối thiểu cần. Nếu số này $\le K$, trả về `true`.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V)$.
**Mã nguồn Java:**
```java
public boolean canLearnSkill(int n, int[][] prerequisites, int targetSkill, int k) {
    List<List<Integer>> parentAdj = new ArrayList<>();
    for (int i = 0; i < n; i++) parentAdj.add(new ArrayList<>());
    
    // Lưu các cạnh ngược: từ kỹ năng con trỏ về kỹ năng cha
    for (int[] pre : prerequisites) {
        parentAdj.get(pre[0]).add(pre[1]);
    }
    
    Set<Integer> requiredSkills = new HashSet<>();
    Queue<Integer> q = new LinkedList<>();
    q.offer(targetSkill);
    requiredSkills.add(targetSkill);
    
    while (!q.isEmpty()) {
        int u = q.poll();
        for (int p : parentAdj.get(u)) {
            if (!requiredSkills.contains(p)) {
                requiredSkills.add(p);
                q.offer(p);
            }
        }
    }
    return requiredSkills.size() <= k;
}
```

## 27. Longest Path in a DAG
**Đề bài chi tiết:** Cho một đồ thị có hướng không chu trình (DAG) và một mảng trọng số độ dài của các cạnh. Tìm đường đi dài nhất (Longest Path) giữa hai đỉnh bất kỳ hoặc xuất phát từ một nguồn.
**Phân tích thuật toán:** Với đồ thị tổng quát, tìm đường đi dài nhất là bài toán NP-Hard. Nhưng đối với DAG, ta có thể tìm trong thời gian tuyến tính bằng cách sử dụng Topological Sort. Ta khởi tạo một mảng `dist` với âm vô cực, ngoại trừ các điểm xuất phát (in-degree = 0) có giá trị 0. Khi lấy từng đỉnh ra theo thứ tự Topo, ta "thư giãn" (relax) các cạnh kề của nó: `dist[v] = Math.max(dist[v], dist[u] + weight(u, v))`.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int longestPathDAG(int n, int[][] edges) {
    List<List<int[]>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] e : edges) {
        adj.get(e[0]).add(new int[]{e[1], e[2]});
        inDegree[e[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    int[] dist = new int[n];
    
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int maxPath = 0;
    while (!q.isEmpty()) {
        int u = q.poll();
        for (int[] edge : adj.get(u)) {
            int v = edge[0];
            int weight = edge[1];
            dist[v] = Math.max(dist[v], dist[u] + weight);
            maxPath = Math.max(maxPath, dist[v]);
            
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    return maxPath;
}
```

## 28. Shortest Path in a DAG
**Đề bài chi tiết:** Cho một đồ thị DAG và đỉnh nguồn $S$. Hãy tìm đường đi ngắn nhất từ $S$ tới tất cả các đỉnh còn lại. Mặc dù có thể dùng thuật toán Dijkstra, hãy tối ưu hóa nó cho cấu trúc đặc biệt của DAG.
**Phân tích thuật toán:** Tương tự đường đi dài nhất trong DAG, ta sử dụng Topological Sort để xử lý các đỉnh một cách tuyến tính. Thay vì dùng Hàng đợi ưu tiên (Priority Queue) như Dijkstra $O(E \log V)$, ta duyệt các đỉnh theo Topo Order. Đối với mỗi đỉnh, ta thực hiện `dist[v] = Math.min(dist[v], dist[u] + weight(u, v))`. Điều này giảm thời gian xuống còn $O(V + E)$.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int[] shortestPathDAG(int n, int[][] edges, int start) {
    List<List<int[]>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] e : edges) {
        adj.get(e[0]).add(new int[]{e[1], e[2]});
        inDegree[e[1]]++;
    }
    
    // Topological sort Kahn
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    List<Integer> topoOrder = new ArrayList<>();
    while (!q.isEmpty()) {
        int u = q.poll();
        topoOrder.add(u);
        for (int[] edge : adj.get(u)) {
            inDegree[edge[0]]--;
            if (inDegree[edge[0]] == 0) q.offer(edge[0]);
        }
    }
    
    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    
    for (int u : topoOrder) {
        if (dist[u] != Integer.MAX_VALUE) {
            for (int[] edge : adj.get(u)) {
                int v = edge[0];
                int w = edge[1];
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                }
            }
        }
    }
    return dist;
}
```

## 29. Reachability in a DAG (Transitive Closure)
**Đề bài chi tiết:** Cho một đồ thị DAG. Xây dựng một ma trận boolean `reach[u][v]`, trong đó `reach[u][v] = true` nếu tồn tại một đường đi từ đỉnh $u$ tới đỉnh $v$, và `false` nếu ngược lại.
**Phân tích thuật toán:** Một cách tiếp cận là chạy DFS/BFS từ mọi đỉnh, mất $O(V(V+E))$. Trên DAG, ta có thể kết hợp Topological Sort với thao tác tập hợp (Bitset/HashSet) từ dưới lên trên. `reach[u] = {u} U reach[v_1] U reach[v_2] ...` với $v_i$ là các đỉnh kề của $u$. Việc xử lý ngược từ các đỉnh có out-degree bằng 0 (hoặc dùng DFS post-order) rất tiện.
**Độ phức tạp:** Thời gian: $O(V + E)$ với bitset thao tác $O(V^2 / 64)$, Không gian: $O(V^2)$.
**Mã nguồn Java:**
```java
public boolean[][] getTransitiveClosureDAG(int n, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for (int[] e : edges) {
        adj.get(e[0]).add(e[1]);
        inDegree[e[1]]++;
    }
    
    List<Integer> topoOrder = new ArrayList<>();
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.offer(i);
    }
    
    while (!q.isEmpty()) {
        int u = q.poll();
        topoOrder.add(u);
        for (int v : adj.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) q.offer(v);
        }
    }
    
    boolean[][] reach = new boolean[n][n];
    for (int i = 0; i < n; i++) reach[i][i] = true;
    
    // Duyệt ngược Topological Order
    for (int i = n - 1; i >= 0; i--) {
        int u = topoOrder.get(i);
        for (int v : adj.get(u)) {
            // Hợp nhất tập v vào tập u
            for (int k = 0; k < n; k++) {
                if (reach[v][k]) {
                    reach[u][k] = true;
                }
            }
        }
    }
    return reach;
}
```

## 30. Maximum Employee Hierarchy Level
**Đề bài chi tiết:** Trong một tập đoàn lớn, mỗi nhân viên có một cấp trên trực tiếp (trừ CEO không có cấp trên). Không có vòng lặp (nhân viên tự quản lý sếp). Cho số lượng nhân viên `N` và danh sách các quan hệ `[nhân_viên, quản_lý]`, hãy xác định số cấp bậc (level) quản lý tối đa trong tập đoàn.
**Phân tích thuật toán:** Bài toán tương đương với việc tìm chiều cao tối đa (số cạnh/số node dài nhất) trong đồ thị DAG biểu diễn hệ thống phân cấp tổ chức. Ta có thể dùng Topo Sort theo dạng duyệt BFS theo level. Ở mỗi bước lặp của vòng `while (!q.isEmpty())`, ta xử lý toàn bộ nhân viên ở level hiện tại, rồi sang level tiếp theo, tăng biến đếm `level`.
**Độ phức tạp:** Thời gian: $O(V + E)$, Không gian: $O(V + E)$.
**Mã nguồn Java:**
```java
public int maxHierarchyLevel(int n, int[][] relations) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    // relations[i] = [nhân_viên, quản_lý] => quản_lý chỉ đạo nhân_viên
    for (int[] rel : relations) {
        adj.get(rel[1]).add(rel[0]);
        inDegree[rel[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        // Những người quản lý cấp cao nhất không có sếp (in-degree = 0)
        if (inDegree[i] == 0) q.offer(i);
    }
    
    int levels = 0;
    while (!q.isEmpty()) {
        int size = q.size();
        levels++;
        for (int i = 0; i < size; i++) {
            int u = q.poll();
            for (int v : adj.get(u)) {
                inDegree[v]--;
                if (inDegree[v] == 0) {
                    q.offer(v);
                }
            }
        }
    }
    return levels;
}
```
