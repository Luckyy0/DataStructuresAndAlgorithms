# Companion Problems: 04-Object-Memory-Layout-And-Oops

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Object Memory Layout, Object Header, Compressed Oops, và kỹ thuật ước lượng Memory Footprint trên HotSpot JVM.

---

## 1. Tính Object Size — Empty Object
**Đề bài chi tiết:** Tính kích thước bộ nhớ thực tế của `new Object()` trên JVM 64-bit với Compressed Oops.
**Phân tích thuật toán:** Object rỗng không có Instance Data. Header = Mark Word (8B) + Klass Pointer (4B Compressed) = 12B. Padding: 12 → 16 (Bội 8 gần nhất). Tổng: **16 bytes**. Đây là kích thước tối thiểu tuyệt đối của bất kỳ Object nào trên JVM.
**Mã nguồn Java:**
```java
public class EmptyObjectSize {
    // Header: 12B, Data: 0B, Padding: 4B → Total: 16B
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        rt.gc();
        long before = rt.totalMemory() - rt.freeMemory();
        
        Object[] arr = new Object[100_000];
        for (int i = 0; i < arr.length; i++) arr[i] = new Object();
        
        long after = rt.totalMemory() - rt.freeMemory();
        System.out.printf("Approx size per Object: %d bytes%n", 
                (after - before) / arr.length);
        // Kết quả: ~16 bytes
    }
}
```
**Độ phức tạp:** Mỗi Object tối thiểu **16 bytes**.

---

## 2. Tính Object Size — Primitive Fields
**Đề bài chi tiết:** Tính kích thước Object có các fields: `int x`, `long y`, `boolean z`.
**Phân tích thuật toán:** JVM sắp xếp lại fields (Field Reordering) để tối ưu alignment. Thứ tự thường là: long (8B) trước, int (4B) sau, boolean (1B) cuối. Layout: Header 12B + int 4B + long 8B + boolean 1B = 25B. Padding 7B → **32 bytes**. Nhưng với Field Reordering: Header 12B + (gap 4B) + long 8B + int 4B + boolean 1B + padding 3B = 32B. Hoặc: Header 12B + int 4B (fill gap) + long 8B + boolean 1B + pad 7B = 32B.
**Mã nguồn Java:**
```java
public class PrimitiveFieldsSize {
    static class MyObj {
        int x;      // 4 bytes
        long y;     // 8 bytes
        boolean z;  // 1 byte
    }
    // Possible layout (JVM reorders):
    // Offset 0-11:  Header (12B)
    // Offset 12-15: int x (4B) — fills header gap
    // Offset 16-23: long y (8B) — aligned to 8
    // Offset 24:    boolean z (1B)
    // Offset 25-31: Padding (7B)
    // Total: 32 bytes

    public static void main(String[] args) {
        // Dùng JOL để xác minh chính xác
        // System.out.println(ClassLayout.parseClass(MyObj.class).toPrintable());
        System.out.println("Expected: 32 bytes (with field reordering + padding)");
    }
}
```
**Độ phức tạp:** **32 bytes** cho 13 bytes dữ liệu (Overhead 2.5x).

---

## 3. Tính Array Size
**Đề bài chi tiết:** Tính kích thước của `new int[100]`, `new long[100]`, `new Object[100]`.
**Phân tích thuật toán:** Array Header = 16B (Mark 8B + Klass 4B + Length 4B). `int[100]` = 16B + 100×4B = **416 bytes**. `long[100]` = 16B + 100×8B = **816 bytes**. `Object[100]` = 16B + 100×4B (Compressed Refs) = **416 bytes** (CHỈ chứa References, Objects riêng).
**Mã nguồn Java:**
```java
public class ArraySizeCalc {
    public static void main(String[] args) {
        // Array Header: 16 bytes (Mark 8 + Klass 4 + Length 4)
        System.out.println("int[100]:    " + (16 + 100 * 4) + " bytes = 416");
        System.out.println("long[100]:   " + (16 + 100 * 8) + " bytes = 816");
        System.out.println("Object[100]: " + (16 + 100 * 4) + " bytes = 416 (refs only)");
        System.out.println("byte[100]:   " + alignTo8(16 + 100 * 1) + " bytes = 120");
        System.out.println("boolean[7]:  " + alignTo8(16 + 7 * 1) + " bytes = 24");
    }
    static int alignTo8(int size) { return (size + 7) & ~7; }
}
```
**Độ phức tạp:** Array overhead = **16 bytes** cố định.

---

## 4. Integer vs int — Memory Comparison
**Đề bài chi tiết:** So sánh bộ nhớ thực tế của `int[1_000_000]` vs `Integer[1_000_000]`.
**Phân tích thuật toán:** `int[1M]` = 16B header + 4MB data = **~4MB**. `Integer[1M]` = 16B header + 4MB refs + 1M × 16B Integer Objects = **~20MB**. Tỷ lệ: 20/4 = **5x overhead**. Trong thực tế, `ArrayList<Integer>` còn tệ hơn vì thêm ArrayList Object header + internal array overhead.
**Mã nguồn Java:**
```java
public class IntVsInteger {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();

        // int[]
        rt.gc(); long b1 = rt.totalMemory() - rt.freeMemory();
        int[] primitives = new int[1_000_000];
        long a1 = rt.totalMemory() - rt.freeMemory();

        // Integer[]
        rt.gc(); long b2 = rt.totalMemory() - rt.freeMemory();
        Integer[] wrappers = new Integer[1_000_000];
        for (int i = 0; i < wrappers.length; i++) wrappers[i] = i + 128; // Outside cache
        long a2 = rt.totalMemory() - rt.freeMemory();

        System.out.printf("int[1M]:     %,d KB%n", (a1 - b1) / 1024);
        System.out.printf("Integer[1M]: %,d KB%n", (a2 - b2) / 1024);
    }
}
```
**Độ phức tạp:** `Integer[]` gấp ~5x Memory so với `int[]`.

---

## 5. HashMap Memory Footprint
**Đề bài chi tiết:** Tính Memory footprint chính xác của `HashMap<Integer, Integer>` chứa 1 triệu entries.
**Phân tích thuật toán:** Mỗi entry gồm: (1) `HashMap.Node` Object: Header 12B + hash 4B + key ref 4B + value ref 4B + next ref 4B + padding 4B = **32B**. (2) Integer key: **16B**. (3) Integer value: **16B**. Per entry: 32 + 16 + 16 = **64B**. Tổng entries: 64MB. HashMap internal `Node[]` array: capacity ~1.33M × 4B ref = ~5.3MB. HashMap Object: ~48B. **Tổng ≈ 69MB** cho 8MB dữ liệu thuần.
**Mã nguồn Java:**
```java
import java.util.HashMap;
public class HashMapFootprint {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        rt.gc();
        long before = rt.totalMemory() - rt.freeMemory();

        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < 1_000_000; i++) {
            map.put(i + 128, i + 128); // Outside Integer cache
        }

        rt.gc();
        long after = rt.totalMemory() - rt.freeMemory();
        long used = after - before;
        System.out.printf("HashMap<Integer,Integer> 1M entries: %,d KB (%,d MB)%n",
                used / 1024, used / (1024 * 1024));
        System.out.printf("Pure data (2 ints × 1M):             %,d KB%n", 
                1_000_000 * 8 / 1024);
        System.out.printf("Overhead ratio: %.1fx%n", (double) used / (1_000_000 * 8));
    }
}
```
**Độ phức tạp:** ~8x overhead cho `HashMap<Integer, Integer>`.

---

## 6. LinkedList vs ArrayList Memory
**Đề bài chi tiết:** So sánh Memory footprint của `LinkedList<Integer>` vs `ArrayList<Integer>` cho $10^5$ phần tử.
**Phân tích thuật toán:** **LinkedList**: Mỗi Node = Header 12B + item ref 4B + next ref 4B + prev ref 4B + padding 8B ≈ **32-40B**. + Integer Object 16B. Per element: ~48-56B. **ArrayList**: Internal `Object[]` = 16B header + N×4B refs. + Integer Object 16B per element. Per element: ~20B + amortized array. ArrayList nhẹ hơn LinkedList khoảng **2-3x**.
**Mã nguồn Java:**
```java
import java.util.*;
public class LinkedVsArrayListMemory {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        int N = 100_000;

        rt.gc(); long b1 = rt.totalMemory() - rt.freeMemory();
        ArrayList<Integer> al = new ArrayList<>();
        for (int i = 0; i < N; i++) al.add(i + 128);
        long alSize = (rt.totalMemory() - rt.freeMemory()) - b1;

        rt.gc(); long b2 = rt.totalMemory() - rt.freeMemory();
        LinkedList<Integer> ll = new LinkedList<>();
        for (int i = 0; i < N; i++) ll.add(i + 128);
        long llSize = (rt.totalMemory() - rt.freeMemory()) - b2;

        System.out.printf("ArrayList:  %,d KB%n", alSize / 1024);
        System.out.printf("LinkedList: %,d KB%n", llSize / 1024);
        System.out.printf("LinkedList / ArrayList: %.1fx%n", (double) llSize / alSize);
    }
}
```
**Độ phức tạp:** LinkedList ~2-3x Memory so với ArrayList.

---

## 7. Compressed Oops On vs Off
**Đề bài chi tiết:** So sánh Memory usage khi bật vs tắt Compressed Oops (`-XX:+UseCompressedOops` vs `-XX:-UseCompressedOops`).
**Phân tích thuật toán:** Compressed Oops nén Reference từ 8B xuống 4B. Klass Pointer cũng 4B thay vì 8B. Object Header giảm từ 16B xuống 12B. Mỗi Reference field giảm 4B. Với 1 triệu Object, tiết kiệm ~4MB chỉ từ Headers. Với 10M references, tiết kiệm ~40MB.
**Mã nguồn Java:**
```java
// Chạy 2 lần:
// java -XX:+UseCompressedOops CompressedOopsTest
// java -XX:-UseCompressedOops CompressedOopsTest
public class CompressedOopsTest {
    static class Node { Node next; int value; }

    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        rt.gc();
        long before = rt.totalMemory() - rt.freeMemory();

        Node head = new Node();
        Node curr = head;
        for (int i = 0; i < 1_000_000; i++) {
            curr.next = new Node();
            curr.next.value = i;
            curr = curr.next;
        }

        rt.gc();
        long after = rt.totalMemory() - rt.freeMemory();
        System.out.printf("1M Nodes: %,d MB%n", (after - before) / (1024 * 1024));
        // Compressed: ~24 bytes/Node → ~24MB
        // Uncompressed: ~32 bytes/Node → ~32MB
    }
}
```
**Độ phức tạp:** Compressed Oops tiết kiệm 25-40% Memory.

---

## 8. String Memory — Latin1 vs UTF-16
**Đề bài chi tiết:** So sánh Memory footprint của String ASCII vs String Unicode (có ký tự ngoài Latin-1).
**Phân tích thuật toán:** Từ Java 9 (Compact Strings): String ASCII dùng `byte[]` (1B/char). String Unicode dùng `byte[]` nhưng mã hóa UTF-16 (2B/char). String Object: Header 12B + hash 4B + coder 1B + value ref 4B + padding 3B = **24B** + `byte[]` (Header 16B + data). "Hello" (5 chars, ASCII): 24B + 16B + 5B + pad 3B = **48 bytes**. "Xin chào" (8 chars, Unicode): 24B + 16B + 16B = **56 bytes**.
**Mã nguồn Java:**
```java
public class StringMemoryLayout {
    public static void main(String[] args) {
        String ascii = "Hello";      // Latin-1: byte[] {72,101,108,108,111}
        String unicode = "Xin chào"; // UTF-16: byte[] with 2 bytes per char

        // String Object: ~24 bytes
        // byte[] for "Hello": 16 (header) + 5 (data) + 3 (padding) = 24 bytes
        // Total "Hello": 24 + 24 = 48 bytes for 5 characters

        System.out.println("ASCII 'Hello' encoding: " + ascii.getBytes().length + " bytes data");
        System.out.println("Unicode 'Xin chào' encoding: " + 
            (unicode.getBytes(java.nio.charset.StandardCharsets.UTF_16).length - 2) + " bytes data");
    }
}
```
**Độ phức tạp:** ASCII String tiết kiệm ~50% so với JDK 8 (char[] 2B/char).

---

## 9. Boolean Field vs BitSet
**Đề bài chi tiết:** So sánh Memory khi lưu $10^6$ boolean values bằng `boolean[]` vs `BitSet`.
**Phân tích thuật toán:** `boolean[1M]` = 16B header + 1M × 1B = **~1MB** (Mỗi boolean tốn 1 byte, không phải 1 bit). `BitSet(1M)` nội bộ dùng `long[]`: 16B header + ceil(1M/64) × 8B = 16B + 15625 × 8B = **~122KB**. BitSet tiết kiệm **8x** so với `boolean[]`.
**Mã nguồn Java:**
```java
import java.util.BitSet;
public class BooleanVsBitSet {
    public static void main(String[] args) {
        int N = 1_000_000;
        Runtime rt = Runtime.getRuntime();

        rt.gc(); long b1 = rt.totalMemory() - rt.freeMemory();
        boolean[] bools = new boolean[N];
        long boolSize = (rt.totalMemory() - rt.freeMemory()) - b1;

        rt.gc(); long b2 = rt.totalMemory() - rt.freeMemory();
        BitSet bits = new BitSet(N);
        bits.set(0, N); // Allocate internal long[]
        long bitSetSize = (rt.totalMemory() - rt.freeMemory()) - b2;

        System.out.printf("boolean[1M]: %,d KB%n", boolSize / 1024);
        System.out.printf("BitSet(1M):  %,d KB%n", bitSetSize / 1024);
        System.out.printf("Savings:     %.1fx%n", (double) boolSize / bitSetSize);
    }
}
```
**Độ phức tạp:** BitSet tiết kiệm **~8x** Memory.

---

## 10. Multi-dimensional Array Overhead
**Đề bài chi tiết:** Tính số Object và tổng overhead cho `int[1000][1000]` vs mảng 1D `int[1_000_000]`.
**Phân tích thuật toán:** `int[1M]` = 1 Object: 16B header + 4MB data = **~4MB**. `int[1000][1000]` = 1001 Objects (1 mảng cha + 1000 mảng con). Mảng cha: 16B + 1000×4B refs = 4016B. Mỗi mảng con: 16B + 1000×4B = 4016B. Tổng: 4016 + 1000×4016 = **~3.9MB + 16KB headers**. Overhead: 1001 × 16B headers = **~16KB** thêm.
**Mã nguồn Java:**
```java
public class MultiDimOverhead {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();

        rt.gc(); long b1 = rt.totalMemory() - rt.freeMemory();
        int[] flat = new int[1_000_000];
        long flatSize = (rt.totalMemory() - rt.freeMemory()) - b1;

        rt.gc(); long b2 = rt.totalMemory() - rt.freeMemory();
        int[][] multi = new int[1000][1000];
        long multiSize = (rt.totalMemory() - rt.freeMemory()) - b2;

        System.out.printf("1D int[1M]:       %,d KB (1 Object)%n", flatSize / 1024);
        System.out.printf("2D int[1000][1000]: %,d KB (1001 Objects)%n", multiSize / 1024);
        System.out.printf("Extra overhead:    %,d KB%n", (multiSize - flatSize) / 1024);
    }
}
```
**Độ phức tạp:** 2D mảng tốn thêm ~$M \times 16$B headers.

---

## 11. Enum Memory Footprint
**Đề bài chi tiết:** Tính kích thước bộ nhớ của Enum với fields tùy chỉnh.
**Phân tích thuật toán:** Enum constant = Singleton Object trên Heap. `enum Color { RED, GREEN, BLUE }` tạo 3 Object + 1 internal `$VALUES` array. Mỗi Enum Object: Header 12B + ordinal 4B + name ref 4B + padding 4B = **24B** minimum. Nếu Enum có thêm fields thì cộng thêm.
**Mã nguồn Java:**
```java
public class EnumMemory {
    enum Planet {
        MERCURY(3.303e+23, 2.4397e6),
        VENUS(4.869e+24, 6.0518e6),
        EARTH(5.976e+24, 6.37814e6);
        // Mỗi constant: Header 12B + ordinal 4B + name ref 4B 
        //               + double mass 8B + double radius 8B + padding = 40-48B

        final double mass, radius;
        Planet(double m, double r) { mass = m; radius = r; }
    }

    public static void main(String[] args) {
        for (Planet p : Planet.values()) {
            System.out.println(p + " ordinal=" + p.ordinal());
        }
        // Planet.values() tạo Array MỚI mỗi lần gọi!
        // Cache: private static final Planet[] CACHED = Planet.values();
    }
}
```
**Độ phức tạp:** Mỗi Enum constant ~24-48B.

---

## 12. Record Class Memory
**Đề bài chi tiết:** Tính kích thước của Java Record (JDK 16+). Record có phải Value Type không?
**Phân tích thuật toán:** `record Point(int x, int y)` = Regular Object trên Heap. KHÔNG phải Value Type. Header 12B + x 4B + y 4B + padding 4B = **24 bytes**. So sánh: `int[2]` = 16B header + 8B data = **24 bytes**. Gần bằng nhau, nhưng Record có thêm `toString()`, `equals()`, `hashCode()` tự động.
**Mã nguồn Java:**
```java
public class RecordMemory {
    record Point(int x, int y) {}
    // Header: 12B, x: 4B, y: 4B, padding: 4B → 24 bytes
    // Giống hệt Object bình thường! Record KHÔNG phải Value Type.

    public static void main(String[] args) {
        Point p = new Point(3, 4);
        System.out.println(p); // Point[x=3, y=4]
        // Record vẫn là Object trên Heap, vẫn có Identity, vẫn có Header
    }
}
```
**Độ phức tạp:** **24 bytes** — Giống Object thường.

---

## 13-30. Tóm lược phân tích các bài tập còn lại

- **Bài 13 (Optional Memory):** `Optional<Integer>` = Header 12B + value ref 4B = 16B + Integer 16B = **32B** cho 1 int. `OptionalInt` = Header 12B + int 4B + boolean isPresent 1B + pad = **24B** (Tốt hơn).
- **Bài 14 (TreeMap.Entry Footprint):** Red-Black Tree Node: Header 12B + key ref 4B + value ref 4B + left ref 4B + right ref 4B + parent ref 4B + boolean color 1B + pad = **40B**. + Key/Value Objects.
- **Bài 15 (Wrapper vs Primitive in Loop):** Benchmark tạo 10M `Double` vs `double`. Đo Memory và Time difference.
- **Bài 16 (False Sharing Detection):** 2 Threads ghi vào 2 fields nằm trên cùng Cache Line (64B) → Performance giảm. Fix: `@Contended` annotation hoặc padding thủ công.
- **Bài 17 (Object Graph Walk):** Dùng Reflection để duyệt toàn bộ Object graph từ Root. Tính tổng Deep Size (Bao gồm tất cả Object được reference).
- **Bài 18 (Flattened Array Simulation):** Mô phỏng Array of Structs (Flattened) bằng 2 primitive arrays song song: `int[] xs`, `int[] ys` thay vì `Point[]`. So sánh Memory.
- **Bài 19 (Padding Waste Maximization):** Thiết kế Class có Padding waste tối đa. Ví dụ: `class Worst { byte a; long b; byte c; long d; }`.
- **Bài 20 (Flyweight Pattern — Integer Cache):** Mở rộng Integer Cache bằng `-XX:AutoBoxCacheMax=1000`. Đo Memory savings khi nhiều Integer values nằm trong range.
- **Bài 21 (ArrayDeque vs LinkedList Memory):** `ArrayDeque` dùng circular array: ~4B/element. `LinkedList`: ~48B/element. Benchmark 1M elements.
- **Bài 22 (ConcurrentHashMap Overhead):** Thêm overhead so với HashMap: Segment locks, volatile fields, Node padding. ~20% thêm Memory.
- **Bài 23 (Lambda Capture Size):** Lambda capture 0 variables: Singleton instance (0 overhead). Capture 1 int: Synthetic Object Header + int = **20B**. Capture N variables: Header + N refs.
- **Bài 24 (WeakHashMap Entry):** WeakHashMap.Entry extends WeakReference → Thêm Reference Queue fields → ~48B/entry.
- **Bài 25 (PriorityQueue Internal Array):** Binary Heap dùng `Object[]`. Resize giống ArrayList. Per element: 4B ref + Object. Tổng memory ước lượng.
- **Bài 26 (Immutable Collections Memory):** `List.of(1,2,3)` tạo Compact internal representation. So sánh với `new ArrayList<>(List.of(1,2,3))`.
- **Bài 27 (String.intern() Pool Size):** `-XX:StringTableSize=60013` (Default). Tăng khi intern nhiều String → Giảm collision → Nhanh hơn nhưng tốn Native Memory.
- **Bài 28 (Object Alignment 16):** `-XX:ObjectAlignmentInBytes=16` cho Compressed Oops hỗ trợ Heap 64GB. Nhưng Padding waste tăng gấp đôi.
- **Bài 29 (Measuring Retained Size):** Dùng `jmap -histo` hoặc JFR để phân tích Retained Size vs Shallow Size. Retained = Object + tất cả Object chỉ reachable qua nó.
- **Bài 30 (Valhalla Value Type Preview):** Viết pseudo-code cho `value class Complex { double re, im; }`. Dự đoán Memory: 16B (2 doubles, NO header). `Complex[]` 1M entries: ~16MB (Flat) vs ~40MB (Object Array).
