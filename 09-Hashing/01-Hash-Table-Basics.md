# Hash Table Basics (Cơ bản về Bảng Băm)

## 1. Tóm tắt (Summary)
Hash Table (Bảng băm) là một cấu trúc dữ liệu cho phép thao tác chèn, xóa và tìm kiếm vô cùng hiệu quả. Bằng cách sử dụng Hash Function (Hàm băm), Hash Table ánh xạ các keys (khóa) tới các index (chỉ mục) trong một array (mảng). Điều này giúp Hash Table đạt được độ phức tạp thời gian trung bình là $O(1)$ cho các thao tác cơ bản, làm cho nó trở thành một trong những cấu trúc dữ liệu phổ biến nhất trong khoa học máy tính.

## 2. Khái niệm cốt lõi (Core Concepts)
*   **Hashing (Băm)**: Quá trình chuyển đổi một khóa (thường là một chuỗi hoặc một object) thành một số nguyên đại diện cho vị trí (index) trong mảng thông qua một Hash Function.
*   **Hash Function (Hàm băm)**: Một hàm toán học $h(x)$ nhận đầu vào là một key $x$ và trả về một index. Hàm băm tốt cần phân phối đều các khóa trên các bucket để giảm thiểu va chạm.
*   **Collision (Va chạm)**: Xảy ra khi hai keys khác nhau được hàm băm ánh xạ vào cùng một index.
*   **Collision Resolution (Xử lý va chạm)**:
    *   **Chaining (Móc nối)**: Tại mỗi index của mảng lưu trữ một Linked List (Danh sách liên kết). Khi xảy ra va chạm, phần tử mới đơn giản được thêm vào Linked List tại index đó.
    *   **Open Addressing (Địa chỉ mở)**: Khi xảy ra va chạm, thuật toán sẽ tìm kiếm một bucket trống khác dựa trên một chuỗi thăm dò. Một kỹ thuật phổ biến là **Linear Probing (Thăm dò tuyến tính)**: kiểm tra lần lượt các bucket tiếp theo (`index + 1`, `index + 2`, v.v.) cho đến khi tìm thấy bucket trống.
*   **Load Factor (Hệ số tải)**: Tỷ lệ giữa số lượng phần tử đang có trong bảng băm ($n$) và kích thước của bảng băm ($m$). Công thức: $\alpha = \frac{n}{m}$.
*   **Rehashing (Băm lại)**: Khi Load Factor vượt quá một ngưỡng nhất định (thường là 0.75), bảng băm sẽ được thay đổi kích thước (thường là gấp đôi) và tất cả các phần tử sẽ được băm lại vào bảng mới để đảm bảo hiệu suất $O(1)$.

## 3. Ứng dụng thực tế (Real-world Applications)
*   **Database Indexing**: Lập chỉ mục trong cơ sở dữ liệu để tìm kiếm nhanh các bản ghi.
*   **Caching**: Triển khai các hệ thống bộ nhớ đệm như Redis, Memcached.
*   **Symbol Tables**: Bảng ký hiệu trong các trình biên dịch (Compilers) để lưu trữ thông tin về các biến, hàm.
*   **Cryptography**: Trong mật mã học, các hàm băm (như SHA-256) được dùng để tạo chữ ký số và lưu mật khẩu.
*   **Routing Tables**: Bảng định tuyến trong các bộ định tuyến mạng.

## 4. Phân tích độ phức tạp (Complexity Analysis)
*   **Thời gian (Time Complexity)**:
    *   **Trung bình (Average Case)**: Chèn $O(1)$, Xóa $O(1)$, Tìm kiếm $O(1)$.
    *   **Xấu nhất (Worst Case)**: Chèn $O(N)$, Xóa $O(N)$, Tìm kiếm $O(N)$. (Xảy ra khi tất cả các keys đều băm vào cùng một index).
*   **Không gian (Space Complexity)**: $O(N)$, trong đó $N$ là số lượng phần tử hoặc kích thước của bảng băm.

## 5. Cấu trúc dữ liệu liên quan (Related Data Structures)
*   **Array**: Nền tảng cơ bản để xây dựng Hash Table.
*   **Linked List**: Được sử dụng trong chiến lược Chaining để giải quyết va chạm.
*   **Binary Search Tree (BST) / Red-Black Tree**: Được dùng trong Hash Map của Java 8+ để thay thế Linked List khi kích thước bucket lớn, cải thiện Worst Case từ $O(N)$ xuống $O(\log N)$.
*   **Trie**: Cấu trúc cây dùng thay thế Hash Table khi keys là chuỗi, tối ưu tìm kiếm theo prefix.

## 6. Triển khai cơ bản (Basic Implementation)
Ví dụ đơn giản sử dụng Chaining bằng Java:
```java
class Node {
    int key;
    int value;
    Node next;
    public Node(int key, int value) { this.key = key; this.value = value; }
}

class HashTable {
    private Node[] buckets;
    private int capacity;
    private int size;

    public HashTable(int capacity) {
        this.capacity = capacity;
        this.buckets = new Node[capacity];
    }

    private int getHash(int key) {
        return Math.abs(key) % capacity;
    }

    public void put(int key, int value) {
        int index = getHash(key);
        Node head = buckets[index];
        while (head != null) {
            if (head.key == key) {
                head.value = value;
                return;
            }
            head = head.next;
        }
        Node newNode = new Node(key, value);
        newNode.next = buckets[index];
        buckets[index] = newNode;
        size++;
    }

    public int get(int key) {
        int index = getHash(key);
        Node head = buckets[index];
        while (head != null) {
            if (head.key == key) return head.value;
            head = head.next;
        }
        return -1;
    }
}
```

## 7. Các biến thể (Variations)
*   **HashSet**: Chỉ lưu trữ các keys, đảm bảo tính duy nhất.
*   **LinkedHashMap**: Duy trì thứ tự chèn của các phần tử bằng cách sử dụng thêm một Doubly Linked List kết nối các nodes.
*   **ConcurrentHashMap**: Bảng băm an toàn luồng (thread-safe), tối ưu cho lập trình đồng thời bằng cơ chế khóa ở mức bucket (lock striping).
*   **Cuckoo Hashing**: Sử dụng nhiều hàm băm để giải quyết va chạm với độ phức tạp $O(1)$ worst case cho tìm kiếm.

## 8. Các nguyên tắc thiết kế (Design Principles)
*   **Uniform Distribution**: Hàm băm phải phân phối các keys đều đặn trên toàn bộ mảng.
*   **Deterministic**: Một key nhất định phải luôn cho ra cùng một mã băm.
*   **Efficiency**: Quá trình tính toán hash value phải nhanh.
*   **Proper Load Factor**: Duy trì ngưỡng Load Factor phù hợp để cân bằng giữa bộ nhớ và hiệu suất.

## 9. Các mẫu thuật toán (Algorithm Patterns)
*   **Frequency Counting**: Dùng Hash Map để đếm số lần xuất hiện của các phần tử (ví dụ: Anagrams, phần tử xuất hiện nhiều nhất).
*   **Two Sum / Pair Tracking**: Dùng Hash Set/Map để lưu trữ các phần tử đã duyệt, giúp tìm cặp phần tử thỏa mãn điều kiện với $O(1)$ lookup.
*   **Caching/Memoization**: Dùng Hash Map để lưu trữ kết quả của các bài toán con đắt đỏ trong Quy hoạch động.
*   **Sliding Window with Hash Map**: Dùng trong các bài toán tìm mảng con có giới hạn điều kiện (ví dụ: chuỗi con không chứa ký tự lặp lại).

## 10. Mẹo phỏng vấn (Interview Tips)
*   **Luôn nghĩ về Hash Table đầu tiên**: Khi bài toán yêu cầu cải thiện thời gian tìm kiếm từ $O(N)$ xuống $O(1)$ hoặc tìm phần tử trùng lặp.
*   **Trade-off Không gian và Thời gian**: Giải thích rõ với Interviewer rằng bạn dùng Hash Table đổi lấy $O(N)$ Space để đạt được $O(1)$ Time.
*   **Khóa tùy chỉnh (Custom Keys)**: Nhớ ghi đè phương thức `equals()` và `hashCode()` nếu sử dụng object tự định nghĩa làm key.
*   **Thảo luận về Worst Case**: Dù Time Complexity trung bình là $O(1)$, hãy luôn chuẩn bị để trả lời về tình huống $O(N)$ khi băm không đều.

## 11. 20 Lỗi thường gặp (Common Bugs)
1. Quên override `hashCode()` khi override `equals()`.
2. Override `hashCode()` nhưng không nhất quán với `equals()`.
3. Sử dụng Object thay đổi được (Mutable object) làm Key trong Hash Map.
4. Tràn số nguyên (Integer Overflow) trong quá trình tính toán Hash Function.
5. Xử lý va chạm bằng Linear Probing không xử lý đúng vòng lặp vô hạn nếu bảng đầy.
6. Lỗi chia cho 0 nếu kích thước bucket là 0.
7. Xóa phần tử trong Open Addressing không sử dụng cờ (Tombstone), gây đứt gãy quá trình tìm kiếm.
8. Tính sai Load Factor (dùng phép chia nguyên thay vì chia số thực).
9. Rehashing thiếu copy phần tử cũ sang cấu trúc mảng mới đúng cách (chỉ số thay đổi).
10. `hashCode()` trả về số âm, dẫn tới lỗi `IndexOutOfBounds` khi dùng phép modulo `%`.
11. Bỏ sót kiểm tra Key là `null`.
12. Hàm băm quá đơn giản, dẫn đến mọi keys rơi vào cùng một bucket.
13. Truy cập concurrent Hash Map không an toàn luồng dẫn đến trạng thái sai lệch.
14. Sửa đổi cấu trúc HashMap khi đang duyệt bằng Iterator (ConcurrentModificationException).
15. Không khởi tạo initial capacity khi biết trước số lượng phần tử, dẫn đến tốn thời gian Rehashing liên tục.
16. Dùng `==` thay vì `.equals()` để so sánh Key trong danh sách móc nối.
17. Giải phóng bộ nhớ sai cách trong C++ Hash map tự viết.
18. Trộn lẫn (mix-up) thứ tự của Key và Value khi gọi phương thức `put()`.
19. Quên tăng/giảm `size` khi thêm/xóa phần tử.
20. So sánh HashCode thay vì so sánh bằng `equals` khi xác định xem phần tử có tồn tại hay không.

## 12. 30 Trường hợp biên (Edge Cases)
1. Bảng băm hoàn toàn trống.
2. Bảng băm có duy nhất 1 phần tử.
3. Chèn một Key đã tồn tại (phải cập nhật Value, không chèn thêm).
4. Tìm kiếm một Key không tồn tại trong bảng băm.
5. Xóa một Key không tồn tại.
6. Xóa phần tử duy nhất trong một bucket (Chaining).
7. Xóa phần tử đầu tiên của danh sách móc nối.
8. Xóa phần tử ở giữa danh sách móc nối.
9. Xóa phần tử ở cuối danh sách móc nối.
10. Bảng băm sử dụng Open Addressing bị đầy 100%.
11. Hash Function trả về số cực lớn (gần MAX_INT).
12. Các keys có giá trị giống hệt nhau về HashCode (tất cả đều va chạm).
13. Chèn các giá trị null làm Key hoặc Value (nếu được hỗ trợ).
14. Rehashing khi bảng băm đạt ngưỡng tải trọng chính xác bằng threshold.
15. Rehashing nhiều lần do thêm liên tục một lượng phần tử khổng lồ.
16. Key có độ dài bằng 0 (ví dụ: chuỗi rỗng `""`).
17. Sử dụng các ký tự non-ASCII, Unicode đặc biệt làm Key.
18. Key là một đối tượng rất lớn tốn nhiều bộ nhớ.
19. Mảng Buckets có kích thước không phải số nguyên tố, ảnh hưởng đến độ phân tán của HashCode.
20. Mảng Buckets kích thước cực lớn gây hết bộ nhớ (Out Of Memory).
21. Concurrent update tại đúng lúc bảng băm đang được rehashing.
22. Key bị thay đổi (mutable) sau khi được cho vào bảng.
23. Yêu cầu tải (load) bảng băm ngay sau khi nó được giải phóng.
24. Key có hash code là `Integer.MIN_VALUE`, hàm `Math.abs` vẫn trả về số âm.
25. Iterator duyệt qua bảng băm trong lúc nó đang thêm xóa.
26. Khởi tạo HashMap với capacity < 0 (IllegalArgumentException).
27. Chèn một lượng lớn các keys liên tiếp nhau trong bảng Open Addressing.
28. Linear Probing wrap-around mảng (quay về đầu mảng sau khi qua điểm cuối).
29. Cấu trúc cây (Tree bins trong Java 8) xảy ra khi quá nhiều keys cùng một bucket, sau đó xóa dần và thoái hóa lại thành danh sách.
30. Tìm kiếm và xóa đồng thời trong đa luồng.

## 13. 20 Câu hỏi phỏng vấn (Interview Questions)
1. Hash Table hoạt động như thế nào?
2. So sánh Chaining và Open Addressing? Khi nào nên dùng cái nào?
3. Trình bày khái niệm Load Factor. Tại sao chúng ta cần Rehashing?
4. Thiết kế một Hash Map đơn giản từ đầu.
5. Hàm băm (Hash Function) như thế nào được coi là một hàm băm "tốt"?
6. Giải thích $O(1)$ thời gian trung bình của Hash Table, và điều gì làm cho nó trở thành $O(N)$?
7. Làm thế nào để xử lý va chạm với chuỗi giá trị trùng lặp rất lớn?
8. Kể về quy tắc bucket chuyển thành Red-Black Tree trong HashMap của Java từ Java 8?
9. Sự khác biệt giữa `HashMap`, `HashTable` và `ConcurrentHashMap` trong Java?
10. Tại sao kích thước (capacity) của HashMap trong Java luôn là một lũy thừa của 2?
11. Nếu sử dụng lớp tự định nghĩa làm Key trong HashMap, bạn cần phải implement những gì?
12. Cuckoo Hashing là gì?
13. Linear Probing và Quadratic Probing khác nhau thế nào? Hiện tượng Clustering (Cụm) là gì?
14. Bạn sẽ thiết kế một bộ đệm LRU Cache như thế nào? (Gợi ý: Hash Map + Doubly Linked List)
15. Bảng băm phân tán (Distributed Hash Table - DHT) là gì? Consistent Hashing hoạt động ra sao?
16. Cách để xóa một phần tử trong một bảng băm dùng Open Addressing mà không gây gián đoạn các tìm kiếm khác?
17. Nếu tôi không override `hashCode()`, HashMap sẽ hoạt động như thế nào?
18. Bạn có thể sử dụng mảng tĩnh thay vì Hash Map để đếm số lượng ký tự trong chuỗi ASCII không? Ưu nhược điểm là gì?
19. Giải thích cơ chế khóa an toàn luồng của ConcurrentHashMap.
20. Nếu có 1 triệu chuỗi, làm thế nào để tìm ra chuỗi trùng lặp nhanh nhất mà ít tốn bộ nhớ nhất?

## 14. Kỹ thuật tối ưu hóa (Optimization Techniques)
*   **Tránh Rehashing không cần thiết**: Khởi tạo HashMap với kích thước `initialCapacity = (số_lượng_phần_tử / load_factor) + 1` để ngăn việc bảng phải băm lại nhiều lần khi có lượng dữ liệu lớn.
*   **Tối ưu Hash Function**: Tránh các phép tính nặng như nhân chia, sử dụng thao tác bitwise XOR hoặc Shift để tăng tốc.
*   **Rút gọn không gian**: Sử dụng cấu trúc Trie, hoặc Bloom Filter trước khi đưa vào Hash Map nếu mục tiêu là kiểm tra tồn tại với bộ nhớ cực nhỏ.

## 15. Tích hợp hệ thống (System Integration)
*   **Bộ nhớ đệm (Memcached, Redis)**: Đều dựa trên nguyên lý Hash Table phân tán, đảm bảo $O(1)$ response time cho web server.
*   **Cơ sở dữ liệu NoSQL**: Document Stores sử dụng Hash Maps ở lõi để lookup theo Object ID nhanh.
*   **Load Balancer**: Dùng Hash IP để ánh xạ các request đến đúng các máy chủ cụ thể, đảm bảo session persistence.

## 16. Đặc tả ngữ cảnh (Contextual Specifics)
*   **Ngôn ngữ Java**: `HashMap` cho phép 1 key `null` và nhiều values `null`. Trong khi `HashTable` không cho phép bất kỳ `null` nào và mặc định đồng bộ (synchronized).
*   **Ngôn ngữ Python**: Từ bản 3.6, `dict` duy trì insertion order (theo thứ tự chèn).
*   **C++**: Bảng băm được gọi là `unordered_map`, và hàm băm được chỉ định bởi thư viện tiêu chuẩn hoặc người dùng tuỳ chỉnh.

## 17. Mô hình học máy (Machine Learning Models)
*   **Feature Hashing (Hashing Trick)**: Một phương pháp giảm chiều dữ liệu hiệu quả, dùng Hash function chuyển đổi features dạng chuỗi thành vector với độ dài cố định mà không cần lưu từ điển, tối ưu cực lớn về memory trong Natural Language Processing.

## 18. Công cụ & Thư viện (Tools & Libraries)
*   Java: `java.util.HashMap`, `java.util.HashSet`, `java.util.concurrent.ConcurrentHashMap`.
*   Thư viện hỗ trợ băm (Hash Algorithms): Apache Commons Codec, Guava Hashing.

## 19. So sánh các cấu trúc (Structure Comparisons)
*   **Hash Map vs Balanced BST (TreeMap)**: Hash Map tốn $O(1)$ cho các phép thêm, xóa, tìm nhưng không giữ thứ tự, trong khi TreeMap có độ phức tạp $O(\log N)$ nhưng luôn duy trì thứ tự có sẵn (Sorted).
*   **Hash Map vs Array**: Array phải dùng index là số nguyên tốn $O(1)$ tìm kiếm, Hash Map cho phép sử dụng hầu hết mọi kiểu dữ liệu làm Key (String, Object) bù lại tốc độ chèn chậm hơn chút do tính toán hàm băm và có bộ nhớ overhead.

## 20. Hướng dẫn gỡ lỗi (Debugging Guide)
*   Khi phần tử bị "mất" sau khi chèn vào `HashMap`, kiểm tra ngay lại class của đối tượng có implement `equals()` và `hashCode()` chưa.
*   Theo dõi tỉ lệ va chạm (Collision rate). Nếu rate quá cao, hãy thay đổi hàm băm (hàm băm hiện tại không đủ phân tán).
*   In danh sách móc nối tại một index cụ thể để xem vấn đề nếu có $O(N)$ lookup time bất thường.

## 21. Quản lý bộ nhớ (Memory Management)
*   Khai báo Load Factor lớn sẽ tiết kiệm không gian mảng ban đầu, nhưng bù lại tăng số lượng va chạm (Chaining dài).
*   Tombstone (cờ đánh dấu bị xóa) trong Open Addressing có thể lấp đầy bảng làm hỏng thuật toán, cần quá trình "quét dọn" hoặc rehashing lại nếu số lượng tombstone vượt quá ngưỡng.

## 22. Tiêu chuẩn viết code (Coding Standards)
*   Luôn đặt `final` cho các biến sử dụng làm properties tham gia vào hàm `hashCode()` để đảm bảo Key là immutable.
*   Đặt tên biến rõ ràng, ví dụ: `capacity`, `loadFactor`, `threshold` khi tự implement.

## 23. Đánh giá bảo mật (Security Review)
*   **Hash Collision DoS Attack**: Kẻ tấn công cố tình gửi lên hệ thống hàng ngàn keys có cùng HashCode khiến Web Server/Database phải duyệt qua danh sách $O(N)$, làm CPU quá tải.
    *   *Cách phòng chống*: Dùng hàm băm ngẫu nhiên có seed ngẫu nhiên cho mỗi phiên, hoặc nâng cấp Chaining thành Cây như trong Java 8.

## 24. Tóm tắt nhanh & Các bước tiếp theo (Quick Recap & Next Steps)
Hash Table là "vũ khí" siêu mạnh mẽ, giúp tối ưu hầu hết các thuật toán về thời gian xuống ngưỡng $O(1)$. Bước tiếp theo: Bạn hãy thực hành kỹ năng với các bài tập sử dụng Hash Map trên mảng, chuỗi ký tự và áp dụng kỹ thuật Two-pointer + Hashing để hiểu sâu hơn.
