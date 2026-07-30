# 04. Knapsack and Subset DP - Problems

## 1. Partition Equal Subset Sum
### Đề bài chi tiết
Cho một mảng số nguyên dương `nums`, hãy kiểm tra xem mảng có thể được chia thành hai tập con (subset) sao cho tổng các phần tử của hai tập con này bằng nhau hay không.
### Phân tích thuật toán
- Để chia mảng thành hai phần có tổng bằng nhau, tổng của toàn bộ mảng bắt buộc phải là số chẵn. Nếu tổng lẻ, ta trả về `false`.
- Mục tiêu là tìm một tập con có tổng bằng `Target = Sum / 2`. 
- Đây chính là bài toán 0/1 Knapsack, nơi `weight` của các phần tử là giá trị của nó, và mảng DP lưu trữ trạng thái Boolean (có thể tạo ra tổng `W` hay không). Sử dụng mảng 1D, duyệt từ phải qua trái.
### Mã nguồn Java
```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % 2 != 0) return false;
        
        int target = sum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        
        for (int num : nums) {
            for (int i = target; i >= num; i--) {
                dp[i] = dp[i] || dp[i - num];
            }
        }
        return dp[target];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times \frac{Sum}{2})$
- Không gian: $\mathcal{O}(\frac{Sum}{2})$ với không gian mảng 1D.

---

## 2. Target Sum
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `target`. Với mỗi phần tử, bạn có thể thêm dấu `+` hoặc `-` ở đằng trước. Trả về số lượng các biểu thức có thể đánh giá đạt được đúng bằng `target`.
### Phân tích thuật toán
- Gọi $S1$ là tổng các phần tử mang dấu dương, $S2$ là tổng mang dấu âm.
- Ta có $S1 + S2 = Sum$ và $S1 - S2 = Target$.
- Từ đó suy ra $S1 = (Sum + Target) / 2$.
- Vậy bài toán được biến đổi thành: Tìm số lượng các tập con có tổng đúng bằng $S1$. Khởi tạo `dp[0] = 1`, bài toán trở thành Subset Sum đếm số lượng cách (0/1 Knapsack 1D).
### Mã nguồn Java
```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for (int x : nums) sum += x;
        if (Math.abs(target) > sum || (sum + target) % 2 == 1) return 0;
        
        int s = (sum + target) / 2;
        int[] dp = new int[s + 1];
        dp[0] = 1;
        
        for (int num : nums) {
            for (int i = s; i >= num; i--) {
                dp[i] += dp[i - num];
            }
        }
        return dp[s];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times S1)$
- Không gian: $\mathcal{O}(S1)$

---

## 3. Ones and Zeroes
### Đề bài chi tiết
Cho một mảng các chuỗi nhị phân `strs` và 2 số nguyên $m$ (số lượng số '0' tối đa) và $n$ (số lượng số '1' tối đa). Trả về số lượng chuỗi lớn nhất từ tập đã cho thỏa mãn cả 2 giới hạn này.
### Phân tích thuật toán
- Đây là bài toán 2D 0/1 Knapsack.
- Mỗi chuỗi nhị phân có `weight` là một cặp $(zeros, ones)$. Value đóng vai trò là $1$ (số lượng chuỗi).
- Hàm truy hồi `dp[i][j] = max(dp[i][j], dp[i - zeros][j - ones] + 1)`. Duyệt ngược hai chiều.
### Mã nguồn Java
```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];
        for (String str : strs) {
            int zeros = 0, ones = 0;
            for (char c : str.toCharArray()) {
                if (c == '0') zeros++;
                else ones++;
            }
            for (int i = m; i >= zeros; i--) {
                for (int j = n; j >= ones; j--) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - zeros][j - ones] + 1);
                }
            }
        }
        return dp[m][n];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(L \times M \times N)$ với $L$ là số chuỗi.
- Không gian: $\mathcal{O}(M \times N)$

---

## 4. Coin Change II
### Đề bài chi tiết
Cho mảng `coins` đại diện cho các mệnh giá đồng xu và số `amount` là tổng tiền. Trả về tổng số tổ hợp đồng xu để tạo thành `amount`. Mỗi đồng xu được dùng không giới hạn lần.
### Phân tích thuật toán
- Đây là bài toán Unbounded Knapsack tìm tổng số cách.
- Để đếm số Combinations thay vì Permutations, vòng lặp `coins` phải ở ngoài, vòng lặp `amount` ở trong và đi theo chiều thuận (từ `coin` đến `amount`).
### Mã nguồn Java
```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        return dp[amount];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times amount)$
- Không gian: $\mathcal{O}(amount)$

---

## 5. Combination Sum IV
### Đề bài chi tiết
Cho mảng `nums` gồm các số dương phân biệt và `target`. Trả về số lượng các hoán vị (combinations that care about sequence) cộng lại bằng `target`.
### Phân tích thuật toán
- Vì trật tự các phần tử khác nhau được coi là các cách khác nhau (Permutations), vòng lặp ngoài cùng phải là `target` và vòng lặp bên trong là `nums`.
- `dp[i] += dp[i - num]`.
### Mã nguồn Java
```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i >= num) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times target)$
- Không gian: $\mathcal{O}(target)$

---

## 6. Last Stone Weight II
### Đề bài chi tiết
Có một tập hợp đá `stones`. Mỗi lượt, chọn ra 2 viên đá đập vào nhau. Trọng lượng viên đá còn lại là trị tuyệt đối của hiệu hai viên đá đó. Tìm trọng lượng viên đá nhỏ nhất có thể còn lại cuối cùng.
### Phân tích thuật toán
- Bài toán tương đương với việc chia mảng thành hai tập con sao cho hiệu của hai tập con là nhỏ nhất.
- Tổng một tập con cần gần bằng `Sum / 2` nhất có thể. Giải bài toán 0/1 Knapsack với `Target = Sum / 2`. Trọng lượng viên đá còn lại chính là $Sum - 2 \times maxWeight$.
### Mã nguồn Java
```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = 0;
        for (int stone : stones) sum += stone;
        int target = sum / 2;
        
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        int maxWeight = 0;
        
        for (int stone : stones) {
            for (int i = target; i >= stone; i--) {
                dp[i] = dp[i] || dp[i - stone];
                if (dp[i]) {
                    maxWeight = Math.max(maxWeight, i);
                }
            }
        }
        return sum - 2 * maxWeight;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times \frac{Sum}{2})$
- Không gian: $\mathcal{O}(\frac{Sum}{2})$

---

## 7. Profitable Schemes
### Đề bài chi tiết
Có `n` thành viên băng đảng và một danh sách các công việc. Công việc `i` cần `group[i]` người và đem lại `profit[i]` lợi nhuận. Trả về số lượng các kế hoạch (tập con công việc) sao cho sử dụng không quá `n` người và đem lại lợi nhuận ít nhất là `minProfit`. Modulo kết quả cho $10^9+7$.
### Phân tích thuật toán
- Sử dụng DP 2D Knapsack: `dp[i][j]` là số cách chọn công việc với số người sử dụng là `i` và lợi nhuận đạt được là `j`.
- Vì yêu cầu ít nhất `minProfit`, ta chặn chỉ số lợi nhuận cao nhất lại: `Math.max(0, j - p)`. Điều này giúp tiết kiệm mảng và gộp mọi lợi nhuận vượt mức vào cột `minProfit`.
### Mã nguồn Java
```java
class Solution {
    public int profitableSchemes(int n, int minProfit, int[] group, int[] profit) {
        int MOD = 1000000007;
        int[][] dp = new int[n + 1][minProfit + 1];
        dp[0][0] = 1;
        
        for (int k = 0; k < group.length; k++) {
            int g = group[k], p = profit[k];
            for (int i = n; i >= g; i--) {
                for (int j = minProfit; j >= 0; j--) {
                    dp[i][j] = (dp[i][j] + dp[i - g][Math.max(0, j - p)]) % MOD;
                }
            }
        }
        
        int sum = 0;
        for (int i = 0; i <= n; i++) {
            sum = (sum + dp[i][minProfit]) % MOD;
        }
        return sum;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(\text{Tasks} \times N \times \text{minProfit})$
- Không gian: $\mathcal{O}(N \times \text{minProfit})$

---

## 8. Number of Dice Rolls With Target Sum
### Đề bài chi tiết
Bạn có `n` con xúc xắc, mỗi con có `k` mặt được đánh số từ `1` đến `k`. Trả về số lượng cách đổ xúc xắc sao cho tổng số điểm đạt được đúng bằng `target` (Modulo $10^9+7$).
### Phân tích thuật toán
- Bài toán Bounded Knapsack/DP đếm số cấu hình có thứ tự.
- Trạng thái `dp[target]` cập nhật liên tục qua $N$ lần đổ. Vì ở lần đổ sau phụ thuộc hoàn toàn vào lần đổ trước, ta sử dụng một mảng mới `nextDp` ở mỗi vòng lặp `n`.
### Mã nguồn Java
```java
class Solution {
    public int numRollsToTarget(int n, int k, int target) {
        int MOD = 1000000007;
        int[] dp = new int[target + 1];
        dp[0] = 1;
        
        for (int i = 1; i <= n; i++) {
            int[] nextDp = new int[target + 1];
            for (int j = 1; j <= k; j++) {
                for (int t = j; t <= target; t++) {
                    nextDp[t] = (nextDp[t] + dp[t - j]) % MOD;
                }
            }
            dp = nextDp;
        }
        return dp[target];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times K \times Target)$
- Không gian: $\mathcal{O}(Target)$

---

## 9. Minimum Cost For Tickets
### Đề bài chi tiết
Bạn có một mảng ngày đi du lịch `days` trong năm, và bạn mua vé du lịch ở mảng `costs` (1-day, 7-day, 30-day passes). Tính chi phí nhỏ nhất để có thể đi du lịch tất cả các ngày trong mảng `days`.
### Phân tích thuật toán
- Nếu ngày hiện tại không có trong `days`, chi phí không đổi (chi phí bằng với ngày hôm trước).
- Nếu có, lựa chọn 1 trong 3 loại vé: Giá tối thiểu của hôm qua + vé 1 ngày, hoặc từ 7 ngày trước + vé 7 ngày, hoặc 30 ngày trước + vé 30 ngày.
- Đây là Knapsack biến thể mảng 1D phụ thuộc vào bước giá cố định.
### Mã nguồn Java
```java
class Solution {
    public int mincostTickets(int[] days, int[] costs) {
        int lastDay = days[days.length - 1];
        int[] dp = new int[lastDay + 1];
        boolean[] isTravelDay = new boolean[lastDay + 1];
        for (int day : days) isTravelDay[day] = true;
        
        for (int i = 1; i <= lastDay; i++) {
            if (!isTravelDay[i]) {
                dp[i] = dp[i - 1];
                continue;
            }
            dp[i] = dp[i - 1] + costs[0];
            dp[i] = Math.min(dp[i], dp[Math.max(0, i - 7)] + costs[1]);
            dp[i] = Math.min(dp[i], dp[Math.max(0, i - 30)] + costs[2]);
        }
        return dp[lastDay];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(D)$, với $D$ là ngày lớn nhất.
- Không gian: $\mathcal{O}(D)$

---

## 10. Perfect Squares
### Đề bài chi tiết
Cho số nguyên `n`, tìm số lượng ít nhất các số chính phương cộng lại bằng `n`.
### Phân tích thuật toán
- Tương tự như bài toán tìm số lượng đồng xu ít nhất tạo thành `Target` (Coin Change), nhưng "các đồng xu" ở đây là các số chính phương $1, 4, 9, 16...$
- Giải quyết bằng Unbounded Knapsack, với mỗi giá trị có sức chứa tối đa lên tới $N$, cập nhật tìm giá trị Min.
### Mã nguồn Java
```java
import java.util.Arrays;

class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        
        for (int i = 1; i * i <= n; i++) {
            int sq = i * i;
            for (int j = sq; j <= n; j++) {
                dp[j] = Math.min(dp[j], dp[j - sq] + 1);
            }
        }
        return dp[n];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times \sqrt{N})$
- Không gian: $\mathcal{O}(N)$

---

## 11. Knapsack with Dependencies
### Đề bài chi tiết
Cho $N$ vật phẩm, mỗi vật phẩm có trọng lượng $W[i]$, giá trị $V[i]$ và một phụ thuộc $P[i]$ (nếu $P[i] = 0$, vật phẩm không phụ thuộc ai, nếu $P[i] > 0$, vật phẩm phụ thuộc vào vật phẩm $P[i]$). Bạn chỉ có thể chọn một vật phẩm nếu vật phẩm mà nó phụ thuộc cũng được chọn. Tìm tổng giá trị lớn nhất có thể đạt được với cái túi có sức chứa tối đa là $M$. (Giả sử các phụ thuộc tạo thành một cấu trúc rừng/cây).
### Phân tích thuật toán
- Đây là bài toán Tree DP (Quy hoạch động trên cây) kết hợp với Knapsack.
- Ta thêm một node giả $0$ làm gốc của tất cả các node không phụ thuộc ai, với trọng lượng và giá trị đều là $0$.
- Gọi `dp[u][j]` là tổng giá trị lớn nhất khi xét cây con gốc `u` với sức chứa $j$.
- Khi tính `dp[u][j]`, ta sẽ duyệt qua các con `v` của `u`. Với mỗi con `v`, ta thực hiện kết hợp kết quả của cây con `v` vào `u` tương tự như một bài toán chia nhóm (Group Knapsack / Bounded Knapsack).
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    List<List<Integer>> tree;
    int[] w, v;
    int[][] dp;

    public int maxValue(int M, int[] W, int[] V, int[] P) {
        int n = W.length;
        tree = new ArrayList<>();
        for (int i = 0; i <= n; i++) tree.add(new ArrayList<>());
        
        w = new int[n + 1];
        v = new int[n + 1];
        
        for (int i = 0; i < n; i++) {
            w[i + 1] = W[i];
            v[i + 1] = V[i];
            tree.get(P[i]).add(i + 1);
        }
        
        dp = new int[n + 1][M + 1];
        dfs(0, M);
        return dp[0][M];
    }
    
    private void dfs(int u, int M) {
        for (int i = w[u]; i <= M; i++) {
            dp[u][i] = v[u];
        }
        
        for (int child : tree.get(u)) {
            dfs(child, M);
            for (int j = M; j >= w[u]; j--) {
                for (int k = 0; k <= j - w[u]; k++) {
                    dp[u][j] = Math.max(dp[u][j], dp[u][j - k] + dp[child][k]);
                }
            }
        }
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times M^2)$
- Không gian: $\mathcal{O}(N \times M)$

---

## 12. Form Largest Integer With Digits That Add up to Target
### Đề bài chi tiết
Cho một mảng `cost` gồm 9 số nguyên (tương ứng với chi phí của các chữ số từ 1 đến 9) và một số nguyên `target`. Trả về một chuỗi đại diện cho số nguyên lớn nhất có thể tạo ra sao cho tổng chi phí các chữ số đúng bằng `target`. Nếu không thể, trả về `"0"`.
### Phân tích thuật toán
- Bài toán tương tự như Unbounded Knapsack, với trọng lượng là `cost[i]` và mục tiêu tối đa hóa độ dài của số.
- Ta dùng `dp[i]` để lưu độ dài lớn nhất của số có thể tạo ra với chi phí `i`. Khởi tạo `dp[0] = 0` và các phần tử khác là `-1`.
- Sau khi có mảng `dp`, ta truy vết ngược từ `target` về $0$ bằng cách ưu tiên chọn chữ số lớn nhất (từ 9 về 1) để xây dựng chuỗi kết quả.
### Mã nguồn Java
```java
class Solution {
    public String largestNumber(int[] cost, int target) {
        int[] dp = new int[target + 1];
        java.util.Arrays.fill(dp, Integer.MIN_VALUE);
        dp[0] = 0;
        
        for (int i = 1; i <= target; i++) {
            for (int d = 0; d < 9; d++) {
                if (i >= cost[d] && dp[i - cost[d]] != Integer.MIN_VALUE) {
                    dp[i] = Math.max(dp[i], dp[i - cost[d]] + 1);
                }
            }
        }
        
        if (dp[target] < 0) return "0";
        
        StringBuilder sb = new StringBuilder();
        int curr = target;
        while (curr > 0) {
            for (int d = 8; d >= 0; d--) {
                if (curr >= cost[d] && dp[curr] == dp[curr - cost[d]] + 1) {
                    sb.append(d + 1);
                    curr -= cost[d];
                    break;
                }
            }
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(\text{target} \times 9)$
- Không gian: $\mathcal{O}(\text{target})$

---

## 13. Combination Sum
### Đề bài chi tiết
Cho mảng `candidates` gồm các số nguyên dương phân biệt và số nguyên `target`. Trả về tất cả các tổ hợp duy nhất sao cho tổng các phần tử bằng `target`. Một số có thể được sử dụng nhiều lần.
### Phân tích thuật toán
- Bài toán yêu cầu liệt kê tất cả các cấu hình, thường được giải bằng DFS / Backtracking. Tuy nhiên, có thể sử dụng DP mảng để truy vết lại cấu hình (tương tự Unbounded Knapsack).
- `dp[i]` sẽ lưu danh sách các tổ hợp tạo ra tổng `i`.
- Để tránh trùng lặp cấu hình, vòng lặp ngoài cùng sẽ duyệt qua từng phần tử trong `candidates`, vòng lặp trong duyệt qua các tổng từ `candidate` đến `target`.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<List<Integer>>> dp = new ArrayList<>();
        for (int i = 0; i <= target; i++) {
            dp.add(new ArrayList<>());
        }
        dp.get(0).add(new ArrayList<>());
        
        for (int candidate : candidates) {
            for (int i = candidate; i <= target; i++) {
                for (List<Integer> list : dp.get(i - candidate)) {
                    List<Integer> newList = new ArrayList<>(list);
                    newList.add(candidate);
                    dp.get(i).add(newList);
                }
            }
        }
        
        return dp.get(target);
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times \text{target} \times C)$, với $C$ là số cấu hình trung bình.
- Không gian: $\mathcal{O}(\text{target} \times C)$

---

## 14. Number of Ways to Earn Points
### Đề bài chi tiết
Trong một bài kiểm tra có `N` loại câu hỏi. Bạn được cung cấp `target` là điểm mục tiêu và mảng 2D `types` với `types[i] = [count_i, marks_i]`, nghĩa là có `count_i` câu hỏi mang lại `marks_i` điểm. Trả về số cách để đạt được đúng `target` điểm. Modulo kết quả cho $10^9 + 7$.
### Phân tích thuật toán
- Đây là bài toán Bounded Knapsack. Mỗi loại câu hỏi ta có thể chọn từ $0$ đến `count_i` câu.
- Cập nhật mảng DP 1D từ phải sang trái để tránh việc sử dụng cùng một loại câu hỏi vượt quá giới hạn.
### Mã nguồn Java
```java
class Solution {
    public int waysToReachTarget(int target, int[][] types) {
        int MOD = 1000000007;
        int[] dp = new int[target + 1];
        dp[0] = 1;
        
        for (int[] type : types) {
            int count = type[0];
            int marks = type[1];
            
            for (int i = target; i >= 0; i--) {
                for (int k = 1; k <= count; k++) {
                    if (i >= k * marks) {
                        dp[i] = (dp[i] + dp[i - k * marks]) % MOD;
                    }
                }
            }
        }
        
        return dp[target];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times \text{target} \times \text{count})$
- Không gian: $\mathcal{O}(\text{target})$

---

## 15. Reach a Position After Exactly k Steps
### Đề bài chi tiết
Cho hai số nguyên `startPos` và `endPos`, cùng số nguyên `k`. Bạn có thể bước sang trái hoặc sang phải 1 bước. Trả về số cách để đi từ `startPos` đến `endPos` sau đúng `k` bước. Modulo $10^9 + 7$.
### Phân tích thuật toán
- Khoảng cách cần đi là $d = |startPos - endPos|$.
- Gọi $R$ là số bước sang phải, $L$ là số bước sang trái. Ta có: $R + L = k$ và $R - L = d$. Từ đó $R = (k + d) / 2$.
- Nếu $(k + d)$ lẻ hoặc $d > k$, số cách bằng $0$.
- Số cách là tổ hợp chập $R$ của $k$: $C(k, R)$. Tính $C(k, R)$ bằng mảng Pascal's Triangle (DP 1D).
### Mã nguồn Java
```java
class Solution {
    public int numberOfWays(int startPos, int endPos, int k) {
        int d = Math.abs(startPos - endPos);
        if (d > k || (k + d) % 2 != 0) return 0;
        
        int R = (k + d) / 2;
        int MOD = 1000000007;
        
        int[] dp = new int[R + 1];
        dp[0] = 1;
        
        for (int i = 1; i <= k; i++) {
            for (int j = Math.min(i, R); j > 0; j--) {
                dp[j] = (dp[j] + dp[j - 1]) % MOD;
            }
        }
        
        return dp[R];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(K \times R) \approx \mathcal{O}(K^2)$
- Không gian: $\mathcal{O}(R) \approx \mathcal{O}(K)$

---

## 16. Uncrossed Lines
### Đề bài chi tiết
Cho hai mảng số nguyên `nums1` và `nums2`. Ta vẽ các đoạn thẳng nối hai số giống nhau ở hai mảng. Trả về số lượng đường thẳng tối đa có thể vẽ sao cho không có hai đường thẳng nào cắt nhau.
### Phân tích thuật toán
- Bài toán tìm dãy con chung dài nhất (Longest Common Subsequence - LCS) vì thứ tự các phần tử không thể bị xáo trộn.
- Dùng DP 1D để tối ưu bộ nhớ.
### Mã nguồn Java
```java
class Solution {
    public int maxUncrossedLines(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int[] dp = new int[n + 1];
        
        for (int i = 1; i <= m; i++) {
            int prev = 0;
            for (int j = 1; j <= n; j++) {
                int temp = dp[j];
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[j] = prev + 1;
                } else {
                    dp[j] = Math.max(dp[j], dp[j - 1]);
                }
                prev = temp;
            }
        }
        
        return dp[n];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(M \times N)$
- Không gian: $\mathcal{O}(N)$

---

## 17. Number of Ways to Form a Target String Given a Dictionary
### Đề bài chi tiết
Cho danh sách các chuỗi `words` (cùng độ dài) và chuỗi `target`. Tại mỗi bước $j$, bạn có thể dùng ký tự thứ $j$ của bất kỳ chuỗi nào trong `words` để tạo thành ký tự tiếp theo của `target`. Mỗi index $j$ chỉ được dùng 1 lần và phải dùng theo thứ tự tăng dần. Trả về số cách tạo thành `target` modulo $10^9+7$.
### Phân tích thuật toán
- Đếm tần số các ký tự tại mỗi cột của `words`: `freq[j][char]`.
- Mảng `dp[i]` là số cách tạo thành tiền tố độ dài `i` của `target`.
- Với mỗi cột $j$, ta có thể dùng ký tự đó để cập nhật `dp[i]` từ `dp[i-1]`. Do tính chất tuần tự, ta duyệt `i` giảm dần giống với bài toán 0/1 Knapsack.
### Mã nguồn Java
```java
class Solution {
    public int numWays(String[] words, String target) {
        int MOD = 1000000007;
        int wordLen = words[0].length();
        int targetLen = target.length();
        
        if (wordLen < targetLen) return 0;
        
        int[][] freq = new int[wordLen][26];
        for (String w : words) {
            for (int j = 0; j < wordLen; j++) {
                freq[j][w.charAt(j) - 'a']++;
            }
        }
        
        long[] dp = new long[targetLen + 1];
        dp[0] = 1;
        
        for (int j = 0; j < wordLen; j++) {
            for (int i = targetLen; i >= 1; i--) {
                char c = target.charAt(i - 1);
                int count = freq[j][c - 'a'];
                if (count > 0) {
                    dp[i] = (dp[i] + dp[i - 1] * count) % MOD;
                }
            }
        }
        
        return (int) dp[targetLen];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(W \times L + T \times L)$
- Không gian: $\mathcal{O}(L \times 26 + T)$

---

## 18. Cherry Pickup II
### Đề bài chi tiết
Cho ma trận `grid`. Robot 1 xuất phát từ `(0, 0)` và Robot 2 từ `(0, cols - 1)`. Ở mỗi bước, cả hai robot đi xuống 1 hàng, có thể đi thẳng hoặc chéo 1 ô. Nếu hai robot vào cùng 1 ô, chỉ thu được lượng cherry ở ô đó 1 lần. Trả về số cherry tối đa cả hai có thể thu thập.
### Phân tích thuật toán
- Trạng thái DP sẽ gồm 3 chiều (hàng hiện tại, cột của robot 1, cột của robot 2).
- Ta có thể tối ưu không gian bằng cách dùng 2 mảng DP 2D cho hàng hiện tại và hàng tiếp theo.
- Tại mỗi hàng, xét $3 \times 3 = 9$ hướng di chuyển kết hợp cho 2 robot.
### Mã nguồn Java
```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) java.util.Arrays.fill(dp[i], -1);
        
        dp[0][n - 1] = grid[0][0] + grid[0][n - 1];
        
        for (int r = 1; r < m; r++) {
            int[][] nextDp = new int[n][n];
            for (int i = 0; i < n; i++) java.util.Arrays.fill(nextDp[i], -1);
            
            for (int c1 = 0; c1 < n; c1++) {
                for (int c2 = 0; c2 < n; c2++) {
                    if (dp[c1][c2] == -1) continue;
                    
                    for (int d1 = -1; d1 <= 1; d1++) {
                        for (int d2 = -1; d2 <= 1; d2++) {
                            int nc1 = c1 + d1, nc2 = c2 + d2;
                            if (nc1 >= 0 && nc1 < n && nc2 >= 0 && nc2 < n) {
                                int cherries = grid[r][nc1];
                                if (nc1 != nc2) cherries += grid[r][nc2];
                                nextDp[nc1][nc2] = Math.max(nextDp[nc1][nc2], dp[c1][c2] + cherries);
                            }
                        }
                    }
                }
            }
            dp = nextDp;
        }
        
        int max = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                max = Math.max(max, dp[i][j]);
            }
        }
        return max;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(M \times N^2 \times 9)$
- Không gian: $\mathcal{O}(N^2)$

---

## 19. Maximum Number of Events That Can Be Attended II
### Đề bài chi tiết
Cho danh sách `events` gồm các sự kiện với thời gian `[startDay, endDay, value]` và số nguyên `k`. Bạn có thể tham gia tối đa `k` sự kiện sao cho thời gian không chồng chéo nhau. Tìm tổng giá trị lớn nhất có thể nhận được.
### Phân tích thuật toán
- Sắp xếp các sự kiện theo ngày kết thúc tăng dần.
- `dp[i][j]` là giá trị lớn nhất đạt được khi xét tới sự kiện `i` và tham gia tối đa `j` sự kiện.
- Dùng Binary Search để tìm sự kiện `p` có `endDay` nhỏ hơn `startDay` của sự kiện `i`.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int maxValue(int[][] events, int k) {
        Arrays.sort(events, (a, b) -> a[1] - b[1]);
        int n = events.length;
        int[][] dp = new int[n + 1][k + 1];
        
        for (int i = 1; i <= n; i++) {
            int start = events[i - 1][0];
            int value = events[i - 1][2];
            
            int left = 0, right = i - 1, p = 0;
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (events[mid][1] < start) {
                    p = mid + 1;
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
            
            for (int j = 1; j <= k; j++) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[p][j - 1] + value);
            }
        }
        
        return dp[n][k];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \log N + N \times K)$
- Không gian: $\mathcal{O}(N \times K)$

---

## 20. Kth Smallest Instructions
### Đề bài chi tiết
Bob di chuyển từ `(0, 0)` tới `(row, col)`. Mỗi bước có thể đi ngang (H) hoặc đi dọc (V). Tìm chuỗi đường đi có thứ tự từ điển nhỏ thứ `k`.
### Phân tích thuật toán
- H đứng trước V trong bảng chữ cái, ưu tiên H ở đầu.
- Tổng số bước đi ngang là `col`, dọc là `row`.
- Tại mỗi vị trí, kiểm tra nếu chọn H, số cách hoàn thành chặng đường tiếp theo là bao nhiêu (sử dụng Pascal's Triangle). Nếu $\ge k$, ta chọn H. Ngược lại, chọn V và giảm $k$ một lượng bằng số cách đã bỏ qua.
### Mã nguồn Java
```java
class Solution {
    public String kthSmallestPath(int[] destination, int k) {
        int v = destination[0], h = destination[1];
        int n = v + h;
        
        int[][] comb = new int[n + 1][h + 1];
        comb[0][0] = 1;
        for (int i = 1; i <= n; i++) {
            comb[i][0] = 1;
            for (int j = 1; j <= Math.min(i, h); j++) {
                comb[i][j] = comb[i - 1][j - 1] + comb[i - 1][j];
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            if (h > 0) {
                int ways = comb[h + v - 1][h - 1];
                if (k <= ways) {
                    sb.append('H');
                    h--;
                } else {
                    sb.append('V');
                    v--;
                    k -= ways;
                }
            } else {
                sb.append('V');
                v--;
            }
        }
        
        return sb.toString();
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}((R+C) \times C + (R+C))$
- Không gian: $\mathcal{O}((R+C) \times C)$

---

## 21. Find the Winner of an Array Game
### Đề bài chi tiết
Cho mảng số nguyên `arr` gồm các phần tử phân biệt và số nguyên `k`. Trò chơi được chơi giữa 2 phần tử đầu tiên của mảng. Phần tử lớn hơn sẽ thắng và giữ nguyên ở vị trí đầu tiên (vị trí 0), phần tử thua sẽ bị chuyển xuống cuối mảng. Trò chơi kết thúc khi có một phần tử thắng đúng `k` trận liên tiếp. Trả về phần tử chiến thắng.
### Phân tích thuật toán
- Đây là bài toán có thể giải bằng vòng lặp mô phỏng đơn giản (Simulation). Nếu $k \ge arr.length$, phần tử lớn nhất trong mảng chắc chắn sẽ là người thắng cuối cùng vì nó sẽ đánh bại tất cả các phần tử khác.
- Duyệt qua mảng: Giữ một biến `current_winner` khởi tạo bằng `arr[0]` và `win_count = 0`. So sánh `current_winner` với `arr[i]`. Nếu `current_winner` lớn hơn, tăng `win_count`. Nếu nhỏ hơn, `current_winner` cập nhật thành `arr[i]` và `win_count = 1`. Khi `win_count == k`, trả về `current_winner`.
### Mã nguồn Java
```java
class Solution {
    public int getWinner(int[] arr, int k) {
        int currentWinner = arr[0];
        int winCount = 0;
        
        for (int i = 1; i < arr.length; i++) {
            if (currentWinner > arr[i]) {
                winCount++;
            } else {
                currentWinner = arr[i];
                winCount = 1;
            }
            
            if (winCount == k) {
                return currentWinner;
            }
        }
        
        return currentWinner;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N)$ với $N$ là số phần tử của mảng.
- Không gian: $\mathcal{O}(1)$

---

## 22. Maximize Greatness of an Array
### Đề bài chi tiết
Cho mảng số nguyên `nums`. Bạn được phép hoán vị các phần tử trong `nums`. Gọi độ vĩ đại (greatness) là số lượng chỉ số `i` sao cho `perm[i] > nums[i]`. Trả về độ vĩ đại lớn nhất có thể đạt được.
### Phân tích thuật toán
- Sử dụng thuật toán Tham lam (Greedy) kết hợp Sắp xếp (Sorting) hoặc Con trỏ (Two Pointers).
- Sắp xếp mảng `nums` tăng dần. Dùng một con trỏ `j` để duyệt qua mảng. Tại mỗi vị trí `i`, ta cố gắng tìm phần tử `nums[j]` nhỏ nhất nhưng vẫn lớn hơn `nums[i]`.
- Nếu `nums[j] > nums[i]`, có nghĩa là ta có thể dùng `nums[j]` để đánh bại `nums[i]`, tăng cả con trỏ `i` và `j`. Nếu không, chỉ tăng con trỏ `j` để tìm một số lớn hơn.
### Mã nguồn Java
```java
import java.util.Arrays;

class Solution {
    public int maximizeGreatness(int[] nums) {
        Arrays.sort(nums);
        int i = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[j] > nums[i]) {
                i++;
            }
        }
        return i;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \log N)$ do bước sắp xếp.
- Không gian: $\mathcal{O}(1)$ hoặc $\mathcal{O}(\log N)$ phụ thuộc vào thuật toán sắp xếp.

---

## 23. Minimum Increment Operations to Make Array Beautiful
### Đề bài chi tiết
Cho mảng số nguyên `nums` và số nguyên `k`. Bạn có thể thực hiện thao tác tăng một phần tử `nums[i]` lên $1$ đơn vị. Mảng được gọi là "đẹp" nếu mọi mảng con có độ dài lớn hơn hoặc bằng $3$ đều chứa ít nhất một phần tử lớn hơn hoặc bằng `k`. Trả về số thao tác tối thiểu cần thiết.
### Phân tích thuật toán
- Bài toán có thể giải bằng Quy hoạch động 1D. Điều kiện "mọi mảng con độ dài $\ge 3$ có phần tử $\ge k$" tương đương với việc "không có 3 phần tử liên tiếp nào đều nhỏ hơn $k$".
- Gọi `dp[i]` là chi phí tối thiểu để thỏa mãn điều kiện từ đầu đến chỉ số `i`, với điều kiện BẮT BUỘC phần tử thứ `i` được nâng lên tối thiểu thành $k$.
- Công thức: `dp[i] = max(0, k - nums[i]) + min(dp[i-1], dp[i-2], dp[i-3])`.
- Kết quả cuối cùng là `min(dp[n-1], dp[n-2], dp[n-3])`.
### Mã nguồn Java
```java
class Solution {
    public long minIncrementOperations(int[] nums, int k) {
        int n = nums.length;
        long[] dp = new long[n];
        
        for (int i = 0; i < n; i++) {
            long cost = Math.max(0, k - nums[i]);
            if (i < 3) {
                dp[i] = cost;
            } else {
                dp[i] = cost + Math.min(dp[i - 1], Math.min(dp[i - 2], dp[i - 3]));
            }
        }
        
        return Math.min(dp[n - 1], Math.min(dp[n - 2], dp[n - 3]));
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N)$
- Không gian: $\mathcal{O}(N)$, có thể tối ưu xuống $\mathcal{O}(1)$ bằng cách lưu 3 biến trạng thái.

---

## 24. Count Valid Paths in a Tree
### Đề bài chi tiết
Cho một cây vô hướng gồm `n` đỉnh được đánh số từ $1$ đến $n$. Một đường đi `(u, v)` được gọi là hợp lệ nếu trên đường đi từ `u` đến `v` có ĐÚNG một đỉnh có nhãn là số nguyên tố. Trả về tổng số lượng các đường đi hợp lệ.
### Phân tích thuật toán
- Đây là bài toán Tree DP. Tại mỗi node $u$, gọi `dp[u][0]` là số đường đi đi xuống từ $u$ không chứa số nguyên tố nào, và `dp[u][1]` là số đường đi đi xuống từ $u$ chứa đúng $1$ số nguyên tố.
- Với mỗi nhánh con $v$ của $u$, nếu kết nối $u$ với $v$, ta có thể ghép các nhánh lại với nhau để tạo thành đường đi hợp lệ.
- Tổng số đường đi đi qua $u$ và có đúng $1$ số nguyên tố sẽ là: $\sum (dp[u][0] \times dp[v][1] + dp[u][1] \times dp[v][0])$.
- Cập nhật `dp[u]` phụ thuộc vào bản thân đỉnh $u$ có phải số nguyên tố hay không. Ta dùng sàng nguyên tố (Sieve of Eratosthenes) để kiểm tra $\mathcal{O}(1)$.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    boolean[] isPrime;
    long ans = 0;
    
    public long countPaths(int n, int[][] edges) {
        isPrime = new boolean[n + 1];
        Arrays.fill(isPrime, true);
        isPrime[0] = isPrime[1] = false;
        for (int p = 2; p * p <= n; p++) {
            if (isPrime[p]) {
                for (int i = p * p; i <= n; i += p) isPrime[i] = false;
            }
        }
        
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i <= n; i++) graph.add(new ArrayList<>());
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }
        
        dfs(1, 0, graph);
        return ans;
    }
    
    private long[] dfs(int u, int p, List<List<Integer>> graph) {
        long[] dp = new long[2];
        if (isPrime[u]) dp[1] = 1;
        else dp[0] = 1;
        
        for (int v : graph.get(u)) {
            if (v == p) continue;
            long[] childDp = dfs(v, u, graph);
            
            ans += dp[0] * childDp[1] + dp[1] * childDp[0];
            
            if (isPrime[u]) {
                dp[1] += childDp[0];
            } else {
                dp[0] += childDp[0];
                dp[1] += childDp[1];
            }
        }
        return dp;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \log \log N)$ cho việc sàng nguyên tố, cộng với $\mathcal{O}(N)$ duyệt cây. Tổng: $\mathcal{O}(N)$.
- Không gian: $\mathcal{O}(N)$

---

## 25. Find the K-th Lucky Number
### Đề bài chi tiết
Biết rằng các số may mắn (lucky numbers) là các số chỉ chứa các chữ số '4' và '7'. Các số may mắn theo thứ tự tăng dần là: 4, 7, 44, 47, 74, 77, 444, 447, ... Trả về số may mắn thứ `k` dưới dạng chuỗi.
### Phân tích thuật toán
- Ta có 2 số có 1 chữ số, 4 số có 2 chữ số, 8 số có 3 chữ số...
- Thực chất nếu coi '4' là '0' và '7' là '1', cây sinh số này tương đương với biểu diễn nhị phân.
- Nếu lấy số $k + 1$ và chuyển sang nhị phân, sau đó bỏ đi bit `1` cao nhất ở đầu tiên, phần còn lại sẽ khớp chính xác với chuỗi cần tìm nếu ta đổi `0` thành `4` và `1` thành `7`.
- Ví dụ: k = 3 (tìm "44"). $k + 1 = 4$, nhị phân là `100`. Bỏ bit `1` đầu tiên còn `00`, thay thế thành `44`.
### Mã nguồn Java
```java
class Solution {
    public String kthLuckyNumber(int k) {
        String bin = Integer.toBinaryString(k + 1);
        StringBuilder sb = new StringBuilder();
        for (int i = 1; i < bin.length(); i++) {
            if (bin.charAt(i) == '0') {
                sb.append('4');
            } else {
                sb.append('7');
            }
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(\log K)$ tương đương số bit.
- Không gian: $\mathcal{O}(\log K)$

---

## 26. Count Paths That Can Form a Palindrome in a Tree
### Đề bài chi tiết
Cho một cây gồm `n` đỉnh. Mỗi cạnh của cây được gán một ký tự viết thường. Một đường đi giữa hai đỉnh `(u, v)` được gọi là có thể tạo thành chuỗi Palindrome nếu có thể sắp xếp lại các ký tự trên đường đi đó để tạo thành Palindrome. Trả về số lượng đường đi hợp lệ.
### Phân tích thuật toán
- Một tập hợp ký tự có thể xếp thành Palindrome nếu có tối đa $1$ ký tự có số lần xuất hiện lẻ.
- Sử dụng Bitmask đại diện cho số lần chẵn/lẻ của 26 ký tự (chuỗi dài 26 bit). Phép toán XOR rất hữu ích vì `A XOR A = 0` (chẵn) và `A XOR 0 = A` (lẻ).
- Gọi `mask[u]` là bitmask chẵn lẻ từ `root` đến đỉnh `u`. Đường đi từ `u` đến `v` có bitmask là `mask[u] ^ mask[v]`.
- Bài toán trở thành tìm số cặp `(u, v)` sao cho `mask[u] ^ mask[v]` có tối đa 1 bit `1`. Sử dụng HashMap để lưu tần suất của các `mask`.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    long ans = 0;
    
    public long countPalindromePaths(List<Integer> parent, String s) {
        int n = parent.size();
        List<List<int[]>> tree = new ArrayList<>();
        for (int i = 0; i < n; i++) tree.add(new ArrayList<>());
        
        for (int i = 1; i < n; i++) {
            tree.get(parent.get(i)).add(new int[]{i, 1 << (s.charAt(i) - 'a')});
        }
        
        Map<Integer, Integer> freq = new HashMap<>();
        freq.put(0, 1);
        dfs(0, 0, tree, freq);
        
        return ans;
    }
    
    private void dfs(int u, int mask, List<List<int[]>> tree, Map<Integer, Integer> freq) {
        for (int[] edge : tree.get(u)) {
            int v = edge[0];
            int nMask = mask ^ edge[1];
            
            // mask[u] ^ mask[v] == 0
            ans += freq.getOrDefault(nMask, 0);
            
            // mask[u] ^ mask[v] có đúng 1 bit 1
            for (int i = 0; i < 26; i++) {
                ans += freq.getOrDefault(nMask ^ (1 << i), 0);
            }
            
            freq.put(nMask, freq.getOrDefault(nMask, 0) + 1);
            dfs(v, nMask, tree, freq);
        }
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times 26)$
- Không gian: $\mathcal{O}(N)$

---

## 27. Maximum Elegance of a K-Length Subsequence
### Đề bài chi tiết
Cho mảng `items` gồm lợi nhuận và danh mục. Bằng cách chọn một dãy con độ dài `k`, ta tính độ thanh lịch (elegance) bằng tổng lợi nhuận cộng với bình phương số lượng danh mục phân biệt. Trả về độ thanh lịch tối đa.
### Phân tích thuật toán
- Sắp xếp các vật phẩm theo lợi nhuận giảm dần. Lấy `k` phần tử đầu tiên làm cấu hình cơ sở (tham lam).
- Ta lưu trữ các phần tử thuộc danh mục đã xuất hiện nhiều hơn 1 lần trong `k` phần tử đầu (lưu trong một Stack).
- Từ phần tử $k$ trở đi, nếu gặp phần tử có danh mục mới chưa xuất hiện, ta lấy nó thay thế cho phần tử có lợi nhuận nhỏ nhất trong Stack.
- Tính toán Elegance tại mỗi bước và cập nhật giá trị Max.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public long findMaximumElegance(int[][] items, int k) {
        Arrays.sort(items, (a, b) -> b[0] - a[0]);
        long totalProfit = 0;
        Set<Integer> seen = new HashSet<>();
        Stack<Integer> duplicates = new Stack<>();
        
        for (int i = 0; i < k; i++) {
            totalProfit += items[i][0];
            if (seen.contains(items[i][1])) {
                duplicates.push(items[i][0]);
            } else {
                seen.add(items[i][1]);
            }
        }
        
        long ans = totalProfit + (long) seen.size() * seen.size();
        
        for (int i = k; i < items.length; i++) {
            if (!seen.contains(items[i][1]) && !duplicates.isEmpty()) {
                seen.add(items[i][1]);
                totalProfit -= duplicates.pop();
                totalProfit += items[i][0];
                long currentElegance = totalProfit + (long) seen.size() * seen.size();
                ans = Math.max(ans, currentElegance);
            }
        }
        
        return ans;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \log N)$
- Không gian: $\mathcal{O}(N)$

---

## 28. Length of the Longest Valid Substring
### Đề bài chi tiết
Cho chuỗi `word` và danh sách các chuỗi bị cấm `forbidden`. Một chuỗi được gọi là hợp lệ nếu không chứa bất kỳ chuỗi con nào nằm trong `forbidden`. Trả về độ dài chuỗi con hợp lệ lớn nhất.
### Phân tích thuật toán
- Bỏ tất cả các chuỗi `forbidden` vào một HashSet để truy xuất $\mathcal{O}(1)$. Điểm đặc biệt: độ dài mỗi chuỗi bị cấm không quá 10.
- Sử dụng kỹ thuật Two Pointers / Cửa sổ trượt (Sliding Window). Với con trỏ phải `right` chạy từ trái sang phải, con trỏ `left` chỉ ranh giới an toàn.
- Khi con trỏ `right` mở rộng, ta kiểm tra các chuỗi con kết thúc tại `right` và có độ dài từ 1 đến 10. Nếu chuỗi con này nằm trong `forbidden`, ta đẩy `left` lên ngay sau vị trí bắt đầu của chuỗi cấm.
### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int longestValidSubstring(String word, List<String> forbidden) {
        Set<String> set = new HashSet<>(forbidden);
        int ans = 0;
        int left = 0;
        
        for (int right = 0; right < word.length(); right++) {
            for (int i = Math.max(left, right - 9); i <= right; i++) {
                if (set.contains(word.substring(i, right + 1))) {
                    left = i + 1;
                }
            }
            ans = Math.max(ans, right - left + 1);
        }
        
        return ans;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times 10 \times 10) = \mathcal{O}(N)$ do Substring tốn thời gian.
- Không gian: $\mathcal{O}(F \times 10) = \mathcal{O}(F)$ với $F$ là tổng độ dài danh sách `forbidden`.

---

## 29. Maximum Number of Jumps to Reach the Last Index
### Đề bài chi tiết
Cho mảng `nums` và số nguyên `target`. Bạn có thể nhảy từ chỉ số $i$ đến $j$ ($i < j$) nếu $-target \le nums[j] - nums[i] \le target$. Trả về số bước nhảy tối đa để đến được chỉ số cuối cùng $n - 1$.
### Phân tích thuật toán
- Đây là bài toán Quy hoạch động cơ bản (Knapsack pattern).
- `dp[i]` lưu trữ số bước nhảy tối đa để đến được vị trí $i$. Khởi tạo `dp[0] = 0` và các vị trí khác là $-1$ (chưa thể đến được).
- Duyệt qua $i$ từ 0 đến $N-1$, với mỗi $i$, duyệt ngược lại các $j$ phía trước để tìm xem có thỏa điều kiện khoảng cách không, nếu có thì `dp[i] = max(dp[i], dp[j] + 1)`.
### Mã nguồn Java
```java
import java.util.Arrays;

class Solution {
    public int maximumJumps(int[] nums, int target) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, -1);
        dp[0] = 0;
        
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] != -1 && Math.abs(nums[i] - nums[j]) <= target) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }
        
        return dp[n - 1];
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N^2)$
- Không gian: $\mathcal{O}(N)$

---

## 30. Number of Subarrays That Match a Pattern I
### Đề bài chi tiết
Cho mảng `nums` và một mảng `pattern` có độ dài $m$ gồm các số `1`, `0`, `-1`. Mảng con của `nums` khớp với `pattern` nếu sự biến thiên giữa các phần tử liên tiếp (lớn hơn, bằng, nhỏ hơn) tương ứng hoàn toàn với các phần tử trong `pattern`. Trả về số lượng mảng con như vậy.
### Phân tích thuật toán
- Đầu tiên, ta có thể tạo một mảng `diff` để biểu diễn sự biến thiên của dãy `nums`. `diff[i] = 1` nếu $nums[i+1] > nums[i]$, `0` nếu bằng và `-1` nếu nhỏ hơn.
- Bài toán lúc này được chuyển thành tìm số lần xuất hiện của chuỗi con `pattern` bên trong chuỗi lớn `diff`.
- Vì giới hạn của bài toán I nhỏ, ta có thể duyệt Brute Force $\mathcal{O}(N \times M)$.
### Mã nguồn Java
```java
class Solution {
    public int countMatchingSubarrays(int[] nums, int[] pattern) {
        int n = nums.length;
        int m = pattern.length;
        int[] diff = new int[n - 1];
        
        for (int i = 0; i < n - 1; i++) {
            if (nums[i + 1] > nums[i]) diff[i] = 1;
            else if (nums[i + 1] == nums[i]) diff[i] = 0;
            else diff[i] = -1;
        }
        
        int count = 0;
        for (int i = 0; i <= n - 1 - m; i++) {
            boolean match = true;
            for (int j = 0; j < m; j++) {
                if (diff[i + j] != pattern[j]) {
                    match = false;
                    break;
                }
            }
            if (match) count++;
        }
        
        return count;
    }
}
```
### Độ phức tạp
- Thời gian: $\mathcal{O}(N \times M)$ trong trường hợp xấu nhất. (Có thể dùng KMP để rút gọn thành $\mathcal{O}(N + M)$).
- Không gian: $\mathcal{O}(N)$ để lưu trữ mảng trung gian `diff`.
