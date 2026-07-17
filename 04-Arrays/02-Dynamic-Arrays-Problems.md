# Companion Problems: 02-Dynamic-Arrays

Tài liệu này cung cấp **30 bài tập thực hành** về Dynamic Array, đi sâu vào việc tự cài đặt, tối ưu hóa và giải quyết các bài toán thao tác mảng động.

---

## 1. Tự cài đặt Dynamic Array đơn giản
**Đề bài chi tiết:** Viết class `CustomArrayList` hỗ trợ `add(int val)`, `get(int index)`, `size()`. Mảng ban đầu kích thước 2. Tăng dung lượng gấp đôi (2x) khi đầy.
**Phân tích thuật toán:** Sử dụng mảng nội bộ `int[]`. Khởi tạo size = 0, capacity = 2. Trong hàm `add()`, kiểm tra `size == capacity`. Nếu đúng, tạo mảng mới cỡ `capacity * 2`, copy dữ liệu bằng `System.arraycopy`, gán lại mảng mới.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class CustomArrayList {
    private int[] data;
    private int size;

    public CustomArrayList() {
        data = new int[2];
        size = 0;
    }

    public void add(int val) {
        if (size == data.length) {
            int[] newData = new int[data.length * 2];
            System.arraycopy(data, 0, newData, 0, size);
            data = newData;
        }
        data[size++] = val;
    }

    public int get(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
        return data[index];
    }

    public int size() { return size; }
}
```
**Độ phức tạp:** `add` $\mathcal{O}(1)$ Amortized, `get` $\mathcal{O}(1)$.

---

## 2. Xóa phần tử tối ưu $\mathcal{O}(N)$
**Đề bài chi tiết:** Thêm hàm `remove(int index)` vào class trên. Giữ nguyên thứ tự các phần tử còn lại.
**Phân tích thuật toán:** Khi xóa phần tử ở `index`, ta phải dịch toàn bộ các phần tử từ `index + 1` đến `size - 1` sang trái 1 vị trí để lấp khoảng trống. Dùng `System.arraycopy(src, srcPos, dest, destPos, length)` cho tốc độ nhanh nhất.
**Mã nguồn Java:**
```java
public class ArrayListRemove {
    private int[] data = {10, 20, 30, 40, 50};
    private int size = 5;

    public int remove(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
        int removedValue = data[index];
        int numMoved = size - index - 1;
        
        if (numMoved > 0) {
            System.arraycopy(data, index + 1, data, index, numMoved);
        }
        size--;
        data[size] = 0; // Clear giá trị cũ (Không bắt buộc với mảng int, nhưng tốt)
        return removedValue;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ do phải dời phần tử. Space $\mathcal{O}(1)$.

---

## 3. Hàm Thu hẹp dung lượng (Trim to Size)
**Đề bài chi tiết:** Giả sử dung lượng mảng là 1000 nhưng size chỉ là 10. Viết hàm `trimToSize()` để thu hẹp `capacity` vừa đúng bằng `size` để giải phóng bộ nhớ.
**Phân tích thuật toán:** Kiểm tra nếu `size < data.length`, tạo mảng mới có dung lượng chính xác bằng `size`, copy toàn bộ `size` phần tử sang mảng mới, và thay thế mảng nội bộ.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class ArrayListTrim {
    private int[] data = new int[1000];
    private int size = 10;

    public void trimToSize() {
        if (size < data.length) {
            data = (size == 0) ? new int[0] : Arrays.copyOf(data, size);
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ (vì copy N phần tử), Space $\mathcal{O}(N)$ cho mảng mới.

---

## 4. Xóa HÀNG LOẠT (RemoveAll / Bulk Delete)
**Đề bài chi tiết:** Cho danh sách. Xóa tất cả các số chia hết cho 2. Yêu cầu: Thời gian $\mathcal{O}(N)$, Không gian $\mathcal{O}(1)$.
**Phân tích thuật toán:** Cách sai: Dùng vòng lặp gọi `remove(i)` $\rightarrow$ $\mathcal{O}(N^2)$. Tối ưu: Dùng Two Pointers (`writeIndex` và `readIndex`). Duyệt qua mảng: Nếu phần tử KHÔNG cần xóa, copy vào `data[writeIndex++]`. Cuối cùng cập nhật lại `size`.
**Mã nguồn Java:**
```java
public class BulkRemove {
    private int[] data = {1, 2, 3, 4, 5, 6, 7, 8};
    private int size = 8;

    public void removeEvens() {
        int writeIndex = 0;
        for (int readIndex = 0; readIndex < size; readIndex++) {
            if (data[readIndex] % 2 != 0) { // Giữ lại số lẻ
                data[writeIndex] = data[readIndex];
                writeIndex++;
            }
        }
        // Xóa rác ở phần cuối (tùy chọn với primitive)
        for (int i = writeIndex; i < size; i++) data[i] = 0;
        size = writeIndex;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Thêm HÀNG LOẠT (AddAll)
**Đề bài chi tiết:** Cho class `CustomArrayList`. Viết hàm `addAll(int[] elements)` để chèn toàn bộ mảng `elements` vào CUỐI danh sách hiện tại.
**Phân tích thuật toán:** Tính tổng kích thước `newSize = size + elements.length`. Nếu `newSize > capacity`, resize mảng nội bộ lên `newSize` (hoặc gấp rưỡi nếu gấp rưỡi đủ chứa). Dùng 1 lệnh `arraycopy` để đưa dữ liệu mới vào cuối.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class BulkAdd {
    private int[] data = new int[5];
    private int size = 2; // data = [1, 2, _, _, _]

    public void addAll(int[] elements) {
        int numNew = elements.length;
        if (numNew == 0) return;
        
        int newSize = size + numNew;
        if (newSize > data.length) {
            int newCapacity = Math.max(data.length + (data.length >> 1), newSize);
            data = Arrays.copyOf(data, newCapacity);
        }
        
        System.arraycopy(elements, 0, data, size, numNew);
        size = newSize;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M)$ với $M$ là chiều dài mảng thêm vào.

---

## 6. Iterator Fail-Fast
**Đề bài chi tiết:** Java `ArrayList` sẽ ném `ConcurrentModificationException` nếu mảng bị sửa đổi (add/remove) trong khi Iterator đang duyệt. Hãy implement cơ chế kiểm tra `modCount` này.
**Phân tích thuật toán:** Thêm biến `modCount` vào class. Mỗi khi `add()` hoặc `remove()` được gọi, `modCount++`. Khi tạo Iterator, lưu lại `expectedModCount = modCount`. Hàm `next()` của Iterator luôn kiểm tra `if (modCount != expectedModCount) throw ...`.
**Mã nguồn Java:**
```java
import java.util.ConcurrentModificationException;
import java.util.Iterator;

public class FailFastList implements Iterable<Integer> {
    private int[] data = {1, 2, 3};
    private int size = 3;
    private int modCount = 0;

    public void add(int val) {
        // (Omitted bounds check)
        data[size++] = val;
        modCount++; // Đánh dấu đã sửa đổi
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            int cursor = 0;
            int expectedModCount = modCount; // Lưu mốc lịch sử

            @Override
            public boolean hasNext() { return cursor < size; }

            @Override
            public Integer next() {
                if (modCount != expectedModCount) {
                    throw new ConcurrentModificationException();
                }
                return data[cursor++];
            }
        };
    }
}
```
**Độ phức tạp:** Phụ thu $\mathcal{O}(1)$ để kiểm tra trạng thái an toàn.

---

## 7. Dynamic Array thu hẹp tự động (Auto-Shrinking)
**Đề bài chi tiết:** Viết hàm `remove(int index)` sao cho nếu số phần tử (`size`) chỉ còn BẰNG HOẶC NHỎ HƠN $1/4$ dung lượng hiện tại, hãy thu hẹp `capacity` xuống một nửa ($1/2$).
**Phân tích thuật toán:** Thu hẹp tại mức $1/4$ thay vì $1/2$ để tránh hiện tượng "Thrashing" (Resize tăng/giảm liên tục khi size dao động quanh ngưỡng). Khi `size <= data.length / 4`, tạo mảng mới bằng một nửa mảng cũ.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class AutoShrinkList {
    private int[] data = new int[16];
    private int size = 4;

    public int remove(int index) {
        int val = data[index];
        System.arraycopy(data, index + 1, data, index, size - index - 1);
        size--;
        
        // Auto-shrink logic
        if (size > 0 && size <= data.length / 4) {
            int newCapacity = data.length / 2;
            data = Arrays.copyOf(data, newCapacity);
        }
        return val;
    }
}
```
**Độ phức tạp:** Thường là $\mathcal{O}(N)$ dời mảng. Amortized cho Shrink là $\mathcal{O}(1)$.

---

## 8. Custom Generics ArrayList
**Đề bài chi tiết:** Tạo cấu trúc `MyGenericList<E>`. Làm thế nào tạo mảng bên trong vì Java không cho phép `new E[capacity]`?
**Phân tích thuật toán:** Cách duy nhất do Type Erasure là dùng mảng `Object[]`, sau đó cast `(E)` khi lấy ra (hàm `get()`). Bỏ qua Warning bằng annotation `@SuppressWarnings("unchecked")`.
**Mã nguồn Java:**
```java
public class MyGenericList<E> {
    private Object[] data;
    private int size;

    public MyGenericList() {
        data = new Object[10];
    }

    public void add(E element) {
        // (Omitted grow logic)
        data[size++] = element;
    }

    @SuppressWarnings("unchecked")
    public E get(int index) {
        return (E) data[index]; // Cast từ Object về E
    }
    
    public void removeLast() {
        if (size > 0) {
            size--;
            data[size] = null; // BẮT BUỘC để tránh Memory Leak (Obsolete reference)
        }
    }
}
```
**Độ phức tạp:** Getter/Setter $\mathcal{O}(1)$. Memory Leak prevented by clearing references.

---

## 9. Dynamic Circular Buffer (Array Deque)
**Đề bài chi tiết:** `ArrayList` bình thường chèn ở đầu mất $\mathcal{O}(N)$. Sửa cấu trúc mảng để chèn ở đầu O(1), mà vẫn duy trì khả năng tự mở rộng.
**Phân tích thuật toán:** Dùng 2 con trỏ `head` và `tail`. Mảng vòng tròn (Circular Buffer). Khi chèn đầu, `head = (head - 1 + capacity) % capacity`. Khi đầy `(size == capacity)`, tạo mảng mới, chép thẳng từ `head` tới cuối, rồi chép phần bị vòng quay trở về đầu (Unroll). Dữ liệu mảng mới sẽ nằm thẳng hàng từ 0.
**Mã nguồn Java:**
```java
public class ArrayDequeCustom {
    private int[] data = new int[4];
    private int head = 0;
    private int size = 0;

    public void addFirst(int val) {
        if (size == data.length) grow();
        head = (head - 1 + data.length) % data.length;
        data[head] = val;
        size++;
    }

    private void grow() {
        int cap = data.length;
        int[] newData = new int[cap * 2];
        // Copy từ head đến cuối
        int rightLen = cap - head;
        System.arraycopy(data, head, newData, 0, rightLen);
        // Copy từ 0 đến head
        System.arraycopy(data, 0, newData, rightLen, head);
        data = newData;
        head = 0;
    }
}
```
**Độ phức tạp:** `addFirst()` $\mathcal{O}(1)$ Amortized.

---

## 10. Thread-safe Dynamic Array với Copy-On-Write
**Đề bài chi tiết:** Tạo mảng động an toàn cho đa luồng, hỗ trợ đọc $\mathcal{O}(1)$ không lock, ghi tốn thời gian $\mathcal{O}(N)$.
**Phân tích thuật toán:** Pattern `CopyOnWriteArrayList`. Khai báo mảng nội bộ `volatile`. Mỗi lần gọi hàm `add()` hay `remove()`, sử dụng Lock (hoặc `synchronized`), nhân bản toàn bộ mảng cũ sang mảng mới, thay đổi trên mảng mới, rồi gán reference trở lại. Đọc `get()` diễn ra trực tiếp trên mảng cũ không chặn.
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.concurrent.locks.ReentrantLock;

public class ThreadSafeList {
    private volatile Object[] array = new Object[0];
    private final ReentrantLock lock = new ReentrantLock();

    public Object get(int index) {
        return array[index]; // Không cần lock
    }

    public void add(Object val) {
        lock.lock();
        try {
            Object[] elements = array;
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1); // Copy toàn bộ
            newElements[len] = val;
            array = newElements; // Thay thế atomic reference
        } finally {
            lock.unlock();
        }
    }
}
```
**Độ phức tạp:** Get $\mathcal{O}(1)$. Add/Remove $\mathcal{O}(N)$. Dùng cho kịch bản nhiều Đọc, cực ít Ghi.

---

## 11. Xóa phần tử trùng lặp tại chỗ (Remove Duplicates In-Place)
**Đề bài chi tiết:** Cho một `ArrayList` đã được sắp xếp tăng dần. Hãy xóa các phần tử trùng lặp sao cho mỗi phần tử chỉ xuất hiện duy nhất một lần. Yêu cầu không tạo mảng phụ (In-Place) với không gian bộ nhớ phụ $\mathcal{O}(1)$ và thời gian $\mathcal{O}(N)$.
**Phân tích thuật toán:** Sử dụng kỹ thuật Two Pointers với con trỏ `slow` (chỉ vào vị trí cần ghi đè tiếp theo) và `fast` (duyệt qua toàn bộ mảng). Bắt đầu từ vị trí 1, so sánh phần tử tại `fast` với phần tử tại `slow - 1` (hoặc so sánh phần tử hiện tại với phần tử liền trước). Nếu chúng khác nhau, sao chép giá trị tại `fast` vào `slow` và tăng `slow`.
**Mã nguồn Java:**
```java
public class RemoveDuplicates {
    private int[] data = {1, 1, 2, 2, 2, 3, 4, 4, 5};
    private int size = 9;

    public void removeDuplicates() {
        if (size <= 1) return;
        int slow = 1;
        for (int fast = 1; fast < size; fast++) {
            if (data[fast] != data[fast - 1]) {
                data[slow] = data[fast];
                slow++;
            }
        }
        // Xóa các tham chiếu cũ (tùy chọn với mảng int)
        for (int i = slow; i < size; i++) data[i] = 0;
        size = slow;
    }
}
```
**Độ phức tạp:** Thời gian (Time) $\mathcal{O}(N)$ vì chỉ duyệt qua mảng một lần. Không gian (Space) $\mathcal{O}(1)$ vì thao tác trực tiếp trên mảng gốc (In-Place).

---

## 12. So sánh hai ArrayList (ArrayList equals)
**Đề bài chi tiết:** Viết hàm `equals(Object obj)` để kiểm tra nội dung của danh sách hiện tại có giống hệt một danh sách khác hay không (cùng kích thước và các phần tử tương ứng bằng nhau).
**Phân tích thuật toán:** Trước tiên kiểm tra xem hai tham chiếu có trỏ đến cùng một đối tượng (Reference check) hay không. Sau đó, kiểm tra Null và kiểu dữ liệu. Tiếp theo kiểm tra kích thước `size`. Cuối cùng, duyệt qua từng phần tử tương ứng của cả hai mảng; nếu có bất kỳ phần tử nào khác nhau, trả về `false`.
**Mã nguồn Java:**
```java
public class MyArrayListEquals {
    private Object[] data;
    private int size;

    public MyArrayListEquals(Object[] elements) {
        this.data = elements.clone();
        this.size = elements.length;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true; // Cùng tham chiếu
        if (obj == null || getClass() != obj.getClass()) return false;
        
        MyArrayListEquals other = (MyArrayListEquals) obj;
        if (this.size != other.size) return false; // Khác kích thước
        
        for (int i = 0; i < size; i++) {
            Object o1 = this.data[i];
            Object o2 = other.data[i];
            if (o1 == null ? o2 != null : !o1.equals(o2)) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ nếu hai mảng giống nhau, $\mathcal{O}(1)$ nếu kích thước khác nhau. Space $\mathcal{O}(1)$.

---

## 13. Khung nhìn mảng con (SubList View)
**Đề bài chi tiết:** Viết hàm `subList(int fromIndex, int toIndex)` trả về một danh sách ảo đại diện cho một khoảng phần tử trong danh sách gốc. Bất kỳ thay đổi nào (như `set()`) trên danh sách ảo đều phải ánh xạ trực tiếp lên danh sách gốc, không tạo bản sao mảng mới.
**Phân tích thuật toán:** Tạo một class nội bộ `SubList` tham chiếu trực tiếp đến `ArrayList` gốc. Ghi đè các phương thức (như `get` và `set`) bằng cách dịch chuyển `index` thêm khoảng `fromIndex`. Cần kiểm tra kỹ lưỡng các lỗi RangeCheck.
**Mã nguồn Java:**
```java
public class ViewArrayList {
    private int[] data = {10, 20, 30, 40, 50};
    private int size = 5;

    public SubList subList(int fromIndex, int toIndex) {
        if (fromIndex < 0 || toIndex > size || fromIndex > toIndex) {
            throw new IndexOutOfBoundsException();
        }
        return new SubList(this, fromIndex, toIndex - fromIndex);
    }

    class SubList {
        private ViewArrayList parent;
        private int offset;
        private int subSize;

        public SubList(ViewArrayList parent, int offset, int size) {
            this.parent = parent;
            this.offset = offset;
            this.subSize = size;
        }

        public int get(int index) {
            if (index < 0 || index >= subSize) throw new IndexOutOfBoundsException();
            return parent.data[offset + index];
        }

        public void set(int index, int val) {
            if (index < 0 || index >= subSize) throw new IndexOutOfBoundsException();
            parent.data[offset + index] = val; // Cập nhật trực tiếp lên gốc
        }
    }
}
```
**Độ phức tạp:** Tạo SubList mất Time $\mathcal{O}(1)$, Space $\mathcal{O}(1)$. Các thao tác `get`/`set` qua SubList tốn $\mathcal{O}(1)$.

---

## 14. Nhân bản danh sách (ArrayList Clone)
**Đề bài chi tiết:** Triển khai hàm `clone()` cho `ArrayList` để trả về một bản sao chép hời (Shallow Copy) của danh sách. Phân tích khác biệt so với sao chép sâu (Deep Copy).
**Phân tích thuật toán:** Shallow copy chỉ sao chép mảng chứa các tham chiếu (References), nghĩa là mảng nội bộ mới được tạo nhưng các object bên trong vẫn là tham chiếu cũ. Khi dùng `clone()`, tạo đối tượng mảng mới và dùng `System.arraycopy` hoặc `Arrays.copyOf` để chép tham chiếu.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class CloneableArrayList implements Cloneable {
    private Object[] data;
    private int size;

    public CloneableArrayList(int capacity) {
        data = new Object[capacity];
    }

    @Override
    public Object clone() {
        try {
            CloneableArrayList cloned = (CloneableArrayList) super.clone();
            // Cần copy mảng nội bộ để đảm bảo an toàn cho danh sách (Shallow Copy)
            cloned.data = Arrays.copyOf(this.data, this.size);
            return cloned;
        } catch (CloneNotSupportedException e) {
            throw new InternalError(e);
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ để copy mảng tham chiếu. Space $\mathcal{O}(N)$ cho mảng nội bộ mới. **Lưu ý:** Shallow Copy không tạo đối tượng mới bên trong mảng, nên sửa object sẽ ảnh hưởng cả 2 bên.

---

## 15. So sánh Clear vs Gán new ArrayList
**Đề bài chi tiết:** Để làm rỗng một mảng động, nên dùng `list.clear()` hay `list = new ArrayList<>()`? Hãy mô phỏng mã hàm `clear()` và đưa ra nhận xét.
**Phân tích thuật toán:** 
- `clear()`: Đặt lại `size = 0`, gán `null` cho các phần tử để Garbage Collector (GC) thu hồi bộ nhớ, giữ nguyên `capacity` (tránh cấp phát lại bộ nhớ sau này). 
- `new ArrayList<>()`: Đòi hỏi JVM tạo Object List và mảng nội bộ hoàn toàn mới, đồng thời mảng cũ và List cũ phải chờ GC gom rác.
**Mã nguồn Java:**
```java
public class ClearComparison {
    private Object[] data = new Object[100];
    private int size = 50;

    // Giữ nguyên dung lượng (capacity), chỉ dọn giá trị
    public void clear() {
        for (int i = 0; i < size; i++) {
            data[i] = null; // Help Garbage Collector
        }
        size = 0;
    }
    
    // Nếu dùng: list = new ArrayList<>();
    // Hệ thống sẽ bỏ rơi mảng cũ, tốn tài nguyên cấp phát mảng mới.
}
```
**Độ phức tạp:** Hàm `clear()` có Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$. Tuy nhiên `clear()` hoạt động nhanh, ít tạo GC pause hơn và phù hợp khi tái sử dụng danh sách.

---

## 16. Chèn hàng loạt tại vị trí (AddAll at Index)
**Đề bài chi tiết:** Viết hàm `addAll(int index, int[] elements)` chèn toàn bộ mảng `elements` vào vị trí `index` trong Dynamic Array, đẩy các phần tử phía sau sang phải.
**Phân tích thuật toán:** Tính tổng kích thước `newSize`. Đảm bảo `capacity` đủ chứa `newSize`. Dùng `System.arraycopy` dời đoạn từ `index` sang vị trí mới cách đó một khoảng bằng `elements.length`. Sau đó, dùng tiếp `System.arraycopy` thứ hai để copy `elements` vào khoảng trống vừa tạo.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class InsertAllList {
    private int[] data = {1, 2, 5, 6};
    private int size = 4;

    public void addAll(int index, int[] elements) {
        if (index < 0 || index > size) throw new IndexOutOfBoundsException();
        int numNew = elements.length;
        if (numNew == 0) return;

        int newSize = size + numNew;
        if (newSize > data.length) {
            data = Arrays.copyOf(data, Math.max(data.length * 2, newSize));
        }

        // Dời các phần tử hiện tại sang phải
        int numMoved = size - index;
        if (numMoved > 0) {
            System.arraycopy(data, index, data, index + numNew, numMoved);
        }

        // Chèn mảng mới vào vị trí trống
        System.arraycopy(elements, 0, data, index, numNew);
        size = newSize;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N + M)$ trong đó $N$ là số lượng phần tử cần dời (Shift), $M$ là độ dài mảng cần thêm. Space $\mathcal{O}(1)$ nếu không cần tăng capacity.

---

## 17. Giữ lại phần tử theo điều kiện (Keep Only If / Retain)
**Đề bài chi tiết:** Tương tự như `removeIf(Predicate)`, hãy viết hàm giữ lại các phần tử thỏa mãn một điều kiện nhất định và xóa phần còn lại (In-Place) với $\mathcal{O}(N)$.
**Phân tích thuật toán:** Cách hiệu quả nhất để làm việc này mà không phải dịch chuyển phần tử liên tục là dùng hai con trỏ (Two Pointers). Con trỏ `read` duyệt qua mảng, nếu phần tử thỏa mãn điều kiện thì copy vào vị trí con trỏ `write` và tăng `write`. Sau cùng, cập nhật `size = write`.
**Mã nguồn Java:**
```java
import java.util.function.Predicate;

public class KeepOnlyIfList<E> {
    private Object[] data;
    private int size;
    
    public KeepOnlyIfList(Object[] initial) {
        this.data = initial;
        this.size = initial.length;
    }

    public void retainIf(Predicate<E> condition) {
        int write = 0;
        for (int read = 0; read < size; read++) {
            @SuppressWarnings("unchecked")
            E element = (E) data[read];
            
            if (condition.test(element)) { // Nếu thỏa mãn thì giữ lại
                data[write] = data[read];
                write++;
            }
        }
        
        // Clear rác để tránh memory leak
        for (int i = write; i < size; i++) {
            data[i] = null; 
        }
        size = write;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (In-place).

---

## 18. Mô phỏng tăng dung lượng: 2.0 vs 1.5 Benchmark
**Đề bài chi tiết:** So sánh việc tăng gấp đôi (2.0x) và tăng gấp rưỡi (1.5x) mảng khi đầy. Viết hàm mô phỏng tính tổng số phần tử bị copy khi chèn $N$ phần tử từ mảng trống ban đầu có capacity = 10.
**Phân tích thuật toán:** Thuật toán duyệt qua $N$ lần chèn. Biến `size` cứ tăng. Bất cứ khi nào `size == capacity`, cập nhật `capacity` và cộng `size` vào `totalCopies`. Hệ số 2.0 copy ít lần hơn nhưng tốn nhiều bộ nhớ chưa sử dụng, hệ số 1.5 cân bằng tốt hơn (được dùng trong Java `ArrayList`).
**Mã nguồn Java:**
```java
public class ResizeBenchmark {
    public static void simulateGrowth(int n, double factor) {
        int capacity = 10;
        int size = 0;
        long totalCopies = 0;

        for (int i = 0; i < n; i++) {
            if (size == capacity) {
                totalCopies += size; // Copy size phần tử sang mảng mới
                capacity = (int) (capacity * factor);
                // Xử lý làm tròn của factor 1.5
                if (capacity == size) capacity++; 
            }
            size++;
        }
        System.out.println("Factor " + factor + " -> Total elements copied: " + totalCopies);
        System.out.println("Final Capacity: " + capacity);
    }

    public static void main(String[] args) {
        simulateGrowth(1_000_000, 2.0); // Copy ít hơn
        simulateGrowth(1_000_000, 1.5); // Copy nhiều hơn một chút, tiết kiệm RAM
    }
}
```
**Độ phức tạp:** Thời gian mô phỏng $\mathcal{O}(N)$, bộ nhớ $\mathcal{O}(1)$. Amortized Time (Trung bình) của mỗi lần `add` luôn là $\mathcal{O}(1)$.

---

## 19. Giới hạn kích thước lớn nhất của mảng
**Đề bài chi tiết:** Giải thích vì sao Java thường giới hạn kích thước mảng là `Integer.MAX_VALUE - 8` thay vì `Integer.MAX_VALUE`. Giả lập lỗi `OutOfMemoryError: Requested array size exceeds VM limit`.
**Phân tích thuật toán:** Các Máy ảo Java (JVM) cần dùng một vài byte ở phần đầu (Object Header) mảng để lưu thông tin về mảng (như độ dài mảng, kiểu dữ liệu, v.v.). Nếu cố gắng cấp phát đến giới hạn tối đa `Integer.MAX_VALUE`, phần header bị chèn ép dẫn đến lỗi. OpenJDK định nghĩa hằng số này là `MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8`.
**Mã nguồn Java:**
```java
public class MaxArraySizeLimit {
    public static void main(String[] args) {
        int maxSafeSize = Integer.MAX_VALUE - 8;
        
        try {
            // JVM phụ thuộc, có thể tạo thành công nếu đủ Heap (-Xmx)
            byte[] safeArray = new byte[maxSafeSize]; 
            System.out.println("Tạo an toàn mảng kích thước MAX_VALUE - 8.");
        } catch (OutOfMemoryError e) {
            System.out.println("Không đủ RAM Heap để tạo mảng.");
        }

        try {
            // Thử vượt quá giới hạn an toàn VM Limit
            byte[] overLimitArray = new byte[Integer.MAX_VALUE - 1];
        } catch (OutOfMemoryError e) {
            // Sẽ throw "Requested array size exceeds VM limit" ngay lập tức
            System.out.println("Lỗi: " + e.getMessage());
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$ để ném lỗi giới hạn VM Limit. Yêu cầu bộ nhớ rất lớn $\mathcal{O}(N)$ để lưu trữ dữ liệu nếu cấp phát thành công.

---

## 20. Chi phí bộ nhớ của Wrapper Type (Primitive Wrapper Overhead)
**Đề bài chi tiết:** Một mảng `int[]` nguyên thủy và `ArrayList<Integer>` chứa số nguyên khác nhau thế nào về bộ nhớ? Phân tích tại sao dùng `ArrayList<Integer>` chứa 1 triệu phần tử tốn RAM hơn.
**Phân tích thuật toán:** Một phần tử `int` tốn đúng 4 bytes. Nhưng `Integer` là một Object (tốn 16 bytes Object Header + 4 bytes dữ liệu + 4 bytes Padding = 24 bytes). Ngoài ra mảng nội bộ của ArrayList chứa tham chiếu object (tốn thêm 4 bytes/8 bytes mỗi tham chiếu). Kết quả: `ArrayList<Integer>` tốn RAM gấp 6 đến 7 lần so với `int[]`.
**Mã nguồn Java:**
```java
// Giả mã minh họa (thường dùng Java Object Layout (JOL) library để đo chính xác)
public class WrapperOverhead {
    public static void main(String[] args) {
        int n = 1_000_000;

        // Mảng primitive: Chỉ tốn (1M * 4 bytes) + 16 bytes header = ~4 MB
        int[] primitiveArray = new int[n];

        // ArrayList<Integer>: 
        // 1. Mảng tham chiếu nội bộ: 1M * 4 bytes (Compressed OOP) = ~4 MB
        // 2. Một triệu đối tượng Integer: 1M * 24 bytes (Header + Payload) = ~24 MB
        // Tổng tốn: ~28 MB. (Gấp 7 lần primitive)
        Integer[] objectArray = new Integer[n];
        for (int i = 0; i < n; i++) {
            objectArray[i] = Integer.valueOf(i);
        }
        
        System.out.println("int[] dùng 4MB. Integer[] dùng 28MB.");
    }
}
```
**Độ phức tạp:** Time khởi tạo mảng Object $\mathcal{O}(N)$, kèm chi phí rất lớn liên quan đến cấp phát bộ nhớ (Garbage Collector workload).

---

## 21. Giao của hai danh sách (List RetainAll / Intersection)
**Đề bài chi tiết:** Cho hai danh sách động, hãy giữ lại những phần tử của danh sách thứ nhất mà có xuất hiện trong danh sách thứ hai, xóa những phần tử không có mặt. Yêu cầu thời gian $\mathcal{O}(N)$ thay vì $\mathcal{O}(N \times M)$.
**Phân tích thuật toán:** Nếu dùng 2 vòng lặp lồng nhau sẽ tốn $\mathcal{O}(N \times M)$. Bằng cách chuyển đổi danh sách thứ hai thành `HashSet`, việc kiểm tra sự tồn tại mất $\mathcal{O}(1)$. Kết hợp với kỹ thuật Two Pointers In-Place để giữ lại các phần tử thỏa mãn.
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Collection;

public class RetainAllList {
    private Object[] data = {1, 2, 3, 4, 5};
    private int size = 5;

    public void retainAll(Collection<?> c) {
        HashSet<Object> set = new HashSet<>(c);
        int write = 0;
        
        for (int read = 0; read < size; read++) {
            if (set.contains(data[read])) {
                data[write++] = data[read];
            }
        }
        
        for (int i = write; i < size; i++) {
            data[i] = null; // Ngăn rò rỉ bộ nhớ
        }
        size = write;
    }
}
```
**Độ phức tạp:** Thời gian (Time) $\mathcal{O}(N + M)$ để duyệt 2 collection. Không gian (Space) $\mathcal{O}(M)$ để tạo Hashing Set.

---

## 22. Chi phí đồng bộ của Vector (Vector Synchronized Overhead)
**Đề bài chi tiết:** Viết mã đo lường và so sánh hiệu suất giữa `java.util.Vector` và `java.util.ArrayList` khi thêm liên tục $10^7$ phần tử trong môi trường đơn luồng (Single-thread).
**Phân tích thuật toán:** `Vector` trong Java là Thread-safe; mọi thao tác như `add()` hay `get()` đều mang từ khóa `synchronized`. Trong môi trường đơn luồng, quá trình kiểm tra lock liên tục gây ra chi phí lớn không cần thiết (Overhead). `ArrayList` không có từ khóa `synchronized` nên nhanh hơn nhiều.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Vector;

public class VectorVsArrayList {
    public static void main(String[] args) {
        int n = 10_000_000;
        
        long start1 = System.currentTimeMillis();
        Vector<Integer> vector = new Vector<>();
        for (int i = 0; i < n; i++) vector.add(i);
        long end1 = System.currentTimeMillis();
        
        long start2 = System.currentTimeMillis();
        ArrayList<Integer> list = new ArrayList<>();
        for (int i = 0; i < n; i++) list.add(i);
        long end2 = System.currentTimeMillis();
        
        System.out.println("Vector time: " + (end1 - start1) + "ms");
        System.out.println("ArrayList time: " + (end2 - start2) + "ms");
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ cho vòng lặp, tuy nhiên thời gian thực thi của `ArrayList` thường nhỏ hơn `Vector` khoảng 30% đến 50%.

---

## 23. Chuyển đổi ArrayList thành Mảng (ArrayList toArray)
**Đề bài chi tiết:** Cài đặt hai phiên bản của hàm `toArray()` cho `ArrayList` giống như OpenJDK: Một trả về `Object[]` và một trả về mảng kiểu Generic `T[]` an toàn (Type-safe).
**Phân tích thuật toán:** Hàm `Object[] toArray()` dùng `Arrays.copyOf` để tạo mảng mới. Hàm `<T> T[] toArray(T[] a)` kiểm tra xem mảng truyền vào có đủ chỗ chứa không. Nếu đủ, chép vào mảng đó; nếu thiếu, dùng Reflection để tạo mảng mới cùng kiểu và trả về.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class ToArrayList<E> {
    private Object[] data = {1, 2, 3};
    private int size = 3;

    public Object[] toArray() {
        return Arrays.copyOf(data, size);
    }

    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size) {
            // Cấp phát mảng mới bằng Reflection dựa trên kiểu của mảng a
            return (T[]) Arrays.copyOf(data, size, a.getClass());
        }
        System.arraycopy(data, 0, a, 0, size);
        if (a.length > size) {
            a[size] = null; // Đánh dấu điểm kết thúc (Chuẩn Collection API)
        }
        return a;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ để tạo và sao chép. Space $\mathcal{O}(N)$ (nếu cần mảng mới).

---

## 24. Yêu cầu cấp phát trước (Ensure Capacity)
**Đề bài chi tiết:** Để tối ưu việc thêm hàng loạt phần tử, hãy viết hàm `ensureCapacity(int minCapacity)` giúp mảng động tự mở rộng đến kích thước mong muốn chỉ với 1 lần phân bổ bộ nhớ duy nhất, tránh việc tự động resize nhiều lần.
**Phân tích thuật toán:** Kiểm tra xem `minCapacity` có vượt qua kích thước vật lý hiện tại của mảng (`data.length`) hay không. Nếu có, tăng mảng bằng cách tạo mảng mới có dung lượng ít nhất bằng `minCapacity`.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class EnsureCapacityList {
    private Object[] data = new Object[10];
    private int size = 0;

    public void ensureCapacity(int minCapacity) {
        if (minCapacity > data.length) {
            // Thường Java sẽ cho tăng gấp rưỡi, nhưng phải đảm bảo ít nhất = minCapacity
            int newCapacity = Math.max(data.length + (data.length >> 1), minCapacity);
            data = Arrays.copyOf(data, newCapacity);
        }
    }

    // Người dùng có thể gọi hàm trước vòng lặp lớn
    public void bulkInsertMock() {
        int n = 1_000_000;
        ensureCapacity(size + n); // Chỉ cấp phát 1 lần!
        for(int i = 0; i < n; i++) {
            data[size++] = i;
        }
    }
}
```
**Độ phức tạp:** Hàm tốn $\mathcal{O}(N)$ để cấp phát mảng, nhưng khi chèn hàng loạt phần tử sau đó sẽ là thuần $\mathcal{O}(1)$ cho mỗi phần tử, giảm thiểu số lần Garbage Collection.

---

## 25. Xóa phần tử an toàn qua Iterator (Iterator Remove)
**Đề bài chi tiết:** Cài đặt Iterator cho mảng động, bao gồm phương thức `remove()` để xóa phần tử đang duyệt một cách an toàn mà không bị vướng ngoại lệ `ConcurrentModificationException`.
**Phân tích thuật toán:** Iterator phải duy trì một biến `lastRet` để biết phần tử nào vừa được gọi qua hàm `next()`. Khi `remove()` được gọi, nó gọi đến hàm `remove` của danh sách mẹ ở vị trí `lastRet`. Để tránh lỗi thay đổi đồng thời, ta cần đồng bộ biến `expectedModCount = modCount` ngay sau khi xóa.
**Mã nguồn Java:**
```java
import java.util.ConcurrentModificationException;
import java.util.Iterator;

public class IteratorRemoveList implements Iterable<Integer> {
    private int[] data = {10, 20, 30, 40};
    private int size = 4;
    private int modCount = 0;

    private void removeIndex(int index) {
        System.arraycopy(data, index + 1, data, index, size - index - 1);
        size--;
        modCount++;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            int cursor = 0;
            int lastRet = -1;
            int expectedModCount = modCount;

            @Override
            public boolean hasNext() { return cursor < size; }

            @Override
            public Integer next() {
                if (modCount != expectedModCount) throw new ConcurrentModificationException();
                lastRet = cursor;
                return data[cursor++];
            }

            @Override
            public void remove() {
                if (lastRet < 0) throw new IllegalStateException();
                if (modCount != expectedModCount) throw new ConcurrentModificationException();
                
                removeIndex(lastRet);
                cursor = lastRet; // Trở lùi con trỏ vì phần tử sau đã dồn lên
                lastRet = -1; // Ngăn gọi remove() 2 lần liên tiếp
                expectedModCount = modCount; // Đồng bộ lại!
            }
        };
    }
}
```
**Độ phức tạp:** Time của thao tác `remove()` vẫn là $\mathcal{O}(N)$ do dịch chuyển mảng mẹ. Space $\mathcal{O}(1)$.

---

## 26. Hiệu suất các phương thức duyệt List (Iteration Performance)
**Đề bài chi tiết:** So sánh các cách duyệt ArrayList trong Java: Vòng lặp `for-i`, `for-each` (Iterator), và `forEach(Lambda)`. Cái nào mang lại hiệu suất tốt nhất và tại sao?
**Phân tích thuật toán:** 
- Vòng lặp `for (int i=0; i<size; i++) get(i)`: Trực tiếp truy cập mảng bên trong qua chỉ số, máy ảo JVM dễ dàng tối ưu hóa (Loop Unrolling, Bounds Check Elimination), mang lại tốc độ **nhanh nhất**.
- Vòng lặp `for-each`: Bị ép khởi tạo đối tượng `Iterator`, tốn chi phí gọi phương thức `hasNext()` và `next()`, kiểm tra `modCount`. Tốc độ khá chậm so với mảng thuần.
- Hàm `forEach(Lambda)`: Tốc độ tương đối ổn nhưng có chi phí gọi Interface Method, không thể dùng lệnh `break` hoặc `continue`.
**Mã nguồn Java:**
```java
import java.util.ArrayList;

public class IterationPerformance {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        // Giả sử add 1_000_000 phần tử

        // Nhanh nhất (Nên dùng cho ArrayList)
        long sum1 = 0;
        for (int i = 0; i < list.size(); i++) {
            sum1 += list.get(i);
        }

        // Chậm hơn một chút (Sinh đối tượng Iterator)
        long sum2 = 0;
        for (Integer val : list) {
            sum2 += val;
        }

        // Hiện đại, khó gỡ lỗi (Debugging), overhead invoke interface
        final long[] sum3 = {0};
        list.forEach(val -> sum3[0] += val);
    }
}
```
**Độ phức tạp:** Cả 3 đều là Time $\mathcal{O}(N)$, nhưng khác nhau lớn về Hằng số thời gian thực thi (Constant factor overhead) trong thực tế.

---

## 27. Mảng động nén - Boolean (Compact BitArrayList)
**Đề bài chi tiết:** `ArrayList<Boolean>` rất tốn dung lượng do cấu trúc Object. Tự cài đặt một `BitArrayList` tối ưu dùng mảng kiểu `long[]`, nơi mỗi biến `long` lưu trữ được 64 giá trị boolean.
**Phân tích thuật toán:** Dùng các toán tử thao tác bit (Bitwise). 
Vị trí trong mảng `long` (word index) là `index / 64` (hoặc `index >> 6`).
Vị trí bit (bit offset) trong số `long` là `index % 64` (hoặc `index & 63`).
Lấy giá trị (Get): Dùng AND bit và Shift.
Thiết lập (Set): Dùng OR bit hoặc AND-NOT.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class BitArrayList {
    private long[] words = new long[1];
    private int size = 0;

    public void add(boolean val) {
        int wordIndex = size >> 6; // size / 64
        if (wordIndex >= words.length) {
            words = Arrays.copyOf(words, words.length * 2);
        }
        if (val) {
            words[wordIndex] |= (1L << (size & 63));
        }
        size++;
    }

    public boolean get(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
        int wordIndex = index >> 6;
        int bitOffset = index & 63;
        return (words[wordIndex] & (1L << bitOffset)) != 0;
    }
}
```
**Độ phức tạp:** Time `add`/`get` $\mathcal{O}(1)$. Tiết kiệm không gian cực tốt: $N$ phần tử chỉ tốn $N/8$ bytes thay vì $N \times 24$ bytes như `ArrayList<Boolean>`.

---

## 28. Mảng động phân mảnh (Chunked Dynamic Array)
**Đề bài chi tiết:** Việc copy toàn bộ danh sách khi resize gây tắc nghẽn cục bộ. Đề xuất cấu trúc Chunked Array (giống `std::deque` trong C++): quản lý danh sách bằng một mảng chứa các "khối" (blocks) có độ lớn cố định.
**Phân tích thuật toán:** Dùng `ArrayList<Object[]>`. Mỗi mảng khối nhỏ (block) có size cố định là 1024.
Khi `add` phần tử thứ 1025, thay vì tạo mảng 2048 và copy 1024 phần tử cũ, ta chỉ cần tạo một mảng khối 1024 mới và chèn nó vào mảng chứa gốc.
Truy cập qua phép chia `index / 1024` và modulo `index % 1024`.
**Mã nguồn Java:**
```java
import java.util.ArrayList;

public class ChunkedList<E> {
    private static final int CHUNK_SIZE = 1024;
    private ArrayList<Object[]> chunks = new ArrayList<>();
    private int size = 0;

    public void add(E val) {
        int chunkIndex = size / CHUNK_SIZE;
        int elementIndex = size % CHUNK_SIZE;

        if (chunkIndex == chunks.size()) {
            chunks.add(new Object[CHUNK_SIZE]); // Sinh thêm khối (Chunk) mới, KHÔNG copy khối cũ
        }
        chunks.get(chunkIndex)[elementIndex] = val;
        size++;
    }

    @SuppressWarnings("unchecked")
    public E get(int index) {
        if (index < 0 || index >= size) throw new IndexOutOfBoundsException();
        int chunkIndex = index / CHUNK_SIZE;
        int elementIndex = index % CHUNK_SIZE;
        return (E) chunks.get(chunkIndex)[elementIndex];
    }
}
```
**Độ phức tạp:** Time `add` thuần $\mathcal{O}(1)$, không hề có độ trễ Amortized. `get` mất $\mathcal{O}(1)$ với chi phí phép chia nhẹ. Tránh được việc GC dọn mảng siêu lớn.

---

## 29. Tối ưu hóa Tuần tự hóa (Custom Serialization)
**Đề bài chi tiết:** `ArrayList` chứa `data` với `capacity = 1000` nhưng `size = 10`. Nếu gửi qua mạng, serialize cả mảng 1000 rất tốn kém. Triển khai phương thức `writeObject` và `readObject` giúp loại bỏ vùng không gian trống.
**Phân tích thuật toán:** Đánh dấu mảng nội bộ là `transient Object[] data` để JVM không tự động serialize nó. Viết hàm `writeObject` tùy biến: Lưu `size` trước, sau đó lặp đúng `size` phần tử và ghi chúng. Tại hàm `readObject`, đọc `size`, sau đó khởi tạo mảng vừa vặn và đọc dữ liệu.
**Mã nguồn Java:**
```java
import java.io.*;

public class SerializedList implements Serializable {
    // transient: Bỏ qua Serialization tự động của Java
    private transient Object[] data;
    private int size;

    public SerializedList(int capacity) {
        data = new Object[capacity];
        size = 0;
    }

    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject(); // Ghi các trường non-transient (như size)
        
        out.writeInt(data.length); // Gửi cả dung lượng (nếu muốn) hoặc chỉ cần thiết
        for (int i = 0; i < size; i++) {
            out.writeObject(data[i]); // Chỉ ghi những ô chứa dữ liệu
        }
    }

    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        in.defaultReadObject(); // Đọc biến size
        
        int capacity = in.readInt();
        data = new Object[capacity]; // Cấp phát vùng nhớ mới an toàn
        for (int i = 0; i < size; i++) {
            data[i] = in.readObject();
        }
    }
}
```
**Độ phức tạp:** Giảm Time Serialization và Space Network từ $\mathcal{O}(Capacity)$ xuống còn $\mathcal{O}(Size)$. Rất quan trọng khi $Capacity \gg Size$.

---

## 30. Mảng động 2 chiều (Dynamic 2D Array)
**Đề bài chi tiết:** Cấu trúc mảng hai chiều trong Java là danh sách các mảng 1 chiều. Hãy triển khai cấu trúc động đa chiều sử dụng danh sách các danh sách: `ArrayList<ArrayList<Integer>>` còn được gọi là mảng răng cưa (Ragged array).
**Phân tích thuật toán:** Một `ArrayList` ngoài quản lý hàng (Row). Tại mỗi hàng lại chứa một `ArrayList` nội bộ quản lý các cột (Column) của hàng đó. Số cột của mỗi hàng không cần bằng nhau. Để thêm hàng hay phần tử, ta cần trỏ đúng `Row Index`.
**Mã nguồn Java:**
```java
import java.util.ArrayList;

public class Dynamic2DArray {
    private ArrayList<ArrayList<Integer>> grid;

    public Dynamic2DArray() {
        grid = new ArrayList<>();
    }

    // Thêm một hàng trống mới
    public void addRow() {
        grid.add(new ArrayList<>());
    }

    // Thêm phần tử vào một hàng cụ thể
    public void addElement(int row, int val) {
        if (row >= grid.size()) {
            throw new IndexOutOfBoundsException("Row does not exist");
        }
        grid.get(row).add(val);
    }

    // Lấy phần tử
    public int getElement(int row, int col) {
        return grid.get(row).get(col);
    }
    
    // In toàn bộ lưới (Mảng răng cưa)
    public void printGrid() {
        for (int r = 0; r < grid.size(); r++) {
            System.out.print("Row " + r + ": ");
            for (int c = 0; c < grid.get(r).size(); c++) {
                System.out.print(grid.get(r).get(c) + " ");
            }
            System.out.println();
        }
    }
}
```
**Độ phức tạp:** Lấy dữ liệu qua `get(row).get(col)` $\mathcal{O}(1)$. Thêm dòng hay cột mới $\mathcal{O}(1)$ Amortized. Rất linh hoạt vì các mảng có thể có độ dài lệch nhau.
