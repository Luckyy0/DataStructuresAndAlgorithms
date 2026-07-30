# Bài tập Backtracking with Strings (30 Bài tập)

## 1. Palindrome Partitioning (Phân hoạch Palindrome)
**Đề bài chi tiết:** Cho chuỗi `s`, phân hoạch `s` sao cho mọi chuỗi con trong phân hoạch đều là chuỗi đối xứng (palindrome). Trả về tất cả các cách phân hoạch có thể có.
**Phân tích thuật toán:**
Dùng quay lui để thử cắt chuỗi tại mọi vị trí `i`. Nếu `s[start...i]` là đối xứng, thêm nó vào `path`, rồi đệ quy phân hoạch tiếp `s[i+1...n]`.
**Độ phức tạp:**
- Time: $O(N \cdot 2^N)$ (tệ nhất là "aaaa...").
- Space: $O(N)$ (độ sâu đệ quy).
**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), res);
        return res;
    }
    
    private void backtrack(String s, int start, List<String> path, List<List<String>> res) {
        if (start == s.length()) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (isPalindrome(s, start, i)) {
                path.add(s.substring(start, i + 1));
                backtrack(s, i + 1, path, res);
                path.remove(path.size() - 1);
            }
        }
    }
    
    private boolean isPalindrome(String s, int l, int r) {
        while (l < r) {
            if (s.charAt(l++) != s.charAt(r--)) return false;
        }
        return true;
    }
}
```

## 2. Letter Combinations of a Phone Number (Tổ hợp chữ cái từ bàn phím điện thoại)
**Đề bài chi tiết:** Cho một chuỗi chứa các số từ `2-9`. Trả về tất cả các tổ hợp chữ cái có thể được biểu diễn từ các số đó theo bàn phím điện thoại cũ.
**Phân tích thuật toán:**
Ánh xạ từng chữ số ra mảng chữ cái. Với mỗi chữ số ở index `i`, duyệt qua các chữ cái tương ứng, gắn vào chuỗi kết quả và đệ quy đến index `i+1`.
**Độ phức tạp:**
- Time: $O(4^N \cdot N)$ (vì một số phím có 4 chữ cái, $N$ là phép tạo chuỗi).
- Space: $O(N)$ (chiều sâu đệ quy).
**Mã nguồn Java:**
```java
class Solution {
    private String[] mapping = new String[]{"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits == null || digits.length() == 0) return res;
        backtrack(res, digits, new StringBuilder(), 0);
        return res;
    }
    
    private void backtrack(List<String> res, String digits, StringBuilder path, int index) {
        if (index == digits.length()) {
            res.add(path.toString());
            return;
        }
        String letters = mapping[digits.charAt(index) - '0'];
        for (char c : letters.toCharArray()) {
            path.append(c);
            backtrack(res, digits, path, index + 1);
            path.deleteCharAt(path.length() - 1);
        }
    }
}
```

## 3. Restore IP Addresses (Khôi phục địa chỉ IP)
**Đề bài chi tiết:** Cho chuỗi chứa toàn chữ số, khôi phục thành các địa chỉ IPv4 hợp lệ bằng cách chèn 3 dấu chấm. IP không được chứa số 0 đứng đầu nếu giá trị lớn hơn 0 (ví dụ `01` là sai) và mỗi số từ 0 đến 255.
**Phân tích thuật toán:**
Quay lui 4 bước, mỗi bước lấy từ 1 đến 3 ký tự. Cắt tỉa: đoạn không được quá 255, không bắt đầu bằng "0" trừ khi là "0", phải đủ 4 đoạn và dùng hết chuỗi.
**Độ phức tạp:**
- Time: $O(3^4) = O(1)$ (tối đa 3 lựa chọn trong 4 phần).
- Space: $O(1)$ (độ sâu đệ quy tối đa 4).
**Mã nguồn Java:**
```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> res = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), res);
        return res;
    }
    
    private void backtrack(String s, int start, List<String> path, List<String> res) {
        if (path.size() == 4) {
            if (start == s.length()) res.add(String.join(".", path));
            return;
        }
        for (int i = start; i < Math.min(start + 3, s.length()); i++) {
            String part = s.substring(start, i + 1);
            if ((part.length() > 1 && part.startsWith("0")) || Integer.parseInt(part) > 255) continue;
            path.add(part);
            backtrack(s, i + 1, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```

## 4. Generate Parentheses (Sinh dấu ngoặc)
**Đề bài chi tiết:** Cho số nguyên `n` chỉ số cặp ngoặc tròn, hãy sinh tất cả các chuỗi ngoặc đúng.
**Phân tích thuật toán:**
Quay lui thêm `(` hoặc `)`. Chỉ thêm `(` nếu số ngoặc mở `< n`. Chỉ thêm `)` nếu số ngoặc đóng `< số ngoặc mở`.
**Độ phức tạp:**
- Time: $O(\frac{4^n}{\sqrt{n}})$ (số Catalan thứ $N$).
- Space: $O(N)$ (độ sâu đệ quy $2N$).
**Mã nguồn Java:**
```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        backtrack(res, new StringBuilder(), 0, 0, n);
        return res;
    }
    
    private void backtrack(List<String> res, StringBuilder path, int open, int close, int max) {
        if (path.length() == max * 2) {
            res.add(path.toString());
            return;
        }
        if (open < max) {
            path.append('(');
            backtrack(res, path, open + 1, close, max);
            path.deleteCharAt(path.length() - 1);
        }
        if (close < open) {
            path.append(')');
            backtrack(res, path, open, close + 1, max);
            path.deleteCharAt(path.length() - 1);
        }
    }
}
```

## 5. Split a String Into the Max Number of Unique Substrings (Cắt chuỗi thành nhiều chuỗi con duy nhất nhất)
**Đề bài chi tiết:** Cho chuỗi `s`, chia chuỗi thành số lượng tối đa các chuỗi con duy nhất (không trùng lặp).
**Phân tích thuật toán:**
Dùng `HashSet` để lưu các chuỗi con đã cắt. Duyệt các điểm cắt, nếu chuỗi con mới chưa có trong tập hợp thì đưa vào và tiếp tục đệ quy.
**Độ phức tạp:**
- Time: $O(N \cdot 2^N)$.
- Space: $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    int maxSplits = 0;
    public int maxUniqueSplit(String s) {
        backtrack(s, 0, new HashSet<>());
        return maxSplits;
    }
    
    private void backtrack(String s, int start, HashSet<String> seen) {
        if (start == s.length()) {
            maxSplits = Math.max(maxSplits, seen.size());
            return;
        }
        if (seen.size() + (s.length() - start) <= maxSplits) return; // Pruning
        
        for (int i = start; i < s.length(); i++) {
            String sub = s.substring(start, i + 1);
            if (!seen.contains(sub)) {
                seen.add(sub);
                backtrack(s, i + 1, seen);
                seen.remove(sub);
            }
        }
    }
}
```

## 6. Word Break II (Ngắt từ II)
**Đề bài chi tiết:** Cho chuỗi `s` và từ điển `wordDict`. Trả về tất cả các câu tạo được từ việc chia chuỗi `s` bằng các từ trong từ điển.
**Phân tích thuật toán:**
Sử dụng Quay lui với Cắt tỉa hoặc Memoization. Cắt `s` từ trái sang phải, nếu đoạn cắt có trong từ điển thì lưu lại và tìm tiếp phần còn lại. Dùng HashMap để lưu kết quả các chuỗi hậu tố.
**Độ phức tạp:**
- Time: $O(2^N \cdot N)$.
- Space: $O(2^N \cdot N)$.
**Mã nguồn Java:**
```java
class Solution {
    Map<String, List<String>> memo = new HashMap<>();
    public List<String> wordBreak(String s, List<String> wordDict) {
        return backtrack(s, new HashSet<>(wordDict));
    }
    
    private List<String> backtrack(String s, Set<String> wordDict) {
        if (memo.containsKey(s)) return memo.get(s);
        List<String> res = new ArrayList<>();
        if (s.isEmpty()) {
            res.add("");
            return res;
        }
        for (String word : wordDict) {
            if (s.startsWith(word)) {
                List<String> subList = backtrack(s.substring(word.length()), wordDict);
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

## 7. Additive Number (Số cộng gộp)
**Đề bài chi tiết:** Một số là additive nếu chứa ít nhất 3 chữ số và mỗi số từ thứ ba bằng tổng hai số trước đó. Kiểm tra chuỗi `num` có phải additive không.
**Phân tích thuật toán:**
Chọn 2 số đầu tiên (độ dài vòng lặp 1 và 2). Viết hàm đệ quy sinh số thứ 3 và so sánh với phần còn lại của chuỗi, lưu ý không được chứa "0" ở đầu nếu độ dài > 1.
**Độ phức tạp:**
- Time: $O(N^3)$.
- Space: $O(N)$.
**Mã nguồn Java:**
```java
import java.math.BigInteger;

class Solution {
    public boolean isAdditiveNumber(String num) {
        int n = num.length();
        for (int i = 1; i <= n / 2; i++) {
            if (num.charAt(0) == '0' && i > 1) break;
            for (int j = 1; Math.max(i, j) <= n - i - j; j++) {
                if (num.charAt(i) == '0' && j > 1) break;
                String n1 = num.substring(0, i);
                String n2 = num.substring(i, i + j);
                if (isValid(n1, n2, i + j, num)) return true;
            }
        }
        return false;
    }
    
    private boolean isValid(String n1, String n2, int start, String num) {
        if (start == num.length()) return true;
        BigInteger num1 = new BigInteger(n1);
        BigInteger num2 = new BigInteger(n2);
        String next = num1.add(num2).toString();
        if (!num.startsWith(next, start)) return false;
        return isValid(n2, next, start + next.length(), num);
    }
}
```

## 8. Brace Expansion (Khai triển ngoặc nhọn)
**Đề bài chi tiết:** Cho một chuỗi cấu trúc `{a,b}c{d,e}f`. Trả về danh sách các chuỗi hợp lệ sinh ra bằng cách lấy 1 từ trong mỗi ngoặc nhọn kết hợp với ngoài ngoặc. (Thường theo thứ tự từ điển).
**Phân tích thuật toán:**
Phân tích chuỗi gốc thành danh sách các mảng/danh sách. Sau đó dùng DFS/Backtracking để tổ hợp các phần tử lại với nhau.
**Độ phức tạp:**
- Time: $O(K^M \cdot \log(K^M))$ với $M$ là số nhóm, $K$ là số phần tử mỗi nhóm.
- Space: $O(K^M)$.
**Mã nguồn Java:**
```java
class Solution {
    public String[] expand(String s) {
        List<List<Character>> groups = new ArrayList<>();
        int i = 0;
        while (i < s.length()) {
            List<Character> group = new ArrayList<>();
            if (s.charAt(i) == '{') {
                i++;
                while (s.charAt(i) != '}') {
                    if (s.charAt(i) != ',') group.add(s.charAt(i));
                    i++;
                }
            } else {
                group.add(s.charAt(i));
            }
            Collections.sort(group);
            groups.add(group);
            i++;
        }
        List<String> res = new ArrayList<>();
        backtrack(groups, 0, new StringBuilder(), res);
        return res.toArray(new String[0]);
    }
    
    private void backtrack(List<List<Character>> groups, int index, StringBuilder path, List<String> res) {
        if (index == groups.size()) {
            res.add(path.toString());
            return;
        }
        for (char c : groups.get(index)) {
            path.append(c);
            backtrack(groups, index + 1, path, res);
            path.deleteCharAt(path.length() - 1);
        }
    }
}
```

## 9. Construct the Lexicographically Largest Valid Sequence (Dựng chuỗi hợp lệ lớn nhất theo thứ tự từ điển)
**Đề bài chi tiết:** Cho nguyên `n`. Tìm chuỗi dài `2n-1` chứa 1 số '1', các số từ '2' tới 'n' xuất hiện 2 lần cách nhau khoảng bằng giá trị của nó, sao cho dãy là lớn nhất.
**Phân tích thuật toán:**
Dùng mảng độ dài `2n-1` khởi tạo bằng 0. Điền từng vị trí từ trái sang phải, luôn thử số lớn nhất (từ $n$ đến 1) chưa dùng. Nếu số $x > 1$, kiểm tra xem ô hiện tại và ô cách đó $x$ bước có trống không.
**Độ phức tạp:**
- Time: $O(N!)$ worst case nhưng rất nhỏ do Pruning.
- Space: $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public int[] constructDistancedSequence(int n) {
        int[] res = new int[n * 2 - 1];
        boolean[] used = new boolean[n + 1];
        backtrack(res, used, n, 0);
        return res;
    }
    
    private boolean backtrack(int[] res, boolean[] used, int n, int index) {
        if (index == res.length) return true;
        if (res[index] != 0) return backtrack(res, used, n, index + 1);
        
        for (int i = n; i >= 1; i--) {
            if (used[i]) continue;
            if (i == 1) {
                res[index] = 1;
                used[1] = true;
                if (backtrack(res, used, n, index + 1)) return true;
                res[index] = 0;
                used[1] = false;
            } else if (index + i < res.length && res[index + i] == 0) {
                res[index] = i;
                res[index + i] = i;
                used[i] = true;
                if (backtrack(res, used, n, index + 1)) return true;
                res[index] = 0;
                res[index + i] = 0;
                used[i] = false;
            }
        }
        return false;
    }
}
```

## 10. Find Unique Binary String (Tìm chuỗi nhị phân duy nhất)
**Đề bài chi tiết:** Cho mảng `nums` chứa `n` chuỗi nhị phân độc nhất, mỗi chuỗi độ dài `n`. Trả về một chuỗi nhị phân độ dài `n` không xuất hiện trong `nums`.
**Phân tích thuật toán:**
Cách 1 là dùng Cantor's Diagonalization $O(N)$.
Cách 2 là Backtracking: thử sinh từ trái sang phải ('0' rồi '1'), kiểm tra chuỗi có tồn tại trong `HashSet` của mảng gốc chưa.
**Độ phức tạp:**
- Time: Dùng Backtracking tối đa $O(N)$, tối ưu nhất $O(N)$.
- Space: $O(N)$.
**Mã nguồn Java:**
```java
class Solution {
    public String findDifferentBinaryString(String[] nums) {
        Set<String> set = new HashSet<>(Arrays.asList(nums));
        return backtrack(new StringBuilder(), nums.length, set);
    }
    
    private String backtrack(StringBuilder path, int n, Set<String> set) {
        if (path.length() == n) {
            String s = path.toString();
            return set.contains(s) ? null : s;
        }
        for (char c : new char[]{'0', '1'}) {
            path.append(c);
            String res = backtrack(path, n, set);
            if (res != null) return res;
            path.deleteCharAt(path.length() - 1);
        }
        return null;
    }
}
```

## 11. Letter Case Permutation (Hoán vị chữ hoa chữ thường)
**Đề bài chi tiết:** Cho chuỗi `s`, với mỗi chữ cái bạn có thể chọn chuyển thành chữ hoa hoặc chữ thường. Sinh ra tất cả các chuỗi có thể tạo thành.
**Phân tích thuật toán:**
Dùng Backtracking duyệt qua từng ký tự của chuỗi. Nếu là chữ số thì giữ nguyên và đệ quy tiếp. Nếu là chữ cái, ta tạo ra 2 nhánh: một nhánh chuyển thành chữ thường, một nhánh chuyển thành chữ hoa.
**Độ phức tạp:**
- Time: $O(2^L \cdot N)$ với $L$ là số lượng chữ cái trong chuỗi.
- Space: $O(N)$ độ sâu đệ quy và lưu trữ chuỗi.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> letterCasePermutation(String s) {
        List<String> res = new ArrayList<>();
        backtrack(s.toCharArray(), 0, res);
        return res;
    }
    
    private void backtrack(char[] chars, int index, List<String> res) {
        if (index == chars.length) {
            res.add(new String(chars));
            return;
        }
        if (Character.isLetter(chars[index])) {
            chars[index] = Character.toLowerCase(chars[index]);
            backtrack(chars, index + 1, res);
            
            chars[index] = Character.toUpperCase(chars[index]);
            backtrack(chars, index + 1, res);
        } else {
            backtrack(chars, index + 1, res);
        }
    }
}
```

## 12. Remove Invalid Parentheses (Loại bỏ ngoặc không hợp lệ)
**Đề bài chi tiết:** Cho một chuỗi `s` chứa dấu ngoặc và chữ cái, loại bỏ số lượng dấu ngoặc ít nhất sao cho chuỗi trở nên hợp lệ. Trả về tất cả các chuỗi hợp lệ có thể có.
**Phân tích thuật toán:**
Đầu tiên đếm số lượng ngoặc mở (`leftRemove`) và ngoặc đóng (`rightRemove`) dư thừa cần phải xóa. Sau đó dùng Quay lui để thử xóa từng ngoặc. Để tránh trùng lặp, nếu các ngoặc giống nhau đứng cạnh nhau, ta chỉ thử xóa ngoặc đầu tiên. Cuối cùng kiểm tra tính hợp lệ của chuỗi nếu đã xóa đủ số lượng ngoặc cần thiết.
**Độ phức tạp:**
- Time: $O(2^N)$ trong trường hợp xấu nhất, nhưng nhờ cắt tỉa số lượng nhánh giảm đi rất nhiều.
- Space: $O(N)$ do dùng Call Stack đệ quy.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        List<String> res = new ArrayList<>();
        int leftRemove = 0, rightRemove = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                leftRemove++;
            } else if (c == ')') {
                if (leftRemove > 0) leftRemove--;
                else rightRemove++;
            }
        }
        backtrack(s, 0, leftRemove, rightRemove, res);
        return res;
    }
    
    private void backtrack(String s, int start, int leftRemove, int rightRemove, List<String> res) {
        if (leftRemove == 0 && rightRemove == 0) {
            if (isValid(s)) res.add(s);
            return;
        }
        for (int i = start; i < s.length(); i++) {
            if (i > start && s.charAt(i) == s.charAt(i - 1)) continue; // Tránh trùng lặp
            
            if (s.charAt(i) == '(' && leftRemove > 0) {
                backtrack(s.substring(0, i) + s.substring(i + 1), i, leftRemove - 1, rightRemove, res);
            }
            if (s.charAt(i) == ')' && rightRemove > 0) {
                backtrack(s.substring(0, i) + s.substring(i + 1), i, leftRemove, rightRemove - 1, res);
            }
        }
    }
    
    private boolean isValid(String s) {
        int count = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') count++;
            else if (c == ')') count--;
            if (count < 0) return false;
        }
        return count == 0;
    }
}
```

## 13. Expression Add Operators (Thêm toán tử vào biểu thức)
**Đề bài chi tiết:** Cho một chuỗi chứa các chữ số `num` và một số nguyên `target`. Hãy trả về tất cả các biểu thức hợp lệ bằng cách chèn thêm các toán tử `+`, `-`, `*` vào giữa các chữ số sao cho giá trị biểu thức bằng `target`.
**Phân tích thuật toán:**
Ta dùng Backtracking sinh lần lượt các số hạng mới. Lưu lại tổng hiện tại (`eval`) và số hạng trước đó (`multed`). Đối với phép cộng/trừ, ta cập nhật dễ dàng. Tuy nhiên với phép nhân, vì nó có độ ưu tiên cao hơn, ta cần lấy tổng hiện tại trừ đi `multed`, rồi cộng với `multed * currentVal`.
**Độ phức tạp:**
- Time: $O(N \cdot 4^N)$ vì có 4 lựa chọn (không đặt gì, đặt `+`, `-`, `*`).
- Space: $O(N)$ là kích thước của stack đệ quy.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<>();
        if (num == null || num.length() == 0) return res;
        backtrack(res, "", num, target, 0, 0, 0);
        return res;
    }
    
    private void backtrack(List<String> res, String path, String num, int target, int pos, long eval, long multed) {
        if (pos == num.length()) {
            if (target == eval) res.add(path);
            return;
        }
        for (int i = pos; i < num.length(); i++) {
            if (i != pos && num.charAt(pos) == '0') break; // Số không được bắt đầu bằng 0
            
            long cur = Long.parseLong(num.substring(pos, i + 1));
            if (pos == 0) {
                backtrack(res, path + cur, num, target, i + 1, cur, cur);
            } else {
                backtrack(res, path + "+" + cur, num, target, i + 1, eval + cur, cur);
                backtrack(res, path + "-" + cur, num, target, i + 1, eval - cur, -cur);
                backtrack(res, path + "*" + cur, num, target, i + 1, eval - multed + multed * cur, multed * cur);
            }
        }
    }
}
```

## 14. Ambiguous Coordinates (Tọa độ mơ hồ)
**Đề bài chi tiết:** Cho một chuỗi biểu diễn một tọa độ bị bỏ đi dấu phẩy phân tách và các dấu thập phân (ví dụ `"(123)"`). Hãy trả về tất cả các tọa độ hợp lệ có thể có `(X, Y)`. Một số không được bắt đầu bằng `0` (trừ số `0` đứng một mình hoặc phần nguyên là `0` trước dấu phẩy), và không có số `0` vô nghĩa ở cuối phần thập phân.
**Phân tích thuật toán:**
Duyệt mọi vị trí để chèn dấu phẩy phân tách chuỗi thành 2 nửa `X` và `Y`. Với mỗi nửa, tiếp tục dùng một hàm sinh các cách chèn dấu thập phân hợp lệ. Sau đó kết hợp tất cả các cặp `(X, Y)` hợp lệ lại với nhau.
**Độ phức tạp:**
- Time: $O(N^3)$ với $N$ là chiều dài chuỗi. Cắt chuỗi thành 2 đoạn mất $O(N)$, mỗi đoạn sinh số thập phân tốn $O(N)$, phép gộp là $O(N)$.
- Space: $O(N^3)$ cho danh sách kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> ambiguousCoordinates(String s) {
        List<String> res = new ArrayList<>();
        s = s.substring(1, s.length() - 1);
        for (int i = 1; i < s.length(); i++) {
            List<String> left = getValidParts(s.substring(0, i));
            List<String> right = getValidParts(s.substring(i));
            for (String l : left) {
                for (String r : right) {
                    res.add("(" + l + ", " + r + ")");
                }
            }
        }
        return res;
    }
    
    private List<String> getValidParts(String s) {
        List<String> res = new ArrayList<>();
        int n = s.length();
        if (n == 0) return res;
        if (n == 1) {
            res.add(s);
            return res;
        }
        if (s.charAt(0) == '0' && s.charAt(n - 1) == '0') return res;
        if (s.charAt(0) == '0') {
            res.add("0." + s.substring(1));
            return res;
        }
        if (s.charAt(n - 1) == '0') {
            res.add(s);
            return res;
        }
        res.add(s); // Dạng số nguyên
        for (int i = 1; i < n; i++) {
            res.add(s.substring(0, i) + "." + s.substring(i)); // Dạng số thập phân
        }
        return res;
    }
}
```

## 15. Regular Expression Matching (Khớp biểu thức chính quy)
**Đề bài chi tiết:** Cài đặt biểu thức chính quy hỗ trợ `.` (khớp với một ký tự bất kỳ) và `*` (khớp với 0 hoặc nhiều ký tự đứng trước nó). Xác định chuỗi `s` có khớp với mẫu `p` không.
**Phân tích thuật toán:**
Nếu `p` chứa `*` ở vị trí thứ hai, ta có 2 nhánh Backtracking:
1. Bỏ qua ký tự và dấu `*` trong `p` (coi như `*` khớp 0 lần).
2. Nếu ký tự đầu tiên khớp, thử giữ nguyên mẫu `p` và di chuyển trên `s` (coi như `*` khớp nhiều lần).
Quy hoạch động (Memoization) giúp tối ưu thời gian.
**Độ phức tạp:**
- Time: $O(S \cdot P)$ với memoization, nếu dùng Backtracking thuần thì tốn $O(2^{\min(S, P)})$.
- Space: $O(S \cdot P)$ (nếu dùng DP memo).
**Mã nguồn Java:**
```java
class Solution {
    Boolean[][] memo;
    public boolean isMatch(String s, String p) {
        memo = new Boolean[s.length() + 1][p.length() + 1];
        return backtrack(s, p, 0, 0);
    }
    
    private boolean backtrack(String s, String p, int i, int j) {
        if (memo[i][j] != null) return memo[i][j];
        if (j == p.length()) return i == s.length();
        
        boolean firstMatch = (i < s.length() && 
                             (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.'));
        
        boolean ans;
        if (j + 1 < p.length() && p.charAt(j + 1) == '*') {
            ans = backtrack(s, p, i, j + 2) || (firstMatch && backtrack(s, p, i + 1, j));
        } else {
            ans = firstMatch && backtrack(s, p, i + 1, j + 1);
        }
        memo[i][j] = ans;
        return ans;
    }
}
```

## 16. Scramble String (Chuỗi tráo đổi)
**Đề bài chi tiết:** Một chuỗi được biểu diễn dưới dạng cây nhị phân, tại các node không phải node lá, ta có thể đổi chỗ 2 con của nó để tạo ra Scramble String. Kiểm tra `s2` có phải được tạo ra từ việc Scramble `s1` không.
**Phân tích thuật toán:**
Dùng đệ quy cắt `s1` và `s2` ra làm 2 phần tại vị trí `i`. `s1` chia thành `s1[0..i]` và `s1[i..n]`. Cần kiểm tra:
1. 2 nửa trái khớp nhau và 2 nửa phải khớp nhau, HOẶC
2. Nửa trái của `s1` khớp với nửa phải của `s2` và nửa phải của `s1` khớp với nửa trái của `s2`.
Có thể dùng Map để Memoize các trạng thái đã xét.
**Độ phức tạp:**
- Time: $O(N^4)$ nhờ cắt tỉa và Memoization.
- Space: $O(N^3)$.
**Mã nguồn Java:**
```java
class Solution {
    Map<String, Boolean> memo = new HashMap<>();
    
    public boolean isScramble(String s1, String s2) {
        if (s1.equals(s2)) return true;
        if (s1.length() != s2.length()) return false;
        
        String key = s1 + "-" + s2;
        if (memo.containsKey(key)) return memo.get(key);
        
        int n = s1.length();
        int[] counts = new int[26];
        for (int i = 0; i < n; i++) {
            counts[s1.charAt(i) - 'a']++;
            counts[s2.charAt(i) - 'a']--;
        }
        for (int c : counts) {
            if (c != 0) {
                memo.put(key, false);
                return false;
            }
        }
        
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

## 17. Wildcard Matching (Khớp mẫu ký tự đại diện)
**Đề bài chi tiết:** Cho mẫu string `p` và string `s`. Thực hiện so khớp, với `?` thay cho 1 ký tự, và `*` thay cho dãy ký tự bất kỳ (kể cả độ dài 0).
**Phân tích thuật toán:**
Sử dụng hai con trỏ hoặc Backtracking tham lam (Greedy Backtrack). Nếu gặp `*`, lưu lại vị trí của `*` và duyệt tiếp chuỗi `s`. Nếu ở bước sau các ký tự không khớp, ta quay lui lại vị trí `*` và ép nó khớp thêm 1 ký tự của chuỗi `s` và thử lại.
**Độ phức tạp:**
- Time: Average $O(S \log P)$, Worst case $O(S \cdot P)$.
- Space: $O(1)$.
**Mã nguồn Java:**
```java
class Solution {
    public boolean isMatch(String s, String p) {
        int sIdx = 0, pIdx = 0, match = 0, starIdx = -1;            
        while (sIdx < s.length()) {
            if (pIdx < p.length() && (p.charAt(pIdx) == '?' || s.charAt(sIdx) == p.charAt(pIdx))) {
                sIdx++;
                pIdx++;
            } else if (pIdx < p.length() && p.charAt(pIdx) == '*') {
                starIdx = pIdx;
                match = sIdx;
                pIdx++;
            } else if (starIdx != -1) {
                pIdx = starIdx + 1;
                match++;
                sIdx = match;
            } else {
                return false;
            }
        }
        while (pIdx < p.length() && p.charAt(pIdx) == '*') {
            pIdx++;
        }
        return pIdx == p.length();
    }
}
```

## 18. Decode Ways II (Cách giải mã II)
**Đề bài chi tiết:** Dãy chữ cái A-Z được mã hóa thành `1-26`. Chuỗi có thể chứa ký tự `*`, đại diện cho một chữ số từ `1-9`. Hãy đếm số cách giải mã chuỗi thành chữ cái (modulo `10^9 + 7`).
**Phân tích thuật toán:**
Dùng Backtracking kết hợp Memoization. Tại mỗi vị trí, thử lấy 1 ký tự giải mã hoặc 2 ký tự. Phải kiểm tra điều kiện rất cẩn thận khi kết hợp với `*`. Do số cách lớn nên cần chia lấy dư liên tục.
**Độ phức tạp:**
- Time: $O(N)$ nhờ có DP Memoization.
- Space: $O(N)$ cho mảng nhớ.
**Mã nguồn Java:**
```java
class Solution {
    public int numDecodings(String s) {
        long[] memo = new long[s.length()];
        Arrays.fill(memo, -1);
        return (int) backtrack(s, 0, memo);
    }
    
    private long backtrack(String s, int index, long[] memo) {
        if (index == s.length()) return 1;
        if (s.charAt(index) == '0') return 0;
        if (memo[index] != -1) return memo[index];
        
        long res = 0;
        char c = s.charAt(index);
        
        if (c == '*') {
            res += 9 * backtrack(s, index + 1, memo); // 1 chữ số (1-9)
            if (index + 1 < s.length()) {
                char next = s.charAt(index + 1);
                if (next == '*') {
                    res += 15 * backtrack(s, index + 2, memo); // 2 chữ số (11-19, 21-26 -> 15 cách)
                } else if (next <= '6') {
                    res += 2 * backtrack(s, index + 2, memo); // vd: 16 hoặc 26
                } else {
                    res += 1 * backtrack(s, index + 2, memo); // vd: 17
                }
            }
        } else {
            res += backtrack(s, index + 1, memo);
            if (index + 1 < s.length()) {
                char next = s.charAt(index + 1);
                if (c == '1') {
                    if (next == '*') res += 9 * backtrack(s, index + 2, memo);
                    else res += backtrack(s, index + 2, memo);
                } else if (c == '2') {
                    if (next == '*') res += 6 * backtrack(s, index + 2, memo);
                    else if (next <= '6') res += backtrack(s, index + 2, memo);
                }
            }
        }
        
        long mod = 1000000007;
        res %= mod;
        memo[index] = res;
        return res;
    }
}
```

## 19. String to Integer (atoi) using Backtracking (Chuyển chuỗi thành số nguyên)
**Đề bài chi tiết:** Cài đặt hàm chuyển chuỗi thành số nguyên (giống `atoi` trong C). Bỏ qua khoảng trắng ở đầu, nhận diện dấu cộng/trừ và dịch các ký tự số sau đó liên tiếp. Có cơ chế chặn tràn kiểu số (Integer.MAX_VALUE / Integer.MIN_VALUE).
**Phân tích thuật toán:**
Tuy thông thường dùng biến lặp, ta có thể thiết kế một hàm đệ quy để truyền trạng thái (giống Backtracking không rẽ nhánh) của `index`, `num` và `sign`.
**Độ phức tạp:**
- Time: $O(N)$ đi qua tối đa $N$ ký tự.
- Space: $O(N)$ Call Stack (đệ quy).
**Mã nguồn Java:**
```java
class Solution {
    public int myAtoi(String s) {
        return parse(s.trim(), 0, 0, 1);
    }
    
    private int parse(String s, int index, long num, int sign) {
        if (index == s.length()) return (int) (num * sign);
        
        char c = s.charAt(index);
        if (index == 0 && (c == '+' || c == '-')) {
            return parse(s, index + 1, num, c == '-' ? -1 : 1);
        }
        
        if (!Character.isDigit(c)) {
            return (int) (num * sign);
        }
        
        num = num * 10 + (c - '0');
        if (sign == 1 && num > Integer.MAX_VALUE) return Integer.MAX_VALUE;
        if (sign == -1 && -num < Integer.MIN_VALUE) return Integer.MIN_VALUE;
        
        return parse(s, index + 1, num, sign);
    }
}
```

## 20. Count Sorted Vowel Strings (Đếm chuỗi nguyên âm đã sắp xếp)
**Đề bài chi tiết:** Cho số nguyên `n`. Hãy đếm tất cả các chuỗi nguyên âm (kết hợp từ `a, e, i, o, u`) có độ dài `n` và đã được sắp xếp hợp lệ (các ký tự trước không đứng sau ký tự có thứ tự từ điển sau nó).
**Phân tích thuật toán:**
Ta dùng Backtracking để sinh ra các chuỗi nguyên âm. Ở mỗi bước, ta duy trì chỉ số của nguyên âm hiện tại (từ `0-4`). Ở bước đệ quy tiếp theo, ta chỉ được chọn nguyên âm có index lớn hơn hoặc bằng. Khi độ dài chuỗi bằng `n` thì ta tăng biến đếm.
**Độ phức tạp:**
- Time: $O(N^4)$ vì số chuỗi là $C(N+4, 4)$.
- Space: $O(N)$ do độ sâu đệ quy.
**Mã nguồn Java:**
```java
class Solution {
    int count = 0;
    
    public int countVowelStrings(int n) {
        backtrack(n, 0, 0);
        return count;
    }
    
    private void backtrack(int n, int length, int startIndex) {
        if (length == n) {
            count++;
            return;
        }
        for (int i = startIndex; i < 5; i++) {
            backtrack(n, length + 1, i);
        }
    }
}
```
## 21. Lexicographical Numbers (Số theo thứ tự từ điển)
**Đề bài chi tiết:** Cho số nguyên `n`, trả về các số từ `1` đến `n` được sắp xếp theo thứ tự từ điển. Không dùng mảng trung gian hay hàm sort, yêu cầu độ phức tạp thời gian là $O(N)$ và không gian $O(1)$ (không tính call stack).
**Phân tích thuật toán:**
Sử dụng DFS/Backtracking để duyệt như trên một cây 10 phân (10-ary tree). Từ số hiện tại `current`, ta có thể thử gắn thêm các chữ số `0-9` vào cuối để tạo thành `current * 10 + i`. Nếu vượt quá `n` thì dừng nhánh đó.
**Độ phức tạp:**
- Time: $O(N)$ duyệt qua $N$ số.
- Space: $O(\log_{10}N)$ do chiều sâu của cây (Call Stack đệ quy).
**Mã nguồn Java:**
```java
class Solution {
    public List<Integer> lexicalOrder(int n) {
        List<Integer> res = new ArrayList<>();
        for (int i = 1; i <= 9; i++) {
            dfs(i, n, res);
        }
        return res;
    }
    
    private void dfs(int current, int n, List<Integer> res) {
        if (current > n) return;
        res.add(current);
        
        for (int i = 0; i <= 9; i++) {
            if (current * 10 + i > n) return;
            dfs(current * 10 + i, n, res);
        }
    }
}
```

## 22. Restore IPv6 Addresses (Khôi phục địa chỉ IPv6 - Biến thể)
**Đề bài chi tiết:** Tương tự như khôi phục IPv4, cho một chuỗi chứa các ký tự hệ Hex (`0-9`, `a-f`). Khôi phục tất cả các địa chỉ IPv6 hợp lệ bằng cách chèn 7 dấu `:`. Một địa chỉ IPv6 chứa 8 nhóm, mỗi nhóm dài từ 1 đến 4 ký tự hệ thập lục phân.
**Phân tích thuật toán:**
Dùng Quay lui thực hiện 8 bước, mỗi bước cắt từ 1 đến 4 ký tự. Nếu sau khi lấy đủ 8 nhóm và vừa hết chuỗi gốc, ta ghép chúng lại bằng dấu `:` và lưu vào kết quả.
**Độ phức tạp:**
- Time: $O(4^8) = O(1)$ vì số lượng nhóm cố định là 8 và mỗi nhóm dài tối đa 4.
- Space: $O(1)$ call stack giới hạn ở độ sâu 8.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> restoreIpV6(String s) {
        List<String> res = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), res);
        return res;
    }
    
    private void backtrack(String s, int start, List<String> path, List<String> res) {
        if (path.size() == 8) {
            if (start == s.length()) {
                res.add(String.join(":", path));
            }
            return;
        }
        
        for (int i = start; i < Math.min(start + 4, s.length()); i++) {
            String part = s.substring(start, i + 1);
            path.add(part);
            backtrack(s, i + 1, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```

## 23. Break a Palindrome (Phá vỡ cấu trúc Palindrome)
**Đề bài chi tiết:** Cho một chuỗi đối xứng (palindrome) độ dài `n`. Hãy thay đổi đúng 1 ký tự bằng một chữ cái tiếng Anh in thường sao cho chuỗi kết quả KHÔNG còn đối xứng và có thứ tự từ điển NHỎ NHẤT có thể. Trả về chuỗi rỗng nếu không thể làm được.
**Phân tích thuật toán:**
Dù bài này thuộc dạng Backtrack String trên nhiều danh sách, nó là bài tham lam tối ưu (Greedy). Duyệt nửa chuỗi đầu tiên, nếu gặp ký tự khác `'a'`, đổi ngay nó thành `'a'`. Nếu toàn là `'a'`, ta chỉ cần đổi ký tự cuối cùng thành `'b'`.
**Độ phức tạp:**
- Time: $O(N)$ độ dài nửa chuỗi.
- Space: $O(N)$ lưu trữ chuỗi kết quả.
**Mã nguồn Java:**
```java
class Solution {
    public String breakPalindrome(String palindrome) {
        if (palindrome.length() <= 1) return "";
        char[] chars = palindrome.toCharArray();
        
        for (int i = 0; i < chars.length / 2; i++) {
            if (chars[i] != 'a') {
                chars[i] = 'a';
                return new String(chars);
            }
        }
        
        // Nếu toàn chữ 'a', đổi chữ cuối cùng thành 'b'
        chars[chars.length - 1] = 'b';
        return new String(chars);
    }
}
```

## 24. Longest Common Subsequence of Multiple Strings (LCS của nhiều chuỗi)
**Đề bài chi tiết:** Tìm độ dài chuỗi con chung dài nhất của 3 chuỗi `A`, `B`, và `C`. (Bài toán mở rộng từ LCS 2 chuỗi, ứng dụng quay lui có nhớ cho số lượng chuỗi nhỏ).
**Phân tích thuật toán:**
Dùng Quay lui (Backtracking) với Memoization. Đặt 3 con trỏ `i, j, k`. Nếu ký tự tại 3 vị trí giống nhau, đệ quy tịnh tiến cả ba. Nếu không, trả về max của 3 trường hợp khi thử bỏ qua ký tự của chuỗi `A`, `B` hoặc `C`.
**Độ phức tạp:**
- Time: $O(|A| \cdot |B| \cdot |C|)$ nhờ Memoization.
- Space: $O(|A| \cdot |B| \cdot |C|)$ cho bảng nhớ và Call Stack.
**Mã nguồn Java:**
```java
class Solution {
    Integer[][][] memo;
    public int lcsOfThree(String A, String B, String C) {
        memo = new Integer[A.length()][B.length()][C.length()];
        return backtrack(A, B, C, 0, 0, 0);
    }
    
    private int backtrack(String A, String B, String C, int i, int j, int k) {
        if (i == A.length() || j == B.length() || k == C.length()) return 0;
        if (memo[i][j][k] != null) return memo[i][j][k];
        
        if (A.charAt(i) == B.charAt(j) && B.charAt(j) == C.charAt(k)) {
            memo[i][j][k] = 1 + backtrack(A, B, C, i + 1, j + 1, k + 1);
        } else {
            int op1 = backtrack(A, B, C, i + 1, j, k);
            int op2 = backtrack(A, B, C, i, j + 1, k);
            int op3 = backtrack(A, B, C, i, j, k + 1);
            memo[i][j][k] = Math.max(op1, Math.max(op2, op3));
        }
        return memo[i][j][k];
    }
}
```

## 25. Word Ladder II (Bậc thang từ vựng II)
**Đề bài chi tiết:** Cho 2 từ `beginWord`, `endWord` và danh sách từ điển `wordList`. Tìm tất cả các chuỗi biến đổi ngắn nhất từ `beginWord` đến `endWord`, mỗi bước chỉ đổi 1 chữ cái và từ trung gian phải có trong từ điển.
**Phân tích thuật toán:**
Cần kết hợp cả BFS và Backtracking(DFS). Dùng BFS để tìm độ dài đường đi ngắn nhất và tạo đồ thị DAG (Directed Acyclic Graph). Sau đó dùng Backtracking duyệt trên đồ thị này để thu thập toàn bộ các con đường có chiều dài bằng chiều dài ngắn nhất.
**Độ phức tạp:**
- Time: $O(V + E + Paths)$, trong đó $Paths$ là số lượng đường đi ngắn nhất.
- Space: $O(V \cdot L)$ cho đồ thị.
**Mã nguồn Java:**
```java
class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet<>(wordList);
        List<List<String>> res = new ArrayList<>();
        if (!dict.contains(endWord)) return res;
        
        Map<String, List<String>> graph = new HashMap<>();
        Map<String, Integer> distance = new HashMap<>();
        dict.add(beginWord);
        bfs(beginWord, dict, graph, distance);
        
        backtrack(beginWord, endWord, graph, distance, new ArrayList<>(Arrays.asList(beginWord)), res);
        return res;
    }
    
    private void bfs(String beginWord, Set<String> dict, Map<String, List<String>> graph, Map<String, Integer> distance) {
        Queue<String> queue = new LinkedList<>();
        queue.add(beginWord);
        distance.put(beginWord, 0);
        for (String w : dict) graph.put(w, new ArrayList<>());
        
        while (!queue.isEmpty()) {
            String curr = queue.poll();
            int curDist = distance.get(curr);
            char[] chars = curr.toCharArray();
            
            for (int i = 0; i < chars.length; i++) {
                char original = chars[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    chars[i] = c;
                    String next = new String(chars);
                    if (dict.contains(next)) {
                        graph.get(curr).add(next);
                        if (!distance.containsKey(next)) {
                            distance.put(next, curDist + 1);
                            queue.add(next);
                        }
                    }
                }
                chars[i] = original;
            }
        }
    }
    
    private void backtrack(String curr, String endWord, Map<String, List<String>> graph, Map<String, Integer> distance, List<String> path, List<List<String>> res) {
        if (curr.equals(endWord)) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (String next : graph.get(curr)) {
            if (distance.get(next) == distance.get(curr) + 1) {
                path.add(next);
                backtrack(next, endWord, graph, distance, path, res);
                path.remove(path.size() - 1);
            }
        }
    }
}
```

## 26. Distinct Subsequences (Các chuỗi con phân biệt)
**Đề bài chi tiết:** Cho chuỗi `s` và `t`, đếm số cách khác nhau để tạo ra chuỗi `t` từ việc chọn các tập hợp con các ký tự trong `s` (vẫn giữ nguyên thứ tự).
**Phân tích thuật toán:**
Dùng Quay lui + Memoization. Tại vị trí `i` của `s` và `j` của `t`, ta luôn có 1 lựa chọn là BỎ QUA ký tự `s[i]`. Nếu `s[i] == t[j]`, ta có thêm 1 lựa chọn là CHỌN ký tự này và đi tiếp. Cộng kết quả của 2 nhánh lại.
**Độ phức tạp:**
- Time: $O(|S| \cdot |T|)$.
- Space: $O(|S| \cdot |T|)$.
**Mã nguồn Java:**
```java
class Solution {
    public int numDistinct(String s, String t) {
        Integer[][] memo = new Integer[s.length()][t.length()];
        return backtrack(s, t, 0, 0, memo);
    }
    
    private int backtrack(String s, String t, int i, int j, Integer[][] memo) {
        if (j == t.length()) return 1;
        if (i == s.length()) return 0;
        if (memo[i][j] != null) return memo[i][j];
        
        int res = backtrack(s, t, i + 1, j, memo); // Không dùng s[i]
        
        if (s.charAt(i) == t.charAt(j)) {
            res += backtrack(s, t, i + 1, j + 1, memo); // Dùng s[i]
        }
        
        memo[i][j] = res;
        return res;
    }
}
```

## 27. Parsing A Boolean Expression (Phân tích biểu thức Boolean)
**Đề bài chi tiết:** Đánh giá chuỗi biểu thức logic chứa các toán tử `!`, `&`, `|` kết hợp với `t` (true), `f` (false) và các dấu ngoặc. Ví dụ: `|(&(t,f,t),!(t))`.
**Phân tích thuật toán:**
Duyệt chuỗi bằng Đệ quy (như Backtracking đệ quy xuống các nhánh con). Khi gặp toán tử, ta tính toàn bộ kết quả của các thành phần con nằm bên trong cặp ngoặc đơn của nó, gộp lại bằng phép toán tương ứng.
**Độ phức tạp:**
- Time: $O(N)$ vì mỗi ký tự được thăm 1 lần.
- Space: $O(N)$ theo Call Stack đệ quy.
**Mã nguồn Java:**
```java
class Solution {
    int index = 0;
    
    public boolean parseBoolExpr(String expression) {
        char c = expression.charAt(index++);
        if (c == 't') return true;
        if (c == 'f') return false;
        
        if (c == '!') {
            index++; // Bỏ qua '('
            boolean res = parseBoolExpr(expression);
            index++; // Bỏ qua ')'
            return !res;
        }
        
        boolean isAnd = (c == '&');
        index++; // Bỏ qua '('
        boolean res = isAnd; // Khởi tạo: AND là true, OR là false
        
        while (index < expression.length() && expression.charAt(index) != ')') {
            if (expression.charAt(index) == ',') {
                index++;
                continue;
            }
            boolean next = parseBoolExpr(expression);
            if (isAnd) res &= next;
            else res |= next;
        }
        index++; // Bỏ qua ')'
        return res;
    }
}
```

## 28. Concatenated Words (Các từ ghép)
**Đề bài chi tiết:** Cho một danh sách các từ duy nhất, tìm tất cả các từ trong danh sách được cấu tạo bởi ít nhất 2 từ hợp lệ khác trong chính danh sách đó.
**Phân tích thuật toán:**
Lặp qua từng từ, tạm thời loại bỏ nó ra khỏi tập `dict`. Dùng Backtracking (kết hợp Memoization) để thử chia từ thành 2 phần (tiền tố và hậu tố). Nếu tiền tố nằm trong `dict` và hậu tố cũng có thể tạo bởi các từ trong `dict`, thì từ đó hợp lệ.
**Độ phức tạp:**
- Time: $O(N \cdot L^3)$ với $N$ là số từ, $L$ là độ dài trung bình của từ.
- Space: $O(N)$ cho HashSet và Memoization.
**Mã nguồn Java:**
```java
class Solution {
    Map<String, Boolean> memo = new HashMap<>();
    
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        Set<String> dict = new HashSet<>(Arrays.asList(words));
        List<String> res = new ArrayList<>();
        
        for (String word : words) {
            dict.remove(word);
            if (backtrack(word, dict)) {
                res.add(word);
            }
            dict.add(word); // Khôi phục lại
        }
        return res;
    }
    
    private boolean backtrack(String word, Set<String> dict) {
        if (dict.contains(word)) return true;
        if (memo.containsKey(word)) return memo.get(word);
        
        for (int i = 1; i < word.length(); i++) {
            String prefix = word.substring(0, i);
            if (dict.contains(prefix)) {
                String suffix = word.substring(i);
                if (backtrack(suffix, dict)) {
                    memo.put(word, true);
                    return true;
                }
            }
        }
        
        memo.put(word, false);
        return false;
    }
}
```

## 29. Brace Expansion II (Khai triển ngoặc nhọn II)
**Đề bài chi tiết:** Cho một chuỗi chứa chữ cái thường, dấu phẩy, và ngoặc nhọn. Một biểu thức được ngăn cách bởi phẩy trong ngoặc `{a,b,c}` đại diện cho tập hợp. Biểu thức nối tiếp nhau `{a,b}{c,d}` đại diện cho tích Đề-các. Khai triển và trả về danh sách các từ vựng đã được sắp xếp, không trùng lặp.
**Phân tích thuật toán:**
Thay vì Recursive Backtrack thuần, ta dùng Queue và cắt các cấu trúc ngoặc đơn giản nhất chưa chứa tổ hợp ngoặc nhọn bên trong để xử lý trước. Liên tục chèn kết quả trở lại Queue. Cách tiếp cận thay thế (Replacement) này mô phỏng rất tốt việc Backtrack trên ngữ pháp Context-Free.
**Độ phức tạp:**
- Time: $O(N \cdot K^M)$ với $K$ là số nhóm khai triển.
- Space: $O(K^M)$ để chứa tất cả trạng thái.
**Mã nguồn Java:**
```java
class Solution {
    public List<String> braceExpansionII(String expression) {
        Queue<String> queue = new LinkedList<>();
        queue.add(expression);
        Set<String> resSet = new HashSet<>();
        
        while (!queue.isEmpty()) {
            String s = queue.poll();
            if (s.indexOf('{') == -1) {
                resSet.add(s);
                continue;
            }
            
            // Tìm ngoặc nhọn kín nhỏ nhất
            int i = 0, l = 0, r = 0;
            while (s.charAt(i) != '}') i++;
            r = i;
            l = i;
            while (s.charAt(l) != '{') l--;
            
            String before = s.substring(0, l);
            String after = s.substring(r + 1);
            String[] strs = s.substring(l + 1, r).split(",");
            
            for (String str : strs) {
                queue.add(before + str + after);
            }
        }
        
        List<String> res = new ArrayList<>(resSet);
        Collections.sort(res);
        return res;
    }
}
```

## 30. Make Number of Distinct Characters Equal (Cân bằng số lượng ký tự khác biệt)
**Đề bài chi tiết:** Cho hai chuỗi `word1` và `word2`. Hãy trả về `true` nếu bạn có thể hoán đổi đúng 1 cặp ký tự (chọn 1 từ `word1` tráo với 1 từ `word2`) sao cho sau đó, số lượng ký tự ĐỘC NHẤT ở 2 chuỗi là bằng nhau.
**Phân tích thuật toán:**
Giống như Backtracking tìm đường 1 bước (chỉ thử 1 nước đi duy nhất). Duyệt qua mọi cặp ký tự từ `'a'` đến `'z'` mà xuất hiện ở `word1` và `word2`. Cập nhật mảng đếm để mô phỏng hoán đổi, kiểm tra điều kiện rồi quay lui (trả lại nguyên trạng thái ban đầu).
**Độ phức tạp:**
- Time: $O(N + M + 26 \times 26) = O(N + M)$.
- Space: $O(1)$ (Mảng đếm chỉ có size 26).
**Mã nguồn Java:**
```java
class Solution {
    public boolean isItPossible(String word1, String word2) {
        int[] count1 = new int[26];
        int[] count2 = new int[26];
        for (char c : word1.toCharArray()) count1[c - 'a']++;
        for (char c : word2.toCharArray()) count2[c - 'a']++;
        
        for (int i = 0; i < 26; i++) {
            for (int j = 0; j < 26; j++) {
                if (count1[i] > 0 && count2[j] > 0) {
                    // Swap
                    count1[i]--; count2[j]--;
                    count1[j]++; count2[i]++;
                    
                    if (getDistinctCount(count1) == getDistinctCount(count2)) {
                        return true;
                    }
                    
                    // Backtrack
                    count1[j]--; count2[i]--;
                    count1[i]++; count2[j]++;
                }
            }
        }
        return false;
    }
    
    private int getDistinctCount(int[] count) {
        int res = 0;
        for (int c : count) {
            if (c > 0) res++;
        }
        return res;
    }
}
```
