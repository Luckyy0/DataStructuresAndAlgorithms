# Bài tập: Concurrent Data Structures trong Java (30 Bài toán Tiêu biểu)

---

## 1. Custom Lock-Striped Hash Map (StripedHashMap)

**Đề bài chi tiết:**
Thiết kế và cài đặt một bảng băm đa luồng an toàn (**Thread-Safe Striped Hash Map**) không sử dụng `ConcurrentHashMap` hay đồng bộ hóa toàn cục (`synchronized` toàn bộ map).
Bảng băm cần hỗ trợ:
- `V put(K key, V value)`: Thêm hoặc cập nhật cặp key-value. Trả về giá trị cũ nếu có, hoặc `null`.
- `V get(Object key)`: Lấy giá trị tương ứng với key, trả về `null` nếu không tìm thấy.
- `V remove(Object key)`: Xóa key khỏi map và trả về giá trị đã xóa.
- `int size()`: Trả về tổng số phần tử trong map tại thời điểm gọi.
- `void clear()`: Xóa toàn bộ phần tử trong bảng băm.

**Yêu cầu kỹ thuật:**
- Áp dụng kỹ thuật **Lock Striping** (phân dải khóa): Chia không gian bucket thành $S$ phân vùng (stripes), mỗi stripe được bảo vệ bởi một `ReentrantLock` độc lập.
- Khi thực hiện các thao tác trên một key (`put`, `get`, `remove`), chỉ khóa đúng stripe tương ứng với hash của key đó để các luồng truy cập các stripe khác nhau diễn ra hoàn toàn song song.
- Thao tác `size()` và `clear()` phải khóa toàn bộ các stripe theo thứ tự chỉ số tăng dần (Index-Ordered Locking) nhằm loại bỏ nguy cơ **Deadlock** (Livelock / Lock-ordering Inversion).

**Phân tích thuật toán:**
1. **Lock Striping Invariant**: Giả sử số lượng khóa là $S$ (thường là lũy thừa của 2, ví dụ 16 hoặc 32). Stripe index cho một key được tính bằng: `stripeIndex = (hash(key) & 0x7FFFFFFF) % S`. Bucket index được tính bằng: `bucketIndex = (hash(key) & 0x7FFFFFFF) % capacity`.
2. **Deadlock Prevention**: Khi một thao tác cần chiếm nhiều hơn một khóa (như `size()`, `clear()`, hoặc rehash), thuật toán bắt buộc phải yêu cầu các khóa theo thứ tự nghiêm ngặt từ `locks[0]` đến `locks[S-1]`. Thao tác giải phóng khóa (`unlock`) trong khối `finally` theo thứ tự ngược lại hoặc thuận đều an toàn một khi tất cả khóa đã được chiếm hữu.
3. **Memory Visibility**: Mỗi bucket là một danh sách liên kết đơn gồm các `Node<K, V>`. Thuộc tính `value` và `next` được khai báo `volatile` để đảm bảo hiện tượng Visibility (Happens-Before Relationship) khi đọc/ghi qua các luồng.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.locks.ReentrantLock;

public class StripedHashMap<K, V> {
    
    private static final int DEFAULT_CAPACITY = 64;
    private static final int DEFAULT_STRIPES = 16;
    
    static class Node<K, V> {
        final K key;
        volatile V value;
        volatile Node<K, V> next;
        final int hash;

        Node(K key, V value, int hash, Node<K, V> next) {
            this.key = key;
            this.value = value;
            this.hash = hash;
            this.next = next;
        }
    }

    private final Node<K, V>[] table;
    private final int capacity;
    private final int numStripes;
    private final ReentrantLock[] locks;

    @SuppressWarnings("unchecked")
    public StripedHashMap(int capacity, int numStripes) {
        this.capacity = capacity;
        this.numStripes = numStripes;
        this.table = (Node<K, V>[]) new Node[capacity];
        this.locks = new ReentrantLock[numStripes];
        for (int i = 0; i < numStripes; i++) {
            this.locks[i] = new ReentrantLock();
        }
    }

    public StripedHashMap() {
        this(DEFAULT_CAPACITY, DEFAULT_STRIPES);
    }

    private int spreadHash(Object key) {
        if (key == null) return 0;
        int h = key.hashCode();
        return h ^ (h >>> 16);
    }

    private int getStripeIndex(int hash) {
        return (hash & 0x7FFFFFFF) % numStripes;
    }

    private int getBucketIndex(int hash) {
        return (hash & 0x7FFFFFFF) % capacity;
    }

    public V put(K key, V value) {
        Objects.requireNonNull(key, "Key cannot be null");
        int hash = spreadHash(key);
        int stripeIdx = getStripeIndex(hash);
        int bucketIdx = getBucketIndex(hash);

        ReentrantLock lock = locks[stripeIdx];
        lock.lock();
        try {
            Node<K, V> head = table[bucketIdx];
            for (Node<K, V> curr = head; curr != null; curr = curr.next) {
                if (curr.hash == hash && (curr.key == key || curr.key.equals(key))) {
                    V oldVal = curr.value;
                    curr.value = value;
                    return oldVal;
                }
            }
            table[bucketIdx] = new Node<>(key, value, hash, head);
            return null;
        } finally {
            lock.unlock();
        }
    }

    public V get(Object key) {
        Objects.requireNonNull(key, "Key cannot be null");
        int hash = spreadHash(key);
        int stripeIdx = getStripeIndex(hash);
        int bucketIdx = getBucketIndex(hash);

        ReentrantLock lock = locks[stripeIdx];
        lock.lock();
        try {
            for (Node<K, V> curr = table[bucketIdx]; curr != null; curr = curr.next) {
                if (curr.hash == hash && (curr.key == key || curr.key.equals(key))) {
                    return curr.value;
                }
            }
            return null;
        } finally {
            lock.unlock();
        }
    }

    public V remove(Object key) {
        Objects.requireNonNull(key, "Key cannot be null");
        int hash = spreadHash(key);
        int stripeIdx = getStripeIndex(hash);
        int bucketIdx = getBucketIndex(hash);

        ReentrantLock lock = locks[stripeIdx];
        lock.lock();
        try {
            Node<K, V> prev = null;
            for (Node<K, V> curr = table[bucketIdx]; curr != null; prev = curr, curr = curr.next) {
                if (curr.hash == hash && (curr.key == key || curr.key.equals(key))) {
                    if (prev == null) {
                        table[bucketIdx] = curr.next;
                    } else {
                        prev.next = curr.next;
                    }
                    return curr.value;
                }
            }
            return null;
        } finally {
            lock.unlock();
        }
    }

    public int size() {
        for (ReentrantLock lock : locks) {
            lock.lock();
        }
        try {
            int count = 0;
            for (int i = 0; i < capacity; i++) {
                for (Node<K, V> curr = table[i]; curr != null; curr = curr.next) {
                    count++;
                }
            }
            return count;
        } finally {
            for (int i = locks.length - 1; i >= 0; i--) {
                locks[i].unlock();
            }
        }
    }

    public void clear() {
        for (ReentrantLock lock : locks) {
            lock.lock();
        }
        try {
            for (int i = 0; i < capacity; i++) {
                table[i] = null;
            }
        } finally {
            for (int i = locks.length - 1; i >= 0; i--) {
                locks[i].unlock();
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - `put`, `get`, `remove`: Trung bình $O(1 + N / C)$, trong đó $N$ là số lượng phần tử, $C$ là dung lượng bucket array. Với $S$ stripes, độ tranh chấp giữa $T$ luồng giảm $S$ lần so với Global Lock.
  - `size`, `clear`: $O(C + N)$ do phải quét qua toàn bộ bucket sau khi đã chiếm $S$ locks.
- **Không gian (Space Complexity):** $O(C + S + N)$, bao gồm mảng bucket, $S$ đối tượng `ReentrantLock`, và $N$ node lưu trữ key-value.

---

## 2. Concurrent LRU Cache with StampedLock

**Đề bài chi tiết:**
Xây dựng cấu trúc bộ nhớ đệm **Least Recently Used (LRU) Cache** có tính an toàn đa luồng cao (**High-Throughput Concurrent LRU Cache**) với dung lượng tối đa $capacity$.
Các thao tác:
- `V get(K key)`: Trả về giá trị của key nếu tồn tại và đưa phần tử này lên đầu danh sách truy cập gần nhất (MRU - Most Recently Used). Nếu không có, trả về `null`.
- `void put(K key, V value)`: Thêm hoặc cập nhật key. Nếu dung lượng vượt quá $capacity$, loại bỏ phần tử ít được sử dụng nhất (LRU - Least Recently Used) ở cuối danh sách.

**Yêu cầu kỹ thuật:**
- Sử dụng `StampedLock` để tối ưu hóa hiệu năng đọc thông qua cơ chế **Optimistic Reading** (Đọc lạc quan không chặn - Non-blocking Optimistic Read).
- Phải kết hợp giữa bảng băm định vị nhanh (`Map<K, Node<K, V>>`) và danh sách liên kết đôi (**Doubly Linked List**) quản lý thứ tự LRU.

**Phân tích thuật toán:**
1. **Optimistic Read Path**: Khi thực hiện `get(key)`, luồng lấy một optimistic stamp thông qua `lock.tryOptimisticRead()`. Luồng đọc con trỏ Node từ Hash Map. Nếu hợp lệ (`lock.validate(stamp)`), luồng nâng cấp lên Write Lock để cập nhật vị trí Node lên đầu DLL (`moveToHead`).
2. **DLL Synchronization**: Mọi thao tác biến đổi liên kết trên Doubly Linked List (`addToHead`, `removeNode`, `removeTail`) phải được bảo vệ bởi Write Lock của `StampedLock` để tránh hiện tượng con trỏ đứt gãy giữa chừng.
3. **Capacity Invariant**: Thao tác `put` kiểm tra kích thước map. Nếu vượt quá giới hạn, node đuôi (`tail.prev`) được tách khỏi DLL và xóa khỏi map dưới sự bảo vệ của Write Lock.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.HashMap;
import java.util.Map;
import java.util.Objects;
import java.util.concurrent.locks.StampedLock;

public class ConcurrentLRUCache<K, V> {
    
    static class Node<K, V> {
        final K key;
        volatile V value;
        Node<K, V> prev;
        Node<K, V> next;

        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    private final int capacity;
    private final Map<K, Node<K, V>> map;
    private final Node<K, V> head;
    private final Node<K, V> tail;
    private final StampedLock lock = new StampedLock();

    public ConcurrentLRUCache(int capacity) {
        if (capacity <= 0) {
            throw new IllegalArgumentException("Capacity must be positive");
        }
        this.capacity = capacity;
        this.map = new HashMap<>(capacity);
        this.head = new Node<>(null, null);
        this.tail = new Node<>(null, null);
        this.head.next = this.tail;
        this.tail.prev = this.head;
    }

    private void addFirst(Node<K, V> node) {
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }

    private void unlink(Node<K, V> node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(Node<K, V> node) {
        unlink(node);
        addFirst(node);
    }

    private Node<K, V> removeTail() {
        Node<K, V> lru = tail.prev;
        if (lru == head) return null;
        unlink(lru);
        return lru;
    }

    public V get(K key) {
        Objects.requireNonNull(key, "Key cannot be null");
        long stamp = lock.tryOptimisticRead();
        Node<K, V> node = map.get(key);
        V val = (node != null) ? node.value : null;

        if (!lock.validate(stamp)) {
            stamp = lock.writeLock();
            try {
                node = map.get(key);
                if (node == null) return null;
                moveToHead(node);
                return node.value;
            } finally {
                lock.unlockWrite(stamp);
            }
        } else if (node != null) {
            stamp = lock.writeLock();
            try {
                moveToHead(node);
                return node.value;
            } finally {
                lock.unlockWrite(stamp);
            }
        }
        return val;
    }

    public void put(K key, V value) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(value, "Value cannot be null");

        long stamp = lock.writeLock();
        try {
            Node<K, V> existing = map.get(key);
            if (existing != null) {
                existing.value = value;
                moveToHead(existing);
            } else {
                if (map.size() >= capacity) {
                    Node<K, V> lru = removeTail();
                    if (lru != null) {
                        map.remove(lru.key);
                    }
                }
                Node<K, V> newNode = new Node<>(key, value);
                map.put(key, newNode);
                addFirst(newNode);
            }
        } finally {
            lock.unlockWrite(stamp);
        }
    }

    public int size() {
        long stamp = lock.readLock();
        try {
            return map.size();
        } finally {
            lock.unlockRead(stamp);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - `get`: $O(1)$ trung bình. Trong kịch bản đọc áp đảo, optimistic read không tạo ra memory barrier ghi hay bus locking, cho phép throughput vượt trội.
  - `put`: $O(1)$ với thao tác băm và nối pointer DLL.
- **Không gian (Space Complexity):** $O(\text{capacity})$ lưu trữ các đối tượng `Node` và mục trong `HashMap`.

---

## 3. Thread-Safe Bounded Blocking Queue (CustomArrayBlockingQueue)

**Đề bài chi tiết:**
Tự thiết kế cấu trúc hàng đợi chặn có giới hạn dung lượng (**Bounded Blocking FIFO Queue**) tương tự `ArrayBlockingQueue` trong gói `java.util.concurrent`.
Hỗ trợ các phương thức:
- `void put(E e)`: Chèn phần tử vào đuôi hàng đợi. Nếu hàng đợi đầy, luồng gọi phải bị block (chờ) cho tới khi có chỗ trống.
- `E take()`: Lấy và xóa phần tử ở đầu hàng đợi. Nếu hàng đợi rỗng, luồng gọi phải bị block cho tới khi có phần tử xuất hiện.
- `boolean offer(E e, long timeout, TimeUnit unit)`: Chèn phần tử có timeout.
- `E poll(long timeout, TimeUnit unit)`: Lấy phần tử có timeout.
- `int size()`: Trả về số lượng phần tử hiện có.

**Yêu cầu kỹ thuật:**
- Dùng một mảng tĩnh tròn (**Circular Ring Array**) để lưu trữ phần tử.
- Sử dụng đúng một `ReentrantLock` kết hợp với **2 Condition Variables** (`notFull`, `notEmpty`).
- Xử lý đúng đắn tín hiệu đánh thức (`signal()`) và ngắt luồng (`InterruptedException`).

**Phân tích thuật toán:**
1. **Dual Conditions**: 
   - `notEmpty`: Luồng `take()` chờ trên điều kiện này khi `count == 0`. Luồng `put()` sau khi thêm phần tử sẽ gọi `notEmpty.signal()`.
   - `notFull`: Luồng `put()` chờ trên điều kiện này khi `count == items.length`. Luồng `take()` sau khi lấy phần tử sẽ gọi `notFull.signal()`.
2. **Circular Indexing**: Con trỏ `putIndex` và `takeIndex` tịnh tiến vòng quanh mảng: `putIndex = (putIndex + 1 == capacity) ? 0 : putIndex + 1`.
3. **Signal vs SignalAll**: Do mỗi thao tác `put` chỉ giải phóng đúng 1 chỗ trống cho 1 luồng tiêu thụ và ngược lại, việc sử dụng `signal()` đơn lẻ giúp tránh hiện tượng **Thundering Herd Problem** (hàng loạt luồng cùng thức dậy tranh chấp vô ích).

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class CustomArrayBlockingQueue<E> {
    
    private final Object[] items;
    private int takeIndex;
    private int putIndex;
    private int count;

    private final ReentrantLock lock;
    private final Condition notEmpty;
    private final Condition notFull;

    public CustomArrayBlockingQueue(int capacity, boolean fair) {
        if (capacity <= 0) {
            throw new IllegalArgumentException("Capacity must be greater than 0");
        }
        this.items = new Object[capacity];
        this.lock = new ReentrantLock(fair);
        this.notEmpty = lock.newCondition();
        this.notFull = lock.newCondition();
    }

    public CustomArrayBlockingQueue(int capacity) {
        this(capacity, false);
    }

    private void enqueue(E x) {
        items[putIndex] = x;
        if (++putIndex == items.length) {
            putIndex = 0;
        }
        count++;
        notEmpty.signal();
    }

    @SuppressWarnings("unchecked")
    private E dequeue() {
        E x = (E) items[takeIndex];
        items[takeIndex] = null; // Giúp Garbage Collection thu hồi
        if (++takeIndex == items.length) {
            takeIndex = 0;
        }
        count--;
        notFull.signal();
        return x;
    }

    public void put(E e) throws InterruptedException {
        Objects.requireNonNull(e, "Element cannot be null");
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length) {
                notFull.await();
            }
            enqueue(e);
        } finally {
            lock.unlock();
        }
    }

    public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0) {
                notEmpty.await();
            }
            return dequeue();
        } finally {
            lock.unlock();
        }
    }

    public boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException {
        Objects.requireNonNull(e);
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length) {
                if (nanos <= 0L) return false;
                nanos = notFull.awaitNanos(nanos);
            }
            enqueue(e);
            return true;
        } finally {
            lock.unlock();
        }
    }

    public E poll(long timeout, TimeUnit unit) throws InterruptedException {
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0) {
                if (nanos <= 0L) return null;
                nanos = notEmpty.awaitNanos(nanos);
            }
            return dequeue();
        } finally {
            lock.unlock();
        }
    }

    public int size() {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho mọi thao tác `put`, `take`, `offer`, `poll`.
- **Không gian (Space Complexity):** $O(\text{capacity})$ bộ nhớ tĩnh cấp phát trước cho mảng `items`, không phát sinh rác (Zero GC allocation) trong quá trình tuần hoàn phần tử.

---

## 4. Lock-Free Treiber Stack with Exponential Backoff

**Đề bài chi tiết:**
Cài đặt ngăn xếp không khóa (**Lock-Free Stack**) dựa trên thuật toán Treiber Stack cổ điển. Để ngăn chặn hiện tượng nghẽn bus hệ thống và suy giảm thông lượng (Cache-line Bouncing / Bus Saturation) khi có hàng trăm luồng cùng tranh chấp CAS đỉnh ngăn xếp, hãy tích hợp cơ chế **Exponential Backoff**.
Các phương thức:
- `void push(E item)`: Đẩy một phần tử vào đỉnh ngăn xếp.
- `E pop()`: Lấy và xóa phần tử ở đỉnh ngăn xếp, trả về `null` nếu rỗng.
- `boolean isEmpty()`: Kiểm tra ngăn xếp rỗng.

**Phân tích thuật toán:**
1. **Treiber Stack Model**: Ngăn xếp là một danh sách liên kết đơn với con trỏ `AtomicReference<Node<E>> top`.
   - `push`: Tạo `newNode`, gán `newNode.next = top.get()`, sau đó dùng CAS `top.compareAndSet(oldTop, newNode)`.
   - `pop`: Đọc `oldTop = top.get()`. Nếu rỗng trả về null. Ngược lại dùng CAS `top.compareAndSet(oldTop, oldTop.next)`.
2. **Exponential Backoff Strategy**: Khi một luồng thất bại trong thao tác CAS, nghĩa là có luồng khác vừa ghi thành công vào cùng một Cache Line. Luồng thất bại sẽ tạm dừng (`Thread.sleep(0, nanos)` hoặc spin-loop với độ trễ tăng theo hàm mũ giữa $minDelay$ và $maxDelay$) trước khi thử lại CAS, giúp phân tán xung đột thời gian.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.Random;
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.locks.LockSupport;

public class LockFreeTreiberStack<E> {

    static class Node<E> {
        final E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }

    static class Backoff {
        private final int minDelayNanos;
        private final int maxDelayNanos;
        private int currentDelay;
        private final Random random = new Random();

        Backoff(int minDelayNanos, int maxDelayNanos) {
            this.minDelayNanos = minDelayNanos;
            this.maxDelayNanos = maxDelayNanos;
            this.currentDelay = minDelayNanos;
        }

        void backoff() {
            int delay = random.nextInt(currentDelay);
            currentDelay = Math.min(maxDelayNanos, 2 * currentDelay);
            LockSupport.parkNanos(delay);
        }

        void reset() {
            currentDelay = minDelayNanos;
        }
    }

    private final AtomicReference<Node<E>> top = new AtomicReference<>(null);

    public void push(E item) {
        Objects.requireNonNull(item, "Item cannot be null");
        Node<E> newNode = new Node<>(item);
        Backoff backoff = new Backoff(100, 100_000);

        while (true) {
            Node<E> oldTop = top.get();
            newNode.next = oldTop;
            if (top.compareAndSet(oldTop, newNode)) {
                return;
            }
            backoff.backoff();
        }
    }

    public E pop() {
        Backoff backoff = new Backoff(100, 100_000);
        while (true) {
            Node<E> oldTop = top.get();
            if (oldTop == null) {
                return null;
            }
            Node<E> newTop = oldTop.next;
            if (top.compareAndSet(oldTop, newTop)) {
                return oldTop.item;
            }
            backoff.backoff();
        }
    }

    public boolean isEmpty() {
        return top.get() == null;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - Kịch bản lý tưởng không tranh chấp: $O(1)$.
  - Dưới tranh chấp cao: $O(1)$ amortized cho mỗi tiến trình thành công (Lock-Free progress guarantee: luôn có ít nhất một luồng hoàn thành nhiệm vụ trong số bước hữu hạn).
- **Không gian (Space Complexity):** $O(N)$ bộ nhớ cho $N$ đối tượng `Node`.

---

## 5. Lock-Free Michael-Scott Queue

**Đề bài chi tiết:**
Cài đặt hàng đợi FIFO không khóa (**Lock-Free FIFO Queue**) chuẩn theo thuật toán kinh điển của Maged M. Michael và Michael L. Scott (nền tảng của `ConcurrentLinkedQueue` trong JDK).
Cung cấp các API:
- `void enqueue(E item)`: Đưa phần tử vào cuối hàng đợi.
- `E dequeue()`: Rút phần tử khỏi đầu hàng đợi, trả về `null` nếu rỗng.
- `boolean isEmpty()`: Kiểm tra trạng thái hàng đợi.

**Phân tích thuật toán:**
1. **Sentinel Node (Dummy Head)**: Khởi tạo hàng đợi với một node giả định `head = tail = new Node(null)`.
2. **Two-Step Enqueue Invariant**:
   - Bước 1: Luồng tìm đuôi thực sự `tail` và dùng CAS để gắn node mới vào `tail.next`.
   - Bước 2: Cập nhật lại con trỏ `tail` trỏ tới `newNode`. Nếu luồng khác thấy `tail.next != null` trước khi luồng hiện tại kịp cập nhật `tail`, luồng đó sẽ hỗ trợ (**Helping Scheme**) đẩy `tail` tiến lên bằng CAS trước khi tiếp tục thao tác của mình.
3. **Dequeue Step**: Đọc `head` và `head.next`. Dùng CAS dịch `head` sang `head.next`, lấy dữ liệu tại `head.next.item` và xóa giá trị để tránh memory leak.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.atomic.AtomicReference;

public class MichaelScottQueue<E> {

    static class Node<E> {
        final E item;
        final AtomicReference<Node<E>> next;

        Node(E item) {
            this.item = item;
            this.next = new AtomicReference<>(null);
        }
    }

    private final AtomicReference<Node<E>> head;
    private final AtomicReference<Node<E>> tail;

    public MichaelScottQueue() {
        Node<E> dummy = new Node<>(null);
        this.head = new AtomicReference<>(dummy);
        this.tail = new AtomicReference<>(dummy);
    }

    public void enqueue(E item) {
        Objects.requireNonNull(item, "Item cannot be null");
        Node<E> newNode = new Node<>(item);

        while (true) {
            Node<E> curTail = tail.get();
            Node<E> tailNext = curTail.next.get();

            if (curTail == tail.get()) { // Kiểm tra tính nhất quán
                if (tailNext != null) {
                    // Tail đang tụt lại phía sau, hỗ trợ đẩy tail tiến lên
                    tail.compareAndSet(curTail, tailNext);
                } else {
                    // Cố gắng gắn newNode vào cuối danh sách
                    if (curTail.next.compareAndSet(null, newNode)) {
                        // Thành công: cố gắng cập nhật tail trỏ tới newNode
                        tail.compareAndSet(curTail, newNode);
                        return;
                    }
                }
            }
        }
    }

    public E dequeue() {
        while (true) {
            Node<E> curHead = head.get();
            Node<E> curTail = tail.get();
            Node<E> headNext = curHead.next.get();

            if (curHead == head.get()) {
                if (curHead == curTail) {
                    if (headNext == null) {
                        return null; // Hàng đợi rỗng
                    }
                    // Tail tụt lại phía sau head, hỗ trợ đẩy tail
                    tail.compareAndSet(curTail, headNext);
                } else {
                    if (headNext == null) continue;
                    E val = headNext.item;
                    if (head.compareAndSet(curHead, headNext)) {
                        return val;
                    }
                }
            }
        }
    }

    public boolean isEmpty() {
        return head.get().next.get() == null;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ trung bình cho cả `enqueue` và `dequeue`. Lock-Free và miễn nhiễm với Deadlock.
- **Không gian (Space Complexity):** $O(N)$ cho các đối tượng Node liên kết.

---

## 6. Lock-Free Concurrent SkipList Map

**Đề bài chi tiết:**
Cài đặt cấu trúc dữ liệu **Lock-Free SkipList Map** lưu trữ các phần tử có thứ tự (`K extends Comparable<K>`).
Yêu cầu các phương thức:
- `boolean put(K key, V value)`: Thêm hoặc cập nhật key-value theo thứ tự sắp xếp.
- `V get(K key)`: Tìm kiếm giá trị tương ứng trong thời gian $O(\log N)$.
- `boolean remove(K key)`: Xóa key khỏi danh sách không khóa sử dụng kỹ thuật đánh dấu logic (**Logical Deletion** với `AtomicMarkableReference`).

**Phân tích thuật toán:**
1. **Harris Lock-Free Linked List**: Để xóa một node mà không dùng khóa, quá trình được chia thành 2 giai đoạn:
   - **Giai đoạn 1 (Logical Delete)**: Dùng CAS đánh dấu trường `next` của node mục tiêu thành `marked = true`. Sau bước này, node vẫn nằm trong danh sách nhưng không còn hợp lệ.
   - **Giai đoạn 2 (Physical Delete)**: Luồng đi ngang qua (kể cả luồng search/insert) phát hiện node bị đánh dấu sẽ dùng CAS trỏ con trỏ `predecessor.next` vượt qua node bị đánh dấu để giải phóng nó về mặt vật lý.
2. **SkipList Multi-Level Hierarchy**: Mỗi tầng là một Lock-Free linked list. Thao tác tìm kiếm bắt đầu từ tầng cao nhất $MAX\_LEVEL - 1$ đi xuống, xác định cặp `[predecessor, successor]` tại mỗi tầng trước khi thực hiện CAS chèn node mới.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.Random;
import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreeSkipList<K extends Comparable<K>, V> {

    private static final int MAX_LEVEL = 16;
    private static final double PROBABILITY = 0.5;

    static class Node<K, V> {
        final K key;
        volatile V value;
        final AtomicMarkableReference<Node<K, V>>[] next;
        final int topLevel;

        @SuppressWarnings("unchecked")
        Node(K key, V value, int height) {
            this.key = key;
            this.value = value;
            this.topLevel = height;
            this.next = (AtomicMarkableReference<Node<K, V>>[]) new AtomicMarkableReference[height + 1];
            for (int i = 0; i < height; i++) {
                this.next[i] = new AtomicMarkableReference<>(null, false);
            }
            this.next[height] = new AtomicMarkableReference<>(null, false);
        }
    }

    private final Node<K, V> head;
    private final Random random = new Random();

    public LockFreeSkipList() {
        this.head = new Node<>(null, null, MAX_LEVEL);
    }

    private int randomLevel() {
        int lvl = 0;
        while (lvl < MAX_LEVEL - 1 && random.nextDouble() < PROBABILITY) {
            lvl++;
        }
        return lvl;
    }

    @SuppressWarnings("unchecked")
    private boolean find(K key, Node<K, V>[] preds, Node<K, V>[] succs) {
        int bottomLevel = 0;
        boolean[] marked = new boolean[1];

        retryLabel:
        while (true) {
            Node<K, V> pred = head;
            for (int level = MAX_LEVEL - 1; level >= bottomLevel; level--) {
                Node<K, V> curr = pred.next[level].getReference();
                while (true) {
                    if (curr == null) break;
                    Node<K, V> succ = curr.next[level].get(marked);
                    
                    while (marked[0]) {
                        boolean snip = pred.next[level].compareAndSet(curr, succ, false, false);
                        if (!snip) continue retryLabel;
                        curr = pred.next[level].getReference();
                        if (curr == null) break;
                        succ = curr.next[level].get(marked);
                    }

                    if (curr.key != null && curr.key.compareTo(key) < 0) {
                        pred = curr;
                        curr = succ;
                    } else {
                        break;
                    }
                }
                preds[level] = pred;
                succs[level] = curr;
            }
            return (succs[bottomLevel] != null && succs[bottomLevel].key != null && succs[bottomLevel].key.compareTo(key) == 0);
        }
    }

    public boolean put(K key, V value) {
        Objects.requireNonNull(key);
        Objects.requireNonNull(value);
        int topLevel = randomLevel();
        @SuppressWarnings("unchecked")
        Node<K, V>[] preds = (Node<K, V>[]) new Node[MAX_LEVEL];
        @SuppressWarnings("unchecked")
        Node<K, V>[] succs = (Node<K, V>[]) new Node[MAX_LEVEL];

        while (true) {
            boolean found = find(key, preds, succs);
            if (found) {
                succs[0].value = value;
                return false;
            }
            Node<K, V> newNode = new Node<>(key, value, topLevel);
            for (int level = 0; level <= topLevel; level++) {
                newNode.next[level].set(succs[level], false);
            }
            Node<K, V> pred = preds[0];
            Node<K, V> succ = succs[0];
            if (!pred.next[0].compareAndSet(succ, newNode, false, false)) {
                continue;
            }
            for (int level = 1; level <= topLevel; level++) {
                while (true) {
                    pred = preds[level];
                    succ = succs[level];
                    if (pred.next[level].compareAndSet(succ, newNode, false, false)) {
                        break;
                    }
                    find(key, preds, succs);
                }
            }
            return true;
        }
    }

    public V get(K key) {
        Objects.requireNonNull(key);
        boolean[] marked = new boolean[1];
        Node<K, V> pred = head;
        for (int level = MAX_LEVEL - 1; level >= 0; level--) {
            Node<K, V> curr = pred.next[level].getReference();
            while (curr != null) {
                Node<K, V> succ = curr.next[level].get(marked);
                while (marked[0]) {
                    curr = succ;
                    if (curr == null) break;
                    succ = curr.next[level].get(marked);
                }
                if (curr != null && curr.key != null) {
                    int cmp = curr.key.compareTo(key);
                    if (cmp < 0) {
                        pred = curr;
                        curr = succ;
                    } else if (cmp == 0) {
                        return curr.value;
                    } else {
                        break;
                    }
                }
            }
        }
        return null;
    }

    public boolean remove(K key) {
        Objects.requireNonNull(key);
        @SuppressWarnings("unchecked")
        Node<K, V>[] preds = (Node<K, V>[]) new Node[MAX_LEVEL];
        @SuppressWarnings("unchecked")
        Node<K, V>[] succs = (Node<K, V>[]) new Node[MAX_LEVEL];
        boolean[] marked = new boolean[1];

        while (true) {
            boolean found = find(key, preds, succs);
            if (!found) return false;
            Node<K, V> victim = succs[0];
            for (int level = victim.topLevel; level >= 1; level--) {
                Node<K, V> succ = victim.next[level].get(marked);
                while (!marked[0]) {
                    victim.next[level].compareAndSet(succ, succ, false, true);
                    succ = victim.next[level].get(marked);
                }
            }
            Node<K, V> succ = victim.next[0].get(marked);
            while (!marked[0]) {
                boolean markedSuccess = victim.next[0].compareAndSet(succ, succ, false, true);
                succ = victim.next[0].get(marked);
                if (markedSuccess) {
                    find(key, preds, succs);
                    return true;
                }
            }
            return false;
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log N)$ trung bình cho `get`, `put`, `remove`. Lock-free, giải quyết được vấn đề tranh chấp điểm nóng.
- **Không gian (Space Complexity):** $O(N)$ với hệ số nhân theo chiều cao trung bình $\frac{1}{1 - p} \approx 2$ tham chiếu trên mỗi node.

---

## 7. Thread-Safe Multi-Map with Copy-On-Write Values

**Đề bài chi tiết:**
Thiết kế cấu trúc đa ánh xạ an toàn luồng (**Concurrent Multi-Map**) tối ưu hóa cho hệ thống có tần suất đọc cực cao (Read-Heavy), trong đó một key có thể gắn với một tập hợp nhiều giá trị duy nhất (Set of values).
Các phương thức:
- `boolean put(K key, V value)`: Thêm value vào tập hợp tương ứng với key. Trả về `true` nếu value chưa từng tồn tại.
- `boolean remove(K key, V value)`: Xóa một value cụ thể khỏi key. Nếu tập hợp giá trị của key trở nên rỗng, tự động loại bỏ key khỏi map chính.
- `Set<V> get(K key)`: Trả về một **Snapshot Set** bất biến (Unmodifiable/Immutable) chứa tất cả giá trị của key tại thời điểm đọc với chi phí $O(1)$.
- `boolean containsEntry(K key, V value)`: Kiểm tra sự tồn tại của cặp (key, value).

**Phân tích thuật toán:**
1. **Concurrent Layering**: Sử dụng `ConcurrentHashMap<K, CopyOnWriteArraySet<V>>`. 
2. **Atomic Container Creation**: Dùng `computeIfAbsent(key, k -> new CopyOnWriteArraySet<>())` để tạo mới tập hợp giá trị một cách an toàn mà không làm mất dữ liệu của các luồng ghi đồng thời.
3. **Atomic Clean-up**: Khi tập hợp giá trị rỗng sau thao tác `remove`, sử dụng `map.computeIfPresent(key, ...)` để xóa key khỏi map chính chỉ khi kích thước set thực sự bằng 0, ngăn chặn tình trạng luồng khác vừa thêm value vào thì key bị xóa mất.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Collections;
import java.util.Objects;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArraySet;

public class ConcurrentCopyOnWriteMultiMap<K, V> {

    private final ConcurrentHashMap<K, CopyOnWriteArraySet<V>> map = new ConcurrentHashMap<>();

    public boolean put(K key, V value) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(value, "Value cannot be null");

        CopyOnWriteArraySet<V> set = map.computeIfAbsent(key, k -> new CopyOnWriteArraySet<>());
        return set.add(value);
    }

    public boolean remove(K key, V value) {
        Objects.requireNonNull(key, "Key cannot be null");
        Objects.requireNonNull(value, "Value cannot be null");

        CopyOnWriteArraySet<V> set = map.get(key);
        if (set == null) return false;

        boolean removed = set.remove(value);
        if (removed) {
            map.computeIfPresent(key, (k, existingSet) -> existingSet.isEmpty() ? null : existingSet);
        }
        return removed;
    }

    public Set<V> get(K key) {
        Objects.requireNonNull(key, "Key cannot be null");
        CopyOnWriteArraySet<V> set = map.get(key);
        if (set == null || set.isEmpty()) {
            return Collections.emptySet();
        }
        return Collections.unmodifiableSet(set);
    }

    public boolean containsEntry(K key, V value) {
        Objects.requireNonNull(key);
        Objects.requireNonNull(value);
        CopyOnWriteArraySet<V> set = map.get(key);
        return set != null && set.contains(value);
    }

    public int keyCount() {
        return map.size();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - `get`: $O(1)$ trả về tham chiếu snapshot.
  - `put`, `remove`: $O(M)$ trong đó $M$ là số lượng phần tử trong set của key đó. Thích hợp với $M$ nhỏ và tỷ lệ đọc > 95%.
- **Không gian (Space Complexity):** $O(K + \sum M_i)$ lưu trữ Map và các mảng con.

---

## 8. Scalable Concurrent Counter with Striped Cells (LongAdder from Scratch)

**Đề bài chi tiết:**
Cài đặt bộ đếm đa luồng hiệu năng cao (**Striped Cell Counter**) từ con số 0, mô phỏng cơ chế của `java.util.concurrent.atomic.LongAdder`.
Yêu cầu:
- `void add(long x)`: Cộng thêm giá trị $x$.
- `void increment()`: Tăng bộ đếm lên 1.
- `long sum()`: Trả về tổng tích lũy hiện tại của tất cả các luồng.
- `void reset()`: Đặt lại toàn bộ các ô đếm về 0.

**Yêu cầu kỹ thuật:**
- Khắc phục triệt để hiện tượng suy giảm hiệu năng do tranh chấp bộ nhớ (**Cache Line Bouncing / False Sharing**).
- Thiết kế mảng `Cell` tự động co giãn kích thước khi phát hiện xung đột CAS liên tiếp.

**Phân tích thuật toán:**
1. **Base Value & Cell Array**: Duy trì một biến `volatile long base`. Nếu không có tranh chấp, luồng cập nhật trực tiếp `base` bằng CAS.
2. **Thread Hash Striping**: Khi CAS trên `base` thất bại, luồng sẽ băm mã định danh luồng (`Thread.currentThread().threadId()`) để chọn một ô trong mảng `Cell[] cells`.
3. **Cache Line Padding**: Mỗi `Cell` cần có dung lượng chiếm trọn một Cache Line (64 bytes trên x86_64) để tránh hai luồng ghi vào hai ô khác nhau nhưng nằm chung một đường truyền cache dẫn đến hiện tượng **False Sharing**.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class StripedCellCounter {

    static final class Cell {
        volatile long p0, p1, p2, p3, p4, p5, p6; // 56 bytes padding
        volatile long value;
        volatile long q0, q1, q2, q3, q4, q5, q6; // 56 bytes padding

        Cell(long val) {
            this.value = val;
        }

        final boolean cas(long cmp, long val) {
            return VALUE_HANDLE.compareAndSet(this, cmp, val);
        }

        private static final VarHandle VALUE_HANDLE;
        static {
            try {
                MethodHandles.Lookup l = MethodHandles.lookup();
                VALUE_HANDLE = l.findVarHandle(Cell.class, "value", long.class);
            } catch (ReflectiveOperationException e) {
                throw new ExceptionInInitializerError(e);
            }
        }
    }

    private static final VarHandle BASE_HANDLE;
    private static final VarHandle CELLS_BUSY_HANDLE;

    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            BASE_HANDLE = l.findVarHandle(StripedCellCounter.class, "base", long.class);
            CELLS_BUSY_HANDLE = l.findVarHandle(StripedCellCounter.class, "cellsBusy", int.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    private volatile transient Cell[] cells;
    private volatile transient long base;
    private volatile transient int cellsBusy;

    private static final int NCPU = Runtime.getRuntime().availableProcessors();

    private boolean casBase(long cmp, long val) {
        return BASE_HANDLE.compareAndSet(this, cmp, val);
    }

    private boolean casCellsBusy() {
        return CELLS_BUSY_HANDLE.compareAndSet(this, 0, 1);
    }

    public void add(long x) {
        Cell[] as; long b, v; int m; Cell a;
        if ((as = cells) != null || !casBase(b = base, b + x)) {
            boolean uncontended = true;
            int h = (int) Thread.currentThread().threadId();
            h ^= h >>> 16;
            if (as == null || (m = as.length - 1) < 0 ||
                (a = as[h & m]) == null ||
                !(uncontended = a.cas(v = a.value, v + x))) {
                longAccumulate(x, h, uncontended);
            }
        }
    }

    private void longAccumulate(long x, int h, boolean wasUncontended) {
        boolean collide = false;
        while (true) {
            Cell[] as; Cell a; int n; long v;
            if ((as = cells) != null && (n = as.length) > 0) {
                if ((a = as[(n - 1) & h]) == null) {
                    if (cellsBusy == 0) {
                        Cell r = new Cell(x);
                        if (cellsBusy == 0 && casCellsBusy()) {
                            boolean created = false;
                            try {
                                Cell[] rs; int mask, j;
                                if ((rs = cells) != null && (mask = rs.length) > 0 &&
                                    rs[j = (mask - 1) & h] == null) {
                                    rs[j] = r;
                                    created = true;
                                }
                            } finally {
                                cellsBusy = 0;
                            }
                            if (created) break;
                            continue;
                        }
                    }
                    collide = false;
                } else if (!wasUncontended) {
                    wasUncontended = true;
                } else if (a.cas(v = a.value, v + x)) {
                    break;
                } else if (n >= NCPU || cells != as) {
                    collide = false;
                } else if (!collide) {
                    collide = true;
                } else if (cellsBusy == 0 && casCellsBusy()) {
                    try {
                        if (cells == as) {
                            Cell[] rs = new Cell[n << 1];
                            System.arraycopy(as, 0, rs, 0, n);
                            cells = rs;
                        }
                    } finally {
                        cellsBusy = 0;
                    }
                    collide = false;
                    continue;
                }
                h ^= h << 13;
                h ^= h >>> 17;
                h ^= h << 5;
            } else if (cellsBusy == 0 && cells == as && casCellsBusy()) {
                boolean init = false;
                try {
                    if (cells == as) {
                        Cell[] rs = new Cell[2];
                        rs[h & 1] = new Cell(x);
                        cells = rs;
                        init = true;
                    }
                } finally {
                    cellsBusy = 0;
                }
                if (init) break;
            } else if (casBase(v = base, v + x)) {
                break;
            }
        }
    }

    public void increment() {
        add(1L);
    }

    public long sum() {
        Cell[] as = cells;
        long sum = base;
        if (as != null) {
            for (Cell a : as) {
                if (a != null) {
                    sum += a.value;
                }
            }
        }
        return sum;
    }

    public void reset() {
        Cell[] as = cells;
        base = 0L;
        if (as != null) {
            for (Cell a : as) {
                if (a != null) {
                    a.value = 0L;
                }
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - `add`/`increment`: $O(1)$ gần như tuyệt đối, khả năng scale tuyến tính theo số core CPU.
  - `sum`: $O(\text{Number of Cells}) \le O(\text{NCPU})$.
- **Không gian (Space Complexity):** $O(\text{NCPU} \times 128\text{ bytes})$ để đảm bảo cache alignment.

---

## 9. High-Performance Disruptor-Style Ring Buffer

**Đề bài chi tiết:**
Thiết kế bộ đệm vòng tròn (**High-Performance Ring Buffer**) hiệu năng siêu cao lấy cảm hứng từ kiến trúc LMAX Disruptor dành cho mô hình Multi-Producer Multi-Consumer (MPMC).
Các phương thức:
- `boolean publish(E item)`: Nhà sản xuất đẩy sự kiện vào bộ đệm.
- `E poll()`: Nhà tiêu dùng lấy sự kiện ra khỏi bộ đệm.
- `long capacity()`: Dung lượng cố định của Ring Buffer (phải là lũy thừa của 2).

**Phân tích thuật toán:**
1. **Sequence Claiming via CAS**: Quản lý con trỏ tuần tự `cursor` (chỉ số ghi) và `gatingSequence` (chỉ số đọc) dạng số nguyên 64-bit tăng đơn điệu không bao giờ tràn.
2. **Bitwise Modulo**: Chỉ số thực tế trong mảng được tính bằng phép toán bit: `index = sequence & (capacity - 1)`.
3. **Cache Line Alignment**: Tạo các class bọc sequence với padding trường để cô lập hoàn toàn biến đếm của Producer và Consumer trên các dòng L1/L2 cache độc lập.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.Objects;

public class DisruptorRingBuffer<E> {

    static class PaddedSequence {
        volatile long p1, p2, p3, p4, p5, p6, p7;
        volatile long value;
        volatile long q1, q2, q3, q4, q5, q6, q7;

        PaddedSequence(long initialValue) {
            this.value = initialValue;
        }

        public long get() {
            return value;
        }

        public boolean compareAndSet(long expected, long update) {
            return VALUE_HANDLE.compareAndSet(this, expected, update);
        }

        private static final VarHandle VALUE_HANDLE;
        static {
            try {
                VALUE_HANDLE = MethodHandles.lookup().findVarHandle(PaddedSequence.class, "value", long.class);
            } catch (ReflectiveOperationException e) {
                throw new ExceptionInInitializerError(e);
            }
        }
    }

    private final Object[] entries;
    private final int bufferSize;
    private final int indexMask;

    private final PaddedSequence producerSequence = new PaddedSequence(-1L);
    private final PaddedSequence consumerSequence = new PaddedSequence(-1L);

    public DisruptorRingBuffer(int bufferSize) {
        if (Integer.bitCount(bufferSize) != 1) {
            throw new IllegalArgumentException("Buffer size must be a power of 2");
        }
        this.bufferSize = bufferSize;
        this.indexMask = bufferSize - 1;
        this.entries = new Object[bufferSize];
    }

    public boolean publish(E item) {
        Objects.requireNonNull(item, "Item cannot be null");
        while (true) {
            long currentProducer = producerSequence.get();
            long nextProducer = currentProducer + 1;
            long currentConsumer = consumerSequence.get();

            if (nextProducer - currentConsumer > bufferSize) {
                return false; // Buffer Full
            }

            if (producerSequence.compareAndSet(currentProducer, nextProducer)) {
                int index = (int) (nextProducer & indexMask);
                entries[index] = item;
                return true;
            }
        }
    }

    @SuppressWarnings("unchecked")
    public E poll() {
        while (true) {
            long currentConsumer = consumerSequence.get();
            long nextConsumer = currentConsumer + 1;
            long currentProducer = producerSequence.get();

            if (nextConsumer > currentProducer) {
                return null; // Buffer Empty
            }

            if (consumerSequence.compareAndSet(currentConsumer, nextConsumer)) {
                int index = (int) (nextConsumer & indexMask);
                E item = (E) entries[index];
                entries[index] = null;
                return item;
            }
        }
    }

    public int capacity() {
        return bufferSize;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho cả `publish` và `poll`. Không sử dụng Kernel Lock, không làm block luồng gọi.
- **Không gian (Space Complexity):** $O(\text{bufferSize})$ mảng định kích thước trước, triệt tiêu Garbage Collector pause.

---

## 10. Scalable Concurrent Rate Limiter (Token Bucket with Atomic CAS)

**Đề bài chi tiết:**
Thiết kế bộ điều phối tốc độ truy cập (**Scalable Lock-Free Token Bucket Rate Limiter**) kiểm soát lưu lượng request đa luồng mà không sử dụng bất kỳ cơ chế khóa chặn (`synchronized`/`ReentrantLock`) nào.
Cung cấp các phương thức:
- `boolean tryAcquire(int tokens)`: Cố gắng lấy $tokens$ đơn vị tài nguyên. Trả về `true` nếu thành công, `false` nếu không đủ token.
- `boolean tryAcquire()`: Tương đương `tryAcquire(1)`.

**Phân tích thuật toán:**
1. **Packed State Invariant**: Sử dụng đối tượng bất biến `TokenState(long lastRefillNanos, double availableTokens)` được cập nhật nguyên tử thông qua `AtomicReference<TokenState>`.
2. **Lazy Token Replenishment**: Khi có luồng yêu cầu token, thuật toán tính khoảng thời gian trôi qua `now - lastRefillNanos`, nhân với tốc độ tạo token (`refillRatePerNano`), sau đó cộng thêm vào `availableTokens` (tối đa bằng `maxCapacity`).
3. **Atomic CAS Loop**: Luồng thử trừ token và dùng CAS để cập nhật `TokenState`. Nếu thất bại do luồng khác ghi đè, vòng lặp tự động đọc trạng thái mới nhất và tính toán lại mà không bị gián đoạn.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.concurrent.atomic.AtomicReference;

public class TokenBucketRateLimiter {

    private record State(long lastRefillNanos, double tokens) {}

    private final double maxTokens;
    private final double refillRatePerNano;
    private final AtomicReference<State> state;

    public TokenBucketRateLimiter(double tokensPerSecond, double maxBurstTokens) {
        if (tokensPerSecond <= 0 || maxBurstTokens <= 0) {
            throw new IllegalArgumentException("Rates and capacity must be positive");
        }
        this.maxTokens = maxBurstTokens;
        this.refillRatePerNano = tokensPerSecond / 1_000_000_000.0;
        this.state = new AtomicReference<>(new State(System.nanoTime(), maxBurstTokens));
    }

    public boolean tryAcquire(int requestedTokens) {
        if (requestedTokens <= 0) {
            throw new IllegalArgumentException("Requested tokens must be positive");
        }

        while (true) {
            long now = System.nanoTime();
            State current = state.get();

            long elapsedNanos = Math.max(0L, now - current.lastRefillNanos());
            double newTokens = Math.min(maxTokens, current.tokens() + elapsedNanos * refillRatePerNano);

            if (newTokens < requestedTokens) {
                return false;
            }

            State next = new State(now, newTokens - requestedTokens);
            if (state.compareAndSet(current, next)) {
                return true;
            }
        }
    }

    public boolean tryAcquire() {
        return tryAcquire(1);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho mỗi thao tác `tryAcquire`.
- **Không gian (Space Complexity):** $O(1)$ không gian bộ nhớ.

---

## 11. Concurrent Trie / Autocomplete with Fine-Grained Locking

**Đề bài chi tiết:**
Cài đặt cây tiền tố đa luồng (**Concurrent Trie / Prefix Tree**) hỗ trợ tính năng tự động gợi ý từ khóa (**Autocomplete**) dưới tải đọc/ghi đồng thời cực lớn.
Các phương thức:
- `void insert(String word)`: Thêm một từ vào cây tiền tố.
- `boolean search(String word)`: Tìm kiếm chính xác từ có tồn tại trong Trie hay không.
- `boolean startsWith(String prefix)`: Kiểm tra có từ nào bắt đầu bằng tiền tố `prefix` hay không.
- `List<String> autocomplete(String prefix, int limit)`: Trả về danh sách tối đa `limit` từ khớp với tiền tố.

**Phân tích thuật toán:**
1. **Concurrent Node Branching**: Mỗi `TrieNode` sử dụng một `ConcurrentHashMap<Character, TrieNode>` để quản lý các nhánh con, loại bỏ khóa tập trung ở root.
2. **Volatile End-of-Word**: Cờ `volatile boolean isEndOfWord` và biến tham chiếu `volatile String fullWord` đảm bảo tính nhìn thấy (Memory Visibility) ngay khi thao tác `insert` hoàn tất.
3. **Snapshot DFS Traversal**: Thao tác `autocomplete` định vị node gốc của tiền tố, sau đó thực hiện duyệt DFS lấy snapshot danh sách kết quả.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentTrie {

    static class TrieNode {
        final ConcurrentHashMap<Character, TrieNode> children = new ConcurrentHashMap<>();
        volatile boolean isEndOfWord = false;
        volatile String word = null;
    }

    private final TrieNode root = new TrieNode();

    public void insert(String word) {
        Objects.requireNonNull(word, "Word cannot be null");
        TrieNode curr = root;
        for (int i = 0; i < word.length(); i++) {
            char ch = word.charAt(i);
            curr = curr.children.computeIfAbsent(ch, c -> new TrieNode());
        }
        curr.word = word;
        curr.isEndOfWord = true;
    }

    public boolean search(String word) {
        Objects.requireNonNull(word);
        TrieNode node = findPrefixNode(word);
        return node != null && node.isEndOfWord;
    }

    public boolean startsWith(String prefix) {
        Objects.requireNonNull(prefix);
        return findPrefixNode(prefix) != null;
    }

    private TrieNode findPrefixNode(String prefix) {
        TrieNode curr = root;
        for (int i = 0; i < prefix.length(); i++) {
            char ch = prefix.charAt(i);
            curr = curr.children.get(ch);
            if (curr == null) return null;
        }
        return curr;
    }

    public List<String> autocomplete(String prefix, int limit) {
        Objects.requireNonNull(prefix);
        List<String> results = new ArrayList<>();
        TrieNode prefixNode = findPrefixNode(prefix);
        if (prefixNode == null || limit <= 0) {
            return results;
        }

        dfsCollect(prefixNode, results, limit);
        return results;
    }

    private void dfsCollect(TrieNode node, List<String> results, int limit) {
        if (node == null || results.size() >= limit) return;
        if (node.isEndOfWord && node.word != null) {
            results.add(node.word);
            if (results.size() >= limit) return;
        }
        for (TrieNode child : node.children.values()) {
            dfsCollect(child, results, limit);
            if (results.size() >= limit) return;
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):**
  - `insert`, `search`, `startsWith`: $O(L)$ với $L$ là độ dài chuỗi ký tự.
  - `autocomplete`: $O(L + K)$ với $K$ là số lượng node con được duyệt trong subtree.
- **Không gian (Space Complexity):** $O(\Sigma \cdot N \cdot L)$ với $\Sigma$ là bảng chữ cái.

---

## 12. Lock-Free Concurrent Disjoint Set Union (DSU with Atomic CAS)

**Đề bài chi tiết:**
Cài đặt cấu trúc tập hợp rời rạc không khóa (**Concurrent Union-Find / DSU**) sử dụng các phép toán CAS nguyên tử.
Các API:
- `int find(int i)`: Tìm đại diện (root) của phần tử $i$ có kết hợp nén đường đi không khóa (**Lock-Free Path Halving / Path Splitting**).
- `boolean union(int i, int j)`: Hợp nhất hai tập hợp chứa $i$ và $j$ dựa theo thứ bậc (**Union by Rank**).
- `boolean connected(int i, int j)`: Kiểm tra hai phần tử có thuộc cùng một thành phần liên thông hay không.

**Phân tích thuật toán:**
1. **Lock-Free Path Halving**: Khi tìm root của node $x$, thay vì đệ quy nén toàn bộ, ta cập nhật $x$ trỏ thẳng tới node ông nội (`parent.compareAndSet(x, p, gp)`) trong vòng lặp `while`. Điều này đảm bảo an toàn tuyệt đối ngay cả khi có luồng khác đang thay đổi cây.
2. **Lock-Free Union**: Tìm root của 2 phần tử $r1 = find(i)$ và $r2 = find(j)$. Dùng CAS gán `parent[r1] = r2` nếu `rank[r1] < rank[r2]`. Nếu CAS thất bại, thực hiện lại vòng lặp tìm kiếm.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.concurrent.atomic.AtomicIntegerArray;

public class ConcurrentUnionFind {

    private final AtomicIntegerArray parent;
    private final AtomicIntegerArray rank;

    public ConcurrentUnionFind(int size) {
        this.parent = new AtomicIntegerArray(size);
        this.rank = new AtomicIntegerArray(size);
        for (int i = 0; i < size; i++) {
            parent.set(i, i);
            rank.set(i, 0);
        }
    }

    public int find(int i) {
        while (true) {
            int p = parent.get(i);
            if (p == i) return i;
            int gp = parent.get(p);
            parent.compareAndSet(i, p, gp);
            i = gp;
        }
    }

    public boolean union(int i, int j) {
        while (true) {
            int rootI = find(i);
            int rootJ = find(j);
            if (rootI == rootJ) return false;

            int rankI = rank.get(rootI);
            int rankJ = rank.get(rootJ);

            if (rankI < rankJ) {
                if (parent.compareAndSet(rootI, rootI, rootJ)) {
                    return true;
                }
            } else if (rankI > rankJ) {
                if (parent.compareAndSet(rootJ, rootJ, rootI)) {
                    return true;
                }
            } else {
                if (parent.compareAndSet(rootJ, rootJ, rootI)) {
                    rank.compareAndSet(rootI, rankI, rankI + 1);
                    return true;
                }
            }
        }
    }

    public boolean connected(int i, int j) {
        return find(i) == find(j);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\alpha(N))$ gần như $O(1)$ cho mọi thao tác.
- **Không gian (Space Complexity):** $O(N)$ cho hai mảng `AtomicIntegerArray`.

---

## 13. Thread-Safe Read-Heavy Expiring Cache (TTL Cache with DelayQueue)

**Đề bài chi tiết:**
Xây dựng một bộ nhớ đệm đa luồng hỗ trợ tự động hết hạn theo thời gian sống (**Time-To-Live Cache**).
Các phương thức:
- `void put(K key, V value, long ttlMillis)`: Ghi cặp key-value với thời gian sống $ttlMillis$.
- `V get(K key)`: Lấy giá trị nếu chưa hết hạn, nếu đã hết hạn trả về `null` và dọn dẹp bộ nhớ.
- `void cleanUpExpiredEntries()`: Chủ động quét và dọn dẹp các mục hết hạn thông qua cấu trúc `DelayQueue`.

**Phân tích thuật toán:**
1. **Lazy Eviction**: Khi gọi `get(key)`, so sánh `System.currentTimeMillis()` với `expireAt`. Nếu đã quá hạn, xóa key khỏi map bằng `map.remove(key, entry)` và trả về `null`.
2. **Active Eviction with DelayQueue**: Mỗi lần `put`, tạo một `DelayedKey<K>` đẩy vào `DelayQueue`. Một luồng nền liên tục gọi `delayQueue.poll()` để giải phóng bộ nhớ của các key đã hết hạn mà không cần luồng ngoài đọc tới.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.*;

public class ConcurrentExpiringCache<K, V> {

    record CacheEntry<V>(V value, long expireAtMillis) {
        boolean isExpired() {
            return System.currentTimeMillis() > expireAtMillis;
        }
    }

    record ExpiringKey<K>(K key, long expireAtMillis) implements Delayed {
        @Override
        public long getDelay(TimeUnit unit) {
            long diff = expireAtMillis - System.currentTimeMillis();
            return unit.convert(diff, TimeUnit.MILLISECONDS);
        }

        @Override
        public int compareTo(Delayed o) {
            return Long.compare(this.expireAtMillis, ((ExpiringKey<?>) o).expireAtMillis);
        }
    }

    private final ConcurrentHashMap<K, CacheEntry<V>> map = new ConcurrentHashMap<>();
    private final DelayQueue<ExpiringKey<K>> delayQueue = new DelayQueue<>();

    public void put(K key, V value, long ttlMillis) {
        Objects.requireNonNull(key);
        Objects.requireNonNull(value);
        if (ttlMillis <= 0) return;

        long expireAt = System.currentTimeMillis() + ttlMillis;
        CacheEntry<V> newEntry = new CacheEntry<>(value, expireAt);
        map.put(key, newEntry);
        delayQueue.offer(new ExpiringKey<>(key, expireAt));
    }

    public V get(K key) {
        Objects.requireNonNull(key);
        CacheEntry<V> entry = map.get(key);
        if (entry == null) return null;

        if (entry.isExpired()) {
            map.remove(key, entry);
            return null;
        }
        return entry.value();
    }

    public void cleanUpExpiredEntries() {
        ExpiringKey<K> delayedKey;
        while ((delayedKey = delayQueue.poll()) != null) {
            K key = delayedKey.key();
            CacheEntry<V> entry = map.get(key);
            if (entry != null && entry.isExpired()) {
                map.remove(key, entry);
            }
        }
    }

    public int size() {
        cleanUpExpiredEntries();
        return map.size();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `get`, $O(\log M)$ cho `put` (do chèn vào Heap của `DelayQueue`).
- **Không gian (Space Complexity):** $O(N)$ lưu trữ trong `ConcurrentHashMap` và `DelayQueue`.

---

## 14. Concurrent Bounded Priority Queue with Lock Striping

**Đề bài chi tiết:**
Thiết kế hàng đợi ưu tiên đa luồng có giới hạn dung lượng (**Concurrent Bounded Priority Queue**) sử dụng kỹ thuật phân dải khóa trên các mức ưu tiên cố định (Fixed Priority Buckets).
Các phương thức:
- `boolean offer(E item, int priority)`: Chèn phần tử với mức ưu tiên $priority \in [0, P - 1]$.
- `E poll()`: Lấy phần tử có mức ưu tiên cao nhất từ trước đến nay.

**Phân tích thuật toán:**
1. **Partitioned Buckets**: Hàng đợi gồm $P$ danh sách liên kết con, mỗi danh sách đại diện cho một mức độ ưu tiên và được bảo vệ bởi một `ReentrantLock` riêng biệt.
2. **Non-blocking Polling**: Thao tác `poll()` quét từ bucket có độ ưu tiên cao nhất $P - 1$ xuống $0$. Tại mỗi bucket, luồng cố gắng chiếm khóa bằng `tryLock()`. Nếu bucket có dữ liệu, lấy phần tử và giải phóng khóa ngay lập tức.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.LinkedList;
import java.util.Objects;
import java.util.concurrent.locks.ReentrantLock;

public class ConcurrentBoundedPriorityQueue<E> {

    private final int numPriorities;
    private final LinkedList<E>[] buckets;
    private final ReentrantLock[] locks;

    @SuppressWarnings("unchecked")
    public ConcurrentBoundedPriorityQueue(int numPriorities) {
        this.numPriorities = numPriorities;
        this.buckets = (LinkedList<E>[]) new LinkedList[numPriorities];
        this.locks = new ReentrantLock[numPriorities];
        for (int i = 0; i < numPriorities; i++) {
            this.buckets[i] = new LinkedList<>();
            this.locks[i] = new ReentrantLock();
        }
    }

    public boolean offer(E item, int priority) {
        Objects.requireNonNull(item);
        if (priority < 0 || priority >= numPriorities) {
            throw new IllegalArgumentException("Priority out of range: " + priority);
        }

        ReentrantLock lock = locks[priority];
        lock.lock();
        try {
            return buckets[priority].add(item);
        } finally {
            lock.unlock();
        }
    }

    public E poll() {
        for (int p = numPriorities - 1; p >= 0; p--) {
            ReentrantLock lock = locks[p];
            if (lock.tryLock()) {
                try {
                    if (!buckets[p].isEmpty()) {
                        return buckets[p].poll();
                    }
                } finally {
                    lock.unlock();
                }
            }
        }
        return null;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `offer`, $O(P)$ cho `poll` trong trường hợp xấu nhất.
- **Không gian (Space Complexity):** $O(N + P)$.

---

## 15. Thread-Safe Interval Map (Range Query Map)

**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu bản đồ khoảng (**Concurrent Interval Map**) cho phép chèn và truy vấn các đoạn số nguyên $[start, end]$ cùng giá trị tương ứng.
Các phương thức:
- `void put(long start, long end, V value)`: Thêm đoạn khoảng $[start, end]$ với điều kiện $start \le end$.
- `List<V> queryOverlapping(long qStart, long qEnd)`: Trả về danh sách tất cả các giá trị của các đoạn giao nhau với $[qStart, qEnd]$.

**Phân tích thuật toán:**
1. **Concurrent SkipList Map Backend**: Lưu trữ các mốc bắt đầu trong `ConcurrentSkipListMap<IntervalKey, V>`.
2. **ReentrantReadWriteLock Guard**: Sử dụng `ReentrantReadWriteLock` cho các thao tác tìm kiếm giao điểm để đảm bảo tính nhất quán của tập dữ liệu trong khi vẫn cho phép nhiều luồng đọc đồng thời.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.*;
import java.util.concurrent.ConcurrentSkipListMap;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentIntervalMap<V> {

    public record Interval(long start, long end) implements Comparable<Interval> {
        @Override
        public int compareTo(Interval o) {
            int cmp = Long.compare(this.start, o.start);
            return (cmp != 0) ? cmp : Long.compare(this.end, o.end);
        }

        public boolean overlaps(long qStart, long qEnd) {
            return this.start <= qEnd && this.end >= qStart;
        }
    }

    private final ConcurrentSkipListMap<Interval, V> map = new ConcurrentSkipListMap<>();
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    public void put(long start, long end, V value) {
        if (start > end) throw new IllegalArgumentException("start must be <= end");
        Objects.requireNonNull(value);

        rwLock.writeLock().lock();
        try {
            map.put(new Interval(start, end), value);
        } finally {
            rwLock.writeLock().unlock();
        }
    }

    public List<V> queryOverlapping(long qStart, long qEnd) {
        if (qStart > qEnd) throw new IllegalArgumentException("qStart must be <= qEnd");

        rwLock.readLock().lock();
        try {
            List<V> results = new ArrayList<>();
            for (Map.Entry<Interval, V> entry : map.entrySet()) {
                Interval interval = entry.getKey();
                if (interval.start > qEnd) {
                    break;
                }
                if (interval.overlaps(qStart, qEnd)) {
                    results.add(entry.getValue());
                }
            }
            return results;
        } finally {
            rwLock.readLock().unlock();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log N)$ cho `put`, $O(\log N + K)$ cho `queryOverlapping` ($K$ là số đoạn giao).
- **Không gian (Space Complexity):** $O(N)$ lưu trữ các node trong SkipList.

---

## 16. Thread-Safe Dynamic Circular Queue with ReentrantReadWriteLock

**Đề bài chi tiết:**
Cài đặt hàng đợi tròn đa luồng có khả năng tự động tăng kích thước gấp đôi (**Dynamically Resizing Concurrent Circular Queue**) khi đầy dữ liệu.
Các phương thức:
- `void enqueue(E item)`: Thêm phần tử vào cuối. Nếu mảng đầy, kích hoạt cơ chế cấp phát lại (Resize).
- `E dequeue()`: Lấy phần tử ở đầu, trả về `null` nếu rỗng.
- `int size()`: Trả về số lượng phần tử.

**Phân tích thuật toán:**
1. **Read/Write Locking Strategy**: Thao tác `enqueue` thông thường và `dequeue` chỉ yêu cầu Read Lock để truy cập và biến đổi các con trỏ `head`/`tail` được bảo vệ nguyên tử.
2. **Write Lock Upgrade for Resizing**: Khi phát hiện mảng đầy, luồng nhả Read Lock và chiếm Write Lock độc quyền để tạo mảng mới có kích thước gấp đôi và sao chép các phần tử tuần tự.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentDynamicCircularQueue<E> {

    private Object[] elements;
    private int head = 0;
    private int tail = 0;
    private int size = 0;
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    public ConcurrentDynamicCircularQueue(int initialCapacity) {
        if (initialCapacity <= 0) throw new IllegalArgumentException();
        this.elements = new Object[initialCapacity];
    }

    public void enqueue(E item) {
        Objects.requireNonNull(item);
        rwLock.writeLock().lock();
        try {
            if (size == elements.length) {
                resize(elements.length * 2);
            }
            elements[tail] = item;
            tail = (tail + 1) % elements.length;
            size++;
        } finally {
            rwLock.writeLock().unlock();
        }
    }

    @SuppressWarnings("unchecked")
    public E dequeue() {
        rwLock.writeLock().lock();
        try {
            if (size == 0) return null;
            E val = (E) elements[head];
            elements[head] = null;
            head = (head + 1) % elements.length;
            size--;
            return val;
        } finally {
            rwLock.writeLock().unlock();
        }
    }

    private void resize(int newCapacity) {
        Object[] newElements = new Object[newCapacity];
        for (int i = 0; i < size; i++) {
            newElements[i] = elements[(head + i) % elements.length];
        }
        this.elements = newElements;
        this.head = 0;
        this.tail = size;
    }

    public int size() {
        rwLock.readLock().lock();
        try {
            return size;
        } finally {
            rwLock.readLock().unlock();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** Amortized $O(1)$ cho `enqueue` và `dequeue`.
- **Không gian (Space Complexity):** $O(N)$ cho mảng lưu trữ.

---

## 17. Lock-Free Elimination Array Backoff Stack

**Đề bài chi tiết:**
Cài đặt ngăn xếp không khóa có khả năng mở rộng cực cao (**Elimination Backoff Stack**) theo thuật toán của Hendler, Shavit và Yerushalmi.
Các phương thức:
- `void push(E item)`
- `E pop()`

**Phân tích thuật toán:**
1. **Elimination Technique**: Nếu một luồng `push(X)` và một luồng `pop()` cùng truy cập ngăn xếp tại cùng một thời điểm, hai thao tác này triệt tiêu lẫn nhau (**Eliminate**). Luồng `pop()` nhận trực tiếp giá trị $X$ từ luồng `push(X)` mà không cần chạm vào đỉnh ngăn xếp trung tâm `top`.
2. **Elimination Array Matrix**: Sử dụng mảng `Exchanger<Object>[]`. Khi CAS trên Treiber Stack thất bại, luồng chọn ngẫu nhiên một ô trao đổi trong mảng và chờ đợi thao tác đối nghịch trong khoảng thời gian timeout ngắn.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.Random;
import java.util.concurrent.Exchanger;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;
import java.util.concurrent.atomic.AtomicReference;

public class EliminationBackoffStack<E> {

    static class Node<E> {
        final E item;
        Node<E> next;
        Node(E item) { this.item = item; }
    }

    private final AtomicReference<Node<E>> top = new AtomicReference<>(null);
    private final Exchanger<Object>[] eliminationArray;
    private final Random random = new Random();
    private static final Object POP_MARKER = new Object();

    @SuppressWarnings("unchecked")
    public EliminationBackoffStack(int eliminationCapacity) {
        this.eliminationArray = new Exchanger[eliminationCapacity];
        for (int i = 0; i < eliminationCapacity; i++) {
            this.eliminationArray[i] = new Exchanger<>();
        }
    }

    public void push(E item) {
        Objects.requireNonNull(item);
        Node<E> newNode = new Node<>(item);

        while (true) {
            Node<E> oldTop = top.get();
            newNode.next = oldTop;
            if (top.compareAndSet(oldTop, newNode)) {
                return;
            }

            int slot = random.nextInt(eliminationArray.length);
            try {
                Object result = eliminationArray[slot].exchange(item, 50, TimeUnit.MICROSECONDS);
                if (result == POP_MARKER) {
                    return;
                }
            } catch (TimeoutException | InterruptedException e) {
                // Hết thời gian chờ, quay lại thử CAS tại top
            }
        }
    }

    @SuppressWarnings("unchecked")
    public E pop() {
        while (true) {
            Node<E> oldTop = top.get();
            if (oldTop == null) {
                return null;
            }
            if (top.compareAndSet(oldTop, oldTop.next)) {
                return oldTop.item;
            }

            int slot = random.nextInt(eliminationArray.length);
            try {
                Object result = eliminationArray[slot].exchange(POP_MARKER, 50, TimeUnit.MICROSECONDS);
                if (result != null && result != POP_MARKER) {
                    return (E) result;
                }
            } catch (TimeoutException | InterruptedException e) {
                // Hết thời gian chờ, quay lại thử CAS tại top
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** Amortized $O(1)$. Khả năng mở rộng thông lượng theo số lượng core mà không bị bão hòa bus bộ nhớ.
- **Không gian (Space Complexity):** $O(N + E)$ với $E$ là kích thước Elimination Array.

---

## 18. Thread-Safe Work-Stealing Deque (Chase-Lev Deque)

**Đề bài chi tiết:**
Cài đặt hàng đợi hai đầu đánh cắp công việc (**Work-Stealing Deque**) theo thuật toán Chase-Lev (trọng tâm của `ForkJoinPool` trong Java).
Các phương thức:
- `void push(E task)`: Luồng sở hữu (Worker) đẩy task vào đáy (Bottom).
- `E pop()`: Luồng sở hữu lấy task từ đáy (Bottom - LIFO).
- `E steal()`: Các luồng khác (Thieves) đánh cắp task từ đỉnh (Top - FIFO).

**Phân tích thuật toán:**
1. **Single-Owner Bottom**: Chỉ có luồng sở hữu được gọi `push` và `pop` tại Bottom nên không cần CAS phức tạp giữa các worker với nhau.
2. **Multi-Thief Top with CAS**: Nhiều luồng Thief có thể đồng thời gọi `steal()` tại Top, sử dụng CAS trên biến chỉ số `top` để giải quyết tranh chấp.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.concurrent.atomic.AtomicLong;
import java.util.concurrent.atomic.AtomicReferenceArray;

public class WorkStealingDeque<E> {

    private final AtomicLong top = new AtomicLong(0);
    private final AtomicLong bottom = new AtomicLong(0);
    private final AtomicReferenceArray<E> buffer;
    private final int mask;

    public WorkStealingDeque(int capacity) {
        if (Integer.bitCount(capacity) != 1) {
            throw new IllegalArgumentException("Capacity must be a power of 2");
        }
        this.buffer = new AtomicReferenceArray<>(capacity);
        this.mask = capacity - 1;
    }

    public void push(E task) {
        long b = bottom.get();
        buffer.set((int) (b & mask), task);
        bottom.set(b + 1);
    }

    public E pop() {
        long b = bottom.get() - 1;
        bottom.set(b);
        long t = top.get();

        if (t <= b) {
            E task = buffer.get((int) (b & mask));
            if (t == b) {
                if (!top.compareAndSet(t, t + 1)) {
                    task = null;
                }
                bottom.set(b + 1);
            }
            return task;
        } else {
            bottom.set(b + 1);
            return null;
        }
    }

    public E steal() {
        while (true) {
            long t = top.get();
            long b = bottom.get();
            if (t >= b) {
                return null;
            }
            E task = buffer.get((int) (t & mask));
            if (top.compareAndSet(t, t + 1)) {
                return task;
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `push`, `pop`, `steal`.
- **Không gian (Space Complexity):** $O(\text{capacity})$.

---

## 19. High-Throughput Concurrent Count-Min Sketch

**Đề bài chi tiết:**
Thiết kế cấu trúc tóm tắt dòng dữ liệu tần suất cao (**Concurrent Count-Min Sketch**) ước lượng tần suất xuất hiện của các phần tử trong luồng dữ liệu lớn mà không tốn nhiều bộ nhớ.
Các phương thức:
- `void add(String item, int count)`: Tăng tần suất của `item` thêm `count`.
- `int estimateCount(String item)`: Ước lượng tần suất xuất hiện của `item`.

**Phân tích thuật toán:**
1. **2D Atomic Counter Array**: Mảng $D \times W$ các `AtomicIntegerArray` ($D$ hàm băm, $W$ bề rộng).
2. **Frequency Estimation Invariant**: `estimateCount(item) = \min_{i=0}^{D-1} Matrix[i][Hash_i(item)]`. Do không có phép trừ, ước lượng luôn $\ge$ tần suất thực tế.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.concurrent.atomic.AtomicIntegerArray;

public class ConcurrentCountMinSketch {

    private final int depth;
    private final int width;
    private final AtomicIntegerArray[] table;
    private final int[] hashSeeds;

    public ConcurrentCountMinSketch(int depth, int width) {
        this.depth = depth;
        this.width = width;
        this.table = new AtomicIntegerArray[depth];
        this.hashSeeds = new int[depth];
        for (int i = 0; i < depth; i++) {
            this.table[i] = new AtomicIntegerArray(width);
            this.hashSeeds[i] = (i + 1) * 0x9E3779B9;
        }
    }

    private int hash(String item, int seed) {
        int h = seed ^ item.hashCode();
        h ^= (h >>> 16);
        h *= 0x85ebca6b;
        h ^= (h >>> 13);
        return Math.abs(h % width);
    }

    public void add(String item, int count) {
        if (count <= 0) return;
        for (int i = 0; i < depth; i++) {
            int col = hash(item, hashSeeds[i]);
            table[i].addAndGet(col, count);
        }
    }

    public int estimateCount(String item) {
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < depth; i++) {
            int col = hash(item, hashSeeds[i]);
            min = Math.min(min, table[i].get(col));
        }
        return min;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(D)$ cho cả `add` và `estimateCount`.
- **Không gian (Space Complexity):** $O(D \times W)$ số nguyên trong bộ nhớ.

---

## 20. Concurrent Bloom Filter with AtomicBitSet

**Đề bài chi tiết:**
Cài đặt cấu trúc dữ liệu lọc Bloom an toàn đa luồng không khóa (**Lock-Free Concurrent Bloom Filter**) bằng cách xây dựng `AtomicBitSet` dựa trên `AtomicLongArray`.
Các phương thức:
- `void add(String item)`: Thêm chuỗi vào Bloom Filter.
- `boolean mightContain(String item)`: Kiểm tra chuỗi có thể đã tồn tại hay chắc chắn chưa từng tồn tại.

**Phân tích thuật toán:**
1. **Atomic Bit Manipulation**: Mỗi bit trong mảng `AtomicLongArray` được bật bằng vòng lặp CAS:
   `long oldVal, newVal; do { oldVal = array.get(idx); newVal = oldVal | (1L << bit); } while (!array.compareAndSet(idx, oldVal, newVal));`
2. **Lock-Free Read**: Đọc giá trị bit qua phép toán bit AND trực tiếp trên `array.get(idx)` có tính `volatile`.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.atomic.AtomicLongArray;

public class ConcurrentBloomFilter {

    private final AtomicLongArray bitArray;
    private final int numBits;
    private final int numHashFunctions;

    public ConcurrentBloomFilter(int expectedInsertions, double fpp) {
        if (expectedInsertions <= 0 || fpp <= 0.0 || fpp >= 1.0) {
            throw new IllegalArgumentException();
        }
        this.numBits = optimalNumOfBits(expectedInsertions, fpp);
        this.numHashFunctions = optimalNumOfHashFunctions(expectedInsertions, numBits);
        int numLongs = (numBits + 63) / 64;
        this.bitArray = new AtomicLongArray(numLongs);
    }

    private static int optimalNumOfBits(long n, double p) {
        return (int) (-n * Math.log(p) / (Math.log(2) * Math.log(2)));
    }

    private static int optimalNumOfHashFunctions(long n, long m) {
        return Math.max(1, (int) Math.round((double) m / n * Math.log(2)));
    }

    private void setBit(int bitIndex) {
        int longIndex = bitIndex / 64;
        long bitMask = 1L << (bitIndex % 64);
        while (true) {
            long current = bitArray.get(longIndex);
            if ((current & bitMask) != 0) return;
            if (bitArray.compareAndSet(longIndex, current, current | bitMask)) {
                return;
            }
        }
    }

    private boolean getBit(int bitIndex) {
        int longIndex = bitIndex / 64;
        long bitMask = 1L << (bitIndex % 64);
        return (bitArray.get(longIndex) & bitMask) != 0;
    }

    public void add(String item) {
        Objects.requireNonNull(item);
        int hash1 = item.hashCode();
        int hash2 = (hash1 >>> 16) ^ (hash1 * 0x85ebca6b);
        for (int i = 0; i < numHashFunctions; i++) {
            int combinedHash = hash1 + i * hash2;
            int bitIndex = Math.abs(combinedHash % numBits);
            setBit(bitIndex);
        }
    }

    public boolean mightContain(String item) {
        Objects.requireNonNull(item);
        int hash1 = item.hashCode();
        int hash2 = (hash1 >>> 16) ^ (hash1 * 0x85ebca6b);
        for (int i = 0; i < numHashFunctions; i++) {
            int combinedHash = hash1 + i * hash2;
            int bitIndex = Math.abs(combinedHash % numBits);
            if (!getBit(bitIndex)) {
                return false;
            }
        }
        return true;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(K)$ với $K$ là số hàm băm.
- **Không gian (Space Complexity):** $O(M)$ bits.

---

## 21. Thread-Safe Read-Copy-Update (RCU) Snapshot Map

**Đề bài chi tiết:**
Cài đặt bảng băm đa luồng sử dụng mô hình kiến trúc **Read-Copy-Update (RCU)** đảm bảo mọi thao tác đọc là hoàn toàn bất biến (**Wait-Free $O(1)$ Reads**).
Các phương thức:
- `V get(K key)`: Không bao giờ bị chặn, không bao giờ dùng lock.
- `void put(K key, V value)`: Sao chép bảng băm ngầm, áp dụng thay đổi và hoán đổi con trỏ root qua CAS.

**Phân tích thuật toán:**
1. **Immutable Snapshot Reference**: Cấu trúc bảng băm được gói trọn vẹn trong một đối tượng `Map<K, V>` bất biến được trỏ bởi `AtomicReference<Map<K, V>> root`.
2. **RCU Write Phase**: Luồng ghi tạo bản sao nông của map hiện tại, cập nhật key-value và dùng CAS `root.compareAndSet(oldMap, newMap)`.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;
import java.util.concurrent.atomic.AtomicReference;

public class RcuSnapshotMap<K, V> {

    private final AtomicReference<Map<K, V>> root = new AtomicReference<>(Collections.emptyMap());

    public V get(K key) {
        Objects.requireNonNull(key);
        return root.get().get(key);
    }

    public void put(K key, V value) {
        Objects.requireNonNull(key);
        Objects.requireNonNull(value);

        while (true) {
            Map<K, V> current = root.get();
            Map<K, V> copy = new HashMap<>(current);
            copy.put(key, value);
            Map<K, V> unmodifiable = Collections.unmodifiableMap(copy);
            if (root.compareAndSet(current, unmodifiable)) {
                return;
            }
        }
    }

    public void remove(K key) {
        Objects.requireNonNull(key);
        while (true) {
            Map<K, V> current = root.get();
            if (!current.containsKey(key)) return;
            Map<K, V> copy = new HashMap<>(current);
            copy.remove(key);
            Map<K, V> unmodifiable = Collections.unmodifiableMap(copy);
            if (root.compareAndSet(current, unmodifiable)) {
                return;
            }
        }
    }

    public Map<K, V> snapshot() {
        return root.get();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `get`, $O(N)$ cho `put`/`remove` (thích hợp với tỷ lệ đọc > 99.9%).
- **Không gian (Space Complexity):** $O(N)$ bộ nhớ.

---

## 22. Concurrent State Machine with Atomic Transitions

**Đề bài chi tiết:**
Thiết kế máy chuyển trạng thái đa luồng (**Concurrent Finite State Machine**) quản lý quy trình chuyển đổi trạng thái nghiêm ngặt mà không xảy ra Race Condition.
Các phương thức:
- `boolean fireEvent(E event)`: Kích hoạt sự kiện để chuyển từ trạng thái hiện tại sang trạng thái mới theo bảng quy tắc.
- `S getState()`: Lấy trạng thái hiện tại.

**Phân tích thuật toán:**
1. **Transition Matrix Invariant**: Quy tắc chuyển đổi là một hàm `(State, Event) -> NextState`.
2. **Atomic Compare-And-Set**: Trạng thái hiện tại được lưu trong `AtomicReference<S>`. Khi nhận sự kiện, tính `nextState` và CAS từ `currentState` sang `nextState`.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Map;
import java.util.Objects;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicReference;

public class ConcurrentStateMachine<S extends Enum<S>, E extends Enum<E>> {

    private record Transition<S, E>(S source, E event) {}

    private final AtomicReference<S> currentState;
    private final Map<Transition<S, E>, S> transitionTable = new ConcurrentHashMap<>();

    public ConcurrentStateMachine(S initialState) {
        this.currentState = new AtomicReference<>(Objects.requireNonNull(initialState));
    }

    public void defineTransition(S from, E event, S to) {
        transitionTable.put(new Transition<>(from, event), to);
    }

    public boolean fireEvent(E event) {
        Objects.requireNonNull(event);
        while (true) {
            S curr = currentState.get();
            S next = transitionTable.get(new Transition<>(curr, event));
            if (next == null) {
                return false;
            }
            if (currentState.compareAndSet(curr, next)) {
                return true;
            }
        }
    }

    public S getState() {
        return currentState.get();
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho mọi thao tác.
- **Không gian (Space Complexity):** $O(|S| \times |E|)$.

---

## 23. Scalable Thread-Safe Object Pool

**Đề bài chi tiết:**
Cài đặt kho tái sử dụng đối tượng đa luồng (**Concurrent Object Pool**) quản lý tài nguyên đắt đỏ (như Database Connections, Sockets) với giới hạn dung lượng $capacity$.
Các phương thức:
- `T acquire(long timeout, TimeUnit unit)`: Mượn đối tượng từ pool.
- `void release(T obj)`: Trả đối tượng về pool.

**Phân tích thuật toán:**
1. **Semaphore Capacity Guard**: Dùng `Semaphore` để giới hạn số lượng đối tượng được mượn đồng thời.
2. **Lock-Free Pool Storage**: Dùng `ConcurrentLinkedQueue<T>` lưu trữ các instance rảnh rỗi.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.*;
import java.util.function.Supplier;

public class ConcurrentObjectPool<T> {

    private final ConcurrentLinkedQueue<T> pool = new ConcurrentLinkedQueue<>();
    private final Semaphore semaphore;
    private final Supplier<T> factory;

    public ConcurrentObjectPool(int capacity, Supplier<T> factory) {
        if (capacity <= 0) throw new IllegalArgumentException();
        this.semaphore = new Semaphore(capacity, true);
        this.factory = Objects.requireNonNull(factory);
    }

    public T acquire(long timeout, TimeUnit unit) throws InterruptedException {
        if (!semaphore.tryAcquire(timeout, unit)) {
            return null;
        }
        T obj = pool.poll();
        if (obj == null) {
            obj = factory.get();
        }
        return obj;
    }

    public void release(T obj) {
        if (obj != null) {
            pool.offer(obj);
            semaphore.release();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho cả `acquire` và `release`.
- **Không gian (Space Complexity):** $O(\text{capacity})$.

---

## 24. Concurrent Sliding Window Rate Counter

**Đề bài chi tiết:**
Cài đặt bộ đếm cửa sổ trượt (**Sliding Window Counter**) ghi nhận số lượng sự kiện xảy ra trong khoảng thời gian $windowMillis$ với độ chính xác chia nhỏ theo $numBuckets$.
Các phương thức:
- `void increment()`: Ghi nhận 1 sự kiện.
- `long getCount()`: Trả về tổng sự kiện trong cửa sổ trượt.

**Phân tích thuật toán:**
1. **Circular Time Slices**: Chia cửa sổ trượt thành $B$ bucket. Mỗi bucket lưu trữ `(epochBucketIndex, AtomicLong count)`.
2. **Lazy Bucket Reset**: Khi thời gian trôi qua, bucket cũ được reset về 0 và gắn epoch mới trước khi cộng dồn.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.concurrent.atomic.AtomicLong;

public class ConcurrentSlidingWindowCounter {

    static class Bucket {
        final AtomicLong count = new AtomicLong(0);
        volatile long bucketId = -1L;
    }

    private final int numBuckets;
    private final long bucketSizeMillis;
    private final long windowMillis;
    private final Bucket[] buckets;

    public ConcurrentSlidingWindowCounter(long windowMillis, int numBuckets) {
        this.windowMillis = windowMillis;
        this.numBuckets = numBuckets;
        this.bucketSizeMillis = windowMillis / numBuckets;
        this.buckets = new Bucket[numBuckets];
        for (int i = 0; i < numBuckets; i++) {
            this.buckets[i] = new Bucket();
        }
    }

    public void increment() {
        long now = System.currentTimeMillis();
        long currentBucketId = now / bucketSizeMillis;
        int idx = (int) (currentBucketId % numBuckets);

        Bucket bucket = buckets[idx];
        if (bucket.bucketId != currentBucketId) {
            synchronized (bucket) {
                if (bucket.bucketId != currentBucketId) {
                    bucket.count.set(0);
                    bucket.bucketId = currentBucketId;
                }
            }
        }
        bucket.count.incrementAndGet();
    }

    public long getCount() {
        long now = System.currentTimeMillis();
        long currentBucketId = now / bucketSizeMillis;
        long sum = 0;

        for (int i = 0; i < numBuckets; i++) {
            Bucket bucket = buckets[i];
            if (currentBucketId - bucket.bucketId < numBuckets) {
                sum += bucket.count.get();
            }
        }
        return sum;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `increment`, $O(\text{numBuckets})$ cho `getCount`.
- **Không gian (Space Complexity):** $O(\text{numBuckets})$.

---

## 25. Lock-Free Ordered Doubly Linked List with AtomicMarkableReference

**Đề bài chi tiết:**
Cài đặt danh sách liên kết đôi có thứ tự không khóa (**Lock-Free Ordered List**) hỗ trợ thao tác chèn và xóa an toàn bằng `AtomicMarkableReference`.
Các phương thức:
- `boolean insert(T value)`: Chèn giá trị theo thứ tự tăng dần.
- `boolean delete(T value)`: Xóa giá trị khỏi danh sách.
- `boolean contains(T value)`: Kiểm tra sự tồn tại.

**Phân tích thuật toán:**
1. **Logical Deletion Flag**: Đánh dấu bit boolean `marked` của `AtomicMarkableReference` để biểu thị node đã bị xóa.
2. **Two-Phase Unlink**: Giai đoạn 1 đánh dấu logic con trỏ next, giai đoạn 2 ngắt kết nối con trỏ vật lý của node liền trước.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Objects;
import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreeOrderedList<T extends Comparable<T>> {

    static class Node<T> {
        final T value;
        final AtomicMarkableReference<Node<T>> next;

        Node(T value) {
            this.value = value;
            this.next = new AtomicMarkableReference<>(null, false);
        }
    }

    private final Node<T> head = new Node<>(null);

    public boolean insert(T value) {
        Objects.requireNonNull(value);
        while (true) {
            Node<T> pred = head;
            Node<T> curr = pred.next.getReference();
            while (curr != null && curr.value.compareTo(value) < 0) {
                pred = curr;
                curr = curr.next.getReference();
            }
            if (curr != null && curr.value.compareTo(value) == 0) {
                return false;
            }
            Node<T> newNode = new Node<>(value);
            newNode.next.set(curr, false);
            if (pred.next.compareAndSet(curr, newNode, false, false)) {
                return true;
            }
        }
    }

    public boolean delete(T value) {
        Objects.requireNonNull(value);
        while (true) {
            Node<T> pred = head;
            Node<T> curr = pred.next.getReference();
            while (curr != null && curr.value.compareTo(value) < 0) {
                pred = curr;
                curr = curr.next.getReference();
            }
            if (curr == null || curr.value.compareTo(value) != 0) {
                return false;
            }
            Node<T> succ = curr.next.getReference();
            if (!curr.next.compareAndSet(succ, succ, false, true)) {
                continue;
            }
            pred.next.compareAndSet(curr, succ, false, false);
            return true;
        }
    }

    public boolean contains(T value) {
        Objects.requireNonNull(value);
        boolean[] marked = new boolean[1];
        Node<T> curr = head.next.get(marked);
        while (curr != null) {
            if (curr.value != null && curr.value.compareTo(value) == 0 && !marked[0]) {
                return true;
            }
            curr = curr.next.get(marked);
        }
        return false;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(N)$ cho mọi thao tác.
- **Không gian (Space Complexity):** $O(N)$ nodes.

---

## 26. Thread-Safe Two-Level Memory Buffer (Double Buffer)

**Đề bài chi tiết:**
Thiết kế bộ đệm ghi đôi (**Concurrent Double Buffering Pattern**) tách biệt giữa luồng nạp dữ liệu (Producers) và luồng đẩy dữ liệu ra đĩa (Background Flusher).
Các phương thức:
- `void write(T record)`: Ghi bản ghi vào active buffer.
- `List<T> swapAndFlush()`: Hoán đổi buffer và trả về toàn bộ dữ liệu đã tích lũy.

**Phân tích thuật toán:**
1. **Active & Passive Buffers**: Duy trì hai danh sách. Luồng ghi chỉ thao tác trên active buffer dưới sự bảo vệ của lock nhẹ.
2. **Buffer Swap**: Luồng flush hoán đổi tham chiếu hai buffer trong $O(1)$ rồi xử lý buffer passive mà không làm nghẽn luồng ghi.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;
import java.util.concurrent.locks.ReentrantLock;

public class ConcurrentDoubleBuffer<T> {

    private List<T> activeBuffer = new ArrayList<>();
    private List<T> flushBuffer = new ArrayList<>();
    private final ReentrantLock lock = new ReentrantLock();

    public void write(T record) {
        Objects.requireNonNull(record);
        lock.lock();
        try {
            activeBuffer.add(record);
        } finally {
            lock.unlock();
        }
    }

    public List<T> swapAndFlush() {
        List<T> toFlush;
        lock.lock();
        try {
            toFlush = activeBuffer;
            activeBuffer = flushBuffer;
            activeBuffer.clear();
            flushBuffer = toFlush;
        } finally {
            lock.unlock();
        }
        return toFlush;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ cho `write`, $O(1)$ cho thao tác hoán đổi `swapAndFlush`.
- **Không gian (Space Complexity):** $O(N)$.

---

## 27. Concurrent Radix Tree (Prefix Routing Table)

**Đề bài chi tiết:**
Cài đặt cây nén tiền tố (**Concurrent Radix Tree**) phục vụ bảng định tuyến mạng hoặc URL Router với cơ chế khóa phân nhánh.
Các phương thức:
- `void insert(String path, V handler)`
- `V route(String path)`

**Phân tích thuật toán:**
1. **Edge Label Compression**: Gom cụm các ký tự trùng lặp trên cùng một cạnh.
2. **Concurrent Branch Lock**: Sử dụng `ReentrantReadWriteLock` trên từng node con để hỗ trợ cập nhật động đường dẫn định tuyến.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.Map;
import java.util.Objects;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentRadixTree<V> {

    static class RadixNode<V> {
        final Map<String, RadixNode<V>> edges = new ConcurrentHashMap<>();
        volatile V value;
        final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    }

    private final RadixNode<V> root = new RadixNode<>();

    public void insert(String path, V handler) {
        Objects.requireNonNull(path);
        Objects.requireNonNull(handler);

        RadixNode<V> curr = root;
        curr.lock.writeLock().lock();
        try {
            curr.edges.computeIfAbsent(path, p -> new RadixNode<>()).value = handler;
        } finally {
            curr.lock.writeLock().unlock();
        }
    }

    public V route(String path) {
        Objects.requireNonNull(path);
        RadixNode<V> node = root.edges.get(path);
        return (node != null) ? node.value : null;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(L)$ với độ dài path $L$.
- **Không gian (Space Complexity):** $O(N \cdot L)$.

---

## 28. Concurrent Flat-Combining Priority Queue

**Đề bài chi tiết:**
Cài đặt hàng đợi ưu tiên áp dụng kỹ thuật kết hợp yêu cầu đa luồng (**Flat Combining**) của Hendler et al.
Các phương thức:
- `void add(E item)`
- `E poll()`

**Phân tích thuật toán:**
1. **Combiner Thread**: Thay vì hàng trăm luồng cùng tranh chấp một heap, mỗi luồng công bố thao tác vào một mảng `PublicationSlot[]`.
2. **Batch Processing**: Luồng đầu tiên giành được Global Lock sẽ trở thành Combiner, thực hiện một lượt gom tất cả yêu cầu trong mảng và xử lý trên `PriorityQueue` nội bộ tuần tự, giảm thiểu tối đa Cache Invalidation.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.PriorityQueue;
import java.util.concurrent.locks.ReentrantLock;

public class FlatCombiningPriorityQueue<E extends Comparable<E>> {

    private enum OpType { ADD, POLL }

    static class RequestSlot<E> {
        volatile OpType op;
        volatile E item;
        volatile Object result;
        volatile boolean completed = false;
    }

    private final PriorityQueue<E> queue = new PriorityQueue<>();
    private final ReentrantLock globalLock = new ReentrantLock();
    private final ThreadLocal<RequestSlot<E>> localSlot = ThreadLocal.withInitial(RequestSlot::new);
    private final RequestSlot<E>[] publicationSlots;

    @SuppressWarnings("unchecked")
    public FlatCombiningPriorityQueue(int maxThreads) {
        this.publicationSlots = new RequestSlot[maxThreads];
        for (int i = 0; i < maxThreads; i++) {
            this.publicationSlots[i] = new RequestSlot<>();
        }
    }

    private void combine() {
        for (RequestSlot<E> slot : publicationSlots) {
            if (slot.op != null && !slot.completed) {
                if (slot.op == OpType.ADD) {
                    queue.add(slot.item);
                    slot.completed = true;
                } else if (slot.op == OpType.POLL) {
                    slot.result = queue.poll();
                    slot.completed = true;
                }
            }
        }
    }

    public void add(E item) {
        RequestSlot<E> slot = localSlot.get();
        slot.op = OpType.ADD;
        slot.item = item;
        slot.completed = false;

        while (!slot.completed) {
            if (globalLock.tryLock()) {
                try {
                    combine();
                } finally {
                    globalLock.unlock();
                }
            }
        }
    }

    @SuppressWarnings("unchecked")
    public E poll() {
        RequestSlot<E> slot = localSlot.get();
        slot.op = OpType.POLL;
        slot.item = null;
        slot.completed = false;

        while (!slot.completed) {
            if (globalLock.tryLock()) {
                try {
                    combine();
                } finally {
                    globalLock.unlock();
                }
            }
        }
        return (E) slot.result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** Amortized $O(\log N)$ với thông lượng batching cao gấp 5-10x dưới heavy contention.
- **Không gian (Space Complexity):** $O(N + T)$ với $T$ là số luồng.

---

## 29. Concurrent Spatial QuadTree with Sub-Grid Locking

**Đề bài chi tiết:**
Thiết kế cây tứ phân không gian đa luồng (**Concurrent QuadTree**) phục vụ quản lý vị trí thực thể 2D với khóa phân vùng độc lập.
Các phương thức:
- `void insert(double x, double y, Object data)`
- `List<Object> queryRange(double minX, double minY, double maxX, double maxY)`

**Phân tích thuật toán:**
1. **Four Quadrants Partitioning**: Không gian được chia làm NW, NE, SW, SE.
2. **Sub-grid Striped Lock**: Mỗi vùng con được bảo vệ bởi một khóa riêng biệt, cho phép ghi đồng thời tại các khu vực địa lý khác nhau.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentSpatialQuadTree {

    public record Point(double x, double y, Object data) {}

    static class QuadNode {
        final double minX, minY, maxX, maxY;
        final List<Point> points = new ArrayList<>();
        QuadNode[] children = null;
        final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

        QuadNode(double minX, double minY, double maxX, double maxY) {
            this.minX = minX; this.minY = minY;
            this.maxX = maxX; this.maxY = maxY;
        }

        boolean contains(double x, double y) {
            return x >= minX && x <= maxX && y >= minY && y <= maxY;
        }
    }

    private final QuadNode root;

    public ConcurrentSpatialQuadTree(double minX, double minY, double maxX, double maxY) {
        this.root = new QuadNode(minX, minY, maxX, maxY);
    }

    public void insert(double x, double y, Object data) {
        insertInternal(root, new Point(x, y, data));
    }

    private void insertInternal(QuadNode node, Point p) {
        node.lock.writeLock().lock();
        try {
            if (node.points.size() < 10 && node.children == null) {
                node.points.add(p);
                return;
            }
            if (node.children == null) {
                subdivide(node);
            }
        } finally {
            node.lock.writeLock().unlock();
        }

        for (QuadNode child : node.children) {
            if (child.contains(p.x, p.y)) {
                insertInternal(child, p);
                return;
            }
        }
    }

    private void subdivide(QuadNode node) {
        double midX = (node.minX + node.maxX) / 2.0;
        double midY = (node.minY + node.maxY) / 2.0;
        node.children = new QuadNode[]{
            new QuadNode(node.minX, midY, midX, node.maxY),
            new QuadNode(midX, midY, node.maxX, node.maxY),
            new QuadNode(node.minX, node.minY, midX, midY),
            new QuadNode(midX, node.minY, node.maxX, midY)
        };
        for (Point pt : node.points) {
            for (QuadNode child : node.children) {
                if (child.contains(pt.x, pt.y)) {
                    child.points.add(pt);
                    break;
                }
            }
        }
        node.points.clear();
    }

    public List<Object> queryRange(double minX, double minY, double maxX, double maxY) {
        List<Object> results = new ArrayList<>();
        queryInternal(root, minX, minY, maxX, maxY, results);
        return results;
    }

    private void queryInternal(QuadNode node, double minX, double minY, double maxX, double maxY, List<Object> res) {
        node.lock.readLock().lock();
        try {
            for (Point pt : node.points) {
                if (pt.x >= minX && pt.x <= maxX && pt.y >= minY && pt.y <= maxY) {
                    res.add(pt.data);
                }
            }
            if (node.children != null) {
                for (QuadNode child : node.children) {
                    if (!(child.maxX < minX || child.minX > maxX || child.maxY < minY || child.minY > maxY)) {
                        queryInternal(child, minX, minY, maxX, maxY, res);
                    }
                }
            }
        } finally {
            node.lock.readLock().unlock();
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(\log N)$ trung bình cho `insert` và `queryRange`.
- **Không gian (Space Complexity):** $O(N)$ lưu trữ các node không gian.

---

## 30. Ultra-Low-Latency MPMC Ring Buffer with VarHandle Memory Fences

**Đề bài chi tiết:**
Cài đặt hàng đợi vòng tròn MPMC (Multi-Producer Multi-Consumer) hiệu năng cực đại sử dụng trực tiếp **VarHandle** với các rào cản bộ nhớ **Acquire/Release Semantics** (Mechanical Sympathy) nhằm triệt tiêu tối đa độ trễ CPU.
Các phương thức:
- `boolean offer(E item)`: Ghi phần tử với Release fence.
- `E poll()`: Đọc phần tử với Acquire fence.

**Phân tích thuật toán:**
1. **Sequence Barrier per Slot**: Mỗi ô trong mảng chứa một trường sequence độc lập được đọc/ghi bằng `VarHandle.getAcquire` và `VarHandle.setRelease`.
2. **Zero-Locking Flow**:
   - Producer kiểm tra `slot.sequence == currentHead`. Nếu khớp, dùng CAS tăng `head`, ghi dữ liệu và gán `slot.sequence = currentHead + 1` bằng `setRelease`.
   - Consumer kiểm tra `slot.sequence == currentTail + 1`. Nếu khớp, dùng CAS tăng `tail`, đọc dữ liệu và gán `slot.sequence = currentTail + bufferSize` bằng `setRelease`.

**Mã nguồn Java:**
```java
package concurrent.datastructures;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.Objects;

public class LowLatencyMpmcRingBuffer<E> {

    static class Cell<E> {
        volatile long sequence;
        volatile E item;

        Cell(long seq) {
            this.sequence = seq;
        }

        private static final VarHandle SEQ_HANDLE;
        static {
            try {
                SEQ_HANDLE = MethodHandles.lookup().findVarHandle(Cell.class, "sequence", long.class);
            } catch (ReflectiveOperationException e) {
                throw new ExceptionInInitializerError(e);
            }
        }

        long getSeqAcquire() {
            return (long) SEQ_HANDLE.getAcquire(this);
        }

        void setSeqRelease(long val) {
            SEQ_HANDLE.setRelease(this, val);
        }
    }

    private final Cell<E>[] cells;
    private final int bufferMask;
    private final int capacity;

    private volatile long head = 0;
    private volatile long tail = 0;

    private static final VarHandle HEAD_HANDLE;
    private static final VarHandle TAIL_HANDLE;

    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD_HANDLE = l.findVarHandle(LowLatencyMpmcRingBuffer.class, "head", long.class);
            TAIL_HANDLE = l.findVarHandle(LowLatencyMpmcRingBuffer.class, "tail", long.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    @SuppressWarnings("unchecked")
    public LowLatencyMpmcRingBuffer(int capacity) {
        if (Integer.bitCount(capacity) != 1) {
            throw new IllegalArgumentException("Capacity must be a power of 2");
        }
        this.capacity = capacity;
        this.bufferMask = capacity - 1;
        this.cells = (Cell<E>[]) new Cell[capacity];
        for (int i = 0; i < capacity; i++) {
            this.cells[i] = new Cell<>(i);
        }
    }

    public boolean offer(E item) {
        Objects.requireNonNull(item);
        while (true) {
            long currentHead = (long) HEAD_HANDLE.getVolatile(this);
            Cell<E> cell = cells[(int) (currentHead & bufferMask)];
            long seq = cell.getSeqAcquire();
            long diff = seq - currentHead;

            if (diff == 0) {
                if (HEAD_HANDLE.compareAndSet(this, currentHead, currentHead + 1)) {
                    cell.item = item;
                    cell.setSeqRelease(currentHead + 1);
                    return true;
                }
            } else if (diff < 0) {
                return false; // Queue đầy
            }
        }
    }

    public E poll() {
        while (true) {
            long currentTail = (long) TAIL_HANDLE.getVolatile(this);
            Cell<E> cell = cells[(int) (currentTail & bufferMask)];
            long seq = cell.getSeqAcquire();
            long diff = seq - (currentTail + 1);

            if (diff == 0) {
                if (TAIL_HANDLE.compareAndSet(this, currentTail, currentTail + 1)) {
                    E item = cell.item;
                    cell.item = null;
                    cell.setSeqRelease(currentTail + capacity);
                    return item;
                }
            } else if (diff < 0) {
                return null; // Queue rỗng
            }
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time Complexity):** $O(1)$ với độ trễ ở mức nano-giây (Sub-microsecond latency), không sinh rác (Zero GC pressure).
- **Không gian (Space Complexity):** $O(\text{capacity})$ bộ nhớ cố định.
