# Metadata
- **Document ID**: DSA-04-03
- **Version**: 1.0
- **Prerequisites**: DSA-04-01 (Array Basics)
- **Learning Objectives**: Hiểu cấu trúc thực sự của Mảng 2 Chiều (2D Array) trong Java. Nhận thức được Mảng 2D trong Java KHÔNG phải là một khối nhớ liên tục (như trong C/C++) mà là "Mảng của các Mảng". Nắm vững các kỹ thuật duyệt mảng tối ưu Cache Locality và chuyển đổi giữa 2D và 1D.
- **Estimated Reading Time**: 50 phút
- **Difficulty**: Intermediate
- **Keywords**: 2D Array, Matrix, Row-Major, Column-Major, Array of Arrays, Jagged Array, Cache Locality

---

# 1 Purpose
Mảng 2 chiều (Ma trận - Matrix) là công cụ thiết yếu để biểu diễn dữ liệu dạng Bảng (Grid), Bản đồ (Map), Bảng Quy hoạch Động (DP Table) hay Ma trận Kề (Adjacency Matrix) trong Đồ thị. Tuy nhiên, cách Java thiết kế Mảng 2 chiều có một số đặc thù về bộ nhớ (Memory overhead) và tốc độ truy xuất (Cache misses) mà một Kỹ sư phần mềm xuất sắc bắt buộc phải hiểu rõ để tối ưu hóa thuật toán.

---

# 2 Motivation
Giả sử bạn phải xử lý một tấm ảnh phân giải $4000 \times 3000$ pixel (12 triệu pixel). Mỗi pixel được biểu diễn bởi một số nguyên (RGB).
- Cấu trúc tự nhiên nhất: `int[][] image = new int[4000][3000]`.
- Vấn đề: Nếu duyệt từng cột (xử lý pixel theo chiều dọc), hệ thống của bạn có thể chậm hơn **10 LẦN** so với duyệt từng hàng (theo chiều ngang).
- Hơn thế nữa, mảng 2 chiều này trong Java tốn thêm **hàng chục Kilobyte bộ nhớ dư thừa** (Overhead) so với ngôn ngữ C++ chỉ để duy trì cấu trúc "Mảng của các Mảng". Tại sao lại như vậy? Chúng ta sẽ giải phẫu nó ngay sau đây.

---

# 3 Mathematical Foundation
Trong Toán học, Ma trận $M \times N$ là một cấu trúc chữ nhật gồm $M$ hàng và $N$ cột.
- Ký hiệu: $A_{i,j}$ là phần tử ở hàng $i$ và cột $j$.
- Trong bộ nhớ máy tính (chỉ là 1 chiều liên tục), ta có thể ánh xạ (Flatten) ma trận 2D thành mảng 1D độ dài $M \times N$.
- **Ánh xạ Row-Major (Duyệt theo Hàng):**
  $$Index = i \times N + j$$
  (Trong đó $N$ là số lượng cột, $0 \le i < M$, $0 \le j < N$).
- **Ánh xạ Column-Major (Duyệt theo Cột):**
  $$Index = j \times M + i$$

Java, C, C++ ưu tiên **Row-Major** (các phần tử cùng một hàng nằm sát nhau). Fortran, MATLAB, R ưu tiên **Column-Major**.

---

# 4 Core Theory
## 4.1 "Array of Arrays" (Mảng của các Mảng)
Khác với C/C++ nơi mảng 2 chiều kích thước $M \times N$ là một khối nhớ liên tiếp kích thước $(M \times N \times ElementSize)$, **Java KHÔNG có Mảng 2 Chiều thực sự**.
Trong Java, `int[][] matrix = new int[3][4]` thực chất là:
1. Tạo 1 mảng 1 chiều kích thước 3 (Mảng cha), chứa các **Con trỏ (References)**.
2. Tạo 3 mảng 1 chiều kích thước 4 (Mảng con), chứa các giá trị `int`.
3. Trỏ 3 phần tử của Mảng cha vào 3 Mảng con.

Hệ quả: Các Mảng con (các hàng) nằm ở những vị trí ngẫu nhiên trên Heap, KHÔNG liên tiếp nhau!

## 4.2 Jagged Arrays (Mảng răng cưa)
Bởi vì mỗi hàng là một mảng độc lập, chúng **không bắt buộc phải có độ dài bằng nhau**.
```java
int[][] jagged = new int[3][];
jagged[0] = new int[5]; // Hàng 0 có 5 phần tử
jagged[1] = new int[2]; // Hàng 1 có 2 phần tử
jagged[2] = new int[8]; // Hàng 2 có 8 phần tử
```
Đặc tính này rất hữu ích để tiết kiệm bộ nhớ khi biểu diễn Đồ thị (Adjacency List) hoặc các Tam giác Pascal.

## 4.3 Cách duyệt mảng (Traversal)
Cách chuẩn xác để lấy số hàng và cột:
- Số hàng: `matrix.length`
- Số cột của hàng i: `matrix[i].length`

**Duyệt theo Row-Major (TỐT):**
```java
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[i].length; j++) {
        // Truy xuất matrix[i][j]
    }
}
```

---

# 5 Visual Explanation

```mermaid
graph LR
    subgraph Parent Array (References)
        P0["matrix[0]"]
        P1["matrix[1]"]
        P2["matrix[2]"]
    end
    
    subgraph Child Array 0 (Row 0)
        C0["[1, 2, 3, 4]"]
    end
    
    subgraph Child Array 1 (Row 1)
        C1["[5, 6, 7, 8]"]
    end
    
    subgraph Child Array 2 (Row 2)
        C2["[9, 10, 11, 12]"]
    end

    P0 --> C0
    P1 --> C1
    P2 --> C2
```

*(Hình trên minh họa: `matrix[0]` và `matrix[1]` có thể ở rất xa nhau trên bộ nhớ Heap).*

---

# 6 Java Implementation
Các thao tác căn bản với Ma trận vuông $N \times N$:

```java
public class MatrixOperations {

    // ===== Tính Tổng Đường chéo chính và phụ =====
    // Time: O(N), Không cần 2 vòng lặp lồng nhau
    public static int sumDiagonals(int[][] matrix) {
        int sum = 0;
        int n = matrix.length;
        for (int i = 0; i < n; i++) {
            sum += matrix[i][i]; // Đường chéo chính
            sum += matrix[i][n - 1 - i]; // Đường chéo phụ
        }
        if (n % 2 != 0) {
            sum -= matrix[n/2][n/2]; // Trừ phần tử ở giữa bị cộng 2 lần (nếu N lẻ)
        }
        return sum;
    }

    // ===== Transpose Matrix (Chuyển vị Ma trận VUÔNG In-place) =====
    // Time: O(N^2), Space: O(1)
    // Đổi chỗ A[i][j] và A[j][i]
    public static void transpose(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) { // Chú ý: j bắt đầu từ i+1
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }

    // ===== Rotate Matrix 90 Độ theo chiều kim đồng hồ (In-place) =====
    // Bước 1: Transpose. Bước 2: Reverse từng hàng.
    // Time: O(N^2), Space: O(1)
    public static void rotate90(int[][] matrix) {
        int n = matrix.length;
        transpose(matrix); // Gọi hàm bên trên
        for (int i = 0; i < n; i++) {
            // Reverse hàng i
            int left = 0, right = n - 1;
            while (left < right) {
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++; right--;
            }
        }
    }
}
```

---

# 8 Complexity Analysis
Cho ma trận kích thước $M \times N$:
| Thao tác | Time | Space | Ghi chú |
|---|---|---|---|
| Đọc `matrix[i][j]` | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ | 2 lần dereference pointer |
| Duyệt toàn bộ | $\mathcal{O}(MN)$ | $\mathcal{O}(1)$ | Row-Major nhanh hơn Column-Major |
| Tìm kiếm tuyến tính | $\mathcal{O}(MN)$ | $\mathcal{O}(1)$ | Tìm phần tử X |
| Cấp phát bộ nhớ | $\mathcal{O}(M)$ | $\mathcal{O}(MN)$ | Khởi tạo $M$ mảng con |
| Transpose (Tạo mới) | $\mathcal{O}(MN)$ | $\mathcal{O}(MN)$ | Đảo hàng thành cột |
| Kéo giãn về 1D | $\mathcal{O}(MN)$ | $\mathcal{O}(MN)$ | Chuyển thành 1D array |

---

# 9 JVM Analysis
## Bộ nhớ Overhead
Định nghĩa `int[][] matrix = new int[M][N]`. Nó tiêu thụ bao nhiêu bộ nhớ thực sự trên Heap?
- **1 Mảng cha**: Chứa $M$ References (4 bytes mỗi Ref nhờ Compressed Oops) + 16 bytes Header $\rightarrow 16 + 4M$ bytes.
- **$M$ Mảng con**: Mỗi mảng chứa $N$ số int (4 bytes mỗi số) + 16 bytes Header $\rightarrow M \times (16 + 4N)$ bytes.
- Tổng cộng: $\approx 4MN + 20M + 16$ bytes.
So sánh với C/C++ (Chỉ tốn đúng $4MN$ bytes). Java tiêu tốn thêm **$20M$ bytes Overhead**.
Nếu $M$ rất lớn (1 triệu hàng, mỗi hàng 2 cột), số byte lãng phí do Header cực kỳ khủng khiếp $\rightarrow$ Giải pháp: Flatten nó thành mảng 1D `int[] flattened = new int[M * N]`.

---

# 10 OpenJDK Analysis
## Bounds Checking cho mảng 2D
Khi truy cập `matrix[i][j]`, JVM (C2 Compiler) phải thực hiện **2 lần kiểm tra giới hạn (Bounds Checking)**:
1. Kiểm tra `0 <= i < matrix.length`. Trả về Reference của mảng con. Nếu mảng con là `null`, ném `NullPointerException`.
2. Lấy được mảng con, kiểm tra `0 <= j < matrix[i].length`. Ném `ArrayIndexOutOfBoundsException` nếu sai. Trả về giá trị.
Vì `matrix[i]` có thể bị gán bằng `null` hoặc gán sang một mảng kích thước khác bất cứ lúc nào, JIT Compiler rất khó để tối ưu hóa hoàn toàn (Eliminate bounds checks) như mảng 1 chiều.

---

# 11 Production Usage
**Kỹ thuật 1D Flattening**
Trong High-Performance Computing (như xử lý Đồ họa, AI, Matrix Math Library), không ai dùng `double[][]` trong Java. Họ dùng `double[]` và tự tính chỉ số:
```java
// TỐI ƯU
class FastMatrix {
    final double[] data;
    final int rows, cols;

    public FastMatrix(int r, int c) {
        rows = r; cols = c;
        data = new double[r * c]; // Khối nhớ liên tục 100%!
    }

    public void set(int r, int c, double val) {
        data[r * cols + c] = val; // Toán học ánh xạ
    }
}
```
Lợi ích: Xóa bỏ 100% Object Header dư thừa (Tránh Memory Overhead), tránh 1 lần Dereference pointer, và đảm bảo Cache Locality hoàn hảo cả về chiều ngang lẫn dọc.

---

# 12 Design Decisions
**Tại sao Java thiết kế "Array of Arrays" thay vì Khối nhớ liên tục như C# (Multidimensional arrays `int[,]`)?**
Đội ngũ Sun Microsystems thiết kế Java theo triết lý "Đơn giản hóa".
1. Hỗ trợ tự nhiên Jagged Array (Mảng răng cưa) mà không cần Syntax mới.
2. Tái sử dụng JVM Bytecode: Chỉ có lệnh `aaload` (Lấy object từ mảng object) và `iaload` (Lấy int từ mảng int). `matrix[i][j]` đơn giản là gọi `aaload` (lấy ra `int[]`) rồi gọi tiếp `iaload`. Không cần lệnh bytecode riêng biệt để tính $i \times N + j$. Cực kỳ đơn giản cho kiến trúc máy ảo.

---

# 13 Common Bugs
20 lỗi thường gặp:
1. `NullPointerException`: Khởi tạo `int[][] m = new int[5][]`, sau đó gọi `m[0][0]` (Mảng con chưa được khởi tạo `new int[3]`).
2. Tưởng mảng có kích thước hình chữ nhật: Cố tình dùng `matrix[0].length` cho tất cả các hàng (Trong khi nó là Jagged array).
3. Đảo lộn `matrix[j][i]` và `matrix[i][j]` (IndexOutOfBounds nếu số hàng $\ne$ số cột).
4. Khởi tạo nhầm: `new int[Cols][Rows]` thay vì `[Rows][Cols]`.
5. Transpose mảng Không vuông (Non-square matrix) bằng in-place Swap. (Gây IndexOutOfBounds. Mảng không vuông BẮT BUỘC phải tạo ma trận mới để Transpose).
6. Lỗi Off-by-one khi duyệt đường chéo phụ: `m[i][N - i]` thay vì `m[i][N - 1 - i]`.
7. `Arrays.fill(matrix, 0)`: Gây lỗi ArrayStoreException, vì `matrix` chứa các tham chiếu `int[]`, không thể fill giá trị `0`. (Phải duyệt từng hàng `Arrays.fill(matrix[i], 0)`).
8. Shallow Copy mảng 2 chiều: `int[][] m2 = m1.clone()`. Chỉ copy mảng cha (Các references). Thay đổi mảng con của `m2` làm thay đổi `m1`. Muốn copy sâu (Deep copy), phải duyệt copy từng hàng.
9. Quên mất Matrix là Zero-indexed (VD bài toán Yêu cầu quân mã ở tọa độ $1 \dots 8$, truy xuất bằng $X, Y$ thay vì $X-1, Y-1$).
10. `Arrays.equals(m1, m2)` sẽ trả false ngay cả khi giá trị bên trong giống nhau. Phải dùng `Arrays.deepEquals()`.
11. `Arrays.toString(m1)` in ra dạng object memory address. Phải dùng `Arrays.deepToString(m1)`.
12. Vòng lặp xoắn ốc (Spiral Traverse) tính sai Boundary dẫn đến thăm 1 ô 2 lần (Overlap).
13. Thuật toán lũt (Flood Fill / DFS) quên kiểm tra biên (`r < 0 || c < 0 || r >= M || c >= N`) trước khi đệ quy.
14. Hệ tọa độ Descartes `(x,y)` ngược với Matrix `(row, col)`. `x` (ngang) = `col`. `y` (dọc) = `row`. Dùng nhầm biến x cho vòng lặp i.
15. Quét Cột trước Hàng trên mảng khổng lồ gây TLE (Time Limit Exceeded) vì Cache Miss.
16. Dùng `ArrayList<ArrayList<Integer>>` sai cách (Chưa add Row ArrayList bên trong đã truy xuất).
17. Tìm phần tử trong mảng 2 chiều sorted nhưng viết thuật toán O(M+N) bắt đầu từ sai góc (Phải bắt đầu từ Góc trên-phải hoặc dưới-trái).
18. Không xử lý mảng rỗng `if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return;`.
19. Game of Life update in-place bị sai logic (Trạng thái ô đã update ảnh hưởng tới neighbor chưa update). Cần mảng phụ hoặc state bitwise.
20. DP Table khởi tạo thừa/thiếu +1 kích thước biên gây lệch index.

---

# 14 Edge Cases
- Ma trận $0 \times 0$ (Rỗng).
- Ma trận 1 Hàng ($1 \times N$) hoặc 1 Cột ($M \times 1$).
- Jagged array có những hàng bị rỗng `matrix[i] = new int[0]`.

---

# 15 Optimization Techniques
- **Trải mảng thành 1 chiều (Flattening)**: Dùng `int[]` thay vì `int[][]` cho các bài toán cần hiệu năng cực cao về bộ nhớ.
- **Duyệt theo Block (Block Matrix Multiplication)**: Để tính nhân 2 ma trận lớn, nếu duyệt tuyến tính sẽ bị Cache Miss trầm trọng ở ma trận thứ 2. Tối ưu: Chia nhỏ ma trận thành các khối (Block) $32 \times 32$ vừa khít CPU L1 Cache.

---

# 16 Best Practices
- LUÔN LUÔN dùng vòng lặp Row (Bên ngoài) và Col (Bên trong) trừ phi có lý do bất khả kháng.
- LUÔN kiểm tra `matrix == null || matrix.length == 0` khi bắt đầu function.
- Khi cần Copy mảng 2D:
```java
int[][] copy = new int[m.length][];
for (int i = 0; i < m.length; i++) {
    copy[i] = Arrays.copyOf(m[i], m[i].length);
}
```

---

# 19 Interview Questions
20 câu hỏi:
**Easy**
1. Mảng 2D trong Java cấp phát trên Heap như thế nào?
2. Có thể tạo mảng 2D với số cột của mỗi hàng khác nhau không?
3. Cách Deep copy mảng 2D trong Java?
4. Đọc/Ghi mảng theo Hàng nhanh hơn hay theo Cột nhanh hơn? Tại sao?
5. `Arrays.toString()` trên mảng 2D in ra cái gì?

**Medium**
6. So sánh `int[][]` của Java và `int[,]` của C#.
7. Xoay ma trận vuông 90 độ In-place không dùng mảng phụ.
8. Liệt kê 4 lỗi thường gặp khi làm việc với chỉ số (row, col) và (x, y).
9. Giải thích "Cache Line" đóng vai trò thế nào khi duyệt mảng 2D.
10. Tại sao Rotate In-place chỉ hoạt động với Ma trận vuông? Nếu mảng M x N thì làm thế nào?
11. Đếm số object được cấp phát khi gọi `new String[100][100]`.
12. Công thức ánh xạ mảng 2D (M x N) xuống 1D.

**Hard & Senior**
13. Nếu bạn phải viết Game of Life (Mạng ô vuông tự động), làm sao để update in-place mà chỉ tốn O(1) space phụ?
14. Phân tích chi tiết Memory Overhead của mảng 2D trong JVM so với mảng 1D flattened.
15. Tại sao JIT Compiler của JVM rất khó gỡ bỏ hoàn toàn Bounds Checking trên mảng 2D?
16. Nếu bạn phải đọc dữ liệu ma trận từ File cực lớn (10GB), cách thiết kế cấu trúc dữ liệu trên RAM như thế nào?
17. Block Matrix Multiplication (Nhân ma trận theo khối) tối ưu L1 Cache ra sao?

---

# 20 Practice Problems Link
Xem toàn bộ 30 bài toán tại: [03-Two-Dimensional-Arrays-Problems.md](03-Two-Dimensional-Arrays-Problems.md).

---

# 23 Summary
Mảng 2 chiều là trái tim của vô số bài toán mô hình lưới (Grid), Đồ thị và DP. Hiểu bản chất "Array of Arrays" của Java sẽ giúp Kỹ sư chủ động phòng tránh Memory Overhead và các lỗi Cache Miss chết người. Kỹ thuật Flatten 2D thành 1D và cách duyệt Row-Major là hành trang không thể thiếu trong balo của Kỹ sư tối ưu hóa hiệu năng.

---

# 24 Checklist
- [ ] Duyệt mảng bằng 2 vòng lặp (Ưu tiên hàng ngoài, cột trong).
- [ ] Hiểu cách tính số Object trên Heap của `new int[M][N]`.
- [ ] Biết viết code sao chép sâu (Deep copy) mảng 2D.
- [ ] Xoay, lật, chuyển vị (Transpose) ma trận vuông.
- [ ] Nhớ cách dùng `Arrays.deepToString()` và `Arrays.deepEquals()`.
