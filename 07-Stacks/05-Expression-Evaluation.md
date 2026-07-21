# Expression Evaluation (Đánh giá biểu thức)

## 1. Metadata
- **Topic:** Expression Evaluation
- **Category:** Data Structures & Algorithms
- **Tag:** Stacks, Parsing, Abstract Syntax Trees (AST)
- **Difficulty:** Medium / Hard

## 2. Purpose (Mục đích)
- Cung cấp hiểu biết toàn diện về các phương pháp đánh giá và phân tích các biểu thức toán học.
- Nắm vững các cách biểu diễn biểu thức: Infix (Trung tố), Postfix (Hậu tố / Reverse Polish Notation) và Prefix (Tiền tố).
- Áp dụng thành thạo cấu trúc dữ liệu Stack trong việc chuyển đổi và tính toán biểu thức.
- Hiểu được nền tảng của Cây cú pháp trừu tượng (Abstract Syntax Trees - AST) trong trình biên dịch (Compilers).

## 3. Motivation (Động lực)
- Trong toán học, con người sử dụng Infix (ví dụ: `A + B`), nhưng máy tính lại gặp khó khăn với Infix vì phải liên tục kiểm tra độ ưu tiên của toán tử và dấu ngoặc.
- Postfix và Prefix loại bỏ hoàn toàn nhu cầu sử dụng dấu ngoặc và định nghĩa rõ ràng thứ tự tính toán, giúp máy tính (hoặc JVM) đánh giá biểu thức trong một vòng lặp `O(N)` duy nhất bằng Stack.
- Hiểu thuật toán này giúp lập trình viên tiếp cận được thiết kế máy tính bỏ túi, các công cụ phân tích ngôn ngữ tự nhiên, hệ thống rule-engine, và trình biên dịch (Compiler Design).

## 4. Mathematical Foundation (Nền tảng toán học)
- **Tính kết hợp (Associativity):** Quy định hướng đánh giá khi các toán tử có cùng độ ưu tiên.
  - Trái sang phải (Left-to-Right): `+`, `-`, `*`, `/`. Ví dụ: `a - b - c` = `(a - b) - c`.
  - Phải sang trái (Right-to-Left): `^` (Lũy thừa). Ví dụ: `a ^ b ^ c` = `a ^ (b ^ c)`.
- **Độ ưu tiên (Precedence):** Mức độ quan trọng của toán tử: `^` > `*`, `/` > `+`, `-`.
- **Đẳng cấu với Cây (Isomorphism to Trees):** Mỗi biểu thức toán học có thể biểu diễn bằng một Cây nhị phân.
  - Infix = Inorder Traversal.
  - Postfix = Postorder Traversal.
  - Prefix = Preorder Traversal.

## 5. Core Theory (Lý thuyết cốt lõi)
1. **Infix (Trung tố):** Toán tử nằm giữa 2 toán hạng. Ví dụ: `A + B * C`
2. **Postfix (Hậu tố / RPN):** Toán tử nằm sau 2 toán hạng. Ví dụ: `A B C * +`
3. **Prefix (Tiền tố):** Toán tử nằm trước 2 toán hạng. Ví dụ: `+ A * B C`
4. **Shunting Yard Algorithm (Dijkstra):** Dùng để chuyển Infix -> Postfix.
   - Duyệt qua từng Token. Nếu là toán hạng, đưa ra Output.
   - Nếu là toán tử, đẩy vào Stack sau khi pop các toán tử có độ ưu tiên lớn hơn hoặc bằng.
   - Dấu ngoặc ưu tiên cao nhất, được xử lý thành các khối độc lập.
5. **Abstract Syntax Trees (AST):** Một biểu diễn phân cấp của cấu trúc cú pháp của mã nguồn/biểu thức, các node lá là toán hạng, node trong là toán tử.

## 6. Visual Explanation (Giải thích trực quan)

```mermaid
flowchart TD
    A[Infix: 3 + 4 * 2 / ( 1 - 5 ) ^ 2] --> B(Shunting Yard Algorithm)
    B --> C[Postfix: 3 4 2 * 1 5 - 2 ^ / +]
    
    C --> D{Evaluate with Stack}
    D -->|Push 3,4,2| E[Stack: 3, 4, 2]
    D -->|* Operator| F[Pop 4,2 -> 4*2=8 -> Push 8]
    F --> G[Stack: 3, 8]
    
    subgraph AST Representation
    Root[+]
    L1[3]
    R1[/]
    L2[*]
    R2[^]
    L21[4]
    L22[2]
    R21[-]
    R22[2]
    R211[1]
    R212[5]
    Root --> L1
    Root --> R1
    R1 --> L2
    R1 --> R2
    L2 --> L21
    L2 --> L22
    R2 --> R21
    R2 --> R22
    R21 --> R211
    R21 --> R212
    end
```

## 7. Java Implementation (Cài đặt Java)

```java
import java.util.*;

public class ExpressionEvaluator {

    // Helper: Trả về độ ưu tiên của toán tử
    private static int precedence(char op) {
        if (op == '+' || op == '-') return 1;
        if (op == '*' || op == '/') return 2;
        if (op == '^') return 3;
        return -1;
    }

    // Helper: Tính toán 2 toán hạng dựa trên toán tử
    private static int applyOp(char op, int b, int a) {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': 
                if (b == 0) throw new UnsupportedOperationException("Cannot divide by zero");
                return a / b;
            case '^': return (int) Math.pow(a, b);
        }
        return 0;
    }

    // Đánh giá trực tiếp Infix Expression
    public static int evaluateInfix(String expression) {
        char[] tokens = expression.toCharArray();
        Stack<Integer> values = new Stack<>();
        Stack<Character> ops = new Stack<>();

        for (int i = 0; i < tokens.length; i++) {
            if (tokens[i] == ' ') continue;

            if (Character.isDigit(tokens[i])) {
                int val = 0;
                while (i < tokens.length && Character.isDigit(tokens[i])) {
                    val = (val * 10) + (tokens[i] - '0');
                    i++;
                }
                values.push(val);
                i--; // Hiệu chỉnh lại index vì vòng lặp for cũng có i++
            } 
            else if (tokens[i] == '(') {
                ops.push(tokens[i]);
            } 
            else if (tokens[i] == ')') {
                while (!ops.isEmpty() && ops.peek() != '(') {
                    values.push(applyOp(ops.pop(), values.pop(), values.pop()));
                }
                ops.pop(); // Pop '('
            } 
            else if (tokens[i] == '+' || tokens[i] == '-' || tokens[i] == '*' || tokens[i] == '/' || tokens[i] == '^') {
                while (!ops.isEmpty() && precedence(tokens[i]) <= precedence(ops.peek())) {
                    // Xử lý tính kết hợp phải-sang-trái cho lũy thừa
                    if (tokens[i] == '^' && ops.peek() == '^') break;
                    values.push(applyOp(ops.pop(), values.pop(), values.pop()));
                }
                ops.push(tokens[i]);
            }
        }

        while (!ops.isEmpty()) {
            values.push(applyOp(ops.pop(), values.pop(), values.pop()));
        }

        return values.pop();
    }
}
```

## 8. Step-by-Step (Từng bước một)
Giả sử đánh giá chuỗi: `10 + 2 * 6`
1. Đọc `10`: Đẩy vào stack `values` -> `[10]`.
2. Đọc `+`: Đẩy vào stack `ops` -> `[+]`.
3. Đọc `2`: Đẩy vào stack `values` -> `[10, 2]`.
4. Đọc `*`: Độ ưu tiên `*` (2) > `+` (1), đẩy vào `ops` -> `[+, *]`.
5. Đọc `6`: Đẩy vào stack `values` -> `[10, 2, 6]`.
6. Kết thúc chuỗi, pop toàn bộ `ops`:
   - Lấy `*`, lấy `6`, `2` -> Tính `2 * 6 = 12`. Push `12` vào `values` -> `[10, 12]`.
   - Lấy `+`, lấy `12`, `10` -> Tính `10 + 12 = 22`. Push `22`.
7. Trả về giá trị trong stack `values`: `22`.

## 9. Complexity Analysis (Phân tích độ phức tạp)
- **Time Complexity:** `O(N)` với N là độ dài của biểu thức. Mỗi ký tự và toán tử được push và pop vào stack nhiều nhất một lần.
- **Space Complexity:** `O(N)` cho các Stacks (chứa các toán hạng và toán tử). Trong trường hợp xấu nhất biểu thức toàn số hoặc dấu ngoặc lồng nhau, bộ nhớ cần tương đương kích thước chuỗi.

## 10. JVM Analysis (Phân tích JVM)
- **Object Allocation:** Sử dụng `java.util.Stack` sinh ra overhead đồng bộ hóa (synchronization) vì Stack kế thừa Vector. Trong thực tế, nên dùng `ArrayDeque` cho JVM tối ưu hóa bộ nhớ (Escape Analysis) và tránh khóa đồng bộ.
- **Autoboxing:** Sử dụng `Stack<Integer>` và `Stack<Character>` sinh ra boxing/unboxing overhead. Các primitive array (như `int[] stack = new int[N]`) sẽ nhanh hơn và thân thiện với bộ nhớ cache L1/L2.

## 11. OpenJDK Analysis (Phân tích OpenJDK)
- Trình biên dịch Javac của OpenJDK cũng sử dụng nguyên lý tương tự (Recursive Descent Parsing hoặc AST traversal) để phân tích biểu thức trong mã nguồn Java của bạn.
- Các biểu thức không có biến sẽ được tính toán trước tại thời điểm biên dịch (Constant Folding). Ví dụ, `int x = 5 * 3;` sẽ được javac tự động biên dịch thành `int x = 15;`.

## 12. Production Usage (Sử dụng trong thực tế)
- **Rule Engines:** Đánh giá các điều kiện kinh doanh động (ví dụ: `OrderValue > 1000 AND CustomerType == 'VIP'`).
- **Spreadsheet Software:** Excel/Google Sheets phân tích cú pháp các ô công thức bằng các thuật toán parser tương tự.
- **Scientific Calculators:** Máy tính bỏ túi sử dụng trực tiếp RPN (Postfix) qua thuật toán Shunting Yard để tối thiểu hóa bộ nhớ.

## 13. Design Decisions (Quyết định thiết kế)
- **Stack vs Recursive Descent:** Stack dễ cài đặt cho các toán tử đơn giản. Tuy nhiên, Recursive Descent Parser mạnh mẽ hơn nhiều khi cần mở rộng ngữ pháp phức tạp (hàm toán học, biến, lệnh rẽ nhánh).
- **ArrayDeque thay vì Stack:** Java's `Stack` bị coi là lỗi thời. Luôn sử dụng `Deque<Integer> stack = new ArrayDeque<>();`.

## 14. Common Bugs (20 Lỗi phổ biến)
1. `EmptyStackException`: Pop từ Stack mà chưa kiểm tra rỗng do biểu thức không hợp lệ.
2. Xử lý sai unary minus (dấu âm), ví dụ `-5` bị hiểu thành phép trừ thiếu toán hạng.
3. Không xử lý được các số có nhiều chữ số (Multi-digit numbers), đọc sai số `123` thành `1`, `2`, `3`.
4. Sai thứ tự toán hạng ở phép trừ/chia: tính `a - b` thay vì `b - a` khi pop từ stack.
5. Quên không bỏ qua các khoảng trắng, dẫn đến parse lỗi ký tự rỗng.
6. Integer Overflow: Các giá trị trung gian nhân với nhau vượt quá giới hạn 32-bit.
7. Sai độ ưu tiên toán tử: Cho rằng `*` và `/` có độ ưu tiên khác nhau, hoặc `^` ưu tiên thấp.
8. Quên không đẩy kết quả phép tính ngược lại vào Stack.
9. Quên vét cạn các toán tử còn lại trong Stack vào cuối chuỗi Infix.
10. Xử lý ngoặc sai: Quên loại bỏ dấu `(` khỏi Stack các toán tử.
11. Vô tình đẩy cả dấu `)` vào Stack.
12. Divide by Zero (Phép chia cho 0) gây Crash hệ thống nếu không catch exception.
13. Xử lý sai tính kết hợp phải-sang-trái (Right-to-left associativity) của toán tử lũy thừa `^`.
14. Bỏ sót các biến môi trường nếu hệ thống đòi hỏi evaluate theo context.
15. Không hỗ trợ toán tử Unary `+` hoặc `-` dư thừa (như `++5` hay `--5`).
16. Duyệt sai hướng (Left-to-Right thay vì Right-to-Left) khi đánh giá Prefix Expression.
17. Dùng phép chia số nguyên (Integer division) `5 / 2 = 2` thay vì kết quả số thực `2.5`.
18. Không ném lỗi nếu trong Stack dư thừa toán hạng sau khi chạy xong thuật toán.
19. Quên reset StringBuilder hoặc biến dồn số khi đọc token tiếp theo.
20. Mismatch giữa số ngoặc mở và ngoặc đóng dẫn đến sai lệch Stack.

## 15. Edge Cases (30 Trường hợp biên)
1. Biểu thức rỗng `""`.
2. Biểu thức chỉ chứa không gian trắng `"   "`.
3. Chỉ chứa duy nhất một toán hạng `"42"`.
4. Bắt đầu bằng dấu trừ `"-5 + 3"`.
5. Toán hạng bằng `0` trong các phép nhân/chia `"0 * 5"`.
6. Phép chia `10 / 0`.
7. Ngoặc lồng nhau nhiều lớp `"(((5)))"`.
8. Thiếu ngoặc đóng `"(1 + 2"`.
9. Thiếu ngoặc mở `"1 + 2)"`.
10. Các toán tử thừa kế tiếp nhau `"1 + + 2"`.
11. Thiếu toán hạng sau toán tử `"1 +"`.
12. Các số rất lớn `"2147483647 + 1"`.
13. Các số rất âm gây Underflow.
14. Lũy thừa với 0 `"2 ^ 0"`.
15. Ký tự chữ cái không hợp lệ `"2 + a"`.
16. Biểu thức chỉ kết thúc sau ngoặc mở `"("`.
17. Biểu thức chỉ toàn toán tử `"+ - * /"`.
18. Lũy thừa liên tiếp `"2 ^ 3 ^ 2"` (Kỳ vọng kết quả là 512, không phải 64).
19. Toán tử ngay trong ngoặc `"(-5)"`.
20. Đầu vào là số thực dạng chuỗi `"3.14 + 2"`.
21. Nhiều khoảng trắng xen kẽ `"1    +   2"`.
22. Biểu thức quá dài gây StackOverflowError.
23. Cùng một toán tử liên tiếp `"1 - 2 - 3"`.
24. Dấu ngoặc trống `"()"`.
25. Nhiều unary minus liên tục `"---5"`.
26. Ngoặc không bao phủ toàn chuỗi `"(1+2) * (3+4)"`.
27. Đánh giá AST bị khuyết mất nhánh lá.
28. Toán tử modulo `%` với số âm.
29. Cây biểu thức bị lệch một bên dài (LinkedList-like AST).
30. Ký tự tab/newline `\t`, `\n` xuất hiện ngẫu nhiên.

## 16. Optimization (Tối ưu hóa)
- Sử dụng Primitive arrays (`int[]`, `char[]`) đi kèm con trỏ (pointers) thay cho `Stack` object để loại bỏ chi phí boxing.
- Tính toán một lần (Memoization / Constant Folding) trong bước tạo AST, nếu subtree chỉ chứa hằng số.

## 17. Best Practices (Thực hành tốt nhất)
- Validation: Luôn kiểm tra tính hợp lệ của hàm bằng một hàm riêng (check brackets matching, invalid chars).
- Dùng Tokenizer/Scanner chuyên dụng thay vì duyệt `String.toCharArray()` nếu biểu thức phức tạp.
- Sử dụng `Double` thay vì `Integer` làm kiểu trả về vì các phép chia dễ sinh số thập phân.

## 18. Benchmark (Đánh giá hiệu năng)
- Với biểu thức trung bình ~100 tokens, cách dùng Stack truyền thống tốn khoảng `5-10 microseconds` mỗi lần gọi.
- Array-based stacks sẽ giảm thời gian xuống còn `< 1 microsecond`.
- JIT Compiler (C2) có thể tối ưu các vòng lặp unrolling rất tốt khi ta dùng mảng nguyên thủy.

## 19. Unit Testing (Kiểm thử Unit Test)
```java
@Test
public void testExpression() {
    assertEquals(22, ExpressionEvaluator.evaluateInfix("10 + 2 * 6"));
    assertEquals(14, ExpressionEvaluator.evaluateInfix("100 * 2 + 12")); // giả sử test case
    assertEquals(72, ExpressionEvaluator.evaluateInfix("100 * ( 2 + 12 ) / 14 - (2*0)")); 
}
```

## 20. Interview Questions (20 Câu hỏi phỏng vấn)
1. Sự khác biệt giữa Infix, Postfix, Prefix là gì? Ưu điểm của Postfix?
2. Tại sao máy tính chuộng Postfix hơn Infix?
3. Trình bày thuật toán Shunting Yard bằng mã giả.
4. Làm thế nào để Shunting Yard xử lý được các hàm như `sin()`, `cos()`?
5. Nếu phép chia cho 0 xảy ra, hệ thống của bạn nên phản hồi thế nào?
6. Làm thế nào phân biệt một dấu `-` là toán tử trừ hay toán tử âm (unary)?
7. Implement bộ Parser tính giá trị biểu thức không dùng Stack (Recursive Descent).
8. AST là gì? Nêu ứng dụng của nó.
9. Từ AST, làm thế nào in ra Postfix expression?
10. Từ Postfix, thuật toán nào để dựng lên AST tương ứng?
11. Độ phức tạp của Shunting Yard algorithm?
12. Nếu biểu thức sinh ra số lớn quá `Integer.MAX_VALUE`, làm sao xử lý?
13. Bạn thiết kế lớp `Calculator` thế nào để tuân thủ nguyên tắc Mở-Đóng (Open-Closed Principle) khi thêm toán tử mới?
14. Có thể dùng Regex để tokenize chuỗi biểu thức phức tạp không?
15. Khi thiết kế Calculator hỗ trợ biến (ví dụ `x + 2`), bạn cần thêm cấu trúc dữ liệu nào?
16. Hạn chế của cấu trúc lớp `Stack` trong Java, giải pháp thay thế?
17. Phân tích ưu/nhược giữa việc sinh AST và việc đánh giá kết quả trực tiếp trên Stack.
18. Thuật toán xử lý associativity cho toán tử lũy thừa khác gì phép cộng?
19. Giải thích Constant Folding trong Compiler thông qua AST.
20. Mở rộng bộ phân tích này để xử lý phép logic Boolean (AND, OR, NOT).

## 21. Practice Problems Link (Liên kết bài tập)
- [LeetCode 150 - Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
- [LeetCode 224 - Basic Calculator](https://leetcode.com/problems/basic-calculator/)
- [LeetCode 227 - Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)
- [LeetCode 772 - Basic Calculator III](https://leetcode.com/problems/basic-calculator-iii/)

## 22. Pattern Recognition (Nhận diện mẫu)
- **Mẫu "Duyệt token & Stack trì hoãn":** Bất cứ khi nào bạn có các toán tử cần "chờ" (trì hoãn) để giải quyết các phần có độ ưu tiên cao hơn (ngoặc hoặc toán tử bậc cao), hãy nghĩ ngay đến Stack.
- **Mẫu "Máy trạng thái (State Machine)":** Dùng để xác định ký tự hiện tại đang là một số ghép, toán tử hay khoảng trắng.

## 23. Real Case Study (Nghiên cứu tình huống thực tế)
- **Apache JEXL:** Thư viện Java cho phép đánh giá biểu thức động (Dynamic Expression) mạnh mẽ được sử dụng trong các nền tảng cấu hình linh hoạt. Nó xây dựng cây AST tinh vi.
- **Elasticsearch:** Cung cấp tính năng Painless scripting (đánh giá biểu thức toán học và logic trực tiếp trên hàng tỷ bản ghi).

## 24. Summary & Checklist (Tóm tắt & Danh sách kiểm tra)
- [x] Hiểu cấu trúc Infix, Postfix, Prefix.
- [x] Áp dụng Shunting Yard Algorithm chuẩn xác.
- [x] Nắm cách xử lý dấu ngoặc, tính kết hợp và độ ưu tiên toán tử.
- [x] Hiểu mô hình AST.
- [x] Biết cách tối ưu bằng Array thay vì Java Stack truyền thống.
- [x] Xử lý tốt các Corner/Edge Cases về Unary Minus, số lớn và Divide By Zero.
