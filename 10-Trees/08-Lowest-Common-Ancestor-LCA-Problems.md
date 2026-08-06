# Danh Sách 30 Bài Tập Thuật Toán Tổ Tiên Chung Thấp Nhất (Lowest Common Ancestor - LCA)

Tài liệu này cung cấp lời giải chi tiết cho **30 bài toán kinh điển và nâng cao** về Tổ tiên chung thấp nhất (Lowest Common Ancestor - LCA) trong Cấu trúc Dữ liệu Cây (Tree Data Structures). Mỗi bài toán đều bao gồm:
1. **Đề bài chi tiết** (Detailed Problem Statement).
2. **Phân tích thuật toán chuyên sâu** (Algorithmic Analysis & Strategy).
3. **Mã nguồn Java 21 hoàn chỉnh** (Production-grade / Competitive Programming Java 21 Solution).
4. **Đánh giá độ phức tạp thời gian & không gian bộ nhớ** (Time & Space Complexity Analysis).

---

## Bảng Tổng Hợp 30 Bài Tập

| STT | Tên Bài Toán | Mức Độ | Kỹ Thuật Chính |
| :--- | :--- | :--- | :--- |
| 1 | LCA trong Cây Tìm Kiếm Nhị Phân (BST) | Easy | BST Property Iteration |
| 2 | LCA trong Cây Nhị Phân Thông Thường | Medium | Post-order DFS / Divide & Conquer |
| 3 | LCA với Con Trỏ Cha (Parent Pointers) | Medium | Two Pointers / Intersection Pattern |
| 4 | LCA của Tất Cả Các Nút Lá Sâu Nhất | Medium | Subtree Depth & LCA Recursion |
| 5 | LCA trên Cây Tổng Quát bằng Binary Lifting | Medium | Binary Lifting ($O(N \log N)$ Prep, $O(\log N)$ Query) |
| 6 | Tìm Tổ Tiên Thứ K của một Nút (K-th Ancestor) | Medium | Binary Lifting Jump |
| 7 | Tính Khoảng Cách Giữa Hai Nút trên Cây Không Trọng Số | Medium | Tree Depth & LCA Formula |
| 8 | Tính Khoảng Cách Giữa Hai Nút trên Cây Có Trọng Số Cạnh | Medium | Prefix Distance Array + LCA |
| 9 | Trọng Số Cạnh Lớn Nhất trên Đường Đi Giữa Hai Nút | Medium | Binary Lifting với Bảng Giá Trị Max |
| 10 | Trọng Số Cạnh Nhỏ Nhất trên Đường Đi Giữa Hai Nút | Medium | Binary Lifting với Bảng Giá Trị Min |
| 11 | Kiểm Tra Nút W Có Nằm Trên Đường Đi $(U, V)$ Hay Không | Medium | Tree Distance Property / Subtree Ancestry |
| 12 | Tổ Tiên Chung Thấp Nhất Của Một Tập Hợp $K$ Nút | Medium | Euler In/Out Order hoặc Iterative LCA |
| 13 | Tìm Đường Kính Cây (Tree Diameter) Kết Hợp Truy Vấn LCA | Medium | Double DFS / Distance Table |
| 14 | Mảng Hiệu Trên Cây - Cập Nhật Giá Trị Đỉnh Trên Đường Đi | Hard | Tree Difference Array (Vertex Path Update) |
| 15 | Mảng Hiệu Trên Cây - Cập Nhật Giá Trị Cạnh Trên Đường Đi | Hard | Tree Difference Array (Edge Path Update) |
| 16 | Thuật Toán Tarjan Tìm LCA Ngoại Tuyến (Offline LCA) | Hard | Tarjan's DSU + DFS Traversal |
| 17 | LCA Bằng Tour Euler Kết Hợp Range Minimum Query (RMQ) | Hard | Euler Tour + Sparse Table ($O(1)$ Query) |
| 18 | Tìm Đường Đi Dạng Chuỗi Ký Tự 'U', 'L', 'R' Giữa Hai Nút | Medium | DFS Path Finding qua LCA |
| 19 | Tìm Cây Con Nhỏ Nhất Chứa Toàn Bộ Nút Có Độ Sâu Lớn Nhất | Medium | Bottom-up Return Pair (Depth, Node) |
| 20 | Tìm Nút Cách Nút Đích Khoảng Cách K trên Cây Tổng Quát | Medium | Binary Lifting / K-th Ancestor Navigation |
| 21 | Tính Tổng Trọng Số Đường Đi Giữa Hai Đỉnh $(U, V)$ | Medium | Prefix Sum Array + LCA Formula |
| 22 | Tìm Nút Thứ K trên Đường Đi Đơn từ $U$ đến $V$ | Medium-Hard | Split Path at LCA + Binary Lifting |
| 23 | Tính Ước Chung Lớn Nhất (GCD) trên Đường Đi Giữa Hai Nút | Hard | Binary Lifting với Phép Toán Kết Hợp (GCD Table) |
| 24 | Đếm Cạnh Trọng Yếu (Critical Edges / Bridges on Tree Paths) | Hard | Path Difference Array + Frequency Accumulation |
| 25 | Xây Dựng Cây Ảo (Virtual Tree / Auxiliary Tree) với LCA | Hard | Euler Tour Sorting + Monotonic Stack |
| 26 | Heavy-Light Decomposition (HLD) Kết Hợp Phân Đoạn LCA | Hard | HLD Tree Flattening + Segment Tree |
| 27 | Tìm Trọng Tâm / Nút Trung Điểm Trên Đường Đi $(U, V)$ | Medium-Hard | Path Length + Binary Lifting to Midpoint |
| 28 | Truy Vấn LCA trong Rừng Động (Dynamic Forest) | Hard | DSU Component Checking + Binary Lifting |
| 29 | Truy Vấn XOR Giá Trị Đỉnh Trên Đường Đi $(U, V)$ | Medium | Prefix XOR Property + Root-to-Node |
| 30 | Tìm Khoảng Cách Ngắn Nhất Từ Đỉnh $U$ Đến Bất Kỳ Đỉnh Nào Trong Tập $S$ | Hard | Virtual Tree + Tree DP / Multi-Source BFS |

---

## Chi Tiết 30 Bài Tập & Lời Giải Hoàn Chỉnh

---

### Bài 1: LCA trong Cây Tìm Kiếm Nhị Phân (BST)

**Đề bài chi tiết:**
Cho gốc của một Cây Tìm Kiếm Nhị Phân (Binary Search Tree - BST) và hai nút $p$ và $q$ có mặt trong cây. Hãy tìm Tổ tiên chung thấp nhất (LCA) của $p$ và $q$.
Theo định nghĩa, LCA của hai nút $p$ và $q$ là nút $x$ sâu nhất trên cây sao cho cả $p$ và $q$ đều là hậu duệ của $x$ (một nút được coi là hậu duệ của chính nó).

**Phân tích thuật toán:**
Trong cây BST, với mọi nút $u$: mọi nút ở cây con bên trái có giá trị nhỏ hơn $u.val$, và mọi nút ở cây con bên phải có giá trị lớn hơn $u.val$.
- Bắt đầu từ $root$:
  - Nếu cả $p.val$ và $q.val$ đều nhỏ hơn $curr.val$, LCA chắc chắn nằm ở cây con bên trái $\rightarrow curr = curr.left$.
  - Nếu cả $p.val$ và $q.val$ đều lớn hơn $curr.val$, LCA chắc chắn nằm ở cây con bên phải $\rightarrow curr = curr.right$.
  - Nếu $p$ và $q$ phân nhánh sang hai phía (một giá trị $\le curr.val$ và một giá trị $\ge curr.val$), hoặc một trong hai nút chính là $curr$, thì $curr$ chính là LCA duy nhất cần tìm.
- Phương pháp lặp (Iterative) giúp đạt $O(1)$ không gian bộ nhớ phụ trợ, không tốn Call Stack.

**Mã nguồn Java:**
```java
public class Solution1 {
    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;
        public TreeNode(int val) { this.val = val; }
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        TreeNode curr = root;
        while (curr != null) {
            if (p.val < curr.val && q.val < curr.val) {
                curr = curr.left;
            } else if (p.val > curr.val && q.val > curr.val) {
                curr = curr.right;
            } else {
                return curr;
            }
        }
        return null;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(H)$ với $H$ là chiều cao của BST ($O(\log N)$ khi cân bằng, $O(N)$ trong trường hợp suy biến).
- **Bộ nhớ:** $O(1)$ phụ trợ.

---

### Bài 2: LCA trong Cây Nhị Phân Thông Thường (Binary Tree)

**Đề bài chi tiết:**
Cho gốc của một cây nhị phân bất kỳ và hai nút $p, q$ thuộc cây. Hãy tìm LCA của $p$ và $q$. Cây không có tính chất BST và các giá trị nút là phân biệt.

**Phân tích thuật toán:**
Áp dụng chiến lược Chia để trị (Divide and Conquer) với duyệt hậu thứ tự (Post-order Traversal):
- Nếu duyệt gặp `null`, trả về `null`.
- Nếu nút hiện tại chính là $p$ hoặc $q$, ta trả về nút hiện tại.
- Đệ quy tìm kiếm $p$ và $q$ ở cây con trái (`leftLCA`) và cây con phải (`rightLCA`).
- Kết hợp kết quả:
  - Nếu cả `leftLCA` và `rightLCA` đều khác `null`, nghĩa là $p$ nằm một bên và $q$ nằm một bên $\rightarrow$ Nút hiện tại chính là LCA.
  - Nếu chỉ một bên khác `null`, trả về kết quả khác `null` đó (LCA hoặc nút đã tìm thấy nằm hoàn toàn ở nhánh con đó).

**Mã nguồn Java:**
```java
public class Solution2 {
    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;
        public TreeNode(int val) { this.val = val; }
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }

        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);

        if (left != null && right != null) {
            return root;
        }
        return (left != null) ? left : right;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$ do mỗi nút trên cây được duyệt qua đúng một lần.
- **Bộ nhớ:** $O(H)$ không gian ngăn xếp đệ quy (Call Stack).

---

### Bài 3: LCA với Con Trỏ Cha (Parent Pointers)

**Đề bài chi tiết:**
Cho hai nút $p$ và $q$ trong một cây nhị phân, trong đó mỗi nút có thêm thuộc tính tham chiếu `parent` trỏ tới nút cha trực tiếp của nó (nút gốc có `parent = null`). Hãy tìm LCA của $p$ và $q$ mà không cần biết nút gốc `root`.

**Phân tích thuật toán:**
Bài toán này tương đương với bài toán "Tìm điểm giao nhau của hai danh sách liên kết đơn" (Intersection of Two Linked Lists):
- Khi di chuyển từ $p$ lên gốc (qua con trỏ cha) và từ $q$ lên gốc, hai đường đi sẽ hội tụ tại LCA.
- Sử dụng kỹ thuật 2 con trỏ:
  - Con trỏ $A$ xuất phát từ $p$, con trỏ $B$ xuất phát từ $q$.
  - Mỗi bước, $A = A.parent$, $B = B.parent$.
  - Khi $A$ chạm `null` (đến trên gốc), gán $A = q$.
  - Khi $B$ chạm `null`, gán $B = p$.
  - Hai con trỏ đi tổng quãng đường bằng nhau ($dist(p, root) + dist(q, root)$) và sẽ gặp nhau chính xác tại nút LCA đầu tiên.

**Mã nguồn Java:**
```java
public class Solution3 {
    public static class Node {
        public int val;
        public Node left;
        public Node right;
        public Node parent;
        public Node(int val) { this.val = val; }
    }

    public Node lowestCommonAncestor(Node p, Node q) {
        Node a = p;
        Node b = q;
        while (a != b) {
            a = (a == null) ? q : a.parent;
            b = (b == null) ? p : b.parent;
        }
        return a;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(H_p + H_q) \le O(H)$ với $H$ là chiều cao cây.
- **Bộ nhớ:** $O(1)$ không cần cấu trúc dữ liệu phụ trợ.

---

### Bài 4: LCA của Tất Cả Các Nút Lá Sâu Nhất

**Đề bài chi tiết:**
Cho gốc của một cây nhị phân. Hãy tìm gốc của cây con nhỏ nhất chứa tất cả các nút lá có độ sâu lớn nhất trong toàn bộ cây.

**Phân tích thuật toán:**
- Một nút $u$ là gốc cây con nhỏ nhất chứa tất cả các lá sâu nhất nếu và chỉ nếu:
  - Độ sâu lớn nhất của cây con bên trái bằng độ sâu lớn nhất của cây con bên phải, và giá trị độ sâu này bằng độ sâu lớn nhất toàn cục.
- Ta xây dựng hàm đệ quy trả về một cặp `(depth, lcaNode)`:
  - Nếu nhánh trái sâu hơn nhánh phải: kết quả lấy từ nhánh trái `(leftDepth + 1, leftNode)`.
  - Nếu nhánh phải sâu hơn nhánh trái: kết quả lấy từ nhánh phải `(rightDepth + 1, rightNode)`.
  - Nếu cả hai nhánh có độ sâu bằng nhau: nút hiện tại chính là LCA của các lá sâu nhất của cả hai nhánh, trả về `(leftDepth + 1, curr)`.

**Mã nguồn Java:**
```java
public class Solution4 {
    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;
        public TreeNode(int val) { this.val = val; }
    }

    private record Result(int depth, TreeNode node) {}

    public TreeNode lcaDeepestLeaves(TreeNode root) {
        return dfs(root).node();
    }

    private Result dfs(TreeNode node) {
        if (node == null) {
            return new Result(0, null);
        }
        Result left = dfs(node.left);
        Result right = dfs(node.right);

        if (left.depth() == right.depth()) {
            return new Result(left.depth() + 1, node);
        } else if (left.depth() > right.depth()) {
            return new Result(left.depth() + 1, left.node());
        } else {
            return new Result(right.depth() + 1, right.node());
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$ duyệt cây 1 lần duy nhất.
- **Bộ nhớ:** $O(H)$ không gian Call Stack.

---

### Bài 5: LCA trên Cây Tổng Quát bằng Binary Lifting

**Đề bài chi tiết:**
Cho một cây vô hướng gồm $N$ đỉnh được đánh số từ $1$ đến $N$, gốc tại đỉnh $1$. Xử lý $Q$ truy vấn, mỗi truy vấn gồm 2 đỉnh $u, v$, yêu cầu tìm $LCA(u, v)$. Cần cấu trúc tiền xử lý cho phép trả lời mỗi truy vấn trong thời gian $O(\log N)$.

**Phân tích thuật toán:**
Áp dụng kỹ thuật **Binary Lifting** (Nhảy nhị phân):
- Định nghĩa $up[u][k]$ là tổ tiên thứ $2^k$ của đỉnh $u$.
- Công thức chuyển trạng thái quy hoạch động:
  $$up[u][k] = up[\,up[u][k-1]\,][k-1]$$
- Để tìm $LCA(u, v)$:
  1. Đưa $u$ và $v$ về cùng độ sâu (nhảy đỉnh sâu hơn lên bằng các bước $2^k$).
  2. Nếu sau khi cân bằng $u == v$, thì $u$ chính là LCA.
  3. Nếu $u \ne v$, thử nhảy đồng thời cả hai lên các bước $2^k$ từ lớn về nhỏ ($k = \lfloor \log_2 N \rfloor \dots 0$). Nếu $up[u][k] \ne up[v][k]$, thực hiện nhảy $u = up[u][k], v = up[v][k]$.
  4. Cuối cùng, $u$ và $v$ sẽ nằm ngay dưới LCA, do đó $LCA(u, v) = up[u][0]$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution5 {
    private final int n;
    private final int log;
    private final List<Integer>[] adj;
    private final int[][] up;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution5(int n, List<Integer>[] adj, int root) {
        this.n = n;
        this.adj = adj;
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];

        dfs(root, root, 0);
    }

    private void dfs(int u, int p, int d) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) {
            int tmp = u; u = v; v = tmp;
        }

        // 1. Nhảy u lên cùng độ sâu với v
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                u = up[u][k];
            }
        }

        if (u == v) return u;

        // 2. Nhảy đồng thời u và v
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }

        return up[u][0];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$ thời gian và không gian bộ nhớ.
- **Mỗi truy vấn:** $O(\log N)$ thời gian, $O(1)$ bộ nhớ.

---

### Bài 6: Tìm Tổ Tiên Thứ K của một Nút (K-th Ancestor)

**Đề bài chi tiết:**
Cho một cây gồm $N$ nút được đánh số từ $0$ đến $N - 1$, cho mảng `parent` với `parent[i]` là cha của nút $i$ (gốc có `parent[0] = -1`). Hãy hiện thực lớp `TreeAncestor(int n, int[] parent)` có phương thức `int getKthAncestor(int node, int k)` trả về tổ tiên thứ $k$ của `node`. Nếu không tồn tại (vượt quá gốc), trả về `-1`.

**Phân tích thuật toán:**
- Biểu diễn $k$ dưới dạng tổng các luỹ thừa của 2: $k = \sum b_i \cdot 2^i$ (biểu diễn nhị phân).
- Ta xây dựng bảng `up[node][step]` bằng Binary Lifting:
  - `up[node][0] = parent[node]`
  - `up[node][i] = (up[node][i-1] != -1) ? up[up[node][i-1]][i-1] : -1`
- Khi truy vấn `getKthAncestor(node, k)`: lặp qua các bit $i$ của $k$, nếu bit thứ $i$ bật, nhảy `node = up[node][i]`. Nếu `node == -1`, dừng sớm và trả về `-1`.

**Mã nguồn Java:**
```java
public class Solution6 {
    static class TreeAncestor {
        private final int[][] up;
        private final int maxLog;

        public TreeAncestor(int n, int[] parent) {
            this.maxLog = 32 - Integer.numberOfLeadingZeros(n);
            this.up = new int[n][maxLog];

            for (int i = 0; i < n; i++) {
                up[i][0] = parent[i];
            }

            for (int j = 1; j < maxLog; j++) {
                for (int i = 0; i < n; i++) {
                    if (up[i][j - 1] == -1) {
                        up[i][j] = -1;
                    } else {
                        up[i][j] = up[up[i][j - 1]][j - 1];
                    }
                }
            }
        }

        public int getKthAncestor(int node, int k) {
            for (int j = 0; j < maxLog; j++) {
                if (((k >> j) & 1) == 1) {
                    node = up[node][j];
                    if (node == -1) return -1;
                }
            }
            return node;
        }
    }
}
```

**Độ phức tạp:**
- **Khởi tạo:** $O(N \log N)$ thời gian và bộ nhớ.
- **Truy vấn:** $O(\log K)$ thời gian, $O(1)$ bộ nhớ.

---

### Bài 7: Tính Khoảng Cách Giữa Hai Nút trên Cây Không Trọng Số

**Đề bài chi tiết:**
Cho một cây không trọng số gồm $N$ đỉnh. Xử lý nhiều truy vấn dạng `distance(u, v)` yêu cầu tính số cạnh trên đường đi đơn ngắn nhất giữa hai đỉnh $u$ và $v$.

**Phân tích thuật toán:**
Gọi $depth[u]$ là khoảng cách từ gốc đến đỉnh $u$.
Đường đi đơn giữa $u$ và $v$ đi từ $u$ lên $LCA(u, v)$ rồi đi xuống $v$. Do đó, công thức tính khoảng cách là:
$$dist(u, v) = depth[u] + depth[v] - 2 \times depth[LCA(u, v)]$$
Sử dụng Binary Lifting để tính $depth$ và tìm $LCA(u, v)$ trong $O(\log N)$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution7 {
    private final int log;
    private final int[][] up;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution7(int n, List<Integer>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1, adj);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                u = up[u][k];
            }
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public int distance(int u, int v) {
        int lca = getLCA(u, v);
        return depth[u] + depth[v] - 2 * depth[lca];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 8: Tính Khoảng Cách Giữa Hai Nút trên Cây Có Trọng Số Cạnh

**Đề bài chi tiết:**
Cho một cây có trọng số gồm $N$ đỉnh và $N - 1$ cạnh, trọng số mỗi cạnh là số nguyên không âm. Cho $Q$ truy vấn khoảng cách, mỗi truy vấn $(u, v)$ yêu cầu tính tổng trọng số các cạnh trên đường đi từ $u$ đến $v$.

**Phân tích thuật toán:**
- Tương tự cây không trọng số, gọi $distToRoot[u]$ là tổng trọng số các cạnh trên đường đi từ gốc đến $u$.
- Tính chất đường đi:
  $$dist(u, v) = distToRoot[u] + distToRoot[v] - 2 \times distToRoot[LCA(u, v)]$$
- Sử dụng DFS để tính $depth[u]$, $distToRoot[u]$ và bảng $up[u][k]$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution8 {
    public record Edge(int to, long weight) {}

    private final int log;
    private final int[][] up;
    private final int[] depth;
    private final long[] distToRoot;

    @SuppressWarnings("unchecked")
    public Solution8(int n, List<Edge>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.distToRoot = new long[n + 1];

        dfs(root, root, 0, 0L, adj);
    }

    private void dfs(int u, int p, int d, long dist, List<Edge>[] adj) {
        depth[u] = d;
        distToRoot[u] = dist;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (Edge e : adj[u]) {
            if (e.to() != p) {
                dfs(e.to(), u, d + 1, dist + e.weight(), adj);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                u = up[u][k];
            }
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public long getWeightedDistance(int u, int v) {
        int lca = getLCA(u, v);
        return distToRoot[u] + distToRoot[v] - 2 * distToRoot[lca];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$ thời gian và không gian.
- **Mỗi truy vấn:** $O(\log N)$ thời gian.

---

### Bài 9: Trọng Số Cạnh Lớn Nhất trên Đường Đi Giữa Hai Nút

**Đề bài chi tiết:**
Cho một cây gồm $N$ đỉnh có trọng số cạnh. Xử lý các truy vấn: Với mỗi cặp đỉnh $(u, v)$, tìm trọng số cạnh lớn nhất (Maximum Edge Weight) trên đường đi đơn giữa $u$ và $v$.

**Phân tích thuật toán:**
Mở rộng bảng Binary Lifting với mảng DP $maxEdge[u][k]$: trọng số cạnh lớn nhất trên đoạn nhảy từ đỉnh $u$ lên tổ tiên thứ $2^k$.
- Công thức chuyển trạng thái:
  $$maxEdge[u][k] = \max(maxEdge[u][k-1],\, maxEdge[up[u][k-1]][k-1])$$
- Khi truy vấn $(u, v)$:
  1. Nhảy đỉnh sâu hơn lên cùng độ sâu, tích luỹ giá trị lớn nhất gặp trên đường nhảy.
  2. Nếu hai đỉnh chưa gặp nhau, nhảy đồng thời lên $LCA(u, v)$, liên tục cập nhật kết quả $\max$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution9 {
    public record Edge(int to, int weight) {}

    private final int log;
    private final int[][] up;
    private final int[][] maxEdge;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution9(int n, List<Edge>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.maxEdge = new int[n + 1][log];
        this.depth = new int[n + 1];

        dfs(root, root, 0, 0, adj);
    }

    private void dfs(int u, int p, int d, int wToParent, List<Edge>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        maxEdge[u][0] = wToParent;

        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
            maxEdge[u][k] = Math.max(maxEdge[u][k - 1], maxEdge[up[u][k - 1]][k - 1]);
        }

        for (Edge e : adj[u]) {
            if (e.to() != p) {
                dfs(e.to(), u, d + 1, e.weight(), adj);
            }
        }
    }

    public int queryMaxEdge(int u, int v) {
        int ans = Integer.MIN_VALUE;
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }

        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                ans = Math.max(ans, maxEdge[u][k]);
                u = up[u][k];
            }
        }

        if (u == v) return ans;

        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                ans = Math.max(ans, Math.max(maxEdge[u][k], maxEdge[v][k]));
                u = up[u][k];
                v = up[v][k];
            }
        }

        ans = Math.max(ans, Math.max(maxEdge[u][0], maxEdge[v][0]));
        return ans;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 10: Trọng Số Cạnh Nhỏ Nhất trên Đường Đi Giữa Hai Nút

**Đề bài chi tiết:**
Cho cây $N$ đỉnh có trọng số cạnh dương. Xử lý các truy vấn tìm trọng số cạnh nhỏ nhất (Minimum Edge Weight / Bottleneck Capacity) trên đường đi đơn giữa $u$ và $v$.

**Phân tích thuật toán:**
Tương tự Bài 9, áp dụng Binary Lifting với bảng $minEdge[u][k]$:
- Cơ sở: $minEdge[u][0] = weight(u, parent[u])$.
- Quy hoạch động:
  $$minEdge[u][k] = \min(minEdge[u][k-1],\, minEdge[up[u][k-1]][k-1])$$
- Khi truy vấn, kết hợp các đoạn nhảy nhị phân bằng phép lấy $\min$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution10 {
    public record Edge(int to, int weight) {}

    private final int log;
    private final int[][] up;
    private final int[][] minEdge;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution10(int n, List<Edge>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.minEdge = new int[n + 1][log];
        this.depth = new int[n + 1];

        dfs(root, root, 0, Integer.MAX_VALUE, adj);
    }

    private void dfs(int u, int p, int d, int wToParent, List<Edge>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        minEdge[u][0] = wToParent;

        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
            minEdge[u][k] = Math.min(minEdge[u][k - 1], minEdge[up[u][k - 1]][k - 1]);
        }

        for (Edge e : adj[u]) {
            if (e.to() != p) {
                dfs(e.to(), u, d + 1, e.weight(), adj);
            }
        }
    }

    public int queryMinEdge(int u, int v) {
        if (u == v) return 0;
        int ans = Integer.MAX_VALUE;
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }

        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                ans = Math.min(ans, minEdge[u][k]);
                u = up[u][k];
            }
        }

        if (u == v) return ans;

        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                ans = Math.min(ans, Math.min(minEdge[u][k], minEdge[v][k]));
                u = up[u][k];
                v = up[v][k];
            }
        }

        ans = Math.min(ans, Math.min(minEdge[u][0], minEdge[v][0]));
        return ans;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 11: Kiểm Tra Nút W Có Nằm Trên Đường Đi $(U, V)$ Hay Không

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh. Cho $Q$ truy vấn bộ ba $(u, v, w)$, hãy kiểm tra xem đỉnh $w$ có nằm trên đường đi đơn nối giữa đỉnh $u$ và đỉnh $v$ hay không.

**Phân tích thuật toán:**
Có hai phương pháp kiểm tra:
- **Phương pháp 1 (Dựa vào khoảng cách):** Đỉnh $w$ thuộc đường đi $(u, v)$ khi và chỉ khi:
  $$dist(u, w) + dist(w, v) = dist(u, v)$$
  Với $dist(a, b)$ được tính qua $LCA(a, b)$ trong $O(\log N)$.
- **Phương pháp 2 (Dựa vào quan hệ tổ tiên - Subtree Ancestry):**
  $w$ nằm trên đường đi $(u, v)$ khi và chỉ khi:
  1. $w$ là tổ tiên của $u$ hoặc $w$ là tổ tiên của $v$.
  2. $w$ là hậu duệ của $LCA(u, v)$ (tức $depth[w] \ge depth[LCA(u, v)]$).
Phương pháp 1 đơn giản, tổng quát và dễ cài đặt chính xác nhất.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution11 {
    private final int log;
    private final int[][] up;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution11(int n, List<Integer>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    private int dist(int u, int v) {
        return depth[u] + depth[v] - 2 * depth[getLCA(u, v)];
    }

    public boolean isNodeOnPath(int u, int v, int w) {
        return dist(u, w) + dist(w, v) == dist(u, v);
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Mỗi truy vấn:** $O(\log N)$ (tính 3 khoảng cách).
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 12: Tổ Tiên Chung Thấp Nhất Của Một Tập Hợp K Nút

**Đề bài chi tiết:**
Cho cây $N$ đỉnh. Cho một tập hợp gồm $K$ đỉnh $S = \{v_1, v_2, \dots, v_K\}$. Hãy tìm tổ tiên chung thấp nhất của toàn bộ tập hợp $S$.

**Phân tích thuật toán:**
- **Định lý Euler Order:** Nếu ta sắp xếp các nút trong cây theo thứ tự thăm trong Euler Tour (thời điểm vào $tin[u]$):
  Tổ tiên chung của tập hợp $S$ chính là $LCA(u_{min}, u_{max})$, trong đó $u_{min}$ là đỉnh có $tin$ nhỏ nhất trong $S$, và $u_{max}$ là đỉnh có $tin$ lớn nhất trong $S$.
- **Thuật toán:**
  1. Trong quá trình DFS, ghi nhận mảng $tin[u]$ (thời điểm duyệt tới $u$).
  2. Với truy vấn tập $S$: duyệt qua $S$, tìm $u_{min} = \arg\min_{v \in S} tin[v]$ và $u_{max} = \arg\max_{v \in S} tin[v]$.
  3. Trả về $LCA(u_{min}, u_{max})$.
- Tiết kiệm đáng kể thay vì phải gọi $K - 1$ lần hàm LCA!

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution12 {
    private final int log;
    private final int[][] up;
    private final int[] depth;
    private final int[] tin;
    private int timer = 0;

    @SuppressWarnings("unchecked")
    public Solution12(int n, List<Integer>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.tin = new int[n + 1];
        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        tin[u] = ++timer;
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public int getSubsetLCA(List<Integer> nodes) {
        if (nodes.isEmpty()) return -1;
        int minNode = nodes.get(0);
        int maxNode = nodes.get(0);

        for (int node : nodes) {
            if (tin[node] < tin[minNode]) minNode = node;
            if (tin[node] > tin[maxNode]) maxNode = node;
        }

        return getLCA(minNode, maxNode);
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(|S| + \log N)$ (tìm min/max trong $O(|S|)$ và 1 lần gọi LCA $O(\log N)$).
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 13: Tìm Đường Kính Cây Kết Hợp Truy Vấn LCA

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh có trọng số cạnh. Cần tìm hai đầu mút của đường kính cây $(d_1, d_2)$ và hỗ trợ truy vấn nhanh: cho một đỉnh $x$ bất kỳ, tìm khoảng cách xa nhất từ $x$ đến bất kỳ đỉnh nào trên cây.

**Phân tích thuật toán:**
- **Tính chất đường kính cây:** Đỉnh xa nhất từ một đỉnh bất kỳ $x$ luôn là một trong hai đầu mút của đường kính cây ($d_1$ hoặc $d_2$).
- Do đó: $\max_{v \in T} dist(x, v) = \max(dist(x, d_1), dist(x, d_2))$.
- Ta tìm $d_1, d_2$ bằng 2 lần DFS/BFS:
  1. DFS từ đỉnh 1 tìm đỉnh $d_1$ xa nhất.
  2. DFS từ $d_1$ tìm đỉnh $d_2$ xa nhất.
- Với mỗi truy vấn $x$, tính khoảng cách tới $d_1$ và $d_2$ qua LCA trong $O(\log N)$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution13 {
    public record Edge(int to, long weight) {}

    private final int n;
    private final List<Edge>[] adj;
    private final Solution8 lcaHelper;
    private int d1, d2;
    private long maxDist;

    @SuppressWarnings("unchecked")
    public Solution13(int n, List<Edge>[] adj) {
        this.n = n;
        this.adj = adj;
        this.lcaHelper = new Solution8(n, adj, 1);
        findDiameter();
    }

    private void findDiameter() {
        maxDist = -1;
        dfsFarthest(1, 0, 0, 1);
        int endpoint1 = farthestNode;

        maxDist = -1;
        dfsFarthest(endpoint1, 0, 0, endpoint1);
        int endpoint2 = farthestNode;

        this.d1 = endpoint1;
        this.d2 = endpoint2;
    }

    private int farthestNode;
    private void dfsFarthest(int u, int p, long d, int start) {
        if (d > maxDist) {
            maxDist = d;
            farthestNode = u;
        }
        for (Edge e : adj[u]) {
            if (e.to() != p) {
                dfsFarthest(e.to(), u, d + e.weight(), start);
            }
        }
    }

    public long getMaxDistance(int x) {
        long dist1 = lcaHelper.getWeightedDistance(x, d1);
        long dist2 = lcaHelper.getWeightedDistance(x, d2);
        return Math.max(dist1, dist2);
    }
}
```

**Độ phức tạp:**
- **Khởi tạo:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 14: Mảng Hiệu Trên Cây - Cập Nhật Giá Trị Đỉnh Trên Đường Đi

**Đề bài chi tiết:**
Cho cây $N$ đỉnh ban đầu có giá trị các đỉnh bằng 0. Cho $M$ thao tác, mỗi thao tác cộng thêm giá trị $V$ vào tất cả các đỉnh trên đường đi đơn nối giữa $u$ và $v$. Sau khi thực hiện xong tất cả $M$ thao tác, hãy in ra giá trị cuối cùng của tất cả $N$ đỉnh.

**Phân tích thuật toán:**
Áp dụng **Mảng hiệu trên cây (Tree Difference Array for Vertices)**:
- Với mỗi thao tác $(u, v, +val)$, ta cập nhật mảng hiệu $diff$:
  $$diff[u] \mathrel{+}= val$$
  $$diff[v] \mathrel{+}= val$$
  $$diff[LCA(u, v)] \mathrel{-}= val$$
  $$diff[parent[LCA(u, v)]] \mathrel{-}= val \quad (\text{nếu } LCA \ne root)$$
- Sau khi xử lý toàn bộ $M$ thao tác ngoại tuyến, ta thực hiện 1 lần DFS Post-order từ dưới lá lên:
  $$val[u] = diff[u] + \sum_{v \in children(u)} val[v]$$

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution14 {
    private final int n;
    private final int log;
    private final List<Integer>[] adj;
    private final int[][] up;
    private final int[] depth;
    private final long[] diff;
    private final long[] finalValues;

    @SuppressWarnings("unchecked")
    public Solution14(int n, List<Integer>[] adj, int root) {
        this.n = n;
        this.adj = adj;
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.diff = new long[n + 1];
        this.finalValues = new long[n + 1];

        dfsInit(root, 0, 0);
    }

    private void dfsInit(int u, int p, int d) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = (up[u][k - 1] != 0) ? up[up[u][k - 1]][k - 1] : 0;
        }
        for (int v : adj[u]) {
            if (v != p) dfsInit(v, u, d + 1);
        }
    }

    private int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public void addPath(int u, int v, long val) {
        int lca = getLCA(u, v);
        diff[u] += val;
        diff[v] += val;
        diff[lca] -= val;
        int pLca = up[lca][0];
        if (pLca != 0) {
            diff[pLca] -= val;
        }
    }

    public long[] computeFinalValues(int root) {
        dfsAccumulate(root, 0);
        return finalValues;
    }

    private void dfsAccumulate(int u, int p) {
        finalValues[u] = diff[u];
        for (int v : adj[u]) {
            if (v != p) {
                dfsAccumulate(v, u);
                finalValues[u] += finalValues[v];
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O((N + M) \log N)$ cho $M$ thao tác cập nhật mảng hiệu, và $O(N)$ cho lần DFS tích luỹ cuối cùng.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 15: Mảng Hiệu Trên Cây - Cập Nhật Giá Trị Cạnh Trên Đường Đi

**Đề bài chi tiết:**
Cho cây $N$ đỉnh và $N - 1$ cạnh. Ban đầu tất cả các cạnh có trọng số bằng 0. Cho $M$ truy vấn: mỗi truy vấn cộng thêm $val$ vào tất cả các **cạnh** trên đường đi giữa $u$ và $v$. Hãy tìm giá trị cuối cùng của mỗi cạnh trên cây.

**Phân tích thuật toán:**
- Mỗi cạnh nối $(v, parent[v])$ được đại diện duy nhất bởi đỉnh con $v$.
- Khi cộng $val$ vào các cạnh trên đường đi từ $u$ đến $v$:
  - Đoạn $u \to LCA(u, v)$ có các cạnh đại diện bởi các đỉnh từ $u$ lên con trực tiếp của LCA.
  - Đoạn $v \to LCA(u, v)$ tương tự.
- Công thức **Mảng hiệu trên cạnh**:
  $$diff[u] \mathrel{+}= val$$
  $$diff[v] \mathrel{+}= val$$
  $$diff[LCA(u, v)] \mathrel{-}= 2 \times val$$
- Sau đó chạy DFS tích luỹ từ lá lên, giá trị tại đỉnh $v$ chính là giá trị của cạnh nối $v$ với cha của nó.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution15 {
    public record EdgeInfo(int edgeId, int u, int v) {}

    private final int n;
    private final int log;
    private final List<int[]>[] adj; // int[] {neighbor, edgeId}
    private final int[][] up;
    private final int[] depth;
    private final int[] edgeToParent;
    private final long[] diff;
    private final long[] resultEdgeVal;

    @SuppressWarnings("unchecked")
    public Solution15(int n, List<int[]>[] adj, int root, int numEdges) {
        this.n = n;
        this.adj = adj;
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.edgeToParent = new int[n + 1];
        this.diff = new long[n + 1];
        this.resultEdgeVal = new long[numEdges + 1];

        dfsInit(root, 0, 0, 0);
    }

    private void dfsInit(int u, int p, int d, int eId) {
        depth[u] = d;
        up[u][0] = p;
        edgeToParent[u] = eId;
        for (int k = 1; k < log; k++) {
            up[u][k] = (up[u][k - 1] != 0) ? up[up[u][k - 1]][k - 1] : 0;
        }
        for (int[] next : adj[u]) {
            int v = next[0];
            int nextEdgeId = next[1];
            if (v != p) dfsInit(v, u, d + 1, nextEdgeId);
        }
    }

    private int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public void addPathEdge(int u, int v, long val) {
        int lca = getLCA(u, v);
        diff[u] += val;
        diff[v] += val;
        diff[lca] -= 2 * val;
    }

    public long[] computeAllEdges(int root) {
        dfsAccumulate(root, 0);
        return resultEdgeVal;
    }

    private long dfsAccumulate(int u, int p) {
        long currentSum = diff[u];
        for (int[] next : adj[u]) {
            int v = next[0];
            if (v != p) {
                currentSum += dfsAccumulate(v, u);
            }
        }
        if (edgeToParent[u] != 0) {
            resultEdgeVal[edgeToParent[u]] = currentSum;
        }
        return currentSum;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \log N + M \log N + N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 16: Thuật Toán Tarjan Tìm LCA Ngoại Tuyến (Offline LCA)

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh và tập $Q$ truy vấn $(u, v)$ đã biết trước (Offline). Hãy tìm LCA cho toàn bộ $Q$ truy vấn với thời gian tuyến tính gần như $O(N + Q)$ sử dụng thuật toán Tarjan kết hợp cấu trúc Disjoint Set Union (DSU).

**Phân tích thuật toán:**
- Thuật toán duyệt cây bằng DFS hậu thứ tự (Post-order) kết hợp DSU:
  1. Khởi tạo mỗi đỉnh $u$ là một tập hợp trong DSU có đại diện `ancestor[u] = u`.
  2. Thăm các đỉnh con của $u$. Sau khi duyệt xong cây con của $v$, hợp nhất tập chứa $v$ vào tập chứa $u$ (`union(u, v)`) và gán `ancestor[find(u)] = u`.
  3. Đánh dấu đỉnh $u$ đã hoàn thành (`visited[u] = true`).
  4. Với mỗi truy vấn $(u, v)$ có liên quan tới $u$: nếu $v$ đã được thăm (`visited[v] == true`), thì $LCA(u, v) = ancestor[find(v)]$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution16 {
    public record Query(int otherNode, int queryIndex) {}

    private final int n;
    private final List<Integer>[] adj;
    private final List<Query>[] queries;
    private final int[] parent;
    private final int[] ancestor;
    private final boolean[] visited;
    private final int[] answers;

    @SuppressWarnings("unchecked")
    public Solution16(int n, List<Integer>[] adj, int q) {
        this.n = n;
        this.adj = adj;
        this.queries = new List[n + 1];
        for (int i = 1; i <= n; i++) queries[i] = new ArrayList<>();

        this.parent = new int[n + 1];
        this.ancestor = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            parent[i] = i;
            ancestor[i] = i;
        }
        this.visited = new boolean[n + 1];
        this.answers = new int[q];
    }

    public void addQuery(int u, int v, int queryIndex) {
        queries[u].add(new Query(v, queryIndex));
        queries[v].add(new Query(u, queryIndex));
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

    public int[] solve(int root) {
        dfsTarjan(root, 0);
        return answers;
    }

    private void dfsTarjan(int u, int p) {
        ancestor[u] = u;
        for (int v : adj[u]) {
            if (v != p) {
                dfsTarjan(v, u);
                union(v, u);
                ancestor[find(u)] = u;
            }
        }
        visited[u] = true;
        for (Query q : queries[u]) {
            if (visited[q.otherNode()]) {
                answers[q.queryIndex()] = ancestor[find(q.otherNode())];
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O((N + Q) \cdot \alpha(N)) \approx O(N + Q)$, nhanh hơn đáng kể so với Binary Lifting khi số truy vấn lớn.
- **Bộ nhớ:** $O(N + Q)$.

---

### Bài 17: LCA Bằng Tour Euler Kết Hợp Range Minimum Query (RMQ)

**Đề bài chi tiết:**
Hãy xây dựng cấu trúc dữ liệu tìm LCA trên cây $N$ đỉnh sao cho sau khi tiền xử lý, mỗi truy vấn $LCA(u, v)$ được trả lời trong thời gian **$O(1)$ tuyệt đối**.

**Phân tích thuật toán:**
- **Euler Tour & Độ sâu:** Duyệt DFS ghi lại dãy Euler Tour $E$ (thêm nút vào dãy mỗi khi thăm đến và mỗi khi từ con quay trở lại cha). Chiều dài mảng $E$ tối đa là $2N - 1$.
- Đồng thời ghi nhận mảng độ sâu tương ứng $D[i] = depth[E[i]]$ và vị trí xuất hiện đầu tiên $first[u]$ của mỗi đỉnh trong dãy $E$.
- **Tính chất RMQ:** $LCA(u, v)$ chính là đỉnh có độ sâu nhỏ nhất trên đoạn $E[first[u] \dots first[v]]$.
- Sử dụng **Sparse Table** trên mảng $D$ để tìm chỉ số có giá trị nhỏ nhất trong đoạn $[L, R]$ trong $O(1)$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution17 {
    private final int[] euler;
    private final int[] depthEuler;
    private final int[] first;
    private final int[][] st;
    private final int[] log2;
    private int tourSize = 0;

    @SuppressWarnings("unchecked")
    public Solution17(int n, List<Integer>[] adj, int root) {
        int maxTour = 2 * n;
        this.euler = new int[maxTour];
        this.depthEuler = new int[maxTour];
        this.first = new int[n + 1];
        Arrays.fill(first, -1);

        dfsEuler(root, 0, 0, adj);

        // Xây dựng bảng Sparse Table trên mảng depthEuler
        int m = tourSize;
        int maxLog = 32 - Integer.numberOfLeadingZeros(m);
        this.st = new int[m][maxLog];
        this.log2 = new int[m + 1];

        for (int i = 2; i <= m; i++) {
            log2[i] = log2[i / 2] + 1;
        }

        for (int i = 0; i < m; i++) {
            st[i][0] = i;
        }

        for (int j = 1; j < maxLog; j++) {
            for (int i = 0; i + (1 << j) <= m; i++) {
                int leftIdx = st[i][j - 1];
                int rightIdx = st[i + (1 << (j - 1))][j - 1];
                st[i][j] = (depthEuler[leftIdx] <= depthEuler[rightIdx]) ? leftIdx : rightIdx;
            }
        }
    }

    private void dfsEuler(int u, int p, int d, List<Integer>[] adj) {
        first[u] = tourSize;
        euler[tourSize] = u;
        depthEuler[tourSize] = d;
        tourSize++;

        for (int v : adj[u]) {
            if (v != p) {
                dfsEuler(v, u, d + 1, adj);
                euler[tourSize] = u;
                depthEuler[tourSize] = d;
                tourSize++;
            }
        }
    }

    public int getLCA(int u, int v) {
        int l = first[u];
        int r = first[v];
        if (l > r) { int t = l; l = r; r = t; }

        int len = r - l + 1;
        int k = log2[len];
        int leftIdx = st[l][k];
        int rightIdx = st[r - (1 << k) + 1][k];

        int bestIdx = (depthEuler[leftIdx] <= depthEuler[rightIdx]) ? leftIdx : rightIdx;
        return euler[bestIdx];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$ để xây dựng Sparse Table kích thước $2N \times \log(2N)$.
- **Truy vấn:** $O(1)$ thời gian thực thi (chỉ gồm vài phép tra cứu mảng và phép bit).
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 18: Tìm Hướng Đi Dạng Ký Tự 'U', 'L', 'R' Giữa Hai Nút

**Đề bài chi tiết:**
Cho gốc của một cây nhị phân và hai giá trị `startValue` và `destValue`. Tìm đường đi ngắn nhất từ nút `startValue` đến nút `destValue` dưới dạng chuỗi ký tự:
- `'U'`: đi từ con lên cha.
- `'L'`: đi từ cha sang con trái.
- `'R'`: đi từ cha sang con phải.

**Phân tích thuật toán:**
1. Tìm đường đi từ `root` đến `startValue` (dạng chuỗi `L`/`R`) và từ `root` đến `destValue`.
2. Tìm điểm phân nhánh (chính là LCA của hai nút): bỏ qua tiền tố chung dài nhất của hai chuỗi đường đi.
3. Phần còn lại của đường đi từ LCA đến `startValue` có độ dài $len$, ta biến đổi thành $len$ ký tự `'U'`.
4. Nối thêm phần còn lại của đường đi từ LCA đến `destValue`.

**Mã nguồn Java:**
```java
public class Solution18 {
    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;
        public TreeNode(int val) { this.val = val; }
    }

    public String getDirections(TreeNode root, int startValue, int destValue) {
        StringBuilder startPath = new StringBuilder();
        StringBuilder destPath = new StringBuilder();

        findPath(root, startValue, startPath);
        findPath(root, destValue, destPath);

        int i = 0;
        int minLen = Math.min(startPath.length(), destPath.length());
        while (i < minLen && startPath.charAt(i) == destPath.charAt(i)) {
            i++;
        }

        StringBuilder res = new StringBuilder();
        for (int k = i; k < startPath.length(); k++) {
            res.append('U');
        }
        res.append(destPath.substring(i));

        return res.toString();
    }

    private boolean findPath(TreeNode node, int target, StringBuilder path) {
        if (node == null) return false;
        if (node.val == target) return true;

        path.append('L');
        if (findPath(node.left, target, path)) return true;
        path.deleteCharAt(path.length() - 1);

        path.append('R');
        if (findPath(node.right, target, path)) return true;
        path.deleteCharAt(path.length() - 1);

        return false;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Bộ nhớ:** $O(H)$ không gian bộ nhớ chuỗi và đệ quy.

---

### Bài 19: Tìm Cây Con Nhỏ Nhất Chứa Toàn Bộ Nút Có Độ Sâu Lớn Nhất

**Đề bài chi tiết:**
Cho gốc cây nhị phân, hãy trả về gốc của cây con nhỏ nhất chứa tất cả các nút có độ sâu lớn nhất (Deepest Nodes).

**Phân tích thuật toán:**
Duyệt đệ quy Bottom-up trả về `(depth, lcaNode)`:
- Nếu cây con trái sâu hơn $\rightarrow$ kết quả thuộc cây con trái.
- Nếu cây con phải sâu hơn $\rightarrow$ kết quả thuộc cây con phải.
- Nếu cả 2 cây con có độ sâu bằng nhau $\rightarrow$ nút hiện tại chính là LCA của toàn bộ nút sâu nhất.

**Mã nguồn Java:**
```java
public class Solution19 {
    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;
        public TreeNode(int val) { this.val = val; }
    }

    private record NodeDepth(TreeNode node, int depth) {}

    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        return helper(root).node();
    }

    private NodeDepth helper(TreeNode root) {
        if (root == null) return new NodeDepth(null, 0);

        NodeDepth left = helper(root.left);
        NodeDepth right = helper(root.right);

        if (left.depth() == right.depth()) {
            return new NodeDepth(root, left.depth() + 1);
        } else if (left.depth() > right.depth()) {
            return new NodeDepth(left.node(), left.depth() + 1);
        } else {
            return new NodeDepth(right.node(), right.depth() + 1);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N)$.
- **Bộ nhớ:** $O(H)$.

---

### Bài 20: Tìm Nút Cách Nút Đích Khoảng Cách K trên Cây Tổng Quát

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh. Với mỗi truy vấn gồm nút đích `target` và khoảng cách `k`, hãy tìm tổ tiên của `target` cách nó đúng $k$ cạnh. Nếu khoảng cách vượt quá gốc, trả về `-1`.

**Phân tích thuật toán:**
Sử dụng phương pháp Binary Lifting:
- Phân tích $k$ thành tổng các luỹ thừa của 2.
- Nhảy liên tiếp qua các bước $2^i$.

**Mã nguồn Java:**
```java
public class Solution20 {
    private final int[][] up;
    private final int log;
    private final int[] depth;

    public Solution20(int n, int[][] upTable, int[] depthTable, int maxLog) {
        this.up = upTable;
        this.depth = depthTable;
        this.log = maxLog;
    }

    public int getNodeAtDistanceKUp(int target, int k) {
        if (depth[target] < k) return -1;
        int curr = target;
        for (int i = 0; i < log; i++) {
            if (((k >> i) & 1) == 1) {
                curr = up[curr][i];
                if (curr == 0) return -1;
            }
        }
        return curr;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(\log K)$.
- **Bộ nhớ:** $O(1)$ phụ trợ.

---

### Bài 21: Tính Tổng Trọng Số Đường Đi Giữa Hai Đỉnh $(U, V)$

**Đề bài chi tiết:**
Cho cây $N$ đỉnh có trọng số trên đỉnh $val[i]$. Xử lý truy vấn tính tổng giá trị các đỉnh trên đường đi từ $u$ đến $v$.

**Phân tích thuật toán:**
- Gọi $sumToRoot[u]$ là tổng giá trị các đỉnh trên đường đi từ $root$ đến $u$.
- Tính chất đường đi:
  $$pathSum(u, v) = sumToRoot[u] + sumToRoot[v] - sumToRoot[LCA(u, v)] - sumToRoot[parent[LCA(u, v)]]$$
- Sử dụng Binary Lifting để tính LCA trong $O(\log N)$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution21 {
    private final int log;
    private final int[][] up;
    private final int[] depth;
    private final long[] sumToRoot;
    private final int[] nodeVal;

    @SuppressWarnings("unchecked")
    public Solution21(int n, List<Integer>[] adj, int[] values, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.sumToRoot = new long[n + 1];
        this.nodeVal = values;

        dfs(root, 0, 0, values[root], adj);
    }

    private void dfs(int u, int p, int d, long currSum, List<Integer>[] adj) {
        depth[u] = d;
        sumToRoot[u] = currSum;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = (up[u][k - 1] != 0) ? up[up[u][k - 1]][k - 1] : 0;
        }
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1, currSum + nodeVal[v], adj);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public long queryPathSum(int u, int v) {
        int lca = getLCA(u, v);
        long ans = sumToRoot[u] + sumToRoot[v] - sumToRoot[lca];
        int pLca = up[lca][0];
        if (pLca != 0) {
            ans -= sumToRoot[pLca];
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 22: Tìm Nút Thứ K trên Đường Đi Đơn từ U đến V

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh. Cho các truy vấn $(u, v, k)$, hãy tìm nút thứ $k$ trên đường đi đơn từ đỉnh $u$ đến đỉnh $v$ (quy ước nút thứ 1 là $u$, nút thứ 2 là nút kế tiếp,...). Nếu đường đi có ít hơn $k$ nút, trả về `-1`.

**Phân tích thuật toán:**
1. Tìm $LCA(u, v)$.
2. Độ dài đoạn từ $u$ lên $LCA$ là $d_1 = depth[u] - depth[LCA] + 1$ (tính cả $u$ và $LCA$).
3. Nếu $k \le d_1$: Nút cần tìm nằm trên nhánh từ $u$ lên $LCA$, chính là tổ tiên thứ $(k - 1)$ của $u$.
4. Nếu $k > d_1$: Nút cần tìm nằm trên nhánh từ $LCA$ xuống $v$.
   - Tổng số nút trên toàn bộ đường đi là $totalNodes = depth[u] + depth[v] - 2 \cdot depth[LCA] + 1$.
   - Nếu $k > totalNodes$, trả về `-1`.
   - Vị trí tính từ $v$ ngược lên là $k' = totalNodes - k$, do đó nút cần tìm là tổ tiên thứ $k'$ của $v$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution22 {
    private final int log;
    private final int[][] up;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution22(int n, List<Integer>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        dfs(root, 0, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = (up[u][k - 1] != 0) ? up[up[u][k - 1]][k - 1] : 0;
        }
        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
    }

    private int getKthAncestor(int node, int k) {
        for (int i = 0; i < log; i++) {
            if (((k >> i) & 1) == 1) {
                node = up[node][i];
                if (node == 0) return -1;
            }
        }
        return node;
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public int getKthNodeOnPath(int u, int v, int k) {
        int lca = getLCA(u, v);
        int d1 = depth[u] - depth[lca] + 1;

        if (k <= d1) {
            return getKthAncestor(u, k - 1);
        }

        int totalNodes = depth[u] + depth[v] - 2 * depth[lca] + 1;
        if (k > totalNodes) return -1;

        int stepsFromV = totalNodes - k;
        return getKthAncestor(v, stepsFromV);
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 23: Tính Ước Chung Lớn Nhất (GCD) trên Đường Đi Giữa Hai Nút

**Đề bài chi tiết:**
Mỗi đỉnh $u$ của cây $N$ đỉnh mang một giá trị nguyên dương $A[u]$. Xử lý $Q$ truy vấn: với mỗi cặp $(u, v)$, hãy tính $\gcd$ của tất cả các giá trị đỉnh trên đường đi đơn từ $u$ đến $v$.

**Phân tích thuật toán:**
Phép toán $\gcd$ có tính chất kết hợp ($a \gcd (b \gcd c) = (a \gcd b) \gcd c$).
Xây dựng bảng $gcdTable[u][k]$: $\gcd$ các đỉnh từ $u$ lên tổ tiên thứ $2^k$:
$$gcdTable[u][k] = \gcd(gcdTable[u][k-1],\, gcdTable[up[u][k-1]][k-1])$$
Khi truy vấn, nhảy nhị phân và liên tục gộp $\gcd$ trong $O(\log N \cdot \log(\max A))$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution23 {
    private final int log;
    private final int[][] up;
    private final long[][] gcdTable;
    private final int[] depth;
    private final long[] val;

    @SuppressWarnings("unchecked")
    public Solution23(int n, List<Integer>[] adj, long[] values, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.gcdTable = new long[n + 1][log];
        this.depth = new int[n + 1];
        this.val = values;

        dfs(root, 0, 0, adj);
    }

    private long gcd(long a, long b) {
        while (b != 0) {
            long t = a % b;
            a = b;
            b = t;
        }
        return a;
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        depth[u] = d;
        up[u][0] = p;
        gcdTable[u][0] = val[u];

        for (int k = 1; k < log; k++) {
            int anc = up[u][k - 1];
            up[u][k] = (anc != 0) ? up[anc][k - 1] : 0;
            if (anc != 0) {
                gcdTable[u][k] = gcd(gcdTable[u][k - 1], gcdTable[anc][k - 1]);
            } else {
                gcdTable[u][k] = gcdTable[u][k - 1];
            }
        }

        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
    }

    public long queryPathGCD(int u, int v) {
        long currentGcd = 0;
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }

        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) {
                currentGcd = gcd(currentGcd, gcdTable[u][k]);
                u = up[u][k];
            }
        }

        if (u == v) {
            return gcd(currentGcd, val[u]);
        }

        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                currentGcd = gcd(currentGcd, gcdTable[u][k]);
                currentGcd = gcd(currentGcd, gcdTable[v][k]);
                u = up[u][k];
                v = up[v][k];
            }
        }

        currentGcd = gcd(currentGcd, gcdTable[u][0]);
        currentGcd = gcd(currentGcd, gcdTable[v][0]);
        currentGcd = gcd(currentGcd, val[up[u][0]]); // Gốc LCA

        return currentGcd;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N \log(\max A))$.
- **Truy vấn:** $O(\log N \log(\max A))$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 24: Đếm Cạnh Trọng Yếu (Bridges on Paths / Network Bottlenecks)

**Đề bài chi tiết:**
Cho mạng lưới giao thông hình cây $N$ đỉnh. Có $M$ tuyến xe bus chạy, tuyến thứ $i$ chạy giữa $u_i$ và $v_i$. Một cạnh được gọi là "trọng yếu" nếu có ít nhất $K$ tuyến xe bus đi qua nó. Hãy đếm số lượng cạnh trọng yếu trên cây.

**Phân tích thuật toán:**
- Đây là bài toán ứng dụng trực tiếp của **Mảng hiệu trên cạnh** (Bài 15).
- Với mỗi tuyến xe bus $(u_i, v_i)$, thực hiện cập nhật mảng hiệu:
  $$diff[u_i] \mathrel{+}= 1, \quad diff[v_i] \mathrel{+}= 1, \quad diff[LCA(u_i, v_i)] \mathrel{-}= 2$$
- DFS tích luỹ mảng hiệu. Đếm số đỉnh $v \ne root$ có tổng tích luỹ $\ge K$.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution24 {
    private final Solution15 edgeDiffHelper;
    private final int kThreshold;
    private final int numEdges;

    @SuppressWarnings("unchecked")
    public Solution24(int n, List<int[]>[] adj, int root, int numEdges, int kThreshold) {
        this.numEdges = numEdges;
        this.kThreshold = kThreshold;
        this.edgeDiffHelper = new Solution15(n, adj, root, numEdges);
    }

    public void addBusRoute(int u, int v) {
        edgeDiffHelper.addPathEdge(u, v, 1);
    }

    public int countCriticalEdges(int root) {
        long[] edgeCounts = edgeDiffHelper.computeAllEdges(root);
        int criticalCount = 0;
        for (int i = 1; i <= numEdges; i++) {
            if (edgeCounts[i] >= kThreshold) {
                criticalCount++;
            }
        }
        return criticalCount;
    }
}
```

**Độ phức tạp:**
- **Thời gian:** $O(N \log N + M \log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 25: Xây Dựng Cây Ảo (Virtual Tree / Auxiliary Tree) với LCA

**Đề bài chi tiết:**
Cho một cây lớn $N$ đỉnh ($N \le 10^5$). Cần xử lý nhiều truy vấn, mỗi truy vấn cho một tập con gồm $K$ đỉnh quan trọng $S = \{v_1, v_2, \dots, v_K\}$ ($\sum K \le 10^5$). Hãy xây dựng cây ảo (Virtual Tree) chỉ chứa các đỉnh trong $S$ và các LCA của chúng sao cho cấu trúc quan hệ tổ tiên và trọng số khoảng cách được bảo toàn, với tổng thời gian $O(K \log K)$.

**Phân tích thuật toán:**
- Kỹ thuật **Virtual Tree**:
  1. Sắp xếp các đỉnh trong $S$ theo thứ tự thăm $tin[u]$.
  2. Bổ sung các LCA của các cặp đỉnh kề nhau vào tập đỉnh.
  3. Dùng một `Monotonic Stack` để lưu trữ chuỗi các đỉnh trên nhánh đang xét:
     - Khi xét đỉnh $u$, chừng nào $u$ không nằm trong cây con của đỉnh đầu stack ($!isAncestor(stack.top, u)$), ta pop đỉnh đầu stack ra và tạo cạnh nối tới đỉnh bên dưới nó.
     - Đẩy $u$ vào stack.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution25 {
    public record VEdge(int to, int weight) {}

    private final int log;
    private final int[][] up;
    private final int[] depth;
    private final int[] tin;
    private final int[] tout;
    private int timer = 0;

    @SuppressWarnings("unchecked")
    public Solution25(int n, List<Integer>[] adj, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.tin = new int[n + 1];
        this.tout = new int[n + 1];

        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        tin[u] = ++timer;
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
        tout[u] = ++timer;
    }

    public boolean isAncestor(int u, int v) {
        return tin[u] <= tin[v] && tout[u] >= tout[v];
    }

    public int getLCA(int u, int v) {
        if (isAncestor(u, v)) return u;
        if (isAncestor(v, u)) return v;
        for (int k = log - 1; k >= 0; k--) {
            if (!isAncestor(up[u][k], v)) {
                u = up[u][k];
            }
        }
        return up[u][0];
    }

    @SuppressWarnings("unchecked")
    public Map<Integer, List<VEdge>> buildVirtualTree(List<Integer> keyNodes) {
        List<Integer> nodes = new ArrayList<>(keyNodes);
        nodes.sort(Comparator.comparingInt(u -> tin[u]));

        int originalSize = nodes.size();
        for (int i = 0; i < originalSize - 1; i++) {
            nodes.add(getLCA(nodes.get(i), nodes.get(i + 1)));
        }

        nodes.sort(Comparator.comparingInt(u -> tin[u]));
        List<Integer> uniqueNodes = new ArrayList<>();
        for (int u : nodes) {
            if (uniqueNodes.isEmpty() || !uniqueNodes.get(uniqueNodes.size() - 1).equals(u)) {
                uniqueNodes.add(u);
            }
        }

        Map<Integer, List<VEdge>> vTree = new HashMap<>();
        for (int u : uniqueNodes) vTree.put(u, new ArrayList<>());

        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(uniqueNodes.get(0));

        for (int i = 1; i < uniqueNodes.size(); i++) {
            int u = uniqueNodes.get(i);
            while (!isAncestor(stack.peek(), u)) {
                stack.pop();
            }
            int p = stack.peek();
            int dist = depth[u] - depth[p];
            vTree.get(p).add(new VEdge(u, dist));
            stack.push(u);
        }

        return vTree;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý toàn cục:** $O(N \log N)$.
- **Xây dựng Virtual Tree:** $O(K \log K)$ với $K = |keyNodes|$.
- **Bộ nhớ:** $O(K)$ cho cây ảo.

---

### Bài 26: Heavy-Light Decomposition (HLD) Kết Hợp Phân Đoạn LCA

**Đề bài chi tiết:**
Hiện thực thuật toán Phân rã cây Nặng - Nhẹ (Heavy-Light Decomposition - HLD) cho phép tìm $LCA(u, v)$ và hỗ trợ cập nhật động giá trị đỉnh kèm truy vấn $\max$ trên đường đi trong thời gian $O(\log^2 N)$ hoặc $O(\log N)$.

**Phân tích thuật toán:**
- Phân rã cây thành các chuỗi nặng (Heavy Chains). Mỗi chuỗi có một đỉnh đầu `head[u]`.
- Tìm LCA thông qua HLD:
  - Chừng nào `head[u] != head[v]`: đỉnh có `head` sâu hơn sẽ nhảy lên cha của `head` đó: $u = parent[head[u]]$.
  - Khi `head[u] == head[v]`: hai đỉnh cùng thuộc một chuỗi nặng, đỉnh nào có độ sâu nhỏ hơn chính là LCA ($depth[u] < depth[v] ? u : v$).
- Đây là phương pháp tìm LCA cực nhanh trong thực tế và tích hợp hoàn hảo với Segment Tree.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution26 {
    private final int n;
    private final List<Integer>[] adj;
    private final int[] parent;
    private final int[] depth;
    private final int[] heavy;
    private final int[] head;
    private final int[] pos;
    private int curPos = 0;

    @SuppressWarnings("unchecked")
    public Solution26(int n, List<Integer>[] adj, int root) {
        this.n = n;
        this.adj = adj;
        this.parent = new int[n + 1];
        this.depth = new int[n + 1];
        this.heavy = new int[n + 1];
        this.head = new int[n + 1];
        this.pos = new int[n + 1];
        Arrays.fill(heavy, -1);

        dfsSize(root, 0, 0);
        dfsDecompose(root, root);
    }

    private int dfsSize(int u, int p, int d) {
        int size = 1;
        int maxChildSize = 0;
        depth[u] = d;
        parent[u] = p;

        for (int v : adj[u]) {
            if (v != p) {
                int childSize = dfsSize(v, u, d + 1);
                size += childSize;
                if (childSize > maxChildSize) {
                    maxChildSize = childSize;
                    heavy[u] = v;
                }
            }
        }
        return size;
    }

    private void dfsDecompose(int u, int h) {
        head[u] = h;
        pos[u] = ++curPos;
        if (heavy[u] != -1) {
            dfsDecompose(heavy[u], h);
        }
        for (int v : adj[u]) {
            if (v != parent[u] && v != heavy[u]) {
                dfsDecompose(v, v);
            }
        }
    }

    public int getLCA(int u, int v) {
        while (head[u] != head[v]) {
            if (depth[head[u]] > depth[head[v]]) {
                u = parent[head[u]];
            } else {
                v = parent[head[v]];
            }
        }
        return (depth[u] < depth[v]) ? u : v;
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N)$.
- **Truy vấn LCA:** $O(\log N)$ số lần nhảy chuỗi.
- **Bộ nhớ:** $O(N)$ (tiết kiệm bộ nhớ hơn Binary Lifting $O(N \log N)$).

---

### Bài 27: Tìm Nút Trung Điểm Trên Đường Đi $(U, V)$

**Đề bài chi tiết:**
Cho một cây $N$ đỉnh. Với mỗi truy vấn $(u, v)$, hãy tìm đỉnh nằm chính giữa đường đi đơn từ $u$ đến $v$. Nếu số lượng đỉnh trên đường đi là chẵn (không có tâm chính xác), trả về `-1`.

**Phân tích thuật toán:**
1. Tính khoảng cách giữa $u$ và $v$: $L = dist(u, v) = depth[u] + depth[v] - 2 \cdot depth[LCA(u, v)]$.
2. Số cạnh trên đường đi là $L$, số đỉnh là $L + 1$.
   - Nếu $L$ là số lẻ $\implies L + 1$ chẵn $\implies$ không có đỉnh chính giữa duy nhất $\rightarrow$ trả về `-1`.
3. Nếu $L$ chẵn $\implies$ đỉnh trung điểm là đỉnh thứ $(L / 2 + 1)$ trên đường đi từ $u$ đến $v$.
4. Tận dụng hàm tìm đỉnh thứ $K$ trên đường đi (Bài 22).

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution27 {
    private final Solution22 pathKthFinder;
    private final int[] depth;

    @SuppressWarnings("unchecked")
    public Solution27(int n, List<Integer>[] adj, int root) {
        this.pathKthFinder = new Solution22(n, adj, root);
        this.depth = new int[n + 1];
        dfsDepth(root, 0, 0, adj);
    }

    private void dfsDepth(int u, int p, int d, List<Integer>[] adj) {
        depth[u] = d;
        for (int v : adj[u]) {
            if (v != p) dfsDepth(v, u, d + 1, adj);
        }
    }

    public int findPathCenter(int u, int v) {
        int lca = pathKthFinder.getLCA(u, v);
        int totalEdges = depth[u] + depth[v] - 2 * depth[lca];
        if (totalEdges % 2 != 0) {
            return -1; // Tổng số đỉnh là totalEdges + 1 (chẵn), không có đỉnh chính giữa
        }
        int midIndex = totalEdges / 2 + 1;
        return pathKthFinder.getKthNodeOnPath(u, v, midIndex);
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 28: Truy Vấn LCA trong Rừng Động (Dynamic Forest)

**Đề bài chi tiết:**
Cho $N$ đỉnh ban đầu rời rạc (không có cạnh). Hỗ trợ 2 loại thao tác trực tuyến:
1. `addEdge(u, v)`: Thêm một cạnh vô hướng giữa $u$ và $v$ (đảm bảo không tạo chu trình).
2. `getLCA(u, v)`: Trả về LCA của $u$ và $v$ nếu chúng cùng thuộc một cây và chọn gốc là đỉnh đại diện của thành phần liên thông, nếu không cùng cây trả về `-1`.

**Phân tích thuật toán:**
- Sử dụng DSU để quản lý các thành phần liên thông.
- Khi thêm cạnh giữa 2 cây, ta thực hiện kỹ thuật **Re-rooting (Đổi gốc cây con)** cho cây có kích thước nhỏ hơn (`Small-to-Large merging`) và gắn vào cây lớn hơn.
- Cập nhật bảng Binary Lifting $up[u][k]$ và $depth[u]$ cho cây con được sáp nhập.

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution28 {
    private final int n;
    private final int log;
    private final List<Integer>[] adj;
    private final int[][] up;
    private final int[] depth;
    private final int[] parentDsu;
    private final int[] size;

    @SuppressWarnings("unchecked")
    public Solution28(int n) {
        this.n = n;
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.adj = new List[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();

        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.parentDsu = new int[n + 1];
        this.size = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            parentDsu[i] = i;
            size[i] = 1;
            up[i][0] = i;
        }
    }

    public int find(int i) {
        if (parentDsu[i] == i) return i;
        return parentDsu[i] = find(parentDsu[i]);
    }

    public void addEdge(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU == rootV) return;

        // Small to large merging
        if (size[rootU] < size[rootV]) {
            int t = u; u = v; v = t;
            t = rootU; rootU = rootV; rootV = t;
        }

        adj[u].add(v);
        adj[v].add(u);
        parentDsu[rootV] = rootU;
        size[rootU] += size[rootV];

        // Re-root toàn bộ cây con chứa v và coi u là cha của v
        rebuildSubtree(v, u, depth[u] + 1);
    }

    private void rebuildSubtree(int u, int p, int d) {
        depth[u] = d;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int next : adj[u]) {
            if (next != p) {
                rebuildSubtree(next, u, d + 1);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (find(u) != find(v)) return -1;
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }
}
```

**Độ phức tạp:**
- **Thêm cạnh:** $O(S \log N)$ với $S$ là kích thước cây nhỏ hơn (tổng thời gian trên $N$ phép hợp nhất là $O(N \log^2 N)$ nhờ Small-to-Large).
- **Truy vấn LCA:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 29: Truy Vấn XOR Giá Trị Đỉnh Trên Đường Đi $(U, V)$

**Đề bài chi tiết:**
Mỗi đỉnh $u$ trên cây có một giá trị $val[u]$. Với mỗi truy vấn $(u, v)$, hãy tính tổng XOR của tất cả các đỉnh trên đường đi đơn nối giữa $u$ và $v$.

**Phân tích thuật toán:**
- Phép toán XOR có tính chất tự triệt tiêu: $x \oplus x = 0$.
- Gọi $xorToRoot[u]$ là XOR của các giá trị đỉnh từ gốc đến $u$.
- Khi tính $xorToRoot[u] \oplus xorToRoot[v]$:
  - Các đỉnh nằm trên đoạn từ gốc đến $parent[LCA(u, v)]$ xuất hiện 2 lần $\implies$ triệt tiêu thành 0.
  - Đỉnh $LCA(u, v)$ xuất hiện 2 lần nên cũng bị triệt tiêu!
- Do đó ta cần XOR thêm giá trị của chính đỉnh $LCA(u, v)$:
  $$pathXor(u, v) = xorToRoot[u] \oplus xorToRoot[v] \oplus val[LCA(u, v)]$$

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution29 {
    private final int log;
    private final int[][] up;
    private final int[] depth;
    private final int[] xorToRoot;
    private final int[] val;

    @SuppressWarnings("unchecked")
    public Solution29(int n, List<Integer>[] adj, int[] values, int root) {
        this.log = 32 - Integer.numberOfLeadingZeros(n);
        this.up = new int[n + 1][log];
        this.depth = new int[n + 1];
        this.xorToRoot = new int[n + 1];
        this.val = values;

        dfs(root, root, 0, values[root], adj);
    }

    private void dfs(int u, int p, int d, int currentXor, List<Integer>[] adj) {
        depth[u] = d;
        xorToRoot[u] = currentXor;
        up[u][0] = p;
        for (int k = 1; k < log; k++) {
            up[u][k] = up[up[u][k - 1]][k - 1];
        }
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1, currentXor ^ val[v], adj);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) { int t = u; u = v; v = t; }
        for (int k = log - 1; k >= 0; k--) {
            if (depth[u] - (1 << k) >= depth[v]) u = up[u][k];
        }
        if (u == v) return u;
        for (int k = log - 1; k >= 0; k--) {
            if (up[u][k] != up[v][k]) {
                u = up[u][k];
                v = up[v][k];
            }
        }
        return up[u][0];
    }

    public int queryPathXor(int u, int v) {
        int lca = getLCA(u, v);
        return xorToRoot[u] ^ xorToRoot[v] ^ val[lca];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$.
- **Mỗi truy vấn:** $O(\log N)$.
- **Bộ nhớ:** $O(N \log N)$.

---

### Bài 30: Tìm Khoảng Cách Ngắn Nhất Từ Đỉnh U Đến Bất Kỳ Đỉnh Nào Trong Tập S

**Đề bài chi tiết:**
Cho cây $N$ đỉnh có trọng số cạnh. Cho tập $S$ gồm $K$ đỉnh đặc biệt. Với mỗi đỉnh truy vấn $u$, tìm $\min_{s \in S} dist(u, s)$. Yêu cầu giải thuật hiệu quả khi có nhiều truy vấn.

**Phân tích thuật toán:**
- **Phương pháp Multi-Source BFS / Tree DP:**
  1. Khởi tạo hàng đợi ưu tiên (Dijkstra) hoặc chạy BFS nhiều nguồn (nếu không trọng số) với khoảng cách khởi tạo $dist[s] = 0$ cho mọi $s \in S$.
  2. Lan truyền khoảng cách ngắn nhất từ tập $S$ ra toàn bộ $N$ đỉnh của cây trong thời gian $O(N \log N)$ hoặc $O(N)$.
  3. Sau khi tiền xử lý, mỗi truy vấn $u$ được trả lời trong $O(1)$ bằng cách đọc mảng $dist[u]$!

**Mã nguồn Java:**
```java
import java.util.*;

public class Solution30 {
    public record Edge(int to, long weight) {}
    public record State(int u, long dist) implements Comparable<State> {
        @Override
        public int compareTo(State o) {
            return Long.compare(this.dist, o.dist);
        }
    }

    private final int n;
    private final List<Edge>[] adj;
    private final long[] minDistToSet;

    @SuppressWarnings("unchecked")
    public Solution30(int n, List<Edge>[] adj, List<Integer> specialSet) {
        this.n = n;
        this.adj = adj;
        this.minDistToSet = new long[n + 1];
        Arrays.fill(minDistToSet, Long.MAX_VALUE);

        multiSourceDijkstra(specialSet);
    }

    private void multiSourceDijkstra(List<Integer> specialSet) {
        PriorityQueue<State> pq = new PriorityQueue<>();
        for (int s : specialSet) {
            minDistToSet[s] = 0;
            pq.offer(new State(s, 0));
        }

        while (!pq.isEmpty()) {
            State curr = pq.poll();
            int u = curr.u();
            long d = curr.dist();

            if (d > minDistToSet[u]) continue;

            for (Edge e : adj[u]) {
                if (minDistToSet[e.to()] > d + e.weight()) {
                    minDistToSet[e.to()] = d + e.weight();
                    pq.offer(new State(e.to(), minDistToSet[e.to()]));
                }
            }
        }
    }

    public long getMinDistanceToSet(int u) {
        return minDistToSet[u];
    }
}
```

**Độ phức tạp:**
- **Tiền xử lý:** $O(N \log N)$ (Multi-source Dijkstra trên cây).
- **Mỗi truy vấn:** $O(1)$ đọc kết quả từ bảng đã tính.
- **Bộ nhớ:** $O(N)$.

---

## Bảng Tổng Kết So Sánh Các Kỹ Thuật Giải LCA

| Kỹ Thuật | Thời Gian Tiền Xử Lý | Thời Gian Truy Vấn | Không Gian Bộ Nhớ | Ưu Điểm Chính | Nhược Điểm / Ngữ Cảnh Phù Hợp |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Đệ Quy Classic** | $O(1)$ | $O(N)$ | $O(H)$ | Dễ cài đặt, không cần chuẩn bị | Chậm khi có nhiều truy vấn trên cây lớn |
| **Binary Lifting** | $O(N \log N)$ | $O(\log N)$ | $O(N \log N)$ | Đa năng, dễ mở rộng tính Max/Min/Sum/GCD | Tốn bộ nhớ hơn HLD |
| **Tarjan DSU** | $O(N + Q)$ | $O(1)$ khấu hao | $O(N + Q)$ | Thời gian tuyến tính cực nhanh | Bắt buộc phải biết trước toàn bộ truy vấn (Offline) |
| **Euler Tour + RMQ** | $O(N \log N)$ | $O(1)$ tuyệt đối | $O(N \log N)$ | Truy vấn $O(1)$ siêu tốc | Khó mở rộng để cập nhật động cấu trúc cây |
| **Heavy-Light (HLD)** | $O(N)$ | $O(\log N)$ | $O(N)$ | Tối ưu bộ nhớ, hỗ trợ cập nhật cây động | Cài đặt tương đối dài |
| **Tree Diff Array** | $O(N \log N)$ | $O(1)$ cập nhật | $O(N \log N)$ | Cập nhật $M$ đoạn đường đi đồng loạt | Chỉ xuất kết quả sau khi hoàn thành tất cả cập nhật |
