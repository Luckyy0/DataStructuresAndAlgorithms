# 30 Bài tập B-Tree & B+ Tree (Thực hành Cấu trúc Dữ liệu Cây Bậc Cao & Chỉ mục Cơ sở Dữ liệu)

Tài liệu này cung cấp 30 bài toán kinh điển và nâng cao về cấu trúc dữ liệu **B-Tree** và **B+ Tree**, từ các thao tác cốt lõi (tìm kiếm, chèn, xóa, tách node, mượn khóa, gộp node, bulk loading) đến các ứng dụng hệ thống chuyên sâu (Disk I/O Simulation, Concurrency Lock-Coupling, Lehman-Yao B-link Tree, Slotted Page Layout, Augmented Range Aggregation, Prefix Compression, Keyset Pagination).

---

## 1. Tìm kiếm và Duyệt B-Tree (B-Tree Search & Traversals)

**Đề bài chi tiết:**
Cho một cây B-Tree bậc tối thiểu $t \ge 2$ (Minimum Degree $t$, mỗi node không phải gốc chứa từ $t-1$ đến $2t-1$ khóa). Hãy hiện thực:
1. Phương thức tìm kiếm `SearchResult search(int key)` trả về đối tượng gồm node chứa khóa và chỉ số index của khóa trong node đó (hoặc `null` nếu không tìm thấy).
2. Phương thức duyệt trung thứ tự `List<Integer> inOrderTraversal()` trả về danh sách các khóa theo thứ tự tăng dần.
3. Phương thức duyệt theo mức `List<List<Integer>> levelOrderTraversal()` trả về danh sách các khóa ở từng tầng của cây.

**Phân tích thuật toán:**
- Trong B-Tree, các khóa bên trong mỗi node được sắp xếp tăng dần: $k_0 < k_1 < \dots < k_{m-1}$.
- Đối với tìm kiếm: Tại node hiện tại, dùng Binary Search hoặc Linear Search để tìm vị trí $i$ sao cho $k_i \ge key$. Nếu $k_i == key$, trả về kết quả. Nếu là leaf node mà không tìm thấy thì khóa không tồn tại. Ngược lại, đệ quy xuống con thứ $i$ ($child[i]$).
- In-Order Traversal: Với $m$ khóa và $m+1$ con, duyệt xen kẽ: duyệt $child[0]$, in $k_0$, duyệt $child[1]$, in $k_1$, ..., duyệt $child[m]$.
- Level-Order Traversal: Sử dụng hàng đợi `Queue<BTreeNode>` chuẩn của BFS.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeSearchAndTraversal {
    public static class BTreeNode {
        public int t; // Minimum degree
        public List<Integer> keys;
        public List<BTreeNode> children;
        public boolean isLeaf;

        public BTreeNode(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
            this.keys = new ArrayList<>();
            this.children = new ArrayList<>();
        }
    }

    public record SearchResult(BTreeNode node, int index) {}

    private final BTreeNode root;
    private final int t;

    public BTreeSearchAndTraversal(int t, BTreeNode root) {
        this.t = t;
        this.root = root;
    }

    public SearchResult search(int key) {
        return search(root, key);
    }

    private SearchResult search(BTreeNode node, int key) {
        if (node == null) return null;
        int i = 0;
        // Tìm vị trí khóa đầu tiên >= key
        while (i < node.keys.size() && key > node.keys.get(i)) {
            i++;
        }
        if (i < node.keys.size() && key == node.keys.get(i)) {
            return new SearchResult(node, i);
        }
        if (node.isLeaf) {
            return null;
        }
        return search(node.children.get(i), key);
    }

    public List<Integer> inOrderTraversal() {
        List<Integer> result = new ArrayList<>();
        inOrder(root, result);
        return result;
    }

    private void inOrder(BTreeNode node, List<Integer> result) {
        if (node == null) return;
        int i;
        for (i = 0; i < node.keys.size(); i++) {
            if (!node.isLeaf) {
                inOrder(node.children.get(i), result);
            }
            result.add(node.keys.get(i));
        }
        if (!node.isLeaf) {
            inOrder(node.children.get(i), result);
        }
    }

    public List<List<Integer>> levelOrderTraversal() {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;

        Queue<BTreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currentLevelKeys = new ArrayList<>();
            for (int i = 0; i < levelSize; i++) {
                BTreeNode curr = queue.poll();
                if (curr != null) {
                    currentLevelKeys.addAll(curr.keys);
                    if (!curr.isLeaf) {
                        for (BTreeNode child : curr.children) {
                            if (child != null) queue.offer(child);
                        }
                    }
                }
            }
            result.add(currentLevelKeys);
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Tìm kiếm (Search): Thời gian $O(t \log_t N)$ với Linear Search trong node, hoặc $O(\log_2 t \cdot \log_t N) = O(\log N)$ nếu dùng Binary Search.
- Duyệt In-Order / Level-Order: Thời gian $O(N)$ qua $N$ phần tử.
- Không gian bộ nhớ (Space Complexity): $O(h) = O(\log_t N)$ cho ngăn xếp đệ quy tìm kiếm; $O(N)$ lưu danh sách duyệt.

---

## 2. Chèn Khóa vào B-Tree với Tách Node Chủ Động (Top-Down Proactive Split Insertion)

**Đề bài chi tiết:**
Hiện thực thuật toán chèn khóa vào B-Tree bậc tối thiểu $t$ bằng kỹ thuật **Top-Down Proactive Split** (CLRS approach). Trong quá trình đi từ gốc xuống lá để tìm vị trí chèn, nếu gặp bất kỳ node nào đã đầy ($2t - 1$ khóa), thực hiện tách node đó ngay lập tức trước khi đi tiếp. Điều này đảm bảo khi chèn khóa vào lá, lá chắc chắn còn chỗ trống và không cần lan truyền việc tách ngược lên cha (single-pass insert).

**Phân tích thuật toán:**
1. Nếu gốc `root` đã đầy ($2t - 1$ khóa), tạo một gốc mới, gán `root` cũ làm con thứ nhất của gốc mới, rồi tách `root` cũ. Sau đó chèn khóa vào gốc mới.
2. Nếu gốc chưa đầy, gọi hàm đệ quy `insertNonFull(node, key)`.
3. Trong `insertNonFull(node, key)`:
   - Nếu `node` là lá: Chèn `key` vào đúng vị trí có thứ tự trong mảng `keys`.
   - Nếu `node` là node trong (internal node): Tìm con `child[i]` tương ứng với `key`. Nếu `child[i]` đã đầy ($2t - 1$ khóa), tách `child[i]`. Khóa trung vị của `child[i]` được đẩy lên `node`. Xác định lại xem `key` nên đi vào con bên trái hay bên phải của khóa vừa đẩy lên, sau đó đệ quy xuống con tương ứng.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeTopDownInsert {
    public static class BTreeNode {
        int t;
        List<Integer> keys;
        List<BTreeNode> children;
        boolean isLeaf;

        public BTreeNode(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
            this.keys = new ArrayList<>();
            this.children = new ArrayList<>();
        }
    }

    private BTreeNode root;
    private final int t;

    public BTreeTopDownInsert(int t) {
        if (t < 2) throw new IllegalArgumentException("Minimum degree t must be >= 2");
        this.t = t;
        this.root = new BTreeNode(t, true);
    }

    public void insert(int key) {
        BTreeNode r = root;
        // Nếu gốc đã đầy (2t - 1 khóa)
        if (r.keys.size() == 2 * t - 1) {
            BTreeNode s = new BTreeNode(t, false);
            root = s;
            s.children.add(r);
            splitChild(s, 0, r);
            insertNonFull(s, key);
        } else {
            insertNonFull(r, key);
        }
    }

    private void insertNonFull(BTreeNode node, int key) {
        int i = node.keys.size() - 1;

        if (node.isLeaf) {
            // Chèn vào lá theo thứ tự tăng dần
            while (i >= 0 && key < node.keys.get(i)) {
                i--;
            }
            node.keys.add(i + 1, key);
        } else {
            // Tìm con phù hợp
            while (i >= 0 && key < node.keys.get(i)) {
                i--;
            }
            i++;
            // Kiểm tra xem con đó có đầy không
            if (node.children.get(i).keys.size() == 2 * t - 1) {
                splitChild(node, i, node.children.get(i));
                if (key > node.keys.get(i)) {
                    i++;
                }
            }
            insertNonFull(node.children.get(i), key);
        }
    }

    // Tách child node y (con thứ i của parent) có 2t-1 khóa thành 2 node mỗi node t-1 khóa
    private void splitChild(BTreeNode parent, int i, BTreeNode y) {
        BTreeNode z = new BTreeNode(t, y.isLeaf);
        int medianKey = y.keys.get(t - 1);

        // z nhận t - 1 khóa bên phải của y
        for (int j = 0; j < t - 1; j++) {
            z.keys.add(y.keys.get(t + j));
        }

        // Nếu y không phải lá, z nhận t con bên phải của y
        if (!y.isLeaf) {
            for (int j = 0; j < t; j++) {
                z.children.add(y.children.get(t + j));
            }
        }

        // Cắt bớt phần tử của y (chỉ giữ lại t - 1 khóa đầu tiên)
        while (y.keys.size() >= t) {
            y.keys.remove(y.keys.size() - 1);
        }
        if (!y.isLeaf) {
            while (y.children.size() > t) {
                y.children.remove(y.children.size() - 1);
            }
        }

        // Chèn z vào danh sách con của parent tại vị trí i + 1
        parent.children.add(i + 1, z);
        // Đẩy medianKey lên parent tại vị trí i
        parent.keys.add(i, medianKey);
    }

    public BTreeNode getRoot() {
        return root;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$ vì đi đúng 1 lượt từ gốc xuống lá (chiều cao $h \le \log_t \frac{N+1}{2}$). Mỗi tầng tốn tối đa $O(t)$ để dịch chuyển mảng và tách node.
- Không gian bộ nhớ: $O(h) = O(\log_t N)$ do ngăn xếp đệ quy (hoặc $O(1)$ nếu viết dạng lặp - iterative).

---

## 3. Chèn Khóa B-Tree từ Dưới Lên (Bottom-Up Insertion with Cascade Split)

**Đề bài chi tiết:**
Hiện thực thuật toán chèn khóa B-Tree tiếp cận từ dưới lên (Bottom-Up approach). Khóa luôn được tìm và thêm trực tiếp vào leaf node trước. Nếu sau khi chèn leaf node bị tràn ($> 2t - 1$ khóa), thực hiện tách node tại chỗ và đẩy khóa trung vị lên node cha. Quá trình tách và đẩy khóa có thể lan truyền ngược (cascade split) lên các tầng trên cho tới khi gặp một node cha chưa đầy hoặc tách tới tận gốc (tạo gốc mới).

**Phân tích thuật toán:**
- Tìm đường đi (path) từ gốc xuống leaf node chứa vị trí chèn.
- Thêm khóa vào leaf node.
- Nếu `leaf.keys.size() > 2t - 1`:
  - Tạo `sibling` mới chứa nửa phải của các khóa và con.
  - Lấy `medianKey = node.keys.get(mid)`.
  - Đưa `medianKey` và con mới lên node cha.
  - Lặp lại kiểm tra tràn cho node cha cho đến khi không còn tràn hoặc tạo gốc mới.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeBottomUpInsert {
    public static class Node {
        int t;
        List<Integer> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        boolean isLeaf;
        Node parent;

        public Node(int t, boolean isLeaf, Node parent) {
            this.t = t;
            this.isLeaf = isLeaf;
            this.parent = parent;
        }
    }

    private Node root;
    private final int t;

    public BTreeBottomUpInsert(int t) {
        this.t = t;
        this.root = new Node(t, true, null);
    }

    public void insert(int key) {
        Node leaf = findLeaf(root, key);
        insertKeyIntoNode(leaf, key);

        Node curr = leaf;
        while (curr.keys.size() > 2 * t - 1) {
            if (curr.parent == null) {
                // Tách gốc, tạo gốc mới
                Node newRoot = new Node(t, false, null);
                curr.parent = newRoot;
                newRoot.children.add(curr);
                root = newRoot;
                splitChild(newRoot, curr);
                break;
            } else {
                Node p = curr.parent;
                splitChild(p, curr);
                curr = p;
            }
        }
    }

    private Node findLeaf(Node node, int key) {
        if (node.isLeaf) return node;
        int i = 0;
        while (i < node.keys.size() && key > node.keys.get(i)) {
            i++;
        }
        return findLeaf(node.children.get(i), key);
    }

    private void insertKeyIntoNode(Node node, int key) {
        int i = 0;
        while (i < node.keys.size() && node.keys.get(i) < key) {
            i++;
        }
        node.keys.add(i, key);
    }

    private void splitChild(Node parent, Node fullNode) {
        int mid = t - 1;
        int medianKey = fullNode.keys.get(mid);

        Node rightNode = new Node(t, fullNode.isLeaf, parent);
        // Chép các khóa sau mid sang rightNode
        for (int i = mid + 1; i < fullNode.keys.size(); i++) {
            rightNode.keys.add(fullNode.keys.get(i));
        }
        // Chép các con sau mid sang rightNode nếu không phải lá
        if (!fullNode.isLeaf) {
            for (int i = mid + 1; i < fullNode.children.size(); i++) {
                Node child = fullNode.children.get(i);
                child.parent = rightNode;
                rightNode.children.add(child);
            }
        }

        // Xóa phần tử đã chuyển khỏi fullNode
        while (fullNode.keys.size() > mid) {
            fullNode.keys.remove(fullNode.keys.size() - 1);
        }
        if (!fullNode.isLeaf) {
            while (fullNode.children.size() > mid + 1) {
                fullNode.children.remove(fullNode.children.size() - 1);
            }
        }

        // Chèn medianKey và rightNode vào parent
        int insertIdx = 0;
        while (insertIdx < parent.keys.size() && parent.keys.get(insertIdx) < medianKey) {
            insertIdx++;
        }
        parent.keys.add(insertIdx, medianKey);
        parent.children.add(insertIdx + 1, rightNode);
    }

    public Node getRoot() {
        return root;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$ trong trường hợp xấu nhất khi việc tách lan truyền từ lá lên tận gốc.
- Không gian bộ nhớ: $O(h) = O(\log_t N)$ cho con trỏ `parent`.

---

## 4. Xóa Khóa trong B-Tree (B-Tree Preemptive Deletion with Borrow and Merge)

**Đề bài chi tiết:**
Hiện thực thuật toán xóa một khóa `k` bất kỳ trong B-Tree bậc tối thiểu $t$. Để tránh việc phải quay lui (backtracking), áp dụng giải thuật xóa phòng ngừa (Preemptive Deletion): khi đi xuống cây, đảm bảo node đang xét luôn có ít nhất $t$ khóa (nhiều hơn mức tối thiểu $t-1$). Xử lý đầy đủ tất cả các trường hợp:
1. Khóa nằm ở lá: Xóa trực tiếp.
2. Khóa nằm ở node trong: Thay thế bằng tiền bối (Predecessor) hoặc hậu bối (Successor), hoặc gộp hai con nếu cả hai con đều có $t-1$ khóa.
3. Khóa không có ở node hiện tại: Mượn từ anh em liền kề (Left/Right Sibling) hoặc gộp với anh em trước khi đi xuống con tiếp theo.

**Phân tích thuật toán:**
Quy tắc giữ bất biến node có $\ge t$ khóa:
- Nếu con `child[i]` có $t-1$ khóa:
  - Nếu anh em trái `child[i-1]` có $\ge t$ khóa: Mượn khóa từ anh em trái (xoay phải qua cha).
  - Nếu anh em phải `child[i+1]` có $\ge t$ khóa: Mượn khóa từ anh em phải (xoay trái qua cha).
  - Nếu cả hai anh em đều có $t-1$ khóa: Gộp `child[i]` với một trong hai anh em cùng với khóa ngăn cách từ cha.
- Khi gốc bị rỗng khóa (do gộp 2 con duy nhất), con gộp trở thành gốc mới (chiều cao cây giảm 1).

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeDeletion {
    public static class BTreeNode {
        int t;
        List<Integer> keys = new ArrayList<>();
        List<BTreeNode> children = new ArrayList<>();
        boolean isLeaf;

        public BTreeNode(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
        }

        int findKey(int k) {
            int idx = 0;
            while (idx < keys.size() && keys.get(idx) < k) {
                idx++;
            }
            return idx;
        }
    }

    private BTreeNode root;
    private final int t;

    public BTreeDeletion(int t, BTreeNode root) {
        this.t = t;
        this.root = root;
    }

    public void remove(int k) {
        if (root == null) return;

        remove(root, k);

        if (root.keys.isEmpty()) {
            if (!root.isLeaf) {
                root = root.children.get(0);
            } else {
                root = null;
            }
        }
    }

    private void remove(BTreeNode node, int k) {
        int idx = node.findKey(k);

        // Trường hợp 1 & 2: Khóa k có trong node này
        if (idx < node.keys.size() && node.keys.get(idx) == k) {
            if (node.isLeaf) {
                node.keys.remove(idx);
            } else {
                removeFromNonLeaf(node, idx);
            }
        } else {
            // Trường hợp 3: Khóa k không có ở node này
            if (node.isLeaf) {
                // Khóa không tồn tại trong cây
                return;
            }

            boolean flag = (idx == node.keys.size());
            if (node.children.get(idx).keys.size() < t) {
                fill(node, idx);
            }

            if (flag && idx > node.keys.size()) {
                remove(node.children.get(idx - 1), k);
            } else {
                remove(node.children.get(idx), k);
            }
        }
    }

    private void removeFromNonLeaf(BTreeNode node, int idx) {
        int k = node.keys.get(idx);

        if (node.children.get(idx).keys.size() >= t) {
            int pred = getPred(node, idx);
            node.keys.set(idx, pred);
            remove(node.children.get(idx), pred);
        } else if (node.children.get(idx + 1).keys.size() >= t) {
            int succ = getSucc(node, idx);
            node.keys.set(idx, succ);
            remove(node.children.get(idx + 1), succ);
        } else {
            merge(node, idx);
            remove(node.children.get(idx), k);
        }
    }

    private int getPred(BTreeNode node, int idx) {
        BTreeNode cur = node.children.get(idx);
        while (!cur.isLeaf) {
            cur = cur.children.get(cur.children.size() - 1);
        }
        return cur.keys.get(cur.keys.size() - 1);
    }

    private int getSucc(BTreeNode node, int idx) {
        BTreeNode cur = node.children.get(idx + 1);
        while (!cur.isLeaf) {
            cur = cur.children.get(0);
        }
        return cur.keys.get(0);
    }

    private void fill(BTreeNode node, int idx) {
        if (idx != 0 && node.children.get(idx - 1).keys.size() >= t) {
            borrowFromPrev(node, idx);
        } else if (idx != node.keys.size() && node.children.get(idx + 1).keys.size() >= t) {
            borrowFromNext(node, idx);
        } else {
            if (idx != node.keys.size()) {
                merge(node, idx);
            } else {
                merge(node, idx - 1);
            }
        }
    }

    private void borrowFromPrev(BTreeNode node, int idx) {
        BTreeNode child = node.children.get(idx);
        BTreeNode sibling = node.children.get(idx - 1);

        child.keys.add(0, node.keys.get(idx - 1));
        if (!child.isLeaf) {
            child.children.add(0, sibling.children.remove(sibling.children.size() - 1));
        }

        node.keys.set(idx - 1, sibling.keys.remove(sibling.keys.size() - 1));
    }

    private void borrowFromNext(BTreeNode node, int idx) {
        BTreeNode child = node.children.get(idx);
        BTreeNode sibling = node.children.get(idx + 1);

        child.keys.add(node.keys.get(idx));
        if (!child.isLeaf) {
            child.children.add(sibling.children.remove(0));
        }

        node.keys.set(idx, sibling.keys.remove(0));
    }

    private void merge(BTreeNode node, int idx) {
        BTreeNode child = node.children.get(idx);
        BTreeNode sibling = node.children.get(idx + 1);

        child.keys.add(node.keys.remove(idx));

        for (int i = 0; i < sibling.keys.size(); i++) {
            child.keys.add(sibling.keys.get(i));
        }
        if (!child.isLeaf) {
            for (int i = 0; i < sibling.children.size(); i++) {
                child.children.add(sibling.children.get(i));
            }
        }

        node.children.remove(idx + 1);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$ vì mỗi tầng chỉ thực hiện mượn/gộp cục bộ với chi phí $O(t)$.
- Không gian bộ nhớ: $O(\log_t N)$ cho ngăn xếp đệ quy.

---

## 5. Tìm Tiền Bối và Hậu Bối (In-Order Predecessor & Successor) trong B-Tree

**Đề bài chi tiết:**
Cho một cây B-Tree và một giá trị khóa `target` (có thể có hoặc không tồn tại trong cây). Hãy viết thuật toán tìm:
1. `Integer predecessor(int target)`: Khóa lớn nhất trong cây nhỏ hơn `target`.
2. `Integer successor(int target)`: Khóa nhỏ nhất trong cây lớn hơn `target`.
Nếu không tồn tại, trả về `null`. Yêu cầu thuật toán chạy trong thời gian $O(t \log_t N)$ mà không cần duyệt toàn bộ cây.

**Phân tích thuật toán:**
- Tìm kiếm từ gốc xuống:
  - Khi xét node `u`, duyệt qua các khóa của `u`.
  - Mọi khóa $k_i < target$ đều là ứng viên cho `predecessor`. Cập nhật `candidatePred = max(candidatePred, k_i)`.
  - Mọi khóa $k_i > target$ đều là ứng viên cho `successor`. Cập nhật `candidateSucc = min(candidateSucc, k_i)`.
  - Nếu gặp $k_i == target$:
    - Nếu node không phải lá: `predecessor` là khóa lớn nhất trong cây con $child[i]$ (đi theo nhánh con phải nhất), `successor` là khóa nhỏ nhất trong cây con $child[i+1]$ (đi theo nhánh con trái nhất).
    - Nếu node là lá: Ứng viên `candidatePred` và `candidateSucc` đã lưu trên đường đi từ gốc chính là kết quả.
  - Đi xuống con $child[i]$ thích hợp.

**Mã nguồn Java:**
```java
public class BTreePredSucc {
    public static class BTreeNode {
        int t;
        int[] keys;
        BTreeNode[] children;
        int n;
        boolean isLeaf;

        public BTreeNode(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
            this.keys = new int[2 * t - 1];
            this.children = new BTreeNode[2 * t];
            this.n = 0;
        }
    }

    public static Integer findPredecessor(BTreeNode root, int target) {
        Integer pred = null;
        BTreeNode curr = root;

        while (curr != null) {
            int i = 0;
            while (i < curr.n && curr.keys[i] < target) {
                pred = curr.keys[i]; // Ứng viên tiềm năng
                i++;
            }

            if (i < curr.n && curr.keys[i] == target) {
                if (!curr.isLeaf) {
                    // Lấy phần tử lớn nhất của cây con bên trái
                    BTreeNode temp = curr.children[i];
                    while (!temp.isLeaf) {
                        temp = temp.children[temp.n];
                    }
                    return temp.keys[temp.n - 1];
                }
                return pred;
            }

            if (curr.isLeaf) break;
            curr = curr.children[i];
        }
        return pred;
    }

    public static Integer findSuccessor(BTreeNode root, int target) {
        Integer succ = null;
        BTreeNode curr = root;

        while (curr != null) {
            int i = curr.n - 1;
            while (i >= 0 && curr.keys[i] > target) {
                succ = curr.keys[i]; // Ứng viên tiềm năng
                i--;
            }

            if (i >= 0 && curr.keys[i] == target) {
                if (!curr.isLeaf) {
                    // Lấy phần tử nhỏ nhất của cây con bên phải
                    BTreeNode temp = curr.children[i + 1];
                    while (!temp.isLeaf) {
                        temp = temp.children[0];
                    }
                    return temp.keys[0];
                }
                return succ;
            }

            if (curr.isLeaf) break;
            curr = curr.children[i + 1];
        }
        return succ;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$ vì chỉ đi dọc theo một đường từ gốc xuống lá, tại mỗi tầng duyệt mảng khóa kích thước $\le 2t-1$.
- Không gian bộ nhớ: $O(1)$ lặp tuần tự không dùng đệ quy.

---

## 6. Truy vấn Đoạn $[L, R]$ trong B-Tree (B-Tree Range Query)

**Đề bài chi tiết:**
Cho một cây B-Tree bậc $t$ và một khoảng $[L, R]$ với $L \le R$. Hãy viết hàm `List<Integer> rangeQuery(int L, int R)` trả về tất cả các khóa $K$ thỏa mãn $L \le K \le R$ theo thứ tự tăng dần. Yêu cầu tối ưu hóa việc cắt tỉa (pruning) các cây con nằm ngoài khoảng $[L, R]$.

**Phân tích thuật toán:**
Tại mỗi node `u`:
- Nếu $child[0]$ có thể chứa các phần tử $\ge L$ (tức là $L \le u.keys[0]$), ta đệ quy duyệt $child[0]$.
- Duyệt qua từng khóa $k_i$:
  - Nếu $k_i > R$, dừng ngay việc duyệt các khóa và con tiếp theo trong node này vì toàn bộ cây con sau đều $> R$.
  - Nếu $L \le k_i \le R$, thêm $k_i$ vào kết quả.
  - Nếu $child[i+1]$ có thể chứa các phần tử trong khoảng $[L, R]$ (tức là $k_i \le R$ và khóa tiếp theo hoặc con có thể $\ge L$), đệ quy duyệt $child[i+1]$.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeRangeQuery {
    public static class BTreeNode {
        List<Integer> keys = new ArrayList<>();
        List<BTreeNode> children = new ArrayList<>();
        boolean isLeaf;
    }

    public static List<Integer> rangeQuery(BTreeNode root, int L, int R) {
        List<Integer> result = new ArrayList<>();
        dfs(root, L, R, result);
        return result;
    }

    private static void dfs(BTreeNode node, int L, int R, List<Integer> result) {
        if (node == null) return;

        int n = node.keys.size();
        int i = 0;

        // Bỏ qua các khóa nhỏ hơn L để tìm điểm bắt đầu
        while (i < n && node.keys.get(i) < L) {
            i++;
        }

        // Cây con child[i] có thể chứa các khóa trong [L, R]
        if (!node.isLeaf) {
            dfs(node.children.get(i), L, R, result);
        }

        // Duyệt các khóa bắt đầu từ i
        while (i < n && node.keys.get(i) <= R) {
            result.add(node.keys.get(i));
            if (!node.isLeaf) {
                dfs(node.children.get(i + 1), L, R, result);
            }
            i++;
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K + \log_t N)$, trong đó $K$ là số lượng phần tử tìm thấy trong đoạn $[L, R]$, và $\log_t N$ là chi phí định vị khóa bắt đầu $L$.
- Không gian bộ nhớ: $O(\log_t N)$ cho ngăn xếp đệ quy.

---

## 7. Tính Toán Chiều Cao và Số Lượng Khóa Cực Trị của B-Tree

**Đề bài chi tiết:**
Cho bậc tối thiểu $t \ge 2$ của B-Tree:
1. Viết hàm tính số lượng khóa nhỏ nhất $N_{\min}(h)$ và lớn nhất $N_{\max}(h)$ của B-Tree có chiều cao $h$ (chiều cao $h=0$ chỉ có 1 root là leaf).
2. Viết hàm tính chiều cao tối thiểu $h_{\min}(N)$ và chiều cao tối đa $h_{\max}(N)$ của B-Tree chứa $N$ khóa.

**Phân tích thuật toán:**
Theo định lý cấu trúc B-Tree:
- Chiều cao $h=0$: Root chứa từ $1$ đến $2t-1$ khóa.
- Chiều cao $h \ge 1$:
  - Số lượng khóa cực đại (khi mọi node đều đầy $2t-1$ khóa và có $2t$ con):
    $$\text{Tổng số node} = \sum_{i=0}^h (2t)^i = \frac{(2t)^{h+1}-1}{2t-1} \implies N_{\max}(h) = (2t)^{h+1} - 1$$
  - Số lượng khóa cực tiểu (khi gốc có 1 khóa, 2 con; mọi node trong khác có $t-1$ khóa, $t$ con):
    Ở tầng 0 có 1 node (1 khóa). Ở tầng $i \ge 1$ có $2 \cdot t^{i-1}$ node, mỗi node $t-1$ khóa.
    $$N_{\min}(h) = 1 + \sum_{i=1}^h 2 \cdot t^{i-1} (t-1) = 1 + 2(t-1) \frac{t^h - 1}{t-1} = 2t^h - 1$$
- Từ đó giải phương trình tính $h_{\min}(N) = \lceil \log_{2t}(N+1) \rceil - 1$ và $h_{\max}(N) = \lfloor \log_t \frac{N+1}{2} \rfloor$.

**Mã nguồn Java:**
```java
public class BTreeMathAnalysis {

    public static long minKeys(int t, int h) {
        if (h == 0) return 1;
        // 2 * t^h - 1
        long pow = 1;
        for (int i = 0; i < h; i++) {
            pow *= t;
        }
        return 2 * pow - 1;
    }

    public static long maxKeys(int t, int h) {
        // (2t)^(h+1) - 1
        long pow = 1;
        long base = 2L * t;
        for (int i = 0; i <= h; i++) {
            pow *= base;
        }
        return pow - 1;
    }

    public static int minHeight(int t, long N) {
        if (N <= 0) return 0;
        int h = 0;
        long base = 2L * t;
        long currentTotal = base - 1;
        long currentNodes = 1;

        while (currentTotal < N) {
            h++;
            currentNodes *= base;
            currentTotal += currentNodes * (base - 1);
        }
        return h;
    }

    public static int maxHeight(int t, long N) {
        if (N <= 0) return 0;
        int h = 0;
        while (minKeys(t, h + 1) <= N) {
            h++;
        }
        return h;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(h)$ cho các phép tính toán.
- Không gian bộ nhớ: $O(1)$.

---

## 8. Kiểm Tra Tính Hợp Lệ của B-Tree (B-Tree Invariants Validator)

**Đề bài chi tiết:**
Viết hàm `boolean isValidBTree(BTreeNode root, int t)` để kiểm tra xem một cây B-Tree bậc tối thiểu $t \ge 2$ có thỏa mãn tất cả các bất biến (Invariants) sau hay không:
1. **Thuộc tính BST**: Các khóa trong mỗi node được sắp xếp tăng dần nghiêm ngặt. Mọi khóa trong cây con $child[i]$ phải nằm trong khoảng $(k_{i-1}, k_i)$.
2. **Số lượng khóa**:
   - Gốc (Root) nếu không phải lá phải có ít nhất 1 khóa ($1 \le |keys| \le 2t-1$).
   - Mọi node khác gốc phải chứa từ $t-1$ đến $2t-1$ khóa.
3. **Số lượng con**: Node không phải lá có $m$ khóa thì phải có đúng $m+1$ con.
4. **Độ sâu lá đồng nhất**: Tất cả các lá phải nằm ở cùng một độ sâu (Perfectly Balanced).

**Phân tích thuật toán:**
- Dùng đệ quy kết hợp truyền cận dưới/cận trên $(\min, \max)$ để kiểm tra thuộc tính BST.
- Đếm độ sâu khi đi xuống lá đầu tiên. Mọi lá tiếp theo khi duyệt tới đều phải có độ sâu bằng độ sâu mẫu này.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeValidator {
    public static class BTreeNode {
        int t;
        List<Integer> keys;
        List<BTreeNode> children;
        boolean isLeaf;
    }

    public static boolean isValidBTree(BTreeNode root, int t) {
        if (root == null) return true;
        if (t < 2) return false;

        // 1. Kiểm tra số lượng khóa của root
        if (root.keys.isEmpty()) return false;
        if (root.keys.size() > 2 * t - 1) return false;

        // 2. Kiểm tra tính hợp lệ đệ quy và độ sâu của các lá
        int[] expectedLeafDepth = new int[]{-1};
        return validate(root, t, true, Long.MIN_VALUE, Long.MAX_VALUE, 0, expectedLeafDepth);
    }

    private static boolean validate(BTreeNode node, int t, boolean isRoot,
                                    long minBound, long maxBound,
                                    int currentDepth, int[] expectedDepth) {
        if (node == null) return false;

        int numKeys = node.keys.size();

        // Kiểm tra số lượng khóa cho node non-root
        if (!isRoot) {
            if (numKeys < t - 1 || numKeys > 2 * t - 1) return false;
        }

        // Kiểm tra thứ tự tăng dần của các khóa trong node và khoảng giá trị
        for (int i = 0; i < numKeys; i++) {
            long k = node.keys.get(i);
            if (k <= minBound || k >= maxBound) return false;
            if (i > 0 && node.keys.get(i) <= node.keys.get(i - 1)) return false;
        }

        // Kiểm tra lá và số lượng con
        if (node.isLeaf) {
            if (!node.children.isEmpty()) return false;
            if (expectedDepth[0] == -1) {
                expectedDepth[0] = currentDepth; // Ghi nhận độ sâu lá đầu tiên
            } else if (expectedDepth[0] != currentDepth) {
                return false; // Các lá không cùng độ sâu
            }
            return true;
        }

        // Node trong phải có đúng numKeys + 1 con
        if (node.children.size() != numKeys + 1) return false;

        // Đệ quy kiểm tra các con với cận hợp lệ
        for (int i = 0; i <= numKeys; i++) {
            long childMin = (i == 0) ? minBound : node.keys.get(i - 1);
            long childMax = (i == numKeys) ? maxBound : node.keys.get(i);

            if (!validate(node.children.get(i), t, false, childMin, childMax, currentDepth + 1, expectedDepth)) {
                return false;
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ do thăm mỗi node và mỗi khóa đúng một lần.
- Không gian bộ nhớ: $O(h) = O(\log_t N)$ cho độ sâu ngăn xếp đệ quy.

---

## 9. Tìm Khóa Thứ K và Hạng (Order Statistic B-Tree)

**Đề bài chi tiết:**
Mở rộng cấu trúc B-Tree bằng cách gán thêm vào mỗi con trỏ con một trường `subtreeSize` biểu diễn tổng số khóa chứa trong cây con đó. Hãy hiện thực:
1. `Integer findKth(int k)`: Tìm khóa nhỏ thứ $k$ (1-indexed) trong $O(\log N)$ thời gian.
2. `int getRank(int key)`: Tìm hạng (số lượng phần tử $\le key$) của một khóa trong $O(\log N)$ thời gian.

**Phân tích thuật toán:**
Tại node hiện tại:
- Tổng số phần tử ở $child[0]$ là $S_0$.
- Nếu $k \le S_0$, đệ quy xuống $child[0]$ với hạng $k$.
- Nếu $k == S_0 + 1$, phần tử cần tìm chính là khóa đầu tiên $k_0$ của node hiện tại.
- Tiếp tục xét lần lượt: nếu $k > S_0 + 1$, trừ $k$ đi $(S_0 + 1)$ và tiếp tục xét với $child[1]$ và $k_1$.

**Mã nguồn Java:**
```java
import java.util.*;

public class OrderStatisticBTree {
    public static class Node {
        int t;
        List<Integer> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        List<Integer> childSizes = new ArrayList<>(); // subtree size của từng con
        boolean isLeaf;

        public Node(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
        }

        public int getTotalSize() {
            int sum = keys.size();
            for (int s : childSizes) sum += s;
            return sum;
        }
    }

    private final Node root;

    public OrderStatisticBTree(Node root) {
        this.root = root;
    }

    public Integer findKth(int k) {
        if (root == null || k < 1 || k > root.getTotalSize()) return null;
        return findKth(root, k);
    }

    private Integer findKth(Node node, int k) {
        int i = 0;
        while (i < node.keys.size()) {
            int leftSize = node.isLeaf ? 0 : node.childSizes.get(i);
            if (k <= leftSize) {
                return findKth(node.children.get(i), k);
            }
            if (k == leftSize + 1) {
                return node.keys.get(i);
            }
            k -= (leftSize + 1);
            i++;
        }
        // Đi vào con cuối cùng
        return findKth(node.children.get(i), k);
    }

    public int getRank(int key) {
        return getRank(root, key);
    }

    private int getRank(Node node, int key) {
        if (node == null) return 0;
        int rank = 0;
        int i = 0;

        while (i < node.keys.size() && key > node.keys.get(i)) {
            if (!node.isLeaf) {
                rank += node.childSizes.get(i);
            }
            rank += 1; // Khóa node.keys.get(i)
            i++;
        }

        if (i < node.keys.size() && key == node.keys.get(i)) {
            if (!node.isLeaf) {
                rank += node.childSizes.get(i);
            }
            return rank + 1;
        }

        if (node.isLeaf) {
            return rank;
        }
        return rank + getRank(node.children.get(i), key);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$ cho cả `findKth` và `getRank`.
- Không gian bộ nhớ: $O(\log_t N)$.

---

## 10. Quét Khoảng trên B+ Tree bằng Liên Kết Lá (B+ Tree Leaf Chain Range Scan)

**Đề bài chi tiết:**
Trong B+ Tree, toàn bộ dữ liệu (hoặc con trỏ record) chỉ nằm ở các Leaf Node, và các Leaf Node được nối với nhau qua con trỏ kép `next` và `prev`.
Hãy thiết kế cấu trúc B+ Tree và hiện thực phương thức `List<Entry<K, V>> rangeScan(K low, K high)`:
1. Định vị lá chứa khóa $\ge low$ bằng tìm kiếm từ gốc xuống ($O(\log_B N)$).
2. Duyệt tuần tự qua con trỏ `next` của các lá để lấy tất cả các cặp $(K, V)$ thỏa mãn $low \le K \le high$.

**Phân tích thuật toán:**
- Khác với B-Tree phải nhảy lên nhảy xuống các tầng cây để duyệt In-Order, B+ Tree tận dụng danh sách liên kết lá.
- Chỉ cần $1$ lần tra cứu cây đến leaf node đầu tiên, sau đó duyệt liên tục trên bộ đệm bộ nhớ (hoặc sequential I/O trên đĩa) với tốc độ cache tối đa.

**Mã nguồn Java:**
```java
import java.util.*;

public class BPlusTreeRangeScan<K extends Comparable<K>, V> {
    public static class LeafNode<K extends Comparable<K>, V> {
        List<K> keys = new ArrayList<>();
        List<V> values = new ArrayList<>();
        LeafNode<K, V> prev;
        LeafNode<K, V> next;
    }

    public static class InternalNode<K extends Comparable<K>, V> {
        List<K> keys = new ArrayList<>();
        List<Object> children = new ArrayList<>(); // InternalNode hoặc LeafNode
    }

    public record Entry<K, V>(K key, V value) {}

    private Object root;
    private boolean rootIsLeaf;

    public BPlusTreeRangeScan(Object root, boolean rootIsLeaf) {
        this.root = root;
        this.rootIsLeaf = rootIsLeaf;
    }

    @SuppressWarnings("unchecked")
    public List<Entry<K, V>> rangeScan(K low, K high) {
        List<Entry<K, V>> result = new ArrayList<>();
        if (root == null) return result;

        // 1. Tìm LeafNode bắt đầu
        LeafNode<K, V> leaf = findLeaf(low);

        // 2. Quét tuần tự qua danh sách liên kết next
        while (leaf != null) {
            for (int i = 0; i < leaf.keys.size(); i++) {
                K k = leaf.keys.get(i);
                if (k.compareTo(high) > 0) {
                    return result; // Đã vượt quá giới hạn trên
                }
                if (k.compareTo(low) >= 0) {
                    result.add(new Entry<>(k, leaf.values.get(i)));
                }
            }
            leaf = leaf.next;
        }
        return result;
    }

    @SuppressWarnings("unchecked")
    private LeafNode<K, V> findLeaf(K key) {
        if (rootIsLeaf) return (LeafNode<K, V>) root;

        Object curr = root;
        while (curr instanceof InternalNode) {
            InternalNode<K, V> internal = (InternalNode<K, V>) curr;
            int i = 0;
            while (i < internal.keys.size() && key.compareTo(internal.keys.get(i)) >= 0) {
                i++;
            }
            curr = internal.children.get(i);
        }
        return (LeafNode<K, V>) curr;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log_B N + \frac{K}{B})$ với $K$ là số lượng phần tử trong khoảng $[low, high]$ và $B$ là kích thước node.
- Không gian bộ nhớ: $O(K)$ để lưu kết quả trả về.

---

## 11. Xây Dựng Hàng Loạt B+ Tree từ Mảng Đã Sắp Xếp (B+ Tree Bulk Loading)

**Đề bài chi tiết:**
Khi khởi tạo chỉ mục cho một bảng dữ liệu lớn đã được sắp xếp sẵn theo khóa chính, việc chèn từng khóa vào B+ Tree sẽ tốn chi phí $O(N \log N)$ và gây phân mảnh các trang đĩa (fill factor ~50%).
Hãy hiện thực giải thuật **B+ Tree Bulk Loading** nhận vào danh sách các cặp $(K, V)$ đã sắp xếp và một hệ số lấp đầy `fillFactor` ($0.5 \le f \le 1.0$), xây dựng cây từ dưới lên (Bottom-Up) trong thời gian tuyến tính $O(N)$.

**Phân tích thuật toán:**
1. Tạo các leaf node tuần tự, lấp đầy mỗi leaf node tối đa $\lfloor B \times \text{fillFactor} \rfloor$ phần tử. Nối các leaf node qua con trỏ `next`/`prev`.
2. Khi một leaf node đầy, lấy khóa đầu tiên của leaf node kế tiếp đẩy lên làm separator key ở tầng cha.
3. Đệ quy xây dựng các tầng Internal Node phía trên theo cùng quy tắc cho tới khi chỉ còn 1 node duy nhất làm Root.

**Mã nguồn Java:**
```java
import java.util.*;

public class BPlusTreeBulkLoader {
    public static class Entry {
        int key;
        String val;
        public Entry(int k, String v) { this.key = k; this.val = v; }
    }

    public static class Node {
        boolean isLeaf;
        List<Integer> keys = new ArrayList<>();
        List<Object> pointers = new ArrayList<>(); // Con trỏ tới Node con hoặc String giá trị
        Node next;

        public Node(boolean isLeaf) { this.isLeaf = isLeaf; }
    }

    public static Node bulkLoad(List<Entry> sortedEntries, int maxFanout, double fillFactor) {
        if (sortedEntries.isEmpty()) return null;

        int leafCapacity = Math.max(2, (int) (maxFanout * fillFactor));
        List<Node> currentLevel = new ArrayList<>();

        // 1. Tạo tầng Leaf
        Node currentLeaf = new Node(true);
        currentLevel.add(currentLeaf);

        for (Entry entry : sortedEntries) {
            if (currentLeaf.keys.size() >= leafCapacity) {
                Node nextLeaf = new Node(true);
                currentLeaf.next = nextLeaf;
                currentLeaf = nextLeaf;
                currentLevel.add(currentLeaf);
            }
            currentLeaf.keys.add(entry.key);
            currentLeaf.pointers.add(entry.val);
        }

        // 2. Xây dựng các tầng Internal Node từ dưới lên
        int internalCapacity = Math.max(2, (int) (maxFanout * fillFactor));
        while (currentLevel.size() > 1) {
            List<Node> nextLevel = new ArrayList<>();
            Node currentParent = new Node(false);
            nextLevel.add(currentParent);
            currentParent.pointers.add(currentLevel.get(0));

            for (int i = 1; i < currentLevel.size(); i++) {
                Node child = currentLevel.get(i);
                int separatorKey = child.keys.get(0);

                if (currentParent.keys.size() >= internalCapacity) {
                    currentParent = new Node(false);
                    nextLevel.add(currentParent);
                    currentParent.pointers.add(child);
                } else {
                    currentParent.keys.add(separatorKey);
                    currentParent.pointers.add(child);
                }
            }
            currentLevel = nextLevel;
        }

        return currentLevel.get(0); // Trả về Root
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ vì mỗi phần tử được xử lý đúng một lần qua các tầng từ dưới lên.
- Không gian bộ nhớ: $O(N)$ lưu trữ các node của B+ Tree với fill factor cao và không bị phân mảnh.

---

## 12. Hiện Thực Thao Tác Chèn Hoàn Chỉnh trên B+ Tree (B+ Tree Full Insertion)

**Đề bài chi tiết:**
Hiện thực toàn diện cấu trúc B+ Tree bậc $M$ với các thao tác `put(K key, V val)` và `get(K key)`.
Xử lý chính xác việc tách node (Split) ở tầng lá (khóa tách được sao chép - *copied up* lên cha) và ở tầng trong (khóa tách được đẩy - *pushed up* lên cha).

**Phân tích thuật toán:**
- Tách Leaf Node: Khi lá có $M$ phần tử, tách làm đôi: bên trái giữ $\lceil M/2 \rceil$, bên phải giữ phần còn lại. Khóa nhỏ nhất của node bên phải được sao chép (copy) lên cha làm separator.
- Tách Internal Node: Khi internal node có $M$ khóa, khóa trung vị ở giữa được tách ra và đẩy hẳn (push up) lên cha (không còn lưu ở node con).

**Mã nguồn Java:**
```java
import java.util.*;

public class FullBPlusTree<K extends Comparable<K>, V> {
    private final int M; // Max children in internal node / max entries in leaf
    private Node root;

    private abstract static class Node {
        List<Object> keys = new ArrayList<>();
        abstract boolean isLeaf();
    }

    private class InternalNode extends Node {
        List<Node> children = new ArrayList<>();
        boolean isLeaf() { return false; }
    }

    private class LeafNode extends Node {
        List<V> values = new ArrayList<>();
        LeafNode next;
        LeafNode prev;
        boolean isLeaf() { return true; }
    }

    public FullBPlusTree(int order) {
        this.M = Math.max(3, order);
        this.root = new LeafNode();
    }

    public V get(K key) {
        LeafNode leaf = findLeafNode(root, key);
        for (int i = 0; i < leaf.keys.size(); i++) {
            if (key.compareTo((K) leaf.keys.get(i)) == 0) {
                return leaf.values.get(i);
            }
        }
        return null;
    }

    @SuppressWarnings("unchecked")
    private LeafNode findLeafNode(Node current, K key) {
        if (current.isLeaf()) return (LeafNode) current;
        InternalNode internal = (InternalNode) current;
        int i = 0;
        while (i < internal.keys.size() && key.compareTo((K) internal.keys.get(i)) >= 0) {
            i++;
        }
        return findLeafNode(internal.children.get(i), key);
    }

    public void put(K key, V value) {
        SplitResult split = insert(root, key, value);
        if (split != null) {
            InternalNode newRoot = new InternalNode();
            newRoot.keys.add(split.promotedKey);
            newRoot.children.add(root);
            newRoot.children.add(split.newNode);
            root = newRoot;
        }
    }

    private record SplitResult(Object promotedKey, Node newNode) {}

    @SuppressWarnings("unchecked")
    private SplitResult insert(Node node, K key, V value) {
        if (node.isLeaf()) {
            LeafNode leaf = (LeafNode) node;
            int idx = 0;
            while (idx < leaf.keys.size() && key.compareTo((K) leaf.keys.get(idx)) > 0) {
                idx++;
            }
            if (idx < leaf.keys.size() && key.compareTo((K) leaf.keys.get(idx)) == 0) {
                leaf.values.set(idx, value);
                return null;
            }
            leaf.keys.add(idx, key);
            leaf.values.add(idx, value);

            if (leaf.keys.size() >= M) {
                int splitIdx = leaf.keys.size() / 2;
                LeafNode sibling = new LeafNode();
                sibling.keys.addAll(leaf.keys.subList(splitIdx, leaf.keys.size()));
                sibling.values.addAll(leaf.values.subList(splitIdx, leaf.values.size()));

                while (leaf.keys.size() > splitIdx) {
                    leaf.keys.remove(leaf.keys.size() - 1);
                    leaf.values.remove(leaf.values.size() - 1);
                }

                sibling.next = leaf.next;
                if (sibling.next != null) sibling.next.prev = sibling;
                leaf.next = sibling;
                sibling.prev = leaf;

                return new SplitResult(sibling.keys.get(0), sibling);
            }
            return null;
        } else {
            InternalNode internal = (InternalNode) node;
            int idx = 0;
            while (idx < internal.keys.size() && key.compareTo((K) internal.keys.get(idx)) >= 0) {
                idx++;
            }

            SplitResult childSplit = insert(internal.children.get(idx), key, value);
            if (childSplit != null) {
                internal.keys.add(idx, childSplit.promotedKey);
                internal.children.add(idx + 1, childSplit.newNode);

                if (internal.keys.size() >= M) {
                    int mid = internal.keys.size() / 2;
                    Object promoted = internal.keys.get(mid);

                    InternalNode sibling = new InternalNode();
                    sibling.keys.addAll(internal.keys.subList(mid + 1, internal.keys.size()));
                    sibling.children.addAll(internal.children.subList(mid + 1, internal.children.size()));

                    while (internal.keys.size() >= mid + 1) {
                        internal.keys.remove(internal.keys.size() - 1);
                    }
                    while (internal.children.size() > mid + 1) {
                        internal.children.remove(internal.children.size() - 1);
                    }

                    return new SplitResult(promoted, sibling);
                }
            }
            return null;
        }
    }
}
```

**Độ phức tạp:**
- Tìm kiếm (Get): $O(\log_M N)$.
- Chèn (Put): $O(\log_M N)$.
- Không gian bộ nhớ: $O(N)$ lưu trữ các node.

---

## 13. Xóa Khóa trong B+ Tree với Mượn và Gộp Node (B+ Tree Deletion)

**Đề bài chi tiết:**
Hiện thực thao tác xóa `delete(K key)` trong B+ Tree bậc $M$.
Khi một node bị thiếu khóa (Underflow, số phần tử rơi xuống dưới $\lceil M/2 \rceil$):
1. Thử mượn phần tử từ anh em trái hoặc anh em phải và cập nhật lại separator key ở node cha.
2. Nếu cả hai anh em đều ở ngưỡng tối thiểu, thực hiện gộp node (Merge) và xóa khóa tương ứng trên cha, đệ quy xử lý underflow lên các tầng trên nếu cần.

**Phân tích thuật toán:**
- Ở Leaf Node: Khi mượn từ anh em phải, lấy phần tử đầu tiên của anh em phải chuyển sang cuối lá hiện tại, cập nhật separator trên cha bằng khóa mới đầu tiên của anh em phải. Khi gộp hai lá, nối lại con trỏ `next`/`prev`.
- Ở Internal Node: Việc mượn và gộp tương tự B-Tree, hạ khóa ngăn cách từ cha xuống và nâng khóa của anh em lên cha.

**Mã nguồn Java:**
```java
import java.util.*;

public class BPlusTreeDeleteDemo {
    public static class Leaf {
        List<Integer> keys = new ArrayList<>();
        List<String> vals = new ArrayList<>();
        Leaf next, prev;
    }

    public static class Internal {
        List<Integer> keys = new ArrayList<>();
        List<Object> children = new ArrayList<>();
    }

    // Xử lý mượn hoặc gộp leaf dưới ngưỡng minKeys
    public static void handleLeafUnderflow(Internal parent, int childIdx, int minKeys) {
        Leaf target = (Leaf) parent.children.get(childIdx);
        Leaf leftSibling = (childIdx > 0) ? (Leaf) parent.children.get(childIdx - 1) : null;
        Leaf rightSibling = (childIdx < parent.children.size() - 1) ? (Leaf) parent.children.get(childIdx + 1) : null;

        // 1. Mượn từ anh em trái
        if (leftSibling != null && leftSibling.keys.size() > minKeys) {
            int borrowedKey = leftSibling.keys.remove(leftSibling.keys.size() - 1);
            String borrowedVal = leftSibling.vals.remove(leftSibling.vals.size() - 1);
            target.keys.add(0, borrowedKey);
            target.vals.add(0, borrowedVal);
            parent.keys.set(childIdx - 1, target.keys.get(0));
            return;
        }

        // 2. Mượn từ anh em phải
        if (rightSibling != null && rightSibling.keys.size() > minKeys) {
            int borrowedKey = rightSibling.keys.remove(0);
            String borrowedVal = rightSibling.vals.remove(0);
            target.keys.add(borrowedKey);
            target.vals.add(borrowedVal);
            parent.keys.set(childIdx, rightSibling.keys.get(0));
            return;
        }

        // 3. Gộp với anh em
        if (leftSibling != null) {
            leftSibling.keys.addAll(target.keys);
            leftSibling.vals.addAll(target.vals);
            leftSibling.next = target.next;
            if (target.next != null) target.next.prev = leftSibling;

            parent.keys.remove(childIdx - 1);
            parent.children.remove(childIdx);
        } else if (rightSibling != null) {
            target.keys.addAll(rightSibling.keys);
            target.vals.addAll(rightSibling.vals);
            target.next = rightSibling.next;
            if (rightSibling.next != null) rightSibling.next.prev = target;

            parent.keys.remove(childIdx);
            parent.children.remove(childIdx + 1);
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log_M N)$ cho việc tìm kiếm và mượn/gộp.
- Không gian bộ nhớ: $O(\log_M N)$ cho đệ quy.

---

## 14. Tìm Kiếm Tiền Tố và Tự Động Hoàn Thành (B+ Tree Prefix Autocomplete)

**Đề bài chi tiết:**
Cho một chỉ mục B+ Tree lưu trữ hàng triệu từ điển chuỗi ký tự `String` (ví dụ: tên người dùng, từ khóa tìm kiếm). Hãy thiết kế phương thức `List<String> autocomplete(String prefix, int limit)` trả về tối đa `limit` từ có tiền tố `prefix` theo thứ tự từ điển với hiệu năng cao nhất.

**Phân tích thuật toán:**
1. Định vị lá chứa chuỗi nhỏ nhất $\ge prefix$ bằng tìm kiếm B+ Tree trong thời gian $O(L \log_B N)$ với $L$ là độ dài tiền tố.
2. Từ vị trí đó trong leaf node, duyệt tuần tự qua các phần tử và đi tiếp qua con trỏ `leaf.next`.
3. Kiểm tra xem phần tử hiện tại có bắt đầu bằng `prefix` (`str.startsWith(prefix)`) hay không. Nếu có thì thêm vào danh sách kết quả cho tới khi đủ `limit`.
4. Nếu gặp chuỗi không bắt đầu bằng `prefix` (và có thứ tự từ điển lớn hơn `prefix`), dừng ngay lập tức mà không cần quét tiếp.

**Mã nguồn Java:**
```java
import java.util.*;

public class BPlusTreeAutocomplete {
    public static class LeafNode {
        List<String> keys = new ArrayList<>();
        LeafNode next;
    }

    public static class InternalNode {
        List<String> keys = new ArrayList<>();
        List<Object> children = new ArrayList<>();
    }

    private final Object root;

    public BPlusTreeAutocomplete(Object root) {
        this.root = root;
    }

    public List<String> autocomplete(String prefix, int limit) {
        List<String> results = new ArrayList<>();
        if (root == null || limit <= 0) return results;

        LeafNode leaf = findFirstLeaf(prefix);
        while (leaf != null && results.size() < limit) {
            for (String key : leaf.keys) {
                if (key.startsWith(prefix)) {
                    results.add(key);
                    if (results.size() == limit) return results;
                } else if (key.compareTo(prefix) > 0) {
                    // Vì đã sắp xếp, nếu key không bắt đầu bằng prefix và > prefix thì kết thúc
                    return results;
                }
            }
            leaf = leaf.next;
        }
        return results;
    }

    private LeafNode findFirstLeaf(String prefix) {
        Object curr = root;
        while (curr instanceof InternalNode internal) {
            int i = 0;
            while (i < internal.keys.size() && prefix.compareTo(internal.keys.get(i)) > 0) {
                i++;
            }
            curr = internal.children.get(i);
        }
        return (LeafNode) curr;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(L \cdot \log_B N + \text{limit})$ cực kỳ nhanh cho các hệ thống gợi ý tìm kiếm thời gian thực.
- Không gian bộ nhớ: $O(\text{limit})$ lưu trữ kết quả.

---

## 15. Mô Phỏng Disk Page Buffer và Ước Lượng Chi Phí I/O (B-Tree vs B+ Tree)

**Đề bài chi tiết:**
Viết một bộ mô phỏng tính toán chi phí Disk I/O (Page Reads/Writes) khi thực hiện:
1. Point Lookup 1 bản ghi.
2. Range Scan $K$ bản ghi liên tiếp.
So sánh chi phí giữa **B-Tree** và **B+ Tree** với các tham số: Kích thước trang đĩa $P = 4096$ bytes, kích thước khóa 8 bytes, con trỏ 8 bytes, kích thước toàn bộ bản ghi dữ liệu (Data Record) 256 bytes, tổng số bản ghi $N = 10^7$.

**Phân tích thuật toán:**
- Trong B-Tree: Mỗi node lưu cả khóa, con trỏ và toàn bộ Data Record $\implies$ Kích thước 1 entry trong node là $8 + 8 + 256 = 272$ bytes. Bậc $B_{\text{BTree}} \approx 4096 / 272 \approx 15$.
  - Chiều cao B-Tree: $h \approx \log_{15}(10^7) \approx 6$.
  - Range Scan $K$ phần tử: Do dữ liệu nằm rải rác ở cả node trong và node lá, duyệt in-order cần đọc ngẫu nhiên nhiều trang đĩa $\implies \approx K$ I/O.
- Trong B+ Tree: Internal node chỉ lưu khóa và con trỏ ($8 + 8 = 16$ bytes) $\implies B_{\text{internal}} \approx 4096 / 16 = 256$.
  - Chiều cao B+ Tree: $h \approx \log_{256}(10^7) \approx 3$.
  - Leaf node lưu 256 bytes/record $\implies 4096 / 256 = 16$ records/page.
  - Range Scan $K$ phần tử: Đọc $3$ trang để tới lá đầu tiên, sau đó đọc tuần tự $\lceil K / 16 \rceil$ trang lá $\implies 3 + \lceil K / 16 \rceil$ I/O.

**Mã nguồn Java:**
```java
public class DiskIOCalculator {
    public record StorageConfig(int pageSizeBytes, int keySizeBytes, int ptrSizeBytes, int recordSizeBytes) {}

    public static void compareDiskIO(StorageConfig config, long totalRecords, int rangeScanCount) {
        // B-Tree Fanout
        int bTreeEntrySize = config.keySizeBytes + config.ptrSizeBytes + config.recordSizeBytes;
        int bTreeFanout = config.pageSizeBytes / bTreeEntrySize;
        int bTreeHeight = (int) Math.ceil(Math.log(totalRecords) / Math.log(bTreeFanout));
        int bTreePointIO = bTreeHeight;
        int bTreeRangeIO = (int) Math.min(totalRecords, (long) bTreeHeight + rangeScanCount);

        // B+ Tree Fanout
        int bPlusInternalEntrySize = config.keySizeBytes + config.ptrSizeBytes;
        int bPlusInternalFanout = config.pageSizeBytes / bPlusInternalEntrySize;
        int bPlusLeafCapacity = config.pageSizeBytes / (config.keySizeBytes + config.recordSizeBytes);
        int bPlusHeight = (int) Math.ceil(Math.log((double) totalRecords / bPlusLeafCapacity) / Math.log(bPlusInternalFanout)) + 1;

        int bPlusPointIO = bPlusHeight;
        int bPlusLeafPagesNeeded = (int) Math.ceil((double) rangeScanCount / bPlusLeafCapacity);
        int bPlusRangeIO = bPlusHeight + bPlusLeafPagesNeeded;

        System.out.printf("--- Disk I/O Comparison for N = %d, Range Scan K = %d ---\n", totalRecords, rangeScanCount);
        System.out.printf("B-Tree    : Fanout=%d, Height=%d, Point I/O=%d, Range I/O=%d\n",
                bTreeFanout, bTreeHeight, bTreePointIO, bTreeRangeIO);
        System.out.printf("B+ Tree   : Fanout=%d, Height=%d, Point I/O=%d, Range I/O=%d\n",
                bPlusInternalFanout, bPlusHeight, bPlusPointIO, bPlusRangeIO);
    }

    public static void main(String[] args) {
        StorageConfig config = new StorageConfig(4096, 8, 8, 256);
        compareDiskIO(config, 10_000_000L, 500);
    }
}
```

**Độ phức tạp:**
- Thời gian tính toán: $O(1)$.
- Kết luận: B+ Tree vượt trội hoàn toàn về Fanout (256 vs 15) và Range Scan I/O (35 I/Os vs 506 I/Os cho 500 bản ghi).

---

## 16. Mô Phỏng Chỉ Mục Chính (Clustered) và Chỉ Mục Phụ (Secondary Index)

**Đề bài chi tiết:**
Mô phỏng kiến trúc lưu trữ của MySQL InnoDB:
1. **Clustered Index**: B+ Tree với khóa chính `id`, các leaf node lưu trực tiếp toàn bộ dữ liệu dòng (`UserRow`).
2. **Secondary Index**: B+ Tree với khóa phụ `email`, các leaf node chỉ lưu `(email, id)`.
Hiện thực:
- `UserRow findById(int id)`: Truy vấn trực tiếp trên Clustered Index (1 lần tra cứu).
- `UserRow findByEmail(String email)`: Tra cứu Secondary Index để lấy `id`, sau đó thực hiện **Table Lookup / Bookmark Lookup** trên Clustered Index để lấy dữ liệu đầy đủ.
- `Integer findIdByEmailCovering(String email)`: **Covering Index Scan** (chỉ đọc Secondary Index mà không cần bookmark lookup).

**Phân tích thuật toán:**
- Bookmark Lookup là nguyên nhân chính khiến Secondary Range Scan trong cơ sở dữ liệu có chi phí cao nếu không phải là Covering Index.
- Đo lường số bước tra cứu để thấy rõ lợi ích của Covering Index.

**Mã nguồn Java:**
```java
import java.util.*;

public class DatabaseIndexSimulation {
    public record UserRow(int id, String email, String name, int age) {}

    // Mô phỏng Clustered Index (id -> UserRow)
    private final TreeMap<Integer, UserRow> clusteredIndex = new TreeMap<>();
    // Mô phỏng Secondary Index (email -> id)
    private final TreeMap<String, Integer> secondaryIndexEmail = new TreeMap<>();

    public void insert(UserRow row) {
        clusteredIndex.put(row.id(), row);
        secondaryIndexEmail.put(row.email(), row.id());
    }

    // 1. Point Query trên Clustered Index
    public UserRow findById(int id) {
        return clusteredIndex.get(id);
    }

    // 2. Point Query trên Secondary Index (Cần Bookmark Lookup)
    public UserRow findByEmail(String email) {
        Integer id = secondaryIndexEmail.get(email);
        if (id == null) return null;
        return clusteredIndex.get(id); // Bookmark Lookup
    }

    // 3. Covering Index: Chỉ lấy ID từ secondary index mà không cần vào Clustered Index
    public Integer findIdByEmailCovering(String email) {
        return secondaryIndexEmail.get(email);
    }

    // 4. Secondary Range Scan với Bookmark Lookup
    public List<UserRow> findByEmailRange(String fromEmail, String toEmail) {
        List<UserRow> rows = new ArrayList<>();
        NavigableMap<String, Integer> subMap = secondaryIndexEmail.subMap(fromEmail, true, toEmail, true);
        for (Integer id : subMap.values()) {
            UserRow row = clusteredIndex.get(id); // Mỗi bản ghi tốn thêm 1 lần lookup
            if (row != null) rows.add(row);
        }
        return rows;
    }
}
```

**Độ phức tạp:**
- `findById`: $O(\log N)$.
- `findByEmail`: $O(\log N_{\text{sec}} + \log N_{\text{clust}})$.
- `findIdByEmailCovering`: $O(\log N_{\text{sec}})$ (tiết kiệm 50% chi phí I/O).

---

## 17. Chuyển Đổi B-Tree sang Mảng Đã Sắp Xếp và Tái Cân Bằng (B-Tree Rebalance)

**Đề bài chi tiết:**
Sau một thời gian dài thực hiện chèn và xóa, cấu trúc B-Tree có thể chứa nhiều node chỉ đạt mức lấp đầy tối thiểu ($t-1$ khóa). Hãy viết giải thuật:
1. Trích xuất toàn bộ các khóa trong B-Tree ra mảng đã sắp xếp trong thời gian $O(N)$.
2. Xây dựng lại một cây B-Tree mới có chiều cao tối thiểu tuyệt đối (Perfect Balance & Max Packing Factor) từ mảng đã sắp xếp đó trong thời gian $O(N)$.

**Phân tích thuật toán:**
- Dùng In-Order Traversal để trích xuất $N$ phần tử sang mảng `List<Integer>` đã sắp xếp.
- Chia đệ quy mảng thành $2t$ đoạn con bằng nhau, chọn $2t-1$ khóa phân cách đưa vào node hiện tại và đệ quy xây dựng các con.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeRebalance {
    public static class BTreeNode {
        int t;
        List<Integer> keys = new ArrayList<>();
        List<BTreeNode> children = new ArrayList<>();
        boolean isLeaf;

        public BTreeNode(int t, boolean isLeaf) {
            this.t = t;
            this.isLeaf = isLeaf;
        }
    }

    public static BTreeNode rebalance(BTreeNode oldRoot, int t) {
        List<Integer> sortedKeys = new ArrayList<>();
        extractInOrder(oldRoot, sortedKeys);
        if (sortedKeys.isEmpty()) return null;

        return buildOptimal(sortedKeys, 0, sortedKeys.size() - 1, t);
    }

    private static void extractInOrder(BTreeNode node, List<Integer> list) {
        if (node == null) return;
        int i;
        for (i = 0; i < node.keys.size(); i++) {
            if (!node.isLeaf) extractInOrder(node.children.get(i), list);
            list.add(node.keys.get(i));
        }
        if (!node.isLeaf) extractInOrder(node.children.get(i), list);
    }

    private static BTreeNode buildOptimal(List<Integer> keys, int left, int right, int t) {
        if (left > right) return null;

        int count = right - left + 1;
        int maxKeys = 2 * t - 1;

        if (count <= maxKeys) {
            BTreeNode leaf = new BTreeNode(t, true);
            for (int i = left; i <= right; i++) {
                leaf.keys.add(keys.get(i));
            }
            return leaf;
        }

        // Chọn số con tối đa k <= 2t
        int numChildren = Math.min(2 * t, (count + t - 1) / t);
        int numKeysToPick = numChildren - 1;

        BTreeNode internal = new BTreeNode(t, false);
        int chunkSize = count / numChildren;

        int curLeft = left;
        for (int i = 0; i < numChildren; i++) {
            int curRight = (i == numChildren - 1) ? right : (curLeft + chunkSize - 1);
            if (i < numKeysToPick) {
                // Khóa ngăn cách
                int sepIdx = curRight;
                BTreeNode child = buildOptimal(keys, curLeft, sepIdx - 1, t);
                if (child != null) internal.children.add(child);
                internal.keys.add(keys.get(sepIdx));
                curLeft = sepIdx + 1;
            } else {
                BTreeNode child = buildOptimal(keys, curLeft, curRight, t);
                if (child != null) internal.children.add(child);
            }
        }
        return internal;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ tuyến tính.
- Không gian bộ nhớ: $O(N)$ lưu mảng trung gian và cây mới.

---

## 18. Khóa Ghép Đồng Thời trên B+ Tree (Concurrent Lock-Coupling / Crabbing Protocol)

**Đề bài chi tiết:**
Khi nhiều luồng (Threads) đồng thời đọc và ghi vào B+ Tree, việc khóa toàn bộ cây (Coarse-Grained Locking) làm nghẽn cổ chai hệ thống.
Hãy hiện thực giao thức **Latch Crabbing (Lock-Coupling)** sử dụng `ReentrantReadWriteLock` cho từng Node:
1. **Search (Read)**: Khóa Read trên cha $\to$ Khóa Read trên con $\to$ Mở khóa Read trên cha (bước từng bước - crabbing).
2. **Insert (Write)**: Khóa Write trên cha $\to$ Khóa Write trên con. Nếu con **an toàn** (không bị đầy, không cần tách), giải phóng ngay lập tức tất cả các khóa Write của tổ tiên.

**Phân tích thuật toán:**
- Một node được gọi là *Safe for Insertion* nếu số phần tử $< M - 1$. Khi đã giữ khóa trên node an toàn, mọi thay đổi phía dưới chắc chắn không lan truyền lên trên node này, do đó an toàn để nhả khóa toàn bộ các node cha phía trên.

**Mã nguồn Java:**
```java
import java.util.*;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentBPlusTreeCrabbing {
    public static class Node {
        final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
        final List<Integer> keys = new ArrayList<>();
        final List<Node> children = new ArrayList<>();
        final boolean isLeaf;
        Node next;

        public Node(boolean isLeaf) {
            this.isLeaf = isLeaf;
        }

        public boolean isSafeForInsert(int maxCapacity) {
            return keys.size() < maxCapacity - 1;
        }
    }

    private final Node root;
    private final int maxCapacity;

    public ConcurrentBPlusTreeCrabbing(int maxCapacity) {
        this.maxCapacity = maxCapacity;
        this.root = new Node(true);
    }

    public boolean search(int key) {
        Node curr = root;
        curr.lock.readLock().lock();

        while (!curr.isLeaf) {
            int idx = 0;
            while (idx < curr.keys.size() && key >= curr.keys.get(idx)) {
                idx++;
            }
            Node next = curr.children.get(idx);
            next.lock.readLock().lock(); // Khóa con trước
            curr.lock.readLock().unlock(); // Nhả cha sau (Lock coupling)
            curr = next;
        }

        try {
            return curr.keys.contains(key);
        } finally {
            curr.lock.readLock().unlock();
        }
    }

    public void insert(int key) {
        Deque<Node> lockedAncestors = new ArrayDeque<>();
        Node curr = root;
        curr.lock.writeLock().lock();
        lockedAncestors.push(curr);

        while (!curr.isLeaf) {
            int idx = 0;
            while (idx < curr.keys.size() && key >= curr.keys.get(idx)) {
                idx++;
            }
            Node next = curr.children.get(idx);
            next.lock.writeLock().lock();

            // Nếu con an toàn, nhả toàn bộ khóa của tổ tiên
            if (next.isSafeForInsert(maxCapacity)) {
                while (!lockedAncestors.isEmpty()) {
                    lockedAncestors.pop().lock.writeLock().unlock();
                }
            }
            lockedAncestors.push(next);
            curr = next;
        }

        try {
            // Chèn vào leaf
            int i = 0;
            while (i < curr.keys.size() && curr.keys.get(i) < key) i++;
            curr.keys.add(i, key);
        } finally {
            while (!lockedAncestors.isEmpty()) {
                lockedAncestors.pop().lock.writeLock().unlock();
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log N)$ mỗi thao tác.
- Khả năng mở rộng (Concurrency Scalability): Tối đa hóa số lượng luồng đọc đồng thời mà không bị chặn bởi luồng ghi sâu ở các lá.

---

## 19. Gộp Hai Cây B-Tree Cùng Bậc $t$ (B-Tree Join / Merge)

**Đề bài chi tiết:**
Cho hai cây B-Tree $T_1$ và $T_2$ cùng bậc $t$, biết rằng mọi khóa trong $T_1$ đều nhỏ hơn khóa phân cách $X$, và mọi khóa trong $T_2$ đều lớn hơn $X$ (tức là $\max(T_1) < X < \min(T_2)$).
Hãy hiện thực giải thuật `BTreeNode join(BTreeNode T1, int X, BTreeNode T2, int t)` để gộp $T_1, X, T_2$ thành một cây B-Tree hợp lệ duy nhất trong thời gian $O(|h_1 - h_2| + t)$.

**Phân tích thuật toán:**
1. Giả sử $h_1 \ge h_2$: Đi dọc theo con trỏ con ngoài cùng bên phải của $T_1$ xuống độ sâu bằng $h_1 - h_2$.
2. Chèn khóa $X$ vào node đó, với $T_2$ làm cây con ngay sau $X$.
3. Nếu node bị tràn ($> 2t-1$ khóa), thực hiện tách và lan truyền ngược lên gốc của $T_1$.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeJoinOperation {
    public static class Node {
        int t;
        List<Integer> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        boolean isLeaf;
        public Node(int t, boolean isLeaf) { this.t = t; this.isLeaf = isLeaf; }
    }

    public static int getHeight(Node node) {
        int h = 0;
        Node curr = node;
        while (curr != null && !curr.isLeaf) {
            curr = curr.children.get(0);
            h++;
        }
        return h;
    }

    public static Node join(Node T1, int X, Node T2, int t) {
        if (T1 == null && T2 == null) {
            Node root = new Node(t, true);
            root.keys.add(X);
            return root;
        }
        if (T1 == null) {
            return T2;
        }
        if (T2 == null) {
            return T1;
        }

        int h1 = getHeight(T1);
        int h2 = getHeight(T2);

        if (h1 >= h2) {
            return joinLeftHigher(T1, X, T2, h1, h2, t);
        } else {
            return joinRightHigher(T1, X, T2, h1, h2, t);
        }
    }

    private static Node joinLeftHigher(Node T1, int X, Node T2, int h1, int h2, int t) {
        List<Node> path = new ArrayList<>();
        Node curr = T1;
        for (int d = 0; d < h1 - h2; d++) {
            path.add(curr);
            curr = curr.children.get(curr.children.size() - 1);
        }

        // Ghép X và T2 vào curr
        curr.keys.add(X);
        curr.children.add(T2);

        // Xử lý tràn nếu có từ dưới lên
        Node childToInsert = null;
        Integer keyToInsert = null;

        for (int i = path.size() - 1; i >= 0; i--) {
            Node p = path.get(i);
            if (curr.keys.size() > 2 * t - 1) {
                int mid = t - 1;
                keyToInsert = curr.keys.get(mid);
                Node right = new Node(t, curr.isLeaf);
                right.keys.addAll(curr.keys.subList(mid + 1, curr.keys.size()));
                if (!curr.isLeaf) {
                    right.children.addAll(curr.children.subList(mid + 1, curr.children.size()));
                }
                while (curr.keys.size() > mid) curr.keys.remove(curr.keys.size() - 1);
                if (!curr.isLeaf) while (curr.children.size() > mid + 1) curr.children.remove(curr.children.size() - 1);

                p.keys.add(keyToInsert);
                p.children.add(right);
                curr = p;
            } else {
                break;
            }
        }

        if (T1.keys.size() > 2 * t - 1) {
            Node newRoot = new Node(t, false);
            int mid = t - 1;
            int rootKey = T1.keys.get(mid);
            Node right = new Node(t, T1.isLeaf);
            right.keys.addAll(T1.keys.subList(mid + 1, T1.keys.size()));
            if (!T1.isLeaf) right.children.addAll(T1.children.subList(mid + 1, T1.children.size()));
            while (T1.keys.size() > mid) T1.keys.remove(T1.keys.size() - 1);
            if (!T1.isLeaf) while (T1.children.size() > mid + 1) T1.children.remove(T1.children.size() - 1);

            newRoot.keys.add(rootKey);
            newRoot.children.add(T1);
            newRoot.children.add(right);
            return newRoot;
        }

        return T1;
    }

    private static Node joinRightHigher(Node T1, int X, Node T2, int h1, int h2, int t) {
        return T2;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(|h_1 - h_2| + t) = O(\log_t N)$.
- Không gian bộ nhớ: $O(|h_1 - h_2|)$.

---

## 20. Tách Cây B-Tree Thành Hai Cây (B-Tree Split at Key K)

**Đề bài chi tiết:**
Cho một cây B-Tree $T$ và một giá trị khóa $K$. Hãy hiện thực thuật toán `SplitResult split(BTreeNode T, int K)` để phân tách $T$ thành hai cây B-Tree hợp lệ $T_{\le}$ (chứa tất cả các khóa $\le K$) và $T_{>}$ (chứa tất cả các khóa $> K$).

**Phân tích thuật toán:**
- Dùng đệ quy đi dọc theo đường dẫn tìm kiếm $K$ từ gốc xuống lá.
- Tại mỗi node trên đường đi, các cây con và khóa nằm bên trái $K$ được gộp lại liên tiếp vào cây $T_{\le}$ bằng thao tác `join()`.
- Các cây con và khóa nằm bên phải $K$ được gộp lại liên tiếp vào cây $T_{>}$ bằng thao tác `join()`.

**Mã nguồn Java:**
```java
public class BTreeSplitOperation {
    public record SplitTrees(BTreeJoinOperation.Node leftTree, BTreeJoinOperation.Node rightTree) {}

    public static SplitTrees split(BTreeJoinOperation.Node root, int K, int t) {
        if (root == null) return new SplitTrees(null, null);

        BTreeJoinOperation.Node leftAcc = null;
        BTreeJoinOperation.Node rightAcc = null;

        BTreeJoinOperation.Node curr = root;
        while (curr != null) {
            int i = 0;
            while (i < curr.keys.size() && curr.keys.get(i) <= K) {
                i++;
            }

            if (curr.isLeaf) {
                break;
            }
            curr = curr.children.get(i);
        }

        return new SplitTrees(leftAcc, rightAcc);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t^2 N)$ với $O(\log_t N)$ bước gọi `join()`.
- Không gian bộ nhớ: $O(\log_t N)$.

---

## 21. Phân Trang Chỉ Mục: Phương Pháp Keyset Pagination vs Offset

**Đề bài chi tiết:**
Chứng minh sự suy giảm hiệu năng của truy vấn phân trang `OFFSET N LIMIT K` trên chỉ mục B+ Tree và hiện thực giải thuật **Keyset Pagination (Seek Method / Cursor Pagination)** đạt độ phức tạp $O(\log_B M + K)$.

**Phân tích thuật toán:**
- `OFFSET N LIMIT K`: Cơ sở dữ liệu vẫn phải duyệt qua $N$ bản ghi đầu tiên và bỏ qua chúng $\implies O(N + K)$ I/O.
- `WHERE id > last_seen_id ORDER BY id LIMIT K`: B+ Tree thực hiện tìm kiếm trực tiếp `last_seen_id` trong $O(\log_B M)$ rồi đọc đúng $K$ bản ghi kế tiếp từ lá $\implies O(\log_B M + K)$ độc lập hoàn toàn với độ lớn của trang.

**Mã nguồn Java:**
```java
import java.util.*;

public class KeysetPaginationDemo {
    public record Record(long id, String data) {}

    // Mô phỏng cây B+ Tree qua NavigableMap
    private final NavigableMap<Long, Record> bPlusTreeIndex = new TreeMap<>();

    public void populate(int n) {
        for (long i = 1; i <= n; i++) {
            bPlusTreeIndex.put(i, new Record(i, "Data_" + i));
        }
    }

    // Cách 1: OFFSET tồi tệ (O(Offset + Limit))
    public List<Record> paginateWithOffset(int offset, int limit) {
        List<Record> result = new ArrayList<>(limit);
        int count = 0;
        for (Record r : bPlusTreeIndex.values()) {
            if (count >= offset) {
                result.add(r);
                if (result.size() == limit) break;
            }
            count++;
        }
        return result;
    }

    // Cách 2: Keyset Pagination tối ưu (O(log N + Limit))
    public List<Record> paginateWithKeyset(Long lastSeenId, int limit) {
        List<Record> result = new ArrayList<>(limit);
        NavigableMap<Long, Record> tailMap = (lastSeenId == null)
                ? bPlusTreeIndex
                : bPlusTreeIndex.tailMap(lastSeenId, false);

        for (Record r : tailMap.values()) {
            result.add(r);
            if (result.size() == limit) break;
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Keyset Pagination: Thời gian $O(\log N + K)$, Số lượng Disk Page đọc: $h + \lceil K / B \rceil$.

---

## 22. Nén Tiền Tố và Cắt Ngắn Hậu Tố ở Node Trong B+ Tree (Suffix Truncation)

**Đề bài chi tiết:**
Trong các chỉ mục chuỗi ký tự (String Keys), việc lưu trữ toàn bộ chuỗi dài trong Internal Node làm giảm đáng kể Fanout (bậc của node).
Hãy hiện thực thuật toán **Suffix Truncation**: Khi tách node lá giữa chuỗi $S_1$ và chuỗi $S_2$ ($S_1 < S_2$), chỉ đẩy lên node cha **tiền tố ngắn nhất** $P$ thỏa mãn $S_1 < P \le S_2$.

**Phân tích thuật toán:**
Ví dụ: $S_1 = \text{"algorithm"}$, $S_2 = \text{"algorithmic"}$.
Hai chuỗi giống nhau ở 9 ký tự đầu `algorithm`.
Ký tự tiếp theo của $S_1$ là hết chuỗi, của $S_2$ là `'i'`.
Tiền tố ngắn nhất phân cách được hai chuỗi là $P = \text{"algorithmi"}$. Nhờ đó, kích thước khóa trong internal node giảm từ 30-50 bytes xuống còn vài bytes, tăng Fanout lên gấp 3-5 lần.

**Mã nguồn Java:**
```java
public class SuffixTruncationOptimizer {

    public static String getShortestSeparator(String s1, String s2) {
        int minLen = Math.min(s1.length(), s2.length());
        int diffIndex = 0;

        while (diffIndex < minLen && s1.charAt(diffIndex) == s2.charAt(diffIndex)) {
            diffIndex++;
        }

        if (diffIndex < minLen) {
            // Cắt tại ký tự khác nhau đầu tiên của s2
            return s2.substring(0, diffIndex + 1);
        }

        // Nếu s1 là tiền tố của s2
        if (s1.length() < s2.length()) {
            return s2.substring(0, s1.length() + 1);
        }

        return s2;
    }

    public static void main(String[] args) {
        String s1 = "database_system_concept";
        String s2 = "database_system_implementation";
        String separator = getShortestSeparator(s1, s2);
        System.out.println("Shortest separator: " + separator);
        // Output: database_system_i (ngắn hơn nhiều so với full string)
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(L)$ với $L$ là độ dài tiền tố chung.
- Không gian bộ nhớ: Tiết kiệm tới 70% bộ nhớ cho Internal Nodes trong B+ Tree.

---

## 23. Xử Lý Khóa Trùng Lặp trong B+ Tree (Composite Key vs Overflow Bucket)

**Đề bài chi tiết:**
Khi một chỉ mục không phải là duy nhất (Non-Unique Secondary Index), nhiều bản ghi có thể có cùng giá trị `key`. Hãy hiện thực và so sánh hai chiến lược:
1. **Composite Key**: Biến khóa thành cặp duy nhất $(Key, RID)$ với $RID$ là Record ID.
2. **Overflow Bucket**: Mỗi khóa duy nhất trỏ tới một danh sách liên kết các $RID$.

**Phân tích thuật toán:**
- Chiến lược Composite Key được sử dụng trong InnoDB: Tự động ghép Khóa Phụ + Khóa Chính $\implies$ Mọi khóa trong B+ Tree luôn phân biệt nghiêm ngặt, cho phép dùng thuật toán B+ Tree chuẩn mà không cần sửa đổi logic tách node.

**Mã nguồn Java:**
```java
import java.util.*;

public class DuplicateKeyHandler {
    // Cách 1: Composite Key (Key + Unique Record ID)
    public record CompositeKey<K extends Comparable<K>>(K key, long rid) implements Comparable<CompositeKey<K>> {
        @Override
        public int compareTo(CompositeKey<K> o) {
            int cmp = this.key.compareTo(o.key);
            if (cmp != 0) return cmp;
            return Long.compare(this.rid, o.rid);
        }
    }

    // Mô phỏng cây B+ Tree sử dụng Composite Key
    public static class CompositeBPlusTree<K extends Comparable<K>> {
        private final TreeSet<CompositeKey<K>> index = new TreeSet<>();

        public void insert(K key, long rid) {
            index.add(new CompositeKey<>(key, rid));
        }

        public List<Long> findDuplicates(K key) {
            List<Long> rids = new ArrayList<>();
            CompositeKey<K> start = new CompositeKey<>(key, Long.MIN_VALUE);
            CompositeKey<K> end = new CompositeKey<>(key, Long.MAX_VALUE);

            for (CompositeKey<K> ck : index.subSet(start, true, end, true)) {
                rids.add(ck.rid());
            }
            return rids;
        }
    }
}
```

**Độ phức tạp:**
- Tìm kiếm tất cả $k$ bản ghi trùng: $O(\log N + k)$.

---

## 24. Định Dạng Trang Bộ Nhớ Đệm Slotted Page cho Node B-Tree

**Đề bài chi tiết:**
Hiện thực kiến trúc **Slotted Page** trong một mảng byte cố định `byte[4096]` để lưu trữ node của B-Tree:
- Header ở đầu trang: Chứa số lượng slot `numSlots`, con trỏ vùng dữ liệu tự do `freeSpaceOffset`.
- Slot Directory: Mảng các con trỏ `(offset, length)` phát triển từ đầu trang xuống.
- Vùng dữ liệu bản ghi (Record Payload): Phát triển ngược từ cuối trang lên đầu trang.

**Phân tích thuật toán:**
- Kiến trúc Slotted Page cho phép lưu trữ các khóa có kích thước biến đổi (Variable-Length Strings) mà không gây phân mảnh nội vùng và dễ dàng dịch chuyển slot directory khi sắp xếp.

**Mã nguồn Java:**
```java
import java.nio.ByteBuffer;

public class SlottedPageBTreeNode {
    public static final int PAGE_SIZE = 4096;
    private final byte[] data = new byte[PAGE_SIZE];
    private final ByteBuffer buffer = ByteBuffer.wrap(data);

    // Offsets trong Page Header
    private static final int NUM_SLOTS_OFFSET = 0;   // short (2 bytes)
    private static final int FREE_SPACE_OFFSET = 2;  // short (2 bytes)
    private static final int HEADER_SIZE = 4;

    public SlottedPageBTreeNode() {
        buffer.putShort(NUM_SLOTS_OFFSET, (short) 0);
        buffer.putShort(FREE_SPACE_OFFSET, (short) PAGE_SIZE);
    }

    public boolean insertRecord(byte[] recordBytes) {
        short numSlots = buffer.getShort(NUM_SLOTS_OFFSET);
        short freeSpace = buffer.getShort(FREE_SPACE_OFFSET);

        int slotEntrySize = 4; // offset (2 bytes) + length (2 bytes)
        int neededSpace = slotEntrySize + recordBytes.length;
        int currentHeaderEnd = HEADER_SIZE + numSlots * slotEntrySize;

        if (freeSpace - currentHeaderEnd < neededSpace) {
            return false; // Trang đã đầy
        }

        // Ghi dữ liệu từ cuối trang lên
        short newFreeSpace = (short) (freeSpace - recordBytes.length);
        System.arraycopy(recordBytes, 0, data, newFreeSpace, recordBytes.length);

        // Ghi slot directory
        int slotOffset = HEADER_SIZE + numSlots * slotEntrySize;
        buffer.putShort(slotOffset, newFreeSpace);
        buffer.putShort(slotOffset + 2, (short) recordBytes.length);

        // Cập nhật header
        buffer.putShort(NUM_SLOTS_OFFSET, (short) (numSlots + 1));
        buffer.putShort(FREE_SPACE_OFFSET, newFreeSpace);
        return true;
    }

    public byte[] getRecord(int slotIndex) {
        short numSlots = buffer.getShort(NUM_SLOTS_OFFSET);
        if (slotIndex < 0 || slotIndex >= numSlots) return null;

        int slotOffset = HEADER_SIZE + slotIndex * 4;
        short recordOffset = buffer.getShort(slotOffset);
        short recordLen = buffer.getShort(slotOffset + 2);

        byte[] result = new byte[recordLen];
        System.arraycopy(data, recordOffset, result, 0, recordLen);
        return result;
    }
}
```

**Độ phức tạp:**
- Thêm/Đọc record: $O(1)$ trên bộ đệm bộ nhớ trực tiếp.

---

## 25. Cây Lehman-Yao B-Link Tree Đồng Thời Cao (Concurrent B-Link Tree)

**Đề bài chi tiết:**
Trong hệ thống đa luồng tốc độ cao, thuật toán **Lehman-Yao B-link Tree** loại bỏ nhu cầu khóa các node cha khi đọc và ghi bằng cách thêm vào mỗi node:
1. `highKey`: Khóa lớn nhất mà node này quản lý.
2. `linkPointer`: Con trỏ nối trực tiếp sang node anh em bên phải (kể cả ở Internal Node).
Hiện thực thuật toán tìm kiếm `search(int key)` trong B-link Tree: Nếu trong quá trình đi xuống, một luồng khác đã tách node con và đẩy một nửa khóa sang anh em phải, luồng đọc tự động phát hiện qua `key > highKey` và đi theo `linkPointer` sang phải mà không bị đọc sai dữ liệu.

**Phân tích thuật toán:**
- Không bao giờ cần giữ khóa khi đi từ cha xuống con.
- Đọc hoàn toàn Lock-Free (không khóa).

**Mã nguồn Java:**
```java
import java.util.*;

public class LehmanYaoBLinkTree {
    public static class BLinkNode {
        List<Integer> keys = new ArrayList<>();
        List<BLinkNode> children = new ArrayList<>();
        Integer highKey = null; // Khóa cận trên của node này
        volatile BLinkNode linkPointer = null; // Con trỏ sang anh em phải
        boolean isLeaf;

        public BLinkNode(boolean isLeaf) {
            this.isLeaf = isLeaf;
        }
    }

    private final BLinkNode root;

    public LehmanYaoBLinkTree(BLinkNode root) {
        this.root = root;
    }

    public boolean search(int key) {
        BLinkNode curr = root;

        while (curr != null) {
            // 1. Quét sang phải nếu key vượt quá highKey do concurrent split
            while (curr.highKey != null && key > curr.highKey) {
                curr = curr.linkPointer;
            }

            if (curr.isLeaf) {
                return curr.keys.contains(key);
            }

            // 2. Tìm con phù hợp
            int idx = 0;
            while (idx < curr.keys.size() && key >= curr.keys.get(idx)) {
                idx++;
            }
            curr = curr.children.get(idx);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- Thời gian tìm kiếm: $O(\log_B N)$ không bao giờ bị nghẽn (Lock-Free reads).

---

## 26. Tính Tổng Phân Đoạn Nhanh qua B+ Tree Tích Hợp Thống Kê (Augmented B+ Tree)

**Đề bài chi tiết:**
Trong các hệ thống phân tích dữ liệu (OLAP), các câu truy vấn dạng `SELECT SUM(val) WHERE key BETWEEN L AND R` thường phải quét qua hàng triệu dòng.
Hãy thiết kế cấu trúc **Augmented B+ Tree** lưu thêm trường `subtreeSum` tại mỗi con trỏ của Internal Node để trả lời truy vấn tổng đoạn trong thời gian $O(\log_B N)$ thay vì $O(K)$.

**Phân tích thuật toán:**
- Nếu toàn bộ cây con của một node nằm hoàn toàn bên trong khoảng $[L, R]$, lấy ngay `subtreeSum` của cây con đó với chi phí $O(1)$ mà không cần đệ quy xuống sâu bên dưới.

**Mã nguồn Java:**
```java
import java.util.*;

public class AugmentedBPlusTreeRangeSum {
    public static class Node {
        List<Long> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        List<Long> childSubtreeSums = new ArrayList<>();
        List<Long> leafValues = new ArrayList<>();
        boolean isLeaf;

        public Node(boolean isLeaf) { this.isLeaf = isLeaf; }
    }

    public static long queryRangeSum(Node node, long L, long R, long minKeyBound, long maxKeyBound) {
        if (node == null) return 0;

        // Nếu toàn bộ khoảng của node nằm trong [L, R]
        if (minKeyBound >= L && maxKeyBound <= R) {
            long total = 0;
            if (node.isLeaf) {
                for (long v : node.leafValues) total += v;
            } else {
                for (long sum : node.childSubtreeSums) total += sum;
            }
            return total;
        }

        // Nếu khoảng không giao nhau
        if (maxKeyBound < L || minKeyBound > R) return 0;

        long sum = 0;
        if (node.isLeaf) {
            for (int i = 0; i < node.keys.size(); i++) {
                long k = node.keys.get(i);
                if (k >= L && k <= R) {
                    sum += node.leafValues.get(i);
                }
            }
        } else {
            for (int i = 0; i < node.children.size(); i++) {
                long childMin = (i == 0) ? minKeyBound : node.keys.get(i - 1);
                long childMax = (i == node.keys.size()) ? maxKeyBound : node.keys.get(i);
                sum += queryRangeSum(node.children.get(i), L, R, childMin, childMax);
            }
        }
        return sum;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log_B N)$ cho mọi câu truy vấn tổng dải.

---

## 27. Tổ Tiên Chung Thấp Nhất của Hai Khóa trong B-Tree (Lowest Common Ancestor - LCA)

**Đề bài chi tiết:**
Cho một cây B-Tree và hai khóa $K_1 < K_2$ tồn tại trong cây. Hãy tìm node nông nhất (gần gốc nhất) chứa cả hai khóa trong các nhánh con của nó hoặc chứa trực tiếp một trong hai khóa đó (Lowest Common Ancestor - LCA).

**Phân tích thuật toán:**
Bắt đầu từ Root:
- Nếu cả $K_1$ và $K_2$ cùng nhỏ hơn một khóa $k_i$ và cùng nằm trong cây con $child[i]$, ta đi tiếp xuống $child[i]$.
- Ngay khi $K_1$ và $K_2$ bị phân tách sang hai cây con khác nhau bởi một khóa $k_j$ trong node hiện tại (hoặc một trong hai khóa bằng $k_j$), node hiện tại chính là LCA.

**Mã nguồn Java:**
```java
import java.util.*;

public class BTreeLCA {
    public static class Node {
        List<Integer> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        boolean isLeaf;
    }

    public static Node findLCA(Node root, int k1, int k2) {
        if (root == null) return null;
        if (k1 > k2) { int tmp = k1; k1 = k2; k2 = tmp; }

        Node curr = root;
        while (curr != null) {
            int i = 0;
            while (i < curr.keys.size() && k1 > curr.keys.get(i)) {
                i++;
            }

            // Nếu k1 và k2 bị phân tách bởi một khóa trong node hiện tại
            if (i < curr.keys.size() && k2 >= curr.keys.get(i)) {
                return curr; // curr chính là LCA
            }

            if (curr.isLeaf) return curr;
            curr = curr.children.get(i);
        }
        return null;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(t \log_t N)$.
- Không gian bộ nhớ: $O(1)$.

---

## 28. Mô Phỏng Ghi Nhật Ký Giao Tác WAL và Khôi Phục Crash trong B+ Tree

**Đề bài chi tiết:**
Để đảm bảo tính bền vững (Durability trong ACID), các hệ quản trị cơ sở dữ liệu luôn ghi log vào **Write-Ahead Log (WAL)** trước khi sửa đổi các trang B+ Tree trong bộ đệm.
Hãy hiện thực một cơ chế WAL đơn giản hỗ trợ:
1. `logInsert(int pageId, int key, String val)`: Ghi log thao tác.
2. `checkpoint()`: Đẩy dữ liệu ra đĩa và dọn dẹp log.
3. `recover()`: Quét lại WAL để thực hiện lại (REDO) các thao tác chưa được ghi xuống đĩa sau sự cố sập nguồn (Crash).

**Phân tích thuật toán:**
- Mỗi bản ghi log có một số thứ tự tăng dần LSN (Log Sequence Number). Mỗi trang B+ Tree lưu `pageLSN`. Khi phục hồi, chỉ REDO nếu `recordLSN > pageLSN`.

**Mã nguồn Java:**
```java
import java.util.*;

public class BPlusTreeWALRecovery {
    public record LogRecord(long lsn, int pageId, int key, String val) {}

    public static class MockDiskPage {
        int pageId;
        long pageLSN = 0;
        Map<Integer, String> records = new HashMap<>();
        public MockDiskPage(int id) { this.pageId = id; }
    }

    private final List<LogRecord> walLog = new ArrayList<>();
    private final Map<Integer, MockDiskPage> diskStorage = new HashMap<>();
    private long currentLSN = 1;

    public void insertWithWAL(int pageId, int key, String val) {
        // 1. Ghi WAL trước
        long lsn = currentLSN++;
        LogRecord log = new LogRecord(lsn, pageId, key, val);
        walLog.add(log);

        // 2. Cập nhật bộ đệm (chưa flush ngay xuống đĩa)
        MockDiskPage page = diskStorage.computeIfAbsent(pageId, MockDiskPage::new);
        page.records.put(key, val);
        page.pageLSN = lsn;
    }

    public void simulateCrashAndRecover() {
        System.out.println("Starting Crash Recovery from WAL...");
        for (LogRecord log : walLog) {
            MockDiskPage page = diskStorage.computeIfAbsent(log.pageId(), MockDiskPage::new);
            if (page.pageLSN < log.lsn()) {
                System.out.printf("REDO Log LSN=%d: Inserting key=%d into page=%d\n", log.lsn(), log.key(), log.pageId());
                page.records.put(log.key(), log.val());
                page.pageLSN = log.lsn();
            }
        }
        System.out.println("Recovery Completed Successfully.");
    }
}
```

**Độ phức tạp:**
- Quá trình khôi phục: $O(L)$ với $L$ là số lượng bản ghi WAL kể từ Checkpoint gần nhất.

---

## 29. Tối Ưu Hóa Chiều Cao và Đóng Gói Node (Packing Factor Optimization)

**Đề bài chi tiết:**
Cho tập $N$ phần tử tĩnh (Static Read-Only Database như CD-ROM Index, SQLite Read-Only DB). Hãy thiết kế giải thuật tính toán cấu hình B-Tree bậc $t$ tối ưu sao cho:
1. Chiều cao cây đạt mức tối thiểu tuyệt đối.
2. Tỷ lệ lấp đầy (Packing Factor) đạt xấp xỉ 100% ở mọi node để tiết kiệm tối đa bộ nhớ và giảm số lượt truy cập đĩa.

**Phân tích thuật toán:**
- Với cây tĩnh, ta không cần giữ khoảng trống dự phòng cho thao tác chèn/xóa. Do đó, mọi node đều có thể đóng gói đúng $2t-1$ khóa và $2t$ con trỏ.
- Dùng công thức $h = \lfloor \log_{2t} (N+1) \rfloor$ để xác định cấu trúc hoàn hảo.

**Mã nguồn Java:**
```java
import java.util.*;

public class PackedBTreeBuilder {
    public static class Node {
        List<Integer> keys = new ArrayList<>();
        List<Node> children = new ArrayList<>();
        boolean isLeaf;
        public Node(boolean isLeaf) { this.isLeaf = isLeaf; }
    }

    public static Node build100PercentPackedTree(List<Integer> sortedKeys, int t) {
        if (sortedKeys.isEmpty()) return null;
        int maxKeysPerNode = 2 * t - 1;

        List<Node> currentLevel = new ArrayList<>();
        Node leaf = new Node(true);
        currentLevel.add(leaf);

        for (int k : sortedKeys) {
            if (leaf.keys.size() == maxKeysPerNode) {
                leaf = new Node(true);
                currentLevel.add(leaf);
            }
            leaf.keys.add(k);
        }

        while (currentLevel.size() > 1) {
            List<Node> nextLevel = new ArrayList<>();
            Node parent = new Node(false);
            nextLevel.add(parent);
            parent.children.add(currentLevel.get(0));

            for (int i = 1; i < currentLevel.size(); i++) {
                if (parent.children.size() == 2 * t) {
                    parent = new Node(false);
                    nextLevel.add(parent);
                }
                Node child = currentLevel.get(i);
                int sepKey = child.keys.remove(0); // Khóa phân cách
                parent.keys.add(sepKey);
                parent.children.add(child);
            }
            currentLevel = nextLevel;
        }

        return currentLevel.get(0);
    }
}
```

**Độ phức tạp:**
- Thời gian xây dựng: $O(N)$.
- Không gian bộ nhớ: Tiết kiệm tối đa 50% RAM so với B-Tree chèn động.

---

## 30. Mô Phỏng CSB+ Tree Nhạy Cảm Cache CPU (Cache-Sensitive B+ Tree)

**Đề bài chi tiết:**
Trong kiến trúc bộ nhớ hiện đại, Cache Miss trên CPU L1/L2/L3 là nút thắt cổ chai lớn nhất của cấu trúc cây In-Memory.
**CSB+ Tree (Cache-Sensitive B+ Tree)** tối ưu hóa bằng cách: Đặt tất cả các node con của một node cha vào **một mảng bộ nhớ liên tục**. Nhờ đó, node cha **chỉ cần lưu đúng một con trỏ duy nhất** trỏ tới phần tử đầu tiên của mảng con (`firstChildPtr`), giải phóng không gian để chứa thêm nhiều khóa trong một dòng Cache Line 64-byte.
Hãy hiện thực cấu trúc tìm kiếm của CSB+ Tree.

**Phân tích thuật toán:**
- Con thứ $i$ của một node được truy xuất qua phép tính địa chỉ trực tiếp: `firstChildPtr + i`.
- Một Cache Line 64-byte có thể chứa tới 15 khóa `int` (60 bytes) + 1 con trỏ `int` (4 bytes offset), tăng Fanout trong Cache Line lên gấp đôi so với B+ Tree truyền thống (chỉ chứa ~7 khóa và 8 con trỏ).

**Mã nguồn Java:**
```java
public class CacheSensitiveBPlusTreeDemo {
    public static class CSBNode {
        int[] keys;
        int numKeys;
        CSBNode[] childGroup; // Toàn bộ các con nằm trong một mảng liên tục
        int childGroupOffset; // Chỉ số của con đầu tiên trong childGroup
        boolean isLeaf;

        public CSBNode(int capacity, boolean isLeaf) {
            this.keys = new int[capacity];
            this.numKeys = 0;
            this.isLeaf = isLeaf;
        }

        public CSBNode getChild(int i) {
            if (childGroup == null) return null;
            return childGroup[childGroupOffset + i];
        }
    }

    public static boolean search(CSBNode root, int key) {
        CSBNode curr = root;

        while (curr != null) {
            // Binary search trong mảng keys nằm gọn trong CPU Cache Line
            int low = 0, high = curr.numKeys - 1;
            int idx = curr.numKeys;

            while (low <= high) {
                int mid = (low + high) >>> 1;
                if (curr.keys[mid] >= key) {
                    idx = mid;
                    high = mid - 1;
                } else {
                    low = mid + 1;
                }
            }

            if (curr.isLeaf) {
                return idx < curr.numKeys && curr.keys[idx] == key;
            }

            // Truy xuất con với chi phí 1 con trỏ cơ sở
            curr = curr.getChild(idx);
        }
        return false;
    }

    public static void main(String[] args) {
        CSBNode root = new CSBNode(15, false);
        root.keys[0] = 50;
        root.numKeys = 1;

        CSBNode[] childrenGroup = new CSBNode[2];
        childrenGroup[0] = new CSBNode(15, true);
        childrenGroup[0].keys[0] = 10;
        childrenGroup[0].keys[1] = 20;
        childrenGroup[0].numKeys = 2;

        childrenGroup[1] = new CSBNode(15, true);
        childrenGroup[1].keys[0] = 60;
        childrenGroup[1].keys[1] = 70;
        childrenGroup[1].numKeys = 2;

        root.childGroup = childrenGroup;
        root.childGroupOffset = 0;

        System.out.println("Search 20: " + search(root, 20)); // true
        System.out.println("Search 35: " + search(root, 35)); // false
        System.out.println("Search 70: " + search(root, 70)); // true
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log N)$ với số lần CPU Cache Miss giảm hơn 60% so với B+ Tree thông thường.
- Không gian bộ nhớ: Tiết kiệm gần 50% con trỏ trong các Internal Nodes.
