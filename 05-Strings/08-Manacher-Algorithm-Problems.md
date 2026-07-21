# 08 - Manacher's Algorithm Problems

## 1. Longest Palindromic Substring
**Đề bài chi tiết**: Cho một chuỗi `s`, trả về chuỗi con đối xứng (palindromic substring) dài nhất trong `s`.
**Phân tích thuật toán**: Sử dụng thuật toán Manacher để tìm palindrome dài nhất trong thời gian $O(N)$ hoặc thuật toán Expand Around Center. Manacher chèn các ký tự `#` để đồng nhất chiều dài.
**Mã nguồn Java**:
```java
public class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() == 0) return "";
        StringBuilder t = new StringBuilder("#");
        for (char c : s.toCharArray()) { t.append(c).append("#"); }
        int[] p = new int[t.length()];
        int center = 0, right = 0;
        int maxLen = 0, maxCenter = 0;
        for (int i = 0; i < t.length(); i++) {
            int mirror = 2 * center - i;
            if (i < right) p[i] = Math.min(right - i, p[mirror]);
            while (i - 1 - p[i] >= 0 && i + 1 + p[i] < t.length() && t.charAt(i - 1 - p[i]) == t.charAt(i + 1 + p[i])) {
                p[i]++;
            }
            if (i + p[i] > right) {
                center = i;
                right = i + p[i];
            }
            if (p[i] > maxLen) {
                maxLen = p[i];
                maxCenter = i;
            }
        }
        int start = (maxCenter - maxLen) / 2;
        return s.substring(start, start + maxLen);
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 2. Palindromic Substrings (Count all)
**Đề bài chi tiết**: Cho chuỗi `s`, đếm tổng số chuỗi con đối xứng có trong `s`.
**Phân tích thuật toán**: Mảng `P` trong thuật toán Manacher lưu trữ bán kính của palindrome tại mỗi tâm. Số lượng palindrome tại tâm `i` chính là `(P[i] + 1) / 2`.
**Mã nguồn Java**:
```java
public class Solution {
    public int countSubstrings(String s) {
        StringBuilder t = new StringBuilder("#");
        for (char c : s.toCharArray()) t.append(c).append("#");
        int[] p = new int[t.length()];
        int center = 0, right = 0, count = 0;
        for (int i = 0; i < t.length(); i++) {
            int mirror = 2 * center - i;
            if (i < right) p[i] = Math.min(right - i, p[mirror]);
            while (i - 1 - p[i] >= 0 && i + 1 + p[i] < t.length() && t.charAt(i - 1 - p[i]) == t.charAt(i + 1 + p[i])) {
                p[i]++;
            }
            if (i + p[i] > right) {
                center = i;
                right = i + p[i];
            }
            count += (p[i] + 1) / 2;
        }
        return count;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 3. Shortest Palindrome
**Đề bài chi tiết**: Bạn được cho một chuỗi `s`. Bạn có thể thêm các ký tự vào đầu chuỗi để tạo thành một palindrome. Tìm palindrome ngắn nhất có thể tạo ra.
**Phân tích thuật toán**: Tìm chuỗi con đối xứng dài nhất bắt đầu từ vị trí 0. Có thể dùng KMP hoặc Manacher. Với Manacher, ta tìm `i` sao cho biên trái của palindrome là `0` (`i - p[i] == 0`) và có `p[i]` lớn nhất.
**Mã nguồn Java**:
```java
public class Solution {
    public String shortestPalindrome(String s) {
        if(s.isEmpty()) return "";
        StringBuilder t = new StringBuilder("#");
        for(char c: s.toCharArray()) t.append(c).append("#");
        int[] p = new int[t.length()];
        int c = 0, r = 0, maxLen = 0;
        for(int i = 0; i < t.length(); i++){
            int mirror = 2 * c - i;
            if(i < r) p[i] = Math.min(r - i, p[mirror]);
            while(i - 1 - p[i] >= 0 && i + 1 + p[i] < t.length() && t.charAt(i - 1 - p[i]) == t.charAt(i + 1 + p[i])) {
                p[i]++;
            }
            if(i + p[i] > r){ c = i; r = i + p[i]; }
            if(i - p[i] == 0) maxLen = Math.max(maxLen, p[i]);
        }
        String suffix = s.substring(maxLen);
        return new StringBuilder(suffix).reverse().toString() + s;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 4. Maximum Number of Non-overlapping Palindrome Substrings
**Đề bài chi tiết**: Tìm số lượng tối đa các chuỗi con đối xứng không giao nhau trong chuỗi `s`, với điều kiện độ dài mỗi palindrome >= k.
**Phân tích thuật toán**: Kết hợp Manacher để kiểm tra nhanh một đoạn có phải palindrome không, kết hợp với Dynamic Programming. Hoặc dùng thuật toán tham lam tìm đoạn ngắn nhất >= k.
**Mã nguồn Java**:
```java
public class Solution {
    public int maxPalindromes(String s, int k) {
        int n = s.length();
        boolean[][] isPal = new boolean[n][n];
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    isPal[i][j] = (len <= 2) || isPal[i + 1][j - 1];
                }
            }
        }
        int[] dp = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            dp[i] = dp[i - 1];
            for (int j = i - k; j >= 0; j--) {
                if (isPal[j][i - 1]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[n];
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N^2)$. (Tối ưu dùng Manacher đạt $O(N \log N)$ hoặc $O(N)$).

## 5. Longest Palindrome
**Đề bài chi tiết**: Cho một chuỗi `s`, tạo chuỗi palindrome dài nhất có thể bằng cách sắp xếp lại các ký tự trong `s`.
**Phân tích thuật toán**: Dùng mảng tần số để đếm số lần xuất hiện của mỗi ký tự. Các ký tự xuất hiện chẵn lần được lấy toàn bộ. Các ký tự lẻ lấy phần chẵn của nó và có thể giữ lại 1 ký tự lẻ ở chính giữa.
**Mã nguồn Java**:
```java
public class Solution {
    public int longestPalindrome(String s) {
        int[] count = new int[128];
        for (char c : s.toCharArray()) {
            count[c]++;
        }
        int ans = 0;
        for (int v : count) {
            ans += v / 2 * 2;
            if (ans % 2 == 0 && v % 2 == 1) {
                ans++;
            }
        }
        return ans;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 6. Valid Palindrome III
**Đề bài chi tiết**: Cho một chuỗi `s` và một số nguyên `k`. Kiểm tra xem `s` có phải là K-Palindrome hay không (có thể biến thành palindrome bằng cách xóa tối đa k ký tự).
**Phân tích thuật toán**: Bài toán chuyển về tìm Longest Palindromic Subsequence (LPS). Nếu độ dài của `s` trừ đi LPS <= k thì trả về true. Dùng DP mảng 1D để tối ưu.
**Mã nguồn Java**:
```java
public class Solution {
    public boolean isValidPalindrome(String s, int k) {
        int n = s.length();
        int[] dp = new int[n];
        int[] prev = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = 1;
            for (int j = i + 1; j < n; j++) {
                int temp = dp[j];
                if (s.charAt(i) == s.charAt(j)) {
                    dp[j] = 2 + prev[j - 1];
                } else {
                    dp[j] = Math.max(dp[j], dp[j - 1]);
                }
                prev[j] = temp;
            }
        }
        return n - dp[n - 1] <= k;
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N)$.

## 7. Break a Palindrome
**Đề bài chi tiết**: Cho một chuỗi `palindrome`, thay đổi đúng 1 ký tự sao cho nó không còn là palindrome và có thứ tự từ điển nhỏ nhất có thể.
**Phân tích thuật toán**: Duyệt qua nửa đầu của chuỗi, thay ký tự đầu tiên khác 'a' thành 'a'. Nếu duyệt hết mà chưa thay (ví dụ toàn 'a'), thay ký tự cuối cùng thành 'b'. Chú ý xử lý trường hợp chuỗi độ dài 1 (trả về rỗng).
**Mã nguồn Java**:
```java
public class Solution {
    public String breakPalindrome(String palindrome) {
        int len = palindrome.length();
        if (len == 1) return "";
        char[] arr = palindrome.toCharArray();
        for (int i = 0; i < len / 2; i++) {
            if (arr[i] != 'a') {
                arr[i] = 'a';
                return new String(arr);
            }
        }
        arr[len - 1] = 'b';
        return new String(arr);
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 8. Construct K Palindrome Strings
**Đề bài chi tiết**: Cho chuỗi `s` và số `k`, kiểm tra xem có thể dùng tất cả các ký tự trong `s` để tạo ra đúng `k` chuỗi palindrome không.
**Phân tích thuật toán**: Số lượng chuỗi palindrome tối thiểu cần tạo chính là số lượng ký tự có tần số lẻ. Nếu số ký tự tần số lẻ > k, trả về false. Nếu k > `s.length()`, trả về false. Còn lại là true.
**Mã nguồn Java**:
```java
public class Solution {
    public boolean canConstruct(String s, int k) {
        if (s.length() < k) return false;
        int[] counts = new int[26];
        for (char c : s.toCharArray()) {
            counts[c - 'a']++;
        }
        int oddCount = 0;
        for (int count : counts) {
            if (count % 2 != 0) oddCount++;
        }
        return oddCount <= k;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 9. Split Two Strings to Make Palindrome
**Đề bài chi tiết**: Cho hai chuỗi `a` và `b` cùng độ dài. Chọn một chỉ số và cắt `a` thành `a_prefix, a_suffix`, `b` thành `b_prefix, b_suffix`. Kiểm tra xem `a_prefix + b_suffix` hoặc `b_prefix + a_suffix` có thể tạo thành palindrome không.
**Phân tích thuật toán**: Hàm kiểm tra xem có thể ghép `a` và `b`. So sánh `a[i]` và `b[n-1-i]` từ ngoài vào trong cho đến khi khác nhau. Phần còn lại phải là palindrome trong chính `a` hoặc `b`.
**Mã nguồn Java**:
```java
public class Solution {
    public boolean checkPalindromeFormation(String a, String b) {
        return check(a, b) || check(b, a);
    }
    
    private boolean check(String a, String b) {
        int i = 0, j = a.length() - 1;
        while (i < j && a.charAt(i) == b.charAt(j)) {
            i++; j--;
        }
        return isPalindrome(a, i, j) || isPalindrome(b, i, j);
    }
    
    private boolean isPalindrome(String s, int i, int j) {
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) return false;
            i++; j--;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 10. Count Different Palindromic Subsequences
**Đề bài chi tiết**: Cho một chuỗi `s`, tìm số lượng các chuỗi con đối xứng (palindromic subsequences) phân biệt. Kết quả có thể rất lớn nên trả về modulo $10^9 + 7$.
**Phân tích thuật toán**: Sử dụng Quy hoạch động DP 2D. `dp[i][j]` là số subsequences phân biệt trong khoảng `[i, j]`. Cần theo dõi sự xuất hiện của các ký tự để tránh đếm trùng (trừ đi phần đã tính khi ký tự lặp lại).
**Mã nguồn Java**:
```java
public class Solution {
    public int countPalindromicSubsequences(String s) {
        int mod = 1_000_000_007;
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) dp[i][i] = 1;
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    int low = i + 1, high = j - 1;
                    while (low <= high && s.charAt(low) != s.charAt(i)) low++;
                    while (low <= high && s.charAt(high) != s.charAt(i)) high--;
                    if (low > high) {
                        dp[i][j] = dp[i + 1][j - 1] * 2 + 2;
                    } else if (low == high) {
                        dp[i][j] = dp[i + 1][j - 1] * 2 + 1;
                    } else {
                        dp[i][j] = dp[i + 1][j - 1] * 2 - dp[low + 1][high - 1];
                    }
                } else {
                    dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];
                }
                dp[i][j] = dp[i][j] < 0 ? dp[i][j] + mod : dp[i][j] % mod;
            }
        }
        return dp[0][n - 1];
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N^2)$.

## 11. Palindrome Partitioning
**Đề bài chi tiết**: Cho một chuỗi `s`, phân chia `s` sao cho mọi chuỗi con trong cách phân chia đều là một palindrome. Trả về tất cả các cách phân chia có thể.
**Phân tích thuật toán**: Sử dụng quay lui (Backtracking) để thử cắt chuỗi. Để kiểm tra palindrome nhanh, có thể tính trước một mảng boolean 2D `dp` bằng quy hoạch động.
**Mã nguồn Java**:
```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = (len <= 2) || dp[i + 1][j - 1];
                }
            }
        }
        backtrack(s, 0, new ArrayList<>(), res, dp);
        return res;
    }
    
    private void backtrack(String s, int start, List<String> path, List<List<String>> res, boolean[][] dp) {
        if (start == s.length()) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (dp[start][i]) {
                path.add(s.substring(start, i + 1));
                backtrack(s, i + 1, path, res, dp);
                path.remove(path.size() - 1);
            }
        }
    }
}
```
**Độ phức tạp**: Thời gian $O(N \cdot 2^N)$, Không gian $O(N^2)$.

## 12. Palindrome Partitioning II
**Đề bài chi tiết**: Cho một chuỗi `s`, phân chia `s` sao cho mọi chuỗi con đều là palindrome. Trả về số lần cắt tối thiểu cần thiết để hoàn thành việc phân chia này.
**Phân tích thuật toán**: Sử dụng Quy hoạch động (Dynamic Programming). Gọi `dp[i]` là số lần cắt tối thiểu cho chuỗi con `s[0..i]`. Duyệt $j$ từ $0$ đến $i$, nếu đoạn `s[j..i]` là palindrome thì cập nhật `dp[i] = min(dp[i], dp[j-1] + 1)`.
**Mã nguồn Java**:
```java
class Solution {
    public int minCut(String s) {
        int n = s.length();
        if (n == 0) return 0;
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
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N^2)$.

## 13. Palindrome Partitioning III
**Đề bài chi tiết**: Cho một chuỗi `s` và số nguyên `k`. Bạn có thể đổi bất kỳ ký tự nào của `s`. Chia chuỗi thành `k` đoạn rời rạc sao cho mỗi đoạn đều là palindrome. Trả về số ký tự tối thiểu cần thay đổi.
**Phân tích thuật toán**: Dùng DP 2 chiều. Trước hết tính `cost[i][j]` là chi phí đổi đoạn `s[i..j]` thành palindrome. Sau đó dùng mảng `dp[i][k]` lưu chi phí nhỏ nhất chia đoạn `s[0..i]` thành `k` phần. `dp[i][k] = min(dp[j][k-1] + cost[j+1][i])`.
**Mã nguồn Java**:
```java
class Solution {
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
        for (int[] row : dp) java.util.Arrays.fill(row, 10000);
        for (int i = 0; i < n; i++) {
            dp[i][1] = cost[0][i];
            for (int part = 2; part <= k; part++) {
                for (int j = 0; j < i; j++) {
                    dp[i][part] = Math.min(dp[i][part], dp[j][part - 1] + cost[j + 1][i]);
                }
            }
        }
        return dp[n - 1][k];
    }
}
```
**Độ phức tạp**: Thời gian $O(N^3)$, Không gian $O(N^2)$.

## 14. Palindrome Partitioning IV
**Đề bài chi tiết**: Cho chuỗi `s`, trả về `true` nếu có thể chia chuỗi thành đúng 3 chuỗi con đối xứng không rỗng.
**Phân tích thuật toán**: Tính mảng boolean 2D `isPal[i][j]` để lưu trạng thái palindrome. Sau đó sử dụng vòng lặp duyệt mọi điểm chia làm 3 phần `s[0..i-1]`, `s[i..j]`, và `s[j+1..n-1]`.
**Mã nguồn Java**:
```java
class Solution {
    public boolean checkPartitioning(String s) {
        int n = s.length();
        boolean[][] isPal = new boolean[n][n];
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    isPal[i][j] = (len <= 2) || isPal[i + 1][j - 1];
                }
            }
        }
        for (int i = 1; i < n - 1; i++) {
            for (int j = i; j < n - 1; j++) {
                if (isPal[0][i - 1] && isPal[i][j] && isPal[j + 1][n - 1]) {
                    return true;
                }
            }
        }
        return false;
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N^2)$.

## 15. Longest Palindromic Subsequence
**Đề bài chi tiết**: Cho một chuỗi `s`, tìm độ dài chuỗi con đối xứng (không nhất thiết liên tiếp) dài nhất trong `s`.
**Phân tích thuật toán**: Dùng mảng DP 2D, `dp[i][j]` là độ dài LPS của đoạn `s[i..j]`. Nếu `s[i] == s[j]`, độ dài tăng thêm 2 so với `dp[i+1][j-1]`. Ngược lại lấy max của `dp[i+1][j]` và `dp[i][j-1]`.
**Mã nguồn Java**:
```java
class Solution {
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
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N^2)$.

## 16. Palindrome Linked List
**Đề bài chi tiết**: Cho node head của một danh sách liên kết đơn, kiểm tra xem nó có đối xứng hay không.
**Phân tích thuật toán**: Dùng thuật toán thỏ và rùa (slow/fast pointers) để tìm điểm giữa. Sau đó, lật ngược nửa sau của danh sách và so sánh với nửa đầu.
**Mã nguồn Java**:
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode prev = null, curr = slow;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        ListNode p1 = head, p2 = prev;
        while (p2 != null) {
            if (p1.val != p2.val) return false;
            p1 = p1.next;
            p2 = p2.next;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 17. Valid Palindrome
**Đề bài chi tiết**: Một chuỗi được gọi là palindrome nếu sau khi chuyển tất cả chữ hoa thành chữ thường và xóa hết các ký tự không phải là chữ hoặc số, chuỗi đọc từ trái sang phải giống từ phải sang trái.
**Phân tích thuật toán**: Sử dụng hai con trỏ `left` và `right` từ hai đầu. Bỏ qua các ký tự không hợp lệ bằng cách dịch chuyển hai con trỏ, so sánh các ký tự hợp lệ ở hai bên.
**Mã nguồn Java**:
```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                left++;
            }
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                right--;
            }
            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 18. Valid Palindrome II
**Đề bài chi tiết**: Cho một chuỗi `s`, trả về `true` nếu nó có thể trở thành palindrome sau khi xóa nhiều nhất một ký tự.
**Phân tích thuật toán**: Dùng hai con trỏ. Khi gặp `s[left] != s[right]`, ta có thể thử bỏ qua `s[left]` (kiểm tra `s[left+1..right]`) hoặc bỏ qua `s[right]` (kiểm tra `s[left..right-1]`).
**Mã nguồn Java**:
```java
class Solution {
    public boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return isPal(s, left + 1, right) || isPal(s, left, right - 1);
            }
            left++; right--;
        }
        return true;
    }
    
    private boolean isPal(String s, int i, int j) {
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) return false;
            i++; j--;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(1)$.

## 19. Prime Palindrome
**Đề bài chi tiết**: Tìm số nguyên tố đối xứng nhỏ nhất lớn hơn hoặc bằng `n`.
**Phân tích thuật toán**: Các số đối xứng có số lượng chữ số chẵn đều chia hết cho 11. Trừ số 11, chúng không thể là số nguyên tố. Sinh dần các số đối xứng từ việc lật nửa trái của chúng (độ dài 1 đến 5 chữ số) và kiểm tra tính nguyên tố.
**Mã nguồn Java**:
```java
class Solution {
    public int primePalindrome(int n) {
        if (n <= 2) return 2;
        if (n >= 8 && n <= 11) return 11;
        for (int len = 1; len <= 5; len++) {
            int start = (int) Math.pow(10, len - 1);
            int end = (int) Math.pow(10, len);
            for (int i = start; i < end; i++) {
                String s = Integer.toString(i);
                StringBuilder sb = new StringBuilder(s);
                int pal = Integer.parseInt(s + sb.reverse().substring(1));
                if (pal >= n && isPrime(pal)) {
                    return pal;
                }
            }
        }
        return -1;
    }
    
    private boolean isPrime(int n) {
        if (n < 2) return false;
        if (n % 2 == 0) return n == 2;
        for (int i = 3; i * i <= n; i += 2) {
            if (n % i == 0) return false;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(\text{số chuỗi đối xứng} \times \sqrt{X})$, Không gian $O(1)$ bỏ qua chuỗi.

## 20. Super Palindromes
**Đề bài chi tiết**: Tìm số lượng "siêu palindrome" (super palindromes) trong đoạn `[left, right]`. "Siêu palindrome" là một palindrome mà căn bậc hai của nó cũng là một palindrome. `left` và `right` có thể lên tới $10^{18}$.
**Phân tích thuật toán**: Căn bậc hai của $10^{18}$ là $10^9$. Thay vì duyệt số siêu palindrome, sinh tất cả các palindrome $R$ lên đến $10^9$ (bằng cách sinh một nửa của $R$ đến $10^5$), sau đó kiểm tra nếu $V = R^2$ nằm trong đoạn đã cho và $V$ cũng là palindrome thì tăng biến đếm.
**Mã nguồn Java**:
```java
class Solution {
    public int superpalindromesInRange(String left, String right) {
        long l = Long.parseLong(left);
        long r = Long.parseLong(right);
        int limit = 100000;
        int count = 0;
        
        for (int k = 1; k < limit; k++) {
            String s = Integer.toString(k);
            String sb = new StringBuilder(s).reverse().toString();
            long R = Long.parseLong(s + sb.substring(1));
            long V = R * R;
            if (V > r) break;
            if (V >= l && isPal(V)) count++;
        }
        
        for (int k = 1; k < limit; k++) {
            String s = Integer.toString(k);
            String sb = new StringBuilder(s).reverse().toString();
            long R = Long.parseLong(s + sb);
            long V = R * R;
            if (V > r) break;
            if (V >= l && isPal(V)) count++;
        }
        return count;
    }
    
    private boolean isPal(long x) {
        long p = 0, temp = x;
        while (temp > 0) {
            p = p * 10 + temp % 10;
            temp /= 10;
        }
        return p == x;
    }
}
```
**Độ phức tạp**: Thời gian $O(W^\frac{1}{4} \log_{10} W)$ với $W = 10^{18}$, Không gian $O(\log_{10} W)$ cho biến đổi chuỗi.

## 21. Find First Palindromic String in the Array
**Đề bài chi tiết**: Cho một mảng các chuỗi `words`, trả về chuỗi đầu tiên trong mảng là một palindrome. Nếu không có chuỗi nào, trả về chuỗi rỗng `""`.
**Phân tích thuật toán**: Duyệt qua từng chuỗi trong mảng `words`. Viết một hàm kiểm tra palindrome đơn giản bằng cách dùng hai con trỏ `left` và `right`. Chuỗi đầu tiên thỏa mãn sẽ được trả về ngay lập tức.
**Mã nguồn Java**:
```java
class Solution {
    public String firstPalindrome(String[] words) {
        for (String word : words) {
            if (isPalindrome(word)) {
                return word;
            }
        }
        return "";
    }
    
    private boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \cdot M)$ với $N$ là số chuỗi và $M$ là độ dài tối đa của chuỗi. Không gian $O(1)$.

## 22. Maximum Product of the Length of Two Palindromic Substrings
**Đề bài chi tiết**: Cho một chuỗi `s`, tìm tích lớn nhất của độ dài hai chuỗi con đối xứng có độ dài lẻ và không giao nhau.
**Phân tích thuật toán**: Sử dụng thuật toán Manacher để tìm bán kính lớn nhất của các chuỗi con đối xứng lẻ tại mỗi tâm. Mảng `left[i]` lưu trữ độ dài palindrome lẻ dài nhất kết thúc tại hoặc trước `i`. Mảng `right[i]` lưu trữ độ dài palindrome lẻ dài nhất bắt đầu tại hoặc sau `i`. Kết quả là `max(left[i] * right[i+1])`.
**Mã nguồn Java**:
```java
class Solution {
    public long maxProduct(String s) {
        int n = s.length();
        int[] manacher = new int[n];
        int center = 0, right = 0;
        for (int i = 0; i < n; i++) {
            if (i < right) manacher[i] = Math.min(right - i, manacher[2 * center - i]);
            while (i - manacher[i] - 1 >= 0 && i + manacher[i] + 1 < n && s.charAt(i - manacher[i] - 1) == s.charAt(i + manacher[i] + 1)) {
                manacher[i]++;
            }
            if (i + manacher[i] > right) {
                center = i;
                right = i + manacher[i];
            }
        }
        
        long[] l = new long[n];
        int maxLen = 1;
        for (int i = 0, j = 0; i < n; i++) {
            while (j + manacher[j] < i) j++;
            maxLen = Math.max(maxLen, 2 * (i - j) + 1);
            l[i] = maxLen;
        }
        
        long[] r = new long[n];
        maxLen = 1;
        for (int i = n - 1, j = n - 1; i >= 0; i--) {
            while (j - manacher[j] > i) j--;
            maxLen = Math.max(maxLen, 2 * (j - i) + 1);
            r[i] = maxLen;
        }
        
        long ans = 0;
        for (int i = 0; i < n - 1; i++) {
            ans = Math.max(ans, l[i] * r[i + 1]);
        }
        return ans;
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 23. Palindrome Pairs
**Đề bài chi tiết**: Cho một mảng các chuỗi phân biệt `words`, tìm tất cả các cặp chỉ số `(i, j)` sao cho phần ghép `words[i] + words[j]` là một palindrome.
**Phân tích thuật toán**: Lưu tất cả các từ cùng với chỉ số của chúng vào HashMap. Duyệt qua mỗi từ `word`, thử chia nó thành hai phần `left` và `right`. Nếu `left` là palindrome, tìm phần đảo ngược của `right` trong HashMap. Nếu `right` là palindrome, tìm phần đảo ngược của `left` trong HashMap. Cần cẩn thận xử lý chuỗi rỗng để tránh bị trùng lắp kết quả.
**Mã nguồn Java**:
```java
class Solution {
    public List<List<Integer>> palindromePairs(String[] words) {
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0; i < words.length; i++) {
            map.put(new StringBuilder(words[i]).reverse().toString(), i);
        }
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            int n = word.length();
            if (n == 0) continue;
            for (int j = 0; j <= n; j++) {
                if (isPal(word, 0, j - 1)) {
                    String right = word.substring(j);
                    if (map.containsKey(right) && map.get(right) != i) {
                        res.add(Arrays.asList(map.get(right), i));
                    }
                }
                if (j != n && isPal(word, j, n - 1)) {
                    String left = word.substring(0, j);
                    if (map.containsKey(left) && map.get(left) != i) {
                        res.add(Arrays.asList(i, map.get(left)));
                    }
                }
            }
        }
        return res;
    }

    private boolean isPal(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
}
```
**Độ phức tạp**: Thời gian $O(N \cdot K^2)$ với $N$ là số từ, $K$ là độ dài từ. Không gian $O(N \cdot K)$.

## 24. Can Make Palindrome from Substring
**Đề bài chi tiết**: Cho chuỗi `s` và mảng các truy vấn, mỗi truy vấn `(left, right, k)` hỏi xem chuỗi con `s[left...right]` có thể biến thành palindrome bằng cách thay đổi tối đa `k` ký tự sau khi tùy ý sắp xếp lại không.
**Phân tích thuật toán**: Vì ta có thể sắp xếp lại chuỗi con, ta chỉ cần quan tâm số lượng ký tự có tần số lẻ. Sử dụng mảng cộng dồn trạng thái bit (prefix xor array) cho 26 ký tự để truy xuất nhanh tần số chẵn/lẻ của mỗi ký tự trong bất kỳ khoảng nào $O(1)$. Số cặp ký tự lẻ cần được thay đổi là `odd_count / 2`. Nếu `odd_count / 2 <= k`, thì trả về `true`.
**Mã nguồn Java**:
```java
class Solution {
    public List<Boolean> canMakePaliQueries(String s, int[][] queries) {
        int n = s.length();
        int[] prefix = new int[n + 1];
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] ^ (1 << (s.charAt(i) - 'a'));
        }
        List<Boolean> ans = new ArrayList<>();
        for (int[] q : queries) {
            int mask = prefix[q[1] + 1] ^ prefix[q[0]];
            int oddCount = Integer.bitCount(mask);
            ans.add(oddCount / 2 <= q[2]);
        }
        return ans;
    }
}
```
**Độ phức tạp**: Thời gian $O(N + Q)$, Không gian $O(N)$.

## 25. Minimum Number of Moves to Make Palindrome
**Đề bài chi tiết**: Cho chuỗi `s`, tìm số lần đổi chỗ 2 ký tự kề nhau tối thiểu để biến `s` thành một palindrome. Đảm bảo rằng luôn có thể thực hiện được.
**Phân tích thuật toán**: Dùng thuật toán tham lam hai con trỏ `left` và `right`. Nếu `s[left] == s[right]`, thu hẹp `left` và `right`. Nếu khác, ta tìm một ký tự giống `s[left]` từ vị trí `right` đi ngược về phía `left`. Khi tìm thấy, dùng các phép đổi chỗ liền kề để đưa nó về `right`. Nếu ký tự tại `s[left]` chỉ xuất hiện 1 lần (tâm của chuỗi lẻ), ta đổi chỗ nó vào chính giữa.
**Mã nguồn Java**:
```java
class Solution {
    public int minMovesToMakePalindrome(String s) {
        int moves = 0;
        char[] arr = s.toCharArray();
        int left = 0, right = arr.length - 1;
        while (left < right) {
            if (arr[left] == arr[right]) {
                left++; right--;
                continue;
            }
            int k = right;
            while (k > left && arr[k] != arr[left]) {
                k--;
            }
            if (k == left) {
                // Char at left is the center character
                char temp = arr[left];
                arr[left] = arr[left + 1];
                arr[left + 1] = temp;
                moves++;
            } else {
                while (k < right) {
                    char temp = arr[k];
                    arr[k] = arr[k + 1];
                    arr[k + 1] = temp;
                    k++;
                    moves++;
                }
                left++; right--;
            }
        }
        return moves;
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N)$.

## 26. Count Palindromic Subsequences
**Đề bài chi tiết**: Cho chuỗi `s` chỉ chứa các chữ số, đếm số lượng chuỗi con đối xứng có độ dài đúng 5. Kết quả trả về theo modulo $10^9 + 7$.
**Phân tích thuật toán**: Một palindrome độ dài 5 có dạng `AB_BA`. Với mỗi vị trí ở giữa `i` (từ $2$ đến $N-3$), ta đếm số cách chọn `AB` ở bên trái của `i` và `BA` ở bên phải của `i`. Có $10 \times 10 = 100$ cặp `AB` (từ `00` đến `99`). Ta có thể dùng mảng cộng dồn để đếm số cặp chữ số ở 2 phía và tính tổng.
**Mã nguồn Java**:
```java
class Solution {
    public int countPalindromes(String s) {
        int n = s.length();
        int mod = 1_000_000_007;
        if (n < 5) return 0;
        
        long[][][] prefix = new long[n][10][10];
        long[] countL = new long[10];
        for (int i = 0; i < n; i++) {
            if (i > 0) {
                for (int j = 0; j < 10; j++) {
                    for (int k = 0; k < 10; k++) prefix[i][j][k] = prefix[i - 1][j][k];
                }
            }
            int d = s.charAt(i) - '0';
            if (i > 0) {
                for (int j = 0; j < 10; j++) prefix[i][j][d] += countL[j];
            }
            countL[d]++;
        }
        
        long[][][] suffix = new long[n][10][10];
        long[] countR = new long[10];
        for (int i = n - 1; i >= 0; i--) {
            if (i < n - 1) {
                for (int j = 0; j < 10; j++) {
                    for (int k = 0; k < 10; k++) suffix[i][j][k] = suffix[i + 1][j][k];
                }
            }
            int d = s.charAt(i) - '0';
            if (i < n - 1) {
                for (int j = 0; j < 10; j++) suffix[i][d][j] += countR[j];
            }
            countR[d]++;
        }
        
        long ans = 0;
        for (int i = 2; i < n - 2; i++) {
            for (int j = 0; j < 10; j++) {
                for (int k = 0; k < 10; k++) {
                    ans = (ans + prefix[i - 1][j][k] * suffix[i + 1][k][j]) % mod;
                }
            }
        }
        return (int) ans;
    }
}
```
**Độ phức tạp**: Thời gian $O(100 \cdot N)$, Không gian $O(100 \cdot N)$.

## 27. Longest Chunked Palindrome Decomposition
**Đề bài chi tiết**: Cho một chuỗi `s`, trả về số lượng nhiều nhất các phần (chunks) được chia sao cho `s` = $P_1 + P_2 + ... + P_k$ và $P_i = P_{k-i+1}$ (các khối đối xứng).
**Phân tích thuật toán**: Dùng thuật toán tham lam hai con trỏ hoặc duyệt từ ngoài vào. Cố gắng tìm chuỗi con ngắn nhất ở hai đầu giống nhau. Ngay khi tìm thấy, ta bóc chúng ra (được thêm 2 vào kết quả) và tiếp tục đệ quy cho phần còn lại. Dùng Rolling Hash có thể giúp tìm chuỗi nhanh, tuy nhiên vì $N \le 1000$, so sánh chuỗi trực tiếp cũng đủ nhanh.
**Mã nguồn Java**:
```java
class Solution {
    public int longestDecomposition(String text) {
        int n = text.length();
        for (int i = 1; i <= n / 2; i++) {
            if (text.substring(0, i).equals(text.substring(n - i))) {
                return 2 + longestDecomposition(text.substring(i, n - i));
            }
        }
        return n == 0 ? 0 : 1;
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$ ở trường hợp tệ nhất, $O(N)$ nếu có Rolling Hash. Không gian $O(N)$ do dùng substring.

## 28. Largest Palindromic Number
**Đề bài chi tiết**: Cho một chuỗi ký tự số `num`, tạo ra chuỗi số nguyên lớn nhất có thể (chứa các số là các ký tự đã cho) và là một palindrome. Không được chứa số $0$ ở đầu trừ khi đó là số $0$ duy nhất.
**Phân tích thuật toán**: Đếm tần số xuất hiện của các chữ số từ 9 đến 0. Sử dụng phần chia hết cho 2 để xếp 2 bên, và có thể dành ra 1 chữ số xuất hiện lẻ lớn nhất để đặt ở tâm. Đặc biệt, nếu chữ số lớn nhất có thể được xếp hai bên là $0$, thì ta không được bỏ nó ra 2 bên, mà chỉ có thể để ở tâm.
**Mã nguồn Java**:
```java
class Solution {
    public String largestPalindromic(String num) {
        int[] counts = new int[10];
        for (char c : num.toCharArray()) {
            counts[c - '0']++;
        }
        StringBuilder half = new StringBuilder();
        for (int i = 9; i >= 0; i--) {
            if (i == 0 && half.length() == 0) continue;
            int pairs = counts[i] / 2;
            for (int k = 0; k < pairs; k++) half.append(i);
        }
        String center = "";
        for (int i = 9; i >= 0; i--) {
            if (counts[i] % 2 != 0) {
                center = String.valueOf(i);
                break;
            }
        }
        if (half.length() == 0 && center.isEmpty()) return "0";
        StringBuilder res = new StringBuilder();
        res.append(half);
        res.append(center);
        res.append(half.reverse());
        return res.toString();
    }
}
```
**Độ phức tạp**: Thời gian $O(N)$, Không gian $O(N)$.

## 29. Find the Closest Palindrome
**Đề bài chi tiết**: Cho một chuỗi `n` đại diện cho một số nguyên, trả về số palindrome gần nhất (không tính chính nó). Nếu có sự hòa nhau, trả về số nhỏ hơn.
**Phân tích thuật toán**: Có các ứng viên có thể xảy ra:
1. Giữ nguyên nửa đầu (rồi phản chiếu), 
2. Nửa đầu cộng 1 (rồi phản chiếu),
3. Nửa đầu trừ 1 (rồi phản chiếu),
4. Số dạng $10^k + 1$ (khi bị giảm số chữ số, ví dụ 100 -> 99).
5. Số dạng $10^k - 1$ (khi bị tăng số chữ số, ví dụ 99 -> 101).
Tạo tất cả các ứng viên, so sánh độ lệch với `n` để lấy kết quả nhỏ nhất (có trị số khoảng cách nhỏ nhất, rồi đến giá trị ứng viên nhỏ nhất).
**Mã nguồn Java**:
```java
class Solution {
    public String nearestPalindromic(String n) {
        int len = n.length();
        long num = Long.parseLong(n);
        List<Long> candidates = new ArrayList<>();
        candidates.add((long) Math.pow(10, len - 1) - 1);
        candidates.add((long) Math.pow(10, len) + 1);
        
        long prefix = Long.parseLong(n.substring(0, (len + 1) / 2));
        for (long i = -1; i <= 1; i++) {
            String p = String.valueOf(prefix + i);
            String candidate = p + new StringBuilder(p.substring(0, len / 2)).reverse().toString();
            candidates.add(Long.parseLong(candidate));
        }
        
        long closest = -1;
        for (long cand : candidates) {
            if (cand == num) continue;
            if (closest == -1 || 
                Math.abs(cand - num) < Math.abs(closest - num) || 
                (Math.abs(cand - num) == Math.abs(closest - num) && cand < closest)) {
                closest = cand;
            }
        }
        return String.valueOf(closest);
    }
}
```
**Độ phức tạp**: Thời gian $O(\text{len})$, Không gian $O(\text{len})$.

## 30. Make a Palindrome by Swapping Adjacent Characters
**Đề bài chi tiết**: Cho chuỗi `s`, bạn có thể đổi chỗ hai ký tự liền kề. Tìm số lần đổi chỗ ít nhất để tạo thành một palindrome. Trả về số lần thao tác đó. Nếu không thể tạo thành palindrome, trả về -1.
**Phân tích thuật toán**: Đây là một phiên bản tương tự với "Minimum Number of Moves to Make Palindrome". Hai con trỏ kiểm tra tính đối xứng. Khi phát hiện chênh lệch, cố gắng dời vị trí ký tự từ xa vào đúng vị trí tương ứng. Đếm các bước dời.
**Mã nguồn Java**:
```java
class Solution {
    public int minSwapsToMakePalindrome(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        int oddCount = 0;
        for (int c : count) if (c % 2 != 0) oddCount++;
        if (oddCount > 1) return -1;
        
        char[] arr = s.toCharArray();
        int left = 0, right = arr.length - 1;
        int swaps = 0;
        
        while (left < right) {
            if (arr[left] == arr[right]) {
                left++; right--;
            } else {
                int k = right;
                while (k > left && arr[k] != arr[left]) k--;
                
                if (k == left) {
                    char temp = arr[left];
                    arr[left] = arr[left + 1];
                    arr[left + 1] = temp;
                    swaps++;
                } else {
                    while (k < right) {
                        char temp = arr[k];
                        arr[k] = arr[k + 1];
                        arr[k + 1] = temp;
                        swaps++;
                        k++;
                    }
                    left++; right--;
                }
            }
        }
        return swaps;
    }
}
```
**Độ phức tạp**: Thời gian $O(N^2)$, Không gian $O(N)$.

