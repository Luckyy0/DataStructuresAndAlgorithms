# Companion Problems: 03-Garbage-Collection-Basics

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Garbage Collection trong JVM, bao gồm nhận diện GC Roots, tối ưu Allocation Rate, phân tích GC Log, và thiết kế thuật toán GC-Friendly.

---

## 1. Xác Định GC Roots
**Đề bài chi tiết:** Cho đoạn code sau, liệt kê tất cả GC Roots và các Object reachable/unreachable.
**Phân tích thuật toán:** GC Roots gồm: (1) Biến cục bộ trên Stack Frame, (2) Biến static, (3) Active threads. Từ mỗi Root, duyệt đồ thị Reference (BFS/DFS) để tìm tất cả Object reachable. Object không reachable = Rác.
**Mã nguồn Java:**
```java
public class GCRootsQuiz {
    static Object staticRef = new Object(); // GC Root (static field)

    public static void main(String[] args) {
        Object a = new Object();  // GC Root (local variable on Stack)
        Object b = new Object();  // GC Root
        Object c = new Object();  // GC Root
        
        a = null; // Object ban đầu của a → UNREACHABLE → Rác
        // b vẫn trỏ tới Object → Reachable → Sống
        // c vẫn trỏ tới Object → Reachable → Sống
        // staticRef → Reachable → Sống
        
        System.gc(); // Gợi ý GC dọn Object a cũ
    }
}
```
**Độ phức tạp:** Phân tích thuần lý thuyết.

---

## 2. Circular Reference và GC
**Đề bài chi tiết:** Tạo 2 Object tham chiếu vòng (A → B → A). Gán cả 2 Reference gốc về `null`. Chứng minh GC VẪN dọn được.
**Phân tích thuật toán:** Java GC dùng **Reachability Analysis** (Từ GC Roots), KHÔNG dùng Reference Counting (Như Python CPython). Khi cả `a` và `b` bị gán `null`, không còn đường đi (Path) từ bất kỳ GC Root nào tới cả 2 Object → Cả 2 thành rác dù vẫn tham chiếu nhau.
**Mã nguồn Java:**
```java
public class CircularRefGC {
    static class Node {
        Node next;
        byte[] payload = new byte[1_000_000]; // 1MB để dễ đo
    }

    public static void main(String[] args) {
        // java -verbose:gc CircularRefGC
        Node a = new Node();
        Node b = new Node();
        a.next = b; // A → B
        b.next = a; // B → A (Circular!)
        
        a = null;
        b = null;
        // Không còn GC Root nào trỏ tới A hoặc B
        // → Cả 2 bị GC dọn dù vẫn tham chiếu vòng
        
        System.gc();
        System.out.println("After GC: Objects should be collected");
    }
}
```
**Độ phức tạp:** GC dọn thành công. Mark-and-Sweep không bị ảnh hưởng bởi Circular Reference.

---

## 3. GC Pressure Benchmark — Primitive vs Wrapper
**Đề bài chi tiết:** So sánh GC hoạt động khi tính tổng $10^8$ số bằng `long` (Zero GC) vs `Long` (Nặng GC). Chạy với `-verbose:gc`.
**Phân tích thuật toán:** `Long sum += i` thực hiện Auto-unbox, cộng, rồi Auto-box tạo Object MỚI. Tốc độ tạo rác (Allocation Rate): $\approx 24\text{B} \times 10^8 = 2.4\text{GB}$ Object rác. GC phải chạy hàng trăm Minor GC. Primitive `long` → Zero allocation → Zero GC.
**Mã nguồn Java:**
```java
public class GCPressureBenchmark {
    public static void main(String[] args) {
        // Chạy: java -verbose:gc -Xmx512m GCPressureBenchmark
        int N = 100_000_000;

        System.out.println("=== Primitive (Zero GC) ===");
        long t1 = System.nanoTime();
        long primSum = 0;
        for (int i = 0; i < N; i++) primSum += i;
        System.out.printf("Time: %d ms%n%n", (System.nanoTime() - t1) / 1_000_000);

        System.out.println("=== Wrapper (Heavy GC) ===");
        long t2 = System.nanoTime();
        Long wrapSum = 0L;
        for (int i = 0; i < N; i++) wrapSum += i;
        System.out.printf("Time: %d ms%n", (System.nanoTime() - t2) / 1_000_000);
    }
}
```
**Độ phức tạp:** Cùng $\mathcal{O}(N)$ nhưng GC overhead gấp 5-10x.

---

## 4. Memory Leak Detection — Static Collection
**Đề bài chi tiết:** Tạo một `static List<byte[]>` và liên tục thêm dữ liệu. Quan sát `OutOfMemoryError`.
**Phân tích thuật toán:** Biến `static` là GC Root vĩnh viễn. `List` được tham chiếu từ static field → Tất cả phần tử bên trong đều Reachable → GC KHÔNG BAO GIỜ dọn → Heap đầy dần → `OutOfMemoryError`. Đây là Memory Leak kinh điển nhất trong Java.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class MemoryLeakStatic {
    // GC Root vĩnh viễn: static field
    private static List<byte[]> leakyCache = new ArrayList<>();

    public static void main(String[] args) {
        // java -Xmx64m MemoryLeakStatic
        try {
            for (int i = 0; ; i++) {
                leakyCache.add(new byte[100_000]); // 100KB mỗi lần
                if (i % 100 == 0) {
                    System.out.printf("Added %d items, Heap: %dMB%n",
                            i, Runtime.getRuntime().totalMemory() / 1_000_000);
                }
            }
        } catch (OutOfMemoryError e) {
            System.out.println("CRASH: " + e.getMessage());
            System.out.println("Items in cache: " + leakyCache.size());
        }
    }
}
```
**Độ phức tạp:** Space $\mathcal{O}(\infty)$ → Crash.

---

## 5. Fix Memory Leak — Bounded Cache
**Đề bài chi tiết:** Sửa Bài 4 bằng cách giới hạn kích thước Cache. Khi đầy, xóa phần tử cũ nhất (LRU).
**Phân tích thuật toán:** Sử dụng `LinkedHashMap` với `removeEldestEntry()` override để tự động xóa phần tử cũ nhất khi vượt quá kích thước giới hạn. Đây là LRU Cache pattern. Space $\mathcal{O}(K)$ với $K$ là Max cache size → Bounded → Không bao giờ OOM.
**Mã nguồn Java:**
```java
import java.util.LinkedHashMap;
import java.util.Map;
public class BoundedCache {
    private static final int MAX_SIZE = 500;
    
    private static Map<Integer, byte[]> cache = new LinkedHashMap<>(MAX_SIZE, 0.75f, true) {
        @Override
        protected boolean removeEldestEntry(Map.Entry<Integer, byte[]> eldest) {
            return size() > MAX_SIZE; // Tự động xóa phần tử cũ nhất
        }
    };

    public static void main(String[] args) {
        // java -Xmx64m BoundedCache
        for (int i = 0; i < 100_000; i++) {
            cache.put(i, new byte[100_000]);
            if (i % 1000 == 0) {
                System.out.printf("i=%d, cache.size=%d, Heap OK%n", i, cache.size());
            }
        }
        System.out.println("Completed without OOM! Cache size: " + cache.size());
    }
}
```
**Độ phức tạp:** Space $\mathcal{O}(K)$ bounded. Time per put: Amortized $\mathcal{O}(1)$.

---

## 6. WeakReference Cache
**Đề bài chi tiết:** Tạo Cache sử dụng `WeakReference`. GC sẽ tự động dọn Cache entry khi Memory áp lực.
**Phân tích thuật toán:** `WeakReference<V>` cho phép GC thu gom Object `V` khi không còn Strong Reference nào khác trỏ tới. Cache tự co lại (Shrink) khi Heap áp lực, tự mở rộng khi Heap thoải mái. Tuy nhiên, Cache Miss rate không thể dự đoán.
**Mã nguồn Java:**
```java
import java.lang.ref.WeakReference;
import java.util.HashMap;
import java.util.Map;
public class WeakRefCache {
    private Map<String, WeakReference<byte[]>> cache = new HashMap<>();

    public void put(String key, byte[] value) {
        cache.put(key, new WeakReference<>(value));
    }

    public byte[] get(String key) {
        WeakReference<byte[]> ref = cache.get(key);
        if (ref == null) return null;
        byte[] val = ref.get(); // Có thể null nếu GC đã dọn
        if (val == null) cache.remove(key); // Dọn dẹp entry
        return val;
    }

    public static void main(String[] args) {
        WeakRefCache c = new WeakRefCache();
        c.put("data", new byte[10_000_000]); // 10MB
        System.out.println("Before GC: " + (c.get("data") != null)); // true
        System.gc();
        System.out.println("After GC:  " + (c.get("data") != null)); // Có thể false
    }
}
```
**Độ phức tạp:** Space tự điều chỉnh. Get/Put $\mathcal{O}(1)$ amortized.

---

## 7. Object Allocation Rate Measurement
**Đề bài chi tiết:** Đo Allocation Rate (GB/s) của thuật toán bằng `Runtime` API.
**Phân tích thuật toán:** Allocation Rate = Tổng bytes cấp phát / Thời gian. Rate cao ($> 1\text{GB/s}$) → Minor GC chạy rất thường xuyên → Pause time tích lũy. Giảm Rate bằng cách: Dùng Primitive, Pre-allocate, Object Pooling.
**Mã nguồn Java:**
```java
public class AllocationRateMeasure {
    public static void main(String[] args) {
        Runtime rt = Runtime.getRuntime();
        rt.gc();
        long startUsed = rt.totalMemory() - rt.freeMemory();
        long startTime = System.nanoTime();

        // Thuật toán tạo nhiều Object
        for (int i = 0; i < 10_000_000; i++) {
            String s = "item-" + i; // Tạo String Object mới mỗi lần
        }

        long endTime = System.nanoTime();
        long endUsed = rt.totalMemory() - rt.freeMemory();
        double seconds = (endTime - startTime) / 1e9;
        long allocated = endUsed - startUsed; // Ước lượng (GC có thể can thiệp)

        System.out.printf("Estimated allocation: %,d bytes in %.2f sec%n", allocated, seconds);
        System.out.printf("Rate: %.2f MB/s%n", allocated / seconds / 1_000_000);
    }
}
```
**Độ phức tạp:** Đo lường, không phải thuật toán.

---

## 8. Generational GC Simulation
**Đề bài chi tiết:** Tạo code mô phỏng vòng đời Object: 95% chết ngay (Young), 5% sống lâu (Old). Quan sát GC behavior.
**Phân tích thuật toán:** Giả thuyết Generational Hypothesis: Hầu hết Object chết trẻ. Code tạo Object tạm bên trong vòng lặp (chết ngay khi ra khỏi scope) và giữ một số ít trong List lâu dài.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class GenerationalSimulation {
    public static void main(String[] args) {
        // java -verbose:gc -Xmx256m GenerationalSimulation
        List<byte[]> longLived = new ArrayList<>();

        for (int i = 0; i < 1_000_000; i++) {
            // 95% Object chết ngay (Young Gen → Minor GC dọn)
            byte[] shortLived = new byte[1000];

            // 5% Object sống lâu (Promoted → Old Gen)
            if (i % 20 == 0) {
                longLived.add(new byte[1000]);
            }

            // Giới hạn Long-lived để không OOM
            if (longLived.size() > 10000) {
                longLived.remove(0);
            }
        }
        System.out.println("Long-lived objects: " + longLived.size());
    }
}
```
**Độ phức tạp:** Minor GC thường xuyên (Nhanh). Major GC hiếm.

---

## 9. Finalize() Trap
**Đề bài chi tiết:** Sử dụng `finalize()` và chứng minh nó delay GC thêm 1 cycle.
**Phân tích thuật toán:** Khi Object có `finalize()`, GC KHÔNG dọn ngay ở Minor GC đầu tiên. Thay vào đó, Object bị đưa vào Finalization Queue → Finalizer Thread gọi `finalize()` → Object mới thực sự eligible cho GC ở cycle TIẾP THEO. Hệ quả: Object sống thêm ít nhất 1 GC cycle, có thể bị promote vào Old Gen → Tốn kém hơn nhiều.
**Mã nguồn Java:**
```java
public class FinalizeTrap {
    @Override
    @SuppressWarnings("deprecation")
    protected void finalize() throws Throwable {
        System.out.println("finalize() called for: " + this);
        // Object CÓ THỂ tự hồi sinh:
        // SomeStaticField = this; → Object trở lại Reachable!
        // Nhưng finalize() CHỈ được gọi 1 lần duy nhất.
    }

    public static void main(String[] args) throws InterruptedException {
        FinalizeTrap obj = new FinalizeTrap();
        obj = null;
        System.gc();
        Thread.sleep(1000); // Đợi Finalizer Thread
        System.gc(); // Lần 2 mới thực sự dọn xong
        System.out.println("Done. finalize() is DEPRECATED since Java 9.");
    }
}
```
**Độ phức tạp:** Delay GC 1+ cycles. NEVER use in Production.

---

## 10. Epsilon GC — No GC Benchmark
**Đề bài chi tiết:** Chạy thuật toán với Epsilon GC (Không bao giờ GC). Đo thời gian thuần túy không bị GC can thiệp.
**Phân tích thuật toán:** Epsilon GC (`-XX:+UseEpsilonGC`, JDK 11+) KHÔNG thu gom rác. Khi Heap đầy → `OutOfMemoryError`. Dùng cho: (1) Micro-benchmark chính xác (Loại bỏ GC noise), (2) Ứng dụng cực ngắn hạn (AWS Lambda <1s), (3) Test Allocation Rate.
**Mã nguồn Java:**
```java
public class EpsilonGCBenchmark {
    // Chạy: java -XX:+UnlockExperimentalVMOptions -XX:+UseEpsilonGC 
    //       -Xmx1g EpsilonGCBenchmark
    public static void main(String[] args) {
        long start = System.nanoTime();
        long sum = 0;
        for (int i = 0; i < 100_000_000; i++) {
            sum += i;
        }
        long elapsed = System.nanoTime() - start;
        System.out.printf("Sum: %d, Time: %d ms (Zero GC interference)%n",
                sum, elapsed / 1_000_000);
    }
}
```
**Độ phức tạp:** Zero GC overhead. Pure algorithm time.

---

## 11-30. Tóm lược phân tích các bài tập GC còn lại

- **Bài 11 (G1GC Region Visualization):** Dùng `-Xlog:gc*` quan sát Region allocation. Eden → Survivor → Old. Humongous Object $\ge 50\%$ Region.
- **Bài 12 (String Deduplication):** Bật `-XX:+UseStringDeduplication` (G1 only). Tạo 1M chuỗi trùng lặp. Đo Heap saving (~10-30%).
- **Bài 13 (SoftReference vs WeakReference):** Soft bị GC CHỈ KHI Heap sắp đầy. Weak bị GC ngay Minor GC tiếp theo. Benchmark cả 2.
- **Bài 14 (PhantomReference + ReferenceQueue):** Thay thế `finalize()`. Đăng ký cleanup handler qua PhantomReference. Dọn Native resource khi Object bị GC.
- **Bài 15 (Object Pool Pattern):** Tạo Pool 100 Objects. Checkout/Checkin thay vì new/GC. Đo Allocation Rate giảm bao nhiêu.
- **Bài 16 (Young Gen Sizing):** `-Xmn` set Young Gen size. Quá nhỏ → Minor GC quá thường xuyên. Quá lớn → Minor GC pause dài. Tìm Sweet spot.
- **Bài 17 (Premature Promotion):** Survivor quá nhỏ → Object bị promote trước tuổi → Old Gen đầy nhanh → Full GC. Fix: `-XX:SurvivorRatio`, `-XX:MaxTenuringThreshold`.
- **Bài 18 (GC Log Parsing):** Parse file `gc.log` bằng Java. Tính: Total pause time, Max pause, Average pause, Throughput %.
- **Bài 19 (Direct ByteBuffer và GC):** `ByteBuffer.allocateDirect()` nằm ngoài Heap. GC KHÔNG quản lý trực tiếp. Phải `System.gc()` để trigger Cleaner.
- **Bài 20 (Concurrent Marking — G1 IHOP):** `-XX:InitiatingHeapOccupancyPercent=45`. G1 bắt đầu Concurrent Marking khi Old Gen đạt 45%. Tăng IHOP → Delay GC → Rủi ro Allocation Stall.
- **Bài 21 (GC và Thread Safepoint):** Counted loop `for(int i=0; i<N; i++)` KHÔNG có safepoint ở JDK cũ → GC bị block. Fix: `-XX:+UseCountedLoopSafepoints`.
- **Bài 22 (ZGC Pause Measurement):** Chạy với `-XX:+UseZGC -Xlog:gc*`. Tạo 10GB rác. Quan sát Max Pause < 1ms.
- **Bài 23 (Allocation Stall Simulation):** Tạo 100 Threads cùng allocate nhanh → Eden đầy → Threads bị block chờ GC → Stall. Giải pháp: Tăng Eden hoặc giảm Allocation Rate.
- **Bài 24 (Class Unloading):** Tạo Custom ClassLoader, nạp Class, rồi gán ClassLoader = null. GC dọn Class CHỈ KHI ClassLoader bị dọn.
- **Bài 25 (GC-Free Algorithm Design):** Viết thuật toán Merge Sort KHÔNG tạo mảng tạm mới mỗi lần merge. Pre-allocate 1 mảng buffer duy nhất.
- **Bài 26 (JFR GC Event Analysis):** Dùng JFR ghi GC events. Mở bằng JDK Mission Control. Phân tích Allocation Profiling.
- **Bài 27 (Humongous Object Avoidance):** Object $\ge 50\%$ G1 Region → Humongous → Bypass Young Gen. Chia nhỏ Object hoặc tăng Region size (`-XX:G1HeapRegionSize`).
- **Bài 28 (Metaspace GC):** Tạo Dynamic Proxy liên tục → Class metadata phình to → Metaspace Full → FullGC. Fix: `-XX:MaxMetaspaceSize`.
- **Bài 29 (Card Table và Remembered Set):** Old Gen object reference Young Gen object. GC cần Card Table để tránh scan toàn bộ Old Gen. Dirty Card → Scan chỉ vùng dirty.
- **Bài 30 (Comparing GC Algorithms):** Benchmark cùng 1 workload trên 4 GC: Serial, Parallel, G1, ZGC. So sánh: Throughput, Max Pause, Avg Pause, Footprint.
