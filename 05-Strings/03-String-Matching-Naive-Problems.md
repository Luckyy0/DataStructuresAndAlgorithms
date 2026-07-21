# Danh sách 30 bài tập Naive String Matching

## 1. Implement strStr() / Find the Index of the First Occurrence in a String
**Đề bài chi tiết**: Cho hai chuỗi `haystack` và `needle`. Hãy trả về chỉ số xuất hiện đầu tiên của `needle` trong `haystack`, hoặc trả về `-1` nếu `needle` không phải là một phần của `haystack`.

**Phân tích thuật toán**: Đây là bài toán cơ sở cho Naive String Matching. Sử dụng vòng lặp lồng nhau, cửa sổ trượt qua mảng `haystack` và so khớp ký tự của `needle`.

**Mã nguồn Java**:
```java
class Solution {
    public int strStr(String haystack, String needle) {
        int n = haystack.length();
        int m = needle.length();
        if (m == 0) return 0;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && haystack.charAt(i + j) == needle.charAt(j)) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$ (Worst case), $O(N)$ (Best case).
- Space: $O(1)$.

## 2. Count Occurrences of Pattern in Text
**Đề bài chi tiết**: Cho một văn bản (text) và một mẫu (pattern), hãy đếm xem có bao nhiêu lần mẫu xuất hiện trong văn bản (cho phép đếm các trường hợp chồng chéo - overlapping).

**Phân tích thuật toán**: Tương tự như tìm vị trí đầu tiên, nhưng thay vì trả về `i` khi khớp, ta tăng biến đếm `count++` và vẫn tiếp tục duyệt vòng lặp ngoài.

**Mã nguồn Java**:
```java
class Solution {
    public int countOccurrences(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        int count = 0;
        if (m == 0 || m > n) return 0;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) count++;
        }
        return count;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 3. Rotate String
**Đề bài chi tiết**: Cho hai chuỗi `s` và `goal`, trả về `true` nếu và chỉ nếu `s` có thể trở thành `goal` sau một số thao tác dịch chuyển vòng tròn (shifts).

**Phân tích thuật toán**: Chuỗi `s` sau khi rotate luôn là một chuỗi con của `s + s`. Do đó, nếu `s.length() == goal.length()`, bài toán trở thành tìm chuỗi mẫu `goal` bên trong văn bản `s + s`. Có thể dùng Naive Matching.

**Mã nguồn Java**:
```java
class Solution {
    public boolean rotateString(String s, String goal) {
        if (s.length() != goal.length()) return false;
        String text = s + s;
        // Sử dụng logic Naive string matching hoặc text.contains(goal)
        int n = text.length();
        int m = goal.length();
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == goal.charAt(j)) {
                j++;
            }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times N)$ do text dài $2N$ và mẫu dài $N$.
- Space: $O(N)$ để lưu chuỗi `s + s`.

## 4. Repeated String Match
**Đề bài chi tiết**: Cho 2 chuỗi `a` và `b`. Trả về số lần lặp tối thiểu chuỗi `a` để chuỗi `b` là một chuỗi con của chuỗi `a` được lặp lại đó. Nếu không thể, trả về `-1`.

**Phân tích thuật toán**: Nối `a` với chính nó nhiều lần cho tới khi độ dài lớn hơn hoặc bằng `b.length()`. Nếu chưa đủ độ phủ, có thể phải nối thêm 1 lần nữa. Sau đó, dùng string matching để kiểm tra xem `b` có xuất hiện hay không.

**Mã nguồn Java**:
```java
class Solution {
    public int repeatedStringMatch(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        while (sb.length() < b.length()) {
            sb.append(a);
            count++;
        }
        if (isSubstring(sb.toString(), b)) return count;
        sb.append(a);
        if (isSubstring(sb.toString(), b)) return count + 1;
        return -1;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) { j++; }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O((N + M) \times M)$ với $N$ là len(b), $M$ là len(a).
- Space: $O(N + M)$ để tạo chuỗi gộp.

## 5. Find All Occurrences of Pattern
**Đề bài chi tiết**: Cho văn bản và chuỗi mẫu, trả về một danh sách (List) các chỉ số (index) chứa vị trí bắt đầu của các lần xuất hiện mẫu trong văn bản.

**Phân tích thuật toán**: Khá giống bài 2 đếm số lượng, nhưng ta dùng mảng hoặc List để lưu lại biến `i` mỗi lần khớp hoàn toàn thành công.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> findAllOccurrences(String text, String pattern) {
        List<Integer> result = new ArrayList<>();
        int n = text.length();
        int m = pattern.length();
        if (m == 0 || m > n) return result;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) result.add(i);
        }
        return result;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(K)$ trong đó $K$ là số lần xuất hiện để lưu kết quả trả về.

## 6. Minimum Number of Flips to Make the Binary String Alternating
**Đề bài chi tiết**: Cho một chuỗi nhị phân `s`. Bạn có thể thao tác: loại bỏ ký tự đầu và ghép xuống cuối. Trả về số lần lật (flip '0' thành '1' hoặc ngược lại) tối thiểu để tạo thành chuỗi luân phiên (như "010101" hoặc "101010").

**Phân tích thuật toán**: Đây là một dạng Sliding Window kết hợp String matching. Ta nhân đôi chuỗi `s` thành `s + s` để mô phỏng các thao tác dịch vòng. Sau đó ta tạo 2 mẫu luân phiên: bắt đầu bằng '0' và '1', và dùng cửa sổ trượt độ dài `N` đếm số khác biệt, sau đó lấy min.

**Mã nguồn Java**:
```java
class Solution {
    public int minFlips(String s) {
        int n = s.length();
        String text = s + s;
        StringBuilder alt1 = new StringBuilder();
        StringBuilder alt2 = new StringBuilder();
        for (int i = 0; i < text.length(); i++) {
            alt1.append(i % 2 == 0 ? '0' : '1');
            alt2.append(i % 2 == 0 ? '1' : '0');
        }
        
        int ans = Integer.MAX_VALUE;
        int diff1 = 0, diff2 = 0;
        for (int i = 0; i < text.length(); i++) {
            if (text.charAt(i) != alt1.charAt(i)) diff1++;
            if (text.charAt(i) != alt2.charAt(i)) diff2++;
            
            if (i >= n) {
                if (text.charAt(i - n) != alt1.charAt(i - n)) diff1--;
                if (text.charAt(i - n) != alt2.charAt(i - n)) diff2--;
            }
            if (i >= n - 1) {
                ans = Math.min(ans, Math.min(diff1, diff2));
            }
        }
        return ans;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ do dùng Sliding Window thay vì khớp ngây thơ.
- Space: $O(N)$ cho các chuỗi bổ sung.

## 7. Check If a Word Occurs As a Prefix of Any Word in a Sentence
**Đề bài chi tiết**: Cho một câu (sentence) gồm các từ cách nhau bởi khoảng trắng, và một từ khóa tìm kiếm (searchWord). Trả về chỉ số 1-based của từ đầu tiên mà `searchWord` là tiền tố. Nếu không tìm thấy, trả về -1.

**Phân tích thuật toán**: Có thể tách chuỗi bằng `split(" ")` hoặc trực tiếp so sánh thủ công. Sử dụng String matching để so sánh `searchWord` từ vị trí 0 của mỗi từ.

**Mã nguồn Java**:
```java
class Solution {
    public int isPrefixOfWord(String sentence, String searchWord) {
        String[] words = sentence.split(" ");
        int m = searchWord.length();
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            if (word.length() >= m) {
                int j = 0;
                while (j < m && word.charAt(j) == searchWord.charAt(j)) {
                    j++;
                }
                if (j == m) return i + 1; // 1-based index
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(L)$ với $L$ là độ dài tổng thể của sentence.
- Space: $O(L)$ do dùng `split`. (Có thể tối ưu xuống $O(1)$ nếu tự traverse char bằng tay).

## 8. Count Prefixes of a Given String
**Đề bài chi tiết**: Cho một mảng các chuỗi `words` và một chuỗi `s`. Đếm số chuỗi trong `words` là tiền tố (prefix) của `s`.

**Phân tích thuật toán**: Duyệt qua mỗi chuỗi `w` trong mảng `words`. Áp dụng String Matching vị trí đầu tiên để xem $M$ ký tự đầu của `s` có khớp hoàn toàn với `w` hay không.

**Mã nguồn Java**:
```java
class Solution {
    public int countPrefixes(String[] words, String s) {
        int count = 0;
        for (String w : words) {
            int m = w.length();
            if (m <= s.length()) {
                int j = 0;
                while (j < m && s.charAt(j) == w.charAt(j)) {
                    j++;
                }
                if (j == m) count++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp**:
- Time: $O(K \times M)$ với $K$ là số từ trong mảng và $M$ là chiều dài lớn nhất của một từ.
- Space: $O(1)$.

## 9. Maximum Repeating Substring
**Đề bài chi tiết**: Cho một chuỗi `sequence` và `word`, hãy tìm giá trị $k$ lớn nhất sao cho `word` lặp lại liên tiếp $k$ lần vẫn là chuỗi con của `sequence`.

**Phân tích thuật toán**: Bắt đầu bằng việc nhân chuỗi `word` lên $k$ lần. Kiểm tra nếu chuỗi mở rộng này có nằm trong `sequence` (sử dụng Naive Matching). Tăng $k$ cho đến khi không khớp.

**Mã nguồn Java**:
```java
class Solution {
    public int maxRepeating(String sequence, String word) {
        int k = 0;
        StringBuilder repeated = new StringBuilder(word);
        while (isSubstring(sequence, repeated.toString())) {
            k++;
            repeated.append(word);
        }
        return k;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        if (m > n) return false;
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) { j++; }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M \times K) \approx O(N^2)$.
- Space: $O(N)$ để giữ `repeated`.

## 10. Check if String is a Prefix of Array
**Đề bài chi tiết**: Cho chuỗi `s` và mảng chuỗi `words`. Kiểm tra xem `s` có được cấu tạo từ cách ghép chính xác lần lượt các chuỗi từ đầu mảng `words` (prefix của array) không.

**Phân tích thuật toán**: Nối dần các từ trong `words` lại theo thứ tự từ $0 \to k$. Sau mỗi bước nối, so sánh chuỗi ghép có bằng `s` không. Có thể tối ưu bằng cách so sánh từng ký tự mà không cần nối chuỗi để đạt Space $O(1)$.

**Mã nguồn Java**:
```java
class Solution {
    public boolean isPrefixString(String s, String[] words) {
        int sIdx = 0;
        for (String word : words) {
            for (int i = 0; i < word.length(); i++) {
                if (sIdx == s.length() || s.charAt(sIdx) != word.charAt(i)) {
                    return false; // Mismatch
                }
                sIdx++;
            }
            if (sIdx == s.length()) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(N)$ với $N$ là chiều dài của chuỗi `s`.
- Space: $O(1)$.

## 11. String Matching in an Array
**Đề bài chi tiết**: Cho một mảng các chuỗi `words`. Tìm tất cả các chuỗi trong mảng mà nó là chuỗi con (substring) của một chuỗi khác cũng nằm trong mảng đó. Trả về danh sách các chuỗi thỏa mãn.

**Phân tích thuật toán**: Duyệt qua từng cặp chuỗi trong mảng `(words[i], words[j])` với `i != j`. Sử dụng thuật toán Naive String Matching để kiểm tra xem `words[i]` có phải là chuỗi con của `words[j]` hay không. Nếu có, thêm `words[i]` vào danh sách kết quả và chuyển sang kiểm tra chuỗi tiếp theo.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> stringMatching(String[] words) {
        List<String> result = new ArrayList<>();
        int n = words.length;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i != j && isSubstring(words[j], words[i])) {
                    result.add(words[i]);
                    break;
                }
            }
        }
        return result;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m > n) return false;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(N^2 \times L^2)$ với $N$ là số lượng chuỗi, $L$ là độ dài tối đa của một chuỗi.
- Space: $O(1)$ (không tính bộ nhớ lưu kết quả).

## 12. Count Substrings That Satisfy a Rule
**Đề bài chi tiết**: Cho chuỗi văn bản `text` và `pattern`. Đếm số lượng chuỗi con trong `text` có chứa ít nhất một lần `pattern`.

**Phân tích thuật toán**: Sinh tất cả các chuỗi con của `text` từ vị trí `i` đến `j`. Với mỗi chuỗi con `text.substring(i, j)`, ta sử dụng thuật toán Naive Matching để kiểm tra xem nó có chứa `pattern` hay không. 

**Mã nguồn Java**:
```java
class Solution {
    public int countSubstrings(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        int count = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = i + m; j <= n; j++) {
                if (isSubstring(text.substring(i, j), pattern)) {
                    count++;
                }
            }
        }
        return count;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        for (int i = 0; i <= n - m; i++) {
            int k = 0;
            while (k < m && text.charAt(i + k) == pattern.charAt(k)) {
                k++;
            }
            if (k == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(N^3 \times M)$ do tính toán duyệt tất cả các chuỗi con và so khớp.
- Space: $O(N)$ cho thao tác cắt chuỗi `substring`.

## 13. Replace All Occurrences
**Đề bài chi tiết**: Cho một chuỗi `text`, chuỗi `pattern` và chuỗi `replacement`. Tìm mọi lần xuất hiện của `pattern` trong `text` và thay thế nó bằng `replacement` sử dụng thuật toán ngây thơ.

**Phân tích thuật toán**: Sử dụng `StringBuilder` để tạo chuỗi mới. Duyệt `text` bằng con trỏ `i`, dùng vòng lặp trong kiểm tra sự xuất hiện của `pattern`. Nếu khớp, nối `replacement` vào kết quả và tịnh tiến `i` qua phần đã khớp. Nếu không, nối ký tự hiện tại và tịnh tiến `i` lên 1.

**Mã nguồn Java**:
```java
class Solution {
    public String replaceAll(String text, String pattern, String replacement) {
        StringBuilder sb = new StringBuilder();
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return text;
        
        int i = 0;
        while (i <= n - m) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) {
                sb.append(replacement);
                i += m; // Bỏ qua phần pattern đã khớp
            } else {
                sb.append(text.charAt(i));
                i++;
            }
        }
        
        while (i < n) {
            sb.append(text.charAt(i));
            i++;
        }
        
        return sb.toString();
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(N + K)$ với $K$ là tổng chiều dài các đoạn được thay thế.

## 14. Valid Anagram matching window
**Đề bài chi tiết**: Cho chuỗi `s` và `p`. Tìm tất cả các vị trí bắt đầu của các chuỗi con trong `s` là anagram (đảo chữ) của `p`.

**Phân tích thuật toán**: Một chuỗi con là anagram nếu có cùng tần suất các ký tự. Trượt một cửa sổ độ dài bằng `p.length()` qua chuỗi `s`, và ở mỗi cửa sổ tiến hành so khớp mảng tần suất của các ký tự hiện có trong cửa sổ đó với `p`.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Arrays;

class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        int n = s.length();
        int m = p.length();
        if (m > n) return result;
        
        int[] pCount = new int[26];
        for (int i = 0; i < m; i++) {
            pCount[p.charAt(i) - 'a']++;
        }
        
        for (int i = 0; i <= n - m; i++) {
            int[] sCount = new int[26];
            for (int j = 0; j < m; j++) {
                sCount[s.charAt(i + j) - 'a']++;
            }
            if (Arrays.equals(pCount, sCount)) {
                result.add(i);
            }
        }
        return result;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$ do tính toán lại tần suất ở mỗi vòng lặp (cách tiếp cận ngây thơ).
- Space: $O(1)$ vì bảng chữ cái có kích thước giới hạn (26 ký tự).

## 15. Delete occurrences of pattern
**Đề bài chi tiết**: Cho chuỗi `text` và chuỗi `pattern`. Xóa liên tục phần chuỗi con đầu tiên khớp với `pattern` khỏi `text` cho đến khi không còn `pattern` xuất hiện. Trả về kết quả cuối cùng.

**Phân tích thuật toán**: Vòng lặp `while` tìm chỉ số xuất hiện đầu tiên của `pattern` bằng thuật toán Naive. Nếu tìm thấy, cắt bỏ đoạn đó bằng cách nối tiền tố và hậu tố lại với nhau, rồi tiếp tục tìm trên chuỗi mới. Dừng lại khi không tìm thấy.

**Mã nguồn Java**:
```java
class Solution {
    public String removeOccurrences(String text, String pattern) {
        int m = pattern.length();
        if (m == 0) return text;
        
        while (true) {
            int idx = findPattern(text, pattern);
            if (idx == -1) break;
            text = text.substring(0, idx) + text.substring(idx + m);
        }
        return text;
    }
    
    private int findPattern(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(K \times N \times M)$ với $K$ là số lần phải xóa, trường hợp xấu nhất là $O(N^2 \times M)$.
- Space: $O(N)$ do tạo các chuỗi con bằng `substring`.

## 16. Matching with Wildcard '?'
**Đề bài chi tiết**: Tìm kiếm chuỗi mẫu `pattern` trong `text`, trong đó `pattern` có thể chứa các ký tự đại diện `?` (có thể khớp với bất kỳ ký tự đơn nào). Trả về chỉ số đầu tiên khớp, hoặc -1.

**Phân tích thuật toán**: Chỉnh sửa một chút trong điều kiện vòng lặp trong của Naive Matching. Tại một vị trí, ta coi là khớp nếu ký tự trong `text` giống ký tự trong `pattern`, hoặc ký tự trong `pattern` là `?`.

**Mã nguồn Java**:
```java
class Solution {
    public int strStrWithWildcard(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return 0;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && (pattern.charAt(j) == '?' || text.charAt(i + j) == pattern.charAt(j))) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 17. Shortest Palindrome using matching
**Đề bài chi tiết**: Thêm một số ký tự vào đầu chuỗi `s` để biến nó thành chuỗi đối xứng (palindrome). Hãy tìm cách thêm sao cho chuỗi kết quả là ngắn nhất.

**Phân tích thuật toán**: Bài toán quy về tìm phần tiền tố dài nhất của `s` mà đã là chuỗi đối xứng. Ta đảo ngược `s` thành `rev`. Sau đó, dùng matching thủ công để so sánh một phần đầu của `s` với phần cuối của `rev`, giảm dần độ dài từ $N$ về 1 để tìm đoạn khớp dài nhất.

**Mã nguồn Java**:
```java
class Solution {
    public String shortestPalindrome(String s) {
        int n = s.length();
        if (n == 0) return s;
        
        String rev = new StringBuilder(s).reverse().toString();
        
        for (int i = 0; i < n; i++) {
            // So khớp tiền tố s với hậu tố rev
            if (s.substring(0, n - i).equals(rev.substring(i))) {
                return rev.substring(0, i) + s;
            }
        }
        return s;
    }
}
```
**Độ phức tạp**:
- Time: $O(N^2)$ vì hàm `equals` tốn $O(N)$ trong mỗi lần lặp.
- Space: $O(N)$ để lưu chuỗi bị đảo ngược.

## 18. Reverse Pattern searching
**Đề bài chi tiết**: Cho văn bản `text` và chuỗi mẫu `pattern`. Tìm vị trí xuất hiện cuối cùng của `pattern` trong `text` thay vì tìm vị trí đầu tiên. Trả về -1 nếu không tìm thấy.

**Phân tích thuật toán**: Vẫn giữ nguyên vòng lặp trong của thuật toán Naive, nhưng vòng lặp ngoài duyệt `i` giảm dần từ `n - m` về 0. Lần đầu tiên khớp hoàn toàn sẽ chính là vị trí xuất hiện cuối cùng.

**Mã nguồn Java**:
```java
class Solution {
    public int lastIndexOfPattern(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return n;
        
        for (int i = n - m; i >= 0; i--) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 19. Overlapping matches counting limit
**Đề bài chi tiết**: Đếm số lần xuất hiện tối đa của `pattern` trong `text` sao cho các chuỗi con khớp không bị chồng chéo (non-overlapping).

**Phân tích thuật toán**: Áp dụng chiến lược tham lam (Greedy). Duyệt tìm `pattern` từ trái sang phải. Ngay khi tìm thấy sự khớp ở chỉ số `i`, ta tăng biến đếm và tịnh tiến con trỏ `i` nhảy qua phần vừa khớp (`i += m`) thay vì chỉ tăng lên 1, đảm bảo lần đếm sau không bị đè lên khoảng hiện tại.

**Mã nguồn Java**:
```java
class Solution {
    public int maxNonOverlapping(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        int count = 0;
        if (m == 0 || m > n) return 0;
        
        int i = 0;
        while (i <= n - m) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) {
                count++;
                i += m; // Bỏ qua đoạn đã khớp
            } else {
                i++;
            }
        }
        return count;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 20. Index of Longest match
**Đề bài chi tiết**: Cho `text` và `pattern`. Tìm chỉ số `i` sao cho độ dài đoạn khớp với phần đầu của `pattern` là dài nhất. Trả về vị trí đầu tiên đạt được độ dài này.

**Phân tích thuật toán**: Vòng lặp `while` không chỉ đòi hỏi `j < m` mà còn lưu lại giá trị `j` lớn nhất từng đạt được ở bất kỳ vị trí `i` nào. Nếu tìm được `j == m` thì trả về ngay.

**Mã nguồn Java**:
```java
class Solution {
    public int indexOfLongestMatch(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return 0;
        
        int maxMatchLen = -1;
        int bestIndex = -1;
        
        for (int i = 0; i <= n - 1; i++) {
            int j = 0;
            while (i + j < n && j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j > maxMatchLen) {
                maxMatchLen = j;
                bestIndex = i;
            }
            if (maxMatchLen == m) return bestIndex;
        }
        return bestIndex;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 21. Lexicographically Smallest Match
**Đề bài chi tiết**: Cho chuỗi văn bản `text` và mảng các chuỗi `patterns`. Tìm chuỗi có thứ tự từ điển nhỏ nhất trong số các chuỗi thuộc `patterns` mà xuất hiện như một chuỗi con của `text`. Nếu không có chuỗi nào, trả về chuỗi rỗng `""`.

**Phân tích thuật toán**: Duyệt qua từng chuỗi `p` trong mảng `patterns`. Sử dụng thuật toán Naive để kiểm tra xem `p` có phải là chuỗi con của `text` hay không. Nếu có, ta so sánh chuỗi `p` với chuỗi kết quả hiện tại, nếu `p` có thứ tự từ điển nhỏ hơn thì cập nhật chuỗi kết quả thành `p`.

**Mã nguồn Java**:
```java
class Solution {
    public String smallestMatch(String text, String[] patterns) {
        String bestMatch = null;
        for (String p : patterns) {
            if (isSubstring(text, p)) {
                if (bestMatch == null || p.compareTo(bestMatch) < 0) {
                    bestMatch = p;
                }
            }
        }
        return bestMatch == null ? "" : bestMatch;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m > n) return false;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(K \times N \times M)$ với $K$ là số lượng patterns, $N$ là độ dài text, $M$ là chiều dài trung bình của pattern.
- Space: $O(1)$.

## 22. K-th Occurrence of String
**Đề bài chi tiết**: Cho chuỗi `text`, chuỗi `pattern` và số nguyên `K`. Tìm chỉ số xuất hiện lần thứ `K` của `pattern` trong `text` (cho phép các lần xuất hiện bị chồng chéo). Trả về -1 nếu không đủ `K` lần.

**Phân tích thuật toán**: Dùng thuật toán Naive trượt qua `text`. Mỗi lần tìm thấy một vị trí khớp hoàn toàn, ta tăng bộ đếm lên. Nếu bộ đếm đạt giá trị `K`, lập tức trả về chỉ số hiện tại.

**Mã nguồn Java**:
```java
class Solution {
    public int kthOccurrence(String text, String pattern, int k) {
        int n = text.length();
        int m = pattern.length();
        int count = 0;
        if (m == 0 || m > n) return -1;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) {
                count++;
                if (count == k) return i;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 23. StartsWith and EndsWith implementation
**Đề bài chi tiết**: Tự cài đặt hai phương thức `startsWith(String text, String prefix)` và `endsWith(String text, String suffix)` mà không sử dụng các hàm có sẵn của ngôn ngữ, sử dụng nguyên lý vòng lặp của chuỗi.

**Phân tích thuật toán**: 
- `startsWith`: So khớp từng ký tự của `prefix` từ chỉ số 0 của `text`.
- `endsWith`: So khớp từng ký tự của `suffix` từ vị trí `text.length() - suffix.length()` của `text`.
Nếu có bất kỳ khác biệt nào, trả về `false`.

**Mã nguồn Java**:
```java
class Solution {
    public boolean startsWith(String text, String prefix) {
        int m = prefix.length();
        if (m > text.length()) return false;
        
        for (int i = 0; i < m; i++) {
            if (text.charAt(i) != prefix.charAt(i)) {
                return false;
            }
        }
        return true;
    }
    
    public boolean endsWith(String text, String suffix) {
        int n = text.length();
        int m = suffix.length();
        if (m > n) return false;
        
        int offset = n - m;
        for (int i = 0; i < m; i++) {
            if (text.charAt(offset + i) != suffix.charAt(i)) {
                return false;
            }
        }
        return true;
    }
}
```
**Độ phức tạp**:
- Time: $O(M)$ với $M$ là độ dài của prefix hoặc suffix.
- Space: $O(1)$.

## 24. Find Text ignoring Case
**Đề bài chi tiết**: Tìm vị trí đầu tiên của chuỗi `pattern` trong chuỗi `text` không phân biệt chữ hoa, chữ thường (case-insensitive).

**Phân tích thuật toán**: Vẫn giữ cấu trúc thuật toán Naive, nhưng khi so sánh hai ký tự, ta quy đổi cả hai về chữ thường (hoặc chữ hoa) trước khi kiểm tra, hoặc dùng hàm so sánh không phân biệt hoa thường của ngôn ngữ lập trình.

**Mã nguồn Java**:
```java
class Solution {
    public int strStrIgnoreCase(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return 0;
        
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && Character.toLowerCase(text.charAt(i + j)) == Character.toLowerCase(pattern.charAt(j))) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 25. Pattern Match with step offset
**Đề bài chi tiết**: Cho chuỗi `text`, chuỗi `pattern` và một số nguyên `step`. Tìm vị trí đầu tiên `i` trong `text` sao cho các ký tự của `pattern` khớp với các ký tự trong `text` cách nhau một khoảng `step`. Cụ thể: `text[i] == pattern[0]`, `text[i + step] == pattern[1]`, `text[i + 2*step] == pattern[2]`...

**Phân tích thuật toán**: Độ dài "cửa sổ" thực tế để chứa toàn bộ pattern theo step là `(m - 1) * step + 1`. Ta giới hạn vòng lặp ngoài `i` đến `n - window`. Vòng lặp trong duyệt từ `j = 0` đến `m-1` và so sánh tại chỉ số `i + j * step`.

**Mã nguồn Java**:
```java
class Solution {
    public int matchWithStep(String text, String pattern, int step) {
        int n = text.length();
        int m = pattern.length();
        if (m == 0) return 0;
        
        int window = (m - 1) * step + 1;
        if (window > n) return -1;
        
        for (int i = 0; i <= n - window; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j * step) == pattern.charAt(j)) {
                j++;
            }
            if (j == m) return i;
        }
        return -1;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 26. Most frequent substring of length M
**Đề bài chi tiết**: Cho một chuỗi `text` và số nguyên `M`. Tìm chuỗi con liên tiếp có độ dài `M` xuất hiện nhiều lần nhất trong `text`. Nếu có nhiều chuỗi có cùng số lần xuất hiện cao nhất, trả về một trong số đó.

**Phân tích thuật toán**: Trượt cửa sổ độ dài `M` qua chuỗi `text`, dùng `substring` để cắt chuỗi và lưu tần suất vào một Hash Map. Cuối cùng, duyệt Hash Map để tìm chuỗi có giá trị (tần suất) lớn nhất.

**Mã nguồn Java**:
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public String mostFrequentSubstring(String text, int m) {
        int n = text.length();
        if (m > n || m <= 0) return "";
        
        Map<String, Integer> counts = new HashMap<>();
        String bestSub = "";
        int maxCount = 0;
        
        for (int i = 0; i <= n - m; i++) {
            String sub = text.substring(i, i + m);
            counts.put(sub, counts.getOrDefault(sub, 0) + 1);
            if (counts.get(sub) > maxCount) {
                maxCount = counts.get(sub);
                bestSub = sub;
            }
        }
        return bestSub;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$ do việc cắt và tạo mã băm cho chuỗi con độ dài $M$.
- Space: $O(N \times M)$ để lưu các chuỗi trong Hash Map.

## 27. Number of Words Matching Subsequence
**Đề bài chi tiết**: Cho chuỗi `text` và mảng chuỗi `words`. Đếm số lượng từ trong `words` là một dãy con (subsequence) của `text`. Dãy con không yêu cầu các ký tự phải liên tiếp nhưng phải đúng thứ tự.

**Phân tích thuật toán**: Khác với chuỗi con (substring) cần duyệt mảng ngoài và trong, dãy con (subsequence) yêu cầu ta dùng 2 con trỏ. Con trỏ `i` cho chuỗi con đang xét và `j` cho `text`. Nếu các ký tự giống nhau thì tăng `i`, còn `j` luôn tăng qua mỗi bước duyệt.

**Mã nguồn Java**:
```java
class Solution {
    public int numMatchingSubseq(String text, String[] words) {
        int count = 0;
        for (String word : words) {
            if (isSubsequence(word, text)) {
                count++;
            }
        }
        return count;
    }
    
    private boolean isSubsequence(String s, String t) {
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
**Độ phức tạp**:
- Time: $O(K \times N)$ với $K$ là số lượng từ trong `words` và $N$ là độ dài của `text`.
- Space: $O(1)$.

## 28. Multiple Pattern Search Basic
**Đề bài chi tiết**: Cho chuỗi văn bản `text` và mảng chuỗi `patterns`. Tìm xem những chuỗi nào trong `patterns` xuất hiện ít nhất 1 lần trong `text`. Trả về danh sách các chuỗi đó.

**Phân tích thuật toán**: Đây là một bài toán cơ sở thường được giải bằng thuật toán Aho-Corasick. Tuy nhiên với giới hạn cơ bản, ta sẽ dùng Naive String Matching duyệt từng chuỗi trong `patterns` và kiểm tra sự tồn tại của nó trong `text`.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> multiplePatternSearch(String text, String[] patterns) {
        List<String> result = new ArrayList<>();
        for (String p : patterns) {
            if (isSubstring(text, p)) {
                result.add(p);
            }
        }
        return result;
    }
    
    private boolean isSubstring(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        if (m > n) return false;
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && text.charAt(i + j) == pattern.charAt(j)) { j++; }
            if (j == m) return true;
        }
        return false;
    }
}
```
**Độ phức tạp**:
- Time: $O(K \times N \times M)$ với $K$ là số mẫu, $N$ là kích thước văn bản và $M$ là độ dài cực đại mẫu.
- Space: $O(1)$ (không tính List kết quả).

## 29. Fuzzy String Matching (1 typo)
**Đề bài chi tiết**: Cho chuỗi văn bản `text` và chuỗi `pattern`. Tìm tất cả các vị trí bắt đầu trong `text` mà tại đó `pattern` khớp với văn bản nhưng cho phép SAI (lệch ký tự) tối đa 1 lần. 

**Phân tích thuật toán**: Vẫn sử dụng hai vòng lặp lồng nhau. Trong quá trình so sánh vòng lặp trong, thay vì dừng lại ngay khi có ký tự khác, ta duy trì một biến đếm số lỗi `mistakes`. Nếu `mistakes > 1`, thì mới ngắt vòng lặp hiện tại.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> fuzzyMatch(String text, String pattern) {
        List<Integer> result = new ArrayList<>();
        int n = text.length();
        int m = pattern.length();
        if (m == 0 || m > n) return result;
        
        for (int i = 0; i <= n - m; i++) {
            int mistakes = 0;
            int j = 0;
            while (j < m) {
                if (text.charAt(i + j) != pattern.charAt(j)) {
                    mistakes++;
                    if (mistakes > 1) break;
                }
                j++;
            }
            if (mistakes <= 1 && j == m) {
                result.add(i);
            }
        }
        return result;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(1)$.

## 30. Implement Split() Function
**Đề bài chi tiết**: Tự cài đặt một hàm mô phỏng phương thức `split(String text, String delimiter)` bằng cách sử dụng thuật toán tìm kiếm ngây thơ để lấy chỉ số của bộ phân tách (delimiter). 

**Phân tích thuật toán**: Sử dụng Naive Matching để tìm vị trí xuất hiện của `delimiter`. Khi tìm thấy tại vị trí `idx`, ta cắt đoạn chuỗi từ đầu con trỏ hiện hành tới `idx`, đưa vào danh sách kết quả, rồi tịnh tiến con trỏ lên đoạn `idx + delimiter.length()`. Tiếp tục như vậy đến khi hết chuỗi. Cuối cùng, thêm phần còn lại của chuỗi vào danh sách.

**Mã nguồn Java**:
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> mySplit(String text, String delimiter) {
        List<String> result = new ArrayList<>();
        int n = text.length();
        int m = delimiter.length();
        if (m == 0) {
            for (char c : text.toCharArray()) result.add(String.valueOf(c));
            return result;
        }
        
        int start = 0;
        int i = 0;
        while (i <= n - m) {
            int j = 0;
            while (j < m && text.charAt(i + j) == delimiter.charAt(j)) {
                j++;
            }
            if (j == m) {
                result.add(text.substring(start, i));
                i += m;
                start = i;
            } else {
                i++;
            }
        }
        // Thêm phần cuối cùng
        result.add(text.substring(start));
        return result;
    }
}
```
**Độ phức tạp**:
- Time: $O(N \times M)$.
- Space: $O(N)$ do việc lưu trữ danh sách các chuỗi phân tách.
