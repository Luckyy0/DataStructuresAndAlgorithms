# Bài 2: Hàng Đợi Vòng (Circular Queue / Ring Buffer)

## 1. Giới thiệu (Introduction)
Circular Queue (Hàng đợi vòng), còn được gọi là Ring Buffer, là một cấu trúc dữ liệu tuyến tính hoạt động theo nguyên lý FIFO (First In First Out), trong đó vị trí cuối cùng được kết nối lại với vị trí đầu tiên để tạo thành một vòng tròn. Điều này giúp tận dụng tối đa không gian lưu trữ so với hàng đợi dựa trên mảng tĩnh (Array-based queue).

## 2. Mục đích và Ứng dụng (Purpose & Applications)
- **Quản lý bộ nhớ đệm (Buffering):** Ring Buffer được sử dụng rộng rãi trong các hệ thống multimedia (âm thanh, video streaming), nơi dữ liệu được đọc và ghi liên tục.
- **Lập lịch tiến trình (CPU Scheduling):** Các thuật toán như Round-Robin sử dụng Circular Queue để quản lý các tiến trình.
- **Quản lý lưu lượng mạng (Traffic Management):** Hàng đợi gói tin trong các router thường triển khai dưới dạng hàng đợi vòng.

## 3. Định nghĩa (Definition)
Circular Queue là một tập hợp các phần tử, trong đó việc thêm mới (enqueue) diễn ra ở phía sau (Rear) và việc loại bỏ (dequeue) diễn ra ở phía trước (Front). Đặc biệt, khi con trỏ Rear đạt đến kích thước tối đa của mảng, nó sẽ vòng lại vị trí đầu tiên nếu vị trí đó đang trống.

## 4. Cấu trúc dữ liệu (Data Structure)
Circular Queue thường được cài đặt bằng mảng (Array) cố định kích thước (`capacity`) và sử dụng hai con trỏ:
- `front`: trỏ tới phần tử đầu tiên của hàng đợi.
- `rear`: trỏ tới phần tử cuối cùng của hàng đợi.
(Hoặc dùng một biến `size` để đếm số lượng phần tử hiện tại nhằm đơn giản hóa việc kiểm tra Full/Empty).

## 5. Các thuật ngữ liên quan (Related Terminology)
- **Ring Buffer:** Tên gọi khác phổ biến của Circular Queue.
- **Modulo Arithmetic:** Toán tử chia lấy phần dư (`%`) để tính vị trí quay vòng.
- **Capacity:** Sức chứa tối đa của hàng đợi.
- **Wrap-around:** Quá trình con trỏ nhảy từ cuối mảng về đầu mảng.

## 6. Khắc phục hạn chế của Hàng đợi tĩnh (Overcoming Array-based Queue Limitations)
Trong hàng đợi dựa trên mảng tĩnh (Linear Array Queue), khi ta thực hiện `dequeue`, con trỏ `front` tiến lên. Dù các ô nhớ đầu mảng bị trống, con trỏ `rear` không thể quay lại đó nếu đã chạm cuối mảng (`rear == capacity - 1`). Điều này gây lãng phí bộ nhớ (wasted space) và yêu cầu phải di chuyển các phần tử lên đầu mảng với chi phí $O(N)$. Circular Queue khắc phục hoàn toàn vấn đề này bằng cách kết nối đầu và cuối mảng.

## 7. Khái niệm Ring Buffer (Ring Buffer Concept)
Ring Buffer coi mảng vật lý như một vòng tròn logic. Dữ liệu được ghi liên tục cho đến khi mảng đầy. Khi đọc dữ liệu, bộ nhớ được giải phóng. Con trỏ ghi (Rear) sẽ đuổi theo con trỏ đọc (Front). Nếu con trỏ ghi đuổi kịp con trỏ đọc, buffer được coi là đầy (Full).

## 8. Modulo Arithmetic cho Wrap-around
Để con trỏ vòng lại đầu mảng khi đến giới hạn cuối, Circular Queue sử dụng phép toán modulo (chia lấy dư):
- Tăng con trỏ `rear`: `rear = (rear + 1) % capacity`
- Tăng con trỏ `front`: `front = (front + 1) % capacity`
Cách tính này đảm bảo chỉ số luôn nằm trong khoảng `[0, capacity - 1]`.

## 9. Kiểm tra Trạng thái Đầy và Rỗng (Checking Full vs Empty state)
Có hai cách chính để kiểm tra trạng thái:
- **Cách 1: Sử dụng biến `size`:**
  - `Empty`: `size == 0`
  - `Full`: `size == capacity`
- **Cách 2: Để trống một ô nhớ (Không dùng biến `size`):**
  - Khởi tạo: `front = 0`, `rear = 0` (hoặc `rear = capacity - 1` tuỳ cài đặt).
  - `Empty`: `front == rear`
  - `Full`: `(rear + 1) % capacity == front` (Có nghĩa là ô tiếp theo của `rear` chính là `front`).

## 10. Hoạt động cốt lõi (Core Operations)
- `enqueue(value)`: Thêm phần tử vào cuối. Kiểm tra Full trước khi thêm. Cập nhật `rear`.
- `dequeue()`: Xóa phần tử ở đầu. Kiểm tra Empty trước khi xóa. Cập nhật `front`.
- `Front()`: Lấy giá trị đầu hàng đợi mà không xóa.
- `Rear()`: Lấy giá trị cuối hàng đợi.

## 11. Chi tiết về Cấu trúc và Thuật toán (Structure & Algorithm Details)
**Enqueue:**
1. Kiểm tra `isFull()`. Nếu đúng, ném lỗi hoặc trả về false.
2. Nếu `isEmpty()`, đặt `front = 0`, `rear = 0`.
3. Nếu không, cập nhật `rear = (rear + 1) % capacity`.
4. Gán `queue[rear] = value`.

**Dequeue:**
1. Kiểm tra `isEmpty()`. Nếu đúng, ném lỗi.
2. Lưu giá trị tại `front`.
3. Nếu `front == rear` (chỉ có 1 phần tử), đặt `front = -1`, `rear = -1` để reset.
4. Nếu không, cập nhật `front = (front + 1) % capacity`.
5. Trả về giá trị đã lưu.

## 12. Các biến thể (Variations)
- **Circular Deque (Double-ended queue vòng):** Cho phép thêm/xóa ở cả hai đầu `front` và `rear`.
- **Lock-free Ring Buffer:** Sử dụng trong lập trình đa luồng (multi-threading) không dùng lock, bằng các phép toán atomic.

## 13. So sánh với các cấu trúc dữ liệu khác (Comparison)
- **So với Linear Array Queue:** Tiết kiệm không gian hơn, không bị lỗi "đầy giả" (false full).
- **So với Linked List Queue:** Truy cập bộ nhớ liền kề (cache-friendly) tốt hơn, không phải cấp phát động (dynamic allocation) liên tục tốn thời gian.

## 14. Ưu điểm và Nhược điểm (Pros & Cons)
**Ưu điểm:**
- Sử dụng tối đa dung lượng bộ nhớ.
- Độ phức tạp thời gian cho enqueue/dequeue luôn là $O(1)$.
- Không tốn chi phí xin cấp phát/huỷ bộ nhớ liên tục.

**Nhược điểm:**
- Kích thước thường cố định từ đầu (static capacity).
- Resize mảng khá phức tạp do phải sắp xếp lại phần tử theo đúng trình tự (không thể copy trực tiếp).

## 15. Phân tích Độ phức tạp (Complexity Analysis)
- **Time Complexity:** $O(1)$ cho tất cả các thao tác (enqueue, dequeue, front, rear).
- **Space Complexity:** $O(N)$ với $N$ là sức chứa (capacity) của mảng.

## 16. Môi trường và Cách cài đặt (Implementation)
Dễ dàng cài đặt bằng mảng (Array) tĩnh trong C/C++, Java, hoặc mảng cấp phát trước dung lượng trong Python. Rất phổ biến trong phần cứng vi điều khiển (MCU) với bộ nhớ hạn chế.

## 17. Best Practices
- Sử dụng biến `size` để đơn giản hóa logic kiểm tra Empty/Full.
- Dung lượng (capacity) nên chọn là một lũy thừa của 2 ($2^N$). Khi đó phép tính modulo có thể thay thế bằng phép bitwise AND cực nhanh: `index & (capacity - 1)`.

## 18. 20 câu hỏi phỏng vấn (20 Interview Questions)
1. Sự khác biệt giữa Queue thông thường và Circular Queue là gì?
2. Tại sao Circular Queue lại tiết kiệm bộ nhớ hơn Queue dựa trên mảng tĩnh?
3. Trình bày công thức tính modulo để quay vòng con trỏ.
4. Làm thế nào để phân biệt trạng thái Full và Empty nếu không dùng biến `size`?
5. Ưu điểm của việc thiết kế dung lượng (capacity) là luỹ thừa của 2?
6. Làm thế nào để cài đặt Circular Deque?
7. Sự khác biệt giữa Circular Queue và Ring Buffer?
8. Nêu ứng dụng thực tế của Ring Buffer trong hệ điều hành?
9. Viết code `enqueue` trên bảng trắng.
10. Làm sao để resize Circular Queue khi đầy? (Phân tích khó khăn khi copy).
11. Ring Buffer đa luồng (Lock-free Ring Buffer) hoạt động thế nào?
12. Khi nào nên dùng Circular Queue thay vì Queue bằng Linked List?
13. Nếu chỉ có một con trỏ `rear` và biến `size`, có cài đặt được Circular Queue không?
14. Xử lý race condition trong Circular Queue ra sao?
15. Circular Queue có hỗ trợ lấy phần tử ở giữa (random access) không? (Có nhưng ít dùng, $O(1)$ nếu biết chỉ số).
16. Hậu quả của lỗi Off-by-one trong Wrap-around?
17. Circular Queue có tương đương với mảng vòng (Circular Array) không?
18. Độ phức tạp không gian (Space Complexity) của Circular Queue là bao nhiêu?
19. Giải thích "false full" (đầy giả) trong Linear Queue.
20. Trình bày ý tưởng thuật toán Josephus problem dùng Circular Queue.

## 19. 20 lỗi thường gặp (20 Common Bugs)
1. **Quên modulo (%):** Cộng chỉ số vượt ra ngoài phạm vi mảng.
2. **Modulo sai base:** Dùng `% size` thay vì `% capacity`.
3. **Phân biệt nhầm Full/Empty:** Khi không dùng biến `size`, `front == rear` xảy ra ở cả hai trạng thái.
4. **Khởi tạo sai vị trí ban đầu:** Khởi tạo nhưng thao tác đầu tiên cập nhật chỉ số bị lệch.
5. **Không reset về -1:** Khi dequeue phần tử cuối cùng, không set `front` và `rear` về trạng thái ban đầu.
6. **Lỗi chỉ số âm (Negative Index):** Khi lùi con trỏ `(front - 1) % capacity` ra số âm. Phải dùng `(front - 1 + capacity) % capacity`.
7. **Resize sai:** Khi hàng đợi đầy, nếu tăng gấp đôi mảng và chỉ copy dữ liệu 1-1, thứ tự logic bị phá vỡ.
8. **Race condition:** Cập nhật `front` và `rear` đồng thời đa luồng mà không có lock.
9. **Cập nhật kích thước (size) sai:** Quên `size++` khi enqueue hoặc `size--` khi dequeue.
10. **Lỗi Off-by-one:** Đọc mảng vượt quá chỉ số do phép so sánh.
11. **Overwrite dữ liệu:** Ghi đè lên dữ liệu khi `isFull()` bị bỏ qua.
12. **Tính toán sai `size`:** Tính `size` từ `front` và `rear` mà không cẩn thận số âm.
13. **Dequeue trên hàng đợi rỗng:** Bỏ qua kiểm tra `isEmpty`.
14. **So sánh nhầm pointer và index:** Khi cài đặt bằng con trỏ thuần trong C.
15. **Lỗi logic mảng:** Bắt đầu index từ 1 thay vì 0 dẫn tới `%` sai.
16. **Xóa một phần tử bất kì:** Gây xáo trộn toàn bộ Ring Buffer.
17. **Cập nhật `rear` trước khi ghi:** Gây mất ô đầu tiên.
18. **Không free memory (Memory Leak):** Dequeue phần tử là object nhưng không xoá liên kết reference.
19. **Copy constructor thiếu vòng:** Hàm copy Circular Queue không xử lý đúng vòng lặp.
20. **Tạo mảng kích thước quá lớn:** Gây lãng phí vùng nhớ hoặc tràn Stack/Heap.

## 20. 30 trường hợp biên (30 Edge Cases)
1. `capacity = 1`.
2. `capacity = 0` (Nên throw error).
3. Hàng đợi rỗng, thực hiện `dequeue()`.
4. Hàng đợi rỗng, thực hiện `Front()`.
5. Hàng đợi rỗng, thực hiện `Rear()`.
6. Hàng đợi đầy, thực hiện `enqueue()`.
7. Hàng đợi vừa đầy, thực hiện `dequeue()` rồi `enqueue()` lặp lại.
8. `rear` nằm ngay trước `front` trong bộ nhớ vật lý.
9. `front` trùng `rear` (khi mảng có 1 phần tử).
10. `front` nằm ở cuối mảng (`capacity - 1`), `rear` nằm ở đầu mảng (`0`).
11. Enqueue liên tục cho đến đầy, sau đó Dequeue toàn bộ.
12. Dequeue phần tử duy nhất trong mảng.
13. Con trỏ `rear` ở cuối mảng chuẩn bị wrap-around.
14. Con trỏ `front` ở cuối mảng chuẩn bị wrap-around.
15. Không có phần tử nào được thêm nhưng vẫn gọi hàm trả trạng thái.
16. `front = 0`, `rear = capacity - 1`.
17. Queue chứa các đối tượng cực lớn (cần cẩn thận giải phóng bộ nhớ khi dequeue).
18. Yêu cầu Resize khi hàng đợi đang ở trạng thái vòng (front > rear).
19. Hàng đợi lưu số âm.
20. Hàng đợi lưu toàn bộ là giá trị `null` / `0`.
21. Cấu hình để Queue luôn ghi đè phần tử cũ nhất khi đầy (chế độ Lossy Ring Buffer).
22. Các hoạt động xen kẽ liên tục (1 enqueue - 1 dequeue) giữ nguyên `size`.
23. `size = capacity - 1` (chuẩn bị đầy).
24. Con trỏ `rear` chạy vòng nhiều lần qua giới hạn mảng.
25. Thao tác trên hàng đợi có kích thước nhỏ nhất có thể hoạt động được.
26. Gọi `isFull()` ngay sau khi `dequeue()`.
27. Lỗi chia cho 0 nếu `capacity` chưa được set up hợp lý.
28. Queue kích thước rất lớn nhưng chỉ xài một mẩu nhỏ lặp đi lặp lại.
29. Reset toàn bộ hàng đợi về trạng thái ban đầu (`Clear()`).
30. Khởi tạo mảng động (Dynamic Array) bên dưới Circular Queue gây lãng phí bộ nhớ khi re-allocation.

## 21. Tài liệu tham khảo (References)
- *Introduction to Algorithms (CLRS)* - Cấu trúc dữ liệu cơ bản.
- LeetCode Problems: Circular Queue / Circular Deque.

## 22. Bài tập thực hành (Practice Problems)
- Xem File bài tập `02-Circular-Queue-Problems.md` để rèn luyện kỹ năng với 30 bài tập đi kèm đáp án.

## 23. Kết luận (Conclusion)
Circular Queue là một cấu trúc dữ liệu thanh lịch để khắc phục vấn đề lãng phí không gian của hàng đợi tĩnh. Việc sử dụng toán tử modulo `%` là phương pháp tuyệt vời để con trỏ có thể vòng lại (wrap-around). Hiểu rõ sự khác biệt giữa mảng vật lý và vòng tròn logic là chìa khóa để triển khai thành công các hệ thống Ring Buffer hiệu năng cao.

## 24. Checklist Ôn tập
- [ ] Phân biệt được khi nào dùng Circular Queue và Linear Queue.
- [ ] Cài đặt thành công `enqueue`, `dequeue` với thời gian $O(1)$.
- [ ] Nắm vững phép toán `modulo` trên chỉ số mảng.
- [ ] Xử lý đúng các trường hợp biên và wrap-around.
