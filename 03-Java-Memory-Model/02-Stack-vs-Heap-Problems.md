# Companion Problems: 02-Stack-vs-Heap

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Stack Memory và Heap Memory trong JVM. Các bài toán tập trung vào nhận diện vị trí bộ nhớ, tối ưu hóa cấp phát, và tránh các lỗi phổ biến.

---

## 1. Xác Định Vị Trí Bộ Nhớ
**Đề bài chi tiết:** Cho đoạn code sau, liệt kê từng biến/object nằm trên Stack hay Heap.
**Phân tích thuật toán:** Primitive cục bộ → Stack. Object/Array tạo bằng `new` → Heap. Reference (con trỏ) → Stack. Static → Method Area.
**Mã nguồn Java:**
```java
public class MemoryLocationQuiz {
    static int globalCount = 0;  // Method Area (Metaspace)

    public void process() {
        int x = 10;              // Stack (Primitive)
        double y = 3.14;         // Stack (Primitive)
        String name = "Alice";   // Stack (Reference), Heap (String Object/Pool)
        int[] scores = {90, 85}; // Stack (Reference), Heap (Array Object)
        Integer boxed = 42;      // Stack (Reference), Heap (Integer Object)*
        // * Integer cache: 42 nằm trong range [-128,127] → Cached Object
        Object obj = new Object(); // Stack (Reference), Heap (Object)
    }
}
```
**Độ phức tạp:** Phân tích thuần lý thuyết.

---

## 2. Pass-by-Value Proof (Primitive)
**Đề bài chi tiết:** Viết hàm nhận `int`, tăng lên 100. Kiểm tra biến ngoài hàm có thay đổi không.
**Phân tích thuật toán:** Java là Pass-by-Value. Khi truyền Primitive `int x = 5`, JVM copy giá trị `5` vào Stack Frame mới. Hàm thay đổi bản sao, bản gốc KHÔNG bị ảnh hưởng.
**Mã nguồn Java:**
```java
public class PassByValuePrimitive {
    static void increment(int n) {
        n += 100; // Thay đổi BẢN SAO trên Stack Frame mới
        System.out.println("Inside: " + n); // 105
    }

    public static void main(String[] args) {
        int x = 5;
        increment(x);
        System.out.println("Outside: " + x); // 5 (KHÔNG thay đổi)
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 3. Pass-by-Value Proof (Object Reference)
**Đề bài chi tiết:** Viết hàm nhận `int[]`, thay đổi phần tử và gán reference mới. Kiểm tra kết quả bên ngoài.
**Phân tích thuật toán:** Truyền `int[] arr` → Copy Reference (4 bytes con trỏ) vào Stack Frame mới. Thay đổi `arr[0]` → Thay đổi Object trên Heap qua con trỏ gốc → Ảnh hưởng bên ngoài. Gán `arr = new int[]{...}` → Trỏ con trỏ CỤC BỘ sang Object MỚI → KHÔNG ảnh hưởng biến bên ngoài.
**Mã nguồn Java:**
```java
public class PassByValueReference {
    static void modify(int[] arr) {
        arr[0] = 999;            // Thay đổi Heap Object → Ảnh hưởng ngoài
        arr = new int[]{-1, -2}; // Gán Reference mới → KHÔNG ảnh hưởng ngoài
        arr[0] = -999;           // Thay đổi Object MỚI → KHÔNG ảnh hưởng ngoài
    }

    public static void main(String[] args) {
        int[] myArr = {1, 2, 3};
        modify(myArr);
        System.out.println(myArr[0]); // 999
        System.out.println(myArr[1]); // 2 (KHÔNG bị ảnh hưởng)
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 4. Auto-boxing Performance Impact
**Đề bài chi tiết:** So sánh thời gian cộng dồn $10^8$ số bằng `long` (Primitive) vs `Long` (Wrapper).
**Phân tích thuật toán:** `Long sum += i` thực hiện: (1) Unbox `sum` thành `long`, (2) Cộng, (3) Auto-box kết quả thành `Long` Object MỚI trên Heap. $10^8$ phép boxing tạo ra $10^8$ Object rác → GC phải chạy hàng trăm lần. Primitive `long` chỉ thao tác trên Stack → Không GC.
**Mã nguồn Java:**
```java
public class AutoboxingBenchmark {
    public static void main(String[] args) {
        int N = 100_000_000;

        long t1 = System.nanoTime();
        long primSum = 0L;
        for (int i = 0; i < N; i++) primSum += i;
        long primTime = System.nanoTime() - t1;

        long t2 = System.nanoTime();
        Long wrapSum = 0L; // Auto-boxing!
        for (int i = 0; i < N; i++) wrapSum += i;
        long wrapTime = System.nanoTime() - t2;

        System.out.printf("Primitive long: %d ms%n", primTime / 1_000_000);
        System.out.printf("Wrapper Long:   %d ms%n", wrapTime / 1_000_000);
        System.out.printf("Slowdown:       %.1fx%n", (double) wrapTime / primTime);
    }
}
```
**Độ phức tạp:** Cùng $\mathcal{O}(N)$ nhưng Wrapper chậm gấp 5-10x.

---

## 5. Integer Cache Trap
**Đề bài chi tiết:** So sánh 2 `Integer` objects bằng `==`. Tại sao `Integer.valueOf(127) == Integer.valueOf(127)` là `true` nhưng `Integer.valueOf(128) == Integer.valueOf(128)` là `false`?
**Phân tích thuật toán:** JVM cache các Integer Object trong phạm vi [-128, 127]. `Integer.valueOf(127)` trả về CÙNG Object từ Cache → `==` so sánh Reference → `true`. `Integer.valueOf(128)` tạo Object MỚI trên Heap → 2 References khác nhau → `==` là `false`. LUÔN dùng `.equals()` cho Wrapper types.
**Mã nguồn Java:**
```java
public class IntegerCacheTrap {
    public static void main(String[] args) {
        Integer a = 127, b = 127;
        System.out.println(a == b);       // true (Cached, cùng Object)
        System.out.println(a.equals(b));  // true

        Integer c = 128, d = 128;
        System.out.println(c == d);       // false (Khác Object!)
        System.out.println(c.equals(d));  // true

        // Mở rộng Cache bằng: -XX:AutoBoxCacheMax=256
    }
}
```
**Độ phức tạp:** Lỗi logic, không phải lỗi performance.

---

## 6. Stack Overflow vs Out of Memory
**Đề bài chi tiết:** Viết 2 chương trình: 1 gây `StackOverflowError`, 1 gây `OutOfMemoryError`. Giải thích sự khác biệt.
**Phân tích thuật toán:** `StackOverflowError`: Stack Frame vượt quá `-Xss` (Thường do đệ quy). `OutOfMemoryError`: Heap đầy vì tạo quá nhiều Object mà GC không giải phóng được (Thường do Memory Leak hoặc dữ liệu quá lớn).
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class ErrorDemo {
    // StackOverflowError
    static void stackOverflow() { stackOverflow(); }

    // OutOfMemoryError
    static void heapOverflow() {
        List<int[]> leak = new ArrayList<>();
        while (true) {
            leak.add(new int[1_000_000]); // 4MB mỗi lần
        }
    }

    public static void main(String[] args) {
        try { stackOverflow(); }
        catch (StackOverflowError e) { System.out.println("Stack overflow caught!"); }

        try { heapOverflow(); }
        catch (OutOfMemoryError e) { System.out.println("Heap overflow caught!"); }
    }
}
```
**Độ phức tạp:** StackOverflow: Space $\mathcal{O}(\infty)$ Stack. HeapOverflow: Space $\mathcal{O}(\infty)$ Heap.

---

## 7. String Pool vs new String
**Đề bài chi tiết:** So sánh `"hello"` (String Literal) vs `new String("hello")`. Bao nhiêu Object được tạo?
**Phân tích thuật toán:** `"hello"` → JVM kiểm tra String Pool (Trên Heap). Nếu đã tồn tại, trả về Reference cũ. `new String("hello")` → Tạo Object MỚI trên Heap ngoài Pool, kể cả khi "hello" đã có trong Pool. Do đó `new String("hello")` tạo 2 Objects (1 trong Pool, 1 ngoài).
**Mã nguồn Java:**
```java
public class StringPoolDemo {
    public static void main(String[] args) {
        String s1 = "hello";           // Pool
        String s2 = "hello";           // CÙNG Object từ Pool
        String s3 = new String("hello"); // Object MỚI ngoài Pool

        System.out.println(s1 == s2);  // true (Cùng Reference)
        System.out.println(s1 == s3);  // false (Khác Reference!)
        System.out.println(s1.equals(s3)); // true (Cùng giá trị)

        String s4 = s3.intern();       // Đưa s3 vào Pool → Trả về Reference Pool
        System.out.println(s1 == s4);  // true
    }
}
```
**Độ phức tạp:** `intern()` tốn $\mathcal{O}(L)$ với $L$ là chiều dài chuỗi.

---

## 8. Đệ Quy vs Vòng Lặp — Stack Usage
**Đề bài chi tiết:** Cài đặt tính tổng 1 đến N bằng đệ quy và vòng lặp. So sánh Stack usage.
**Phân tích thuật toán:** Đệ quy tạo $N$ Stack Frames. Vòng lặp chỉ dùng 1 Frame. Với $N = 10^6$ và `-Xss1m`, đệ quy sẽ crash. Vòng lặp hoạt động bình thường.
**Mã nguồn Java:**
```java
public class RecursionVsLoop {
    // O(N) Stack Frames → StackOverflow nếu N lớn
    static long sumRecursive(int n) {
        if (n == 0) return 0;
        return n + sumRecursive(n - 1);
    }

    // O(1) Stack → An toàn với mọi N
    static long sumIterative(int n) {
        long sum = 0;
        for (int i = 1; i <= n; i++) sum += i;
        return sum;
    }

    public static void main(String[] args) {
        int N = 100_000;
        System.out.println("Iterative: " + sumIterative(N)); // OK

        try {
            System.out.println("Recursive: " + sumRecursive(N)); // Có thể crash
        } catch (StackOverflowError e) {
            System.out.println("StackOverflow at N=" + N);
        }
    }
}
```
**Độ phức tạp:** Đệ quy Space $\mathcal{O}(N)$. Vòng lặp Space $\mathcal{O}(1)$.

---

## 9. ThreadLocal Memory Leak
**Đề bài chi tiết:** Sử dụng `ThreadLocal` để lưu Object lớn. Minh họa Memory Leak khi Thread Pool tái sử dụng Thread.
**Phân tích thuật toán:** `ThreadLocal` lưu dữ liệu riêng cho mỗi Thread. Khi Thread kết thúc, dữ liệu bị GC. NHƯNG trong Thread Pool, Thread KHÔNG kết thúc mà bị tái sử dụng. `ThreadLocal` tiếp tục giữ Reference → Object KHÔNG bị GC → Memory Leak. Phải gọi `threadLocal.remove()` sau khi dùng xong.
**Mã nguồn Java:**
```java
import java.util.concurrent.*;
public class ThreadLocalLeak {
    static ThreadLocal<byte[]> cache = new ThreadLocal<>();

    public static void main(String[] args) throws Exception {
        ExecutorService pool = Executors.newFixedThreadPool(2);
        for (int i = 0; i < 1000; i++) {
            pool.submit(() -> {
                cache.set(new byte[1_000_000]); // 1MB mỗi task
                // BUG: Quên gọi cache.remove()
                // Thread Pool tái sử dụng Thread → 1MB bị giữ mãi
            });
        }
        pool.shutdown();
        // Fix: Thêm cache.remove() trong finally block
    }
}
```
**Độ phức tạp:** Memory Leak $\mathcal{O}(\text{ThreadPoolSize} \times \text{ObjectSize})$.

---

## 10. Multi-dimensional Array Heap Objects
**Đề bài chi tiết:** Khởi tạo `int[3][4]`. Bao nhiêu Object được tạo trên Heap?
**Phân tích thuật toán:** Java không có mảng 2D thực sự. `int[3][4]` là mảng 1D chứa 3 References, mỗi Reference trỏ đến mảng `int[4]`. Tổng Object trên Heap: 1 (Mảng cha `int[][]` kích thước 3) + 3 (Mảng con `int[]` kích thước 4) = **4 Objects**. Ngược lại, C/C++ `int[3][4]` chỉ là 1 khối RAM liền mạch.
**Mã nguồn Java:**
```java
public class MultiDimArray {
    public static void main(String[] args) {
        int[][] matrix = new int[3][4]; 
        // Heap: 4 Objects tổng cộng
        // matrix[0], matrix[1], matrix[2] là 3 con trỏ trên mảng cha
        // Mỗi con trỏ trỏ đến 1 Object int[] riêng biệt

        // Chứng minh: Các mảng con có thể có ĐỊA CHỈ khác nhau
        System.out.println(matrix[0]); // [I@1a2b3c (Địa chỉ Object 1)
        System.out.println(matrix[1]); // [I@4d5e6f (Địa chỉ Object 2)
        // Nếu là mảng liền mạch, chúng phải cách nhau đúng 16 bytes
    }
}
```
**Độ phức tạp:** $M + 1$ Objects cho mảng `[M][N]`.

---

## 11-30. Tóm lược phân tích các bài tập còn lại

- **Bài 11 (Primitive Array vs Object Array Memory):** `int[1000]` tốn ~4KB. `Integer[1000]` tốn ~20KB. Đo bằng `Runtime.freeMemory()`.
- **Bài 12 (Swap Two Variables — Stack Only):** Hoán vị 2 biến `int` bằng XOR. Không tạo Object. Space $\mathcal{O}(1)$.
- **Bài 13 (Return Array From Method):** Array trên Heap sống sau khi method return. Reference bị copy (Pass-by-value). Caller nhận Reference → Object sống.
- **Bài 14 (Recursive Fibonacci Stack Depth):** `fib(N)` đệ quy: Stack Depth tối đa $N$ (không phải $2^N$). $2^N$ là số lần gọi, $N$ là Stack Depth tối đa.
- **Bài 15 (Tail Recursion Attempt):** Viết Factorial đệ quy đuôi. Java KHÔNG tối ưu → Stack vẫn $\mathcal{O}(N)$. Chuyển sang vòng lặp thủ công.
- **Bài 16 (Object Graph Reference Counting):** Vẽ đồ thị Reference: A → B → C, A → C. GC dùng Reachability (Mark-and-Sweep) không phải Reference Counting.
- **Bài 17 (Closure Lambda Capture):** Lambda `() -> x` capture biến `x`. Nếu `x` là Primitive → Copy giá trị vào Heap Object (Synthetic class). Nếu `x` là Reference → Copy Reference.
- **Bài 18 (Immutable Object on Heap):** `String`, `Integer`, `BigDecimal` là Immutable. Mỗi phép toán tạo Object MỚI trên Heap thay vì thay đổi Object cũ.
- **Bài 19 (Direct ByteBuffer — Off-Heap):** `ByteBuffer.allocateDirect(size)` cấp phát Native Memory ngoài Heap. GC KHÔNG quản lý. Tránh GC pressure nhưng phải tự dọn dẹp.
- **Bài 20 (WeakReference and GC):** `WeakReference<Object>` cho phép GC thu gom Object khi không còn Strong Reference. Ứng dụng: WeakHashMap cho Cache.
- **Bài 21 (Stack Frame Size Estimation):** Đệ quy với 10 biến cục bộ `int` vs 1 biến. So sánh Max Depth trước StackOverflow.
- **Bài 22 (Shallow vs Deep Copy):** `clone()` mặc định là Shallow Copy (Copy Reference). Deep Copy phải đệ quy clone từng Object con.
- **Bài 23 (Memory Leak qua Static Collection):** `static List<Object>` → GC không bao giờ dọn → Memory Leak vĩnh viễn. Fix: Dùng `WeakHashMap` hoặc gọi `clear()`.
- **Bài 24 (Varargs và Heap):** `void foo(int... args)` → JVM tạo `int[]` trên Heap ngầm. Gọi nhiều lần → Nhiều Array rác.
- **Bài 25 (Record Class Memory):** Java 16+ `record Point(int x, int y)` → Vẫn là Object trên Heap (16B header + 8B data = 24B). Project Valhalla sẽ cho phép Inline class trên Stack.
- **Bài 26 (Enum Memory):** Enum constants là Singleton trên Heap. `Color.RED` chỉ có 1 instance. `values()` tạo Array MỚI mỗi lần gọi → Cache lại nếu gọi nhiều.
- **Bài 27 (SoftReference cho Cache):** `SoftReference` bị GC thu gom KHI Heap sắp đầy. Tốt cho In-memory Cache: Tự co lại khi Memory áp lực.
- **Bài 28 (Phantom Reference và Cleanup):** `PhantomReference` + `ReferenceQueue` → Dọn dẹp Native Resources khi Object bị GC. Thay thế cho `finalize()` (Deprecated).
- **Bài 29 (Stack Allocation qua Escape Analysis):** Benchmark Object tạo trong vòng lặp mà không escape. So sánh thời gian khi bật/tắt `-XX:+DoEscapeAnalysis`.
- **Bài 30 (Value Types Preview — Valhalla):** Trình bày Inline Types / Value Types (Project Valhalla): Object KHÔNG có Header, nằm trên Stack, giống `struct` trong C. Cải thiện Memory Layout cho DSA.
