# Companion Problems: 01-JVM-Architecture

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về JVM Architecture, bao gồm phân tích Bytecode, quản lý bộ nhớ Runtime, JIT Compilation, và Class Loading.

---

## 1. Phân Tích Bytecode Của Phép Cộng
**Đề bài chi tiết:** Viết một hàm Java đơn giản cộng 2 số nguyên. Dùng `javap -c` để xem bytecode. Giải thích từng Opcode.
**Phân tích thuật toán:** JVM là kiến trúc Stack-based. Phép `a + b` được biên dịch thành chuỗi: Push `a` lên Operand Stack (`iload_1`), Push `b` (`iload_2`), Pop 2 giá trị và cộng (`iadd`), Push kết quả, Pop và lưu vào biến cục bộ (`istore_3`). Mỗi Opcode tốn 1 byte.
**Mã nguồn Java:**
```java
public class BytecodeAdd {
    public int add(int a, int b) {
        int c = a + b;
        return c;
    }
    // javap -c BytecodeAdd.class
    // 0: iload_1
    // 1: iload_2
    // 2: iadd
    // 3: istore_3
    // 4: iload_3
    // 5: ireturn
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 2. Stack Frame Analysis — Đệ Quy
**Đề bài chi tiết:** Viết hàm đệ quy tính giai thừa. Vẽ trạng thái Java Stack (Các Stack Frame lồng nhau) tại thời điểm `factorial(4)` chạm Base case.
**Phân tích thuật toán:** Khi `factorial(4)` được gọi, JVM tạo Stack Frame chứa `n=4`. Gọi tiếp `factorial(3)` tạo Frame mới chồng lên. Tiếp tục cho đến `factorial(0)`. Tại thời điểm đó, Java Stack có 5 Frames. Khi `factorial(0)` return, Frame bị Pop. JVM giải phóng dần Stack Frames từ trên xuống.
**Mã nguồn Java:**
```java
public class StackFrameDemo {
    public int factorial(int n) {
        if (n == 0) return 1;
        return n * factorial(n - 1);
    }
    // Stack at n=0:
    // | factorial(0) | n=0, return 1       | ← Top (PC here)
    // | factorial(1) | n=1, waiting         |
    // | factorial(2) | n=2, waiting         |
    // | factorial(3) | n=3, waiting         |
    // | factorial(4) | n=4, waiting         |
    // | main()       | called factorial(4)  | ← Bottom
}
```
**Độ phức tạp:** Space $\mathcal{O}(N)$ cho Call Stack.

---

## 3. Heap vs Stack — Object Reference
**Đề bài chi tiết:** Tạo một Object `Point` trong hàm. Trả về Reference. Phân tích: Object nằm ở đâu? Reference nằm ở đâu? Khi nào Object bị GC?
**Phân tích thuật toán:** Biến `p` (Reference) nằm trên **Java Stack** trong Stack Frame của method `createPoint()`. Object `Point` thực sự nằm trên **Heap**. Khi `createPoint()` return, Stack Frame bị hủy, nhưng Object trên Heap VẪN TỒN TẠI nếu có Reference khác trỏ tới (ví dụ: biến nhận giá trị return). Nếu không còn Reference nào, Object trở thành rác và GC sẽ dọn.
**Mã nguồn Java:**
```java
public class HeapVsStack {
    static class Point { int x, y; }

    public Point createPoint() {
        Point p = new Point(); // p: Stack, new Point(): Heap
        p.x = 10;
        p.y = 20;
        return p; // Reference được copy ra ngoài
    }

    public static void main(String[] args) {
        HeapVsStack demo = new HeapVsStack();
        Point result = demo.createPoint(); // result giữ Reference → Object sống
        // Khi main() kết thúc → result bị hủy → Object thành rác → GC dọn
    }
}
```
**Độ phức tạp:** Space $\mathcal{O}(1)$ (1 Object trên Heap).

---

## 4. ClassLoader Hierarchy — Delegation Model
**Đề bài chi tiết:** In ra ClassLoader của `String.class`, `javax.net.ssl.SSLSocket.class`, và lớp của ứng dụng. Giải thích kết quả.
**Phân tích thuật toán:** `String.class` được nạp bởi Bootstrap ClassLoader (trả về `null` trong Java vì Bootstrap viết bằng C). `SSLSocket` được nạp bởi Platform ClassLoader. Lớp ứng dụng được nạp bởi Application ClassLoader. Parent-First Delegation đảm bảo rằng không ai có thể tạo class `java.lang.String` giả mạo — Bootstrap luôn nạp trước.
**Mã nguồn Java:**
```java
public class ClassLoaderDemo {
    public static void main(String[] args) {
        // Bootstrap ClassLoader (null)
        System.out.println("String: " + String.class.getClassLoader());

        // Platform ClassLoader
        System.out.println("SSLSocket: " + javax.net.ssl.SSLSocket.class.getClassLoader());

        // Application ClassLoader
        System.out.println("This class: " + ClassLoaderDemo.class.getClassLoader());

        // In hierarchy
        ClassLoader cl = ClassLoaderDemo.class.getClassLoader();
        while (cl != null) {
            System.out.println("  → " + cl);
            cl = cl.getParent();
        }
        System.out.println("  → Bootstrap (null)");
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$.

---

## 5. JIT Warm-up Benchmark
**Đề bài chi tiết:** Đo thời gian thực thi của hàm sắp xếp mảng khi "Cold" (Lần đầu tiên) và sau khi "Warm" (Lần thứ 10,000). Tính Speedup.
**Phân tích thuật toán:** Lần đầu, JVM dùng Interpreter để thực thi — mỗi bytecode được dịch sang Machine Code tại chỗ rồi thực thi, cực chậm. Sau ~10,000 lần gọi (Default CompileThreshold), C2 JIT Compiler biên dịch toàn bộ method thành Native Code tối ưu, bao gồm Loop Unrolling, Bounds Check Elimination, và SIMD Vectorization. Speedup thường là 10x-50x.
**Mã nguồn Java:**
```java
import java.util.Arrays;
public class JITWarmup {
    static void sortCopy(int[] original) {
        int[] copy = Arrays.copyOf(original, original.length);
        Arrays.sort(copy);
    }

    public static void main(String[] args) {
        int[] data = new int[100_000];
        for (int i = 0; i < data.length; i++) data[i] = data.length - i;

        // Cold
        long t1 = System.nanoTime();
        sortCopy(data);
        long cold = System.nanoTime() - t1;

        // Warm up
        for (int i = 0; i < 15_000; i++) sortCopy(data);

        // Hot
        long t2 = System.nanoTime();
        sortCopy(data);
        long hot = System.nanoTime() - t2;

        System.out.printf("Cold: %,d ns%nHot:  %,d ns%nSpeedup: %.1fx%n", cold, hot, (double)cold / hot);
    }
}
```
**Độ phức tạp:** Cùng $\mathcal{O}(N \log N)$ nhưng hằng số khác biệt rất lớn.

---

## 6. Metaspace Monitoring
**Đề bài chi tiết:** Sử dụng `ManagementFactory` để đọc bộ nhớ Metaspace hiện tại. Giải thích Metaspace chứa gì.
**Phân tích thuật toán:** Metaspace (Thay thế PermGen từ Java 8) nằm ngoài Heap, lưu trữ: Class metadata (Tên lớp, phương thức, trường), Constant Pool, và Bytecode đã compile. Nó tự động mở rộng theo Native Memory (Không bị giới hạn bởi `-Xmx`). Dùng `-XX:MaxMetaspaceSize` để giới hạn.
**Mã nguồn Java:**
```java
import java.lang.management.*;
import java.util.List;
public class MetaspaceMonitor {
    public static void main(String[] args) {
        List<MemoryPoolMXBean> pools = ManagementFactory.getMemoryPoolMXBeans();
        for (MemoryPoolMXBean pool : pools) {
            if (pool.getName().contains("Metaspace") || pool.getName().contains("Class")) {
                MemoryUsage usage = pool.getUsage();
                System.out.printf("Pool: %s%n  Used: %,d bytes%n  Max: %,d bytes%n",
                        pool.getName(), usage.getUsed(),
                        usage.getMax() == -1 ? Long.MAX_VALUE : usage.getMax());
            }
        }
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 7. StackOverflowError — Đệ Quy Sâu
**Đề bài chi tiết:** Viết hàm đệ quy vô hạn. Bắt `StackOverflowError`. In ra số tầng đệ quy tối đa trước khi crash.
**Phân tích thuật toán:** Mỗi Stack Frame tốn vài trăm byte (tùy số lượng biến cục bộ). Với Stack size mặc định 1MB, số Frame tối đa thường khoảng 5,000-20,000 (Tùy kích thước mỗi Frame). Lưu ý: `StackOverflowError` là Error, KHÔNG phải Exception — nó nghiêm trọng hơn và thường không nên catch trong Production.
**Mã nguồn Java:**
```java
public class StackOverflowDemo {
    static int depth = 0;

    static void recurse() {
        depth++;
        recurse();
    }

    public static void main(String[] args) {
        try {
            recurse();
        } catch (StackOverflowError e) {
            System.out.println("Max recursion depth: " + depth);
            // Thử chạy với: java -Xss256k StackOverflowDemo
            // và: java -Xss4m StackOverflowDemo
        }
    }
}
```
**Độ phức tạp:** Space $\mathcal{O}(N)$ cho Call Stack, $N$ bị giới hạn bởi `-Xss`.

---

## 8. Object Header Size — JOL Analysis
**Đề bài chi tiết:** Sử dụng JOL (Java Object Layout) để in ra kích thước thực tế của Object Header trên JVM 64-bit.
**Phân tích thuật toán:** Trên HotSpot JVM 64-bit với Compressed Oops: Object Header = 12 bytes (8 bytes Mark Word + 4 bytes Class Pointer). Padding thêm 4 bytes để căn chỉnh (Alignment) thành bội số 8. Tổng tối thiểu: 16 bytes. Mỗi Reference field tốn 4 bytes (Compressed) hoặc 8 bytes (Uncompressed).
**Mã nguồn Java:**
```java
// Cần dependency: org.openjdk.jol:jol-core:0.16
import org.openjdk.jol.info.ClassLayout;
public class ObjectHeaderDemo {
    static class Empty {} // Object rỗng
    static class OneInt { int x; } // Object có 1 field int
    static class OneRef { Object ref; } // Object có 1 field reference

    public static void main(String[] args) {
        System.out.println(ClassLayout.parseClass(Empty.class).toPrintable());
        System.out.println(ClassLayout.parseClass(OneInt.class).toPrintable());
        System.out.println(ClassLayout.parseClass(OneRef.class).toPrintable());
    }
}
```
**Độ phức tạp:** Mỗi Object tối thiểu 16 bytes overhead.

---

## 9. Biên Dịch Có Điều Kiện (Conditional JIT)
**Đề bài chi tiết:** Sử dụng flag `-XX:+PrintCompilation` để quan sát method nào được JIT compile. Viết code gọi 1 method hơn 10,000 lần.
**Phân tích thuật toán:** HotSpot JVM đếm số lần mỗi method được gọi (Invocation Counter) và số lần vòng lặp chạy (Back-edge Counter). Khi tổng vượt threshold (Mặc định ~10,000), method được đưa vào hàng đợi biên dịch JIT. Flag `-XX:+PrintCompilation` in ra log mỗi khi method được compile.
**Mã nguồn Java:**
```java
// Chạy: java -XX:+PrintCompilation ConditionalJIT
public class ConditionalJIT {
    static int hotMethod(int x) {
        return x * x + x; // Sẽ bị JIT compile sau ~10000 lần gọi
    }

    static int coldMethod(int x) {
        return x + 1; // Chỉ gọi 1 lần → Interpreter
    }

    public static void main(String[] args) {
        coldMethod(42); // Gọi 1 lần → Không compile

        for (int i = 0; i < 100_000; i++) {
            hotMethod(i); // Gọi 100,000 lần → JIT compile
        }
    }
}
```
**Độ phức tạp:** Sau JIT, `hotMethod` chạy nhanh gấp 10-50x so với Interpreter.

---

## 10. Escape Analysis Demo
**Đề bài chi tiết:** Viết code tạo Object bên trong method mà KHÔNG trả về bên ngoài. Chứng minh JIT có thể loại bỏ việc cấp phát Heap (Scalar Replacement).
**Phân tích thuật toán:** Khi JIT phát hiện một Object được tạo bên trong method và Reference của nó KHÔNG "thoát" ra ngoài (Không return, không gán vào field, không truyền cho Thread khác), JIT thực hiện "Escape Analysis" và quyết định: (1) Cấp phát Object trên Stack thay vì Heap, hoặc (2) Phân rã Object thành các biến Scalar riêng lẻ (Scalar Replacement). Kết quả: KHÔNG có cấp phát Heap, KHÔNG có GC pressure.
**Mã nguồn Java:**
```java
public class EscapeAnalysisDemo {
    static class Point { int x, y; }

    // Object 'p' KHÔNG escape → JIT sẽ Scalar Replace
    static int sumPoint(int a, int b) {
        Point p = new Point(); // JIT có thể loại bỏ hoàn toàn 'new'
        p.x = a;
        p.y = b;
        return p.x + p.y; // Trở thành: return a + b;
    }

    public static void main(String[] args) {
        long sum = 0;
        for (int i = 0; i < 100_000_000; i++) {
            sum += sumPoint(i, i + 1);
        }
        System.out.println(sum);
        // Với Escape Analysis: 0 Object trên Heap, GC không bao giờ chạy
        // Không có EA: 100 triệu Object → GC chạy hàng trăm lần
    }
}
```
**Độ phức tạp:** Với EA: Space $\mathcal{O}(1)$. Không có EA: Space $\mathcal{O}(N)$.

---

## 11. Custom ClassLoader
**Đề bài chi tiết:** Viết một ClassLoader tùy chỉnh đọc file `.class` từ một thư mục tùy ý. Nạp và thực thi lớp động.
**Phân tích thuật toán:** Kế thừa `ClassLoader` và override `findClass()`. Đọc file `.class` thành mảng byte (`byte[]`), rồi gọi `defineClass()` để chuyển bytecode thành đối tượng `Class<?>`. Đây là nền tảng của Plugin systems, OSGi, và Application Servers.
**Mã nguồn Java:**
```java
import java.io.*;
import java.nio.file.*;
public class CustomClassLoader extends ClassLoader {
    private final String classDir;

    public CustomClassLoader(String classDir, ClassLoader parent) {
        super(parent);
        this.classDir = classDir;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            String path = classDir + "/" + name.replace('.', '/') + ".class";
            byte[] bytes = Files.readAllBytes(Path.of(path));
            return defineClass(name, bytes, 0, bytes.length);
        } catch (IOException e) {
            throw new ClassNotFoundException(name, e);
        }
    }

    public static void main(String[] args) throws Exception {
        CustomClassLoader loader = new CustomClassLoader("/tmp/classes", 
            ClassLoader.getSystemClassLoader());
        Class<?> clazz = loader.loadClass("com.example.Plugin");
        Object instance = clazz.getDeclaredConstructor().newInstance();
        System.out.println("Loaded: " + instance.getClass().getName());
        System.out.println("ClassLoader: " + instance.getClass().getClassLoader());
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(K)$ với $K$ là kích thước file `.class`.

---

## 12. Runtime Memory Info
**Đề bài chi tiết:** In ra Heap Used, Heap Max, và số CPU cores mà JVM nhận biết. Sử dụng `Runtime` API.
**Phân tích thuật toán:** `Runtime.getRuntime()` cung cấp thông tin JVM: `maxMemory()` là giới hạn `-Xmx`, `totalMemory()` là Heap hiện tại (có thể mở rộng), `freeMemory()` là phần chưa dùng trong `totalMemory()`. `availableProcessors()` trả về số CPU/Core mà JVM nhìn thấy (Quan trọng cho ForkJoinPool).
**Mã nguồn Java:**
```java
public class RuntimeInfo {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        long MB = 1024 * 1024;
        System.out.printf("Max Memory (Xmx):  %,d MB%n", rt.maxMemory() / MB);
        System.out.printf("Total Memory:      %,d MB%n", rt.totalMemory() / MB);
        System.out.printf("Free Memory:       %,d MB%n", rt.freeMemory() / MB);
        System.out.printf("Used Memory:       %,d MB%n", (rt.totalMemory() - rt.freeMemory()) / MB);
        System.out.printf("Available CPUs:    %d%n", rt.availableProcessors());
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 13. Bytecode If-Else Analysis
**Đề bài chi tiết:** Viết hàm `max(a, b)` dùng `if-else`. Dùng `javap -c` xem bytecode. So sánh với `Math.max()`.
**Phân tích thuật toán:** Bytecode `if-else` sử dụng lệnh `if_icmpge` (So sánh 2 số nguyên, nhảy nếu $\ge$). JIT Compiler tối ưu thành lệnh `cmov` (Conditional Move) trên x86, loại bỏ Branch Prediction penalty. `Math.max()` cũng dùng `if-else` bên trong nhưng thường bị JIT inline hoàn toàn.
**Mã nguồn Java:**
```java
public class BytecodeIfElse {
    public int max(int a, int b) {
        if (a >= b) return a;
        else return b;
    }
    // Bytecode:
    // 0: iload_1
    // 1: iload_2
    // 2: if_icmplt 7    ← Nếu a < b, nhảy đến dòng 7
    // 5: iload_1
    // 6: ireturn         ← return a
    // 7: iload_2
    // 8: ireturn         ← return b
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 14. TLAB Allocation Test
**Đề bài chi tiết:** Tạo 10 triệu Object nhỏ liên tiếp. Tại sao nó cực nhanh dù tạo Object trên Heap?
**Phân tích thuật toán:** JVM sử dụng TLAB (Thread Local Allocation Buffer) — mỗi Thread được cấp riêng một vùng nhớ nhỏ trên Heap. Cấp phát Object chỉ là: `pointer += objectSize`. Không cần Lock, không cần CAS, cực nhanh. TLAB chỉ hết khi đầy, lúc đó Thread mới xin vùng mới từ Heap chung (Cần synchronization).
**Mã nguồn Java:**
```java
public class TLABDemo {
    static class Tiny { int x; } // Object rất nhỏ: 16 bytes

    public static void main(String[] args) {
        long start = System.nanoTime();
        for (int i = 0; i < 10_000_000; i++) {
            new Tiny(); // TLAB: bump pointer, cực nhanh
        }
        long elapsed = System.nanoTime() - start;
        System.out.printf("10M Objects in %d ms (%.1f ns/obj)%n",
                elapsed / 1_000_000, (double) elapsed / 10_000_000);
    }
}
```
**Độ phức tạp:** TLAB Allocation: Amortized $\mathcal{O}(1)$.

---

## 15. Deoptimization Trigger
**Đề bài chi tiết:** JIT Compiler giả định một biến luôn là `null` (Speculative optimization). Viết code khiến giả định bị sai, buộc JIT phải Deoptimize.
**Phân tích thuật toán:** JIT thu thập Profile data (Type profiling). Nếu 10,000 lần gọi, tham số luôn là `ClassA`, JIT sẽ inline trực tiếp method của `ClassA` (Monomorphic dispatch). Khi đột ngột nhận `ClassB`, JIT phát hiện sai → Deoptimize: Trả code từ Native về Interpreter, rồi compile lại với Profile mới (Megamorphic dispatch). Deoptimization tốn vài ms.
**Mã nguồn Java:**
```java
public class DeoptDemo {
    interface Shape { int area(); }
    static class Circle implements Shape {
        int r;
        Circle(int r) { this.r = r; }
        public int area() { return (int)(Math.PI * r * r); }
    }
    static class Square implements Shape {
        int s;
        Square(int s) { this.s = s; }
        public int area() { return s * s; }
    }

    static int computeArea(Shape shape) {
        return shape.area(); // JIT inline Circle.area() nếu luôn là Circle
    }

    public static void main(String[] args) {
        // Warm up: Luôn Circle → JIT giả định Monomorphic
        for (int i = 0; i < 100_000; i++) {
            computeArea(new Circle(i));
        }
        // Trigger Deoptimization: Đột ngột truyền Square
        System.out.println(computeArea(new Square(5)));
        // Dùng -XX:+PrintCompilation để thấy "made not entrant" (deopt marker)
    }
}
```
**Độ phức tạp:** Deoptimization gây spike latency vài ms.

---

## 16-30. Tóm lược phân tích các bài tập JVM còn lại

- **Bài 16 (Constant Pool Analysis):** Dùng `javap -verbose` xem Constant Pool. String literal "Hello" được lưu trong String Pool (Heap Interned). `Integer.valueOf(127)` dùng Cache (Flyweight Pattern).
- **Bài 17 (Method Inlining Threshold):** Phương thức < 35 bytecodes được inline tự động. Thêm code rác (Padding) vào method để vượt threshold → JIT KHÔNG inline → Chậm hơn.
- **Bài 18 (Array vs ArrayList Memory):** Đo Memory: `int[1000]` tốn ~4KB. `ArrayList<Integer>` 1000 phần tử tốn ~20KB (Object headers + Boxing).
- **Bài 19 (Fork-Join Pool & Available Processors):** `ForkJoinPool.commonPool()` dùng `Runtime.availableProcessors() - 1` threads. Trên Docker container giới hạn 2 CPU, Pool chỉ có 1 worker thread.
- **Bài 20 (Classpath Conflict):** 2 JAR chứa cùng class `com.lib.Util` nhưng version khác. ClassLoader nạp cái nào? (Cái đầu tiên trên Classpath).
- **Bài 21 (Compressed Oops vs Uncompressed):** Chạy benchmark với `-XX:+UseCompressedOops` và `-XX:-UseCompressedOops`. Đo Memory usage của 1M Objects.
- **Bài 22 (JFR Event Recording):** Dùng `jcmd <pid> JFR.start` để ghi JIT Compilation events. Phân tích trong JDK Mission Control.
- **Bài 23 (Static Initializer Order):** Class A phụ thuộc Class B trong static block, B phụ thuộc A → Circular dependency → Hành vi không xác định.
- **Bài 24 (Bytecode Loop):** So sánh bytecode của `for` loop vs `while` loop vs `do-while`. Chúng gần như giống nhau.
- **Bài 25 (JNI Performance Overhead):** Gọi Native method qua JNI tốn ~100ns overhead mỗi lần (Do context switch giữa JVM và Native code).
- **Bài 26 (Module System Encapsulation):** Java 9+ module `java.base` không exports `jdk.internal.*`. Dùng `--add-opens` để bypass → Rủi ro bảo mật.
- **Bài 27 (Code Cache Overflow):** Tạo hàng nghìn Lambda/Anonymous class → Code Cache đầy → `-XX:ReservedCodeCacheSize=256m`.
- **Bài 28 (Safepoint Bias):** JIT chỉ chèn Safepoint vào cuối Counted Loop. Vòng lặp `for (long i = 0; i < Long.MAX_VALUE; i++)` KHÔNG có safepoint → GC không thể Stop-The-World → Hệ thống treo. Fix: Dùng `int` counter hoặc thêm `-XX:+UseCountedLoopSafepoints`.
- **Bài 29 (GC Roots Identification):** Liệt kê 4 loại GC Roots: Stack variables, Static fields, JNI references, Monitor locks. Object không reachable từ bất kỳ Root nào → Rác.
- **Bài 30 (CDS AppCDS Benchmark):** Tạo Shared Archive bằng `java -Xshare:dump`. So sánh Startup time trước và sau CDS.
