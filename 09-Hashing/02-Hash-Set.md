# Hash Set (Tập Hợp Băm)

## 1. Giới thiệu (Introduction)
Hash Set là một cấu trúc dữ liệu cơ bản được sử dụng phổ biến trong lập trình để lưu trữ các phần tử duy nhất (không trùng lặp). Đây là một tập hợp (set) dựa trên nguyên lý của Hash Table, giúp thực hiện các thao tác tra cứu, chèn và xóa vô cùng nhanh chóng.

## 2. Định nghĩa (Definition)
Hash Set là một loại cấu trúc dữ liệu lưu trữ một tập hợp các khóa (keys) mà không có giá trị (values) đi kèm, đảm bảo tính duy nhất (uniqueness) của mỗi khóa trong tập hợp. Dưới tầng sâu (under the hood), nó thường được cài đặt bằng một Hash Map với các giá trị (values) là một hằng số giả (dummy value).

## 3. Các đặc điểm chính (Key Characteristics)
- **Tính duy nhất (Uniqueness)**: Không cho phép chứa các phần tử trùng lặp (duplicates). Nếu chèn một phần tử đã tồn tại, nó sẽ bị bỏ qua hoặc ghi đè.
- **Tính không thứ tự (Unordered nature)**: Các phần tử không được lưu trữ theo thứ tự chèn vào hay thứ tự tự nhiên của chúng.
- **Dựa trên Hashing**: Sử dụng hàm băm (hash function) để tính toán vị trí lưu trữ (bucket) của phần tử.
- **Khóa null (Null keys)**: Đa số các cài đặt (như `java.util.HashSet`) cho phép lưu trữ tối đa một phần tử `null`.

## 4. Tại sao cần sử dụng (Why use it)
Hash Set được sử dụng khi chúng ta cần quản lý một danh sách các phần tử mà ở đó, việc đảm bảo không có phần tử trùng lặp và khả năng kiểm tra sự tồn tại (lookup/contains) của một phần tử phải được thực hiện với hiệu suất cực cao (thường là O(1)). Nó loại bỏ nhu cầu phải duyệt qua toàn bộ mảng để kiểm tra xem một phần tử đã tồn tại hay chưa.

## 5. Hoạt động như thế nào (How it works)
Khi bạn thêm một phần tử vào Hash Set:
1. Hàm băm (Hash Function) tính toán hash code của phần tử đó (ví dụ, gọi phương thức `hashCode()` trong Java).
2. Hash code này được sử dụng để tìm chỉ số (index) của bucket tương ứng trong một mảng (array) ẩn bên dưới.
3. Nếu bucket trống, phần tử được lưu vào đó.
4. Nếu bucket đã có phần tử (Hash Collision - Đụng độ băm), nó sử dụng phương thức `equals()` để kiểm tra xem phần tử mới có trùng với phần tử đã có không. Nếu trùng, phần tử mới không được chèn. Nếu không trùng, nó được thêm vào bucket (thường là thông qua Linked List hoặc Tree).

## 6. `java.util.HashSet` vs `TreeSet` (Hash vs Tree)
- **Cơ chế hoạt động**: `HashSet` dựa trên Hash Table (`HashMap`), còn `TreeSet` dựa trên cây nhị phân tìm kiếm tự cân bằng (thường là Red-Black Tree, qua `TreeMap`).
- **Thứ tự**: `HashSet` không bảo đảm bất kì thứ tự nào. `TreeSet` lưu các phần tử theo thứ tự tăng dần (sorted order) tự nhiên hoặc theo `Comparator`.
- **Hiệu năng**:
  - `HashSet`: Các thao tác cơ bản (add, remove, contains) mất thời gian O(1) trung bình.
  - `TreeSet`: Các thao tác này mất thời gian O(log N).
- **Phần tử Null**: `HashSet` cho phép 1 phần tử null. `TreeSet` thường không cho phép null (vì sẽ gây ra `NullPointerException` khi so sánh).
- **Khi nào dùng**: Dùng `HashSet` khi chỉ cần tính duy nhất và truy xuất nhanh. Dùng `TreeSet` khi cần các phần tử luôn được sắp xếp.

## 7. Các thao tác cơ bản (Basic Operations)
- **Chèn (Insert / Add)**: Thêm một phần tử vào Set nếu nó chưa tồn tại.
- **Xóa (Delete / Remove)**: Loại bỏ một phần tử khỏi Set.
- **Tìm kiếm (Lookup / Contains)**: Kiểm tra xem một phần tử có nằm trong Set hay không.

## 8. Độ phức tạp (Time and Space Complexity)
| Thao tác | Trung bình (Average Case) | Tồi tệ nhất (Worst Case) |
| :--- | :--- | :--- |
| **Lookup (Contains)** | O(1) | O(N) |
| **Insert (Add)** | O(1) | O(N) |
| **Delete (Remove)** | O(1) | O(N) |

**Space Complexity**: O(N), với N là số lượng phần tử lưu trong Set.
Worst case O(N) xảy ra khi có quá nhiều đụng độ băm (collisions) và tất cả các phần tử rơi vào cùng một bucket. (Tuy nhiên trong Java 8+, việc dùng Tree Bin có thể giảm worst case xuống O(log N)).

## 9. Các phép toán trên Tập hợp (Set Operations)
Hash Set rất hữu ích để mô phỏng các phép toán tập hợp trong toán học:
- **Union (Hợp)**: Tập hợp các phần tử thuộc A hoặc B (hoặc cả hai). `A.addAll(B)`.
- **Intersection (Giao)**: Tập hợp các phần tử cùng nằm trong A và B. `A.retainAll(B)`.
- **Difference (Hiệu)**: Tập hợp các phần tử thuộc A nhưng không thuộc B. `A.removeAll(B)`.
- **Subset (Tập con)**: Kiểm tra B có phải là tập con của A. `A.containsAll(B)`.

## 10. Ưu điểm (Advantages)
- Tốc độ truy xuất và cập nhật cực nhanh (O(1) trong hầu hết các trường hợp).
- Code gọn gàng để loại bỏ các phần tử trùng lặp từ bất kỳ Collection nào (ví dụ: gán List vào Set).
- Hỗ trợ tốt các phép toán tập hợp phức tạp.

## 11. Nhược điểm (Disadvantages)
- Không duy trì thứ tự phần tử, gây khó khăn nếu cần duyệt theo thứ tự chèn.
- Tốn bộ nhớ hơn so với các cấu trúc đơn giản như Array do cần duy trì mảng băm và các Node/Object riêng biệt (overhead memory).
- Hiệu năng phụ thuộc nhiều vào chất lượng của hàm băm (hash function).

## 12. Các biến thể (Variants)
- **LinkedHashSet**: Duy trì thứ tự chèn (insertion-order) của các phần tử thông qua một Doubly Linked List kết nối các entry.
- **TreeSet**: Đảm bảo thứ tự sắp xếp của các phần tử.
- **EnumSet**: Dành riêng cho việc lưu trữ các phần tử thuộc một Enum, cực kỳ nhanh và tốn ít bộ nhớ nhờ sử dụng bit vector.
- **ConcurrentSkipListSet / CopyOnWriteArraySet**: Sử dụng trong môi trường đa luồng (Multi-threading).

## 13. Khi nào nên sử dụng (When to use)
- Cần một cấu trúc dữ liệu để kiểm tra sự tồn tại (existence check) nhanh chóng.
- Cần loại bỏ (deduplicate) các phần tử trùng lặp từ một tập dữ liệu lớn.
- Sử dụng làm cấu trúc mảng đánh dấu `visited` trong các thuật toán Graph (BFS, DFS) đối với các đồ thị không biểu diễn được bằng mảng số nguyên đơn giản.

## 14. Khi nào KHÔNG nên sử dụng (When NOT to use)
- Cần truy cập các phần tử theo chỉ số (index).
- Cần duyệt các phần tử theo thứ tự nhất định (nên dùng TreeSet, List hoặc LinkedHashSet).
- Bộ nhớ (Memory) bị giới hạn nghiêm ngặt.
- Làm việc với mảng các số nguyên có giới hạn nhỏ (ví dụ từ 1 đến 1000) - lúc này dùng boolean array / bit manipulation sẽ nhanh và tiết kiệm hơn rất nhiều.

## 15. Ứng dụng thực tế (Real-world Applications)
- Duy trì danh sách các users hiện đang online (không thể có 2 user cùng ID).
- Lọc các từ duy nhất từ một văn bản lớn.
- Lưu trữ các token hợp lệ/đã bị thu hồi (revoked tokens) trong xác thực bảo mật.
- Kiểm tra chính tả (Spell checkers): lưu trữ toàn bộ từ điển vào Hash Set.

## 16. Code mẫu triển khai cơ bản (Basic Implementation Code)
Dưới đây là một triển khai minh họa Hash Set đơn giản bằng Java, dựa trên cơ chế Hash Table với Separate Chaining.

```java
import java.util.LinkedList;

public class MyHashSet {
    private final int BUCKET_SIZE = 1000;
    private LinkedList<Integer>[] buckets;

    public MyHashSet() {
        buckets = new LinkedList[BUCKET_SIZE];
        for (int i = 0; i < BUCKET_SIZE; i++) {
            buckets[i] = new LinkedList<>();
        }
    }

    private int getHash(int key) {
        return Integer.hashCode(key) % BUCKET_SIZE;
    }

    public void add(int key) {
        int index = getHash(key);
        if (!buckets[index].contains(key)) {
            buckets[index].add(key);
        }
    }

    public void remove(int key) {
        int index = getHash(key);
        buckets[index].remove((Integer) key);
    }

    public boolean contains(int key) {
        int index = getHash(key);
        return buckets[index].contains(key);
    }
}
```

## 17. So sánh với các cấu trúc dữ liệu khác (Comparison)
- **Hash Set vs Array**: Array có thứ tự và index, Hash Set không có; Hash Set chặn trùng lặp, Array cho phép. Lookup trong Hash Set là O(1), trong Array chưa sắp xếp là O(N).
- **Hash Set vs Hash Map**: Hash Set lưu trữ tập các khóa (keys), còn Hash Map lưu trữ tập các cặp khóa - giá trị (key-value pairs). Trên thực tế `HashSet` thường bọc bên ngoài một `HashMap`.
- **Hash Set vs Bloom Filter**: Bloom Filter có thể kiểm tra một phần tử "chắc chắn KHÔNG có mặt" hoặc "CÓ THỂ có mặt" nhưng dùng cực ít bộ nhớ. Hash Set kiểm tra chính xác nhưng tốn nhiều bộ nhớ hơn.

## 18. Các kĩ thuật tối ưu (Optimization Techniques)
- **Pre-sizing**: Khởi tạo capacity (sức chứa) cho HashSet khi biết trước số lượng phần tử tối đa để tránh quá trình re-hashing tốn kém: `new HashSet<>(initialCapacity)`. (Chú ý tính cả load factor, ví dụ cần lưu 100 phần tử thì size khoảng 100/0.75 + 1).
- **Overriding `equals()` và `hashCode()` đúng chuẩn**: Đối với custom object, luôn phải ghi đè cả hai hàm này. Hàm hash code cần phân tán đều (distribute evenly) để tránh collision.

## 19. Mẹo khi phỏng vấn (Interview Tips)
- Hầu hết các bài toán "tìm phần tử duy nhất", "đếm số phần tử khác nhau", "kiểm tra hai mảng có chung phần tử không" đều dùng Hash Set.
- Đề cập đến `HashSet` như một cách giảm độ phức tạp thời gian từ O(N^2) xuống O(N).
- Hãy nhớ nhược điểm không có thứ tự. Nếu interviewer yêu cầu giữ nguyên thứ tự xuất hiện ban đầu, hãy đổi sang dùng `LinkedHashSet`.
- Nắm vững sự khác nhau giữa HashSet và TreeSet vì đây là câu hỏi lý thuyết rất phổ biến.

## 20. 20 Câu hỏi phỏng vấn (20 Interview Questions)
1. Hash Set hoạt động như thế nào trong Java dưới tầng sâu?
2. Sự khác biệt cốt lõi giữa HashSet, LinkedHashSet và TreeSet?
3. Tại sao khi override `equals()`, bắt buộc phải override `hashCode()`?
4. Load factor là gì và nó ảnh hưởng thế nào đến hiệu suất của HashSet?
5. Nếu một class custom làm key trong HashSet không cung cấp hàm `hashCode()` tốt, điều gì sẽ xảy ra?
6. Chuyện gì xảy ra nếu bạn đưa các object mutable (có thể thay đổi) vào một HashSet, sau đó thay đổi giá trị của chúng?
7. Làm sao để mô phỏng một Hash Set nếu ngôn ngữ không hỗ trợ?
8. Kể tên các trường hợp nên dùng boolean array thay vì HashSet?
9. Hash Set mất thời gian truy xuất là bao nhiêu trong trường hợp worst-case?
10. `ConcurrentModificationException` trong HashSet là gì và làm thế nào để tránh?
11. Làm thế nào để implement các phép toán union, intersection, difference của toán học trên HashSet?
12. EnumSet có gì đặc biệt so với HashSet?
13. Việc thêm phần tử `null` vào HashSet có hợp lệ không? TreeSet thì sao?
14. Cơ chế giải quyết đụng độ (Collision Resolution) của HashSet trong Java là gì?
15. Rehashing xảy ra khi nào trong HashSet?
16. Làm thế nào để tạo một HashSet chỉ đọc (Read-only) trong Java?
17. So sánh chi phí bộ nhớ của HashSet với ArrayList chứa cùng lượng dữ liệu.
18. CopyOnWriteArraySet hoạt động như thế nào?
19. Giải thích sự khác biệt giữa phép toán `contains()` trên HashSet và `contains()` trên ArrayList.
20. Nếu HashSet có 10 triệu phần tử, thao tác `toArray()` có mất nhiều thời gian không?

## 21. 20 Lỗi thường gặp (20 Common Bugs)
1. Quên ghi đè (override) `hashCode()` khi đã override `equals()`.
2. Hàm `hashCode()` luôn trả về một hằng số, làm mất hoàn toàn hiệu năng của HashSet (biến nó thành LinkedList).
3. Đưa đối tượng mutable vào HashSet, sau đó chỉnh sửa trạng thái của đối tượng làm đổi giá trị `hashCode()`, gây tình trạng "mất tích" (không thể tìm thấy hay xóa đối tượng đó khỏi Set).
4. Khởi tạo HashSet với Capacity mặc định trong một vòng lặp lớn, dẫn đến overhead cấp phát lại bộ nhớ liên tục (rehashing).
5. Duyệt qua HashSet và kì vọng các phần tử được trả về theo thứ tự chèn.
6. Sử dụng TreeSet thay vì HashSet nhưng quên cài đặt interface `Comparable` cho Object.
7. Hiểu lầm rằng HashSet hoàn toàn không tiêu tốn bộ nhớ (O(1) memory), trong khi memory footprint của node và bucket array là khá lớn.
8. Thêm (Add) một mảng `int[]` vào HashSet thay vì các phần tử của nó (`Set<int[]>`), khiến Set kiểm tra trùng lặp theo tham chiếu mảng (reference).
9. Thêm đồng thời phần tử vào HashSet trong môi trường Multi-thread mà không sử dụng cấu trúc đồng bộ (`Collections.synchronizedSet` hoặc `ConcurrentHashMap.newKeySet()`).
10. Gây ra `ConcurrentModificationException` khi cố xóa phần tử khỏi HashSet trong lúc duyệt vòng lặp `for-each` thay vì dùng `Iterator.remove()`.
11. Bỏ qua giá trị trả về boolean của phương thức `add()` và `remove()` dẫn đến việc xử lý sai logic khi chèn trùng lặp.
12. Hàm `equals()` không phản xạ (reflexive), đối xứng (symmetric), hoặc bắc cầu (transitive).
13. So sánh trực tiếp hai HashSet bằng `==` thay vì `equals()`.
14. Khởi tạo `TreeSet` có chứa `null` dẫn đến `NullPointerException`.
15. Không làm sạch `HashSet` (clear) được dùng lại nhiều lần trong vòng lặp thuật toán, dẫn đến tràn dữ liệu cũ.
16. Nhầm lẫn giữa phương thức `remove()` của Set (dùng object) và `remove()` của List (có thể dùng index).
17. Sử dụng Hash Code không tương thích trong hệ thống phân tán, nơi object hash code được serialize và deserialize có thể không khớp.
18. Trộn lẫn các phiên bản generic và raw (ví dụ: `HashSet set = new HashSet(); set.add("String");`).
19. Lạm dụng HashSet để chứa quá ít phần tử (overhead của HashSet có thể tệ hơn việc duyệt mảng 3-4 phần tử).
20. Nhầm lẫn `retainAll()` với `removeAll()`.

## 22. 30 Trường hợp kiểm thử đặc biệt (30 Edge Cases)
1. Khởi tạo một Hash Set hoàn toàn trống (Empty set).
2. Kiểm tra `contains()` trên một Set trống (Empty set).
3. Thêm phần tử đầu tiên vào Set.
4. Thêm một phần tử đã tồn tại sẵn trong Set.
5. Thêm phần tử `null` vào Set.
6. Xóa phần tử `null` khỏi Set.
7. Xóa phần tử duy nhất trong Set.
8. Xóa phần tử không tồn tại trong Set.
9. Xóa toàn bộ phần tử trong Set (`clear()`).
10. Cố gắng thêm hàng loạt phần tử giống hệt nhau liên tiếp.
11. Hash Collision cực đoan: Thêm n object khác nhau nhưng `hashCode()` trả về cùng một giá trị.
12. Thêm và xóa xen kẽ liên tục trên cùng một Object.
13. Thêm số nguyên âm và số dương vào Set (cùng trị tuyệt đối) để kiểm tra hash code.
14. Khởi tạo Set với kích thước ban đầu siêu lớn (`initialCapacity` > số phần tử).
15. Set chứa số lượng phần tử vượt quá load factor (Kích hoạt quá trình Rehashing).
16. Phép tính Union với chính nó (`setA.addAll(setA)`).
17. Phép tính Intersection với chính nó (`setA.retainAll(setA)`).
18. Phép tính Difference với chính nó (`setA.removeAll(setA)`).
19. Iterator duyệt qua Set bị thay đổi bởi luồng khác.
20. `containsAll()` với một danh sách rỗng (luôn là true).
21. Thêm một Object cực lớn (Deep object hierarchy) vào Set.
22. Sử dụng object làm key có vòng lặp tham chiếu khép kín (circular reference) trong hàm `hashCode()`.
23. `TreeSet` xử lý custom `Comparator` với trường hợp đánh giá 2 đối tượng là "bằng nhau" (return 0) dù trường khác nhau (khiến `TreeSet` chèn đè hoặc bỏ qua).
24. Thêm số thực dấu phẩy động (float/double) có giá trị `NaN` vào Set.
25. Thêm các String độ dài 0 vào Set.
26. Kiểm thử hàm `toArray()` với mảng đầu vào quá nhỏ.
27. Đưa vào object mutable, đổi object, rồi kiểm tra `contains()`.
28. Iterator đang được gọi trên Set rỗng.
29. Gọi `Iterator.remove()` nhiều lần liên tiếp không có lời gọi `next()`.
30. Đẩy dữ liệu vào HashSet liên tục tới khi chạm giới hạn bộ nhớ (Out of Memory Error).

## 23. Tài liệu tham khảo (References)
- *Introduction to Algorithms (Cormen, Leiserson, Rivest, Stein)* - Chương Hash Tables.
- Tài liệu chính thức Java Documentation (Oracle) - `java.util.HashSet`.
- *Effective Java (Joshua Bloch)* - Các nguyên tắc về Override equals và hashCode.
- Hướng dẫn cấu trúc dữ liệu cơ bản trên GeeksforGeeks, LeetCode, HackerRank.

## 24. Kết luận (Conclusion)
Hash Set là cấu trúc dữ liệu cốt lõi, không thể thiếu khi giải bài toán tìm kiếm tối ưu, xử lý tính duy nhất và các phép toán tập hợp. Mặc dù nó sử dụng bộ nhớ lớn hơn các cấu trúc tuyến tính khác, tốc độ xử lý O(1) trung bình cho tra cứu, thêm và xóa khiến nó trở thành công cụ đắc lực nhất để tối ưu thời gian chạy (Time Complexity) cho vô số thuật toán và ứng dụng thực tiễn.
