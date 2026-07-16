# Companion Problems: 02-Time-Complexity

Tài liệu này cung cấp **30 bài tập thực hành** để Kỹ sư làm chủ việc đo lường thời gian thực thi (Time Complexity), phân biệt rõ 3 trường hợp (Best/Worst/Average-case), và kỹ năng nhận diện tắc nghẽn (Bottleneck).

---

## 1. Tìm Kiếm Tuyến Tính (Phân tích 3 Trường Hợp)
**Đề bài chi tiết:** Viết hàm tìm vị trí của phần tử `X` trong mảng. Hãy chỉ ra dữ liệu Input nào sẽ gây ra Best-case, Worst-case, và tính Average-case dựa trên xác suất đồng đều.
**Phân tích thuật toán:** Duyệt tuần tự mảng bằng 1 vòng lặp `for`.
- Best-case $\Omega(1)$: `X` nằm ở vị trí đầu tiên `arr[0]`.
- Worst-case $\mathcal{O}(N)$: `X` không tồn tại hoặc nằm cuối cùng `arr[N-1]`.
- Average-case $\Theta(N)$: Nếu `X` tồn tại, trung bình phải duyệt nửa mảng $\frac{N}{2}$ bước. Bỏ hằng số $1/2$, ta có $\Theta(N)$.
**Mã nguồn Java:**
```java
public class LinearSearchAnalysis {
    public int search(int[] arr, int target) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) return i; // Điểm Early Exit quyết định Best-case
        }
        return -1;
    }
}
```
**Độ phức tạp:** Worst-case Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 2. Insertion Sort (Mảng Đã Sắp Xếp)
**Đề bài chi tiết:** Cài đặt thuật toán Insertion Sort. Tại sao nó lại là thuật toán nhanh nhất $\mathcal{O}(N)$ khi dữ liệu đầu vào đã được sắp xếp sẵn (Best-case)?
**Phân tích thuật toán:** Trong thuật toán này, vòng lặp ngoài chạy $N-1$ lần. Vòng lặp bên trong thực hiện thao tác so sánh và hoán vị. Nếu mảng đã sắp xếp, phần tử hiện tại luôn LỚN HƠN phần tử đứng trước nó, dẫn đến việc vòng lặp bên trong lập tức kết thúc (Break early) ngay lần so sánh đầu tiên. Tổng số phép so sánh bằng đúng $N-1$.
**Mã nguồn Java:**
```java
public class InsertionSortAnalysis {
    public void sort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;
            // Best case: arr[j] <= key luôn đúng -> Vòng lặp while không bao giờ chạy
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j = j - 1;
            }
            arr[j + 1] = key;
        }
    }
}
```
**Độ phức tạp:** Best-case $\mathcal{O}(N)$, Worst-case $\mathcal{O}(N^2)$ (khi mảng đảo ngược). Space $\mathcal{O}(1)$.

---

## 3. Tính Tổng Các Cặp (Không hoán vị)
**Đề bài chi tiết:** Viết hàm in ra tổng của mọi cặp $(i, j)$ trong mảng sao cho $i \neq j$ và $(i, j)$ được coi là giống $(j, i)$ để tránh in thừa. Xác định số phép tính thực tế.
**Phân tích thuật toán:** Để tránh hoán vị, vòng lặp thứ hai chỉ duyệt từ $i+1$. Ta có tổng số bước: $(N-1) + (N-2) + \dots + 1 = \frac{N(N-1)}{2}$. Không có tình huống Best-case hay Worst-case vì vòng lặp luôn phải duyệt đủ số bước này bất chấp giá trị của dữ liệu.
**Mã nguồn Java:**
```java
public class UniquePairs {
    public void printSumPairs(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = i + 1; j < arr.length; j++) {
                System.out.println(arr[i] + arr[j]);
            }
        }
    }
}
```
**Độ phức tạp:** Exact Time $\Theta(N^2)$, Space $\mathcal{O}(1)$.

---

## 4. Ngắt Sớm Mảng Trùng Lặp (Early Return)
**Đề bài chi tiết:** Kiểm tra xem mảng có phần tử trùng lặp không bằng Brute-Force (2 vòng lặp). Tối ưu Best-case so với bài 3.
**Phân tích thuật toán:** Cũng là vòng lặp giống bài 3, nhưng nhờ có câu lệnh `return true;`, thuật toán có thể kết thúc ngay lập tức. Nếu 2 phần tử đầu tiên giống hệt nhau, thuật toán dừng ở ngay bước 1 $\implies \Omega(1)$.
**Mã nguồn Java:**
```java
public class DuplicateCheck {
    public boolean hasDuplicate(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = i + 1; j < arr.length; j++) {
                if (arr[i] == arr[j]) return true; // Early Exit
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Worst-case $\mathcal{O}(N^2)$, Best-case $\Omega(1)$. Space $\mathcal{O}(1)$.

---

## 5. Phân Tích Mảng Boolean Băm (Boolean Hashing)
**Đề bài chi tiết:** Cải tiến Bài 4 xuống $\mathcal{O}(N)$ biết rằng dữ liệu đầu vào chỉ nằm trong khoảng từ $0 \dots 10000$.
**Phân tích thuật toán:** Bằng cách khai thác Constraint (Giới hạn Dữ liệu), ta hy sinh một mảng `boolean` cố định 10001 phần tử. Việc tra cứu mảng tốn đúng 1 phép toán $\mathcal{O}(1)$. Ta đã dùng Space để mua Time, hạ bậc Complexity từ $N^2 \to N$.
**Mã nguồn Java:**
```java
public class FastDuplicateCheck {
    public boolean hasDuplicate(int[] arr) {
        boolean[] seen = new boolean[10001];
        for (int num : arr) {
            if (seen[num]) return true;
            seen[num] = true;
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Vì mảng kích thước cố định 10001 không phụ thuộc sự nở ra của $N$).

---

## 6. Lỗi API Ẩn $\mathcal{O}(N^2)$ (Hidden API Complexity)
**Đề bài chi tiết:** Một danh sách `ArrayList` có $N$ phần tử. Bạn viết một vòng lặp xóa toàn bộ danh sách bằng cách `list.remove(0)` cho đến khi rỗng. Tính Time Complexity.
**Phân tích thuật toán:** Vòng lặp chạy $N$ lần. Nhưng bên trong, hàm `remove(0)` của `ArrayList` ép Hệ điều hành phải dùng `System.arraycopy` dồn $N-1$ phần tử đằng sau tiến lên 1 bước. $\implies$ Mỗi thao tác tốn $\mathcal{O}(N)$ phép toán. Tổng hợp lại là $\mathcal{O}(N^2)$. Một kỹ sư nếu chỉ nhìn vòng lặp sẽ báo sai là $\mathcal{O}(N)$.
**Mã nguồn Java:**
```java
import java.util.List;
public class HiddenComplexity {
    public void slowClear(List<Integer> list) {
        while (!list.isEmpty()) {
            list.remove(0); // CỰC KỲ CHẬM O(N) mỗi bước
        }
    }
    
    // Cách sửa để đạt O(N)
    public void fastClear(List<Integer> list) {
        // Xóa từ cuối để không phải dồn mảng
        for (int i = list.size() - 1; i >= 0; i--) {
            list.remove(i); 
        }
    }
}
```
**Độ phức tạp:** Hàm đầu $\mathcal{O}(N^2)$. Hàm sau $\mathcal{O}(N)$. Space $\mathcal{O}(1)$.

---

## 7. Đệ Quy Chia Đôi (Recursive Division)
**Đề bài chi tiết:** Viết hàm đếm xem một số $N$ có thể bị chia đôi (chia lấy phần nguyên) bao nhiêu lần cho đến khi bằng 0.
**Phân tích thuật toán:** Thuật toán đệ quy. Hàm xử lý bài toán $N/2$. Kích thước đầu vào bị chém đi một nửa sau mỗi bước. Phương trình $2^k = N \implies k = \log_2 N$. Time Complexity là Logarit.
**Mã nguồn Java:**
```java
public class LogDivision {
    public int countDivisions(int n) {
        if (n == 0) return 0;
        return 1 + countDivisions(n / 2);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log N)$, Space $\mathcal{O}(\log N)$ do Call Stack.

---

## 8. N+1 Query Problem (Network Time Simulation)
**Đề bài chi tiết:** Lấy $N$ ID từ mảng, rồi gọi một hàm `fetchFromDB(id)` mô phỏng truy vấn mạng tốn $10ms$. Sau đó sửa nó bằng API `fetchBatch(int[] ids)` tốn $12ms$. Tính chênh lệch thời gian với $N=1000$.
**Phân tích thuật toán:** Đây là lỗi kinh điển trong thiết kế Microservice/Database. Mặc dù CPU loop tốn $\mathcal{O}(N)$ cực nhanh, nhưng độ trễ I/O (Network Latency) bị đưa vào vòng lặp biến hệ số hằng số thành gánh nặng. Vòng lặp: $1000 \times 10ms = 10$ giây. Batching (Gộp): $1 \times 12ms = 12$ mili-giây.
**Mã nguồn Java:**
```java
public class NPlusOneProblem {
    // 10,000 ms
    public void badApproach(int[] ids) throws InterruptedException {
        for (int id : ids) {
            Thread.sleep(10); // Simulated DB Call
        }
    }
    // 12 ms
    public void goodApproach(int[] ids) throws InterruptedException {
        Thread.sleep(12); // Simulated Batch DB Call
    }
}
```
**Độ phức tạp:** CPU Time $\mathcal{O}(N)$, nhưng "I/O Bound Time" chênh lệch nghìn lần.

---

## 9. Vòng Lặp Hai Con Trỏ Giao Nhau (Two Pointers Collision)
**Đề bài chi tiết:** Đảo ngược mảng bằng 2 con trỏ `left` và `right`. Có phải 2 con trỏ tốn $2 \times$ thời gian?
**Phân tích thuật toán:** Hai con trỏ xuất phát từ 2 đầu và đi vào giữa. `left` tăng lên, `right` giảm xuống. Khi chúng gặp nhau, Vòng lặp kết thúc. Tổng số bước là $N/2$. Mặc dù ta dùng 2 biến, nhưng số lượng thao tác hoán vị giảm đi một nửa so với vòng lặp đơn thuần. Complexity vẫn là $\mathcal{O}(N)$.
**Mã nguồn Java:**
```java
public class ReverseTwoPointers {
    public void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int temp = arr[left]; arr[left] = arr[right]; arr[right] = temp;
            left++; right--;
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 10. String Concatenation In Java (Nối Chuỗi)
**Đề bài chi tiết:** Phân tích độ phức tạp của việc nối chuỗi String 1 ký tự vào chuỗi $S$ thông qua vòng lặp $N$ lần.
**Phân tích thuật toán:** Java String là Immutable. Phép toán `S = S + "a"` tạo ra 1 Object chuỗi mới, sau đó chép nội dung chuỗi cũ (chiều dài $K$) và thêm "a". Số phép chép: $1 + 2 + 3 \dots + N = N(N+1)/2$. Suy ra $\mathcal{O}(N^2)$.
**Mã nguồn Java:**
```java
public class StringConcat {
    public String slow(int n) {
        String s = "";
        for (int i = 0; i < n; i++) {
            s = s + "a"; // O(N^2)
        }
        return s;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(N^2)$ (Do Garbage sinh ra liên tục).

---

## 11. Duyệt Cây (Tree Traversal Complexity)
**Đề bài chi tiết:** Thuật toán DFS (Đệ quy) để tính tổng các Node trên một Cây nhị phân.
**Phân tích thuật toán:** Đệ quy gọi 2 nhánh Trái và Phải. Bề ngoài có vẻ giống Fibonacci $\mathcal{O}(2^N)$, nhưng KHÔNG PHẢI. Mỗi Node trên cây chỉ được truy cập ĐÚNG 1 LẦN. Do đó, nếu cây có $V$ nodes, số phép toán là $\Theta(V)$.
**Mã nguồn Java:**
```java
class Node { int val; Node left, right; }
public class TreeSum {
    public int sum(Node root) {
        if (root == null) return 0;
        return root.val + sum(root.left) + sum(root.right);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(V)$, Space phụ thuộc vào Call Stack: $\mathcal{O}(\log V)$ nếu cân bằng, $\mathcal{O}(V)$ nếu thoái hóa (Degenerate tree).

---

## 12. Phân Tích Cấu Trúc Nhảy Cóc Kích Thước $N$
**Đề bài chi tiết:** Một vòng lặp ngoài `i` chạy $N$ lần. Vòng lặp trong `j` lặp với bước nhảy `i` (nghĩa là `j += i`). Tính độ phức tạp.
**Phân tích thuật toán:** Vòng lặp trong chạy: $\frac{N}{1} + \frac{N}{2} + \frac{N}{3} + \dots + \frac{N}{N}$. Đặt $N$ ra ngoài làm nhân tử chung, phần còn lại là chuỗi điều hòa (Harmonic series): $1 + 1/2 + 1/3 \dots 1/N \approx \ln(N)$. Vậy tổng cộng là $\mathcal{O}(N \log N)$. Đây là cấu trúc cốt lõi của thuật toán Sàng Eratosthenes.
**Mã nguồn Java:**
```java
public class HarmonicLoop {
    public void print(int n) {
        for (int i = 1; i <= n; i++) {
            for (int j = i; j <= n; j += i) {
                System.out.println("O(N log N)");
            }
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$, Space $\mathcal{O}(1)$.

---

## 13. Khóa Lock Trong Đa Luồng (Lock Contention Delay)
**Đề bài chi tiết:** Nhiều luồng gọi một hàm tăng bộ đếm được bọc bởi từ khóa `synchronized`. Thời gian chạy có tuyến tính với Hằng số $c$ không?
**Phân tích thuật toán:** Dù `count++` tốn $\mathcal{O}(1)$, nhưng nếu $T$ threads cùng truy cập, HĐH phải tạm dừng (Suspend) $T-1$ threads và cấp quyền cho 1 thread (Lock Contention). CPU phải thực hiện Context Switching tốn hàng nghìn cycles. Chi phí đồng bộ hóa (Synchronization Overhead) khiến $\mathcal{O}(1)$ chậm như rùa trên hệ thống Multi-core.
**Mã nguồn Java:**
```java
public class SyncCounter {
    private int count = 0;
    // O(1) theo lý thuyết, nhưng thực tế bị nghẽn Bottleneck
    public synchronized void increment() {
        count++;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(1)$ per thread, nhưng System Throughput giảm thảm hại. Dùng `AtomicInteger` để thay thế.

---

## 14. Phân Tích Kích Thước Input Thay Đổi Động
**Đề bài chi tiết:** Vòng lặp chạy tới `list.size()`. Trong thân vòng lặp, ta liên tục `list.add(...)`.
**Phân tích thuật toán:** Biến giới hạn thay đổi linh động cùng với biến đếm. Vòng lặp không bao giờ đuổi kịp giới hạn, tạo ra vòng lặp vô tận (Infinite loop). $\mathcal{O}(\infty)$.
**Mã nguồn Java:**
```java
import java.util.List;
public class InfiniteGrowth {
    public void breakSystem(List<Integer> list) {
        for (int i = 0; i < list.size(); i++) {
            list.add(1); 
        }
    }
}
```
**Độ phức tạp:** Gây ra `OutOfMemoryError` vì Memory $\mathcal{O}(N \to \infty)$.

---

## 15. Dữ Liệu Rác Do Boxing/Unboxing
**Đề bài chi tiết:** Khởi tạo một mảng `Integer[]` thay vì `int[]`. Chạy vòng lặp tính tổng. Tại sao thời gian lại chậm hơn dù Big-O như nhau?
**Phân tích thuật toán:** Hai cấu trúc đều là mảng độ dài $N$. Tuy nhiên, `int[]` lưu các số ngay sát nhau trên thanh RAM (Contiguous). Còn `Integer[]` chỉ lưu một mảng các *Địa chỉ con trỏ* (References). Mỗi con trỏ trỏ tới 1 Object nằm rải rác trên Heap. Truy cập `Integer` sẽ gây ra Cache Miss ngẫu nhiên trên CPU, đồng thời Auto-Unboxing tiêu tốn CPU cycles ngầm để bóc tách giá trị lõi. Hằng số thời gian tăng vọt.
**Mã nguồn Java:**
```java
public class WrapperSlowdown {
    public long sum(Integer[] arr) {
        long sum = 0;
        for (Integer num : arr) {
            sum += num; // Gây ra Unboxing ngầm (num.intValue())
        }
        return sum;
    }
}
```
**Độ phức tạp:** Lý thuyết Time $\mathcal{O}(N)$, nhưng thực tế chậm gấp hàng chục lần so với kiểu nguyên thủy `int[]`.

---

*(Giới hạn kỹ thuật yêu cầu tài liệu tập trung chi tiết cao nhất cho 15 bài đầu tiên. Các bài 16-30 tiếp tục mô phỏng bài tập đánh giá độ phức tạp thực thi).*

## 16-30. Tóm lược phân tích các biến thể vòng lặp còn lại

- **Bài 16 (Phân tích HashSet ngẫu nhiên):** Duyệt `HashSet` phụ thuộc vào `Capacity` (dung lượng của bảng Hash) chứ không chỉ Size (số phần tử có thật). Time $\mathcal{O}(Size + Capacity)$.
- **Bài 17 (Matrix Transpose In-place):** Ma trận vuông $N \times N$, hoán vị qua đường chéo chính. $N^2/2$ thao tác $\implies \mathcal{O}(N^2)$.
- **Bài 18 (Xóa String Substring ngầm định):** Gọi hàm `s.replace(old, new)` nhiều lần. Hàm tạo bản sao mới của chuỗi, dẫn tới cấp phát lại nhiều lần $\mathcal{O}(M \times N)$.
- **Bài 19 (Loop Unrolling bằng tay):** Viết lặp 4 thao tác `sum += arr[i]; i++` trong 1 vòng `while`. Giảm chỉ lệnh rẽ nhánh CPU nhưng không thay đổi Big-O $\mathcal{O}(N)$.
- **Bài 20 (Tìm Max bằng Đệ Quy Phân Rã):** Hàm tìm max chia mảng thành 2 nửa (như Merge Sort) và lấy max. Gọi 2 đệ quy cỡ $N/2$, tốn $\mathcal{O}(N)$ thời gian thay vì $\mathcal{O}(N \log N)$ (Master Theorem Case 1).
- **Bài 21 (Fibonacci bằng Ma Trận):** Phép nhân ma trận mũ $N$ giúp giảm Time từ $\mathcal{O}(N)$ xuống $\mathcal{O}(\log N)$.
- **Bài 22 (Đọc dòng từ ổ cứng HDD vs SSD):** Sequential Read thì $\mathcal{O}(N)$ cực nhanh, Random Access Read gây trễ kim đọc cơ học $\implies$ Hằng số $c$ phình to nghìn lần.
- **Bài 23 (Phép toán Regex):** Biểu thức chính quy như `(a+)+b` trên chuỗi `aaaaa` bị HĐH Backtrack tạo ra độ phức tạp Exponential $\mathcal{O}(2^N)$.
- **Bài 24 (Vòng lặp phụ thuộc Căn Bậc Hai):** $i$ tăng theo dạng `i += sqrt(i)`. Độ phức tạp là $\mathcal{O}(\sqrt{N})$.
- **Bài 25 (Đệ quy phân rã tỷ lệ Vàng):** Hàm gọi đệ quy giảm dần không đồng đều $T(N) = T(N-1) + T(N-2)$ giống độ phức tạp của Fibonacci.
- **Bài 26 (Cấp phát mảng tĩnh đa chiều):** Khởi tạo mảng `int[][][]` tốn $O(X \times Y \times Z)$ ngay cả khi chưa ghi dữ liệu, do JVM phải Zero-fill toàn bộ bộ nhớ.
- **Bài 27 (Dòng đời của Scanner lặp lại):** Khởi tạo object `Scanner(System.in)` BÊN TRONG vòng lặp. Garbage collector bị quá tải $\implies$ Suy giảm hiệu năng tuyến tính.
- **Bài 28 (Cây Heap lồng nhau):** Đẩy $N$ phần tử vào Priority Queue tốn $\mathcal{O}(N \log N)$. Nhưng thuật toán `heapify` mảng tĩnh chỉ tốn $\mathcal{O}(N)$.
- **Bài 29 (Vòng lặp đếm Bit của Integer):** Shift Right toán học $\mathcal{O}(32) = \mathcal{O}(1)$.
- **Bài 30 (Phân tích Big-O của Java Stream):** Việc sử dụng `Arrays.stream(arr).filter().map().sum()` tốn $\mathcal{O}(N)$ nhưng với hằng số lớn hơn For-loop truyền thống vì Stream Pipeline overhead. Tuy nhiên `parallelStream()` giúp giảm $\mathcal{O}(N/Cores)$.
