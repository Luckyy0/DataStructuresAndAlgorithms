# Companion Problems: 01-Fork-Join-Framework

Tài liệu này cung cấp **30 bài tập thực hành chuyên sâu** về mô hình lập trình song song **Fork-Join Framework** và giải thuật **Parallel Divide-and-Conquer** trong Java 21. Mỗi bài toán bao gồm đề bài chi tiết, phân tích bản chất xử lý song song và ngưỡng cơ sở (Sequential Threshold), giải pháp hoàn chỉnh bằng `RecursiveTask` / `RecursiveAction`, cùng phân tích độ phức tạp đa chiều: Work ($T_1$), Span ($T_\infty$), Parallel Runtime ($T_P$) và Space Complexity.

---

## 1. Parallel Array Sum (Tính tổng mảng song song)

**Đề bài chi tiết:**
Cho một mảng số nguyên $A$ kích thước $N$ ($1 \le N \le 10^8$). Hãy tính tổng toàn bộ các phần tử trong mảng $S = \sum_{i=0}^{N-1} A[i]$ bằng cách sử dụng Java Fork-Join Framework để tận dụng tối đa năng lực xử lý của hệ thống đa lõi (multi-core processor).

**Phân tích thuật toán:**
- **Chiến lược phân rã (Divide-and-Conquer):** Mảng $A[low..high]$ được chia đôi tại điểm giữa $mid = low + \frac{high - low}{2}$. Subtask bên trái $A[low..mid]$ được fork sang luồng khác (`leftTask.fork()`), subtask bên phải $A[mid..high]$ được tính toán trực tiếp trên luồng hiện tại (`compute()`), sau đó gọi `leftTask.join()` để lấy kết quả và cộng dồn. Điều này giúp tiết kiệm việc phân phối một task thừa vào hàng đợi công việc (Work-Stealing Queue).
- **Sequential Cutoff Threshold:** Với kích thước mảng nhỏ ($N \le 10\,000$), chi phí tạo `ForkJoinTask`, quản lý frame trên heap và context switch vượt trội hơn thời gian tính toán của CPU. Khi `high - low <= THRESHOLD`, thuật toán chuyển sang vòng lặp tuần tự để CPU tận dụng tính năng Vectorization (SIMD) và L1/L2 Data Cache locality.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelArraySum {

    private static final int THRESHOLD = 10_000;

    public static class SumTask extends RecursiveTask<Long> {
        private final int[] array;
        private final int low;
        private final int high;

        public SumTask(int[] array, int low, int high) {
            this.array = array;
            this.low = low;
            this.high = high;
        }

        @Override
        protected Long compute() {
            if (high - low <= THRESHOLD) {
                long sum = 0;
                for (int i = low; i < high; i++) {
                    sum += array[i];
                }
                return sum;
            }

            int mid = low + (high - low) / 2;
            SumTask leftTask = new SumTask(array, low, mid);
            SumTask rightTask = new SumTask(array, mid, high);

            leftTask.fork(); // Đẩy subtask trái vào deque luồng hiện tại
            long rightResult = rightTask.compute(); // Luồng hiện tại xử lý subtask phải
            long leftResult = leftTask.join(); // Lấy kết quả từ subtask trái

            return leftResult + rightResult;
        }
    }

    public static long computeSum(int[] array) {
        if (array == null || array.length == 0) return 0L;
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new SumTask(array, 0, array.length));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N)$ (Tổng số phép toán cộng tương đương thuật toán tuần tự).
- **Span ($T_\infty$):** $\mathcal{O}(\log N)$ (Chiều sâu của cây đệ quy song song).
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log N\right)$ với $P$ là số CPU cores (theo Định lý Brent).
- **Space Complexity:** $\mathcal{O}(\log N)$ không gian bộ nhớ ngăn xếp task tree.

---

## 2. Parallel Min/Max with Index (Tìm cực trị và vị trí song song)

**Đề bài chi tiết:**
Cho mảng số nguyên $A$ gồm $N$ phần tử. Hãy tìm giá trị nhỏ nhất (Minimum) và giá trị lớn nhất (Maximum) cùng với chỉ số (index) đầu tiên xuất hiện của các giá trị đó trong mảng bằng mô hình song song.

**Phân tích thuật toán:**
- **Cấu trúc dữ liệu kết quả:** Mỗi task trả về một record `Extremum(int minVal, int minIdx, int maxVal, int maxIdx)`.
- **Hợp nhất (Combine Phase):** Khi nhận kết quả từ 2 nhánh `left` và `right`, so sánh `left.minVal` và `right.minVal`. Nếu bằng nhau, ưu tiên lấy chỉ số nhỏ hơn `min(left.minIdx, right.minIdx)`. Tương tự với giá trị lớn nhất.
- **Tối ưu hóa bộ nhớ:** Tránh tạo quá nhiều đối tượng trung gian bằng cách sử dụng Java 21 `record` dạng bất biến, hỗ trợ Garbage Collector tối ưu hóa qua Escape Analysis (Scalar Replacement).

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelMinMaxIndex {

    public record Extremum(int minVal, int minIdx, int maxVal, int maxIdx) {}

    private static final int THRESHOLD = 8_192;

    public static class MinMaxTask extends RecursiveTask<Extremum> {
        private final int[] array;
        private final int low;
        private final int high;

        public MinMaxTask(int[] array, int low, int high) {
            this.array = array;
            this.low = low;
            this.high = high;
        }

        @Override
        protected Extremum compute() {
            if (high - low <= THRESHOLD) {
                int minVal = array[low], minIdx = low;
                int maxVal = array[low], maxIdx = low;
                for (int i = low + 1; i < high; i++) {
                    int val = array[i];
                    if (val < minVal) {
                        minVal = val;
                        minIdx = i;
                    }
                    if (val > maxVal) {
                        maxVal = val;
                        maxIdx = i;
                    }
                }
                return new Extremum(minVal, minIdx, maxVal, maxIdx);
            }

            int mid = low + (high - low) / 2;
            MinMaxTask leftTask = new MinMaxTask(array, low, mid);
            MinMaxTask rightTask = new MinMaxTask(array, mid, high);

            leftTask.fork();
            Extremum right = rightTask.compute();
            Extremum left = leftTask.join();

            // Kết hợp kết quả cực trị
            int minVal = Math.min(left.minVal(), right.minVal());
            int minIdx = (left.minVal() < right.minVal()) ? left.minIdx() :
                         (left.minVal() > right.minVal()) ? right.minIdx() :
                         Math.min(left.minIdx(), right.minIdx());

            int maxVal = Math.max(left.maxVal(), right.maxVal());
            int maxIdx = (left.maxVal() > right.maxVal()) ? left.maxIdx() :
                         (left.maxVal() < right.maxVal()) ? right.maxIdx() :
                         Math.min(left.maxIdx(), right.maxIdx());

            return new Extremum(minVal, minIdx, maxVal, maxIdx);
        }
    }

    public static Extremum findMinMax(int[] array) {
        if (array == null || array.length == 0) {
            throw new IllegalArgumentException("Array must not be empty");
        }
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new MinMaxTask(array, 0, array.length));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N)$ phép so sánh.
- **Span ($T_\infty$):** $\mathcal{O}(\log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log N\right)$.
- **Space Complexity:** $\mathcal{O}(\log N)$ call-stack frames.

---

## 3. Parallel Prefix Sum / Scan (Thuật toán Blelloch Parallel Scan)

**Đề bài chi tiết:**
Cho mảng $A$ gồm $N$ số nguyên ($N = 2^k$). Hãy tính mảng Inclusive Prefix Sum $P$ trong đó $P[i] = \sum_{j=0}^{i} A[j]$ bằng thuật toán song song Blelloch với độ phức tạp công việc $\mathcal{O}(N)$ và thời gian tới hạn (Span) $\mathcal{O}(\log N)$.

**Phân tích thuật toán:**
Thuật toán Blelloch gồm 2 giai đoạn (Two-Pass):
1. **Up-Sweep (Reduce Phase):** Xây dựng cây nhị phân tính tổng các node con từ dưới lên. Tại mỗi bước, $d$ chạy từ $0$ đến $\log_2 N - 1$, phần tử tại $i + 2^{d+1} - 1$ nhận tổng của node trái và node phải.
2. **Down-Sweep (Distribution Phase):** Đặt phần tử gốc bằng $0$, sau đó truyền giá trị từ trên xuống. Node trái nhận giá trị của node cha hiện tại, node phải nhận tổng của node cha cộng giá trị cũ của node trái.
- Cả hai giai đoạn đều có thể song song hóa hoàn toàn bằng Fork-Join theo từng tầng của cây nhị phân.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelPrefixScan {

    private static final int THRESHOLD = 4_096;

    // Up-sweep pass (Reduce)
    private static class UpSweepTask extends RecursiveAction {
        private final long[] tree;
        private final int low, high;

        UpSweepTask(long[] tree, int low, int high) {
            this.tree = tree;
            this.low = low;
            this.high = high;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                // Tuần tự tính tổng cho đoạn nhỏ
                for (int step = 1; step < (high - low); step *= 2) {
                    for (int i = low + 2 * step - 1; i < high; i += 2 * step) {
                        tree[i] += tree[i - step];
                    }
                }
                return;
            }
            int mid = low + (high - low) / 2;
            invokeAll(new UpSweepTask(tree, low, mid), new UpSweepTask(tree, mid, high));
            tree[high - 1] += tree[mid - 1];
        }
    }

    // Down-sweep pass (Propagate)
    private static class DownSweepTask extends RecursiveAction {
        private final long[] tree;
        private final int low, high;
        private final long prefix;

        DownSweepTask(long[] tree, int low, int high, long prefix) {
            this.tree = tree;
            this.low = low;
            this.high = high;
            this.prefix = prefix;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                long running = prefix;
                for (int i = low; i < high; i++) {
                    running += tree[i];
                    tree[i] = running;
                }
                return;
            }
            int mid = low + (high - low) / 2;
            long leftSum = tree[mid - 1];
            
            DownSweepTask leftTask = new DownSweepTask(tree, low, mid, prefix);
            DownSweepTask rightTask = new DownSweepTask(tree, mid, high, prefix + leftSum);
            invokeAll(leftTask, rightTask);
        }
    }

    public static long[] computePrefixSum(int[] input) {
        int n = input.length;
        if (n == 0) return new long[0];

        long[] result = new long[n];
        for (int i = 0; i < n; i++) result[i] = input[i];

        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new UpSweepTask(result, 0, n));
            pool.invoke(new DownSweepTask(result, 0, n, 0L));
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N)$ phép toán.
- **Span ($T_\infty$):** $\mathcal{O}(\log N)$ bước đồng bộ hóa.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$ mảng lưu trữ trạng thái cây.

---

## 4. Parallel Merge Sort (Sắp xếp trộn song song với Parallel Merge)

**Đề bài chi tiết:**
Cho mảng $A$ gồm $N$ phần tử. Hãy cài đặt giải thuật Sắp xếp trộn song song (Parallel Merge Sort). Ngoài việc chia đôi và đệ quy song song 2 nửa, hãy song song hóa cả giai đoạn Merge hai mảng con đã sắp xếp để đạt Span $\mathcal{O}(\log^3 N)$.

**Phân tích thuật toán:**
- **Parallel Partitioning:** Mảng được chia thành 2 nửa và sắp xếp đệ quy song song bằng `invokeAll(leftSort, rightSort)`.
- **Parallel Merge:** Thay vì trộn tuần tự mất $\mathcal{O}(N)$ thời gian Span, ta dùng Binary Search để tìm vị trí chia đoạn của mảng thứ hai tương ứng với phần tử trung vị của mảng thứ nhất, rồi đệ quy trộn 2 cặp mảng con độc lập.
- **Ngưỡng chuyển đổi:** Với $N \le 2\,048$, sử dụng `DualPivotQuicksort` (hoặc `Arrays.sort`) để đạt tốc độ tối đa trên tập dữ liệu vừa vặn L1 cache.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelMergeSort {

    private static final int SORT_THRESHOLD = 2_048;
    private static final int MERGE_THRESHOLD = 1_024;

    public static class SortTask extends RecursiveAction {
        private final int[] src, dst;
        private final int low, high;

        public SortTask(int[] src, int[] dst, int low, int high) {
            this.src = src;
            this.dst = dst;
            this.low = low;
            this.high = high;
        }

        @Override
        protected void compute() {
            if (high - low <= SORT_THRESHOLD) {
                Arrays.sort(src, low, high);
                System.arraycopy(src, low, dst, low, high - low);
                return;
            }

            int mid = low + (high - low) / 2;
            // Đổi vai trò src và dst để tránh copy mảng liên tục
            invokeAll(
                new SortTask(dst, src, low, mid),
                new SortTask(dst, src, mid, high)
            );

            new MergeTask(src, dst, low, mid, mid, high, low).compute();
        }
    }

    public static class MergeTask extends RecursiveAction {
        private final int[] src, dst;
        private final int l1, h1, l2, h2, outLow;

        public MergeTask(int[] src, int[] dst, int l1, int h1, int l2, int h2, int outLow) {
            this.src = src;
            this.dst = dst;
            this.l1 = l1;
            this.h1 = h1;
            this.l2 = l2;
            this.h2 = h2;
            this.outLow = outLow;
        }

        @Override
        protected void compute() {
            int len1 = h1 - l1;
            int len2 = h2 - l2;

            if (len1 + len2 <= MERGE_THRESHOLD) {
                sequentialMerge();
                return;
            }

            // Đảm bảo đoạn 1 luôn dài hơn hoặc bằng đoạn 2
            if (len1 < len2) {
                new MergeTask(src, dst, l2, h2, l1, h1, outLow).compute();
                return;
            }

            int mid1 = l1 + len1 / 2;
            int pivot = src[mid1];
            int mid2 = binarySearch(src, l2, h2, pivot);

            int outMid = outLow + (mid1 - l1) + (mid2 - l2);
            dst[outMid] = pivot;

            invokeAll(
                new MergeTask(src, dst, l1, mid1, l2, mid2, outLow),
                new MergeTask(src, dst, mid1 + 1, h1, mid2, h2, outMid + 1)
            );
        }

        private int binarySearch(int[] arr, int from, int to, int key) {
            int low = from, high = to - 1;
            while (low <= high) {
                int mid = (low + high) >>> 1;
                if (arr[mid] < key) low = mid + 1;
                else high = mid - 1;
            }
            return low;
        }

        private void sequentialMerge() {
            int i = l1, j = l2, k = outLow;
            while (i < h1 && j < h2) {
                dst[k++] = (src[i] <= src[j]) ? src[i++] : src[j++];
            }
            while (i < h1) dst[k++] = src[i++];
            while (j < h2) dst[k++] = src[j++];
        }
    }

    public static void sort(int[] array) {
        if (array == null || array.length <= 1) return;
        int[] aux = array.clone();
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new SortTask(aux, array, 0, array.length));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \log N)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log^3 N)$ (Do cả sort và merge đều song song hóa).
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log N}{P} + \log^3 N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$ cho mảng phụ `aux`.

---

## 5. Parallel Quickselect (K-th Order Statistic song song)

**Đề bài chi tiết:**
Cho mảng số nguyên chưa sắp xếp $A$ có $N$ phần tử và số nguyên $k$ ($0 \le k < N$). Hãy tìm phần tử nhỏ thứ $k$ (0-indexed) trong mảng sử dụng thuật toán Quickselect song song với 3-Way Partitioning để xử lý hiệu quả mảng chứa nhiều phần tử trùng lặp.

**Phân tích thuật toán:**
- **Parallel Counting & Filtering:** Tại mỗi bước chọn pivot $P$, thay vì phân hoạch in-place trên 1 luồng, ta dùng Fork-Join để đếm song song số lượng phần tử $< P$, $= P$, $> P$ và trích xuất song song vào các vùng đệm con.
- **Recursive Reduction:** Dựa trên số lượng phần tử của 3 tập ($L, E, G$), xác định tập con chứa phần tử thứ $k$ và tiếp tục đệ quy trên tập đó.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ThreadLocalRandom;

public class ParallelQuickselect {

    private static final int THRESHOLD = 16_384;

    public record PartitionCounts(int lessCount, int equalCount, int greaterCount) {}

    public static class CountTask extends RecursiveTask<PartitionCounts> {
        private final int[] array;
        private final int low, high, pivot;

        public CountTask(int[] array, int low, int high, int pivot) {
            this.array = array;
            this.low = low;
            this.high = high;
            this.pivot = pivot;
        }

        @Override
        protected PartitionCounts compute() {
            if (high - low <= THRESHOLD) {
                int l = 0, e = 0, g = 0;
                for (int i = low; i < high; i++) {
                    int val = array[i];
                    if (val < pivot) l++;
                    else if (val == pivot) e++;
                    else g++;
                }
                return new PartitionCounts(l, e, g);
            }

            int mid = low + (high - low) / 2;
            CountTask leftTask = new CountTask(array, low, mid, pivot);
            CountTask rightTask = new CountTask(array, mid, high, pivot);

            leftTask.fork();
            PartitionCounts r = rightTask.compute();
            PartitionCounts l = leftTask.join();

            return new PartitionCounts(
                l.lessCount() + r.lessCount(),
                l.equalCount() + r.equalCount(),
                l.greaterCount() + r.greaterCount()
            );
        }
    }

    public static int select(int[] array, int k) {
        if (k < 0 || k >= array.length) throw new IndexOutOfBoundsException();
        int[] current = array.clone();
        int low = 0, high = current.length;

        try (ForkJoinPool pool = new ForkJoinPool()) {
            while (high - low > THRESHOLD) {
                int pivotIdx = low + ThreadLocalRandom.current().nextInt(high - low);
                int pivot = current[pivotIdx];

                PartitionCounts counts = pool.invoke(new CountTask(current, low, high, pivot));

                if (k < counts.lessCount()) {
                    current = filter(current, low, high, pivot, -1, counts.lessCount());
                    low = 0;
                    high = current.length;
                } else if (k < counts.lessCount() + counts.equalCount()) {
                    return pivot;
                } else {
                    k -= (counts.lessCount() + counts.equalCount());
                    current = filter(current, low, high, pivot, 1, counts.greaterCount());
                    low = 0;
                    high = current.length;
                }
            }
        }

        java.util.Arrays.sort(current, low, high);
        return current[low + k];
    }

    private static int[] filter(int[] arr, int low, int high, int pivot, int sign, int size) {
        int[] result = new int[size];
        int idx = 0;
        for (int i = low; i < high; i++) {
            if ((sign < 0 && arr[i] < pivot) || (sign > 0 && arr[i] > pivot)) {
                result[idx++] = arr[i];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** Trung bình $\mathcal{O}(N)$, Tệ nhất $\mathcal{O}(N^2)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$ trung bình.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$ do cấp phát mảng lọc trung gian.

---

## 6. Parallel Block Matrix Multiplication (Nhân ma trận khối song song)

**Đề bài chi tiết:**
Cho hai ma trận vuông $A$ và $B$ kích thước $N \times N$ ($N$ là lũy thừa của 2). Hãy tính ma trận tích $C = A \times B$ bằng giải thuật Divide-and-Conquer song song chia ma trận thành các khối $2 \times 2$ (Block Matrix Decomposition).

**Phân tích thuật toán:**
- **Phân rã ma trận:**
  $$
  \begin{pmatrix} C_{11} & C_{12} \\ C_{21} & C_{22} \end{pmatrix} = \begin{pmatrix} A_{11} & A_{12} \\ A_{21} & A_{22} \end{pmatrix} \times \begin{pmatrix} B_{11} & B_{12} \\ B_{21} & B_{22} \end{pmatrix}
  $$
  Tạo ra 8 phép nhân ma trận con kích thước $\frac{N}{2} \times \frac{N}{2}$ độc lập và 4 phép cộng ma trận.
- **Ngưỡng chuyển đổi:** Khi $N \le 64$, chuyển sang giải thuật lặp tuần tự có tối ưu hoá thứ tự vòng lặp `i-k-j` (IKJ loop order) để tận dụng CPU Cache và chống Cache Miss.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelMatrixMultiplication {

    private static final int THRESHOLD = 64;

    public static class MatMulTask extends RecursiveAction {
        private final double[][] A, B, C;
        private final int aRow, aCol, bRow, bCol, cRow, cCol, size;

        public MatMulTask(double[][] A, double[][] B, double[][] C,
                          int aRow, int aCol, int bRow, int bCol,
                          int cRow, int cCol, int size) {
            this.A = A; this.B = B; this.C = C;
            this.aRow = aRow; this.aCol = aCol;
            this.bRow = bRow; this.bCol = bCol;
            this.cRow = cRow; this.cCol = cCol;
            this.size = size;
        }

        @Override
        protected void compute() {
            if (size <= THRESHOLD) {
                // IKJ Loop tiling for optimal cache line utilization
                for (int i = 0; i < size; i++) {
                    for (int k = 0; k < size; k++) {
                        double r = A[aRow + i][aCol + k];
                        for (int j = 0; j < size; j++) {
                            C[cRow + i][cCol + j] += r * B[bRow + k][bCol + j];
                        }
                    }
                }
                return;
            }

            int h = size / 2;
            // 8 submatrix multiplication tasks
            invokeAll(
                new MatMulTask(A, B, C, aRow, aCol, bRow, bCol, cRow, cCol, h),
                new MatMulTask(A, B, C, aRow, aCol + h, bRow + h, bCol, cRow, cCol, h),
                new MatMulTask(A, B, C, aRow, aCol, bRow, bCol + h, cRow, cCol + h, h),
                new MatMulTask(A, B, C, aRow, aCol + h, bRow + h, bCol + h, cRow, cCol + h, h),
                new MatMulTask(A, B, C, aRow + h, aCol, bRow, bCol, cRow + h, cCol, h),
                new MatMulTask(A, B, C, aRow + h, aCol + h, bRow + h, bCol, cRow + h, cCol, h),
                new MatMulTask(A, B, C, aRow + h, aCol, bRow, bCol + h, cRow + h, cCol, h),
                new MatMulTask(A, B, C, aRow + h, aCol + h, bRow + h, bCol + h, cRow + h, cCol, h)
            );
        }
    }

    public static double[][] multiply(double[][] A, double[][] B) {
        int n = A.length;
        double[][] C = new double[n][n];
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new MatMulTask(A, B, C, 0, 0, 0, 0, 0, 0, n));
        }
        return C;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N^3)$ (hoặc $\mathcal{O}(N^{2.81})$ nếu dùng Strassen).
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$ hoặc $\mathcal{O}(\log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N^3}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N^2)$ cho ma trận kết quả $C$.

---

## 7. Parallel Fibonacci & Task Granularity Tuning (Điều chỉnh độ mịn tác vụ)

**Đề bài chi tiết:**
Hãy tính số Fibonacci thứ $N$ ($F_N$) bằng mô hình Fork-Join đệ quy nhằm minh họa cơ chế Work-Stealing và phân tích ảnh hưởng nghiêm trọng của việc không thiết lập Threshold (Granularity Tuning).

**Phân tích thuật toán:**
- **Vấn đề Granularity:** Cây đệ quy của Fibonacci có $2^N$ nút. Nếu fork mọi nút cho đến $N \le 1$, chi phí cấp phát đối tượng trên Heap và quản lý Deque sẽ làm sụp đổ hiệu năng hệ thống.
- **Tối ưu hóa Cutoff:** Thiết lập $N \le 25$ để tính tuần tự bằng mảng hoặc biến tích luỹ, biến bài toán tính $F_{45}$ từ vài phút xuống còn vài mili-giây.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelFibonacci {

    private static final int SEQUENTIAL_THRESHOLD = 25;

    public static class FibTask extends RecursiveTask<Long> {
        private final int n;

        public FibTask(int n) {
            this.n = n;
        }

        @Override
        protected Long compute() {
            if (n <= SEQUENTIAL_THRESHOLD) {
                return computeSequential(n);
            }

            FibTask f1 = new FibTask(n - 1);
            FibTask f2 = new FibTask(n - 2);

            f1.fork();
            long r2 = f2.compute();
            long r1 = f1.join();

            return r1 + r2;
        }

        private long computeSequential(int k) {
            if (k <= 1) return k;
            long a = 0, b = 1;
            for (int i = 2; i <= k; i++) {
                long c = a + b;
                a = b;
                b = c;
            }
            return b;
        }
    }

    public static long getFibonacci(int n) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new FibTask(n));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(\phi^N)$ với $\phi \approx 1.618$ (hoặc $\mathcal{O}(N)$ sau ngưỡng tuần tự).
- **Span ($T_\infty$):** $\mathcal{O}(N)$.
- **Space Complexity:** $\mathcal{O}(N)$ kích thước call-stack.

---

## 8. Parallel Image Gaussian Blur (2D Convolution / Stencil song song)

**Đề bài chi tiết:**
Cho một ma trận ảnh $H \times W$ biểu diễn bởi mảng điểm ảnh 1D `int[] pixels` (định dạng ARGB). Hãy áp dụng bộ lọc làm mờ Gaussian Blur kích thước kernel $K \times K$ song song bằng Fork-Join Framework.

**Phân tích thuật toán:**
- **Tách lọc 2 chiều (Separable Filter):** Gaussian Blur 2D có thể tách thành 2 bước lọc 1D liên tiếp: Horizontal Blur theo hàng, sau đó Vertical Blur theo cột.
- **Phân rã không gian:** Chia ảnh thành các dải sọc ngang/dọc (Slab Decomposition) để mỗi lõi CPU xử lý một dải độc lập, không cần khóa đồng bộ (Lock-free) vì dữ liệu đầu vào là Read-only và ghi vào mảng đích riêng biệt.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelGaussianBlur {

    private static final int ROW_THRESHOLD = 64;

    public static class BlurHorizontalTask extends RecursiveAction {
        private final int[] src, dst;
        private final int width, height, radius;
        private final int startRow, endRow;
        private final float[] kernel;

        public BlurHorizontalTask(int[] src, int[] dst, int width, int height,
                                  int radius, float[] kernel, int startRow, int endRow) {
            this.src = src; this.dst = dst;
            this.width = width; this.height = height;
            this.radius = radius; this.kernel = kernel;
            this.startRow = startRow; this.endRow = endRow;
        }

        @Override
        protected void compute() {
            if (endRow - startRow <= ROW_THRESHOLD) {
                for (int y = startRow; y < endRow; y++) {
                    for (int x = 0; x < width; x++) {
                        float r = 0, g = 0, b = 0;
                        for (int k = -radius; k <= radius; k++) {
                            int px = Math.min(Math.max(x + k, 0), width - 1);
                            int rgb = src[y * width + px];
                            float weight = kernel[k + radius];
                            r += ((rgb >> 16) & 0xFF) * weight;
                            g += ((rgb >> 8) & 0xFF) * weight;
                            b += (rgb & 0xFF) * weight;
                        }
                        int ir = Math.min(255, (int) r);
                        int ig = Math.min(255, (int) g);
                        int ib = Math.min(255, (int) b);
                        dst[y * width + x] = (0xFF << 24) | (ir << 16) | (ig << 8) | ib;
                    }
                }
                return;
            }

            int mid = startRow + (endRow - startRow) / 2;
            invokeAll(
                new BlurHorizontalTask(src, dst, width, height, radius, kernel, startRow, mid),
                new BlurHorizontalTask(src, dst, width, height, radius, kernel, mid, endRow)
            );
        }
    }

    public static int[] blur(int[] pixels, int width, int height, int radius) {
        int[] temp = new int[pixels.length];
        int[] result = new int[pixels.length];
        float[] kernel = createGaussianKernel(radius);

        try (ForkJoinPool pool = new ForkJoinPool()) {
            // Horizontal Pass
            pool.invoke(new BlurHorizontalTask(pixels, temp, width, height, radius, kernel, 0, height));
            // Vertical Pass có thể cài đặt tương tự đổi chiều x, y
        }
        return temp;
    }

    private static float[] createGaussianKernel(int radius) {
        int size = radius * 2 + 1;
        float[] k = new float[size];
        float sigma = radius / 3.0f;
        float sum = 0;
        for (int i = -radius; i <= radius; i++) {
            float val = (float) Math.exp(-(i * i) / (2 * sigma * sigma));
            k[i + radius] = val;
            sum += val;
        }
        for (int i = 0; i < size; i++) k[i] /= sum;
        return k;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(W \times H \times K)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log H + K)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{W \cdot H \cdot K}{P} + \log H\right)$.
- **Space Complexity:** $\mathcal{O}(W \times H)$ cho mảng đệm.

---

## 9. Parallel Directory File Tree Size Scanner (Quét dung lượng cây thư mục)

**Đề bài chi tiết:**
Cho một đường dẫn thư mục gốc `Path root`. Hãy quét đệ quy toàn bộ cây thư mục và tính tổng kích thước (tính theo byte) của tất cả các file bên trong bằng mô hình song song `RecursiveTask`.

**Phân tích thuật toán:**
- **Bất đối xứng I/O và Đệ quy:** Cây thư mục có cấu trúc phân nhánh không đều (unbalanced tree). Khi gặp thư mục con, tạo subtask và `fork()` vào pool; khi gặp file thông thường, cộng dồn kích thước.
- **Xử lý Work-Stealing:** Các luồng xử lý thư mục sâu sẽ tự động nhả bớt các nhánh con sang cho luồng rảnh rỗi qua cơ chế Work-Stealing Deque.

**Mã nguồn Java:**
```java
import java.io.IOException;
import java.nio.file.DirectoryStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelDirectoryScanner {

    public static class DirectorySizeTask extends RecursiveTask<Long> {
        private final Path path;

        public DirectorySizeTask(Path path) {
            this.path = path;
        }

        @Override
        protected Long compute() {
            long totalSize = 0L;
            List<DirectorySizeTask> subTasks = new ArrayList<>();

            try (DirectoryStream<Path> stream = Files.newDirectoryStream(path)) {
                for (Path entry : stream) {
                    if (Files.isDirectory(entry)) {
                        DirectorySizeTask task = new DirectorySizeTask(entry);
                        task.fork(); // Đẩy nhánh con sang luồng khác
                        subTasks.add(task);
                    } else {
                        totalSize += Files.size(entry);
                    }
                }
            } catch (IOException | SecurityException ignored) {
                // Bỏ qua các file/thư mục không có quyền truy cập
            }

            for (DirectorySizeTask task : subTasks) {
                totalSize += task.join();
            }

            return totalSize;
        }
    }

    public static long calculateDirectorySize(Path root) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new DirectorySizeTask(root));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(F + D)$ với $F$ là số file, $D$ là số thư mục.
- **Span ($T_\infty$):** $\mathcal{O}(\text{MaxDepth})$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{F+D}{P} + \text{MaxDepth}\right)$ (phụ thuộc tốc độ I/O đĩa).
- **Space Complexity:** $\mathcal{O}(D)$ bộ nhớ task objects.

---

## 10. Parallel Histogram Count (Đếm tần suất đa lớp song song)

**Đề bài chi tiết:**
Cho mảng $A$ gồm $N$ số nguyên trong khoảng $[0, K - 1]$. Hãy tính mảng tần suất (Histogram) $H$ kích thước $K$ trong đó $H[v]$ là số lần xuất hiện của giá trị $v$ trong mảng mà không gặp tình trạng Contentious Locks trên biến đếm chung.

**Phân tích thuật toán:**
- **Tránh Lock Contention & False Sharing:** Nếu tất cả các luồng cùng cập nhật một mảng `AtomicIntegerArray` hay dùng lock trên từng bin, bus bộ nhớ sẽ bị nghẽn (Cache Coherence Traffic).
- **Local Histogram & Parallel Reduction:** Mỗi leaf task tính một mảng `int[K]` cục bộ riêng biệt, sau đó giai đoạn Join sẽ gộp 2 mảng $K$ phần tử bằng phép cộng song song hoặc tuần tự tùy $K$.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelHistogram {

    private static final int THRESHOLD = 32_768;

    public static class HistogramTask extends RecursiveTask<int[]> {
        private final int[] data;
        private final int low, high, numBins;

        public HistogramTask(int[] data, int low, int high, int numBins) {
            this.data = data;
            this.low = low;
            this.high = high;
            this.numBins = numBins;
        }

        @Override
        protected int[] compute() {
            if (high - low <= THRESHOLD) {
                int[] localHist = new int[numBins];
                for (int i = low; i < high; i++) {
                    int val = data[i];
                    if (val >= 0 && val < numBins) {
                        localHist[val]++;
                    }
                }
                return localHist;
            }

            int mid = low + (high - low) / 2;
            HistogramTask leftTask = new HistogramTask(data, low, mid, numBins);
            HistogramTask rightTask = new HistogramTask(data, mid, high, numBins);

            leftTask.fork();
            int[] rightHist = rightTask.compute();
            int[] leftHist = leftTask.join();

            // Merge 2 histograms
            int[] merged = new int[numBins];
            for (int i = 0; i < numBins; i++) {
                merged[i] = leftHist[i] + rightHist[i];
            }
            return merged;
        }
    }

    public static int[] computeHistogram(int[] data, int numBins) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new HistogramTask(data, 0, data.length, numBins));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N + K \cdot \frac{N}{\text{THRESHOLD}})$.
- **Span ($T_\infty$):** $\mathcal{O}(\log N \cdot K)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + K \log N\right)$.
- **Space Complexity:** $\mathcal{O}(K \cdot \log N)$ cho mảng cục bộ tại các node.

---

## 11. Parallel Monte Carlo $\pi$ Estimation (Mô phỏng Monte Carlo xấp xỉ Pi)

**Đề bài chi tiết:**
Hãy ước lượng giá trị của số $\pi$ bằng phương pháp mô phỏng Monte Carlo với $N$ điểm ngẫu nhiên ($N = 10^9$) bằng Fork-Join Framework, đảm bảo tính độc lập thống kê giữa các luồng bằng `SplittableRandom`.

**Phân tích thuật toán:**
- **Pseudo-Random Number Generator (PRNG) Contention:** Dùng chung `java.util.Random` sẽ gây tranh chấp Atomic CAS. Sử dụng `SplittableRandom.split()` để tạo PRNG độc lập tuyệt đối cho mỗi subtask mà không cần đồng bộ.
- **Quy tắc hình học:** Một điểm ngẫu nhiên $(x, y) \in [0, 1]^2$ nằm trong góc phần tư đường tròn nếu $x^2 + y^2 \le 1$. Số $\pi \approx 4 \times \frac{\text{inCircle}}{\text{totalPoints}}$.

**Mã nguồn Java:**
```java
import java.util.SplittableRandom;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelMonteCarloPi {

    private static final long THRESHOLD = 5_000_000L;

    public static class MonteCarloTask extends RecursiveTask<Long> {
        private final long iterations;
        private final SplittableRandom random;

        public MonteCarloTask(long iterations, SplittableRandom random) {
            this.iterations = iterations;
            this.random = random;
        }

        @Override
        protected Long compute() {
            if (iterations <= THRESHOLD) {
                long insideCount = 0;
                for (long i = 0; i < iterations; i++) {
                    double x = random.nextDouble();
                    double y = random.nextDouble();
                    if (x * x + y * y <= 1.0) {
                        insideCount++;
                    }
                }
                return insideCount;
            }

            long half = iterations / 2;
            MonteCarloTask leftTask = new MonteCarloTask(half, random.split());
            MonteCarloTask rightTask = new MonteCarloTask(iterations - half, random.split());

            leftTask.fork();
            long rightResult = rightTask.compute();
            long leftResult = leftTask.join();

            return leftResult + rightResult;
        }
    }

    public static double estimatePi(long totalIterations) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            long inside = pool.invoke(new MonteCarloTask(totalIterations, new SplittableRandom()));
            return 4.0 * inside / totalIterations;
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log N\right)$.
- **Space Complexity:** $\mathcal{O}(\log N)$ task objects.

---

## 12. Parallel Merge of Two Sorted Arrays (Trộn 2 mảng đã sắp xếp song song)

**Đề bài chi tiết:**
Cho hai mảng đã sắp xếp tăng dần $A$ kích thước $M$ và $B$ kích thước $N$. Hãy trộn hai mảng thành một mảng đã sắp xếp $C$ kích thước $M + N$ bằng giải thuật song song Divide-and-Conquer với Span $\mathcal{O}(\log^2(M + N))$.

**Phân tích thuật toán:**
- Tìm phần tử trung vị của mảng lớn hơn (giả sử $A[midA]$).
- Dùng Binary Search tìm vị trí chèn của $A[midA]$ trong mảng $B$, chia $B$ thành 2 phần $B[0..idxB-1]$ và $B[idxB..N-1]$.
- Phần tử $A[midA]$ được đặt chính xác tại vị trí $midA + idxB$ trong mảng kết quả $C$.
- Hai nửa con được trộn hoàn toàn độc lập và song song bằng `invokeAll`.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelArrayMerge {

    private static final int THRESHOLD = 1_024;

    public static class MergeSubtask extends RecursiveAction {
        private final int[] A, B, C;
        private final int aLow, aHigh, bLow, bHigh, cLow;

        public MergeSubtask(int[] A, int aLow, int aHigh,
                            int[] B, int bLow, int bHigh,
                            int[] C, int cLow) {
            this.A = A; this.aLow = aLow; this.aHigh = aHigh;
            this.B = B; this.bLow = bLow; this.bHigh = bHigh;
            this.C = C; this.cLow = cLow;
        }

        @Override
        protected void compute() {
            int aLen = aHigh - aLow;
            int bLen = bHigh - bLow;

            if (aLen + bLen <= THRESHOLD) {
                sequentialMerge();
                return;
            }

            if (aLen < bLen) {
                new MergeSubtask(B, bLow, bHigh, A, aLow, aHigh, C, cLow).compute();
                return;
            }

            int aMid = aLow + aLen / 2;
            int pivot = A[aMid];
            int bMid = binarySearch(B, bLow, bHigh, pivot);

            int cMid = cLow + (aMid - aLow) + (bMid - bLow);
            C[cMid] = pivot;

            invokeAll(
                new MergeSubtask(A, aLow, aMid, B, bLow, bMid, C, cLow),
                new MergeSubtask(A, aMid + 1, aHigh, B, bMid, bHigh, C, cMid + 1)
            );
        }

        private int binarySearch(int[] arr, int from, int to, int key) {
            int low = from, high = to - 1;
            while (low <= high) {
                int mid = (low + high) >>> 1;
                if (arr[mid] < key) low = mid + 1;
                else high = mid - 1;
            }
            return low;
        }

        private void sequentialMerge() {
            int i = aLow, j = bLow, k = cLow;
            while (i < aHigh && j < bHigh) {
                C[k++] = (A[i] <= B[j]) ? A[i++] : B[j++];
            }
            while (i < aHigh) C[k++] = A[i++];
            while (j < bHigh) C[k++] = B[j++];
        }
    }

    public static int[] mergeSortedArrays(int[] A, int[] B) {
        int[] C = new int[A.length + B.length];
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new MergeSubtask(A, 0, A.length, B, 0, B.length, C, 0));
        }
        return C;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(M + N)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log(M + N) \cdot \log(\min(M, N))) = \mathcal{O}(\log^2(M + N))$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{M+N}{P} + \log^2(M+N)\right)$.
- **Space Complexity:** $\mathcal{O}(M + N)$ cho mảng kết quả $C$.

---

## 13. Parallel Word Frequency Count (Đếm tần số từ MapReduce song song)

**Đề bài chi tiết:**
Cho một tập hợp $N$ đoạn văn bản (Documents). Hãy thống kê số lần xuất hiện của tất cả các từ đơn (Word Frequency Map) theo mô hình Map-Reduce bộ nhớ trong dùng `RecursiveTask`.

**Phân tích thuật toán:**
- **Giai đoạn Map (Phân tách):** Mỗi leaf task bóc tách từng từ trong khối văn bản và đưa vào một `Map<String, Integer>` cục bộ.
- **Giai đoạn Reduce (Hợp nhất):** Hợp nhất 2 Map bằng cách gộp các entry có khóa nhỏ hơn sang khóa lớn hơn (`mapA.merge(k, v, Integer::sum)`).

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelWordCount {

    private static final int THRESHOLD = 500;

    public static class WordCountTask extends RecursiveTask<Map<String, Integer>> {
        private final String[] documents;
        private final int low, high;

        public WordCountTask(String[] documents, int low, int high) {
            this.documents = documents;
            this.low = low;
            this.high = high;
        }

        @Override
        protected Map<String, Integer> compute() {
            if (high - low <= THRESHOLD) {
                Map<String, Integer> localMap = new HashMap<>();
                for (int i = low; i < high; i++) {
                    String[] words = documents[i].toLowerCase().split("\\W+");
                    for (String w : words) {
                        if (!w.isBlank()) {
                            localMap.merge(w, 1, Integer::sum);
                        }
                    }
                }
                return localMap;
            }

            int mid = low + (high - low) / 2;
            WordCountTask leftTask = new WordCountTask(documents, low, mid);
            WordCountTask rightTask = new WordCountTask(documents, mid, high);

            leftTask.fork();
            Map<String, Integer> rightMap = rightTask.compute();
            Map<String, Integer> leftMap = leftTask.join();

            // Merge Map nhỏ vào Map lớn để tối ưu chi phí copy
            Map<String, Integer> target = (leftMap.size() >= rightMap.size()) ? leftMap : rightMap;
            Map<String, Integer> source = (target == leftMap) ? rightMap : leftMap;

            source.forEach((k, v) -> target.merge(k, v, Integer::sum));
            return target;
        }
    }

    public static Map<String, Integer> countWords(String[] documents) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new WordCountTask(documents, 0, documents.length));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(W)$ với $W$ là tổng số từ trong toàn bộ văn bản.
- **Span ($T_\infty$):** $\mathcal{O}(U \log D)$ với $U$ là số từ unique, $D$ là số tài liệu.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{W}{P} + U \log D\right)$.
- **Space Complexity:** $\mathcal{O}(U \cdot \log D)$ heap memory.

---

## 14. Parallel Breadth-First Search (Level-Synchronous Parallel BFS)

**Đề bài chi tiết:**
Cho đồ thị vô hướng $G = (V, E)$ gồm $|V|$ đỉnh và $|E|$ cạnh. Hãy tính khoảng cách ngắn nhất từ đỉnh nguồn $S$ tới tất cả các đỉnh bằng thuật toán Level-Synchronous Parallel BFS.

**Phân tích thuật toán:**
- **Phân tầng (Level-by-Level):** BFS duyệt đồ thị theo từng tầng khoảng cách $d = 0, 1, 2, \dots$. Mọi đỉnh trong frontier hiện tại $F_d$ có thể mở rộng các đỉnh kề độc lập và song song.
- **Tránh trùng lặp:** Dùng mảng `AtomicIntegerArray` lưu khoảng cách `dist[]` khởi tạo bằng `-1`. Khi duyệt đỉnh kề $v$, sử dụng `compareAndSet(-1, currentDist + 1)` để đảm bảo chỉ có đúng 1 luồng gán thành công đỉnh $v$ vào frontier tiếp theo $F_{d+1}$.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;
import java.util.concurrent.atomic.AtomicIntegerArray;

public class ParallelBFS {

    private static final int THRESHOLD = 2_048;

    public static class ExpandFrontierTask extends RecursiveAction {
        private final List<Integer>[] adj;
        private final int[] currentFrontier;
        private final int low, high, nextDist;
        private final AtomicIntegerArray dist;
        private final List<Integer> localNext;

        public ExpandFrontierTask(List<Integer>[] adj, int[] currentFrontier,
                                  int low, int high, int nextDist,
                                  AtomicIntegerArray dist, List<Integer> localNext) {
            this.adj = adj;
            this.currentFrontier = currentFrontier;
            this.low = low;
            this.high = high;
            this.nextDist = nextDist;
            this.dist = dist;
            this.localNext = localNext;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                for (int i = low; i < high; i++) {
                    int u = currentFrontier[i];
                    for (int v : adj[u]) {
                        if (dist.compareAndSet(v, -1, nextDist)) {
                            localNext.add(v);
                        }
                    }
                }
                return;
            }

            int mid = low + (high - low) / 2;
            List<Integer> leftLocal = new ArrayList<>();
            List<Integer> rightLocal = new ArrayList<>();

            invokeAll(
                new ExpandFrontierTask(adj, currentFrontier, low, mid, nextDist, dist, leftLocal),
                new ExpandFrontierTask(adj, currentFrontier, mid, high, nextDist, dist, rightLocal)
            );

            synchronized (localNext) {
                localNext.addAll(leftLocal);
                localNext.addAll(rightLocal);
            }
        }
    }

    public static int[] bfs(List<Integer>[] adj, int source) {
        int n = adj.length;
        AtomicIntegerArray dist = new AtomicIntegerArray(n);
        for (int i = 0; i < n; i++) dist.set(i, -1);
        dist.set(source, 0);

        int[] frontier = new int[]{source};
        int currentDist = 0;

        try (ForkJoinPool pool = new ForkJoinPool()) {
            while (frontier.length > 0) {
                List<Integer> nextFrontierList = new ArrayList<>();
                pool.invoke(new ExpandFrontierTask(adj, frontier, 0, frontier.length,
                                                   currentDist + 1, dist, nextFrontierList));

                frontier = nextFrontierList.stream().mapToInt(i -> i).toArray();
                currentDist++;
            }
        }

        int[] finalDist = new int[n];
        for (int i = 0; i < n; i++) finalDist[i] = dist.get(i);
        return finalDist;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(|V| + |E|)$.
- **Span ($T_\infty$):** $\mathcal{O}(D \cdot \log |V|)$ với $D$ là đường kính (Diameter) của đồ thị.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{|V| + |E|}{P} + D \log |V|\right)$.
- **Space Complexity:** $\mathcal{O}(|V| + |E|)$.

---

## 15. Parallel Segmented Sieve of Eratosthenes (Sàng số nguyên tố phân đoạn song song)

**Đề bài chi tiết:**
Cho số nguyên $N$ ($1 \le N \le 10^9$). Hãy đếm số lượng các số nguyên tố trong khoảng $[1, N]$ bằng thuật toán Sàng phân đoạn song song (Parallel Segmented Sieve).

**Phân tích thuật toán:**
- **Base Primes:** Tìm tất cả số nguyên tố $\le \sqrt{N}$ tuần tự bằng sàng chuẩn.
- **Segment Independence:** Khoảng $[\sqrt{N}, N]$ được chia thành các đoạn có kích thước $L \approx 10^5$ (vừa vặn L1 Data Cache). Mỗi đoạn $[low, high]$ được một tác vụ `RecursiveTask` sàng hoàn toàn độc lập mà không cần truyền thông liên luồng.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelSegmentedSieve {

    private static final int SEGMENT_SIZE = 131_072; // 128 KB chunk

    public static class SieveSegmentTask extends RecursiveTask<Long> {
        private final int[] basePrimes;
        private final long rangeLow, rangeHigh;

        public SieveSegmentTask(int[] basePrimes, long rangeLow, long rangeHigh) {
            this.basePrimes = basePrimes;
            this.rangeLow = rangeLow;
            this.rangeHigh = rangeHigh;
        }

        @Override
        protected Long compute() {
            if (rangeHigh - rangeLow <= SEGMENT_SIZE) {
                int len = (int) (rangeHigh - rangeLow);
                boolean[] isComposite = new boolean[len];

                for (int p : basePrimes) {
                    long start = Math.max((long) p * p, ((rangeLow + p - 1) / p) * p);
                    for (long j = start; j < rangeHigh; j += p) {
                        isComposite[(int) (j - rangeLow)] = true;
                    }
                }

                long count = 0;
                for (int i = 0; i < len; i++) {
                    if (!isComposite[i] && (rangeLow + i) >= 2) {
                        count++;
                    }
                }
                return count;
            }

            long mid = rangeLow + (rangeHigh - rangeLow) / 2;
            SieveSegmentTask leftTask = new SieveSegmentTask(basePrimes, rangeLow, mid);
            SieveSegmentTask rightTask = new SieveSegmentTask(basePrimes, mid, rangeHigh);

            leftTask.fork();
            long r = rightTask.compute();
            long l = leftTask.join();

            return l + r;
        }
    }

    public static long countPrimes(long N) {
        if (N < 2) return 0;
        int limit = (int) Math.sqrt(N);
        int[] basePrimes = getSimplePrimes(limit);

        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new SieveSegmentTask(basePrimes, 2, N + 1));
        }
    }

    private static int[] getSimplePrimes(int limit) {
        boolean[] comp = new boolean[limit + 1];
        List<Integer> primes = new ArrayList<>();
        for (int p = 2; p <= limit; p++) {
            if (!comp[p]) {
                primes.add(p);
                for (long j = (long) p * p; j <= limit; j += p) {
                    comp[(int) j] = true;
                }
            }
        }
        return primes.stream().mapToInt(i -> i).toArray();
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \log \log N)$.
- **Span ($T_\infty$):** $\mathcal{O}\left(\sqrt{N} + \log\left(\frac{N}{\text{SEGMENT\_SIZE}}\right)\right)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log \log N}{P} + \sqrt{N}\right)$.
- **Space Complexity:** $\mathcal{O}(\sqrt{N})$ bộ nhớ base primes.

---

## 16. Parallel Document Inverted Index Builder (Xây dựng chỉ mục đảo song song)

**Đề bài chi tiết:**
Cho danh sách $N$ văn bản, mỗi văn bản có `docId` và nội dung chuỗi. Hãy xây dựng bảng chỉ mục đảo (Inverted Index) ánh xạ mỗi từ khóa `term` tới danh sách các `docId` chứa từ khóa đó theo mô hình song song.

**Phân tích thuật toán:**
- **Map Phase:** Mỗi luồng xử lý một phân đoạn tài liệu, tạo một cục bộ `Map<String, List<Integer>>`.
- **Reduce Phase:** Trộn đệ quy hai chỉ mục đảo bằng cách gộp danh sách các `docId` (do danh sách `docId` được duyệt theo thứ tự tăng dần, việc gộp tương tự phép Merge trong Merge Sort).

**Mã nguồn Java:**
```java
import java.util.*;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelInvertedIndex {

    public record Document(int id, String text) {}

    private static final int THRESHOLD = 250;

    public static class IndexTask extends RecursiveTask<Map<String, List<Integer>>> {
        private final Document[] docs;
        private final int low, high;

        public IndexTask(Document[] docs, int low, int high) {
            this.docs = docs;
            this.low = low;
            this.high = high;
        }

        @Override
        protected Map<String, List<Integer>> compute() {
            if (high - low <= THRESHOLD) {
                Map<String, List<Integer>> localIndex = new HashMap<>();
                for (int i = low; i < high; i++) {
                    Document doc = docs[i];
                    String[] tokens = doc.text().toLowerCase().split("\\W+");
                    Set<String> uniqueTerms = new HashSet<>(Arrays.asList(tokens));
                    for (String term : uniqueTerms) {
                        if (!term.isBlank()) {
                            localIndex.computeIfAbsent(term, k -> new ArrayList<>()).add(doc.id());
                        }
                    }
                }
                return localIndex;
            }

            int mid = low + (high - low) / 2;
            IndexTask leftTask = new IndexTask(docs, low, mid);
            IndexTask rightTask = new IndexTask(docs, mid, high);

            leftTask.fork();
            Map<String, List<Integer>> rMap = rightTask.compute();
            Map<String, List<Integer>> lMap = leftTask.join();

            // Merge Map
            Map<String, List<Integer>> target = (lMap.size() >= rMap.size()) ? lMap : rMap;
            Map<String, List<Integer>> source = (target == lMap) ? rMap : lMap;

            source.forEach((k, v) -> target.computeIfAbsent(k, key -> new ArrayList<>()).addAll(v));
            return target;
        }
    }

    public static Map<String, List<Integer>> buildIndex(Document[] docs) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new IndexTask(docs, 0, docs.length));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(\text{TotalWords})$.
- **Span ($T_\infty$):** $\mathcal{O}(\text{UniqueTerms} \cdot \log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{\text{TotalWords}}{P} + \text{UniqueTerms} \log N\right)$.
- **Space Complexity:** $\mathcal{O}(\text{TotalUniquePairs})$.

---

## 17. Parallel N-Queens Solution Count (Đếm cấu hình N-Hậu song song)

**Đề bài chi tiết:**
Cho số nguyên $N$ ($1 \le N \le 18$). Hãy đếm số lượng cách xếp $N$ quân hậu trên bàn cờ $N \times N$ sao cho không có hai quân hậu nào tấn công lẫn nhau bằng thuật toán quay lui (Backtracking) kết hợp Fork-Join.

**Phân tích thuật toán:**
- **Bitmask Backtracking:** Biểu diễn các cột, đường chéo chính, đường chéo phụ bị kiểm soát bằng các biến bit integer (`cols`, `diag1`, `diag2`).
- **Parallel Cutoff Depth:** Khi bàn cờ ở các hàng đầu tiên ($row < \text{CUTOFF\_DEPTH}$), mỗi vị trí đặt hậu hợp lệ sinh ra một `RecursiveTask` riêng biệt. Khi $row \ge \text{CUTOFF\_DEPTH}$, chuyển hoàn toàn sang đệ quy quay lui tuần tự cực nhanh không tạo task heap.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelNQueens {

    private static final int CUTOFF_DEPTH = 3;

    public static class NQueensTask extends RecursiveTask<Long> {
        private final int n;
        private final int row;
        private final int cols;
        private final int diag1;
        private final int diag2;

        public NQueensTask(int n, int row, int cols, int diag1, int diag2) {
            this.n = n;
            this.row = row;
            this.cols = cols;
            this.diag1 = diag1;
            this.diag2 = diag2;
        }

        @Override
        protected Long compute() {
            if (row == n) return 1L;

            if (row >= CUTOFF_DEPTH) {
                return solveSequential(row, cols, diag1, diag2);
            }

            int availablePositions = ((1 << n) - 1) & ~(cols | diag1 | diag2);
            List<NQueensTask> subtasks = new ArrayList<>();

            while (availablePositions != 0) {
                int bit = availablePositions & -availablePositions;
                availablePositions -= bit;

                NQueensTask task = new NQueensTask(
                    n, row + 1,
                    cols | bit,
                    (diag1 | bit) << 1,
                    (diag2 | bit) >> 1
                );
                task.fork();
                subtasks.add(task);
            }

            long totalSolutions = 0;
            for (NQueensTask task : subtasks) {
                totalSolutions += task.join();
            }
            return totalSolutions;
        }

        private long solveSequential(int r, int c, int d1, int d2) {
            if (r == n) return 1L;
            long count = 0;
            int available = ((1 << n) - 1) & ~(c | d1 | d2);
            while (available != 0) {
                int bit = available & -available;
                available -= bit;
                count += solveSequential(r + 1, c | bit, (d1 | bit) << 1, (d2 | bit) >> 1);
            }
            return count;
        }
    }

    public static long totalNQueens(int n) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new NQueensTask(n, 0, 0, 0, 0));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N!)$ (được tỉa nhánh bằng bitmask).
- **Span ($T_\infty$):** $\mathcal{O}(N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N!}{P} + N\right)$.
- **Space Complexity:** $\mathcal{O}(N^2)$ dung lượng heap tasks.

---

## 18. Parallel Flood Fill (Tô màu loang vùng ảnh song song)

**Đề bài chi tiết:**
Cho ma trận ảnh nhị phân $M \times N$. Hãy tìm và gắn nhãn (Labeling) tất cả các thành phần liên thông 4 hướng (Connected Components) bằng giải thuật phân rã lưới 2D song song kết hợp Disjoint Set Union (DSU).

**Phân tích thuật toán:**
- **Phân rã lưới 2D (Block Decomposition):** Chia ma trận thành các khối nhỏ độc lập. Mỗi khối chạy thuật toán Flood Fill / BFS cục bộ để gán nhãn cho các điểm ảnh trong khối.
- **Parallel Stitching (Khâu biên):** Tại các đường biên giáp ranh giữa các khối kề nhau, nếu hai điểm ảnh cùng có màu gốc và nằm cạnh nhau, ta thực hiện phép `union(labelA, labelB)` trên cấu trúc Concurrent Disjoint Set để hợp nhất các nhãn liên vùng.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;
import java.util.concurrent.atomic.AtomicIntegerArray;

public class ParallelFloodFill {

    private static final int BLOCK_SIZE = 128;

    public static class DisjointSet {
        private final AtomicIntegerArray parent;

        public DisjointSet(int size) {
            parent = new AtomicIntegerArray(size);
            for (int i = 0; i < size; i++) parent.set(i, i);
        }

        public int find(int i) {
            while (i != parent.get(i)) {
                int p = parent.get(i);
                int gp = parent.get(p);
                parent.compareAndSet(i, p, gp); // Path halving
                i = parent.get(i);
            }
            return i;
        }

        public void union(int i, int j) {
            int rootI, rootJ;
            do {
                rootI = find(i);
                rootJ = find(j);
                if (rootI == rootJ) return;
            } while (!parent.compareAndSet(Math.max(rootI, rootJ), Math.max(rootI, rootJ), Math.min(rootI, rootJ)));
        }
    }

    public static class BlockLabelTask extends RecursiveAction {
        private final int[][] grid;
        private final int[][] labels;
        private final int r1, r2, c1, c2, width;
        private final DisjointSet dsu;

        public BlockLabelTask(int[][] grid, int[][] labels, int r1, int r2, int c1, int c2, int width, DisjointSet dsu) {
            this.grid = grid; this.labels = labels;
            this.r1 = r1; this.r2 = r2;
            this.c1 = c1; this.c2 = c2;
            this.width = width; this.dsu = dsu;
        }

        @Override
        protected void compute() {
            if (r2 - r1 <= BLOCK_SIZE && c2 - c1 <= BLOCK_SIZE) {
                // Khởi tạo label bằng tọa độ 1D
                for (int r = r1; r < r2; r++) {
                    for (int c = c1; c < c2; c++) {
                        if (grid[r][c] == 1) {
                            int id = r * width + c;
                            labels[r][c] = id;
                            if (r > r1 && grid[r - 1][c] == 1) dsu.union(id, (r - 1) * width + c);
                            if (c > c1 && grid[r][c - 1] == 1) dsu.union(id, r * width + (c - 1));
                        }
                    }
                }
                return;
            }

            int midR = r1 + (r2 - r1) / 2;
            int midC = c1 + (c2 - c1) / 2;

            invokeAll(
                new BlockLabelTask(grid, labels, r1, midR, c1, midC, width, dsu),
                new BlockLabelTask(grid, labels, r1, midR, midC, c2, width, dsu),
                new BlockLabelTask(grid, labels, midR, r2, c1, midC, width, dsu),
                new BlockLabelTask(grid, labels, midR, r2, midC, c2, width, dsu)
            );
        }
    }

    public static int[][] labelConnectedComponents(int[][] grid) {
        int h = grid.length, w = grid[0].length;
        int[][] labels = new int[h][w];
        DisjointSet dsu = new DisjointSet(h * w);

        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new BlockLabelTask(grid, labels, 0, h, 0, w, w, dsu));
            // Hợp nhất đường biên dọc và ngang
            for (int r = 0; r < h; r++) {
                for (int c = 0; c < w; c++) {
                    if (grid[r][c] == 1) {
                        if (r + 1 < h && grid[r + 1][c] == 1) dsu.union(r * w + c, (r + 1) * w + c);
                        if (c + 1 < w && grid[r][c + 1] == 1) dsu.union(r * w + c, r * w + (c + 1));
                    }
                }
            }
        }
        for (int r = 0; r < h; r++) {
            for (int c = 0; c < w; c++) {
                if (grid[r][c] == 1) labels[r][c] = dsu.find(r * w + c);
            }
        }
        return labels;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(H \times W \cdot \alpha(HW))$.
- **Span ($T_\infty$):** $\mathcal{O}(\log(HW))$.
- **Space Complexity:** $\mathcal{O}(H \times W)$.

---

## 19. Parallel QuadTree Construction (Xây dựng cây tứ phân song song)

**Đề bài chi tiết:**
Cho $N$ điểm trong không gian 2 chiều $[x_{\min}, x_{\max}] \times [y_{\min}, y_{\max}]$. Hãy xây dựng cây không gian QuadTree hoàn chỉnh bằng mô hình Fork-Join song song.

**Phân tích thuật toán:**
- **Spatial Partitioning:** Mỗi nút QuadTree chia không gian thành 4 góc phần tư: Tây Bắc (NW), Đông Bắc (NE), Tây Nam (SW), Đông Nam (SE).
- **Parallel Subdivisions:** Tập điểm trong mỗi góc phần tư được lọc song song hoặc tuần tự tùy thuộc kích thước $N$, sau đó 4 nhánh con được xây dựng đồng thời bằng `invokeAll`.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelQuadTree {

    public record Point2D(double x, double y) {}
    public record BoundingBox(double xMin, double yMin, double xMax, double yMax) {
        boolean contains(Point2D p) {
            return p.x() >= xMin && p.x() <= xMax && p.y() >= yMin && p.y() <= yMax;
        }
    }

    public static class QuadNode {
        final BoundingBox bounds;
        final List<Point2D> points;
        QuadNode nw, ne, sw, se;
        boolean isLeaf;

        QuadNode(BoundingBox bounds) {
            this.bounds = bounds;
            this.points = new ArrayList<>();
            this.isLeaf = true;
        }
    }

    private static final int LEAF_CAPACITY = 32;
    private static final int PARALLEL_THRESHOLD = 2_048;

    public static class BuildQuadTreeTask extends RecursiveTask<QuadNode> {
        private final List<Point2D> points;
        private final BoundingBox bounds;

        public BuildQuadTreeTask(List<Point2D> points, BoundingBox bounds) {
            this.points = points;
            this.bounds = bounds;
        }

        @Override
        protected QuadNode compute() {
            QuadNode node = new QuadNode(bounds);

            if (points.size() <= LEAF_CAPACITY) {
                node.points.addAll(points);
                return node;
            }

            node.isLeaf = false;
            double midX = (bounds.xMin() + bounds.xMax()) / 2.0;
            double midY = (bounds.yMin() + bounds.yMax()) / 2.0;

            BoundingBox nwBox = new BoundingBox(bounds.xMin(), midY, midX, bounds.yMax());
            BoundingBox neBox = new BoundingBox(midX, midY, bounds.xMax(), bounds.yMax());
            BoundingBox swBox = new BoundingBox(bounds.xMin(), bounds.yMin(), midX, midY);
            BoundingBox seBox = new BoundingBox(midX, bounds.yMin(), bounds.xMax(), midY);

            List<Point2D> nwPts = new ArrayList<>(), nePts = new ArrayList<>();
            List<Point2D> swPts = new ArrayList<>(), sePts = new ArrayList<>();

            for (Point2D p : points) {
                if (nwBox.contains(p)) nwPts.add(p);
                else if (neBox.contains(p)) nePts.add(p);
                else if (swBox.contains(p)) swPts.add(p);
                else sePts.add(p);
            }

            BuildQuadTreeTask tNW = new BuildQuadTreeTask(nwPts, nwBox);
            BuildQuadTreeTask tNE = new BuildQuadTreeTask(nePts, neBox);
            BuildQuadTreeTask tSW = new BuildQuadTreeTask(swPts, swBox);
            BuildQuadTreeTask tSE = new BuildQuadTreeTask(sePts, seBox);

            if (points.size() > PARALLEL_THRESHOLD) {
                invokeAll(tNW, tNE, tSW, tSE);
                node.nw = tNW.join();
                node.ne = tNE.join();
                node.sw = tSW.join();
                node.se = tSE.join();
            } else {
                node.nw = tNW.compute();
                node.ne = tNE.compute();
                node.sw = tSW.compute();
                node.se = tSE.compute();
            }

            return node;
        }
    }

    public static QuadNode build(List<Point2D> points, BoundingBox bounds) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new BuildQuadTreeTask(points, bounds));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \log N)$ (cây cân bằng).
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log N}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N \log N)$.

---

## 20. Parallel Barnes-Hut N-Body Force (Tính tương tác lực N-Vật thể song song)

**Đề bài chi tiết:**
Trong mô phỏng vật lý thiên văn, cho $N$ thiên thể có khối lượng $m_i$ và vị trí $(x_i, y_i)$. Hãy tính tổng lực hấp dẫn tác dụng lên từng thiên thể bằng giải thuật Barnes-Hut song song với ngưỡng xấp xỉ $\theta = 0.5$.

**Phân tích thuật toán:**
- **Giai đoạn 1 (Xây cây):** Xây dựng QuadTree chứa khối tâm (Center of Mass) của từng nút.
- **Giai đoạn 2 (Tính lực song song):** Với mỗi thiên thể $i$, việc duyệt cây để tính tổng lực là hoàn toàn độc lập với các thiên thể khác. Ta phân rã mảng $N$ thiên thể thành các dải để tính lực song song.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelBarnesHut {

    public static class Body {
        double x, y, vx, vy, fx, fy, mass;
        Body(double x, double y, double mass) {
            this.x = x; this.y = y; this.mass = mass;
        }
    }

    private static final int THRESHOLD = 512;
    private static final double G = 6.67430e-11;
    private static final double THETA = 0.5;

    public static class ForceTask extends RecursiveAction {
        private final Body[] bodies;
        private final int low, high;
        private final ParallelQuadTree.QuadNode root;

        public ForceTask(Body[] bodies, int low, int high, ParallelQuadTree.QuadNode root) {
            this.bodies = bodies;
            this.low = low;
            this.high = high;
            this.root = root;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                for (int i = low; i < high; i++) {
                    computeBodyForce(bodies[i], root);
                }
                return;
            }

            int mid = low + (high - low) / 2;
            invokeAll(
                new ForceTask(bodies, low, mid, root),
                new ForceTask(bodies, mid, high, root)
            );
        }

        private void computeBodyForce(Body b, ParallelQuadTree.QuadNode node) {
            if (node == null) return;
            // Xấp xỉ lực bằng khối tâm nếu node ở xa (s / d < THETA)
            double dx = node.bounds.xMin() - b.x;
            double dy = node.bounds.yMin() - b.y;
            double dist = Math.hypot(dx, dy);
            if (dist < 1e-9) return;

            double s = node.bounds.xMax() - node.bounds.xMin();
            if (node.isLeaf || (s / dist) < THETA) {
                double force = (G * b.mass * 1.0) / (dist * dist + 1e-4);
                b.fx += force * (dx / dist);
                b.fy += force * (dy / dist);
            } else {
                computeBodyForce(b, node.nw);
                computeBodyForce(b, node.ne);
                computeBodyForce(b, node.sw);
                computeBodyForce(b, node.se);
            }
        }
    }

    public static void computeForces(Body[] bodies, ParallelQuadTree.QuadNode root) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new ForceTask(bodies, 0, bodies.length, root));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \log N)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log N}{P} + \log N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$ lưu trữ cây.

---

## 21. Parallel Karatsuba Large Integer Multiplication (Nhân số nguyên lớn Karatsuba song song)

**Đề bài chi tiết:**
Cho hai số nguyên lớn $X$ và $Y$ biểu diễn dưới dạng mảng $N$ chữ số ($N = 2^k$). Hãy nhân hai số $Z = X \times Y$ bằng thuật toán Karatsuba song song với $T_1 = \mathcal{O}(N^{\log_2 3}) \approx \mathcal{O}(N^{1.585})$.

**Phân tích thuật toán:**
- Biểu diễn $X = X_1 \cdot B^m + X_0$ và $Y = Y_1 \cdot B^m + Y_0$.
- Tính 3 tích con: $z_0 = X_0 Y_0$, $z_2 = X_1 Y_1$, và $z_1 = (X_0 + X_1)(Y_0 + Y_1) - z_0 - z_2$.
- Ba phép nhân con này hoàn toàn độc lập và có thể chạy song song trên 3 subtasks.

**Mã nguồn Java:**
```java
import java.math.BigInteger;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelKaratsuba {

    private static final int THRESHOLD = 1_024; // Ngưỡng tính theo số lượng bit

    public static class KaratsubaTask extends RecursiveTask<BigInteger> {
        private final BigInteger x, y;

        public KaratsubaTask(BigInteger x, BigInteger y) {
            this.x = x;
            this.y = y;
        }

        @Override
        protected BigInteger compute() {
            int n = Math.max(x.bitLength(), y.bitLength());
            if (n <= THRESHOLD) {
                return x.multiply(y); // Dùng phép nhân tuần tự tối ưu của JDK
            }

            int half = (n + 1) / 2;
            BigInteger mask = BigInteger.ONE.shiftLeft(half).subtract(BigInteger.ONE);

            BigInteger x0 = x.and(mask);
            BigInteger x1 = x.shiftRight(half);
            BigInteger y0 = y.and(mask);
            BigInteger y1 = y.shiftRight(half);

            KaratsubaTask t0 = new KaratsubaTask(x0, y0);
            KaratsubaTask t2 = new KaratsubaTask(x1, y1);
            KaratsubaTask t1 = new KaratsubaTask(x0.add(x1), y0.add(y1));

            t0.fork();
            t2.fork();

            BigInteger z1 = t1.compute();
            BigInteger z0 = t0.join();
            BigInteger z2 = t2.join();

            BigInteger middle = z1.subtract(z0).subtract(z2);

            return z2.shiftLeft(2 * half).add(middle.shiftLeft(half)).add(z0);
        }
    }

    public static BigInteger multiply(BigInteger x, BigInteger y) {
        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new KaratsubaTask(x, y));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N^{\log_2 3}) \approx \mathcal{O}(N^{1.585})$.
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N^{1.585}}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$ bộ nhớ trung gian.

---

## 22. Parallel Longest Common Subsequence Wavefront (LCS đường chéo sóng song song)

**Đề bài chi tiết:**
Cho hai chuỗi ký tự $S_1$ độ dài $M$ và $S_2$ độ dài $N$. Hãy tính độ dài chuỗi con chung dài nhất (LCS) bằng giải thuật Quy hoạch động Wavefront (Anti-Diagonal Parallelism).

**Phân tích thuật toán:**
- Trong bảng DP kích thước $M \times N$, ô $DP[i][j]$ chỉ phụ thuộc vào $DP[i-1][j]$, $DP[i][j-1]$ và $DP[i-1][j-1]$.
- Do đó, tất cả các ô nằm trên cùng một đường chéo phụ $i + j = k$ đều độc lập và có thể tính song song cùng một lúc.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelLCSWavefront {

    private static final int THRESHOLD = 1_024;

    public static class DiagonalTask extends RecursiveAction {
        private final char[] s1, s2;
        private final int[][] dp;
        private final int diag, low, high;

        public DiagonalTask(char[] s1, char[] s2, int[][] dp, int diag, int low, int high) {
            this.s1 = s1; this.s2 = s2;
            this.dp = dp; this.diag = diag;
            this.low = low; this.high = high;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                for (int idx = low; idx < high; idx++) {
                    int i = idx;
                    int j = diag - i;
                    if (i >= 1 && i <= s1.length && j >= 1 && j <= s2.length) {
                        if (s1[i - 1] == s2[j - 1]) {
                            dp[i][j] = dp[i - 1][j - 1] + 1;
                        } else {
                            dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                        }
                    }
                }
                return;
            }

            int mid = low + (high - low) / 2;
            invokeAll(
                new DiagonalTask(s1, s2, dp, diag, low, mid),
                new DiagonalTask(s1, s2, dp, diag, mid, high)
            );
        }
    }

    public static int computeLCS(String str1, String str2) {
        char[] s1 = str1.toCharArray();
        char[] s2 = str2.toCharArray();
        int m = s1.length, n = s2.length;
        int[][] dp = new int[m + 1][n + 1];

        try (ForkJoinPool pool = new ForkJoinPool()) {
            for (int diag = 2; diag <= m + n; diag++) {
                int minI = Math.max(1, diag - n);
                int maxI = Math.min(m, diag - 1);
                if (minI <= maxI) {
                    pool.invoke(new DiagonalTask(s1, s2, dp, diag, minI, maxI + 1));
                }
            }
        }
        return dp[m][n];
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(M \times N)$.
- **Span ($T_\infty$):** $\mathcal{O}(M + N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{MN}{P} + (M+N)\right)$.
- **Space Complexity:** $\mathcal{O}(M \times N)$.

---

## 23. Parallel QuickHull (Tìm bao lồi tập điểm 2D song song)

**Đề bài chi tiết:**
Cho $N$ điểm trên mặt phẳng 2D. Hãy tìm tập hợp các điểm tạo thành bao lồi (Convex Hull) bằng giải thuật QuickHull song song.

**Phân tích thuật toán:**
- Tìm 2 điểm cực trị có hoành độ nhỏ nhất $P_{\min}$ và lớn nhất $P_{\max}$. Đường thẳng nối 2 điểm chia tập hợp thành 2 nửa: nửa trên và nửa dưới.
- Với mỗi nửa, tìm điểm $P_{\text{far}}$ xa đường thẳng nhất song song. Điểm này chắc chắn thuộc bao lồi và chia nửa mặt phẳng thành 2 tam giác con. Tiếp tục đệ quy song song cho các tập điểm bên ngoài tam giác.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelQuickHull {

    public record Point(long x, long y) {}

    private static final int THRESHOLD = 2_048;

    public static class HullTask extends RecursiveTask<List<Point>> {
        private final Point p1, p2;
        private final List<Point> points;

        public HullTask(Point p1, Point p2, List<Point> points) {
            this.p1 = p1; this.p2 = p2;
            this.points = points;
        }

        @Override
        protected List<Point> compute() {
            if (points.isEmpty()) return new ArrayList<>();

            // Tìm điểm xa nhất
            Point maxPoint = null;
            long maxDist = 0;
            for (Point p : points) {
                long d = distance(p1, p2, p);
                if (d > maxDist) {
                    maxDist = d;
                    maxPoint = p;
                }
            }

            if (maxPoint == null) return new ArrayList<>();

            List<Point> leftSet1 = new ArrayList<>();
            List<Point> leftSet2 = new ArrayList<>();

            for (Point p : points) {
                if (distance(p1, maxPoint, p) > 0) leftSet1.add(p);
                else if (distance(maxPoint, p2, p) > 0) leftSet2.add(p);
            }

            HullTask t1 = new HullTask(p1, maxPoint, leftSet1);
            HullTask t2 = new HullTask(maxPoint, p2, leftSet2);

            t1.fork();
            List<Point> r2 = t2.compute();
            List<Point> r1 = t1.join();

            List<Point> result = new ArrayList<>(r1);
            result.add(maxPoint);
            result.addAll(r2);
            return result;
        }

        private long distance(Point a, Point b, Point c) {
            return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
        }
    }

    public static List<Point> computeHull(List<Point> pts) {
        if (pts.size() <= 2) return new ArrayList<>(pts);
        Point minX = pts.getFirst(), maxX = pts.getFirst();
        for (Point p : pts) {
            if (p.x < minX.x) minX = p;
            if (p.x > maxX.x) maxX = p;
        }

        List<Point> left = new ArrayList<>(), right = new ArrayList<>();
        for (Point p : pts) {
            long cross = (maxX.x - minX.x) * (p.y - minX.y) - (maxX.y - minX.y) * (p.x - minX.x);
            if (cross > 0) left.add(p);
            else if (cross < 0) right.add(p);
        }

        try (ForkJoinPool pool = new ForkJoinPool()) {
            HullTask tLeft = new HullTask(minX, maxX, left);
            HullTask tRight = new HullTask(maxX, minX, right);
            invokeAll(tLeft, tRight);

            List<Point> hull = new ArrayList<>();
            hull.add(minX);
            hull.addAll(tLeft.join());
            hull.add(maxX);
            hull.addAll(tRight.join());
            return hull;
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** Trung bình $\mathcal{O}(N \log N)$, Tệ nhất $\mathcal{O}(N^2)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log N}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$.

---

## 24. Parallel Multi-Chunk Rabin-Karp String Search (Tìm chuỗi phân đoạn song song)

**Đề bài chi tiết:**
Cho văn bản $T$ độ dài $N$ và chuỗi mẫu $P$ độ dài $M$ ($M \ll N$). Hãy tìm tất cả các vị trí xuất hiện của $P$ trong $T$ bằng thuật toán Rabin-Karp phân đoạn song song với xử lý vùng biên (Overlap Boundary).

**Phân tích thuật toán:**
- **Chunk Partitioning:** Chia văn bản $T$ thành các khối kích thước $C$.
- **Xử lý Biên (Overlap Area):** Mỗi khối phụ trách tìm kiếm trong đoạn $[low, high + M - 1]$ để không bỏ sót các kết quả nằm đè lên ranh giới giữa 2 khối liên tiếp.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelRabinKarp {

    private static final int THRESHOLD = 65_536;
    private static final long PRIME = 1_000_000_007L;
    private static final long BASE = 256L;

    public static class SearchTask extends RecursiveTask<List<Integer>> {
        private final String text, pattern;
        private final int low, high;
        private final long patternHash, highestPower;

        public SearchTask(String text, String pattern, int low, int high, long patternHash, long highestPower) {
            this.text = text; this.pattern = pattern;
            this.low = low; this.high = high;
            this.patternHash = patternHash;
            this.highestPower = highestPower;
        }

        @Override
        protected List<Integer> compute() {
            if (high - low <= THRESHOLD) {
                List<Integer> matches = new ArrayList<>();
                int m = pattern.length();
                int searchEnd = Math.min(high + m - 1, text.length());
                if (searchEnd - low < m) return matches;

                long currentHash = 0;
                for (int i = low; i < low + m; i++) {
                    currentHash = (currentHash * BASE + text.charAt(i)) % PRIME;
                }

                for (int i = low; i <= searchEnd - m; i++) {
                    if (currentHash == patternHash && text.startsWith(pattern, i)) {
                        matches.add(i);
                    }
                    if (i < searchEnd - m) {
                        currentHash = (BASE * (currentHash - text.charAt(i) * highestPower % PRIME + PRIME) + text.charAt(i + m)) % PRIME;
                    }
                }
                return matches;
            }

            int mid = low + (high - low) / 2;
            SearchTask leftTask = new SearchTask(text, pattern, low, mid, patternHash, highestPower);
            SearchTask rightTask = new SearchTask(text, pattern, mid, high, patternHash, highestPower);

            leftTask.fork();
            List<Integer> r = rightTask.compute();
            List<Integer> l = leftTask.join();

            l.addAll(r);
            return l;
        }
    }

    public static List<Integer> search(String text, String pattern) {
        int m = pattern.length();
        if (m == 0 || text.length() < m) return List.of();

        long patternHash = 0, highestPower = 1;
        for (int i = 0; i < m; i++) {
            patternHash = (patternHash * BASE + pattern.charAt(i)) % PRIME;
            if (i > 0) highestPower = (highestPower * BASE) % PRIME;
        }

        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new SearchTask(text, pattern, 0, text.length(), patternHash, highestPower));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N + M)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log N + M)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N}{P} + \log N\right)$.
- **Space Complexity:** $\mathcal{O}(\text{Matches} + \log N)$.

---

## 25. Parallel Radix-2 Cooley-Tukey FFT (Biến đổi Fourier nhanh song song)

**Đề bài chi tiết:**
Cho mảng số phức $X$ có độ dài $N = 2^k$. Hãy tính biến đổi rời rạc Fourier $Y = \text{FFT}(X)$ bằng giải thuật Cooley-Tukey song song.

**Phân tích thuật toán:**
- Phân rã mảng đầu vào thành 2 mảng con: phần tử ở vị trí chẵn $X_{\text{even}}$ và phần tử ở vị trí lẻ $X_{\text{odd}}$.
- Đệ quy song song tính FFT cho 2 mảng con bằng `invokeAll(leftTask, rightTask)`.
- Giai đoạn bướm (Butterfly Operation): Kết hợp kết quả $Y[k] = Y_{\text{even}}[k] + \omega^k Y_{\text{odd}}[k]$.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelFFT {

    public record Complex(double re, double im) {
        Complex add(Complex b) { return new Complex(re + b.re, im + b.im); }
        Complex sub(Complex b) { return new Complex(re - b.re, im - b.im); }
        Complex mul(Complex b) {
            return new Complex(re * b.re - im * b.im, re * b.im + im * b.re);
        }
    }

    private static final int THRESHOLD = 1_024;

    public static class FFTTask extends RecursiveAction {
        private final Complex[] data;
        private final int low, length, step;

        public FFTTask(Complex[] data, int low, int length, int step) {
            this.data = data;
            this.low = low;
            this.length = length;
            this.step = step;
        }

        @Override
        protected void compute() {
            if (length == 1) return;

            if (length <= THRESHOLD) {
                sequentialFFT(data, low, length, step);
                return;
            }

            int half = length / 2;
            invokeAll(
                new FFTTask(data, low, half, step * 2),
                new FFTTask(data, low + step, half, step * 2)
            );

            // Giai đoạn kết hợp Butterfly
            Complex[] temp = new Complex[length];
            for (int k = 0; k < half; k++) {
                double angle = -2 * Math.PI * k / length;
                Complex wk = new Complex(Math.cos(angle), Math.sin(angle));
                Complex even = data[low + 2 * k * step];
                Complex odd = data[low + (2 * k + 1) * step];
                Complex oddWk = odd.mul(wk);

                temp[k] = even.add(oddWk);
                temp[k + half] = even.sub(oddWk);
            }

            for (int k = 0; k < length; k++) {
                data[low + k * step] = temp[k];
            }
        }

        private void sequentialFFT(Complex[] arr, int off, int n, int st) {
            if (n == 1) return;
            int h = n / 2;
            sequentialFFT(arr, off, h, st * 2);
            sequentialFFT(arr, off + st, h, st * 2);
            Complex[] t = new Complex[n];
            for (int k = 0; k < h; k++) {
                double angle = -2 * Math.PI * k / n;
                Complex wk = new Complex(Math.cos(angle), Math.sin(angle));
                Complex e = arr[off + 2 * k * st];
                Complex o = arr[off + (2 * k + 1) * st].mul(wk);
                t[k] = e.add(o);
                t[k + h] = e.sub(o);
            }
            for (int k = 0; k < n; k++) arr[off + k * st] = t[k];
        }
    }

    public static Complex[] computeFFT(Complex[] input) {
        Complex[] result = input.clone();
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new FFTTask(result, 0, result.length, 1));
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \log N)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log^2 N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N \log N}{P} + \log^2 N\right)$.
- **Space Complexity:** $\mathcal{O}(N)$.

---

## 26. Parallel Graph Connected Components (Thành phần liên thông song song)

**Đề bài chi tiết:**
Cho đồ thị vô hướng $G = (V, E)$ với $|E|$ rất lớn. Hãy xác định thành phần liên thông của mọi đỉnh bằng phương pháp gộp cạnh song song kết hợp Concurrent Disjoint Set Union.

**Phân tích thuật toán:**
- Mảng cạnh $E$ được chia thành các đoạn con độc lập. Mỗi luồng duyệt một đoạn cạnh và thực hiện `union(u, v)` vào một bảng cha chia sẻ chung không khóa (Lock-free DSU).
- Áp dụng kỹ thuật Path-Halving với Atomic Compare-And-Set để đảm bảo tính an toàn đa luồng.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;
import java.util.concurrent.atomic.AtomicIntegerArray;

public class ParallelConnectedComponents {

    public record Edge(int u, int v) {}

    private static final int THRESHOLD = 8_192;

    public static class EdgeProcessTask extends RecursiveAction {
        private final Edge[] edges;
        private final int low, high;
        private final AtomicIntegerArray parent;

        public EdgeProcessTask(Edge[] edges, int low, int high, AtomicIntegerArray parent) {
            this.edges = edges;
            this.low = low;
            this.high = high;
            this.parent = parent;
        }

        @Override
        protected void compute() {
            if (high - low <= THRESHOLD) {
                for (int i = low; i < high; i++) {
                    union(edges[i].u(), edges[i].v());
                }
                return;
            }

            int mid = low + (high - low) / 2;
            invokeAll(
                new EdgeProcessTask(edges, low, mid, parent),
                new EdgeProcessTask(edges, mid, high, parent)
            );
        }

        private int find(int i) {
            while (i != parent.get(i)) {
                int p = parent.get(i);
                int gp = parent.get(p);
                parent.compareAndSet(i, p, gp);
                i = parent.get(i);
            }
            return i;
        }

        private void union(int u, int v) {
            while (true) {
                int rootU = find(u);
                int rootV = find(v);
                if (rootU == rootV) break;
                int minR = Math.min(rootU, rootV);
                int maxR = Math.max(rootU, rootV);
                if (parent.compareAndSet(maxR, maxR, minR)) break;
            }
        }
    }

    public static int[] findComponents(int numVertices, Edge[] edges) {
        AtomicIntegerArray parent = new AtomicIntegerArray(numVertices);
        for (int i = 0; i < numVertices; i++) parent.set(i, i);

        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new EdgeProcessTask(edges, 0, edges.length, parent));
        }

        int[] result = new int[numVertices];
        for (int i = 0; i < numVertices; i++) {
            int root = i;
            while (root != parent.get(root)) root = parent.get(root);
            result[i] = root;
        }
        return result;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(|E| \cdot \alpha(|V|))$.
- **Span ($T_\infty$):** $\mathcal{O}\left(\frac{|E|}{\text{THRESHOLD}} + \log |V|\right)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{|E| \alpha(|V|)}{P} + \log |V|\right)$.
- **Space Complexity:** $\mathcal{O}(|V|)$.

---

## 27. Parallel K-Means Clustering Step (Phân cụm K-Means song song)

**Đề bài chi tiết:**
Cho $N$ điểm dữ liệu $D$ chiều và $K$ tâm cụm (Centroids). Hãy thực hiện một bước lặp (Single Iteration) của thuật toán K-Means: gán mỗi điểm vào tâm cụm gần nhất và tính lại tọa độ các tâm cụm mới bằng Fork-Join Framework.

**Phân tích thuật toán:**
- **Map Phase:** Chia $N$ điểm cho các tác vụ con. Mỗi tác vụ tính tổng tọa độ và số lượng điểm thuộc về từng cụm trong phạm vi cục bộ của nó.
- **Reduce Phase:** Hợp nhất tổng tọa độ và số lượng điểm của $K$ cụm từ các tác vụ con, sau đó chia trung bình để ra tâm cụm mới.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ParallelKMeans {

    private static final int THRESHOLD = 10_000;

    public record ClusterSummary(double[][] sumCoords, int[] counts) {}

    public static class AssignTask extends RecursiveTask<ClusterSummary> {
        private final double[][] points;
        private final double[][] centroids;
        private final int low, high, k, dims;

        public AssignTask(double[][] points, double[][] centroids, int low, int high, int k, int dims) {
            this.points = points; this.centroids = centroids;
            this.low = low; this.high = high;
            this.k = k; this.dims = dims;
        }

        @Override
        protected ClusterSummary compute() {
            if (high - low <= THRESHOLD) {
                double[][] sums = new double[k][dims];
                int[] counts = new int[k];

                for (int i = low; i < high; i++) {
                    double[] p = points[i];
                    int bestC = 0;
                    double minDist = Double.MAX_VALUE;

                    for (int c = 0; c < k; c++) {
                        double d = distSq(p, centroids[c]);
                        if (d < minDist) {
                            minDist = d;
                            bestC = c;
                        }
                    }

                    counts[bestC]++;
                    for (int d = 0; d < dims; d++) sums[bestC][d] += p[d];
                }
                return new ClusterSummary(sums, counts);
            }

            int mid = low + (high - low) / 2;
            AssignTask t1 = new AssignTask(points, centroids, low, mid, k, dims);
            AssignTask t2 = new AssignTask(points, centroids, mid, high, k, dims);

            t1.fork();
            ClusterSummary r2 = t2.compute();
            ClusterSummary r1 = t1.join();

            // Merge 2 summaries
            double[][] totalSums = new double[k][dims];
            int[] totalCounts = new int[k];
            for (int c = 0; c < k; c++) {
                totalCounts[c] = r1.counts()[c] + r2.counts()[c];
                for (int d = 0; d < dims; d++) {
                    totalSums[c][d] = r1.sumCoords()[c][d] + r2.sumCoords()[c][d];
                }
            }
            return new ClusterSummary(totalSums, totalCounts);
        }

        private double distSq(double[] a, double[] b) {
            double s = 0;
            for (int i = 0; i < a.length; i++) {
                double diff = a[i] - b[i];
                s += diff * diff;
            }
            return s;
        }
    }

    public static double[][] runSingleIteration(double[][] points, double[][] centroids) {
        int k = centroids.length;
        int dims = points[0].length;

        try (ForkJoinPool pool = new ForkJoinPool()) {
            ClusterSummary summary = pool.invoke(new AssignTask(points, centroids, 0, points.length, k, dims));
            double[][] newCentroids = new double[k][dims];
            for (int c = 0; c < k; c++) {
                int cnt = summary.counts()[c];
                if (cnt > 0) {
                    for (int d = 0; d < dims; d++) newCentroids[c][d] = summary.sumCoords()[c][d] / cnt;
                } else {
                    newCentroids[c] = centroids[c].clone();
                }
            }
            return newCentroids;
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N \cdot K \cdot D)$.
- **Span ($T_\infty$):** $\mathcal{O}(K \cdot D \log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{NKD}{P} + KD \log N\right)$.
- **Space Complexity:** $\mathcal{O}(K \cdot D \log N)$.

---

## 28. Parallel Ray Tracing Tile Renderer (Render ảnh Ray Tracing theo khối)

**Đề bài chi tiết:**
Cho một khung cảnh 3D gồm các quả cầu (Spheres) và nguồn sáng. Hãy kết xuất hình ảnh $W \times H$ pixel bằng phương pháp Ray Tracing phân chia không gian màn hình thành các ô gạch 2D (Tiles) song song.

**Phân tích thuật toán:**
- Mỗi tia sáng (Ray) được phóng độc lập qua từng pixel trên màn hình để tìm giao điểm và tính màu.
- Chia ảnh thành các ô gạch kích thước $16 \times 16$ pixel. Mỗi ô được thực thi bởi một `RecursiveAction` trên pool luồng Fork-Join.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelRayTracer {

    private static final int TILE_SIZE = 16;

    public record Sphere(double x, double y, double z, double r, int color) {}

    public static class RenderTileTask extends RecursiveAction {
        private final int[] buffer;
        private final int width, height;
        private final int x0, y0, x1, y1;
        private final Sphere[] scene;

        public RenderTileTask(int[] buffer, int width, int height,
                              int x0, int y0, int x1, int y1, Sphere[] scene) {
            this.buffer = buffer; this.width = width; this.height = height;
            this.x0 = x0; this.y0 = y0; this.x1 = x1; this.y1 = y1;
            this.scene = scene;
        }

        @Override
        protected void compute() {
            if ((x1 - x0) <= TILE_SIZE && (y1 - y0) <= TILE_SIZE) {
                for (int y = y0; y < y1; y++) {
                    for (int x = x0; x < x1; x++) {
                        buffer[y * width + x] = traceRay(x, y);
                    }
                }
                return;
            }

            int midX = x0 + (x1 - x0) / 2;
            int midY = y0 + (y1 - y0) / 2;

            invokeAll(
                new RenderTileTask(buffer, width, height, x0, y0, midX, midY, scene),
                new RenderTileTask(buffer, width, height, midX, y0, x1, midY, scene),
                new RenderTileTask(buffer, width, height, x0, midY, midX, y1, scene),
                new RenderTileTask(buffer, width, height, midX, midY, x1, y1, scene)
            );
        }

        private int traceRay(int px, int py) {
            double dirX = (px - width / 2.0) / width;
            double dirY = (py - height / 2.0) / height;
            double dirZ = 1.0;

            int hitColor = 0xFF000000; // Background đen
            double closestDist = Double.MAX_VALUE;

            for (Sphere s : scene) {
                // Kiểm tra giao điểm tia gốc (0,0,0) hướng (dirX, dirY, dirZ) với hình cầu
                double b = -2 * (s.x * dirX + s.y * dirY + s.z * dirZ);
                double c = s.x * s.x + s.y * s.y + s.z * s.z - s.r * s.r;
                double delta = b * b - 4 * (dirX * dirX + dirY * dirY + dirZ * dirZ) * c;
                if (delta >= 0) {
                    double dist = (-b - Math.sqrt(delta)) / 2.0;
                    if (dist > 0 && dist < closestDist) {
                        closestDist = dist;
                        hitColor = s.color();
                    }
                }
            }
            return hitColor;
        }
    }

    public static int[] render(int width, int height, Sphere[] scene) {
        int[] buffer = new int[width * height];
        try (ForkJoinPool pool = new ForkJoinPool()) {
            pool.invoke(new RenderTileTask(buffer, width, height, 0, 0, width, height, scene));
        }
        return buffer;
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(W \times H \times |\text{Scene}|)$.
- **Span ($T_\infty$):** $\mathcal{O}(\log(W \times H))$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{WH|\text{Scene}|}{P} + \log(WH)\right)$.
- **Space Complexity:** $\mathcal{O}(W \times H)$ cho pixel frame buffer.

---

## 29. Parallel Blocked Floyd-Warshall (Đường đi ngắn nhất mọi cặp đỉnh song song)

**Đề bài chi tiết:**
Cho ma trận trọng số $N \times N$ của đồ thị. Hãy tìm ma trận khoảng cách ngắn nhất giữa mọi cặp đỉnh bằng giải thuật Blocked Floyd-Warshall song song để tăng tính địa phương của bộ nhớ đệm (Cache Locality).

**Phân tích thuật toán:**
Tại mỗi bước lặp $k$ từ $0$ đến $B-1$ khối:
- **Phase 1:** Cập nhật khối chéo chính $(k, k)$ tuần tự.
- **Phase 2:** Cập nhật song song các khối nằm trên cùng hàng $k$ và cùng cột $k$.
- **Phase 3:** Cập nhật song song toàn bộ các khối còn lại độc lập nhau.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

public class ParallelBlockedFloydWarshall {

    private static final int BLOCK_SIZE = 64;

    public static class UpdateBlocksTask extends RecursiveAction {
        private final int[][] dist;
        private final int kBlock, lowI, highI, numBlocks;

        public UpdateBlocksTask(int[][] dist, int kBlock, int lowI, int highI, int numBlocks) {
            this.dist = dist; this.kBlock = kBlock;
            this.lowI = lowI; this.highI = highI;
            this.numBlocks = numBlocks;
        }

        @Override
        protected void compute() {
            if (highI - lowI <= 1) {
                int bi = lowI;
                if (bi != kBlock) {
                    for (int bj = 0; bj < numBlocks; bj++) {
                        if (bj != kBlock) {
                            updateBlock(dist, bi, bj, kBlock);
                        }
                    }
                }
                return;
            }

            int mid = lowI + (highI - lowI) / 2;
            invokeAll(
                new UpdateBlocksTask(dist, kBlock, lowI, mid, numBlocks),
                new UpdateBlocksTask(dist, kBlock, mid, highI, numBlocks)
            );
        }

        private void updateBlock(int[][] d, int bi, int bj, int bk) {
            int i0 = bi * BLOCK_SIZE, i1 = Math.min(i0 + BLOCK_SIZE, d.length);
            int j0 = bj * BLOCK_SIZE, j1 = Math.min(j0 + BLOCK_SIZE, d.length);
            int k0 = bk * BLOCK_SIZE, k1 = Math.min(k0 + BLOCK_SIZE, d.length);

            for (int k = k0; k < k1; k++) {
                for (int i = i0; i < i1; i++) {
                    for (int j = j0; j < j1; j++) {
                        if (d[i][k] != Integer.MAX_VALUE && d[k][j] != Integer.MAX_VALUE) {
                            d[i][j] = Math.min(d[i][j], d[i][k] + d[k][j]);
                        }
                    }
                }
            }
        }
    }

    public static void computeAPSP(int[][] dist) {
        int n = dist.length;
        int numBlocks = (n + BLOCK_SIZE - 1) / BLOCK_SIZE;

        try (ForkJoinPool pool = new ForkJoinPool()) {
            for (int k = 0; k < numBlocks; k++) {
                // Phase 3: Cập nhật song song toàn bộ các block độc lập
                pool.invoke(new UpdateBlocksTask(dist, k, 0, numBlocks, numBlocks));
            }
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(N^3)$.
- **Span ($T_\infty$):** $\mathcal{O}(N \log N)$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{N^3}{P} + N \log N\right)$.
- **Space Complexity:** $\mathcal{O}(1)$ ngoài ma trận đầu vào.

---

## 30. Parallel Adaptive Simpson's Numerical Integration (Tính tích phân số thích nghi song song)

**Đề bài chi tiết:**
Cho một hàm số liên tục $f(x)$ và khoảng $[a, b]$. Hãy tính xấp xỉ tích phân xác định $I = \int_{a}^{b} f(x) \, dx$ với sai số tuyệt đối $\le \epsilon$ bằng phương pháp Quy tắc Simpson Thích nghi song song (Adaptive Simpson's Quadrature).

**Phân tích thuật toán:**
- **Quy tắc Simpson cơ sở:** $S(a, b) = \frac{b - a}{6} \left(f(a) + 4 f\left(\frac{a+b}{2}\right) + f(b)\right)$.
- **Tiêu chuẩn hội tụ thích nghi:** Nếu sai phân $|S(a, m) + S(m, b) - S(a, b)| < 15\epsilon$, ta chấp nhận giá trị $S(a, m) + S(m, b) + \frac{S(a, m) + S(m, b) - S(a, b)}{15}$.
- Ngược lại, chia đôi khoảng thành $[a, m]$ và $[m, b]$, đệ quy song song với sai số $\frac{\epsilon}{2}$.

**Mã nguồn Java:**
```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;
import java.util.function.DoubleUnaryOperator;

public class ParallelAdaptiveIntegration {

    private static final int MAX_PARALLEL_DEPTH = 8;

    public static class SimpsonTask extends RecursiveTask<Double> {
        private final DoubleUnaryOperator f;
        private final double a, b, eps, fa, fb, fc;
        private final double wholeArea;
        private final int depth;

        public SimpsonTask(DoubleUnaryOperator f, double a, double b, double eps,
                           double fa, double fb, double fc, double wholeArea, int depth) {
            this.f = f; this.a = a; this.b = b; this.eps = eps;
            this.fa = fa; this.fb = fb; this.fc = fc;
            this.wholeArea = wholeArea; this.depth = depth;
        }

        @Override
        protected Double compute() {
            double c = (a + b) / 2.0;
            double d = (a + c) / 2.0;
            double e = (c + b) / 2.0;

            double fd = f.applyAsDouble(d);
            double fe = f.applyAsDouble(e);

            double leftArea = (c - a) / 6.0 * (fa + 4 * fd + fc);
            double rightArea = (b - c) / 6.0 * (fc + 4 * fe + fb);
            double delta = leftArea + rightArea - wholeArea;

            if (Math.abs(delta) <= 15 * eps || depth >= 50) {
                return leftArea + rightArea + delta / 15.0;
            }

            SimpsonTask leftTask = new SimpsonTask(f, a, c, eps / 2.0, fa, fc, fd, leftArea, depth + 1);
            SimpsonTask rightTask = new SimpsonTask(f, c, b, eps / 2.0, fc, fb, fe, rightArea, depth + 1);

            if (depth < MAX_PARALLEL_DEPTH) {
                leftTask.fork();
                double r = rightTask.compute();
                double l = leftTask.join();
                return l + r;
            } else {
                return leftTask.compute() + rightTask.compute();
            }
        }
    }

    public static double integrate(DoubleUnaryOperator f, double a, double b, double eps) {
        double c = (a + b) / 2.0;
        double fa = f.applyAsDouble(a);
        double fb = f.applyAsDouble(b);
        double fc = f.applyAsDouble(c);
        double wholeArea = (b - a) / 6.0 * (fa + 4 * fc + fb);

        try (ForkJoinPool pool = new ForkJoinPool()) {
            return pool.invoke(new SimpsonTask(f, a, b, eps, fa, fb, fc, wholeArea, 0));
        }
    }
}
```

**Độ phức tạp:**
- **Work ($T_1$):** $\mathcal{O}(K)$ phụ thuộc độ cong và độ nhẵn của hàm $f(x)$.
- **Span ($T_\infty$):** $\mathcal{O}(\text{MaxDepth})$.
- **Parallel Time ($T_P$):** $\mathcal{O}\left(\frac{K}{P} + \text{MaxDepth}\right)$.
- **Space Complexity:** $\mathcal{O}(\text{MaxDepth})$.
