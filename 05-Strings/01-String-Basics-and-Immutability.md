# Java String Basics and Immutability

## 1. Metadata
- **Cấp độ:** Beginner to Intermediate
- **Chủ đề:** Java String, Immutability, String Pool, Memory Management
- **Phiên bản Java:** 21+

## 2. Purpose
Tài liệu này cung cấp cái nhìn toàn diện về chuỗi (String) trong Java, tập trung vào bản chất bất biến (Immutability), cơ chế quản lý bộ nhớ qua String Pool, và các thay đổi quan trọng trong JVM từ Java 9+ (Compact Strings).

## 3. Motivation
String là kiểu dữ liệu được sử dụng phổ biến nhất trong mọi ứng dụng Java. Hiểu sâu về cấu trúc bên trong, cách JVM cấp phát và thu hồi bộ nhớ cho String sẽ giúp kỹ sư viết mã hiệu suất cao, tránh các lỗi liên quan đến Memory Leaks và Thread Safety.

## 4. Mathematical Foundation
Dưới góc độ toán học, một chuỗi $S$ là một chuỗi hữu hạn các ký tự được lấy từ một bảng chữ cái $\Sigma$. 
Độ dài của chuỗi $|S| = n$.
Ký tự tại vị trí $i$ (với $0 \le i < n$) là $S[i]$.
Với Java 9+, một chuỗi có thể lưu trữ dưới dạng một mảng byte thay vì char, phụ thuộc vào việc các ký tự có thuộc $\Sigma_{LATIN1}$ hay không.

## 5. Core Theory
- **Immutability:** Đối tượng String sau khi được khởi tạo sẽ không bao giờ thay đổi giá trị. Mọi thao tác biến đổi đều trả về một đối tượng String mới. Lý do bao gồm: Security (Bảo mật - như các tham số truyền vào database, network), Caching (Băm HashCode nhanh chóng), Thread safety (An toàn khi xử lý đa luồng).
- **String Pool:** Vùng nhớ đặc biệt trong Heap (trước Java 7 là PermGen) dùng để lưu trữ các chuỗi literal. Giúp tiết kiệm bộ nhớ khi có nhiều chuỗi giống nhau.
- **String.intern():** Phương thức giúp đưa một chuỗi được tạo bằng từ khóa `new` vào trong String Pool nếu nó chưa tồn tại, và trả về tham chiếu từ Pool.
- **Java 9+ Compact Strings:** Tối ưu hóa bộ nhớ bằng cách lưu trữ chuỗi dưới dạng `byte[]` thay vì `char[]`. Nếu chuỗi chỉ chứa ký tự Latin-1 (1 byte), nó sẽ dùng 1 byte/ký tự. Nếu có ký tự UTF-16, nó sẽ dùng 2 byte/ký tự.
- **UTF-16 Encoding:** Chuỗi nội bộ của Java xử lý văn bản ở dạng UTF-16, đòi hỏi cẩn trọng với các Surrogate Pairs khi làm việc với Emoji hoặc ký tự đặc biệt.
- **`==` vs `.equals()`:** `==` so sánh địa chỉ vùng nhớ (tham chiếu). `.equals()` so sánh nội dung chuỗi.
- **GC Implications of `substring()`:** Ở Java 6, `substring()` chia sẻ mảng char cơ sở, dẫn đến rò rỉ bộ nhớ nếu chuỗi gốc cực lớn. Từ Java 7, `substring()` luôn sao chép mảng sang mảng mới.

## 6. Visual Explanation
```mermaid
graph TD
    A[String s1 = "Hello"] -->|References| B(String Pool: "Hello")
    C[String s2 = "Hello"] -->|References| B
    D[String s3 = new String("Hello")] -->|References| E(Heap: "Hello" Object)
    E -.->|Internal Reference| B
    F[s3.intern()] -->|Returns| B
```

## 7. Java Implementation
```java
public class StringBasics {
    public static void main(String[] args) {
        String s1 = "DSA"; // String Pool
        String s2 = "DSA"; // String Pool
        String s3 = new String("DSA"); // Heap
        
        System.out.println(s1 == s2); // true
        System.out.println(s1 == s3); // false
        System.out.println(s1.equals(s3)); // true
        
        String s4 = s3.intern();
        System.out.println(s1 == s4); // true
    }
}
```

## 8. Step-by-Step
Khi dòng `String s = "Java"` chạy:
1. JVM kiểm tra trong String Pool xem đã có "Java" chưa.
2. Nếu chưa, tạo một đối tượng String chứa "Java" trong Pool và trả về tham chiếu.
3. Nếu có, trả về tham chiếu đến chuỗi hiện có.
Khi `String s2 = new String("Java")` chạy:
1. "Java" literal được đưa vào Pool nếu chưa có.
2. Từ khóa `new` ép JVM tạo một object String hoàn toàn mới trong Heap, nội dung sao chép từ literal trong Pool.

## 9. Complexity Analysis
- **Khởi tạo literal:** $O(1)$ đến $O(L)$ (với $L$ là độ dài chuỗi để tính hash và kiểm tra Pool).
- **So sánh `equals`:** $O(L)$ (tốt nhất $O(1)$ nếu cùng tham chiếu hoặc khác độ dài).
- **Nối chuỗi `+`:** $O(L_1 + L_2)$.
- **`substring`:** $O(L)$ từ Java 7+ do phải sao chép mảng.
- **Space Complexity:** $O(L)$ (với Compact Strings là 1 byte hoặc 2 byte mỗi ký tự).

## 10. JVM Analysis
JVM tối ưu việc gọi liên tiếp toán tử `+` trên String bằng cách sử dụng `StringBuilder` hoặc từ Java 9 dùng `StringConcatFactory` (sử dụng `invokedynamic` bytecode) giúp tối ưu hóa tốt hơn và sinh mã máy nhanh hơn.

## 11. OpenJDK Analysis
Trong mã nguồn OpenJDK, `java.lang.String` có thuộc tính `private final byte[] value;` và `private final byte coder;` (`coder` là `0` cho LATIN1, `1` cho UTF-16). Điều này đảm bảo tính đóng gói và không cho phép can thiệp trực tiếp thay đổi nội dung.

## 12. Production Usage
- String thường làm khóa (key) cho `HashMap` do đặc tính Immutable (HashCode được cache và không đổi).
- Tránh việc ghép chuỗi trong vòng lặp bằng `+`, sử dụng `StringBuilder` để tránh rác (Garbage Collection overhead).

## 13. Design Decisions
Quyết định biến String thành Immutable ở Java là để:
- Hỗ trợ String Pool (nếu thay đổi được, một biến đổi thay đổi sẽ làm sai lệch các tham chiếu khác).
- Đảm bảo an toàn bảo mật (không ai có thể lén đổi đường dẫn tệp tin hoặc chuỗi truy vấn cơ sở dữ liệu sau khi đã duyệt qua lớp xác thực).

## 14. Common Bugs
1. Sử dụng `==` để so sánh nội dung chuỗi (logic error).
2. Gọi hàm thay đổi String nhưng không lưu lại kết quả (`s.trim();` thay vì `s = s.trim();`).
3. NullPointerException do gọi phương thức trên chuỗi null (nên dùng `"const".equals(s)`).
4. Dùng toán tử `+` trong vòng lặp gây tràn bộ nhớ.
5. So sánh chuỗi với StringBuilder dùng `equals()` (luôn false).
6. Quên `hashCode()` khi tự viết class bao bọc String.
7. Rò rỉ bộ nhớ khi sử dụng `String.intern()` bừa bãi trong các bản Java cũ (đầy PermGen).
8. Sử dụng `split` với ký tự regex đặc biệt mà quên escape (vd: `split(".")` thay vì `split("\\.")`).
9. Lỗi encode/decode khi gọi `getBytes()` mà không chỉ định `Charset`.
10. So sánh chuỗi không phân biệt hoa thường bằng `toLowerCase().equals()` thay vì `equalsIgnoreCase()` (tạo rác thừa).
11. Xử lý Surrogate pairs bị lỗi khi dùng `length()` thay vì `codePointCount()`.
12. Vô tình in ra đối tượng mảng `char[]` bằng cách dùng `.toString()` mặc định thay vì `String.valueOf()`.
13. Chuyển đổi số sang chuỗi bằng `"" + number` thay vì `String.valueOf(number)`.
14. Tự tạo String rỗng bằng `new String()` thay vì `""`.
15. Không xử lý khoảng trắng ẩn (như non-breaking space) khi `.trim()`.
16. Nhầm lẫn giữa `isEmpty()` và `isBlank()` (Java 11).
17. Dùng `StringBuffer` khi không có đa luồng (chậm hơn `StringBuilder`).
18. Không kiểm tra null trước khi thao tác chuỗi.
19. Gán null vào biểu thức nối chuỗi (sẽ biến thành chuỗi `"null"` thay vì lỗi).
20. Hardcode chuỗi cho thư mục thay vì `File.separator`.

## 15. Edge Cases
1. Chuỗi `null`.
2. Chuỗi rỗng `""`.
3. Chuỗi chỉ chứa một khoảng trắng `" "`.
4. Chuỗi chứa ký tự null `\0` ở giữa `"a\0b"`.
5. Chuỗi rất lớn vượt quá bộ nhớ.
6. Ký tự Emoji cần 2 chars (surrogate pair).
7. Chuỗi chỉ chứa các ký tự đặc biệt.
8. Ký tự non-breaking space.
9. Dấu gạch chéo ngược `\`.
10. `s.substring(0, 0)` trả về chuỗi rỗng.
11. `s.substring(s.length())` trả về chuỗi rỗng.
12. `indexOf` với chỉ mục bắt đầu âm.
13. `charAt` với index ngoài mảng.
14. Chỉ mục lớn hơn độ dài chuỗi trong `substring`.
15. `replace()` không tìm thấy chuỗi nào để thay.
16. Ghép một phần tử null vào String.
17. `split()` với chuỗi đầu vào rỗng.
18. `split()` trả về mảng rỗng nếu chuỗi toàn delimiter.
19. Ký tự viết hoa phức tạp trong Locale khác nhau (như chữ 'I' trong tiếng Thổ Nhĩ Kỳ).
20. Chuỗi được giải mã từ byte buffer không hợp lệ.
21. Tạo chuỗi từ mảng char lớn nhưng chỉ dùng 1 phần `new String(charArray, offset, count)`.
22. `toUpperCase` không đổi độ dài chuỗi? (Một số ký tự tiếng Đức `ß` đổi thành `SS` tăng độ dài).
23. Gán chuỗi bằng `StringBuilder.toString()` liên tục.
24. Dùng `String.format()` với sai số lượng đối số.
25. Tham số `regex` của `replaceAll` rỗng.
26. Ký tự newline đa nền tảng (`\r\n` vs `\n`).
27. Đọc chuỗi từ File có BOM (Byte Order Mark).
28. Dùng `String.matches()` chỉ so khớp toàn bộ chuỗi.
29. Tràn bộ nhớ khi `intern()` một file từ điển khổng lồ.
30. Ghi đè phương thức bằng cách sử dụng String param trùng tên.

## 16. Optimization
- Khởi tạo trước dung lượng cho `StringBuilder` nếu biết trước: `new StringBuilder(100)`.
- Sử dụng `String.isBlank()` thay vì `trim().isEmpty()`.
- Tận dụng `Compact Strings` tự động của JVM 9+ (luôn kiểm tra file log tiết kiệm Heap).

## 17. Best Practices
- Ưu tiên sử dụng String literal thay vì `new String()`.
- Sử dụng hằng số (`public static final String`) cho các giá trị chuỗi sử dụng nhiều lần.
- Đặt Literal lên trước khi so sánh `equals()` (Yoda condition): `"expected".equals(actual)`.
- Khi log thông tin với SLF4J, dùng dấu `{}` thay vì ghép chuỗi `+` để tránh GC overhead khi cấu hình log level tắt.

## 18. Benchmark
So sánh hiệu suất nối 100,000 chuỗi:
- Toán tử `+`: Chậm (do tạo quá nhiều object).
- `StringBuilder`: Nhanh (chỉ thay đổi nội dung bên trong mảng byte/char).
- `StringBuffer`: Chậm hơn `StringBuilder` do có `synchronized`.

## 19. Unit Testing
```java
@Test
public void testStringEquality() {
    String a = "test";
    String b = "test";
    String c = new String("test");
    
    assertSame(a, b);
    assertNotSame(a, c);
    assertEquals(a, c);
}
```

## 20. Interview Questions
1. Tại sao String là immutable trong Java?
2. Sự khác biệt giữa Heap Memory và String Pool là gì?
3. Toán tử `==` và phương thức `.equals()` khác nhau thế nào khi so sánh String?
4. Khởi tạo `String s = new String("A")` tạo ra bao nhiêu đối tượng?
5. `String.intern()` hoạt động như thế nào?
6. Kể tên các tình huống rò rỉ bộ nhớ liên quan đến String ở Java 6 và cách khắc phục ở Java 7.
7. Giải thích khái niệm Compact Strings được giới thiệu ở Java 9.
8. Sự khác nhau giữa `StringBuffer` và `StringBuilder`?
9. Khi nào nên dùng toán tử `+` và khi nào nên dùng `StringBuilder`?
10. `String.valueOf()` và `Object.toString()` khác nhau thế nào khi tham số là null?
11. Tại sao mật khẩu nên được lưu dưới dạng `char[]` thay vì `String`?
12. Có thể tạo một subclass kế thừa class `String` không? Tại sao?
13. Hàm `hashCode` của String được tính toán và lưu trữ như thế nào?
14. Phân biệt `isEmpty()` và `isBlank()` trong String Java.
15. Giải thích Surrogate Pair và cách lấy độ dài thực tế của các ký tự hiển thị (Code points).
16. Làm thế nào để đảo ngược chuỗi hiệu quả trong Java?
17. Giải thích khái niệm "String Deduplication" trong Garbage Collector (G1 GC).
18. Toán tử `switch-case` hoạt động với String từ Java 7 như thế nào? (dùng hashCode và equals).
19. Việc nối chuỗi liên tục bằng toán tử `+` trong Java mới nhất (Java 9+) có còn chậm như các phiên bản cũ không?
20. Cách chuyển đổi qua lại an toàn giữa mảng byte và chuỗi.

## 21. Practice Problems Link
Xem file `01-String-Basics-and-Immutability-Problems.md` đính kèm.

## 22. Pattern Recognition
- Khi cần duyệt chuỗi qua các ký tự: dùng `toCharArray()` hoặc `charAt(i)` trong vòng lặp for.
- Khi cần tìm kiếm chuỗi con: dùng Two Pointers, KMP hoặc Rabin-Karp thuật toán.
- Kiểm tra tính Palindrome: Two Pointers từ 2 đầu mút `i`, `j`.
- Đếm ký tự (Anagram, Unique): Dùng `int[] count = new int[26]` hoặc `HashMap`.

## 23. Real Case Study
Trong hệ thống chat của ngân hàng ABC, ban đầu sử dụng `s = s + new_message` mỗi khi có tin nhắn mới, hệ thống bị OOM (Out Of Memory) trong giờ cao điểm. Giải pháp: Đổi toàn bộ hệ thống nối chuỗi sang `StringBuilder` và dọn dẹp các mảng byte không cần thiết, hiệu suất GC được cải thiện tới 40%.

## 24. Summary
String là cấu trúc cơ bản nhưng quan trọng nhất. Nắm vững tính Immutable và cách JVM xử lý String (Pool, Compact String, String Deduplication) là yêu cầu bắt buộc đối với kỹ sư Java.

## 25. Checklist
- [x] Hiểu về Immutability.
- [x] Nắm rõ hoạt động của String Pool.
- [x] Phân biệt `==` và `equals()`.
- [x] Sử dụng đúng `StringBuilder` vs `StringBuffer`.
- [x] Quản lý String memory leak and GC impacts.
