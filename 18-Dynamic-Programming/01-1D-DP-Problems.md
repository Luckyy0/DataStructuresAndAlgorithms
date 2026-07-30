# Bài tập 1D Dynamic Programming

## 1. Climbing Stairs
**Đề bài chi tiết:** 
Bạn đang leo một cầu thang. Phải mất `n` bước để lên đến đỉnh. Mỗi lần bạn có thể leo 1 hoặc 2 bậc. Có bao nhiêu cách khác nhau để lên đến đỉnh?

**Phân tích thuật toán:**
Đây là bài toán tương tự dãy Fibonacci. Số cách để tới bậc `i` bằng tổng số cách tới bậc `i-1` (và bước 1 bậc) và số cách tới bậc `i-2` (và bước 2 bậc).
Gọi `dp[i]` là số cách tới bậc `i`. `dp[i] = dp[i-1] + dp[i-2]`. Ta có thể tối ưu không gian xuống $O(1)$.

**Mã nguồn Java:**
```java
class Solution {
    public int climbStairs(int n) {
        if (n <= 2) return n;
        int prev1 = 2; // Số cách tới bậc i-1
        int prev2 = 1; // Số cách tới bậc i-2
        
        for (int i = 3; i <= n; i++) {
            int current = prev1 + prev2;
            prev2 = prev1;
            prev1 = current;
        }
        return prev1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 2. Min Cost Climbing Stairs
**Đề bài chi tiết:** 
Bạn được cung cấp một mảng số nguyên `cost` trong đó `cost[i]` là chi phí của bậc thứ `i` trên cầu thang. Sau khi trả chi phí, bạn có thể leo một hoặc hai bậc. Bạn có thể bắt đầu từ bậc ở chỉ số 0 hoặc chỉ số 1. Hãy tìm chi phí tối thiểu để đạt tới đỉnh của cầu thang.

**Phân tích thuật toán:**
Tại bậc `i`, chi phí tối thiểu để đạt tới bậc `i` là chi phí tại `i` cộng với giá trị nhỏ nhất của chi phí để đạt tới bậc `i-1` và bậc `i-2`.
`dp[i] = cost[i] + min(dp[i-1], dp[i-2])`.
Đỉnh cầu thang thực chất là bậc `n` (bên ngoài mảng `cost`), nên ta lấy `min(dp[n-1], dp[n-2])`.

**Mã nguồn Java:**
```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        if (n == 2) return Math.min(cost[0], cost[1]);
        
        int prev2 = cost[0];
        int prev1 = cost[1];
        
        for (int i = 2; i < n; i++) {
            int current = cost[i] + Math.min(prev1, prev2);
            prev2 = prev1;
            prev1 = current;
        }
        
        return Math.min(prev1, prev2);
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 3. House Robber
**Đề bài chi tiết:** 
Bạn là một tên trộm chuyên nghiệp lên kế hoạch ăn trộm tiền ở các ngôi nhà dọc theo một con phố. Mỗi ngôi nhà có một số tiền nhất định. Các ngôi nhà liền kề có hệ thống an ninh kết nối, nếu bạn cướp hai nhà liền kề trong cùng một đêm, cảnh sát sẽ được báo động. Cho mảng `nums` đại diện cho số tiền của mỗi nhà, trả về số tiền tối đa bạn có thể cướp mà không báo động cảnh sát.

**Phân tích thuật toán:**
Tại mỗi ngôi nhà `i`, tên trộm có 2 lựa chọn:
1. Cướp nhà `i`: Tiền thu được là `nums[i] + dp[i-2]` (không được cướp nhà `i-1`).
2. Không cướp nhà `i`: Tiền thu được là `dp[i-1]`.
Do đó, `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`. Tối ưu không gian bằng 2 biến.

**Mã nguồn Java:**
```java
class Solution {
    public int rob(int[] nums) {
        if (nums.length == 0) return 0;
        if (nums.length == 1) return nums[0];
        
        int prev2 = nums[0];
        int prev1 = Math.max(nums[0], nums[1]);
        
        for (int i = 2; i < nums.length; i++) {
            int current = Math.max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 4. House Robber II
**Đề bài chi tiết:** 
Giống với House Robber, nhưng các ngôi nhà hiện được sắp xếp thành một vòng tròn. Tức là ngôi nhà đầu tiên và ngôi nhà cuối cùng kề nhau. Tính số tiền tối đa có thể cướp.

**Phân tích thuật toán:**
Vì nhà đầu và cuối nối nhau, ta không thể cướp cả hai. Vậy bài toán chia làm 2 bài House Robber tuyến tính:
1. Xét các nhà từ `0` đến `n-2` (bỏ nhà cuối).
2. Xét các nhà từ `1` đến `n-1` (bỏ nhà đầu).
Kết quả là Max của hai trường hợp này.

**Mã nguồn Java:**
```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0];
        if (n == 2) return Math.max(nums[0], nums[1]);
        
        return Math.max(robHelper(nums, 0, n - 2), robHelper(nums, 1, n - 1));
    }
    
    private int robHelper(int[] nums, int start, int end) {
        int prev2 = nums[start];
        int prev1 = Math.max(nums[start], nums[start + 1]);
        
        for (int i = start + 2; i <= end; i++) {
            int current = Math.max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = current;
        }
        return prev1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 5. Decode Ways
**Đề bài chi tiết:** 
Một tin nhắn chứa các chữ cái từ A-Z được mã hóa thành các con số bằng cách sử dụng ánh xạ: 'A' -> "1", 'B' -> "2", ..., 'Z' -> "26". Cho một chuỗi `s` chứa các chữ số, trả về số cách giải mã nó.

**Phân tích thuật toán:**
Gọi `dp[i]` là số cách giải mã chuỗi con `s[0...i-1]`.
Ta có thể tách 1 chữ số (`s[i-1]`) nếu nó khác '0' -> `dp[i] += dp[i-1]`.
Ta có thể tách 2 chữ số (`s[i-2...i-1]`) nếu nó thuộc "10" đến "26" -> `dp[i] += dp[i-2]`.

**Mã nguồn Java:**
```java
class Solution {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0 || s.charAt(0) == '0') return 0;
        int n = s.length();
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        
        for (int i = 2; i <= n; i++) {
            int oneDigit = Integer.parseInt(s.substring(i - 1, i));
            int twoDigits = Integer.parseInt(s.substring(i - 2, i));
            
            if (oneDigit >= 1 && oneDigit <= 9) {
                dp[i] += dp[i - 1];
            }
            if (twoDigits >= 10 && twoDigits <= 26) {
                dp[i] += dp[i - 2];
            }
        }
        return dp[n];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$ (Có thể tối ưu xuống $O(1)$)

---

## 6. Word Break
**Đề bài chi tiết:** 
Cho chuỗi `s` và một từ điển `wordDict`. Trả về `true` nếu `s` có thể được phân đoạn thành một chuỗi cách nhau bởi dấu cách gồm một hoặc nhiều từ trong từ điển.

**Phân tích thuật toán:**
Gọi `dp[i]` là trạng thái boolean: chuỗi con của `s` từ 0 đến `i-1` có thể phân đoạn được hay không.
`dp[i] = true` nếu tồn tại `j < i` sao cho `dp[j] == true` và phần chuỗi từ `j` đến `i-1` nằm trong `wordDict`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> dict = new HashSet<>(wordDict);
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && dict.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^3)$ (Cắt chuỗi bằng `substring` mất thêm thời gian $O(N)$)
- Space Complexity: $O(N)$ cộng với không gian từ điển.

---

## 7. Coin Change (Fewest coins)
**Đề bài chi tiết:** 
Bạn được cho mảng `coins` biểu thị các mệnh giá đồng xu khác nhau và số nguyên `amount` biểu thị tổng số tiền. Trả về số đồng xu tối thiểu cần thiết để tạo thành `amount`. Nếu không thể, trả về `-1`.

**Phân tích thuật toán:**
Gọi `dp[i]` là số xu ít nhất để tạo thành tổng `i`.
Với mỗi đồng xu `c` trong `coins`, nếu `i >= c`, ta có thể cập nhật `dp[i] = min(dp[i], dp[i-c] + 1)`.
Khởi tạo mảng `dp` bằng một giá trị lớn hơn giới hạn (ví dụ `amount + 1`).

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (i - coin >= 0) {
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1);
                }
            }
        }
        
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(S \times C)$ với $S$ là `amount`, $C$ là số loại xu.
- Space Complexity: $O(S)$

---

## 8. Maximum Subarray (DP view)
**Đề bài chi tiết:** 
Cho mảng số nguyên `nums`, hãy tìm một mảng con liên tiếp có tổng lớn nhất và trả về tổng đó. (Kadane's Algorithm)

**Phân tích thuật toán:**
Dưới góc nhìn quy hoạch động, gọi `dp[i]` là tổng mảng con lớn nhất kết thúc tại vị trí `i`.
`dp[i] = max(nums[i], dp[i-1] + nums[i])`.
Nghĩa là ta bắt đầu mảng con mới tại `i` hoặc nối tiếp vào mảng con kết thúc tại `i-1`.
Giá trị lớn nhất có thể nằm ở bất kỳ đâu trong `dp`, ta lưu max toàn cục.

**Mã nguồn Java:**
```java
class Solution {
    public int maxSubArray(int[] nums) {
        int currentMax = nums[0];
        int globalMax = nums[0];
        
        for (int i = 1; i < nums.length; i++) {
            currentMax = Math.max(nums[i], currentMax + nums[i]);
            globalMax = Math.max(globalMax, currentMax);
        }
        
        return globalMax;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 9. Maximum Product Subarray
**Đề bài chi tiết:** 
Cho mảng số nguyên `nums`, hãy tìm mảng con liên tiếp (chứa ít nhất 1 số) có tích lớn nhất và trả về tích đó.

**Phân tích thuật toán:**
Vì có số âm, tích của hai số âm sẽ thành dương lớn. Ta phải duy trì cả tích lớn nhất và tích nhỏ nhất kết thúc tại `i`.
Khi gặp số âm, max hiện tại và min hiện tại sẽ đổi chỗ cho nhau.
`currMax = max(nums[i], max(prevMax * nums[i], prevMin * nums[i]))`
`currMin = min(nums[i], min(prevMax * nums[i], prevMin * nums[i]))`

**Mã nguồn Java:**
```java
class Solution {
    public int maxProduct(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int maxSoFar = nums[0];
        int minSoFar = nums[0];
        int result = maxSoFar;
        
        for (int i = 1; i < nums.length; i++) {
            int current = nums[i];
            int tempMax = Math.max(current, Math.max(maxSoFar * current, minSoFar * current));
            minSoFar = Math.min(current, Math.min(maxSoFar * current, minSoFar * current));
            
            maxSoFar = tempMax;
            result = Math.max(result, maxSoFar);
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 10. Longest Increasing Subsequence
**Đề bài chi tiết:** 
Cho mảng `nums`, tìm độ dài của chuỗi con tăng dần dài nhất (Subsequence không cần liên tiếp).

**Phân tích thuật toán:**
Gọi `dp[i]` là độ dài chuỗi tăng dần dài nhất kết thúc tại phần tử `i`.
Khởi tạo mảng `dp` với các giá trị 1.
Với mỗi `i`, xét tất cả các phần tử `j` đứng trước `i` (từ `0` đến `i-1`).
Nếu `nums[i] > nums[j]`, thì `dp[i] = max(dp[i], dp[j] + 1)`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        int maxLength = 1;
        
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            maxLength = Math.max(maxLength, dp[i]);
        }
        
        return maxLength;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$ (Có thể tối ưu xuống $O(N \log N)$ bằng Binary Search)
- Space Complexity: $O(N)$

---

## 11. Partition Equal Subset Sum
**Đề bài chi tiết:** 
Cho một mảng chỉ chứa các số nguyên dương `nums`, hãy xác định xem có thể chia mảng này thành hai tập con sao cho tổng các phần tử trong cả hai tập con là bằng nhau.

**Phân tích thuật toán:**
Bài toán này có thể được chuyển thành bài toán 0/1 Knapsack. Tổng của tất cả các phần tử trong mảng phải là một số chẵn. Nếu tổng là lẻ, ta không thể chia thành hai tập bằng nhau. Nếu tổng là chẵn, mục tiêu của ta là tìm một tập con có tổng chính xác bằng `sum / 2`.
Gọi `dp[i]` là trạng thái boolean: liệu có thể tạo ra tổng `i` từ các phần tử đã xét hay không.
Khởi tạo `dp[0] = true`. Với mỗi phần tử `num` trong mảng, ta cập nhật mảng `dp` từ phải sang trái để tránh sử dụng một phần tử nhiều lần:
`dp[i] = dp[i] || dp[i - num]` với `i` chạy từ `sum / 2` về `num`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        
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

**Độ phức tạp:**
- Time Complexity: $O(N \times \text{sum})$
- Space Complexity: $O(\text{sum})$

---

## 12. Target Sum
**Đề bài chi tiết:** 
Bạn được cung cấp một mảng số nguyên `nums` và một số nguyên `target`. Bạn muốn xây dựng một biểu thức bằng cách thêm một ký hiệu `+` hoặc `-` trước mỗi số nguyên trong `nums` và sau đó nối tất cả các số nguyên. Trả về số lượng các biểu thức khác nhau mà bạn có thể xây dựng có giá trị bằng `target`.

**Phân tích thuật toán:**
Gọi tập các số có dấu `+` là $P$ và tập các số có dấu `-` là $N$.
Ta có:
$\sum P - \sum N = target$
$\sum P + \sum N + \sum P - \sum N = target + \sum P + \sum N$
$2 \sum P = target + sum(nums)$
$\sum P = (target + sum(nums)) / 2$
Bài toán trở thành: Tìm số cách chọn một tập con từ `nums` sao cho tổng của chúng bằng $S = (target + sum) / 2$.
Đây là bài toán tương tự như 0/1 Knapsack. Gọi `dp[i]` là số cách để tạo thành tổng `i`.
Với mỗi số `num`, ta duyệt từ $S$ về `num`: `dp[i] += dp[i - num]`.

**Mã nguồn Java:**
```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for (int num : nums) sum += num;
        
        if (Math.abs(target) > sum || (sum + target) % 2 != 0) return 0;
        
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

**Độ phức tạp:**
- Time Complexity: $O(N \times S)$
- Space Complexity: $O(S)$

---

## 13. Palindromic Substrings
**Đề bài chi tiết:** 
Cho một chuỗi `s`, hãy đếm xem có bao nhiêu chuỗi con là chuỗi đối xứng (palindromic substrings) trong nó. Các chuỗi con có nội dung giống nhau nhưng ở vị trí khác nhau được tính là các chuỗi con khác nhau.

**Phân tích thuật toán:**
Sử dụng quy hoạch động. Gọi `dp[i][j]` là `true` nếu chuỗi con `s[i...j]` là chuỗi đối xứng.
Điều kiện:
- Nếu độ dài là 1 (`i == j`): `dp[i][i] = true`.
- Nếu độ dài là 2 (`j = i + 1`): `dp[i][i+1] = (s[i] == s[i+1])`.
- Nếu độ dài > 2: `dp[i][j] = (s[i] == s[j]) && dp[i+1][j-1]`.
Ta có thể duyệt độ dài từ 1 đến `n` hoặc sử dụng phương pháp mở rộng từ tâm (Expand Around Center) để tối ưu không gian xuống $O(1)$. Ở đây ta sử dụng Expand Around Center.

**Mã nguồn Java:**
```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        int count = 0;
        
        for (int i = 0; i < n; i++) {
            // Lẻ: Tâm là một ký tự
            count += expandAroundCenter(s, i, i);
            // Chẵn: Tâm là khoảng giữa hai ký tự
            count += expandAroundCenter(s, i, i + 1);
        }
        
        return count;
    }
    
    private int expandAroundCenter(String s, int left, int right) {
        int count = 0;
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            count++;
            left--;
            right++;
        }
        return count;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(1)$

---

## 14. Longest Palindromic Substring
**Đề bài chi tiết:** 
Cho một chuỗi `s`, trả về chuỗi con đối xứng dài nhất trong `s`.

**Phân tích thuật toán:**
Tương tự như Palindromic Substrings, ta có thể dùng phương pháp Expand Around Center. Thay vì đếm số lượng, ta theo dõi độ dài lớn nhất và vị trí bắt đầu của chuỗi đó.
Mỗi lần mở rộng từ tâm, nếu độ dài tìm được lớn hơn độ dài lớn nhất hiện tại, ta cập nhật vị trí bắt đầu và độ dài mới.

**Mã nguồn Java:**
```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) return "";
        int start = 0, end = 0;
        
        for (int i = 0; i < s.length(); i++) {
            int len1 = expandAroundCenter(s, i, i);
            int len2 = expandAroundCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        
        return s.substring(start, end + 1);
    }
    
    private int expandAroundCenter(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        return right - left - 1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(1)$

---

## 15. Perfect Squares
**Đề bài chi tiết:** 
Cho số nguyên dương `n`, hãy tìm số lượng ít nhất các số chính phương (perfect squares: 1, 4, 9, 16,...) có tổng bằng `n`.

**Phân tích thuật toán:**
Đây là bài toán biến thể của Coin Change. Các "đồng xu" ở đây là các số chính phương $1^2, 2^2, 3^2, ...$ sao cho $j^2 \le i$.
Gọi `dp[i]` là số lượng số chính phương ít nhất để tạo thành tổng `i`.
`dp[i] = min(dp[i], dp[i - j*j] + 1)` với mọi $j$ sao cho $j^2 \le i$.
Khởi tạo mảng `dp` với giá trị lớn (ví dụ `n+1`), `dp[0] = 0`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, n + 1);
        dp[0] = 0;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j * j <= i; j++) {
                dp[i] = Math.min(dp[i], dp[i - j * j] + 1);
            }
        }
        
        return dp[n];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \sqrt{N})$
- Space Complexity: $O(N)$

---

## 16. Ugly Number II
**Đề bài chi tiết:** 
Viết một chương trình để tìm số xấu (ugly number) thứ `n`.
Số xấu là các số nguyên dương mà các thừa số nguyên tố của nó chỉ bao gồm `2, 3, 5`.

**Phân tích thuật toán:**
Mỗi số xấu mới phải được tạo ra bằng cách nhân một số xấu cũ với 2, 3 hoặc 5.
Ta duy trì 3 con trỏ `p2`, `p3`, `p5` trỏ tới các số xấu trong mảng đã được tính toán. Tại mỗi bước, số xấu tiếp theo sẽ là giá trị nhỏ nhất trong số `dp[p2] * 2`, `dp[p3] * 3` và `dp[p5] * 5`.
Sau khi tìm được số nhỏ nhất, ta tăng con trỏ tương ứng lên 1. Chú ý có thể có các giá trị trùng nhau nên cần sử dụng `if` riêng biệt cho mỗi điều kiện.

**Mã nguồn Java:**
```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] dp = new int[n];
        dp[0] = 1;
        int p2 = 0, p3 = 0, p5 = 0;
        
        for (int i = 1; i < n; i++) {
            int next2 = dp[p2] * 2;
            int next3 = dp[p3] * 3;
            int next5 = dp[p5] * 5;
            
            dp[i] = Math.min(next2, Math.min(next3, next5));
            
            if (dp[i] == next2) p2++;
            if (dp[i] == next3) p3++;
            if (dp[i] == next5) p5++;
        }
        
        return dp[n - 1];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$

---

## 17. Counting Bits
**Đề bài chi tiết:** 
Cho số nguyên `n`, hãy trả về một mảng `ans` độ dài `n + 1` sao cho với mỗi `i` (`0 <= i <= n`), `ans[i]` là số lượng bit `1` trong biểu diễn nhị phân của `i`.

**Phân tích thuật toán:**
Ta có thể tận dụng quy hoạch động. Nhận thấy rằng:
- Nếu `i` chẵn: số bit 1 của `i` bằng số bit 1 của `i / 2` (vì dịch phải 1 bit không làm mất bit 1 nào, bit cuối là 0).
- Nếu `i` lẻ: số bit 1 của `i` bằng số bit 1 của `i / 2` cộng thêm 1 (bit cuối là 1).
Công thức tổng quát: `dp[i] = dp[i >> 1] + (i & 1)`.

**Mã nguồn Java:**
```java
class Solution {
    public int[] countBits(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 0;
        
        for (int i = 1; i <= n; i++) {
            dp[i] = dp[i >> 1] + (i & 1);
        }
        
        return dp;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$

---

## 18. Wiggle Subsequence
**Đề bài chi tiết:** 
Một chuỗi đan xen (wiggle sequence) là một chuỗi mà sự chênh lệch giữa các phần tử liên tiếp luôn luân phiên giữa dương và âm.
Cho một mảng các số nguyên `nums`, hãy trả về độ dài của chuỗi con đan xen dài nhất.

**Phân tích thuật toán:**
Ta duy trì 2 mảng DP (hoặc 2 biến):
- `up`: độ dài chuỗi đan xen dài nhất kết thúc tại `i` với sự chênh lệch cuối cùng là dương.
- `down`: độ dài chuỗi đan xen dài nhất kết thúc tại `i` với sự chênh lệch cuối cùng là âm.
Khi duyệt mảng:
- Nếu `nums[i] > nums[i-1]`: `up = down + 1`, `down` giữ nguyên.
- Nếu `nums[i] < nums[i-1]`: `down = up + 1`, `up` giữ nguyên.
- Nếu `nums[i] == nums[i-1]`: Cả hai giữ nguyên.

**Mã nguồn Java:**
```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length < 2) return nums.length;
        
        int up = 1;
        int down = 1;
        
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                up = down + 1;
            } else if (nums[i] < nums[i - 1]) {
                down = up + 1;
            }
        }
        
        return Math.max(up, down);
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 19. Best Time to Buy and Sell Stock with Cooldown
**Đề bài chi tiết:** 
Bạn được cung cấp mảng `prices` trong đó `prices[i]` là giá cổ phiếu vào ngày thứ `i`.
Hãy tìm lợi nhuận tối đa bạn có thể đạt được. Bạn có thể thực hiện nhiều giao dịch, với điều kiện:
Sau khi bạn bán cổ phiếu, bạn không thể mua cổ phiếu vào ngày hôm sau (tức là có khoảng thời gian nghỉ 1 ngày - cooldown). Bạn phải bán trước khi mua lại.

**Phân tích thuật toán:**
Dùng State Machine DP. Trong bất kỳ ngày nào, ta có thể ở 1 trong 3 trạng thái:
1. `hold`: Đang giữ cổ phiếu (đã mua, chưa bán).
2. `sold`: Vừa bán cổ phiếu hôm nay (bắt buộc ngày mai vào `rest`).
3. `rest`: Không có cổ phiếu và cũng không bán vào ngày hôm qua (có thể mua vào hôm nay).
Công thức chuyển đổi:
- `hold[i] = max(hold[i-1], rest[i-1] - prices[i])`
- `sold[i] = hold[i-1] + prices[i]`
- `rest[i] = max(rest[i-1], sold[i-1])`
Lợi nhuận tối đa cuối cùng là `max(sold[n-1], rest[n-1])`.

**Mã nguồn Java:**
```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;
        
        int hold = -prices[0];
        int sold = 0;
        int rest = 0;
        
        for (int i = 1; i < prices.length; i++) {
            int prevHold = hold;
            int prevSold = sold;
            int prevRest = rest;
            
            hold = Math.max(prevHold, prevRest - prices[i]);
            sold = prevHold + prices[i];
            rest = Math.max(prevRest, prevSold);
        }
        
        return Math.max(sold, rest);
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 20. Integer Break
**Đề bài chi tiết:** 
Cho số nguyên dương `n`, hãy chia nó thành tổng của ít nhất 2 số nguyên dương sao cho tích của các số nguyên này là lớn nhất. Trả về tích lớn nhất đó.

**Phân tích thuật toán:**
Gọi `dp[i]` là tích lớn nhất có thể chia từ số `i`.
Với mỗi số `i` từ 2 đến `n`, ta thử chia `i` thành hai phần: `j` và `i - j`.
Phần `i - j` có thể không cần chia thêm (giữ nguyên là `i - j`) hoặc tiếp tục chia (giá trị lớn nhất là `dp[i - j]`).
Vậy: `dp[i] = max(dp[i], j * max(i - j, dp[i - j]))` với `1 <= j < i`.

**Mã nguồn Java:**
```java
class Solution {
    public int integerBreak(int n) {
        int[] dp = new int[n + 1];
        dp[1] = 1;
        
        for (int i = 2; i <= n; i++) {
            for (int j = 1; j < i; j++) {
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));
            }
        }
        
        return dp[n];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(N)$

---

## 21. Number of Longest Increasing Subsequence
**Đề bài chi tiết:** 
Cho một mảng chưa sắp xếp các số nguyên `nums`, hãy trả về số lượng các chuỗi con tăng dần dài nhất (longest increasing subsequences).
Chuỗi con là chuỗi được dẫn xuất từ mảng bằng cách xóa một số hoặc không xóa phần tử nào mà không làm thay đổi thứ tự của các phần tử còn lại.

**Phân tích thuật toán:**
Sử dụng quy hoạch động. Thay vì chỉ duy trì mảng `lengths` (lưu chiều dài LIS kết thúc tại `i`), ta dùng thêm mảng `counts` (lưu số lượng LIS kết thúc tại `i`).
Với mỗi `i` và `j < i`:
- Nếu `nums[i] > nums[j]`:
  - Nếu `lengths[j] + 1 > lengths[i]`: Nghĩa là ta tìm thấy một LIS dài hơn. Cập nhật `lengths[i] = lengths[j] + 1` và reset `counts[i] = counts[j]`.
  - Nếu `lengths[j] + 1 == lengths[i]`: Nghĩa là ta tìm thêm được các cách khác để tạo ra LIS có cùng chiều dài. Cập nhật `counts[i] += counts[j]`.
Cuối cùng, tìm chiều dài max trong `lengths`, tổng các `counts[i]` có `lengths[i] == max_length` chính là kết quả.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int findNumberOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        int[] lengths = new int[n];
        int[] counts = new int[n];
        Arrays.fill(lengths, 1);
        Arrays.fill(counts, 1);
        
        int maxLength = 1;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    if (lengths[j] + 1 > lengths[i]) {
                        lengths[i] = lengths[j] + 1;
                        counts[i] = counts[j];
                    } else if (lengths[j] + 1 == lengths[i]) {
                        counts[i] += counts[j];
                    }
                }
            }
            maxLength = Math.max(maxLength, lengths[i]);
        }
        
        int result = 0;
        for (int i = 0; i < n; i++) {
            if (lengths[i] == maxLength) {
                result += counts[i];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^2)$
- Space Complexity: $O(N)$

---

## 22. Combination Sum IV
**Đề bài chi tiết:** 
Cho một mảng gồm các số nguyên phân biệt `nums` và một số nguyên mục tiêu `target`, trả về số lượng các hoán vị kết hợp có tổng bằng `target`.
(Thứ tự các phần tử khác nhau được tính là các tổ hợp khác nhau).

**Phân tích thuật toán:**
Đây là bài toán tương tự Coin Change nhưng thứ tự đóng vai trò quan trọng (hoán vị).
Gọi `dp[i]` là số lượng cách để tạo thành tổng `i`.
Để tính `dp[i]`, ta có thể cộng thêm một phần tử `num` bất kỳ từ `nums`. Nếu `i >= num`, thì mọi cách tạo ra tổng `i - num` đều có thể tạo ra tổng `i` bằng cách nối thêm `num` vào cuối.
Công thức truy hồi: `dp[i] = sum(dp[i - num])` với mọi `num` trong `nums`.
Khởi tạo `dp[0] = 1` (có 1 cách để tạo tổng 0 là không chọn gì cả).

**Mã nguồn Java:**
```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i - num >= 0) {
                    dp[i] += dp[i - num];
                }
            }
        }
        
        return dp[target];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \times \text{target})$
- Space Complexity: $O(\text{target})$

---

## 23. Arithmetic Slices
**Đề bài chi tiết:** 
Một chuỗi số nguyên được gọi là cấp số cộng nếu nó bao gồm ít nhất ba phần tử và sự chênh lệch giữa hai phần tử liên tiếp bất kỳ là giống nhau.
Cho một mảng số nguyên `nums`, hãy trả về số lượng các chuỗi con cấp số cộng liền kề của `nums`.

**Phân tích thuật toán:**
Gọi `dp[i]` là số lượng chuỗi con cấp số cộng kết thúc tại chỉ số `i`.
Để một chuỗi con kết thúc tại `i` tạo thành cấp số cộng, phần tử `nums[i]`, `nums[i-1]`, `nums[i-2]` phải có chênh lệch bằng nhau (`nums[i] - nums[i-1] == nums[i-1] - nums[i-2]`).
Nếu điều kiện này thoả mãn, chuỗi con đó không chỉ là một cấp số cộng mới độ dài 3, mà nó còn có thể kéo dài tất cả các cấp số cộng kết thúc tại `i-1`. Do đó:
`dp[i] = dp[i-1] + 1`.
Tổng tất cả các `dp[i]` sẽ là kết quả của bài toán. Ta có thể tối ưu không gian bằng 1 biến `current`.

**Mã nguồn Java:**
```java
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        if (nums.length < 3) return 0;
        
        int current = 0;
        int total = 0;
        
        for (int i = 2; i < nums.length; i++) {
            if (nums[i] - nums[i - 1] == nums[i - 1] - nums[i - 2]) {
                current += 1;
                total += current;
            } else {
                current = 0;
            }
        }
        
        return total;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 24. Delete and Earn
**Đề bài chi tiết:** 
Cho một mảng số nguyên `nums`. Bạn muốn kiếm được nhiều điểm nhất có thể.
Mỗi thao tác, bạn chọn bất kỳ `nums[i]` nào và xóa nó để kiếm `nums[i]` điểm. Sau đó, bạn cũng phải xóa mọi phần tử bằng `nums[i] - 1` hoặc `nums[i] + 1`.
Trả về số điểm tối đa bạn có thể kiếm được.

**Phân tích thuật toán:**
Bài toán này có thể đưa về bài toán House Robber.
Đầu tiên, ta gom nhóm các số có cùng giá trị lại và tính tổng số điểm có được nếu xóa toàn bộ chúng. Ta tạo một mảng `points` với `points[x]` là tổng số điểm thu được từ tất cả các phần tử có giá trị `x`.
Sau khi đưa về mảng `points`, bài toán trở thành: không được chọn 2 phần tử kề nhau trong mảng `points`. (Giống hệt House Robber).
`dp[i] = max(dp[i-1], dp[i-2] + points[i])`.

**Mã nguồn Java:**
```java
class Solution {
    public int deleteAndEarn(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        
        int maxVal = 0;
        for (int num : nums) {
            maxVal = Math.max(maxVal, num);
        }
        
        int[] points = new int[maxVal + 1];
        for (int num : nums) {
            points[num] += num;
        }
        
        int prev2 = 0;
        int prev1 = points[0]; 
        
        for (int i = 1; i <= maxVal; i++) {
            int current = Math.max(prev1, prev2 + points[i]);
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N + M)$ với $M$ là giá trị lớn nhất trong mảng `nums`.
- Space Complexity: $O(M)$

---

## 25. Maximum Length of Pair Chain
**Đề bài chi tiết:** 
Cho `n` cặp số `pairs` trong đó `pairs[i] = [left_i, right_i]` và `left_i < right_i`.
Một cặp `(c, d)` có thể nối sau cặp `(a, b)` nếu `b < c`. 
Hãy tìm độ dài của chuỗi cặp dài nhất có thể tạo ra. (Các cặp có thể được chọn theo bất kỳ thứ tự nào).

**Phân tích thuật toán:**
Bài toán này có thể giải bằng Quy hoạch động (giống Longest Increasing Subsequence) nhưng giải thuật Tham lam (Greedy) tối ưu hơn nhiều.
Sắp xếp mảng `pairs` theo phần tử thứ hai của mỗi cặp (theo `right`).
Duyệt qua danh sách đã sắp xếp, nếu phần tử `left` của cặp hiện tại lớn hơn phần tử `right` của cặp đã chọn trước đó, ta nối thêm cặp này vào chuỗi.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int findLongestChain(int[][] pairs) {
        Arrays.sort(pairs, (a, b) -> Integer.compare(a[1], b[1]));
        int currentEnd = Integer.MIN_VALUE;
        int maxChain = 0;
        
        for (int[] pair : pairs) {
            if (pair[0] > currentEnd) {
                currentEnd = pair[1];
                maxChain++;
            }
        }
        return maxChain;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N \log N)$
- Space Complexity: $O(1)$

---

## 26. Minimum Score Triangulation of Polygon
**Đề bài chi tiết:** 
Bạn được cung cấp một mảng số nguyên `values` trong đó `values[i]` là giá trị của đỉnh thứ `i` của một đa giác lồi `N` đỉnh.
Triangulation là việc chia đa giác thành `N-2` tam giác không chồng chéo lên nhau. Điểm của một tam giác là tích giá trị 3 đỉnh của nó. Tổng điểm của phép chia là tổng điểm của các tam giác cấu thành.
Trả về tổng điểm nhỏ nhất có thể của một phép chia đa giác thành tam giác.

**Phân tích thuật toán:**
Đây là dạng DP trên khoảng (Interval DP).
Gọi `dp[i][j]` là tổng điểm nhỏ nhất để chia đa giác tạo bởi các đỉnh từ `i` đến `j` thành các tam giác.
Khoảng cách ít nhất để tạo 1 tam giác là 3 đỉnh, tức `j - i >= 2`.
Ta xét đỉnh `k` nằm giữa `i` và `j` (`i < k < j`). Đỉnh `i`, `j`, `k` tạo thành một tam giác chia phần còn lại thành 2 đa giác nhỏ hơn: từ `i` đến `k` và từ `k` đến `j`.
Công thức: `dp[i][j] = min(dp[i][k] + dp[k][j] + values[i]*values[j]*values[k])` với mọi `k`.

**Mã nguồn Java:**
```java
class Solution {
    public int minScoreTriangulation(int[] values) {
        int n = values.length;
        int[][] dp = new int[n][n];
        
        // Duyệt độ dài của khoảng cách d từ 2 đến n-1
        for (int d = 2; d < n; d++) {
            for (int i = 0; i < n - d; i++) {
                int j = i + d;
                dp[i][j] = Integer.MAX_VALUE;
                
                for (int k = i + 1; k < j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j] + values[i] * values[j] * values[k]);
                }
            }
        }
        
        return dp[0][n - 1];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N^3)$
- Space Complexity: $O(N^2)$

---

## 27. Maximum Alternating Subsequence Sum
**Đề bài chi tiết:** 
Tổng xen kẽ của một mảng `[a0, a1, a2, a3, ...]` được định nghĩa là `a0 - a1 + a2 - a3 + ...`.
Cho mảng số nguyên `nums`, hãy trả về tổng xen kẽ lớn nhất của một chuỗi con (subsequence) bất kỳ của `nums` (sau khi lập lại chỉ số chuỗi con từ 0).

**Phân tích thuật toán:**
Sử dụng State Machine DP (tương tự Best Time to Buy and Sell Stock).
Tại mỗi phần tử `nums[i]`, một chuỗi con xen kẽ kết thúc tại phần tử này có thể mang dấu cộng (dương) hoặc dấu trừ (âm).
Gọi:
- `evenSum`: tổng xen kẽ lớn nhất của một chuỗi con có độ dài chẵn (tức là phần tử cuối mang dấu `-`).
- `oddSum`: tổng xen kẽ lớn nhất của một chuỗi con có độ dài lẻ (tức là phần tử cuối mang dấu `+`).
Với mỗi số `num` trong mảng:
- Cập nhật `oddSum = max(oddSum, evenSum + num)`
- Cập nhật `evenSum = max(evenSum, oddSum - num)`
Tuy nhiên phải dùng giá trị cũ của `oddSum` khi cập nhật `evenSum`. Trả về `oddSum` ở cuối (vì tổng độ dài lẻ luôn lớn hơn độ dài chẵn kết thúc bằng phép trừ số dương).

**Mã nguồn Java:**
```java
class Solution {
    public long maxAlternatingSum(int[] nums) {
        long evenSum = 0; // độ dài chẵn
        long oddSum = 0;  // độ dài lẻ
        
        for (int num : nums) {
            long prevEven = evenSum;
            long prevOdd = oddSum;
            
            oddSum = Math.max(prevOdd, prevEven + num);
            evenSum = Math.max(prevEven, prevOdd - num);
        }
        
        return oddSum;
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$

---

## 28. Minimum Cost For Tickets
**Đề bài chi tiết:** 
Bạn có dự định đi du lịch bằng tàu hỏa vào một số ngày trong năm, được cho dưới dạng mảng `days`. Mỗi vé bán ra với 3 loại: vé 1 ngày, vé 7 ngày, vé 30 ngày. Chi phí tương ứng nằm trong mảng `costs`.
Trả về chi phí tối thiểu bạn cần để đi du lịch mọi ngày trong danh sách `days`.

**Phân tích thuật toán:**
Dùng mảng `dp[i]` để lưu chi phí nhỏ nhất để đi du lịch đến ngày thứ `i` của năm (từ 1 đến 365).
Nếu ngày `i` không nằm trong danh sách cần đi du lịch (`days`), thì chi phí không thay đổi: `dp[i] = dp[i-1]`.
Nếu ngày `i` nằm trong `days`, ta cần mua một trong 3 loại vé:
- Vé 1 ngày: chi phí là `dp[i-1] + costs[0]`
- Vé 7 ngày: chi phí là `dp[max(0, i-7)] + costs[1]`
- Vé 30 ngày: chi phí là `dp[max(0, i-30)] + costs[2]`
`dp[i]` sẽ là giá trị nhỏ nhất trong 3 lựa chọn trên.

**Mã nguồn Java:**
```java
class Solution {
    public int mincostTickets(int[] days, int[] costs) {
        int lastDay = days[days.length - 1];
        boolean[] isTravelDay = new boolean[lastDay + 1];
        for (int day : days) {
            isTravelDay[day] = true;
        }
        
        int[] dp = new int[lastDay + 1];
        
        for (int i = 1; i <= lastDay; i++) {
            if (!isTravelDay[i]) {
                dp[i] = dp[i - 1];
            } else {
                int cost1 = dp[i - 1] + costs[0];
                int cost7 = dp[Math.max(0, i - 7)] + costs[1];
                int cost30 = dp[Math.max(0, i - 30)] + costs[2];
                dp[i] = Math.min(cost1, Math.min(cost7, cost30));
            }
        }
        
        return dp[lastDay];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(D)$ với $D$ là ngày du lịch lớn nhất (tối đa 365).
- Space Complexity: $O(D)$

---

## 29. Domino and Tromino Tiling
**Đề bài chi tiết:** 
Bạn có hai loại hình gạch: hình chữ nhật $2 \times 1$ (Domino) và hình chữ L (Tromino). Cả hai loại đều có thể được xoay.
Cho một số nguyên `n`, hãy đếm số lượng cách lát kín một bảng kích thước $2 \times n$. Kết quả có thể rất lớn, trả về phần dư cho $10^9 + 7$.

**Phân tích thuật toán:**
Dùng Quy hoạch động. Gọi `dp[i]` là số cách lát kín bảng $2 \times i$.
Để xây dựng bảng $2 \times i$:
- Cách cơ bản bằng Domino: thêm 1 Domino đứng (`dp[i-1]`) hoặc 2 Domino ngang (`dp[i-2]`).
- Bằng Tromino và các hình kết hợp: Các hình khối ghép phức tạp hơn (bị khuyết 1 ô vuông) sẽ đóng góp `2 * dp[i-3] + 2 * dp[i-4] + ... + 2 * dp[0]`.
Rút gọn phương trình toán học từ quan hệ đệ quy, ta có công thức tuyến tính ngắn gọn:
`dp[i] = 2 * dp[i-1] + dp[i-3]` (Với $i \ge 4$).

**Mã nguồn Java:**
```java
class Solution {
    public int numTilings(int n) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        if (n == 3) return 5;
        
        int MOD = 1000000007;
        long[] dp = new long[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 5;
        
        for (int i = 4; i <= n; i++) {
            dp[i] = (2 * dp[i - 1] + dp[i - 3]) % MOD;
        }
        
        return (int) dp[n];
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(N)$

---

## 30. Paint House
**Đề bài chi tiết:** 
Có một dãy `n` ngôi nhà. Mỗi ngôi nhà có thể được sơn bằng một trong ba màu: đỏ, xanh lam hoặc xanh lục. Tuy nhiên, không có hai ngôi nhà liền kề nào được sơn cùng màu.
Bạn được cung cấp một ma trận `costs` kích thước $n \times 3$, trong đó `costs[i][0]` là chi phí sơn nhà `i` màu đỏ, `costs[i][1]` màu xanh lam, `costs[i][2]` màu xanh lục.
Hãy tìm chi phí tối thiểu để sơn tất cả các ngôi nhà.

**Phân tích thuật toán:**
Thay vì dùng mảng 2D cho bài toán này, ta có thể dùng 3 biến state lưu chi phí sơn đến ngôi nhà hiện tại cho từng màu.
Tại nhà `i`, nếu ta sơn màu đỏ (0), thì nhà `i-1` phải là màu xanh lam (1) hoặc xanh lục (2).
Chi phí để sơn nhà `i` bằng màu `c` sẽ bằng `costs[i][c]` cộng với giá trị nhỏ nhất của hai màu khác của nhà `i-1`.
Cuối cùng, trả về giá trị nhỏ nhất trong 3 biến state tại nhà cuối cùng.

**Mã nguồn Java:**
```java
class Solution {
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) return 0;
        
        int costR = costs[0][0];
        int costB = costs[0][1];
        int costG = costs[0][2];
        
        for (int i = 1; i < costs.length; i++) {
            int prevR = costR;
            int prevB = costB;
            int prevG = costG;
            
            costR = costs[i][0] + Math.min(prevB, prevG);
            costB = costs[i][1] + Math.min(prevR, prevG);
            costG = costs[i][2] + Math.min(prevR, prevB);
        }
        
        return Math.min(costR, Math.min(costB, costG));
    }
}
```

**Độ phức tạp:**
- Time Complexity: $O(N)$
- Space Complexity: $O(1)$
