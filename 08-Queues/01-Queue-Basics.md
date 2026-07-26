# 01. Queue Basics (Cơ bản về Hàng đợi)

## 1. Giới thiệu (Introduction)
**Queue** (Hàng đợi) là một cấu trúc dữ liệu tuyến tính, hoạt động dựa trên nguyên tắc **FIFO (First In First Out)**. Có nghĩa là phần tử nào được thêm vào trước sẽ được lấy ra trước, tương tự như việc xếp hàng mua vé ngoài đời thực.

## 2. Nguyên tắc FIFO (First In First Out)
- **First In**: Phần tử được thêm vào (Enqueue) sớm nhất sẽ đứng ở đầu (Front) của hàng đợi.
- **First Out**: Khi lấy phần tử ra (Dequeue), phần tử ở đầu sẽ được lấy ra đầu tiên.

## 3. Các thao tác cơ bản (Core Operations)
- **Enqueue(x)**: Thêm phần tử `x` vào cuối (Rear) của hàng đợi. Thời gian: O(1).
- **Dequeue()**: Loại bỏ và trả về phần tử ở đầu (Front) của hàng đợi. Thời gian: O(1).
- **Front() / Peek()**: Trả về phần tử ở đầu hàng đợi mà không loại bỏ nó. Thời gian: O(1).
- **Rear()**: Trả về phần tử ở cuối hàng đợi (tùy thuộc vào implementation). Thời gian: O(1).
- **IsEmpty()**: Kiểm tra xem hàng đợi có rỗng không. Thời gian: O(1).

## 4. Độ phức tạp (Complexity)
- **Time Complexity**: Enqueue O(1), Dequeue O(1), Peek O(1). (Tìm kiếm phần tử: O(N)).
- **Space Complexity**: O(N) với N là số lượng phần tử.

## 5. Interface `Queue` trong Java
Trong Java, `Queue` là một Interface kế thừa từ `Collection`. Các implementation phổ biến:
- `LinkedList`: Thực thi Queue sử dụng danh sách liên kết đôi. Phù hợp khi thêm/xóa nhiều.
- `ArrayDeque`: Thực thi Queue (và Deque) sử dụng mảng động (resizable array). Thường nhanh hơn `LinkedList` và tiết kiệm bộ nhớ hơn vì không có overhead của node.

## 6. Cài đặt bằng Mảng (Array-based Implementation)
- Cần duy trì hai con trỏ: `front` và `rear`.
- Vấn đề: Sau một số lần thêm/xóa, không gian phía trước mảng bị lãng phí. Giải pháp là sử dụng **Circular Queue** (hàng đợi vòng).

## 7. Cài đặt bằng Danh sách liên kết (Linked List-based)
- Cần con trỏ `head` (chỉ phần tử đầu) và `tail` (chỉ phần tử cuối).
- Enqueue tương đương với việc thêm vào cuối (thao tác trên `tail`).
- Dequeue tương đương với việc xóa ở đầu (thao tác trên `head`).
- Không bị giới hạn kích thước, nhưng tốn thêm bộ nhớ cho pointer.

## 8. So sánh Array-based và Linked List-based
- **Array-based (Circular Queue)**: Kích thước cố định (hoặc cần resize tốn kém), bộ nhớ liền kề giúp cache thân thiện.
- **Linked list-based**: Kích thước động, tốn overhead bộ nhớ cho pointer, cache miss nhiều hơn do phân mảnh bộ nhớ.

## 9. Ứng dụng thực tế (Real-world Usage)
- **Thuật toán BFS (Breadth-First Search)**: Queue là cốt lõi để duyệt đồ thị, cây theo chiều rộng.
- **Task Scheduling (Lập lịch tác vụ)**: Hàng đợi in, hàng đợi xử lý request trong Web Server.
- **Xử lý bất đồng bộ (Asynchronous processing)**: Message queues như RabbitMQ, Kafka.

## 10. Các biến thể của Queue
- **Circular Queue**: Tối ưu không gian mảng.
- **Priority Queue**: Các phần tử có độ ưu tiên, phần tử ưu tiên cao lấy ra trước (thường cài bằng Heap).
- **Deque (Double-ended Queue)**: Cho phép thêm/xóa ở cả 2 đầu.

## 11. Khi nào nên dùng Queue?
- Khi cần duy trì thứ tự xử lý theo đúng trình tự xuất hiện (FIFO).
- Khi xử lý dữ liệu theo luồng (streams) từ nhiều producer.

## 12. Khi nào KHÔNG nên dùng Queue?
- Khi cần truy cập ngẫu nhiên (random access) vào các phần tử (dùng Array/List).
- Khi cần lấy ra phần tử gần nhất được thêm vào (dùng Stack - LIFO).

## 13. Hệ thống Message Queue
- Mở rộng khái niệm Queue lên mức kiến trúc hệ thống, cho phép các microservices giao tiếp bất đồng bộ.

## 14. BlockingQueue trong Java Concurrency
- `BlockingQueue` là hàng đợi hỗ trợ thread-safe, tự động block (chờ) khi hàng rỗng (lúc lấy) hoặc hàng đầy (lúc thêm). Phù hợp cho mô hình Producer-Consumer.

## 15. Thiết kế bộ đệm (Buffer)
- CPU scheduling, I/O buffers thường sử dụng Queue để điều phối sự chênh lệch tốc độ giữa thiết bị nhận và gửi.

## 16. So sánh Queue và Stack
- Queue: FIFO (Công bằng, xử lý theo thời gian).
- Stack: LIFO (Truy vết lại quá trình, đệ quy, undo).

## 17. 20 Lỗi thường gặp (Common Bugs)
1. Quên cập nhật con trỏ `rear` khi enqueue.
2. Quên cập nhật con trỏ `front` khi dequeue.
3. Không xử lý trường hợp Queue rỗng khi gọi Dequeue (gây NullPointerException hoặc Underflow).
4. Không xử lý Queue đầy với Array-based (gây Overflow).
5. Null item insertion: Cố gắng thêm `null` vào Queue (nhiều collection không cho phép).
6. Sai điều kiện kiểm tra rỗng (`front == rear` hoặc `front > rear` tùy cách cài).
7. Circular Queue: Tính toán sai index `(rear + 1) % size`.
8. Memory leak trong Array-based khi không set null cho phần tử vừa dequeue.
9. Linked List Queue: Không cập nhật lại `tail` khi lấy ra phần tử cuối cùng (Queue thành rỗng).
10. Sử dụng ArrayList để làm Queue (xóa ở đầu mất O(N)).
11. ConcurrentModificationException khi duyệt Queue và thay đổi nó.
12. Thread-safety: Sử dụng Queue thường trong môi trường multi-thread mà không đồng bộ.
13. Nhầm lẫn giữa `poll()` (trả về null) và `remove()` (ném Exception) trong Java.
14. Nhầm lẫn giữa `peek()` và `element()`.
15. Không giải phóng bộ nhớ (đặc biệt trong C/C++) sau khi dequeue node.
16. Deadlock khi sử dụng `BlockingQueue` sai cách (dùng `.put()` và `.take()` trên cùng một thread).
17. PriorityQueue sai logic so sánh (`Comparator`).
18. Xóa sai phần tử bằng `remove(Object)` thay vì dequeue đầu hàng.
19. Quên tăng/giảm biến `size` khi tự implement.
20. Thêm dữ liệu vào biến Deque nhưng gọi nhầm các hàm Stack.

## 18. 30 Trường hợp biên (Edge Cases)
1. Hàng đợi rỗng (Empty Queue).
2. Hàng đợi có đúng 1 phần tử.
3. Hàng đợi đã đầy (với kích thước cố định).
4. Thao tác Enqueue sau khi Queue vừa đầy.
5. Thao tác Dequeue sau khi Queue vừa rỗng.
6. Thêm và xóa liên tục xen kẽ nhau 1 phần tử (Enqueue, Dequeue, Enqueue, ...).
7. Circular Queue khi `front` nằm sau `rear`.
8. Circular Queue khi `front` và `rear` chỉ chênh nhau 1 vị trí (Queue đầy).
9. Mảng chứa toàn giá trị `null` nhưng Queue không rỗng.
10. Phần tử được thêm vào là cực lớn hoặc cực nhỏ.
11. Số lượng thao tác vượt quá giới hạn integer (`MAX_VALUE`).
12. Linked List Queue khi bộ nhớ Heap đầy.
13. Thay đổi kích thước (resize) mảng khi `front` > `rear` trong Circular Queue.
14. Chèn trùng lặp phần tử.
15. Xóa phần tử ở giữa Queue (bằng iterator).
16. Clone một Queue và sửa đổi Queue gốc.
17. Serialize và Deserialize một Queue.
18. Hàng đợi không chứa định kiểu (Raw type).
19. Iterator đang duyệt thì có thao tác Dequeue.
20. `addAll` một collection rỗng vào Queue.
21. Truy cập mảng vật lý bên dưới Queue (reflection).
22. Khởi tạo Queue với kích thước bằng 0 hoặc âm.
23. `BlockingQueue`: Bị ngắt (Interrupted) khi đang đợi `take()`.
24. `ArrayDeque` tự động nhân đôi kích thước khi `head == tail`.
25. Thêm phần tử khi Thread khác đang `peek()`.
26. Queue chứa các phần tử tham chiếu vòng (Circular reference).
27. Đẩy các kiểu dữ liệu khác nhau vào cùng một Queue.
28. Queue lớn tràn bộ nhớ JVM (OOM).
29. Cố gắng sử dụng Queue trong đệ quy.
30. Priority Queue với tất cả phần tử có cùng độ ưu tiên.

## 19. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Queue khác gì Stack? Cấu trúc nào nên dùng cho BFS?
2. Hãy cài đặt Queue sử dụng 2 Stack. Độ phức tạp là bao nhiêu?
3. Cài đặt Stack sử dụng Queue như thế nào?
4. Circular Queue là gì và nó giải quyết vấn đề gì của Array-based Queue?
5. Trình bày cách cài đặt Deque (Double-ended queue).
6. Phân biệt `Queue` và `PriorityQueue`.
7. `ArrayDeque` và `LinkedList` trong Java, cái nào nhanh hơn khi dùng làm Queue? Vì sao?
8. Tại sao các message broker (Kafka, RabbitMQ) lại dùng kiến trúc Queue?
9. Trình bày bài toán Producer-Consumer sử dụng Queue.
10. Bạn sẽ thiết kế một Queue có giới hạn kích thước (Bounded Queue) như thế nào trong môi trường đa luồng?
11. `poll()` khác `remove()` như thế nào trong giao diện `Queue` của Java?
12. Cài đặt LRU Cache, bạn có dùng Queue không? Tại sao?
13. Làm thế nào để đảo ngược nửa đầu của một Queue?
14. Phân tích độ phức tạp thời gian khi cài đặt Queue bằng ArrayList.
15. Queue có thể dùng để phát hiện chu trình (cycle) trong đồ thị không?
16. Nêu ý tưởng sử dụng Queue để sinh các số nhị phân từ 1 đến N.
17. Tính toán mức độ phân mảnh bộ nhớ khi sử dụng Linked List Queue.
18. Trong OS, Round Robin CPU Scheduling sử dụng Queue như thế nào?
19. Nêu cách kiểm tra xem một String có phải Palindrome không chỉ sử dụng một Queue và một Stack.
20. Làm sao để tìm max trong Queue với độ phức tạp O(1) ở mọi thời điểm?

## 20. Queue vs PriorityQueue
- Khác biệt ở cách lấy phần tử. Queue theo FIFO thuần túy, PriorityQueue theo mức độ ưu tiên (Comparator hoặc Comparable).

## 21. Cài đặt Queue từ các cấu trúc khác
- **Từ 2 Stack**: Dùng Stack thứ nhất để Enqueue, Stack thứ hai để Dequeue (đổ sang nếu rỗng).

## 22. Tối ưu bộ nhớ
- Với kích thước biết trước, ArrayDeque tối ưu hơn LinkedList (không có object node).

## 23. Kinh nghiệm thực chiến (Best Practices)
- Luôn ưu tiên dùng `ArrayDeque` thay vì `LinkedList` trong Java nếu chỉ cần thao tác Queue thông thường.
- Cẩn thận với các API ném exception (`add`, `remove`, `element`) so với API trả về special value (`offer`, `poll`, `peek`).

## 24. Tổng kết & Tài liệu tham khảo
- Queue là cấu trúc dữ liệu nền tảng cho việc lập lịch, xử lý luồng sự kiện và duyệt cây/đồ thị. Nắm vững Circular Queue và Deque là chìa khóa để tối ưu hiệu suất.
- Tham khảo: Javadoc (`java.util.Queue`, `java.util.Deque`), "Introduction to Algorithms" (CLRS).
