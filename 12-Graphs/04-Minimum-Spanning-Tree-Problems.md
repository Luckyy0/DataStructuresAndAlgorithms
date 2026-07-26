# Bài tập Cây khung nhỏ nhất (MST) & Disjoint Set Union (DSU)

## 1. Redundant Connection (LeetCode 684)

### Đề bài chi tiết
Cho một đồ thị vô hướng liên thông dạng cây (với `N` đỉnh và `N-1` cạnh), nhưng có thêm đúng 1 cạnh thừa làm cho đồ thị xuất hiện chu trình. Trả về cạnh xuất hiện cuối cùng trong mảng đầu vào mà nếu loại bỏ nó, đồ thị sẽ trở thành một cây.

### Phân tích thuật toán
Sử dụng DSU. Ta duyệt qua từng cạnh của đầu vào, với mỗi cạnh `[u, v]`, ta tìm tập hợp chứa `u` và `v`. Nếu `u` và `v` đã nằm trong cùng một tập hợp, có nghĩa là việc thêm cạnh này sẽ tạo thành chu trình. Do yêu cầu trả về cạnh cuối cùng (trong mảng), ta chỉ cần trả về cạnh vi phạm này là xong (vì ta duyệt tuần tự từ đầu đến cuối mảng).

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        parent = new int[n + 1];
        
        // Khởi tạo DSU
        for (int i = 1; i <= n; i++) {
            parent[i] = i;
        }
        
        // Xử lý từng cạnh
        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            
            // Nếu đã chung một root, tức là tạo chu trình
            if (find(u) == find(v)) {
                return edge;
            } else {
                union(u, v);
            }
        }
        
        return new int[0];
    }
    
    // Tìm gốc có tối ưu Path Compression
    private int find(int i) {
        if (parent[i] == i) {
            return i;
        }
        return parent[i] = find(parent[i]);
    }
    
    // Gộp hai đỉnh (đơn giản, không dùng rank)
    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N * α(N)) ≈ O(N)` vì chúng ta dùng Path Compression.
- **Không gian**: `O(N)` để lưu trữ mảng `parent`.

---

## 2. Min Cost to Connect All Points (LeetCode 1584)

### Đề bài chi tiết
Cho mảng `points` đại diện cho tọa độ các điểm trên mặt phẳng 2D. Khoảng cách giữa 2 điểm là khoảng cách Manhattan `|x1 - x2| + |y1 - y2|`. Yêu cầu tìm chi phí nhỏ nhất (tổng khoảng cách) để kết nối tất cả các điểm, sao cho có chính xác một đường đi giữa 2 điểm bất kỳ. Đây chính là bài toán tìm Minimum Spanning Tree (MST).

### Phân tích thuật toán
Vì đồ thị này là dạng Dense Graph (mọi điểm đều kết nối được với mọi điểm khác), sử dụng thuật toán Prim sẽ hiệu quả hơn việc tạo mảng `E` cạnh và dùng Kruskal. Ta dùng Priority Queue để lấy ra cạnh có chi phí nhỏ nhất nối từ các điểm đã có trong MST ra các điểm chưa có.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int minCostConnectPoints(int[][] points) {
        int n = points.length;
        // Hàng đợi ưu tiên lưu [cost, node]
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        boolean[] inMST = new boolean[n];
        
        // Bắt đầu từ đỉnh 0
        pq.offer(new int[]{0, 0});
        int totalCost = 0;
        int edgesUsed = 0;
        
        while (edgesUsed < n) {
            int[] curr = pq.poll();
            int cost = curr[0];
            int node = curr[1];
            
            // Nếu đã nằm trong MST rồi thì bỏ qua
            if (inMST[node]) continue;
            
            inMST[node] = true;
            totalCost += cost;
            edgesUsed++;
            
            // Thêm các cạnh kề của node hiện tại vào PQ
            for (int nextNode = 0; nextNode < n; nextNode++) {
                if (!inMST[nextNode]) {
                    int dist = Math.abs(points[node][0] - points[nextNode][0]) + 
                               Math.abs(points[node][1] - points[nextNode][1]);
                    pq.offer(new int[]{dist, nextNode});
                }
            }
        }
        
        return totalCost;
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2 log N)`. Có `O(N^2)` cạnh, mỗi thao tác push vào Priority Queue mất `O(log N)`.
- **Không gian**: `O(N^2)` để lưu các cạnh trong Priority Queue trong trường hợp xấu nhất, và `O(N)` cho mảng đánh dấu `inMST`.

---

## 3. Number of Operations to Make Network Connected (LeetCode 1319)

### Đề bài chi tiết
Có `n` máy tính được đánh số từ `0` đến `n - 1`. Bạn được cho mảng `connections`, trong đó `connections[i] = [a, b]` thể hiện có dây cáp nối máy `a` và `b`. Bạn có thể tháo các dây cáp thừa và nối lại để tất cả các máy tính có thể liên thông với nhau. Trả về số lần di chuyển tối thiểu hoặc `-1` nếu không thể thực hiện được.

### Phân tích thuật toán
Để kết nối `N` máy tính, ta cần ít nhất `N - 1` cáp. Nếu số lượng `connections` nhỏ hơn `N - 1`, ta trả về `-1`.
Sau đó, số lần thao tác chuyển cáp cần thiết chính bằng `Số lượng thành phần liên thông - 1`. Ta dùng DSU để đếm số lượng thành phần liên thông độc lập của đồ thị.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    
    public int makeConnected(int n, int[][] connections) {
        if (connections.length < n - 1) return -1;
        
        parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        int components = n; // Ban đầu mỗi đỉnh là 1 thành phần
        
        for (int[] conn : connections) {
            int rootA = find(conn[0]);
            int rootB = find(conn[1]);
            
            // Nối 2 tập hợp lại, giảm số thành phần liên thông
            if (rootA != rootB) {
                parent[rootA] = rootB;
                components--;
            }
        }
        
        return components - 1;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(V + E) = O(N + C)` với `C` là số connections.
- **Không gian**: `O(N)` để duy trì DSU.

---

## 4. Accounts Merge (LeetCode 721)

### Đề bài chi tiết
Cho mảng danh sách `accounts`, phần tử đầu tiên của mỗi danh sách là tên người dùng, theo sau là các email thuộc về họ. Biết rằng nếu hai tài khoản có chung ít nhất 1 email, thì chúng thuộc về cùng một người. Hãy hợp nhất các tài khoản lại, định dạng đầu ra sắp xếp email theo thứ tự từ điển, tên đứng đầu.

### Phân tích thuật toán
Đây là bài toán gộp các tập hợp (merge sets). Ta dùng DSU trên các email. Mỗi email sẽ trỏ tới một ID (hoặc email gốc). Đầu tiên gộp tất cả các email cùng account. Sau đó duyệt gom các email vào ID gốc của nó. Cuối cùng sort các danh sách email lại.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    Map<String, String> parent;
    
    public List<List<String>> accountsMerge(List<List<String>> accounts) {
        parent = new HashMap<>();
        Map<String, String> owner = new HashMap<>();
        Map<String, TreeSet<String>> disjointSets = new HashMap<>();
        
        // Khởi tạo DSU và gán chủ sở hữu cho từng email
        for (List<String> account : accounts) {
            String name = account.get(0);
            for (int i = 1; i < account.size(); i++) {
                String email = account.get(i);
                parent.put(email, email);
                owner.put(email, name);
            }
        }
        
        // Union các email chung account
        for (List<String> account : accounts) {
            String firstEmail = account.get(1);
            for (int i = 2; i < account.size(); i++) {
                union(firstEmail, account.get(i));
            }
        }
        
        // Nhóm các email theo gốc của chúng
        for (String email : parent.keySet()) {
            String root = find(email);
            disjointSets.computeIfAbsent(root, k -> new TreeSet<>()).add(email);
        }
        
        // Tạo kết quả trả về
        List<List<String>> res = new ArrayList<>();
        for (String root : disjointSets.keySet()) {
            List<String> mergedAccount = new ArrayList<>();
            mergedAccount.add(owner.get(root)); // Tên người dùng
            mergedAccount.addAll(disjointSets.get(root)); // Danh sách email đã sort (TreeSet)
            res.add(mergedAccount);
        }
        
        return res;
    }
    
    private String find(String s) {
        if (parent.get(s).equals(s)) {
            return s;
        }
        String root = find(parent.get(s));
        parent.put(s, root);
        return root;
    }
    
    private void union(String s1, String s2) {
        String root1 = find(s1);
        String root2 = find(s2);
        if (!root1.equals(root2)) {
            parent.put(root1, root2);
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N * K log(N * K))` với `N` là số accounts, `K` là độ dài trung bình account do phải sort email.
- **Không gian**: `O(N * K)` để lưu trữ map DSU và kết quả.

---

## 5. Number of Connected Components in an Undirected Graph (LeetCode 323)

### Đề bài chi tiết
Bạn được cung cấp một số nguyên `n` biểu thị số lượng các nút và một mảng `edges` biểu thị các cạnh trong đồ thị vô hướng. Trả về số lượng các thành phần liên thông trong đồ thị.

### Phân tích thuật toán
Đây là bài tập cơ bản nhất của DSU (Template standard). Khởi tạo số lượng thành phần bằng `n`. Mỗi lần duyệt một cạnh, nếu chúng nối 2 đỉnh thuộc 2 thành phần khác nhau, ta gộp lại và giảm số lượng thành phần đi 1. Kết thúc vòng lặp, ta trả về số lượng thành phần hiện có.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    int components;
    
    public int countComponents(int n, int[][] edges) {
        parent = new int[n];
        components = n;
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
        
        for (int[] edge : edges) {
            union(edge[0], edge[1]);
        }
        
        return components;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // Path compression
    }
    
    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
            components--; // Giảm số component khi hợp nhất thành công
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(V + E)`.
- **Không gian**: `O(V)` cho mảng `parent`.

---

## 6. Evaluate Division (LeetCode 399)

### Đề bài chi tiết
Cho mảng `equations` chứa các cặp biến dạng chuỗi `[A, B]` và mảng `values` biểu thị phép chia `A / B = values[i]`. Bạn được cho thêm mảng `queries`. Tính kết quả mỗi `query`, nếu không thể xác định trả về `-1.0`.

### Phân tích thuật toán
DSU kết hợp với trọng số (Weighted DSU). Mảng `weight` sẽ lưu tỉ lệ giữa node hiện tại và root của nó. Khi thực hiện phép tìm kiếm (`find`), ta cập nhật đường đi (Path compression) và đồng thời nhân dần giá trị tỉ lệ. Khi thực hiện `union`, ta cập nhật root và giá trị chênh lệch (tỉ lệ) tương ứng.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    Map<String, String> parent = new HashMap<>();
    Map<String, Double> weight = new HashMap<>(); // node / root
    
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        // Init
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            if (!parent.containsKey(u)) { parent.put(u, u); weight.put(u, 1.0); }
            if (!parent.containsKey(v)) { parent.put(v, v); weight.put(v, 1.0); }
            union(u, v, values[i]);
        }
        
        double[] ans = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String c = queries.get(i).get(0);
            String d = queries.get(i).get(1);
            
            if (!parent.containsKey(c) || !parent.containsKey(d)) {
                ans[i] = -1.0;
            } else {
                String rootC = find(c);
                String rootD = find(d);
                if (!rootC.equals(rootD)) {
                    ans[i] = -1.0;
                } else {
                    ans[i] = weight.get(c) / weight.get(d);
                }
            }
        }
        return ans;
    }
    
    private String find(String node) {
        if (parent.get(node).equals(node)) {
            return node;
        }
        String root = parent.get(node);
        String finalRoot = find(root);
        
        parent.put(node, finalRoot);
        weight.put(node, weight.get(node) * weight.get(root)); // update weight
        return finalRoot;
    }
    
    private void union(String u, String v, double value) {
        String rootU = find(u);
        String rootV = find(v);
        
        if (!rootU.equals(rootV)) {
            parent.put(rootU, rootV);
            weight.put(rootU, value * weight.get(v) / weight.get(u));
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(E + Q)` với `E` là số phương trình, `Q` là số truy vấn.
- **Không gian**: `O(V)` lưu map DSU (với `V` là số lượng các biến duy nhất).

---

## 7. Regions Cut By Slashes (LeetCode 959)

### Đề bài chi tiết
Một lưới `n x n` chứa các ký tự '/', '\', hoặc khoảng trắng ' '. Các đường gạch chia các ô vuông lưới thành các khu vực riêng biệt. Trả về số lượng khu vực được chia ra.

### Phân tích thuật toán
Ta có thể chia mỗi ô vuông thành 4 phần nhỏ (trên, phải, dưới, trái). Ban đầu lưới có `N * N * 4` phần tử tương ứng với `N * N` ô.
Nếu là không gian trống (' '), 4 phần trong ô đó liên thông với nhau.
Nếu là '/', góc trái-trên và góc phải-dưới bị cắt. Ta liên thông (trên, trái) với nhau và (dưới, phải) với nhau.
Các ô kề nhau sẽ luôn nối phần dưới (ô trên) với phần trên (ô dưới), phần phải (ô trái) với phần trái (ô phải). Số lượng thành phần liên thông của DSU cuối cùng chính là kết quả.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    int count;
    
    public int regionsBySlashes(String[] grid) {
        int n = grid.length;
        int size = n * n * 4;
        parent = new int[size];
        count = size;
        for (int i = 0; i < size; i++) parent[i] = i;
        
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                int index = 4 * (r * n + c);
                char val = grid[r].charAt(c);
                
                // Kết nối nội bộ các phần trong ô
                if (val == '/') {
                    union(index + 0, index + 3);
                    union(index + 1, index + 2);
                } else if (val == '\\') {
                    union(index + 0, index + 1);
                    union(index + 2, index + 3);
                } else {
                    union(index + 0, index + 1);
                    union(index + 1, index + 2);
                    union(index + 2, index + 3);
                }
                
                // Kết nối với các ô xung quanh
                if (r + 1 < n) {
                    union(index + 2, (index + 4 * n) + 0); // Dưới của hiện tại = Trên của ô dưới
                }
                if (c + 1 < n) {
                    union(index + 1, (index + 4) + 3); // Phải của hiện tại = Trái của ô phải
                }
            }
        }
        
        return count;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
    
    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
            count--;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2)`.
- **Không gian**: `O(N^2)`.

---

## 8. Checking Existence of Edge Length Limited Paths (LeetCode 1697)

### Đề bài chi tiết
Bạn được cung cấp một mảng `edgeList` chứa thông tin về đường nối vô hướng và có độ dài. Cung cấp mảng `queries` trong đó mỗi query `[p, q, limit]` yêu cầu xác định xem có đường đi từ `p` đến `q` mà mọi cạnh đều nghiêm ngặt NHỎ HƠN `limit` hay không.

### Phân tích thuật toán
Sort các truy vấn theo `limit` tăng dần (nhớ giữ lại index gốc để phục hồi kết quả).
Sort các cạnh theo `weight` tăng dần.
Sử dụng DSU. Khi xử lý một query có `limit` là `L`, ta sẽ dùng `union` tất cả các cạnh có độ dài `< L`. Sau đó, ta dùng `find(p) == find(q)` để trả lời truy vấn. Vì mảng query đã sort nên ta xử lý offline (tiến trình cạnh chỉ lùi tới, không cần quay lui DSU).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;
    
    public boolean[] distanceLimitedPathsExist(int n, int[][] edgeList, int[][] queries) {
        int m = queries.length;
        boolean[] res = new boolean[m];
        
        // Thêm index gốc vào mảng queries
        int[][] Q = new int[m][4];
        for (int i = 0; i < m; i++) {
            Q[i][0] = queries[i][0];
            Q[i][1] = queries[i][1];
            Q[i][2] = queries[i][2]; // limit
            Q[i][3] = i; // original index
        }
        
        Arrays.sort(Q, (a, b) -> Integer.compare(a[2], b[2]));
        Arrays.sort(edgeList, (a, b) -> Integer.compare(a[2], b[2]));
        
        parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        int edgeIdx = 0;
        for (int i = 0; i < m; i++) {
            int[] q = Q[i];
            int p = q[0], v = q[1], limit = q[2], originalIdx = q[3];
            
            // Union tất cả các cạnh thoả mãn nhỏ hơn limit
            while (edgeIdx < edgeList.length && edgeList[edgeIdx][2] < limit) {
                union(edgeList[edgeIdx][0], edgeList[edgeIdx][1]);
                edgeIdx++;
            }
            
            res[originalIdx] = (find(p) == find(v));
        }
        
        return res;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
    
    private void union(int i, int j) {
        int r1 = find(i);
        int r2 = find(j);
        if (r1 != r2) parent[r1] = r2;
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(E log E + Q log Q + (E + Q) * α(V))`. Phụ thuộc nhiều vào Sort mảng.
- **Không gian**: `O(V + Q)` lưu trữ DSU và mảng queries bản sao.

---

## 9. Similar String Groups (LeetCode 839)

### Đề bài chi tiết
Hai chuỗi tương tự (similar) nếu chúng ta có thể hoán đổi hai chữ cái (ở những vị trí khác nhau) của một chuỗi để tạo ra chuỗi kia. Cũng coi chúng là tương tự nếu chúng bằng nhau. Các nhóm từ tương tự này tạo thành các biểu đồ liên thông. Tìm số nhóm.

### Phân tích thuật toán
DSU để đếm số nhóm liên thông. `N` chuỗi là `N` node. Nếu hai chuỗi có đúng 0 hoặc 2 vị trí khác nhau, chúng được tính là tương tự (tức là có cạnh nối) -> gọi hàm `union`.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    int components;
    
    public int numSimilarGroups(String[] strs) {
        int n = strs.length;
        parent = new int[n];
        components = n;
        for (int i = 0; i < n; i++) parent[i] = i;
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isSimilar(strs[i], strs[j])) {
                    union(i, j);
                }
            }
        }
        return components;
    }
    
    private boolean isSimilar(String s1, String s2) {
        int diff = 0;
        for (int i = 0; i < s1.length(); i++) {
            if (s1.charAt(i) != s2.charAt(i)) {
                diff++;
                if (diff > 2) return false;
            }
        }
        return diff == 0 || diff == 2;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
    
    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
            components--;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2 * L)`. Với `N` là độ dài danh sách từ, `L` là chiều dài chuỗi. `N` ở đây tối đa 300, thời gian đủ nhỏ.
- **Không gian**: `O(N)` cho DSU.

---

## 10. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree (LeetCode 1489)

### Đề bài chi tiết
Trả về tất cả các cạnh "nghiêm trọng" (critical) và "giả nghiêm trọng" (pseudo-critical) của cây khung nhỏ nhất.
- Critical: Nếu thiếu cạnh này, trọng số MST tăng lên hoặc không tạo nổi MST.
- Pseudo-Critical: Cạnh có thể thuộc về ít nhất MỘT MST nhưng không phải MST nào cũng chứa nó (tức là thay thế được).

### Phân tích thuật toán
1. Đầu tiên tìm trọng lượng cây khung nhỏ nhất chuẩn: `baseMST`.
2. Duyệt từng cạnh `e`, bỏ cạnh này đi và tính lại MST. Nếu không nối đủ đồ thị hoặc trọng lượng MST > `baseMST`, đó là **Critical Edge**.
3. Nếu không phải Critical, ta ép buộc phải lấy cạnh `e` ngay từ đầu rồi tính MST (bằng cách lấy `e` trước, rồi duyệt Kruskal cho phần còn lại). Nếu MST tạo ra có trọng số == `baseMST`, thì đây là **Pseudo-Critical Edge**.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for(int i=0; i<n; i++) parent[i] = i;
        }
        public int find(int i) {
            if(parent[i] == i) return i;
            return parent[i] = find(parent[i]);
        }
        public boolean union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if(rootI != rootJ) {
                parent[rootI] = rootJ;
                return true;
            }
            return false;
        }
    }

    public List<List<Integer>> findCriticalAndPseudoCriticalEdges(int n, int[][] edges) {
        int m = edges.length;
        int[][] E = new int[m][4];
        for (int i = 0; i < m; i++) {
            E[i][0] = edges[i][0];
            E[i][1] = edges[i][1];
            E[i][2] = edges[i][2];
            E[i][3] = i; // Lữu giữ index nguyên thủy
        }
        Arrays.sort(E, (a, b) -> Integer.compare(a[2], b[2]));
        
        int baseWeight = getMSTWeight(n, E, -1, -1);
        
        List<Integer> critical = new ArrayList<>();
        List<Integer> pseudo = new ArrayList<>();
        
        for (int i = 0; i < m; i++) {
            // Kiểm tra Critical
            if (getMSTWeight(n, E, i, -1) > baseWeight) {
                critical.add(E[i][3]);
            } 
            // Kiểm tra Pseudo-critical
            else if (getMSTWeight(n, E, -1, i) == baseWeight) {
                pseudo.add(E[i][3]);
            }
        }
        
        List<List<Integer>> res = new ArrayList<>();
        res.add(critical);
        res.add(pseudo);
        return res;
    }
    
    // skipEdge: Bỏ qua cạnh ở index này.
    // forceEdge: Ép buộc nối cạnh ở index này trước.
    private int getMSTWeight(int n, int[][] E, int skipEdge, int forceEdge) {
        DSU dsu = new DSU(n);
        int weight = 0;
        int count = 0;
        
        if (forceEdge != -1) {
            dsu.union(E[forceEdge][0], E[forceEdge][1]);
            weight += E[forceEdge][2];
            count++;
        }
        
        for (int i = 0; i < E.length; i++) {
            if (i == skipEdge) continue;
            if (dsu.union(E[i][0], E[i][1])) {
                weight += E[i][2];
                count++;
            }
        }
        
        // Không thể tạo MST
        if (count != n - 1) return Integer.MAX_VALUE;
        return weight;
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(E^2 log E)`. Có `E` cạnh, mỗi vòng test mất `O(E α(V))` chạy Kruskal. Sắp xếp đầu mất `O(E log E)`.
- **Không gian**: `O(V + E)`.

---

## 11. Sentence Similarity II (LeetCode 737)

### Đề bài chi tiết
Cho hai mảng chuỗi `words1` và `words2` có cùng độ dài, và một danh sách các cặp chuỗi `pairs` đại diện cho các từ tương tự nhau. Tính tương tự có tính chất bắc cầu (nếu A tương tự B, B tương tự C thì A tương tự C). Trả về `true` nếu `words1` và `words2` tương tự nhau, ngược lại trả về `false`. Hai mảng được coi là tương tự nếu mỗi từ ở vị trí `i` của `words1` đều giống hoặc tương tự với từ ở vị trí `i` của `words2`.

### Phân tích thuật toán
Vì các chuỗi là các tên (String), ta sử dụng `Map` để lưu trữ quan hệ cha-con trong DSU hoặc có thể map mỗi chuỗi thành một số nguyên ID để dùng mảng DSU cơ bản. Sau đó, với mỗi cặp trong `pairs`, ta `union` chúng lại với nhau. Cuối cùng, duyệt qua từng vị trí `i`, kiểm tra xem `words1[i]` và `words2[i]` có chung tập hợp (có cùng gốc) hay không thông qua thao tác `find`. Nếu có bất kỳ cặp nào không chung gốc, trả về `false`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    Map<String, String> parent = new HashMap<>();

    public boolean areSentencesSimilarTwo(String[] words1, String[] words2, List<List<String>> pairs) {
        if (words1.length != words2.length) return false;

        for (List<String> pair : pairs) {
            String w1 = pair.get(0);
            String w2 = pair.get(1);
            if (!parent.containsKey(w1)) parent.put(w1, w1);
            if (!parent.containsKey(w2)) parent.put(w2, w2);
            union(w1, w2);
        }

        for (int i = 0; i < words1.length; i++) {
            String w1 = words1[i];
            String w2 = words2[i];
            
            if (w1.equals(w2)) continue;
            if (!parent.containsKey(w1) || !parent.containsKey(w2)) return false;
            if (!find(w1).equals(find(w2))) return false;
        }

        return true;
    }

    private String find(String s) {
        if (parent.get(s).equals(s)) return s;
        String root = find(parent.get(s));
        parent.put(s, root);
        return root;
    }

    private void union(String s1, String s2) {
        String root1 = find(s1);
        String root2 = find(s2);
        if (!root1.equals(root2)) {
            parent.put(root1, root2);
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N + P * α(V))` với `N` là số lượng từ trong câu, `P` là số lượng cặp từ, và `V` là số từ duy nhất. Do thao tác map/chuỗi có thể tốn `O(L)` độ dài từ.
- **Không gian**: `O(V)` để lưu `parent` map.

---

## 12. Graph Valid Tree (LeetCode 261)

### Đề bài chi tiết
Cho `n` đỉnh được đánh số từ `0` đến `n - 1` và một danh sách các cạnh vô hướng `edges`. Viết một hàm kiểm tra xem các cạnh này có tạo thành một cây hợp lệ hay không.

### Phân tích thuật toán
Một đồ thị vô hướng tạo thành một cây khi và chỉ khi:
1. Đồ thị có đúng `n - 1` cạnh.
2. Đồ thị không có chu trình (toàn bộ các đỉnh phải liên thông với nhau tạo thành 1 thành phần duy nhất).
Chúng ta kiểm tra điều kiện (1) trước. Sau đó dùng DSU, mỗi lần nối 2 đỉnh, nếu chúng đã cùng nằm trong 1 tập hợp thì có nghĩa là có chu trình (trả về false). Nếu không có chu trình và xử lý xong, đồ thị chắc chắn là cây hợp lệ (do số cạnh đã bằng `n-1`).

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false;

        parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;

        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            
            if (find(u) == find(v)) {
                return false; // Phát hiện chu trình
            }
            union(u, v);
        }

        return true;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(V + E * α(V))`. Vì `E = V - 1`, thời gian chạy gần như là `O(V)`.
- **Không gian**: `O(V)` lưu mảng DSU.

---

## 13. Number of Provinces (LeetCode 547)

### Đề bài chi tiết
Có `n` thành phố. Một số thành phố kết nối với nhau, một số khác thì không. Nếu `a` kết nối trực tiếp với `b`, và `b` kết nối trực tiếp với `c`, thì `a` được coi là kết nối gián tiếp với `c`. Một tỉnh là một nhóm các thành phố kết nối trực tiếp hoặc gián tiếp với nhau. Bạn được cho một ma trận kề `N x N` tên là `isConnected`, trả về tổng số tỉnh.

### Phân tích thuật toán
Số lượng tỉnh chính là số lượng các thành phần liên thông (Connected Components). Dùng DSU, khởi tạo số lượng thành phần ban đầu là `n`. Duyệt qua nửa trên đường chéo của ma trận kề `isConnected`. Nếu `isConnected[i][j] == 1`, thực hiện `union(i, j)`. Mỗi lần gộp thành công 2 tập hợp, giảm số lượng thành phần đi 1. Cuối cùng, trả về số lượng thành phần liên thông.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    int count;

    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        parent = new int[n];
        count = n;

        for (int i = 0; i < n; i++) parent[i] = i;

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isConnected[i][j] == 1) {
                    union(i, j);
                }
            }
        }

        return count;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
            count--;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2)`. Cần duyệt toàn bộ ma trận kề, các thao tác DSU mất `O(α(N))`.
- **Không gian**: `O(N)` cho mảng `parent`.

---

## 14. Satisfiability of Equality Equations (LeetCode 990)

### Đề bài chi tiết
Cho một mảng các chuỗi `equations` biểu thị mối quan hệ giữa các biến. Mỗi chuỗi có độ dài 4 và dạng "a==b" hoặc "a!=b". Các biến là một chữ cái thường đơn lẻ (từ 'a' đến 'z'). Trả về `true` nếu có thể gán giá trị nguyên cho các biến thỏa mãn mọi phương trình, ngược lại trả về `false`.

### Phân tích thuật toán
Ta có thể dùng DSU với tập đỉnh là các ký tự từ 'a' đến 'z'. Quá trình gồm 2 bước:
1. Duyệt tất cả các phương trình có dạng `==`, gộp 2 biến này vào chung một tập hợp (union).
2. Duyệt qua tất cả các phương trình có dạng `!=`. Kiểm tra nếu 2 biến ở hai bên đã chung một tập hợp (find(a) == find(b)), thì đây là sự mâu thuẫn (vừa bằng nhau vừa khác nhau). Lúc này trả về `false`.
Nếu không phát hiện mâu thuẫn nào sau khi duyệt hết, trả về `true`.

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public boolean equationsPossible(String[] equations) {
        parent = new int[26];
        for (int i = 0; i < 26; i++) {
            parent[i] = i;
        }

        // Bước 1: Xử lý các phép bằng (==)
        for (String eq : equations) {
            if (eq.charAt(1) == '=') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                union(u, v);
            }
        }

        // Bước 2: Kiểm tra các phép khác (!=)
        for (String eq : equations) {
            if (eq.charAt(1) == '!') {
                int u = eq.charAt(0) - 'a';
                int v = eq.charAt(3) - 'a';
                if (find(u) == find(v)) {
                    return false;
                }
            }
        }

        return true;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N)` với `N` là độ dài mảng `equations`. Mỗi phương trình tốn `O(α(26)) = O(1)`.
- **Không gian**: `O(1)` (chỉ tốn mảng kích thước cố định 26).

---

## 15. Find if Path Exists in Graph (LeetCode 1971)

### Đề bài chi tiết
Cho một đồ thị vô hướng gồm `n` đỉnh. Mảng `edges` biểu thị các đường nối vô hướng. Cho trước `source` và `destination`, kiểm tra xem có đường đi hợp lệ nào nối từ `source` tới `destination` không.

### Phân tích thuật toán
Để kiểm tra tính liên thông giữa 2 điểm trong đồ thị tĩnh (không có thay đổi cập nhật đồ thị giữa chừng), DSU là lựa chọn tối ưu. Ta lấy toàn bộ các `edges` và gọi hàm `union(u, v)` để gom các đỉnh vào cùng các tập hợp. Sau cùng, chỉ cần kiểm tra xem `find(source) == find(destination)` không.

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public boolean validPath(int n, int[][] edges, int source, int destination) {
        parent = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }

        for (int[] edge : edges) {
            union(edge[0], edge[1]);
        }

        return find(source) == find(destination);
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(V + E * α(V))` tương đương `O(V + E)`.
- **Không gian**: `O(V)` lưu trữ DSU.

---

## 16. Redundant Connection II (LeetCode 685)

### Đề bài chi tiết
Giống bài Redundant Connection nhưng đồ thị này là ĐỒ THỊ CÓ HƯỚNG dạng cây có gốc (tất cả các đỉnh có in-degree = 1 trừ đỉnh gốc có in-degree = 0). Cũng có một cạnh dư thừa được thêm vào. Yêu cầu tìm cạnh dư thừa đó (nếu có nhiều kết quả, ưu tiên cạnh đứng sau cùng trong mảng đầu vào).

### Phân tích thuật toán
Cạnh dư thừa sẽ gây ra 1 trong 3 trường hợp:
1. Có đỉnh bị in-degree = 2, không có chu trình. (Trả về cạnh thứ hai làm in-degree = 2).
2. Không có đỉnh in-degree = 2, nhưng có chu trình. (Giải giống bài 1).
3. Vừa có đỉnh in-degree = 2, vừa có chu trình. (Cạnh tạo in-degree = 2 nằm trong chu trình chính là cạnh cần loại bỏ. Nếu cạnh kia nằm ngoài chu trình thì loại không giải quyết được chu trình).
Ta sử dụng mảng `inDegree` để tìm ra 2 cạnh hướng vào cùng một đỉnh (gọi là `cand1` và `cand2`). Sau đó dùng DSU chạy trên các cạnh (bỏ qua `cand2`). Nếu tạo ra chu trình, mà có `cand2` thì loại `cand1`. Nếu không có `cand2` thì loại ngay cạnh vừa xét. Nếu không phát hiện chu trình, ta loại `cand2`.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    
    public int[] findRedundantDirectedConnection(int[][] edges) {
        int n = edges.length;
        int[] inDegree = new int[n + 1];
        int[] cand1 = null;
        int[] cand2 = null;
        
        // Tìm 2 cạnh hướng vào cùng 1 đỉnh (in-degree = 2)
        for (int[] edge : edges) {
            int u = edge[0], v = edge[1];
            if (inDegree[v] == 0) {
                inDegree[v] = u;
            } else {
                cand1 = new int[]{inDegree[v], v};
                cand2 = new int[]{u, v};
                break;
            }
        }
        
        parent = new int[n + 1];
        for (int i = 1; i <= n; i++) parent[i] = i;
        
        // DSU
        for (int[] edge : edges) {
            if (cand2 != null && edge[0] == cand2[0] && edge[1] == cand2[1]) {
                continue; // Tạm bỏ qua cand2
            }
            
            int u = edge[0], v = edge[1];
            int rootU = find(u);
            int rootV = find(v);
            
            if (rootU == rootV) {
                // Xảy ra chu trình
                if (cand1 == null) {
                    return edge; // Trường hợp 2
                } else {
                    return cand1; // Trường hợp 3
                }
            }
            parent[rootV] = rootU; // Chú ý: Đồ thị có hướng nên v luôn nối vào u
        }
        
        return cand2; // Trường hợp 1
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // Path compression
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N * α(N)) ≈ O(N)`.
- **Không gian**: `O(N)`.

---

## 17. Largest Component Size by Common Factor (LeetCode 952)

### Đề bài chi tiết
Cho mảng số nguyên `nums` có các giá trị đôi một khác nhau. Hai đỉnh `i` và `j` (giá trị `nums[i]` và `nums[j]`) sẽ có một cạnh vô hướng nối chúng nếu chúng có một ước số chung lớn hơn 1 (ước số chung lớn nhất > 1). Trả về kích thước của thành phần liên thông lớn nhất trong đồ thị.

### Phân tích thuật toán
Sử dụng phân tích thừa số nguyên tố kết hợp DSU. Mỗi số `nums[i]` ta sẽ phân tích ra các thừa số nguyên tố của nó. Dùng DSU gom nhóm số đó với các thừa số nguyên tố của nó (hoặc liên kết chúng với nhau). Sau khi phân tích và gộp nhóm tất cả các số, ta đếm xem mỗi số `nums[i]` quy về chung một root nào nhiều nhất. Kích thước nhóm lớn nhất đó chính là kết quả.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;

    public int largestComponentSize(int[] nums) {
        int maxVal = 0;
        for (int num : nums) {
            maxVal = Math.max(maxVal, num);
        }

        parent = new int[maxVal + 1];
        for (int i = 0; i <= maxVal; i++) parent[i] = i;

        // Phân tích ra thừa số nguyên tố và nối DSU
        for (int num : nums) {
            for (int i = 2; i * i <= num; i++) {
                if (num % i == 0) {
                    union(num, i);
                    union(num, num / i);
                }
            }
        }

        // Đếm kích thước tập hợp
        Map<Integer, Integer> countMap = new HashMap<>();
        int maxGroup = 0;
        for (int num : nums) {
            int root = find(num);
            countMap.put(root, countMap.getOrDefault(root, 0) + 1);
            maxGroup = Math.max(maxGroup, countMap.get(root));
        }

        return maxGroup;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N * √M * α(M))` với `M` là giá trị max trong `nums` và `N` là độ dài mảng. Việc phân tích thừa số nguyên tố mất `O(√M)`.
- **Không gian**: `O(M)` cho mảng DSU.

---

## 18. Swim in Rising Water (LeetCode 778)

### Đề bài chi tiết
Cho một lưới vuông `n x n` chứa các giá trị khác nhau biểu thị độ cao. Bạn có thể bơi qua biên nếu độ cao của cả 2 ô đều không vượt quá thời gian `t`. Bạn cần bơi từ điểm `(0,0)` tới điểm `(n-1, n-1)`. Thời gian `t` tối thiểu là bao nhiêu?

### Phân tích thuật toán
Đây là bài toán Kruskal hoặc BFS + Binary Search hoặc Dijkstra. Giải bằng DSU: Ta sắp xếp tất cả các ô theo độ cao tăng dần. Lần lượt thả các ô này vào và kết nối (union) nó với các ô lân cận (4 hướng) đã được thả vào trước đó. Khi ô `(0, 0)` và `(n-1, n-1)` thuộc chung một tập hợp (có chung root), thì độ cao của ô vừa được thêm vào chính là thời gian `t` tối thiểu cần tìm.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;

    public int swimInWater(int[][] grid) {
        int n = grid.length;
        parent = new int[n * n];
        for (int i = 0; i < n * n; i++) parent[i] = i;

        int[][] cells = new int[n * n][3];
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                cells[r * n + c] = new int[]{r, c, grid[r][c]};
            }
        }

        // Sắp xếp các ô theo độ cao tăng dần
        Arrays.sort(cells, (a, b) -> Integer.compare(a[2], b[2]));

        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        for (int[] cell : cells) {
            int r = cell[0], c = cell[1], h = cell[2];
            int idx = r * n + c;

            // Xem xét 4 hướng
            for (int[] dir : dirs) {
                int nr = r + dir[0];
                int nc = c + dir[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n) {
                    if (grid[nr][nc] <= h) { // Chỉ union các đỉnh thấp hơn hoặc bằng
                        union(idx, nr * n + nc);
                    }
                }
            }

            // Nếu góc bắt đầu và góc kết thúc liên thông
            if (find(0) == find(n * n - 1)) {
                return h;
            }
        }

        return 0;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootI] = rootJ;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2 log N)` chủ yếu dành cho thao tác sắp xếp các ô. Các thao tác DSU mất `O(N^2 α(N^2))`.
- **Không gian**: `O(N^2)` lưu DSU và các ô.

---

## 19. Optimize Water Distribution in a Village (LeetCode 1168)

### Đề bài chi tiết
Có `n` ngôi nhà. Mỗi ngôi nhà có thể tự đào giếng nước với chi phí `wells[i-1]`, hoặc có thể nối ống với các nhà khác thông qua mạng lưới. Mảng `pipes[i] = [house1, house2, cost]` là chi phí nối 2 nhà. Tìm chi phí tối thiểu để cung cấp nước cho tất cả các nhà.

### Phân tích thuật toán
Đây là biến thể bài toán Minimum Spanning Tree (MST). Điểm khác biệt là có lựa chọn "tự đào giếng". Ta biến đổi bài toán: Thêm một "nhà ảo" thứ 0 đại diện cho nguồn nước ngầm. Chi phí để đào giếng cho nhà `i` sẽ tương đương với chi phí tạo 1 đường ống nối nhà thứ 0 với nhà `i`. Cùng với các đường ống đã có sẵn, ta dùng thuật toán Kruskal (với DSU) để tìm MST cho đồ thị gồm `n + 1` đỉnh (từ 0 đến n).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;

    public int minCostToSupplyWater(int n, int[] wells, int[][] pipes) {
        // Tạo mảng danh sách toàn bộ các cạnh
        List<int[]> edges = new ArrayList<>();
        
        // Thêm các cạnh đào giếng (nối đỉnh 0 và đỉnh i)
        for (int i = 0; i < wells.length; i++) {
            edges.add(new int[]{0, i + 1, wells[i]});
        }
        
        // Thêm các cạnh nối ống nước
        for (int[] pipe : pipes) {
            edges.add(pipe);
        }

        // Sắp xếp cạnh theo chi phí
        edges.sort((a, b) -> Integer.compare(a[2], b[2]));

        parent = new int[n + 1];
        for (int i = 0; i <= n; i++) parent[i] = i;

        int totalCost = 0;
        int edgesCount = 0;

        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int cost = edge[2];

            if (find(u) != find(v)) {
                union(u, v);
                totalCost += cost;
                edgesCount++;
                if (edgesCount == n) break; // n cạnh cho n+1 đỉnh
            }
        }

        return totalCost;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int r1 = find(i);
        int r2 = find(j);
        if (r1 != r2) parent[r1] = r2;
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(E log E)` với `E = N + P` (số lượng giếng + số lượng ống). Phụ thuộc bước sort.
- **Không gian**: `O(N + E)` để chứa mảng các cạnh mở rộng và mảng parent.

---

## 20. Count Unreachable Pairs of Nodes in an Undirected Graph (LeetCode 2316)

### Đề bài chi tiết
Cho `n` đỉnh đánh số từ `0` đến `n - 1`, và một danh sách `edges` vô hướng. Yêu cầu đếm xem có bao nhiêu cặp đỉnh không thể đến được với nhau. Hai cặp `[a, b]` và `[b, a]` được tính là 1.

### Phân tích thuật toán
Một cặp đỉnh không thể tới được với nhau nếu chúng nằm ở 2 thành phần liên thông khác nhau.
- Dùng DSU gom nhóm các đỉnh.
- Sau khi gom nhóm, đếm số lượng đỉnh thuộc về mỗi thành phần (component). Dùng mảng lưu `size` của mỗi tập hợp.
- Giả sử đồ thị có k thành phần liên thông, kích thước là `S_1, S_2, ..., S_k`. Ta duyệt qua kích thước của mỗi thành phần. Khi xét đến thành phần có `size = S_i`, số lượng đỉnh chưa được nối đến nó (hoặc còn lại) là `remaining = n - S_i`. Số cặp không nối là `S_i * remaining`. Chú ý sau khi tính, ta trừ đi `S_i` khỏi `remaining` để tính tiếp (tránh đếm trùng hai lần).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;
    int[] size;

    public long countPairs(int n, int[][] edges) {
        parent = new int[n];
        size = new int[n];
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }

        for (int[] edge : edges) {
            union(edge[0], edge[1]);
        }

        long totalPairs = 0;
        long remainingNodes = n;

        // Lưu ý chỉ xử lý các root
        for (int i = 0; i < n; i++) {
            if (parent[i] == i) { // đây là 1 root
                long componentSize = size[i];
                totalPairs += componentSize * (remainingNodes - componentSize);
                remainingNodes -= componentSize;
            }
        }

        return totalPairs;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            // Gom rootJ vào rootI, cập nhật size
            parent[rootJ] = rootI;
            size[rootI] += size[rootJ];
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(V + E * α(V))` chạy DSU và đếm các nhóm.
- **Không gian**: `O(V)` lưu trữ mảng DSU `parent` và mảng `size`.

---

## 21. Lexicographically Smallest Equivalent String (LeetCode 1061)

### Đề bài chi tiết
Bạn được cung cấp 2 chuỗi `s1` và `s2` cùng độ dài và chuỗi `baseStr`. Chúng ta nói `s1[i]` và `s2[i]` là các ký tự tương đương. Các ký tự tương đương tuân theo quy tắc phản xạ, đối xứng và bắc cầu. Trả về chuỗi tương đương nhỏ nhất theo thứ tự từ điển của `baseStr` bằng cách sử dụng các thông tin về ký tự tương đương.

### Phân tích thuật toán
Sử dụng DSU cho các kí tự `a-z`. Trong quá trình gộp (Union), thay vì chọn root một cách ngẫu nhiên hoặc theo hạng (rank), ta **luôn chọn ký tự có thứ tự từ điển nhỏ hơn làm root**. Sau khi xử lý tất cả các ký tự tương đương từ `s1` và `s2`, với mỗi ký tự trong `baseStr`, ta chỉ cần tìm (Find) root của nó (đã là ký tự nhỏ nhất có thể đổi thành) và ghép lại thành chuỗi kết quả.

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public String smallestEquivalentString(String s1, String s2, String baseStr) {
        parent = new int[26];
        for (int i = 0; i < 26; i++) {
            parent[i] = i;
        }

        for (int i = 0; i < s1.length(); i++) {
            union(s1.charAt(i) - 'a', s2.charAt(i) - 'a');
        }

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < baseStr.length(); i++) {
            int root = find(baseStr.charAt(i) - 'a');
            sb.append((char) (root + 'a'));
        }

        return sb.toString();
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            // Luôn chọn root có giá trị (thứ tự từ điển) nhỏ hơn
            if (rootI < rootJ) {
                parent[rootJ] = rootI;
            } else {
                parent[rootI] = rootJ;
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N + M)` với `N` là độ dài chuỗi `s1` và `M` là độ dài chuỗi `baseStr`. Thao tác trên DSU chỉ tốn `O(1)` do kích thước luôn là 26.
- **Không gian**: `O(1)` vì mảng `parent` có kích thước cố định 26.

---

## 22. Path With Minimum Effort (LeetCode 1631)

### Đề bài chi tiết
Bạn là người leo núi chuẩn bị chuyến đi. Cho một ma trận 2D `heights` trong đó `heights[row][col]` là độ cao của ô. Bạn đi từ góc trên-trái `(0, 0)` tới góc dưới-phải `(rows-1, columns-1)`. Độ khó của đường đi ("effort") là chênh lệch độ cao tuyệt đối lớn nhất giữa hai ô kề nhau trên đường đi đó. Trả về nỗ lực tối thiểu yêu cầu.

### Phân tích thuật toán
Bài này có thể dùng DSU + Kruskal (hoặc Dijkstra, hoặc Binary Search + BFS). Với DSU: Ta tạo mảng lưu tất cả các cạnh (sự nối tiếp giữa 2 ô kề nhau 4 chiều) cùng với trọng số (độ chênh lệch độ cao). Sau đó, sắp xếp các cạnh theo trọng số tăng dần. Bắt đầu dùng DSU để nối các ô lại (union), duyệt các cạnh từ nhỏ đến lớn. Ngay khi `(0, 0)` liên thông với `(rows-1, columns-1)`, trọng số của cạnh vừa thêm vào chính là đáp án cần tìm.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;

    public int minimumEffortPath(int[][] heights) {
        int r = heights.length;
        int c = heights[0].length;
        if (r == 1 && c == 1) return 0;

        List<int[]> edges = new ArrayList<>();
        for (int i = 0; i < r; i++) {
            for (int j = 0; j < c; j++) {
                int u = i * c + j;
                if (i > 0) {
                    int v = (i - 1) * c + j;
                    edges.add(new int[]{u, v, Math.abs(heights[i][j] - heights[i-1][j])});
                }
                if (j > 0) {
                    int v = i * c + (j - 1);
                    edges.add(new int[]{u, v, Math.abs(heights[i][j] - heights[i][j-1])});
                }
            }
        }

        Collections.sort(edges, (a, b) -> Integer.compare(a[2], b[2]));

        parent = new int[r * c];
        for (int i = 0; i < r * c; i++) parent[i] = i;

        for (int[] edge : edges) {
            union(edge[0], edge[1]);
            if (find(0) == find(r * c - 1)) {
                return edge[2];
            }
        }
        return 0;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int r1 = find(i);
        int r2 = find(j);
        if (r1 != r2) parent[r1] = r2;
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(E log E)` với `E = R * C * 2` là số cạnh. Quá trình sort mất thời gian nhất.
- **Không gian**: `O(R * C)` lưu mảng DSU và danh sách cạnh.

---

## 23. Making A Large Island (LeetCode 827)

### Đề bài chi tiết
Cho lưới nhị phân `n x n`. Bạn được phép thay đổi nhiều nhất 1 số `0` thành số `1`. Trả về kích thước của hòn đảo lớn nhất (nhóm các số `1` liên thông 4 chiều) trong lưới sau tối đa một lần thay đổi đó.

### Phân tích thuật toán
1. Dùng DSU gom nhóm tất cả các ô số `1` liên thông với nhau và tính kích thước của mỗi hòn đảo. (Cũng có thể dùng BFS/DFS kết hợp gán ID đảo).
2. Lưu kết quả lớn nhất hiện có (trong trường hợp toàn bộ lưới là `1` và không còn `0` nào để thay đổi).
3. Duyệt qua tất cả các ô là `0`. Nếu ta thử đổi ô đó thành `1`, diện tích mới sẽ là `1 + tổng diện tích các hòn đảo láng giềng`. Dùng `Set` các `root` của láng giềng để tránh cộng trùng lặp cùng một hòn đảo nhiều lần. Lấy Max của các diện tích mới tạo được.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;
    int[] size;

    public int largestIsland(int[][] grid) {
        int n = grid.length;
        parent = new int[n * n];
        size = new int[n * n];
        
        for (int i = 0; i < n * n; i++) {
            parent[i] = i;
            size[i] = 1;
        }

        int maxArea = 0;
        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        // Bước 1: Nối các ô 1 lại với nhau
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    int idx = r * n + c;
                    for (int[] dir : dirs) {
                        int nr = r + dir[0];
                        int nc = c + dir[1];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            union(idx, nr * n + nc);
                        }
                    }
                }
            }
        }

        // Lấy kích thước hòn đảo lớn nhất lúc chưa biến đổi
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    maxArea = Math.max(maxArea, size[find(r * n + c)]);
                }
            }
        }

        // Bước 2: Thử đổi 0 thành 1
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    Set<Integer> neighborRoots = new HashSet<>();
                    for (int[] dir : dirs) {
                        int nr = r + dir[0];
                        int nc = c + dir[1];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            neighborRoots.add(find(nr * n + nc));
                        }
                    }
                    int currentArea = 1; // Tính cả ô số 0 đổi thành 1
                    for (int root : neighborRoots) {
                        currentArea += size[root];
                    }
                    maxArea = Math.max(maxArea, currentArea);
                }
            }
        }

        return maxArea == 0 ? 1 : maxArea; // TH lưới trống
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootJ] = rootI;
            size[rootI] += size[rootJ];
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2)` duyệt qua lưới 2 lần, mỗi lần làm `O(α(N^2))`.
- **Không gian**: `O(N^2)` lưu mảng DSU `parent` và `size`.

---

## 24. Minimize Malware Spread (LeetCode 924)

### Đề bài chi tiết
Cho một đồ thị mạng được biểu diễn bằng ma trận kề `graph` và một mảng `initial` chứa các node đã nhiễm mã độc (malware). Virus lan truyền trên toàn bộ thành phần liên thông nếu trong thành phần đó có ít nhất một node nhiễm mã độc. 
Nếu bạn được loại bỏ (chữa lành) đúng 1 node trong mảng `initial`, hãy trả về node nào mà khi loại bỏ sẽ làm giảm tối đa số node nhiễm mã độc trong mạng. (Nếu có nhiều kết quả, ưu tiên node có index nhỏ nhất).

### Phân tích thuật toán
1. Dùng DSU gom nhóm các node lại để tạo thành các thành phần liên thông (component), và duy trì `size` của mỗi component.
2. Đếm số lượng node bị nhiễm (`initial`) thuộc về từng component. (Ta dùng `malwareCount`).
3. Một node được chữa lành sẽ có ý nghĩa (cứu được toàn bộ component của nó) CHỈ KHI nó là node bị nhiễm DUY NHẤT trong component đó. Lúc này số node được cứu = kích thước component. 
4. Duyệt các node trong `initial`, ưu tiên node có thể cứu số lượng lớn nhất, và nếu bằng nhau thì ưu tiên node có giá trị index nhỏ.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;
    int[] size;

    public int minMalwareSpread(int[][] graph, int[] initial) {
        int n = graph.length;
        parent = new int[n];
        size = new int[n];
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (graph[i][j] == 1) {
                    union(i, j);
                }
            }
        }

        int[] malwareCount = new int[n];
        for (int node : initial) {
            malwareCount[find(node)]++;
        }

        Arrays.sort(initial); // Ưu tiên chỉ số nhỏ nếu kết quả hòa
        int maxSaved = -1;
        int resNode = initial[0];

        for (int node : initial) {
            int root = find(node);
            // Chỉ cứu được nếu trong component có ĐÚNG 1 mã độc
            if (malwareCount[root] == 1) {
                if (size[root] > maxSaved) {
                    maxSaved = size[root];
                    resNode = node;
                } else if (size[root] == maxSaved) {
                    resNode = Math.min(resNode, node);
                }
            }
        }

        return resNode;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootJ] = rootI;
            size[rootI] += size[rootJ];
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N^2)` do duyệt ma trận kích thước `N x N`.
- **Không gian**: `O(N)` cho các mảng của DSU.

---

## 25. Bricks Falling When Hit (LeetCode 803)

### Đề bài chi tiết
Cho một lưới `m x n` hiển thị một bức tường (số 1 là gạch, 0 là khoảng trống). Một viên gạch ổn định nếu nó bám trực tiếp trên nóc (hàng `0`) hoặc liên thông 4 chiều với một viên gạch ổn định khác. Có mảng `hits` là tập hợp các lần "bắn phá" làm vỡ viên gạch tại một tọa độ. Yêu cầu trả về mảng số lượng gạch "rơi xuống" (trước đó ổn định, sau khi bị bắn trở nên bất ổn định) ứng với từng phát bắn.

### Phân tích thuật toán
DSU rất tốt để xử lý thao tác thêm (Union), nhưng rất kém khi xóa (Delete/bắn phá). 
Do đó, chúng ta giải ngược bằng hiện tượng **thả gạch vào (Reverse Time)**:
1. Xóa toàn bộ gạch tại `hits` từ đầu.
2. Dùng DSU xây dựng cấu trúc ổn định của phần tường còn sót lại. Nối các viên gạch vào một `Top-Node` ảo (đại diện cho mái).
3. Duyệt ngược `hits` từ cuối về đầu: Ta thêm gạch (tại tọa độ `hit`) vào lưới. Nếu trước khi thêm, cấu trúc chưa chạm Mái, mà sau khi thêm viên gạch kết nối được Mái, toàn bộ Component đó đã được "cứu" khỏi rơi. Số gạch rơi chính là độ tăng `size` của `Top-Node` trừ đi 1 (viên đạn) (và lấy lớn hơn hoặc bằng 0).
4. Lưu ý: Lần lượt ghi kết quả vào mảng rồi đảo ngược.

### Mã nguồn Java
```java
class Solution {
    int[] parent;
    int[] size;
    int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public int[] hitBricks(int[][] grid, int[][] hits) {
        int m = grid.length;
        int n = grid[0].length;
        
        // Đánh dấu các viên bị bắn là 2 (tạm biến mất) nếu ban đầu nó là 1
        for (int[] hit : hits) {
            if (grid[hit[0]][hit[1]] == 1) {
                grid[hit[0]][hit[1]] = 2;
            }
        }
        
        int totalNodes = m * n + 1; // node ảo là tổng số m*n
        int topNode = m * n;
        parent = new int[totalNodes];
        size = new int[totalNodes];
        for (int i = 0; i < totalNodes; i++) {
            parent[i] = i;
            size[i] = 1;
        }
        
        // Nối mảng tường còn lại
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    int idx = i * n + j;
                    if (i == 0) union(idx, topNode); // nối hàng 0 vào nóc
                    
                    if (i > 0 && grid[i - 1][j] == 1) union(idx, (i - 1) * n + j);
                    if (j > 0 && grid[i][j - 1] == 1) union(idx, i * n + (j - 1));
                }
            }
        }
        
        int[] res = new int[hits.length];
        
        // Bắn ngược từ cuối
        for (int i = hits.length - 1; i >= 0; i--) {
            int r = hits[i][0];
            int c = hits[i][1];
            
            if (grid[r][c] != 2) continue; // Ban đầu nó là 0, không có gạch vỡ
            
            int preSize = size[find(topNode)]; // size của Mái
            
            // Đặt gạch lại
            grid[r][c] = 1;
            int idx = r * n + c;
            
            if (r == 0) union(idx, topNode);
            for (int[] dir : dirs) {
                int nr = r + dir[0];
                int nc = c + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                    union(idx, nr * n + nc);
                }
            }
            
            int currentSize = size[find(topNode)];
            res[i] = Math.max(0, currentSize - preSize - 1);
        }
        
        return res;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            // Nóc luôn làm gốc lớn nhất
            if (rootJ == parent.length - 1) {
                parent[rootI] = rootJ;
                size[rootJ] += size[rootI];
            } else {
                parent[rootJ] = rootI;
                size[rootI] += size[rootJ];
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(M * N + H)` với `H` là số lượng `hits`.
- **Không gian**: `O(M * N)` lưu DSU.

---

## 26. Checking Existence of Edge Length Limited Paths II (LeetCode 1724)

### Đề bài chi tiết
Cho đồ thị vô hướng `n` đỉnh. Cung cấp API `distanceLimitedPathsExist(int p, int q, int limit)`. Cần trả lời truy vấn Online: Có đường đi giữa `p` và `q` sao cho TẤT CẢ độ dài cạnh trên đó nghiêm ngặt nhỏ hơn `limit` hay không?

### Phân tích thuật toán
Do các queries không thể được xếp thứ tự Offline như bài phần I, ta cần dùng cấu trúc DSU ghi lại lịch sử các trọng số cạnh thay vì ghi đè lên ngay lập tức.
- Sắp xếp tất cả các cạnh đồ thị theo weight tăng dần.
- DSU không thực hiện Path Compression thông thường (vì làm mất cấu trúc gốc). Nó lưu mảng `weight[]` là trọng số của cạnh gộp 2 node với nhau.
- Khi Union `(u, v)`, ta sử dụng union by rank và ghi nhận giá trị weight tại `weight[v]`.
- Khi Find `(u)` với một `limit`, ta lội ngược lên gốc dọc theo chuỗi cha nếu `weight[parent] < limit`.
- Nếu Find(p, limit) == Find(q, limit) -> Liên thông.

### Mã nguồn Java
```java
import java.util.*;

class DistanceLimitedPathsExist {
    int[] parent;
    int[] weight; // Lưu giá trị cạnh nối đỉnh i với parent[i]
    int[] rank;

    public DistanceLimitedPathsExist(int n, int[][] edgeList) {
        parent = new int[n];
        weight = new int[n];
        rank = new int[n];
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            weight[i] = Integer.MAX_VALUE; // Không có cha thì coi như cực đại
            rank[i] = 1;
        }
        
        // Sắp xếp các cạnh tăng dần
        Arrays.sort(edgeList, (a, b) -> Integer.compare(a[2], b[2]));
        
        for (int[] edge : edgeList) {
            int u = edge[0];
            int v = edge[1];
            int w = edge[2];
            union(u, v, w);
        }
    }
    
    private void union(int u, int v, int w) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU != rootV) {
            // Union by Rank để cây cân bằng, giữ log(N)
            if (rank[rootU] > rank[rootV]) {
                parent[rootV] = rootU;
                weight[rootV] = w;
            } else if (rank[rootU] < rank[rootV]) {
                parent[rootU] = rootV;
                weight[rootU] = w;
            } else {
                parent[rootV] = rootU;
                weight[rootV] = w;
                rank[rootU]++;
            }
        }
    }
    
    // Tìm gốc hiện tại (KHÔNG limit) - Dùng khi Build
    private int find(int i) {
        if (parent[i] == i) return i;
        return find(parent[i]); // KHÔNG Path Compression
    }

    // Tìm gốc hợp lệ với mức limit
    private int findWithLimit(int i, int limit) {
        if (parent[i] == i || weight[i] >= limit) {
            return i;
        }
        return findWithLimit(parent[i], limit);
    }
    
    public boolean query(int p, int q, int limit) {
        return findWithLimit(p, limit) == findWithLimit(q, limit);
    }
}
```

### Độ phức tạp
- **Khởi tạo**: `O(E log E + E log V)`. Sort cạnh tốn `E log E`. Do không path compression mà dùng union by rank, cây có độ sâu tối đa `O(log V)`.
- **Truy vấn (Query)**: `O(log V)` do đi theo con trỏ `parent`.
- **Không gian**: `O(V)`.

---

## 27. Number of Good Paths (LeetCode 2421)

### Đề bài chi tiết
Cho một cây gồm `n` đỉnh, mảng `vals` là giá trị ở mỗi đỉnh và một mảng `edges`. Một đường đi là "đường dẫn tốt" nếu: 
1. Đỉnh đầu và cuối có cùng giá trị.
2. Tất cả các đỉnh trên đường đi có giá trị nhỏ hơn hoặc bằng giá trị đỉnh đầu/cuối đó.
Tính số lượng Good Paths (đường đi độ dài bằng 1 cũng được tính là 1 Good Path).

### Phân tích thuật toán
- Ta xét các đỉnh theo giá trị tăng dần. Các đỉnh lớn hơn sẽ đóng vai trò như "chướng ngại vật".
- Do vậy, nếu ta sắp xếp các đỉnh theo `vals` và dần dần thêm đỉnh (và các cạnh nối với các đỉnh đã được thêm có giá trị nhỏ hơn hoặc bằng), thì ở bất kỳ thời điểm nào, một thành phần liên thông (component) sẽ chứa các đường dẫn hợp lệ.
- Dùng DSU gom nhóm các đỉnh hợp lệ. Ta duy trì đếm số lượng các đỉnh CÓ GIÁ TRỊ TỐI ĐA (bằng `vals` của đỉnh đang xét) bên trong mỗi Component. Nếu gộp thành công, số lượng đường dẫn tốt tạo ra bằng số cách chọn cặp: `count[rootU] * count[rootV]`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;
    int[] count; // Đếm số đỉnh có Max value ở Component đó

    public int numberOfGoodPaths(int[] vals, int[][] edges) {
        int n = vals.length;
        parent = new int[n];
        count = new int[n];
        
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            count[i] = 1;
        }
        
        // Danh sách cạnh (có hướng ảo từ đỉnh nhỏ đến lớn cho dễ Code)
        List<Integer>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();
        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
            adj[edge[1]].add(edge[0]);
        }
        
        // Sắp xếp Index các đỉnh theo vals
        Integer[] nodes = new Integer[n];
        for (int i = 0; i < n; i++) nodes[i] = i;
        Arrays.sort(nodes, (a, b) -> Integer.compare(vals[a], vals[b]));
        
        int goodPaths = n; // Bắt đầu bằng mỗi đỉnh là 1 đường đi tốt (độ dài 1)
        
        for (int node : nodes) {
            int val = vals[node];
            for (int neighbor : adj[node]) {
                if (vals[neighbor] <= val) {
                    int rootU = find(node);
                    int rootV = find(neighbor);
                    
                    if (rootU != rootV) {
                        // Nối V vào U (do U có maxVal >= V)
                        parent[rootV] = rootU;
                        
                        if (vals[rootU] == vals[rootV]) {
                            goodPaths += count[rootU] * count[rootV];
                            count[rootU] += count[rootV];
                        } else {
                            // vals[rootV] < vals[rootU], max bảo toàn, số đếm được giữ của U
                        }
                    }
                }
            }
        }
        
        return goodPaths;
    }

    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(N log N)` để sắp xếp các đỉnh. Duyệt qua mảng và các cạnh tốn `O(N * α(N))`.
- **Không gian**: `O(N)` lưu trữ DSU và Adj-list.

---

## 28. Find All People With Secret (LeetCode 2092)

### Đề bài chi tiết
Bạn được biết số lượng người `n` và danh sách các cuộc họp `meetings`, mỗi cuộc họp `[xi, yi, timei]` biểu thị `xi` và `yi` gặp nhau vào thời điểm `timei`. Có 2 người là `0` và `firstPerson` biết bí mật vào thời điểm 0. Bí mật lan truyền tức thì khi có cuộc gặp. Cuộc họp diễn ra cùng một lúc có thể lan truyền domino bí mật. Trả về list tất cả những người biết bí mật.

### Phân tích thuật toán
- Sort các cuộc họp theo thời gian tăng dần.
- Group các cuộc họp CÙNG MỘT THỜI ĐIỂM lại với nhau.
- Ở mỗi thời điểm, dùng DSU gom các thành viên dự họp với nhau. Những người thuộc nhóm có chứa đỉnh 0 (hoặc những người đã biết bí mật) sẽ biết được bí mật.
- Sau khi duyệt xong nhóm thời điểm đó, quan trọng là: **Ta phải Reset cấu trúc mạng DSU cho những người KHÔNG BIẾT bí mật.** (Chỉ giữ lại union của những người đã biết bí mật, người không biết sẽ huỷ liên kết với nhau, trở về đứng độc lập).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    int[] parent;

    public List<Integer> findAllPeople(int n, int[][] meetings, int firstPerson) {
        parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        union(0, firstPerson); // 0 và firstPerson biết
        
        Arrays.sort(meetings, (a, b) -> Integer.compare(a[2], b[2]));
        
        int i = 0;
        int m = meetings.length;
        
        while (i < m) {
            int j = i;
            while (j < m && meetings[j][2] == meetings[i][2]) {
                j++;
            }
            // Xét chung một mốc thời gian từ i đến j-1
            Set<Integer> currentPeople = new HashSet<>();
            for (int k = i; k < j; k++) {
                union(meetings[k][0], meetings[k][1]);
                currentPeople.add(meetings[k][0]);
                currentPeople.add(meetings[k][1]);
            }
            
            // Nếu sau thời điểm này mà không liên thông với 0 (không biết bí mật) -> Tách ra
            for (int person : currentPeople) {
                if (find(person) != find(0)) {
                    parent[person] = person;
                }
            }
            
            i = j;
        }
        
        List<Integer> res = new ArrayList<>();
        for (int p = 0; p < n; p++) {
            if (find(p) == find(0)) {
                res.add(p);
            }
        }
        return res;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
    
    private void union(int i, int j) {
        int rootI = find(i);
        int rootJ = find(j);
        if (rootI != rootJ) {
            parent[rootJ] = rootI;
        }
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(M log M)` cho sắp xếp `meetings`. Thời gian DSU là `O(M * α(N))`.
- **Không gian**: `O(N)` cho DSU, thêm `O(M)` cho Hashset trong trường hợp xấu.

---

## 29. Process Restricted Friend Requests (LeetCode 2076)

### Đề bài chi tiết
Cho `n` người dùng, mảng `restrictions` chỉ ra 2 người không thể làm bạn. Mảng `requests` biểu thị lời mời kết bạn tuần tự. Hai người được làm bạn (và hợp nhất mạng bạn bè) nếu mạng bạn bè hợp nhất KHÔNG chứa bất kỳ ràng buộc (restriction) nào ở trên. Trả về mảng boolean phản hồi có chấp nhận request hay không.

### Phân tích thuật toán
DSU kết hợp kiểm tra toàn diện:
- Duyệt qua từng request. Gọi root của 2 người trong request.
- Nếu chúng đã ở chung nhóm (cùng root), thì hiển nhiên thành công.
- Nếu khác root, ta duyệt mảng `restrictions`. Đối với mỗi restriction `[u, v]`, gọi root của `u` và root của `v`. Nếu `(rootU == rootA và rootV == rootB)` hoặc ngược lại, thì việc union 2 người đó sẽ vi phạm điều cấm (tạo ra mạng có chứa u và v). Lúc đó ta cấm Union và trả lời `false`.
- Nếu quét hết restriction mà không vi phạm, ta chấp nhận và gộp `union`.

### Mã nguồn Java
```java
class Solution {
    int[] parent;

    public boolean[] friendRequests(int n, int[][] restrictions, int[][] requests) {
        parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        boolean[] res = new boolean[requests.length];
        
        for (int i = 0; i < requests.length; i++) {
            int u = requests[i][0];
            int v = requests[i][1];
            
            int rootU = find(u);
            int rootV = find(v);
            
            if (rootU == rootV) {
                res[i] = true;
                continue;
            }
            
            boolean canBefriend = true;
            for (int[] resrict : restrictions) {
                int rX = find(resrict[0]);
                int rY = find(resrict[1]);
                
                // Nếu liên kết u,v thì tập hợp u sẽ hợp vào v
                // Check xem rX, rY có rơi vào tập U,V không
                if ((rX == rootU && rY == rootV) || (rX == rootV && rY == rootU)) {
                    canBefriend = false;
                    break;
                }
            }
            
            if (canBefriend) {
                res[i] = true;
                parent[rootU] = rootV; // Union
            } else {
                res[i] = false;
            }
        }
        
        return res;
    }
    
    private int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }
}
```

### Độ phức tạp
- **Thời gian**: `O(Q * R * α(N))` với `Q` là số requests và `R` là số restrictions. (R và Q khá nhỏ `<= 1000`, qua ổn thỏa).
- **Không gian**: `O(N)` cho mảng DSU.

---

## 30. Design Graph With Shortest Path Calculator (LeetCode 2642)

### Đề bài chi tiết
Thiết kế một cấu trúc đồ thị có hướng, có trọng số dương.
Cung cấp các API:
1. `Graph(int n, int[][] edges)`: Khởi tạo.
2. `addEdge(int[] edge)`: Thêm mới một cạnh.
3. `shortestPath(int node1, int node2)`: Tìm đường đi ngắn nhất từ node1 tới node2 (hoặc trả về -1 nếu không thể đến được).

### Phân tích thuật toán
Bài này yêu cầu đồ thị tìm đường đi ngắn nhất cập nhật Online. Ta sử dụng Dijkstra's Algorithm trên danh sách kề để tính đường đi. Do trọng số dương, Dijkstra với Priority Queue `O(E log V)` là hiệu quả nhất và phù hợp. Không nhất thiết dùng DSU trong bài Shortest Path vì cấu trúc đồ thị liên tục thay đổi (Thêm vào) và ta không cần Spanning Tree tĩnh, mà cần Shortest Path trực tiếp.

### Mã nguồn Java
```java
import java.util.*;

class Graph {
    List<int[]>[] adj;
    int n;

    public Graph(int n, int[][] edges) {
        this.n = n;
        adj = new ArrayList[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();
        
        for (int[] edge : edges) {
            adj[edge[0]].add(new int[]{edge[1], edge[2]});
        }
    }
    
    public void addEdge(int[] edge) {
        adj[edge[0]].add(new int[]{edge[1], edge[2]});
    }
    
    public int shortestPath(int node1, int node2) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        
        pq.offer(new int[]{node1, 0});
        dist[node1] = 0;
        
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int u = curr[0];
            int d = curr[1];
            
            if (u == node2) return d;
            
            if (d > dist[u]) continue;
            
            for (int[] neighbor : adj[u]) {
                int v = neighbor[0];
                int weight = neighbor[1];
                
                if (dist[u] + weight < dist[v]) {
                    dist[v] = dist[u] + weight;
                    pq.offer(new int[]{v, dist[v]});
                }
            }
        }
        
        return -1;
    }
}
```

### Độ phức tạp
- **Khởi tạo**: `O(V + E)`.
- **addEdge**: `O(1)`.
- **shortestPath**: `O(E log V)` theo thuật toán Dijkstra.
- **Không gian**: `O(V + E)` lưu trữ danh sách kề đồ thị.
