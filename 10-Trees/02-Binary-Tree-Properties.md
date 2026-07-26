# Binary Tree Properties (Các tính chất của cây nhị phân)

## 1. Giới thiệu (Introduction)
Trong khoa học máy tính, **Binary Tree** (Cây nhị phân) là một cấu trúc dữ liệu nền tảng. Khi làm việc với cây nhị phân, việc hiểu rõ các **Properties** (tính chất) của nó như **Height** (chiều cao), **Depth** (độ sâu), **Size** (kích thước), và cách kiểm tra các đặc tính cấu trúc là vô cùng quan trọng. Các thuộc tính này không chỉ giúp đánh giá độ phức tạp của thuật toán mà còn là cơ sở để giải quyết hầu hết các bài toán liên quan đến cây.

## 2. Ứng dụng thực tế (Real-world Applications)
- **Hệ thống file**: Tính toán kích thước (Size) của một thư mục (chứa các thư mục con và file) tương đương với việc đếm số node hoặc tổng dung lượng của một Subtree.
- **Mạng máy tính**: Tìm đường dẫn ngắn nhất hoặc điểm chung gần nhất giữa hai máy tính trong mạng cục bộ dựa trên **Lowest Common Ancestor (LCA)**.
- **Render UI/DOM Tree**: Xác định độ sâu (Depth) của các thẻ HTML để áp dụng CSS hoặc xử lý sự kiện phù hợp.
- **Cơ sở dữ liệu**: Xác định cấu trúc của index tree để tối ưu hóa truy vấn thông qua việc kiểm tra tính cân bằng (Balanced).

## 3. Định nghĩa & Khái niệm cốt lõi (Definitions & Core Concepts)
- **Node**: Một phần tử của cây chứa dữ liệu và con trỏ tới con trái (left child) và con phải (right child).
- **Depth (Độ sâu)**: Số lượng cạnh trên đường đi từ gốc (root) đến một node cụ thể. Độ sâu của root là 0.
- **Height (Chiều cao)**: Số lượng cạnh trên đường đi dài nhất từ một node cụ thể đến một lá (leaf node). Chiều cao của cây là chiều cao của root.
- **Size (Kích thước)**: Tổng số lượng node trong cây.
- **Diameter (Đường kính)**: Đường đi dài nhất giữa hai node bất kỳ trong cây. Đường đi này có thể đi qua hoặc không đi qua root.
- **Lowest Common Ancestor (LCA)**: Tổ tiên chung thấp nhất của hai node p và q là một node nằm thấp nhất trên cây (xa root nhất) nhận cả p và q làm hậu duệ.
- **Path (Đường đi)**: Chuỗi các node nối tiếp nhau bởi các cạnh.
- **Subtree (Cây con)**: Một node bất kỳ trong cây và toàn bộ các node hậu duệ của nó.

## 4. Đặc điểm và Tính chất (Characteristics & Properties)
- **Số lượng node tối đa ở độ sâu $d$**: $2^d$.
- **Số lượng node tối đa của cây chiều cao $h$**: $2^{h+1} - 1$.
- Tính đệ quy: Mỗi cây nhị phân bao gồm một root, một Subtree trái và một Subtree phải, cả hai đều là các Binary Tree. Do đó, hầu hết các thuộc tính của cây có thể được tính toán một cách đệ quy.

## 5. Các thao tác cơ bản (Basic Operations)
- `getHeight(node)`: Tính chiều cao của cây.
- `getSize(node)`: Đếm số lượng node.
- `getLCA(root, p, q)`: Tìm LCA của hai node.
- `getDiameter(root)`: Tính đường kính của cây.

## 6. Phân loại (Classifications)
Tính chất của cây có thể dẫn đến việc phân loại cây nhị phân thành:
- **Balanced Binary Tree**: Cây mà chiều cao của hai Subtree tại mỗi node chênh lệch không quá 1.
- **Symmetric Tree**: Cây đối xứng qua trục ở giữa (nửa trái là hình ảnh phản chiếu của nửa phải).
- **Identical Trees**: Hai cây có cùng cấu trúc và giá trị tại mọi node tương ứng giống nhau.

## 7. Các cấu trúc dữ liệu / Biến thể liên quan (Related Data Structures / Variations)
- **Binary Search Tree (BST)**: Đặc tính LCA trên BST có thể tìm nhanh hơn so với Binary Tree thông thường.
- **N-ary Tree**: Mở rộng tính chất Height, Depth cho cây có nhiều con.

## 8. So sánh với các cấu trúc dữ liệu khác (Comparison with other DS)
- Khác với **Graph** thông thường, Tree không có chu trình (cycle), và có duy nhất một đường đi giữa hai node bất kỳ, giúp các bài toán như tìm LCA và Path Sum trở nên trực quan và có thể giải quyết bằng **DFS** hoặc **BFS** mà không cần mảng `visited`.

## 9. Phân tích độ phức tạp (Complexity Analysis)
- Đa số các thuật toán kiểm tra tính chất cây (Height, Size, LCA, Diameter) đều duyệt qua toàn bộ các node.
- **Time Complexity**: $O(N)$ với $N$ là số node trong cây.
- **Space Complexity**: $O(H)$ với $H$ là chiều cao của cây (cho call stack của đệ quy). $O(\log N)$ trong trường hợp cây cân bằng, $O(N)$ trong trường hợp cây lệch (skewed tree).

## 10. Hướng dẫn triển khai (Implementation Guide)
Hầu hết các bài toán tính chất cây nhị phân được giải quyết bằng **Depth-First Search (DFS)**, đặc biệt là **Post-order Traversal** (duyệt hậu thứ tự), vì để đánh giá một node, ta thường cần biết kết quả từ hai con của nó trước.
- **Base case**: Kiểm tra `node == null`.
- **Recursive step**: Gọi đệ quy cho `left` và `right`.
- **Combine**: Kết hợp kết quả của `left` và `right` cùng với giá trị của `node` hiện tại để trả về cho node cha.

## 11. Mã nguồn mẫu (Code Template)

**Template cho DFS Post-order lấy thông tin từ dưới lên (Bottom-up):**
```java
public ReturnType dfs(TreeNode node) {
    if (node == null) {
        return BASE_VALUE;
    }
    
    ReturnType leftResult = dfs(node.left);
    ReturnType rightResult = dfs(node.right);
    
    // Xử lý logic tại node hiện tại dựa trên leftResult và rightResult
    ReturnType currentResult = process(leftResult, rightResult, node.val);
    
    return currentResult;
}
```

## 12. Các mẫu thuật toán phổ biến (Common Algorithm Patterns)
- **Top-Down DFS (Truyền trạng thái xuống)**: Hữu ích cho Path Sum. Ta truyền một biến tích lũy từ gốc xuống các lá.
- **Bottom-Up DFS (Lấy kết quả từ dưới lên)**: Dùng cho Height, Diameter, LCA, Subtree.
- **Two Pointers/Parallel Traversal**: Dùng cho Identical Trees hoặc Symmetric Tree, duyệt song song hai con trỏ trên một hoặc hai cây.

## 13. 20 Lỗi thường gặp (20 Common Bugs)
1. Quên kiểm tra `root == null` (Base case).
2. Tính nhầm Depth và Height (bắt đầu từ 0 hay 1).
3. Trong LCA, không xử lý trường hợp một node không tồn tại trong cây (nếu bài toán không đảm bảo p, q chắc chắn có).
4. Đường kính (Diameter): Nhầm lẫn giữa số node và số cạnh (số cạnh = số node - 1).
5. Đường kính: Chỉ tính đường đi qua root, quên mất đường kính có thể nằm hoàn toàn ở Subtree.
6. Path Sum: Dừng kiểm tra tại null node thay vì leaf node.
7. Path Sum: Nhầm lẫn giữa đường đi bất kỳ (có thể không qua root hoặc leaf) và đường đi Root-to-Leaf.
8. Symmetric Tree: So sánh `node.left` với `node.left` thay vì `node.left` với `node.right`.
9. Biến toàn cục (Global variable): Quên reset biến toàn cục giữa các test cases.
10. Tham chiếu sai p và q trong bài LCA do trùng giá trị nhưng khác địa chỉ ô nhớ.
11. `isSubtree`: Không kiểm tra đệ quy sâu bên trong cây cha khi root hiện tại không khớp.
12. `isSubtree`: Khi `isIdentical` thất bại, chỉ kiểm tra tiếp một trong hai con trái hoặc phải, thay vì cả hai.
13. `Balanced Tree`: Tính đi tính lại Height của node con (tốn $O(N^2)$) thay vì trả về -1 nếu không cân bằng để tối ưu thành $O(N)$.
14. NullPointerException khi truy cập `node.left.val` mà không check `node.left != null`.
15. Không xử lý số nguyên tràn viền (Integer Overflow) khi tính Path Sum với cây lớn.
16. Nhầm lẫn phép toán tử `||` và `&&` khi kiểm tra hai nhánh (ví dụ: Symmetric cần `&&`).
17. Khởi tạo giá trị lớn nhất/nhỏ nhất bằng 0 thay vì `Integer.MIN_VALUE` / `Integer.MAX_VALUE`.
18. Không copy cấu trúc List khi thêm vào List tổng trong bài Path Sum II (bị tham chiếu).
19. Quên backtrack (xóa node khỏi List) khi dùng DFS tìm tất cả các đường đi.
20. Invert Binary Tree: Mất giá trị `left` hoặc `right` do gán đè mà không dùng biến tạm (temp).

## 14. 30 Góc khuất và Trường hợp đặc biệt (30 Edge Cases)
1. Cây rỗng (`root == null`).
2. Cây chỉ có một node (root node).
3. Cây lệch trái hoàn toàn (Left skewed tree).
4. Cây lệch phải hoàn toàn (Right skewed tree).
5. LCA: Cả p và q đều là cùng một node.
6. LCA: p là cha/tổ tiên của q.
7. LCA: q là cha/tổ tiên của p.
8. LCA: p và q nằm ở hai nhánh khác nhau.
9. LCA: p hoặc q (hoặc cả hai) không tồn tại trong cây.
10. Diameter: Đường kính thực sự đi qua một node con chứ không phải root.
11. Path Sum: Tổng các node âm.
12. Path Sum: Tổng yêu cầu là 0, cây có các số 0 hoặc số âm.
13. Path Sum: Leaf node có giá trị bằng targetSum, nhưng quên kiểm tra nó có phải lá không.
14. Subtree: Hai cây hoàn toàn giống nhau.
15. Subtree: Cây t (cây con) lớn hơn cây s (cây mẹ).
16. Subtree: Cây t rỗng (theo định nghĩa thường là true).
17. Subtree: Cây t có các giá trị giống một phần nhánh của s nhưng có thêm lá.
18. Identical Trees: Cấu trúc giống nhau nhưng giá trị khác nhau ở node cuối cùng.
19. Identical Trees: Giá trị giống nhau nhưng cấu trúc khác nhau (ví dụ: một bên trái, một bên phải).
20. Symmetric Tree: Nửa trái rỗng, nửa phải có node.
21. Symmetric Tree: Cây có giá trị đối xứng nhưng cấu trúc không đối xứng.
22. Maximum Depth: Cây có 1 nhánh cực dài, nhánh còn lại ngắn.
23. Minimum Depth: Root chỉ có con trái (Depth lúc này bằng nhánh trái, chứ không phải nhánh phải rỗng).
24. Balanced Tree: Một node mất cân bằng ở tận đáy, nhưng các node trên vẫn trông có vẻ cân bằng nếu tính sai.
25. Path Sum II: Cây có đường đi tổng đúng nhưng không kết thúc tại lá.
26. Invert Tree: Đảo ngược một cây không cân bằng.
27. Đếm số node: Cây kích thước cực lớn gây tràn call stack (StackOverflowError).
28. Diameter: Cây gồm toàn node có giá trị giống hệt nhau.
29. Tìm lá: Cây có cấu trúc ziczac dài ngoằng.
30. Binary Tree to DLL (Doubly Linked List): Mất liên kết gốc sau khi thay đổi con trỏ.

## 15. Kinh nghiệm thực chiến (Best Practices)
- **Sử dụng Helper Function**: Rất nhiều bài toán cây cần thêm biến trong hàm (ví dụ: depth hiện tại, target sum còn lại). Việc tạo một hàm `dfs()` private giúp code gọn và sạch.
- **Biến trả về vs Biến toàn cục (Return value vs Global Variables)**: Hạn chế dùng biến toàn cục. Hãy ưu tiên việc gói dữ liệu trả về vào một class đặc biệt (Object, Array, Map) nếu cần nhiều hơn một giá trị từ node con (ví dụ: trả về cả mảng `[height, isBalanced]`).
- **Post-order là cứu cánh**: Khi bạn cần thông tin từ con trước khi ra quyết định ở cha, luôn nghĩ đến Post-order.

## 16. Mẹo phỏng vấn (Interview Tips)
- Luôn kiểm tra `if (root == null)` đầu tiên!
- Nếu phỏng vấn viên hỏi: "Có cách nào không dùng đệ quy không?", bạn cần chuẩn bị sẵn cách dùng **Stack** (cho DFS) hoặc **Queue** (cho BFS) để thay thế.
- Khi làm bài về **Minimum Depth**, hãy nhấn mạnh việc một node rỗng không phải là lá, lá phải là node không có cả con trái lẫn phải. Rất nhiều người nhầm bài này.

## 17. 20 Câu hỏi phỏng vấn thực tế (20 Interview Questions)
1. "Làm sao để tìm Lowest Common Ancestor của một Binary Tree không phải là Binary Search Tree?"
2. "Sự khác biệt giữa Path Sum từ Root tới Leaf và Path Sum đi qua node bất kỳ là gì?"
3. "Viết thuật toán kiểm tra cây cân bằng (Balanced Binary Tree) với độ phức tạp $O(N)$."
4. "Đường kính của cây nhị phân có bắt buộc phải đi qua gốc không?"
5. "Làm thế nào để nghịch đảo (invert) một cây nhị phân?"
6. "Làm sao để đếm số lượng node của một Complete Binary Tree nhanh hơn $O(N)$?"
7. "Bạn có thể kiểm tra cây đối xứng (Symmetric Tree) bằng vòng lặp không?"
8. "Nếu cây quá sâu gây tràn Stack trong DFS, bạn sẽ xử lý thế nào?"
9. "Làm sao để tìm độ sâu lớn nhất (Max Depth) bằng BFS?"
10. "Tại sao bài toán tìm Minimum Depth lại cần chú ý cẩn thận hơn Max Depth?"
11. "Viết thuật toán tìm xem cây con T có nằm trong cây mẹ S hay không."
12. "Path Sum II yêu cầu in ra tất cả các đường đi. Bạn quản lý bộ nhớ thế nào?"
13. "Làm sao để serialize và deserialize một cây nhị phân?"
14. "Tìm LCA của danh sách k node thay vì 2 node."
15. "Giải thích cách tìm tổng khoảng cách giữa mọi cặp node trong cây."
16. "Xác định xem một cây có phải là cây BST không dựa trên thuộc tính cây."
17. "Làm thế nào để tạo một cây nhị phân từ mảng duyệt Preorder và Inorder?"
18. "LCA với Parent Pointers: Nếu mỗi node có thêm con trỏ tới cha, bạn tìm LCA như thế nào?"
19. "Giải bài Path Sum III: Đường đi có thể bắt đầu và kết thúc ở bất kỳ đâu."
20. "Vertical Order Traversal của một cây nhị phân thực hiện thế nào?"

## 18. Câu hỏi trắc nghiệm kiểm tra kiến thức (MCQ)
**Câu 1:** Trong BFS, ta thường dùng cấu trúc dữ liệu nào?
A. Stack
B. Queue
C. Priority Queue
D. Hash Map
*Đáp án: B*

**Câu 2:** Độ sâu của root node là bao nhiêu?
A. -1
B. 0
C. 1
D. Không xác định
*Đáp án: B (Trong một số quy ước là 1, nhưng thông dụng nhất trong lập trình là 0)*

**Câu 3:** Thuật toán duyệt nào thăm root cuối cùng?
A. Pre-order
B. In-order
C. Post-order
D. Level-order
*Đáp án: C*

## 19. Các bài toán kinh điển (Classic Problems)
- Maximum Depth of Binary Tree
- Invert Binary Tree
- Diameter of Binary Tree
- Lowest Common Ancestor
- Symmetric Tree
- Path Sum
- Subtree of Another Tree

## 20. Tài liệu tham khảo (References)
- Introduction to Algorithms (CLRS) - Chapter 10: Elementary Data Structures
- Wikipedia: Binary Tree

## 21. Nguồn học thêm (Further Reading)
- LeetCode Explore Card: Binary Tree
- GeeksforGeeks: Binary Tree Data Structure

## 22. Mindmap/Tóm tắt (Summary)
- **Binary Tree Properties**: Liên quan đến kích thước, độ sâu, chiều cao.
- **Core Traversal**: DFS (Pre, In, Post) & BFS.
- **Key Patterns**: Bottom-up (Post-order) để gom kết quả, Top-down để truyền giá trị.
- **Base Case**: Luôn cẩn thận với Null Node.

## 23. Bài tập thực hành (Practice Exercises)
Chuyển sang file `02-Binary-Tree-Properties-Problems.md` để xem danh sách 30 bài tập thực hành.

## 24. Kết luận
Hiểu rõ các thuộc tính (properties) và cách duyệt trên Binary Tree là nền tảng tối quan trọng. Hầu như mọi bài toán nâng cao về Graph, DP on Trees, hay BST đều xuất phát từ những khái niệm cốt lõi này. Hãy luyện tập nhiều với DFS đệ quy để có "cảm giác" tốt về cách Tree hoạt động.
