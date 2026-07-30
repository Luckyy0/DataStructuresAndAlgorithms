# Bài tập Chia để trị (Divide and Conquer) - Cơ bản

Tài liệu này bao gồm 30 bài tập ứng dụng kỹ thuật Chia để trị (Divide and Conquer). 10 bài đầu tiên được trình bày chi tiết bao gồm đề bài, phân tích, mã nguồn Java và độ phức tạp. 20 bài còn lại được tóm tắt ý tưởng.

---

## 1. Maximum Subarray (LeetCode 53)

**Đề bài chi tiết:**
Cho một mảng số nguyên `nums`, hãy tìm mảng con liên tiếp (chứa ít nhất một phần tử) có tổng lớn nhất và trả về tổng đó. (Yêu cầu giải bằng phương pháp Chia để trị).

**Phân tích thuật toán (D&C approach):**
- **Divide**: Chia mảng thành 2 nửa: trái (`left`) và phải (`right`).
- **Conquer**: Đệ quy tìm tổng lớn nhất trong nửa trái và nửa phải.
- **Combine**: Tổng lớn nhất có thể nằm ở nửa trái, nửa phải, hoặc **bắt chéo qua điểm giữa** (crossing middle). Ta tính tổng lớn nhất bắt chéo qua giữa và so sánh với 2 tổng kia để lấy max.

**Mã nguồn Java:**
```java
public class MaximumSubarray {
    public int maxSubArray(int[] nums) {
        return findMaxSubArray(nums, 0, nums.length - 1);
    }

    private int findMaxSubArray(int[] nums, int left, int right) {
        if (left == right) {
            return nums[left];
        }
        int mid = left + (right - left) / 2;
        int leftMax = findMaxSubArray(nums, left, mid);
        int rightMax = findMaxSubArray(nums, mid + 1, right);
        int crossMax = findMaxCrossingSubArray(nums, left, mid, right);
        
        return Math.max(Math.max(leftMax, rightMax), crossMax);
    }

    private int findMaxCrossingSubArray(int[] nums, int left, int mid, int right) {
        int leftSum = Integer.MIN_VALUE;
        int sum = 0;
        for (int i = mid; i >= left; i--) {
            sum += nums[i];
            if (sum > leftSum) leftSum = sum;
        }

        int rightSum = Integer.MIN_VALUE;
        sum = 0;
        for (int i = mid + 1; i <= right; i++) {
            sum += nums[i];
            if (sum > rightSum) rightSum = sum;
        }

        return leftSum + rightSum;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$. Mỗi tầng của đệ quy chia đôi mảng và vòng lặp tính crossing max tốn $O(N)$ công sức. Áp dụng Master Theorem: $T(N) = 2T(N/2) + O(N) \implies O(N \log N)$.
- **Không gian (Space):** $O(\log N)$ do call stack của đệ quy.

---

## 2. Majority Element (LeetCode 169)

**Đề bài chi tiết:**
Cho mảng `nums` kích thước `n`, trả về phần tử đa số (majority element). Phần tử đa số là phần tử xuất hiện nhiều hơn $\lfloor n / 2 \rfloor$ lần. Giả định luôn tồn tại phần tử đa số trong mảng. (Giải bằng D&C).

**Phân tích thuật toán:**
- Nếu chia mảng thành 2 nửa, phần tử đa số của toàn bộ mảng ít nhất phải là phần tử đa số của nửa trái hoặc nửa phải.
- Trị (Conquer): Đệ quy tìm phần tử đa số của 2 nửa.
- Kết hợp (Combine): Nếu 2 nửa trả về cùng 1 phần tử, trả về phần tử đó. Nếu khác, đếm số lần xuất hiện của cả 2 phần tử trong mảng hiện tại và trả về phần tử có số lượng đếm nhiều hơn.

**Mã nguồn Java:**
```java
public class MajorityElement {
    public int majorityElement(int[] nums) {
        return majorityElementRec(nums, 0, nums.length - 1);
    }

    private int majorityElementRec(int[] nums, int left, int right) {
        if (left == right) {
            return nums[left];
        }

        int mid = left + (right - left) / 2;
        int leftMajority = majorityElementRec(nums, left, mid);
        int rightMajority = majorityElementRec(nums, mid + 1, right);

        if (leftMajority == rightMajority) {
            return leftMajority;
        }

        int leftCount = countInRange(nums, leftMajority, left, right);
        int rightCount = countInRange(nums, rightMajority, left, right);

        return leftCount > rightCount ? leftMajority : rightMajority;
    }

    private int countInRange(int[] nums, int num, int left, int right) {
        int count = 0;
        for (int i = left; i <= right; i++) {
            if (nums[i] == num) {
                count++;
            }
        }
        return count;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$. Tương tự Merge Sort, phương trình là $T(N) = 2T(N/2) + O(N)$.
- **Không gian (Space):** $O(\log N)$ (độ sâu cây đệ quy).

---

## 3. Pow(x, n) (LeetCode 50)

**Đề bài chi tiết:**
Cài đặt hàm `pow(x, n)`, tính $x$ lũy thừa $n$ ($x^n$).

**Phân tích thuật toán:**
- Thay vì nhân $x$ lên $n$ lần (Time O(n)), ta có thể dùng Chia để trị: $x^n = x^{n/2} \times x^{n/2}$.
- Nếu $n$ lẻ: $x^n = x^{n/2} \times x^{n/2} \times x$.
- Lưu ý xử lý trường hợp $n$ âm và tránh tràn số nguyên (integer overflow) khi $n = -2^{31}$.

**Mã nguồn Java:**
```java
public class PowXN {
    public double myPow(double x, int n) {
        long N = n; // Dùng long để tránh tràn số khi n = Integer.MIN_VALUE
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }
        return fastPow(x, N);
    }

    private double fastPow(double x, long n) {
        if (n == 0) {
            return 1.0;
        }
        double half = fastPow(x, n / 2);
        if (n % 2 == 0) {
            return half * half;
        } else {
            return half * half * x;
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log n)$. Bài toán kích thước $n$ được chia nhỏ còn $n/2$ mỗi lần.
- **Không gian (Space):** $O(\log n)$ (Call stack).

---

## 4. Longest Common Prefix (LeetCode 14)

**Đề bài chi tiết:**
Viết hàm để tìm tiền tố chung dài nhất trong một mảng chuỗi. Nếu không có, trả về chuỗi rỗng `""`. (Áp dụng giải thuật D&C).

**Phân tích thuật toán:**
- D&C: Tiền tố chung dài nhất (LCP) của mảng $LCP(S_1 \dots S_n) = LCP(LCP(S_1 \dots S_k), LCP(S_{k+1} \dots S_n))$.
- Chia mảng thành hai nửa, đệ quy tìm LCP cho nửa trái và nửa phải, sau đó kết hợp tìm LCP của 2 kết quả trả về.

**Mã nguồn Java:**
```java
public class LongestCommonPrefix {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        return longestCommonPrefixRec(strs, 0, strs.length - 1);
    }

    private String longestCommonPrefixRec(String[] strs, int left, int right) {
        if (left == right) {
            return strs[left];
        } else {
            int mid = left + (right - left) / 2;
            String lcpLeft = longestCommonPrefixRec(strs, left, mid);
            String lcpRight = longestCommonPrefixRec(strs, mid + 1, right);
            return commonPrefix(lcpLeft, lcpRight);
        }
    }

    private String commonPrefix(String left, String right) {
        int minLength = Math.min(left.length(), right.length());
        for (int i = 0; i < minLength; i++) {
            if (left.charAt(i) != right.charAt(i)) {
                return left.substring(0, i);
            }
        }
        return left.substring(0, minLength);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(S)$, trong đó $S$ là tổng số ký tự của tất cả chuỗi. Master Theorem: $T(N) = 2T(N/2) + O(M)$ (M là độ dài chuỗi). Tổng các bước kết hợp là tối đa $O(S)$.
- **Không gian (Space):** $O(M \cdot \log N)$ (Với $M$ là chiều dài lớn nhất của chuỗi, độ sâu cây là $\log N$).

---

## 5. Find Peak Element (LeetCode 162)

**Đề bài chi tiết:**
Phần tử "đỉnh" (peak) là phần tử lớn hơn các hàng xóm của nó. Cho mảng số nguyên, hãy tìm một phần tử đỉnh và trả về chỉ số của nó. Giả sử `nums[-1] = nums[n] = -\infty`. Yêu cầu thuật toán $O(\log N)$.

**Phân tích thuật toán (D&C / Binary Search perspective):**
- Lấy phần tử giữa `mid`.
- Nếu `nums[mid] > nums[mid + 1]`, mảng đang đi xuống, do đó chắc chắn có một đỉnh nằm ở nửa trái (bao gồm cả `mid`).
- Nếu `nums[mid] < nums[mid + 1]`, mảng đang đi lên, chắc chắn có đỉnh ở nửa phải.

**Mã nguồn Java:**
```java
public class FindPeakElement {
    public int findPeakElement(int[] nums) {
        return searchPeak(nums, 0, nums.length - 1);
    }
    
    private int searchPeak(int[] nums, int left, int right) {
        if (left == right) {
            return left;
        }
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[mid + 1]) {
            // Đỉnh ở nửa trái (có thể là chính mid)
            return searchPeak(nums, left, mid);
        } else {
            // Đỉnh ở nửa phải
            return searchPeak(nums, mid + 1, right);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log N)$, chỉ đi theo 1 nhánh đệ quy $T(N) = T(N/2) + O(1)$.
- **Không gian (Space):** $O(\log N)$ (chi phí Stack, có thể tối ưu thành $O(1)$ bằng Iterative).

---

## 6. Search a 2D Matrix II (LeetCode 240)

**Đề bài chi tiết:**
Viết một hàm tìm kiếm một giá trị `target` trong ma trận `m x n`. Ma trận có các tính chất:
1. Các số nguyên trong mỗi hàng được sắp xếp theo thứ tự tăng dần từ trái sang phải.
2. Các số nguyên trong mỗi cột được sắp xếp theo thứ tự tăng dần từ trên xuống dưới.

**Phân tích thuật toán (D&C perspective):**
- Có thể chia ma trận thành 4 phần tư (quadrants). 
- Tuy nhiên, cách tối ưu nhất (giống với D&C qua việc loại bỏ search space) là bắt đầu từ góc trên bên phải. 
- Nếu phần tử hiện tại `> target`, ta loại bỏ toàn bộ cột đó (di chuyển sang trái).
- Nếu phần tử hiện tại `< target`, ta loại bỏ toàn bộ hàng đó (di chuyển xuống dưới).

**Mã nguồn Java:**
```java
public class Search2DMatrixII {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        // Bắt đầu từ góc trên bên phải
        int row = 0;
        int col = cols - 1;
        
        while (row < rows && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--; // Bỏ qua cột này
            } else {
                row++; // Bỏ qua hàng này
            }
        }
        
        return false;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(M + N)$. Tại mỗi bước thu hẹp không gian bài toán xuống còn ma trận $(M-1 \times N)$ hoặc $(M \times N-1)$.
- **Không gian (Space):** $O(1)$. Không dùng đệ quy hoặc bộ nhớ phụ.

---

## 7. Fibonacci Number (Matrix Exponentiation) (LeetCode 509)

**Đề bài chi tiết:**
Số Fibonacci được xác định bởi công thức: $F(0) = 0, F(1) = 1$, và $F(n) = F(n-1) + F(n-2)$. Hãy tính $F(n)$ trong độ phức tạp thời gian $O(\log N)$.

**Phân tích thuật toán:**
- Việc dùng đệ quy thông thường tốn $O(2^N)$. DP tốn $O(N)$.
- Sử dụng ma trận (Matrix Exponentiation) với kỹ thuật Divide and Conquer (như hàm `Pow(x, n)`):
$\begin{pmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n$

**Mã nguồn Java:**
```java
public class FibonacciNumber {
    public int fib(int n) {
        if (n == 0) return 0;
        int[][] M = new int[][]{{1, 1}, {1, 0}};
        power(M, n - 1);
        return M[0][0];
    }
    
    private void power(int[][] matrix, int n) {
        if (n == 0 || n == 1) return;
        int[][] M = new int[][]{{1, 1}, {1, 0}};
        
        power(matrix, n / 2);
        multiply(matrix, matrix);
        
        if (n % 2 != 0) {
            multiply(matrix, M);
        }
    }
    
    private void multiply(int[][] F, int[][] M) {
        int x = F[0][0] * M[0][0] + F[0][1] * M[1][0];
        int y = F[0][0] * M[0][1] + F[0][1] * M[1][1];
        int z = F[1][0] * M[0][0] + F[1][1] * M[1][0];
        int w = F[1][0] * M[0][1] + F[1][1] * M[1][1];
        
        F[0][0] = x;
        F[0][1] = y;
        F[1][0] = z;
        F[1][1] = w;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log n)$. Thuật toán tính luỹ thừa ma trận dựa trên D&C.
- **Không gian (Space):** $O(\log n)$ (do gọi đệ quy `power`).

---

## 8. Maximum Depth of Binary Tree (LeetCode 104)

**Đề bài chi tiết:**
Cho gốc của một cây nhị phân (binary tree), hãy tìm chiều sâu tối đa của nó. 

**Phân tích thuật toán (D&C):**
- Cây nhị phân là cấu trúc đệ quy (Recursive data structure).
- Divide: Tìm chiều sâu lớn nhất của cây con bên trái và cây con bên phải độc lập.
- Combine: Lấy Max của hai kết quả cộng thêm 1 (cho node hiện tại).
- Base case: Nếu cây rỗng (node `null`), chiều sâu = 0.

**Mã nguồn Java:**
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class MaximumDepthBinaryTree {
    public int maxDepth(TreeNode root) {
        // Base case
        if (root == null) {
            return 0;
        }
        
        // Divide and Conquer
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        
        // Combine
        return Math.max(leftDepth, rightDepth) + 1;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$, với $N$ là số lượng nodes. Mỗi node được duyệt qua đúng 1 lần.
- **Không gian (Space):** $O(H)$ với $H$ là chiều cao cây (O(log N) cho cây cân bằng, O(N) cho cây lệch).

---

## 9. Validate Binary Search Tree (LeetCode 98)

**Đề bài chi tiết:**
Cho gốc của một cây nhị phân, kiểm tra xem nó có phải là một Cây tìm kiếm nhị phân (BST) hợp lệ hay không.

**Phân tích thuật toán (D&C):**
- Mỗi cây con trong BST cũng phải là một BST. Hơn nữa, tất cả giá trị node bên trái phải `< node.val` và bên phải phải `> node.val`.
- Divide & Conquer: Kiểm tra xem cây bên trái có phải là BST trong khoảng `(min, node.val)` và cây bên phải có phải là BST trong khoảng `(node.val, max)` không.

**Mã nguồn Java:**
```java
public class ValidateBST {
    public boolean isValidBST(TreeNode root) {
        return isValidBSTRec(root, null, null);
    }
    
    private boolean isValidBSTRec(TreeNode node, Integer lower, Integer upper) {
        if (node == null) return true; // Base case
        
        int val = node.val;
        if (lower != null && val <= lower) return false;
        if (upper != null && val >= upper) return false;
        
        // Divide and Combine: Cả 2 cây con đều phải hợp lệ
        if (!isValidBSTRec(node.right, val, upper)) return false;
        if (!isValidBSTRec(node.left, lower, val)) return false;
        
        return true;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ thăm tất cả các nodes đúng 1 lần.
- **Không gian (Space):** $O(N)$ trong trường hợp tồi tệ nhất do Call Stack (cây bị lệch thẳng).

---

## 10. Sqrt(x) (LeetCode 69)

**Đề bài chi tiết:**
Cho số nguyên không âm `x`, tính và trả về căn bậc hai của `x` (làm tròn xuống số nguyên gần nhất).

**Phân tích thuật toán:**
- D&C / Binary Search: Không gian tìm kiếm nằm từ $1$ đến $x$.
- Lấy `mid`, tính `mid * mid`. Nếu bằng `x`, trả về `mid`.
- Nếu `mid * mid > x`, thu hẹp về nửa trái. Ngược lại, thu hẹp về nửa phải và lưu `mid` làm giá trị tạm.

**Mã nguồn Java:**
```java
public class SqrtX {
    public int mySqrt(int x) {
        if (x == 0) return 0;
        int left = 1, right = x, ans = 0;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            // Dùng chia để tránh tràn số: mid * mid <= x <=> mid <= x / mid
            if (mid <= x / mid) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log x)$. Không gian tìm kiếm bị chia đôi qua từng bước.
- **Không gian (Space):** $O(1)$.

---

## 11. Merge Sort Algorithm

**Đề bài chi tiết:**
Cài đặt thuật toán sắp xếp trộn (Merge Sort) để sắp xếp một mảng số nguyên theo thứ tự tăng dần. (Yêu cầu áp dụng phương pháp Chia để trị - Divide and Conquer).

**Phân tích thuật toán:**
- **Divide**: Chia mảng hiện tại thành 2 nửa bằng nhau (hoặc chênh lệch 1 phần tử) cho đến khi mỗi nửa chỉ còn 1 phần tử.
- **Conquer**: Các mảng con 1 phần tử được xem là đã sắp xếp.
- **Combine**: Trộn (Merge) 2 mảng con đã sắp xếp thành 1 mảng lớn hơn, duy trì tính sắp xếp. Quá trình này lặp lại cho đến khi ghép lại thành mảng ban đầu.

**Mã nguồn Java:**
```java
public class MergeSort {
    public void sort(int[] nums) {
        if (nums == null || nums.length <= 1) return;
        mergeSort(nums, 0, nums.length - 1);
    }
    
    private void mergeSort(int[] nums, int left, int right) {
        if (left < right) {
            int mid = left + (right - left) / 2;
            mergeSort(nums, left, mid);
            mergeSort(nums, mid + 1, right);
            merge(nums, left, mid, right);
        }
    }
    
    private void merge(int[] nums, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) {
                temp[k++] = nums[i++];
            } else {
                temp[k++] = nums[j++];
            }
        }
        
        while (i <= mid) {
            temp[k++] = nums[i++];
        }
        
        while (j <= right) {
            temp[k++] = nums[j++];
        }
        
        for (int p = 0; p < temp.length; p++) {
            nums[left + p] = temp[p];
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ cho mọi trường hợp.
- **Không gian (Space):** $O(N)$ do cần mảng phụ `temp` để trộn các phần tử.

---

## 12. Quick Sort Algorithm

**Đề bài chi tiết:**
Cài đặt thuật toán sắp xếp nhanh (Quick Sort) để sắp xếp một mảng số nguyên theo thứ tự tăng dần.

**Phân tích thuật toán:**
- **Divide**: Chọn một phần tử làm chốt (pivot). Phân hoạch (Partition) mảng thành 2 phần: phần trái gồm các phần tử nhỏ hơn hoặc bằng pivot, phần phải gồm các phần tử lớn hơn pivot.
- **Conquer**: Gọi đệ quy Quick Sort trên 2 phần trái và phải.
- **Combine**: Không cần bước kết hợp vì mảng được sắp xếp tại chỗ (in-place) trong quá trình phân hoạch.

**Mã nguồn Java:**
```java
public class QuickSort {
    public void sort(int[] nums) {
        if (nums == null || nums.length <= 1) return;
        quickSort(nums, 0, nums.length - 1);
    }
    
    private void quickSort(int[] nums, int left, int right) {
        if (left < right) {
            int pivotIndex = partition(nums, left, right);
            quickSort(nums, left, pivotIndex - 1);
            quickSort(nums, pivotIndex + 1, right);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int pivot = nums[right];
        int i = left - 1;
        
        for (int j = left; j < right; j++) {
            if (nums[j] <= pivot) {
                i++;
                swap(nums, i, j);
            }
        }
        swap(nums, i + 1, right);
        return i + 1;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** Trung bình $O(N \log N)$. Tồi tệ nhất $O(N^2)$ nếu pivot luôn là phần tử lớn nhất/nhỏ nhất (mảng đã sắp xếp).
- **Không gian (Space):** $O(\log N)$ (trung bình) cho call stack, tồi tệ nhất $O(N)$.

---

## 13. Binary Search (LeetCode 704)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` được sắp xếp theo thứ tự tăng dần và một số nguyên `target`. Hãy tìm chỉ số của `target` trong mảng `nums`. Nếu `target` không tồn tại, trả về `-1`. 

**Phân tích thuật toán:**
- D&C: Do mảng đã sắp xếp, ta có thể so sánh `target` với phần tử ở giữa (mid).
- Nếu `nums[mid] == target`, trả về `mid`.
- Nếu `nums[mid] < target`, phần tử cần tìm chỉ có thể nằm ở nửa phải. Gọi đệ quy (hoặc lặp) trên mảng bên phải.
- Nếu `nums[mid] > target`, gọi tìm kiếm trên nửa trái.

**Mã nguồn Java:**
```java
public class BinarySearch {
    public int search(int[] nums, int target) {
        return binarySearchRec(nums, target, 0, nums.length - 1);
    }
    
    private int binarySearchRec(int[] nums, int target, int left, int right) {
        if (left > right) return -1;
        
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            return binarySearchRec(nums, target, mid + 1, right);
        } else {
            return binarySearchRec(nums, target, left, mid - 1);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log N)$ do kích thước bài toán giảm đi một nửa sau mỗi bước.
- **Không gian (Space):** $O(\log N)$ cho đệ quy stack (hoặc $O(1)$ nếu giải bằng Iterative).

---

## 14. Construct Binary Tree from Preorder and Inorder Traversal (LeetCode 105)

**Đề bài chi tiết:**
Cho hai mảng số nguyên `preorder` và `inorder`, trong đó `preorder` là kết quả duyệt tiền thứ tự (preorder traversal) của một cây nhị phân và `inorder` là duyệt trung thứ tự (inorder traversal) của cùng cây đó. Hãy xây dựng và trả về cây nhị phân ban đầu.

**Phân tích thuật toán:**
- **Divide**: 
  - Phần tử đầu tiên trong `preorder` luôn là gốc (Root).
  - Tìm vị trí của gốc này trong mảng `inorder`. Vị trí này sẽ chia `inorder` thành 2 phần: nửa trái là cây con trái, nửa phải là cây con phải.
- **Conquer**: Đệ quy xây dựng cây con trái và cây con phải từ các mảng con tương ứng.
- **Combine**: Gắn cây con trái và phải vào node Root hiện tại.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

public class ConstructTreePreorderInorder {
    private Map<Integer, Integer> inorderMap;
    private int preorderIndex;
    
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        inorderMap = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            inorderMap.put(inorder[i], i);
        }
        preorderIndex = 0;
        return buildTreeRec(preorder, 0, inorder.length - 1);
    }
    
    private TreeNode buildTreeRec(int[] preorder, int left, int right) {
        if (left > right) return null;
        
        int rootVal = preorder[preorderIndex++];
        TreeNode root = new TreeNode(rootVal);
        
        int mid = inorderMap.get(rootVal);
        
        root.left = buildTreeRec(preorder, left, mid - 1);
        root.right = buildTreeRec(preorder, mid + 1, right);
        
        return root;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ vì chúng ta dùng `HashMap` để tra cứu vị trí trong $O(1)$ và xây dựng từng node một lần.
- **Không gian (Space):** $O(N)$ lưu trữ `HashMap` và $O(H)$ cho call stack đệ quy.

---

## 15. Construct Binary Tree from Inorder and Postorder Traversal (LeetCode 106)

**Đề bài chi tiết:**
Cho hai mảng số nguyên `inorder` và `postorder` (hậu thứ tự). Hãy xây dựng và trả về cây nhị phân ban đầu.

**Phân tích thuật toán:**
- Tương tự như bài 14, nhưng phần tử cuối cùng của `postorder` luôn là gốc (Root).
- Tìm vị trí của Root trong `inorder` để xác định độ lớn của cây con trái và phải.
- **Quan trọng**: Do `postorder` duyệt Trái -> Phải -> Gốc, khi duyệt ngược từ cuối mảng `postorder`, ta sẽ xây dựng **cây con phải** trước, rồi mới đến **cây con trái**.

**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

public class ConstructTreeInorderPostorder {
    private Map<Integer, Integer> inorderMap;
    private int postorderIndex;
    
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        inorderMap = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            inorderMap.put(inorder[i], i);
        }
        postorderIndex = postorder.length - 1;
        return buildTreeRec(postorder, 0, inorder.length - 1);
    }
    
    private TreeNode buildTreeRec(int[] postorder, int left, int right) {
        if (left > right) return null;
        
        int rootVal = postorder[postorderIndex--];
        TreeNode root = new TreeNode(rootVal);
        
        int mid = inorderMap.get(rootVal);
        
        // Cần xây dựng nhánh phải trước vì duyệt postorder từ cuối lên
        root.right = buildTreeRec(postorder, mid + 1, right);
        root.left = buildTreeRec(postorder, left, mid - 1);
        
        return root;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ sử dụng `HashMap` lookup.
- **Không gian (Space):** $O(N)$ cho `HashMap` cộng không gian Stack đệ quy $O(H)$.

---

## 16. Kth Largest Element in an Array (Quick Select) (LeetCode 215)

**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và số nguyên `k`, hãy tìm phần tử lớn thứ `k` trong mảng. Giải bài toán mà không sắp xếp toàn bộ mảng (sử dụng Quick Select - một biến thể của Divide and Conquer).

**Phân tích thuật toán:**
- Tương tự Quick Sort, chọn 1 phần tử làm `pivot` và phân hoạch mảng. 
- Sau khi phân hoạch, `pivot` sẽ nằm ở vị trí chính xác của nó như thể mảng đã được sắp xếp.
- Giả sử vị trí của `pivot` là `p`. Nếu `p` bằng vị trí của phần tử lớn thứ `k` (tức là index `N - k` nếu mảng tăng dần), ta tìm được kết quả.
- Nếu `p < N - k`, phần tử cần tìm nằm ở nửa bên phải của `pivot`.
- Nếu `p > N - k`, tìm ở nửa bên trái. Thuật toán này bỏ qua một nửa mảng không cần thiết mỗi lần đệ quy.

**Mã nguồn Java:**
```java
import java.util.Random;

public class KthLargestElement {
    public int findKthLargest(int[] nums, int k) {
        int targetIndex = nums.length - k;
        return quickSelect(nums, 0, nums.length - 1, targetIndex);
    }
    
    private int quickSelect(int[] nums, int left, int right, int target) {
        if (left == right) return nums[left];
        
        Random rand = new Random();
        int pivotIndex = left + rand.nextInt(right - left + 1);
        pivotIndex = partition(nums, left, right, pivotIndex);
        
        if (pivotIndex == target) {
            return nums[pivotIndex];
        } else if (pivotIndex < target) {
            return quickSelect(nums, pivotIndex + 1, right, target);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, target);
        }
    }
    
    private int partition(int[] nums, int left, int right, int pivotIndex) {
        int pivotValue = nums[pivotIndex];
        swap(nums, pivotIndex, right);
        int storeIndex = left;
        
        for (int i = left; i < right; i++) {
            if (nums[i] < pivotValue) {
                swap(nums, storeIndex, i);
                storeIndex++;
            }
        }
        swap(nums, storeIndex, right);
        return storeIndex;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** Trung bình $O(N)$ do ta loại bỏ được một nửa (hoặc một phần) mảng ở mỗi bước đệ quy. Tồi tệ nhất $O(N^2)$ (đã giảm thiểu bằng Random Pivot).
- **Không gian (Space):** $O(\log N)$ trung bình trên call stack.

---

## 17. Balanced Binary Tree (LeetCode 110)

**Đề bài chi tiết:**
Cho một cây nhị phân, xác định xem nó có phải là một cây cân bằng độ cao (height-balanced) hay không. Cây cân bằng độ cao được định nghĩa là cây mà độ sâu của hai cây con của bất kỳ node nào không bao giờ chênh lệch quá 1.

**Phân tích thuật toán:**
- Thay vì gọi hàm `height` tốn $O(N)$ cho mỗi node dẫn đến $O(N^2)$, ta có thể tính chiều cao đệ quy từ dưới lên (Bottom-up).
- D&C: Gọi đệ quy lấy chiều cao của cây con trái và phải. Nếu phát hiện chênh lệch $> 1$, đánh dấu cây không cân bằng bằng cách trả về `-1`.
- Nếu một trong hai cây con trả về `-1`, ta lan truyền kết quả `-1` lên trên (Combine).

**Mã nguồn Java:**
```java
public class BalancedBinaryTree {
    public boolean isBalanced(TreeNode root) {
        return checkHeight(root) != -1;
    }
    
    private int checkHeight(TreeNode node) {
        if (node == null) return 0; // Chiều cao = 0
        
        int leftHeight = checkHeight(node.left);
        if (leftHeight == -1) return -1; // Cây trái lệch
        
        int rightHeight = checkHeight(node.right);
        if (rightHeight == -1) return -1; // Cây phải lệch
        
        // Kiểm tra điều kiện cân bằng
        if (Math.abs(leftHeight - rightHeight) > 1) {
            return -1; // Đánh dấu không cân bằng
        }
        
        return Math.max(leftHeight, rightHeight) + 1; // Chiều cao node hiện tại
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ do mỗi node chỉ được thăm đúng 1 lần (tính từ dưới lên).
- **Không gian (Space):** $O(H)$ với $H$ là chiều cao của cây (call stack).

---

## 18. Invert Binary Tree (LeetCode 226)

**Đề bài chi tiết:**
Đảo ngược một cây nhị phân (trái thành phải, phải thành trái cho toàn bộ các node) và trả về gốc của nó.

**Phân tích thuật toán (D&C):**
- **Divide**: Đảo ngược cây con trái và đảo ngược cây con phải thông qua đệ quy.
- **Combine**: Tráo đổi (swap) con trỏ trái và phải của node hiện tại.
- Thuật toán này có thể thực hiện theo dạng Top-down hoặc Bottom-up. Dưới đây là Bottom-up/Top-down kết hợp đơn giản.

**Mã nguồn Java:**
```java
public class InvertBinaryTree {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        
        // Tráo đổi con trái và con phải
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        
        // Đệ quy chia để trị cho hai nhánh
        invertTree(root.left);
        invertTree(root.right);
        
        return root; // Kết hợp trả về gốc đã đảo
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ vì chúng ta phải truy cập tất cả $N$ nodes trong cây.
- **Không gian (Space):** $O(H)$ với $H$ là chiều cao của cây.

---

## 19. Lowest Common Ancestor of a Binary Tree (LeetCode 236)

**Đề bài chi tiết:**
Cho một cây nhị phân và hai node `p` và `q` nằm trong cây đó. Hãy tìm tổ tiên chung gần nhất (Lowest Common Ancestor - LCA) của `p` và `q`.

**Phân tích thuật toán:**
- D&C: Đệ quy tìm `p` và `q` trên cây con trái và phải.
- Nếu node hiện tại là `null` hoặc bằng `p` hoặc bằng `q`, trả về node đó.
- Gọi hàm trên nhánh trái (`left`) và nhánh phải (`right`).
- **Combine**: Nếu cả `left` và `right` đều khác `null`, chứng tỏ `p` nằm một bên, `q` nằm một bên so với node hiện tại $\implies$ Node hiện tại chính là LCA. Nếu một bên trả về `null`, tức là cả `p` và `q` đều nằm ở nhánh kia, ta chỉ việc trả về nhánh khác `null`.

**Mã nguồn Java:**
```java
public class LowestCommonAncestor {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // Base case
        if (root == null || root == p || root == q) {
            return root;
        }
        
        // Divide: Tìm LCA ở nhánh trái và nhánh phải
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        
        // Combine
        if (left != null && right != null) {
            // p và q nằm ở 2 nhánh khác nhau
            return root; 
        }
        
        // Trả về nhánh có chứa ít nhất 1 node
        return left != null ? left : right;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ trong trường hợp tồi tệ nhất cần duyệt hết cây.
- **Không gian (Space):** $O(H)$ với $H$ là chiều cao của cây do đệ quy stack.

---

## 20. Merge k Sorted Lists (LeetCode 23)

**Đề bài chi tiết:**
Cho một mảng gồm `k` danh sách liên kết (Linked Lists), mỗi danh sách liên kết đã được sắp xếp tăng dần. Hãy hợp nhất tất cả các danh sách liên kết này thành một danh sách liên kết duy nhất được sắp xếp tăng dần và trả về nó.

**Phân tích thuật toán (D&C):**
- Thay vì trộn từng List vào một List lớn (tốn $O(k \cdot N)$), ta có thể áp dụng chiến lược Chia để trị (như Merge Sort).
- **Divide**: Chia mảng các danh sách liên kết thành 2 nửa.
- **Conquer**: Gọi đệ quy hợp nhất 2 nửa độc lập với nhau.
- **Combine**: Trộn (Merge) hai danh sách kết quả lại thành một danh sách (giống bài Merge Two Sorted Lists).
- Bước chia giảm số lần trộn danh sách dài xuống còn mức logarit.

**Mã nguồn Java:**
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class MergeKSortedLists {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        return mergeRec(lists, 0, lists.length - 1);
    }
    
    private ListNode mergeRec(ListNode[] lists, int left, int right) {
        if (left == right) return lists[left];
        
        int mid = left + (right - left) / 2;
        ListNode l1 = mergeRec(lists, left, mid);
        ListNode l2 = mergeRec(lists, mid + 1, right);
        
        return mergeTwoLists(l1, l2);
    }
    
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }
            curr = curr.next;
        }
        
        if (l1 != null) curr.next = l1;
        if (l2 != null) curr.next = l2;
        
        return dummy.next;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log k)$, trong đó $N$ là tổng số lượng các nodes của tất cả các Linked Lists, $k$ là số lượng Lists. Cây đệ quy có độ sâu $\log k$.
- **Không gian (Space):** $O(\log k)$ cho call stack.

---

## 21. Different Ways to Add Parentheses (LeetCode 241)

**Đề bài chi tiết:**
Cho một chuỗi `expression` chứa các con số và các phép toán `+`, `-`, `*`, hãy trả về tất cả các kết quả có thể có từ việc tính toán các cách nhóm (đặt dấu ngoặc) khác nhau. Bạn có thể trả về câu trả lời theo bất kỳ thứ tự nào.

**Phân tích thuật toán:**
- Sử dụng Chia để trị: Tại mỗi phép toán (`+`, `-`, `*`), ta chia biểu thức thành hai nửa (trái và phải).
- Đệ quy giải bài toán con cho nửa trái và nửa phải, kết quả là tập hợp các giá trị có thể.
- Kết hợp (Combine): Tính toán chéo từng phần tử kết quả của nửa trái với từng phần tử của nửa phải dựa trên phép toán hiện tại và thêm vào danh sách kết quả tổng.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class DifferentWaysToAddParentheses {
    public List<Integer> diffWaysToCompute(String expression) {
        List<Integer> result = new ArrayList<>();
        
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                String part1 = expression.substring(0, i);
                String part2 = expression.substring(i + 1);
                
                List<Integer> left = diffWaysToCompute(part1);
                List<Integer> right = diffWaysToCompute(part2);
                
                for (int l : left) {
                    for (int r : right) {
                        if (c == '+') result.add(l + r);
                        else if (c == '-') result.add(l - r);
                        else if (c == '*') result.add(l * r);
                    }
                }
            }
        }
        
        if (result.isEmpty()) {
            result.add(Integer.parseInt(expression));
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** Kích thước kết quả liên quan đến số Catalan (Catalan number). $O(2^N)$ cho số lượng phép tính, với $N$ là số lượng dấu toán tử.
- **Không gian (Space):** $O(2^N)$ để lưu trữ danh sách kết quả và call stack trong trường hợp xấu nhất. (Có thể dùng Memoization để tối ưu hoá).

---

## 22. The Skyline Problem (LeetCode 218)

**Đề bài chi tiết:**
Hình bóng của một thành phố (skyline) là đường viền ngoài cùng được tạo ra bởi tất cả các tòa nhà khi nhìn từ xa. Cho vị trí và chiều cao của tất cả các tòa nhà, hãy trả về skyline hình thành bởi các tòa nhà này. Mỗi toà nhà được cho dưới dạng `[left, right, height]`. Skyline là danh sách các điểm ngoặt (key points).

**Phân tích thuật toán:**
- D&C: Áp dụng tư tưởng của Merge Sort.
- Chia tập hợp các toà nhà thành 2 nửa.
- Tìm skyline cho 2 nửa đệ quy.
- Trộn (Merge): Kết hợp 2 skyline bằng cách duy trì chiều cao hiện tại của 2 bên (`h1`, `h2`) và xét toạ độ nhỏ hơn. Chiều cao tối đa hiện tại = `max(h1, h2)`. Nếu chiều cao tối đa thay đổi thì thêm vào kết quả.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class SkylineProblem {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        if (buildings == null || buildings.length == 0) return new ArrayList<>();
        return getSkylineRec(buildings, 0, buildings.length - 1);
    }
    
    private List<List<Integer>> getSkylineRec(int[][] buildings, int left, int right) {
        List<List<Integer>> res = new ArrayList<>();
        if (left == right) {
            res.add(Arrays.asList(buildings[left][0], buildings[left][2]));
            res.add(Arrays.asList(buildings[left][1], 0));
            return res;
        }
        
        int mid = left + (right - left) / 2;
        List<List<Integer>> leftSkyline = getSkylineRec(buildings, left, mid);
        List<List<Integer>> rightSkyline = getSkylineRec(buildings, mid + 1, right);
        return mergeSkylines(leftSkyline, rightSkyline);
    }
    
    private List<List<Integer>> mergeSkylines(List<List<Integer>> sky1, List<List<Integer>> sky2) {
        List<List<Integer>> res = new ArrayList<>();
        int h1 = 0, h2 = 0, i = 0, j = 0;
        
        while (i < sky1.size() && j < sky2.size()) {
            int x, h;
            List<Integer> point1 = sky1.get(i);
            List<Integer> point2 = sky2.get(j);
            
            if (point1.get(0) < point2.get(0)) {
                x = point1.get(0);
                h1 = point1.get(1);
                i++;
            } else if (point1.get(0) > point2.get(0)) {
                x = point2.get(0);
                h2 = point2.get(1);
                j++;
            } else {
                x = point1.get(0);
                h1 = point1.get(1);
                h2 = point2.get(1);
                i++; j++;
            }
            
            h = Math.max(h1, h2);
            if (res.isEmpty() || !res.get(res.size() - 1).get(1).equals(h)) {
                res.add(Arrays.asList(x, h));
            }
        }
        
        while (i < sky1.size()) res.add(sky1.get(i++));
        while (j < sky2.size()) res.add(sky2.get(j++));
        
        return res;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ theo Master Theorem tương tự Merge Sort.
- **Không gian (Space):** $O(N \log N)$ cho việc lưu trữ các mảng phụ trong quá trình merge và cây đệ quy.

---

## 23. Search in Rotated Sorted Array (LeetCode 33)

**Đề bài chi tiết:**
Cho một mảng nguyên `nums` được sắp xếp tăng dần nhưng bị "xoay" (rotated) tại một điểm pivot không xác định trước, và một số `target`. Hãy tìm và trả về chỉ số của `target` trong `nums`, nếu không tìm thấy trả về `-1`. Yêu cầu thuật toán với độ phức tạp $O(\log N)$.

**Phân tích thuật toán:**
- Đây là bài toán áp dụng D&C thông qua Binary Search.
- Tại bất kỳ thời điểm nào chia mảng bằng `mid`, ít nhất một nửa (trái hoặc phải) của mảng luôn được sắp xếp tăng dần và không bị xoay.
- Ta kiểm tra nửa nào là chuẩn sắp xếp. Từ đó xem `target` có nằm trong nửa đó hay không. Nếu có, thu hẹp về nửa đó, nếu không, tìm nửa còn lại.

**Mã nguồn Java:**
```java
public class SearchRotatedSortedArray {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) return mid;
            
            if (nums[left] <= nums[mid]) { // Nửa trái được sắp xếp chuẩn
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else { // Nửa phải được sắp xếp chuẩn
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        
        return -1;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log N)$, loại bỏ một nửa mảng sau mỗi lần chia (Divide and Conquer).
- **Không gian (Space):** $O(1)$.

---

## 24. Find Minimum in Rotated Sorted Array (LeetCode 153)

**Đề bài chi tiết:**
Cho mảng `nums` độ dài `n` đã sắp xếp tăng dần và bị xoay `1` đến `n` lần. Tất cả các phần tử đều là duy nhất. Tìm phần tử nhỏ nhất của mảng này trong $O(\log N)$.

**Phân tích thuật toán:**
- Áp dụng D&C (Binary Search).
- Ta so sánh `nums[mid]` với `nums[right]`.
- Nếu `nums[mid] > nums[right]`, chứng tỏ mảng không nằm theo thứ tự bình thường, phần tử nhỏ nhất chắc chắn phải nằm ở nửa bên phải (từ `mid + 1` đến `right`).
- Ngược lại `nums[mid] <= nums[right]`, phần nhỏ nhất nằm ở bên trái hoặc ngay tại `mid`. Do đó `right = mid`.

**Mã nguồn Java:**
```java
public class FindMinimumRotatedArray {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        
        return nums[left];
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log N)$.
- **Không gian (Space):** $O(1)$.

---

## 25. Count Inversions in an Array

**Đề bài chi tiết:**
Cho một mảng `nums`. Một cặp phần tử `(i, j)` được gọi là một nghịch thế (inversion) nếu `i < j` và `nums[i] > nums[j]`. Hãy đếm số lượng nghịch thế trong mảng.

**Phân tích thuật toán:**
- D&C: Thay vì đếm kiểu $O(N^2)$, ta dùng Merge Sort để đếm nghịch thế.
- Phân hoạch: Gọi đệ quy nửa trái và nửa phải, lấy tổng số đếm của 2 nửa.
- Trong lúc Merge (trộn hai nửa đã được sắp xếp tăng dần): Nếu phần tử ở nửa trái lớn hơn phần tử ở nửa phải (`nums[i] > nums[j]`), điều đó có nghĩa là **tất cả các phần tử còn lại của nửa trái** (vì chúng đã được sắp xếp) cũng sẽ lớn hơn `nums[j]`. Ta cộng thêm `(mid - i + 1)` vào số nghịch thế.

**Mã nguồn Java:**
```java
public class CountInversions {
    public long countInversions(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        int[] temp = new int[nums.length];
        return mergeSortAndCount(nums, temp, 0, nums.length - 1);
    }
    
    private long mergeSortAndCount(int[] nums, int[] temp, int left, int right) {
        long count = 0;
        if (left < right) {
            int mid = left + (right - left) / 2;
            count += mergeSortAndCount(nums, temp, left, mid);
            count += mergeSortAndCount(nums, temp, mid + 1, right);
            count += mergeAndCount(nums, temp, left, mid, right);
        }
        return count;
    }
    
    private long mergeAndCount(int[] nums, int[] temp, int left, int mid, int right) {
        int i = left, j = mid + 1, k = left;
        long invCount = 0;
        
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) {
                temp[k++] = nums[i++];
            } else {
                temp[k++] = nums[j++];
                invCount += (mid - i + 1); // Đếm nghịch thế
            }
        }
        
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        for (i = left; i <= right; i++) nums[i] = temp[i];
        
        return invCount;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N \log N)$ giống hoàn toàn với Merge Sort.
- **Không gian (Space):** $O(N)$ lưu mảng `temp`.

---

## 26. Median of Two Sorted Arrays (LeetCode 4)

**Đề bài chi tiết:**
Cho hai mảng đã được sắp xếp `nums1` và `nums2` có kích thước lần lượt là $m$ và $n$, trả về trung vị (median) của hai mảng sau khi được trộn. Yêu cầu thời gian chạy tổng thể là $O(\log(m+n))$.

**Phân tích thuật toán:**
- D&C: Thay vì trộn hai mảng rồi tìm trung vị ($O(M+N)$), bài toán có thể quy về việc "Tìm phần tử lớn thứ K" trong hai mảng đã sắp xếp.
- K là $\frac{M+N+1}{2}$. Ta có thể loại bỏ `K/2` phần tử ở mỗi bước.
- Lấy phần tử thứ `K/2` của mỗi mảng và so sánh:
  - Nếu `nums1[K/2 - 1] < nums2[K/2 - 1]`, thì `K/2` phần tử đầu tiên của `nums1` chắc chắn không thể là kết quả. Ta có thể an toàn vứt bỏ nó, và thu hẹp $K$ thành $K - K/2$.

**Mã nguồn Java:**
```java
public class MedianOfTwoSortedArrays {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int total = nums1.length + nums2.length;
        if (total % 2 != 0) {
            return findKth(nums1, 0, nums2, 0, total / 2 + 1);
        } else {
            int left = findKth(nums1, 0, nums2, 0, total / 2);
            int right = findKth(nums1, 0, nums2, 0, total / 2 + 1);
            return (left + right) / 2.0;
        }
    }
    
    private int findKth(int[] nums1, int start1, int[] nums2, int start2, int k) {
        if (start1 >= nums1.length) return nums2[start2 + k - 1];
        if (start2 >= nums2.length) return nums1[start1 + k - 1];
        if (k == 1) return Math.min(nums1[start1], nums2[start2]);
        
        int halfK = k / 2;
        int midVal1 = (start1 + halfK - 1 < nums1.length) ? nums1[start1 + halfK - 1] : Integer.MAX_VALUE;
        int midVal2 = (start2 + halfK - 1 < nums2.length) ? nums2[start2 + halfK - 1] : Integer.MAX_VALUE;
        
        if (midVal1 < midVal2) {
            return findKth(nums1, start1 + halfK, nums2, start2, k - halfK);
        } else {
            return findKth(nums1, start1, nums2, start2 + halfK, k - halfK);
        }
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log(M+N))$ ở mỗi bước không gian tìm kiếm chia đi một nửa.
- **Không gian (Space):** $O(\log(M+N))$ là độ sâu tối đa của ngăn xếp đệ quy.

---

## 27. Number of 1 Bits (D&C approach)

**Đề bài chi tiết:**
Viết một hàm nhận vào một số nguyên không dấu (unsigned integer) và trả về số lượng bit '1' mà nó sở hữu (hay còn gọi là Hamming weight).

**Phân tích thuật toán:**
- Dù cách phổ biến nhất là thao tác bit `n & (n - 1)`, ta có thể áp dụng D&C (Divide and Conquer bitwise SWAR algorithm - SIMD within a register).
- Tư tưởng:
  - Gom từng cặp 1 bit, cộng lại (lưu vào 2 bit).
  - Gom các cụm 2 bit, cộng lại (lưu vào 4 bit).
  - Gom các cụm 4 bit, cộng lại... cho đến khi gom toàn bộ 32 bit.

**Mã nguồn Java:**
```java
public class NumberOf1Bits {
    // Note: Tham số n được truyền là signed int trong Java nhưng xử lý bằng dịch phải không dấu >>>
    public int hammingWeight(int n) {
        // Cụm 1 bit -> Cụm 2 bit
        n = (n & 0x55555555) + ((n >>> 1) & 0x55555555);
        // Cụm 2 bit -> Cụm 4 bit
        n = (n & 0x33333333) + ((n >>> 2) & 0x33333333);
        // Cụm 4 bit -> Cụm 8 bit
        n = (n & 0x0F0F0F0F) + ((n >>> 4) & 0x0F0F0F0F);
        // Cụm 8 bit -> Cụm 16 bit
        n = (n & 0x00FF00FF) + ((n >>> 8) & 0x00FF00FF);
        // Cụm 16 bit -> Cụm 32 bit
        n = (n & 0x0000FFFF) + ((n >>> 16) & 0x0000FFFF);
        return n;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log W)$, với $W$ là số lượng bit (32 bit). Vì số phép toán bằng $\log_2(32) = 5$, ta coi như $O(1)$.
- **Không gian (Space):** $O(1)$.

---

## 28. Find First and Last Position of Element in Sorted Array (LeetCode 34)

**Đề bài chi tiết:**
Cho một mảng nguyên `nums` được sắp xếp theo chiều tăng dần, tìm vị trí bắt đầu và vị trí kết thúc của số `target`. Nếu không tìm thấy, trả về `[-1, -1]`. Yêu cầu thuật toán phải có độ phức tạp $O(\log n)$.

**Phân tích thuật toán:**
- Chia để trị (Binary Search): Thay vì tìm một điểm, ta chia thành 2 hàm tìm kiếm riêng biệt.
- Lần 1: Binary search tìm biên trái (Left bound). Khi `nums[mid] == target`, ta không dừng mà ép `right = mid - 1` để đẩy tiếp về bên trái.
- Lần 2: Binary search tìm biên phải (Right bound). Khi `nums[mid] == target`, ta ép `left = mid + 1` để đẩy tiếp về bên phải.

**Mã nguồn Java:**
```java
public class FindFirstLastPosition {
    public int[] searchRange(int[] nums, int target) {
        int left = findBound(nums, target, true);
        if (left == -1) return new int[]{-1, -1};
        int right = findBound(nums, target, false);
        return new int[]{left, right};
    }
    
    private int findBound(int[] nums, int target, boolean isFirst) {
        int left = 0, right = nums.length - 1, ans = -1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                ans = mid;
                if (isFirst) {
                    right = mid - 1; // Đẩy sang trái
                } else {
                    left = mid + 1; // Đẩy sang phải
                }
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return ans;
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(\log n)$, vì ta thực hiện hai phép tìm kiếm nhị phân liên tiếp độc lập.
- **Không gian (Space):** $O(1)$ chỉ sử dụng thêm các biến con trỏ.

---

## 29. Symmetric Tree (LeetCode 101)

**Đề bài chi tiết:**
Cho gốc (root) của một cây nhị phân, kiểm tra xem nó có phải là ảnh phản chiếu của chính nó hay không (tức là đối xứng quanh tâm của nó).

**Phân tích thuật toán:**
- D&C: Hai cây T1 và T2 đối xứng nhau nếu:
  1. Gốc của chúng có giá trị bằng nhau.
  2. Cây con trái của T1 đối xứng với cây con phải của T2 và ngược lại.
- Trị (Conquer): Ta viết một hàm phụ kiểm tra hai node song song theo điều kiện trên đệ quy.

**Mã nguồn Java:**
```java
public class SymmetricTree {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }
    
    private boolean isMirror(TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        
        return (t1.val == t2.val)
            && isMirror(t1.left, t2.right)
            && isMirror(t1.right, t2.left);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(N)$ vì chúng ta duyệt toàn bộ cây.
- **Không gian (Space):** $O(H)$ không gian do call stack. Với cây lệch nhiều $H = O(N)$, cây hoàn hảo $H = O(\log N)$.

---

## 30. Subtree of Another Tree (LeetCode 572)

**Đề bài chi tiết:**
Cho gốc (root) của hai cây nhị phân `s` (cây gốc) và `t` (cây con), kiểm tra xem cây `t` có phải là một cây con của `s` hay không. Một cây con của `s` là một cây bao gồm một node nằm trong `s` và toàn bộ hậu duệ của node đó.

**Phân tích thuật toán:**
- D&C kết hợp hai bước:
  1. Duyệt (Divide) cây `s`: Với mỗi node trong cây `s`, ta xem nó có phải là điểm xuất phát làm nên cây `t` hay không.
  2. Trị (Conquer): So sánh hai cây (hàm `isSameTree` dựa trên D&C).
- Cây `t` là cây con nếu `t` giống hệt cây hiện tại `s`, hoặc `t` là cây con của nhánh trái của `s`, hoặc `t` là cây con của nhánh phải của `s`.

**Mã nguồn Java:**
```java
public class SubtreeOfAnotherTree {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (s == null) return false;
        if (isSameTree(s, t)) return true;
        
        // Đệ quy D&C trên nhánh trái và phải của s
        return isSubtree(s.left, t) || isSubtree(s.right, t);
    }
    
    private boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        return (p.val == q.val)
            && isSameTree(p.left, q.left)
            && isSameTree(p.right, q.right);
    }
}
```

**Độ phức tạp:**
- **Thời gian (Time):** $O(M \times N)$ trong đó $M$ là số node của `s`, $N$ là số node của `t`. Trường hợp tồi tệ nhất phải so sánh `t` với tất cả các node trong `s`.
- **Không gian (Space):** $O(\max(H_M, H_N))$ là độ sâu lớn nhất cho call stack đệ quy.
