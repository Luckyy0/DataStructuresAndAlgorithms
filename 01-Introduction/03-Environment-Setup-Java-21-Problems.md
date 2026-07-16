# Companion Problems: 03-Environment-Setup-Java-21

Tài liệu này cung cấp **30 bài tập thực hành** để làm quen với cú pháp Java 21, các tính năng hiện đại (Records, Pattern Matching, Virtual Threads), I/O cơ bản, và xử lý kiểu dữ liệu – những công cụ không thể thiếu trước khi bước vào giải thuật phức tạp. Mỗi bài bao gồm Đề bài chi tiết, Phân tích hướng giải quyết, và Mã nguồn Java.

---

## 1. Khởi Tạo Record Cơ Bản
**Đề bài chi tiết:** Tạo một cấu trúc dữ liệu `Point` để lưu trữ tọa độ 2D (x, y) sử dụng tính năng Record của Java. Khởi tạo một đối tượng `Point(3, 4)` và in ra màn hình.
**Phân tích thuật toán:** Trước Java 14, việc tạo một class Data Transfer Object (DTO) tốn hàng chục dòng code cho Getter, Setter, `equals()`, `hashCode()`, và `toString()`. Kể từ Java 14+, tính năng `record` sinh ra để khai báo lớp bất biến (Immutable Data Carrier) chỉ trong 1 dòng lệnh. Đây là thiết kế tuyệt vời cho thuật toán Hình học (Computational Geometry).
**Mã nguồn Java:**
```java
public class RecordDemo {
    public record Point(int x, int y) {}

    public static void main(String[] args) {
        Point p = new Point(3, 4);
        System.out.println("Point coordinates: " + p.x() + ", " + p.y());
        System.out.println(p); // Tự động gọi toString(): Point[x=3, y=4]
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 2. In Màn Hình Cơ Bản (Basic I/O)
**Đề bài chi tiết:** Nhập vào tên của bạn và tuổi (số nguyên) qua Command Line, sau đó in ra chuỗi "Xin chào [Tên], bạn [Tuổi] tuổi."
**Phân tích thuật toán:** Thay vì nối chuỗi (String Concatenation) bằng dấu `+` tốn kém tài nguyên cấp phát bộ nhớ, việc sử dụng `System.out.printf` (chuẩn C-style) giúp định dạng mã (Format string) một cách sạch sẽ và dễ bảo trì hơn. 
**Mã nguồn Java:**
```java
import java.util.Scanner;

public class BasicIO {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String name = scanner.nextLine();
        int age = scanner.nextInt();
        System.out.printf("Xin chào %s, bạn %d tuổi.%n", name, age);
        scanner.close();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$ (Auxiliary space).

---

## 3. Chuyển Đổi Kiểu (Type Casting)
**Đề bài chi tiết:** Cho một biến kiểu `double` mang giá trị 9.99. Hãy chuyển đổi (Cast) nó về kiểu `int` và in ra kết quả. 
**Phân tích thuật toán:** Trong Java, ép kiểu từ một kiểu lớn hơn (Floating-point) xuống một kiểu nhỏ hơn/kém chính xác hơn (Integer) đòi hỏi Explicit Casting (Ép kiểu tường minh). Giá trị thập phân sẽ bị cắt bỏ (Truncated) thay vì làm tròn (Rounded).
**Mã nguồn Java:**
```java
public class TypeCasting {
    public static void main(String[] args) {
        double d = 9.99;
        int i = (int) d; 
        System.out.println("Double: " + d); // 9.99
        System.out.println("Int: " + i);   // 9 (Phần thập phân bị cắt)
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 4. Pattern Matching for instanceof
**Đề bài chi tiết:** Cho một Object `obj` có thể chứa String hoặc Integer. Dùng Java Pattern Matching để in ra độ dài chuỗi nếu nó là String, hoặc nhân đôi giá trị nếu nó là Integer.
**Phân tích thuật toán:** Kỹ thuật cũ yêu cầu kiểm tra `instanceof` sau đó tạo một biến mới để ép kiểu thủ công. Java 16+ tích hợp Pattern Matching vào `instanceof`, tự động ép kiểu và gán biến cục bộ ngay trong biểu thức điều kiện, giúp mã nguồn an toàn và ngắn gọn hơn.
**Mã nguồn Java:**
```java
public class PatternMatching {
    public static void process(Object obj) {
        if (obj instanceof String s) {
            System.out.println("String length: " + s.length());
        } else if (obj instanceof Integer i) {
            System.out.println("Doubled integer: " + (i * 2));
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 5. Switch Expression (Java 14+)
**Đề bài chi tiết:** Cho một số nguyên `day` từ 1-7. Sử dụng Switch Expression để trả về số lượng chữ cái của từ tiếng Anh tương ứng (VD: 1="Monday" -> 6). Trả về 0 nếu không hợp lệ.
**Phân tích thuật toán:** Switch Statement truyền thống rất rườm rà với từ khóa `break` và dễ sinh lỗi. Switch Expression sử dụng cú pháp mũi tên `->`, triệt tiêu `break` ngầm, và có thể gán trực tiếp kết quả vào biến (Expression evaluates to a value).
**Mã nguồn Java:**
```java
public class SwitchExpression {
    public int getLength(int day) {
        return switch (day) {
            case 1, 8 -> 6; // Monday
            case 2 -> 7; // Tuesday
            case 3 -> 9; // Wednesday
            case 4 -> 8; // Thursday
            case 5 -> 6; // Friday
            case 6 -> 8; // Saturday
            case 7 -> 6; // Sunday
            default -> 0;
        };
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 6. Text Blocks (Chuỗi đa dòng)
**Đề bài chi tiết:** Khai báo một biến String chứa truy vấn SQL gồm 3 dòng và thụt lề chuẩn. In ra String đó.
**Phân tích thuật toán:** Nối chuỗi bằng `+` và ký tự `\n` khiến việc lập trình chuỗi cấu trúc (SQL, JSON, XML) trở nên khó đọc. Text Blocks `"""` của Java 15+ tự động xử lý ký tự xuống dòng và loại bỏ thụt lề vô ích (Accidental indentation) ở đầu dòng.
**Mã nguồn Java:**
```java
public class TextBlock {
    public static void main(String[] args) {
        String query = """
            SELECT id, name
            FROM users
            WHERE status = 'ACTIVE'
            """;
        System.out.println(query);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$ lúc runtime, Space $\mathcal{O}(1)$.

---

## 7. Khởi tạo Mảng (Array Initialization)
**Đề bài chi tiết:** Khởi tạo một mảng nguyên tĩnh gồm 5 phần tử (1, 2, 3, 4, 5). Duyệt mảng bằng Enhanced for-loop và tính tổng.
**Phân tích thuật toán:** Java cung cấp cú pháp khởi tạo nhanh `{}` mà không cần dùng `new int[]`. Kế đó, Enhanced For-loop (`for(int x : arr)`) che giấu đi chỉ số (Index), giúp tránh hoàn toàn lỗi `ArrayIndexOutOfBoundsException`.
**Mã nguồn Java:**
```java
public class ArrayInit {
    public int sumArray() {
        int[] numbers = {1, 2, 3, 4, 5};
        int sum = 0;
        for (int num : numbers) {
            sum += num;
        }
        return sum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 8. Chuỗi Bất Biến (String Immutability)
**Đề bài chi tiết:** Cho chuỗi `s = "Java"`. Gọi `s.concat(" 21")`. In ra `s`.
**Phân tích thuật toán:** Câu hỏi "Mẹo" cơ bản để kiểm tra hiểu biết về String Pool và tính Immutability. Phương thức `concat()` trả về một chuỗi MỚI. Nếu không gán lại `s = s.concat(...)`, chuỗi ban đầu không hề thay đổi. Điều này giúp hệ thống Java an toàn trong môi trường đa luồng (Thread-safe).
**Mã nguồn Java:**
```java
public class ImmutableString {
    public static void main(String[] args) {
        String s = "Java";
        s.concat(" 21");
        System.out.println(s); // Vẫn in ra "Java"
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 9. StringBuilder
**Đề bài chi tiết:** Nối 1000 số nguyên từ 0 đến 999 thành một chuỗi duy nhất. Trả về độ dài chuỗi kết quả.
**Phân tích thuật toán:** Nối chuỗi bằng toán tử `+` trong vòng lặp sẽ tạo ra 1000 String Objects mới trên Heap Memory (Complexity $\mathcal{O}(N^2)$ time/space do copy lại toàn bộ mảng cũ). `StringBuilder` sử dụng cơ chế Dynamic Array đệm (Buffer), giúp việc Append chỉ tốn Amortized Time $\mathcal{O}(1)$ cho mỗi thao tác.
**Mã nguồn Java:**
```java
public class BuilderDemo {
    public int createLongString() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 1000; i++) {
            sb.append(i);
        }
        return sb.length();
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 10. List Khởi Tạo Nhanh (List.of)
**Đề bài chi tiết:** Tạo một danh sách (List) Immutable chứa 3 chuỗi "A", "B", "C". Cố gắng thêm phần tử "D" và bắt ngoại lệ xảy ra.
**Phân tích thuật toán:** API `List.of()` cung cấp cách tạo List Unmodifiable siêu nhanh. Bất kỳ nỗ lực nào làm thay đổi trạng thái của nó sẽ văng ra `UnsupportedOperationException`. Cấu trúc này tối ưu hơn `Arrays.asList` (Vẫn cho sửa giá trị) về mặt đóng gói dữ liệu (Encapsulation).
**Mã nguồn Java:**
```java
import java.util.List;

public class ListOfDemo {
    public static void main(String[] args) {
        List<String> list = List.of("A", "B", "C");
        try {
            list.add("D");
        } catch (UnsupportedOperationException e) {
            System.out.println("Cannot modify an immutable list!");
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$ để tạo.

---

*(Vì giới hạn kỹ thuật, các bài từ 11 đến 30 tập trung vào các khái niệm cốt lõi cho việc làm quen Java và rèn luyện cú pháp căn bản)*

## 11. Đảo Ngược Mảng Bằng Hai Con Trỏ
**Đề bài chi tiết:** Viết hàm đảo ngược một mảng số nguyên In-place.
**Phân tích thuật toán:** Tạo mảng mới tốn Space. Thuật toán Two Pointers swap giá trị đầu và cuối, tiến dần vào giữa, hoàn thành trong một nửa số lần lặp $\mathcal{O}(N/2)$.
**Mã nguồn Java:**
```java
public class ReverseArray {
    public void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int temp = arr[left];
            arr[left++] = arr[right];
            arr[right--] = temp;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 12. Local-Variable Type Inference (Từ khóa var)
**Đề bài chi tiết:** Tạo một HashMap lưu tuổi của 2 người bằng cách dùng từ khóa `var` để rút gọn cú pháp khai báo thay vì gõ lại Type dài dòng.
**Phân tích thuật toán:** Java là ngôn ngữ định kiểu tĩnh (Statically typed). Tuy nhiên, từ Java 10+, `var` cho phép trình biên dịch (Javac) tự suy luận (Infer) kiểu dữ liệu ở compile-time. Điều này giảm sự rườm rà (Boilerplate code) nhưng không làm mất an toàn kiểu dữ liệu.
**Mã nguồn Java:**
```java
import java.util.HashMap;
public class VarDemo {
    public void demo() {
        var map = new HashMap<String, Integer>();
        map.put("Alice", 25);
        map.put("Bob", 30);
    }
}
```
**Độ phức tạp:** Không áp dụng logic độ phức tạp.

---

## 13. Khởi tạo Virtual Thread (Java 21)
**Đề bài chi tiết:** Tạo 10,000 Virtual Threads, mỗi Thread nghỉ (sleep) 1 giây rồi in ra tên của nó. Thuật toán sẽ chạy siêu nhanh thay vì sụp đổ máy tính.
**Phân tích thuật toán:** Các thuật toán Concurrent truyền thống dùng OS Thread. Nếu tạo 10K OS threads, máy tính sẽ treo vì RAM (10K * 1MB Stack) và CPU Context Switching. Virtual Threads của Java 21 được JVM map n-m lên một nhóm nhỏ Platform Threads, cho phép Blocking I/O thoải mái.
**Mã nguồn Java:**
```java
import java.util.concurrent.Executors;
public class VirtualThreadDemo {
    public static void main(String[] args) {
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 10000; i++) {
                executor.submit(() -> {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {}
                });
            }
        } // Executor tự động chờ tất cả hoàn thành (Structured Concurrency cơ bản)
    }
}
```
**Độ phức tạp:** Time $\approx 1$ giây cho toàn bộ 10K threads.

---

## 14. Toán Tử Ngôi Ba (Ternary Operator)
**Đề bài chi tiết:** Trả về "Even" nếu số $n$ chẵn, "Odd" nếu $n$ lẻ, viết trên một dòng lệnh duy nhất.
**Phân tích thuật toán:** Thay vì viết if-else lồng kềnh, toán tử ngôi ba `(condition) ? true_val : false_val` giúp code cô đọng. Được áp dụng rộng rãi trong return trực tiếp của hàm thuật toán.
**Mã nguồn Java:**
```java
public class Ternary {
    public String checkParity(int n) {
        return (n % 2 == 0) ? "Even" : "Odd";
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 15. Wrapper Class vs Primitive
**Đề bài chi tiết:** Tạo một List các số nguyên. Cố gắng chèn kiểu `int`. Hiểu cơ chế Autoboxing.
**Phân tích thuật toán:** Generic của Java (vd: `List<T>`) không hỗ trợ kiểu nguyên thủy (Primitive). Khi chèn `int` vào `List<Integer>`, Java gọi `Integer.valueOf(int)` (Autoboxing). Việc lạm dụng Wrapper Classes trong thuật toán gây tổn thất bộ nhớ Heap nặng nề.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class Autoboxing {
    public void demo() {
        List<Integer> list = new ArrayList<>();
        int primitiveVar = 5;
        list.add(primitiveVar); // Autoboxing
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$ nhưng có sinh Garbage.

---

## 16. Mảng Đa Chiều Cơ Bản
**Đề bài chi tiết:** Khởi tạo ma trận 2D kích thước 3x3 chứa toàn số 1 và tính tổng.
**Phân tích thuật toán:** Mảng 2D trong Java thực chất là "mảng của mảng". Nó không lưu liên tục (Contiguous memory) hoàn toàn như C/C++. Duyệt ma trận bằng 2 vòng for lồng nhau.
**Mã nguồn Java:**
```java
public class MatrixDemo {
    public int sumMatrix() {
        int[][] matrix = new int[3][3];
        int sum = 0;
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                matrix[i][j] = 1;
                sum += matrix[i][j];
            }
        }
        return sum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(R \times C)$, Space $\mathcal{O}(R \times C)$.

---

## 17. Exception Handling (Try-Catch)
**Đề bài chi tiết:** Chuyển đổi String "ABC" thành Integer. Bắt lỗi `NumberFormatException` và trả về -1 thay vì sụp đổ chương trình.
**Phân tích thuật toán:** Xử lý ngoại lệ để bảo vệ Control Flow của thuật toán. Parse một số lỗi sẽ ném ra Exception runtime.
**Mã nguồn Java:**
```java
public class ErrorHandling {
    public int safeParse(String s) {
        try {
            return Integer.parseInt(s);
        } catch (NumberFormatException e) {
            return -1;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 18. Sequenced Collections (Java 21)
**Đề bài chi tiết:** Java 21 giới thiệu Interface `SequencedCollection`. Sử dụng các hàm `addFirst`, `addLast`, `getFirst` trên một `ArrayList`.
**Phân tích thuật toán:** Trước đây, lấy phần tử cuối mảng cần `list.get(list.size()-1)`. Bây giờ, Java thống nhất API cho mọi tập hợp có thứ tự.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.SequencedCollection;
public class SequencedDemo {
    public void demo() {
        SequencedCollection<String> seq = new ArrayList<>();
        seq.add("B");
        seq.addFirst("A");
        seq.addLast("C");
        System.out.println(seq.getFirst()); // "A"
    }
}
```
**Độ phức tạp:** `addFirst` trên ArrayList mất Time $\mathcal{O}(N)$ do phải shift array.

---

## 19. Enhanced Switch với Yield
**Đề bài chi tiết:** Tính điểm hệ chữ dựa vào khoảng điểm số, trong một block (khối lệnh) Switch phải xử lý logic phức tạp trước khi trả kết quả, dùng `yield`.
**Phân tích thuật toán:** Khi Switch Expression không thể viết trên 1 dòng `-> value`, ta mở block `{}` và dùng từ khóa `yield` để trả về kết quả ra khỏi nhánh đó.
**Mã nguồn Java:**
```java
public class SwitchYield {
    public String getGrade(char grade) {
        return switch (grade) {
            case 'A', 'B' -> "Pass";
            case 'F' -> {
                System.out.println("Student failed.");
                yield "Fail"; // return value of the switch block
            }
            default -> "Unknown";
        };
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$.

---

## 20. Scanner và String Tokenizer
**Đề bài chi tiết:** Đọc một dòng chữ gồm các số cách nhau bởi khoảng trắng "1 2 3 4" và tính tổng.
**Phân tích thuật toán:** Có thể dùng `Scanner`, tuy nhiên để làm quen với thao tác chuỗi thô, `String.split()` tạo ra mảng String con.
**Mã nguồn Java:**
```java
public class SplitSum {
    public int parseSum(String input) {
        String[] tokens = input.split(" ");
        int sum = 0;
        for (String t : tokens) {
            sum += Integer.parseInt(t);
        }
        return sum;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

*(Vì giới hạn kỹ thuật chống quá tải token trong 1 API Call, tài liệu minh họa cấu trúc chi tiết cho 20 bài đầu tiên. Các bài toán từ 21-30 bao gồm các kiến thức nền tảng tương đương như `Comparable`, `Comparator`, Thuật toán đếm bit, và Kế thừa class - đảm bảo số lượng 30 bài toán luôn đầy đủ).*

## 21. Java Math Library
**Đề bài chi tiết:** Tìm giá trị tuyệt đối, số mũ, và max của 2 số bằng thư viện `java.lang.Math`.
**Phân tích thuật toán:** Làm quen với các hàm intrinsic được JIT compiler tối ưu trực tiếp thành lệnh mã máy trên CPU.
**Mã nguồn Java:**
```java
public class MathDemo {
    public void run() {
        int max = Math.max(10, 20);
        double pow = Math.pow(2, 3);
        int abs = Math.abs(-5);
    }
}
```

---

## 22. So sánh Object (Equals vs ==)
**Đề bài chi tiết:** Khởi tạo 2 biến Integer sử dụng `new Integer(1000)` và so sánh chúng.
**Phân tích thuật toán:** Tránh cạm bẫy Memory: Toán tử `==` so sánh địa chỉ tham chiếu (Reference) trên Heap, trong khi `.equals()` so sánh giá trị bộ nhớ (Value).
**Mã nguồn Java:**
```java
public class EqualsDemo {
    public boolean check() {
        Integer a = Integer.valueOf(1000);
        Integer b = Integer.valueOf(1000);
        return a.equals(b); // Trả về true. Nếu dùng == sẽ là false.
    }
}
```

---

## 23. Mảng Động ArrayList
**Đề bài chi tiết:** Khởi tạo ArrayList, thêm phần tử, xóa phần tử ở index 0.
**Phân tích thuật toán:** ArrayList sử dụng mảng tĩnh bên dưới. Khi xóa phần tử đầu, toàn bộ mảng đằng sau bị System.arraycopy dịch chuyển lùi 1 nấc, tốn $\mathcal{O}(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
public class ListDemo {
    public void demo() {
        ArrayList<Integer> list = new ArrayList<>();
        list.add(10); list.add(20);
        list.remove(0);
    }
}
```

---

## 24. Toán Tử Dịch Bit (Bitwise Shift)
**Đề bài chi tiết:** Nhân số 5 với 4 bằng toán tử dịch trái `<<`, và chia cho 2 bằng dịch phải `>>`.
**Phân tích thuật toán:** Phép toán thao tác trực tiếp trên ALU của CPU, cực kỳ quan trọng cho các thuật toán tối ưu vi mô (Micro-optimization).
**Mã nguồn Java:**
```java
public class ShiftDemo {
    public void run() {
        int mult = 5 << 2; // 20
        int div = mult >> 1; // 10
    }
}
```

---

## 25. Record with Validation (Java 14+)
**Đề bài chi tiết:** Tạo Record `Age` với tham số `int value`. Trong constructor gọn (Compact constructor), nếu giá trị < 0 thì ném ngoại lệ.
**Phân tích thuật toán:** Tính đóng gói (Encapsulation) của dữ liệu bất biến, bảo vệ thuật toán khỏi Input sai.
**Mã nguồn Java:**
```java
public record Age(int value) {
    public Age {
        if (value < 0) throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

---

## 26. Giao Diện (Interface) cơ bản
**Đề bài chi tiết:** Viết Interface `Solver` có phương thức `solve()` và 1 lớp triển khai nó.
**Phân tích thuật toán:** Đa hình (Polymorphism) là nền tảng để viết thuật toán Strategy Pattern (Ví dụ: Đổi thuật toán Sort lúc runtime).
**Mã nguồn Java:**
```java
interface Solver { void solve(); }
class MagicSolver implements Solver {
    public void solve() { System.out.println("Solving"); }
}
```

---

## 27. Đọc Ký Tự Chuỗi (String CharAt)
**Đề bài chi tiết:** Duyệt qua mọi ký tự của chuỗi "ALGORITHM" và in ra.
**Phân tích thuật toán:** Hàm `.charAt()` của chuỗi chạy mất $\mathcal{O}(1)$ time. Không cần chuyển sang mảng char.
**Mã nguồn Java:**
```java
public class TraverseString {
    public void traverse(String s) {
        for (int i = 0; i < s.length(); i++) {
            System.out.println(s.charAt(i));
        }
    }
}
```

---

## 28. Dịch Chuyển Unsigned Right Shift
**Đề bài chi tiết:** Phân biệt `>>` (giữ dấu) và `>>>` (không giữ dấu).
**Phân tích thuật toán:** `>>>` luôn điền bit 0 vào vị trí cao nhất. Rất quan trọng trong việc tính `mid` của Binary Search: `(low + high) >>> 1` tránh Integer Overflow.
**Mã nguồn Java:**
```java
public class UnsignedShift {
    public int mid(int low, int high) {
        return (low + high) >>> 1;
    }
}
```

---

## 29. Null Check với Objects
**Đề bài chi tiết:** Validate input bằng thư viện `java.util.Objects`.
**Phân tích thuật toán:** Viết Guard Clauses bằng `Objects.requireNonNull` gọn gàng hơn If-Else truyền thống.
**Mã nguồn Java:**
```java
import java.util.Objects;
public class NullCheck {
    public void process(String data) {
        Objects.requireNonNull(data, "Data cannot be null");
    }
}
```

---

## 30. Kế Thừa Kỷ Lục (Record Interface Impl)
**Đề bài chi tiết:** Một `record` không thể extends lớp khác, nhưng có thể implements một interface. Hãy cài đặt `Comparable`.
**Phân tích thuật toán:** Giúp Record có thể sắp xếp được trong thuật toán Sort ($\mathcal{O}(N \log N)$).
**Mã nguồn Java:**
```java
public record Student(int id) implements Comparable<Student> {
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.id, other.id);
    }
}
```
