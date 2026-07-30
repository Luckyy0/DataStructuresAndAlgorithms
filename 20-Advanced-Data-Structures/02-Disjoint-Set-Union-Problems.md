# Bài tập Disjoint Set Union (DSU)

## 1. Number of Provinces (LeetCode 547)
**Đề bài chi tiết:**
Có `n` thành phố. Một số thành phố được kết nối trực tiếp với nhau, số khác thì không. Các kết nối mang tính chất bắc cầu: nếu A nối với B, và B nối với C, thì A nối tiếp với C. Một tỉnh (province) là một nhóm các thành phố được kết nối trực tiếp hoặc gián tiếp, và không có thành phố bên ngoài nào nằm chung. Bạn được cho ma trận vuông `isConnected` kích thước `n x n` trong đó `isConnected[i][j] = 1` nếu thành phố `i` và `j` nối nhau trực tiếp, và `0` nếu không. Trả về tổng số tỉnh.

**Phân tích thuật toán:**
Đây là bài toán đếm số thành phần liên thông cơ bản. Ta sẽ khởi tạo DSU với `n` đỉnh, số lượng thành phần ban đầu là `n`. Duyệt ma trận `isConnected`, với mỗi cặp `i, j` nếu bằng `1` thì ta Union chúng. Mỗi lần gộp thành công, số lượng thành phần sẽ trừ đi 1. Cuối cùng, số components còn lại chính là số tỉnh.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for(int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if(parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rootX = find(x);
            int rootY = find(y);
            if(rootX != rootY) {
                parent[rootX] = rootY;
                count--;
            }
        }
    }
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        DSU dsu = new DSU(n);
        for(int i = 0; i < n; i++) {
            for(int j = i + 1; j < n; j++) {
                if(isConnected[i][j] == 1) {
                    dsu.union(i, j);
                }
            }
        }
        return dsu.count;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \cdot \alpha(N))$ do duyệt qua một nửa ma trận $N \times N$.
- Không gian (Space Complexity): $O(N)$ cho mảng DSU `parent`.

---

## 2. Redundant Connection (LeetCode 684)
**Đề bài chi tiết:**
Trong bài toán này, một cây là một đồ thị vô hướng nối liên tiếp không có chu trình. Cung cấp mảng `edges` biểu diễn đồ thị. Nó đã từng là một cây chứa `N` đỉnh (từ `1` đến `N`), sau đó được thêm đúng một cạnh vô hướng để tạo thành một chu trình. Trả về cạnh có thể bị xóa đi để đồ thị trở lại thành cây. Nếu có nhiều đáp án, trả về cạnh xuất hiện cuối cùng trong mảng đầu vào.

**Phân tích thuật toán:**
Dùng DSU để thêm từng cạnh trong đồ thị. Khi ta gặp một cạnh `[u, v]` mà cả `u` và `v` đều đã thuộc cùng một tập hợp (tức là `find(u) == find(v)`), thì việc nối `u` và `v` sẽ tạo thành chu trình. Cạnh đó chính là "Redundant Connection".

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n + 1];
            for (int i = 1; i <= n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx == ry) return false;
            parent[rx] = ry;
            return true;
        }
    }
    public int[] findRedundantConnection(int[][] edges) {
        DSU dsu = new DSU(edges.length);
        for (int[] edge : edges) {
            if (!dsu.union(edge[0], edge[1])) {
                return edge;
            }
        }
        return new int[0];
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(V \cdot \alpha(V))$
- Không gian: $O(V)$

---

## 3. Accounts Merge (LeetCode 721)
**Đề bài chi tiết:**
Được cung cấp danh sách `accounts`, mỗi phần tử `accounts[i]` là danh sách chuỗi, trong đó `accounts[i][0]` là tên (Name), các chuỗi sau là địa chỉ email thuộc tài khoản đó. Nhiệm vụ là gộp các email của cùng một người. Nếu hai tài khoản có chung ít nhất 1 email, chúng cùng thuộc 1 người. Kết quả trả về phải chứa các email được sắp xếp thứ tự bảng chữ cái.

**Phân tích thuật toán:**
Ta sẽ coi mỗi email là một đỉnh của đồ thị. Sử dụng DSU để gộp các email. Do email là String, ta dùng HashMap để ánh xạ email sang một số nguyên ID để dùng DSU. Hoặc có thể Union ID của các tài khoản `accounts[i]`, vì tất cả các email trong cùng `accounts[i]` sẽ thuộc về cùng 1 người.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }
    public List<List<String>> accountsMerge(List<List<String>> accounts) {
        int n = accounts.size();
        DSU dsu = new DSU(n);
        
        // Map Email -> Account ID
        Map<String, Integer> emailToId = new HashMap<>();
        for (int i = 0; i < n; i++) {
            for (int j = 1; j < accounts.get(i).size(); j++) {
                String email = accounts.get(i).get(j);
                if (emailToId.containsKey(email)) {
                    dsu.union(i, emailToId.get(email));
                } else {
                    emailToId.put(email, i);
                }
            }
        }
        
        // Gộp emails lại theo ID tài khoản đại diện
        Map<Integer, TreeSet<String>> mergedAccounts = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int root = dsu.find(i);
            mergedAccounts.putIfAbsent(root, new TreeSet<>());
            for (int j = 1; j < accounts.get(i).size(); j++) {
                mergedAccounts.get(root).add(accounts.get(i).get(j));
            }
        }
        
        List<List<String>> res = new ArrayList<>();
        for (int id : mergedAccounts.keySet()) {
            List<String> account = new ArrayList<>();
            account.add(accounts.get(id).get(0)); // Name
            account.addAll(mergedAccounts.get(id));
            res.add(account);
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(E \log E)$ trong đó $E$ là tổng số lượng email, vì phải sắp xếp email với TreeSet. DSU mất $O(E \cdot \alpha(N))$.
- Không gian: $O(E)$ cho HashMap và DSU.

---

## 4. Number of Operations to Make Network Connected (LeetCode 1319)
**Đề bài chi tiết:**
Có `n` máy tính từ `0` đến `n-1` nối bằng cáp `connections` dạng mạng Ethernet. Một cáp nối trực tiếp 2 máy tính. Cho phép trích rút một cáp thừa để nối 2 máy tính khác. Hỏi số cáp tối thiểu cần di chuyển để tất cả máy tính kết nối với nhau? Nếu không đủ cáp trả về `-1`.

**Phân tích thuật toán:**
Để kết nối `N` đỉnh thành một thành phần liên thông, ta cần tối thiểu `N - 1` cạnh. Nếu tổng số `connections < n - 1`, lập tức trả về `-1`.
Dùng DSU đếm số lượng thành phần liên thông (components). Ban đầu là `n`. Cứ mỗi lần Union thành công thì `components--`. Số thao tác cần thiết chính là `components - 1`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for(int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if(parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if(rx != ry) {
                parent[rx] = ry;
                count--;
            }
        }
    }
    public int makeConnected(int n, int[][] connections) {
        if (connections.length < n - 1) return -1;
        DSU dsu = new DSU(n);
        for (int[] c : connections) {
            dsu.union(c[0], c[1]);
        }
        return dsu.count - 1;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(E \cdot \alpha(N))$
- Không gian: $O(N)$

---

## 5. Most Stones Removed with Same Row or Column (LeetCode 947)
**Đề bài chi tiết:**
Trên lưới 2D, các viên đá được đặt tại hệ tọa độ `(x, y)`. Bạn được phép loại bỏ một viên đá nếu viên đá đó chia sẻ cùng cột hoặc cùng hàng với một viên đá khác chưa bị loại bỏ. Tìm số viên đá lớn nhất có thể bị loại bỏ.

**Phân tích thuật toán:**
Nếu coi mỗi viên đá là 1 đỉnh, 2 viên đá có cạnh chung nếu chúng cùng dòng hoặc cùng cột. Tất cả các viên đá trong cùng một thành phần liên thông sẽ có thể bị gỡ bỏ cho đến khi chỉ còn lại 1 viên đá cuối cùng.
Do đó, Số lượng đá tháo được = Tổng số viên đá - Số lượng thành phần liên thông (Number of Components).
Để Union cột và dòng, ta có thể đánh chỉ số dòng là `x`, chỉ số cột là `y + 10001` để phân biệt.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        Map<Integer, Integer> parent = new HashMap<>();
        int count = 0;
        
        public int find(int x) {
            if (!parent.containsKey(x)) {
                parent.put(x, x);
                count++;
            }
            if (x != parent.get(x)) {
                parent.put(x, find(parent.get(x)));
            }
            return parent.get(x);
        }
        
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent.put(rx, ry);
                count--;
            }
        }
    }
    public int removeStones(int[][] stones) {
        DSU dsu = new DSU();
        for (int[] stone : stones) {
            // stone[0] là x, stone[1] + 10001 là y (tránh trùng id dòng x)
            dsu.union(stone[0], stone[1] + 10001); 
        }
        return stones.length - dsu.count;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \cdot \alpha(N))$ vì duyệt qua $N$ hòn đá.
- Không gian: $O(N)$ cho map DSU.

---

## 6. Longest Consecutive Sequence (LeetCode 128)
**Đề bài chi tiết:**
Cho một mảng số nguyên chưa được sắp xếp. Tìm độ dài dãy các số liên tiếp (consecutive elements sequence) dài nhất. Phải làm với thời gian $O(N)$.

**Phân tích thuật toán:**
Dù thường dùng HashSet, nhưng ta hoàn toàn có thể giải bằng DSU. Với mỗi số `num`, nếu có `num - 1` hoặc `num + 1` tồn tại, ta sẽ Union chúng. DSU cần lưu thêm kích thước `size[]` cho mỗi tập hợp để tìm tập có size lớn nhất. Vì khoảng số có thể âm và rất lớn, dùng HashMap cho mảng `parent` và `size`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        Map<Integer, Integer> parent = new HashMap<>();
        Map<Integer, Integer> size = new HashMap<>();
        int maxSize = 0;
        
        public void add(int x) {
            if (!parent.containsKey(x)) {
                parent.put(x, x);
                size.put(x, 1);
                maxSize = Math.max(maxSize, 1);
            }
        }
        
        public int find(int x) {
            if (parent.get(x) != x) {
                parent.put(x, find(parent.get(x)));
            }
            return parent.get(x);
        }
        
        public void union(int x, int y) {
            if (!parent.containsKey(x) || !parent.containsKey(y)) return;
            int rootX = find(x);
            int rootY = find(y);
            if (rootX != rootY) {
                parent.put(rootX, rootY);
                size.put(rootY, size.get(rootY) + size.get(rootX));
                maxSize = Math.max(maxSize, size.get(rootY));
            }
        }
    }
    public int longestConsecutive(int[] nums) {
        if (nums.length == 0) return 0;
        DSU dsu = new DSU();
        for (int num : nums) {
            if(dsu.parent.containsKey(num)) continue; // avoid duplicates
            dsu.add(num);
            dsu.union(num, num - 1);
            dsu.union(num, num + 1);
        }
        return dsu.maxSize;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N)$
- Không gian: $O(N)$

---

## 7. Evaluate Division (LeetCode 399)
**Đề bài chi tiết:**
Bạn được cấp mảng các phép chia dạng mảng `equations` (`A / B = k`) và các mảng `values`. Cần trả lời các câu hỏi truy vấn `queries` (hỏi giá trị `C / D = ?`).

**Phân tích thuật toán:**
Đây là bài Weighted DSU. Mỗi biến biểu diễn một nút trong đồ thị. Trọng số của cạnh nối từ `x` đến gốc của `x` (ví dụ, `weight[x]`) thể hiện tỷ lệ `x / root`. Khi Find(x), ta nén đường và cập nhật tích lũy tỷ lệ. Khi Union(x, y) với $x/y = val$, ta biết $\frac{x}{rootX} = wX$ và $\frac{y}{rootY} = wY$. Từ đó tìm tỷ lệ giữa $rootX$ và $rootY$ để gộp lại.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        Map<String, String> parent = new HashMap<>();
        Map<String, Double> weight = new HashMap<>();
        
        public void add(String x) {
            if (!parent.containsKey(x)) {
                parent.put(x, x);
                weight.put(x, 1.0);
            }
        }
        
        public String find(String x) {
            if (!x.equals(parent.get(x))) {
                String originParent = parent.get(x);
                String root = find(originParent);
                parent.put(x, root);
                weight.put(x, weight.get(x) * weight.get(originParent));
            }
            return parent.get(x);
        }
        
        public void union(String x, String y, double value) {
            add(x); add(y);
            String rootX = find(x);
            String rootY = find(y);
            if (!rootX.equals(rootY)) {
                parent.put(rootX, rootY);
                weight.put(rootX, value * weight.get(y) / weight.get(x));
            }
        }
    }
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        DSU dsu = new DSU();
        for (int i = 0; i < equations.size(); i++) {
            dsu.union(equations.get(i).get(0), equations.get(i).get(1), values[i]);
        }
        
        double[] res = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String u = queries.get(i).get(0);
            String v = queries.get(i).get(1);
            if (!dsu.parent.containsKey(u) || !dsu.parent.containsKey(v)) {
                res[i] = -1.0;
            } else {
                String rootU = dsu.find(u);
                String rootV = dsu.find(v);
                if (!rootU.equals(rootV)) {
                    res[i] = -1.0;
                } else {
                    res[i] = dsu.weight.get(u) / dsu.weight.get(v);
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N + Q)$ 
- Không gian: $O(N)$ 

---

## 8. Regions Cut By Slashes (LeetCode 959)
**Đề bài chi tiết:**
Một lưới $n \times n$ chứa ký tự `'/'`, `'\'`, hoặc khoảng trắng `' '`. Hãy đếm số khu vực (regions) bị chia cắt trên lưới.

**Phân tích thuật toán:**
Mỗi ô $(r, c)$ trong lưới có thể được chia thành 4 tam giác: trên (0), phải (1), dưới (2), trái (3). Tổng số tam giác là $4 \times N \times N$. Với mỗi ô:
- Dù có gạch chéo hay không, các ô liền kề (trên - dưới, trái - phải) luôn nối với nhau.
- Nếu là `'/'`: Nối (0 và 3), nối (1 và 2).
- Nếu là `'\'`: Nối (0 và 1), nối (2 và 3).
- Nếu `' '`: Nối toàn bộ (0, 1, 2, 3).
Dùng DSU để đếm số vùng liên thông.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                count--;
            }
        }
    }
    public int regionsBySlashes(String[] grid) {
        int n = grid.length;
        DSU dsu = new DSU(n * n * 4);
        
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                int base = 4 * (r * n + c);
                char val = grid[r].charAt(c);
                
                // Nối bên trong cùng 1 ô
                if (val == '/') {
                    dsu.union(base + 0, base + 3);
                    dsu.union(base + 1, base + 2);
                } else if (val == '\\') {
                    dsu.union(base + 0, base + 1);
                    dsu.union(base + 2, base + 3);
                } else {
                    dsu.union(base + 0, base + 1);
                    dsu.union(base + 1, base + 2);
                    dsu.union(base + 2, base + 3);
                }
                
                // Nối với các ô liền kề
                // Ô bên trên
                if (r > 0) dsu.union(base + 0, 4 * ((r - 1) * n + c) + 2);
                // Ô bên trái
                if (c > 0) dsu.union(base + 3, 4 * (r * n + (c - 1)) + 1);
            }
        }
        return dsu.count;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N^2 \cdot \alpha(N^2))$
- Không gian: $O(N^2)$

---

## 9. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree (LeetCode 1489)
**Đề bài chi tiết:**
Cho đồ thị có trọng số. Tìm tất cả các cạnh "Critical" (Nếu xóa nó, trọng lượng MST tăng lên) và "Pseudo-critical" (Có thể thuộc về ít nhất một MST, nhưng không phải mọi MST).

**Phân tích thuật toán:**
Dùng thuật toán Kruskal + DSU.
1. Tính trọng lượng MST chuẩn = `baseWeight`.
2. Critical Edge: Bỏ cạnh đó đi, tính lại MST. Nếu không nối đủ các đỉnh hoặc `MST_weight > baseWeight` thì nó là Critical.
3. Pseudo-Critical Edge: Bắt buộc chọn cạnh đó (nối nó trước tiên), tính phần còn lại. Nếu `MST_weight == baseWeight` và nó không phải Critical, thì nó là Pseudo-critical.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                return true;
            }
            return false;
        }
    }
    
    public List<List<Integer>> findCriticalAndPseudoCriticalEdges(int n, int[][] edges) {
        int m = edges.length;
        int[][] newEdges = new int[m][4];
        for (int i = 0; i < m; i++) {
            newEdges[i] = new int[]{edges[i][0], edges[i][1], edges[i][2], i};
        }
        Arrays.sort(newEdges, (a, b) -> a[2] - b[2]);
        
        int baseWeight = getMST(n, newEdges, -1, -1);
        
        List<Integer> critical = new ArrayList<>();
        List<Integer> pseudo = new ArrayList<>();
        
        for (int i = 0; i < m; i++) {
            if (getMST(n, newEdges, i, -1) > baseWeight) {
                critical.add(newEdges[i][3]);
            } else if (getMST(n, newEdges, -1, i) == baseWeight) {
                pseudo.add(newEdges[i][3]);
            }
        }
        
        return Arrays.asList(critical, pseudo);
    }
    
    private int getMST(int n, int[][] edges, int blockEdge, int preEdge) {
        DSU dsu = new DSU(n);
        int weight = 0;
        int edgesCount = 0;
        
        if (preEdge != -1) {
            weight += edges[preEdge][2];
            dsu.union(edges[preEdge][0], edges[preEdge][1]);
            edgesCount++;
        }
        
        for (int i = 0; i < edges.length; i++) {
            if (i == blockEdge) continue;
            if (dsu.union(edges[i][0], edges[i][1])) {
                weight += edges[i][2];
                edgesCount++;
            }
        }
        
        return edgesCount == n - 1 ? weight : Integer.MAX_VALUE;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(E^2 \log V)$ vì gọi Kruskal cho mỗi cạnh.
- Không gian: $O(V + E)$

---

## 10. Satisfiability of Equality Equations (LeetCode 990)
**Đề bài chi tiết:**
Cho mảng các biểu thức so sánh hai biến chỉ gồm chữ cái viết thường (vd: `"a==b"`, `"b!=c"`). Kiểm tra xem có thể gán giá trị hợp lệ cho các biến mà không bị mâu thuẫn hay không.

**Phân tích thuật toán:**
Dùng DSU.
1. Duyệt qua tất cả các phương trình `"=="`, gọi hàm `Union()` để gộp các biến thành một nhóm.
2. Duyệt qua tất cả các phương trình `"!="`, kiểm tra `Find()` của 2 biến. Nếu chúng nằm chung 1 nhóm, chứng tỏ có mâu thuẫn -> Trả về `false`.
3. Nếu duyệt hết không lỗi, trả về `true`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent = new int[26];
        public DSU() {
            for (int i = 0; i < 26; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }
    public boolean equationsPossible(String[] equations) {
        DSU dsu = new DSU();
        for (String eq : equations) {
            if (eq.charAt(1) == '=') {
                dsu.union(eq.charAt(0) - 'a', eq.charAt(3) - 'a');
            }
        }
        
        for (String eq : equations) {
            if (eq.charAt(1) == '!') {
                if (dsu.find(eq.charAt(0) - 'a') == dsu.find(eq.charAt(3) - 'a')) {
                    return false;
                }
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Thời gian: $O(N \cdot \alpha(N))$, ở đây là 26 kí tự nên là $O(N)$
- Không gian: $O(1)$ (Mảng 26 phần tử).

---

## 11. Sentence Similarity II (LeetCode 737)
**Đề bài chi tiết:**
Cho hai mảng chuỗi `words1` và `words2` (đại diện cho hai câu) có cùng độ dài, và một mảng các cặp từ đồng nghĩa `pairs`. Các cặp từ đồng nghĩa có tính chất bắc cầu, nghĩa là nếu "A" đồng nghĩa với "B" và "B" đồng nghĩa với "C", thì "A" đồng nghĩa với "C". Hãy kiểm tra xem hai câu `words1` và `words2` có tương đồng với nhau hay không. Hai câu được coi là tương đồng nếu `words1[i]` và `words2[i]` là giống nhau hoặc đồng nghĩa với nhau với mọi vị trí `i`.

**Phân tích thuật toán:**
Sử dụng DSU để quản lý các nhóm từ đồng nghĩa. Do các từ là chuỗi (`String`), ta có thể dùng `Map<String, String>` làm mảng `parent` trong DSU để lưu gốc của từng từ. Duyệt qua mảng `pairs` và gọi hàm `Union()` để gộp các từ. Sau đó duyệt qua từng cặp từ ở cùng vị trí `i` trong `words1` và `words2`, nếu chúng không giống nhau và `Find(words1[i]) != Find(words2[i])` thì hai câu không tương đồng (trả về `false`). Nếu mọi cặp đều hợp lệ thì trả về `true`. Chú ý trường hợp độ dài 2 mảng không bằng nhau thì trả về `false` ngay.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        Map<String, String> parent = new HashMap<>();
        
        public String find(String x) {
            if (!parent.containsKey(x)) {
                parent.put(x, x);
            }
            if (!x.equals(parent.get(x))) {
                parent.put(x, find(parent.get(x)));
            }
            return parent.get(x);
        }
        
        public void union(String x, String y) {
            String rootX = find(x);
            String rootY = find(y);
            if (!rootX.equals(rootY)) {
                parent.put(rootX, rootY);
            }
        }
    }
    
    public boolean areSentencesSimilarTwo(String[] words1, String[] words2, List<List<String>> pairs) {
        if (words1.length != words2.length) return false;
        
        DSU dsu = new DSU();
        for (List<String> pair : pairs) {
            dsu.union(pair.get(0), pair.get(1));
        }
        
        for (int i = 0; i < words1.length; i++) {
            String w1 = words1[i];
            String w2 = words2[i];
            if (w1.equals(w2)) continue;
            if (!dsu.find(w1).equals(dsu.find(w2))) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \cdot \alpha(N) + P \cdot \alpha(P))$, với $N$ là độ dài câu và $P$ là số lượng cặp từ. Chạy các thao tác băm chuỗi.
- Không gian (Space Complexity): $O(P)$ để lưu Hash Map của DSU.

---

## 12. Friend Circles (LeetCode 547)
*(Tên khác của bài Number of Provinces)*
**Đề bài chi tiết:**
Có `n` học sinh trong một lớp. Một số người là bạn bè trực tiếp, một số người thì không. Tình bạn có tính bắc cầu: nếu A là bạn của B và B là bạn của C, thì A là bạn (gián tiếp) của C. Một Friend Circle (Vòng tròn bạn bè) là một nhóm các học sinh là bạn trực tiếp hoặc gián tiếp của nhau. Cho một ma trận vuông `M` kích thước `n x n`, với `M[i][j] = 1` nếu học sinh `i` và `j` là bạn trực tiếp, và `0` nếu không. Trả về tổng số vòng tròn bạn bè.

**Phân tích thuật toán:**
Đề bài tương tự bài "Number of Provinces". Ta sử dụng cấu trúc DSU cơ bản với `n` phần tử, ban đầu số lượng vòng tròn là `n`. Duyệt qua nửa trên của ma trận `M` (vì tính đối xứng của bạn bè), nếu `M[i][j] == 1`, tiến hành gộp (`Union`) hai học sinh vào cùng một nhóm. Nếu hai người chưa cùng nhóm thì gộp lại và giảm số lượng vòng tròn đi 1. Cuối cùng, số lượng vòng tròn còn lại chính là đáp án.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for(int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if(parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rootX = find(x);
            int rootY = find(y);
            if(rootX != rootY) {
                parent[rootX] = rootY;
                count--;
            }
        }
    }
    public int findCircleNum(int[][] M) {
        int n = M.length;
        DSU dsu = new DSU(n);
        for(int i = 0; i < n; i++) {
            for(int j = i + 1; j < n; j++) {
                if(M[i][j] == 1) {
                    dsu.union(i, j);
                }
            }
        }
        return dsu.count;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \cdot \alpha(N))$ vì duyệt ma trận.
- Không gian (Space Complexity): $O(N)$ dùng lưu mảng `parent` trong DSU.

---

## 13. Largest Component Size by Common Factor (LeetCode 952)
**Đề bài chi tiết:**
Cho một mảng các số nguyên dương duy nhất `nums`. Hai số được coi là có cạnh nối nhau nếu chúng có chung một ước số nguyên tố (prime factor) chung lớn hơn 1. Tìm kích thước của thành phần liên thông (component) lớn nhất trong đồ thị.

**Phân tích thuật toán:**
Việc xét từng cặp số $O(N^2)$ tính GCD sẽ bị quá thời gian (TLE). Thay vào đó, ta sử dụng DSU để gộp mỗi số nguyên trong `nums` với tất cả các thừa số nguyên tố của nó. Gốc của các thừa số nguyên tố này sẽ được hợp nhất lại với nhau. Sau quá trình này, các số trong `nums` có chung thừa số nguyên tố sẽ thuộc cùng một tập hợp. Cuối cùng, ta đếm số lượng các số trong mảng `nums` chia sẻ cùng một gốc trong DSU và tìm nhóm lớn nhất.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) parent[rx] = ry;
        }
    }
    
    public int largestComponentSize(int[] nums) {
        int maxVal = 0;
        for (int num : nums) maxVal = Math.max(maxVal, num);
        
        DSU dsu = new DSU(maxVal + 1);
        for (int num : nums) {
            int n = num;
            for (int i = 2; i * i <= n; i++) {
                if (n % i == 0) {
                    dsu.union(num, i);
                    while (n % i == 0) n /= i;
                }
            }
            if (n > 1) {
                dsu.union(num, n);
            }
        }
        
        Map<Integer, Integer> count = new HashMap<>();
        int res = 0;
        for (int num : nums) {
            int root = dsu.find(num);
            count.put(root, count.getOrDefault(root, 0) + 1);
            res = Math.max(res, count.get(root));
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \sqrt{M} \cdot \alpha(M))$ với $N$ là số lượng phần tử của mảng `nums` và $M$ là giá trị cực đại trong `nums`. Phân tích thừa số nguyên tố tốn tối đa $O(\sqrt{M})$ cho mỗi số.
- Không gian (Space Complexity): $O(M)$ cho mảng DSU `parent`.

---

## 14. Minimize Malware Spread (LeetCode 924)
**Đề bài chi tiết:**
Cho một mạng lưới máy tính được biểu diễn bằng đồ thị ma trận vuông `graph` (`graph[i][j] = 1` nếu có kết nối trực tiếp giữa máy `i` và máy `j`). Một mảng `initial` biểu diễn danh sách các máy ban đầu bị nhiễm mã độc (malware). Bất kỳ máy nào nằm trong cùng một thành phần liên thông với một máy nhiễm mã độc cũng sẽ bị nhiễm. Bệnh lây lan tới khi dừng lại hoàn toàn. 
Ta có thể chọn đúng một máy tính từ `initial` để gỡ mã độc ban đầu. Hỏi nên gỡ mã độc từ máy nào để số máy tính bị nhiễm cuối cùng là ít nhất? Nếu có nhiều máy tính như vậy, hãy trả về máy có chỉ số `index` nhỏ nhất.

**Phân tích thuật toán:**
Dùng DSU để gom các thành phần liên thông của toàn bộ mạng lưới. Đếm kích thước mỗi tập (thành phần liên thông) và số lượng đỉnh nhiễm bệnh ban đầu (`initial`) trong tập đó. 
- Nếu một tập chỉ chứa đúng 1 đỉnh nhiễm bệnh, việc "chữa" cho đỉnh đó sẽ cứu toàn bộ kích thước của tập này khỏi nhiễm bệnh. 
- Nếu tập có 2 đỉnh nhiễm trở lên, việc chữa 1 đỉnh không thể cứu cả tập vì các máy nhiễm khác vẫn sẽ lây lan qua tập.
Vậy ta ưu tiên tìm đỉnh trong `initial` độc chiếm một thành phần liên thông có kích thước (size) lớn nhất. Nếu không có đỉnh nào đứng riêng một thành phần, ta trả về đỉnh có chỉ số nhỏ nhất trong `initial`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int[] size;
        public DSU(int n) {
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                size[ry] += size[rx];
            }
        }
    }
    
    public int minMalwareSpread(int[][] graph, int[] initial) {
        int n = graph.length;
        DSU dsu = new DSU(n);
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (graph[i][j] == 1) {
                    dsu.union(i, j);
                }
            }
        }
        
        int[] malwareCount = new int[n];
        for (int node : initial) {
            malwareCount[dsu.find(node)]++;
        }
        
        Arrays.sort(initial);
        int ans = initial[0];
        int maxSaved = -1;
        
        for (int node : initial) {
            int root = dsu.find(node);
            if (malwareCount[root] == 1) {
                int saved = dsu.size[root];
                if (saved > maxSaved) {
                    maxSaved = saved;
                    ans = node;
                }
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \cdot \alpha(N))$ để duyệt ma trận `graph` và gộp DSU. $O(K \log K)$ để sort mảng `initial` (với $K$ là độ dài mảng này). Tổng thời gian $O(N^2)$.
- Không gian (Space Complexity): $O(N)$ lưu DSU parent và size, `malwareCount`.

---

## 15. Swim in Rising Water (LeetCode 778)
**Đề bài chi tiết:**
Cho ma trận `grid` kích thước `N x N` biểu diễn độ cao của các ô. Mức nước bắt đầu dâng lên theo thời gian `t`. Tại thời điểm `t`, mực nước trên mọi nơi có độ sâu là `t`. Bạn có thể bơi từ ô vuông này sang ô vuông khác kề cạnh nó nếu và chỉ nếu độ cao của cả 2 ô tối đa là `t`. 
Tìm thời gian `t` nhỏ nhất sao cho bạn có thể bơi từ ô đỉnh góc trên cùng bên trái `(0, 0)` tới ô góc dưới cùng bên phải `(N-1, N-1)`.

**Phân tích thuật toán:**
Dùng ý tưởng của thuật toán Kruskal kết hợp Offline DSU. Mỗi ô tương ứng với một độ cao duy nhất `[0, N*N - 1]`. Tại thời điểm `t`, các ô có độ cao $\le t$ bắt đầu được "kích hoạt". Ta sẽ dần dần thêm các ô theo thứ tự độ cao tăng dần. Mỗi khi thêm một ô, ta thử nối nó với các ô kề cạnh với điều kiện các ô kề đã được "kích hoạt" trước đó (tức độ cao của chúng cũng $\le t$). Sau mỗi lần Union, ta kiểm tra xem `(0, 0)` và `(N-1, N-1)` đã nằm chung một thành phần liên thông chưa. Nếu rồi, giá trị `t` hiện tại chính là kết quả nhỏ nhất (vì ta đang duyệt `t` tăng dần).

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
        public boolean isConnected(int x, int y) {
            return find(x) == find(y);
        }
    }
    
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        DSU dsu = new DSU(n * n);
        
        // Lưu trữ tọa độ theo độ cao (độ cao luôn phân biệt từ 0 đến n*n - 1)
        int[] time = new int[n * n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                time[grid[i][j]] = i * n + j;
            }
        }
        
        int[] dirs = {-1, 0, 1, 0, -1};
        for (int t = 0; t < n * n; t++) {
            int r = time[t] / n;
            int c = time[t] % n;
            
            // Thử nối với 4 ô xung quanh
            for (int i = 0; i < 4; i++) {
                int nr = r + dirs[i];
                int nc = c + dirs[i+1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] <= t) {
                    dsu.union(time[t], nr * n + nc);
                }
            }
            // Kiểm tra điểm xuất phát và đích
            if (dsu.isConnected(0, n * n - 1)) {
                return t;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \cdot \alpha(N^2))$
- Không gian (Space Complexity): $O(N^2)$ cho DSU.

---

## 16. Graph Valid Tree (LeetCode 261)
**Đề bài chi tiết:**
Cho `n` đỉnh được đánh số từ `0` đến `n - 1` và danh sách các cạnh vô hướng `edges`. Hãy viết hàm kiểm tra xem các cạnh này có tạo thành một cây hợp lệ (valid tree) hay không. Một đồ thị được gọi là cây nếu nó hoàn toàn liên thông và không chứa bất kỳ chu trình nào.

**Phân tích thuật toán:**
Để là một cây hợp lệ:
1. Số cạnh bắt buộc phải bằng $n - 1$. Nếu không phải, trả về `false` ngay.
2. Không có chu trình.
3. Tất cả các đỉnh phải liên thông.

Việc kiểm tra 2 và 3 có thể làm một lúc bằng DSU. Khởi tạo `count = n`. Nếu gặp một cạnh `[u, v]` mà chúng đã thuộc cùng một tập hợp (nghĩa là `Find(u) == Find(v)`), thì đồ thị có chu trình, trả về `false`. Ngược lại tiến hành gộp lại và giảm `count`. Cuối cùng kiểm tra `count == 1` thì nó là một cây liên thông và không chu trình.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx == ry) return false; // Cycle detected
            parent[rx] = ry;
            count--;
            return true;
        }
    }
    
    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false;
        
        DSU dsu = new DSU(n);
        for (int[] edge : edges) {
            if (!dsu.union(edge[0], edge[1])) {
                return false;
            }
        }
        
        return dsu.count == 1;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(V + E \cdot \alpha(V))$ trong đó $V=n$ và $E=n-1$.
- Không gian (Space Complexity): $O(V)$ cho DSU.

---

## 17. Number of Islands II (LeetCode 305)
**Đề bài chi tiết:**
Cho kích thước bảng 2D là `m x n` ban đầu toàn bộ là nước (0). Bạn nhận được danh sách các truy vấn `positions`. Tại mỗi truy vấn `[r, c]`, người ta đổ đất vào tọa độ `(r, c)` biến nó thành đất liền (1). Trả về một mảng chứa số lượng đảo (island) sau mỗi truy vấn. Một đảo là một vùng đất liên thông theo 4 hướng.

**Phân tích thuật toán:**
Do ma trận thay đổi động theo từng bước, đây là dạng bài toán lý tưởng cho DSU (Dynamic Connectivity). Ta duy trì biến `count` lưu số đảo hiện tại, ban đầu bằng 0. Mỗi khi có một ô đất mới:
1. Nếu ô đó đã là đất (duplicate query), bỏ qua. Nếu chưa, ta tăng `count++`, đánh dấu nó là đất liền, và nó đứng thành 1 component.
2. Kiểm tra 4 ô kề nó. Nếu kề là đất, ta Union (gộp) chúng lại. Mỗi lần Union thành công (tức là 2 phần chưa cùng một tập), số lượng đảo giảm đi 1 (`count--`).
3. Lưu số `count` vào danh sách kết quả sau mỗi bước.
Chỉ số DSU có thể mã hóa thành mảng 1 chiều $r \times n + c$.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int size) {
            parent = new int[size];
            Arrays.fill(parent, -1);
            count = 0;
        }
        public void setParent(int x) {
            if (parent[x] == -1) {
                parent[x] = x;
                count++;
            }
        }
        public boolean isValid(int x) {
            return parent[x] != -1;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                count--;
            }
        }
    }
    
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        List<Integer> result = new ArrayList<>();
        DSU dsu = new DSU(m * n);
        int[] dirs = {-1, 0, 1, 0, -1};
        
        for (int[] pos : positions) {
            int r = pos[0];
            int c = pos[1];
            int id = r * n + c;
            
            if (!dsu.isValid(id)) {
                dsu.setParent(id);
                for (int i = 0; i < 4; i++) {
                    int nr = r + dirs[i];
                    int nc = c + dirs[i+1];
                    int nid = nr * n + nc;
                    if (nr >= 0 && nr < m && nc >= 0 && nc < n && dsu.isValid(nid)) {
                        dsu.union(id, nid);
                    }
                }
            }
            result.add(dsu.count);
        }
        
        return result;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(K \cdot \alpha(M \cdot N))$ với $K$ là số truy vấn `positions`. Mỗi bước xử lý 4 cạnh.
- Không gian (Space Complexity): $O(M \cdot N)$ cho mảng DSU.

---

## 18. Bricks Falling When Hit (LeetCode 803)
**Đề bài chi tiết:**
Cho ma trận `grid` trong đó `1` là viên gạch, `0` là khoảng trống. Một viên gạch sẽ không rơi nếu nó gắn liền với nóc (dòng 0) hoặc nó dính vào một viên gạch khác không rơi. Có danh sách `hits` là các phát bắn. Mỗi phát làm nổ viên gạch tại `(r, c)`. Khi gạch biến mất, các viên gạch tựa vào nó mà không có chỗ bám lên nóc sẽ bị rơi. Trả về mảng số viên gạch bị rơi sau mỗi phát bắn.

**Phân tích thuật toán:**
Khi xóa dần một đỉnh từ đồ thị, nó có thể làm gãy đồ thị thành nhiều thành phần. DSU thông thường không hỗ trợ thao tác "Split/Remove" hiệu quả. Vì thế ta dùng thủ thuật Time Reversal (Reverse DSU). Thay vì xóa, ta sẽ đi ngược từ cuối về đầu (chạy truy vấn xóa ngược thành thêm).
1. Biến các ô bị hit thành 0. Đếm các viên gạch còn lại vào DSU (kết nối với nút `Top`).
2. Đi ngược `hits`. Nạp lại viên gạch bị phá. Khi kết nối lại, kích thước của thành phần chứa nút `Top` tăng thêm bao nhiêu, thì đó chính là số viên gạch vừa móc được vào mái (bị rơi ở truy vấn thuận).

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int[] size;
        public DSU(int n) {
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                size[ry] += size[rx];
            }
        }
        public int topSize() {
            return size[find(0)];
        }
    }
    
    public int[] hitBricks(int[][] grid, int[][] hits) {
        int R = grid.length, C = grid[0].length;
        int[][] gridCopy = new int[R][C];
        for (int i = 0; i < R; i++) gridCopy[i] = grid[i].clone();
        
        for (int[] hit : hits) {
            gridCopy[hit[0]][hit[1]] = 0; // Hit all bricks
        }
        
        DSU dsu = new DSU(R * C + 1);
        int top = R * C;
        int[] dirs = {-1, 0, 1, 0, -1};
        
        for (int i = 0; i < R; i++) {
            for (int j = 0; j < C; j++) {
                if (gridCopy[i][j] == 1) {
                    int id = i * C + j;
                    if (i == 0) dsu.union(id, top);
                    if (i > 0 && gridCopy[i - 1][j] == 1) dsu.union(id, (i - 1) * C + j);
                    if (j > 0 && gridCopy[i][j - 1] == 1) dsu.union(id, i * C + j - 1);
                }
            }
        }
        
        int[] res = new int[hits.length];
        for (int i = hits.length - 1; i >= 0; i--) {
            int r = hits[i][0];
            int c = hits[i][1];
            if (grid[r][c] == 0) continue; // It was already empty
            
            int preSize = dsu.topSize();
            int id = r * C + c;
            gridCopy[r][c] = 1;
            
            if (r == 0) dsu.union(id, top);
            for (int k = 0; k < 4; k++) {
                int nr = r + dirs[k];
                int nc = c + dirs[k+1];
                if (nr >= 0 && nr < R && nc >= 0 && nc < C && gridCopy[nr][nc] == 1) {
                    dsu.union(id, nr * C + nc);
                }
            }
            
            int newSize = dsu.topSize();
            res[i] = Math.max(0, newSize - preSize - 1);
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O((R \cdot C + Q) \cdot \alpha(R \cdot C))$ với $Q$ là số thao tác bắn.
- Không gian (Space Complexity): $O(R \cdot C)$ lưu ma trận copy và DSU.

---

## 19. Similar String Groups (LeetCode 839)
**Đề bài chi tiết:**
Hai chuỗi $X$ và $Y$ có cùng các ký tự giống nhau nhưng thứ tự có thể khác. Gọi là "tương đồng" nếu tráo đổi 2 vị trí ký tự trong $X$ thì thành $Y$ (hoặc chúng giống hệt nhau). Tính chất tương đồng mang tính bắc cầu. Cho danh sách các chuỗi, hãy đếm xem có bao nhiêu nhóm chuỗi tương đồng.

**Phân tích thuật toán:**
Để kiểm tra 2 chuỗi có tương đồng không: nếu số ký tự khác nhau $\le 2$, chúng tương đồng (vì có thể swap lớn nhất 2 vị trí).
Bài toán đưa về đếm số thành phần liên thông trong đồ thị, nơi đỉnh là các chuỗi. Ta dùng DSU để gộp các chuỗi tương đồng lại với nhau. Số nhóm chính là số Component. Duyệt qua mọi cặp chuỗi để kiểm tra và gộp. Nếu sau khi xét một cặp, chúng thỏa mãn đk tương tự và chưa cùng nhóm thì gọi `union`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                count--;
            }
        }
    }
    
    public int numSimilarGroups(String[] strs) {
        int n = strs.length;
        DSU dsu = new DSU(n);
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isSimilar(strs[i], strs[j])) {
                    dsu.union(i, j);
                }
            }
        }
        return dsu.count;
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
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \cdot L)$ trong đó $N$ là số lượng chuỗi, $L$ là độ dài mỗi chuỗi, do dùng vòng lặp lồng $N^2$ để so sánh từng cặp.
- Không gian (Space Complexity): $O(N)$ lưu DSU parent array.

---

## 20. Checking Existence of Edge Length Limited Paths (LeetCode 1697)
**Đề bài chi tiết:**
Cho đồ thị có `n` đỉnh vô hướng và mảng `edgeList` là trọng lượng (độ dài) các cạnh. Bạn nhận được danh sách `queries`, mỗi truy vấn $q_j = [p_j, q_j, \text{limit}_j]$ hỏi xem có đường đi từ $p_j$ đến $q_j$ mà mọi cạnh trên đường đi đều có trọng lượng nghiêm ngặt nhỏ hơn $\text{limit}_j$ hay không.

**Phân tích thuật toán:**
Dùng Offline DSU kết hợp Sorting.
1. Sort toàn bộ `edgeList` theo độ dài cạnh tăng dần.
2. Lưu kèm ID gốc cho các truy vấn và sort `queries` theo `limit` tăng dần.
3. Duyệt từng truy vấn. Trước khi trả lời, ta có thể `union` mọi cạnh trong `edgeList` có khoảng cách nhỏ hơn `limit` của truy vấn hiện tại. DSU giúp gom các đỉnh liên thông.
4. Trả lời truy vấn: nếu đỉnh $p$ và $q$ cùng thuộc 1 thành phần liên thông trong DSU ($find(p) == find(q)$), kết quả là `true`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
        public boolean isConnected(int x, int y) {
            return find(x) == find(y);
        }
    }
    
    public boolean[] distanceLimitedPathsExist(int n, int[][] edgeList, int[][] queries) {
        int m = queries.length;
        int[][] sortedQueries = new int[m][4];
        for (int i = 0; i < m; i++) {
            sortedQueries[i] = new int[]{queries[i][0], queries[i][1], queries[i][2], i};
        }
        
        Arrays.sort(sortedQueries, (a, b) -> a[2] - b[2]);
        Arrays.sort(edgeList, (a, b) -> a[2] - b[2]);
        
        boolean[] res = new boolean[m];
        DSU dsu = new DSU(n);
        int edgeIdx = 0;
        
        for (int[] q : sortedQueries) {
            int p = q[0], q_dest = q[1], limit = q[2], q_orig_idx = q[3];
            
            while (edgeIdx < edgeList.length && edgeList[edgeIdx][2] < limit) {
                dsu.union(edgeList[edgeIdx][0], edgeList[edgeIdx][1]);
                edgeIdx++;
            }
            
            res[q_orig_idx] = dsu.isConnected(p, q_dest);
        }
        
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(E \log E + Q \log Q + (E+Q)\cdot \alpha(V))$, sắp xếp cạnh và truy vấn.
- Không gian (Space Complexity): $O(V + Q)$ lưu mảng truy vấn có chứa ID gốc và mảng trả lời, cũng như DSU parent array.

---

## 21. Find the City With the Smallest Number of Neighbors at a Threshold Distance (LeetCode 1334)
**Đề bài chi tiết:**
Có `n` thành phố đánh số từ `0` đến `n-1`. Cho mảng `edges` trong đó `edges[i] = [fromi, toi, weighti]` là một cạnh vô hướng giữa 2 thành phố với trọng số là khoảng cách. Cho một số nguyên `distanceThreshold`. Tìm thành phố có số lượng thành phố khác có thể đến được với khoảng cách tối đa `distanceThreshold` là nhỏ nhất. Nếu có nhiều thành phố thỏa mãn, trả về thành phố có chỉ số lớn nhất.

**Phân tích thuật toán:**
Mặc dù bài này thường được giải bằng thuật toán Floyd-Warshall (hoặc Dijkstra nhiều lần) để tìm đường đi ngắn nhất mọi cặp đỉnh, ta vẫn có thể liên hệ đến đồ thị liên thông và giới hạn trọng số. Ở đây, ta trình bày cách giải chuẩn bằng thuật toán Floyd-Warshall thay vì DSU do nó tính toán khoảng cách ngắn nhất trực tiếp một cách hiệu quả nhất (O(N^3)). Ta khởi tạo ma trận khoảng cách, sau đó cập nhật bằng Floyd-Warshall. Cuối cùng, duyệt qua từng thành phố để đếm số lượng hàng xóm nằm trong giới hạn `distanceThreshold` và cập nhật kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        int[][] dist = new int[n][n];
        for (int i = 0; i < n; i++) {
            Arrays.fill(dist[i], 10001);
            dist[i][i] = 0;
        }
        for (int[] edge : edges) {
            dist[edge[0]][edge[1]] = edge[2];
            dist[edge[1]][edge[0]] = edge[2];
        }
        
        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }
        
        int minReachable = n;
        int resCity = -1;
        
        for (int i = 0; i < n; i++) {
            int reachableCount = 0;
            for (int j = 0; j < n; j++) {
                if (i != j && dist[i][j] <= distanceThreshold) {
                    reachableCount++;
                }
            }
            if (reachableCount <= minReachable) {
                minReachable = reachableCount;
                resCity = i;
            }
        }
        
        return resCity;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^3)$
- Không gian (Space Complexity): $O(N^2)$

---

## 22. Lexicographically Smallest Equivalent String (LeetCode 1061)
**Đề bài chi tiết:**
Cho hai chuỗi `s1` và `s2` có cùng độ dài, các ký tự tương ứng tại mỗi vị trí `i` trong 2 chuỗi là tương đương nhau. Tính chất tương đương mang tính bắc cầu. Hãy dùng thông tin tương đương này để biến đổi chuỗi `baseStr` thành chuỗi tương đương có thứ tự từ điển nhỏ nhất (lexicographically smallest).

**Phân tích thuật toán:**
Đây là một ứng dụng rất hay của DSU. Khởi tạo mảng DSU `parent` cho 26 chữ cái. Mặc định chữ cái nào tự làm cha của chữ cái đó.
Khi gộp `Union(x, y)`, thay vì gộp ngẫu nhiên, ta *luôn gộp vào chữ cái có thứ tự từ điển nhỏ hơn*. Tức là gốc của một tập luôn là ký tự nhỏ nhất trong tập đó.
Sau khi tạo xong DSU từ `s1` và `s2`, duyệt qua `baseStr`, lấy `Find()` của từng ký tự để thay thế thành ký tự đại diện nhỏ nhất.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent = new int[26];
        public DSU() {
            for (int i = 0; i < 26; i++) {
                parent[i] = i;
            }
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]); // Nén đường
        }
        public void union(int x, int y) {
            int rootX = find(x);
            int rootY = find(y);
            if (rootX != rootY) {
                if (rootX < rootY) {
                    parent[rootY] = rootX; // Luôn trỏ tới nhỏ hơn
                } else {
                    parent[rootX] = rootY;
                }
            }
        }
    }
    
    public String smallestEquivalentString(String s1, String s2, String baseStr) {
        DSU dsu = new DSU();
        for (int i = 0; i < s1.length(); i++) {
            dsu.union(s1.charAt(i) - 'a', s2.charAt(i) - 'a');
        }
        
        StringBuilder sb = new StringBuilder();
        for (char c : baseStr.toCharArray()) {
            char smallest = (char)(dsu.find(c - 'a') + 'a');
            sb.append(smallest);
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N + M)$ với $N$ là độ dài `s1`, $M$ là độ dài `baseStr`. (Vì DSU kích thước cố định là 26).
- Không gian (Space Complexity): $O(1)$ vì mảng `parent` chỉ chứa 26 phần tử.

---

## 23. Min Cost to Connect All Points (LeetCode 1584)
**Đề bài chi tiết:**
Cho mảng `points` là tọa độ các điểm trên mặt phẳng 2D. Chi phí để nối 2 điểm $[x_i, y_i]$ và $[x_j, y_j]$ là khoảng cách Manhattan: $|x_i - x_j| + |y_i - y_j|$. Trả về chi phí nhỏ nhất để tất cả các điểm kết nối với nhau (trực tiếp hoặc gián tiếp).

**Phân tích thuật toán:**
Đây là bài toán tìm Cây bao trùm nhỏ nhất (Minimum Spanning Tree - MST). Ta có thể dùng thuật toán Kruskal kết hợp DSU. 
Vì đồ thị là đồ thị đầy đủ, ta sẽ tạo toàn bộ các cạnh giữa mọi cặp đỉnh (tổng cộng $O(N^2)$ cạnh), lưu vào mảng và sắp xếp tăng dần theo độ dài.
Sau đó duyệt các cạnh từ nhỏ đến lớn, dùng DSU gom các đỉnh lại cho đến khi số cạnh gộp thành công là $N - 1$.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                return true;
            }
            return false;
        }
    }
    
    public int minCostConnectPoints(int[][] points) {
        int n = points.length;
        List<int[]> edges = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int cost = Math.abs(points[i][0] - points[j][0]) + Math.abs(points[i][1] - points[j][1]);
                edges.add(new int[]{cost, i, j});
            }
        }
        
        edges.sort((a, b) -> Integer.compare(a[0], b[0]));
        
        DSU dsu = new DSU(n);
        int totalCost = 0;
        int edgesConnected = 0;
        
        for (int[] edge : edges) {
            if (dsu.union(edge[1], edge[2])) {
                totalCost += edge[0];
                edgesConnected++;
                if (edgesConnected == n - 1) break;
            }
        }
        
        return totalCost;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N^2 \log (N^2))$ do sắp xếp danh sách các cạnh.
- Không gian (Space Complexity): $O(N^2)$ lưu trữ các cạnh.

---

## 24. Optimization of Water Distribution in a Village (LeetCode 1168)
**Đề bài chi tiết:**
Có `n` ngôi nhà trong làng. Ta có thể cung cấp nước cho một ngôi nhà bằng cách đào một cái giếng tại nhà đó (chi phí nằm trong mảng `wells`), hoặc lắp đường ống dẫn nước từ một ngôi nhà khác đã có nước (chi phí nằm trong `pipes`). Tính tổng chi phí nhỏ nhất để mọi ngôi nhà đều có nước.

**Phân tích thuật toán:**
Có thể xem đây là bài toán MST với một biến thể: một số điểm có thể kết nối với "nguồn ảo" qua việc tự đào giếng. 
Ta tạo một đỉnh ảo (đỉnh `0`). Việc đào giếng tại nhà `i` với chi phí `c` tương đương với việc tạo một cạnh giữa đỉnh `0` và đỉnh `i` với trọng số `c`.
Tất cả các ống dẫn nước là các cạnh giữa 2 nhà. Gom toàn bộ các đường ống và các cạnh nối tới giếng, sau đó dùng thuật toán Kruskal.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                return true;
            }
            return false;
        }
    }
    
    public int minCostToSupplyWater(int n, int[] wells, int[][] pipes) {
        List<int[]> edges = new ArrayList<>();
        
        // Thêm các cạnh nối từ nguồn ảo (0) đến mỗi nhà
        for (int i = 0; i < n; i++) {
            edges.add(new int[]{0, i + 1, wells[i]});
        }
        
        for (int[] pipe : pipes) {
            edges.add(new int[]{pipe[0], pipe[1], pipe[2]});
        }
        
        edges.sort((a, b) -> Integer.compare(a[2], b[2]));
        
        DSU dsu = new DSU(n + 1);
        int minCost = 0;
        int edgesCount = 0;
        
        for (int[] edge : edges) {
            if (dsu.union(edge[0], edge[1])) {
                minCost += edge[2];
                edgesCount++;
                if (edgesCount == n) break; // n cạnh vì có n+1 đỉnh
            }
        }
        return minCost;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(E \log E)$ với $E$ là tổng số đường ống cộng với số nhà.
- Không gian (Space Complexity): $O(E + V)$

---

## 25. Smallest String With Swaps (LeetCode 1202)
**Đề bài chi tiết:**
Cho chuỗi `s` và danh sách các cặp vị trí `pairs`. Bạn được phép tráo đổi vô hạn lần các ký tự ở các cặp vị trí trong `pairs`. Hãy trả về chuỗi có thứ tự từ điển nhỏ nhất sau các phép đổi chỗ.

**Phân tích thuật toán:**
Mỗi cặp `[a, b]` đại diện cho một cạnh nối 2 vị trí. Tất cả các vị trí trong cùng một thành phần liên thông (Connected Component) đều có thể hoán vị tùy ý cho nhau. 
Do đó, thuật toán là:
1. Dùng DSU gom tất cả các chỉ số (index) kết nối với nhau.
2. Với mỗi chỉ số `i`, đẩy ký tự `s[i]` vào danh sách các ký tự thuộc cùng một Group `find(i)`. Dùng PriorityQueue để tự động sắp xếp theo thứ tự từ điển tăng dần.
3. Duyệt lại mảng kết quả, mỗi vị trí `i` lấy ký tự nhỏ nhất từ PriorityQueue tương ứng của `find(i)`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }
    
    public String smallestStringWithSwaps(String s, List<List<Integer>> pairs) {
        int n = s.length();
        DSU dsu = new DSU(n);
        for (List<Integer> pair : pairs) {
            dsu.union(pair.get(0), pair.get(1));
        }
        
        Map<Integer, PriorityQueue<Character>> map = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int root = dsu.find(i);
            map.putIfAbsent(root, new PriorityQueue<>());
            map.get(root).offer(s.charAt(i));
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            sb.append(map.get(dsu.find(i)).poll());
        }
        
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(E \cdot \alpha(V) + V \log V)$ với $E$ là số cặp và $V$ là độ dài chuỗi (tốn thời gian đẩy vào và pop từ PriorityQueue).
- Không gian (Space Complexity): $O(V)$ lưu DSU và các PriorityQueue.

---

## 26. Couples Holding Hands (LeetCode 765)
**Đề bài chi tiết:**
Có `N` cặp đôi (tổng cộng `2N` người) ngồi trên một hàng ghế `2N` chỗ. Người `2i` và `2i+1` là một cặp (VD: 0 & 1, 2 & 3). Cho mảng `row` mô tả vị trí của mọi người hiện tại. Hãy tìm số phép đổi chỗ ít nhất giữa hai người bất kỳ để tất cả các cặp đôi đều được ngồi cạnh nhau. 

**Phân tích thuật toán:**
Ghế chẵn `i` và lẻ `i+1` được gộp thành 1 "Couch" (ghế sofa). Nếu 2 người ngồi trên cùng 1 Couch thuộc về 2 cặp đôi khác nhau, ta có thể coi Couch đó kết nối 2 cặp đôi này lại.
Dùng DSU trên tập hợp các "cặp đôi" (mỗi cặp đôi là 1 ID = `người / 2`). Duyệt qua các Couch, giả sử người trên Couch là `x` và `y`, ta gộp nhóm của cặp đôi chứa `x` (`x/2`) và cặp đôi chứa `y` (`y/2`). 
Nếu $K$ cặp đôi liên thông với nhau, ta chỉ cần đúng $K - 1$ phép hoán đổi để sắp xếp lại chúng. Do đó, tổng số phép đổi bằng $N - \text{Số lượng nhóm (Count)}$.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n];
            count = n;
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                count--;
            }
        }
    }
    
    public int minSwapsCouples(int[] row) {
        int n = row.length / 2;
        DSU dsu = new DSU(n);
        
        for (int i = 0; i < row.length; i += 2) {
            int couple1 = row[i] / 2;
            int couple2 = row[i + 1] / 2;
            dsu.union(couple1, couple2);
        }
        
        return n - dsu.count;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(N \cdot \alpha(N))$
- Không gian (Space Complexity): $O(N)$ lưu DSU.

---

## 27. Path With Minimum Effort (LeetCode 1631)
**Đề bài chi tiết:**
Cho ma trận `heights` gồm độ cao của các ô vuông lưới. Một người có thể đi lên, xuống, trái, phải. "Effort" của một đường đi được tính bằng mức chênh lệch độ cao tuyệt đối lớn nhất giữa hai ô liên tiếp trên đường đi đó. Tìm mức Effort nhỏ nhất để đi từ góc trái trên tới góc phải dưới.

**Phân tích thuật toán:**
Dùng Binary Search kết hợp DSU (hoặc BFS/DFS). 
Biên độ chênh lệch dao động từ $0$ đến $10^6$. Giả sử ta đang thử một giới hạn `limit`. Ta sẽ chỉ cho phép nối các ô kề nhau nếu độ lệch độ cao $\le \text{limit}$. 
Dùng DSU, duyệt lại toàn bộ ma trận, Union mọi cạnh thỏa mãn. Cuối cùng kiểm tra `isConnected(start, end)`. Nếu nối được, giới hạn `limit` này đủ sức đạt tới, ta thử thu hẹp `limit` xuống. Nếu không, ta phải tăng `limit`.
*(Có một hướng khác tiếp cận tương tự Kruskal: Sắp xếp mọi cạnh theo trọng số (sự chênh lệch cao), đưa dần vào DSU đến khi Start và End thông nhau thì cạnh cuối cùng được đưa vào chính là Effort cần tìm)*.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
        public boolean isConnected(int x, int y) {
            return find(x) == find(y);
        }
    }
    
    public int minimumEffortPath(int[][] heights) {
        int R = heights.length;
        int C = heights[0].length;
        
        List<int[]> edges = new ArrayList<>();
        for (int r = 0; r < R; r++) {
            for (int c = 0; c < C; c++) {
                int id = r * C + c;
                if (r > 0) {
                    edges.add(new int[]{Math.abs(heights[r][c] - heights[r-1][c]), id, (r-1) * C + c});
                }
                if (c > 0) {
                    edges.add(new int[]{Math.abs(heights[r][c] - heights[r][c-1]), id, r * C + c - 1});
                }
            }
        }
        
        // Sắp xếp các cạnh để áp dụng tư tưởng Kruskal (hoặc Offline DSU)
        edges.sort((a, b) -> Integer.compare(a[0], b[0]));
        
        DSU dsu = new DSU(R * C);
        for (int[] edge : edges) {
            dsu.union(edge[1], edge[2]);
            if (dsu.isConnected(0, R * C - 1)) {
                return edge[0];
            }
        }
        
        return 0; // Khi chỉ có 1 ô
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(V \log V)$ vì sắp xếp tập các cạnh ($V = R \cdot C$).
- Không gian (Space Complexity): $O(V)$ lưu trữ danh sách các cạnh và DSU.

---

## 28. Connecting Cities With Minimum Cost (LeetCode 1135)
**Đề bài chi tiết:**
Có `n` thành phố từ `1` đến `n` và mảng các `connections`, trong đó `connections[i] = [city1, city2, cost]` là chi phí để nối city1 với city2. Tìm chi phí rẻ nhất để làm mọi thành phố có thể kết nối với nhau. Nếu không thể, trả về `-1`.

**Phân tích thuật toán:**
Đây là bài kinh điển về Cây bao trùm nhỏ nhất (MST).
Sắp xếp danh sách `connections` theo chi phí tăng dần. Khởi tạo DSU với `N` thành phần. Duyệt qua mảng đã sắp xếp, nếu 2 thành phố chưa cùng nhóm thì gộp nhóm, cộng dồn chi phí và giảm biến count đếm số thành phần. Cuối cùng, kiểm tra nếu `count == 1` thì trả về tổng, nếu không trả về `-1`.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        int count;
        public DSU(int n) {
            parent = new int[n + 1];
            count = n;
            for (int i = 1; i <= n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public boolean union(int x, int y) {
            int rx = find(x);
            int ry = find(y);
            if (rx != ry) {
                parent[rx] = ry;
                count--;
                return true;
            }
            return false;
        }
    }
    
    public int minimumCost(int n, int[][] connections) {
        Arrays.sort(connections, (a, b) -> Integer.compare(a[2], b[2]));
        DSU dsu = new DSU(n);
        int totalCost = 0;
        
        for (int[] conn : connections) {
            if (dsu.union(conn[0], conn[1])) {
                totalCost += conn[2];
            }
        }
        
        return dsu.count == 1 ? totalCost : -1;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(E \log E)$ do sắp xếp mảng `connections`.
- Không gian (Space Complexity): $O(V)$ cho DSU.

---

## 29. Last Day Where You Can Still Cross (LeetCode 1970)
**Đề bài chi tiết:**
Cho một lưới `row x col` ban đầu là đất. Ta có mảng `cells` chỉ ra ô nào biến thành nước ở ngày thứ `i`. Ta muốn tìm ngày cuối cùng mà vẫn có thể đi bộ từ hàng trên cùng xuống hàng dưới cùng (đi trên đất).

**Phân tích thuật toán:**
Dùng thủ thuật Reverse DSU (Time Reversal). Ta bắt đầu từ ngày cuối cùng (tất cả là nước). Sau đó đi ngược thời gian, biến mỗi ô bị chìm về lại thành đất. Khi một ô thành đất, ta hợp nhất (Union) nó với các ô đất kề cạnh nó. Ta sẽ gắn hai Đỉnh Ảo là `Top` và `Bottom`. Mọi ô đất ở hàng 0 được nối với `Top`, hàng cuối nối với `Bottom`. Trong quá trình đi ngược, ngay khi `Top` và `Bottom` thông nhau trong DSU, ngày hiện tại chính là ngày cuối cùng mà người đi lại được.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
        public boolean isConnected(int x, int y) {
            return find(x) == find(y);
        }
    }
    
    public int latestDayToCross(int row, int col, int[][] cells) {
        int top = row * col;
        int bottom = row * col + 1;
        DSU dsu = new DSU(row * col + 2);
        int[][] grid = new int[row][col];
        // Ban đầu gán toàn bộ ô bị ngập = 1
        for (int[] cell : cells) {
            grid[cell[0] - 1][cell[1] - 1] = 1;
        }
        
        int[] dr = {-1, 0, 1, 0};
        int[] dc = {0, 1, 0, -1};
        
        for (int i = cells.length - 1; i >= 0; i--) {
            int r = cells[i][0] - 1;
            int c = cells[i][1] - 1;
            int id = r * col + c;
            
            grid[r][c] = 0; // Biến lại thành đất
            
            if (r == 0) dsu.union(id, top);
            if (r == row - 1) dsu.union(id, bottom);
            
            for (int k = 0; k < 4; k++) {
                int nr = r + dr[k];
                int nc = c + dc[k];
                if (nr >= 0 && nr < row && nc >= 0 && nc < col && grid[nr][nc] == 0) {
                    dsu.union(id, nr * col + nc);
                }
            }
            
            if (dsu.isConnected(top, bottom)) {
                return i; // Số ngày đi lại được (do cells 1-indexed)
            }
        }
        return 0;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(K \cdot \alpha(R \cdot C))$ với $K$ là số truy vấn `cells`.
- Không gian (Space Complexity): $O(R \cdot C)$ cho DSU.

---

## 30. Process Restricted Friend Requests (LeetCode 2076)
**Đề bài chi tiết:**
Có `n` người. Có danh sách các cặp bị cấm `restrictions`, không thể trở thành bạn bè. Có danh sách các yêu cầu kết bạn `requests`. Trả về một mảng boolean đánh giá xem mỗi yêu cầu kết bạn có thành công không. Yêu cầu chỉ thành công nếu sau khi gộp hai người (và toàn bộ vòng tròn bạn bè của họ), không có cặp nào trong `restrictions` rơi vào chung một vòng tròn bạn bè.

**Phân tích thuật toán:**
Dùng DSU cơ bản để quản lý mạng lưới bạn bè. Duyệt từng `request[u, v]`. Trước khi thực sự `Union`, gọi `find(u)` và `find(v)` để kiểm tra nếu 2 gốc này kết hợp lại có vi phạm bất kỳ `restriction` nào không.
Ta lặp qua toàn bộ `restrictions[x, y]`. Nếu gốc của `x` và `y` hiện đang lần lượt bằng gốc của `u` và `v` (hoặc ngược lại), thì việc gộp `u` và `v` sẽ đưa `x` và `y` vào cùng 1 nhóm, vi phạm điều kiện. Nếu không vi phạm lệnh cấm nào, ta gộp lại và trả về `true` cho request đó.

**Mã nguồn Java:**
```java
class Solution {
    class DSU {
        int[] parent;
        public DSU(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] == x) return x;
            return parent[x] = find(parent[x]);
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }
    
    public boolean[] friendRequests(int n, int[][] restrictions, int[][] requests) {
        boolean[] res = new boolean[requests.length];
        DSU dsu = new DSU(n);
        
        for (int i = 0; i < requests.length; i++) {
            int u = requests[i][0];
            int v = requests[i][1];
            int rootU = dsu.find(u);
            int rootV = dsu.find(v);
            
            if (rootU == rootV) {
                res[i] = true;
                continue;
            }
            
            boolean isValid = true;
            for (int[] rest : restrictions) {
                int rootX = dsu.find(rest[0]);
                int rootY = dsu.find(rest[1]);
                if ((rootX == rootU && rootY == rootV) || (rootX == rootV && rootY == rootU)) {
                    isValid = false;
                    break;
                }
            }
            
            res[i] = isValid;
            if (isValid) {
                dsu.union(rootU, rootV);
            }
        }
        
        return res;
    }
}
```
**Độ phức tạp:**
- Thời gian (Time Complexity): $O(Q \cdot R \cdot \alpha(N))$ với $Q$ là số lượng yêu cầu `requests` và $R$ là số lượng `restrictions`.
- Không gian (Space Complexity): $O(N)$ lưu DSU.

