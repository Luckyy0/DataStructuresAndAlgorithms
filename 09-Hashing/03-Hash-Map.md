# Hash Map

## 1. Giới thiệu (Introduction)
Hash Map (hay Hash Table, Dictionary trong các ngôn ngữ khác) là một Data Structure lưu trữ dữ liệu dưới dạng các cặp Key-Value. Hash Map sử dụng một Hash Function để tính toán một Index (chỉ số) vào một mảng của các buckets hoặc slots, từ đó có thể tìm thấy giá trị (Value) mong muốn tương ứng với Key. 

Trong Java, `java.util.HashMap` là implementation phổ biến nhất. Khái niệm cốt lõi là ánh xạ (mapping) từ một khóa (Key) duy nhất tới một giá trị (Value).

## 2. Ứng dụng thực tế (Real-world Applications)
- **Database Indexing**: Lưu trữ chỉ mục để truy xuất dữ liệu nhanh chóng.
- **Caching**: Lưu trữ kết quả của các phép tính toán tốn kém hoặc dữ liệu thường xuyên truy cập (như Memcached, Redis).
- **Symbol Table**: Trình biên dịch sử dụng Hash Map để quản lý các biến và hàm.
- **Router Tables**: Dùng trong mạng máy tính để lưu trữ đường dẫn.
- **Đếm tần suất**: Đếm số lần xuất hiện của từ trong văn bản, số lần mua hàng, v.v.

## 3. Cấu trúc dữ liệu (Data Structure)
Hash Map bao gồm:
- **Array (Mảng)**: Cấu trúc lưu trữ chính.
- **Hash Function (Hàm băm)**: Chuyển đổi Key thành một giá trị Index (thường là số nguyên) để lưu vào Array.
- **Collision Resolution (Xử lý xung đột)**: Xử lý trường hợp nhiều Key có cùng Index (Chaining sử dụng Linked List/Tree, hoặc Open Addressing).
- **Key-Value Pair**: Dữ liệu thực tế được lưu trữ (ví dụ: `Map.Entry` trong Java).

## 4. Cách hoạt động (How it works)
- **Insert (Put)**: Tính Hash Code của Key -> Áp dụng Hash Function để lấy Index -> Lưu Key-Value vào bucket tại Index đó. Nếu có collision, lưu vào cấu trúc dữ liệu bổ sung (như LinkedList hoặc Red-Black Tree ở Java 8+).
- **Search (Get)**: Tính Hash Code của Key -> Lấy Index -> Tìm Key trong bucket tương ứng và trả về Value.
- **Delete (Remove)**: Tính Hash Code của Key -> Lấy Index -> Xóa Key-Value khỏi bucket.
- **Load Factor & Rehashing**: Khi số lượng phần tử vượt qua `capacity * loadFactor` (mặc định 0.75 trong Java), Hash Map sẽ tự động tăng kích thước (thường gấp đôi) và phân bổ lại tất cả các phần tử (Rehashing).

## 5. Các class quan trọng trong Java
- **`java.util.HashMap`**: Bảng băm cơ bản, không đảm bảo thứ tự của các phần tử.
- **`java.util.LinkedHashMap`**: Duy trì thứ tự chèn (insertion-order) bằng cách sử dụng Doubly Linked List kết hợp với Hash Map. Rất hữu ích khi xây dựng LRU Cache.
- **`java.util.TreeMap`**: (Mặc dù là Tree chứ không phải Hash Map thuần túy) Thực thi Map nhưng sắp xếp các Key theo thứ tự tự nhiên hoặc theo Comparator.

## 6. Các phương thức hữu ích (Helpful Methods) trong Java
- `getOrDefault(Object key, V defaultValue)`: Trả về giá trị của Key, nếu Key không tồn tại thì trả về `defaultValue`. Thường dùng trong bài toán đếm tần suất.
- `putIfAbsent(K key, V value)`: Chỉ chèn Key-Value nếu Key chưa tồn tại trong Map (hoặc Value hiện tại là null).
- `computeIfAbsent(K key, Function mappingFunction)`: Tính toán giá trị bằng hàm nếu Key chưa có và chèn vào Map. Rất hay dùng cho việc tạo danh sách khởi tạo (như khởi tạo `ArrayList` bên trong `Map<K, List<V>>`).

## 7. Duyệt qua Hash Map (Iterating)
Trong Java, có nhiều cách để duyệt qua các phần tử:
- **Sử dụng `Map.Entry` và `entrySet()`** (Cách hiệu quả nhất):
  ```java
  Map<String, Integer> map = new HashMap<>();
  for (Map.Entry<String, Integer> entry : map.entrySet()) {
      System.out.println(entry.getKey() + " : " + entry.getValue());
  }
  ```
- **Sử dụng `keySet()`**:
  ```java
  for (String key : map.keySet()) {
      System.out.println(key + " : " + map.get(key));
  }
  ```
- **Sử dụng `values()`**:
  ```java
  for (Integer value : map.values()) {
      System.out.println(value);
  }
  ```
- **Sử dụng `forEach` (Java 8+)**:
  ```java
  map.forEach((k, v) -> System.out.println(k + " : " + v));
  ```

## 8. Mẫu bài toán "Đếm tần suất" (Counting Frequencies Pattern)
Đây là pattern phổ biến nhất khi sử dụng Hash Map:
```java
String s = "abracadabra";
Map<Character, Integer> counts = new HashMap<>();
for (char c : s.toCharArray()) {
    counts.put(c, counts.getOrDefault(c, 0) + 1);
}
```

## 9. Phân tích độ phức tạp (Complexity Analysis)
- **Time Complexity**:
  - Tốt nhất (Trung bình): `O(1)` cho get, put, remove.
  - Xấu nhất: `O(N)` nếu xảy ra Collision quá nhiều (tất cả các phần tử vào cùng một bucket). Trong Java 8+, nhờ sử dụng Red-Black Tree, trường hợp xấu nhất giảm xuống `O(log N)`.
- **Space Complexity**: `O(N)` để lưu trữ N phần tử trong các cấu trúc bên dưới.

## 10. Ưu điểm và nhược điểm (Pros & Cons)
**Ưu điểm:**
- Truy xuất, chèn, xóa siêu nhanh `O(1)` trung bình.
- Hỗ trợ lưu trữ khóa và giá trị một cách linh hoạt.
- Có thể dùng làm cấu trúc trung gian tuyệt vời để giảm độ phức tạp thời gian từ `O(N^2)` xuống `O(N)`.

**Nhược điểm:**
- Không duy trì thứ tự phần tử (`HashMap`).
- Tốn bộ nhớ (overheads) so với mảng nguyên thủy.
- Hiệu suất giảm nghiêm trọng nếu hàm Hash tồi gây ra nhiều Collision.

## 11. Khi nào nên sử dụng (When to use)
- Khi cần look-up dữ liệu bằng một Key duy nhất một cách nhanh chóng.
- Khi cần đếm tần suất xuất hiện của các phần tử.
- Khi cần ánh xạ quan hệ 1-1 hoặc 1-N giữa các phần tử.
- Khi muốn tối ưu bài toán lặp vòng lồng nhau (Two Sum là ví dụ kinh điển).

## 12. So sánh với các cấu trúc dữ liệu khác (Comparison)
- **Hash Map vs Array**: Array sử dụng chỉ số nguyên liên tục, Hash Map có thể sử dụng bất kỳ Object nào làm Key. Array tốn ít bộ nhớ hơn.
- **Hash Map vs Binary Search Tree (TreeMap)**: TreeMap giữ thứ tự các Key và có độ phức tạp `O(log N)`. Hash Map `O(1)` nhưng không có thứ tự.
- **Hash Set vs Hash Map**: Hash Set chỉ lưu Key (sử dụng Hash Map ẩn bên dưới với Value là một hằng số dummy). Hash Map lưu cả Key và Value.

## 13. Ngôn ngữ lập trình (Implementation in Languages)
- **Java**: `HashMap`, `LinkedHashMap`, `TreeMap`, `ConcurrentHashMap`, `Hashtable`.
- **Python**: `dict` (cũng duy trì thứ tự chèn từ Python 3.7+), `defaultdict`, `Counter`.
- **C++**: `std::unordered_map`.
- **JavaScript**: `Map`, `Object` (chú ý Object keys luôn là string hoặc symbol).

## 14. Các biến thể (Variations)
- **ConcurrentHashMap**: Dành cho môi trường Multi-threading trong Java, cho phép truy cập đồng thời mà không bị block toàn bộ map.
- **IdentityHashMap**: Sử dụng tham chiếu reference-equality (`==`) thay vì object-equality (`equals()`).
- **WeakHashMap**: Cho phép Garbage Collector thu hồi Key nếu Key không còn được trỏ bởi tham chiếu mạnh nào khác.

## 15. 20 Lỗi thường gặp (Common Bugs)
1. Sử dụng Object tự định nghĩa làm Key nhưng không override `equals()` và `hashCode()`.
2. Override `equals()` nhưng quên override `hashCode()`.
3. Thay đổi giá trị của Key sau khi đã đưa vào Hash Map, dẫn đến không tìm lại được (Lost Key).
4. Sử dụng `map.get(key)` trả về `null` rồi gọi phương thức trên giá trị đó sinh ra `NullPointerException`.
5. Unboxing `null` từ Hash Map về kiểu nguyên thủy (ví dụ `int val = map.get("A")` khi "A" không có sẽ bị NPE).
6. Lặp qua `keySet()` và gọi `map.get(key)` trong vòng lặp (chậm hơn so với dùng `entrySet()`).
7. Gọi `map.remove(key)` trong khi duyệt qua map bằng for-each (gây `ConcurrentModificationException`). Hãy dùng `Iterator.remove()`.
8. Sử dụng mảng (`int[]`) làm Key trực tiếp trong Java (mảng không override `hashCode()`/`equals()` dựa trên nội dung).
9. Quên mất rằng `HashMap` không bảo toàn thứ tự, mong chờ các phần tử in ra giống thứ tự chèn.
10. Nhầm lẫn giữa `putIfAbsent` và `put`: `put` sẽ ghi đè Value nếu Key đã tồn tại.
11. Bỏ qua việc cung cấp Capacity ban đầu hợp lý khi biết trước kích thước, gây ra rehashing nhiều lần, ảnh hưởng hiệu suất.
12. Xóa phần tử bằng cách gán giá trị `null` (`map.put(key, null)`) thay vì gọi `map.remove(key)` (Size của map vẫn tăng).
13. Kiểm tra sự tồn tại của key bằng cách so sánh với `null` (`map.get(key) != null`) thay vì `containsKey(key)` (vì có thể Value chính là `null`).
14. Lưu dữ liệu nhạy cảm vào Hash Map thông thường và hy vọng nó an toàn trong bộ nhớ.
15. Không đồng bộ hóa khi truy cập Hash Map trong môi trường đa luồng (gây Race conditions, thậm chí Infinite Loop trong các phiên bản Java cũ khi rehashing).
16. Nhầm lẫn giữa capacity và size (Capacity là số lượng buckets, size là số phần tử lưu).
17. Khởi tạo Hash Map trong vòng lặp thay vì khởi tạo một lần và tái sử dụng.
18. Không quan tâm đến Load Factor khi thiết kế hệ thống có yêu cầu ngặt nghèo về bộ nhớ hoặc tốc độ.
19. Gắn đối tượng to lớn làm Key và không bao giờ xóa đi (Memory Leak do Hash Map luôn giữ tham chiếu).
20. Trả về `HashMap` trực tiếp từ API thay vì trả về `Collections.unmodifiableMap` (làm mất tính đóng gói - Encapsulation).

## 16. 30 Edge Cases
1. Map trống (Empty Map).
2. Key là `null`. (Lưu ý: `HashMap` và `LinkedHashMap` cho phép 1 key `null`, nhưng `TreeMap` hoặc `ConcurrentHashMap` thì ném NPE).
3. Value là `null` (Có thể lưu nhiều Value `null`).
4. Xóa phần tử không tồn tại trong map (hàm remove trả về null).
5. Map chứa số lượng phần tử cực lớn (tiệm cận giới hạn `Integer.MAX_VALUE`).
6. Tìm kiếm Key bằng đối tượng cùng giá trị nội dung nhưng khác địa chỉ ô nhớ (cần `equals`/`hashCode` tốt).
7. Ghi đè cùng một Key nhiều lần liên tục.
8. Khởi tạo map với initialCapacity là số rất nhỏ (ví dụ 1).
9. Khởi tạo map với initialCapacity là số rất lớn.
10. Khởi tạo map với loadFactor bất thường (ví dụ: 0.1 hoặc 10.0).
11. Số lượng Collision cực lớn (tất cả các phần tử có chung hashCode) -> Java 8 chuyển qua Tree, kiểm tra hiệu suất.
12. Tương tác với Key null trong các hàm như `getOrDefault`, `putIfAbsent`.
13. Duyệt map vừa rỗng xong.
14. Cập nhật Value của cùng một Key trong quá trình map đang được duyệt (`ConcurrentModificationException`).
15. Thay đổi thuộc tính định danh của Key object khi nó đang nằm trong map.
16. Đưa phần tử vào map sau đó map bị set thành null và gọi GC.
17. Serialize/Deserialize một Hash Map.
18. Clone một Hash Map (chỉ là shallow copy).
19. Các String keys khác nhau nhưng có cùng hashCode (ví dụ `"FB"` và `"Ea"` trong Java).
20. Dùng đối tượng có `hashCode` trả về một số ngẫu nhiên (hoàn toàn sai logic của Map).
21. Truy cập `ConcurrentHashMap` bằng nhiều luồng đồng thời ghi cùng 1 Key.
22. Key là kiểu Double hoặc Float chứa giá trị `NaN` (kiểm tra hành vi của `equals`).
23. Gộp 2 map lại với nhau (`putAll`) trong đó map nguồn chứa các key trùng với map đích.
24. Gọi `computeIfAbsent` nhưng hàm ánh xạ trả về `null` (Map không lưu key mới).
25. Gọi `compute` nhưng hàm ánh xạ ném ra Exception (Dữ liệu cũ trong map vẫn không đổi).
26. Key object trả về `hashCode` âm.
27. Đếm tần suất với dữ liệu có ký tự Unicode phức tạp hoặc emoji.
28. Dùng `LinkedHashMap` ở chế độ `access-order` (cho LRU cache) và lấy phần tử, kiểm tra thứ tự có thay đổi không.
29. Gọi `clear()` trên map rồi kiểm tra `isEmpty()`.
30. Tìm Key không đúng type (ví dụ Map `<String, Integer>` nhưng gọi `map.get(123)`) -> Trình biên dịch không báo lỗi nhưng trả về null.

## 17. Các mẫu bài tập thường gặp (Common Problem Patterns)
1. **Đếm tần suất (Frequency Counting)**: Gom nhóm anagram, kiểm tra xem hai chuỗi có phải permutation không, tìm số xuất hiện nhiều nhất.
2. **Theo dõi phần tử đã thấy (Tracking Seen Elements)**: Cặp tổng Two Sum, tìm phần tử trùng lặp đầu tiên.
3. **Ánh xạ trạng thái/quan hệ (Mapping Relationships)**: Đồ thị vô hướng (Adjacency list), mô phỏng quan hệ 1-1 (Isomorphic Strings, Word Pattern).
4. **Prefix Sum + Hash Map**: Tính tổng mảng con bằng k (Subarray Sum Equals K), đếm số lượng mảng con. Cực kỳ phổ biến.
5. **Sliding Window + Hash Map**: Minimum Window Substring, độ dài chuỗi con không chứa ký tự lặp lại.

## 18. Bài tập thực hành (Practice Problems)
Xem danh sách đầy đủ 30 bài tập trong file `03-Hash-Map-Problems.md`.

## 19. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Hash Map trong Java hoạt động như thế nào? (How does HashMap work internally in Java?)
2. Độ phức tạp thời gian trung bình và tồi nhất của các thao tác `put` và `get` là bao nhiêu?
3. Khi nào thì trường hợp tồi nhất xảy ra trong Hash Map? Java xử lý việc này ra sao?
4. Load Factor là gì và nó ảnh hưởng thế nào đến hiệu năng?
5. `hashCode()` và `equals()` có hợp đồng (contract) gì? Chuyện gì xảy ra nếu vi phạm?
6. Tại sao kích thước (capacity) của HashMap luôn là một lũy thừa của 2?
7. Sự khác biệt giữa `HashMap` và `HashTable` là gì?
8. Sự khác biệt giữa `HashMap`, `LinkedHashMap` và `TreeMap`?
9. Có thể sử dụng đối tượng có thể thay đổi (mutable object) làm Key trong HashMap không? Tại sao?
10. `ConcurrentHashMap` khác `HashMap` chỗ nào? Nó khác `Collections.synchronizedMap` chỗ nào?
11. Collision resolution là gì? Có những phương pháp nào xử lý Collision?
12. Hãy giải thích cơ chế Rehashing trong HashMap.
13. Khi 2 Object có cùng `hashCode`, làm sao HashMap lấy ra chính xác Value cho Object cần tìm?
14. HashMap có cho phép Key null và Value null không? TreeMap thì sao?
15. Mảng (Array) đằng sau Hash Map gọi là gì? (Buckets/Bins).
16. Từ Java 8, HashMap đã được cải tiến như thế nào? (Thêm Red-Black tree khi số lượng phần tử trong bucket đạt ngưỡng).
17. Ngưỡng (Threshold) chuyển từ LinkedList sang Tree trong bucket là bao nhiêu? (TREEIFY_THRESHOLD = 8).
18. Ý nghĩa của hàm `computeIfAbsent` là gì? Đưa ra một ví dụ áp dụng.
19. Tại sao dùng `String` làm Key trong HashMap lại rất tốt?
20. Nếu có 1 triệu phần tử muốn cho vào HashMap, bạn sẽ khởi tạo HashMap như thế nào để tối ưu hiệu suất?

## 20. Mẹo và thủ thuật (Tips & Tricks)
- Luôn khởi tạo kích thước cố định nếu biết trước: `new HashMap<>((int) (expectedSize / 0.75f) + 1)` để tránh rehashing.
- Khi cần Cache (ví dụ LRU Cache), hãy kế thừa `LinkedHashMap` và override hàm `removeEldestEntry`.
- Không sử dụng các thuật toán vòng lặp phức tạp để đếm, cứ `getOrDefault` rồi cộng 1 là cách viết thanh lịch và chuẩn nhất.

## 21. Khái niệm liên quan (Related Concepts)
- **Hash Function**: MD5, SHA-1, MurmurHash, SipHash.
- **Set Data Structure**: Dùng chung cốt lõi với Map.
- **Cuckoo Hashing**, **Robin Hood Hashing**: Các cơ chế Hashing nâng cao khác.
- **Prefix Sum**: Luôn đi kèm với Hash Map để giải bài toán Subarray.

## 22. Thử thách nâng cao (Advanced Challenges)
- Viết lại một `MyHashMap` bằng mảng cơ bản mà không sử dụng Collection framework, cài đặt đầy đủ phương thức put, get, remove và tự xử lý resize.
- Thiết kế một Distributed Hash Table (DHT) như DynamoDB.

## 23. Debugging và Troubleshooting
- Nếu thấy `get()` luôn trả về null mặc dù bạn "nghĩ" rằng đã thêm key vào rồi: Kiểm tra lại các phương thức `equals` và `hashCode` của đối tượng Key, xem có bị thay đổi trạng thái sau khi chèn không.
- Nếu thấy hiệu suất chậm hơn bình thường: Gắn profiler để kiểm tra xem có đang bị quá nhiều hash collisions (hàm băm phân phối không đều) hay liên tục bị resizing (rehashing).

## 24. Tổng kết (Conclusion)
Hash Map là cấu trúc dữ liệu trung tâm trong kỹ thuật lập trình phần mềm và giải thuật. Với khả năng truy cập `O(1)` nhờ Hashing, nó cho phép chúng ta đánh đổi không gian (Space Complexity) lấy tốc độ (Time Complexity) - một quy tắc Vàng trong tối ưu hóa thuật toán. Nắm vững Hash Map không chỉ giúp bạn qua vòng phỏng vấn mà còn xây dựng các hệ thống nhanh và mạnh mẽ.
