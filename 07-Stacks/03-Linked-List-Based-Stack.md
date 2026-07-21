# Chuyên đề: Linked-List-Based Stack (Ngăn xếp dựa trên Danh sách liên kết)

## 1. Metadata
- **Tên chuyên đề**: Linked-List-Based Stack
- **Độ khó**: Dễ (Easy)
- **Cấu trúc dữ liệu**: Stack, Singly Linked List
- **Phiên bản Java**: Java 21+

## 2. Purpose (Mục đích)
Mục đích của cấu trúc dữ liệu **Linked-List-Based Stack** là cung cấp một cơ chế lưu trữ theo nguyên lý LIFO (Last-In-First-Out) dựa trên danh sách liên kết, giúp kích thước của ngăn xếp có thể phát triển linh hoạt mà không bị giới hạn bởi dung lượng mảng cố định ban đầu, tận dụng việc cấp phát bộ nhớ động của JVM.

## 3. Motivation (Động lực)
Trong một Array-based Stack, ta phải đối mặt với bài toán thay đổi kích thước (resizing) mảng mỗi khi ngăn xếp đầy (gây ra chi phí $O(N)$ trong thao tác đó). Động lực chính của Linked-List-Based Stack là loại bỏ hoàn toàn chi phí thay đổi kích thước này. Các thao tác `push()` và `pop()` luôn được thực hiện với độ phức tạp $O(1)$ chặt chẽ trong mọi trường hợp, rất phù hợp với các hệ thống có yêu cầu ngặt nghèo về độ trễ lớn nhất (max latency) hoặc những tình huống mà số lượng phần tử biến động vô cùng lớn và không thể đoán trước.

## 4. Mathematical Foundation (Nền tảng toán học)
Về mặt bộ nhớ, một Node trong danh sách liên kết sẽ tốn không gian lưu trữ cho phần tử (payload) và con trỏ trỏ tới Node tiếp theo.
Ký hiệu:
- $N$ là số lượng phần tử.
- $S_{node}$ là kích thước bộ nhớ của một Node.
Tổng không gian bộ nhớ: $M = N \times S_{node}$
Do JVM quản lý object header và reference, $S_{node} \approx 24$ bytes (Object Header) + $4$ bytes (Reference) + Kích thước phần tử, lớn hơn đáng kể so với một Array-based Stack chỉ lưu con trỏ trong mảng liên tục.

## 5. Core Theory (Lý thuyết cốt lõi)
Ngăn xếp được triển khai bằng **Singly Linked List** (Danh sách liên kết đơn), trong đó:
- Đỉnh của ngăn xếp (Top) được đại diện bởi phần tử `head` của danh sách.
- Thao tác **Push**: Tạo một Node mới, trỏ `next` của Node mới tới `head` hiện tại, sau đó cập nhật `head` thành Node mới.
- Thao tác **Pop**: Trả về dữ liệu của `head`, sau đó cập nhật `head = head.next`.

## 6. Visual Explanation (Giải thích trực quan)
```mermaid
graph LR
    subgraph "Linked-List Stack"
        Head[Top / Head] --> N1[Node 1\n(Data: 50)]
        N1 --> N2[Node 2\n(Data: 30)]
        N2 --> N3[Node 3\n(Data: 10)]
        N3 --> Null[null]
    end
    style Head fill:#f96,stroke:#333,stroke-width:2px
```
*Mỗi khi Push, một Node mới sẽ được chèn vào vị trí trước `Head` hiện tại. Mỗi khi Pop, `Head` sẽ bị xóa đi.*

## 7. Java Implementation (Mã nguồn Java 21)
```java
public class LinkedListStack<T> {
    private static class Node<T> {
        final T data;
        Node<T> next;

        Node(T data) {
            this.data = data;
        }
    }

    private Node<T> top;
    private int size;

    public LinkedListStack() {
        this.top = null;
        this.size = 0;
    }

    // O(1) Time | O(1) Space
    public void push(T item) {
        Node<T> newNode = new Node<>(item);
        newNode.next = top;
        top = newNode;
        size++;
    }

    // O(1) Time | O(1) Space
    public T pop() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        T item = top.data;
        top = top.next;
        size--;
        return item;
    }

    // O(1) Time | O(1) Space
    public T peek() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        return top.data;
    }

    public boolean isEmpty() {
        return top == null;
    }

    public int size() {
        return size;
    }
}
```

## 8. Step-by-Step (Từng bước)
**Quá trình Push(20):**
1. Kiểm tra bộ nhớ trống (JVM lo).
2. Tạo `Node(20)`.
3. Gán `Node.next = top`.
4. Cập nhật `top = Node`.
5. Tăng `size` lên 1.

**Quá trình Pop():**
1. Kiểm tra `top == null` (Nếu có, ném ngoại lệ).
2. Lấy `data = top.data`.
3. Di chuyển `top = top.next` (Loại bỏ Node đầu).
4. Giảm `size` đi 1.
5. Trả về `data`.

## 9. Complexity Analysis (Độ phức tạp)
- **Time Complexity (Thời gian):**
  - Push: $O(1)$ (Luôn chèn ở đầu).
  - Pop: $O(1)$ (Luôn xóa ở đầu).
  - Peek: $O(1)$.
- **Space Complexity (Không gian):**
  - $O(N)$ bộ nhớ phụ cho $N$ nodes. Mỗi phần tử đều cần đối tượng Node đi kèm (Memory overhead).

## 10. JVM Analysis (Phân tích JVM)
- **Object Header Overhead:** Mỗi đối tượng `Node` trong Java tốn ít nhất 12-16 byte (chưa bao gồm data) chỉ để chứa Object Header, và 4-8 byte cho biến `next`.
- **Garbage Collection:** Thao tác `pop()` liên tục tạo ra nhiều đối tượng `Node` không còn tham chiếu, tạo áp lực đáng kể lên hệ thống thu gom rác (Garbage Collector), đặc biệt là Eden space trong thế hệ Young.
- **Cache Locality:** Các `Node` được cấp phát rải rác trên Heap, dẫn đến **Cache Miss** thường xuyên hơn rất nhiều so với cấp phát liên tục trên mảng (Array-based Stack).

## 11. OpenJDK Analysis (Phân tích OpenJDK)
Trong thư viện tiêu chuẩn Java, không có một lớp riêng biệt mang tên `LinkedListStack`. Tuy nhiên, `LinkedList` của Java triển khai `Deque` và có các phương thức `push` và `pop` tuân thủ nguyên lý Stack, bản chất chúng là thao tác chèn/xóa ở phần đầu danh sách liên kết vòng kép (Doubly Linked List).

## 12. Production Usage (Sử dụng thực tế)
- Phù hợp trong các hệ thống xử lý phân tán mà không gian bộ nhớ rất dồi dào nhưng cần tránh tình trạng "STW" (Stop The World) lâu do quá trình array copy.
- Khi triển khai thuật toán Tree Traversal hay Graph Traversal (DFS) mà độ sâu đệ quy có thể vượt quá dung lượng mảng liên tục hoặc dẫn tới `StackOverflowError`.

## 13. Design Decisions (Quyết định thiết kế)
- **Tại sao chèn ở Head (Đầu) thay vì Tail (Đuôi)?** Chèn và xóa ở Head của Singly Linked List có độ phức tạp $O(1)$. Nếu chèn ở Tail mà không có tail pointer, nó sẽ mất $O(N)$. Kể cả có tail pointer, xóa ở Tail trong Singly Linked List vẫn mất $O(N)$ vì phải duyệt từ đầu để tìm Node ngay trước đó.

## 14. Common Bugs (20 Lỗi phổ biến)
1. **Quên kiểm tra ngăn xếp rỗng** khi gọi `pop()` dẫn tới `NullPointerException`.
2. **Không cập nhật lại thuộc tính `size`** trong `push()` hoặc `pop()`.
3. **Memory leak:** Trong C/C++, quên giải phóng Node cũ khi `pop()`.
4. **Chèn nhầm ở đuôi** thay vì đầu danh sách, làm sai lệch thời gian $O(1)$ thành $O(N)$.
5. **Khởi tạo sai giá trị `top`:** Gán `top = new Node(null)` thay vì `null`.
6. **Không genericize (đóng gói kiểu dữ liệu chung):** Chỉ dùng cho `int` thay vì `<T>`, giảm tính tái sử dụng.
7. **Bỏ quên con trỏ `next`:** Viết `top = newNode` trước khi `newNode.next = top`, gây mất toàn bộ phần còn lại của ngăn xếp.
8. **Trả về giá trị Node thay vì Dữ liệu:** `return top;` thay vì `return top.data;`.
9. **Lầm tưởng `peek()` sẽ thay đổi kích thước ngăn xếp**.
10. **Lỗi NullPointerException trên dữ liệu:** Cho phép đẩy `null` nhưng không lường trước lúc lấy ra làm gián đoạn chương trình.
11. **Không handle đa luồng (Concurrency):** `push` và `pop` cùng lúc bị race condition (mất Data hoặc sai kích thước).
12. **Xóa sai phần tử:** Cho rằng danh sách có con trỏ kép rồi gọi prev.
13. **Vòng lặp vô hạn (Circular link):** Lỗi vô tình tạo ra vòng lặp `node.next = node` lúc chèn.
14. **Quên tạo class Node thành static:** Trong Java, nếu class Node là inner class non-static, nó sẽ chứa reference đến outer class, gây tốn bộ nhớ vô ích.
15. **Không cài đặt EmptyStackException:** Trả về `null` thay vì ném lỗi chuẩn khi stack trống, vi phạm contract chuẩn.
16. **Cập nhật `size` sai vị trí:** Giảm `size` trước khi kiểm tra rỗng có thể gây ra `size` bị âm.
17. **Casting sai kiểu:** Khai báo Object chung chung rồi cast sai lớp lúc sử dụng.
18. **Không có method `isEmpty()`**: Ép buộc người dùng kiểm tra `size() == 0`, khó đọc hơn.
19. **Thiếu keyword `final` ở Node data:** Dữ liệu có thể bị đổi lén từ bên ngoài nếu có reference đến Node.
20. **Tạo mảng đối tượng Node Generic:** Lỗi compile do type erasure trong Java.

## 15. Edge Cases (30 Trường hợp góc)
1. **Ngăn xếp rỗng** (Empty stack).
2. **Push 1 phần tử rồi Pop ngay lập tức.**
3. **Pop nhiều hơn số lượng Push.**
4. **Push giá trị `null`.**
5. **Peek khi ngăn xếp có đúng 1 phần tử.**
6. **Peek khi ngăn xếp rỗng.**
7. **Push lượng lớn phần tử (Ví dụ: 10 triệu) làm cạn kiệt Heap Memory.**
8. **Interleaving Push và Pop:** Chuỗi chèn rút xen kẽ, ví dụ: Push, Pop, Push, Pop.
9. **Trạng thái `size` sau nhiều lần EmptyStackException.**
10. **Push đối tượng thay vì kiểu nguyên thủy.**
11. **Sử dụng trên Môi trường Đa luồng (Multithread) không đồng bộ.**
12. **Sửa đổi nội dung phần tử được push vào từ bên ngoài (Mutable objects).**
13. **Push phần tử đầu tiên vào ngăn xếp rỗng (cập nhật từ null -> node).**
14. **So sánh địa chỉ `top` ở hai ngăn xếp khác nhau.**
15. **Hành vi của Garbage Collector khi Pop nhanh liên tục hàng triệu phần tử.**
16. **Truy cập đồng thời từ 2 luồng đều gọi `pop()`.**
17. **Gọi `size()` lúc ngăn xếp rỗng phải đảm bảo là 0.**
18. **Cấu trúc lại đối tượng Node nếu cần tái sử dụng (Object Pooling).**
19. **Kế thừa và overidding (nếu class không final).**
20. **Lưu trữ chính ngăn xếp bên trong ngăn xếp đó.**
21. **Kích thước của ngăn xếp vượt giới hạn Integer.MAX_VALUE.**
22. **Push/Pop trong vòng lặp vô hạn (kiểm tra rò rỉ bộ nhớ thực tế).**
23. **Truyền kiểu nguyên thủy thông qua Autoboxing làm giảm hiệu năng.**
24. **Sử dụng Custom Object có hashCode và equals phức tạp được đẩy vào Stack.**
25. **`top` bị reflection truy cập và thay đổi.**
26. **Khuôn mẫu Serialization nếu ngăn xếp cần Serialize.**
27. **Clear toàn bộ ngăn xếp (thiếu method `clear()`).**
28. **In ra toàn bộ phần tử trong ngăn xếp (iterator/toString) thay đổi luồng.**
29. **Hiệu ứng phân mảnh heap do các thao tác chèn/xóa liên tục.**
30. **Sử dụng tham chiếu Weak/Soft reference trong nội dung Node.**

## 16. Optimization (Tối ưu hóa)
- **Object Pooling:** Giữ lại một danh sách các `Node` đã `pop()` vào một "pool" thay vì đẩy cho Garbage Collector. Khi có `push()`, lấy `Node` từ pool ra sử dụng. Tránh phân mảnh bộ nhớ và giảm áp lực GC.
- **Escape Analysis:** JVM có thể 최 ưu hóa đối tượng Node trên ngăn xếp CPU nếu ngăn xếp chỉ tồn tại giới hạn trong 1 hàm cục bộ.

## 17. Best Practices (Thực hành tốt nhất)
- Khai báo class Node là `static` (nếu nằm trong Inner class) để không ngầm chứa tham chiếu của Outer class.
- Thiết kế trường dữ liệu là `final` bên trong `Node` khi có thể để tránh bị sửa đổi.
- Kế thừa hoặc dùng `Deque` (`ArrayDeque`) từ Java Collection Framework thay vì tự xây dựng từ con số 0 trong Production, ngoại trừ yêu cầu đặc thù.
- Sử dụng `isEmpty()` thay vì `size() == 0` để cải thiện mức độ rõ ràng của code.

## 18. Benchmark (Đánh giá hiệu năng)
- **Array-based Stack** vs **Linked-List Stack**:
  - Array-based thao tác trên mảng liên tục, tận dụng CPU Cache, tốc độ nhanh gấp 2-5 lần so với Linked-List-based ở bài toán nhỏ.
  - Linked-List Stack không bao giờ bị đứng hệ thống tạm thời do Array Resize, thời gian phản hồi (latency) vô cùng ổn định (Predictable latency).
  - Linked-List Stack ngốn bộ nhớ gấp khoảng 3-4 lần do overhead từ object.

## 19. Unit Testing (Kiểm thử đơn vị)
```java
@Test
public void testPushAndPop() {
    LinkedListStack<Integer> stack = new LinkedListStack<>();
    stack.push(10);
    stack.push(20);
    assertEquals(20, stack.pop());
    assertEquals(10, stack.pop());
    assertTrue(stack.isEmpty());
}

@Test(expected = EmptyStackException.class)
public void testEmptyPop() {
    LinkedListStack<Integer> stack = new LinkedListStack<>();
    stack.pop();
}
```

## 20. Interview Questions (20 Câu hỏi phỏng vấn)
1. **So sánh cấu trúc Stack cài đặt bằng mảng và bằng danh sách liên kết.**
2. **Bạn sẽ chèn/xóa vào phần đầu (Head) hay phần cuối (Tail) của Linked List khi làm Stack? Vì sao?**
3. **Điều gì xảy ra với bộ nhớ nếu chúng ta liên tục thực hiện push và pop trong Linked-List Stack?**
4. **Làm thế nào để cài đặt Min Stack có độ phức tạp O(1) dùng Linked List?**
5. **Overhead bộ nhớ của Linked-List Stack đến từ đâu?**
6. **Làm thế nào để kiểm tra nếu một chuỗi đóng mở ngoặc là hợp lệ dùng Stack?**
7. **Có cần khai báo Node class là `static` không? Tại sao?**
8. **Viết một hàm đảo ngược các phần tử trong ngăn xếp chỉ dùng đệ quy (không dùng bộ nhớ phụ).**
9. **Làm thế nào để ngăn chặn race conditions trong Linked-List Stack khi chạy đa luồng?**
10. **Trường hợp nào thì Linked-List Stack bị `OutOfMemoryError`?**
11. **Làm sao để triển khai hàm `clone()` deep-copy cho một Linked-List Stack?**
12. **Nếu muốn duyệt qua tất cả phần tử trong Stack, bạn sẽ cung cấp Iterator hoạt động như thế nào?**
13. **Singly Linked List hay Doubly Linked List tốt hơn cho thao tác Stack?**
14. **Garbage Collector ảnh hưởng đến hiệu năng của Stack này như thế nào so với Array-based?**
15. **Làm thế nào để loại bỏ phần tử ở giữa ngăn xếp (giả sử kích thước là lẻ)?**
16. **Làm sao để xóa tất cả các phần tử (clear) khỏi ngăn xếp nhanh nhất?**
17. **Cấu trúc JVM sinh ra giới hạn thế nào cho một Node object?**
18. **Có thể dùng Linked-List Stack để giải bài toán Hậu tố (Postfix expression) như thế nào?**
19. **Nếu Stack bị lưu trữ tham chiếu vòng (Circular reference) ở Next pointer thì hậu quả là gì?**
20. **Kể tên vài thành phần nội bộ của JDK sử dụng cấu trúc tương tự Linked-List Stack.**

## 21. Practice Problems Link (Liên kết bài tập)
Xem file đính kèm: `03-Linked-List-Based-Stack-Problems.md` để rèn luyện tư duy thuật toán thông qua 30 bài tập.

## 22. Pattern Recognition (Nhận diện mẫu)
Nhận diện cấu trúc Stack trong các bài toán đồ thị (Duyệt theo chiều sâu - DFS), duyệt qua biểu thức toán học (Infix, Postfix, Prefix), và các vấn đề đệ quy sâu cần bộ nhớ Heap để tránh StackOverflow.

## 23. Real Case Study (Nghiên cứu tình huống)
Hệ thống **Undo/Redo** trong các trình soạn thảo văn bản (như Microsoft Word) thường sử dụng hai cấu trúc Stack (Undo Stack và Redo Stack). Do số lượng action là không xác định, sử dụng cấu trúc Linked List có thể là một mô hình hợp lý giúp giới hạn dung lượng chỉ phụ thuộc vào RAM tổng thể, đồng thời loại bỏ độ trễ khi cần tăng dung lượng lưu trữ đột ngột (resize array).

## 24. Summary & Checklist (Tóm tắt & Danh sách kiểm tra)
- [x] Hiểu về cấu trúc Singly Linked List để cài đặt Stack.
- [x] Nắm rõ Push/Pop ở vị trí Head đạt được $O(1)$.
- [x] Nhận thức được nhược điểm Memory Overhead và Cache Miss.
- [x] So sánh được với Array-based Stack trong từng điều kiện ngữ cảnh.
- [x] Trả lời được ít nhất 10/20 câu hỏi phỏng vấn cơ bản.
