# 30 Bài tập Math và Bit Manipulation

## 1. Sum of Two Integers
**Đề bài chi tiết:** Tính tổng của hai số nguyên `a` và `b` mà không sử dụng các toán tử `+` và `-`. (LeetCode 371)
**Phân tích thuật toán:**
Dùng phép `XOR` (`^`) để cộng các bit không có phần nhớ (carry). Dùng phép `AND` (`&`) kết hợp dịch trái 1 bit (`<< 1`) để tính phần nhớ. Lặp lại cho đến khi phần nhớ bằng 0.
**Độ phức tạp:** O(1) Thời gian (tối đa 32 bước cho số nguyên 32 bit), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int getSum(int a, int b) {
        while (b != 0) {
            int carry = (a & b) << 1;
            a = a ^ b;
            b = carry;
        }
        return a;
    }
}
```

## 2. Divide Two Integers
**Đề bài chi tiết:** Chia hai số nguyên (dividend và divisor) mà không sử dụng toán tử nhân, chia và mod. Trả về phần nguyên của thương. Nếu kết quả tràn số thì trả về `2^31 - 1`. (LeetCode 29)
**Phân tích thuật toán:**
Dùng phép dịch trái (`<<`) để tìm lũy thừa lớn nhất của 2 sao cho `divisor * 2^i <= dividend`. Trừ số đó đi và cộng `2^i` vào thương. Phải dùng `long` để tránh tràn số trong quá trình trung gian.
**Độ phức tạp:** O(log N) Thời gian (N là giá trị của dividend), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int divide(int dividend, int divisor) {
        if (dividend == Integer.MIN_VALUE && divisor == -1) return Integer.MAX_VALUE;
        long dvd = Math.abs((long) dividend);
        long dvs = Math.abs((long) divisor);
        int ans = 0;
        int sign = dividend > 0 == divisor > 0 ? 1 : -1;
        while (dvd >= dvs) {
            long temp = dvs, m = 1;
            while (temp << 1 <= dvd) {
                temp <<= 1;
                m <<= 1;
            }
            dvd -= temp;
            ans += m;
        }
        return sign * ans;
    }
}
```

## 3. Gray Code
**Đề bài chi tiết:** Cho số bit `n`, trả về danh sách các chuỗi Gray Code (2 giá trị liên tiếp khác nhau 1 bit). (LeetCode 89)
**Phân tích thuật toán:**
Có thể sử dụng công thức toán học: số ở vị trí thứ `i` (0-indexed) có giá trị Gray code là `i ^ (i >> 1)`. Duyệt từ `0` đến `2^n - 1`.
**Độ phức tạp:** O(2^n) Thời gian, O(2^n) Không gian.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> grayCode(int n) {
        List<Integer> res = new ArrayList<>();
        int size = 1 << n;
        for (int i = 0; i < size; i++) {
            res.add(i ^ (i >> 1));
        }
        return res;
    }
}
```

## 4. Circular Permutation in Binary Representation
**Đề bài chi tiết:** Cho `n` và chuỗi bắt đầu `start`. Trả về Gray code nhưng phải bắt đầu từ `start` và chuỗi tạo vòng lặp hợp lệ. (LeetCode 1238)
**Phân tích thuật toán:**
Sinh mảng Gray Code thông thường. Tìm vị trí của số `start`, sau đó nối chuỗi đằng sau lên trước chuỗi đằng trước (quay mảng / rotate array). Hoặc ta có thể dùng tính chất bù XOR: Số thứ `i` có thể bắt đầu bằng `start ^ (i ^ (i >> 1))`.
**Độ phức tạp:** O(2^n) Thời gian, O(2^n) Không gian.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> circularPermutation(int n, int start) {
        List<Integer> res = new ArrayList<>();
        int size = 1 << n;
        for (int i = 0; i < size; i++) {
            res.add(start ^ (i ^ (i >> 1)));
        }
        return res;
    }
}
```

## 5. Integer Replacement
**Đề bài chi tiết:** Cho số nguyên n. Nếu chẵn, chia 2. Nếu lẻ, bạn có thể thay thành n+1 hoặc n-1. Trả về số bước tối thiểu để biến n thành 1. (LeetCode 397)
**Phân tích thuật toán:**
Dùng bit: Nếu lẻ, nhìn vào 2 bit cuối. Nếu kết thúc bằng `11` (và n != 3), thì n+1 sẽ đưa về nhiều số 0 ở cuối hơn. Nếu kết thúc bằng `01` hoặc n == 3, thì n-1 tối ưu hơn. Chú ý số nguyên lớn cần dùng long hoặc unsigned.
**Độ phức tạp:** O(log N) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int integerReplacement(int n) {
        long N = n;
        int count = 0;
        while (N != 1) {
            if ((N & 1) == 0) {
                N >>>= 1;
            } else if (N == 3 || ((N >>> 1) & 1) == 0) {
                N--;
            } else {
                N++;
            }
            count++;
        }
        return count;
    }
}
```

## 6. Bitwise ORs of Subarrays
**Đề bài chi tiết:** Cho một mảng `arr`. Trả về số lượng kết quả duy nhất có thể được tạo ra bằng phép toán Bitwise OR của các mảng con liên tiếp. (LeetCode 898)
**Phân tích thuật toán:**
Duyệt qua mảng. Tại mỗi bước, giữ một tập Set chứa các kết quả OR liên tiếp kết thúc tại vị trí hiện tại. Số lượng giá trị OR trong set tại mỗi vị trí không quá 32 vì mỗi lần OR giá trị chỉ tăng lên (thêm bit 1).
**Độ phức tạp:** O(32 * N) Thời gian, O(32 * N) Không gian (cho Set).
**Mã nguồn Java:**
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int subarrayBitwiseORs(int[] arr) {
        Set<Integer> res = new HashSet<>();
        Set<Integer> curr = new HashSet<>();
        
        for (int x : arr) {
            Set<Integer> next = new HashSet<>();
            next.add(x);
            for (int y : curr) {
                next.add(x | y);
            }
            res.addAll(next);
            curr = next;
        }
        return res.size();
    }
}
```

## 7. Find Missing and Repeated Values
**Đề bài chi tiết:** Một mảng 2D kích thước `n x n` chứa các giá trị từ `1` đến `n^2`. Một số bị lặp lại, một số bị thiếu. Tìm hai số đó. (LeetCode 2965 / Logic giống Find Missing and Repeating bằng toán học/bit)
**Phân tích thuật toán:**
Toán học: Lấy tổng kỳ vọng trừ tổng mảng, và bình phương kỳ vọng trừ bình phương mảng. Dùng bitwise: XOR tất cả từ `1` đến `n^2` và mảng. Ta được `X ^ Y`. Lấy bit khác biệt đầu tiên (`rsb = x & -x`) để chia tập ra làm hai nhóm và XOR tìm ra từng giá trị.
**Độ phức tạp:** O(N^2) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int[] findMissingAndRepeatedValues(int[][] grid) {
        int n = grid.length;
        int n2 = n * n;
        long sum = 0, sumSq = 0;
        long expectedSum = (long)n2 * (n2 + 1) / 2;
        long expectedSumSq = (long)n2 * (n2 + 1) * (2 * n2 + 1) / 6;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                long val = grid[i][j];
                sum += val;
                sumSq += val * val;
            }
        }
        
        long diff = expectedSum - sum; // missing - repeated
        long sumDiff = expectedSumSq - sumSq; // missing^2 - repeated^2
        long sumEq = sumDiff / diff; // missing + repeated
        
        int missing = (int)((diff + sumEq) / 2);
        int repeated = (int)((sumEq - diff) / 2);
        
        return new int[]{repeated, missing};
    }
}
```

## 8. Maximum Number of Achievable Transfer Requests
**Đề bài chi tiết:** Có n tòa nhà, một mảng `requests` biểu diễn yêu cầu di chuyển. Tìm số yêu cầu lớn nhất có thể thỏa mãn sao cho số nhân viên mỗi tòa nhà không đổi (in-degree == out-degree). (LeetCode 1601)
**Phân tích thuật toán:**
Số lượng requests tối đa là 16. Sử dụng Bitmask (hoặc Backtracking) sinh tất cả tổ hợp request `(2^16)`. Tính bậc (degree) thay đổi của các tòa nhà. Nếu toàn bộ là 0, đếm số bit `1` trong mask và lấy giá trị lớn nhất.
**Độ phức tạp:** O(2^R * R) Thời gian, O(N) Không gian, với R là số lượng requests.
**Mã nguồn Java:**
```java
public class Solution {
    public int maximumRequests(int n, int[][] requests) {
        int r = requests.length;
        int max = 0;
        int maxMask = 1 << r;
        
        for (int mask = 0; mask < maxMask; mask++) {
            int[] net = new int[n];
            int count = 0;
            for (int i = 0; i < r; i++) {
                if ((mask & (1 << i)) != 0) {
                    net[requests[i][0]]--;
                    net[requests[i][1]]++;
                    count++;
                }
            }
            boolean valid = true;
            for (int v : net) {
                if (v != 0) {
                    valid = false;
                    break;
                }
            }
            if (valid) max = Math.max(max, count);
        }
        return max;
    }
}
```

## 9. Find the Longest Substring Containing Vowels in Even Counts
**Đề bài chi tiết:** Tìm chuỗi con dài nhất mà mỗi nguyên âm (a, e, i, o, u) đều xuất hiện một số chẵn lần. (LeetCode 1371)
**Phân tích thuật toán:**
Dùng một số nguyên 5 bit (Bitmask) để lưu tính chẵn lẻ (Parity) của 5 nguyên âm. `0` là chẵn, `1` là lẻ. Duyệt chuỗi, cập nhật bitmask. Nếu một mask lặp lại, khoảng cách từ vị trí xuất hiện đầu tiên của mask đó đến hiện tại chính là một chuỗi thỏa mãn.
**Độ phức tạp:** O(N) Thời gian, O(1) Không gian (mảng 32 phần tử).
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int findTheLongestSubstring(String s) {
        int[] firstOccur = new int[32];
        Arrays.fill(firstOccur, -2);
        firstOccur[0] = -1;
        
        int maxLen = 0;
        int mask = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == 'a') mask ^= (1 << 0);
            else if (c == 'e') mask ^= (1 << 1);
            else if (c == 'i') mask ^= (1 << 2);
            else if (c == 'o') mask ^= (1 << 3);
            else if (c == 'u') mask ^= (1 << 4);
            
            if (firstOccur[mask] != -2) {
                maxLen = Math.max(maxLen, i - firstOccur[mask]);
            } else {
                firstOccur[mask] = i;
            }
        }
        return maxLen;
    }
}
```

## 10. Minimum Cost to Connect Two Groups of Points (Bitmask math)
**Đề bài chi tiết:** Cho hai nhóm điểm A (cỡ `m`) và B (cỡ `n`), và ma trận giá tiền nối điểm. Mỗi điểm phải nối ít nhất 1 đường. Tìm chi phí nhỏ nhất. (LeetCode 1595)
**Phân tích thuật toán:**
DP với Bitmask. Giữ trạng thái của DP là `dp(i, mask)`: `i` là điểm hiện tại của nhóm 1, `mask` biểu diễn các điểm của nhóm 2 đã được kết nối. Khi ở trạng thái `i`, ta có thể nối điểm `i` với bất kỳ tập con nào của nhóm 2, sau đó cập nhật mask. Tuy nhiên, thay vì duyệt tất cả, ta tối ưu: Nối điểm `i` với đúng 1 điểm `j` ở nhóm 2 và chuyển qua trạng thái tiếp theo, phần còn thiếu của nhóm 2 cuối cùng sẽ được điền bằng cạnh rẻ nhất có thể.
**Độ phức tạp:** O(M * N * 2^N) Thời gian.
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.List;

public class Solution {
    public int connectTwoGroups(List<List<Integer>> cost) {
        int m = cost.size(), n = cost.get(0).size();
        int[] minCost2 = new int[n];
        Arrays.fill(minCost2, Integer.MAX_VALUE);
        for (int j = 0; j < n; j++) {
            for (int i = 0; i < m; i++) {
                minCost2[j] = Math.min(minCost2[j], cost.get(i).get(j));
            }
        }
        
        int[][] dp = new int[m + 1][1 << n];
        for (int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE / 2);
        dp[0][0] = 0;
        
        for (int i = 0; i < m; i++) {
            for (int mask = 0; mask < (1 << n); mask++) {
                for (int j = 0; j < n; j++) {
                    int nextMask = mask | (1 << j);
                    dp[i + 1][nextMask] = Math.min(dp[i + 1][nextMask], dp[i][mask] + cost.get(i).get(j));
                }
            }
        }
        
        int ans = Integer.MAX_VALUE;
        for (int mask = 0; mask < (1 << n); mask++) {
            int currentCost = dp[m][mask];
            for (int j = 0; j < n; j++) {
                if ((mask & (1 << j)) == 0) {
                    currentCost += minCost2[j];
                }
            }
            ans = Math.min(ans, currentCost);
        }
        return ans;
    }
}
```

## 11. Maximum XOR of Two Numbers in an Array
**Đề bài chi tiết:** Cho một mảng số nguyên `nums`. Trả về giá trị lớn nhất của `nums[i] ^ nums[j]`, trong đó `0 <= i <= j < n`. (LeetCode 421)
**Phân tích thuật toán:**
Sử dụng cấu trúc dữ liệu Trie để lưu trữ từng bit của các số từ trái sang phải (từ bit cao nhất xuống thấp nhất, thường là 31 xuống 0). Để tối đa hóa XOR với một số `x`, ta duyệt Trie theo nguyên tắc: luôn cố gắng đi theo nhánh có bit ngược với bit hiện tại của `x` (tức là ưu tiên chọn `1 - bit`). Nếu không có nhánh đó, ta buộc phải đi theo nhánh cùng bit.
**Độ phức tạp:** O(N * L) Thời gian, O(N * L) Không gian, với L = 32 (số lượng bit lớn nhất).
**Mã nguồn Java:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[2];
}

public class Solution {
    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();
        for (int num : nums) {
            TrieNode node = root;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >>> i) & 1;
                if (node.children[bit] == null) {
                    node.children[bit] = new TrieNode();
                }
                node = node.children[bit];
            }
        }
        
        int max = 0;
        for (int num : nums) {
            TrieNode node = root;
            int currentMax = 0;
            for (int i = 31; i >= 0; i--) {
                int bit = (num >>> i) & 1;
                int oppositeBit = 1 - bit;
                if (node.children[oppositeBit] != null) {
                    currentMax |= (1 << i);
                    node = node.children[oppositeBit];
                } else {
                    node = node.children[bit];
                }
            }
            max = Math.max(max, currentMax);
        }
        return max;
    }
}
```

## 12. Single Number I, II, III
**Đề bài chi tiết:** 
- **Single Number I:** Mỗi phần tử xuất hiện 2 lần, ngoại trừ 1 phần tử xuất hiện 1 lần. Tìm phần tử đó.
- **Single Number II:** Mỗi phần tử xuất hiện 3 lần, ngoại trừ 1 phần tử xuất hiện 1 lần.
- **Single Number III:** Mỗi phần tử xuất hiện 2 lần, ngoại trừ 2 phần tử xuất hiện 1 lần.
**Phân tích thuật toán:**
- **I:** XOR toàn bộ mảng. Các cặp giống nhau XOR bằng 0, giá trị còn lại là phần tử xuất hiện 1 lần.
- **II:** Đếm số lượng bit 1 tại mỗi vị trí `i` (từ 0 đến 31) của tất cả các số. Nếu tổng số bit 1 chia hết cho 3 thì bit `i` của kết quả là 0, ngược lại là 1. Dùng State Machine sẽ cho không gian O(1) và cực kì nhanh.
- **III:** XOR toàn mảng được `X ^ Y`. Lấy bit 1 khác biệt đầu tiên (`XOR & -XOR`) để chia mảng thành 2 nhóm, mỗi nhóm chứa 1 số duy nhất, sau đó XOR từng nhóm để tìm ra `X` và `Y`.
Dưới đây minh họa lời giải cho Single Number II.
**Độ phức tạp:** O(N) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int singleNumber(int[] nums) {
        int ones = 0, twos = 0;
        for (int num : nums) {
            ones = (ones ^ num) & ~twos;
            twos = (twos ^ num) & ~ones;
        }
        return ones;
    }
}
```

## 13. Number of 1 Bits (Hamming Weight)
**Đề bài chi tiết:** Cho một số nguyên không âm `n`, đếm số lượng bit `1` trong biểu diễn nhị phân của nó (còn được gọi là Hamming weight). (LeetCode 191)
**Phân tích thuật toán:**
Cách tối ưu nhất là sử dụng phép toán `n & (n - 1)`. Phép toán này sẽ xóa đi bit `1` ở vị trí thấp nhất (phải cùng) của `n`. Ta lặp quá trình này và tăng biến đếm lên 1 mỗi lần cho đến khi `n == 0`.
**Độ phức tạp:** O(K) Thời gian (với K là số lượng bit 1 trong n), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n = n & (n - 1);
            count++;
        }
        return count;
    }
}
```

## 14. Counting Bits
**Đề bài chi tiết:** Cho số nguyên `n`, trả về một mảng có độ dài `n + 1` mà mỗi phần tử tại vị trí `i` (`0 <= i <= n`) là số lượng bit `1` trong biểu diễn nhị phân của `i`. (LeetCode 338)
**Phân tích thuật toán:**
Sử dụng Quy hoạch động (Dynamic Programming) kết hợp Bit Manipulation. Đối với số `i`, số lượng bit `1` có thể tính dựa trên số lượng bit `1` của `i >> 1` (giá trị đã tính trước đó) cộng thêm bit cuối cùng của `i` (`i & 1`). Công thức: `dp[i] = dp[i >> 1] + (i & 1)`.
**Độ phức tạp:** O(N) Thời gian, O(N) Không gian (trả về mảng).
**Mã nguồn Java:**
```java
public class Solution {
    public int[] countBits(int n) {
        int[] ans = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            ans[i] = ans[i >> 1] + (i & 1);
        }
        return ans;
    }
}
```

## 15. Power of Two, Four
**Đề bài chi tiết:** Kiểm tra xem một số nguyên `n` có phải là lũy thừa của 2 (hoặc 4) hay không. (LeetCode 231, 342)
**Phân tích thuật toán:**
- **Power of Two:** Số nguyên dương `n` là lũy thừa của 2 khi và chỉ khi nó có duy nhất một bit `1`. Điều kiện là `n > 0` và `(n & (n - 1)) == 0`.
- **Power of Four:** Ngoài điều kiện là lũy thừa của 2, số lũy thừa của 4 còn phải có bit `1` nằm ở các vị trí chẵn (0, 2, 4,...). Dùng bitmask `0x55555555` (dãy nhị phân `01010101...`) để kiểm tra `(n & 0x55555555) != 0`.
Dưới đây là lời giải cho Power of Four.
**Độ phức tạp:** O(1) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isPowerOfFour(int n) {
        // Kiểm tra dương và lũy thừa của 2
        if (n <= 0 || (n & (n - 1)) != 0) {
            return false;
        }
        // Kiểm tra bit 1 nằm ở vị trí chẵn
        return (n & 0x55555555) != 0;
    }
}
```

## 16. Reverse Bits
**Đề bài chi tiết:** Đảo ngược các bit của một số nguyên không dấu 32-bit. (LeetCode 190)
**Phân tích thuật toán:**
Khởi tạo biến `res = 0`. Lặp 32 lần (vì số nguyên 32 bit). Tại mỗi vòng lặp, trích xuất bit cuối của `n` (`n & 1`), dịch trái kết quả đó tới vị trí tương ứng (`31 - i`) và dùng phép OR (`|`) để gộp vào `res`. Đồng thời, dịch logic `n` sang phải 1 bit (`n >>> 1`).
**Độ phức tạp:** O(1) Thời gian (cố định 32 vòng lặp), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int reverseBits(int n) {
        int res = 0;
        for (int i = 0; i < 32; i++) {
            int bit = n & 1;
            res |= (bit << (31 - i));
            n >>>= 1;
        }
        return res;
    }
}
```

## 17. Bitwise AND of Numbers Range
**Đề bài chi tiết:** Cho hai số nguyên `left` và `right` biểu diễn một đoạn `[left, right]`. Trả về kết quả của phép Bitwise AND cho tất cả các số nằm trong đoạn này. (LeetCode 201)
**Phân tích thuật toán:**
Nếu `left` và `right` không bằng nhau, phần chênh lệch sẽ làm cho các bit thấp biến đổi liên tục (chứa cả 0 và 1), phép AND của các bit đó sẽ là 0. Do đó, kết quả thực chất là **tiền tố bit chung (common bit prefix)** của `left` và `right`. Ta dịch phải cả hai số cho đến khi chúng bằng nhau, đồng thời đếm số bước dịch `shift`. Cuối cùng, dịch trái giá trị chung lên `shift` bước.
**Độ phức tạp:** O(1) Thời gian (tối đa 32 vòng lặp), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int shift = 0;
        while (left < right) {
            left >>= 1;
            right >>= 1;
            shift++;
        }
        return left << shift;
    }
}
```

## 18. Maximum Product of Word Lengths
**Đề bài chi tiết:** Cho mảng chuỗi `words`. Tìm giá trị lớn nhất của `words[i].length() * words[j].length()` sao cho 2 chuỗi không chứa ký tự chung. (LeetCode 318)
**Phân tích thuật toán:**
Sử dụng một mảng số nguyên (bitmask) để đại diện cho mỗi chuỗi. Mỗi bit thứ `k` (0-25) sẽ là 1 nếu ký tự thứ `k` (a-z) xuất hiện trong chuỗi. Kích thước chuỗi được lưu riêng. Hai chuỗi không có ký tự chung khi và chỉ khi `mask[i] & mask[j] == 0`. Sau khi tạo mask cho tất cả các chuỗi, ta dùng vòng lặp đôi để kiểm tra và cập nhật tích lớn nhất.
**Độ phức tạp:** O(N^2 + L) Thời gian (N là số lượng từ, L là tổng độ dài các từ), O(N) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int maxProduct(String[] words) {
        int n = words.length;
        int[] masks = new int[n];
        for (int i = 0; i < n; i++) {
            for (char c : words[i].toCharArray()) {
                masks[i] |= (1 << (c - 'a'));
            }
        }
        
        int maxProd = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if ((masks[i] & masks[j]) == 0) {
                    maxProd = Math.max(maxProd, words[i].length() * words[j].length());
                }
            }
        }
        return maxProd;
    }
}
```

## 19. Subsets (Power Set)
**Đề bài chi tiết:** Cho một mảng `nums` chứa các số nguyên phân biệt. Trả về toàn bộ tập con có thể có (powerset). (LeetCode 78)
**Phân tích thuật toán:**
Một mảng có `n` phần tử sẽ có `2^n` tập con. Ta có thể duyệt các số nguyên từ `0` đến `2^n - 1`. Đối với mỗi số nguyên, biểu diễn nhị phân của nó sẽ chỉ ra phần tử nào được đưa vào tập con: nếu bit thứ `j` là 1, ta thêm `nums[j]` vào tập con hiện tại.
**Độ phức tạp:** O(N * 2^N) Thời gian, O(N * 2^N) Không gian (lưu kết quả).
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        int totalSubsets = 1 << n; // 2^n
        
        for (int mask = 0; mask < totalSubsets; mask++) {
            List<Integer> currentSubset = new ArrayList<>();
            for (int j = 0; j < n; j++) {
                // Kiểm tra bit j của mask có phải là 1 không
                if ((mask & (1 << j)) != 0) {
                    currentSubset.add(nums[j]);
                }
            }
            res.add(currentSubset);
        }
        
        return res;
    }
}
```

## 20. Total Hamming Distance
**Đề bài chi tiết:** Cho một mảng các số nguyên `nums`. Khoảng cách Hamming giữa hai số là số vị trí bit khác nhau giữa chúng. Tìm tổng khoảng cách Hamming giữa tất cả các cặp số trong mảng. (LeetCode 477)
**Phân tích thuật toán:**
Đừng duyệt qua tất cả các cặp (độ phức tạp O(N^2)). Thay vào đó, xét từng bit từ `0` đến `31`. Tại mỗi vị trí bit `i`, đếm số lượng số trong `nums` có bit này bằng `1` (giả sử là `count1`), và số lượng có bit này bằng `0` (là `N - count1`). Khoảng cách Hamming tổng cộng tạo ra tại bit `i` giữa tất cả các cặp sẽ là `count1 * (N - count1)`. Tính tổng lượng này ở tất cả 32 vị trí bit.
**Độ phức tạp:** O(N) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int totalHammingDistance(int[] nums) {
        int total = 0;
        int n = nums.length;
        
        for (int i = 0; i < 32; i++) {
            int count1 = 0;
            for (int num : nums) {
                if (((num >> i) & 1) == 1) {
                    count1++;
                }
            }
            total += count1 * (n - count1);
        }
        
        return total;
    }
}
```

## 21. Maximum XOR with an Element From Array
**Đề bài chi tiết:** Cho một mảng `nums` và mảng `queries`, trong đó `queries[i] = [x_i, m_i]`. Với mỗi truy vấn, tìm giá trị lớn nhất của `x_i ^ nums[j]` sao cho `nums[j] <= m_i`. Nếu không có phần tử nào thỏa mãn, trả về -1. (LeetCode 1707)
**Phân tích thuật toán:**
Dùng kĩ thuật Offline Queries (xử lý ngoại tuyến): Sắp xếp mảng `nums` tăng dần và mảng `queries` tăng dần theo `m_i`. Duyệt qua các truy vấn đã sắp xếp. Khi xử lý một truy vấn, lần lượt chèn các phần tử `nums[j] <= m_i` vào một cây Trie. Sau đó, tìm giá trị XOR lớn nhất giữa `x_i` và các phần tử trong Trie (giống bài toán Maximum XOR of Two Numbers).
**Độ phức tạp:** O(N log N + Q log Q + (N + Q) * 32) Thời gian, O(N * 32 + Q) Không gian.
**Mã nguồn Java:**
```java
import java.util.Arrays;

class TrieNode {
    TrieNode[] children = new TrieNode[2];
}

class Trie {
    TrieNode root = new TrieNode();
    
    public void insert(int num) {
        TrieNode node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.children[bit] == null) {
                node.children[bit] = new TrieNode();
            }
            node = node.children[bit];
        }
    }
    
    public int getMaxXor(int num) {
        if (root.children[0] == null && root.children[1] == null) return -1;
        TrieNode node = root;
        int maxXor = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int oppBit = 1 - bit;
            if (node.children[oppBit] != null) {
                maxXor |= (1 << i);
                node = node.children[oppBit];
            } else {
                node = node.children[bit];
            }
        }
        return maxXor;
    }
}

public class Solution {
    public int[] maximizeXor(int[] nums, int[][] queries) {
        Arrays.sort(nums);
        int q = queries.length;
        int[][] qs = new int[q][3];
        for (int i = 0; i < q; i++) {
            qs[i][0] = queries[i][0]; // x_i
            qs[i][1] = queries[i][1]; // m_i
            qs[i][2] = i; // original index
        }
        Arrays.sort(qs, (a, b) -> Integer.compare(a[1], b[1]));
        
        int[] ans = new int[q];
        Trie trie = new Trie();
        int idx = 0;
        
        for (int[] query : qs) {
            int x = query[0], m = query[1], originalIdx = query[2];
            while (idx < nums.length && nums[idx] <= m) {
                trie.insert(nums[idx]);
                idx++;
            }
            ans[originalIdx] = trie.getMaxXor(x);
        }
        return ans;
    }
}
```

## 22. XOR Operation in an Array
**Đề bài chi tiết:** Cho số nguyên `n` và `start`. Trả về kết quả XOR của tất cả phần tử `nums[i] = start + 2 * i` (mảng 0-indexed, với `i` từ `0` đến `n-1`). (LeetCode 1486)
**Phân tích thuật toán:**
Để mô phỏng, ta duyệt tuyến tính qua `n` phần tử và tính XOR dần dần, thao tác rất đơn giản và an toàn với `n <= 1000`. Nếu tối ưu O(1), ta xét tính chất: số hạng có bước nhảy là 2, bit cuối (LSB) của kết quả phụ thuộc vào `start` và `n` có lẻ hay không. Các bit cao có thể tính nhờ vào chuỗi XOR chuẩn từ 1 tới k. Giải pháp dưới đây dùng mô phỏng O(N) thông dụng nhất.
**Độ phức tạp:** O(N) Thời gian, O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int xorOperation(int n, int start) {
        int res = 0;
        for (int i = 0; i < n; i++) {
            res ^= (start + 2 * i);
        }
        return res;
    }
}
```

## 23. Decode XORed Array
**Đề bài chi tiết:** Có một mảng số nguyên ẩn `arr`. Mảng `encoded` được tạo ra với độ dài ngắn hơn 1 phần tử sao cho `encoded[i] = arr[i] ^ arr[i + 1]`. Cho mảng `encoded` và giá trị phần tử đầu tiên `first`, hãy giải mã để trả về `arr`. (LeetCode 1720)
**Phân tích thuật toán:**
Dựa vào tính chất đối xứng của phép XOR: Nếu `a ^ b = c` thì `a ^ c = b`. Vì ta đã biết `arr[0] = first` và `encoded[i] = arr[i] ^ arr[i+1]`, ta có thể dễ dàng tính phần tử tiếp theo: `arr[i+1] = arr[i] ^ encoded[i]`. Duyệt tuyến tính qua mảng `encoded` để điền đầy đủ `arr`.
**Độ phức tạp:** O(N) Thời gian, O(N) Không gian (cho mảng kết quả).
**Mã nguồn Java:**
```java
public class Solution {
    public int[] decode(int[] encoded, int first) {
        int n = encoded.length;
        int[] arr = new int[n + 1];
        arr[0] = first;
        for (int i = 0; i < n; i++) {
            arr[i + 1] = arr[i] ^ encoded[i];
        }
        return arr;
    }
}
```

## 24. Find the Kth Largest Integer in the Array (String Form)
**Đề bài chi tiết:** Cho một mảng chuỗi `nums` biểu diễn các số nguyên lớn và số nguyên `k`. Trả về số nguyên lớn thứ `k` dưới dạng chuỗi (không thể ép kiểu trực tiếp thành int hay long do giới hạn chiều dài). (LeetCode 1985)
**Phân tích thuật toán:**
Dù bài toán không thuần túy là Bit Manipulation, ý tưởng tư duy chiều dài chuỗi tương đương số bit hoặc số lượng chữ số rất quan trọng: chuỗi dài hơn đại diện cho số lớn hơn. Nếu chiều dài bằng nhau, so sánh theo thứ tự từ điển (lexicographical order) sẽ đúng bằng so sánh giá trị số. Ta dùng PriorityQueue (Min-Heap) lưu `k` phần tử lớn nhất cùng bộ Comparator tuỳ chỉnh trên.
**Độ phức tạp:** O(N log K * L) Thời gian (L là độ dài tối đa của chuỗi), O(K) Không gian.
**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

public class Solution {
    public String kthLargestNumber(String[] nums, int k) {
        PriorityQueue<String> minHeap = new PriorityQueue<>((a, b) -> {
            if (a.length() == b.length()) {
                return a.compareTo(b); // Độ dài bằng, so sánh từng chữ số
            }
            return Integer.compare(a.length(), b.length()); // Ưu tiên chiều dài
        });
        
        for (String num : nums) {
            minHeap.offer(num);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
        
        return minHeap.peek();
    }
}
```

## 25. Minimum Flips to Make a OR b == c
**Đề bài chi tiết:** Cho 3 số dương `a, b, c`. Tìm số lần lật bit tối thiểu trên `a` và `b` sao cho kết quả phép toán `a | b == c`. (LeetCode 1318)
**Phân tích thuật toán:**
Duyệt qua 32 vị trí bit của `a, b, c`. Xét bit ở vị trí thứ `i` của 3 số là `bitA, bitB, bitC`.
- Nếu `bitC == 1`: Ta cần ít nhất một trong `bitA` hoặc `bitB` là `1`. Nếu cả hai đều `0`, ta cần lật 1 trong 2 bit (tăng flip lên 1).
- Nếu `bitC == 0`: Ta cần CẢ `bitA` và `bitB` đều phải là `0`. Số lượng flip cần thiết chính là tổng của `bitA + bitB`.
**Độ phức tạp:** O(1) Thời gian (luôn lặp 32 lần), O(1) Không gian.
**Mã nguồn Java:**
```java
public class Solution {
    public int minFlips(int a, int b, int c) {
        int flips = 0;
        for (int i = 0; i < 32; i++) {
            int bitA = (a >> i) & 1;
            int bitB = (b >> i) & 1;
            int bitC = (c >> i) & 1;
            
            if (bitC == 1) {
                if (bitA == 0 && bitB == 0) flips++;
            } else {
                flips += (bitA + bitB);
            }
        }
        return flips;
    }
}
```

## 26. Sort Integers by The Number of 1 Bits
**Đề bài chi tiết:** Cho một mảng `arr`. Sắp xếp các số nguyên trong mảng theo số lượng bit `1` trong biểu diễn nhị phân của nó theo thứ tự tăng dần. Nếu có cùng số lượng bit `1`, sắp xếp theo giá trị thực của chúng tăng dần. (LeetCode 1356)
**Phân tích thuật toán:**
Chuyển đổi mảng nguyên thủy (primitive array) thành mảng đối tượng `Integer` (hoặc dùng stream) để áp dụng hàm Comparator tuỳ chỉnh. Sử dụng hàm đếm `Integer.bitCount(num)` có sẵn (tự triển khai bằng thuật toán Brian Kernighan `n & (n - 1)`) để lấy số lượng bit `1` của hai số và đối chiếu theo yêu cầu.
**Độ phức tạp:** O(N log N) Thời gian, O(N) Không gian (cho mảng trung gian).
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int[] sortByBits(int[] arr) {
        Integer[] nums = new Integer[arr.length];
        for (int i = 0; i < arr.length; i++) {
            nums[i] = arr[i];
        }
        
        Arrays.sort(nums, (a, b) -> {
            int countA = Integer.bitCount(a);
            int countB = Integer.bitCount(b);
            if (countA == countB) {
                return Integer.compare(a, b);
            }
            return Integer.compare(countA, countB);
        });
        
        for (int i = 0; i < arr.length; i++) {
            arr[i] = nums[i];
        }
        
        return arr;
    }
}
```

## 27. Beautiful Arrangement
**Đề bài chi tiết:** Có số `n`, hãy đếm số lượng các mảng hoán vị của các số từ `1` đến `n` sao cho phần tử thứ `i` (1-indexed) thỏa mãn: `arr[i] % i == 0` hoặc `i % arr[i] == 0`. (LeetCode 526)
**Phân tích thuật toán:**
Dùng thuật toán Quay lui (Backtracking). Trạng thái của các số đã sử dụng có thể được biểu diễn bằng một số nguyên (bitmask), trong đó bit thứ `j` (1-indexed) bằng 1 tức là số `j` đã được điền. Nhờ cách cắt tỉa nhánh (pruning) thông minh: chỉ tiếp tục đệ quy nếu vị trí hiện tại điền số thỏa mãn phép chia hết, số lượng cấu hình cần duyệt giảm đi rất nhiều.
**Độ phức tạp:** Khó đánh giá chính xác, tiệm cận O(K) với K là số lượng trạng thái hợp lệ. Không gian O(N) cho ngăn xếp gọi đệ quy.
**Mã nguồn Java:**
```java
public class Solution {
    int count = 0;

    public int countArrangement(int n) {
        backtrack(n, 1, 0);
        return count;
    }
    
    private void backtrack(int n, int pos, int mask) {
        if (pos > n) {
            count++;
            return;
        }
        for (int i = 1; i <= n; i++) {
            // Nếu bit i chưa được thiết lập
            if ((mask & (1 << i)) == 0) {
                if (i % pos == 0 || pos % i == 0) {
                    backtrack(n, pos + 1, mask | (1 << i));
                }
            }
        }
    }
}
```

## 28. Number of Valid Words for Each Puzzle
**Đề bài chi tiết:** Cho danh sách các từ `words` và câu đố `puzzles`. Một `word` hợp lệ cho một `puzzle` nếu `word` chứa chữ cái đầu tiên của `puzzle`, và mọi chữ cái trong `word` đều nằm trong `puzzle`. Đếm số lượng từ hợp lệ cho mỗi câu đố. (LeetCode 1178)
**Phân tích thuật toán:**
Mã hóa từng `word` thành một bitmask 26-bit chứa các kí tự có trong từ và lưu tần suất vào HashMap. Với mỗi `puzzle` (luôn có 7 kí tự), mã hóa thành một bitmask. Ta sinh tất cả các tập con (submask) của puzzle mask này bằng vòng lặp `submask = (submask - 1) & mask`. Với từng submask, kiểm tra xem nó có mang bit của kí tự đầu tiên trong puzzle không, nếu có thì cộng số lượng word tương ứng từ HashMap vào.
**Độ phức tạp:** O(W * L_w + P * 2^7) Thời gian, O(W) Không gian.
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Solution {
    public List<Integer> findNumOfValidWords(String[] words, String[] puzzles) {
        Map<Integer, Integer> wordCount = new HashMap<>();
        for (String word : words) {
            int mask = 0;
            for (char c : word.toCharArray()) {
                mask |= (1 << (c - 'a'));
            }
            wordCount.put(mask, wordCount.getOrDefault(mask, 0) + 1);
        }
        
        List<Integer> res = new ArrayList<>();
        for (String puzzle : puzzles) {
            int mask = 0;
            for (char c : puzzle.toCharArray()) {
                mask |= (1 << (c - 'a'));
            }
            int firstBit = 1 << (puzzle.charAt(0) - 'a');
            
            int count = 0;
            int submask = mask;
            while (submask != 0) {
                if ((submask & firstBit) != 0) {
                    count += wordCount.getOrDefault(submask, 0);
                }
                submask = (submask - 1) & mask;
            }
            res.add(count);
        }
        return res;
    }
}
```

## 29. Find Kth Bit in Nth Binary String
**Đề bài chi tiết:** Sinh một chuỗi nhị phân $S_n$ bắt đầu từ $S_1 = "0"$, $S_i = S_{i-1} + "1" + \text{reverse}(\text{invert}(S_{i-1}))$. Tìm ký tự thứ `k` (1-indexed) trong $S_n$. (LeetCode 1545)
**Phân tích thuật toán:**
Chiều dài của $S_n$ là $2^n - 1$. Ký tự đứng ở trung tâm luôn là '1' (nằm ở vị trí $2^{n-1}$).
Dùng Đệ quy / Chia để trị: 
- Nếu $k == 2^{n-1}$, phần tử là '1'.
- Nếu $k < 2^{n-1}$, phần tử nằm ở nửa đầu $S_{n-1}$, ta gọi đệ quy tiếp.
- Nếu $k > 2^{n-1}$, phần tử nằm ở nửa sau, vốn là phản chiếu đối xứng đảo của nửa đầu. Ta gọi đệ quy cho vị trí $(2^n - k)$ trong $S_{n-1}$ và lật bit kết quả (invert).
**Độ phức tạp:** O(N) Thời gian (do chiều sâu lớn nhất là n), O(N) Không gian cho đệ quy.
**Mã nguồn Java:**
```java
public class Solution {
    public char findKthBit(int n, int k) {
        if (n == 1) return '0';
        
        int mid = 1 << (n - 1);
        if (k == mid) {
            return '1';
        } else if (k < mid) {
            return findKthBit(n - 1, k);
        } else {
            char c = findKthBit(n - 1, (1 << n) - k);
            return c == '0' ? '1' : '0';
        }
    }
}
```

## 30. Evaluate Boolean Binary Tree
**Đề bài chi tiết:** Cho một cây nhị phân đầy đủ chứa 0 (False), 1 (True), 2 (Toán tử OR), 3 (Toán tử AND). Các nút lá chỉ mang giá trị 0 hoặc 1. Các nút nhánh mang giá trị 2 hoặc 3. Hãy đánh giá giá trị boolean của toàn bộ cây từ dưới lên. (LeetCode 2331)
**Phân tích thuật toán:**
Duyệt cây theo kiểu Depth First Search (Post-order Traversal).
- Gặp nút lá (không có con trái, phải): Trả về giá trị boolean tương ứng `(val == 1)`.
- Ở mỗi nút nhánh: Gọi đệ quy lấy giá trị đánh giá của nhánh trái và phải. Nếu nút gốc là `2` thì dùng phép OR `(||)` cho 2 kết quả. Nếu là `3`, dùng phép AND `(&&)` (toán tử logic tương đồng với Bitwise OR/AND áp dụng trên 1 bit).
**Độ phức tạp:** O(N) Thời gian với N là số lượng nút, O(H) Không gian cho call stack (H là chiều cao cây).
**Mã nguồn Java:**
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
public class Solution {
    public boolean evaluateTree(TreeNode root) {
        if (root.left == null && root.right == null) {
            return root.val == 1;
        }
        
        boolean left = evaluateTree(root.left);
        boolean right = evaluateTree(root.right);
        
        if (root.val == 2) {
            return left || right;
        } else {
            return left && right;
        }
    }
}
```
