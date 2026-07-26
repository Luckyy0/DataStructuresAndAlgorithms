# Bài Tập Hàng Đợi Vòng (Circular Queue) & Mảng Vòng (Circular Array)

Tài liệu này bao gồm 30 bài tập về cấu trúc dữ liệu hàng đợi vòng (Circular Queue) và các bài toán mảng vòng (Circular Array). 10 bài đầu tiên được cung cấp đầy đủ đề bài, phân tích, mã nguồn Java và phân tích độ phức tạp. 20 bài còn lại là phần tóm tắt để tự luyện tập.

---

## 1. Design Circular Queue
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu Hàng đợi vòng (Circular Queue) hỗ trợ các thao tác sau:
- `MyCircularQueue(k)`: Khởi tạo hàng đợi với kích thước `k`.
- `Front()`: Lấy phần tử ở đầu hàng đợi. Trả về -1 nếu rỗng.
- `Rear()`: Lấy phần tử ở cuối hàng đợi. Trả về -1 nếu rỗng.
- `enQueue(value)`: Thêm một phần tử vào hàng đợi. Trả về true nếu thành công.
- `deQueue()`: Xóa phần tử khỏi hàng đợi. Trả về true nếu thành công.
- `isEmpty()`: Kiểm tra hàng đợi có rỗng không.
- `isFull()`: Kiểm tra hàng đợi có đầy không.

**Phân tích thuật toán:**
Sử dụng một mảng có kích thước `k`. Ta dùng 2 biến `head` và `count` (hoặc `front` và `size`). Con trỏ đuôi `tail` có thể tính bằng `(head + count - 1) % k`. Cách này rất trực quan và tránh lỗi xử lý wrap-around phức tạp khi cập nhật nhiều con trỏ cùng lúc.

**Mã nguồn Java:**
```java
class MyCircularQueue {
    private int[] queue;
    private int headIndex;
    private int count;
    private int capacity;

    public MyCircularQueue(int k) {
        this.capacity = k;
        this.queue = new int[k];
        this.headIndex = 0;
        this.count = 0;
    }
    
    public boolean enQueue(int value) {
        if (this.count == this.capacity)
            return false;
        int tailIndex = (this.headIndex + this.count) % this.capacity;
        this.queue[tailIndex] = value;
        this.count += 1;
        return true;
    }
    
    public boolean deQueue() {
        if (this.count == 0)
            return false;
        this.headIndex = (this.headIndex + 1) % this.capacity;
        this.count -= 1;
        return true;
    }
    
    public int Front() {
        if (this.count == 0) return -1;
        return this.queue[this.headIndex];
    }
    
    public int Rear() {
        if (this.count == 0) return -1;
        int tailIndex = (this.headIndex + this.count - 1) % this.capacity;
        return this.queue[tailIndex];
    }
    
    public boolean isEmpty() { return (this.count == 0); }
    public boolean isFull() { return (this.count == this.capacity); }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(1)$ cho mỗi thao tác.
- Không gian (Space Complexity): $O(k)$ với `k` là kích thước mảng.

---

## 2. Design Circular Deque
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu Hàng đợi hai đầu vòng (Circular Deque) hỗ trợ:
- `insertFront()`: Thêm phần tử vào đầu.
- `insertLast()`: Thêm phần tử vào cuối.
- `deleteFront()`: Xóa phần tử đầu.
- `deleteLast()`: Xóa phần tử cuối.
- `getFront()`, `getRear()`, `isEmpty()`, `isFull()`.

**Phân tích thuật toán:**
Mở rộng từ Circular Queue, Circular Deque yêu cầu di chuyển lùi con trỏ. Khi cần chèn vào đầu hoặc xoá ở cuối, vị trí mới tính bằng công thức `(index - 1 + capacity) % capacity` để tránh bị số âm.

**Mã nguồn Java:**
```java
class MyCircularDeque {
    private int[] deque;
    private int front, rear, size, capacity;

    public MyCircularDeque(int k) {
        capacity = k;
        deque = new int[k];
        front = 0; rear = 0; size = 0;
    }
    
    public boolean insertFront(int value) {
        if (isFull()) return false;
        front = (front - 1 + capacity) % capacity;
        deque[front] = value;
        size++;
        return true;
    }
    
    public boolean insertLast(int value) {
        if (isFull()) return false;
        deque[rear] = value;
        rear = (rear + 1) % capacity;
        size++;
        return true;
    }
    
    public boolean deleteFront() {
        if (isEmpty()) return false;
        front = (front + 1) % capacity;
        size--;
        return true;
    }
    
    public boolean deleteLast() {
        if (isEmpty()) return false;
        rear = (rear - 1 + capacity) % capacity;
        size--;
        return true;
    }
    
    public int getFront() {
        return isEmpty() ? -1 : deque[front];
    }
    
    public int getRear() {
        return isEmpty() ? -1 : deque[(rear - 1 + capacity) % capacity];
    }
    
    public boolean isEmpty() { return size == 0; }
    public boolean isFull() { return size == capacity; }
}
```

**Độ phức tạp:**
- Thời gian: $O(1)$ cho tất cả thao tác.
- Không gian: $O(k)$.

---

## 3. Find the Winner of the Circular Game (Josephus Problem)
**Đề bài chi tiết:**
Có `n` người đứng thành vòng tròn từ 1 đến `n`. Đếm `k` người và loại người thứ `k`. Lặp lại cho đến khi còn 1 người. Trả về người chiến thắng.

**Phân tích thuật toán:**
Dùng Queue mô phỏng. Bỏ qua `k-1` người đầu tiên bằng cách `dequeue` rồi lại `enqueue` họ xuống cuối. Người thứ `k` sẽ bị `dequeue` đi hẳn. Lặp lại quá trình cho đến khi `queue.size() == 1`.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public int findTheWinner(int n, int k) {
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 1; i <= n; i++) queue.offer(i);
        
        while (queue.size() > 1) {
            for (int i = 1; i < k; i++) {
                queue.offer(queue.poll());
            }
            queue.poll(); // Xoá người bị loại
        }
        return queue.peek();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n \times k)$.
- Không gian: $O(n)$ cho Queue. (Có thể dùng công thức đệ quy để giải với $O(n)$ time và $O(1)$ space).

---

## 4. Defuse the Bomb
**Đề bài chi tiết:**
Bạn có mảng vòng `code` (n phần tử). Chìa khóa `k`:
- Nếu `k > 0`, thay `i` bằng tổng của `k` số tiếp theo.
- Nếu `k < 0`, thay bằng tổng của `|k|` số trước đó.
- Nếu `k == 0`, thay bằng 0.

**Phân tích thuật toán:**
Sử dụng Cửa sổ trượt (Sliding Window) trên mảng vòng. Khởi tạo tổng của `k` phần tử thích hợp. Di chuyển cửa sổ bằng cách trừ đi phần tử bị bỏ lại và cộng thêm phần tử mới tới, sử dụng toán tử `%` để lấy chỉ số mảng vòng.

**Mã nguồn Java:**
```java
class Solution {
    public int[] decrypt(int[] code, int k) {
        int n = code.length;
        int[] res = new int[n];
        if (k == 0) return res;
        
        int start = 1, end = k, sum = 0;
        if (k < 0) {
            start = n - Math.abs(k);
            end = n - 1;
        }
        
        for (int i = start; i <= end; i++) sum += code[i % n];
        
        for (int i = 0; i < n; i++) {
            res[i] = sum;
            sum -= code[start % n];
            start++;
            end++;
            sum += code[end % n];
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(n)$.

---

## 5. Circular Array Loop
**Đề bài chi tiết:**
Trong mảng vòng, di chuyển `nums[i]` bước. Tìm xem có vòng lặp (cycle) nào có độ dài > 1 và các phần tử trong vòng lặp đều có cùng dấu (chỉ đi tới hoặc chỉ đi lùi) hay không.

**Phân tích thuật toán:**
Sử dụng thuật toán "rùa và thỏ" (Slow & Fast Pointers). Ở mỗi bước, rùa nhảy 1 bước, thỏ nhảy 2 bước. Phải đảm bảo hướng di chuyển (dấu) không thay đổi. Nếu rùa gặp thỏ, vòng lặp tồn tại. Sau đó đánh dấu các phần tử đã duyệt qua thành 0 để tránh tính lại.

**Mã nguồn Java:**
```java
class Solution {
    public boolean circularArrayLoop(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            if (nums[i] == 0) continue;
            int slow = i, fast = next(nums, i);
            while (nums[fast] * nums[i] > 0 && nums[next(nums, fast)] * nums[i] > 0) {
                if (slow == fast) {
                    if (slow == next(nums, slow)) break;
                    return true;
                }
                slow = next(nums, slow);
                fast = next(nums, next(nums, fast));
            }
            int curr = i;
            while (nums[curr] * nums[i] > 0) {
                int next = next(nums, curr);
                nums[curr] = 0;
                curr = next;
            }
        }
        return false;
    }
    
    private int next(int[] nums, int i) {
        int n = nums.length;
        return (((i + nums[i]) % n) + n) % n;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

---

## 6. Maximum Sum Circular Subarray
**Đề bài chi tiết:**
Tìm mảng con liên tiếp có tổng lớn nhất trong mảng vòng.

**Phân tích thuật toán:**
Tính tổng tối đa (`maxSum`) của mảng con không nối vòng (dùng Kadane). Tính tổng mảng. Tính tổng tối thiểu (`minSum`) của mảng con. Nếu mảng con nối vòng, nó bằng Tổng mảng - `minSum`. Chọn giá trị lớn nhất giữa hai đáp án. Lưu ý xử lý trường hợp mọi số đều âm.

**Mã nguồn Java:**
```java
class Solution {
    public int maxSubarraySumCircular(int[] nums) {
        int totalSum = 0, maxEndingHere = 0, maxSum = Integer.MIN_VALUE;
        int minEndingHere = 0, minSum = Integer.MAX_VALUE;
        
        for (int num : nums) {
            totalSum += num;
            maxEndingHere = Math.max(maxEndingHere + num, num);
            maxSum = Math.max(maxSum, maxEndingHere);
            minEndingHere = Math.min(minEndingHere + num, num);
            minSum = Math.min(minSum, minEndingHere);
        }
        
        return maxSum > 0 ? Math.max(maxSum, totalSum - minSum) : maxSum;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

---

## 7. Next Greater Element II
**Đề bài chi tiết:**
Cho mảng vòng, tìm phần tử lớn hơn tiếp theo cho mọi phần tử trong mảng.

**Phân tích thuật toán:**
Dùng Ngăn xếp đơn điệu (Monotonic Stack) giảm dần. Do là mảng vòng, ta duyệt mảng 2 lần (độ dài `2 * n`) và lấy index giả lập bằng `i % n`. Push index vào ngăn xếp nếu chưa tìm được số lớn hơn tiếp theo.

**Mã nguồn Java:**
```java
import java.util.Stack;
import java.util.Arrays;

class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Arrays.fill(res, -1);
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < 2 * n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i % n]) {
                res[stack.pop()] = nums[i % n];
            }
            if (i < n) stack.push(i);
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(n)$.

---

## 8. Gas Station
**Đề bài chi tiết:**
Có trạm xăng hình vòng tròn. Tìm điểm khởi hành để đi hết một vòng mà không bị cạn xăng, biết mức xăng tại trạm và chi phí tới trạm tiếp theo. Trả về -1 nếu không thể.

**Phân tích thuật toán:**
Cộng dồn lượng xăng dư hiện tại. Nếu lượng dư `< 0`, đoạn đường vừa qua không hợp lệ, đặt lại trạm xuất phát tiếp theo. Nếu tổng xăng của mọi trạm >= tổng chi phí, chắc chắn có đường đi.

**Mã nguồn Java:**
```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int totalTank = 0, currentTank = 0;
        int startingStation = 0;
        
        for (int i = 0; i < gas.length; i++) {
            totalTank += gas[i] - cost[i];
            currentTank += gas[i] - cost[i];
            
            if (currentTank < 0) {
                startingStation = i + 1;
                currentTank = 0;
            }
        }
        return totalTank >= 0 ? startingStation : -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

---

## 9. Broken Calculator
**Đề bài chi tiết:**
Bắt đầu từ `startValue`. Máy tính chỉ có thể: nhân đôi số hiện tại (`*2`) hoặc trừ đi 1 (`-1`). Tìm số bước tối thiểu để biến nó thành `target`.

**Phân tích thuật toán:**
Dùng chiến lược mô phỏng ngược. Thay vì từ `start` đến `target` làm không gian trạng thái Queue bùng nổ, ta đi ngược từ `target` về `start`: Nếu số chẵn thì chia 2, số lẻ thì cộng 1. Khi `target <= start`, đáp án là cộng nốt phần chênh lệch.

**Mã nguồn Java:**
```java
class Solution {
    public int brokenCalc(int startValue, int target) {
        int ans = 0;
        while (target > startValue) {
            ans++;
            if (target % 2 == 1) {
                target++;
            } else {
                target /= 2;
            }
        }
        return ans + startValue - target;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(\log(\text{target}/\text{startValue}))$.
- Không gian: $O(1)$.

---

## 10. Rotating the Box
**Đề bài chi tiết:**
Mô phỏng trọng lực trên hộp 2D bằng cách xoay $90^\circ$. Đá `#` rơi xuống, chướng ngại vật `*` đứng yên, khoảng trống `.`.

**Phân tích thuật toán:**
Cho đá rơi tự do trên từng hàng (chuyển về bên phải) bằng cách dùng con trỏ giữ vị trí trống thấp nhất. Sau đó xoay ma trận đi 90 độ.

**Mã nguồn Java:**
```java
class Solution {
    public char[][] rotateTheBox(char[][] box) {
        int m = box.length, n = box[0].length;
        
        // Mô phỏng trọng lực
        for (int i = 0; i < m; i++) {
            int empty = n - 1;
            for (int j = n - 1; j >= 0; j--) {
                if (box[i][j] == '*') {
                    empty = j - 1;
                } else if (box[i][j] == '#') {
                    box[i][j] = '.';
                    box[i][empty] = '#';
                    empty--;
                }
            }
        }
        
        // Xoay 90 độ
        char[][] res = new char[n][m];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                res[j][m - 1 - i] = box[i][j];
            }
        }
        return res;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(m \times n)$.
- Không gian: $O(m \times n)$.

---

## 11. Design Front Middle Back Queue
**Đề bài chi tiết:**
Thiết kế một hàng đợi hỗ trợ thêm và xóa phần tử ở đầu, giữa và cuối hàng đợi. Cụ thể cần triển khai các hàm:
- `pushFront(val)`: Thêm `val` vào đầu.
- `pushMiddle(val)`: Thêm `val` vào chính giữa hàng đợi.
- `pushBack(val)`: Thêm `val` vào cuối.
- `popFront()`: Xóa phần tử ở đầu và trả về nó. Nếu rỗng trả về -1.
- `popMiddle()`: Xóa phần tử ở giữa và trả về nó. Nếu rỗng trả về -1.
- `popBack()`: Xóa phần tử ở cuối và trả về nó. Nếu rỗng trả về -1.

**Phân tích thuật toán:**
Để các thao tác hoạt động hiệu quả, ta sử dụng 2 Deque (hoặc LinkedList) để đại diện cho nửa đầu (`left`) và nửa sau (`right`) của hàng đợi. Yêu cầu là luôn giữ cho chúng cân bằng: `left.size() == right.size()` hoặc `left.size() == right.size() - 1`. Khi push/pop ở Middle, thao tác được thực hiện ở đuôi của `left` hoặc đầu của `right`. Hàm `balance()` giúp đảm bảo sự cân bằng này sau mỗi thao tác thêm/xoá.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Deque;

class FrontMiddleBackQueue {
    Deque<Integer> left, right;

    public FrontMiddleBackQueue() {
        left = new LinkedList<>();
        right = new LinkedList<>();
    }
    
    private void balance() {
        if (left.size() > right.size()) {
            right.addFirst(left.pollLast());
        } else if (right.size() > left.size() + 1) {
            left.addLast(right.pollFirst());
        }
    }
    
    public void pushFront(int val) {
        left.addFirst(val);
        balance();
    }
    
    public void pushMiddle(int val) {
        if (left.size() < right.size()) {
            left.addLast(val);
        } else {
            right.addFirst(val);
        }
    }
    
    public void pushBack(int val) {
        right.addLast(val);
        balance();
    }
    
    public int popFront() {
        if (right.isEmpty()) return -1;
        int val = left.isEmpty() ? right.pollFirst() : left.pollFirst();
        balance();
        return val;
    }
    
    public int popMiddle() {
        if (right.isEmpty()) return -1;
        int val = left.size() == right.size() ? left.pollLast() : right.pollFirst();
        balance();
        return val;
    }
    
    public int popBack() {
        if (right.isEmpty()) return -1;
        int val = right.pollLast();
        balance();
        return val;
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(1)$ cho mỗi thao tác do LinkedList trong Java hỗ trợ truy xuất, thêm, xoá ở hai đầu với thời gian hằng số.
- Không gian (Space Complexity): $O(n)$ với `n` là số lượng phần tử có trong Queue.

---

## 12. Number of Students Unable to Eat Lunch
**Đề bài chi tiết:**
Có một hàng đợi sinh viên (`students`) và một ngăn xếp bánh mì (`sandwiches`). Cả sinh viên và bánh mì đều có 2 loại: 0 (bánh mì tròn) và 1 (bánh mì vuông).
- Nếu sinh viên ở đầu hàng thích loại bánh mì ở đỉnh ngăn xếp, họ sẽ lấy nó và rời khỏi hàng.
- Nếu không, họ sẽ không lấy bánh mì và quay về cuối hàng đợi.
Quá trình này lặp lại cho đến khi không còn sinh viên nào trong hàng muốn lấy bánh mì trên đỉnh. Tính số lượng sinh viên không thể ăn trưa.

**Phân tích thuật toán:**
Không cần thiết phải dùng Queue mô phỏng trực tiếp, do số lần quay về cuối hàng đợi có thể khiến thời gian chạy lớn. Bản chất vấn đề là sinh viên có thể tự do di chuyển trong Queue, nên thứ tự trong Queue không quan trọng, chỉ cần quan tâm số lượng sinh viên thích từng loại. Ta duyệt từ trên xuống dưới mảng `sandwiches`, nếu còn sinh viên thích loại bánh này thì trừ đi 1. Nếu đến một chiếc bánh mà lượng sinh viên thích bánh đó bằng 0, tất cả sinh viên còn lại trong hàng (dù thích bánh gì) sẽ bị mắc kẹt.

**Mã nguồn Java:**
```java
class Solution {
    public int countStudents(int[] students, int[] sandwiches) {
        int[] counts = new int[2];
        for (int student : students) {
            counts[student]++;
        }
        
        for (int i = 0; i < sandwiches.length; i++) {
            if (counts[sandwiches[i]] > 0) {
                counts[sandwiches[i]]--;
            } else {
                return sandwiches.length - i; // Số sinh viên còn lại
            }
        }
        return 0;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ với `n` là số lượng sinh viên (hoặc bánh mì).
- Không gian: $O(1)$ (Mảng `counts` luôn có kích thước là 2).

---

## 13. Time Needed to Buy Tickets
**Đề bài chi tiết:**
Có `n` người đứng trong hàng đợi mua vé. Mảng `tickets` cho biết người thứ `i` muốn mua `tickets[i]` vé. Mất 1 giây để mua 1 vé. Sau khi mua, nếu còn muốn mua thêm, người đó sẽ quay lại cuối hàng đợi. Trả về tổng số giây để người ở vị trí `k` (chỉ số 0) mua xong tất cả vé của mình.

**Phân tích thuật toán:**
Thay vì dùng vòng lặp mô phỏng chi tiết, ta phân tích số vé mà mỗi người mua được trong khoảng thời gian người `k` đang mua.
- Đối với những người đứng trước `k` (và chính `k`): Họ sẽ mua được tối đa `tickets[k]` vé trước khi người `k` hoàn tất.
- Đối với những người đứng sau `k`: Vì người `k` kết thúc việc mua tại lượt thứ `tickets[k]`, những người đứng sau chỉ kịp mua tối đa `tickets[k] - 1` vé.

**Mã nguồn Java:**
```java
class Solution {
    public int timeRequiredToBuy(int[] tickets, int k) {
        int time = 0;
        for (int i = 0; i < tickets.length; i++) {
            if (i <= k) {
                time += Math.min(tickets[i], tickets[k]);
            } else {
                time += Math.min(tickets[i], tickets[k] - 1);
            }
        }
        return time;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

---

## 14. Task Scheduler
**Đề bài chi tiết:**
Cho mảng ký tự `tasks` chứa các tác vụ của CPU, và số nguyên `n` đại diện cho thời gian chờ tối thiểu (cooldown) giữa hai tác vụ giống nhau. Có thể thực hiện các tác vụ theo bất kỳ thứ tự nào. Tìm thời gian ít nhất để CPU hoàn thành toàn bộ tác vụ.

**Phân tích thuật toán:**
Ý tưởng cốt lõi là phân bổ các tác vụ có tần suất xuất hiện nhiều nhất trước. Nếu gọi `maxFreq` là số lần xuất hiện nhiều nhất của một tác vụ, chúng ta sẽ cần `maxFreq - 1` khoảng trống, mỗi khoảng cách nhau `n` thời gian. Tổng thời gian cơ sở là `(maxFreq - 1) * (n + 1)`. Cộng thêm số lượng tác vụ có cùng `maxFreq` đó vào cuối để được kết quả dự tính. Trong trường hợp có quá nhiều tác vụ lẻ tẻ lấp đầy mọi khoảng trống làm thời gian chạy vượt qua số trên, đáp án thực tế sẽ chính là tổng số lượng các tác vụ.

**Mã nguồn Java:**
```java
import java.util.Arrays;

class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] freq = new int[26];
        for (char task : tasks) {
            freq[task - 'A']++;
        }
        
        Arrays.sort(freq);
        int maxFreq = freq[25];
        int maxCount = 0;
        
        for (int i = 25; i >= 0; i--) {
            if (freq[i] == maxFreq) {
                maxCount++;
            } else {
                break;
            }
        }
        
        int time = (maxFreq - 1) * (n + 1) + maxCount;
        return Math.max(time, tasks.length);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N)$ với $N$ là tổng số lượng tác vụ (bước sắp xếp tốn $O(1)$ do mảng chỉ có kích thước 26).
- Không gian: $O(1)$.

---

## 15. Design Hit Counter
**Đề bài chi tiết:**
Thiết kế `HitCounter` để đếm số lượt truy cập (hit) trong 5 phút qua (300 giây).
- `hit(timestamp)`: Ghi nhận 1 hit ở thời điểm `timestamp`.
- `getHits(timestamp)`: Trả về tổng số hit trong vòng 300 giây qua so với `timestamp`. 
Các `timestamp` được cung cấp sẽ tăng dần.

**Phân tích thuật toán:**
Có thể sử dụng ý tưởng Mảng vòng (Circular Array) với kích thước 300. Cần 2 mảng `times` lưu thời gian xảy ra hit và `hits` lưu số lần hit trong thời điểm đó. Khi có request, tính `index = timestamp % 300`. Nếu `times[index]` chưa phải là thời điểm hiện tại, có nghĩa là dữ liệu cũ đã vượt qua 300 giây, ta sẽ xoá (ghi đè) nó với thời điểm hiện tại và cài `hits[index] = 1`. Nếu cùng thời điểm, chỉ cần tăng số hit lên.

**Mã nguồn Java:**
```java
class HitCounter {
    private int[] times;
    private int[] hits;

    public HitCounter() {
        times = new int[300];
        hits = new int[300];
    }
    
    public void hit(int timestamp) {
        int index = timestamp % 300;
        if (times[index] != timestamp) {
            times[index] = timestamp;
            hits[index] = 1;
        } else {
            hits[index]++;
        }
    }
    
    public int getHits(int timestamp) {
        int total = 0;
        for (int i = 0; i < 300; i++) {
            if (timestamp - times[i] < 300) {
                total += hits[i];
            }
        }
        return total;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(1)$ cho `hit`, $O(300) = O(1)$ cho `getHits`.
- Không gian: $O(300) = O(1)$ vì chỉ lưu 2 mảng kích thước cố định.

---

## 16. Product of the Last K Numbers
**Đề bài chi tiết:**
Thiết kế cấu trúc dữ liệu tính tích của `k` số cuối cùng.
- `add(int num)`: Thêm số vào cuối chuỗi.
- `getProduct(int k)`: Trả về tích `k` số cuối cùng.

**Phân tích thuật toán:**
Để tối ưu `getProduct` đạt thời gian $O(1)$, ta sử dụng mảng Tích luỹ kế (Prefix Product). Thay vì thêm các số trực tiếp, ta lưu tích của mọi số trước đó. Khi tính tích `k` phần tử cuối, ta chỉ cần phép chia tích tại chỉ số cuối cho phần tử tại `n - 1 - k`. Một trường hợp đặc biệt là khi `num = 0`, tích các đoạn liên quan đều sẽ trở thành 0. Do đó ta khởi tạo lại mảng từ đầu mỗi khi gặp số 0. Nếu `k` lớn hơn độ dài mảng (nghĩa là nó bao gồm số 0 vừa loại bỏ), kết quả sẽ là 0.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class ProductOfNumbers {
    private List<Integer> prefixProduct;

    public ProductOfNumbers() {
        prefixProduct = new ArrayList<>();
        prefixProduct.add(1);
    }
    
    public void add(int num) {
        if (num == 0) {
            prefixProduct = new ArrayList<>();
            prefixProduct.add(1);
        } else {
            int last = prefixProduct.get(prefixProduct.size() - 1);
            prefixProduct.add(last * num);
        }
    }
    
    public int getProduct(int k) {
        int n = prefixProduct.size();
        if (k >= n) return 0;
        return prefixProduct.get(n - 1) / prefixProduct.get(n - 1 - k);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(1)$ cho mỗi thao tác.
- Không gian: $O(N)$ dùng để lưu mảng tích, với $N$ là số thao tác chèn.

---

## 17. Moving Average from Data Stream
**Đề bài chi tiết:**
Thiết kế cấu trúc `MovingAverage` để tính giá trị trung bình cộng của `size` phần tử gần nhất được nạp vào.
- `MovingAverage(int size)`: Khởi tạo với kích thước cửa sổ nhất định.
- `next(int val)`: Nạp thêm số và trả về giá trị trung bình tính đến hiện tại.

**Phân tích thuật toán:**
Đây là ứng dụng kinh điển của Hàng đợi vòng (Circular Queue). Sử dụng một mảng có kích thước `size` và các con trỏ. Khi mảng đã đầy, số mới nạp vào sẽ thay thế (ghi đè) lên vị trí cũ nhất. Chúng ta cần một biến `sum` để giữ tổng hiện tại. Mỗi khi thêm số mới, trừ đi giá trị cũ bị đẩy ra (nếu có) và cộng giá trị mới vào `sum`.

**Mã nguồn Java:**
```java
class MovingAverage {
    private int[] window;
    private int size;
    private int count;
    private int head;
    private double sum;

    public MovingAverage(int size) {
        this.window = new int[size];
        this.size = size;
        this.count = 0;
        this.head = 0;
        this.sum = 0.0;
    }
    
    public double next(int val) {
        count++;
        int tail = (head + 1) % size;
        sum = sum - window[tail] + val;
        window[tail] = val;
        head = tail;
        
        return sum / Math.min(size, count);
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(1)$ cho mỗi thao tác `next`.
- Không gian: $O(S)$ với $S$ là kích thước `size` của cửa sổ lưu trong mảng vòng.

---

## 18. Reveal Cards In Increasing Order
**Đề bài chi tiết:**
Bạn có một mảng bài, nhiệm vụ là sắp xếp các quân bài để khi thực hiện theo quy tắc: "rút lá đầu tiên ra ngoài, lấy lá tiếp theo chuyển xuống cuối bộ bài, lặp lại" thì kết quả những lá rút ra phải tạo thành thứ tự tăng dần.

**Phân tích thuật toán:**
Quá trình rút bài hoàn toàn có thể mô phỏng lại bằng một Queue chứa các chỉ số (index) ban đầu. Khởi tạo hàng đợi index từ `0` đến `n-1`. Thực hiện thao tác: `poll()` một index và coi đó là vị trí lá bài sẽ được rút, tiếp tục lấy phần tử kế tiếp `poll()` đẩy ngược ra sau `offer()`. Các index này thể hiện chính xác thứ tự vị trí mà bài được rút. Chỉ cần sort mảng bài ban đầu và đưa chúng vào mảng kết quả ứng với các index ta nhận được từ Queue.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public int[] deckRevealedIncreasing(int[] deck) {
        int n = deck.length;
        Arrays.sort(deck);
        Queue<Integer> indexQueue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            indexQueue.offer(i);
        }
        
        int[] result = new int[n];
        for (int card : deck) {
            result[indexQueue.poll()] = card;
            if (!indexQueue.isEmpty()) {
                indexQueue.offer(indexQueue.poll());
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n \log n)$ để sắp xếp bài và $O(n)$ để mô phỏng Queue.
- Không gian: $O(n)$ do mảng kết quả và Queue chứa index.

---

## 19. First Unique Character in a String
**Đề bài chi tiết:**
Cho một chuỗi `s`, hãy tìm ký tự đầu tiên trong chuỗi không bị lặp lại và trả về chỉ số (index) của nó. Nếu mọi ký tự đều bị lặp lại, hãy trả về -1.

**Phân tích thuật toán:**
Với bảng chữ cái tiếng Anh in thường có 26 ký tự, ta duyệt chuỗi lần đầu tiên và sử dụng mảng đếm kích thước 26 để theo dõi tần suất xuất hiện. Việc duyệt từ đầu đến cuối chuỗi tương đương với tính chất FIFO. Ở vòng lặp thứ hai, duyệt lại từ đầu chuỗi, phần tử nào có số đếm bằng 1 thì chính là ký tự đầu tiên xuất hiện duy nhất.

**Mã nguồn Java:**
```java
class Solution {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        int n = s.length();
        
        // Đếm tần suất
        for (int i = 0; i < n; i++) {
            count[s.charAt(i) - 'a']++;
        }
        
        // Tìm ký tự có tần suất = 1 đầu tiên theo đúng thứ tự chuỗi
        for (int i = 0; i < n; i++) {
            if (count[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ khi duyệt chuỗi tổng cộng 2 lần.
- Không gian: $O(1)$ do mảng `count` cố định là 26 phần tử.

---

## 20. Implement Stack using Queues
**Đề bài chi tiết:**
Thực hiện cấu trúc dữ liệu Ngăn xếp (Stack / LIFO) chỉ sử dụng duy nhất một cấu trúc Hàng đợi (Queue / FIFO). Yêu cầu có các hàm `push`, `pop`, `top`, và `empty`.

**Phân tích thuật toán:**
Để Queue (FIFO) mô phỏng được Stack (LIFO), ta cần thay đổi vị trí của các phần tử sao cho phần tử thêm vào gần nhất phải được nằm trên cùng. Mỗi khi `push` phần tử `x` vào sau hàng đợi, ta đếm `size` hiện tại, và lấy toàn bộ các phần tử đứng trước `x` luân chuyển ngược trở lại ra phía sau. Bằng cách này, `x` sẽ trồi lên đầu Queue, tạo thành đỉnh (top) giả lập của Stack.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

class MyStack {
    private Queue<Integer> queue;

    public MyStack() {
        queue = new LinkedList<>();
    }
    
    public void push(int x) {
        queue.add(x);
        int size = queue.size();
        while (size > 1) {
            queue.add(queue.remove()); // Luân chuyển n - 1 phần tử
            size--;
        }
    }
    
    public int pop() {
        return queue.remove();
    }
    
    public int top() {
        return queue.peek();
    }
    
    public boolean empty() {
        return queue.isEmpty();
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ cho hàm `push`, trong đó `n` là kích thước Stack hiện tại, do phải thực hiện vòng lặp $n-1$ lần. Các thao tác còn lại đều tốn $O(1)$.
- Không gian: $O(n)$ vì chỉ lưu trữ trên Queue.

---

## 21. Find the Winner of an Array Game
**Đề bài chi tiết:**
Cho một mảng các số nguyên phân biệt `arr` và một số nguyên `k`. Trò chơi sẽ diễn ra giữa 2 phần tử đầu tiên của mảng (tức là `arr[0]` và `arr[1]`). Trong mỗi lượt chơi, so sánh 2 phần tử, phần tử lớn hơn sẽ thắng và giữ nguyên vị trí ở đầu mảng. Phần tử nhỏ hơn sẽ bị chuyển xuống cuối mảng. Người thắng cuộc của trò chơi là phần tử đầu tiên thắng được `k` lượt liên tiếp. Trả về người thắng.

**Phân tích thuật toán:**
Dùng mảng để mô phỏng, phần tử hiện tại đang thắng được lưu trong biến `currentWinner`, số trận thắng liên tiếp là `winCount`. Nếu `k` lớn hơn độ dài mảng, người thắng chắc chắn là giá trị lớn nhất trong mảng. Duyệt qua mảng: so sánh `currentWinner` với phần tử tiếp theo. Nếu `currentWinner` thắng, tăng `winCount`. Nếu thua, phần tử tiếp theo trở thành `currentWinner` và `winCount = 1`. Quá trình hoạt động tương tự cấu trúc hàng đợi vòng, khi người thua bị đẩy xuống cuối. Dừng khi `winCount == k` hoặc đã duyệt hết mảng (trường hợp này phần tử lớn nhất thắng).

**Mã nguồn Java:**
```java
class Solution {
    public int getWinner(int[] arr, int k) {
        int currentWinner = arr[0];
        int winCount = 0;
        
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > currentWinner) {
                currentWinner = arr[i];
                winCount = 1;
            } else {
                winCount++;
            }
            
            if (winCount == k) {
                return currentWinner;
            }
        }
        
        return currentWinner; // Nếu k rất lớn, số lớn nhất sẽ thắng
    }
}
```

**Độ phức tạp:**
- Thời gian (Time Complexity): $O(n)$ với `n` là kích thước mảng.
- Không gian (Space Complexity): $O(1)$.

---

## 22. Dota2 Senate
**Đề bài chi tiết:**
Hai phe, Radiant ('R') và Dire ('D'), có mặt trong Thượng viện. Mỗi thượng nghị sĩ có thể cấm quyền biểu quyết của một thượng nghị sĩ thuộc phe đối lập, hoặc tuyên bố chiến thắng nếu toàn bộ phe đối lập đã bị mất quyền. Thứ tự bỏ phiếu diễn ra lần lượt theo cấu trúc vòng tròn (Circular Queue). Trả về phe chiến thắng.

**Phân tích thuật toán:**
Sử dụng hai Hàng đợi (Queue) riêng biệt để lưu trữ vị trí (chỉ số index) của các thượng nghị sĩ phe R và phe D. Bắt đầu từ đầu hàng đợi, so sánh index của hai thượng nghị sĩ R và D. Người có index nhỏ hơn (đến lượt trước) sẽ được quyền "cấm" người kia. Thượng nghị sĩ chiến thắng ở lượt này sẽ được đẩy xuống cuối hàng đợi để chờ lượt tiếp theo trong vòng lặp tròn. Lưu ý phải cộng thêm `n` vào index của họ khi đẩy xuống cuối để thể hiện vòng tiếp theo.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public String predictPartyVictory(String senate) {
        int n = senate.length();
        Queue<Integer> radiant = new LinkedList<>();
        Queue<Integer> dire = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            if (senate.charAt(i) == 'R') radiant.add(i);
            else dire.add(i);
        }
        
        while (!radiant.isEmpty() && !dire.isEmpty()) {
            int rIndex = radiant.poll();
            int dIndex = dire.poll();
            
            if (rIndex < dIndex) {
                radiant.add(rIndex + n);
            } else {
                dire.add(dIndex + n);
            }
        }
        
        return radiant.size() > 0 ? "Radiant" : "Dire";
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$, mỗi phần tử bị loại sẽ dần làm giảm kích thước hàng đợi.
- Không gian: $O(n)$ do lưu hai Queue.

---

## 23. Circular Sentence
**Đề bài chi tiết:**
Một câu được gọi là "tuần hoàn" (Circular Sentence) nếu:
- Ký tự cuối cùng của mỗi từ giống với ký tự đầu tiên của từ tiếp theo.
- Ký tự cuối cùng của từ cuối cùng giống với ký tự đầu tiên của từ đầu tiên.
Cho chuỗi `sentence`, kiểm tra xem nó có phải câu tuần hoàn không.

**Phân tích thuật toán:**
Sử dụng phép tính đối chiếu vòng. Kiểm tra điều kiện vòng trước: ký tự cuối câu có khớp ký tự đầu câu không (`sentence.charAt(0) == sentence.charAt(n - 1)`). Sau đó duyệt chuỗi, hễ gặp khoảng trắng (`' '`), so sánh ký tự ngay trước đó (đại diện cho cuối từ) với ký tự ngay sau đó (đại diện cho đầu từ tiếp theo). Nếu không khớp, trả về `false`.

**Mã nguồn Java:**
```java
class Solution {
    public boolean isCircularSentence(String sentence) {
        int n = sentence.length();
        if (sentence.charAt(0) != sentence.charAt(n - 1)) {
            return false;
        }
        
        for (int i = 0; i < n; i++) {
            if (sentence.charAt(i) == ' ') {
                if (sentence.charAt(i - 1) != sentence.charAt(i + 1)) {
                    return false;
                }
            }
        }
        
        return true;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ vì chỉ cần duyệt qua chuỗi đúng một lần.
- Không gian: $O(1)$ không dùng cấu trúc dữ liệu phụ trợ.

---

## 24. Length of the Longest Alphabetical Continuous Substring
**Đề bài chi tiết:**
Một chuỗi con liền kề theo bảng chữ cái (Alphabetical Continuous Substring) là một chuỗi gồm các ký tự liền tiếp nhau trong bảng chữ cái (ví dụ "abc", "def"). Lưu ý không được nối vòng từ 'z' về 'a' ở bài này (tùy vào dạng, nhưng chuẩn là theo thứ tự ASCII bình thường). Tìm độ dài lớn nhất của chuỗi con thỏa mãn điều kiện đó.

**Phân tích thuật toán:**
Đây là một biến thể của bài toán tìm chuỗi con dài nhất. Ta chỉ cần duyệt qua từng ký tự và duy trì một bộ đếm `currentLen`. Nếu ký tự hiện tại liền sau ký tự trước đó (tức `s.charAt(i) - s.charAt(i - 1) == 1`), ta tăng `currentLen` lên 1. Nếu bị ngắt mạch, `currentLen` được reset về 1. Ở mỗi bước, cập nhật biến `maxLen` để lưu kết quả.

**Mã nguồn Java:**
```java
class Solution {
    public int longestContinuousSubstring(String s) {
        int maxLen = 1;
        int currentLen = 1;
        
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) - s.charAt(i - 1) == 1) {
                currentLen++;
                maxLen = Math.max(maxLen, currentLen);
            } else {
                currentLen = 1;
            }
        }
        
        return maxLen;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ với `n` là độ dài chuỗi.
- Không gian: $O(1)$.

---

## 25. Lock-Free Bounded Queue Implementation
**Đề bài chi tiết:**
Thiết kế một Hàng đợi giới hạn (Bounded Queue) hỗ trợ lập trình đa luồng (multi-threading) không dùng khóa (Lock-free) bằng Circular Array, nhằm tránh hiện tượng "cổ chai" (bottleneck) do các Thread bị block bởi Lock.

**Phân tích thuật toán:**
Do Java hỗ trợ các biến `AtomicInteger` với phép toán Compare-And-Swap (CAS), ta có thể thiết kế một Ring Buffer để enqueue/dequeue đồng thời. Con trỏ `head` và `tail` sẽ là `AtomicInteger`. Thay vì dùng khóa (`synchronized` hoặc `ReentrantLock`), các Thread liên tục thử cập nhật trạng thái vòng đợi bằng `.compareAndSet()`. Hàm chèn và xóa sẽ kiểm tra dung lượng hợp lệ qua phép modulo.

**Mã nguồn Java:**
```java
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReferenceArray;

class LockFreeBoundedQueue<T> {
    private final AtomicReferenceArray<T> buffer;
    private final int capacity;
    private final AtomicInteger head;
    private final AtomicInteger tail;

    public LockFreeBoundedQueue(int capacity) {
        this.capacity = capacity;
        this.buffer = new AtomicReferenceArray<>(capacity);
        this.head = new AtomicInteger(0);
        this.tail = new AtomicInteger(0);
    }
    
    public boolean enqueue(T item) {
        if (item == null) throw new NullPointerException();
        while (true) {
            int currentTail = tail.get();
            int currentHead = head.get();
            if (currentTail - currentHead >= capacity) {
                return false; // Queue đã đầy
            }
            int index = currentTail % capacity;
            if (buffer.compareAndSet(index, null, item)) {
                tail.compareAndSet(currentTail, currentTail + 1);
                return true;
            }
        }
    }
    
    public T dequeue() {
        while (true) {
            int currentHead = head.get();
            int currentTail = tail.get();
            if (currentHead == currentTail) {
                return null; // Queue đang rỗng
            }
            int index = currentHead % capacity;
            T item = buffer.get(index);
            if (item != null && buffer.compareAndSet(index, item, null)) {
                head.compareAndSet(currentHead, currentHead + 1);
                return item;
            }
        }
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(1)$ amortized cho mỗi thao tác ở trường hợp lý tưởng.
- Không gian: $O(N)$ lưu dữ liệu trên Buffer tĩnh.

---

## 26. Sliding Window Maximum
**Đề bài chi tiết:**
Cho mảng `nums` và một cửa sổ trượt kích thước `k` di chuyển từ trái sang phải. Tại mỗi bước di chuyển, chỉ có thể nhìn thấy `k` số trong cửa sổ. Trả về mảng chứa số lớn nhất của từng cửa sổ.

**Phân tích thuật toán:**
Dùng một Hàng đợi hai đầu (Deque) để lưu các chỉ số của mảng. Cần duy trì các phần tử trong Deque theo chiều giảm dần về mặt giá trị. Nếu gặp phần tử lớn hơn các phần tử ở đuôi Deque, ta loại bỏ đuôi (vì các phần tử đó không bao giờ có thể trở thành giá trị lớn nhất do đã có phần tử mới to hơn và "trẻ" hơn). Loại bỏ phần tử ở đầu Deque nếu chỉ số của nó đã trượt ra khỏi cửa sổ (`index < i - k + 1`). Giá trị lớn nhất của cửa sổ hiện tại luôn nằm ở đầu Deque.

**Mã nguồn Java:**
```java
import java.util.Deque;
import java.util.LinkedList;

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0) return new int[0];
        int n = nums.length;
        int[] result = new int[n - k + 1];
        Deque<Integer> deque = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            // Xoá các chỉ số đã ra khỏi cửa sổ
            while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }
            // Loại bỏ các phần tử nhỏ hơn số hiện tại khỏi đuôi Deque
            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }
            deque.offerLast(i);
            
            // Ghi nhận đáp án khi cửa sổ đã đủ k phần tử
            if (i >= k - 1) {
                result[i - k + 1] = nums[deque.peekFirst()];
            }
        }
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ vì mỗi phần tử được thêm và lấy ra khỏi Deque tối đa 1 lần.
- Không gian: $O(k)$ do kích thước của Deque tối đa là `k`.

---

## 27. Minimum Swaps to Group All 1's Together II
**Đề bài chi tiết:**
Cho một mảng vòng (circular array) chỉ gồm 0 và 1, tính số lần đổi chỗ (swap) tối thiểu cần thiết để nhóm tất cả các số 1 lại cạnh nhau ở bất kỳ vị trí nào.

**Phân tích thuật toán:**
Gọi `totalOnes` là tổng số lượng các số 1 trong mảng. Vấn đề đổi chỗ để nhóm các số 1 có thể chuyển thành: "Tìm một cửa sổ trượt (Sliding Window) có kích thước `totalOnes` sao cho số lượng số 0 trong đó là nhỏ nhất". Vì đây là mảng vòng, ta trượt cửa sổ qua cả biên của mảng bằng phép modulo `(i + totalOnes) % n`. Cửa sổ nào chứa nhiều số 1 nhất thì cần đổi chỗ ít nhất.

**Mã nguồn Java:**
```java
class Solution {
    public int minSwaps(int[] nums) {
        int totalOnes = 0;
        for (int num : nums) totalOnes += num;
        
        if (totalOnes == 0 || totalOnes == nums.length) return 0;
        
        int n = nums.length;
        int currentOnes = 0;
        int maxOnesInWindow = 0;
        
        // Khởi tạo cửa sổ đầu tiên
        for (int i = 0; i < totalOnes; i++) {
            currentOnes += nums[i];
        }
        maxOnesInWindow = currentOnes;
        
        // Trượt cửa sổ trên mảng vòng
        for (int i = 0; i < n; i++) {
            currentOnes -= nums[i];
            currentOnes += nums[(i + totalOnes) % n];
            maxOnesInWindow = Math.max(maxOnesInWindow, currentOnes);
        }
        
        return totalOnes - maxOnesInWindow;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

---

## 28. Find All K-Distant Indices in an Array
**Đề bài chi tiết:**
Cho mảng số nguyên `nums` và hai số nguyên `key` và `k`. Tìm tất cả các chỉ số `i` (đã sắp xếp tăng dần) sao cho tồn tại ít nhất một chỉ số `j` thoả mãn `|i - j| <= k` và `nums[j] == key`.

**Phân tích thuật toán:**
Duyệt qua mảng để tìm các vị trí `j` thoả mãn `nums[j] == key`. Với mỗi `j` tìm được, ta đánh dấu các vị trí `i` nằm trong khoảng từ `j - k` đến `j + k`. Để tránh trùng lặp và tiết kiệm thời gian (chỉ duyệt mỗi phần tử một lần duy nhất), ta sử dụng một biến `lastAdded` làm con trỏ nhằm đảm bảo rằng mỗi phần tử không bị chèn vào kết quả nhiều lần.

**Mã nguồn Java:**
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        List<Integer> result = new ArrayList<>();
        int lastAdded = -1;
        int n = nums.length;
        
        for (int j = 0; j < n; j++) {
            if (nums[j] == key) {
                int start = Math.max(lastAdded + 1, j - k);
                int end = Math.min(n - 1, j + k);
                for (int i = start; i <= end; i++) {
                    result.add(i);
                    lastAdded = i;
                }
            }
        }
        
        return result;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$ vì biến `lastAdded` đảm bảo mỗi phần tử chỉ được xử lý chèn 1 lần.
- Không gian: $O(1)$ (không tính mảng danh sách trả về).

---

## 29. Repeated String Match
**Đề bài chi tiết:**
Cho hai chuỗi `a` và `b`, trả về số lần tối thiểu cần lặp lại chuỗi `a` (nối tiếp nhau tạo thành chuỗi lặp vòng) để chuỗi `b` trở thành chuỗi con của nó. Nếu không thể, trả về -1.

**Phân tích thuật toán:**
Với việc `a` được lặp vòng để nối vào chính nó, ta liên tục cộng dồn `a` cho đến khi độ dài của chuỗi kết quả lớn hơn hoặc bằng độ dài chuỗi `b`. Nếu tại bước này, `b` xuất hiện trong chuỗi kết quả, ta trả về số lần lặp. Do việc lặp vòng phụ thuộc vào điểm cắt ngang (offset), nên số vòng tối đa chỉ cần thêm 1 lần độ dài của `a` nữa (ví dụ: chuỗi `b` vắt ngang qua 2 chuỗi `a`). Vì thế ta chỉ cần kiểm tra tối đa 2 ngưỡng.

**Mã nguồn Java:**
```java
class Solution {
    public int repeatedStringMatch(String a, String b) {
        StringBuilder sb = new StringBuilder(a);
        int count = 1;
        
        while (sb.length() < b.length()) {
            sb.append(a);
            count++;
        }
        
        if (sb.indexOf(b) != -1) return count;
        
        sb.append(a); // Kiểm tra biên (offset)
        if (sb.indexOf(b) != -1) return count + 1;
        
        return -1;
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(N \times (N + M))$ với $N, M$ là độ dài của chuỗi `a` và `b`, giả sử phép `indexOf` chạy với thời gian $O(N \times M)$. (Có thể tối ưu xuống $O(N + M)$ nếu dùng thuật toán tìm kiếm chuỗi KMP).
- Không gian: $O(N + M)$ cho bộ nhớ của `StringBuilder`.

---

## 30. Josephus Problem (General Form)
**Đề bài chi tiết:**
(Khái quát hoá của bài toán người sống sót). Cho `n` người xếp vòng tròn đánh số từ 1 đến `n`. Bắt đầu từ 1, đếm đến người thứ `k` và loại bỏ người đó, lặp lại liên tục cho đến khi chỉ còn một người. Trả về vị trí của người chiến thắng.

**Phân tích thuật toán:**
Ở bài tập phần trước, chúng ta sử dụng Queue mất $O(n \times k)$ thời gian. Với phiên bản tổng quát cần độ tối ưu cao, ta sử dụng thuật toán Quy hoạch động (Dynamic Programming). Vị trí an toàn ở mỗi bước đệ quy có công thức $f(n, k) = (f(n - 1, k) + k) \% n$. Chuyển đổi nó sang dạng vòng lặp lặp lại (bottom-up) để khử đệ quy giúp tiết kiệm bộ nhớ Call Stack. (Lưu ý: công thức chuẩn dựa trên hệ đếm gốc 0, do đó kết quả cuối cùng phải cộng thêm 1).

**Mã nguồn Java:**
```java
class Solution {
    public int findTheWinner(int n, int k) {
        // Hệ đếm gốc 0
        int winner = 0; 
        
        for (int i = 2; i <= n; i++) {
            winner = (winner + k) % i;
        }
        
        // Trả về hệ đếm gốc 1
        return winner + 1; 
    }
}
```

**Độ phức tạp:**
- Thời gian: $O(n)$.
- Không gian: $O(1)$.

