# Companion Problems: 04-Amortized-Analysis

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Amortized Analysis (Phân tích chi phí rải đều). Các bài toán tập trung vào việc tính toán, chứng minh và nhận diện Amortized Cost trong các cấu trúc dữ liệu thực tế.

---

## 1. Dynamic Array Add — Aggregate Method
**Đề bài chi tiết:** Cài đặt một Dynamic Array (Mảng động) với hệ số nhân đôi (Doubling strategy). Thêm $N = 32$ phần tử. Đếm tổng số phép copy xảy ra trong quá trình Resize. Tính Amortized Cost bằng Aggregate Method.
**Phân tích thuật toán:** Resize xảy ra khi kích thước bằng Capacity, tại các thời điểm $1, 2, 4, 8, 16, 32$. Tổng số phép copy: $1 + 2 + 4 + 8 + 16 = 31$. Cộng thêm 32 phép ghi: Tổng cost $= 31 + 32 = 63$. Amortized Cost $= 63/32 \approx 1.97$. Bằng Aggregate Method, $\hat{c} = \mathcal{O}(1)$.
**Mã nguồn Java:**
```java
public class AggregateMethodDemo {
    public static void main(String[] args) {
        int N = 32;
        int capacity = 1, size = 0;
        long totalCopies = 0, totalWrites = 0;
        int[] arr = new int[capacity];

        for (int i = 0; i < N; i++) {
            if (size == capacity) {
                totalCopies += capacity; // Đếm copy
                capacity *= 2;
                arr = java.util.Arrays.copyOf(arr, capacity);
            }
            arr[size++] = i;
            totalWrites++;
        }
        long totalCost = totalCopies + totalWrites;
        System.out.printf("Total cost: %d, Amortized: %.2f%n", totalCost, (double) totalCost / N);
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ per `add()`.

---

## 2. Dynamic Array Add — Accounting Method
**Đề bài chi tiết:** Sử dụng Accounting Method để chứng minh Amortized $\mathcal{O}(1)$ cho Dynamic Array. Gán phí $\hat{c} = 3$ cho mỗi thao tác `add()`. Theo dõi số dư Credit qua 16 thao tác.
**Phân tích thuật toán:** Mỗi thao tác `add()` trả phí 3 đồng. 1 đồng dùng để ghi phần tử vào mảng. 2 đồng dư được lưu vào "ngân hàng Credit". Khi Resize xảy ra tại bước thứ $K$, ta phải copy $K/2$ phần tử (vì mảng nhân đôi). Nhưng $K/2$ phần tử mới nhất (được thêm kể từ lần Resize trước) mỗi cái đã đóng góp 2 đồng Credit $\implies$ Tổng Credit đúng bằng $K$ (đủ trả chi phí copy). Do đó Credit KHÔNG BAO GIỜ âm.
**Mã nguồn Java:**
```java
public class AccountingMethodDemo {
    public static void main(String[] args) {
        int N = 16;
        int capacity = 1, size = 0;
        int credit = 0;
        int CHARGE = 3; // Phí ước lượng mỗi thao tác

        for (int i = 0; i < N; i++) {
            credit += CHARGE; // Thu phí
            if (size == capacity) {
                credit -= capacity; // Trả phí copy
                capacity *= 2;
            }
            credit -= 1; // Trả phí ghi
            size++;
            System.out.printf("Step %2d: Credit = %d%n", i + 1, credit);
        }
        System.out.println("Credit luôn >= 0: Chứng minh hoàn tất.");
    }
}
```
**Độ phức tạp:** Amortized $\hat{c} = 3 = \mathcal{O}(1)$.

---

## 3. Dynamic Array Add — Potential Method
**Đề bài chi tiết:** Cài đặt hàm tính Potential (Thế năng) $\Phi(D) = 2 \times \text{Size} - \text{Capacity}$ sau mỗi thao tác `add()`. Xác minh Amortized Cost luôn bằng 3.
**Phân tích thuật toán:** Potential Method sử dụng hàm $\Phi$. Với thao tác add() không resize: $\hat{c} = 1 + \Phi_{after} - \Phi_{before} = 1 + (2(s+1) - cap) - (2s - cap) = 3$. Với thao tác add() có resize (cap nhân đôi, copy $s$ phần tử): $c = s + 1$, $\hat{c} = (s+1) + (2(s+1) - 2s) - (2s - s) = s + 1 + 2 - s = 3$. Cả hai trường hợp đều cho $\hat{c} = 3$.
**Mã nguồn Java:**
```java
public class PotentialMethodDemo {
    public static void main(String[] args) {
        int N = 16;
        int capacity = 1, size = 0;

        for (int i = 0; i < N; i++) {
            int phiBefore = 2 * size - capacity;
            int actualCost;
            if (size == capacity) {
                actualCost = size + 1; // copy + write
                capacity *= 2;
            } else {
                actualCost = 1; // write only
            }
            size++;
            int phiAfter = 2 * size - capacity;
            int amortized = actualCost + phiAfter - phiBefore;
            System.out.printf("Step %2d: actual=%d, Phi=%d->%d, amortized=%d%n",
                    i + 1, actualCost, phiBefore, phiAfter, amortized);
        }
    }
}
```
**Độ phức tạp:** Amortized $\hat{c} = 3 = \mathcal{O}(1)$. QED.

---

## 4. Multi-Pop Stack
**Đề bài chi tiết:** Cài đặt Stack hỗ trợ 3 thao tác: `push(x)`, `pop()`, `multipop(k)` — Pop $k$ phần tử cùng lúc. Chứng minh chuỗi $N$ thao tác bất kỳ tốn Amortized $\mathcal{O}(1)$ trên mỗi thao tác.
**Phân tích thuật toán:** Mỗi phần tử chỉ có thể bị Pop TỐI ĐA 1 lần (vì sau khi Pop nó biến mất). Nên tổng số lần Pop trên TẤT CẢ các lệnh multipop trong $N$ thao tác không bao giờ vượt quá tổng số lần Push. Tổng chi phí: $\le 2N$ (N lần push + tối đa N lần pop). Amortized: $2N/N = 2 = \mathcal{O}(1)$.
**Mã nguồn Java:**
```java
import java.util.Stack;
public class MultiPopStack {
    private Stack<Integer> stack = new Stack<>();

    public void push(int x) {
        stack.push(x);
    }

    public int pop() {
        return stack.pop();
    }

    /**
     * Pop min(k, size) phần tử.
     * Worst-case O(N), nhưng Amortized O(1) per element.
     */
    public void multipop(int k) {
        int count = Math.min(k, stack.size());
        for (int i = 0; i < count; i++) {
            stack.pop();
        }
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ per thao tác trên chuỗi $N$ thao tác.

---

## 5. Binary Counter — Bit Flip Amortization
**Đề bài chi tiết:** Cài đặt bộ đếm nhị phân $K$ bit. Thao tác `increment()` tăng giá trị lên 1. Đếm tổng số lần flip bit (0→1 hoặc 1→0) qua $N$ lần increment. Tính Amortized Cost.
**Phân tích thuật toán:** Bit 0 (LSB) flip MỖI lần increment: $N$ lần. Bit 1 flip MỖI 2 lần: $N/2$. Bit 2 flip MỖI 4 lần: $N/4$. Tổng flips: $N + N/2 + N/4 + \dots < 2N$. Amortized: $2N/N = 2 = \mathcal{O}(1)$.
**Mã nguồn Java:**
```java
public class BinaryCounter {
    private int[] bits;
    private long totalFlips;

    public BinaryCounter(int k) {
        bits = new int[k];
        totalFlips = 0;
    }

    public void increment() {
        int i = 0;
        // Carry propagation: Đặt lại tất cả bit 1 thành 0
        while (i < bits.length && bits[i] == 1) {
            bits[i] = 0;
            totalFlips++;
            i++;
        }
        // Đặt bit đầu tiên tìm được (hoặc bit tràn) thành 1
        if (i < bits.length) {
            bits[i] = 1;
            totalFlips++;
        }
    }

    public double getAmortizedFlips(int ops) {
        return (double) totalFlips / ops;
    }

    public static void main(String[] args) {
        int N = 1_000_000;
        BinaryCounter counter = new BinaryCounter(32);
        for (int i = 0; i < N; i++) counter.increment();
        System.out.printf("Amortized flips per increment: %.4f%n",
                counter.getAmortizedFlips(N));
        // Kết quả hội tụ về ~2.0
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ per increment.

---

## 6. HashMap Rehashing Simulation
**Đề bài chi tiết:** Cài đặt một Hash Table đơn giản với Chaining (Danh sách liên kết). Load Factor threshold $= 0.75$. Theo dõi tổng chi phí Rehash qua $N$ lần `put()`.
**Phân tích thuật toán:** Rehash xảy ra khi $\text{size} > 0.75 \times \text{capacity}$. Tại mỗi lần Rehash, mọi entry bị di chuyển sang bảng mới gấp đôi kích thước — tốn $\mathcal{O}(\text{size})$. Nhưng giữa 2 lần Rehash liên tiếp, ta thêm được ít nhất $\text{capacity}/4$ entry mới, rải đều chi phí Rehash cho các entry này.
**Mã nguồn Java:**
```java
import java.util.LinkedList;
public class SimpleHashMap {
    private LinkedList<int[]>[] buckets;
    private int size, capacity;
    private long totalRehashCost;
    private static final double LOAD_FACTOR = 0.75;

    @SuppressWarnings("unchecked")
    public SimpleHashMap(int initCap) {
        capacity = initCap;
        buckets = new LinkedList[capacity];
        for (int i = 0; i < capacity; i++) buckets[i] = new LinkedList<>();
        size = 0;
        totalRehashCost = 0;
    }

    public void put(int key, int value) {
        if ((double) size / capacity > LOAD_FACTOR) {
            rehash();
        }
        int idx = Math.abs(key) % capacity;
        buckets[idx].add(new int[]{key, value});
        size++;
    }

    @SuppressWarnings("unchecked")
    private void rehash() {
        int newCap = capacity * 2;
        LinkedList<int[]>[] newBuckets = new LinkedList[newCap];
        for (int i = 0; i < newCap; i++) newBuckets[i] = new LinkedList<>();
        for (LinkedList<int[]> bucket : buckets) {
            for (int[] entry : bucket) {
                int idx = Math.abs(entry[0]) % newCap;
                newBuckets[idx].add(entry);
                totalRehashCost++; // Đếm chi phí di chuyển
            }
        }
        buckets = newBuckets;
        capacity = newCap;
    }

    public static void main(String[] args) {
        int N = 100_000;
        SimpleHashMap map = new SimpleHashMap(16);
        for (int i = 0; i < N; i++) map.put(i, i);
        System.out.printf("Total rehash cost: %d%n", map.totalRehashCost);
        System.out.printf("Amortized rehash cost per put: %.4f%n",
                (double) map.totalRehashCost / N);
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ per `put()`.

---

## 7. StringBuilder Resize
**Đề bài chi tiết:** Cài đặt `MyStringBuilder` mô phỏng cơ chế nội bộ của `StringBuilder`. Buffer ban đầu 16 ký tự. Khi đầy, nhân đôi kích thước. Chứng minh `append(char)` là Amortized $\mathcal{O}(1)$.
**Phân tích thuật toán:** Cơ chế giống hệt Dynamic Array. Buffer là một `char[]` nội bộ. Khi đầy, tạo `char[]` mới gấp đôi và `System.arraycopy`. Chứng minh bằng Aggregate Method: Tổng copy $< 2N$, tổng ghi $= N$, Amortized $= 3N/N = 3$.
**Mã nguồn Java:**
```java
public class MyStringBuilder {
    private char[] buffer;
    private int size;

    public MyStringBuilder() {
        buffer = new char[16]; // Giá trị mặc định giống StringBuilder Java
        size = 0;
    }

    public MyStringBuilder append(char c) {
        if (size == buffer.length) {
            char[] newBuf = new char[buffer.length * 2];
            System.arraycopy(buffer, 0, newBuf, 0, size);
            buffer = newBuf;
        }
        buffer[size++] = c;
        return this;
    }

    @Override
    public String toString() {
        return new String(buffer, 0, size);
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ per `append()`.

---

## 8. Queue Bằng Hai Stack (Amortized Queue)
**Đề bài chi tiết:** Cài đặt Queue (Hàng đợi) chỉ bằng 2 Stack. Thao tác `enqueue()` đẩy vào Stack A. Thao tác `dequeue()` Pop từ Stack B. Khi Stack B rỗng, đổ toàn bộ Stack A sang B. Chứng minh mỗi thao tác Enqueue/Dequeue là Amortized $\mathcal{O}(1)$.
**Phân tích thuật toán:** Mỗi phần tử trong suốt vòng đời của nó chỉ bị: Push vào A (1 lần), Pop khỏi A rồi Push vào B (1 lần mỗi phép), Pop khỏi B (1 lần). Tổng cộng mỗi phần tử bị thao tác chính xác 4 lần. $N$ phần tử tốn $4N$ thao tác. Chia đều: $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
import java.util.Stack;
public class QueueWithTwoStacks<T> {
    private Stack<T> inbox = new Stack<>();   // Enqueue
    private Stack<T> outbox = new Stack<>();  // Dequeue

    public void enqueue(T item) {
        inbox.push(item); // O(1)
    }

    public T dequeue() {
        if (outbox.isEmpty()) {
            // Đổ toàn bộ inbox sang outbox: O(N) Worst-case
            while (!inbox.isEmpty()) {
                outbox.push(inbox.pop());
            }
        }
        return outbox.pop(); // O(1)
    }
}
```
**Độ phức tạp:** Enqueue $\mathcal{O}(1)$. Dequeue Amortized $\mathcal{O}(1)$.

---

## 9. Increment/Decrement Counter — Asymmetry
**Đề bài chi tiết:** Binary Counter hỗ trợ cả `increment()` và `decrement()`. Chứng minh Amortized Analysis BỊ PHÁ VỠ trong trường hợp này.
**Phân tích thuật toán:** Xét chuỗi thao tác: Increment, Decrement, Increment, Decrement... (lặp lại) tại giá trị $2^K - 1$ (tất cả bit đều là 1). Mỗi Increment flip $K$ bit ($111...1 \to 1000...0$). Mỗi Decrement flip $K$ bit ($1000...0 \to 0111...1$). Tổng flips sau $N$ thao tác: $N \times K$. Amortized Cost: $K = \mathcal{O}(\log N)$. Kết luận: Decrement phá vỡ tính chất Amortized $\mathcal{O}(1)$ của Counter.
**Mã nguồn Java:**
```java
public class IncDecCounter {
    private int[] bits;
    private long totalFlips;

    public IncDecCounter(int k) { bits = new int[k]; totalFlips = 0; }

    public void increment() {
        int i = 0;
        while (i < bits.length && bits[i] == 1) { bits[i++] = 0; totalFlips++; }
        if (i < bits.length) { bits[i] = 1; totalFlips++; }
    }

    public void decrement() {
        int i = 0;
        while (i < bits.length && bits[i] == 0) { bits[i++] = 1; totalFlips++; }
        if (i < bits.length) { bits[i] = 0; totalFlips++; }
    }

    public static void main(String[] args) {
        IncDecCounter c = new IncDecCounter(20);
        // Đưa về giá trị 2^20 - 1
        for (int i = 0; i < (1 << 20) - 1; i++) c.increment();
        c.totalFlips = 0;
        // Lặp Inc/Dec
        int N = 100_000;
        for (int i = 0; i < N; i++) {
            if (i % 2 == 0) c.increment(); else c.decrement();
        }
        System.out.printf("Amortized flips: %.4f (NOT O(1))%n", (double) c.totalFlips / N);
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(\log N)$ — KHÔNG còn $\mathcal{O}(1)$.

---

## 10. Dynamic Array với Growth Factor 1.5
**Đề bài chi tiết:** Thay đổi hệ số nhân từ $2\times$ sang $1.5\times$ (giống ArrayList Java). So sánh tổng chi phí copy và Peak Memory với bài Doubling.
**Phân tích thuật toán:** Với factor $1.5\times$, Resize xảy ra thường hơn nhưng mỗi lần copy ít hơn. Tổng chi phí copy vẫn là chuỗi hình học (Geometric series) hội tụ: $N + 2N/3 + 4N/9 + \dots < 3N$. Amortized vẫn $\mathcal{O}(1)$ nhưng hằng số cao hơn doubling. Peak Memory giảm vì mảng mới nhỏ hơn.
**Mã nguồn Java:**
```java
public class GrowthFactor15 {
    public static void main(String[] args) {
        int N = 1_000_000;
        int capacity = 1, size = 0;
        long totalCopies = 0;
        int peakMemory = 0;

        for (int i = 0; i < N; i++) {
            if (size == capacity) {
                int newCap = capacity + (capacity >> 1); // 1.5x
                if (newCap == capacity) newCap++;
                totalCopies += capacity;
                peakMemory = Math.max(peakMemory, capacity + newCap);
                capacity = newCap;
            }
            size++;
        }
        System.out.printf("Total copies: %d, Amortized: %.4f%n", totalCopies, (double)(totalCopies + N) / N);
        System.out.printf("Peak memory (units): %d%n", peakMemory);
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$, Peak Memory $\approx 2.5N$ (thấp hơn $3N$ của doubling).

---

## 11. Dynamic Array Shrinking
**Đề bài chi tiết:** Cài đặt Dynamic Array hỗ trợ `remove()`. Khi size giảm xuống dưới $\text{capacity}/4$, thu nhỏ mảng còn $\text{capacity}/2$. Chứng minh Amortized Cost vẫn $\mathcal{O}(1)$.
**Phân tích thuật toán:** Tại sao không shrink khi $\text{size} < \text{capacity}/2$? Vì có thể gây "Thrashing" (Resize lên xuống liên tục). Khi threshold là $1/4$ và resize lên $2\times$, giữa 2 lần resize cần ít nhất $N/4$ thao tác, đủ rải đều chi phí.
**Mã nguồn Java:**
```java
public class ShrinkableArray {
    private int[] data;
    private int size, capacity;

    public ShrinkableArray() { capacity = 4; data = new int[capacity]; size = 0; }

    public void add(int val) {
        if (size == capacity) { resize(capacity * 2); }
        data[size++] = val;
    }

    public int removeLast() {
        int val = data[--size];
        // Shrink khi size < capacity/4 (KHÔNG phải capacity/2 để tránh Thrashing)
        if (size > 0 && size == capacity / 4) { resize(capacity / 2); }
        return val;
    }

    private void resize(int newCap) {
        int[] newData = new int[newCap];
        System.arraycopy(data, 0, newData, 0, size);
        data = newData;
        capacity = newCap;
    }
}
```
**Độ phức tạp:** Cả `add()` và `removeLast()` đều Amortized $\mathcal{O}(1)$.

---

## 12. Stack Queue Hybrid — Batched Transfer
**Đề bài chi tiết:** Cải tiến bài Queue 2 Stack (Bài 8). Khi inbox đạt $K$ phần tử, lập tức chuyển (Transfer) sang outbox thay vì đợi outbox rỗng. Phân tích tác động lên Amortized Cost.
**Phân tích thuật toán:** Nếu ta chủ động Transfer trước khi outbox rỗng, một phần tử có thể bị chuyển NHIỀU lần (nếu bị push vào outbox rồi lại bị chuyển lại khi outbox chưa rỗng). Điều này phá vỡ bất biến "mỗi phần tử chỉ bị chuyển 1 lần". Amortized Cost tăng lên tùy thuộc vào $K$. Kết luận: Thiết kế ban đầu (chỉ chuyển khi outbox rỗng) là TỐI ƯU.
**Mã nguồn Java:**
```java
// Minh họa: Chỉ cho thấy thiết kế tối ưu (không thay đổi logic)
// Bài học: ĐỪNG tối ưu sớm (Premature Optimization)
public class OptimalQueueTwoStacks<T> {
    private java.util.Stack<T> inbox = new java.util.Stack<>();
    private java.util.Stack<T> outbox = new java.util.Stack<>();

    public void enqueue(T item) { inbox.push(item); }

    public T dequeue() {
        if (outbox.isEmpty()) { // CHỈ chuyển khi outbox rỗng
            while (!inbox.isEmpty()) outbox.push(inbox.pop());
        }
        return outbox.pop();
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(1)$ (Tối ưu).

---

## 13. Pre-allocated ArrayList vs Default
**Đề bài chi tiết:** So sánh thời gian thêm $10^7$ phần tử vào `ArrayList()` vs `ArrayList(10_000_000)`.
**Phân tích thuật toán:** Pre-allocation loại bỏ hoàn toàn mọi chi phí Resize. ArrayList mặc định bắt đầu với Capacity 10, phải Resize khoảng $\log_{1.5}(10^7 / 10) \approx 40$ lần. Mỗi lần Resize tạo mảng mới và copy toàn bộ dữ liệu. Pre-allocation tốt hơn cả về Time (ít copy) lẫn Peak Memory (không bao giờ có 2 mảng đồng thời).
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
public class PreAllocBenchmark {
    public static void main(String[] args) {
        int N = 10_000_000;

        long t1 = System.nanoTime();
        List<Integer> a = new ArrayList<>();
        for (int i = 0; i < N; i++) a.add(i);
        long time1 = System.nanoTime() - t1;

        long t2 = System.nanoTime();
        List<Integer> b = new ArrayList<>(N);
        for (int i = 0; i < N; i++) b.add(i);
        long time2 = System.nanoTime() - t2;

        System.out.printf("Default: %d ms%n", time1 / 1_000_000);
        System.out.printf("Pre-alloc: %d ms%n", time2 / 1_000_000);
    }
}
```
**Độ phức tạp:** Cả hai đều Amortized $\mathcal{O}(1)$, nhưng Pre-alloc nhanh hơn ~20-30%.

---

## 14. Splay Tree Access — Amortized O(log N)
**Đề bài chi tiết:** Cài đặt cây Splay Tree cơ bản (Chỉ cần thao tác `splay()`). Truy cập Node sâu nhất. Chứng minh dù Worst-case là $\mathcal{O}(N)$, chuỗi $M$ thao tác tốn $\mathcal{O}(M \log N)$.
**Phân tích thuật toán:** Splay Tree tự cấu trúc lại (Self-adjusting): Khi truy cập một Node, nó được xoay (Rotate) lên gốc. Thao tác xoay cực tốn kém cho Node sâu, nhưng sau khi xoay, Node đó ở gốc (tầng 0) — truy cập sau này gần như miễn phí. Bằng Potential Method (với $\Phi = \sum \log(\text{subtree size})$), ta chứng minh Amortized $\mathcal{O}(\log N)$.
**Mã nguồn Java:**
```java
public class SplayTreeBasic {
    static class Node { int key; Node left, right, parent; }

    private Node root;

    private void rotateRight(Node x) {
        Node y = x.parent;
        y.left = x.right;
        if (x.right != null) x.right.parent = y;
        x.parent = y.parent;
        if (y.parent != null) {
            if (y == y.parent.left) y.parent.left = x;
            else y.parent.right = x;
        }
        x.right = y;
        y.parent = x;
    }

    private void rotateLeft(Node x) {
        Node y = x.parent;
        y.right = x.left;
        if (x.left != null) x.left.parent = y;
        x.parent = y.parent;
        if (y.parent != null) {
            if (y == y.parent.left) y.parent.left = x;
            else y.parent.right = x;
        }
        x.left = y;
        y.parent = x;
    }

    /**
     * Splay node x lên gốc.
     * Amortized O(log N) per splay.
     */
    public void splay(Node x) {
        while (x.parent != null) {
            if (x.parent.parent == null) {
                // Zig
                if (x == x.parent.left) rotateRight(x);
                else rotateLeft(x);
            } else if (x == x.parent.left && x.parent == x.parent.parent.left) {
                // Zig-Zig
                rotateRight(x.parent);
                rotateRight(x);
            } else if (x == x.parent.right && x.parent == x.parent.parent.right) {
                // Zig-Zig (mirror)
                rotateLeft(x.parent);
                rotateLeft(x);
            } else if (x == x.parent.left && x.parent == x.parent.parent.right) {
                // Zig-Zag
                rotateRight(x);
                rotateLeft(x);
            } else {
                // Zig-Zag (mirror)
                rotateLeft(x);
                rotateRight(x);
            }
        }
        root = x;
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(\log N)$ per splay.

---

## 15. Union-Find với Path Compression
**Đề bài chi tiết:** Cài đặt Union-Find (Disjoint Set Union) với Path Compression và Union by Rank. Chứng minh chuỗi $M$ thao tác `find()` trên $N$ phần tử tốn Amortized gần $\mathcal{O}(1)$.
**Phân tích thuật toán:** Inverse Ackermann function $\alpha(N)$ tăng cực kỳ chậm ($\alpha(2^{65536}) = 5$). Trong thực tế, $\alpha(N) \le 4$ cho mọi $N$ khả thi. Nhờ Path Compression (Mỗi Node trỏ thẳng về Root), cây trở nên phẳng dần theo thời gian. Đây là một trong những Amortized Analysis phức tạp nhất trong Khoa học Máy tính (Tarjan, 1975).
**Mã nguồn Java:**
```java
public class UnionFind {
    private int[] parent, rank;

    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
    }

    /**
     * Find with Path Compression.
     * Amortized O(α(N)) ≈ O(1).
     */
    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // Path Compression
        }
        return parent[x];
    }

    public void union(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (rank[ra] < rank[rb]) { int t = ra; ra = rb; rb = t; }
        parent[rb] = ra;
        if (rank[ra] == rank[rb]) rank[ra]++;
    }
}
```
**Độ phức tạp:** Amortized $\mathcal{O}(\alpha(N))$ per operation.

---

## 16-30. Tóm lược phân tích các biến thể Amortized Analysis

- **Bài 16 (ArrayList ensureCapacity):** Gọi `ensureCapacity(N)` trước khi thêm $N$ phần tử loại bỏ toàn bộ Resize overhead. Amortized Cost giảm từ $\hat{c} = 3$ xuống $\hat{c} = 1$.
- **Bài 17 (Fibonacci Heap decreaseKey):** Amortized $\mathcal{O}(1)$ nhờ kỹ thuật "Cascading Cut" — Mỗi Node chỉ bị cắt tối đa 1 lần trước khi gắn lại. Chứng minh bằng Potential Method.
- **Bài 18 (Deque ArrayDeque Resize):** Circular Array nhân đôi khi Head gặp Tail. Amortized $\mathcal{O}(1)$, cơ chế giống Dynamic Array.
- **Bài 19 (Red-Black Tree Rebalance):** Mỗi Insert tốn tối đa 2 Rotations $\mathcal{O}(1)$. Mỗi Delete tốn tối đa 3 Rotations $\mathcal{O}(1)$. Recoloring tốn $\mathcal{O}(\log N)$ Worst-case nhưng Amortized $\mathcal{O}(1)$.
- **Bài 20 (Lazy Propagation trên Segment Tree):** Thao tác Range Update $\mathcal{O}(\log N)$ nhưng nếu tính tổng chuỗi truy vấn, Lazy Propagation rải đều chi phí cập nhật qua nhiều Query.
- **Bài 21 (Extendible Hashing):** Thay vì Rehash toàn bộ bảng, chỉ chia đôi (Split) 1 Bucket khi tràn. Chi phí Split $\mathcal{O}(\text{bucket size})$ rải đều cho các Insert.
- **Bài 22 (Move-to-Front Heuristic trên Linked List):** Khi truy cập một phần tử, di chuyển nó lên đầu danh sách. Amortized Analysis cho thấy nó chỉ tệ hơn Optimal $2\times$ (Competitive ratio).
- **Bài 23 (Treap Insert/Delete):** Amortized $\mathcal{O}(\log N)$ nhờ kết hợp Random Priority với BST Property. Không cần Rotation phức tạp như AVL.
- **Bài 24 (B-Tree Node Split):** Khi Node đầy, chia đôi và đẩy median lên cha. Chi phí Split $\mathcal{O}(B)$ (B là order) nhưng cần $B/2$ Insert mới trigger Split tiếp.
- **Bài 25 (Garbage Collector Amortization):** GC trẻ (Young Generation) chạy rất thường xuyên nhưng cực nhanh (Amortized cost thấp). GC già (Old Generation) chạy hiếm nhưng tốn kém (Amortized vẫn chấp nhận được).
- **Bài 26 (TCP Congestion Window):** Window size tăng tuyến tính (Additive Increase), giảm nhân đôi (Multiplicative Decrease). Amortized throughput hội tụ.
- **Bài 27 (LRU Cache Eviction):** `LinkedHashMap` với `removeEldestEntry()`. Mỗi `put()` tốn $\mathcal{O}(1)$ Amortized kể cả khi Eviction xảy ra.
- **Bài 28 (Buffered Writer Flush):** Ghi $N$ byte vào BufferedOutputStream. Flush xảy ra mỗi 8KB. Amortized I/O cost per byte: $\mathcal{O}(1)$.
- **Bài 29 (Dynamic Median Finder):** 2 Heap (Max-Heap + Min-Heap). Rebalancing khi chênh lệch kích thước $> 1$. Amortized Insert $\mathcal{O}(\log N)$, Find Median $\mathcal{O}(1)$.
- **Bài 30 (Incremental Rehashing như Redis):** Di chuyển $K$ Entry mỗi thao tác thay vì toàn bộ. Amortized vẫn $\mathcal{O}(1)$ nhưng Worst-case đơn lẻ giảm từ $\mathcal{O}(N)$ xuống $\mathcal{O}(K)$.
