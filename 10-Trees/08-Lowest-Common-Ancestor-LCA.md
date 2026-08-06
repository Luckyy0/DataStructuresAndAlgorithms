# Thuật Toán Tìm Tổ Tiên Chung Thấp Nhất (Lowest Common Ancestor - LCA)

---

## 1. Metadata

- **Document ID:** DSA-10-08
- **Version:** 1.0
- **Prerequisites:** 
  - Graph Theory Basics (Adjacency List, Trees, Directed/Undirected Graphs)
  - Depth-First Search (DFS) & Breadth-First Search (BFS) Traversal
  - Disjoint Set Union (DSU / Union-Find)
  - Dynamic Programming (DP) & Bit Manipulation
  - Range Minimum Query (RMQ) & Sparse Table
- **Learning Objectives:**
  - Nắm vững định nghĩa toán học và các tính chất cơ bản của Lowest Common Ancestor (LCA).
  - Làm chủ 4 phương pháp kinh điển: Naive Parent Pointer, Binary Lifting ($O(N \log N)$ prep, $O(\log N)$ query), Euler Tour + RMQ Sparse Table ($O(N \log N)$ prep, $O(1)$ query), và Tarjan's Offline LCA ($O(N + Q)$).
  - Hiểu sâu cấu trúc bộ nhớ JVM (Cache Locality, Flattened 1D Array vs 2D Jagged Array, Call Stack Recursion).
  - Ánh xạ LCA vào các hệ thống thực tế: `git merge-base`, Java HotSpot C2 Compiler Dominator Tree, Network Routing, DOM Engine.
  - Xử lý triệt để 20 lỗi phổ biến (Common Bugs) và 30 trường hợp biên (Edge Cases).
- **Estimated Reading Time:** 45 - 60 phút
- **Difficulty:** Advanced
- **Keywords:** Lowest Common Ancestor, LCA, Binary Lifting, Euler Tour Technique, RMQ, Sparse Table, Tarjan Offline DSU, K-th Ancestor, Tree Distance, Dominator Tree, Git Merge Base.

---

## 2. Purpose (Mục Đích)

Trong cấu trúc dữ liệu cây (Tree Data Structure), bài toán tìm **Lowest Common Ancestor (LCA)** của hai nút $u$ và $v$ là một trong những bài toán nền tảng và có tần suất ứng dụng cao nhất trong khoa học máy tính lý thuyết lẫn kỹ thuật phần mềm thực chiến.

Tài liệu này cung cấp cái nhìn toàn diện từ lý thuyết toán học hình thức, phân tích thuật toán, tối ưu hóa mức hệ thống/JVM, cho đến cài đặt mã nguồn Java 21 chuẩn công nghiệp (Production-Grade). Tài liệu được biên soạn nhằm phục vụ cả việc giải quyết các bài toán Competitive Programming hóc búa lẫn việc thiết kế các hệ thống xử lý phân cấp phức tạp trong môi trường doanh nghiệp.

---

## 3. Motivation (Động Lực Thực Tiễn)

Tại sao LCA lại đóng vai trò tối quan trọng?
1. **Truy vấn khoảng cách trên cây (Tree Distance Queries):** Khoảng cách giữa hai nút bất kỳ $u$ và $v$ trên cây có trọng số hoặc không trọng số được tính thông qua LCA trong $O(1)$ sau khi tiền xử lý:
   $$\text{dist}(u, v) = \text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[\text{LCA}(u, v)]$$
2. **Truy vấn đường đi trên cây (Tree Path Queries):** Mọi đường đi đơn giữa $u$ và $v$ đều có thể phân rã thành hai đoạn đường đi có hướng lên gốc: $u \to \text{LCA}(u, v)$ và $\text{LCA}(u, v) \to v$. Điều này cho phép áp dụng Prefix Sum, Segment Tree, hoặc Fenwick Tree trên cây.
3. **Kiểm soát phiên bản phân tán (Distributed Version Control - Git):** Khi thực hiện lệnh `git merge branchA branchB`, Git phải xác định điểm rẽ nhánh chung gần nhất (**merge-base**) của hai commit DAG, vốn là bài toán mở rộng của LCA trên đồ thị không chu trình có hướng (DAG).
4. **Trình biên dịch và Phân tích phân cấp (Compiler & Static Analysis):** Xây dựng **Dominator Tree** trong HotSpot JVM C2 Compiler để tối ưu hóa mã trung gian (IR), phát hiện vòng lặp (Loop Invariant Code Motion), và xác định Common Supertype trong Bytecode Verifier.
5. **Định tuyến mạng phân cấp (Hierarchical Network Routing):** Xác định Router biên chung nhỏ nhất để chuyển tiếp gói tin với độ trễ thấp nhất trong mạng Spanning Tree.

---

## 4. Mathematical Foundation (Cơ Sở Toán Học)

### 4.1. Định nghĩa hình thức (Formal Definition)

Cho một cây có gốc (Rooted Tree) $T = (V, E)$ với tập đỉnh $V$, tập cạnh $E$ ($|V| = N, |E| = N - 1$), và một nút gốc được chỉ định $r \in V$.

- **Ancestor (Tổ tiên):** Nút $x$ được gọi là tổ tiên của $u$ ($x \in \text{Anc}(u)$) nếu $x$ nằm trên đường đi đơn duy nhất từ gốc $r$ đến $u$. Theo quy ước: $u \in \text{Anc}(u)$ (mỗi nút là tổ tiên của chính nó).
- **Depth (Độ sâu):** $\text{depth}[u]$ là số lượng cạnh trên đường đi đơn từ gốc $r$ đến $u$. $\text{depth}[r] = 0$.
- **Lowest Common Ancestor (LCA):** Với hai nút bất kỳ $u, v \in V$, $\text{LCA}(u, v)$ là nút $w \in V$ thỏa mãn:
  1. $w \in \text{Anc}(u) \cap \text{Anc}(v)$ ($w$ là tổ tiên chung của $u$ và $v$).
  2. $\forall x \in \text{Anc}(u) \cap \text{Anc}(v)$, ta có $\text{depth}[x] \le \text{depth}[w]$ (nghĩa là $w$ có độ sâu lớn nhất trong tất cả các tổ tiên chung, tức là $w$ xa gốc $r$ nhất và gần $u, v$ nhất).

```
        r (Root)
       / \
      a   b
     / \   \
    u   w   v
       /
      k
```
Trong sơ đồ trên, $\text{Anc}(u) = \{r, a, u\}$, $\text{Anc}(k) = \{r, a, w, k\}$.
Giao của tập tổ tiên: $\text{Anc}(u) \cap \text{Anc}(k) = \{r, a\}$.
Tổ tiên có độ sâu lớn nhất là $a$ ($\text{depth}[a] = 1 > \text{depth}[r] = 0$), do đó $\text{LCA}(u, k) = a$.

---

### 4.2. Các tính chất đại số và đồ thị (Algebraic & Graph Properties)

1. **Giao hoán (Commutativity):**
   $$\text{LCA}(u, v) = \text{LCA}(v, u)$$
2. **Lũy đẳng (Idempotence):**
   $$\text{LCA}(u, u) = u$$
3. **Kết hợp (Associativity):**
   $$\text{LCA}(u, \text{LCA}(v, w)) = \text{LCA}(\text{LCA}(u, v), w)$$
   *Hệ quả:* Cho phép tìm LCA của một tập hợp nhiều nút $S \subseteq V$ bằng cách gộp liên tiếp: $\text{LCA}(u_1, u_2, \dots, u_k) = \text{LCA}(u_1, \text{LCA}(u_2, \dots, u_k))$.
4. **Quan hệ cha con trực tiếp (Ancestor Subsumption):**
   $$u \in \text{Anc}(v) \iff \text{LCA}(u, v) = u$$
5. **Công thức khoảng cách (Tree Distance Metric):**
   Với cây không trọng số:
   $$\text{dist}(u, v) = \text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[\text{LCA}(u, v)]$$
   Với cây có trọng số (hàm khoảng cách $d(u)$ từ gốc $r$ đến $u$):
   $$\text{dist}_W(u, v) = d(u) + d(v) - 2 \cdot d(\text{LCA}(u, v))$$
6. **Thời gian duyệt DFS (Eulerian In/Out Time Property):**
   Gọi $\text{tin}[u]$ và $\text{tout}[u]$ lần lượt là thời điểm vào và ra của nút $u$ trong quá trình DFS.
   $$u \in \text{Anc}(v) \iff \text{tin}[u] \le \text{tin}[v] \land \text{tout}[u] \ge \text{tout}[v]$$
   Điều này cho phép kiểm tra quan hệ tổ tiên trong thời gian $O(1)$.

---

## 5. Core Theory: 4 Phương Pháp Tìm LCA

| Phương Pháp | Tiền xử lý (Preprocessing) | Truy vấn (Query) | Bộ nhớ (Space) | Kiểu thuật toán | Khả năng mở rộng đường đi |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Naive Parent Pointer** | $O(N)$ hoặc $O(1)$ | $O(N)$ (tệ nhất) | $O(N)$ | Trực tiếp / Hai con trỏ | Thấp |
| **2. Binary Lifting** | $O(N \log N)$ | $O(\log N)$ | $O(N \log N)$ | Quy hoạch động / Nhảy nhị phân | Rất cao (kèm RMQ, Path Sum) |
| **3. Euler Tour + RMQ** | $O(N \log N)$ hoặc $O(N)$ | $O(1)$ | $O(N \log N)$ hoặc $O(N)$ | Sparse Table / Fischer-Heun | Trung bình |
| **4. Tarjan Offline DSU** | $O(N + Q \cdot \alpha(N))$ | $O(1)$ khấu hao | $O(N + Q)$ | DFS + Disjoint Set Union | Chỉ chạy Offline |

---

### 5.1. Phương Pháp 1: Naive Parent Pointer & Depth Equalization ($O(N)$ Query)

- **Ý tưởng:** 
  1. Tính độ sâu $\text{depth}[u]$ và mảng cha $\text{parent}[u]$ của mọi nút qua DFS.
  2. Để tìm $\text{LCA}(u, v)$: giả sử $\text{depth}[u] > \text{depth}[v]$, nâng $u$ lên nút cha liên tục cho đến khi $\text{depth}[u] == \text{depth}[v]$.
  3. Sau đó, nâng đồng thời cả $u$ và $v$ lên từng bước ($u = \text{parent}[u]$, $v = \text{parent}[v]$) cho đến khi chúng gặp nhau tại cùng một nút.
- **Đánh giá:** Rất đơn giản, không tốn bộ nhớ bảng nhảy, nhưng với cây suy biến dạng đường thẳng (Skewed Tree / Degenerate Linked List), thời gian truy vấn là $O(N)$. Không phù hợp khi có $Q = 10^5$ truy vấn.

---

### 5.2. Phương Pháp 2: Binary Lifting (Nâng Nhị Phân)

Binary Lifting là kỹ thuật ứng dụng Quy hoạch động (Dynamic Programming) trên cây dựa trên nguyên lý biểu diễn nhị phân: Mọi số nguyên dương $K$ đều phân tích duy nhất thành tổng các lũy thừa của $2$ ($K = \sum b_i 2^i$). Thay vì nhảy từng bước $1$, ta tiền xử lý các bước nhảy có độ dài $2^0, 2^1, 2^2, \dots, 2^{\lfloor \log_2 N \rfloor}$.

#### Bảng Quy Hoạch Động $\text{up}[u][i]$:
- $\text{up}[u][i]$ là tổ tiên thứ $2^i$ của nút $u$.
- **Hệ thức truy hồi (Recurrence Relation):**
  $$\text{up}[u][0] = \text{parent}[u]$$
  $$\text{up}[u][i] = \text{up}[\text{up}[u][i - 1]][i - 1] \quad (\forall i \ge 1)$$
  *Ý nghĩa:* Tổ tiên thứ $2^i$ của $u$ chính là tổ tiên thứ $2^{i-1}$ của nút (tổ tiên thứ $2^{i-1}$ của $u$). Bước nhảy $2^i = 2^{i-1} + 2^{i-1}$.

```
  u ----(2^(i-1) jumps)----> w = up[u][i-1] ----(2^(i-1) jumps)----> up[w][i-1] = up[u][i]
```

#### Quy trình truy vấn $\text{LCA}(u, v)$:
1. **Cân bằng độ sâu:** Giả sử $\text{depth}[u] \ge \text{depth}[v]$. Cần đưa $u$ lên cùng độ sâu với $v$.
   - Tính chênh lệch $\Delta = \text{depth}[u] - \text{depth}[v]$.
   - Duyệt $i$ từ $\lfloor \log_2 N \rfloor$ về $0$: nếu bit thứ $i$ của $\Delta$ bật (tức $(\Delta \gg i) \& 1 == 1$), gán $u = \text{up}[u][i]$.
2. **Kiểm tra trùng hợp:** Nếu sau khi cân bằng $u == v$, thì $v$ chính là tổ tiên của $u$, trả về $u$.
3. **Nhảy đồng thời (Simultaneous Jumping):** Duyệt $i$ từ $\lfloor \log_2 N \rfloor$ về $0$:
   - Nếu $\text{up}[u][i] \ne \text{up}[v][i]$, tức là tổ tiên thứ $2^i$ chưa vượt qua hoặc chưa chạm tới LCA, ta nhảy cả $u$ và $v$ lên: $u = \text{up}[u][i]$, $v = \text{up}[v][i]$.
   - Nếu $\text{up}[u][i] == \text{up}[v][i]$, ta không nhảy vì có thể đã vượt quá LCA chung gần nhất.
4. **Kết quả:** Sau vòng lặp, $u$ và $v$ sẽ đứng ngay dưới LCA chung. Do đó $\text{LCA}(u, v) = \text{up}[u][0]$.

---

### 5.3. Phương Pháp 3: Euler Tour + Range Minimum Query (RMQ) Sparse Table

Phương pháp này chuyển đổi bài toán cây thành bài toán tìm giá trị nhỏ nhất trên đoạn (RMQ) của mảng 1D, đạt thời gian truy vấn $O(1)$.

#### Thuật toán:
1. **Euler Tour (Duyệt vòng Euler):** Thực hiện DFS duyệt cây. Mỗi khi thăm một nút (kể cả khi mới vào hoặc khi quay lui từ con về), ghi lại nút đó vào mảng $\text{eulerNodes}[]$ và độ sâu tương ứng vào $\text{eulerDepths}[]$.
   - Chiều dài mảng Euler Tour tối đa là $2N - 1$.
2. **Mảng vị trí xuất hiện đầu tiên ($\text{firstOccur}[u]$):** Lưu chỉ số đầu tiên mà nút $u$ xuất hiện trong $\text{eulerNodes}[]$.
3. **Quy đổi về RMQ:**
   - Để tìm $\text{LCA}(u, v)$, giả sử $L = \min(\text{firstOccur}[u], \text{firstOccur}[v])$ và $R = \max(\text{firstOccur}[u], \text{firstOccur}[v])$.
   - Mọi nút nằm trên đường đi giữa $u$ và $v$ trên cây đều được thăm qua trong đoạn $[L, R]$ của Euler Tour.
   - Nút có $\text{depth}$ nhỏ nhất trong đoạn $\text{eulerDepths}[L \dots R]$ chính là $\text{LCA}(u, v)$.
4. **Sparse Table:** Tiền xử lý RMQ trên mảng $\text{eulerDepths}$ kích thước $M = 2N - 1$ trong $O(M \log M)$ thời gian và bộ nhớ. Truy vấn RMQ thực hiện trong $O(1)$ bằng phép tính khoảng lũy thừa 2:
   $$\text{k} = \lfloor \log_2(R - L + 1) \rfloor$$
   $$\text{RMQ}(L, R) = \arg\min(\text{eulerDepths}[\text{ST}[k][L]], \text{eulerDepths}[\text{ST}[k][R - 2^k + 1]])$$

---

### 5.4. Phương Pháp 4: Tarjan's Offline LCA (Disjoint Set Union - DSU)

Tarjan's Offline LCA giải quyết toàn bộ $Q$ truy vấn trong thời gian $O(N + Q \cdot \alpha(N))$ bằng cách duyệt DFS hậu thứ tự (Post-order Traversal) kết hợp với cấu trúc DSU.

#### Trạng thái nút (Node Coloring):
- **WHITE (0 - Unvisited):** Chưa thăm.
- **GRAY (1 - Visiting):** Đang nằm trong Call Stack đệ quy (đang duyệt cây con của nó).
- **BLACK (2 - Visited):** Đã duyệt xong hoàn toàn cả nút và toàn bộ cây con.

#### Quy trình xử lý:
1. Khởi tạo mỗi nút là một tập hợp rời rạc trong DSU với $\text{ancestor}[u] = u$.
2. Khi DFS đến nút $u$:
   - Đổi màu $u \to \text{GRAY}$.
   - Với mỗi nút con $v$ của $u$:
     - DFS($v$).
     - Hợp nhất DSU: $\text{union}(u, v)$.
     - Cập nhật đại diện: $\text{ancestor}[\text{find}(u)] = u$.
   - Đổi màu $u \to \text{BLACK}$.
   - Duyệt qua tất cả các truy vấn có chứa $u$ dạng $(u, v)$:
     - Nếu $v$ đã có màu **BLACK**, thì $\text{LCA}(u, v) = \text{ancestor}[\text{find}(v)]$.

---

## 6. Visual Explanation (Minh Họa Trực Quan)

### 6.1. Sơ đồ cây mẫu 12 nút

```mermaid
graph TD
    1((1)) --> 2((2))
    1 --> 3((3))
    2 --> 4((4))
    2 --> 5((5))
    3 --> 6((6))
    3 --> 7((7))
    4 --> 8((8))
    4 --> 9((9))
    5 --> 10((10))
    7 --> 11((11))
    7 --> 12((12))

    classDef root fill:#f96,stroke:#333,stroke-width:2px;
    classDef lca fill:#6f9,stroke:#333,stroke-width:3px;
    classDef target fill:#9cf,stroke:#333,stroke-width:2px;

    class 1 root;
    class 2 lca;
    class 8,10 target;
```

Trong cây trên:
- `depth`: $\text{depth}[1]=0, \text{depth}[2]=1, \text{depth}[4]=2, \text{depth}[8]=3, \text{depth}[5]=2, \text{depth}[10]=3$.
- Với cặp truy vấn $(8, 10)$:
  - $\text{Anc}(8) = \{1, 2, 4, 8\}$
  - $\text{Anc}(10) = \{1, 2, 5, 10\}$
  - $\text{Anc}(8) \cap \text{Anc}(10) = \{1, 2\}$
  - Tổ tiên sâu nhất là nút **2** $\implies \text{LCA}(8, 10) = 2$.

---

### 6.2. Cơ chế Binary Lifting: Bảng Nhảy $\text{up}[u][i]$

Giả sử $\log_2(12) \approx 4$, bảng nhảy kích thước $13 \times 4$:

```
+------+--------+------------+------------+------------+
| Node | depth  | 2^0 (Cha)  | 2^1 (Ông)  | 2^2 (+4)   |
+------+--------+------------+------------+------------+
|  1   |   0    |     1      |     1      |     1      |
|  2   |   1    |     1      |     1      |     1      |
|  3   |   1    |     1      |     1      |     1      |
|  4   |   2    |     2      |     1      |     1      |
|  5   |   2    |     2      |     1      |     1      |
|  6   |   2    |     3      |     1      |     1      |
|  7   |   2    |     3      |     1      |     1      |
|  8   |   3    |     4      |     2      |     1      |
|  9   |   3    |     4      |     2      |     1      |
|  10  |   3    |     5      |     2      |     1      |
|  11  |   3    |     7      |     3      |     1      |
|  12  |   3    |     7      |     3      |     1      |
+------+--------+------------+------------+------------+
```

*Quy trình tìm $\text{LCA}(8, 10)$:*
1. $\text{depth}[8] = 3, \text{depth}[10] = 3 \implies \Delta = 0$ (không cần cân bằng).
2. $i = 2$: $\text{up}[8][2] = 1, \text{up}[10][2] = 1$ (bằng nhau $\implies$ không nhảy).
3. $i = 1$: $\text{up}[8][1] = 2, \text{up}[10][1] = 2$ (bằng nhau $\implies$ không nhảy).
4. $i = 0$: $\text{up}[8][0] = 4, \text{up}[10][0] = 5$ (khác nhau $\implies$ nhảy! $u \gets 4, v \gets 5$).
5. Kết thúc vòng lặp, $\text{LCA} = \text{up}[4][0] = 2$.

---

### 6.3. Euler Tour & Phép Ánh Xạ Sang RMQ

Duyệt Euler Tour từ gốc 1:
```
Index:    0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16 17 18 19 20 21 22
Nodes:   [1, 2, 4, 8, 4, 9, 4, 2, 5, 10, 5, 2, 1, 3, 6, 3, 7, 11, 7, 12, 7, 3, 1]
Depths:  [0, 1, 2, 3, 2, 3, 2, 1, 2,  3, 2, 1, 0, 1, 2, 1, 2,  3, 2,  3, 2, 1, 0]
```

- $\text{firstOccur}[8] = 3$
- $\text{firstOccur}[10] = 9$
- Đoạn cần tìm RMQ trên mảng Depths: chỉ số $3 \to 9$:
  `Sub-array Depths[3..9]: [3, 2, 3, 2, 1, 2, 3]`
- Giá trị nhỏ nhất trong đoạn là `1`, xuất hiện tại index $7$ (hoặc $11$).
- $\text{eulerNodes}[7] = 2 \implies \text{LCA}(8, 10) = 2$.

---

## 7. Java 21 Production-Grade Implementation

Dưới đây là mã nguồn Java 21 đầy đủ, đóng gói chuyên nghiệp, bao gồm 3 lớp độc lập: `BinaryLiftingLCA`, `EulerTourRMQLCA`, và `TarjanOfflineLCA`.

```java
package com.dsa.trees.lca;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.Objects;

/**
 * Production-grade implementations of Lowest Common Ancestor (LCA) algorithms in Java 21.
 * Supports Binary Lifting, Euler Tour + RMQ (Sparse Table), and Tarjan's Offline LCA.
 */
public final class LCASolutions {

    private LCASolutions() {
        // Prevent instantiation of utility wrapper class
    }

    // =========================================================================
    // 1. BINARY LIFTING LCA IMPLEMENTATION
    // =========================================================================

    /**
     * Binary Lifting LCA implementation with flattened 1D jump table for optimal cache locality.
     * Preprocessing: O(N log N) time, O(N log N) space.
     * Query: O(log N) time, O(1) auxiliary space.
     */
    public static final class BinaryLiftingLCA {
        private final int n;
        private final int log;
        private final int root;
        private final int[] depth;
        // Flattened 1D array representing up[node][i] = upFlat[node * log + i]
        private final int[] upFlat;
        private final List<List<Integer>> adj;

        public BinaryLiftingLCA(int n, int root) {
            if (n <= 0) {
                throw new IllegalArgumentException("Number of nodes must be positive: " + n);
            }
            this.n = n;
            this.root = root;
            // log = ceil(log2(n)) + 1
            this.log = 32 - Integer.numberOfLeadingZeros(Math.max(1, n));
            this.depth = new int[n];
            this.upFlat = new int[n * log];
            this.adj = new ArrayList<>(n);
            for (int i = 0; i < n; i++) {
                adj.add(new ArrayList<>());
            }
        }

        public void addEdge(int u, int v) {
            validateNode(u);
            validateNode(v);
            adj.get(u).add(v);
            adj.get(v).add(u);
        }

        public void build() {
            validateNode(root);
            dfs(root, root, 0);
        }

        private void setUp(int node, int i, int ancestor) {
            upFlat[node * log + i] = ancestor;
        }

        private int getUp(int node, int i) {
            return upFlat[node * log + i];
        }

        private void dfs(int u, int p, int d) {
            depth[u] = d;
            setUp(u, 0, p);

            for (int i = 1; i < log; i++) {
                int midAncestor = getUp(u, i - 1);
                setUp(u, i, getUp(midAncestor, i - 1));
            }

            for (int v : adj.get(u)) {
                if (v != p) {
                    dfs(v, u, d + 1);
                }
            }
        }

        public int getLCA(int u, int v) {
            validateNode(u);
            validateNode(v);

            // Step 1: Ensure depth[u] >= depth[v]
            if (depth[u] < depth[v]) {
                int temp = u;
                u = v;
                v = temp;
            }

            // Step 2: Lift u to the same depth as v
            int diff = depth[u] - depth[v];
            for (int i = log - 1; i >= 0; i--) {
                if (((diff >> i) & 1) == 1) {
                    u = getUp(u, i);
                }
            }

            if (u == v) {
                return u;
            }

            // Step 3: Jump simultaneously
            for (int i = log - 1; i >= 0; i--) {
                if (getUp(u, i) != getUp(v, i)) {
                    u = getUp(u, i);
                    v = getUp(v, i);
                }
            }

            return getUp(u, 0);
        }

        public int getKthAncestor(int node, int k) {
            validateNode(node);
            if (k < 0) {
                throw new IllegalArgumentException("k must be non-negative: " + k);
            }
            if (k > depth[node]) {
                return -1; // Out of bounds above root
            }

            int curr = node;
            for (int i = log - 1; i >= 0; i--) {
                if (((k >> i) & 1) == 1) {
                    curr = getUp(curr, i);
                }
            }
            return curr;
        }

        public int getDistance(int u, int v) {
            int lca = getLCA(u, v);
            return depth[u] + depth[v] - 2 * depth[lca];
        }

        public int getDepth(int node) {
            validateNode(node);
            return depth[node];
        }

        private void validateNode(int u) {
            if (u < 0 || u >= n) {
                throw new IndexOutOfBoundsException("Node " + u + " is out of bounds [0, " + (n - 1) + "]");
            }
        }
    }

    // =========================================================================
    // 2. EULER TOUR + RMQ (SPARSE TABLE) LCA IMPLEMENTATION
    // =========================================================================

    /**
     * Euler Tour + Sparse Table RMQ LCA.
     * Preprocessing: O(N log N) time, O(N log N) space.
     * Query: O(1) strictly time, O(1) space.
     */
    public static final class EulerTourRMQLCA {
        private final int n;
        private final int root;
        private final List<List<Integer>> adj;
        private final int[] firstOccur;
        private final int[] depth;
        private final int[] eulerNodes;
        private final int[] eulerDepths;
        private int eulerIndex = 0;

        // Sparse Table storing indices of eulerDepths
        private int[][] st;
        private int[] log2Lookup;
        private boolean built = false;

        public EulerTourRMQLCA(int n, int root) {
            if (n <= 0) {
                throw new IllegalArgumentException("Number of nodes must be positive: " + n);
            }
            this.n = n;
            this.root = root;
            this.adj = new ArrayList<>(n);
            for (int i = 0; i < n; i++) {
                adj.add(new ArrayList<>());
            }
            this.firstOccur = new int[n];
            Arrays.fill(firstOccur, -1);
            this.depth = new int[n];
            int maxEulerSize = 2 * n;
            this.eulerNodes = new int[maxEulerSize];
            this.eulerDepths = new int[maxEulerSize];
        }

        public void addEdge(int u, int v) {
            validateNode(u);
            validateNode(v);
            adj.get(u).add(v);
            adj.get(v).add(u);
        }

        public void build() {
            validateNode(root);
            eulerIndex = 0;
            dfs(root, -1, 0);
            buildSparseTable();
            built = true;
        }

        private void dfs(int u, int p, int d) {
            firstOccur[u] = eulerIndex;
            depth[u] = d;
            eulerNodes[eulerIndex] = u;
            eulerDepths[eulerIndex] = d;
            eulerIndex++;

            for (int v : adj.get(u)) {
                if (v != p) {
                    dfs(v, u, d + 1);
                    eulerNodes[eulerIndex] = u;
                    eulerDepths[eulerIndex] = d;
                    eulerIndex++;
                }
            }
        }

        private void buildSparseTable() {
            int m = eulerIndex;
            log2Lookup = new int[m + 1];
            log2Lookup[1] = 0;
            for (int i = 2; i <= m; i++) {
                log2Lookup[i] = log2Lookup[i / 2] + 1;
            }

            int kMax = log2Lookup[m] + 1;
            st = new int[kMax][m];

            for (int i = 0; i < m; i++) {
                st[0][i] = i;
            }

            for (int k = 1; (1 << k) <= m; k++) {
                int half = 1 << (k - 1);
                for (int i = 0; i + (1 << k) <= m; i++) {
                    int idx1 = st[k - 1][i];
                    int idx2 = st[k - 1][i + half];
                    st[k][i] = (eulerDepths[idx1] <= eulerDepths[idx2]) ? idx1 : idx2;
                }
            }
        }

        public int getLCA(int u, int v) {
            if (!built) {
                throw new IllegalStateException("LCA structure must be built before queries.");
            }
            validateNode(u);
            validateNode(v);

            int left = firstOccur[u];
            int right = firstOccur[v];
            if (left > right) {
                int tmp = left;
                left = right;
                right = tmp;
            }

            int len = right - left + 1;
            int k = log2Lookup[len];
            int idx1 = st[k][left];
            int idx2 = st[k][right - (1 << k) + 1];
            int bestIdx = (eulerDepths[idx1] <= eulerDepths[idx2]) ? idx1 : idx2;

            return eulerNodes[bestIdx];
        }

        public int getDistance(int u, int v) {
            int lca = getLCA(u, v);
            return depth[u] + depth[v] - 2 * depth[lca];
        }

        private void validateNode(int u) {
            if (u < 0 || u >= n) {
                throw new IndexOutOfBoundsException("Node " + u + " out of bounds.");
            }
        }
    }

    // =========================================================================
    // 3. TARJAN'S OFFLINE LCA (DSU) IMPLEMENTATION
    // =========================================================================

    public record Query(int u, int v, int queryIndex) {}

    /**
     * Tarjan's Offline LCA using Disjoint Set Union (Union-Find).
     * Time Complexity: O(N + Q * alpha(N)).
     * Space Complexity: O(N + Q).
     */
    public static final class TarjanOfflineLCA {
        private final int n;
        private final int root;
        private final List<List<Integer>> adj;
        private final List<List<Query>> queryAdj;
        private final int[] parent;
        private final int[] rank;
        private final int[] ancestor;
        private final boolean[] visited;
        private final int[] answers;

        public TarjanOfflineLCA(int n, int root, int queryCount) {
            if (n <= 0) {
                throw new IllegalArgumentException("Node count must be positive.");
            }
            this.n = n;
            this.root = root;
            this.adj = new ArrayList<>(n);
            this.queryAdj = new ArrayList<>(n);
            for (int i = 0; i < n; i++) {
                adj.add(new ArrayList<>());
                queryAdj.add(new ArrayList<>());
            }
            this.parent = new int[n];
            this.rank = new int[n];
            this.ancestor = new int[n];
            this.visited = new boolean[n];
            this.answers = new int[queryCount];
            Arrays.fill(answers, -1);

            for (int i = 0; i < n; i++) {
                parent[i] = i;
                ancestor[i] = i;
            }
        }

        public void addEdge(int u, int v) {
            adj.get(u).add(v);
            adj.get(v).add(u);
        }

        public void addQuery(int u, int v, int queryIdx) {
            queryAdj.get(u).add(new Query(u, v, queryIdx));
            if (u != v) {
                queryAdj.get(v).add(new Query(v, u, queryIdx));
            }
        }

        private int find(int i) {
            if (parent[i] == i) {
                return i;
            }
            return parent[i] = find(parent[i]);
        }

        private void unionSets(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI != rootJ) {
                if (rank[rootI] < rank[rootJ]) {
                    parent[rootI] = rootJ;
                } else if (rank[rootI] > rank[rootJ]) {
                    parent[rootJ] = rootI;
                } else {
                    parent[rootJ] = rootI;
                    rank[rootI]++;
                }
            }
        }

        public int[] solve() {
            dfs(root, -1);
            return answers;
        }

        private void dfs(int u, int p) {
            for (int v : adj.get(u)) {
                if (v != p) {
                    dfs(v, u);
                    unionSets(u, v);
                    ancestor[find(u)] = u;
                }
            }

            visited[u] = true;

            for (Query q : queryAdj.get(u)) {
                int other = (q.u() == u) ? q.v() : q.u();
                if (visited[other]) {
                    int lcaNode = ancestor[find(other)];
                    answers[q.queryIndex()] = lcaNode;
                }
            }
        }
    }
}
```

---

## 8. Step-by-Step Execution Trace

Hãy cùng lần theo từng bước tính toán chi tiết khi thực thi thuật toán **Binary Lifting** trên cây mẫu gồm 12 nút (nút 0 đến 11, với gốc là nút 0).

### Cấu trúc cây:
- `0 -> 1, 2`
- `1 -> 3, 4`
- `2 -> 5, 6`
- `3 -> 7, 8`
- `4 -> 9`
- `6 -> 10, 11`

### 8.1. Trace Truy vấn $\text{LCA}(7, 9)$:
1. **Kiểm tra độ sâu:**
   - $\text{depth}[7] = 3$ (đường đi: $0 \to 1 \to 3 \to 7$).
   - $\text{depth}[9] = 3$ (đường đi: $0 \to 1 \to 4 \to 9$).
   - Độ sâu bằng nhau: $\Delta = 3 - 3 = 0$. Không cần nâng.
2. **Kiểm tra trùng hợp:** $7 \ne 9$.
3. **Nhảy đồng thời với $\log = 4$ ($i = 3, 2, 1, 0$):**
   - **$i = 3$ ($2^3 = 8$):** $\text{up}[7][3] = 0$, $\text{up}[9][3] = 0 \implies$ Bằng nhau, không nhảy.
   - **$i = 2$ ($2^2 = 4$):** $\text{up}[7][2] = 0$, $\text{up}[9][2] = 0 \implies$ Bằng nhau, không nhảy.
   - **$i = 1$ ($2^1 = 2$):** $\text{up}[7][1] = 1$, $\text{up}[9][1] = 1 \implies$ Bằng nhau, không nhảy.
   - **$i = 0$ ($2^0 = 1$):** $\text{up}[7][0] = 3$, $\text{up}[9][0] = 4 \implies$ Khác nhau ($3 \ne 4$). Ta nhảy:
     $$u \gets \text{up}[7][0] = 3, \quad v \gets \text{up}[9][0] = 4$$
4. **Kết quả:** $\text{LCA}(7, 9) = \text{up}[u][0] = \text{up}[3][0] = 1$.

---

### 8.2. Trace Truy vấn $\text{LCA}(7, 10)$:
1. **Kiểm tra độ sâu:** $\text{depth}[7] = 3$, $\text{depth}[10] = 3 \implies \Delta = 0$.
2. **Nhảy đồng thời:**
   - **$i = 3$:** $\text{up}[7][3] = 0, \text{up}[10][3] = 0 \implies$ Giữ nguyên.
   - **$i = 2$:** $\text{up}[7][2] = 0, \text{up}[10][2] = 0 \implies$ Giữ nguyên.
   - **$i = 1$:** $\text{up}[7][1] = 1, \text{up}[10][1] = 2 \implies 1 \ne 2$, Nhảy!
     $$u \gets 1, \quad v \gets 2$$
   - **$i = 0$:** $\text{up}[1][0] = 0, \text{up}[2][0] = 0 \implies$ Giữ nguyên.
3. **Kết quả:** $\text{LCA}(7, 10) = \text{up}[1][0] = 0$.

---

### 8.3. Trace Truy vấn $\text{LCA}(7, 1)$ (Một nút là tổ tiên của nút kia):
1. **Kiểm tra độ sâu:** $\text{depth}[7] = 3$, $\text{depth}[1] = 1$.
   - $\Delta = 3 - 1 = 2 = (10)_2$.
2. **Nâng $u$ lên cùng độ sâu:**
   - $i = 1$ (bit 1 bật): $u \gets \text{up}[7][1] = 1$.
3. **Kiểm tra trùng hợp:** Sau khi nâng, $u = 1 == v = 1$.
4. **Kết quả:** Trả về ngay $1$ mà không cần thực hiện bước nhảy đồng thời.

---

## 9. Complexity Analysis (Phân Tích Độ Phức Tạp)

### 9.1. Bảng So Sánh Chi Tiết

| Thuật Toán | Tiền Xử Lý Thời Gian | Tiền Xử Lý Bộ Nhớ | Thời Gian 1 Truy Vấn | Bộ Nhớ Phụ Mỗi Truy Vấn | Tính Chất |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Naive Parent Lifting** | $O(N)$ | $O(N)$ | $O(N)$ tệ nhất / $O(H)$ | $O(1)$ | Online, thích hợp cây cân bằng |
| **Binary Lifting** | $O(N \log N)$ | $O(N \log N)$ | $O(\log N)$ | $O(1)$ | Online, hỗ trợ Dynamic Path Aggregation |
| **Euler Tour + RMQ** | $O(N \log N)$ | $O(N \log N)$ | $O(1)$ strictly | $O(1)$ | Online, nhanh nhất khi $Q \gg N$ |
| **Tarjan DSU** | $O(N + Q \cdot \alpha(N))$ | $O(N + Q)$ | $O(\alpha(N)) \approx O(1)$ | $O(1)$ | Offline, tối ưu bộ nhớ nhất |
| **Heavy-Light Decomp** | $O(N)$ | $O(N)$ | $O(\log N)$ | $O(1)$ | Online, mạnh nhất cho Dynamic Tree Updates |

### 9.2. Chứng minh toán học thời gian của Binary Lifting
- **Tiền xử lý:**
  - DFS duyệt qua mỗi đỉnh đúng 1 lần: $O(V + E) = O(N)$.
  - Tại mỗi đỉnh, lặp $\log_2 N$ lần để tính `up[u][i]`: $\sum_{u=1}^N \log_2 N = O(N \log N)$.
- **Truy vấn:**
  - Cân bằng độ sâu: Vòng lặp duyệt $\log_2 N$ bước $\implies O(\log N)$.
  - Nhảy đồng thời: Vòng lặp duyệt $\log_2 N$ bước $\implies O(\log N)$.
  - Tổng thời gian truy vấn: $O(\log N)$.

---

## 10. JVM & System Level Analysis

### 10.1. Bộ nhớ & Cache Locality: 2D Jagged Array vs Flattened 1D Array

Trong Java, mảng 2 chiều `int[][] up = new int[N][LOG]` không phải là một khối bộ nhớ phẳng liên tục. Nó là một mảng gồm $N$ con trỏ (Object References), mỗi con trỏ trỏ tới một mảng `int[LOG]` độc lập trên Java Heap.

```
Mô hình int[][] (Jagged Array):
[Pointer Array in Heap]
  up[0] ---> [Object Header (16B) | Length (4B) | int, int, int...] (Mảnh vỡ Heap)
  up[1] ---> [Object Header (16B) | Length (4B) | int, int, int...] (Mảnh vỡ Heap)
  up[2] ---> [Object Header (16B) | Length (4B) | int, int, int...] (Mảnh vỡ Heap)

Mô hình int[] upFlat (Flattened 1D Array):
[Object Header (16B) | Length (4B) | u0_i0, u0_i1, ... u1_i0, u1_i1, ... un_iLOG] (Khối liên tục)
```

#### Phân tích chi phí bộ nhớ với $N = 10^6, \text{LOG} = 20$ trên 64-bit JVM (+CompressedOops):
1. **Với `int[][]`:**
   - Mảng ngoài: 24 bytes header + $10^6 \times 4$ bytes = 4.0 MB.
   - $10^6$ mảng con: Mỗi mảng con tốn 16 bytes header + 4 bytes length + 4 bytes padding + $20 \times 4 = 80$ bytes $\implies 104$ bytes.
   - Tổng cộng: $4.0 \text{ MB} + 104 \text{ MB} \approx 108 \text{ MB}$.
   - **Tác động CPU Cache:** Truy cập `up[u][i]` phải thực hiện 2 lần dereference con trỏ (`up[u]` $\to$ `[i]`), gây Cache Miss ở cấp độ L1/L2 liên tục.
2. **Với `int[] upFlat`:**
   - Duy nhất 1 mảng 1D: 24 bytes header + $10^6 \times 20 \times 4$ bytes = **80.0 MB** (Tiết kiệm gần 30 MB bộ nhớ Heap).
   - **Tác động CPU Cache:** Bộ nhớ hoàn toàn liên tục. Khi CPU nạp một Cache Line (64 bytes = 16 số nguyên `int`), các bước nhảy `upFlat[u * log + i]` của cùng một nút đều nằm trọn trong L1 Data Cache.

---

### 10.2. Call Stack Recursion & Tràn Ngăn Xếp (`StackOverflowError`)

Với cây suy biến dạng đường thẳng (Skewed Tree / Linked List) có độ sâu $N = 10^5$, hàm đệ quy `dfs(u, p, d)` sẽ tạo $10^5$ Stack Frames. Kích thước mặc định của Java Thread Stack (`-Xss1m`) chỉ chứa được khoảng $10^4$ frames, dẫn đến `java.lang.StackOverflowError`.

#### Giải pháp trong Production:
1. **Chuyển sang BFS hoặc DFS khử đệ quy bằng Stack tùy biến:** Sử dụng `int[] stack` tự quản lý trên Heap.
2. **Cấu hình JVM:** Thiết lập cờ `-Xss8m` nếu bắt buộc dùng đệ quy trên dữ liệu sâu.

---

## 11. OpenJDK & JVM Internal Architecture Analysis

### 11.1. Dominator Tree trong HotSpot C2 Compiler
Trong trình biên dịch JIT tối ưu hóa cao cấp HotSpot C2 (`src/hotspot/share/opto/cfg.cpp` và `loopnode.cpp`), JVM xây dựng đồ thị luồng điều khiển (Control Flow Graph - CFG) của bytecode. Để tối ưu hóa việc di dời mã bất biến ra khỏi vòng lặp (Loop Invariant Code Motion - LICM) và loại bỏ kiểm tra null trùng lặp (Null Check Elimination), C2 xây dựng **Dominator Tree**.

Một nút $D$ thống trị (dominates) nút $N$ nếu mọi luồng thực thi từ Entry đến $N$ đều phải đi qua $D$. Điểm giao thoa kiểm soát nhỏ nhất của hai nhánh rẽ điều kiện (chẳng hạn nút $\phi$ - Phi Node) chính là **Immediate Dominator (idom)**, được tính toán thông qua biến thể của thuật toán Lengauer-Tarjan và bài toán LCA trên Dominator Tree.

```
       [Entry]
        /   \
    [Branch1] [Branch2]
        \   /
      [Phi Node]  <--- LCA trong Dominator Tree xác định Scope tối ưu
```

---

### 11.2. Class Hierarchy Analysis (CHA) & Common Supertype Computation
Trong Bytecode Verifier (`verificationType.cpp`) và quá trình JIT Inlining Devirtualization:
- Khi JVM kiểm tra tính hợp lệ của hai kiểu tham chiếu $T_1, T_2$ trên Operand Stack tại điểm hội tụ (Merge Point), JVM phải tìm **Lowest Common Supertype** trong đồ thị kế thừa lớp.
- Nếu không có đa kế thừa interface, phân cấp kế thừa đơn của Java Class hoàn toàn là một Cây (Single Inheritance Tree) có gốc là `java.lang.Object`. Bài toán tìm kiểu dữ liệu chung nhỏ nhất chính là bài toán tìm **LCA trên Java Class Inheritance Tree**.

---

## 12. Production Use Cases (Ứng Dụng Thực Tế)

```
+-------------------------------------------------------------------------------+
|                         CÁC ỨNG DỤNG THỰC CHIẾN CỦA LCA                       |
+-------------------------------------------------------------------------------+
| 1. Git Version Control: `git merge-base` xác định common ancestor trên DAG    |
| 2. Network Routing: Spanning Tree Protocol (STP) & BGP Route Aggregation      |
| 3. DOM & UI Frameworks: Event Bubbling & Shared Ancestor Container in React/DOM|
| 4. Bioinformatics: Phân tích cây phát sinh loài (Phylogenetic Tree Distance)  |
| 5. Distributed Systems: Vector Clock Synchronization & Causal Ordering        |
+-------------------------------------------------------------------------------+
```

1. **Git Merge-Base:** Khi hợp nhất nhánh, Git tìm commit tổ tiên chung gần nhất để áp dụng thuật toán 3-way merge.
2. **DOM Tree Event Propagation:** Khi hai phần tử UI tương tác (ví dụ Drag & Drop từ element $A$ sang element $B$), trình duyệt tìm `commonAncestorContainer` để xác định phạm vi cần vẽ lại (Repaint/Reflow Scope) tối thiểu.
3. **Bioinformatics:** Trong hệ gen học, khoảng cách tiến hóa giữa hai loài sinh vật được tính bằng khoảng cách trên cây phân loại sinh học thông qua $\text{LCA}(Loài_1, Loài_2)$.

---

## 13. Design Decisions & Trade-offs

```
                       BẠN CẦN CHỌN THUẬT TOÁN LCA NÀO?
                                      |
                     +----------------+----------------+
                     |                                 |
             Truy vấn Offline                  Truy vấn Online
             (Biết trước hết Q)               (Hỏi đến đâu trả lời đến đó)
                     |                                 |
             [Tarjan DSU]                              |
             Time: O(N + Q)            +---------------+---------------+
             Space: O(N + Q)           |                               |
                                $Q \ge 10^6$ & Cây Tĩnh       Cần tính Path Aggregation
                                (Cực kỳ nhạy cảm Latency)     (Min/Max/Sum trên đường đi)
                                       |                               |
                               [Euler Tour + RMQ]              [Binary Lifting]
                               Time Prep: O(N log N)          Time Prep: O(N log N)
                               Time Query: O(1)               Time Query: O(log N)
```

---

## 14. 20 Common Bugs & How to Fix Them

1. **Bug 1: Thiếu kích thước $\log$ bảng nhảy.**
   - *Nguyên nhân:* Tính $\log = \lfloor \log_2 N \rfloor$ mà quên $+1$, dẫn đến không nhảy được đến gốc.
   - *Khắc phục:* `int log = 32 - Integer.numberOfLeadingZeros(n);`.
2. **Bug 2: Trỏ ra ngoài gốc $\text{up}[root][i] \ne root$.**
   - *Nguyên nhân:* Để $\text{up}[root][0] = -1$ hoặc $0$ không thống nhất làm mảng nhảy tràn chỉ số âm.
   - *Khắc phục:* Gán $\text{up}[root][i] = root$ cho mọi $i$.
3. **Bug 3: Thứ tự ưu tiên toán tử khi dịch bit.**
   - *Nguyên nhân:* Viết `(diff >> i) & 1 == 1` trong Java bị lỗi cú pháp vì `==` ưu tiên hơn `&`.
   - *Khắc phục:* Đóng ngoặc chuẩn: `((diff >> i) & 1) == 1`.
4. **Bug 4: Kích thước mảng Euler Tour không đủ.**
   - *Nguyên nhân:* Khởi tạo `eulerNodes` với kích thước $N$ thay vì $2N$.
   - *Khắc phục:* Cây $N$ đỉnh có đúng $2N - 1$ lần thăm trong Euler Tour, cấp phát `2 * N`.
5. **Bug 5: Bỏ qua bước kiểm tra $u == v$ sau khi cân bằng độ sâu.**
   - *Nguyên nhân:* Nếu $v$ là tổ tiên trực tiếp của $u$, sau khi nâng $u$ lên cùng độ sâu thì $u = v$. Nếu vẫn tiếp tục nhảy đồng thời sẽ trả về sai $\text{up}[u][0]$ (nút cha của $v$).
   - *Khắc phục:* Bắt buộc thêm `if (u == v) return u;`.
6. **Bug 6: Lỗi nhảy nhị phân khi bằng nhau thay vì khi khác nhau.**
   - *Nguyên nhân:* Nhầm lẫn điều kiện `if (up[u][i] == up[v][i])`.
   - *Khắc phục:* Chỉ nhảy khi chưa vượt qua LCA: `if (up[u][i] != up[v][i])`.
7. **Bug 7: Tarjan DSU cập nhật ancestor sai thời điểm.**
   - *Nguyên nhân:* Gán `ancestor[find(u)] = u` trước khi gọi đệ quy con.
   - *Khắc phục:* Phải gán sau khi `unionSets(u, v)`.
8. **Bug 8: Quên đăng ký truy vấn hai chiều trong Tarjan Offline.**
   - *Nguyên nhân:* Chỉ thêm query vào danh sách của $u$ mà không thêm vào $v$.
   - *Khắc phục:* Thêm query cho cả $u$ và $v$ với cùng `queryIndex`.
9. **Bug 9: Xử lý truy vấn Tarjan khi nút đối tác đang có màu `GRAY`.**
   - *Nguyên nhân:* Trả về LCA sai khi nút đối tác đang nằm trên ngăn xếp đệ quy hiện tại.
   - *Khắc phục:* Chỉ ghi nhận kết quả khi `visited[v] == true` (đã là nút BLACK).
10. **Bug 10: Xung đột chỉ số 0-based và 1-based.**
    - *Nguyên nhân:* Dữ liệu đầu vào đánh số đỉnh từ $1 \dots N$ nhưng mảng code từ $0 \dots N-1$.
    - *Khắc phục:* Chuẩn hóa toàn bộ đỉnh về 0-based ngay tại tầng Adapter/Input.
11. **Bug 11: `StackOverflowError` do đệ quy DFS quá sâu.**
    - *Khắc phục:* Viết hàm DFS khử đệ quy bằng `int[] stack` hoặc dùng BFS để khởi tạo `depth` và `parent`.
12. **Bug 12: Hoán vị sai $L$ và $R$ trong Euler Tour RMQ.**
    - *Nguyên nhân:* Khi `firstOccur[u] > firstOccur[v]`, không hoán đổi dẫn đến truy vấn đoạn $[L, R]$ không hợp lệ.
    - *Khắc phục:* `if (L > R) { int tmp = L; L = R; R = tmp; }`.
13. **Bug 13: Tràn số khi dịch bit `1 << 31`.**
    - *Khắc phục:* Luôn đảm bảo số mũ $i < 31$.
14. **Bug 14: Lỗi bảng thưa Sparse Table khi $N = 1$.**
    - *Khắc phục:* Khởi tạo mảng log2 với `log2Lookup[1] = 0` và cận an toàn.
15. **Bug 15: Truy vấn khoảng cách trên cây có chu trình.**
    - *Khắc phục:* Kiểm tra đồ thị đầu vào đảm bảo $E = V - 1$ và liên thông trước khi build.
16. **Bug 16: Không tái tạo (rebuild) bảng nhảy khi cây bị đột biến.**
    - *Khắc phục:* Đánh dấu cờ `isDirty = true` khi thêm/xóa cạnh.
17. **Bug 17: Tính khoảng cách bị số âm do tràn số với trọng số lớn.**
    - *Khắc phục:* Dùng kiểu `long[] dist` thay cho `int[]` đối với cây có trọng số lớn.
18. **Bug 18: Lỗi `getKthAncestor` với $K > \text{depth}[u]$.**
    - *Khắc phục:* Trả về `-1` thay vì nhảy vượt quá gốc ra kết quả sai.
19. **Bug 19: Tarjan DSU thiếu Path Compression.**
    - *Khắc phục:* Luôn gán `parent[i] = find(parent[i])`.
20. **Bug 20: Thread-Safety Race Condition khi tiền xử lý lười (Lazy Init).**
    - *Khắc phục:* Dùng `volatile` hoặc build hoàn tất trong constructor bất biến.

---

## 15. 30 Comprehensive Edge Cases & Handling Strategies

1. **Cây chỉ có đúng 1 nút ($N = 1$):** `getLCA(0, 0)` trả về ngay `0`.
2. **Hai nút truy vấn trùng nhau ($u == v$):** Trả về ngay $u$.
3. **Một nút là gốc ($u = root$ hoặc $v = root$):** $\text{LCA}(root, v) = root$.
4. **Nút cha con trực tiếp ($u = parent[v]$):** Trả về $u$.
5. **Cây suy biến thành một đường thẳng (Skewed Chain):** Kiểm tra chống `StackOverflowError`.
6. **Cây hình ngôi sao (Star Tree - 1 nút cha nối $N-1$ nút lá):** $\text{LCA}(lá_i, lá_j) = root$.
7. **Cây nhị phân hoàn hảo (Perfect Binary Tree):** Kiểm tra tính cân bằng của mọi bước nhảy.
8. **Hai nút nằm ở hai nhánh con đối lập của gốc:** $\text{LCA}(u, v) = root$.
9. **Hai nút là anh em ruột (Siblings):** $\text{LCA}(u, v) = parent[u]$.
10. **Truy vấn $K$-th Ancestor với $K = 0$:** Trả về chính nút đó.
11. **Truy vấn $K$-th Ancestor với $K = \text{depth}[u]$:** Trả về `root`.
12. **Truy vấn $K$-th Ancestor với $K > \text{depth}[u]$:** Trả về `-1`.
13. **Truy vấn khoảng cách giữa hai nút trùng nhau:** $\text{dist}(u, u) = 0$.
14. **Khoảng cách giữa hai nút cạnh nhau:** $\text{dist}(u, v) = 1$.
15. **Đồ thị rừng rời rạc (Forest - $u$ và $v$ ở 2 cây khác nhau):** Ném `IllegalArgumentException` hoặc trả về `-1`.
16. **Nút $u$ hoặc $v$ nằm ngoài chỉ số $[0, N - 1]$:** Ném `IndexOutOfBoundsException`.
17. **Cây có $N = 2$ nút:** Kiểm tra các phép gán cơ sở.
18. **Cây sâu tối đa ($N = 10^5$, chiều cao $10^5$):** Tối ưu hóa đệ quy.
19. **Số lượng truy vấn cực lớn ($Q = 10^7$):** Sử dụng Euler Tour + RMQ để đạt $O(1)$ query.
20. **Bộ nhớ bị giới hạn nghiêm ngặt (Memory Limit 32MB):** Dùng Tarjan Offline hoặc Binary Lifting với 1D array.
21. **Truy vấn liên tiếp lặp lại cùng một cặp $(u, v)$:** Đảm bảo tính Idempotent thuần túy.
22. **Nút gốc không phải là 0 (Root là đỉnh bất kỳ $r$):** Kiểm tra tính đúng đắn của DFS từ $r$.
23. **Cạnh có trọng số âm (không có chu trình âm):** Hàm khoảng cách vẫn đúng vì khoảng cách chỉ phụ thuộc vào khoảng cách từ gốc.
24. **Cây có trọng số bằng 0:** Khoảng cách giữa các nút có thể bằng 0.
25. **Thứ tự thêm cạnh không theo chiều từ cha đến con:** Thuật toán phải xây dựng đồ thị vô hướng và định hướng qua DFS.
26. **Đỉnh lá nằm ở độ sâu cực lớn:** Đảm bảo độ sâu tính bằng `int` không bị tràn.
27. **Gọi hàm `getLCA` trước khi gọi `build()`:** Ném `IllegalStateException`.
28. **Các truy vấn Tarjan có $u == v$:** Xử lý chính xác mà không lặp vô tận.
29. **Cây có bậc đỉnh cực cao (High Branching Factor):** Sử dụng `ArrayList` thay vì cấu trúc mảng cố định lãng phí.
30. **Truy vấn đa luồng đồng thời trên cùng đối tượng LCA:** Đảm bảo trạng thái Read-Only hoàn toàn Thread-Safe.

---

## 16. Optimization Techniques

1. **Bitwise Fast $\log_2$ Lookup:** Sử dụng hàm nội tại CPU `31 - Integer.numberOfLeadingZeros(x)` để tính $\lfloor \log_2 x \rfloor$ trong 1 chu kỳ máy (Single Clock Cycle).
2. **Flattened Memory Layout:** Chuyển đổi bảng nhảy 2D thành mảng 1D `int[N * LOG]` giúp tăng tốc độ nạp Cache Line lên hơn $300\%$.
3. **Primitive Specialized Collections:** Sử dụng mảng nguyên thủy `int[]` thay vì `List<Integer>` trong cấu trúc biểu diễn đồ thị (dùng Forward Star / Head-Next Array) khi cần tối ưu tuyệt đối.
4. **Fischer-Heun RMQ $<O(N), O(1)>$:** Phân rã mảng Euler Tour thành các block kích thước $B = \frac{1}{2} \log_2 N$, kết hợp Sparse Table cho các block và Bitmask RMQ nội bộ block để đạt tiền xử lý tuyến tính $O(N)$ và truy vấn $O(1)$.

---

## 17. Best Practices

- **Immutability & Encapsulation:** Đóng gói toàn bộ cấu trúc cây và mảng tiền xử lý bên trong đối tượng bất biến sau khi gọi `build()`.
- **Defensive Validation:** Luôn kiểm tra biên mọi chỉ số đỉnh đầu vào để tránh `ArrayIndexOutOfBoundsException` gây sập ứng dụng ngầm.
- **Fail-Fast State Checks:** Kiểm tra trạng thái sẵn sàng của cấu trúc dữ liệu trước khi phục vụ truy vấn.

---

## 18. JMH Benchmark Code

```java
package com.dsa.trees.lca;

import org.openjdk.jmh.annotations.*;
import java.util.Random;
import java.util.concurrent.TimeUnit;

@BenchmarkMode(Mode.AverageTime)
@OutputTimeUnit(TimeUnit.NANOSECONDS)
@Warmup(iterations = 3, time = 1)
@Measurement(iterations = 5, time = 1)
@Fork(1)
@State(Scope.Benchmark)
public class LCABenchmark {

    @Param({"10000", "100000"})
    private int nodeCount;

    private LCASolutions.BinaryLiftingLCA binaryLifting;
    private LCASolutions.EulerTourRMQLCA eulerRMQ;
    private int[] queryU;
    private int[] queryV;
    private static final int QUERY_SIZE = 10000;
    private int queryIdx = 0;

    @Setup(Level.Trial)
    public void setup() {
        binaryLifting = new LCASolutions.BinaryLiftingLCA(nodeCount, 0);
        eulerRMQ = new LCASolutions.EulerTourRMQLCA(nodeCount, 0);

        // Build a random balanced tree
        Random rng = new Random(42);
        for (int i = 1; i < nodeCount; i++) {
            int parent = rng.nextInt(i);
            binaryLifting.addEdge(parent, i);
            eulerRMQ.addEdge(parent, i);
        }
        binaryLifting.build();
        eulerRMQ.build();

        queryU = new int[QUERY_SIZE];
        queryV = new int[QUERY_SIZE];
        for (int i = 0; i < QUERY_SIZE; i++) {
            queryU[i] = rng.nextInt(nodeCount);
            queryV[i] = rng.nextInt(nodeCount);
        }
    }

    @Benchmark
    public int benchmarkBinaryLifting() {
        int idx = (queryIdx++) % QUERY_SIZE;
        return binaryLifting.getLCA(queryU[idx], queryV[idx]);
    }

    @Benchmark
    public int benchmarkEulerTourRMQ() {
        int idx = (queryIdx++) % QUERY_SIZE;
        return eulerRMQ.getLCA(queryU[idx], queryV[idx]);
    }
}
```

---

## 19. Unit Testing Suite (JUnit 5)

```java
package com.dsa.trees.lca;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

import java.util.Random;

class LCASolutionsTest {

    private LCASolutions.BinaryLiftingLCA blLCA;
    private LCASolutions.EulerTourRMQLCA rmqlCA;

    @BeforeEach
    void setUp() {
        // Cây 12 nút như sơ đồ Section 6
        int n = 13;
        blLCA = new LCASolutions.BinaryLiftingLCA(n, 1);
        rmqlCA = new LCASolutions.EulerTourRMQLCA(n, 1);

        int[][] edges = {
            {1, 2}, {1, 3}, {2, 4}, {2, 5},
            {3, 6}, {3, 7}, {4, 8}, {4, 9},
            {5, 10}, {7, 11}, {7, 12}
        };

        for (int[] e : edges) {
            blLCA.addEdge(e[0], e[1]);
            rmqlCA.addEdge(e[0], e[1]);
        }
        blLCA.build();
        rmqlCA.build();
    }

    @Test
    @DisplayName("Kiểm tra LCA giữa hai lá đối xứng")
    void testLCAOfLeaves() {
        assertEquals(2, blLCA.getLCA(8, 10));
        assertEquals(2, rmqlCA.getLCA(8, 10));

        assertEquals(1, blLCA.getLCA(8, 12));
        assertEquals(1, rmqlCA.getLCA(8, 12));
    }

    @Test
    @DisplayName("Kiểm tra LCA khi một nút là tổ tiên của nút kia")
    void testLCAAncestorDescendant() {
        assertEquals(2, blLCA.getLCA(2, 8));
        assertEquals(2, rmqlCA.getLCA(2, 8));

        assertEquals(1, blLCA.getLCA(1, 12));
        assertEquals(1, rmqlCA.getLCA(1, 12));
    }

    @Test
    @DisplayName("Kiểm tra LCA của cùng một nút")
    void testLCASameNode() {
        assertEquals(8, blLCA.getLCA(8, 8));
        assertEquals(8, rmqlCA.getLCA(8, 8));
    }

    @Test
    @DisplayName("Kiểm tra tính khoảng cách giữa hai nút trên cây")
    void testTreeDistance() {
        // Đường đi 8 -> 4 -> 2 -> 5 -> 10 (độ dài 4)
        assertEquals(4, blLCA.getDistance(8, 10));
        assertEquals(4, rmqlCA.getDistance(8, 10));

        // Đường đi 8 -> 4 -> 2 -> 1 -> 3 -> 7 -> 12 (độ dài 6)
        assertEquals(6, blLCA.getDistance(8, 12));
    }

    @Test
    @DisplayName("Kiểm tra Tarjan Offline LCA")
    void testTarjanOfflineLCA() {
        LCASolutions.TarjanOfflineLCA tarjan = new LCASolutions.TarjanOfflineLCA(13, 1, 3);
        int[][] edges = {
            {1, 2}, {1, 3}, {2, 4}, {2, 5},
            {3, 6}, {3, 7}, {4, 8}, {4, 9},
            {5, 10}, {7, 11}, {7, 12}
        };
        for (int[] e : edges) {
            tarjan.addEdge(e[0], e[1]);
        }
        tarjan.addQuery(8, 10, 0);
        tarjan.addQuery(8, 12, 1);
        tarjan.addQuery(1, 11, 2);

        int[] ans = tarjan.solve();
        assertEquals(2, ans[0]);
        assertEquals(1, ans[1]);
        assertEquals(1, ans[2]);
    }

    @Test
    @DisplayName("Property-Based Randomized Equivalence Test")
    void testRandomizedEquivalence() {
        int n = 500;
        LCASolutions.BinaryLiftingLCA bl = new LCASolutions.BinaryLiftingLCA(n, 0);
        LCASolutions.EulerTourRMQLCA rmq = new LCASolutions.EulerTourRMQLCA(n, 0);
        Random rng = new Random(12345);

        for (int i = 1; i < n; i++) {
            int p = rng.nextInt(i);
            bl.addEdge(p, i);
            rmq.addEdge(p, i);
        }
        bl.build();
        rmq.build();

        for (int q = 0; q < 5000; q++) {
            int u = rng.nextInt(n);
            int v = rng.nextInt(n);
            int lca1 = bl.getLCA(u, v);
            int lca2 = rmq.getLCA(u, v);
            assertEquals(lca1, lca2, "Mismatch at query (" + u + ", " + v + ")");
        }
    }
}
```

---

## 20. 20 Interview Questions (From Easy to Staff Level)

1. **Câu 1 (Easy):** Định nghĩa Lowest Common Ancestor (LCA) của hai nút trên cây có gốc?
   - *Trả lời:* LCA là nút tổ tiên chung của cả hai nút có độ sâu lớn nhất (xa gốc nhất).
2. **Câu 2 (Easy):** Làm thế nào để tìm LCA trong Cây tìm kiếm nhị phân (BST) mà không cần cấu trúc phụ trợ?
   - *Trả lời:* So sánh giá trị với nút hiện tại: Nếu cả $u, v < curr$, đi sang trái; nếu cả $u, v > curr$, đi sang phải; nếu $u \le curr \le v$, trả về $curr$ trong $O(H)$ thời gian và $O(1)$ bộ nhớ.
3. **Câu 3 (Medium):** Trình bày nguyên lý Binary Lifting và công thức truy hồi của bảng $\text{up}[u][i]$?
   - *Trả lời:* $\text{up}[u][i] = \text{up}[\text{up}[u][i-1]][i-1]$ với bước nhảy $2^i = 2^{i-1} + 2^{i-1}$.
4. **Câu 4 (Medium):** Cách tính khoảng cách ngắn nhất giữa hai nút $u$ và $v$ trên cây không trọng số trong $O(1)$ sau khi có LCA?
   - *Trả lời:* $\text{dist}(u, v) = \text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[\text{LCA}(u, v)]$.
5. **Câu 5 (Medium):** Euler Tour + RMQ giải quyết bài toán LCA như thế nào? Độ phức tạp thời gian truy vấn là bao nhiêu?
   - *Trả lời:* Chuyển đổi cây thành mảng Euler Tour độ sâu kích thước $2N-1$. LCA là nút có độ sâu nhỏ nhất trong đoạn giữa 2 lần xuất hiện đầu tiên. Truy vấn đạt $O(1)$ qua Sparse Table.
6. **Câu 6 (Medium):** Nêu sự khác biệt giữa thuật toán Online LCA và Offline LCA?
   - *Trả lời:* Online xử lý từng truy vấn độc lập ngay lập tức mà không cần biết trước các truy vấn tương lai; Offline yêu cầu nhận toàn bộ $Q$ truy vấn trước để duyệt cây 1 lần duy nhất.
7. **Câu 7 (Hard):** Làm thế nào để tìm giá trị trọng số lớn nhất trên đường đi giữa $u$ và $v$ bằng Binary Lifting?
   - *Trả lời:* Duy trì thêm bảng $\text{maxEdge}[u][i] = \max(\text{maxEdge}[u][i-1], \text{maxEdge}[\text{up}[u][i-1]][i-1])$ và gộp kết quả trong quá trình nâng $u, v$.
8. **Câu 8 (Hard):** Giải thích cách Tarjan Offline LCA sử dụng Disjoint Set Union (DSU) để đạt độ phức tạp $O(N + Q \cdot \alpha(N))$.
   - *Trả lời:* Duyệt Post-order, gộp cây con vào nút cha sau khi duyệt xong và gán đại diện $\text{ancestor}[\text{find}(u)] = u$. Với mỗi truy vấn có đỉnh kia đã BLACK, $\text{LCA} = \text{ancestor}[\text{find}(v)]$.
9. **Câu 9 (Hard):** Phân tích ưu và nhược điểm của việc dùng mảng 1 chiều `int[] upFlat` so với mảng 2 chiều `int[][] up` trên JVM 64-bit.
   - *Trả lời:* Mảng 1D giảm phân mảnh Heap, tiết kiệm header objects, tối ưu CPU Cache prefetching L1/L2, giảm GC pressure.
10. **Câu 10 (Hard):** Làm thế nào để kiểm tra nút $u$ có phải là tổ tiên của nút $v$ trong $O(1)$ mà không cần bảng nhảy?
    - *Trả lời:* Dùng thời gian Euler In/Out: $\text{tin}[u] \le \text{tin}[v] \land \text{tout}[u] \ge \text{tout}[v]$.
11. **Câu 11 (Hard):** Cho một tập hợp $K$ nút trên cây. Làm thế nào để tìm LCA chung của cả $K$ nút trong thời gian tối ưu?
    - *Trả lời:* Áp dụng tính chất kết hợp $\text{LCA}(u_1, \dots, u_k) = \text{LCA}(\text{LCA}(u_1, u_2), \dots)$ trong $O(K \log N)$ hoặc tìm nút có $\text{tin}$ nhỏ nhất và lớn nhất rồi lấy LCA của 2 nút đó trong $O(\log N)$.
12. **Câu 12 (Hard):** Làm thế nào để mở rộng Binary Lifting khi cây hỗ trợ thao tác gắn thêm lá động trực tuyến?
    - *Trả lời:* Khi thêm lá $v$ gắn vào cha $u$: $\text{depth}[v] = \text{depth}[u] + 1$, tính $\text{up}[v][i] = \text{up}[\text{up}[v][i-1]][i-1]$ trong $O(\log N)$ mà không cần tính lại toàn bộ cây.
13. **Câu 13 (Staff):** So sánh Heavy-Light Decomposition (HLD) và Binary Lifting khi giải quyết bài toán truy vấn và cập nhật động trên đường đi (Dynamic Path Queries & Updates).
    - *Trả lời:* Binary Lifting không hỗ trợ cập nhật trọng số cạnh hiệu quả ($O(N \log N)$ để rebuild); HLD phân rã cây thành các chuỗi nặng kết hợp Segment Tree cho phép cả cập nhật điểm/đoạn và truy vấn đường đi trong $O(\log^2 N)$ hoặc $O(\log N)$.
14. **Câu 14 (Staff):** Trình bày cách xây dựng Cây Rút Gọn (Virtual Tree / Auxiliary Tree) từ tập $K$ đỉnh được chọn bằng cách sắp xếp theo $\text{tin}$ và sử dụng LCA.
    - *Trả lời:* Sắp xếp $K$ đỉnh theo $\text{tin}$, đưa thêm LCA của các cặp liên tiếp vào tập, sắp xếp lại và dùng Stack để dựng cây con chỉ gồm $O(K)$ đỉnh trong $O(K \log K)$.
15. **Câu 15 (Staff):** Thuật toán `git merge-base` xử lý trường hợp Criss-Cross Merge (nhiều tổ tiên chung không so sánh được trên DAG) như thế nào?
    - *Trả lời:* Git tìm tập tất cả Minimal Common Ancestors trên DAG. Nếu có nhiều hơn 1, Git tạo một Virtual Merge Base bằng cách đệ quy merge các common ancestors đó lại trước.
16. **Câu 16 (Staff):** Làm thế nào để đạt độ phức tạp tiền xử lý $O(N)$ và truy vấn $O(1)$ cho bài toán LCA (Fischer-Heun RMQ)?
    - *Trả lời:* Chia mảng Euler Tour thành các block kích thước $\frac{1}{2} \log_2 N$. Dùng Sparse Table cho các block ($O(N)$) và mã hóa chuỗi $\pm 1$ nhị phân bằng Bitmask để tra bảng RMQ nội bộ block trong $O(1)$.
17. **Câu 17 (Staff):** Giải thích cơ chế Dominator Tree LCA trong việc thực hiện Loop Invariant Code Motion của HotSpot C2 JIT Compiler.
    - *Trả lời:* Một biểu thức bất biến trong vòng lặp có thể được nâng an toàn lên Immediate Dominator (idom) của Header vòng lặp nếu nó chi phối mọi lối thoát của vòng lặp.
18. **Câu 18 (Staff):** Tại sao trong mạng SDN quy mô lớn, việc xác định LCA của hai node biên (Edge Switches) lại quyết định dung lượng bộ nhớ định tuyến (TCAM)?
    - *Trả lời:* Chỉ các switch nằm trên đường dẫn từ $u, v$ lên LCA mới cần lưu quy tắc luồng (Flow Rules), các switch khác không bị ô nhiễm bảng định tuyến.
19. **Câu 19 (Staff):** Trong cây có $N = 10^7$ nút, làm thế nào để thiết kế dịch vụ LCA chịu tải 1 triệu QPS với độ trễ $P99 < 100\text{ns}$ trên một máy chủ đơn lẻ?
    - *Trả lời:* Sử dụng Euler Tour + RMQ với mảng Off-heap phẳng liên tục, tận dụng SIMD AVX-512 để xử lý batch query, loại bỏ hoàn toàn GC overhead và tận dụng Non-blocking Memory Mapped Files.
20. **Câu 20 (Staff):** Làm thế nào để tính tổng khoảng cách từ 1 nút tới tất cả các nút khác trên cây trong $O(N)$ bằng Rerooting DP kết hợp tính chất LCA?
    - *Trả lời:* DFS lần 1 tính kích thước cây con `subtreeSize[u]` và tổng khoảng cách từ gốc. DFS lần 2 đẩy gốc từ $u$ sang con $v$: $\text{totalDist}[v] = \text{totalDist}[u] - \text{subtreeSize}[v] + (N - \text{subtreeSize}[v])$.

---

## 21. Practice Problems Link

Khám phá toàn bộ 30 bài tập kinh điển kèm lời giải chi tiết tại:
👉 **[08-Lowest-Common-Ancestor-LCA-Problems.md](08-Lowest-Common-Ancestor-LCA-Problems.md)**

---

## 22. Pattern Recognition (Nhận Diện Mẫu Bài Toán)

```
+-------------------------------------------------------------------------------+
|                      DẤU HIỆU NHẬN BIẾT BÀI TOÁN SỬ DỤNG LCA                  |
+-------------------------------------------------------------------------------+
| 1. "Khoảng cách ngắn nhất giữa 2 đỉnh trên cây"                               |
| 2. "Kiểm tra đỉnh X có nằm trên đường đi từ U đến V hay không"                |
| 3. "Tìm đỉnh thứ K trên đường đi đơn giữa U và V"                             |
| 4. "Thực hiện cộng giá trị lên tất cả các cạnh/đỉnh trên đường đi (U, V)"     |
| 5. "Tìm người quản lý chung cấp thấp nhất trong sơ đồ tổ chức"                |
| 6. "Tìm tiền tố chung dài nhất (LCP) trên Trie Tree hoặc Suffix Tree"         |
+-------------------------------------------------------------------------------+
```

*Công thức kiểm tra nút $x$ nằm trên đường đi giữa $u$ và $v$:*
$$x \text{ nằm trên } (u, v) \iff \text{dist}(u, x) + \text{dist}(x, v) = \text{dist}(u, v)$$
$$\iff (\text{LCA}(u, x) = x \lor \text{LCA}(v, x) = x) \land (\text{depth}[x] \ge \text{depth}[\text{LCA}(u, v)])$$

---

## 23. Real Case Study: Git Merge-Base Algorithm

### 23.1. Bối cảnh
Trong hệ thống Git Monorepo quy mô hàng triệu commits (ví dụ Linux Kernel hay Chromium), thao tác `git merge` diễn ra liên tục. Đồ thị commit của Git không phải là một cây đơn giản mà là một **Directed Acyclic Graph (DAG)** do sự xuất hiện của các Merge Commits (có 2 hoặc nhiều commit cha).

```
      A --- B --- C (feature-1)
     / \ /
    X   X   <--- Criss-Cross Merge
     \ / \
      D --- E --- F (feature-2)
```

### 23.2. Thách thức Criss-Cross Merge
Khi hai nhánh được merge chéo nhau qua lại, tồn tại nhiều commit tổ tiên chung độc lập mà không có commit nào là con của commit kia (ví dụ cả $B$ và $D$ đều là Common Ancestor nhưng không so sánh được độ sâu).

### 23.3. Giải pháp của Git
1. **Tìm tập hợp các tổ tiên chung tối tiểu (Minimal Common Ancestors):** Dùng BFS từ 2 nhánh, tô màu commit theo bitmask. Loại bỏ mọi ancestor là tổ tiên của một ancestor khác trong tập.
2. **Virtual Merge Base:** Nếu còn lại $K > 1$ tổ tiên chung, Git tự động tạo một commit ảo trong bộ nhớ bằng cách thực hiện đệ quy thuật toán 3-way merge giữa các tổ tiên đó, sau đó lấy commit ảo này làm điểm tựa để 3-way merge hai nhánh chính.

---

## 24. Summary & Implementation Checklist

### 24.1. Quick Reference Formulas

$$\text{up}[u][i] = \text{up}[\text{up}[u][i-1]][i-1]$$
$$\text{dist}(u, v) = \text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[\text{LCA}(u, v)]$$
$$u \in \text{Anc}(v) \iff \text{tin}[u] \le \text{tin}[v] \land \text{tout}[u] \ge \text{tout}[v]$$

---

### 24.2. Implementation Checklist
- [x] Đã khởi tạo mảng nhảy với $\text{LOG} = \lceil \log_2 N \rceil + 1$.
- [x] Đã gán $\text{up}[root][i] = root$ để tránh trỏ ra ngoài mảng.
- [x] Đã xử lý trường hợp cơ sở $u == v$ sau khi cân bằng độ sâu.
- [x] Đã làm phẳng bộ nhớ 2D thành 1D `int[]` để tối ưu CPU Cache.
- [x] Đã kiểm tra không bị `StackOverflowError` trên dữ liệu cây suy biến.
- [x] Mọi chỉ số đầu vào đều được validate cẩn thận trong khoảng $[0, N-1]$.
