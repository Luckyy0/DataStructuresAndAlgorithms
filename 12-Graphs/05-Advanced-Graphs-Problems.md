# Bài tập Advanced Graphs

Danh sách 30 bài tập về các chủ đề đồ thị nâng cao (Bipartite, SCC, Eulerian, Bridges/Articulation Points). 10 bài đầu có phân tích và mã nguồn chi tiết bằng Java.

## 1. Is Graph Bipartite? (LeetCode 785)
**Đề bài chi tiết:**
Cho một đồ thị vô hướng dạng danh sách kề `graph`, với `graph[u]` là mảng các đỉnh kề với đỉnh `u`. Kiểm tra xem đồ thị có phải là đồ thị hai phía (Bipartite) hay không.

**Phân tích thuật toán:**
Dùng mảng `colors` khởi tạo bằng 0. Giá trị 1 và -1 đại diện cho hai màu khác nhau. Duyệt qua tất cả các đỉnh, nếu đỉnh chưa tô màu (`colors[i] == 0`), sử dụng DFS hoặc BFS để bắt đầu tô màu 1 cho nó. Với mỗi hàng xóm, gán màu đối nghịch. Nếu gặp hàng xóm đã tô màu và có cùng màu với đỉnh hiện tại -> Không phải Bipartite.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];
        
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0 && !dfs(graph, colors, i, 1)) {
                return false;
            }
        }
        return true;
    }
    
    private boolean dfs(int[][] graph, int[] colors, int node, int color) {
        colors[node] = color;
        for (int neighbor : graph[node]) {
            if (colors[neighbor] == color) return false;
            if (colors[neighbor] == 0 && !dfs(graph, colors, neighbor, -color)) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$ vì duyệt qua mọi đỉnh và cạnh.
- Space: $O(V)$ cho mảng colors và đệ quy stack.

---

## 2. Possible Bipartition (LeetCode 886)
**Đề bài chi tiết:**
Có $n$ người (1 đến $n$). Có một danh sách `dislikes` trong đó `dislikes[i] = [a, b]` nghĩa là người a và người b không được ở cùng một nhóm. Liệu có thể chia $n$ người thành hai nhóm được không?

**Phân tích thuật toán:**
Bài này chính là bài toán tìm Bipartite Graph. Xây dựng đồ thị từ danh sách `dislikes` (coi dislikes như các cạnh vô hướng). Chạy thuật toán tô màu như bài 1 để kiểm tra.

**Mã nguồn Java:**
```java
class Solution {
    public boolean possibleBipartition(int n, int[][] dislikes) {
        List<Integer>[] graph = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) graph[i] = new ArrayList<>();
        for (int[] d : dislikes) {
            graph[d[0]].add(d[1]);
            graph[d[1]].add(d[0]);
        }
        
        int[] color = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            if (color[i] == 0) {
                Queue<Integer> q = new LinkedList<>();
                q.add(i);
                color[i] = 1;
                while (!q.isEmpty()) {
                    int curr = q.poll();
                    for (int next : graph[curr]) {
                        if (color[next] == color[curr]) return false;
                        if (color[next] == 0) {
                            color[next] = -color[curr];
                            q.add(next);
                        }
                    }
                }
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$ 
- Space: $O(V + E)$ để lưu cấu trúc đồ thị adj list.

---

## 3. Critical Connections in a Network (LeetCode 1192)
**Đề bài chi tiết:**
Cho $n$ server được kết nối bởi $n-1$ hoặc nhiều kết nối vô hướng. Critical connection (Cầu - Bridge) là kết nối mà nếu xóa nó, một số máy chủ sẽ không thể kết nối tới nhau. Trả về mọi critical connections.

**Phân tích thuật toán:**
Sử dụng thuật toán DFS tìm Bridges (Cầu) dựa trên tư tưởng của Tarjan. Duy trì `disc` (thời gian phát hiện) và `low` (thời gian phát hiện nhỏ nhất của đỉnh liên thông qua back-edges). Cạnh $(u, v)$ là cầu nếu `low[v] > disc[u]`.

**Mã nguồn Java:**
```java
class Solution {
    int time = 0;
    
    public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
        List<Integer>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        for (List<Integer> edge : connections) {
            graph[edge.get(0)].add(edge.get(1));
            graph[edge.get(1)].add(edge.get(0));
        }
        
        int[] disc = new int[n];
        int[] low = new int[n];
        Arrays.fill(disc, -1);
        List<List<Integer>> res = new ArrayList<>();
        
        dfs(0, -1, disc, low, graph, res);
        return res;
    }
    
    private void dfs(int u, int parent, int[] disc, int[] low, List<Integer>[] graph, List<List<Integer>> res) {
        disc[u] = low[u] = ++time;
        for (int v : graph[u]) {
            if (v == parent) continue;
            if (disc[v] == -1) {
                dfs(v, u, disc, low, graph, res);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] > disc[u]) {
                    res.add(Arrays.asList(u, v));
                }
            } else {
                low[u] = Math.min(low[u], disc[v]);
            }
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$
- Space: $O(V + E)$

---

## 4. Reconstruct Itinerary (LeetCode 332)
**Đề bài chi tiết:**
Cho một mảng vé bay, mỗi vé là mảng `[from, to]`. Sắp xếp lại lịch trình bắt đầu từ "JFK". Nếu có nhiều lộ trình, trả về lộ trình có thứ tự từ điển nhỏ nhất. Bảo đảm có ít nhất một hành trình hợp lệ.

**Phân tích thuật toán:**
Bài toán tìm Eulerian Path trên đồ thị có hướng. Do phải ưu tiên thứ tự từ điển, dùng `PriorityQueue` cho danh sách kề để các điểm đến tự động sắp xếp. Áp dụng Hierholzer's Algorithm: DFS từ "JFK", luôn đi xuống đỉnh chưa thăm, xóa cạnh đã dùng, lưu kết quả theo dạng post-order rồi đảo ngược mảng kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        Map<String, PriorityQueue<String>> graph = new HashMap<>();
        for (List<String> t : tickets) {
            graph.computeIfAbsent(t.get(0), k -> new PriorityQueue<>()).add(t.get(1));
        }
        
        LinkedList<String> route = new LinkedList<>();
        dfs("JFK", graph, route);
        return route;
    }
    
    private void dfs(String airport, Map<String, PriorityQueue<String>> graph, LinkedList<String> route) {
        PriorityQueue<String> nextAirports = graph.get(airport);
        while (nextAirports != null && !nextAirports.isEmpty()) {
            dfs(nextAirports.poll(), graph, route);
        }
        route.addFirst(airport); // Post-order: add vào đầu danh sách (tương đương reverse)
    }
}
```
**Độ phức tạp:**
- Time: $O(E \log(E/V))$ (do priority queue sắp xếp cạnh ở mỗi đỉnh).
- Space: $O(V + E)$

---

## 5. Valid Arrangement of Pairs (LeetCode 2097)
**Đề bài chi tiết:**
Cho mảng `pairs` nơi `pairs[i] = [start_i, end_i]`. Sắp xếp các cặp sao cho `end_i-1 == start_i`. Đây là yêu cầu tạo mảng hợp lệ chứa tất cả các cặp.

**Phân tích thuật toán:**
Một dạng trực tiếp của Eulerian Path trên directed graph. Các số là các đỉnh, các pairs là cạnh. Để tìm Eulerian Path, đầu tiên cần xác định điểm xuất phát: là điểm có `out_degree - in_degree == 1`. Nếu không có đỉnh nào như vậy (chu trình Euler), có thể xuất phát từ bất kỳ đỉnh nào có cạnh ra. Sau đó dùng Hierholzer.

**Mã nguồn Java:**
```java
class Solution {
    public int[][] validArrangement(int[][] pairs) {
        Map<Integer, Stack<Integer>> graph = new HashMap<>();
        Map<Integer, Integer> outDegree = new HashMap<>();
        Map<Integer, Integer> inDegree = new HashMap<>();
        
        for (int[] p : pairs) {
            graph.computeIfAbsent(p[0], k -> new Stack<>()).push(p[1]);
            outDegree.put(p[0], outDegree.getOrDefault(p[0], 0) + 1);
            inDegree.put(p[1], inDegree.getOrDefault(p[1], 0) + 1);
        }
        
        int startNode = pairs[0][0];
        for (int node : outDegree.keySet()) {
            if (outDegree.get(node) - inDegree.getOrDefault(node, 0) == 1) {
                startNode = node;
                break;
            }
        }
        
        List<Integer> path = new ArrayList<>();
        dfs(startNode, graph, path);
        
        int[][] res = new int[pairs.length][2];
        for (int i = 0; i < pairs.length; i++) {
            res[i][0] = path.get(path.size() - 1 - i);
            res[i][1] = path.get(path.size() - 2 - i);
        }
        return res;
    }
    
    private void dfs(int curr, Map<Integer, Stack<Integer>> graph, List<Integer> path) {
        Stack<Integer> nextNodes = graph.get(curr);
        while (nextNodes != null && !nextNodes.isEmpty()) {
            dfs(nextNodes.pop(), graph, path);
        }
        path.add(curr);
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$
- Space: $O(V + E)$

---

## 6. Minimum Number of Days to Disconnect Island (LeetCode 1568)
**Đề bài chi tiết:**
Cho grid nhị phân, 1 là đất, 0 là nước. Một island liên thông 4 chiều. Ta có thể đổi một ô 1 thành 0 mỗi ngày. Trả về số ngày tối thiểu để grid bị chia làm 2 hoặc 0 island. 

**Phân tích thuật toán:**
Một grid chỉ cần tối đa 2 ngày (xóa 2 ô quanh 1 góc) để ngắt liên thông. Do đó đáp án chỉ có thể là 0, 1 hoặc 2.
1. Khởi đầu kiểm tra xem có phải >= 2 đảo hoặc 0 đảo không, nếu vậy đáp án = 0.
2. Thử xóa từng ô 1 (đổi thành 0) rồi kiểm tra lại. Nếu sau khi xóa 1 ô mà grid tách ra -> Đáp án là 1 (có Articulation Point).
3. Nếu không ô nào làm grid tách ra -> Đáp án = 2.

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
    
    public int minDays(int[][] grid) {
        if (countIslands(grid) != 1) return 0;
        
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    grid[i][j] = 0;
                    if (countIslands(grid) != 1) return 1;
                    grid[i][j] = 1;
                }
            }
        }
        return 2;
    }
    
    private int countIslands(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        int count = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1 && !visited[i][j]) {
                    count++;
                    dfs(grid, visited, i, j, m, n);
                }
            }
        }
        return count;
    }
    
    private void dfs(int[][] grid, boolean[][] visited, int i, int j, int m, int n) {
        visited[i][j] = true;
        for (int[] d : dirs) {
            int x = i + d[0], y = j + d[1];
            if (x >= 0 && x < m && y >= 0 && y < n && grid[x][y] == 1 && !visited[x][y]) {
                dfs(grid, visited, x, y, m, n);
            }
        }
    }
}
```
**Độ phức tạp:**
- Time: $O((R \times C)^2)$ do mỗi đỉnh có thể chạy DFS một lần.
- Space: $O(R \times C)$

---

## 7. Crack Safe (LeetCode 753)
**Đề bài chi tiết:**
Khóa két có một mật khẩu chiều dài $n$, với các chữ số từ 0 đến $k-1$. Mật khẩu tự động khớp nếu $n$ số cuối bạn nhập trùng với mật khẩu. Trả về chuỗi ngắn nhất mà đảm bảo chắc chắn có thể mở két. 

**Phân tích thuật toán:**
Chuỗi De Bruijn Sequence. Tìm Eulerian Path trên đồ thị hướng với đỉnh là chuỗi độ dài $n-1$, và cạnh là chuỗi độ dài $n$. Ta dùng Hierholzer, bắt đầu từ một chuỗi (ví dụ toàn 0), luôn cố gắng thêm một chữ số tiếp theo. Chuỗi kết quả đảo ngược post-order DFS.

**Mã nguồn Java:**
```java
class Solution {
    public String crackSafe(int n, int k) {
        if (n == 1 && k == 1) return "0";
        Set<String> visited = new HashSet<>();
        StringBuilder res = new StringBuilder();
        
        StringBuilder startNode = new StringBuilder();
        for (int i = 0; i < n - 1; i++) startNode.append("0");
        
        dfs(startNode.toString(), k, visited, res);
        res.append(startNode);
        return res.toString();
    }
    
    private void dfs(String node, int k, Set<String> visited, StringBuilder res) {
        for (int i = 0; i < k; i++) {
            String edge = node + i;
            if (!visited.contains(edge)) {
                visited.add(edge);
                String nextNode = edge.substring(1);
                dfs(nextNode, k, visited, res);
                res.append(i); // Đảo ngược do post-order
            }
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(k^n)$ - có đúng ngần ấy cạnh.
- Space: $O(k^n)$ cho Hash Set và chuỗi đệ quy.

---

## 8. Strongly Connected Components (Kosaraju's Template)
**Đề bài chi tiết:**
Cho đồ thị có hướng với $V$ đỉnh và $E$ cạnh. Tìm và in ra tất cả các thành phần liên thông mạnh (SCC) bằng thuật toán Kosaraju.

**Phân tích thuật toán:**
1. Chạy DFS tạo mảng Finishing Time (Lưu vào Stack - Đỉnh xong sau thì đẩy vào Stack sau).
2. Tạo đồ thị đảo chiều $G^{T}$.
3. Chạy lại DFS trên $G^{T}$, rút tuần tự các đỉnh từ Stack. Mỗi lần gọi DFS xuất phát từ 1 đỉnh trong Stack sẽ gom được 1 SCC.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> getSCCs(int v, List<List<Integer>> adj) {
        Stack<Integer> stack = new Stack<>();
        boolean[] visited = new boolean[v];
        
        // B1: Push to stack based on finish time
        for (int i = 0; i < v; i++) {
            if (!visited[i]) {
                dfs1(i, adj, visited, stack);
            }
        }
        
        // B2: Transpose Graph
        List<List<Integer>> transpose = new ArrayList<>();
        for(int i = 0; i < v; i++) transpose.add(new ArrayList<>());
        for (int i = 0; i < v; i++) {
            for (int neighbor : adj.get(i)) {
                transpose.get(neighbor).add(i);
            }
        }
        
        // B3: DFS on transpose graph
        Arrays.fill(visited, false);
        List<List<Integer>> sccs = new ArrayList<>();
        
        while (!stack.isEmpty()) {
            int node = stack.pop();
            if (!visited[node]) {
                List<Integer> currentScc = new ArrayList<>();
                dfs2(node, transpose, visited, currentScc);
                sccs.add(currentScc);
            }
        }
        return sccs;
    }
    
    private void dfs1(int node, List<List<Integer>> adj, boolean[] visited, Stack<Integer> stack) {
        visited[node] = true;
        for (int next : adj.get(node)) {
            if (!visited[next]) dfs1(next, adj, visited, stack);
        }
        stack.push(node);
    }
    
    private void dfs2(int node, List<List<Integer>> adj, boolean[] visited, List<Integer> scc) {
        visited[node] = true;
        scc.add(node);
        for (int next : adj.get(node)) {
            if (!visited[next]) dfs2(next, adj, visited, scc);
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$ do thực hiện DFS 2 lần.
- Space: $O(V + E)$ tạo lại đồ thị đảo.

---

## 9. Maximum Number of Accepted Invitations (LeetCode 1820)
**Đề bài chi tiết:**
Có $m$ nam và $n$ nữ tham gia tiệc. `grid[i][j] = 1` nghĩa là nam `i` muốn mời nữ `j`. Mỗi nam chỉ được mời 1 nữ, mỗi nữ chỉ đồng ý 1 nam. Tìm số lượng lời mời thành công tối đa.

**Phân tích thuật toán:**
Đây là bài Maximum Bipartite Matching cổ điển. Dùng thuật toán DFS-based augmenting paths. Với mỗi bạn nam, thử tìm một bạn nữ. Nếu bạn nữ chưa có người mời, khớp luôn. Nếu bạn nữ đã có người mời trước đó (gọi là $A$), ta thử bảo người $A$ kiếm bạn nữ khác. Nếu $A$ tìm được người khác, bạn nữ hiện tại có thể nhường cho bạn nam đang xét.

**Mã nguồn Java:**
```java
class Solution {
    public int maximumInvitations(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[] match = new int[n]; 
        Arrays.fill(match, -1);
        int maxInvites = 0;
        
        for (int i = 0; i < m; i++) {
            boolean[] visited = new boolean[n];
            if (dfs(grid, i, visited, match)) {
                maxInvites++;
            }
        }
        return maxInvites;
    }
    
    private boolean dfs(int[][] grid, int u, boolean[] visited, int[] match) {
        for (int v = 0; v < grid[0].length; v++) {
            if (grid[u][v] == 1 && !visited[v]) {
                visited[v] = true;
                // Nếu v chưa match, hoặc người đã match với v tìm được người khác
                if (match[v] == -1 || dfs(grid, match[v], visited, match)) {
                    match[v] = u;
                    return true;
                }
            }
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time: $O(M \times N)$
- Space: $O(N)$ lưu match array và visited.

---

## 10. Word Ladder II (LeetCode 126)
**Đề bài chi tiết:**
Tìm TẤT CẢ các đường đi ngắn nhất để biến đổi từ `beginWord` thành `endWord`, mỗi lần đổi 1 ký tự và từ phải có trong `wordList`.

**Phân tích thuật toán:**
Do cần tìm TẤT CẢ đường ngắn nhất, chúng ta cần dùng BFS kết hợp DFS. BFS để tạo đồ thị DAG (để loại bỏ chu trình và giữ các cạnh dẫn tới khoảng cách ngắn nhất), sau đó dùng DFS duyệt từ DAG này ra tất cả các đường đi.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);
        List<List<String>> res = new ArrayList<>();
        if (!wordSet.contains(endWord)) return res;
        
        Map<String, List<String>> graph = new HashMap<>();
        Set<String> currentLayer = new HashSet<>();
        currentLayer.add(beginWord);
        boolean found = false;
        
        while (!currentLayer.isEmpty() && !found) {
            wordSet.removeAll(currentLayer);
            Set<String> nextLayer = new HashSet<>();
            for (String word : currentLayer) {
                char[] chars = word.toCharArray();
                for (int i = 0; i < chars.length; i++) {
                    char original = chars[i];
                    for (char c = 'a'; c <= 'z'; c++) {
                        chars[i] = c;
                        String nextWord = new String(chars);
                        if (wordSet.contains(nextWord)) {
                            nextLayer.add(nextWord);
                            graph.computeIfAbsent(word, k -> new ArrayList<>()).add(nextWord);
                            if (nextWord.equals(endWord)) found = true;
                        }
                    }
                    chars[i] = original;
                }
            }
            currentLayer = nextLayer;
        }
        
        if (found) {
            List<String> path = new ArrayList<>();
            path.add(beginWord);
            dfs(beginWord, endWord, graph, path, res);
        }
        return res;
    }
    
    private void dfs(String current, String end, Map<String, List<String>> graph, List<String> path, List<List<String>> res) {
        if (current.equals(end)) {
            res.add(new ArrayList<>(path));
            return;
        }
        if (!graph.containsKey(current)) return;
        for (String next : graph.get(current)) {
            path.add(next);
            dfs(next, end, graph, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```
**Độ phức tạp:**
- Time: $O(N \times L \times 26 + \text{Paths})$ với $N$ là số từ, $L$ là độ dài từ.
- Space: $O(N + E)$ cho đồ thị và BFS set.

---

## 11. Similar String Groups (LeetCode 839)
**Đề bài chi tiết:**
Hai chuỗi được coi là tương tự (similar) nếu chúng ta có thể hoán đổi hai chữ cái ở hai vị trí khác nhau để biến chuỗi này thành chuỗi kia, hoặc chúng đã bằng nhau. Cho một danh sách các chuỗi `strs`. Mọi chuỗi trong `strs` đều là phép hoán vị của nhau (cùng độ dài và cùng các ký tự). Nhiệm vụ của bạn là tìm số lượng nhóm chuỗi tương tự (connected components).

**Phân tích thuật toán:**
Sử dụng cấu trúc dữ liệu Disjoint Set Union (DSU) hoặc DFS/BFS. Ta xây dựng đồ thị với mỗi chuỗi là một đỉnh. Có một cạnh giữa hai chuỗi nếu chúng là "similar". Hai chuỗi similar nếu số lượng vị trí khác nhau của chúng bằng 0 hoặc 2. Dùng DSU, duyệt qua mọi cặp chuỗi, nếu similar thì `union` chúng. Cuối cùng, số nhóm chính là số lượng components trong DSU.

**Mã nguồn Java:**
```java
class Solution {
    public int numSimilarGroups(String[] strs) {
        int n = strs.length;
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        int groups = n;

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isSimilar(strs[i], strs[j])) {
                    int rootI = find(parent, i);
                    int rootJ = find(parent, j);
                    if (rootI != rootJ) {
                        parent[rootI] = rootJ;
                        groups--;
                    }
                }
            }
        }
        return groups;
    }

    private boolean isSimilar(String a, String b) {
        int diff = 0;
        for (int i = 0; i < a.length(); i++) {
            if (a.charAt(i) != b.charAt(i)) {
                diff++;
                if (diff > 2) return false;
            }
        }
        return true;
    }

    private int find(int[] parent, int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent, parent[i]);
    }
}
```
**Độ phức tạp:**
- Time: $O(N^2 \times L)$ với $N$ là số lượng chuỗi, $L$ là độ dài mỗi chuỗi.
- Space: $O(N)$ cho mảng parent của DSU.

---

## 12. Sort Items by Groups Respecting Dependencies (LeetCode 1203)
**Đề bài chi tiết:**
Có $n$ items, một số thuộc về các nhóm $m$ (group). Các items có dependency `beforeItems` (phải làm item $u$ trước item $v$). Trả về danh sách sắp xếp của các items sao cho: các items cùng group phải nằm cạnh nhau, và thỏa mãn mọi dependencies. Nếu không thể sắp xếp, trả về mảng rỗng.

**Phân tích thuật toán:**
Đây là bài toán Topological Sort lồng nhau (2-level Topo Sort). Ta cần thực hiện Topo Sort ở mức nhóm (Groups) và mức phần tử (Items). 
1. Gán ID nhóm mới cho các phần tử không có nhóm (`group[i] == -1`).
2. Xây dựng đồ thị phụ thuộc cho Items và Groups, đồng thời tính In-degree.
3. Chạy thuật toán Kahn (BFS Topo Sort) cho cả Items và Groups. Nếu số lượng sau khi sort không bằng số đỉnh, nghĩa là có chu trình (không hợp lệ).
4. Sắp xếp lại Items vào từng Group, rồi ghép các Group theo thứ tự Topo Sort của Group.

**Mã nguồn Java:**
```java
class Solution {
    public int[] sortItems(int n, int m, int[] group, List<List<Integer>> beforeItems) {
        for (int i = 0; i < n; i++) {
            if (group[i] == -1) group[i] = m++;
        }
        
        List<Integer>[] itemGraph = new ArrayList[n];
        List<Integer>[] groupGraph = new ArrayList[m];
        for (int i = 0; i < n; i++) itemGraph[i] = new ArrayList<>();
        for (int i = 0; i < m; i++) groupGraph[i] = new ArrayList<>();
        
        int[] itemInDegree = new int[n];
        int[] groupInDegree = new int[m];
        
        for (int i = 0; i < n; i++) {
            for (int prev : beforeItems.get(i)) {
                itemGraph[prev].add(i);
                itemInDegree[i]++;
                if (group[i] != group[prev]) {
                    groupGraph[group[prev]].add(group[i]);
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
        for (int grp : groupOrder) {
            if (orderedGroups.containsKey(grp)) {
                for (int item : orderedGroups.get(grp)) {
                    res[idx++] = item;
                }
            }
        }
        return res;
    }
    
    private List<Integer> topoSort(List<Integer>[] graph, int[] inDegree, int count) {
        List<Integer> order = new ArrayList<>();
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < count; i++) {
            if (inDegree[i] == 0) q.offer(i);
        }
        while (!q.isEmpty()) {
            int curr = q.poll();
            order.add(curr);
            for (int next : graph[curr]) {
                inDegree[next]--;
                if (inDegree[next] == 0) q.offer(next);
            }
        }
        return order.size() == count ? order : new ArrayList<>();
    }
}
```
**Độ phức tạp:**
- Time: $O(N + M + E)$ với $E$ là số lượng dependencies.
- Space: $O(N + M + E)$ cho đồ thị.

---

## 13. Remove Max Number of Edges to Keep Graph Fully Traversable (LeetCode 1579)
**Đề bài chi tiết:**
Alice và Bob muốn duyệt đồ thị. Đồ thị có 3 loại cạnh: 1 - chỉ Alice đi được, 2 - chỉ Bob đi được, 3 - cả hai cùng đi được. Tìm số lượng cạnh tối đa có thể xóa mà Alice và Bob vẫn có thể duyệt toàn bộ các đỉnh (đồ thị liên thông cho mỗi người). Nếu không thể, trả về -1.

**Phân tích thuật toán:**
Dùng Union-Find. Để giữ lại ít cạnh nhất (xóa nhiều cạnh nhất), ta phải ưu tiên giữ lại các cạnh loại 3 (dùng chung cho cả 2 người). 
1. Khởi tạo 2 cấu trúc DSU cho Alice và Bob.
2. Nối tất cả các cạnh loại 3. Nếu nối thành công (không tạo chu trình), tăng biến đếm số cạnh đã dùng.
3. Nối các cạnh loại 1 cho Alice, loại 2 cho Bob.
4. Kiểm tra xem cả hai DSU có đúng 1 thành phần liên thông không. Số cạnh bị xóa = Tổng số cạnh ban đầu - số cạnh đã dùng.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int components;
        
        DSU(int n) {
            parent = new int[n + 1];
            for (int i = 1; i <= n; i++) parent[i] = i;
            components = n;
        }
        
        int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        
        boolean union(int x, int y) {
            int rootX = find(x), rootY = find(y);
            if (rootX != rootY) {
                parent[rootX] = rootY;
                components--;
                return true;
            }
            return false;
        }
    }
    
    public int maxNumEdgesToRemove(int n, int[][] edges) {
        DSU alice = new DSU(n);
        DSU bob = new DSU(n);
        int edgesUsed = 0;
        
        // Cạnh loại 3
        for (int[] e : edges) {
            if (e[0] == 3) {
                boolean a = alice.union(e[1], e[2]);
                boolean b = bob.union(e[1], e[2]);
                if (a || b) edgesUsed++;
            }
        }
        
        // Cạnh loại 1 và 2
        for (int[] e : edges) {
            if (e[0] == 1 && alice.union(e[1], e[2])) edgesUsed++;
            if (e[0] == 2 && bob.union(e[1], e[2])) edgesUsed++;
        }
        
        if (alice.components != 1 || bob.components != 1) return -1;
        
        return edges.length - edgesUsed;
    }
}
```
**Độ phức tạp:**
- Time: $O(E \times \alpha(V))$ với $\alpha$ là Inverse Ackermann function.
- Space: $O(V)$ cho DSU.

---

## 14. Checking Existence of Edge Length Limited Paths (LeetCode 1697)
**Đề bài chi tiết:**
Cho đồ thị vô hướng có trọng số. Có danh sách các queries `[u, v, limit]`. Kiểm tra xem có đường đi nào giữa $u$ và $v$ mà mọi cạnh trên đường đi đều nhỏ hơn `< limit` không. Trả về mảng boolean tương ứng.

**Phân tích thuật toán:**
Đây là bài toán dùng Offline Queries + Union Find. Thay vì duyệt đồ thị mỗi truy vấn, ta sắp xếp các cạnh theo trọng số tăng dần, đồng thời sắp xếp các truy vấn theo `limit` tăng dần.
Với mỗi truy vấn, ta dùng DSU hợp nhất tất cả các cạnh có trọng số `< limit`. Sau đó kiểm tra xem $u$ và $v$ có nằm chung một thành phần liên thông (cùng gốc DSU) hay không.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        DSU(int n) {
            parent = new int[n];
            for(int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int i) {
            if(parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }
        void union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if(rootI != rootJ) parent[rootI] = rootJ;
        }
    }
    
    public boolean[] distanceLimitedPathsExist(int n, int[][] edgeList, int[][] queries) {
        int q = queries.length;
        boolean[] res = new boolean[q];
        int[][] qs = new int[q][4];
        for(int i = 0; i < q; i++) {
            qs[i][0] = queries[i][0];
            qs[i][1] = queries[i][1];
            qs[i][2] = queries[i][2];
            qs[i][3] = i;
        }
        
        Arrays.sort(qs, (a, b) -> Integer.compare(a[2], b[2]));
        Arrays.sort(edgeList, (a, b) -> Integer.compare(a[2], b[2]));
        
        DSU dsu = new DSU(n);
        int edgeIdx = 0;
        
        for(int i = 0; i < q; i++) {
            while(edgeIdx < edgeList.length && edgeList[edgeIdx][2] < qs[i][2]) {
                dsu.union(edgeList[edgeIdx][0], edgeList[edgeIdx][1]);
                edgeIdx++;
            }
            res[qs[i][3]] = (dsu.find(qs[i][0]) == dsu.find(qs[i][1]));
        }
        
        return res;
    }
}
```
**Độ phức tạp:**
- Time: $O(E \log E + Q \log Q + (E + Q) \alpha(V))$ cho sắp xếp và DSU.
- Space: $O(V + Q)$ cho DSU và mảng queries.

---

## 15. Find Critical and Pseudo-Critical Edges in MST (LeetCode 1489)
**Đề bài chi tiết:**
Cho đồ thị vô hướng có trọng số. Tìm MST. Một cạnh là critical (quan trọng) nếu bỏ nó đi, trọng số MST tăng lên hoặc không thể tạo MST. Cạnh là pseudo-critical nếu nó có thể xuất hiện trong ít nhất một MST (nhưng không phải mọi MST).

**Phân tích thuật toán:**
Sử dụng thuật toán Kruskal tìm trọng số MST gốc (`minWeight`).
Để kiểm tra 1 cạnh là critical: Bỏ cạnh đó đi, chạy Kruskal. Nếu trọng số MST mới `> minWeight` hoặc đồ thị mất liên thông $\Rightarrow$ Critical.
Để kiểm tra 1 cạnh là pseudo-critical: Không bỏ cạnh, mà bắt buộc dùng cạnh đó vào MST đầu tiên, chạy Kruskal phần còn lại. Nếu trọng số MST mới `== minWeight` $\Rightarrow$ Pseudo-critical.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int i) {
            if (parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }
        boolean union(int i, int j) {
            int rootI = find(i), rootJ = find(j);
            if (rootI != rootJ) {
                parent[rootI] = rootJ;
                return true;
            }
            return false;
        }
    }

    public List<List<Integer>> findCriticalAndPseudoCriticalEdges(int n, int[][] edges) {
        int m = edges.length;
        int[][] newEdges = new int[m][4];
        for (int i = 0; i < m; i++) {
            newEdges[i][0] = edges[i][0];
            newEdges[i][1] = edges[i][1];
            newEdges[i][2] = edges[i][2];
            newEdges[i][3] = i; // Giữ lại original index
        }
        Arrays.sort(newEdges, (a, b) -> Integer.compare(a[2], b[2]));

        int minWeight = getMST(n, newEdges, -1, -1);
        List<Integer> critical = new ArrayList<>();
        List<Integer> pseudo = new ArrayList<>();

        for (int i = 0; i < m; i++) {
            if (getMST(n, newEdges, i, -1) > minWeight) {
                critical.add(newEdges[i][3]);
            } else if (getMST(n, newEdges, -1, i) == minWeight) {
                pseudo.add(newEdges[i][3]);
            }
        }

        return Arrays.asList(critical, pseudo);
    }

    private int getMST(int n, int[][] edges, int skipIdx, int pickIdx) {
        DSU dsu = new DSU(n);
        int weight = 0;
        int count = 0;

        if (pickIdx != -1) {
            dsu.union(edges[pickIdx][0], edges[pickIdx][1]);
            weight += edges[pickIdx][2];
            count++;
        }

        for (int i = 0; i < edges.length; i++) {
            if (i == skipIdx) continue;
            if (dsu.union(edges[i][0], edges[i][1])) {
                weight += edges[i][2];
                count++;
            }
        }

        return count == n - 1 ? weight : Integer.MAX_VALUE;
    }
}
```
**Độ phức tạp:**
- Time: $O(E^2 \alpha(V))$ vì với mỗi cạnh ta lại chạy Kruskal (tốn $E \alpha(V)$).
- Space: $O(V + E)$ cho DSU và mảng phụ.

---

## 16. Validate Binary Tree Nodes (LeetCode 1361)
**Đề bài chi tiết:**
Cho $n$ nodes từ 0 đến $n-1$, và mảng `leftChild`, `rightChild`. Kiểm tra xem các nodes này có tạo thành chính xác MỘT cây nhị phân hợp lệ không.

**Phân tích thuật toán:**
Một đồ thị tạo thành cây nhị phân hợp lệ khi:
1. Mỗi đỉnh có số lượng in-degree tối đa là 1. Đỉnh gốc có in-degree bằng 0.
2. Có duy nhất 1 gốc (root).
3. Bắt đầu từ gốc, có thể duyệt qua tất cả $n$ đỉnh (tính liên thông), và không có chu trình.

**Mã nguồn Java:**
```java
class Solution {
    public boolean validateBinaryTreeNodes(int n, int[] leftChild, int[] rightChild) {
        int[] inDegree = new int[n];
        for (int i = 0; i < n; i++) {
            if (leftChild[i] != -1) inDegree[leftChild[i]]++;
            if (rightChild[i] != -1) inDegree[rightChild[i]]++;
        }
        
        int root = -1;
        for (int i = 0; i < n; i++) {
            if (inDegree[i] > 1) return false;
            if (inDegree[i] == 0) {
                if (root != -1) return false;
                root = i;
            }
        }
        
        if (root == -1) return false;
        
        boolean[] visited = new boolean[n];
        Queue<Integer> q = new LinkedList<>();
        q.offer(root);
        visited[root] = true;
        int count = 1;
        
        while (!q.isEmpty()) {
            int curr = q.poll();
            int[] children = {leftChild[curr], rightChild[curr]};
            for (int child : children) {
                if (child != -1) {
                    if (visited[child]) return false; // Có chu trình
                    visited[child] = true;
                    q.offer(child);
                    count++;
                }
            }
        }
        
        return count == n;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$
- Space: $O(N)$

---

## 17. Alien Dictionary (LeetCode 269)
**Đề bài chi tiết:**
Cho danh sách các từ ngữ ngoài hành tinh đã được sắp xếp theo thứ tự từ điển ngoài hành tinh. Trả về thứ tự từ điển của các ký tự dưới dạng chuỗi. Nếu không có thứ tự hợp lệ, trả về chuỗi rỗng.

**Phân tích thuật toán:**
Đây là bài toán Topological Sort điển hình.
1. Xây dựng đồ thị hướng từ sự khác biệt đầu tiên giữa hai từ liên tiếp trong mảng. Ký tự khác biệt đầu tiên $c_1$ và $c_2$ chứng tỏ $c_1 \rightarrow c_2$.
2. Tính in-degree cho các ký tự. Chú ý edge case: "abc", "ab" thì không hợp lệ vì chuỗi con phải đứng trước.
3. Chạy BFS Topo Sort (Kahn). Nếu chuỗi kết quả thu được có độ dài bằng số ký tự độc lập xuất hiện trong từ điển, trả về nó, ngược lại là rỗng.

**Mã nguồn Java:**
```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character, List<Character>> graph = new HashMap<>();
        Map<Character, Integer> inDegree = new HashMap<>();
        for (String w : words) {
            for (char c : w.toCharArray()) {
                inDegree.putIfAbsent(c, 0);
                graph.putIfAbsent(c, new ArrayList<>());
            }
        }
        
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            if (w1.length() > w2.length() && w1.startsWith(w2)) return ""; // Invalid
            
            for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
                char c1 = w1.charAt(j), c2 = w2.charAt(j);
                if (c1 != c2) {
                    graph.get(c1).add(c2);
                    inDegree.put(c2, inDegree.get(c2) + 1);
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
            char curr = q.poll();
            sb.append(curr);
            for (char next : graph.get(curr)) {
                inDegree.put(next, inDegree.get(next) - 1);
                if (inDegree.get(next) == 0) q.offer(next);
            }
        }
        
        if (sb.length() < inDegree.size()) return "";
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Time: $O(C)$ với $C$ là tổng chiều dài các từ. 
- Space: $O(1)$ (do chỉ có tối đa 26 ký tự chữ cái tiếng Anh).

---

## 18. Make Array Strictly Increasing (LeetCode 1187)
**Đề bài chi tiết:**
Cho hai mảng `arr1` và `arr2`. Trả về số lần đổi phần tử tối thiểu để `arr1` trở thành tăng ngặt. Ta có thể thay thế một phần tử của `arr1` bằng bất kỳ phần tử nào trong `arr2`. Nếu không thể, trả về -1.

**Phân tích thuật toán:**
Dùng Dynamic Programming, hoặc coi là bài toán tìm đường đi ngắn nhất trên DAG. Gọi hàm DFS có memoization: `dfs(i, prev)` là chi phí nhỏ nhất để làm mảng từ chỉ số `i` tăng ngặt biết số trước nó là `prev`.
Tại vị trí `i`, ta có thể:
1. Giữ nguyên nếu `arr1[i] > prev` (chi phí + 0).
2. Lấy phần tử nhỏ nhất trong `arr2` mà lớn hơn `prev` (Dùng Binary Search `TreeSet` hoặc `Arrays.binarySearch`), (chi phí + 1). Lấy min của hai trường hợp.

**Mã nguồn Java:**
```java
class Solution {
    Map<String, Integer> memo = new HashMap<>();
    
    public int makeArrayIncreasing(int[] arr1, int[] arr2) {
        TreeSet<Integer> set = new TreeSet<>();
        for (int num : arr2) set.add(num);
        
        int res = dfs(0, -1, arr1, set);
        return res >= 2001 ? -1 : res;
    }
    
    private int dfs(int i, int prev, int[] arr1, TreeSet<Integer> set) {
        if (i == arr1.length) return 0;
        
        String key = i + "," + prev;
        if (memo.containsKey(key)) return memo.get(key);
        
        int cost = 2001; // Số phần tử tối đa là 2000
        
        // Cố gắng giữ nguyên
        if (arr1[i] > prev) {
            cost = dfs(i + 1, arr1[i], arr1, set);
        }
        
        // Cố gắng thay thế
        Integer replacement = set.higher(prev);
        if (replacement != null) {
            cost = Math.min(cost, 1 + dfs(i + 1, replacement, arr1, set));
        }
        
        memo.put(key, cost);
        return cost;
    }
}
```
**Độ phức tạp:**
- Time: $O(N \times M \log M)$ (với tối ưu) trong đó $N$ là chiều dài `arr1`, $M$ là chiều dài `arr2`.
- Space: $O(N \times M)$ cho bảng memo.

---

## 19. Path with Maximum Probability (LeetCode 1514)
**Đề bài chi tiết:**
Cho đồ thị vô hướng có xác suất thành công ở mỗi cạnh. Tìm đường đi từ đỉnh `start` tới `end` với xác suất thành công lớn nhất. (Xác suất đường đi bằng tích xác suất các cạnh).

**Phân tích thuật toán:**
Thuật toán Dijkstra. Vì xác suất trong khoảng [0, 1] nên tích các cạnh trên một đường luôn giảm dần, giống với tính chất đường dài ra thì trọng số tăng lên trong Dijkstra gốc. Thay vì cộng trọng số và dùng Min-Heap, ta sẽ nhân xác suất và dùng Max-Heap. Khởi tạo mảng `prob[start] = 1.0`, mọi đỉnh khác là `0.0`.

**Mã nguồn Java:**
```java
class Solution {
    class Node {
        int id;
        double prob;
        Node(int id, double prob) { this.id = id; this.prob = prob; }
    }
    
    public double maxProbability(int n, int[][] edges, double[] succProb, int start, int end) {
        List<Node>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        
        for (int i = 0; i < edges.length; i++) {
            int u = edges[i][0], v = edges[i][1];
            double p = succProb[i];
            graph[u].add(new Node(v, p));
            graph[v].add(new Node(u, p));
        }
        
        double[] probs = new double[n];
        probs[start] = 1.0;
        
        PriorityQueue<Node> pq = new PriorityQueue<>((a, b) -> Double.compare(b.prob, a.prob));
        pq.offer(new Node(start, 1.0));
        
        while (!pq.isEmpty()) {
            Node curr = pq.poll();
            int u = curr.id;
            double probU = curr.prob;
            
            if (u == end) return probU;
            if (probU < probs[u]) continue;
            
            for (Node next : graph[u]) {
                int v = next.id;
                double probV = next.prob;
                if (probU * probV > probs[v]) {
                    probs[v] = probU * probV;
                    pq.offer(new Node(v, probs[v]));
                }
            }
        }
        
        return 0.0;
    }
}
```
**Độ phức tạp:**
- Time: $O(E \log V)$
- Space: $O(V + E)$

---

## 20. Evaluate Division (LeetCode 399)
**Đề bài chi tiết:**
Cho các phương trình dạng `a / b = k`. Cần tính giá trị cho các truy vấn `c / d = ?`. Đồ thị mô phỏng quan hệ giữa các biến: `a -> b` với trọng số `k`, và `b -> a` trọng số `1.0 / k`. Trả về mảng đáp án cho từng query.

**Phân tích thuật toán:**
Dùng DFS trên đồ thị có trọng số. Mỗi truy vấn `c / d`, ta cần tìm đường đi từ `c` tới `d`. Trọng số kết quả là tích các trọng số trên cạnh đi qua. Cần cấu trúc `Map<String, Map<String, Double>>` để lưu đồ thị. Xử lý các edge cases như biến chưa từng tồn tại trong hệ thống.

**Mã nguồn Java:**
```java
class Solution {
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, Map<String, Double>> graph = new HashMap<>();
        
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            double val = values[i];
            
            graph.computeIfAbsent(u, k -> new HashMap<>()).put(v, val);
            graph.computeIfAbsent(v, k -> new HashMap<>()).put(u, 1.0 / val);
        }
        
        double[] res = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String src = queries.get(i).get(0);
            String dst = queries.get(i).get(1);
            
            if (!graph.containsKey(src) || !graph.containsKey(dst)) {
                res[i] = -1.0;
            } else if (src.equals(dst)) {
                res[i] = 1.0;
            } else {
                Set<String> visited = new HashSet<>();
                res[i] = dfs(graph, src, dst, 1.0, visited);
            }
        }
        return res;
    }
    
    private double dfs(Map<String, Map<String, Double>> graph, String curr, String end, double prod, Set<String> visited) {
        visited.add(curr);
        Map<String, Double> neighbors = graph.get(curr);
        
        if (neighbors.containsKey(end)) {
            return prod * neighbors.get(end);
        }
        
        for (Map.Entry<String, Double> entry : neighbors.entrySet()) {
            String next = entry.getKey();
            if (!visited.contains(next)) {
                double result = dfs(graph, next, end, prod * entry.getValue(), visited);
                if (result != -1.0) {
                    return result;
                }
            }
        }
        
        return -1.0;
    }
}
```
**Độ phức tạp:**
- Time: $O(Q \times (V + E))$ với $Q$ là số truy vấn, $V, E$ là số biến và số quan hệ.
- Space: $O(V + E)$

---

## 21. Network Delay Time (LeetCode 743)
**Đề bài chi tiết:**
Có mạng gồm `n` node từ 1 đến `n`. Bạn được cung cấp mảng `times` với `times[i] = (u, v, w)`, nghĩa là thời gian truyền tín hiệu từ `u` tới `v` mất `w`. Ta gửi tín hiệu từ node `k`. Trả về thời gian tối thiểu để tất cả các node nhận được tín hiệu. Trả về -1 nếu không thể đến tất cả các node.

**Phân tích thuật toán:**
Dùng thuật toán Dijkstra để tìm đường đi ngắn nhất từ đỉnh nguồn `k` tới tất cả các đỉnh khác. Sử dụng PriorityQueue để luôn lấy ra đỉnh có khoảng cách gần nhất. Kết quả chính là giá trị lớn nhất trong mảng các khoảng cách ngắn nhất từ `k`. Nếu tồn tại khoảng cách vô cực, nghĩa là có đỉnh không thể tới, ta trả về -1.

**Mã nguồn Java:**
```java
class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        Map<Integer, List<int[]>> graph = new HashMap<>();
        for (int[] time : times) {
            graph.computeIfAbsent(time[0], x -> new ArrayList<>()).add(new int[]{time[1], time[2]});
        }
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        pq.offer(new int[]{k, 0});
        
        Map<Integer, Integer> dist = new HashMap<>();
        
        while (!pq.isEmpty()) {
            int[] info = pq.poll();
            int d = info[1], node = info[0];
            
            if (dist.containsKey(node)) continue;
            dist.put(node, d);
            
            if (graph.containsKey(node)) {
                for (int[] edge : graph.get(node)) {
                    int nei = edge[0], d2 = edge[1];
                    if (!dist.containsKey(nei)) {
                        pq.offer(new int[]{nei, d + d2});
                    }
                }
            }
        }
        
        if (dist.size() != n) return -1;
        int ans = 0;
        for (int cand : dist.values()) {
            ans = Math.max(ans, cand);
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time: $O((V + E) \log V)$ vì dùng Priority Queue.
- Space: $O(V + E)$ lưu đồ thị và khoảng cách.

---

## 22. Find Eventual Safe States (LeetCode 802)
**Đề bài chi tiết:**
Cho đồ thị có hướng. Một node là "terminal node" nếu out-degree bằng 0. Một node là "safe node" nếu tất cả các đường đi xuất phát từ nó đều dẫn đến terminal node (nghĩa là nó không thể nằm trong bất kỳ vòng lặp/chu trình nào, và cũng không dẫn tới chu trình). Trả về danh sách các safe nodes đã sắp xếp.

**Phân tích thuật toán:**
Có thể dùng Topological Sort trên đồ thị ngược (Reverse Graph). Các terminal nodes có in-degree bằng 0 trên Reverse Graph. Chạy BFS đẩy từ từ các đỉnh an toàn vào kết quả.
Cách 2: Dùng DFS Coloring (0 = chưa thăm, 1 = đang thăm/thuộc chu trình, 2 = an toàn). Quét DFS từ mỗi đỉnh, nếu gặp 1 đỉnh đang thăm $\Rightarrow$ có chu trình, đánh dấu toàn bộ nhánh đó là 1. Nếu DFS trả về không có chu trình, đánh dấu 2.

**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        List<Integer> res = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            if (dfs(graph, color, i)) {
                res.add(i);
            }
        }
        return res;
    }
    
    // Trả về true nếu node i an toàn (không dẫn tới chu trình)
    private boolean dfs(int[][] graph, int[] color, int i) {
        if (color[i] > 0) {
            return color[i] == 2;
        }
        
        color[i] = 1; // Đang thăm (có khả năng nằm trong chu trình)
        for (int next : graph[i]) {
            if (!dfs(graph, color, next)) {
                return false;
            }
        }
        
        color[i] = 2; // Đã thăm xong và xác nhận an toàn
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$ mỗi đỉnh và cạnh thăm tối đa một lần.
- Space: $O(V)$ cho mảng color và stack đệ quy.

---

## 23. Longest Increasing Path in a Matrix (LeetCode 329)
**Đề bài chi tiết:**
Cho ma trận số nguyên `m x n`. Trả về độ dài đường đi tăng dần ngặt dài nhất. Bạn có thể di chuyển 4 hướng (lên, xuống, trái, phải).

**Phân tích thuật toán:**
Sử dụng DFS kết hợp Memoization. Tại mỗi ô `(i, j)`, ta tìm đường đi dài nhất xuất phát từ nó. Kết quả được lưu vào `memo[i][j]` để tái sử dụng, giúp giảm độ phức tạp từ hàm mũ xuống tuyến tính. Do yêu cầu đường đi tăng ngặt, tự động không có chu trình (đồ thị DAG ẨN). Do đó không cần mảng `visited` thông thường.

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int[][] memo = new int[m][n];
        int maxLen = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                maxLen = Math.max(maxLen, dfs(matrix, i, j, memo));
            }
        }
        return maxLen;
    }
    
    private int dfs(int[][] matrix, int i, int j, int[][] memo) {
        if (memo[i][j] != 0) return memo[i][j];
        
        int max = 1;
        for (int[] d : dirs) {
            int x = i + d[0];
            int y = j + d[1];
            if (x >= 0 && x < matrix.length && y >= 0 && y < matrix[0].length && matrix[x][y] > matrix[i][j]) {
                int len = 1 + dfs(matrix, x, y, memo);
                max = Math.max(max, len);
            }
        }
        
        memo[i][j] = max;
        return max;
    }
}
```
**Độ phức tạp:**
- Time: $O(M \times N)$ do mỗi ô được tính giá trị 1 lần.
- Space: $O(M \times N)$ cho mảng memo và call stack.

---

## 24. Sequence Reconstruction (LeetCode 444)
**Đề bài chi tiết:**
Cho một mảng gốc `nums` chứa các số từ `1` đến `n`. Bạn được cung cấp danh sách `sequences`. Kiểm tra xem `nums` có phải là **topological sort duy nhất** (shortest và unique) được tạo từ `sequences` hay không. 

**Phân tích thuật toán:**
Dựng đồ thị có hướng từ các `sequences`: mỗi phần tử chỉ tới phần tử ngay sau nó.
Sau đó sử dụng thuật toán Kahn để Topo Sort. 
Nếu muốn mảng `nums` là duy nhất:
1. Đồ thị phải sử dụng toàn bộ `n` đỉnh.
2. Trong quá trình BFS, hàng đợi (`Queue`) luôn chỉ có độ lớn tối đa là `1`. Nếu tại một thời điểm `queue.size() > 1`, nghĩa là có nhiều hơn một lựa chọn Topo Sort.
3. So sánh thứ tự Topo Sort sinh ra với `nums`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        List<Integer>[] graph = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) graph[i] = new ArrayList<>();
        int[] inDegree = new int[n + 1];
        
        for (List<Integer> seq : sequences) {
            for (int i = 0; i < seq.size() - 1; i++) {
                int u = seq.get(i);
                int v = seq.get(i + 1);
                graph[u].add(v);
                inDegree[v]++;
            }
        }
        
        Queue<Integer> q = new LinkedList<>();
        for (int i = 1; i <= n; i++) {
            if (inDegree[i] == 0) q.offer(i);
        }
        
        int idx = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1) return false;
            int curr = q.poll();
            if (nums[idx++] != curr) return false;
            
            for (int next : graph[curr]) {
                inDegree[next]--;
                if (inDegree[next] == 0) q.offer(next);
            }
        }
        
        return idx == n;
    }
}
```
**Độ phức tạp:**
- Time: $O(V + E)$ với $V = N$ và $E$ là tổng số cặp kề nhau trong `sequences`.
- Space: $O(V + E)$

---

## 25. Satisfiability of Equality Equations (LeetCode 990)
**Đề bài chi tiết:**
Cho mảng các phương trình chuỗi `equations` ví dụ `["a==b", "b!=c"]`. Ký tự là các chữ cái in thường. Hỏi hệ thống này có nghiệm hợp lệ không?

**Phân tích thuật toán:**
Bài toán tính chất bắc cầu: nếu $a = b$ và $b = c$ thì $a = c$. Sử dụng Disjoint Set Union (DSU).
1. Quét qua tất cả phương trình dạng `==`. Gom hai biến vào chung một Connected Component (DSU union).
2. Quét qua tất cả phương trình dạng `!=`. Kiểm tra xem hai biến đó có vô tình nằm chung một Component (chung Root) hay không. Nếu chung $\Rightarrow$ mâu thuẫn $\Rightarrow$ trả về false.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        void union(int x, int y) {
            int rootX = find(x), rootY = find(y);
            if (rootX != rootY) parent[rootX] = rootY;
        }
    }
    
    public boolean equationsPossible(String[] equations) {
        DSU dsu = new DSU(26);
        
        for (String eq : equations) {
            if (eq.charAt(1) == '=') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                dsu.union(u, v);
            }
        }
        
        for (String eq : equations) {
            if (eq.charAt(1) == '!') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                if (dsu.find(u) == dsu.find(v)) {
                    return false;
                }
            }
        }
        
        return true;
    }
}
```
**Độ phức tạp:**
- Time: $O(N)$ do số ký tự chữ cái là cố định (26), Union-Find tốn thời gian hằng số.
- Space: $O(1)$ mảng kích thước 26.

---

## 26. Redundant Connection (LeetCode 684)
**Đề bài chi tiết:**
Cho đồ thị vô hướng dạng cây (có `n` đỉnh, `n-1` cạnh), nhưng bị chèn thêm **chính xác 1 cạnh thừa**, làm xuất hiện đúng 1 chu trình. Cho mảng `edges`. Trả về cạnh cần xóa để đồ thị trở lại thành cây. Nếu có nhiều cạnh thỏa mãn, trả về cạnh xuất hiện cuối cùng trong input `edges`.

**Phân tích thuật toán:**
Dùng Union-Find (DSU). Khởi tạo `n` đỉnh riêng biệt. Lặp qua các cạnh, với mỗi cạnh `(u, v)`:
Nếu `u` và `v` đã ở chung một nhóm (DSU find ra cùng gốc), thì cạnh này chính là nguyên nhân tạo ra chu trình $\Rightarrow$ trả về ngay cạnh này (vì bài duyệt lần lượt nên chắc chắn là cạnh cuối). 
Nếu không, gọi union `u` và `v`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        DSU(int n) {
            parent = new int[n + 1];
            for (int i = 1; i <= n; i++) parent[i] = i;
        }
        int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        boolean union(int x, int y) {
            int rx = find(x), ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                return true;
            }
            return false; // Đã nằm cùng Component -> Tạo chu trình
        }
    }
    
    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        DSU dsu = new DSU(n);
        
        for (int[] e : edges) {
            if (!dsu.union(e[0], e[1])) {
                return e;
            }
        }
        return new int[0];
    }
}
```
**Độ phức tạp:**
- Time: $O(V \times \alpha(V))$ xấp xỉ tuyến tính.
- Space: $O(V)$ cho DSU.

---

## 27. Redundant Connection II (LeetCode 685)
**Đề bài chi tiết:**
Bài toán tương tự Redundant Connection 1, nhưng đồ thị là **có hướng** (cây có hướng với các cạnh chạy từ cha xuống con, trong đó mọi node ngoại trừ gốc có đúng 1 cha). Bây giờ thêm 1 cạnh thừa có hướng. Tìm và xóa cạnh đó để đồ thị quay về cây gốc.

**Phân tích thuật toán:**
Khi thêm một cạnh vào Directed Tree, có 3 trường hợp xảy ra đối với số lượng In-degree:
1. Có một node có In-degree = 2, và có chu trình (cạnh thêm chỉ ngược về tổ tiên).
2. Có một node có In-degree = 2, và không có chu trình (cạnh thêm từ một nhánh này sang nhánh kia).
3. KHÔNG có node nào có In-degree = 2 (tất cả In-degree = 1), nhưng có chu trình (cạnh thêm nối node lá ngược về Root).

Xử lý:
- Tính In-degree. Nếu có node có 2 In-degree, thì 1 trong 2 cạnh đi tới node đó bị lỗi (lưu là `cand1`, `cand2`).
- Chạy Union-Find. Đừng thêm `cand2` vào (thử coi như bỏ `cand2`). 
- Nếu DSU báo có chu trình:
  - Nếu đã có `cand1, cand2`, thì cạnh bị bỏ (`cand2`) là vô tội, chu trình sinh ra do `cand1`. Trả về `cand1`.
  - Nếu không có node In-degree = 2 (case 3), trả về luôn cạnh gây ra chu trình.
- Nếu không có chu trình, nghĩa là bỏ `cand2` xong mọi thứ êm đẹp $\Rightarrow$ trả về `cand2`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        DSU(int n) {
            parent = new int[n + 1];
            for(int i = 1; i <= n; i++) parent[i] = i;
        }
        int find(int x) {
            if(parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        boolean union(int x, int y) {
            int rx = find(x), ry = find(y);
            if(rx != ry) {
                parent[rx] = ry;
                return true;
            }
            return false;
        }
    }
    
    public int[] findRedundantDirectedConnection(int[][] edges) {
        int n = edges.length;
        int[] parent = new int[n + 1];
        int[] cand1 = null, cand2 = null;
        
        for (int[] e : edges) {
            int u = e[0], v = e[1];
            if (parent[v] != 0) { // Đã có cha rồi, v có in-degree = 2
                cand1 = new int[]{parent[v], v};
                cand2 = new int[]{u, v};
                break;
            } else {
                parent[v] = u;
            }
        }
        
        DSU dsu = new DSU(n);
        for (int[] e : edges) {
            // Không xét cand2, giả sử cand2 là cạnh cần xoá
            if (cand2 != null && e[0] == cand2[0] && e[1] == cand2[1]) continue;
            
            if (!dsu.union(e[0], e[1])) {
                // Tạo chu trình
                if (cand1 != null) return cand1; // Case 1
                return e; // Case 3
            }
        }
        
        return cand2; // Case 2
    }
}
```
**Độ phức tạp:**
- Time: $O(V \times \alpha(V))$
- Space: $O(V)$

---

## 28. As Far from Land as Possible (LeetCode 1162)
**Đề bài chi tiết:**
Cho ma trận `n x n`, `1` là đất, `0` là nước. Tìm ô nước nào có khoảng cách Manhattan lớn nhất đến ô đất gần nhất, và trả về khoảng cách đó. Nếu toàn đất hoặc toàn nước trả về -1.

**Phân tích thuật toán:**
Dùng Multi-source BFS. Gán tất cả các ô đất (`1`) vào hàng đợi (Queue), khoảng cách của chúng là 0. Chạy BFS ra xung quanh (loang nước). Khoảng cách nước lan tới ô `0` cuối cùng chính là đáp án lớn nhất.

**Mã nguồn Java:**
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
        
        int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
        int distance = -1;
        
        while (!q.isEmpty()) {
            int size = q.size();
            distance++;
            for (int k = 0; k < size; k++) {
                int[] curr = q.poll();
                for (int[] d : dirs) {
                    int x = curr[0] + d[0];
                    int y = curr[1] + d[1];
                    if (x >= 0 && x < n && y >= 0 && y < n && grid[x][y] == 0) {
                        grid[x][y] = 2; // Đánh dấu đã thăm
                        q.offer(new int[]{x, y});
                    }
                }
            }
        }
        
        return distance;
    }
}
```
**Độ phức tạp:**
- Time: $O(N^2)$ vì mỗi ô được thăm tối đa 1 lần.
- Space: $O(N^2)$ cho hàng đợi BFS.

---

## 29. Shortest Bridge (LeetCode 934)
**Đề bài chi tiết:**
Có 2 hòn đảo trong ma trận (đảo là tập hợp các số `1` liên thông 4 hướng). Ta có thể đổi số `0` thành `1` để làm cầu nối 2 hòn đảo. Trả về số ô số `0` lật ít nhất để 2 hòn đảo nối với nhau.

**Phân tích thuật toán:**
Kết hợp DFS và Multi-source BFS.
1. Dùng DFS tìm hòn đảo đầu tiên, đánh dấu mọi ô của nó (chuyển sang `2`) và đẩy tất cả các ô đó vào Queue. Dừng luôn không tìm đảo khác.
2. Dùng BFS với Queue hiện có (đang chứa toàn bộ biên của đảo 1) để loang ra các ô `0` xung quanh, mỗi vòng BFS đánh dấu ô `0` thành `-1` (hoặc `2`). 
3. Ngay khi BFS chạm phải một ô `1` (nghĩa là đã chạm tới hòn đảo thứ hai), trả về số bước BFS đã đi (Level của BFS).

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{1,0},{-1,0},{0,1},{0,-1}};
    
    public int shortestBridge(int[][] grid) {
        int n = grid.length;
        Queue<int[]> q = new LinkedList<>();
        boolean found = false;
        
        // DFS tìm đảo 1
        for (int i = 0; i < n && !found; i++) {
            for (int j = 0; j < n && !found; j++) {
                if (grid[i][j] == 1) {
                    dfs(grid, i, j, q, n);
                    found = true;
                }
            }
        }
        
        // BFS tìm đường ngắn nhất
        int step = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            for (int k = 0; k < size; k++) {
                int[] curr = q.poll();
                for (int[] d : dirs) {
                    int x = curr[0] + d[0], y = curr[1] + d[1];
                    if (x >= 0 && x < n && y >= 0 && y < n) {
                        if (grid[x][y] == 1) return step; // Chạm đảo 2
                        if (grid[x][y] == 0) {
                            grid[x][y] = 2; // Đã thăm
                            q.offer(new int[]{x, y});
                        }
                    }
                }
            }
            step++;
        }
        
        return step;
    }
    
    private void dfs(int[][] grid, int i, int j, Queue<int[]> q, int n) {
        if (i < 0 || i >= n || j < 0 || j >= n || grid[i][j] != 1) return;
        
        grid[i][j] = 2; // Đánh dấu thuộc đảo 1
        q.offer(new int[]{i, j});
        
        for (int[] d : dirs) dfs(grid, i + d[0], j + d[1], q, n);
    }
}
```
**Độ phức tạp:**
- Time: $O(N^2)$ vì mỗi ô chỉ duyệt qua hằng số lần.
- Space: $O(N^2)$ cho Stack đệ quy DFS và Queue BFS.

---

## 30. Design Graph With Shortest Path Calculator (LeetCode 2642)
**Đề bài chi tiết:**
Thiết kế một lớp API quản lý đồ thị có hướng, có trọng số. Bao gồm: khởi tạo với danh sách cạnh, `addEdge(int[] edge)` thêm 1 cạnh mới, `shortestPath(int node1, int node2)` trả về chi phí tối thiểu từ node1 đến node2. Khó khăn là dữ liệu thay đổi liên tục và nhiều queries.

**Phân tích thuật toán:**
Dùng đồ thị dạng danh sách kề `List<List<int[]>>`. `addEdge` chạy với $O(1)$.
Khi gọi `shortestPath`, thực thi Dijkstra từ đỉnh `node1`. Vì đồ thị không có cạnh âm, Dijkstra tối ưu nhất với PriorityQueue. Do số lượng node nhỏ ($n \le 100$) và được gọi tối đa 100 lần, mỗi lần gọi Dijkstra chi phí chấp nhận được.

**Mã nguồn Java:**
```java
class Graph {
    List<List<int[]>> adj;
    int n;

    public Graph(int n, int[][] edges) {
        this.n = n;
        adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) addEdge(e);
    }
    
    public void addEdge(int[] edge) {
        adj.get(edge[0]).add(new int[]{edge[1], edge[2]});
    }
    
    public int shortestPath(int node1, int node2) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        
        dist[node1] = 0;
        pq.offer(new int[]{node1, 0});
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            
            if (u == node2) return d;
            if (d > dist[u]) continue;
            
            for (int[] neighbor : adj.get(u)) {
                int v = neighbor[0], weight = neighbor[1];
                if (d + weight < dist[v]) {
                    dist[v] = d + weight;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        
        return -1;
    }
}
```
**Độ phức tạp:**
- Time: Khởi tạo $O(V + E)$. Hàm `addEdge`: $O(1)$. Hàm `shortestPath`: $O(E \log V)$ mỗi truy vấn.
- Space: $O(V + E)$ lưu đồ thị tổng hợp.
