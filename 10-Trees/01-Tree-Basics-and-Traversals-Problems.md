# Binary Tree Traversals - Problems

Tài liệu này bao gồm 30 bài tập về Duyệt cây nhị phân (Binary Tree Traversals). 10 bài đầu tiên cung cấp chi tiết: Đề bài, Phân tích thuật toán, Mã nguồn Java, Độ phức tạp. 20 bài còn lại được tóm tắt.

---

## 1. Binary Tree Inorder Traversal
**Đề bài chi tiết:** Cho gốc (`root`) của một cây nhị phân, trả về mảng/danh sách duyệt theo thứ tự `Inorder` (Trái - Node - Phải).
**Phân tích thuật toán:** Có thể dùng đệ quy hoặc dùng một Stack. Với đệ quy, hàm sẽ gọi đến nhánh trái, sau đó lưu giá trị node, rồi gọi đến nhánh phải.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            res.add(curr.val);
            curr = curr.right;
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$ (chiều cao của cây trong trường hợp tệ nhất).

---

## 2. Binary Tree Preorder Traversal
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, trả về danh sách duyệt `Preorder` (Node - Trái - Phải).
**Phân tích thuật toán:** Đẩy node hiện tại vào kết quả. Để duyệt lặp, push node gốc vào Stack. Sau khi pop node hiện tại, push nhánh Phải, sau đó push nhánh Trái để bảo đảm nhánh Trái sẽ được pop ra trước.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            res.add(node.val);
            if (node.right != null) stack.push(node.right);
            if (node.left != null) stack.push(node.left);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 3. Binary Tree Postorder Traversal
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, trả về danh sách duyệt `Postorder` (Trái - Phải - Node).
**Phân tích thuật toán:** Một mẹo đơn giản với dùng vòng lặp là thực hiện duyệt Node - Phải - Trái (tương tự Preorder hoán đổi Trái-Phải), sau đó đảo ngược danh sách (reverse list) ở cuối. Hoặc có thể chèn trực tiếp lên đầu (addFirst) của LinkedList.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        if (root == null) return res;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            res.addFirst(node.val); // Chèn vào đầu
            if (node.left != null) stack.push(node.left);
            if (node.right != null) stack.push(node.right);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 4. Binary Tree Level Order Traversal
**Đề bài chi tiết:** Trả về danh sách chứa các node theo từng mức (Level Order), mỗi mức là một mảng riêng biệt từ trái sang phải.
**Phân tích thuật toán:** Sử dụng `Queue`. Ở mỗi vòng lặp `while`, duyệt qua chính xác số lượng node tương đương kích thước (`size`) hiện tại của Queue, thu thập giá trị và push các node con vào Queue.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int levelNum = queue.size();
            List<Integer> subList = new ArrayList<>();
            for (int i = 0; i < levelNum; i++) {
                TreeNode node = queue.poll();
                subList.add(node.val);
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
            }
            res.add(subList);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 5. Binary Tree Zigzag Level Order Traversal
**Đề bài chi tiết:** Tương tự như Level Order, nhưng duyệt theo mô hình zigzag: mức đầu từ trái qua phải, mức sau từ phải qua trái, thay phiên.
**Phân tích thuật toán:** Kế thừa từ Level Order Traversal nhưng sử dụng thêm một cờ đánh dấu `leftToRight`. Nếu cờ này `false`, ta sẽ thêm giá trị của node vào vị trí đầu của mảng kết quả tạm (sublist).
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        boolean leftToRight = true;
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> row = new LinkedList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                if (leftToRight) row.add(node.val);
                else row.addFirst(node.val); // Dùng List thay cho LinkedList nếu muốn O(1)
                
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
            }
            res.add(row);
            leftToRight = !leftToRight;
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 6. Binary Tree Right Side View
**Đề bài chi tiết:** Giả sử bạn đứng ở góc nhìn phía bên phải của một cây nhị phân, trả về danh sách các giá trị node bạn có thể nhìn thấy, từ trên xuống dưới.
**Phân tích thuật toán:** Ta có thể dùng BFS và lấy phần tử cuối cùng của mỗi level. Hoặc dùng DFS theo thứ tự Gốc - Phải - Trái và thu thập node đầu tiên chạm tới ở mức (depth) mới.
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        dfs(root, res, 0);
        return res;
    }
    private void dfs(TreeNode node, List<Integer> res, int depth) {
        if (node == null) return;
        if (depth == res.size()) res.add(node.val);
        dfs(node.right, res, depth + 1); // Duyệt phải trước
        dfs(node.left, res, depth + 1);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$ (chiều cao $H$).

---

## 7. Vertical Order Traversal of a Binary Tree
**Đề bài chi tiết:** Lấy danh sách các node theo chiều dọc (trục y) từ trái qua phải. Nếu các node trùng cùng tọa độ, sắp xếp theo giá trị của chúng.
**Phân tích thuật toán:** Thực hiện duyệt DFS/BFS cùng với một cặp hoành độ x và y. Dùng `TreeMap` để tự động sắp xếp hoành độ x, và một `PriorityQueue` bên trong để xử lý y và giá trị node tại cùng một vị trí.
**Mã nguồn Java:**
```java
class Solution {
    class Point {
        TreeNode node; int r, c;
        Point(TreeNode node, int r, int c) {
            this.node = node; this.r = r; this.c = c;
        }
    }
    public List<List<Integer>> verticalTraversal(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        TreeMap<Integer, PriorityQueue<Point>> map = new TreeMap<>();
        Queue<Point> q = new LinkedList<>();
        q.add(new Point(root, 0, 0));
        while (!q.isEmpty()) {
            Point p = q.poll();
            map.putIfAbsent(p.c, new PriorityQueue<>((a, b) -> {
                if (a.r == b.r) return a.node.val - b.node.val;
                return a.r - b.r;
            }));
            map.get(p.c).add(p);
            if (p.node.left != null) q.add(new Point(p.node.left, p.r + 1, p.c - 1));
            if (p.node.right != null) q.add(new Point(p.node.right, p.r + 1, p.c + 1));
        }
        for (PriorityQueue<Point> pq : map.values()) {
            List<Integer> list = new ArrayList<>();
            while (!pq.isEmpty()) list.add(pq.poll().node.val);
            res.add(list);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N \log N)$ do PriorityQueue, Space: $O(N)$.

---

## 8. Construct Binary Tree from Preorder and Inorder Traversal
**Đề bài chi tiết:** Cho 2 mảng là Preorder và Inorder của một cây, xây dựng lại cây nhị phân duy nhất ban đầu.
**Phân tích thuật toán:** Trong Preorder, phần tử đầu tiên luôn là Gốc. Tìm phần tử đó trong Inorder, mọi phần tử bên trái sẽ tạo ra nhánh Trái, mọi phần tử bên phải tạo ra nhánh Phải. Dùng đệ quy lặp lại quá trình này (có thể dùng HashMap tối ưu tìm kiếm index).
**Mã nguồn Java:**
```java
class Solution {
    Map<Integer, Integer> inMap = new HashMap<>();
    int preIndex = 0;
    
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) inMap.put(inorder[i], i);
        return build(preorder, 0, inorder.length - 1);
    }
    
    private TreeNode build(int[] preorder, int inStart, int inEnd) {
        if (inStart > inEnd) return null;
        TreeNode root = new TreeNode(preorder[preIndex++]);
        int rootIndex = inMap.get(root.val);
        root.left = build(preorder, inStart, rootIndex - 1);
        root.right = build(preorder, rootIndex + 1, inEnd);
        return root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 9. Construct Binary Tree from Inorder and Postorder Traversal
**Đề bài chi tiết:** Xây dựng lại cây khi biết 2 mảng Inorder và Postorder.
**Phân tích thuật toán:** Tương tự bài 8. Điểm khác biệt là trong Postorder, gốc nằm ở cuối mảng, và chúng ta lấy root lùi dần về đầu mảng. Vì vậy đệ quy cần ưu tiên gọi khởi tạo nhánh Phải trước khi gọi nhánh Trái.
**Mã nguồn Java:**
```java
class Solution {
    Map<Integer, Integer> inMap = new HashMap<>();
    int postIndex;
    
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for (int i = 0; i < inorder.length; i++) inMap.put(inorder[i], i);
        postIndex = postorder.length - 1;
        return build(postorder, 0, inorder.length - 1);
    }
    
    private TreeNode build(int[] postorder, int inStart, int inEnd) {
        if (inStart > inEnd) return null;
        TreeNode root = new TreeNode(postorder[postIndex--]);
        int rootIndex = inMap.get(root.val);
        root.right = build(postorder, rootIndex + 1, inEnd); // Phải trước
        root.left = build(postorder, inStart, rootIndex - 1); // Trái sau
        return root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 10. Populating Next Right Pointers in Each Node
**Đề bài chi tiết:** Cho cây nhị phân hoàn hảo (mọi lá cùng cấp). Mỗi node có con trỏ `next`. Hãy trỏ `next` của mỗi node tới node nằm liền kề bên phải nó trong cùng một mức.
**Phân tích thuật toán:** Duyệt BFS thường tốn $O(N)$ bộ nhớ. Để làm với không gian O(1), ta dùng chính con trỏ `next` ở level cao hơn để thiết lập các `next` pointer ở level thấp hơn.
**Mã nguồn Java:**
```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        Node levelStart = root;
        while (levelStart.left != null) {
            Node curr = levelStart;
            while (curr != null) {
                curr.left.next = curr.right;
                if (curr.next != null) {
                    curr.right.next = curr.next.left;
                }
                curr = curr.next;
            }
            levelStart = levelStart.left;
        }
        return root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(1)$.

---

## 11. Populating Next Right Pointers in Each Node II
**Đề bài chi tiết:** Cho gốc của một cây nhị phân bất kỳ. Mỗi node có một con trỏ `next`. Trỏ `next` của mỗi node tới node liền kề bên phải nó trong cùng một mức. Nếu không có node liền kề bên phải, `next` trỏ tới `null`.
**Phân tích thuật toán:** Ta không thể giả định cây hoàn hảo. Do đó, ở mỗi mức, cần duy trì một con trỏ `dummy` head để liên kết mức tiếp theo, và một con trỏ `curr` để duyệt từng mức.
**Mã nguồn Java:**
```java
class Solution {
    public Node connect(Node root) {
        Node curr = root;
        while (curr != null) {
            Node dummy = new Node(0);
            Node tail = dummy;
            while (curr != null) {
                if (curr.left != null) {
                    tail.next = curr.left;
                    tail = tail.next;
                }
                if (curr.right != null) {
                    tail.next = curr.right;
                    tail = tail.next;
                }
                curr = curr.next;
            }
            curr = dummy.next;
        }
        return root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(1)$.

---

## 12. Flatten Binary Tree to Linked List
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, hãy "làm phẳng" (flatten) cây này thành một danh sách liên kết một chiều lệch phải theo thứ tự tiền thứ tự (Preorder) (In-place).
**Phân tích thuật toán:** Duyệt theo thứ tự Postorder đảo ngược (Phải - Trái - Gốc). Lưu lại node trước đó (prev). Gán con phải của node hiện tại bằng `prev` và con trái bằng `null`, cập nhật `prev = node`. Hoặc có thể dùng vòng lặp Morris traversal.
**Mã nguồn Java:**
```java
class Solution {
    private TreeNode prev = null;
    public void flatten(TreeNode root) {
        if (root == null) return;
        flatten(root.right);
        flatten(root.left);
        root.right = prev;
        root.left = null;
        prev = root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$ (do call stack đệ quy).

---

## 13. Serialize and Deserialize Binary Tree
**Đề bài chi tiết:** Thiết kế một thuật toán để mã hóa (serialize) một cây nhị phân thành một chuỗi và giải mã (deserialize) chuỗi đó lại thành cây nhị phân ban đầu.
**Phân tích thuật toán:** Sử dụng DFS (Preorder). Ghi giá trị các node, nếu node `null` ghi `"N"`. Khi giải mã, dùng một `Queue` để lấy dần các giá trị từ chuỗi ra và tái tạo cây theo đệ quy.
**Mã nguồn Java:**
```java
public class Codec {
    public String serialize(TreeNode root) {
        if (root == null) return "N,";
        return root.val + "," + serialize(root.left) + serialize(root.right);
    }

    public TreeNode deserialize(String data) {
        Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
        return buildTree(queue);
    }
    
    private TreeNode buildTree(Queue<String> queue) {
        String val = queue.poll();
        if (val.equals("N")) return null;
        TreeNode node = new TreeNode(Integer.parseInt(val));
        node.left = buildTree(queue);
        node.right = buildTree(queue);
        return node;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 14. Binary Tree Maximum Path Sum
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, tìm tổng lớn nhất của một đường đi (path) bất kỳ trong cây. Đường đi có thể bắt đầu và kết thúc tại bất kỳ node nào.
**Phân tích thuật toán:** Dùng DFS. Hàm DFS trả về tổng lớn nhất của đường đi từ một node xuôi xuống một nhánh. Tại mỗi node, cập nhật giá trị max toàn cục = `node.val + max(left, 0) + max(right, 0)`.
**Mã nguồn Java:**
```java
class Solution {
    int maxPath = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        dfs(root);
        return maxPath;
    }
    private int dfs(TreeNode node) {
        if (node == null) return 0;
        int left = Math.max(0, dfs(node.left));
        int right = Math.max(0, dfs(node.right));
        maxPath = Math.max(maxPath, left + right + node.val);
        return Math.max(left, right) + node.val;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 15. Lowest Common Ancestor of a Binary Tree
**Đề bài chi tiết:** Tìm nút tổ tiên chung gần nhất (LCA) của hai nút `p` và `q` trong cây nhị phân.
**Phân tích thuật toán:** Duyệt DFS từ gốc. Nếu bằng `p`, `q` hoặc `null` thì trả về node đó. Nếu cả nhánh trái và phải đều trả về giá trị khác `null` -> node hiện tại là LCA. Nếu chỉ 1 nhánh khác `null`, trả về nhánh đó.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) return root;
        return left != null ? left : right;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 16. Diameter of Binary Tree
**Đề bài chi tiết:** Cho một cây nhị phân, tính đường kính (diameter) của nó (số cạnh lớn nhất trong đường đi giữa hai node bất kỳ).
**Phân tích thuật toán:** Tương tự bài tính chiều cao. Ở mỗi node, đường kính qua node đó = chiều cao nhánh trái + chiều cao nhánh phải. Cập nhật kết quả lớn nhất qua từng node.
**Mã nguồn Java:**
```java
class Solution {
    int maxDiameter = 0;
    public int diameterOfBinaryTree(TreeNode root) {
        height(root);
        return maxDiameter;
    }
    private int height(TreeNode node) {
        if (node == null) return 0;
        int left = height(node.left);
        int right = height(node.right);
        maxDiameter = Math.max(maxDiameter, left + right);
        return Math.max(left, right) + 1;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 17. Maximum Depth of Binary Tree
**Đề bài chi tiết:** Tìm độ sâu lớn nhất của một cây nhị phân (từ gốc đến lá xa nhất).
**Phân tích thuật toán:** Đệ quy tính độ sâu. Độ sâu tại một node = 1 + max(độ sâu nhánh trái, độ sâu nhánh phải).
**Mã nguồn Java:**
```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 18. Minimum Depth of Binary Tree
**Đề bài chi tiết:** Tìm độ sâu nhỏ nhất của một cây nhị phân (từ gốc đến lá gần nhất).
**Phân tích thuật toán:** Dùng BFS. Khi duyệt Level Order, node đầu tiên gặp mà cả 2 con đều `null` (lá) chính là node có độ sâu nhỏ nhất.
**Mã nguồn Java:**
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> q = new LinkedList<>();
        q.add(root);
        int depth = 1;
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();
                if (node.left == null && node.right == null) return depth;
                if (node.left != null) q.add(node.left);
                if (node.right != null) q.add(node.right);
            }
            depth++;
        }
        return depth;
    }
}
```
**Độ phức tạp:** Time: $O(N)$ (nhanh hơn DFS ở trung bình), Space: $O(N)$.

---

## 19. Symmetric Tree
**Đề bài chi tiết:** Kiểm tra xem một cây nhị phân có đối xứng qua gương quanh gốc của nó hay không.
**Phân tích thuật toán:** Viết hàm đệ quy kiểm tra 2 node `left` và `right`. Chúng đối xứng nếu cả 2 `null`, hoặc cùng có giá trị và (left.left đối xứng right.right) và (left.right đối xứng right.left).
**Mã nguồn Java:**
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }
    private boolean isMirror(TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        return (t1.val == t2.val) 
            && isMirror(t1.left, t2.right) 
            && isMirror(t1.right, t2.left);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 20. Path Sum
**Đề bài chi tiết:** Cho một cây nhị phân và một số nguyên `targetSum`, kiểm tra xem có đường đi từ gốc tới một lá nào có tổng các giá trị bằng `targetSum` không.
**Phân tích thuật toán:** Đệ quy DFS. Mỗi lần gọi đệ quy, trừ `targetSum` đi giá trị node hiện tại. Khi đến node lá, kiểm tra xem `targetSum` còn lại có bằng `0` không.
**Mã nguồn Java:**
```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        if (root.left == null && root.right == null && targetSum - root.val == 0) {
            return true;
        }
        return hasPathSum(root.left, targetSum - root.val) 
            || hasPathSum(root.right, targetSum - root.val);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 21. Path Sum II
**Đề bài chi tiết:** Cho gốc của một cây nhị phân và một số nguyên `targetSum`, trả về tất cả các đường đi từ gốc đến lá sao cho tổng các giá trị trên đường đi bằng `targetSum`.
**Phân tích thuật toán:** Duyệt DFS (Backtracking). Lưu trữ đường đi hiện tại vào một danh sách. Mỗi khi đến một node lá và tổng các node trên đường đi đúng bằng `targetSum`, thêm bản sao chép của danh sách hiện tại vào kết quả. Sau đó, backtrack (xóa node cuối cùng) để thử nhánh khác.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        List<List<Integer>> res = new ArrayList<>();
        dfs(root, targetSum, new ArrayList<>(), res);
        return res;
    }
    private void dfs(TreeNode node, int sum, List<Integer> current, List<List<Integer>> res) {
        if (node == null) return;
        current.add(node.val);
        if (node.left == null && node.right == null && sum == node.val) {
            res.add(new ArrayList<>(current));
        } else {
            dfs(node.left, sum - node.val, current, res);
            dfs(node.right, sum - node.val, current, res);
        }
        current.remove(current.size() - 1); // Backtrack
    }
}
```
**Độ phức tạp:** Time: $O(N^2)$ trong trường hợp tệ nhất (mỗi lá phải copy một list độ dài $N$), Space: $O(H)$ cho call stack và List trung gian.

---

## 22. Path Sum III
**Đề bài chi tiết:** Tìm số lượng đường đi có tổng bằng `targetSum` trong cây nhị phân. Đường đi phải đi từ trên xuống dưới (từ node cha xuống node con) nhưng không nhất thiết bắt đầu từ gốc hoặc kết thúc ở lá.
**Phân tích thuật toán:** Tương tự bài toán Subarray Sum Equals K. Dùng HashMap để lưu trữ tần suất của `prefixSum`. Tại mỗi node, cập nhật `prefixSum` hiện tại, kiểm tra xem `prefixSum - targetSum` đã xuất hiện bao nhiêu lần, cộng vào tổng số đường đi.
**Mã nguồn Java:**
```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0L, 1);
        return dfs(root, 0L, targetSum, prefixSumMap);
    }
    private int dfs(TreeNode node, long currSum, int target, Map<Long, Integer> map) {
        if (node == null) return 0;
        currSum += node.val;
        int count = map.getOrDefault(currSum - target, 0);
        map.put(currSum, map.getOrDefault(currSum, 0) + 1);
        
        count += dfs(node.left, currSum, target, map);
        count += dfs(node.right, currSum, target, map);
        
        map.put(currSum, map.get(currSum) - 1); // Backtrack
        return count;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 23. Sum Root to Leaf Numbers
**Đề bài chi tiết:** Mỗi đường đi từ gốc đến lá biểu diễn một con số. Ví dụ, đường đi 1->2->3 đại diện cho số 123. Tính tổng tất cả các con số sinh ra từ mọi đường đi từ gốc đến lá.
**Phân tích thuật toán:** Duyệt DFS, truyền giá trị tính toán xuống nhánh con. Tại node hiện tại: `currentValue = parentValue * 10 + node.val`. Nếu là node lá, trả về `currentValue`.
**Mã nguồn Java:**
```java
class Solution {
    public int sumNumbers(TreeNode root) {
        return dfs(root, 0);
    }
    private int dfs(TreeNode node, int currentSum) {
        if (node == null) return 0;
        currentSum = currentSum * 10 + node.val;
        if (node.left == null && node.right == null) {
            return currentSum;
        }
        return dfs(node.left, currentSum) + dfs(node.right, currentSum);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 24. Check Completeness of a Binary Tree
**Đề bài chi tiết:** Kiểm tra xem một cây nhị phân có phải là cây nhị phân hoàn chỉnh (Complete Binary Tree) hay không. Trong cây hoàn chỉnh, tất cả các mức đều chứa đầy node ngoại trừ mức cuối cùng, và các node ở mức cuối cùng đều được dồn hết về bên trái.
**Phân tích thuật toán:** Duyệt BFS (Level Order). Cho cả node `null` vào Queue. Khi gặp node `null` đầu tiên, đánh dấu cờ `end = true`. Nếu sau đó lấy ra một node khác `null` từ Queue -> cây không hoàn chỉnh.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isCompleteTree(TreeNode root) {
        if (root == null) return true;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        boolean end = false;
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if (node == null) {
                end = true;
            } else {
                if (end) return false;
                queue.add(node.left);
                queue.add(node.right);
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 25. Find Bottom Left Tree Value
**Đề bài chi tiết:** Tìm giá trị của node nằm dưới cùng và bên trái cùng (bottom-left) của cây nhị phân.
**Phân tích thuật toán:** Sử dụng BFS, duyệt từ Phải sang Trái (add con Phải trước, con Trái sau). Bằng cách này, node cuối cùng được lấy ra khỏi hàng đợi chính là node dưới cùng bên trái.
**Mã nguồn Java:**
```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        TreeNode curr = null;
        while (!queue.isEmpty()) {
            curr = queue.poll();
            if (curr.right != null) queue.add(curr.right);
            if (curr.left != null) queue.add(curr.left);
        }
        return curr.val;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 26. Invert Binary Tree
**Đề bài chi tiết:** Đảo ngược một cây nhị phân (tráo đổi con trái và con phải của toàn bộ các node).
**Phân tích thuật toán:** Duyệt đệ quy. Lưu lại con trái, gán con trái bằng con phải đã được đảo, gán con phải bằng con trái đã được đảo.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        TreeNode left = root.left;
        TreeNode right = root.right;
        root.left = invertTree(right);
        root.right = invertTree(left);
        return root;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$.

---

## 27. Kth Smallest Element in a BST
**Đề bài chi tiết:** Cho gốc của một Cây Nhị Phân Tìm Kiếm (BST) và một số nguyên `k`, trả về phần tử nhỏ thứ `k` trong cây.
**Phân tích thuật toán:** Với BST, duyệt Inorder (Trái - Node - Phải) sẽ trả về danh sách các giá trị tăng dần. Duyệt Inorder đếm số lượng node đã đi qua, khi biến đếm bằng `k` thì đó chính là kết quả.
**Mã nguồn Java:**
```java
class Solution {
    private int count = 0;
    private int result = 0;
    
    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return result;
    }
    
    private void inorder(TreeNode node, int k) {
        if (node == null || count >= k) return;
        inorder(node.left, k);
        count++;
        if (count == k) {
            result = node.val;
            return;
        }
        inorder(node.right, k);
    }
}
```
**Độ phức tạp:** Time: $O(H + k)$, Space: $O(H)$.

---

## 28. Recover Binary Search Tree
**Đề bài chi tiết:** Có hai node trong BST bị hoán đổi vị trí nhầm lẫn. Hãy khôi phục lại cây mà không làm thay đổi cấu trúc (In-place).
**Phân tích thuật toán:** Khi duyệt Inorder BST chuẩn, các node sẽ theo thứ tự tăng dần. Nếu có hai node hoán vị, ta sẽ thấy sự sụt giảm ở một hoặc hai chỗ. Lưu lại node lớn hơn ở lần sụt giảm thứ nhất và node nhỏ hơn ở lần sụt giảm thứ hai, cuối cùng tráo đổi giá trị của chúng.
**Mã nguồn Java:**
```java
class Solution {
    TreeNode first = null, second = null, prev = new TreeNode(Integer.MIN_VALUE);
    public void recoverTree(TreeNode root) {
        inorder(root);
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
    }
    
    private void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        if (first == null && prev.val > root.val) {
            first = prev;
        }
        if (first != null && prev.val > root.val) {
            second = root;
        }
        prev = root;
        inorder(root.right);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(H)$ (hoặc $O(1)$ nếu sử dụng Morris Traversal).

---

## 29. Binary Tree Boundary Traversal
**Đề bài chi tiết:** Trích xuất đường viền biên (boundary) của một cây nhị phân ngược chiều kim đồng hồ, bắt đầu từ gốc, lấy biên trái, các node lá (từ trái qua phải) rồi biên phải (từ dưới lên).
**Phân tích thuật toán:** Tách thành 3 bài toán con: Lấy các node viền trái không phải là lá, lấy tất cả node lá, và lấy các node viền phải không phải là lá (sau đó đảo ngược lại).
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> boundaryOfBinaryTree(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        if (!isLeaf(root)) res.add(root.val);
        
        TreeNode t = root.left;
        while (t != null) {
            if (!isLeaf(t)) res.add(t.val);
            if (t.left != null) t = t.left;
            else t = t.right;
        }
        
        addLeaves(res, root);
        
        Stack<Integer> stack = new Stack<>();
        t = root.right;
        while (t != null) {
            if (!isLeaf(t)) stack.push(t.val);
            if (t.right != null) t = t.right;
            else t = t.left;
        }
        while (!stack.isEmpty()) res.add(stack.pop());
        
        return res;
    }
    private boolean isLeaf(TreeNode node) {
        return node.left == null && node.right == null;
    }
    private void addLeaves(List<Integer> res, TreeNode node) {
        if (node == null) return;
        if (isLeaf(node)) {
            res.add(node.val);
            return;
        }
        addLeaves(res, node.left);
        addLeaves(res, node.right);
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.

---

## 30. Binary Tree Level Order Traversal II
**Đề bài chi tiết:** Duyệt các node trong cây nhị phân theo tầng (Level Order) nhưng trả về danh sách theo chiều ngược lại (từ tầng dưới cùng lên gốc).
**Phân tích thuật toán:** Rất giống với bài Level Order chuẩn (BFS bằng Queue). Chỉ khác là khi lấy ra list của một tầng, thay vì `res.add(sublist)`, ta dùng `res.addFirst(sublist)` đối với LinkedList, hoặc reverse List kết quả cuối cùng.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        LinkedList<List<Integer>> res = new LinkedList<>();
        if (root == null) return res;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> subList = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                subList.add(node.val);
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
            }
            res.addFirst(subList);
        }
        return res;
    }
}
```
**Độ phức tạp:** Time: $O(N)$, Space: $O(N)$.
