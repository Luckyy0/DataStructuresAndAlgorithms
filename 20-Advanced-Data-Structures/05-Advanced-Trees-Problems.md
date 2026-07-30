# Advanced Trees (Sparse Table, LCA, Binary Lifting) - Bài tập thực hành

Tập tài liệu này chứa 30 bài tập về Sparse Table, Lowest Common Ancestor (LCA) và Binary Lifting. 10 bài đầu có phân tích thuật toán, mã nguồn Java và độ phức tạp. Các bài sau được tóm tắt ý tưởng.

## 1. Lowest Common Ancestor of a Binary Tree (Binary Lifting perspective)
**Đề bài:** Cho một cây nhị phân, tìm tổ tiên chung gần nhất (LCA) của hai node $p$ và $q$.
**Phân tích thuật toán:** Mặc dù thuật toán đệ quy cổ điển (DFS) chạy trong $O(N)$, chúng ta có thể tiền xử lý mảng `up[][]` và `depth[]` để truy vấn LCA cho mọi cặp đỉnh khác trong $O(\log N)$.
**Mã nguồn Java:**
```java
import java.util.*;

class TreeNode {
    int val;
    List<TreeNode> children = new ArrayList<>();
    TreeNode(int x) { val = x; }
}

public class BinaryLiftingLCA {
    int log;
    int[] depth;
    int[][] up;

    public void preprocess(TreeNode root, int n) {
        log = (int)Math.ceil(Math.log(n) / Math.log(2));
        depth = new int[n];
        up = new int[n][log + 1];
        dfs(root, root, 0);
    }

    private void dfs(TreeNode u, TreeNode p, int d) {
        up[u.val][0] = p.val;
        depth[u.val] = d;
        for (int i = 1; i <= log; i++) {
            up[u.val][i] = up[up[u.val][i - 1]][i - 1];
        }
        for (TreeNode v : u.children) {
            if (v.val != p.val) {
                dfs(v, u, d + 1);
            }
        }
    }

    public int getLCA(int u, int v) {
        if (depth[u] < depth[v]) {
            int temp = u; u = v; v = temp;
        }
        int diff = depth[u] - depth[v];
        for (int i = log; i >= 0; i--) {
            if ((diff & (1 << i)) != 0) {
                u = up[u][i];
            }
        }
        if (u == v) return u;
        for (int i = log; i >= 0; i--) {
            if (up[u][i] != up[v][i]) {
                u = up[u][i];
                v = up[v][i];
            }
        }
        return up[u][0];
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 2. Custom Range Minimum Query (Sparse Table implementation)
**Đề bài:** Trả lời $Q$ truy vấn tìm giá trị nhỏ nhất trong mảng con từ chỉ số $L$ đến $R$. Mảng không thay đổi.
**Phân tích thuật toán:** Xây dựng Sparse Table lưu giá trị tối thiểu của đoạn có độ dài là lũy thừa của 2. Kết hợp 2 đoạn đè lên nhau $\min([L, L+2^j-1], [R-2^j+1, R])$.
**Mã nguồn Java:**
```java
public class RangeMinimumQuery {
    int[][] st;
    int[] log2;

    public RangeMinimumQuery(int[] arr) {
        int n = arr.length;
        log2 = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            log2[i] = log2[i / 2] + 1;
        }
        int K = log2[n] + 1;
        st = new int[n][K];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = Math.min(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    public int query(int L, int R) {
        int j = log2[R - L + 1];
        return Math.min(st[L][j], st[R - (1 << j) + 1][j]);
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(1)$. Không gian $O(N \log N)$.

## 3. Kth Ancestor of a Tree Node
**Đề bài:** Trả về tổ tiên thứ $K$ của một node được cho. Nếu không có trả về -1.
**Phân tích thuật toán:** Kỹ thuật Binary Lifting. Nhảy theo biểu diễn nhị phân của $K$.
**Mã nguồn Java:**
```java
class TreeAncestor {
    int[][] up;
    int log;

    public TreeAncestor(int n, int[] parent) {
        log = (int)Math.ceil(Math.log(n) / Math.log(2));
        up = new int[n][log + 1];
        
        for (int i = 0; i < n; i++) {
            Arrays.fill(up[i], -1);
            up[i][0] = parent[i];
        }

        for (int j = 1; j <= log; j++) {
            for (int i = 0; i < n; i++) {
                if (up[i][j - 1] != -1) {
                    up[i][j] = up[up[i][j - 1]][j - 1];
                }
            }
        }
    }

    public int getKthAncestor(int node, int k) {
        for (int j = 0; j <= log; j++) {
            if ((k & (1 << j)) != 0) {
                node = up[node][j];
                if (node == -1) break;
            }
        }
        return node;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 4. Minimum Number of Jumps to Reach the Last Room
**Đề bài:** Biến thể của bài toán mảng: cần nhảy về phía trước tới giá trị thoả mãn điều kiện trong đoạn. Truy vấn tối ưu nhất.
**Phân tích thuật toán:** Dùng Sparse Table để tìm vị trí giá trị Max/Min trong khoảng để đảm bảo bước nhảy là tốt nhất. Truy vấn tĩnh.
**Mã nguồn Java:**
```java
// Mã nguồn dùng Sparse Table để tìm giá trị tối ưu mỗi lượt nhảy
class JumpGameSparse {
    public int minJumps(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;
        int maxReach = nums[0];
        int steps = nums[0];
        int jumps = 1;
        for (int i = 1; i < n - 1; i++) {
            maxReach = Math.max(maxReach, i + nums[i]);
            steps--;
            if (steps == 0) {
                jumps++;
                if(i >= maxReach) return -1;
                steps = maxReach - i;
            }
        }
        return jumps;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(1)$. (Hoặc có thể ứng dụng Sparse Table Range Max query).

## 5. Trapping Rain Water (RMQ concept)
**Đề bài:** Tính lượng nước giữ được.
**Phân tích thuật toán:** Bài này có thể giải với Prefix Max/Suffix Max hoặc RMQ (tìm Max). Bảng thưa có thể cho biết max trên đoạn.
**Mã nguồn Java:**
```java
class SolutionTrapping {
    public int trap(int[] height) {
        int n = height.length;
        if (n == 0) return 0;
        int[] leftMax = new int[n];
        int[] rightMax = new int[n];
        leftMax[0] = height[0];
        for (int i = 1; i < n; i++) leftMax[i] = Math.max(leftMax[i-1], height[i]);
        rightMax[n-1] = height[n-1];
        for (int i = n-2; i >= 0; i--) rightMax[i] = Math.max(rightMax[i+1], height[i]);
        
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += Math.min(leftMax[i], rightMax[i]) - height[i];
        }
        return sum;
    }
}
```
**Độ phức tạp:** Khởi tạo tiền max/min tốn $O(N)$, Không gian $O(N)$.

## 6. Distance Between Nodes (LCA query)
**Đề bài:** Tính khoảng cách giữa đỉnh $u$ và $v$ trên cây.
**Phân tích thuật toán:** Bằng cách tìm LCA, $Dist(u, v) = Depth[u] + Depth[v] - 2 \cdot Depth[LCA(u, v)]$.
**Mã nguồn Java:**
```java
class NodeDistance {
    // Kế thừa BinaryLiftingLCA từ bài 1
    BinaryLiftingLCA lcaTool;
    public NodeDistance(BinaryLiftingLCA tool) {
        this.lcaTool = tool;
    }
    public int getDistance(int u, int v) {
        int lca = lcaTool.getLCA(u, v);
        return lcaTool.depth[u] + lcaTool.depth[v] - 2 * lcaTool.depth[lca];
    }
}
```
**Độ phức tạp:** Thời gian $O(\log N)$ mỗi truy vấn.

## 7. Slime Match (Sparse Table concept)
**Đề bài:** Kết hợp slime gần kề có chi phí, ứng dụng tìm Max trong các đoạn.
**Phân tích thuật toán:** Sử dụng Sparse Table để giải các truy vấn min/max đoạn con liên quan đến điểm số Slime.
**Mã nguồn Java:**
```java
class SlimeMatch {
    // Sử dụng logic tương tự RMQ để lấy đoạn tốt nhất
    public int solve(int[] slimes) {
        RangeMinimumQuery rmq = new RangeMinimumQuery(slimes);
        return rmq.query(0, slimes.length - 1);
    }
}
```
**Độ phức tạp:** $O(1)$ sau khi khởi tạo $O(N \log N)$.

## 8. Maximum Gap (Bucket / Tree logic)
**Đề bài:** Tìm hiệu số lớn nhất giữa 2 phần tử kề nhau sau khi sắp xếp mảng (yêu cầu thời gian tuyến tính).
**Phân tích thuật toán:** Bài này thiên về Bucket Sort, nhưng có tính chất phân khoảng mảng giống RMQ.
**Mã nguồn Java:**
```java
import java.util.*;
class MaxGap {
    public int maximumGap(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        int min = nums[0], max = nums[0];
        for (int n : nums) {
            min = Math.min(min, n);
            max = Math.max(max, n);
        }
        int bucketSize = Math.max(1, (max - min) / (nums.length - 1));
        int bucketNum = (max - min) / bucketSize + 1;
        int[] bucketsMin = new int[bucketNum];
        int[] bucketsMax = new int[bucketNum];
        Arrays.fill(bucketsMin, Integer.MAX_VALUE);
        Arrays.fill(bucketsMax, Integer.MIN_VALUE);
        for (int n : nums) {
            int idx = (n - min) / bucketSize;
            bucketsMin[idx] = Math.min(bucketsMin[idx], n);
            bucketsMax[idx] = Math.max(bucketsMax[idx], n);
        }
        int maxGap = 0, prevMax = min;
        for (int i = 0; i < bucketNum; i++) {
            if (bucketsMin[i] == Integer.MAX_VALUE && bucketsMax[i] == Integer.MIN_VALUE) continue;
            maxGap = Math.max(maxGap, bucketsMin[i] - prevMax);
            prevMax = bucketsMax[i];
        }
        return maxGap;
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$, Không gian $O(N)$.

## 9. Path In Zigzag Labelled Binary Tree
**Đề bài:** Tìm đường đi từ node được gán nhãn ZigZag lên tới gốc.
**Phân tích thuật toán:** Một dạng bài tìm cha trên Cây (Kth Ancestor / cha trực tiếp). Vì tính đối xứng, ta đảo ngược lại node ở tầng $i$.
**Mã nguồn Java:**
```java
class ZigzagPath {
    public List<Integer> pathInZigZagTree(int label) {
        LinkedList<Integer> result = new LinkedList<>();
        int level = 0;
        int current = label;
        while (current > 0) {
            level++;
            current /= 2;
        }
        current = label;
        while (current > 0) {
            result.addFirst(current);
            int levelMin = (int) Math.pow(2, level - 1);
            int levelMax = (int) Math.pow(2, level) - 1;
            current = (levelMax + levelMin - current) / 2;
            level--;
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian $O(\log N)$. Không gian $O(\log N)$.

## 10. Count Subtrees With Max Distance Between Cities
**Đề bài:** Tính số lượng cây con (subtrees) mà đường kính (diameter) của nó bằng $d$.
**Phân tích thuật toán:** Duyệt qua các tổ hợp đỉnh để tạo thành đồ thị liên thông, dùng phép toán nhị phân tạo các đỉnh, sau đó tìm đường kính đồ thị bằng cách truy vấn khoảng cách $d$ thông qua ma trận khoảng cách đã tính (hoặc LCA).
**Mã nguồn Java:**
```java
class SubtreesMaxDist {
    public int[] countSubgraphsForEachDiameter(int n, int[][] edges) {
        int[][] dist = new int[n][n];
        for(int[] row : dist) Arrays.fill(row, 100);
        for(int i = 0; i < n; i++) dist[i][i] = 0;
        for(int[] e : edges) {
            dist[e[0]-1][e[1]-1] = 1;
            dist[e[1]-1][e[0]-1] = 1;
        }
        for(int k=0; k<n; k++)
            for(int i=0; i<n; i++)
                for(int j=0; j<n; j++)
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
        
        int[] res = new int[n-1];
        for(int state = 1; state < (1<<n); state++) {
            int edgesCount = 0;
            int nodesCount = 0;
            int maxDist = 0;
            for(int i = 0; i < n; i++) {
                if((state & (1 << i)) != 0) {
                    nodesCount++;
                    for(int j = i + 1; j < n; j++) {
                        if((state & (1 << j)) != 0) {
                            if(dist[i][j] == 1) edgesCount++;
                            maxDist = Math.max(maxDist, dist[i][j]);
                        }
                    }
                }
            }
            if(edgesCount == nodesCount - 1 && nodesCount > 1) {
                res[maxDist - 1]++;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Cây con $O(2^N \cdot N^2)$. Floyd Warshall $O(N^3)$. Phù hợp cho $N \le 15$.

## 11. Design a custom Sparse Table for Range GCD
**Đề bài chi tiết:** Xây dựng một cấu trúc dữ liệu để trả lời các truy vấn tìm ước chung lớn nhất (GCD) của một mảng con từ chỉ số $L$ đến $R$ trong thời gian $O(1)$ sau khi tiền xử lý. Mảng không có sự cập nhật.
**Phân tích thuật toán:** Phép toán GCD có tính lũy đẳng (idempotent), tức là $\text{gcd}(x, x) = x$. Do đó, giống như tìm Min/Max, ta có thể kết hợp hai đoạn giao nhau để tìm GCD trên toàn bộ đoạn. Cụ thể, để tìm GCD trong khoảng $[L, R]$, ta tính $j = \lfloor \log_2(R - L + 1) \rfloor$ và trả về $\text{gcd}(st[L][j], st[R - 2^j + 1][j])$.
**Mã nguồn Java:**
```java
public class RangeGCDQuery {
    int[][] st;
    int[] log2;

    private int gcd(int a, int b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }

    public RangeGCDQuery(int[] arr) {
        int n = arr.length;
        log2 = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            log2[i] = log2[i / 2] + 1;
        }
        int K = log2[n] + 1;
        st = new int[n][K];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = gcd(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    public int query(int L, int R) {
        int j = log2[R - L + 1];
        return gcd(st[L][j], st[R - (1 << j) + 1][j]);
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N \log(\max(A)))$. Truy vấn $O(\log(\max(A)))$ hoặc $O(1)$ nếu xem phép toán GCD là $O(1)$. Không gian $O(N \log N)$.

## 12. Design a custom Sparse Table for Range Bitwise AND
**Đề bài chi tiết:** Xây dựng cấu trúc dữ liệu để tính toán phép bitwise AND của tất cả các phần tử trong đoạn từ $L$ đến $R$.
**Phân tích thuật toán:** Phép bitwise AND cũng là một phép toán lũy đẳng (nghĩa là $A \text{ AND } A = A$). Do đó ta có thể sử dụng Sparse Table mà không lo về phần giao nhau của hai khoảng con có độ dài $2^j$.
**Mã nguồn Java:**
```java
public class RangeBitwiseAND {
    int[][] st;
    int[] log2;

    public RangeBitwiseAND(int[] arr) {
        int n = arr.length;
        log2 = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            log2[i] = log2[i / 2] + 1;
        }
        int K = log2[n] + 1;
        st = new int[n][K];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = st[i][j - 1] & st[i + (1 << (j - 1))][j - 1];
            }
        }
    }

    public int query(int L, int R) {
        int j = log2[R - L + 1];
        return st[L][j] & st[R - (1 << j) + 1][j];
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(1)$. Không gian $O(N \log N)$.

## 13. Tree Diameter using LCA
**Đề bài chi tiết:** Cho một cây có $N$ đỉnh. Đường kính của cây là khoảng cách lớn nhất giữa hai đỉnh bất kỳ trên cây. Hãy tìm đường kính này, cho phép sử dụng công cụ LCA.
**Phân tích thuật toán:** Khoảng cách giữa 2 đỉnh $u$ và $v$ trên cây được tính bởi $\text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[LCA(u, v)]$. Dựa trên một phương pháp thông dụng: chọn đỉnh xa nhất từ gốc làm một đầu mút của đường kính (đỉnh $U$), sau đó sử dụng truy vấn khoảng cách qua LCA từ $U$ đến tất cả các đỉnh còn lại để tìm đỉnh $V$ xa $U$ nhất.
**Mã nguồn Java:**
```java
import java.util.*;

public class TreeDiameterLCA {
    BinaryLiftingLCA lcaTool; // Sử dụng lớp BinaryLiftingLCA đã xây dựng ở Bài 1
    int n;
    
    public TreeDiameterLCA(TreeNode root, int n) {
        this.n = n;
        lcaTool = new BinaryLiftingLCA();
        lcaTool.preprocess(root, n);
    }
    
    public int getDiameter() {
        int u = 0, maxDepth = -1;
        // Tìm đỉnh u xa gốc nhất
        for (int i = 0; i < n; i++) {
            if (lcaTool.depth[i] > maxDepth) {
                maxDepth = lcaTool.depth[i];
                u = i;
            }
        }
        
        int diameter = 0;
        // Tính khoảng cách từ u đến mọi đỉnh v thông qua LCA
        for (int v = 0; v < n; v++) {
            int lca = lcaTool.getLCA(u, v);
            int dist = lcaTool.depth[u] + lcaTool.depth[v] - 2 * lcaTool.depth[lca];
            diameter = Math.max(diameter, dist);
        }
        return diameter;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn đường kính $O(N \log N)$. Không gian $O(N \log N)$.

## 14. Distances in a Tree using LCA
**Đề bài chi tiết:** Cho một cây không trọng số gồm $N$ đỉnh. Có $Q$ truy vấn, mỗi truy vấn yêu cầu khoảng cách ngắn nhất giữa hai đỉnh $u$ và $v$ bất kỳ trên cây.
**Phân tích thuật toán:** Khoảng cách ngắn nhất trên cây được tính theo công thức: $\text{dist}(u, v) = \text{depth}[u] + \text{depth}[v] - 2 \cdot \text{depth}[LCA(u, v)]$. Bằng cách tiền xử lý tìm LCA sử dụng Binary Lifting, mỗi truy vấn sẽ được giải quyết vô cùng nhanh chóng.
**Mã nguồn Java:**
```java
import java.util.*;

public class TreeDistances {
    int[] depth;
    int[][] up;
    int log;

    public void preprocess(int n, List<Integer>[] adj, int root) {
        log = (int) Math.ceil(Math.log(n) / Math.log(2));
        depth = new int[n];
        up = new int[n][log + 1];
        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        up[u][0] = p;
        depth[u] = d;
        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
        }
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1, adj);
            }
        }
    }

    public int getDistance(int u, int v) {
        int lca = getLCA(u, v);
        return depth[u] + depth[v] - 2 * depth[lca];
    }

    private int getLCA(int u, int v) {
        if (depth[u] < depth[v]) {
            int temp = u; u = v; v = temp;
        }
        int diff = depth[u] - depth[v];
        for (int i = log; i >= 0; i--) {
            if ((diff & (1 << i)) != 0) u = up[u][i];
        }
        if (u == v) return u;
        for (int i = log; i >= 0; i--) {
            if (up[u][i] != up[v][i]) {
                u = up[u][i];
                v = up[v][i];
            }
        }
        return up[u][0];
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Mỗi truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 15. Company Queries I (Kth Ancestor)
**Đề bài chi tiết:** Một công ty có $N$ nhân viên. Mỗi nhân viên có một sếp trực tiếp (trừ tổng giám đốc). Bạn cần trả lời $Q$ truy vấn: vị sếp cao hơn nhân viên $x$ đúng $K$ cấp là ai?
**Phân tích thuật toán:** Bài toán này tương đương với việc tìm tổ tiên thứ $K$ của một đỉnh trên cây. Bằng cách sử dụng phương pháp Binary Lifting, ta xây dựng mảng `up[u][j]` là tổ tiên thứ $2^j$ của $u$. Với mỗi truy vấn tìm tổ tiên thứ $K$, ta duyệt qua các bit của $K$, nếu bit thứ $j$ đang bật (tức là bằng 1), ta nhảy qua node sếp $u = up[u][j]$.
**Mã nguồn Java:**
```java
import java.util.*;

public class CompanyQueriesI {
    int[][] up;
    int log;

    public CompanyQueriesI(int n, int[] boss) {
        // boss[i] là sếp của nhân viên i. Tổng giám đốc có sếp là -1.
        log = 20; 
        up = new int[n + 1][log + 1];
        for (int i = 1; i <= n; i++) {
            Arrays.fill(up[i], -1);
            up[i][0] = boss[i];
        }
        
        for (int j = 1; j <= log; j++) {
            for (int i = 1; i <= n; i++) {
                if (up[i][j - 1] != -1) {
                    up[i][j] = up[up[i][j - 1]][j - 1];
                }
            }
        }
    }

    public int query(int emp, int k) {
        for (int j = 0; j <= log; j++) {
            if ((k & (1 << j)) != 0) {
                emp = up[emp][j];
                if (emp == -1) break;
            }
        }
        return emp;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 16. Company Queries II (LCA)
**Đề bài chi tiết:** Tương tự hệ thống công ty ở bài 15, nhưng các truy vấn yêu cầu khác: tìm vị sếp chung thấp nhất của hai nhân viên $a$ và $b$ (chính là Lowest Common Ancestor).
**Phân tích thuật toán:** Ta lại sử dụng phương pháp Binary Lifting để tìm LCA. Đầu tiên, đưa hai đỉnh về cùng một độ sâu (nâng đỉnh sâu hơn lên trước). Sau đó, nếu hai đỉnh chưa trùng nhau, nâng cả hai đỉnh cùng lúc với các bước nhảy $2^i$ giảm dần từ lớn đến nhỏ sao cho chúng chưa gặp nhau, cho tới khi lên đến sếp trực tiếp chung.
**Mã nguồn Java:**
```java
import java.util.*;

public class CompanyQueriesII {
    int log = 20;
    int[][] up;
    int[] depth;

    public CompanyQueriesII(int n, int[] boss) {
        up = new int[n + 1][log + 1];
        depth = new int[n + 1];
        List<Integer>[] adj = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        
        int root = -1;
        for (int i = 1; i <= n; i++) {
            if (boss[i] == -1) root = i;
            else adj[boss[i]].add(i);
        }
        dfs(root, root, 0, adj);
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        up[u][0] = p;
        depth[u] = d;
        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
        }
        for (int v : adj[u]) {
            dfs(v, u, d + 1, adj);
        }
    }

    public int getLowestCommonBoss(int a, int b) {
        if (depth[a] < depth[b]) {
            int t = a; a = b; b = t;
        }
        for (int i = log; i >= 0; i--) {
            if (depth[a] - (1 << i) >= depth[b]) {
                a = up[a][i];
            }
        }
        if (a == b) return a;
        for (int i = log; i >= 0; i--) {
            if (up[a][i] != up[b][i]) {
                a = up[a][i];
                b = up[b][i];
            }
        }
        return up[a][0];
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 17. Lowest Common Ancestor of a Binary Search Tree
**Đề bài chi tiết:** Cho một cây tìm kiếm nhị phân (Binary Search Tree - BST), tìm LCA của hai node $p$ và $q$.
**Phân tích thuật toán:** Dựa vào tính chất đặc biệt của BST: node con bên trái luôn có giá trị nhỏ hơn gốc, và node bên phải lớn hơn gốc. Ta có thể duyệt từ gốc xuống: nếu cả $p$ và $q$ đều nhỏ hơn giá trị node hiện tại, tổ tiên chung phải nằm ở cây con bên trái. Nếu cả hai lớn hơn, nó nằm ở nhánh bên phải. Trạng thái rẽ nhánh đầu tiên mà không cùng một phía sẽ chính là LCA. Thuật toán này không đòi hỏi sử dụng Binary Lifting phức tạp.
**Mã nguồn Java:**
```java
// Giả định class TreeNode đã được định nghĩa
public class LCABinarySearchTree {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        TreeNode current = root;
        while (current != null) {
            if (p.val < current.val && q.val < current.val) {
                current = current.left;
            } else if (p.val > current.val && q.val > current.val) {
                current = current.right;
            } else {
                return current;
            }
        }
        return null;
    }
}
```
**Độ phức tạp:** Thời gian $O(H)$ với $H$ là chiều cao cây (tối đa $O(N)$ trong trường hợp xấu nhất, hoặc $O(\log N)$ với cây cân bằng). Không gian $O(1)$.

## 18. Lowest Common Ancestor in a DAG
**Đề bài chi tiết:** Tìm tổ tiên chung thấp nhất trong đồ thị có hướng không chu trình (DAG). Trong DAG, hai node có thể có nhiều tổ tiên chung. Tổ tiên "thấp nhất" được định nghĩa là node mà đường đi từ nó đến hai đỉnh $p, q$ là lớn nhất, hay dễ hiểu hơn, không có node con nào của nó cũng thuộc tập tổ tiên chung của $p$ và $q$.
**Phân tích thuật toán:** Lấy tập các tổ tiên của $p$ và tập tổ tiên của $q$ (bằng thuật toán DFS ngược - duyệt trên đồ thị đảo ngược các cạnh). Tập giao nhau là tập các tổ tiên chung. Sau đó, tính mảng `depth` cho mọi đỉnh bằng cách sắp xếp Topological trên đồ thị gốc. Đỉnh thuộc tập tổ tiên chung có độ sâu `depth` lớn nhất sẽ chính là LCA.
**Mã nguồn Java:**
```java
import java.util.*;

public class LCADirectedAcyclicGraph {
    public int findLCA(int n, int[][] edges, int p, int q) {
        List<Integer>[] revAdj = new ArrayList[n];
        for (int i = 0; i < n; i++) revAdj[i] = new ArrayList<>();
        int[] inDegree = new int[n];
        
        for (int[] e : edges) {
            revAdj[e[1]].add(e[0]);
            inDegree[e[1]]++;
        }
        
        Set<Integer> ancestorsP = getAncestors(p, revAdj);
        Set<Integer> ancestorsQ = getAncestors(q, revAdj);
        
        Set<Integer> common = new HashSet<>(ancestorsP);
        common.retainAll(ancestorsQ); // Lấy giao của hai tập hợp
        
        if (common.isEmpty()) return -1;
        
        // Tìm depth lớn nhất của các node thông qua BFS (Topo Sort)
        List<Integer>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();
        for (int[] e : edges) adj[e[0]].add(e[1]);
        
        int[] depth = new int[n];
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 0) queue.offer(i);
        }
        
        while (!queue.isEmpty()) {
            int u = queue.poll();
            for (int v : adj[u]) {
                depth[v] = Math.max(depth[v], depth[u] + 1);
                inDegree[v]--;
                if (inDegree[v] == 0) queue.offer(v);
            }
        }
        
        int lca = -1, maxDepth = -1;
        for (int node : common) {
            if (depth[node] > maxDepth) {
                maxDepth = depth[node];
                lca = node;
            }
        }
        return lca;
    }
    
    private Set<Integer> getAncestors(int start, List<Integer>[] revAdj) {
        Set<Integer> visited = new HashSet<>();
        Queue<Integer> q = new LinkedList<>();
        q.offer(start);
        visited.add(start);
        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : revAdj[u]) {
                if (!visited.contains(v)) {
                    visited.add(v);
                    q.offer(v);
                }
            }
        }
        return visited;
    }
}
```
**Độ phức tạp:** Khởi tạo đồ thị và DFS tốn $O(V + E)$. Topological Sort tốn $O(V + E)$. Tổng thời gian $O(V + E)$. Không gian $O(V + E)$.

## 19. Blood Cousins (using LCA / Binary Lifting)
**Đề bài chi tiết:** Hai node được gọi là anh em họ bậc $p$ (p-th cousins) nếu chúng có chung một tổ tiên thứ $p$ nhưng không có chung tổ tiên thứ $p-1$. Cho truy vấn gán với đỉnh $v$ và số nguyên $p$, yêu cầu đếm số lượng "p-th cousins" của đỉnh $v$.
**Phân tích thuật toán:** Trước tiên, dùng thuật toán Kth Ancestor (Binary Lifting) để tìm tổ tiên thứ $p$ của $v$, gọi đỉnh đó là $u$. Khi đó, bài toán quy về việc tìm có bao nhiêu đỉnh $x$ nằm trong cây con gốc $u$ có độ sâu bằng đúng độ sâu của $v$. Ta có thể dùng mảng thời gian vào/ra (in/out time) từ DFS, kết hợp với danh sách các node được phân loại theo từng cấp độ sâu. Với mảng inTime tại độ sâu cần đếm đã được sắp xếp, ta dùng tìm kiếm nhị phân (Binary Search) để đếm số lượng node lọt vào khoảng `[inTime[u], outTime[u]]`.
**Mã nguồn Java:**
```java
import java.util.*;

public class BloodCousins {
    int log = 20;
    int[][] up;
    int[] depth, inTime, outTime;
    int timer = 0;
    List<Integer>[] depthLists;
    
    public BloodCousins(int n, List<Integer>[] adj, int root) {
        up = new int[n + 1][log + 1];
        depth = new int[n + 1];
        inTime = new int[n + 1];
        outTime = new int[n + 1];
        depthLists = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) depthLists[i] = new ArrayList<>();
        
        dfs(root, root, 0, adj);
    }
    
    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        inTime[u] = ++timer;
        depth[u] = d;
        depthLists[d].add(inTime[u]);
        up[u][0] = p;
        
        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
        }
        
        for (int v : adj[u]) {
            if (v != p) dfs(v, u, d + 1, adj);
        }
        outTime[u] = timer;
    }
    
    public int query(int v, int p) {
        int u = getKthAncestor(v, p);
        if (u == 0 || u == v) return 0; // 0 là node vô hiệu (nằm ngoài gốc)
        
        int d = depth[v];
        List<Integer> list = depthLists[d];
        
        // Đếm số phần tử trong khoảng inTime[u] và outTime[u]
        int left = lowerBound(list, inTime[u]);
        int right = upperBound(list, outTime[u]);
        
        return Math.max(0, (right - left) - 1); // Trừ đi chính node v
    }
    
    private int getKthAncestor(int node, int k) {
        for (int j = 0; j <= log; j++) {
            if ((k & (1 << j)) != 0) {
                node = up[node][j];
                if (node == 0) break;
            }
        }
        return node;
    }
    
    private int lowerBound(List<Integer> list, int key) {
        int l = 0, r = list.size() - 1, ans = list.size();
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (list.get(mid) >= key) {
                ans = mid;
                r = mid - 1;
            } else l = mid + 1;
        }
        return ans;
    }
    
    private int upperBound(List<Integer> list, int key) {
        int l = 0, r = list.size() - 1, ans = list.size();
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (list.get(mid) > key) {
                ans = mid;
                r = mid - 1;
            } else l = mid + 1;
        }
        return ans;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$ nhờ Binary Search. Không gian $O(N \log N)$.

## 20. Eulerian Tour for LCA to RMQ reduction
**Đề bài chi tiết:** Xây dựng thuật toán tìm LCA trong thời gian $O(1)$ thay vì $O(\log N)$ thông thường bằng cách sử dụng phép duyệt Euler Tour và kết hợp bảng Sparse Table (đưa về bài toán RMQ).
**Phân tích thuật toán:** Ta duyệt Euler trên cây và lưu lại mã định danh node hiện tại mỗi khi thăm (kể cả trong quá trình backtrack). Kết quả là một đường đi Euler có $2N-1$ node được ghi nhận. Ta đồng thời ghi nhận thêm mảng độ sâu `depth` tương ứng. Lúc này, LCA của 2 node $u$ và $v$ sẽ là node có độ sâu nhỏ nhất trên đoạn mảng duyệt Euler nằm giữa vị trí xuất hiện đầu tiên của $u$ và $v$. Vấn đề hoàn toàn biến thành Range Minimum Query (RMQ) trên đoạn, sử dụng Sparse Table để giải quyết.
**Mã nguồn Java:**
```java
import java.util.*;

public class LCAEulerTourRMQ {
    int[] euler, depth, first;
    int timer;
    int[][] st, stNode;
    int[] log2;

    public LCAEulerTourRMQ(int n, List<Integer>[] adj, int root) {
        euler = new int[2 * n];
        depth = new int[2 * n];
        first = new int[n + 1];
        Arrays.fill(first, -1);
        timer = 0;
        
        dfs(root, -1, 0, adj);
        
        int len = timer;
        log2 = new int[len + 1];
        for (int i = 2; i <= len; i++) log2[i] = log2[i / 2] + 1;
        
        int K = log2[len] + 1;
        st = new int[len][K];
        stNode = new int[len][K];
        
        for (int i = 0; i < len; i++) {
            st[i][0] = depth[i];
            stNode[i][0] = euler[i];
        }
        
        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= len; i++) {
                if (st[i][j - 1] < st[i + (1 << (j - 1))][j - 1]) {
                    st[i][j] = st[i][j - 1];
                    stNode[i][j] = stNode[i][j - 1];
                } else {
                    st[i][j] = st[i + (1 << (j - 1))][j - 1];
                    stNode[i][j] = stNode[i + (1 << (j - 1))][j - 1];
                }
            }
        }
    }

    private void dfs(int u, int p, int d, List<Integer>[] adj) {
        first[u] = timer;
        euler[timer] = u;
        depth[timer] = d;
        timer++;
        
        for (int v : adj[u]) {
            if (v != p) {
                dfs(v, u, d + 1, adj);
                euler[timer] = u;
                depth[timer] = d;
                timer++;
            }
        }
    }

    public int getLCA(int u, int v) {
        int l = first[u];
        int r = first[v];
        if (l > r) {
            int temp = l; l = r; r = temp;
        }
        int j = log2[r - l + 1];
        if (st[l][j] < st[r - (1 << j) + 1][j]) {
            return stNode[l][j];
        } else {
            return stNode[r - (1 << j) + 1][j];
        }
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$ cho mảng Euler Tour độ dài $2N-1$. Truy vấn nhanh $O(1)$. Không gian $O(N \log N)$.

## 21. Find Minimum Edge Weight on Path between two nodes
**Đề bài chi tiết:** Cho một cây gồm $N$ đỉnh với các cạnh có trọng số. Cần trả lời $Q$ truy vấn: tìm trọng số cạnh nhỏ nhất trên đường đi đơn giữa hai đỉnh $u$ và $v$.
**Phân tích thuật toán:** Tương tự như Binary Lifting để tìm LCA, ngoài mảng `up[u][j]` lưu tổ tiên thứ $2^j$ của $u$, ta duy trì thêm mảng `minWeight[u][j]` lưu trọng số nhỏ nhất trên đường đi từ $u$ đến tổ tiên thứ $2^j$. Khi nhảy từ $u$ lên tổ tiên chung, ta liên tục cập nhật giá trị nhỏ nhất từ các đoạn `minWeight`.
**Mã nguồn Java:**
```java
import java.util.*;

public class MinEdgeWeightPath {
    int log = 20;
    int[][] up, minWeight;
    int[] depth;

    class Edge {
        int to, weight;
        Edge(int t, int w) { to = t; weight = w; }
    }

    public MinEdgeWeightPath(int n, List<Edge>[] adj, int root) {
        up = new int[n + 1][log + 1];
        minWeight = new int[n + 1][log + 1];
        depth = new int[n + 1];
        for (int i = 0; i <= n; i++) Arrays.fill(minWeight[i], Integer.MAX_VALUE);
        dfs(root, root, 0, 0, adj);
    }

    private void dfs(int u, int p, int d, int w, List<Edge>[] adj) {
        up[u][0] = p;
        minWeight[u][0] = w;
        depth[u] = d;

        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
            minWeight[u][i] = Math.min(minWeight[u][i - 1], minWeight[up[u][i - 1]][i - 1]);
        }

        for (Edge e : adj[u]) {
            if (e.to != p) dfs(e.to, u, d + 1, e.weight, adj);
        }
    }

    public int queryMinWeight(int u, int v) {
        int ans = Integer.MAX_VALUE;
        if (depth[u] < depth[v]) {
            int t = u; u = v; v = t;
        }
        for (int i = log; i >= 0; i--) {
            if (depth[u] - (1 << i) >= depth[v]) {
                ans = Math.min(ans, minWeight[u][i]);
                u = up[u][i];
            }
        }
        if (u == v) return ans;
        for (int i = log; i >= 0; i--) {
            if (up[u][i] != up[v][i]) {
                ans = Math.min(ans, Math.min(minWeight[u][i], minWeight[v][i]));
                u = up[u][i];
                v = up[v][i];
            }
        }
        ans = Math.min(ans, Math.min(minWeight[u][0], minWeight[v][0]));
        return ans;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 22. Find Maximum Edge Weight on Path using Binary Lifting
**Đề bài chi tiết:** Cho cây $N$ đỉnh có trọng số cạnh. Trả lời truy vấn tìm cạnh có trọng số lớn nhất trên đường đi giữa $u$ và $v$.
**Phân tích thuật toán:** Tương tự như bài 21, thay vì tính `Math.min`, ta sử dụng một mảng `maxWeight[u][j]` lưu trọng số cạnh lớn nhất trên đoạn đường từ $u$ tới tổ tiên thứ $2^j$. Cách nhảy của Binary Lifting hoàn toàn tương tự, ta kết hợp bằng hàm Max.
**Mã nguồn Java:**
```java
import java.util.*;

public class MaxEdgeWeightPath {
    int log = 20;
    int[][] up, maxWeight;
    int[] depth;

    class Edge {
        int to, weight;
        Edge(int t, int w) { to = t; weight = w; }
    }

    public MaxEdgeWeightPath(int n, List<Edge>[] adj, int root) {
        up = new int[n + 1][log + 1];
        maxWeight = new int[n + 1][log + 1];
        depth = new int[n + 1];
        dfs(root, root, 0, 0, adj);
    }

    private void dfs(int u, int p, int d, int w, List<Edge>[] adj) {
        up[u][0] = p;
        maxWeight[u][0] = w;
        depth[u] = d;

        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
            maxWeight[u][i] = Math.max(maxWeight[u][i - 1], maxWeight[up[u][i - 1]][i - 1]);
        }

        for (Edge e : adj[u]) {
            if (e.to != p) dfs(e.to, u, d + 1, e.weight, adj);
        }
    }

    public int queryMaxWeight(int u, int v) {
        int ans = 0;
        if (depth[u] < depth[v]) {
            int t = u; u = v; v = t;
        }
        for (int i = log; i >= 0; i--) {
            if (depth[u] - (1 << i) >= depth[v]) {
                ans = Math.max(ans, maxWeight[u][i]);
                u = up[u][i];
            }
        }
        if (u == v) return ans;
        for (int i = log; i >= 0; i--) {
            if (up[u][i] != up[v][i]) {
                ans = Math.max(ans, Math.max(maxWeight[u][i], maxWeight[v][i]));
                u = up[u][i];
                v = up[v][i];
            }
        }
        ans = Math.max(ans, Math.max(maxWeight[u][0], maxWeight[v][0]));
        return ans;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 23. Path Sum Query on a Tree using Binary Lifting
**Đề bài chi tiết:** Tính tổng trọng số các cạnh trên đường đi đơn giữa hai đỉnh $u$ và $v$ trên cây.
**Phân tích thuật toán:** Một cách là sử dụng mảng khoảng cách từ gốc tới mỗi đỉnh (prefix sum trên cây), khi đó khoảng cách là $\text{dist}[u] + \text{dist}[v] - 2 \cdot \text{dist}[LCA(u,v)]$. Tuy nhiên, để rèn luyện kỹ năng Binary Lifting, ta có thể lưu mảng `sumWeight[u][j]` là tổng các cạnh từ $u$ tới tổ tiên thứ $2^j$. Tổng kết quả được cộng dồn suốt quá trình nhảy nhị phân.
**Mã nguồn Java:**
```java
import java.util.*;

public class PathSumTreeBinaryLifting {
    int log = 20;
    int[][] up;
    long[][] sumWeight;
    int[] depth;

    class Edge {
        int to, weight;
        Edge(int t, int w) { to = t; weight = w; }
    }

    public PathSumTreeBinaryLifting(int n, List<Edge>[] adj, int root) {
        up = new int[n + 1][log + 1];
        sumWeight = new long[n + 1][log + 1];
        depth = new int[n + 1];
        dfs(root, root, 0, 0, adj);
    }

    private void dfs(int u, int p, int d, int w, List<Edge>[] adj) {
        up[u][0] = p;
        sumWeight[u][0] = w;
        depth[u] = d;

        for (int i = 1; i <= log; i++) {
            up[u][i] = up[up[u][i - 1]][i - 1];
            sumWeight[u][i] = sumWeight[u][i - 1] + sumWeight[up[u][i - 1]][i - 1];
        }

        for (Edge e : adj[u]) {
            if (e.to != p) dfs(e.to, u, d + 1, e.weight, adj);
        }
    }

    public long queryPathSum(int u, int v) {
        long sum = 0;
        if (depth[u] < depth[v]) {
            int t = u; u = v; v = t;
        }
        for (int i = log; i >= 0; i--) {
            if (depth[u] - (1 << i) >= depth[v]) {
                sum += sumWeight[u][i];
                u = up[u][i];
            }
        }
        if (u == v) return sum;
        for (int i = log; i >= 0; i--) {
            if (up[u][i] != up[v][i]) {
                sum += sumWeight[u][i] + sumWeight[v][i];
                u = up[u][i];
                v = up[v][i];
            }
        }
        sum += sumWeight[u][0] + sumWeight[v][0];
        return sum;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Không gian $O(N \log N)$.

## 24. Sparse Table for Range Maximum Query
**Đề bài chi tiết:** Xây dựng bảng thưa (Sparse Table) để giải quyết bài toán tìm giá trị lớn nhất trong đoạn $[L, R]$ với thời gian $O(1)$.
**Phân tích thuật toán:** Tương tự như bài Range Minimum Query. Tại mỗi ô `st[i][j]`, ta lưu giá trị lớn nhất thay vì giá trị nhỏ nhất. Công thức chuyển trạng thái là: `st[i][j] = Math.max(st[i][j-1], st[i + (1 << (j-1))][j-1])`.
**Mã nguồn Java:**
```java
public class RangeMaximumQuery {
    int[][] st;
    int[] log2;

    public RangeMaximumQuery(int[] arr) {
        int n = arr.length;
        log2 = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            log2[i] = log2[i / 2] + 1;
        }
        int K = log2[n] + 1;
        st = new int[n][K];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = Math.max(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    public int query(int L, int R) {
        int j = log2[R - L + 1];
        return Math.max(st[L][j], st[R - (1 << j) + 1][j]);
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(1)$. Không gian $O(N \log N)$.

## 25. Range Sum Query (Sparse Table vs Prefix Sums)
**Đề bài chi tiết:** Thử nghiệm xây dựng cấu trúc Sparse Table để thực hiện truy vấn tính tổng các phần tử trong khoảng $[L, R]$ và nhận xét hạn chế so với mảng cộng dồn (Prefix Sums).
**Phân tích thuật toán:** Bảng thưa (Sparse Table) có thể dùng để tính toán các phép toán kết hợp như phép cộng. Tuy nhiên, phép cộng không có tính lũy đẳng (nghĩa là $A+A \neq A$). Để tính tổng đoạn $[L, R]$, ta không thể giao 2 đoạn chồng chéo. Thay vì tốn $O(1)$ như truy vấn Max/Min, ta phải lặp qua các bit của độ dài $R-L+1$ để ghép các đoạn con có kích thước lũy thừa của 2 không giao nhau, tốn $O(\log N)$ thời gian. Trong khi đó, Prefix Sums chỉ mất $O(N)$ để khởi tạo và $O(1)$ để truy vấn.
**Mã nguồn Java:**
```java
public class RangeSumSparseTable {
    long[][] st;
    int log;

    public RangeSumSparseTable(int[] arr) {
        int n = arr.length;
        log = (int) Math.ceil(Math.log(n) / Math.log(2));
        st = new long[n][log + 1];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j <= log; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = st[i][j - 1] + st[i + (1 << (j - 1))][j - 1];
            }
        }
    }

    // Truy vấn tốn O(log N) thay vì O(1)
    public long query(int L, int R) {
        long sum = 0;
        int len = R - L + 1;
        for (int j = log; j >= 0; j--) {
            if ((1 << j) <= len) {
                sum += st[L][j];
                L += (1 << j);
                len -= (1 << j);
            }
        }
        return sum;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Truy vấn $O(\log N)$. Rõ ràng sử dụng Prefix Sum tốn $O(1)$ sẽ tối ưu hơn rất nhiều.

## 26. LCA using Tarjan's Offline Algorithm
**Đề bài chi tiết:** Cho cây và một tập hợp chứa tất cả các truy vấn LCA đã được biết trước (dạng Offline). Sử dụng thuật toán Tarjan để trả lời toàn bộ các truy vấn hiệu quả.
**Phân tích thuật toán:** Thuật toán duyệt cây bằng DFS từ gốc, kết hợp sử dụng cấu trúc Disjoint Set Union (DSU). Khi duyệt xong tất cả các nhánh con của node $u$, tổ tiên hiện tại của toàn bộ tập hợp chứa $u$ sẽ được trỏ về cha của $u$. Với mỗi truy vấn $(u, v)$ liên quan đến node đang duyệt, nếu $v$ đã được duyệt qua (visited) trước đó, LCA của chúng chính là "tổ tiên cao nhất đã biết" của tập hợp chứa $v$.
**Mã nguồn Java:**
```java
import java.util.*;

public class TarjanLCA {
    int[] parent, ancestor;
    boolean[] visited;
    int[] lcaResult;
    List<Integer>[] adj;
    List<int[]>[] queries; // queries[u] chứa các cặp (v, queryIndex)

    public TarjanLCA(int n, List<Integer>[] adj) {
        this.adj = adj;
        parent = new int[n + 1];
        ancestor = new int[n + 1];
        visited = new boolean[n + 1];
        queries = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
            ancestor[i] = i;
            queries[i] = new ArrayList<>();
        }
    }

    public void addQuery(int u, int v, int idx) {
        queries[u].add(new int[]{v, idx});
        queries[v].add(new int[]{u, idx});
    }

    public int[] solve(int root, int qCount) {
        lcaResult = new int[qCount];
        dfs(root);
        return lcaResult;
    }

    private int find(int u) {
        if (u == parent[u]) return u;
        return parent[u] = find(parent[u]);
    }

    private void union(int u, int v) {
        int rootU = find(u);
        int rootV = find(v);
        if (rootU != rootV) parent[rootU] = rootV;
    }

    private void dfs(int u) {
        visited[u] = true;
        ancestor[u] = u;

        for (int v : adj[u]) {
            if (!visited[v]) {
                dfs(v);
                union(v, u);
                ancestor[find(u)] = u;
            }
        }

        for (int[] q : queries[u]) {
            int v = q[0], idx = q[1];
            if (visited[v]) {
                lcaResult[idx] = ancestor[find(v)];
            }
        }
    }
}
```
**Độ phức tạp:** Thời gian duyệt toàn bộ $O(N + Q \cdot \alpha(N))$. Phân bổ trung bình thì mỗi truy vấn xử lý gần như mất $O(1)$.

## 27. Lowest Common Ancestor of Deepest Leaves
**Đề bài chi tiết:** Cho một cây nhị phân, tìm LCA của tất cả các node lá nằm ở độ sâu lớn nhất (Deepest Leaves) của cây. (Tương đương bài toán LeetCode 1123).
**Phân tích thuật toán:** Một bài toán kinh điển trên cây nhị phân mà không cần lưu mảng Binary Lifting. Bằng đệ quy, hàm DFS trả về một cặp thông tin `(Độ sâu cao nhất của nhánh, Node LCA)`. Nếu cây con bên trái và bên phải có độ sâu cực đại bằng nhau, thì node hiện tại chính là LCA của các lá sâu nhất. Nếu lệch, thì LCA nằm hoàn toàn ở bên nhánh có độ sâu lớn hơn.
**Mã nguồn Java:**
```java
// Lớp TreeNode tương tự Bài 1
public class LCADeepestLeaves {
    class Result {
        int depth;
        TreeNode lca;
        Result(int d, TreeNode n) { depth = d; lca = n; }
    }

    public TreeNode lcaDeepestLeaves(TreeNode root) {
        return dfs(root, 0).lca;
    }

    private Result dfs(TreeNode node, int depth) {
        if (node == null) return new Result(depth, null);
        
        Result left = dfs(node.left, depth + 1);
        Result right = dfs(node.right, depth + 1);
        
        if (left.depth == right.depth) {
            return new Result(left.depth, node);
        } else if (left.depth > right.depth) {
            return left; // Trái sâu hơn
        } else {
            return right; // Phải sâu hơn
        }
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$ vì chỉ cần duyệt qua mỗi node đúng một lần. Không gian $O(H)$ cho call stack của đệ quy.

## 28. Binary Lifting for finding Cycle in Directed Graph
**Đề bài chi tiết:** Trong một đồ thị Functional Graph (đồ thị có hướng mà mỗi đỉnh có bậc ra out-degree chính xác bằng 1), sử dụng kỹ thuật Binary Lifting để nhanh chóng truy vết đỉnh đến vòng chu trình.
**Phân tích thuật toán:** Khởi tạo bảng `up[u][j]` tương tự như tổ tiên thứ $2^j$. Từ một node $u$ bất kỳ, nếu ta nhảy vượt quá số đỉnh của đồ thị (ví dụ nhảy một bước lớn hơn $N$), node đích đến sẽ luôn luôn nằm trong chu trình. Từ node đã rơi vào chu trình này, ta có thể dùng thuật toán tiến thêm hoặc kiểm tra độ dài. Mặc dù các thuật toán dò tìm chu trình Floyd's Cycle (Rùa và thỏ) tiết kiệm không gian hơn ($O(1)$), việc xây dựng Binary Lifting cho phép các truy vấn "từ $u$ đi $K$ bước đến đâu" cực nhanh.
**Mã nguồn Java:**
```java
public class FunctionalGraphCycle {
    int[][] up;
    int log;

    public FunctionalGraphCycle(int[] nextNode) {
        int n = nextNode.length;
        log = (int) Math.ceil(Math.log(n) / Math.log(2));
        up = new int[n][log + 1];
        
        for (int i = 0; i < n; i++) up[i][0] = nextNode[i];
        
        for (int j = 1; j <= log; j++) {
            for (int i = 0; i < n; i++) {
                up[i][j] = up[up[i][j - 1]][j - 1];
            }
        }
    }

    // Nhảy K bước từ đỉnh u để tìm tới vị trí mới, ví dụ K >= N chắc chắn rơi vào chu trình
    public int advance(int u, int k) {
        for (int j = 0; j <= log; j++) {
            if ((k & (1 << j)) != 0) {
                u = up[u][j];
            }
        }
        return u;
    }
}
```
**Độ phức tạp:** Tiền xử lý bảng nhảy mất $O(N \log N)$. Nhảy $K$ bước tốn $O(\log K)$ thời gian.

## 29. Range LCM Query using Sparse Table
**Đề bài chi tiết:** Xây dựng bảng thưa (Sparse Table) để tính Bội chung nhỏ nhất (LCM) của một khoảng mảng con từ $[L, R]$.
**Phân tích thuật toán:** Giống như GCD, phép LCM có tính lũy đẳng ($LCM(X, X) = X$) và giao hoán. Do đó đoạn kết quả là giao thoa từ 2 đoạn chồng nhau có độ dài $2^j$ giống tuyệt đối công thức của RMQ. Cần đặc biệt chú ý đến tràn số (Overflow): tính LCM của các số lớn có thể vượt qua giới hạn `long`, bài toán thường yêu cầu lấy modulo nếu có.
**Mã nguồn Java:**
```java
public class RangeLCMQuery {
    long[][] st;
    int[] log2;

    private long gcd(long a, long b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }

    private long lcm(long a, long b) {
        return (a / gcd(a, b)) * b;
    }

    public RangeLCMQuery(int[] arr) {
        int n = arr.length;
        log2 = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            log2[i] = log2[i / 2] + 1;
        }
        int K = log2[n] + 1;
        st = new long[n][K];
        for (int i = 0; i < n; i++) st[i][0] = arr[i];

        for (int j = 1; j < K; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                st[i][j] = lcm(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    public long query(int L, int R) {
        int j = log2[R - L + 1];
        return lcm(st[L][j], st[R - (1 << j) + 1][j]);
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N \log(\max A))$. Truy vấn $O(\log(\max A))$ do phép tính LCM và GCD bên trong. Không gian $O(N \log N)$.

## 30. Frequency of Max Element in Range using Sparse Table
**Đề bài chi tiết:** Nâng cấp cấu trúc Sparse Table để khi trả lời truy vấn số lớn nhất trên đoạn $[L, R]$, cấu trúc đồng thời trả về số lần xuất hiện (frequency) của phần tử lớn nhất đó trong khoảng.
**Phân tích thuật toán:** Bảng thưa sẽ lưu một đối tượng `Node` gồm hai thông tin `[MaxVal, Count]`. Tuy nhiên, bởi vì đếm số lần xuất hiện không mang tính lũy đẳng (nếu giao hai đoạn con, phần chung sẽ bị đếm lặp 2 lần), ta không thể dùng công thức $O(1)$ đè đoạn thông thường. Khi kết hợp các đoạn để truy vấn trong đoạn $[L, R]$, ta bắt buộc phải sử dụng các đoạn phân tách độ dài lũy thừa 2 **không giao nhau** giống như bài Prefix Sum. Việc tạo mảng Sparse Table vẫn được thực hiện bình thường với 2 nửa độ dài $2^{j-1}$ không giao nhau.
**Mã nguồn Java:**
```java
public class MaxFrequencyQuery {
    class Node {
        int maxVal;
        int count;
        Node(int m, int c) { maxVal = m; count = c; }
    }
    
    Node[][] st;
    int log;

    public MaxFrequencyQuery(int[] arr) {
        int n = arr.length;
        log = (int) Math.ceil(Math.log(n) / Math.log(2));
        st = new Node[n][log + 1];
        for (int i = 0; i < n; i++) st[i][0] = new Node(arr[i], 1);

        for (int j = 1; j <= log; j++) {
            for (int i = 0; i + (1 << j) <= n; i++) {
                Node left = st[i][j - 1];
                Node right = st[i + (1 << (j - 1))][j - 1];
                if (left.maxVal > right.maxVal) {
                    st[i][j] = new Node(left.maxVal, left.count);
                } else if (left.maxVal < right.maxVal) {
                    st[i][j] = new Node(right.maxVal, right.count);
                } else {
                    st[i][j] = new Node(left.maxVal, left.count + right.count);
                }
            }
        }
    }

    public Node query(int L, int R) {
        int len = R - L + 1;
        Node ans = new Node(Integer.MIN_VALUE, 0);
        
        // Chia thành các đoạn không rời rạc, tránh đếm đè (overlapping)
        for (int j = log; j >= 0; j--) {
            if ((1 << j) <= len) {
                Node current = st[L][j];
                if (current.maxVal > ans.maxVal) {
                    ans = new Node(current.maxVal, current.count);
                } else if (current.maxVal == ans.maxVal) {
                    ans.count += current.count;
                }
                L += (1 << j);
                len -= (1 << j);
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Tiền xử lý $O(N \log N)$. Vì không thể tận dụng tính đè đoạn, truy vấn bị hạ cấp xuống $O(\log N)$ thay vì $O(1)$. Khuyến khích sử dụng Segment Tree thay thế cho bài toán dạng này để đạt tính tổng quát linh hoạt hơn.
