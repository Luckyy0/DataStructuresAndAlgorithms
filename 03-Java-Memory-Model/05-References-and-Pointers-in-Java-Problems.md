# Companion Problems: 05-References-and-Pointers-in-Java

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về hệ thống Reference trong Java: Strong, Soft, Weak, Phantom Reference, ReferenceQueue, và Cleaner API.

---

## 1. Strong Reference — Object Lifetime
**Đề bài chi tiết:** Tạo Object, gán vào biến, gán `null`, gọi `System.gc()`. Xác minh Object bị GC sau khi mất Strong Reference.
**Phân tích thuật toán:** Khi biến Strong Reference bị gán `null`, Object trở thành Unreachable từ mọi GC Root. GC sẽ dọn Object ở GC cycle tiếp theo. Dùng `finalize()` (chỉ để demo) để xác minh Object bị GC.
**Mã nguồn Java:**
```java
public class StrongRefLifetime {
    @Override
    @SuppressWarnings("deprecation")
    protected void finalize() {
        System.out.println("Object finalized! (GC collected it)");
    }

    public static void main(String[] args) throws InterruptedException {
        StrongRefLifetime obj = new StrongRefLifetime();
        System.out.println("Object created: " + obj);

        obj = null; // Mất Strong Reference → Eligible cho GC
        System.gc();
        Thread.sleep(500); // Đợi Finalizer Thread
        System.out.println("After GC: Object should be collected");
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$.

---

## 2. Weak Reference — Immediate GC
**Đề bài chi tiết:** Tạo Object chỉ được giữ bởi `WeakReference`. Gọi `System.gc()`. Xác minh `get()` trả về `null`.
**Phân tích thuật toán:** Weak Reference KHÔNG ngăn GC thu gom Object. Ngay Minor GC tiếp theo, nếu Object chỉ có Weak Reference (không có Strong/Soft Reference nào), Object bị dọn và `WeakReference.get()` trả về `null`.
**Mã nguồn Java:**
```java
import java.lang.ref.WeakReference;
public class WeakRefImmediate {
    public static void main(String[] args) throws InterruptedException {
        Object strongRef = new Object();
        WeakReference<Object> weakRef = new WeakReference<>(strongRef);

        System.out.println("Before null: " + weakRef.get()); // Object@xxx

        strongRef = null; // Mất Strong Ref → Chỉ còn Weak Ref
        System.gc();
        Thread.sleep(200);

        System.out.println("After GC:    " + weakRef.get()); // null (Đã bị dọn)
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$. GC dọn ngay.

---

## 3. Soft Reference — Memory Pressure Cache
**Đề bài chi tiết:** Tạo Soft Reference tới mảng 50MB. Cấp phát thêm RAM cho đến khi Heap áp lực. Xác minh GC xóa Soft Ref.
**Phân tích thuật toán:** Soft Reference tồn tại lâu hơn Weak. GC CHỈ dọn khi Heap sắp đầy (Low Memory). Khi Heap thoải mái, `get()` trả về Object bình thường. Khi Heap áp lực, GC dọn Soft Ref để giải phóng RAM.
**Mã nguồn Java:**
```java
import java.lang.ref.SoftReference;
import java.util.ArrayList;
import java.util.List;
public class SoftRefPressure {
    public static void main(String[] args) {
        // java -Xmx128m SoftRefPressure
        SoftReference<byte[]> softCache = new SoftReference<>(new byte[50_000_000]); // 50MB

        System.out.println("Before pressure: " + (softCache.get() != null)); // true

        // Tạo áp lực Heap
        List<byte[]> pressure = new ArrayList<>();
        try {
            while (true) {
                pressure.add(new byte[5_000_000]); // 5MB mỗi lần
                if (softCache.get() == null) {
                    System.out.println("Soft Ref cleared after allocating " +
                            pressure.size() * 5 + " MB");
                    break;
                }
            }
        } catch (OutOfMemoryError e) {
            System.out.println("OOM! Soft ref status: " + (softCache.get() != null));
        }
    }
}
```
**Độ phức tạp:** GC dọn Soft Ref khi Heap $> 90\%$.

---

## 4. Phantom Reference + ReferenceQueue
**Đề bài chi tiết:** Dùng Phantom Reference để nhận thông báo khi Object bị GC. In thông báo cleanup từ Background Thread.
**Phân tích thuật toán:** Phantom Reference `get()` LUÔN null. Nó chỉ dùng để nhận notification qua `ReferenceQueue`. Khi Object bị finalize xong, Phantom Ref được enqueue. Background Thread poll Queue và thực hiện cleanup (Giải phóng Native resource, đóng file handle, v.v.).
**Mã nguồn Java:**
```java
import java.lang.ref.PhantomReference;
import java.lang.ref.Reference;
import java.lang.ref.ReferenceQueue;
public class PhantomRefDemo {
    public static void main(String[] args) throws InterruptedException {
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        Object target = new Object();
        PhantomReference<Object> phantom = new PhantomReference<>(target, queue);

        System.out.println("phantom.get(): " + phantom.get()); // LUÔN null

        // Background cleanup Thread
        Thread cleaner = new Thread(() -> {
            try {
                Reference<?> ref = queue.remove(); // Blocking wait
                System.out.println("Received notification: Object was GC'd!");
                ref.clear(); // Dọn dẹp Phantom Reference
            } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
        });
        cleaner.setDaemon(true);
        cleaner.start();

        target = null; // Mất Strong Ref
        System.gc();
        Thread.sleep(1000);
    }
}
```
**Độ phức tạp:** $\mathcal{O}(1)$ notification.

---

## 5. WeakHashMap — Auto Cleanup
**Đề bài chi tiết:** Sử dụng `WeakHashMap`. Tạo Key, put Entry, gán Key = null. Xác minh Entry tự biến mất sau GC.
**Phân tích thuật toán:** `WeakHashMap` giữ Key bằng `WeakReference`. Khi Key mất Strong Reference từ bên ngoài → Key bị GC → Entry bị xóa tự động (Stale entry expunge). Map tự co lại mà không cần lập trình viên xóa thủ công.
**Mã nguồn Java:**
```java
import java.util.WeakHashMap;
public class WeakHashMapDemo {
    public static void main(String[] args) throws InterruptedException {
        WeakHashMap<Object, String> map = new WeakHashMap<>();

        Object key1 = new Object();
        Object key2 = new Object();
        map.put(key1, "Value 1");
        map.put(key2, "Value 2");

        System.out.println("Before: size = " + map.size()); // 2

        key1 = null; // Mất Strong Ref tới key1
        System.gc();
        Thread.sleep(200);

        System.out.println("After GC: size = " + map.size()); // 1 (key1 entry bị xóa)
        System.out.println("key2 still: " + map.containsValue("Value 2")); // true
    }
}
```
**Độ phức tạp:** Auto-cleanup $\mathcal{O}(1)$ amortized per operation.

---

## 6. WeakHashMap Memory Leak Trap
**Đề bài chi tiết:** Minh họa Memory Leak khi Value giữ Strong Reference ngược lại Key trong WeakHashMap.
**Phân tích thuật toán:** Nếu Value chứa Strong Reference tới Key, thì Key luôn Strongly Reachable (qua Value → Key). WeakReference cho Key bị "vô hiệu hóa" vì GC thấy Key vẫn Strongly Reachable. Entry KHÔNG BAO GIỜ bị xóa → Memory Leak.
**Mã nguồn Java:**
```java
import java.util.WeakHashMap;
public class WeakHashMapLeak {
    public static void main(String[] args) throws InterruptedException {
        WeakHashMap<Object, Object> map = new WeakHashMap<>();
        Object key = new Object();

        // BUG: Value chứa Reference ngược lại Key!
        Object[] value = new Object[]{ key, "data" };
        map.put(key, value);

        key = null; // Tưởng Key bị GC, nhưng Value[0] giữ Strong Ref tới Key!
        System.gc();
        Thread.sleep(200);

        System.out.println("Size: " + map.size()); // VẪN = 1 (LEAK!)
        // Fix: Đảm bảo Value KHÔNG reference Key
    }
}
```
**Độ phức tạp:** Memory Leak vĩnh viễn.

---

## 7. SoftReference LRU Cache
**Đề bài chi tiết:** Xây dựng Cache dùng `SoftReference` kết hợp `LinkedHashMap` (LRU). GC tự co Cache khi Heap áp lực.
**Phân tích thuật toán:** `LinkedHashMap` với `accessOrder = true` tự sắp xếp theo thời gian truy cập (LRU). Kết hợp với `SoftReference`: Entry cũ bị GC dọn trước nhờ `SoftRefLRUPolicyMSPerMB`. Khi `get()` trả về null, xóa Entry khỏi Map.
**Mã nguồn Java:**
```java
import java.lang.ref.SoftReference;
import java.util.LinkedHashMap;
import java.util.Map;
public class SoftLRUCache<K, V> {
    private final Map<K, SoftReference<V>> map;

    public SoftLRUCache(int maxSize) {
        map = new LinkedHashMap<>(maxSize, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<K, SoftReference<V>> eldest) {
                return size() > maxSize;
            }
        };
    }

    public synchronized V get(K key) {
        SoftReference<V> ref = map.get(key);
        if (ref == null) return null;
        V value = ref.get();
        if (value == null) map.remove(key); // Dọn dead entry
        return value;
    }

    public synchronized void put(K key, V value) {
        map.put(key, new SoftReference<>(value));
    }
}
```
**Độ phức tạp:** Get/Put $\mathcal{O}(1)$. Space tự điều chỉnh.

---

## 8. Cleaner API — Modern Resource Cleanup
**Đề bài chi tiết:** Tạo class quản lý Native buffer bằng `Cleaner` API (Java 9+). Implement `AutoCloseable` cho deterministic cleanup.
**Phân tích thuật toán:** `Cleaner` sử dụng Phantom Reference nội bộ. Khi Object bị GC, Cleaner action được gọi trên Cleaner Thread. QUAN TRỌNG: Cleaner action (Runnable) KHÔNG ĐƯỢC reference `this` (Object gốc) — nếu có, Object sẽ KHÔNG BAO GIỜ bị GC (Strong Ref vòng).
**Mã nguồn Java:**
```java
import java.lang.ref.Cleaner;
public class NativeBuffer implements AutoCloseable {
    private static final Cleaner CLEANER = Cleaner.create();
    private final Cleaner.Cleanable cleanable;
    private final long size;

    // Cleanup action: STATIC class, KHÔNG reference `this`!
    private static class CleanupAction implements Runnable {
        private final long bufferAddress;
        CleanupAction(long addr) { this.bufferAddress = addr; }
        @Override
        public void run() {
            System.out.println("Cleaning up native buffer at " + bufferAddress);
            // Unsafe.freeMemory(bufferAddress); // Thực tế
        }
    }

    public NativeBuffer(long size) {
        this.size = size;
        long addr = allocateNative(size);
        this.cleanable = CLEANER.register(this, new CleanupAction(addr));
    }

    @Override
    public void close() {
        cleanable.clean(); // Deterministic cleanup
    }

    private static long allocateNative(long size) { return System.nanoTime(); } // Stub
}
```
**Độ phức tạp:** Cleanup $\mathcal{O}(1)$.

---

## 9. ReferenceQueue Polling
**Đề bài chi tiết:** Tạo 1000 WeakReferences với ReferenceQueue. Force GC. Poll Queue và đếm số References bị enqueue.
**Phân tích thuật toán:** Sau GC, tất cả WeakReferences tới Object không có Strong Ref bị enqueue vào ReferenceQueue. `queue.poll()` trả về Reference hoặc `null` (Non-blocking). `queue.remove()` blocking.
**Mã nguồn Java:**
```java
import java.lang.ref.Reference;
import java.lang.ref.ReferenceQueue;
import java.lang.ref.WeakReference;
public class RefQueuePolling {
    public static void main(String[] args) throws InterruptedException {
        ReferenceQueue<Object> queue = new ReferenceQueue<>();
        for (int i = 0; i < 1000; i++) {
            new WeakReference<>(new Object(), queue);
            // Object không có Strong Ref → Eligible ngay
        }

        System.gc();
        Thread.sleep(500);

        int count = 0;
        while (queue.poll() != null) count++;
        System.out.println("References enqueued: " + count); // ~1000
    }
}
```
**Độ phức tạp:** Poll $\mathcal{O}(1)$ per Reference.

---

## 10. Listener Memory Leak Fix
**Đề bài chi tiết:** Minh họa Listener Memory Leak (Strong Ref) và Fix bằng WeakReference.
**Phân tích thuật toán:** Event source giữ Strong Reference tới Listener. Khi Listener owner bị destroy nhưng quên unregister → Listener Object sống mãi → Memory Leak. Fix: Event source giữ WeakReference tới Listener. Khi Owner bị GC → Listener bị GC → Tự cleanup.
**Mã nguồn Java:**
```java
import java.lang.ref.WeakReference;
import java.util.*;
public class WeakListenerDemo {
    interface Listener { void onEvent(String data); }

    static class EventSource {
        // Fix: Dùng WeakReference thay vì Strong
        private final List<WeakReference<Listener>> listeners = new ArrayList<>();

        void addListener(Listener l) {
            listeners.add(new WeakReference<>(l));
        }

        void fireEvent(String data) {
            Iterator<WeakReference<Listener>> it = listeners.iterator();
            while (it.hasNext()) {
                Listener l = it.next().get();
                if (l == null) {
                    it.remove(); // Dọn dead listener
                } else {
                    l.onEvent(data);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        EventSource source = new EventSource();
        Listener listener = data -> System.out.println("Got: " + data);
        source.addListener(listener);
        source.fireEvent("Hello"); // Got: Hello

        listener = null; // Listener owner bị destroy
        System.gc();
        Thread.sleep(200);

        source.fireEvent("World"); // Không in gì (Listener đã bị GC)
        System.out.println("Active listeners: 0 (auto-cleaned)");
    }
}
```
**Độ phức tạp:** Auto-cleanup, zero Memory Leak.

---

## 11-30. Tóm lược phân tích các bài tập còn lại

- **Bài 11 (Strong vs Soft Benchmark):** Cache 100 images (10MB mỗi ảnh). Strong: OOM tại image 12. Soft: Tự evict, chạy được hàng nghìn lần.
- **Bài 12 (WeakHashMap Thread Safety):** `Collections.synchronizedMap(new WeakHashMap<>())` hoặc dùng `ConcurrentHashMap<K, WeakReference<V>>`. Benchmark contention.
- **Bài 13 (Finalize Resurrection):** Object trong `finalize()` gán `this` vào static field → Hồi sinh. GC chỉ gọi finalize 1 lần. Lần 2 Object bị dọn vĩnh viễn.
- **Bài 14 (SoftRefLRUPolicy Tuning):** Chạy với `-XX:SoftRefLRUPolicyMSPerMB=0` (Dọn ngay) vs `=10000` (Giữ 10s/MB Free). Đo Cache Hit Rate.
- **Bài 15 (Phantom Ref Native Cleanup):** Quản lý DirectByteBuffer: Allocate native → Wrap trong Phantom Ref → Poll queue → Free native. Tương tự cách `DirectByteBuffer` thực tế hoạt động trong JDK.
- **Bài 16 (Reference Counting vs Reachability):** Implement Reference Counting bằng Java (không dùng GC). So sánh: RC không xử lý Circular Ref. Mark-and-Sweep xử lý được.
- **Bài 17 (Intern String + WeakHashMap):** Implement custom String Interning bằng WeakHashMap. Deduplicate String tự động.
- **Bài 18 (Soft Cache Stampede Protection):** Khi GC dọn Soft Cache → N threads cùng miss → N lần recompute. Fix: Singly-flight (Chỉ 1 thread recompute, N-1 threads đợi).
- **Bài 19 (ClassLoader Leak Detection):** Custom ClassLoader giữ Strong Ref tới Classes → Classes giữ Ref tới ClassLoader → Circular → Cả 2 không bị GC. Detect bằng Heap Dump.
- **Bài 20 (ThreadLocal as WeakReference):** ThreadLocal internal dùng WeakReference cho Key (ThreadLocal object). Value vẫn Strong. Khi ThreadLocal bị GC → Key null → Entry stale → Cleanup khi Thread gọi get()/set().
- **Bài 21 (Ephemeron — Weak Key Strong Value):** WeakHashMap model. Key Weak, Value Strong. Khi Key bị GC → Value cũng bị dọn (Nếu không có Ref khác).
- **Bài 22 (Double-checked Soft Cache):** Thread-safe Soft Cache với volatile + double-checked locking cho cache miss handling.
- **Bài 23 (Phantom Ref Ordered Cleanup):** Sắp xếp cleanup theo dependency order. Object A phụ thuộc B → Cleanup B trước A.
- **Bài 24 (Reference Processing Impact on GC Pause):** Nhiều Soft/Weak/Phantom References → GC Phase "Reference Processing" tốn thêm thời gian. `-XX:+ParallelRefProcEnabled` giảm impact.
- **Bài 25 (Concurrent Soft Cache with Caffeine):** So sánh tự viết SoftReference Cache vs Caffeine `softValues()`. Caffeine xử lý: Eviction, Statistics, Async loading.
- **Bài 26 (Memory-Sensitive Data Structure):** Implement SkipList mà Node dùng SoftReference cho Value. Khi Memory áp lực, giá trị bị evict nhưng cấu trúc SkipList vẫn nguyên vẹn.
- **Bài 27 (WeakReference trong Observable Pattern):** RxJava/Reactor Subscriber leak. Fix: `takeUntil(lifecycle)` hoặc WeakReference subscriber.
- **Bài 28 (Off-Heap Cache với Phantom Ref cleanup):** Cache data trong DirectByteBuffer (Off-Heap). Phantom Ref → Queue → Free native memory. Tránh GC overhead hoàn toàn.
- **Bài 29 (Reachability Fence):** `Reference.reachabilityFence(this)` ngăn JIT tối ưu hóa sớm khiến Object bị GC trước khi method kết thúc. Use case: JNI native call.
- **Bài 30 (Full Reference System Benchmark):** Tạo 1M objects cho mỗi loại Reference. Force GC. Đo: Time to collect, ReferenceQueue throughput, Memory overhead per Reference type.
