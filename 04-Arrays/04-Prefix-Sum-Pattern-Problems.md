# Companion Problems: 04-Prefix-Sum-Pattern

Tài liệu này cung cấp **30 bài tập thực hành** về Prefix Sum từ cơ bản đến nâng cao.

---

## 1. Range Sum Query - Immutable (LeetCode 303)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Xử lý nhiều truy vấn tính tổng các phần tử trong khoảng `[left, right]`.
**Phân tích thuật toán:** Sử dụng mảng Prefix Sum $N+1$. Khởi tạo `prefix[i] = prefix[i-1] + nums[i-1]`. Truy vấn `Sum(left, right) = prefix[right+1] - prefix[left]`. Thời gian khởi tạo $\mathcal{O}(N)$, thời gian truy vấn $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
class NumArray {
    private int[] prefix;

    public NumArray(int[] nums) {
        prefix = new int[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        return prefix[right + 1] - prefix[left];
    }
}
```
**Độ phức tạp:** Khởi tạo: Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. Truy vấn: Time $\mathcal{O}(1)$.

---

## 2. Subarray Sum Equals K (LeetCode 560)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` (có số âm) và số nguyên `k`. Tìm số lượng mảng con liên tiếp có tổng bằng `k`.
**Phân tích thuật toán:** Duyệt qua mảng để tính Prefix Sum hiện tại. Dùng `HashMap` lưu tần suất xuất hiện của các Prefix Sum trước đó. Tại bước $i$, tìm xem `currentSum - k` đã xuất hiện bao nhiêu lần. Cộng tần suất đó vào kết quả. Khởi tạo map với `(0, 1)`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class SubarraySumK {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int currentSum = 0;
        int count = 0;
        
        for (int num : nums) {
            currentSum += num;
            if (map.containsKey(currentSum - k)) {
                count += map.get(currentSum - k);
            }
            map.put(currentSum, map.getOrDefault(currentSum, 0) + 1);
        }
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 3. Contiguous Array (LeetCode 525)
**Đề bài chi tiết:** Cho mảng nhị phân `nums`. Tìm chiều dài tối đa của mảng con liên tiếp chứa số lượng 0 và 1 bằng nhau.
**Phân tích thuật toán:** Đổi tất cả số 0 thành -1. Bài toán trở thành "Tìm mảng con dài nhất có tổng bằng 0". Dùng `HashMap` lưu lại **chỉ số (index) đầu tiên** mà Prefix Sum xuất hiện. Chiều dài = `i - map.get(sum)`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class ContiguousArray {
    public int findMaxLength(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1); // Base case cho mảng con bắt đầu từ index 0
        int maxLength = 0;
        int currentSum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            currentSum += (nums[i] == 1) ? 1 : -1;
            if (map.containsKey(currentSum)) {
                maxLength = Math.max(maxLength, i - map.get(currentSum));
            } else {
                map.put(currentSum, i); // Chỉ lưu lần xuất hiện ĐẦU TIÊN
            }
        }
        return maxLength;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 4. Find Pivot Index (LeetCode 724)
**Đề bài chi tiết:** Cho mảng số nguyên. Tìm "Pivot Index" sao cho tổng bên trái index bằng tổng bên phải index. Nếu không có trả về -1. Trả về index nhỏ nhất nếu có nhiều.
**Phân tích thuật toán:** Tính `totalSum` của toàn mảng trước ($\mathcal{O}(N)$). Duyệt qua mảng lần 2, duy trì `leftSum`. `rightSum` tại bước $i$ = `totalSum - leftSum - nums[i]`. Nếu `leftSum == rightSum`, trả về $i$.
**Mã nguồn Java:**
```java
public class PivotIndex {
    public int pivotIndex(int[] nums) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int leftSum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (leftSum == totalSum - leftSum - nums[i]) {
                return i;
            }
            leftSum += nums[i];
        }
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 5. Product of Array Except Self (LeetCode 238)
**Đề bài chi tiết:** Trả về mảng `answer` sao cho `answer[i]` bằng tích của tất cả phần tử trừ `nums[i]`. Yêu cầu: Không dùng phép chia, thời gian $\mathcal{O}(N)$, không gian phụ $\mathcal{O}(1)$.
**Phân tích thuật toán:** Tính Prefix Product: `answer[i]` lưu tích từ đầu đến $i-1$. Sau đó duyệt ngược từ cuối mảng về đầu, dùng 1 biến `suffixProduct` nhân dần vào `answer[i]`.
**Mã nguồn Java:**
```java
public class ProductExceptSelf {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] answer = new int[n];
        
        // Build prefix products directly in answer array
        answer[0] = 1;
        for (int i = 1; i < n; i++) {
            answer[i] = answer[i - 1] * nums[i - 1];
        }
        
        // Multiply by suffix products
        int suffixProduct = 1;
        for (int i = n - 1; i >= 0; i--) {
            answer[i] *= suffixProduct;
            suffixProduct *= nums[i];
        }
        
        return answer;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Không tính mảng kết quả).

---

## 6. Range Sum Query 2D - Immutable (LeetCode 304)
**Đề bài chi tiết:** Tính tổng các phần tử trong hình chữ nhật từ $(row1, col1)$ đến $(row2, col2)$.
**Phân tích thuật toán:** Áp dụng công thức 2D Prefix Sum. Khởi tạo: $P[i+1][j+1] = A[i][j] + P[i][j+1] + P[i+1][j] - P[i][j]$. Truy vấn: $P[r2+1][c2+1] - P[r1][c2+1] - P[r2+1][c1] + P[r1][c1]$.
**Mã nguồn Java:**
```java
class NumMatrix {
    private int[][] prefix;

    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        prefix = new int[matrix.length + 1][matrix[0].length + 1];
        
        for (int r = 0; r < matrix.length; r++) {
            for (int c = 0; c < matrix[0].length; c++) {
                prefix[r + 1][c + 1] = matrix[r][c] 
                                     + prefix[r][c + 1] 
                                     + prefix[r + 1][c] 
                                     - prefix[r][c];
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return prefix[row2 + 1][col2 + 1] 
             - prefix[row1][col2 + 1] 
             - prefix[row2 + 1][col1] 
             + prefix[row1][col1];
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$ cho khởi tạo, $\mathcal{O}(1)$ cho truy vấn. Space $\mathcal{O}(M \times N)$.

---

## 7. Subarray Sums Divisible by K (LeetCode 974)
**Đề bài chi tiết:** Tìm số lượng mảng con liên tiếp có tổng chia hết cho $K$.
**Phân tích thuật toán:** Một mảng con từ $i$ đến $j$ có tổng chia hết cho $K$ khi và chỉ khi $P[j] \pmod K == P[i-1] \pmod K$. Duyệt mảng, tính `prefix % K`. Lưu số lần xuất hiện của mỗi số dư vào HashMap hoặc Array. Xử lý số dư âm: `(sum % K + K) % K`.
**Mã nguồn Java:**
```java
public class DivisibleByK {
    public int subarraysDivByK(int[] nums, int k) {
        int[] modCount = new int[k];
        modCount[0] = 1; // Base case
        int currentSum = 0;
        int count = 0;
        
        for (int num : nums) {
            currentSum += num;
            int mod = (currentSum % k + k) % k; // Handle negative modulo
            count += modCount[mod];
            modCount[mod]++;
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(K)$ (Dùng mảng tối ưu hơn HashMap).

---

## 8. Maximum Size Subarray Sum Equals k (LeetCode 325)
**Đề bài chi tiết:** Tìm chiều dài lớn nhất của mảng con liên tiếp có tổng bằng $K$.
**Phân tích thuật toán:** Tương tự bài 2, nhưng thay vì đếm số lượng, ta tìm `maxLength`. Trong HashMap, chỉ lưu vị trí ĐẦU TIÊN của một `prefixSum` (Không ghi đè nếu đã tồn tại). `maxLength = Math.max(maxLength, i - map.get(currentSum - k))`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class MaxSizeSubarrayK {
    public int maxSubArrayLen(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int currentSum = 0;
        int maxLength = 0;
        
        for (int i = 0; i < nums.length; i++) {
            currentSum += nums[i];
            if (map.containsKey(currentSum - k)) {
                maxLength = Math.max(maxLength, i - map.get(currentSum - k));
            }
            if (!map.containsKey(currentSum)) {
                map.put(currentSum, i);
            }
        }
        return maxLength;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 9. Find the Longest Substring Containing Vowels in Even Counts (LeetCode 1371)
**Đề bài chi tiết:** Cho chuỗi $s$. Tìm chuỗi con dài nhất mà tất cả 5 nguyên âm ('a','e','i','o','u') đều xuất hiện số lần CHẴN.
**Phân tích thuật toán:** Trạng thái chẵn/lẻ của 5 nguyên âm có thể biểu diễn bằng 5 bits (Bitmask) từ 0 đến 31. Dùng mảng `pos[32]` để lưu vị trí đầu tiên xuất hiện của một bitmask. Nếu gặp nguyên âm, lật bit tương ứng bằng phép XOR `^`. Nếu bitmask này đã xuất hiện trước đó, nghĩa là đoạn ở giữa có số nguyên âm bị lật chẵn lần.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class EvenVowels {
    public int findTheLongestSubstring(String s) {
        int[] pos = new int[32];
        Arrays.fill(pos, -2);
        pos[0] = -1;
        
        int mask = 0, maxLength = 0;
        String vowels = "aeiou";
        
        for (int i = 0; i < s.length(); i++) {
            int idx = vowels.indexOf(s.charAt(i));
            if (idx >= 0) mask ^= (1 << idx);
            
            if (pos[mask] != -2) {
                maxLength = Math.max(maxLength, i - pos[mask]);
            } else {
                pos[mask] = i;
            }
        }
        return maxLength;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (Vì mảng kích thước cố định 32).

---

## 10. Continuous Subarray Sum (LeetCode 523)
**Đề bài chi tiết:** Kiểm tra xem có tồn tại mảng con độ dài $\ge 2$ có tổng chia hết cho $K$ hay không. ($K > 0$).
**Phân tích thuật toán:** Prefix Sum + Modulo + HashMap. Để đảm bảo độ dài $\ge 2$, ta lưu `(mod, index)` vào map. Nếu mod đã tồn tại và `i - map.get(mod) >= 2` thì trả về `true`. Khởi tạo map với `(0, -1)`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class ContinuousSubarraySum {
    public boolean checkSubarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int currentSum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            currentSum += nums[i];
            int mod = currentSum % k;
            
            if (map.containsKey(mod)) {
                if (i - map.get(mod) >= 2) return true;
            } else {
                map.put(mod, i);
            }
        }
        return false;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(\min(N, K))$.

---

## 11. XOR Queries of a Subarray (LeetCode 1310)
**Đề bài chi tiết:** Cho mảng số nguyên dương `arr` và mảng các truy vấn `queries` trong đó `queries[i] = [left_i, right_i]`. Hãy tính giá trị XOR của các phần tử từ `left_i` đến `right_i` trong mảng `arr` và trả về mảng kết quả.
**Phân tích thuật toán:** Sử dụng Prefix XOR. Khởi tạo mảng `prefixXOR` sao cho `prefixXOR[i]` lưu giá trị XOR từ đầu mảng đến chỉ số `i-1`. Giá trị XOR trên khoảng `[L, R]` được tính bằng công thức: `prefixXOR[R + 1] ^ prefixXOR[L]`. Phép toán này hoạt động nhờ tính chất $A \oplus A = 0$.
**Mã nguồn Java:**
```java
public class XORQueries {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] prefixXOR = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            prefixXOR[i + 1] = prefixXOR[i] ^ arr[i];
        }
        
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int L = queries[i][0];
            int R = queries[i][1];
            ans[i] = prefixXOR[R + 1] ^ prefixXOR[L];
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Khởi tạo: Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. Mỗi truy vấn: Time $\mathcal{O}(1)$. Tổng thời gian $\mathcal{O}(N + Q)$.

---

## 12. Minimum Operations to Reduce X to Zero (LeetCode 1658)
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `x`. Mỗi thao tác, bạn có thể xóa phần tử đầu hoặc cuối của mảng và trừ giá trị đó vào `x`. Tìm số thao tác ít nhất để đưa `x` về đúng 0, nếu không thể thì trả về -1.
**Phân tích thuật toán:** Bài toán tương đương với việc tìm mảng con liên tiếp dài nhất (Longest Subarray) nằm ở "giữa" mảng sao cho tổng của nó bằng `Sum(nums) - x`. Ta dùng Two Pointers (Sliding Window) hoặc HashMap kết hợp Prefix Sum. Ở đây dùng Sliding Window vì mảng chỉ chứa số nguyên dương, tạo ra Prefix Sum tăng ngặt.
**Mã nguồn Java:**
```java
public class MinOperationsReduceX {
    public int minOperations(int[] nums, int x) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int target = totalSum - x;
        if (target < 0) return -1;
        if (target == 0) return nums.length;
        
        int maxLength = -1;
        int currentSum = 0;
        int left = 0;
        
        for (int right = 0; right < nums.length; right++) {
            currentSum += nums[right];
            
            while (currentSum > target && left <= right) {
                currentSum -= nums[left++];
            }
            
            if (currentSum == target) {
                maxLength = Math.max(maxLength, right - left + 1);
            }
        }
        
        return maxLength == -1 ? -1 : nums.length - maxLength;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 13. Make Sum Divisible by P (LeetCode 1590)
**Đề bài chi tiết:** Cho mảng `nums` gồm các số nguyên dương và số nguyên dương `p`. Tìm chiều dài nhỏ nhất của mảng con cần xóa đi sao cho tổng các phần tử còn lại chia hết cho `p`. Không được phép xóa toàn bộ mảng. Nếu không thể thì trả về -1.
**Phân tích thuật toán:** Tổng toàn bộ mảng là `total`. Nếu `total % p == 0`, trả về 0. Ngược lại, cần tìm mảng con ngắn nhất có tổng `S` sao cho `S % p == total % p = target`. Dùng Prefix Sum Modulo: ta tìm vị trí `j` gần nhất trước `i` thỏa mãn `(prefix[i] - prefix[j]) % p == target`, suy ra `prefix[j] % p == (prefix[i] - target + p) % p`. Dùng HashMap lưu vị trí cập nhật gần nhất của mỗi số dư.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class MakeSumDivisibleByP {
    public int minSubarray(int[] nums, int p) {
        int n = nums.length;
        long totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int target = (int)(totalSum % p);
        if (target == 0) return 0;
        
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        long currentSum = 0;
        int minLen = n;
        
        for (int i = 0; i < n; i++) {
            currentSum += nums[i];
            int currentMod = (int)(currentSum % p);
            int neededMod = (currentMod - target + p) % p;
            
            if (map.containsKey(neededMod)) {
                minLen = Math.min(minLen, i - map.get(neededMod));
            }
            
            map.put(currentMod, i);
        }
        
        return minLen == n ? -1 : minLen;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 14. Path Sum III (LeetCode 437)
**Đề bài chi tiết:** Cho cây nhị phân và một số nguyên `targetSum`. Trả về số lượng đường đi có tổng các giá trị trên đường đi bằng `targetSum`. Đường đi không nhất thiết bắt đầu từ gốc hay kết thúc ở lá, nhưng phải đi xuống (từ nút cha xuống nút con).
**Phân tích thuật toán:** Kết hợp Duyệt cây (DFS) và Prefix Sum. Quá trình đi từ gốc xuống các lá tương đương với một mảng. Dùng HashMap lưu tần suất của `currentSum`. Tại mỗi nút, nếu `currentSum - targetSum` đã tồn tại trong Map, cộng số lần đó vào kết quả. Khi quay lui (backtrack), cần giảm tần suất của `currentSum` trong Map để không ảnh hưởng đến các nhánh khác.
**Mã nguồn Java:**
```java
import java.util.HashMap;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class PathSumIII {
    public int pathSum(TreeNode root, int targetSum) {
        HashMap<Long, Integer> map = new HashMap<>();
        map.put(0L, 1);
        return dfs(root, 0L, targetSum, map);
    }
    
    private int dfs(TreeNode node, long currentSum, int targetSum, HashMap<Long, Integer> map) {
        if (node == null) return 0;
        
        currentSum += node.val;
        int count = map.getOrDefault(currentSum - targetSum, 0);
        
        map.put(currentSum, map.getOrDefault(currentSum, 0) + 1);
        
        count += dfs(node.left, currentSum, targetSum, map);
        count += dfs(node.right, currentSum, targetSum, map);
        
        map.put(currentSum, map.get(currentSum) - 1); // Backtrack
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$ (Duyệt qua $N$ nút 1 lần), Space $\mathcal{O}(H)$ (Với $H$ là chiều cao của cây, tương đương số nút trên một nhánh).

---

## 15. Number of Submatrices That Sum to Target (LeetCode 1074)
**Đề bài chi tiết:** Cho ma trận 2D `matrix` và số nguyên `target`. Tìm số lượng ma trận con (submatrix) có tổng các phần tử bằng `target`.
**Phân tích thuật toán:** Áp dụng ý tưởng "Subarray Sum Equals K" (1D Prefix Sum + HashMap) lên ma trận 2D. Ta tính Prefix Sum theo từng hàng. Sau đó, cố định hai cột `col1` và `col2`. Coi các giá trị tổng hàng từ `col1` đến `col2` như một mảng 1D, rồi áp dụng Hash Map để đếm số lượng mảng con 1D có tổng bằng `target`.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class SubmatricesSumTarget {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        // Compute row-wise prefix sum
        for (int r = 0; r < rows; r++) {
            for (int c = 1; c < cols; c++) {
                matrix[r][c] += matrix[r][c - 1];
            }
        }
        
        int count = 0;
        
        for (int c1 = 0; c1 < cols; c1++) {
            for (int c2 = c1; c2 < cols; c2++) {
                HashMap<Integer, Integer> map = new HashMap<>();
                map.put(0, 1);
                int currentSum = 0;
                
                for (int r = 0; r < rows; r++) {
                    int rowSum = matrix[r][c2] - (c1 > 0 ? matrix[r][c1 - 1] : 0);
                    currentSum += rowSum;
                    
                    if (map.containsKey(currentSum - target)) {
                        count += map.get(currentSum - target);
                    }
                    map.put(currentSum, map.getOrDefault(currentSum, 0) + 1);
                }
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(R \times C^2)$, Space $\mathcal{O}(R)$.

---

## 16. Largest Magic Square (LeetCode 1895)
**Đề bài chi tiết:** Cho ma trận `grid`, ma trận vuông con được gọi là "Magic Square" nếu tổng các phần tử trên mọi hàng, mọi cột và hai đường chéo đều bằng nhau. Tìm kích thước của Magic Square lớn nhất có thể. (Magic square không cần phải chứa các số từ $1$ đến $k^2$).
**Phân tích thuật toán:** Dùng Prefix Sum để tính tổng nhanh cho hàng và cột (2 mảng 2D riêng biệt hoặc tính gộp). Xét mọi kích thước `k` từ $\min(M, N)$ giảm dần xuống 1. Với mỗi `k`, duyệt qua mọi ma trận vuông con kích thước `k x k`. Kiểm tra xem tổng tất cả các hàng, cột và 2 đường chéo có bằng nhau không. Vì `k` giảm dần, ma trận hợp lệ đầu tiên chính là kết quả lớn nhất.
**Mã nguồn Java:**
```java
public class LargestMagicSquare {
    public int largestMagicSquare(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] rowSum = new int[m][n + 1];
        int[][] colSum = new int[m + 1][n];
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                rowSum[i][j + 1] = rowSum[i][j] + grid[i][j];
                colSum[i + 1][j] = colSum[i][j] + grid[i][j];
            }
        }
        
        for (int k = Math.min(m, n); k > 1; k--) {
            for (int i = 0; i <= m - k; i++) {
                for (int j = 0; j <= n - k; j++) {
                    if (isMagic(grid, rowSum, colSum, i, j, k)) return k;
                }
            }
        }
        return 1;
    }
    
    private boolean isMagic(int[][] grid, int[][] rowSum, int[][] colSum, int r, int c, int k) {
        int target = rowSum[r][c + k] - rowSum[r][c];
        
        for (int i = 0; i < k; i++) {
            if (rowSum[r + i][c + k] - rowSum[r + i][c] != target) return false;
            if (colSum[r + k][c + i] - colSum[r][c + i] != target) return false;
        }
        
        int diag1 = 0, diag2 = 0;
        for (int i = 0; i < k; i++) {
            diag1 += grid[r + i][c + i];
            diag2 += grid[r + i][c + k - 1 - i];
        }
        
        return diag1 == target && diag2 == target;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N \times \min(M, N))$, Space $\mathcal{O}(M \times N)$.

---

## 17. Matrix Block Sum (LeetCode 1314)
**Đề bài chi tiết:** Cho ma trận `mat` kích thước `m x n` và số nguyên `k`. Trả về ma trận `answer` trong đó `answer[i][j]` là tổng các phần tử `mat[r][c]` với `i - k <= r <= i + k` và `j - k <= c <= j + k`. Giới hạn chỉ số nằm gọn trong ma trận.
**Phân tích thuật toán:** Đây là một bài thực hành trực tiếp 2D Prefix Sum. Khởi tạo mảng Prefix 2D. Với mỗi ô `(i, j)`, hình chữ nhật bao quanh sẽ có tọa độ từ góc trên bên trái `(max(0, i-k), max(0, j-k))` đến góc dưới bên phải `(min(m-1, i+k), min(n-1, j+k))`. Truy vấn tổng bằng $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
public class MatrixBlockSum {
    public int[][] matrixBlockSum(int[][] mat, int k) {
        int m = mat.length;
        int n = mat[0].length;
        int[][] prefix = new int[m + 1][n + 1];
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                prefix[i + 1][j + 1] = mat[i][j] 
                                     + prefix[i][j + 1] 
                                     + prefix[i + 1][j] 
                                     - prefix[i][j];
            }
        }
        
        int[][] ans = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int r1 = Math.max(0, i - k);
                int c1 = Math.max(0, j - k);
                int r2 = Math.min(m - 1, i + k);
                int c2 = Math.min(n - 1, j + k);
                
                ans[i][j] = prefix[r2 + 1][c2 + 1] 
                          - prefix[r1][c2 + 1] 
                          - prefix[r2 + 1][c1] 
                          + prefix[r1][c1];
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(M \times N)$, Space $\mathcal{O}(M \times N)$.

---

## 18. Vowels of All Substrings (LeetCode 2063)
**Đề bài chi tiết:** Cho chuỗi `word`. Trả về tổng số nguyên âm xuất hiện trong tất cả các chuỗi con của `word`. Nguyên âm là 'a', 'e', 'i', 'o', 'u'.
**Phân tích thuật toán:** Dù thuộc chương Prefix Sum, nhưng đây là bài toán Tổ hợp. Ta đếm số lượng chuỗi con chứa ký tự thứ `i`. Một chuỗi con phải bắt đầu từ chỉ số `start` (trong đoạn `[0, i]`, có `i + 1` lựa chọn) và kết thúc tại chỉ số `end` (trong đoạn `[i, n-1]`, có `n - i` lựa chọn). Nếu `word[i]` là nguyên âm, nó sẽ đóng góp `(i + 1) * (n - i)` lần vào tổng kết quả.
**Mã nguồn Java:**
```java
public class VowelsSubstrings {
    public long countVowels(String word) {
        long totalVowels = 0;
        int n = word.length();
        
        for (int i = 0; i < n; i++) {
            char c = word.charAt(i);
            if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u') {
                totalVowels += (long) (i + 1) * (n - i);
            }
        }
        
        return totalVowels;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 19. Corporate Flight Bookings (LeetCode 1109)
**Đề bài chi tiết:** Có `n` chuyến bay đánh số từ 1 đến `n`. Cho mảng `bookings` trong đó `bookings[i] = [first_i, last_i, seats_i]` nghĩa là đặt `seats_i` ghế trên mỗi chuyến bay từ `first_i` đến `last_i`. Trả về mảng `answer` là tổng số ghế được đặt trên từng chuyến bay.
**Phân tích thuật toán:** Áp dụng Mảng sai phân (Difference Array). Đây là dạng nghịch đảo của Prefix Sum. Thay vì cộng `seats` cho từng chuyến bay từ `L` đến `R`, ta cập nhật `diff[L] += seats` và `diff[R + 1] -= seats`. Sau đó, `answer[i] = answer[i-1] + diff[i]`. Điều này giảm Time Update từ $\mathcal{O}(K)$ thành $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
public class FlightBookings {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] diff = new int[n + 2];
        
        for (int[] booking : bookings) {
            int first = booking[0];
            int last = booking[1];
            int seats = booking[2];
            
            diff[first] += seats;
            diff[last + 1] -= seats;
        }
        
        int[] ans = new int[n];
        int currentSeats = 0;
        for (int i = 1; i <= n; i++) {
            currentSeats += diff[i];
            ans[i - 1] = currentSeats;
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(B + N)$ (với $B$ là số bookings), Space $\mathcal{O}(N)$.

---

## 20. Count Vowel Strings in Ranges (LeetCode 2559)
**Đề bài chi tiết:** Cho mảng chuỗi `words` và mảng truy vấn `queries` `[L, R]`. Một chuỗi hợp lệ nếu nó bắt đầu và kết thúc đều bằng nguyên âm. Đếm số lượng chuỗi hợp lệ nằm trong khoảng từ chỉ số `L` đến `R`.
**Phân tích thuật toán:** Bước 1: Tạo một mảng nhị phân `isValid` cùng độ dài với `words`, `isValid[i] = 1` nếu `words[i]` hợp lệ, ngược lại là `0`. Bước 2: Tạo Prefix Sum trên mảng `isValid` để trả lời nhanh mọi truy vấn đếm trong đoạn `[L, R]`.
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Arrays;

public class VowelStringsRanges {
    public int[] vowelStrings(String[] words, int[][] queries) {
        HashSet<Character> vowels = new HashSet<>(Arrays.asList('a', 'e', 'i', 'o', 'u'));
        int n = words.length;
        int[] prefix = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            String w = words[i];
            if (vowels.contains(w.charAt(0)) && vowels.contains(w.charAt(w.length() - 1))) {
                prefix[i + 1] = prefix[i] + 1;
            } else {
                prefix[i + 1] = prefix[i];
            }
        }
        
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int L = queries[i][0];
            int R = queries[i][1];
            ans[i] = prefix[R + 1] - prefix[L];
        }
        
        return ans;
    }
}
```
**Độ phức tạp:** Khởi tạo: Time $\mathcal{O}(N \times \text{length})$. Truy vấn: $\mathcal{O}(1)$ cho mỗi query. Tổng Time $\mathcal{O}(N + Q)$. Space $\mathcal{O}(N)$.

---

## 21. Plates Between Candles (LeetCode 2055)
**Đề bài chi tiết:** Cho một chuỗi `s` bao gồm các ký tự `*` (đĩa) và `|` (nến). Bạn cũng được cho mảng các truy vấn `queries`, trong đó `queries[i] = [left_i, right_i]`. Đối với mỗi truy vấn, trả về số lượng đĩa nằm giữa hai cây nến bất kỳ trong khoảng `[left_i, right_i]`.
**Phân tích thuật toán:** Sử dụng 3 mảng: `prefix` (đếm số lượng đĩa từ đầu đến `i`), `leftCandle` (lưu vị trí nến gần nhất bên trái `i`), và `rightCandle` (lưu vị trí nến gần nhất bên phải `i`). Truy vấn `[L, R]` được tính bằng `prefix[rightCandle[R]] - prefix[leftCandle[L]]` (với điều kiện nến trái nằm trước nến phải).
**Mã nguồn Java:**
```java
public class PlatesBetweenCandles {
    public int[] platesBetweenCandles(String s, int[][] queries) {
        int n = s.length();
        int[] prefix = new int[n];
        int[] leftCandle = new int[n];
        int[] rightCandle = new int[n];
        
        int plates = 0;
        int candlePos = -1;
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) == '*') plates++;
            else candlePos = i;
            prefix[i] = plates;
            leftCandle[i] = candlePos;
        }
        
        candlePos = -1;
        for (int i = n - 1; i >= 0; i--) {
            if (s.charAt(i) == '|') candlePos = i;
            rightCandle[i] = candlePos;
        }
        
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int l = rightCandle[queries[i][0]];
            int r = leftCandle[queries[i][1]];
            if (l != -1 && r != -1 && l < r) {
                ans[i] = prefix[r] - prefix[l];
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Khởi tạo: Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$. Mỗi truy vấn: Time $\mathcal{O}(1)$.

---

## 22. Longest Well-Performing Interval (LeetCode 1124)
**Đề bài chi tiết:** Cho mảng `hours` biểu diễn số giờ làm việc mỗi ngày. Một ngày làm việc mệt mỏi là khi `hours[i] > 8`. Một khoảng làm việc (interval) được gọi là "tốt" (well-performing) nếu số ngày mệt mỏi lớn hơn số ngày bình thường. Tìm độ dài của khoảng làm việc tốt dài nhất.
**Phân tích thuật toán:** Quy đổi bài toán: nếu `hours[i] > 8` thì gán bằng $1$, ngược lại bằng $-1$. Bài toán trở thành tìm khoảng có tổng $> 0$ dài nhất. Sử dụng Prefix Sum và HashMap lưu lại chỉ số đầu tiên mà mỗi giá trị tổng xuất hiện. Nếu `prefixSum > 0`, độ dài là `i + 1`. Nếu `prefixSum <= 0`, ta tìm xem `prefixSum - 1` đã xuất hiện ở đâu.
**Mã nguồn Java:**
```java
import java.util.HashMap;

public class LongestWPI {
    public int longestWPI(int[] hours) {
        int maxLength = 0;
        int prefixSum = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < hours.length; i++) {
            prefixSum += (hours[i] > 8) ? 1 : -1;
            
            if (prefixSum > 0) {
                maxLength = i + 1;
            } else {
                if (!map.containsKey(prefixSum)) {
                    map.put(prefixSum, i);
                }
                if (map.containsKey(prefixSum - 1)) {
                    maxLength = Math.max(maxLength, i - map.get(prefixSum - 1));
                }
            }
        }
        return maxLength;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 23. Ways to Split Array Into Three Subarrays (LeetCode 1712)
**Đề bài chi tiết:** Cho mảng các số nguyên không âm `nums`. Chia mảng thành 3 mảng con không rỗng liên tiếp (`left`, `mid`, `right`) sao cho `sum(left) <= sum(mid) <= sum(right)`. Tìm số cách chia thỏa mãn (modulo $10^9 + 7$).
**Phân tích thuật toán:** Sử dụng Prefix Sum và Two Pointers (hoặc Binary Search). Duyệt qua điểm cắt thứ nhất `i` (chia `left` và `mid`). Với mỗi `i`, tìm giới hạn dưới `j` (điểm cắt thứ hai) sao cho `sum(mid) >= sum(left)` và giới hạn trên `k` sao cho `sum(right) >= sum(mid)`. Hai con trỏ `j` và `k` sẽ di chuyển tiến lên do mảng chỉ chứa số không âm.
**Mã nguồn Java:**
```java
public class WaysToSplitArray {
    public int waysToSplit(int[] nums) {
        int n = nums.length;
        int[] prefix = new int[n];
        prefix[0] = nums[0];
        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] + nums[i];
        }
        
        long ans = 0;
        int MOD = 1000000007;
        int j = 1, k = 1;
        
        for (int i = 0; i < n - 2; i++) {
            int leftSum = prefix[i];
            
            j = Math.max(j, i + 1);
            while (j < n - 1 && prefix[j] - leftSum < leftSum) {
                j++;
            }
            
            k = Math.max(k, j);
            while (k < n - 1 && prefix[n - 1] - prefix[k] >= prefix[k] - leftSum) {
                k++;
            }
            
            if (j < k) {
                ans = (ans + k - j) % MOD;
            }
        }
        
        return (int) ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 24. Sum of Absolute Differences in a Sorted Array (LeetCode 1685)
**Đề bài chi tiết:** Cho mảng số nguyên dương `nums` đã được sắp xếp tăng dần. Trả về mảng `result` sao cho `result[i]` bằng tổng trị tuyệt đối của sự chênh lệch giữa `nums[i]` và tất cả các phần tử khác trong mảng.
**Phân tích thuật toán:** Vì mảng đã sắp xếp, `nums[j] <= nums[i]` với $j < i$ và `nums[j] >= nums[i]` với $j > i$. Ta dùng Prefix Sum để tính tổng các đoạn. Tổng chênh lệch tại $i$ bằng: `(nums[i] * i - prefixSum[i-1]) + (suffixSum[i+1] - nums[i] * (n - i - 1))`. Có thể tối ưu bộ nhớ bằng cách chỉ duy trì 1 biến tổng bên trái thay vì mảng prefix.
**Mã nguồn Java:**
```java
public class SumOfAbsoluteDifferences {
    public int[] getSumAbsoluteDifferences(int[] nums) {
        int n = nums.length;
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int[] result = new int[n];
        int leftSum = 0;
        
        for (int i = 0; i < n; i++) {
            int rightSum = totalSum - leftSum - nums[i];
            result[i] = (nums[i] * i - leftSum) + (rightSum - nums[i] * (n - i - 1));
            leftSum += nums[i];
        }
        
        return result;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$ (không tính mảng kết quả).

---

## 25. Maximum Score of a Good Subarray (LeetCode 1793)
**Đề bài chi tiết:** Cho mảng `nums` và số nguyên `k`. Một mảng con `[i, j]` là "tốt" nếu $i \le k \le j$. Điểm số được tính bằng `min(nums[i..j]) * (j - i + 1)`. Tìm điểm số lớn nhất của mảng con tốt có thể có.
**Phân tích thuật toán:** Sử dụng Two Pointers (hoặc Greedy). Bắt đầu với $i = k$ và $j = k$. Liên tục mở rộng cửa sổ về phía bên nào có giá trị liền kề lớn hơn (chọn max giữa `nums[i-1]` và `nums[j+1]`). Trong quá trình mở rộng, cập nhật giá trị Min hiện tại và tính điểm số.
**Mã nguồn Java:**
```java
public class MaxScoreGoodSubarray {
    public int maximumScore(int[] nums, int k) {
        int n = nums.length;
        int i = k, j = k;
        int minVal = nums[k];
        int maxScore = nums[k];
        
        while (i > 0 || j < n - 1) {
            if (i == 0) {
                j++;
            } else if (j == n - 1) {
                i--;
            } else if (nums[i - 1] > nums[j + 1]) {
                i--;
            } else {
                j++;
            }
            
            minVal = Math.min(minVal, Math.min(nums[i], nums[j]));
            maxScore = Math.max(maxScore, minVal * (j - i + 1));
        }
        
        return maxScore;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 26. Find the Middle Index in Array (LeetCode 1991)
**Đề bài chi tiết:** Tìm chỉ số giữa (`middle index`) của mảng `nums`. Đây là chỉ số mà tổng các phần tử bên trái nó bằng tổng các phần tử bên phải nó. Trả về chỉ số nhỏ nhất nếu có nhiều, không có trả về -1. (Giống hệt bài Pivot Index).
**Phân tích thuật toán:** Tính tổng toàn mảng trước. Duyệt mảng và duy trì tổng bên trái `leftSum`. Tổng bên phải tại $i$ sẽ là `totalSum - leftSum - nums[i]`. So sánh và trả về $i$ sớm nhất nếu hai tổng bằng nhau.
**Mã nguồn Java:**
```java
public class MiddleIndex {
    public int findMiddleIndex(int[] nums) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        
        int leftSum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (leftSum == totalSum - leftSum - nums[i]) {
                return i;
            }
            leftSum += nums[i];
        }
        
        return -1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 27. Max Chunks To Make Sorted II (LeetCode 768)
**Đề bài chi tiết:** Cho mảng `arr` có thể chứa các giá trị trùng lặp. Chia mảng thành nhiều mảng con liền kề ("chunks") nhất có thể, sao cho khi sắp xếp từng chunk riêng rẽ và nối chúng lại, ta được một mảng sắp xếp hoàn chỉnh. Trả về số chunk tối đa.
**Phân tích thuật toán:** Sử dụng mảng Prefix Max và Suffix Min. Một đoạn từ $0$ đến $i$ có thể được chia thành một tập các chunk độc lập nếu giá trị lớn nhất trong đoạn $0..i$ nhỏ hơn hoặc bằng giá trị nhỏ nhất của tất cả phần tử bên phải đoạn đó. Do đó, nếu `prefixMax[i] <= suffixMin[i + 1]`, ta có thể kết thúc một chunk tại $i$.
**Mã nguồn Java:**
```java
public class MaxChunksII {
    public int maxChunksToSorted(int[] arr) {
        int n = arr.length;
        int[] suffixMin = new int[n];
        
        suffixMin[n - 1] = arr[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            suffixMin[i] = Math.min(suffixMin[i + 1], arr[i]);
        }
        
        int chunks = 0;
        int maxLeft = 0;
        
        for (int i = 0; i < n - 1; i++) {
            maxLeft = Math.max(maxLeft, arr[i]);
            if (maxLeft <= suffixMin[i + 1]) {
                chunks++;
            }
        }
        
        return chunks + 1;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(N)$.

---

## 28. Grid Illumination (LeetCode 1001)
**Đề bài chi tiết:** Cho một lưới $N \times N$, và mảng vị trí các bóng đèn `lamps`. Mỗi bóng đèn chiếu sáng hàng, cột và 2 đường chéo đi qua nó. Các truy vấn yêu cầu kiểm tra xem ô $q$ có được chiếu sáng không. Sau mỗi truy vấn, các bóng đèn nằm trong ô $q$ và 8 ô kề cạnh đều bị tắt. Trả về mảng 1/0 tương ứng với kết quả chiếu sáng.
**Phân tích thuật toán:** Lưu vị trí bóng đèn bằng HashMap/HashSet. Dùng 4 HashMap lưu số lượng bóng đèn soi sáng cho mỗi hàng, cột, đường chéo 1 ($r - c$) và đường chéo 2 ($r + c$). Khi tắt đèn, cập nhật 4 HashMap này tương ứng. Truy vấn chỉ tốn $\mathcal{O}(1)$.
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.HashSet;

public class GridIllumination {
    public int[] gridIllumination(int n, int[][] lamps, int[][] queries) {
        HashMap<Integer, Integer> row = new HashMap<>();
        HashMap<Integer, Integer> col = new HashMap<>();
        HashMap<Integer, Integer> diag1 = new HashMap<>();
        HashMap<Integer, Integer> diag2 = new HashMap<>();
        HashSet<Long> activeLamps = new HashSet<>();
        
        for (int[] lamp : lamps) {
            int r = lamp[0], c = lamp[1];
            long key = (long) r * n + c;
            if (!activeLamps.contains(key)) {
                activeLamps.add(key);
                row.put(r, row.getOrDefault(r, 0) + 1);
                col.put(c, col.getOrDefault(c, 0) + 1);
                diag1.put(r - c, diag1.getOrDefault(r - c, 0) + 1);
                diag2.put(r + c, diag2.getOrDefault(r + c, 0) + 1);
            }
        }
        
        int[] ans = new int[queries.length];
        int[][] dirs = {{-1,-1}, {-1,0}, {-1,1}, {0,-1}, {0,0}, {0,1}, {1,-1}, {1,0}, {1,1}};
        
        for (int i = 0; i < queries.length; i++) {
            int r = queries[i][0], c = queries[i][1];
            if (row.getOrDefault(r, 0) > 0 || col.getOrDefault(c, 0) > 0 || 
                diag1.getOrDefault(r - c, 0) > 0 || diag2.getOrDefault(r + c, 0) > 0) {
                ans[i] = 1;
            }
            
            for (int[] d : dirs) {
                int nr = r + d[0], nc = c + d[1];
                long key = (long) nr * n + nc;
                if (activeLamps.remove(key)) {
                    row.put(nr, row.get(nr) - 1);
                    col.put(nc, col.get(nc) - 1);
                    diag1.put(nr - nc, diag1.get(nr - nc) - 1);
                    diag2.put(nr + nc, diag2.get(nr + nc) - 1);
                }
            }
        }
        return ans;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(L + Q)$ với $L$ là số bóng đèn, Space $\mathcal{O}(L)$.

---

## 29. Number of Ways to Split Array (LeetCode 2270)
**Đề bài chi tiết:** Cho mảng số nguyên `nums` có kích thước $n$. Tìm số cách chia mảng thành 2 phần bằng 1 đường cắt tại $i$ (với $0 \le i < n-1$) sao cho tổng bên trái $\ge$ tổng bên phải.
**Phân tích thuật toán:** Tính tổng toàn mảng $S$. Duyệt qua mảng và cộng dồn phần tử vào $leftSum$. Kiểm tra xem $leftSum \ge S - leftSum$. Lưu ý sử dụng kiểu `long` cho biến tổng để tránh tràn số.
**Mã nguồn Java:**
```java
public class WaysToSplitArray2 {
    public int waysToSplitArray(int[] nums) {
        long totalSum = 0;
        for (int num : nums) totalSum += num;
        
        long leftSum = 0;
        int count = 0;
        
        for (int i = 0; i < nums.length - 1; i++) {
            leftSum += nums[i];
            long rightSum = totalSum - leftSum;
            if (leftSum >= rightSum) {
                count++;
            }
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N)$, Space $\mathcal{O}(1)$.

---

## 30. Continuous Subarrays (LeetCode 2762)
**Đề bài chi tiết:** Cho mảng số nguyên `nums`. Một mảng con được gọi là "liên tục" (continuous) nếu chênh lệch giữa phần tử lớn nhất và nhỏ nhất trong mảng con đó không vượt quá 2. Tính số lượng các mảng con liên tục.
**Phân tích thuật toán:** Sử dụng Sliding Window kết hợp với `TreeMap` hoặc hai `Deque` để duy trì giá trị lớn nhất và nhỏ nhất trong cửa sổ. Khi độ chênh lệch giữa Max và Min $> 2$, ta tịnh tiến con trỏ trái và loại bỏ giá trị ra khỏi `TreeMap`. Số mảng con kết thúc tại chỉ số `right` là `right - left + 1`.
**Mã nguồn Java:**
```java
import java.util.TreeMap;

public class ContinuousSubarrays {
    public long continuousSubarrays(int[] nums) {
        int n = nums.length;
        long count = 0;
        int left = 0;
        TreeMap<Integer, Integer> map = new TreeMap<>();
        
        for (int right = 0; right < n; right++) {
            map.put(nums[right], map.getOrDefault(nums[right], 0) + 1);
            
            while (map.lastKey() - map.firstKey() > 2) {
                int countLeft = map.get(nums[left]);
                if (countLeft == 1) {
                    map.remove(nums[left]);
                } else {
                    map.put(nums[left], countLeft - 1);
                }
                left++;
            }
            
            count += (right - left + 1);
        }
        
        return count;
    }
}
```
**Độ phức tạp:** Time $\mathcal{O}(N \log N)$ (có thể tối ưu thành $\mathcal{O}(N)$ bằng `Deque`), Space $\mathcal{O}(1)$ (vì TreeMap chỉ lưu tối đa 4 giá trị đồng thời do độ lệch tối đa là 2).

