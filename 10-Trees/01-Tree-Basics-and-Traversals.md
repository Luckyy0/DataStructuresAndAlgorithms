# Binary Tree Traversals

## 1. Giới thiệu (Introduction)
Duyệt cây (Tree Traversal) là quá trình đi qua tất cả các node trong cây theo một thứ tự cụ thể. Khác với cấu trúc dữ liệu tuyến tính (như mảng hoặc danh sách liên kết) chỉ có một cách duyệt duy nhất, cây nhị phân có thể được duyệt qua theo nhiều cách khác nhau, chia làm hai loại chính: Depth First Search (DFS) và Breadth First Search (BFS).

## 2. Các khái niệm cơ bản về cây (Basic Concepts of Trees)
- **Node**: Thành phần cơ bản của cây chứa dữ liệu và con trỏ đến các con.
- **Root**: Node gốc của cây.
- **Leaf**: Node không có con (cả trái và phải đều `null`).
- **Depth/Height**: Độ sâu hoặc độ cao của cây.
Trong Java, một TreeNode thường được định nghĩa như sau:
```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

## 3. Depth First Search (DFS) Tổng quan (DFS Overview)
DFS trên cây đi sâu xuống tận cùng của một nhánh trước khi quay lui (backtrack). Có ba chiến lược duyệt DFS chính dựa trên thời điểm ta "thăm" (visit) node gốc so với các node con của nó:
- **Preorder**: Node -> Trái -> Phải
- **Inorder**: Trái -> Node -> Phải
- **Postorder**: Trái -> Phải -> Node

## 4. DFS: Preorder Traversal (Tiền thứ tự)
- **Thứ tự**: Xử lý node hiện tại, sau đó duyệt nhánh trái, rồi duyệt nhánh phải.
- **Ứng dụng**: Thường được dùng để tạo bản sao (copy) của cây hoặc lấy tiền tố (prefix) của một biểu thức.

## 5. DFS: Inorder Traversal (Trung thứ tự)
- **Thứ tự**: Duyệt nhánh trái, xử lý node hiện tại, sau đó duyệt nhánh phải.
- **Ứng dụng**: Đối với Binary Search Tree (BST), Inorder Traversal sẽ duyệt và lấy ra các giá trị theo thứ tự tăng dần (sorted order).

## 6. DFS: Postorder Traversal (Hậu thứ tự)
- **Thứ tự**: Duyệt nhánh trái, duyệt nhánh phải, cuối cùng mới xử lý node hiện tại.
- **Ứng dụng**: Thường được dùng để xóa cây (xóa các con trước khi xóa node cha), hoặc tính toán không gian bộ nhớ của cây.

## 7. Breadth First Search (BFS) Tổng quan (BFS Overview)
BFS tiếp cận cây theo từng mức (level). Nó sẽ duyệt tất cả các node ở độ sâu $d$ trước khi chuyển sang các node ở độ sâu $d+1$. BFS thường sử dụng Queue để lưu vết các node sẽ được duyệt tiếp theo.

## 8. BFS: Level Order Traversal (Duyệt theo mức)
- **Thứ tự**: Từ trên xuống dưới, từ trái sang phải tại mỗi mức.
- **Ứng dụng**: Dùng để tìm đường đi ngắn nhất (shortest path) trong đồ thị vô quyền, hoặc khi cần in cây theo từng tầng.

## 9. Cài đặt bằng Đệ quy (Recursive Implementations)
Đệ quy là cách tiếp cận tự nhiên nhất cho DFS vì cây nhị phân là cấu trúc đệ quy (mỗi cây con cũng là một cây nhị phân).
```java
// Inorder Example
public void inorder(TreeNode root, List<Integer> res) {
    if (root == null) return;
    inorder(root.left, res);
    res.add(root.val);
    inorder(root.right, res);
}
```

## 10. Cài đặt lặp bằng Stack/Queue (Iterative Implementations)
- **DFS lặp**: Sử dụng `Stack` để mô phỏng lại Call Stack của hệ điều hành.
- **BFS lặp**: Sử dụng `Queue` (như `LinkedList` hoặc `ArrayDeque` trong Java).

## 11. Morris Traversal (O(1) Space Complexity)
Morris Traversal là một thuật toán thông minh cho phép duyệt cây Inorder/Preorder mà không sử dụng Stack hoặc Đệ quy (tiết kiệm không gian bộ nhớ thành $O(1)$). Thuật toán này sử dụng các con trỏ `right` của node lá ở nhánh con trái để tạo một đường dẫn tạm thời (thread) quay lại node hiện tại.

## 12. Phân tích độ phức tạp (Time and Space Complexity)
- **Time Complexity**: $O(N)$ cho tất cả các loại Traversal vì mỗi node đều được thăm chính xác một hoặc hai lần.
- **Space Complexity**:
  - Recursive DFS: $O(H)$ với $H$ là chiều cao của cây. Xấu nhất $O(N)$, tốt nhất $O(\log N)$.
  - Iterative DFS: $O(H)$ không gian bộ nhớ phụ trợ (Stack).
  - Iterative BFS: $O(W)$ với $W$ là chiều rộng tối đa của cây (tối đa $\lceil N/2 \rceil$ node). Xấu nhất $O(N)$.
  - Morris Traversal: $O(1)$ không gian (bỏ qua không gian lưu trữ kết quả đầu ra).

## 13. So sánh Đệ quy và Vòng lặp (Recursive vs Iterative)
- **Đệ quy**: Code ngắn gọn, dễ đọc, tự nhiên. Tuy nhiên có rủi ro `StackOverflowError` với những cây cực sâu.
- **Vòng lặp (Iterative)**: Phức tạp hơn để viết đúng (đặc biệt là Postorder), nhưng an toàn hơn về mặt bộ nhớ Call Stack và có thể dễ dàng can thiệp để ngắt quãng quá trình duyệt.

## 14. 20 Lỗi thường gặp (20 Common Bugs)
1. Quên base case `if (root == null) return;` trong đệ quy.
2. Hoán đổi thứ tự gọi đệ quy trái/phải làm sai lệch thứ tự duyệt.
3. Trong Level Order BFS, thêm giá trị `null` vào Queue khiến sinh ra `NullPointerException`.
4. Quên dùng biến `size` để lặp qua chính xác số lượng node của level hiện tại trong BFS.
5. Pop sai node từ Stack trong thuật toán Postorder lặp bằng 1 Stack.
6. Push node trái và phải sai thứ tự vào Stack trong Preorder lặp (Preorder lặp cần push node PHẢI trước, rồi mới tới TRÁI).
7. Gặp lặp vô hạn (Infinite Loop) trong Morris Traversal do quên phục hồi cây bằng cách xóa tham chiếu rác (thread links).
8. Sửa đổi cấu trúc cây vô ý (mutate) trong khi duyệt.
9. Quên kiểm tra `node.left != null` trước khi thêm vào Queue.
10. Lỗi `StackOverflowError` trong đệ quy với cây sâu lệch (skewed tree).
11. Morris Traversal gán nhầm `curr = curr.right` thay vì `curr = curr.left` ở bước đi xuống trái.
12. Iterative Inorder: Quên pop node khỏi Stack sau khi di chuyển đến lá cuối cùng bên trái.
13. Truy cập trường thuộc tính như `node.left.val` mà chưa xác nhận `node.left` không `null`.
14. Quản lý trạng thái visited sai trong Postorder lặp (cần biết đã duyệt nhánh phải chưa).
15. Không khởi tạo kết quả, gán List/Array là null rồi gọi `add()`.
16. Nhầm lẫn giữa ArrayDeque và PriorityQueue khi cài đặt Queue bình thường cho BFS.
17. Dùng Iterator nhưng quên xử lý ConcurrentModificationException nếu nhiều thread.
18. Không làm mới bộ nhớ (clear Data structures) giữa nhiều test cases chạy chung class.
19. Trả về tham chiếu nội bộ thay vì bản sao (clone) khi được yêu cầu.
20. Tràn số Integer (Overflow) khi tính toán giá trị của node trong quá trình duyệt.

## 15. 30 Trường hợp góc (30 Edge Cases)
1. Cây hoàn toàn rỗng (`root == null`).
2. Cây chỉ có 1 node (chỉ gốc).
3. Cây lệch trái hoàn toàn (Left skewed tree).
4. Cây lệch phải hoàn toàn (Right skewed tree).
5. Cây nhị phân hoàn hảo (Perfect binary tree).
6. Cây nhị phân đầy đủ (Full binary tree).
7. Cây nhị phân hoàn chỉnh (Complete binary tree).
8. Cây cực lớn gây giới hạn memory.
9. Cây rất sâu gây Stack Overflow (độ sâu $10^5$).
10. Tất cả node có giá trị giống hệt nhau.
11. Cây có giá trị node âm dương xen kẽ.
12. Các node nằm rải rác tạo ra nhiều khoảng trống.
13. Node có con trái nhưng con phải `null`.
14. Node có con phải nhưng con trái `null`.
15. Cây hình Zig-zag (trái - phải - trái - phải).
16. Các giá trị node trùng lặp rải rác nhiều nơi.
17. Các node có giá trị lớn nhất theo kiểu `Integer.MAX_VALUE`.
18. Có vòng lặp đồ thị vô tình (không còn là cây nhị phân chuẩn).
19. BFS tại level có rất nhiều nodes vượt mức cấp phát mảng.
20. Duyệt cây để construct mà input array có chứa yếu tố trùng lặp (không hợp lệ cho việc phân định).
21. Preorder hoặc Inorder array đầu vào bị rỗng.
22. Độ cao là $2^{10}$ nhưng số node siêu thưa thớt.
23. Chuỗi Serialization của cây cực sâu gây lỗi HeapSpace.
24. Quá trình Traversal phải ngắt giữa chừng theo yêu cầu.
25. Mất kết nối tham chiếu (Garbage collection issue).
26. Mảng nhập để xây cây nhưng số lượng phần tử sai lệch độ dài.
27. Sử dụng thuật toán Morris trên cây dạng Read-Only memory (vì Morris có tạm thời đổi thuộc tính).
28. Tính tổng nhưng tràn kiểu trả về (cần kiểu long).
29. Cây quá rộng ở chiều ngang (BFS chiếm quá nhiều RAM).
30. Tìm LCA khi hai node cùng nằm ở một nhánh lệch phải.

## 16. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Hãy trình bày sự khác biệt giữa DFS và BFS trên cây.
2. Viết thuật toán Inorder Traversal mà không dùng đệ quy.
3. Làm thế nào để thực hiện Morris Traversal với không gian $O(1)$?
4. Trình bày cách xây dựng (construct) cây từ chuỗi Preorder và Inorder.
5. In ra các node ở Right Side View của cây nhị phân.
6. Vertical Order Traversal có độ phức tạp thời gian là bao nhiêu?
7. Level Order Traversal theo kiểu Zigzag (Spiral) được thực hiện như thế nào?
8. Nối các con trỏ `next` trong các node của mức tương ứng (Populating Next Right Pointers).
9. Tìm Lowest Common Ancestor (LCA) của 2 node trong cây.
10. Serialize và Deserialize một cây nhị phân sao cho tối ưu.
11. Làm thế nào kiểm tra một cây có phải BST hợp lệ hay không sử dụng Inorder.
12. Tìm K-th Smallest Element trong BST.
13. Thuật toán Invert Binary Tree.
14. Tại sao người ta lại sử dụng Postorder để xóa/giải phóng cây trong C++?
15. Path Sum: Kiểm tra xem có đường đi từ root đến leaf nào có tổng bằng K.
16. Tìm độ dài của đường kính cây nhị phân (Diameter of Binary Tree).
17. Khôi phục một BST bị hoán vị nhầm 2 node (Recover BST).
18. Boundary Traversal: Duyệt cây theo biên.
19. Chuyển đổi Binary Tree sang Doubly Linked List in-place.
20. Flatten Binary Tree sang một cấu trúc giống LinkedList (preorder).

## 17. Ứng dụng thực tế (Real-world Applications)
- **Preorder**: Sao chép cây, Parser cho biểu thức toán học dạng tiền tố.
- **Inorder**: Sắp xếp trong BST, Parser cho biểu thức trung tố.
- **Postorder**: Xóa cây, thu dọn rác bộ nhớ (Garbage Collection), phân tích cú pháp dạng hậu tố.
- **BFS (Level Order)**: Thuật toán quét và load dữ liệu theo tầng, mạng lưới routing ngang hàng.

## 18. Kỹ thuật tối ưu hóa (Optimization Techniques)
- Tránh cấp phát bộ nhớ thừa trong quá trình duyệt vòng lặp.
- Sử dụng ArrayDeque thay cho LinkedList để làm Queue trong Java.
- Áp dụng Morris Traversal khi vấn đề hạn chế ngặt nghèo về không gian bộ nhớ.

## 19. Mẫu code chuẩn (Standard Templates)
Mẫu cài đặt DFS tổng quát (Java):
```java
// Cấu trúc đệ quy chuẩn
public void dfs(TreeNode root) {
    if (root == null) return;
    // Xử lý logic Preorder
    dfs(root.left);
    // Xử lý logic Inorder
    dfs(root.right);
    // Xử lý logic Postorder
}
```

## 20. Trực quan hóa (Algorithm Visualization)
Trong quá trình học DFS/BFS, hãy thử vẽ ra mặt phẳng ngang (level) hoặc trục dọc (call stack) để tưởng tượng con trỏ di chuyển qua lại giữa Root, Left và Right như thế nào.

## 21. Tích hợp với cấu trúc dữ liệu khác (Integration with other DS)
Traversal thường được kết hợp với Hashing (HashMap) để lưu vết chiều ngang hoặc tọa độ dọc (như Vertical Traversal) hoặc kết hợp với Priority Queue.

## 22. Mở rộng cho N-ary Tree (N-ary Tree Traversal)
Với N-ary Tree (Cây có nhiều con), `Inorder` thường ít phổ biến và khó định nghĩa rõ ràng, trong khi `Preorder` và `Postorder`, `Level order` vẫn hoạt động tốt bằng cách duyệt vòng lặp qua mảng/danh sách các `children`.

## 23. Bài tập thực hành (Self-assessment Exercises)
Cố gắng tự tay viết toàn bộ các cài đặt DFS/BFS đệ quy và không đệ quy ra giấy. Đặc biệt là thuật toán Postorder sử dụng lặp với một Stack duy nhất (sử dụng biến `prev` pointer).

## 24. Tổng kết (Conclusion)
Duyệt cây nhị phân (Binary Tree Traversals) là tiền đề cho hàng loạt những thuật toán và kiến trúc dữ liệu nâng cao khác. Bạn cần hiểu vững về thứ tự duyệt, cách viết đệ quy cũng như lặp để tự tin vượt qua mọi bài phỏng vấn.
