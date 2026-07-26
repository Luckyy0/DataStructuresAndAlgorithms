# Thiết kế các cấu trúc dữ liệu Hash (Design Hash Data Structures)

## 1. Giới thiệu (Introduction)
Thiết kế Hash Data Structures (như `HashMap`, `HashSet`) là một chủ đề cực kỳ phổ biến trong các cuộc phỏng vấn System Design và DSA. Nó kiểm tra sự hiểu biết sâu sắc của ứng viên về cách Hashing hoạt động dưới nắp ca-pô (under the hood), bao gồm cách giải quyết va chạm (collision resolution), cấp phát động (dynamic resizing), và kết hợp với các cấu trúc dữ liệu khác để đạt được hiệu suất O(1).

## 2. Các khái niệm cốt lõi (Core Concepts)
- **Hash Function**: Hàm băm dùng để chuyển đổi một key thành một index trong mảng.
- **Buckets**: Mảng các phần tử, trong đó mỗi vị trí (index) chứa một hoặc một danh sách các phần tử có cùng mã băm (hash code).
- **Collision Resolution**: Cơ chế xử lý khi hai keys khác nhau tạo ra cùng một hash code (thường dùng Chaining hoặc Open Addressing).
- **Load Factor**: Tỉ lệ giữa số lượng phần tử và số lượng buckets. Khi vượt qua một ngưỡng (threshold) nhất định, cấu trúc dữ liệu cần được thay đổi kích thước.
- **Rehashing**: Quá trình phân bổ lại (re-distribute) các phần tử vào một mảng mới có kích thước lớn hơn khi Load Factor vượt ngưỡng.

## 3. Implementing Hash Map / Hash Set từ đầu (From Scratch)
Để tự xây dựng một `HashMap` hoặc `HashSet`:
- Cần một mảng cơ sở (underlying array) để lưu trữ.
- Kích thước ban đầu thường là một số nguyên tố hoặc lũy thừa của 2 (ví dụ: 16).
- Cần định nghĩa một lớp `Node` hoặc `Entry` để lưu cặp Key-Value (hoặc chỉ Key cho HashSet) cùng với con trỏ `next` nếu sử dụng Chaining.

## 4. Sử dụng mảng của danh sách liên kết (Chaining)
Phương pháp phổ biến nhất để xử lý va chạm (collision) là **Separate Chaining**.
- Mỗi bucket trong mảng chứa một con trỏ tới head của một Linked List (hoặc Binary Search Tree trong Java 8+).
- Khi thêm một cặp (key, value):
  - Tính `index = hash(key) % capacity`.
  - Duyệt qua Linked List tại `bucket[index]`. Nếu key đã tồn tại, cập nhật value.
  - Nếu không, thêm một Node mới vào đầu hoặc cuối danh sách.

## 5. Logic thay đổi kích thước và Quá trình Rehashing (Resizing & Rehashing)
- Khi `size / capacity >= load_factor` (ví dụ: 0.75):
  - Tạo một mảng `new_buckets` với kích thước gấp đôi (capacity * 2).
  - Duyệt qua toàn bộ phần tử trong mảng cũ.
  - Tính toán lại index mới cho mỗi phần tử: `new_index = hash(key) % new_capacity`.
  - Chuyển phần tử sang `new_buckets[new_index]`.
- Rehashing là thao tác tốn kém O(N) về thời gian, nhưng nhờ Phân tích khấu hao (Amortized Analysis), thao tác thêm (insert) vẫn được coi là O(1) trung bình.

## 6. Thiết kế cấu trúc dữ liệu phức tạp: LRU Cache
- **Định nghĩa**: Least Recently Used (LRU) Cache là bộ nhớ đệm loại bỏ phần tử ít được sử dụng nhất gần đây khi dung lượng đầy.
- **Yêu cầu**: `get(key)` và `put(key, value)` trong O(1).
- **Thiết kế**: Kết hợp `HashMap` và `Doubly Linked List` (DLL).
  - `HashMap<Key, Node>` giúp truy xuất Node trong O(1).
  - DLL lưu trữ thứ tự sử dụng. Head là phần tử dùng gần đây nhất (Most Recently Used), Tail là phần tử ít dùng nhất (Least Recently Used).
  - Khi truy cập hoặc cập nhật, di chuyển Node lên Head. Khi đầy, xóa Node ở Tail khỏi DLL và khỏi HashMap.

## 7. Thiết kế cấu trúc dữ liệu phức tạp: LFU Cache
- **Định nghĩa**: Least Frequently Used (LFU) Cache loại bỏ phần tử có tần suất sử dụng thấp nhất. Nếu có nhiều phần tử cùng tần suất thấp nhất, loại bỏ phần tử cũ nhất (theo LRU).
- **Yêu cầu**: `get` và `put` trong O(1).
- **Thiết kế**: Sử dụng hai HashMaps và một Doubly Linked List cho mỗi tần suất.
  - `HashMap<Key, Node>` lưu giá trị và tần suất (freq).
  - `HashMap<Integer, DoublyLinkedList>` ánh xạ từ freq sang danh sách các nodes có cùng freq.
  - Biến `minFreq` theo dõi tần suất nhỏ nhất hiện tại.

## 8. Time Complexity (Độ phức tạp thời gian)
- **Insert / Put**: O(1) trung bình, O(N) trường hợp xấu nhất (nếu có va chạm toàn bộ).
- **Search / Get**: O(1) trung bình, O(N) trường hợp xấu nhất.
- **Delete / Remove**: O(1) trung bình, O(N) trường hợp xấu nhất.
- **Rehashing**: O(N).

## 9. Space Complexity (Độ phức tạp không gian)
- O(N) để lưu trữ N phần tử.
- Các cấu trúc như LRU/LFU yêu cầu không gian bổ sung cho các con trỏ (prev, next), nên không gian bộ nhớ cần dùng lớn hơn so với Hash Map cơ bản, dù độ phức tạp vẫn là O(N).

## 10. Design Patterns (Các mẫu thiết kế)
- **Decorator/Wrapper**: Mở rộng tính năng của HashMap chuẩn (ví dụ: LRU).
- **Factory**: Tạo các loại Hash structure với cấu hình load_factor hoặc capacity khác nhau.

## 11. Các tối ưu hóa (Optimizations)
- **Cân bằng cây (Treeification)**: Giống Java `HashMap`, chuyển từ Linked List sang Red-Black Tree khi độ dài bucket vượt ngưỡng (ví dụ: 8) để giảm trường hợp xấu nhất của Search từ O(N) xuống O(log N).
- **Bitwise AND cho Modulo**: Thay vì dùng phép chia lấy dư `%`, sử dụng `& (capacity - 1)` để tính index (nhanh hơn nhiều) với điều kiện capacity là lũy thừa của 2.

## 12. Khi nào nên và không nên sử dụng
- **Nên sử dụng**: Khi cần tra cứu, chèn, xóa nhanh (O(1)); khi không cần duy trì thứ tự các phần tử.
- **Không nên sử dụng**: Khi cần duyệt theo thứ tự (cần TreeMap); bộ nhớ hạn chế (overhead của con trỏ và buckets trống lớn).

## 13. Ưu điểm (Advantages)
- Hiệu suất cực nhanh cho các thao tác CRUD cơ bản.
- Linh hoạt trong việc kết hợp (compose) với mảng, Linked List, Heap để tạo cấu trúc dữ liệu phức tạp hơn.

## 14. Nhược điểm (Disadvantages)
- Không duy trì thứ tự sắp xếp (trừ LinkedHashMap).
- Có thể bị suy giảm hiệu năng do va chạm (Collisions).
- Tốn bộ nhớ hơn so với Array.

## 15. So sánh với các Cấu trúc dữ liệu khác
- **Hash Map vs Tree Map**: Hash Map có O(1) trung bình nhưng không có thứ tự. Tree Map có O(log N) cho mọi thao tác và duy trì thứ tự (sorted).
- **Hash Map vs Array**: Array nhanh hơn nếu keys là các số nguyên liên tiếp, không có collision overhead, nhưng Hash Map cho phép keys thuộc bất kỳ kiểu dữ liệu nào.

## 16. Các lỗi thường gặp (20 Common Bugs)
1. Quên override `hashCode` hoặc `equals` cho custom objects làm key.
2. `hashCode` trả về các giá trị không phân tán tốt (poor distribution).
3. `equals` và `hashCode` không nhất quán (inconsistent).
4. Sử dụng mutable objects làm key (key thay đổi sau khi chèn).
5. Tính index bằng `%` sinh ra lỗi mảng (ArrayOutOfBounds) nếu hash code âm (`Math.abs` hoặc bit mask cần thiết).
6. Infinite loop trong Linked List khi xử lý va chạm đồng thời (multi-threading bug trong Java <= 7).
7. ConcurrentModificationException khi duyệt và xóa phần tử không qua Iterator.
8. Khởi tạo mảng buckets với size không phải lũy thừa của 2 (nếu dùng bitwise optimization).
9. Quên kiểm tra `key == null`.
10. Quên xử lý trường hợp update giá trị cho một key đã tồn tại (thay vì add mới).
11. Trong LRU, quên cập nhật `head`/`tail` khi xóa một phần tử ở giữa DLL.
12. LRU: Quên xóa khỏi `HashMap` khi evict (loại bỏ) phần tử ở `tail`.
13. LFU: Quên cập nhật `minFreq` khi toàn bộ danh sách của `minFreq` đã trống.
14. Khi resize, quên xóa sạch bucket cũ, gây memory leak.
15. Không tăng size/count sau khi chèn thành công.
16. Xóa phần tử đầu (head) của bucket bằng cách sai, gây mất các node phía sau.
17. Logic tính `load_factor` sử dụng phép chia số nguyên (`size / capacity`) trả về 0.
18. Không lock đúng cách khi thiết kế Thread-Safe Hash Map.
19. Rehash lặp đi lặp lại vô hạn nếu logic tăng capacity bị lỗi.
20. Trả về true/false sai logic trong HashSet `add()`/`remove()`.

## 17. Các trường hợp biên (30 Edge Cases)
1. Thêm key là `null` (nếu ngôn ngữ/thiết kế cho phép).
2. Thêm value là `null`.
3. Số lượng phần tử chèn vào vượt quá `Integer.MAX_VALUE`.
4. Tìm kiếm key không tồn tại.
5. Xóa key không tồn tại.
6. Xóa phần tử duy nhất trong một bucket.
7. Xóa phần tử đầu tiên trong bucket có nhiều phần tử.
8. Xóa phần tử cuối cùng trong bucket có nhiều phần tử.
9. Xóa phần tử ở giữa trong bucket có nhiều phần tử.
10. Load factor chính xác bằng ngưỡng kích hoạt Resize.
11. Liên tục chèn và xóa khiến cấu trúc không resize nhưng có fragment.
12. Mọi phần tử được chèn đều có cùng hash code (Worst case collision).
13. Kích thước ban đầu (capacity) được truyền vào là 0 hoặc âm.
14. LRU: Dung lượng cache (capacity) = 0.
15. LRU: Dung lượng cache (capacity) = 1.
16. LFU: Tất cả phần tử đều có cùng tần suất, khi đầy phải xóa theo nguyên tắc LRU.
17. Khởi tạo Hash structure và không bao giờ chèn phần tử nào.
18. Yêu cầu `get` ngay sau khi vừa `delete`.
19. Update (chèn lại key đã tồn tại) làm tăng tần suất trong LFU.
20. Update trong LRU không làm tăng số lượng phần tử nhưng phải di chuyển Node.
21. Truy cập liên tục cùng một key trong LRU/LFU (Stress test cache).
22. Key là các đối tượng rất lớn (ví dụ: chuỗi siêu dài).
23. Hash function trả về số âm cực nhỏ `Integer.MIN_VALUE`.
24. Quá trình resize diễn ra trong khi bộ nhớ còn lại của hệ thống rất thấp.
25. Concurrent read/write trên cấu trúc không thread-safe.
26. Custom object trả về cùng 1 hash code nhưng `equals` luôn `false`.
27. Custom object trả về các hash code khác nhau nhưng `equals` luôn `true`.
28. LRU Cache: Cập nhật giá trị của một khóa đã bị evict.
29. Cấu trúc lồng nhau (HashMap of HashMaps).
30. Tìm kiếm với một object khác vùng nhớ nhưng có cùng dữ liệu với object trong map.

## 18. Các câu hỏi phỏng vấn phổ biến (20 Interview Questions)
1. Làm thế nào để thiết kế một HashMap từ đầu?
2. Sự khác biệt giữa HashSet và HashMap là gì? Cơ chế bên dưới của HashSet là gì?
3. Giải thích về Collision và các cách giải quyết?
4. Load Factor là gì? Tại sao giá trị 0.75 thường được sử dụng?
5. Trình bày chi tiết quá trình Rehashing. Nó ảnh hưởng đến hiệu suất như thế nào?
6. Tại sao kích thước (capacity) của HashMap trong Java thường là lũy thừa của 2?
7. Treeification trong HashMap (Java 8) diễn ra khi nào?
8. Thiết kế LRU Cache.
9. Thiết kế LFU Cache.
10. Làm thế nào để implement một Hash map mà get, set, delete và getRandom đều là O(1)?
11. Implement một hệ thống theo dõi bộ đếm (Counter) có hỗ trợ inc, dec, getMax, getMin trong O(1).
12. Có thể sử dụng List làm key của HashMap không?
13. Phân biệt ConcurrentHashMap, HashTable và Synchronized HashMap.
14. Custom class nào có thể dùng làm key trong HashMap? Điều kiện là gì?
15. Tại sao phải override cả hai hàm equals và hashCode?
16. Chuyện gì xảy ra nếu hashCode trả về một hằng số cho mọi object?
17. Thiết kế một hệ thống URL Shortener (Bitly).
18. Thiết kế một In-Memory File System.
19. Có cách nào tránh O(N) của quá trình rehashing không? (Incremental rehashing).
20. Phân tích độ phức tạp khấu hao (Amortized complexity) khi array resizing.

## 19. Các ứng dụng thực tế (Real-world Applications)
- **Database Indexing**: Một số index sử dụng hash cho truy xuất O(1).
- **Caching**: Redis, Memcached sử dụng Hash structure rất nhiều (ví dụ LRU/LFU policies).
- **Language Internals**: Symbol tables trong compilers.
- **Routing**: Phân phối request bằng Consistent Hashing.
- **Distributed Systems**: Distributed Hash Tables (DHT) trong các hệ thống P2P, Cassandra, DynamoDB.

## 20. Trực quan hóa (Visualization)
- Hình dung một mảng các ngăn kéo (buckets). Một hàm toán học (Hash Function) nhận vào một cái nhãn (Key) và chỉ định số của ngăn kéo.
- Bên trong ngăn kéo, ta có thể cất các tờ giấy (Values) được nối với nhau bằng kẹp ghim (Linked List).
- Khi có quá nhiều giấy, ta mua một cái tủ mới to gấp đôi và phân loại lại toàn bộ giấy sang tủ mới.

## 21. Nguồn tài liệu tham khảo (References)
- CLRS: Introduction to Algorithms (Ch. 11 Hash Tables)
- Java Source Code: `java.util.HashMap`
- Các bài học System Design về Caching, Consistent Hashing.

## 22. Đánh giá và ôn tập (Review and Revision)
- Tự viết mã Hash Map và Hash Set mà không cần xem tài liệu.
- Viết mã thuần thục cho LRU và LFU Cache trên giấy hoặc whiteboard.
- Kiểm tra tính đúng đắn với các edge cases, đặc biệt là khi node cần sửa nằm ở tail/head.

## 23. Bài tập thực hành (Practice Problems)
- Chuyển sang file bài tập `04-Design-Hash-Data-Structures-Problems.md`. Các bài như Design HashMap (LeetCode 706), LRU Cache (LeetCode 146), LFU Cache (LeetCode 460) là bắt buộc phải làm.

## 24. Tổng kết (Conclusion)
Hiểu rõ cách thiết kế cấu trúc dữ liệu Hash không chỉ giúp bạn sử dụng các thư viện chuẩn hiệu quả hơn, tránh bugs do equals/hashCode, mà còn rèn luyện kỹ năng kết hợp nhiều cấu trúc (Arrays, Linked Lists, Trees) để thỏa mãn các yêu cầu thời gian chặt chẽ trong System Design.
