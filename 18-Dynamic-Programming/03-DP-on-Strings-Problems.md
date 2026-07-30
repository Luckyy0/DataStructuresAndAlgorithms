# Thực hành: Dynamic Programming on Strings

Dưới đây là danh sách 30 bài tập ứng dụng Quy hoạch động trên chuỗi (DP on Strings). 10 bài đầu tiên đi kèm đề bài chi tiết, phân tích thuật toán, mã nguồn Java và độ phức tạp. 20 bài sau được tóm tắt ý tưởng chính.

## 1. Longest Palindromic Substring
**Đề bài chi tiết:** Cho chuỗi `s`, tìm chuỗi con liên tiếp (substring) đối xứng dài nhất trong `s`.
**Phân tích thuật toán:**
Dùng mảng DP 2 chiều `dp[i][j]` kiểu boolean để lưu trữ xem đoạn từ `i` đến `j` có phải là Palindrome không.
- `dp[i][i] = true` (Ký tự đơn luôn đối xứng).
- `dp[i][j] = (s.charAt(i) == s.charAt(j)) && dp[i+1][j-1]` (Với chuỗi độ dài > 2).
Khởi tạo và lặp theo khoảng cách `len` từ 1 đến N.
**Mã nguồn Java:**
```java
public class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 2) return s;
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int start = 0, maxLen = 1;

        for (int i = 0; i < n; i++) dp[i][i] = true;

        for (int len = 2; len <= n; len++) {
            for (int i = 0; i < n - len + 1; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    if (len == 2 || dp[i + 1][j - 1]) {
                        dp[i][j] = true;
                        if (len > maxLen) {
                            maxLen = len;
                            start = i;
                        }
                    }
                }
            }
        }
        return s.substring(start, start + maxLen);
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 2. Palindromic Substrings
**Đề bài chi tiết:** Cho chuỗi `s`, đếm tổng số lượng chuỗi con đối xứng (palindromic substrings) có trong chuỗi.
**Phân tích thuật toán:**
Tương tự bài 1, nhưng thay vì chỉ tìm chuỗi dài nhất, ta tăng bộ đếm lên mỗi khi phát hiện một `dp[i][j] == true`.
**Mã nguồn Java:**
```java
public class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int count = 0;

        for (int len = 1; len <= n; len++) {
            for (int i = 0; i < n - len + 1; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    if (len <= 2 || dp[i + 1][j - 1]) {
                        dp[i][j] = true;
                        count++;
                    }
                }
            }
        }
        return count;
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 3. Longest Common Subsequence
**Đề bài chi tiết:** Cho hai chuỗi `text1` và `text2`, trả về độ dài của dãy con chung dài nhất (LCS).
**Phân tích thuật toán:**
`dp[i][j]` là độ dài LCS của tiền tố `text1[0..i-1]` và `text2[0..j-1]`.
Nếu `text1[i-1] == text2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1`
Ngược lại: `dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1])`.
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
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 4. Edit Distance
**Đề bài chi tiết:** Cho hai chuỗi `word1` và `word2`, tìm số thao tác tối thiểu (chèn, xóa, thay thế) để biến `word1` thành `word2`.
**Phân tích thuật toán:**
- Thêm (Insert): `dp[i][j-1] + 1`
- Xóa (Delete): `dp[i-1][j] + 1`
- Thay thế (Replace): `dp[i-1][j-1] + 1`
Lấy min của cả 3 trường hợp.
**Mã nguồn Java:**
```java
public class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
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
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 5. Distinct Subsequences
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t`, đếm số lượng dãy con của `s` bằng với `t`.
**Phân tích thuật toán:**
`dp[i][j]` là số cách tạo chuỗi `t[0..j-1]` từ `s[0..i-1]`.
Nếu ký tự trùng, ta có thể dùng ký tự đó hoặc bỏ qua: `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]`.
Nếu không trùng, ta bắt buộc bỏ qua: `dp[i][j] = dp[i-1][j]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int numDistinct(String s, String t) {
        int m = s.length(), n = t.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 0; i <= m; i++) dp[i][0] = 1;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == t.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 6. Interleaving String
**Đề bài chi tiết:** Kiểm tra xem chuỗi `s3` có được hình thành bởi sự xen kẽ (interleaving) của chuỗi `s1` và `s2` hay không.
**Phân tích thuật toán:**
`dp[i][j]` trả về true nếu `s3[0..i+j-1]` được ghép bởi `s1[0..i-1]` và `s2[0..j-1]`.
Nếu ký tự hiện tại của `s3` khớp `s1`, trạng thái là `dp[i-1][j]`. Nếu khớp `s2`, trạng thái là `dp[i][j-1]`.
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
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 7. Regular Expression Matching
**Đề bài chi tiết:** Triển khai Regular Expression Matching hỗ trợ `.` (khớp 1 ký tự) và `*` (khớp 0 hoặc nhiều ký tự trước đó).
**Phân tích thuật toán:**
Xét `p[j-1] == '*'`, ta có thể:
- Bỏ qua pattern `X*`: `dp[i][j] = dp[i][j-2]`.
- Dùng pattern `X*` để khớp 1 ký tự của chuỗi `s`: Yêu cầu `s[i-1]` và `p[j-2]` phải khớp (`.` hoặc ký tự giống nhau), dẫn đến trạng thái `dp[i-1][j]`.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;

        for (int j = 2; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 2] || (dp[i - 1][j] && 
                              (s.charAt(i - 1) == p.charAt(j - 2) || p.charAt(j - 2) == '.'));
                } else if (p.charAt(j - 1) == '.' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 8. Wildcard Matching
**Đề bài chi tiết:** Triển khai Wildcard Matching hỗ trợ `?` (khớp 1 ký tự) và `*` (khớp bất kỳ chuỗi ký tự nào, kể cả rỗng).
**Phân tích thuật toán:**
Sự khác biệt là `*` ở đây đại diện cho bất kỳ chuỗi nào.
Nếu `p[j-1] == '*'`:
Khớp 0 ký tự (`dp[i][j-1]`) hoặc khớp 1 ký tự và giữ lại `*` (`dp[i-1][j]`).
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;

        for (int j = 1; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 1];
            }
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                } else if (p.charAt(j - 1) == '?' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 9. Delete Operation for Two Strings
**Đề bài chi tiết:** Tìm số bước tối thiểu để biến hai chuỗi `word1` và `word2` thành giống nhau, bằng cách chỉ xóa ký tự.
**Phân tích thuật toán:**
Dùng biến thể của bài toán LCS. Số thao tác cần thiết bằng tổng chiều dài hai chuỗi trừ đi 2 lần chiều dài LCS. Hoặc dùng DP tính khoảng cách trực tiếp.
**Mã nguồn Java:**
```java
public class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length(), n = word2.length();
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        int lcs = dp[m][n];
        return m + n - 2 * lcs;
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 10. Minimum ASCII Delete Sum for Two Strings
**Đề bài chi tiết:** Tương tự bài 9, nhưng yêu cầu tối thiểu hóa tổng mã ASCII của các ký tự bị xóa.
**Phân tích thuật toán:**
Mảng `dp[i][j]` lưu chi phí ASCII xóa nhỏ nhất cho tiền tố `i` và `j`. Base cases cần cộng dồn các ký tự xóa. Nếu bằng nhau, `dp[i][j] = dp[i-1][j-1]`. Ngược lại, lấy min của việc xóa ở chuỗi thứ nhất hay xóa ở chuỗi thứ 2.
**Mã nguồn Java:**
```java
public class Solution {
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 1; i <= m; i++) dp[i][0] = dp[i - 1][0] + s1.charAt(i - 1);
        for (int j = 1; j <= n; j++) dp[0][j] = dp[0][j - 1] + s2.charAt(j - 1);

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(dp[i - 1][j] + s1.charAt(i - 1), 
                                        dp[i][j - 1] + s2.charAt(j - 1));
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 11. Longest Palindromic Subsequence
**Đề bài chi tiết:** Cho chuỗi `s`, tìm độ dài của dãy con đối xứng (palindromic subsequence) dài nhất trong `s`.
**Phân tích thuật toán:**
Có thể giải bằng cách tìm Longest Common Subsequence (LCS) giữa chuỗi `s` và chuỗi đảo ngược của `s`. Hoặc dùng mảng DP 2 chiều `dp[i][j]` lưu độ dài Palindromic Subsequence dài nhất trong khoảng từ `i` đến `j`.
- Nếu `s.charAt(i) == s.charAt(j)`: `dp[i][j] = dp[i+1][j-1] + 2`.
- Ngược lại: `dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1])`.
**Mã nguồn Java:**
```java
public class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int i = n - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 12. Longest Repeating Subsequence
**Đề bài chi tiết:** Cho chuỗi `s`, tìm độ dài dãy con lặp lại dài nhất (longest repeating subsequence) sao cho hai dãy con này không có cùng chỉ số tại cùng một vị trí.
**Phân tích thuật toán:**
Bài toán có thể được quy về việc tìm Longest Common Subsequence (LCS) của chuỗi `s` với chính nó, nhưng với điều kiện bổ sung là `i != j` (không được trùng vị trí).
**Mã nguồn Java:**
```java
public class Solution {
    public int longestRepeatingSubsequence(String s) {
        int n = s.length();
        int[][] dp = new int[n + 1][n + 1];
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == s.charAt(j - 1) && i != j) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[n][n];
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 13. Shortest Common Supersequence
**Đề bài chi tiết:** Cho hai chuỗi `str1` và `str2`, tìm chuỗi ngắn nhất chứa cả `str1` và `str2` như là dãy con (subsequence).
**Phân tích thuật toán:**
Chiều dài của Shortest Common Supersequence bằng `m + n - LCS(str1, str2)`. Để xây dựng chuỗi kết quả, ta tính bảng DP của LCS trước, sau đó duyệt ngược (backtrack) từ `dp[m][n]` về `dp[0][0]` để nối các ký tự.
**Mã nguồn Java:**
```java
public class Solution {
    public String shortestCommonSupersequence(String str1, String str2) {
        int m = str1.length(), n = str2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        StringBuilder sb = new StringBuilder();
        int i = m, j = n;
        while (i > 0 && j > 0) {
            if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                sb.append(str1.charAt(i - 1));
                i--; j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                sb.append(str1.charAt(i - 1));
                i--;
            } else {
                sb.append(str2.charAt(j - 1));
                j--;
            }
        }
        while (i > 0) sb.append(str1.charAt(--i));
        while (j > 0) sb.append(str2.charAt(--j));
        return sb.reverse().toString();
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 14. Count Different Palindromic Subsequences
**Đề bài chi tiết:** Cho chuỗi `s`, đếm số lượng dãy con đối xứng (palindromic subsequences) phân biệt. Kết quả có thể rất lớn nên trả về modulo `10^9 + 7`.
**Phân tích thuật toán:**
Sử dụng DP 2 chiều `dp[i][j]` lưu số dãy con đối xứng phân biệt trong `s[i..j]`.
Nếu `s[i] == s[j]`:
- Tìm vị trí `left` và `right` của ký tự giống `s[i]` ở giữa đoạn.
- Tùy thuộc vào số lượng ký tự giống nhau ở giữa mà cộng trừ thích hợp để tránh đếm trùng lặp.
Nếu `s[i] != s[j]`: `dp[i][j] = dp[i+1][j] + dp[i][j-1] - dp[i+1][j-1]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int countPalindromicSubsequences(String s) {
        int n = s.length(), MOD = 1000000007;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) dp[i][i] = 1;
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i < n - len + 1; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    int left = i + 1, right = j - 1;
                    while (left <= right && s.charAt(left) != s.charAt(i)) left++;
                    while (left <= right && s.charAt(right) != s.charAt(j)) right--;
                    if (left > right) dp[i][j] = dp[i + 1][j - 1] * 2 + 2;
                    else if (left == right) dp[i][j] = dp[i + 1][j - 1] * 2 + 1;
                    else dp[i][j] = dp[i + 1][j - 1] * 2 - dp[left + 1][right - 1];
                } else {
                    dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];
                }
                dp[i][j] = dp[i][j] < 0 ? dp[i][j] + MOD : dp[i][j] % MOD;
            }
        }
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 15. Word Break
**Đề bài chi tiết:** Cho chuỗi `s` và một từ điển `wordDict`, kiểm tra xem có thể phân tách `s` thành một dãy các từ có trong từ điển hay không (các từ có thể tái sử dụng).
**Phân tích thuật toán:**
Dùng mảng DP 1 chiều `dp[i]` đánh dấu xem tiền tố độ dài `i` của `s` có thể được tạo thành từ từ điển hay không.
`dp[i] = true` nếu tồn tại `j < i` sao cho `dp[j] == true` và phần chuỗi từ `j` đến `i` nằm trong `wordDict`.
**Mã nguồn Java:**
```java
import java.util.List;
import java.util.Set;
import java.util.HashSet;

public class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict);
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];
    }
}
```
**Độ phức tạp:** Time: O(N^3) (với N là độ dài của s do cắt chuỗi substring), Space: O(N)

## 16. Word Break II
**Đề bài chi tiết:** Cho chuỗi `s` và một từ điển `wordDict`, tìm tất cả các cách phân tách `s` thành các từ trong từ điển và trả về danh sách các câu phân cách bởi khoảng trắng.
**Phân tích thuật toán:**
Dùng đệ quy có nhớ (Memoization / DFS + DP). Hàm đệ quy sẽ trả về danh sách các câu hợp lệ được tạo ra từ chuỗi con. Ta lưu lại kết quả cho từng chuỗi con vào một Hash Map để không phải tính lại.
**Mã nguồn Java:**
```java
import java.util.*;

public class Solution {
    private Map<String, List<String>> memo = new HashMap<>();

    public List<String> wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict);
        return dfs(s, wordSet);
    }

    private List<String> dfs(String s, Set<String> wordSet) {
        if (memo.containsKey(s)) return memo.get(s);
        List<String> res = new ArrayList<>();
        if (s.isEmpty()) {
            res.add("");
            return res;
        }
        for (String word : wordSet) {
            if (s.startsWith(word)) {
                List<String> subList = dfs(s.substring(word.length()), wordSet);
                for (String sub : subList) {
                    res.add(word + (sub.isEmpty() ? "" : " ") + sub);
                }
            }
        }
        memo.put(s, res);
        return res;
    }
}
```
**Độ phức tạp:** Time: O(2^N + V) với thời gian có thể là luỹ thừa ở tệ nhất, Space: O(2^N + V)

## 17. Scramble String
**Đề bài chi tiết:** Có thể biểu diễn một chuỗi dưới dạng cây nhị phân bằng cách đệ quy chia chuỗi làm 2 phần không rỗng. Ta có thể tráo đổi 2 cây con của một nút (swap). Cho 2 chuỗi `s1` và `s2`, kiểm tra xem `s2` có phải là chuỗi xáo trộn (scramble string) của `s1` không.
**Phân tích thuật toán:**
Giải bằng đệ quy có nhớ hoặc DP 3 chiều `dp[k][i][j]` (độ dài `k`, bắt đầu tại `i` của `s1`, `j` của `s2`).
Với mỗi cách chia cắt `s1` tại vị trí `i` (từ 1 đến `n-1`), ta kiểm tra 2 trường hợp: không tráo đổi (khớp đoạn đầu với đoạn đầu, đoạn cuối với đoạn cuối) và tráo đổi (khớp đoạn đầu với đoạn cuối và ngược lại).
**Mã nguồn Java:**
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private Map<String, Boolean> memo = new HashMap<>();

    public boolean isScramble(String s1, String s2) {
        if (s1.equals(s2)) return true;
        if (s1.length() != s2.length()) return false;
        
        String key = s1 + "#" + s2;
        if (memo.containsKey(key)) return memo.get(key);
        
        int[] count = new int[26];
        for (int i = 0; i < s1.length(); i++) {
            count[s1.charAt(i) - 'a']++;
            count[s2.charAt(i) - 'a']--;
        }
        for (int c : count) {
            if (c != 0) return false;
        }
        
        int n = s1.length();
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
**Độ phức tạp:** Time: O(N^4), Space: O(N^3)

## 18. Decode Ways
**Đề bài chi tiết:** Một tin nhắn chứa các chữ cái từ 'A'-'Z' được mã hóa thành các số từ 1 đến 26. Cho một chuỗi `s` chứa các chữ số, đếm số cách giải mã (decode) chuỗi.
**Phân tích thuật toán:**
Sử dụng mảng DP 1 chiều `dp[i]` lưu số cách giải mã tiền tố độ dài `i`.
- Nếu `s[i-1] != '0'`, ta có thể dịch nó như 1 ký tự: `dp[i] += dp[i-1]`.
- Nếu số tạo bởi 2 ký tự cuối (từ `s[i-2]` đến `s[i-1]`) nằm trong khoảng `10` đến `26`, ta có thể dịch thành 1 ký tự: `dp[i] += dp[i-2]`.
**Mã nguồn Java:**
```java
public class Solution {
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
**Độ phức tạp:** Time: O(N), Space: O(N)

## 19. Minimum Insertions to Form a Palindrome
**Đề bài chi tiết:** Cho chuỗi `s`, bạn có thể chèn ký tự vào bất kỳ vị trí nào của chuỗi. Tìm số lượng ký tự tối thiểu cần chèn để biến `s` thành chuỗi đối xứng (palindrome).
**Phân tích thuật toán:**
Độ dài của chuỗi đối xứng dài nhất (LPS) tạo thành từ chuỗi `s` chính là `LPS(s)`. Số lượng ký tự tối thiểu cần chèn chính là `n - LPS(s)`. Ta sử dụng DP tương tự bài 11 để tìm chiều dài LPS.
**Mã nguồn Java:**
```java
public class Solution {
    public int minInsertions(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int i = n - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        return n - dp[0][n - 1];
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 20. Palindrome Partitioning II
**Đề bài chi tiết:** Cho chuỗi `s`, phân chia (partition) chuỗi `s` sao cho mỗi phần tử sau khi cắt đều là một chuỗi đối xứng. Tìm số nhát cắt tối thiểu để đạt được điều này.
**Phân tích thuật toán:**
Ta dùng mảng `isPal[i][j]` (được tính bằng DP giống bài 1) để kiểm tra tính đối xứng của chuỗi con `s[i..j]`.
Dùng mảng DP 1 chiều `dp[i]` lưu số nhát cắt tối thiểu cho tiền tố `s[0..i]`.
Khởi tạo `dp[i] = i` (tối đa cần cắt `i` lần). Với mỗi `j <= i`, nếu `isPal[j][i]` là true thì:
- Nếu `j == 0`, `dp[i] = 0` (vì `s[0..i]` là đối xứng).
- Ngược lại `dp[i] = Math.min(dp[i], dp[j - 1] + 1)`.
**Mã nguồn Java:**
```java
public class Solution {
    public int minCut(String s) {
        int n = s.length();
        boolean[][] isPal = new boolean[n][n];
        int[] dp = new int[n];
        
        for (int i = 0; i < n; i++) {
            int min = i;
            for (int j = 0; j <= i; j++) {
                if (s.charAt(j) == s.charAt(i) && (i - j <= 2 || isPal[j + 1][i - 1])) {
                    isPal[j][i] = true;
                    min = j == 0 ? 0 : Math.min(min, dp[j - 1] + 1);
                }
            }
            dp[i] = min;
        }
        return dp[n - 1];
    }
}
```
**Độ phức tạp:** Time: O(N^2), Space: O(N^2)

## 21. Maximum Length of Repeated Subarray
**Đề bài chi tiết:** Cho hai mảng số nguyên `nums1` và `nums2`, trả về chiều dài tối đa của một mảng con (subarray) xuất hiện trong cả hai mảng.
**Phân tích thuật toán:**
Tương tự như Longest Common Substring. Dùng mảng DP 2 chiều `dp[i][j]` lưu chiều dài mảng con chung kết thúc tại `nums1[i-1]` và `nums2[j-1]`.
- Nếu `nums1[i-1] == nums2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1`, và cập nhật `max`.
- Ngược lại: `dp[i][j] = 0`.
**Mã nguồn Java:**
```java
public class Solution {
    public int findLength(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int[][] dp = new int[m + 1][n + 1];
        int maxLen = 0;
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (nums1[i - 1] == nums2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                    maxLen = Math.max(maxLen, dp[i][j]);
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Time: O(M*N), Space: O(M*N)

## 22. Is Subsequence
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t`, kiểm tra xem `s` có phải là dãy con (subsequence) của `t` hay không.
**Phân tích thuật toán:**
Cách tối ưu nhất là dùng 2 con trỏ (Two Pointers) với độ phức tạp O(N). Nếu xét dưới góc độ Quy hoạch động, `dp[i][j]` là độ dài LCS của `s[0..i]` và `t[0..j]`. Trả về true nếu độ dài này bằng độ dài `s`.
Ở đây trình bày thuật toán Two Pointers vì đây là cách tiếp cận phổ biến và hiệu quả nhất cho bài toán này.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean isSubsequence(String s, String t) {
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) {
                i++;
            }
            j++;
        }
        return i == s.length();
    }
}
```
**Độ phức tạp:** Time: O(N), Space: O(1)

## 23. Longest String Chain
**Đề bài chi tiết:** Cho một mảng các chuỗi `words`. Một chuỗi `wordA` là tiền nhiệm của `wordB` nếu ta chèn chính xác 1 ký tự vào `wordA` để được `wordB`. Tìm chuỗi các từ (word chain) dài nhất.
**Phân tích thuật toán:**
Sắp xếp mảng `words` theo độ dài tăng dần. Dùng Hash Map `dp` để lưu độ dài chuỗi dài nhất kết thúc tại mỗi từ.
Với mỗi từ, thử xóa từng ký tự để tạo ra từ tiền nhiệm. Nếu từ tiền nhiệm tồn tại trong `dp`, cập nhật giá trị cho từ hiện tại: `dp.put(word, Math.max(dp.getOrDefault(word, 1), dp.get(prev) + 1))`.
**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int longestStrChain(String[] words) {
        Arrays.sort(words, (a, b) -> a.length() - b.length());
        Map<String, Integer> dp = new HashMap<>();
        int maxChain = 1;
        for (String word : words) {
            int presentLength = 1;
            for (int i = 0; i < word.length(); i++) {
                String prev = word.substring(0, i) + word.substring(i + 1);
                if (dp.containsKey(prev)) {
                    presentLength = Math.max(presentLength, dp.get(prev) + 1);
                }
            }
            dp.put(word, presentLength);
            maxChain = Math.max(maxChain, presentLength);
        }
        return maxChain;
    }
}
```
**Độ phức tạp:** Time: O(N * L^2) (N là số từ, L là độ dài tối đa của từ), Space: O(N)

## 24. Find All Good Strings
**Đề bài chi tiết:** Cho số nguyên `n`, hai chuỗi `s1`, `s2` và một chuỗi `evil`. Tìm số lượng chuỗi độ dài `n` có thứ tự từ điển nằm giữa `s1` và `s2` (bao gồm cả hai) và không chứa `evil` dưới dạng chuỗi con. Trả về kết quả modulo 10^9 + 7.
**Phân tích thuật toán:**
Kết hợp Digit DP (hoặc String DP) và KMP (để chuyển trạng thái khi khớp chuỗi `evil`).
Lập mảng `LPS` của `evil`. Hàm DFS có trạng thái: `(index, evilMatch, isBound1, isBound2)`. Đệ quy đếm số cách điền các ký tự thỏa mãn điều kiện, nếu `evilMatch` đạt đến độ dài `evil` thì số cách = 0.
**Mã nguồn Java:**
```java
public class Solution {
    Integer[][][][] memo;
    int[] lps;
    String evil;
    int mod = 1000000007;

    public int findGoodStrings(int n, String s1, String s2, String evil) {
        this.evil = evil;
        this.lps = computeLPS(evil);
        this.memo = new Integer[n][evil.length()][2][2];
        return dfs(0, 0, true, true, n, s1, s2);
    }

    private int dfs(int i, int evilMatch, boolean isBound1, boolean isBound2, int n, String s1, String s2) {
        if (evilMatch == evil.length()) return 0;
        if (i == n) return 1;
        int b1 = isBound1 ? 1 : 0, b2 = isBound2 ? 1 : 0;
        if (memo[i][evilMatch][b1][b2] != null) return memo[i][evilMatch][b1][b2];
        
        char minChar = isBound1 ? s1.charAt(i) : 'a';
        char maxChar = isBound2 ? s2.charAt(i) : 'z';
        long res = 0;
        
        for (char c = minChar; c <= maxChar; c++) {
            int nextMatch = evilMatch;
            while (nextMatch > 0 && evil.charAt(nextMatch) != c) {
                nextMatch = lps[nextMatch - 1];
            }
            if (evil.charAt(nextMatch) == c) nextMatch++;
            
            res = (res + dfs(i + 1, nextMatch, isBound1 && (c == minChar), 
                             isBound2 && (c == maxChar), n, s1, s2)) % mod;
        }
        return memo[i][evilMatch][b1][b2] = (int) res;
    }

    private int[] computeLPS(String s) {
        int[] lps = new int[s.length()];
        int len = 0, i = 1;
        while (i < s.length()) {
            if (s.charAt(i) == s.charAt(len)) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        return lps;
    }
}
```
**Độ phức tạp:** Time: O(N * |evil| * 26), Space: O(N * |evil|)

## 25. Number of Ways to Form a Target String Given a Dictionary
**Đề bài chi tiết:** Cho một từ điển `words` và một chuỗi `target`. Ta có thể chọn bất kỳ ký tự nào từ cùng một chỉ số cột (index) của bất kỳ từ nào trong `words` để tạo thành `target` từ trái sang phải, nhưng mỗi cột chỉ được dùng nhiều nhất 1 lần (và phải dùng tiến lên theo cột). Đếm số cách tạo ra chuỗi `target`.
**Phân tích thuật toán:**
Bước 1: Đếm tần suất mỗi ký tự ở mỗi cột của `words`.
Bước 2: Sử dụng Quy hoạch động. Gọi `dp[i][j]` là số cách tạo tiền tố `target[0..i]` bằng các cột từ `0..j`.
`dp[i][j] = dp[i][j-1] + dp[i-1][j-1] * freq[target[i]][j]`. Có thể tối ưu bộ nhớ xuống mảng 1 chiều.
**Mã nguồn Java:**
```java
public class Solution {
    public int numWays(String[] words, String target) {
        int mod = 1000000007;
        int m = target.length(), n = words[0].length();
        long[][] freq = new long[26][n];
        for (String w : words) {
            for (int j = 0; j < n; j++) {
                freq[w.charAt(j) - 'a'][j]++;
            }
        }
        long[] dp = new long[m + 1];
        dp[0] = 1;
        for (int j = 0; j < n; j++) {
            for (int i = m; i >= 1; i--) {
                long count = freq[target.charAt(i - 1) - 'a'][j];
                if (count > 0) {
                    dp[i] = (dp[i] + dp[i - 1] * count) % mod;
                }
            }
        }
        return (int) dp[m];
    }
}
```
**Độ phức tạp:** Time: O(L*N + M*N) (L: số lượng từ trong words), Space: O(N + M)

## 26. Longest Valid Parentheses
**Đề bài chi tiết:** Cho một chuỗi chỉ chứa các ký tự `(` và `)`, tìm chiều dài của chuỗi con (substring) các dấu ngoặc hợp lệ dài nhất.
**Phân tích thuật toán:**
Dùng mảng `dp` trong đó `dp[i]` lưu chiều dài chuỗi ngoặc hợp lệ kết thúc tại vị trí `i`.
Nếu `s[i] == ')'`:
- Nếu `s[i-1] == '('`: `dp[i] = dp[i-2] + 2` (nếu `i >= 2`).
- Nếu `s[i-1] == ')'` và `s[i - dp[i-1] - 1] == '('`: chuỗi hợp lệ mới bao trọn chuỗi trước đó, `dp[i] = dp[i-1] + 2 + dp[i - dp[i-1] - 2]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int longestValidParentheses(String s) {
        int maxLen = 0;
        int[] dp = new int[s.length()];
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                maxLen = Math.max(maxLen, dp[i]);
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Time: O(N), Space: O(N)

## 27. Make String a Subsequence Using Cyclic Increments
**Đề bài chi tiết:** Cho 2 chuỗi `str1` và `str2`. Ta có thể chọn một tập các chỉ số trong `str1` và tăng giá trị vòng tròn các ký tự (ví dụ 'z' thành 'a') đúng 1 lần. Kiểm tra xem có thể biến đổi để `str1` chứa `str2` như là một dãy con (subsequence) hay không.
**Phân tích thuật toán:**
Sử dụng phương pháp Hai Con Trỏ (Two Pointers). Với mỗi ký tự ở `str1`, ta kiểm tra xem nó có bằng ký tự đang xét ở `str2` không, hoặc sau khi tăng 1 bước vòng tròn nó có bằng không. Nếu thỏa mãn thì tiến con trỏ của `str2`. Nếu con trỏ của `str2` đi đến cuối thì trả về true.
**Mã nguồn Java:**
```java
public class Solution {
    public boolean canMakeSubsequence(String str1, String str2) {
        int i = 0, j = 0;
        while (i < str1.length() && j < str2.length()) {
            char c1 = str1.charAt(i);
            char c2 = str2.charAt(j);
            if (c1 == c2 || (c1 - 'a' + 1) % 26 + 'a' == c2) {
                j++;
            }
            i++;
        }
        return j == str2.length();
    }
}
```
**Độ phức tạp:** Time: O(N), Space: O(1)

## 28. String Compression II
**Đề bài chi tiết:** Chạy thuật toán mã hóa đoạn dài (run-length encoding, ví dụ aabbaa -> a2b2a2). Bạn được phép xoá nhiều nhất `k` ký tự để độ dài của chuỗi nén là ngắn nhất có thể. Tìm độ dài chuỗi nén tối thiểu đó.
**Phân tích thuật toán:**
Sử dụng mảng DP: `dp[i][k]` là độ dài ngắn nhất của chuỗi nén cho tiền tố `s[0..i-1]` và đã xóa `k` ký tự.
Tại `s[i]`, có thể chọn xóa ký tự (chi phí: `dp[i+1][k-1]`), hoặc giữ nguyên ký tự này, và nhìn ngược lại các ký tự giống nó ở phía trước (với những ký tự khác được coi là xóa đi). Nếu gộp được `same` ký tự giống nhau và xóa `diff` ký tự khác nhau, cập nhật trạng thái với chiều dài đoạn mã hóa mới thêm vào.
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public int getLengthOfOptimalCompression(String s, int k) {
        int n = s.length();
        int[][] dp = new int[n + 1][k + 1];
        for (int[] row : dp) Arrays.fill(row, n);
        dp[0][0] = 0;

        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= k; j++) {
                if (j > 0) dp[i][j] = dp[i - 1][j - 1]; // Xóa ký tự thứ i
                int same = 0, diff = 0;
                for (int l = i; l >= 1; l--) {
                    if (s.charAt(l - 1) == s.charAt(i - 1)) same++;
                    else diff++;
                    if (j >= diff) {
                        int len = same == 1 ? 1 : same < 10 ? 2 : same < 100 ? 3 : 4;
                        dp[i][j] = Math.min(dp[i][j], dp[l - 1][j - diff] + len);
                    }
                }
            }
        }
        return dp[n][k];
    }
}
```
**Độ phức tạp:** Time: O(N^2 * K), Space: O(N * K)

## 29. Form Largest Integer With Digits That Add up to Target
**Đề bài chi tiết:** Cho một mảng `cost` gồm 9 số tương ứng chi phí để viết các chữ số từ 1 đến 9. Tìm số nguyên lớn nhất có thể tạo ra dưới dạng chuỗi với tổng chi phí bằng đúng `target`. Nếu không có cách nào tạo ra đúng `target`, trả về "0".
**Phân tích thuật toán:**
Bài toán tương tự như Balo không giới hạn (Unbounded Knapsack).
Mảng `dp[i]` lưu số chữ số (độ dài) lớn nhất có thể tạo được với chi phí `i`. Để được số lớn nhất, ta ưu tiên tạo số có nhiều chữ số nhất. Nếu chiều dài bằng nhau, số có chữ số lớn (từ 9 xuống 1) ở đầu sẽ lớn hơn.
Sau khi dựng `dp` chiều dài, ta duyệt từ chữ số 9 xuống 1 để tham lam xây chuỗi kết quả (chọn số càng lớn càng tốt miễn là vẫn đi đúng hướng tối ưu độ dài).
**Mã nguồn Java:**
```java
import java.util.Arrays;

public class Solution {
    public String largestNumber(int[] cost, int target) {
        int[] dp = new int[target + 1];
        Arrays.fill(dp, Integer.MIN_VALUE);
        dp[0] = 0;
        for (int i = 1; i <= target; i++) {
            for (int j = 0; j < 9; j++) {
                if (i >= cost[j]) {
                    dp[i] = Math.max(dp[i], dp[i - cost[j]] + 1);
                }
            }
        }
        if (dp[target] < 0) return "0";
        StringBuilder sb = new StringBuilder();
        int curr = target;
        for (int j = 8; j >= 0; j--) {
            while (curr >= cost[j] && dp[curr] == dp[curr - cost[j]] + 1) {
                sb.append(j + 1);
                curr -= cost[j];
            }
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Time: O(Target), Space: O(Target)

## 30. Maximum Number of Non-Overlapping Palindromes
**Đề bài chi tiết:** Cho một chuỗi `s` và một số nguyên `k`. Tìm số lượng tối đa các chuỗi con không giao nhau (non-overlapping) sao cho mỗi chuỗi con đều là một chuỗi đối xứng (palindrome) và có độ dài ít nhất là `k`.
**Phân tích thuật toán:**
Sử dụng Quy hoạch động. Nếu một đoạn có độ dài lớn hơn `k+1` là palindrome, thì lõi của nó cũng chứa một chuỗi palindrome có độ dài `k` hoặc `k+1`. Do đó, ta chỉ cần tìm kiếm và sử dụng các chuỗi đối xứng có độ dài đúng bằng `k` hoặc `k+1`.
Dùng mảng `dp[i]` lưu số lượng chuỗi palindrome tối đa tìm được trong tiền tố `s[0..i]`.
**Mã nguồn Java:**
```java
public class Solution {
    public int maxPalindromes(String s, int k) {
        int n = s.length();
        int[] dp = new int[n + 1];
        for (int i = 0; i < n; i++) {
            dp[i + 1] = Math.max(dp[i + 1], dp[i]);
            
            // Kiểm tra palindrome độ dài k
            if (i + k <= n && isPalindrome(s, i, i + k - 1)) {
                dp[i + k] = Math.max(dp[i + k], dp[i] + 1);
            }
            // Kiểm tra palindrome độ dài k+1
            if (i + k + 1 <= n && isPalindrome(s, i, i + k)) {
                dp[i + k + 1] = Math.max(dp[i + k + 1], dp[i] + 1);
            }
        }
        return dp[n];
    }
    
    private boolean isPalindrome(String s, int l, int r) {
        while (l < r) {
            if (s.charAt(l++) != s.charAt(r--)) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Time: O(N * K), Space: O(N)
