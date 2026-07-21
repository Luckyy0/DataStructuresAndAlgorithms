# Doubly Linked List (Danh Sách Liên Kết Đôi)

## 1. Metadata
- **Khóa học**: Java Data Structures and Algorithms
- **Chủ đề**: Doubly Linked List
- **Mức độ**: Intermediate
- **Mục tiêu**: Hiểu rõ cấu trúc Node với con trỏ `prev` và `next`, sự đánh đổi Time-Space, internals của `LinkedList` trong Java, và ứng dụng trong LRU Cache.

## 2. Purpose (Mục đích)
Mục đích của cấu trúc dữ liệu Doubly Linked List (DLL) là cung cấp khả năng duyệt qua các phần tử theo cả hai chiều (tới và lui), đồng thời cho phép chèn và xóa các phần tử ở bất kỳ vị trí nào với độ phức tạp $O(1)$ khi đã biết reference (tham chiếu) của Node.

## 3. Motivation (Động lực)
Singly Linked List có hạn chế lớn là chỉ có thể duyệt theo một chiều (forward). Khi muốn xóa một Node mà chỉ biết reference tới chính nó, ta vẫn cần phải duyệt từ đầu danh sách để tìm Node trước đó (`prev`). DLL giải quyết vấn đề này bằng cách lưu thêm một tham chiếu `prev` tại mỗi Node, đánh đổi thêm một chút bộ nhớ (Extra memory) để lấy tốc độ xóa $O(1)$.

## 4. Mathematical Foundation (Nền tảng toán học)
- **Kích thước**: $N$ phần tử.
- **Biểu diễn Node**: $V = (data, prev, next)$.
- **Bộ nhớ mỗi Node**: Kích thước object header + tham chiếu data + tham chiếu next + tham chiếu prev. Trong Java (64-bit JVM, UseCompressedOops), thường mất khoảng 24-32 bytes overhead cho mỗi Node.

## 5. Core Theory (Lý thuyết cốt lõi)
DLL là một tập hợp các Node. Mỗi Node bao gồm 3 phần:
1. `data`: Dữ liệu được lưu trữ.
2. `next`: Tham chiếu trỏ tới Node kế tiếp.
3. `prev`: Tham chiếu trỏ tới Node trước đó.
Các con trỏ `prev` của Head và `next` của Tail thường trỏ tới `null` (trong DLL tuyến tính) hoặc nối với nhau (trong Circular DLL).

## 6. Visual Explanation (Giải thích trực quan)

```mermaid
graph LR
    NULL1((null)) <-- prev --- A[Node 1]
    A --- next --> B[Node 2]
    B <-- prev --- A
    B --- next --> C[Node 3]
    C <-- prev --- B
    C --- next --> NULL2((null))
```

## 7. Java Implementation (Cài đặt bằng Java)
```java
public class DoublyLinkedList<T> {
    private static class Node<T> {
        T data;
        Node<T> prev;
        Node<T> next;

        Node(T data) {
            this.data = data;
        }
    }

    private Node<T> head;
    private Node<T> tail;
    private int size;

    public void addFirst(T data) {
        Node<T> newNode = new Node<>(data);
        if (head == null) {
            head = tail = newNode;
        } else {
            newNode.next = head;
            head.prev = newNode;
            head = newNode;
        }
        size++;
    }

    public void remove(Node<T> node) {
        if (node == null) return;
        if (node.prev != null) node.prev.next = node.next;
        else head = node.next;

        if (node.next != null) node.next.prev = node.prev;
        else tail = node.prev;

        node.prev = null;
        node.next = null; // Help GC
        size--;
    }
}
```

## 8. Step-by-Step (Từng bước)
**Xóa một Node có tham chiếu `node`**:
1. Cập nhật con trỏ `next` của `node.prev` trỏ tới `node.next`.
2. Cập nhật con trỏ `prev` của `node.next` trỏ tới `node.prev`.
3. Nếu `node` là `head`, cập nhật `head = node.next`.
4. Nếu `node` là `tail`, cập nhật `tail = node.prev`.
5. Đặt `node.prev` và `node.next` thành `null` (Garbage Collection).

## 9. Complexity Analysis (Phân tích độ phức tạp)
- **Time Complexity**:
  - Chèn đầu/cuối: $O(1)$
  - Xóa khi biết Node: $O(1)$
  - Tìm kiếm: $O(N)$
- **Space Complexity**: $O(N)$
- **Time-Space Trade-off**: Ta tiêu tốn thêm bộ nhớ cho con trỏ `prev` (đánh đổi Space) để nhận lại khả năng xóa/chèn tại Node bất kỳ trong thời gian $O(1)$ (tối ưu Time).

## 10. JVM Analysis (Phân tích JVM)
- Mỗi `Node` là một object trên Heap.
- Việc tạo nhiều Node có thể gây phân mảnh bộ nhớ (Memory fragmentation) và tăng áp lực lên Garbage Collector (GC), đặc biệt là Minor GC khi tạo và hủy liên tục.

## 11. OpenJDK Analysis (Phân tích OpenJDK)
Trong Java, `java.util.LinkedList` là một cấu trúc dữ liệu DLL hoàn chỉnh.
- Internals: Sử dụng class nội bộ tĩnh `Node<E>` chứa `item`, `next`, `prev`.
- `LinkedList` implements cả `List` và `Deque`.
- Không cấp phát mảng như `ArrayList`, nhưng overhead cho mỗi phần tử rất lớn.

## 12. Production Usage (Sử dụng trong thực tế)
DLL là lõi để xây dựng **LRU Cache** (Least Recently Used Cache) kết hợp với `HashMap`.
- `HashMap` giữ reference (tham chiếu) tới Node trong DLL ($O(1)$ access).
- Khi truy cập hoặc thêm mới, Node được di chuyển lên đầu DLL ($O(1)$ operation).
- Khi Cache đầy, Node ở cuối DLL bị xóa bỏ ($O(1)$ remove).

## 13. Design Decisions (Các quyết định thiết kế)
- Thường dùng hai Node giả (Dummy Head và Dummy Tail) còn gọi là `Sentinel nodes` để đơn giản hóa việc xử lý các điều kiện biên (không cần kiểm tra `null`).

## 14. Common Bugs (20 Các lỗi thường gặp)
1. Quên cập nhật `head` khi xóa Node đầu tiên.
2. Quên cập nhật `tail` khi xóa Node cuối cùng.
3. NullPointerException khi truy cập `node.next.prev`.
4. Quên cập nhật `prev` khi thêm Node mới vào đầu.
5. Tạo vòng lặp vô hạn do trỏ nhầm `next` và `prev`.
6. Lỗi Garbage Collection do không null hóa các tham chiếu đã xóa (Memory leak).
7. Xóa sai kích thước (size) khi thao tác với Node.
8. So sánh object bằng `==` thay vì `.equals()`.
9. Thêm Node vào danh sách trống không set cả head và tail.
10. Sắp xếp DLL bị mất liên kết `prev`.
11. Bỏ qua việc update head/tail trong thao tác di chuyển Node lên đầu (LRU).
12. ConcurrentModificationException khi duyệt bằng Iterator.
13. Hàm `clear()` không cắt đứt liên kết giữa các Node.
14. Đệ quy quá sâu khi in DLL (StackOverflowError).
15. Khởi tạo Dummy Head và Tail nhưng tính cả chúng vào size.
16. Thêm phần tử vào giữa nhưng quên trỏ `next` của Node hiện tại về phía trước.
17. Xóa toàn bộ DLL nhưng quên đưa head và tail về null.
18. Nhầm lẫn giữa Node index và Node value.
19. Iterator `hasPrevious()` trả về sai logic ở đầu danh sách.
20. Trộn 2 DLL làm đứt chuỗi `prev` của DLL thứ hai.

## 15. Edge Cases (30 Các trường hợp góc)
1. Thêm vào DLL đang rỗng.
2. Xóa khỏi DLL chỉ có 1 Node.
3. Xóa phần tử Head của DLL.
4. Xóa phần tử Tail của DLL.
5. Chèn trước Head.
6. Chèn sau Tail.
7. Đảo ngược DLL trống.
8. Đảo ngược DLL có 1 phần tử.
9. Tìm kiếm phần tử không tồn tại.
10. Xóa phần tử không thuộc DLL.
11. Truy cập chỉ số âm.
12. Truy cập chỉ số lớn hơn size.
13. Chèn vào chỉ số bằng size.
14. Lọc bỏ các Node liên tiếp giống nhau.
15. Chuyển đổi DLL sang Array khi rỗng.
16. Gọi `pop()` trên DLL rỗng.
17. Gọi `peek()` trên DLL rỗng.
18. Xóa nhiều Node có cùng giá trị.
19. Gộp (merge) một DLL với DLL rỗng.
20. Clone một DLL.
21. Serialize/Deserialize DLL vòng.
22. Chèn 1 Node tự trỏ vào chính nó.
23. Sử dụng Node từ DLL khác.
24. LRU Cache dung lượng 0.
25. LRU Cache dung lượng 1.
26. Update value trong LRU Cache.
27. Đẩy nhiều phần tử trùng lặp vào LFU/LRU.
28. Head và Tail trỏ cùng một vùng nhớ giả.
29. Thao tác Thread-safe trên DLL.
30. OutOfMemory khi size quá giới hạn mảng của JVM.

## 16. Optimization (Tối ưu hóa)
- Sử dụng **Dummy Node** (Sentinel Head & Tail) để loại bỏ toàn bộ khối lệnh `if (node.prev != null)`.
- Sử dụng Unrolled Linked List nếu cần locality của Cache trên CPU.

## 17. Best Practices (Thực hành tốt nhất)
- Hạn chế sử dụng `java.util.LinkedList` trong Production nếu không thực sự cần thiết do Memory Overhead lớn. Sử dụng `ArrayList` hoặc `ArrayDeque` thay thế cho đa số trường hợp.
- Khi cần implement LRU Cache, hãy kế thừa `LinkedHashMap` thay vì tự viết DLL trừ khi là yêu cầu phỏng vấn.

## 18. Benchmark (Đánh giá hiệu suất)
- So sánh với `ArrayList`: DLL nhanh hơn rõ rệt khi xóa một Node ở giữa bằng iterator ($O(1)$ vs $O(N)$). Nhưng cực kỳ chậm khi truy cập ngẫu nhiên (Random Access).
- Duyệt (Iteration): DLL thường chậm hơn do Cache Miss (các Node phân tán trên Heap).

## 19. Unit Testing (Kiểm thử vòng lập)
Kiểm tra kĩ các tính chất:
- Sau khi chèn, `size` tăng.
- Sau khi gọi `remove(node)`, đi từ `head` tới `tail` không còn `node` đó, và đi ngược từ `tail` về `head` cũng không còn `node` đó.

## 20. Interview Questions (20 Câu hỏi phỏng vấn)
1. Ưu điểm và nhược điểm của DLL so với Singly Linked List là gì?
2. Cách thiết kế LRU Cache sử dụng DLL và HashMap?
3. Tại sao cấu trúc `java.util.LinkedList` lại sử dụng DLL?
4. Đảo ngược DLL như thế nào?
5. Làm sao tìm phần tử ở giữa của DLL?
6. Cách chuyển đổi Binary Tree thành in-place DLL?
7. Flatten một Multilevel DLL như thế nào?
8. Tại sao việc xóa Node trong DLL lại có độ phức tạp O(1) nếu đã biết con trỏ?
9. Garbage Collector của Java xử lý DLL bị mất tham chiếu như thế nào?
10. Circular DLL khác gì với DLL thông thường?
11. Implement LFU Cache khó hơn LRU Cache ở điểm nào?
12. Có thể sử dụng DLL làm Queue không? So sánh với Circular Array.
13. Xóa tất cả các phần tử có giá trị X trong DLL mất bao lâu?
14. Sắp xếp DLL sử dụng thuật toán nào là hiệu quả nhất?
15. Lỗi Memory Leak có thể xảy ra trong tự implement DLL như thế nào?
16. Biến `modCount` trong `java.util.LinkedList` có tác dụng gì?
17. Cách swap hai Node trong DLL?
18. Xoay (Rotate) DLL theo k bước?
19. Giải thích Time-Space trade-off trong việc lưu trữ con trỏ `prev`.
20. Nếu bộ nhớ là một rào cản nghiêm trọng, làm thế nào để implement DLL chỉ bằng 1 con trỏ? (XOR Linked List - khái niệm C/C++, trong Java không thể).

## 21. Practice Problems Link (Liên kết bài tập)
Xem file `02-Doubly-Linked-List-Problems.md` trong cùng thư mục.

## 22. Pattern Recognition (Nhận diện mẫu)
- **Cần thêm/xóa nhanh ở giữa và có hỗ trợ tìm kiếm O(1)**: Pattern -> HashMap kết hợp với Doubly Linked List.
- **Xử lý trượt hoặc lịch sử truy cập**: Pattern -> Node di chuyển qua lại (LRU).

## 23. Real Case Study (Nghiên cứu tình huống thực tế)
- **Browser History**: Nút Back và Forward trên trình duyệt được implement thông qua DLL. Mỗi URL là một Node.
- **Text Editor**: Chức năng Undo/Redo (tuy có thể dùng Stack nhưng DLL cung cấp giới hạn linh hoạt).

## 24. Summary & Checklist (Tóm tắt & Danh sách kiểm tra)
- [x] Hiểu cấu trúc Node (prev, next, data).
- [x] Nắm rõ cách thêm/xóa phần tử O(1).
- [x] Hiểu được sự đánh đổi không gian bộ nhớ lấy thời gian.
- [x] Thành thạo việc ứng dụng Dummy Nodes.
- [x] Nắm được thiết kế cốt lõi của LRU Cache.
