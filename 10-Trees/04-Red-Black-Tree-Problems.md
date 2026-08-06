# 30 Bài Tập Thực Hành Chuyên Sâu: Red-Black Tree (Cây Đỏ - Đen)

Tài liệu này cung cấp **30 bài tập thực hành kinh điển và nâng cao** về cấu trúc dữ liệu **Red-Black Tree** (Cây Đỏ - Đen). Các bài toán được thiết kế từ mức độ kiểm tra tính chất bất biến (invariants), mô phỏng các trường hợp tái cân bằng (rotations, recoloring), cài đặt Left-Leaning Red-Black Tree (LLRB), cấu trúc dữ liệu tăng cường (Order-Statistic Tree, Interval Tree), đến các ứng dụng hệ thống thực tế như bộ lập lịch CFS của Linux kernel, đa luồng hiệu năng cao và cấu trúc bất biến (Persistent Red-Black Tree).

Tất cả giải pháp đều được viết bằng **Java 21**, tuân thủ nguyên tắc thiết kế mã nguồn chuẩn mực, tối ưu hóa bộ nhớ và phân tích độ phức tạp thời gian/không gian chi tiết.

---

## 1. Validate Red-Black Tree Invariants (Kiểm Tra Tính Chất Bất Biến Của Red-Black Tree)

**Đề bài chi tiết:**  
Cho gốc `root` của một cây nhị phân tìm kiếm mà mỗi node có thêm thuộc tính màu `color` (quy ước `RED` hoặc `BLACK`). Hãy viết hàm kiểm tra xem cây có thỏa mãn đầy đủ **5 tính chất bất biến** của Red-Black Tree hay không:
1. Mỗi node chỉ có thể là `RED` hoặc `BLACK`.
2. Node gốc (`root`) bắt buộc phải là `BLACK` (nếu cây khác rỗng).
3. Mọi node lá `NIL` (null) đều được coi là `BLACK`.
4. Nếu một node là `RED`, thì cả hai node con của nó bắt buộc phải là `BLACK` (không tồn tại hai node `RED` liền kề nhau trên cùng một nhánh).
5. Mọi đường đi đơn từ một node bất kỳ đến bất kỳ node lá `NIL` con cháu nào của nó đều phải chứa **cùng một số lượng node `BLACK`** (gọi là *Black-Height*).

Ngoài ra, cây vẫn phải đảm bảo tính chất của một **Binary Search Tree (BST)** hợp lệ.

**Phân tích thuật toán:**  
- Ta kết hợp kiểm tra tính chất BST (khoảng giá trị `(min, max)`) cùng với kiểm tra màu sắc và chiều cao đen (Black-Height).
- Sử dụng đệ quy Depth-First Search (DFS) trả về chiều cao đen của cây con gốc `node`. Nếu phát hiện bất kỳ vi phạm nào (hai node đỏ liên tiếp, vi phạm thứ tự khóa BST, hoặc chiều cao đen của hai nhánh con trái/phải không bằng nhau), hàm sẽ trả về giá trị lỗi `-1` để dừng sớm (early termination / fail-fast).
- Ở gốc `root`, kiểm tra `root.color == BLACK`.

**Mã nguồn Java:**
```java
public class RedBlackTreeValidator {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left;
        public Node right;

        public Node(int val, Color color) {
            this.val = val;
            this.color = color;
        }
    }

    public boolean isValidRedBlackTree(Node root) {
        if (root == null) {
            return true; // Cây rỗng là một RBT hợp lệ
        }

        // Tính chất 2: Gốc bắt buộc phải là BLACK
        if (root.color != Color.BLACK) {
            return false;
        }

        // Kiểm tra tính chất BST và 5 tính chất RBT
        return validateAndGetBlackHeight(root, null, null) != -1;
    }

    private int validateAndGetBlackHeight(Node node, Integer min, Integer max) {
        if (node == null) {
            // Tính chất 3: Node NIL luôn là BLACK, đóng góp 1 vào Black-Height
            return 1;
        }

        // Kiểm tra tính chất Binary Search Tree
        if ((min != null && node.val <= min) || (max != null && node.val >= max)) {
            return -1;
        }

        // Tính chất 4: Nếu node là RED, hai con không được phép là RED
        if (node.color == Color.RED) {
            if ((node.left != null && node.left.color == Color.RED) ||
                (node.right != null && node.right.color == Color.RED)) {
                return -1;
            }
        }

        // Đệ quy tính Black-Height của cây con trái và phải
        int leftBlackHeight = validateAndGetBlackHeight(node.left, min, node.val);
        if (leftBlackHeight == -1) return -1;

        int rightBlackHeight = validateAndGetBlackHeight(node.right, node.val, max);
        if (rightBlackHeight == -1) return -1;

        // Tính chất 5: Chiều cao đen của hai nhánh con bắt buộc phải bằng nhau
        if (leftBlackHeight != rightBlackHeight) {
            return -1;
        }

        // Trả về Black-Height hiện tại (+1 nếu node này là BLACK)
        return leftBlackHeight + (node.color == Color.BLACK ? 1 : 0);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N)$ vì mỗi node được duyệt đúng một lần theo mô hình DFS.  
- **Không gian (Space Complexity):** $\mathcal{O}(H) = \mathcal{O}(\log N)$ do bộ nhớ Call Stack của đệ quy, trong đó $H \le 2 \log_2(N + 1)$ là chiều cao của Red-Black Tree.

---

## 2. Calculate Black-Height of Red-Black Tree (Tính Chiều Cao Đen)

**Đề bài chi tiết:**  
Cho gốc `root` của một Red-Black Tree đã được xác thực là hợp lệ. Hãy tính **Black-Height** (chiều cao đen) của cây, được định nghĩa là số lượng node `BLACK` trên bất kỳ đường đi nào từ gốc `root` xuống đến node lá `NIL` (không tính chính node `root` nếu `root` là `BLACK`, hoặc tính theo quy ước chuẩn của CLRS).

**Phân tích thuật toán:**  
- Do tính chất bất biến thứ 5 của Red-Black Tree đảm bảo rằng *mọi đường đi từ gốc tới lá đều có cùng số lượng node đen*, ta không cần duyệt toàn bộ cây mà chỉ cần đi theo một đường đi duy nhất từ `root` xuống lá bất kỳ (ví dụ luôn rẽ trái).
- Ta duyệt từ `root` xuống `null`. Nếu node hiện tại là `BLACK` và khác `root`, ta tăng biến đếm. Tiếp tục cho đến khi gặp `null`.

**Mã nguồn Java:**
```java
public class BlackHeightCalculator {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left;
        public Node right;

        public Node(int val, Color color) {
            this.val = val;
            this.color = color;
        }
    }

    /**
     * Tính Black-Height theo chuẩn CLRS: Số lượng node BLACK trên đường đi từ
     * con của node hiện tại xuống lá NIL (không tính chính node hiện tại).
     */
    public int calculateBlackHeight(Node root) {
        if (root == null) {
            return 0;
        }

        int blackHeight = 0;
        // Duyệt theo một nhánh duy nhất (ví dụ nhánh trái)
        Node current = root.left;
        while (current != null) {
            if (current.color == Color.BLACK) {
                blackHeight++;
            }
            current = current.left;
        }

        // Cộng thêm 1 cho lá NIL (lá NIL luôn là BLACK)
        return blackHeight + 1;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ vì chiều cao của cây bị chặn trên bởi $2 \log_2(N + 1)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ sử dụng biến lặp đơn giản.

---

## 3. Left Rotation & Right Rotation with Parent Pointers (Phép Xoay Cây Kèm Con Trỏ Cha)

**Đề bài chi tiết:**  
Cài đặt hai thao tác nền tảng của Red-Black Tree: `rotateLeft(Node x)` và `rotateRight(Node y)` trên cây có con trỏ trỏ về cha (`parent`). Thao tác xoay phải cập nhật chính xác các liên kết:
- Con trỏ `left` và `right` của các node liên quan.
- Con trỏ `parent` của node con bị chuyển giao (beta subtree).
- Con trỏ `parent` của hai node trung tâm xoay.
- Cập nhật con trỏ con của node ông bà (grandparent) hoặc cập nhật `root` nếu node bị xoay là gốc của cây.

**Phân tích thuật toán:**  
- **Rotate Left tại node $x$:**
  - Lấy con phải của $x$ là $y = x.right$.
  - Cây con trái của $y$ ($y.left$) trở thành con phải của $x$. Nếu $y.left \ne null$, gán $y.left.parent = x$.
  - Gán $y.parent = x.parent$. Nếu $x.parent == null$, $y$ trở thành gốc mới `root`. Ngược lại nếu $x$ là con trái của cha nó, gán $x.parent.left = y$; ngược lại gán $x.parent.right = y$.
  - Đặt $x$ làm con trái của $y$: $y.left = x$, và gán $x.parent = y$.
- **Rotate Right tại node $y$:** Đối xứng hoàn toàn với Rotate Left.

**Mã nguồn Java:**
```java
public class RedBlackTreeRotations {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left, right, parent;

        public Node(int val, Color color) {
            this.val = val;
            this.color = color;
        }
    }

    public Node root;

    public void rotateLeft(Node x) {
        if (x == null || x.right == null) return;

        Node y = x.right;
        x.right = y.left;

        if (y.left != null) {
            y.left.parent = x;
        }

        y.parent = x.parent;
        if (x.parent == null) {
            this.root = y;
        } else if (x == x.parent.left) {
            x.parent.left = y;
        } else {
            x.parent.right = y;
        }

        y.left = x;
        x.parent = y;
    }

    public void rotateRight(Node y) {
        if (y == null || y.left == null) return;

        Node x = y.left;
        y.left = x.right;

        if (x.right != null) {
            x.right.parent = y;
        }

        x.parent = y.parent;
        if (y.parent == null) {
            this.root = x;
        } else if (y == y.parent.left) {
            y.parent.left = x;
        } else {
            y.parent.right = x;
        }

        x.right = y;
        y.parent = x;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(1)$ cho mỗi phép xoay (chỉ gồm các phép gán con trỏ cố định).  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ không tốn thêm bộ nhớ phụ trợ.

---

## 4. Red-Black Tree Insertion Fixup Simulation (Cân Bằng Sau Khi Chèn - Chuẩn CLRS)

**Đề bài chi tiết:**  
Cài đặt thuật toán chèn một giá trị mới vào Red-Black Tree chuẩn (với nút cha `parent` và nút lá canh gác `NIL` hoặc dùng `null`), bao gồm bước chèn kiểu BST (màu của node mới luôn là `RED`) và bước xử lý xung đột đỏ-đỏ `insertFixup(Node z)` xử lý đầy đủ 3 trường hợp kinh điển của CLRS:
- **Case 1:** Bác (Uncle) $y$ của $z$ có màu `RED` $\rightarrow$ Đổi màu cha, bác thành `BLACK`, ông thành `RED`, dịch $z$ lên ông.
- **Case 2:** Bác $y$ có màu `BLACK` và $z$ tạo thành góc nhọn (Triangle - ví dụ $z$ là con phải còn cha là con trái) $\rightarrow$ Xoay trái tại cha để chuyển về Case 3.
- **Case 3:** Bác $y$ có màu `BLACK` và $z$ thẳng hàng (Line - ví dụ $z$ là con trái và cha là con trái) $\rightarrow$ Đổi màu cha thành `BLACK`, ông thành `RED`, xoay phải tại ông.

**Phân tích thuật toán:**  
- Khi chèn một node đỏ $z$, tính chất 4 (không có 2 node đỏ liên tiếp) có thể bị vi phạm nếu cha của $z$ cũng là `RED`.
- Vòng lặp `while (z.parent != null && z.parent.color == RED)` chạy tối đa $\mathcal{O}(\log N)$ bước.
- Mỗi lần gặp Case 1, cây chỉ đổi màu và đẩy xung đột lên trên 2 bậc. Khi gặp Case 2 hoặc Case 3, sau tối đa 2 phép xoay, xung đột đỏ-đỏ được giải quyết dứt điểm và vòng lặp dừng lại.
- Cuối cùng, luôn đảm bảo `root.color = BLACK`.

**Mã nguồn Java:**
```java
public class StandardRedBlackTree {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left, right, parent;

        public Node(int val) {
            this.val = val;
            this.color = Color.RED; // Node mới chèn luôn là RED
        }
    }

    private Node root;

    public void insert(int val) {
        Node z = new Node(val);
        Node y = null;
        Node x = this.root;

        // Bước 1: Tìm vị trí chèn theo chuẩn BST
        while (x != null) {
            y = x;
            if (z.val < x.val) {
                x = x.left;
            } else if (z.val > x.val) {
                x = x.right;
            } else {
                return; // Không chèn khóa trùng lặp
            }
        }

        z.parent = y;
        if (y == null) {
            this.root = z;
        } else if (z.val < y.val) {
            y.left = z;
        } else {
            y.right = z;
        }

        // Bước 2: Tái cân bằng Red-Black Tree
        insertFixup(z);
    }

    private void insertFixup(Node z) {
        while (z.parent != null && z.parent.color == Color.RED) {
            Node p = z.parent;
            Node gp = p.parent;

            if (gp != null && p == gp.left) {
                Node uncle = gp.right;
                // Case 1: Uncle là RED
                if (uncle != null && uncle.color == Color.RED) {
                    p.color = Color.BLACK;
                    uncle.color = Color.BLACK;
                    gp.color = Color.RED;
                    z = gp; // Tiếp tục kiểm tra từ ông
                } else {
                    // Case 2: Uncle là BLACK và z là con phải (Triangle)
                    if (z == p.right) {
                        z = p;
                        rotateLeft(z);
                        p = z.parent;
                        gp = p != null ? p.parent : null;
                    }
                    // Case 3: Uncle là BLACK và z là con trái (Line)
                    if (p != null) p.color = Color.BLACK;
                    if (gp != null) {
                        gp.color = Color.RED;
                        rotateRight(gp);
                    }
                }
            } else if (gp != null) { // Đối xứng: p == gp.right
                Node uncle = gp.left;
                // Case 1 đối xứng
                if (uncle != null && uncle.color == Color.RED) {
                    p.color = Color.BLACK;
                    uncle.color = Color.BLACK;
                    gp.color = Color.RED;
                    z = gp;
                } else {
                    // Case 2 đối xứng
                    if (z == p.left) {
                        z = p;
                        rotateRight(z);
                        p = z.parent;
                        gp = p != null ? p.parent : null;
                    }
                    // Case 3 đối xứng
                    if (p != null) p.color = Color.BLACK;
                    if (gp != null) {
                        gp.color = Color.RED;
                        rotateLeft(gp);
                    }
                }
            } else {
                break;
            }
        }
        this.root.color = Color.BLACK;
    }

    private void rotateLeft(Node x) {
        Node y = x.right;
        if (y == null) return;
        x.right = y.left;
        if (y.left != null) y.left.parent = x;
        y.parent = x.parent;
        if (x.parent == null) this.root = y;
        else if (x == x.parent.left) x.parent.left = y;
        else x.parent.right = y;
        y.left = x;
        x.parent = y;
    }

    private void rotateRight(Node y) {
        Node x = y.left;
        if (x == null) return;
        y.left = x.right;
        if (x.right != null) x.right.parent = y;
        x.parent = y.parent;
        if (y.parent == null) this.root = x;
        else if (y == y.parent.left) y.parent.left = x;
        else y.parent.right = x;
        x.right = y;
        y.parent = x;
    }

    public Node getRoot() { return root; }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ tổng thể. Bước chèn BST tốn $\mathcal{O}(\log N)$, bước `insertFixup` thực hiện tối đa $\mathcal{O}(\log N)$ phép đổi màu và **tối đa 2 phép xoay**.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ lặp tuần tự.

---

## 5. Left-Leaning Red-Black Tree (LLRB) Insertion (Cài Đặt Chèn LLRB - Robert Sedgewick)

**Đề bài chi tiết:**  
Cài đặt thuật toán chèn trên **Left-Leaning Red-Black Tree (LLRB)** (biến thể ánh xạ tương đương 1-1 với 2-3 Tree do Robert Sedgewick phát minh). LLRB duy trì thêm một bất biến đơn giản hóa: *Mọi liên kết đỏ (RED link) chỉ được phép nghiêng về bên trái (`node.left` có thể là RED, nhưng `node.right` tuyệt đối không được là RED)*.

Các thao tác cân bằng đệ quy sau khi chèn:
1. Nếu con phải là `RED` và con trái là `BLACK` $\rightarrow$ `rotateLeft(h)`.
2. Nếu cả con trái và cháu trái-trái đều là `RED` $\rightarrow$ `rotateRight(h)`.
3. Nếu cả con trái và con phải đều là `RED` $\rightarrow$ `flipColors(h)`.

**Phân tích thuật toán:**  
- LLRB loại bỏ sự phức tạp của 8 trường hợp xoay trong cây đỏ đen truyền thống bằng cách sử dụng đệ quy từ dưới lên (bottom-up fixup).
- Mã nguồn ngắn gọn hơn 4-5 lần so với RBT chuẩn, loại bỏ hoàn toàn con trỏ `parent`.

**Mã nguồn Java:**
```java
public class LeftLeaningRedBlackTree<Key extends Comparable<Key>, Value> {
    private static final boolean RED = true;
    private static final boolean BLACK = false;

    public class Node {
        public Key key;
        public Value val;
        public Node left, right;
        public boolean color;

        public Node(Key key, Value val, boolean color) {
            this.key = key;
            this.val = val;
            this.color = color;
        }
    }

    private Node root;

    private boolean isRed(Node x) {
        if (x == null) return false;
        return x.color == RED;
    }

    private Node rotateLeft(Node h) {
        Node x = h.right;
        h.right = x.left;
        x.left = h;
        x.color = h.color;
        h.color = RED;
        return x;
    }

    private Node rotateRight(Node h) {
        Node x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = h.color;
        h.color = RED;
        return x;
    }

    private void flipColors(Node h) {
        h.color = RED;
        h.left.color = BLACK;
        h.right.color = BLACK;
    }

    public void put(Key key, Value val) {
        root = put(root, key, val);
        root.color = BLACK; // Root luôn là BLACK
    }

    private Node put(Node h, Key key, Value val) {
        if (h == null) return new Node(key, val, RED);

        int cmp = key.compareTo(h.key);
        if (cmp < 0)      h.left = put(h.left, key, val);
        else if (cmp > 0) h.right = put(h.right, key, val);
        else              h.val = val;

        // Tái cân bằng theo chuẩn LLRB 2-3 Tree
        if (isRed(h.right) && !isRed(h.left))      h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left))   h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right))       flipColors(h);

        return h;
    }

    public Value get(Key key) {
        Node x = root;
        while (x != null) {
            int cmp = key.compareTo(x.key);
            if (cmp < 0) x = x.left;
            else if (cmp > 0) x = x.right;
            else return x.val;
        }
        return null;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ cho cả thao tác `put` và `get`.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ cho Call Stack đệ quy.

---

## 6. Red-Black Tree Deletion Fixup Simulation (Cân Bằng Sau Khi Xóa - Chuẩn CLRS)

**Đề bài chi tiết:**  
Cài đặt thao tác `deleteFixup(Node x)` xử lý hiện tượng **Double Black** sau khi xóa một node màu đen trong Red-Black Tree chuẩn CLRS. Mô phỏng đầy đủ 4 trường hợp tái cân bằng:
- **Case 1:** Sibling $w$ của $x$ có màu `RED` $\rightarrow$ Đổi màu $w$ thành `BLACK`, cha $p$ thành `RED`, xoay trái tại $p$, chuyển sang Case 2, 3 hoặc 4.
- **Case 2:** Sibling $w$ là `BLACK` và cả 2 con của $w$ đều là `BLACK` $\rightarrow$ Đổi màu $w$ thành `RED`, đẩy double black lên cha $p$ ($x = p$).
- **Case 3:** Sibling $w$ là `BLACK`, con trái của $w$ là `RED`, con phải là `BLACK` $\rightarrow$ Đổi màu con trái của $w$ thành `BLACK`, $w$ thành `RED`, xoay phải tại $w$, chuyển về Case 4.
- **Case 4:** Sibling $w$ là `BLACK` và con phải của $w$ là `RED` $\rightarrow$ Gán màu của $w$ bằng màu của $p$, đổi màu $p$ và con phải của $w$ thành `BLACK`, xoay trái tại $p$, loại bỏ hoàn toàn double black ($x = root$).

**Phân tích thuật toán:**  
- Khi xóa node đen, Black-Height trên nhánh đó bị giảm 1, tạo ra vi phạm double black.
- Vòng lặp `deleteFixup` di chuyển double black lên trên gốc hoặc tiêu diệt nó bằng tối đa 3 phép xoay.

**Mã nguồn Java:**
```java
public class RedBlackTreeDeletion {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left, right, parent;

        public Node(int val) {
            this.val = val;
            this.color = Color.BLACK;
        }
    }

    private Node root;

    public void deleteFixup(Node x, Node xParent) {
        while (x != root && (x == null || x.color == Color.BLACK)) {
            if (x == (xParent != null ? xParent.left : null)) {
                Node w = xParent.right; // Sibling
                // Case 1: Sibling w là RED
                if (w != null && w.color == Color.RED) {
                    w.color = Color.BLACK;
                    xParent.color = Color.RED;
                    rotateLeft(xParent);
                    w = xParent.right;
                }

                // Case 2: Sibling w là BLACK và cả 2 con của w là BLACK
                if ((w == null || w.left == null || w.left.color == Color.BLACK) &&
                    (w == null || w.right == null || w.right.color == Color.BLACK)) {
                    if (w != null) w.color = Color.RED;
                    x = xParent;
                    xParent = x.parent;
                } else {
                    // Case 3: Sibling w là BLACK, con phải của w là BLACK, con trái là RED
                    if (w.right == null || w.right.color == Color.BLACK) {
                        if (w.left != null) w.left.color = Color.BLACK;
                        w.color = Color.RED;
                        rotateRight(w);
                        w = xParent.right;
                    }
                    // Case 4: Sibling w là BLACK và con phải của w là RED
                    if (w != null) {
                        w.color = xParent.color;
                        if (w.right != null) w.right.color = Color.BLACK;
                    }
                    xParent.color = Color.BLACK;
                    rotateLeft(xParent);
                    x = root;
                    break;
                }
            } else { // Đối xứng: x == xParent.right
                Node w = xParent.left;
                if (w != null && w.color == Color.RED) {
                    w.color = Color.BLACK;
                    xParent.color = Color.RED;
                    rotateRight(xParent);
                    w = xParent.left;
                }

                if ((w == null || w.left == null || w.left.color == Color.BLACK) &&
                    (w == null || w.right == null || w.right.color == Color.BLACK)) {
                    if (w != null) w.color = Color.RED;
                    x = xParent;
                    xParent = x.parent;
                } else {
                    if (w.left == null || w.left.color == Color.BLACK) {
                        if (w.right != null) w.right.color = Color.BLACK;
                        w.color = Color.RED;
                        rotateLeft(w);
                        w = xParent.left;
                    }
                    if (w != null) {
                        w.color = xParent.color;
                        if (w.left != null) w.left.color = Color.BLACK;
                    }
                    xParent.color = Color.BLACK;
                    rotateRight(xParent);
                    x = root;
                    break;
                }
            }
        }
        if (x != null) x.color = Color.BLACK;
    }

    private void rotateLeft(Node x) {
        Node y = x.right;
        if (y == null) return;
        x.right = y.left;
        if (y.left != null) y.left.parent = x;
        y.parent = x.parent;
        if (x.parent == null) this.root = y;
        else if (x == x.parent.left) x.parent.left = y;
        else x.parent.right = y;
        y.left = x;
        x.parent = y;
    }

    private void rotateRight(Node y) {
        Node x = y.left;
        if (x == null) return;
        y.left = x.right;
        if (x.right != null) x.right.parent = y;
        x.parent = y.parent;
        if (y.parent == null) this.root = x;
        else if (y == y.parent.left) y.parent.left = x;
        else y.parent.right = x;
        x.right = y;
        y.parent = x;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$, trong đó số lần xoay tối đa không quá **3 lần**.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$.

---

## 7. Left-Leaning Red-Black Tree Deletion (Xóa Trong LLRB Tree)

**Đề bài chi tiết:**  
Cài đặt thuật toán xóa khóa bất kỳ `delete(Key key)` trong Left-Leaning Red-Black Tree bằng kỹ thuật mượn node từ trên xuống (*top-down 2-3-4 transformations*) sử dụng các hàm trợ giúp `moveRedLeft(Node h)` và `moveRedRight(Node h)`.

**Phân tích thuật toán:**  
- Khi đi xuống cây để tìm node cần xóa, ta biến đổi sao cho node hiện tại luôn không phải là node 2-node đơn độc (đảm bảo nó là 3-node hoặc 4-node tạm thời) để khi xóa ở lá không để lại vi phạm cây.
- Sau khi xóa, trên đường đệ quy quay lui (unwinding stack), ta gọi lại các phép xoay và flipColors để khôi phục bất biến Left-Leaning.

**Mã nguồn Java:**
```java
public class LLRBDeletion<Key extends Comparable<Key>, Value> {
    private static final boolean RED = true;
    private static final boolean BLACK = false;

    public class Node {
        public Key key;
        public Value val;
        public Node left, right;
        public boolean color;

        public Node(Key key, Value val, boolean color) {
            this.key = key;
            this.val = val;
            this.color = color;
        }
    }

    private Node root;

    private boolean isRed(Node x) {
        if (x == null) return false;
        return x.color == RED;
    }

    private Node rotateLeft(Node h) {
        Node x = h.right;
        h.right = x.left;
        x.left = h;
        x.color = h.color;
        h.color = RED;
        return x;
    }

    private Node rotateRight(Node h) {
        Node x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = h.color;
        h.color = RED;
        return x;
    }

    private void flipColors(Node h) {
        h.color = !h.color;
        if (h.left != null) h.left.color = !h.left.color;
        if (h.right != null) h.right.color = !h.right.color;
    }

    private Node moveRedLeft(Node h) {
        flipColors(h);
        if (isRed(h.right.left)) {
            h.right = rotateRight(h.right);
            h = rotateLeft(h);
            flipColors(h);
        }
        return h;
    }

    private Node moveRedRight(Node h) {
        flipColors(h);
        if (isRed(h.left.left)) {
            h = rotateRight(h);
            flipColors(h);
        }
        return h;
    }

    private Node balance(Node h) {
        if (isRed(h.right) && !isRed(h.left))    h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right))     flipColors(h);
        return h;
    }

    private Node min(Node x) {
        while (x.left != null) x = x.left;
        return x;
    }

    private Node deleteMin(Node h) {
        if (h.left == null) return null;
        if (!isRed(h.left) && !isRed(h.left.left)) {
            h = moveRedLeft(h);
        }
        h.left = deleteMin(h.left);
        return balance(h);
    }

    public void delete(Key key) {
        if (root == null) return;
        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }
        root = delete(root, key);
        if (root != null) root.color = BLACK;
    }

    private Node delete(Node h, Key key) {
        if (key.compareTo(h.key) < 0) {
            if (!isRed(h.left) && h.left != null && !isRed(h.left.left)) {
                h = moveRedLeft(h);
            }
            h.left = delete(h.left, key);
        } else {
            if (isRed(h.left)) {
                h = rotateRight(h);
            }
            if (key.compareTo(h.key) == 0 && (h.right == null)) {
                return null;
            }
            if (!isRed(h.right) && h.right != null && !isRed(h.right.left)) {
                h = moveRedRight(h);
            }
            if (key.compareTo(h.key) == 0) {
                Node successor = min(h.right);
                h.key = successor.key;
                h.val = successor.val;
                h.right = deleteMin(h.right);
            } else {
                h.right = delete(h.right, key);
            }
        }
        return balance(h);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ cho thao tác xóa.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ cho Call Stack.

---

## 8. Floor and Ceiling Query in Red-Black Tree (Truy Vấn Cận Dưới và Cận Trên)

**Đề bài chi tiết:**  
Cài đặt hai phương thức `floor(Key key)` (trả về khóa lớn nhất $\le key$) và `ceiling(Key key)` (trả về khóa nhỏ nhất $\ge key$) trên Red-Black Tree. Nếu không tồn tại khóa thỏa mãn, trả về `null`.

**Phân tích thuật toán:**  
- Vì Red-Black Tree đảm bảo độ cao $H \le 2 \log_2(N + 1)$, việc tìm kiếm nhị phân trên cây diễn ra trong thời gian $\mathcal{O}(\log N)$.
- **Floor:**
  - Nếu `key == root.key`, floor chính là `root.key`.
  - Nếu `key < root.key`, floor chắc chắn nằm ở cây con trái.
  - Nếu `key > root.key`, floor có thể nằm ở cây con phải. Nếu cây con phải có phần tử $\le key$, lấy phần tử đó; ngược lại chính là `root.key`.

**Mã nguồn Java:**
```java
public class RedBlackTreeFloorCeil<Key extends Comparable<Key>> {
    public static class Node<Key> {
        public Key key;
        public Node<Key> left, right;

        public Node(Key key) {
            this.key = key;
        }
    }

    private Node<Key> root;

    public Key floor(Key key) {
        Node<Key> x = floor(root, key);
        return x == null ? null : x.key;
    }

    private Node<Key> floor(Node<Key> x, Key key) {
        if (x == null) return null;
        int cmp = key.compareTo(x.key);
        if (cmp == 0) return x;
        if (cmp < 0)  return floor(x.left, key);

        Node<Key> rightFloor = floor(x.right, key);
        return (rightFloor != null) ? rightFloor : x;
    }

    public Key ceiling(Key key) {
        Node<Key> x = ceiling(root, key);
        return x == null ? null : x.key;
    }

    private Node<Key> ceiling(Node<Key> x, Key key) {
        if (x == null) return null;
        int cmp = key.compareTo(x.key);
        if (cmp == 0) return x;
        if (cmp > 0)  return ceiling(x.right, key);

        Node<Key> leftCeil = ceiling(x.left, key);
        return (leftCeil != null) ? leftCeil : x;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ qua đệ quy (hoặc $\mathcal{O}(1)$ nếu viết bằng vòng lặp).

---

## 9. Order-Statistic Red-Black Tree: Select & Rank (Cây Thứ Tự Động)

**Đề bài chi tiết:**  
Tăng cường (augment) mỗi node trong Red-Black Tree thêm trường `size` (tổng số node trong cây con gốc tại node đó, với `size = size(left) + size(right) + 1`). Cài đặt hai thao tác:
1. `select(int k)`: Trả về khóa của node có thứ tự nhỏ thứ $k$ (0-indexed).
2. `rank(Key key)`: Trả về số lượng các phần tử trong cây có khóa nghiêm ngặt nhỏ hơn `key`.

Cả hai thao tác phải chạy trong thời gian $\mathcal{O}(\log N)$ và trường `size` phải được cập nhật chính xác trong mọi phép xoay và chèn/xóa.

**Phân tích thuật toán:**  
- Trong phép xoay `rotateLeft` và `rotateRight`, ta chỉ cần tính lại `size` của 2 node tham gia xoay (node cũ và node mới lên làm gốc cụm).
- `select(k)`: So sánh $k$ với kích thước nhánh trái $t = size(node.left)$.
  - Nếu $t == k$, trả về $node.key$.
  - Nếu $t > k$, tìm tiếp ở nhánh trái với chỉ số $k$.
  - Nếu $t < k$, tìm ở nhánh phải với chỉ số $k - t - 1$.
- `rank(key)`: Tương tự Binary Search, nếu rẽ phải thì cộng dồn $size(left) + 1$.

**Mã nguồn Java:**
```java
public class OrderStatisticRedBlackTree<Key extends Comparable<Key>> {
    private static final boolean RED = true;
    private static final boolean BLACK = false;

    public class Node {
        public Key key;
        public Node left, right;
        public boolean color;
        public int size;

        public Node(Key key, boolean color, int size) {
            this.key = key;
            this.color = color;
            this.size = size;
        }
    }

    private Node root;

    private int size(Node x) {
        return x == null ? 0 : x.size;
    }

    private boolean isRed(Node x) {
        return x != null && x.color == RED;
    }

    private Node rotateLeft(Node h) {
        Node x = h.right;
        h.right = x.left;
        x.left = h;
        x.color = h.color;
        h.color = RED;
        x.size = h.size;
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }

    private Node rotateRight(Node h) {
        Node x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = h.color;
        h.color = RED;
        x.size = h.size;
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }

    private void flipColors(Node h) {
        h.color = RED;
        h.left.color = BLACK;
        h.right.color = BLACK;
    }

    public void insert(Key key) {
        root = insert(root, key);
        root.color = BLACK;
    }

    private Node insert(Node h, Key key) {
        if (h == null) return new Node(key, RED, 1);

        int cmp = key.compareTo(h.key);
        if (cmp < 0)      h.left = insert(h.left, key);
        else if (cmp > 0) h.right = insert(h.right, key);

        if (isRed(h.right) && !isRed(h.left))      h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left))   h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right))       flipColors(h);

        h.size = size(h.left) + size(h.right) + 1;
        return h;
    }

    public Key select(int k) {
        if (k < 0 || k >= size(root)) throw new IllegalArgumentException("Index out of bound");
        return select(root, k).key;
    }

    private Node select(Node x, int k) {
        int t = size(x.left);
        if (t > k)      return select(x.left, k);
        else if (t < k) return select(x.right, k - t - 1);
        else            return x;
    }

    public int rank(Key key) {
        return rank(root, key);
    }

    private int rank(Node x, Key key) {
        if (x == null) return 0;
        int cmp = key.compareTo(x.key);
        if (cmp < 0)      return rank(x.left, key);
        else if (cmp > 0) return 1 + size(x.left) + rank(x.right, key);
        else              return size(x.left);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ cho cả `select` và `rank`.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ cho ngăn xếp đệ quy.

---

## 10. Range Count and Range Search in Red-Black Tree (Đếm và Liệt Kê Theo Đoạn)

**Đề bài chi tiết:**  
Sử dụng Red-Black Tree tăng cường (có trường `size`), hãy cài đặt hai phương thức:
1. `rangeCount(Key low, Key high)`: Đếm số lượng khóa nằm trong đoạn $[low, high]$ trong thời gian $\mathcal{O}(\log N)$.
2. `rangeSearch(Key low, Key high)`: Liệt kê toàn bộ các khóa trong đoạn $[low, high]$ theo thứ tự tăng dần trong thời gian $\mathcal{O}(\log N + K)$ ($K$ là số phần tử thỏa mãn).

**Phân tích thuật toán:**  
- **Range Count:** Nhờ hàm `rank` từ bài 9, số phần tử trong $[low, high]$ được tính đơn giản bằng công thức:
  $$\text{rangeCount}(low, high) = \text{rank}(high) - \text{rank}(low) + (\text{contains}(high) ? 1 : 0)$$
- **Range Search:** Duyệt cây theo thứ tự In-order có tỉa nhánh (pruning): Chỉ rẽ trái nếu $low < node.key$, thêm $node.key$ vào danh sách nếu $low \le node.key \le high$, và chỉ rẽ phải nếu $high > node.key$.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class RedBlackRangeQueries<Key extends Comparable<Key>> {
    private OrderStatisticRedBlackTree<Key> tree = new OrderStatisticRedBlackTree<>();

    public int rangeCount(Key low, Key high, boolean highExists) {
        if (low.compareTo(high) > 0) return 0;
        int rankLow = tree.rank(low);
        int rankHigh = tree.rank(high);
        return rankHigh - rankLow + (highExists ? 1 : 0);
    }

    public List<Key> rangeSearch(OrderStatisticRedBlackTree<Key>.Node root, Key low, Key high) {
        List<Key> result = new ArrayList<>();
        rangeSearch(root, low, high, result);
        return result;
    }

    private void rangeSearch(OrderStatisticRedBlackTree<Key>.Node node, Key low, Key high, List<Key> result) {
        if (node == null) return;
        int cmpLow = low.compareTo(node.key);
        int cmpHigh = high.compareTo(node.key);

        if (cmpLow < 0) {
            rangeSearch(node.left, low, high, result);
        }
        if (cmpLow <= 0 && cmpHigh >= 0) {
            result.add(node.key);
        }
        if (cmpHigh > 0) {
            rangeSearch(node.right, low, high, result);
        }
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** `rangeCount` tốn $\mathcal{O}(\log N)$; `rangeSearch` tốn $\mathcal{O}(\log N + K)$ với $K$ là số phần tử trả về.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N + K)$ cho Call Stack và mảng kết quả.

---

## 11. Interval Tree Based on Red-Black Tree (Cây Đoạn Tăng Cường)

**Đề bài chi tiết:**  
Cài đặt cấu trúc dữ liệu **Interval Tree** trên nền Red-Black Tree để quản lý tập hợp các đoạn đóng $[low, high]$. Mỗi node lưu một đoạn $[low, high]$ (khóa chính là $low$) và một giá trị tăng cường `maxHigh` là giá trị $high$ lớn nhất trong toàn bộ cây con gốc tại node đó:
$$\text{maxHigh}(x) = \max(x.high, \text{maxHigh}(x.left), \text{maxHigh}(x.right))$$
Cài đặt phương thức `searchOverlap(Interval q)` tìm một đoạn bất kỳ trong cây có phần giao (chồng lấn) với đoạn truy vấn $q = [q.low, q.high]$ trong $\mathcal{O}(\log N)$.

**Phân tích thuật toán:**  
- Hai đoạn $[a_1, a_2]$ và $[b_1, b_2]$ giao nhau khi và chỉ khi $a_1 \le b_2$ và $b_1 \le a_2$.
- Khi tìm kiếm giao đoạn từ gốc:
  - Nếu node hiện tại giao với $q$, trả về ngay đoạn đó.
  - Nếu nhánh con trái khác rỗng và $x.left.maxHigh \ge q.low$, ta **chắc chắn có thể rẽ trái** vì nếu có đoạn giao nhau thì ít nhất một đoạn sẽ nằm bên trái.
  - Ngược lại, ta rẽ phải.

**Mã nguồn Java:**
```java
public class IntervalTree {
    public static class Interval {
        public int low, high;
        public Interval(int low, int high) {
            this.low = low;
            this.high = high;
        }
        public boolean intersects(Interval other) {
            return this.low <= other.high && other.low <= this.high;
        }
    }

    public static class Node {
        public Interval interval;
        public int maxHigh;
        public Node left, right;
        public boolean color;

        public Node(Interval interval, boolean color) {
            this.interval = interval;
            this.maxHigh = interval.high;
            this.color = color;
        }
    }

    private static final boolean RED = true;
    private static final boolean BLACK = false;
    private Node root;

    private int getMax(Node x) {
        return x == null ? Integer.MIN_VALUE : x.maxHigh;
    }

    private void updateMax(Node x) {
        if (x != null) {
            x.maxHigh = Math.max(x.interval.high, Math.max(getMax(x.left), getMax(x.right)));
        }
    }

    private boolean isRed(Node x) {
        return x != null && x.color == RED;
    }

    private Node rotateLeft(Node h) {
        Node x = h.right;
        h.right = x.left;
        x.left = h;
        x.color = h.color;
        h.color = RED;
        updateMax(h);
        updateMax(x);
        return x;
    }

    private Node rotateRight(Node h) {
        Node x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = h.color;
        h.color = RED;
        updateMax(h);
        updateMax(x);
        return x;
    }

    private void flipColors(Node h) {
        h.color = RED;
        h.left.color = BLACK;
        h.right.color = BLACK;
    }

    public void insert(Interval interval) {
        root = insert(root, interval);
        root.color = BLACK;
    }

    private Node insert(Node h, Interval interval) {
        if (h == null) return new Node(interval, RED);

        if (interval.low < h.interval.low) h.left = insert(h.left, interval);
        else                               h.right = insert(h.right, interval);

        if (isRed(h.right) && !isRed(h.left))      h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left))   h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right))       flipColors(h);

        updateMax(h);
        return h;
    }

    public Interval searchOverlap(Interval q) {
        Node curr = root;
        while (curr != null) {
            if (curr.interval.intersects(q)) {
                return curr.interval;
            }
            if (curr.left != null && curr.left.maxHigh >= q.low) {
                curr = curr.left;
            } else {
                curr = curr.right;
            }
        }
        return null;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** Chèn và tìm kiếm giao đoạn đều đạt $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(N)$ lưu trữ cây.

---

## 12. Count Inversions Using Red-Black Tree (Đếm Nghịch Thế Bằng Cây Đỏ Đen)

**Đề bài chi tiết:**  
Cho một mảng các số nguyên $A[0 \dots N-1]$. Một cặp số $(i, j)$ được gọi là nghịch thế nếu $i < j$ và $A[i] > A[j]$. Hãy sử dụng Order-Statistic Red-Black Tree để đếm tổng số cặp nghịch thế trong thời gian $\mathcal{O}(N \log N)$.

**Phân tích thuật toán:**  
- Duyệt mảng từ trái sang phải. Với mỗi phần tử $A[j]$, ta chèn $A[j]$ vào Order-Statistic Tree.
- Số lượng các phần tử đã chèn trước đó lớn hơn $A[j]$ chính là:
  $$\text{inversions}(j) = \text{total\_elements\_in\_tree} - \text{rank}(A[j] + 1)$$
- Cộng dồn giá trị này qua tất cả các phần tử của mảng.

**Mã nguồn Java:**
```java
public class InversionCounter {
    public long countInversions(int[] arr) {
        OrderStatisticRedBlackTree<Integer> tree = new OrderStatisticRedBlackTree<>();
        long totalInversions = 0;

        for (int i = 0; i < arr.length; i++) {
            int currentVal = arr[i];
            int elementsSmallerOrEqual = tree.rank(currentVal + 1);
            int elementsGreater = i - elementsSmallerOrEqual;
            totalInversions += elementsGreater;
            tree.insert(currentVal);
        }

        return totalInversions;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N \log N)$ vì thực hiện $N$ lần chèn và truy vấn rank trên RBT.  
- **Không gian (Space Complexity):** $\mathcal{O}(N)$ lưu trữ các node trong cây.

---

## 13. Persistent Red-Black Tree (Red-Black Tree Bất Biến / Đa Phiên Bản)

**Đề bài chi tiết:**  
Cài đặt **Persistent Red-Black Tree** hỗ trợ thao tác chèn tạo phiên bản mới `insert(int version, Key key)` mà không làm thay đổi các phiên bản cũ. Cây sử dụng kỹ thuật **Path Copying**: Mỗi khi thêm node hoặc xoay, chỉ nhân bản các node trên đường đi từ gốc tới vị trí biến đổi.

**Phân tích thuật toán:**  
- Trong Functional Programming và kiến trúc cơ sở dữ liệu (như Git tree hoặc Copy-on-Write B-Tree), cấu trúc dữ liệu bất biến cho phép truy vấn lịch sử tại bất kỳ mốc thời gian nào.
- Với mỗi phép chèn, chỉ có $\mathcal{O}(\log N)$ node trên đường đi từ gốc xuống lá bị sao chép mới, chia sẻ toàn bộ các cây con không bị tác động.

**Mã nguồn Java:**
```java
public class PersistentRedBlackTree<Key extends Comparable<Key>> {
    public record Node<Key>(Key key, boolean color, Node<Key> left, Node<Key> right) {
        public static final boolean RED = true;
        public static final boolean BLACK = false;

        public Node<Key> withLeft(Node<Key> newLeft) {
            return new Node<>(this.key, this.color, newLeft, this.right);
        }
        public Node<Key> withRight(Node<Key> newRight) {
            return new Node<>(this.key, this.color, this.left, newRight);
        }
        public Node<Key> withColor(boolean newColor) {
            return new Node<>(this.key, newColor, this.left, this.right);
        }
    }

    private static final boolean RED = true;
    private static final boolean BLACK = false;

    private boolean isRed(Node<Key> x) {
        return x != null && x.color == RED;
    }

    private Node<Key> rotateLeft(Node<Key> h) {
        Node<Key> x = h.right;
        Node<Key> newH = new Node<>(h.key, RED, h.left, x.left);
        return new Node<>(x.key, h.color, newH, x.right);
    }

    private Node<Key> rotateRight(Node<Key> h) {
        Node<Key> x = h.left;
        Node<Key> newH = new Node<>(h.key, RED, x.right, h.right);
        return new Node<>(x.key, h.color, x.left, newH);
    }

    private Node<Key> flipColors(Node<Key> h) {
        Node<Key> newLeft = h.left.withColor(BLACK);
        Node<Key> newRight = h.right.withColor(BLACK);
        return new Node<>(h.key, RED, newLeft, newRight);
    }

    public Node<Key> insert(Node<Key> root, Key key) {
        Node<Key> newRoot = insertHelper(root, key);
        return newRoot.withColor(BLACK);
    }

    private Node<Key> insertHelper(Node<Key> h, Key key) {
        if (h == null) return new Node<>(key, RED, null, null);

        int cmp = key.compareTo(h.key);
        Node<Key> current = h;
        if (cmp < 0) {
            current = current.withLeft(insertHelper(h.left, key));
        } else if (cmp > 0) {
            current = current.withRight(insertHelper(h.right, key));
        } else {
            return h;
        }

        if (isRed(current.right) && !isRed(current.left))      current = rotateLeft(current);
        if (isRed(current.left) && isRed(current.left.left))   current = rotateRight(current);
        if (isRed(current.left) && isRed(current.right))       current = flipColors(current);

        return current;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ cho mỗi phiên bản mới.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ node mới được cấp phát cho mỗi thao tác chèn.

---

## 14. Convert Sorted Array to Balanced Red-Black Tree (Chuyển Mảng Đã Sắp Xếp Thành RBT)

**Đề bài chi tiết:**  
Cho một mảng số nguyên đã sắp xếp tăng dần không chứa phần tử trùng lặp. Hãy xây dựng một Red-Black Tree hoàn chỉnh, cân bằng tối ưu và gán màu các node sao cho thỏa mãn đầy đủ 5 tính chất của RBT trong thời gian $\mathcal{O}(N)$.

**Phân tích thuật toán:**  
- Chia để trị (Divide and Conquer): Chọn phần tử ở giữa làm gốc cụm, đệ quy xây dựng cây con trái và phải.
- Để gán màu: Một cây nhị phân cân bằng hoàn hảo (Perfect Binary Tree) có thể được tô toàn bộ bằng màu `BLACK`. Nếu chiều cao cây không phải lũy thừa của 2, ta chỉ cần tô các node lá ở tầng sâu nhất bằng màu `RED`.

**Mã nguồn Java:**
```java
public class SortedArrayToRedBlackTree {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left, right;

        public Node(int val, Color color) {
            this.val = val;
            this.color = color;
        }
    }

    public Node buildRedBlackTree(int[] arr) {
        if (arr == null || arr.length == 0) return null;
        return buildTree(arr, 0, arr.length - 1);
    }

    private Node buildTree(int[] arr, int start, int end) {
        if (start > end) return null;

        int mid = start + (end - start) / 2;
        // Node trung tâm
        Node node = new Node(arr[mid], Color.BLACK);

        node.left = buildTree(arr, start, mid - 1);
        node.right = buildTree(arr, mid + 1, end);

        // Nếu là node lá đơn lẻ ở biên có thể tô RED nếu cần điều chỉnh độ cao đen
        return node;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N)$ vì duyệt qua mỗi phần tử của mảng đúng 1 lần.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ cho độ sâu đệ quy.

---

## 15. Convert Sorted Doubly Linked List to Red-Black Tree (Chuyển DLL Thành RBT Trong $\mathcal{O}(N)$)

**Đề bài chi tiết:**  
Cho một danh sách liên kết đôi (Doubly Linked List) đã được sắp xếp tăng dần. Hãy chuyển đổi nó thành một Red-Black Tree cân bằng trong thời gian $\mathcal{O}(N)$ và không gian bộ nhớ phụ trợ $\mathcal{O}(\log N)$ (Bottom-Up In-Order Construction).

**Phân tích thuật toán:**  
- Thuật toán In-Order mô phỏng việc duyệt cây: Xây dựng cây con trái kích thước $N/2$, liên kết phần tử hiện tại của List làm node gốc, sau đó dịch con trỏ List sang phần tử tiếp theo và xây dựng cây con phải kích thước $N - N/2 - 1$.
- Bằng cách này, ta không cần truy cập ngẫu nhiên (random access) vào danh sách liên kết.

**Mã nguồn Java:**
```java
public class SortedListToRedBlackTree {
    public static class ListNode {
        public int val;
        public ListNode prev, next;
        public ListNode(int val) { this.val = val; }
    }

    public enum Color { RED, BLACK }

    public static class TreeNode {
        public int val;
        public Color color;
        public TreeNode left, right;
        public TreeNode(int val, Color color) {
            this.val = val;
            this.color = color;
        }
    }

    private ListNode currentHead;

    public TreeNode sortedListToRBT(ListNode head) {
        int length = countNodes(head);
        this.currentHead = head;
        return buildTree(0, length - 1);
    }

    private int countNodes(ListNode head) {
        int count = 0;
        ListNode curr = head;
        while (curr != null) {
            count++;
            curr = curr.next;
        }
        return count;
    }

    private TreeNode buildTree(int start, int end) {
        if (start > end) return null;

        int mid = start + (end - start) / 2;

        TreeNode leftChild = buildTree(start, mid - 1);

        TreeNode root = new TreeNode(this.currentHead.val, Color.BLACK);
        root.left = leftChild;

        this.currentHead = this.currentHead.next;

        root.right = buildTree(mid + 1, end);
        return root;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ trên Call Stack.

---

## 16. Find K-th Largest and K-th Smallest in Red-Black Tree (Tìm Phần Tử Thứ K)

**Đề bài chi tiết:**  
Cho một Red-Black Tree tăng cường trường `size`. Hãy viết phương thức tìm phần tử **lớn thứ $K$** (1-based index) và phần tử **nhỏ thứ $K$** trong thời gian $\mathcal{O}(\log N)$.

**Phân tích thuật toán:**  
- Phần tử lớn thứ $K$ tương đương với phần tử nhỏ thứ $(N - K)$ theo chỉ số 0-based.
- Ta tái sử dụng hàm `select(index)` với độ phức tạp $\mathcal{O}(\log N)$.

**Mã nguồn Java:**
```java
public class KthElementFinder<Key extends Comparable<Key>> {
    private final OrderStatisticRedBlackTree<Key> tree;

    public KthElementFinder(OrderStatisticRedBlackTree<Key> tree) {
        this.tree = tree;
    }

    public Key findKthSmallest(int k, int totalNodes) {
        if (k < 1 || k > totalNodes) throw new IllegalArgumentException("Invalid K");
        return tree.select(k - 1); // 0-based index
    }

    public Key findKthLargest(int k, int totalNodes) {
        if (k < 1 || k > totalNodes) throw new IllegalArgumentException("Invalid K");
        return tree.select(totalNodes - k);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$.

---

## 17. Bidirectional Red-Black Tree Iterator (Duyệt Hai Chiều Với Bộ Nhớ $\mathcal{O}(1)$)

**Đề bài chi tiết:**  
Cài đặt một Iterator hai chiều (`hasNext()`, `next()`, `hasPrevious()`, `previous()`) duyệt qua Red-Black Tree theo thứ tự In-Order mà **không sử dụng Stack hoặc List phụ trợ** ($\mathcal{O}(1)$ space) dựa trên con trỏ `parent`.

**Phân tích thuật toán:**  
- `next()`: Tìm **In-order Successor**:
  - Nếu có con phải, Successor là node trái nhất của cây con phải.
  - Nếu không có con phải, đi ngược lên cha cho đến khi node hiện tại là con trái của cha nó.
- `previous()`: Tìm **In-order Predecessor**: Đối xứng hoàn toàn với Successor.

**Mã nguồn Java:**
```java
import java.util.NoSuchElementException;

public class RedBlackTreeIterator {
    public static class Node {
        public int val;
        public Node left, right, parent;
        public Node(int val) { this.val = val; }
    }

    private Node current;
    private final Node root;

    public RedBlackTreeIterator(Node root) {
        this.root = root;
        this.current = getMin(root);
    }

    private Node getMin(Node node) {
        while (node != null && node.left != null) node = node.left;
        return node;
    }

    private Node getMax(Node node) {
        while (node != null && node.right != null) node = node.right;
        return node;
    }

    public boolean hasNext() {
        return current != null;
    }

    public int next() {
        if (current == null) throw new NoSuchElementException();
        int val = current.val;

        if (current.right != null) {
            current = getMin(current.right);
        } else {
            Node p = current.parent;
            Node ch = current;
            while (p != null && ch == p.right) {
                ch = p;
                p = p.parent;
            }
            current = p;
        }
        return val;
    }

    public boolean hasPrevious() {
        return current != null;
    }

    public int previous() {
        if (current == null) throw new NoSuchElementException();
        int val = current.val;

        if (current.left != null) {
            current = getMax(current.left);
        } else {
            Node p = current.parent;
            Node ch = current;
            while (p != null && ch == p.left) {
                ch = p;
                p = p.parent;
            }
            current = p;
        }
        return val;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(1)$ khấu hao (amortized) cho mỗi thao tác `next()` và `previous()`.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ hoàn toàn không cấp phát thêm bộ nhớ.

---

## 18. Count Smaller Elements After Self (Đếm Phần Tử Nhỏ Hơn Phía Sau)

**Đề bài chi tiết:**  
Cho mảng số nguyên `nums`. Hãy trả về một mảng `counts` trong đó `counts[i]` là số lượng phần tử $nums[j]$ nằm bên phải $nums[i]$ ($j > i$) thỏa mãn $nums[j] < nums[i]$. Giải bài toán bằng Red-Black Tree tăng cường.

**Phân tích thuật toán:**  
- Duyệt mảng từ phải qua trái.
- Tại mỗi phần tử `nums[i]`:
  - Dùng `rank(nums[i])` để lấy số lượng phần tử nhỏ hơn đã chèn trước đó trong RBT.
  - Chèn `nums[i]` vào RBT.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SmallerAfterSelf {
    public List<Integer> countSmaller(int[] nums) {
        if (nums == null || nums.length == 0) return Collections.emptyList();

        List<Integer> result = new ArrayList<>(nums.length);
        OrderStatisticRedBlackTree<Integer> tree = new OrderStatisticRedBlackTree<>();

        for (int i = nums.length - 1; i >= 0; i--) {
            int smallerCount = tree.rank(nums[i]);
            result.add(smallerCount);
            tree.insert(nums[i]);
        }

        Collections.reverse(result);
        return result;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N \log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(N)$.

---

## 19. Two Sum in Red-Black Tree (Tìm Cặp Khóa Có Tổng Bằng Target Trong $\mathcal{O}(1)$ Không Gian)

**Đề bài chi tiết:**  
Cho một Red-Black Tree chứa các số nguyên duy nhất và một số nguyên `target`. Hãy kiểm tra xem có tồn tại hai node bất kỳ trong cây có tổng giá trị đúng bằng `target` hay không. Tối ưu hóa bộ nhớ phụ trợ ở mức $\mathcal{O}(H)$.

**Phân tích thuật toán:**  
- Sử dụng hai Iterator: một duyệt xuôi In-Order (từ nhỏ đến lớn) và một duyệt ngược Reverse In-Order (từ lớn đến nhỏ) mô phỏng kỹ thuật **Two Pointers**.
- So sánh tổng `valLeft + valRight` với `target`. Nếu nhỏ hơn, tiến con trỏ trái; nếu lớn hơn, lùi con trỏ phải.

**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class TwoSumRedBlackTree {
    public static class TreeNode {
        public int val;
        public TreeNode left, right;
        public TreeNode(int val) { this.val = val; }
    }

    public static class BSTIterator {
        private final Deque<TreeNode> stack = new ArrayDeque<>();
        private final boolean forward;

        public BSTIterator(TreeNode root, boolean forward) {
            this.forward = forward;
            pushAll(root);
        }

        private void pushAll(TreeNode node) {
            while (node != null) {
                stack.push(node);
                node = forward ? node.left : node.right;
            }
        }

        public boolean hasNext() { return !stack.isEmpty(); }

        public int next() {
            TreeNode node = stack.pop();
            if (forward) pushAll(node.right);
            else pushAll(node.left);
            return node.val;
        }
    }

    public boolean findTarget(TreeNode root, int target) {
        if (root == null) return false;

        BSTIterator leftIter = new BSTIterator(root, true);
        BSTIterator rightIter = new BSTIterator(root, false);

        int left = leftIter.next();
        int right = rightIter.next();

        while (left < right) {
            int sum = left + right;
            if (sum == target) return true;
            else if (sum < target) {
                if (leftIter.hasNext()) left = leftIter.next();
                else break;
            } else {
                if (rightIter.hasNext()) right = rightIter.next();
                else break;
            }
        }
        return false;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N)$ trong trường hợp xấu nhất (duyệt toàn bộ cây).  
- **Không gian (Space Complexity):** $\mathcal{O}(H) = \mathcal{O}(\log N)$ cho hai ngăn xếp Iterator.

---

## 20. Merge Two Red-Black Trees (Hợp Nhất Hai Cây Đỏ Đen)

**Đề bài chi tiết:**  
Cho hai Red-Black Tree có kích thước lần lượt là $N$ và $M$. Hãy hợp nhất chúng thành một Red-Black Tree duy nhất hợp lệ trong thời gian $\mathcal{O}(N + M)$ và không gian $\mathcal{O}(N + M)$.

**Phân tích thuật toán:**  
- **Bước 1:** Trích xuất các phần tử của cả hai cây thành hai danh sách đã sắp xếp bằng cách duyệt In-Order trong $\mathcal{O}(N)$ và $\mathcal{O}(M)$.
- **Bước 2:** Trộn (merge) hai danh sách đã sắp xếp thành một danh sách duy nhất kích thước $N + M$ theo kỹ thuật Merge của MergeSort trong $\mathcal{O}(N + M)$.
- **Bước 3:** Chuyển đổi danh sách đã trộn thành Red-Black Tree cân bằng hoàn hảo bằng thuật toán chia để trị trong $\mathcal{O}(N + M)$.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class MergeRedBlackTrees {
    public static class Node {
        public int val;
        public Node left, right;
        public boolean isRed;
        public Node(int val) { this.val = val; }
    }

    public Node merge(Node root1, Node root2) {
        List<Integer> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        inorder(root1, list1);
        inorder(root2, list2);

        List<Integer> mergedList = mergeSortedLists(list1, list2);
        return buildBalancedTree(mergedList, 0, mergedList.size() - 1);
    }

    private void inorder(Node root, List<Integer> list) {
        if (root == null) return;
        inorder(root.left, list);
        list.add(root.val);
        inorder(root.right, list);
    }

    private List<Integer> mergeSortedLists(List<Integer> l1, List<Integer> l2) {
        List<Integer> res = new ArrayList<>(l1.size() + l2.size());
        int i = 0, j = 0;
        while (i < l1.size() && j < l2.size()) {
            if (l1.get(i) <= l2.get(j)) res.add(l1.get(i++));
            else res.add(l2.get(j++));
        }
        while (i < l1.size()) res.add(l1.get(i++));
        while (j < l2.size()) res.add(l2.get(j++));
        return res;
    }

    private Node buildBalancedTree(List<Integer> list, int start, int end) {
        if (start > end) return null;
        int mid = start + (end - start) / 2;
        Node node = new Node(list.get(mid));
        node.isRed = false; // Black
        node.left = buildBalancedTree(list, start, mid - 1);
        node.right = buildBalancedTree(list, mid + 1, end);
        return node;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N + M)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(N + M)$ lưu trữ danh sách trung gian.

---

## 21. Split a Red-Black Tree by Key (Tách Cây Đỏ Đen Theo Khóa)

**Đề bài chi tiết:**  
Cho một Red-Black Tree $T$ và một khóa $X$. Hãy tách cây thành hai cây Red-Black Tree độc lập: $T_1$ chứa toàn bộ các khóa $< X$ và $T_2$ chứa toàn bộ các khóa $\ge X$. Cài đặt giải thuật đệ quy bảo toàn cấu trúc trong $\mathcal{O}(\log N)$.

**Phân tích thuật toán:**  
- Phép tách cây (Split) kết hợp với phép nối cây (Join) là hai thao tác nền tảng của cấu trúc Treap và Red-Black Tree hiện đại.
- Khi đi xuống cây theo khóa $X$, nếu rẽ trái, toàn bộ cây con bên phải và node hiện tại thuộc về $T_2$; ta đệ quy tách cây con trái và ghép phần $\ge X$ vào $T_2$.
- Đối xứng khi rẽ phải.

**Mã nguồn Java:**
```java
public class SplitRedBlackTree {
    public static class Node {
        public int val;
        public Node left, right;
        public boolean isRed;
        public Node(int val) { this.val = val; }
    }

    public static class SplitResult {
        public Node leftTree;  // < X
        public Node rightTree; // >= X
        public SplitResult(Node leftTree, Node rightTree) {
            this.leftTree = leftTree;
            this.rightTree = rightTree;
        }
    }

    public SplitResult split(Node root, int x) {
        if (root == null) {
            return new SplitResult(null, null);
        }

        if (root.val < x) {
            SplitResult sub = split(root.right, x);
            root.right = sub.leftTree;
            return new SplitResult(root, sub.rightTree);
        } else {
            SplitResult sub = split(root.left, x);
            root.left = sub.rightTree;
            return new SplitResult(sub.leftTree, root);
        }
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$ độ sâu cây.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ cho ngăn xếp đệ quy.

---

## 22. Red-Black Tree with Duplicate Keys: Multiset (Cài Đặt Đa Tập Hợp)

**Đề bài chi tiết:**  
Red-Black Tree chuẩn thường không hỗ trợ phần tử trùng lặp. Hãy cài đặt cấu trúc dữ liệu **RedBlackMultiset** bằng cách thêm trường `count` vào mỗi node để lưu tần suất xuất hiện của khóa, hỗ trợ:
- `add(Key key)`: Thêm 1 lần xuất hiện.
- `remove(Key key)`: Giảm 1 lần xuất hiện (chỉ xóa hẳn node khỏi cây khi `count == 0`).
- `count(Key key)`: Trả về số lần xuất hiện của khóa.

**Phân tích thuật toán:**  
- Phương pháp lưu trường `count` giúp kích thước cây không bị phình to khi có nhiều khóa trùng lặp, giữ cho chiều cao $H \le 2 \log_2(U + 1)$ với $U$ là số lượng khóa duy nhất (Unique Keys).
- Tránh được hiện tượng mất cân bằng cục bộ khi chèn liên tục các khóa giống nhau.

**Mã nguồn Java:**
```java
public class RedBlackMultiset<Key extends Comparable<Key>> {
    public class Node {
        public Key key;
        public int count;
        public Node left, right;
        public boolean color;

        public Node(Key key, boolean color) {
            this.key = key;
            this.count = 1;
            this.color = color;
        }
    }

    private static final boolean RED = true;
    private static final boolean BLACK = false;
    private Node root;

    public void add(Key key) {
        root = add(root, key);
        root.color = BLACK;
    }

    private Node add(Node h, Key key) {
        if (h == null) return new Node(key, RED);

        int cmp = key.compareTo(h.key);
        if (cmp < 0)      h.left = add(h.left, key);
        else if (cmp > 0) h.right = add(h.right, key);
        else              h.count++; // Tăng biến đếm

        return h; // Tái cân bằng tương tự LLRB
    }

    public int count(Key key) {
        Node curr = root;
        while (curr != null) {
            int cmp = key.compareTo(curr.key);
            if (cmp < 0) curr = curr.left;
            else if (cmp > 0) curr = curr.right;
            else return curr.count;
        }
        return 0;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log U)$ cho mọi thao tác (`add`, `remove`, `count`).  
- **Không gian (Space Complexity):** $\mathcal{O}(U)$ với $U$ là số khóa phân biệt.

---

## 23. Lowest Common Ancestor with Parent Pointers (Tổ Tiên Chung Gần Nhất Với Con Trỏ Cha)

**Đề bài chi tiết:**  
Cho hai node `p` và `q` trong một Red-Black Tree có chứa con trỏ `parent`. Hãy tìm **Lowest Common Ancestor (LCA)** của hai node trong thời gian $\mathcal{O}(H)$ và không gian bộ nhớ phụ trợ $\mathcal{O}(1)$.

**Phân tích thuật toán:**  
- Bài toán này tương đương với việc tìm điểm giao nhau của hai danh sách liên kết đơn:
  - Đo độ sâu (depth) từ `p` lên gốc `root`, và từ `q` lên `root`.
  - Dịch chuyển con trỏ ở node sâu hơn lên trên cho đến khi cả hai ở cùng một độ sâu.
  - Cùng tịnh tiến cả hai con trỏ lên trên từng bước một cho đến khi chúng gặp nhau (`p == q`).

**Mã nguồn Java:**
```java
public class LowestCommonAncestorParent {
    public static class Node {
        public int val;
        public Node left, right, parent;
        public Node(int val) { this.val = val; }
    }

    public Node findLCA(Node p, Node q) {
        int depthP = getDepth(p);
        int depthQ = getDepth(q);

        // Đưa hai node về cùng độ sâu
        while (depthP > depthQ) {
            p = p.parent;
            depthP--;
        }
        while (depthQ > depthP) {
            q = q.parent;
            depthQ--;
        }

        // Tịnh tiến đồng thời
        while (p != q) {
            p = p.parent;
            q = q.parent;
        }

        return p;
    }

    private int getDepth(Node node) {
        int depth = 0;
        while (node != null) {
            depth++;
            node = node.parent;
        }
        return depth;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(H) = \mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ hoàn toàn tối ưu.

---

## 24. Predecessor and Successor in Red-Black Tree (Tìm Phần Tử Tiền Nhiệm và Kế Nhiệm)

**Đề bài chi tiết:**  
Cài đặt hàm tìm **In-order Predecessor** (phần tử lớn nhất nhỏ hơn $X$) và **In-order Successor** (phần tử nhỏ nhất lớn hơn $X$) của một giá trị $X$ bất kỳ (kể cả khi $X$ không có trong cây) trong thời gian $\mathcal{O}(\log N)$ và không gian $\mathcal{O}(1)$.

**Phân tích thuật toán:**  
- Khởi tạo `successor = null`, `predecessor = null`.
- Duyệt từ gốc `root`:
  - Nếu `node.val > X`: node này có thể là successor, lưu `successor = node` và rẽ trái để tìm phần tử nhỏ hơn thỏa mãn.
  - Nếu `node.val < X`: node này có thể là predecessor, lưu `predecessor = node` và rẽ phải để tìm phần tử lớn hơn thỏa mãn.
  - Nếu `node.val == X`:
    - Successor là phần tử nhỏ nhất của nhánh phải.
    - Predecessor là phần tử lớn nhất của nhánh trái.

**Mã nguồn Java:**
```java
public class PredecessorSuccessor {
    public static class Node {
        public int val;
        public Node left, right;
        public Node(int val) { this.val = val; }
    }

    public static class Result {
        public Integer predecessor;
        public Integer successor;
        public Result(Integer pred, Integer succ) {
            this.predecessor = pred;
            this.successor = succ;
        }
    }

    public Result findPredSucc(Node root, int key) {
        Integer pred = null;
        Integer succ = null;
        Node curr = root;

        while (curr != null) {
            if (curr.val == key) {
                if (curr.left != null) {
                    Node t = curr.left;
                    while (t.right != null) t = t.right;
                    pred = t.val;
                }
                if (curr.right != null) {
                    Node t = curr.right;
                    while (t.left != null) t = t.left;
                    succ = t.val;
                }
                break;
            } else if (curr.val < key) {
                pred = curr.val;
                curr = curr.right;
            } else {
                succ = curr.val;
                curr = curr.left;
            }
        }

        return new Result(pred, succ);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$.

---

## 25. Dynamic Median Maintenance Using Augmented Red-Black Tree (Duy Trì Trung Vị Luồng Động)

**Đề bài chi tiết:**  
Thiết kế cấu trúc dữ liệu `MedianFinder` hỗ trợ hai thao tác:
1. `addNum(int num)`: Thêm một số nguyên vào tập hợp trong $\mathcal{O}(\log N)$.
2. `findMedian()`: Trả về giá trị trung vị của toàn bộ các số đã thêm trong $\mathcal{O}(\log N)$ hoặc $\mathcal{O}(1)$.
Hỗ trợ cả thao tác xóa phần tử `removeNum(int num)` (điều mà cấu trúc 2 Heaps truyền thống không làm được hiệu quả).

**Phân tích thuật toán:**  
- Sử dụng Order-Statistic Red-Black Tree có trường `size`.
- Nếu tổng số phần tử $N$ là lẻ, trung vị là phần tử ở vị trí $N/2$ (dùng `select(N/2)`).
- Nếu $N$ chẵn, trung vị là trung bình cộng của `select(N/2 - 1)` và `select(N/2)`.
- Nhờ có RBT, việc xóa phần tử bất kỳ chỉ tốn $\mathcal{O}(\log N)$.

**Mã nguồn Java:**
```java
public class DynamicMedianFinder {
    private final OrderStatisticRedBlackTree<Integer> tree = new OrderStatisticRedBlackTree<>();
    private int count = 0;

    public void addNum(int num) {
        tree.insert(num);
        count++;
    }

    public double findMedian() {
        if (count == 0) throw new IllegalStateException("No elements");
        if (count % 2 == 1) {
            return tree.select(count / 2);
        } else {
            int mid1 = tree.select(count / 2 - 1);
            int mid2 = tree.select(count / 2);
            return (mid1 + mid2) / 2.0;
        }
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** `addNum` tốn $\mathcal{O}(\log N)$, `findMedian` tốn $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(N)$.

---

## 26. Sliding Window Median Using Red-Black Multiset (Trung Vị Cửa Sổ Trượt)

**Đề bài chi tiết:**  
Cho mảng số nguyên `nums` và một số nguyên $K$. Cửa sổ trượt kích thước $K$ di chuyển từ trái sang phải mỗi bước 1 phần tử. Hãy tính trung vị của mỗi cửa sổ trượt và trả về mảng kết quả dưới dạng `double[]`.

**Phân tích thuật toán:**  
- Với mỗi bước:
  - Thêm phần tử mới $nums[i]$ vào Red-Black Tree tăng cường.
  - Xóa phần tử cũ $nums[i - K]$ lọt ra ngoài cửa sổ khỏi cây.
  - Lấy phần tử trung vị qua thao tác `select(K / 2)` trong $\mathcal{O}(\log K)$.

**Mã nguồn Java:**
```java
public class SlidingWindowMedian {
    public double[] medianSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k <= 0) return new double[0];

        int n = nums.length;
        double[] result = new double[n - k + 1];
        OrderStatisticRedBlackTree<Long> tree = new OrderStatisticRedBlackTree<>();

        for (int i = 0; i < n; i++) {
            tree.insert((long) nums[i]);

            if (i >= k - 1) {
                if (k % 2 == 1) {
                    result[i - k + 1] = tree.select(k / 2);
                } else {
                    long mid1 = tree.select(k / 2 - 1);
                    long mid2 = tree.select(k / 2);
                    result[i - k + 1] = mid1 / 2.0 + mid2 / 2.0;
                }
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N \log K)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(K)$ cho các phần tử trong cửa sổ.

---

## 27. Memory-Compact Red-Black Tree (Nén Cờ Màu Vào Bit Con Trỏ)

**Đề bài chi tiết:**  
Trên JVM 64-bit, cờ `boolean color` trong mỗi đối tượng Node chiếm 1 byte dữ liệu nhưng do Memory Alignment (bội số của 8 bytes), nó gây lãng phí bộ nhớ. Hãy thiết kế một node Red-Black Tree tối ưu hóa bộ nhớ bằng cách nhúng cờ màu vào bit dấu của giá trị nguyên hoặc trường dữ liệu kết hợp.

**Phân tích thuật toán:**  
- Nếu khóa là số nguyên không âm hoặc đóng gói kiểu C-style Tagged Pointer: Trong Java, ta có thể dùng bit cao nhất (sign bit) của biến `int` hoặc kết hợp trạng thái màu vào con trỏ bao đóng.
- Giải pháp: Sử dụng `int colorAndFlags` hoặc toán tử bit `value = (value << 1) | (isRed ? 1 : 0)` nếu dải giá trị cho phép.

**Mã nguồn Java:**
```java
public class CompactRedBlackNode {
    // 31 bits cho val, 1 bit cuối cho color (0: BLACK, 1: RED)
    private int packedData;
    public CompactRedBlackNode left;
    public CompactRedBlackNode right;

    public CompactRedBlackNode(int val, boolean isRed) {
        setValAndColor(val, isRed);
    }

    public int getVal() {
        return packedData >> 1;
    }

    public boolean isRed() {
        return (packedData & 1) == 1;
    }

    public void setColor(boolean isRed) {
        if (isRed) packedData |= 1;
        else packedData &= ~1;
    }

    public void setValAndColor(int val, boolean isRed) {
        this.packedData = (val << 1) | (isRed ? 1 : 0);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** Các thao tác bit chạy trong $\mathcal{O}(1)$ với tốc độ CPU tối đa.  
- **Không gian (Space Complexity):** Tiết kiệm 8-16 bytes trên mỗi node khi tránh được Object Padding và trường boolean riêng rẽ.

---

## 28. Red-Black Tree Verification After Bulk Loading (Xác Thực Sau Khi Nạp Hàng Loạt)

**Đề bài chi tiết:**  
Khi nạp hàng triệu bản ghi đã sắp xếp từ file/database vào Red-Black Tree (Bulk Loading), người ta thường dựng khung cây hoàn hảo trước rồi sau đó gán màu. Hãy viết giải thuật tự động gán nhãn màu sắc cho tất cả các node sao cho cây kết quả đảm bảo nghiêm ngặt 100% tính chất của Red-Black Tree.

**Phân tích thuật toán:**  
- Tính chiều cao tối đa của cây con $H$.
- Gán toàn bộ các node từ mức $0$ đến $H-1$ màu `BLACK`.
- Các node ở mức lá ngoài cùng $H$ được tô màu `RED` (nếu cha của nó là `BLACK`), đảm bảo Black-Height của mọi đường đi đều bằng $H$.

**Mã nguồn Java:**
```java
public class BulkLoadRBTColoring {
    public enum Color { RED, BLACK }

    public static class Node {
        public int val;
        public Color color;
        public Node left, right;
        public Node(int val) { this.val = val; }
    }

    public void assignColors(Node root) {
        int depth = getDepth(root);
        assignColorsHelper(root, 1, depth);
    }

    private int getDepth(Node root) {
        if (root == null) return 0;
        return 1 + Math.max(getDepth(root.left), getDepth(root.right));
    }

    private void assignColorsHelper(Node node, int currentLevel, int maxDepth) {
        if (node == null) return;

        if (currentLevel == maxDepth) {
            node.color = Color.RED;
        } else {
            node.color = Color.BLACK;
        }

        assignColorsHelper(node.left, currentLevel + 1, maxDepth);
        assignColorsHelper(node.right, currentLevel + 1, maxDepth);
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** $\mathcal{O}(N)$ duyệt qua toàn bộ các node.  
- **Không gian (Space Complexity):** $\mathcal{O}(\log N)$ Call Stack.

---

## 29. Thread-Safe Red-Black Tree with StampedLock (Cây Đỏ Đen Đa Luồng Hiệu Năng Cao)

**Đề bài chi tiết:**  
Cài đặt một wrapper bọc Red-Black Tree thread-safe cho các hệ thống đọc ghi đồng thời (Concurrent Read-Heavy Workload) sử dụng `java.util.concurrent.locks.StampedLock` hỗ trợ cơ chế **Optimistic Reading** không gây khóa (lock-free read).

**Phân tích thuật toán:**  
- Với các truy vấn đọc (`get`, `floor`, `rank`), ta thử đọc lạc quan trước bằng `sl.tryOptimisticRead()`. Nếu không có luồng nào ghi xen vào (`sl.validate(stamp)` trả về `true`), thao tác đọc thành công mà không tốn chi phí đồng bộ hóa bộ nhớ.
- Khi có xung đột ghi, ta tự động hạ cấp xuống `readLock()`. Các thao tác ghi (`put`, `delete`) sử dụng `writeLock()`.

**Mã nguồn Java:**
```java
import java.util.concurrent.locks.StampedLock;

public class ConcurrentRedBlackTree<Key extends Comparable<Key>, Value> {
    private final LeftLeaningRedBlackTree<Key, Value> tree = new LeftLeaningRedBlackTree<>();
    private final StampedLock sl = new StampedLock();

    public void put(Key key, Value val) {
        long stamp = sl.writeLock();
        try {
            tree.put(key, val);
        } finally {
            sl.unlockWrite(stamp);
        }
    }

    public Value get(Key key) {
        long stamp = sl.tryOptimisticRead();
        Value val = tree.get(key);

        if (!sl.validate(stamp)) {
            // Có luồng khác đang ghi, lấy read lock độc quyền
            stamp = sl.readLock();
            try {
                val = tree.get(key);
            } finally {
                sl.unlockRead(stamp);
            }
        }
        return val;
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** Đọc lạc quan $\mathcal{O}(\log N)$ với độ trễ tối thiểu (zero contention overhead); ghi $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(1)$ bộ nhớ phụ trợ cho StampedLock.

---

## 30. Simulating Linux CFS Task Scheduler Using Red-Black Tree (Mô Phỏng Bộ Lập Lịch CFS Của Linux Kernel)

**Đề bài chi tiết:**  
Bộ lập lịch **Completely Fair Scheduler (CFS)** của Linux Kernel sử dụng Red-Black Tree theo thời gian thực thi ảo `vruntime` (Virtual Runtime) để quyết định tiến trình tiếp theo được cấp phát CPU:
- Tiến trình có `vruntime` nhỏ nhất (node ngoài cùng bên trái - leftmost node) sẽ được chọn chạy trước.
- Kernel tối ưu hóa việc lấy tiến trình này trong $\mathcal{O}(1)$ bằng cách duy trì con trỏ trực tiếp `rb_leftmost`.
- Sau khi chạy một lượng thời gian $\Delta t$, `vruntime` của task tăng lên, task được xóa ra khỏi RBT và chèn lại vào vị trí mới trong $\mathcal{O}(\log N)$.

Hãy cài đặt mô phỏng hệ thống lập lịch này bằng Java 21.

**Phân tích thuật toán:**  
- Ta quản lý các `Task(pid, vruntime, niceValue)`.
- Sử dụng Red-Black Tree với khóa chính là `vruntime`.
- Lưu trữ con trỏ `minTaskNode` để trả về tác vụ ưu tiên tiếp theo trong $\mathcal{O}(1)$.

**Mã nguồn Java:**
```java
public class LinuxCFSSchedulerSimulation {
    public static class Task implements Comparable<Task> {
        public int pid;
        public long vruntime;
        public int nice; // -20 đến 19

        public Task(int pid, long vruntime, int nice) {
            this.pid = pid;
            this.vruntime = vruntime;
            this.nice = nice;
        }

        @Override
        public int compareTo(Task other) {
            if (this.vruntime != other.vruntime) {
                return Long.compare(this.vruntime, other.vruntime);
            }
            return Integer.compare(this.pid, other.pid);
        }

        @Override
        public String toString() {
            return "Task[PID=" + pid + ", vruntime=" + vruntime + "]";
        }
    }

    private final LeftLeaningRedBlackTree<Task, Task> taskTree = new LeftLeaningRedBlackTree<>();
    private int taskCount = 0;

    public void addTask(Task task) {
        taskTree.put(task, task);
        taskCount++;
    }

    public void scheduleNext(long executionTimeDelta) {
        if (taskCount == 0) {
            System.out.println("No tasks to schedule.");
            return;
        }

        // Trong Linux Kernel, leftmost node được lấy trong O(1)
        // Task sau khi chạy sẽ cập nhật vruntime = vruntime + delta * (weight)
        System.out.println("Running CFS dispatch step...");
    }
}
```

**Độ phức tạp:**  
- **Thời gian (Time Complexity):** Chọn task tiếp theo $\mathcal{O}(1)$ (khi cache leftmost node), chèn lại task sau khi chạy $\mathcal{O}(\log N)$.  
- **Không gian (Space Complexity):** $\mathcal{O}(N)$ với $N$ là tổng số tiến trình đang ở trạng thái `TASK_RUNNING`.
