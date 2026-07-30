# Binary Search on Matrices - Bài tập thực hành

Tài liệu này cung cấp 30 bài tập ứng dụng kỹ thuật Tìm kiếm nhị phân trên Ma trận (Binary Search on Matrices). 10 bài đầu tiên bao gồm phân tích chi tiết và mã nguồn Java, 20 bài sau được tóm tắt.

---

## 1. Search a 2D Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` chứa số nguyên dương. Ma trận có hai tính chất:
1. Mỗi hàng được sắp xếp tăng dần từ trái sang phải.
2. Phần tử đầu tiên của mỗi hàng lớn hơn phần tử cuối cùng của hàng trước đó.
Kiểm tra xem `target` có tồn tại trong ma trận hay không.

**Phân tích thuật toán:**
Nhờ tính chất (2), ta có thể "trải phẳng" (flatten) ma trận thành một mảng 1D đã sắp xếp. Kích thước ảo là `M * N`. Chỉ số `mid` trong mảng 1D được ánh xạ về ma trận 2D thông qua công thức `row = mid / n` và `col = mid % n`. Áp dụng Binary Search chuẩn trên không gian 1D này.

**Mã nguồn Java:**
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int low = 0, high = m * n - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int midVal = matrix[mid / n][mid % n];
            
            if (midVal == target) {
                return true;
            } else if (midVal < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return false;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(\log(m \times n))$
- **Không gian (Space):** $O(1)$

---

## 2. Search a 2D Matrix II
**Đề bài chi tiết:**
Cho ma trận `m x n` trong đó mỗi hàng sắp xếp tăng dần từ trái qua phải, mỗi cột sắp xếp tăng dần từ trên xuống dưới. Tìm `target` trong ma trận.

**Phân tích thuật toán:**
Vì hàng sau không nối tiếp hàng trước (không bảo toàn tính chất 1D), ta duyệt từ góc trên cùng bên phải `(0, n-1)`. Tại đây, mọi phần tử bên dưới cùng cột đều lớn hơn nó, mọi phần tử bên trái cùng hàng đều nhỏ hơn nó. So sánh phần tử này với `target` để quyết định loại bỏ cả một cột (sang trái) hoặc loại bỏ cả một hàng (xuống dưới).

**Mã nguồn Java:**
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return false;
        
        int row = 0;
        int col = matrix[0].length - 1;
        
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
**Độ phức tạp:**
- **Thời gian (Time):** $O(m + n)$
- **Không gian (Space):** $O(1)$

---

## 3. Kth Smallest Element in a Sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `n x n` mà mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự tăng dần. Tìm phần tử nhỏ thứ `k` trong ma trận.

**Phân tích thuật toán:**
Sử dụng Binary Search trên không gian giá trị (Value Range). Đặt `low = matrix[0][0]` và `high = matrix[n-1][n-1]`. Ở mỗi vòng, tính `mid` và đếm số phần tử trong ma trận $\le mid$. Nếu số phần tử này $< k$, nghĩa là đáp án phải lớn hơn `mid`, ta tăng `low = mid + 1`. Nếu $\ge k$, thu hẹp `high = mid`. Hàm đếm có thể chạy trong $O(n)$ bằng cách duyệt từ góc dưới bên trái (Bottom-Left).

**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int low = matrix[0][0];
        int high = matrix[n - 1][n - 1];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countLessOrEqual(matrix, mid);
            if (count < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int countLessOrEqual(int[][] matrix, int val) {
        int count = 0;
        int n = matrix.length;
        int r = n - 1;
        int c = 0;
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= val) {
                count += r + 1;
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n \log(\text{max} - \text{min}))$
- **Không gian (Space):** $O(1)$

---

## 4. Count Negative Numbers in a Sorted Matrix
**Đề bài chi tiết:**
Cho một ma trận `m x n` được sắp xếp giảm dần cả theo hàng và theo cột. Đếm số lượng các số âm trong ma trận.

**Phân tích thuật toán:**
Bắt đầu từ góc trên bên phải hoặc dưới bên trái. Ví dụ, xuất phát từ dưới cùng bên trái `(m-1, 0)`. Nếu tại đó là số âm, toàn bộ các phần tử cùng cột trên nó (từ 0 đến m-1) chưa chắc là âm, nhưng toàn bộ bên phải cùng hàng (từ 0 đến n-1) chắc chắn là âm. Ta đếm và nhảy hàng lên trên. Nếu dương, nhảy cột sang phải.

**Mã nguồn Java:**
```java
class Solution {
    public int countNegatives(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int r = m - 1;
        int c = 0;
        int count = 0;
        
        while (r >= 0 && c < n) {
            if (grid[r][c] < 0) {
                count += (n - c);
                r--;
            } else {
                c++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m + n)$
- **Không gian (Space):** $O(1)$

---

## 5. Find a Peak Element II
**Đề bài chi tiết:**
Một phần tử đỉnh (peak) trong ma trận 2D là phần tử lớn hơn nghiêm ngặt cả 4 phần tử kề cạnh nó (trên, dưới, trái, phải). Ma trận không có 2 phần tử liền kề bằng nhau. Tìm vị trí của một phần tử đỉnh bất kỳ.

**Phân tích thuật toán:**
Sử dụng Binary Search trên số lượng cột. Chọn cột giữa `mid`, tìm vị trí hàng `max_row` có phần tử lớn nhất trong cột `mid`. So sánh phần tử này với 2 phần tử bên trái và phải của nó. Nếu nó lớn hơn cả 2, đây là đỉnh. Nếu nó nhỏ hơn phần tử bên trái, tiếp tục tìm ở nửa trái. Nếu nhỏ hơn bên phải, tìm nửa phải.

**Mã nguồn Java:**
```java
class Solution {
    public int[] findPeakGrid(int[][] mat) {
        int low = 0, high = mat[0].length - 1;
        while (low <= high) {
            int midCol = low + (high - low) / 2;
            int maxRow = 0;
            for (int i = 0; i < mat.length; i++) {
                if (mat[i][midCol] > mat[maxRow][midCol]) {
                    maxRow = i;
                }
            }
            
            boolean leftIsGreater = (midCol - 1 >= low) && (mat[maxRow][midCol - 1] > mat[maxRow][midCol]);
            boolean rightIsGreater = (midCol + 1 <= high) && (mat[maxRow][midCol + 1] > mat[maxRow][midCol]);
            
            if (!leftIsGreater && !rightIsGreater) {
                return new int[]{maxRow, midCol};
            } else if (leftIsGreater) {
                high = midCol - 1;
            } else {
                low = midCol + 1;
            }
        }
        return new int[]{-1, -1};
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \log n)$
- **Không gian (Space):** $O(1)$

---

## 6. Median in a row-wise sorted Matrix
**Đề bài chi tiết:**
Cho ma trận có `r` hàng, `c` cột (với `r * c` là số lẻ), mỗi hàng được sắp xếp tăng dần. Tìm trung vị (Median) của toàn bộ ma trận.

**Phân tích thuật toán:**
Tương tự bài Kth Smallest, dùng Binary Search trên khoảng giá trị từ phần tử nhỏ nhất đến phần tử lớn nhất trong ma trận. Giá trị trung vị là phần tử mà số lượng phần tử $\le$ nó trong toàn ma trận tối thiểu là `(r * c) / 2 + 1`. Do mỗi hàng đã sắp xếp, đếm phần tử $\le mid$ trên mỗi hàng dùng `Arrays.binarySearch` (hoặc `upper_bound`).

**Mã nguồn Java:**
```java
public class Solution {
    public int findMedian(int[][] A) {
        int r = A.length;
        int c = A[0].length;
        int low = 1, high = 1000000000;
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            for (int i = 0; i < r; i++) {
                count += countSmallerOrEqual(A[i], mid);
            }
            
            if (count < (r * c) / 2 + 1) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int countSmallerOrEqual(int[] row, int val) {
        int l = 0, h = row.length - 1;
        while (l <= h) {
            int m = l + (h - l) / 2;
            if (row[m] <= val) l = m + 1;
            else h = m - 1;
        }
        return l;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(r \log c \log(max - min))$
- **Không gian (Space):** $O(1)$

---

## 7. Kth Smallest Number in Multiplication Table
**Đề bài chi tiết:**
Cho bảng cửu chương ảo kích thước `m x n` (giá trị ô `(i, j)` là `i * j` với index hệ 1). Tìm phần tử nhỏ thứ `k` trong bảng này.

**Phân tích thuật toán:**
Dùng Binary Search over value range. Khoảng giá trị `low = 1, high = m * n`. Ở mỗi bước thử `mid`, đếm số lượng phần tử $\le mid$. Trong một hàng thứ `i` (các bội số của `i`), số phần tử $\le mid$ là `min(mid / i, n)`. Cộng tổng lại để kiểm tra điều kiện dịch `low` hoặc `high`.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthNumber(int m, int n, int k) {
        int low = 1;
        int high = m * n;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (count(mid, m, n) < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int count(int val, int m, int n) {
        int count = 0;
        for (int i = 1; i <= m; i++) {
            count += Math.min(val / i, n);
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \log(m \times n))$
- **Không gian (Space):** $O(1)$

---

## 8. Find the Kth Smallest Sum of a Matrix With Sorted Rows
**Đề bài chi tiết:**
Cho ma trận `m x n` có các hàng đã được sắp xếp tăng dần. Bạn cần chọn đúng 1 phần tử từ mỗi hàng để tạo ra 1 mảng. Tìm tổng nhỏ thứ `k` của tất cả các mảng có thể tạo ra.

**Phân tích thuật toán:**
Dùng Priority Queue (Min Heap) tương tự như gom nhóm từng mảng một hoặc dùng Binary Search qua value range để check xem số lượng tổng $\le mid$ có đủ `k` hay không (bằng backtracking cẩn thận). Dưới đây minh họa bằng cách gộp từng hàng sử dụng Priority Queue (cách trực quan) mặc dù bài này có cách BS chuyên sâu.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int kthSmallest(int[][] mat, int k) {
        int[] row = mat[0];
        for (int i = 1; i < mat.length; i++) {
            row = merge(row, mat[i], k);
        }
        return row[k - 1];
    }
    
    private int[] merge(int[] row1, int[] row2, int k) {
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        for (int i = 0; i < Math.min(row1.length, k); i++) {
            pq.offer(new int[]{row1[i] + row2[0], i, 0});
        }
        
        List<Integer> res = new ArrayList<>();
        while (k > 0 && !pq.isEmpty()) {
            int[] curr = pq.poll();
            res.add(curr[0]);
            int r1Idx = curr[1], r2Idx = curr[2];
            if (r2Idx + 1 < row2.length) {
                pq.offer(new int[]{row1[r1Idx] + row2[r2Idx + 1], r1Idx, r2Idx + 1});
            }
            k--;
        }
        
        int[] ans = new int[res.size()];
        for (int i = 0; i < res.size(); i++) ans[i] = res.get(i);
        return ans;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \times k \log k)$
- **Không gian (Space):** $O(k)$

---

## 9. Matrix Median
**Đề bài chi tiết:**
Cùng bản chất với bài tập số 6. Cho một ma trận lẻ `N x M` được sắp xếp theo từng hàng. Tìm trung vị. Giả sử giá trị nằm trong đoạn `[1, 10^9]`. 

**Phân tích thuật toán:**
Áp dụng Binary Search on Value Range `[1, 10^9]`. Đếm lượng phần tử $\le mid$ ở từng hàng dùng Binary search mảng 1D `O(log M)`. Tổng thời gian một lần kiểm tra `O(N log M)`.

**Mã nguồn Java:**
```java
public class Solution {
    public int findMedian(ArrayList<ArrayList<Integer>> A) {
        int n = A.size();
        int m = A.get(0).size();
        int low = 1, high = (int) 1e9;
        int req = (n * m) / 2;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            for (int i = 0; i < n; i++) {
                count += upperBound(A.get(i), mid);
            }
            
            if (count <= req) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return low;
    }
    
    private int upperBound(ArrayList<Integer> arr, int key) {
        int l = 0, r = arr.size() - 1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (arr.get(mid) <= key) l = mid + 1;
            else r = mid - 1;
        }
        return l;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log M \log(10^9))$
- **Không gian (Space):** $O(1)$

---

## 10. K-th Smallest Prime Fraction
**Đề bài chi tiết:**
Cho mảng `arr` chứa các số nguyên tố được sắp xếp tăng dần, bắt đầu bằng 1. Xét mọi phân số `arr[i] / arr[j]` với $i < j$. Tìm phân số nhỏ thứ `k` và trả về mảng gồm 2 phần tử `[arr[i], arr[j]]`. Có thể coi nó là bài toán trên ma trận ảo giá trị phân số.

**Phân tích thuật toán:**
Dùng Binary Search on Float Value Range. Đặt `low = 0.0`, `high = 1.0`. Duyệt mảng bằng 2 con trỏ, tính số lượng phân số $\le mid$. Lưu lại phân số lớn nhất thỏa mãn $\le mid$. Nếu số lượng == k, trả về kết quả lưu trữ. Tăng giảm `low`/`high` tùy kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        double low = 0, high = 1.0;
        
        while (low < high) {
            double mid = low + (high - low) / 2;
            int count = 0;
            int num = 0, den = 1;
            int j = 1;
            
            for (int i = 0; i < n; i++) {
                while (j < n && arr[i] > mid * arr[j]) {
                    j++;
                }
                count += n - j;
                if (j < n && (num * arr[j] < den * arr[i])) {
                    num = arr[i];
                    den = arr[j];
                }
            }
            
            if (count == k) return new int[]{num, den};
            else if (count < k) low = mid;
            else high = mid;
        }
        return new int[]{};
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log(\text{Max} / \text{Epsilon}))$
- **Không gian (Space):** $O(1)$

---

## 11. Search in a Row-wise and Column-wise Sorted Matrix (Duplicates Allow)
**Đề bài chi tiết:**
Cho ma trận `m x n` trong đó mỗi hàng sắp xếp tăng dần từ trái qua phải, mỗi cột sắp xếp tăng dần từ trên xuống dưới. Ma trận có thể chứa các giá trị trùng lặp. Đếm số lần xuất hiện của `target` trong ma trận.

**Phân tích thuật toán:**
Duyệt từ góc trên cùng bên phải. Khác với bài toán tìm kiếm xem có tồn tại hay không, ở đây ta cần cộng dồn số lượng. Khi gặp phần tử `== target`, ta đếm số lượng các phần tử bằng `target` liên tiếp trên cùng hàng này từ cột hiện tại sang trái, sau đó tiến xuống hàng dưới (`row++`). Nếu phần tử $> target$, ta lùi `col--`. Nếu phần tử $< target$, ta tăng `row++`.

**Mã nguồn Java:**
```java
class Solution {
    public int countTarget(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return 0;
        int row = 0;
        int col = matrix[0].length - 1;
        int count = 0;
        
        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                // Đếm tất cả các giá trị bằng target ở phía bên trái trên cùng hàng này
                int c = col;
                while (c >= 0 && matrix[row][c] == target) {
                    count++;
                    c--;
                }
                row++; // Sau đó xuống hàng tiếp theo để tìm tiếp
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m + n)$
- **Không gian (Space):** $O(1)$

---

## 12. Kth Largest Element in a Sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `n x n` mà mỗi hàng và mỗi cột đều được sắp xếp theo thứ tự tăng dần. Tìm phần tử lớn thứ `k` trong ma trận.

**Phân tích thuật toán:**
Phần tử lớn thứ `k` trong ma trận `N x N` tương đương với phần tử nhỏ thứ `(N * N - k + 1)`. Vậy ta có thể chuyển trực tiếp về bài toán tìm phần tử nhỏ thứ `N^2 - k + 1` và áp dụng thuật toán Binary Search on Value Range (như Bài 3).

**Mã nguồn Java:**
```java
class Solution {
    public int kthLargest(int[][] matrix, int k) {
        int n = matrix.length;
        return kthSmallest(matrix, n * n - k + 1);
    }
    
    private int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int low = matrix[0][0];
        int high = matrix[n - 1][n - 1];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countLessOrEqual(matrix, mid);
            if (count < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int countLessOrEqual(int[][] matrix, int val) {
        int count = 0;
        int n = matrix.length;
        int r = n - 1, c = 0;
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= val) {
                count += r + 1;
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n \log(\text{max} - \text{min}))$
- **Không gian (Space):** $O(1)$

---

## 13. Find Common Elements in All Rows of a Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` với mỗi hàng được sắp xếp tăng dần. Tìm tất cả các phần tử xuất hiện ở mọi hàng của ma trận. Nếu không có phần tử nào chung, trả về danh sách rỗng. Các phần tử chung được liệt kê theo giá trị tăng dần, không trùng lặp.

**Phân tích thuật toán:**
Do các hàng đã sắp xếp, ta duyệt qua các phần tử không trùng lặp ở hàng đầu tiên. Đối với mỗi phần tử này, ta sử dụng Binary Search để kiểm tra xem nó có tồn tại trong tất cả các hàng còn lại hay không. Nếu tồn tại ở mọi hàng, ta thêm nó vào kết quả.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<Integer> findCommonElements(int[][] matrix) {
        List<Integer> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0) return res;
        
        int m = matrix.length;
        int n = matrix[0].length;
        
        for (int j = 0; j < n; j++) {
            // Tránh kiểm tra lại các phần tử trùng lặp trong hàng đầu tiên
            if (j > 0 && matrix[0][j] == matrix[0][j - 1]) continue;
            
            int val = matrix[0][j];
            boolean isCommon = true;
            for (int i = 1; i < m; i++) {
                if (!binarySearch(matrix[i], val)) {
                    isCommon = false;
                    break;
                }
            }
            if (isCommon) {
                res.add(val);
            }
        }
        return res;
    }
    
    private boolean binarySearch(int[] arr, int target) {
        int low = 0, high = arr.length - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] == target) return true;
            else if (arr[mid] < target) low = mid + 1;
            else high = mid - 1;
        }
        return false;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n \cdot m \log n)$
- **Không gian (Space):** $O(1)$

---

## 14. Maximum 1s in a Row in sorted binary Matrix
**Đề bài chi tiết:**
Cho ma trận nhị phân `m x n` (chỉ chứa 0 và 1). Mỗi hàng được sắp xếp tăng dần (nghĩa là các số 0 đứng trước các số 1). Tìm chỉ số của hàng chứa nhiều số 1 nhất và số lượng số 1 tương ứng.

**Phân tích thuật toán:**
Bắt đầu từ góc trên cùng bên phải `(0, n-1)`. Nếu phần tử tại đó là 1, ta dịch sang trái (`col--`) vì bên trái cũng có thể là 1, và lưu lại hàng hiện tại. Nếu phần tử là 0, ta dịch xuống dưới (`row++`) tìm hàng tiếp theo nhưng bắt đầu từ vị trí `col` đã đạt được, giúp thuật toán chỉ tìm các hàng có số lượng số 1 nhiều hơn kỷ lục trước đó.

**Mã nguồn Java:**
```java
class Solution {
    public int[] rowWithMax1s(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return new int[]{-1, 0};
        
        int m = matrix.length;
        int n = matrix[0].length;
        int maxRow = -1;
        int r = 0, c = n - 1;
        
        while (r < m && c >= 0) {
            if (matrix[r][c] == 1) {
                maxRow = r;
                c--;
            } else {
                r++;
            }
        }
        
        int maxOnes = (maxRow == -1) ? 0 : (n - 1 - c);
        return new int[]{maxRow, maxOnes};
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m + n)$
- **Không gian (Space):** $O(1)$

---

## 15. Find First occurrence of a 1 in a row-wise sorted boolean matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` gồm các phần tử boolean (hoặc 0/1) được sắp xếp theo từng hàng. Nhiệm vụ là tìm vị trí cột đầu tiên xuất hiện giá trị 1 trên toàn bộ ma trận (vị trí cột nhỏ nhất chứa số 1 trong bất kỳ hàng nào).

**Phân tích thuật toán:**
Duyệt qua tất cả các hàng, áp dụng Binary Search trên mỗi hàng để tìm index (chỉ số cột) của số 1 đầu tiên xuất hiện. Sau đó, lấy giá trị nhỏ nhất trong số các index tìm được của tất cả các hàng.

**Mã nguồn Java:**
```java
class Solution {
    public int firstOccurrenceOfOne(int[][] matrix) {
        int minCol = Integer.MAX_VALUE;
        int m = matrix.length;
        int n = matrix[0].length;
        
        for (int i = 0; i < m; i++) {
            int col = findFirstOne(matrix[i], n);
            if (col != -1 && col < minCol) {
                minCol = col;
            }
        }
        
        return minCol == Integer.MAX_VALUE ? -1 : minCol;
    }
    
    private int findFirstOne(int[] row, int n) {
        int low = 0, high = n - 1;
        int res = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (row[mid] == 1) {
                res = mid;
                high = mid - 1; // Tiếp tục tìm số 1 ở phía bên trái
            } else {
                low = mid + 1;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \log n)$
- **Không gian (Space):** $O(1)$

---

## 16. Check if Matrix is strictly sorted
**Đề bài chi tiết:**
Cho ma trận `m x n`, kiểm tra xem ma trận có được sắp xếp hoàn toàn hay không. Ma trận được sắp xếp hoàn toàn nếu mỗi hàng được sắp xếp tăng dần và phần tử đầu tiên của hàng tiếp theo lớn hơn phần tử cuối cùng của hàng hiện tại.

**Phân tích thuật toán:**
Coi ma trận như một mảng 1D dài `m * n`. Việc kiểm tra rất đơn giản: duyệt `i` từ `0` đến `m * n - 2`, so sánh phần tử thứ `i` với phần tử thứ `i+1` (dùng phép chia `/ n` và `% n` để lấy tọa độ). Nếu phần tử trước $\ge$ phần tử sau, kết luận là `false`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isStrictlySorted(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return true;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int total = m * n;
        
        for (int i = 0; i < total - 1; i++) {
            int r1 = i / n, c1 = i % n;
            int r2 = (i + 1) / n, c2 = (i + 1) % n;
            
            if (matrix[r1][c1] >= matrix[r2][c2]) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \times n)$
- **Không gian (Space):** $O(1)$

---

## 17. Count pairs with given sum in two Sorted Matrices
**Đề bài chi tiết:**
Cho hai ma trận `mat1` và `mat2` cùng kích thước `n x n` được sắp xếp tăng dần theo dạng 1D. Đếm số lượng cặp `(a, b)` trong đó `a` thuộc `mat1`, `b` thuộc `mat2` sao cho `a + b == target`.

**Phân tích thuật toán:**
Coi hai ma trận như 2 mảng 1D đã sắp xếp. Áp dụng kỹ thuật Two Pointers: con trỏ `p1` bắt đầu từ 0 (đầu `mat1`), con trỏ `p2` bắt đầu từ `N^2 - 1` (cuối `mat2`). Tính tổng `sum = mat1[p1] + mat2[p2]`. Nếu `sum == target`, tăng đếm và dời cả hai con trỏ. Nếu `< target` thì tăng `p1`, ngược lại giảm `p2`.

**Mã nguồn Java:**
```java
class Solution {
    public int countPairs(int[][] mat1, int[][] mat2, int target) {
        int n = mat1.length;
        int p1 = 0, p2 = n * n - 1;
        int count = 0;
        
        while (p1 < n * n && p2 >= 0) {
            int val1 = mat1[p1 / n][p1 % n];
            int val2 = mat2[p2 / n][p2 % n];
            int sum = val1 + val2;
            
            if (sum == target) {
                count++;
                p1++;
                p2--;
            } else if (sum < target) {
                p1++;
            } else {
                p2--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n^2)$
- **Không gian (Space):** $O(1)$

---

## 18. N-th Element in Spiral Matrix Order
**Đề bài chi tiết:**
Cho ma trận `m x n` chứa các phần tử. Một người duyệt ma trận này theo đường xoắn ốc (Spiral Order). Cho vị trí `k` (1-indexed), tìm phần tử ở vị trí thứ `k` theo thứ tự duyệt này mà không cần xây dựng toàn bộ đường đi.

**Phân tích thuật toán:**
Thay vì đi từng bước `O(M * N)`, ta mô phỏng vòng lặp duyệt xoắn ốc và cắt bớt theo các đường viền. Mỗi vòng có viền trên độ dài `n`, viền phải `m - 1`... Ta trừ dần `k` cho độ dài của cạnh đang xét. Khi `k` nhỏ hơn hoặc bằng chiều dài cạnh đó, ta có thể tính tọa độ kết quả tức thời trong $O(1)$.

**Mã nguồn Java:**
```java
class Solution {
    public int findKthElement(int[][] matrix, int k) {
        int m = matrix.length;
        int n = matrix[0].length;
        int top = 0, bottom = m - 1;
        int left = 0, right = n - 1;
        
        while (top <= bottom && left <= right) {
            // Duyệt từ trái sang phải
            if (k <= right - left + 1) return matrix[top][left + k - 1];
            k -= (right - left + 1);
            top++;
            
            // Duyệt từ trên xuống dưới
            if (k <= bottom - top + 1) return matrix[top + k - 1][right];
            k -= (bottom - top + 1);
            right--;
            
            if (top <= bottom) {
                // Duyệt từ phải sang trái
                if (k <= right - left + 1) return matrix[bottom][right - k + 1];
                k -= (right - left + 1);
                bottom--;
            }
            
            if (left <= right) {
                // Duyệt từ dưới lên trên
                if (k <= bottom - top + 1) return matrix[bottom - k + 1][left];
                k -= (bottom - top + 1);
                left++;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(\min(m, n))$
- **Không gian (Space):** $O(1)$

---

## 19. Median in Matrix with Both Row and Col sorted
**Đề bài chi tiết:**
Cho một ma trận lẻ `n x n` mà cả hàng và cột đều được sắp xếp theo chiều tăng dần. Tìm phần tử trung vị của ma trận (phần tử đứng giữa sau khi sắp xếp toàn bộ ma trận).

**Phân tích thuật toán:**
Trung vị là phần tử nhỏ thứ `k = (n * n) / 2 + 1`. Tương tự bài toán Kth Smallest Element, ta dùng Binary Search trên khoảng giá trị (`low = matrix[0][0]`, `high = matrix[n-1][n-1]`) và hàm đếm số phần tử $\le mid$ có thể chạy trong $O(n)$ từ góc dưới cùng bên trái lên trên cùng bên phải.

**Mã nguồn Java:**
```java
class Solution {
    public int medianSortedMatrix(int[][] matrix) {
        int n = matrix.length;
        int low = matrix[0][0];
        int high = matrix[n - 1][n - 1];
        int req = (n * n) / 2 + 1;
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countLessOrEqual(matrix, mid);
            if (count < req) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int countLessOrEqual(int[][] matrix, int val) {
        int count = 0;
        int n = matrix.length;
        int r = n - 1;
        int c = 0;
        while (r >= 0 && c < n) {
            if (matrix[r][c] <= val) {
                count += r + 1;
                c++;
            } else {
                r--;
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n \log(\text{max} - \text{min}))$
- **Không gian (Space):** $O(1)$

---

## 20. Count Negative Numbers (Variant - Row only)
**Đề bài chi tiết:**
Cho ma trận `m x n`. Mỗi hàng của ma trận được sắp xếp theo chiều giảm dần từ trái sang phải, nhưng không có tính chất liên kết giữa các hàng. Đếm tổng số lượng số âm trong toàn bộ ma trận.

**Phân tích thuật toán:**
Vì chỉ có tính chất sắp xếp ở từng hàng, ta áp dụng Binary Search độc lập cho từng hàng. Với mỗi hàng, ta tìm chỉ số đầu tiên mà tại đó giá trị là số âm. Do mảng giảm dần, mọi phần tử đằng sau nó đều âm. Số lượng số âm của hàng đó sẽ là `n - index`.

**Mã nguồn Java:**
```java
class Solution {
    public int countNegatives(int[][] matrix) {
        int count = 0;
        int n = matrix[0].length;
        
        for (int[] row : matrix) {
            int idx = firstNegativeIndex(row);
            if (idx != -1) {
                count += (n - idx);
            }
        }
        return count;
    }
    
    private int firstNegativeIndex(int[] arr) {
        int low = 0, high = arr.length - 1;
        int res = -1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] < 0) {
                res = mid;
                high = mid - 1; // Cố gắng tìm số âm đầu tiên về phía trái
            } else {
                low = mid + 1; // Số hiện tại >= 0, số âm ở phía bên phải
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \log n)$
- **Không gian (Space):** $O(1)$

---

## 21. Smallest Element strictly greater than Target in 2D Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` được sắp xếp tăng dần theo kiểu 1D (hàng sau nối tiếp hàng trước). Tìm phần tử nhỏ nhất trong ma trận có giá trị lớn hơn hẳn `target` (tương đương hàm `upper_bound`).

**Phân tích thuật toán:**
Do ma trận được sắp xếp kiểu 1D, ta dùng Binary Search trên các chỉ số từ `0` đến `m * n - 1`. Trong quá trình tìm kiếm, nếu phần tử tại `mid` $\le target$, ta tìm bên phải (`low = mid + 1`). Nếu phần tử $> target$, đây có thể là đáp án, lưu lại giá trị và tiếp tục tìm bên trái (`high = mid - 1`) để có giá trị nhỏ nhất có thể.

**Mã nguồn Java:**
```java
class Solution {
    public int upperBound(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return -1;
        int m = matrix.length;
        int n = matrix[0].length;
        int low = 0, high = m * n - 1;
        int res = -1; // Hoặc một giá trị vô cực tùy đề bài
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int val = matrix[mid / n][mid % n];
            
            if (val > target) {
                res = val;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(\log(m \times n))$
- **Không gian (Space):** $O(1)$

---

## 22. Kth Smallest Difference in a Matrix
**Đề bài chi tiết:**
Cho một ma trận `m x n`. Xét tất cả các cặp phần tử `(a, b)` trong ma trận (với $a, b$ là 2 phần tử bất kỳ). Tìm giá trị chênh lệch tuyệt đối $|a - b|$ nhỏ thứ `k` trong tất cả các cặp.

**Phân tích thuật toán:**
Đầu tiên ta trải phẳng (flatten) ma trận thành một mảng 1D gồm $M \times N$ phần tử và sắp xếp mảng đó. Sau đó, bài toán trở thành tìm khoảng cách nhỏ thứ `k` trong một mảng đã sắp xếp. Ta sử dụng Binary Search trên khoảng giá trị của sự chênh lệch: `low = 0`, `high = arr[last] - arr[0]`. Với mỗi giá trị `mid`, dùng Two Pointers (hoặc Sliding Window) để đếm số cặp có hiệu $\le mid$.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int kthSmallestDifference(int[][] matrix, int k) {
        int m = matrix.length;
        int n = matrix[0].length;
        int[] arr = new int[m * n];
        int idx = 0;
        
        for (int[] row : matrix) {
            for (int val : row) {
                arr[idx++] = val;
            }
        }
        Arrays.sort(arr);
        
        int low = 0;
        int high = arr[arr.length - 1] - arr[0];
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (countPairs(arr, mid) < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int countPairs(int[] arr, int mid) {
        int count = 0, left = 0;
        for (int right = 0; right < arr.length; right++) {
            while (arr[right] - arr[left] > mid) {
                left++;
            }
            count += right - left;
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(K \log K + K \log(\text{max\_diff}))$ với $K = m \times n$
- **Không gian (Space):** $O(m \times n)$

---

## 23. Peak Element in Grid (Multiple Peaks)
**Đề bài chi tiết:**
Cho ma trận `m x n` không có 2 phần tử liền kề bằng nhau. Một "đỉnh" (peak) là phần tử lớn hơn nghiêm ngặt cả 4 phần tử xung quanh (trên, dưới, trái, phải). Tìm tất cả các phần tử đỉnh trong ma trận.

**Phân tích thuật toán:**
Khác với bài 5 chỉ yêu cầu tìm một đỉnh bất kỳ bằng Binary Search, để tìm TẤT CẢ các đỉnh, ta không thể bỏ qua các nhánh của Binary Search vì đỉnh có thể nằm ở bất kỳ đâu. Thuật toán phù hợp nhất để liệt kê toàn bộ các đỉnh là duyệt qua mọi phần tử, và kiểm tra 4 hướng.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public List<int[]> findAllPeaks(int[][] mat) {
        List<int[]> res = new ArrayList<>();
        int m = mat.length;
        int n = mat[0].length;
        int[] dirs = {-1, 0, 1, 0, -1};
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                boolean isPeak = true;
                for (int d = 0; d < 4; d++) {
                    int r = i + dirs[d];
                    int c = j + dirs[d + 1];
                    if (r >= 0 && r < m && c >= 0 && c < n) {
                        if (mat[i][j] <= mat[r][c]) {
                            isPeak = false;
                            break;
                        }
                    }
                }
                if (isPeak) {
                    res.add(new int[]{i, j});
                }
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \times n)$
- **Không gian (Space):** $O(1)$ (Không tính không gian lưu trữ kết quả)

---

## 24. Find the Row with Maximum Number of 1s (Optimal)
**Đề bài chi tiết:**
Tương tự Bài 14: Cho ma trận nhị phân `m x n`, trong đó các hàng đã được sắp xếp (0 đứng trước 1). Tìm chỉ số của hàng có nhiều số 1 nhất.

**Phân tích thuật toán:**
Bài toán này nhấn mạnh phương pháp giải tối ưu $O(M + N)$ tốt hơn $O(M \log N)$. Thuật toán dùng biến con trỏ bắt đầu ở góc trên cùng bên phải `(0, n-1)`. Nếu phần tử tại đó là 1, ta dịch trái; nếu là 0, ta dịch xuống hàng kế tiếp nhưng giữ nguyên cột. Điều này đảm bảo ta chỉ cập nhật khi tìm được hàng có nhiều số 1 hơn kỷ lục hiện tại.

**Mã nguồn Java:**
```java
class Solution {
    public int rowWithMax1s(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return -1;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int maxRow = -1;
        int r = 0, c = n - 1;
        
        while (r < m && c >= 0) {
            if (matrix[r][c] == 1) {
                maxRow = r;
                c--; // Tiếp tục dò sang trái
            } else {
                r++; // Tìm xuống hàng dưới nhưng giữ nguyên cột
            }
        }
        
        return maxRow;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m + n)$
- **Không gian (Space):** $O(1)$

---

## 25. Sorted Matrix to BST
**Đề bài chi tiết:**
Cho một ma trận `m x n` được sắp xếp theo dạng 1D. Hãy chuyển đổi ma trận này thành một Cây tìm kiếm nhị phân cân bằng (Balanced BST).

**Phân tích thuật toán:**
Tương tự như việc chuyển mảng đã sắp xếp thành BST, ta chọn phần tử ở giữa đoạn làm gốc (root). Sau đó đệ quy nửa trái để làm cây con trái, nửa phải làm cây con phải. Việc tính toán chỉ số trên không gian 1D sẽ được ánh xạ ngược về tọa độ 2D bằng công thức `row = index / n` và `col = index % n`.

**Mã nguồn Java:**
```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

class Solution {
    public TreeNode sortedMatrixToBST(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return null;
        int m = matrix.length;
        int n = matrix[0].length;
        return buildBST(matrix, 0, m * n - 1, n);
    }
    
    private TreeNode buildBST(int[][] matrix, int left, int right, int n) {
        if (left > right) return null;
        
        int mid = left + (right - left) / 2;
        int r = mid / n;
        int c = mid % n;
        
        TreeNode root = new TreeNode(matrix[r][c]);
        root.left = buildBST(matrix, left, mid - 1, n);
        root.right = buildBST(matrix, mid + 1, right, n);
        
        return root;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(m \times n)$
- **Không gian (Space):** $O(\log(m \times n))$ (chiều sâu của call stack đệ quy)

---

## 26. Kth Minimum Element in Column-wise Sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` mà mỗi cột được sắp xếp tăng dần (hàng không được sắp xếp). Tìm phần tử nhỏ thứ `k` trong toàn bộ ma trận.

**Phân tích thuật toán:**
Dùng Binary Search trên Value Range: Tìm `min` và `max` của toàn ma trận. Thử `mid`, đếm số phần tử $\le mid$ trên từng cột bằng `upper_bound`. Do mỗi cột đã được sắp xếp, `upper_bound` chạy mất $O(\log m)$ mỗi cột. Tổng thời gian cho 1 bước tìm kiếm là $O(n \log m)$.

**Mã nguồn Java:**
```java
class Solution {
    public int kthSmallestColumnWise(int[][] matrix, int k) {
        int m = matrix.length;
        int n = matrix[0].length;
        int low = Integer.MAX_VALUE, high = Integer.MIN_VALUE;
        
        for (int j = 0; j < n; j++) {
            low = Math.min(low, matrix[0][j]);
            high = Math.max(high, matrix[m - 1][j]);
        }
        
        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            for (int j = 0; j < n; j++) {
                count += upperBoundColumn(matrix, j, mid);
            }
            if (count < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
    
    private int upperBoundColumn(int[][] matrix, int col, int target) {
        int low = 0, high = matrix.length - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[mid][col] <= target) low = mid + 1;
            else high = mid - 1;
        }
        return low;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(n \log m \log(\text{max} - \text{min}))$
- **Không gian (Space):** $O(1)$

---

## 27. Number of Elements in Range [A, B] in Sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` được sắp xếp theo dạng 1D. Trả về số lượng phần tử có giá trị nằm trong đoạn `[A, B]`.

**Phân tích thuật toán:**
Do ma trận được xem như mảng 1D đã sắp xếp, số lượng phần tử trong đoạn `[A, B]` chính bằng chỉ số của hàm `upper_bound(B)` trừ đi chỉ số của `lower_bound(A)`. Áp dụng 2 lần Binary Search trên không gian 1D ảo.

**Mã nguồn Java:**
```java
class Solution {
    public int countElementsInRange(int[][] matrix, int A, int B) {
        int m = matrix.length;
        int n = matrix[0].length;
        int len = m * n;
        
        int lower = lowerBound(matrix, len, n, A);
        int upper = upperBound(matrix, len, n, B);
        
        return upper - lower;
    }
    
    private int lowerBound(int[][] matrix, int len, int n, int target) {
        int low = 0, high = len - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[mid / n][mid % n] < target) low = mid + 1;
            else high = mid - 1;
        }
        return low;
    }
    
    private int upperBound(int[][] matrix, int len, int n, int target) {
        int low = 0, high = len - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[mid / n][mid % n] <= target) low = mid + 1;
            else high = mid - 1;
        }
        return low;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(\log(m \times n))$
- **Không gian (Space):** $O(1)$

---

## 28. Nearest Element to Target in sorted Matrix
**Đề bài chi tiết:**
Cho ma trận `m x n` được sắp xếp theo dạng 1D. Cho một số `target` (không nhất thiết nằm trong ma trận). Tìm phần tử trong ma trận có giá trị gần nhất với `target` (tức là độ lệch $|val - target|$ nhỏ nhất).

**Phân tích thuật toán:**
Dùng Binary Search trên không gian 1D ảo để tìm `lower_bound` của `target`. Gọi chỉ số tìm được là `idx`. Phần tử gần nhất chỉ có thể là `arr[idx]` (phần tử đầu tiên $\ge target$) hoặc `arr[idx - 1]` (phần tử cuối cùng $< target$). So sánh chênh lệch giữa hai giá trị này và trả về phần tử có độ chênh lệch nhỏ hơn.

**Mã nguồn Java:**
```java
class Solution {
    public int findNearestElement(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        int len = m * n;
        
        int low = 0, high = len - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[mid / n][mid % n] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        
        // low chính là chỉ số lower_bound
        if (low == 0) return matrix[0][0];
        if (low == len) return matrix[(len - 1) / n][(len - 1) % n];
        
        int val1 = matrix[low / n][low % n];
        int val2 = matrix[(low - 1) / n][(low - 1) % n];
        
        if (Math.abs(val1 - target) < Math.abs(val2 - target)) {
            return val1;
        } else {
            return val2;
        }
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(\log(m \times n))$
- **Không gian (Space):** $O(1)$

---

## 29. Submatrix Sum K in Sorted Matrix
**Đề bài chi tiết:**
Cho một ma trận `m x n` chứa toàn số nguyên dương đã được sắp xếp tăng dần trên cả hàng và cột. Tìm số lượng các ma trận con (submatrix) có tổng bằng đúng `target`.

**Phân tích thuật toán:**
Cố định cận trên `r1` và cận dưới `r2` của hàng. Bài toán trở thành tìm khoảng cột `[c1, c2]` sao cho tổng mảng 1D bằng `target`. Dùng mảng Prefix Sum 2D để lấy tổng từng cột nhanh chóng. Vì ma trận chứa toàn số dương, mảng Prefix Sum dọc theo các cột sẽ tăng dần, cho phép ta dùng Sliding Window (Two Pointers) để tìm khoảng `[c1, c2]` thỏa mãn thay vì dùng HashMap.

**Mã nguồn Java:**
```java
class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int count = 0;
        
        // Tính Prefix Sum theo từng hàng để tối ưu O(1) truy vấn tổng của hàng trong khoảng [c1, c2]
        for (int i = 0; i < m; i++) {
            for (int j = 1; j < n; j++) {
                matrix[i][j] += matrix[i][j - 1];
            }
        }
        
        // Cố định 2 cột c1 và c2
        for (int c1 = 0; c1 < n; c1++) {
            for (int c2 = c1; c2 < n; c2++) {
                int sum = 0;
                int leftRow = 0;
                
                // Sliding window trên các hàng
                for (int rightRow = 0; rightRow < m; rightRow++) {
                    int rowSum = matrix[rightRow][c2] - (c1 > 0 ? matrix[rightRow][c1 - 1] : 0);
                    sum += rowSum;
                    
                    while (sum > target && leftRow <= rightRow) {
                        int leftRowSum = matrix[leftRow][c2] - (c1 > 0 ? matrix[leftRow][c1 - 1] : 0);
                        sum -= leftRowSum;
                        leftRow++;
                    }
                    if (sum == target) {
                        count++;
                    }
                }
            }
        }
        return count;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** $O(N^2 \times M)$
- **Không gian (Space):** $O(1)$ (tận dụng ma trận đầu vào để tính Prefix Sum)

---

## 30. Matrix Fractional Cascading
**Đề bài chi tiết:**
(Kỹ thuật nâng cao) Giả sử ta cần tìm kiếm một giá trị `target` trên nhiều hàng của ma trận `m x n` được sắp xếp (truy vấn lặp lại nhiều lần). Mô tả phương pháp và cấu trúc dữ liệu mô phỏng Fractional Cascading giúp giảm độ phức tạp truy vấn từ $O(M \log N)$ xuống $O(\log N + M)$.
(Lưu ý: Yêu cầu cài đặt cấu trúc Cascading đơn giản gồm các con trỏ chuyển tiếp).

**Phân tích thuật toán:**
Fractional Cascading kết hợp mảng hiện tại với một phần (thường là một nửa) của mảng bên dưới nó để tăng tốc tìm kiếm. Ta tạo một mảng bổ sung cho mỗi hàng. Mỗi phần tử trong mảng mới lưu giá trị và hai con trỏ: một trỏ tới vị trí của nó ở mảng nguyên bản, một trỏ tới vị trí tương ứng ở hàng tiếp theo. Bằng cách này, sau khi tốn $O(\log N)$ để tìm ở hàng đầu tiên, ta chỉ tốn $O(1)$ truy xuất con trỏ để tìm ở hàng thứ hai, thứ ba... dẫn đến tổng thời gian truy vấn là $O(\log N + M)$.

**Mã nguồn Java:**
```java
// Cấu trúc minh họa ý tưởng Fractional Cascading cơ bản
class Node {
    int val;
    int currIdx;  // Index ở mảng thật của hàng hiện tại
    int nextRowIdx; // Index tại mảng augmented của hàng dưới
    Node(int v, int c, int n) { 
        val = v; 
        currIdx = c; 
        nextRowIdx = n; 
    }
}

class Solution {
    // Để giữ độ dài mã nguồn hợp lý, ta giả lập hàm sử dụng Fractional Cascading
    // Quá trình build Cascading List mất O(M * N), nhưng sau đó truy vấn mất O(log N + M)
    public int[] searchCascading(Node[][] augmentedMatrix, int[][] matrix, int target) {
        int m = matrix.length;
        if (m == 0) return new int[0];
        
        int[] res = new int[m]; // Lưu index tìm được ở mỗi hàng
        
        // Bước 1: Binary Search trên hàng đầu tiên O(log N)
        int pos = binarySearch(augmentedMatrix[0], target);
        
        // Bước 2: Đi theo con trỏ chuyển tiếp với O(1) mỗi hàng
        for (int i = 0; i < m; i++) {
            if (pos < augmentedMatrix[i].length) {
                res[i] = augmentedMatrix[i][pos].currIdx;
                pos = augmentedMatrix[i][pos].nextRowIdx; // Dịch chuyển tới hàng dưới
            } else {
                res[i] = -1; // Vượt quá giới hạn (Out of bounds)
                pos = augmentedMatrix[i].length > 0 ? augmentedMatrix[i][augmentedMatrix[i].length - 1].nextRowIdx : 0;
            }
        }
        return res;
    }
    
    private int binarySearch(Node[] arr, int target) {
        int low = 0, high = arr.length - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (arr[mid].val < target) low = mid + 1;
            else high = mid - 1;
        }
        return low;
    }
}
```
**Độ phức tạp:**
- **Thời gian (Time):** Khởi tạo $O(M \times N)$, mỗi truy vấn mất $O(\log N + M)$
- **Không gian (Space):** $O(M \times N)$ cho các mảng phụ.
