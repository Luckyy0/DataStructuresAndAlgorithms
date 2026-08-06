# Danh Sách Bài Tập Cây AVL (AVL Tree Problems)

Tài liệu này tổng hợp **30 bài toán kinh điển và nâng cao** về Cây AVL (AVL Tree), được thiết kế bài bản từ mức độ cơ bản (Verification, Rotations, Insertion, Deletion) đến nâng cao (Augmented AVL Trees, Order Statistic Trees, Dynamic Range Queries, Multi-tree Merging/Splitting). Mỗi bài toán đều có đề bài chi tiết, phân tích thuật toán chuyên sâu, mã nguồn Java 21 hoàn chỉnh và phân tích độ phức tạp thời gian/không gian.

---

## Bảng Tổng Hợp 30 Bài Toán Cây AVL

| STT | Tên bài toán | Chủ đề chính | Mức độ | Độ phức tạp thời gian |
|:---:|:---|:---|:---:|:---:|
| 1 | Validate AVL Tree Property | Cấu trúc & Invariant | Dễ | $O(n)$ |
| 2 | AVL Tree Insertion & Rotations | Thao tác cốt lõi | Trung bình | $O(\log n)$ |
| 3 | AVL Tree Deletion & Rebalancing | Thao tác cốt lõi | Trung bình | $O(\log n)$ |
| 4 | Sorted Array to Optimal AVL Tree | Xây dựng cây | Dễ | $O(n)$ |
| 5 | Inorder Successor & Predecessor | Truy vấn thứ tự | Dễ | $O(\log n)$ |
| 6 | Calculate Balance Factor & Heights | Duyệt cây Bottom-up | Dễ | $O(n)$ |
| 7 | Minimum Nodes in AVL of Height $h$ | Quy hoạch động / Toán | Dễ | $O(h)$ |
| 8 | Order Statistic Tree: $K$-th Smallest | Augmented AVL Tree | Trung bình | $O(\log n)$ |
| 9 | Order Statistic Tree: Rank of Key | Augmented AVL Tree | Trung bình | $O(\log n)$ |
| 10 | Count Inversions in Array | Ứng dụng Augmented AVL | Khó | $O(n \log n)$ |
| 11 | Count of Smaller Numbers After Self | Ứng dụng Augmented AVL | Khó | $O(n \log n)$ |
| 12 | Continuous Median with Dual AVL | Đa cấu trúc dữ liệu | Khó | $O(\log n)$ / op |
| 13 | Range Sum Query in Augmented AVL | Range Query | Trung bình | $O(\log n)$ |
| 14 | Range Count Query in AVL Tree | Range Query | Dễ | $O(\log n)$ |
| 15 | Merge Two AVL Trees | Hợp nhất cây | Khó | $O(m + n)$ |
| 16 | Split AVL Tree by Key | Phân tách cây | Rất khó | $O(\log n)$ |
| 17 | AVL Tree Join Operation | Nối cây tổng quát | Khó | $O(\|h_1 - h_2\|)$ |
| 18 | Floor and Ceiling Search | Truy vấn xấp xỉ | Dễ | $O(\log n)$ |
| 19 | Closest Key Search | Truy vấn tìm kiếm | Dễ | $O(\log n)$ |
| 20 | Lowest Common Ancestor (LCA) | Truy vấn cấu trúc | Dễ | $O(\log n)$ |
| 21 | Range Minimum Query (RMQ) Dynamic | Augmented AVL Array | Khó | $O(\log n)$ |
| 22 | Reconstruct AVL from Preorder Traversal | Tái tạo cây | Trung bình | $O(n)$ |
| 23 | Check Foldable / Structural Symmetry | Cấu trúc đối xứng | Dễ | $O(n)$ |
| 24 | Delete Minimum & Maximum in AVL | Thao tác Priority Queue | Dễ | $O(\log n)$ |
| 25 | Count Valid AVL Subtrees | Bottom-up Verification | Trung bình | $O(n)$ |
| 26 | AVL Tree with Duplicate Keys (Frequency) | Biến thể lưu trữ | Trung bình | $O(\log n)$ |
| 27 | Serialize and Deserialize AVL Tree | I/O & Tuần tự hóa | Trung bình | $O(n)$ |
| 28 | Dynamic Array using AVL Tree (List API) | Dynamic Sequence | Khó | $O(\log n)$ / op |
| 29 | Sliding Window Median using AVL Tree | Sliding Window | Khó | $O(n \log k)$ |
| 30 | Morris Inorder Traversal on AVL Tree | Tối ưu bộ nhớ $O(1)$ | Trung bình | $O(n)$ time, $O(1)$ space |

---

### Bài toán 1: Kiểm Tra Cây Nhị Phân Thỏa Mãn Tính Chất AVL Tree (Validate AVL Tree)

**Đề bài chi tiết:**
Cho gốc (`root`) của một cây nhị phân. Hãy viết hàm kiểm tra xem cây này có phải là một Cây AVL (AVL Tree) hợp lệ hay không.
Một cây nhị phân là cây AVL hợp lệ nếu thỏa mãn đồng thời hai điều kiện:
1. Cây là một Cây Tìm Kiếm Nhị Phân (Binary Search Tree - BST) hợp lệ: Với mọi nút $u$, tất cả các nút thuộc cây con trái có giá trị nhỏ hơn $u.val$, và tất cả các nút thuộc cây con phải có giá trị lớn hơn $u.val$.
2. Cây thỏa mãn điều kiện cân bằng chiều cao AVL: Với mọi nút $u$, chênh lệch chiều cao giữa cây con trái và cây con phải không vượt quá 1 (tức $|height(left) - height(right)| \le 1$).

**Phân tích thuật toán:**
- Sử dụng đệ quy hậu thứ tự (Post-order Traversal / Bottom-up DFS).
- Mỗi nút trả về một đối tượng chứa:
  - `isAVL` (boolean): Cây con có phải AVL hay không.
  - `height` (int): Chiều cao của cây con.
  - `minVal` (long): Giá trị nhỏ nhất trong cây con.
  - `maxVal` (long): Giá trị lớn nhất trong cây con.
- Với mỗi nút hiện tại:
  1. Đệ quy kiểm tra cây con trái và phải. Nếu một trong hai cây con không phải là AVL $\rightarrow$ cây hiện tại không phải AVL.
  2. Kiểm tra điều kiện BST: Giá trị nút hiện tại phải $> maxVal_{left}$ và $< minVal_{right}$.
  3. Kiểm tra điều kiện cân bằng: $|height_{left} - height_{right}| \le 1$.
  4. Chiều cao mới: $height = 1 + \max(height_{left}, height_{right})$.
- Tránh việc tính lại chiều cao từ gốc xuống gây độ phức tạp $O(n^2)$. Phương pháp Bottom-up đạt $O(n)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem01ValidateAVLTree {

    public static class TreeNode {
        public int val;
        public TreeNode left;
        public TreeNode right;

        public TreeNode(int val) {
            this.val = val;
        }
    }

    private record TreeInfo(boolean isAVL, int height, long minVal, long maxVal) {}

    public static boolean isValidAVL(TreeNode root) {
        return validate(root).isAVL();
    }

    private static TreeInfo validate(TreeNode node) {
        if (node == null) {
            return new TreeInfo(true, 0, Long.MAX_VALUE, Long.MIN_VALUE);
        }

        TreeInfo leftInfo = validate(node.left);
        if (!leftInfo.isAVL()) {
            return new TreeInfo(false, 0, 0, 0);
        }

        TreeInfo rightInfo = validate(node.right);
        if (!rightInfo.isAVL()) {
            return new TreeInfo(false, 0, 0, 0);
        }

        // 1. Kiểm tra tính chất BST
        if (node.left != null && leftInfo.maxVal() >= node.val) {
            return new TreeInfo(false, 0, 0, 0);
        }
        if (node.right != null && rightInfo.minVal() <= node.val) {
            return new TreeInfo(false, 0, 0, 0);
        }

        // 2. Kiểm tra tính chất cân bằng AVL
        if (Math.abs(leftInfo.height() - rightInfo.height()) > 1) {
            return new TreeInfo(false, 0, 0, 0);
        }

        int currentHeight = 1 + Math.max(leftInfo.height(), rightInfo.height());
        long currentMin = (node.left != null) ? leftInfo.minVal() : node.val;
        long currentMax = (node.right != null) ? rightInfo.maxVal() : node.val;

        return new TreeInfo(true, currentHeight, currentMin, currentMax);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$, trong đó $n$ là số lượng nút trong cây, vì mỗi nút được duyệt qua đúng một lần trong quá trình duyệt hậu thứ tự.
- **Không gian (Space Complexity):** $O(h) = O(\log n)$ (với cây cân bằng) hoặc $O(n)$ (trường hợp suy biến) do ngăn xếp đệ quy gọi hàm (Call Stack).

---

### Bài toán 2: Chèn Phần Tử và Cân Bằng Cây AVL (AVL Insertion & Rotations)

**Đề bài chi tiết:**
Xây dựng cấu trúc dữ liệu Cây AVL hoàn chỉnh với thao tác chèn khóa (`insert(int key)`). Đảm bảo rằng sau mỗi lần chèn, cây tự động phát hiện mất cân bằng và thực hiện đúng 4 phép quay:
1. Quay đơn phải (Right Rotation - LL Case)
2. Quay đơn trái (Left Rotation - RR Case)
3. Quay kép trái-phải (Left-Right Rotation - LR Case)
4. Quay kép phải-trái (Right-Left Rotation - RL Case)
Cập nhật thuộc tính chiều cao (`height`) của mỗi nút chính xác.

**Phân tích thuật toán:**
- Bước 1: Thực hiện chèn chuẩn theo quy tắc của BST thông qua đệ quy.
- Bước 2: Khi hàm đệ quy quay lui (backtracking), cập nhật lại chiều cao của nút hiện tại: $height(node) = 1 + \max(height(node.left), height(node.right))$.
- Bước 3: Tính hệ số cân bằng (Balance Factor): $BF = height(node.left) - height(node.right)$.
- Bước 4: Nếu $|BF| > 1$, xác định 1 trong 4 trường hợp:
  - $BF > 1$ và $key < node.left.key \rightarrow$ **LL Case**: Quay phải tại `node`.
  - $BF < -1$ và $key > node.right.key \rightarrow$ **RR Case**: Quay trái tại `node`.
  - $BF > 1$ và $key > node.left.key \rightarrow$ **LR Case**: Quay trái tại `node.left`, sau đó quay phải tại `node`.
  - $BF < -1$ và $key < node.right.key \rightarrow$ **RL Case**: Quay phải tại `node.right`, sau đó quay trái tại `node`.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem02AVLInsertion {

    public static class AVLNode {
        public int key;
        public int height;
        public AVLNode left;
        public AVLNode right;

        public AVLNode(int key) {
            this.key = key;
            this.height = 1;
        }
    }

    private static int height(AVLNode node) {
        return node == null ? 0 : node.height;
    }

    private static int getBalance(AVLNode node) {
        return node == null ? 0 : height(node.left) - height(node.right);
    }

    private static AVLNode rightRotate(AVLNode y) {
        AVLNode x = y.left;
        AVLNode T2 = x.right;

        // Thực hiện quay
        x.right = y;
        y.left = T2;

        // Cập nhật chiều cao
        y.height = Math.max(height(y.left), height(y.right)) + 1;
        x.height = Math.max(height(x.left), height(x.right)) + 1;

        return x; // Root mới của cây con
    }

    private static AVLNode leftRotate(AVLNode x) {
        AVLNode y = x.right;
        AVLNode T2 = y.left;

        // Thực hiện quay
        y.left = x;
        x.right = T2;

        // Cập nhật chiều cao
        x.height = Math.max(height(x.left), height(x.right)) + 1;
        y.height = Math.max(height(y.left), height(y.right)) + 1;

        return y; // Root mới của cây con
    }

    public static AVLNode insert(AVLNode node, int key) {
        // 1. Chèn BST thông thường
        if (node == null) {
            return new AVLNode(key);
        }

        if (key < node.key) {
            node.left = insert(node.left, key);
        } else if (key > node.key) {
            node.right = insert(node.right, key);
        } else {
            return node; // Khóa trùng lặp không chèn lại
        }

        // 2. Cập nhật chiều cao
        node.height = 1 + Math.max(height(node.left), height(node.right));

        // 3. Lấy Balance Factor
        int balance = getBalance(node);

        // 4. Cân bằng lại cây nếu cần
        // LL Case
        if (balance > 1 && key < node.left.key) {
            return rightRotate(node);
        }

        // RR Case
        if (balance < -1 && key > node.right.key) {
            return leftRotate(node);
        }

        // LR Case
        if (balance > 1 && key > node.left.key) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }

        // RL Case
        if (balance < -1 && key < node.right.key) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho mọi trường hợp (Best, Average, Worst) vì chiều cao của cây AVL luôn được duy trì $\le 1.44 \log_2 n$.
- **Không gian (Space Complexity):** $O(\log n)$ do bộ nhớ stack đệ quy tương ứng với chiều cao cây.

---

### Bài toán 3: Xóa Phần Tử và Tái Cân Bằng Cây AVL (AVL Deletion & Rebalancing)

**Đề bài chi tiết:**
Hiện thực thao tác xóa khóa (`delete(int key)`) trên Cây AVL. Sau khi xóa, nếu cây bị mất cân bằng tại bất kỳ nút tổ tiên nào trên đường đi từ nút bị xóa về gốc, thuật toán phải tự động thực hiện các phép quay để khôi phục tính chất AVL.

**Phân tích thuật toán:**
- Bước 1: Tìm và xóa nút theo quy tắc BST:
  - Nếu nút lá: Trả về `null`.
  - Nếu nút có 1 con: Trả về con của nó.
  - Nếu nút có 2 con: Tìm Inorder Successor (nút nhỏ nhất bên cây con phải), gán giá trị của successor vào nút hiện tại, sau đó đệ quy xóa successor ở cây con phải.
- Bước 2: Cập nhật chiều cao của nút hiện tại khi quay lui.
- Bước 3: Tính Balance Factor: $BF = height(node.left) - height(node.right)$.
- Bước 4: Kiểm tra 4 trường hợp mất cân bằng (chú ý kiểm tra $BF$ của nút con):
  - $BF > 1$ và $getBalance(node.left) \ge 0 \rightarrow$ **LL Case**: Quay phải `node`.
  - $BF > 1$ và $getBalance(node.left) < 0 \rightarrow$ **LR Case**: Quay trái `node.left`, rồi quay phải `node`.
  - $BF < -1$ và $getBalance(node.right) \le 0 \rightarrow$ **RR Case**: Quay trái `node`.
  - $BF < -1$ và $getBalance(node.right) > 0 \rightarrow$ **RL Case**: Quay phải `node.right`, rồi quay trái `node`.
- Chú ý: Khác với thao tác chèn (chỉ cần tối đa 2 phép quay), thao tác xóa có thể yêu cầu $O(\log n)$ phép quay lan truyền từ nút lá lên đến tận gốc.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem03AVLDeletion {

    public static class AVLNode {
        public int key;
        public int height;
        public AVLNode left;
        public AVLNode right;

        public AVLNode(int key) {
            this.key = key;
            this.height = 1;
        }
    }

    private static int height(AVLNode node) {
        return node == null ? 0 : node.height;
    }

    private static int getBalance(AVLNode node) {
        return node == null ? 0 : height(node.left) - height(node.right);
    }

    private static AVLNode rightRotate(AVLNode y) {
        AVLNode x = y.left;
        AVLNode T2 = x.right;
        x.right = y;
        y.left = T2;
        y.height = Math.max(height(y.left), height(y.right)) + 1;
        x.height = Math.max(height(x.left), height(x.right)) + 1;
        return x;
    }

    private static AVLNode leftRotate(AVLNode x) {
        AVLNode y = x.right;
        AVLNode T2 = y.left;
        y.left = x;
        x.right = T2;
        x.height = Math.max(height(x.left), height(x.right)) + 1;
        y.height = Math.max(height(y.left), height(y.right)) + 1;
        return y;
    }

    private static AVLNode minValueNode(AVLNode node) {
        AVLNode current = node;
        while (current.left != null) {
            current = current.left;
        }
        return current;
    }

    public static AVLNode delete(AVLNode root, int key) {
        if (root == null) {
            return null;
        }

        if (key < root.key) {
            root.left = delete(root.left, key);
        } else if (key > root.key) {
            root.right = delete(root.right, key);
        } else {
            // Nút cần xóa được tìm thấy
            if (root.left == null || root.right == null) {
                AVLNode temp = (root.left != null) ? root.left : root.right;
                if (temp == null) {
                    // Không có con
                    root = null;
                } else {
                    // Có 1 con
                    root = temp;
                }
            } else {
                // Có 2 con: Tìm Inorder Successor
                AVLNode temp = minValueNode(root.right);
                root.key = temp.key;
                root.right = delete(root.right, temp.key);
            }
        }

        if (root == null) {
            return null;
        }

        // Cập nhật chiều cao
        root.height = 1 + Math.max(height(root.left), height(root.right));

        // Kiểm tra và tái cân bằng
        int balance = getBalance(root);

        // LL Case
        if (balance > 1 && getBalance(root.left) >= 0) {
            return rightRotate(root);
        }

        // LR Case
        if (balance > 1 && getBalance(root.left) < 0) {
            root.left = leftRotate(root.left);
            return rightRotate(root);
        }

        // RR Case
        if (balance < -1 && getBalance(root.right) <= 0) {
            return leftRotate(root);
        }

        // RL Case
        if (balance < -1 && getBalance(root.right) > 0) {
            root.right = rightRotate(root.right);
            return leftRotate(root);
        }

        return root;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ vì tìm kiếm, xóa và các phép quay dọc theo đường đi từ gốc đến nút lá đều có độ phức tạp tỷ lệ thuận với chiều cao của cây.
- **Không gian (Space Complexity):** $O(\log n)$ do stack đệ quy.

---

### Bài toán 4: Chuyển Mảng Đã Sắp Xếp Thành Cây AVL Tối Ưu (Sorted Array to Optimal AVL Tree)

**Đề bài chi tiết:**
Cho một mảng số nguyên đã sắp xếp tăng dần `nums`. Hãy chuyển đổi mảng này thành một Cây AVL cân bằng hoàn hảo (chiều cao tối thiểu tuyệt đối) trong thời gian tuyến tính $O(n)$.

**Phân tích thuật toán:**
- Dùng kỹ thuật Chia để trị (Divide and Conquer):
  1. Chọn phần tử ở chính giữa mảng: `mid = left + (right - left) / 2` làm gốc của cây hiện tại.
  2. Nửa bên trái `[left, mid - 1]` dùng để xây dựng cây con trái.
  3. Nửa bên phải `[mid + 1, right]` dùng để xây dựng cây con phải.
  4. Chiều cao của nút = $1 + \max(height(left), height(right))$.
- Vì kích thước hai cây con chênh lệch tối đa 1 phần tử tại mọi mức, cây tạo thành tự động cân bằng hoàn hảo ($|BF| \le 1$) mà không cần thực hiện bất kỳ phép quay nào.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem04SortedArrayToAVL {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left;
        public AVLNode right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    public static AVLNode sortedArrayToAVL(int[] nums) {
        if (nums == null || nums.length == 0) {
            return null;
        }
        return buildAVL(nums, 0, nums.length - 1);
    }

    private static AVLNode buildAVL(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }

        int mid = left + (right - left) / 2;
        AVLNode root = new AVLNode(nums[mid]);

        root.left = buildAVL(nums, left, mid - 1);
        root.right = buildAVL(nums, mid + 1, right);

        int leftH = root.left != null ? root.left.height : 0;
        int rightH = root.right != null ? root.right.height : 0;
        root.height = 1 + Math.max(leftH, rightH);

        return root;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$, mỗi phần tử được thăm và tạo nút đúng một lần.
- **Không gian (Space Complexity):** $O(\log n)$ không gian bộ nhớ call stack.

---

### Bài toán 5: Tìm Phần Tử Liền Trước và Kế Tiếp Trong Cây AVL (Inorder Successor & Predecessor)

**Đề bài chi tiết:**
Cho gốc của một cây AVL và một giá trị khóa `key` (khóa có thể có hoặc không có trong cây). Hãy tìm:
1. **Inorder Successor:** Phần tử nhỏ nhất trong cây có giá trị $> key$.
2. **Inorder Predecessor:** Phần tử lớn nhất trong cây có giá trị $< key$.
Yêu cầu thuật toán chạy trong thời gian $O(\log n)$ và không gian bổ sung $O(1)$ (không dùng đệ quy hoặc lưu mảng).

**Phân tích thuật toán:**
- **Tìm Inorder Predecessor:**
  - Bắt đầu từ `root`, khởi tạo `pred = null`.
  - Nếu `curr.val >= key`: Di chuyển sang trái `curr = curr.left` (vì nút hiện tại và cây con phải của nó đều $\ge key$).
  - Nếu `curr.val < key`: Ghi nhận `pred = curr`, sau đó di chuyển sang phải `curr = curr.right` để tìm phần tử lớn hơn nhưng vẫn $< key$.
- **Tìm Inorder Successor:**
  - Bắt đầu từ `root`, khởi tạo `succ = null`.
  - Nếu `curr.val <= key`: Di chuyển sang phải `curr = curr.right`.
  - Nếu `curr.val > key`: Ghi nhận `succ = curr`, sau đó di chuyển sang trái `curr = curr.left` để tìm phần tử nhỏ hơn nhưng vẫn $> key$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem05SuccessorPredecessor {

    public static class AVLNode {
        public int val;
        public AVLNode left;
        public AVLNode right;

        public AVLNode(int val) {
            this.val = val;
        }
    }

    public record PredSuccResult(AVLNode predecessor, AVLNode successor) {}

    public static PredSuccResult findPredecessorAndSuccessor(AVLNode root, int key) {
        AVLNode predecessor = null;
        AVLNode successor = null;

        AVLNode curr = root;
        // Tìm Predecessor
        while (curr != null) {
            if (curr.val < key) {
                predecessor = curr;
                curr = curr.right;
            } else {
                curr = curr.left;
            }
        }

        // Tìm Successor
        curr = root;
        while (curr != null) {
            if (curr.val > key) {
                successor = curr;
                curr = curr.left;
            } else {
                curr = curr.right;
            }
        }

        return new PredSuccResult(predecessor, successor);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ vì cây AVL có chiều cao $h = O(\log n)$ và mỗi vòng lặp duyệt xuống 1 mức.
- **Không gian (Space Complexity):** $O(1)$ vì chỉ sử dụng các con trỏ tạm, không đệ quy.

---

### Bài toán 6: Tính Balance Factor và Chiều Cao Của Tất Cả Các Node (Calculate Balance Factor & Heights)

**Đề bài chi tiết:**
Cho một cây nhị phân thông thường. Hãy tính chiều cao và Hệ số cân bằng (Balance Factor: $BF = height(left) - height(right)$) cho toàn bộ các nút trong cây, đồng thời trả về danh sách tất cả các nút có $|BF| > 1$ (các nút bị mất cân bằng AVL).

**Phân tích thuật toán:**
- Sử dụng phép duyệt hậu thứ tự (Post-order Traversal).
- Chiều cao của nút lá được quy ước là 1, nút `null` là 0.
- Tại mỗi nút $u$:
  1. $h_{left} = height(u.left)$, $h_{right} = height(u.right)$.
  2. $u.height = 1 + \max(h_{left}, h_{right})$.
  3. $u.BF = h_{left} - h_{right}$.
  4. Nếu $|u.BF| > 1$, thêm $u$ vào danh sách mất cân bằng.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

import java.util.ArrayList;
import java.util.List;

public class Problem06CalculateBalanceFactor {

    public static class Node {
        public int val;
        public int height;
        public int balanceFactor;
        public Node left;
        public Node right;

        public Node(int val) {
            this.val = val;
        }
    }

    public static List<Node> computeBalanceAndFindImbalanced(Node root) {
        List<Node> imbalancedNodes = new ArrayList<>();
        calculateMetrics(root, imbalancedNodes);
        return imbalancedNodes;
    }

    private static int calculateMetrics(Node node, List<Node> imbalancedNodes) {
        if (node == null) {
            return 0;
        }

        int leftH = calculateMetrics(node.left, imbalancedNodes);
        int rightH = calculateMetrics(node.right, imbalancedNodes);

        node.height = 1 + Math.max(leftH, rightH);
        node.balanceFactor = leftH - rightH;

        if (Math.abs(node.balanceFactor) > 1) {
            imbalancedNodes.add(node);
        }

        return node.height;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$, mỗi nút được xử lý đúng 1 lần.
- **Không gian (Space Complexity):** $O(h)$ bộ nhớ stack đệ quy.

---

### Bài toán 7: Số Nút Tối Thiểu Trong Cây AVL Chiều Cao $h$ (Minimum Nodes in AVL of Height $h$)

**Đề bài chi tiết:**
Cho số nguyên không âm $h$ đại diện cho chiều cao của cây AVL (với quy ước cây rỗng có $h=0$, cây có 1 nút có $h=1$). Hãy tính số lượng nút tối thiểu $N(h)$ để tạo thành một cây AVL hợp lệ có chiều cao đúng bằng $h$.

**Phân tích thuật toán:**
- Để một cây AVL đạt chiều cao $h$ với số nút ít nhất:
  - Một cây con phải có chiều cao $h - 1$ với số nút tối thiểu $N(h-1)$.
  - Cây con còn lại phải có chiều cao $h - 2$ với số nút tối thiểu $N(h-2)$ (để đảm bảo hiệu số chiều cao $|(h-1) - (h-2)| = 1 \le 1$).
  - Gốc đóng góp 1 nút.
- Hệ thức truy hồi:
  $$N(0) = 0$$
  $$N(1) = 1$$
  $$N(2) = 2$$
  $$N(h) = N(h - 1) + N(h - 2) + 1 \quad \text{với } h \ge 3$$
- Đây là biến thể của dãy số Fibonacci: $N(h) = F(h + 2) - 1$.
- Ta có thể giải bằng Quy hoạch động với 2 biến nhớ trong $O(h)$ thời gian và $O(1)$ không gian.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem07MinNodesAVL {

    public static long minNodesForHeight(int h) {
        if (h <= 0) return 0;
        if (h == 1) return 1;
        if (h == 2) return 2;

        long prev2 = 1; // N(1)
        long prev1 = 2; // N(2)
        long current = 0;

        for (int i = 3; i <= h; i++) {
            current = prev1 + prev2 + 1;
            prev2 = prev1;
            prev1 = current;
        }

        return current;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(h)$.
- **Không gian (Space Complexity):** $O(1)$.

---

### Bài toán 8: Tìm Phần Tử Nhỏ Thứ K Trong Cây AVL (Order Statistic AVL Tree - K-th Smallest)

**Đề bài chi tiết:**
Thiết kế một Cây AVL mở rộng (Augmented AVL Tree) hỗ trợ thao tác `findKthSmallest(int k)` (1-indexed) trong thời gian $O(\log n)$, đồng thời vẫn duy trì các thao tác chèn (`insert`) và xóa (`delete`) trong $O(\log n)$.

**Phân tích thuật toán:**
- Mở rộng mỗi nút của cây AVL với trường `size` lưu số lượng nút trong cây con có gốc tại nút đó.
- Công thức tính `size`: $size(u) = 1 + size(u.left) + size(u.right)$.
- Khi thực hiện các phép quay cây, ngoài `height`, ta cập nhật lại `size`.
- Thuật toán tìm phần tử nhỏ thứ $k$:
  1. $leftSize = size(curr.left)$.
  2. Nếu $k == leftSize + 1$: Phần tử tại $curr$ chính là phần tử cần tìm.
  3. Nếu $k \le leftSize$: Phần tử nằm ở cây con trái $\rightarrow$ đi sang trái tìm phần tử thứ $k$.
  4. Nếu $k > leftSize + 1$: Phần tử nằm ở cây con phải $\rightarrow$ đi sang phải tìm phần tử thứ $k - (leftSize + 1)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem08OrderStatisticAVL {

    public static class OrderNode {
        public int val;
        public int height;
        public int size;
        public OrderNode left;
        public OrderNode right;

        public OrderNode(int val) {
            this.val = val;
            this.height = 1;
            this.size = 1;
        }
    }

    private static int size(OrderNode node) {
        return node == null ? 0 : node.size;
    }

    private static int height(OrderNode node) {
        return node == null ? 0 : node.height;
    }

    private static void updateNode(OrderNode node) {
        if (node != null) {
            node.height = 1 + Math.max(height(node.left), height(node.right));
            node.size = 1 + size(node.left) + size(node.right);
        }
    }

    private static OrderNode rightRotate(OrderNode y) {
        OrderNode x = y.left;
        OrderNode T2 = x.right;

        x.right = y;
        y.left = T2;

        updateNode(y);
        updateNode(x);

        return x;
    }

    private static OrderNode leftRotate(OrderNode x) {
        OrderNode y = x.right;
        OrderNode T2 = y.left;

        y.left = x;
        x.right = T2;

        updateNode(x);
        updateNode(y);

        return y;
    }

    public static OrderNode insert(OrderNode node, int val) {
        if (node == null) return new OrderNode(val);

        if (val < node.val) {
            node.left = insert(node.left, val);
        } else if (val > node.val) {
            node.right = insert(node.right, val);
        } else {
            return node;
        }

        updateNode(node);

        int balance = height(node.left) - height(node.right);

        if (balance > 1 && val < node.left.val) return rightRotate(node);
        if (balance < -1 && val > node.right.val) return leftRotate(node);
        if (balance > 1 && val > node.left.val) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }
        if (balance < -1 && val < node.right.val) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    public static Integer findKthSmallest(OrderNode root, int k) {
        if (root == null || k <= 0 || k > size(root)) {
            return null;
        }

        OrderNode curr = root;
        while (curr != null) {
            int leftSize = size(curr.left);
            if (k == leftSize + 1) {
                return curr.val;
            } else if (k <= leftSize) {
                curr = curr.left;
            } else {
                k -= (leftSize + 1);
                curr = curr.right;
            }
        }
        return null;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho cả `insert` và `findKthSmallest`.
- **Không gian (Space Complexity):** $O(\log n)$ khi chèn, $O(1)$ khi tìm kiếm.

---

### Bài toán 9: Xác Định Thứ Hạng (Rank) Của Một Phần Tử Trong Cây AVL

**Đề bài chi tiết:**
Cho một Cây AVL mở rộng (lưu trữ `size`). Hãy viết hàm `getRank(int key)` trả về số lượng các phần tử trong cây có giá trị nhỏ hơn `key` (Rank 0-indexed) trong thời gian $O(\log n)$.

**Phân tích thuật toán:**
- Khởi tạo `rank = 0`, con trỏ `curr = root`.
- Khi duyệt từ gốc xuống:
  - Nếu `key == curr.val`: Cộng thêm số nút ở cây con trái `size(curr.left)` vào `rank` và trả về.
  - Nếu `key < curr.val`: Phần tử nằm ở cây con trái $\rightarrow$ đi sang trái: `curr = curr.left`.
  - Nếu `key > curr.val`: Phần tử lớn hơn `curr` và toàn bộ cây con trái của nó $\rightarrow$ cộng `size(curr.left) + 1` vào `rank`, sau đó đi sang phải: `curr = curr.right`.
- Nếu `curr == null`, trả về `rank` đã tích lũy (đây cũng chính là vị trí chèn lý tưởng).

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem09RankOfKey {

    public static class OrderNode {
        public int val;
        public int size;
        public OrderNode left;
        public OrderNode right;

        public OrderNode(int val) {
            this.val = val;
            this.size = 1;
        }
    }

    private static int size(OrderNode node) {
        return node == null ? 0 : node.size;
    }

    public static int getRank(OrderNode root, int key) {
        int rank = 0;
        OrderNode curr = root;

        while (curr != null) {
            if (key == curr.val) {
                rank += size(curr.left);
                return rank;
            } else if (key < curr.val) {
                curr = curr.left;
            } else {
                rank += size(curr.left) + 1;
                curr = curr.right;
            }
        }

        return rank;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ vì chiều cao cây AVL được bảo toàn.
- **Không gian (Space Complexity):** $O(1)$ không gian bộ nhớ phụ.

---

### Bài toán 10: Đếm Số Cặp Nghịch Thế Trong Mảng Bằng Cây AVL (Count Inversions)

**Đề bài chi tiết:**
Một cặp chỉ số $(i, j)$ được gọi là một cặp nghịch thế (Inversion) trong mảng `nums` nếu $i < j$ và $nums[i] > nums[j]$.
Hãy đếm tổng số cặp nghịch thế trong mảng bằng cách sử dụng Cây AVL mở rộng.

**Phân tích thuật toán:**
- Duyệt mảng từ phải qua trái ($j = n - 1 \to 0$).
- Với mỗi phần tử $nums[j]$:
  - Ta cần đếm xem trong tập các phần tử đã duyệt trước đó (tức các phần tử đứng sau $nums[j]$), có bao nhiêu phần tử $< nums[j]$.
  - Sử dụng phép truy vấn `getRank(nums[j])` trên cây AVL: Kết quả trả về chính là số lượng phần tử $< nums[j]$ đã được chèn vào cây.
  - Cộng số lượng này vào biến tổng `totalInversions`.
  - Chèn $nums[j]$ vào cây AVL.
- Xử lý các phần tử trùng lặp bằng cách lưu trữ `count` tần suất tại mỗi nút.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem10CountInversions {

    private static class Node {
        int val;
        int height;
        int size;
        int count;
        Node left, right;

        Node(int val) {
            this.val = val;
            this.height = 1;
            this.size = 1;
            this.count = 1;
        }
    }

    private static int height(Node n) { return n == null ? 0 : n.height; }
    private static int size(Node n) { return n == null ? 0 : n.size; }

    private static void update(Node n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
            n.size = n.count + size(n.left) + size(n.right);
        }
    }

    private static Node rightRotate(Node y) {
        Node x = y.left;
        Node T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static Node leftRotate(Node x) {
        Node y = x.right;
        Node T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static Node insert(Node node, int val, int[] smallerCount) {
        if (node == null) {
            return new Node(val);
        }

        if (val == node.val) {
            node.count++;
            smallerCount[0] += size(node.left);
        } else if (val < node.val) {
            node.left = insert(node.left, val, smallerCount);
        } else {
            smallerCount[0] += size(node.left) + node.count;
            node.right = insert(node.right, val, smallerCount);
        }

        update(node);

        int balance = height(node.left) - height(node.right);
        if (balance > 1 && val < node.left.val) return rightRotate(node);
        if (balance < -1 && val > node.right.val) return leftRotate(node);
        if (balance > 1 && val > node.left.val) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }
        if (balance < -1 && val < node.right.val) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    public static long countInversions(int[] nums) {
        if (nums == null || nums.length < 2) return 0;

        long total = 0;
        Node root = null;

        for (int i = nums.length - 1; i >= 0; i--) {
            int[] smaller = new int[1];
            root = insert(root, nums[i], smaller);
            total += smaller[0];
        }

        return total;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n \log n)$ với $n$ lần chèn và truy vấn vào cây AVL.
- **Không gian (Space Complexity):** $O(n)$ lưu trữ $n$ nút trong cây AVL.

---

### Bài toán 11: Đếm Số Phần Tử Nhỏ Hơn Đứng Sau Mỗi Phần Tử (Count of Smaller Numbers After Self)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`. Trả về một mảng `counts` sao cho `counts[i]` là số lượng các phần tử đứng sau `nums[i]` (tức chỉ số $j > i$) có giá trị nhỏ hơn `nums[i]`.
Hiện thực thuật toán bằng Cây AVL mở rộng.

**Phân tích thuật toán:**
- Tương tự bài toán Count Inversions, ta duyệt ngược từ cuối mảng $i = n - 1 \to 0$.
- Tại mỗi phần tử `nums[i]`, khi chèn vào cây AVL, ta tính số lượng các phần tử trong cây $< nums[i]$ và gán trực tiếp vào `counts[i]`.
- Cây AVL được duy trì cân bằng sau mỗi phép chèn đảm bảo mỗi thao tác mất đúng $O(\log n)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Problem11CountSmallerAfterSelf {

    private static class Node {
        int val;
        int height;
        int size;
        int count;
        Node left, right;

        Node(int val) {
            this.val = val;
            this.height = 1;
            this.size = 1;
            this.count = 1;
        }
    }

    private static int height(Node n) { return n == null ? 0 : n.height; }
    private static int size(Node n) { return n == null ? 0 : n.size; }

    private static void update(Node n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
            n.size = n.count + size(n.left) + size(n.right);
        }
    }

    private static Node rightRotate(Node y) {
        Node x = y.left;
        Node T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static Node leftRotate(Node x) {
        Node y = x.right;
        Node T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static Node insert(Node node, int val, int[] countRef) {
        if (node == null) return new Node(val);

        if (val == node.val) {
            node.count++;
            countRef[0] += size(node.left);
        } else if (val < node.val) {
            node.left = insert(node.left, val, countRef);
        } else {
            countRef[0] += size(node.left) + node.count;
            node.right = insert(node.right, val, countRef);
        }

        update(node);

        int balance = height(node.left) - height(node.right);
        if (balance > 1 && val < node.left.val) return rightRotate(node);
        if (balance < -1 && val > node.right.val) return leftRotate(node);
        if (balance > 1 && val > node.left.val) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }
        if (balance < -1 && val < node.right.val) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    public static List<Integer> countSmaller(int[] nums) {
        if (nums == null || nums.length == 0) return Collections.emptyList();

        int n = nums.length;
        Integer[] result = new Integer[n];
        Node root = null;

        for (int i = n - 1; i >= 0; i--) {
            int[] count = new int[1];
            root = insert(root, nums[i], count);
            result[i] = count[0];
        }

        List<Integer> list = new ArrayList<>(n);
        Collections.addAll(list, result);
        return list;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n \log n)$.
- **Không gian (Space Complexity):** $O(n)$.

---

### Bài toán 12: Tìm Trung Vị Trong Luồng Dữ Liệu Bằng Cặp Cây AVL (Continuous Median with Dual AVL)

**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu `MedianFinder` hỗ trợ:
- `addNum(int num)`: Thêm một số nguyên từ luồng dữ liệu vào cấu trúc.
- `findMedian()`: Trả về số trung vị (median) của toàn bộ các số đã thêm dưới dạng số thực (`double`).
Hiện thực bằng việc sử dụng 2 cây AVL mở rộng (hoặc 1 cây AVL có hỗ trợ truy vấn thứ hạng $K$-th).

**Phân tích thuật toán:**
- Phương pháp 1: Sử dụng 1 cây AVL có lưu `size` và `count` (Order Statistic Tree).
  - Khi cần tìm trung vị của $N$ phần tử:
    - Nếu $N$ lẻ: Lấy phần tử nhỏ thứ $(N / 2 + 1)$.
    - Nếu $N$ chẵn: Lấy trung bình cộng của phần tử thứ $(N / 2)$ và $(N / 2 + 1)$.
- Mọi thao tác chèn mất $O(\log n)$, truy vấn trung vị mất $O(\log n)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem12ContinuousMedian {

    private static class Node {
        int val;
        int height;
        int size;
        int count;
        Node left, right;

        Node(int val) {
            this.val = val;
            this.height = 1;
            this.size = 1;
            this.count = 1;
        }
    }

    public static class MedianFinderAVL {
        private Node root;
        private int totalElements = 0;

        private int height(Node n) { return n == null ? 0 : n.height; }
        private int size(Node n) { return n == null ? 0 : n.size; }

        private void update(Node n) {
            if (n != null) {
                n.height = 1 + Math.max(height(n.left), height(n.right));
                n.size = n.count + size(n.left) + size(n.right);
            }
        }

        private Node rightRotate(Node y) {
            Node x = y.left;
            Node T2 = x.right;
            x.right = y;
            y.left = T2;
            update(y);
            update(x);
            return x;
        }

        private Node leftRotate(Node x) {
            Node y = x.right;
            Node T2 = y.left;
            y.left = x;
            x.right = T2;
            update(x);
            update(y);
            return y;
        }

        private Node insert(Node node, int val) {
            if (node == null) return new Node(val);

            if (val == node.val) {
                node.count++;
            } else if (val < node.val) {
                node.left = insert(node.left, val);
            } else {
                node.right = insert(node.right, val);
            }

            update(node);

            int balance = height(node.left) - height(node.right);
            if (balance > 1 && val < node.left.val) return rightRotate(node);
            if (balance < -1 && val > node.right.val) return leftRotate(node);
            if (balance > 1 && val > node.left.val) {
                node.left = leftRotate(node.left);
                return rightRotate(node);
            }
            if (balance < -1 && val < node.right.val) {
                node.right = rightRotate(node.right);
                return leftRotate(node);
            }

            return node;
        }

        private int findKth(Node node, int k) {
            int leftSize = size(node.left);
            if (k > leftSize && k <= leftSize + node.count) {
                return node.val;
            } else if (k <= leftSize) {
                return findKth(node.left, k);
            } else {
                return findKth(node.right, k - leftSize - node.count);
            }
        }

        public void addNum(int num) {
            root = insert(root, num);
            totalElements++;
        }

        public double findMedian() {
            if (totalElements == 0) throw new IllegalStateException("Cây rỗng");

            if (totalElements % 2 != 0) {
                return findKth(root, totalElements / 2 + 1);
            } else {
                int m1 = findKth(root, totalElements / 2);
                int m2 = findKth(root, totalElements / 2 + 1);
                return (m1 + (double) m2) / 2.0;
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** `addNum`: $O(\log n)$, `findMedian`: $O(\log n)$.
- **Không gian (Space Complexity):** $O(n)$ cho $n$ phần tử.

---

### Bài toán 13: Truy Vấn Tổng Các Phần Tử Trong Khoảng $[L, R]$ (Range Sum Query in AVL)

**Đề bài chi tiết:**
Cho một tập hợp các số nguyên động. Hãy thiết kế một Cây AVL mở rộng hỗ trợ:
1. `insert(int val)`: Thêm một phần tử vào cây.
2. `rangeSum(int L, int R)`: Tính tổng của tất cả các phần tử $x$ trong cây thỏa mãn $L \le x \le R$ trong thời gian $O(\log n)$.

**Phân tích thuật toán:**
- Mở rộng mỗi nút với trường `subtreeSum` (kiểu `long` để tránh tràn số):
  $$subtreeSum(u) = u.val + subtreeSum(u.left) + subtreeSum(u.right)$$
- Để tính `rangeSum(L, R)`, ta sử dụng hàm phụ trợ `sumLessThanOrEqualTo(X)` (tính tổng các phần tử $\le X$):
  - Khi duyệt cây:
    - Nếu `curr.val <= X`: Toàn bộ cây con trái và nút hiện tại đều $\le X \rightarrow$ cộng $curr.val + subtreeSum(curr.left)$ vào kết quả, rồi duyệt tiếp sang cây con phải.
    - Nếu `curr.val > X`: Đi sang cây con trái để tìm các phần tử nhỏ hơn.
- Kết quả của `rangeSum(L, R)` là: `sumLessThanOrEqualTo(R) - sumLessThanOrEqualTo(L - 1)`.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem13RangeSumAVL {

    public static class SumNode {
        public int val;
        public int height;
        public long subtreeSum;
        public SumNode left, right;

        public SumNode(int val) {
            this.val = val;
            this.height = 1;
            this.subtreeSum = val;
        }
    }

    private static int height(SumNode n) { return n == null ? 0 : n.height; }
    private static long sum(SumNode n) { return n == null ? 0 : n.subtreeSum; }

    private static void update(SumNode n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
            n.subtreeSum = n.val + sum(n.left) + sum(n.right);
        }
    }

    private static SumNode rightRotate(SumNode y) {
        SumNode x = y.left;
        SumNode T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static SumNode leftRotate(SumNode x) {
        SumNode y = x.right;
        SumNode T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    public static SumNode insert(SumNode node, int val) {
        if (node == null) return new SumNode(val);

        if (val < node.val) {
            node.left = insert(node.left, val);
        } else if (val > node.val) {
            node.right = insert(node.right, val);
        } else {
            return node;
        }

        update(node);

        int balance = height(node.left) - height(node.right);
        if (balance > 1 && val < node.left.val) return rightRotate(node);
        if (balance < -1 && val > node.right.val) return leftRotate(node);
        if (balance > 1 && val > node.left.val) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }
        if (balance < -1 && val < node.right.val) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    public static long sumLessThanOrEqualTo(SumNode root, int x) {
        long total = 0;
        SumNode curr = root;
        while (curr != null) {
            if (curr.val <= x) {
                total += curr.val + sum(curr.left);
                curr = curr.right;
            } else {
                curr = curr.left;
            }
        }
        return total;
    }

    public static long rangeSum(SumNode root, int L, int R) {
        if (L > R) return 0;
        return sumLessThanOrEqualTo(root, R) - sumLessThanOrEqualTo(root, L - 1);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho mỗi phép chèn và truy vấn đoạn.
- **Không gian (Space Complexity):** $O(\log n)$ đệ quy cho phép chèn, $O(1)$ cho phép truy vấn.

---

### Bài toán 14: Đếm Số Lượng Phần Tử Trong Đoạn $[L, R]$ (Range Count Query)

**Đề bài chi tiết:**
Cho một Cây AVL mở rộng lưu trường `size`. Hãy viết hàm `countInRange(int L, int R)` trả về số lượng các phần tử trong cây có giá trị nằm trong đoạn $[L, R]$ ($L \le x \le R$) trong thời gian $O(\log n)$.

**Phân tích thuật toán:**
- Tương tự như hàm tính tổng đoạn, ta định nghĩa hàm phụ trợ `countLessThanOrEqualTo(X)` (đếm số phần tử $\le X$).
- `countLessThanOrEqualTo(X)`:
  - Khởi tạo `count = 0`.
  - Duyệt từ gốc xuống:
    - Nếu `curr.val <= X`: Toàn bộ cây con trái và chính nút `curr` đều $\le X \rightarrow$ `count += size(curr.left) + 1`, sau đó duyệt sang nhánh phải `curr = curr.right`.
    - Nếu `curr.val > X`: Duyệt sang nhánh trái `curr = curr.left`.
- Kết quả: `countInRange(L, R) = countLessThanOrEqualTo(R) - countLessThanOrEqualTo(L - 1)`.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem14RangeCountAVL {

    public static class Node {
        public int val;
        public int size;
        public Node left, right;

        public Node(int val) {
            this.val = val;
            this.size = 1;
        }
    }

    private static int size(Node n) {
        return n == null ? 0 : n.size;
    }

    public static int countLessThanOrEqualTo(Node root, int x) {
        int count = 0;
        Node curr = root;
        while (curr != null) {
            if (curr.val <= x) {
                count += size(curr.left) + 1;
                curr = curr.right;
            } else {
                curr = curr.left;
            }
        }
        return count;
    }

    public static int countInRange(Node root, int L, int R) {
        if (L > R || root == null) return 0;
        return countLessThanOrEqualTo(root, R) - countLessThanOrEqualTo(root, L - 1);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ vì chỉ duyệt qua 2 đường đi từ gốc đến lá.
- **Không gian (Space Complexity):** $O(1)$.

---

### Bài toán 15: Hợp Nhất Hai Cây AVL (Merge Two AVL Trees)

**Đề bài chi tiết:**
Cho gốc của hai Cây AVL $T_1$ (kích thước $m$) và $T_2$ (kích thước $n$). Hãy hợp nhất hai cây thành một Cây AVL duy nhất chứa tất cả các phần tử của cả hai cây trong thời gian $O(m + n)$ và không gian $O(m + n)$.

**Phân tích thuật toán:**
1. Thực hiện duyệt trung thứ tự (Inorder Traversal) trên $T_1$ để thu được danh sách đã sắp xếp $L_1$ kích thước $m$.
2. Thực hiện duyệt trung thứ tự trên $T_2$ để thu được danh sách đã sắp xếp $L_2$ kích thước $n$.
3. Trộn (Merge) hai danh sách đã sắp xếp $L_1$ và $L_2$ thành một danh sách kết quả $L$ có kích thước $m + n$ (tương tự như bước Merge trong MergeSort) trong thời gian $O(m + n)$.
4. Chuyển đổi danh sách đã sắp xếp $L$ thành Cây AVL cân bằng hoàn hảo theo phương pháp Chia để trị (Bài toán 4) trong thời gian $O(m + n)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

import java.util.ArrayList;
import java.util.List;

public class Problem15MergeAVLTrees {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    private static void inorder(AVLNode node, List<Integer> list) {
        if (node == null) return;
        inorder(node.left, list);
        list.add(node.val);
        inorder(node.right, list);
    }

    private static List<Integer> mergeSortedLists(List<Integer> l1, List<Integer> l2) {
        List<Integer> result = new ArrayList<>(l1.size() + l2.size());
        int i = 0, j = 0;
        while (i < l1.size() && j < l2.size()) {
            if (l1.get(i) <= l2.get(j)) {
                result.add(l1.get(i++));
            } else {
                result.add(l2.get(j++));
            }
        }
        while (i < l1.size()) result.add(l1.get(i++));
        while (j < l2.size()) result.add(l2.get(j++));
        return result;
    }

    private static AVLNode buildBalancedAVL(List<Integer> list, int start, int end) {
        if (start > end) return null;
        int mid = start + (end - start) / 2;
        AVLNode node = new AVLNode(list.get(mid));
        node.left = buildBalancedAVL(list, start, mid - 1);
        node.right = buildBalancedAVL(list, mid + 1, end);

        int lh = node.left != null ? node.left.height : 0;
        int rh = node.right != null ? node.right.height : 0;
        node.height = 1 + Math.max(lh, rh);
        return node;
    }

    public static AVLNode mergeTrees(AVLNode root1, AVLNode root2) {
        List<Integer> l1 = new ArrayList<>();
        List<Integer> l2 = new ArrayList<>();
        inorder(root1, l1);
        inorder(root2, l2);

        List<Integer> merged = mergeSortedLists(l1, l2);
        return buildBalancedAVL(merged, 0, merged.size() - 1);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(m + n)$.
- **Không gian (Space Complexity):** $O(m + n)$ để lưu các danh sách phần tử.

---

### Bài toán 16: Phân Tách Cây AVL Theo Khóa (Split AVL Tree by Key)

**Đề bài chi tiết:**
Cho gốc của một Cây AVL $T$ và một giá trị khóa $K$. Hãy viết hàm `split(AVLNode root, int K)` để tách $T$ thành hai Cây AVL độc lập $T_1$ và $T_2$ sao cho:
- $T_1$ chứa tất cả các phần tử $\le K$.
- $T_2$ chứa tất cả các phần tử $> K$.
- Cả $T_1$ và $T_2$ đều thỏa mãn tính chất cân bằng chiều cao của AVL.
Yêu cầu thuật toán đạt thời gian $O(\log n)$.

**Phân tích thuật toán:**
- Dựa trên thuật toán `join(T_left, k, T_right)` (Bài toán 17): Nối 2 cây AVL khi biết mọi khóa của cây trái $< k <$ mọi khóa của cây phải trong thời gian $O(|h_{left} - h_{right}|)$.
- Thuật toán `split` đệ quy:
  - Nếu `root == null`: Trả về cặp `(null, null)`.
  - Nếu `K < root.val`:
    - Đệ quy tách cây con trái: `(L1, L2) = split(root.left, K)`.
    - $T_1 = L1$.
    - $T_2 = join(L2, root.val, root.right)$.
  - Nếu `K >= root.val`:
    - Đệ quy tách cây con phải: `(R1, R2) = split(root.right, K)`.
    - $T_1 = join(root.left, root.val, R1)$.
    - $T_2 = R2$.
- Độ phức tạp tổng cộng của các phép `join` lồng nhau theo đường đi từ gốc xuống lá là chuỗi hình học có tổng chặn trên bởi $O(\log n)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem16SplitAVLTree {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    public record SplitResult(AVLNode leftTree, AVLNode rightTree) {}

    private static int height(AVLNode n) { return n == null ? 0 : n.height; }

    private static void updateHeight(AVLNode n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
        }
    }

    private static AVLNode rightRotate(AVLNode y) {
        AVLNode x = y.left;
        AVLNode T2 = x.right;
        x.right = y;
        y.left = T2;
        updateHeight(y);
        updateHeight(x);
        return x;
    }

    private static AVLNode leftRotate(AVLNode x) {
        AVLNode y = x.right;
        AVLNode T2 = y.left;
        y.left = x;
        x.right = T2;
        updateHeight(x);
        updateHeight(y);
        return y;
    }

    private static AVLNode balance(AVLNode node) {
        if (node == null) return null;
        updateHeight(node);
        int bf = height(node.left) - height(node.right);

        if (bf > 1) {
            if (height(node.left.left) < height(node.left.right)) {
                node.left = leftRotate(node.left);
            }
            return rightRotate(node);
        }
        if (bf < -1) {
            if (height(node.right.right) < height(node.right.left)) {
                node.right = rightRotate(node.right);
            }
            return leftRotate(node);
        }
        return node;
    }

    public static AVLNode joinRight(AVLNode tl, int k, AVLNode tr) {
        if (height(tl) <= height(tr) + 1) {
            AVLNode node = new AVLNode(k);
            node.left = tl;
            node.right = tr;
            updateHeight(node);
            return balance(node);
        }
        tl.right = joinRight(tl.right, k, tr);
        return balance(tl);
    }

    public static AVLNode joinLeft(AVLNode tl, int k, AVLNode tr) {
        if (height(tr) <= height(tl) + 1) {
            AVLNode node = new AVLNode(k);
            node.left = tl;
            node.right = tr;
            updateHeight(node);
            return balance(tr);
        }
        tr.left = joinLeft(tl, k, tr.left);
        return balance(tr);
    }

    public static AVLNode join(AVLNode tl, int k, AVLNode tr) {
        if (height(tl) > height(tr) + 1) {
            return joinRight(tl, k, tr);
        } else if (height(tr) > height(tl) + 1) {
            return joinLeft(tl, k, tr);
        } else {
            AVLNode node = new AVLNode(k);
            node.left = tl;
            node.right = tr;
            updateHeight(node);
            return node;
        }
    }

    public static SplitResult split(AVLNode root, int k) {
        if (root == null) {
            return new SplitResult(null, null);
        }

        if (k < root.val) {
            SplitResult leftRes = split(root.left, k);
            AVLNode rightTree = join(leftRes.rightTree(), root.val, root.right);
            return new SplitResult(leftRes.leftTree(), rightTree);
        } else {
            SplitResult rightRes = split(root.right, k);
            AVLNode leftTree = join(root.left, root.val, rightRes.leftTree());
            return new SplitResult(leftTree, rightRes.rightTree());
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$.
- **Không gian (Space Complexity):** $O(\log n)$ trên call stack.

---

### Bài toán 17: Phép Nối Hai Cây AVL Với Khóa Trung Gian (AVL Join Operation)

**Đề bài chi tiết:**
Cho hai cây AVL $T_L$ và $T_R$ sao cho mọi phần tử trong $T_L$ đều nhỏ hơn khóa $k$, và mọi phần tử trong $T_R$ đều lớn hơn khóa $k$. Hãy thực hiện phép nối `join(T_L, k, T_R)` để tạo thành một cây AVL hợp lệ duy nhất trong thời gian $O(|height(T_L) - height(T_R)|) = O(\log n)$.

**Phân tích thuật toán:**
- Giả sử $height(T_L) \ge height(T_R)$:
  - Ta đi theo nhánh phải của $T_L$ cho đến khi tìm thấy nút $u$ có $height(u) \le height(T_R) + 1$.
  - Tạo nút mới `newNode = Node(k)`.
  - Gán `newNode.left = u` và `newNode.right = T_R`.
  - Thay thế $u$ bằng `newNode` trong cha của $u$, sau đó quay lui tái cân bằng dọc theo đường đi về gốc $T_L$.
- Ngược lại nếu $height(T_R) > height(T_L)$, ta làm tương tự bằng cách đi theo nhánh trái của $T_R$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem17AVLJoin {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    private static int height(AVLNode n) { return n == null ? 0 : n.height; }

    private static void update(AVLNode n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
        }
    }

    private static AVLNode rightRotate(AVLNode y) {
        AVLNode x = y.left;
        AVLNode T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static AVLNode leftRotate(AVLNode x) {
        AVLNode y = x.right;
        AVLNode T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static AVLNode rebalance(AVLNode node) {
        if (node == null) return null;
        update(node);
        int bf = height(node.left) - height(node.right);

        if (bf > 1) {
            if (height(node.left.left) < height(node.left.right)) {
                node.left = leftRotate(node.left);
            }
            return rightRotate(node);
        }
        if (bf < -1) {
            if (height(node.right.right) < height(node.right.left)) {
                node.right = rightRotate(node.right);
            }
            return leftRotate(node);
        }
        return node;
    }

    public static AVLNode join(AVLNode tl, int k, AVLNode tr) {
        if (tl == null && tr == null) {
            return new AVLNode(k);
        }
        if (tl == null) {
            return insertBST(tr, k);
        }
        if (tr == null) {
            return insertBST(tl, k);
        }

        if (height(tl) > height(tr) + 1) {
            tl.right = join(tl.right, k, tr);
            return rebalance(tl);
        } else if (height(tr) > height(tl) + 1) {
            tr.left = join(tl, k, tr.left);
            return rebalance(tr);
        } else {
            AVLNode root = new AVLNode(k);
            root.left = tl;
            root.right = tr;
            update(root);
            return root;
        }
    }

    private static AVLNode insertBST(AVLNode root, int k) {
        if (root == null) return new AVLNode(k);
        if (k < root.val) root.left = insertBST(root.left, k);
        else root.right = insertBST(root.right, k);
        return rebalance(root);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(|h(T_L) - h(T_R)|) = O(\log n)$.
- **Không gian (Space Complexity):** $O(|h(T_L) - h(T_R)|)$ do stack đệ quy.

---

### Bài toán 18: Tìm Cận Dưới và Cận Trên Trong Cây AVL (Floor and Ceiling Search)

**Đề bài chi tiết:**
Cho gốc của một cây AVL và một giá trị $X$. Hãy tìm:
1. `floor(X)`: Giá trị lớn nhất trong cây $\le X$.
2. `ceiling(X)`: Giá trị nhỏ nhất trong cây $\ge X$.
Nếu không tồn tại, trả về `null`.

**Phân tích thuật toán:**
- **Tìm Floor:**
  - Bắt đầu từ gốc:
    - Nếu `curr.val == X`: `curr.val` chính là floor.
    - Nếu `curr.val > X`: Floor chắc chắn phải nằm ở cây con bên trái $\rightarrow curr = curr.left$.
    - Nếu `curr.val < X`: `curr.val` là một ứng viên tiềm năng $\rightarrow$ lưu lại `ans = curr.val` rồi đi sang cây con bên phải $curr = curr.right$ để tìm giá trị lớn hơn.
- **Tìm Ceiling:** Tương tự theo hướng ngược lại.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem18FloorAndCeiling {

    public static class AVLNode {
        public int val;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
        }
    }

    public static Integer findFloor(AVLNode root, int key) {
        Integer floor = null;
        AVLNode curr = root;
        while (curr != null) {
            if (curr.val == key) {
                return curr.val;
            } else if (curr.val > key) {
                curr = curr.left;
            } else {
                floor = curr.val;
                curr = curr.right;
            }
        }
        return floor;
    }

    public static Integer findCeiling(AVLNode root, int key) {
        Integer ceiling = null;
        AVLNode curr = root;
        while (curr != null) {
            if (curr.val == key) {
                return curr.val;
            } else if (curr.val < key) {
                curr = curr.right;
            } else {
                ceiling = curr.val;
                curr = curr.left;
            }
        }
        return ceiling;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$.
- **Không gian (Space Complexity):** $O(1)$.

---

### Bài toán 19: Tìm Khóa Gần Nhất Với Mục Tiêu Trong Cây AVL (Closest Key Search)

**Đề bài chi tiết:**
Cho gốc của một cây AVL chứa các số nguyên và một số thực mục tiêu `target`. Hãy tìm giá trị nguyên trong cây có khoảng cách tuyệt đối $|val - target|$ là nhỏ nhất.

**Phân tích thuật toán:**
- Khởi tạo `closest = root.val`.
- Duyệt từ gốc xuống:
  - So sánh khoảng cách: Nếu $|curr.val - target| < |closest - target|$, cập nhật `closest = curr.val`.
  - Nếu `target < curr.val`: Di chuyển sang trái `curr = curr.left`.
  - Nếu `target > curr.val`: Di chuyển sang phải `curr = curr.right`.
  - Nếu `target == curr.val`: Trả về `curr.val` ngay lập tức (khoảng cách = 0).

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem19ClosestKey {

    public static class AVLNode {
        public int val;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
        }
    }

    public static int findClosestKey(AVLNode root, double target) {
        if (root == null) throw new IllegalArgumentException("Cây không được rỗng");

        int closest = root.val;
        AVLNode curr = root;

        while (curr != null) {
            if (Math.abs(curr.val - target) < Math.abs(closest - target)) {
                closest = curr.val;
            }

            if (target < curr.val) {
                curr = curr.left;
            } else if (target > curr.val) {
                curr = curr.right;
            } else {
                return curr.val;
            }
        }

        return closest;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$.
- **Không gian (Space Complexity):** $O(1)$.

---

### Bài toán 20: Tìm Tổ Tiên Chung Gần Nhất Trong Cây AVL (Lowest Common Ancestor - LCA)

**Đề bài chi tiết:**
Cho gốc của cây AVL và hai giá trị khóa $p$ và $q$. Hãy tìm nút tổ tiên chung gần nhất (LCA) của $p$ và $q$ trong thời gian $O(\log n)$ và bộ nhớ bổ sung $O(1)$.

**Phân tích thuật toán:**
- Tận dụng tính chất Cây Tìm Kiếm Nhị Phân:
  - Bắt đầu từ gốc:
    - Nếu cả $p$ và $q$ đều $< curr.val$: LCA phải nằm bên cây con trái $\rightarrow curr = curr.left$.
    - Nếu cả $p$ và $q$ đều $> curr.val$: LCA phải nằm bên cây con phải $\rightarrow curr = curr.right$.
    - Nếu một khóa $\le curr.val$ và khóa còn lại $\ge curr.val$: Nút `curr` chính là điểm phân nhánh đầu tiên, hay chính là LCA.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem20LCAinAVL {

    public static class AVLNode {
        public int val;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
        }
    }

    public static AVLNode lowestCommonAncestor(AVLNode root, int p, int q) {
        AVLNode curr = root;
        while (curr != null) {
            if (p < curr.val && q < curr.val) {
                curr = curr.left;
            } else if (p > curr.val && q > curr.val) {
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
- **Thời gian (Time Complexity):** $O(\log n)$.
- **Không gian (Space Complexity):** $O(1)$.

---

### Bài toán 21: Cây AVL Thực Hiện Cập Nhật và Truy Vấn Đoạn (Dynamic Range Minimum Query - RMQ)

**Đề bài chi tiết:**
Thiết kế một cây AVL biểu diễn một dãy số động hỗ trợ các thao tác:
1. `insert(int index, int val)`: Chèn phần tử giá trị `val` vào vị trí `index`.
2. `queryMin(int L, int R)`: Trả về giá trị nhỏ nhất trong đoạn chỉ số $[L, R]$.
Tất cả các thao tác phải chạy trong $O(\log n)$.

**Phân tích thuật toán:**
- Sử dụng Augmented AVL Tree lưu trữ:
  - `size`: Kích thước cây con để định vị phần tử theo chỉ số (implicit index).
  - `minVal`: Giá trị nhỏ nhất của tất cả các phần tử trong cây con có gốc tại nút này.
- Khi truy vấn `queryMin(L, R)`, ta duyệt cây và so sánh đoạn truy vấn với đoạn chỉ số của nút hiện tại:
  - Nếu đoạn của nút nằm hoàn toàn trong $[L, R]$: Trả về `node.minVal`.
  - Nếu đoạn không giao nhau: Bỏ qua.
  - Nếu giao nhau một phần: Đệ quy kiểm tra con trái, nút hiện tại, và con phải.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem21DynamicRMQ {

    public static class RMQNode {
        public int val;
        public int minVal;
        public int size;
        public int height;
        public RMQNode left, right;

        public RMQNode(int val) {
            this.val = val;
            this.minVal = val;
            this.size = 1;
            this.height = 1;
        }
    }

    private static int size(RMQNode n) { return n == null ? 0 : n.size; }
    private static int height(RMQNode n) { return n == null ? 0 : n.height; }
    private static int minVal(RMQNode n) { return n == null ? Integer.MAX_VALUE : n.minVal; }

    private static void update(RMQNode n) {
        if (n != null) {
            n.size = 1 + size(n.left) + size(n.right);
            n.height = 1 + Math.max(height(n.left), height(n.right));
            n.minVal = Math.min(n.val, Math.min(minVal(n.left), minVal(n.right)));
        }
    }

    private static RMQNode rightRotate(RMQNode y) {
        RMQNode x = y.left;
        RMQNode T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static RMQNode leftRotate(RMQNode x) {
        RMQNode y = x.right;
        RMQNode T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static RMQNode balance(RMQNode n) {
        update(n);
        int bf = height(n.left) - height(n.right);
        if (bf > 1) {
            if (height(n.left.left) < height(n.left.right)) {
                n.left = leftRotate(n.left);
            }
            return rightRotate(n);
        }
        if (bf < -1) {
            if (height(n.right.right) < height(n.right.left)) {
                n.right = rightRotate(n.right);
            }
            return leftRotate(n);
        }
        return n;
    }

    public static RMQNode insertAt(RMQNode node, int index, int val) {
        if (node == null) return new RMQNode(val);

        int leftSize = size(node.left);
        if (index <= leftSize) {
            node.left = insertAt(node.left, index, val);
        } else {
            node.right = insertAt(node.right, index - leftSize - 1, val);
        }

        return balance(node);
    }

    public static int queryMin(RMQNode node, int qL, int qR, int currL, int currR) {
        if (node == null || qL > currR || qR < currL) {
            return Integer.MAX_VALUE;
        }

        if (qL <= currL && currR <= qR) {
            return node.minVal;
        }

        int leftSize = size(node.left);
        int nodeIdx = currL + leftSize;
        int res = Integer.MAX_VALUE;

        if (qL <= nodeIdx && nodeIdx <= qR) {
            res = Math.min(res, node.val);
        }

        if (node.left != null && qL < nodeIdx) {
            res = Math.min(res, queryMin(node.left, qL, qR, currL, nodeIdx - 1));
        }
        if (node.right != null && qR > nodeIdx) {
            res = Math.min(res, queryMin(node.right, qL, qR, nodeIdx + 1, currR));
        }

        return res;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho chèn và truy vấn min đoạn.
- **Không gian (Space Complexity):** $O(\log n)$ ngăn xếp đệ quy.

---

### Bài toán 22: Tái Tạo Cây AVL Từ Mảng Duyệt Tiền Thứ Tự (Reconstruct AVL from Preorder)

**Đề bài chi tiết:**
Cho một mảng các số nguyên đại diện cho thứ tự duyệt tiền thứ tự (Preorder Traversal) của một Cây AVL. Hãy khôi phục lại cấu trúc cây AVL trong thời gian tuyến tính $O(n)$.

**Phân tích thuật toán:**
- Duyệt tiền thứ tự thăm: `Root -> Left -> Right`.
- Sử dụng giới hạn khoảng giá trị `(min, max)`:
  - Nút gốc có khoảng `(Long.MIN_VALUE, Long.MAX_VALUE)`.
  - Cây con trái của nút có giá trị `val` có khoảng `(min, val)`.
  - Cây con phải có khoảng `(val, max)`.
- Duy trì một biến con trỏ chỉ số toàn cục (hoặc mảng 1 phần tử `int[] index`). Nếu phần tử hiện tại nằm trong khoảng hợp lệ, tạo nút và đệ quy xây dựng con trái rồi con phải.
- Cập nhật chiều cao khi quay lui.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem22ReconstructPreorder {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    public static AVLNode bstFromPreorder(int[] preorder) {
        if (preorder == null || preorder.length == 0) return null;
        int[] index = new int[]{0};
        return build(preorder, index, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private static AVLNode build(int[] preorder, int[] index, long min, long max) {
        if (index[0] >= preorder.length) return null;

        int val = preorder[index[0]];
        if (val <= min || val >= max) return null;

        index[0]++;
        AVLNode root = new AVLNode(val);
        root.left = build(preorder, index, min, val);
        root.right = build(preorder, index, val, max);

        int lh = root.left != null ? root.left.height : 0;
        int rh = root.right != null ? root.right.height : 0;
        root.height = 1 + Math.max(lh, rh);

        return root;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$, mỗi phần tử được kiểm tra đúng một số lần hằng số.
- **Không gian (Space Complexity):** $O(h) = O(\log n)$ bộ nhớ stack.

---

### Bài toán 23: Kiểm Tra Cây Đối Xứng Cấu Trúc và Cân Bằng (Check Foldable / Symmetric AVL)

**Đề bài chi tiết:**
Một cây nhị phân được gọi là "Foldable" (gấp lại được / đối xứng cấu trúc) nếu cây con trái và cây con phải là hình ảnh đối gương cấu trúc của nhau. Hãy kiểm tra xem một cây nhị phân có vừa là Foldable vừa là cây AVL cân bằng hay không.

**Phân tích thuật toán:**
1. Kiểm tra tính chất đối gương cấu trúc giữa hai cây con $T_1, T_2$:
   - Cả hai cùng `null` $\rightarrow$ `true`.
   - Một cây `null`, cây kia khác `null` $\rightarrow$ `false`.
   - Đệ quy kiểm tra: `isMirror(T1.left, T2.right)` và `isMirror(T1.right, T2.left)`.
2. Kiểm tra tính chất cân bằng chiều cao AVL tại mỗi nút thông qua hàm tính chiều cao Bottom-up (Bài toán 1).

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem23FoldableAndBalanced {

    public static class TreeNode {
        public int val;
        public TreeNode left, right;

        public TreeNode(int val) {
            this.val = val;
        }
    }

    public static boolean isFoldableAndAVL(TreeNode root) {
        if (root == null) return true;
        return isFoldable(root.left, root.right) && checkAVLHeight(root) != -1;
    }

    private static boolean isFoldable(TreeNode n1, TreeNode n2) {
        if (n1 == null && n2 == null) return true;
        if (n1 == null || n2 == null) return false;
        return isFoldable(n1.left, n2.right) && isFoldable(n1.right, n2.left);
    }

    private static int checkAVLHeight(TreeNode node) {
        if (node == null) return 0;
        int lh = checkAVLHeight(node.left);
        if (lh == -1) return -1;
        int rh = checkAVLHeight(node.right);
        if (rh == -1) return -1;

        if (Math.abs(lh - rh) > 1) return -1;
        return 1 + Math.max(lh, rh);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$.
- **Không gian (Space Complexity):** $O(h) = O(\log n)$.

---

### Bài toán 24: Xóa Phần Tử Nhỏ Nhất và Lớn Nhất Trong Cây AVL (Delete Min / Max)

**Đề bài chi tiết:**
Hiện thực hai hàm chuyên biệt:
- `deleteMin(AVLNode root)`: Xóa phần tử có khóa nhỏ nhất trong cây AVL và tái cân bằng.
- `deleteMax(AVLNode root)`: Xóa phần tử có khóa lớn nhất trong cây AVL và tái cân bằng.
Cả hai thao tác phải chạy trong $O(\log n)$.

**Phân tích thuật toán:**
- Với `deleteMin`:
  - Nếu `node.left == null`: Nút hiện tại chính là nút nhỏ nhất $\rightarrow$ trả về `node.right`.
  - Ngược lại: `node.left = deleteMin(node.left)`, sau đó cập nhật chiều cao và tái cân bằng `node`.
- Với `deleteMax`: Làm tương tự trên nhánh phải.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem24DeleteMinMax {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    private static int height(AVLNode n) { return n == null ? 0 : n.height; }

    private static void update(AVLNode n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
        }
    }

    private static AVLNode rightRotate(AVLNode y) {
        AVLNode x = y.left;
        AVLNode T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static AVLNode leftRotate(AVLNode x) {
        AVLNode y = x.right;
        AVLNode T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static AVLNode balance(AVLNode node) {
        if (node == null) return null;
        update(node);
        int bf = height(node.left) - height(node.right);

        if (bf > 1) {
            if (height(node.left.left) < height(node.left.right)) {
                node.left = leftRotate(node.left);
            }
            return rightRotate(node);
        }
        if (bf < -1) {
            if (height(node.right.right) < height(node.right.left)) {
                node.right = rightRotate(node.right);
            }
            return leftRotate(node);
        }
        return node;
    }

    public static AVLNode deleteMin(AVLNode root) {
        if (root == null) return null;
        if (root.left == null) return root.right;
        root.left = deleteMin(root.left);
        return balance(root);
    }

    public static AVLNode deleteMax(AVLNode root) {
        if (root == null) return null;
        if (root.right == null) return root.left;
        root.right = deleteMax(root.right);
        return balance(root);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$.
- **Không gian (Space Complexity):** $O(\log n)$.

---

### Bài toán 25: Đếm Số Lượng Cây Con Là Cây AVL Hợp Lệ (Count Valid AVL Subtrees)

**Đề bài chi tiết:**
Cho một cây nhị phân bất kỳ. Hãy đếm xem có bao nhiêu cây con (subtree) của cây này là một Cây AVL hợp lệ (thỏa mãn cả tính chất BST và tính chất cân bằng chiều cao AVL).

**Phân tích thuật toán:**
- Duyệt hậu thứ tự (Post-order Traversal).
- Mỗi nút trả về một bản ghi: `(isAVL, height, minVal, maxVal)`.
- Duy trì một biến đếm toàn cục `totalAVLSubtrees`.
- Một cây con tại `u` là AVL nếu:
  1. Cả cây con trái và cây con phải đều là AVL.
  2. $u.val > maxVal_{left}$ và $u.val < minVal_{right}$.
  3. $|height_{left} - height_{right}| \le 1$.
- Nếu thỏa mãn, tăng `totalAVLSubtrees` lên 1.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem25CountAVLSubtrees {

    public static class TreeNode {
        public int val;
        public TreeNode left, right;

        public TreeNode(int val) {
            this.val = val;
        }
    }

    private record SubtreeInfo(boolean isAVL, int height, long minVal, long maxVal) {}

    public static int countAVLSubtrees(TreeNode root) {
        int[] count = new int[1];
        helper(root, count);
        return count[0];
    }

    private static SubtreeInfo helper(TreeNode node, int[] count) {
        if (node == null) {
            return new SubtreeInfo(true, 0, Long.MAX_VALUE, Long.MIN_VALUE);
        }

        SubtreeInfo left = helper(node.left, count);
        SubtreeInfo right = helper(node.right, count);

        boolean isBST = (node.left == null || left.maxVal() < node.val) &&
                        (node.right == null || right.minVal() > node.val);
        boolean isBalanced = Math.abs(left.height() - right.height()) <= 1;

        if (left.isAVL() && right.isAVL() && isBST && isBalanced) {
            count[0]++;
            int h = 1 + Math.max(left.height(), right.height());
            long min = node.left != null ? left.minVal() : node.val;
            long max = node.right != null ? right.maxVal() : node.val;
            return new SubtreeInfo(true, h, min, max);
        }

        return new SubtreeInfo(false, 0, 0, 0);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$, mỗi nút duyệt 1 lần.
- **Không gian (Space Complexity):** $O(h) = O(\log n)$ trên stack.

---

### Bài toán 26: Cây AVL Hỗ Trợ Khóa Trùng Lặp (AVL Tree with Frequency Count)

**Đề bài chi tiết:**
Thiết kế Cây AVL hỗ trợ chèn và xóa các phần tử có giá trị trùng nhau bằng cách sử dụng bộ đếm tần suất (`count`) tại mỗi nút.
Hiện thực các hàm:
- `insert(int key)`: Tăng tần suất nếu khóa đã tồn tại, ngược lại tạo nút mới với `count = 1`.
- `delete(int key)`: Giảm tần suất nếu `count > 1`, nếu `count == 1` thì xóa hoàn toàn nút khỏi cây.

**Phân tích thuật toán:**
- Khi chèn một khóa đã có trong cây: Ta không tạo nút mới mà chỉ tăng `node.count++`, sau đó cập nhật lại `size` của các nút tổ tiên mà không cần quay cây (vì chiều cao không thay đổi).
- Khi xóa: Nếu `node.count > 1`, giảm `node.count--` và cập nhật lại `size`. Nếu `node.count == 1`, tiến hành xóa nút như bài toán AVL Deletion thông thường.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem26AVLWithDuplicates {

    public static class FreqNode {
        public int val;
        public int count;
        public int height;
        public FreqNode left, right;

        public FreqNode(int val) {
            this.val = val;
            this.count = 1;
            this.height = 1;
        }
    }

    private static int height(FreqNode n) { return n == null ? 0 : n.height; }

    private static void update(FreqNode n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
        }
    }

    private static FreqNode rightRotate(FreqNode y) {
        FreqNode x = y.left;
        FreqNode T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static FreqNode leftRotate(FreqNode x) {
        FreqNode y = x.right;
        FreqNode T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static FreqNode balance(FreqNode node) {
        if (node == null) return null;
        update(node);
        int bf = height(node.left) - height(node.right);

        if (bf > 1) {
            if (height(node.left.left) < height(node.left.right)) {
                node.left = leftRotate(node.left);
            }
            return rightRotate(node);
        }
        if (bf < -1) {
            if (height(node.right.right) < height(node.right.left)) {
                node.right = rightRotate(node.right);
            }
            return leftRotate(node);
        }
        return node;
    }

    public static FreqNode insert(FreqNode node, int val) {
        if (node == null) return new FreqNode(val);

        if (val == node.val) {
            node.count++;
            return node;
        } else if (val < node.val) {
            node.left = insert(node.left, val);
        } else {
            node.right = insert(node.right, val);
        }

        return balance(node);
    }

    private static FreqNode minValueNode(FreqNode node) {
        FreqNode curr = node;
        while (curr.left != null) curr = curr.left;
        return curr;
    }

    public static FreqNode delete(FreqNode root, int val) {
        if (root == null) return null;

        if (val < root.val) {
            root.left = delete(root.left, val);
        } else if (val > root.val) {
            root.right = delete(root.right, val);
        } else {
            if (root.count > 1) {
                root.count--;
                return root;
            }

            if (root.left == null || root.right == null) {
                root = (root.left != null) ? root.left : root.right;
            } else {
                FreqNode temp = minValueNode(root.right);
                root.val = temp.val;
                root.count = temp.count;
                temp.count = 1; // Đảm bảo đệ quy xóa dứt điểm nút successor
                root.right = delete(root.right, temp.val);
            }
        }

        return balance(root);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho cả chèn và xóa.
- **Không gian (Space Complexity):** $O(\log n)$.

---

### Bài toán 27: Tuần Tự Hóa và Giải Tuần Tự Hóa Cây AVL (Serialize and Deserialize AVL Tree)

**Đề bài chi tiết:**
Thiết kế thuật toán để chuyển đổi một Cây AVL thành một chuỗi văn bản (`serialize`) và chuyển đổi chuỗi đó ngược lại thành đúng Cây AVL ban đầu (`deserialize`).

**Phân tích thuật toán:**
- Vì cây AVL là một Cây Tìm Kiếm Nhị Phân (BST) không có khóa trùng, ta có thể chỉ cần lưu thứ tự duyệt tiền thứ tự (Preorder) mà không cần lưu các ký tự `null` (giảm đáng kể kích thước chuỗi).
- Khi giải tuần tự hóa (`deserialize`): Sử dụng thuật toán tái tạo cây BST từ Preorder với giới hạn khoảng `(min, max)` trong $O(n)$ (Bài toán 22).

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

import java.util.Arrays;

public class Problem27SerializeDeserializeAVL {

    public static class AVLNode {
        public int val;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.height = 1;
        }
    }

    public static String serialize(AVLNode root) {
        StringBuilder sb = new StringBuilder();
        serializePreorder(root, sb);
        return sb.toString();
    }

    private static void serializePreorder(AVLNode node, StringBuilder sb) {
        if (node == null) return;
        sb.append(node.val).append(",");
        serializePreorder(node.left, sb);
        serializePreorder(node.right, sb);
    }

    public static AVLNode deserialize(String data) {
        if (data == null || data.isEmpty()) return null;
        String[] tokens = data.split(",");
        int[] values = Arrays.stream(tokens).filter(s -> !s.isEmpty()).mapToInt(Integer::parseInt).toArray();
        int[] index = new int[]{0};
        return build(values, index, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private static AVLNode build(int[] values, int[] index, long min, long max) {
        if (index[0] >= values.length) return null;
        int val = values[index[0]];
        if (val <= min || val >= max) return null;

        index[0]++;
        AVLNode node = new AVLNode(val);
        node.left = build(values, index, min, val);
        node.right = build(values, index, val, max);

        int lh = node.left != null ? node.left.height : 0;
        int rh = node.right != null ? node.right.height : 0;
        node.height = 1 + Math.max(lh, rh);
        return node;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$ cho cả hai quá trình.
- **Không gian (Space Complexity):** $O(n)$ lưu trữ chuỗi dữ liệu.

---

### Bài toán 28: Hiện Thực Mảng Động Bằng Cây AVL (Dynamic Array via AVL Tree)

**Đề bài chi tiết:**
Hiện thực một cấu trúc danh sách động `AVLList` hỗ trợ:
- `get(int index)`: Lấy giá trị tại chỉ số `index` trong $O(\log n)$.
- `set(int index, int val)`: Thay đổi giá trị tại `index` trong $O(\log n)$.
- `insert(int index, int val)`: Chèn phần tử vào vị trí `index` trong $O(\log n)$ (các phần tử phía sau tự dịch chuyển).
- `remove(int index)`: Xóa phần tử tại vị trí `index` trong $O(\log n)$.

**Phân tích thuật toán:**
- Dùng cây AVL với implicit indexing (chỉ số ngầm định): Vị trí của một nút trong danh sách được xác định bởi số lượng nút trong cây con bên trái của nó.
- Vị trí của nút hiện tại $u$ là $size(u.left)$.
- Mọi thao tác chèn/xóa tại vị trí bất kỳ đều chỉ mất $O(\log n)$ nhờ việc cân bằng cây tự động, vượt trội hoàn toàn so với $O(n)$ của `ArrayList` thông thường.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem28DynamicArrayAVL {

    public static class AVLNode {
        public int val;
        public int size;
        public int height;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
            this.size = 1;
            this.height = 1;
        }
    }

    public static class AVLList {
        private AVLNode root;

        private int size(AVLNode n) { return n == null ? 0 : n.size; }
        private int height(AVLNode n) { return n == null ? 0 : n.height; }

        private void update(AVLNode n) {
            if (n != null) {
                n.size = 1 + size(n.left) + size(n.right);
                n.height = 1 + Math.max(height(n.left), height(n.right));
            }
        }

        private AVLNode rightRotate(AVLNode y) {
            AVLNode x = y.left;
            AVLNode T2 = x.right;
            x.right = y;
            y.left = T2;
            update(y);
            update(x);
            return x;
        }

        private AVLNode leftRotate(AVLNode x) {
            AVLNode y = x.right;
            AVLNode T2 = y.left;
            y.left = x;
            x.right = T2;
            update(x);
            update(y);
            return y;
        }

        private AVLNode balance(AVLNode node) {
            if (node == null) return null;
            update(node);
            int bf = height(node.left) - height(node.right);

            if (bf > 1) {
                if (height(node.left.left) < height(node.left.right)) {
                    node.left = leftRotate(node.left);
                }
                return rightRotate(node);
            }
            if (bf < -1) {
                if (height(node.right.right) < height(node.right.left)) {
                    node.right = rightRotate(node.right);
                }
                return leftRotate(node);
            }
            return node;
        }

        public int size() {
            return size(root);
        }

        public int get(int index) {
            if (index < 0 || index >= size()) throw new IndexOutOfBoundsException();
            AVLNode curr = root;
            while (curr != null) {
                int leftSize = size(curr.left);
                if (index == leftSize) return curr.val;
                else if (index < leftSize) curr = curr.left;
                else {
                    index -= (leftSize + 1);
                    curr = curr.right;
                }
            }
            throw new IllegalStateException();
        }

        public void insert(int index, int val) {
            if (index < 0 || index > size()) throw new IndexOutOfBoundsException();
            root = insertAt(root, index, val);
        }

        private AVLNode insertAt(AVLNode node, int index, int val) {
            if (node == null) return new AVLNode(val);

            int leftSize = size(node.left);
            if (index <= leftSize) {
                node.left = insertAt(node.left, index, val);
            } else {
                node.right = insertAt(node.right, index - leftSize - 1, val);
            }

            return balance(node);
        }

        public void remove(int index) {
            if (index < 0 || index >= size()) throw new IndexOutOfBoundsException();
            root = removeAt(root, index);
        }

        private AVLNode removeAt(AVLNode node, int index) {
            int leftSize = size(node.left);
            if (index < leftSize) {
                node.left = removeAt(node.left, index);
            } else if (index > leftSize) {
                node.right = removeAt(node.right, index - leftSize - 1);
            } else {
                if (node.left == null || node.right == null) {
                    return node.left != null ? node.left : node.right;
                }
                AVLNode minRight = node.right;
                while (minRight.left != null) minRight = minRight.left;
                node.val = minRight.val;
                node.right = removeAt(node.right, 0);
            }
            return balance(node);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log n)$ cho tất cả các thao tác `get`, `set`, `insert`, `remove`.
- **Không gian (Space Complexity):** $O(n)$.

---

### Bài toán 29: Tìm Trung Vị Cửa Sổ Trượt Bằng Cây AVL (Sliding Window Median)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và kích thước cửa sổ trượt $k$. Cửa sổ trượt di chuyển từ trái sang phải mỗi lần 1 vị trí. Hãy tính trung vị của mỗi cửa sổ và trả về mảng kết quả dưới dạng số thực `double[]`.

**Phân tích thuật toán:**
- Sử dụng Cây AVL mở rộng hỗ trợ:
  - `insert(val)` trong $O(\log k)$.
  - `delete(val)` trong $O(\log k)$.
  - `findKth(k)` trong $O(\log k)$.
- Khi dịch chuyển cửa sổ từ $i$ sang $i + 1$:
  - Xóa phần tử cũ `nums[i - k]` ra khỏi cây AVL.
  - Chèn phần tử mới `nums[i]` vào cây AVL.
  - Truy vấn trung vị của cây hiện tại trong $O(\log k)$.
- Tổng thời gian cho mảng kích thước $n$ là $O(n \log k)$, hiệu quả hơn nhiều so với việc sắp xếp lại cửa sổ mất $O(n \cdot k \log k)$.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

public class Problem29SlidingWindowMedian {

    private static class Node {
        long val;
        int height;
        int size;
        Node left, right;

        Node(long val) {
            this.val = val;
            this.height = 1;
            this.size = 1;
        }
    }

    private static int height(Node n) { return n == null ? 0 : n.height; }
    private static int size(Node n) { return n == null ? 0 : n.size; }

    private static void update(Node n) {
        if (n != null) {
            n.height = 1 + Math.max(height(n.left), height(n.right));
            n.size = 1 + size(n.left) + size(n.right);
        }
    }

    private static Node rightRotate(Node y) {
        Node x = y.left;
        Node T2 = x.right;
        x.right = y;
        y.left = T2;
        update(y);
        update(x);
        return x;
    }

    private static Node leftRotate(Node x) {
        Node y = x.right;
        Node T2 = y.left;
        y.left = x;
        x.right = T2;
        update(x);
        update(y);
        return y;
    }

    private static Node balance(Node node) {
        if (node == null) return null;
        update(node);
        int bf = height(node.left) - height(node.right);

        if (bf > 1) {
            if (height(node.left.left) < height(node.left.right)) {
                node.left = leftRotate(node.left);
            }
            return rightRotate(node);
        }
        if (bf < -1) {
            if (height(node.right.right) < height(node.right.left)) {
                node.right = rightRotate(node.right);
            }
            return leftRotate(node);
        }
        return node;
    }

    private static Node insert(Node node, long val) {
        if (node == null) return new Node(val);
        if (val <= node.val) node.left = insert(node.left, val);
        else node.right = insert(node.right, val);
        return balance(node);
    }

    private static Node minValueNode(Node node) {
        Node curr = node;
        while (curr.left != null) curr = curr.left;
        return curr;
    }

    private static Node delete(Node node, long val) {
        if (node == null) return null;
        if (val < node.val) {
            node.left = delete(node.left, val);
        } else if (val > node.val) {
            node.right = delete(node.right, val);
        } else {
            if (node.left == null || node.right == null) {
                return node.left != null ? node.left : node.right;
            }
            Node temp = minValueNode(node.right);
            node.val = temp.val;
            node.right = delete(node.right, temp.val);
        }
        return balance(node);
    }

    private static long findKth(Node node, int k) {
        int leftSize = size(node.left);
        if (k == leftSize + 1) return node.val;
        else if (k <= leftSize) return findKth(node.left, k);
        else return findKth(node.right, k - leftSize - 1);
    }

    public static double[] medianSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k <= 0) return new double[0];

        int n = nums.length;
        double[] result = new double[n - k + 1];
        Node root = null;

        for (int i = 0; i < k; i++) {
            root = insert(root, nums[i]);
        }

        for (int i = k; i <= n; i++) {
            if (k % 2 != 0) {
                result[i - k] = (double) findKth(root, k / 2 + 1);
            } else {
                long m1 = findKth(root, k / 2);
                long m2 = findKth(root, k / 2 + 1);
                result[i - k] = (m1 + (double) m2) / 2.0;
            }

            if (i < n) {
                root = delete(root, nums[i - k]);
                root = insert(root, nums[i]);
            }
        }

        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n \log k)$.
- **Không gian (Space Complexity):** $O(k)$ lưu trữ cây AVL kích thước $k$.

---

### Bài toán 30: Duyệt Cây AVL Không Dùng Stack Với Không Gian $O(1)$ (Morris Inorder Traversal)

**Đề bài chi tiết:**
Cho gốc của một Cây AVL. Hãy thực hiện duyệt trung thứ tự (Inorder Traversal) và in ra toàn bộ các giá trị của cây theo thứ tự tăng dần mà chỉ sử dụng đúng $O(1)$ bộ nhớ phụ (không đệ quy, không dùng `Stack`, không thay đổi cấu trúc gốc của cây sau khi duyệt xong).

**Phân tích thuật toán:**
- Sử dụng thuật toán Morris Traversal:
  - Tận dụng các con trỏ `right` còn trống của các nút lá để tạo liên kết tạm thời (Thread) trỏ về nút kế tiếp (Inorder Successor).
  - Với mỗi nút hiện tại `curr`:
    1. Nếu `curr.left == null`: Thêm `curr.val` vào kết quả, chuyển sang `curr = curr.right`.
    2. Nếu `curr.left != null`: Tìm nút liền trước trong phép duyệt trung thứ tự (Inorder Predecessor: nút phải cùng của cây con trái):
       - Nếu `pred.right == null`: Tạo thread `pred.right = curr`, sau đó di chuyển sang trái `curr = curr.left`.
       - Nếu `pred.right == curr`: Gỡ bỏ thread `pred.right = null`, thêm `curr.val` vào kết quả, sau đó di chuyển sang phải `curr = curr.right`.
- Cây AVL được khôi phục nguyên vẹn trạng thái ban đầu sau khi duyệt xong.

**Mã nguồn Java:**
```java
package com.dsa.trees.avl;

import java.util.ArrayList;
import java.util.List;

public class Problem30MorrisTraversalAVL {

    public static class AVLNode {
        public int val;
        public AVLNode left, right;

        public AVLNode(int val) {
            this.val = val;
        }
    }

    public static List<Integer> morrisInorder(AVLNode root) {
        List<Integer> result = new ArrayList<>();
        AVLNode curr = root;

        while (curr != null) {
            if (curr.left == null) {
                result.add(curr.val);
                curr = curr.right;
            } else {
                // Tìm Inorder Predecessor
                AVLNode pred = curr.left;
                while (pred.right != null && pred.right != curr) {
                    pred = pred.right;
                }

                if (pred.right == null) {
                    // Tạo thread
                    pred.right = curr;
                    curr = curr.left;
                } else {
                    // Gỡ bỏ thread
                    pred.right = null;
                    result.add(curr.val);
                    curr = curr.right;
                }
            }
        }

        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(n)$ vì mỗi cạnh trong cây được đi qua tối đa 2 lần.
- **Không gian (Space Complexity):** $O(1)$ bộ nhớ phụ trợ (ngoại trừ danh sách chứa kết quả đầu ra).
