# Monotonic Stack - Bài Tập Thực Hành

Tài liệu này bao gồm 30 bài tập về Monotonic Stack (Stack đơn điệu), với 10 bài đầu tiên có đầy đủ Đề bài, Phân tích, Mã nguồn Java 21, Độ phức tạp.

---

## 1. Next Greater Element I (LeetCode 496)

### Đề bài chi tiết
Cho hai mảng số nguyên phân biệt `nums1` và `nums2`, trong đó `nums1` là mảng con của `nums2`. Tìm Next Greater Element (phần tử lớn hơn tiếp theo) cho mỗi phần tử của `nums1` trong `nums2`. Nếu không có, trả về `-1`.

### Phân tích thuật toán
- Sử dụng Decreasing Monotonic Stack để duyệt `nums2`.
- Kết hợp `HashMap` để lưu cặp key-value `(phần tử hiện tại : phần tử lớn hơn tiếp theo)`.
- Duyệt qua `nums1` và lấy kết quả từ `HashMap`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
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
- **Time**: $O(N + M)$ với $N, M$ là độ dài của 2 mảng.
- **Space**: $O(N)$ lưu trữ trong Map và Stack.

---

## 2. Daily Temperatures (LeetCode 739)

### Đề bài chi tiết
Cho mảng `temperatures` biểu diễn nhiệt độ hàng ngày, trả về mảng `answer` trong đó `answer[i]` là số ngày phải chờ sau ngày thứ `i` để có nhiệt độ ấm hơn. Nếu không có, trả về 0.

### Phân tích thuật toán
- Tìm khoảng cách index tới phần tử lớn hơn tiếp theo.
- Stack đơn điệu giảm, lưu giữ index.
- Khi gặp nhiệt độ cao hơn `temperatures[stack.peek()]`, pop và tính khoảng cách: `i - poppedIndex`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int prevIndex = stack.pop();
                result[prevIndex] = i - prevIndex;
            }
            stack.push(i);
        }
        return result;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 3. Largest Rectangle in Histogram (LeetCode 84)

### Đề bài chi tiết
Cho mảng `heights` biểu diễn độ cao các cột hình chữ nhật có chiều rộng 1. Tìm diện tích hình chữ nhật lớn nhất trong histogram (biểu đồ đồ thị).

### Phân tích thuật toán
- Dùng Monotonic Increasing Stack. Khi gặp cột thấp hơn đỉnh stack, ta không thể kéo dài hình chữ nhật của cột ở đỉnh sang bên phải nữa.
- Lấy đỉnh stack ra tính diện tích: Chiều cao = đỉnh vừa pop, Chiều rộng = `i - stack.peek() - 1` (nếu stack rỗng thì chiều rộng bằng `i`).
- Trick: Thêm giá trị `0` vào cuối mảng để ép tất cả các phần tử trong stack phải pop ra ngoài.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        int maxArea = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        
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
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 4. Trapping Rain Water (LeetCode 42)

### Đề bài chi tiết
Cho `n` số nguyên không âm đại diện cho bản đồ độ cao có chiều rộng 1. Tính toán lượng nước có thể giữ lại được sau khi trời mưa.

### Phân tích thuật toán
- Sử dụng Decreasing Stack. Nếu gặp cột cao hơn đỉnh stack, vùng lõm được hình thành.
- Tính lượng nước theo vùng lõm (bounded bằng phần tử ở `stack.peek()` và phần tử `i` hiện tại).
- Công thức: `width = i - left - 1`, `waterHeight = min(height[left], height[i]) - height[mid]`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int trap(int[] height) {
        int water = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < height.length; i++) {
            while (!stack.isEmpty() && height[i] > height[stack.peek()]) {
                int mid = stack.pop();
                if (stack.isEmpty()) break;
                
                int left = stack.peek();
                int h = Math.min(height[left], height[i]) - height[mid];
                int w = i - left - 1;
                water += h * w;
            }
            stack.push(i);
        }
        return water;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 5. Sum of Subarray Minimums (LeetCode 907)

### Đề bài chi tiết
Cho mảng số nguyên `arr`, tìm tổng giá trị cực tiểu của mọi dãy con (subarray) liên tiếp của `arr`. Kết quả có thể rất lớn, hãy lấy modulo $10^9 + 7$.

### Phân tích thuật toán
- Tìm đoạn dài nhất mà phần tử `arr[i]` là nhỏ nhất.
- Bằng cách dùng 2 Monotonic Stack (tăng dần) để tìm Previous Smaller và Next Smaller của mỗi phần tử.
- Số mảng con = `(i - prev_smaller_index) * (next_smaller_index - i)`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int sumSubarrayMins(int[] arr) {
        int n = arr.length;
        long mod = (long)1e9 + 7;
        Deque<Integer> stack = new ArrayDeque<>();
        int[] left = new int[n];
        int[] right = new int[n];
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) stack.pop();
            left[i] = stack.isEmpty() ? i + 1 : i - stack.peek();
            stack.push(i);
        }
        
        stack.clear();
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) stack.pop();
            right[i] = stack.isEmpty() ? n - i : stack.peek() - i;
            stack.push(i);
        }
        
        long result = 0;
        for (int i = 0; i < n; i++) {
            result = (result + (long)arr[i] * left[i] * right[i]) % mod;
        }
        return (int)result;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 6. Online Stock Span (LeetCode 901)

### Đề bài chi tiết
Viết một class nhận liên tiếp các luồng giá cổ phiếu hàng ngày và trả về "nhịp" (span) của cổ phiếu. Span của ngày hôm nay bằng 1 cộng với số ngày liên tiếp trước đó có giá bé hơn hoặc bằng giá hôm nay.

### Phân tích thuật toán
- Duy trì Monotonic Decreasing Stack. Mỗi node chứa `[price, span]`.
- Nếu giá mới `>=` đỉnh stack, pop đỉnh ra, đồng thời cộng dồn giá trị `span` của đỉnh đó vào giá trị hiện tại.

### Mã nguồn Java
```java
import java.util.*;

class StockSpanner {
    // Record trong Java 14+ lưu trữ cặp giá và khoảng ngày
    private record Stock(int price, int span) {}
    private final Deque<Stock> stack;

    public StockSpanner() {
        stack = new ArrayDeque<>();
    }
    
    public int next(int price) {
        int span = 1;
        while (!stack.isEmpty() && stack.peek().price() <= price) {
            span += stack.pop().span();
        }
        stack.push(new Stock(price, span));
        return span;
    }
}
```
### Độ phức tạp
- **Time**: $O(1)$ trung bình cho mỗi lần gọi `next()`.
- **Space**: $O(N)$

---

## 7. Next Greater Element II (LeetCode 503)

### Đề bài chi tiết
Tìm Next Greater Element trong mảng vòng (Circular Array). 

### Phân tích thuật toán
- Mảng vòng có thể được xử lý đơn giản bằng cách duyệt mảng 2 lần (`2 * n`).
- Sử dụng kỹ thuật chia dư `i % n` để trỏ đúng vị trí ảo của mảng vòng.
- Các logic về Monotonic Decreasing Stack giữ nguyên.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1);
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < 2 * n; i++) {
            int num = nums[i % n];
            while (!stack.isEmpty() && nums[stack.peek()] < num) {
                result[stack.pop()] = num;
            }
            if (i < n) stack.push(i);
        }
        
        return result;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 8. Maximal Rectangle (LeetCode 85)

### Đề bài chi tiết
Cho một ma trận nhị phân 2D (các ký tự '0' và '1'). Tìm hình chữ nhật lớn nhất chỉ chứa '1' và trả về diện tích.

### Phân tích thuật toán
- Biến bài toán thành bài Largest Rectangle in Histogram.
- Với mỗi hàng, tính độ cao của cột '1' liên tiếp tính từ trên xuống.
- Gọi lại hàm tính Histogram ở Bài 3 cho từng hàng để tìm Max.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix.length == 0) return 0;
        int maxArea = 0;
        int[] heights = new int[matrix[0].length];
        
        for (char[] row : matrix) {
            for (int j = 0; j < row.length; j++) {
                heights[j] = row[j] == '1' ? heights[j] + 1 : 0;
            }
            maxArea = Math.max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }
    
    private int largestRectangleArea(int[] heights) {
        int n = heights.length, maxArea = 0;
        Deque<Integer> stack = new ArrayDeque<>();
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
### Độ phức tạp
- **Time**: $O(R \times C)$
- **Space**: $O(C)$

---

## 9. Remove Duplicate Letters (LeetCode 316)

### Đề bài chi tiết
Cho chuỗi s, xóa ký tự trùng lặp sao cho mỗi chữ cái chỉ xuất hiện 1 lần. Kết quả phải có thứ tự từ điển nhỏ nhất.

### Phân tích thuật toán
- Sử dụng Monotonic Increasing Stack để loại bỏ các ký tự lớn hơn hiện tại NẾU như các ký tự bị loại bỏ CÒN xuất hiện ở phía sau (kiểm tra đếm tần suất hoặc index cuối).
- Mảng `boolean[] visited` để kiểm tra ký tự đã có trong stack hay chưa.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public String removeDuplicateLetters(String s) {
        int[] lastIndex = new int[26];
        for (int i = 0; i < s.length(); i++) {
            lastIndex[s.charAt(i) - 'a'] = i;
        }
        
        boolean[] visited = new boolean[26];
        Deque<Character> stack = new ArrayDeque<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (visited[c - 'a']) continue;
            
            while (!stack.isEmpty() && stack.peek() > c && lastIndex[stack.peek() - 'a'] > i) {
                visited[stack.pop() - 'a'] = false;
            }
            
            stack.push(c);
            visited[c - 'a'] = true;
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) sb.append(stack.pollLast());
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(1)$ (Stack tối đa 26 ký tự)

---

## 10. Find the Most Competitive Subsequence (LeetCode 1673)

### Đề bài chi tiết
Tìm dãy con của mảng có độ dài `k` mang tính cạnh tranh cao nhất (nhỏ nhất theo thứ tự từ điển).

### Phân tích thuật toán
- Dùng Increasing Monotonic Stack. Khi gặp phần tử nhỏ hơn đỉnh, pop ra nhưng phải đảm bảo SỐ LƯỢNG PHẦN TỬ CÒN LẠI đủ để điền đầy độ dài `k`.
- Nếu kích thước stack đã đạt `k` mà giá trị mới lớn hơn hoặc bằng, ta có thể bỏ qua (hoặc giữ kích thước stack tối đa là `k`).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] mostCompetitive(int[] nums, int k) {
        int n = nums.length;
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            // Còn đủ phần tử phía sau để lắp đầy không gian còn lại (k - stack.size())
            while (!stack.isEmpty() && nums[i] < stack.peek() && nums.length - i + stack.size() - 1 >= k) {
                stack.pop();
            }
            if (stack.size() < k) {
                stack.push(nums[i]);
            }
        }
        
        int[] result = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            result[i] = stack.pop();
        }
        return result;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(K)$

---

## 11. Number of Visible People in a Queue (LeetCode 1944)

### Đề bài chi tiết
Có `n` người đứng xếp hàng, cho mảng `heights` biểu diễn chiều cao của mỗi người. Một người ở vị trí `i` có thể nhìn thấy người ở vị trí `j` nếu `i < j` và mọi người đứng giữa họ đều thấp hơn cả hai người này. Trả về một mảng trong đó phần tử thứ `i` là số người mà người thứ `i` có thể nhìn thấy ở phía bên phải.

### Phân tích thuật toán
- Sử dụng Monotonic Decreasing Stack để duyệt từ phải sang trái, lưu trữ chiều cao của những người đang xét.
- Khi người hiện tại cao hơn người ở đỉnh stack, họ chắn tầm nhìn của người hiện tại tới những người thấp hơn phía sau, nên ta pop người đó ra và tăng biến đếm số người nhìn thấy lên 1.
- Sau vòng lặp while, nếu stack vẫn còn phần tử (nghĩa là có một người cao hơn ở phía sau), người hiện tại vẫn có thể nhìn thấy người đó, ta tăng biến đếm thêm 1.
- Push người hiện tại vào stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] canSeePersonsCount(int[] heights) {
        int n = heights.length;
        int[] res = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = n - 1; i >= 0; i--) {
            int count = 0;
            while (!stack.isEmpty() && heights[i] > stack.peek()) {
                stack.pop();
                count++;
            }
            if (!stack.isEmpty()) {
                count++;
            }
            res[i] = count;
            stack.push(heights[i]);
        }
        return res;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 12. Asteroid Collision (LeetCode 735)

### Đề bài chi tiết
Cho mảng `asteroids` đại diện cho các tiểu hành tinh trong một hàng. Mỗi tiểu hành tinh có giá trị dương (di chuyển sang phải) hoặc âm (di chuyển sang trái). Nếu hai tiểu hành tinh ngược chiều gặp nhau, va chạm sẽ xảy ra: cái nhỏ hơn phát nổ, nếu bằng nhau thì cả hai cùng nổ. Tìm trạng thái của các tiểu hành tinh sau tất cả các vụ va chạm.

### Phân tích thuật toán
- Sử dụng Stack để lưu các tiểu hành tinh chưa bị phát nổ.
- Nếu gặp tiểu hành tinh dương, luôn push vào stack vì nó đang bay sang phải.
- Nếu gặp tiểu hành tinh âm, sẽ xảy ra va chạm nếu đỉnh stack là dương.
- Vòng lặp: Nếu đỉnh stack dương và nhỏ hơn giá trị tuyệt đối của tiểu hành tinh âm, pop đỉnh stack (đỉnh stack nổ). Tiếp tục kiểm tra va chạm.
- Nếu đỉnh stack bằng giá trị tuyệt đối của nó, pop đỉnh stack và đánh dấu tiểu hành tinh âm cũng nổ.
- Nếu stack rỗng hoặc đỉnh stack âm, tiểu hành tinh âm hiện tại bay an toàn sang trái, push vào stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        Deque<Integer> stack = new ArrayDeque<>();
        
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
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 13. 132 Pattern (LeetCode 456)

### Đề bài chi tiết
Cho một mảng số nguyên `nums`, kiểm tra xem có tồn tại một mẫu 132 hay không. Một mẫu 132 là một dãy con gồm 3 phần tử `nums[i], nums[j], nums[k]` sao cho `i < j < k` và `nums[i] < nums[k] < nums[j]`. Trả về `true` nếu có, ngược lại trả về `false`.

### Phân tích thuật toán
- Duyệt từ phải sang trái, duy trì biến `third` (đóng vai trò `nums[k]`) là phần tử lớn nhất có thể thỏa mãn `nums[k] < nums[j]`. Khởi tạo `third = Integer.MIN_VALUE`.
- Dùng Monotonic Decreasing Stack để lưu trữ các phần tử làm ứng viên cho `nums[j]`.
- Nếu gặp `nums[i] < third`, ta đã tìm thấy mẫu 132 hợp lệ.
- Nếu `nums` hiện tại lớn hơn đỉnh stack, pop đỉnh ra và gán vào `third` (vì phần tử pop ra nằm bên phải và nhỏ hơn `nums` hiện tại). 
- Cuối cùng push `nums` hiện tại vào stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public boolean find132pattern(int[] nums) {
        Deque<Integer> stack = new ArrayDeque<>();
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
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 14. Sliding Window Maximum (LeetCode 239)

### Đề bài chi tiết
Cho mảng số nguyên `nums` và một cửa sổ trượt kích thước `k` di chuyển từ trái sang phải. Ở mỗi vị trí cửa sổ, tìm giá trị lớn nhất trong cửa sổ đó. Trả về mảng chứa các giá trị lớn nhất.

### Phân tích thuật toán
- Sử dụng Monotonic Decreasing Deque (hàng đợi hai đầu) để lưu trữ chỉ số (index) của các phần tử.
- Đỉnh Deque (phía trước) luôn giữ index của phần tử lớn nhất trong cửa sổ hiện tại.
- Khi cửa sổ dịch chuyển, loại bỏ các index ở phía trước Deque nếu chúng đã nằm ngoài cửa sổ (tức là `< i - k + 1`).
- Loại bỏ các index ở phía sau Deque nếu giá trị tại đó nhỏ hơn `nums[i]`, vì chúng không thể là giá trị lớn nhất được nữa.
- Thêm `i` vào phía sau Deque. Nếu cửa sổ đã đủ kích thước (khi `i >= k - 1`), lấy phần tử ở phía trước Deque đưa vào mảng kết quả.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || k <= 0) return new int[0];
        int n = nums.length;
        int[] res = new int[n - k + 1];
        int resIdx = 0;
        
        Deque<Integer> deque = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }
            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }
            deque.offerLast(i);
            
            if (i >= k - 1) {
                res[resIdx++] = nums[deque.peekFirst()];
            }
        }
        return res;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$ vì mỗi index được đưa vào và lấy ra tối đa 1 lần.
- **Space**: $O(K)$ lưu trữ tối đa k phần tử trong Deque.

---

## 15. Shortest Subarray with Sum at Least K (LeetCode 862)

### Đề bài chi tiết
Cho mảng số nguyên `nums` và một số nguyên `k`, tìm chiều dài của mảng con liên tiếp ngắn nhất có tổng các phần tử ít nhất là `k`. Nếu không có mảng con nào thỏa mãn, trả về `-1`.

### Phân tích thuật toán
- Sử dụng mảng Prefix Sum để tính nhanh tổng mảng con. Tổng từ index `i` đến `j` là `prefix[j+1] - prefix[i]`.
- Dùng Monotonic Increasing Deque lưu index của mảng Prefix Sum.
- Khi duyệt, nếu `prefix[i] - prefix[deque.peekFirst()] >= k`, ta cập nhật kết quả. Sau đó `pollFirst()` vì ta đang tìm mảng ngắn nhất, việc giữ lại index đầu sẽ không cho kết quả tốt hơn do `i` tiếp tục tăng.
- Để giữ Deque đơn điệu tăng, ta `pollLast()` các phần tử lớn hơn hoặc bằng `prefix[i]`. Việc này là cần thiết vì mảng con xuất phát từ `prefix[i]` sẽ có tiềm năng độ dài ngắn hơn và điều kiện đạt `k` dễ hơn so với một `prefix` lớn hơn ở phía trước nó.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int shortestSubarray(int[] nums, int k) {
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
        
        int minLen = n + 1;
        Deque<Integer> deque = new ArrayDeque<>();
        
        for (int i = 0; i <= n; i++) {
            while (!deque.isEmpty() && prefix[i] - prefix[deque.peekFirst()] >= k) {
                minLen = Math.min(minLen, i - deque.pollFirst());
            }
            while (!deque.isEmpty() && prefix[i] <= prefix[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }
        
        return minLen <= n ? minLen : -1;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 16. Maximum Width Ramp (LeetCode 962)

### Đề bài chi tiết
Cho mảng số nguyên `nums`. Một đoạn dốc (ramp) `(i, j)` là một đoạn sao cho `i < j` và `nums[i] <= nums[j]`. Chiều rộng của dốc là `j - i`. Tìm chiều rộng lớn nhất của đoạn dốc trong mảng, nếu không có trả về `0`.

### Phân tích thuật toán
- Xây dựng Monotonic Decreasing Stack chứa các index tiềm năng cho vị trí bắt đầu `i`. Ta duyệt từ trái sang phải, nếu mảng giảm dần, đẩy `i` vào stack.
- Duyệt mảng `j` từ phải sang trái. Nếu `nums[j] >= nums[stack.peek()]`, đó là một ramp hợp lệ. Pop khỏi stack, cập nhật `maxWidth = max(maxWidth, j - popped_i)`.
- Việc pop là hoàn toàn an toàn vì ta đang đi từ phải sang trái (nghĩa là `j` lớn nhất có thể). Bất kỳ `j` nào sau đó cũng sẽ cho khoảng cách nhỏ hơn, do đó ta không cần giữ lại `popped_i` nữa.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int maxWidthRamp(int[] nums) {
        Deque<Integer> stack = new ArrayDeque<>();
        int n = nums.length;
        
        for (int i = 0; i < n; i++) {
            if (stack.isEmpty() || nums[stack.peek()] > nums[i]) {
                stack.push(i);
            }
        }
        
        int maxWidth = 0;
        for (int j = n - 1; j >= 0; j--) {
            while (!stack.isEmpty() && nums[j] >= nums[stack.peek()]) {
                maxWidth = Math.max(maxWidth, j - stack.pop());
            }
        }
        
        return maxWidth;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 17. Car Fleet (LeetCode 853)

### Đề bài chi tiết
Có `n` chiếc xe đi cùng một hướng đến điểm đích `target`. Cho mảng vị trí ban đầu `position` và tốc độ `speed` của mỗi xe. Một chiếc xe không thể vượt chiếc khác nhưng có thể bắt kịp và đi cùng tạo thành một đoàn xe chạy với tốc độ của chiếc chậm hơn. Tìm số đoàn xe sẽ đến đích.

### Phân tích thuật toán
- Ghép `position` và `speed` thành mảng đối tượng, sắp xếp theo vị trí giảm dần (xe gần đích đứng trước).
- Tính thời gian đến đích của mỗi xe: `time = (target - position) / speed`.
- Sử dụng biến `maxTime` (hoạt động giống như Monotonic Stack). Duyệt từ xe gần đích nhất về sau: nếu thời gian xe hiện tại lớn hơn `maxTime`, nó không thể bắt kịp xe trước đó, hình thành một đoàn xe mới. Cập nhật `maxTime = time`.
- Nếu thời gian nhỏ hơn hoặc bằng, nó sẽ bắt kịp và sáp nhập vào đoàn xe hiện tại, không thay đổi số lượng đoàn.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        double[][] cars = new double[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = (double)(target - position[i]) / speed[i];
        }
        
        Arrays.sort(cars, (a, b) -> Double.compare(b[0], a[0]));
        
        int fleets = 0;
        double maxTime = 0;
        
        for (int i = 0; i < n; i++) {
            if (cars[i][1] > maxTime) {
                maxTime = cars[i][1];
                fleets++;
            }
        }
        
        return fleets;
    }
}
```
### Độ phức tạp
- **Time**: $O(N \log N)$ do quá trình sắp xếp mảng.
- **Space**: $O(N)$ lưu trữ thông tin xe.

---

## 18. Sum of Subarray Ranges (LeetCode 2104)

### Đề bài chi tiết
Cho mảng số nguyên `nums`. Biên độ (range) của một mảng con là sự chênh lệch giữa phần tử lớn nhất và nhỏ nhất trong đó. Tính tổng biên độ của mọi mảng con của `nums`.

### Phân tích thuật toán
- Tổng biên độ = Tổng của `Max(Subarray)` - Tổng của `Min(Subarray)`.
- Áp dụng kỹ thuật như ở Bài 5 (Sum of Subarray Minimums), dùng Monotonic Stack để tính đóng góp của mỗi phần tử khi nó đóng vai trò là phần tử nhỏ nhất và phần tử lớn nhất trong một mảng con.
- Sử dụng Increasing Stack cho Min và Decreasing Stack cho Max. 
- Tổng kết quả lại trong 2 vòng lặp.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public long subArrayRanges(int[] nums) {
        int n = nums.length;
        long sum = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        
        // Tính tổng Min
        for (int i = 0; i <= n; i++) {
            while (!stack.isEmpty() && (i == n || nums[stack.peek()] > nums[i])) {
                int j = stack.pop();
                int k = stack.isEmpty() ? -1 : stack.peek();
                sum -= (long)nums[j] * (i - j) * (j - k);
            }
            stack.push(i);
        }
        
        stack.clear();
        // Tính tổng Max
        for (int i = 0; i <= n; i++) {
            while (!stack.isEmpty() && (i == n || nums[stack.peek()] < nums[i])) {
                int j = stack.pop();
                int k = stack.isEmpty() ? -1 : stack.peek();
                sum += (long)nums[j] * (i - j) * (j - k);
            }
            stack.push(i);
        }
        
        return sum;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 19. Valid Parentheses (LeetCode 20)

### Đề bài chi tiết
Cho chuỗi `s` chỉ chứa các ký tự `(`, `)`, `{`, `}`, `[` và `]`. Kiểm tra xem chuỗi có hợp lệ hay không (mỗi dấu mở ngoặc phải có dấu đóng ngoặc tương ứng đúng loại và theo đúng thứ tự).

### Phân tích thuật toán
- Sử dụng Stack chuẩn để kiểm tra tính hợp lệ.
- Khi gặp ký tự mở ngoặc, push ký tự ĐÓNG ngoặc tương ứng vào Stack.
- Khi gặp ký tự đóng ngoặc, pop phần tử ra khỏi Stack. Nếu Stack rỗng hoặc phần tử bị pop không trùng với ký tự đang xét, chuỗi không hợp lệ.
- Sau khi duyệt hết, chuỗi chỉ hợp lệ khi Stack rỗng (mọi dấu ngoặc đã được đóng đủ).

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(')');
            } else if (c == '{') {
                stack.push('}');
            } else if (c == '[') {
                stack.push(']');
            } else {
                if (stack.isEmpty() || stack.pop() != c) {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 20. Final Prices With a Special Discount in a Shop (LeetCode 1475)

### Đề bài chi tiết
Cho mảng `prices`. Bạn sẽ nhận được khuyến mãi: nếu mua món đồ thứ `i`, bạn được giảm giá bằng `prices[j]` với `j` là index nhỏ nhất sao cho `j > i` và `prices[j] <= prices[i]`. Nếu không có món nào thỏa mãn, không được giảm. Trả về mảng giá sau khi đã áp dụng giảm giá.

### Phân tích thuật toán
- Đây chính là bài toán tìm Next Smaller or Equal Element (Phần tử nhỏ hơn hoặc bằng tiếp theo).
- Sử dụng Monotonic Increasing Stack lưu trữ chỉ số các phần tử.
- Khi gặp phần tử `prices[i] <= prices[stack.peek()]`, ta có thể giảm giá cho phần tử ở đỉnh stack. Pop phần tử đó ra và trừ đi `prices[i]`.
- Các phần tử còn lại trong mảng giữ nguyên.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] finalPrices(int[] prices) {
        int[] res = prices.clone();
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < prices.length; i++) {
            while (!stack.isEmpty() && prices[i] <= prices[stack.peek()]) {
                int j = stack.pop();
                res[j] -= prices[i];
            }
            stack.push(i);
        }
        
        return res;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 21. Next Greater Node In Linked List (LeetCode 1019)

### Đề bài chi tiết
Cho `head` của một danh sách liên kết (Linked List). Tìm Next Greater Element cho mỗi node. Trả về mảng số nguyên. Nếu không có trả về `0`.

### Phân tích thuật toán
- Vì Linked List không có index và chỉ duyệt được một chiều, ta có thể lưu các giá trị vào một `ArrayList` trước để dễ xử lý.
- Sau khi có mảng, bài toán trở thành tìm Next Greater Element thông thường.
- Sử dụng Monotonic Decreasing Stack để lưu trữ các index và tìm phần tử lớn hơn tiếp theo.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int[] nextLargerNodes(ListNode head) {
        List<Integer> values = new ArrayList<>();
        while (head != null) {
            values.add(head.val);
            head = head.next;
        }
        
        int n = values.size();
        int[] res = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && values.get(stack.peek()) < values.get(i)) {
                res[stack.pop()] = values.get(i);
            }
            stack.push(i);
        }
        return res;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 22. Steps to Make Array Non-decreasing (LeetCode 2289)

### Đề bài chi tiết
Cho mảng `nums`. Ở mỗi bước, xóa bỏ tất cả các phần tử `nums[i]` thỏa mãn `nums[i-1] > nums[i]`. Trả về số bước cần thiết để mảng trở thành không giảm (non-decreasing).

### Phân tích thuật toán
- Sử dụng Monotonic Decreasing Stack, mỗi phần tử trong stack lưu cặp `[giá trị, số bước để bị xóa]`.
- Khi duyệt từ phải sang trái, nếu `nums[i] > stack.peek()[0]`, `nums[i]` sẽ "ăn" (xóa) phần tử đó. 
- Số bước để `nums[i]` ăn hết các phần tử nhỏ hơn nó ở bên phải bằng giá trị lớn nhất trong số: `số bước của phần tử bị ăn` và `tổng số phần tử bị ăn cho đến nay`.
- Cập nhật max bước.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int totalSteps(int[] nums) {
        int n = nums.length;
        int maxSteps = 0;
        // Stack lưu mảng {giá trị, số bước để bị xóa bởi phần tử lớn hơn bên trái}
        Deque<int[]> stack = new ArrayDeque<>();
        
        for (int i = n - 1; i >= 0; i--) {
            int steps = 0;
            while (!stack.isEmpty() && nums[i] > stack.peek()[0]) {
                steps = Math.max(steps + 1, stack.peek()[1]);
                stack.pop();
            }
            maxSteps = Math.max(maxSteps, steps);
            stack.push(new int[]{nums[i], steps});
        }
        
        return maxSteps;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 23. Maximum Score of a Good Subarray (LeetCode 1793)

### Đề bài chi tiết
Cho mảng `nums` và số nguyên `k`. Subarray `(i, j)` được gọi là "good" nếu `i <= k <= j`. Điểm của subarray là `min(nums[i..j]) * (j - i + 1)`. Tìm điểm lớn nhất có thể của một good subarray.

### Phân tích thuật toán
- Bài toán tương tự Largest Rectangle in Histogram (Bài 3) nhưng có thêm điều kiện subarray phải chứa index `k`.
- Sử dụng Monotonic Increasing Stack tương tự bài Histogram, tính điểm số, nhưng chỉ cập nhật `maxScore` đối với những hình chữ nhật thỏa mãn `left <= k <= right`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int maximumScore(int[] nums, int k) {
        int n = nums.length;
        int maxScore = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i <= n; i++) {
            int h = (i == n) ? 0 : nums[i];
            while (!stack.isEmpty() && h < nums[stack.peek()]) {
                int height = nums[stack.pop()];
                int left = stack.isEmpty() ? 0 : stack.peek() + 1;
                int right = i - 1;
                
                if (left <= k && k <= right) {
                    maxScore = Math.max(maxScore, height * (right - left + 1));
                }
            }
            stack.push(i);
        }
        return maxScore;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 24. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit (LeetCode 1438)

### Đề bài chi tiết
Cho mảng `nums` và một `limit`. Tìm độ dài lớn nhất của một mảng con liên tiếp sao cho chênh lệch tuyệt đối giữa phần tử lớn nhất và nhỏ nhất trong mảng con đó không vượt quá `limit`.

### Phân tích thuật toán
- Sử dụng Sliding Window kết hợp với 2 Monotonic Deque.
- Một Deque lưu các phần tử để tìm Max (giảm dần), một Deque lưu các phần tử để tìm Min (tăng dần).
- Khi `Max - Min > limit`, ta phải thu hẹp cửa sổ (tăng `left`) và pop các phần tử ra khỏi hai Deque nếu chúng bằng với `nums[left]`.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int longestSubarray(int[] nums, int limit) {
        Deque<Integer> maxDq = new ArrayDeque<>();
        Deque<Integer> minDq = new ArrayDeque<>();
        int left = 0, maxLength = 0;
        
        for (int right = 0; right < nums.length; right++) {
            while (!maxDq.isEmpty() && maxDq.peekLast() < nums[right]) maxDq.pollLast();
            while (!minDq.isEmpty() && minDq.peekLast() > nums[right]) minDq.pollLast();
            
            maxDq.offerLast(nums[right]);
            minDq.offerLast(nums[right]);
            
            while (maxDq.peekFirst() - minDq.peekFirst() > limit) {
                if (maxDq.peekFirst() == nums[left]) maxDq.pollFirst();
                if (minDq.peekFirst() == nums[left]) minDq.pollFirst();
                left++;
            }
            
            maxLength = Math.max(maxLength, right - left + 1);
        }
        
        return maxLength;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 25. Construct Binary Search Tree from Preorder Traversal (LeetCode 1008)

### Đề bài chi tiết
Cho mảng `preorder` biểu diễn kết quả duyệt Preorder của một Cây nhị phân tìm kiếm (BST). Xây dựng lại cây BST.

### Phân tích thuật toán
- Duyệt Preorder là `Root -> Left -> Right`.
- Sử dụng Monotonic Decreasing Stack để lưu các node.
- Nếu phần tử hiện tại nhỏ hơn đỉnh stack, nó là con trái của đỉnh stack -> gắn vào nhánh trái, push vào stack.
- Nếu phần tử hiện tại lớn hơn đỉnh stack, ta pop các phần tử nhỏ hơn ra. Phần tử cuối cùng bị pop sẽ là cha (parent) của phần tử hiện tại, và phần tử hiện tại là con phải của cha đó -> gắn vào nhánh phải, push vào stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public TreeNode bstFromPreorder(int[] preorder) {
        if (preorder == null || preorder.length == 0) return null;
        
        TreeNode root = new TreeNode(preorder[0]);
        Deque<TreeNode> stack = new ArrayDeque<>();
        stack.push(root);
        
        for (int i = 1; i < preorder.length; i++) {
            TreeNode child = new TreeNode(preorder[i]);
            if (child.val < stack.peek().val) {
                stack.peek().left = child;
            } else {
                TreeNode parent = stack.peek();
                while (!stack.isEmpty() && stack.peek().val < child.val) {
                    parent = stack.pop();
                }
                parent.right = child;
            }
            stack.push(child);
        }
        return root;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 26. Evaluate Reverse Polish Notation (LeetCode 150)

### Đề bài chi tiết
Đánh giá giá trị của một biểu thức toán học dạng Reverse Polish Notation (Ký pháp Ba Lan ngược). Các phép toán hợp lệ gồm `+`, `-`, `*`, `/`.

### Phân tích thuật toán
- Đây là bài toán Stack kinh điển. Duyệt các token, nếu là số thì push vào stack.
- Nếu là toán tử, pop 2 toán hạng từ stack, thực hiện phép tính (chú ý thứ tự toán hạng: toán hạng 2 pop trước thao tác với toán hạng 1 pop sau), rồi push kết quả ngược lại stack.
- Kết quả cuối cùng là phần tử duy nhất còn lại trong stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (String token : tokens) {
            switch (token) {
                case "+":
                    stack.push(stack.pop() + stack.pop());
                    break;
                case "-":
                    int b = stack.pop(), a = stack.pop();
                    stack.push(a - b);
                    break;
                case "*":
                    stack.push(stack.pop() * stack.pop());
                    break;
                case "/":
                    int d = stack.pop(), c = stack.pop();
                    stack.push(c / d);
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
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 27. Max Chunks To Make Sorted II (LeetCode 768)

### Đề bài chi tiết
Cho mảng số nguyên `arr` (có thể chứa các phần tử trùng lặp). Ta muốn chia mảng thành nhiều đoạn (chunks) nhất có thể sao cho nếu ta sắp xếp từng đoạn rồi nối chúng lại, kết quả sẽ là một mảng được sắp xếp tăng dần. Trả về số đoạn lớn nhất.

### Phân tích thuật toán
- Sử dụng Monotonic Increasing Stack lưu `max` của mỗi chunk.
- Nếu phần tử hiện tại lớn hơn hoặc bằng đỉnh stack (hoặc stack rỗng), nó có thể bắt đầu một chunk mới -> push vào stack.
- Nếu phần tử hiện tại nhỏ hơn đỉnh stack, nó không thể nằm trong chunk mới mà phải gộp vào chunk trước đó. Lấy `max = stack.pop()`, sau đó tiếp tục pop các giá trị trong stack mà lớn hơn phần tử hiện tại. Cuối cùng push lại `max` vào stack đại diện cho việc gộp các chunk lại với giá trị lớn nhất.
- Kích thước stack cuối cùng chính là số lượng chunks.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int maxChunksToSorted(int[] arr) {
        Deque<Integer> stack = new ArrayDeque<>();
        for (int num : arr) {
            if (stack.isEmpty() || stack.peek() <= num) {
                stack.push(num);
            } else {
                int max = stack.pop();
                while (!stack.isEmpty() && stack.peek() > num) {
                    stack.pop();
                }
                stack.push(max);
            }
        }
        return stack.size();
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 28. Smallest K-Length Subsequence With Occurrences of a Letter (LeetCode 2030)

### Đề bài chi tiết
Cho chuỗi `s`, số nguyên `k`, ký tự `letter` và số `repetition`. Tìm chuỗi con (subsequence) có độ dài `k` nhỏ nhất theo thứ tự từ điển, trong đó ký tự `letter` xuất hiện ít nhất `repetition` lần.

### Phân tích thuật toán
- Đếm tổng số lần xuất hiện của `letter` trong chuỗi để biết còn lại bao nhiêu ký tự `letter` phía sau.
- Dùng Monotonic Increasing Stack. Khi muốn pop đỉnh stack (để lấy ký tự nhỏ hơn vào thay thế), phải đảm bảo số lượng `letter` đã lấy và còn lại đủ `repetition`. Đồng thời, số lượng phần tử hiện tại trong stack cộng số phần tử còn lại phải `>= k`.
- Nếu stack nhỏ hơn `k`, ta xem xét thêm ký tự hiện tại vào.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public String smallestSubsequence(String s, int k, char letter, int repetition) {
        int n = s.length();
        int totalLetters = 0;
        for (char c : s.toCharArray()) if (c == letter) totalLetters++;
        
        Deque<Character> stack = new ArrayDeque<>();
        int countInStack = 0;
        
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            
            while (!stack.isEmpty() && stack.peek() > c 
                   && stack.size() + n - i - 1 >= k 
                   && (stack.peek() != letter || countInStack + totalLetters - 1 >= repetition)) {
                if (stack.pop() == letter) countInStack--;
            }
            
            if (stack.size() < k) {
                if (c == letter) {
                    stack.push(c);
                    countInStack++;
                } else if (k - stack.size() > repetition - countInStack) {
                    stack.push(c);
                }
            }
            
            if (c == letter) totalLetters--;
        }
        
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) sb.append(stack.pollLast());
        return sb.toString();
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(K)$

---

## 29. Minimum Cost Tree From Leaf Values (LeetCode 1130)

### Đề bài chi tiết
Cho mảng `arr` chứa các giá trị của các node lá trong một cây nhị phân. Mỗi node không phải lá có giá trị bằng tích của node lá lớn nhất ở cây con trái và cây con phải của nó. Tổng của tất cả các node không phải lá được gọi là cost của cây. Tìm cost nhỏ nhất có thể.

### Phân tích thuật toán
- Để giảm thiểu cost, ta nên ưu tiên nhân những phần tử NHỎ NHẤT trước với hàng xóm NHỎ NHẤT kề nó.
- Dùng Monotonic Decreasing Stack. Nếu phần tử hiện tại lớn hơn đỉnh stack, pop đỉnh ra. Phần tử vừa pop sẽ được nhân với giá trị nhỏ hơn giữa hàng xóm bên trái của nó (hiện là đỉnh stack) và hàng xóm bên phải (phần tử hiện tại).
- Cộng tích vào tổng. Cuối cùng, nhân chập các phần tử còn lại trong stack.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
    public int mctFromLeafValues(int[] arr) {
        int cost = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(Integer.MAX_VALUE);
        
        for (int num : arr) {
            while (stack.peek() <= num) {
                int drop = stack.pop();
                cost += drop * Math.min(stack.peek(), num);
            }
            stack.push(num);
        }
        
        while (stack.size() > 2) {
            cost += stack.pop() * stack.peek();
        }
        
        return cost;
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$

---

## 30. Remove K Digits (LeetCode 402)

### Đề bài chi tiết
Cho chuỗi `num` biểu diễn một số nguyên không âm và số nguyên `k`. Xóa `k` chữ số khỏi `num` sao cho số nguyên mới tạo thành là nhỏ nhất có thể.

### Phân tích thuật toán
- Để số nhỏ nhất, các chữ số nhỏ cần nằm ở đầu ưu tiên từ trái sang phải.
- Sử dụng Monotonic Increasing Stack. Khi gặp số nhỏ hơn đỉnh stack, pop đỉnh ra (tốn 1 lần xóa, `k--`).
- Sau khi duyệt hết, nếu `k > 0`, ta pop từ cuối stack cho đến khi `k=0`.
- Ghép các ký tự lại và nhớ loại bỏ các số '0' ở đầu.

### Mã nguồn Java
```java
import java.util.*;

public class Solution {
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
            sb.append(stack.pollLast());
        }
        
        // Loại bỏ các chữ số '0' ở đầu
        while (sb.length() > 0 && sb.charAt(0) == '0') {
            sb.deleteCharAt(0);
        }
        
        return sb.length() == 0 ? "0" : sb.toString();
    }
}
```
### Độ phức tạp
- **Time**: $O(N)$
- **Space**: $O(N)$
