# 04 - KMP Algorithm Problems

Danh sách 30 bài tập về thuật toán KMP (Knuth-Morris-Pratt), bao gồm các bài toán từ cơ bản đến nâng cao.

## 1. Find the Index of the First Occurrence in a String (KMP)
### Đề bài chi tiết
Cho hai chuỗi `haystack` và `needle`. Trả về chỉ số (index) của lần xuất hiện đầu tiên của `needle` trong `haystack`, hoặc `-1` nếu `needle` không thuộc `haystack`.

### Phân tích thuật toán
Sử dụng trực tiếp thuật toán KMP:
1. Xây dựng mảng LPS cho `needle`.
2. Duyệt qua `haystack`, sử dụng LPS để nhảy qua các ký tự không cần kiểm tra lại.

### Mã nguồn Java
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        if (needle.isEmpty()) return 0;
        int m = needle.length(), n = haystack.length();
        int[] lps = new int[m];
        
        // Build LPS
        for (int i = 1, len = 0; i < m; ) {
            if (needle.charAt(i) == needle.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        
        // KMP Search
        for (int i = 0, j = 0; i < n; ) {
            if (haystack.charAt(i) == needle.charAt(j)) {
                i++; j++;
            }
            if (j == m) {
                return i - j;
            } else if (i < n && haystack.charAt(i) != needle.charAt(j)) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)`
- Không gian: `O(M)` cho mảng LPS.

## 2. Repeated Substring Pattern
### Đề bài chi tiết
Cho một chuỗi `s`, kiểm tra xem nó có thể được tạo thành bằng cách lấy một chuỗi con và lặp lại nó nhiều lần hay không.

### Phân tích thuật toán
Sử dụng mảng LPS. Nếu `s` có thể được tạo bởi chuỗi con lặp lại, thì phần dư của phép chia độ dài `s` cho `s.length() - lps[s.length() - 1]` sẽ bằng 0.

### Mã nguồn Java
```java
public class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int n = s.length();
        int[] lps = new int[n];
        int len = 0, i = 1;
        while (i < n) {
            if (s.charAt(i) == s.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int l = lps[n - 1];
        return (l > 0 && n % (n - l) == 0);
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 3. Shortest Palindrome
### Đề bài chi tiết
Cho chuỗi `s`. Bạn có thể thêm các ký tự vào phía trước chuỗi để tạo thành chuỗi đối xứng (palindrome). Tìm chuỗi đối xứng ngắn nhất có thể tạo ra.

### Phân tích thuật toán
Tạo chuỗi mới `t = s + "#" + s.reverse()`. Tính mảng LPS cho `t`. Ký tự cuối cùng của mảng LPS, `lps[t.length() - 1]`, sẽ là độ dài của tiền tố đối xứng dài nhất trong `s`. Các ký tự còn lại cần được đảo ngược và thêm vào trước.

### Mã nguồn Java
```java
public class Solution {
    public String shortestPalindrome(String s) {
        String rev = new StringBuilder(s).reverse().toString();
        String t = s + "#" + rev;
        int[] lps = new int[t.length()];
        
        for (int i = 1, len = 0; i < t.length(); ) {
            if (t.charAt(i) == t.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        
        int p = lps[t.length() - 1];
        return rev.substring(0, s.length() - p) + s;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 4. Longest Happy Prefix
### Đề bài chi tiết
Một chuỗi được gọi là "Happy Prefix" nếu nó là một tiền tố không rỗng và đồng thời là một hậu tố (không bao gồm toàn bộ chuỗi). Trả về Happy Prefix dài nhất của chuỗi `s`.

### Phân tích thuật toán
Đây chính là định nghĩa gốc của giá trị `lps[n - 1]`. Ta chỉ cần tạo mảng LPS và lấy chuỗi con từ `0` đến `lps[n - 1]`.

### Mã nguồn Java
```java
public class Solution {
    public String longestPrefix(String s) {
        int n = s.length();
        if (n == 0) return "";
        int[] lps = new int[n];
        
        for (int i = 1, len = 0; i < n; ) {
            if (s.charAt(i) == s.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        
        return s.substring(0, lps[n - 1]);
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 5. Find All Occurrences of Pattern (KMP)
### Đề bài chi tiết
Cho văn bản `text` và `pattern`. Tìm tất cả các chỉ số mà tại đó `pattern` xuất hiện trong `text` (bao gồm cả các phần chồng lấp).

### Phân tích thuật toán
Khi `j == M` trong thuật toán KMP, ta lưu chỉ số và gán `j = lps[j - 1]` để tiếp tục tìm kiếm thay vì dừng lại.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<Integer> searchAll(String text, String pattern) {
        List<Integer> res = new ArrayList<>();
        if (pattern.isEmpty()) return res;
        
        int n = text.length(), m = pattern.length();
        int[] lps = new int[m];
        
        for (int i = 1, len = 0; i < m; ) {
            if (pattern.charAt(i) == pattern.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        for (int i = 0, j = 0; i < n; ) {
            if (text.charAt(i) == pattern.charAt(j)) {
                i++; j++;
            }
            if (j == m) {
                res.add(i - j);
                j = lps[j - 1];
            } else if (i < n && text.charAt(i) != pattern.charAt(j)) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)`
- Không gian: `O(M)`

## 6. String Matching in an Array
### Đề bài chi tiết
Cho mảng các chuỗi `words`. Trả về tất cả các chuỗi trong `words` là chuỗi con của một từ khác trong mảng. (Ở đây KMP có thể được áp dụng để kiểm tra chuỗi con).

### Phân tích thuật toán
Với mỗi cặp chuỗi, dùng KMP để kiểm tra chuỗi ngắn có thuộc chuỗi dài hay không. (Có thể dùng `.contains()` tích hợp sẵn, nhưng dùng KMP để thể hiện kỹ năng áp dụng thuật toán).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<String> stringMatching(String[] words) {
        Set<String> res = new HashSet<>();
        for (int i = 0; i < words.length; i++) {
            for (int j = 0; j < words.length; j++) {
                if (i != j && words[j].length() >= words[i].length()) {
                    if (kmp(words[j], words[i])) {
                        res.add(words[i]);
                    }
                }
            }
        }
        return new ArrayList<>(res);
    }
    
    private boolean kmp(String text, String pattern) {
        int[] lps = new int[pattern.length()];
        for(int i=1, len=0; i<pattern.length();) {
            if(pattern.charAt(i) == pattern.charAt(len)) lps[i++] = ++len;
            else if(len != 0) len = lps[len-1];
            else lps[i++] = 0;
        }
        for(int i=0, j=0; i<text.length();) {
            if(text.charAt(i) == pattern.charAt(j)) { i++; j++; }
            if(j == pattern.length()) return true;
            else if(i < text.length() && text.charAt(i) != pattern.charAt(j)) {
                if(j != 0) j = lps[j-1]; else i++;
            }
        }
        return false;
    }
}
```

### Độ phức tạp
- Thời gian: `O(W^2 * L)`, với `W` là số từ, `L` là chiều dài trung bình.
- Không gian: `O(L)` cho mảng LPS.

## 7. Number of Matching Subsequences
### Đề bài chi tiết
Cho chuỗi `s` và mảng chuỗi `words`. Tìm số lượng `words[i]` là một chuỗi con (subsequence) của `s`. (Lưu ý bài này không trực tiếp dùng KMP, nhưng nằm trong mảng String Matching Pattern và các state-machine giống DFA của KMP).

### Phân tích thuật toán
Dùng kỹ thuật Next Array (như DFA) để lưu lại vị trí xuất hiện tiếp theo của từng ký tự trong chuỗi `s`. Từ đó, với mỗi `word`, ta duyệt nhánh `O(L)` thời gian.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int numMatchingSubseq(String s, String[] words) {
        int[][] next = new int[s.length() + 1][26];
        Arrays.fill(next[s.length()], -1);
        
        for (int i = s.length() - 1; i >= 0; i--) {
            for (int j = 0; j < 26; j++) {
                next[i][j] = next[i + 1][j];
            }
            next[i][s.charAt(i) - 'a'] = i;
        }
        
        int count = 0;
        for (String w : words) {
            int currIdx = 0;
            boolean match = true;
            for (char c : w.toCharArray()) {
                if (currIdx >= s.length() || next[currIdx][c - 'a'] == -1) {
                    match = false;
                    break;
                }
                currIdx = next[currIdx][c - 'a'] + 1;
            }
            if (match) count++;
        }
        return count;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + W * L)`
- Không gian: `O(N * 26)`

## 8. Minimum Time to Revert Word to Initial State
### Đề bài chi tiết
Bạn có chuỗi `word` và một số nguyên `k`. Trong mỗi bước, bạn loại bỏ `k` ký tự đầu tiên và thêm `k` ký tự bất kỳ vào cuối. Tìm số bước ít nhất để chuỗi trở lại trạng thái ban đầu.

### Phân tích thuật toán
Tìm một tiền tố của chuỗi đồng thời là hậu tố của chuỗi gốc (Longest Prefix Suffix), với điều kiện số ký tự loại bỏ phải chia hết cho `k`. Hàm Z-algorithm hoặc KMP (LPS array) có thể được áp dụng.

### Mã nguồn Java
```java
public class Solution {
    public int minimumTimeToInitialState(String word, int k) {
        int n = word.length();
        int[] lps = new int[n];
        for (int i = 1, len = 0; i < n; ) {
            if (word.charAt(i) == word.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        
        int len = lps[n - 1];
        while (len > 0 && (n - len) % k != 0) {
            len = lps[len - 1];
        }
        
        if ((n - len) % k == 0) return (n - len) / k;
        return (n + k - 1) / k; // ceiling div
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 9. Find the Length of the Longest Common Prefix
### Đề bài chi tiết
Cho hai mảng số nguyên, tìm độ dài của tiền tố chung dài nhất giữa một số bất kỳ từ mảng thứ nhất và một số bất kỳ từ mảng thứ hai. (Sử dụng cấu trúc Trie hoặc so sánh chuỗi).

### Phân tích thuật toán
Dù không sử dụng KMP truyền thống, bài toán Prefix thường dùng cấu trúc Trie để lưu trữ các tiền tố. Chuyển các số thành chuỗi và đẩy vào Trie.

### Mã nguồn Java
```java
class TrieNode {
    TrieNode[] children = new TrieNode[10];
}

public class Solution {
    public int longestCommonPrefix(int[] arr1, int[] arr2) {
        TrieNode root = new TrieNode();
        for (int num : arr1) {
            TrieNode curr = root;
            String s = String.valueOf(num);
            for (char c : s.toCharArray()) {
                if (curr.children[c - '0'] == null) {
                    curr.children[c - '0'] = new TrieNode();
                }
                curr = curr.children[c - '0'];
            }
        }
        
        int maxLen = 0;
        for (int num : arr2) {
            TrieNode curr = root;
            String s = String.valueOf(num);
            int len = 0;
            for (char c : s.toCharArray()) {
                if (curr.children[c - '0'] != null) {
                    len++;
                    curr = curr.children[c - '0'];
                } else {
                    break;
                }
            }
            maxLen = Math.max(maxLen, len);
        }
        return maxLen;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N * L)` với `L` là số chữ số lớn nhất.
- Không gian: `O(N * L)` cho Trie.

## 10. Construct String with Minimum Cost
### Đề bài chi tiết
Tìm cách tạo chuỗi `target` bằng cách ghép các chuỗi từ `words` sao cho tổng chi phí `costs` là nhỏ nhất.

### Phân tích thuật toán
Sử dụng Aho-Corasick (mở rộng của KMP trên Trie) kết hợp với Dynamic Programming. Aho-Corasick giúp duyệt qua chuỗi `target` và tìm mọi word khớp tại mỗi vị trí nhanh chóng. 

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    // Để giữ code đơn giản, áp dụng Trie và DP (hoạt động tốt với bộ test vừa phải)
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int cost = Integer.MAX_VALUE;
    }
    
    public int minimumCost(String target, String[] words, int[] costs) {
        TrieNode root = new TrieNode();
        for (int i = 0; i < words.length; i++) {
            TrieNode curr = root;
            for (char c : words[i].toCharArray()) {
                if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.cost = Math.min(curr.cost, costs[i]);
        }
        
        int n = target.length();
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        
        for (int i = 0; i < n; i++) {
            if (dp[i] == Integer.MAX_VALUE) continue;
            TrieNode curr = root;
            for (int j = i; j < n; j++) {
                char c = target.charAt(j);
                if (curr.children[c - 'a'] == null) break;
                curr = curr.children[c - 'a'];
                if (curr.cost != Integer.MAX_VALUE) {
                    dp[j + 1] = Math.min(dp[j + 1], dp[i] + curr.cost);
                }
            }
        }
        return dp[n] == Integer.MAX_VALUE ? -1 : dp[n];
    }
}
```

### Độ phức tạp
- Thời gian: `O(N^2 + W * L)`
- Không gian: `O(W * L + N)`

## 11. KMP trên Ma trận 2D (2D KMP Algorithm)
### Đề bài chi tiết
Cho một ma trận văn bản `text` và một ma trận mẫu `pattern`. Trả về `true` nếu `pattern` xuất hiện dưới dạng một khối 2D liên tiếp bên trong `text`, ngược lại trả về `false`.

### Phân tích thuật toán
Sử dụng KMP 1D như một kỹ thuật phụ trợ. Ta quét qua từng hàng của `text`, nếu hàng đầu tiên của `pattern` xuất hiện trong một hàng nào đó của `text` (tìm bằng KMP), ta sẽ tiếp tục kiểm tra thủ công các hàng còn lại của `pattern` tại cùng cột đó. Phương pháp này gọi là "khớp mẫu từng dòng" (Row-by-row matching).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public boolean searchMatrix(char[][] text, char[][] pattern) {
        int r = text.length, c = text[0].length;
        int pr = pattern.length, pc = pattern[0].length;
        if (pr > r || pc > c) return false;
        
        String[] patStr = new String[pr];
        for(int i = 0; i < pr; i++) patStr[i] = new String(pattern[i]);
        String[] textStr = new String[r];
        for(int i = 0; i < r; i++) textStr[i] = new String(text[i]);
        
        for (int i = 0; i <= r - pr; i++) {
            List<Integer> matches = kmp(textStr[i], patStr[0]);
            for (int col : matches) {
                boolean found = true;
                for (int k = 1; k < pr; k++) {
                    if (!textStr[i + k].substring(col, col + pc).equals(patStr[k])) {
                        found = false;
                        break;
                    }
                }
                if (found) return true;
            }
        }
        return false;
    }
    
    private List<Integer> kmp(String text, String pat) {
        int n = text.length(), m = pat.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m;) {
            if (pat.charAt(i) == pat.charAt(len)) lps[i++] = ++len;
            else if (len != 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        List<Integer> res = new ArrayList<>();
        for (int i = 0, j = 0; i < n;) {
            if (text.charAt(i) == pat.charAt(j)) { i++; j++; }
            if (j == m) { res.add(i - j); j = lps[j - 1]; }
            else if (i < n && text.charAt(i) != pat.charAt(j)) {
                if (j != 0) j = lps[j - 1]; else i++;
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: Trung bình `O(N * M + R * C)`, tối tệ `O(N * M * R * C)` tùy vào số lần khớp ảo.
- Không gian: `O(N * M + R * C)` để lưu chuỗi (hoặc `O(C)` nếu tối ưu mảng LPS).

## 12. KMP luồng động (Stream Checker)
### Đề bài chi tiết
Thiết kế một cấu trúc dữ liệu nhận một chuỗi `pattern` lúc khởi tạo. Sau đó, nó sẽ liên tục nhận từng ký tự từ một luồng dữ liệu thông qua phương thức `query(char letter)`. Phương thức trả về `true` nếu ký tự vừa nhận kết thúc một chuỗi khớp hoàn toàn với `pattern`.

### Phân tích thuật toán
Thuật toán KMP hoạt động như một DFA (Deterministic Finite Automaton). Thay vì phải lưu lại toàn bộ chuỗi ký tự đã nhập (rất tốn kém bộ nhớ đối với luồng dữ liệu liên tục), ta chỉ cần duy trì trạng thái `j` hiện tại (đại diện cho số lượng ký tự đã khớp). Mỗi lần có ký tự mới, ta nhảy `j` dựa trên mảng LPS.

### Mã nguồn Java
```java
public class StreamChecker {
    private String pattern;
    private int[] lps;
    private int j;

    public StreamChecker(String pattern) {
        this.pattern = pattern;
        int m = pattern.length();
        this.lps = new int[m];
        this.j = 0;
        
        // Build LPS
        for (int i = 1, len = 0; i < m; ) {
            if (pattern.charAt(i) == pattern.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
    }

    public boolean query(char letter) {
        while (j > 0 && letter != pattern.charAt(j)) {
            j = lps[j - 1];
        }
        if (letter == pattern.charAt(j)) {
            j++;
        }
        if (j == pattern.length()) {
            j = lps[j - 1]; // Reset một phần để tiếp tục nhận ký tự tiếp theo
            return true;
        }
        return false;
    }
}
```

### Độ phức tạp
- Thời gian: Khởi tạo `O(M)`. Phương thức `query(char letter)` có thời gian khấu hao `O(1)`.
- Không gian: `O(M)` cho mảng LPS.

## 13. Kiểm tra tính xoay vòng của chuỗi (String Rotation)
### Đề bài chi tiết
Cho hai chuỗi `s` và `goal`. Trả về `true` nếu `s` có thể trở thành `goal` sau khi thực hiện một số phép xoay vòng các ký tự (chuyển ký tự đầu xuống cuối cùng).

### Phân tích thuật toán
Nếu hai chuỗi khác độ dài, ta có thể trả về `false` ngay lập tức. Nếu cùng độ dài, nếu ta nối chuỗi `s` vào chính nó tạo thành `s + s`, thì mọi kết quả xoay vòng có thể có của `s` đều sẽ là một chuỗi con nằm trong `s + s`. Bài toán quy về việc tìm `goal` trong `s + s`, sử dụng KMP.

### Mã nguồn Java
```java
public class Solution {
    public boolean rotateString(String s, String goal) {
        if (s.length() != goal.length()) return false;
        String doubleS = s + s;
        return kmp(doubleS, goal);
    }
    
    private boolean kmp(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        if (m == 0) return true;
        int[] lps = new int[m];
        
        for (int i = 1, len = 0; i < m; ) {
            if (pattern.charAt(i) == pattern.charAt(len)) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        for (int i = 0, j = 0; i < n; ) {
            if (text.charAt(i) == pattern.charAt(j)) { i++; j++; }
            if (j == m) return true;
            else if (i < n && text.charAt(i) != pattern.charAt(j)) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return false;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)` với `N` là độ dài chuỗi `s`.
- Không gian: `O(N)` cho chuỗi `doubleS` và mảng LPS.

## 14. Đếm số lần xuất hiện không chồng lấp của chuỗi con
### Đề bài chi tiết
Cho văn bản `text` và mẫu `pattern`. Đếm số lần xuất hiện tối đa của `pattern` trong `text` sao cho các lần xuất hiện không có bất kỳ ký tự nào bị chồng lấp lên nhau.

### Phân tích thuật toán
Ta duyệt thuật toán KMP như bình thường. Khi tìm thấy một lần khớp hoàn chỉnh (`j == pattern.length()`), thay vì giảm `j` bằng `lps[j - 1]` để tìm chuỗi chồng lấp, ta sẽ tăng biến đếm lên `1` và reset `j = 0`. Việc này buộc thuật toán bỏ qua mọi sự chồng lấp và bắt đầu tìm kiếm chuỗi mẫu mới hoàn toàn ở các vị trí tiếp theo.

### Mã nguồn Java
```java
public class Solution {
    public int maxNonOverlapping(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        if (m == 0) return 0;
        int[] lps = new int[m];
        
        for (int i = 1, len = 0; i < m; ) {
            if (pattern.charAt(i) == pattern.charAt(len)) lps[i++] = ++len;
            else if (len != 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        int count = 0;
        for (int i = 0, j = 0; i < n; ) {
            if (text.charAt(i) == pattern.charAt(j)) { i++; j++; }
            
            if (j == m) {
                count++;
                j = 0; // Bắt đầu tìm kiếm mẫu mới, bỏ qua mọi chồng lấp
            } else if (i < n && text.charAt(i) != pattern.charAt(j)) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return count;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)`
- Không gian: `O(M)`

## 15. Mảng Z-Algorithm
### Đề bài chi tiết
Cho một chuỗi `s`, hãy trả về mảng Z, trong đó `Z[i]` là độ dài tiền tố chung dài nhất giữa bản thân chuỗi `s` và hậu tố của `s` bắt đầu tại vị trí `i`. (Đây là thuật toán có ý tưởng tương tự KMP dùng để tìm kiếm chuỗi con).

### Phân tích thuật toán
Thuật toán Z duy trì một khung `[L, R]` biểu diễn khoảng khớp lớn nhất đã tìm thấy. Khi tính toán vị trí `i`:
- Nếu `i > R`, kiểm tra thủ công và cập nhật lại `L` và `R`.
- Nếu `i <= R`, ta có thể sử dụng kết quả đã tính trước tại `Z[i - L]`. Tuy nhiên, chỉ được kế thừa nếu giá trị này không vượt quá khoảng `R - i + 1`, nếu vượt quá phải duyệt tiếp từ `R`.

### Mã nguồn Java
```java
public class Solution {
    public int[] getZArray(String s) {
        int n = s.length();
        int[] z = new int[n];
        int L = 0, R = 0;
        
        for (int i = 1; i < n; i++) {
            if (i > R) {
                L = R = i;
                while (R < n && s.charAt(R - L) == s.charAt(R)) R++;
                z[i] = R - L;
                R--;
            } else {
                int k = i - L;
                if (z[k] < R - i + 1) {
                    z[i] = z[k];
                } else {
                    L = i;
                    while (R < n && s.charAt(R - L) == s.charAt(R)) R++;
                    z[i] = R - L;
                    R--;
                }
            }
        }
        return z;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)` để lưu trữ mảng Z.

## 16. Khớp chuỗi với ký tự thay thế đoạn (Wildcard Pattern Matching)
### Đề bài chi tiết
Cho văn bản `text` và mẫu `pattern`. Trong `pattern` có thể chứa ký tự đại diện `*`, đại diện cho bất kỳ chuỗi ký tự nào (bao gồm cả chuỗi rỗng). Trả về `true` nếu `pattern` khớp với `text`. Bài toán yêu cầu sử dụng KMP để cải thiện việc tìm kiếm các phân đoạn.

### Phân tích thuật toán
Ta chia `pattern` bằng các dấu `*` thành nhiều chuỗi con (sub-patterns). Dùng thuật toán KMP để tìm kiếm lần lượt các chuỗi con này bên trong `text` theo thứ tự từ trái sang phải. Biến `currentIdx` sẽ lưu vị trí cuối cùng được khớp để đảm bảo chuỗi con tiếp theo xuất hiện sau vị trí đó. Lưu ý cần xử lý cẩn thận nếu mẫu không bắt đầu hoặc kết thúc bằng `*`.

### Mã nguồn Java
```java
public class Solution {
    public boolean isMatch(String text, String pattern) {
        // Tách pattern thành các từ khóa phụ, giữ lại các phần tử rỗng
        String[] parts = pattern.split("\\*", -1);
        int currentIdx = 0;
        
        for (int k = 0; k < parts.length; k++) {
            String part = parts[k];
            if (part.isEmpty()) continue;
            
            int idx = kmpSearch(text, part, currentIdx);
            if (idx == -1) return false;
            
            // Nếu phần tử đầu tiên không chứa *, nó phải khớp ở ngay đầu text
            if (k == 0 && pattern.charAt(0) != '*' && idx != 0) return false;
            
            currentIdx = idx + part.length();
        }
        
        // Nếu pattern không kết thúc bằng *, text không được còn phần thừa nào phía sau
        if (pattern.charAt(pattern.length() - 1) != '*' && currentIdx != text.length()) {
            return text.endsWith(parts[parts.length - 1]);
        }
        return true;
    }
    
    private int kmpSearch(String text, String pat, int startIdx) {
        if (pat.isEmpty()) return startIdx;
        int n = text.length(), m = pat.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (pat.charAt(i) == pat.charAt(len)) lps[i++] = ++len;
            else if (len != 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        for (int i = startIdx, j = 0; i < n; ) {
            if (text.charAt(i) == pat.charAt(j)) { i++; j++; }
            if (j == m) return i - j;
            else if (i < n && text.charAt(i) != pat.charAt(j)) {
                if (j != 0) j = lps[j - 1]; else i++;
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- Thời gian: Tối tệ `O(N * M)` do đặc tính chia cắt mẫu, trung bình `O(N + M)`.
- Không gian: `O(M)`.

## 17. Tìm khoảng cách nhỏ nhất giữa hai Pattern
### Đề bài chi tiết
Cho một văn bản `text` và hai mẫu `word1`, `word2`. Tìm khoảng cách ngắn nhất (tính bằng số ký tự nằm giữa) giữa một lần xuất hiện của `word1` và một lần xuất hiện của `word2` trong `text`. Nếu chồng lấp thì khoảng cách bằng `0`.

### Phân tích thuật toán
Áp dụng KMP để tìm toàn bộ chỉ số bắt đầu của `word1` và lưu vào `pos1`, tìm của `word2` lưu vào `pos2`. Hai mảng này sẽ có thứ tự tăng dần. Sử dụng kỹ thuật hai con trỏ (Two Pointers) để duyệt qua `pos1` và `pos2`, tìm khoảng cách tối thiểu giữa kết thúc của từ này và bắt đầu của từ kia.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int findShortestDistance(String text, String word1, String word2) {
        List<Integer> pos1 = kmp(text, word1);
        List<Integer> pos2 = kmp(text, word2);
        
        if (pos1.isEmpty() || pos2.isEmpty()) return -1;
        
        int minDistance = Integer.MAX_VALUE;
        int i = 0, j = 0;
        while (i < pos1.size() && j < pos2.size()) {
            int p1 = pos1.get(i);
            int p2 = pos2.get(j);
            
            if (p1 < p2) {
                minDistance = Math.min(minDistance, p2 - (p1 + word1.length()));
                i++;
            } else {
                minDistance = Math.min(minDistance, p1 - (p2 + word2.length()));
                j++;
            }
        }
        return minDistance < 0 ? 0 : minDistance; 
    }
    
    private List<Integer> kmp(String text, String pat) {
        int m = pat.length(), n = text.length();
        int[] lps = new int[m];
        for(int i=1, len=0; i<m; ) {
            if(pat.charAt(i) == pat.charAt(len)) lps[i++] = ++len;
            else if(len > 0) len = lps[len-1];
            else lps[i++] = 0;
        }
        List<Integer> res = new ArrayList<>();
        for(int i=0, j=0; i<n; ) {
            if(text.charAt(i) == pat.charAt(j)) { i++; j++; }
            if(j == m) { res.add(i - j); j = lps[j-1]; }
            else if(i < n && text.charAt(i) != pat.charAt(j)) {
                if(j != 0) j = lps[j-1]; else i++;
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M1 + M2)`
- Không gian: `O(N)` cho danh sách các chỉ số.

## 18. KMP trên mảng số nguyên
### Đề bài chi tiết
Cho một mảng các số nguyên `nums` và mảng số nguyên `pattern`. Kiểm tra xem `pattern` có phải là một mảng con liên tiếp của `nums` hay không. Trả về chỉ số bắt đầu đầu tiên, hoặc `-1`.

### Phân tích thuật toán
Thuật toán KMP không phụ thuộc vào bảng mã ký tự, nó chỉ dựa trên việc kiểm tra sự tương đồng (bằng nhau). Chúng ta có thể dễ dàng thay đổi kiểu dữ liệu đầu vào của chuỗi thành mảng số nguyên `int[]` và tính toán mảng LPS cho dãy số nguyên.

### Mã nguồn Java
```java
public class Solution {
    public int findArray(int[] nums, int[] pattern) {
        int n = nums.length, m = pattern.length;
        if (m == 0) return 0;
        int[] lps = new int[m];
        
        for (int i = 1, len = 0; i < m; ) {
            if (pattern[i] == pattern[len]) lps[i++] = ++len;
            else if (len != 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        for (int i = 0, j = 0; i < n; ) {
            if (nums[i] == pattern[j]) { i++; j++; }
            if (j == m) return i - j;
            else if (i < n && nums[i] != pattern[j]) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)`
- Không gian: `O(M)` cho mảng LPS.

## 19. Tạo chuỗi ngẫu nhiên tránh Pattern (DP + KMP/DFA)
### Đề bài chi tiết
Cho một chuỗi `pattern` có độ dài `m` và số nguyên `n`. Tìm số lượng các chuỗi có độ dài `n` (chỉ chứa các ký tự chữ thường từ 'a' đến 'z') sao cho không chứa `pattern` dưới dạng chuỗi con. Trả về kết quả sau khi chia lấy dư cho `10^9 + 7`.

### Phân tích thuật toán
Ta mô phỏng KMP như một biểu đồ chuyển đổi trạng thái DFA. `dfa[j][c]` cho biết trạng thái mới nếu trạng thái hiện tại là `j` và ta ghép thêm ký tự `c`.
Gọi `dp[i][j]` là số lượng các chuỗi độ dài `i` kết thúc tại trạng thái `j`. Trạng thái `m` đại diện cho việc `pattern` đã khớp, do đó ta không cho phép bất kỳ chuỗi nào đi tới trạng thái `m`. 
Sử dụng DP và mảng DFA được sinh ra bởi LPS.

### Mã nguồn Java
```java
public class Solution {
    public int findGoodStrings(int n, String pattern) {
        int m = pattern.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (pattern.charAt(i) == pattern.charAt(len)) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        // DFA[j][c] = trạng thái tiếp theo nếu đang ở j và nhận c
        int[][] dfa = new int[m][26];
        for (int j = 0; j < m; j++) {
            for (int c = 0; c < 26; c++) {
                int state = j;
                while (state > 0 && pattern.charAt(state) - 'a' != c) {
                    state = lps[state - 1];
                }
                if (pattern.charAt(state) - 'a' == c) {
                    state++;
                }
                dfa[j][c] = state;
            }
        }
        
        int MOD = 1_000_000_007;
        int[][] dp = new int[n + 1][m];
        dp[0][0] = 1; // 1 cách để có chuỗi rỗng
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (dp[i][j] == 0) continue;
                for (int c = 0; c < 26; c++) {
                    int nextState = dfa[j][c];
                    if (nextState < m) { // Tránh trạng thái m (khớp pattern)
                        dp[i + 1][nextState] = (dp[i + 1][nextState] + dp[i][j]) % MOD;
                    }
                }
            }
        }
        
        int ans = 0;
        for (int j = 0; j < m; j++) {
            ans = (ans + dp[n][j]) % MOD;
        }
        return ans;
    }
}
```

### Độ phức tạp
- Thời gian: `O(M * 26 + N * M * 26)`.
- Không gian: `O(N * M)` cho DP và `O(M * 26)` cho DFA.

## 20. Thuật toán Aho-Corasick cơ bản (Multiple Pattern Matching)
### Đề bài chi tiết
Tìm kiếm sự xuất hiện của nhiều chuỗi mẫu (một danh sách `words`) cùng lúc trong một chuỗi văn bản `text`. Sử dụng cấu trúc Aho-Corasick để tìm vị trí xuất hiện của bất kỳ chuỗi mẫu nào trong một lần duyệt duy nhất.

### Phân tích thuật toán
Thuật toán Aho-Corasick là sự kết hợp của cây Tiền tố (Trie) và mảng LPS (dưới dạng failure links).
1. Xây dựng Trie từ các từ mẫu.
2. Dùng Duyệt theo chiều rộng (BFS) để tính toán nút thất bại (`fail`) cho từng nút. Nếu tại một nút không khớp ký tự, nó sẽ nhảy đến vị trí `fail` tương tự như `j = lps[j-1]`.
3. Duyệt `text` qua DFA mở rộng này để đếm hoặc tìm từ khớp.

### Mã nguồn Java
```java
import java.util.*;

class TrieNode {
    TrieNode[] children = new TrieNode[26];
    TrieNode fail = null;
    List<Integer> wordIndices = new ArrayList<>();
}

public class AhoCorasick {
    private TrieNode root = new TrieNode();
    
    public void buildAutomaton(String[] words) {
        // 1. Build Trie
        for (int i = 0; i < words.length; i++) {
            TrieNode curr = root;
            for (char c : words[i].toCharArray()) {
                if (curr.children[c - 'a'] == null) {
                    curr.children[c - 'a'] = new TrieNode();
                }
                curr = curr.children[c - 'a'];
            }
            curr.wordIndices.add(i);
        }
        
        // 2. Build Failure Links (BFS)
        Queue<TrieNode> q = new LinkedList<>();
        for (int i = 0; i < 26; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        
        while (!q.isEmpty()) {
            TrieNode curr = q.poll();
            for (int i = 0; i < 26; i++) {
                if (curr.children[i] != null && curr.children[i] != root) {
                    TrieNode failState = curr.fail;
                    curr.children[i].fail = failState.children[i];
                    // Nối kết quả từ failure state
                    curr.children[i].wordIndices.addAll(curr.children[i].fail.wordIndices);
                    q.add(curr.children[i]);
                } else {
                    curr.children[i] = curr.fail.children[i];
                }
            }
        }
    }
    
    public List<Integer> search(String text) {
        List<Integer> foundIndices = new ArrayList<>();
        TrieNode curr = root;
        for (char c : text.toCharArray()) {
            curr = curr.children[c - 'a'];
            if (!curr.wordIndices.isEmpty()) {
                foundIndices.addAll(curr.wordIndices);
            }
        }
        return foundIndices;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + L + Z)` với `N` là độ dài văn bản, `L` là tổng chiều dài mẫu, `Z` là số lần khớp.
- Không gian: `O(L * 26)`.

## 21. Khớp chuỗi có tối đa k lỗi sai (String Matching with at most k mismatches)
### Đề bài chi tiết
Cho văn bản `text`, chuỗi mẫu `pattern` và số nguyên `k`. Tìm tất cả các chỉ số bắt đầu trong `text` sao cho độ lệch (số ký tự khác nhau ở cùng vị trí - Hamming distance) giữa chuỗi con của `text` và `pattern` không vượt quá `k`.

### Phân tích thuật toán
Thuật toán KMP gốc không xử lý được các lỗi sai lệch do tính chất chu kỳ bị phá vỡ. Để bám sát tư tưởng tìm kiếm, giải pháp đơn giản nhất là duyệt trượt cửa sổ (Sliding Window) kết hợp đếm lỗi. (Trong thực tế, thuật toán tối ưu là Kangaroo method dùng Suffix Array + LCP hoặc Rolling Hash, nhưng vượt quá giới hạn của KMP cơ bản). Ở đây ta cài đặt phương pháp duyệt có kết hợp thoát sớm (early exit) khi số lỗi vượt quá `k`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<Integer> searchWithMismatches(String text, String pattern, int k) {
        List<Integer> res = new ArrayList<>();
        int n = text.length(), m = pattern.length();
        if (m > n) return res;
        
        for (int i = 0; i <= n - m; i++) {
            int mismatches = 0;
            for (int j = 0; j < m; j++) {
                if (text.charAt(i + j) != pattern.charAt(j)) {
                    mismatches++;
                }
                if (mismatches > k) break;
            }
            if (mismatches <= k) {
                res.add(i);
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N * M)` trong trường hợp tệ nhất, nhanh hơn nhiều trong thực tế do thoát sớm.
- Không gian: `O(1)` (không tính mảng kết quả).

## 22. Tìm chuỗi con tuần hoàn tối thiểu của văn bản (Minimum Periodic Substring)
### Đề bài chi tiết
Cho một chuỗi `s`, tìm chuỗi con liên tiếp ngắn nhất bắt đầu từ chỉ số `0` mà nếu lặp lại nhiều lần sẽ tạo thành chính `s` (có thể bị cắt ngang ở lần lặp cuối cùng). Ví dụ: `ababab` -> `ab`, `abcabcab` -> `abc`.

### Phân tích thuật toán
Sử dụng mảng LPS (Longest Prefix Suffix) của thuật toán KMP. Độ dài của chuỗi lặp lại nhỏ nhất luôn bằng `N - LPS[N - 1]` với `N` là độ dài chuỗi gốc. Điều này hoạt động kể cả khi vòng lặp cuối cùng chưa hoàn thiện, vì LPS luôn tìm tiền tố và hậu tố khớp nhau lớn nhất.

### Mã nguồn Java
```java
public class Solution {
    public String minPeriodicSubstring(String s) {
        int n = s.length();
        if (n == 0) return "";
        int[] lps = new int[n];
        
        for (int i = 1, len = 0; i < n; ) {
            if (s.charAt(i) == s.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        
        int period = n - lps[n - 1];
        return s.substring(0, period);
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)` cho mảng LPS.

## 23. Áp dụng KMP nén chuỗi lặp lại (String Compression)
### Đề bài chi tiết
Kiểm tra xem một chuỗi `s` có thể được nén thành định dạng lặp lại hay không. Nếu `s` được tạo thành từ một chuỗi con lặp lại hoàn toàn (ít nhất 2 lần), hãy trả về chuỗi nén dưới dạng `"chuỗi_con*số_lần"`. Ngược lại, trả về chính chuỗi `s`. Ví dụ: `ababab` -> `ab*3`.

### Phân tích thuật toán
Sử dụng mảng LPS. Một chuỗi có thể được lặp lại hoàn chỉnh nếu và chỉ nếu `LPS[N - 1] > 0` và `N % (N - LPS[N - 1]) == 0`. Khi đó, chuỗi con là `s.substring(0, N - LPS[N - 1])` và số lần lặp là `N / (N - LPS[N - 1])`.

### Mã nguồn Java
```java
public class Solution {
    public String compressRepeated(String s) {
        int n = s.length();
        if (n < 2) return s;
        int[] lps = new int[n];
        
        for (int i = 1, len = 0; i < n; ) {
            if (s.charAt(i) == s.charAt(len)) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        int period = n - lps[n - 1];
        if (lps[n - 1] > 0 && n % period == 0) {
            return s.substring(0, period) + "*" + (n / period);
        }
        return s;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 24. KMP phân biệt Unicode (Surrogate pairs)
### Đề bài chi tiết
Triển khai thuật toán KMP để tìm kiếm mẫu trong văn bản sao cho thuật toán hỗ trợ chính xác các ký tự Unicode nằm ngoài dải Basic Multilingual Plane (BMP) (ví dụ: Emoji). Chuỗi trong Java sử dụng UTF-16 nên một Emoji có thể chiếm 2 đơn vị `char` (Surrogate pair).

### Phân tích thuật toán
Để tránh việc khớp sai các phần của surrogate pair hoặc tính toán LPS bị sai lệch do độ dài mã hóa khác nhau, ta chuyển chuỗi `String` thành mảng các điểm mã (Code Points) kiểu `int[]`. Sau đó, áp dụng thuật toán KMP trực tiếp trên mảng số nguyên.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int kmpUnicode(String text, String pattern) {
        int[] tPoints = text.codePoints().toArray();
        int[] pPoints = pattern.codePoints().toArray();
        
        int n = tPoints.length, m = pPoints.length;
        if (m == 0) return 0;
        
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (pPoints[i] == pPoints[len]) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        for (int i = 0, j = 0; i < n; ) {
            if (tPoints[i] == pPoints[j]) { i++; j++; }
            if (j == m) return i - j; // Trả về chỉ số dựa trên Code Point
            else if (i < n && tPoints[i] != pPoints[j]) {
                if (j != 0) j = lps[j - 1]; else i++;
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)` với N, M là số lượng Code Points.
- Không gian: `O(N + M)` do việc sinh mảng Code Points.

## 25. Lọc từ ngữ nhạy cảm (Profanity filtering)
### Đề bài chi tiết
Cho một tin nhắn `message` và danh sách các từ khóa cấm `badWords`. Yêu cầu thay thế mọi sự xuất hiện của bất kỳ từ khóa cấm nào trong tin nhắn bằng các dấu `*` có cùng độ dài với từ khóa đó. (Hỗ trợ từ khóa chồng lấp).

### Phân tích thuật toán
Dùng thuật toán Aho-Corasick để tìm mọi vị trí xuất hiện của các `badWords` trong `message`. Duy trì một mảng boolean `mask` có cùng độ dài với `message`. Bất cứ khi nào phát hiện một từ cấm kết thúc tại `i` có độ dài `L`, ta đánh dấu `mask` từ `i - L + 1` đến `i` là `true`. Cuối cùng, dùng `StringBuilder` thay thế các vị trí `true` thành `*`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    class TrieNode {
        TrieNode[] children = new TrieNode[256];
        TrieNode fail;
        List<Integer> wordLengths = new ArrayList<>();
    }
    
    public String filterProfanity(String message, String[] badWords) {
        TrieNode root = new TrieNode();
        for (String w : badWords) {
            TrieNode curr = root;
            for (char c : w.toCharArray()) {
                if (curr.children[c] == null) curr.children[c] = new TrieNode();
                curr = curr.children[c];
            }
            curr.wordLengths.add(w.length());
        }
        
        Queue<TrieNode> q = new LinkedList<>();
        for (int i = 0; i < 256; i++) {
            if (root.children[i] != null) {
                root.children[i].fail = root;
                q.add(root.children[i]);
            } else {
                root.children[i] = root;
            }
        }
        
        while (!q.isEmpty()) {
            TrieNode curr = q.poll();
            for (int i = 0; i < 256; i++) {
                if (curr.children[i] != null && curr.children[i] != root) {
                    curr.children[i].fail = curr.fail.children[i];
                    curr.children[i].wordLengths.addAll(curr.children[i].fail.wordLengths);
                    q.add(curr.children[i]);
                } else {
                    curr.children[i] = curr.fail.children[i];
                }
            }
        }
        
        boolean[] mask = new boolean[message.length()];
        TrieNode curr = root;
        for (int i = 0; i < message.length(); i++) {
            curr = curr.children[message.charAt(i)];
            for (int len : curr.wordLengths) {
                for (int j = i - len + 1; j <= i; j++) {
                    mask[j] = true;
                }
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < message.length(); i++) {
            if (mask[i]) sb.append('*');
            else sb.append(message.charAt(i));
        }
        return sb.toString();
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + L)` trong đó `N` là độ dài tin nhắn, `L` là tổng độ dài các từ cấm.
- Không gian: `O(N + L * 256)`.

## 26. Tìm mẫu dài nhất lặp lại ít nhất 2 lần không chồng lấp
### Đề bài chi tiết
Tìm chuỗi con dài nhất xuất hiện ít nhất 2 lần trong chuỗi đầu vào mà các lần xuất hiện không chồng lấp lên nhau. Ví dụ: `banana` -> `na` hoặc `an`.

### Phân tích thuật toán
Sử dụng KMP cho mỗi hậu tố của chuỗi `s`. Vòng lặp `i` xét chuỗi con `sub = s.substring(i)`. Tính mảng LPS của `sub`. Giá trị `LPS[k]` thể hiện tiền tố lặp lại ở cuối, nhưng để không chồng lấp ta giới hạn giá trị kiểm tra không vượt quá một nửa khoảng cách `k`.
Thuật toán này chạy trong `O(N^2)`, tốt cho các chuỗi độ dài trung bình. (Cách giải `O(N log N)` dùng Suffix Array).

### Mã nguồn Java
```java
public class Solution {
    public String longestRepeatedNonOverlappingSubstring(String s) {
        int n = s.length();
        int maxLen = 0;
        int maxStart = 0;
        
        for (int i = 0; i < n; i++) {
            String sub = s.substring(i);
            int m = sub.length();
            int[] lps = new int[m];
            
            for (int j = 1, len = 0; j < m; ) {
                if (sub.charAt(j) == sub.charAt(len)) {
                    lps[j++] = ++len;
                } else if (len > 0) {
                    len = lps[len - 1];
                } else {
                    lps[j++] = 0;
                }
            }
            
            for (int j = 0; j < m; j++) {
                // Kiểm tra điều kiện không chồng lấp
                int len = Math.min(lps[j], (j + 1) / 2);
                if (len > maxLen) {
                    maxLen = len;
                    maxStart = i + j - len + 1; // Cập nhật vị trí chuỗi gốc
                }
            }
        }
        
        return s.substring(maxStart, maxStart + maxLen);
    }
}
```

### Độ phức tạp
- Thời gian: `O(N^2)`
- Không gian: `O(N)` cho mỗi bước phân tích LPS.

## 27. Mở rộng LPS để tìm chuỗi đối xứng (Palindrome) cục bộ
### Đề bài chi tiết
Cho chuỗi `s`. Tìm tất cả các độ dài của các chuỗi con bắt đầu từ vị trí `0` của `s` (Prefixes) thỏa mãn tính chất đối xứng (Palindrome).

### Phân tích thuật toán
Tạo chuỗi `t = s + "#" + s.reverse()`. Tính mảng LPS cho `t`. 
Không chỉ giá trị cuối cùng `LPS[t.length() - 1]` mới là chuỗi đối xứng dài nhất. Theo tính chất của LPS (chuỗi con lớn nhất là tiền tố và hậu tố), các giá trị tiếp theo quay lui qua failure links như `LPS[LPS[t.length() - 1] - 1]` cũng đại diện cho các chuỗi đối xứng (vì chúng đều là tiền tố vừa khớp với hậu tố của chuỗi lật ngược).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<Integer> findAllPrefixPalindromes(String s) {
        String rev = new StringBuilder(s).reverse().toString();
        String t = s + "#" + rev;
        int n = t.length();
        int[] lps = new int[n];
        
        for (int i = 1, len = 0; i < n; ) {
            if (t.charAt(i) == t.charAt(len)) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        List<Integer> res = new ArrayList<>();
        int len = lps[n - 1];
        while (len > 0) {
            res.add(len);
            len = lps[len - 1]; // Quay lui mảng LPS
        }
        
        Collections.sort(res);
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N)`
- Không gian: `O(N)`

## 28. Thuật toán Rabin-Karp (So sánh với KMP)
### Đề bài chi tiết
Cài đặt thuật toán Rabin-Karp để tìm `pattern` trong `text`. Bài toán yêu cầu thể hiện một phương pháp thay thế hoàn hảo cho KMP dựa trên Rolling Hash để đối chiếu sự khác biệt về thuật toán.

### Phân tích thuật toán
Rabin-Karp tính toán giá trị Hash của `pattern`. Sau đó nó trượt một cửa sổ có độ dài `M` qua văn bản `text` và cập nhật mã Hash trong thời gian `O(1)`. Nếu hai mã Hash trùng nhau, ta so sánh từng ký tự để loại trừ trường hợp va chạm (collision).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<Integer> rabinKarp(String text, String pattern) {
        List<Integer> res = new ArrayList<>();
        int n = text.length(), m = pattern.length();
        if (m == 0 || m > n) return res;
        
        long base = 256;
        long mod = 1_000_000_007; // Số nguyên tố lớn
        long hashPat = 0, hashText = 0, h = 1;
        
        for (int i = 0; i < m - 1; i++) h = (h * base) % mod;
        
        for (int i = 0; i < m; i++) {
            hashPat = (base * hashPat + pattern.charAt(i)) % mod;
            hashText = (base * hashText + text.charAt(i)) % mod;
        }
        
        for (int i = 0; i <= n - m; i++) {
            if (hashPat == hashText) {
                // Kiểm tra collision
                boolean match = true;
                for (int j = 0; j < m; j++) {
                    if (text.charAt(i + j) != pattern.charAt(j)) {
                        match = false; break;
                    }
                }
                if (match) res.add(i);
            }
            if (i < n - m) {
                hashText = (base * (hashText - text.charAt(i) * h) + text.charAt(i + m)) % mod;
                if (hashText < 0) hashText += mod;
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: Trung bình `O(N + M)`, tối tệ `O(N * M)` do va chạm Hash. KMP đảm bảo `O(N + M)` mọi lúc.
- Không gian: `O(1)` (không tính mảng kết quả).

## 29. Tối ưu bộ nhớ cho LPS nếu Pattern được nén (Run-Length Encoding)
### Đề bài chi tiết
Mẫu `pattern` và văn bản `text` được cung cấp dưới dạng nén Run-Length Encoding (ví dụ: chuỗi `aaaabbc` nén thành `[('a', 4), ('b', 2), ('c', 1)]`). Hãy tìm số lần `pattern` xuất hiện trong `text` mà không cần giải nén chúng.

### Phân tích thuật toán
Thuật toán KMP hoạt động trên các mảng đối tượng `Block(ký tự, số lượng)`. Việc khớp mẫu có một lưu ý đặc biệt:
- Block ở giữa (từ `1` đến `M-2`) phải khớp cả ký tự lẫn số lượng.
- Block đầu và cuối của mẫu chỉ cần khớp ký tự và số lượng của mẫu $\le$ số lượng của văn bản.
Để đơn giản, nếu mẫu lớn hơn 2 block, ta chạy KMP trên các block giữa để tìm vị trí khớp, sau đó thủ công kiểm tra block đầu và block cuối liền kề. (Bài toán chia thành: KMP trên mảng đối tượng Block).

### Mã nguồn Java
```java
import java.util.*;

class Block {
    char c; int count;
    public Block(char c, int count) { this.c = c; this.count = count; }
    public boolean equals(Block other) { return this.c == other.c && this.count == other.count; }
}

public class Solution {
    public int searchCompressed(List<Block> text, List<Block> pattern) {
        int n = text.size(), m = pattern.size();
        if (m == 1) {
            int ans = 0;
            for (Block b : text) {
                if (b.c == pattern.get(0).c && b.count >= pattern.get(0).count) {
                    ans += b.count - pattern.get(0).count + 1;
                }
            }
            return ans;
        }
        if (m == 2) {
            int ans = 0;
            for (int i = 0; i < n - 1; i++) {
                if (text.get(i).c == pattern.get(0).c && text.get(i).count >= pattern.get(0).count &&
                    text.get(i+1).c == pattern.get(1).c && text.get(i+1).count >= pattern.get(1).count) {
                    ans++;
                }
            }
            return ans;
        }
        
        // KMP cho các Block nằm giữa pattern (từ 1 đến m-2)
        List<Block> patMid = pattern.subList(1, m - 1);
        int midLen = patMid.size();
        int[] lps = new int[midLen];
        for (int i = 1, len = 0; i < midLen; ) {
            if (patMid.get(i).equals(patMid.get(len))) lps[i++] = ++len;
            else if (len > 0) len = lps[len - 1];
            else lps[i++] = 0;
        }
        
        int ans = 0;
        for (int i = 1, j = 0; i < n - 1; ) {
            if (text.get(i).equals(patMid.get(j))) { i++; j++; }
            if (j == midLen) {
                // Kiểm tra viền ngoài (block đầu và cuối)
                Block tPrev = text.get(i - midLen - 1);
                Block tNext = text.get(i);
                Block pFirst = pattern.get(0);
                Block pLast = pattern.get(m - 1);
                
                if (tPrev.c == pFirst.c && tPrev.count >= pFirst.count &&
                    tNext.c == pLast.c && tNext.count >= pLast.count) {
                    ans++;
                }
                j = lps[j - 1];
            } else if (i < n - 1 && !text.get(i).equals(patMid.get(j))) {
                if (j != 0) j = lps[j - 1]; else i++;
            }
        }
        return ans;
    }
}
```

### Độ phức tạp
- Thời gian: `O(N + M)` trên mảng đã nén.
- Không gian: `O(M)` cho mảng LPS.

## 30. Matching trong hệ thống DNA sequencing với độ trễ tối thiểu
### Đề bài chi tiết
Chuỗi văn bản DNA rất dài (`10^8` ký tự) và chỉ giới hạn trong bảng chữ cái `A, C, G, T`. Tìm sự xuất hiện của `pattern` sao cho thuật toán đảm bảo độ trễ mỗi ký tự nhận vào là một hằng số tuyệt đối `O(1)` thực thụ (DFA 2 chiều thay vì KMP quay lui).

### Phân tích thuật toán
Thuật toán KMP dùng mảng `LPS`, trong trường hợp tệ nhất, một ký tự có thể kích hoạt quay lui (backtracking) vòng `while` nhiều lần, tạo độ trễ. 
Để đạt `O(1)` độ trễ cho mỗi ký tự, ta tính toán hoàn chỉnh ma trận DFA `(độ_dài_mẫu) x 4`. Trạng thái chuyển đổi ngay lập tức dựa trên DFA đã tính. Chữ cái được ánh xạ thành `0..3`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<Integer> searchDNA(String dnaText, String pattern) {
        List<Integer> res = new ArrayList<>();
        int m = pattern.length(), n = dnaText.length();
        if (m == 0) return res;
        
        Map<Character, Integer> charMap = new HashMap<>();
        charMap.put('A', 0); charMap.put('C', 1);
        charMap.put('G', 2); charMap.put('T', 3);
        
        // Tính mảng DFA kích thước M x 4
        int[][] dfa = new int[m][4];
        dfa[0][charMap.get(pattern.charAt(0))] = 1;
        
        int x = 0; // Trạng thái fallback (tương tự LPS)
        for (int j = 1; j < m; j++) {
            for (int c = 0; c < 4; c++) {
                dfa[j][c] = dfa[x][c]; // Copy lỗi từ trạng thái fallback
            }
            dfa[j][charMap.get(pattern.charAt(j))] = j + 1; // Khớp thành công
            x = dfa[x][charMap.get(pattern.charAt(j))]; // Cập nhật fallback
        }
        
        // Quá trình tìm kiếm không bao giờ quay lui
        int state = 0;
        for (int i = 0; i < n; i++) {
            char c = dnaText.charAt(i);
            if (!charMap.containsKey(c)) {
                state = 0; // Reset nếu gặp ký tự lạ
                continue;
            }
            state = dfa[state][charMap.get(c)];
            if (state == m) {
                res.add(i - m + 1);
                state = x; // Reset về trạng thái fallback
            }
        }
        
        return res;
    }
}
```

### Độ phức tạp
- Thời gian: Tiền xử lý `O(M * 4)`, tìm kiếm `O(N)` hoàn toàn không quay lui (độ trễ tuyệt đối `O(1)` cho mỗi ký tự).
- Không gian: `O(M * 4)` cho DFA.
