# Expression Evaluation - Problems (Bài tập Đánh giá biểu thức)

Tài liệu này bao gồm 30 bài tập về đánh giá và phân tích biểu thức. 10 bài đầu tiên cung cấp đầy đủ đề bài, phân tích thuật toán, mã nguồn Java, và độ phức tạp. 20 bài còn lại được tóm tắt ý tưởng.

---

## 1. Evaluate Reverse Polish Notation (LeetCode 150)
### Đề bài chi tiết
Cho một mảng các chuỗi `tokens` biểu diễn một biểu thức toán học dưới dạng Hậu tố (Reverse Polish Notation / Postfix). Hãy đánh giá biểu thức đó và trả về một số nguyên thể hiện giá trị của nó. Các toán tử hợp lệ là `+`, `-`, `*`, `/`.

### Phân tích thuật toán
- Sử dụng cấu trúc dữ liệu Stack.
- Duyệt qua từng `token` trong mảng:
  - Nếu là toán hạng (số), ta đẩy (push) vào Stack.
  - Nếu là toán tử, ta rút (pop) 2 toán hạng trên cùng của Stack ra, thực hiện phép tính tương ứng rồi đẩy kết quả trở lại Stack.
- Lưu ý thứ tự pop: Toán hạng thứ hai được pop trước, tiếp đến là toán hạng thứ nhất (để thực hiện đúng cho phép trừ và phép chia `op1 - op2`).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (String token : tokens) {
            if (isOperator(token)) {
                int b = stack.pop();
                int a = stack.pop();
                if (token.equals("+")) stack.push(a + b);
                else if (token.equals("-")) stack.push(a - b);
                else if (token.equals("*")) stack.push(a * b);
                else if (token.equals("/")) stack.push(a / b);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }
    
    private boolean isOperator(String s) {
        return s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/");
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`, với `N` là số lượng token. Mỗi token được xử lý đúng một lần.
- **Không gian (Space):** `O(N)`, cần sử dụng bộ nhớ Stack chứa tối đa `N/2` toán hạng.

---

## 2. Basic Calculator (LeetCode 224)
### Đề bài chi tiết
Cho một chuỗi `s` đại diện cho một biểu thức toán học hợp lệ. Chỉ bao gồm các số không âm, ký tự `+`, `-`, `(`, `)` và khoảng trắng. Yêu cầu tính toán và trả về kết quả.

### Phân tích thuật toán
- Vì chỉ có phép cộng và phép trừ, độ ưu tiên không bị xáo trộn. Dấu ngoặc làm thay đổi trạng thái của toán tử (ví dụ `-(A - B)` thành `-A + B`).
- Sử dụng Stack để lưu lại trạng thái của kết quả hiện tại và dấu (sign) khi chúng ta mở một dấu ngoặc `(`.
- Khởi tạo `result = 0` và `sign = 1`.
- Khi gặp `(`, lưu trữ `result` và `sign` vào stack, khởi tạo lại cho tính toán bên trong.
- Khi gặp `)`, tính toán kết quả trong ngoặc, rồi áp dụng `sign` và cộng với `result` bên ngoài đã lưu ở stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int calculate(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        int result = 0;
        int number = 0;
        int sign = 1;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                number = 10 * number + (c - '0');
            } else if (c == '+') {
                result += sign * number;
                number = 0;
                sign = 1;
            } else if (c == '-') {
                result += sign * number;
                number = 0;
                sign = -1;
            } else if (c == '(') {
                stack.push(result);
                stack.push(sign);
                sign = 1;
                result = 0;
            } else if (c == ')') {
                result += sign * number;
                number = 0;
                result *= stack.pop();    // sign
                result += stack.pop();    // result outside
            }
        }
        if (number != 0) result += sign * number;
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`, với `N` là độ dài chuỗi.
- **Không gian:** `O(N)` cho Stack trong trường hợp dấu ngoặc lồng nhau quá sâu.

---

## 3. Basic Calculator II (LeetCode 227)
### Đề bài chi tiết
Tính toán chuỗi biểu thức chỉ chứa `+`, `-`, `*`, `/` và không có dấu ngoặc `()`.

### Phân tích thuật toán
- Sử dụng một biến `lastSign` (khởi tạo là `+`).
- Sử dụng Stack để chứa kết quả của các nhóm phép tính. Khi gặp phép `+` hoặc `-`, ta đẩy thẳng số (hoặc -số) vào Stack.
- Khi gặp `*` hoặc `/`, ta lấy số ở đỉnh Stack nhân/chia với số vừa đọc được rồi đẩy lại vào Stack.
- Cuối cùng cộng dồn tất cả các giá trị trong Stack.
- *Lưu ý:* Để tối ưu O(1) không gian, ta có thể dùng biến `lastNumber` thay thế cho Stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int calculate(String s) {
        if (s == null || s.isEmpty()) return 0;
        int len = s.length();
        Deque<Integer> stack = new ArrayDeque<>();
        int currentNumber = 0;
        char operation = '+';
        
        for (int i = 0; i < len; i++) {
            char currentChar = s.charAt(i);
            if (Character.isDigit(currentChar)) {
                currentNumber = (currentNumber * 10) + (currentChar - '0');
            }
            if (!Character.isDigit(currentChar) && !Character.isWhitespace(currentChar) || i == len - 1) {
                if (operation == '-') stack.push(-currentNumber);
                else if (operation == '+') stack.push(currentNumber);
                else if (operation == '*') stack.push(stack.pop() * currentNumber);
                else if (operation == '/') stack.push(stack.pop() / currentNumber);
                
                operation = currentChar;
                currentNumber = 0;
            }
        }
        
        int result = 0;
        while (!stack.isEmpty()) {
            result += stack.pop();
        }
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`, duyệt chuỗi một lần.
- **Không gian:** `O(N)` cho kích thước Stack.

---

## 4. Basic Calculator III (LeetCode 772 - Mở rộng)
### Đề bài chi tiết
Tính toán chuỗi biểu thức chứa tất cả `+`, `-`, `*`, `/` và `()`.

### Phân tích thuật toán
- Kết hợp bài 2 và bài 3.
- Dùng đệ quy hoặc kết hợp 2 Stack (một chứa giá trị, một chứa toán tử).
- Cấu trúc đệ quy (Recursive Descent) kết hợp với cấu trúc của bài 3 xử lý toán tử. Khi gặp `(`, ta gọi hàm đệ quy để tính toán khối bên trong, sau đó trả về giá trị cho vòng ngoài.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    int index = 0;
    
    public int calculate(String s) {
        return parse(s);
    }
    
    private int parse(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        char operation = '+';
        int num = 0;
        
        while (index < s.length()) {
            char c = s.charAt(index++);
            
            if (Character.isDigit(c)) {
                num = num * 10 + (c - '0');
            } else if (c == '(') {
                num = parse(s);
            }
            
            if (!Character.isDigit(c) && c != ' ' || index == s.length()) {
                if (operation == '+') stack.push(num);
                else if (operation == '-') stack.push(-num);
                else if (operation == '*') stack.push(stack.pop() * num);
                else if (operation == '/') stack.push(stack.pop() / num);
                
                operation = c;
                num = 0;
                
                if (c == ')') break; // Thoát đệ quy
            }
        }
        
        int result = 0;
        while (!stack.isEmpty()) result += stack.pop();
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`.
- **Không gian:** `O(N)` do Call Stack đệ quy.

---

## 5. Convert Infix to Postfix (Shunting Yard Algorithm)
### Đề bài chi tiết
Chuyển đổi một biểu thức Infix thành Postfix.

### Phân tích thuật toán
- Sử dụng thuật toán Shunting Yard của Dijkstra.
- Toán hạng đưa thẳng ra chuỗi Output.
- Toán tử `(` được push vào Stack.
- Toán tử `)` gây kích hoạt pop Stack cho tới khi gặp `(`.
- Các toán tử khác được kiểm tra độ ưu tiên với đỉnh Stack, pop các toán tử có độ ưu tiên lớn hơn hoặc bằng ra Output trước khi push toán tử mới.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    private int precedence(char c) {
        if (c == '^') return 3;
        else if (c == '/' || c == '*') return 2;
        else if (c == '+' || c == '-') return 1;
        return -1;
    }
    
    public String infixToPostfix(String exp) {
        StringBuilder result = new StringBuilder();
        Deque<Character> stack = new ArrayDeque<>();
        
        for (int i = 0; i < exp.length(); i++) {
            char c = exp.charAt(i);
            
            if (Character.isLetterOrDigit(c)) {
                result.append(c);
            } else if (c == '(') {
                stack.push(c);
            } else if (c == ')') {
                while (!stack.isEmpty() && stack.peek() != '(') {
                    result.append(stack.pop());
                }
                stack.pop();
            } else {
                while (!stack.isEmpty() && precedence(c) <= precedence(stack.peek())) {
                    result.append(stack.pop());
                }
                stack.push(c);
            }
        }
        
        while (!stack.isEmpty()) {
            result.append(stack.pop());
        }
        
        return result.toString();
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`.
- **Không gian:** `O(N)`.

---

## 6. Convert Prefix to Postfix
### Đề bài chi tiết
Cho chuỗi tiền tố (Prefix). Chuyển đổi nó thành hậu tố (Postfix). Ví dụ `*+AB-CD` -> `AB+CD-*`.

### Phân tích thuật toán
- Duyệt chuỗi từ Phải sang Trái (ngược lại với Postfix).
- Nếu là toán hạng, đẩy vào Stack.
- Nếu là toán tử, pop 2 toán hạng ra (giả sử A và B), nối thành chuỗi theo định dạng `A + B + operator`. Sau đó đẩy chuỗi này lại vào Stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    private boolean isOperator(char x) {
        return x == '+' || x == '-' || x == '*' || x == '/';
    }
    
    public String prefixToPostfix(String pre_exp) {
        Deque<String> stack = new ArrayDeque<>();
        int length = pre_exp.length();
        
        for (int i = length - 1; i >= 0; i--) {
            char c = pre_exp.charAt(i);
            if (isOperator(c)) {
                String op1 = stack.pop();
                String op2 = stack.pop();
                String temp = op1 + op2 + c;
                stack.push(temp);
            } else {
                stack.push(c + "");
            }
        }
        return stack.pop();
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`
- **Không gian:** `O(N)`

---

## 7. Convert Postfix to Prefix
### Đề bài chi tiết
Cho chuỗi hậu tố (Postfix). Chuyển đổi thành tiền tố (Prefix). Ví dụ `AB+CD-*` -> `*+AB-CD`.

### Phân tích thuật toán
- Duyệt chuỗi từ Trái sang Phải.
- Nếu gặp toán hạng, đẩy vào Stack.
- Nếu gặp toán tử, pop 2 toán hạng (B và A). Nối chuỗi theo cấu trúc `operator + A + B`. Đẩy trở lại Stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    private boolean isOperator(char x) {
        return x == '+' || x == '-' || x == '*' || x == '/';
    }
    
    public String postfixToPrefix(String post_exp) {
        Deque<String> stack = new ArrayDeque<>();
        
        for (int i = 0; i < post_exp.length(); i++) {
            char c = post_exp.charAt(i);
            if (isOperator(c)) {
                String op1 = stack.pop();
                String op2 = stack.pop();
                String temp = c + op2 + op1;
                stack.push(temp);
            } else {
                stack.push(c + "");
            }
        }
        return stack.pop();
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`
- **Không gian:** `O(N)`

---

## 8. Decode String (LeetCode 394)
### Đề bài chi tiết
Cho một chuỗi đã được mã hóa. Hãy giải mã nó. Quy tắc giải mã: `k[encoded_string]` sẽ được lặp lại chính xác `k` lần.

### Phân tích thuật toán
- Đây cũng là một dạng biểu thức toán học chứa toán tử nhân (lặp chuỗi).
- Sử dụng 2 Stack: `countStack` chứa số lần lặp và `stringStack` chứa các đoạn chuỗi chưa hoàn thành.
- Nếu gặp chữ số, cộng dồn thành số đếm.
- Nếu gặp `[`, đẩy số đếm và StringBuilder hiện tại vào Stacks. Khởi tạo lại.
- Nếu gặp `]`, pop số lần lặp, nối chuỗi con vừa tạo số lần đó vào chuỗi đã pop từ `stringStack`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public String decodeString(String s) {
        Deque<Integer> countStack = new ArrayDeque<>();
        Deque<StringBuilder> stringStack = new ArrayDeque<>();
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
- **Thời gian:** `O(N + K)` với K là số lượng ký tự đầu ra.
- **Không gian:** `O(N)` cho các Stacks.

---

## 9. Parse Lisp Expression (LeetCode 736)
### Đề bài chi tiết
Đánh giá một biểu thức Lisp được đơn giản hóa, có 3 hàm cốt lõi: `add`, `mult`, và `let`. `let` cung cấp phạm vi biến.

### Phân tích thuật toán
- Xử lý phức tạp với Scopes (Phạm vi). Các biến gán bởi `let` chỉ tồn tại trong nhánh con của biểu thức đó.
- Dùng đệ quy để evaluate biểu thức. Đưa vào một `HashMap` biểu diễn môi trường các biến hiện tại.
- Để duy trì phạm vi phân tách độc lập (Scoping), hãy copy `HashMap` mỗi khi bước vào một cấp độ đệ quy mới.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int evaluate(String expression) {
        return eval(expression, new HashMap<>());
    }
    
    private int eval(String exp, Map<String, Integer> scope) {
        if (exp.charAt(0) != '(') {
            if (Character.isDigit(exp.charAt(0)) || exp.charAt(0) == '-') return Integer.parseInt(exp);
            return scope.get(exp);
        }
        
        Map<String, Integer> nextScope = new HashMap<>(scope);
        List<String> tokens = parseTokens(exp.substring(1, exp.length() - 1));
        
        if (tokens.get(0).equals("add")) {
            return eval(tokens.get(1), nextScope) + eval(tokens.get(2), nextScope);
        } else if (tokens.get(0).equals("mult")) {
            return eval(tokens.get(1), nextScope) * eval(tokens.get(2), nextScope);
        } else { // let
            for (int i = 1; i < tokens.size() - 1; i += 2) {
                nextScope.put(tokens.get(i), eval(tokens.get(i + 1), nextScope));
            }
            return eval(tokens.get(tokens.size() - 1), nextScope);
        }
    }
    
    private List<String> parseTokens(String s) {
        List<String> res = new ArrayList<>();
        int i = 0;
        while (i < s.length()) {
            if (s.charAt(i) == ' ') { i++; continue; }
            if (s.charAt(i) == '(') {
                int bal = 1, j = i + 1;
                while (bal > 0) {
                    if (s.charAt(j) == '(') bal++;
                    if (s.charAt(j) == ')') bal--;
                    j++;
                }
                res.add(s.substring(i, j));
                i = j;
            } else {
                int j = i;
                while (j < s.length() && s.charAt(j) != ' ') j++;
                res.add(s.substring(i, j));
                i = j;
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N^2)` do thao tác String parsing ngốn nhiều chi phí.
- **Không gian:** `O(N)` với N là số cấp đệ quy và hash maps tạo mới.

---

## 10. Construct Binary Expression Tree from Postfix Expression (LeetCode 1628)
### Đề bài chi tiết
Từ một mảng các token ở định dạng Postfix, hãy xây dựng một Cây Cú Pháp Trừu Tượng (Abstract Syntax Tree / AST) và trả về Root của cây.

### Phân tích thuật toán
- Sử dụng Stack của loại `Node`.
- Nếu gặp toán hạng, khởi tạo `Node` đại diện toán hạng và đẩy vào Stack.
- Nếu gặp toán tử, khởi tạo `Node` đại diện toán tử, lấy 2 node ở đầu Stack làm 2 con (`right` pop trước, `left` pop sau). Sau đó đẩy ngược Node cha này vào Stack.

### Mã nguồn Java
```java
import java.util.*;

abstract class Node {
    public abstract int evaluate();
}

class NumNode extends Node {
    int val;
    public NumNode(int val) { this.val = val; }
    public int evaluate() { return val; }
}

class OpNode extends Node {
    char op;
    Node left, right;
    public OpNode(char op, Node left, Node right) {
        this.op = op;
        this.left = left;
        this.right = right;
    }
    public int evaluate() {
        if (op == '+') return left.evaluate() + right.evaluate();
        if (op == '-') return left.evaluate() - right.evaluate();
        if (op == '*') return left.evaluate() * right.evaluate();
        if (op == '/') return left.evaluate() / right.evaluate();
        return 0;
    }
}

public class TreeBuilder {
    Node buildTree(String[] postfix) {
        Deque<Node> stack = new ArrayDeque<>();
        for (String token : postfix) {
            if ("+-*/".contains(token)) {
                Node right = stack.pop();
                Node left = stack.pop();
                stack.push(new OpNode(token.charAt(0), left, right));
            } else {
                stack.push(new NumNode(Integer.parseInt(token)));
            }
        }
        return stack.pop();
    }
}
```
### Độ phức tạp
- **Thời gian:** `O(N)`.
- **Không gian:** `O(N)` cho kích thước cây.

---

## 11. Prefix Evaluation
### Đề bài chi tiết
Cho một biểu thức tiền tố (Prefix / Polish Notation) dưới dạng một mảng các chuỗi `tokens`. Hãy đánh giá biểu thức đó và trả về kết quả dưới dạng số nguyên. Các toán tử bao gồm `+`, `-`, `*`, `/`.

### Phân tích thuật toán
- Tương tự như đánh giá biểu thức hậu tố (Postfix), nhưng ta sẽ duyệt mảng `tokens` từ phải sang trái.
- Sử dụng cấu trúc dữ liệu Stack.
- Duyệt qua từng `token`:
  - Nếu là toán hạng, ta đẩy (push) vào Stack.
  - Nếu là toán tử, ta rút (pop) 2 toán hạng trên cùng của Stack ra, thực hiện phép tính và đẩy kết quả trở lại Stack. Chú ý thứ tự: toán hạng lấy ra đầu tiên là toán hạng thứ nhất (op1), toán hạng lấy ra thứ hai là toán hạng thứ hai (op2) - ngược lại so với Postfix.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int evaluatePrefix(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = tokens.length - 1; i >= 0; i--) {
            String token = tokens[i];
            if (isOperator(token)) {
                int op1 = stack.pop();
                int op2 = stack.pop();
                if (token.equals("+")) stack.push(op1 + op2);
                else if (token.equals("-")) stack.push(op1 - op2);
                else if (token.equals("*")) stack.push(op1 * op2);
                else if (token.equals("/")) stack.push(op1 / op2);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }
    
    private boolean isOperator(String s) {
        return s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/");
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` vì mỗi phần tử được duyệt đúng một lần.
- **Không gian (Space):** `O(N)` để lưu trữ các toán hạng trong Stack.

---

## 12. Infix Evaluation in One Pass
### Đề bài chi tiết
Cho một biểu thức trung tố (Infix) dưới dạng chuỗi gồm số, các toán tử `+`, `-`, `*`, `/` và dấu ngoặc `()`. Hãy đánh giá biểu thức này trong một lần duyệt (One Pass) mà không cần chuyển toàn bộ biểu thức sang dạng Hậu tố (Postfix) trước.

### Phân tích thuật toán
- Sử dụng hai Stack: `values` (chứa số) và `ops` (chứa toán tử và dấu ngoặc).
- Duyệt chuỗi từ trái sang phải:
  - Nếu là khoảng trắng, bỏ qua.
  - Nếu là số, phân tích toàn bộ số đó và đẩy vào `values`.
  - Nếu là `(`, đẩy vào `ops`.
  - Nếu là `)`, thực hiện tính toán với các toán tử trong `ops` cho đến khi gặp `(` ở đỉnh `ops`.
  - Nếu là toán tử `+`, `-`, `*`, `/`, thực hiện tính toán cho các toán tử trong `ops` có độ ưu tiên lớn hơn hoặc bằng nó, sau đó đẩy toán tử hiện tại vào `ops`.
- Cuối cùng, xử lý nốt các toán tử còn lại trong `ops`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int evaluateInfix(String s) {
        Deque<Integer> values = new ArrayDeque<>();
        Deque<Character> ops = new ArrayDeque<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == ' ') continue;
            
            if (Character.isDigit(c)) {
                int num = 0;
                while (i < s.length() && Character.isDigit(s.charAt(i))) {
                    num = num * 10 + (s.charAt(i) - '0');
                    i++;
                }
                i--;
                values.push(num);
            } else if (c == '(') {
                ops.push(c);
            } else if (c == ')') {
                while (ops.peek() != '(') {
                    values.push(applyOp(ops.pop(), values.pop(), values.pop()));
                }
                ops.pop();
            } else if (c == '+' || c == '-' || c == '*' || c == '/') {
                while (!ops.isEmpty() && hasPrecedence(c, ops.peek())) {
                    values.push(applyOp(ops.pop(), values.pop(), values.pop()));
                }
                ops.push(c);
            }
        }
        
        while (!ops.isEmpty()) {
            values.push(applyOp(ops.pop(), values.pop(), values.pop()));
        }
        
        return values.pop();
    }
    
    private boolean hasPrecedence(char op1, char op2) {
        if (op2 == '(' || op2 == ')') return false;
        if ((op1 == '*' || op1 == '/') && (op2 == '+' || op2 == '-')) return false;
        return true;
    }
    
    private int applyOp(char op, int b, int a) {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': return a / b;
        }
        return 0;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là chiều dài của chuỗi.
- **Không gian (Space):** `O(N)` cho các Stacks `values` và `ops`.

---

## 13. Minimum Add to Make Parentheses Valid (Leetcode 921)
### Đề bài chi tiết
Một chuỗi dấu ngoặc đơn hợp lệ nếu nó rỗng, hoặc có dạng `(A)`, hoặc `AB`, trong đó A và B là các chuỗi hợp lệ.
Cho một chuỗi `s` chỉ chứa `(` và `)`. Bạn cần tìm số lượng ít nhất các dấu ngoặc cần chèn vào (ở bất kỳ vị trí nào) để làm cho chuỗi hợp lệ.

### Phân tích thuật toán
- Ta có thể giải bằng cách dùng một Stack để loại bỏ các cặp ngoặc hợp lệ, sau đó đếm số phần tử còn lại trong Stack.
- Tối ưu bộ nhớ: Sử dụng biến `open_needed` và `close_needed` thay vì Stack thực sự.
  - Khi gặp `(`, ta tăng số ngoặc đóng cần thiết `close_needed++`.
  - Khi gặp `)`, nếu có ngoặc mở để khớp (`close_needed > 0`), ta giảm `close_needed--`. Nếu không, ta thiếu một ngoặc mở, tăng `open_needed++`.
- Tổng số ngoặc cần thêm là `open_needed + close_needed`.

### Mã nguồn Java
```java
class Solution {
    public int minAddToMakeValid(String s) {
        int openNeeded = 0;
        int closeNeeded = 0;
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                closeNeeded++;
            } else if (c == ')') {
                if (closeNeeded > 0) {
                    closeNeeded--;
                } else {
                    openNeeded++;
                }
            }
        }
        return openNeeded + closeNeeded;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là chiều dài của chuỗi `s`.
- **Không gian (Space):** `O(1)` vì chỉ sử dụng các biến đếm nguyên.

---

## 14. Valid Parentheses (Leetcode 20)
### Đề bài chi tiết
Cho một chuỗi `s` chỉ chứa các ký tự `'('`, `')'`, `'{'`, `'}'`, `'['` và `']'`. Kiểm tra xem chuỗi có hợp lệ hay không.
Một chuỗi hợp lệ nếu:
1. Các ngoặc mở phải được đóng bởi các ngoặc đóng cùng loại.
2. Các ngoặc mở phải được đóng theo đúng thứ tự.
3. Mọi ngoặc đóng đều có một ngoặc mở tương ứng cùng loại.

### Phân tích thuật toán
- Đây là bài toán Stack kinh điển.
- Duyệt qua từng ký tự của chuỗi:
  - Nếu là dấu ngoặc mở (`(`, `{`, `[`), đẩy (push) vào Stack.
  - Nếu là dấu ngoặc đóng, kiểm tra Stack. Nếu Stack rỗng hoặc phần tử ở đỉnh Stack không khớp với loại dấu đóng hiện tại, chuỗi không hợp lệ. Nếu khớp, lấy phần tử đỉnh ra (pop).
- Cuối cùng, nếu Stack rỗng thì chuỗi hợp lệ, ngược lại thì không.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(')');
            } else if (c == '{') {
                stack.push('}');
            } else if (c == '[') {
                stack.push(']');
            } else if (stack.isEmpty() || stack.pop() != c) {
                return false;
            }
        }
        return stack.isEmpty();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là chiều dài chuỗi.
- **Không gian (Space):** `O(N)` do phải chứa số lượng phần tử vào Stack (trường hợp toàn ngoặc mở).

---

## 15. Longest Valid Parentheses (Leetcode 32)
### Đề bài chi tiết
Cho một chuỗi chỉ chứa các ký tự `(` và `)`. Hãy tìm độ dài của chuỗi con (substring) dài nhất gồm các dấu ngoặc hợp lệ (well-formed).

### Phân tích thuật toán
- Cách dùng Stack: Ta lưu trữ chỉ số (index) của các dấu ngoặc.
- Khởi tạo Stack với giá trị `-1` làm đáy để tính độ dài dễ dàng.
- Duyệt chuỗi:
  - Nếu gặp `(`, đẩy chỉ số hiện tại vào Stack.
  - Nếu gặp `)`, lấy đỉnh Stack ra (pop).
    - Nếu Stack trở nên rỗng, có nghĩa là `)` này không hợp lệ, ta đẩy chỉ số hiện tại của nó vào làm mốc mới.
    - Nếu Stack không rỗng, độ dài chuỗi hợp lệ tính bằng `chỉ số hiện tại - chỉ số ở đỉnh Stack`. Cập nhật độ dài lớn nhất.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int longestValidParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(-1);
        int maxLength = 0;
        
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else {
                stack.pop();
                if (stack.isEmpty()) {
                    stack.push(i);
                } else {
                    maxLength = Math.max(maxLength, i - stack.peek());
                }
            }
        }
        return maxLength;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`.
- **Không gian (Space):** `O(N)` vì sử dụng Stack lưu vị trí (index).

---

## 16. Score of Parentheses (Leetcode 856)
### Đề bài chi tiết
Cho một chuỗi dấu ngoặc hợp lệ `s`. Tính điểm số của chuỗi theo các quy tắc sau:
- `()` tính là 1 điểm.
- `AB` (A ghép với B, cả hai hợp lệ) có điểm là `score(A) + score(B)`.
- `(A)` có điểm là `2 * score(A)`.

### Phân tích thuật toán
- Ta dùng một Stack lưu trữ điểm số ở mỗi cấp độ lồng (depth).
- Khởi tạo Stack với điểm 0 cho cấp độ ngoài cùng.
- Khi gặp `(`, ta bước vào một cấp độ sâu hơn, đẩy 0 vào Stack.
- Khi gặp `)`, ta đóng cấp độ hiện tại:
  - Lấy (pop) điểm số của cấp độ hiện tại (gọi là `v`).
  - Điểm của phần tử vừa đóng sẽ là `max(2 * v, 1)`.
  - Cộng điểm này vào cấp độ bên trên (đỉnh mới của Stack).

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int scoreOfParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0); // Cấp độ 0
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(0);
            } else {
                int v = stack.pop();
                int currentScore = Math.max(2 * v, 1);
                stack.push(stack.pop() + currentScore);
            }
        }
        return stack.pop();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`
- **Không gian (Space):** `O(N)` cho Stack, tối đa bằng độ sâu lớn nhất của ngoặc.

---

## 17. Evaluate Boolean Binary Tree (Leetcode 2331)
### Đề bài chi tiết
Cho gốc (root) của một cây nhị phân boolean. 
- Node lá (không có con) có giá trị là 0 (False) hoặc 1 (True).
- Node nội (có con) có giá trị là 2 (OR) hoặc 3 (AND).
Hãy đánh giá cây này theo biểu thức logic boolean và trả về kết quả True/False.

### Phân tích thuật toán
- Đây là một bài đánh giá Abstract Syntax Tree (AST) đơn giản.
- Ta có thể giải bằng đệ quy.
- Nếu node là lá (giá trị 0 hoặc 1), trả về `val == 1`.
- Nếu node là OR (giá trị 2), gọi đệ quy đánh giá hai nhánh trái phải và áp dụng phép `||`.
- Nếu node là AND (giá trị 3), đánh giá trái phải và áp dụng phép `&&`.

### Mã nguồn Java
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
class Solution {
    public boolean evaluateTree(TreeNode root) {
        if (root.val == 0) return false;
        if (root.val == 1) return true;
        
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
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là số lượng nodes của cây.
- **Không gian (Space):** `O(H)` với H là chiều cao của cây (chi phí Call Stack đệ quy).

---

## 18. Parsing A Boolean Expression (Leetcode 1106)
### Đề bài chi tiết
Cho một biểu thức boolean dưới dạng chuỗi `expression`. Đánh giá và trả về kết quả của nó.
Biểu thức có các dạng:
- `"t"`, đánh giá thành True.
- `"f"`, đánh giá thành False.
- `"!(expr)"`, phép NOT.
- `"&(expr1,expr2,...)"`, phép AND.
- `"|(expr1,expr2,...)"`, phép OR.

### Phân tích thuật toán
- Dùng một Stack để lưu các ký tự.
- Duyệt qua từng ký tự của chuỗi:
  - Nếu khác `)`, phẩy `,`, khoảng trắng thì đẩy vào Stack.
  - Khi gặp `)`, ta rút (pop) lần lượt các phần tử từ Stack cho tới khi gặp `(`. Các phần tử rút ra là tham số (các giá trị `t` hoặc `f`).
  - Sau khi bỏ đi `(`, đỉnh Stack hiện tại sẽ chứa một toán tử (`!`, `&`, `|`). Lấy toán tử này ra và đánh giá dựa trên các tham số thu thập được, sau đó đẩy kết quả (`'t'` hoặc `'f'`) lại vào Stack.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public boolean parseBoolExpr(String expression) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : expression.toCharArray()) {
            if (c == ',' || c == ' ') continue;
            if (c != ')') {
                stack.push(c);
            } else {
                List<Character> args = new ArrayList<>();
                while (stack.peek() != '(') {
                    args.add(stack.pop());
                }
                stack.pop(); // Bỏ '('
                char op = stack.pop();
                stack.push(evaluate(op, args));
            }
        }
        return stack.pop() == 't';
    }
    
    private char evaluate(char op, List<Character> args) {
        if (op == '!') {
            return args.get(0) == 't' ? 'f' : 't';
        } else if (op == '&') {
            for (char arg : args) {
                if (arg == 'f') return 'f';
            }
            return 't';
        } else { // op == '|'
            for (char arg : args) {
                if (arg == 't') return 't';
            }
            return 'f';
        }
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` mỗi ký tự chỉ được push và pop một lần.
- **Không gian (Space):** `O(N)` cho kích thước Stack.

---

## 19. Calculator with Variables
### Đề bài chi tiết
Mở rộng bộ tính toán cơ bản (Basic Calculator). Chuỗi biểu thức `s` có thể chứa biến số là một chuỗi chữ cái (ví dụ `x`, `abc`). Một từ điển (HashMap) `variables` được truyền vào mang theo giá trị của các biến số này. Yêu cầu tính toán kết quả cuối cùng. (Biết rằng biểu thức có các toán tử `+`, `-`, `*`, `/`, ngoặc tròn).

### Phân tích thuật toán
- Đây là biến thể của `Basic Calculator III` kết hợp thay thế biến.
- Khi phân tích (parse) chuỗi:
  - Nếu gặp một chuỗi chữ cái (variable name), ta đọc toàn bộ tên biến và tra cứu (lookup) trong bảng băm `variables` để lấy giá trị (dạng số).
  - Phần còn lại tương tự quá trình Evaluate với Stack và Đệ quy như trong bài Basic Calculator III.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    int index = 0;
    
    public int calculate(String s, Map<String, Integer> variables) {
        return parse(s, variables);
    }
    
    private int parse(String s, Map<String, Integer> variables) {
        Deque<Integer> stack = new ArrayDeque<>();
        char operation = '+';
        int num = 0;
        
        while (index < s.length()) {
            char c = s.charAt(index);
            
            if (Character.isLetter(c)) {
                StringBuilder varName = new StringBuilder();
                while (index < s.length() && Character.isLetter(s.charAt(index))) {
                    varName.append(s.charAt(index++));
                }
                num = variables.getOrDefault(varName.toString(), 0);
                continue; // Không tăng index vì vòng while nội bộ đã tăng
            } else if (Character.isDigit(c)) {
                num = num * 10 + (c - '0');
            } else if (c == '(') {
                index++;
                num = parse(s, variables);
            }
            
            if (index < s.length() && !Character.isLetterOrDigit(s.charAt(index)) && s.charAt(index) != ' ' || index == s.length() - 1) {
                if (operation == '+') stack.push(num);
                else if (operation == '-') stack.push(-num);
                else if (operation == '*') stack.push(stack.pop() * num);
                else if (operation == '/') stack.push(stack.pop() / num);
                
                if (index < s.length()) {
                    operation = s.charAt(index);
                }
                num = 0;
                if (index < s.length() && s.charAt(index) == ')') {
                    index++;
                    break;
                }
            }
            index++;
        }
        
        int result = 0;
        while (!stack.isEmpty()) result += stack.pop();
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`
- **Không gian (Space):** `O(N)` (cho Call Stack và Stack chứa toán hạng).

---

## 20. Check If Word Is Valid After Substitutions (Leetcode 1003)
### Đề bài chi tiết
Cho một chuỗi `s`. Một chuỗi được coi là hợp lệ nếu nó bắt đầu dưới dạng chuỗi rỗng `""`. Bạn có thể lặp lại việc chèn chuỗi `"abc"` vào bất cứ đâu trong chuỗi hợp lệ hiện tại để nhận được chuỗi mới hợp lệ. Kiểm tra xem chuỗi `s` đã cho có hợp lệ hay không.
Ví dụ: `"aabcbc"` là hợp lệ, `"abccba"` là không hợp lệ.

### Phân tích thuật toán
- Bài toán này có thể xử lý tương tự như bài đánh giá ngoặc đơn hợp lệ, nhưng thay vì các cặp ngoặc, ta xử lý chuỗi 3 ký tự liên tiếp `"abc"`.
- Dùng một Stack chứa từng ký tự:
  - Khi chèn một ký tự vào Stack, nếu ký tự đó là `'c'`, ta kiểm tra 2 ký tự trên đỉnh Stack xem có phải lần lượt là `'b'` và `'a'` hay không.
  - Nếu đúng, ta lấy `'b'` và `'a'` ra (xóa cụm `"abc"` khỏi Stack).
  - Nếu không đúng, ta giữ nguyên và đẩy `'c'` vào.
- Kết thúc, nếu Stack rỗng thì chuỗi hợp lệ.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == 'c') {
                if (stack.size() >= 2) {
                    char first = stack.pop();
                    char second = stack.pop();
                    if (first == 'b' && second == 'a') {
                        continue;
                    } else {
                        stack.push(second);
                        stack.push(first);
                        stack.push('c');
                    }
                } else {
                    return false;
                }
            } else {
                stack.push(c);
            }
        }
        return stack.isEmpty();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`
- **Không gian (Space):** `O(N)`

---

## 21. Remove Outermost Parentheses (Leetcode 1021)
### Đề bài chi tiết
Một chuỗi dấu ngoặc đơn hợp lệ là một chuỗi rỗng `""`, `"(" + A + ")"`, hoặc `A + B`, trong đó `A` và `B` là các chuỗi ngoặc đơn hợp lệ.
Một chuỗi ngoặc hợp lệ là cơ bản (primitive) nếu nó không rỗng và không thể phân chia thành hai chuỗi ngoặc hợp lệ không rỗng.
Cho một chuỗi `s` đại diện cho một biểu thức ngoặc hợp lệ, hãy loại bỏ các dấu ngoặc ngoài cùng của mọi chuỗi cơ bản thành phần.

### Phân tích thuật toán
- Ta sử dụng một biến đếm (counter) để theo dõi độ sâu của các dấu ngoặc thay vì một Stack vật lý (để tiết kiệm bộ nhớ).
- Duyệt qua từng ký tự của chuỗi:
  - Nếu gặp dấu `(`, ta kiểm tra xem nếu bộ đếm `> 0` thì đây không phải là ngoặc ngoài cùng, ta thêm `(` vào kết quả. Sau đó tăng bộ đếm.
  - Nếu gặp dấu `)`, ta giảm bộ đếm. Nếu bộ đếm `> 0`, đây không phải là ngoặc ngoài cùng, ta thêm `)` vào kết quả.

### Mã nguồn Java
```java
class Solution {
    public String removeOuterParentheses(String s) {
        StringBuilder sb = new StringBuilder();
        int opened = 0;
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                if (opened > 0) {
                    sb.append(c);
                }
                opened++;
            } else if (c == ')') {
                opened--;
                if (opened > 0) {
                    sb.append(c);
                }
            }
        }
        
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là độ dài của chuỗi `s`.
- **Không gian (Space):** `O(N)` để lưu chuỗi kết quả (nếu không tính kết quả trả về, độ phức tạp không gian là `O(1)`).

---

## 22. Reverse Substrings Between Each Pair of Parentheses (Leetcode 1190)
### Đề bài chi tiết
Cho một chuỗi `s` chứa các chữ cái in thường và cặp dấu ngoặc đơn `()` hợp lệ. Bạn cần đảo ngược các chuỗi nằm trong mỗi cặp ngoặc đơn, bắt đầu từ cặp ngoặc nằm sâu nhất (trong cùng).
Trả về chuỗi cuối cùng sau khi đã xóa hết dấu ngoặc.

### Phân tích thuật toán
- Dùng một Stack để lưu trữ chỉ số (index) của các dấu ngoặc mở `(`.
- Duyệt qua mảng:
  - Khi gặp `(`, ta lưu chỉ số của nó vào Stack.
  - Khi gặp `)`, ta lấy chỉ số mở `start` tương ứng từ đỉnh Stack ra. Ta tiến hành đảo ngược chuỗi từ `start + 1` đến vị trí hiện tại trừ 1 (sử dụng một mảng ký tự).
- Cuối cùng, lọc bỏ tất cả dấu ngoặc khỏi chuỗi để được kết quả.
*(Tối ưu: Có thể giải `O(N)` bằng phương pháp thuật toán đếm cầu (Wormhole) nhưng Stack approach `O(N^2)` vẫn được chấp nhận ở độ dài chuỗi ngắn)*.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public String reverseParentheses(String s) {
        char[] arr = s.toCharArray();
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == '(') {
                stack.push(i);
            } else if (arr[i] == ')') {
                int start = stack.pop();
                reverse(arr, start + 1, i - 1);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (char c : arr) {
            if (c != '(' && c != ')') {
                sb.append(c);
            }
        }
        return sb.toString();
    }
    
    private void reverse(char[] arr, int left, int right) {
        while (left < right) {
            char temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N^2)` trong trường hợp xấu nhất các ngoặc lồng nhau liên tục, việc lặp đảo ngược tốn nhiều thời gian.
- **Không gian (Space):** `O(N)`.

---

## 23. Different Ways to Add Parentheses (Leetcode 241)
### Đề bài chi tiết
Cho một biểu thức số học gồm số và các toán tử `+`, `-`, `*`. Hãy tính toán và trả về tất cả các kết quả có thể nhận được từ việc nhóm các số và toán tử bằng nhiều cách chèn ngoặc đơn khác nhau.

### Phân tích thuật toán
- Đây là bài toán sử dụng phương pháp Chia để Trị (Divide and Conquer), có thể memoize (Quy hoạch động).
- Duyệt qua chuỗi, nếu gặp một toán tử (operator), ta sẽ cắt chuỗi làm 2 phần: phần trái (left) và phần phải (right).
- Gọi đệ quy để giải quyết hai phần trái và phải này, trả về danh sách các kết quả khả thi của mỗi bên.
- Lấy mỗi kết quả từ phần trái kết hợp với mỗi kết quả từ phần phải thông qua toán tử hiện tại để thu được kết quả mới và đưa vào mảng tổng.
- Nếu chuỗi không chứa toán tử, nó là một số duy nhất, trả về số đó.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    Map<String, List<Integer>> memo = new HashMap<>();
    
    public List<Integer> diffWaysToCompute(String expression) {
        if (memo.containsKey(expression)) {
            return memo.get(expression);
        }
        
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                List<Integer> left = diffWaysToCompute(expression.substring(0, i));
                List<Integer> right = diffWaysToCompute(expression.substring(i + 1));
                
                for (int l : left) {
                    for (int r : right) {
                        if (c == '+') res.add(l + r);
                        else if (c == '-') res.add(l - r);
                        else res.add(l * r);
                    }
                }
            }
        }
        
        if (res.isEmpty()) { // Chỉ có số nguyên
            res.add(Integer.parseInt(expression));
        }
        memo.put(expression, res);
        return res;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** Phụ thuộc số lượng cách gắn ngoặc hợp lệ (tỉ lệ với số Catalan).
- **Không gian (Space):** Phụ thuộc số Catalan do kích thước Call Stack và HashMap.

---

## 24. Maximize Expression Value
### Đề bài chi tiết
Cho một biểu thức toán học dưới dạng chuỗi gồm các số nguyên dương và các toán tử `+`, `-`, `*`. Hãy chèn các dấu ngoặc vào chuỗi một cách hợp lệ để giá trị biểu thức là lớn nhất có thể.

### Phân tích thuật toán
- Bài toán tương tự bài 23 (Different Ways to Add Parentheses) nhưng thay vì liệt kê mọi cách, ta chỉ cần tìm giá trị cực đại.
- Do có sự xuất hiện của phép trừ và phép nhân (có thể làm kết quả âm), giá trị lớn nhất có thể xuất phát từ phép nhân giữa hai giá trị âm nhỏ nhất (Min * Min = Max). Do đó, ta cần lưu trữ cả giá trị nhỏ nhất (min) và giá trị lớn nhất (max) cho mỗi bài toán con.
- Sử dụng Quy hoạch động (Dynamic Programming) trên khoảng (Range DP) thay vì đệ quy thuần túy để tối ưu.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int maximizeExpression(String expression) {
        List<Integer> nums = new ArrayList<>();
        List<Character> ops = new ArrayList<>();
        
        int num = 0;
        for (char c : expression.toCharArray()) {
            if (Character.isDigit(c)) {
                num = num * 10 + (c - '0');
            } else {
                nums.add(num);
                num = 0;
                ops.add(c);
            }
        }
        nums.add(num);
        
        int n = nums.size();
        int[][] maxVal = new int[n][n];
        int[][] minVal = new int[n][n];
        
        for (int i = 0; i < n; i++) {
            maxVal[i][i] = nums.get(i);
            minVal[i][i] = nums.get(i);
        }
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                maxVal[i][j] = Integer.MIN_VALUE;
                minVal[i][j] = Integer.MAX_VALUE;
                
                for (int k = i; k < j; k++) {
                    char op = ops.get(k);
                    int[] a = { maxVal[i][k], minVal[i][k] };
                    int[] b = { maxVal[k+1][j], minVal[k+1][j] };
                    
                    for (int x : a) {
                        for (int y : b) {
                            int val = (op == '+') ? x + y : (op == '-') ? x - y : x * y;
                            maxVal[i][j] = Math.max(maxVal[i][j], val);
                            minVal[i][j] = Math.min(minVal[i][j], val);
                        }
                    }
                }
            }
        }
        
        return maxVal[0][n - 1];
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N^3)` với N là số lượng toán hạng.
- **Không gian (Space):** `O(N^2)` do 2 bảng DP `maxVal` và `minVal`.

---

## 25. Mini Parser (Leetcode 385)
### Đề bài chi tiết
Cho một chuỗi là sự đại diện của một mảng lồng nhau (Nested List). Implement một Mini Parser (trình phân tích cú pháp nhỏ) để chuyển chuỗi (String) đó thành cấu trúc `NestedInteger`.
Ví dụ: `"324"` -> đối tượng NestedInteger lưu số `324`.
Ví dụ 2: `"[123,[456,[789]]]"` -> mảng chứa số 123 và một mảng con lồng tiếp.

### Phân tích thuật toán
- Đây là bài toán đệ quy xử lý các cụm ngoặc vuông `[]` tương tự như đánh giá biểu thức (Expression Evaluation).
- Dùng một Stack chứa các đối tượng `NestedInteger`.
- Khi gặp `[`, ta tạo ra một `NestedInteger` mới đại diện cho danh sách (list), đẩy vào Stack.
- Khi gặp `,`, kết thúc một số nguyên (nếu có), thêm nó vào `NestedInteger` ở đỉnh Stack.
- Khi gặp `]`, kết thúc một số nguyên (nếu có) và đóng danh sách hiện tại. Ta lấy (pop) danh sách đỉnh của Stack ra, thêm nó vào mảng ở đỉnh Stack mới (trừ khi Stack đã rỗng thì đối tượng pop ra chính là root).

### Mã nguồn Java
```java
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * public interface NestedInteger {
 *     // Constructor initializes an empty nested list.
 *     public NestedInteger();
 *
 *     // Constructor initializes a single integer.
 *     public NestedInteger(int value);
 *
 *     // @return true if this NestedInteger holds a single integer, rather than a nested list.
 *     public boolean isInteger();
 *
 *     // @return the single integer that this NestedInteger holds, if it holds a single integer
 *     // Return null if this NestedInteger holds a nested list
 *     public Integer getInteger();
 *
 *     // Set this NestedInteger to hold a single integer.
 *     public void setInteger(int value);
 *
 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
 *     public void add(NestedInteger ni);
 *
 *     // @return the nested list that this NestedInteger holds, if it holds a nested list
 *     // Return empty list if this NestedInteger holds a single integer
 *     public List<NestedInteger> getList();
 * }
 */
import java.util.*;

class Solution {
    public NestedInteger deserialize(String s) {
        if (s.isEmpty()) return null;
        if (s.charAt(0) != '[') return new NestedInteger(Integer.parseInt(s));
        
        Deque<NestedInteger> stack = new ArrayDeque<>();
        int sign = 1, num = 0;
        boolean hasNum = false;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '[') {
                NestedInteger ni = new NestedInteger();
                if (!stack.isEmpty()) {
                    stack.peek().add(ni);
                }
                stack.push(ni);
            } else if (c == '-') {
                sign = -1;
            } else if (Character.isDigit(c)) {
                num = num * 10 + (c - '0');
                hasNum = true;
            } else if (c == ',' || c == ']') {
                if (hasNum) {
                    stack.peek().add(new NestedInteger(sign * num));
                }
                num = 0;
                sign = 1;
                hasNum = false;
                if (c == ']' && stack.size() > 1) {
                    stack.pop();
                }
            }
        }
        return stack.pop();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)`
- **Không gian (Space):** `O(N)` do bộ nhớ Stack tương đương độ sâu của ngoặc.

---

## 26. Ternary Expression Parser (Leetcode 439)
### Đề bài chi tiết
Cho một biểu thức chứa 3 ngôi, bao gồm ký tự, dấu `?` và dấu `:`. Hãy đánh giá (evaluate) biểu thức này và trả về kết quả (luôn là một chuỗi duy nhất của một ký tự). 
Ví dụ: `"T?2:3"` -> `"2"`, `"F?1:T?4:5"` -> `"4"`.
Biểu thức được tính toán từ PHẢI sang TRÁI.

### Phân tích thuật toán
- Vì tính chất liên kết phải (right-associativity) của phép toán 3 ngôi, nếu ta duyệt chuỗi từ Trái qua Phải sẽ rất phức tạp vì không rõ dấu `:` thuộc về dấu `?` nào.
- Cách tốt nhất là duyệt chuỗi từ Phải sang Trái (Right-to-Left).
- Dùng một Stack. Đẩy tất cả các ký tự vào Stack.
- Khi đỉnh của Stack có một ký tự và phần tử tiếp theo là `?` (tức là ta gặp `?` trong khi duyệt từ phải sang trái), ta rút cả bộ ba (condition, true_val, false_val) ra khỏi Stack.
- Xác định giá trị theo điều kiện `condition == 'T'` và đẩy kết quả trở lại Stack.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public String parseTernary(String expression) {
        Deque<Character> stack = new ArrayDeque<>();
        int i = expression.length() - 1;
        
        while (i >= 0) {
            char c = expression.charAt(i);
            if (stack.size() > 0 && stack.peek() == '?') {
                stack.pop(); // loại bỏ '?'
                char first = stack.pop();
                stack.pop(); // loại bỏ ':'
                char second = stack.pop();
                
                if (c == 'T') {
                    stack.push(first);
                } else {
                    stack.push(second);
                }
            } else {
                stack.push(c);
            }
            i--;
        }
        return String.valueOf(stack.pop());
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là độ dài chuỗi biểu thức.
- **Không gian (Space):** `O(N)` cho Stack.

---

## 27. Construct String from Binary Tree (Leetcode 606)
### Đề bài chi tiết
Cho gốc (root) của một cây nhị phân, hãy xây dựng một chuỗi bao gồm các dấu ngoặc đơn và giá trị biểu diễn cây. 
Nguyên tắc: `root_val + (left_tree) + (right_tree)`.
Bạn phải bỏ qua tất cả các cặp ngoặc rỗng không cần thiết, tuy nhiên không được bỏ qua nhóm ngoặc trái nếu có ngoặc phải ở cây con tương ứng (để phân biệt cây con trái/phải rõ ràng).

### Phân tích thuật toán
- Đây là quá trình "Serialization" cây nhị phân (ngược lại quá trình Parse).
- Dùng Đệ quy cho dễ triển khai.
- Nếu `root == null`, trả về `""`.
- Cấu trúc chung: kết quả = `root.val`.
- Nếu có nút bên trái hoặc có nút bên phải, ta luôn luôn cần ngoặc trái (kể cả rỗng, nếu node phải không rỗng).
- Tức là nếu `root.right != null`, ta bắt buộc nối `(` + `left` + `)` và `(` + `right` + `)`.
- Nếu `root.right == null` nhưng `root.left != null`, ta chỉ nối `(` + `left` + `)`.

### Mã nguồn Java
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
class Solution {
    public String tree2str(TreeNode root) {
        if (root == null) return "";
        String result = root.val + "";
        
        String left = tree2str(root.left);
        String right = tree2str(root.right);
        
        if (right.equals("") && left.equals("")) {
            return result;
        } else if (right.equals("")) {
            return result + "(" + left + ")";
        } else {
            return result + "(" + left + ")(" + right + ")";
        }
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N)` với N là số node của cây.
- **Không gian (Space):** `O(H)` cho Call Stack và `O(N)` bộ nhớ cho StringBuilder.

---

## 28. Exclusive Time of Functions (Leetcode 636)
### Đề bài chi tiết
Trên một CPU lõi đơn (single-threaded CPU), chúng ta có các log thực thi hàm (Function Call Stack logs). Mỗi log có định dạng `{function_id}:{"start" | "end"}:{timestamp}`.
Hàm được mô phỏng hoạt động độc lập; một hàm có thể tạm dừng (preempted) để CPU chạy hàm con của nó. Khi hàm con kết thúc, CPU tiếp tục chạy hàm ban đầu.
Tính toán tổng thời gian độc quyền (exclusive time) của từng hàm (Thời gian hàm đó thực thi và CPU không chạy bất kỳ hàm con nào khác).

### Phân tích thuật toán
- Đây là bài toán kinh điển mô phỏng Call Stack của hệ điều hành.
- Dùng một Stack lưu trữ ID của hàm đang thực thi (đang ở trạng thái `start`).
- Biến `prevTime` để ghi nhận mốc thời gian vừa xảy ra sự kiện.
- Khi gặp sự kiện `start`:
  - Nếu Stack không rỗng, hàm trên đỉnh Stack vừa chạy xong được 1 đoạn từ `prevTime` tới `currentTime`. Ta cộng `currentTime - prevTime` vào tổng thời gian hàm đó.
  - Sau đó, đẩy ID của hàm mới vào Stack, cập nhật `prevTime = currentTime`.
- Khi gặp sự kiện `end`:
  - Rút (pop) đỉnh Stack ra (là ID của hàm hiện tại).
  - Hàm này vừa chạy xong 1 đoạn từ `prevTime` tới `currentTime`. Ta cộng `currentTime - prevTime + 1` (vì thời điểm end là cuối đơn vị thời gian) vào tổng.
  - Cập nhật `prevTime = currentTime + 1`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        int prevTime = 0;
        
        for (String log : logs) {
            String[] parts = log.split(":");
            int id = Integer.parseInt(parts[0]);
            String type = parts[1];
            int timestamp = Integer.parseInt(parts[2]);
            
            if (type.equals("start")) {
                if (!stack.isEmpty()) {
                    result[stack.peek()] += timestamp - prevTime;
                }
                stack.push(id);
                prevTime = timestamp;
            } else { // "end"
                result[stack.pop()] += timestamp - prevTime + 1;
                prevTime = timestamp + 1;
            }
        }
        
        return result;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(L)` với L là số lượng log entries.
- **Không gian (Space):** `O(L)` cho không gian Stack.

---

## 29. Number of Atoms (Leetcode 726)
### Đề bài chi tiết
Cho một biểu thức công thức hóa học dưới dạng chuỗi, hãy tính số lượng mỗi loại nguyên tử và trả về dạng chuỗi đã được sắp xếp (ví dụ `H2O`, `Mg(OH)2`).
Biểu thức chứa các chữ cái viết hoa (bắt đầu tên nguyên tử), có thể kèm chữ thường, theo sau bởi một số để đếm số lượng, và có ngoặc đơn lồng nhau `( )`.

### Phân tích thuật toán
- Đây là một bài toán Parse biểu thức giống bài Decode String, nhưng thay vì chỉ có StringBuilder, ta sử dụng một `Map<String, Integer>` tại mỗi cấp độ ngoặc (Scope) để đếm tần suất nguyên tử.
- Ta dùng một `Stack<Map<String, Integer>>`.
- Khởi tạo Stack với một Map rỗng.
- Duyệt qua chuỗi:
  - Nếu gặp `(`, tạo một Map mới và đẩy vào Stack.
  - Nếu gặp `)`, lấy Map ra khỏi Stack (top). Kế tiếp ta đọc một con số `K` đi theo sau (nếu có). Nhân tất cả số lượng (count) trong Map vừa lấy ra với `K`. Cuối cùng gộp nó (cộng dồn) vào Map bên dưới đỉnh mới của Stack.
  - Nếu gặp tên Nguyên tử, phân tích toàn bộ tên nguyên tử, đọc số đếm `K` đi kèm sau đó và thêm nó vào Map ở đỉnh Stack hiện tại.
- Cuối cùng, kết quả là một Map gom tất cả các giá trị. Do yêu cầu in theo thứ tự Alphabet, ta dùng `TreeMap`.

### Mã nguồn Java
```java
import java.util.*;

class Solution {
    public String countOfAtoms(String formula) {
        Deque<Map<String, Integer>> stack = new ArrayDeque<>();
        stack.push(new TreeMap<>());
        int n = formula.length();
        int i = 0;
        
        while (i < n) {
            char c = formula.charAt(i);
            if (c == '(') {
                stack.push(new TreeMap<>());
                i++;
            } else if (c == ')') {
                Map<String, Integer> topMap = stack.pop();
                i++;
                int iStart = i;
                int multiplicity = 1;
                while (i < n && Character.isDigit(formula.charAt(i))) i++;
                if (i > iStart) {
                    multiplicity = Integer.parseInt(formula.substring(iStart, i));
                }
                
                Map<String, Integer> parentMap = stack.peek();
                for (String key : topMap.keySet()) {
                    parentMap.put(key, parentMap.getOrDefault(key, 0) + topMap.get(key) * multiplicity);
                }
            } else {
                int iStart = i++;
                while (i < n && Character.isLowerCase(formula.charAt(i))) i++;
                String name = formula.substring(iStart, i);
                
                iStart = i;
                while (i < n && Character.isDigit(formula.charAt(i))) i++;
                int count = 1;
                if (i > iStart) {
                    count = Integer.parseInt(formula.substring(iStart, i));
                }
                
                Map<String, Integer> map = stack.peek();
                map.put(name, map.getOrDefault(name, 0) + count);
            }
        }
        
        StringBuilder sb = new StringBuilder();
        Map<String, Integer> map = stack.peek();
        for (String key : map.keySet()) {
            sb.append(key);
            int count = map.get(key);
            if (count > 1) {
                sb.append(count);
            }
        }
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** `O(N^2)` trong trường hợp phải merge map vào nhau liên tục ở nhiều cấp độ (hoặc `O(N log N)` nếu xét việc dùng TreeMap có giới hạn key).
- **Không gian (Space):** `O(N)`.

---

## 30. Basic Calculator IV (Leetcode 770)
### Đề bài chi tiết
Cho một biểu thức số học `expression` chứa các biến nguyên không xác định (tương tự Basic Calculator III). Cùng với đó là danh sách tên biến và giá trị đánh giá được, bạn cần thế số vào và đơn giản hóa biểu thức (khai triển đa thức).
Trả về kết quả dưới dạng danh sách chuỗi, đại diện cho đa thức đã sắp xếp giảm dần theo bậc. Ví dụ: `"e - 8 + temperature - cold"`, đánh giá `"e" = 1`, kết quả `["-1*cold", "1*temperature", "-7"]`.

### Phân tích thuật toán
- Các phần tử cơ bản là một lớp `Poly` (Đa thức) với danh sách các hạng tử `Term` (Hệ số, Các biến chuỗi).
- Ta định nghĩa các phép toán `+`, `-`, `*` cho `Poly`.
- Việc Parse (phân tích) biểu thức giống hoàn toàn Basic Calculator III (sử dụng Stack hoặc chuyển qua Postfix sau đó Evaluation).
- Quy tắc sắp xếp trả về: Sắp xếp theo Bậc giảm dần (độ dài danh sách biến), sau đó là chuỗi từ điển của danh sách biến, cuối cùng in theo định dạng chuẩn (bỏ qua hạng tử hệ số 0).

### Mã nguồn Java
```java
import java.util.*;

class Poly {
    Map<List<String>, Integer> terms;

    public Poly() {
        terms = new HashMap<>();
    }

    public Poly update(List<String> vars, int coef) {
        terms.put(vars, terms.getOrDefault(vars, 0) + coef);
        return this;
    }

    public Poly add(Poly p) {
        Poly res = new Poly();
        for (List<String> key : this.terms.keySet()) res.update(key, this.terms.get(key));
        for (List<String> key : p.terms.keySet()) res.update(key, p.terms.get(key));
        return res;
    }

    public Poly sub(Poly p) {
        Poly res = new Poly();
        for (List<String> key : this.terms.keySet()) res.update(key, this.terms.get(key));
        for (List<String> key : p.terms.keySet()) res.update(key, -p.terms.get(key));
        return res;
    }

    public Poly mul(Poly p) {
        Poly res = new Poly();
        for (List<String> k1 : this.terms.keySet()) {
            for (List<String> k2 : p.terms.keySet()) {
                List<String> newVars = new ArrayList<>(k1);
                newVars.addAll(k2);
                Collections.sort(newVars);
                res.update(newVars, this.terms.get(k1) * p.terms.get(k2));
            }
        }
        return res;
    }
}

class Solution {
    public List<String> basicCalculatorIV(String expression, String[] evalvars, int[] evalints) {
        Map<String, Integer> evalMap = new HashMap<>();
        for (int i = 0; i < evalvars.length; i++) {
            evalMap.put(evalvars[i], evalints[i]);
        }

        List<String> tokens = new ArrayList<>();
        int i = 0, n = expression.length();
        while (i < n) {
            if (expression.charAt(i) == ' ') {
                i++;
                continue;
            }
            if (expression.charAt(i) == '(' || expression.charAt(i) == ')' || "+-*".indexOf(expression.charAt(i)) >= 0) {
                tokens.add(expression.substring(i, i + 1));
                i++;
            } else {
                int j = i;
                while (j < n && expression.charAt(j) != ' ' && expression.charAt(j) != '(' && expression.charAt(j) != ')' && "+-*".indexOf(expression.charAt(j)) < 0) {
                    j++;
                }
                tokens.add(expression.substring(i, j));
                i = j;
            }
        }

        List<String> postfix = toPostfix(tokens);
        Deque<Poly> stack = new ArrayDeque<>();
        for (String t : postfix) {
            if (t.equals("+")) stack.push(stack.pop().add(stack.pop()));
            else if (t.equals("-")) {
                Poly b = stack.pop();
                Poly a = stack.pop();
                stack.push(a.sub(b));
            } else if (t.equals("*")) stack.push(stack.pop().mul(stack.pop()));
            else {
                Poly p = new Poly();
                if (t.matches("\\d+")) { 
                    p.update(new ArrayList<>(), Integer.parseInt(t));
                } else if (evalMap.containsKey(t)) {
                    p.update(new ArrayList<>(), evalMap.get(t));
                } else {
                    p.update(Arrays.asList(t), 1);
                }
                stack.push(p);
            }
        }

        Poly resPoly = stack.pop();
        List<List<String>> keys = new ArrayList<>(resPoly.terms.keySet());
        keys.sort((a, b) -> {
            if (a.size() != b.size()) return b.size() - a.size();
            for (int k = 0; k < a.size(); k++) {
                int cmp = a.get(k).compareTo(b.get(k));
                if (cmp != 0) return cmp;
            }
            return 0;
        });

        List<String> ans = new ArrayList<>();
        for (List<String> key : keys) {
            int coef = resPoly.terms.get(key);
            if (coef == 0) continue;
            StringBuilder sb = new StringBuilder();
            sb.append(coef);
            for (String var : key) {
                sb.append("*").append(var);
            }
            ans.add(sb.toString());
        }
        return ans;
    }

    private List<String> toPostfix(List<String> tokens) {
        List<String> out = new ArrayList<>();
        Deque<String> ops = new ArrayDeque<>();
        Map<String, Integer> precedence = new HashMap<>();
        precedence.put("+", 1); precedence.put("-", 1); precedence.put("*", 2);

        for (String t : tokens) {
            if (t.equals("(")) {
                ops.push(t);
            } else if (t.equals(")")) {
                while (!ops.peek().equals("(")) {
                    out.add(ops.pop());
                }
                ops.pop();
            } else if (precedence.containsKey(t)) {
                while (!ops.isEmpty() && precedence.containsKey(ops.peek()) && precedence.get(ops.peek()) >= precedence.get(t)) {
                    out.add(ops.pop());
                }
                ops.push(t);
            } else {
                out.add(t);
            }
        }
        while (!ops.isEmpty()) out.add(ops.pop());
        return out;
    }
}
```
### Độ phức tạp
- **Thời gian (Time):** Độ phức tạp phụ thuộc vào kích thước phân phối đa thức sau khi nhân và khai triển (`O(N * 2^V)` với V là số biến).
- **Không gian (Space):** Chi phí cấp phát Map cho các hạng tử khá cao, tỷ lệ thuận với số lượng phần tử khai triển.
