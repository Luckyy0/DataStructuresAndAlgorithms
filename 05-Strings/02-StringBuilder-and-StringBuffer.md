# 02 - StringBuilder và StringBuffer

## 1. Metadata
- **Topic**: StringBuilder and StringBuffer
- **Level**: Intermediate
- **Prerequisites**: String basics, Arrays, Time Complexity
- **Language**: Java 21

## 2. Purpose (Mục đích)
- Hiểu rõ bản chất của chuỗi có thể thay đổi (mutable strings) trong Java.
- Phân biệt được sự khác nhau giữa `StringBuilder` và `StringBuffer` (đặc biệt là Thread safety).
- Nắm bắt cơ chế mở rộng bộ nhớ (capacity growth) của mảng nội bộ.
- Hiểu được chi phí hiệu năng (performance overhead) của toán tử `+` trong vòng lặp và cách JVM tối ưu hóa.

## 3. Motivation (Động lực)
- String trong Java là immutable, việc nối chuỗi liên tục tạo ra nhiều đối tượng rác, gây tốn bộ nhớ và tăng thời gian chạy của Garbage Collector.
- `StringBuilder` cung cấp giải pháp xây dựng chuỗi hiệu quả, giảm thiểu tối đa việc cấp phát lại bộ nhớ.
- Khi làm việc trong môi trường đa luồng (multi-threaded), `StringBuffer` đảm bảo tính đồng bộ (thread-safe) mà không làm mất tính đúng đắn của dữ liệu.

## 4. Mathematical Foundation (Nền tảng Toán học)
- Mảng ký tự nội bộ (`char[]` hoặc `byte[]` trong Java 9+) có kích thước giới hạn (capacity).
- Khi thêm phần tử vượt quá capacity, mảng mới được tạo ra với kích thước thường là: $NewCapacity = (OldCapacity \times 2) + 2$.
- Thời gian khấu hao (Amortized time) cho thao tác `append` là $O(1)$. Tuy nhiên, trong trường hợp xấu nhất (cần mở rộng mảng), thời gian là $O(N)$ với $N$ là kích thước mới.

## 5. Core Theory (Lý thuyết Cốt lõi)
- **Mutable Strings**: Trái với `String`, nội dung của `StringBuilder` và `StringBuffer` có thể thay đổi trực tiếp trên bộ nhớ đã cấp phát.
- **Thread Safety**: `StringBuffer` sử dụng từ khóa `synchronized` trên hầu hết các phương thức, đảm bảo thread-safe nhưng giảm hiệu năng do lock overhead. `StringBuilder` không synchronized, chạy nhanh hơn và được khuyên dùng trong môi trường single-thread.
- **Capacity vs Length**:
  - `length`: Số lượng ký tự hiện tại đang chứa trong chuỗi.
  - `capacity`: Kích thước tối đa của mảng nội bộ trước khi cần phải cấp phát mảng mới.
- **`byte[]` trong Java 9+**: Sử dụng Compact Strings, lưu trữ Latin-1 bằng 1 byte/ký tự, UTF-16 bằng 2 bytes/ký tự, giúp tiết kiệm bộ nhớ.

## 6. Visual Explanation (Giải thích trực quan)
```mermaid
stateDiagram-v2
    state "Capacity: 16, Length: 0" as C1
    state "Capacity: 16, Length: 5" as C2
    state "Capacity: 16, Length: 15" as C3
    state "Capacity: 34, Length: 18" as C4
    
    [*] --> C1 : new StringBuilder()
    C1 --> C2 : append("Hello")
    C2 --> C3 : append(" World!!!!")
    C3 --> C4 : append(" OK") 
    note right of C4 : Mảng đầy, mở rộng: (16 * 2) + 2 = 34
```

## 7. Java Implementation (Cài đặt mô phỏng trong Java)
```java
public class CustomStringBuilder {
    private char[] value;
    private int count;

    public CustomStringBuilder(int capacity) {
        value = new char[capacity];
    }
    
    public CustomStringBuilder() {
        this(16);
    }

    public CustomStringBuilder append(String str) {
        if (str == null) str = "null";
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    }

    private void ensureCapacityInternal(int minimumCapacity) {
        if (minimumCapacity - value.length > 0) {
            int newCapacity = (value.length << 1) + 2;
            if (newCapacity - minimumCapacity < 0) {
                newCapacity = minimumCapacity;
            }
            if (newCapacity < 0) {
                if (minimumCapacity < 0) throw new OutOfMemoryError();
                newCapacity = Integer.MAX_VALUE;
            }
            char[] copy = new char[newCapacity];
            System.arraycopy(value, 0, copy, 0, count);
            value = copy;
        }
    }
    
    @Override
    public String toString() {
        return new String(value, 0, count);
    }
}
```

## 8. Step-by-Step (Chi tiết hoạt động)
- **`append`**: Nối dữ liệu vào cuối. Nếu thiếu chỗ, mở rộng `capacity`, sau đó chép dữ liệu vào (arraycopy).
- **`insert`**: Dịch chuyển các ký tự từ vị trí chèn về phía sau, sau đó chép dữ liệu mới vào chỗ trống. Tốn chi phí dịch chuyển $O(N)$.
- **`delete`**: Dịch chuyển các ký tự phía sau phần bị xóa lên lấp chỗ trống. Kích thước mảng (capacity) không giảm.
- **`reverse`**: Đảo ngược chuỗi tại chỗ (in-place) bằng cách hoán đổi ký tự từ hai đầu mảng vào giữa. Với surrogate pairs (UTF-16), cần xử lý cẩn thận để không phá vỡ cặp ký tự.

## 9. Complexity Analysis (Độ phức tạp)
| Thao tác | Time Complexity | Space Complexity | Ghi chú |
|----------|----------------|------------------|--------|
| `append` | Amortized $O(1)$ | $O(1)$ (nếu không mở rộng) | Nếu mở rộng mảng: $O(N)$ |
| `insert` | $O(N)$ | $O(1)$ | Dịch chuyển các ký tự |
| `delete` | $O(N)$ | $O(1)$ | Dịch chuyển ký tự lấp chỗ |
| `reverse`| $O(N)$ | $O(1)$ | Duyệt qua nửa mảng |
| `charAt` | $O(1)$ | $O(1)$ | Truy cập mảng theo index |

## 10. JVM Analysis (Phân tích cấp độ JVM)
- Toán tử `+` để nối chuỗi (String concatenation):
  - Trước Java 9: Trình biên dịch (Javac) sẽ biên dịch mã nối chuỗi `a + b` thành `new StringBuilder().append(a).append(b).toString()`. Việc nối chuỗi trong vòng lặp bằng `+` sẽ tạo ra một đối tượng `StringBuilder` mới mỗi vòng lặp, dẫn tới rác bộ nhớ (Memory Churn).
  - Từ Java 9+: Sử dụng `InvokeDynamic` và `StringConcatFactory` giúp tối ưu hóa việc ghép chuỗi lúc chạy (runtime), hiệu năng cao hơn, nhưng vẫn nên dùng `StringBuilder` tường minh trong vòng lặp.

## 11. OpenJDK Analysis (Mã nguồn OpenJDK)
- Trong Java 8, `StringBuilder` kế thừa từ `AbstractStringBuilder` với `char[] value`.
- Từ Java 9+, OpenJDK thay đổi `value` thành `byte[] value` và thêm thuộc tính `byte coder` (0 là LATIN1, 1 là UTF16) (Compact Strings). Nếu chuỗi chỉ chứa ASCII, nó chỉ chiếm 1 byte mỗi ký tự, tiết kiệm 50% RAM so với `char[]`.
- Phương thức mở rộng capacity sử dụng `newCapacity = (oldCapacity << 1) + 2`. Tại sao cộng 2? Để đảm bảo rằng ngay cả khi oldCapacity = 0 (khởi tạo với mảng rỗng), newCapacity sẽ bằng 2 thay vì 0, và bù đắp một chút bộ nhớ dự phòng ở những mảng siêu nhỏ.

## 12. Production Usage (Sử dụng trong thực tế)
- **Luôn dùng `StringBuilder`** khi cần ghép chuỗi trong vòng lặp hoặc khi độ dài chuỗi tạo ra không xác định.
- **Khởi tạo Capacity:** Cố gắng ước tính và truyền kích thước dự định vào hàm tạo `new StringBuilder(estimatedSize)` để tránh việc mảng bị sao chép và cấp phát lại nhiều lần.
- Chỉ dùng `StringBuffer` trong các hệ thống cũ hoặc khi nhiều luồng thực sự chia sẻ cùng một đối tượng bộ đệm chuỗi (rất hiếm gặp thực tế).

## 13. Design Decisions (Quyết định thiết kế)
- **Tại sao có 2 lớp riêng biệt thay vì 1 lớp có cờ (flag) synchronized?** 
  - Gắn lock (`synchronized`) làm giảm hiệu suất rất nhiều. Hầu hết các thao tác tạo chuỗi chỉ xảy ra trên một thread (local variable). Do đó, Java 1.5 giới thiệu `StringBuilder` để phục vụ riêng cho môi trường đơn luồng, tối ưu tốc độ mà không cần lock.
- **String bất biến (Immutable):** Do `String` bất biến, nó an toàn để làm key trong HashMap. Ngược lại, `StringBuilder` thay đổi được, không nên làm key.

## 14. Common Bugs (20 Lỗi phổ biến)
1. Dùng toán tử `+` trong vòng lặp `for` lớn gây OutOfMemoryError.
2. Nhầm lẫn giữa `length()` và `capacity()`.
3. Quên không khởi tạo dung lượng ban đầu (`StringBuilder(int)`) khi biết rõ kích thước, dẫn đến re-allocation nhiều lần.
4. Trả về tham chiếu của StringBuilder thay vì gọi `toString()` khiến đối tượng vô tình bị thay đổi từ bên ngoài.
5. So sánh 2 đối tượng StringBuilder bằng `equals()` thay vì `toString().equals()`. (StringBuilder không override equals()).
6. Dùng `StringBuffer` trong môi trường đơn luồng gây lãng phí hiệu suất vì lock.
7. Đưa `StringBuilder` vào `HashSet` hoặc dùng làm key trong `HashMap`.
8. Quên rằng phương thức `reverse()` thay đổi trực tiếp chuỗi hiện tại (in-place).
9. Gây ra chu kỳ đệ quy khi `StringBuilder.append(this)`.
10. Sử dụng StringBuilder trên nhiều luồng mà không có đồng bộ, gây hỏng dữ liệu.
11. Bỏ qua giá trị trả về của `append` khi gán chuỗi kiểu cascading (ví dụ: gán nhầm hoặc quên nối tiếp).
12. Cố gắng `append` một giá trị null mà không nhận ra nó sẽ thành chuỗi "null".
13. Nhầm lẫn index khi dùng `delete(start, end)` - end là exclusive.
14. Lỗi IndexOutOfBoundsException khi gọi `insert` ở vị trí ngoài phạm vi độ dài chuỗi hiện tại.
15. Không làm rỗng StringBuilder (`setLength(0)`) khi dùng lại cùng một đối tượng trong các vòng lặp ngoài, làm chuỗi ngày càng dài.
16. Dùng `delete` để xóa từng ký tự một trong vòng lặp, dẫn tới độ phức tạp $O(N^2)$.
17. Cắt chuỗi con bằng `substring()` trả về một `String` tạo ra bộ nhớ mới thay vì tận dụng StringBuilder.
18. Không quan tâm tới ký tự Surrogate Pair khi dùng `reverse()` trên dữ liệu chứa Emoji (Java xử lý đúng nhưng nếu dùng logic tự viết thì dễ sai).
19. Gán `null` cho StringBuilder thay vì `setLength(0)` khiến GC phải làm việc nhiều.
20. Mảng tăng kích thước quá lớn, vượt quá giới hạn Integer.MAX_VALUE gây lỗi OutOfMemoryError.

## 15. Edge Cases (30 Trường hợp biên)
1. Thêm chuỗi rỗng `""` vào StringBuilder.
2. Thêm giá trị `null` (StringBuilder sẽ append ký tự 'n', 'u', 'l', 'l').
3. Capacity ban đầu được cấu hình là 0 (`new StringBuilder(0)`).
4. `delete` với start = end (không có gì thay đổi).
5. `delete` với end > length (tự động cắt về length).
6. Gọi `reverse()` trên một StringBuilder rỗng.
7. Gọi `reverse()` trên chuỗi độ dài 1.
8. Thêm các chuỗi chỉ chứa Emoji có độ dài 4 bytes (Surrogate pair).
9. Mảng nội bộ vừa khít đến phần tử cuối cùng trước khi bị yêu cầu `append` 1 byte.
10. Chuỗi kết quả có độ dài chạm giới hạn mảng của JVM (khoảng 2GB).
11. Dùng `setLength(0)` sau đó `append` dữ liệu siêu dài, mảng đã phình to không tự động thu nhỏ.
12. Gọi `trimToSize()` khi dung lượng đúng bằng độ dài chuỗi.
13. `insert` vào đầu chuỗi (`insert(0, str)`), dời toàn bộ phần tử $O(N)$.
14. `insert` vào cuối chuỗi (tương tự append nhưng chậm hơn đôi chút do bước kiểm tra).
15. Khởi tạo bằng `CharSequence` là chính bản thân StringBuilder đó.
16. `deleteCharAt` ở vị trí đầu, cuối và giữa.
17. So sánh `StringBuilder(10)` và `StringBuilder(20)` lúc mới khởi tạo (cùng rỗng nhưng capacity khác nhau).
18. `replace` với chuỗi thay thế ngắn hơn chuỗi bị thay thế.
19. `replace` với chuỗi thay thế dài hơn chuỗi bị thay thế (phải đẩy phần tử về sau).
20. `append` một mảng `char[]` một phần.
21. Thêm tham chiếu đến mảng `char[]` bằng null.
22. `appendCodePoint()` với giá trị codepoint nằm ngoài BMP (Basic Multilingual Plane).
23. Gán `setLength(newLength)` với newLength > length cũ, các ký tự thêm vào mặc định là `\u0000`.
24. StringBuilder là biến tĩnh (static variable), bị chia sẻ giữa nhiều tác vụ đơn luồng nhưng chồng chéo.
25. Cố gắng thêm mảng có size = 0.
26. Khởi tạo StringBuilder với chuỗi dài > 16, capacity sẽ là chuỗi đó + 16.
27. Đảo ngược một chuỗi mà nội dung là một Palindrome hoàn hảo.
28. Gây lỗi tràn bộ nhớ (Heap size limit) với vòng lặp `append` vô hạn.
29. Cố gắng sử dụng `indexOf` trong StringBuilder.
30. Đọc mảng bị cắt đứt giữa Surrogate Pair.

## 16. Optimization (Tối ưu hóa)
- Khởi tạo với dung lượng bằng hoặc lớn hơn một chút so với độ dài dự kiến.
- Dùng `sb.setLength(0)` để tái sử dụng đối tượng `StringBuilder` có sẵn thay vì `new StringBuilder()` liên tục (như trong logging format).
- Sử dụng `trimToSize()` khi cần giải phóng bớt bộ nhớ sau một quá trình `append` khổng lồ và đối tượng này sẽ tồn tại lâu dài trên heap.

## 17. Best Practices (Thực hành tốt nhất)
- Hạn chế dùng `StringBuffer` trừ khi bắt buộc thread-safety.
- Tránh gọi `toString()` nhiều lần không cần thiết.
- Nếu phải ghép nhiều chuỗi ngắn cố định (vd: `a + " " + b`), `+` (String concatenation) ở Java 9+ đôi khi ngắn gọn và hiệu suất đủ tốt (nhờ invokedynamic), nhưng ở quy mô lớn hoặc trong vòng lặp thì phải dùng `StringBuilder`.
- Chaining (ví dụ: `sb.append(a).append(b)`) giúp mã rõ ràng hơn và chỉ gọi một biến tham chiếu.

## 18. Benchmark (Đánh giá hiệu năng)
```java
// Ghép 100,000 chuỗi:
// 1. Dùng toán tử + (trong vòng lặp) -> ~ 15000ms (và sinh rất nhiều Garbage).
// 2. Dùng StringBuilder (không cấu hình capacity) -> ~ 5ms.
// 3. Dùng StringBuilder (với capacity ban đầu là 100,000 * length) -> ~ 2ms.
```
- Việc cấp phát động và chép mảng ở cách (2) tốn thời gian hơn cách (3). Toán tử `+` chậm khủng khiếp ở cách (1) vì nó chạy $O(N^2)$ ở tổng thời gian (mỗi vòng lặp tạo 1 đối tượng StringBuilder, rồi chuyển về String, chuỗi cứ thế phình to phải copy lại toàn bộ liên tục).

## 19. Unit Testing
```java
@Test
void testStringBuilderCapacity() {
    StringBuilder sb = new StringBuilder(10);
    assertEquals(10, sb.capacity());
    assertEquals(0, sb.length());
    
    sb.append("0123456789");
    assertEquals(10, sb.length());
    assertEquals(10, sb.capacity());
    
    sb.append("A");
    assertEquals(11, sb.length());
    assertEquals(22, sb.capacity()); // (10 * 2) + 2
}
```

## 20. Interview Questions (Câu hỏi phỏng vấn)
1. Phân biệt `String`, `StringBuilder` và `StringBuffer`.
2. Tại sao `StringBuilder` nhanh hơn `StringBuffer`?
3. Toán tử `+` để nối chuỗi hoạt động như thế nào trong Java 8 và Java 9+?
4. Điều gì xảy ra khi một `StringBuilder` hết dung lượng (capacity)?
5. Độ phức tạp của phương thức `append` là bao nhiêu?
6. Tại sao công thức tăng kích thước lại là `(oldCapacity * 2) + 2`?
7. Sự khác biệt giữa `length()` và `capacity()`?
8. Tại sao không nên sử dụng `StringBuilder` làm khóa (key) trong `HashMap`?
9. Phương thức `equals()` của `StringBuilder` có so sánh nội dung không?
10. Compact Strings ở Java 9 ảnh hưởng gì đến `StringBuilder`?
11. Khi nào nên dùng `StringBuffer` trong dự án mới?
12. Phương thức `reverse()` hoạt động như thế nào?
13. Nếu bạn truyền `null` vào `StringBuilder.append()`, chuyện gì xảy ra?
14. Có thể thu nhỏ dung lượng của `StringBuilder` lại được không? Bằng cách nào?
15. Bạn sẽ tối ưu hóa một vòng lặp sử dụng quá nhiều `StringBuilder` được cấp phát mới như thế nào?
16. Việc sử dụng `sb.setLength(0)` khác gì với `sb = new StringBuilder()` trong vòng lặp?
17. Điều gì xảy ra nếu cố gắng chèn (`insert`) một chuỗi vào vị trí ngoài độ dài thực tế của `StringBuilder`?
18. Trình biên dịch có thể tự động thay thế việc nối chuỗi bằng `StringBuilder` không? Hạn chế của nó là gì?
19. So sánh chi phí thời gian của `insert` so với `append`.
20. `StringBuilder` có phải là thread-safe không? Làm sao để đồng bộ hóa nếu cần thiết mà không dùng `StringBuffer`?

## 21. Practice Problems Link
[Practice Problems](./02-StringBuilder-and-StringBuffer-Problems.md)

## 22. Pattern Recognition (Nhận diện mẫu)
- Các bài toán thao tác trên chuỗi mà độ dài kết quả thay đổi (như giải nén, mã hóa, thêm xóa ký tự).
- Duyệt và xây dựng một chuỗi kết quả từng phần tử một.
- Các thao tác cần thay đổi (mutate) tại một index nhất định, sau đó in ra kết quả.

## 23. Real Case Study (Nghiên cứu tình huống thực tế)
- **Log generation**: Khi gom các thông tin log (timestamp, level, message, stacktrace), `StringBuilder` giúp tạo ra chuỗi cuối cùng một cách nhanh chóng.
- **SQL Query Builder**: Các ORM như Hibernate xây dựng truy vấn SQL động sử dụng logic nối chuỗi liên tục tùy vào các điều kiện.
- **JSON/XML Serialization**: Chuyển đổi các đối tượng Java sang định dạng chuỗi JSON bằng cách dùng `StringBuilder` để gom nối các trường.

## 24. Summary & Checklist (Tóm tắt & Checklist)
- [ ] Nắm được cách cấp phát mảng của StringBuilder.
- [ ] Hiểu được vì sao String immutable và phải cần tới StringBuilder.
- [ ] Phân biệt được sự khác biệt với StringBuffer.
- [ ] Biết cách tối ưu hóa hiệu năng bằng cách khởi tạo capacity đúng cách.
- [ ] Không sử dụng StringBuilder trong môi trường song song (multi-thread) mà không có cơ chế khoá bảo vệ.
- [ ] Nhớ gọi `toString()` để lấy kết quả thay vì thao tác trên tham chiếu của builder.
- [ ] Tránh dùng `equals()` trên StringBuilder.
