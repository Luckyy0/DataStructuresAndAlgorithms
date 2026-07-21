# 02 - StringBuilder và StringBuffer - Bài tập thực hành

## 1. Reverse Words in a String III (Đảo ngược từ trong chuỗi III)
### Đề bài chi tiết
Cho một chuỗi `s`, hãy đảo ngược thứ tự các ký tự trong từng từ của chuỗi, đồng thời giữ nguyên khoảng trắng ban đầu và thứ tự các từ.
Ví dụ: `s = "Let's take LeetCode contest"`, Output: `"s'teL ekat edoCteeL tsetnoc"`.

### Phân tích thuật toán
- Sử dụng hai con trỏ hoặc tách chuỗi bằng khoảng trắng.
- Tối ưu nhất là duyệt qua chuỗi bằng một vòng lặp, xác định bắt đầu và kết thúc của từng từ.
- Dùng `StringBuilder` để đảo ngược các từ và ghép lại, nhằm tránh tạo ra nhiều chuỗi rác.

### Mã nguồn Java
```java
public class Solution {
    public String reverseWords(String s) {
        StringBuilder result = new StringBuilder();
        StringBuilder word = new StringBuilder();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c != ' ') {
                word.append(c);
            } else {
                result.append(word.reverse()).append(" ");
                word.setLength(0); // Tái sử dụng StringBuilder
            }
        }
        // Thêm từ cuối cùng
        result.append(word.reverse());
        return result.toString();
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, với $N$ là độ dài chuỗi $s$, mỗi ký tự được truy cập một vài lần hằng số.
- **Không gian (Space)**: $O(N)$, sử dụng `StringBuilder` để lưu chuỗi kết quả.

## 2. String Compression (Nén chuỗi)
### Đề bài chi tiết
Cho một mảng các ký tự `chars`. Hãy nén nó bằng thuật toán sau:
Nếu độ dài chuỗi nén ngắn hơn hoặc bằng độ dài mảng ban đầu, trả về độ dài mới của mảng và sửa đổi mảng ban đầu in-place (tại chỗ). Thuật toán nén: đối với mỗi nhóm ký tự liên tiếp giống nhau, ghi ký tự đó theo sau là số lượng của nó (nếu > 1).
Ví dụ: `chars = ["a","a","b","b","c","c","c"]`. Trả về `6`, mảng chuyển thành `["a","2","b","2","c","3"]`.

### Phân tích thuật toán
- Dùng hai con trỏ: một con trỏ `read` để duyệt qua mảng, một con trỏ `write` để ghi kết quả trực tiếp lên `chars`.
- Đếm số lần lặp lại của ký tự hiện tại.
- Dùng `String.valueOf(count)` và chuyển thành các ký tự để ghi vào `chars`. `StringBuilder` không cần thiết nếu ta làm in-place, nhưng nếu trả về String thì `StringBuilder` là bắt buộc. Ở bài toán in-place, ta coi mảng đầu vào như là vùng đệm.

### Mã nguồn Java
```java
public class Solution {
    public int compress(char[] chars) {
        int indexAns = 0, index = 0;
        while (index < chars.length) {
            char currentChar = chars[index];
            int count = 0;
            while (index < chars.length && chars[index] == currentChar) {
                index++;
                count++;
            }
            chars[indexAns++] = currentChar;
            if (count > 1) {
                for (char c : String.valueOf(count).toCharArray()) {
                    chars[indexAns++] = c;
                }
            }
        }
        return indexAns;
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(N)$, duyệt mảng một lần.
- **Không gian**: $O(1)$, vì thao tác trực tiếp trên mảng `chars` ban đầu.

## 3. Decode String (Giải mã chuỗi)
### Đề bài chi tiết
Cho một chuỗi được mã hóa, trả về chuỗi sau khi được giải mã.
Quy tắc mã hóa là: `k[encoded_string]`, có nghĩa là `encoded_string` bên trong dấu ngoặc vuông được lặp lại chính xác `k` lần. Số `k` đảm bảo là số dương.
Ví dụ: `s = "3[a]2[bc]"`, Output: `"aaabcbc"`.

### Phân tích thuật toán
- Sử dụng ngăn xếp (Stack) để lưu số lần lặp và chuỗi hiện tại trước dấu `[`.
- Mỗi khi gặp số, tính giá trị số (`k`).
- Mỗi khi gặp `[`, đẩy số `k` và chuỗi hiện tại (`StringBuilder`) vào stack, rồi reset `StringBuilder`.
- Mỗi khi gặp `]`, lấy chuỗi trước đó ra, nối với chuỗi hiện tại lặp `k` lần.

### Mã nguồn Java
```java
import java.util.Stack;

public class Solution {
    public String decodeString(String s) {
        Stack<Integer> countStack = new Stack<>();
        Stack<StringBuilder> stringStack = new Stack<>();
        StringBuilder currentString = new StringBuilder();
        int k = 0;
        
        for (char ch : s.toCharArray()) {
            if (Character.isDigit(ch)) {
                k = k * 10 + (ch - '0');
            } else if (ch == '[') {
                countStack.push(k);
                stringStack.push(currentString);
                currentString = new StringBuilder();
                k = 0;
            } else if (ch == ']') {
                StringBuilder decodedString = stringStack.pop();
                int currentK = countStack.pop();
                for (int i = 0; i < currentK; i++) {
                    decodedString.append(currentString);
                }
                currentString = decodedString;
            } else {
                currentString.append(ch);
            }
        }
        return currentString.toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(\max(K) \times N)$, độ dài của chuỗi kết quả có thể lớn phụ thuộc vào giá trị số $k$.
- **Không gian**: $O(M)$, kích thước ngăn xếp tối đa tương đương số lượng dấu ngoặc vuông lồng nhau.

## 4. Multiply Strings (Nhân hai chuỗi)
### Đề bài chi tiết
Cho hai số nguyên không âm `num1` và `num2` dưới dạng chuỗi, trả về tích của chúng cũng dưới dạng chuỗi. Không được chuyển đổi trực tiếp thành số nguyên (BigInteger).
Ví dụ: `num1 = "123", num2 = "456"`, Output: `"56088"`.

### Phân tích thuật toán
- Mô phỏng phép nhân tay (đặt tính rồi tính).
- Chiều dài mảng kết quả tối đa là `m + n` với `m = num1.length`, `n = num2.length`.
- Vị trí ở chuỗi kết quả cho tích của `num1[i]` và `num2[j]` nằm ở `i + j` và `i + j + 1`.
- Dùng một mảng nguyên để lưu từng chữ số, cuối cùng dùng `StringBuilder` để gom mảng lại thành chuỗi, bỏ các số 0 ở đầu.

### Mã nguồn Java
```java
public class Solution {
    public String multiply(String num1, String num2) {
        if ("0".equals(num1) || "0".equals(num2)) return "0";
        
        int m = num1.length(), n = num2.length();
        int[] pos = new int[m + n];
        
        for(int i = m - 1; i >= 0; i--) {
            for(int j = n - 1; j >= 0; j--) {
                int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                int p1 = i + j, p2 = i + j + 1;
                int sum = mul + pos[p2];
                
                pos[p1] += sum / 10;
                pos[p2] = sum % 10;
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for(int p : pos) {
            if(!(sb.length() == 0 && p == 0)) {
                sb.append(p);
            }
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(M \times N)$, với $M, N$ là chiều dài của hai chuỗi.
- **Không gian**: $O(M + N)$, để lưu mảng `pos` và đối tượng `StringBuilder` kết quả.

## 5. Add Binary (Cộng hai số nhị phân)
### Đề bài chi tiết
Cho hai chuỗi nhị phân `a` và `b`, trả về tổng của chúng (cũng dưới dạng chuỗi nhị phân).
Ví dụ: `a = "1010", b = "1011"`, Output: `"10101"`.

### Phân tích thuật toán
- Duyệt từ cuối hai chuỗi về đầu, cộng từng bit cùng với bit nhớ (carry).
- Sử dụng `StringBuilder` để nối các kết quả từ cuối. Do cộng từ phần đơn vị trở lên (từ phải sang trái), kết quả trong `StringBuilder` bị ngược.
- Cuối cùng gọi `sb.reverse().toString()`.

### Mã nguồn Java
```java
public class Solution {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1, j = b.length() - 1, carry = 0;
        
        while (i >= 0 || j >= 0) {
            int sum = carry;
            if (i >= 0) sum += a.charAt(i--) - '0';
            if (j >= 0) sum += b.charAt(j--) - '0';
            
            sb.append(sum % 2);
            carry = sum / 2;
        }
        
        if (carry != 0) sb.append(carry);
        return sb.reverse().toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(\max(N, M))$, độ dài lớn nhất của chuỗi.
- **Không gian**: $O(\max(N, M))$, không gian cho `StringBuilder` kết quả.

## 6. Zigzag Conversion (Chuyển đổi Zigzag)
### Đề bài chi tiết
Cho một chuỗi `s` và số hàng `numRows`. Viết chuỗi `s` theo hình zigzag trên số hàng đó, sau đó đọc từng dòng từ trái sang phải.
Ví dụ: `s = "PAYPALISHIRING", numRows = 3`.
P   A   H   N
A P L S I I G
Y   I   R
Output: `"PAHNAPLSIIGYIR"`

### Phân tích thuật toán
- Dùng một mảng các `StringBuilder`, mỗi `StringBuilder` đại diện cho một hàng.
- Biến `goingDown` kiểm tra trạng thái di chuyển lên hoặc xuống. Duyệt qua từng ký tự và đưa vào đúng `StringBuilder`.
- Gộp các hàng lại với nhau bằng `StringBuilder` ngoài cùng.

### Mã nguồn Java
```java
public class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;
        
        StringBuilder[] rows = new StringBuilder[Math.min(numRows, s.length())];
        for (int i = 0; i < rows.length; i++) {
            rows[i] = new StringBuilder();
        }
        
        int curRow = 0;
        boolean goingDown = false;
        
        for (char c : s.toCharArray()) {
            rows[curRow].append(c);
            if (curRow == 0 || curRow == numRows - 1) goingDown = !goingDown;
            curRow += goingDown ? 1 : -1;
        }
        
        StringBuilder ret = new StringBuilder();
        for (StringBuilder row : rows) {
            ret.append(row);
        }
        return ret.toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(N)$, duyệt qua mỗi ký tự một lần.
- **Không gian**: $O(N)$, sử dụng mảng `StringBuilder` lưu tổng số các ký tự.

## 7. Text Justification (Căn lề văn bản)
### Đề bài chi tiết
Cho mảng các từ `words` và số `maxWidth`. Căn lề sao cho mỗi dòng chứa chính xác `maxWidth` ký tự, căn đều cả hai bên (trái và phải). Dòng cuối cùng phải được căn trái và không có thêm khoảng trắng xen kẽ.
Ví dụ: `words = ["This", "is", "an", "example"], maxWidth = 16`.
Output: `["This    is    an", "example         "]`.

### Phân tích thuật toán
- Nhóm các từ lại sao cho tổng độ dài cộng với khoảng trắng tối thiểu vừa đúng `maxWidth`.
- Dùng `StringBuilder` để nối từ. Tùy thuộc vào số chỗ trống còn lại, chia đều các khoảng trắng hoặc cộng dư vào các khoảng trống đầu tiên.
- Đối với dòng cuối cùng, căn lề trái và điền phần còn lại toàn bằng khoảng trắng.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> fullJustify(String[] words, int maxWidth) {
        List<String> res = new ArrayList<>();
        int i = 0;
        
        while (i < words.length) {
            int j = i + 1;
            int lineLength = words[i].length();
            while (j < words.length && (lineLength + words[j].length() + (j - i - 1)) < maxWidth) {
                lineLength += words[j].length();
                j++;
            }
            
            int diff = maxWidth - lineLength;
            int numberOfWords = j - i;
            if (numberOfWords == 1 || j >= words.length) {
                res.add(leftJustify(words, diff, i, j));
            } else {
                res.add(middleJustify(words, diff, i, j));
            }
            i = j;
        }
        return res;
    }
    
    private String leftJustify(String[] words, int diff, int i, int j) {
        int spacesOnRight = diff - (j - i - 1);
        StringBuilder sb = new StringBuilder(words[i]);
        for (int k = i + 1; k < j; k++) {
            sb.append(" ").append(words[k]);
        }
        sb.append(" ".repeat(spacesOnRight));
        return sb.toString();
    }
    
    private String middleJustify(String[] words, int diff, int i, int j) {
        int spacesNeeded = j - i - 1;
        int spaces = diff / spacesNeeded;
        int extraSpaces = diff % spacesNeeded;
        StringBuilder sb = new StringBuilder(words[i]);
        for (int k = i + 1; k < j; k++) {
            int spacesToApply = spaces + (extraSpaces-- > 0 ? 1 : 0);
            sb.append(" ".repeat(spacesToApply)).append(words[k]);
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(N)$, trong đó $N$ là số ký tự của tất cả các từ gộp lại.
- **Không gian**: $O(N)$, để lưu trữ danh sách các chuỗi đã xử lý và `StringBuilder` tạm thời.

## 8. Compare Version Numbers (So sánh phiên bản)
### Đề bài chi tiết
Cho hai chuỗi `version1` và `version2`, so sánh chúng dưới dạng số phiên bản. Trả về `1` nếu `version1 > version2`, `-1` nếu `<` và `0` nếu bằng nhau. Bỏ qua số 0 ở đầu.
Ví dụ: `version1 = "1.01", version2 = "1.001"`, Output: `0`.

### Phân tích thuật toán
- Ta có thể tách chuỗi theo `.` bằng `String.split("\\.")`.
- Do không được đảm bảo độ dài hai phiên bản như nhau (ví dụ "1.0" và "1"), ta phải chạy tới giới hạn lớn nhất của cả hai mảng. Nếu thiếu, coi như phần tử đó là 0.
- `Integer.parseInt` giúp tự động bỏ qua số 0 đứng trước. Tuy nhiên, dùng `split` chậm, ta có thể tự duyệt chuỗi để tối ưu, không tạo ra chuỗi trung gian.

### Mã nguồn Java
```java
public class Solution {
    public int compareVersion(String version1, String version2) {
        int n1 = version1.length(), n2 = version2.length();
        int i = 0, j = 0;
        
        while (i < n1 || j < n2) {
            int num1 = 0, num2 = 0;
            
            while (i < n1 && version1.charAt(i) != '.') {
                num1 = num1 * 10 + (version1.charAt(i) - '0');
                i++;
            }
            while (j < n2 && version2.charAt(j) != '.') {
                num2 = num2 * 10 + (version2.charAt(j) - '0');
                j++;
            }
            
            if (num1 < num2) return -1;
            if (num1 > num2) return 1;
            
            i++;
            j++;
        }
        
        return 0;
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(\max(N, M))$, duyệt qua cả hai chuỗi 1 lần.
- **Không gian**: $O(1)$, vì không tạo mảng mảng lưu tạm, không dùng split hay StringBuilder (chỉ cần biến nguyên để tính).

## 9. Remove All Adjacent Duplicates In String (Xóa các ký tự lặp liền kề)
### Đề bài chi tiết
Cho chuỗi `s` chỉ chứa chữ cái thường. Xóa tất cả các cặp ký tự lặp liền kề cho đến khi không thể xóa được nữa.
Ví dụ: `s = "abbaca"`. `bb` xóa còn `aaca`. `aa` xóa còn `ca`. Output: `"ca"`.

### Phân tích thuật toán
- Sử dụng `StringBuilder` giống như một cái ngăn xếp (Stack).
- Duyệt qua từng ký tự, nếu ký tự đó giống với ký tự cuối cùng của `StringBuilder`, ta sẽ xóa ký tự cuối (`deleteCharAt`).
- Ngược lại, thêm ký tự đó vào `StringBuilder`.

### Mã nguồn Java
```java
public class Solution {
    public String removeDuplicates(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            int size = sb.length();
            if (size > 0 && sb.charAt(size - 1) == c) {
                sb.deleteCharAt(size - 1);
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(N)$, vòng lặp duyệt qua chuỗi một lần. `deleteCharAt` ở cuối chuỗi trong `StringBuilder` là $O(1)$.
- **Không gian**: $O(N)$, độ dài của `StringBuilder` trả về, là ngăn xếp nội bộ.

## 10. Basic Calculator II (Máy tính cơ bản II)
### Đề bài chi tiết
Cho chuỗi `s` đại diện cho biểu thức toán học, hãy tính và trả về kết quả. Biểu thức chứa `+`, `-`, `*`, `/` và số nguyên. Không có ngoặc.
Ví dụ: `s = " 3+5 / 2 "`, Output: `5`.

### Phân tích thuật toán
- Dùng một biến `lastSign` để lưu dấu trước đó (khởi tạo là `+`).
- Nếu số, cập nhật số hiện tại.
- Nếu là ký tự phép toán hoặc cuối chuỗi:
  - Nếu `lastSign == '+'`, đẩy số vào stack.
  - Nếu `lastSign == '-'`, đẩy âm của số đó vào stack.
  - Nếu `lastSign == '*'`, lấy phần tử trên đỉnh stack nhân với số hiện tại và đẩy lại.
  - Nếu `lastSign == '/'`, lấy phần tử trên đỉnh stack chia cho số hiện tại và đẩy lại.
- Dùng mảng giả Stack hoặc chạy liên tục 2 biến `lastNumber`, `currentNumber` để đạt $O(1)$ không gian.

### Mã nguồn Java
```java
public class Solution {
    public int calculate(String s) {
        if (s == null || s.isEmpty()) return 0;
        int len = s.length();
        int currentNumber = 0, lastNumber = 0, result = 0;
        char sign = '+';
        
        for (int i = 0; i < len; i++) {
            char currentChar = s.charAt(i);
            if (Character.isDigit(currentChar)) {
                currentNumber = (currentNumber * 10) + (currentChar - '0');
            }
            if (!Character.isDigit(currentChar) && !Character.isWhitespace(currentChar) || i == len - 1) {
                if (sign == '+' || sign == '-') {
                    result += lastNumber;
                    lastNumber = (sign == '+') ? currentNumber : -currentNumber;
                } else if (sign == '*') {
                    lastNumber = lastNumber * currentNumber;
                } else if (sign == '/') {
                    lastNumber = lastNumber / currentNumber;
                }
                sign = currentChar;
                currentNumber = 0;
            }
        }
        result += lastNumber;
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian**: $O(N)$, chỉ duyệt qua chuỗi một lần duy nhất.
- **Không gian**: $O(1)$, vì không dùng stack.

---

## 11. Longest Common Prefix (Tiền tố chung dài nhất)
### Đề bài chi tiết
Viết một hàm để tìm chuỗi tiền tố chung dài nhất trong một mảng các chuỗi.
Nếu không có tiền tố chung, trả về chuỗi rỗng `""`.
Ví dụ: `strs = ["flower", "flow", "flight"]`, Output: `"fl"`.

### Phân tích thuật toán
- Lấy chuỗi đầu tiên trong mảng làm tiền tố giả định (prefix).
- Duyệt qua các chuỗi còn lại, liên tục kiểm tra xem chuỗi hiện tại có bắt đầu bằng `prefix` không (dùng `indexOf`).
- Nếu không khớp, cắt bớt ký tự cuối của `prefix` và lặp lại cho đến khi khớp.
- Cách làm này được gọi là "Horizontal scanning", hiệu quả cao và dễ cài đặt.

### Mã nguồn Java
```java
public class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        String prefix = strs[0];
        for (int i = 1; i < strs.length; i++) {
            while (strs[i].indexOf(prefix) != 0) {
                prefix = prefix.substring(0, prefix.length() - 1);
                if (prefix.isEmpty()) return "";
            }
        }
        return prefix;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(S)$, trong đó $S$ là tổng số ký tự của tất cả các chuỗi trong mảng.
- **Không gian (Space)**: $O(1)$, vì chỉ sử dụng không gian hằng số để lưu chuỗi tiền tố (nếu không tính phần bộ nhớ cấp phát khi substring).

## 12. Integer to Roman (Số nguyên sang số La Mã)
### Đề bài chi tiết
Chữ số La Mã được thể hiện bởi bảy biểu tượng khác nhau: `I`, `V`, `X`, `L`, `C`, `D` và `M`.
Cho một số nguyên dương (nhỏ hơn 4000), hãy chuyển đổi nó thành chữ số La Mã.
Ví dụ: `num = 1994`, Output: `"MCMXCIV"` (M = 1000, CM = 900, XC = 90 và IV = 4).

### Phân tích thuật toán
- Chuẩn bị hai mảng song song: một mảng chứa các giá trị số nguyên giảm dần và một mảng chứa chuỗi La Mã tương ứng (bao gồm cả các trường hợp đặc biệt như `CM`, `XC`, `IV`).
- Duyệt qua các giá trị, chừng nào số `num` còn lớn hơn hoặc bằng giá trị hiện tại, trừ đi giá trị đó và nối chuỗi La Mã tương ứng vào kết quả.
- Sử dụng `StringBuilder` để thao tác nối chuỗi nhanh chóng.

### Mã nguồn Java
```java
public class Solution {
    public String intToRoman(int num) {
        int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] strs = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        StringBuilder sb = new StringBuilder();
        
        for (int i = 0; i < values.length; i++) {
            while (num >= values[i]) {
                num -= values[i];
                sb.append(strs[i]);
            }
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(1)$, vì số lượng phép toán tối đa chỉ phụ thuộc vào độ dài chuỗi kết quả hằng số (nhỏ hơn 4000).
- **Không gian (Space)**: $O(1)$, kích thước bộ nhớ cấp phát không đổi (cho `StringBuilder` và các mảng tĩnh).

## 13. Roman to Integer (Số La Mã sang số nguyên)
### Đề bài chi tiết
Chữ số La Mã thường được viết từ lớn đến nhỏ, từ trái sang phải. Tuy nhiên, có một số trường hợp đặc biệt như `I` đứng trước `V` (4) thay vì `IIII`.
Cho một chuỗi ký tự biểu diễn số La Mã, hãy chuyển đổi nó thành số nguyên dương.
Ví dụ: `s = "MCMXCIV"`, Output: `1994`.

### Phân tích thuật toán
- Duyệt qua chuỗi từ trái qua phải (hoặc phải qua trái).
- Nếu giá trị của ký tự hiện tại nhỏ hơn giá trị của ký tự ngay bên phải nó, ta trừ đi giá trị của nó khỏi tổng.
- Nếu lớn hơn hoặc bằng, ta cộng giá trị đó vào tổng.
- Dùng cấu trúc `Map` hoặc viết hàm `switch-case` nhỏ để tra cứu giá trị.

### Mã nguồn Java
```java
public class Solution {
    public int romanToInt(String s) {
        int sum = 0;
        int n = s.length();
        for (int i = 0; i < n; i++) {
            int currentVal = getValue(s.charAt(i));
            if (i < n - 1 && currentVal < getValue(s.charAt(i + 1))) {
                sum -= currentVal;
            } else {
                sum += currentVal;
            }
        }
        return sum;
    }
    
    private int getValue(char ch) {
        switch(ch) {
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
            default: return 0;
        }
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, trong đó $N$ là độ dài chuỗi.
- **Không gian (Space)**: $O(1)$, chỉ dùng vài biến số nguyên để lưu tổng.

## 14. Valid Palindrome (Chuỗi đối xứng hợp lệ)
### Đề bài chi tiết
Một cụm từ là chuỗi đối xứng (Palindrome) nếu sau khi chuyển tất cả chữ cái viết hoa thành chữ cái viết thường và loại bỏ tất cả các ký tự không phải là chữ cái và số, nó đọc xuôi và ngược giống nhau.
Cho một chuỗi `s`, trả về `true` nếu nó là palindrome, hoặc `false` nếu ngược lại.
Ví dụ: `s = "A man, a plan, a canal: Panama"`, Output: `true` (đọc là "amanaplanacanalpanama").

### Phân tích thuật toán
- Dùng `StringBuilder` để giữ lại các ký tự hợp lệ (chữ hoặc số) và chuyển thành chữ thường.
- Tuy nhiên, cách tối ưu hơn là dùng kỹ thuật hai con trỏ (Two Pointers) từ 2 đầu mảng chạy vào giữa, loại bỏ các ký tự đặc biệt trực tiếp trên chuỗi gốc, giảm thiểu không gian bộ nhớ.

### Mã nguồn Java
```java
public class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            char charLeft = s.charAt(left);
            char charRight = s.charAt(right);
            
            if (!Character.isLetterOrDigit(charLeft)) {
                left++;
            } else if (!Character.isLetterOrDigit(charRight)) {
                right--;
            } else {
                if (Character.toLowerCase(charLeft) != Character.toLowerCase(charRight)) {
                    return false;
                }
                left++;
                right--;
            }
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, duyệt qua chuỗi một lần.
- **Không gian (Space)**: $O(1)$, chỉ sử dụng hai con trỏ trên chuỗi ban đầu.

## 15. Valid Anagram (Đảo chữ hợp lệ)
### Đề bài chi tiết
Cho hai chuỗi `s` và `t`, viết hàm kiểm tra xem `t` có phải là Anagram (từ đảo chữ) của `s` hay không. Anagram là chuỗi được tạo ra bằng cách sắp xếp lại các ký tự của chuỗi ban đầu, sử dụng từng ký tự gốc đúng một lần.
Ví dụ: `s = "anagram", t = "nagaram"`, Output: `true`.

### Phân tích thuật toán
- Vì chuỗi chỉ gồm các ký tự chữ cái viết thường từ 'a' đến 'z', ta dùng một mảng số nguyên kích thước 26 để đếm số lần xuất hiện của từng ký tự.
- Cộng số đếm đối với chuỗi `s` và trừ đi đối với chuỗi `t`.
- Nếu mảng kết quả cuối cùng đều là 0, thì hai chuỗi là anagram của nhau.

### Mã nguồn Java
```java
public class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
            count[t.charAt(i) - 'a']--;
        }
        
        for (int val : count) {
            if (val != 0) return false;
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, với $N$ là chiều dài của chuỗi.
- **Không gian (Space)**: $O(1)$, vì mảng luôn có kích thước cố định là 26.

## 16. Group Anagrams (Nhóm các Anagram)
### Đề bài chi tiết
Cho một mảng các chuỗi `strs`, hãy nhóm các Anagram lại với nhau. Có thể trả về kết quả theo bất kỳ thứ tự nào.
Ví dụ: `strs = ["eat","tea","tan","ate","nat","bat"]`
Output: `[["bat"],["nat","tan"],["ate","eat","tea"]]`.

### Phân tích thuật toán
- Chúng ta cần một Key duy nhất (chuỗi đại diện) cho từng nhóm Anagram.
- Có thể lấy Key bằng cách sắp xếp các ký tự của chuỗi (vd `eat` -> `aet`), nhưng việc sort tốn $K \log K$.
- Cách tối ưu hơn: sử dụng mảng đếm (tần suất xuất hiện) tạo ra chuỗi khóa bằng `StringBuilder`. Nhóm các chuỗi vào một Map.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) return new ArrayList<>();
        Map<String, List<String>> map = new HashMap<>();
        
        for (String s : strs) {
            int[] count = new int[26];
            for (char c : s.toCharArray()) {
                count[c - 'a']++;
            }
            
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < 26; i++) {
                sb.append('#').append(count[i]);
            }
            String key = sb.toString();
            
            map.putIfAbsent(key, new ArrayList<>());
            map.get(key).add(s);
        }
        
        return new ArrayList<>(map.values());
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N \times K)$, với $N$ là số lượng chuỗi và $K$ là chiều dài lớn nhất của một chuỗi.
- **Không gian (Space)**: $O(N \times K)$, sử dụng HashMap để chứa tất cả các phần tử.

## 17. String to Integer (atoi)
### Đề bài chi tiết
Viết hàm `myAtoi(string s)` chuyển đổi một chuỗi ký tự thành số nguyên 32-bit có dấu.
Quy tắc:
1. Bỏ qua các khoảng trắng đứng đầu.
2. Kiểm tra dấu `+` hoặc `-`.
3. Đọc số nguyên tới khi gặp ký tự không phải số.
4. Giới hạn giá trị trả về trong khoảng $[-2^{31}, 2^{31}-1]$ (Integer Overflow).
Ví dụ: `s = "   -42"`, Output: `-42`.

### Phân tích thuật toán
- Xử lý tuần tự theo từng quy tắc: vòng lặp bỏ trống khoảng trắng, đọc dấu và tính số.
- Vấn đề khó nhất là xử lý giới hạn (Overflow). Khi nhân 10 và cộng chữ số mới, ta cần kiểm tra trước xem nó có làm tràn biến `int` không.
- Nếu tràn, trả về `Integer.MAX_VALUE` hoặc `Integer.MIN_VALUE`.

### Mã nguồn Java
```java
public class Solution {
    public int myAtoi(String s) {
        int index = 0, sign = 1, total = 0;
        int n = s.length();
        
        // Bỏ qua khoảng trắng
        while (index < n && s.charAt(index) == ' ') index++;
        
        // Kiểm tra dấu
        if (index < n && (s.charAt(index) == '+' || s.charAt(index) == '-')) {
            sign = s.charAt(index) == '+' ? 1 : -1;
            index++;
        }
        
        // Xử lý từng chữ số
        while (index < n) {
            int digit = s.charAt(index) - '0';
            if (digit < 0 || digit > 9) break;
            
            // Kiểm tra Overflow
            if (total > Integer.MAX_VALUE / 10 || 
                (total == Integer.MAX_VALUE / 10 && digit > Integer.MAX_VALUE % 10)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            
            total = total * 10 + digit;
            index++;
        }
        return total * sign;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, với $N$ là chiều dài chuỗi.
- **Không gian (Space)**: $O(1)$.

## 18. Implement strStr() / Find the Index of the First Occurrence in a String
### Đề bài chi tiết
Cho 2 chuỗi `haystack` và `needle`, trả về chỉ số (vị trí) xuất hiện đầu tiên của `needle` trong `haystack`. Nếu `needle` không thuộc `haystack`, trả về `-1`.
Ví dụ: `haystack = "sadbutsad", needle = "sad"`, Output: `0`.

### Phân tích thuật toán
- Cách dễ nhất là dùng Sliding Window để kiểm tra chuỗi con ở mỗi vị trí có khớp với `needle` không.
- Đối với phỏng vấn, đôi khi yêu cầu triển khai KMP Algorithm, nhưng một giải pháp trực tiếp $O(M \times N)$ thường cũng được chấp nhận, đặc biệt khi ngôn ngữ Java đã có `indexOf` tối ưu sẵn trong lõi. Dưới đây triển khai cách trực tiếp.

### Mã nguồn Java
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        int m = haystack.length();
        int n = needle.length();
        
        if (n == 0) return 0;
        
        for (int i = 0; i <= m - n; i++) {
            int j = 0;
            while (j < n && haystack.charAt(i + j) == needle.charAt(j)) {
                j++;
            }
            if (j == n) return i;
        }
        return -1;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O((M-N) \times N)$ trong trường hợp xấu nhất. 
- **Không gian (Space)**: $O(1)$.

## 19. Minimum Window Substring (Cửa sổ chuỗi con nhỏ nhất)
### Đề bài chi tiết
Cho hai chuỗi `s` và `t`, trả về chuỗi con ngắn nhất trong `s` sao cho nó chứa đầy đủ tất cả các ký tự của `t` (bao gồm cả các ký tự bị lặp lại). Nếu không có chuỗi nào thỏa mãn, trả về `""`.
Ví dụ: `s = "ADOBECODEBANC", t = "ABC"`, Output: `"BANC"`.

### Phân tích thuật toán
- Sử dụng Sliding Window (cửa sổ trượt) cùng một mảng đếm tần suất.
- Con trỏ `right` mở rộng dần để thu thập đủ số lượng ký tự cần thiết.
- Con trỏ `left` thu hẹp để tìm kích thước cửa sổ tối thiểu. Cập nhật kết quả min-length.

### Mã nguồn Java
```java
public class Solution {
    public String minWindow(String s, String t) {
        int[] map = new int[128];
        for (char c : t.toCharArray()) {
            map[c]++;
        }
        
        int left = 0, right = 0;
        int count = t.length(), minLen = Integer.MAX_VALUE, startIndex = 0;
        
        while (right < s.length()) {
            if (map[s.charAt(right++)]-- > 0) {
                count--;
            }
            
            while (count == 0) {
                if (right - left < minLen) {
                    minLen = right - left;
                    startIndex = left;
                }
                if (map[s.charAt(left++)]++ == 0) {
                    count++;
                }
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(startIndex, startIndex + minLen);
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(M + N)$, với $M, N$ là độ dài của hai chuỗi, mỗi ký tự duyệt qua khoảng 2 lần.
- **Không gian (Space)**: $O(1)$, vì mảng kí tự thuộc bảng mã ASCII kích thước cố định là 128.

## 20. Longest Substring Without Repeating Characters (Chuỗi con dài nhất không chứa ký tự lặp)
### Đề bài chi tiết
Cho một chuỗi `s`, hãy tìm chiều dài của chuỗi con dài nhất không chứa ký tự lặp lại.
Ví dụ: `s = "abcabcbb"`, Output: `3` (Chuỗi con là "abc").

### Phân tích thuật toán
- Dùng kỹ thuật Sliding Window. Con trỏ `right` chạy liên tục từng ký tự.
- Dùng HashSet để lưu giữ trạng thái, nếu ký tự đang xét đã tồn tại trong Set, ta liên tục nhích con trỏ `left` và xóa các ký tự khỏi Set cho tới khi ký tự trùng lặp được loại bỏ.
- Cập nhật độ dài max sau mỗi bước thêm mới ký tự vào Set.

### Mã nguồn Java
```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>();
        int left = 0, maxLen = 0;
        
        for (int right = 0; right < s.length(); right++) {
            while (set.contains(s.charAt(right))) {
                set.remove(s.charAt(left++));
            }
            set.add(s.charAt(right));
            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, tối đa $2N$ thao tác vì các con trỏ `left` và `right` không bao giờ đi lùi.
- **Không gian (Space)**: $O(\min(M, N))$, với $N$ là chiều dài chuỗi, $M$ là số lượng ký tự khác nhau trong bộ mã.

---

## 21. Longest Palindromic Substring (Chuỗi con đối xứng dài nhất)
### Đề bài chi tiết
Cho một chuỗi `s`, trả về chuỗi con đối xứng (Palindrome) dài nhất nằm trong `s`.
Ví dụ: `s = "babad"`, Output: `"bab"` (hoặc `"aba"` cũng hợp lệ).

### Phân tích thuật toán
- Sử dụng thuật toán "Mở rộng từ trung tâm" (Expand around center).
- Một chuỗi đối xứng sẽ tỏa ra từ tâm của nó. Tâm này có thể là một ký tự (chuỗi dài lẻ) hoặc nằm giữa hai ký tự (chuỗi dài chẵn).
- Duyệt qua từng vị trí trong chuỗi, thử mở rộng từ cả hai loại tâm và cập nhật độ dài lớn nhất đạt được.

### Mã nguồn Java
```java
public class Solution {
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
        int L = left, R = right;
        while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
            L--;
            R++;
        }
        return R - L - 1;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N^2)$, vì ta phải kiểm tra khoảng $2N-1$ tâm, mỗi tâm mở rộng tối đa $N$ bước.
- **Không gian (Space)**: $O(1)$, chỉ lưu vài biến số nguyên.

## 22. First Unique Character in a String (Ký tự độc nhất đầu tiên)
### Đề bài chi tiết
Cho chuỗi `s`, tìm ký tự đầu tiên không bị lặp lại trong chuỗi và trả về chỉ số của nó. Nếu không tồn tại, trả về `-1`.
Ví dụ: `s = "leetcode"`, Output: `0`. `s = "loveleetcode"`, Output: `2`.

### Phân tích thuật toán
- Dùng một mảng kích thước 26 (do chuỗi chỉ chứa ký tự in thường) để lưu tần suất xuất hiện của mỗi ký tự.
- Duyệt chuỗi lần 1 để cập nhật tần suất vào mảng đếm.
- Duyệt chuỗi lần 2 để kiểm tra, ký tự nào có tần suất bằng 1 đầu tiên thì trả về chỉ số của nó.

### Mã nguồn Java
```java
public class Solution {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;
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
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, duyệt qua chuỗi tối đa 2 lần.
- **Không gian (Space)**: $O(1)$, vì mảng luôn có kích thước cố định là 26.

## 23. Ransom Note (Thư tống tiền)
### Đề bài chi tiết
Cho hai chuỗi `ransomNote` và `magazine`, trả về `true` nếu `ransomNote` có thể được tạo thành từ các ký tự trong `magazine` (mỗi ký tự trong `magazine` chỉ được dùng 1 lần), ngược lại `false`.
Ví dụ: `ransomNote = "aa"`, `magazine = "aab"`, Output: `true`.

### Phân tích thuật toán
- Dùng một mảng đếm 26 ký tự để lưu số lần xuất hiện của từng ký tự trong `magazine`.
- Duyệt qua `ransomNote`, mỗi lần gặp một ký tự, trừ giá trị trong mảng đi 1.
- Nếu giá trị sau khi trừ nhỏ hơn 0, nghĩa là `magazine` không đủ ký tự đó, trả về `false`.

### Mã nguồn Java
```java
public class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] count = new int[26];
        
        for (char c : magazine.toCharArray()) {
            count[c - 'a']++;
        }
        
        for (char c : ransomNote.toCharArray()) {
            if (--count[c - 'a'] < 0) {
                return false;
            }
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(M + N)$, với $M, N$ là độ dài của 2 chuỗi.
- **Không gian (Space)**: $O(1)$.

## 24. Isomorphic Strings (Chuỗi đồng cấu)
### Đề bài chi tiết
Hai chuỗi `s` và `t` được gọi là Isomorphic nếu các ký tự trong `s` có thể được ánh xạ (map) để tạo thành `t`. Không có hai ký tự nào được ánh xạ vào cùng một ký tự, nhưng một ký tự có thể ánh xạ vào chính nó.
Ví dụ: `s = "egg"`, `t = "add"`, Output: `true`. `s = "foo"`, `t = "bar"`, Output: `false`.

### Phân tích thuật toán
- Có thể dùng 2 mảng lưu vết có kích thước 256 (bảng mã ASCII) để ghi lại vị trí xuất hiện cuối cùng của các ký tự tương ứng.
- Khi duyệt qua `s` và `t`, nếu vị trí xuất hiện cuối cùng của 2 ký tự này khác nhau, có nghĩa là chúng không được ánh xạ một-một, trả về `false`.

### Mã nguồn Java
```java
public class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        
        int[] map1 = new int[256];
        int[] map2 = new int[256];
        
        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            
            if (map1[c1] != map2[c2]) {
                return false;
            }
            
            // Lưu lại vị trí + 1 để tránh nhầm lẫn với giá trị mặc định 0
            map1[c1] = i + 1;
            map2[c2] = i + 1;
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$.
- **Không gian (Space)**: $O(1)$.

## 25. Word Pattern (Mẫu từ)
### Đề bài chi tiết
Cho chuỗi `pattern` và chuỗi `s`, xác định xem `s` có tuân theo đúng mẫu `pattern` hay không (ánh xạ 1-1).
Ví dụ: `pattern = "abba"`, `s = "dog cat cat dog"`, Output: `true`.

### Phân tích thuật toán
- Tách chuỗi `s` thành một mảng các từ dựa trên khoảng trắng.
- Sử dụng hai cấu trúc `HashMap`: một map ánh xạ `Character -> String`, một map ánh xạ `String -> Character` để đảm bảo không có trường hợp 2 ký tự ánh xạ cùng 1 từ (như `pattern = "abba"`, `s = "dog dog dog dog"`).

### Mã nguồn Java
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) return false;
        
        Map<Character, String> charToWord = new HashMap<>();
        Map<String, Character> wordToChar = new HashMap<>();
        
        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];
            
            if (charToWord.containsKey(c) && !charToWord.get(c).equals(word)) {
                return false;
            }
            if (wordToChar.containsKey(word) && wordToChar.get(word) != c) {
                return false;
            }
            
            charToWord.put(c, word);
            wordToChar.put(word, c);
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N + M)$, thời gian để tách chuỗi `s` thành các từ và đưa vào Hash.
- **Không gian (Space)**: $O(M)$, $M$ là số lượng từ lưu trữ trong HashMap.

## 26. Find All Anagrams in a String (Tìm tất cả các Anagram trong chuỗi)
### Đề bài chi tiết
Cho chuỗi `s` và `p`, trả về một mảng chứa chỉ số bắt đầu của các chuỗi con Anagram của `p` trong `s`.
Ví dụ: `s = "cbaebabacd"`, `p = "abc"`, Output: `[0, 6]`.

### Phân tích thuật toán
- Sử dụng Sliding Window kết hợp với đếm tần suất.
- Tạo một mảng đếm tần suất cho `p`.
- Trượt một cửa sổ có độ dài `p.length()` trên chuỗi `s`, với mỗi bước: thêm ký tự mới vào mảng đếm và xóa ký tự cũ nhất khỏi mảng đếm.
- So sánh hai mảng đếm, nếu giống nhau, ta tìm được một Anagram.

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> result = new ArrayList<>();
        if (s.length() < p.length()) return result;
        
        int[] pCount = new int[26];
        int[] sCount = new int[26];
        
        for (char c : p.toCharArray()) {
            pCount[c - 'a']++;
        }
        
        for (int i = 0; i < s.length(); i++) {
            sCount[s.charAt(i) - 'a']++;
            
            // Loại bỏ ký tự nằm ngoài kích thước cửa sổ
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
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, với $N$ là độ dài chuỗi `s` (bước kiểm tra mảng $26$ phần tử tốn thời gian hằng số).
- **Không gian (Space)**: $O(1)$.

## 27. Longest Repeating Character Replacement (Thay thế ký tự lặp dài nhất)
### Đề bài chi tiết
Cho chuỗi `s` (chỉ gồm chữ cái in hoa) và số nguyên `k`. Bạn có thể thay đổi tối đa `k` ký tự bất kỳ thành ký tự khác. Tìm chuỗi con dài nhất chứa các ký tự giống hệt nhau sau khi thực hiện thay đổi.
Ví dụ: `s = "AABABBA"`, `k = 1`, Output: `4` (thay 'A' ở vị trí giữa thành 'B', tạo thành "BBBB").

### Phân tích thuật toán
- Sử dụng Sliding Window. Trong mỗi cửa sổ, ta duy trì biến `maxCount` là số lần xuất hiện của ký tự phổ biến nhất.
- Số lượng ký tự cần thay thế để toàn bộ cửa sổ giống nhau bằng `(độ dài cửa sổ) - maxCount`.
- Nếu phép tính này lớn hơn `k`, ta buộc phải thu hẹp cửa sổ từ bên trái (tăng `left`) để tìm một cửa sổ hợp lệ mới.

### Mã nguồn Java
```java
public class Solution {
    public int characterReplacement(String s, int k) {
        int[] count = new int[26];
        int left = 0, maxCount = 0, maxLength = 0;
        
        for (int right = 0; right < s.length(); right++) {
            maxCount = Math.max(maxCount, ++count[s.charAt(right) - 'A']);
            
            while (right - left + 1 - maxCount > k) {
                count[s.charAt(left) - 'A']--;
                left++;
            }
            maxLength = Math.max(maxLength, right - left + 1);
        }
        return maxLength;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, mỗi ký tự được duyệt qua và loại bỏ nhiều nhất một lần.
- **Không gian (Space)**: $O(1)$.

## 28. Valid Palindrome II (Chuỗi đối xứng hợp lệ II)
### Đề bài chi tiết
Cho chuỗi `s`, trả về `true` nếu `s` có thể trở thành chuỗi đối xứng (Palindrome) sau khi xóa tối đa 1 ký tự.
Ví dụ: `s = "abca"`, Output: `true` (Xóa 'c' để thành "aba").

### Phân tích thuật toán
- Sử dụng hai con trỏ `left` và `right`. 
- Nếu tìm thấy 2 ký tự khác nhau `s[left] != s[right]`, ta chỉ có 2 sự lựa chọn: bỏ đi `s[left]` hoặc bỏ đi `s[right]`. 
- Ta kiểm tra xem phần còn lại (nếu bỏ `left` thì xét từ `left+1` đến `right`, nếu bỏ `right` thì xét từ `left` đến `right-1`) có phải là chuỗi đối xứng không. Nếu có 1 trường hợp đúng, trả về `true`.

### Mã nguồn Java
```java
public class Solution {
    public boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
            }
            left++;
            right--;
        }
        return true;
    }
    
    private boolean isPalindrome(String s, int i, int j) {
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) return false;
            i++;
            j--;
        }
        return true;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N)$, mặc dù có gọi hàm `isPalindrome`, phần chuỗi còn lại chỉ được duyệt nhiều nhất 1 lần.
- **Không gian (Space)**: $O(1)$.

## 29. Repeated String Match (Nối chuỗi lặp lại)
### Đề bài chi tiết
Cho hai chuỗi `a` và `b`, tìm số lần lặp lại `a` ít nhất (nối `a` với chính nó) sao cho `b` là chuỗi con của nó. Nếu không thể, trả về `-1`.
Ví dụ: `a = "abcd"`, `b = "cdabcdab"`, Output: `3` (Nối `a` ba lần thành `"abcdabcdabcd"`, lúc này `b` nằm ở giữa).

### Phân tích thuật toán
- Sử dụng `StringBuilder` để liên tục nối chuỗi `a` cho đến khi độ dài của nó lớn hơn hoặc bằng độ dài của `b`.
- Tại thời điểm này, kiểm tra xem `b` có phải là chuỗi con không.
- Nếu không, việc nối thêm chuỗi `a` đúng một lần nữa có thể tạo ra chuỗi con `b` bị vượt biên (ví dụ chuỗi `b` được phân bổ ở cuối chuỗi `a` trước và đầu chuỗi `a` sau).
- Nếu vẫn không có, trả về `-1`.

### Mã nguồn Java
```java
public class Solution {
    public int repeatedStringMatch(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        
        while (sb.length() < b.length()) {
            sb.append(a);
            count++;
        }
        
        if (sb.indexOf(b) != -1) return count;
        
        sb.append(a);
        count++;
        if (sb.indexOf(b) != -1) return count;
        
        return -1;
    }
}
```
### Độ phức tạp
- **Thời gian (Time)**: $O(N + M)$ đối với việc nối chuỗi, và hàm `indexOf` trong Java tối ưu có thể đạt $O((N+M) \times M)$ hoặc $O(N+M)$ tùy thuộc vào implementation.
- **Không gian (Space)**: $O(N + M)$, chuỗi StringBuilder dài tới tối đa $length(a) \times 2 + length(b)$.

## 30. Edit Distance (Khoảng cách chỉnh sửa)
### Đề bài chi tiết
Cho hai từ `word1` và `word2`, tính số thao tác tối thiểu (khoảng cách chỉnh sửa) để biến `word1` thành `word2`. 
Bạn được phép thực hiện các thao tác: Thêm một ký tự, Xóa một ký tự, Thay thế một ký tự.
Ví dụ: `word1 = "horse"`, `word2 = "ros"`, Output: `3` (Thay h->r, xóa r, xóa e).

### Phân tích thuật toán
- Sử dụng Quy hoạch động (Dynamic Programming). Bảng `dp[i][j]` lưu số thao tác tối thiểu để chuyển `i` ký tự đầu của `word1` thành `j` ký tự đầu của `word2`.
- Khởi tạo hàng 0 và cột 0 (đại diện cho việc biến chuỗi rỗng thành chuỗi độ dài `k` tốn `k` thao tác chèn/xóa).
- Nếu `word1[i-1] == word2[j-1]`, ta không tốn thao tác nào: `dp[i][j] = dp[i-1][j-1]`.
- Nếu khác, ta chọn 1 trong 3 thao tác tốn 1 bước phí nhỏ nhất: `dp[i][j] = 1 + min(thay thế, xóa, chèn)`.
  - Thay thế: `dp[i-1][j-1]`
  - Xóa: `dp[i-1][j]`
  - Chèn: `dp[i][j-1]`

### Mã nguồn Java
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
### Độ phức tạp
- **Thời gian (Time)**: $O(M \times N)$, điền tất cả các ô của bảng kích thước $M \times N$.
- **Không gian (Space)**: $O(M \times N)$, cần ma trận 2 chiều. Có thể tối ưu thành $O(\min(M, N))$ nếu chỉ dùng 2 hàng hoặc 1 mảng 1 chiều.
