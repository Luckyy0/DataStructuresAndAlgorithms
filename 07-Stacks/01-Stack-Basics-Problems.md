# Stack Basics - 30 Practice Problems

Tài liệu này cung cấp 30 bài tập cơ bản về Stack. 10 bài đầu tiên được phân tích chi tiết kèm mã nguồn Java đầy đủ. 20 bài sau được tóm tắt ý tưởng.

## 1. Valid Parentheses (LeetCode 20)
**Đề bài chi tiết:** Cho một chuỗi `s` chỉ chứa các ký tự `'('`, `')'`, `'{'`, `'}'`, `'['` và `']'`. Xác định xem chuỗi đầu vào có hợp lệ hay không. Một chuỗi hợp lệ nếu các dấu ngoặc mở phải được đóng bằng dấu ngoặc cùng loại, và đóng đúng thứ tự.
**Phân tích thuật toán:** Sử dụng Stack để lưu các dấu ngoặc mở. Khi gặp dấu ngoặc đóng, kiểm tra xem Stack có rỗng không và phần tử ở đỉnh Stack có phải là dấu ngoặc mở tương ứng không. Nếu đúng, pop phần tử đó ra. Cuối cùng, nếu Stack rỗng thì chuỗi hợp lệ.
**Độ phức tạp:** Thời gian: $O(N)$ (duyệt qua chuỗi 1 lần). Không gian: $O(N)$ (trường hợp xấu nhất chuỗi chỉ toàn dấu ngoặc mở).
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ValidParentheses {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '{' || c == '[') {
                stack.push(c);
            } else {
                if (stack.isEmpty()) return false;
                char top = stack.pop();
                if ((c == ')' && top != '(') ||
                    (c == '}' && top != '{') ||
                    (c == ']' && top != '[')) {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }
}
```

## 2. Min Stack (LeetCode 155)
**Đề bài chi tiết:** Thiết kế một Stack hỗ trợ `push`, `pop`, `top`, và lấy ra phần tử nhỏ nhất `getMin` trong thời gian $O(1)$.
**Phân tích thuật toán:** Sử dụng 2 stacks: `stack` chính để lưu dữ liệu, và `minStack` để lưu giá trị nhỏ nhất tương ứng với mỗi phần tử tại thời điểm nó được đưa vào. Khi push phần tử $x$, push $x$ vào `stack`, và push $min(x, minStack.peek())$ vào `minStack`.
**Độ phức tạp:** Thời gian cho tất cả thao tác: $O(1)$. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class MinStack {
    private Deque<Integer> stack;
    private Deque<Integer> minStack;

    public MinStack() {
        stack = new ArrayDeque<>();
        minStack = new ArrayDeque<>();
    }
    
    public void push(int val) {
        stack.push(val);
        if (minStack.isEmpty() || val <= minStack.peek()) {
            minStack.push(val);
        } else {
            minStack.push(minStack.peek());
        }
    }
    
    public void pop() {
        stack.pop();
        minStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

## 3. Implement Queue using Stacks (LeetCode 232)
**Đề bài chi tiết:** Cài đặt cấu trúc dữ liệu Queue (Hàng đợi) chỉ bằng cách sử dụng 2 Stacks.
**Phân tích thuật toán:** Dùng `stackIn` để lưu các phần tử khi được push vào. Khi cần pop hoặc peek, đổ tất cả phần tử từ `stackIn` sang `stackOut` (nếu `stackOut` rỗng). Lúc này phần tử cũ nhất sẽ nằm trên đỉnh của `stackOut` (đúng nguyên lý FIFO của Queue).
**Độ phức tạp:** Push: $O(1)$. Pop/Peek: $O(1)$ amortized (trung bình). Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class MyQueue {
    private Deque<Integer> stackIn;
    private Deque<Integer> stackOut;

    public MyQueue() {
        stackIn = new ArrayDeque<>();
        stackOut = new ArrayDeque<>();
    }
    
    public void push(int x) {
        stackIn.push(x);
    }
    
    public int pop() {
        shiftStacks();
        return stackOut.pop();
    }
    
    public int peek() {
        shiftStacks();
        return stackOut.peek();
    }
    
    public boolean empty() {
        return stackIn.isEmpty() && stackOut.isEmpty();
    }
    
    private void shiftStacks() {
        if (stackOut.isEmpty()) {
            while (!stackIn.isEmpty()) {
                stackOut.push(stackIn.pop());
            }
        }
    }
}
```

## 4. Backspace String Compare (LeetCode 844)
**Đề bài chi tiết:** Cho hai chuỗi `s` và `t` đại diện cho các lần gõ phím, trong đó ký tự `'#'` đóng vai trò là phím Backspace. Kiểm tra xem hai chuỗi có bằng nhau sau khi xử lý các phím Backspace không.
**Phân tích thuật toán:** Duyệt từng chuỗi, sử dụng Stack để lưu các ký tự bình thường. Khi gặp `'#'`, pop phần tử ở đỉnh Stack (nếu Stack không rỗng) vì ký tự đó bị xóa. So sánh chuỗi tạo thành từ 2 Stacks.
**Độ phức tạp:** Thời gian: $O(N + M)$ với $N, M$ là độ dài chuỗi. Không gian: $O(N + M)$ lưu các ký tự (hoặc có thể dùng Two Pointers để đạt $O(1)$ không gian).
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class BackspaceCompare {
    public boolean backspaceCompare(String s, String t) {
        return build(s).equals(build(t));
    }
    
    private String build(String str) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : str.toCharArray()) {
            if (c != '#') {
                stack.push(c);
            } else if (!stack.isEmpty()) {
                stack.pop();
            }
        }
        return stack.toString();
    }
}
```

## 5. Remove All Adjacent Duplicates In String (LeetCode 1047)
**Đề bài chi tiết:** Cho một chuỗi `s`. Trong mỗi bước, tìm hai ký tự liền kề giống nhau và xóa chúng. Quá trình lặp lại cho đến khi không thể xóa được nữa. Trả về chuỗi kết quả.
**Phân tích thuật toán:** Dùng Stack để duyệt chuỗi. Nếu ký tự hiện tại giống với phần tử trên đỉnh Stack, ta pop phần tử đó ra (hủy bỏ cả hai). Nếu không, push ký tự hiện tại vào Stack. Cuối cùng nối các ký tự trong Stack lại thành chuỗi, nhớ đảo ngược (vì pop ra từ stack sẽ ngược).
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class RemoveDuplicates {
    public String removeDuplicates(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (!stack.isEmpty() && stack.peek() == c) {
                stack.pop();
            } else {
                stack.push(c);
            }
        }
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        return sb.reverse().toString();
    }
}
```

## 6. Make The String Great (LeetCode 1544)
**Đề bài chi tiết:** Một chuỗi được coi là "good" nếu không có hai ký tự kề nhau nào là cùng một chữ cái nhưng một viết hoa, một viết thường (ví dụ: 'a' và 'A'). Hãy xóa các cặp ký tự đó để làm cho chuỗi "good".
**Phân tích thuật toán:** Tương tự bài 5. Khi kiểm tra phần tử trên đỉnh Stack, điều kiện xóa là ký tự hiện tại và đỉnh Stack là cùng một chữ cái nhưng khác case (chênh lệch mã ASCII là 32: `Math.abs(c1 - c2) == 32`).
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class MakeStringGreat {
    public String makeGood(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (!stack.isEmpty() && Math.abs(stack.peek() - c) == 32) {
                stack.pop();
            } else {
                stack.push(c);
            }
        }
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        return sb.reverse().toString();
    }
}
```

## 7. Baseball Game (LeetCode 682)
**Đề bài chi tiết:** Bạn ghi điểm cho một trận bóng chày với các quy tắc đặc biệt. `+`: điểm mới là tổng 2 điểm trước. `D`: điểm mới gấp đôi điểm trước. `C`: hủy bỏ điểm gần nhất. Trả về tổng số điểm cuối cùng.
**Phân tích thuật toán:** Dùng Stack lưu trữ các điểm số hợp lệ. Khi gặp `C` thì pop, `D` thì peek đỉnh stack rồi nhân 2 và push, `+` thì pop 2 phần tử trên cùng tính tổng, sau đó push lại cả 2 phần tử và tổng mới. Cuối cùng cộng dồn tất cả phần tử trong Stack.
**Độ phức tạp:** Thời gian: $O(N)$ với $N$ là số thao tác. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class BaseballGame {
    public int calPoints(String[] operations) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (String op : operations) {
            if (op.equals("+")) {
                int top = stack.pop();
                int newTop = top + stack.peek();
                stack.push(top);
                stack.push(newTop);
            } else if (op.equals("D")) {
                stack.push(2 * stack.peek());
            } else if (op.equals("C")) {
                stack.pop();
            } else {
                stack.push(Integer.parseInt(op));
            }
        }
        int sum = 0;
        for (int score : stack) {
            sum += score;
        }
        return sum;
    }
}
```

## 8. Clear Digits (LeetCode 3174)
**Đề bài chi tiết:** Bạn được cho một chuỗi `s`. Nhiệm vụ của bạn là xóa toàn bộ các chữ số (digits) bằng cách: Xóa chữ số đầu tiên xuất hiện bên trái và ký tự chữ cái gần nó nhất ở bên trái.
**Phân tích thuật toán:** Dùng Stack. Khi gặp ký tự là chữ số, nếu trong Stack có phần tử (sẽ luôn là chữ cái vì ta duyệt từ trái sang), ta pop phần tử đó ra. Nếu là chữ cái, ta push vào Stack. 
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ClearDigits {
    public String clearDigits(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                if (!stack.isEmpty()) {
                    stack.pop();
                }
            } else {
                stack.push(c);
            }
        }
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        return sb.reverse().toString();
    }
}
```

## 9. Crawler Log Folder (LeetCode 1598)
**Đề bài chi tiết:** Một hệ thống log nhận các lệnh chuyển thư mục: `"../"`, `"./"`, hoặc `"x/"` (vào thư mục con x). Tìm số bước tối thiểu để quay về thư mục gốc (Root).
**Phân tích thuật toán:** Ta có thể mô phỏng bằng Stack. `"../"` tương đương với `pop()` (nếu không ở gốc), `"./"` không làm gì cả, và thư mục khác tương đương với `push()`. Kích thước của Stack ở cuối sẽ là số bước cần thiết. (Trong thực tế chỉ cần dùng biến đếm nguyên `depth` để tiết kiệm bộ nhớ).
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(1)$ nếu dùng biến đếm, $O(N)$ nếu dùng Stack.
**Mã nguồn Java:**
```java
public class CrawlerLogFolder {
    public int minOperations(String[] logs) {
        int depth = 0;
        for (String log : logs) {
            if (log.equals("../")) {
                if (depth > 0) depth--;
            } else if (!log.equals("./")) {
                depth++;
            }
        }
        return depth;
    }
}
```

## 10. Remove Outermost Parentheses (LeetCode 1021)
**Đề bài chi tiết:** Cho chuỗi ngoặc hợp lệ hợp thành từ các chuỗi cơ sở, xóa các dấu ngoặc ngoài cùng của từng chuỗi cơ sở và trả về kết quả.
**Phân tích thuật toán:** Khi duyệt chuỗi, mỗi khi bắt đầu một dấu mở ngoặc `'('` mà độ sâu lớn hơn 0, ta giữ lại nó. Mỗi khi gặp dấu đóng ngoặc `')'` mà độ sâu còn lớn hơn 1 (trước khi trừ đi), ta giữ lại nó. Dùng biến nguyên mô phỏng Stack.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ cho chuỗi kết quả.
**Mã nguồn Java:**
```java
public class RemoveOutermostParentheses {
    public String removeOuterParentheses(String s) {
        StringBuilder sb = new StringBuilder();
        int opened = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                if (opened > 0) sb.append(c);
                opened++;
            } else {
                opened--;
                if (opened > 0) sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

## 11. Next Greater Element I (LeetCode 496)
**Đề bài chi tiết:** Cho hai mảng số nguyên phân biệt `nums1` và `nums2`, trong đó `nums1` là tập con của `nums2`. Trả về một mảng chứa "phần tử lớn hơn tiếp theo" (Next Greater Element) cho mỗi phần tử của `nums1` trong `nums2`. Phần tử lớn hơn tiếp theo của `x` là phần tử đầu tiên lớn hơn `x` nằm bên phải `x` trong `nums2`. Nếu không có, trả về -1.
**Phân tích thuật toán:** Sử dụng Monotonic Decreasing Stack (Stack giảm dần) và HashMap. Duyệt mảng `nums2`, với mỗi phần tử, nếu nó lớn hơn đỉnh Stack, nghĩa là nó là "phần tử lớn hơn tiếp theo" của đỉnh Stack. Pop đỉnh Stack ra và lưu vào HashMap (key: đỉnh Stack, value: phần tử hiện tại). Cuối cùng, push phần tử hiện tại vào Stack. Duyệt `nums1` và tra cứu kết quả từ HashMap.
**Độ phức tạp:** Thời gian: $O(N + M)$ với $N, M$ là độ dài hai mảng. Không gian: $O(M)$ cho Stack và HashMap.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.HashMap;
import java.util.Map;

public class NextGreaterElement {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Deque<Integer> stack = new ArrayDeque<>();
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int num : nums2) {
            while (!stack.isEmpty() && stack.peek() < num) {
                map.put(stack.pop(), num);
            }
            stack.push(num);
        }
        
        int[] ans = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            ans[i] = map.getOrDefault(nums1[i], -1);
        }
        return ans;
    }
}
```

## 12. Daily Temperatures (LeetCode 739)
**Đề bài chi tiết:** Cho mảng số nguyên `temperatures` đại diện cho nhiệt độ hàng ngày. Trả về một mảng `answer` trong đó `answer[i]` là số ngày bạn phải chờ sau ngày thứ `i` để có một ngày ấm hơn. Nếu không có ngày nào trong tương lai ấm hơn, để `answer[i] == 0`.
**Phân tích thuật toán:** Sử dụng Monotonic Stack lưu **chỉ số** (index) của các ngày có nhiệt độ chưa tìm được ngày ấm hơn. Khi gặp ngày có nhiệt độ cao hơn nhiệt độ ở đỉnh Stack, ta pop đỉnh Stack ra và tính số ngày chờ là `i - index`. Lặp lại quá trình rồi push chỉ số ngày hiện tại vào Stack.
**Độ phức tạp:** Thời gian: $O(N)$ vì mỗi phần tử được push và pop tối đa một lần. Không gian: $O(N)$ cho Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class DailyTemperatures {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] ans = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int prevIndex = stack.pop();
                ans[prevIndex] = i - prevIndex;
            }
            stack.push(i);
        }
        return ans;
    }
}
```

## 13. Evaluate Reverse Polish Notation (LeetCode 150)
**Đề bài chi tiết:** Tính giá trị của một biểu thức số học dưới dạng Ký pháp Ba Lan Ngược (Reverse Polish Notation - RPN). Các toán tử hợp lệ là `+`, `-`, `*`, và `/`. Mỗi toán hạng có thể là số nguyên hoặc một biểu thức RPN khác.
**Phân tích thuật toán:** Duyệt mảng `tokens`. Nếu gặp một số, push vào Stack. Nếu gặp toán tử, pop 2 số trên cùng của Stack (lưu ý thứ tự: số pop trước là toán hạng 2, số pop sau là toán hạng 1), thực hiện phép tính tương ứng rồi push kết quả trở lại Stack. Cuối cùng, phần tử duy nhất còn lại trong Stack là kết quả.
**Độ phức tạp:** Thời gian: $O(N)$ với $N$ là số lượng tokens. Không gian: $O(N)$ cho Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class EvaluateRPN {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (String token : tokens) {
            if (token.equals("+")) {
                stack.push(stack.pop() + stack.pop());
            } else if (token.equals("-")) {
                int b = stack.pop();
                int a = stack.pop();
                stack.push(a - b);
            } else if (token.equals("*")) {
                stack.push(stack.pop() * stack.pop());
            } else if (token.equals("/")) {
                int b = stack.pop();
                int a = stack.pop();
                stack.push(a / b);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }
}
```

## 14. Simplify Path (LeetCode 71)
**Đề bài chi tiết:** Cho một chuỗi đường dẫn tuyệt đối (bắt đầu bằng `/`) đến một file hoặc thư mục trong hệ thống file kiểu Unix, hãy chuyển nó về đường dẫn chuẩn (canonical path) rút gọn nhất. Đường dẫn chuẩn không chứa dấu `/` thừa, không có `.` hay `..`.
**Phân tích thuật toán:** Tách chuỗi bởi dấu `/`. Dùng Stack để lưu các thư mục hợp lệ. Khi duyệt các phần tử đã tách: nếu gặp rỗng hoặc `.` thì bỏ qua, gặp `..` thì pop Stack nếu Stack không rỗng (quay lại thư mục cha), ngược lại push phần tử (tên thư mục) vào Stack. Cuối cùng nối các phần tử trong Stack bằng `/`.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ chứa mảng tách và Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class SimplifyPath {
    public String simplifyPath(String path) {
        Deque<String> stack = new ArrayDeque<>();
        String[] components = path.split("/");
        
        for (String dir : components) {
            if (dir.equals("..")) {
                if (!stack.isEmpty()) stack.pop();
            } else if (!dir.isEmpty() && !dir.equals(".")) {
                stack.push(dir);
            }
        }
        
        StringBuilder res = new StringBuilder();
        if (stack.isEmpty()) return "/";
        
        while (!stack.isEmpty()) {
            res.insert(0, "/" + stack.pop());
        }
        
        return res.toString();
    }
}
```

## 15. Design a Stack With Increment Operation (LeetCode 1381)
**Đề bài chi tiết:** Thiết kế một cấu trúc dữ liệu Stack tùy chỉnh hỗ trợ các thao tác: `push(x)`, `pop()`, và `increment(k, val)` (tăng $k$ phần tử nằm dưới cùng của Stack thêm một lượng `val`).
**Phân tích thuật toán:** Dùng mảng để làm Stack, kết hợp mảng phụ `inc` để lưu giá trị cần tăng thêm (kỹ thuật lazy increment). Khi gọi `increment(k, val)`, ta chỉ cộng `val` vào vị trí nhỏ nhất giữa $k-1$ và đỉnh stack hiện tại trong mảng `inc`. Khi gọi `pop()`, trả về `stack[top] + inc[top]`, sau đó cộng dồn `inc[top]` xuống phần tử bên dưới `inc[top-1]` và đặt `inc[top] = 0`.
**Độ phức tạp:** Push, Pop, Increment đều $O(1)$. Không gian: $O(N)$ với $N$ là maxSize.
**Mã nguồn Java:**
```java
public class CustomStack {
    private int[] stack;
    private int[] inc;
    private int top;

    public CustomStack(int maxSize) {
        stack = new int[maxSize];
        inc = new int[maxSize];
        top = -1;
    }
    
    public void push(int x) {
        if (top < stack.length - 1) {
            top++;
            stack[top] = x;
        }
    }
    
    public int pop() {
        if (top == -1) return -1;
        int res = stack[top] + inc[top];
        if (top > 0) {
            inc[top - 1] += inc[top];
        }
        inc[top] = 0;
        top--;
        return res;
    }
    
    public void increment(int k, int val) {
        int limit = Math.min(k - 1, top);
        if (limit >= 0) {
            inc[limit] += val;
        }
    }
}
```

## 16. Minimum Add to Make Parentheses Valid (LeetCode 921)
**Đề bài chi tiết:** Cho chuỗi ngoặc `s`. Tìm số lượng dấu ngoặc ít nhất cần thêm vào bất kỳ vị trí nào để chuỗi trở thành hợp lệ.
**Phân tích thuật toán:** Không cần dùng Stack thực sự, chỉ cần 2 biến `open` (số ngoặc mở chưa có ngoặc đóng) và `need` (số ngoặc đóng bị thiếu khi gặp đóng ngoặc nhưng không có mở ngoặc). Khi gặp `'('`, tăng `open`. Gặp `')'`, nếu `open > 0` thì giảm `open`, ngược lại tăng `need`. Tổng số cần thêm là `open + need`.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(1)$.
**Mã nguồn Java:**
```java
public class MinAddToMakeValid {
    public int minAddToMakeValid(String s) {
        int open = 0;
        int need = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                open++;
            } else {
                if (open > 0) open--;
                else need++;
            }
        }
        return open + need;
    }
}
```

## 17. Maximum Nesting Depth of the Parentheses (LeetCode 1614)
**Đề bài chi tiết:** Tìm độ sâu lồng nhau tối đa của các dấu ngoặc hợp lệ trong một chuỗi ký tự (bao gồm cả các ký tự thường).
**Phân tích thuật toán:** Vì chuỗi được đảm bảo hợp lệ (VPS - Valid Parentheses String), ta chỉ cần đếm số ngoặc mở đang mở (tương đương với kích thước Stack nếu dùng Stack). Gặp `'('` tăng biến đếm và cập nhật max, gặp `')'` giảm biến đếm.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(1)$.
**Mã nguồn Java:**
```java
public class MaxDepthParentheses {
    public int maxDepth(String s) {
        int currentDepth = 0;
        int maxDepth = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                currentDepth++;
                maxDepth = Math.max(maxDepth, currentDepth);
            } else if (c == ')') {
                currentDepth--;
            }
        }
        return maxDepth;
    }
}
```

## 18. Decode String (LeetCode 394)
**Đề bài chi tiết:** Cho một chuỗi được mã hóa, yêu cầu giải mã nó. Cú pháp mã hóa là `k[encoded_string]`, nghĩa là chuỗi `encoded_string` bên trong dấu ngoặc vuông được lặp lại chính xác `k` lần.
**Phân tích thuật toán:** Dùng 2 Stacks: `countStack` lưu số lần lặp, `stringStack` lưu chuỗi tích lũy trước đó. Khi duyệt chuỗi, nếu gặp số, tạo số đó. Nếu gặp `'['`, push số lượng và chuỗi tạm thời vào Stacks rồi reset. Gặp chữ cái, thêm vào chuỗi tạm. Gặp `']'`, pop chuỗi tạm trước đó, pop số lần lặp, rồi nối chuỗi tạm hiện tại với chuỗi pop ra.
**Độ phức tạp:** Thời gian: $O(N)$ với $N$ là độ dài chuỗi kết quả (sau khi giải mã). Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class DecodeString {
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

## 19. Online Stock Span (LeetCode 901)
**Đề bài chi tiết:** Thiết kế lớp `StockSpanner` tính toán "span" của giá cổ phiếu hôm nay. Span là số ngày liên tiếp tính từ hôm nay trở về trước có giá nhỏ hơn hoặc bằng giá hôm nay.
**Phân tích thuật toán:** Dùng Monotonic Stack lưu các mảng chứa cặp `[price, span]`. Khi nhận `price` mới, nếu Stack không rỗng và giá ở đỉnh nhỏ hơn hoặc bằng `price`, pop đỉnh ra và cộng dồn span của nó vào span của ngày hiện tại. Cuối cùng push `[price, tổng span]` vào Stack.
**Độ phức tạp:** Thao tác `next`: $O(1)$ amortized (trung bình, vì mỗi phần tử push và pop tối đa 1 lần). Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class StockSpanner {
    private Deque<int[]> stack;

    public StockSpanner() {
        stack = new ArrayDeque<>();
    }
    
    public int next(int price) {
        int span = 1;
        while (!stack.isEmpty() && stack.peek()[0] <= price) {
            span += stack.pop()[1];
        }
        stack.push(new int[]{price, span});
        return span;
    }
}
```

## 20. Exclusive Time of Functions (LeetCode 636)
**Đề bài chi tiết:** Trên hệ thống đơn luồng, các hàm được thực thi, có thể gọi nhau, tạm dừng rồi tiếp tục. Dựa trên danh sách các log dạng `id:start:time` hoặc `id:end:time`, tính tổng thời gian độc quyền (exclusive time) thực thi của mỗi hàm (không tính thời gian hàm khác chạy chen vào).
**Phân tích thuật toán:** Dùng Stack lưu ID của hàm đang thực thi. Biến `prevTime` lưu thời điểm trước đó. Khi gặp log `start`, cộng dồn thời gian từ `prevTime` đến hiện tại cho hàm ở đỉnh Stack, rồi push ID mới vào, cập nhật `prevTime = time`. Gặp `end`, hàm kết thúc tại thời điểm `time`, cộng thời gian `time - prevTime + 1` cho hàm trên đỉnh, pop nó ra, và cập nhật `prevTime = time + 1`.
**Độ phức tạp:** Thời gian: $O(L)$ với $L$ là số lượng log. Không gian: $O(L)$ (tối đa cho Stack) và $O(N)$ cho mảng kết quả.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.List;

public class ExclusiveTimeOfFunctions {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] res = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        int prevTime = 0;
        
        for (String log : logs) {
            String[] parts = log.split(":");
            int id = Integer.parseInt(parts[0]);
            String type = parts[1];
            int time = Integer.parseInt(parts[2]);
            
            if (type.equals("start")) {
                if (!stack.isEmpty()) {
                    res[stack.peek()] += time - prevTime;
                }
                stack.push(id);
                prevTime = time;
            } else {
                res[stack.pop()] += time - prevTime + 1;
                prevTime = time + 1;
            }
        }
        return res;
    }
}
```

## 21. Asteroid Collision (LeetCode 735)
**Đề bài chi tiết:** Cho mảng các số nguyên `asteroids` đại diện cho các tiểu hành tinh trên một hàng. Giá trị tuyệt đối đại diện cho kích thước, dấu dương là di chuyển sang phải, dấu âm là di chuyển sang trái. Các tiểu hành tinh di chuyển cùng tốc độ. Tìm trạng thái của các tiểu hành tinh sau tất cả các vụ va chạm. Hai tiểu hành tinh va chạm thì tiểu hành tinh nhỏ hơn sẽ phát nổ, nếu kích thước bằng nhau cả hai đều phát nổ.
**Phân tích thuật toán:** Dùng Stack. Khi gặp một thiên thạch: nếu nó bay sang phải (dương), push vào Stack. Nếu bay sang trái (âm), kiểm tra trong khi Stack có thiên thạch bay sang phải và kích thước nhỏ hơn thiên thạch hiện tại thì pop chúng ra. Nếu kích thước bằng nhau thì pop đỉnh và thiên thạch hiện tại cũng vỡ. Nếu Stack rỗng hoặc đỉnh bay sang trái thì push thiên thạch hiện tại vào Stack.
**Độ phức tạp:** Thời gian: $O(N)$ vì mỗi tiểu hành tinh được push và pop tối đa một lần. Không gian: $O(N)$ cho Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class AsteroidCollision {
    public int[] asteroidCollision(int[] asteroids) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (int a : asteroids) {
            boolean exploded = false;
            while (!stack.isEmpty() && a < 0 && stack.peek() > 0) {
                if (stack.peek() < -a) {
                    stack.pop();
                    continue;
                } else if (stack.peek() == -a) {
                    stack.pop();
                }
                exploded = true;
                break;
            }
            if (!exploded) {
                stack.push(a);
            }
        }
        int[] res = new int[stack.size()];
        for (int i = res.length - 1; i >= 0; i--) {
            res[i] = stack.pop();
        }
        return res;
    }
}
```

## 22. Remove K Digits (LeetCode 402)
**Đề bài chi tiết:** Cho số nguyên không âm `num` dưới dạng chuỗi và số nguyên `k`. Hãy xóa `k` chữ số khỏi `num` sao cho số thu được là nhỏ nhất có thể.
**Phân tích thuật toán:** Dùng Monotonic Stack (Stack tăng dần). Duyệt qua từng chữ số, nếu chữ số hiện tại nhỏ hơn đỉnh Stack và `k > 0`, ta pop đỉnh Stack ra, giảm `k` (chọn xóa chữ số lớn hơn để làm số nhỏ đi). Sau đó push chữ số hiện tại vào Stack. Nếu duyệt xong mà `k > 0` thì pop tiếp `k` chữ số ở cuối. Cuối cùng nối lại chuỗi và loại bỏ các số 0 ở đầu.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ cho Stack và chuỗi kết quả.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class RemoveKDigits {
    public String removeKdigits(String num, int k) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : num.toCharArray()) {
            while (!stack.isEmpty() && k > 0 && stack.peek() > c) {
                stack.pop();
                k--;
            }
            stack.push(c);
        }
        while (k > 0 && !stack.isEmpty()) {
            stack.pop();
            k--;
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        sb.reverse();
        
        while (sb.length() > 1 && sb.charAt(0) == '0') {
            sb.deleteCharAt(0);
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```

## 23. Validate Stack Sequences (LeetCode 946)
**Đề bài chi tiết:** Cho hai mảng số nguyên các giá trị phân biệt `pushed` và `popped`. Trả về `true` nếu đó có thể là kết quả của một chuỗi các thao tác push và pop trên một stack trống, và `false` nếu ngược lại.
**Phân tích thuật toán:** Dùng một Stack mô phỏng lại quá trình. Duyệt từng phần tử của mảng `pushed`, đẩy phần tử đó vào Stack. Sau mỗi lần đẩy, dùng vòng lặp while kiểm tra xem đỉnh Stack có bằng với phần tử hiện tại đang chờ pop ở mảng `popped` hay không. Nếu bằng, pop nó ra và tăng con trỏ của `popped`. Cuối cùng, nếu Stack rỗng thì chuỗi thao tác là hợp lệ.
**Độ phức tạp:** Thời gian: $O(N)$ với $N$ là số lượng phần tử. Không gian: $O(N)$ cho Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ValidateStackSequences {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> stack = new ArrayDeque<>();
        int j = 0;
        for (int x : pushed) {
            stack.push(x);
            while (!stack.isEmpty() && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        return stack.isEmpty();
    }
}
```

## 24. Score of Parentheses (LeetCode 856)
**Đề bài chi tiết:** Cho chuỗi ngoặc hợp lệ `s`, tính điểm của chuỗi dựa trên các quy tắc: `()` có điểm là 1; `AB` có điểm là `A + B`; `(A)` có điểm là `2 * A`.
**Phân tích thuật toán:** Dùng Stack. Khi gặp `'('`, ta chưa biết điểm số bên trong là bao nhiêu nên lưu tạm giá trị 0. Khi gặp `')'`, pop giá trị trên đỉnh Stack ra. Nếu là 0, nghĩa là bên trong không có gì (tương ứng `()`), ta có 1 điểm. Nếu là khác 0, nghĩa là bên trong có điểm `A`, ta nhân đôi điểm thành `2 * A`. Sau đó cộng điểm vừa tính được vào phần tử đang nằm trên đỉnh Stack mới (thuộc tầng lồng bên ngoài). 
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ chiều sâu tối đa của Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ScoreOfParentheses {
    public int scoreOfParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0); // Điểm cơ sở
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(0);
            } else {
                int innerScore = stack.pop();
                int currentScore = Math.max(2 * innerScore, 1);
                stack.push(stack.pop() + currentScore);
            }
        }
        return stack.pop();
    }
}
```

## 25. Reverse Substrings Between Each Pair of Parentheses (LeetCode 1190)
**Đề bài chi tiết:** Bạn được cho một chuỗi `s` chứa chữ cái tiếng Anh thường và dấu ngoặc đơn. Đảo ngược các chuỗi nằm trong mỗi cặp dấu ngoặc `()`, bắt đầu từ cặp dấu ngoặc trong cùng. Kết quả không được chứa dấu ngoặc.
**Phân tích thuật toán:** Dùng Stack để lưu vị trí các dấu ngoặc mở `'('`. Khi gặp `'('`, đẩy vị trí của nó vào Stack. Khi gặp `')'`, pop vị trí mở ngoặc ra và đảo ngược đoạn chuỗi giữa 2 vị trí đó (bằng thuật toán 2 con trỏ). Cuối cùng lọc bỏ tất cả dấu ngoặc đơn để lấy chuỗi kết quả.
**Độ phức tạp:** Thời gian: $O(N^2)$ trong trường hợp lồng nhau sâu. Không gian: $O(N)$.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ReverseParentheses {
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

## 26. Build an Array With Stack Operations (LeetCode 1441)
**Đề bài chi tiết:** Bạn được cho một mảng `target` chứa các số nguyên tăng dần ngặt nghèo (strictly increasing) và số nguyên `n`. Ban đầu bạn có một stack trống. Lặp qua chuỗi số từ $1$ đến $n$, trả về một mảng chứa các thao tác "Push" và "Pop" để xây dựng mảng `target`.
**Phân tích thuật toán:** Khởi tạo biến `current = 1`. Duyệt qua từng số trong mảng `target`. Với mỗi số `t` trong `target`, nếu `current < t`, nghĩa là các số từ `current` đến `t - 1` không có trong mảng, ta cần mô phỏng "Push" rồi "Pop" cho chúng. Khi `current == t`, ta chỉ việc thêm một thao tác "Push". Sau đó tăng `current` lên 1. Dừng khi đã xét hết mảng `target`.
**Độ phức tạp:** Thời gian: $O(M)$ với $M$ là phần tử lớn nhất trong `target`. Không gian: $O(1)$ (không tính kết quả trả về).
**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

public class BuildArray {
    public List<String> buildArray(int[] target, int n) {
        List<String> res = new ArrayList<>();
        int current = 1;
        for (int t : target) {
            while (current < t) {
                res.add("Push");
                res.add("Pop");
                current++;
            }
            res.add("Push");
            current++;
        }
        return res;
    }
}
```

## 27. Number of Students Unable to Eat Lunch (LeetCode 1700)
**Đề bài chi tiết:** Trong một trường học, học sinh có 2 sở thích ăn uống (0 hoặc 1) đứng xếp hàng (Queue). Sandwich cũng có 2 loại (0 hoặc 1) xếp thành một chồng (Stack). Nếu học sinh đầu hàng thích sandwich trên cùng, họ sẽ lấy nó và rời khỏi hàng. Nếu không, họ sẽ di chuyển xuống cuối hàng chờ. Quá trình dừng lại khi không ai ở trong hàng muốn cái bánh ở đầu chồng. Trả về số học sinh không thể ăn trưa.
**Phân tích thuật toán:** Không cần phải thực sự mô phỏng Queue và Stack. Ta chỉ cần đếm tổng số lượng học sinh thích loại 0 và thích loại 1. Duyệt qua chồng `sandwiches`, đối với mỗi chiếc bánh, nếu có học sinh nào thích loại đó (số lượng $> 0$) thì giảm số lượng đi. Nếu không còn học sinh nào thích loại bánh đó, quá trình sẽ dừng lại (vì các học sinh còn lại không ai lấy bánh và sẽ luân chuyển mãi mãi). Trả về số học sinh còn lại.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(1)$.
**Mã nguồn Java:**
```java
public class StudentsUnableToEat {
    public int countStudents(int[] students, int[] sandwiches) {
        int[] counts = new int[2];
        for (int student : students) {
            counts[student]++;
        }
        for (int sandwich : sandwiches) {
            if (counts[sandwich] > 0) {
                counts[sandwich]--;
            } else {
                break;
            }
        }
        return counts[0] + counts[1];
    }
}
```

## 28. Check If Word Is Valid After Substitutions (LeetCode 1003)
**Đề bài chi tiết:** Một chuỗi hợp lệ nếu nó bắt đầu là rỗng, sau đó tại mỗi bước bạn có thể chèn chuỗi `"abc"` vào bất kỳ vị trí nào. Cho chuỗi `s`, kiểm tra xem nó có hợp lệ hay không.
**Phân tích thuật toán:** Dùng Stack tương tự bài Parentheses. Duyệt qua chuỗi `s`. Khi gặp ký tự `'a'` hoặc `'b'`, ta push vào Stack. Khi gặp `'c'`, ta phải pop 2 ký tự trên cùng ra và kiểm tra xem có lần lượt là `'b'` và `'a'` hay không (vì `'c'` nằm cuối chuỗi `"abc"`). Nếu không, chuỗi không hợp lệ. Cuối cùng, nếu Stack rỗng, chuỗi hợp lệ.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ chứa tối đa $N$ ký tự trong Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class IsValidWord {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char ch : s.toCharArray()) {
            if (ch == 'c') {
                if (stack.isEmpty() || stack.pop() != 'b') return false;
                if (stack.isEmpty() || stack.pop() != 'a') return false;
            } else {
                stack.push(ch);
            }
        }
        return stack.isEmpty();
    }
}
```

## 29. Removing Stars From a String (LeetCode 2390)
**Đề bài chi tiết:** Bạn được cho một chuỗi `s` chứa các dấu `*`. Trong một thao tác, bạn có thể chọn một dấu `*`, xóa dấu `*` đó và xóa ký tự không phải dấu `*` gần nhất nằm bên trái của nó. Trả về chuỗi còn lại sau khi tất cả dấu sao bị xóa.
**Phân tích thuật toán:** Sử dụng Stack (hoặc StringBuilder như một Stack). Duyệt chuỗi từ trái qua phải, nếu gặp ký tự thường, push vào Stack. Nếu gặp dấu `*`, pop phần tử trên cùng ra (vì đó là ký tự gần nhất nằm bên trái). Cuối cùng các ký tự trong Stack chính là kết quả.
**Độ phức tạp:** Thời gian: $O(N)$. Không gian: $O(N)$ chứa chuỗi kết quả.
**Mã nguồn Java:**
```java
public class RemoveStars {
    public String removeStars(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c == '*') {
                if (sb.length() > 0) {
                    sb.deleteCharAt(sb.length() - 1);
                }
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

## 30. Final Prices With a Special Discount in a Shop (LeetCode 1475)
**Đề bài chi tiết:** Bạn được cho một mảng `prices` nơi `prices[i]` là giá mặt hàng thứ `i` tại cửa hàng. Bạn sẽ nhận được giảm giá bằng với `prices[j]` trong đó `j` là chỉ số nhỏ nhất sao cho `j > i` và `prices[j] <= prices[i]`. Trả về mảng giá cuối cùng sau khi giảm.
**Phân tích thuật toán:** Đây là bài toán Next Smaller Element cơ bản. Dùng Monotonic Increasing Stack (Stack tăng dần). Duyệt mảng từ trái sang phải, với mỗi phần tử `prices[i]`, kiểm tra các chỉ số `j` trong Stack. Nếu `prices[i] <= prices[j]`, ta cập nhật giảm giá cho phần tử tại `j` là `prices[j] = prices[j] - prices[i]` rồi pop `j` ra khỏi Stack. Sau đó push `i` vào Stack. Các phần tử còn trong Stack khi kết thúc thì không có discount.
**Độ phức tạp:** Thời gian: $O(N)$ vì mỗi phần tử được push và pop vào Stack đúng 1 lần. Không gian: $O(N)$ cho Monotonic Stack.
**Mã nguồn Java:**
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class FinalPrices {
    public int[] finalPrices(int[] prices) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < prices.length; i++) {
            while (!stack.isEmpty() && prices[stack.peek()] >= prices[i]) {
                int index = stack.pop();
                prices[index] -= prices[i];
            }
            stack.push(i);
        }
        return prices; // Sửa trực tiếp trên mảng gốc để tiết kiệm bộ nhớ
    }
}
```
