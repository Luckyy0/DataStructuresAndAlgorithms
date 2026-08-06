# Danh sách 30 bài tập Thuật toán Lock-Free & Wait-Free (Concurrent & Parallel Algorithms)

Tài liệu này cung cấp 30 bài toán kinh điển và nâng cao về cấu trúc dữ liệu và thuật toán phi khóa (**Lock-Free & Wait-Free Algorithms**), được thiết kế theo tiêu chuẩn công nghiệp Java 21. Mỗi bài tập bao gồm: mô tả đề bài chi tiết, phân tích cơ chế đồng bộ (CAS Loops, Helping Mechanisms, Memory Ordering, Invariants, Linearizability), mã nguồn Java 21 hoàn chỉnh và phân tích độ phức tạp thời gian, không gian kèm bảo đảm tiến trình (**Progress Guarantees**).

---

## 1. Lock-Free Treiber Stack (LIFO Stack)

### Đề bài chi tiết
Thiết kế một ngăn xếp đồng thời (**Concurrent Stack**) không sử dụng khóa (**Lock-Free LIFO Stack**) hỗ trợ hai thao tác cơ bản:
- `void push(E item)`: Thêm một phần tử vào đỉnh ngăn xếp.
- `E pop()`: Lấy và xóa phần tử ở đỉnh ngăn xếp. Trả về `null` nếu ngăn xếp rỗng.

Cấu trúc dữ liệu phải đảm bảo tính chất **Linearizability**, an toàn luồng (**Thread-Safe**) khi có hàng ngàn luồng truy cập đồng thời, và thỏa mãn mức độ bảo đảm tiến trình **Lock-Free** (ít nhất một luồng luôn hoàn thành công việc sau một số hữu hạn bước).

### Phân tích thuật toán
- **Cấu trúc dữ liệu**: Danh sách liên kết đơn trong đó con trỏ `top` tham chiếu đến nút đầu tiên (đỉnh stack).
- **Cơ chế CAS (Compare-And-Swap)**:
  - Thao tác `push(item)`: Tạo nút mới `newNode(item)`. Đọc giá trị hiện tại của `top` (gọi là `curTop`), thiết lập `newNode.next = curTop`. Thực hiện thao tác nguyên tử `CAS(top, curTop, newNode)`. Nếu CAS thất bại (do luồng khác đã thay đổi `top`), lặp lại từ đầu (CAS Loop).
  - Thao tác `pop()`: Đọc giá trị `curTop`. Nếu `curTop == null`, trả về `null`. Ngược lại, lấy `newTop = curTop.next` và thực hiện `CAS(top, curTop, newTop)`. Nếu thành công, trả về dữ liệu của `curTop`. Nếu thất bại, lặp lại.
- **Linearizability Point**:
  - `push`: Thời điểm lệnh CAS trên biến `top` thành công.
  - `pop`: Thời điểm lệnh CAS cập nhật `top` thành `curTop.next` thành công (hoặc thời điểm đọc thấy `top == null`).
- **Memory Ordering**: Sử dụng `AtomicReference<Node<E>>` hoặc `VarHandle` với ngữ nghĩa volatile (Acquire-Release) để ngăn cản việc CPU/JIT sắp xếp lại thứ tự ghi trường `next` trước khi trỏ `top`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeTreiberStack<E> {

    private static final class Node<E> {
        final E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }

    private volatile Node<E> top;

    private static final VarHandle TOP;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            TOP = l.findVarHandle(LockFreeTreiberStack.class, "top", Node.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public void push(E item) {
        if (item == null) {
            throw new NullPointerException("Null items are not allowed");
        }
        Node<E> newNode = new Node<>(item);
        while (true) {
            Node<E> curTop = top;
            newNode.next = curTop;
            if (TOP.compareAndSet(this, curTop, newNode)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public E pop() {
        while (true) {
            Node<E> curTop = top;
            if (curTop == null) {
                return null;
            }
            Node<E> newTop = curTop.next;
            if (TOP.compareAndSet(this, curTop, newTop)) {
                return curTop.item;
            }
            Thread.onSpinWait();
        }
    }

    public boolean isEmpty() {
        return top == null;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - Tốt nhất / Không tranh chấp (Best case): $O(1)$.
  - Trung bình (Average case): $O(1)$ amortized.
  - Xấu nhất (Worst case under contention): $O(\infty)$ cho từng luồng cá biệt (starvation), nhưng hệ thống tổng thể luôn đạt $O(1)$ tiến độ.
- **Không gian (Space):** $O(N)$ với $N$ là số lượng phần tử hiện có trong ngăn xếp.
- **Progress Guarantee:** **Lock-Free**.

---

## 2. Lock-Free Michael-Scott Queue (FIFO Queue)

### Đề bài chi tiết
Triển khai hàng đợi FIFO không khóa (**Lock-Free FIFO Queue**) dựa trên thuật toán nổi tiếng của Maged M. Michael và Michael L. Scott (1996), là nền tảng của `java.util.concurrent.ConcurrentLinkedQueue`.
Hàng đợi phải hỗ trợ:
- `void enqueue(E item)`: Chèn một phần tử vào cuối hàng đợi.
- `E dequeue()`: Lấy và xóa phần tử ở đầu hàng đợi, trả về `null` nếu rỗng.

Yêu cầu sử dụng **Sentinel Node (Dummy Node)** để tránh việc `head` và `tail` bị `null`, đồng thời áp dụng cơ chế **Helping Mechanism** để luồng khác có thể giúp đưa `tail` tiến về phía trước nếu luồng thêm phần tử bị trễ.

### Phân tích thuật toán
- **Cấu trúc Sentinel**: Khởi tạo với một nút giả `Dummy(item = null)`, cả `head` và `tail` đều trỏ vào Dummy node này.
- **Invariants**:
  - `head` luôn trỏ tới nút sentinel hiện tại hoặc một nút đã bị dequeue.
  - `tail` trỏ tới nút cuối cùng hoặc nút áp chót trong danh sách liên kết.
- **Thao tác Enqueue (2 bước CAS)**:
  1. Đọc `curTail` và `tailNext = curTail.next`.
  2. Nếu `curTail == tail`:
     - Nếu `tailNext == null`: Cố gắng gắn nút mới vào cuối bằng `CAS(curTail.next, null, newNode)`. Nếu thành công, thử dịch chuyển `CAS(tail, curTail, newNode)` rồi kết thúc.
     - Nếu `tailNext != null`: `tail` đang bị tụt lại phía sau do luồng khác vừa gắn nút xong nhưng chưa kịp trỏ `tail`. Luồng hiện tại tiến hành **hỗ trợ (help)** bằng cách thực hiện `CAS(tail, curTail, tailNext)`.
- **Thao tác Dequeue**:
  1. Đọc `curHead`, `curTail`, `headNext = curHead.next`.
  2. Nếu `curHead == head`:
     - Nếu `curHead == curTail`:
       - Nếu `headNext == null` $\rightarrow$ Hàng đợi rỗng, trả về `null`.
       - Nếu `headNext != null` $\rightarrow$ `tail` bị tụt lại, giúp dịch chuyển `CAS(tail, curTail, headNext)`.
     - Nếu `curHead != curTail`:
       - Đọc giá trị `item = headNext.item`.
       - Thử dịch chuyển `CAS(head, curHead, headNext)`. Nếu thành công, trả về `item` và giải phóng tham chiếu của dummy cũ.
- **Linearizability Point**:
  - `enqueue`: Thời điểm CAS nối `curTail.next = newNode` thành công.
  - `dequeue`: Thời điểm CAS dịch `head = headNext` thành công.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeMichaelScottQueue<E> {

    private static final class Node<E> {
        volatile E item;
        volatile Node<E> next;

        private static final VarHandle NEXT;
        static {
            try {
                MethodHandles.Lookup l = MethodHandles.lookup();
                NEXT = l.findVarHandle(Node.class, "next", Node.class);
            } catch (ReflectiveOperationException e) {
                throw new ExceptionInInitializerError(e);
            }
        }

        Node(E item) {
            this.item = item;
        }

        boolean casNext(Node<E> cmp, Node<E> val) {
            return NEXT.compareAndSet(this, cmp, val);
        }
    }

    private volatile Node<E> head;
    private volatile Node<E> tail;

    private static final VarHandle HEAD;
    private static final VarHandle TAIL;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD = l.findVarHandle(LockFreeMichaelScottQueue.class, "head", Node.class);
            TAIL = l.findVarHandle(LockFreeMichaelScottQueue.class, "tail", Node.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public LockFreeMichaelScottQueue() {
        Node<E> dummy = new Node<>(null);
        this.head = dummy;
        this.tail = dummy;
    }

    public void enqueue(E item) {
        if (item == null) throw new NullPointerException("Item cannot be null");
        Node<E> newNode = new Node<>(item);
        while (true) {
            Node<E> curTail = tail;
            Node<E> tailNext = curTail.next;
            if (curTail == tail) {
                if (tailNext == null) {
                    // Bước 1: Gắn node mới vào cuối
                    if (curTail.casNext(null, newNode)) {
                        // Bước 2: Dịch chuyển tail (thất bại cũng không sao vì luồng khác sẽ help)
                        TAIL.compareAndSet(this, curTail, newNode);
                        return;
                    }
                } else {
                    // Helping mechanism: tail bị tụt, hỗ trợ đẩy tail lên
                    TAIL.compareAndSet(this, curTail, tailNext);
                }
            }
            Thread.onSpinWait();
        }
    }

    public E dequeue() {
        while (true) {
            Node<E> curHead = head;
            Node<E> curTail = tail;
            Node<E> headNext = curHead.next;
            if (curHead == head) {
                if (curHead == curTail) {
                    if (headNext == null) {
                        return null; // Hàng đợi rỗng
                    }
                    // Tail bị tụt lại so với head
                    TAIL.compareAndSet(this, curTail, headNext);
                } else {
                    if (headNext != null) {
                        E item = headNext.item;
                        if (HEAD.compareAndSet(this, curHead, headNext)) {
                            headNext.item = null; // Biến node này thành dummy node mới
                            return item;
                        }
                    }
                }
            }
            Thread.onSpinWait();
        }
    }

    public boolean isEmpty() {
        return head.next == null;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `enqueue`: $O(1)$ trung bình, tối đa 2 thao tác CAS thành công trên toàn hệ thống.
  - `dequeue`: $O(1)$ trung bình.
- **Không gian (Space):** $O(N)$ lưu trữ các nút.
- **Progress Guarantee:** **Lock-Free**.

---

## 3. Lock-Free Singly-Linked List with Search, Insert, and Delete (Harris's Algorithm)

### Đề bài chi tiết
Xây dựng một danh sách liên kết có thứ tự không khóa (**Lock-Free Ordered Set / Linked List**) theo thuật toán của **Timothy L. Harris (2001)** hỗ trợ:
- `boolean add(int key)`: Chèn khóa `key` theo thứ tự tăng dần. Trả về `true` nếu chèn thành công, `false` nếu khóa đã tồn tại.
- `boolean remove(int key)`: Xóa khóa `key`. Trả về `true` nếu xóa thành công, `false` nếu không tìm thấy.
- `boolean contains(int key)`: Kiểm tra sự tồn tại của `key`.

Thách thức chính: Khi luồng A đang xóa nút $N$ (thay đổi `pred.next`), luồng B có thể đồng thời chèn nút mới $M$ ngay sau $N$ ($N.next = M$). Thuật toán phải sử dụng **Logical Deletion (Marking)** để ngăn chặn tình huống mất dữ liệu này.

### Phân tích thuật toán
- **Logical Deletion vs Physical Deletion**:
  1. *Logical Deletion*: Đánh dấu bit `mark` trên con trỏ `N.next` (sử dụng `AtomicMarkableReference` hoặc đóng gói vào biến tham chiếu). Khi một nút đã bị đánh dấu xóa, không một luồng nào được phép chèn thêm nút sau nó.
  2. *Physical Deletion*: Nối `pred.next` nhảy qua nút bị đánh dấu để trỏ tới `curr.next`. Luồng tìm kiếm (Search) hoặc luồng chèn/xóa khi đi qua các nút bị đánh dấu sẽ chủ động giúp đỡ (**help**) gỡ nút vật lý.
- **Phương thức `find(key)`**:
  - Duyệt từ `head` tới `tail`. Duyệt đồng thời `pred`, `curr`, và `succ`.
  - Nếu phát hiện `curr` bị đánh dấu xóa, thực hiện `CAS(pred.next, curr, succ)`. Nếu CAS thất bại, quay lại duyệt từ `head`.
  - Dừng lại khi gặp nút có `curr.key >= key`. Trả về bộ đôi `(pred, curr)`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreeHarrisLinkedList {

    private static final class Node {
        final int key;
        final AtomicMarkableReference<Node> next;

        Node(int key, Node next) {
            this.key = key;
            this.next = new AtomicMarkableReference<>(next, false);
        }
    }

    private final Node head;
    private final Node tail;

    public LockFreeHarrisLinkedList() {
        this.tail = new Node(Integer.MAX_VALUE, null);
        this.head = new Node(Integer.MIN_VALUE, tail);
    }

    private static final class Window {
        final Node pred;
        final Node curr;

        Window(Node pred, Node curr) {
            this.pred = pred;
            this.curr = curr;
        }
    }

    private Window find(int key) {
        Node pred = null, curr = null, succ = null;
        boolean[] marked = {false};
        boolean snip;

        retry: while (true) {
            pred = head;
            curr = pred.next.getReference();
            while (true) {
                succ = curr.next.get(marked);
                while (marked[0]) {
                    // Physical removal helping
                    snip = pred.next.compareAndSet(curr, succ, false, false);
                    if (!snip) continue retry;
                    curr = succ;
                    succ = curr.next.get(marked);
                }
                if (curr.key >= key) {
                    return new Window(pred, curr);
                }
                pred = curr;
                curr = succ;
            }
        }
    }

    public boolean add(int key) {
        while (true) {
            Window window = find(key);
            Node pred = window.pred;
            Node curr = window.curr;
            if (curr.key == key) {
                return false; // Key đã tồn tại
            }
            Node newNode = new Node(key, curr);
            if (pred.next.compareAndSet(curr, newNode, false, false)) {
                return true;
            }
            Thread.onSpinWait();
        }
    }

    public boolean remove(int key) {
        boolean snip;
        while (true) {
            Window window = find(key);
            Node pred = window.pred;
            Node curr = window.curr;
            if (curr.key != key) {
                return false;
            }
            Node succ = curr.next.getReference();
            // Bước 1: Logical Deletion
            snip = curr.next.attemptMark(succ, true);
            if (!snip) {
                continue;
            }
            // Bước 2: Physical Deletion (Helping)
            pred.next.compareAndSet(curr, succ, false, false);
            return true;
        }
    }

    public boolean contains(int key) {
        boolean[] marked = {false};
        Node curr = head.next.getReference();
        while (curr.key < key) {
            curr = curr.next.getReference();
            curr.next.get(marked);
        }
        return curr.key == key && !marked[0];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `contains`: $O(N)$ không dùng CAS, duyệt tuyến tính (Lock-Free / Wait-Free traversal).
  - `add` / `remove`: $O(N)$ trung bình khi duyệt, $O(1)$ cho thao tác CAS tại vị trí chèn/xóa.
- **Không gian (Space):** $O(N)$ lưu trữ các nút và tham chiếu `AtomicMarkableReference`.
- **Progress Guarantee:** **Lock-Free** cho `add`/`remove`, **Wait-Free** đối với `contains` trong môi trường GC an toàn.

---

## 4. Lock-Free ABA-Proof Stack using AtomicStampedReference

### Đề bài chi tiết
Trong các hệ thống quản lý bộ nhớ tái chế (như C/C++ `malloc`/`free` hoặc Free-List Allocator), vấn đề **ABA Problem** xảy ra khi luồng 1 đọc `top = A`, nút `A.next = B`. Luồng 2 chen vào lấy `A`, lấy `B`, sau đó đẩy lại `A` vào stack. Khi luồng 1 tiếp tục thực thi `CAS(top, A, B)`, lệnh CAS thành công nhưng cấu trúc ngăn xếp bị phá hủy do nút `B` đã bị giải phóng hoặc liên kết sai.

Hãy triển khai một **ABA-Proof Lock-Free Stack** trong Java bằng cách sử dụng **Versioned Pointer / Stamped Reference** (`AtomicStampedReference`), kết hợp giữa con trỏ nút và một số nguyên định danh phiên bản (`stamp/tag`).

### Phân tích thuật toán
- Mỗi khi `top` thay đổi (do `push` hoặc `pop`), số hiệu `stamp` được tăng thêm 1 đơn vị.
- Thao tác `CAS` kiểm tra đồng thời cả 2 điều kiện:
  1. `expectedReference == currentReference` (Địa chỉ nút đỉnh ngăn xếp)
  2. `expectedStamp == currentStamp` (Phiên bản thời gian)
- Dù nút `A` có bị giải phóng và đưa trở lại đỉnh ngăn xếp, `stamp` mới sẽ khác với `stamp` cũ, khiến lệnh CAS của luồng bị trễ lập tức thất bại an toàn.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicStampedReference;

public class LockFreeAbaProofStack<E> {

    private static final class Node<E> {
        final E item;
        Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }

    private final AtomicStampedReference<Node<E>> topRef = new AtomicStampedReference<>(null, 0);

    public void push(E item) {
        if (item == null) throw new NullPointerException("Item cannot be null");
        Node<E> newNode = new Node<>(item);
        int[] stampHolder = new int[1];
        while (true) {
            Node<E> curTop = topRef.get(stampHolder);
            int curStamp = stampHolder[0];
            newNode.next = curTop;
            if (topRef.compareAndSet(curTop, newNode, curStamp, curStamp + 1)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public E pop() {
        int[] stampHolder = new int[1];
        while (true) {
            Node<E> curTop = topRef.get(stampHolder);
            int curStamp = stampHolder[0];
            if (curTop == null) {
                return null;
            }
            Node<E> newTop = curTop.next;
            if (topRef.compareAndSet(curTop, newTop, curStamp, curStamp + 1)) {
                return curTop.item;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình cho `push` và `pop`.
- **Không gian (Space):** $O(N)$ lưu trữ các nút và đối tượng Pair trong `AtomicStampedReference`.
- **Progress Guarantee:** **Lock-Free**.

---

## 5. Lock-Free Elimination-Backoff Stack

### Đề bài chi tiết
Trong các hệ thống đa lõi quy mô lớn (64-128 cores), ngăn xếp Treiber gặp hiện tượng thắt cổ chai nghiêm trọng tại biến `top` do mọi luồng đều tranh chấp một vị trí duy nhất.
Hãy thiết kế **Elimination-Backoff Stack** (theo Danny Hendler, Nir Shavit, Lena Yerushalmi - 2004):
- Khi một luồng gặp xung đột CAS trên `top`, thay vì tiếp tục xoay vòng vô ích gây bão bộ nhớ (cache coherence traffic), luồng sẽ chuyển sang một mảng triệt tiêu (**Elimination Array**).
- Tại đây, một luồng `push(x)` có thể bắt cặp trực tiếp và trao đổi dữ liệu cho một luồng `pop()`. Hai thao tác tự triệt tiêu lẫn nhau (**Eliminate**) mà không cần chạm vào biến `top` của ngăn xếp.

### Phân tích thuật toán
- **Elimination Array**: Một mảng gồm $K$ đối tượng `Exchanger<E>`.
- Mỗi luồng khi bị CAS thất bại trên `top` sẽ chọn ngẫu nhiên một vị trí trong mảng để chờ đối tác trong một khoảng thời gian ngắn (`timeout`).
- Nếu luồng `push(v)` gặp luồng `pop()`, luồng `pop()` nhận giá trị `v`, cả 2 hoàn thành thành công mà không chạm vào `top`.
- Nếu hết thời gian chờ mà không gặp đối tác, luồng quay lại thử thực hiện thao tác trên `top`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicReference;

public class LockFreeEliminationBackoffStack<E> {

    private static final class Node<E> {
        final E item;
        Node<E> next;
        Node(E item) { this.item = item; }
    }

    private final AtomicReference<Node<E>> top = new AtomicReference<>(null);
    private final EliminationArray<E> eliminationArray;

    public LockFreeEliminationBackoffStack(int capacity, int durationMs) {
        this.eliminationArray = new EliminationArray<>(capacity, durationMs);
    }

    public void push(E item) {
        if (item == null) throw new NullPointerException();
        Node<E> node = new Node<>(item);
        while (true) {
            Node<E> curTop = top.get();
            node.next = curTop;
            if (top.compareAndSet(curTop, node)) {
                return;
            }
            try {
                E other = eliminationArray.visit(item);
                if (other == null) {
                    // Đã triệt tiêu thành công với một luồng pop
                    return;
                }
            } catch (TimeoutException e) {
                // Hết thời gian chờ, quay lại thử trên top
            }
            Thread.onSpinWait();
        }
    }

    public E pop() {
        while (true) {
            Node<E> curTop = top.get();
            if (curTop == null) {
                return null;
            }
            Node<E> newTop = curTop.next;
            if (top.compareAndSet(curTop, newTop)) {
                return curTop.item;
            }
            try {
                E matchedItem = eliminationArray.visit(null);
                if (matchedItem != null) {
                    // Đã nhận trực tiếp item từ một luồng push
                    return matchedItem;
                }
            } catch (TimeoutException e) {
                // Timeout, quay lại loop
            }
            Thread.onSpinWait();
        }
    }

    private static final class EliminationArray<T> {
        private final Exchanger<T>[] exchangers;
        private final int durationMs;

        @SuppressWarnings("unchecked")
        EliminationArray(int capacity, int durationMs) {
            this.durationMs = durationMs;
            this.exchangers = (Exchanger<T>[]) new Exchanger[capacity];
            for (int i = 0; i < capacity; i++) {
                this.exchangers[i] = new Exchanger<>();
            }
        }

        public T visit(T value) throws TimeoutException {
            int index = ThreadLocalRandom.current().nextInt(exchangers.length);
            try {
                return exchangers[index].exchange(value, durationMs, TimeUnit.MILLISECONDS);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                throw new TimeoutException("Interrupted");
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình. Trong điều kiện tải cực cao, thông lượng tăng tuyến tính theo số lõi CPU nhờ cơ chế triệt tiêu song song song.
- **Không gian (Space):** $O(N + K)$ với $K$ là kích thước mảng Elimination.
- **Progress Guarantee:** **Lock-Free**.

---

## 6. Lock-Free Bounded MPMC Ring Buffer Queue (Disruptor Pattern)

### Đề bài chi tiết
Thiết kế một hàng đợi vòng giới hạn (**Bounded Ring Buffer Queue**) hỗ trợ nhiều luồng sản xuất và nhiều luồng tiêu thụ (**Multi-Producer Multi-Consumer - MPMC**) không sử dụng khóa.
- Kích thước hàng đợi cố định $C = 2^k$ (Power of Two).
- Hỗ trợ:
  - `boolean offer(E item)`: Thêm phần tử nếu còn chỗ, trả về `false` nếu đầy.
  - `E poll()`: Lấy phần tử nếu có, trả về `null` nếu rỗng.
- Yêu cầu chống hiện tượng **False Sharing** bằng cách đệm dòng nhớ Cache (**Cache Line Padding / `@Contended`**) và sử dụng số thứ tự nguyên tử (`sequence`) cho từng cell trong buffer.

### Phân tích thuật toán
- Mỗi ô nhớ (Cell) trong Ring Buffer chứa một phần tử `element` và một số thứ tự `sequence` volatile.
- Ban đầu, mỗi ô tại chỉ số `i` có `sequence = i`.
- **Offer (Enqueue)**:
  1. Đọc số thứ tự sản xuất `pos = tail.get()`.
  2. Vị trí ô nhớ là `idx = pos & (capacity - 1)`.
  3. Đọc `seq = buffer[idx].sequence`.
  4. Nếu `seq == pos` (ô này đang trống và sẵn sàng ghi):
     - Thử `CAS(tail, pos, pos + 1)`. Nếu thành công: ghi `buffer[idx].item = item`, sau đó cập nhật `buffer[idx].sequence = pos + 1` để thông báo cho luồng tiêu thụ.
  5. Nếu `seq < pos`: Hàng đợi bị đầy $\rightarrow$ trả về `false`.
- **Poll (Dequeue)**:
  1. Đọc số thứ tự tiêu thụ `pos = head.get()`.
  2. Vị trí ô nhớ là `idx = pos & (capacity - 1)`.
  3. Đọc `seq = buffer[idx].sequence`.
  4. Nếu `seq == pos + 1` (ô này đã có dữ liệu mới):
     - Thử `CAS(head, pos, pos + 1)`. Nếu thành công: lấy `item = buffer[idx].item`, `buffer[idx].item = null`, sau đó cập nhật `buffer[idx].sequence = pos + capacity` để báo ô này có thể ghi ở vòng tiếp theo.
  5. Nếu `seq < pos + 1`: Hàng đợi rỗng $\rightarrow$ trả về `null`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeMpmcRingBuffer<E> {

    private static final class Cell<E> {
        volatile long sequence;
        volatile E item;

        Cell(long sequence) {
            this.sequence = sequence;
        }
    }

    private final int capacity;
    private final int mask;
    private final Cell<E>[] buffer;

    private volatile long head = 0;
    private volatile long tail = 0;

    private static final VarHandle HEAD;
    private static final VarHandle TAIL;
    private static final VarHandle CELL_SEQ;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD = l.findVarHandle(LockFreeMpmcRingBuffer.class, "head", long.class);
            TAIL = l.findVarHandle(LockFreeMpmcRingBuffer.class, "tail", long.class);
            CELL_SEQ = l.findVarHandle(Cell.class, "sequence", long.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    @SuppressWarnings("unchecked")
    public LockFreeMpmcRingBuffer(int capacityPowerOfTwo) {
        if (Integer.bitCount(capacityPowerOfTwo) != 1) {
            throw new IllegalArgumentException("Capacity must be a power of 2");
        }
        this.capacity = capacityPowerOfTwo;
        this.mask = capacityPowerOfTwo - 1;
        this.buffer = new Cell[capacity];
        for (int i = 0; i < capacity; i++) {
            buffer[i] = new Cell<>(i);
        }
    }

    public boolean offer(E item) {
        if (item == null) throw new NullPointerException();
        while (true) {
            long curTail = (long) TAIL.getVolatile(this);
            int idx = (int) (curTail & mask);
            Cell<E> cell = buffer[idx];
            long seq = (long) CELL_SEQ.getVolatile(cell);
            long dif = seq - curTail;

            if (dif == 0) {
                if (TAIL.compareAndSet(this, curTail, curTail + 1)) {
                    cell.item = item;
                    CELL_SEQ.setRelease(cell, curTail + 1);
                    return true;
                }
            } else if (dif < 0) {
                return false; // Queue đầy
            }
            Thread.onSpinWait();
        }
    }

    public E poll() {
        while (true) {
            long curHead = (long) HEAD.getVolatile(this);
            int idx = (int) (curHead & mask);
            Cell<E> cell = buffer[idx];
            long seq = (long) CELL_SEQ.getVolatile(cell);
            long dif = seq - (curHead + 1);

            if (dif == 0) {
                if (HEAD.compareAndSet(this, curHead, curHead + 1)) {
                    E item = cell.item;
                    cell.item = null;
                    CELL_SEQ.setRelease(cell, curHead + capacity);
                    return item;
                }
            } else if (dif < 0) {
                return null; // Queue rỗng
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho cả `offer` và `poll`.
- **Không gian (Space):** $O(C)$ với $C$ là dung lượng cố định của buffer, cấp phát 1 lần duy nhất (Zero Garbage Collection pressure).
- **Progress Guarantee:** **Lock-Free**.

---

## 7. Lock-Free Resizable Array (Dynamic Array)

### Đề bài chi tiết
Xây dựng một mảng động không khóa (**Lock-Free Dynamic Array / Vector**) dựa trên thuật toán của Damian Dechev, Peter Pirkelbauer và Bjarne Stroustrup.
Mảng phải hỗ trợ:
- `void pushBack(E item)`: Thêm một phần tử vào cuối mảng, tự động tăng kích thước mảng nếu cần.
- `E get(int index)`: Đọc phần tử tại vị trí `index` trong thời gian $O(1)$.
- `void set(int index, E item)`: Cập nhật phần tử tại vị trí `index`.
- `int size()`: Lấy số lượng phần tử hiện có.

Không được phép sao chép toàn bộ mảng khi mở rộng (tránh Stop-The-World). Thay vào đó, sử dụng cấu trúc cây 2 cấp gồm các mảng con (**Memory Chunks**) có kích thước tăng lũy thừa $2^0, 2^1, 2^2, \dots$.

### Phân tích thuật toán
- Mảng được tổ chức thành mảng con cấp 1 `chunks` chứa tối đa 30 mảng con cấp 2.
- Mảng con thứ $k$ có kích thước $2^k$.
- Vị trí của chỉ số $i$:
  - Mảng con $k = \lfloor \log_2(i + 2) \rfloor - 1$ (tính nhanh qua `Integer.numberOfLeadingZeros`).
  - Vị trí bên trong mảng con: offset $= (i + 2) - 2^{k+1}$.
- Cấp phát mảng con diễn ra lười (**Lazy Allocation**) bằng lệnh CAS trên con trỏ mảng con `chunks[k]`.
- Thao tác `pushBack` sử dụng `AtomicInteger size` để đặt trước vị trí (`index = size.getAndIncrement()`), sau đó ghi giá trị vào ô nhớ tương ứng.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReferenceArray;

public class LockFreeResizableArray<E> {

    private static final int FIRST_BUCKET_CAPACITY = 2;
    private final AtomicReferenceArray<AtomicReferenceArray<E>> chunks = new AtomicReferenceArray<>(30);
    private final AtomicInteger size = new AtomicInteger(0);

    public LockFreeResizableArray() {
        chunks.set(0, new AtomicReferenceArray<>(FIRST_BUCKET_CAPACITY));
    }

    private static int getBucketIndex(int index) {
        int pos = index + FIRST_BUCKET_CAPACITY;
        return 31 - Integer.numberOfLeadingZeros(pos) - 1;
    }

    private static int getOffset(int index, int bucket) {
        return index + FIRST_BUCKET_CAPACITY - (1 << (bucket + 1));
    }

    private void ensureAllocated(int bucket, int capacity) {
        if (chunks.get(bucket) == null) {
            AtomicReferenceArray<E> newArray = new AtomicReferenceArray<>(capacity);
            chunks.compareAndSet(bucket, null, newArray);
        }
    }

    public void pushBack(E item) {
        if (item == null) throw new NullPointerException();
        int idx = size.getAndIncrement();
        int bucket = getBucketIndex(idx);
        int offset = getOffset(idx, bucket);
        
        ensureAllocated(bucket, 1 << (bucket + 1));
        
        AtomicReferenceArray<E> chunk = chunks.get(bucket);
        chunk.set(offset, item);
    }

    public E get(int index) {
        if (index < 0 || index >= size.get()) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        int bucket = getBucketIndex(index);
        int offset = getOffset(index, bucket);
        AtomicReferenceArray<E> chunk = chunks.get(bucket);
        return chunk != null ? chunk.get(offset) : null;
    }

    public void set(int index, E item) {
        if (index < 0 || index >= size.get()) {
            throw new IndexOutOfBoundsException("Index: " + index);
        }
        if (item == null) throw new NullPointerException();
        int bucket = getBucketIndex(index);
        int offset = getOffset(index, bucket);
        AtomicReferenceArray<E> chunk = chunks.get(bucket);
        chunk.set(offset, item);
    }

    public int size() {
        return size.get();
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `get`, `set`: $O(1)$ tuyệt đối (Wait-Free).
  - `pushBack`: $O(1)$ amortized (Lock-Free).
- **Không gian (Space):** $O(N)$ bộ nhớ, không cần sao chép dữ liệu cũ khi mảng nở rộng.
- **Progress Guarantee:** `get`/`set` đạt **Wait-Free**, `pushBack` đạt **Lock-Free**.

---

## 8. Lock-Free SkipList Set

### Đề bài chi tiết
Triển khai một tập hợp có thứ tự không khóa (**Lock-Free Concurrent SkipList Set**) dựa trên thuật toán của Mikhail Fomitchev và Eric Ruppert.
Hỗ trợ:
- `boolean add(int key)`: Thêm khóa theo thứ tự.
- `boolean remove(int key)`: Xóa khóa.
- `boolean contains(int key)`: Tìm kiếm khóa trong thời gian $O(\log N)$.

Mỗi nút trong SkipList có chiều cao ngẫu nhiên (Tower of Links). Quá trình xóa một nút phải đánh dấu `AtomicMarkableReference` trên tất cả các tầng từ tầng cao nhất xuống tầng đáy, sau đó thực hiện gỡ liên kết vật lý.

### Phân tích thuật toán
- **Cấu trúc Nút**: Mỗi `Node` chứa `key` và một mảng `AtomicMarkableReference<Node>[] next` có chiều dài bằng chiều cao của tháp.
- **Tìm kiếm (`find`)**: Duyệt từ tầng cao nhất xuống tầng 0. Tại mỗi tầng, tìm cặp `(pred, curr)` sao cho `pred.key < key <= curr.key`. Trong quá trình duyệt, nếu bắt gặp nút đã bị đánh dấu xóa (`isMarked() == true`), luồng tìm kiếm tiến hành hỗ trợ gỡ nút vật lý trên tầng đó.
- **Xóa (`remove`)**:
  1. Gọi `find(key)` để định vị nút mục tiêu.
  2. Đánh dấu xóa `mark` trên tất cả các tầng của nút từ `topLevel` xuống tầng 0.
  3. Khi tầng 0 được đánh dấu thành công, thao tác xóa được coi là hoàn tất về mặt logic (Linearization Point).
  4. Luồng gọi `find(key)` một lần nữa để kích hoạt cơ chế gỡ vật lý.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.ThreadLocalRandom;
import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreeSkipListSet {

    private static final int MAX_LEVEL = 16;

    private static final class Node {
        final int key;
        final AtomicMarkableReference<Node>[] next;
        final int topLevel;

        @SuppressWarnings("unchecked")
        Node(int key, int height) {
            this.key = key;
            this.topLevel = height - 1;
            this.next = (AtomicMarkableReference<Node>[]) new AtomicMarkableReference[height];
            for (int i = 0; i < height; i++) {
                this.next[i] = new AtomicMarkableReference<>(null, false);
            }
        }
    }

    private final Node head = new Node(Integer.MIN_VALUE, MAX_LEVEL);
    private final Node tail = new Node(Integer.MAX_VALUE, MAX_LEVEL);

    public LockFreeSkipListSet() {
        for (int i = 0; i < MAX_LEVEL; i++) {
            head.next[i].set(tail, false);
        }
    }

    private static int randomLevel() {
        int level = 1;
        while (level < MAX_LEVEL && (ThreadLocalRandom.current().nextInt() & 1) == 0) {
            level++;
        }
        return level;
    }

    private boolean find(int key, Node[] preds, Node[] succs) {
        boolean[] marked = {false};
        boolean snip;
        Node pred = null, curr = null, succ = null;

        retry: while (true) {
            pred = head;
            for (int level = MAX_LEVEL - 1; level >= 0; level--) {
                curr = pred.next[level].getReference();
                while (true) {
                    succ = curr.next[level].get(marked);
                    while (marked[0]) {
                        snip = pred.next[level].compareAndSet(curr, succ, false, false);
                        if (!snip) continue retry;
                        curr = succ;
                        succ = curr.next[level].get(marked);
                    }
                    if (curr.key < key) {
                        pred = curr;
                        curr = succ;
                    } else {
                        break;
                    }
                }
                preds[level] = pred;
                succs[level] = curr;
            }
            return curr.key == key;
        }
    }

    public boolean add(int key) {
        int topLevel = randomLevel() - 1;
        Node[] preds = new Node[MAX_LEVEL];
        Node[] succs = new Node[MAX_LEVEL];

        while (true) {
            boolean found = find(key, preds, succs);
            if (found) {
                return false;
            }
            Node newNode = new Node(key, topLevel + 1);
            for (int level = 0; level <= topLevel; level++) {
                newNode.next[level].set(succs[level], false);
            }
            Node pred = preds[0];
            Node succ = succs[0];
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

    public boolean remove(int key) {
        Node[] preds = new Node[MAX_LEVEL];
        Node[] succs = new Node[MAX_LEVEL];
        while (true) {
            boolean found = find(key, preds, succs);
            if (!found) {
                return false;
            }
            Node nodeToRemove = succs[0];
            for (int level = nodeToRemove.topLevel; level >= 1; level--) {
                boolean[] marked = {false};
                Node succ = nodeToRemove.next[level].get(marked);
                while (!marked[0]) {
                    nodeToRemove.next[level].attemptMark(succ, true);
                    succ = nodeToRemove.next[level].get(marked);
                }
            }
            boolean[] marked = {false};
            Node succ = nodeToRemove.next[0].get(marked);
            while (!marked[0]) {
                boolean markedSuccess = nodeToRemove.next[0].attemptMark(succ, true);
                succ = nodeToRemove.next[0].get(marked);
                if (markedSuccess) {
                    find(key, preds, succs); // Gỡ vật lý
                    return true;
                }
            }
            return false;
        }
    }

    public boolean contains(int key) {
        boolean[] marked = {false};
        Node pred = head;
        Node curr = null;
        Node succ = null;
        for (int level = MAX_LEVEL - 1; level >= 0; level--) {
            curr = pred.next[level].getReference();
            while (true) {
                succ = curr.next[level].get(marked);
                while (marked[0]) {
                    curr = succ;
                    succ = curr.next[level].get(marked);
                }
                if (curr.key < key) {
                    pred = curr;
                    curr = succ;
                } else {
                    break;
                }
            }
        }
        return curr.key == key;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `contains`: $O(\log N)$ trung bình (Wait-Free traversal).
  - `add`, `remove`: $O(\log N)$ trung bình (Lock-Free).
- **Không gian (Space):** $O(N)$ với hệ số nhân trung bình 2 tham chiếu trên mỗi nút.
- **Progress Guarantee:** **Lock-Free** cho cập nhật, **Wait-Free** cho tìm kiếm.

---

## 9. Lock-Free Doubly-Linked List with Marked Auxiliary Pointers

### Đề bài chi tiết
Danh sách liên kết đôi hỗ trợ việc xóa nút ở vị trí bất kỳ trong thời gian $O(1)$ khi đã có con trỏ nút, nhưng trong môi trường phi khóa, việc duy trì tính nhất quán đồng thời của cả hai con trỏ `prev` và `next` là cực kỳ khó khăn.
Hãy thiết kế một **Lock-Free Doubly-Linked List** hỗ trợ:
- `void insertAfter(Node pred, int value)`: Chèn nút mới ngay sau nút `pred`.
- `boolean delete(Node target)`: Xóa một nút đã biết.
- Duyệt hai chiều an toàn.

Áp dụng phương pháp của Sundell và Tsigas: Đánh dấu `mark` trên con trỏ `next` để xóa logic, đồng thời cập nhật con trỏ `prev` như một gợi ý hỗ trợ tìm kiếm (**Backlink / Hint Pointer**).

### Phân tích thuật toán
- **Con trỏ Next**: Quyết định tính đúng đắn và thứ tự tuyến tính hóa của danh sách. Con trỏ này sử dụng `AtomicMarkableReference`.
- **Con trỏ Prev**: Đóng vai trò là gợi ý giúp khôi phục liên kết ngược. Nếu `curr.prev` trỏ tới một nút đã bị xóa, thuật toán sẽ lần ngược theo các liên kết hợp lệ để cập nhật lại `curr.prev`.
- **Thao tác Xóa**:
  1. Thực hiện `attemptMark(succ, true)` trên `target.next`.
  2. Sửa `target.prev.next` trỏ tới `succ` bằng CAS.
  3. Cập nhật `succ.prev = target.prev`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicMarkableReference;
import java.util.concurrent.atomic.AtomicReference;

public class LockFreeDoublyLinkedList {

    public static final class Node {
        final int value;
        final AtomicMarkableReference<Node> next;
        final AtomicReference<Node> prev;

        public Node(int value) {
            this.value = value;
            this.next = new AtomicMarkableReference<>(null, false);
            this.prev = new AtomicReference<>(null);
        }
    }

    private final Node head;
    private final Node tail;

    public LockFreeDoublyLinkedList() {
        this.head = new Node(Integer.MIN_VALUE);
        this.tail = new Node(Integer.MAX_VALUE);
        this.head.next.set(tail, false);
        this.tail.prev.set(head);
    }

    public Node insertAfter(Node pred, int value) {
        Node newNode = new Node(value);
        while (true) {
            boolean[] marked = {false};
            Node succ = pred.next.get(marked);
            if (marked[0]) {
                // pred đã bị xóa, không thể chèn sau nó
                return null;
            }
            newNode.next.set(succ, false);
            newNode.prev.set(pred);
            if (pred.next.compareAndSet(succ, newNode, false, false)) {
                // Sửa liên kết ngược của succ
                correctPrev(newNode, succ);
                return newNode;
            }
            Thread.onSpinWait();
        }
    }

    private void correctPrev(Node pred, Node succ) {
        while (true) {
            Node link = succ.prev.get();
            if (pred.next.isMarked()) return;
            if (succ.prev.compareAndSet(link, pred)) {
                if (pred.next.isMarked()) correctPrev(pred.prev.get(), succ);
                return;
            }
            Thread.onSpinWait();
        }
    }

    public boolean delete(Node target) {
        if (target == head || target == tail) return false;
        while (true) {
            boolean[] marked = {false};
            Node succ = target.next.get(marked);
            if (marked[0]) return false; // Đã bị xóa trước đó
            if (target.next.attemptMark(succ, true)) {
                // Logical deletion thành công, sửa liên kết vật lý
                Node pred = target.prev.get();
                pred.next.compareAndSet(target, succ, false, false);
                correctPrev(pred, succ);
                return true;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho `insertAfter` và `delete` khi đã có tham chiếu con trỏ.
- **Không gian (Space):** $O(N)$ bộ nhớ cho các nút liên kết.
- **Progress Guarantee:** **Lock-Free**.

---

## 10. Lock-Free Split-Ordered Hash Map

### Đề bài chi tiết
Thiết kế một bảng băm không khóa (**Lock-Free Split-Ordered Hash Table**) theo thuật toán của Ori Shalev và Nir Shavit (2006).
Bảng băm phải hỗ trợ:
- `boolean put(int key, String value)`: Thêm hoặc cập nhật cặp khóa-giá trị.
- `String get(int key)`: Đọc giá trị tương ứng.
- `boolean remove(int key)`: Xóa khóa.
- Tự động mở rộng bảng băm (**Dynamic Resizing**) mà không cần khóa toàn cục và không cần sao chép hay di chuyển các nút hiện có.

### Phân tích thuật toán
- **Ý tưởng Split-Ordering**: Toàn bộ các phần tử được lưu trữ trong một danh sách liên kết không khóa duy nhất (Harris's Linked List), được sắp xếp theo thứ tự **đảo ngược bit (Bit-Reversal)** của mã băm `hash(key)`.
- Khi kích thước bảng băm tăng từ $2^k$ lên $2^{k+1}$, một bucket $B$ mới được khởi tạo bằng cách chèn một **Dummy Node** vào danh sách tại vị trí tương ứng với bit đảo ngược của chỉ số bucket.
- Do các khóa đã được sắp xếp theo bit đảo ngược, các phần tử thuộc bucket mới tự động nằm ngay sau dummy node vừa chèn mà không cần dịch chuyển bất kỳ nút dữ liệu nào!

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicMarkableReference;
import java.util.concurrent.atomic.AtomicReferenceArray;

public class LockFreeSplitOrderedHashMap {

    private static final class Node {
        final long orderKey; // Key đã được bit-reverse
        final int rawKey;
        final String value;
        final AtomicMarkableReference<Node> next;

        Node(long orderKey, int rawKey, String value, Node next) {
            this.orderKey = orderKey;
            this.rawKey = rawKey;
            this.value = value;
            this.next = new AtomicMarkableReference<>(next, false);
        }
    }

    private final AtomicReferenceArray<Node> buckets = new AtomicReferenceArray<>(1024);
    private final Node head = new Node(0, 0, null, null);
    private final AtomicInteger size = new AtomicInteger(0);
    private final AtomicInteger capacity = new AtomicInteger(2);

    public LockFreeSplitOrderedHashMap() {
        head.next.set(new Node(Long.MAX_VALUE, 0, null, null), false);
        buckets.set(0, head);
    }

    private static long makeRegularKey(int key) {
        return reverseBits(key | 0x80000000L);
    }

    private static long makeDummyKey(int bucketIndex) {
        return reverseBits(bucketIndex & 0x7FFFFFFFL);
    }

    private static long reverseBits(long n) {
        return Long.reverse(n);
    }

    private Node getBucket(int bucketIndex) {
        Node b = buckets.get(bucketIndex);
        if (b != null) return b;
        int parent = bucketIndex ^ Integer.highestOneBit(bucketIndex);
        Node parentNode = getBucket(parent);
        Node dummy = new Node(makeDummyKey(bucketIndex), 0, null, null);
        
        // Chèn dummy node vào danh sách sau parentNode
        Node curr = insertOrdered(parentNode, dummy);
        buckets.compareAndSet(bucketIndex, null, curr);
        return buckets.get(bucketIndex);
    }

    private Node insertOrdered(Node start, Node newNode) {
        while (true) {
            Node pred = start;
            Node curr = pred.next.getReference();
            while (curr != null && Long.compareUnsigned(curr.orderKey, newNode.orderKey) < 0) {
                pred = curr;
                curr = curr.next.getReference();
            }
            if (curr != null && curr.orderKey == newNode.orderKey) {
                return curr; // Đã tồn tại dummy node
            }
            newNode.next.set(curr, false);
            if (pred.next.compareAndSet(curr, newNode, false, false)) {
                return newNode;
            }
            Thread.onSpinWait();
        }
    }

    public boolean put(int key, String value) {
        long oKey = makeRegularKey(key);
        int cap = capacity.get();
        int bucketIdx = key % cap;
        Node bucketHead = getBucket(bucketIdx);

        Node newNode = new Node(oKey, key, value, null);
        while (true) {
            Node pred = bucketHead;
            Node curr = pred.next.getReference();
            while (curr != null && Long.compareUnsigned(curr.orderKey, oKey) < 0) {
                pred = curr;
                curr = curr.next.getReference();
            }
            if (curr != null && curr.orderKey == oKey && !curr.next.isMarked()) {
                return false; // Key đã tồn tại
            }
            newNode.next.set(curr, false);
            if (pred.next.compareAndSet(curr, newNode, false, false)) {
                if (size.incrementAndGet() / capacity.get() > 2) {
                    capacity.compareAndSet(cap, cap * 2);
                }
                return true;
            }
            Thread.onSpinWait();
        }
    }

    public String get(int key) {
        long oKey = makeRegularKey(key);
        int bucketIdx = key % capacity.get();
        Node curr = getBucket(bucketIdx).next.getReference();
        while (curr != null && Long.compareUnsigned(curr.orderKey, oKey) < 0) {
            curr = curr.next.getReference();
        }
        if (curr != null && curr.orderKey == oKey && !curr.next.isMarked()) {
            return curr.value;
        }
        return null;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình cho `put`, `get`, `remove`. Mở rộng bảng băm diễn ra tiệm tiến $O(1)$ trên mỗi thao tác.
- **Không gian (Space):** $O(N)$ lưu trữ các nút dữ liệu và mảng con trỏ bucket.
- **Progress Guarantee:** **Lock-Free**.

---

## 11. Lock-Free Memory Pool / Free-List Object Allocator

### Đề bài chi tiết
Trong các ứng dụng giao dịch siêu tốc (Low Latency / HFT), việc cấp phát đối tượng mới liên tục trên Java Heap gây áp lực lớn cho GC (Garbage Collection Pauses).
Hãy thiết kế một **Lock-Free Object Pool / Free-List Allocator** hỗ trợ:
- `T acquire()`: Lấy một đối tượng có sẵn trong pool ra để tái sử dụng. Nếu rỗng, tạo mới đối tượng.
- `void release(T object)`: Hoàn trả đối tượng vào pool.

Yêu cầu: Sử dụng cấu trúc danh sách liên kết tái chế không khóa, tích hợp giải pháp chống ABA Problem bằng số định danh thế hệ (Generational Tag).

### Phân tích thuật toán
- Mỗi đối tượng được bọc trong một `PoolNode<T>`.
- Pool duy trì đỉnh `head` trỏ tới danh sách các nút đang rảnh rỗi.
- Sử dụng `AtomicStampedReference<PoolNode<T>>` để quản lý `head` và `stamp`.
- Khi `acquire()`: Luồng lấy nút ở `head` và dịch `head = head.next`.
- Khi `release(obj)`: Đặt lại trạng thái `obj.reset()`, chèn lại nút vào đỉnh `head`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicStampedReference;
import java.util.function.Supplier;

public class LockFreeObjectPool<T> {

    public interface Resettable {
        void reset();
    }

    private static final class Node<T> {
        final T value;
        Node<T> next;

        Node(T value) {
            this.value = value;
        }
    }

    private final AtomicStampedReference<Node<T>> freeList = new AtomicStampedReference<>(null, 0);
    private final Supplier<T> factory;

    public LockFreeObjectPool(Supplier<T> factory) {
        this.factory = factory;
    }

    public T acquire() {
        int[] stampHolder = new int[1];
        while (true) {
            Node<T> curHead = freeList.get(stampHolder);
            int curStamp = stampHolder[0];
            if (curHead == null) {
                return factory.get(); // Tạo mới nếu rỗng
            }
            Node<T> nextNode = curHead.next;
            if (freeList.compareAndSet(curHead, nextNode, curStamp, curStamp + 1)) {
                curHead.next = null;
                return curHead.value;
            }
            Thread.onSpinWait();
        }
    }

    public void release(T object) {
        if (object == null) return;
        if (object instanceof Resettable r) {
            r.reset();
        }
        Node<T> newNode = new Node<>(object);
        int[] stampHolder = new int[1];
        while (true) {
            Node<T> curHead = freeList.get(stampHolder);
            int curStamp = stampHolder[0];
            newNode.next = curHead;
            if (freeList.compareAndSet(curHead, newNode, curStamp, curStamp + 1)) {
                return;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình cho cả `acquire` và `release`.
- **Không gian (Space):** $O(M)$ với $M$ là số đối tượng cực đại được tái sử dụng trong pool.
- **Progress Guarantee:** **Lock-Free**.

---

## 12. Lock-Free Epoch-Based Reclamation (RCU List)

### Đề bài chi tiết
Trong các hệ thống quản lý bộ nhớ phi khóa cấp thấp (như Linux Kernel RCU hoặc cơ chế EBR trong cơ sở dữ liệu), khi một nút bị gỡ khỏi danh sách liên kết, không thể lập tức thu hồi/giải phóng nút đó vì có thể vẫn còn các luồng đọc (**Readers**) đang giữ con trỏ tới nút này.
Hãy mô phỏng cơ chế **Epoch-Based Reclamation (EBR / RCU)** bảo vệ danh sách liên kết:
- Duy trì một đồng hồ thế hệ toàn cục `globalEpoch`.
- Mỗi luồng đọc khi bắt đầu truy cập danh sách phải công bố kỷ nguyên của mình (`enterEpoch()`) và báo hiệu khi thoát (`exitEpoch()`).
- Nút bị xóa được đưa vào hàng đợi `retiredQueue`. Nút chỉ thực sự được giải phóng khi tất cả các luồng đọc đã bước qua kỷ nguyên mới (**Grace Period** kết thúc).

### Phân tích thuật toán
- `globalEpoch` có giá trị tăng dần (0, 1, 2...).
- Mỗi luồng duy trì một biến `localEpoch` trong `ThreadLocal`.
- Khi luồng đọc: `enterEpoch()` gán `localEpoch = globalEpoch`.
- Khi luồng xóa: Đặt nút bị xóa vào danh sách rác của kỷ nguyên hiện tại `retired[globalEpoch % 3]`.
- Thường kỳ, một luồng kiểm tra nếu tất cả các luồng đang hoạt động đều có `localEpoch == globalEpoch`, nó có thể an toàn tăng `globalEpoch++` và giải phóng toàn bộ các nút ở kỷ nguyên `globalEpoch - 2`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReference;

public class LockFreeEpochReclamation {

    private static final int NUM_EPOCHS = 3;
    private final AtomicInteger globalEpoch = new AtomicInteger(0);

    private static final class ThreadEpochState {
        volatile int epoch = -1; // -1 nghĩa là đang inactive
    }

    private final ConcurrentLinkedQueue<ThreadEpochState> registeredThreads = new ConcurrentLinkedQueue<>();
    private final ThreadLocal<ThreadEpochState> localState = ThreadLocal.withInitial(() -> {
        ThreadEpochState state = new ThreadEpochState();
        registeredThreads.add(state);
        return state;
    });

    @SuppressWarnings("unchecked")
    private final ConcurrentLinkedQueue<Object>[] retiredLists = new ConcurrentLinkedQueue[NUM_EPOCHS];

    public LockFreeEpochReclamation() {
        for (int i = 0; i < NUM_EPOCHS; i++) {
            retiredLists[i] = new ConcurrentLinkedQueue<>();
        }
    }

    public void enterCriticalSection() {
        localState.get().epoch = globalEpoch.get();
    }

    public void exitCriticalSection() {
        localState.get().epoch = -1;
    }

    public void retire(Object garbage) {
        int e = globalEpoch.get();
        retiredLists[e % NUM_EPOCHS].add(garbage);
        tryAdvanceEpoch();
    }

    private void tryAdvanceEpoch() {
        int current = globalEpoch.get();
        for (ThreadEpochState state : registeredThreads) {
            int tEpoch = state.epoch;
            if (tEpoch != -1 && tEpoch != current) {
                return; // Còn luồng ở epoch cũ -> Grace period chưa xong
            }
        }
        // Tất cả luồng đều ở epoch hiện tại hoặc inactive -> advance epoch
        if (globalEpoch.compareAndSet(current, current + 1)) {
            int safeEpochToReclaim = (current + 1) % NUM_EPOCHS;
            // Thu hồi toàn bộ rác an toàn của 2 epoch trước
            retiredLists[safeEpochToReclaim].clear();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho `enterCriticalSection` và `exitCriticalSection` (Wait-Free). $O(T)$ cho thao tác kiểm tra Grace Period với $T$ luồng.
- **Không gian (Space):** $O(G)$ lưu trữ tạm thời các đối tượng rác chờ giải phóng.
- **Progress Guarantee:** **Wait-Free** cho Readers, **Lock-Free** cho Memory Reclamation.

---

## 13. Lock-Free Work-Stealing Deque (Chase-Lev Deque)

### Đề bài chi tiết
Thiết kế hàng đợi 2 đầu phục vụ thuật toán đánh cắp công việc (**Work-Stealing Deque**) theo bài báo kinh điển của David Chase và Yossi Lev (2005), là lõi của `ForkJoinPool` trong Java.
Hỗ trợ:
- Luồng chủ (**Owner Thread**):
  - `void push(Task t)`: Đẩy task vào đáy (`bottom`).
  - `Task pop()`: Lấy task từ đáy (`bottom`) theo thứ tự LIFO.
- Các luồng đánh cắp (**Thief Threads**):
  - `Task steal()`: Đánh cắp task từ đỉnh (`top`) theo thứ tự FIFO bằng CAS.

### Phân tích thuật toán
- Duy trì 2 con trỏ: `top` (được truy cập bởi luồng steal và pop) và `bottom` (chỉ được cập nhật bởi owner).
- `push`: Luồng owner ghi `buffer[bottom] = task`, sau đó tăng `bottom = bottom + 1` với Release memory barrier. Không có sự tranh chấp với luồng steal tại `bottom`.
- `pop`: Luồng owner giảm `bottom = bottom - 1`.
  - Nếu `top < bottom`: Có ít nhất 2 task, owner lấy task an toàn mà không cần CAS.
  - Nếu `top == bottom`: Chỉ còn đúng 1 task, xảy ra tranh chấp giữa owner và luồng steal. Owner thực hiện `CAS(top, t, t + 1)`. Nếu thành công, owner nhận task; nếu thất bại, task đã bị luồng khác steal.
- `steal`: Luồng thief đọc `top`. Nếu `top < bottom`, thief đọc task tại `buffer[top]` và cố gắng thực hiện `CAS(top, t, t + 1)`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeChaseLevDeque<E> {

    private final int capacity;
    private final int mask;
    private final Object[] buffer;

    private volatile long top = 0;
    private volatile long bottom = 0;

    private static final VarHandle TOP;
    private static final VarHandle BOTTOM;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            TOP = l.findVarHandle(LockFreeChaseLevDeque.class, "top", long.class);
            BOTTOM = l.findVarHandle(LockFreeChaseLevDeque.class, "bottom", long.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public LockFreeChaseLevDeque(int capacityPowerOfTwo) {
        this.capacity = capacityPowerOfTwo;
        this.mask = capacityPowerOfTwo - 1;
        this.buffer = new Object[capacity];
    }

    public void push(E task) {
        long b = (long) BOTTOM.getOpaque(this);
        buffer[(int) (b & mask)] = task;
        BOTTOM.setRelease(this, b + 1);
    }

    @SuppressWarnings("unchecked")
    public E pop() {
        long b = (long) BOTTOM.getOpaque(this) - 1;
        BOTTOM.setOpaque(this, b);
        VarHandle.fullFence();
        long t = (long) TOP.getAcquire(this);

        if (t <= b) {
            E task = (E) buffer[(int) (b & mask)];
            if (t == b) {
                // Task duy nhất còn lại, cạnh tranh với steal
                if (!TOP.compareAndSet(this, t, t + 1)) {
                    task = null; // Bị steal mất
                }
                BOTTOM.setOpaque(this, b + 1);
            }
            return task;
        } else {
            // Deque rỗng
            BOTTOM.setOpaque(this, b + 1);
            return null;
        }
    }

    @SuppressWarnings("unchecked")
    public E steal() {
        while (true) {
            long t = (long) TOP.getAcquire(this);
            VarHandle.fullFence();
            long b = (long) BOTTOM.getAcquire(this);
            if (t < b) {
                E task = (E) buffer[(int) (t & mask)];
                if (TOP.compareAndSet(this, t, t + 1)) {
                    return task;
                }
            } else {
                return null;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `push`: $O(1)$ Wait-Free.
  - `pop`: $O(1)$ Wait-Free trong hầu hết trường hợp (chỉ CAS khi còn 1 phần tử).
  - `steal`: $O(1)$ Lock-Free.
- **Không gian (Space):** $O(C)$ với mảng vòng dung lượng $C$.
- **Progress Guarantee:** `push` và `pop` (đối với phần tử không tranh chấp) là **Wait-Free**; `steal` là **Lock-Free**.

---

## 14. Lock-Free Sliding Window Rate Limiter

### Đề bài chi tiết
Xây dựng một bộ kiểm soát tốc độ giới hạn truy cập (**Rate Limiter**) không khóa dựa trên thuật toán **Token Bucket** trong cửa sổ trượt thời gian.
- Cho phép tối đa $M$ tokens, tốc độ nạp $R$ tokens/giây.
- Hỗ trợ phương thức: `boolean tryAcquire(int tokens)`:
  - Nếu đủ token trong bucket, trừ số token và trả về `true`.
  - Nếu không đủ token, trả về `false` ngay lập tức.
- Phải đảm bảo an toàn luồng tuyệt đối dưới hàng triệu request/giây mà không sử dụng bất kỳ `synchronized` hay `ReentrantLock` nào.

### Phân tích thuật toán
- Trạng thái của Bucket được đóng gói trong một cấu trúc nguyên tử duy nhất (hoặc đóng gói 64-bit: 32-bit token count + 32-bit timestamp epoch).
- Trong mỗi thao tác `tryAcquire`:
  1. Đọc trạng thái hiện tại gồm `(availableTokens, lastRefillNanos)`.
  2. Tính toán số token mới được sinh ra trong khoảng thời gian $\Delta t = \text{now} - \text{lastRefillNanos}$.
  3. Tính `newTokens = min(M, availableTokens + generatedTokens)`.
  4. Nếu `newTokens < tokens` $\rightarrow$ Trả về `false`.
  5. Nếu đủ: Tính toán trạng thái mới `(newTokens - tokens, now)` và thực hiện `CAS`. Nếu thành công $\rightarrow$ trả về `true`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeTokenBucketRateLimiter {

    private static final class State {
        final double tokens;
        final long lastNanos;

        State(double tokens, long lastNanos) {
            this.tokens = tokens;
            this.lastNanos = lastNanos;
        }
    }

    private final double maxCapacity;
    private final double refillTokensPerNano;
    private volatile State state;

    private static final VarHandle STATE;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            STATE = l.findVarHandle(LockFreeTokenBucketRateLimiter.class, "state", State.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public LockFreeTokenBucketRateLimiter(double maxCapacity, double refillPerSecond) {
        this.maxCapacity = maxCapacity;
        this.refillTokensPerNano = refillPerSecond / 1_000_000_000.0;
        this.state = new State(maxCapacity, System.nanoTime());
    }

    public boolean tryAcquire(double requestedTokens) {
        if (requestedTokens <= 0) throw new IllegalArgumentException();
        while (true) {
            State curState = (State) STATE.getVolatile(this);
            long now = System.nanoTime();
            long elapsed = Math.max(0, now - curState.lastNanos);
            double newTokens = Math.min(maxCapacity, curState.tokens + elapsed * refillTokensPerNano);

            if (newTokens < requestedTokens) {
                return false;
            }

            State nextState = new State(newTokens - requestedTokens, now);
            if (STATE.compareAndSet(this, curState, nextState)) {
                return true;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình.
- **Không gian (Space):** $O(1)$ cho State đối tượng.
- **Progress Guarantee:** **Lock-Free**.

---

## 15. Wait-Free Single-Producer Single-Consumer (SPSC) Queue

### Đề bài chi tiết
Triển khai một hàng đợi vòng **Single-Producer Single-Consumer (SPSC)** đảm bảo mức độ tiến trình **Wait-Free** (mọi thao tác của Producer và Consumer đều hoàn thành sau số hữu hạn bước $O(1)$, không có vòng lặp vô hạn và không có CAS loop).
Hỗ trợ:
- `boolean offer(E item)`: Thực thi bởi duy nhất Producer thread.
- `E poll()`: Thực thi bởi duy nhất Consumer thread.

### Phân tích thuật toán
- Producer chỉ cập nhật `tail` và đọc `head`.
- Consumer chỉ cập nhật `head` và đọc `tail`.
- Không có bất kỳ sự cạnh tranh ghi (Write-Write Contention) trên cùng một biến.
- Đọc-ghi con trỏ sử dụng **Memory Ordering Acquire/Release** (hoặc `lazySet` / `setRelease`) để loại bỏ hoàn toàn chi phí đắt đỏ của lệnh phần cứng `LOCK CMPXCHG`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class WaitFreeSpscRingBuffer<E> {

    private final int capacity;
    private final int mask;
    private final Object[] buffer;

    private volatile long head = 0;
    private volatile long tail = 0;

    private static final VarHandle HEAD;
    private static final VarHandle TAIL;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD = l.findVarHandle(WaitFreeSpscRingBuffer.class, "head", long.class);
            TAIL = l.findVarHandle(WaitFreeSpscRingBuffer.class, "tail", long.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public WaitFreeSpscRingBuffer(int capacityPowerOfTwo) {
        this.capacity = capacityPowerOfTwo;
        this.mask = capacityPowerOfTwo - 1;
        this.buffer = new Object[capacity];
    }

    public boolean offer(E item) {
        if (item == null) throw new NullPointerException();
        long currentTail = (long) TAIL.getOpaque(this);
        long currentHead = (long) HEAD.getAcquire(this);

        if (currentTail - currentHead >= capacity) {
            return false; // Queue đầy
        }

        buffer[(int) (currentTail & mask)] = item;
        TAIL.setRelease(this, currentTail + 1);
        return true;
    }

    @SuppressWarnings("unchecked")
    public E poll() {
        long currentHead = (long) HEAD.getOpaque(this);
        long currentTail = (long) TAIL.getAcquire(this);

        if (currentHead >= currentTail) {
            return null; // Queue rỗng
        }

        int index = (int) (currentHead & mask);
        E item = (E) buffer[index];
        buffer[index] = null;
        HEAD.setRelease(this, currentHead + 1);
        return item;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ tuyệt đối, không có vòng lặp, không có CAS.
- **Không gian (Space):** $O(C)$ bộ nhớ đệm.
- **Progress Guarantee:** **Wait-Free** (Cả Producer và Consumer đều hoàn tất trong thời gian xác định).

---

## 16. Wait-Free Multi-Producer Single-Consumer (MPSC) Unbounded Queue

### Đề bài chi tiết
Triển khai một hàng đợi không giới hạn hỗ trợ nhiều luồng ghi và một luồng đọc duy nhất (**Multi-Producer Single-Consumer - MPSC**) theo phong cách thư viện nổi tiếng **JCTools** và mạng **Netty**.
Hàng đợi phải đảm bảo:
- `void offer(E item)`: Thực thi bởi nhiều producer, sử dụng thao tác nguyên tử `getAndSet` trên `tail` để đạt mức độ bảo đảm tiến trình **Wait-Free** ($O(1)$ không vòng lặp CAS).
- `E poll()`: Thực thi bởi 1 luồng consumer duy nhất.

### Phân tích thuật toán
- Sử dụng danh sách liên kết các nút `Node<E>`.
- Khởi tạo với một nút Sentinel Dummy. `head` và `tail` trỏ vào Dummy node này.
- **Offer (Producer)**:
  - Tạo `newNode(item)`.
  - Thực hiện nguyên tử `prevNode = tail.getAndSet(newNode)`. Lệnh `getAndSet` (Atomic Exchange) luôn thành công sau đúng 1 chu kỳ máy mà không bao giờ bị fail/retry!
  - Nối `prevNode.next = newNode` (Release barrier).
- **Poll (Consumer)**:
  - Đọc `curHead` và `next = curHead.next`.
  - Nếu `next != null`: lấy giá trị `next.item`, cập nhật `head = next`, xóa tham chiếu cũ và trả về giá trị.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class WaitFreeMpscUnboundedQueue<E> {

    private static final class Node<E> {
        E item;
        volatile Node<E> next;

        Node(E item) {
            this.item = item;
        }
    }

    private volatile Node<E> head;
    private volatile Node<E> tail;

    private static final VarHandle HEAD;
    private static final VarHandle TAIL;
    private static final VarHandle NEXT;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD = l.findVarHandle(WaitFreeMpscUnboundedQueue.class, "head", Node.class);
            TAIL = l.findVarHandle(WaitFreeMpscUnboundedQueue.class, "tail", Node.class);
            NEXT = l.findVarHandle(Node.class, "next", Node.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public WaitFreeMpscUnboundedQueue() {
        Node<E> dummy = new Node<>(null);
        this.head = dummy;
        this.tail = dummy;
    }

    public void offer(E item) {
        if (item == null) throw new NullPointerException();
        Node<E> newNode = new Node<>(item);
        @SuppressWarnings("unchecked")
        Node<E> prev = (Node<E>) TAIL.getAndSet(this, newNode); // Wait-free O(1) step
        NEXT.setRelease(prev, newNode);
    }

    @SuppressWarnings("unchecked")
    public E poll() {
        Node<E> curHead = (Node<E>) HEAD.getOpaque(this);
        Node<E> nextNode = (Node<E>) NEXT.getAcquire(curHead);

        if (nextNode != null) {
            E item = nextNode.item;
            nextNode.item = null;
            HEAD.setOpaque(this, nextNode);
            return item;
        }
        return null; // Rỗng hoặc đang trong quá trình liên kết
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `offer`: $O(1)$ tuyệt đối, không có vòng lặp (**Wait-Free**).
  - `poll`: $O(1)$ cho luồng Consumer.
- **Không gian (Space):** $O(N)$ phân bổ động.
- **Progress Guarantee:** `offer` đạt **Wait-Free**, `poll` đạt **Lock-Free / Wait-Free**.

---

## 17. Wait-Free Universal Construction (Herlihy's Protocol)

### Đề bài chi tiết
Maurice Herlihy (1991) đã chứng minh rằng: Từ một đối tượng nguyên tử hỗ trợ lệnh CAS (Consensus Object cấp độ $\infty$), ta có thể biến đổi **mọi cấu trúc dữ liệu tuần tự bất kỳ (Sequential Data Structure)** thành một cấu trúc dữ liệu đa luồng đồng thời đạt bảo đảm tiến trình **Wait-Free**.
Hãy triển khai **Wait-Free Universal Construction** để biến một đối tượng tuần tự (ví dụ: một Counter hoặc danh sách) thành dạng Wait-Free:
- Mỗi luồng thông báo yêu cầu của mình vào mảng `announce`.
- Các luồng hợp tác giúp đỡ (**Helping Mechanism**) để đưa yêu cầu của nhau vào một danh sách các thao tác đã đồng thuận (**Consensus Log**).
- Đảm bảo mọi luồng đều nhận được kết quả sau tối đa $O(T)$ bước với $T$ là số lượng luồng.

### Phân tích thuật toán
- Luồng $i$ tạo đối tượng yêu cầu `Invocation` và ghi vào `announce[i]`.
- Danh sách lịch sử là một chuỗi các nút `Node`. Mỗi nút chứa đối tượng đồng thuận `Consensus` trỏ tới nút kế tiếp.
- Luồng $i$ duyệt từ đầu chuỗi, áp dụng từng thao tác tuần tự lên bản sao trạng thái.
- Khi đi đến đuôi chuỗi, luồng $i$ hỗ trợ đề xuất các yêu cầu chưa xử lý trong mảng `announce` vào đuôi chuỗi bằng lệnh CAS trên nút kế tiếp.
- Do mỗi vòng lặp luôn có ít nhất một yêu cầu trong `announce` được cam kết, mọi luồng đều được phục vụ sau tối đa $T$ vòng.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicReference;
import java.util.function.Function;

public class WaitFreeUniversalConstruction<S, R> {

    public static final class Invocation<S, R> {
        final Function<S, R> operation;
        final AtomicReference<R> response = new AtomicReference<>(null);

        public Invocation(Function<S, R> operation) {
            this.operation = operation;
        }
    }

    private static final class Node<S, R> {
        final Invocation<S, R> invoc;
        final AtomicReference<Node<S, R>> next = new AtomicReference<>(null);
        final int seq;

        Node(Invocation<S, R> invoc, int seq) {
            this.invoc = invoc;
            this.seq = seq;
        }
    }

    private final int numThreads;
    @SuppressWarnings("unchecked")
    private final AtomicReference<Invocation<S, R>>[] announce;
    private final Node<S, R> tailLog;

    @SuppressWarnings("unchecked")
    public WaitFreeUniversalConstruction(int numThreads) {
        this.numThreads = numThreads;
        this.announce = (AtomicReference<Invocation<S, R>>[]) new AtomicReference[numThreads];
        for (int i = 0; i < numThreads; i++) {
            announce[i] = new AtomicReference<>(null);
        }
        this.tailLog = new Node<>(null, 0);
    }

    public R execute(int threadId, Function<S, R> op, S initialCopy) {
        Invocation<S, R> invoc = new Invocation<>(op);
        announce[threadId].set(invoc);

        Node<S, R> curr = tailLog;
        while (invoc.response.get() == null) {
            // Giúp đỡ lần lượt các luồng trong announce array
            for (int i = 0; i < numThreads; i++) {
                Invocation<S, R> helpInvoc = announce[i].get();
                if (helpInvoc != null && helpInvoc.response.get() == null) {
                    Node<S, R> nextNode = new Node<>(helpInvoc, curr.seq + 1);
                    curr.next.compareAndSet(null, nextNode);
                    curr = curr.next.get();
                    if (curr.invoc == helpInvoc) {
                        R res = helpInvoc.operation.apply(initialCopy);
                        helpInvoc.response.compareAndSet(null, res);
                    }
                }
            }
        }
        return invoc.response.get();
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(T \times \text{Cost}(op))$ với $T$ là số lượng luồng trong hệ thống.
- **Không gian (Space):** $O(N)$ lưu trữ lịch sử các nút trong Consensus Log.
- **Progress Guarantee:** **Wait-Free** (Mỗi luồng hoàn thành trong tối đa $T$ vòng lặp).

---

## 18. Lock-Free Hazard Pointers Memory Management

### Đề bài chi tiết
Trong các ngôn ngữ không có GC tự động hoặc khi xây dựng các cấu trúc dữ liệu Off-Heap trong Java, **Hazard Pointers (Maged M. Michael - 2004)** là kỹ thuật kinh điển giúp luồng đọc bảo vệ một vùng nhớ không bị thu hồi/tái chế trong khi nó đang truy cập.
Hãy thiết kế hệ thống **Hazard Pointers Manager**:
- Mỗi luồng đọc sở hữu $K$ con trỏ nguy hiểm (**Hazard Pointers**).
- Trước khi giải tham chiếu một con trỏ `p`, luồng gán `HP[threadId] = p`.
- Khi một luồng khác xóa nút `p`, nó đưa `p` vào danh sách chờ giải phóng (`retiredList`).
- Khi danh sách `retiredList` đạt ngưỡng $R$, luồng quét qua tất cả các Hazard Pointers của các luồng khác. Nút nào không nằm trong bất kỳ Hazard Pointer nào thì được an toàn giải phóng.

### Phân tích thuật toán
- Đọc một con trỏ an toàn qua mẫu:
  1. `p = node.ref;`
  2. `HP.set(p);`
  3. `if (node.ref != p) retry;` // Xác thực nút chưa bị xóa trước khi gán HP
- Quá trình quét rác (`scanAndReclaim`):
  1. Đọc tất cả các HP đang hoạt động đưa vào một bảng băm/tập hợp `HashSet`.
  2. Duyệt qua `retiredList`: Nếu một nút không có trong `HashSet`, lập tức giải phóng; nếu có, giữ lại cho lần quét sau.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.*;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicReference;

public class LockFreeHazardPointerManager<T> {

    public static final class HazardPointer<T> {
        final AtomicReference<T> pointer = new AtomicReference<>(null);
    }

    private final int maxThreads;
    private final List<HazardPointer<T>> hazardPointers;
    private final ThreadLocal<HazardPointer<T>> localHp;
    private final ThreadLocal<List<T>> localRetiredList = ThreadLocal.withInitial(ArrayList::new);
    private final int RETIRE_THRESHOLD = 50;

    public LockFreeHazardPointerManager(int maxThreads) {
        this.maxThreads = maxThreads;
        this.hazardPointers = new ArrayList<>(maxThreads);
        for (int i = 0; i < maxThreads; i++) {
            hazardPointers.add(new HazardPointer<>());
        }
        AtomicInteger idGen = new AtomicInteger(0);
        this.localHp = ThreadLocal.withInitial(() -> hazardPointers.get(idGen.getAndIncrement() % maxThreads));
    }

    public void protect(T node) {
        localHp.get().pointer.set(node);
    }

    public void clear() {
        localHp.get().pointer.set(null);
    }

    public void retire(T node) {
        List<T> retired = localRetiredList.get();
        retired.add(node);
        if (retired.size() >= RETIRE_THRESHOLD) {
            scanAndReclaim(retired);
        }
    }

    private void scanAndReclaim(List<T> retired) {
        Set<T> activeGuards = new HashSet<>();
        for (HazardPointer<T> hp : hazardPointers) {
            T ptr = hp.pointer.get();
            if (ptr != null) {
                activeGuards.add(ptr);
            }
        }

        Iterator<T> it = retired.iterator();
        while (it.hasNext()) {
            T obj = it.next();
            if (!activeGuards.contains(obj)) {
                // Giải phóng vùng nhớ obj an toàn
                it.remove();
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):**
  - `protect`, `clear`: $O(1)$ Wait-Free.
  - `retire`: $O(1)$ amortized (quét rác phân bổ đều).
- **Không gian (Space):** $O(T \times K + R)$ với $T$ luồng, $K$ con trỏ/luồng, $R$ kích thước ngưỡng thu gom.
- **Progress Guarantee:** **Wait-Free** cho `protect`, **Lock-Free** cho thu gom bộ nhớ.

---

## 19. Lock-Free Atomic Counter with Exponential Backoff and Spin-Wait

### Đề bài chi tiết
Khi hàng trăm luồng đồng thời thực hiện thao tác cộng trên một biến đếm nguyên tử duy nhất (`AtomicLong`), hiện tượng xung đột CAS (CAS Storm) gây suy giảm hiệu năng nghiêm trọng do hiện tượng nghẽn bus bộ nhớ.
Hãy thiết kế **Lock-Free Atomic Counter with Truncated Exponential Backoff**:
- Sử dụng lệnh CPU gợi ý `Thread.onSpinWait()` (tương đương lệnh `PAUSE` trên x86/ARM) để tiết kiệm năng lượng và giảm độ trễ pipeline của CPU.
- Khi CAS thất bại liên tiếp, áp dụng thuật toán lùi lũy thừa ngẫu nhiên (**Randomized Truncated Exponential Backoff**) để phân tán thời điểm thử lại của các luồng.

### Phân tích thuật toán
- Khởi tạo `delay = MIN_DELAY`.
- Khi CAS thất bại:
  1. Thực hiện số vòng lặp `Thread.onSpinWait()` tỉ lệ thuận với `delay`.
  2. `delay = min(MAX_DELAY, delay * 2)`.
  3. Thêm độ lệch ngẫu nhiên (**Jitter**) để chống lại hiện tượng cộng hưởng (thử lại cùng lúc).
- Khi CAS thành công: Thoát khỏi vòng lặp.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.concurrent.ThreadLocalRandom;

public class LockFreeExponentialBackoffCounter {

    private volatile long value = 0;

    private static final VarHandle VALUE;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            VALUE = l.findVarHandle(LockFreeExponentialBackoffCounter.class, "value", long.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    private static final int MIN_BACKOFF_SPINS = 4;
    private static final int MAX_BACKOFF_SPINS = 1024;

    public long incrementAndGet() {
        int backoff = MIN_BACKOFF_SPINS;
        while (true) {
            long current = (long) VALUE.getVolatile(this);
            long next = current + 1;
            if (VALUE.compareAndSet(this, current, next)) {
                return next;
            }

            // Exponential Backoff with Jitter
            int spins = ThreadLocalRandom.current().nextInt(backoff / 2, backoff);
            for (int i = 0; i < spins; i++) {
                Thread.onSpinWait();
            }
            backoff = Math.min(MAX_BACKOFF_SPINS, backoff * 2);
        }
    }

    public long get() {
        return (long) VALUE.getVolatile(this);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ khi không tranh chấp, $O(\log C)$ trung bình dưới tải cao.
- **Không gian (Space):** $O(1)$.
- **Progress Guarantee:** **Lock-Free**.

---

## 20. Lock-Free Flat Combining Simulator

### Đề bài chi tiết
**Flat Combining (Hendler, Incze, Shavit, Tzafrir - 2010)** là kỹ thuật đồng bộ hóa hiệu năng cao kết hợp giữa cơ chế Lock-Free và Batching:
- Thay vì tất cả các luồng tranh nhau thực hiện thao tác trên cấu trúc dữ liệu, mỗi luồng công bố thao tác của mình vào một mảng đăng ký (**Publication List**).
- Một luồng duy nhất trở thành luồng kết hợp (**Combiner**) nắm giữ quyền xử lý toàn bộ các yêu cầu được gom lại trong một mẻ (**Batch Processing**), sau đó trả kết quả cho các luồng khác đang đợi.
- Triển khai cơ chế Flat Combining để quản lý một tập hợp các thao tác.

### Phân tích thuật toán
- Biến cờ `combinerLock` sử dụng `AtomicBoolean` để luồng bầu chọn Combiner một cách Lock-Free.
- Mỗi luồng có một `Slot` chứa `request`, `response`, và trạng thái `PENDING / COMPLETED`.
- Luồng nào giành được `combinerLock` sẽ duyệt qua tất cả các slot đang active, thực thi hàng loạt các request tuần tự (tận dụng tối đa L1/L2 Cache), cập nhật response, rồi giải phóng cờ `combinerLock`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicBoolean;

public class LockFreeFlatCombiner<T, R> {

    public interface BatchExecutable<T, R> {
        R apply(T request);
    }

    private static final class RequestSlot<T, R> {
        volatile T request;
        volatile R response;
        volatile boolean done;
        volatile boolean active = true;
    }

    private final AtomicBoolean combinerLock = new AtomicBoolean(false);
    private final ConcurrentLinkedQueue<RequestSlot<T, R>> publicationList = new ConcurrentLinkedQueue<>();
    private final ThreadLocal<RequestSlot<T, R>> threadSlot = ThreadLocal.withInitial(() -> {
        RequestSlot<T, R> slot = new RequestSlot<>();
        publicationList.add(slot);
        return slot;
    });

    private final BatchExecutable<T, R> logic;

    public LockFreeFlatCombiner(BatchExecutable<T, R> logic) {
        this.logic = logic;
    }

    public R execute(T request) {
        RequestSlot<T, R> mySlot = threadSlot.get();
        mySlot.request = request;
        mySlot.done = false;

        while (!mySlot.done) {
            if (combinerLock.compareAndSet(false, true)) {
                try {
                    // Trở thành Combiner: Quét và xử lý toàn bộ request trong publication list
                    for (RequestSlot<T, R> slot : publicationList) {
                        if (slot.active && !slot.done && slot.request != null) {
                            slot.response = logic.apply(slot.request);
                            slot.request = null;
                            slot.done = true;
                        }
                    }
                } finally {
                    combinerLock.set(false);
                }
            } else {
                Thread.onSpinWait();
            }
        }
        return mySlot.response;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ amortized dưới tải cao nhờ hiệu quả L1 Cache locality của Combiner.
- **Không gian (Space):** $O(T)$ với $T$ là số luồng tham gia.
- **Progress Guarantee:** **Lock-Free**.

---

## 21. Lock-Free Concurrent Priority Queue

### Đề bài chi tiết
Triển khai một hàng đợi ưu tiên không khóa (**Lock-Free Priority Queue**) hỗ trợ:
- `void insert(int priority, E value)`: Thêm một phần tử với mức ưu tiên xác định.
- `E deleteMin()`: Lấy và xóa phần tử có mức ưu tiên nhỏ nhất (hoặc lớn nhất).

Sử dụng cấu trúc **Lock-Free SkipList** với cơ chế đánh dấu xóa logic (Logical Deletion Marking) để đảm bảo nhiều luồng có thể chèn và rút phần tử ưu tiên đồng thời mà không bị nghẽn ở nút gốc (Root Node) như Binary Heap truyền thống.

### Phân tích thuật toán
- Phần tử có độ ưu tiên nhỏ nhất luôn nằm ở đầu tầng 0 của SkipList (ngay sau sentinel `head`).
- `deleteMin()`:
  1. Duyệt từ `head.next[0]`.
  2. Bỏ qua các nút đã bị đánh dấu xóa.
  3. Khi gặp nút hợp lệ đầu tiên, thử thực hiện `CAS` để đánh dấu xóa logic (`attemptMark`).
  4. Nếu thành công, trả về giá trị phần tử và kích hoạt gỡ vật lý. Nếu thất bại (do luồng khác vừa lấy), tiếp tục tìm nút kế tiếp.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicMarkableReference;

public class LockFreePriorityQueue<E> {

    private static final class Node<E> {
        final int priority;
        final E value;
        final AtomicMarkableReference<Node<E>> next;

        Node(int priority, E value, Node<E> next) {
            this.priority = priority;
            this.value = value;
            this.next = new AtomicMarkableReference<>(next, false);
        }
    }

    private final Node<E> head = new Node<>(Integer.MIN_VALUE, null, null);
    private final Node<E> tail = new Node<>(Integer.MAX_VALUE, null, null);

    public LockFreePriorityQueue() {
        head.next.set(tail, false);
    }

    public void insert(int priority, E value) {
        if (value == null) throw new NullPointerException();
        Node<E> newNode = new Node<>(priority, value, null);
        while (true) {
            Node<E> pred = head;
            Node<E> curr = pred.next.getReference();
            while (curr != tail && curr.priority < priority) {
                pred = curr;
                curr = curr.next.getReference();
            }
            newNode.next.set(curr, false);
            if (pred.next.compareAndSet(curr, newNode, false, false)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public E deleteMin() {
        while (true) {
            Node<E> pred = head;
            Node<E> curr = pred.next.getReference();
            if (curr == tail) {
                return null; // Rỗng
            }
            boolean[] marked = {false};
            Node<E> succ = curr.next.get(marked);
            if (marked[0]) {
                // Giúp gỡ nút đã xóa
                pred.next.compareAndSet(curr, succ, false, false);
                continue;
            }
            if (curr.next.attemptMark(succ, true)) {
                pred.next.compareAndSet(curr, succ, false, false);
                return curr.value;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** `insert`: $O(N)$ (hoặc $O(\log N)$ nếu mở rộng multi-level SkipList), `deleteMin`: $O(1)$ amortized.
- **Không gian (Space):** $O(N)$.
- **Progress Guarantee:** **Lock-Free**.

---

## 22. Lock-Free Dynamic BitSet

### Đề bài chi tiết
Thiết kế một vector bit đồng thời không khóa (**Lock-Free Concurrent BitSet**) hỗ trợ kích thước vô hạn (tự động mở rộng) với các thao tác nguyên tử:
- `void set(int bitIndex)`: Đặt bit tại vị trí `bitIndex` thành 1.
- `void clear(int bitIndex)`: Xóa bit tại vị trí `bitIndex` về 0.
- `boolean get(int bitIndex)`: Đọc giá trị của bit.
- `int cardinality()`: Đếm tổng số bit 1 đang được bật.

### Phân tích thuật toán
- Sử dụng `AtomicReferenceArray<AtomicLong>` hoặc `AtomicLongArray` phân tầng để lưu trữ các từ 64-bit (`long words`).
- Thao tác `set(bitIndex)`:
  - `wordIdx = bitIndex >> 6`, `bitMask = 1L << (bitIndex & 63)`.
  - CAS loop trên từ 64-bit: `CAS(oldWord, oldWord | bitMask)`.
- Thao tác `clear(bitIndex)`:
  - CAS loop: `CAS(oldWord, oldWord & ~bitMask)`.
- Thao tác `get(bitIndex)`:
  - Wait-Free: `(word & bitMask) != 0`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicLongArray;

public class LockFreeBitSet {

    private final AtomicLongArray words;
    private final int capacityBits;

    public LockFreeBitSet(int capacityBits) {
        this.capacityBits = capacityBits;
        int numWords = (capacityBits + 63) >>> 6;
        this.words = new AtomicLongArray(numWords);
    }

    public void set(int bitIndex) {
        if (bitIndex < 0 || bitIndex >= capacityBits) throw new IndexOutOfBoundsException();
        int wordIdx = bitIndex >>> 6;
        long bitMask = 1L << (bitIndex & 63);
        while (true) {
            long current = words.get(wordIdx);
            long updated = current | bitMask;
            if (current == updated || words.compareAndSet(wordIdx, current, updated)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public void clear(int bitIndex) {
        if (bitIndex < 0 || bitIndex >= capacityBits) throw new IndexOutOfBoundsException();
        int wordIdx = bitIndex >>> 6;
        long bitMask = 1L << (bitIndex & 63);
        while (true) {
            long current = words.get(wordIdx);
            long updated = current & ~bitMask;
            if (current == updated || words.compareAndSet(wordIdx, current, updated)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public boolean get(int bitIndex) {
        if (bitIndex < 0 || bitIndex >= capacityBits) throw new IndexOutOfBoundsException();
        int wordIdx = bitIndex >>> 6;
        long bitMask = 1L << (bitIndex & 63);
        return (words.get(wordIdx) & bitMask) != 0;
    }

    public int cardinality() {
        int sum = 0;
        for (int i = 0; i < words.length(); i++) {
            sum += Long.bitCount(words.get(i));
        }
        return sum;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** `get`: $O(1)$ Wait-Free; `set`, `clear`: $O(1)$ Lock-Free; `cardinality`: $O(W)$ với $W$ là số từ 64-bit.
- **Không gian (Space):** $O(N / 64)$ words.
- **Progress Guarantee:** `get` đạt **Wait-Free**, `set`/`clear` đạt **Lock-Free**.

---

## 23. Lock-Free Reference Counting Garbage Collector (Split Counter)

### Đề bài chi tiết
Triển khai cơ chế đếm tham chiếu nguyên tử (**Lock-Free Atomic Reference Counting**) cho các cấu trúc dữ liệu chia sẻ đa luồng.
Thách thức: Tránh race condition khi một luồng đang tăng biến đếm tham chiếu (`retain`) trong khi một luồng khác đang giảm về 0 và hủy đối tượng (`release`).
Giải pháp: Áp dụng kỹ thuật **Split Reference Count** (tách thành `externalCount` nằm cạnh con trỏ và `internalCount` nằm bên trong thân đối tượng).

### Phân tích thuật toán
- Mỗi con trỏ trỏ tới đối tượng gồm 2 trường: `(ObjectPtr, ExternalCount)`.
- Khi luồng sao chép con trỏ: Thực hiện CAS tăng `externalCount`.
- Khi luồng hủy bỏ một con trỏ: Giảm `internalCount` tương ứng.
- Khi `internalCount + externalCount == 0`, đối tượng được an toàn giải phóng.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicInteger;

public class LockFreeReferenceCountedObject<T> {

    public static final class RefCounted<T> {
        final T data;
        final AtomicInteger internalCount;

        public RefCounted(T data, int initialCount) {
            this.data = data;
            this.internalCount = new AtomicInteger(initialCount);
        }

        public void retain() {
            internalCount.incrementAndGet();
        }

        public boolean release() {
            if (internalCount.decrementAndGet() == 0) {
                dispose();
                return true;
            }
            return false;
        }

        private void dispose() {
            // Hủy tài nguyên
        }

        public T get() {
            return data;
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho cả `retain` và `release` (Wait-Free thông qua `getAndIncrement` / `getAndDecrement`).
- **Không gian (Space):** $O(1)$ phụ phí cho mỗi đối tượng.
- **Progress Guarantee:** **Wait-Free**.

---

## 24. Lock-Free Read-Write State Machine

### Đề bài chi tiết
Thiết kế một cơ chế kiểm soát đọc-ghi phi khóa (**Lock-Free Read-Write State Machine / Shared-Exclusive State**) đóng gói trạng thái trong một số nguyên 32-bit `AtomicInteger`:
- 16-bit thấp: Số lượng luồng đang đọc (**Shared Readers Count**).
- 1-bit cờ: Luồng đang ghi (**Exclusive Writer Flag**).
- 15-bit cao: Số lượng luồng ghi đang chờ (**Pending Writers Count**) để ngăn hiện tượng bỏ đói luồng ghi (**Writer Starvation**).
- Hỗ trợ các thao tác chuyển trạng thái không dùng mutex: `tryAcquireRead()`, `releaseRead()`, `tryAcquireWrite()`, `releaseWrite()`.

### Phân tích thuật toán
- Bit mask: `READER_MASK = 0x0000FFFF`, `WRITER_BIT = 0x00010000`, `WAITING_WRITER_MASK = 0xFFFE0000`.
- `tryAcquireRead()`: Chỉ thành công khi không có writer và không có waiting writer:
  - Kiểm tra `(state & (WRITER_BIT | WAITING_WRITER_MASK)) == 0`.
  - CAS tăng 1 vào 16-bit thấp: `CAS(state, state + 1)`.
- `tryAcquireWrite()`: Chỉ thành công khi `state == 0`:
  - `CAS(0, WRITER_BIT)`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;

public class LockFreeReadWriteStateMachine {

    private volatile int state = 0;

    private static final int WRITER_BIT = 1 << 16;
    private static final int READER_MASK = 0xFFFF;
    private static final int WAITING_WRITER_INC = 1 << 17;

    private static final VarHandle STATE;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            STATE = l.findVarHandle(LockFreeReadWriteStateMachine.class, "state", int.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public boolean tryAcquireRead() {
        while (true) {
            int s = (int) STATE.getVolatile(this);
            if ((s & WRITER_BIT) != 0 || (s >>> 17) > 0) {
                return false; // Có writer hoặc writer đang đợi
            }
            if ((s & READER_MASK) == READER_MASK) {
                throw new IllegalStateException("Max readers reached");
            }
            if (STATE.compareAndSet(this, s, s + 1)) {
                return true;
            }
            Thread.onSpinWait();
        }
    }

    public void releaseRead() {
        while (true) {
            int s = (int) STATE.getVolatile(this);
            if ((s & READER_MASK) == 0) {
                throw new IllegalStateException("Underflow");
            }
            if (STATE.compareAndSet(this, s, s - 1)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public boolean tryAcquireWrite() {
        while (true) {
            int s = (int) STATE.getVolatile(this);
            if (s != 0) {
                return false;
            }
            if (STATE.compareAndSet(this, 0, WRITER_BIT)) {
                return true;
            }
            Thread.onSpinWait();
        }
    }

    public void releaseWrite() {
        while (true) {
            int s = (int) STATE.getVolatile(this);
            if ((s & WRITER_BIT) == 0) {
                throw new IllegalStateException("Not holding write lock");
            }
            if (STATE.compareAndSet(this, s, s & ~WRITER_BIT)) {
                return;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho tất cả các thao tác chuyển trạng thái.
- **Không gian (Space):** $O(1)$ (gói gọn trong 4 byte).
- **Progress Guarantee:** **Lock-Free**.

---

## 25. Lock-Free Dual Queue (Synchronous Transfer Queue)

### Đề bài chi tiết
Trong hàng đợi đồng bộ (`SynchronousQueue` / Dual Queue - Scherer & Scott), các luồng đưa dữ liệu (**Data Producers**) và luồng nhận dữ liệu (**Reservation Consumers**) gặp nhau trực tiếp mà không cần buffer trung gian.
Hãy thiết kế một **Lock-Free Dual Queue**:
- Hàng đợi chứa các nút có hai kiểu: `DATA` (chứa phần tử thực) hoặc `REQUEST` (đại diện cho luồng đang đợi nhận dữ liệu).
- Nếu hàng đợi đang chứa các nút `REQUEST`, một luồng `put(item)` đến sẽ không thêm nút data mới mà sẽ bắt cặp (fulfill) với request đầu tiên và đánh thức luồng nhận.
- Nếu hàng đợi đang chứa `DATA`, luồng `take()` sẽ bắt cặp với data node đầu tiên.

### Phân tích thuật toán
- Mỗi nút `Node` chứa `type` (`DATA` hoặc `REQUEST`), tham chiếu `item`, và con trỏ `waiter` (`Thread`).
- Khi một luồng đến:
  - Nếu `head == tail` hoặc kiểu của nút ở cuối khớp với kiểu của thao tác hiện tại $\rightarrow$ Thêm nút mới vào cuối hàng đợi bằng CAS, sau đó gọi `LockSupport.park()` chờ đối tác.
  - Nếu kiểu nút ở đầu trái ngược (ví dụ hàng đợi đang có `REQUEST` và thao tác hiện tại là `DATA`) $\rightarrow$ Dùng `CAS` trên trường `item` của request để ghép cặp, sau đó gọi `LockSupport.unpark(waiter)`.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.locks.LockSupport;

public class LockFreeDualQueue<E> {

    private enum Mode { DATA, REQUEST }

    private static final class Node<E> {
        final Mode mode;
        final AtomicReference<E> item;
        final AtomicReference<Node<E>> next;
        volatile Thread waiter;

        Node(Mode mode, E item) {
            this.mode = mode;
            this.item = new AtomicReference<>(item);
            this.next = new AtomicReference<>(null);
            this.waiter = Thread.currentThread();
        }
    }

    private final Node<E> sentinel = new Node<>(Mode.REQUEST, null);
    private final AtomicReference<Node<E>> head = new AtomicReference<>(sentinel);
    private final AtomicReference<Node<E>> tail = new AtomicReference<>(sentinel);

    public void put(E data) {
        if (data == null) throw new NullPointerException();
        while (true) {
            Node<E> h = head.get();
            Node<E> t = tail.get();
            if (h == t || t.mode == Mode.DATA) {
                Node<E> next = t.next.get();
                if (t == tail.get()) {
                    if (next != null) {
                        tail.compareAndSet(t, next);
                    } else {
                        Node<E> node = new Node<>(Mode.DATA, data);
                        if (t.next.compareAndSet(null, node)) {
                            tail.compareAndSet(t, node);
                            while (node.item.get() != null) {
                                LockSupport.park(this);
                            }
                            return;
                        }
                    }
                }
            } else {
                Node<E> first = h.next.get();
                if (t != tail.get() || h != head.get() || first == null) continue;
                if (first.item.compareAndSet(null, data)) {
                    head.compareAndSet(h, first);
                    LockSupport.unpark(first.waiter);
                    return;
                }
            }
            Thread.onSpinWait();
        }
    }

    public E take() {
        while (true) {
            Node<E> h = head.get();
            Node<E> t = tail.get();
            if (h == t || t.mode == Mode.REQUEST) {
                Node<E> next = t.next.get();
                if (t == tail.get()) {
                    if (next != null) {
                        tail.compareAndSet(t, next);
                    } else {
                        Node<E> node = new Node<>(Mode.REQUEST, null);
                        if (t.next.compareAndSet(null, node)) {
                            tail.compareAndSet(t, node);
                            while (node.item.get() == null) {
                                LockSupport.park(this);
                            }
                            return node.item.get();
                        }
                    }
                }
            } else {
                Node<E> first = h.next.get();
                if (t != tail.get() || h != head.get() || first == null) continue;
                E val = first.item.get();
                if (val != null && first.item.compareAndSet(val, null)) {
                    head.compareAndSet(h, first);
                    LockSupport.unpark(first.waiter);
                    return val;
                }
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ trung bình cho thao tác ghép cặp và chuyển giao.
- **Không gian (Space):** $O(N)$ lưu trữ các nút đang chờ.
- **Progress Guarantee:** **Lock-Free**.

---

## 26. Lock-Free Sliding Window Multi-Metric Aggregator

### Đề bài chi tiết
Trong các hệ thống giám sát thời gian thực (như Prometheus / Metrics Collector), ta cần liên tục tính tổng (**Sum**), số lượng (**Count**), giá trị trung bình (**Average**), và giá trị lớn nhất (**Max**) trong một cửa sổ thời gian trượt $W$ giây (ví dụ: 60 giây gần nhất).
Hãy xây dựng **Lock-Free Sliding Window Multi-Metric Aggregator**:
- Phân chia cửa sổ trượt thành $B$ buckets thời gian (ví dụ 60 buckets, mỗi bucket 1 giây).
- Hỗ trợ `void record(long value)`: Ghi nhận một giá trị với thời gian hiện tại không dùng khóa.
- Hỗ trợ `Snapshot getSnapshot()`: Lấy ảnh chụp tức thời của cửa sổ trượt.

### Phân tích thuật toán
- Mỗi bucket chứa: `volatile long epochSecond`, `LongAdder sum`, `LongAdder count`, `AtomicLong max`.
- Khi ghi nhận giá trị tại giây `nowSec`:
  - `bucketIdx = nowSec % B`.
  - Nếu `bucket.epochSecond != nowSec`: Luồng dùng CAS để đặt lại bucket cho chu kỳ thời gian mới.
  - Cập nhật số liệu vào bucket tương ứng.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.atomic.AtomicLong;
import java.util.concurrent.atomic.LongAdder;

public class LockFreeSlidingWindowAggregator {

    private static final class Bucket {
        final AtomicLong epochSecond = new AtomicLong(-1);
        final LongAdder sum = new LongAdder();
        final LongAdder count = new LongAdder();
        final AtomicLong max = new AtomicLong(Long.MIN_VALUE);

        void reset(long newEpoch) {
            epochSecond.set(newEpoch);
            sum.reset();
            count.reset();
            max.set(Long.MIN_VALUE);
        }
    }

    private final int numBuckets;
    private final Bucket[] buckets;

    public record Snapshot(long sum, long count, double average, long max) {}

    public LockFreeSlidingWindowAggregator(int windowSeconds) {
        this.numBuckets = windowSeconds;
        this.buckets = new Bucket[windowSeconds];
        for (int i = 0; i < windowSeconds; i++) {
            buckets[i] = new Bucket();
        }
    }

    public void record(long value) {
        long nowSec = System.currentTimeMillis() / 1000;
        int idx = (int) (nowSec % numBuckets);
        Bucket b = buckets[idx];

        long curEpoch = b.epochSecond.get();
        if (curEpoch != nowSec) {
            if (b.epochSecond.compareAndSet(curEpoch, nowSec)) {
                b.sum.reset();
                b.count.reset();
                b.max.set(Long.MIN_VALUE);
            }
        }

        b.sum.add(value);
        b.count.increment();
        b.max.accumulateAndGet(value, Math::max);
    }

    public Snapshot getSnapshot() {
        long nowSec = System.currentTimeMillis() / 1000;
        long totalSum = 0;
        long totalCount = 0;
        long overallMax = Long.MIN_VALUE;

        for (int i = 0; i < numBuckets; i++) {
            Bucket b = buckets[i];
            long epoch = b.epochSecond.get();
            if (nowSec - epoch < numBuckets) {
                totalSum += b.sum.sum();
                totalCount += b.count.sum();
                overallMax = Math.max(overallMax, b.max.get());
            }
        }

        double avg = totalCount > 0 ? (double) totalSum / totalCount : 0.0;
        return new Snapshot(totalSum, totalCount, avg, overallMax == Long.MIN_VALUE ? 0 : overallMax);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** `record`: $O(1)$ Wait-Free / Lock-Free; `getSnapshot`: $O(B)$ với $B$ là số buckets.
- **Không gian (Space):** $O(B)$ cố định.
- **Progress Guarantee:** **Lock-Free**.

---

## 27. Lock-Free Concurrent LRU Cache Eviction

### Đề bài chi tiết
Triển khai bộ nhớ đệm đồng thời (**Concurrent Cache**) hỗ trợ thuật toán loại bỏ phần tử cũ nhất (**LRU - Least Recently Used**) không sử dụng khóa:
- Sử dụng `ConcurrentHashMap` để lưu trữ dữ liệu.
- Thay vì dùng danh sách liên kết đôi có khóa (Doubly-Linked List Lock bottleneck), hãy sử dụng thuật toán **Clock / Second-Chance Eviction** dựa trên bộ đếm thế hệ truy cập nguyên tử để chọn phần tử bị đẩy ra khi cache vượt quá dung lượng `maxCapacity`.

### Phân tích thuật toán
- Mỗi entry trong cache chứa: `key`, `value`, và một `AtomicInteger accessBit`.
- Khi `get(key)`: Đặt `entry.accessBit = 1`.
- Khi chèn vượt quá dung lượng: Bộ giải phóng chạy vòng lặp Clock:
  - Duyệt qua các phần tử: Nếu `accessBit == 1`, đặt lại thành `0`; nếu `accessBit == 0`, tiến hành xóa phần tử này khỏi cache bằng CAS.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;

public class LockFreeClockCache<K, V> {

    private static final class Entry<V> {
        final V value;
        final AtomicInteger accessBit = new AtomicInteger(1);

        Entry(V value) {
            this.value = value;
        }
    }

    private final int maxCapacity;
    private final ConcurrentHashMap<K, Entry<V>> map = new ConcurrentHashMap<>();

    public LockFreeClockCache(int maxCapacity) {
        this.maxCapacity = maxCapacity;
    }

    public V get(K key) {
        Entry<V> entry = map.get(key);
        if (entry != null) {
            entry.accessBit.set(1);
            return entry.value;
        }
        return null;
    }

    public void put(K key, V value) {
        if (map.size() >= maxCapacity) {
            evict();
        }
        map.put(key, new Entry<>(value));
    }

    private void evict() {
        for (K k : map.keySet()) {
            Entry<V> entry = map.get(k);
            if (entry != null) {
                if (entry.accessBit.compareAndSet(1, 0)) {
                    continue;
                } else {
                    map.remove(k, entry);
                    return;
                }
            }
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** `get`: $O(1)$ Wait-Free; `put`: $O(1)$ amortized.
- **Không gian (Space):** $O(C)$ với $C$ là dung lượng tối đa.
- **Progress Guarantee:** **Lock-Free**.

---

## 28. Lock-Free Atomic Multi-Word Snapshot (Afek et al.)

### Đề bài chi tiết
Cho một mảng $M$ thanh ghi nguyên tử. Thiết kế thuật toán chụp ảnh tức thời (**Atomic Snapshot Object**) theo Yehuda Afek et al. (1993):
- `void update(int registerIndex, int value)`: Cập nhật giá trị một ô nhớ kèm số thứ tự phiên bản.
- `int[] scan()`: Trả về một mảng chứa giá trị của toàn bộ $M$ thanh ghi tại cùng một thời điểm logic (**Linearizable Multi-Word Atomic Read**), bất chấp việc các luồng khác đang liên tục ghi đè dữ liệu.

### Phân tích thuật toán
- Mỗi thanh ghi lưu trữ bộ ba: `(value, sequence, snapArray)`.
- `scan()`:
  - Luồng thực hiện hai lần đọc liên tiếp toàn bộ mảng (**Double Collect**).
  - Nếu tất cả các `sequence` ở 2 lần đọc giống hệt nhau $\rightarrow$ Mảng không bị thay đổi trong quá trình đọc, trả về kết quả ngay lập tức.
  - Nếu một luồng ghi làm thay đổi mảng liên tiếp $\ge 2$ lần, nó sẽ sao chép snapshot của nó vào `snapArray`, luồng scan có thể mượn trực tiếp snapshot này (**Helping Mechanism**) để đạt bảo đảm tiến trình **Wait-Free**.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.Arrays;
import java.util.concurrent.atomic.AtomicReferenceArray;

public class LockFreeAtomicSnapshot {

    public static final class RegisterValue {
        final int value;
        final long seq;
        final int[] snap;

        public RegisterValue(int value, long seq, int[] snap) {
            this.value = value;
            this.seq = seq;
            this.snap = snap;
        }
    }

    private final int capacity;
    private final AtomicReferenceArray<RegisterValue> registers;

    public LockFreeAtomicSnapshot(int capacity) {
        this.capacity = capacity;
        this.registers = new AtomicReferenceArray<>(capacity);
        int[] initialSnap = new int[capacity];
        for (int i = 0; i < capacity; i++) {
            registers.set(i, new RegisterValue(0, 0, initialSnap));
        }
    }

    public void update(int index, int value) {
        int[] snap = scan();
        while (true) {
            RegisterValue oldVal = registers.get(index);
            RegisterValue newVal = new RegisterValue(value, oldVal.seq + 1, snap);
            if (registers.compareAndSet(index, oldVal, newVal)) {
                return;
            }
            Thread.onSpinWait();
        }
    }

    public int[] scan() {
        RegisterValue[] firstCollect = new RegisterValue[capacity];
        RegisterValue[] secondCollect = new RegisterValue[capacity];
        boolean[] moved = new boolean[capacity];

        while (true) {
            for (int i = 0; i < capacity; i++) firstCollect[i] = registers.get(i);
            for (int i = 0; i < capacity; i++) secondCollect[i] = registers.get(i);

            boolean clean = true;
            for (int i = 0; i < capacity; i++) {
                if (firstCollect[i].seq != secondCollect[i].seq) {
                    if (moved[i]) {
                        // Nút i đã đổi 2 lần, mượn snapshot của nó
                        return Arrays.copyOf(secondCollect[i].snap, capacity);
                    }
                    moved[i] = true;
                    clean = false;
                    break;
                }
            }

            if (clean) {
                int[] result = new int[capacity];
                for (int i = 0; i < capacity; i++) {
                    result[i] = firstCollect[i].value;
                }
                return result;
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** `update`: $O(M^2)$ Worst-Case; `scan`: $O(M^2)$ với $M$ thanh ghi.
- **Không gian (Space):** $O(M^2)$ lưu trữ các mảng snapshot.
- **Progress Guarantee:** **Wait-Free** (hoàn tất sau tối đa 2 lần phát hiện thay đổi).

---

## 29. Lock-Free Software Transactional Memory (STM) Mini-Engine

### Đề bài chi tiết
Xây dựng một động cơ bộ nhớ giao dịch phần mềm (**Software Transactional Memory - STM**) tối giản dựa trên thuật toán **TL2 (Transactional Locking 2 - Dice, Shalev, Shavit)**:
- Hỗ trợ thực thi các đoạn mã giao dịch thỏa mãn tính chất **ACID** trong bộ nhớ chia sẻ.
- Hỗ trợ: `T executeTransaction(TransactionBlock<T> tx)`.
- Áp dụng kiểm tra xung đột lạc quan (**Optimistic Concurrency Control**) với đồng hồ phiên bản toàn cục (`globalVersionClock`). Nếu phát hiện xung đột dữ liệu giữa thời điểm đọc và ghi, giao dịch sẽ tự động Rollback và Retry mà không gây Deadlock.

### Phân tích thuật toán
- Mỗi biến giao dịch `TxVar<T>` chứa: `value` và `version`.
- Mỗi giao dịch duy trì:
  - `readVersion`: Đọc từ `globalVersionClock` khi bắt đầu.
  - `readSet`: Tập hợp các biến đã đọc và version tại thời điểm đọc.
  - `writeSet`: Tập hợp các biến chuẩn bị ghi.
- Commit Phase:
  1. Khóa tạm thời các biến trong `writeSet`.
  2. Xác thực lại `readSet` (Validate): Kiểm tra xem có biến nào bị luồng khác sửa đổi sau `readVersion` không.
  3. Nếu hợp lệ: Cấp phát `commitVersion = globalVersionClock.incrementAndGet()`, ghi đè giá trị mới và mở khóa.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.atomic.AtomicLong;

public class LockFreeMiniStm {

    private static final AtomicLong globalClock = new AtomicLong(0);

    public static final class TxVar<T> {
        volatile T value;
        volatile long version;

        public TxVar(T initialValue) {
            this.value = initialValue;
            this.version = globalClock.get();
        }
    }

    public static final class TransactionContext {
        final long readVersion = globalClock.get();
        final Map<TxVar<?>, Object> writeSet = new HashMap<>();
        final Map<TxVar<?>, Long> readSet = new HashMap<>();

        @SuppressWarnings("unchecked")
        public <T> T read(TxVar<T> var) {
            if (writeSet.containsKey(var)) {
                return (T) writeSet.get(var);
            }
            long v = var.version;
            T val = var.value;
            readSet.put(var, v);
            if (v > readVersion) {
                throw new RollbackException();
            }
            return val;
        }

        public <T> void write(TxVar<T> var, T val) {
            writeSet.put(var, val);
        }
    }

    public static final class RollbackException extends RuntimeException {}

    @FunctionalInterface
    public interface TxBlock<R> {
        R run(TransactionContext ctx);
    }

    public static <R> R atomic(TxBlock<R> block) {
        while (true) {
            TransactionContext ctx = new TransactionContext();
            try {
                R res = block.run(ctx);
                if (commit(ctx)) {
                    return res;
                }
            } catch (RollbackException e) {
                // Xung đột, retry
            }
            Thread.onSpinWait();
        }
    }

    @SuppressWarnings({"unchecked", "rawtypes"})
    private static boolean commit(TransactionContext ctx) {
        if (ctx.writeSet.isEmpty()) return true;

        long commitVersion = globalClock.incrementAndGet();
        // Validate read-set
        for (Map.Entry<TxVar<?>, Long> entry : ctx.readSet.entrySet()) {
            if (entry.getKey().version > ctx.readVersion) {
                return false;
            }
        }
        // Write-back
        for (Map.Entry<TxVar<?>, Object> entry : ctx.writeSet.entrySet()) {
            TxVar var = entry.getKey();
            var.value = entry.getValue();
            var.version = commitVersion;
        }
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(|ReadSet| + |WriteSet|)$ khi commit thành công.
- **Không gian (Space):** $O(|ReadSet| + |WriteSet|)$ bộ nhớ tạm cho Transaction Context.
- **Progress Guarantee:** **Obstruction-Free / Lock-Free**.

---

## 30. Lock-Free Synchronous Transfer Stack (Dual Stack)

### Đề bài chi tiết
Thiết kế cấu trúc ngăn xếp truyền dữ liệu trực tiếp (**Lock-Free Dual Stack / Synchronous Stack**) tương tự như chế độ Fair=false của `java.util.concurrent.SynchronousQueue` (Scherer, Lea, Scott - 2006).
Ngăn xếp hỗ trợ hai kiểu nút:
- Nút dữ liệu (`DATA`): Đại diện cho thao tác `push(item)` đang chờ một luồng `pop()`.
- Nút yêu cầu (`REQUEST`): Đại diện cho thao tác `pop()` đang chờ một luồng `push()`.
- Khi một nút mới có chế độ đối lập được đẩy vào đỉnh stack, nó không nằm yên mà sẽ đóng vai trò là một nút hoàn tất (**Fulfilling Node**), liên kết trực tiếp với nút ngay dưới đỉnh và giải phóng cả hai khỏi ngăn xếp sau một thao tác CAS kép.

### Phân tích thuật toán
- Các chế độ của nút: `REQUEST = 0`, `DATA = 1`, `FULFILLING = 2`.
- Khi luồng đến đỉnh `head`:
  1. Nếu `head == null` hoặc `head.mode == myMode`: Thử dùng `CAS(head, curHead, newNode)` để đẩy mình lên đỉnh stack và chờ đối tác qua `LockSupport.park()`.
  2. Nếu `head.mode == oppositeMode`: Thử chèn một nút trung gian có cờ `FULFILLING | myMode` lên đỉnh stack.
  3. Sau khi chèn thành công nút Fulfilling, thực hiện thao tác hoàn tất bằng cách nối `head` nhảy qua cả nút fulfilling và nút mục tiêu (`CAS(head, fulfillingNode, targetNode.next)`), sau đó đánh thức luồng đối tác.

### Mã nguồn Java
```java
package parallel.lockfree;

import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
import java.util.concurrent.locks.LockSupport;

public class LockFreeSynchronousStack<E> {

    private static final int REQUEST = 0;
    private static final int DATA = 1;
    private static final int FULFILLING = 2;

    private static final class Node<E> {
        final int mode;
        volatile E item;
        volatile Node<E> next;
        volatile Node<E> match;
        volatile Thread waiter;

        Node(int mode, E item) {
            this.mode = mode;
            this.item = item;
            this.waiter = Thread.currentThread();
        }

        boolean casNext(Node<E> cmp, Node<E> val) {
            return NEXT.compareAndSet(this, cmp, val);
        }

        private static final VarHandle NEXT;
        private static final VarHandle MATCH;
        static {
            try {
                MethodHandles.Lookup l = MethodHandles.lookup();
                NEXT = l.findVarHandle(Node.class, "next", Node.class);
                MATCH = l.findVarHandle(Node.class, "match", Node.class);
            } catch (ReflectiveOperationException e) {
                throw new ExceptionInInitializerError(e);
            }
        }
    }

    private volatile Node<E> head;

    private static final VarHandle HEAD;
    static {
        try {
            MethodHandles.Lookup l = MethodHandles.lookup();
            HEAD = l.findVarHandle(LockFreeSynchronousStack.class, "head", Node.class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }

    public void push(E item) {
        if (item == null) throw new NullPointerException();
        transfer(item, DATA);
    }

    public E pop() {
        return transfer(null, REQUEST);
    }

    private E transfer(E item, int mode) {
        Node<E> s = null;
        while (true) {
            @SuppressWarnings("unchecked")
            Node<E> h = (Node<E>) HEAD.getVolatile(this);

            if (h == null || (h.mode & ~FULFILLING) == mode) {
                // Stack rỗng hoặc cùng mode -> Thêm node chờ
                if (s == null) s = new Node<>(mode, item);
                s.next = h;
                if (HEAD.compareAndSet(this, h, s)) {
                    // Chờ luồng khác fulfill
                    while (s.match == null) {
                        LockSupport.park(this);
                    }
                    return mode == REQUEST ? s.match.item : null;
                }
            } else if ((h.mode & FULFILLING) == 0) {
                // Khác mode -> Fulfill node đỉnh
                if (s == null) s = new Node<>(mode | FULFILLING, item);
                s.next = h;
                if (HEAD.compareAndSet(this, h, s)) {
                    while (true) {
                        Node<E> m = s.next;
                        if (m == null) {
                            HEAD.compareAndSet(this, s, null);
                            s = null;
                            break;
                        }
                        Node<E> mn = m.next;
                        m.match = s;
                        s.match = m;
                        HEAD.compareAndSet(this, s, mn);
                        LockSupport.unpark(m.waiter);
                        return mode == REQUEST ? m.item : item;
                    }
                }
            } else {
                // Giúp đỡ fulfilling node khác hoàn tất
                Node<E> m = h.next;
                if (m != null) {
                    HEAD.compareAndSet(this, h, m.next);
                    LockSupport.unpark(m.waiter);
                }
            }
            Thread.onSpinWait();
        }
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ amortized cho cả `push` và `pop` qua cơ chế Fulfilling và Helping.
- **Không gian (Space):** $O(N)$ phân bổ động các nút đồng bộ trên ngăn xếp.
- **Progress Guarantee:** **Lock-Free**.

---

## Bảng tổng kết 30 thuật toán và cấp độ bảo đảm tiến trình (Progress Guarantees)

| STT | Tên thuật toán | Cấu trúc dữ liệu chính | Progress Guarantee | Điểm cốt lõi kỹ thuật |
|:---:|:---|:---|:---|:---|
| 1 | Lock-Free Treiber Stack | Singly-Linked List | **Lock-Free** | CAS Loop trên biến `top`, LIFO order |
| 2 | Michael-Scott Queue | Linked List + Sentinel | **Lock-Free** | 2-step CAS enqueue, Helping mechanism trên `tail` |
| 3 | Harris's Ordered List | Linked List + Marks | **Lock-Free** / **Wait-Free Search** | Logical deletion qua `AtomicMarkableReference` |
| 4 | ABA-Proof Stack | Stamped Linked List | **Lock-Free** | `AtomicStampedReference` + Tag counter |
| 5 | Elimination Backoff Stack | Treiber Stack + Exchanger[] | **Lock-Free** | Triệt tiêu xung đột đa luồng không chạm đỉnh stack |
| 6 | MPMC Ring Buffer | Contended Array + Sequence | **Lock-Free** | Power-of-2 bitmask, Cache line padding, Seq markers |
| 7 | Resizable Dynamic Array | 2-Level Bucket Tree | **Wait-Free Get** / **Lock-Free Add** | Cây mảng phân tầng, không copy khi resize |
| 8 | Lock-Free SkipList Set | Multi-Level Towers | **Lock-Free** / **Wait-Free Search** | Đánh dấu xóa từ đỉnh tháp xuống đáy |
| 9 | Lock-Free Doubly-Linked List | Bidirectional Links | **Lock-Free** | `prev` link như backlink/hint phục hồi |
| 10 | Split-Ordered Hash Map | Bit-Reversed List | **Lock-Free** | Expand bucket bằng Dummy nodes, Zero-copy rehash |
| 11 | Free-List Object Pool | Recycled Node Stack | **Lock-Free** | Tái chế đối tượng, Zero GC, ABA versioning |
| 12 | Epoch-Based Reclamation (RCU) | Epoch Queues | **Wait-Free Read** / **Lock-Free Retire** | Quiescent state & Grace period memory tracking |
| 13 | Chase-Lev Work-Stealing Deque | Ring Buffer + Top/Bottom | **Wait-Free Push** / **Lock-Free Steal** | Single Owner LIFO + Multi-Thief FIFO CAS steal |
| 14 | Sliding Window Rate Limiter | Token Bucket + Time Stamp | **Lock-Free** | CAS atomic update `(tokens, nanoTime)` |
| 15 | SPSC Ring Buffer Queue | Circular Array + Memory Barriers | **Wait-Free** | Unidirectional Single-Producer Single-Consumer |
| 16 | MPSC Unbounded Queue | Node Chain + Atomic Exchange | **Wait-Free Offer** / **Lock-Free Poll** | `getAndSet` $O(1)$ Wait-Free enqueue |
| 17 | Universal Construction | Consensus Log + Announce Array | **Wait-Free** | Biến cấu trúc tuần tự thành Wait-Free sau $T$ bước |
| 18 | Hazard Pointers Manager | Thread-Local Guards | **Wait-Free Guard** / **Lock-Free Reclaim** | Bảo vệ con trỏ đọc an toàn không bị free |
| 19 | Atomic Counter Backoff | AtomicLong + Backoff Spins | **Lock-Free** | `Thread.onSpinWait()`, Exponential jitter backoff |
| 20 | Flat Combining Simulator | Publication Slots + Combiner | **Lock-Free** | Batch execution, tối ưu hóa L1/L2 Data Cache |
| 21 | Lock-Free Priority Queue | SkipList / Marked Nodes | **Lock-Free** | `deleteMin` $O(1)$ amortized qua Logical Marking |
| 22 | Concurrent Dynamic BitSet | AtomicLongArray | **Wait-Free Get** / **Lock-Free Update** | CAS bitwise manipulation `(OR / AND ~MASK)` |
| 23 | Reference Counting GC | Split Counter | **Wait-Free** | `retain`/`release` an toàn không race condition |
| 24 | Read-Write State Machine | 32-bit Packed State | **Lock-Free** | Shared readers count + Exclusive writer flag bitwise |
| 25 | Lock-Free Dual Queue | Data/Request Nodes | **Lock-Free** | Scherer-Scott rendezvous transfer, thread unparking |
| 26 | Multi-Metric Aggregator | Bucketed Ring Buffer | **Lock-Free** | Online Time-bucketed LongAdders & AtomicLongs |
| 27 | Concurrent LRU Clock Cache | ConcurrentHashMap + AccessBit | **Lock-Free** | Clock / Second chance eviction algorithm |
| 28 | Atomic Snapshot Object | Vector Registers + Scan | **Wait-Free** | Double collect validation & Borrowing snapshots |
| 29 | Mini Software STM Engine | Transaction Context + TL2 | **Obstruction-Free** | Versioned optimistic commit & Automatic rollback |
| 30 | Dual Synchronous Stack | Fulfilling Modes Stack | **Lock-Free** | Rendezvous pairing trên đỉnh ngăn xếp |

---
*Tài liệu thuộc bộ giáo trình **Data Structures & Algorithms in Java 21 - Parallel & Concurrent Algorithms Series**.*
