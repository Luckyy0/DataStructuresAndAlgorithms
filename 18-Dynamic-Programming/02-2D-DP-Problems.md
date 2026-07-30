# 2D Dynamic Programming Problems

## 1. Unique Paths (Đường đi duy nhất)

**Đề bài chi tiết:**
Cho một robot nằm ở góc trên cùng bên trái của một lưới kích thước `m x n` (ô `grid[0][0]`). Robot chỉ có thể di chuyển xuống dưới hoặc sang phải tại bất kỳ thời điểm nào. Robot đang cố gắng đến được góc dưới cùng bên phải của lưới (ô `grid[m - 1][n - 1]`). Hãy tính xem có bao nhiêu đường đi duy nhất có thể xảy ra.

**Phân tích thuật toán:**
Sử dụng mảng 2D `dp` có kích thước `m x n`, trong đó `dp[i][j]` đại diện cho số lượng đường đi để đến được ô `(i, j)`.
- *Base case:* `dp[0][j] = 1` và `dp[i][0] = 1` vì chỉ có 1 cách duy nhất đi theo đường biên mép (chỉ đi ngang hoặc chỉ đi dọc).
- *State Transition:* `dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`.

**Mã nguồn Java:**
```java
public class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        
        for (int i = 0; i < m; i++) dp[i][0] = 1;
        for (int j = 0; j < n; j++) dp[0][j] = 1;
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$ để duyệt qua toàn bộ lưới.
- Không gian (Space Complexity): $O(m \times n)$ cho mảng 2D (có thể tối ưu thành $O(n)$).

---

## 2. Unique Paths II (Đường đi duy nhất II - Có vật cản)

**Đề bài chi tiết:**
Tương tự như bài Unique Paths, nhưng lưới bây giờ có các chướng ngại vật (biểu diễn bằng `1`). Robot không thể đi qua các ô chứa chướng ngại vật. Hãy tính số lượng đường đi duy nhất từ góc trên-trái xuống góc dưới-phải.

**Phân tích thuật toán:**
Vẫn sử dụng mảng 2D `dp`. Nếu ô `obstacleGrid[i][j] == 1`, thì `dp[i][j] = 0` (không có cách nào đi qua).
- *Base case:* Cần chú ý khi khởi tạo hàng đầu/cột đầu. Nếu gặp vật cản, tất cả các ô phía sau nó ở hàng/cột đó đều không thể tới được (có số cách đi bằng 0).
- *State Transition:* Nếu ô đó không có vật cản, `dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`.

**Mã nguồn Java:**
```java
public class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length, n = obstacleGrid[0].length;
        if (obstacleGrid[0][0] == 1) return 0;
        
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        
        // Khởi tạo cột đầu
        for (int i = 1; i < m; i++) {
            dp[i][0] = (obstacleGrid[i][0] == 0 && dp[i - 1][0] == 1) ? 1 : 0;
        }
        // Khởi tạo hàng đầu
        for (int j = 1; j < n; j++) {
            dp[0][j] = (obstacleGrid[0][j] == 0 && dp[0][j - 1] == 1) ? 1 : 0;
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                } else {
                    dp[i][j] = 0;
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \times n)$
- Không gian: $O(m \times n)$

---

## 3. Minimum Path Sum (Tổng đường đi nhỏ nhất)

**Đề bài chi tiết:**
Cho một lưới `m x n` chứa các số nguyên không âm `grid`. Hãy tìm một đường đi từ góc trên cùng bên trái đến góc dưới cùng bên phải sao cho tổng các số trên đường đi là nhỏ nhất. Bạn chỉ được phép di chuyển xuống dưới hoặc sang phải.

**Phân tích thuật toán:**
Sử dụng mảng 2D `dp` nơi `dp[i][j]` lưu tổng nhỏ nhất để đạt được ô `(i, j)`.
- *Base case:* `dp[0][0] = grid[0][0]`. Các ô ở hàng 0 chỉ có thể đến từ ô bên trái, và cột 0 chỉ có thể đến từ ô phía trên.
- *State Transition:* `dp[i][j] = grid[i][j] + Math.min(dp[i - 1][j], dp[i][j - 1])`.

**Mã nguồn Java:**
```java
public class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];
        
        dp[0][0] = grid[0][0];
        
        for (int i = 1; i < m; i++) dp[i][0] = dp[i - 1][0] + grid[i][0];
        for (int j = 1; j < n; j++) dp[0][j] = dp[0][j - 1] + grid[0][j];
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = grid[i][j] + Math.min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \times n)$
- Không gian: $O(m \times n)$ (có thể thay đổi trực tiếp trên mảng `grid` để đạt $O(1)$ không gian).

---

## 4. Triangle (Đường đi nhỏ nhất trong tam giác)

**Đề bài chi tiết:**
Cho một mảng mảng (đại diện cho tam giác các số), hãy tìm tổng nhỏ nhất của đường đi từ đỉnh đến đáy. Mỗi bước, bạn có thể di chuyển đến nút liền kề của hàng ngay bên dưới (từ chỉ mục `i` có thể đi đến chỉ mục `i` hoặc `i+1` ở hàng tiếp theo).

**Phân tích thuật toán:**
Duyệt từ đáy tam giác lên trên đỉnh (Bottom-Up) sẽ giúp tránh phải tính toán ranh giới biên (edge cases).
- *Base case:* Hàng cuối cùng `dp` chính là hàng cuối cùng của `triangle`.
- *State Transition:* Với hàng `i` từ dưới lên, `dp[j] = triangle.get(i).get(j) + Math.min(dp[j], dp[j + 1])`. (Dùng mảng 1 chiều để tối ưu luôn).

**Mã nguồn Java:**
```java
import java.util.List;

public class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] dp = new int[n];
        
        // Khởi tạo hàng cuối cùng
        for (int i = 0; i < n; i++) {
            dp[i] = triangle.get(n - 1).get(i);
        }
        
        // Duyệt ngược từ hàng n-2 lên hàng 0
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[j] = triangle.get(i).get(j) + Math.min(dp[j], dp[j + 1]);
            }
        }
        
        return dp[0];
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n^2)$ với $n$ là số hàng của tam giác.
- Không gian: $O(n)$ do đã tối ưu với mảng 1D.

---

## 5. Minimum Falling Path Sum (Tổng đường đi rơi nhỏ nhất)

**Đề bài chi tiết:**
Cho một ma trận vuông nguyên `matrix` kích thước `n x n`. Hãy tìm tổng nhỏ nhất của đường đi "rơi" xuyên qua ma trận. Đường đi rơi bắt đầu từ bất kỳ phần tử nào ở hàng trên cùng, di chuyển xuống hàng kế tiếp ở 3 cột liền kề (cột `j-1`, `j`, `j+1`).

**Phân tích thuật toán:**
Tương tự bài Minimum Path Sum, nhưng chúng ta có 3 sự lựa chọn trạng thái trước đó.
- *State Transition:* `dp[i][j] = matrix[i][j] + Math.min(dp[i-1][j-1], Math.min(dp[i-1][j], dp[i-1][j+1]))`.
- Ta cần cẩn thận xử lý biên để không bị lỗi `IndexOutOfBounds` (khi `j=0` hoặc `j=n-1`).

**Mã nguồn Java:**
```java
public class Solution {
    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int[][] dp = new int[n][n];
        
        for (int j = 0; j < n; j++) {
            dp[0][j] = matrix[0][j];
        }
        
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int left = j > 0 ? dp[i - 1][j - 1] : Integer.MAX_VALUE;
                int mid = dp[i - 1][j];
                int right = j < n - 1 ? dp[i - 1][j + 1] : Integer.MAX_VALUE;
                
                dp[i][j] = matrix[i][j] + Math.min(left, Math.min(mid, right));
            }
        }
        
        int minSum = Integer.MAX_VALUE;
        for (int j = 0; j < n; j++) {
            minSum = Math.min(minSum, dp[n - 1][j]);
        }
        return minSum;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n^2)$.
- Không gian: $O(n^2)$ (có thể dễ dàng làm trên ma trận gốc hoặc mảng 1D).

---

## 6. Maximal Square (Hình vuông lớn nhất)

**Đề bài chi tiết:**
Cho ma trận `matrix` kích thước `m x n` chứa các số nhị phân `'0'` và `'1'`, tìm hình vuông lớn nhất chỉ chứa toàn số `'1'` và trả về diện tích của nó.

**Phân tích thuật toán:**
Sử dụng mảng `dp[i][j]` để lưu chiều dài cạnh của hình vuông lớn nhất có góc dưới bên phải nằm tại ô `(i, j)`.
- *State Transition:* Nếu `matrix[i][j] == '1'`, thì `dp[i][j] = 1 + Math.min(dp[i-1][j-1], Math.min(dp[i-1][j], dp[i][j-1]))`. 
- Lý do: Một hình vuông kích thước k cần 3 hình vuông kích thước k-1 nằm ở góc trên, trái, và trên-trái.

**Mã nguồn Java:**
```java
public class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int[][] dp = new int[m + 1][n + 1];
        int maxLen = 0;
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (matrix[i - 1][j - 1] == '1') {
                    dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1]));
                    maxLen = Math.max(maxLen, dp[i][j]);
                }
            }
        }
        return maxLen * maxLen;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \times n)$.
- Không gian: $O(m \times n)$ (có thể thu về $O(n)$ bằng mảng 1 chiều).

---

## 7. Dungeon Game (Trò chơi Hầm ngục)

**Đề bài chi tiết:**
Các yêu tinh bắt nhốt công chúa ở góc phải dưới của một lưới hầm ngục `m x n`. Hiệp sĩ bắt đầu từ góc trái trên và phải cứu cô. Mỗi ô chứa quái vật (số âm - làm giảm máu) hoặc bình máu (số dương - hồi máu). Hiệp sĩ chết nếu máu giảm xuống $\le 0$. Hãy tìm lượng máu tối thiểu ban đầu hiệp sĩ cần để cứu công chúa.

**Phân tích thuật toán:**
Bài này nếu chạy từ trái trên xuống sẽ bị phụ thuộc vào lượng máu tối thiểu đạt được và lượng máu đang có. Giải pháp tối ưu là chạy ngược từ phải dưới về trái trên (Bottom-Up ngược).
- *State Transition:* `dp[i][j]` là máu tối thiểu cần khi đứng ở ô `(i,j)`. Ta có `minHealthNeeded = Math.min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j]`. Máu luôn phải $\ge 1$, nên `dp[i][j] = Math.max(1, minHealthNeeded)`.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int m = dungeon.length;
        int n = dungeon[0].length;
        int[][] dp = new int[m + 1][n + 1];
        
        for (int[] row : dp) {
            Arrays.fill(row, Integer.MAX_VALUE);
        }
        
        dp[m][n - 1] = 1;
        dp[m - 1][n] = 1;
        
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int minHealth = Math.min(dp[i + 1][j], dp[i][j + 1]) - dungeon[i][j];
                dp[i][j] = Math.max(1, minHealth);
            }
        }
        
        return dp[0][0];
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \times n)$.
- Không gian: $O(m \times n)$.

---

## 8. Cherry Pickup (Nhặt Cherry)

**Đề bài chi tiết:**
Bạn có một lưới `N x N` chứa chướng ngại vật (-1), khoảng trống (0) và cherry (1). Bạn bắt đầu từ (0, 0), đi xuống (N-1, N-1) rồi quay về (0, 0). Hướng đi lượt xuống là xuống hoặc phải. Lượt lên là lên hoặc trái. Bạn nhặt cherry trên đường đi, mỗi quả chỉ được tính 1 lần. Tìm số lượng cherry tối đa nhặt được.

**Phân tích thuật toán:**
Đi xuống rồi lên tương đương với 2 người cùng đi từ (0, 0) xuống (N-1, N-1).
Trạng thái: 2 người di chuyển cùng số bước `t`, do đó `t = r1 + c1 = r2 + c2`.
Có thể biểu diễn trạng thái qua 3 biến: `(r1, c1, r2)` vì `c2 = r1 + c1 - r2`.
Sử dụng 3D DP (được coi là biến thể cao cấp của 2D DP) để theo dõi song song hai đường đi.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    int[][][] memo;
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        memo = new int[n][n][n];
        for (int[][] m2D : memo) {
            for (int[] m1D : m2D) Arrays.fill(m1D, Integer.MIN_VALUE);
        }
        return Math.max(0, dp(0, 0, 0, grid));
    }
    
    private int dp(int r1, int c1, int r2, int[][] grid) {
        int n = grid.length;
        int c2 = r1 + c1 - r2;
        
        if (r1 == n || c1 == n || r2 == n || c2 == n || 
            grid[r1][c1] == -1 || grid[r2][c2] == -1) {
            return -999999;
        }
        if (r1 == n - 1 && c1 == n - 1) return grid[n - 1][n - 1];
        if (memo[r1][c1][r2] != Integer.MIN_VALUE) return memo[r1][c1][r2];
        
        int ans = grid[r1][c1];
        if (r1 != r2 || c1 != c2) ans += grid[r2][c2]; // Không đếm trùng
        
        int temp = Math.max(
            Math.max(dp(r1 + 1, c1, r2 + 1, grid), dp(r1 + 1, c1, r2, grid)),
            Math.max(dp(r1, c1 + 1, r2 + 1, grid), dp(r1, c1 + 1, r2, grid))
        );
        
        ans += temp;
        memo[r1][c1][r2] = ans;
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N^3)$ số lượng trạng thái là $N \times N \times N$.
- Không gian: $O(N^3)$ cho mảng memoization.

---

## 9. Knight Probability in Chessboard (Xác suất quân Mã trên bàn cờ)

**Đề bài chi tiết:**
Trên bàn cờ `n x n`, một quân mã ở ô `(row, column)` và có `k` bước di chuyển. Xác suất mã nằm trên bàn cờ sau `k` bước là bao nhiêu? Ở mỗi bước, mã chọn 1 trong 8 hướng di chuyển hợp lệ với xác suất đều nhau (1/8).

**Phân tích thuật toán:**
Dùng mảng 3 chiều `dp[step][i][j]` (hoặc mảng 2 chiều liên tục luân phiên `dp` và `nextDp`) để lưu xác suất ở ô `(i, j)` tại bước `step`.
- *State Transition:* `nextDp[i][j] += dp[r][c] / 8.0` cho 8 hướng di chuyển từ `(r, c)`.

**Mã nguồn Java:**
```java
public class Solution {
    public double knightProbability(int n, int k, int row, int column) {
        int[][] dirs = {{-2,-1},{-2,1},{-1,-2},{-1,2},{1,-2},{1,2},{2,-1},{2,1}};
        double[][] dp = new double[n][n];
        dp[row][column] = 1.0;
        
        for (int step = 0; step < k; step++) {
            double[][] nextDp = new double[n][n];
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (dp[i][j] > 0) {
                        for (int[] dir : dirs) {
                            int r = i + dir[0];
                            int c = j + dir[1];
                            if (r >= 0 && r < n && c >= 0 && c < n) {
                                nextDp[r][c] += dp[i][j] / 8.0;
                            }
                        }
                    }
                }
            }
            dp = nextDp;
        }
        
        double prob = 0.0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                prob += dp[i][j];
            }
        }
        return prob;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(K \times N^2)$.
- Không gian: $O(N^2)$ (đã tối ưu trạng thái 3D về 2 mảng 2D lân cận).

---

## 10. Out of Boundary Paths (Đường đi ra ngoài biên)

**Đề bài chi tiết:**
Cho lưới `m x n`. Quả bóng xuất phát từ `(startRow, startColumn)`. Bạn được di chuyển tối đa `maxMove` bước, mỗi bước sang các ô lân cận dọc hoặc ngang. Số đường đi để bóng ra khỏi lưới là bao nhiêu (kết quả modulo $10^9 + 7$)?

**Phân tích thuật toán:**
Giống bài 9, ta dùng DP 3D lưu ở mỗi ô số cách để đến được ô đó với đúng `step` bước. Nếu bước tiếp theo ra ngoài biên, ta cộng số cách vào tổng kết quả.
- *State Transition:* Tổng hợp số lượng các đường đi từ 4 hướng kề bên với bước ít hơn 1.

**Mã nguồn Java:**
```java
public class Solution {
    public int findPaths(int m, int n, int maxMove, int startRow, int startColumn) {
        if (maxMove == 0) return 0;
        int MOD = 1_000_000_007;
        int[][] dp = new int[m][n];
        dp[startRow][startColumn] = 1;
        int count = 0;
        
        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        
        for (int step = 1; step <= maxMove; step++) {
            int[][] temp = new int[m][n];
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (dp[i][j] > 0) {
                        for (int[] dir : dirs) {
                            int r = i + dir[0];
                            int c = j + dir[1];
                            if (r < 0 || r >= m || c < 0 || c >= n) {
                                count = (count + dp[i][j]) % MOD;
                            } else {
                                temp[r][c] = (temp[r][c] + dp[i][j]) % MOD;
                            }
                        }
                    }
                }
            }
            dp = temp;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\text{maxMove} \times m \times n)$.
- Không gian: $O(m \times n)$ với sự luân phiên giữa 2 mảng trạng thái.

---

## 11. Longest Common Subsequence (Chuỗi con chung dài nhất)

**Đề bài chi tiết:**
Cho hai chuỗi `text1` và `text2`, hãy trả về độ dài của chuỗi con chung dài nhất của chúng. Nếu không có chuỗi con chung nào, hãy trả về 0. Một chuỗi con của một chuỗi là một chuỗi mới được tạo ra từ chuỗi ban đầu bằng cách xóa một số (có thể không xóa) ký tự mà không thay đổi vị trí tương đối của các ký tự còn lại.

**Phân tích thuật toán:**
Sử dụng mảng 2D `dp` kích thước `(m+1) x (n+1)`, trong đó `dp[i][j]` là độ dài chuỗi con chung dài nhất của `text1[0..i-1]` và `text2[0..j-1]`.
- *Base case:* Khi một trong hai chuỗi rỗng (`i=0` hoặc `j=0`), `dp[i][j] = 0`.
- *State Transition:* 
  Nếu `text1.charAt(i-1) == text2.charAt(j-1)`, thì ký tự này thuộc về chuỗi con chung: `dp[i][j] = 1 + dp[i-1][j-1]`. 
  Ngược lại, lấy giá trị lớn nhất khi bỏ bớt 1 ký tự từ chuỗi này hoặc chuỗi kia: `dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1])`.

**Mã nguồn Java:**
```java
public class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        return dp[m][n];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$ (có thể tối ưu không gian xuống $O(\min(m, n))$).

---

## 12. Edit Distance (Khoảng cách chỉnh sửa)

**Đề bài chi tiết:**
Cho hai chuỗi `word1` và `word2`, trả về số lượng thao tác tối thiểu cần thiết để chuyển đổi `word1` thành `word2`. Các thao tác cho phép trên một từ bao gồm: chèn 1 ký tự, xóa 1 ký tự, hoặc thay thế 1 ký tự.

**Phân tích thuật toán:**
Sử dụng mảng 2D `dp[i][j]` để lưu số thao tác tối thiểu để chuyển `word1[0..i-1]` thành `word2[0..j-1]`.
- *Base case:* `dp[i][0] = i` (xóa toàn bộ `i` ký tự), và `dp[0][j] = j` (chèn toàn bộ `j` ký tự).
- *State Transition:* 
  Nếu ký tự giống nhau `word1.charAt(i-1) == word2.charAt(j-1)`: Không tốn thao tác nào, `dp[i][j] = dp[i-1][j-1]`.
  Nếu khác nhau: Cần ít nhất 1 thao tác (thay thế, xóa, hoặc chèn), `dp[i][j] = 1 + Math.min(dp[i-1][j-1], Math.min(dp[i-1][j], dp[i][j-1]))`.

**Mã nguồn Java:**
```java
public class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 0; i <= m; i++) dp[i][0] = i;
        for (int j = 0; j <= n; j++) dp[0][j] = j;
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], 
                                   Math.min(dp[i - 1][j], dp[i][j - 1]));
                }
            }
        }
        
        return dp[m][n];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$.

---

## 13. Number of Paths with Max Score (Số đường đi với điểm tối đa)

**Đề bài chi tiết:**
Cho một bảng lưới chứa các ký tự `'E'` (điểm bắt đầu - góc dưới cùng bên phải), `'S'` (điểm kết thúc - góc trên cùng bên trái), `'X'` (vật cản) và các chữ số `'1'-'9'`. Đi từ `'E'` đến `'S'`, mỗi bước chỉ có thể đi lên trên, sang trái hoặc chéo lên trên-trái. Điểm của đường đi là tổng các chữ số đi qua. Hãy tìm điểm tối đa có thể đạt được và số lượng đường đi đạt điểm tối đa đó (modulo $10^9+7$). Nếu không có đường đi nào, trả về `[0, 0]`.

**Phân tích thuật toán:**
Duyệt ngược từ dưới cùng bên phải (`'E'`) dần lên trên cùng bên trái (`'S'`).
Dùng hai mảng 2D: `score[i][j]` (điểm lớn nhất từ ô này đến đích) và `paths[i][j]` (số cách đạt được điểm lớn nhất).
- *State Transition:* Từ ô `(i, j)`, ta xem xét 3 ô lân cận `(i+1, j)`, `(i, j+1)`, `(i+1, j+1)`. Lấy điểm lớn nhất trong 3 ô đó, nếu hợp lệ thì cập nhật cho ô hiện tại. Cộng dồn số cách đi từ những hướng có điểm lớn nhất đó.

**Mã nguồn Java:**
```java
import java.util.List;

public class Solution {
    public int[] pathsWithMaxScore(List<String> board) {
        int n = board.size();
        int MOD = 1_000_000_007;
        int[][] score = new int[n][n];
        int[][] paths = new int[n][n];
        
        paths[n - 1][n - 1] = 1;
        
        int[][] dirs = {{1, 0}, {0, 1}, {1, 1}};
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                char c = board.get(i).charAt(j);
                if (c == 'X' || paths[i][j] == 0 && !(i == n - 1 && j == n - 1)) {
                    continue;
                }
                for (int[] dir : dirs) {
                    int r = i - dir[0], cIdx = j - dir[1];
                    if (r >= 0 && cIdx >= 0 && board.get(r).charAt(cIdx) != 'X') {
                        int add = (r == 0 && cIdx == 0) ? 0 : board.get(r).charAt(cIdx) - '0';
                        if (score[r][cIdx] < score[i][j] + add) {
                            score[r][cIdx] = score[i][j] + add;
                            paths[r][cIdx] = paths[i][j];
                        } else if (score[r][cIdx] == score[i][j] + add) {
                            paths[r][cIdx] = (paths[r][cIdx] + paths[i][j]) % MOD;
                        }
                    }
                }
            }
        }
        
        return new int[]{paths[0][0] > 0 ? score[0][0] : 0, paths[0][0]};
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n^2)$ với $n$ là kích thước của lưới.
- Không gian (Space Complexity): $O(n^2)$.

---

## 14. Maximum Non Negative Product in a Matrix (Tích số không âm lớn nhất trong ma trận)

**Đề bài chi tiết:**
Cho một ma trận nguyên `grid` kích thước `m x n`. Bạn bắt đầu từ góc trên cùng bên trái `(0,0)` và muốn đến góc dưới cùng bên phải `(m-1, n-1)`. Tại mỗi bước, chỉ được di chuyển xuống hoặc sang phải. Tích của đường đi là tích tất cả các phần tử đã duyệt qua. Tìm tích lớn nhất sao cho $\ge 0$ và trả về kết quả sau khi chia modulo $10^9+7$. Trả về -1 nếu không có đường đi nào có tích $\ge 0$.

**Phân tích thuật toán:**
Do ma trận có thể chứa số âm, tích lớn nhất có thể được tạo ra từ 2 số âm cực nhỏ nhân với nhau. Do đó, ta phải lưu cả tích nhỏ nhất (`minVal`) và lớn nhất (`maxVal`) cho đến ô hiện tại.
- *State Transition:* Tại `(i, j)`, ta xét tích của `grid[i][j]` với các giá trị `maxVal` và `minVal` từ ô bên trên `(i-1, j)` và ô bên trái `(i, j-1)`. Cập nhật `maxVal[i][j]` là GTLN và `minVal[i][j]` là GTNN của các tổ hợp đó.

**Mã nguồn Java:**
```java
public class Solution {
    public int maxProductPath(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        long[][] maxVal = new long[m][n];
        long[][] minVal = new long[m][n];
        
        maxVal[0][0] = minVal[0][0] = grid[0][0];
        
        for (int i = 1; i < m; i++) {
            maxVal[i][0] = minVal[i][0] = maxVal[i - 1][0] * grid[i][0];
        }
        for (int j = 1; j < n; j++) {
            maxVal[0][j] = minVal[0][j] = maxVal[0][j - 1] * grid[0][j];
        }
        
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                long p1 = maxVal[i - 1][j] * grid[i][j];
                long p2 = minVal[i - 1][j] * grid[i][j];
                long p3 = maxVal[i][j - 1] * grid[i][j];
                long p4 = minVal[i][j - 1] * grid[i][j];
                
                maxVal[i][j] = Math.max(Math.max(p1, p2), Math.max(p3, p4));
                minVal[i][j] = Math.min(Math.min(p1, p2), Math.min(p3, p4));
            }
        }
        
        long ans = maxVal[m - 1][n - 1];
        if (ans < 0) return -1;
        return (int) (ans % 1_000_000_007);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$.

---

## 15. Paint House II (Tô màu ngôi nhà II)

**Đề bài chi tiết:**
Có $N$ ngôi nhà xếp thành hàng và $K$ loại màu sắc khác nhau. Chi phí để tô màu ngôi nhà $i$ bằng màu $j$ được cho bởi ma trận `costs[i][j]`. Hãy tìm tổng chi phí tối thiểu để tô màu cho toàn bộ các ngôi nhà sao cho không có 2 ngôi nhà kề nhau nào được tô cùng một màu.

**Phân tích thuật toán:**
Dùng mảng 2D `dp[i][j]` để lưu chi phí tối thiểu tô $i$ ngôi nhà đầu tiên với nhà $i$ mang màu $j$.
Để giảm độ phức tạp từ $O(N \times K^2)$ xuống $O(N \times K)$, ở mỗi hàng ta tìm màu có chi phí nhỏ nhất (min1) và nhỏ thứ hai (min2). 
Khi xét ngôi nhà tiếp theo, nếu ta chọn màu $j$ không trùng với màu min1 trước đó, ta cộng min1. Nếu chọn $j$ trùng màu min1, ta bắt buộc phải cộng min2.

**Mã nguồn Java:**
```java
public class Solution {
    public int minCostII(int[][] costs) {
        if (costs == null || costs.length == 0) return 0;
        int n = costs.length, k = costs[0].length;
        
        int min1 = 0, min2 = 0, minColor = -1;
        
        for (int i = 0; i < n; i++) {
            int currMin1 = Integer.MAX_VALUE, currMin2 = Integer.MAX_VALUE, currMinColor = -1;
            
            for (int j = 0; j < k; j++) {
                int cost = costs[i][j] + (j == minColor ? min2 : min1);
                
                if (cost < currMin1) {
                    currMin2 = currMin1;
                    currMin1 = cost;
                    currMinColor = j;
                } else if (cost < currMin2) {
                    currMin2 = cost;
                }
            }
            min1 = currMin1;
            min2 = currMin2;
            minColor = currMinColor;
        }
        
        return min1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n \times k)$.
- Không gian (Space Complexity): $O(1)$ (do chỉ cần lưu min1, min2 của trạng thái trước).

---

## 16. Interleaving String (Chuỗi xen kẽ)

**Đề bài chi tiết:**
Cho 3 chuỗi `s1`, `s2` và `s3`. Kiểm tra xem `s3` có được tạo ra bằng cách xen kẽ các ký tự của `s1` và `s2` (giữ nguyên thứ tự ban đầu của chúng) hay không.

**Phân tích thuật toán:**
Dùng mảng 2D boolean `dp[i][j]` đại diện cho việc `s1[0..i-1]` và `s2[0..j-1]` có tạo thành được `s3[0..i+j-1]` hay không.
- *Base case:* `dp[0][0] = true`.
- *State Transition:* 
  Nếu `s1.charAt(i-1) == s3.charAt(i+j-1)`, trạng thái phụ thuộc vào `dp[i-1][j]`.
  Nếu `s2.charAt(j-1) == s3.charAt(i+j-1)`, trạng thái phụ thuộc vào `dp[i][j-1]`.
  Kết hợp lại bằng phép OR lôgic.

**Mã nguồn Java:**
```java
public class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int m = s1.length(), n = s2.length();
        if (m + n != s3.length()) return false;
        
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        
        for (int i = 1; i <= m; i++) {
            dp[i][0] = dp[i - 1][0] && s1.charAt(i - 1) == s3.charAt(i - 1);
        }
        for (int j = 1; j <= n; j++) {
            dp[0][j] = dp[0][j - 1] && s2.charAt(j - 1) == s3.charAt(j - 1);
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                char c = s3.charAt(i + j - 1);
                dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == c) ||
                           (dp[i][j - 1] && s2.charAt(j - 1) == c);
            }
        }
        
        return dp[m][n];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$ (có thể tối ưu không gian xuống $O(n)$).

---

## 17. Count Square Submatrices with All Ones (Đếm ma trận vuông chứa toàn 1)

**Đề bài chi tiết:**
Cho ma trận `m x n` gồm các số `0` và `1`, đếm xem có tất cả bao nhiêu ma trận con hình vuông chứa toàn số `1`.

**Phân tích thuật toán:**
Dùng mảng 2D `dp[i][j]` lưu độ dài cạnh lớn nhất của hình vuông kết thúc tại góc dưới phải `(i, j)`.
Do tính chất của hình vuông, nếu có một hình vuông cạnh $K$ tại đó thì nó sẽ tự động bao hàm 1 hình vuông cạnh $K-1$, 1 hình vuông $K-2$,... nghĩa là tổng số hình vuông toàn 1 kết thúc tại `(i, j)` chính bằng `dp[i][j]`.
- *State Transition:* `dp[i][j] = 1 + Math.min(dp[i-1][j-1], Math.min(dp[i-1][j], dp[i][j-1]))` nếu `matrix[i][j] == 1`. Tổng các `dp[i][j]` là kết quả cuối cùng.

**Mã nguồn Java:**
```java
public class Solution {
    public int countSquares(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int[][] dp = new int[m][n];
        int count = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], 
                                       Math.min(dp[i - 1][j], dp[i][j - 1]));
                    }
                    count += dp[i][j];
                }
            }
        }
        
        return count;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$ (có thể thực hiện in-place để đạt $O(1)$ không gian).

---

## 18. Matrix Block Sum (Tổng khối ma trận)

**Đề bài chi tiết:**
Cho một ma trận nguyên `mat` kích thước $m \times n$ và số nguyên $k$. Trả về ma trận `answer` cùng kích thước trong đó `answer[i][j]` là tổng tất cả các phần tử `mat[r][c]` trong phạm vi bao quanh nó: `i - k <= r <= i + k` và `j - k <= c <= j + k`.

**Phân tích thuật toán:**
Dùng mảng Tổng tiền tố 2D (2D Prefix Sum) để tăng tốc độ truy vấn tổng khối.
- Tính `dp[i][j]` là tổng các phần tử trong hình chữ nhật từ `(0, 0)` tới `(i-1, j-1)`.
- Khi cần tổng khối từ `(r1, c1)` tới `(r2, c2)`, ta áp dụng công thức nguyên lý bao hàm loại trừ: `sum = dp[r2+1][c2+1] - dp[r1][c2+1] - dp[r2+1][c1] + dp[r1][c1]`. Chú ý tính toán chính xác giới hạn biên.

**Mã nguồn Java:**
```java
public class Solution {
    public int[][] matrixBlockSum(int[][] mat, int k) {
        int m = mat.length, n = mat[0].length;
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = mat[i - 1][j - 1] + dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1];
            }
        }
        
        int[][] ans = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int r1 = Math.max(0, i - k);
                int c1 = Math.max(0, j - k);
                int r2 = Math.min(m - 1, i + k);
                int c2 = Math.min(n - 1, j + k);
                
                ans[i][j] = dp[r2 + 1][c2 + 1] - dp[r1][c2 + 1] - dp[r2 + 1][c1] + dp[r1][c1];
            }
        }
        
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$.

---

## 19. Minimum Falling Path Sum II (Tổng đường đi rơi nhỏ nhất II)

**Đề bài chi tiết:**
Cho ma trận vuông `grid` kích thước `n x n`. Hãy tìm tổng nhỏ nhất của đường đi xuyên qua ma trận. Khác với bài cơ bản, bước đi từ hàng `i` xuống hàng `i+1` có thể chọn *bất kỳ* ô nào, miễn là nó không nằm cùng cột với ô hiện tại.

**Phân tích thuật toán:**
Thay vì lặp lại toàn bộ hàng trước ($O(n^3)$), ta có thể tối ưu bằng cách lưu lại 2 giá trị nhỏ nhất của hàng trước: `min1` (GNN thứ nhất) và `min2` (GNN thứ hai) kèm theo chỉ số cột của `min1`.
- Khi tính cho hàng hiện tại, nếu ô đang xét không nằm cùng cột với `min1`, ta cộng giá trị nó với `min1`. Nếu cùng cột, ta bị cấm chọn `min1`, nên cộng với `min2`.

**Mã nguồn Java:**
```java
public class Solution {
    public int minFallingPathSum(int[][] grid) {
        int n = grid.length;
        int min1 = 0, min2 = 0, minCol = -1;
        
        for (int i = 0; i < n; i++) {
            int currMin1 = Integer.MAX_VALUE, currMin2 = Integer.MAX_VALUE, currCol = -1;
            
            for (int j = 0; j < n; j++) {
                int cost = grid[i][j] + (j == minCol ? min2 : min1);
                
                if (cost < currMin1) {
                    currMin2 = currMin1;
                    currMin1 = cost;
                    currCol = j;
                } else if (cost < currMin2) {
                    currMin2 = cost;
                }
            }
            min1 = currMin1;
            min2 = currMin2;
            minCol = currCol;
        }
        
        return min1;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n^2)$.
- Không gian (Space Complexity): $O(1)$.

---

## 20. Paths in Matrix Whose Sum Is Divisible by K (Số đường đi trong ma trận chia hết cho K)

**Đề bài chi tiết:**
Cho ma trận nguyên `grid` $m \times n$ và số nguyên $k$. Bắt đầu từ `(0, 0)` đi xuống dưới hoặc sang phải đến `(m-1, n-1)`. Đếm số lượng đường đi mà tổng các phần tử chia hết cho $k$. Kết quả trả về modulo $10^9+7$.

**Phân tích thuật toán:**
Dùng mảng 3D `dp[i][j][rem]` lưu số lượng đường đi đến ô `(i, j)` có số dư `rem` khi chia tổng cho $k$.
- *State Transition:* Tại ô `(i, j)`, ta xét tất cả số dư `r` từ 0 đến $k-1$ của các ô bên trên `(i-1, j)` và bên trái `(i, j-1)`. Trạng thái dư mới `newRem = (r + grid[i][j]) % k`. Ta cộng dồn số lượng đường đi vào `dp[i][j][newRem]`.

**Mã nguồn Java:**
```java
public class Solution {
    public int numberOfPaths(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        int MOD = 1_000_000_007;
        int[][][] dp = new int[m][n][k];
        
        dp[0][0][grid[0][0] % k] = 1;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                for (int rem = 0; rem < k; rem++) {
                    if (dp[i][j][rem] == 0) continue;
                    
                    if (i + 1 < m) {
                        int nextRem = (rem + grid[i + 1][j]) % k;
                        dp[i + 1][j][nextRem] = (dp[i + 1][j][nextRem] + dp[i][j][rem]) % MOD;
                    }
                    if (j + 1 < n) {
                        int nextRem = (rem + grid[i][j + 1]) % k;
                        dp[i][j + 1][nextRem] = (dp[i][j + 1][nextRem] + dp[i][j][rem]) % MOD;
                    }
                }
            }
        }
        
        return dp[m - 1][n - 1][0];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n \times k)$.
- Không gian (Space Complexity): $O(m \times n \times k)$ (có thể thu gọn còn $O(n \times k)$ bằng cách chỉ giữ lại hàng trước).

---

## 21. Maximum Number of Points with Cost (Điểm số tối đa với chi phí)

**Đề bài chi tiết:**
Cho ma trận `points` kích thước `m x n`. Tại mỗi hàng, bạn phải chọn đúng một ô. Chi phí để chọn ô ở cột `c1` của hàng trước và cột `c2` của hàng hiện tại là `abs(c1 - c2)`. Trả về số điểm tối đa có thể đạt được sau khi chọn từ hàng đầu đến hàng cuối.

**Phân tích thuật toán:**
Dùng mảng 1 chiều `dp` độ dài `n` để lưu số điểm tối đa khi kết thúc ở cột `j` của hàng trước.
Khi sang hàng mới, nếu duyệt trực tiếp sẽ tốn $O(n^2)$. Để tối ưu thành $O(n)$, ta chia việc tìm max thành 2 mảng:
- Quét từ trái sang phải: `left[j] = Math.max(left[j-1] - 1, dp[j])`
- Quét từ phải sang trái: `right[j] = Math.max(right[j+1] - 1, dp[j])`
Tại hàng mới, điểm tối đa nếu chọn cột `j` sẽ là: `new_dp[j] = points[i][j] + Math.max(left[j], right[j])`.

**Mã nguồn Java:**
```java
public class Solution {
    public long maxPoints(int[][] points) {
        int m = points.length, n = points[0].length;
        long[] dp = new long[n];
        
        for (int j = 0; j < n; j++) dp[j] = points[0][j];
        
        for (int i = 1; i < m; i++) {
            long[] left = new long[n];
            long[] right = new long[n];
            long[] new_dp = new long[n];
            
            left[0] = dp[0];
            for (int j = 1; j < n; j++) {
                left[j] = Math.max(left[j - 1] - 1, dp[j]);
            }
            
            right[n - 1] = dp[n - 1];
            for (int j = n - 2; j >= 0; j--) {
                right[j] = Math.max(right[j + 1] - 1, dp[j]);
            }
            
            for (int j = 0; j < n; j++) {
                new_dp[j] = points[i][j] + Math.max(left[j], right[j]);
            }
            dp = new_dp;
        }
        
        long ans = 0;
        for (long val : dp) ans = Math.max(ans, val);
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$ nhờ kỹ thuật quét trái/phải.
- Không gian (Space Complexity): $O(n)$.

---

## 22. Path with Maximum Gold (Đường đi chứa nhiều vàng nhất)

**Đề bài chi tiết:**
Cho ma trận `grid` đại diện cho một mỏ vàng. Mỗi ô chứa một số vàng. Bạn có thể bắt đầu và kết thúc ở bất kỳ ô nào có vàng, và di chuyển lên, xuống, trái, phải. Không được bước vào ô có giá trị `0` (không có vàng). Mỗi ô chỉ được thăm đúng 1 lần. Trả về lượng vàng lớn nhất có thể thu thập được.

**Phân tích thuật toán:**
Do ràng buộc của mỏ vàng thường khá nhỏ (ví dụ $15 \times 15$ với nhiều ô 0), ta có thể sử dụng Backtracking kết hợp DFS. Thực chất đây là thuật toán vét cạn trên lưới.
Tại mỗi ô có vàng, ta khởi tạo DFS. Đánh dấu ô đang xét thành 0 (để tránh đi lại), sau đó đi tiếp vào 4 hướng và cuối cùng trả lại giá trị cũ (backtrack) để thử các đường đi khác.

**Mã nguồn Java:**
```java
public class Solution {
    int maxGold = 0;
    
    public int getMaximumGold(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] > 0) {
                    dfs(grid, i, j, 0);
                }
            }
        }
        return maxGold;
    }
    
    private void dfs(int[][] grid, int i, int j, int currentGold) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
            maxGold = Math.max(maxGold, currentGold);
            return;
        }
        
        int val = grid[i][j];
        currentGold += val;
        grid[i][j] = 0; // Đánh dấu đã thăm
        
        dfs(grid, i + 1, j, currentGold);
        dfs(grid, i - 1, j, currentGold);
        dfs(grid, i, j + 1, currentGold);
        dfs(grid, i, j - 1, currentGold);
        
        grid[i][j] = val; // Khôi phục trạng thái (Backtrack)
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): Lên tới $O(K \times 3^K)$ trong trường hợp tệ nhất, với $K$ là số lượng ô có vàng (do ta đi 4 hướng nhưng 1 hướng bị chặn bởi ô cũ).
- Không gian (Space Complexity): $O(K)$ cho call stack của DFS.

---

## 23. Where Will the Ball Fall (Bóng sẽ rơi ở đâu)

**Đề bài chi tiết:**
Cho ma trận `grid` gồm các số `1` (ván trượt nghiêng từ trái trên xuống phải dưới) và `-1` (nghiêng từ phải trên xuống trái dưới). Thả `n` quả bóng từ đỉnh của `n` cột. Hãy xác định xem mỗi quả bóng sẽ rớt ra ở cột nào dưới cùng của lưới, hoặc trả về `-1` nếu bóng bị kẹt (ví dụ tạo thành hình chữ V hoặc đập vào tường).

**Phân tích thuật toán:**
Có thể giải bằng cách dùng DP hoặc mô phỏng. Do mỗi quả bóng di chuyển độc lập, ta mô phỏng đường đi của từng quả bóng từ hàng 0 xuống hàng $m-1$.
Nếu bóng đang ở cột `j` và `grid[i][j] == 1`, nó có xu hướng lăn sang `j+1`. Nó sẽ lăn được nếu `grid[i][j+1] == 1` và không bị đụng tường phải. Nếu `grid[i][j] == -1`, nó lăn sang `j-1` nếu `grid[i][j-1] == -1` và không đụng tường trái. Ngược lại là bị kẹt.

**Mã nguồn Java:**
```java
public class Solution {
    public int[] findBall(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[] result = new int[n];
        
        for (int j = 0; j < n; j++) {
            int currentCol = j;
            for (int i = 0; i < m; i++) {
                int nextCol = currentCol + grid[i][currentCol];
                
                // Kiểm tra có chạm biên hoặc tạo hình chữ V hay không
                if (nextCol < 0 || nextCol >= n || grid[i][currentCol] != grid[i][nextCol]) {
                    currentCol = -1;
                    break;
                }
                currentCol = nextCol;
            }
            result[j] = currentCol;
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(1)$ (không tính mảng kết quả).

---

## 24. Triangle Paths Count (Đếm số đường đi nhỏ nhất trong tam giác)

**Đề bài chi tiết:**
Dựa trên bài toán Triangle (đường đi có tổng nhỏ nhất). Lần này, ngoài việc tìm tổng nhỏ nhất, bạn phải đếm xem có bao nhiêu đường đi đạt được mức tổng nhỏ nhất đó.

**Phân tích thuật toán:**
Ta dùng cách duyệt Bottom-Up. Thay vì chỉ lưu 1 giá trị `dp[j]` là tổng nhỏ nhất, ta lưu một cặp gồm mảng `minPath[j]` và `count[j]`.
- Tại hàng `i`, ô `(i, j)` nhận được đường đi từ ô `(i+1, j)` và `(i+1, j+1)`.
- Nếu `minPath[j] < minPath[j+1]`, ô đó kế thừa đường đi nhỏ hơn, số cách `count` cũng kế thừa từ `j`.
- Nếu `minPath[j] > minPath[j+1]`, kế thừa từ `j+1`.
- Nếu `minPath[j] == minPath[j+1]`, số cách sẽ là tổng số cách của cả `j` và `j+1`.

**Mã nguồn Java:**
```java
import java.util.List;

public class Solution {
    public int countMinPaths(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] minPath = new int[n];
        int[] count = new int[n];
        
        for (int i = 0; i < n; i++) {
            minPath[i] = triangle.get(n - 1).get(i);
            count[i] = 1;
        }
        
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                int val = triangle.get(i).get(j);
                if (minPath[j] < minPath[j + 1]) {
                    minPath[j] = val + minPath[j];
                    // count[j] giữ nguyên
                } else if (minPath[j] > minPath[j + 1]) {
                    minPath[j] = val + minPath[j + 1];
                    count[j] = count[j + 1];
                } else {
                    minPath[j] = val + minPath[j];
                    count[j] = count[j] + count[j + 1];
                }
            }
        }
        
        return count[0];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n^2)$.
- Không gian (Space Complexity): $O(n)$.

---

## 25. Longest Increasing Path in a Matrix (Đường đi tăng dần dài nhất)

**Đề bài chi tiết:**
Cho một ma trận nguyên `matrix` kích thước `m x n`, hãy tìm chiều dài của đường đi tăng dần ngặt (strictly increasing) dài nhất. Tại mỗi ô, bạn có thể đi theo 4 hướng. Không được di chuyển ra ngoài biên lưới hoặc theo đường chéo.

**Phân tích thuật toán:**
Dùng DFS kết hợp với kỹ thuật Ghi nhớ (Memoization). Mảng `memo[i][j]` lưu trữ độ dài đường đi tăng dần dài nhất xuất phát từ ô `(i, j)`.
Do tính chất "tăng dần ngặt", ta sẽ không bao giờ quay lại một ô đã đi qua, do đó không lo có chu trình lặp vô hạn. Nếu `memo[i][j]` đã được tính toán, ta chỉ việc lấy ra dùng mà không cần duyệt lại DFS.

**Mã nguồn Java:**
```java
public class Solution {
    private static final int[][] DIRS = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length;
        int[][] memo = new int[m][n];
        int maxLen = 1;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                maxLen = Math.max(maxLen, dfs(matrix, i, j, memo));
            }
        }
        return maxLen;
    }
    
    private int dfs(int[][] matrix, int i, int j, int[][] memo) {
        if (memo[i][j] != 0) return memo[i][j];
        
        int max = 1;
        for (int[] dir : DIRS) {
            int x = i + dir[0], y = j + dir[1];
            if (x >= 0 && x < matrix.length && y >= 0 && y < matrix[0].length 
                && matrix[x][y] > matrix[i][j]) {
                max = Math.max(max, 1 + dfs(matrix, x, y, memo));
            }
        }
        
        memo[i][j] = max;
        return max;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$ do mỗi ô chỉ tính đúng 1 lần.
- Không gian (Space Complexity): $O(m \times n)$ cho mảng memo và call stack.

---

## 26. Kth Smallest Instructions (Chỉ dẫn nhỏ thứ K)

**Đề bài chi tiết:**
Một robot xuất phát từ `(0, 0)` và muốn đến `(row, col)`. Robot chỉ được đi `H` (phải) và `V` (xuống). Bạn cần trả về một chuỗi đại diện cho đường đi có thứ tự từ điển nhỏ thứ $k$. Biết rằng chuỗi bắt đầu bằng 'H' luôn có thứ tự nhỏ hơn chuỗi bắt đầu bằng 'V'.

**Phân tích thuật toán:**
Dùng toán tổ hợp và quy hoạch động đếm số đường. Có tổng cộng `R + C` bước đi, trong đó có `R` bước 'V' và `C` bước 'H'. Số cách sắp xếp là tổ hợp chập `R` của `R + C`: $\binom{R+C}{R}$.
Tại mỗi bước đi, nếu ta chọn 'H', số cách sắp xếp phần còn lại là $\binom{rem\_r + rem\_c - 1}{rem\_r}$.
- Nếu $k$ nhỏ hơn hoặc bằng số cách đó, điều này nghĩa là đáp án nằm trong nhánh chọn 'H', ta gắn 'H' vào chuỗi.
- Nếu $k$ lớn hơn số cách đó, nghĩa là nhánh 'H' không đủ chứa đáp án. Ta phải rẽ sang nhánh 'V', trừ $k$ đi số lượng đường nhánh 'H'.

**Mã nguồn Java:**
```java
public class Solution {
    public String kthSmallestPath(int[] destination, int k) {
        int r = destination[0], c = destination[1];
        
        // Tính tổ hợp C(n, k)
        int[][] comb = new int[r + c + 1][r + c + 1];
        for (int i = 0; i <= r + c; i++) {
            comb[i][0] = 1;
            for (int j = 1; j <= i; j++) {
                comb[i][j] = comb[i - 1][j - 1] + comb[i - 1][j];
            }
        }
        
        StringBuilder sb = new StringBuilder();
        while (r > 0 && c > 0) {
            // Số cách nếu ta đi 'H'
            int waysIfH = comb[r + c - 1][r];
            if (k <= waysIfH) {
                sb.append('H');
                c--;
            } else {
                sb.append('V');
                r--;
                k -= waysIfH;
            }
        }
        
        // Gắn phần dư
        while (c-- > 0) sb.append('H');
        while (r-- > 0) sb.append('V');
        
        return sb.toString();
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O((R+C)^2)$ để tính tam giác Pascal, sau đó là $O(R+C)$ để xây dựng chuỗi.
- Không gian (Space Complexity): $O((R+C)^2)$ cho mảng lưu tổ hợp.

---

## 27. Number of Ways to Paint N × 3 Grid (Số cách sơn lưới N x 3)

**Đề bài chi tiết:**
Có một lưới kích thước `n x 3` và bạn có 3 màu (Đỏ, Vàng, Xanh lục). Mỗi ô trong lưới phải được tô đúng 1 màu sao cho không có 2 ô kề cạnh (ngang và dọc) nào cùng màu. Tính tổng số cách sơn lưới (modulo $10^9+7$).

**Phân tích thuật toán:**
Xét một hàng gồm 3 ô, có 2 kiểu sơn:
- Cấu hình 3 màu khác nhau ("ABC"): Có 6 cách chọn (VD: Đ-V-X, V-Đ-X,...).
- Cấu hình 2 màu giống nhau ở 2 bên ("ABA"): Có 6 cách chọn (VD: Đ-V-Đ, X-Đ-X,...).
Mối liên hệ giữa các hàng:
- Nếu hàng trước là "ABC": Ta có thể ráp vào dưới nó 2 kiểu "ABC" và 2 kiểu "ABA".
- Nếu hàng trước là "ABA": Ta có thể ráp vào dưới nó 2 kiểu "ABC" và 3 kiểu "ABA".
Gọi `color3` là số mẫu "ABC" và `color2` là số mẫu "ABA". Ta có công thức truy hồi:
`new_color3 = color3 * 2 + color2 * 2`
`new_color2 = color3 * 2 + color2 * 3`

**Mã nguồn Java:**
```java
public class Solution {
    public int numOfWays(int n) {
        long color3 = 6;
        long color2 = 6;
        long MOD = 1_000_000_007;
        
        for (int i = 2; i <= n; i++) {
            long tempColor3 = (color3 * 2 + color2 * 2) % MOD;
            long tempColor2 = (color3 * 2 + color2 * 3) % MOD;
            
            color3 = tempColor3;
            color2 = tempColor2;
        }
        
        return (int) ((color3 + color2) % MOD);
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n)$ (có thể tối ưu thành $O(\log n)$ nhờ nhân ma trận lũy thừa).
- Không gian (Space Complexity): $O(1)$.

---

## 28. Profitable Schemes (Kế hoạch sinh lời)

**Đề bài chi tiết:**
Có một nhóm gồm `n` người phạm tội. Bạn được cung cấp danh sách các tội phạm. Tội phạm thứ `i` cần `group[i]` người để thực hiện và mang lại lợi nhuận `profit[i]`. Bạn có thể chọn thực hiện bất kỳ tội phạm nào miễn là tổng số người thực hiện không vượt quá `n`. Hãy đếm số cách (modulo $10^9+7$) để đạt được mức lợi nhuận ít nhất là `minProfit`.

**Phân tích thuật toán:**
Đây là dạng bài toán Cái túi (Knapsack Problem) 2 chiều (người và lợi nhuận).
Mảng DP `dp[p][g]` đại diện cho số cách chọn ra một số tội phạm đạt được mức lợi nhuận `p` và dùng đúng `g` người.
Vì ta chỉ quan tâm tới những kế hoạch đạt "ít nhất" `minProfit`, các mức lợi nhuận lớn hơn đều có thể dồn (cap) vào ô `minProfit`.
`dp[min(p + profit[i], minProfit)][g + group[i]] += dp[p][g]`

**Mã nguồn Java:**
```java
public class Solution {
    public int profitableSchemes(int n, int minProfit, int[] group, int[] profit) {
        int MOD = 1_000_000_007;
        int[][] dp = new int[minProfit + 1][n + 1];
        dp[0][0] = 1;
        
        for (int k = 0; k < group.length; k++) {
            int g = group[k];
            int p = profit[k];
            // Phải duyệt lùi để tránh sử dụng lại 1 tội phạm nhiều lần
            for (int i = minProfit; i >= 0; i--) {
                for (int j = n - g; j >= 0; j--) {
                    int nextP = Math.min(i + p, minProfit);
                    dp[nextP][j + g] = (dp[nextP][j + g] + dp[i][j]) % MOD;
                }
            }
        }
        
        int ans = 0;
        for (int j = 0; j <= n; j++) {
            ans = (ans + dp[minProfit][j]) % MOD;
        }
        return ans;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(\text{len}(group) \times \text{minProfit} \times n)$.
- Không gian (Space Complexity): $O(\text{minProfit} \times n)$.

---

## 29. Video Stitching (Nối video)

**Đề bài chi tiết:**
Bạn có một loạt các đoạn video thể thao đại diện dưới dạng các khoảng thời gian `clips[i] = [start, end]`. Bạn cần cắt các đoạn này và ghép lại để tạo ra một đoạn video liên tục bao phủ hoàn toàn từ `0` đến `time`. Hãy trả về số lượng đoạn video tối thiểu cần thiết, hoặc `-1` nếu không thể tạo ra được.

**Phân tích thuật toán:**
Sử dụng mảng DP 1 chiều `dp[i]` biểu diễn số lượng đoạn video ít nhất để bao phủ mốc thời gian từ `0` đến `i`.
Khởi tạo toàn bộ mảng là một số cực lớn, với `dp[0] = 0`.
Đối với mỗi giây `i` từ 1 tới `time`, ta duyệt qua tất cả các `clips`. Nếu khoảng thời gian của clip `[s, e]` chứa đựng `i` (tức là $s \le i \le e$), ta có thể đạt được mốc thời gian `i` với chi phí là `dp[s] + 1`. Chọn clip cho chi phí nhỏ nhất.

**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int videoStitching(int[][] clips, int time) {
        int[] dp = new int[time + 1];
        Arrays.fill(dp, time + 10);
        dp[0] = 0;
        
        for (int i = 1; i <= time; i++) {
            for (int[] clip : clips) {
                if (clip[0] <= i && i <= clip[1]) {
                    dp[i] = Math.min(dp[i], dp[clip[0]] + 1);
                }
            }
        }
        
        return dp[time] >= time + 10 ? -1 : dp[time];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(T \times N)$ với $T$ là `time` và $N$ là số lượng đoạn clip.
- Không gian (Space Complexity): $O(T)$.
*(Lưu ý: Có thuật toán tham lam Greedy tốn $O(N \log N)$ để giải quyết bài này tối ưu hơn).*

---

## 30. Regular Expression Matching (Khớp chuỗi biểu thức chính quy)

**Đề bài chi tiết:**
Cho một chuỗi `s` và một mẫu `p`, hãy cài đặt hàm hỗ trợ biểu thức chính quy với ký tự `'.'` và `'*'`.
- `'.'` Khớp với bất kỳ ký tự đơn lẻ nào.
- `'*'` Khớp với 0 hoặc nhiều lần ký tự xuất hiện ngay trước nó.
Chuỗi `s` khớp hoàn toàn với mẫu `p` chứ không phải khớp một phần.

**Phân tích thuật toán:**
Dùng mảng 2D `dp[i][j]` là `true` nếu `s[0..i-1]` khớp với `p[0..j-1]`.
- *Base case:* `dp[0][0] = true`. Nếu chuỗi `s` rỗng, mẫu `p` có thể khớp nếu có dạng `x*y*z*`.
- *State Transition:* 
  - Nếu `p[j-1] == '.'` hoặc `s[i-1] == p[j-1]`: `dp[i][j] = dp[i-1][j-1]`.
  - Nếu `p[j-1] == '*'`: Có hai trường hợp.
    + Không xài phần tử trước `*` (khớp 0 lần): `dp[i][j] = dp[i][j-2]`.
    + Có xài (khớp $\ge 1$ lần): Nếu ký tự trước `*` khớp với ký tự hiện tại của `s`, `dp[i][j] = dp[i][j] | dp[i-1][j]`.

**Mã nguồn Java:**
```java
public class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        
        dp[0][0] = true;
        
        // Khởi tạo hàng đầu tiên: chuỗi s rỗng
        for (int j = 2; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                char cs = s.charAt(i - 1);
                char cp = p.charAt(j - 1);
                
                if (cp == '.' || cp == cs) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (cp == '*') {
                    char prevCp = p.charAt(j - 2);
                    // Bỏ qua ký tự trước '*'
                    dp[i][j] = dp[i][j - 2];
                    
                    // Nếu ký tự trước '*' khớp với cs
                    if (prevCp == '.' || prevCp == cs) {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                }
            }
        }
        
        return dp[m][n];
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(m \times n)$.
- Không gian (Space Complexity): $O(m \times n)$.
