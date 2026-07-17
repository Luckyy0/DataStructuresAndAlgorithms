# Metadata
- **Document ID**: DSA-04-02
- **Version**: 1.0
- **Prerequisites**: DSA-04-01 (Array Basics), DSA-02-04 (Amortized Analysis)
- **Learning Objectives**: Hiểu cấu trúc nội bộ của Dynamic Array (cụ thể là `ArrayList` trong Java). Nắm vững cơ chế Resize (Growth Policy), độ phức tạp Amortized $\mathcal{O}(1)$ cho thao tác Append, và các vấn đề về Memory Overhead do Boxing/Unboxing primitives.
- **Estimated Reading Time**: 55 phút
- **Difficulty**: Intermediate
- **Keywords**: Dynamic Array, ArrayList, Resize, Amortized Analysis, System.arraycopy, Capacity, Boxing

---

# 1 Purpose
Array mặc định trong Java có nhược điểm lớn nhất là **Fixed Size** (Kích thước cố định). Để giải quyết vấn đề này, Kỹ sư phần mềm cần một cấu trúc dữ liệu có khả năng **Tự động thay đổi kích thước** khi cần thiết. `ArrayList` chính là giải pháp - một mảng động (Dynamic Array) gói gọn (wrap) bên trong mảng nguyên thủy `Object[]`, tự động cấp phát lại bộ nhớ khi dữ liệu vượt quá sức chứa.

---

# 2 Motivation
Giả sử bạn cần đọc một luồng dữ liệu (Data Stream) từ File, nhưng bạn KHÔNG BIẾT TRƯỚC file có bao nhiêu dòng.
- Dùng Array thường: Bạn phải tạo mảng rất lớn `new String[1000000]`. Nếu file chỉ có 10 dòng, bạn lãng phí 99.9% bộ nhớ. Nếu file có 2 triệu dòng, bạn bị `ArrayIndexOutOfBoundsException`.
- Dùng `ArrayList`: Bắt đầu với mảng nhỏ. Mỗi khi đầy, nó tự động tìm không gian mới rộng hơn và chuyển nhà. Bạn không bao giờ phải lo về giới hạn kích thước hay lãng phí quá nhiều RAM.

---

# 3 Mathematical Foundation
## Amortized Analysis of Append Operation
Câu hỏi: Khi mảng đầy, ta phải copy toàn bộ $N$ phần tử sang mảng mới. Vậy thêm một phần tử vào cuối (Append) là $\mathcal{O}(N)$ hay $\mathcal{O}(1)$?
**Sử dụng Phương pháp Kế toán (Accounting Method):**
- Mảng bắt đầu với dung lượng 1, tự nhân đôi (2x) khi đầy.
- Thao tác gán (Write): Chi phí thực tế = 1$
- Khi cấp phát mới: Chi phí copy mỗi phần tử cũ = 1$
- Giả sử ta tính phí 3$ cho MỖI thao tác Append.
  - 1$ dùng để trả cho thao tác gán thực tế.
  - 1$ dùng để trả trước (deposit) cho phần tử này vào lần copy TƯƠNG LAI.
  - 1$ dùng để trả nợ cho một phần tử ở nửa mảng trước (chưa có deposit cho lần copy tới).
- Phép màu toán học: Số dư tài khoản (Credit) LUÔN luôn đủ để trả chi phí Copy khi mảng đầy!
**Kết luận**: Chi phí trung bình (Amortized) cho mỗi thao tác Append là một HẰNG SỐ (3 phép toán) $\rightarrow \mathcal{O}(1)$ Amortized.

---

# 4 Core Theory
## 4.1 Cơ chế hoạt động (Internals)
Một Dynamic Array được định nghĩa bởi 2 thuộc tính:
- `size` (Kích thước logic): Số lượng phần tử THỰC SỰ mà User đã thêm vào.
- `capacity` (Dung lượng vật lý): Kích thước của mảng `Object[]` ngầm bên dưới.
Luôn có: `0 <= size <= capacity`.

## 4.2 Growth Policy (Chính sách tăng trưởng)
Khi `size == capacity`, mảng bên dưới đã đầy. Nếu có yêu cầu `add()`, Dynamic Array sẽ:
1. Tạo một mảng mới với `newCapacity` lớn hơn.
2. Dùng `System.arraycopy()` để copy toàn bộ dữ liệu từ mảng cũ sang mảng mới.
3. Cập nhật con trỏ nội bộ trỏ tới mảng mới, mảng cũ bị Garbage Collector thu gom.

**Hệ số nhân (Growth Factor):**
- Java (`ArrayList`): `1.5x` (Chính xác là `oldCapacity + (oldCapacity >> 1)`).
- C++ (`std::vector`): Thường là `2x` (Tùy compiler).
- Python (`list`): $\approx 1.125x$.

## 4.3 Shrinking Policy (Chính sách thu hẹp)
- Khi `remove()` phần tử, `size` giảm đi, nhưng `capacity` của `ArrayList` **KHÔNG BAO GIỜ TỰ ĐỘNG GIẢM**.
- Nếu bạn add 1 triệu phần tử, rồi xóa đi 999,999 phần tử, `ArrayList` vẫn chiếm RAM của 1 triệu phần tử!
- Cách khắc phục thủ công: Gọi phương thức `trimToSize()`.

---

# 5 Visual Explanation

```mermaid
graph TD
    subgraph Bước 1: Khởi tạo
        A1[size=0, capacity=4<br/> [ _, _, _, _ ]]
    end
    
    subgraph Bước 2: Thêm 4 phần tử (Đầy)
        A2[size=4, capacity=4<br/> [ 1, 2, 3, 4 ]]
    end
    
    subgraph Bước 3: Thêm phần tử thứ 5 (Resize 1.5x = 6)
        A3[Tạo mảng mới cỡ 6]
        A4[Copy 4 phần tử cũ]
        A5[size=5, capacity=6<br/> [ 1, 2, 3, 4, 5, _ ]]
    end
    
    A1 -.->|add 1,2,3,4| A2
    A2 -.->|add 5| A3
    A3 --> A4 --> A5
```

---

# 6 Java Implementation
Triển khai một `MyArrayList` đơn giản hóa từ OpenJDK:

```java
import java.util.Arrays;

public class MyArrayList<E> {
    private static final int DEFAULT_CAPACITY = 10;
    private Object[] elementData;
    private int size;

    public MyArrayList() {
        this.elementData = new Object[DEFAULT_CAPACITY];
        this.size = 0;
    }

    // Time: Amortized O(1), Worst-case O(N)
    public void add(E element) {
        if (size == elementData.length) {
            grow(); // Resize khi mảng đầy
        }
        elementData[size] = element;
        size++;
    }

    // Time: O(N) do phải dời phần tử
    public void add(int index, E element) {
        checkPositionIndex(index);
        if (size == elementData.length) {
            grow();
        }
        // Dịch phải từ index tới cuối
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        elementData[index] = element;
        size++;
    }

    // Time: O(1)
    @SuppressWarnings("unchecked")
    public E get(int index) {
        checkElementIndex(index);
        return (E) elementData[index];
    }

    // Time: O(N) do phải dời phần tử
    @SuppressWarnings("unchecked")
    public E remove(int index) {
        checkElementIndex(index);
        E oldValue = (E) elementData[index];

        int numMoved = size - index - 1;
        if (numMoved > 0) {
            // Dịch trái để lấp chỗ trống
            System.arraycopy(elementData, index + 1, elementData, index, numMoved);
        }
        size--;
        elementData[size] = null; // Clear to let GC do its work
        return oldValue;
    }

    private void grow() {
        int oldCapacity = elementData.length;
        // Java ArrayList growth logic: 1.5x
        int newCapacity = oldCapacity + (oldCapacity >> 1); 
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private void checkElementIndex(int index) {
        if (index < 0 || index >= size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
    }

    private void checkPositionIndex(int index) {
        if (index < 0 || index > size) {
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        }
    }
}
```

---

# 8 Complexity Analysis
| Thao tác | Best Case | Worst Case (Amortized) | Ghi chú |
|---|---|---|---|
| Lấy phần tử (Get) | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | Dựa trên mảng ngầm |
| Cập nhật (Set) | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | |
| Thêm ở cuối (Add) | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ Amortized | Worst case $\mathcal{O}(N)$ khi cần resize |
| Thêm ở vị trí K | $\mathcal{O}(1)$ (K=size) | $\mathcal{O}(N)$ (K=0) | Dịch mảng bằng arraycopy |
| Xóa ở cuối (Remove) | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | Chỉ set null và giảm size |
| Xóa ở vị trí K | $\mathcal{O}(1)$ (K=size-1)| $\mathcal{O}(N)$ (K=0) | Dịch mảng lấp khoảng trống |

---

# 9 JVM Analysis
## Vấn đề Box/Unbox (Generics)
Trong Java, Generics bị xóa kiểu (Type Erasure) và trở thành `Object`.
Bạn **không thể** tạo `ArrayList<int>`. Bạn PHẢI tạo `ArrayList<Integer>`.
Hậu quả:
- `int[]`: Các số int nằm liên tiếp sát nhau trên bộ nhớ (4 bytes/phần tử).
- `ArrayList<Integer>`: Mảng `Object[]` chứa các **References** (Con trỏ - 4 bytes với Compressed Oops). Mỗi con trỏ trỏ ra một vùng nhớ riêng biệt chứa đối tượng `Integer` (16 bytes).
- Tổng bộ nhớ: Gấp 5-6 lần mảng nguyên thủy.
- Cache Locality: Vô dụng! Việc duyệt `ArrayList<Integer>` chậm hơn duyệt `int[]` đáng kể vì Cache Miss liên tục (CPU phải dereference pointer nhảy tứ tung trên Heap).

---

# 10 OpenJDK Analysis
## Khởi tạo lười biếng (Lazy Initialization)
Trong Java 8+, `new ArrayList<>()` KHÔNG tạo mảng cỡ 10 ngay lập tức!
Nó trỏ tới một mảng tĩnh rỗng `DEFAULTCAPACITY_EMPTY_ELEMENTDATA`.
Chỉ khi lệnh `add()` ĐẦU TIÊN được gọi, nó mới cấp phát mảng kích thước 10. Điều này giúp tối ưu cực kỳ nhiều RAM nếu hệ thống tạo ra hàng vạn ArrayList rỗng.

## System.arraycopy
Phương thức dời mảng bên trong `add(index)` và `remove(index)` luôn dùng `System.arraycopy()`. Dù độ phức tạp là $\mathcal{O}(N)$, nhờ được implement bằng C/C++ Native với tập lệnh SIMD/AVX (`memmove`), tốc độ của nó nhanh hơn vòng `for` thủ công hàng chục lần. Kể cả xóa ở vị trí 0 của mảng 100,000 phần tử cũng chỉ mất chưa tới 1 microsecond.

---

# 11 Production Usage
## So sánh các cấu trúc tương đồng
| Thuộc tính | `ArrayList` | `Vector` | `CopyOnWriteArrayList` | `LinkedList` |
|---|---|---|---|---|
| Thread-Safe | KHÔNG | CÓ (Synchronized) | CÓ (Copy khi viết) | KHÔNG |
| Tốc độ truy cập | Nhanh | Chậm hơn | Rất nhanh (Đọc) | Rất chậm |
| Overhead bộ nhớ | Thấp | Thấp | Rất cao khi ghi | Cực cao (Mỗi Node) |
| Khi nào dùng | 99% trường hợp | Legacy, không khuyên dùng | Đọc nhiều, ghi rất ít | Xóa/thêm liên tục ở hai đầu |

## Quy tắc vàng trong Production
**LUÔN khởi tạo trước dung lượng nếu biết (Pre-sizing):**
```java
// TỒI: Sẽ bị resize nhiều lần, tốn chi phí copy và tạo rác (Garbage)
List<String> list1 = new ArrayList<>();
for (int i = 0; i < 1_000_000; i++) list1.add(data[i]);

// TỐT NHẤT: Khởi tạo đúng dung lượng, KHÔNG BAO GIỜ bị resize!
List<String> list2 = new ArrayList<>(1_000_000);
for (int i = 0; i < 1_000_000; i++) list2.add(data[i]);
```

---

# 12 Design Decisions
**Tại sao Java chọn hệ số Growth Factor = 1.5 thay vì 2.0 (như C++)?**
Hệ số 2.0 có nhược điểm: Kích thước mảng mới LUÔN lớn hơn tổng kích thước của TẤT CẢ các mảng cũ cộng lại. (Ví dụ: 1, 2, 4, 8, 16. Mảng mới 16 lớn hơn tổng mảng cũ 1+2+4+8 = 15). Điều này khiến Memory Allocator của hệ điều hành KHÔNG THỂ tái sử dụng (reuse) các mảnh bộ nhớ cũ gộp lại thành mảng mới.
Hệ số 1.5 (hoặc tỷ lệ Vàng $1.618$) cho phép tái sử dụng bộ nhớ tốt hơn, thân thiện với Garbage Collector, đánh đổi lại số lần resize sẽ nhiều hơn một chút.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. Sửa danh sách trong vòng lặp `for-each` gây `ConcurrentModificationException`. (Phải dùng `Iterator.remove()`).
2. Dùng `ArrayList` cho dữ liệu Primitive lớn (gây OOM do Auto-boxing).
3. Quên gọi `trimToSize()` sau khi xóa một lượng dữ liệu khổng lồ (Memory Leak dạng ẩn).
4. Khởi tạo `new ArrayList<>()` trong vòng lặp vô hạn.
5. So sánh 2 List bằng `==` thay vì `.equals()`.
6. Dùng `remove(int index)` thay vì `remove(Object o)` (Ví dụ `list.remove(1)` trên danh sách số nguyên).
7. Tưởng `Arrays.asList()` trả về `java.util.ArrayList`. (Nó trả về mảng Fixed-size ẩn dạng List, add() sẽ ném Exception!).
8. Tưởng `List.subList()` tạo List mới độc lập. (Nó trả về một View trỏ thẳng vào List gốc. Đổi List gốc $\rightarrow$ View lỗi, Đổi View $\rightarrow$ List gốc bị đổi).
9. Thread-safety: Dùng ArrayList trong môi trường Multi-thread mà không bọc `Collections.synchronizedList()`.
10. Gọi `list.size()` trong vòng lặp `for (int i = 0; i < list.size(); i++)` kết hợp với lệnh `list.remove(i)` làm sai lệch index.
11. Lỗi IndexOutOfBounds do truy cập `list.get(list.size())`.
12. Gọi `contains()` trên `ArrayList` liên tục trong vòng lặp lồng nhau (Biến bài toán thành $\mathcal{O}(N^2)$ vì `contains` tốn $\mathcal{O}(N)$).
13. Thêm phần tử vào index lớn hơn kích thước hiện tại (Gây IndexOutOfBounds).
14. Hiểu lầm `capacity` và `size`. Tưởng `new ArrayList<>(100)` sẽ cho phép gọi `.set(50, val)` (Lỗi vì size vẫn là 0).
15. Không làm rỗng (clear) ArrayList static khi kết thúc xử lý (Memory Leak vĩnh viễn).
16. Xóa nhiều phần tử: Thay vì duyệt ngược `for (int i = size-1; i>=0)`, duyệt xuôi làm trượt index.
17. Dùng `ArrayList` thay vì `Deque` cho bài toán mô phỏng Hàng đợi (Queue/Stack) - Lấy phần tử đầu tốn $\mathcal{O}(N)$.
18. Clone (Copy) ArrayList dạng Shallow Copy. Sửa đổi object bên trong ảnh hưởng đến cả List gốc.
19. Gán List này cho List khác bằng phép `=` rồi clear List 1 $\rightarrow$ List 2 cũng trống rỗng.
20. Trả về tham chiếu của private ArrayList ra ngoài (Leaking reference).

---

# 14 Edge Cases
- Thêm phần tử `null` (ArrayList cho phép).
- Xóa phần tử khi danh sách rỗng (Throws Exception).
- Thêm vào index 0 (Tồi tệ nhất về performance, dịch toàn bộ mảng).
- Mảng resize khi dung lượng tiệm cận `Integer.MAX_VALUE` (Gây OutOfMemoryError).

---

# 15 Optimization Techniques
- Dùng primitive collections library (như **Trove**, **Eclipse Collections**, **FastUtil**) nếu cần mảng động cho `int, long, double` để tránh overhead.
- Gom nhóm thao tác xóa: Thay vì gọi `remove(i)` 100 lần (Tốn $\mathcal{O}(100 \times N)$), hãy dùng `removeAll()` hoặc duyệt 1 vòng chuyển các phần tử cần giữ lại lên đầu (Two Pointers), sau đó set null đoạn cuối một lần (Tốn $\mathcal{O}(N)$).

---

# 16 Best Practices
- Ưu tiên `List<String> list = new ArrayList<>();` (Code theo Interface).
- Dùng `Collections.emptyList()` nếu hàm cần trả về mảng rỗng để không tạo rác.
- Khởi tạo capacity trước nếu đọc data từ API / Database mà đã biết số lượng.
- Nếu phải Insert / Delete liên tục ở giữa danh sách với số lượng cực lớn, hãy suy nghĩ lại cấu trúc dữ liệu thay vì bám lấy ArrayList.

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Sự khác biệt giữa `Array` và `ArrayList`?
2. `ArrayList` cho phép lưu phần tử `null` không?
3. Tại sao `ArrayList` bị giới hạn dung lượng là số nguyên (`int`) mà không phải `long`?
4. Vector khác ArrayList ở điểm nào?
5. Làm thế nào để mảng không vượt quá dung lượng cần thiết? (`trimToSize`).

**Medium**
6. Giải thích Amortized $\mathcal{O}(1)$ của hàm `add()`.
7. Capacity khác Size ở chỗ nào?
8. Điều gì xảy ra khi bạn gọi `new ArrayList<>(100)` rồi gọi `list.get(5)`?
9. Cơ chế Resize hoạt động thế nào trong Java 8+? Hệ số là bao nhiêu?
10. Tại sao `contains()` trên ArrayList tốn thời gian lâu? Cải thiện thế nào?
11. Trình bày chi tiết LỖI `ConcurrentModificationException`.
12. Sự khác biệt giữa `Arrays.asList(arr)` và `new ArrayList<>(Arrays.asList(arr))`?

**Hard & Senior**
13. Giải thích tại sao Growth Factor 1.5 lại tốt hơn 2.0 (Gợi ý: Memory reuse).
14. Hệ thống Low Latency cấm dùng `ArrayList<Integer>`, tại sao? (Boxing/Cache misses).
15. Implement hàm `removeIf(Predicate)` của ArrayList sao cho tối ưu đạt $\mathcal{O}(N)$ thời gian thay vì $\mathcal{O}(N^2)$.
16. Phân tích `CopyOnWriteArrayList` và Thread-Safety trong kịch bản Read-Heavy.
17. Phân tích memory footprint byte-by-byte của `ArrayList<Integer>` so với `int[]` với 1000 phần tử.

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [02-Dynamic-Arrays-Problems.md](02-Dynamic-Arrays-Problems.md).

---

# 23 Summary
`ArrayList` là sự kết hợp giữa tốc độ truy cập $\mathcal{O}(1)$ tuyệt vời của Mảng (Array) và tính linh hoạt tự điều chỉnh dung lượng. Bằng chiến lược Amortized Resizing, nó duy trì hiệu suất chèn ở mức $\mathcal{O}(1)$ trung bình. Kỹ sư giỏi cần nhớ 3 quy tắc: Khởi tạo dung lượng sẵn, Cẩn thận khi Xóa phần tử, và Tránh dùng cho Primitive types ở môi trường hiệu năng khắc nghiệt.

---

# 24 Checklist
- [ ] Giải thích được tại sao `add()` là $\mathcal{O}(1)$ Amortized.
- [ ] Biết dùng `System.arraycopy` để dời mảng.
- [ ] Luôn Pre-size ArrayList khi có thể.
- [ ] Không dùng vòng lặp thường để gọi `remove()`.
- [ ] Hiểu Overhead của Generics (Boxing).
