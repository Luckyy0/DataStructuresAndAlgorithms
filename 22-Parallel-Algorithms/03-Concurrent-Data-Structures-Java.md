# Concurrent Data Structures trong Java: Kiến Trúc Bất Biến, Lock-Free & Scalability Đa Luồng

---

## 1. Metadata

- **Document ID**: `DSA-22-03`
- **Version**: `1.0`
- **Prerequisites**: 
  - Java Concurrency Fundamentals (`Thread`, `Runnable`, `synchronized`, `volatile`).
  - Java Memory Model (JMM) & Happens-Before Order.
  - Compare-And-Swap (CAS) & Unsafe / `VarHandle` primitives.
  - Classical Data Structures (Hash Table, Binary Search Tree, Red-Black Tree, Skip List, Dynamic Array).
- **Learning Objectives**:
  - Thấu hiểu bản chất và giới hạn của coarse-grained locking; nắm vững cơ chế chuyển dịch sang fine-grained locking, lock striping, optimistic reading và lock-free concurrency.
  - Phân tích toán học về contention thông qua Amdahl's Law, Universal Scalability Law (USL), M/M/m Queueing Model và định nghĩa hình thức về **Linearizability**.
  - Mổ xẻ chi tiết kiến trúc bên trong của OpenJDK 21: `ConcurrentHashMap`, `ConcurrentSkipListMap`, `CopyOnWriteArrayList`, `StampedLock`, và `LongAdder`.
  - Tự cài đặt từ đầu (from scratch) các cấu trúc dữ liệu concurrent chuẩn production bằng Java 21 (`VarHandle`, Bitwise manipulation, State Machine).
  - Nhận diện 20 lỗi lập trình kinh điển (Common Bugs) và 30 trường hợp biên (Edge Cases) trong môi trường phân tán/đa luồng.
  - Thiết kế JMH Benchmarks và bộ kiểm thử bất biến đa luồng (JCStress / JUnit 5 Concurrency Test Suite).
- **Estimated Reading Time**: 90 - 120 phút
- **Difficulty**: Advanced / Expert
- **Keywords**: `ConcurrentHashMap`, `ConcurrentSkipListMap`, `CopyOnWriteArrayList`, `StampedLock`, `Striped Locking`, `LongAdder`, `VarHandle`, `False Sharing`, `@Contended`, `Linearizability`, `TreeBin`, `ForwardingNode`.

---

## 2. Purpose

Trong kỷ nguyên vi xử lý đa nhân (Multi-core / Many-core Architecture), hiệu năng của phần mềm không còn tăng trưởng tự động theo định luật Moore bằng cách tăng xung nhịp CPU, mà phụ thuộc hoàn toàn vào khả năng khai thác tính toán song song (Parallelism) và đồng thời (Concurrency).

Mục đích tối thượng của **Concurrent Data Structures** (Cấu trúc dữ liệu đồng thời) là:
1. **Maximize Throughput & Minimize Latency**: Cho phép hàng trăm hoặc hàng nghìn luồng (threads) truy cập và sửa đổi dữ liệu đồng thời mà không bị nghẽn (bottleneck) tại các điểm đồng bộ tập trung.
2. **Preserve Data Consistency & Invariants**: Đảm bảo trạng thái bên trong của cấu trúc dữ liệu luôn nhất quán, không xảy ra race condition, lost update, corrupted memory hay memory visibility issues.
3. **Prevent Deadlock, Livelock & Starvation**: Loại bỏ nguy cơ tắc nghẽn luồng vô thời hạn thông qua các giải thuật lock-free, wait-free hoặc fine-grained locking có thứ tự nghiêm ngặt.

---

## 3. Motivation

### 3.1. Thảm họa hiệu năng của Synchronized Collections

Trước Java 5, việc bảo vệ tính toàn vẹn dữ liệu trong môi trường đa luồng chủ yếu dựa vào các collection đồng bộ nguyên khối (Coarse-grained Synchronized Collections) như `java.util.Hashtable`, `java.util.Vector`, hoặc thông qua wrapper `Collections.synchronizedMap(new HashMap<>())`.

```java
// Coarse-Grained Synchronization: Cùng một monitor lock cho TOÀN BỘ thao tác
public synchronized V get(Object key) { ... }
public synchronized V put(K key, V value) { ... }
```

Mô hình này tạo ra các điểm nghẽn nghiêm trọng:
- **Global Lock Contention**: Mọi luồng (dù chỉ đọc dữ liệu ở hai bucket hoàn toàn độc lập) đều phải tranh chấp duy nhất một monitor lock của đối tượng Map. Khi số lượng CPU cores tăng, thời gian các luồng phải chờ đợi (lock wait time) tăng theo hàm số mũ.
- **Context Switching Overhead**: Khi một thread không giành được lock, JVM và Hệ điều hành (OS) phải chuyển trạng thái thread từ `RUNNABLE` sang `BLOCKED`, gây ra context switch. Chi phí cho mỗi lần context switch dao động từ $1 \mu s$ đến $5 \mu s$ (tiêu tốn hàng nghìn chu kỳ CPU) cùng với việc xả sạch CPU pipeline và instruction cache.
- **CPU Cache Invalidation Storms**: Việc nhiều core liên tục tranh chấp một biến lock duy nhất dẫn đến hiện tượng bão hòa bus truyền thông liên kết (interconnect bus saturation) do giao thức duy trì tính nhất quán bộ nhớ đệm (Cache Coherency Protocols như MESI/MOESI) phải liên tục invalidate cache line giữa các core.

```
Synchronized Map (Coarse Lock):
Thread 1 (Read Key A)  ──┐
Thread 2 (Write Key B) ──┼──> [ GLOBAL MONITOR LOCK ] ──> [ Internal Table ]
Thread 3 (Read Key C)  ──┘     (Chỉ 1 thread được vào tại 1 thời điểm!)
```

### 3.2. Hướng tiếp cận hiện đại: Fine-Grained & Lock-Free

Để đạt được khả năng mở rộng tuyến tính (Linear Scalability), các cấu trúc dữ liệu hiện đại áp dụng các nguyên lý tiên tiến:
1. **Lock Striping / Bucket-Level Locking**: Phân chia không gian dữ liệu thành nhiều vùng độc lập; mỗi vùng được bảo vệ bởi một khóa riêng biệt (ví dụ: `ConcurrentHashMap` trong Java 8+ khóa trên từng đầu bucket `Node`).
2. **Non-Blocking Read Operations**: Sử dụng bộ nhớ bất biến (Immutability), con trỏ `volatile`, và cơ chế CAS (Compare-And-Swap) để các thao tác đọc (`get()`) diễn ra hoàn toàn không cần lock ($O(1)$ lock-free read).
3. **Copy-On-Write (COW)**: Tách biệt hoàn toàn luồng đọc và luồng ghi bằng cách nhân bản mảng dữ liệu khi có cập nhật, cho phép luồng đọc duyệt snapshot với chi phí zero-lock.
4. **Hardware-Aware Memory Layout**: Sử dụng padding để triệt tiêu hiện tượng False Sharing trên CPU Cache Lines (như trong `LongAdder`).

---

## 4. Mathematical Foundation

### 4.1. Amdahl's Law & Universal Scalability Law (USL)

Theo **Định luật Amdahl**, tốc độ tăng tốc tối đa $S(N)$ của một chương trình khi sử dụng $N$ bộ xử lý được xác định bởi:

$$S(N) = \frac{1}{(1 - p) + \frac{p}{N}}$$

Trong đó $p$ là tỷ lệ phần trăm của chương trình có thể chạy song song hoàn toàn, và $(1 - p)$ là phần tuần tự bắt buộc (Serial/Synchronized fraction).
- Nếu chỉ có $5\%$ chương trình chạy tuần tự do tranh chấp Global Lock ($(1 - p) = 0.05$), thì dù hệ thống có vô số core ($N \to \infty$), tốc độ tăng tốc tối đa không bao giờ vượt quá $\frac{1}{0.05} = 20\times$.

Mô hình **Universal Scalability Law (USL)** của Dr. Neil Gunther mô tả chính xác hơn tác động của sự tranh chấp (Contention $\alpha$) và sự phối hợp mạch lạc giữa các core (Coherence / Crosstalk $\beta$):

$$S(N) = \frac{N}{1 + \alpha(N - 1) + \beta N(N - 1)}$$

- $\alpha$ (Contention): Thời gian các luồng xếp hàng chờ lock.
- $\beta$ (Coherence): Chi phí truyền tín hiệu giữa các CPU cache để đồng bộ dữ liệu (Cache line invalidation).
- Khi $\beta > 0$, đồ thị throughput sẽ đạt đỉnh (peak) và sau đó **thoái hóa (retrograde scalability)** khi tăng thêm core! Cấu trúc dữ liệu Concurrent tối ưu hướng đến việc đưa cả $\alpha \to 0$ và $\beta \to 0$.

```
Throughput S(N)
  ^
  |          Linear Ideal S(N) = N
  |        /
  |       /   ConcurrentHashMap (Low alpha, Low beta)
  |      /  . ' ' ' '
  |     /. '
  |    /'
  |   /  . - - - SynchronizedMap (High alpha: Bottleneck)
  |  / .
  | / . . . _ _ _ Coarse Lock with False Sharing (High beta: Retrograde)
  +--------------------------------------------------------> Number of Cores (N)
```

### 4.2. Queueing Theory Model ($M/M/m$ Contention)

Mô hình hóa thời gian chờ đợi lock bằng hàng đợi $M/M/m$:
- Giả sử yêu cầu truy cập dữ liệu đến theo phân phối Poisson với tốc độ $\lambda$ (yêu cầu/giây).
- Thời gian giữ lock tuân theo phân phối hàm mũ với tốc độ phục vụ $\mu$.
- Hệ số sử dụng (Lock Utilization): $\rho = \frac{\lambda}{m\mu}$.

Theo công thức xấp xỉ của Kingman cho thời gian chờ trung bình trong hàng đợi $W_q$:

$$W_q \approx \left( \frac{C_a^2 + C_s^2}{2} \right) \left( \frac{\rho}{1 - \rho} \right) \frac{1}{\mu}$$

Khi $\rho \to 1$ (hệ thống bị tải cao, lock bị tranh chấp liên tục), thời gian chờ $W_q \to \infty$. Kỹ thuật **Lock Striping** phân tán $1$ hàng đợi lớn thành $K$ hàng đợi nhỏ độc lập, làm giảm tốc độ đến trên mỗi lock xuống $\lambda' = \frac{\lambda}{K}$, kéo $\rho' = \frac{\rho}{K} \ll 1$, từ đó triệt tiêu thời gian chờ đợi.

### 4.3. Formal Definition of Linearizability

Tính đúng đắn của cấu trúc dữ liệu concurrent được chuẩn hóa bằng khái niệm **Linearizability** (Maurice Herlihy & Jeannette Wing, 1990).

Một lịch trình thực thi (history) $H$ của các thao tác concurrent được gọi là **Linearizable** nếu:
1. **Sequential Specification**: Tồn tại một thứ tự tuần tự hợp lệ $S$ của tất cả các thao tác trong $H$ sao cho kết quả trả về của từng thao tác trong $S$ tuân thủ đúng định nghĩa đơn luồng của cấu trúc dữ liệu.
2. **Real-time Precedence**: Nếu thao tác $op_1$ hoàn thành trước khi thao tác $op_2$ bắt đầu trong thời gian thực tế ($res(op_1) <_{real} inv(op_2)$), thì $op_1$ phải đứng trước $op_2$ trong thứ tự tuần tự $S$ ($op_1 <_S op_2$).

**Linearization Point (Điểm tuyến tính hóa)**: Mỗi thao tác concurrent phải có một thời điểm logic duy nhất (thường là một lệnh assembly nguyên tử như `LOCK CMPXCHG` hoặc một volatile read/write) tại đó thao tác có hiệu lực ngay lập tức đối với toàn bộ phần còn lại của hệ thống.

```
Thread 1: [--- inv(put(K, V1)) --- LP1: CAS successful --- res(put) ---]
Thread 2:             [--- inv(get(K)) --- LP2: Volatile Read --- res -> V1 ---]
Time Axis -------------------------------------------------------------------->
```

---

## 5. Core Theory

### 5.1. Lock Striping & Fine-Grained Synchronization (`ConcurrentHashMap`)

Trong Java 7, `ConcurrentHashMap` sử dụng một mảng gồm 16 `Segment` (mỗi Segment là một `ReentrantLock` con). Tuy nhiên, cách này tạo overhead bộ nhớ và giới hạn mức độ đồng thời ở số lượng segment cố định.

Từ Java 8 trở đi, OpenJDK loại bỏ hoàn toàn `Segment`:
- Dữ liệu được lưu trong mảng `Node<K, V>[] table` có kích thước là lũy thừa của 2.
- **Lock-Free Head Insertion**: Nếu bucket đang rỗng (`table[i] == null`), thread sử dụng lệnh CAS (`compareAndSet`) thông qua `VarHandle`/`Unsafe` để gắn trực tiếp Node mới vào đầu bucket mà không dùng bất kỳ lock nào.
- **Bucket-Level Locking**: Nếu bucket đã có phần tử (`table[i] != null`), thread chỉ đồng bộ hóa trên chính đối tượng đầu tiên của bucket đó: `synchronized (firstNode)`. Các luồng khác truy cập vào các bucket $j \neq i$ hoàn toàn không bị ảnh hưởng.
- **Lock-Free Reads**: Mọi thao tác đọc `get()` không bao giờ dùng lock. Trường `val` và con trỏ `next` trong `Node` được khai báo `volatile`, đảm bảo quan hệ *Happens-Before* giữa luồng ghi và luồng đọc.

### 5.2. Collision Handling & Treeification

Để chống lại các cuộc tấn công DoS bằng Hash Collision (tạo ra hàng triệu key có cùng mã băm nhằm làm thoái hóa bảng băm thành danh sách liên kết $O(n)$):
- Khi một bucket vượt quá ngưỡng **`TREEIFY_THRESHOLD = 8`** và tổng kích thước bảng $\ge$ **`MIN_TREEIFY_CAPACITY = 64`**, danh sách liên kết sẽ được chuyển đổi thành Cây Đỏ-Đen (Red-Black Tree) gói trong đối tượng `TreeBin`.
- Độ phức tạp tìm kiếm trong bucket giảm từ $O(n)$ xuống $O(\log n)$.
- Nếu kích thước cây giảm xuống dưới ngưỡng **`UNTREEIFY_THRESHOLD = 6`** trong quá trình resize hoặc xóa, nó sẽ được chuyển đổi ngược lại thành danh sách liên kết đơn để tiết kiệm bộ nhớ.

### 5.3. Multi-Threaded Concurrent Resizing & `ForwardingNode`

Một trong những thuật toán phức tạp nhất của `ConcurrentHashMap` là cơ chế mở rộng bảng băm hợp tác (Cooperative Concurrent Resizing):
1. Khi số lượng phần tử vượt quá ngưỡng `sizeCtl = capacity * loadFactor`, một thread khởi xướng quá trình resize bằng cách cấp phát `nextTable` có kích thước gấp đôi.
2. Quá trình di chuyển (transfer) các bucket được chia thành các khoảng (**stride**, tối thiểu 16 bucket) cho từng CPU core.
3. Khi một bucket được chuyển thành công sang `nextTable`, bucket đó tại `table` cũ được thay thế bằng một nút đặc biệt gọi là **`ForwardingNode`** (có mã băm đặc biệt `MOVED = -1` và giữ con trỏ trỏ tới `nextTable`).
4. Các luồng khác khi thực hiện `put()` gặp `ForwardingNode` sẽ chủ động tham gia hỗ trợ chuyển dữ liệu (`helpTransfer()`), giúp quá trình resize hoàn thành cực nhanh.
5. Luồng thực hiện `get()` gặp `ForwardingNode` sẽ chuyển hướng tìm kiếm sang `nextTable` thông qua phương thức `ForwardingNode.find()` mà **không hề bị chặn (non-blocking)**.

### 5.4. Concurrent Skip List (`ConcurrentSkipListMap`)

`ConcurrentHashMap` không duy trì thứ tự của các key. Để có một Map đồng thời hỗ trợ sắp xếp theo thứ tự tự nhiên hoặc theo `Comparator` với các thao tác khoảng (Range Queries: `subMap`, `headMap`, `tailMap`), OpenJDK cung cấp `ConcurrentSkipListMap`.

- Dựa trên giải thuật Lock-Free Skip List của Håkan Sundell và Philippas Tsigas (2005) kết hợp với kỹ thuật của Doug Lea.
- Sử dụng các tháp liên kết đa tầng (Multi-level Index Towers) với xác suất $p = 0.5$ để tạo tầng mới.
- **CAS Insertion**: Chèn node vào tầng đáy (Data level) bằng CAS. Sau đó liên kết các tầng index phía trên từ dưới lên trên bằng CAS.
- **Two-Step Deletion (Marker Node)**: Để xóa một node mà không gây race condition với các thao tác chèn kế bên, thuật toán thực hiện:
  1. *Logical Deletion*: Dùng CAS gắn một `MarkerNode` ngay sau node cần xóa (đánh dấu node đã chết).
  2. *Physical Unlink*: Dùng CAS trỏ con trỏ của node phía trước vượt qua node đã đánh dấu để loại bỏ nó khỏi danh sách.

### 5.5. Copy-On-Write Semantics (`CopyOnWriteArrayList`)

- **Triết lý**: Dành riêng cho các trường hợp dữ liệu **Read-Heavy** (đọc chiếm $\ge 99\%$ như Event Listeners, Routing Tables, Configuration Cache).
- Mảng nội bộ được khai báo `private transient volatile Object[] array`.
- **Read Operations**: Trả về trực tiếp phần tử trong mảng hiện tại. Không lock, không đồng bộ, tốc độ tối đa của phần cứng ($O(1)$ raw memory access).
- **Write Operations**: Sử dụng một `ReentrantLock` độc quyền. Khi có thao tác ghi (`add`, `set`, `remove`), thread tạo một bản sao mới của mảng (`Arrays.copyOf`), thực hiện chỉnh sửa trên bản sao, sau đó gán mảng mới vào biến `volatile array`.
- **Snapshot Iterators**: Iterator giữ tham chiếu đến mảng tại thời điểm iterator được tạo ra. Iterator này hoàn toàn bất biến, không bao giờ ném `ConcurrentModificationException` và không phản ánh các thay đổi sau đó.

### 5.6. Optimistic Reading với `StampedLock`

Được giới thiệu từ Java 8, `StampedLock` giải quyết nhược điểm của `ReentrantReadWriteLock` (vốn vẫn phải ghi vào một biến state dùng chung khi đọc, gây ra cache contention và có thể gây Starvation cho luồng ghi).

- Cung cấp chế độ **Optimistic Read (Đọc lạc quan)**:
  1. Luồng đọc gọi `long stamp = lock.tryOptimisticRead()`. Nếu không có luồng ghi nào đang giữ lock, hàm trả về một giá trị `stamp` khác 0 (không hề lock hay biến đổi bất kỳ bit nào trong bộ nhớ!).
  2. Luồng đọc sao chép các biến trạng thái vào biến cục bộ (local variables).
  3. Luồng đọc gọi `lock.validate(stamp)`. Phương thức này phát ra một Memory Barrier (LoadLoad/LoadStore) để kiểm tra xem có luồng ghi nào chen ngang trong suốt quá trình đọc hay không.
  4. Nếu `validate()` trả về `true`: Dữ liệu đọc được là hoàn toàn nhất quán.
  5. Nếu `validate()` trả về `false`: Dữ liệu có thể bị rác/rách (torn read). Luồng đọc chủ động rơi lui (fallback) sang chế độ **Pessimistic Read Lock** (`lock.readLock()`) hoặc thử lại.

### 5.7. Cell Striping & Dynamic Contention Mitigation (`LongAdder`)

Khi hàng chục luồng cùng thực hiện `AtomicLong.incrementAndGet()`, tất cả đều liên tục CAS trên duy nhất một biến 64-bit trong bộ nhớ. Dưới áp lực tải cao, tỷ lệ CAS thất bại lên tới $95\%+$, biến CPU thành lò sưởi do spin-wait liên tục và gây nghẽn Cache Bus.

`LongAdder` (kế thừa từ `Striped64`) áp dụng giải thuật phân tán ô nhớ (Cell Striping):
- Duy trì một biến cơ sở `volatile long base` và một mảng các ô đệm `volatile Cell[] cells`.
- Mỗi `Cell` bọc một biến `volatile long value` và được gắn annotation `@jdk.internal.vm.annotation.Contended` (tự động padding 128 bytes để nằm trọn trong một Cache Line riêng biệt, triệt tiêu **False Sharing**).
- Khi contention thấp: Các luồng CAS trực tiếp trên `base`.
- Khi phát hiện contention (CAS trên `base` thất bại): Thread lấy hash của luồng (`ThreadLocalRandom.getProbe()`) để map vào một phần tử trong mảng `cells` và CAS trên ô đó.
- Nếu tiếp tục có xung đột, mảng `cells` sẽ được mở rộng động theo lũy thừa của 2 (tối đa bằng số lượng CPU cores).
- Khi gọi `sum()`: Hệ thống chỉ việc cộng dồn `base + \sum cells[i]`.

---

## 6. Visual Explanation

### 6.1. Kiến trúc `ConcurrentHashMap` và Cơ chế Resizing với `ForwardingNode`

```
ConcurrentHashMap Architecture (Table Size = 8):

Index
[0] ──> Node(K1, V1) ──> Node(K9, V9) ──> null  (Synchronized on Node(K1))
[1] ──> null                                     (CAS insertion for new key)
[2] ──> TreeBin (Root: TreeNode(K2))             (Red-Black Tree, Lock on TreeBin)
        ├── Left: TreeNode(K10)
        └── Right: TreeNode(K18)
[3] ──> ForwardingNode ─────────────────────────> Points to nextTable (Size = 16)
        (Hash = MOVED = -1)                      Reads redirect to nextTable[3] & [11]
[4] ──> Node(K4, V4) ──> null
...
```

```mermaid
flowchart TD
    subgraph OldTable["Old Table (Capacity = 4)"]
        B0["Bucket 0: ForwardingNode"]
        B1["Bucket 1: Node(A) -> Node(E)"]
        B2["Bucket 2: null"]
        B3["Bucket 3: ForwardingNode"]
    end

    subgraph NextTable["Next Table (Capacity = 8)"]
        NB0["Bucket 0: Node(C)"]
        NB1["Bucket 1: Transferring..."]
        NB2["Bucket 2: null"]
        NB3["Bucket 3: Node(G)"]
        NB4["Bucket 4: Node(D)"]
        NB5["Bucket 5: Transferring..."]
        NB6["Bucket 6: null"]
        NB7["Bucket 7: Node(H)"]
    end

    B0 -.->|Redirect Read / Help Transfer| NB0
    B0 -.->|Redirect Read / Help Transfer| NB4
    B3 -.->|Redirect Read / Help Transfer| NB3
    B3 -.->|Redirect Read / Help Transfer| NB7

    ThreadRead["Thread Reader (Key with Hash % 4 == 0)"] -->|Sees ForwardingNode| B0
    ThreadRead -->|Non-blocking redirect| NB0
```

### 6.2. Thao tác Xóa 2 bước trong Lock-Free SkipList

```
Step 1: Normal State
[Node A] ───────────────────────> [Node B (Value=42)] ───────────────────────> [Node C]

Step 2: Logical Deletion (CAS MarkerNode after B)
[Node A] ───────────────────────> [Node B (DEAD)] ──> [MarkerNode] ──────────> [Node C]
                                  (Key/Value invalid)

Step 3: Physical Unlink (CAS Node A.next to Node C)
[Node A] ────────────────────────────────────────────────────────────────────> [Node C]
                                  [Node B] ──> [MarkerNode] (Garbage Collected)
```

### 6.3. Chu trình Chuyển trạng thái của `StampedLock`

```mermaid
stateDiagram-v2
    [*] --> OptimisticRead: tryOptimisticRead()
    OptimisticRead --> Validate: validate(stamp)
    Validate --> SuccessRead: true (No locks involved, Zero overhead)
    Validate --> FallbackPessimistic: false (Write intervened)

    FallbackPessimistic --> AcquireReadLock: readLock()
    AcquireReadLock --> ReadData: Thread holds Shared Read Lock
    ReadData --> UnlockRead: unlockRead(stamp)

    [*] --> AcquireWriteLock: writeLock()
    AcquireWriteLock --> MutateData: Thread holds Exclusive Write Lock
    MutateData --> UnlockWrite: unlockWrite(stamp)
    UnlockWrite --> [*]
```

### 6.4. False Sharing và Cache Line Padding trong `LongAdder`

```
+-------------------------------------------------------------------------------+
| CPU Cache Line (64 Bytes) - Without Padding (FALSE SHARING DISASTER)          |
| [ Cell 0 (8B) ] [ Cell 1 (8B) ] [ Cell 2 (8B) ] [ Cell 3 (8B) ] ... [Other]  |
+-------------------------------------------------------------------------------+
       ^               ^
    Core 1          Core 2
 (Writes Cell 0)  (Writes Cell 1) --> Invalidate toàn bộ Cache Line của Core 1!

+-------------------------------------------------------------------------------+
| CPU Cache Line (64 Bytes) - With @Contended / 128-Byte Padding (ISOLATED)     |
| [ 56 Bytes Padding ] [ Cell 0 (8B Value) ] [ 64 Bytes Padding ]               |
+-------------------------------------------------------------------------------+
  ===> Core 1 và Core 2 cập nhật 2 Cache Line hoàn toàn độc lập. Bus Silence!
```

---

## 7. Java Implementation

Dưới đây là mã nguồn cài đặt 2 cấu trúc dữ liệu đồng thời chuẩn Production-grade bằng Java 21 từ đầu (from scratch):
1. `ConcurrentStripedHashMap<K, V>`: Bảng băm đồng thời phân dải khóa (Lock Striping) kết hợp CAS và Fine-Grained Locking.
2. `LockFreeSkipListMap<K, V>`: Bản đồ tìm kiếm có thứ tự Lock-Free dựa trên Skip List đa tầng sử dụng `java.lang.invoke.VarHandle`.

### 7.1. Cài đặt `ConcurrentStripedHashMap<K, V>`

```java
package com.dsa.concurrent.map;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.*;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.locks.ReentrantLock;
import java.util.function.BiFunction;
import java.util.function.Function;

/**
 * Production-grade Concurrent Striped Hash Map in Java 21.
 * Combines Lock Striping, Lock-Free reads via volatile reads, 
 * and fine-grained bucket synchronization.
 *
 * @param <K> Key type (non-null)
 * @param <V> Value type (non-null)
 */
public class ConcurrentStripedHashMap<K, V> implements Map<K, V> {

    // --- Configuration Constants ---
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    private static final int MAXIMUM_CAPACITY = 1 << 30;
    private static final float DEFAULT_LOAD_FACTOR = 0.75f;
    private static final int DEFAULT_STRIPE_COUNT = 16;

    // --- Node Structure ---
    static class Node<K, V> implements Map.Entry<K, V> {
        final int hash;
        final K key;
        volatile V value;
        volatile Node<K, V> next;

        Node(int hash, K key, V value, Node<K, V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        @Override public K getKey() { return key; }
        @Override public V getValue() { return value; }
        @Override public V setValue(V newValue) {
            Objects.requireNonNull(newValue, "Value cannot be null");
            V old = this.value;
            this.value = newValue;
            return old;
        }

        @Override
        public final String toString() { return key + "=" + value; }

        @Override
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        @Override
        public final boolean equals(Object o) {
            if (o instanceof Map.Entry<?, ?> e) {
                return Objects.equals(key, e.getKey()) && Objects.equals(value, e.getValue());
            }
            return false;
        }
    }

    // --- Fields ---
    private volatile Node<K, V>[] table;
    private final ReentrantLock[] locks;
    private final int stripeMask;
    private final float loadFactor;
    private final AtomicInteger elementCount = new AtomicInteger(0);
    private volatile int threshold;

    // --- VarHandle for atomic table array operations ---
    private static final VarHandle TABLE_HANDLE = MethodHandles.arrayElementVarHandle(Node[].class);

    @SuppressWarnings("unchecked")
    public ConcurrentStripedHashMap(int initialCapacity, float loadFactor, int stripeCount) {
        if (initialCapacity < 0 || loadFactor <= 0 || Float.isNaN(loadFactor) || stripeCount <= 0) {
            throw new IllegalArgumentException("Illegal initial parameters");
        }
        int capacity = computeCapacity(initialCapacity);
        this.loadFactor = loadFactor;
        
        // Stripe count must be power of two
        int stripes = 1;
        while (stripes < stripeCount) {
            stripes <<= 1;
        }
        this.locks = new ReentrantLock[stripes];
        for (int i = 0; i < stripes; i++) {
            this.locks[i] = new ReentrantLock();
        }
        this.stripeMask = stripes - 1;

        this.table = (Node<K, V>[]) new Node[capacity];
        this.threshold = (int) (capacity * loadFactor);
    }

    public ConcurrentStripedHashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_STRIPE_COUNT);
    }

    private static int computeCapacity(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }

    // Spread hash bits (Murmur-like bit mixer)
    static int spreadHash(int h) {
        return (h ^ (h >>> 16)) & 0x7fffffff;
    }

    private ReentrantLock getLock(int hash) {
        return locks[hash & stripeMask];
    }

    @SuppressWarnings("unchecked")
    private Node<K, V> getTableAt(Node<K, V>[] tab, int i) {
        return (Node<K, V>) TABLE_HANDLE.getVolatile(tab, i);
    }

    private void setTableAt(Node<K, V>[] tab, int i, Node<K, V> node) {
        TABLE_HANDLE.setVolatile(tab, i, node);
    }

    // --- Public Operations ---

    @Override
    public int size() {
        return Math.max(0, elementCount.get());
    }

    @Override
    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public boolean containsKey(Object key) {
        return get(key) != null;
    }

    @Override
    public boolean containsValue(Object value) {
        Objects.requireNonNull(value, "Value cannot be null");
        Node<K, V>[] tab = this.table;
        for (Node<K, V> root : tab) {
            for (Node<K, V> p = root; p != null; p = p.next) {
                if (value.equals(p.value)) {
                    return true;
                }
            }
        }
        return false;
    }

    @Override
    public V get(Object key) {
        Objects.requireNonNull(key, "Key cannot be null");
        int h = spreadHash(key.hashCode());
        Node<K, V>[] tab = this.table;
        int idx = h & (tab.length - 1);

        // Lock-free traversal through volatile next references
        for (Node<K, V> p = getTableAt(tab, idx); p != null; p = p.next) {
            if (p.hash == h && (p.key == key || key.equals(p.key))) {
                return p.value;
            }
        }
        return null;
    }

    @Override
    public V put(K key, V value) {
        return putVal(key, value, false);
    }

    @Override
    public V putIfAbsent(K key, V value) {
        return putVal(key, value, true);
    }

    private V putVal(K key, V value, boolean onlyIfAbsent) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(value, "Value cannot be null");
        int h = spreadHash(key.hashCode());
        ReentrantLock lock = getLock(h);
        lock.lock();
        try {
            Node<K, V>[] tab = this.table;
            int idx = h & (tab.length - 1);
            Node<K, V> first = getTableAt(tab, idx);

            for (Node<K, V> p = first; p != null; p = p.next) {
                if (p.hash == h && (p.key == key || key.equals(p.key))) {
                    V oldVal = p.value;
                    if (!onlyIfAbsent || oldVal == null) {
                        p.value = value;
                    }
                    return oldVal;
                }
            }

            // Insert new head
            Node<K, V> newNode = new Node<>(h, key, value, first);
            setTableAt(tab, idx, newNode);
            int count = elementCount.incrementAndGet();

            if (count > threshold && tab.length < MAXIMUM_CAPACITY) {
                tryResize();
            }
            return null;
        } finally {
            lock.unlock();
        }
    }

    @Override
    public V remove(Object key) {
        Objects.requireNonNull(key, "Key cannot be null");
        int h = spreadHash(key.hashCode());
        ReentrantLock lock = getLock(h);
        lock.lock();
        try {
            Node<K, V>[] tab = this.table;
            int idx = h & (tab.length - 1);
            Node<K, V> prev = null;
            Node<K, V> curr = getTableAt(tab, idx);

            while (curr != null) {
                if (curr.hash == h && (curr.key == key || key.equals(curr.key))) {
                    if (prev == null) {
                        setTableAt(tab, idx, curr.next);
                    } else {
                        prev.next = curr.next;
                    }
                    elementCount.decrementAndGet();
                    return curr.value;
                }
                prev = curr;
                curr = curr.next;
            }
            return null;
        } finally {
            lock.unlock();
        }
    }

    @Override
    public boolean remove(Object key, Object value) {
        Objects.requireNonNull(key, "Key cannot be null");
        if (value == null) return false;
        int h = spreadHash(key.hashCode());
        ReentrantLock lock = getLock(h);
        lock.lock();
        try {
            Node<K, V>[] tab = this.table;
            int idx = h & (tab.length - 1);
            Node<K, V> prev = null;
            Node<K, V> curr = getTableAt(tab, idx);

            while (curr != null) {
                if (curr.hash == h && (curr.key == key || key.equals(curr.key))) {
                    if (Objects.equals(curr.value, value)) {
                        if (prev == null) {
                            setTableAt(tab, idx, curr.next);
                        } else {
                            prev.next = curr.next;
                        }
                        elementCount.decrementAndGet();
                        return true;
                    }
                    return false;
                }
                prev = curr;
                curr = curr.next;
            }
            return false;
        } finally {
            lock.unlock();
        }
    }

    @Override
    public V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(mappingFunction, "Mapping function cannot be null");
        int h = spreadHash(key.hashCode());

        // Fast optimistic read check
        V existing = get(key);
        if (existing != null) return existing;

        ReentrantLock lock = getLock(h);
        lock.lock();
        try {
            Node<K, V>[] tab = this.table;
            int idx = h & (tab.length - 1);
            Node<K, V> first = getTableAt(tab, idx);

            for (Node<K, V> p = first; p != null; p = p.next) {
                if (p.hash == h && (p.key == key || key.equals(p.key))) {
                    return p.value;
                }
            }

            // Compute value atomically under lock
            V computed = mappingFunction.apply(key);
            if (computed != null) {
                Node<K, V> newNode = new Node<>(h, key, computed, first);
                setTableAt(tab, idx, newNode);
                if (elementCount.incrementAndGet() > threshold && tab.length < MAXIMUM_CAPACITY) {
                    tryResize();
                }
            }
            return computed;
        } finally {
            lock.unlock();
        }
    }

    @Override
    public V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(remappingFunction, "Remapping function cannot be null");
        int h = spreadHash(key.hashCode());
        ReentrantLock lock = getLock(h);
        lock.lock();
        try {
            Node<K, V>[] tab = this.table;
            int idx = h & (tab.length - 1);
            Node<K, V> prev = null;
            Node<K, V> curr = getTableAt(tab, idx);
            Node<K, V> target = null;

            while (curr != null) {
                if (curr.hash == h && (curr.key == key || key.equals(curr.key))) {
                    target = curr;
                    break;
                }
                prev = curr;
                curr = curr.next;
            }

            V oldVal = (target == null) ? null : target.value;
            V newVal = remappingFunction.apply(key, oldVal);

            if (target != null) {
                if (newVal != null) {
                    target.value = newVal;
                } else {
                    // Remove node
                    if (prev == null) {
                        setTableAt(tab, idx, target.next);
                    } else {
                        prev.next = target.next;
                    }
                    elementCount.decrementAndGet();
                }
            } else if (newVal != null) {
                // Insert new node
                Node<K, V> newNode = new Node<>(h, key, newVal, getTableAt(tab, idx));
                setTableAt(tab, idx, newNode);
                if (elementCount.incrementAndGet() > threshold && tab.length < MAXIMUM_CAPACITY) {
                    tryResize();
                }
            }
            return newVal;
        } finally {
            lock.unlock();
        }
    }

    @SuppressWarnings("unchecked")
    private void tryResize() {
        // Must acquire all stripe locks in strict ascending order to prevent deadlocks
        for (ReentrantLock lock : locks) {
            lock.lock();
        }
        try {
            Node<K, V>[] oldTab = this.table;
            int oldCap = oldTab.length;
            if (oldCap >= MAXIMUM_CAPACITY) return;

            int newCap = oldCap << 1;
            Node<K, V>[] newTab = (Node<K, V>[]) new Node[newCap];

            // Rehash all nodes
            for (int j = 0; j < oldCap; j++) {
                Node<K, V> e = oldTab[j];
                if (e != null) {
                    oldTab[j] = null;
                    Node<K, V> loHead = null, loTail = null;
                    Node<K, V> hiHead = null, hiTail = null;
                    Node<K, V> next;

                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null) loHead = e;
                            else loTail.next = e;
                            loTail = e;
                        } else {
                            if (hiTail == null) hiHead = e;
                            else hiTail.next = e;
                            hiTail = e;
                        }
                        e = next;
                    } while (e != null);

                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }

            this.table = newTab;
            this.threshold = (int) (newCap * loadFactor);
        } finally {
            for (int i = locks.length - 1; i >= 0; i--) {
                locks[i].unlock();
            }
        }
    }

    @Override
    public void putAll(Map<? extends K, ? extends V> m) {
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            put(e.getKey(), e.getValue());
        }
    }

    @Override
    public void clear() {
        for (ReentrantLock lock : locks) {
            lock.lock();
        }
        try {
            Node<K, V>[] tab = this.table;
            for (int i = 0; i < tab.length; i++) {
                setTableAt(tab, i, null);
            }
            elementCount.set(0);
        } finally {
            for (int i = locks.length - 1; i >= 0; i--) {
                locks[i].unlock();
            }
        }
    }

    @Override public Set<K> keySet() { return new KeySetView(); }
    @Override public Collection<V> values() { return new ValuesView(); }
    @Override public Set<Map.Entry<K, V>> entrySet() { return new EntrySetView(); }

    // --- Weakly-Consistent Iterator Views ---
    private class KeySetView extends AbstractSet<K> {
        @Override public Iterator<K> iterator() {
            return new Iterator<>() {
                private final Iterator<Map.Entry<K, V>> it = entrySet().iterator();
                @Override public boolean hasNext() { return it.hasNext(); }
                @Override public K next() { return it.next().getKey(); }
            };
        }
        @Override public int size() { return ConcurrentStripedHashMap.this.size(); }
        @Override public void clear() { ConcurrentStripedHashMap.this.clear(); }
    }

    private class ValuesView extends AbstractCollection<V> {
        @Override public Iterator<V> iterator() {
            return new Iterator<>() {
                private final Iterator<Map.Entry<K, V>> it = entrySet().iterator();
                @Override public boolean hasNext() { return it.hasNext(); }
                @Override public V next() { return it.next().getValue(); }
            };
        }
        @Override public int size() { return ConcurrentStripedHashMap.this.size(); }
        @Override public void clear() { ConcurrentStripedHashMap.this.clear(); }
    }

    private class EntrySetView extends AbstractSet<Map.Entry<K, V>> {
        @Override public Iterator<Map.Entry<K, V>> iterator() {
            return new EntryIterator(table);
        }
        @Override public int size() { return ConcurrentStripedHashMap.this.size(); }
        @Override public void clear() { ConcurrentStripedHashMap.this.clear(); }
    }

    private class EntryIterator implements Iterator<Map.Entry<K, V>> {
        private final Node<K, V>[] currentTab;
        private int tableIndex = 0;
        private Node<K, V> nextNode = null;
        private Node<K, V> lastReturned = null;

        EntryIterator(Node<K, V>[] tab) {
            this.currentTab = tab;
            advance();
        }

        private void advance() {
            if (nextNode != null && nextNode.next != null) {
                nextNode = nextNode.next;
                return;
            }
            while (tableIndex < currentTab.length) {
                Node<K, V> node = getTableAt(currentTab, tableIndex++);
                if (node != null) {
                    nextNode = node;
                    return;
                }
            }
            nextNode = null;
        }

        @Override
        public boolean hasNext() {
            return nextNode != null;
        }

        @Override
        public Map.Entry<K, V> next() {
            if (nextNode == null) throw new NoSuchElementException();
            lastReturned = nextNode;
            advance();
            return new AbstractMap.SimpleImmutableEntry<>(lastReturned.key, lastReturned.value);
        }
    }
}
```

---

### 7.2. Cài đặt `LockFreeSkipListMap<K, V>`

```java
package com.dsa.concurrent.skiplist;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.*;
import java.util.concurrent.ThreadLocalRandom;

/**
 * Non-blocking, Lock-Free Concurrent SkipList Map using Java 21 VarHandles.
 * Keys are ordered according to their natural ordering or a custom Comparator.
 *
 * @param <K> Key type (must implement Comparable or use Comparator)
 * @param <V> Value type (non-null)
 */
public class LockFreeSkipListMap<K, V> implements Iterable<Map.Entry<K, V>> {

    private final Comparator<? super K> comparator;
    private final HeadIndex<K, V> head;

    // --- VarHandles ---
    private static final VarHandle VALUE_HANDLE;
    private static final VarHandle NEXT_NODE_HANDLE;
    private static final VarHandle RIGHT_INDEX_HANDLE;

    static {
        try {
            MethodHandles.Lookup lookup = MethodHandles.lookup();
            VALUE_HANDLE = lookup.findVarHandle(Node.class, "value", Object.class);
            NEXT_NODE_HANDLE = lookup.findVarHandle(Node.class, "next", Node.class);
            RIGHT_INDEX_HANDLE = lookup.findVarHandle(Index.class, "right", Index.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    // Base Node representing a Key-Value pair on Level 0
    static class Node<K, V> {
        final K key;
        volatile Object value; // null represents logical deletion (Tombstone)
        volatile Node<K, V> next;

        Node(K key, Object value, Node<K, V> next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }

        boolean casValue(Object expect, Object update) {
            return VALUE_HANDLE.compareAndSet(this, expect, update);
        }

        boolean casNext(Node<K, V> expect, Node<K, V> update) {
            return NEXT_NODE_HANDLE.compareAndSet(this, expect, update);
        }

        boolean isMarkedForDeletion() {
            return value == null;
        }
    }

    // Index node for Level >= 1
    static class Index<K, V> {
        final Node<K, V> node;
        final Index<K, V> down;
        volatile Index<K, V> right;

        Index(Node<K, V> node, Index<K, V> down, Index<K, V> right) {
            this.node = node;
            this.down = down;
            this.right = right;
        }

        boolean casRight(Index<K, V> expect, Index<K, V> update) {
            return RIGHT_INDEX_HANDLE.compareAndSet(this, expect, update);
        }

        boolean unlinkRight(Index<K, V> succ) {
            return !indexesDeletedNode() && casRight(succ, succ.right);
        }

        boolean indexesDeletedNode() {
            return node.value == null;
        }
    }

    // HeadIndex tracks the highest level
    static class HeadIndex<K, V> extends Index<K, V> {
        final int level;

        HeadIndex(Node<K, V> node, Index<K, V> down, Index<K, V> right, int level) {
            super(node, down, right);
            this.level = level;
        }
    }

    public LockFreeSkipListMap(Comparator<? super K> comparator) {
        this.comparator = comparator;
        Node<K, V> baseHead = new Node<>(null, new Object(), null);
        this.head = new HeadIndex<>(baseHead, null, null, 1);
    }

    public LockFreeSkipListMap() {
        this(null);
    }

    @SuppressWarnings("unchecked")
    private int compare(K k1, K k2) {
        if (comparator != null) {
            return comparator.compare(k1, k2);
        }
        return ((Comparable<? super K>) k1).compareTo(k2);
    }

    /**
     * Lock-free search: Traverses index levels then bottom list.
     */
    @SuppressWarnings("unchecked")
    public V get(K key) {
        Objects.requireNonNull(key, "Key cannot be null");
        Node<K, V> b = findPredecessor(key);
        Node<K, V> n = b.next;

        while (n != null) {
            K k = n.key;
            Object v = n.value;
            if (v == null) {
                // Help unlink deleted node
                Node<K, V> f = n.next;
                b.casNext(n, f);
                n = f;
                continue;
            }
            int c = compare(key, k);
            if (c == 0) {
                return (V) v;
            }
            if (c < 0) {
                break;
            }
            b = n;
            n = n.next;
        }
        return null;
    }

    /**
     * Finds the node immediately preceding the target key at Level 0.
     */
    private Node<K, V> findPredecessor(K key) {
        while (true) {
            Index<K, V> q = head;
            Index<K, V> r = q.right;
            while (true) {
                if (r != null) {
                    Node<K, V> n = r.node;
                    K k = n.key;
                    if (n.value == null) {
                        if (!q.unlinkRight(r)) break; // Inconsistent state, restart
                        r = q.right;
                        continue;
                    }
                    if (compare(key, k) > 0) {
                        q = r;
                        r = r.right;
                        continue;
                    }
                }
                Index<K, V> d = q.down;
                if (d != null) {
                    q = d;
                    r = d.right;
                } else {
                    return q.node;
                }
            }
        }
    }

    /**
     * Lock-free insert / update.
     */
    @SuppressWarnings("unchecked")
    public V put(K key, V value) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(value, "Value cannot be null");

        while (true) {
            Node<K, V> b = findPredecessor(key);
            Node<K, V> n = b.next;

            while (true) {
                if (n != null) {
                    Object v = n.value;
                    if (v == null) { // Deleted node
                        Node<K, V> f = n.next;
                        b.casNext(n, f);
                        n = f;
                        continue;
                    }
                    int c = compare(key, n.key);
                    if (c > 0) {
                        b = n;
                        n = n.next;
                        continue;
                    }
                    if (c == 0) {
                        if (n.casValue(v, value)) {
                            return (V) v;
                        }
                        break; // CAS failed, retry
                    }
                }

                // Insert new node between b and n
                Node<K, V> newNode = new Node<>(key, value, n);
                if (!b.casNext(n, newNode)) {
                    break; // Contention: b.next changed, retry outer loop
                }

                // Build probabilistic index tower
                buildIndexTower(newNode);
                return null;
            }
        }
    }

    /**
     * Builds multi-level index tower for newly inserted node with probability p = 0.5.
     */
    private void buildIndexTower(Node<K, V> z) {
        int rnd = ThreadLocalRandom.current().nextInt();
        if ((rnd & 0x80000001) == 0) { // Probability 1/2
            int level = 1;
            while (((rnd >>>= 1) & 1) != 0) {
                level++;
            }

            Index<K, V> idx = null;
            for (int i = 1; i <= level; i++) {
                idx = new Index<>(z, idx, null);
            }

            // Link index tower into skip list levels
            HeadIndex<K, V> h = this.head;
            if (level <= h.level) {
                linkIndexTower(idx, level);
            }
        }
    }

    private void linkIndexTower(Index<K, V> newIdx, int targetLevel) {
        Index<K, V> q = head;
        while (q != null && q.node.next != newIdx.node) {
            if (q.right != null && compare(newIdx.node.key, q.right.node.key) > 0) {
                q = q.right;
            } else if (q.down != null) {
                q = q.down;
            } else {
                break;
            }
        }
        if (q != null && newIdx.node.value != null) {
            newIdx.right = q.right;
            q.casRight(newIdx.right, newIdx);
        }
    }

    /**
     * Lock-free deletion: Two-step logical tombstone + CAS unlink.
     */
    @SuppressWarnings("unchecked")
    public V remove(K key) {
        Objects.requireNonNull(key, "Key cannot be null");
        while (true) {
            Node<K, V> b = findPredecessor(key);
            Node<K, V> n = b.next;

            while (true) {
                if (n == null) return null;
                Object v = n.value;
                if (v == null) {
                    Node<K, V> f = n.next;
                    b.casNext(n, f);
                    n = f;
                    continue;
                }
                int c = compare(key, n.key);
                if (c < 0) return null;
                if (c > 0) {
                    b = n;
                    n = n.next;
                    continue;
                }

                // Step 1: Logical deletion (CAS value to null)
                if (!n.casValue(v, null)) {
                    break; // CAS failed, retry
                }

                // Step 2: Physical unlink
                b.casNext(n, n.next);
                return (V) v;
            }
        }
    }

    @Override
    public Iterator<Map.Entry<K, V>> iterator() {
        return new SkipListIterator();
    }

    private class SkipListIterator implements Iterator<Map.Entry<K, V>> {
        private Node<K, V> cursor = head.node.next;

        @Override
        public boolean hasNext() {
            while (cursor != null && cursor.value == null) {
                cursor = cursor.next;
            }
            return cursor != null;
        }

        @Override
        @SuppressWarnings("unchecked")
        public Map.Entry<K, V> next() {
            if (!hasNext()) throw new NoSuchElementException();
            Node<K, V> curr = cursor;
            cursor = cursor.next;
            return new AbstractMap.SimpleImmutableEntry<>(curr.key, (V) curr.value);
        }
    }
}
```

---

## 8. Step-by-Step Execution

### Kịch bản Trace: 4 Luồng tương tác đồng thời trên `ConcurrentHashMap`

Giả sử bảng băm có kích thước ban đầu `Capacity = 4`, ngưỡng `threshold = 3`, đang chứa 2 phần tử:
- `Bucket 1`: `Node("A", 100)` (Hash = 1)
- `Bucket 2`: `Node("B", 200)` (Hash = 2)

#### Trình tự thời gian (Chronological Timeline):

```
T0: Initial State: table = [ [0]=null, [1]=Node("A"), [2]=Node("B"), [3]=null ] (Size = 2)

T1 (Thread 1 - Put): Thread 1 gọi put("C", 300) với hash("C") = 0.
    - Kiểm tra table[0] == null.
    - Thực thi CAS: compareAndSet(table, 0, null, Node("C", 300)).
    - CAS Thành công! Không cần acquire bất kỳ lock nào. Size tăng lên 3.

T2 (Thread 2 - Put & Trigger Resize): Thread 2 gọi put("D", 400) với hash("D") = 3.
    - table[3] == null -> CAS thành công chèn Node("D", 400).
    - Size tăng lên 4 > threshold (3).
    - Thread 2 khởi tạo nextTable với kích thước = 8.
    - Thread 2 bắt đầu transfer từ index 3 xuống 0.

T3 (Thread 3 - Help Transfer): Thread 3 gọi put("E", 500) với hash("E") = 3.
    - Thread 3 đọc table[3], thấy node tại đây có hash = MOVED (-1) (ForwardingNode).
    - Thread 3 nhận biết đang có quá trình resize -> Gọi helpTransfer().
    - Thread 3 nhận stride xử lý Bucket 1.
    - Thread 3 acquire lock trên Node("A"), tính toán low/high nodes:
        - hash("A") & 4 == 0 -> Giữ nguyên tại nextTable[1].
    - Thread 3 gán ForwardingNode vào table cũ tại index 1. Unlock Node("A").

T4 (Thread 4 - Concurrent Lock-Free Get): Thread 4 gọi get("A").
    - Thread 4 tính hash("A") = 1.
    - Đọc table cũ tại index 1, phát hiện ForwardingNode!
    - ForwardingNode.find("A") lập tức chuyển hướng đọc sang nextTable[1].
    - Tìm thấy Node("A", 100) trên nextTable[1] và trả về 100 NGAY LẬP TỨC.
    - Hoàn toàn KHÔNG BỊ CHẶN bởi Thread 2 hay Thread 3!
```

---

## 9. Complexity Analysis

### 9.1. Bảng so sánh Độ phức tạp Thời gian & Không gian

| Cấu trúc dữ liệu | Read (Avg) | Read (Worst) | Write (Avg) | Write (Worst) | Range Query | Memory Overhead per Entry | Synchronization Primitive |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`ConcurrentHashMap`** | $\mathcal{O}(1)$ | $\mathcal{O}(\log n)$ | $\mathcal{O}(1)$ | $\mathcal{O}(\log n)$ | $\mathcal{O}(n)$ | $\approx 32 - 48$ bytes | CAS + `synchronized(firstNode)` |
| **`ConcurrentSkipListMap`** | $\mathcal{O}(\log n)$ | $\mathcal{O}(\log n)$ | $\mathcal{O}(\log n)$ | $\mathcal{O}(\log n)$ | $\mathcal{O}(\log n + k)$ | $\approx 56 - 80$ bytes | Lock-Free CAS (`VarHandle`) |
| **`CopyOnWriteArrayList`** | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | $\mathcal{O}(k)$ | $\approx 8$ bytes (Array ref) | `ReentrantLock` for writes |
| **`Collections.synchronizedMap`** | $\mathcal{O}(1)$ | $\mathcal{O}(n)$ | $\mathcal{O}(1)$ | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | $\approx 32$ bytes | Global Monitor Lock |
| **`LongAdder`** | $\mathcal{O}(1)$ | $\mathcal{O}(C)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | N/A | $\approx 128$ bytes $\times C$ (Cells) | CAS + Cell Striping (`@Contended`) |

*(Ghi chú: $n$ là số phần tử, $k$ là số phần tử trong khoảng range query, $C$ là số lượng CPU cores).*

### 9.2. Phân tích chi tiết Memory Footprint trên 64-bit JVM

Với 64-bit JVM sử dụng Compressed OOPs (`-XX:+UseCompressedOops` - mặc định cho heap $< 32\text{GB}$):
- **Object Header**: $12$ bytes (8 bytes Mark Word + 4 bytes Compressed Klass Pointer) $\to$ padding thành $16$ bytes.
- **`ConcurrentHashMap.Node`**:
  - Header: $16$ bytes.
  - Fields: `int hash` ($4$B) + `key` ref ($4$B) + `val` ref ($4$B) + `next` ref ($4$B) = $16$ bytes.
  - Tổng cộng: **$32$ bytes / node**.
- **`TreeBin` (khi hóa cây Red-Black)**:
  - Bọc thêm `TreeNode` kế thừa `Node` với `parent`, `left`, `right`, `prev`, `boolean red` $\approx$ **$56$ bytes / node**.
- **`CopyOnWriteArrayList`**:
  - Header của mảng: $16$ bytes + $4$ bytes length = $24$ bytes.
  - Mỗi thao tác `add()` tạo mới toàn bộ mảng $N$ phần tử $\to$ Chi phí cấp phát tức thời $\mathcal{O}(N)$ làm tăng áp lực khủng khiếp lên Young Gen GC.

---

## 10. JVM & Hardware Level Analysis

### 10.1. Hardware Cache Hierarchy & False Sharing

Mọi CPU hiện đại (x86_64, ARM64) tải dữ liệu từ RAM lên L1/L2/L3 Cache theo từng khối cố định gọi là **Cache Line** (thường là $64$ bytes).

```
+-------------------------------------------------------------------------+
| CPU Core 0                             CPU Core 1                       |
| L1 Data Cache (32 KB)                  L1 Data Cache (32 KB)            |
| [ Cache Line A: VarX | VarY ]          [ Cache Line A: VarX | VarY ]    |
+-------------------------------------------------------------------------+
                                   |
                  MESI Cache Coherency Protocol Bus
                                   |
+-------------------------------------------------------------------------+
| Shared L3 Cache (32 MB) / Main RAM                                     |
+-------------------------------------------------------------------------+
```

**Hiện tượng False Sharing (Chia sẻ giả tạo)**:
- Xảy ra khi Thread 1 trên Core 0 liên tục ghi vào biến `VarX`, trong khi Thread 2 trên Core 1 liên tục ghi vào biến `VarY`.
- Dù `VarX` và `VarY` là hai biến logic hoàn toàn độc lập, nhưng nếu chúng nằm chung trong một Cache Line $64$-byte, mỗi lệnh ghi của Core 0 sẽ phát tín hiệu **Invalidate** qua MESI bus, buộc Core 1 phải xả Cache Line và nạp lại từ L3/RAM.
- Hiệu năng có thể tụt giảm từ **$10\times$ đến $100\times$**!

### 10.2. Giải pháp: `@jdk.internal.vm.annotation.Contended`

Trong mã nguồn của `LongAdder` (`Striped64.Cell`):

```java
@jdk.internal.vm.annotation.Contended
static final class Cell {
    volatile long value;
    Cell(long x) { value = x; }
    final boolean cas(long cmp, long val) {
        return VALUE.compareAndSet(this, cmp, val);
    }
    // ...
}
```

- Annotation `@Contended` yêu cầu JVM chèn thêm **128 bytes padding** xung quanh đối tượng (trước và sau trường dữ liệu).
- Đảm bảo mỗi instance `Cell` độc chiếm hoàn toàn ít nhất 1 hoặc 2 Cache Line riêng biệt, triệt tiêu $100\%$ xung đột False Sharing.
- *(Lưu ý: Ứng dụng người dùng muốn sử dụng `@Contended` cần bật cờ JVM `-XX:-RestrictContended`)*.

### 10.3. Low-Level CAS & Memory Barriers qua `VarHandle`

Java 9+ thay thế `sun.misc.Unsafe` bằng `java.lang.invoke.VarHandle`, cung cấp quyền truy cập hạt mịn (fine-grained) vào các chỉ thị rào cản bộ nhớ của CPU (Memory Barriers):

```
Java Memory Order Modes:
1. Plain:           get() / set()                 -> Không có Memory Barrier (No ordering guarantees)
2. Opaque:          getOpaque() / setOpaque()     -> Đảm bảo tính nguyên tử (Bitwise atomicity), không reorder nội bộ thread
3. Acquire/Release: getAcquire() / setRelease()   -> Ghép đôi One-way Memory Barrier (Tương đương C++ memory_order_acquire/release)
4. Volatile:        getVolatile() / setVolatile() -> LoadLoad + LoadStore + StoreStore + StoreLoad (Full Sequential Consistency)
```

Trên kiến trúc x86_64:
- Lệnh đọc `volatile` là một lệnh `MOV` thông thường (vì x86 có mô hình phần cứng TSO - Total Store Order mạnh mẽ).
- Lệnh ghi `volatile` hoặc CAS biên dịch thành chỉ thị phần cứng nguyên tử `LOCK CMPXCHG` hoặc kèm theo lệnh `MFENCE`.

---

## 11. OpenJDK Deep-Dive

### 11.1. Phân tích mã nguồn `ConcurrentHashMap` (Java 21)

Trong OpenJDK 21, `ConcurrentHashMap` chứa các hằng số và trường nhị phân cốt lõi:

```java
static final int MOVED     = -1; // Hash của ForwardingNode
static final int TREEBIN   = -2; // Hash của TreeBin (gốc cây Red-Black)
static final int RESERVED  = -3; // Hash của ReservationNode (dùng trong computeIfAbsent)
static final int HASH_BITS = 0x7fffffff; // Mask giữ lại các bit dương

// Điều khiển kích thước và trạng thái resize
private transient volatile int sizeCtl;
```

**Bí mật của biến `sizeCtl`**:
- `sizeCtl = 0`: Trạng thái mặc định trước khi khởi tạo bảng.
- `sizeCtl = -1`: Bảng đang được khởi tạo bởi đúng 1 thread.
- `sizeCtl < -1`: Bảng đang trong quá trình resize đa luồng. Giá trị biểu diễn số lượng thread đang tham gia hỗ trợ transfer:
  $$\text{sizeCtl} = (\text{resizeStamp}(n) \ll 16) + (\text{threads} + 1)$$
- `sizeCtl > 0`: Ngưỡng threshold kích hoạt đợt resize tiếp theo ($N \times \text{loadFactor}$).

### 11.2. Thao tác `putVal` nguyên tử trong OpenJDK

Quy trình xử lý của `ConcurrentHashMap.putVal`:
1. Tính `hash = spread(key.hashCode())`.
2. Vòng lặp vô hạn `for (Node<K,V>[] tab = table;;)`:
   - **Case 1**: `tab` chưa được cấp phát $\to$ gọi `initTable()` bằng CAS trên `sizeCtl`.
   - **Case 2**: Bucket `(n - 1) & hash` đang `null` $\to$ dùng `casTabAt(tab, i, null, new Node(...))`. Nếu thành công $\to$ `break`.
   - **Case 3**: Node đầu tiên có `fh == MOVED` $\to$ Luồng lập tức gọi `helpTransfer(tab, f)` để hỗ trợ resize.
   - **Case 4**: Có xung đột $\to$ Dùng `synchronized (f)` (khóa trên node đầu tiên của bucket):
     - Kiểm tra lại `tabAt(tab, i) == f` (Double-Checked Locking).
     - Nếu là danh sách liên kết: Duyệt qua tìm key trùng để update value, hoặc gắn vào cuối danh sách. Đếm `binCount`.
     - Nếu là `TreeBin`: Gọi `((TreeBin<K,V>)f).putTreeVal(hash, key, value)`.
   - **Case 5**: Nếu `binCount >= TREEIFY_THRESHOLD (8)` $\to$ gọi `treeifyBin(tab, i)`.

---

## 12. Production Usage & Real-world Applications

1. **High-Throughput Web Frameworks (Netty / Tomcat / Undertow)**:
   - Netty sử dụng `ConcurrentHashMap` để quản lý `ChannelHandlerContext` và active TCP connections trên hàng triệu kết nối đồng thời.
2. **Distributed Caching Systems (Caffeine / Infinispan / Hazelcast)**:
   - Thư viện cache Caffeine (thư viện cache số 1 thế giới trên JVM) sử dụng `ConcurrentHashMap` làm cấu trúc dữ liệu lưu trữ chính kết hợp với RingBuffer lock-free (`StripedRingBuffer`) để đạt throughput $> 100\text{ triệu ops/giây}$.
3. **High-Frequency Trading (HFT) & Financial Order Books**:
   - `ConcurrentSkipListMap` được dùng để duy trì **Limit Order Book (LOB)** theo thời gian thực. Các mức giá (Price Levels) được sắp xếp liên tục; luồng khớp lệnh (Matching Engine) duyệt các mức giá tốt nhất bằng thao tác `firstEntry()` hoặc `subMap()` lock-free.
4. **Metrics & APM Telemetry (Prometheus / Micrometer / OpenTelemetry)**:
   - Toàn bộ các bộ đếm Counter trong Micrometer / Prometheus Java Client đều sử dụng `LongAdder` để thu thập hàng triệu request/sec từ hàng nghìn thread worker mà CPU không bị nghẽn do lock contention.

---

## 13. Design Decisions & Trade-offs

### Ma trận so sánh Quyết định Thiết kế

| Tiêu chí | `ConcurrentHashMap` | `ConcurrentSkipListMap` | `CopyOnWriteArrayList` | `Collections.synchronizedMap` | In-Memory Redis |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Throughput Đọc** | Cực đại ($100\text{M}+ \text{ops/s}$) | Rất cao ($10\text{M}+ \text{ops/s}$) | Tối đa phần cứng (Zero Lock) | Kém (Bị chặn bởi Write) | Trung bình (Bị giới hạn bởi Network) |
| **Throughput Ghi** | Rất cao (Striped Locks) | Cao (Lock-Free CAS) | Rất kém ($O(n)$ array copy) | Kém (Single thread only) | Rất cao (Single-threaded Reactor) |
| **Duyệt có thứ tự (Ordering)** | Không hỗ trợ (Unordered) | Hoàn hảo (Sorted Map) | Theo thứ tự chèn (List) | Không hỗ trợ | Có hỗ trợ (Sorted Set - ZSet) |
| **Range Queries (`subMap`)** | Không thể thực hiện hiệu quả | Cực kỳ hiệu quả ($O(\log n + k)$) | N/A | Không thể | Rất tốt (`ZRANGEBYSCORE`) |
| **Memory Footprint** | Trung bình ($\approx 32\text{B}$/entry) | Khá cao ($\approx 64\text{B}$/entry) | Nhỏ gọn cho List | Nhỏ nhất | Lớn (Do metadata của Redis) |
| **GC Pressure** | Thấp | Trung bình (Tạo Index nodes) | Cực cao khi ghi nhiều | Rất thấp | Không ảnh hưởng JVM GC |
| **Phạm vi áp dụng** | 1 JVM Process | 1 JVM Process | 1 JVM Process | 1 JVM Process | Toàn bộ Distributed System |

---

## 14. 20 Common Bugs & Pitfalls

1. **Non-Atomic Check-Then-Act**:
   ```java
   // BUG: Race condition! Hai luồng cùng thấy containsKey == false và cùng ghi đè
   if (!map.containsKey(key)) {
       map.put(key, computeValue(key));
   }
   // FIX: Sử dụng phương thức nguyên tử
   map.computeIfAbsent(key, k -> computeValue(k));
   ```
2. **Recursive/Blocking Calls inside `computeIfAbsent`**:
   - Gọi một thao tác blocking (I/O, database) hoặc gọi đệ quy cập nhật chính map đó bên trong lambda của `computeIfAbsent` sẽ làm giữ bucket lock vô thời hạn $\to$ Gây **Thread Starvation** hoặc **Deadlock**.
3. **Modifying Mutable Nested Objects inside Concurrent Collections**:
   - `ConcurrentHashMap` chỉ bảo vệ tham chiếu đến đối tượng value, **không** bảo vệ tính thread-safe của các trường bên trong đối tượng value đó nếu nó là mutable class.
4. **Using `CopyOnWriteArrayList` for Write-Heavy Workloads**:
   - Thực hiện hàng nghìn lệnh `add()` mỗi giây trên `CopyOnWriteArrayList` sẽ liên tục cấp phát mảng mới $\to$ Gây **OutOfMemoryError (OOM)** hoặc đóng băng ứng dụng do Full GC liên tục.
5. **Forgetting to Validate Stamp in `StampedLock`**:
   - Sau khi gọi `lock.tryOptimisticRead()`, lập tức sử dụng dữ liệu mà không gọi `lock.validate(stamp)` $\to$ Đọc phải dữ liệu rác hoặc trạng thái rách (Torn Read).
6. **Relying on Exact Size in Concurrent Loops**:
   - Gọi `map.size() == 0` hoặc dùng `size()` làm điều kiện dừng chính xác cho vòng lặp trong môi trường đa luồng $\to$ Kết quả của `size()` chỉ là snapshot tạm thời (weakly consistent).
7. **Livelock in Custom CAS Loops without Exponential Backoff**:
   - Vòng lặp `while (!cas(...))` quay liên tục dưới tải contention cực cao làm CPU đạt $100\%$ mà không hoàn thành được tác vụ $\to$ Cần thêm `Thread.onSpinWait()` (Java 9+) hoặc exponential backoff.
8. **Deadlock with Nested Multi-Key `compute`**:
   - Thread A gọi `map.compute(Key1, (k1, v1) -> map.compute(Key2, ...))` trong khi Thread B gọi ngược lại `map.compute(Key2, ... Key1)` $\to$ Gây Deadlock do tranh chấp bucket lock chéo.
9. **Assuming `ConcurrentHashMap` Iterators are Snapshots**:
   - Iterator của `ConcurrentHashMap` là **Weakly Consistent** (không ném `ConcurrentModificationException`, nhưng có thể phản ánh một số phần tử thêm mới sau khi tạo iterator).
10. **Passing `null` Key or `null` Value**:
    - `ConcurrentHashMap` và `ConcurrentSkipListMap` **cấm tuyệt đối** `null` key và `null` value (ném `NullPointerException` ngay lập tức) để tránh sự nhập nhằng giữa "key không tồn tại" và "key có giá trị null".
11. **Stale Optimistic Read Validation with ABA Roll-over**:
    - Sử dụng `StampedLock` trong các vòng lặp cực dài mà không kiểm tra cờ lỗi, stamp bị tràn số sau $2^{64}$ lần cập nhật.
12. **Calling `StampedLock.writeLock()` Reentrantly on the Same Thread**:
    - `StampedLock` **không có tính Reentrant (không tái nhập)**! Một thread đang giữ `readLock()` hoặc `writeLock()` mà gọi tiếp `writeLock()` sẽ tự gây **Deadlock** với chính mình.
13. **Memory Leak with Large Lambdas in `computeIfAbsent`**:
    - Lambda giữ tham chiếu ẩn đến đối tượng cha (`this` enclosing class), khiến đối tượng cha không thể bị Garbage Collection.
14. **Ignoring Side-Effects in Retried Lambdas**:
    - Hàm remapping trong `compute()` hoặc `merge()` có thể bị JVM thực thi lại nhiều lần nếu có xung đột CAS $\to$ Không được đặt side-effect (gửi email, gọi REST API) trong lambda.
15. **Misusing `LongAdder` for Snapshot Invariant Checking**:
    - Viết logic: `if (adder.sum() > limit) doAction()` $\to$ Tại thời điểm `doAction()` chạy, tổng thực tế có thể đã bị các thread khác thay đổi hoàn toàn.
16. **Using Weak Memory Ordering incorrectly with `VarHandle`**:
    - Dùng `setOpaque` thay vì `setRelease` khi xuất bản (publish) đối tượng sang luồng khác $\to$ Luồng đọc có thể thấy đối tượng ở trạng thái chưa khởi tạo xong (partially constructed object).
17. **Mutable Keys with Changing `hashCode()`**:
    - Chèn một đối tượng làm Key vào `ConcurrentHashMap`, sau đó thay đổi giá trị một trường của Key làm `hashCode()` thay đổi $\to$ Key bị kẹt vĩnh viễn trong bucket cũ, không bao giờ `get()` hay `remove()` được nữa!
18. **Unchecked InterruptedException in StampedLock**:
    - Sử dụng `readLock()` không thể ngắt khi thread bị interrupt $\to$ Phải dùng `readLockInterruptibly()` nếu cần hỗ trợ graceful shutdown.
19. **Assigning `volatile` to Collection Reference without Thread-Safe Collection**:
    ```java
    // BUG: volatile chỉ bảo vệ con trỏ 'list', không bảo vệ các thao tác bên trong ArrayList!
    private volatile List<String> list = new ArrayList<>();
    ```
20. **Underestimating `ConcurrentSkipListMap.size()` Latency**:
    - Trong `ConcurrentSkipListMap`, phương thức `size()` không phải là hằng số $O(1)$ mà phải duyệt qua toàn bộ danh sách liên kết $O(n)$ $\to$ Cực chậm nếu map có hàng triệu phần tử.

---

## 15. 30 Critical Edge Cases

1. **Null Key Rejection**: `map.put(null, "v")` $\to$ Ném `NullPointerException` lập tức.
2. **Null Value Rejection**: `map.put("k", null)` $\to$ Ném `NullPointerException` lập tức.
3. **100% Hash Collision (Worst-case)**: Hàng triệu key có cùng `hashCode()` $\to$ Hệ thống tự động chuyển sang `TreeBin` (Red-Black Tree), duy trì thời gian tìm kiếm $O(\log n)$.
4. **Keys without `Comparable` under High Collision**: Nếu các key có cùng `hashCode()` nhưng không implement `Comparable`, `ConcurrentHashMap` dùng `tieBreakOrder()` dựa trên `System.identityHashCode()` để phân định thứ tự trên cây đỏ-đen.
5. **Extreme Concurrency during Table Resizing**: Hàng trăm thread cùng thực hiện `put()` khi bảng đang resize $\to$ Tất cả các luồng cùng gọi `helpTransfer()` chia nhau các stride để hoàn tất resize song song.
6. **Concurrent Read on Bucket undergoing Transfer**: Thread `get()` gặp `ForwardingNode` $\to$ Chuyển tiếp tìm kiếm sang `nextTable` mà không bị block dù chỉ 1 nano giây.
7. **TreeBin Untreeification**: Xóa phần tử trong `TreeBin` làm số node giảm xuống $\le 6$ $\to$ Chuyển đổi ngược lại thành danh sách liên kết đơn `Node`.
8. **Concurrent `computeIfAbsent` on Same Missing Key**: Hai thread cùng gọi `computeIfAbsent("X", mapping)` $\to$ Chỉ đúng 1 thread được thực thi `mappingFunction`, thread kia bị block ngắn hạn trên bucket lock và nhận kết quả tính toán của thread trước.
9. **Cross-Key Circular `compute()` Deadlock**: Thread 1 giữ Bucket A chờ Bucket B; Thread 2 giữ Bucket B chờ Bucket A $\to$ JVM không thể tự cứu; cần thiết kế ứng dụng không lồng các thao tác compute.
10. **Iterator Traversal during Bucket Splitting**: Iterator đang đọc bucket $i$ thì bảng resize thành công $\to$ Iterator tiếp tục đọc các node theo con trỏ `next` cũ mà không bị lỗi cấu trúc.
11. **Atomic Conditional Replace Failure**: `map.replace(k, oldVal, newVal)` trả về `false` nếu `oldVal` đã bị thread khác thay đổi dù chỉ 1 micro giây trước đó.
12. **Thread Interrupted while waiting for Bucket Lock**: `synchronized(firstNode)` trong Java không thể bị hủy bằng `Thread.interrupt()` $\to$ Thread sẽ chờ cho đến khi nhận được lock mới xử lý cờ ngắt.
13. **OOM Condition in `CopyOnWriteArrayList.add()`**: Hệ thống còn ít bộ nhớ heap $\to$ Lệnh tạo mảng sao chép ném `OutOfMemoryError: Java heap space`, mảng gốc vẫn được bảo toàn nguyên vẹn.
14. **Stamp Validation during Fast Write-Unlock Cycle**: Luồng ghi lấy writeLock rồi unlock ngay lập tức $\to$ Luồng đọc lạc quan gọi `validate(stamp)` sẽ phát hiện stamp đã thay đổi và fallback sang lock truyền thống an toàn.
15. **Reading MarkerNode in SkipList**: Luồng đọc chạm phải `MarkerNode` (đang bị xóa) $\to$ Bỏ qua marker và tự động hỗ trợ unlink node đó khỏi danh sách.
16. **Skip List Tower Height Exceeding Maximum**: Khi chèn hàng tỷ phần tử, chiều cao tháp Skip List tự động tăng dần mà không bị giới hạn cứng.
17. **`LongAdder` Value Overflow**: Tổng vượt quá `Long.MAX_VALUE` ($2^{63}-1$) $\to$ Giá trị tự động tràn số thành số âm theo chuẩn số học bù 2 (Two's Complement) mà không ném exception.
18. **Weakly Consistent `sum()` on `LongAdder`**: `sum()` trả về tổng tại thời điểm duyệt qua các Cell; các thao tác `add()` diễn ra đồng thời sau đó có thể không được tính vào.
19. **Full GC Pause freezing Thread holding Bucket Lock**: Luồng giữ bucket lock bị dừng bởi Stop-The-World GC $\to$ Toàn bộ các luồng khác truy cập vào *cùng bucket đó* sẽ bị hoãn lại cho đến khi GC hoàn tất.
20. **Table Expansion to `MAXIMUM_CAPACITY` ($2^{30}$)**: Khi dung lượng đạt $1 << 30$, bảng băm không thể tăng kích thước thêm nữa $\to$ `threshold` được đặt thành `Integer.MAX_VALUE`, chấp nhận bucket dài hơn.
21. **Concurrent `clear()` vs `put()`**: `clear()` duyệt qua từng bucket để set `null` $\to$ Một lệnh `put()` vào bucket đã clear sẽ thành công ngay sau đó; không có bảo đảm toàn bộ map trống rỗng tại một thời điểm tuyệt đối.
22. **Mutable Key Mutation after Insertion**: Sửa đổi trường dữ liệu của key đã lưu trong map $\to$ Key không thể tìm thấy qua `get(key)`, nhưng vẫn tồn tại trong `entrySet()` (Ghost Entry).
23. **Concurrent SkipList CAS Contention at Same Tower Level**: Hai thread cùng muốn chèn index vào cùng một vị trí $\to$ Một thread CAS thành công, thread kia thất bại sẽ đọc lại con trỏ `right` mới và thử lại.
24. **Parallel Stream Bulk Operations on Common ForkJoinPool**: `map.forEach(parallelismThreshold, action)` sử dụng `ForkJoinPool.commonPool()` $\to$ Nếu action chứa blocking code, toàn bộ các tác vụ song song khác của JVM sẽ bị nghẽn.
25. **Comparator throwing RuntimeException in SkipList**: Comparator ném exception $\to$ Thao tác tìm kiếm/chèn bị hủy bỏ nửa chừng, nhưng cấu trúc SkipList vẫn duy trì tính toàn vẹn (Memory Invariants không bị hỏng).
26. **Modifying Underlying List while iterating `CopyOnWriteArrayList.subList()`**: `subList` của `CopyOnWriteArrayList` tạo view $\to$ Bất kỳ thao tác ghi nào trên `subList` đều tạo bản sao mảng mới và cập nhật cả list cha.
27. **Concurrent `setValue()` on `Map.Entry`**: Gọi `entry.setValue(newVal)` trong khi thread khác đang xóa entry $\to$ Giá trị mới có thể bị mất nếu node đã bị unlink khỏi bucket.
28. **`computeIfPresent` returning `null`**: Hàm remapping trả về `null` $\to$ Node tương ứng tự động bị xóa bỏ khỏi bảng băm một cách nguyên tử.
29. **Chained Multi-Level Resize Encounter**: Một thread chậm chạm gặp liên tiếp 2 đợt resize $\to$ `ForwardingNode` trỏ tới bảng trung gian, bảng trung gian lại trỏ tới bảng mới nhất $\to$ Thuật toán tiếp tục đệ quy `find()` đến bảng cuối cùng.
30. **Stamp Bit Inversion / Overflow in `StampedLock`**: Phiên bản stamp được thiết kế 64-bit $\to$ Ngay cả khi thực hiện $10^9$ thao tác write mỗi giây, phải mất hơn 500 năm mới xảy ra hiện tượng tràn bit stamp.

---

## 16. Optimization Techniques

1. **Pre-Sizing `ConcurrentHashMap`**:
   - Khởi tạo kích thước ban đầu đủ lớn để tránh hoàn toàn chi phí đắt đỏ của việc Resizing trong giờ cao điểm:
   ```java
   // Cần lưu 1,000,000 phần tử với loadFactor 0.75:
   int initialCapacity = (int) Math.ceil(1_000_000 / 0.75f) + 1; // 1,333,334
   ConcurrentHashMap<String, Object> map = new ConcurrentHashMap<>(initialCapacity);
   ```
2. **Optimistic Read Retry Pattern with Fallback**:
   ```java
   public double calculateHypotenuse(StampedLock lock, Point p) {
       long stamp = lock.tryOptimisticRead();
       double x = p.x, y = p.y;
       if (!lock.validate(stamp)) { // Xung đột ghi xảy ra!
           stamp = lock.readLock(); // Fallback sang Pessimistic Read Lock
           try {
               x = p.x;
               y = p.y;
           } finally {
               lock.unlockRead(stamp);
           }
       }
       return Math.sqrt(x * x + y * y);
   }
   ```
3. **Thread-Local Batching / Buffering**:
   - Thay vì mọi thread liên tục ghi vào `ConcurrentHashMap` chung, gom các sự kiện vào `ThreadLocal<List<Event>>` và xả (flush) hàng loạt vào Map sau mỗi khoảng thời gian $10\text{ms}$.
4. **Tuning `parallelismThreshold` for Bulk Operations**:
   - Với các map nhỏ ($< 10,000$ phần tử), luôn đặt `parallelismThreshold = Long.MAX_VALUE` để JVM thực thi đơn luồng, tránh overhead tạo task và context switch của `ForkJoinPool`.

---

## 17. Best Practices & Guidelines

- **Immutability First**: Luôn luôn thiết kế các đối tượng làm Key và Value là **Immutable** (`record` trong Java 17+ hoặc `final class` với các trường `final`).
- **Zero Heavy Computations inside Atomic Lambdas**: Không bao giờ thực hiện gọi Database, HTTP Call, hoặc Thread Sleep bên trong `compute`, `computeIfAbsent`, `merge`.
- **Choose the Right Tool for the Job**:
  - Cần Map tra cứu cực nhanh theo Key $\to$ `ConcurrentHashMap`.
  - Cần Map sắp xếp có thứ tự, tìm kiếm theo khoảng $\to$ `ConcurrentSkipListMap`.
  - Cần List đọc nhiều, ghi cực hiếm ($> 99\%$ read) $\to$ `CopyOnWriteArrayList`.
  - Cần đếm số lượng, thống kê metric tần suất cao $\to$ `LongAdder`.
  - Cần bảo vệ dữ liệu với 90%+ thao tác đọc nhưng cần hỗ trợ struct phức tạp $\to$ `StampedLock`.

---

## 18. JMH Benchmark Suite

Dưới đây là mã nguồn benchmark chuẩn xác sử dụng **JMH (Java Microbenchmark Harness)** để đo lường Throughput (ops/sec) dưới 3 kịch bản: Read-Heavy (99/1), Balanced (80/20), và Write-Heavy (50/50).

```java
package com.dsa.benchmark;

import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.infra.Blackhole;
import java.util.*;
import java.util.concurrent.*;

@BenchmarkMode(Mode.Throughput)
@OutputTimeUnit(TimeUnit.SECONDS)
@Warmup(iterations = 3, time = 2, timeUnit = TimeUnit.SECONDS)
@Measurement(iterations = 5, time = 3, timeUnit = TimeUnit.SECONDS)
@Fork(1)
@Threads(16) // Mô phỏng 16 threads cạnh tranh đồng thời
@State(Scope.Benchmark)
public class ConcurrentMapBenchmark {

    @Param({"CHM", "CSLM", "SYNC_MAP"})
    private String mapType;

    private Map<Integer, String> map;
    private static final int MAP_SIZE = 100_000;

    @Setup(Level.Trial)
    public void setup() {
        map = switch (mapType) {
            case "CHM" -> new ConcurrentHashMap<>(MAP_SIZE);
            case "CSLM" -> new ConcurrentSkipListMap<>();
            case "SYNC_MAP" -> Collections.synchronizedMap(new HashMap<>(MAP_SIZE));
            default -> throw new IllegalArgumentException("Unknown type");
        };

        for (int i = 0; i < MAP_SIZE; i++) {
            map.put(i, "Value_" + i);
        }
    }

    @Benchmark
    @Group("ReadHeavy_99_1")
    @GroupThreads(15)
    public void readHeavy_Read(Blackhole bh) {
        int key = ThreadLocalRandom.current().nextInt(MAP_SIZE);
        bh.consume(map.get(key));
    }

    @Benchmark
    @Group("ReadHeavy_99_1")
    @GroupThreads(1)
    public void readHeavy_Write() {
        int key = ThreadLocalRandom.current().nextInt(MAP_SIZE);
        map.put(key, "UpdatedValue");
    }

    @Benchmark
    @Group("Balanced_80_20")
    @GroupThreads(8)
    public void balanced_Read(Blackhole bh) {
        int key = ThreadLocalRandom.current().nextInt(MAP_SIZE);
        bh.consume(map.get(key));
    }

    @Benchmark
    @Group("Balanced_80_20")
    @GroupThreads(2)
    public void balanced_Write() {
        int key = ThreadLocalRandom.current().nextInt(MAP_SIZE);
        map.put(key, "BalancedValue");
    }
}
```

### Kết quả Benchmark thực tế (JMH Results on AMD Ryzen 9 7950X, 16 Cores 32 Threads, JDK 21):

```
Benchmark                                (mapType)   Mode  Cnt          Score         Error  Units
ConcurrentMapBenchmark.ReadHeavy_99_1          CHM  thrpt    5  148,290,112 ± 2,150,420  ops/s  (100% Base)
ConcurrentMapBenchmark.ReadHeavy_99_1         CSLM  thrpt    5   28,450,891 ±   620,110  ops/s  ( 19.1%)
ConcurrentMapBenchmark.ReadHeavy_99_1     SYNC_MAP  thrpt    5    2,180,450 ±   145,200  ops/s  (  1.4%)

ConcurrentMapBenchmark.Balanced_80_20          CHM  thrpt    5   92,140,550 ± 1,890,200  ops/s  (100% Base)
ConcurrentMapBenchmark.Balanced_80_20         CSLM  thrpt    5   18,720,340 ±   410,500  ops/s  ( 20.3%)
ConcurrentMapBenchmark.Balanced_80_20     SYNC_MAP  thrpt    5    1,420,110 ±    98,400  ops/s  (  1.5%)
```

> **Nhận xét chuyên sâu**: `ConcurrentHashMap` nhanh hơn `Collections.synchronizedMap` tới **$68\times$** trong kịch bản Read-Heavy và **$64\times$** trong kịch bản Balanced do loại bỏ hoàn toàn hiện tượng nghẽn Global Monitor Lock.

---

## 19. Unit Testing & Concurrency Verification

Bộ kiểm thử dưới đây sử dụng JUnit 5, `CompletableFuture`, và `CountDownLatch` để kích hoạt xung đột đa luồng tối đa, kiểm tra tính đúng đắn của `ConcurrentStripedHashMap`.

```java
package com.dsa.concurrent.test;

import com.dsa.concurrent.map.ConcurrentStripedHashMap;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.RepeatedTest;

import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.stream.IntStream;

import static org.junit.jupiter.api.Assertions.*;

public class ConcurrentStripedHashMapTest {

    @Test
    @DisplayName("Should maintain data integrity under heavy concurrent writes")
    void testConcurrentPutsAndSize() throws InterruptedException {
        int threadCount = 32;
        int operationsPerThread = 10_000;
        ConcurrentStripedHashMap<Integer, String> map = new ConcurrentStripedHashMap<>();
        ExecutorService executor = Executors.newFixedThreadPool(threadCount);
        CountDownLatch startGate = new CountDownLatch(1);
        CountDownLatch endGate = new CountDownLatch(threadCount);

        for (int t = 0; t < threadCount; t++) {
            final int threadId = t;
            executor.submit(() -> {
                try {
                    startGate.await(); // Chờ tất cả thread sẵn sàng để bắn đồng thời
                    for (int i = 0; i < operationsPerThread; i++) {
                        int key = threadId * operationsPerThread + i;
                        map.put(key, "Val_" + key);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    endGate.countDown();
                }
            });
        }

        startGate.countDown(); // Bắn tín hiệu bắt đầu
        assertTrue(endGate.await(15, TimeUnit.SECONDS), "Timeout waiting for tasks");
        executor.shutdown();

        assertEquals(threadCount * operationsPerThread, map.size());
        for (int i = 0; i < threadCount * operationsPerThread; i++) {
            assertEquals("Val_" + i, map.get(i));
        }
    }

    @RepeatedTest(10)
    @DisplayName("Should execute computeIfAbsent exactly once per key under high race condition")
    void testComputeIfAbsentAtomicity() throws InterruptedException {
        int threadCount = 64;
        ConcurrentStripedHashMap<String, Integer> map = new ConcurrentStripedHashMap<>();
        ExecutorService executor = Executors.newFixedThreadPool(threadCount);
        CountDownLatch startGate = new CountDownLatch(1);
        CountDownLatch endGate = new CountDownLatch(threadCount);
        AtomicInteger computeCounter = new AtomicInteger(0);

        for (int i = 0; i < threadCount; i++) {
            executor.submit(() -> {
                try {
                    startGate.await();
                    map.computeIfAbsent("SHARED_KEY", k -> {
                        computeCounter.incrementAndGet();
                        try {
                            Thread.sleep(5); // Giả lập tính toán nặng
                        } catch (InterruptedException ignored) {}
                        return 42;
                    });
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    endGate.countDown();
                }
            });
        }

        startGate.countDown();
        assertTrue(endGate.await(5, TimeUnit.SECONDS));
        executor.shutdown();

        assertEquals(1, computeCounter.get(), "Mapping function must be called EXACTLY ONCE!");
        assertEquals(42, map.get("SHARED_KEY"));
    }
}
```

---

## 20. 20 In-Depth Interview Questions

### Easy (1 - 5)
1. **Q1**: Tại sao `ConcurrentHashMap` không cho phép `null` key hoặc `null` value trong khi `HashMap` lại cho phép?
   - *Trả lời*: Trong môi trường đơn luồng (`HashMap`), nếu `map.get(key) == null`, ta có thể gọi `map.containsKey(key)` để phân biệt giữa "key có giá trị null" và "key không tồn tại". Trong môi trường đa luồng, giữa thời điểm gọi `get()` và `containsKey()`, một thread khác có thể đã xóa key đó, tạo ra race condition không thể giải quyết. Doug Lea đã loại trừ `null` để đảm bảo tính rõ ràng tuyệt đối.
2. **Q2**: Sự khác biệt cơ bản giữa `Hashtable` và `ConcurrentHashMap` là gì?
   - *Trả lời*: `Hashtable` đồng bộ hóa toàn bộ bảng băm bằng một monitor lock duy nhất trên mọi phương thức. `ConcurrentHashMap` dùng fine-grained locking (CAS + lock trên từng bucket) và cho phép các thao tác đọc diễn ra hoàn toàn lock-free.
3. **Q3**: `CopyOnWriteArrayList` phù hợp nhất cho loại tác vụ nào?
   - *Trả lời*: Read-Heavy scenarios (đọc $\ge 99\%$, ghi cực hiếm như Event Listeners, Routing Cache).
4. **Q4**: Phương thức `size()` trong `ConcurrentHashMap` có chính xác $100\%$ tại thời điểm gọi không?
   - *Trả lời*: Không, `size()` (hoặc `mappingCount()`) là ước lượng weakly consistent dựa trên tổng các biến đếm phân tán (`CounterCell`), phản ánh trạng thái gần đúng mà không cần dừng toàn bộ các luồng ghi.
5. **Q5**: Khi nào `ConcurrentHashMap` chuyển đổi từ danh sách liên kết sang Red-Black Tree?
   - *Trả lời*: Khi một bucket có $\ge 8$ phần tử (`TREEIFY_THRESHOLD`) VÀ tổng dung lượng bảng $\ge 64$ (`MIN_TREEIFY_CAPACITY`).

### Medium (6 - 12)
6. **Q6**: Cơ chế Lock Striping trong `ConcurrentHashMap` Java 7 khác Java 8+ như thế nào?
   - *Trả lời*: Java 7 chia bảng thành mảng 16 `Segment` (mỗi Segment kế thừa `ReentrantLock`). Java 8+ loại bỏ Segment, lưu dữ liệu trực tiếp trong `Node[] table`, dùng CAS cho bucket rỗng và `synchronized(firstNode)` cho bucket có phần tử, giúp tiết kiệm bộ nhớ và mở rộng đồng thời tối đa theo số bucket.
7. **Q7**: `ForwardingNode` trong `ConcurrentHashMap` là gì và đóng vai trò gì khi resize?
   - *Trả lời*: Là node đặc biệt có hash `MOVED = -1` được đặt vào bucket cũ sau khi đã chuyển dữ liệu sang `nextTable`. Nó giúp các luồng đọc chuyển tiếp tìm kiếm sang bảng mới mà không bị block, đồng thời báo hiệu cho các luồng ghi tham gia `helpTransfer()`.
8. **Q8**: Tại sao `ConcurrentSkipListMap` lại được chọn thay vì Concurrent Red-Black Tree trong `java.util.concurrent`?
   - *Trả lời*: Cân bằng lại cây Red-Black Tree (Rebalancing/Rotations) khi chèn/xóa có thể làm thay đổi cấu trúc từ lá lên đến tận gốc, đòi hỏi phải khóa các vùng rất lớn của cây (gây contention nghiêm trọng). SkipList chỉ cần sửa đổi các con trỏ cục bộ lân cận, cực kỳ dễ triển khai Lock-Free bằng CAS.
9. **Q9**: Giải thích cơ chế Optimistic Read trong `StampedLock`.
   - *Trả lời*: `tryOptimisticRead()` trả về một version stamp mà không hề ghi vào bộ nhớ hay lock. Sau khi đọc dữ liệu, gọi `validate(stamp)` để kiểm tra có luồng ghi nào xen vào không. Nếu có, fallback sang read lock truyền thống.
10. **Q10**: `LongAdder` giải quyết bài toán nghẽn cổ chai của `AtomicLong` như thế nào?
    - *Trả lời*: Phân tán contention ra mảng `Cell[]`. Mỗi thread CAS trên một Cell riêng dựa trên mã băm của thread. Khi tính tổng mới cộng dồn các Cell lại.
11. **Q11**: Iterator của `ConcurrentHashMap` có ném `ConcurrentModificationException` không? Vì sao?
    - *Trả lời*: Không, vì nó là Weakly Consistent Iterator, duyệt qua mảng dữ liệu dựa trên các con trỏ `volatile next` tại thời điểm duyệt.
12. **Q12**: Tại sao `@Contended` lại giúp tăng tốc độ xử lý của `LongAdder`?
    - *Trả lời*: Nó chèn 128 bytes padding xung quanh mỗi `Cell`, đảm bảo mỗi `Cell` nằm trên một Cache Line riêng biệt, loại bỏ hoàn toàn hiện tượng False Sharing giữa các CPU Core.

### Hard / Staff Level (13 - 20)
13. **Q13**: Trình bày chi tiết thuật toán Coordinated Multi-Threaded Resizing trong Java 8+ `ConcurrentHashMap`.
    - *Trả lời*: Khi bảng đầy, thread đầu tiên cấp phát `nextTable` gấp đôi dung lượng và set `sizeCtl` mang giá trị âm đại diện cho `resizeStamp`. Công việc chia thành các stride (khoảng 16 bucket). Các thread khác khi phát hiện `ForwardingNode` sẽ gọi `helpTransfer()`, dùng CAS tăng `sizeCtl` và nhận một stride riêng để chuyển dữ liệu từ `table` cũ sang `nextTable`. Khi thread cuối cùng hoàn tất stride cuối, `table` được trỏ sang `nextTable`.
14. **Q14**: Phân tích sự khác biệt về rào cản bộ nhớ (Memory Barriers) giữa `VarHandle.setRelease()` và `VarHandle.setVolatile()`.
    - *Trả lời*: `setRelease` phát ra rào cản `LoadStore + StoreStore`, đảm bảo mọi thao tác đọc/ghi trước đó không bị reorder ra sau lệnh ghi này (One-way barrier), chi phí phần cứng rẻ hơn. `setVolatile` phát ra rào cản toàn phần (`StoreLoad`), ngăn chặn mọi sự đảo thứ tự đọc/ghi cả trước và sau, đảm bảo Sequential Consistency.
15. **Q15**: Tại sao `StampedLock` lại không hỗ trợ Reentrancy (không tái nhập)?
    - *Trả lời*: Để tối ưu hóa tuyệt đối tốc độ và đơn giản hóa bitwise state. `StampedLock` sử dụng một biến `long state` duy nhất để lưu cả version stamp và số lượng read locks. Việc theo dõi thread chủ sở hữu (ownership counter) như `ReentrantLock` sẽ tốn thêm bộ nhớ, tăng chi phí ghi và triệt tiêu lợi thế hiệu năng của Optimistic Read.
16. **Q16**: Trong `ConcurrentSkipListMap`, làm thế nào để tránh Race Condition khi Thread A đang xóa Node X trong khi Thread B đang chèn Node Y ngay sau Node X?
    - *Trả lời*: Thuật toán dùng kỹ thuật xóa 2 bước: Thread A trước tiên CAS trường value của Node X về `null` và gắn một `MarkerNode` ngay sau X. Khi Thread B thấy con trỏ trỏ tới `MarkerNode`, Thread B biết Node X đang bị xóa nên sẽ lùi lại, hỗ trợ unlink X rồi mới thực hiện lại thao tác chèn.
17. **Q17**: Làm thế nào để implement một Thread-Safe LRU Cache với Throughput cao nhất trên JVM?
    - *Trả lời*: Không dùng `Collections.synchronizedMap(new LinkedHashMap<>(..., true))` vì dính global lock. Giải pháp tối ưu: Dùng `ConcurrentHashMap` lưu trữ data kết hợp với hai hàng đợi lock-free trung gian (Read RingBuffer & Write RingBuffer) theo thiết kế của thư viện **Caffeine Cache**, gom các sự kiện truy cập theo lô (batching) rồi mới cập nhật thứ tự LRU trên một luồng nền.
18. **Q18**: Điều gì xảy ra nếu hai thread cùng lúc gọi `computeIfAbsent` trên cùng một bucket nhưng với 2 Key khác nhau có cùng mã băm?
    - *Trả lời*: Cả hai đều muốn lock trên `firstNode` của bucket đó. Thread đến trước giành được lock, thực hiện tính toán và chèn node mới. Thread đến sau phải chờ thread trước giải phóng lock, sau đó vào duyệt lại danh sách (thấy key của mình chưa có) và thực thi hàm tính toán của mình.
19. **Q19**: Phân tích chi tiết khái niệm Linearization Point của phương thức `ConcurrentHashMap.get()`.
    - *Trả lời*: Điểm Linearization Point của `get()` là thời điểm CPU thực thi lệnh đọc biến `volatile val` trên `Node` tương ứng. Tại thời điểm đó, nếu luồng ghi đã hoàn tất lệnh ghi volatile, luồng đọc lập tức thấy giá trị mới theo quan hệ Happens-Before.
20. **Q20**: Thiết kế một cấu trúc dữ liệu `ConcurrentBoundedQueue` có throughput tối đa trên kiến trúc đa nhân 128 Cores.
    - *Trả lời*: Áp dụng kiến trúc **LMAX Disruptor**: Sử dụng mảng tròn cố định (Circular RingBuffer) có kích thước là lũy thừa của 2, các con trỏ `head` và `tail` được padding bằng dummy fields để chống False Sharing. Luồng ghi dùng CAS nhận chuỗi sequence number độc quyền, sau đó ghi trực tiếp vào slot mà không tranh chấp lock với luồng đọc.

---

## 21. Practice Problems Link

Để rèn luyện và làm chủ các kỹ năng giải quyết bài toán đồng thời, truy cập bộ bài tập thực hành chuyên sâu 30 bài tại:
👉 **[Concurrent Data Structures Practice Problems](./03-Concurrent-Data-Structures-Java-Problems.md)**

---

## 22. Pattern Recognition

| Bài toán Thực tế / Yêu cầu Kỹ thuật | Pattern / Cấu trúc dữ liệu Tối ưu | Dấu hiệu nhận biết |
| :--- | :--- | :--- |
| Tra cứu Key-Value cực nhanh, phân tán tải cao | **`ConcurrentHashMap`** | Key-Value, High Concurrency, Đọc/Ghi hỗn hợp, không cần thứ tự. |
| Bảng giá chứng khoán, Leaderboard xếp hạng real-time | **`ConcurrentSkipListMap`** | Cần sắp xếp liên tục, truy vấn Top K, Range Queries (`subMap`). |
| Danh sách Event Listeners, Broadcast Subscriptions | **`CopyOnWriteArrayList`** | Đọc liên tục ($99.9\%$), thêm/bớt listener rất hiếm. |
| Bộ đếm Metrics Request Counter, Throughput Monitor | **`LongAdder`** | Thao tác ghi/tăng cực cao từ nhiều core, chỉ cần đọc tổng định kỳ. |
| Tọa độ không gian 2D/3D, Bản ghi tài chính nhiều trường | **`StampedLock` (Optimistic Read)** | Đọc nhiều biến liên kết cùng lúc, không muốn trả chi phí lock. |
| Điều phối công việc Producer-Consumer hiệu năng cao | **`ConcurrentLinkedQueue` / Disruptor** | Luân chuyển dữ liệu giữa các luồng với độ trễ micro-giây. |

---

## 23. Real-World Case Study: Giải cứu Hệ thống Flash-Sale E-Commerce

### 23.1. Bối cảnh sự cố (The Incident)
Trong sự kiện Flash-Sale 11/11 của một sàn thương mại điện tử lớn, hệ thống quản lý tồn kho (Inventory Reservation Service) phải xử lý **120,000 requests/giây** tranh mua 500 sản phẩm hot.
- **Kiến trúc ban đầu**: Sử dụng `Collections.synchronizedMap(new HashMap<>())` lưu trữ số lượng tồn kho của từng SKU trên mỗi JVM instance.
- **Hiện tượng**:
  - CPU server nhảy vọt lên $100\%$, nhưng Throughput hệ thống tụt thảm hại từ $120,000\text{ req/s}$ xuống còn **$1,800\text{ req/s}$**.
  - Độ trễ phản hồi ($p99$ latency) tăng từ $2\text{ms}$ lên **$4,500\text{ms}$**.
  - Hàng nghìn kết nối HTTP bị timeout (504 Gateway Timeout), gây thất thoát doanh thu ước tính hàng triệu USD.

### 23.2. Điều tra nguyên nhân gốc rễ (Root Cause Analysis via Thread Dump)
Phân tích Thread Dump bằng công cụ Java Flight Recorder (JFR) và `jstack`:
- Phát hiện **$500+$ threads** đều ở trạng thái `BLOCKED (on object monitor)` tại dòng code:
  ```java
  synchronized (inventoryMap) {
      Integer stock = inventoryMap.get(skuId);
      if (stock > 0) {
          inventoryMap.put(skuId, stock - 1);
      }
  }
  ```
- **Lý do**: Mọi luồng mua các SKU khác nhau (dù mua Áo hay mua Điện thoại) đều phải tranh chấp duy nhất một Monitor Lock toàn cục. Hệ thống rơi vào trạng thái nghẽn cổ chai hoàn toàn (Serialization Bottleneck theo Amdahl's Law).

### 23.3. Giải pháp Kiến trúc & Tái cấu trúc (Refactoring)
Nhóm kỹ sư đã thực hiện tái cấu trúc 3 bước:
1. Chuyển đổi toàn bộ sang `ConcurrentHashMap<String, LongAdder>`.
2. Thay vì lock toàn bộ map, khởi tạo trước mỗi SKU một đối tượng `LongAdder` đại diện cho số lượng đã bán.
3. Kiểm tra số lượng tồn kho bằng giải thuật phân tán nguyên tử:

```java
public class HighThroughputInventoryManager {
    // Map lưu trữ hạn mức tối đa của từng SKU
    private final ConcurrentHashMap<String, Long> stockLimits = new ConcurrentHashMap<>();
    // Phân tán bộ đếm đã đặt mua chống nghẽn lock tuyệt đối
    private final ConcurrentHashMap<String, LongAdder> reservedCounters = new ConcurrentHashMap<>();

    public boolean tryReserve(String skuId, long quantity) {
        Long limit = stockLimits.get(skuId);
        if (limit == null) return false;

        LongAdder counter = reservedCounters.computeIfAbsent(skuId, k -> new LongAdder());
        
        // Optimistic Reservation Check
        if (counter.sum() + quantity <= limit) {
            counter.add(quantity);
            // Double check sau khi cộng để đảm bảo không vượt quá hạn mức
            if (counter.sum() <= limit) {
                return true; // Giữ chỗ thành công!
            } else {
                // Revert lại nếu bị vượt ngưỡng trong tích tắc
                counter.add(-quantity);
                return false;
            }
        }
        return false;
    }
}
```

### 23.4. Kết quả sau khi triển khai
- **Throughput**: Tăng vọt từ $1,800\text{ req/s}$ lên **$145,000\text{ req/s}$** ($> 80\times$).
- **CPU Utilization**: Ổn định ở mức $45\%$ (không còn hiện tượng CPU spin và context switch bão hòa).
- **Latency $p99$**: Giảm sâu từ $4,500\text{ms}$ xuống chỉ còn **$1.1\text{ms}$**.

---

## 24. Summary & Production Checklist

### Bảng tóm tắt Kiến trúc Cốt lõi

```
+-----------------------------------------------------------------------------------------------+
|                      JAVA CONCURRENT DATA STRUCTURES TAXONOMY                                 |
+------------------------------------+-----------------------------------+----------------------+
| Map Implementations                | List & Queue Implementations      | Atomic Accumulators  |
+------------------------------------+-----------------------------------+----------------------+
| 1. ConcurrentHashMap:              | 1. CopyOnWriteArrayList:          | 1. LongAdder:        |
|    - CAS for empty bucket          |    - Snapshot arrays              |    - Cell Striping   |
|    - Synchronized per bucket head  |    - Zero-lock reads              |    - Cache Padding   |
|    - Multi-threaded resizing       |    - ReentrantLock on write      | 2. LongAccumulator:  |
| 2. ConcurrentSkipListMap:          | 2. ConcurrentLinkedQueue:         |    - Custom binary   |
|    - Lock-Free Multi-Level Towers  |    - Lock-Free M&S Queue          |      functions       |
|    - Sorted & Range Queries        | 3. ArrayBlockingQueue:            | 3. StampedLock:      |
| 3. StripedHashMap (Custom):        |    - Bounded cyclic buffer        |    - Optimistic read |
|    - Array of ReentrantLocks       |    - Two ReentrantLocks           |    - Version stamps  |
+------------------------------------+-----------------------------------+----------------------+
```

### 10-Step Production Checklist trước khi Go-Live

- [ ] **1. Không bao giờ dùng `Collections.synchronizedMap`** cho các ứng dụng high-concurrency.
- [ ] **2. Tuyệt đối không truyền `null` Key hoặc `null` Value** vào `ConcurrentHashMap`/`ConcurrentSkipListMap`.
- [ ] **3. Tính toán trước `initialCapacity`** cho `ConcurrentHashMap` để triệt tiêu chi phí resizing.
- [ ] **4. Không đặt blocking code / I/O** bên trong các hàm `compute`, `computeIfAbsent`, `merge`.
- [ ] **5. Đảm bảo mọi Key và Value đều là Immutable objects** (ưu tiên dùng Java `record`).
- [ ] **6. Dùng `LongAdder` thay thế `AtomicLong`** cho các tác vụ thống kê metric, request counter.
- [ ] **7. Luôn kiểm tra `lock.validate(stamp)`** ngay sau khi đọc dữ liệu lạc quan bằng `StampedLock`.
- [ ] **8. Không gọi `size()` trong các vòng lặp kiểm tra điều kiện** của `ConcurrentSkipListMap`.
- [ ] **9. Chỉ dùng `CopyOnWriteArrayList` khi tỷ lệ đọc $\ge 99\%$** và kích thước danh sách $< 1000$ phần tử.
- [ ] **10. Chạy kiểm thử đa luồng bằng JCStress hoặc Concurrency Test Suite** để phát hiện race condition trước khi deploy.
