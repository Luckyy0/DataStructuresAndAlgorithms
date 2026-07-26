# Binary Search Tree (BST)

## 1. Giới thiệu (Introduction)
Binary Search Tree (BST - Cây nhị phân tìm kiếm) là một cấu trúc dữ liệu dạng cây nhị phân (Binary Tree) dựa trên cơ sở liên kết giữa các node, trong đó khóa của mỗi node lớn hơn mọi khóa thuộc cây con trái và nhỏ hơn mọi khóa thuộc cây con phải.

## 2. Lịch sử (History)
Thuật toán tìm kiếm và cây nhị phân tìm kiếm đã được phát triển vào đầu những năm 1960 độc lập bởi P.F. Windley, A.D. Booth, A.J.T. Colin, và T.N. Hibbard, đánh dấu một bước tiến lớn trong tối ưu tìm kiếm bộ nhớ động.

## 3. Ứng dụng thực tế (Real-world Applications)
- **Database Indexing**: Cơ sở của B-Tree dùng trong index database.
- **Quản lý bộ nhớ**: Duy trì bộ nhớ phân bổ trong hệ điều hành.
- **Collections Framework**: Cài đặt `TreeSet` và `TreeMap` trong Java (thường dùng Red-Black Tree).
- **Auto-completion**: Ứng dụng để thiết kế Trie hoặc hỗ trợ các cấu trúc tự động gợi ý từ khóa.

## 4. Khái niệm cốt lõi & Định nghĩa (Core Concepts & Definitions)
- **Node**: Một điểm dữ liệu bao gồm giá trị, con trỏ đến left child và right child.
- **BST Property**: Left Child Value < Root Value < Right Child Value.
- **Balanced BST**: Là cây BST mà chênh lệch chiều cao giữa các nhánh trái và phải của mọi node không vượt quá 1, giúp thao tác luôn xấp xỉ O(log N).

## 5. Cơ chế hoạt động (How it Works)
Khi thao tác (tìm kiếm, chèn), ta luôn bắt đầu từ Root. Tại mỗi bước, ta đối chiếu giá trị của node hiện tại với giá trị mục tiêu (Target). Nếu Target nhỏ hơn, ta chuyển sang nhánh trái, ngược lại ta chuyển sang nhánh phải.

## 6. Cấu trúc & Tính chất (Structure & Properties)
- Mỗi node có nhiều nhất 2 cây con.
- Mọi node trong cây con bên trái đều nhỏ hơn root.
- Mọi node trong cây con bên phải đều lớn hơn root.
- Tính chất này được đảm bảo đệ quy cho mọi cây con trong BST.
- **Inorder Traversal** (Duyệt Left-Root-Right) của một BST luôn trả về một mảng đã được sắp xếp tăng dần.

## 7. Các thao tác cơ bản (Basic Operations)
- **Search (Tìm kiếm)**: Đi từ root xuống lá, so sánh và chọn nhánh đi theo tính chất của BST. O(H).
- **Insert (Chèn)**: Giống Search, tìm vị trí trống thích hợp (NULL pointer) và gán node mới vào vị trí đó. O(H).
- **Delete (Xóa)**: Xóa node với 3 trường hợp: (1) Node là lá: xóa trực tiếp, (2) Node có 1 con: nối trực tiếp cha của node bị xóa với con của node bị xóa, (3) Node có 2 con: tìm node Inorder Successor hoặc Predecessor thay thế lên và xóa node thay thế đó. O(H).

## 8. Các thao tác nâng cao (Advanced Operations)
- **Tìm Inorder Successor**: Phần tử nhỏ nhất lớn hơn node hiện tại (Nằm tận cùng bên trái của nhánh phải).
- **Tìm Inorder Predecessor**: Phần tử lớn nhất nhỏ hơn node hiện tại (Nằm tận cùng bên phải của nhánh trái).
- **Cân bằng (Rebalance)**: Sử dụng các phép quay (Rotations) để giảm chiều cao cây bị lệch.

## 9. Độ phức tạp (Time & Space Complexity)
- **Time Complexity (Search, Insert, Delete)**: 
  - Trường hợp trung bình: $O(\log N)$ với cây cân bằng.
  - Trường hợp xấu nhất: $O(N)$ nếu cây suy biến thành Linked List (Skewed Tree).
- **Space Complexity**:
  - $O(N)$ lưu trữ.
  - Call Stack đệ quy tốn $O(H)$, với H là chiều cao (từ $\log N$ đến $N$).

## 10. Ưu & Nhược điểm (Pros & Cons)
- **Ưu điểm**: Linh hoạt thao tác với dữ liệu động. Thao tác Search/Insert/Delete trung bình nhanh hơn Linked List và mảng không sắp xếp.
- **Nhược điểm**: Có khả năng trở thành cây suy biến khiến độ phức tạp tụt xuống bằng mảng/danh sách. Không lưu các phần tử một cách tuyến tính liền kề nhau như mảng (cache-locality không tốt).

## 11. So sánh (Comparison)
- **Vs Hash Table**: BST duy trì trạng thái dữ liệu có thứ tự (Ordered Data), hỗ trợ Range Query, Min/Max query dễ dàng. Hash Table không đảm bảo thứ tự nhưng Insert/Search tốn $O(1)$.
- **Vs Array/LinkedList**: Cấu trúc tuyến tính tìm kiếm tốn $O(N)$, BST trung bình mất $O(\log N)$.

## 12. Chi tiết cài đặt (Implementation details)
```java
public class BST {
    class Node {
        int key;
        Node left, right;
        public Node(int item) { key = item; left = right = null; }
    }
    Node root;
    
    // Insert
    Node insert(Node root, int key) {
        if (root == null) return new Node(key);
        if (key < root.key) root.left = insert(root.left, key);
        else if (key > root.key) root.right = insert(root.right, key);
        return root;
    }
}
```

## 13. Edge Cases & Constraints (30 Edge cases)
1. Cây rỗng (Null root).
2. Cây có duy nhất 1 node.
3. BST bị lệch hoàn toàn trái (Left-skewed).
4. BST bị lệch hoàn toàn phải (Right-skewed).
5. Xóa lá (Leaf node).
6. Xóa Root (Node gốc) khi chỉ có 1 con.
7. Xóa Root khi có đủ 2 con.
8. Giá trị Insert đã tồn tại (Duplicates trong BST).
9. Tìm một giá trị không tồn tại.
10. Lấy Min/Max ở cây rỗng.
11. Node cần xóa không tồn tại.
12. Tìm Inorder Successor của phần tử lớn nhất.
13. Tìm Inorder Predecessor của phần tử nhỏ nhất.
14. Node có giá trị nhỏ nhất nhưng lại có con phải.
15. Node có giá trị lớn nhất nhưng lại có con trái.
16. Xây dựng cây từ mảng rỗng.
17. Xây dựng cây từ mảng toàn phần tử âm.
18. Tràn bộ nhớ stack (StackOverflow) do cây quá cao.
19. Tràn số Integer (Integer Overflow) khi kiểm tra `node.val` với bound.
20. Inorder traversal cho một cây bị lệch 10,000 nodes.
21. Preorder traversal so sánh giữa cấu trúc cân bằng và suy biến.
22. Tìm kiếm LCA khi một trong 2 node không có trong cây.
23. Tìm Kth smallest khi $k > N$.
24. Tìm Kth smallest khi $k \le 0$.
25. Cây lớn gặp giới hạn Heap Memory khi nạp hết node vào mảng (OOM).
26. BST chỉ toàn các node có 1 nhánh duy nhất xen kẽ zigzag.
27. Validate BST với node trùng (đòi hỏi quyết định < hay <=).
28. Convert BST sang Linked list in-place.
29. Cấu trúc Tree với các node chứa tham chiếu vòng (Cyclic - invalid tree).
30. Thay đổi giá trị node trực tiếp làm phá hỏng tính chất BST.

## 14. Common Bugs & Pitfalls (20 Common bugs)
1. Bỏ quên gán lại giá trị cho node con trong phép gọi đệ quy `root.left = insert(...)`.
2. Kiểm tra NullPointerException lỏng lẻo khi dò xuống `.left` và `.right`.
3. Validate BST sai cách bằng việc chỉ kiểm tra Left < Root < Right cho các node kế cận.
4. Trả về sai node trong hàm Delete khi xóa node có 2 nhánh con.
5. Cập nhật nhầm giá trị của Successor thay vì cập nhật con trỏ thực sự.
6. Khi tìm Inorder Successor từ nhánh phải bị sai nếu trỏ vào null.
7. Đệ quy quên Base case cho cây rỗng.
8. Quên không free vùng nhớ hoặc xóa tham chiếu ở node cũ.
9. Đếm sai số lượng node trong Kth smallest element (thay đổi biến global không cẩn thận).
10. So sánh `==` cho giá trị kiểu Integer object thay vì dùng `.equals()`.
11. Sử dụng biến đếm tĩnh (static count) nhưng không reset giữa các testcase (LeetCode bug).
12. Phá vỡ tính chất giới hạn (`min`/`max` bound) khi duyệt qua trái/phải.
13. Dùng sai giá trị `Long.MIN_VALUE` làm min bound cho node có thể lưu giá trị `Integer.MIN_VALUE`.
14. Tìm tổ tiên chung gần nhất (LCA) không xử lý nhánh rẽ hướng đúng.
15. In-place manipulation làm thay đổi các node của cây ban đầu trước khi duyệt xong.
16. Stack bị phình lên vô tận trong Iterative Traversal do không pop ra đúng lúc.
17. Tìm node thay thế khi delete nhưng xóa nhầm luôn nhánh con của node đó.
18. Iterator cho BST thiết kế không lưu giữ được context (state).
19. Quên mất duyệt Right-Root-Left khi giải các bài từ Lớn về Nhỏ.
20. Trả về Root sai sau khi cây ban đầu bị thay đổi hoặc root cũ đã bị xóa.

## 15. Kỹ thuật tối ưu (Optimization Techniques)
- Sử dụng **Iterative Approach** (Stack giả) thay thế đệ quy cho cây lớn chống StackOverflow.
- Lưu con trỏ `parent` trong cấu trúc Node (nếu được phép) để dễ truy xuất hướng ngược.
- Sử dụng **Threaded Binary Tree** (Cây nhị phân khâu) hoặc thuật toán **Morris Traversal** để duyệt BST Inorder chỉ tốn không gian $O(1)$.

## 16. Chiến lược kiểm thử (Testing Strategies)
- Validate lại cây thành Inorder List để kiểm tra dãy có sắp xếp hay không.
- Chèn thử mảng ngẫu nhiên xen kẽ, sau đó xóa theo số lượng giảm dần và kiểm tra cấu trúc.
- Test bằng các dãy số giảm dần liên tục hoặc tăng dần liên tục để tạo Skewed Tree.

## 17. Interview Questions (20 Interview questions)
1. Validate Binary Search Tree.
2. Inorder Successor / Predecessor.
3. Kth Smallest Element.
4. Serialize and Deserialize BST.
5. Recover BST (Two elements swapped).
6. Lowest Common Ancestor.
7. Insert and Delete node.
8. Trim a Binary Search Tree.
9. Convert Sorted Array to Balanced BST.
10. Range Sum of BST.
11. Two Sum IV (BST).
12. Find Mode in BST.
13. Convert BST to Greater Tree.
14. Unique Binary Search Trees (DP/Catalan).
15. Largest BST Subtree.
16. Construct BST from Preorder Traversal.
17. Balance a Binary Search Tree.
18. Split BST.
19. Minimum Absolute Difference in BST.
20. Closest Binary Search Tree Value.

## 18. Mẫu code chuẩn/Cheatsheet (Standard Templates/Cheatsheet)
```java
// Template Inorder - iterative
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
```

## 19. Chủ đề liên quan (Related Topics)
- **Binary Tree**: Tổ tiên của BST.
- **AVL Tree, Red-Black Tree**: Giải pháp khắc phục độ phức tạp $O(N)$.
- **B-Tree**: Nền tảng cấu trúc lưu trữ database.
- **Trie (Prefix Tree)**: Cấu trúc tìm kiếm theo chuỗi nhanh.

## 20. Tài liệu tham khảo (Recommended Resources)
- Sách *Introduction to Algorithms (CLRS)*.
- Khoá học *Algorithms Part I & II (Princeton University - Coursera)*.
- Mục *Binary Search Tree* trên *GeeksforGeeks* và *LeetCode Explore*.

## 21. Thuật ngữ (Glossary of terms)
- **Leaf Node**: Nút lá, nút không có con.
- **Root**: Nút gốc trên cùng của cây.
- **Predecessor/Successor**: Các nút liền trước / liền sau trong dãy đã sắp xếp Inorder.
- **Height**: Độ dài đường đi từ rễ đến lá xa nhất.

## 22. Tổng kết (Summary)
BST là một dạng đồ thị đặc biệt. Khi làm chủ BST, bạn nắm được nền tảng quan trọng của các kiểu dữ liệu phân cấp, giúp tăng hiệu suất hệ thống đáng kể nhờ tính chất Binary Search.

## 23. Câu hỏi thường gặp (FAQs)
- **Tại sao cần BST khi đã có Map (Hash Table)?**
  Hash Table nhanh O(1) nhưng không có thứ tự. Nếu bạn cần liệt kê các giá trị trong một dải (Range search) hay tìm Next/Previous Element, BST là bắt buộc.
- **Có BST có giá trị trùng lặp không?**
  Hoàn toàn có, ta có thể đặt rule: $<= Root$ nằm trái, hoặc $> Root$ nằm phải. Tuy nhiên thường người ta tăng biến count ở các giá trị trùng để giữ nguyên số lượng node tĩnh.

## 24. Bước tiếp theo (Actionable Next Steps)
- Hiểu rõ 3 bài toán: Insert, Delete, Search bằng tay qua hình vẽ.
- Áp dụng Iterative Traversal Inorder.
- Thực hành trên các bài toán có sự ứng dụng tính chất Left < Root < Right để loại bỏ phân nửa khối lượng dữ liệu (pruning).
