# Bài 4: Hàng đợi ưu tiên (Priority Queue)

## 1. Giới thiệu chung (Introduction)
Priority Queue là một cấu trúc dữ liệu trừu tượng hoạt động giống như một queue bình thường nhưng có thêm một tính năng quan trọng: mỗi phần tử đều có một "độ ưu tiên" (priority) gắn liền với nó.

## 2. Priority Queue là gì? (Concept)
Trong Priority Queue, phần tử có độ ưu tiên cao sẽ được lấy ra trước phần tử có độ ưu tiên thấp. Nếu hai phần tử có cùng độ ưu tiên, chúng thường được lấy ra theo thứ tự chèn vào (FIFO) tùy thuộc vào cách triển khai cụ thể.

## 3. Priority Queue vs Standard Queue
- **Standard Queue**: Xử lý phần tử theo nguyên tắc FIFO (First In First Out). Phần tử nào vào trước sẽ ra trước.
- **Priority Queue**: Xử lý phần tử dựa trên độ ưu tiên. Bất kể thứ tự thêm vào là gì, phần tử ưu tiên nhất luôn được xử lý trước tiên.

## 4. Cơ chế hoạt động (How it works)
Khi bạn thêm một phần tử vào Priority Queue, nó sẽ tự động được sắp xếp vào đúng vị trí dựa trên giá trị ưu tiên. Quá trình lấy ra (poll/dequeue) sẽ luôn trả về phần tử nằm ở vị trí đầu tiên (cao nhất/thấp nhất tùy cấu hình).

## 5. Triển khai bằng Min Heap / Max Heap (Underlying implementation)
Priority Queue thường được triển khai dưới dạng Binary Heap (Cây nhị phân) do mang lại hiệu suất tốt.
- **Min Heap**: Gốc của cây (root) là phần tử nhỏ nhất. Phù hợp khi giá trị nhỏ có ưu tiên cao.
- **Max Heap**: Gốc của cây là phần tử lớn nhất. Phù hợp khi giá trị lớn có ưu tiên cao.
Việc dùng Array hay LinkedList để triển khai Priority Queue có độ phức tạp cao ở thao tác thêm hoặc xóa (O(N)), trong khi Heap giảm xuống còn O(log N).

## 6. Các thao tác cơ bản (Basic Operations)
- **Insert / Push / Offer**: Thêm một phần tử vào hàng đợi ưu tiên.
- **Peek / Top**: Xem phần tử có độ ưu tiên cao nhất mà không xóa nó.
- **Extract-Min / Extract-Max / Poll / Pop**: Lấy và xóa phần tử có độ ưu tiên cao nhất khỏi hàng đợi.

## 7. Phân tích độ phức tạp (Time & Space Complexities)
- **Insert (Thêm mới):** O(log N) do quá trình heapify-up.
- **Peek (Xem đỉnh):** O(1) do phần tử ưu tiên luôn ở gốc.
- **Extract-Min/Max (Lấy và xóa đỉnh):** O(log N) do thao tác đưa phần tử cuối lên gốc và heapify-down.
- **Search (Tìm kiếm):** O(N) do tính chất của Heap không hỗ trợ tìm kiếm nhanh như BST.
- **Space Complexity:** O(N) để lưu trữ N phần tử trong heap (thường là mảng).

## 8. Ưu điểm và Nhược điểm (Pros & Cons)
**Ưu điểm:**
- Cung cấp thao tác truy xuất phần tử lớn nhất/nhỏ nhất với O(1).
- Thêm mới và xóa phần tử ưu tiên khá hiệu quả với O(log N).
**Nhược điểm:**
- Truy xuất các phần tử không nằm ở gốc mất thời gian O(N).
- Không duy trì thứ tự sắp xếp của tất cả phần tử bên trong.

## 9. Khi nào nên sử dụng Priority Queue? (When to use)
- Các thuật toán tìm đường (Dijkstra, A*).
- Xử lý các bài toán tìm K phần tử lớn nhất/nhỏ nhất (Top K Elements).
- Thuật toán Prim cho Cây khung nhỏ nhất (MST).
- Scheduling tasks theo độ ưu tiên.
- Trộn K danh sách đã sắp xếp (Merge K sorted lists).

## 10. Lớp `PriorityQueue` trong Java
Java cung cấp lớp `java.util.PriorityQueue` triển khai Min Heap mặc định.
```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5);
pq.offer(1);
pq.offer(3);
System.out.println(pq.poll()); // Kết quả: 1
```

## 11. Sử dụng `Comparator` trong Java
Để tạo Max Heap hoặc ưu tiên theo đối tượng tùy chỉnh, ta dùng `Comparator`.
```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
// Hoặc 
PriorityQueue<Integer> maxHeap2 = new PriorityQueue<>(Collections.reverseOrder());
```
Với custom object:
```java
PriorityQueue<Student> pq = new PriorityQueue<>((s1, s2) -> Integer.compare(s2.score, s1.score));
```

## 12. Các biến thể của Priority Queue (Variants)
- **Double-ended priority queue**: Cho phép truy xuất cả Max và Min trong O(log N) hoặc O(1).
- **Indexed Priority Queue**: Cho phép cập nhật ưu tiên của phần tử bên trong (decrease-key/increase-key) với O(log N) (thường dùng mảng ánh xạ chỉ số).

## 13. Ứng dụng thực tế (Real-world applications)
- Hệ điều hành (CPU Task Scheduling).
- Băng thông mạng (Quality of Service - QoS routers).
- Các hệ thống nén dữ liệu (Huffman Coding).

## 14. So sánh với các cấu trúc dữ liệu khác
- **Array / LinkedList đã sắp xếp**: Peek O(1), nhưng Insert O(N).
- **Binary Search Tree (BST)**: Insert O(log N), Min/Max O(log N), nhưng có risk bị lệch (O(N)) nếu không cân bằng. Heap đơn giản hơn và có constant factor nhỏ hơn.

## 15. Cách cài đặt Priority Queue từ đầu (Implementation from scratch)
Cần thiết kế một lớp `MinHeap` sử dụng một mảng `int[]` và các hàm `heapifyUp`, `heapifyDown`, `parent`, `leftChild`, `rightChild`.

## 16. Mẫu thuật toán thường gặp (Common Patterns)
- **Top K Elements**: Dùng Min Heap cỡ K để tìm Top K lớn nhất.
- **Two Heaps**: Dùng 1 Max Heap (lưu nửa bé) và 1 Min Heap (lưu nửa lớn) để tìm Median trực tiếp (Find Median from Data Stream).
- **K-way Merge**: Dùng heap để gom chung phần tử đầu tiên của các danh sách (Merge K Sorted Lists).

## 17. 20 câu hỏi phỏng vấn phổ biến (20 Interview Questions)
1. Priority Queue là gì? Phân biệt với Standard Queue.
2. Binary Heap được dùng như thế nào để làm Priority Queue?
3. Trình bày độ phức tạp của Insert, Delete-Max trong Heap.
4. Cách tìm phần tử nhỏ thứ K trong mảng bằng Priority Queue?
5. Làm thế nào để Priority Queue trong Java trở thành Max Heap?
6. Làm sao để merge K mảng đã được sắp xếp?
7. Cấu trúc Indexed Priority Queue là gì?
8. Dùng cấu trúc nào để implement Dijkstra Algorithm?
9. Thuật toán Heap Sort hoạt động thế nào?
10. Tại sao build heap bằng mảng mất O(N) mà không phải O(N log N)?
11. "Decrease key" operation là gì và có độ phức tạp bao nhiêu?
12. PriorityQueue trong Java không thread-safe, dùng class nào thay thế nếu cần multithreading? (`PriorityBlockingQueue`)
13. Giải quyết bài toán The Skyline Problem bằng gì?
14. So sánh Priority Queue bằng Heap và TreeSet (Balanced BST) trong Java.
15. Khi nào không nên dùng Priority Queue?
16. Tìm K số thường xuất hiện nhất trong luồng dữ liệu (Data stream)?
17. Giải thích khái niệm "Heapify".
18. Xóa một phần tử bất kỳ khỏi PriorityQueue của Java mất bao lâu? (O(N) để tìm, O(log N) để xóa).
19. Giải thích Huffman Coding sử dụng PQ.
20. Task Scheduler có thể giải như thế nào với PQ?

## 18. Cách tiếp cận lời giải trong phỏng vấn (Interview Approach)
- Khi bài toán yêu cầu tìm "K lớn nhất / nhỏ nhất" -> Nhắc tới Priority Queue đầu tiên.
- Khi luồng dữ liệu liên tục (streaming data) và cần cập nhật Top/Max/Min liên tục -> Priority Queue.
- Luôn cẩn thận viết đúng Comparator để tránh lỗi logic thứ tự.

## 19. 20 Lỗi thường gặp (20 Common Bugs)
1. Nhầm lẫn giữa logic Min Heap và Max Heap (ví dụ viết `a - b` thay vì `b - a`).
2. Gây lỗi tràn số (Integer overflow) trong Comparator khi dùng `a - b`. Nên dùng `Integer.compare(a, b)`.
3. Gọi `poll()` trên một PriorityQueue rỗng gây lỗi hoặc trả về `null` tùy ngôn ngữ, nhưng không check null sinh ra NullPointerException.
4. Quên implement interface `Comparable` cho Object khi khởi tạo PQ không có Comparator.
5. Cập nhật state/field của phần tử đã ở trong PQ mà không gỡ ra (remove) và cho vào lại (add) -> Heap bị vỡ cấu trúc.
6. Sử dụng vòng lặp `foreach` cho PQ và hy vọng nó duyệt ra các phần tử theo thứ tự ưu tiên (Iterating over a PQ does NOT guarantee sorted order).
7. Gán nhầm kích thước khởi tạo (Capacity) là giới hạn thực sự của Queue.
8. So sánh chuỗi bằng toán tử `-` thay vì `compareTo`.
9. Dùng Max Heap thay vì Min Heap cho bài toán tìm Top K phần tử lớn nhất.
10. Dùng Min Heap cho bài toán Top K nhỏ nhất (Nên dùng Max Heap).
11. Không quan tâm tới Memory Limit khi stream data quá lớn.
12. Gỡ phần tử bằng `remove(Object)` với mong đợi O(log N) - thực tế nó là O(N).
13. Nhầm index tính Parent/Child khi tự implement heap gốc 0 (Parent = (i-1)/2) và gốc 1 (Parent = i/2).
14. Truyền sai object reference (các object thay đổi cùng vùng nhớ).
15. Vượt qua kích thước mảng nếu tự implement heap mà không resize mảng.
16. Quên điều kiện dừng (base case) khi viết hàm heapify đệ quy.
17. Vô tình thêm giá trị `null` vào trong PriorityQueue của Java (sẽ quăng `NullPointerException`).
18. K-way merge đẩy sai phần tử (push giá trị tiếp theo không cùng index dòng/cột) gây infinite loop hoặc lỗi IndexOutOfBounds.
19. Viết sai logic giải quyết phần tử trùng lặp (tie-breaking) trong Comparator.
20. Trộn lẫn kiểu nguyên thủy (primitive) và đối tượng (wrapper classes) gây overhead autoboxing quá lớn.

## 20. 30 Trường hợp biên (30 Edge Cases)
1. Hàng đợi hoàn toàn trống (Empty).
2. Hàng đợi có duy nhất 1 phần tử.
3. Hàng đợi chứa hàng triệu phần tử (Kiểm tra tràn bộ nhớ).
4. Thêm/Rút liên tục một số lượng bằng nhau phần tử.
5. Tất cả phần tử trong PQ đều có cùng độ ưu tiên (All identical elements).
6. Độ ưu tiên chứa cả số âm và số dương.
7. Số lượng phần tử K lớn hơn tổng kích thước N của data (Top K problem).
8. K = 0 (Top 0 phần tử).
9. Mảng đầu vào rỗng (Empty stream).
10. K = 1 (Tương đương tìm Max/Min cơ bản).
11. Phần tử là chuỗi chứa ký tự đặc biệt, so sánh độ ưu tiên.
12. Hai phần tử cùng điểm ưu tiên, cần tiêu chí tie-break thứ hai.
13. Tie-break thứ hai cũng bằng nhau, cần tiêu chí tie-break thứ ba.
14. Các phần tử liên tục update độ ưu tiên lên/xuống (Dijkstra algorithm).
15. Gọi remove(object) khi object không tồn tại trong PQ.
16. Gọi peek() khi PQ trống.
17. Dữ liệu cực lớn gây Integer Overflow/Underflow cho Comparator.
18. Yêu cầu rút K phần tử từ PQ chỉ có < K phần tử.
19. Xóa toàn bộ PQ bằng hàm clear() so với rút tuần tự.
20. Trạng thái sau khi dùng `Arrays.asList()` hoặc `List.of()` add vào PQ.
21. Thêm số thực `Double.NaN` hoặc Infinity.
22. Khởi tạo mảng có capacity = 0 trong C++/Java custom heap.
23. Sử dụng multi-threading đồng thời thay đổi PriorityQueue thông thường.
24. Sử dụng PQ lồng PQ (PQ of PQs).
25. Mảng đầu vào đã được sắp xếp sẵn (Best case cho build, worst case logic).
26. Mảng đầu vào sắp xếp ngược (Worst case cho một số cách triển khai).
27. Đỉnh heap có frequency lớn hơn tổng frequency các node còn lại (Task Scheduler).
28. Luồng stream vô tận (Endless stream), dùng Two Heaps phải cân bằng size.
29. Giá trị ưu tiên là object không thể deserialize.
30. Dùng Comparator lambda capture context gây memory leak.

## 21. Kỹ thuật gỡ lỗi (Debugging Techniques)
- Luôn in ra `peek()` trước mỗi lượt xử lý.
- Dùng chức năng debug kiểm tra mảng nội bộ (underlying array) của lớp `PriorityQueue` xem đã tạo đúng heap property chưa.
- Kiểm tra xem kết quả của Comparator có trả về đúng 3 trạng thái: âm, 0, dương.

## 22. Mẹo và Thủ thuật (Tips & Tricks)
- **Top K Largest**: Dùng Min-Heap với kích thước cố định K. Khi kích thước > K, gọi `poll()` để vứt bỏ số nhỏ nhất. Sau cùng heap còn K số lớn nhất.
- **Top K Smallest**: Dùng Max-Heap kích thước K.
- Sử dụng `Collections.reverseOrder()` làm Comparator ngắn gọn cho Max Heap số học.
- Hạn chế dùng `remove(Object)` vì tốn O(N) thời gian.

## 23. Tài liệu tham khảo (References)
- CLRS: Introduction to Algorithms (Chương Heapsort).
- Java Official Documentation (java.util.PriorityQueue).
- Các bài giảng cấu trúc dữ liệu cơ bản về Binary Heap.

## 24. Tổng kết (Conclusion)
Priority Queue / Heap là một trong những cấu trúc dữ liệu quan trọng và có tần suất xuất hiện rất nhiều trong phỏng vấn ở các chủ đề liên quan tới greedy, top K elements, và scheduling. Nắm chắc cách tùy chỉnh `Comparator` là kỹ năng sinh tồn đối với phần này.
