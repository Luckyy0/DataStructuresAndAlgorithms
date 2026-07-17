# Companion Problems: 05-Master-Theorem

Tài liệu này cung cấp **30 bài tập thực hành** chuyên sâu về Định lý Chủ (Master Theorem), Phương pháp Cây Đệ quy (Recursion Tree), và Phương pháp Thay thế (Substitution Method) để giải các Phương trình Đệ quy (Recurrence Relations).

---

## 1. Binary Search — Case 2 ($a = 1$)
**Đề bài chi tiết:** Viết hàm Binary Search đệ quy. Xác định Recurrence và giải bằng Master Theorem.
**Phân tích thuật toán:** Hàm chia mảng thành 2 nửa nhưng chỉ đệ quy vào 1 nửa ($a = 1$). Bước so sánh tốn $\mathcal{O}(1)$. Recurrence: $T(N) = T(N/2) + 1$. Tính $p = \log_2 1 = 0$. $f(N) = 1 = N^0$. $c = 0 = p$. **Case 2**: $T(N) = \Theta(N^0 \cdot \log N) = \Theta(\log N)$.
**Mã nguồn Java:**
```java
public class BinarySearchRecurrence {
    public int search(int[] arr, int target, int lo, int hi) {
        if (lo > hi) return -1;
        int mid = lo + (hi - lo) / 2;   // O(1)
        if (arr[mid] == target) return mid;
        if (arr[mid] > target)
            return search(arr, target, lo, mid - 1);   // T(N/2)
        else
            return search(arr, target, mid + 1, hi);    // T(N/2)
    }
}
```
**Độ phức tạp:** Time $\Theta(\log N)$ (Case 2). Space $\mathcal{O}(\log N)$ do Call Stack.

---

## 2. Merge Sort — Case 2 ($a = 2$)
**Đề bài chi tiết:** Cài đặt Merge Sort đệ quy. Viết Recurrence, xác định Case và giải.
**Phân tích thuật toán:** Chia mảng thành 2 nửa, đệ quy cả 2 ($a = 2$, $b = 2$). Bước Merge duyệt toàn bộ $N$ phần tử: $f(N) = N$. Recurrence: $T(N) = 2T(N/2) + N$. $p = \log_2 2 = 1$. $c = 1 = p$. **Case 2**: $T(N) = \Theta(N \log N)$.
**Mã nguồn Java:**
```java
public class MergeSortRecurrence {
    public void sort(int[] arr, int lo, int hi) {
        if (lo >= hi) return;
        int mid = lo + (hi - lo) / 2;
        sort(arr, lo, mid);             // T(N/2)
        sort(arr, mid + 1, hi);         // T(N/2)
        merge(arr, lo, mid, hi);        // O(N)
    }

    private void merge(int[] arr, int lo, int mid, int hi) {
        int[] temp = new int[hi - lo + 1];
        int i = lo, j = mid + 1, k = 0;
        while (i <= mid && j <= hi)
            temp[k++] = arr[i] <= arr[j] ? arr[i++] : arr[j++];
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= hi) temp[k++] = arr[j++];
        System.arraycopy(temp, 0, arr, lo, temp.length);
    }
}
```
**Độ phức tạp:** Time $\Theta(N \log N)$ (Case 2). Space $\mathcal{O}(N)$.

---

## 3. Tìm Max Đệ quy — Case 1 ($a = 2, f(N) = 1$)
**Đề bài chi tiết:** Tìm phần tử lớn nhất trong mảng bằng đệ quy chia đôi. Viết Recurrence và giải.
**Phân tích thuật toán:** Chia mảng thành 2 nửa ($a = 2, b = 2$). Bước "Merge" chỉ so sánh 2 giá trị max: $f(N) = 1 = N^0$. $p = \log_2 2 = 1$. $c = 0 < p = 1$. **Case 1**: $T(N) = \Theta(N^1) = \Theta(N)$.
**Mã nguồn Java:**
```java
public class RecursiveMax {
    public int findMax(int[] arr, int lo, int hi) {
        if (lo == hi) return arr[lo];       // Base case
        int mid = lo + (hi - lo) / 2;
        int leftMax = findMax(arr, lo, mid);    // T(N/2)
        int rightMax = findMax(arr, mid + 1, hi); // T(N/2)
        return Math.max(leftMax, rightMax);     // O(1) merge
    }
}
```
**Độ phức tạp:** Time $\Theta(N)$ (Case 1). Space $\mathcal{O}(\log N)$.

---

## 4. Nhân Ma Trận Strassen — Case 1 ($a = 7$)
**Đề bài chi tiết:** Giải thích Recurrence của thuật toán Strassen. Tại sao giảm từ 8 xuống 7 bài toán con lại tạo ra bước nhảy vọt?
**Phân tích thuật toán:** Strassen nhân 2 ma trận $N \times N$ bằng 7 phép nhân ma trận con $N/2 \times N/2$ (thay vì 8 theo Naive). Bước cộng/trừ ma trận con tốn $\mathcal{O}(N^2)$. Recurrence: $T(N) = 7T(N/2) + N^2$. $p = \log_2 7 \approx 2.807$. $c = 2 < p$. **Case 1**: $T(N) = \Theta(N^{\log_2 7}) \approx \Theta(N^{2.807})$.
**Mã nguồn Java:**
```java
// Pseudo-code cho Strassen (Full implementation rất dài)
public class StrassenPseudo {
    // T(N) = 7T(N/2) + O(N^2)
    // 7 phép nhân con: M1..M7 (Công thức Strassen)
    // 18 phép cộng/trừ ma trận: O(N^2) mỗi phép
    // => Tiết kiệm 1 phép nhân, đổi lấy thêm phép cộng
    // Naive: O(N^3). Strassen: O(N^2.807)
}
```
**Độ phức tạp:** $\Theta(N^{2.807})$ (Case 1).

---

## 5. Karatsuba Multiplication — Case 1 ($a = 3$)
**Đề bài chi tiết:** Cài đặt thuật toán nhân 2 số nguyên lớn bằng Karatsuba. Viết Recurrence.
**Phân tích thuật toán:** Chia mỗi số $N$ chữ số thành 2 nửa ($N/2$ chữ số). Phép nhân ngây thơ cần 4 phép nhân con. Karatsuba sử dụng mẹo $(a+b)(c+d) = ac + ad + bc + bd$ để giảm xuống 3 phép nhân con bằng cách tính $ac$, $bd$, và $(a+b)(c+d) - ac - bd = ad + bc$. Recurrence: $T(N) = 3T(N/2) + \mathcal{O}(N)$. $p = \log_2 3 \approx 1.585$. $c = 1 < p$. **Case 1**: $\Theta(N^{1.585})$.
**Mã nguồn Java:**
```java
import java.math.BigInteger;
public class Karatsuba {
    public BigInteger multiply(BigInteger x, BigInteger y) {
        int n = Math.max(x.bitLength(), y.bitLength());
        if (n <= 64) return x.multiply(y); // Base case

        int half = n / 2;
        BigInteger a = x.shiftRight(half); // High half
        BigInteger b = x.subtract(a.shiftLeft(half)); // Low half
        BigInteger c = y.shiftRight(half);
        BigInteger d = y.subtract(c.shiftLeft(half));

        BigInteger ac = multiply(a, c);       // T(N/2)
        BigInteger bd = multiply(b, d);       // T(N/2)
        BigInteger abcd = multiply(a.add(b), c.add(d)); // T(N/2)

        // (a+b)(c+d) - ac - bd = ad + bc
        BigInteger adbc = abcd.subtract(ac).subtract(bd);

        return ac.shiftLeft(2 * half).add(adbc.shiftLeft(half)).add(bd);
    }
}
```
**Độ phức tạp:** $\Theta(N^{1.585})$ (Case 1, nhanh hơn $\Theta(N^2)$ Naive).

---

## 6. Quick Select — Case 3 ($a = 1, f(N) = N$)
**Đề bài chi tiết:** Tìm phần tử lớn thứ $K$ trong mảng không sắp xếp. Average-case Recurrence?
**Phân tích thuật toán:** Quick Select chia mảng theo Pivot. Average-case: Pivot rơi vào giữa, chỉ đệ quy 1 nửa. $T(N) = T(N/2) + N$. $a = 1, b = 2, p = 0, c = 1$. $c > p$. **Case 3**: $T(N) = \Theta(N)$. (Worst-case: $T(N) = T(N-1) + N = \Theta(N^2)$, không áp dụng Master Theorem).
**Mã nguồn Java:**
```java
public class QuickSelect {
    public int kthSmallest(int[] arr, int lo, int hi, int k) {
        if (lo == hi) return arr[lo];
        int pivotIdx = partition(arr, lo, hi);
        int count = pivotIdx - lo + 1;
        if (k == count) return arr[pivotIdx];
        if (k < count) return kthSmallest(arr, lo, pivotIdx - 1, k);
        return kthSmallest(arr, pivotIdx + 1, hi, k - count);
    }

    private int partition(int[] arr, int lo, int hi) {
        int pivot = arr[hi]; int i = lo;
        for (int j = lo; j < hi; j++) {
            if (arr[j] <= pivot) {
                int t = arr[i]; arr[i] = arr[j]; arr[j] = t; i++;
            }
        }
        int t = arr[i]; arr[i] = arr[hi]; arr[hi] = t;
        return i;
    }
}
```
**Độ phức tạp:** Average $\Theta(N)$ (Case 3). Worst $\Theta(N^2)$.

---

## 7. Closest Pair of Points — Case 2 ($k = 1$)
**Đề bài chi tiết:** Tìm cặp điểm gần nhất trong mặt phẳng 2D. Giải Recurrence.
**Phân tích thuật toán:** Chia tập hợp điểm thành 2 nửa theo tọa độ $x$ ($a = 2, b = 2$). Bước Merge (Tìm cặp gần nhất xuyên biên) tốn $\mathcal{O}(N \log N)$ nếu sort theo $y$, hoặc $\mathcal{O}(N)$ nếu pre-sort. Trường hợp Merge $\mathcal{O}(N \log N)$: $T(N) = 2T(N/2) + N \log N$. $p = 1, c = 1, k = 1$. **Case 2 mở rộng**: $T(N) = \Theta(N \log^2 N)$.
**Mã nguồn Java:**
```java
// Pseudo-code (Full implementation khá dài)
public class ClosestPairPseudo {
    // 1. Sort theo x: O(N log N)
    // 2. Chia thành Left, Right
    // 3. d = min(closestPair(Left), closestPair(Right))
    // 4. Kiểm tra strip (dải biên rộng 2d): O(N log N) nếu sort lại
    // T(N) = 2T(N/2) + O(N log N) => O(N log^2 N)
    // Tối ưu: Pre-sort theo y => Merge O(N) => O(N log N)
}
```
**Độ phức tạp:** $\Theta(N \log^2 N)$ hoặc $\Theta(N \log N)$ nếu tối ưu Merge.

---

## 8. Tìm Kiếm Ternary (Ternary Search)
**Đề bài chi tiết:** Thay vì chia đôi (Binary Search), chia mảng thành 3 phần bằng nhau. Recurrence? Nhanh hơn Binary Search không?
**Phân tích thuật toán:** $T(N) = T(N/3) + 2$ ($a = 1, b = 3$, 2 phép so sánh). $p = \log_3 1 = 0$. $c = 0 = p$. **Case 2**: $T(N) = \Theta(\log N)$. Nhưng cơ số Logarithm: Binary Search so sánh $\log_2 N$ lần, Ternary Search so sánh $2 \log_3 N \approx 1.26 \log_2 N$ lần. **Ternary Search CHẬM HƠN** vì mỗi bước tốn 2 phép so sánh thay vì 1.
**Mã nguồn Java:**
```java
public class TernarySearch {
    public int search(int[] arr, int target, int lo, int hi) {
        if (lo > hi) return -1;
        int third = (hi - lo) / 3;
        int m1 = lo + third, m2 = hi - third;
        if (arr[m1] == target) return m1;
        if (arr[m2] == target) return m2;
        if (target < arr[m1]) return search(arr, target, lo, m1 - 1);
        if (target > arr[m2]) return search(arr, target, m2 + 1, hi);
        return search(arr, target, m1 + 1, m2 - 1);
    }
}
```
**Độ phức tạp:** $\Theta(\log_3 N)$ nhưng $2 \times$ so sánh mỗi bước $\implies$ chậm hơn Binary Search.

---

## 9. Power Function — Exponentiation by Squaring
**Đề bài chi tiết:** Tính $x^N$ bằng đệ quy. So sánh cách ngây thơ ($N$ phép nhân) với cách chia đôi mũ.
**Phân tích thuật toán:** Ngây thơ: $T(N) = T(N-1) + 1 = \Theta(N)$. Tối ưu: $x^N = (x^{N/2})^2$ nếu $N$ chẵn. Recurrence: $T(N) = T(N/2) + 1$. $a = 1, b = 2, p = 0, c = 0$. **Case 2**: $T(N) = \Theta(\log N)$.
**Mã nguồn Java:**
```java
public class FastPower {
    public long power(long base, int exp, long mod) {
        if (exp == 0) return 1;
        long half = power(base, exp / 2, mod); // T(N/2)
        half = (half * half) % mod;             // O(1)
        if (exp % 2 == 1) half = (half * base) % mod;
        return half;
    }
}
```
**Độ phức tạp:** Time $\Theta(\log N)$, Space $\mathcal{O}(\log N)$.

---

## 10. Counting Inversions
**Đề bài chi tiết:** Đếm số cặp nghịch thế (Inversion pairs) trong mảng: số cặp $(i, j)$ mà $i < j$ và $arr[i] > arr[j]$.
**Phân tích thuật toán:** Giống Merge Sort nhưng đếm thêm nghịch thế trong bước Merge. Recurrence giống hệt Merge Sort: $T(N) = 2T(N/2) + N$. **Case 2**: $\Theta(N \log N)$. Brute Force tốn $\Theta(N^2)$.
**Mã nguồn Java:**
```java
public class CountInversions {
    public long countAndSort(int[] arr, int lo, int hi) {
        if (lo >= hi) return 0;
        int mid = lo + (hi - lo) / 2;
        long count = countAndSort(arr, lo, mid) + countAndSort(arr, mid + 1, hi);
        count += mergeCount(arr, lo, mid, hi);
        return count;
    }

    private long mergeCount(int[] arr, int lo, int mid, int hi) {
        int[] temp = new int[hi - lo + 1];
        int i = lo, j = mid + 1, k = 0;
        long inversions = 0;
        while (i <= mid && j <= hi) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                inversions += (mid - i + 1); // Tất cả phần tử còn lại bên trái là Inversion
                temp[k++] = arr[j++];
            }
        }
        while (i <= mid) temp[k++] = arr[i++];
        while (j <= hi) temp[k++] = arr[j++];
        System.arraycopy(temp, 0, arr, lo, temp.length);
        return inversions;
    }
}
```
**Độ phức tạp:** Time $\Theta(N \log N)$ (Case 2). Space $\mathcal{O}(N)$.

---

## 11. Tower of Hanoi
**Đề bài chi tiết:** Giải bài toán Tháp Hà Nội với $N$ đĩa. Viết Recurrence và chứng minh $T(N) = 2^N - 1$.
**Phân tích thuật toán:** Để di chuyển $N$ đĩa từ cọc A sang cọc C qua cọc B: Di chuyển $N-1$ đĩa nhỏ từ A sang B ($T(N-1)$), di chuyển đĩa lớn nhất từ A sang C ($1$ bước), di chuyển $N-1$ đĩa từ B sang C ($T(N-1)$). Recurrence: $T(N) = 2T(N-1) + 1$. Đây KHÔNG phải dạng chuẩn Master Theorem ($b$ không phải hằng số). Giải bằng Substitution: $T(N) = 2^N - 1$.
**Mã nguồn Java:**
```java
public class TowerOfHanoi {
    public void solve(int n, char from, char to, char aux) {
        if (n == 0) return;
        solve(n - 1, from, aux, to);    // T(N-1)
        System.out.println(from + " -> " + to); // O(1)
        solve(n - 1, aux, to, from);    // T(N-1)
    }
}
```
**Độ phức tạp:** Time $\Theta(2^N)$. Space $\mathcal{O}(N)$ do Call Stack.

---

## 12. Fibonacci Divide and Conquer (Matrix Exponentiation)
**Đề bài chi tiết:** Tính số Fibonacci thứ $N$ bằng lũy thừa ma trận. Recurrence?
**Phân tích thuật toán:** Biểu diễn Fibonacci dưới dạng ma trận: $\begin{pmatrix} F(N+1) \\ F(N) \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^N \begin{pmatrix} 1 \\ 0 \end{pmatrix}$. Lũy thừa ma trận bằng Exponentiation by Squaring: $T(N) = T(N/2) + \mathcal{O}(1)$ (phép nhân 2 ma trận $2 \times 2$ tốn hằng số). **Case 2**: $\Theta(\log N)$.
**Mã nguồn Java:**
```java
public class FibonacciMatrix {
    public long fib(int n) {
        if (n <= 1) return n;
        long[][] base = {{1, 1}, {1, 0}};
        long[][] result = matPow(base, n - 1);
        return result[0][0];
    }

    private long[][] matPow(long[][] m, int p) {
        if (p == 1) return m;
        long[][] half = matPow(m, p / 2); // T(N/2)
        long[][] res = matMul(half, half); // O(1) for 2x2
        if (p % 2 == 1) res = matMul(res, m);
        return res;
    }

    private long[][] matMul(long[][] a, long[][] b) {
        return new long[][]{
            {a[0][0]*b[0][0] + a[0][1]*b[1][0], a[0][0]*b[0][1] + a[0][1]*b[1][1]},
            {a[1][0]*b[0][0] + a[1][1]*b[1][0], a[1][0]*b[0][1] + a[1][1]*b[1][1]}
        };
    }
}
```
**Độ phức tạp:** Time $\Theta(\log N)$, Space $\mathcal{O}(\log N)$.

---

## 13. Maximum Subarray — Divide and Conquer
**Đề bài chi tiết:** Tìm mảng con có tổng lớn nhất (Maximum Subarray Sum) bằng Divide and Conquer.
**Phân tích thuật toán:** Chia mảng thành 2 nửa. Mảng con tối ưu hoặc nằm hoàn toàn bên Trái, hoặc Phải, hoặc đi qua điểm giữa. Bước tìm Crossing-max tốn $\mathcal{O}(N)$. Recurrence: $T(N) = 2T(N/2) + N$. **Case 2**: $\Theta(N \log N)$. (Kadane's Algorithm giải bài này trong $\Theta(N)$).
**Mã nguồn Java:**
```java
public class MaxSubarrayDC {
    public int maxSubArray(int[] arr, int lo, int hi) {
        if (lo == hi) return arr[lo];
        int mid = lo + (hi - lo) / 2;
        int leftMax = maxSubArray(arr, lo, mid);
        int rightMax = maxSubArray(arr, mid + 1, hi);
        int crossMax = maxCrossing(arr, lo, mid, hi);
        return Math.max(Math.max(leftMax, rightMax), crossMax);
    }

    private int maxCrossing(int[] arr, int lo, int mid, int hi) {
        int leftSum = Integer.MIN_VALUE, sum = 0;
        for (int i = mid; i >= lo; i--) { sum += arr[i]; leftSum = Math.max(leftSum, sum); }
        int rightSum = Integer.MIN_VALUE; sum = 0;
        for (int i = mid + 1; i <= hi; i++) { sum += arr[i]; rightSum = Math.max(rightSum, sum); }
        return leftSum + rightSum;
    }
}
```
**Độ phức tạp:** Time $\Theta(N \log N)$ (Case 2).

---

## 14. Vẽ Recursion Tree Cho $T(N) = 3T(N/2) + N$
**Đề bài chi tiết:** Sử dụng Recursion Tree Method để giải $T(N) = 3T(N/2) + N$.
**Phân tích thuật toán:** Mỗi tầng: Tầng 0: $N$. Tầng 1: $3 \times N/2 = 3N/2$. Tầng 2: $9 \times N/4 = 9N/4$. Tầng $i$: $(3/2)^i \times N$. Số tầng: $\log_2 N$. Tổng: $N \sum_{i=0}^{\log_2 N} (3/2)^i = N \cdot \frac{(3/2)^{\log_2 N + 1} - 1}{3/2 - 1}$. $(3/2)^{\log_2 N} = N^{\log_2(3/2)} = N^{\log_2 3 - 1}$. Tổng $\approx 2N \cdot N^{\log_2 3 - 1} = 2N^{\log_2 3}$. Xác nhận Master Theorem: $p = \log_2 3 \approx 1.585$, $c = 1 < p$. **Case 1**: $\Theta(N^{1.585})$. ✅
**Mã nguồn Java:**
```java
public class RecursionTreeDemo {
    static long ops = 0;
    static void solve(int n) {
        if (n <= 1) return;
        ops += n; // Work at this level
        solve(n / 2); solve(n / 2); solve(n / 2); // 3 subproblems of N/2
    }
    public static void main(String[] args) {
        int n = 1 << 20;
        solve(n);
        double expected = Math.pow(n, Math.log(3) / Math.log(2));
        System.out.printf("Ops: %d, N^1.585: %.0f, Ratio: %.4f%n", ops, expected, ops / expected);
    }
}
```
**Độ phức tạp:** $\Theta(N^{\log_2 3})$ (Case 1).

---

## 15. Substitution Method — Chứng Minh $T(N) = 2T(N/2) + N$ là $\Theta(N \log N)$
**Đề bài chi tiết:** Chứng minh bằng Quy nạp toán học (Mathematical Induction) rằng $T(N) \le cN \log N$.
**Phân tích thuật toán:**
- **Giả thuyết quy nạp**: $T(k) \le ck \log k$ cho mọi $k < N$.
- **Bước quy nạp**: $T(N) = 2T(N/2) + N \le 2c(N/2)\log(N/2) + N = cN(\log N - 1) + N = cN\log N - cN + N$.
- Cần: $cN\log N - cN + N \le cN\log N \implies N \le cN \implies c \ge 1$.
- Chọn $c = 1$: $T(N) \le N \log N$. QED.
**Mã nguồn Java:**
```java
// Verification code
public class SubstitutionVerify {
    static int T(int n) {
        if (n <= 1) return 1;
        return 2 * T(n / 2) + n;
    }
    public static void main(String[] args) {
        for (int n = 2; n <= 1024; n *= 2) {
            double ratio = (double) T(n) / (n * (Math.log(n) / Math.log(2)));
            System.out.printf("T(%d) = %d, ratio = %.4f%n", n, T(n), ratio);
            // Ratio phải hội tụ về hằng số
        }
    }
}
```
**Độ phức tạp:** Confirmed $\Theta(N \log N)$.

---

## 16-30. Tóm lược phân tích các Recurrence Relations còn lại

- **Bài 16 ($T(N) = 4T(N/2) + N$):** $p = 2, c = 1$. Case 1: $\Theta(N^2)$. Ứng dụng: Nhân ma trận ngây thơ khi Merge tốn $\mathcal{O}(N)$.
- **Bài 17 ($T(N) = 4T(N/2) + N^2$):** $p = 2, c = 2$. Case 2: $\Theta(N^2 \log N)$. Cây đệ quy cân bằng hoàn hảo.
- **Bài 18 ($T(N) = 4T(N/2) + N^3$):** $p = 2, c = 3 > p$. Case 3: $\Theta(N^3)$. Root thống trị.
- **Bài 19 ($T(N) = T(N/2) + \log N$):** $p = 0, c = 0, k = 1$. Case 2 mở rộng: $\Theta(\log^2 N)$.
- **Bài 20 ($T(N) = 2T(N/4) + \sqrt{N}$):** $p = \log_4 2 = 0.5, c = 0.5$. Case 2: $\Theta(\sqrt{N} \log N)$.
- **Bài 21 ($T(N) = 2T(N/4) + 1$):** $p = 0.5, c = 0$. Case 1: $\Theta(\sqrt{N})$.
- **Bài 22 ($T(N) = 3T(N/3) + N$):** $p = 1, c = 1$. Case 2: $\Theta(N \log N)$. 3-way Merge Sort.
- **Bài 23 ($T(N) = 8T(N/4) + N^{1.5}$):** $p = \log_4 8 = 1.5, c = 1.5$. Case 2: $\Theta(N^{1.5} \log N)$.
- **Bài 24 ($T(N) = T(N-1) + N$):** KHÔNG phải Master Theorem. Telescoping: $T(N) = N + (N-1) + \dots + 1 = \Theta(N^2)$.
- **Bài 25 ($T(N) = 2T(N-1) + 1$):** KHÔNG phải Master Theorem. $T(N) = 2^N - 1 = \Theta(2^N)$. Tower of Hanoi.
- **Bài 26 ($T(N) = T(\sqrt{N}) + 1$):** Đặt $M = \log N$. $S(M) = S(M/2) + 1 = \Theta(\log M) = \Theta(\log \log N)$.
- **Bài 27 (Median of Medians):** $T(N) = T(N/5) + T(7N/10) + N$. Akra-Bazzi: $(1/5)^p + (7/10)^p = 1$. Giải: $p \approx 0.84$. Kết quả: $\Theta(N)$.
- **Bài 28 (FFT):** $T(N) = 2T(N/2) + N$. Case 2: $\Theta(N \log N)$. Cách mạng hóa Xử lý tín hiệu.
- **Bài 29 ($T(N) = T(N/2) + T(N/4) + N$):** Akra-Bazzi: $(1/2)^p + (1/4)^p = 1$. Giải: $p \approx 0.694$. $T(N) = \Theta(N)$.
- **Bài 30 (Đệ quy lồng nhau $T(N) = T(T(N-1))$):** Không thể giải bằng bất kỳ Theorem chuẩn nào. Cần phân tích cụ thể từng hàm $T$ bên trong.
