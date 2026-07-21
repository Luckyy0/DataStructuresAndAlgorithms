# Bài tập: Linked-List-Based Stack

## 1. Implement Stack using Linked List
**Đề bài chi tiết**: Cài đặt một cấu trúc dữ liệu Stack (Ngăn xếp) bằng cách sử dụng Singly Linked List. Phải hỗ trợ các thao tác `push`, `pop`, `peek`, `isEmpty`.
**Phân tích thuật toán**: Thao tác chèn và xóa ở đầu của danh sách liên kết đảm bảo độ phức tạp thời gian luôn là O(1). Node mới sẽ trỏ tới Node hiện tại của Top.
**Độ phức tạp**: Thời gian: O(1) cho mọi thao tác. Không gian: O(N) cho N phần tử.
**Mã nguồn Java**:
```java
class MyStack<T> {
    private static class Node<T> {
        T data;
        Node<T> next;
        Node(T data) { this.data = data; }
    }
    private Node<T> top;

    public void push(T val) {
        Node<T> newNode = new Node<>(val);
        newNode.next = top;
        top = newNode;
    }

    public T pop() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        T res = top.data;
        top = top.next;
        return res;
    }

    public T peek() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        return top.data;
    }

    public boolean isEmpty() {
        return top == null;
    }
}
```

## 2. Reverse a Stack using recursion (without extra space)
**Đề bài chi tiết**: Đảo ngược các phần tử trong một ngăn xếp (Stack) bằng cách sử dụng đệ quy thay vì sử dụng vòng lặp hay cấu trúc dữ liệu mảng/ngăn xếp tạm thời khác.
**Phân tích thuật toán**: Lấy phần tử đỉnh ra và giữ nó trong Call Stack của JVM (đệ quy). Khi ngăn xếp rỗng, bắt đầu nạp lại các phần tử nhưng đặt chúng vào *đáy* của ngăn xếp thông qua một hàm phụ `insertAtBottom`.
**Độ phức tạp**: Thời gian: O(N^2) (Do mỗi lần chèn lại ở đáy tốn O(N)). Không gian: O(N) đệ quy ẩn.
**Mã nguồn Java**:
```java
import java.util.Stack;

class ReverseStack {
    public static void reverse(Stack<Integer> stack) {
        if (!stack.isEmpty()) {
            int top = stack.pop();
            reverse(stack);
            insertAtBottom(stack, top);
        }
    }

    private static void insertAtBottom(Stack<Integer> stack, int val) {
        if (stack.isEmpty()) {
            stack.push(val);
        } else {
            int temp = stack.pop();
            insertAtBottom(stack, val);
            stack.push(temp);
        }
    }
}
```

## 3. Sort a Stack
**Đề bài chi tiết**: Sắp xếp một ngăn xếp sao cho phần tử nhỏ nhất nằm trên đỉnh. Có thể dùng đệ quy hoặc ngăn xếp phụ.
**Phân tích thuật toán**: Tương tự bài toán Reverse, ta lấy từng phần tử ra, sắp xếp phần còn lại đệ quy, sau đó chèn lại phần tử vào đúng vị trí theo thuật toán đệ quy được gọi là `sortedInsert`.
**Độ phức tạp**: Thời gian: O(N^2). Không gian: O(N) đệ quy.
**Mã nguồn Java**:
```java
import java.util.Stack;

class SortStack {
    public static void sort(Stack<Integer> stack) {
        if (!stack.isEmpty()) {
            int x = stack.pop();
            sort(stack);
            sortedInsert(stack, x);
        }
    }

    private static void sortedInsert(Stack<Integer> stack, int x) {
        if (stack.isEmpty() || x > stack.peek()) {
            stack.push(x);
            return;
        }
        int temp = stack.pop();
        sortedInsert(stack, x);
        stack.push(temp);
    }
}
```

## 4. Delete middle element of a stack
**Đề bài chi tiết**: Xóa phần tử nằm ở giữa ngăn xếp. Nếu kích thước là N, phần tử giữa nằm ở vị trí `floor(N/2)` tính từ đỉnh xuống. Chỉ được dùng đệ quy.
**Phân tích thuật toán**: Dùng một hàm đệ quy truyền vào tổng kích thước và biến đếm. Lấy phần tử ra, tăng đếm, nếu đếm bằng nửa kích thước thì không chèn lại. Các phần tử còn lại được push lại bình thường.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N) đệ quy.
**Mã nguồn Java**:
```java
import java.util.Stack;

class DeleteMiddle {
    public static void deleteMid(Stack<Integer> stack, int sizeOfStack) {
        solve(stack, 0, sizeOfStack);
    }

    private static void solve(Stack<Integer> stack, int count, int sizeOfStack) {
        if (count == sizeOfStack / 2) {
            stack.pop();
            return;
        }
        int temp = stack.pop();
        solve(stack, count + 1, sizeOfStack);
        stack.push(temp);
    }
}
```

## 5. Insert Element at Bottom of Stack
**Đề bài chi tiết**: Thêm một phần tử X vào vị trí cuối cùng (đáy) của ngăn xếp mà không làm hỏng thứ tự của các phần tử hiện tại.
**Phân tích thuật toán**: Dùng đệ quy bóc từng phần tử trên cùng ra. Khi rỗng thì `push(X)`. Sau đó `push` lại lần lượt các phần tử đã bóc từ call stack.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class InsertBottom {
    public static Stack<Integer> insertAtBottom(Stack<Integer> stack, int x) {
        if (stack.isEmpty()) {
            stack.push(x);
        } else {
            int top = stack.pop();
            insertAtBottom(stack, x);
            stack.push(top);
        }
        return stack;
    }
}
```

## 6. Reverse First K elements of Queue (using stack)
**Đề bài chi tiết**: Cho một hàng đợi (Queue), đảo ngược K phần tử đầu tiên của hàng đợi, để nguyên thứ tự các phần tử còn lại.
**Phân tích thuật toán**: Dequeue K phần tử từ Queue đẩy vào Stack. Sau đó Pop tất cả Stack đẩy lại vào Queue (Lúc này K phần tử đã bị đảo). Để khôi phục đúng thứ tự, Dequeue và Enqueue lại N-K phần tử còn lại.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(K) do dùng Stack dung lượng K.
**Mã nguồn Java**:
```java
import java.util.Queue;
import java.util.Stack;

class ReverseKQueue {
    public Queue<Integer> modifyQueue(Queue<Integer> q, int k) {
        if (q == null || k > q.size() || k <= 0) return q;
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < k; i++) {
            stack.push(q.poll());
        }
        while (!stack.isEmpty()) {
            q.add(stack.pop());
        }
        int remaining = q.size() - k;
        for (int i = 0; i < remaining; i++) {
            q.add(q.poll());
        }
        return q;
    }
}
```

## 7. Check if stack elements are pairwise consecutive
**Đề bài chi tiết**: Cho một ngăn xếp các số nguyên, kiểm tra xem tất cả các cặp số liên tiếp nhau từ đỉnh xuống đáy (cặp 1-2, 3-4...) có mang giá trị chênh lệch chính xác là 1 hay -1 không. Nếu tổng số phần tử lẻ, bỏ qua phần tử cuối cùng ở đáy. Khôi phục lại ngăn xếp như ban đầu.
**Phân tích thuật toán**: Ta cần một ngăn xếp trung gian (hoặc queue) để lưu vết và bảo tồn thứ tự. Pop 2 phần tử, kiểm tra |a-b| == 1. Lưu lại vào Auxiliary Stack. Sau đó trả lại cấu trúc gốc.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class PairwiseConsecutive {
    public static boolean checkConsecutive(Stack<Integer> s) {
        Stack<Integer> aux = new Stack<>();
        boolean result = true;
        
        while (s.size() > 1) {
            int a = s.pop();
            int b = s.pop();
            if (Math.abs(a - b) != 1) {
                result = false;
            }
            aux.push(a);
            aux.push(b);
        }
        while (!aux.isEmpty()) {
            s.push(aux.pop());
        }
        return result;
    }
}
```

## 8. Interleave the first half of the queue with second half
**Đề bài chi tiết**: Đan xen nửa đầu hàng đợi vào nửa sau của chính hàng đợi đó chỉ bằng cách sử dụng cấu trúc Stack. Kích thước hàng đợi luôn chẵn.
**Phân tích thuật toán**:
- Bước 1: Dequeue nửa đầu vào Stack.
- Bước 2: Pop Stack vào lại Queue (Nửa đầu sẽ bị đảo).
- Bước 3: Dequeue nửa đầu (hiện tại) vào Stack lần 2. (Lúc này các số nằm trong stack khôi phục thứ tự).
- Bước 4: Đan xen 1 phần tử từ Stack, 1 phần tử Queue.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N/2).
**Mã nguồn Java**:
```java
import java.util.Queue;
import java.util.Stack;

class InterleaveQueue {
    public static void interleaveQueue(Queue<Integer> q) {
        if (q.size() % 2 != 0) return;
        Stack<Integer> stack = new Stack<>();
        int half = q.size() / 2;
        
        for (int i = 0; i < half; i++) stack.push(q.poll());
        while (!stack.isEmpty()) q.add(stack.pop());
        for (int i = 0; i < half; i++) q.add(q.poll());
        for (int i = 0; i < half; i++) stack.push(q.poll());
        
        while (!stack.isEmpty()) {
            q.add(stack.pop());
            q.add(q.poll());
        }
    }
}
```

## 9. Find the pattern in Stack (132 Pattern)
**Đề bài chi tiết**: Cho mảng các số. Xác định xem có tồn tại chuỗi 3 số `i < j < k` sao cho `nums[i] < nums[k] < nums[j]` hay không. Có thể coi mảng này đang duyệt ngược và dùng cấu trúc Stack.
**Phân tích thuật toán**: Duyệt từ phải sang trái. Duy trì một Stack chứa các ứng viên cho vị trí `j` và một biến `third` lưu giá trị lớn nhất hợp lệ cho vị trí `k`. Khi gặp `nums[i] < third`, ta tìm thấy 132 pattern.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class Pattern132 {
    public boolean find132pattern(int[] nums) {
        if (nums == null || nums.length < 3) return false;
        Stack<Integer> stack = new Stack<>();
        int third = Integer.MIN_VALUE;

        for (int i = nums.length - 1; i >= 0; i--) {
            if (nums[i] < third) return true;
            while (!stack.isEmpty() && nums[i] > stack.peek()) {
                third = stack.pop();
            }
            stack.push(nums[i]);
        }
        return false;
    }
}
```

## 10. Remove K Digits
**Đề bài chi tiết**: Cho một số nguyên không âm đại diện bằng chuỗi `num` và số nguyên `k`. Loại bỏ `k` chữ số khỏi `num` để giá trị còn lại là nhỏ nhất có thể. Cài đặt bằng Stack (Singly Linked List đằng sau là một tùy chọn).
**Phân tích thuật toán**: Dùng thuật toán Monotonic Stack. Khi gặp một số nhỏ hơn phần tử trên đỉnh của ngăn xếp và còn giới hạn `k`, ta liên tục `pop` ngăn xếp.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class RemoveKDigits {
    public String removeKdigits(String num, int k) {
        Stack<Character> stack = new Stack<>();
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
        for (char c : stack) sb.append(c);
        while (sb.length() > 0 && sb.charAt(0) == '0') {
            sb.deleteCharAt(0);
        }
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```

## 11. Next Greater Element I
**Đề bài chi tiết**: Cho hai mảng số nguyên phân biệt `nums1` và `nums2`, trong đó `nums1` là tập con của `nums2`. Với mỗi phần tử trong `nums1`, tìm phần tử lớn hơn tiếp theo của nó nằm bên phải trong `nums2`. Nếu không có, trả về -1.
**Phân tích thuật toán**: Sử dụng thuật toán Monotonic Stack giảm dần để tìm phần tử lớn hơn tiếp theo cho tất cả các phần tử trong `nums2`. Dùng một HashMap để ánh xạ phần tử tới kết quả của nó, sau đó duyệt qua `nums1` để lấy kết quả từ HashMap.
**Độ phức tạp**: Thời gian: O(N) với N là độ dài của `nums2`. Không gian: O(N) để lưu trữ Stack và HashMap.
**Mã nguồn Java**:
```java
import java.util.HashMap;
import java.util.Stack;

class NextGreaterElementI {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        HashMap<Integer, Integer> map = new HashMap<>();
        Stack<Integer> stack = new Stack<>();
        
        for (int num : nums2) {
            while (!stack.isEmpty() && stack.peek() < num) {
                map.put(stack.pop(), num);
            }
            stack.push(num);
        }
        
        int[] result = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            result[i] = map.getOrDefault(nums1[i], -1);
        }
        return result;
    }
}
```

## 12. Next Greater Element II
**Đề bài chi tiết**: Cho một mảng số nguyên dạng xoay vòng (circular array), tìm phần tử lớn hơn tiếp theo cho mỗi phần tử trong mảng. Nếu không tìm thấy, trả về -1. Mảng xoay vòng có nghĩa là phần tử tiếp theo của phần tử cuối cùng là phần tử đầu tiên.
**Phân tích thuật toán**: Dùng Monotonic Stack lưu trữ **chỉ số** (index) của các phần tử. Do mảng xoay vòng, ta duyệt mảng 2 lần (tới 2*N). Khi duyệt, dùng phép toán modulo `i % N` để lấy giá trị thực tế. Phần tử nào trong Stack nhỏ hơn giá trị hiện tại sẽ được pop ra và cập nhật kết quả.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Arrays;
import java.util.Stack;

class NextGreaterElementII {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Arrays.fill(res, -1);
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < 2 * n; i++) {
            int num = nums[i % n];
            while (!stack.isEmpty() && nums[stack.peek()] < num) {
                res[stack.pop()] = num;
            }
            if (i < n) stack.push(i);
        }
        return res;
    }
}
```

## 13. Daily Temperatures
**Đề bài chi tiết**: Cho mảng `temperatures` biểu diễn nhiệt độ từng ngày, hãy trả về mảng `answer` sao cho `answer[i]` là số ngày bạn phải chờ sau ngày thứ `i` để có một ngày ấm hơn. Nếu không có ngày nào như vậy, giữ giá trị 0.
**Phân tích thuật toán**: Dùng Monotonic Stack lưu **chỉ số** (index) của các ngày mà chưa tìm được ngày ấm hơn. Mỗi khi duyệt qua một ngày mới có nhiệt độ cao hơn ngày ở đỉnh Stack, ta pop ra và tính số ngày chênh lệch bằng phép trừ chỉ số.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class DailyTemperatures {
    public int[] dailyTemperatures(int[] temperatures) {
        int[] answer = new int[temperatures.length];
        Stack<Integer> stack = new Stack<>(); // lưu index
        
        for (int i = 0; i < temperatures.length; i++) {
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int prevDay = stack.pop();
                answer[prevDay] = i - prevDay;
            }
            stack.push(i);
        }
        return answer;
    }
}
```

## 14. Valid Parentheses
**Đề bài chi tiết**: Cho một chuỗi `s` chỉ chứa các ký tự `'('`, `')'`, `'{'`, `'}'`, `'['` và `']'`, xác định xem chuỗi đầu vào có hợp lệ hay không. Chuỗi hợp lệ nếu ngoặc mở được đóng bởi cùng loại ngoặc và theo đúng thứ tự.
**Phân tích thuật toán**: Dùng Stack để lưu các ký tự mở ngoặc. Khi gặp ký tự đóng ngoặc, kiểm tra xem Stack có rỗng không và đỉnh Stack có phải là ký tự mở ngoặc tương ứng không. Cuối cùng, Stack phải rỗng mới hợp lệ.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class ValidParentheses {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
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

## 15. Longest Valid Parentheses
**Đề bài chi tiết**: Cho một chuỗi chỉ chứa các ký tự `'('` và `')'`, tìm độ dài của chuỗi con liên tiếp chứa các dấu ngoặc hợp lệ dài nhất.
**Phân tích thuật toán**: Sử dụng Stack lưu **chỉ số** của các dấu ngoặc. Khởi tạo Stack bằng cách push `-1` làm điểm gốc. Khi gặp `'('`, push chỉ số của nó vào. Khi gặp `')'`, pop một phần tử khỏi Stack. Nếu Stack rỗng sau khi pop, push chỉ số hiện tại vào làm điểm gốc mới. Nếu không rỗng, cập nhật độ dài lớn nhất bằng `i - stack.peek()`.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class LongestValidParentheses {
    public int longestValidParentheses(String s) {
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int maxLen = 0;
        
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

## 16. Evaluate Reverse Polish Notation
**Đề bài chi tiết**: Tính giá trị của một biểu thức số học được viết dưới dạng Ký pháp Ba Lan ngược (Reverse Polish Notation - RPN). Các toán tử hợp lệ là `+`, `-`, `*`, `/`.
**Phân tích thuật toán**: Duyệt qua từng chuỗi con (token). Nếu là số, đẩy vào Stack. Nếu là toán tử, rút 2 số ở trên cùng của Stack ra, thực hiện phép tính tương ứng và đẩy kết quả trở lại Stack. Cuối cùng, kết quả là phần tử duy nhất còn lại trong Stack.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class EvaluateRPN {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for (String token : tokens) {
            switch (token) {
                case "+":
                    stack.push(stack.pop() + stack.pop());
                    break;
                case "-":
                    int a = stack.pop();
                    int b = stack.pop();
                    stack.push(b - a);
                    break;
                case "*":
                    stack.push(stack.pop() * stack.pop());
                    break;
                case "/":
                    int c = stack.pop();
                    int d = stack.pop();
                    stack.push(d / c);
                    break;
                default:
                    stack.push(Integer.parseInt(token));
                    break;
            }
        }
        return stack.pop();
    }
}
```

## 17. Basic Calculator
**Đề bài chi tiết**: Cài đặt một máy tính cơ bản để đánh giá một biểu thức chuỗi đơn giản. Biểu thức có thể chứa các khoảng trắng, dấu cộng `+`, trừ `-`, ngoặc mở `(`, ngoặc đóng `)` và số nguyên không âm.
**Phân tích thuật toán**: Dùng Stack để lưu trạng thái của kết quả tích lũy `result` và dấu `sign` hiện tại trước khi vào trong ngoặc. Khi gặp dấu `(`, đưa kết quả hiện tại và dấu ngay trước ngoặc vào Stack, sau đó reset để tính bên trong. Khi gặp dấu `)`, rút dấu và giá trị cũ ra để gộp chung với giá trị trong ngoặc vừa tính xong.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class BasicCalculator {
    public int calculate(String s) {
        Stack<Integer> stack = new Stack<>();
        int result = 0;
        int number = 0;
        int sign = 1;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                number = number * 10 + (c - '0');
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
                result = 0;
                sign = 1;
            } else if (c == ')') {
                result += sign * number;
                number = 0;
                result *= stack.pop(); // sign
                result += stack.pop(); // old result
            }
        }
        if (number != 0) result += sign * number;
        return result;
    }
}
```

## 18. Asteroid Collision
**Đề bài chi tiết**: Cho một mảng `asteroids` đại diện cho các thiên thạch trên cùng một hàng. Giá trị tuyệt đối là kích thước, dấu dương (di chuyển sang phải) và dấu âm (di chuyển sang trái). Tìm trạng thái cuối cùng của các thiên thạch sau mọi va chạm. Thiên thạch nhỏ nổ, bằng nhau thì cả hai nổ, cùng chiều không va chạm.
**Phân tích thuật toán**: Dùng Stack để mô phỏng. Nếu thiên thạch đi sang phải (> 0), luôn đưa vào Stack. Nếu đi sang trái (< 0), kiểm tra đỉnh Stack (nếu đang đi sang phải). Xử lý vòng lặp va chạm, phá hủy thiên thạch nhỏ hơn.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class AsteroidCollision {
    public int[] asteroidCollision(int[] asteroids) {
        Stack<Integer> stack = new Stack<>();
        for (int ast : asteroids) {
            boolean exploded = false;
            while (!stack.isEmpty() && ast < 0 && stack.peek() > 0) {
                if (stack.peek() < -ast) {
                    stack.pop();
                    continue;
                } else if (stack.peek() == -ast) {
                    stack.pop();
                }
                exploded = true;
                break;
            }
            if (!exploded) {
                stack.push(ast);
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

## 19. Min Stack
**Đề bài chi tiết**: Thiết kế một Stack hỗ trợ các thao tác `push`, `pop`, `top`, và lấy phần tử nhỏ nhất `getMin` đều trong thời gian không đổi O(1).
**Phân tích thuật toán**: Tạo một node riêng trong Linked List có thêm trường dữ liệu `min` (giá trị nhỏ nhất từ đáy đến node hiện tại). Mỗi khi push một phần tử mới, tính `min` hiện tại bằng cách so sánh với `min` của top node cũ.
**Độ phức tạp**: Thời gian: O(1) cho mọi thao tác. Không gian: O(N).
**Mã nguồn Java**:
```java
class MinStack {
    private class Node {
        int val;
        int min;
        Node next;
        
        private Node(int val, int min, Node next) {
            this.val = val;
            this.min = min;
            this.next = next;
        }
    }
    
    private Node head;

    public MinStack() {
        head = null;
    }
    
    public void push(int val) {
        if (head == null) {
            head = new Node(val, val, null);
        } else {
            head = new Node(val, Math.min(val, head.min), head);
        }
    }
    
    public void pop() {
        head = head.next;
    }
    
    public int top() {
        return head.val;
    }
    
    public int getMin() {
        return head.min;
    }
}
```

## 20. Implement Queue using Stacks
**Đề bài chi tiết**: Cài đặt một hàng đợi (Queue - FIFO) chỉ bằng cách sử dụng hai ngăn xếp (Stack - LIFO). Hỗ trợ các thao tác `push`, `pop`, `peek`, `empty`.
**Phân tích thuật toán**: Dùng `stack1` để nhận phần tử khi `push`. Khi cần lấy phần tử ra (`pop` hoặc `peek`), ta chuyển toàn bộ phần tử từ `stack1` sang `stack2` (đảo ngược thứ tự) nếu `stack2` đang rỗng, sau đó pop từ `stack2`.
**Độ phức tạp**: Thời gian: Amortized O(1) cho mỗi thao tác. Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class MyQueue {
    private Stack<Integer> s1;
    private Stack<Integer> s2;

    public MyQueue() {
        s1 = new Stack<>();
        s2 = new Stack<>();
    }
    
    public void push(int x) {
        s1.push(x);
    }
    
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) s2.push(s1.pop());
        }
        return s2.pop();
    }
    
    public int peek() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) s2.push(s1.pop());
        }
        return s2.peek();
    }
    
    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}
```

## 21. Decode String
**Đề bài chi tiết**: Cho một chuỗi đã mã hóa, hãy trả về chuỗi đã được giải mã. Quy tắc mã hóa là: `k[encoded_string]`, trong đó `encoded_string` bên trong dấu ngoặc vuông sẽ được lặp lại chính xác `k` lần. Bạn có thể cho rằng chuỗi đầu vào luôn hợp lệ, không có khoảng trắng thừa và ngoặc vuông luôn đúng định dạng.
**Phân tích thuật toán**: Sử dụng hai Stack: một Stack để lưu trữ số `k` (số lần lặp) và một Stack để lưu trữ các chuỗi kết quả tạm thời. Khi duyệt qua chuỗi:
- Nếu là chữ số, tính toán giá trị `k`.
- Nếu là `[`, đẩy `k` hiện tại và chuỗi kết quả hiện tại vào các Stack, sau đó reset chúng.
- Nếu là `]`, lấy chuỗi và `k` từ Stack ra, nhân chuỗi hiện tại lên `k` lần và nối vào chuỗi lấy từ Stack.
- Nếu là chữ cái, thêm vào chuỗi hiện tại.
**Độ phức tạp**: Thời gian: O(N) trong đó N là độ dài chuỗi kết quả. Không gian: O(N) để lưu trữ hai Stack.
**Mã nguồn Java**:
```java
import java.util.Stack;

class DecodeString {
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

## 22. Simplify Path
**Đề bài chi tiết**: Cho một chuỗi `path` là một đường dẫn tuyệt đối (bắt đầu bằng dấu gạch chéo `/`) đến một tập tin hoặc thư mục trong hệ thống tệp kiểu Unix, hãy chuyển đổi nó thành đường dẫn chuẩn (canonical path) được đơn giản hóa.
Trong Unix, dấu chấm `.` nghĩa là thư mục hiện tại, dấu hai chấm `..` nghĩa là thư mục cha. Đường dẫn chuẩn phải bắt đầu bằng `/`, các thư mục phân tách bằng đúng một dấu `/`, và không chứa `.` hay `..`.
**Phân tích thuật toán**: Tách chuỗi theo dấu `/`. Dùng Stack để lưu các thư mục. Nếu gặp thư mục bình thường (không trống, khác `.` và `..`), đẩy vào Stack. Nếu gặp `..`, lấy ra khỏi Stack (nếu Stack không rỗng). Cuối cùng, nối các phần tử trong Stack lại với nhau bằng dấu `/`.
**Độ phức tạp**: Thời gian: O(N) với N là độ dài chuỗi `path`. Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class SimplifyPath {
    public String simplifyPath(String path) {
        Stack<String> stack = new Stack<>();
        String[] components = path.split("/");
        
        for (String directory : components) {
            if (directory.equals(".") || directory.isEmpty()) {
                continue;
            } else if (directory.equals("..")) {
                if (!stack.isEmpty()) {
                    stack.pop();
                }
            } else {
                stack.push(directory);
            }
        }
        
        StringBuilder result = new StringBuilder();
        for (String dir : stack) {
            result.append("/");
            result.append(dir);
        }
        
        return result.length() > 0 ? result.toString() : "/";
    }
}
```

## 23. Trapping Rain Water
**Đề bài chi tiết**: Cho `n` số nguyên không âm đại diện cho một bản đồ độ cao (elevation map) với chiều rộng của mỗi cột là 1, hãy tính lượng nước có thể đọng lại sau cơn mưa.
**Phân tích thuật toán**: Sử dụng thuật toán Monotonic Stack giảm dần. Lưu **chỉ số** của các cột vào Stack. Khi gặp cột hiện tại cao hơn cột ở đỉnh Stack, nghĩa là ta có một "vùng trũng" có thể chứa nước. Pop phần tử đỉnh ra (đáy của vùng trũng), nếu Stack rỗng thì thoát. Nếu không, chiều cao nước sẽ được tính dựa trên min của cột hiện tại và đỉnh Stack mới, nhân với khoảng cách.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N) cho Stack.
**Mã nguồn Java**:
```java
import java.util.Stack;

class TrappingRainWater {
    public int trap(int[] height) {
        Stack<Integer> stack = new Stack<>();
        int water = 0;
        
        for (int i = 0; i < height.length; i++) {
            while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                int top = stack.pop();
                if (stack.isEmpty()) {
                    break;
                }
                int distance = i - stack.peek() - 1;
                int boundedHeight = Math.min(height[i], height[stack.peek()]) - height[top];
                water += distance * boundedHeight;
            }
            stack.push(i);
        }
        
        return water;
    }
}
```

## 24. Largest Rectangle in Histogram
**Đề bài chi tiết**: Cho mảng các số nguyên `heights` đại diện cho biểu đồ cột (histogram) có độ rộng mỗi cột là 1. Tìm diện tích hình chữ nhật lớn nhất có thể tạo ra trong biểu đồ.
**Phân tích thuật toán**: Dùng Monotonic Stack tăng dần lưu trữ **chỉ số**. Nếu gặp cột thấp hơn cột trên đỉnh Stack, tiến hành pop và tính diện tích. Cột bị pop chính là chiều cao hình chữ nhật, còn chiều rộng bằng khoảng cách từ chỉ số hiện tại tới chỉ số mới trên đỉnh Stack trừ đi 1. Sau khi duyệt hết, pop phần còn lại của Stack tương tự.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class LargestRectangle {
    public int largestRectangleArea(int[] heights) {
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;
        int n = heights.length;
        
        for (int i = 0; i <= n; i++) {
            int currentHeight = (i == n) ? 0 : heights[i];
            while (!stack.isEmpty() && currentHeight < heights[stack.peek()]) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        
        return maxArea;
    }
}
```

## 25. Maximal Rectangle
**Đề bài chi tiết**: Cho ma trận nhị phân 2 chiều, các ô chỉ chứa `0` và `1`. Tìm hình chữ nhật có diện tích lớn nhất chứa toàn số `1` và trả về diện tích của nó.
**Phân tích thuật toán**: Bài toán được mở rộng từ **Largest Rectangle in Histogram**. Ta duyệt ma trận theo từng hàng, coi mỗi hàng làm mặt đất của một histogram. Nếu ô là `1`, chiều cao cột tăng thêm 1; nếu ô là `0`, chiều cao cột trở về 0. Sau đó áp dụng hàm giải bài toán Histogram để tìm max area trên từng hàng và lấy kết quả lớn nhất tổng thể.
**Độ phức tạp**: Thời gian: O(R * C). Không gian: O(C) cho mảng heights và Stack. (R là số hàng, C là số cột).
**Mã nguồn Java**:
```java
import java.util.Stack;

class MaximalRectangle {
    public int maximalRectangle(char[][] matrix) {
        if (matrix.length == 0) return 0;
        int maxArea = 0;
        int cols = matrix[0].length;
        int[] heights = new int[cols];
        
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == '1') {
                    heights[j] += 1;
                } else {
                    heights[j] = 0;
                }
            }
            maxArea = Math.max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }
    
    private int largestRectangleArea(int[] heights) {
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;
        int n = heights.length;
        for (int i = 0; i <= n; i++) {
            int h = (i == n) ? 0 : heights[i];
            while (!stack.isEmpty() && h < heights[stack.peek()]) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        return maxArea;
    }
}
```

## 26. Validate Stack Sequences
**Đề bài chi tiết**: Cho hai mảng số nguyên `pushed` và `popped` chứa các giá trị phân biệt, hãy trả về `true` nếu đó có thể là kết quả của một chuỗi thao tác push và pop hợp lệ trên một ngăn xếp rỗng ban đầu, hoặc trả về `false` nếu không.
**Phân tích thuật toán**: Khởi tạo một Stack giả lập. Duyệt qua mảng `pushed`, mỗi bước đẩy phần tử vào Stack. Trong khi đỉnh Stack bằng với phần tử hiện tại của `popped` (được chỉ định bởi một biến con trỏ), ta tiến hành pop liên tục và tăng con trỏ. Cuối cùng, nếu Stack rỗng thì chuỗi hợp lệ.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N) cho Stack mô phỏng.
**Mã nguồn Java**:
```java
import java.util.Stack;

class ValidateStackSequences {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int j = 0;
        
        for (int x : pushed) {
            stack.push(x);
            while (!stack.isEmpty() && j < popped.length && stack.peek() == popped[j]) {
                stack.pop();
                j++;
            }
        }
        
        return j == popped.length;
    }
}
```

## 27. Remove All Adjacent Duplicates In String
**Đề bài chi tiết**: Cho chuỗi `s` chỉ chứa các ký tự chữ thường, một lượt xóa trùng lặp kề nhau bao gồm việc chọn hai chữ cái giống nhau và kề nhau, sau đó loại bỏ chúng khỏi chuỗi. Lặp lại quá trình trên cho đến khi không thể xóa được nữa. Trả về chuỗi cuối cùng.
**Phân tích thuật toán**: Dùng Stack. Duyệt từng ký tự, nếu Stack không rỗng và ký tự hiện tại giống ký tự trên đỉnh Stack, ta pop khỏi Stack. Ngược lại, push ký tự hiện tại vào Stack. Cuối cùng, nối các phần tử của Stack lại thành chuỗi kết quả. (Có thể dùng StringBuilder như một Stack để tối ưu).
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N) cho Stack.
**Mã nguồn Java**:
```java
class RemoveDuplicates {
    public String removeDuplicates(String s) {
        StringBuilder sb = new StringBuilder(); // Mô phỏng Stack
        for (char c : s.toCharArray()) {
            int size = sb.length();
            if (size > 0 && sb.charAt(size - 1) == c) {
                sb.deleteCharAt(size - 1); // pop
            } else {
                sb.append(c); // push
            }
        }
        return sb.toString();
    }
}
```

## 28. Online Stock Span
**Đề bài chi tiết**: Thiết kế một hệ thống thu thập giá trị cổ phiếu hàng ngày và trả về "stock span" (nhịp tăng giá cổ phiếu) của ngày hôm nay. Stock span của giá cổ phiếu ngày hôm nay được định nghĩa là số lượng ngày liên tiếp tối đa (bắt đầu từ ngày hôm nay và đếm lùi về trước) mà giá cổ phiếu nhỏ hơn hoặc bằng giá của ngày hôm nay.
**Phân tích thuật toán**: Dùng Monotonic Stack giảm dần. Mỗi phần tử trong Stack là một mảng 2 phần tử `[price, span]`. Khi một giá trị mới đến, trong khi giá trị đó lớn hơn hoặc bằng giá trên đỉnh Stack, ta pop Stack ra và cộng dồn span của phần tử bị pop vào span của giá trị hiện tại. Cuối cùng, push vào Stack và trả về span hiện tại.
**Độ phức tạp**: Thời gian: O(1) amortized cho mỗi lệnh gọi `next`. Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class StockSpanner {
    private Stack<int[]> stack;

    public StockSpanner() {
        stack = new Stack<>();
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

## 29. Minimum Add to Make Parentheses Valid
**Đề bài chi tiết**: Cho một chuỗi `s` chỉ chứa `'('` và `')'`. Bạn có thể chèn một dấu ngoặc bất kỳ vào vị trí bất kỳ của chuỗi. Hãy tìm số thao tác chèn ít nhất để làm cho chuỗi dấu ngoặc trở nên hợp lệ.
**Phân tích thuật toán**: Ta có thể dùng 2 biến đếm để tối ưu thay vì Stack. `left_needed` theo dõi số lượng `'('` cần thêm, và `right_needed` theo dõi số lượng `')'` cần thêm khi có `'('` chưa được đóng. Gặp `'('`, tăng `right_needed`. Gặp `')'`, nếu `right_needed > 0` thì giảm nó (có `'('` để khớp), nếu không thì phải thêm `'('`, tăng `left_needed`. Kết quả là tổng `left_needed + right_needed`.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(1).
**Mã nguồn Java**:
```java
class MinAddToMakeValid {
    public int minAddToMakeValid(String s) {
        int left_needed = 0;
        int right_needed = 0;
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                right_needed++;
            } else if (c == ')') {
                if (right_needed > 0) {
                    right_needed--;
                } else {
                    left_needed++;
                }
            }
        }
        
        return left_needed + right_needed;
    }
}
```

## 30. Score of Parentheses
**Đề bài chi tiết**: Cho chuỗi dấu ngoặc hợp lệ `s`, tính điểm của chuỗi dựa trên quy tắc:
- `()` tính 1 điểm.
- `AB` có điểm là `A + B`.
- `(A)` có điểm là `2 * A`, với `A` là một chuỗi dấu ngoặc hợp lệ.
**Phân tích thuật toán**: Dùng Stack lưu trữ điểm số cục bộ tại mỗi độ sâu lồng nhau của ngoặc. Khởi tạo bằng `push(0)`. Khi gặp `'('`, push 0 vào Stack. Khi gặp `')'`, ta pop 2 điểm số trên cùng: điểm bên trong ngoặc là `v = pop()`, điểm bên ngoài hiện tại là `w = pop()`. Cập nhật điểm ngoài thành `w + Math.max(2 * v, 1)` và push lại vào Stack. 
Cuối cùng, Stack chỉ còn lại một phần tử chính là tổng điểm.
**Độ phức tạp**: Thời gian: O(N). Không gian: O(N).
**Mã nguồn Java**:
```java
import java.util.Stack;

class ScoreOfParentheses {
    public int scoreOfParentheses(String s) {
        Stack<Integer> stack = new Stack<>();
        stack.push(0); // Điểm của lớp ngoài cùng
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(0);
            } else {
                int v = stack.pop();
                int w = stack.pop();
                stack.push(w + Math.max(2 * v, 1));
            }
        }
        
        return stack.pop();
    }
}
```
