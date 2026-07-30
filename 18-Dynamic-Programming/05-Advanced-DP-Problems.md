# Bài tập Quy hoạch động nâng cao (Advanced DP Problems)

## 1. Burst Balloons (LeetCode 312)
**Đề bài chi tiết:** Cho `n` quả bóng bay, được đánh số từ `0` đến `n - 1`. Mỗi quả bóng có một số xu liên kết `nums[i]`. Nếu làm nổ bóng `i`, bạn nhận được `nums[i - 1] * nums[i] * nums[i + 1]` xu. Tìm số xu lớn nhất có thể thu được. (Thêm 1 vào đầu và cuối giả lập biên).

**Phân tích thuật toán:**
Sử dụng Interval DP. Thay vì chọn quả bóng nổ đầu tiên, hãy chọn quả bóng nổ **cuối cùng** trong đoạn `[i, j]`. Cọi `k` là quả bóng cuối cùng nổ, ta chia thành bài toán con `[i, k-1]` và `[k+1, j]`.
- Trạng thái `dp[i][j]`: Số xu lớn nhất nổ bóng trong khoảng `(i, j)`.
- Công thức: `dp[i][j] = max(dp[i][k] + dp[k][j] + nums[i] * nums[k] * nums[j])` với `i < k < j`.

**Mã nguồn Java:**
```java
public class BurstBalloons {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int[] arr = new int[n + 2];
        arr[0] = 1;
        arr[n + 1] = 1;
        for (int i = 0; i < n; i++) arr[i + 1] = nums[i];

        int[][] dp = new int[n + 2][n + 2];
        // length của khoảng (i, j) = j - i
        for (int len = 2; len <= n + 1; len++) {
            for (int i = 0; i <= n + 1 - len; i++) {
                int j = i + len;
                for (int k = i + 1; k < j; k++) {
                    dp[i][j] = Math.max(dp[i][j], 
                        dp[i][k] + dp[k][j] + arr[i] * arr[k] * arr[j]);
                }
            }
        }
        return dp[0][n + 1];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3), Không gian O(N^2).

---

## 2. Minimum Cost to Cut a Stick (LeetCode 1547)
**Đề bài chi tiết:** Cho một thanh gỗ dài `n` và mảng các điểm cắt `cuts`. Chi phí của một lần cắt bằng chiều dài phần gỗ đang cắt. Tìm chi phí tối thiểu để cắt hết mảng.

**Phân tích thuật toán:**
Giống Burst Balloons, sắp xếp các vết cắt, thêm `0` và `n` vào 2 đầu làm mốc. Dùng Interval DP với `dp[i][j]` là chi phí nhỏ nhất để hoàn thành các vết cắt từ chỉ số `i` đến `j`.

**Mã nguồn Java:**
```java
import java.util.*;

public class MinimumCostCutStick {
    public int minCost(int n, int[] cuts) {
        List<Integer> c = new ArrayList<>();
        for (int cut : cuts) c.add(cut);
        c.add(0);
        c.add(n);
        Collections.sort(c);
        
        int m = c.size();
        int[][] dp = new int[m][m];
        
        for (int len = 2; len < m; len++) {
            for (int i = 0; i < m - len; i++) {
                int j = i + len;
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i + 1; k < j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j] + c.get(j) - c.get(i));
                }
            }
        }
        return dp[0][m - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(M^3) với M là số vết cắt, Không gian O(M^2).

---

## 3. Shortest Path Visiting All Nodes (LeetCode 847)
**Đề bài chi tiết:** Cho đồ thị vô hướng. Tìm độ dài đường đi ngắn nhất thăm qua toàn bộ các đỉnh. Bạn có thể thăm đỉnh và cạnh nhiều lần.

**Phân tích thuật toán:**
Áp dụng Bitmask + BFS/DP. Trạng thái gồm `(u, mask)`: đang đứng tại `u` và đã thăm danh sách các đỉnh được mã hóa bởi `mask`. Do cạnh không trọng số, dùng BFS là tối ưu nhất.

**Mã nguồn Java:**
```java
import java.util.*;

public class ShortestPathVisitingAllNodes {
    public int shortestPathLength(int[][] graph) {
        int n = graph.length;
        if (n == 1) return 0;
        
        Queue<int[]> queue = new LinkedList<>();
        boolean[][] visited = new boolean[n][1 << n];
        
        for (int i = 0; i < n; i++) {
            queue.add(new int[]{i, 1 << i});
            visited[i][1 << i] = true;
        }
        
        int steps = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                int node = curr[0];
                int mask = curr[1];
                
                if (mask == (1 << n) - 1) return steps;
                
                for (int neighbor : graph[node]) {
                    int nextMask = mask | (1 << neighbor);
                    if (!visited[neighbor][nextMask]) {
                        visited[neighbor][nextMask] = true;
                        queue.add(new int[]{neighbor, nextMask});
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```
**Độ phức tạp:** Thời gian O(N * 2^N), Không gian O(N * 2^N).

---

## 4. Maximum Students Taking Exam (LeetCode 1349)
**Đề bài chi tiết:** Cho lưới `m x n` biểu diễn phòng thi. Một số chỗ có thể ngồi, nhưng không được xếp sinh viên ngồi kề nhau theo hàng ngang hoặc đường chéo. Tìm số lượng sinh viên lớn nhất có thể xếp.

**Phân tích thuật toán:**
Bitmask DP trên dòng. Trạng thái `dp[i][mask]` là số học sinh tối đa xếp tới dòng `i` với cách bố trí mặt nạ `mask`. Kiểm tra tính hợp lệ của `mask` và sự tương thích với dòng `mask` trước đó.

**Mã nguồn Java:**
```java
public class MaxStudentsExam {
    public int maxStudents(char[][] seats) {
        int m = seats.length;
        int n = seats[0].length;
        int[] validRows = new int[m];
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (seats[i][j] == '.') {
                    validRows[i] |= (1 << j);
                }
            }
        }
        
        int[][] dp = new int[m + 1][1 << n];
        for (int i = 1; i <= m; i++) {
            int valid = validRows[i - 1];
            for (int mask = 0; mask < (1 << n); mask++) {
                if ((mask & valid) == mask && (mask & (mask >> 1)) == 0) {
                    for (int prevMask = 0; prevMask < (1 << n); prevMask++) {
                        if ((mask & (prevMask >> 1)) == 0 && ((mask >> 1) & prevMask) == 0) {
                            dp[i][mask] = Math.max(dp[i][mask], dp[i - 1][prevMask] + Integer.bitCount(mask));
                        }
                    }
                }
            }
        }
        
        int max = 0;
        for (int val : dp[m]) max = Math.max(max, val);
        return max;
    }
}
```
**Độ phức tạp:** Thời gian O(M * 4^N), Không gian O(M * 2^N).

---

## 5. Count Numbers with Unique Digits (LeetCode 357)
**Đề bài chi tiết:** Cho số nguyên `n`, đếm tổng số lượng chữ số độc nhất (không trùng lặp) trong khoảng `0 <= x < 10^n`.

**Phân tích thuật toán:**
Có thể giải bằng tổ hợp học hoặc Digit DP. Bài toán tương đối đơn giản: Chữ số đầu tiên có 9 lựa chọn, các chữ số sau giảm dần.

**Mã nguồn Java:**
```java
public class CountUniqueDigits {
    public int countNumbersWithUniqueDigits(int n) {
        if (n == 0) return 1;
        int res = 10;
        int uniqueDigits = 9;
        int availableNumber = 9;
        while (n > 1 && availableNumber > 0) {
            uniqueDigits = uniqueDigits * availableNumber;
            res += uniqueDigits;
            availableNumber--;
            n--;
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 6. Numbers At Most N Given Digit Set (LeetCode 902)
**Đề bài chi tiết:** Cho mảng các chữ số `digits` và số `n`. Hỏi có thể tạo được bao nhiêu số nguyên dương nhỏ hơn hoặc bằng `n`.

**Phân tích thuật toán:**
Sử dụng Digit DP tiêu chuẩn hoặc phương pháp đếm toán học. Đếm độ dài ngắn hơn trước, sau đó duyệt độ dài bằng số chữ số của N theo chiều từ trái qua phải.

**Mã nguồn Java:**
```java
public class AtMostNGivenDigitSet {
    public int atMostNGivenDigitSet(String[] digits, int n) {
        String S = String.valueOf(n);
        int K = S.length();
        int[] dp = new int[K + 1];
        dp[K] = 1;
        
        for (int i = K - 1; i >= 0; i--) {
            int si = S.charAt(i) - '0';
            for (String d : digits) {
                if (Integer.parseInt(d) < si) {
                    dp[i] += Math.pow(digits.length, K - i - 1);
                } else if (Integer.parseInt(d) == si) {
                    dp[i] += dp[i + 1];
                }
            }
        }
        
        for (int i = 1; i < K; i++) {
            dp[0] += Math.pow(digits.length, i);
        }
        return dp[0];
    }
}
```
**Độ phức tạp:** Thời gian O(K * D) với K là độ dài `n`, D là kích thước `digits`, Không gian O(K).

---

## 7. House Robber III (Tree DP) (LeetCode 337)
**Đề bài chi tiết:** Cho một cây nhị phân mô phỏng nhà. Không được cướp 2 nhà (2 node) liên kết trực tiếp với nhau. Tìm số tiền lớn nhất có thể cướp.

**Phân tích thuật toán:**
Tree DP cơ bản (In-DP). Với mỗi node, ta có trạng thái `(rob, not_rob)`. Giá trị khi chọn node hiện tại sẽ bằng node hiện tại cộng với con không chọn.

**Mã nguồn Java:**
```java
class TreeNode {
    int val; TreeNode left, right;
    TreeNode(int x) { val = x; }
}
public class HouseRobberIII {
    public int rob(TreeNode root) {
        int[] res = dfs(root);
        return Math.max(res[0], res[1]);
    }
    
    // index 0: not rob, index 1: rob
    private int[] dfs(TreeNode node) {
        if (node == null) return new int[]{0, 0};
        
        int[] left = dfs(node.left);
        int[] right = dfs(node.right);
        
        int[] res = new int[2];
        res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
        res[1] = node.val + left[0] + right[0];
        
        return res;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H) với H là độ sâu cây.

---

## 8. Binary Tree Cameras (LeetCode 968)
**Đề bài chi tiết:** Cần đặt tối thiểu camera vào các node trên cây nhị phân sao cho mọi node đều được quan sát. Mỗi camera có thể nhìn chính nó, node cha và các node con trực tiếp.

**Phân tích thuật toán:**
Tree DP (Trạng thái Node). Phân thành 3 trạng thái: 0: Cần được bao phủ, 1: Đã được bao phủ (nhưng không có camera), 2: Chứa camera. Trả về từ dưới lên (Bottom-up).

**Mã nguồn Java:**
```java
public class BinaryTreeCameras {
    private int cameras = 0;
    public int minCameraCover(TreeNode root) {
        if (dfs(root) == 0) cameras++;
        return cameras;
    }
    
    private int dfs(TreeNode node) {
        if (node == null) return 1;
        
        int left = dfs(node.left);
        int right = dfs(node.right);
        
        if (left == 0 || right == 0) {
            cameras++;
            return 2;
        }
        if (left == 2 || right == 2) return 1;
        
        return 0;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(H).

---

## 9. Matrix Chain Multiplication (Concept)
**Đề bài chi tiết:** Cho chuỗi `n` ma trận, tìm cách nhóm phép nhân với dấu ngoặc đơn để đạt số lượng phép nhân vô hướng tối thiểu.

**Phân tích thuật toán:**
Interval DP tiêu chuẩn. Trạng thái `dp[i][j]` là chi phí tối thiểu để nhân ma trận từ `i` đến `j`. `dp[i][j] = min(dp[i][k] + dp[k+1][j] + cost)` với `i <= k < j`.

**Mã nguồn Java:**
```java
public class MatrixChainMultiplication {
    public int matrixMultiplication(int N, int p[]) {
        int[][] dp = new int[N][N];
        
        for (int len = 2; len < N; len++) {
            for (int i = 1; i < N - len + 1; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i; k < j; k++) {
                    int cost = dp[i][k] + dp[k + 1][j] + p[i - 1] * p[k] * p[j];
                    if (cost < dp[i][j]) dp[i][j] = cost;
                }
            }
        }
        return dp[1][N - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3), Không gian O(N^2).

---

## 10. Number of Ways to Wear Different Hats to Each Other (LeetCode 1434)
**Đề bài chi tiết:** Có `n` người và 40 loại mũ. Tìm số cách đội mũ sao cho không ai đội chung loại mũ.

**Phân tích thuật toán:**
Bitmask DP. Lật ngược bài toán: Ánh xạ Mũ -> Người thay vì Người -> Mũ (vì N = 10 rất nhỏ nhưng M = 40). `dp[hat][mask]` là số cách chia xong tới mũ `hat` với `mask` biểu thị số người đã có mũ.

**Mã nguồn Java:**
```java
import java.util.*;

public class WearHats {
    public int numberWays(List<List<Integer>> hats) {
        int n = hats.size();
        int MOD = 1_000_000_007;
        List<Integer>[] hatToPerson = new ArrayList[41];
        for (int i = 1; i <= 40; i++) hatToPerson[i] = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            for (int h : hats.get(i)) hatToPerson[h].add(i);
        }
        
        int[] dp = new int[1 << n];
        dp[0] = 1;
        
        for (int i = 1; i <= 40; i++) {
            int[] nextDp = dp.clone();
            for (int p : hatToPerson[i]) {
                for (int mask = (1 << n) - 1; mask >= 0; mask--) {
                    if ((mask & (1 << p)) == 0) {
                        nextDp[mask | (1 << p)] = (nextDp[mask | (1 << p)] + dp[mask]) % MOD;
                    }
                }
            }
            dp = nextDp;
        }
        return dp[(1 << n) - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(M * 2^N) với M=40, N=10. Không gian O(2^N).

---

## 11. Scramble String (LeetCode 87)
**Đề bài chi tiết:** Cho hai chuỗi `s1` và `s2` có cùng độ dài. Có thể xáo trộn một chuỗi bằng cách biểu diễn nó như một cây nhị phân (mỗi node không phải lá chứa 2 chuỗi con) và có quyền chọn đổi chỗ 2 con của một node bất kỳ (hoặc không đổi). Hỏi `s2` có phải là một chuỗi xáo trộn của `s1` không.

**Phân tích thuật toán:**
Sử dụng Interval DP kết hợp đệ quy có nhớ (Memoization). Kiểm tra từng vị trí cắt `i` từ 1 đến `length - 1`. Hai chuỗi là xáo trộn của nhau nếu tồn tại điểm cắt `i` sao cho:
- Phần bên trái của `s1` xáo trộn thành phần bên trái của `s2` VÀ phần bên phải của `s1` xáo trộn thành phần bên phải của `s2` (không đổi chỗ).
- Hoặc phần bên trái của `s1` xáo trộn thành phần bên phải của `s2` VÀ phần bên phải của `s1` xáo trộn thành phần bên trái của `s2` (có đổi chỗ).

**Mã nguồn Java:**
```java
import java.util.*;

public class ScrambleString {
    private Map<String, Boolean> memo = new HashMap<>();

    public boolean isScramble(String s1, String s2) {
        if (s1.equals(s2)) return true;
        if (s1.length() != s2.length()) return false;

        String key = s1 + "#" + s2;
        if (memo.containsKey(key)) return memo.get(key);

        int n = s1.length();
        int[] count = new int[26];
        for (int i = 0; i < n; i++) {
            count[s1.charAt(i) - 'a']++;
            count[s2.charAt(i) - 'a']--;
        }
        for (int i = 0; i < 26; i++) {
            if (count[i] != 0) {
                memo.put(key, false);
                return false;
            }
        }

        for (int i = 1; i < n; i++) {
            if (isScramble(s1.substring(0, i), s2.substring(0, i)) && 
                isScramble(s1.substring(i), s2.substring(i))) {
                memo.put(key, true);
                return true;
            }
            if (isScramble(s1.substring(0, i), s2.substring(n - i)) && 
                isScramble(s1.substring(i), s2.substring(0, n - i))) {
                memo.put(key, true);
                return true;
            }
        }
        
        memo.put(key, false);
        return false;
    }
}
```
**Độ phức tạp:** Thời gian O(N^4) cho DP/Memoization (hoặc O(N^3) với DP mảng 3 chiều), Không gian O(N^3).

---

## 12. Remove Boxes (LeetCode 546)
**Đề bài chi tiết:** Cho một mảng `boxes` chứa nhiều hộp với các màu (số) khác nhau. Bạn có thể xóa một số hộp liên tiếp có cùng màu và nhận được `k * k` điểm với `k` là số lượng hộp vừa xóa. Tìm số điểm tối đa có thể đạt được sau khi xóa hết hộp.

**Phân tích thuật toán:**
Dùng quy hoạch động đa chiều (3D DP). Trạng thái `dp[i][j][k]` là số điểm lớn nhất khi xét đoạn từ hộp `i` đến `j`, biết rằng có `k` hộp có cùng màu với hộp `i` đang dính ở ngay trước vị trí `i`. Ta có thể chọn xóa hộp `i` (và `k` hộp trước đó) để nhận `(k+1)^2` điểm, hoặc tìm một hộp `m` (i < m <= j) cùng màu với `i`, xóa phần ở giữa rồi gộp chúng lại.

**Mã nguồn Java:**
```java
public class RemoveBoxes {
    public int removeBoxes(int[] boxes) {
        int n = boxes.length;
        int[][][] dp = new int[n][n][n];
        return dfs(boxes, 0, n - 1, 0, dp);
    }
    
    private int dfs(int[] boxes, int i, int j, int k, int[][][] dp) {
        if (i > j) return 0;
        if (dp[i][j][k] > 0) return dp[i][j][k];
        
        int i0 = i, k0 = k;
        while (i + 1 <= j && boxes[i + 1] == boxes[i]) {
            i++;
            k++;
        }
        
        int res = (k + 1) * (k + 1) + dfs(boxes, i + 1, j, 0, dp);
        for (int m = i + 1; m <= j; m++) {
            if (boxes[m] == boxes[i]) {
                res = Math.max(res, dfs(boxes, i + 1, m - 1, 0, dp) + dfs(boxes, m, j, k + 1, dp));
            }
        }
        
        return dp[i0][j][k0] = res;
    }
}
```
**Độ phức tạp:** Thời gian O(N^4), Không gian O(N^3).

---

## 13. Strange Printer (LeetCode 664)
**Đề bài chi tiết:** Máy in kỳ lạ có thể in một chuỗi các ký tự giống nhau trong mỗi lượt, và in đè lên những ký tự đã có. Tìm số lượt in tối thiểu để in ra chuỗi `s`.

**Phân tích thuật toán:**
Tương tự Interval DP trong Remove Boxes. Trạng thái `dp[i][j]` là số lần in tối thiểu cho đoạn từ `i` đến `j`. Mặc định `dp[i][j] = dp[i][j-1] + 1` (in thêm ký tự cuối). Nếu tồn tại `k` (i <= k < j) sao cho `s[k] == s[j]`, ta có thể cập nhật `dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j-1])`.

**Mã nguồn Java:**
```java
public class StrangePrinter {
    public int strangePrinter(String s) {
        int n = s.length();
        if (n == 0) return 0;
        int[][] dp = new int[n][n];
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i + 1; j < n; j++) {
                dp[i][j] = dp[i][j - 1] + 1;
                for (int k = i; k < j; k++) {
                    if (s.charAt(k) == s.charAt(j)) {
                        dp[i][j] = Math.min(dp[i][j], dp[i][k] + (k + 1 <= j - 1 ? dp[k + 1][j - 1] : 0));
                    }
                }
            }
        }
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3), Không gian O(N^2).

---

## 14. Tallest Billboard (LeetCode 956)
**Đề bài chi tiết:** Cho một tập các thanh thép có độ dài khác nhau. Bạn có thể hàn nối chúng lại để tạo thành 2 trụ nâng một bảng quảng cáo. Hai trụ phải có độ cao bằng nhau. Tìm chiều cao lớn nhất có thể của bảng quảng cáo (tức là chiều cao một trụ). Nếu không thể, trả về 0.

**Phân tích thuật toán:**
Sử dụng DP với độ chênh lệch (Difference as State). Trạng thái `dp[diff]` lưu chiều cao lớn nhất của trụ cao hơn khi độ chênh lệch giữa 2 trụ là `diff`. Khởi tạo `dp[0] = 0` và các ô khác bằng số âm cực nhỏ. Với mỗi thanh thép `x`, cập nhật mảng DP: cộng `x` vào trụ cao, hoặc cộng `x` vào trụ thấp.

**Mã nguồn Java:**
```java
import java.util.*;

public class TallestBillboard {
    public int tallestBillboard(int[] rods) {
        Map<Integer, Integer> dp = new HashMap<>();
        dp.put(0, 0);
        
        for (int x : rods) {
            Map<Integer, Integer> current = new HashMap<>(dp);
            for (Map.Entry<Integer, Integer> entry : current.entrySet()) {
                int d = entry.getKey();
                int h = entry.getValue();
                
                // Thêm vào trụ cao hơn
                dp.put(d + x, Math.max(dp.getOrDefault(d + x, 0), h + x));
                // Thêm vào trụ thấp hơn
                int newDiff = Math.abs(d - x);
                dp.put(newDiff, Math.max(dp.getOrDefault(newDiff, 0), Math.max(h, h - d + x)));
            }
        }
        
        return dp.getOrDefault(0, 0);
    }
}
```
**Độ phức tạp:** Thời gian O(N * S) với S là tổng độ dài các thanh (S <= 5000), Không gian O(S).

---

## 15. K-th Smallest in Lexicographical Order (LeetCode 440)
**Đề bài chi tiết:** Cho số nguyên `n` và số nguyên `k`. Tìm số nhỏ thứ `k` theo thứ tự từ điển trong các số từ `1` đến `n`.

**Phân tích thuật toán:**
Được tiếp cận qua dạng phân tích số trên cây tiền tố (Digit Tree/Trie). Đây là một dạng áp dụng toán đếm số lượng node con của một nhánh trên cây từ điển để nhảy vọt, nếu số lượng node con nhỏ hơn `k` thì bỏ qua nhánh đó (nhảy sang node cùng cấp), nếu lớn hơn hoặc bằng `k` thì đi xuống node con của nó. 

**Mã nguồn Java:**
```java
public class KthSmallestLexicographical {
    public int findKthNumber(int n, int k) {
        int curr = 1;
        k = k - 1;
        while (k > 0) {
            long steps = countSteps(n, curr, curr + 1);
            if (steps <= k) {
                curr += 1;
                k -= (int)steps;
            } else {
                curr *= 10;
                k -= 1;
            }
        }
        return curr;
    }
    
    private long countSteps(int n, long n1, long n2) {
        long steps = 0;
        while (n1 <= n) {
            steps += Math.min(n + 1L, n2) - n1;
            n1 *= 10;
            n2 *= 10;
        }
        return steps;
    }
}
```
**Độ phức tạp:** Thời gian O(log(N)^2), Không gian O(1).

---

## 16. Numbers With Repeated Digits (LeetCode 1012)
**Đề bài chi tiết:** Cho số nguyên dương `n`, đếm số lượng các số nguyên dương bé hơn hoặc bằng `n` có chứa ít nhất một chữ số lặp lại.

**Phân tích thuật toán:**
Sử dụng nguyên lý bù trừ: Kết quả = `n` - (Số các số nguyên dương <= `n` KHÔNG có chữ số lặp lại). Để đếm các số <= `n` không có chữ số lặp, sử dụng Digit DP hoặc toán tổ hợp. Xử lý các số có độ dài ngắn hơn độ dài của `n` trước, sau đó xét các số có cùng độ dài theo từng chữ số từ trái qua phải.

**Mã nguồn Java:**
```java
import java.util.*;

public class NumbersRepeatedDigits {
    public int numDupDigitsAtMostN(int n) {
        return n - countSpecialNumbers(n);
    }
    
    private int countSpecialNumbers(int n) {
        List<Integer> digits = new ArrayList<>();
        int temp = n + 1;
        while (temp > 0) {
            digits.add(temp % 10);
            temp /= 10;
        }
        Collections.reverse(digits);
        
        int res = 0, len = digits.size();
        
        // Đếm các số có độ dài nhỏ hơn
        for (int i = 1; i < len; i++) {
            res += 9 * perm(9, i - 1);
        }
        
        // Đếm các số có cùng độ dài nhỏ hơn n + 1
        boolean[] seen = new boolean[10];
        for (int i = 0; i < len; i++) {
            int d = digits.get(i);
            for (int j = (i == 0 ? 1 : 0); j < d; j++) {
                if (!seen[j]) {
                    res += perm(9 - i, len - i - 1);
                }
            }
            if (seen[d]) break;
            seen[d] = true;
        }
        return res;
    }
    
    private int perm(int m, int n) {
        int res = 1;
        for (int i = 0; i < n; i++) {
            res *= (m - i);
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian O(D^2) với D là số lượng chữ số (D <= 10), Không gian O(D).

---

## 17. Cherry Pickup (LeetCode 741)
**Đề bài chi tiết:** Cho một lưới hai chiều. Tìm số cherry lớn nhất có thể thu thập được bằng cách đi từ góc trên-trái xuống góc dưới-phải, rồi quay ngược lại từ góc dưới-phải về góc trên-trái.

**Phân tích thuật toán:**
Quay ngược từ (N-1, N-1) về (0,0) tương đương với việc 2 người cùng xuất phát từ (0,0) đi tới (N-1, N-1). Tại cùng một bước `t`, hoành độ và tung độ thỏa mãn `r + c = t`. Vì vậy, trạng thái là `dp[t][r1][r2]`, khi đó `c1 = t - r1` và `c2 = t - r2`. Nếu `r1 == r2` và `c1 == c2`, chỉ đếm cherry 1 lần.

**Mã nguồn Java:**
```java
import java.util.*;

public class CherryPickup {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        int[][][] dp = new int[2 * n - 1][n][n];
        for (int[][] arr2D : dp) {
            for (int[] arr1D : arr2D) {
                Arrays.fill(arr1D, Integer.MIN_VALUE);
            }
        }
        int ans = dfs(grid, 0, 0, 0, n, dp);
        return Math.max(0, ans);
    }
    
    private int dfs(int[][] grid, int t, int r1, int r2, int n, int[][][] dp) {
        int c1 = t - r1;
        int c2 = t - r2;
        if (r1 == n || c1 == n || r2 == n || c2 == n || 
            grid[r1][c1] == -1 || grid[r2][c2] == -1) {
            return Integer.MIN_VALUE;
        }
        if (t == 2 * n - 2) return grid[n - 1][n - 1];
        
        if (dp[t][r1][r2] != Integer.MIN_VALUE) return dp[t][r1][r2];
        
        int cherries = grid[r1][c1] + (r1 == r2 ? 0 : grid[r2][c2]);
        int maxCherries = Math.max(
            Math.max(dfs(grid, t + 1, r1 + 1, r2 + 1, n, dp), dfs(grid, t + 1, r1 + 1, r2, n, dp)),
            Math.max(dfs(grid, t + 1, r1, r2 + 1, n, dp), dfs(grid, t + 1, r1, r2, n, dp))
        );
        
        if (maxCherries == Integer.MIN_VALUE) {
            return dp[t][r1][r2] = Integer.MIN_VALUE;
        }
        return dp[t][r1][r2] = cherries + maxCherries;
    }
}
```
**Độ phức tạp:** Thời gian O(N^3), Không gian O(N^3).

---

## 18. Cherry Pickup II (LeetCode 1463)
**Đề bài chi tiết:** Cho một lưới, robot 1 xuất phát ở góc trên-trái, robot 2 ở góc trên-phải. Cả hai cùng đi xuống đến dòng cuối cùng (mỗi bước đi xuống một dòng và dịch cột -1, 0, hoặc 1). Tính số cherry lớn nhất thu được. 

**Phân tích thuật toán:**
Dùng DP 3 chiều: `dp[row][c1][c2]` lưu số lượng cherry tối đa thu được khi 2 robot đang ở hàng `row` và nằm tại cột `c1` và `c2`. Do cả 2 cùng xuống 1 hàng mỗi bước, ta dễ dàng định nghĩa hàm đệ quy truy hồi các trạng thái của hàng tiếp theo với 9 tổ hợp di chuyển.

**Mã nguồn Java:**
```java
import java.util.*;

public class CherryPickupII {
    public int cherryPickup(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        Integer[][][] dp = new Integer[m][n][n];
        return dfs(grid, 0, 0, n - 1, dp);
    }
    
    private int dfs(int[][] grid, int r, int c1, int c2, Integer[][][] dp) {
        int m = grid.length, n = grid[0].length;
        if (r == m) return 0;
        if (dp[r][c1][c2] != null) return dp[r][c1][c2];
        
        int cherries = grid[r][c1] + (c1 == c2 ? 0 : grid[r][c2]);
        int maxNext = 0;
        
        for (int dc1 = -1; dc1 <= 1; dc1++) {
            for (int dc2 = -1; dc2 <= 1; dc2++) {
                int nc1 = c1 + dc1;
                int nc2 = c2 + dc2;
                if (nc1 >= 0 && nc1 < n && nc2 >= 0 && nc2 < n) {
                    maxNext = Math.max(maxNext, dfs(grid, r + 1, nc1, nc2, dp));
                }
            }
        }
        
        return dp[r][c1][c2] = cherries + maxNext;
    }
}
```
**Độ phức tạp:** Thời gian O(M * N^2), Không gian O(M * N^2).

---

## 19. Count Different Palindromic Subsequences (LeetCode 730)
**Đề bài chi tiết:** Cho chuỗi `S` chứa các ký tự `'a', 'b', 'c', 'd'`. Đếm số lượng các chuỗi con không liền kề là chuỗi đối xứng (palindrome) nhưng phải phân biệt (không trùng nhau). Kết quả modulo 10^9+7.

**Phân tích thuật toán:**
Interval DP. Trạng thái `dp[i][j]` là số chuỗi con đối xứng phân biệt trong `S[i..j]`.
- Nếu `S[i] != S[j]`: `dp[i][j] = dp[i+1][j] + dp[i][j-1] - dp[i+1][j-1]`
- Nếu `S[i] == S[j]`: `dp[i][j] = 2 * dp[i+1][j-1]`. Sau đó phải xử lý các ký tự giống `S[i]` ở giữa đoạn để tránh đếm trùng. Nếu không có ký tự trùng, cộng 2 (là "a" và "aa"). Nếu có 1 ký tự trùng, cộng 1 (là "a"). Nếu có từ 2 ký tự trùng, trừ đi `dp[low+1][high-1]` phần đã đếm trùng.

**Mã nguồn Java:**
```java
public class CountDifferentPalindromicSubsequences {
    public int countPalindromicSubsequences(String s) {
        int MOD = 1_000_000_007;
        int n = s.length();
        int[][] dp = new int[n][n];
        
        for (int i = 0; i < n; i++) dp[i][i] = 1;
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) != s.charAt(j)) {
                    dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];
                } else {
                    dp[i][j] = dp[i + 1][j - 1] * 2;
                    int left = i + 1;
                    int right = j - 1;
                    while (left <= right && s.charAt(left) != s.charAt(i)) left++;
                    while (left <= right && s.charAt(right) != s.charAt(i)) right--;
                    
                    if (left > right) {
                        dp[i][j] += 2;
                    } else if (left == right) {
                        dp[i][j] += 1;
                    } else {
                        dp[i][j] -= dp[left + 1][right - 1];
                    }
                }
                dp[i][j] = dp[i][j] < 0 ? dp[i][j] + MOD : dp[i][j] % MOD;
            }
        }
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3) (với tìm kiếm while) hoặc O(N^2) nếu tối ưu mảng lưu vị trí next/prev. Không gian O(N^2).

---

## 20. Palindrome Partitioning III (LeetCode 1278)
**Đề bài chi tiết:** Cho chuỗi `s` và số nguyên `k`. Bạn có thể thay đổi bất kỳ ký tự nào của `s` để biến nó thành chuỗi khác. Tìm số lượng ký tự tối thiểu cần thay đổi sao cho bạn có thể chia `s` thành đúng `k` chuỗi con mà mỗi chuỗi đều là chuỗi đối xứng (palindrome).

**Phân tích thuật toán:**
Kết hợp 2 DP. Đầu tiên là Interval DP `cost[i][j]` tính số phép đổi ký tự ít nhất để `s[i..j]` thành palindrome. Sau đó dùng DP tuyến tính trên đoạn `dp[i][c]` là chi phí tối thiểu để chia đoạn `s[0..i]` thành `c` phần.
`dp[i][c] = min(dp[j-1][c-1] + cost[j][i])` với `1 <= j <= i`.

**Mã nguồn Java:**
```java
public class PalindromePartitioningIII {
    public int palindromePartition(String s, int k) {
        int n = s.length();
        int[][] cost = new int[n][n];
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                cost[i][j] = cost[i + 1][j - 1] + (s.charAt(i) == s.charAt(j) ? 0 : 1);
            }
        }
        
        int[][] dp = new int[n][k + 1];
        for (int i = 0; i < n; i++) {
            for (int c = 0; c <= k; c++) {
                dp[i][c] = 10000;
            }
        }
        
        for (int i = 0; i < n; i++) {
            dp[i][1] = cost[0][i];
            for (int c = 2; c <= k && c <= i + 1; c++) {
                for (int j = i; j >= c - 1; j--) {
                    dp[i][c] = Math.min(dp[i][c], dp[j - 1][c - 1] + cost[j][i]);
                }
            }
        }
        
        return dp[n - 1][k];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3 + K * N^2), Không gian O(N^2).

---

## 21. Find the Shortest Superstring (LeetCode 943)
**Đề bài chi tiết:** Cho một mảng các chuỗi `words`. Tìm chuỗi ngắn nhất chứa tất cả các chuỗi trong `words` như một chuỗi con. Nếu có nhiều đáp án cùng chiều dài ngắn nhất, trả về một đáp án bất kỳ.

**Phân tích thuật toán:**
Dùng Bitmask DP, đây là bài toán Traveling Salesperson Problem (TSP). Đồ thị có tối đa 12 đỉnh (số lượng chuỗi). Trọng số cạnh từ `i` đến `j` là số ký tự cần thêm vào chuỗi `i` để có chứa chuỗi `j` liền kề. `dp[mask][i]` là chiều dài nhỏ nhất để chứa tập hợp các chuỗi biểu diễn bởi `mask`, kết thúc tại chuỗi `i`.

**Mã nguồn Java:**
```java
import java.util.*;

public class ShortestSuperstring {
    public String shortestSuperstring(String[] words) {
        int n = words.length;
        int[][] graph = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                graph[i][j] = words[j].length();
                for (int k = Math.min(words[i].length(), words[j].length()); k >= 1; k--) {
                    if (words[i].substring(words[i].length() - k).equals(words[j].substring(0, k))) {
                        graph[i][j] = words[j].length() - k;
                        break;
                    }
                }
            }
        }
        
        int[][] dp = new int[1 << n][n];
        int[][] parent = new int[1 << n][n];
        for (int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE / 2);
        for (int i = 0; i < n; i++) dp[1 << i][i] = words[i].length();
        
        for (int mask = 1; mask < (1 << n); mask++) {
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) > 0) {
                    int prevMask = mask ^ (1 << i);
                    for (int j = 0; j < n; j++) {
                        if ((prevMask & (1 << j)) > 0) {
                            if (dp[prevMask][j] + graph[j][i] < dp[mask][i]) {
                                dp[mask][i] = dp[prevMask][j] + graph[j][i];
                                parent[mask][i] = j;
                            }
                        }
                    }
                }
            }
        }
        
        int minLen = Integer.MAX_VALUE, last = -1;
        for (int i = 0; i < n; i++) {
            if (dp[(1 << n) - 1][i] < minLen) {
                minLen = dp[(1 << n) - 1][i];
                last = i;
            }
        }
        
        List<Integer> path = new ArrayList<>();
        int currMask = (1 << n) - 1;
        while (currMask > 0) {
            path.add(last);
            int prev = parent[currMask][last];
            currMask ^= (1 << last);
            last = prev;
        }
        Collections.reverse(path);
        
        StringBuilder res = new StringBuilder(words[path.get(0)]);
        for (int i = 1; i < path.size(); i++) {
            int p1 = path.get(i - 1), p2 = path.get(i);
            res.append(words[p2].substring(words[p2].length() - graph[p1][p2]));
        }
        return res.toString();
    }
}
```
**Độ phức tạp:** Thời gian O(N^2 * 2^N), Không gian O(N * 2^N).

---

## 22. Valid Permutations for DI Sequence (LeetCode 903)
**Đề bài chi tiết:** Cho chuỗi `S` gồm 'D' (giảm) và 'I' (tăng) với độ dài `n`. Tìm số lượng hoán vị của các số từ `0` đến `n` thỏa mãn mẫu tăng/giảm của chuỗi `S`. Kết quả modulo 10^9+7.

**Phân tích thuật toán:**
Dùng DP trên hoán vị. Gọi `dp[i][j]` là số hoán vị hợp lệ có chiều dài `i+1` (dùng các số từ `0` đến `i`), sao cho phần tử cuối cùng của hoán vị là số lớn thứ `j` trong tập các số được dùng (tức là `j` số có sẵn nhỏ hơn nó).
- Nếu `S[i-1] == 'I'`, phần tử cuối phải lớn hơn phần tử áp cuối: `dp[i][j] = sum(dp[i-1][k])` với `0 <= k < j`.
- Nếu `S[i-1] == 'D'`, phần tử cuối phải nhỏ hơn phần tử áp cuối: `dp[i][j] = sum(dp[i-1][k])` với `j <= k <= i-1`.

**Mã nguồn Java:**
```java
public class ValidPermutationsDI {
    public int numPermsDISequence(String S) {
        int n = S.length();
        int MOD = 1_000_000_007;
        int[][] dp = new int[n + 1][n + 1];
        
        dp[0][0] = 1;
        
        for (int i = 1; i <= n; i++) {
            if (S.charAt(i - 1) == 'I') {
                int sum = 0;
                for (int j = 1; j <= i; j++) {
                    sum = (sum + dp[i - 1][j - 1]) % MOD;
                    dp[i][j] = sum;
                }
            } else {
                int sum = 0;
                for (int j = i - 1; j >= 0; j--) {
                    sum = (sum + dp[i - 1][j]) % MOD;
                    dp[i][j] = sum;
                }
            }
        }
        
        int res = 0;
        for (int j = 0; j <= n; j++) {
            res = (res + dp[n][j]) % MOD;
        }
        return res;
    }
}
```
**Độ phức tạp:** Thời gian O(N^2), Không gian O(N^2) (có thể tối ưu không gian xuống O(N)).

---

## 23. Student Attendance Record II (LeetCode 552)
**Đề bài chi tiết:** Một học sinh điểm danh với 3 trạng thái: 'A' (Vắng mặt), 'L' (Muộn), 'P' (Có mặt). Tìm số lượng chuỗi điểm danh có độ dài `n` mà hợp lệ. Chuỗi hợp lệ không được chứa quá 1 chữ 'A' và không chứa 3 chữ 'L' liên tiếp.

**Phân tích thuật toán:**
Dùng State Machine DP. Trạng thái `dp[i][a][l]` là số chuỗi độ dài `i` có `a` lần 'A' (a <= 1) và `l` lần 'L' liên tiếp ở cuối (l <= 2). Tại bước thứ `i`, ta thử thêm 'P' (reset `l` về 0), thêm 'L' (tăng `l` lên 1 nếu `l` < 2), hoặc thêm 'A' (tăng `a` lên 1 nếu `a` == 0, reset `l` về 0).

**Mã nguồn Java:**
```java
public class StudentAttendanceRecordII {
    public int checkRecord(int n) {
        int MOD = 1_000_000_007;
        // dp[A_count][L_consecutive_count]
        long[][] dp = new long[2][3];
        dp[0][0] = 1;
        
        for (int i = 0; i < n; i++) {
            long[][] next = new long[2][3];
            
            // Add 'P'
            for (int a = 0; a < 2; a++) {
                for (int l = 0; l < 3; l++) {
                    next[a][0] = (next[a][0] + dp[a][l]) % MOD;
                }
            }
            
            // Add 'L'
            for (int a = 0; a < 2; a++) {
                for (int l = 0; l < 2; l++) {
                    next[a][l + 1] = (next[a][l + 1] + dp[a][l]) % MOD;
                }
            }
            
            // Add 'A'
            for (int l = 0; l < 3; l++) {
                next[1][0] = (next[1][0] + dp[0][l]) % MOD;
            }
            
            dp = next;
        }
        
        long res = 0;
        for (int a = 0; a < 2; a++) {
            for (int l = 0; l < 3; l++) {
                res = (res + dp[a][l]) % MOD;
            }
        }
        return (int) res;
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1) (do mảng DP kích thước cố định).

---

## 24. Count Vowels Permutation (LeetCode 1220)
**Đề bài chi tiết:** Cho số nguyên `n`. Tìm số lượng chuỗi có độ dài `n` chỉ bao gồm các nguyên âm ('a', 'e', 'i', 'o', 'u') với điều kiện: 
- 'a' chỉ có thể đi sau là 'e'.
- 'e' chỉ có thể đi sau là 'a' hoặc 'i'.
- 'i' không thể theo sau bởi 'i' (có thể là 'a', 'e', 'o', 'u').
- 'o' chỉ có thể đi sau là 'i' hoặc 'u'.
- 'u' chỉ có thể đi sau là 'a'.

**Phân tích thuật toán:**
Quy hoạch động đơn giản với trạng thái `dp[vowel]` số lượng chuỗi kết thúc bằng một nguyên âm cụ thể. Dựa trên luật trên, ta đảo ngược quy tắc để biết ký tự hiện tại có thể được tạo ra từ ký tự nào trước đó (ví dụ 'e' có thể được tạo ra từ 'a' và 'i', vậy tổng số chuỗi kết thúc bằng 'e' = số chuỗi kết thúc bằng 'a' + số chuỗi kết thúc bằng 'i' ở bước trước).

**Mã nguồn Java:**
```java
public class CountVowelsPermutation {
    public int countVowelPermutation(int n) {
        int MOD = 1_000_000_007;
        long a = 1, e = 1, i = 1, o = 1, u = 1;
        
        for (int len = 2; len <= n; len++) {
            long nextA = (e + i + u) % MOD;
            long nextE = (a + i) % MOD;
            long nextI = (e + o) % MOD;
            long nextO = i % MOD;
            long nextU = (i + o) % MOD;
            
            a = nextA;
            e = nextE;
            i = nextI;
            o = nextO;
            u = nextU;
        }
        
        return (int) ((a + e + i + o + u) % MOD);
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(1).

---

## 25. Minimum Cost to Merge Stones (LeetCode 1000)
**Đề bài chi tiết:** Có `n` đống đá, mỗi đống có `stones[i]` viên đá. Mỗi bước, bạn có thể gộp đúng `K` đống đá liền kề thành 1 đống, chi phí là tổng số đá của `K` đống đó. Tìm chi phí nhỏ nhất để gộp tất cả thành 1 đống. Nếu không thể, trả về -1.

**Phân tích thuật toán:**
Để gộp `n` đống thành 1 đống mỗi lần mất `K-1` đống, nên `(n - 1) % (K - 1) == 0` là điều kiện cần. Sử dụng Interval DP: `dp[i][j][m]` là chi phí nhỏ nhất để gộp từ `i` đến `j` thành `m` đống. Hoặc tối ưu DP bằng cách chỉ lưu `dp[i][j]` là chi phí gom `[i..j]` thành số đống nhỏ nhất có thể. Tại đoạn độ dài `len`, chia làm 2 phần gộp: một phần gộp thành 1 đống, phần còn lại.

**Mã nguồn Java:**
```java
public class MinimumCostMergeStones {
    public int mergeStones(int[] stones, int K) {
        int n = stones.length;
        if ((n - 1) % (K - 1) != 0) return -1;
        
        int[] prefix = new int[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + stones[i];
        
        int[][] dp = new int[n][n];
        
        for (int len = K; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for (int mid = i; mid < j; mid += K - 1) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][mid] + dp[mid + 1][j]);
                }
                if ((len - 1) % (K - 1) == 0) {
                    dp[i][j] += prefix[j + 1] - prefix[i];
                }
            }
        }
        
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(N^3 / K), Không gian O(N^2).

---

## 26. Maximize Score After N Operations (LeetCode 1799)
**Đề bài chi tiết:** Cho mảng `nums` chứa `2n` số nguyên. Bạn cần thực hiện `n` phép tính, ở phép tính thứ `i` (từ 1 đến `n`), bạn chọn 2 số trong mảng, tính Ước chung lớn nhất (GCD) của chúng, nhân với `i`, cộng vào điểm số và xóa 2 số đó khỏi mảng. Tìm điểm số tối đa.

**Phân tích thuật toán:**
Bitmask DP + GCD có nhớ. Mảng có tối đa 14 phần tử. Dùng trạng thái `dp[mask]` lưu điểm cao nhất với các phần tử chưa bị xóa. Tại mỗi mặt nạ có số lượng bit 1 là chẵn, lấy `mask` trừ đi 2 bit (chọn 2 phần tử) để truy hồi DP. Lượt thứ `i` được tính bằng `(số lượng số ban đầu - số bit 1 trong mask) / 2 + 1`.

**Mã nguồn Java:**
```java
import java.util.*;

public class MaximizeScoreAfterNOperations {
    public int maxScore(int[] nums) {
        int n = nums.length;
        int[] dp = new int[1 << n];
        int[][] gcd = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                gcd[i][j] = getGcd(nums[i], nums[j]);
            }
        }
        
        for (int mask = 3; mask < (1 << n); mask++) {
            int count = Integer.bitCount(mask);
            if (count % 2 != 0) continue;
            
            int step = count / 2;
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) == 0) continue;
                for (int j = i + 1; j < n; j++) {
                    if ((mask & (1 << j)) == 0) continue;
                    
                    int prevMask = mask ^ (1 << i) ^ (1 << j);
                    dp[mask] = Math.max(dp[mask], dp[prevMask] + step * gcd[i][j]);
                }
            }
        }
        return dp[(1 << n) - 1];
    }
    
    private int getGcd(int a, int b) {
        return b == 0 ? a : getGcd(b, a % b);
    }
}
```
**Độ phức tạp:** Thời gian O(2^N * N^2), Không gian O(2^N).

---

## 27. Find Minimum Time to Finish All Jobs (LeetCode 1723)
**Đề bài chi tiết:** Cho một mảng `jobs` chứa thời gian hoàn thành từng công việc, và số `k` người làm việc. Cần giao tất cả công việc cho mọi người sao cho thời gian làm việc tối đa của một người là nhỏ nhất có thể (tối thiểu hóa max).

**Phân tích thuật toán:**
Có thể giải bằng DFS + Branch & Bound + Binary Search, nhưng theo DP thì đây là DP trên Bitmask. `dp[i][mask]` là thời gian lớn nhất tối thiểu khi chia một tập con công việc `mask` cho `i` người. `dp[i][mask] = min_{submask}( max(dp[i-1][mask ^ submask], sum[submask]) )`.

**Mã nguồn Java:**
```java
public class FindMinimumTimeAllJobs {
    public int minimumTimeRequired(int[] jobs, int k) {
        int n = jobs.length;
        int[] sum = new int[1 << n];
        for (int i = 0; i < (1 << n); i++) {
            for (int j = 0; j < n; j++) {
                if ((i & (1 << j)) != 0) {
                    sum[i] += jobs[j];
                }
            }
        }
        
        int[][] dp = new int[k][1 << n];
        for (int i = 0; i < (1 << n); i++) {
            dp[0][i] = sum[i];
        }
        
        for (int i = 1; i < k; i++) {
            for (int mask = 0; mask < (1 << n); mask++) {
                dp[i][mask] = Integer.MAX_VALUE;
                for (int submask = mask; submask > 0; submask = (submask - 1) & mask) {
                    dp[i][mask] = Math.min(dp[i][mask], 
                        Math.max(dp[i - 1][mask ^ submask], sum[submask]));
                }
            }
        }
        return dp[k - 1][(1 << n) - 1];
    }
}
```
**Độ phức tạp:** Thời gian O(K * 3^N), Không gian O(K * 2^N).

---

## 28. Maximum Number of Groups Getting Fresh Donuts (LeetCode 1815)
**Đề bài chi tiết:** Có một cửa hàng bánh rán bán theo lô kích thước `batchSize`. Có một số nhóm người. Bạn có thể phục vụ theo bất kỳ thứ tự nào. Một nhóm vui vẻ nếu họ đến cửa hàng khi số bánh ráp còn dư chia hết cho lô. Tính số lượng tối đa nhóm vui vẻ.

**Phân tích thuật toán:**
Số khách mỗi nhóm `modulo batchSize`. Nếu phần dư là 0, họ luôn vui vẻ nên tách riêng (đứng đầu). Các nhóm có phần dư `x` kết hợp với nhóm dư `batchSize - x` cũng luôn tạo ra một nhóm vui vẻ. Với số nhóm còn lại, dùng Memoization DP trên tần suất. Trạng thái lưu trữ số lượng các nhóm phần dư từ `1` đến `batchSize-1`. 

**Mã nguồn Java:**
```java
import java.util.*;

public class MaximumGroupsFreshDonuts {
    private Map<String, Integer> memo = new HashMap<>();
    
    public int maxHappyGroups(int batchSize, int[] groups) {
        int[] counts = new int[batchSize];
        for (int g : groups) counts[g % batchSize]++;
        
        int res = counts[0];
        counts[0] = 0;
        
        for (int i = 1; i <= batchSize / 2; i++) {
            if (i == batchSize - i) {
                res += counts[i] / 2;
                counts[i] %= 2;
            } else {
                int min = Math.min(counts[i], counts[batchSize - i]);
                res += min;
                counts[i] -= min;
                counts[batchSize - i] -= min;
            }
        }
        
        return res + dfs(counts, 0, batchSize);
    }
    
    private int dfs(int[] counts, int remainder, int batchSize) {
        String state = Arrays.toString(counts);
        if (memo.containsKey(state)) return memo.get(state);
        
        int max = 0;
        for (int i = 1; i < batchSize; i++) {
            if (counts[i] > 0) {
                counts[i]--;
                int currentHappy = (remainder == 0 ? 1 : 0);
                max = Math.max(max, currentHappy + dfs(counts, (remainder + i) % batchSize, batchSize));
                counts[i]++;
            }
        }
        memo.put(state, max);
        return max;
    }
}
```
**Độ phức tạp:** Bằng trạng thái tần số (Số lượng nhóm nhỏ, `batchSize <= 9`). Thời gian O(Số lượng tổ hợp phân chia), Không gian O(Tổ hợp phân chia).

---

## 29. Maximum Score from Performing Multiplication Operations (LeetCode 1770)
**Đề bài chi tiết:** Cho mảng `nums` kích thước `N` và mảng `multipliers` kích thước `M` (`M <= 10^3`, `N <= 10^5`). Ở mỗi bước `i` (từ `0` đến `M-1`), bạn lấy 1 số nguyên ở đầu hoặc cuối mảng `nums`, nhân với `multipliers[i]` và cộng vào điểm. Tối đa hóa điểm.

**Phân tích thuật toán:**
Dùng DP 2 chiều (Interval DP rút gọn). Không thể dùng trạng thái `dp[start][end]` vì không gian `O(N^2)` sẽ Timeout/Memory Limit (do N = 10^5). Tuy nhiên, số phép toán chỉ là `M <= 1000`. Gọi `dp[i][left]` là điểm số lớn nhất khi đã làm `i` phép toán và đã lấy `left` phần tử từ bên trái của `nums`. Suy ra số lượng lấy bên phải là `right = i - left`. Và chỉ mục bên phải là `n - 1 - right`.

**Mã nguồn Java:**
```java
public class MaximumScoreMultiplication {
    public int maximumScore(int[] nums, int[] multipliers) {
        int m = multipliers.length;
        int n = nums.length;
        int[][] dp = new int[m + 1][m + 1];
        
        for (int i = m - 1; i >= 0; i--) {
            for (int left = i; left >= 0; left--) {
                int right = i - left;
                int pickLeft = nums[left] * multipliers[i] + dp[i + 1][left + 1];
                int pickRight = nums[n - 1 - right] * multipliers[i] + dp[i + 1][left];
                dp[i][left] = Math.max(pickLeft, pickRight);
            }
        }
        
        return dp[0][0];
    }
}
```
**Độ phức tạp:** Thời gian O(M^2), Không gian O(M^2).

---

## 30. Stone Game VIII (LeetCode 1872)
**Đề bài chi tiết:** Có `n` viên đá giá trị `stones`. Alice và Bob chơi trò chơi: Chọn `x > 1` viên đá đầu tiên, cộng điểm bằng tổng `x` viên đó, loại bỏ `x` viên đá đó và đặt 1 viên đá có giá trị bằng tổng điểm vừa thu được vào đầu. Trò chơi dừng khi chỉ còn 1 viên đá. Mục tiêu: Alice muốn tối đa hóa (Điểm Alice - Bob), Bob muốn tối thiểu hóa. 

**Phân tích thuật toán:**
Minimax DP. Đặt mảng cộng dồn `prefix[i]`. Khi một người chọn lấy đá tại `i`, điểm của người đó tăng thêm `prefix[i]`, và chuyển lượt cho người kia. Lượt tiếp theo, người kia phải chọn cắt ở một vị trí `j > i`. 
Trạng thái `dp[i]` là chênh lệch điểm tối đa nếu người chơi hiện tại phải đưa ra quyết định ở đoạn `[i, n-1]`.
Người đó có thể lấy tại `i` (được `prefix[i] - dp[i+1]`) hoặc nhường lấy tại `i` (nghĩa là lấy tối ưu từ `i+1`, được `dp[i+1]`). Vậy `dp[i] = max(prefix[i] - dp[i+1], dp[i+1])`.

**Mã nguồn Java:**
```java
public class StoneGameVIII {
    public int stoneGameVIII(int[] stones) {
        int n = stones.length;
        int[] prefix = new int[n];
        prefix[0] = stones[0];
        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] + stones[i];
        }
        
        int[] dp = new int[n];
        dp[n - 1] = prefix[n - 1];
        
        for (int i = n - 2; i >= 1; i--) {
            dp[i] = Math.max(dp[i + 1], prefix[i] - dp[i + 1]);
        }
        
        return dp[1]; // Phải lấy tối thiểu 2 viên đá đầu tiên (chỉ mục >= 1)
    }
}
```
**Độ phức tạp:** Thời gian O(N), Không gian O(N) (có thể tối ưu xuống O(1)).
