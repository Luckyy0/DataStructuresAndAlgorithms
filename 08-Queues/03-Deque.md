# Chuyên đề: Deque (Double-ended Queue)

## 1. Khái niệm cơ bản (Introduction)
**Deque** (viết tắt của *Double-ended Queue*, phát âm là "deck") là một cấu trúc dữ liệu hàng đợi hai đầu. Không giống như Queue thông thường (chỉ thêm ở cuối và lấy ở đầu) hay Stack (chỉ thêm và lấy ở cùng một đầu), Deque cho phép thêm và xóa phần tử ở cả **hai đầu** của cấu trúc dữ liệu.

## 2. Các thao tác cơ bản (Basic Operations)
Các thao tác cốt lõi trên Deque đều có độ phức tạp thời gian $O(1)$:
- **Insert Front:** Thêm phần tử vào đầu Deque.
- **Insert Last:** Thêm phần tử vào cuối Deque.
- **Delete Front:** Xóa phần tử khỏi đầu Deque.
- **Delete Last:** Xóa phần tử khỏi cuối Deque.
- **Get Front:** Lấy phần tử ở đầu (không xóa).
- **Get Last:** Lấy phần tử ở cuối (không xóa).

## 3. Tại sao cần cấu trúc dữ liệu này (Why needed)
- Cung cấp sự **linh hoạt tối đa**: Có thể hoạt động độc lập như một Queue (FIFO) hoặc một Stack (LIFO).
- Thay thế hoàn hảo cho lớp `Stack` cũ trong Java (vốn kế thừa từ `Vector` và có hiệu năng kém do đồng bộ hóa).
- Là nền tảng để giải quyết các bài toán tối ưu trên các khoảng tuần tự (Sliding Window, Monotonic Deque).

## 4. Ứng dụng thực tế (Real-world applications)
1. **Lịch sử duyệt web (Browser History):** Nút Back/Forward có thể được triển khai qua một Deque.
2. **Thuật toán Work Stealing (Lập lịch đa luồng):** Các luồng (thread) xử lý công việc từ đầu Deque của chính nó. Nếu hết việc, nó có thể "ăn cắp" công việc từ cuối Deque của luồng khác. (Được dùng trong `ForkJoinPool` của Java).
3. **Thao tác Undo/Redo:** Trong các trình soạn thảo văn bản.
4. **Hệ thống cache:** Một số chiến lược lưu bộ nhớ đệm (như LRU, LFU) thường dùng Deque kết hợp HashMap.

## 5. So sánh với các cấu trúc dữ liệu khác (Comparison)

| Tiêu chí | Stack (Ngăn xếp) | Queue (Hàng đợi) | Deque (Hàng đợi hai đầu) |
| :--- | :--- | :--- | :--- |
| **Nguyên lý** | LIFO (Last In First Out) | FIFO (First In First Out) | Hỗ trợ cả LIFO và FIFO |
| **Thêm phần tử** | Chỉ ở Đỉnh (Top) | Chỉ ở Cuối (Rear) | Ở cả Đầu (Front) và Cuối (Rear)|
| **Xóa phần tử** | Chỉ ở Đỉnh (Top) | Chỉ ở Đầu (Front) | Ở cả Đầu (Front) và Cuối (Rear)|

## 6. Độ phức tạp thời gian & không gian (Time & Space Complexity)
- **Time Complexity:** 
  - Insert / Delete (Front & Rear): $O(1)$
  - Search / Access ngẫu nhiên: $O(N)$
- **Space Complexity:** $O(N)$ cho bộ nhớ lưu trữ các phần tử.

## 7. Cấu trúc dữ liệu bên dưới (Underlying Data Structures)
Deque thường được triển khai dưới hai hình thức:
1. **Circular Array (Mảng vòng):** Quản lý hai con trỏ `head` và `tail`. Khi đến cuối mảng, con trỏ vòng lại từ đầu.
2. **Doubly Linked List (Danh sách liên kết kép):** Mỗi node có con trỏ trỏ tới node trước (`prev`) và node sau (`next`). Dễ dàng thay đổi ở cả hai đầu.

## 8. Interface Deque trong Java
Trong Java Collections Framework, `java.util.Deque` extends `java.util.Queue`.
Các phương thức phổ biến (tương ứng giữa quăng ngoại lệ và trả về giá trị đặc biệt):

| Thao tác | Throw Exception (Nếu lỗi) | Return Special Value (Null/False) |
| :--- | :--- | :--- |
| Insert Front | `addFirst(e)` | `offerFirst(e)` |
| Insert Last | `addLast(e)` | `offerLast(e)` |
| Remove Front | `removeFirst()` | `pollFirst()` |
| Remove Last | `removeLast()` | `pollLast()` |
| Examine Front | `getFirst()` | `peekFirst()` |
| Examine Last | `getLast()` | `peekLast()` |

## 9. `ArrayDeque` vs `LinkedList`
Cả hai đều implement interface `Deque`, nhưng có sự khác biệt rõ rệt:
- **`ArrayDeque`**: Dùng mảng nội bộ (Circular Array). Nhanh hơn, sử dụng bộ nhớ hiệu quả hơn (không sinh ra object cho từng node), thao tác với Cache CPU tốt hơn. **Không cho phép phần tử `null`**.
- **`LinkedList`**: Dùng Doubly Linked List. Thích hợp nếu số lượng phần tử thay đổi cực kỳ thất thường, ít tốn chi phí resize mảng. Cho phép phần tử `null`. Tốn nhiều bộ nhớ hơn do overhead của các node con trỏ.
*Khuyên dùng*: Luôn ưu tiên `ArrayDeque` trừ khi có lý do đặc biệt phải dùng `LinkedList`.

## 10. Sử dụng Deque như Stack và Queue
- **Như Stack (LIFO):** Sử dụng các hàm `push(e)` (tương đương `addFirst(e)`) và `pop()` (tương đương `removeFirst()`). `ArrayDeque` được Java khuyên dùng thay cho class `Stack`.
- **Như Queue (FIFO):** Sử dụng các hàm `offer(e)` (tương đương `offerLast(e)`) và `poll()` (tương đương `pollFirst()`).

## 11. Các mô hình thuật toán phổ biến (Monotonic Deque pattern)
**Hàng đợi hai đầu đơn điệu (Monotonic Deque)** là mẫu thuật toán nâng cao, rất mạnh để giải quyết các bài toán "Cửa sổ trượt lớn nhất/nhỏ nhất" (Sliding Window Maximum/Minimum).
- **Quy tắc:** Duy trì các phần tử trong Deque theo một thứ tự đơn điệu (tăng dần hoặc giảm dần).
- Khi thêm phần tử mới, chúng ta loại bỏ tất cả các phần tử ở cuối Deque mà "nhỏ hơn" (hoặc lớn hơn) phần tử mới, vì chúng đã trở nên vô dụng so với phần tử mới (phần tử mới xuất hiện muộn hơn và có giá trị tốt hơn).
- Ở đầu Deque, loại bỏ các phần tử nằm ngoài "cửa sổ trượt".
- **Kết quả:** Đầu của Deque luôn là phần tử lớn nhất/nhỏ nhất trong cửa sổ hiện tại.

## 12. Triển khai Deque cơ bản (Java Implementation)
```java
// Triển khai Deque bằng Circular Array cơ bản
public class MyDeque<T> {
    private Object[] arr;
    private int head, tail, size, capacity;

    public MyDeque(int capacity) {
        this.capacity = capacity;
        arr = new Object[capacity];
        head = 0; tail = 0; size = 0;
    }

    public boolean isFull() { return size == capacity; }
    public boolean isEmpty() { return size == 0; }

    public void addFirst(T val) {
        if (isFull()) throw new IllegalStateException("Deque is full");
        head = (head - 1 + capacity) % capacity;
        arr[head] = val;
        size++;
    }

    public void addLast(T val) {
        if (isFull()) throw new IllegalStateException("Deque is full");
        arr[tail] = val;
        tail = (tail + 1) % capacity;
        size++;
    }

    public T removeFirst() {
        if (isEmpty()) throw new IllegalStateException("Deque is empty");
        T val = (T) arr[head];
        arr[head] = null; // Help GC
        head = (head + 1) % capacity;
        size--;
        return val;
    }
    // Các phương thức removeLast, getFirst, getLast...
}
```

## 13. Cách thiết kế test cases (Test cases design)
- Kiểm thử các chức năng cơ bản: thêm đầu, thêm cuối, xóa đầu, xóa cuối, xem phần tử.
- Kiểm thử Deque rỗng: Gọi các hàm `remove()` có quăng exception không? Gọi các hàm `poll()` có trả về `null` không?
- Kiểm thử Deque đầy (với giới hạn capacity).
- Đảm bảo khi dùng Circular Array, việc con trỏ vòng qua ranh giới cuối/đầu mảng không bị lỗi.

## 14. 20 Common Bugs
1. Nhầm lẫn giữa `pollFirst()` và `removeFirst()` khi Deque rỗng (`removeFirst` ném ngoại lệ).
2. Dùng toán tử modulo sai khi giảm con trỏ (ví dụ `head - 1 % n` thay vì `(head - 1 + n) % n`).
3. Khởi tạo `head` và `tail` sai quy ước, dẫn đến phần tử đầu tiên bị lưu nhầm vị trí.
4. Quên tăng/giảm biến `size` khi thêm hoặc xóa.
5. So sánh phần tử bằng `==` thay vì `.equals()` trong danh sách lưu trữ object.
6. Khi phần tử bị xóa từ Deque dựa trên mảng, không gán `null` (gây memory leak).
7. Resize array sai logic ở bước chép đè dữ liệu đối với Circular Array.
8. ArrayDeque: Cố gắng chèn phần tử `null` (ném ra `NullPointerException`).
9. Nhầm lẫn các method giả lập Stack của Deque (push/pop) với logic của Queue.
10. `Iterator` của Deque có thể đi lùi (dùng `descendingIterator()`) nhưng code người dùng lại vòng lặp tuần tự.
11. Không khởi tạo đủ kích thước lớn trong bài tập Monotonic Deque dẫn tới OutOfMemory.
12. Xóa sai phần tử khỏi Deque trong thuật toán Sliding Window (quên check index cửa sổ trượt).
13. Kiểm tra rỗng (isEmpty) không đồng bộ trong môi trường đa luồng (multi-threading).
14. Lặp qua Deque trong khi đang sửa đổi Deque (ném `ConcurrentModificationException`).
15. Quên không làm mới lại Deque (clear) giữa các test case riêng biệt trong lập trình thi đấu.
16. Nhầm lẫn `peekLast()` với `pollLast()`, chỉ đọc mà tưởng là đã xóa.
17. Concurrent access vào `ArrayDeque` dẫn đến trạng thái sai lệch (nên dùng `ConcurrentLinkedDeque`).
18. Xử lý logic tăng `tail` bằng phép cộng dồn không qua modulo khiến tràn biến index.
19. `Deque.remove(Object)`: Xóa phần tử đầu tiên gặp được (có thể không đúng ý đồ mong muốn xóa từ cuối lên).
20. Bỏ qua giá trị khởi tạo capacity mặc định của `ArrayDeque` (16) khiến resize diễn ra quá nhiều nếu dữ liệu lớn.

## 15. 30 Edge Cases
1. Deque rỗng (Empty Deque).
2. Deque có duy nhất 1 phần tử (Single element).
3. Deque đầy (nếu dùng mảng tĩnh) và vẫn muốn chèn thêm (Full Deque).
4. Thực hiện `removeFirst` liên tục đến khi rỗng.
5. Thực hiện `removeLast` liên tục đến khi rỗng.
6. Chèn phần tử liên tục vào đầu (Head cross bounds).
7. Chèn phần tử liên tục vào cuối (Tail cross bounds).
8. Xen kẽ `addFirst` và `addLast`.
9. Thêm từ đầu rồi xóa từ cuối (FIFO mô phỏng).
10. Thêm từ đầu rồi xóa từ đầu (LIFO mô phỏng).
11. Truy xuất các phần tử bằng `getFirst` và `getLast` khi mảng rỗng.
12. Yêu cầu xóa một phần tử cụ thể không tồn tại trong Deque.
13. Thêm một số lượng phần tử vượt qua `Integer.MAX_VALUE` (Tràn bộ nhớ / Overflows).
14. Đảo ngược toàn bộ các thao tác (reverse usage pattern).
15. Array Resize kích hoạt khi con trỏ `head` nằm đằng sau `tail`.
16. Array Resize kích hoạt khi con trỏ `head` ở đúng index 0.
17. Clear() được gọi liên tục.
18. Thêm phần tử `null` vào `LinkedList` implementation của Deque.
19. Monotonic Deque có tất cả các phần tử đầu vào bằng nhau.
20. Monotonic Deque với mảng đầu vào tăng ngặt (Strictly increasing).
21. Monotonic Deque với mảng đầu vào giảm ngặt (Strictly decreasing).
22. Sliding window có kích thước $K$ lớn hơn kích thước mảng.
23. Sliding window với kích thước $K = 1$.
24. Lưu các index có giá trị cực lớn vào Deque.
25. Iterator duyệt qua một Deque có các phép thêm/xóa xen kẽ.
26. Khởi tạo custom Deque bằng capacity <= 0.
27. Đẩy các số âm lớn vào Monotonic Deque tính khoảng cách max.
28. Kiểm tra tính `contains()` của object bị đột biến trạng thái (mutated).
29. Gọi `pop()` trên `LinkedList` rỗng (NoSuchElementException).
30. Khởi tạo `ArrayDeque` với Collection rỗng.

## 16. 20 Interview Questions
1. So sánh `Stack`, `Queue` và `Deque`. Nên sử dụng chúng trong ngữ cảnh nào?
2. Tại sao Java doc khuyên không nên dùng class `Stack` mà nên chuyển sang dùng `Deque` (`ArrayDeque`)?
3. Trình bày cách xây dựng Monotonic Deque cho bài toán Sliding Window Maximum.
4. Triển khai cấu trúc dữ liệu Deque bằng Mảng vòng (Circular Array).
5. Triển khai cấu trúc dữ liệu Deque bằng Danh sách liên kết kép (Doubly Linked List).
6. Làm thế nào để implement Deque chỉ bằng 2 Queue?
7. Làm thế nào để mô phỏng 1 Queue bằng 2 Stack? Nhưng nếu dùng 2 Deque thì sao?
8. Phân biệt các phương thức `offer/poll/peek` với `add/remove/element` trong Deque.
9. Có thể sử dụng Deque để kiểm tra chuỗi Palindrome (chuỗi đối xứng) như thế nào?
10. Khi nào `ArrayDeque` chậm hơn `LinkedList` trong môi trường thực tế?
11. Triển khai hệ thống LRU Cache sử dụng HashMap và Deque (Double Linked List).
12. Monotonic Deque là gì? Tại sao độ phức tạp của nó là O(N) dù có 2 vòng lặp lồng nhau?
13. Cho bài toán: Thiết kế Deque có khả năng lấy ra phần tử Middle (ở giữa) trong O(1). Bạn giải quyết thế nào?
14. Thiết kế cấu trúc `MinStack` bằng cách sử dụng Deque.
15. Giải bài toán Constrained Subsequence Sum với sự trợ giúp của Deque.
16. Phân tích memory overhead (lãng phí bộ nhớ) giữa việc dùng các Nodes của DoublyLinkedList và Array liên tục của ArrayDeque.
17. Cách `ArrayDeque` xử lý resize khi mảng đầy diễn ra như thế nào? Bitwise operation có được dùng không?
18. Làm sao để sử dụng `ConcurrentLinkedDeque` hiệu quả?
19. Mô phỏng thuật toán "Lịch trình công việc qua Work Stealing" dùng mảng các Deque.
20. Bài toán Shortest Subarray with Sum at Least K: Tại sao cần kết hợp Prefix Sum và Monotonic Deque?

## 17. Kinh nghiệm thực chiến (Practical experience)
- Trong C++, `std::deque` không hoàn toàn cấp phát liên tục mà chia thành các mảng bộ nhớ nhỏ liên kết với nhau. Tuy nhiên, trong Java, `ArrayDeque` là **một** mảng xoay vòng cấp phát liên tục.
- Trong lập trình thi đấu hoặc phỏng vấn bằng Java, hãy luôn `ArrayDeque<Integer> dq = new ArrayDeque<>();` để dùng thay Stack. Nó vừa chuẩn idiom hiện đại, vừa nhanh hơn nhiều.
- Khi làm bài có Deque để lưu trữ, đặc biệt là Monotonic Deque, thường chúng ta **lưu trữ chỉ số (index)** của phần tử thay vì giá trị (value), vì từ index ta tra ngược ra value được, đồng thời kiểm tra được khoảng cách window `(i - dq.peekFirst())`.

## 18. Checklist khi review code (Code review checklist)
- Đã khởi tạo Deque với đúng cấu trúc (`ArrayDeque` thay vì `LinkedList` nếu không lưu `null`) chưa?
- Khi pop phần tử trong while loop cho monotonic, đã đảm bảo `!dq.isEmpty()` trước chưa?
- Phân biệt rõ việc xóa ở đầu (`removeFirst`) hay cuối (`removeLast`) chưa?
- Khi mô phỏng Stack, đã nhất quán sử dụng `push`/`pop` chưa? Đừng mix `add` (cuối) và `pop` (đầu) gây loạn hành vi.

## 19. Hướng dẫn Debug
- Khi in biến `ArrayDeque`, Java gọi `toString()` in từ first đến last, trông tương tự cấu trúc `[first, ..., last]`.
- Nếu kích thước cửa sổ vượt mốc, kiểm tra điều kiện xóa đầu `if(!dq.isEmpty() && dq.peekFirst() <= i - k) dq.pollFirst();`. (Dùng `<` hay `<=` tùy vào bài).

## 20. FAQ (Câu hỏi thường gặp)
**H: Tại sao `ArrayDeque` lại cấm `null`?**
Đ: `ArrayDeque` sử dụng con trỏ và trả về `null` cho các phương thức `poll()`/`peek()` để biểu diễn trạng thái Deque rỗng. Nếu cho phép chứa giá trị `null`, không thể phân biệt được giá trị `null` thực tế và trạng thái rỗng.

**H: ArrayDeque có thead-safe không?**
Đ: Không. Để dùng Deque đa luồng, dùng `ConcurrentLinkedDeque` hoặc `LinkedBlockingDeque`.

## 21. Thuật ngữ tiếng Anh (English terminology)
- **Deque / Double-ended Queue**: Hàng đợi hai đầu.
- **Head / Front**: Đầu của hàng đợi.
- **Tail / Rear**: Cuối của hàng đợi.
- **Sliding Window**: Cửa sổ trượt.
- **Monotonic**: Đơn điệu (liên tục tăng hoặc giảm).
- **Circular Array**: Mảng vòng.
- **Capacity**: Sức chứa tối đa.

## 22. Lộ trình tự học (Self-study roadmap)
1. Bắt đầu bằng việc tự code class `MyDeque` sử dụng mảng vòng.
2. Nắm vững các methods của `ArrayDeque` trong Java Collections.
3. Học kỹ thuật duyệt mảng và duy trì tính đơn điệu (Monotonic Queue/Deque).
4. Áp dụng vào bài toán Sliding Window Maximum cơ bản.
5. Nâng cao kỹ năng kết hợp Deque với Prefix Sum, Dynamic Programming trong các bài khó trên LeetCode.

## 23. Bài tập thực hành (Practice problems)
(Vui lòng xem file `03-Deque-Problems.md` để tham khảo danh sách 30 bài tập về cấu trúc dữ liệu Deque, cùng với source code đầy đủ cho các bài tiêu biểu).

## 24. Tổng kết (Summary)
Deque là một cấu trúc dữ liệu cực kỳ linh hoạt và mạnh mẽ, tối ưu hoàn hảo việc truy xuất ở cả hai đầu bằng $O(1)$. Nhờ đặc tính này, Deque không chỉ có thể mô phỏng hiệu suất cao cho Stack và Queue, mà còn là trái tim của các thuật toán nâng cao như Sliding Window và Monotonic Array. Việc thành thạo Deque là yếu tố quyết định để vượt qua các vòng phỏng vấn chuyên sâu của các tập đoàn công nghệ.
