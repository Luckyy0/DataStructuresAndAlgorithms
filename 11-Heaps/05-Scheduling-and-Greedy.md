# Lịch trình và Thuật toán Tham lam với Heap (Scheduling and Greedy with Heap)

## 1. Giới thiệu (Introduction)
Trong Khoa học Máy tính, **Scheduling** (lập lịch) và **Greedy** (tham lam) là những bài toán cốt lõi. Khi kết hợp với cấu trúc dữ liệu **Heap** (hay **Priority Queue** - Hàng đợi ưu tiên), chúng ta có thể giải quyết các bài toán tối ưu hóa một cách linh hoạt (dynamically). Heap cho phép duy trì và lấy ra phần tử lớn nhất/nhỏ nhất với thời gian $O(\log N)$, giúp các quyết định Greedy cục bộ đạt được hiệu quả tốt nhất tại mỗi bước mà không cần phải sắp xếp (sort) lại toàn bộ dữ liệu.

## 2. Ứng dụng thực tế (Real-world Applications)
1. **CPU Scheduling**: Lập lịch thực thi tiến trình trên hệ điều hành (ví dụ: Shortest Job First).
2. **Quản lý phòng họp (Meeting Rooms)**: Tối ưu hóa số lượng phòng họp tối thiểu cần thiết để đáp ứng tất cả các cuộc họp.
3. **Định tuyến mạng (Network Routing)**: Giao thức OSPF sử dụng thuật toán Dijkstra (dựa trên Min Heap) để tìm đường đi ngắn nhất.
4. **Load Balancing**: Phân bổ request tới các server đang rảnh nhất.
5. **Streaming/Multimedia**: Buffer frames dựa trên timestamp.

## 3. Phân tích chi tiết (In-depth Analysis)
### 3.1 Sử dụng Heaps để đưa ra quyết định Greedy động (Dynamically)
Khác với Greedy truyền thống chỉ cần sắp xếp một mảng tĩnh một lần $O(N \log N)$ và duyệt qua, nhiều bài toán yêu cầu cập nhật lại không gian trạng thái (thêm/bớt phần tử) liên tục. Bằng cách sử dụng Heap, ta có thể `insert` và `extract` phần tử tối ưu ở mỗi bước chỉ trong $O(\log N)$.
### 3.2 Lập lịch tiến trình trên CPU (CPU Task Scheduling)
Các task có thể đến (arrive) vào các thời điểm khác nhau với thời gian xử lý (burst time) khác nhau. Min Heap thường được dùng để ưu tiên task có `burst time` nhỏ nhất hoặc độ ưu tiên cao nhất, mô phỏng thuật toán SJF (Shortest Job First) động.
### 3.3 Tối ưu hóa tài nguyên (Meeting Rooms Optimization)
Dùng Min Heap để theo dõi thời gian kết thúc của các cuộc họp đang diễn ra. Đỉnh của Min Heap luôn là cuộc họp kết thúc sớm nhất. Nếu một cuộc họp mới bắt đầu sau thời gian này, phòng có thể được tái sử dụng (pop và push). Nếu không, ta cần phòng mới (chỉ push). Kích thước lớn nhất của Heap chính là số phòng cần thiết.
### 3.4 Liên kết với Thuật toán Dijkstra (Dijkstra's Algorithm conceptual link)
Dijkstra có bản chất là Greedy: tại mỗi bước, luôn chọn node có khoảng cách nhỏ nhất chưa được xử lý. Min Heap (Priority Queue) chính là công cụ hoàn hảo để "lập lịch" việc duyệt các đỉnh kề. Node được pop ra khỏi Heap đại diện cho lựa chọn Greedy an toàn nhất hiện tại.

## 4. Hướng dẫn triển khai (Implementation Guide)
**Java Priority Queue Pattern cho Scheduling**:
```java
// Sắp xếp các task theo thời gian đến (arrive)
Arrays.sort(tasks, (a, b) -> Integer.compare(a.arrive, b.arrive));

// Min Heap theo thời gian xử lý (burst time)
PriorityQueue<Task> pq = new PriorityQueue<>((a, b) -> Integer.compare(a.burst, b.burst));

int time = 0;
int i = 0;
while (i < tasks.length || !pq.isEmpty()) {
    // Đẩy các task đã đến vào Heap
    while (i < tasks.length && tasks[i].arrive <= time) {
        pq.offer(tasks[i++]);
    }
    
    if (pq.isEmpty()) {
        time = tasks[i].arrive; // Nhảy vọt thời gian (Jump time)
    } else {
        Task curr = pq.poll();
        time += curr.burst; // Xử lý task
    }
}
```

## 5. Độ phức tạp (Complexity Analysis)
- **Time Complexity**: Thông thường mỗi task được `offer` và `poll` khỏi Heap đúng 1 lần. Do đó mất $O(N \log N)$.
- **Space Complexity**: Heap có thể chứa tối đa $N$ phần tử, tốn $O(N)$ không gian phụ.

## 6. So sánh các cách tiếp cận (Approaches Comparison)
| Tiêu chí | Sorting + Greedy tĩnh | Heap + Greedy động | Cây nhị phân tìm kiếm (BST/TreeMap) |
| --- | --- | --- | --- |
| Dữ liệu thay đổi liên tục | Không hiệu quả | Tốt ($O(\log N)$ mỗi thao tác) | Tốt ($O(\log N)$ mỗi thao tác) |
| Truy xuất Min/Max | $O(1)$ sau khi sort | $O(1)$ tìm, $O(\log N)$ lấy | $O(\log N)$ |
| Hằng số Overhead | Rất nhỏ | Trung bình | Lớn (do duy trì tính cân bằng của cây) |

## 7. Ưu điểm & Nhược điểm (Pros & Cons)
**Ưu điểm**:
- Xử lý các luồng dữ liệu động (Data Streams) một cách dễ dàng.
- Mã nguồn thường ngắn gọn, dễ đọc với các thư viện tiêu chuẩn như `PriorityQueue` (Java), `heapq` (Python), `priority_queue` (C++).

**Nhược điểm**:
- Lạm dụng Heap có thể dẫn đến overhead quá mức cho những bài toán có thể giải quyết chỉ bằng một lần Sort.
- Rất khó debug khi các quy tắc so sánh (Comparator) phức tạp.

## 8. Sai lầm phổ biến (20 Common Bugs)
1. Sử dụng sai Max Heap thay vì Min Heap hoặc ngược lại.
2. Quên không xử lý vòng lặp bên trong để đưa hết các element đồng thời vào Heap.
3. Không jump `time` một cách hợp lý khi Heap trống, dẫn đến Time Limit Exceeded (TLE).
4. Sắp xếp sai `Comparator` dẫn đến Overflow khi lấy `a - b` (nên dùng `Integer.compare()`).
5. Vô ý sửa đổi state của đối tượng đang nằm trong Heap khiến Heap bị vỡ cấu trúc.
6. So sánh các chỉ số (index) thay vì so sánh các giá trị thực tại đối tượng.
7. Đẩy `null` vào Heap.
8. Quên không reset Heap trong các multiple test cases.
9. Giả định Heap iterator sẽ trả về theo thứ tự sorted (thực tế không phải).
10. Break vòng lặp sớm khi chưa xử lý hết Heap.
11. Bỏ qua các task có thời gian xử lý bằng 0.
12. Tính toán sai `end_time` cho các intervals.
13. Nhầm lẫn giữa việc `poll` node cha hiện tại với `peek`.
14. Bỏ qua các events có cùng mốc thời gian (không sắp xếp độ ưu tiên phụ).
15. Nhầm lẫn khi áp dụng Dijkstra: đưa node vào Heap thay vì cập nhật khi đã tìm được đường ngắn hơn (dẫn đến TLE).
16. Bỏ quên visited set trong thuật toán đồ thị kết hợp Heap, dẫn tới vòng lặp vô hạn.
17. Dùng `remove(Object)` của Priority Queue (tốn $O(N)$) thay vì thiết kế lazy deletion $O(\log N)$.
18. Không handling tie-breaking chính xác (khi giá trị chính bằng nhau, phải so sánh giá trị phụ).
19. Gán sai giá trị ban đầu cho các khoảng cách là `0` thay vì `Integer.MAX_VALUE` trong Dijkstra.
20. Trộn lẫn cấu trúc Event: start và end events không được định danh rõ ràng.

## 9. Các trường hợp ngoại lệ (30 Edge Cases)
1. Mảng/Danh sách đầu vào trống (Empty array).
2. Có duy nhất 1 công việc/cuộc họp.
3. Tất cả công việc có cùng thời điểm bắt đầu.
4. Tất cả công việc có cùng thời gian xử lý.
5. Không có công việc nào trùng thời gian (Non-overlapping tasks).
6. Mọi công việc đều hoàn toàn chồng chéo lên nhau (Fully overlapping tasks).
7. Intervals có `start == end` (độ dài = 0).
8. Thời gian lớn vượt quá kiểu `int` 32-bit (cần dùng `long`).
9. `burst time` bằng 0.
10. Số lượng phần tử cực lớn $10^5$, có thể gây lỗi bộ nhớ hoặc TLE nếu thuật toán $O(N^2)$.
11. Đồ thị trong Dijkstra không liên thông (Unconnected graph).
12. Có chu trình trong đồ thị (Cycles) đối với Priority Queue traversal.
13. Trọng số cạnh là 0 trong Dijkstra.
14. Đồ thị có một node đơn độc.
15. Nhiều cạnh song song (Multiple edges giữa 2 nodes).
16. Trùng lặp event trong hệ thống (Duplicate entries).
17. Start time âm (Negative times).
18. Trọng số đồ thị âm (Dijkstra không hoạt động chuẩn, phải dùng Bellman-Ford).
19. Trạng thái Heap liên tục ở trạng thái push và pop một đối tượng duy nhất luân phiên.
20. Task bị tạm ngưng (preempted) và chèn lại vào Heap.
21. Thuật toán có tie (nhiều elements có cùng key) nhưng yêu cầu bảo toàn thứ tự ban đầu (stable queue).
22. Có yêu cầu update một phần tử đang nằm giữa Heap.
23. `k` (capacity) lớn hơn số lượng công việc hiện có.
24. Quản lý ID của Meeting rooms để cấp phát cụ thể.
25. Hàng đợi Priority Queue chỉ được cấp phát hạn chế bộ nhớ.
26. Kích thước cửa sổ trượt (sliding window) chứa toàn bộ mảng.
27. Đỉnh có trọng số nhỏ lại cần bị skip do điều kiện bên ngoài.
28. Thời gian chênh lệch khổng lồ giữa task 1 và task 2 (Gap time test).
29. Khoảng giá trị `Integer.MIN_VALUE` và `Integer.MAX_VALUE` trong so sánh.
30. Yêu cầu in ra thứ tự lịch trình, thay vì chỉ trả về một con số tối ưu.

## 10. Phân tích tối ưu (Optimization Analysis)
Trong các trường hợp cần thay đổi (update) mức độ ưu tiên của một phần tử trong Heap, việc gọi `pq.remove()` sẽ mất $O(N)$. Kỹ thuật tối ưu (Optimization) thường dùng là **Lazy Deletion** (xóa lười): thay vì xóa, ta chỉ cập nhật mảng phụ `distance` hoặc `valid`, sau đó cứ đẩy phiên bản mới vào Heap. Khi `poll()` một phần tử ở trên đỉnh, nếu nó là phiên bản cũ, ta vứt bỏ nó ngay (chi phí amortized $O(\log N)$).

## 11. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. "Explain Meeting Rooms II and how Heap helps solve it optimally."
2. "How would you implement Dijkstra's shortest path using a Priority Queue?"
3. "What happens to the time complexity if we use an array instead of a Heap for Priority Queue in Dijkstra?"
4. "How do you schedule tasks on a single-threaded CPU with arrive and burst times using a Min Heap?"
5. "Can we use a TreeMap instead of PriorityQueue for Meeting Rooms II?"
6. "How do you solve Task Scheduler (LeetCode 621) using a Max Heap?"
7. "How does a Greedy choice differ from Dynamic Programming?"
8. "Explain the algorithm to find the Minimum Number of Refueling Stops using Max Heap."
9. "How do you handle tie-breakers in a Priority Queue?"
10. "Describe how to find the Cheapest Flights within K stops. Does standard Dijkstra work?"
11. "Why can't Dijkstra handle negative weight edges? How does the Heap behave?"
12. "What is Lazy Deletion in Priority Queues?"
13. "Can we maintain the Top K Frequent Elements using a Heap? Which type of Heap?"
14. "Explain Seat Reservation Manager design using Min Heap."
15. "How would you optimize an interval merging problem using a Heap?"
16. "Is it possible to implement a LRU Cache using Priority Queue? Why or why not?"
17. "In Course Schedule III, why do we use a Max Heap to keep track of taken courses?"
18. "Explain how to find maximum average pass ratio using a Max Heap."
19. "How do you handle jumping time (idle time) effectively in single-threaded CPU task?"
20. "If I want to merge K sorted arrays, how do I apply a Min Heap?"

## 12. Các biến thể (Variations)
- **Preemptive Scheduling**: CPU có thể ngắt task hiện tại nếu task mới có độ ưu tiên cao hơn.
- **Multiple Resources**: Thay vì 1 CPU, có $M$ máy chủ rảnh rỗi.
- **Weighted Job Scheduling**: Thay vì greedy đơn thuần, có trọng số, cần kết hợp Binary Search + DP.

## 13. Các khái niệm liên quan (Related Concepts)
- **Sweep Line Algorithm** (Quét dòng): Thường kết hợp với Heap để xử lý events tại mỗi timestamp.
- **Graph Traversal**: BFS kết hợp Heap (Dijkstra, Prim).
- **Two Heaps Pattern**: Duy trì Median của Data Stream.

## 14. Mẹo & Thủ thuật (Tips & Tricks)
- Trong Java, `PriorityQueue` có constructor truyền vào Collection để heapify trong $O(N)$ thời gian thay vì $O(N \log N)$.
- Luôn kiểm tra `pq.isEmpty()` trước khi `peek()` hoặc `poll()`.
- Để sort interval, luôn ưu tiên sort mảng ban đầu theo `start_time` và ném `end_time` vào Heap.

## 15. Hướng dẫn gỡ lỗi (Debugging Guide)
- In ra mảng/list đối tượng sau khi `poll()` để kiểm chứng xem trật tự của Comparator có đúng như kỳ vọng.
- Breakpoint tại những chỗ có `time += current.burst` hoặc `time = next.arrive` để check idle time.

## 16. Mô hình thiết kế (Design Patterns)
Thiết kế Comparator như một Strategy Pattern giúp dễ dàng thay đổi tiêu chí Scheduling (theo tên, theo ID, theo mức độ khẩn cấp).

## 17. Khía cạnh kiến trúc (Architectural Aspects)
Trong microservices, Message Queues (RabbitMQ, Kafka) hoặc Priority Queues trong Celery, Redis đều áp dụng trực tiếp khái niệm Heap Scheduling để phân phối worker tasks.

## 18. Thực hành tốt nhất (Best Practices)
- Immutable keys: Các thuộc tính được sử dụng trong Comparator không nên bị thay đổi sau khi đối tượng được cho vào Heap.
- Tránh dùng các phép toán `+ -` để so sánh vì nguy cơ tràn số (integer overflow), dùng `Double.compare()` hoặc `Integer.compare()`.

## 19. Câu hỏi thường gặp (FAQ)
**Q: Dijkstra và BFS khác nhau thế nào?**
A: BFS dùng Queue (FIFO), Dijkstra dùng Priority Queue (Heap). BFS dùng cho đồ thị không trọng số, Dijkstra cho đồ thị có trọng số dương.

**Q: Tại sao Course Schedule III lại dùng Max Heap thay vì Min Heap?**
A: Vì ta muốn loại bỏ khóa học tốn nhiều thời gian nhất để "làm rỗng" thời gian cho các khóa học khác khi tổng thời gian vượt quá deadline.

## 20. Lộ trình học tập (Learning Path)
1. Nắm vững Priority Queue APIs.
2. Hiểu cấu trúc bên dưới (Array-based Binary Heap).
3. Luyện tập Sweep Line + Heap.
4. Áp dụng Heap vào Đồ thị (Dijkstra, Prim, A*).
5. Nghiên cứu Two Heaps.

## 21. Tài liệu tham khảo (References)
- CLRS Algorithms (Ch. 6: Heapsort, Ch. 24: Single-Source Shortest Paths)
- Javadocs for `java.util.PriorityQueue`

## 22. Bài tập thực hành (Practice Exercises)
Xem file `05-Scheduling-and-Greedy-Problems.md` đính kèm.

## 23. Phân tích không gian giải pháp (Solution Space Analysis)
Khi không gian giải pháp quá lớn, Heap giúp ta cắt tỉa (prune) và định hướng (guide) bước tiếp theo đi vào hướng tối ưu nhất mà không cần khám phá toàn bộ nhánh (chẳng hạn thuật toán A* - biến thể của Dijkstra kết hợp Heuristic).

## 24. Kết luận (Conclusion)
Sự kết hợp giữa Greedy và Heap là một "vũ khí" cực mạnh trong thi đấu thuật toán và phỏng vấn phần mềm. Nó giải quyết triệt để vấn đề "Làm thế nào để chọn ra phương án tốt nhất mọi lúc khi trạng thái thay đổi liên tục?". Bằng cách hiểu rõ cách tổ chức luồng thời gian, kiểm soát idle time và lazy deletion, bạn sẽ vượt qua được những bài toán khó nhất liên quan đến lập lịch và tài nguyên.
