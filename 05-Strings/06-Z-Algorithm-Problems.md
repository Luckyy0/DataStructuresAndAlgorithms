# Khóa học Cấu trúc Dữ liệu và Thuật toán - Bài tập Z Algorithm

Tài liệu này cung cấp 30 bài tập về thuật toán Z (Z-Algorithm), từ cơ bản đến nâng cao. 10 bài đầu tiên bao gồm Đề bài chi tiết, Phân tích thuật toán, Mã nguồn Java 21 và Độ phức tạp.

## 1. Find All Occurrences of Pattern (Z-Algorithm)

### Đề bài chi tiết
Cho hai chuỗi văn bản `text` và `pattern`. Tìm tất cả các chỉ số bắt đầu của `pattern` trong `text` bằng thuật toán Z. Trả về danh sách các chỉ số.

### Phân tích thuật toán
Sử dụng thuật toán Z trên chuỗi gộp `S = pattern + "$" + text`. Biến `$` là ký tự đặc biệt không xuất hiện trong `text` và `pattern`. Mảng Z được tính trên chuỗi `S`. Với mỗi chỉ số `i > pattern.length()`, nếu `Z[i] == pattern.length()`, ta tìm thấy một lần xuất hiện của mẫu. Vị trí trong chuỗi gốc `text` sẽ là `i - pattern.length() - 1`.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class FindAllOccurrences {
    public static List<Integer> search(String text, String pattern) {
        List<Integer> result = new ArrayList<>();
        if (pattern.isEmpty() || text.isEmpty()) return result;
        
        String s = pattern + "$" + text;
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
        }
        
        int pLen = pattern.length();
        for (int i = pLen + 1; i < n; i++) {
            if (z[i] == pLen) {
                result.add(i - pLen - 1);
            }
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n + m)$ với $n$ là chiều dài `text`, $m$ là chiều dài `pattern`.
- **Không gian (Space):** $O(n + m)$ do tạo chuỗi nối và mảng Z.

---

## 2. Longest Happy Prefix (Z-Algorithm)

### Đề bài chi tiết
Một chuỗi được gọi là "happy prefix" nếu nó là một tiền tố không rỗng đồng thời cũng là hậu tố của chuỗi đó (không tính chính toàn bộ chuỗi). Trả về happy prefix dài nhất của chuỗi s.

### Phân tích thuật toán
Thay vì sử dụng mảng LPS như KMP, ta có thể dùng thuật toán Z. Ta tính mảng Z cho chuỗi $S$. Để một chuỗi con bắt đầu từ vị trí $i$ vừa là tiền tố vừa là hậu tố, vị trí đó cộng với $Z[i]$ phải chính xác bằng kích thước của chuỗi $S$, tức là $i + Z[i] = n$. Khi điều kiện này thỏa mãn, tiền tố/hậu tố có độ dài $Z[i]$. Ta tìm $Z[i]$ lớn nhất thỏa mãn điều kiện này.

### Mã nguồn Java
```java
public class LongestHappyPrefix {
    public String longestPrefix(String s) {
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        int maxLen = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            // Kiểm tra xem phần khớp có kéo dài đến cuối chuỗi không
            if (i + z[i] == n) {
                maxLen = Math.max(maxLen, z[i]);
            }
        }
        
        return s.substring(0, maxLen);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$.
- **Không gian (Space):** $O(n)$ cho mảng Z.

---

## 3. Sum of String Lengths of Longest Common Prefixes

### Đề bài chi tiết
Tính tổng độ dài của tiền tố chung dài nhất (LCP) giữa chuỗi $S$ và tất cả các hậu tố của nó. (Tương đương LeetCode 2223: Sum of Scores of Built Strings).

### Phân tích thuật toán
Giá trị $Z[i]$ theo định nghĩa chính là độ dài của tiền tố chung dài nhất giữa chuỗi $S$ và phần hậu tố bắt đầu từ chỉ số $i$. Do đó, bài toán đơn giản yêu cầu ta tính mảng Z của toàn bộ chuỗi $S$, sau đó tính tổng tất cả các phần tử $Z[i]$ (với $i$ từ $1$ đến $n-1$). Đừng quên cộng thêm độ dài của toàn bộ chuỗi cho hậu tố bắt đầu từ $i=0$ (chính nó).

### Mã nguồn Java
```java
public class SumOfScores {
    public long sumScores(String s) {
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        long sum = n; // Hậu tố bắt đầu tại i=0 bằng chính s
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            sum += z[i];
        }
        return sum;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$.
- **Không gian (Space):** $O(n)$.

---

## 4. Shortest Palindrome (Z-Algorithm)

### Đề bài chi tiết
Cho một chuỗi `s`. Bạn có thể thêm các ký tự vào ĐẦU chuỗi để biến nó thành một chuỗi Palindrome (chuỗi đối xứng). Yêu cầu tìm chuỗi Palindrome ngắn nhất có thể tạo ra.

### Phân tích thuật toán
Bài toán có thể hiểu là tìm chuỗi tiền tố (prefix) dài nhất của `s` mà bản thân nó là palindrome. Khi đã tìm được tiền tố này, ta chỉ cần lấy phần còn lại (hậu tố) của `s`, đảo ngược nó và nối vào đầu `s`. Ta có thể giải bằng cách nối chuỗi: `concat = s + "#" + reverse(s)`. Tính mảng Z cho chuỗi `concat`. Ta tìm phần tử $Z[i]$ lớn nhất sao cho $i + Z[i] = concat.length()$. Đó chính là độ dài palindrome prefix dài nhất.

### Mã nguồn Java
```java
public class ShortestPalindrome {
    public String shortestPalindrome(String s) {
        if (s == null || s.isEmpty()) return "";
        
        String rev = new StringBuilder(s).reverse().toString();
        String concat = s + "#" + rev;
        int n = concat.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        int longestPalinPrefix = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && concat.charAt(z[i]) == concat.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            // i >= s.length() + 1 nghĩa là đang ở phần reverse string
            if (i >= s.length() + 1 && i + z[i] == n) {
                longestPalinPrefix = Math.max(longestPalinPrefix, z[i]);
            }
        }
        
        String suffixToAdd = s.substring(longestPalinPrefix);
        return new StringBuilder(suffixToAdd).reverse().toString() + s;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$ với $n$ là chiều dài `s`.
- **Không gian (Space):** $O(n)$ do tạo các chuỗi trung gian và mảng Z.

---

## 5. Repeated Substring Pattern

### Đề bài chi tiết
Kiểm tra xem một chuỗi `s` có thể được tạo thành bằng cách lấy một chuỗi con và lặp lại nó nhiều lần hay không. (VD: "abab" -> true, "aba" -> false).

### Phân tích thuật toán
Ta tính mảng Z của chuỗi `s`. Một chuỗi được tạo bởi chuỗi con lặp lại nếu tồn tại một vị trí $i$ sao cho: độ dài chuỗi $n$ chia hết cho $i$ (tức là $i$ là độ dài của đoạn lặp), và $i + Z[i] == n$. Nếu có tồn tại bất kỳ $i$ nào như vậy, `s` là Repeated Substring Pattern.

### Mã nguồn Java
```java
public class RepeatedSubstringPattern {
    public boolean repeatedSubstringPattern(String s) {
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            if (n % i == 0 && i + z[i] == n) {
                return true;
            }
        }
        
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$.
- **Không gian (Space):** $O(n)$ để lưu trữ mảng Z.

---

## 6. Match Substring After Replacement

### Đề bài chi tiết
Bạn có hai chuỗi `s` và `sub`. Bạn cũng có một tập hợp các quy tắc thay thế (mảng hai chiều `mappings`). Xác định xem chuỗi `sub` có thể trở thành chuỗi con của `s` sau khi áp dụng bất kỳ thay thế nào không. Bằng Z-algorithm, chúng ta mô phỏng logic khớp mẫu mở rộng.

### Phân tích thuật toán
Vì có mapping (có thể A thành B, nhưng không ngược lại), Z-Algorithm truyền thống không áp dụng được trực tiếp do tính đối xứng của hàm kiểm tra `==`. Tuy nhiên, ta có thể dùng một dạng biến thể: Ta xây dựng bảng `boolean` hỗ trợ `isMatch(char, char)` và chạy một Z-algorithm được tinh chỉnh, hoặc trực tiếp kiểm tra từng vị trí `i` trên `s` nhưng dùng kỹ thuật Two-pointer (trượt) tương tự. Để tối ưu với chuỗi dài, dùng mảng bù trừ. (Dưới đây triển khai cách kiểm tra tối ưu mảng 2D ánh xạ cùng với ý tưởng trượt).

### Mã nguồn Java
```java
public class MatchSubstringAfterReplacement {
    public boolean matchReplacement(String s, String sub, char[][] mappings) {
        boolean[][] match = new boolean[256][256];
        for (int i = 0; i < 256; i++) match[i][i] = true;
        for (char[] m : mappings) {
            match[m[0]][m[1]] = true;
        }
        
        int n = s.length(), m = sub.length();
        for (int i = 0; i <= n - m; i++) {
            boolean valid = true;
            for (int j = 0; j < m; j++) {
                if (!match[sub.charAt(j)][s.charAt(i + j)]) {
                    valid = false;
                    break;
                }
            }
            if (valid) return true;
        }
        return false;
    }
}
// Chú ý: Do phép so khớp thay thế không có tính bắt cầu, Z-algorithm chuẩn 
// không hỗ trợ. Giải pháp này dùng trượt cửa sổ naive kết hợp mảng O(1) Lookup.
```

### Độ phức tạp
- **Thời gian (Time):** $O(n \times m)$ tối đa, do không áp dụng được hoàn toàn thuật toán tuyến tính.
- **Không gian (Space):** $O(1)$ do mảng `boolean[256][256]` cố định.

---

## 7. Find the Index of the First Occurrence in a String (Z-Algorithm)

### Đề bài chi tiết
Cho hai chuỗi `haystack` và `needle`, trả về chỉ số xuất hiện đầu tiên của `needle` trong `haystack`, hoặc -1 nếu `needle` không thuộc `haystack`. (LeetCode 28).

### Phân tích thuật toán
Giải trực tiếp bằng thuật toán Z tương tự bài 1, nhưng ta chỉ cần trả về phần tử đầu tiên thỏa mãn `Z[i] == needle.length()`. Thay vì lưu mảng kết quả, return luôn ngay khi tìm thấy để tối ưu hóa.

### Mã nguồn Java
```java
public class FindFirstOccurrence {
    public int strStr(String haystack, String needle) {
        if (needle.isEmpty()) return 0;
        
        String s = needle + "$" + haystack;
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        int m = needle.length();
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            // Trả về ngay khi tìm thấy lần khớp đầu tiên
            if (z[i] == m) {
                return i - m - 1;
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n + m)$.
- **Không gian (Space):** $O(n + m)$.

---

## 8. Longest Prefix Suffix

### Đề bài chi tiết
Tìm độ dài của tiền tố dài nhất đồng thời cũng là hậu tố không tầm thường (không phải toàn bộ chuỗi). Giống bài 2 nhưng yêu cầu trả về độ dài thay vì chuỗi.

### Phân tích thuật toán
Sử dụng mảng Z: duyệt qua `i` từ 1 đến `n - 1`. Khi tìm thấy `i + Z[i] == n`, cập nhật kết quả. Vì ta duyệt từ trái sang phải, giá trị đầu tiên thỏa mãn cũng chính là hậu tố bắt đầu sớm nhất (có độ dài lớn nhất). Nên return `Z[i]` ngay khi thấy.

### Mã nguồn Java
```java
public class LongestPrefixSuffixLen {
    public int lps(String s) {
        int n = s.length();
        if (n == 0) return 0;
        int[] z = new int[n];
        int l = 0, r = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            // Tìm tiền tố lớn nhất đồng thời là hậu tố
            if (i + z[i] == n) {
                return z[i];
            }
        }
        return 0;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$.
- **Không gian (Space):** $O(n)$.

---

## 9. Count Prefix and Suffix Pairs

### Đề bài chi tiết
Cho mảng các chuỗi `words`. Gọi `isPrefixAndSuffix(word1, word2)` trả về true nếu `word1` vừa là tiền tố vừa là hậu tố của `word2`. Tính số cặp `(i, j)` với `i < j` sao cho `isPrefixAndSuffix(words[i], words[j])` là true.

### Phân tích thuật toán
Vì số lượng từ trong mảng có thể lên đến hàng nghìn, việc so sánh cặp tốn $O(N^2 \times L)$. Ta có thể dùng Z-Algorithm trên từng phép thử, kiểm tra `word1` có phải tiền tố/hậu tố bằng mảng Z hoặc dùng thư viện có sẵn (startsWith / endsWith) trên Java giúp ngắn gọn hơn cho độ dài nhỏ. Dưới đây là hiện thực thuật toán kiểm tra một hàm `isPrefixSuffix` thủ công dùng ý tưởng tiền tố hậu tố.

### Mã nguồn Java
```java
public class CountPrefixSuffixPairs {
    public int countPrefixSuffixPairs(String[] words) {
        int count = 0;
        int n = words.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isPrefixAndSuffix(words[i], words[j])) {
                    count++;
                }
            }
        }
        return count;
    }

    private boolean isPrefixAndSuffix(String str1, String str2) {
        int len1 = str1.length();
        int len2 = str2.length();
        if (len1 > len2) return false;
        
        // Kiểm tra prefix và suffix thủ công
        for (int i = 0; i < len1; i++) {
            if (str1.charAt(i) != str2.charAt(i)) return false;
            if (str1.charAt(i) != str2.charAt(len2 - len1 + i)) return false;
        }
        return true;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N^2 \times L)$, với $N$ là số từ và $L$ là độ dài trung bình.
- **Không gian (Space):** $O(1)$.

---

## 10. Period of a String

### Đề bài chi tiết
Tìm chu kỳ ngắn nhất $p$ của một chuỗi $S$ sao cho $S$ có thể được biểu diễn như một chuỗi con của sự lặp lại từ một chuỗi có độ dài $p$. 

### Phân tích thuật toán
Áp dụng mảng Z, duyệt `i` từ 1 đến `n-1`. Khi $i + Z[i] == n$, nghĩa là hậu tố bắt đầu từ $i$ hoàn toàn khớp với tiền tố. Khi đó $i$ chính là ứng cử viên cho độ dài chu kỳ. Chu kỳ ngắn nhất sẽ là giá trị $i$ nhỏ nhất thỏa mãn $i + z[i] == n$. (Trường hợp lặp lại hoàn toàn hoặc lặp lại có phần dư ở cuối). Nếu không có $i$ nào thỏa mãn, chu kỳ của nó chính là $n$.

### Mã nguồn Java
```java
public class PeriodOfString {
    public int findShortestPeriod(String s) {
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            if (i + z[i] == n) {
                return i;
            }
        }
        
        return n; // Không có chu kỳ nhỏ hơn độ dài chuỗi
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$.
- **Không gian (Space):** $O(n)$.

---

## 11. Rotate String

### Đề bài chi tiết
Cho hai chuỗi `s` và `goal`. Trả về `true` nếu và chỉ nếu `s` có thể trở thành `goal` sau một số thao tác dịch chuyển vòng quanh (quay). Một thao tác quay `s` bao gồm việc di chuyển ký tự đầu tiên cùng sang cuối cùng. Ví dụ: "abcde" quay 1 lần thành "bcdea".

### Phân tích thuật toán
Thuật toán Z có thể được sử dụng để giải quyết bài toán này trong thời gian tuyến tính. Đầu tiên, nếu độ dài của `s` và `goal` khác nhau, chúng không thể là vòng quay của nhau. Tiếp theo, ta tạo một chuỗi ghép `concat = goal + "$" + s + s`. Nếu `goal` là một vòng quay của `s`, thì `goal` chắc chắn sẽ xuất hiện như một chuỗi con bên trong `s + s`. Ta áp dụng thuật toán Z trên chuỗi `concat`. Nếu tồn tại bất kỳ vị trí $i$ nào sao cho $Z[i]$ bằng với độ dài của `goal`, điều đó chứng tỏ `goal` là một vòng quay của `s`.

### Mã nguồn Java
```java
public class RotateString {
    public boolean rotateString(String s, String goal) {
        if (s.length() != goal.length()) {
            return false;
        }
        if (s.isEmpty() && goal.isEmpty()) return true;
        
        String concat = goal + "$" + s + s;
        int n = concat.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        int m = goal.length();
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && concat.charAt(z[i]) == concat.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            if (z[i] == m) {
                return true;
            }
        }
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$ với $n$ là chiều dài của chuỗi `s`.
- **Không gian (Space):** $O(n)$ để lưu trữ chuỗi ghép và mảng Z.

---

## 12. K-th Occurrence of Pattern

### Đề bài chi tiết
Cho chuỗi văn bản `text`, chuỗi mẫu `pattern` và số nguyên dương `k`. Yêu cầu tìm vị trí xuất hiện lần thứ `k` của `pattern` trong `text` (vị trí tính từ 0). Nếu mẫu không xuất hiện đủ `k` lần, trả về -1.

### Phân tích thuật toán
Bài toán này mở rộng từ bài toán tìm kiếm mẫu cơ bản bằng thuật toán Z. Ta nối `S = pattern + "$" + text` và tính mảng Z cho chuỗi này. Khi duyệt qua nửa sau của mảng Z (tương ứng với các vị trí trong `text`), mỗi khi gặp $Z[i] == pattern.length()$, ta đếm số lần xuất hiện lên 1. Khi đếm đủ `k` lần, ta trả về chỉ số bắt đầu thực sự trong `text` là `i - pattern.length() - 1`.

### Mã nguồn Java
```java
public class KthOccurrenceOfPattern {
    public int findKthOccurrence(String text, String pattern, int k) {
        if (pattern.isEmpty() || k <= 0) return -1;
        
        String s = pattern + "$" + text;
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        int m = pattern.length();
        int count = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            if (z[i] == m) {
                count++;
                if (count == k) {
                    return i - m - 1;
                }
            }
        }
        return -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n + m)$ với $n$ là chiều dài `text`, $m$ là chiều dài `pattern`.
- **Không gian (Space):** $O(n + m)$.

---

## 13. Check if String is a Prefix of Array

### Đề bài chi tiết
Cho một chuỗi `s` và một mảng các chuỗi `words`. Trả về `true` nếu `s` là tiền tố của mảng `words`. Chuỗi `s` được gọi là tiền tố của mảng `words` nếu nó có thể được tạo thành bằng cách nối $k$ chuỗi đầu tiên của `words` lại với nhau, với $k$ nằm trong khoảng từ 1 đến `words.length`.

### Phân tích thuật toán
Bài toán không đòi hỏi thuật toán tìm kiếm phức tạp như Z-Algorithm vì ta chỉ việc nối dần các từ trong `words` lại. Tuy nhiên, ở góc độ so khớp chuỗi, ta đang kiểm tra xem chuỗi nối được có bằng đúng chuỗi `s` hay không. Ta lần lượt nối từng từ vào một biến `StringBuilder`. Tại mỗi bước nối, nếu độ dài vượt qua `s` hoặc chuỗi tạo thành không khớp với tiền tố của `s`, ta có thể kết luận sớm. Nếu bằng chính xác `s`, ta trả về `true`.

### Mã nguồn Java
```java
public class StringPrefixOfArray {
    public boolean isPrefixString(String s, String[] words) {
        StringBuilder sb = new StringBuilder();
        for (String word : words) {
            sb.append(word);
            // Nếu độ dài đã tạo vượt quá độ dài s
            if (sb.length() > s.length()) {
                return false;
            }
            // Nếu bằng chính xác độ dài s, kiểm tra nội dung
            if (sb.length() == s.length()) {
                return sb.toString().equals(s);
            }
            // Nếu chuỗi đang xây dựng không phải là tiền tố của s, dừng luôn
            if (!s.startsWith(sb.toString())) {
                return false;
            }
        }
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$ với $n$ là độ dài của `s`.
- **Không gian (Space):** $O(n)$ để xây dựng chuỗi.

---

## 14. Count Substrings That Differ by One Character

### Đề bài chi tiết
Cho hai chuỗi `s` và `t`. Trả về số lượng các cặp chuỗi con của `s` và `t` (có cùng độ dài) khác nhau đúng một ký tự. Ví dụ: "aba" và "baba" có một số chuỗi con có thể tạo thành các cặp khác nhau một ký tự.

### Phân tích thuật toán
Sử dụng phương pháp trượt cửa sổ hoặc duyệt trực tiếp. Với mỗi vị trí bắt đầu $i$ trong $s$ và $j$ trong $t$, ta kéo dài chuỗi con về phía bên phải. Tại mỗi bước mở rộng, ta đếm số lượng ký tự khác biệt. Khi sự khác biệt bằng 1, đó là một chuỗi hợp lệ, ta cộng vào kết quả. Nếu sự khác biệt lớn hơn 1, ta dừng lại việc mở rộng đoạn đó. Thuật toán này đủ tốt cho các chuỗi ngắn (như giới hạn LeetCode là 100).

### Mã nguồn Java
```java
public class CountSubstringsDifferByOne {
    public int countSubstrings(String s, String t) {
        int count = 0;
        int ns = s.length(), nt = t.length();
        
        for (int i = 0; i < ns; i++) {
            for (int j = 0; j < nt; j++) {
                int diff = 0;
                // Mở rộng chuỗi con song song trên s và t
                for (int k = 0; i + k < ns && j + k < nt; k++) {
                    if (s.charAt(i + k) != t.charAt(j + k)) {
                        diff++;
                    }
                    if (diff == 1) {
                        count++;
                    } else if (diff > 1) {
                        break;
                    }
                }
            }
        }
        return count;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(S \times T \times \min(S, T))$ trong đó $S$ và $T$ là độ dài của 2 chuỗi.
- **Không gian (Space):** $O(1)$ vì chỉ dùng biến đếm cơ bản.

---

## 15. Minimum Number of Moves to Make Palindrome

### Đề bài chi tiết
Cho một chuỗi `s` có thể được chuyển thành một chuỗi đối xứng (palindrome) bằng cách thực hiện số lần đổi chỗ (hoán đổi) hai ký tự kề nhau một số lần. Tìm số lần đổi chỗ tối thiểu để tạo thành palindrome. Dữ liệu đảm bảo `s` có thể biến đổi thành palindrome.

### Phân tích thuật toán
Bài toán sử dụng kỹ thuật Two-Pointers (hai con trỏ) kết hợp chiến lược tham lam (Greedy). Ta giữ hai con trỏ `left` và `right` ở hai đầu chuỗi. Nếu ký tự ở `left` khác với `right`, ta tìm ký tự giống `left` nằm ở gần `right` nhất và đưa nó về `right` bằng cách hoán đổi kề nhau. Nếu không tìm thấy (ký tự ở `left` là ký tự đơn lẻ nằm giữa palindrome), ta đổi nó vào giữa. Cứ như vậy, lặp lại cho đến khi `left >= right`. Quá trình này giúp tối thiểu hóa số lần di chuyển.

### Mã nguồn Java
```java
public class MinMovesToPalindrome {
    public int minMovesToMakePalindrome(String s) {
        int n = s.length();
        char[] arr = s.toCharArray();
        int moves = 0;
        int left = 0, right = n - 1;
        
        while (left < right) {
            if (arr[left] == arr[right]) {
                left++;
                right--;
                continue;
            }
            
            int k = right;
            // Tìm ký tự giống arr[left] từ right lùi về left
            while (k > left && arr[k] != arr[left]) {
                k--;
            }
            
            if (k == left) {
                // Đây là ký tự duy nhất sẽ nằm ở giữa
                // Đẩy nó vào giữa (chỉ cần hoán đổi một bước với k kề bên)
                char temp = arr[left];
                arr[left] = arr[left + 1];
                arr[left + 1] = temp;
                moves++;
            } else {
                // Di chuyển ký tự tìm được về vị trí right
                while (k < right) {
                    char temp = arr[k];
                    arr[k] = arr[k + 1];
                    arr[k + 1] = temp;
                    k++;
                    moves++;
                }
                left++;
                right--;
            }
        }
        return moves;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n^2)$ do phải di chuyển các ký tự.
- **Không gian (Space):** $O(n)$ lưu mảng ký tự.

---

## 16. Palindromic Substrings

### Đề bài chi tiết
Cho một chuỗi `s`, hãy đếm tổng số lượng chuỗi con đối xứng (palindromic substrings) có trong `s`. Một chuỗi được gọi là đối xứng nếu đọc từ trái sang phải hay từ phải sang trái đều giống nhau. (Ví dụ: "abc" có 3 chuỗi con đối xứng là "a", "b", "c").

### Phân tích thuật toán
Ta có thể giải bài này bằng cách mở rộng từ giữa (Expand Around Center). Ở mỗi vị trí ký tự, nó có thể là tâm của một chuỗi đối xứng có độ dài lẻ, hoặc là tâm cùng với ký tự kế bên cho một chuỗi đối xứng có độ dài chẵn. Bằng cách cố gắng mở rộng ra 2 bên tại mọi tâm có thể, ta đếm được tất cả các chuỗi con đối xứng. Kỹ thuật này duyệt chuỗi tuyến tính nhưng quá trình mở rộng lồng nhau, thời gian tối đa là $O(n^2)$. Thuật toán Manacher có thể giảm xuống $O(n)$ nhưng Expand Around Center dễ thực hiện và phổ biến hơn.

### Mã nguồn Java
```java
public class PalindromicSubstrings {
    public int countSubstrings(String s) {
        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            // Mở rộng với tâm là 1 ký tự (chuỗi dài lẻ)
            count += expandAroundCenter(s, i, i);
            // Mở rộng với tâm là 2 ký tự (chuỗi dài chẵn)
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

### Độ phức tạp
- **Thời gian (Time):** $O(n^2)$ trong đó $n$ là chiều dài của chuỗi.
- **Không gian (Space):** $O(1)$.

---

## 17. String Matching in an Array

### Đề bài chi tiết
Cho một mảng các chuỗi `words`. Trả về tất cả các chuỗi trong `words` là chuỗi con (substring) của một chuỗi khác trong mảng. Kết quả có thể trả về theo bất kỳ thứ tự nào.

### Phân tích thuật toán
Cách tiếp cận đơn giản và hiệu quả nhất cho độ dài mảng và chuỗi ngắn là duyệt qua mọi cặp chuỗi $(i, j)$ với $i \neq j$. Ta kiểm tra xem `words[i]` có nằm trong `words[j]` hay không (bằng phương thức `contains()`). Nếu có, ta đưa `words[i]` vào danh sách kết quả và tránh trùng lặp bằng cách ngắt vòng lặp sớm khi đã tìm thấy `words[i]` thỏa mãn.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class StringMatchingInArray {
    public List<String> stringMatching(String[] words) {
        List<String> result = new ArrayList<>();
        int n = words.length;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i != j && words[j].contains(words[i])) {
                    result.add(words[i]);
                    break; // Không cần kiểm tra thêm cho words[i]
                }
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N^2 \times L^2)$ với $N$ là số lượng chuỗi, $L$ là độ dài tối đa của chuỗi (giả sử `contains` tốn $O(L^2)$).
- **Không gian (Space):** $O(N)$ để lưu danh sách kết quả.

---

## 18. Make String a Subsequence Using Cyclic Increments

### Đề bài chi tiết
Cho hai chuỗi `str1` và `str2`. Ta có thể chọn một tập hợp các chỉ số trong `str1` và dịch chuyển ký tự tại các chỉ số đó lên 1 bậc theo vòng tròn (ví dụ 'a' thành 'b', 'z' thành 'a'). Một ký tự chỉ được dịch chuyển tối đa 1 lần. Xác định xem `str2` có thể trở thành một dãy con (subsequence) của `str1` hay không.

### Phân tích thuật toán
Sử dụng Two-Pointers để theo dõi chỉ số `i` của `str1` và `j` của `str2`. Ký tự `str1[i]` có thể khớp với `str2[j]` theo hai cách: nó bằng đúng `str2[j]`, hoặc sau khi dịch chuyển 1 bậc nó bằng `str2[j]`. Nếu khớp, ta chuyển sang ký tự tiếp theo trong cả hai chuỗi. Nếu không khớp, ta chỉ tiến con trỏ `i` trên `str1` để tìm kiếm sự phù hợp khác. Cuối cùng, nếu `j` duyệt hết `str2`, điều đó chứng tỏ `str2` có thể trở thành dãy con.

### Mã nguồn Java
```java
public class MakeSubsequenceCyclic {
    public boolean canMakeSubsequence(String str1, String str2) {
        int i = 0, j = 0;
        int n = str1.length(), m = str2.length();
        
        while (i < n && j < m) {
            char c1 = str1.charAt(i);
            char c2 = str2.charAt(j);
            
            // Ký tự dịch vòng quanh 1 bậc
            char nextC1 = (c1 == 'z') ? 'a' : (char)(c1 + 1);
            
            if (c1 == c2 || nextC1 == c2) {
                j++; // Khớp được ký tự trong str2
            }
            i++; // Luôn duyệt tiến str1
        }
        
        return j == m; // Đã tìm thấy toàn bộ str2
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$ với $n$ là chiều dài của `str1`.
- **Không gian (Space):** $O(1)$.

---

## 19. Longest Common Prefix of Two Strings

### Đề bài chi tiết
Cho hai chuỗi `s1` và `s2`. Tìm độ dài tiền tố chung dài nhất (Longest Common Prefix) của chúng. (Lưu ý: Không phải chuỗi con, mà phải bắt đầu ngay từ vị trí đầu tiên của cả 2 chuỗi).

### Phân tích thuật toán
Bài toán đơn giản: ta chỉ cần lặp đồng thời qua các ký tự của `s1` và `s2` từ chỉ số 0, đếm số lượng ký tự giống nhau liên tiếp cho đến khi gặp ký tự khác biệt hoặc hết chuỗi. Điều này đạt được độ phức tạp tối ưu và không gian $O(1)$. Có thể dùng mảng Z cho chuỗi ghép nhưng phức tạp hơn mức cần thiết.

### Mã nguồn Java
```java
public class LongestCommonPrefixTwoStrings {
    public int longestCommonPrefix(String s1, String s2) {
        int len = Math.min(s1.length(), s2.length());
        int i = 0;
        while (i < len && s1.charAt(i) == s2.charAt(i)) {
            i++;
        }
        return i;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(\min(n, m))$ với $n, m$ là chiều dài hai chuỗi.
- **Không gian (Space):** $O(1)$.

---

## 20. Substring with Largest Variance

### Đề bài chi tiết
Cho chuỗi `s` gồm các chữ cái tiếng Anh in thường. Variance (phương sai) của một chuỗi là độ chênh lệch lớn nhất giữa số lần xuất hiện của một ký tự xuất hiện nhiều nhất và ký tự xuất hiện ít nhất. Tìm variance lớn nhất trong tất cả các chuỗi con của `s`.

### Phân tích thuật toán
Duyệt qua tất cả các cặp ký tự $(a, b)$ với $a \neq b$. Coi sự xuất hiện của $a$ là $+1$ và $b$ là $-1$. Vấn đề chuyển về việc tìm dãy con liên tiếp có tổng lớn nhất chứa ít nhất một phần tử $-1$. Ta áp dụng thuật toán tương tự Kadane's Algorithm. Biến `majorCount` đếm số lượng $a$, `minorCount` đếm số lượng $b$. Nếu `minorCount > majorCount`, ta có thể reset nhưng chỉ khi vẫn còn ký tự $b$ ở phía sau (`restMinor > 0`), để đảm bảo tìm được chuỗi hợp lệ.

### Mã nguồn Java
```java
public class LargestVariance {
    public int largestVariance(String s) {
        int maxVar = 0;
        int[] freq = new int[26];
        for (char c : s.toCharArray()) freq[c - 'a']++;
        
        // Duyệt mọi cặp ký tự (a, b)
        for (int i = 0; i < 26; i++) {
            for (int j = 0; j < 26; j++) {
                if (i == j || freq[i] == 0 || freq[j] == 0) continue;
                
                char major = (char) (i + 'a');
                char minor = (char) (j + 'a');
                
                int majorCount = 0;
                int minorCount = 0;
                int restMinor = freq[j];
                
                for (char c : s.toCharArray()) {
                    if (c == major) majorCount++;
                    if (c == minor) {
                        minorCount++;
                        restMinor--;
                    }
                    
                    if (minorCount > 0) {
                        maxVar = Math.max(maxVar, majorCount - minorCount);
                    }
                    
                    // Reset nếu minor > major và vẫn còn minor phía trước
                    if (minorCount > majorCount && restMinor > 0) {
                        majorCount = 0;
                        minorCount = 0;
                    }
                }
            }
        }
        return maxVar;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(26 \times 26 \times n) \approx O(n)$, với $n$ là chiều dài chuỗi.
- **Không gian (Space):** $O(1)$ cho mảng tần suất cố định 26 ký tự.

---

## 21. Maximum Deletions on a String

### Đề bài chi tiết
Cho chuỗi `s`. Ở mỗi bước, bạn có thể xóa một tiền tố của chuỗi `s` có độ dài `i` nếu tiền tố đó xuất hiện lặp lại ngay lập tức sau nó trong chuỗi (tức là `s[0..i-1] == s[i..2i-1]`). Nếu không có `i` nào thỏa mãn, bạn có thể xóa toàn bộ chuỗi còn lại trong một thao tác. Tìm số lượng thao tác xóa tối đa để xóa hết chuỗi `s`.

### Phân tích thuật toán
Sử dụng Quy hoạch động (Dynamic Programming) kết hợp với mảng LCP (Longest Common Prefix). `dp[i]` là số thao tác xóa tối đa cho hậu tố bắt đầu từ chỉ số `i`. Ta tính bảng `lcp[i][j]` để kiểm tra nhanh chuỗi con. `lcp[i][j]` là độ dài tiền tố chung của `s[i...]` và `s[j...]`. Nếu `lcp[i][i + len] >= len`, nghĩa là tiền tố độ dài `len` lặp lại. Lúc này `dp[i] = Math.max(dp[i], 1 + dp[i + len])`. 

### Mã nguồn Java
```java
public class MaximumDeletions {
    public int deleteString(String s) {
        int n = s.length();
        int[][] lcp = new int[n + 1][n + 1];
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                if (s.charAt(i) == s.charAt(j)) {
                    lcp[i][j] = 1 + lcp[i + 1][j + 1];
                }
            }
        }
        
        int[] dp = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = 1; // Thao tác xóa toàn bộ phần còn lại
            for (int len = 1; i + 2 * len <= n; len++) {
                if (lcp[i][i + len] >= len) {
                    dp[i] = Math.max(dp[i], 1 + dp[i + len]);
                }
            }
        }
        return dp[0];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n^2)$ cho việc xây dựng bảng LCP và tính toán DP.
- **Không gian (Space):** $O(n^2)$ cho mảng 2D `lcp`. Có thể tối ưu bộ nhớ nhưng $O(n^2)$ là chấp nhận được với độ dài giới hạn 4000.

---

## 22. Minimum Time to Revert Word to Initial State

### Đề bài chi tiết
Cho một chuỗi `word` và một số nguyên `k`. Tại mỗi giây, bạn bắt buộc phải loại bỏ `k` ký tự đầu tiên của chuỗi, sau đó nối thêm `k` ký tự bất kỳ vào cuối chuỗi. Trả về số giây tối thiểu (lớn hơn 0) để chuỗi trở về trạng thái ban đầu của nó.

### Phân tích thuật toán
Bản chất của bài toán là tìm thời điểm `t` sao cho sau khi cắt đi `t * k` ký tự đầu tiên, phần còn lại của chuỗi (là một hậu tố của `word`) hoàn toàn khớp với tiền tố của `word` có cùng độ dài. Đây chính là mục đích cốt lõi của thuật toán Z. Ta tính mảng Z của `word`. Tiếp theo, duyệt qua các khoảng thời gian `i` là bội số của `k` (tức là $i = k, 2k, 3k...$). Nếu tại một vị trí $i$, giá trị $Z[i]$ đúng bằng độ dài của hậu tố còn lại ($Z[i] == n - i$), điều đó có nghĩa hậu tố này khớp với tiền tố của chuỗi gốc, và chuỗi có thể được lấp đầy lại ở đuôi để trở về ban đầu. Trả về $i / k$. Nếu duyệt qua toàn bộ mà không khớp, trả về phép chia làm tròn lên của $n / k$.

### Mã nguồn Java
```java
public class MinimumTimeToRevert {
    public int minimumTimeToInitialState(String word, int k) {
        int n = word.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && word.charAt(z[i]) == word.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
        }
        
        for (int i = k; i < n; i += k) {
            if (z[i] == n - i) {
                return i / k;
            }
        }
        
        return (n + k - 1) / k; // Tương đương Math.ceil((double)n / k)
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n)$ do mảng Z tính trong tuyến tính.
- **Không gian (Space):** $O(n)$ lưu mảng Z.

---

## 23. Longest Prefix which is also a Substring

### Đề bài chi tiết
Cho hai chuỗi `s1` và `s2`. Tìm độ dài lớn nhất của một tiền tố của chuỗi `s1` sao cho tiền tố này cũng xuất hiện dưới dạng một chuỗi con bên trong `s2`.

### Phân tích thuật toán
Ta có thể áp dụng thuật toán Z bằng cách nối hai chuỗi với nhau: `S = s1 + "$" + s2`. Mảng Z được tính trên toàn bộ chuỗi này. Bất kỳ giá trị $Z[i]$ nào với $i > s1.length()$ đều thể hiện độ dài của chuỗi con trong `s2` (bắt đầu tại $i$) đồng thời là tiền tố của `s1`. Ta chỉ cần tìm giá trị $Z[i]$ lớn nhất trong đoạn phần hậu tố ứng với `s2`. 

### Mã nguồn Java
```java
public class LongestPrefixSubstring {
    public int maxPrefixLength(String s1, String s2) {
        if (s1.isEmpty() || s2.isEmpty()) return 0;
        
        String s = s1 + "$" + s2;
        int n = s.length();
        int[] z = new int[n];
        int l = 0, r = 0;
        
        int maxLen = 0;
        int s1Len = s1.length();
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            while (i + z[i] < n && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
            
            if (i > s1Len) { // Đang ở vị trí của s2
                maxLen = Math.max(maxLen, z[i]);
            }
        }
        
        return maxLen;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n + m)$ với $n$ là chiều dài `s1`, $m$ là chiều dài `s2`.
- **Không gian (Space):** $O(n + m)$.

---

## 24. Find Beautiful Indices in the Given Array

### Đề bài chi tiết
Cho chuỗi `s`, hai mẫu `a` và `b`, và số nguyên `k`. Một chỉ số `i` là "beautiful" nếu chuỗi con của `s` bắt đầu từ `i` khớp với `a`, và tồn tại một chỉ số `j` sao cho chuỗi con của `s` bắt đầu từ `j` khớp với `b`, với khoảng cách `|j - i| <= k`. Trả về danh sách tất cả các chỉ số beautiful đã được sắp xếp tăng dần.

### Phân tích thuật toán
Bước 1: Tìm tất cả các chỉ số xuất hiện của chuỗi `a` trong `s`. Lưu vào danh sách `indicesA`. Ta có thể dùng thuật toán Z hoặc KMP, hay đơn giản là tìm tuyến tính.
Bước 2: Tìm tất cả các chỉ số xuất hiện của chuỗi `b` trong `s`. Lưu vào danh sách `indicesB`.
Bước 3: Với mỗi `i` trong `indicesA`, ta kiểm tra xem có `j` nào trong `indicesB` thỏa mãn `|j - i| <= k` không. Do `indicesB` đã được sắp xếp, ta có thể dùng tìm kiếm nhị phân (Binary Search) hoặc kỹ thuật Two Pointers để rà soát nhanh chóng thay vì vòng lặp $O(A \times B)$.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class BeautifulIndices {
    public List<Integer> beautifulIndices(String s, String a, String b, int k) {
        List<Integer> indicesA = getIndices(s, a);
        List<Integer> indicesB = getIndices(s, b);
        List<Integer> result = new ArrayList<>();
        
        int ptrB = 0;
        for (int i : indicesA) {
            // Đẩy con trỏ B lên cho đến khi nó vào vùng hợp lệ hoặc vượt qua
            while (ptrB < indicesB.size() && indicesB.get(ptrB) < i - k) {
                ptrB++;
            }
            
            // Nếu con trỏ hiện tại nằm trong khoảng [i - k, i + k]
            if (ptrB < indicesB.size() && Math.abs(indicesB.get(ptrB) - i) <= k) {
                result.add(i);
            }
        }
        return result;
    }
    
    // Tìm các vị trí mẫu xuất hiện bằng các hàm tiêu chuẩn (có thể tối ưu Z-algo)
    private List<Integer> getIndices(String s, String pattern) {
        List<Integer> res = new ArrayList<>();
        int pLen = pattern.length();
        for (int i = 0; i <= s.length() - pLen; i++) {
            if (s.startsWith(pattern, i)) {
                res.add(i);
            }
        }
        return res;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(S \times (A + B))$ cho tìm kiếm chuỗi con (nếu dùng KMP/Z thì là $O(S)$). Bước Two Pointers tốn $O(N_A + N_B)$.
- **Không gian (Space):** $O(S)$ để lưu danh sách chỉ số.

---

## 25. Prefix and Suffix Search

### Đề bài chi tiết
Thiết kế một lớp `WordFilter` được khởi tạo bằng một từ điển các chuỗi `words`. Cung cấp phương thức `f(String pref, String suff)` trả về chỉ số của chuỗi trong từ điển có tiền tố là `pref` và hậu tố là `suff`. Nếu có nhiều chuỗi hợp lệ, trả về chỉ số lớn nhất. Nếu không có, trả về -1.

### Phân tích thuật toán
Lưu tất cả các kết hợp của hậu tố và tiền tố vào cấu trúc dữ liệu Trie. Với một từ `word`, ta sinh ra các chuỗi có dạng `suffix + "{" + word` đối với mọi hậu tố có thể của `word`. Ta chèn chuỗi này vào Trie và gán cho nút cuối giá trị là `index` (chỉ số của từ trong mảng). Khi truy vấn, ta tìm trong Trie với khóa là `suff + "{" + pref`. Chỉ số lưu trong nút đó chính là đáp án cần tìm (nó sẽ tự ghi đè lên các từ trước đó vì ta chèn các từ theo thứ tự chỉ số tăng dần).

### Mã nguồn Java
```java
class WordFilter {
    class TrieNode {
        TrieNode[] children = new TrieNode[27]; // 26 chữ cái + '{'
        int weight = -1;
    }
    
    private TrieNode root;

    public WordFilter(String[] words) {
        root = new TrieNode();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            int len = word.length();
            // Sinh tất cả các suffix + "{" + word
            for (int j = 0; j <= len; j++) {
                String key = word.substring(j) + "{" + word;
                insert(key, i);
            }
        }
    }
    
    private void insert(String key, int weight) {
        TrieNode curr = root;
        for (char c : key.toCharArray()) {
            int idx = c == '{' ? 26 : c - 'a';
            if (curr.children[idx] == null) {
                curr.children[idx] = new TrieNode();
            }
            curr = curr.children[idx];
            curr.weight = weight; // Cập nhật weight lớn nhất (chỉ số muộn nhất)
        }
    }
    
    public int f(String pref, String suff) {
        TrieNode curr = root;
        String searchKey = suff + "{" + pref;
        for (char c : searchKey.toCharArray()) {
            int idx = c == '{' ? 26 : c - 'a';
            if (curr.children[idx] == null) {
                return -1;
            }
            curr = curr.children[idx];
        }
        return curr.weight;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** Khởi tạo $O(N \times L^2)$ với $N$ là số từ và $L$ là độ dài từ lớn nhất. Truy vấn mất $O(L_{pref} + L_{suff})$.
- **Không gian (Space):** $O(N \times L^2)$ để lưu trữ toàn bộ các trạng thái trong Trie.

---

## 26. Count Palindromic Subsequences

### Đề bài chi tiết
Đếm tổng số lượng chuỗi con không nhất thiết liên tiếp (subsequences) đối xứng trong một chuỗi `s`. Do đáp án có thể rất lớn, trả về kết quả theo modulo $10^9 + 7$. Các chuỗi con được xem là khác nhau nếu các ký tự được chọn từ những vị trí khác nhau trong chuỗi gốc, ngay cả khi chúng tạo ra cùng một giá trị chuỗi.

### Phân tích thuật toán
Giải bằng Quy hoạch động (DP). Định nghĩa mảng 2D `dp[i][j]` là số chuỗi con đối xứng trong chuỗi `s` từ chỉ số `i` đến `j`. 
- Khi `s[i] != s[j]`, số lượng bằng chuỗi xét không có `s[i]` cộng chuỗi xét không có `s[j]` và trừ đi phần giao: `dp[i][j] = dp[i+1][j] + dp[i][j-1] - dp[i+1][j-1]`.
- Khi `s[i] == s[j]`, ngoài phần giao như trên, ký tự `s[i]` và `s[j]` tạo thêm chuỗi mới bằng cách bao quanh tất cả các palindromic subsequences ở giữa cộng thêm 1 chuỗi gồm chính 2 ký tự: `dp[i][j] = dp[i+1][j] + dp[i][j-1] + 1`. 
Do phép trừ có thể ra số âm, ta phải cộng thêm modulo trước khi lấy dư.

### Mã nguồn Java
```java
public class CountPalindromicSubsequences {
    public int countPalindromes(String s) {
        int n = s.length();
        long[][] dp = new long[n][n];
        long MOD = 1000000007;
        
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = (dp[i + 1][j] + dp[i][j - 1] + 1) % MOD;
                } else {
                    dp[i][j] = (dp[i + 1][j] + dp[i][j - 1] - dp[i + 1][j - 1] + MOD) % MOD;
                }
            }
        }
        
        return (int) dp[0][n - 1];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(n^2)$.
- **Không gian (Space):** $O(n^2)$ cho mảng lưu trạng thái DP.

---

## 27. Number of Ways to Form a Target String

### Đề bài chi tiết
Bạn có mảng các từ `words` có cùng độ dài và một chuỗi `target`. Tìm số cách tạo thành `target` bằng cách ghép các ký tự từ các từ trong `words`. Nếu bạn dùng ký tự ở cột $k$ trong `words` để tạo thành ký tự thứ $i$ trong `target`, thì ký tự thứ $i+1$ trong `target` phải được lấy từ cột có chỉ số lớn hơn $k$.

### Phân tích thuật toán
Đây là bài toán Quy hoạch động 2D kết hợp tiền xử lý tần suất. Gọi `freq[c][k]` là số lần xuất hiện của ký tự `c` tại cột `k` trong mọi từ của `words`. Mảng `dp[i][j]` là số cách tạo thành `target` từ vị trí $i$ trở đi, nếu ta được phép dùng các cột từ $j$ trở đi. 
Công thức chuyển trạng thái: `dp[i][j] = dp[i][j+1]` (bỏ qua cột $j$) + `dp[i+1][j+1] * freq[target[i]][j]` (dùng cột $j$ cho ký tự $target[i]$). Tối ưu không gian bằng mảng 1D.

### Mã nguồn Java
```java
public class NumberOfWaysTarget {
    public int numWays(String[] words, String target) {
        int m = target.length();
        int k = words[0].length();
        long MOD = 1000000007;
        
        long[][] freq = new long[26][k];
        for (String w : words) {
            for (int j = 0; j < k; j++) {
                freq[w.charAt(j) - 'a'][j]++;
            }
        }
        
        long[] dp = new long[m + 1];
        dp[0] = 1;
        
        for (int j = 0; j < k; j++) {
            // Cập nhật ngược để dùng lại mảng 1D an toàn
            for (int i = m - 1; i >= 0; i--) {
                char c = target.charAt(i);
                dp[i + 1] = (dp[i + 1] + dp[i] * freq[c - 'a'][j]) % MOD;
            }
        }
        
        return (int) dp[m];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(W \times K + M \times K)$ với $W$ là số từ, $K$ là độ dài từ, $M$ là độ dài `target`.
- **Không gian (Space):** $O(K + M)$ tối ưu.

---

## 28. Subtree of Another Tree

### Đề bài chi tiết
Cho gốc của hai cây nhị phân `root` và `subRoot`. Trả về `true` nếu tồn tại một cây con của `root` có cấu trúc và giá trị các nút hoàn toàn giống với `subRoot`, ngược lại trả về `false`.

### Phân tích thuật toán
Thay vì duyệt cây và so sánh tuần tự, ta có thể serialize hóa (chuyển đổi thành chuỗi) cả hai cây theo thứ tự duyệt tiền thứ tự (Pre-order). Cần lưu ý sử dụng ký tự đặc biệt cho nhánh null (ví dụ `#`) và đặt giá trị nút vào các dấu phân cách (ví dụ `,`) để phân biệt `12` và `1`, `2`. Sau đó, vấn đề trở thành kiểm tra chuỗi biểu diễn `subRoot` có phải là chuỗi con của chuỗi `root` hay không (sử dụng phương thức `contains` hoặc thuật toán so khớp chuỗi KMP/Z).

### Mã nguồn Java
```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class SubtreeOfAnotherTree {
    public boolean isSubtree(TreeNode root, TreeNode subRoot) {
        StringBuilder sbRoot = new StringBuilder();
        StringBuilder sbSubRoot = new StringBuilder();
        
        serialize(root, sbRoot);
        serialize(subRoot, sbSubRoot);
        
        return sbRoot.toString().contains(sbSubRoot.toString());
    }
    
    private void serialize(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#,");
            return;
        }
        sb.append("^").append(node.val).append(",_"); // Khóa giá trị tránh trùng
        serialize(node.left, sb);
        serialize(node.right, sb);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N + M)$ cho serialization và string matching.
- **Không gian (Space):** $O(N + M)$ lưu các chuỗi kết quả, kích thước tuyến tính với số lượng nút.

---

## 29. Find the Longest Substring Containing Vowels in Even Counts

### Đề bài chi tiết
Tìm chuỗi con liên tiếp dài nhất trong chuỗi `s` sao cho mỗi nguyên âm (a, e, i, o, u) đều xuất hiện một số lượng chẵn (0, 2, 4,...).

### Phân tích thuật toán
Sử dụng kỹ thuật Bitmask và trạng thái tiền tố. Một số nguyên 5 bit có thể đại diện cho tính chẵn lẻ của 5 nguyên âm: `a` (bit 0), `e` (bit 1), v.v. Bắt đầu với mask = 0 (tất cả đều chẵn). Khi quét qua các ký tự, nếu gặp nguyên âm, ta lật bit tương ứng bằng phép XOR. Dùng một Hash Map (hoặc mảng) lưu trữ chỉ số nhỏ nhất mà ta từng gặp một mask cụ thể. Nếu một mask lặp lại tại vị trí `i` đã từng xuất hiện ở vị trí `j` trước đó, điều này có nghĩa đoạn từ `j+1` đến `i` không làm thay đổi tính chẵn lẻ, tức là mọi nguyên âm xuất hiện chẵn lần.

### Mã nguồn Java
```java
import java.util.Arrays;

public class LongestSubstringVowelsEven {
    public int findTheLongestSubstring(String s) {
        int[] firstOccurrence = new int[32];
        Arrays.fill(firstOccurrence, -2);
        firstOccurrence[0] = -1; // Trạng thái 0 có trước khi duyệt
        
        int mask = 0;
        int maxLen = 0;
        String vowels = "aeiou";
        
        for (int i = 0; i < s.length(); i++) {
            int vowelIdx = vowels.indexOf(s.charAt(i));
            if (vowelIdx >= 0) {
                mask ^= (1 << vowelIdx);
            }
            
            if (firstOccurrence[mask] != -2) {
                maxLen = Math.max(maxLen, i - firstOccurrence[mask]);
            } else {
                firstOccurrence[mask] = i;
            }
        }
        
        return maxLen;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$ vì ta duyệt qua chuỗi một lần duy nhất.
- **Không gian (Space):** $O(1)$ bởi vì mảng `firstOccurrence` có kích thước cố định là 32.

---

## 30. Wildcard Matching

### Đề bài chi tiết
Cho một chuỗi văn bản `s` và một chuỗi mẫu `p`. Thực hiện so khớp chuỗi theo các đại diện (wildcard pattern matching):
- `?` Khớp với bất kỳ ký tự đơn nào.
- `*` Khớp với bất kỳ chuỗi ký tự nào (bao gồm cả chuỗi rỗng).
Trả về `true` nếu mẫu `p` khớp hoàn toàn với toàn bộ chuỗi `s` (không phải chuỗi con).

### Phân tích thuật toán
Dùng DP 2D. `dp[i][j]` có ý nghĩa: chuỗi `s` có độ dài `i` có khớp với mẫu `p` có độ dài `j` hay không. 
- Khởi tạo: `dp[0][0] = true` (2 chuỗi rỗng). Nếu `p` bắt đầu bằng các dấu `*`, `dp[0][j] = true`.
- Transition: 
Nếu `p[j-1] == '?'` hoặc `s[i-1] == p[j-1]`, ta lấy `dp[i-1][j-1]`.
Nếu `p[j-1] == '*'`, nó có thể khớp với chuỗi rỗng (`dp[i][j-1]`) hoặc khớp với ít nhất một ký tự (`dp[i-1][j]`).

### Mã nguồn Java
```java
public class WildcardMatching {
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        
        dp[0][0] = true;
        // Xử lý mẫu bắt đầu bằng nhiều dấu *
        for (int j = 1; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 1];
            }
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                char charS = s.charAt(i - 1);
                char charP = p.charAt(j - 1);
                
                if (charP == '?' || charP == charS) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (charP == '*') {
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                }
            }
        }
        
        return dp[m][n];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(M \times N)$ điền bảng 2D.
- **Không gian (Space):** $O(M \times N)$ cho mảng DP (có thể giảm xuống $O(N)$).
