# 30 Bài tập Binary Search Tree (BST)

## 1. Validate Binary Search Tree
**Đề bài chi tiết:** Cho gốc `root` của một cây nhị phân, kiểm tra xem nó có phải là một Binary Search Tree hợp lệ hay không.
**Phân tích thuật toán:** Duyệt qua cây và duy trì các biên `min` và `max`. Một node phải có giá trị lớn hơn `min` và nhỏ hơn `max`. Khi rẽ trái, cập nhật `max = node.val`. Khi rẽ phải, cập nhật `min = node.val`.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValid(root, null, null);
    }
    private boolean isValid(TreeNode node, Integer min, Integer max) {
        if (node == null) return true;
        if ((min != null && node.val <= min) || (max != null && node.val >= max)) {
            return false;
        }
        return isValid(node.left, min, node.val) && isValid(node.right, node.val, max);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H) (H là chiều cao cây).

## 2. Search in a Binary Search Tree
**Đề bài chi tiết:** Tìm node có giá trị bằng `val` trong BST và trả về cây con gốc tại node đó, nếu không có trả về `null`.
**Phân tích thuật toán:** Sử dụng tính chất BST, nếu giá trị cần tìm nhỏ hơn giá trị node hiện tại, ta tìm kiếm nhánh trái, ngược lại nhánh phải. Dùng đệ quy hoặc vòng lặp.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        while (root != null && root.val != val) {
            root = val < root.val ? root.left : root.right;
        }
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1) (bản Iterative).

## 3. Insert into a Binary Search Tree
**Đề bài chi tiết:** Chèn một giá trị `val` vào một BST để kết quả vẫn là một BST hợp lệ. Đảm bảo chèn làm node lá.
**Phân tích thuật toán:** Dò xuống gốc như thao tác Search để tìm ra vị trí `null` và tạo mới node.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        if (val < root.val) {
            root.left = insertIntoBST(root.left, val);
        } else {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(H) (call stack).

## 4. Delete Node in a BST
**Đề bài chi tiết:** Xóa node có giá trị bằng `key` và bảo toàn tính chất BST.
**Phân tích thuật toán:** 
- Trường hợp 1: Node lá -> xóa luôn.
- Trường hợp 2: Có 1 con -> thay node này bằng node con.
- Trường hợp 3: Có 2 con -> Tìm Successor (nhỏ nhất bên nhánh phải), ghi đè giá trị và đệ quy xóa Successor.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;
        if (key < root.val) root.left = deleteNode(root.left, key);
        else if (key > root.val) root.right = deleteNode(root.right, key);
        else {
            if (root.left == null) return root.right;
            else if (root.right == null) return root.left;
            
            TreeNode minNode = findMin(root.right);
            root.val = minNode.val;
            root.right = deleteNode(root.right, minNode.val);
        }
        return root;
    }
    private TreeNode findMin(TreeNode node) {
        while (node.left != null) node = node.left;
        return node;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(H).

## 5. Lowest Common Ancestor of a Binary Search Tree
**Đề bài chi tiết:** Cho BST, tìm tổ tiên chung gần nhất (LCA) của 2 node p và q.
**Phân tích thuật toán:** LCA trong BST có tính chất: `p` và `q` nằm về 2 nhánh của cây so với tổ tiên chung. Duyệt từ gốc, rẽ hướng nếu cả p và q đều lớn hơn/hoặc nhỏ hơn gốc. Vị trí rẽ phân kỳ đầu tiên là LCA.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (root != null) {
            if (p.val < root.val && q.val < root.val) root = root.left;
            else if (p.val > root.val && q.val > root.val) root = root.right;
            else return root;
        }
        return null;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1).

## 6. Kth Smallest Element in a BST
**Đề bài chi tiết:** Cho BST và số nguyên `k`, tìm phần tử nhỏ thứ `k` của cây.
**Phân tích thuật toán:** Duyệt Inorder (Left-Root-Right) để thu được dãy tăng dần. Dừng lại khi duyệt đến phần tử thứ k.
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    int result = -1;
    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return result;
    }
    private void inorder(TreeNode node, int k) {
        if (node == null || count >= k) return;
        inorder(node.left, k);
        count++;
        if (count == k) result = node.val;
        inorder(node.right, k);
    }
}
```
**Độ phức tạp:** Thời gian O(H + k), Không gian O(H).

## 7. Convert Sorted Array to Binary Search Tree
**Đề bài chi tiết:** Cho một mảng nguyên đã sắp xếp, chuyển nó thành một cây BST có chiều cao cân bằng.
**Phân tích thuật toán:** Cây cân bằng cần chọn gốc là phần tử ở giữa mảng, sau đó đệ quy xây dựng nửa trái cho cây con trái và nửa phải cho cây con phải.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }
    private TreeNode build(int[] nums, int left, int right) {
        if (left > right) return null;
        int mid = left + (right - left) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = build(nums, left, mid - 1);
        root.right = build(nums, mid + 1, right);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(log N).

## 8. Recover Binary Search Tree
**Đề bài chi tiết:** Có hai node trong BST bị hoán vị nhầm. Khôi phục lại BST mà không làm thay đổi cấu trúc của nó (Yêu cầu $O(1)$ memory không tính call stack).
**Phân tích thuật toán:** Trong quá trình Inorder, phần tử hiện tại luôn phải lớn hơn phần tử trước đó. Tìm các lần suy giảm (drop) để xác định được 2 node bị sai, sau đó swap giá trị.
**Mã nguồn Java:**
```java
class Solution {
    TreeNode first = null, second = null, prev = null;
    public void recoverTree(TreeNode root) {
        inorder(root);
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
    }
    private void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        if (prev != null && root.val < prev.val) {
            if (first == null) first = prev;
            second = root;
        }
        prev = root;
        inorder(root.right);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 9. Binary Search Tree Iterator
**Đề bài chi tiết:** Thiết kế Iterator `BSTIterator` để duyệt trung thứ tự Inorder, hàm `next()` tốn thời gian trung bình O(1) và O(H) không gian.
**Phân tích thuật toán:** Sử dụng Stack để mô phỏng đệ quy nhánh trái. Khởi tạo bằng cách đẩy toàn bộ left child xuống tận cùng. Khi gọi `next()`, pop ra giá trị và lặp lại bước đẩy left child cho cây con phải.
**Mã nguồn Java:**
```java
class BSTIterator {
    private Stack<TreeNode> stack = new Stack<>();
    public BSTIterator(TreeNode root) {
        pushAll(root);
    }
    public int next() {
        TreeNode tmpNode = stack.pop();
        pushAll(tmpNode.right);
        return tmpNode.val;
    }
    public boolean hasNext() {
        return !stack.isEmpty();
    }
    private void pushAll(TreeNode node) {
        while (node != null) {
            stack.push(node);
            node = node.left;
        }
    }
}
```
**Độ phức tạp:** Thời gian Amortized O(1), Không gian O(H).

## 10. Unique Binary Search Trees
**Đề bài chi tiết:** Cho số $n$, trả về số lượng các cấu trúc BST duy nhất có thể được tạo ra từ 1 đến $n$.
**Phân tích thuật toán:** Đây là số Catalan. Có thể dùng Dynamic Programming để giải. `G(n) = \sum_{i=1}^n (G(i-1) * G(n-i))`, trong đó chọn $i$ làm root thì nhánh trái có $i-1$ đỉnh và phải có $n-i$ đỉnh.
**Mã nguồn Java:**
```java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                dp[i] += dp[j - 1] * dp[i - j];
            }
        }
        return dp[n];
    }
}
```
**Độ phức tạp:** Thời gian O(N^2), Không gian O(N).

---

## 11. Inorder Successor in BST
**Đề bài chi tiết:** Cho một cây BST và một node `p`, tìm node kế tiếp của `p` theo thứ tự duyệt Inorder. Nếu không có, trả về `null`.
**Phân tích thuật toán:** Bắt đầu từ `root`. Nếu giá trị của `root` lớn hơn giá trị của `p`, thì `root` có thể là successor, lưu lại `root` và rẽ trái để tìm một candidate nhỏ hơn. Nếu giá trị của `root` nhỏ hơn hoặc bằng `p`, ta phải rẽ phải để tìm successor.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        TreeNode successor = null;
        while (root != null) {
            if (root.val > p.val) {
                successor = root;
                root = root.left;
            } else {
                root = root.right;
            }
        }
        return successor;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1).

## 12. K-th Largest Element in a BST
**Đề bài chi tiết:** Cho gốc của một BST và một số nguyên `k`, tìm phần tử lớn thứ `k` trong cây.
**Phân tích thuật toán:** Thay vì duyệt Inorder (Left-Root-Right) để được mảng tăng dần, ta duyệt Reverse Inorder (Right-Root-Left) để được mảng giảm dần. Dừng duyệt khi đếm đến phần tử thứ `k`.
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    int result = -1;
    public int kthLargest(TreeNode root, int k) {
        reverseInorder(root, k);
        return result;
    }
    private void reverseInorder(TreeNode node, int k) {
        if (node == null || count >= k) return;
        reverseInorder(node.right, k);
        count++;
        if (count == k) result = node.val;
        reverseInorder(node.left, k);
    }
}
```
**Độ phức tạp:** Thời gian O(H + k), Không gian O(H).

## 13. Two Sum IV - Input is a BST
**Đề bài chi tiết:** Cho gốc của một BST và số nguyên `k`, trả về `true` nếu tồn tại hai phần tử trong BST có tổng bằng `k`, ngược lại trả về `false`.
**Phân tích thuật toán:** Duyệt qua cây BST (bằng BFS hoặc DFS) và lưu các giá trị đã gặp vào một `HashSet`. Nếu tại node hiện tại `node`, giá trị `k - node.val` đã có trong tập hợp thì trả về `true`.
**Mã nguồn Java:**
```java
class Solution {
    public boolean findTarget(TreeNode root, int k) {
        Set<Integer> set = new HashSet<>();
        return dfs(root, set, k);
    }
    private boolean dfs(TreeNode root, Set<Integer> set, int k) {
        if (root == null) return false;
        if (set.contains(k - root.val)) return true;
        set.add(root.val);
        return dfs(root.left, set, k) || dfs(root.right, set, k);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

## 14. Minimum Absolute Difference in BST
**Đề bài chi tiết:** Cho gốc của BST, tìm khoảng cách tuyệt đối nhỏ nhất giữa các giá trị của hai node khác nhau bất kỳ trong cây.
**Phân tích thuật toán:** BST có đặc tính khi duyệt Inorder sẽ thu được một dãy tăng dần. Độ chênh lệch nhỏ nhất luôn nằm giữa hai node liền kề nhau trong dãy duyệt Inorder này. Ta lưu lại node trước đó `prev` và tính độ chênh lệch.
**Mã nguồn Java:**
```java
class Solution {
    Integer prev = null;
    int minDiff = Integer.MAX_VALUE;
    public int getMinimumDifference(TreeNode root) {
        inorder(root);
        return minDiff;
    }
    private void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        if (prev != null) {
            minDiff = Math.min(minDiff, root.val - prev);
        }
        prev = root.val;
        inorder(root.right);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 15. Find Mode in Binary Search Tree
**Đề bài chi tiết:** Cho gốc của một BST chứa cả phần tử trùng lặp. Tìm các giá trị xuất hiện nhiều nhất (mode) trong cây.
**Phân tích thuật toán:** Do các giá trị bằng nhau sẽ nằm gần nhau khi duyệt Inorder, ta chỉ cần đếm số lần xuất hiện liên tiếp của một giá trị. Cập nhật số lần xuất hiện lớn nhất `maxCount` và danh sách các mode khi duyệt.
**Mã nguồn Java:**
```java
class Solution {
    Integer prev = null;
    int count = 1;
    int maxCount = 0;
    List<Integer> modes = new ArrayList<>();
    
    public int[] findMode(TreeNode root) {
        inorder(root);
        int[] result = new int[modes.size()];
        for (int i = 0; i < modes.size(); i++) {
            result[i] = modes.get(i);
        }
        return result;
    }
    
    private void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        
        if (prev != null) {
            if (root.val == prev) {
                count++;
            } else {
                count = 1;
            }
        }
        if (count > maxCount) {
            maxCount = count;
            modes.clear();
            modes.add(root.val);
        } else if (count == maxCount) {
            modes.add(root.val);
        }
        prev = root.val;
        
        inorder(root.right);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H) (call stack) hoặc O(N) (danh sách kết quả).

## 16. Construct Binary Search Tree from Preorder Traversal
**Đề bài chi tiết:** Cho một mảng các số nguyên đại diện cho duyệt Preorder của BST, hãy xây dựng lại và trả về gốc của BST đó.
**Phân tích thuật toán:** Duyệt qua mảng Preorder. Để quyết định xem một phần tử thuộc nhánh trái hay phải, ta sử dụng giới hạn `upperBound`. Khi duyệt, nếu phần tử nhỏ hơn `upperBound`, nó thuộc về cây con, ngược lại trả về `null`.
**Mã nguồn Java:**
```java
class Solution {
    int i = 0;
    public TreeNode bstFromPreorder(int[] preorder) {
        return build(preorder, Integer.MAX_VALUE);
    }
    private TreeNode build(int[] preorder, int bound) {
        if (i == preorder.length || preorder[i] > bound) return null;
        TreeNode root = new TreeNode(preorder[i++]);
        root.left = build(preorder, root.val);
        root.right = build(preorder, bound);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H) (H là chiều cao cây).

## 17. Trim a Binary Search Tree
**Đề bài chi tiết:** Cho gốc của BST và phạm vi `[low, high]`. Cắt tỉa (trim) cây sao cho tất cả các phần tử của nó đều nằm trong đoạn `[low, high]`. Trả về gốc của cây mới.
**Phân tích thuật toán:** 
- Nếu `root.val < low`, cả root và nhánh trái đều không hợp lệ, ta trả về kết quả cắt tỉa của nhánh phải.
- Nếu `root.val > high`, cả root và nhánh phải đều không hợp lệ, ta trả về kết quả cắt tỉa của nhánh trái.
- Nếu `low <= root.val <= high`, giữ lại root và đệ quy xuống cắt tỉa cả 2 nhánh.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) return null;
        if (root.val < low) return trimBST(root.right, low, high);
        if (root.val > high) return trimBST(root.left, low, high);
        
        root.left = trimBST(root.left, low, high);
        root.right = trimBST(root.right, low, high);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 18. Range Sum of BST
**Đề bài chi tiết:** Cho gốc của một BST và hai số nguyên `low` và `high`, trả về tổng giá trị của tất cả các node nằm trong đoạn `[low, high]`.
**Phân tích thuật toán:** Duyệt qua cây đệ quy DFS. Nếu `node.val` nằm ngoài khoảng thì ta chỉ đệ quy sang nhánh có thể chứa giá trị hợp lệ (nếu `< low` rẽ phải, nếu `> high` rẽ trái). Điều này giúp cắt tỉa (pruning) các nhánh không cần thiết.
**Mã nguồn Java:**
```java
class Solution {
    public int rangeSumBST(TreeNode root, int low, int high) {
        if (root == null) return 0;
        int sum = 0;
        if (root.val >= low && root.val <= high) sum += root.val;
        if (root.val > low) sum += rangeSumBST(root.left, low, high);
        if (root.val < high) sum += rangeSumBST(root.right, low, high);
        return sum;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 19. Balance a Binary Search Tree
**Đề bài chi tiết:** Cho một BST có thể không cân bằng, hãy trả về một BST chứa cùng các node nhưng được cân bằng hoàn toàn (độ sâu nhánh trái và phải chênh lệch không quá 1).
**Phân tích thuật toán:** Việc cân bằng yêu cầu duyệt Inorder để lấy ra mảng tăng dần các giá trị. Sau đó áp dụng cách tạo cây giống hệt bài *Convert Sorted Array to Binary Search Tree*.
**Mã nguồn Java:**
```java
class Solution {
    private List<TreeNode> sortedArr = new ArrayList<>();
    
    public TreeNode balanceBST(TreeNode root) {
        inorder(root);
        return build(0, sortedArr.size() - 1);
    }
    
    private void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        sortedArr.add(root);
        inorder(root.right);
    }
    
    private TreeNode build(int left, int right) {
        if (left > right) return null;
        int mid = left + (right - left) / 2;
        TreeNode root = sortedArr.get(mid);
        root.left = build(left, mid - 1);
        root.right = build(mid + 1, right);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

## 20. All Elements in Two Binary Search Trees
**Đề bài chi tiết:** Cho hai cây BST `root1` và `root2`, trả về một danh sách chứa tất cả các phần tử từ cả hai cây được sắp xếp theo thứ tự tăng dần.
**Phân tích thuật toán:** Lấy hai danh sách duyệt Inorder từ hai cây, ta sẽ có hai danh sách tăng dần. Sau đó, dùng kỹ thuật gộp mảng (merge hai danh sách đã sắp xếp như trong thuật toán Merge Sort).
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> getAllElements(TreeNode root1, TreeNode root2) {
        List<Integer> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        inorder(root1, list1);
        inorder(root2, list2);
        
        List<Integer> result = new ArrayList<>();
        int i = 0, j = 0;
        while (i < list1.size() && j < list2.size()) {
            if (list1.get(i) < list2.get(j)) {
                result.add(list1.get(i++));
            } else {
                result.add(list2.get(j++));
            }
        }
        while (i < list1.size()) result.add(list1.get(i++));
        while (j < list2.size()) result.add(list2.get(j++));
        return result;
    }
    private void inorder(TreeNode root, List<Integer> list) {
        if (root == null) return;
        inorder(root.left, list);
        list.add(root.val);
        inorder(root.right, list);
    }
}
```
**Độ phức tạp:** Thời gian O(N + M) (với N, M là số node 2 cây), Không gian O(N + M).

## 21. Convert BST to Greater Tree
**Đề bài chi tiết:** Cho gốc của một Binary Search Tree (BST), hãy biến đổi nó thành Greater Tree sao cho mọi khóa gốc ban đầu được thay đổi thành khóa ban đầu cộng với tổng của tất cả các khóa lớn hơn khóa ban đầu trong BST.
**Phân tích thuật toán:** Một BST có tính chất là duyệt Reverse Inorder (Right-Root-Left) sẽ đi qua các phần tử theo thứ tự giảm dần. Bằng cách duy trì một biến `sum` tích lũy, ta cập nhật giá trị của mỗi node đang xét.
**Mã nguồn Java:**
```java
class Solution {
    int sum = 0;
    public TreeNode convertBST(TreeNode root) {
        if (root != null) {
            convertBST(root.right);
            sum += root.val;
            root.val = sum;
            convertBST(root.left);
        }
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 22. Delete Nodes And Return Forest
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, mỗi node có một giá trị duy nhất. Đưa ra một mảng `to_delete` chứa giá trị các node cần xóa. Trả về danh sách gốc của các cây trong khu rừng (forest) còn lại sau khi xóa. (Mặc dù đề bài là cho Binary Tree chung, cách giải có thể áp dụng cho BST).
**Phân tích thuật toán:** Duyệt Post-order (trái, phải, gốc). Kiểm tra node hiện tại có thuộc tập `to_delete` hay không. Nếu có, ta đưa các con trái và con phải của nó (nếu khác `null`) vào danh sách kết quả, và trả về `null` cho node cha. Node gốc của cây ban đầu nếu không bị xóa cũng phải đưa vào kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        Set<Integer> toDeleteSet = new HashSet<>();
        for (int val : to_delete) toDeleteSet.add(val);
        List<TreeNode> forest = new ArrayList<>();
        
        root = process(root, toDeleteSet, forest);
        if (root != null) {
            forest.add(root);
        }
        return forest;
    }
    
    private TreeNode process(TreeNode node, Set<Integer> toDeleteSet, List<TreeNode> forest) {
        if (node == null) return null;
        node.left = process(node.left, toDeleteSet, forest);
        node.right = process(node.right, toDeleteSet, forest);
        
        if (toDeleteSet.contains(node.val)) {
            if (node.left != null) forest.add(node.left);
            if (node.right != null) forest.add(node.right);
            return null;
        }
        return node;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

## 23. Lowest Common Ancestor of Deepest Leaves
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, tìm tổ tiên chung gần nhất (LCA) của các node lá ở độ sâu lớn nhất. (Áp dụng chung cho Binary Tree / BST).
**Phân tích thuật toán:** Viết một hàm tính chiều cao của cây. Tại mỗi node, ta tính chiều cao của nhánh trái và nhánh phải. Nếu nhánh trái cao hơn, LCA chắc chắn nằm ở bên trái. Nếu nhánh phải cao hơn, LCA nằm bên phải. Nếu bằng nhau, thì node hiện tại chính là LCA chứa tất cả các lá sâu nhất.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        if (root == null) return null;
        int leftHeight = depth(root.left);
        int rightHeight = depth(root.right);
        if (leftHeight == rightHeight) {
            return root;
        } else if (leftHeight > rightHeight) {
            return lcaDeepestLeaves(root.left);
        } else {
            return lcaDeepestLeaves(root.right);
        }
    }
    private int depth(TreeNode node) {
        if (node == null) return 0;
        return 1 + Math.max(depth(node.left), depth(node.right));
    }
}
```
**Độ phức tạp:** Thời gian O(N^2) (có thể tối ưu thành O(N) nếu trả về luôn Pair <Node, Depth>), Không gian O(H).

## 24. Closest Binary Search Tree Value
**Đề bài chi tiết:** Cho một BST và một giá trị mục tiêu `target`, tìm giá trị trong BST gần với `target` nhất.
**Phân tích thuật toán:** Khởi tạo biến `closest` lưu giá trị gần nhất. Bắt đầu từ `root`, cập nhật `closest` nếu khoảng cách từ giá trị node hiện tại đến `target` nhỏ hơn. Sau đó quyết định rẽ sang nhánh trái (nếu `target < node.val`) hoặc nhánh phải (nếu `target > node.val`).
**Mã nguồn Java:**
```java
class Solution {
    public int closestValue(TreeNode root, double target) {
        int closest = root.val;
        while (root != null) {
            if (Math.abs(root.val - target) < Math.abs(closest - target)) {
                closest = root.val;
            }
            root = target < root.val ? root.left : root.right;
        }
        return closest;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1).

## 25. Inorder Predecessor in BST
**Đề bài chi tiết:** Cho một cây BST và một node `p`, tìm node ngay trước `p` theo thứ tự duyệt Inorder. Nếu không có, trả về `null`.
**Phân tích thuật toán:** Bắt đầu từ `root`, nếu `root.val < p.val`, `root` có thể là Predecessor, ta lưu lại và thử tìm xem bên nhánh phải có node nào gần với `p` hơn không. Nếu `root.val >= p.val`, thì Predecessor chỉ có thể nằm ở nhánh trái.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode inorderPredecessor(TreeNode root, TreeNode p) {
        TreeNode predecessor = null;
        while (root != null) {
            if (root.val < p.val) {
                predecessor = root;
                root = root.right;
            } else {
                root = root.left;
            }
        }
        return predecessor;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1).

## 26. Serialize and Deserialize BST
**Đề bài chi tiết:** Thiết kế một thuật toán để chuỗi hóa (serialize) một BST thành chuỗi và ngược lại tái dựng (deserialize) BST từ chuỗi đó. Mã hóa phải nhỏ gọn nhất có thể.
**Phân tích thuật toán:** Với BST, chỉ cần dùng mảng duyệt Preorder là đủ để xây dựng lại cây (vì có thể dùng tính chất BST Bounds thay vì lưu các kí tự `#` cho node `null`). Ta nối giá trị bằng kí tự phân cách phẩy `,`. Khi dựng lại, dùng giới hạn `upperBound` như bài 16.
**Mã nguồn Java:**
```java
public class Codec {
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        preorder(root, sb);
        return sb.toString();
    }
    private void preorder(TreeNode root, StringBuilder sb) {
        if (root == null) return;
        sb.append(root.val).append(",");
        preorder(root.left, sb);
        preorder(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) return null;
        String[] q = data.split(",");
        int[] index = new int[]{0};
        return build(q, index, Integer.MAX_VALUE);
    }
    private TreeNode build(String[] q, int[] index, int bound) {
        if (index[0] == q.length || Integer.parseInt(q[index[0]]) > bound) return null;
        TreeNode root = new TreeNode(Integer.parseInt(q[index[0]++]));
        root.left = build(q, index, root.val);
        root.right = build(q, index, bound);
        return root;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N).

## 27. Largest BST Subtree
**Đề bài chi tiết:** Cho một cây nhị phân, tìm cây con lớn nhất (có số lượng node nhiều nhất) mà chính nó là một Binary Search Tree (BST) hợp lệ. Trả về kích thước của BST đó.
**Phân tích thuật toán:** Dùng tính chất Bottom-up (Duyệt Post-order). Một cây gốc `root` là BST nếu cây con trái là BST, cây con phải là BST, và `max(left) < root.val < min(right)`. Ta trả về cho mỗi node một mảng hoặc class mô tả: `{isBST, size, min, max}`.
**Mã nguồn Java:**
```java
class Solution {
    class NodeInfo {
        boolean isBst;
        int size, min, max;
        NodeInfo(boolean isBst, int size, int min, int max) {
            this.isBst = isBst; this.size = size; this.min = min; this.max = max;
        }
    }
    int maxSize = 0;
    public int largestBSTSubtree(TreeNode root) {
        postOrder(root);
        return maxSize;
    }
    private NodeInfo postOrder(TreeNode root) {
        if (root == null) {
            return new NodeInfo(true, 0, Integer.MAX_VALUE, Integer.MIN_VALUE);
        }
        NodeInfo left = postOrder(root.left);
        NodeInfo right = postOrder(root.right);
        
        if (left.isBst && right.isBst && root.val > left.max && root.val < right.min) {
            int size = left.size + right.size + 1;
            maxSize = Math.max(maxSize, size);
            return new NodeInfo(true, size, Math.min(root.val, left.min), Math.max(root.val, right.max));
        }
        return new NodeInfo(false, 0, 0, 0);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

## 28. Split BST
**Đề bài chi tiết:** Cho gốc của một BST và một giá trị `V`. Cắt BST ra làm hai cây: một cây có tất cả các node nhỏ hơn hoặc bằng `V`, cây kia chứa tất cả các node lớn hơn `V`. Vẫn bảo toàn cấu trúc cây ban đầu. Trả về mảng chứa 2 gốc mới.
**Phân tích thuật toán:** Đệ quy cắt cây. Nếu `root.val <= V`, toàn bộ nhánh trái nằm ở cây thứ nhất. Nhánh phải của `root` sẽ đệ quy chia đôi tiếp, kết quả nhỏ hơn `<= V` gắn vào `root.right`, và lớn hơn gắn vào cây kia. Tương tự cho trường hợp `root.val > V`.
**Mã nguồn Java:**
```java
class Solution {
    public TreeNode[] splitBST(TreeNode root, int V) {
        if (root == null) return new TreeNode[]{null, null};
        if (root.val <= V) {
            TreeNode[] res = splitBST(root.right, V);
            root.right = res[0];
            return new TreeNode[]{root, res[1]};
        } else {
            TreeNode[] res = splitBST(root.left, V);
            root.left = res[1];
            return new TreeNode[]{res[0], root};
        }
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(H).

## 29. Find Distance Between Two Nodes of a BST
**Đề bài chi tiết:** Cho gốc của BST và 2 giá trị node có sẵn trong cây `p` và `q`. Tìm khoảng cách giữa 2 node (số lượng cạnh giữa chúng).
**Phân tích thuật toán:** Bước 1: Tìm Lowest Common Ancestor (LCA) của `p` và `q` (trong BST, bài toán này tìm rất dễ O(H)). Bước 2: Từ LCA, ta tính khoảng cách từ LCA đến `p` cộng khoảng cách từ LCA đến `q`. 
**Mã nguồn Java:**
```java
class Solution {
    public int findDistance(TreeNode root, int p, int q) {
        TreeNode lca = findLCA(root, p, q);
        return dist(lca, p) + dist(lca, q);
    }
    private TreeNode findLCA(TreeNode root, int p, int q) {
        while (root != null) {
            if (p < root.val && q < root.val) root = root.left;
            else if (p > root.val && q > root.val) root = root.right;
            else return root;
        }
        return null;
    }
    private int dist(TreeNode node, int target) {
        int d = 0;
        while (node.val != target) {
            d++;
            node = target < node.val ? node.left : node.right;
        }
        return d;
    }
}
```
**Độ phức tạp:** Thời gian O(H), Không gian O(1) (bản Iterative).

## 30. Count Complete Tree Nodes
**Đề bài chi tiết:** Cho gốc của một Complete Binary Tree (cây nhị phân hoàn chỉnh, ngoại trừ tầng cuối các lá đều lệch trái), đếm số lượng node của cây với độ phức tạp tốt hơn $O(N)$.
**Phân tích thuật toán:** Nếu cây đầy đủ (chiều cao nhánh trái bám cực trái và nhánh phải bám cực phải bằng nhau), số lượng node là `2^h - 1`. Nếu không, đệ quy đếm bên trái và bên phải. Nhờ thuộc tính cây hoàn chỉnh, ít nhất một trong hai cây con sẽ là cây nhị phân đầy đủ.
**Mã nguồn Java:**
```java
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null) return 0;
        int leftDepth = getDepth(root.left);
        int rightDepth = getDepth(root.right);
        
        if (leftDepth == rightDepth) {
            return (1 << leftDepth) + countNodes(root.right);
        } else {
            return (1 << rightDepth) + countNodes(root.left);
        }
    }
    private int getDepth(TreeNode node) {
        int d = 0;
        while (node != null) {
            d++;
            node = node.left;
        }
        return d;
    }
}
```
**Độ phức tạp:** Thời gian O(\log^2 N), Không gian O(\log N).
