# Bài tập Binary Tree Properties (Các tính chất của cây nhị phân)

Tập tài liệu này cung cấp 30 bài tập về các tính chất cốt lõi của cây nhị phân như chiều cao, độ sâu, đường kính, LCA, đường đi (Path Sum), cây con. 10 bài đầu tiên sẽ được giải quyết chi tiết bao gồm đề bài, phân tích, mã nguồn Java và độ phức tạp.

---

## 1. Maximum Depth of Binary Tree (LeetCode 104)
**Đề bài chi tiết:** Cho gốc (root) của một cây nhị phân, trả về độ sâu lớn nhất (maximum depth) của nó. Độ sâu lớn nhất là số lượng các node dọc theo con đường dài nhất từ node gốc xuống tới node lá xa nhất.

**Phân tích thuật toán:**
Sử dụng đệ quy (DFS Bottom-up). Độ sâu của cây bằng 1 cộng với giá trị lớn nhất giữa độ sâu của cây con trái và độ sâu của cây con phải.
- **Base case:** Nếu `root == null`, trả về 0.
- **Recursive step:** Lấy độ sâu lớn nhất của trái và phải.

**Mã nguồn Java:**
```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        
        return Math.max(leftDepth, rightDepth) + 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Ta duyệt qua mỗi node đúng một lần.
- **Space Complexity:** $O(H)$ - H là chiều cao của cây (call stack của đệ quy). $O(N)$ trong trường hợp xấu nhất (cây lệch), $O(\log N)$ nếu cây cân bằng.

---

## 2. Minimum Depth of Binary Tree (LeetCode 111)
**Đề bài chi tiết:** Cho một cây nhị phân, tìm độ sâu nhỏ nhất của nó. Độ sâu nhỏ nhất là số lượng node trên con đường ngắn nhất từ node gốc tới node lá gần nhất.

**Phân tích thuật toán:**
Cũng dùng đệ quy, nhưng cần lưu ý: nếu một node chỉ có 1 con, độ sâu nhỏ nhất không phải là nhánh rỗng (0), mà phải tính trên nhánh con tồn tại. Chỉ khi nào có cả hai con, ta mới so sánh `min` của hai bên.

**Mã nguồn Java:**
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        // Nếu không có con trái, đi xuống con phải
        if (root.left == null) {
            return minDepth(root.right) + 1;
        }
        // Nếu không có con phải, đi xuống con trái
        if (root.right == null) {
            return minDepth(root.left) + 1;
        }
        // Nếu có cả 2, lấy giá trị nhỏ nhất
        return Math.min(minDepth(root.left), minDepth(root.right)) + 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt mọi node trong trường hợp tệ nhất.
- **Space Complexity:** $O(H)$ - Không gian cho Stack đệ quy.

---

## 3. Diameter of Binary Tree (LeetCode 543)
**Đề bài chi tiết:** Cho `root` của cây nhị phân, tính độ dài của đường kính của cây. Đường kính là chiều dài của con đường dài nhất giữa hai node bất kỳ trong cây. Đường này có thể đi hoặc không đi qua node gốc. Độ dài được tính bằng số lượng cạnh (edges).

**Phân tích thuật toán:**
Đường kính tại một node cụ thể bằng `Height(Left) + Height(Right)`. Do đường kính lớn nhất có thể không nằm ở root, ta cần dùng một biến toàn cục (hoặc mảng 1 phần tử) để liên tục cập nhật đường kính lớn nhất mỗi khi tính chiều cao của bất kỳ node nào (DFS Post-order).

**Mã nguồn Java:**
```java
class Solution {
    int maxDiameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        calculateHeight(root);
        return maxDiameter;
    }

    private int calculateHeight(TreeNode node) {
        if (node == null) return 0;

        int leftHeight = calculateHeight(node.left);
        int rightHeight = calculateHeight(node.right);

        // Cập nhật đường kính lớn nhất nếu tìm thấy đường đi qua node này dài hơn
        maxDiameter = Math.max(maxDiameter, leftHeight + rightHeight);

        // Trả về chiều cao để cha của nó sử dụng
        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 4. Balanced Binary Tree (LeetCode 110)
**Đề bài chi tiết:** Cho cây nhị phân, xác định xem nó có phải là một cây cân bằng chiều cao (height-balanced) hay không. Cây cân bằng chiều cao khi chiều cao của cây con trái và phải của mọi node chênh lệch nhau không quá 1.

**Phân tích thuật toán:**
Sử dụng đệ quy DFS để tính chiều cao giống bài trước. Tuy nhiên, nếu ở bất kỳ nhánh nào ta phát hiện sự chênh lệch chiều cao > 1, ta lập tức trả về -1 (đánh dấu là không cân bằng). Từ đó về sau, cha cũng nhận -1 và truyền thẳng lên tới root để tiết kiệm thời gian, tránh vòng lặp thừa.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return checkHeight(root) != -1;
    }

    private int checkHeight(TreeNode node) {
        if (node == null) return 0;

        int leftHeight = checkHeight(node.left);
        if (leftHeight == -1) return -1; // Cắt tỉa nhánh (Pruning)

        int rightHeight = checkHeight(node.right);
        if (rightHeight == -1) return -1;

        if (Math.abs(leftHeight - rightHeight) > 1) {
            return -1;
        }

        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 5. Symmetric Tree (LeetCode 101)
**Đề bài chi tiết:** Cho root của cây nhị phân, kiểm tra xem nó có phải là một cái gương tự phản chiếu hay không (đối xứng xung quanh tâm của nó).

**Phân tích thuật toán:**
Một cây đối xứng nếu con trái và con phải của root phản chiếu nhau. Hai cây t1 và t2 phản chiếu nếu:
- Cả hai node bằng null (đối xứng).
- Hoặc, cả hai có giá trị giống nhau VÀ con trái của t1 đối xứng với con phải của t2, VÀ con phải của t1 đối xứng với con trái của t2.

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
        if (t1.val != t2.val) return false;

        return isMirror(t1.left, t2.right) && isMirror(t1.right, t2.left);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 6. Invert Binary Tree (LeetCode 226)
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, hãy đảo ngược (invert) cây và trả về gốc của nó. Gương phản chiếu của mỗi node trái sẽ thành phải và ngược lại.

**Phân tích thuật toán:**
Rất đơn giản, tại mỗi node, ta lưu con trái vào biến tạm, sau đó gọi đệ quy đảo con phải gán cho con trái, và gọi đệ quy đảo con trái (biến tạm) gán cho con phải. Có thể duyệt Pre-order hoặc Post-order đều hoạt động tốt.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        
        TreeNode temp = root.left;
        root.left = invertTree(root.right);
        root.right = invertTree(temp);
        
        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 7. Path Sum (LeetCode 112)
**Đề bài chi tiết:** Cho cây nhị phân và số nguyên `targetSum`, trả về `true` nếu cây có con đường từ Root tới Leaf sao cho tổng các giá trị dọc theo đường đi bằng `targetSum`. Trả về `false` nếu không có.

**Phân tích thuật toán:**
Dùng DFS đệ quy giảm dần `targetSum` sau mỗi lần đi xuống.
- Khi chạm đến một node, ta trừ `targetSum` cho giá trị node đó.
- Nếu đây là leaf node (không có con trái và phải), ta kiểm tra xem `targetSum` còn lại có bằng đúng giá trị node đó (hoặc `targetSum` còn lại = 0 sau khi trừ) không.

**Mã nguồn Java:**
```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        
        // Trả về true nếu là node lá và targetSum vừa đủ
        if (root.left == null && root.right == null && root.val == targetSum) {
            return true;
        }
        
        // Gọi đệ quy cho nhánh trái và nhánh phải với targetSum đã giảm đi
        int nextSum = targetSum - root.val;
        return hasPathSum(root.left, nextSum) || hasPathSum(root.right, nextSum);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 8. Path Sum II (LeetCode 113)
**Đề bài chi tiết:** Tương tự bài Path Sum 1, nhưng thay vì trả về `true`/`false`, yêu cầu trả về danh sách TẤT CẢ các đường đi từ root tới leaf có tổng bằng `targetSum`.

**Phân tích thuật toán:**
Sử dụng DFS Backtracking. Ta duyệt mảng và mang theo một `List` chứa đường đi hiện tại. Khi đến lá, nếu tổng bằng `targetSum`, ta copy `List` đó vào danh sách kết quả. Quá trình quay lui (backtrack) thực hiện bằng cách xóa phần tử cuối khỏi `List` sau khi duyệt xong nhánh con.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> currentPath = new ArrayList<>();
        findPaths(root, targetSum, currentPath, result);
        return result;
    }
    
    private void findPaths(TreeNode node, int target, List<Integer> currentPath, List<List<Integer>> result) {
        if (node == null) return;
        
        currentPath.add(node.val);
        
        if (node.left == null && node.right == null && node.val == target) {
            result.add(new ArrayList<>(currentPath)); // Copy cấu trúc mảng
        } else {
            findPaths(node.left, target - node.val, currentPath, result);
            findPaths(node.right, target - node.val, currentPath, result);
        }
        
        // Backtrack
        currentPath.remove(currentPath.size() - 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ thao tác duyệt. (Có thể tốn thêm $O(N)$ để copy list ở worst case). Tổng quan $O(N^2)$ trong trường hợp cực đoan.
- **Space Complexity:** $O(H)$ cho recursion stack và mảng tạm.

---

## 9. Lowest Common Ancestor of a Binary Tree (LeetCode 236)
**Đề bài chi tiết:** Cho một cây nhị phân, tìm Tổ tiên chung thấp nhất (LCA) của hai node p và q. LCA được định nghĩa là node thấp nhất trên cây có chứa cả p và q làm hậu duệ. 

**Phân tích thuật toán:**
Dùng DFS truy lùng từ dưới lên.
- Nếu node hiện tại là `null`, hoặc chính là `p`, hoặc `q` -> trả về node hiện tại.
- Tìm trong nhánh trái và phải. Nếu nhánh trái trả về kết quả, nhánh phải cũng trả về kết quả -> Node hiện tại chính là LCA (p và q nằm ở hai bên).
- Nếu chỉ 1 nhánh trả về khác `null`, ta truyền nhánh đó lên trên.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        // Nếu tìm thấy p và q ở 2 nhánh khác nhau
        if (left != null && right != null) {
            return root;
        }
        
        // Trả về node khác null (nhánh chứa p hoặc q)
        return left != null ? left : right;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

---

## 10. Subtree of Another Tree (LeetCode 572)
**Đề bài chi tiết:** Cho root của hai cây nhị phân `root` và `subRoot`, trả về `true` nếu có một cây con của `root` có cấu trúc và giá trị y hệt `subRoot`.

**Phân tích thuật toán:**
Có hai bài toán nhỏ. Bài 1: Kiểm tra 2 cây có Identical không (Giống hệt nhau). Bài 2: Duyệt từng node trong cây gốc để so khớp bài 1 với `subRoot`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isSubtree(TreeNode root, TreeNode subRoot) {
        if (root == null) return false;
        
        // Kiểm tra xem root hiện tại có khớp với subRoot không
        if (isIdentical(root, subRoot)) {
            return true;
        }
        
        // Nếu không, tìm trong nhánh trái hoặc nhánh phải
        return isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot);
    }
    
    private boolean isIdentical(TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        if (t1.val != t2.val) return false;
        
        return isIdentical(t1.left, t2.left) && isIdentical(t1.right, t2.right);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \times M)$ với $N$ là số node của cây chính, $M$ là số node của `subRoot`.
- **Space Complexity:** $O(H_{root} + H_{subRoot})$ cho call stack.

---

## 11. Same Tree (LeetCode 100)
**Đề bài chi tiết:** Cho gốc của hai cây nhị phân `p` và `q`, viết hàm kiểm tra xem chúng có giống nhau hay không. Hai cây nhị phân được coi là giống nhau nếu chúng có cấu trúc hoàn toàn giống nhau và các node tương ứng có cùng giá trị.

**Phân tích thuật toán:**
Sử dụng đệ quy để kiểm tra đồng thời cả hai cây. Nếu cả hai node đều `null`, chúng giống nhau. Nếu một trong hai node `null` hoặc giá trị của chúng khác nhau, chúng không giống nhau. Nếu giống nhau, tiếp tục kiểm tra đệ quy cho cây con bên trái và cây con bên phải.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null || p.val != q.val) {
            return false;
        }
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Trong đó $N$ là số lượng node của cây nhỏ hơn.
- **Space Complexity:** $O(H)$ - $H$ là chiều cao của cây để duy trì call stack.

---

## 12. Count Complete Tree Nodes (LeetCode 222)
**Đề bài chi tiết:** Cho gốc của một cây nhị phân hoàn chỉnh (complete binary tree), trả về số lượng các node trong cây đó. Cây nhị phân hoàn chỉnh là cây mà mọi mức độ (level), ngoại trừ có thể là mức cuối cùng, đều được lấp đầy, và tất cả các node ở mức cuối cùng càng nằm sang trái càng tốt.

**Phân tích thuật toán:**
Cách $O(N)$ là đếm từng node. Nhưng với tính chất của cây hoàn chỉnh, ta có thể làm trong $O(\log^2 N)$. Bằng cách đo chiều cao của cây con cực trái và cực phải, nếu bằng nhau thì đó là cây hoàn hảo (perfect tree) và số node là $2^h - 1$. Nếu khác nhau, ta đệ quy xuống trái và phải.

**Mã nguồn Java:**
```java
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null) return 0;
        
        int leftHeight = getLeftHeight(root);
        int rightHeight = getRightHeight(root);
        
        if (leftHeight == rightHeight) {
            return (1 << leftHeight) - 1; // 2^h - 1
        }
        
        return 1 + countNodes(root.left) + countNodes(root.right);
    }
    
    private int getLeftHeight(TreeNode node) {
        int height = 0;
        while (node != null) {
            height++;
            node = node.left;
        }
        return height;
    }
    
    private int getRightHeight(TreeNode node) {
        int height = 0;
        while (node != null) {
            height++;
            node = node.right;
        }
        return height;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(\log^2 N)$ - Ở mỗi bước ta tốn $O(\log N)$ để tính chiều cao, và đệ quy $O(\log N)$ bước.
- **Space Complexity:** $O(\log N)$ - Cho kích thước của call stack đệ quy.

---

## 13. Maximum Depth of N-ary Tree (LeetCode 559)
**Đề bài chi tiết:** Cho một cây N-ary, hãy tìm độ sâu lớn nhất của nó. Độ sâu lớn nhất là số lượng node trên đường đi dài nhất từ node gốc đến node lá xa nhất. N-ary tree có mỗi node chứa một giá trị và một danh sách các con của nó.

**Phân tích thuật toán:**
Tương tự bài số 1, ta áp dụng DFS. Độ sâu của node hiện tại sẽ bằng 1 cộng với giá trị lớn nhất trong số các độ sâu của tất cả các node con.

**Mã nguồn Java:**
```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) return 0;
        
        int max = 0;
        for (Node child : root.children) {
            max = Math.max(max, maxDepth(child));
        }
        
        return max + 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - $N$ là tổng số node của cây.
- **Space Complexity:** $O(H)$ - $H$ là độ sâu lớn nhất của cây.

---

## 14. Sum Root to Leaf Numbers (LeetCode 129)
**Đề bài chi tiết:** Cho root của một cây nhị phân chứa các chữ số từ 0 đến 9. Mỗi con đường từ gốc đến lá đại diện cho một số (ví dụ: 1 -> 2 -> 3 đại diện cho số 123). Trả về tổng của tất cả các số được tạo ra từ các đường đi từ gốc đến lá.

**Phân tích thuật toán:**
Dùng DFS với một tham số để lưu trữ giá trị số tạo thành dọc theo đường đi từ gốc xuống (`currentSum = currentSum * 10 + node.val`). Khi đến node lá, ta trả về giá trị này. Tổng của node hiện tại bằng tổng của các nhánh trái và phải.

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

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt qua tất cả các node của cây.
- **Space Complexity:** $O(H)$ - Bộ nhớ cho call stack của đệ quy.

---

## 15. Path Sum III (LeetCode 437)
**Đề bài chi tiết:** Cho gốc của một cây nhị phân và số nguyên `targetSum`, trả về số lượng đường đi có tổng các node trên đó bằng `targetSum`. Đường đi không nhất thiết phải bắt đầu từ gốc hay kết thúc ở lá, nhưng phải đi từ trên xuống (từ node cha xuống node con).

**Phân tích thuật toán:**
Tối ưu nhất là dùng kĩ thuật Prefix Sum kết hợp với HashMap (như bài Subarray Sum Equals K) trong quá trình duyệt DFS. `prefixSum` lưu tổng từ gốc tới node hiện tại, nếu `prefixSum - targetSum` đã tồn tại trong HashMap, nghĩa là có đường đi hợp lệ. Khi backtrack, ta cần trừ đi tần suất của `prefixSum` hiện tại trong HashMap.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    int count = 0;
    
    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefixMap = new HashMap<>();
        prefixMap.put(0L, 1);
        dfs(root, 0L, targetSum, prefixMap);
        return count;
    }
    
    private void dfs(TreeNode node, long currentSum, int targetSum, Map<Long, Integer> prefixMap) {
        if (node == null) return;
        
        currentSum += node.val;
        
        if (prefixMap.containsKey(currentSum - targetSum)) {
            count += prefixMap.get(currentSum - targetSum);
        }
        
        prefixMap.put(currentSum, prefixMap.getOrDefault(currentSum, 0) + 1);
        
        dfs(node.left, currentSum, targetSum, prefixMap);
        dfs(node.right, currentSum, targetSum, prefixMap);
        
        // Backtrack
        prefixMap.put(currentSum, prefixMap.get(currentSum) - 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt mỗi node 1 lần, thao tác HashMap là $O(1)$.
- **Space Complexity:** $O(N)$ - Cho HashMap và call stack (trong trường hợp xấu nhất).

---

## 16. Find Bottom Left Tree Value (LeetCode 513)
**Đề bài chi tiết:** Cho gốc của một cây nhị phân, trả về giá trị của node nằm tận cùng bên dưới cùng bên trái của cây.

**Phân tích thuật toán:**
Có thể dùng BFS (truyền thống) duyệt từ phải sang trái; node cuối cùng pop ra khỏi hàng đợi chính là kết quả. Hoặc dùng DFS, duy trì một biến theo dõi độ sâu lớn nhất đã chạm tới. Mỗi khi đạt độ sâu mới, do duyệt Pre-order (nhánh trái trước), node đầu tiên ở độ sâu đó chắc chắn là node trái nhất.

**Mã nguồn Java:**
```java
class Solution {
    int maxDepth = -1;
    int bottomLeftValue = 0;
    
    public int findBottomLeftValue(TreeNode root) {
        dfs(root, 0);
        return bottomLeftValue;
    }
    
    private void dfs(TreeNode node, int depth) {
        if (node == null) return;
        
        if (depth > maxDepth) {
            maxDepth = depth;
            bottomLeftValue = node.val;
        }
        
        dfs(node.left, depth + 1);
        dfs(node.right, depth + 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Cần duyệt tất cả các node.
- **Space Complexity:** $O(H)$ - Không gian đệ quy.

---

## 17. Maximum Width of Binary Tree (LeetCode 662)
**Đề bài chi tiết:** Cho gốc của cây nhị phân, trả về chiều rộng lớn nhất của nó. Chiều rộng của một cấp là độ dài giữa các node ở ngoài cùng bên trái và ngoài cùng bên phải của cấp đó (bao gồm cả các node null ở giữa).

**Phân tích thuật toán:**
Sử dụng BFS và đánh số thứ tự (index) cho các node giống như lưu trữ cây trong mảng (node thứ $i$ có con trái là $2i$ và con phải là $2i+1$). Chiều rộng tại một level bằng `index của node cuối - index của node đầu + 1`. Để tránh tràn số, ta có thể chuẩn hóa index bằng cách trừ đi index của node đầu tiên ở mỗi mức.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    class Pair {
        TreeNode node;
        int index;
        Pair(TreeNode node, int index) {
            this.node = node;
            this.index = index;
        }
    }
    
    public int widthOfBinaryTree(TreeNode root) {
        if (root == null) return 0;
        int maxWidth = 0;
        Queue<Pair> queue = new LinkedList<>();
        queue.add(new Pair(root, 0));
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            int minIndex = queue.peek().index;
            int first = 0, last = 0;
            
            for (int i = 0; i < size; i++) {
                int curIndex = queue.peek().index - minIndex;
                TreeNode node = queue.poll().node;
                
                if (i == 0) first = curIndex;
                if (i == size - 1) last = curIndex;
                
                if (node.left != null) {
                    queue.add(new Pair(node.left, curIndex * 2));
                }
                if (node.right != null) {
                    queue.add(new Pair(node.right, curIndex * 2 + 1));
                }
            }
            maxWidth = Math.max(maxWidth, last - first + 1);
        }
        
        return maxWidth;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Mỗi node được đẩy vào queue một lần.
- **Space Complexity:** $O(N)$ - Trong trường hợp xấu nhất queue lưu một cấp có $N/2$ node.

---

## 18. Lowest Common Ancestor of a Binary Search Tree (LeetCode 235)
**Đề bài chi tiết:** Cho một cây tìm kiếm nhị phân (BST), tìm Tổ tiên chung thấp nhất (LCA) của hai node p và q. 

**Phân tích thuật toán:**
Dựa vào tính chất của BST: node bên trái nhỏ hơn node hiện tại, node bên phải lớn hơn.
Nếu cả `p` và `q` đều lớn hơn `root`, LCA nằm ở nhánh phải.
Nếu cả `p` và `q` đều nhỏ hơn `root`, LCA nằm ở nhánh trái.
Nếu `p` và `q` nằm về hai phía của `root` (hoặc một trong hai chính là `root`), thì `root` chính là LCA.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (root != null) {
            if (p.val > root.val && q.val > root.val) {
                root = root.right;
            } else if (p.val < root.val && q.val < root.val) {
                root = root.left;
            } else {
                return root; // Tách ra 2 ngã hoặc bằng root
            }
        }
        return null;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(H)$ - Do đi dọc xuống một nhánh của cây. Worst case là $O(N)$ nếu cây lệch.
- **Space Complexity:** $O(1)$ - Không dùng đệ quy, không thêm cấu trúc dữ liệu.

---

## 19. All Nodes Distance K in Binary Tree (LeetCode 863)
**Đề bài chi tiết:** Cho root của cây nhị phân, node `target` và số nguyên `k`. Trả về một mảng chứa giá trị của tất cả các node nằm cách `target` khoảng cách `k`.

**Phân tích thuật toán:**
Cây là đồ thị có hướng (từ trên xuống), để đi ngược từ `target` lên cha của nó, ta cần dựng cấu trúc đồ thị hai chiều (chuyển đổi cây thành đồ thị bằng cách dùng HashMap map từ Node tới Parent). Sau đó, dùng BFS bắt đầu từ `target` để loang ra tới độ xa `k`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        Map<TreeNode, TreeNode> parentMap = new HashMap<>();
        buildParentMap(root, null, parentMap);
        
        Queue<TreeNode> queue = new LinkedList<>();
        Set<TreeNode> visited = new HashSet<>();
        
        queue.add(target);
        visited.add(target);
        
        int currentDistance = 0;
        
        while (!queue.isEmpty()) {
            if (currentDistance == k) {
                List<Integer> result = new ArrayList<>();
                for (TreeNode node : queue) {
                    result.add(node.val);
                }
                return result;
            }
            
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode curr = queue.poll();
                
                if (curr.left != null && !visited.contains(curr.left)) {
                    queue.add(curr.left);
                    visited.add(curr.left);
                }
                if (curr.right != null && !visited.contains(curr.right)) {
                    queue.add(curr.right);
                    visited.add(curr.right);
                }
                TreeNode parent = parentMap.get(curr);
                if (parent != null && !visited.contains(parent)) {
                    queue.add(parent);
                    visited.add(parent);
                }
            }
            currentDistance++;
        }
        
        return new ArrayList<>();
    }
    
    private void buildParentMap(TreeNode node, TreeNode parent, Map<TreeNode, TreeNode> parentMap) {
        if (node == null) return;
        parentMap.put(node, parent);
        buildParentMap(node.left, node, parentMap);
        buildParentMap(node.right, node, parentMap);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Một lần chạy DFS để build map, một lần chạy BFS tìm node.
- **Space Complexity:** $O(N)$ - Cho Map lưu parent, HashSet và Queue.

---

## 20. Binary Tree Longest Consecutive Sequence (LeetCode 298)
**Đề bài chi tiết:** Cho một cây nhị phân, hãy tìm độ dài của chuỗi liên tiếp tăng dần dài nhất từ cha xuống con (ví dụ: 1 -> 2 -> 3). 

**Phân tích thuật toán:**
Dùng DFS truyền giá trị độ dài chuỗi hiện tại. Mỗi lần đi từ cha xuống con, nếu `child.val == parent.val + 1`, chuỗi được kéo dài thêm 1. Ngược lại, chuỗi bị cắt đứt và bắt đầu lại độ dài từ 1 ở node con đó. Liên tục cập nhật một biến max ở phạm vi toàn cục.

**Mã nguồn Java:**
```java
class Solution {
    private int maxLength = 0;
    
    public int longestConsecutive(TreeNode root) {
        if (root == null) return 0;
        dfs(root, null, 0);
        return maxLength;
    }
    
    private void dfs(TreeNode node, TreeNode parent, int length) {
        if (node == null) return;
        
        if (parent != null && node.val == parent.val + 1) {
            length++;
        } else {
            length = 1;
        }
        
        maxLength = Math.max(maxLength, length);
        
        dfs(node.left, node, length);
        dfs(node.right, node, length);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt qua tất cả các node.
- **Space Complexity:** $O(H)$ - H là chiều cao của cây (cho call stack).

---

## 21. Cousins in Binary Tree (LeetCode 993)
**Đề bài chi tiết:** Trong một cây nhị phân, gốc nằm ở độ sâu 0, và các con của mỗi độ sâu k nằm ở độ sâu k+1. Hai node của cây nhị phân được gọi là anh em họ (cousins) nếu chúng có cùng độ sâu nhưng khác node cha. Cho gốc của cây nhị phân và các giá trị x, y. Trả về `true` nếu và chỉ nếu các node tương ứng với các giá trị x và y là anh em họ.

**Phân tích thuật toán:**
Dùng BFS hoặc DFS để ghi nhận lại độ sâu (depth) và cha (parent) của 2 node x và y. Nếu cả 2 đều được tìm thấy, so sánh `depth[x] == depth[y]` và `parent[x] != parent[y]`.

**Mã nguồn Java:**
```java
class Solution {
    int xDepth = -1, yDepth = -1;
    TreeNode xParent = null, yParent = null;

    public boolean isCousins(TreeNode root, int x, int y) {
        dfs(root, null, x, y, 0);
        return xDepth == yDepth && xParent != yParent;
    }

    private void dfs(TreeNode node, TreeNode parent, int x, int y, int depth) {
        if (node == null) return;
        
        if (node.val == x) {
            xDepth = depth;
            xParent = parent;
        } else if (node.val == y) {
            yDepth = depth;
            yParent = parent;
        }
        
        dfs(node.left, node, x, y, depth + 1);
        dfs(node.right, node, x, y, depth + 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Trong trường hợp xấu nhất, duyệt qua tất cả các node.
- **Space Complexity:** $O(H)$ - Không gian trên call stack.

---

## 22. Check if a String Is a Valid Sequence from Root to Leaves (LeetCode 1430)
**Đề bài chi tiết:** Cho một cây nhị phân với mỗi node chứa một số nguyên từ 0 đến 9 và một mảng số nguyên `arr`. Trả về `true` nếu tồn tại một đường đi từ gốc tới lá mà các giá trị trên đường đi đó lần lượt tạo thành mảng `arr`.

**Phân tích thuật toán:**
Dùng DFS với chỉ số `index` tương ứng với mảng `arr`. Tại mỗi bước, kiểm tra `node.val == arr[index]`. Nếu sai hoặc hết mảng, trả về false. Chỉ trả về true nếu `index` ở phần tử cuối cùng của `arr` VÀ `node` hiện tại là node lá (không có con trái, con phải).

**Mã nguồn Java:**
```java
class Solution {
    public boolean isValidSequence(TreeNode root, int[] arr) {
        return dfs(root, arr, 0);
    }
    
    private boolean dfs(TreeNode node, int[] arr, int index) {
        if (node == null || index == arr.length || node.val != arr[index]) {
            return false;
        }
        
        // Nếu đã đến phần tử cuối và node hiện tại là leaf
        if (index == arr.length - 1 && node.left == null && node.right == null) {
            return true;
        }
        
        return dfs(node.left, arr, index + 1) || dfs(node.right, arr, index + 1);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - N là số node.
- **Space Complexity:** $O(H)$ - H là chiều cao của cây.

---

## 23. Count Good Nodes in Binary Tree (LeetCode 1448)
**Đề bài chi tiết:** Cho cây nhị phân, một node được gọi là "Good" nếu trên con đường từ gốc đến node đó, không có node nào có giá trị lớn hơn node đó. Hãy đếm số lượng "Good nodes".

**Phân tích thuật toán:**
Duyệt DFS và truyền xuống giá trị `maxVal` (giá trị lớn nhất trên đường đi từ root tới hiện tại). Nếu `node.val >= maxVal`, node đó là Good node, ta cộng 1 vào kết quả và cập nhật `maxVal = node.val`.

**Mã nguồn Java:**
```java
class Solution {
    public int goodNodes(TreeNode root) {
        return dfs(root, root.val);
    }
    
    private int dfs(TreeNode node, int maxVal) {
        if (node == null) return 0;
        
        int res = 0;
        if (node.val >= maxVal) {
            res = 1;
            maxVal = node.val;
        }
        
        res += dfs(node.left, maxVal);
        res += dfs(node.right, maxVal);
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Mỗi node duyệt một lần.
- **Space Complexity:** $O(H)$ - Không gian stack của đệ quy.

---

## 24. Nodes in a Subtree (HackerRank)
**Đề bài chi tiết:** Cho một cây với `N` node, mỗi node lưu trữ một ký tự. Cho một danh sách các truy vấn, mỗi truy vấn cung cấp một `node_id` và một ký tự `c`. Yêu cầu tìm xem trong cây con (subtree) của `node_id` có bao nhiêu node chứa ký tự `c`.

**Phân tích thuật toán:**
Nếu có nhiều truy vấn, DFS một lần là không đủ. Ta có thể duyệt cây một lần và dùng DFS để tính toán bảng tần suất ký tự cho từng node. Tại mỗi node, tần suất ký tự bằng tổng tần suất từ tất cả con của nó cộng với ký tự của chính nó. Do số lượng ký tự thường nhỏ (26 chữ cái tiếng Anh), ta có thể tạo mảng `int[26]` cho mỗi node.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    class Node {
        int id;
        char val;
        List<Node> children = new ArrayList<>();
    }
    
    // Map lưu số lượng ký tự c trong subtree của từng node id
    Map<Integer, int[]> countMap = new HashMap<>();

    public int[] countOfNodes(Node root, ArrayList<Query> queries) {
        dfs(root);
        
        int[] res = new int[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            int id = queries.get(i).id;
            char c = queries.get(i).c;
            if (countMap.containsKey(id)) {
                res[i] = countMap.get(id)[c - 'a'];
            } else {
                res[i] = 0;
            }
        }
        return res;
    }
    
    private int[] dfs(Node node) {
        int[] counts = new int[26];
        if (node == null) return counts;
        
        counts[node.val - 'a']++;
        
        for (Node child : node.children) {
            int[] childCounts = dfs(child);
            for (int i = 0; i < 26; i++) {
                counts[i] += childCounts[i];
            }
        }
        
        countMap.put(node.id, counts);
        return counts;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N \times 26 + Q)$ - Quá trình DFS tốn $O(N)$ kết hợp với gộp mảng 26 phần tử. Sau đó mỗi truy vấn $Q$ tốn $O(1)$.
- **Space Complexity:** $O(N \times 26)$ - Lưu thông tin bảng tần suất cho mọi node.

---

## 25. Lowest Common Ancestor III (LeetCode 1644)
**Đề bài chi tiết:** Tìm Tổ tiên chung thấp nhất (LCA) của hai node `p` và `q` trong cây nhị phân. Điểm khác biệt là: `p` hoặc `q` có thể **không tồn tại** trong cây. Nếu một trong hai không tồn tại, trả về `null`.

**Phân tích thuật toán:**
Với thuật toán gốc, khi gặp `p` hoặc `q` ta trả về ngay mà không tìm kiếm xuống dưới. Nhưng ở bài này, ta phải duyệt sâu hết để đảm bảo tìm thấy cả `p` và `q`. Do đó, sử dụng DFS duyệt Post-order. Khai báo 2 cờ (flags) để xác nhận có gặp cả 2 node hay không.

**Mã nguồn Java:**
```java
class Solution {
    boolean pFound = false;
    boolean qFound = false;
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        TreeNode lca = dfs(root, p, q);
        return (pFound && qFound) ? lca : null;
    }
    
    private TreeNode dfs(TreeNode node, TreeNode p, TreeNode q) {
        if (node == null) return null;
        
        // Vẫn phải duyệt con trước khi xử lý node hiện tại
        TreeNode left = dfs(node.left, p, q);
        TreeNode right = dfs(node.right, p, q);
        
        if (node == p) {
            pFound = true;
            return node;
        }
        if (node == q) {
            qFound = true;
            return node;
        }
        
        if (left != null && right != null) return node;
        
        return left != null ? left : right;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Cần duyệt tất cả các node để xác nhận sự tồn tại.
- **Space Complexity:** $O(H)$ - Không gian stack của đệ quy.

---

## 26. Lowest Common Ancestor of a Binary Tree IV (LeetCode 1676)
**Đề bài chi tiết:** Cho root của cây nhị phân và một mảng chứa $K$ nodes. Tìm tổ tiên chung thấp nhất của tất cả $K$ node đó. Biết rằng tất cả các node đều có mặt trong cây.

**Phân tích thuật toán:**
Đưa tất cả $K$ node vào một `HashSet` để tra cứu $O(1)$. Khi DFS, nếu `node` hiện tại nằm trong Set, ta có thể trả về `node` hiện tại (vì nó là cha của các con của nó hoặc của chính nó). Nếu 2 nhánh trái và phải đều trả về khác `null`, có nghĩa là 1 tập con các node nằm ở trái, 1 tập ở phải, nên `node` hiện tại chính là LCA chung.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode[] nodes) {
        Set<TreeNode> set = new HashSet<>(Arrays.asList(nodes));
        return dfs(root, set);
    }
    
    private TreeNode dfs(TreeNode node, Set<TreeNode> set) {
        if (node == null) return null;
        if (set.contains(node)) return node;
        
        TreeNode left = dfs(node.left, set);
        TreeNode right = dfs(node.right, set);
        
        if (left != null && right != null) return node;
        
        return left != null ? left : right;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt 1 vòng cây.
- **Space Complexity:** $O(K + H)$ - Kích thước Set là $K$, call stack đệ quy $H$.

---

## 27. Merge Two Binary Trees (LeetCode 617)
**Đề bài chi tiết:** Cho gốc của hai cây nhị phân `root1` và `root2`. Trộn hai cây này lại với quy tắc: nếu 2 node đè lên nhau, giá trị node mới là tổng của 2 node đó. Nếu một trong hai node là `null`, node không `null` sẽ thay thế tại vị trí đó. Trả về cây mới.

**Phân tích thuật toán:**
Sử dụng đệ quy, hàm sẽ trả về node mới (hoặc tái sử dụng node cũ). Tại mỗi vị trí, nếu một trong hai node `null`, trả về node còn lại. Ngược lại, tạo node mới bằng tổng 2 giá trị và gọi đệ quy gán cho con trái, con phải.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null) return root2;
        if (root2 == null) return root1;
        
        TreeNode merged = new TreeNode(root1.val + root2.val);
        merged.left = mergeTrees(root1.left, root2.left);
        merged.right = mergeTrees(root1.right, root2.right);
        
        return merged;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - N là số node tối thiểu của 1 trong 2 cây (chỉ duyệt những phần giao nhau).
- **Space Complexity:** $O(H)$ - Không gian đệ quy.

---

## 28. Smallest Subtree with all the Deepest Nodes (LeetCode 865)
**Đề bài chi tiết:** Cho root của cây nhị phân, tìm subtree nhỏ nhất có chứa toàn bộ các node ở độ sâu sâu nhất của cây.

**Phân tích thuật toán:**
Hàm đệ quy trả về một object/pair chứa 2 thông tin: độ sâu lớn nhất của subtree bên dưới, và node LCA của những node sâu nhất. Tại node hiện tại:
- Lấy thông tin từ bên trái và bên phải.
- Nếu sâu bằng nhau: `LCA` = node hiện tại, `depth` = depth(trái) + 1.
- Nếu sâu lệch nhau: trả về Pair của bên sâu hơn (tăng depth lên 1).

**Mã nguồn Java:**
```java
class Solution {
    class Result {
        TreeNode node;
        int depth;
        Result(TreeNode node, int depth) {
            this.node = node;
            this.depth = depth;
        }
    }
    
    public TreeNode subtreeWithAllDeepest(TreeNode root) {
        return dfs(root).node;
    }
    
    private Result dfs(TreeNode node) {
        if (node == null) return new Result(null, 0);
        
        Result left = dfs(node.left);
        Result right = dfs(node.right);
        
        if (left.depth == right.depth) {
            return new Result(node, left.depth + 1);
        }
        
        if (left.depth > right.depth) {
            return new Result(left.node, left.depth + 1);
        } else {
            return new Result(right.node, right.depth + 1);
        }
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt từ dưới lên.
- **Space Complexity:** $O(H)$ - Call stack.

---

## 29. Pseudo-Palindromic Paths in a Binary Tree (LeetCode 1457)
**Đề bài chi tiết:** Một cây nhị phân chứa các số từ 1 đến 9. Một đường đi từ gốc tới lá được coi là pseudo-palindromic (giả đối xứng) nếu có ít nhất một hoán vị của các giá trị trên đường đi đó tạo thành chuỗi Palindrome. Đếm số lượng đường đi giả đối xứng.

**Phân tích thuật toán:**
Một dãy số tạo được chuỗi Palindrome nếu có **tối đa 1** con số xuất hiện với số lần lẻ.
Vì chỉ có 9 chữ số (1-9), ta có thể sử dụng Bitmask. Dùng phép XOR: mỗi khi gặp số `x`, ta thực hiện `mask = mask ^ (1 << x)`. Bit 1 sẽ chập lại thành 0 nếu xuất hiện chẵn lần. Cuối cùng, đường đi là giả đối xứng nếu số bit 1 của `mask` là 0 hoặc 1 (tức là `(mask & (mask - 1)) == 0`).

**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int pseudoPalindromicPaths (TreeNode root) {
        dfs(root, 0);
        return count;
    }
    
    private void dfs(TreeNode node, int mask) {
        if (node == null) return;
        
        // Flip bit tương ứng với node.val
        mask ^= (1 << node.val);
        
        if (node.left == null && node.right == null) {
            // Kiểm tra số lượng bit 1 có <= 1 không
            if ((mask & (mask - 1)) == 0) {
                count++;
            }
        } else {
            dfs(node.left, mask);
            dfs(node.right, mask);
        }
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt DFS.
- **Space Complexity:** $O(H)$ - Đệ quy.

---

## 30. Maximum Difference Between Node and Ancestor (LeetCode 1026)
**Đề bài chi tiết:** Cho gốc cây nhị phân, tìm giá trị lớn nhất `V = |A.val - B.val|` sao cho node A là tổ tiên (ancestor) của node B (hoặc ngược lại).

**Phân tích thuật toán:**
Khoảng cách lớn nhất giữa một tổ tiên và con cháu trên cùng một đường đi chính là chênh lệch giữa giá trị Lớn nhất và giá trị Nhỏ nhất trên đường đi đó.
Truyền `max` và `min` theo chiều đi xuống trong quá trình DFS. Khi tới leaf node, độ chênh lệch là `max - min`. Lấy `max - min` lớn nhất của mọi nhánh con.

**Mã nguồn Java:**
```java
class Solution {
    public int maxAncestorDiff(TreeNode root) {
        return dfs(root, root.val, root.val);
    }
    
    private int dfs(TreeNode node, int currentMax, int currentMin) {
        if (node == null) {
            return currentMax - currentMin;
        }
        
        currentMax = Math.max(currentMax, node.val);
        currentMin = Math.min(currentMin, node.val);
        
        int leftDiff = dfs(node.left, currentMax, currentMin);
        int rightDiff = dfs(node.right, currentMax, currentMin);
        
        return Math.max(leftDiff, rightDiff);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt mọi node một lần.
- **Space Complexity:** $O(H)$ - Không gian call stack.
