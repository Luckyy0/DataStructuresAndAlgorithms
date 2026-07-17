# Companion Problems: 03-Two-Dimensional-Arrays

Tài liệu này cung cấp **30 bài tập thực hành** thao tác trên Mảng 2 chiều (Matrix), Grid và 2D Arrays.

---

## 1. Rotate Image (LeetCode 48)
**Đề bài chi tiết:** Cho một ma trận vuông $N \times N$ biểu diễn một hình ảnh. Xoay hình ảnh 90 độ theo chiều kim đồng hồ In-place.
**Phân tích thuật toán:** Để xoay In-place mà không cần thêm ma trận phụ, ta làm 2 bước: Bước 1: Transpose (Chuyển vị - Đổi chỗ `matrix[i][j]` và `matrix[j][i]`). Bước 2: Lật ngược (Reverse) từng hàng.
**Mã nguồn Java:**
```java
public class RotateImage {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        
        // Bước 1: Transpose
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        
        // Bước 2: Reverse từng hàng
        for (int i = 0; i < n; i++) {
            int left = 0, right = n - 1;
            while (left < right) {
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++;
                right--;
            }
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$ (Duyệt toàn ma trận), Space $\mathcal{O}(1)$.

---

## 2. Spiral Matrix (LeetCode 54)
**Đề bài chi tiết:** Trả về tất cả các phần tử của ma trận $M \times N$ theo thứ tự hình xoắn ốc (Spiral order).
**Phân tích thuật toán:** Định nghĩa 4 đường biên: `top`, `bottom`, `left`, `right`. Quét viền ngoài theo thứ tự: Trái $\rightarrow$ Phải, Trên $\rightarrow$ Dưới, Phải $\rightarrow$ Trái, Dưới $\rightarrow$ Trên. Sau mỗi lần quét, thu hẹp đường biên tương ứng. Kiểm tra điều kiện lặp để tránh trùng lặp.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class SpiralMatrix {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix.length == 0) return res;
        
        int top = 0, bottom = matrix.length - 1;
        int left = 0, right = matrix[0].length - 1;
        
        while (top <= bottom && left <= right) {
            // Trái -> Phải
            for (int i = left; i <= right; i++) res.add(matrix[top][i]);
            top++;
            
            // Trên -> Dưới
            for (int i = top; i <= bottom; i++) res.add(matrix[i][right]);
            right--;
            
            if (top <= bottom) {
                // Phải -> Trái
                for (int i = right; i >= left; i--) res.add(matrix[bottom][i]);
                bottom--;
            }
            if (left <= right) {
                // Dưới -> Trên
                for (int i = bottom; i >= top; i--) res.add(matrix[i][left]);
                left++;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$ (Không tính Output array).

---

## 3. Set Matrix Zeroes (LeetCode 73)
**Đề bài chi tiết:** Cho ma trận $M \times N$, nếu một phần tử bằng 0, hãy đặt toàn bộ hàng và cột chứa nó thành 0. Yêu cầu In-place (Space $\mathcal{O}(1)$).
**Phân tích thuật toán:** Dùng chính hàng đầu (Row 0) và cột đầu (Col 0) làm Mảng đánh dấu (Marker array). Nếu `matrix[i][j] == 0`, gán `matrix[i][0] = 0` và `matrix[0][j] = 0`. Cần 2 biến cờ phụ `row0` và `col0` để xem hàng/cột đầu tiên có chứa số 0 gốc không.
**Mã nguồn Java:**
```java
public class SetMatrixZeroes {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean firstRowZero = false, firstColZero = false;
        
        // Kiểm tra hàng 0 và cột 0
        for (int i = 0; i < m; i++) if (matrix[i][0] == 0) firstColZero = true;
        for (int j = 0; j < n; j++) if (matrix[0][j] == 0) firstRowZero = true;
        
        // Đánh dấu
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
        // Đổ Zero
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // Cập nhật lại hàng 0, cột 0
        if (firstColZero) for (int i = 0; i < m; i++) matrix[i][0] = 0;
        if (firstRowZero) for (int j = 0; j < n; j++) matrix[0][j] = 0;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$.

---

## 4. Search a 2D Matrix II (LeetCode 240)
**Đề bài chi tiết:** Tìm kiếm số `target` trong ma trận $M \times N$ với tính chất: Mọi hàng được sắp xếp tăng dần từ trái qua phải. Mọi cột được sắp xếp tăng dần từ trên xuống dưới.
**Phân tích thuật toán:** Kỹ thuật Search Space Reduction. Bắt đầu ở **Góc trên-phải** (Hoặc dưới-trái). Nếu `current > target` $\rightarrow$ Loại toàn bộ cột đó (`col--`). Nếu `current < target` $\rightarrow$ Loại toàn bộ hàng đó (`row++`). Mỗi bước loại bỏ 1 hàng hoặc cột.
**Mã nguồn Java:**
```java
public class Search2DMatrix {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        
        int row = 0;
        int col = matrix[0].length - 1; // Bắt đầu ở góc trên-phải
        
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M + N)$, Space $\mathcal{O}(1)$.

---

## 5. Pascal's Triangle (LeetCode 118)
**Đề bài chi tiết:** Tạo $N$ hàng đầu tiên của Tam giác Pascal. Output dạng `List<List<Integer>>` (Tương đương mảng 2 chiều Jagged).
**Phân tích thuật toán:** Hàng thứ $i$ có $i+1$ phần tử. Phần tử đầu và cuối của mỗi hàng là 1. Phần tử ở giữa $C[j] = prevRow[j-1] + prevRow[j]$.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class PascalsTriangle {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>();
        if (numRows == 0) return triangle;
        
        triangle.add(new ArrayList<>());
        triangle.get(0).add(1); // Hàng đầu tiên
        
        for (int i = 1; i < numRows; i++) {
            List<Integer> row = new ArrayList<>();
            List<Integer> prevRow = triangle.get(i - 1);
            
            row.add(1); // Đầu
            for (int j = 1; j < i; j++) { // Giữa
                row.add(prevRow.get(j - 1) + prevRow.get(j));
            }
            row.add(1); // Cuối
            
            triangle.add(row);
        }
        
        return triangle;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(N^2)$.

---

## 6. Diagonal Traverse (LeetCode 498)
**Đề bài chi tiết:** Trả về tất cả phần tử của ma trận $M \times N$ theo đường chéo ziczac.
**Phân tích thuật toán:** Một đường chéo có tổng tọa độ không đổi `r + c == sum`. Hướng đi phụ thuộc vào `sum` là chẵn hay lẻ. Khi chạm biên, ta chuyển hàng/cột tương ứng.
**Mã nguồn Java:**
```java
public class DiagonalTraverse {
    public int[] findDiagonalOrder(int[][] mat) {
        if (mat == null || mat.length == 0) return new int[0];
        int m = mat.length, n = mat[0].length;
        int[] res = new int[m * n];
        int r = 0, c = 0;
        
        for (int i = 0; i < res.length; i++) {
            res[i] = mat[r][c];
            if ((r + c) % 2 == 0) { // Lên trên bên phải
                if (c == n - 1) { r++; }
                else if (r == 0) { c++; }
                else { r--; c++; }
            } else { // Xuống dưới bên trái
                if (r == m - 1) { c++; }
                else if (c == 0) { r++; }
                else { r++; c--; }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$ (Nếu không tính output).

---

## 7. Game of Life (LeetCode 289)
**Đề bài chi tiết:** Cập nhật bảng theo quy luật Conway's Game of Life In-place.
**Phân tích thuật toán:** Trạng thái ô thay đổi phụ thuộc vào số hàng xóm (neighbor). Để In-place, ta dùng **Bitmask** hoặc **State Mapping**. Ví dụ: `2` = Chết thành Sống, `3` = Sống thành Chết. Sau khi tính toán xong toàn bảng, duyệt lại lần 2 gán giá trị cuối cùng.
**Mã nguồn Java:**
```java
public class GameOfLife {
    public void gameOfLife(int[][] board) {
        int m = board.length, n = board[0].length;
        int[] dx = {-1, -1, -1, 0, 0, 1, 1, 1};
        int[] dy = {-1, 0, 1, -1, 1, -1, 0, 1};
        
        // 0: dead -> dead, 1: live -> live
        // 2: dead -> live, 3: live -> dead
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int liveNeighbors = 0;
                for (int k = 0; k < 8; k++) {
                    int r = i + dx[k], c = j + dy[k];
                    if (r >= 0 && r < m && c >= 0 && c < n && (board[r][c] == 1 || board[r][c] == 3)) {
                        liveNeighbors++;
                    }
                }
                if (board[i][j] == 1 && (liveNeighbors < 2 || liveNeighbors > 3)) board[i][j] = 3;
                if (board[i][j] == 0 && liveNeighbors == 3) board[i][j] = 2;
            }
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 2) board[i][j] = 1;
                else if (board[i][j] == 3) board[i][j] = 0;
            }
        }
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$.

---

## 8. Matrix Multiplication
**Đề bài chi tiết:** Cho 2 ma trận $A$ (kích thước $M \times K$) và $B$ (kích thước $K \times N$). Tính ma trận $C = A \times B$.
**Phân tích thuật toán:** Vòng lặp truyền thống: $C_{ij} = \sum A_{ik} \times B_{kj}$. Ba vòng lặp lồng nhau: $i, j, k$.
**Mã nguồn Java:**
```java
public class MatrixMultiplication {
    public int[][] multiply(int[][] A, int[][] B) {
        int m = A.length;
        int k = A[0].length;
        int n = B[0].length;
        int[][] C = new int[m][n];
        
        // Tối ưu Cache bằng thứ tự lặp i, k, j thay vì i, j, k
        for (int i = 0; i < m; i++) {
            for (int p = 0; p < k; p++) {
                int temp = A[i][p];
                for (int j = 0; j < n; j++) {
                    C[i][j] += temp * B[p][j];
                }
            }
        }
        return C;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times K \times N)$, Space $\mathcal{O}(M \times N)$.

---

## 9. Valid Sudoku (LeetCode 36)
**Đề bài chi tiết:** Kiểm tra xem bảng Sudoku $9 \times 9$ có hợp lệ không (Chỉ cần phần hiện có, chưa cần giải).
**Phân tích thuật toán:** Cần kiểm tra Hàng, Cột, và Sub-box $3 \times 3$. Dùng 3 mảng boolean để lưu vết trạng thái số từ 1-9 đã xuất hiện. Công thức mapping index sub-box: `boxIndex = (row / 3) * 3 + (col / 3)`.
**Mã nguồn Java:**
```java
public class ValidSudoku {
    public boolean isValidSudoku(char[][] board) {
        boolean[][] rows = new boolean[9][9];
        boolean[][] cols = new boolean[9][9];
        boolean[][] boxes = new boolean[9][9];
        
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') continue;
                
                int num = board[r][c] - '1'; // 0-indexed
                int boxIndex = (r / 3) * 3 + (c / 3);
                
                if (rows[r][num] || cols[c][num] || boxes[boxIndex][num]) {
                    return false;
                }
                
                rows[r][num] = true;
                cols[c][num] = true;
                boxes[boxIndex][num] = true;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(81) \approx \mathcal{O}(1)$, Space $\mathcal{O}(81) \approx \mathcal{O}(1)$.

---

## 10. Number of Islands (LeetCode 200)
**Đề bài chi tiết:** Đếm số hòn đảo (Vùng số '1' liền kề theo 4 hướng) trên lưới 2D.
**Phân tích thuật toán:** Thuật toán duyệt Đồ thị vô hướng. Nếu gặp '1', đánh dấu là đảo (tăng count), sau đó gọi Đệ quy DFS (hoặc dùng BFS/Queue) để thăm tất cả các ô lân cận và đổi chúng thành '0' (hoặc đã thăm).
**Mã nguồn Java:**
```java
public class NumberOfIslands {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int count = 0;
        
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == '1') {
                    count++;
                    dfs(grid, r, c); // Đánh chìm hòn đảo này
                }
            }
        }
        return count;
    }
    
    private void dfs(char[][] grid, int r, int c) {
        if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] == '0') {
            return;
        }
        grid[r][c] = '0'; // Đánh dấu đã thăm
        dfs(grid, r + 1, c);
        dfs(grid, r - 1, c);
        dfs(grid, r, c + 1);
        dfs(grid, r, c - 1);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$ thăm mọi ô tối đa 2 lần. Space $\mathcal{O}(M \times N)$ cho Stack của đệ quy trong Worst-case (Toàn là đảo).

---

## 11. Max Area of Island (LeetCode 695)
**Đề bài chi tiết:** Cho một ma trận nhị phân (grid) gồm các số 0 (nước) và 1 (đảo). Một hòn đảo là một nhóm các số 1 kết nối với nhau theo 4 hướng (trên, dưới, trái, phải). Tìm diện tích lớn nhất của một hòn đảo trong ma trận. Nếu không có đảo nào, trả về 0.
**Phân tích thuật toán:** Sử dụng DFS hoặc BFS để duyệt qua từng ô của ma trận. Khi gặp một ô là đất (1), ta khởi động DFS để tính diện tích của hòn đảo đó bằng cách đệ quy đếm các ô đất lân cận và đánh dấu chúng là đã thăm (ví dụ bằng cách đổi 1 thành 0). Cập nhật diện tích lớn nhất `maxArea` sau mỗi lần duyệt một hòn đảo mới.
**Mã nguồn Java:**
```java
public class MaxAreaOfIsland {
    public int maxAreaOfIsland(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int maxArea = 0;
        
        for (int r = 0; r < grid.length; r++) {
            for (int c = 0; c < grid[0].length; c++) {
                if (grid[r][c] == 1) {
                    maxArea = Math.max(maxArea, dfs(grid, r, c));
                }
            }
        }
        return maxArea;
    }
    
    private int dfs(int[][] grid, int r, int c) {
        if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] == 0) {
            return 0;
        }
        grid[r][c] = 0; // Đánh dấu đã thăm
        int area = 1; // Tính ô hiện tại
        area += dfs(grid, r + 1, c);
        area += dfs(grid, r - 1, c);
        area += dfs(grid, r, c + 1);
        area += dfs(grid, r, c - 1);
        return area;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M \times N)$ (kích thước Call Stack trong trường hợp xấu nhất).

---

## 12. Surrounded Regions (LeetCode 130)
**Đề bài chi tiết:** Cho một ma trận $M \times N$ chứa các ký tự 'X' và 'O'. Hãy bắt mọi vùng 'O' bị bao quanh hoàn toàn bởi 'X' (chuyển các 'O' đó thành 'X'). Một vùng 'O' không bị bao quanh nếu có ít nhất một ô 'O' nằm trên đường biên của ma trận.
**Phân tích thuật toán:** Thay vì tìm các vùng bị bao quanh, ta tìm các vùng **không** bị bao quanh. Các vùng này chắc chắn phải kết nối với một ô 'O' trên biên (border). Duyệt các ô trên biên, nếu gặp 'O', dùng DFS để đánh dấu toàn bộ vùng lân cận thành ký tự đặc biệt (ví dụ 'T'). Cuối cùng, duyệt toàn bộ ma trận: đổi 'O' thành 'X' (vì bị bao quanh), và đổi 'T' về lại 'O' (không bị bao quanh).
**Mã nguồn Java:**
```java
public class SurroundedRegions {
    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        int m = board.length, n = board[0].length;
        
        // Cột đầu, cột cuối
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') dfs(board, i, 0);
            if (board[i][n - 1] == 'O') dfs(board, i, n - 1);
        }
        // Hàng đầu, hàng cuối
        for (int j = 0; j < n; j++) {
            if (board[0][j] == 'O') dfs(board, 0, j);
            if (board[m - 1][j] == 'O') dfs(board, m - 1, j);
        }
        
        // Quét lại toàn bộ ma trận
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') board[i][j] = 'X'; // Bị bao quanh
                else if (board[i][j] == 'T') board[i][j] = 'O'; // Không bị bao quanh
            }
        }
    }
    
    private void dfs(char[][] board, int r, int c) {
        if (r < 0 || c < 0 || r >= board.length || c >= board[0].length || board[r][c] != 'O') {
            return;
        }
        board[r][c] = 'T'; // Đánh dấu an toàn
        dfs(board, r + 1, c);
        dfs(board, r - 1, c);
        dfs(board, r, c + 1);
        dfs(board, r, c - 1);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M \times N)$.

---

## 13. Search a 2D Matrix (LeetCode 74)
**Đề bài chi tiết:** Viết một thuật toán hiệu quả để tìm kiếm một giá trị `target` trong ma trận $M \times N$. Ma trận này có các tính chất: Các số trong mỗi hàng được sắp xếp tăng dần từ trái qua phải. Số đầu tiên của một hàng lớn hơn số cuối cùng của hàng trước đó.
**Phân tích thuật toán:** Nhờ hai tính chất trên, nếu trải phẳng (flatten) ma trận thành mảng 1D, ta sẽ được một mảng tăng dần. Do đó, có thể áp dụng thuật toán Binary Search tiêu chuẩn. Chỉ số của mảng 1D là `mid`, ta ánh xạ lại vào ma trận 2D bằng công thức `row = mid / cols`, `col = mid % cols`.
**Mã nguồn Java:**
```java
public class Search2DMatrixI {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int left = 0, right = m * n - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midValue = matrix[mid / n][mid % n]; // Ánh xạ 1D -> 2D
            
            if (midValue == target) {
                return true;
            } else if (midValue < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(\log(M \times N))$, Space $\mathcal{O}(1)$.

---

## 14. Toeplitz Matrix (LeetCode 766)
**Đề bài chi tiết:** Một ma trận được gọi là Toeplitz nếu mọi đường chéo chính (từ trên-trái xuống dưới-phải) đều chứa các phần tử giống hệt nhau. Cho ma trận $M \times N$, trả về `true` nếu nó là ma trận Toeplitz, ngược lại trả về `false`.
**Phân tích thuật toán:** Để kiểm tra điều kiện trên, ta chỉ cần so sánh mỗi phần tử với phần tử nằm ở vị trí trên-trái của nó (tức là so sánh `matrix[i][j]` với `matrix[i-1][j-1]`). Nếu có bất kỳ cặp nào khác nhau, ma trận không phải là Toeplitz. Ta bắt đầu duyệt từ hàng 1 và cột 1.
**Mã nguồn Java:**
```java
public class ToeplitzMatrix {
    public boolean isToeplitzMatrix(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] != matrix[i - 1][j - 1]) {
                    return false; // Phát hiện sai khác trên đường chéo
                }
            }
        }
        
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$.

---

## 15. Reshape the Matrix (LeetCode 566)
**Đề bài chi tiết:** Trong MATLAB, hàm `reshape` có thể thay đổi kích thước ma trận $M \times N$ thành một kích thước mới $R \times C$ trong khi vẫn giữ nguyên dữ liệu gốc theo thứ tự quét hàng (row-traversing order). Nếu lệnh `reshape` hợp lệ, trả về ma trận mới. Nếu không hợp lệ (số lượng phần tử không khớp), trả về ma trận gốc.
**Phân tích thuật toán:** Kiểm tra $M \times N == R \times C$. Nếu không bằng, trả về ma trận cũ. Nếu bằng, khởi tạo ma trận mới. Dùng một biến `count` chạy từ $0$ đến $M \times N - 1$. Tọa độ ô trong ma trận mới là `[count / c][count % c]`, tọa độ trong ma trận cũ là `[count / n][count % n]`.
**Mã nguồn Java:**
```java
public class ReshapeTheMatrix {
    public int[][] matrixReshape(int[][] mat, int r, int c) {
        int m = mat.length;
        int n = mat[0].length;
        
        // Kiểm tra tính hợp lệ
        if (m * n != r * c) {
            return mat;
        }
        
        int[][] res = new int[r][c];
        int count = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                res[count / c][count % c] = mat[i][j];
                count++;
            }
        }
        
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(R \times C)$ (Ma trận kết quả).

---

## 16. Magic Squares In Grid (LeetCode 840)
**Đề bài chi tiết:** Một Magic Square kích thước $3 \times 3$ là một lưới điền các số riêng biệt từ 1 đến 9 sao cho tổng của mỗi hàng, mỗi cột và cả hai đường chéo đều bằng nhau (và bằng 15). Cho một lưới grid kích thước $M \times N$, đếm số lượng Magic Square $3 \times 3$ có trong lưới.
**Phân tích thuật toán:** Duyệt qua mọi ô $(i, j)$ có thể làm góc trên bên trái của lưới $3 \times 3$. Ta viết một hàm `isMagic(grid, i, j)` để kiểm tra lưới con đó. Điều kiện: phải chứa các số từ 1-9 không trùng lặp, và tổng các hàng/cột/chéo đều bằng 15.
**Mã nguồn Java:**
```java
public class MagicSquaresInGrid {
    public int numMagicSquaresInside(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int count = 0;
        
        for (int i = 0; i <= m - 3; i++) {
            for (int j = 0; j <= n - 3; j++) {
                if (isMagic(grid, i, j)) count++;
            }
        }
        return count;
    }
    
    private boolean isMagic(int[][] grid, int r, int c) {
        boolean[] seen = new boolean[10];
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                int val = grid[r + i][c + j];
                if (val < 1 || val > 9 || seen[val]) return false;
                seen[val] = true;
            }
        }
        // Kiểm tra tổng hàng, cột, chéo
        int sum1 = grid[r][c] + grid[r][c+1] + grid[r][c+2];
        int sum2 = grid[r+1][c] + grid[r+1][c+1] + grid[r+1][c+2];
        int sum3 = grid[r+2][c] + grid[r+2][c+1] + grid[r+2][c+2];
        int col1 = grid[r][c] + grid[r+1][c] + grid[r+2][c];
        int col2 = grid[r][c+1] + grid[r+1][c+1] + grid[r+2][c+1];
        int col3 = grid[r][c+2] + grid[r+1][c+2] + grid[r+2][c+2];
        int diag1 = grid[r][c] + grid[r+1][c+1] + grid[r+2][c+2];
        int diag2 = grid[r][c+2] + grid[r+1][c+1] + grid[r+2][c];
        
        return sum1 == 15 && sum2 == 15 && sum3 == 15 && 
               col1 == 15 && col2 == 15 && col3 == 15 && 
               diag1 == 15 && diag2 == 15;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$ do mỗi ô kiểm tra tốn $\mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 17. Lucky Numbers in a Matrix (LeetCode 1380)
**Đề bài chi tiết:** Cho ma trận $M \times N$ chứa các số khác nhau. Trả về tất cả các số may mắn (lucky numbers). Một số may mắn là phần tử nhỏ nhất trong hàng chứa nó và đồng thời lớn nhất trong cột chứa nó.
**Phân tích thuật toán:** Cách đơn giản là duyệt tìm giá trị nhỏ nhất của mỗi hàng (lưu vào một mảng `rowMins`) và lớn nhất của mỗi cột (lưu vào mảng `colMaxs`). Sau đó duyệt lại toàn bộ ma trận, nếu `matrix[i][j] == rowMins[i] && matrix[i][j] == colMaxs[j]`, thì đó là số may mắn.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class LuckyNumbersInMatrix {
    public List<Integer> luckyNumbers (int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int[] rowMins = new int[m];
        int[] colMaxs = new int[n];
        
        for (int i = 0; i < m; i++) {
            rowMins[i] = Integer.MAX_VALUE;
            for (int j = 0; j < n; j++) {
                rowMins[i] = Math.min(rowMins[i], matrix[i][j]);
                colMaxs[j] = Math.max(colMaxs[j], matrix[i][j]);
            }
        }
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == rowMins[i] && matrix[i][j] == colMaxs[j]) {
                    res.add(matrix[i][j]);
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M + N)$.

---

## 18. Kth Smallest Element in a Sorted Matrix (LeetCode 378)
**Đề bài chi tiết:** Cho một ma trận $N \times N$ mà mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự tăng dần. Tìm phần tử nhỏ thứ K trong ma trận. Cần tối ưu sao cho tốt hơn $\mathcal{O}(N^2 \log(N^2))$.
**Phân tích thuật toán:** Giải pháp tối ưu là Binary Search trên không gian giá trị (Value Space). Khoảng tìm kiếm từ `low = matrix[0][0]` đến `high = matrix[N-1][N-1]`. Với mỗi `mid`, ta đếm số lượng phần tử $\le mid$ trong ma trận. Nếu số lượng $\ge K$, ta tìm tiếp trong nửa trái (`high = mid`). Ngược lại, ta tìm nửa phải (`low = mid + 1`).
**Mã nguồn Java:**
```java
public class KthSmallestInSortedMatrix {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int low = matrix[0][0], high = matrix[n - 1][n - 1];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countLessOrEqual(matrix, mid);
            
            if (count >= k) {
                high = mid; // Có thể phần tử mid chính là đáp án
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
    
    private int countLessOrEqual(int[][] matrix, int mid) {
        int count = 0;
        int n = matrix.length;
        int row = n - 1; // Bắt đầu từ góc dưới trái
        int col = 0;
        
        while (row >= 0 && col < n) {
            if (matrix[row][col] <= mid) {
                count += row + 1; // Tất cả các phần tử phía trên cột này đều <= mid
                col++;
            } else {
                row--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log(\text{Max} - \text{Min}))$, Space $\mathcal{O}(1)$.

---

## 19. Check if Matrix Is X-Matrix (LeetCode 2319)
**Đề bài chi tiết:** Một ma trận vuông được gọi là X-Matrix nếu cả hai đường chéo chính chứa toàn số khác 0, và mọi phần tử không thuộc hai đường chéo này đều bằng 0. Cho ma trận vuông `grid`, kiểm tra xem nó có phải X-Matrix hay không.
**Phân tích thuật toán:** Duyệt mọi ô $(i, j)$ của ma trận. Một ô thuộc đường chéo nếu `i == j` (chéo chính) hoặc `i + j == n - 1` (chéo phụ). Nếu ô thuộc chéo, nó phải $\ne 0$. Nếu ô không thuộc chéo, nó phải $= 0$.
**Mã nguồn Java:**
```java
public class XMatrix {
    public boolean checkXMatrix(int[][] grid) {
        int n = grid.length;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                boolean isDiagonal = (i == j) || (i + j == n - 1);
                
                if (isDiagonal) {
                    if (grid[i][j] == 0) return false;
                } else {
                    if (grid[i][j] != 0) return false;
                }
            }
        }
        
        return true;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$, Space $\mathcal{O}(1)$.

---

## 20. Spiral Matrix II (LeetCode 59)
**Đề bài chi tiết:** Cho số nguyên dương $N$, hãy tạo một ma trận vuông $N \times N$ và điền vào đó các số từ 1 đến $N^2$ theo thứ tự hình xoắn ốc (Spiral order).
**Phân tích thuật toán:** Tương tự bài Spiral Matrix, ta định nghĩa 4 ranh giới: `top`, `bottom`, `left`, `right`. Sử dụng một biến đếm `num` từ 1 tăng dần. Chạy vòng lặp điền giá trị theo các hướng: Trái sang Phải, Trên xuống Dưới, Phải sang Trái, Dưới lên Trên, sau đó thu hẹp ranh giới.
**Mã nguồn Java:**
```java
public class SpiralMatrixII {
    public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int top = 0, bottom = n - 1;
        int left = 0, right = n - 1;
        int num = 1;
        
        while (num <= n * n) {
            // Trái -> Phải
            for (int i = left; i <= right; i++) matrix[top][i] = num++;
            top++;
            
            // Trên -> Dưới
            for (int i = top; i <= bottom; i++) matrix[i][right] = num++;
            right--;
            
            // Phải -> Trái
            for (int i = right; i >= left; i--) matrix[bottom][i] = num++;
            bottom--;
            
            // Dưới -> Trên
            for (int i = bottom; i >= top; i--) matrix[i][left] = num++;
            left++;
        }
        
        return matrix;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$ (điền đúng $N^2$ ô), Space $\mathcal{O}(N^2)$ (Ma trận output).

---

## 21. Count Negative Numbers in a Sorted Matrix (LeetCode 1351)
**Đề bài chi tiết:** Cho một ma trận $M \times N$ `grid` trong đó các hàng và cột đều được sắp xếp theo thứ tự giảm dần. Đếm số lượng các số âm trong `grid`.
**Phân tích thuật toán:** Bắt đầu từ góc trên bên phải (hàng 0, cột $N-1$). Nếu phần tử hiện tại là số âm, thì toàn bộ các phần tử phía dưới nó trong cột đó cũng là số âm (vì cột giảm dần). Do đó, ta cộng số lượng phần tử còn lại của cột vào tổng, rồi dịch sang trái (`c--`). Nếu phần tử hiện tại không âm, ta đi xuống dưới (`r++`).
**Mã nguồn Java:**
```java
public class CountNegativeNumbers {
    public int countNegatives(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int r = 0, c = n - 1;
        int count = 0;
        
        while (r < m && c >= 0) {
            if (grid[r][c] < 0) {
                count += (m - r); // Các phần tử bên dưới đều âm
                c--; // Sang trái
            } else {
                r++; // Xuống dưới
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M + N)$, Space $\mathcal{O}(1)$.

---

## 22. Flipping an Image (LeetCode 832)
**Đề bài chi tiết:** Cho một ma trận nhị phân `image` kích thước $N \times N$, hãy lật theo chiều ngang (flip horizontally) rồi đảo ngược (invert) nó, và trả về kết quả. Lật ngang nghĩa là đảo ngược mỗi hàng. Đảo ngược nghĩa là thay $0$ bằng $1$ và $1$ bằng $0$.
**Phân tích thuật toán:** Hai thao tác này có thể thực hiện đồng thời. Ta sử dụng hai con trỏ `left` và `right` cho từng hàng. Đổi chỗ phần tử ở `left` và `right`, đồng thời lấy toán tử XOR với 1 (hoặc $1 - x$) để đảo ngược bit. Nếu `left == right` (độ dài lẻ), ta chỉ cần đảo ngược bit đó.
**Mã nguồn Java:**
```java
public class FlippingAnImage {
    public int[][] flipAndInvertImage(int[][] image) {
        int n = image.length;
        
        for (int i = 0; i < n; i++) {
            int left = 0, right = n - 1;
            while (left <= right) {
                // Đảo ngược vị trí và đảo bit cùng lúc
                int temp = image[i][left] ^ 1;
                image[i][left] = image[i][right] ^ 1;
                image[i][right] = temp;
                left++;
                right--;
            }
        }
        return image;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$ (duyệt nửa mảng cho mỗi hàng), Space $\mathcal{O}(1)$ In-place.

---

## 23. The K Weakest Rows in a Matrix (LeetCode 1337)
**Đề bài chi tiết:** Cho ma trận nhị phân $M \times N$ đại diện cho hàng đợi lính (1) và thường dân (0). Các số 1 luôn đứng trước số 0 trong mỗi hàng. Một hàng được gọi là yếu hơn hàng khác nếu nó có ít lính hơn, hoặc cùng số lính nhưng có chỉ số (index) nhỏ hơn. Tìm $K$ hàng yếu nhất.
**Phân tích thuật toán:** Số quân lính của mỗi hàng chính là số lượng số 1. Vì số 1 đứng trước số 0, ta có thể dùng Binary Search để tìm số quân lính tốn $\mathcal{O}(\log N)$. Để tìm $K$ hàng yếu nhất, ta đẩy cặp `[số lính, chỉ số hàng]` vào Max-Heap (PriorityQueue). Max-Heap duy trì kích thước $K$.
**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class KWeakestRows {
    public int[] kWeakestRows(int[][] mat, int k) {
        // [số lính, chỉ số hàng], sắp xếp giảm dần để tạo Max-Heap
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            if (a[0] == b[0]) return b[1] - a[1];
            return b[0] - a[0];
        });
        
        for (int i = 0; i < mat.length; i++) {
            int soldiers = countSoldiers(mat[i]);
            pq.offer(new int[]{soldiers, i});
            if (pq.size() > k) {
                pq.poll();
            }
        }
        
        int[] res = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            res[i] = pq.poll()[1];
        }
        return res;
    }
    
    private int countSoldiers(int[] row) {
        int left = 0, right = row.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (row[mid] == 1) left = mid + 1;
            else right = mid - 1;
        }
        return left;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \log N + M \log K)$, Space $\mathcal{O}(K)$ cho Max-Heap.

---

## 24. Shift 2D Grid (LeetCode 1260)
**Đề bài chi tiết:** Cho một ma trận $M \times N$ `grid` và số nguyên `k`. Hãy dịch chuyển (shift) grid $K$ lần. Một lần dịch chuyển có nghĩa là: Phần tử `grid[i][j]` chuyển sang `grid[i][j+1]`; `grid[i][n-1]` chuyển thành `grid[i+1][0]`; và `grid[m-1][n-1]` chuyển thành `grid[0][0]`.
**Phân tích thuật toán:** Việc dịch chuyển 2D thực chất là dịch chuyển mảng 1D vòng quanh (circular shift). Tổng số phần tử là $Total = M \times N$. Dịch chuyển $K$ bước tương đương chuyển đổi tọa độ phẳng. Phần tử có thứ tự phẳng $p$ sẽ được chuyển đến $(p + k) \pmod{Total}$. Từ thứ tự phẳng ta tính lại tọa độ 2D là `row = p / n` và `col = p % n`.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Shift2DGrid {
    public List<List<Integer>> shiftGrid(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        int total = m * n;
        k = k % total;
        
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            List<Integer> row = new ArrayList<>();
            for (int j = 0; j < n; j++) row.add(0);
            res.add(row);
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int flatIndex = i * n + j;
                int newIndex = (flatIndex + k) % total;
                int newRow = newIndex / n;
                int newCol = newIndex % n;
                res.get(newRow).set(newCol, grid[i][j]);
            }
        }
        return res;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M \times N)$ cho output.

---

## 25. Available Captures for Rook (LeetCode 999)
**Đề bài chi tiết:** Trên bàn cờ $8 \times 8$, có một quân Xe (Rook - 'R'), một số quân Tượng (Bishop - 'B') và Tốt (Pawn - 'p') màu đen. Xe di chuyển ngang dọc không giới hạn nhưng dừng lại khi gặp Tượng (block). Đếm số lượng Tốt đen mà Xe có thể ăn trong một nước đi (tối đa là 4 hướng).
**Phân tích thuật toán:** Đầu tiên, tìm tọa độ của Xe. Sau đó duyệt 4 hướng: Lên, Xuống, Trái, Phải. Đi theo mỗi hướng cho tới khi ra khỏi bàn cờ, hoặc gặp Tượng 'B', hoặc gặp Tốt 'p'. Nếu gặp 'p', tính là 1 lần ăn và dừng hướng đó.
**Mã nguồn Java:**
```java
public class CapturesForRook {
    public int numRookCaptures(char[][] board) {
        int r = 0, c = 0;
        // Tìm quân Xe
        for (int i = 0; i < 8; i++) {
            for (int j = 0; j < 8; j++) {
                if (board[i][j] == 'R') {
                    r = i; c = j;
                    break;
                }
            }
        }
        
        int captures = 0;
        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        
        for (int[] d : dirs) {
            int x = r + d[0], y = c + d[1];
            while (x >= 0 && x < 8 && y >= 0 && y < 8) {
                if (board[x][y] == 'B') break; // Bị chặn
                if (board[x][y] == 'p') {
                    captures++;
                    break; // Ăn chốt rồi dừng
                }
                x += d[0];
                y += d[1];
            }
        }
        return captures;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(64) \approx \mathcal{O}(1)$, Space $\mathcal{O}(1)$.

---

## 26. Island Perimeter (LeetCode 463)
**Đề bài chi tiết:** Cho lưới $M \times N$ trong đó 1 là đất, 0 là nước. Các ô đất kết nối với nhau tạo thành một hòn đảo duy nhất không có hồ (nước bên trong). Tính chu vi của hòn đảo.
**Phân tích thuật toán:** Mỗi ô đất có 4 cạnh (đóng góp 4 vào chu vi). Tuy nhiên, nếu hai ô đất liền kề nhau, cạnh chung giữa chúng không thuộc chu vi. Do đó, chu vi bằng $4 \times \text{số đất} - 2 \times \text{số cạnh liền kề}$. Ta chỉ cần kiểm tra cạnh lân cận bên phải và bên dưới để tránh đếm trùng.
**Mã nguồn Java:**
```java
public class IslandPerimeter {
    public int islandPerimeter(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int land = 0, neighbor = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    land++;
                    if (i < m - 1 && grid[i + 1][j] == 1) neighbor++; // Liền kề dưới
                    if (j < n - 1 && grid[i][j + 1] == 1) neighbor++; // Liền kề phải
                }
            }
        }
        
        return land * 4 - neighbor * 2;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$.

---

## 27. Flood Fill (LeetCode 733)
**Đề bài chi tiết:** Một hình ảnh được biểu diễn bởi một mảng số nguyên 2D `image`. Cho tọa độ `(sr, sc)` của ô khởi đầu và màu mới `newColor`, hãy thực hiện Flood Fill (tương tự công cụ thùng sơn Paint). Thay đổi màu ô khởi đầu và tất cả ô lân cận kết nối theo 4 hướng có cùng màu cũ thành màu mới.
**Phân tích thuật toán:** Thuật toán duyệt Đồ thị cơ bản (DFS hoặc BFS). Nếu `newColor` giống màu cũ của `image[sr][sc]`, bỏ qua để tránh vòng lặp vô hạn. Dùng DFS đệ quy: đổi màu ô hiện tại thành `newColor`, sau đó tiếp tục gọi đệ quy cho 4 ô lân cận nếu chúng trùng màu cũ.
**Mã nguồn Java:**
```java
public class FloodFill {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int oldColor = image[sr][sc];
        if (oldColor != newColor) {
            dfs(image, sr, sc, oldColor, newColor);
        }
        return image;
    }
    
    private void dfs(int[][] image, int r, int c, int oldColor, int newColor) {
        if (r < 0 || c < 0 || r >= image.length || c >= image[0].length || image[r][c] != oldColor) {
            return;
        }
        image[r][c] = newColor; // Đổi màu
        dfs(image, r + 1, c, oldColor, newColor);
        dfs(image, r - 1, c, oldColor, newColor);
        dfs(image, r, c + 1, oldColor, newColor);
        dfs(image, r, c - 1, oldColor, newColor);
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$ (số lượng pixel thay màu tối đa bằng kích thước mảng), Space $\mathcal{O}(M \times N)$ cho Call Stack.

---

## 28. Rotting Oranges (LeetCode 994)
**Đề bài chi tiết:** Trong lưới `grid`, $0$ là ô trống, $1$ là cam tươi, $2$ là cam thối. Mỗi phút, một trái cam tươi nằm liền kề 4 hướng với trái cam thối sẽ bị lây nhiễm và trở thành thối. Tính số phút tối thiểu để không còn trái cam tươi nào, hoặc trả về -1 nếu không thể.
**Phân tích thuật toán:** Bài toán tìm đường đi ngắn nhất nhiều nguồn (Multi-source Shortest Path). Sử dụng BFS, đặt tất cả cam thối ban đầu vào Queue và đếm số lượng cam tươi. Mỗi mức (level) của BFS tương đương với 1 phút. Cập nhật số cam tươi lây nhiễm. Cuối cùng, nếu số cam tươi bằng 0 thì trả về số phút, ngược lại trả về -1.
**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class RottingOranges {
    public int orangesRotting(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        Queue<int[]> queue = new LinkedList<>();
        int fresh = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 2) queue.offer(new int[]{i, j});
                else if (grid[i][j] == 1) fresh++;
            }
        }
        
        if (fresh == 0) return 0; // Đã thối toàn bộ hoặc trống
        
        int minutes = 0;
        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        
        while (!queue.isEmpty() && fresh > 0) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                for (int[] d : dirs) {
                    int r = curr[0] + d[0], c = curr[1] + d[1];
                    if (r >= 0 && r < m && c >= 0 && c < n && grid[r][c] == 1) {
                        grid[r][c] = 2; // Bị thối
                        fresh--;
                        queue.offer(new int[]{r, c});
                    }
                }
            }
            minutes++;
        }
        return fresh == 0 ? minutes : -1; // Trừ trường hợp lan cuối cùng nếu tính nhầm
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M \times N)$.

---

## 29. Shortest Path in Binary Matrix (LeetCode 1091)
**Đề bài chi tiết:** Cho lưới $N \times N$, 0 là ô đi được, 1 là tường cản. Tìm độ dài đường đi ngắn nhất (Clear Path) từ góc trên trái `(0, 0)` tới dưới phải `(N-1, N-1)` đi theo 8 hướng. Độ dài tính bằng số ô đã đi qua. Nếu không có đường đi, trả về -1.
**Phân tích thuật toán:** Bài toán tìm đường đi ngắn nhất trên đồ thị không trọng số $\rightarrow$ Dùng BFS. Nếu điểm bắt đầu hoặc điểm cuối là 1, lập tức trả về -1. Khởi tạo Queue bắt đầu từ `(0,0)` với quãng đường là 1. Tại mỗi ô, duyệt 8 hướng liền kề. Nếu gặp số 0, cập nhật nó thành 1 (hoặc một số đánh dấu) để tránh thăm lại và đưa vào Queue.
**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class ShortestPathBinaryMatrix {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) return -1;
        
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 1}); // [r, c, distance]
        grid[0][0] = 1; // Đánh dấu đã thăm
        
        int[][] dirs = {
            {-1, -1}, {-1, 0}, {-1, 1},
            {0, -1},           {0, 1},
            {1, -1},  {1, 0},  {1, 1}
        };
        
        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0], c = curr[1], dist = curr[2];
            
            if (r == n - 1 && c == n - 1) return dist;
            
            for (int[] d : dirs) {
                int nr = r + d[0], nc = c + d[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0) {
                    grid[nr][nc] = 1; // Thăm và đóng ô lại
                    queue.offer(new int[]{nr, nc, dist + 1});
                }
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N^2)$ (mỗi ô thăm tối đa 1 lần), Space $\mathcal{O}(N^2)$ cho Queue.

---

## 30. Minimum Path Sum (LeetCode 64)
**Đề bài chi tiết:** Cho một ma trận $M \times N$ chứa các số nguyên không âm. Tìm một đường đi từ góc trên-trái xuống góc dưới-phải (chỉ được đi sang phải hoặc xuống dưới) sao cho tổng các con số trên đường đi là nhỏ nhất.
**Phân tích thuật toán:** Bài toán Quy hoạch động (Dynamic Programming). Trạng thái $DP[i][j]$ là tổng chi phí nhỏ nhất để đi đến ô $(i, j)$. Công thức truy hồi: $DP[i][j] = \text{grid}[i][j] + \min(DP[i-1][j], DP[i][j-1])$. Để tối ưu không gian In-place, ta có thể ghi đè trực tiếp lên ma trận đầu vào `grid`.
**Mã nguồn Java:**
```java
public class MinimumPathSum {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        
        // Cập nhật hàng đầu tiên (chỉ có thể đi từ trái sang)
        for (int i = 1; i < n; i++) {
            grid[0][i] += grid[0][i - 1];
        }
        
        // Cập nhật cột đầu tiên (chỉ có thể đi từ trên xuống)
        for (int i = 1; i < m; i++) {
            grid[i][0] += grid[i - 1][0];
        }
        
        // Cập nhật các ô còn lại
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                grid[i][j] += Math.min(grid[i - 1][j], grid[i][j - 1]);
            }
        }
        
        return grid[m - 1][n - 1];
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(1)$ do tính In-place (hoặc $\mathcal{O}(N)$ nếu sử dụng DP 1D để không đổi ma trận gốc).

---

