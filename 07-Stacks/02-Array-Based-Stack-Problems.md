# 02 - Array-Based Stack Problems

Tài liệu này bao gồm 30 bài tập về Stack (đặc biệt phù hợp áp dụng Array-Based Stack). 10 bài đầu có phân tích thuật toán, độ phức tạp và mã nguồn Java 21 đầy đủ. 20 bài sau được tóm tắt.

---

## 1. Implement Stack using Arrays

### Đề bài chi tiết
Triển khai một Stack cơ bản sử dụng mảng tĩnh (Static Array). Cung cấp các phương thức `push`, `pop`, `top`, và `isEmpty`. Nếu ngăn xếp đầy và gọi `push`, không cần làm gì (hoặc ném lỗi). Nếu ngăn xếp rỗng và gọi `pop`, trả về `-1`.

### Phân tích thuật toán
- Khởi tạo mảng có sức chứa cố định (ví dụ 1000).
- Dùng một con trỏ `topIndex` trỏ vào vị trí hiện tại. Ban đầu `topIndex = -1`.
- Khi `push(x)`: Tăng `topIndex`, ghi x vào mảng.
- Khi `pop()`: Lấy giá trị tại `topIndex` rồi giảm `topIndex` (trả về -1 nếu rỗng).

### Mã nguồn Java
```java
class MyStack {
    private int[] arr;
    private int topIndex;

    public MyStack() {
        arr = new int[1000];
        topIndex = -1;
    }

    public void push(int x) {
        if (topIndex < arr.length - 1) {
            arr[++topIndex] = x;
        }
    }

    public int pop() {
        if (topIndex == -1) return -1;
        return arr[topIndex--];
    }
    
    public int top() {
        if (topIndex == -1) return -1;
        return arr[topIndex];
    }
    
    public boolean isEmpty() {
        return topIndex == -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho mọi thao tác.
- **Không gian (Space):** $O(N)$ (nơi $N = 1000$ là kích thước khởi tạo).

---

## 2. Design a Stack With Increment Operation (LeetCode 1381)

### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu `CustomStack` hỗ trợ các thao tác:
- `CustomStack(int maxSize)`: Khởi tạo mảng với maxSize.
- `void push(int x)`: Đẩy x vào stack, bỏ qua nếu đầy.
- `int pop()`: Trả về phần tử bị pop, hoặc -1 nếu rỗng.
- `void inc(int k, int val)`: Cộng `val` vào $k$ phần tử nằm sâu nhất dưới đáy stack. Nếu số phần tử ít hơn $k$, cộng `val` vào tất cả.

### Phân tích thuật toán
Cách thô bạo (Naïve): duyệt từ `0` đến `Math.min(k, size) - 1` để cộng $O(N)$.
Cách tối ưu $O(1)$ Increment: Sử dụng một mảng `inc[]` phụ tương đương kích thước stack. Khi gọi `inc(k, val)`, ta chỉ lưu giá trị `val` tại vị trí $i = \min(k, size) - 1$. Khi `pop()`, ta cộng giá trị lưu trong mảng `inc` vào kết quả, và truyền dồn giá trị đó xuống vị trí $i-1$.

### Mã nguồn Java
```java
class CustomStack {
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
            // Truyền giá trị increment xuống tầng dưới (Lazy propagation)
            inc[top - 1] += inc[top];
        }
        inc[top] = 0; // Reset
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

### Độ phức tạp
- **Thời gian:** $O(1)$ cho tất cả `push`, `pop`, `increment`.
- **Không gian:** $O(N)$ (cần 2 mảng kích thước $N$).

---

## 3. Max Stack (LeetCode 716)

### Đề bài chi tiết
Thiết kế max stack cho phép đẩy, lấy ra phần tử, xem đỉnh, lấy max, lấy max và xóa nó (popMax).

### Phân tích thuật toán
Có nhiều cách làm. Tuy nhiên nếu dựa vào 2 Array Stack:
- Stack 1 (`values`): Chứa các số.
- Stack 2 (`maxes`): Chứa số max tương ứng với `values`.
- `popMax()`: Dùng một mảng phụ tạm thời (buffer) để chứa các phần tử pop ra cho đến khi tìm thấy max, rồi đẩy ngược buffer lại. Điều này làm `popMax` tốn $O(N)$.

### Mã nguồn Java
```java
import java.util.ArrayDeque;
import java.util.Deque;

class MaxStack {
    private Deque<Integer> stack;
    private Deque<Integer> maxStack;

    public MaxStack() {
        stack = new ArrayDeque<>();
        maxStack = new ArrayDeque<>();
    }
    
    public void push(int x) {
        int max = maxStack.isEmpty() ? x : Math.max(maxStack.peek(), x);
        stack.push(x);
        maxStack.push(max);
    }
    
    public int pop() {
        maxStack.pop();
        return stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int peekMax() {
        return maxStack.peek();
    }
    
    public int popMax() {
        int max = peekMax();
        Deque<Integer> buffer = new ArrayDeque<>();
        while (top() != max) {
            buffer.push(pop());
        }
        pop(); // Xóa giá trị max
        while (!buffer.isEmpty()) {
            push(buffer.pop());
        }
        return max;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(1)$ cho push, pop, top, peekMax. $O(N)$ cho popMax.
- **Không gian:** $O(N)$ để duy trì 2 stack.

---

## 4. Implement Two Stacks in an Array

### Đề bài chi tiết
Khởi tạo cấu trúc dữ liệu cho phép triển khai hai ngăn xếp trên cùng một mảng duy nhất kích thước $N$. Không được ném lỗi trừ khi cả mảng thực sự đầy.

### Phân tích thuật toán
- `Stack 1` bắt đầu từ chỉ số đầu mảng `0` và tăng dần lên (`top1`).
- `Stack 2` bắt đầu từ chỉ số cuối mảng `N-1` và giảm dần xuống (`top2`).
- Trạng thái tràn (Overflow) xảy ra chỉ khi `top1 + 1 == top2`.

### Mã nguồn Java
```java
class TwoStacks {
    private int[] arr;
    private int top1;
    private int top2;

    public TwoStacks(int size) {
        arr = new int[size];
        top1 = -1;
        top2 = size;
    }

    public void push1(int x) {
        if (top1 < top2 - 1) {
            arr[++top1] = x;
        } else {
            throw new RuntimeException("Stack Overflow");
        }
    }

    public void push2(int x) {
        if (top1 < top2 - 1) {
            arr[--top2] = x;
        } else {
            throw new RuntimeException("Stack Overflow");
        }
    }

    public int pop1() {
        if (top1 >= 0) return arr[top1--];
        return -1;
    }

    public int pop2() {
        if (top2 < arr.length) return arr[top2++];
        return -1;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(1)$ cho tất cả hàm.
- **Không gian:** $O(N)$ (chung bộ nhớ trên 1 mảng).

---

## 5. N Stacks In An Array

### Đề bài chi tiết
Tạo ra cấu trúc dữ liệu để đại diện cho $N$ ngăn xếp trong một mảng duy nhất kích thước $S$.

### Phân tích thuật toán
Việc sử dụng chia mảng cố định không tối ưu. Thay vào đó dùng cơ chế **Array-based Linked List**.
- Dùng mảng `arr` chứa giá trị.
- Dùng mảng `top` kích thước $N$, chứa chỉ số của phần tử trên cùng cho mỗi stack.
- Dùng mảng `next` kích thước $S$. `next[i]` chứa chỉ số phần tử đứng dưới `arr[i]` nếu vị trí đó đang có dữ liệu, VÀ chứa chỉ số trống tiếp theo nếu `arr[i]` đang trống.
- Biến `freeTop` quản lý chuỗi ô trống.

### Mã nguồn Java
```java
import java.util.Arrays;

class NStack {
    private int[] arr;
    private int[] top;
    private int[] next;
    private int freeTop;

    public NStack(int N, int S) {
        arr = new int[S];
        top = new int[N];
        next = new int[S];
        Arrays.fill(top, -1);
        
        for (int i = 0; i < S - 1; i++) {
            next[i] = i + 1;
        }
        next[S - 1] = -1;
        freeTop = 0;
    }

    public boolean push(int x, int m) {
        if (freeTop == -1) return false; // Overflow
        
        int i = freeTop; // Lấy index trống
        freeTop = next[i]; // Cập nhật gốc free
        
        arr[i] = x; // Lưu giá trị
        next[i] = top[m - 1]; // Trỏ next của phần tử mới này về đỉnh cũ
        top[m - 1] = i; // Cập nhật đỉnh mới cho stack thứ m
        
        return true;
    }

    public int pop(int m) {
        if (top[m - 1] == -1) return -1; // Underflow
        
        int i = top[m - 1]; // Lấy index của top hiện tại
        top[m - 1] = next[i]; // Cập nhật top về vị trí dưới
        
        next[i] = freeTop; // Cập nhật next của i trỏ tới freeTop cũ
        freeTop = i; // Khôi phục freeTop bằng i
        
        return arr[i];
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(1)$ cho push và pop.
- **Không gian:** $O(N + S)$ (cần thêm mảng trợ giúp `top` và `next`).

---

## 6. Minimum Add to Make Parentheses Valid (LeetCode 921)

### Đề bài chi tiết
Cho một chuỗi `S` chứa `(` và `)`. Bạn cần tìm số lượng dấu ngoặc cần phải thêm vào ở vị trí bất kỳ để chuỗi trở thành chuỗi ngoặc hợp lệ.

### Phân tích thuật toán
Không nhất thiết phải dùng Stack truyền thống. Việc sử dụng biến đếm như một "ArrayStack giả" ở $O(1)$ không gian là tốt nhất.
- Khai báo biến `open` (số ngoặc mở) và `add` (số lượng cần thêm).
- Nếu gặp `(`, `open++`.
- Nếu gặp `)`, nếu `open > 0` thì `open--` (khớp được), ngược lại `add++` (cần thêm ngoặc mở).
Kết quả trả về `open + add`.

### Mã nguồn Java
```java
class Solution {
    public int minAddToMakeValid(String s) {
        int openCount = 0;
        int additions = 0;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                openCount++;
            } else {
                if (openCount > 0) {
                    openCount--;
                } else {
                    additions++;
                }
            }
        }
        return additions + openCount;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$ do duyệt mảng 1 lần.
- **Không gian:** $O(1)$ (tối ưu hơn hẳn việc khởi tạo ArrayStack thực sự).

---

## 7. Maximum Nesting Depth of the Parentheses (LeetCode 1614)

### Đề bài chi tiết
Cho chuỗi hợp lệ gồm ngoặc và số/toán tử. Tìm độ sâu làm tổ sâu nhất của chuỗi. VD: `"(1+(2*3)+((8)/4))+1"` trả về `3`.

### Phân tích thuật toán
Tương tự bài trên, vì là cấu trúc Last-In-First-Out của các cặp ngoặc, ta mô phỏng bằng con số.
- Biến `currentDepth` = độ lớn của "stack".
- Duyệt qua mảng:
  - Gặp `(` thì tăng `currentDepth`, cập nhật giá trị max.
  - Gặp `)` thì giảm `currentDepth`.

### Mã nguồn Java
```java
class Solution {
    public int maxDepth(String s) {
        int maxDepth = 0;
        int currentDepth = 0;
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                currentDepth++;
                if (currentDepth > maxDepth) {
                    maxDepth = currentDepth;
                }
            } else if (c == ')') {
                currentDepth--;
            }
        }
        return maxDepth;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$.
- **Không gian:** $O(1)$.

---

## 8. Exclusive Time of Functions (LeetCode 636)

### Đề bài chi tiết
Trên một CPU 1 nhân, thực thi nhiều hàm. Các hàm được ghi log với dạng `[id:start:time]` hoặc `[id:end:time]`. Một hàm bị ngắt khi có hàm con gọi (push vào stack). Tính tổng thời gian thực thi độc quyền cho mỗi hàm.

### Phân tích thuật toán
Sử dụng Stack.
- Duyệt từng log.
- Nếu là `"start"`: ta push ID hàm vào stack. Nhưng trước đó, nếu stack không rỗng, hàm đang nằm ở đỉnh stack cần được cập nhật thời gian: `time += time_hiện_tại - prev_time`. Cập nhật `prev_time`.
- Nếu là `"end"`: ta pop ID khỏi stack. Cộng vào cho ID vừa pop một khoảng `time_hiện_tại - prev_time + 1`. Sau đó cập nhật `prev_time = time_hiện_tại + 1`.

### Mã nguồn Java
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.List;

class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        int prevTime = 0;

        for (String log : logs) {
            String[] parts = log.split(":");
            int id = Integer.parseInt(parts[0]);
            String type = parts[1];
            int time = Integer.parseInt(parts[2]);

            if (type.equals("start")) {
                if (!stack.isEmpty()) {
                    result[stack.peek()] += time - prevTime;
                }
                stack.push(id);
                prevTime = time;
            } else {
                result[stack.pop()] += time - prevTime + 1;
                prevTime = time + 1;
            }
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(L)$ với $L$ là số lượng log, do split string tốn chi phí.
- **Không gian:** $O(N)$ kích thước kết quả và $O(N/2)$ stack depth.

---

## 9. Find the Most Competitive Subsequence (LeetCode 1673)

### Đề bài chi tiết
Cho mảng `nums` và số `k`. Tìm chuỗi con (subsequence) độ dài `k` mang giá trị cạnh tranh nhất (tương đương với thứ tự từ điển nhỏ nhất).

### Phân tích thuật toán
Sử dụng **Monotonic Array Stack**.
- Ta sẽ duyệt mảng, với mỗi số `nums[i]`, nếu đỉnh stack lớn hơn nó VÀ ta vẫn đủ số lượng phần tử còn lại trong mảng gốc để cấu thành độ dài `k` (nghĩa là `mảng.length - i + stack.size() > k`), ta sẽ pop đỉnh stack đó đi.
- Push số hiện tại vào nếu dung lượng stack còn nhận thêm (size < k).
- Do cần in mảng kết quả nhanh, sử dụng chính một mảng nguyên `int[]` như Stack là siêu tối ưu.

### Mã nguồn Java
```java
class Solution {
    public int[] mostCompetitive(int[] nums, int k) {
        int[] stack = new int[k];
        int top = -1;
        int n = nums.length;
        
        for (int i = 0; i < n; i++) {
            // Khi đỉnh lớn hơn phần tử xét, và phần tử còn lại đủ để tạo size k
            while (top >= 0 && stack[top] > nums[i] && n - i + top >= k) {
                top--;
            }
            if (top < k - 1) {
                stack[++top] = nums[i];
            }
        }
        return stack;
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$ (mỗi phần tử push và pop tối đa 1 lần).
- **Không gian:** $O(K)$ cho mảng lưu kết quả cũng đóng vai trò Stack.

---

## 10. Verify Preorder Serialization of a Binary Tree (LeetCode 331)

### Đề bài chi tiết
Cho chuỗi tiền tố (preorder) của cây nhị phân, trong đó `#` là node null (đáy). Kiểm tra xem chuỗi đó có hợp lệ không bằng cách không cần xây dựng cây thực tế.

### Phân tích thuật toán
- **Cách dùng Stack Array:** Mảng String chứa các tokens. Cứ đẩy vào stack, nếu gặp `num, #, #` (đỉnh là 2 dấu `#` liên tiếp và dưới cùng là `số`) thì pop 3 cái ra, và thay thế bằng `#`. Cuối cùng nếu hợp lệ, mảng còn duy nhất `#`.
- **Cách đếm (Slot trick):** Một node thường thêm 2 slot, node null chiếm 1 slot. Cực kì tối ưu về không gian ($O(1)$).

### Mã nguồn Java
```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] nodes = preorder.split(",");
        int slots = 1;
        
        for (String node : nodes) {
            slots--; // Dù là node thực hay '#' thì đều chiếm 1 vị trí
            
            // Nếu hết slot trước khi đọc hết chuỗi
            if (slots < 0) return false;
            
            // Nếu không phải null, tạo ra 2 slot chờ nhánh trái phải
            if (!node.equals("#")) {
                slots += 2;
            }
        }
        
        return slots == 0; // Phải vừa vặn không thừa không thiếu slot
    }
}
```

### Độ phức tạp
- **Thời gian:** $O(N)$ vì duyệt độ dài string.
- **Không gian:** $O(N)$ do `split` tạo mảng (có thể làm tốt hơn nữa bằng quét index nếu muốn đạt hoàn toàn $O(1)$).

---

## 11. Next Greater Element I (LeetCode 496)

### Đề bài chi tiết
Cho hai mảng số nguyên riêng biệt `nums1` và `nums2`, trong đó `nums1` là tập con của `nums2`. Tìm phần tử lớn hơn tiếp theo (next greater element) cho mỗi phần tử của `nums1` trong `nums2`. Phần tử lớn hơn tiếp theo của $x$ là phần tử đầu tiên bên phải $x$ trong `nums2` có giá trị lớn hơn $x$. Nếu không có, trả về -1.

### Phân tích thuật toán
Sử dụng Monotonic Stack kết hợp Hash Map. Duyệt mảng `nums2` từ trái sang phải. Nếu stack không rỗng và phần tử hiện tại lớn hơn phần tử trên đỉnh stack, điều đó có nghĩa là phần tử hiện tại chính là "next greater element" của đỉnh stack. Ta pop đỉnh stack ra và lưu vào HashMap: `map.put(popped_element, current_element)`. Sau đó push phần tử hiện tại vào stack. Cuối cùng, duyệt qua `nums1` và lấy kết quả từ HashMap.

### Mã nguồn Java
```java
import java.util.HashMap;
import java.util.Map;
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        Deque<Integer> stack = new ArrayDeque<>();
        
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

### Độ phức tạp
- **Thời gian (Time):** $O(N + M)$, trong đó $N$ và $M$ là chiều dài của `nums1` và `nums2`.
- **Không gian (Space):** $O(M)$ để lưu trữ Stack và HashMap cho `nums2`.

---

## 12. Next Greater Element II (LeetCode 503)

### Đề bài chi tiết
Cho một mảng vòng (circular array) `nums`, in ra "next greater element" của mỗi phần tử. Vì là mảng vòng, phần tử tiếp theo của phần tử cuối cùng là phần tử đầu tiên của mảng. Trả về -1 nếu không có phần tử lớn hơn.

### Phân tích thuật toán
Mảng vòng có thể được xử lý bằng cách duyệt mảng 2 lần (chiều dài $2 \times N$). Sử dụng Monotonic Stack để lưu trữ **chỉ số (index)** thay vì giá trị. Trong quá trình duyệt từ $0$ đến $2N - 1$, phần tử thực sự được xét là `nums[i % N]`. Nếu `nums[i % N]` lớn hơn `nums[stack.peek()]`, ta cập nhật kết quả cho vị trí `stack.pop()` và lặp lại. Nếu chưa cập nhật kết quả, khởi tạo bằng -1.

### Mã nguồn Java
```java
import java.util.Arrays;
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < 2 * n; i++) {
            int currentNum = nums[i % n];
            while (!stack.isEmpty() && nums[stack.peek()] < currentNum) {
                result[stack.pop()] = currentNum;
            }
            if (i < n) {
                stack.push(i);
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$, mỗi phần tử được push và pop nhiều nhất 1 lần.
- **Không gian (Space):** $O(N)$ cho Stack.

---

## 13. Daily Temperatures (LeetCode 739)

### Đề bài chi tiết
Cho một mảng `temperatures` đại diện cho nhiệt độ từng ngày, trả về một mảng `answer` sao cho `answer[i]` là số ngày bạn phải chờ sau ngày thứ `i` để có một ngày ấm hơn. Nếu không có ngày nào trong tương lai ấm hơn, giữ nguyên giá trị `0`.

### Phân tích thuật toán
Tiếp tục sử dụng Monotonic Stack lưu trữ **chỉ số (index)** các ngày chưa tìm được ngày ấm hơn. Khi gặp một ngày có nhiệt độ cao hơn nhiệt độ của ngày ở đỉnh Stack, ta lấy ngày đó ra (pop) và tính khoảng cách: `ngày_hiện_tại - ngày_lấy_ra`. Ghi kết quả vào mảng `answer` tại vị trí `ngày_lấy_ra`. Các ngày không tìm thấy sẽ mặc định là 0.

### Mã nguồn Java
```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] answer = new int[n];
        int[] stack = new int[n]; // Dùng mảng làm Stack để tối ưu tốc độ
        int top = -1;
        
        for (int i = 0; i < n; i++) {
            while (top >= 0 && temperatures[i] > temperatures[stack[top]]) {
                int index = stack[top--];
                answer[index] = i - index;
            }
            stack[++top] = i;
        }
        
        return answer;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$ để lưu mảng `stack`.

---

## 14. Valid Parentheses (LeetCode 20)

### Đề bài chi tiết
Cho một chuỗi `s` chỉ chứa các ký tự `'('`, `')'`, `'{'`, `'}'`, `'['` và `']'`, xác định xem chuỗi đầu vào có hợp lệ hay không. Chuỗi hợp lệ nếu các dấu ngoặc mở được đóng bởi cùng loại dấu ngoặc đóng và theo đúng thứ tự.

### Phân tích thuật toán
Dùng một Stack để lưu trữ các dấu ngoặc đóng tương ứng (hoặc lưu ngoặc mở). Mỗi khi gặp dấu mở `(`, `[`, `{`, ta đẩy dấu đóng tương ứng `)`, `]`, `}` vào Stack. Khi gặp dấu đóng, ta kiểm tra xem Stack có rỗng không hoặc phần tử lấy ra (pop) có trùng khớp với dấu đóng đó không. Kết thúc duyệt, chuỗi hợp lệ khi Stack rỗng.

### Mã nguồn Java
```java
class Solution {
    public boolean isValid(String s) {
        char[] stack = new char[s.length()];
        int top = -1;
        
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack[++top] = ')';
            } else if (c == '{') {
                stack[++top] = '}';
            } else if (c == '[') {
                stack[++top] = ']';
            } else {
                if (top == -1 || stack[top--] != c) {
                    return false;
                }
            }
        }
        
        return top == -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$ cho mảng `stack`.

---

## 15. Remove All Adjacent Duplicates in String (LeetCode 1047)

### Đề bài chi tiết
Cho một chuỗi `s` gồm chữ cái viết thường. Việc xóa trùng lặp kề nhau bao gồm việc chọn hai chữ cái kề nhau và giống nhau rồi xóa chúng. Trả về chuỗi cuối cùng sau khi thực hiện tất cả các thao tác xóa có thể.

### Phân tích thuật toán
Sử dụng mảng `char[]` hoạt động như một Stack (Array-based Stack). Duyệt qua từng ký tự, nếu ký tự đang xét giống với ký tự ở đỉnh Stack, ta loại bỏ ký tự ở đỉnh Stack (bằng cách giảm con trỏ `top`). Nếu khác hoặc Stack rỗng, ta đẩy ký tự vào Stack. Cuối cùng, mảng Stack từ 0 đến `top` sẽ tạo thành chuỗi kết quả.

### Mã nguồn Java
```java
class Solution {
    public String removeDuplicates(String s) {
        char[] stack = new char[s.length()];
        int top = -1;
        
        for (char c : s.toCharArray()) {
            if (top >= 0 && stack[top] == c) {
                top--; // Bỏ ký tự trùng lặp
            } else {
                stack[++top] = c; // Thêm ký tự mới
            }
        }
        
        return new String(stack, 0, top + 1);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$.

---

## 16. Remove All Adjacent Duplicates In String II (LeetCode 1209)

### Đề bài chi tiết
Cho chuỗi `s` và số nguyên `k`. Bạn cần xóa đi `k` ký tự giống nhau đứng kề nhau. Lặp lại việc này cho đến khi không thể xóa được nữa. Trả về chuỗi kết quả.

### Phân tích thuật toán
Thay vì chỉ kiểm tra đỉnh, ta cần biết số lượng ký tự kề nhau đã xuất hiện. Dùng hai mảng làm Stack song song: một cho ký tự (char stack) và một cho bộ đếm (count stack). Khi duyệt ký tự:
- Nếu giống đỉnh char stack, tăng giá trị ở đỉnh count stack. Nếu count đạt `k`, pop cả 2 stack.
- Nếu khác đỉnh char stack, push ký tự mới và push `1` vào count stack.
Xây dựng kết quả bằng StringBuilder dựa trên 2 stack.

### Mã nguồn Java
```java
class Solution {
    public String removeDuplicates(String s, int k) {
        int n = s.length();
        char[] charStack = new char[n];
        int[] countStack = new int[n];
        int top = -1;
        
        for (char c : s.toCharArray()) {
            if (top >= 0 && charStack[top] == c) {
                countStack[top]++;
                if (countStack[top] == k) {
                    top--;
                }
            } else {
                top++;
                charStack[top] = c;
                countStack[top] = 1;
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i <= top; i++) {
            for (int j = 0; j < countStack[i]; j++) {
                sb.append(charStack[i]);
            }
        }
        
        return sb.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$ cho 2 Stack.

---

## 17. Min Stack (LeetCode 155)

### Đề bài chi tiết
Thiết kế cấu trúc dữ liệu `MinStack` hỗ trợ các thao tác `push`, `pop`, `top`, và lấy ra phần tử nhỏ nhất `getMin` trong thời gian $O(1)$.

### Phân tích thuật toán
Có thể dùng 2 stack song song hoặc 1 Node (Pair) lưu giá trị và giá trị min hiện tại. Ở đây ta có thể dùng 2 mảng tĩnh (Array-Based) giả lập. Một mảng `valStack` chứa giá trị thực tế, và `minStack` chứa giá trị nhỏ nhất tương ứng tính từ đáy lên. Mỗi khi push giá trị `val`, ta cũng push `Math.min(val, min_cũ)` vào `minStack`.

### Mã nguồn Java
```java
class MinStack {
    private int[] valStack;
    private int[] minStack;
    private int top;

    public MinStack() {
        valStack = new int[30000];
        minStack = new int[30000];
        top = -1;
    }
    
    public void push(int val) {
        if (top == -1) {
            top++;
            valStack[top] = val;
            minStack[top] = val;
        } else {
            int currentMin = minStack[top];
            top++;
            valStack[top] = val;
            minStack[top] = Math.min(val, currentMin);
        }
    }
    
    public void pop() {
        if (top >= 0) {
            top--;
        }
    }
    
    public int top() {
        return valStack[top];
    }
    
    public int getMin() {
        return minStack[top];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(1)$ cho mọi thao tác.
- **Không gian (Space):** $O(N)$.

---

## 18. Asteroid Collision (LeetCode 735)

### Đề bài chi tiết
Cho một mảng các thiên thạch `asteroids`. Giá trị tuyệt đối là kích thước, dấu (dương/âm) là hướng bay (phải/trái). Nếu hai thiên thạch gặp nhau, cái nhỏ hơn sẽ phát nổ. Nếu bằng nhau, cả hai phát nổ. Hai thiên thạch cùng hướng không bao giờ gặp nhau. Trả về trạng thái các thiên thạch sau mọi va chạm.

### Phân tích thuật toán
Va chạm chỉ xảy ra khi thiên thạch trong Stack bay sang phải (`> 0`) và thiên thạch mới bay sang trái (`< 0`). Sử dụng Stack: duyệt qua các thiên thạch, nếu xảy ra điều kiện va chạm, so sánh kích thước tuyệt đối:
- Đỉnh nhỏ hơn thì pop đỉnh và tiếp tục xét.
- Bằng nhau thì pop đỉnh và phá hủy luôn cả cái đang xét.
- Đỉnh lớn hơn thì cái đang xét bị phá hủy (không thêm vào Stack).

### Mã nguồn Java
```java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        int[] stack = new int[asteroids.length];
        int top = -1;
        
        for (int ast : asteroids) {
            boolean exploded = false;
            while (top >= 0 && stack[top] > 0 && ast < 0) {
                if (Math.abs(stack[top]) < Math.abs(ast)) {
                    top--; // Thiên thạch trong stack nổ, tiếp tục vòng lặp
                    continue;
                } else if (Math.abs(stack[top]) == Math.abs(ast)) {
                    top--; // Cả hai cùng nổ
                }
                exploded = true;
                break;
            }
            if (!exploded) {
                stack[++top] = ast;
            }
        }
        
        int[] result = new int[top + 1];
        for (int i = 0; i <= top; i++) {
            result[i] = stack[i];
        }
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$, mỗi thiên thạch push và pop cao nhất 1 lần.
- **Không gian (Space):** $O(N)$ lưu Stack.

---

## 19. Online Stock Span (LeetCode 901)

### Đề bài chi tiết
Thiết kế lớp `StockSpanner` tính toán nhịp của giá cổ phiếu. Nhịp cổ phiếu của ngày hôm nay được định nghĩa là số lượng ngày liên tiếp tối đa (bắt đầu từ hôm nay và ngược về trước) có giá nhỏ hơn hoặc bằng giá hôm nay.

### Phân tích thuật toán
Dùng Monotonic Stack lưu thông tin theo cặp `(giá, nhịp)`. Khi có giá cổ phiếu mới, chừng nào Stack còn phần tử và giá ở đỉnh Stack nhỏ hơn hoặc bằng giá mới, ta pop phần tử đó ra và cộng gộp số nhịp của nó vào nhịp của ngày hôm nay. Cuối cùng push giá trị mới và nhịp tổng vào Stack.

### Mã nguồn Java
```java
import java.util.ArrayDeque;
import java.util.Deque;

class StockSpanner {
    // int[] chứa {price, span}
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

### Độ phức tạp
- **Thời gian (Time):** Khấu hao $O(1)$ cho mỗi lần gọi `next()`.
- **Không gian (Space):** $O(N)$ (lưu tối đa $N$ ngày).

---

## 20. Final Prices With a Special Discount (LeetCode 1475)

### Đề bài chi tiết
Cho mảng `prices`. Với mặt hàng `i`, bạn sẽ được giảm giá bằng `prices[j]` trong đó `j > i` là mặt hàng đầu tiên có `prices[j] <= prices[i]`. Trả về mảng giá sau khi đã trừ đi giảm giá. Nếu không có mặt hàng nào phù hợp, bạn không được giảm giá.

### Phân tích thuật toán
Đây là dạng bài "Next Smaller or Equal Element". Sử dụng Monotonic Stack (Array) duyệt mảng từ trái qua phải. Stack lưu trữ các chỉ số của những mặt hàng chưa tìm được mặt hàng giảm giá. Nếu gặp một giá `prices[i]` nhỏ hơn hoặc bằng `prices[stack[top]]`, ta lấy `stack[top]` ra và cập nhật giá sau khi giảm bằng `prices[stack[top]] - prices[i]`.

### Mã nguồn Java
```java
class Solution {
    public int[] finalPrices(int[] prices) {
        int n = prices.length;
        int[] result = prices.clone(); // Bắt đầu với giá trị chưa giảm
        int[] stack = new int[n];
        int top = -1;
        
        for (int i = 0; i < n; i++) {
            // Khi tìm được giá nhỏ hơn hoặc bằng, nó chính là discount cho các index trên stack
            while (top >= 0 && prices[i] <= prices[stack[top]]) {
                int idx = stack[top--];
                result[idx] = prices[idx] - prices[i];
            }
            stack[++top] = i; // Đưa index hiện tại vào stack
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(N)$ để lưu Stack (mảng tự chế siêu tốc).

---

## 21. Evaluate Reverse Polish Notation (LeetCode 150)

### Đề bài chi tiết
Cho một mảng các chuỗi `tokens` đại diện cho biểu thức toán học dưới dạng Hậu tố (Reverse Polish Notation). Tính toán và trả về giá trị của biểu thức đó. Các toán tử hợp lệ gồm `+`, `-`, `*`, và `/`. Phép chia hai số nguyên phải được làm tròn về phía 0 (truncate toward zero). Không có chia cho 0, và mọi biểu thức đều hợp lệ.

### Phân tích thuật toán
Sử dụng Stack để duyệt qua mảng `tokens`. Nếu gặp toán tử, ta pop 2 phần tử trên cùng khỏi Stack, thực hiện phép tính tương ứng (phần tử lấy ra sau nằm trước phần tử lấy ra trước trong phép tính), rồi push kết quả trở lại Stack. Nếu gặp số, ta chuyển đổi chuỗi sang số nguyên và push vào Stack. Kết quả cuối cùng là phần tử duy nhất còn lại trong Stack.

### Mã nguồn Java
```java
class Solution {
    public int evalRPN(String[] tokens) {
        int[] stack = new int[tokens.length];
        int top = -1;
        
        for (String token : tokens) {
            if (token.equals("+")) {
                int b = stack[top--];
                int a = stack[top--];
                stack[++top] = a + b;
            } else if (token.equals("-")) {
                int b = stack[top--];
                int a = stack[top--];
                stack[++top] = a - b;
            } else if (token.equals("*")) {
                int b = stack[top--];
                int a = stack[top--];
                stack[++top] = a * b;
            } else if (token.equals("/")) {
                int b = stack[top--];
                int a = stack[top--];
                stack[++top] = a / b;
            } else {
                stack[++top] = Integer.parseInt(token);
            }
        }
        
        return stack[top];
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$ với $N$ là số lượng tokens.
- **Không gian (Space):** $O(N)$ cho Stack số nguyên.

---

## 22. Build an Array With Stack Operations (LeetCode 1441)

### Đề bài chi tiết
Cho mảng `target` và số nguyên `n`. Xây dựng mảng `target` bằng cách thực hiện các thao tác "Push" và "Pop" trên dòng dữ liệu từ $1$ đến $n$. Mảng `target` được đảm bảo là tăng ngặt. Trả về mảng các chuỗi thao tác.

### Phân tích thuật toán
Chúng ta dùng một con trỏ `index` duyệt qua `target`. Với mỗi số nguyên `i` từ $1$ đến $n$, ta thêm "Push" vào danh sách kết quả. Nếu `i` bằng với `target[index]`, nghĩa là số này cần giữ lại, ta tăng `index`. Nếu không bằng, số này phải bị bỏ đi, nên ta thêm "Pop". Vòng lặp dừng khi `index` bằng với kích thước của `target` (hoàn tất xây dựng).

### Mã nguồn Java
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> buildArray(int[] target, int n) {
        List<String> result = new ArrayList<>();
        int index = 0;
        
        for (int i = 1; i <= n && index < target.length; i++) {
            result.add("Push");
            if (target[index] == i) {
                index++;
            } else {
                result.add("Pop");
            }
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$.
- **Không gian (Space):** $O(1)$ (Không tính bộ nhớ của List kết quả trả về).

---

## 23. Make The String Great (LeetCode 1544)

### Đề bài chi tiết
Một chuỗi tốt (great string) là chuỗi không chứa 2 ký tự kề nhau $s[i]$ và $s[i+1]$ thỏa mãn: một chữ cái in hoa và chữ cái còn lại in thường của cùng một ký tự. Biến đổi chuỗi thành chuỗi tốt bằng cách xóa đi các cặp xấu như vậy. Trả về chuỗi kết quả.

### Phân tích thuật toán
Dùng mảng `char[]` đóng vai trò là Stack. Duyệt từng ký tự trong chuỗi gốc, so sánh với ký tự trên đỉnh Stack. Một cặp ký tự hoa-thường của cùng một chữ cái sẽ có hiệu giá trị ASCII chính xác bằng $32$ (tức là `Math.abs(c - stack[top]) == 32`). Nếu tìm thấy cặp xấu, giảm `top` (pop), nếu không tìm thấy thì push ký tự vào Stack.

### Mã nguồn Java
```java
class Solution {
    public String makeGood(String s) {
        char[] stack = new char[s.length()];
        int top = -1;
        
        for (char c : s.toCharArray()) {
            if (top >= 0 && Math.abs(stack[top] - c) == 32) {
                top--; // Bỏ cặp xấu
            } else {
                stack[++top] = c;
            }
        }
        
        return new String(stack, 0, top + 1);
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$ với $N$ là chiều dài chuỗi.
- **Không gian (Space):** $O(N)$ cho `char[]` Stack.

---

## 24. Longest Valid Parentheses (LeetCode 32)

### Đề bài chi tiết
Cho chuỗi chứa các dấu ngoặc `'('` và `')'`, tìm độ dài của chuỗi con liên tục có các ngoặc hợp lệ dài nhất.

### Phân tích thuật toán
Sử dụng Stack chứa các **chỉ số (index)**. Ban đầu, push giá trị `-1` vào Stack làm mốc bắt đầu.
Duyệt qua chuỗi:
- Nếu là `'('`, push chỉ số vào Stack.
- Nếu là `')'`, pop phần tử trên đỉnh. Sau khi pop, nếu Stack rỗng (nghĩa là mất mốc), ta lấy chỉ số hiện tại push vào làm mốc mới. Nếu Stack không rỗng, độ dài hợp lệ hiện tại được tính bằng `i - stack.peek()`. Ta cập nhật độ dài lớn nhất (max length).

### Mã nguồn Java
```java
class Solution {
    public int longestValidParentheses(String s) {
        int[] stack = new int[s.length() + 1];
        int top = -1;
        stack[++top] = -1; // Mốc đáy
        
        int maxLength = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack[++top] = i;
            } else {
                top--; // Pop
                if (top == -1) {
                    stack[++top] = i; // Cập nhật mốc mới
                } else {
                    maxLength = Math.max(maxLength, i - stack[top]);
                }
            }
        }
        
        return maxLength;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$
- **Không gian (Space):** $O(N)$

---

## 25. Validate Stack Sequences (LeetCode 946)

### Đề bài chi tiết
Cho hai mảng số nguyên `pushed` và `popped` chứa các giá trị phân biệt. Kiểm tra xem chúng có thể là kết quả của một chuỗi các thao tác đẩy và lấy (push/pop) trên một ngăn xếp rỗng ban đầu hay không.

### Phân tích thuật toán
Dùng một mảng để giả lập quá trình. Dùng một con trỏ `j` để duyệt mảng `popped`. Lặp qua từng phần tử trong `pushed` và đưa vào ngăn xếp giả. Ngay sau khi đẩy vào, ta sử dụng vòng lặp `while` để liên tục lấy phần tử ra khỏi ngăn xếp (pop) nếu đỉnh ngăn xếp khớp với `popped[j]`, đồng thời tăng `j`. Quá trình hợp lệ nếu toàn bộ ngăn xếp được làm rỗng.

### Mã nguồn Java
```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        int[] stack = new int[pushed.length];
        int top = -1;
        int j = 0; // Con trỏ cho mảng popped
        
        for (int x : pushed) {
            stack[++top] = x;
            while (top >= 0 && stack[top] == popped[j]) {
                top--;
                j++;
            }
        }
        
        return top == -1;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$, vì mỗi phần tử chỉ bị đẩy vào và rút ra khỏi ngăn xếp đúng 1 lần.
- **Không gian (Space):** $O(N)$ để mô phỏng ngăn xếp.

---

## 26. Decode String (LeetCode 394)

### Đề bài chi tiết
Giải mã chuỗi đã được nén theo định dạng `k[encoded_string]`, trong đó `encoded_string` bên trong dấu ngoặc vuông sẽ được lặp lại `k` lần. `k` luôn là số nguyên dương.

### Phân tích thuật toán
Chúng ta dùng 2 ngăn xếp song song: `countStack` chứa số lần lặp và `stringStack` chứa các chuỗi đã xây dựng trước đó. 
- Duyệt chuỗi, nếu là chữ số, ta cộng dồn tạo số `k`. 
- Nếu gặp `[`, đẩy số `k` và chuỗi hiện tại (`currentStr`) vào 2 Stack tương ứng, và đặt lại `k=0`, `currentStr=""`. 
- Nếu gặp `]`, lấy `count` từ `countStack`, lấy chuỗi nền từ `stringStack`, sau đó nối `currentStr` lặp lại `count` lần vào chuỗi nền và coi đó là `currentStr` mới.

### Mã nguồn Java
```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public String decodeString(String s) {
        Deque<Integer> countStack = new ArrayDeque<>();
        Deque<StringBuilder> stringStack = new ArrayDeque<>();
        StringBuilder currentStr = new StringBuilder();
        int k = 0;
        
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                k = k * 10 + (c - '0');
            } else if (c == '[') {
                countStack.push(k);
                stringStack.push(currentStr);
                currentStr = new StringBuilder();
                k = 0;
            } else if (c == ']') {
                StringBuilder decodedStr = stringStack.pop();
                int currentK = countStack.pop();
                for (int i = 0; i < currentK; i++) {
                    decodedStr.append(currentStr);
                }
                currentStr = decodedStr;
            } else {
                currentStr.append(c);
            }
        }
        return currentStr.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(\max(K) \times N)$, phụ thuộc vào độ lớn của $k$ và độ dài chuỗi trả về.
- **Không gian (Space):** $O(M)$ với $M$ là số lượng ngoặc lồng nhau (độ sâu).

---

## 27. Simplify Path (LeetCode 71)

### Đề bài chi tiết
Cho chuỗi đường dẫn tuyệt đối thư mục trên hệ thống Unix (bắt đầu bằng `/`), hãy biến đổi nó thành đường dẫn tiêu chuẩn (canonical path) đơn giản nhất. Chuyển đổi dấu `//` thành `/`, bỏ qua dấu `.` (thư mục hiện tại) và di chuyển lên một cấp với `..`.

### Phân tích thuật toán
Tách chuỗi bằng hàm `split("/")`. Dùng Stack chứa các tên thư mục hợp lệ. Nếu mảng bị tách trống hoặc là `.`, ta bỏ qua. Nếu là `..`, ta pop phần tử trên đỉnh Stack nếu Stack không rỗng. Nếu là tên thư mục, ta push vào Stack. Cuối cùng, kết nối các phần tử từ dưới lên trên bằng `/` để có chuỗi kết quả.

### Mã nguồn Java
```java
class Solution {
    public String simplifyPath(String path) {
        String[] parts = path.split("/");
        String[] stack = new String[parts.length];
        int top = -1;
        
        for (String part : parts) {
            if (part.equals("") || part.equals(".")) {
                continue;
            }
            if (part.equals("..")) {
                if (top >= 0) top--;
            } else {
                stack[++top] = part;
            }
        }
        
        if (top == -1) return "/";
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i <= top; i++) {
            sb.append("/").append(stack[i]);
        }
        return sb.toString();
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$, tách chuỗi và duyệt toàn bộ mảng.
- **Không gian (Space):** $O(N)$, lưu trữ các từ trong mảng Stack.

---

## 28. Basic Calculator (LeetCode 224)

### Đề bài chi tiết
Tính toán chuỗi biểu thức toán học hợp lệ chứa các chữ số, `+`, `-`, `(`, `)`, và khoảng trắng. Lưu ý không có nhân chia.

### Phân tích thuật toán
Sử dụng Stack để lưu trạng thái (`result` tạm thời và dấu `sign` trước mỗi dấu ngoặc mở). 
- Biến `result` để tính tổng hiện tại, `sign` (1 hoặc -1) thể hiện dấu hiện tại. 
- Khi duyệt chữ số, tính toàn bộ giá trị rồi cộng nó (kèm dấu) vào `result`. 
- Khi gặp `+` hoặc `-`, cập nhật lại `sign`. 
- Khi gặp `(`, lưu `result` và `sign` vào Stack, sau đó reset lại `result=0`, `sign=1`. 
- Khi gặp `)`, tính xong ngoặc, pop `sign` từ Stack nhân với `result` ngoặc này, rồi pop `result` cũ cộng thêm vào (chính là thực hiện phép tính phía ngoài ngoặc).

### Mã nguồn Java
```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int calculate(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
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
                sign = 1;
                result = 0;
            } else if (c == ')') {
                result += sign * number;
                number = 0;
                result *= stack.pop(); // sign
                result += stack.pop(); // result_cũ
            }
        }
        
        if (number != 0) {
            result += sign * number;
        }
        
        return result;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$
- **Không gian (Space):** $O(N)$ trong trường hợp xấu nhất toàn dấu ngoặc lồng nhau.

---

## 29. Pattern 132 (LeetCode 456)

### Đề bài chi tiết
Tìm xem có tồn tại chuỗi mẫu 132 trong mảng số nguyên hay không. Tức là tồn tại 3 chỉ số $i < j < k$ sao cho `nums[i] < nums[k] < nums[j]`.

### Phân tích thuật toán
Duyệt mảng từ phải qua trái để tìm $k$ và $j$.
Ta dùng Monotonic Stack lưu các giá trị được coi là $nums[j]$ (lớn nhất). Biến `k_val` lưu lại giá trị lớn nhất trong số các phần tử nhỏ hơn đỉnh Stack (tức là $nums[k]$). 
- Nếu gặp `nums[i] < k_val`, tức là ta đã tìm ra mẫu (vì ta chắc chắn $nums[i] < nums[k]$ và trước đó đã đảm bảo $nums[k] < nums[j]$ nhờ việc rút $k$ ra từ Stack).
- Nếu lớn hơn `k_val`, ta tiếp tục cập nhật lại `k_val` bằng cách pop các phần tử nhỏ hơn `nums[i]` từ Stack, và sau đó push `nums[i]` vào. 

### Mã nguồn Java
```java
class Solution {
    public boolean find132pattern(int[] nums) {
        int n = nums.length;
        if (n < 3) return false;
        
        int[] stack = new int[n];
        int top = -1;
        int k_val = Integer.MIN_VALUE; // Đóng vai trò là nums[k]
        
        for (int i = n - 1; i >= 0; i--) {
            if (nums[i] < k_val) {
                return true;
            }
            while (top >= 0 && stack[top] < nums[i]) {
                k_val = stack[top--];
            }
            stack[++top] = nums[i];
        }
        
        return false;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$
- **Không gian (Space):** $O(N)$

---

## 30. Largest Rectangle in Histogram (LeetCode 84)

### Đề bài chi tiết
Cho mảng số nguyên biểu thị chiều cao của các thanh cột liền kề nhau có độ rộng bằng 1. Tìm diện tích hình chữ nhật lớn nhất có thể tạo ra bên trong các cột histogram này.

### Phân tích thuật toán
Monotonic Stack lưu trữ các chỉ số của cột tăng dần. Khi gặp một cột thấp hơn cột trên đỉnh Stack, nó đánh dấu "đầu cuối" (giới hạn phải) của hình chữ nhật đối với cột ở đỉnh. Ta liên tục lấy cột từ đỉnh ra (pop) tính diện tích.
- Chiều cao là `heights[popped_index]`.
- Chiều rộng là `(i - stack_top_mới - 1)`, nếu rỗng thì chiều rộng từ đầu mảng `i`.
- Cuối cùng duyệt vòng for `i = n` để xử lý nốt các phần tử còn kẹt trong Stack (những thanh cột mở rộng được tới sát lề phải).

### Mã nguồn Java
```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int[] stack = new int[n];
        int top = -1;
        int maxArea = 0;
        
        for (int i = 0; i <= n; i++) {
            int currentHeight = (i == n) ? 0 : heights[i];
            
            while (top >= 0 && currentHeight < heights[stack[top]]) {
                int h = heights[stack[top--]];
                int w = (top == -1) ? i : (i - stack[top] - 1);
                maxArea = Math.max(maxArea, h * w);
            }
            
            stack[++top] = i;
        }
        
        return maxArea;
    }
}
```

### Độ phức tạp
- **Thời gian (Time):** $O(N)$, push và pop mỗi phần tử 1 lần.
- **Không gian (Space):** $O(N)$ cho Stack.

