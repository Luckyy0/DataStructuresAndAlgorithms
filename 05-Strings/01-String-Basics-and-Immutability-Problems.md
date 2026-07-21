# 30 String Problems

## 1. Valid Palindrome
**Đề bài chi tiết:** Cho một chuỗi $s$, kiểm tra xem nó có phải là chuỗi đối xứng (palindrome) hay không, sau khi chuyển tất cả chữ hoa thành chữ thường và loại bỏ tất cả các ký tự không phải chữ cái và số.
**Phân tích thuật toán:** Sử dụng phương pháp Two Pointers. Một con trỏ $i$ từ đầu chuỗi, $j$ từ cuối chuỗi. Bỏ qua các ký tự không hợp lệ. So sánh các ký tự hợp lệ ở vị trí $i$ và $j$.
**Mã nguồn Java:**
```java
public class ValidPalindrome {
    public boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
            while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;
            if (Character.toLowerCase(s.charAt(i)) != Character.toLowerCase(s.charAt(j))) {
                return false;
            }
            i++;
            j--;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$ với $n$ là chiều dài chuỗi.

## 2. Valid Anagram
**Đề bài chi tiết:** Cho 2 chuỗi $s$ và $t$, trả về `true` nếu $t$ là anagram (đảo chữ) của $s$, ngược lại trả về `false`.
**Phân tích thuật toán:** Đếm tần suất xuất hiện của từng ký tự trong $s$. Trừ đi tần suất của các ký tự trong $t$. Nếu tất cả bằng 0 thì là anagram. Do đề bài giới hạn các chữ cái thường nên dùng mảng int[26].
**Mã nguồn Java:**
```java
public class ValidAnagram {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
            count[t.charAt(i) - 'a']--;
        }
        for (int c : count) {
            if (c != 0) return false;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 3. First Unique Character in a String
**Đề bài chi tiết:** Cho một chuỗi $s$, tìm ký tự không lặp lại đầu tiên và trả về chỉ mục của nó. Nếu không có, trả về -1.
**Phân tích thuật toán:** Sử dụng mảng tần số 26 phần tử để đếm số lần xuất hiện. Sau đó lặp qua chuỗi lần 2 để tìm ký tự đầu tiên có tần số bằng 1.
**Mã nguồn Java:**
```java
public class FirstUniqueCharacter {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        for (int i = 0; i < s.length(); i++) {
            if (count[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 4. Reverse String
**Đề bài chi tiết:** Viết hàm đảo ngược một mảng ký tự $s$. Phải sửa đổi mảng đầu vào tại chỗ (in-place) với không gian phụ trợ $O(1)$.
**Phân tích thuật toán:** Hai con trỏ $i$ và $j$ từ đầu và cuối mảng, hoán đổi phần tử tại vị trí $i$ và $j$, sau đó tịnh tiến $i$ và lùi $j$ cho đến khi $i \ge j$.
**Mã nguồn Java:**
```java
public class ReverseString {
    public void reverseString(char[] s) {
        int left = 0, right = s.length - 1;
        while (left < right) {
            char temp = s[left];
            s[left] = s[right];
            s[right] = temp;
            left++;
            right--;
        }
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 5. Isomorphic Strings
**Đề bài chi tiết:** Hai chuỗi $s$ và $t$ có độ dài bằng nhau. Xác định xem chúng có đồng hình (isomorphic) hay không (các ký tự trong $s$ có thể thay thế bằng các ký tự duy nhất để tạo thành $t$).
**Phân tích thuật toán:** Sử dụng 2 mảng int[256] để lưu vị trí xuất hiện cuối cùng của từng ký tự trong cả $s$ và $t$. Ở mỗi vị trí $i$, so sánh mảng của cả 2 ký tự.
**Mã nguồn Java:**
```java
public class IsomorphicStrings {
    public boolean isIsomorphic(String s, String t) {
        int[] mapS = new int[256];
        int[] mapT = new int[256];
        
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            if (mapS[c1] != mapT[c2]) {
                return false;
            }
            mapS[c1] = i + 1;
            mapT[c2] = i + 1;
        }
        return true;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$ (bảng chữ cái ASCII cố định).

## 6. Longest Common Prefix
**Đề bài chi tiết:** Viết hàm tìm chuỗi tiền tố chung dài nhất giữa một mảng các chuỗi. Nếu không có trả về `""`.
**Phân tích thuật toán:** Lấy chuỗi đầu tiên làm tiền tố chung giả định. Lặp qua các chuỗi còn lại, rút ngắn dần tiền tố này cho đến khi nó trùng khớp với đoạn bắt đầu (sử dụng `startsWith`).
**Mã nguồn Java:**
```java
public class LongestCommonPrefix {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        String prefix = strs[0];
        for (int i = 1; i < strs.length; i++) {
            while (!strs[i].startsWith(prefix)) {
                prefix = prefix.substring(0, prefix.length() - 1);
                if (prefix.isEmpty()) return "";
            }
        }
        return prefix;
    }
}
```
**Độ phức tạp:** Thời gian $O(S)$ với $S$ là tổng số ký tự của tất cả các chuỗi, Không gian $O(1)$.

## 7. Find All Anagrams in a String
**Đề bài chi tiết:** Cho chuỗi $s$ và $p$, trả về mảng tất cả các chỉ mục bắt đầu của các đoạn con là anagram của $p$ trong $s$.
**Phân tích thuật toán:** Kỹ thuật Sliding Window cố định kích thước của $p$. Sử dụng 2 mảng đếm tần suất. Trượt cửa sổ trên chuỗi $s$ và cập nhật tần suất, so sánh xem 2 mảng có khớp không.
**Mã nguồn Java:**
```java
import java.util.*;

public class FindAllAnagrams {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < p.length()) return result;
        
        int[] pCount = new int[26];
        int[] sCount = new int[26];
        
        for (char c : p.toCharArray()) pCount[c - 'a']++;
        
        for (int i = 0; i < s.length(); i++) {
            sCount[s.charAt(i) - 'a']++;
            if (i >= p.length()) {
                sCount[s.charAt(i - p.length()) - 'a']--;
            }
            if (Arrays.equals(pCount, sCount)) {
                result.add(i - p.length() + 1);
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 8. Group Anagrams
**Đề bài chi tiết:** Cho một mảng các chuỗi, nhóm các anagram lại với nhau.
**Phân tích thuật toán:** Sắp xếp mỗi chuỗi theo bảng chữ cái. Chuỗi sau khi sắp xếp đóng vai trò là khoá (Key) trong bảng băm (`HashMap`). Giá trị là danh sách các chuỗi gốc tương ứng với khoá.
**Mã nguồn Java:**
```java
import java.util.*;

public class GroupAnagrams {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] chars = s.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            map.putIfAbsent(key, new ArrayList<>());
            map.get(key).add(s);
        }
        return new ArrayList<>(map.values());
    }
}
```
**Độ phức tạp:** Thời gian $O(N \cdot K \log K)$ với $N$ là số lượng chuỗi, $K$ là chiều dài tối đa của một chuỗi. Không gian $O(N \cdot K)$.

## 9. Longest Palindromic Substring
**Đề bài chi tiết:** Cho một chuỗi $s$, tìm và trả về chuỗi con đối xứng dài nhất trong $s$.
**Phân tích thuật toán:** Thuật toán Expand Around Center. Xem xét mỗi vị trí và mỗi khoảng giữa 2 vị trí làm tâm của một chuỗi đối xứng, sau đó mở rộng ra 2 bên để tìm chuỗi dài nhất.
**Mã nguồn Java:**
```java
public class LongestPalindromicSubstring {
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
**Độ phức tạp:** Thời gian $O(n^2)$, Không gian $O(1)$.

## 10. Count and Say
**Đề bài chi tiết:** Chuỗi count-and-say bắt đầu với "1". Ở mỗi bước tiếp theo, đếm số lượng các chữ số giống nhau liên tiếp và nói ra (VD: "11" -> "21" (hai số 1)). Trả về chuỗi ở bước thứ $n$.
**Phân tích thuật toán:** Sử dụng vòng lặp từ 1 đến $n$. Tại mỗi bước, lặp qua chuỗi hiện tại, đếm các ký tự liền kề giống nhau và xây dựng chuỗi cho bước tiếp theo bằng `StringBuilder`.
**Mã nguồn Java:**
```java
public class CountAndSay {
    public String countAndSay(int n) {
        String s = "1";
        for (int i = 1; i < n; i++) {
            StringBuilder sb = new StringBuilder();
            int count = 1;
            for (int j = 1; j < s.length(); j++) {
                if (s.charAt(j) == s.charAt(j - 1)) {
                    count++;
                } else {
                    sb.append(count).append(s.charAt(j - 1));
                    count = 1;
                }
            }
            sb.append(count).append(s.charAt(s.length() - 1));
            s = sb.toString();
        }
        return s;
    }
}
```
**Độ phức tạp:** Thời gian xấp xỉ $O(2^n)$, Không gian $O(2^n)$ để chứa chuỗi lớn nhất.

## 11. Implement strStr() (Find indexOf)
**Đề bài chi tiết:** Cho hai chuỗi $haystack$ và $needle$. Trả về chỉ mục của lần xuất hiện đầu tiên của $needle$ trong $haystack$, hoặc -1 nếu $needle$ không phải là một phần của $haystack$.
**Phân tích thuật toán:** Duyệt qua các ký tự của $haystack$ và với mỗi vị trí, kiểm tra xem chuỗi con có độ dài bằng $needle$ bắt đầu từ đó có khớp với $needle$ hay không (Sliding Window đơn giản).
**Mã nguồn Java:**
```java
public class ImplementStrStr {
    public int strStr(String haystack, String needle) {
        if (needle.isEmpty()) return 0;
        int m = haystack.length(), n = needle.length();
        for (int i = 0; i <= m - n; i++) {
            int j = 0;
            while (j < n && haystack.charAt(i + j) == needle.charAt(j)) {
                j++;
            }
            if (j == n) {
                return i;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:** Thời gian $O((m - n + 1) \cdot n)$ trong trường hợp tệ nhất, Không gian $O(1)$.

## 12. Length of Last Word
**Đề bài chi tiết:** Cho một chuỗi $s$ bao gồm các từ và dấu cách, trả về độ dài của từ cuối cùng trong chuỗi.
**Phân tích thuật toán:** Duyệt ngược từ cuối chuỗi $s$. Bỏ qua tất cả các dấu cách ở cuối. Khi gặp ký tự khác dấu cách, bắt đầu đếm. Dừng lại khi gặp dấu cách tiếp theo.
**Mã nguồn Java:**
```java
public class LengthOfLastWord {
    public int lengthOfLastWord(String s) {
        int length = 0;
        int i = s.length() - 1;
        
        while (i >= 0 && s.charAt(i) == ' ') {
            i--;
        }
        
        while (i >= 0 && s.charAt(i) != ' ') {
            length++;
            i--;
        }
        
        return length;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 13. Add Binary
**Đề bài chi tiết:** Cho hai chuỗi nhị phân $a$ và $b$, trả về tổng của chúng dưới dạng một chuỗi nhị phân.
**Phân tích thuật toán:** Duyệt đồng thời hai chuỗi từ cuối lên đầu. Sử dụng biến $carry$ để nhớ phần dư sau mỗi phép cộng. Dùng `StringBuilder` để xây dựng chuỗi kết quả theo chiều ngược lại, sau đó đảo ngược chuỗi để có kết quả cuối cùng.
**Mã nguồn Java:**
```java
public class AddBinary {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1, j = b.length() - 1;
        int carry = 0;
        
        while (i >= 0 || j >= 0 || carry > 0) {
            int sum = carry;
            if (i >= 0) sum += a.charAt(i--) - '0';
            if (j >= 0) sum += b.charAt(j--) - '0';
            
            sb.append(sum % 2);
            carry = sum / 2;
        }
        
        return sb.reverse().toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(\max(m, n))$, Không gian $O(\max(m, n))$.

## 14. String to Integer (atoi)
**Đề bài chi tiết:** Cài đặt hàm chuyển đổi một chuỗi thành số nguyên 32-bit có dấu. Bỏ qua các khoảng trắng đầu chuỗi, xác định dấu, và đọc các chữ số cho đến khi gặp ký tự không phải chữ số. Xử lý tràn số học (Integer Overflow).
**Phân tích thuật toán:** Sử dụng con trỏ duyệt từ đầu. Đầu tiên bỏ qua khoảng trắng. Sau đó kiểm tra dấu `+` hoặc `-`. Tiếp theo, đọc các chữ số liên tiếp và cộng dồn vào kết quả. Kiểm tra tràn số liên tục tại mỗi bước.
**Mã nguồn Java:**
```java
public class StringToInteger {
    public int myAtoi(String s) {
        int i = 0, n = s.length();
        while (i < n && s.charAt(i) == ' ') i++;
        if (i == n) return 0;
        
        int sign = 1;
        if (s.charAt(i) == '+' || s.charAt(i) == '-') {
            sign = s.charAt(i) == '+' ? 1 : -1;
            i++;
        }
        
        int res = 0;
        while (i < n && Character.isDigit(s.charAt(i))) {
            int digit = s.charAt(i) - '0';
            if (res > Integer.MAX_VALUE / 10 || 
               (res == Integer.MAX_VALUE / 10 && digit > Integer.MAX_VALUE % 10)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            res = res * 10 + digit;
            i++;
        }
        return res * sign;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 15. Multiply Strings
**Đề bài chi tiết:** Cho hai số nguyên không âm $num1$ và $num2$ biểu diễn dưới dạng chuỗi, trả về tích của chúng dưới dạng chuỗi. Không sử dụng thư viện số lớn tích hợp.
**Phân tích thuật toán:** Mô phỏng quá trình nhân tay. Một số có độ dài $m$ nhân với số có độ dài $n$ cho kết quả độ dài tối đa $m+n$. Tạo mảng `pos` kích thước $m+n$. Nhân từng chữ số $num1[i]$ với $num2[j]$ cộng vào `pos[i+j+1]` và xử lý phần dư (carry) tại `pos[i+j]`.
**Mã nguồn Java:**
```java
public class MultiplyStrings {
    public String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) return "0";
        int m = num1.length(), n = num2.length();
        int[] pos = new int[m + n];
        
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int p1 = i + j, p2 = i + j + 1;
                int sum = mul + pos[p2];
                
                pos[p1] += sum / 10;
                pos[p2] = sum % 10;
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (int p : pos) {
            if (!(sb.length() == 0 && p == 0)) sb.append(p);
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(m \times n)$, Không gian $O(m + n)$.

## 16. Roman to Integer
**Đề bài chi tiết:** Cho một chuỗi biểu diễn một số La Mã, hãy chuyển đổi nó thành một số nguyên.
**Phân tích thuật toán:** Duyệt chuỗi từ trái sang phải. Nếu giá trị của ký tự hiện tại nhỏ hơn giá trị của ký tự liền sau nó, ta phải trừ giá trị hiện tại (ví dụ IV = 5 - 1 = 4). Ngược lại, ta cộng giá trị đó vào kết quả.
**Mã nguồn Java:**
```java
import java.util.*;

public class RomanToInteger {
    public int romanToInt(String s) {
        Map<Character, Integer> map = new HashMap<>();
        map.put('I', 1); map.put('V', 5); map.put('X', 10);
        map.put('L', 50); map.put('C', 100); map.put('D', 500);
        map.put('M', 1000);
        
        int result = 0;
        for (int i = 0; i < s.length(); i++) {
            if (i < s.length() - 1 && map.get(s.charAt(i)) < map.get(s.charAt(i + 1))) {
                result -= map.get(s.charAt(i));
            } else {
                result += map.get(s.charAt(i));
            }
        }
        return result;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(1)$.

## 17. Integer to Roman
**Đề bài chi tiết:** Cho một số nguyên $num$ (từ 1 đến 3999), chuyển đổi nó thành số La Mã.
**Phân tích thuật toán:** Chuẩn bị mảng giá trị số nguyên giảm dần và mảng ký hiệu La Mã tương ứng. Duyệt qua mảng giá trị, lấy phần nguyên chia cho giá trị đó để gắn các ký hiệu, và cập nhật phần dư của $num$.
**Mã nguồn Java:**
```java
public class IntegerToRoman {
    public String intToRoman(int num) {
        int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] symbols = {"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < values.length; i++) {
            while (num >= values[i]) {
                num -= values[i];
                sb.append(symbols[i]);
            }
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(1)$, Không gian $O(1)$.

## 18. Longest Substring Without Repeating Characters
**Đề bài chi tiết:** Cho chuỗi $s$, tìm độ dài của chuỗi con dài nhất mà không có bất kỳ ký tự nào lặp lại.
**Phân tích thuật toán:** Kỹ thuật Sliding Window. Sử dụng HashMap lưu vị trí xuất hiện gần nhất của ký tự. Dùng hai con trỏ `left` và `right`. Nếu gặp ký tự lặp lại, cập nhật `left` đến vị trí sau ký tự lặp lại cuối cùng (nếu vị trí này nằm sau `left` hiện tại).
**Mã nguồn Java:**
```java
import java.util.*;

public class LongestSubstring {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); 
        for (int right = 0, left = 0; right < n; right++) {
            char c = s.charAt(right);
            if (map.containsKey(c)) {
                left = Math.max(map.get(c), left);
            }
            ans = Math.max(ans, right - left + 1);
            map.put(c, right + 1);
        }
        return ans;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(\min(m, n))$ với $m$ là bảng chữ cái.

## 19. Valid Parentheses
**Đề bài chi tiết:** Cho chuỗi $s$ chỉ chứa dấu ngoặc `()`, `{}`, `[]`, xác định chuỗi có hợp lệ hay không.
**Phân tích thuật toán:** Dùng Stack. Duyệt từng ký tự: nếu là dấu mở thì push dấu đóng tương ứng vào stack. Nếu là dấu đóng, pop từ stack ra và kiểm tra xem có khớp không.
**Mã nguồn Java:**
```java
import java.util.*;

public class ValidParentheses {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c == '(') stack.push(')');
            else if (c == '{') stack.push('}');
            else if (c == '[') stack.push(']');
            else if (stack.isEmpty() || stack.pop() != c) {
                return false;
            }
        }
        return stack.isEmpty();
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(n)$.

## 20. Generate Parentheses
**Đề bài chi tiết:** Cho số $n$, sinh tất cả các tổ hợp dấu ngoặc đơn hợp lệ (có $n$ cặp).
**Phân tích thuật toán:** Dùng Backtracking. Xây dựng chuỗi đệ quy bằng cách thêm dấu `(` nếu số ngoặc mở đã thêm nhỏ hơn $n$, và thêm dấu `)` nếu số ngoặc đóng nhỏ hơn số ngoặc mở.
**Mã nguồn Java:**
```java
import java.util.*;

public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, "", 0, 0, n);
        return result;
    }
    
    private void backtrack(List<String> result, String current, int open, int close, int max) {
        if (current.length() == max * 2) {
            result.add(current);
            return;
        }
        
        if (open < max) {
            backtrack(result, current + "(", open + 1, close, max);
        }
        if (close < open) {
            backtrack(result, current + ")", open, close + 1, max);
        }
    }
}
```
**Độ phức tạp:** Thời gian $O(4^n / \sqrt{n})$, Không gian $O(n)$ do ngăn xếp đệ quy.

## 21. Letter Combinations of a Phone Number
**Đề bài chi tiết:** Cho một chuỗi chứa các chữ số từ `2-9`, trả về tất cả các tổ hợp chữ cái có thể có mà các chữ số đó đại diện. Ánh xạ các chữ số tới các chữ cái giống như trên nút bấm điện thoại (ví dụ: `2` -> "abc", `3` -> "def").
**Phân tích thuật toán:** Sử dụng Backtracking. Xây dựng một mảng `mapping` chứa các chuỗi tương ứng với mỗi chữ số. Khởi tạo một chuỗi rỗng. Tại mỗi chữ số trong chuỗi đầu vào, duyệt qua các chữ cái tương ứng và đệ quy xây dựng chuỗi kết quả.
**Mã nguồn Java:**
```java
import java.util.*;

public class LetterCombinations {
    private String[] mapping = new String[] {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    
    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits == null || digits.isEmpty()) return result;
        backtrack(result, digits, new StringBuilder(), 0);
        return result;
    }
    
    private void backtrack(List<String> result, String digits, StringBuilder current, int index) {
        if (index == digits.length()) {
            result.add(current.toString());
            return;
        }
        String letters = mapping[digits.charAt(index) - '0'];
        for (char c : letters.toCharArray()) {
            current.append(c);
            backtrack(result, digits, current, index + 1);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```
**Độ phức tạp:** Thời gian $O(4^n \cdot n)$ với $n$ là độ dài chuỗi đầu vào (do tối đa 4 chữ cái cho số 7 và 9). Không gian $O(n)$ cho Call Stack đệ quy.

## 22. Word Break
**Đề bài chi tiết:** Cho một chuỗi $s$ và một danh sách các từ $wordDict$, trả về `true` nếu $s$ có thể được phân đoạn thành một chuỗi các từ ngăn cách bằng dấu cách sao cho mỗi từ đều nằm trong $wordDict$. Các từ trong từ điển có thể sử dụng nhiều lần.
**Phân tích thuật toán:** Sử dụng Quy hoạch động (Dynamic Programming). Gọi `dp[i]` là biến boolean thể hiện xem chuỗi con từ $0$ đến $i$ có thể tách được hay không. Cập nhật `dp[i]` bằng cách kiểm tra tất cả các vị trí $j$ từ $0$ đến $i$, nếu `dp[j] == true` và đoạn cắt từ $j$ đến $i$ nằm trong `wordDict`, thì `dp[i] = true`.
**Mã nguồn Java:**
```java
import java.util.*;

public class WordBreak {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> dict = new HashSet<>(wordDict);
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;
        
        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && dict.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[s.length()];
    }
}
```
**Độ phức tạp:** Thời gian $O(n^3)$ (duyệt $i$, duyệt $j$, và hàm `substring`), Không gian $O(n)$ cho mảng `dp`.

## 23. Decode String
**Đề bài chi tiết:** Cho một chuỗi mã hóa, trả về chuỗi đã được giải mã. Quy tắc mã hóa là: `k[encoded_string]`, trong đó chuỗi bên trong ngoặc vuông lặp lại chính xác $k$ lần.
**Phân tích thuật toán:** Dùng hai Stack: một stack `countStack` để chứa số lần lặp và một stack `stringStack` để chứa chuỗi hiện tại. Duyệt từng ký tự, gặp số thì tính toán biến `count`, gặp chữ thì nối vào chuỗi hiện tại, gặp `[` thì đẩy `count` và chuỗi hiện tại vào stack, gặp `]` thì pop ra và nhân chuỗi lên `count` lần sau đó nối vào chuỗi cha.
**Mã nguồn Java:**
```java
import java.util.*;

public class DecodeString {
    public String decodeString(String s) {
        Stack<Integer> countStack = new Stack<>();
        Stack<StringBuilder> stringStack = new Stack<>();
        StringBuilder currentString = new StringBuilder();
        int k = 0;
        
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                k = k * 10 + (c - '0');
            } else if (c == '[') {
                countStack.push(k);
                stringStack.push(currentString);
                currentString = new StringBuilder();
                k = 0;
            } else if (c == ']') {
                StringBuilder decoded = stringStack.pop();
                int currentK = countStack.pop();
                for (int i = 0; i < currentK; i++) {
                    decoded.append(currentString);
                }
                currentString = decoded;
            } else {
                currentString.append(c);
            }
        }
        return currentString.toString();
    }
}
```
**Độ phức tạp:** Thời gian $O(N)$ với $N$ là kích thước chuỗi kết quả cuối cùng. Không gian $O(M)$ với $M$ là số lượng ngoặc vuông lồng nhau.

## 24. Minimum Window Substring
**Đề bài chi tiết:** Cho hai chuỗi $s$ và $t$, trả về chuỗi con ngắn nhất trong $s$ chứa tất cả các ký tự của $t$. Nếu không có, trả về chuỗi rỗng `""`.
**Phân tích thuật toán:** Sử dụng Kỹ thuật Sliding Window cùng hai con trỏ `left` và `right`. Mảng tần số đếm số lượng các ký tự cần thiết từ $t$. `right` mở rộng cửa sổ bằng cách giảm tần số ký tự. Khi toàn bộ ký tự được bao phủ (`count == t.length()`), tăng `left` để thu hẹp cửa sổ tìm cửa sổ nhỏ nhất.
**Mã nguồn Java:**
```java
public class MinimumWindowSubstring {
    public String minWindow(String s, String t) {
        if (s == null || t == null || s.length() == 0 || t.length() == 0) return "";
        int[] map = new int[128];
        for (char c : t.toCharArray()) map[c]++;
        
        int left = 0, right = 0, minLen = Integer.MAX_VALUE, start = 0, count = t.length();
        
        while (right < s.length()) {
            if (map[s.charAt(right++)]-- > 0) {
                count--;
            }
            while (count == 0) {
                if (right - left < minLen) {
                    minLen = right - left;
                    start = left;
                }
                if (map[s.charAt(left++)]++ == 0) {
                    count++;
                }
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(start, start + minLen);
    }
}
```
**Độ phức tạp:** Thời gian $O(|S| + |T|)$, Không gian $O(1)$ (mảng cố định 128 phần tử ASCII).

## 25. Edit Distance
**Đề bài chi tiết:** Cho hai chuỗi $word1$ và $word2$, tìm số thao tác tối thiểu cần thiết để chuyển đổi $word1$ thành $word2$. Các thao tác cho phép: Chèn ký tự, Xóa ký tự, Thay thế ký tự.
**Phân tích thuật toán:** Quy hoạch động (DP). `dp[i][j]` là khoảng cách chỉnh sửa giữa chuỗi con độ dài $i$ của $word1$ và chuỗi con độ dài $j$ của $word2$. Nếu $word1[i-1] == word2[j-1]$, thì `dp[i][j] = dp[i-1][j-1]`. Ngược lại, tính min của 3 phép: thay thế, xóa, hoặc chèn cộng thêm 1.
**Mã nguồn Java:**
```java
public class EditDistance {
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
**Độ phức tạp:** Thời gian $O(m \times n)$, Không gian $O(m \times n)$.

## 26. Distinct Subsequences
**Đề bài chi tiết:** Cho hai chuỗi $s$ và $t$, trả về số lượng các chuỗi con phân biệt của $s$ khớp với $t$.
**Phân tích thuật toán:** Quy hoạch động. `dp[i][j]` là số cách tạo chuỗi $t[0..j-1]$ từ $s[0..i-1]$. Nếu $s[i-1] == t[j-1]$, ta có thể chọn hoặc không chọn ký tự $s[i-1]$ (do đó `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]`). Nếu không bằng, ta bắt buộc bỏ qua $s[i-1]$ (`dp[i][j] = dp[i-1][j]`).
**Mã nguồn Java:**
```java
public class DistinctSubsequences {
    public int numDistinct(String s, String t) {
        int m = s.length();
        int n = t.length();
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
**Độ phức tạp:** Thời gian $O(m \times n)$, Không gian $O(m \times n)$.

## 27. Wildcard Matching
**Đề bài chi tiết:** Cho chuỗi đầu vào $s$ và mẫu $p$, triển khai kiểm tra mẫu khớp hoàn toàn với quy tắc ký tự đại diện: `?` khớp 1 ký tự bất kỳ, `*` khớp một dãy (có thể rỗng) các ký tự bất kỳ.
**Phân tích thuật toán:** Quy hoạch động. `dp[i][j]` kiểm tra $s[0..i-1]$ và $p[0..j-1]$. Nếu $p[j-1] == '*'$, `dp[i][j]` có thể là chuỗi rỗng (`dp[i][j-1]`) hoặc khớp ít nhất 1 ký tự (`dp[i-1][j]`). Ngược lại, nếu ký tự bằng nhau hoặc bằng `?`, `dp[i][j] = dp[i-1][j-1]`.
**Mã nguồn Java:**
```java
public class WildcardMatching {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        
        for (int j = 1; j <= n; j++) {
            if (p.charAt(j - 1) == '*') dp[0][j] = dp[0][j - 1];
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
**Độ phức tạp:** Thời gian $O(m \times n)$, Không gian $O(m \times n)$.

## 28. Regular Expression Matching
**Đề bài chi tiết:** Cho một chuỗi $s$ và biểu thức chính quy $p$, cài đặt hàm so khớp hoàn toàn với: `.` khớp với 1 ký tự đơn, `*` khớp với 0 hoặc nhiều lần ký tự ngay liền trước nó.
**Phân tích thuật toán:** Quy hoạch động tương tự Wildcard Matching. Tại $p[j-1] == '*'$, ký tự liền trước là $p[j-2]$. Ta có thể khớp 0 lần (`dp[i][j-2]`). Nếu $p[j-2]$ khớp với $s[i-1]$ (hoặc là `.`), ta có thể khớp 1 hoặc nhiều lần (`dp[i-1][j]`).
**Mã nguồn Java:**
```java
public class RegularExpressionMatching {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;
        
        for (int j = 1; j <= n; j++) {
            if (p.charAt(j - 1) == '*') dp[0][j] = dp[0][j - 2];
        }
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 2];
                    if (p.charAt(j - 2) == '.' || p.charAt(j - 2) == s.charAt(i - 1)) {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                } else if (p.charAt(j - 1) == '.' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[m][n];
    }
}
```
**Độ phức tạp:** Thời gian $O(m \times n)$, Không gian $O(m \times n)$.

## 29. Longest Valid Parentheses
**Đề bài chi tiết:** Cho chuỗi chỉ chứa `(` và `)`, tìm độ dài của chuỗi con chứa các dấu ngoặc hợp lệ dài nhất.
**Phân tích thuật toán:** Sử dụng Stack. Khởi tạo stack và thêm chỉ số -1 làm mốc cơ sở. Duyệt mảng, nếu là `(`, push chỉ số vào. Nếu là `)`, pop phần tử ra. Nếu stack trống sau khi pop, push chỉ số hiện tại làm mốc mới. Nếu không rỗng, cập nhật độ dài tối đa.
**Mã nguồn Java:**
```java
import java.util.*;

public class LongestValidParentheses {
    public int longestValidParentheses(String s) {
        int maxLen = 0;
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else {
                stack.pop();
                if (stack.isEmpty()) {
                    stack.push(i);
                } else {
                    maxLen = Math.max(maxLen, i - stack.peek());
                }
            }
        }
        return maxLen;
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(n)$.

## 30. Simplify Path
**Đề bài chi tiết:** Cho một chuỗi $path$ mô tả đường dẫn tuyệt đối, hãy đơn giản hóa nó. Loại bỏ dấu `/` thừa, `.` (thư mục hiện tại) và xử lý `..` (lùi về thư mục cha).
**Phân tích thuật toán:** Sử dụng phương thức `.split("/")` để chia chuỗi thành các phần. Duyệt qua các thành phần: bỏ qua các chuỗi rỗng và `.`. Gặp `..` thì pop thư mục cha ra khỏi Stack nếu Stack không rỗng. Còn lại thì push vào Stack. Cuối cùng nối lại các phần tử với `/`.
**Mã nguồn Java:**
```java
import java.util.*;

public class SimplifyPath {
    public String simplifyPath(String path) {
        Stack<String> stack = new Stack<>();
        String[] components = path.split("/");
        
        for (String dir : components) {
            if (dir.equals("..")) {
                if (!stack.isEmpty()) {
                    stack.pop();
                }
            } else if (!dir.equals("") && !dir.equals(".")) {
                stack.push(dir);
            }
        }
        
        StringBuilder res = new StringBuilder();
        for (String dir : stack) {
            res.append("/").append(dir);
        }
        return res.length() > 0 ? res.toString() : "/";
    }
}
```
**Độ phức tạp:** Thời gian $O(n)$, Không gian $O(n)$.
