# Cẩm nang Divide and Conquer trên Cấu trúc Cây

## 1. Giới thiệu về Divide and Conquer trên Cấu trúc Cây
Chia để trị (Divide and Conquer - D&C) là một chiến lược thiết kế thuật toán tự nhiên và mạnh mẽ nhất khi áp dụng lên cấu trúc dữ liệu cây (Tree). Vì bản thân cây đã được định nghĩa theo cách đệ quy (một node gốc và các cây con), việc giải quyết bài toán trên cây thường tương đương với việc giải quyết bài toán trên các cây con rồi tổng hợp kết quả lại.

## 2. Nguyên lý hoạt động
Nguyên lý của D&C trên cây bao gồm 3 bước:
1. **Divide**: Chia bài toán tại node hiện tại thành các bài toán nhỏ hơn trên các subtree (thường là left subtree và right subtree).
2. **Conquer**: Gọi đệ quy để giải quyết các bài toán con này. Các node lá (leaf nodes) hoặc node null (đáy đệ quy) sẽ được xử lý trực tiếp.
3. **Combine**: Kết hợp kết quả (return values) từ left subtree và right subtree cùng với thông tin của node hiện tại để tạo ra kết quả cho node hiện tại.

## 3. Treating left and right subtrees as independent subproblems
Một đặc điểm quan trọng của D&C trên cây là **sự độc lập của các bài toán con**.
- **Không chia sẻ trạng thái**: Khác với đồ thị, cây không có chu trình (cycle), do đó nhánh trái và nhánh phải hoàn toàn độc lập với nhau.
- **Thực thi song song**: Vì độc lập, việc tính toán trên left subtree không ảnh hưởng đến right subtree, giúp thuật toán dễ hiểu, dễ debug và có thể tối ưu hóa chạy song song (parallel execution).

## 4. Constructing trees from array representations
Bài toán tái tạo cây từ mảng duyệt (Inorder, Preorder, Postorder) là ứng dụng kinh điển của D&C.
- **Preorder/Postorder**: Cung cấp thông tin về node gốc (Root) của cây/subtree hiện tại (Preorder: phần tử đầu, Postorder: phần tử cuối).
- **Inorder**: Cung cấp cách chia đôi cây. Khi tìm thấy vị trí của Root trong mảng Inorder, tất cả phần tử bên trái Root thuộc left subtree, tất cả phần tử bên phải thuộc right subtree.
- **Chiến lược D&C**: Tìm gốc -> Chia Inorder thành mảng con trái/phải -> Chia Preorder/Postorder thành các mảng con tương ứng dựa trên kích thước -> Gọi đệ quy để xây dựng left child và right child.

## 5. Xử lý hậu thứ tự (Postorder Processing for D&C)
Hầu hết các bài toán D&C trên cây dựa trên thao tác **Postorder Traversal** (Duyệt hậu thứ tự).
- Trong Postorder, chúng ta xử lý các node con trước khi xử lý node gốc.
- Điều này hoàn toàn khớp với bước **Combine** của D&C: ta cần kết quả từ bài toán con (left, right subtrees) trước khi có thể tính toán kết quả cho bài toán lớn (root).

## 6. Định dạng hàm đệ quy chuẩn
```java
public ResultType divideAndConquer(TreeNode root) {
    // 1. Base case
    if (root == null) return new ResultType(...);
    
    // 2. Divide & Conquer
    ResultType leftResult = divideAndConquer(root.left);
    ResultType rightResult = divideAndConquer(root.right);
    
    // 3. Combine
    ResultType currentResult = merge(leftResult, rightResult, root.val);
    
    return currentResult;
}
```

## 7. So sánh Top-down và Bottom-up
- **Top-down (Preorder based)**: Truyền thông tin từ gốc xuống lá (tham số của hàm). Phù hợp cho việc tìm đường đi (path).
- **Bottom-up (Postorder based)**: Đây chính là cốt lõi của D&C. Tính toán từ lá rồi truyền kết quả (return value) lên gốc.

## 8. Quản lý trạng thái (State Management)
Đôi khi một giá trị trả về đơn lẻ không đủ để giải quyết bài toán (ví dụ: Largest BST Subtree). Cần tạo một class `State` hoặc `Result` để bọc (wrap) nhiều thông tin: kích thước, max, min, isBST... để trả về từ mỗi node.

## 9. Phân tích độ phức tạp thời gian
- Thường là $O(N)$ vì mỗi node được truy cập một lần.
- Nếu tại mỗi node tốn $O(N)$ để xử lý kết quả (ví dụ tìm node trong Inorder array mà không dùng HashMap), độ phức tạp có thể lên $O(N^2)$.

## 10. Phân tích độ phức tạp không gian
- Không gian phụ trợ chính là Call Stack của đệ quy.
- Cây cân bằng (Balanced Tree): $O(\log N)$.
- Cây lệch (Skewed Tree): $O(N)$.

## 11. Các mẫu thiết kế (Design Patterns for Tree D&C)
- **Result Wrapper Pattern**: Trả về một Object chứa nhiều thuộc tính (State pattern).
- **Global Variable Pattern**: Dùng một biến toàn cục để cập nhật max/min trong quá trình duyệt. Tuy nhiên, trả về kết quả qua đệ quy vẫn được ưu tiên hơn vì tính "functional".

## 12. Ứng dụng trong thực tiễn
- Xử lý ngôn ngữ tự nhiên (Abstract Syntax Trees - AST).
- Rendering DOM tree trong trình duyệt.
- Xử lý các query trên cấu trúc dữ liệu không gian (KD-Tree, Quad-Tree).

## 13. Cấu trúc dữ liệu hỗ trợ
- **HashMap**: Tối ưu hóa việc tìm kiếm phần tử, ví dụ map value sang index trong mảng Inorder để giảm thời gian tìm kiếm từ $O(N)$ xuống $O(1)$.
- **Deque/Queue**: Hỗ trợ khi kết hợp D&C với Level-order.

## 14. Tối ưu hóa bằng Memoization
Trên cây thông thường không có overlapping subproblems vì các nhánh độc lập. Tuy nhiên, với bài toán như "Unique Binary Search Trees", các cây con với cùng tập giá trị có thể lặp lại, lúc này D&C kết hợp với Memoization (Dynamic Programming) là cần thiết.

## 15. Khử đệ quy (Iterative Approaches)
Mặc dù đệ quy tự nhiên nhất, nhưng có thể khử đệ quy D&C bằng cách dùng hai Stacks hoặc mô phỏng lại Call Stack, dù code sẽ rất phức tạp và khó đọc.

## 16. 20 Lỗi thường gặp (Common Bugs)
1. Quên kiểm tra `root == null` (Base case).
2. Trả về sai giá trị tại base case (ví dụ trả về 0 thay vì Integer.MIN_VALUE khi tìm max).
3. Sử dụng biến toàn cục nhưng quên reset giữa các test cases.
4. Nhầm lẫn giữa Preorder và Postorder traversal khi logic yêu cầu bottom-up.
5. Sửa đổi cấu trúc cây (tree mutation) vô tình trong quá trình duyệt.
6. Tham chiếu sai left/right khi xử lý mảng (ví dụ: `inStart, inIndex - 1` vs `inStart, inIndex`).
7. Tính toán sai độ dài của left subtree khi chia mảng Preorder.
8. Tràn bộ nhớ (Stack Overflow) do cây quá sâu (Skewed tree).
9. Quên lưu kết quả trung gian, dẫn đến tính toán lại (TLE).
10. Xử lý sai lá (Leaf node) thành null node.
11. Truy cập thuộc tính của node trả về từ con mà chưa check null.
12. NullPointerException khi gọi `leftResult.size` mà `leftResult` là null.
13. Không phân tách rõ logic của Left Subtree và Right Subtree.
14. Nhầm lẫn logic kết hợp (Combine): Dùng `+` thay vì `Math.max()`.
15. Xây dựng cây vô tận do index truyền vào đệ quy không thu hẹp.
16. Thay đổi giá trị mảng đầu vào khi không được phép.
17. Hiểu sai yêu cầu: Đường đi (Path) phải liên tục nhưng lại đứt quãng.
18. Không xem xét trường hợp giá trị node bị âm.
19. Gán nhầm `root.left = buildTree(...)` thành `root.right = buildTree(...)`.
20. Trả về node con thay vì node hiện tại.

## 17. 30 Trường hợp biên (Edge Cases)
1. Cây rỗng (Root = null).
2. Cây chỉ có 1 node (Root).
3. Cây lệch trái hoàn toàn (Left skewed).
4. Cây lệch phải hoàn toàn (Right skewed).
5. Cây có tất cả các node đều có cùng giá trị.
6. Cây có giá trị node âm và dương đan xen.
7. Cây cân bằng hoàn hảo (Perfect binary tree).
8. Cây cực kỳ sâu gây ra StackOverflowError (> 10000 nodes).
9. Mảng Inorder/Preorder đầu vào trống.
10. Mảng đầu vào chứa 1 phần tử.
11. Mảng Inorder và Preorder không khớp nhau (Invalid input).
12. Cây có số lượng node là số chẵn/số lẻ.
13. Đường đi lớn nhất (Max Path) không đi qua gốc mà nằm hoàn toàn trong một cây con.
14. Mọi node đều âm (trong bài tìm max path sum).
15. Leaf node ở các độ sâu rất khác nhau.
16. Construct tree nhưng mảng có phần tử trùng lặp (nếu đề bài cho phép).
17. Tìm LCA khi hai node cha - con của nhau.
18. Tìm LCA khi một hoặc cả hai node không tồn tại trong cây.
19. Subtree có giá trị rất lớn gây tràn kiểu `int` (Integer Overflow).
20. Construct BST từ mảng đã sắp xếp nhưng mảng bị lệch (chứa phần tử trùng).
21. Bài toán yêu cầu Serialize/Deserialize với cấu trúc đặc biệt chứa null strings.
22. Hàm trả về cấu trúc tuỳ chỉnh (State) có null properties.
23. Cây có dạng zigzag (trái, rồi phải, rồi trái...).
24. Quản lý trạng thái toàn cục với cây có 0 node.
25. Mảng đầu vào truyền vào kích thước quá lớn.
26. Kết quả là cây rỗng nhưng yêu cầu trả về list rỗng thay vì null.
27. Đếm số lượng BST sinh ra vượt quá giới hạn integer.
28. Kết hợp kết quả từ nhánh trái là hợp lệ, nhưng nhánh phải không hợp lệ.
29. Giá trị max/min của node đúng bằng `Integer.MAX_VALUE` / `Integer.MIN_VALUE`.
30. Tìm LCA khi cây chỉ có gốc là hai target nodes giống nhau.

## 18. 20 Câu hỏi phỏng vấn (Interview Questions)
1. "Làm thế nào để xây dựng cây nhị phân từ Inorder và Preorder traversals?"
2. "Explain the differences between Top-down and Bottom-up approach on trees."
3. "Bạn làm thế nào để Serialize và Deserialize một cây nhị phân?"
4. "Tìm tổ tiên chung gần nhất (LCA) của hai nodes trong một cây nhị phân."
5. "LCA trong cây BST khác với LCA trong cây nhị phân bình thường như thế nào?"
6. "Làm thế nào để tìm đường đi có tổng lớn nhất trong một cây nhị phân?"
7. "Bạn sẽ thiết kế thuật toán thế nào để đếm số lượng node của Complete Binary Tree trong O(log^2 N)?"
8. "Viết hàm đảo ngược cây nhị phân (Invert Binary Tree)."
9. "Làm sao để kiểm tra một cây có phải là BST hợp lệ không (Validate BST)?"
10. "Xác định xem hai cây nhị phân có cấu trúc và giá trị giống hệt nhau không."
11. "Làm thế nào để làm phẳng cây nhị phân thành danh sách liên kết (Flatten Binary Tree)?"
12. "Cho biết Postorder, có thể tạo ra cây duy nhất không nếu không có Inorder?"
13. "Làm thế nào để tìm cây con BST lớn nhất (Largest BST Subtree) trong một cây nhị phân?"
14. "Thuật toán để kiểm tra xem một cây có đối xứng (Symmetric) hay không?"
15. "Giải bài toán House Robber III trên cấu trúc cây."
16. "Xây dựng tất cả các cây BST duy nhất từ 1 đến N (Unique BST II)."
17. "Giải thích cách xoá một node trong cây BST."
18. "Làm thế nào để chia một cây thành hai phần sao cho tổng các node của hai phần chênh lệch nhau ít nhất?"
19. "Định dạng State Pattern khi nào cần dùng trong các bài toán cây?"
20. "Nếu bộ nhớ bị giới hạn, làm sao để thực hiện inorder traversal không dùng đệ quy và không dùng stack phụ (Morris Traversal)?"

## 19. Kinh nghiệm thực chiến (Practical Experience)
Luôn định nghĩa rõ ràng: hàm đệ quy của tôi nhận vào cái gì, và trả về cái gì. Một hàm đệ quy tốt là một hàm có input/output ràng buộc chặt chẽ, không bị phụ thuộc lỏng lẻo vào biến toàn cục (global variables). Tránh việc sử dụng các hàm void để cập nhật danh sách hoặc bộ đếm nếu có thể gói gọn vào kiểu trả về.

## 20. Các bài toán biến thể (Variant Problems)
Bài toán D&C trên cây N-ary tree hoặc cây trên đồ thị vô hướng (Undirected tree graph) sẽ cần kết hợp thêm vòng lặp for ở bước chia bài toán thay vì chỉ gọi left và right, tuy nhiên bản chất Divide & Conquer vẫn không đổi.

## 21. Debug và Testing
- In ra Preorder và Inorder để dễ hình dung cây trên terminal khi không có giao diện vẽ đồ thị.
- Test bằng các cây lệch để tìm lỗi StackOverflow.

## 22. Hệ thống đánh giá (Evaluation Metrics)
Các bài toán D&C cây thường được đánh giá gắt gao ở Space Complexity. Đôi khi có thể tối ưu O(N) xuống O(H) không gian bộ nhớ phụ bằng cách dùng đệ quy tự nhiên (Call Stack) và không cấp phát thêm cấu trúc dữ liệu mới trong từng cấp đệ quy.

## 23. Tài liệu tham khảo
- Introduction to Algorithms (CLRS) - Chương về Cây và Đệ quy.
- Nền tảng LeetCode (Tree Tag).
- Elements of Programming Interviews (EPI) - Recursion & Binary Trees.

## 24. Tổng kết
Chia để trị là trái tim của mọi thuật toán làm việc với cấu trúc cây. Nắm vững kỹ năng này, bạn không chỉ giải quyết được các bài phỏng vấn ở mức độ Hard mà còn hiểu rõ nguyên lý vận hành của đệ quy và các cấu trúc dữ liệu phân cấp.
