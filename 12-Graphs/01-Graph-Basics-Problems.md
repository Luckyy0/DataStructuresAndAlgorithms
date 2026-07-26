# Graph Basics Problems

## 1. Number of Islands
**Đề bài chi tiết:**  
Cho một lưới (grid) 2D có kích thước `m x n`, bao gồm các ký tự `'1'` (đất liền) và `'0'` (nước). Đếm số lượng các "hòn đảo". Một hòn đảo được bao quanh bởi nước và được tạo thành bằng cách nối các phần đất liền kề theo 4 hướng.

**Phân tích thuật toán:**  
Sử dụng thuật toán DFS hoặc BFS. Duyệt qua từng ô, nếu gặp `'1'`, tăng biến đếm số đảo lên 1, đồng thời dùng DFS/BFS để đổi toàn bộ phần đất liền kề của hòn đảo này thành `'0'` để tránh đếm lại.

**Mã nguồn Java:**
```java
class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int numIslands = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == '1') {
                    numIslands++;
                    dfs(grid, i, j);
                }
            }
        }
        return numIslands;
    }
    
    private void dfs(char[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') {
            return;
        }
        grid[i][j] = '0';
        dfs(grid, i + 1, j);
        dfs(grid, i - 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i, j - 1);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$ 
- Space Complexity: $O(m \times n)$ 

---

## 2. Max Area of Island
**Đề bài chi tiết:**  
Cho một ma trận nhị phân 2D `grid`. Tìm diện tích lớn nhất của một hòn đảo. Diện tích là số ô có giá trị 1 của hòn đảo đó.

**Phân tích thuật toán:**  
Duyệt DFS hoặc BFS để tính diện tích của mỗi hòn đảo. Đổi `'1'` thành `'0'` sau khi visit và cộng dồn số lượng ô thuộc mỗi đảo trong quá trình loang ra, rồi cập nhật max.

**Mã nguồn Java:**
```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int maxArea = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1) {
                    maxArea = Math.max(maxArea, dfs(grid, i, j));
                }
            }
        }
        return maxArea;
    }
    
    private int dfs(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
            return 0;
        }
        grid[i][j] = 0;
        return 1 + dfs(grid, i + 1, j) + dfs(grid, i - 1, j) + dfs(grid, i, j + 1) + dfs(grid, i, j - 1);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$
- Space Complexity: $O(m \times n)$

---

## 3. Clone Graph
**Đề bài chi tiết:**  
Cho một tham chiếu đến một node của một đồ thị vô hướng và liên thông. Trả về một bản sao sâu (deep copy) của đồ thị đó.

**Phân tích thuật toán:**  
Dùng một Hash Map để lưu trữ quan hệ giữa các Node gốc và Node clone. Duyệt đồ thị bằng DFS. Nếu node hiện tại chưa trong Map, tạo node clone và lưu vào Map, đệ quy clone các neighbor.

**Mã nguồn Java:**
```java
class Solution {
    private HashMap<Node, Node> visited = new HashMap<>();
    
    public Node cloneGraph(Node node) {
        if (node == null) return null;
        if (visited.containsKey(node)) return visited.get(node);
        
        Node cloneNode = new Node(node.val, new ArrayList<>());
        visited.put(node, cloneNode);
        
        for (Node neighbor : node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neighbor));
        }
        return cloneNode;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V)$

---

## 4. Pacific Atlantic Water Flow
**Đề bài chi tiết:**  
Cho ma trận `m x n` biểu diễn độ cao. Nước chảy từ ô cao sang ô thấp theo 4 hướng. Tìm các ô mà nước chảy được ra cả Thái Bình Dương (trái/trên) và Đại Tây Dương (phải/dưới).

**Phân tích thuật toán:**  
DFS ngược từ các ô biên tiếp giáp đại dương (lên các ô cao hơn hoặc bằng). Giao điểm của kết quả DFS từ Pacific và Atlantic là đáp án.

**Mã nguồn Java:**
```java
class Solution {
    int[][] dir = {{0,1}, {0,-1}, {1,0}, {-1,0}};
    public List<List<Integer>> pacificAtlantic(int[][] heights) {
        List<List<Integer>> result = new ArrayList<>();
        if(heights == null || heights.length == 0) return result;
        
        int m = heights.length, n = heights[0].length;
        boolean[][] pac = new boolean[m][n], atl = new boolean[m][n];
        
        for(int i = 0; i < m; i++) { dfs(heights, pac, -1, i, 0); dfs(heights, atl, -1, i, n-1); }
        for(int i = 0; i < n; i++) { dfs(heights, pac, -1, 0, i); dfs(heights, atl, -1, m-1, i); }
        
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++)
                if (pac[i][j] && atl[i][j]) result.add(Arrays.asList(i, j));
        return result;
    }
    
    private void dfs(int[][] h, boolean[][] v, int prev, int r, int c) {
        if (r < 0 || r >= h.length || c < 0 || c >= h[0].length || v[r][c] || h[r][c] < prev) return;
        v[r][c] = true;
        for (int[] d : dir) dfs(h, v, h[r][c], r + d[0], c + d[1]);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$
- Space Complexity: $O(m \times n)$

---

## 5. Surrounded Regions
**Đề bài chi tiết:**  
Cho bảng 2D gồm `'X'` và `'O'`. Bắt các vùng `'O'` bị bao vây bởi `'X'` bằng cách đổi `'O'` thành `'X'`.

**Phân tích thuật toán:**  
DFS từ các `'O'` nằm trên biên và đánh dấu tạm là `'#'`. Lặp lại ma trận, đổi `'O'` còn lại thành `'X'` và `'#'` thành `'O'`.

**Mã nguồn Java:**
```java
class Solution {
    public void solve(char[][] board) {
        int m = board.length, n = board[0].length;
        for(int i = 0; i < m; i++) { dfs(board, i, 0); dfs(board, i, n-1); }
        for(int j = 0; j < n; j++) { dfs(board, 0, j); dfs(board, m-1, j); }
        
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == 'O') board[i][j] = 'X';
                else if(board[i][j] == '#') board[i][j] = 'O';
            }
        }
    }
    private void dfs(char[][] board, int i, int j) {
        if(i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != 'O') return;
        board[i][j] = '#';
        dfs(board, i+1, j); dfs(board, i-1, j); dfs(board, i, j+1); dfs(board, i, j-1);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$
- Space Complexity: $O(m \times n)$

---

## 6. Rotting Oranges
**Đề bài chi tiết:**  
Cho grid: `0` (trống), `1` (cam tươi), `2` (cam thối). Mỗi phút, cam tươi tiếp giáp cam thối cũng thối. Tìm số phút ít nhất để không còn cam tươi.

**Phân tích thuật toán:**  
Sử dụng Multi-source BFS. Đẩy các cam thối vào Queue ban đầu và đếm số cam tươi. BFS loang dần từng phút.

**Mã nguồn Java:**
```java
class Solution {
    public int orangesRotting(int[][] grid) {
        Queue<int[]> q = new LinkedList<>();
        int fresh = 0, m = grid.length, n = grid[0].length;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 2) q.offer(new int[]{i, j});
                else if(grid[i][j] == 1) fresh++;
            }
        }
        if(fresh == 0) return 0;
        int mins = 0;
        int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
        while(!q.isEmpty()) {
            int size = q.size();
            boolean rot = false;
            for(int i = 0; i < size; i++) {
                int[] curr = q.poll();
                for(int[] d : dirs) {
                    int r = curr[0] + d[0], c = curr[1] + d[1];
                    if(r >= 0 && r < m && c >= 0 && c < n && grid[r][c] == 1) {
                        grid[r][c] = 2; fresh--;
                        q.offer(new int[]{r, c});
                        rot = true;
                    }
                }
            }
            if(rot) mins++;
        }
        return fresh == 0 ? mins : -1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$
- Space Complexity: $O(m \times n)$

---

## 7. Walls and Gates
**Đề bài chi tiết:**  
Tính khoảng cách từ cổng (`0`) gần nhất đến tất cả các phòng trống (`INF`).

**Phân tích thuật toán:**  
Multi-source BFS từ tất cả các cổng loang ra các phòng trống xung quanh để đảm bảo tìm được khoảng cách ngắn nhất.

**Mã nguồn Java:**
```java
class Solution {
    public void wallsAndGates(int[][] rooms) {
        Queue<int[]> q = new LinkedList<>();
        int m = rooms.length, n = rooms[0].length;
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++)
                if (rooms[i][j] == 0) q.add(new int[] {i, j});
                
        int[][] dirs = {{-1,0}, {1,0}, {0,-1}, {0,1}};
        while (!q.isEmpty()) {
            int[] point = q.poll();
            for (int[] d : dirs) {
                int r = point[0] + d[0], c = point[1] + d[1];
                if (r < 0 || r >= m || c < 0 || c >= n || rooms[r][c] != Integer.MAX_VALUE) continue;
                rooms[r][c] = rooms[point[0]][point[1]] + 1;
                q.add(new int[] {r, c});
            }
        }
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(m \times n)$
- Space Complexity: $O(m \times n)$

---

## 8. Word Ladder
**Đề bài chi tiết:**  
Tìm số phép biến đổi từ ngắn nhất từ `beginWord` đến `endWord`, mỗi lần đổi 1 ký tự và phải thuộc `wordList`.

**Phân tích thuật toán:**  
BFS tìm đường đi ngắn nhất. Thay đổi từng ký tự từ 'a' đến 'z' để tìm từ hợp lệ trong Set.

**Mã nguồn Java:**
```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        if (!dict.contains(endWord)) return 0;
        
        Queue<String> q = new LinkedList<>();
        q.add(beginWord);
        int level = 1;
        
        while(!q.isEmpty()){
            int size = q.size();
            for(int i = 0; i < size; i++){
                char[] chars = q.poll().toCharArray();
                for(int j = 0; j < chars.length; j++){
                    char orig = chars[j];
                    for(char c = 'a'; c <= 'z'; c++){
                        if(chars[j] == c) continue;
                        chars[j] = c;
                        String next = String.valueOf(chars);
                        if(next.equals(endWord)) return level + 1;
                        if(dict.remove(next)) q.add(next);
                    }
                    chars[j] = orig;
                }
            }
            level++;
        }
        return 0;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(M^2 \times N)$
- Space Complexity: $O(M \times N)$

---

## 9. Snakes and Ladders
**Đề bài chi tiết:**  
Tìm số bước tung xúc xắc nhỏ nhất để tới ô cuối trên bàn cờ. Bàn cờ đánh số ziczac.

**Phân tích thuật toán:**  
BFS từ ô 1, cộng 1-6. Tính toạ độ từ số thứ tự, di chuyển nếu có thang hoặc rắn.

**Mã nguồn Java:**
```java
class Solution {
    public int snakesAndLadders(int[][] board) {
        int n = board.length;
        Queue<Integer> q = new LinkedList<>();
        boolean[] v = new boolean[n * n + 1];
        q.add(1); v[1] = true;
        int steps = 0;
        
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int curr = q.poll();
                if (curr == n * n) return steps;
                for (int next = curr + 1; next <= Math.min(curr + 6, n * n); next++) {
                    int[] rc = getCoord(next, n);
                    int dest = board[rc[0]][rc[1]] == -1 ? next : board[rc[0]][rc[1]];
                    if (!v[dest]) { v[dest] = true; q.add(dest); }
                }
            }
            steps++;
        }
        return -1;
    }
    private int[] getCoord(int s, int n) {
        int r = n - 1 - (s - 1) / n;
        int c = (s - 1) % n;
        if ((n - 1 - r) % 2 != 0) c = n - 1 - c;
        return new int[]{r, c};
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(n^2)$
- Space Complexity: $O(n^2)$

---

## 10. Number of Provinces
**Đề bài chi tiết:**  
Tìm số lượng thành phần liên thông trong ma trận kề biểu diễn các thành phố.

**Phân tích thuật toán:**  
Dùng DFS hoặc BFS hoặc Union-Find để đếm các thành phần liên thông.

**Mã nguồn Java:**
```java
class Solution {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length, count = 0;
        boolean[] v = new boolean[n];
        for (int i = 0; i < n; i++) {
            if (!v[i]) { dfs(isConnected, v, i); count++; }
        }
        return count;
    }
    private void dfs(int[][] adj, boolean[] v, int i) {
        for (int j = 0; j < adj.length; j++) {
            if (adj[i][j] == 1 && !v[j]) {
                v[j] = true;
                dfs(adj, v, j);
            }
        }
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(n^2)$
- Space Complexity: $O(n)$

---

## 11. Course Schedule
**Đề bài chi tiết:**  
Có tổng cộng `numCourses` khóa học được đánh số từ `0` đến `numCourses - 1`. Một số khóa học có yêu cầu tiên quyết, ví dụ muốn học khóa `A` phải học khóa `B` trước, biểu diễn bằng cặp `[A, B]`. Cho số khóa học và danh sách các điều kiện tiên quyết, trả về `true` nếu bạn có thể hoàn thành tất cả các khóa học, ngược lại trả về `false`.

**Phân tích thuật toán:**  
Bài toán tương đương với việc phát hiện chu trình trong đồ thị có hướng (Cycle Detection in Directed Graph). Có thể giải bằng thuật toán duyệt theo chiều sâu (DFS) với mảng đánh dấu trạng thái (0: chưa thăm, 1: đang thăm, 2: đã thăm), hoặc bằng thuật toán sắp xếp tô-pô (Topological Sort - thuật toán Kahn) dựa trên bậc vào (in-degree). Dưới đây dùng thuật toán Kahn: Tính bậc vào của các đỉnh, đưa các đỉnh có bậc vào bằng 0 vào Queue, duyệt và giảm bậc vào của các đỉnh kề. Nếu số đỉnh lấy ra bằng tổng số khóa học thì không có chu trình.

**Mã nguồn Java:**
```java
class Solution {
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
            if (inDegree[i] == 0) q.add(i);
        }
        
        int count = 0;
        while (!q.isEmpty()) {
            int curr = q.poll();
            count++;
            for (int next : adj.get(curr)) {
                inDegree[next]--;
                if (inDegree[next] == 0) q.add(next);
            }
        }
        return count == numCourses;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V + E)$

---

## 12. Course Schedule II
**Đề bài chi tiết:**  
Tương tự bài 11, nhưng yêu cầu trả về một mảng chứa thứ tự các khóa học cần học để hoàn thành toàn bộ chương trình. Nếu không thể hoàn thành, trả về mảng rỗng.

**Phân tích thuật toán:**  
Sử dụng thuật toán sắp xếp tô-pô (Topological Sort). Có thể dùng thuật toán Kahn sử dụng Queue. Khi lấy một đỉnh ra khỏi Queue, ta thêm nó vào mảng kết quả. Nếu số đỉnh thêm vào bằng `numCourses`, trả về mảng đó, ngược lại trả về mảng rỗng.

**Mã nguồn Java:**
```java
class Solution {
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
            if (inDegree[i] == 0) q.add(i);
        }
        
        int[] order = new int[numCourses];
        int index = 0;
        while (!q.isEmpty()) {
            int curr = q.poll();
            order[index++] = curr;
            for (int next : adj.get(curr)) {
                inDegree[next]--;
                if (inDegree[next] == 0) q.add(next);
            }
        }
        return index == numCourses ? order : new int[0];
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V + E)$

---

## 13. Graph Valid Tree
**Đề bài chi tiết:**  
Cho `n` đỉnh đánh số từ `0` đến `n - 1` và một mảng các cạnh (đồ thị vô hướng). Trả về `true` nếu các cạnh tạo thành một cây hợp lệ, ngược lại trả về `false`.

**Phân tích thuật toán:**  
Một đồ thị vô hướng là một cây khi và chỉ khi nó có đúng `n - 1` cạnh và liên thông toàn phần (không có chu trình). Đầu tiên kiểm tra `edges.length == n - 1`. Sau đó dùng BFS, DFS hoặc Union-Find để kiểm tra tính liên thông. Nếu số đỉnh thăm được qua quá trình duyệt từ 1 đỉnh bằng `n` thì là cây. Dưới đây là cách sử dụng Union-Find để phát hiện chu trình.

**Mã nguồn Java:**
```java
class Solution {
    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false;
        
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        for (int[] edge : edges) {
            int root1 = find(parent, edge[0]);
            int root2 = find(parent, edge[1]);
            if (root1 == root2) return false; // Có chu trình
            parent[root1] = root2;
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
- Time Complexity: $O(V + E \times \alpha(V))$
- Space Complexity: $O(V)$

---

## 14. Word Ladder II
**Đề bài chi tiết:**  
Cho từ bắt đầu (`beginWord`), từ kết thúc (`endWord`) và một danh sách từ vựng (`wordList`). Tìm tất cả các dãy biến đổi ngắn nhất từ `beginWord` đến `endWord`, mỗi lần chỉ đổi 1 ký tự và các từ trung gian phải có trong `wordList`.

**Phân tích thuật toán:**  
Bài này cần 2 bước: 1. Dùng BFS để tìm khoảng cách ngắn nhất từ `beginWord` đến mọi từ và xây dựng đồ thị DAG (Directed Acyclic Graph) để tránh chu trình. 2. Dùng DFS (Backtracking) dựa trên đồ thị này để truy vết từ `beginWord` đến `endWord` và thu thập các đường đi hợp lệ.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        List<List<String>> res = new ArrayList<>();
        if (!dict.contains(endWord)) return res;
        
        Map<String, List<String>> adj = new HashMap<>();
        Map<String, Integer> distance = new HashMap<>();
        dict.add(beginWord);
        bfs(beginWord, endWord, dict, adj, distance);
        dfs(beginWord, endWord, adj, distance, new ArrayList<>(Arrays.asList(beginWord)), res);
        return res;
    }
    
    private void bfs(String start, String end, Set<String> dict, Map<String, List<String>> adj, Map<String, Integer> distance) {
        for (String word : dict) adj.put(word, new ArrayList<>());
        Queue<String> q = new LinkedList<>();
        q.add(start); distance.put(start, 0);
        
        while (!q.isEmpty()) {
            String curr = q.poll();
            int dist = distance.get(curr);
            if (curr.equals(end)) break;
            
            char[] chars = curr.toCharArray();
            for (int i = 0; i < chars.length; i++) {
                char orig = chars[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    chars[i] = c;
                    String next = new String(chars);
                    if (dict.contains(next)) {
                        adj.get(curr).add(next);
                        if (!distance.containsKey(next)) {
                            distance.put(next, dist + 1);
                            q.add(next);
                        }
                    }
                }
                chars[i] = orig;
            }
        }
    }
    
    private void dfs(String curr, String end, Map<String, List<String>> adj, Map<String, Integer> distance, List<String> path, List<List<String>> res) {
        if (curr.equals(end)) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (String next : adj.get(curr)) {
            if (distance.get(next) == distance.get(curr) + 1) {
                path.add(next);
                dfs(next, end, adj, distance, path, res);
                path.remove(path.size() - 1);
            }
        }
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$ cho BFS, cộng với số lượng đường đi trả về, tệ nhất có thể là exponential.
- Space Complexity: $O(N \times K)$ với N là số từ và K là chiều dài của từ.

---

## 15. Evaluate Division
**Đề bài chi tiết:**  
Cho các phép chia dưới dạng `A / B = k` và một mảng truy vấn `C / D = ?`. Trả về kết quả của từng truy vấn. Nếu không tính được trả về `-1.0`.

**Phân tích thuật toán:**  
Đây là bài toán duyệt trên đồ thị có hướng và có trọng số. Phương trình `A / B = k` tương đương với có một cạnh từ `A` đến `B` với trọng số `k` và một cạnh từ `B` đến `A` với trọng số `1.0 / k`. Ứng với mỗi truy vấn `C / D`, dùng DFS hoặc BFS tìm đường đi từ `C` đến `D`, kết quả là tích của các trọng số trên đường đi.

**Mã nguồn Java:**
```java
class Solution {
    class Node {
        String dest;
        double value;
        Node(String d, double v) { dest = d; value = v; }
    }
    
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, List<Node>> adj = new HashMap<>();
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0), v = equations.get(i).get(1);
            adj.putIfAbsent(u, new ArrayList<>());
            adj.putIfAbsent(v, new ArrayList<>());
            adj.get(u).add(new Node(v, values[i]));
            adj.get(v).add(new Node(u, 1.0 / values[i]));
        }
        
        double[] res = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String c = queries.get(i).get(0), d = queries.get(i).get(1);
            if (!adj.containsKey(c) || !adj.containsKey(d)) res[i] = -1.0;
            else res[i] = dfs(adj, c, d, new HashSet<>(), 1.0);
        }
        return res;
    }
    
    private double dfs(Map<String, List<Node>> adj, String curr, String dest, Set<String> visited, double ans) {
        if (curr.equals(dest)) return ans;
        visited.add(curr);
        for (Node next : adj.get(curr)) {
            if (!visited.contains(next.dest)) {
                double res = dfs(adj, next.dest, dest, visited, ans * next.value);
                if (res != -1.0) return res;
            }
        }
        return -1.0;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(Q \times (V + E))$ với Q là số truy vấn.
- Space Complexity: $O(V + E)$

---

## 16. Alien Dictionary
**Đề bài chi tiết:**  
Cho một mảng các chuỗi từ vựng của một ngôn ngữ ngoài hành tinh (đã được sắp xếp theo từ điển của họ). Rút ra thứ tự của các ký tự trong ngôn ngữ đó. Nếu không có thứ tự hợp lệ, trả về chuỗi rỗng.

**Phân tích thuật toán:**  
Xét từng cặp từ liền kề trong danh sách, tìm ký tự khác biệt đầu tiên để tạo một cạnh (ví dụ `t[i] = 'a'`, `t[i+1] = 'b'` thì ta có `a -> b`). Sau đó dùng Topological Sort trên đồ thị các ký tự. Lưu ý kiểm tra trường hợp từ thứ nhất dài hơn nhưng chứa từ thứ hai làm tiền tố (ví dụ "abc" đứng trước "ab") thì thứ tự không hợp lệ.

**Mã nguồn Java:**
```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> adj = new HashMap<>();
        Map<Character, Integer> inDegree = new HashMap<>();
        
        for (String w : words) {
            for (char c : w.toCharArray()) {
                adj.putIfAbsent(c, new HashSet<>());
                inDegree.putIfAbsent(c, 0);
            }
        }
        
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            if (w1.length() > w2.length() && w1.startsWith(w2)) return "";
            for (int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
                if (w1.charAt(j) != w2.charAt(j)) {
                    if (!adj.get(w1.charAt(j)).contains(w2.charAt(j))) {
                        adj.get(w1.charAt(j)).add(w2.charAt(j));
                        inDegree.put(w2.charAt(j), inDegree.get(w2.charAt(j)) + 1);
                    }
                    break;
                }
            }
        }
        
        Queue<Character> q = new LinkedList<>();
        for (char c : inDegree.keySet()) {
            if (inDegree.get(c) == 0) q.add(c);
        }
        
        StringBuilder sb = new StringBuilder();
        while (!q.isEmpty()) {
            char curr = q.poll();
            sb.append(curr);
            for (char next : adj.get(curr)) {
                inDegree.put(next, inDegree.get(next) - 1);
                if (inDegree.get(next) == 0) q.add(next);
            }
        }
        
        return sb.length() == inDegree.size() ? sb.toString() : "";
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(C)$ với $C$ là tổng chiều dài tất cả các từ.
- Space Complexity: $O(1)$ (tối đa 26 ký tự)

---

## 17. Cheapest Flights Within K Stops
**Đề bài chi tiết:**  
Có `n` thành phố kết nối bằng các chuyến bay biểu diễn dưới dạng `(u, v, w)` với trọng số `w` là giá tiền. Tìm giá chuyến bay rẻ nhất từ `src` đến `dst` với tối đa `k` điểm dừng (tức là dùng tối đa `k + 1` cạnh).

**Phân tích thuật toán:**  
Sử dụng thuật toán Bellman-Ford lặp lại đúng `k + 1` lần. Mỗi lần lặp dùng mảng trạng thái cũ của bước trước đó để tránh cập nhật quá số bước quy định.

**Mã nguồn Java:**
```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] prices = new int[n];
        Arrays.fill(prices, Integer.MAX_VALUE);
        prices[src] = 0;
        
        for (int i = 0; i <= k; i++) {
            int[] tempPrices = Arrays.copyOf(prices, n);
            for (int[] f : flights) {
                int u = f[0], v = f[1], w = f[2];
                if (prices[u] != Integer.MAX_VALUE) {
                    tempPrices[v] = Math.min(tempPrices[v], prices[u] + w);
                }
            }
            prices = tempPrices;
        }
        return prices[dst] == Integer.MAX_VALUE ? -1 : prices[dst];
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(E \times k)$
- Space Complexity: $O(V)$

---

## 18. Network Delay Time
**Đề bài chi tiết:**  
Cho mạng các nốt nhận tín hiệu có hướng, một tín hiệu gửi từ nốt `k`. Tìm thời gian tối thiểu để tất cả nốt nhận được tín hiệu. Trả về `-1` nếu không thể.

**Phân tích thuật toán:**  
Đây là bài toán tìm đường đi ngắn nhất từ một đỉnh đến tất cả các đỉnh khác. Sử dụng thuật toán Dijkstra với PriorityQueue (Min-Heap) để tìm và tính thời gian. Kết quả là thời gian lớn nhất trong mảng khoảng cách ngắn nhất, hoặc `-1` nếu còn nốt chưa có tín hiệu.

**Mã nguồn Java:**
```java
class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        Map<Integer, List<int[]>> adj = new HashMap<>();
        for (int[] t : times) {
            adj.computeIfAbsent(t[0], x -> new ArrayList<>()).add(new int[]{t[1], t[2]});
        }
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        pq.add(new int[]{k, 0});
        
        Map<Integer, Integer> dist = new HashMap<>();
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0], d = curr[1];
            if (dist.containsKey(u)) continue;
            
            dist.put(u, d);
            if (adj.containsKey(u)) {
                for (int[] next : adj.get(u)) {
                    if (!dist.containsKey(next[0])) {
                        pq.add(new int[]{next[0], d + next[1]});
                    }
                }
            }
        }
        
        if (dist.size() != n) return -1;
        int max = 0;
        for (int d : dist.values()) max = Math.max(max, d);
        return max;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(E \log V)$
- Space Complexity: $O(V + E)$

---

## 19. Reconstruct Itinerary
**Đề bài chi tiết:**  
Cho danh sách các vé bay `[from, to]`. Tạo một hành trình bắt đầu từ "JFK" qua tất cả các vé. Nếu có nhiều hành trình hợp lệ, chọn hành trình nhỏ nhất theo thứ tự từ vựng.

**Phân tích thuật toán:**  
Bài toán tìm đường đi Eulerian Path. Sử dụng thuật toán Hierholzer: sắp xếp các điểm đến theo thứ tự từ vựng (Lexicographical order) bằng PriorityQueue, sau đó dùng DFS (Post-order traversal) lưu đường đi. Kết quả được lưu theo thứ tự đảo ngược nên khi trả về ta đã có đường đi chuẩn.

**Mã nguồn Java:**
```java
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        Map<String, PriorityQueue<String>> adj = new HashMap<>();
        for (List<String> t : tickets) {
            adj.computeIfAbsent(t.get(0), k -> new PriorityQueue<>()).add(t.get(1));
        }
        
        List<String> route = new LinkedList<>();
        dfs("JFK", adj, route);
        return route;
    }
    
    private void dfs(String airport, Map<String, PriorityQueue<String>> adj, List<String> route) {
        PriorityQueue<String> pq = adj.get(airport);
        while (pq != null && !pq.isEmpty()) {
            dfs(pq.poll(), adj, route);
        }
        route.add(0, airport);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(E \log \frac{E}{V})$ (chi phí sắp xếp các PriorityQueue)
- Space Complexity: $O(V + E)$

---

## 20. Is Graph Bipartite?
**Đề bài chi tiết:**  
Có một đồ thị vô hướng cho bởi danh sách kề. Kiểm tra đồ thị có phải đồ thị hai phía (Bipartite Graph) hay không. (Các đỉnh có thể chia thành hai tập sao cho không có cạnh nối giữa các đỉnh cùng tập).

**Phân tích thuật toán:**  
Sử dụng bài toán tô màu đồ thị bằng BFS hoặc DFS. Khởi tạo mảng màu với các giá trị 0 (chưa tô). Đỉnh chưa tô sẽ được tô màu 1, các đỉnh kề tô màu -1. Nếu trong quá trình duyệt qua kề, một đỉnh đã có màu và màu đó trùng với màu của đỉnh hiện tại, ta có mâu thuẫn -> trả về `false`. Nếu duyệt qua toàn bộ các đỉnh liên thông mà hợp lệ -> trả về `true`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n]; // 0: uncolored, 1: color A, -1: color B
        
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
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V)$

---

## 21. Keys and Rooms
**Đề bài chi tiết:**  
Có `n` căn phòng được đánh số từ `0` đến `n - 1`, và tất cả các phòng đều bị khóa ngoại trừ phòng `0`. Mỗi phòng chứa một danh sách các chìa khóa (mỗi chìa khóa là một số nguyên từ `0` đến `n - 1`), cho phép bạn mở khóa căn phòng tương ứng. Trả về `true` nếu bạn có thể ghé thăm tất cả các phòng, ngược lại trả về `false`.

**Phân tích thuật toán:**  
Bài toán tương đương với việc duyệt đồ thị có hướng để xem từ đỉnh `0` có thể đi tới tất cả các đỉnh khác hay không. Ta có thể dùng BFS (với Queue) hoặc DFS (với Stack/Đệ quy) bắt đầu từ đỉnh `0`. Dùng một mảng boolean (hoặc HashSet) để đánh dấu các phòng đã vào. Cuối cùng kiểm tra số phòng đã vào có bằng `n` hay không.

**Mã nguồn Java:**
```java
class Solution {
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        boolean[] visited = new boolean[n];
        visited[0] = true;
        Queue<Integer> q = new LinkedList<>();
        q.add(0);
        
        int count = 1;
        while (!q.isEmpty()) {
            int curr = q.poll();
            for (int key : rooms.get(curr)) {
                if (!visited[key]) {
                    visited[key] = true;
                    q.add(key);
                    count++;
                }
            }
        }
        return count == n;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$ với $V$ là số phòng, $E$ là tổng số chìa khóa.
- Space Complexity: $O(V)$

---

## 22. Redundant Connection
**Đề bài chi tiết:**  
Cho một đồ thị vô hướng ban đầu là một cây có `n` đỉnh, sau đó bị thêm 1 cạnh thừa làm xuất hiện một chu trình. Mảng `edges` chứa danh sách các cạnh theo thứ tự. Trả về cạnh cần bị loại bỏ (cạnh cuối cùng trong danh sách nếu có nhiều đáp án) để đồ thị quay về là một cây vô hướng.

**Phân tích thuật toán:**  
Sử dụng cấu trúc dữ liệu Disjoint Set (Union-Find) để gom nhóm các đỉnh. Duyệt qua từng cạnh trong mảng `edges`. Đối với mỗi cạnh `[u, v]`, nếu `u` và `v` đã thuộc cùng một tập hợp (có cùng root), thì cạnh `[u, v]` chính là cạnh gây ra chu trình và là kết quả bài toán. Nếu chưa, gộp hai tập hợp chứa `u` và `v` lại với nhau.

**Mã nguồn Java:**
```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        int[] parent = new int[n + 1];
        for (int i = 1; i <= n; i++) parent[i] = i;
        
        for (int[] edge : edges) {
            int rootU = find(parent, edge[0]);
            int rootV = find(parent, edge[1]);
            if (rootU == rootV) return edge;
            parent[rootU] = rootV;
        }
        return new int[0];
    }
    
    private int find(int[] parent, int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent, parent[i]);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E \times \alpha(V))$
- Space Complexity: $O(V)$

---

## 23. Find the Town Judge
**Đề bài chi tiết:**  
Trong một thị trấn có `n` người được đánh số từ `1` đến `n`. Có một lời đồn rằng một người trong số họ là thẩm phán bí mật. Thẩm phán phải thỏa mãn 2 điều kiện: Thẩm phán không tin tưởng ai, và mọi người khác (trừ thẩm phán) đều tin tưởng thẩm phán. Cho mảng `trust` trong đó `trust[i] = [a, b]` nghĩa là người `a` tin tưởng người `b`. Tìm thẩm phán, nếu không tồn tại hoặc không thể xác định, trả về `-1`.

**Phân tích thuật toán:**  
Bài toán tương đương với việc tìm một đỉnh trong đồ thị có hướng sao cho đỉnh đó có out-degree (bậc ra) bằng 0 và in-degree (bậc vào) bằng `n - 1`. Ta chỉ cần một mảng đếm (đóng vai trò in-degree trừ out-degree). Khi `a` tin `b`, điểm của `a` giảm 1, của `b` tăng 1. Sau khi duyệt xong, người có điểm bằng `n - 1` là thẩm phán.

**Mã nguồn Java:**
```java
class Solution {
    public int findJudge(int n, int[][] trust) {
        int[] count = new int[n + 1];
        for (int[] t : trust) {
            count[t[0]]--;
            count[t[1]]++;
        }
        for (int i = 1; i <= n; i++) {
            if (count[i] == n - 1) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$ (duyệt số người và số quan hệ)
- Space Complexity: $O(V)$

---

## 24. Find Eventual Safe States
**Đề bài chi tiết:**  
Cho một đồ thị có hướng (dưới dạng mảng kề). Một đỉnh (node) được gọi là "safe state" nếu mọi đường đi xuất phát từ đỉnh đó cuối cùng đều dẫn đến một đỉnh cuối (terminal node - đỉnh không có cạnh ra). Trả về danh sách tất cả các safe nodes được sắp xếp tăng dần.

**Phân tích thuật toán:**  
Một đỉnh là an toàn nếu không có bất kỳ đường đi nào từ nó dẫn đến một chu trình (Cycle). Sử dụng DFS để phát hiện chu trình. Các trạng thái: 0 (chưa thăm), 1 (đang thăm - nằm trên đường dẫn hiện tại, có thể tạo chu trình), 2 (đã thăm và an toàn). Bất cứ khi nào đi vào một đỉnh đang thăm (1), ta phát hiện có chu trình.

**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        int[] state = new int[n]; // 0: unvisited, 1: visiting/unsafe, 2: safe
        List<Integer> res = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            if (dfs(graph, i, state)) res.add(i);
        }
        return res;
    }
    
    private boolean dfs(int[][] graph, int node, int[] state) {
        if (state[node] > 0) return state[node] == 2;
        
        state[node] = 1; // Mark as visiting
        for (int next : graph[node]) {
            if (!dfs(graph, next, state)) return false;
        }
        
        state[node] = 2; // Mark as safe
        return true;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$
- Space Complexity: $O(V)$

---

## 25. All Paths From Source to Target
**Đề bài chi tiết:**  
Cho một đồ thị có hướng không có chu trình (DAG) gồm `n` đỉnh được đánh số từ `0` đến `n - 1`. Tìm tất cả các đường đi có thể từ đỉnh `0` đến đỉnh `n - 1` và trả về chúng theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**  
Vì đồ thị là DAG (Directed Acyclic Graph) nên không cần lo về chu trình. Ta có thể dùng duyệt sâu (DFS) kết hợp với Backtracking (quay lui). Từ đỉnh nguồn `0`, thử tất cả các cạnh có thể đi tiếp. Ghi nhận đường đi, khi đến đỉnh `n - 1` thì thêm đường đi vào danh sách kết quả, rồi quay lui để thử đường khác.

**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        path.add(0);
        dfs(graph, 0, path, res);
        return res;
    }
    
    private void dfs(int[][] graph, int node, List<Integer> path, List<List<Integer>> res) {
        if (node == graph.length - 1) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int next : graph[node]) {
            path.add(next);
            dfs(graph, next, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(2^V \times V)$ (trường hợp tệ nhất có $2^V$ đường đi)
- Space Complexity: $O(V)$ (độ sâu của đệ quy và mảng lưu path)

---

## 26. Minimum Height Trees
**Đề bài chi tiết:**  
Cho đồ thị vô hướng dạng cây với `n` đỉnh. Nếu chọn một đỉnh bất kỳ làm gốc, cây sẽ có một chiều cao nhất định. Tìm tất cả các gốc sao cho chiều cao của cây là nhỏ nhất (Minimum Height Trees - MHTs) và trả về danh sách các đỉnh đó.

**Phân tích thuật toán:**  
Kết quả (các đỉnh là MHTs) luôn là đỉnh nằm ở trung tâm của đồ thị (1 đỉnh nếu số nút trên đường đi dài nhất lẻ, 2 đỉnh nếu chẵn). Sử dụng thuật toán cắt tỉa lá dần dần (Topological Sort hướng từ lá vào trong). Khởi tạo hàng đợi với tất cả các đỉnh có bậc 1 (lá). Ở mỗi bước, xóa các lá và giảm bậc các đỉnh kề nó, nếu kề trở thành bậc 1 thì đưa vào hàng đợi. Tiếp tục cho đến khi chỉ còn 1 hoặc 2 đỉnh.

**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        if (n == 1) return Collections.singletonList(0);
        
        List<Set<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new HashSet<>());
        for (int[] e : edges) {
            adj.get(e[0]).add(e[1]);
            adj.get(e[1]).add(e[0]);
        }
        
        List<Integer> leaves = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (adj.get(i).size() == 1) leaves.add(i);
        }
        
        while (n > 2) {
            n -= leaves.size();
            List<Integer> newLeaves = new ArrayList<>();
            for (int leaf : leaves) {
                int neighbor = adj.get(leaf).iterator().next();
                adj.get(neighbor).remove(leaf);
                if (adj.get(neighbor).size() == 1) newLeaves.add(neighbor);
            }
            leaves = newLeaves;
        }
        return leaves;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V)$
- Space Complexity: $O(V)$

---

## 27. Regions Cut By Slashes
**Đề bài chi tiết:**  
Trong lưới kích thước `n x n`, mỗi ô có thể là `'/'`, `'\'`, hoặc khoảng trắng `' '`. Các ký tự này chia các ô lưới thành các vùng. Tìm tổng số vùng.

**Phân tích thuật toán:**  
Khó khăn ở đây là một ô lưới bị cắt chéo. Kỹ thuật ở đây là phóng to mỗi ô 1x1 thành một ô 3x3 để giữ nguyên cấu trúc vật lý của nó (tránh tình trạng cạnh chéo chung một điểm bị rò rỉ). Trong ma trận 3x3, ta vẽ các đường nét đứt, các khoảng trống coi như là `0`, vết cắt là `1`. Sau đó dùng bài toán đếm số lượng "đảo" (số lượng vùng `0`) bằng DFS.

**Mã nguồn Java:**
```java
class Solution {
    public int regionsBySlashes(String[] grid) {
        int n = grid.length;
        int[][] expanded = new int[n * 3][n * 3];
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i].charAt(j) == '/') {
                    expanded[i * 3][j * 3 + 2] = 1;
                    expanded[i * 3 + 1][j * 3 + 1] = 1;
                    expanded[i * 3 + 2][j * 3] = 1;
                } else if (grid[i].charAt(j) == '\\') {
                    expanded[i * 3][j * 3] = 1;
                    expanded[i * 3 + 1][j * 3 + 1] = 1;
                    expanded[i * 3 + 2][j * 3 + 2] = 1;
                }
            }
        }
        
        int regions = 0;
        for (int i = 0; i < n * 3; i++) {
            for (int j = 0; j < n * 3; j++) {
                if (expanded[i][j] == 0) {
                    dfs(expanded, i, j);
                    regions++;
                }
            }
        }
        return regions;
    }
    
    private void dfs(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 1) return;
        grid[i][j] = 1; // đánh dấu là vết cắt để không đi lại
        dfs(grid, i + 1, j); dfs(grid, i - 1, j);
        dfs(grid, i, j + 1); dfs(grid, i, j - 1);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N^2)$ (cụ thể là $O(9 \times N^2)$)
- Space Complexity: $O(N^2)$

---

## 28. Shortest Bridge
**Đề bài chi tiết:**  
Cho ma trận nhị phân có đúng 2 "hòn đảo". Một đảo là tập hợp các số `1` nối nhau. Tìm số lượng ô `0` tối thiểu cần đổi thành `1` để hai hòn đảo kết nối với nhau.

**Phân tích thuật toán:**  
1. Tìm hòn đảo đầu tiên bằng DFS, đánh dấu tất cả các ô của đảo thứ nhất thành `2` (hoặc số bất kỳ để phân biệt), đồng thời đẩy tất cả các ô trên đảo thứ nhất vào một hàng đợi (Queue).  
2. Sử dụng Multi-source BFS bắt đầu từ các ô trong Queue để loang (mở rộng dần qua các số `0`) cho tới khi đụng trúng ô `1` của hòn đảo thứ hai. Số bước loang cũng chính là số lượng số `0` cần đổi (độ dài cây cầu).

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
    public int shortestBridge(int[][] grid) {
        int n = grid.length;
        Queue<int[]> q = new LinkedList<>();
        boolean found = false;
        
        for (int i = 0; i < n && !found; i++) {
            for (int j = 0; j < n && !found; j++) {
                if (grid[i][j] == 1) {
                    dfs(grid, i, j, q);
                    found = true;
                }
            }
        }
        
        int steps = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            for (int k = 0; k < size; k++) {
                int[] curr = q.poll();
                for (int[] d : dirs) {
                    int r = curr[0] + d[0], c = curr[1] + d[1];
                    if (r >= 0 && r < n && c >= 0 && c < n) {
                        if (grid[r][c] == 1) return steps;
                        if (grid[r][c] == 0) {
                            grid[r][c] = 2;
                            q.add(new int[]{r, c});
                        }
                    }
                }
            }
            steps++;
        }
        return -1;
    }
    
    private void dfs(int[][] grid, int i, int j, Queue<int[]> q) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid.length || grid[i][j] != 1) return;
        grid[i][j] = 2;
        q.add(new int[]{i, j});
        for (int[] d : dirs) dfs(grid, i + d[0], j + d[1], q);
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(N^2)$

---

## 29. Making A Large Island
**Đề bài chi tiết:**  
Bạn được quyền đổi **tối đa** một số `0` thành số `1` trong ma trận `grid`. Tìm diện tích hòn đảo lớn nhất có thể tạo thành.

**Phân tích thuật toán:**  
Nếu duyệt brute-force thay từng ô `0` rồi chạy BFS/DFS thì mất $O(N^4)$. Tối ưu:  
1. Duyệt toàn bộ ma trận bằng DFS, đánh dấu mỗi đảo bằng một id (bắt đầu từ 2), đồng thời lưu trữ lại diện tích của mỗi id vào một `HashMap`.  
2. Lặp lại ma trận lần nữa, tại mỗi ô `0`, kiểm tra 4 hướng xung quanh, lấy tổng diện tích của các đảo (sử dụng HashSet chứa các id để không bị cộng trùng cùng 1 đảo) cộng thêm 1. Tìm diện tích lớn nhất.

**Mã nguồn Java:**
```java
class Solution {
    int[][] dirs = {{1,0}, {-1,0}, {0,1}, {0,-1}};
    public int largestIsland(int[][] grid) {
        int n = grid.length, id = 2, maxArea = 0;
        Map<Integer, Integer> areaMap = new HashMap<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int area = dfs(grid, i, j, id);
                    areaMap.put(id, area);
                    maxArea = Math.max(maxArea, area);
                    id++;
                }
            }
        }
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    Set<Integer> seenIds = new HashSet<>();
                    int curArea = 1;
                    for (int[] d : dirs) {
                        int r = i + d[0], c = j + d[1];
                        if (r >= 0 && r < n && c >= 0 && c < n && grid[r][c] > 1) {
                            seenIds.add(grid[r][c]);
                        }
                    }
                    for (int seenId : seenIds) curArea += areaMap.get(seenId);
                    maxArea = Math.max(maxArea, curArea);
                }
            }
        }
        return maxArea == 0 ? 1 : maxArea; // Trường hợp lưới toàn số 0
    }
    
    private int dfs(int[][] grid, int i, int j, int id) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid.length || grid[i][j] != 1) return 0;
        grid[i][j] = id;
        int area = 1;
        for (int[] d : dirs) area += dfs(grid, i + d[0], j + d[1], id);
        return area;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(N^2)$

---

## 30. Possible Bipartition
**Đề bài chi tiết:**  
Có `n` người được đánh số từ `1` đến `n`. Danh sách `dislikes[i] = [a, b]` nghĩa là người `a` và người `b` không thích nhau và không thể ở chung nhóm. Kiểm tra xem có thể chia mọi người vào đúng hai nhóm hay không.

**Phân tích thuật toán:**  
Đây chính là bài toán kiểm tra đồ thị hai phía (Is Graph Bipartite) nhưng dưới định dạng khác. Các `dislikes` đóng vai trò như các cạnh nối giữa các đỉnh. Ta xây dựng đồ thị kề từ danh sách `dislikes`. Sau đó dùng DFS hoặc BFS để tô màu mảng (màu 1 và màu -1). Nếu gặp một người kề mà đã có màu trùng với mình thì việc chia nhóm thất bại.

**Mã nguồn Java:**
```java
class Solution {
    public boolean possibleBipartition(int n, int[][] dislikes) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        for (int[] d : dislikes) {
            adj.get(d[0]).add(d[1]);
            adj.get(d[1]).add(d[0]);
        }
        
        int[] colors = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            if (colors[i] == 0 && !dfs(adj, colors, i, 1)) {
                return false;
            }
        }
        return true;
    }
    
    private boolean dfs(List<List<Integer>> adj, int[] colors, int curr, int color) {
        colors[curr] = color;
        for (int next : adj.get(curr)) {
            if (colors[next] == color) return false;
            if (colors[next] == 0 && !dfs(adj, colors, next, -color)) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Time Complexity: $O(V + E)$ với $V = n$ và $E$ là số lượng `dislikes`.
- Space Complexity: $O(V + E)$ để biểu diễn đồ thị.
