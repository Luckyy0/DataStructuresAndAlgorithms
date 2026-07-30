# Danh sách bài tập Divide and Conquer on Trees

## 1. Construct Binary Tree from Preorder and Inorder Traversal
**Đề bài chi tiết:**
Cho hai mảng số nguyên `preorder` và `inorder` lần lượt đại diện cho kết quả duyệt tiền thứ tự (preorder traversal) và trung thứ tự (inorder traversal) của một cây nhị phân (Binary Tree). Cấu trúc cây có các giá trị node là duy nhất. Hãy xây dựng (construct) và trả về cây nhị phân đó.

**Phân tích thuật toán:**
- Đặc điểm của `preorder` là phần tử đầu tiên luôn là gốc (Root) của cây.
- Khi tìm được giá trị Root trong mảng `inorder`, ta có thể chia mảng `inorder` thành 2 phần: bên trái Root là tập các nodes của Left Subtree, bên phải Root là tập các nodes của Right Subtree.
- Áp dụng chiến lược Divide and Conquer:
  1. Lấy phần tử đầu trong khoảng hiện tại của `preorder` làm Root.
  2. Tìm vị trí (index) của Root trong `inorder`. (Dùng HashMap để tối ưu thời gian tìm kiếm $O(1)$).
  3. Kích thước của Left Subtree = index - inStart.
  4. Đệ quy xây dựng Left Subtree và Right Subtree bằng cách truyền đúng các khoảng (ranges) của 2 mảng.

**Mã nguồn Java:**
```java
class Solution {
    private Map<Integer, Integer> inMap = new HashMap<>();
    private int preIndex = 0;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        for (int i = 0; i < inorder.length; i++) {
            inMap.put(inorder[i], i);
        }
        return build(preorder, 0, inorder.length - 1);
    }

    private TreeNode build(int[] preorder, int inStart, int inEnd) {
        if (inStart > inEnd) return null;

        int rootVal = preorder[preIndex++];
        TreeNode root = new TreeNode(rootVal);
        int inIndex = inMap.get(rootVal);

        // Divide & Conquer
        root.left = build(preorder, inStart, inIndex - 1);
        root.right = build(preorder, inIndex + 1, inEnd);

        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ với $N$ là số lượng nodes. Việc xây dựng HashMap mất $O(N)$, mỗi node được khởi tạo đúng 1 lần.
- **Space Complexity:** $O(N)$ lưu trữ HashMap và $O(H)$ cho Call Stack (trong đó $H$ là chiều cao cây, worst case là $O(N)$).

## 2. Construct Binary Tree from Inorder and Postorder Traversal
**Đề bài chi tiết:**
Cho hai mảng `inorder` và `postorder` biểu diễn Inorder traversal và Postorder traversal của một cây nhị phân. Các giá trị trong cây là duy nhất. Hãy xây dựng và trả về cây nhị phân đó.

**Phân tích thuật toán:**
- Tương tự như bài trước, nhưng với mảng `postorder`, phần tử **cuối cùng** của khoảng hiện tại sẽ là gốc (Root).
- Ta sẽ duyệt `postorder` ngược từ cuối lên đầu.
- Một lưu ý rất quan trọng khi duyệt ngược `postorder` là ta phải xây dựng **Right Subtree** trước **Left Subtree** (vì trong postorder, right child luôn đứng ngay trước root).

**Mã nguồn Java:**
```java
class Solution {
    private Map<Integer, Integer> inMap = new HashMap<>();
    private int postIndex;

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        postIndex = postorder.length - 1;
        for (int i = 0; i < inorder.length; i++) {
            inMap.put(inorder[i], i);
        }
        return build(postorder, 0, inorder.length - 1);
    }

    private TreeNode build(int[] postorder, int inStart, int inEnd) {
        if (inStart > inEnd) return null;

        int rootVal = postorder[postIndex--];
        TreeNode root = new TreeNode(rootVal);
        int inIndex = inMap.get(rootVal);

        // Chú ý: Phải build Right Subtree trước!
        root.right = build(postorder, inIndex + 1, inEnd);
        root.left = build(postorder, inStart, inIndex - 1);

        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ dùng HashMap.
- **Space Complexity:** $O(N)$ cho HashMap + $O(H)$ Call Stack.

## 3. Convert Sorted Array to Binary Search Tree
**Đề bài chi tiết:**
Cho một mảng các số nguyên `nums` đã được sắp xếp tăng dần, hãy chuyển nó thành một cây tìm kiếm nhị phân cân bằng chiều cao (height-balanced BST). (Một cây cân bằng chiều cao là cây mà độ sâu của 2 subtrees ở mọi node không chênh lệch quá 1).

**Phân tích thuật toán:**
- Mảng đã sắp xếp chính là kết quả của **Inorder Traversal** của một BST.
- Để cây cân bằng, ta luôn chọn phần tử ở **giữa** (mid) của mảng làm Root. 
- Sau đó, mảng sẽ được chia thành hai nửa bằng nhau: nửa trái trở thành Left Subtree, nửa phải trở thành Right Subtree.
- Áp dụng Divide and Conquer để đệ quy xây dựng trên hai nửa của mảng.

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

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì mỗi phần tử được truy cập 1 lần để tạo node.
- **Space Complexity:** $O(\log N)$ (do cây chắc chắn cân bằng nên độ sâu đệ quy là $\log N$).

## 4. Unique Binary Search Trees II
**Đề bài chi tiết:**
Cho số nguyên `n`, hãy trả về tất cả các cấu trúc cây tìm kiếm nhị phân (BSTs) có thể có được tạo ra bằng cách sử dụng các nodes có giá trị từ 1 đến `n`. Trả về kết quả dưới dạng danh sách các Roots của các cây.

**Phân tích thuật toán:**
- Với một khoảng `[start, end]`, bất kỳ số `i` nào trong khoảng này đều có thể làm Root.
- Nếu `i` là Root, thì các giá trị từ `start` đến `i - 1` sẽ hình thành Left Subtree, và các giá trị từ `i + 1` đến `end` hình thành Right Subtree.
- Ta đệ quy để sinh ra tất cả các Left Subtrees có thể có, và tất cả các Right Subtrees có thể có. Sau đó lấy tích Đề-các (Cartesian product) của chúng ghép vào node Root `i`.
- Dùng đệ quy Divide and Conquer.

**Mã nguồn Java:**
```java
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if (n == 0) return new ArrayList<>();
        return build(1, n);
    }
    
    private List<TreeNode> build(int start, int end) {
        List<TreeNode> res = new ArrayList<>();
        if (start > end) {
            res.add(null);
            return res;
        }
        
        for (int i = start; i <= end; i++) {
            List<TreeNode> leftSubtrees = build(start, i - 1);
            List<TreeNode> rightSubtrees = build(i + 1, end);
            
            for (TreeNode left : leftSubtrees) {
                for (TreeNode right : rightSubtrees) {
                    TreeNode root = new TreeNode(i);
                    root.left = left;
                    root.right = right;
                    res.add(root);
                }
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(C_n)$ - số Catalan thứ n. $C_n \approx \frac{4^n}{n\sqrt{n}}$.
- **Space Complexity:** $O(C_n)$ để lưu trữ danh sách các cây sinh ra.

## 5. Maximum Binary Tree
**Đề bài chi tiết:**
Cho một mảng nguyên `nums` không có giá trị trùng lặp. Maximum Binary Tree được xây dựng như sau:
1. Gốc là phần tử lớn nhất trong mảng.
2. Cây con trái (Left Subtree) là cây tối đa được xây dựng từ mảng con bên trái của phần tử lớn nhất.
3. Cây con phải (Right Subtree) là cây tối đa được xây dựng từ mảng con bên phải.
Hãy xây dựng và trả về Maximum Binary Tree.

**Phân tích thuật toán:**
- Bài toán đã mô tả rõ ràng cấu trúc của Divide and Conquer:
  - **Divide**: Tìm max trong mảng hiện tại, chia mảng thành phần bên trái max và phần bên phải max.
  - **Conquer**: Xây dựng left/right subtrees đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }
    
    private TreeNode build(int[] nums, int start, int end) {
        if (start > end) return null;
        
        int maxIndex = start;
        for (int i = start + 1; i <= end; i++) {
            if (nums[i] > nums[maxIndex]) {
                maxIndex = i;
            }
        }
        
        TreeNode root = new TreeNode(nums[maxIndex]);
        root.left = build(nums, start, maxIndex - 1);
        root.right = build(nums, maxIndex + 1, end);
        
        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N^2)$ trong trường hợp xấu nhất (mảng sắp xếp tăng/giảm dần). Trung bình $O(N \log N)$.
- **Space Complexity:** $O(N)$ do độ sâu của Call Stack.

## 6. Lowest Common Ancestor of a Binary Tree
**Đề bài chi tiết:**
Cho một cây nhị phân, hãy tìm Lowest Common Ancestor (LCA) - tổ tiên chung gần nhất của hai nodes `p` và `q` trong cây. Đảm bảo `p` và `q` luôn tồn tại trong cây.

**Phân tích thuật toán:**
- Dùng D&C theo hướng Bottom-up (Postorder).
- Nếu node hiện tại là `p` hoặc `q` hoặc `null`, trả về chính node đó.
- Giải quyết bài toán trên Left Subtree và Right Subtree. Gọi kết quả là `left` và `right`.
- **Combine**: 
  - Nếu `left != null` và `right != null`, tức là `p` và `q` nằm ở 2 nhánh khác nhau của `root`. Vậy `root` chính là LCA.
  - Nếu một bên null, bên kia khác null, truyền kết quả khác null lên trên.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        if (left != null && right != null) {
            return root;
        }
        
        return left != null ? left : right;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ do ta phải thăm tất cả nodes trong trường hợp xấu nhất.
- **Space Complexity:** $O(H)$ với H là chiều cao của cây.

## 7. House Robber III (D&C view)
**Đề bài chi tiết:**
Tên trộm (robber) đang ở một khu phố có sơ đồ dạng Binary Tree. Không thể cướp ở 2 ngôi nhà được liên kết trực tiếp (node cha và node con) trong cùng một đêm. Tính số tiền tối đa cướp được.

**Phân tích thuật toán:**
- Đây là bài toán DP trên Tree giải bằng D&C và State pattern.
- Tại mỗi node, ta có 2 trạng thái: `rob` (cướp node này) hoặc `notRob` (không cướp node này).
- Trả về một mảng `int[] res = new int[2]` trong đó:
  - `res[0]`: max nếu KHÔNG cướp root hiện tại.
  - `res[1]`: max nếu CƯỚP root hiện tại.
- Kết hợp (Combine):
  - Cướp root = `root.val + left[0] + right[0]`
  - Không cướp root = `max(left[0], left[1]) + max(right[0], right[1])`

**Mã nguồn Java:**
```java
class Solution {
    public int rob(TreeNode root) {
        int[] res = robSub(root);
        return Math.max(res[0], res[1]);
    }
    
    private int[] robSub(TreeNode root) {
        if (root == null) return new int[2];
        
        int[] left = robSub(root.left);
        int[] right = robSub(root.right);
        
        int[] res = new int[2];
        // res[0] is notRob
        res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
        // res[1] is rob
        res[1] = root.val + left[0] + right[0];
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì tính một lần từ dưới lên trên.
- **Space Complexity:** $O(H)$ cho call stack.

## 8. All Possible Full Binary Trees
**Đề bài chi tiết:**
Một cây nhị phân đầy đủ (Full Binary Tree - FBT) là cây mà mọi node đều có chính xác 0 hoặc 2 node con. Cho số nguyên `n`, hãy trả về danh sách tất cả các FBT có `n` nodes.

**Phân tích thuật toán:**
- Một FBT luôn có số node **lẻ**. Nếu `n` chẵn, trả về list rỗng.
- Để xây dựng cây có `n` nodes, gốc dùng 1 node, còn lại `n-1` nodes. Ta lặp lại chia `n-1` thành `i` nodes cho bên trái và `n - 1 - i` nodes cho bên phải.
- Gọi đệ quy lấy danh sách lefts, rights rồi ghép lại. Dùng memoization (HashMap) để lưu trạng thái và tránh trùng lặp.

**Mã nguồn Java:**
```java
class Solution {
    private Map<Integer, List<TreeNode>> memo = new HashMap<>();
    
    public List<TreeNode> allPossibleFBT(int n) {
        if (!memo.containsKey(n)) {
            List<TreeNode> res = new ArrayList<>();
            if (n % 2 == 0) {
                // n chẵn không thể là FBT
            } else if (n == 1) {
                res.add(new TreeNode(0));
            } else {
                for (int i = 1; i < n; i += 2) {
                    List<TreeNode> lefts = allPossibleFBT(i);
                    List<TreeNode> rights = allPossibleFBT(n - 1 - i);
                    
                    for (TreeNode l : lefts) {
                        for (TreeNode r : rights) {
                            TreeNode root = new TreeNode(0);
                            root.left = l;
                            root.right = r;
                            res.add(root);
                        }
                    }
                }
            }
            memo.put(n, res);
        }
        return memo.get(n);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(2^N)$ phụ thuộc vào số lượng hình dáng cây (Catalan numbers).
- **Space Complexity:** $O(2^N)$ bộ nhớ để chứa memoization cache và cây.

## 9. Construct Binary Tree from Preorder and Postorder Traversal
**Đề bài chi tiết:**
Xây dựng cây nhị phân từ mảng `preorder` và `postorder`. Có thể có nhiều kết quả hợp lệ, trả về bất kỳ kết quả nào.

**Phân tích thuật toán:**
- `preorder[0]` là root. `preorder[1]` sẽ là root của Left Subtree.
- Ta tìm `preorder[1]` trong `postorder` (sử dụng HashMap). Giả sử nó ở vị trí `index`.
- Khi đó các nodes từ `0` đến `index` trong `postorder` sẽ thuộc Left Subtree. Số lượng phần tử của Left Subtree là `count = index + 1`.
- Dựa vào `count`, ta chia `preorder` và `postorder` để gọi đệ quy.

**Mã nguồn Java:**
```java
class Solution {
    private Map<Integer, Integer> postMap = new HashMap<>();

    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        for (int i = 0; i < postorder.length; i++) {
            postMap.put(postorder[i], i);
        }
        return build(preorder, 0, preorder.length - 1, 0, postorder.length - 1);
    }

    private TreeNode build(int[] pre, int preStart, int preEnd, int postStart, int postEnd) {
        if (preStart > preEnd) return null;
        if (preStart == preEnd) return new TreeNode(pre[preStart]);

        int rootVal = pre[preStart];
        TreeNode root = new TreeNode(rootVal);
        
        int leftRootVal = pre[preStart + 1];
        int postIndex = postMap.get(leftRootVal);
        int leftSubtreeCount = postIndex - postStart + 1;

        root.left = build(pre, preStart + 1, preStart + leftSubtreeCount, postStart, postIndex);
        root.right = build(pre, preStart + leftSubtreeCount + 1, preEnd, postIndex + 1, postEnd - 1);

        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(N)$ cho HashMap và $O(H)$ cho đệ quy.

## 10. Serialize and Deserialize Binary Tree
**Đề bài chi tiết:**
Thiết kế thuật toán để chuyển một cây nhị phân thành một chuỗi (Serialization) và chuyển chuỗi đó ngược lại thành cấu trúc cây nhị phân (Deserialization).

**Phân tích thuật toán:**
- **Serialize**: Sử dụng Preorder Traversal. Nếu node null, ghi lại ký hiệu (ví dụ "#"). Cách nhau bằng dấu phẩy.
- **Deserialize**: Sử dụng D&C (Preorder). Dùng Queue để chứa danh sách giá trị. Lấy phần tử đầu trong queue, nếu là "#", trả về null. Nếu là số, tạo Node, rồi đệ quy gọi tạo left và right.

**Mã nguồn Java:**
```java
public class Codec {
    // Serialize
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        buildString(root, sb);
        return sb.toString();
    }

    private void buildString(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#").append(",");
        } else {
            sb.append(node.val).append(",");
            buildString(node.left, sb);
            buildString(node.right, sb);
        }
    }

    // Deserialize
    public TreeNode deserialize(String data) {
        Queue<String> nodes = new LinkedList<>(Arrays.asList(data.split(",")));
        return buildTree(nodes);
    }
    
    private TreeNode buildTree(Queue<String> nodes) {
        String val = nodes.poll();
        if (val.equals("#")) return null;
        
        TreeNode root = new TreeNode(Integer.parseInt(val));
        root.left = buildTree(nodes);
        root.right = buildTree(nodes);
        return root;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ cho cả hai hàm.
- **Space Complexity:** $O(N)$ cho String/Queue và Stack recursion.

## 11. Path Sum III
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân (Binary Tree) và một số nguyên `targetSum`. Trả về số lượng các đường đi (paths) có tổng các giá trị trên đường đi đó bằng `targetSum`. Đường đi không nhất thiết phải bắt đầu từ gốc (root) hay kết thúc ở lá (leaf), nhưng nó phải đi theo hướng từ trên xuống (từ node cha xuống node con).

**Phân tích thuật toán:**
- Có thể áp dụng D&C (Divide and Conquer) kết hợp Prefix Sum để tối ưu thời gian từ $O(N^2)$ xuống $O(N)$.
- Tại mỗi node, ta duy trì một biến `currentSum` lưu tổng các giá trị từ root đến node hiện tại.
- Để tìm xem có đường đi nào kết thúc tại node hiện tại và có tổng bằng `targetSum` không, ta cần tìm xem trong đường đi từ root đến node hiện tại có tồn tại `currentSum - targetSum` không. Ta dùng một HashMap để lưu các giá trị prefix sum và số lần xuất hiện của chúng.
- Sau khi duyệt qua Left Subtree và Right Subtree, ta cần loại bỏ `currentSum` hiện tại khỏi HashMap (Backtracking) để không ảnh hưởng đến các nhánh khác.

**Mã nguồn Java:**
```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0L, 1);
        return dfs(root, 0L, targetSum, prefixSumMap);
    }

    private int dfs(TreeNode node, long currentSum, int targetSum, Map<Long, Integer> prefixSumMap) {
        if (node == null) {
            return 0;
        }

        currentSum += node.val;
        int count = prefixSumMap.getOrDefault(currentSum - targetSum, 0);

        prefixSumMap.put(currentSum, prefixSumMap.getOrDefault(currentSum, 0) + 1);

        // Divide & Conquer
        count += dfs(node.left, currentSum, targetSum, prefixSumMap);
        count += dfs(node.right, currentSum, targetSum, prefixSumMap);

        // Backtrack
        prefixSumMap.put(currentSum, prefixSumMap.get(currentSum) - 1);

        return count;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì mỗi node được duyệt qua đúng 1 lần.
- **Space Complexity:** $O(N)$ lưu trữ HashMap và Call Stack trong trường hợp xấu nhất (cây lệch).

## 12. Binary Tree Maximum Path Sum
**Đề bài chi tiết:**
Một đường đi (path) trong cây nhị phân là một chuỗi các nodes trong đó mỗi cặp nodes kề nhau có một cạnh nối. Một node chỉ xuất hiện tối đa một lần trong chuỗi. Đường đi không nhất thiết phải đi qua gốc. Cho gốc của một cây nhị phân, hãy tìm đường đi có tổng các giá trị lớn nhất (maximum path sum) và trả về tổng đó.

**Phân tích thuật toán:**
- Sử dụng chiến lược Divide and Conquer với Postorder Traversal.
- Tại mỗi node, ta tính đường đi có tổng lớn nhất xuất phát từ node đó đi xuống nhánh trái hoặc nhánh phải. Nếu tổng đó âm, ta lấy 0 (tức là không chọn nhánh đó).
- Đường đi ngang qua node hiện tại (bao gồm cả node con trái, node hiện tại và node con phải) sẽ có tổng là `node.val + leftMax + rightMax`. Ta dùng biến cục bộ (global) để lưu lại giá trị max này.
- Hàm đệ quy sẽ trả về giá trị đường đi lớn nhất tính từ node hiện tại xuống một trong hai nhánh con (để phục vụ cho node cha), tức là `node.val + max(leftMax, rightMax)`.

**Mã nguồn Java:**
```java
class Solution {
    private int maxSum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        calculateMaxPath(root);
        return maxSum;
    }

    private int calculateMaxPath(TreeNode node) {
        if (node == null) {
            return 0;
        }

        // Lấy max giữa 0 và đường đi nhánh trái/phải để loại bỏ phần âm
        int leftMax = Math.max(0, calculateMaxPath(node.left));
        int rightMax = Math.max(0, calculateMaxPath(node.right));

        // Cập nhật đường đi lớn nhất ngang qua node hiện tại
        maxSum = Math.max(maxSum, node.val + leftMax + rightMax);

        // Trả về đường đi lớn nhất cho node cha
        return node.val + Math.max(leftMax, rightMax);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì ta thăm mỗi node chính xác một lần.
- **Space Complexity:** $O(H)$ với H là chiều cao của cây do Call Stack, worst case là $O(N)$.

## 13. Diameter of Binary Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy tính đường kính (diameter) của cây đó. Đường kính của cây nhị phân là độ dài của đường đi dài nhất giữa hai nodes bất kỳ trong cây. Độ dài này được tính bằng số cạnh giữa chúng. Đường đi này có thể đi qua hoặc không đi qua node gốc.

**Phân tích thuật toán:**
- Sử dụng Divide and Conquer bằng cách tính chiều cao của cây con trái và cây con phải đệ quy.
- Tại một node bất kỳ, đường kính đi qua node đó sẽ bằng chiều cao của cây con trái cộng với chiều cao của cây con phải (`leftHeight + rightHeight`).
- Ta duy trì một biến cục bộ để lưu lại đường kính lớn nhất trong quá trình duyệt.
- Hàm đệ quy trả về chiều cao lớn nhất từ node hiện tại, là `1 + max(leftHeight, rightHeight)`.

**Mã nguồn Java:**
```java
class Solution {
    private int maxDiameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        getHeight(root);
        return maxDiameter;
    }

    private int getHeight(TreeNode node) {
        if (node == null) {
            return 0;
        }

        int leftHeight = getHeight(node.left);
        int rightHeight = getHeight(node.right);

        // Cập nhật đường kính lớn nhất
        maxDiameter = Math.max(maxDiameter, leftHeight + rightHeight);

        // Trả về chiều cao của node hiện tại
        return 1 + Math.max(leftHeight, rightHeight);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ duyệt qua tất cả các nodes một lần.
- **Space Complexity:** $O(H)$ cho Call Stack.

## 14. Count Complete Tree Nodes
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân hoàn chỉnh (Complete Binary Tree), hãy trả về số lượng nodes có trong cây.
Trong cây nhị phân hoàn chỉnh, tất cả các mức (levels) ngoại trừ có thể là mức cuối cùng, đều chứa đầy đủ nodes. Mức cuối cùng được điền từ trái sang phải. Thiết kế thuật toán chạy nhanh hơn $O(N)$.

**Phân tích thuật toán:**
- Dùng D&C kết hợp tính chất của Complete Binary Tree.
- Tính chiều cao nhánh ngoài cùng bên trái (`leftHeight`) và nhánh ngoài cùng bên phải (`rightHeight`).
- Nếu `leftHeight == rightHeight`, thì cây hiện tại là Perfect Binary Tree. Số lượng nodes là $2^h - 1$.
- Nếu `leftHeight != rightHeight`, đệ quy đếm số nodes của Left Subtree và Right Subtree rồi cộng với 1 (node gốc).

**Mã nguồn Java:**
```java
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null) return 0;

        int leftHeight = getLeftHeight(root);
        int rightHeight = getRightHeight(root);

        if (leftHeight == rightHeight) {
            return (1 << leftHeight) - 1; // 2^leftHeight - 1
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
- **Time Complexity:** Mỗi bước tính chiều cao mất $O(\log N)$. Ta gọi đệ quy tối đa $O(\log N)$ lần (vì ít nhất 1 nhánh luôn là Perfect Tree). Tổng thời gian là $O(\log^2 N)$.
- **Space Complexity:** $O(\log N)$ độ sâu của Call Stack.

## 15. Balanced Binary Tree
**Đề bài chi tiết:**
Cho một cây nhị phân, hãy xác định xem cây này có phải là cây cân bằng chiều cao (height-balanced) hay không. Một cây nhị phân được gọi là cân bằng chiều cao nếu chênh lệch chiều cao giữa cây con trái và cây con phải của mọi node không vượt quá 1.

**Phân tích thuật toán:**
- Dùng Divide and Conquer với Postorder Traversal.
- Tại mỗi node, lấy chiều cao của Left Subtree và Right Subtree.
- Nếu một nhánh đã mất cân bằng (trả về -1), ta có thể truyền -1 lên liên tục (Early Stop).
- Nếu cả 2 nhánh đều hợp lệ, kiểm tra chênh lệch chiều cao. Nếu lớn hơn 1, trả về -1 báo hiệu mất cân bằng.
- Nếu độ chênh lệch <= 1, trả về chiều cao hiện tại `1 + max(leftHeight, rightHeight)`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return checkHeight(root) != -1;
    }

    private int checkHeight(TreeNode node) {
        if (node == null) return 0;

        int leftHeight = checkHeight(node.left);
        if (leftHeight == -1) return -1;

        int rightHeight = checkHeight(node.right);
        if (rightHeight == -1) return -1;

        if (Math.abs(leftHeight - rightHeight) > 1) {
            return -1;
        }

        return 1 + Math.max(leftHeight, rightHeight);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ vì mỗi node chỉ được thăm tính toán đúng 1 lần.
- **Space Complexity:** $O(H)$ với H là chiều cao của cây.

## 16. Flatten Binary Tree to Linked List
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy làm phẳng (flatten) cây thành một danh sách liên kết (Linked List). Danh sách liên kết này phải sử dụng cùng cấu trúc `TreeNode` trong đó nhánh `right` trỏ đến node tiếp theo trong danh sách và nhánh `left` luôn là `null`. Thứ tự các nodes trong danh sách phải trùng với kết quả của Preorder Traversal.

**Phân tích thuật toán:**
- Dùng D&C đệ quy theo Postorder (Duyệt Left, Duyệt Right, Xử lý Root).
- Hàm đệ quy làm phẳng Left Subtree và Right Subtree.
- Sau khi Left và Right đã được làm phẳng thành Linked List:
  1. Lưu trữ cây con phải cũ.
  2. Gắn cây con trái vào nhánh phải của node hiện tại. Đặt nhánh trái thành `null`.
  3. Duyệt đến phần tử cuối cùng của nhánh phải mới, rồi gắn cây con phải cũ vào đó.

**Mã nguồn Java:**
```java
class Solution {
    public void flatten(TreeNode root) {
        if (root == null) return;

        // D&C
        flatten(root.left);
        flatten(root.right);

        // Lưu trữ cây phải cũ
        TreeNode rightSubtree = root.right;

        // Gắn cây trái vào nhánh phải, cây trái thành null
        root.right = root.left;
        root.left = null;

        // Tìm node cuối cùng của nhánh phải mới
        TreeNode current = root;
        while (current.right != null) {
            current = current.right;
        }

        // Gắn cây phải cũ vào node cuối
        current.right = rightSubtree;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ (Mặc dù có vòng lặp while nhưng tổng tất cả các lần duyệt qua node chỉ $O(N)$).
- **Space Complexity:** $O(H)$ do Call Stack.

## 17. Delete Node in a BST
**Đề bài chi tiết:**
Cho gốc của một Cây Tìm Kiếm Nhị Phân (BST) và một khoá (key). Hãy xoá node chứa key đó khỏi BST và trả về gốc của BST mới. Yêu cầu cấu trúc BST sau khi xoá vẫn phải hợp lệ.

**Phân tích thuật toán:**
- Divide and Conquer:
  - Nếu `key < root.val`, đệ quy xoá ở Left Subtree.
  - Nếu `key > root.val`, đệ quy xoá ở Right Subtree.
  - Nếu `key == root.val`, ta cần xoá root hiện tại:
    - Nếu không có con trái, trả về con phải.
    - Nếu không có con phải, trả về con trái.
    - Nếu có cả 2 con: Tìm node nhỏ nhất ở Right Subtree (Inorder Successor), thay thế giá trị root bằng giá trị của successor, sau đó xoá successor ở Right Subtree.

**Mã nguồn Java:**
```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;

        if (key < root.val) {
            root.left = deleteNode(root.left, key);
        } else if (key > root.val) {
            root.right = deleteNode(root.right, key);
        } else {
            // Node chỉ có 1 con hoặc không có con
            if (root.left == null) return root.right;
            if (root.right == null) return root.left;

            // Node có 2 con: Tìm successor (node nhỏ nhất bên nhánh phải)
            TreeNode minNode = findMin(root.right);
            root.val = minNode.val;
            // Xoá successor ở nhánh phải
            root.right = deleteNode(root.right, root.val);
        }
        return root;
    }

    private TreeNode findMin(TreeNode node) {
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(H)$ với H là chiều cao của cây ($O(\log N)$ đối với cây cân bằng, $O(N)$ cây lệch).
- **Space Complexity:** $O(H)$ Call Stack.

## 18. Most Frequent Subtree Sum
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy tìm tổng cây con xuất hiện nhiều nhất. Tổng của một cây con được tính bằng tổng các giá trị của toàn bộ nodes trong cây con đó (bao gồm cả node gốc của cây con). Trả về tất cả các tổng cây con có tần suất xuất hiện cao nhất dưới dạng một mảng. Có thể trả về kết quả theo thứ tự bất kỳ.

**Phân tích thuật toán:**
- Dùng D&C (Postorder Traversal) để tính tổng của cây con tại mỗi node.
- Tại một node, tổng của nó = `node.val + sum(left) + sum(right)`.
- Dùng HashMap để lưu trữ tần suất xuất hiện của mỗi giá trị tổng.
- Duy trì biến `maxCount` để theo dõi tần suất lớn nhất. Cuối cùng, lấy các tổng có tần suất bằng `maxCount` để đưa vào mảng kết quả.

**Mã nguồn Java:**
```java
class Solution {
    private Map<Integer, Integer> countMap = new HashMap<>();
    private int maxCount = 0;

    public int[] findFrequentTreeSum(TreeNode root) {
        getTreeSum(root);

        List<Integer> res = new ArrayList<>();
        for (Map.Entry<Integer, Integer> entry : countMap.entrySet()) {
            if (entry.getValue() == maxCount) {
                res.add(entry.getKey());
            }
        }

        int[] result = new int[res.size()];
        for (int i = 0; i < res.size(); i++) {
            result[i] = res.get(i);
        }
        return result;
    }

    private int getTreeSum(TreeNode node) {
        if (node == null) return 0;

        int leftSum = getTreeSum(node.left);
        int rightSum = getTreeSum(node.right);

        int totalSum = node.val + leftSum + rightSum;
        int count = countMap.getOrDefault(totalSum, 0) + 1;
        countMap.put(totalSum, count);

        maxCount = Math.max(maxCount, count);

        return totalSum;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - Duyệt qua tất cả nodes. Thêm vào HashMap $O(1)$.
- **Space Complexity:** $O(N)$ - Lưu HashMap và Call Stack.

## 19. Find Duplicate Subtrees
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, trả về tất cả các cây con trùng lặp (duplicate subtrees). Đối với mỗi loại cây con lặp lại, bạn chỉ cần trả về node gốc của một trong số chúng. Hai cây con được coi là giống nhau nếu cấu trúc của chúng hoàn toàn giống nhau và các giá trị của các nodes ở cùng vị trí cũng bằng nhau.

**Phân tích thuật toán:**
- Áp dụng kỹ thuật Serialization (tuần tự hoá).
- Ta chuyển đổi cấu trúc cây con thành chuỗi (String) dạng `(left_subtree)root.val(right_subtree)`.
- Sử dụng Postorder Traversal, đệ quy tạo chuỗi đại diện cho nhánh trái và nhánh phải, rồi gộp lại với node hiện tại.
- Lưu chuỗi vào HashMap để đếm tần suất xuất hiện. Nếu một chuỗi xuất hiện lần thứ 2, ta thêm node gốc tương ứng vào danh sách kết quả.

**Mã nguồn Java:**
```java
class Solution {
    private Map<String, Integer> serialMap = new HashMap<>();
    private List<TreeNode> res = new ArrayList<>();

    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        serialize(root);
        return res;
    }

    private String serialize(TreeNode node) {
        if (node == null) return "#";

        String leftSerial = serialize(node.left);
        String rightSerial = serialize(node.right);

        String currentSerial = node.val + "," + leftSerial + "," + rightSerial;

        int count = serialMap.getOrDefault(currentSerial, 0);
        if (count == 1) {
            res.add(node);
        }
        serialMap.put(currentSerial, count + 1);

        return currentSerial;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N^2)$ vì mỗi lần nối chuỗi (String concatenation) ở mỗi node có độ dài tổng cộng lên đến $O(N)$.
- **Space Complexity:** $O(N^2)$ để lưu toàn bộ các chuỗi trong HashMap.

## 20. Merge Two Binary Trees
**Đề bài chi tiết:**
Cho gốc của hai cây nhị phân `root1` và `root2`. Hãy hợp nhất chúng thành một cây mới. Quy tắc hợp nhất: nếu cả hai nodes ở cùng một vị trí đều tồn tại, hãy cộng giá trị của chúng làm node mới. Nếu một node là `null`, sử dụng node còn lại làm gốc tại đó.

**Phân tích thuật toán:**
- Sử dụng Divide and Conquer, duyệt qua cả hai cây đồng thời.
- Nếu cả 2 nodes đều `null`, trả về `null`.
- Nếu 1 node `null`, trả về node kia.
- Nếu cả 2 nodes đều tồn tại, tạo một node mới có giá trị bằng `root1.val + root2.val`.
- Đệ quy hợp nhất hai nhánh trái cho nhánh trái của node mới, và tương tự cho nhánh phải.

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
- **Time Complexity:** $O(M)$ với $M$ là số lượng nodes nhỏ hơn giữa hai cây (vì khi 1 node null, ta không duyệt tiếp nữa mà chỉ trả về nhánh còn lại).
- **Space Complexity:** $O(H)$ với H là chiều cao nhỏ hơn giữa hai cây để phục vụ Call Stack.

## 21. Maximum Depth of Binary Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy tìm độ sâu lớn nhất (maximum depth) của nó. Độ sâu lớn nhất là số lượng nodes trên đường đi dài nhất từ gốc đến node lá xa nhất.

**Phân tích thuật toán:**
- Dùng Divide and Conquer.
- Độ sâu lớn nhất của cây tại một node bằng 1 cộng với giá trị lớn nhất giữa độ sâu của nhánh trái và nhánh phải.
- Nếu node hiện tại là `null`, trả về 0.

**Mã nguồn Java:**
```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        
        return 1 + Math.max(leftDepth, rightDepth);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ - phải thăm mọi node.
- **Space Complexity:** $O(H)$ cho Call Stack (trong trường hợp xấu nhất là $O(N)$).

## 22. Minimum Depth of Binary Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy tìm độ sâu nhỏ nhất (minimum depth) của nó. Độ sâu nhỏ nhất là số lượng nodes trên đường đi ngắn nhất từ gốc đến một node lá gần nhất. Lưu ý: Một node lá là node không có bất kỳ con nào.

**Phân tích thuật toán:**
- Dùng Divide and Conquer.
- Nếu `root == null`, trả về 0.
- Cần chú ý Edge Case: Nếu một nhánh bị `null`, ta không được lấy min giữa 0 và nhánh còn lại (vì như thế kết quả sẽ là 1, nhưng node đó chưa chắc là lá).
- Do đó, nếu `left == null`, ta phải đi theo nhánh `right`. Nếu `right == null`, ta đi theo nhánh `left`.
- Nếu cả hai nhánh đều tồn tại, ta lấy `1 + min(leftDepth, rightDepth)`.

**Mã nguồn Java:**
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        
        if (root.left == null) {
            return 1 + minDepth(root.right);
        }
        if (root.right == null) {
            return 1 + minDepth(root.left);
        }
        
        return 1 + Math.min(minDepth(root.left), minDepth(root.right));
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$.
- **Space Complexity:** $O(H)$ cho Call Stack.

## 23. Same Tree
**Đề bài chi tiết:**
Cho gốc của hai cây nhị phân `p` và `q`. Hãy viết một hàm kiểm tra xem chúng có giống hệt nhau hay không. Hai cây nhị phân được coi là giống nhau nếu chúng có cấu trúc hệt nhau và các nodes ở cùng vị trí có giá trị bằng nhau.

**Phân tích thuật toán:**
- Dùng Divide and Conquer duyệt đồng thời trên cả hai cây.
- Nếu cả hai node đều `null`, trả về `true`.
- Nếu chỉ một trong hai node là `null`, trả về `false`.
- Nếu giá trị của hai nodes khác nhau, trả về `false`.
- Gọi đệ quy để kiểm tra cây con trái và cây con phải. Hai cây là `Same Tree` khi và chỉ khi nhánh trái của chúng giống nhau VÀ nhánh phải của chúng giống nhau.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        if (p.val != q.val) return false;
        
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(\min(N, M))$ với $N, M$ là số lượng nodes của 2 cây.
- **Space Complexity:** $O(\min(H_1, H_2))$ với $H_1, H_2$ là chiều cao của 2 cây.

## 24. Symmetric Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy kiểm tra xem cây đó có tự đối xứng (trục đối xứng ở giữa) hay không (hay còn gọi là soi gương của chính nó).

**Phân tích thuật toán:**
- Đây là một biến thể của bài Same Tree. 
- Để cây tự đối xứng, thì nhánh con trái phải là ảnh ảo (mirror) của nhánh con phải.
- Ta xây dựng một hàm đệ quy phụ nhận 2 nodes: `t1` và `t2`.
- Hàm trả về `true` nếu: `t1.val == t2.val`, VÀ nhánh trái của `t1` đối xứng với nhánh phải của `t2`, VÀ nhánh phải của `t1` đối xứng với nhánh trái của `t2`.

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

## 25. Sum Root to Leaf Numbers
**Đề bài chi tiết:**
Bạn được cung cấp gốc của một cây nhị phân chỉ chứa các chữ số từ 0 đến 9. Mỗi đường đi từ gốc đến lá đại diện cho một số. Ví dụ, đường đi `1 -> 2 -> 3` đại diện cho số `123`. Hãy trả về tổng của tất cả các số được tạo bởi các đường đi từ gốc đến lá.

**Phân tích thuật toán:**
- Sử dụng Divide and Conquer kết hợp Top-down (truyền trạng thái từ trên xuống).
- Tại mỗi node, ta tính giá trị hiện tại bằng cách `currentSum = currentSum * 10 + node.val`.
- Nếu node là lá, trả về `currentSum`.
- Nếu không phải lá, tổng sẽ bằng kết quả lấy từ nhánh trái cộng với kết quả lấy từ nhánh phải.

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
- **Time Complexity:** $O(N)$ - Thăm mọi node 1 lần.
- **Space Complexity:** $O(H)$ cho Call Stack.

## 26. Distribute Coins in Binary Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân với `N` nodes, mỗi node chứa một số lượng đồng xu (coins). Tổng số lượng đồng xu trên toàn bộ cây là `N`. Trong một lượt di chuyển, ta có thể chuyển 1 đồng xu từ một node đến một node cha hoặc một node con liền kề của nó. Trả về số lượng lượt di chuyển tối thiểu để làm cho mỗi node trong cây đều có chính xác 1 đồng xu.

**Phân tích thuật toán:**
- Dùng D&C (Postorder). Tại mỗi node, ta tính toán số dư đồng xu mà cây con tại node đó có (thừa hoặc thiếu).
- Nếu một cây con có số dư dương, nó cần phải di chuyển số đồng xu thừa đó lên cha của nó. Nếu số dư âm, nó cần cha của nó cấp bù số đồng xu còn thiếu.
- Số dư của cây con tại một node bằng: `số xu của node đó + số dư nhánh trái + số dư nhánh phải - 1 (giữ lại 1 xu cho chính nó)`.
- Số lượng lượt di chuyển đi qua một node sẽ chính bằng `|số dư nhánh trái| + |số dư nhánh phải|`. Ta duy trì một biến cục bộ để cộng dồn các lượt di chuyển này.

**Mã nguồn Java:**
```java
class Solution {
    private int moves = 0;

    public int distributeCoins(TreeNode root) {
        dfs(root);
        return moves;
    }
    
    private int dfs(TreeNode node) {
        if (node == null) return 0;
        
        int leftBalance = dfs(node.left);
        int rightBalance = dfs(node.right);
        
        moves += Math.abs(leftBalance) + Math.abs(rightBalance);
        
        return node.val + leftBalance + rightBalance - 1;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

## 27. Longest Univalue Path
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy tìm độ dài của đường đi dài nhất trong đó mỗi node trên đường đi đều có cùng một giá trị. Đường đi có thể qua hoặc không qua node gốc. Độ dài của đường đi giữa hai nodes được đại diện bằng số lượng cạnh nối giữa chúng.

**Phân tích thuật toán:**
- Dùng D&C (Postorder) tương tự bài Maximum Path Sum và Diameter of Binary Tree.
- Hàm đệ quy trả về độ dài đường đi dài nhất xuất phát từ `node` đi xuống con của nó, sao cho tất cả có cùng giá trị với `node`.
- Nếu con trái có cùng giá trị với `node`, nhánh trái có thể kéo dài thêm `1 + left_result`. Nếu không, nhánh trái coi như bị đứt đoạn, giá trị tại node đó cho nhánh trái = 0.
- Nếu con phải có cùng giá trị, nhánh phải kéo dài `1 + right_result`. Nếu không = 0.
- Đường đi ngang qua `node` sẽ có độ dài `leftLength + rightLength`. Cập nhật max toàn cục. Trả về `max(leftLength, rightLength)` lên cho cha.

**Mã nguồn Java:**
```java
class Solution {
    private int maxLength = 0;

    public int longestUnivaluePath(TreeNode root) {
        dfs(root);
        return maxLength;
    }
    
    private int dfs(TreeNode node) {
        if (node == null) return 0;
        
        int left = dfs(node.left);
        int right = dfs(node.right);
        
        int leftLength = 0;
        int rightLength = 0;
        
        if (node.left != null && node.left.val == node.val) {
            leftLength = left + 1;
        }
        if (node.right != null && node.right.val == node.val) {
            rightLength = right + 1;
        }
        
        maxLength = Math.max(maxLength, leftLength + rightLength);
        
        return Math.max(leftLength, rightLength);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

## 28. Largest BST Subtree
**Đề bài chi tiết:**
Cho một cây nhị phân, hãy tìm cây con lớn nhất (có nhiều nodes nhất) và là một Cây Tìm Kiếm Nhị Phân (BST). Trả về số lượng nodes của cây con đó.

**Phân tích thuật toán:**
- Ta áp dụng Divide and Conquer với State Pattern. Tại mỗi node, ta cần biết các thông tin sau từ con của nó để xác định xem nó có tạo thành BST hay không:
  1. Kích thước (size) của cây con.
  2. Giá trị nhỏ nhất trong cây con (minVal).
  3. Giá trị lớn nhất trong cây con (maxVal).
- Một cây con có gốc tại `node` là BST nếu: Nhánh trái là BST, nhánh phải là BST, và `maxVal(left) < node.val < minVal(right)`.
- Nếu thoả mãn, cập nhật kích thước lớn nhất. Trả về `[size_left + size_right + 1, min(node, minVal_left), max(node, maxVal_right)]`.
- Nếu không phải BST, trả về flag đánh dấu không hợp lệ (ví dụ: `[-1, 0, 0]`).

**Mã nguồn Java:**
```java
class Solution {
    private int maxBSTSize = 0;

    public int largestBSTSubtree(TreeNode root) {
        dfs(root);
        return maxBSTSize;
    }
    
    // Trả về mảng int[]: {isBST (1=true, 0=false), size, minVal, maxVal}
    private int[] dfs(TreeNode node) {
        if (node == null) {
            return new int[]{1, 0, Integer.MAX_VALUE, Integer.MIN_VALUE};
        }
        
        int[] left = dfs(node.left);
        int[] right = dfs(node.right);
        
        if (left[0] == 1 && right[0] == 1 && node.val > left[3] && node.val < right[2]) {
            int size = left[1] + right[1] + 1;
            maxBSTSize = Math.max(maxBSTSize, size);
            
            int minVal = Math.min(node.val, left[2]);
            int maxVal = Math.max(node.val, right[3]);
            
            return new int[]{1, size, minVal, maxVal};
        }
        
        return new int[]{0, 0, 0, 0};
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$
- **Space Complexity:** $O(H)$

## 29. Flip Equivalent Binary Trees
**Đề bài chi tiết:**
Một cây nhị phân $X$ được gọi là tương đương qua phép lật (flip equivalent) với cây nhị phân $Y$ nếu và chỉ nếu ta có thể biến $X$ thành $Y$ bằng cách chọn một số nodes và hoán đổi vị trí của cây con trái và cây con phải của chúng. Viết hàm kiểm tra xem hai cây `root1` và `root2` có lật tương đương hay không.

**Phân tích thuật toán:**
- Dùng D&C đệ quy so sánh hai cây.
- Hai cây là Flip Equivalent nếu:
  1. Chúng cùng `null`.
  2. Chúng cùng không `null` và có cùng giá trị `val`.
  3. Xảy ra một trong hai trường hợp sau với các nhánh con của chúng:
     - **Không lật (No Flip):** `left` của `root1` tương đương `left` của `root2` VÀ `right` tương đương `right`.
     - **Có lật (Flip):** `left` của `root1` tương đương `right` của `root2` VÀ `right` của `root1` tương đương `left` của `root2`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return true;
        if (root1 == null || root2 == null) return false;
        if (root1.val != root2.val) return false;
        
        boolean noFlip = flipEquiv(root1.left, root2.left) && flipEquiv(root1.right, root2.right);
        boolean flip = flipEquiv(root1.left, root2.right) && flipEquiv(root1.right, root2.left);
        
        return noFlip || flip;
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(\min(N_1, N_2))$. Mặc dù có tới 4 cuộc gọi đệ quy, nhưng do tất cả các nodes là duy nhất trong mỗi cây (theo context thông thường của bài toán), số trạng thái hợp lệ để gọi đệ quy sâu hơn là bị giới hạn. Do đó thời gian chạy thực tế trung bình là tuyến tính.
- **Space Complexity:** $O(\min(H_1, H_2))$.

## 30. Validate Binary Search Tree
**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, hãy xác định xem nó có phải là một Cây Tìm Kiếm Nhị Phân (BST) hợp lệ hay không.
BST hợp lệ là cây thỏa mãn:
- Nhánh con trái của một node chỉ chứa các nodes có giá trị **nhỏ hơn ngặt** giá trị của node đó.
- Nhánh con phải của một node chỉ chứa các nodes có giá trị **lớn hơn ngặt** giá trị của node đó.
- Cả hai nhánh trái và phải đều phải là BST hợp lệ.

**Phân tích thuật toán:**
- Thay vì chỉ kiểm tra node con trực tiếp (như vậy có thể sai nếu node cháu lọt ra ngoài khoảng hợp lệ), ta cần truyền các ranh giới `(min, max)` xuống cho mỗi node.
- Tại gốc, khoảng hợp lệ là $(-\infty, +\infty)$.
- Khi đi sang nhánh trái của node có giá trị `V`, khoảng hợp lệ được cập nhật giới hạn trên là `V` (tức là `(min, V)`).
- Khi đi sang nhánh phải, giới hạn dưới là `V` (tức là `(V, max)`).
- Nếu tại node bất kỳ giá trị không nằm trong `(min, max)`, trả về `false`.
- Chú ý xử lý kiểu dữ liệu, nên dùng `Long` để biểu diễn $-\infty$ và $+\infty$ nhằm tránh tràn số nguyên `Integer`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return validate(root, null, null);
    }
    
    private boolean validate(TreeNode node, Integer low, Integer high) {
        if (node == null) return true;
        
        // Kiểm tra điều kiện giới hạn
        if ((low != null && node.val <= low) || (high != null && node.val >= high)) {
            return false;
        }
        
        // Divide and Conquer
        return validate(node.left, low, node.val) && validate(node.right, node.val, high);
    }
}
```

**Độ phức tạp:**
- **Time Complexity:** $O(N)$ 
- **Space Complexity:** $O(H)$ 
