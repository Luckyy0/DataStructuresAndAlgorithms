# Bài tập: Queue Basics

## 1. Implement Stack using Queues (LeetCode 225)
**Đề bài chi tiết:**  
Cài đặt một cấu trúc dữ liệu Stack (LIFO) chỉ sử dụng hai Hàng đợi (Queue). Hỗ trợ các thao tác của stack: `push`, `pop`, `top`, và `empty`.

**Phân tích thuật toán:**  
Chúng ta có thể sử dụng một Queue duy nhất. Khi `push(x)`, thêm phần tử vào hàng đợi, sau đó với các phần tử đã có từ trước, ta lần lượt lấy ra (`dequeue`) và đẩy lại vào cuối (`enqueue`). Điều này làm cho phần tử mới nhất luôn nằm ở đầu Queue, mô phỏng hành vi của Stack.

**Mã nguồn Java:**
```java
class MyStack {
    private Queue<Integer> q;

    public MyStack() {
        q = new LinkedList<>();
    }
    
    public void push(int x) {
        q.add(x);
        int size = q.size();
        while (size > 1) {
            q.add(q.remove());
            size--;
        }
    }
    
    public int pop() {
        return q.remove();
    }
    
    public int top() {
        return q.peek();
    }
    
    public boolean empty() {
        return q.isEmpty();
    }
}
```
**Độ phức tạp:**
- Time: `push`: O(N), `pop`: O(1), `top`: O(1), `empty`: O(1).
- Space: O(N) để lưu trữ các phần tử.

---

## 2. First Unique Character in a String (LeetCode 387)
**Đề bài chi tiết:**  
Cho một chuỗi `s`, tìm ký tự không lặp lại đầu tiên trong chuỗi và trả về chỉ số của nó. Nếu không tồn tại, trả về -1.

**Phân tích thuật toán:**  
Sử dụng mảng đếm tần suất 26 phần tử (cho 26 chữ cái tiếng Anh). Có thể kết hợp Queue để lưu thứ tự xuất hiện của ký tự. Tuy nhiên, đơn giản nhất là duyệt mảng 2 lần: lần 1 đếm tần suất, lần 2 tìm ký tự có tần suất = 1. Bài này Queue hữu ích khi xử lý dữ liệu luồng (stream), nhưng với chuỗi có sẵn ta có thể dùng mảng tần suất.

**Mã nguồn Java:**
```java
class Solution {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        for(char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        for(int i = 0; i < s.length(); i++) {
            if(count[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
```
**Độ phức tạp:**
- Time: O(N) với N là chiều dài chuỗi.
- Space: O(1) do mảng cố định 26 phần tử.

---

## 3. Number of Recent Calls (LeetCode 933)
**Đề bài chi tiết:**  
Thiết kế một class `RecentCounter` để đếm số lượng requests (yêu cầu) nhận được trong khoảng thời gian 3000 mili-giây (ms) gần nhất.

**Phân tích thuật toán:**  
Sử dụng Queue để lưu trữ thời gian (timestamp) của các request. Khi có request mới tại thời điểm `t`, đưa `t` vào Queue. Sau đó, loại bỏ (dequeue) tất cả các request ở đầu Queue có thời gian nhỏ hơn `t - 3000`. Kích thước của Queue lúc này chính là số lượng request hợp lệ.

**Mã nguồn Java:**
```java
class RecentCounter {
    Queue<Integer> q;
    public RecentCounter() {
        q = new LinkedList<>();
    }
    
    public int ping(int t) {
        q.add(t);
        while(q.peek() < t - 3000) {
            q.poll();
        }
        return q.size();
    }
}
```
**Độ phức tạp:**
- Time: O(1) trung bình (amortized). Mỗi request vào và ra tối đa 1 lần.
- Space: O(W) với W là số lượng requests trong cửa sổ 3000ms.

---

## 4. Moving Average from Data Stream (LeetCode 346)
**Đề bài chi tiết:**  
Tính trung bình cộng của toàn bộ một cửa sổ trượt (sliding window) với kích thước `size` cho một luồng số nguyên đầu vào.

**Phân tích thuật toán:**  
Sử dụng Queue để duy trì cửa sổ trượt. Giữ một biến `sum` để tính tổng. Mỗi khi đưa một số mới vào, cộng vào `sum` và đẩy số vào Queue. Nếu Queue vượt quá kích thước `size`, lấy số đầu ra và trừ vào `sum`. Trả về `sum / số lượng thực tế`.

**Mã nguồn Java:**
```java
class MovingAverage {
    private Queue<Integer> queue;
    private int size;
    private double sum;

    public MovingAverage(int size) {
        this.queue = new LinkedList<>();
        this.size = size;
        this.sum = 0;
    }
    
    public double next(int val) {
        queue.offer(val);
        sum += val;
        if (queue.size() > size) {
            sum -= queue.poll();
        }
        return sum / queue.size();
    }
}
```
**Độ phức tạp:**
- Time: O(1) mỗi thao tác `next`.
- Space: O(N) với N là kích thước của cửa sổ (`size`).

---

## 5. Time Needed to Buy Tickets (LeetCode 2073)
**Đề bài chi tiết:**  
Có `n` người đang xếp hàng mua vé, vị trí 0-indexed. Mỗi giây mua được 1 vé. Cho mảng `tickets` (số vé mỗi người muốn mua) và `k` (vị trí người cần theo dõi). Tính số giây để người ở vị trí `k` mua đủ vé.

**Phân tích thuật toán:**  
Mô phỏng hàng đợi: Nếu người i đứng trước (hoặc chính là) người k (i <= k), họ sẽ mua tối đa `tickets[k]` vé trước khi k hoàn thành. Nếu người i đứng sau k (i > k), họ sẽ mua tối đa `tickets[k] - 1` vé. Ta cộng dồn số vé thực tế.

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
- Time: O(N) với N là số lượng người.
- Space: O(1).

---

## 6. Number of Students Unable to Eat Lunch (LeetCode 1700)
**Đề bài chi tiết:**  
Học sinh đứng thành hàng đợi và chọn bánh mì hình tròn (0) hoặc vuông (1) đang nằm ở đầu ngăn xếp. Nếu loại bánh trùng với sở thích, học sinh lấy và đi. Nếu không, học sinh quay xuống cuối hàng đợi. Hỏi còn lại bao nhiêu học sinh không ăn được.

**Phân tích thuật toán:**  
Số lượng bánh không thay đổi, chỉ thay đổi thứ tự học sinh. Nếu ở đầu stack có bánh loại X mà không còn sinh viên nào thích loại X trong hàng, thì tiến trình dừng lại. Ta đếm số lượng sinh viên thích mỗi loại bánh và duyệt ngăn xếp.

**Mã nguồn Java:**
```java
class Solution {
    public int countStudents(int[] students, int[] sandwiches) {
        int[] counts = new int[2];
        for(int s : students) counts[s]++;
        
        for(int sw : sandwiches) {
            if(counts[sw] > 0) {
                counts[sw]--;
            } else {
                break;
            }
        }
        return counts[0] + counts[1];
    }
}
```
**Độ phức tạp:**
- Time: O(N) với N là số sinh viên.
- Space: O(1).

---

## 7. Reveal Cards In Increasing Order (LeetCode 950)
**Đề bài chi tiết:**  
Bạn có một bộ bài. Bạn muốn sắp xếp chúng để khi thực hiện thao tác: rút bài đầu tiên, chuyển lá tiếp theo xuống cuối bộ bài... thì thứ tự rút ra tăng dần. Trả về mảng bài ban đầu.

**Phân tích thuật toán:**  
Sắp xếp bộ bài và mô phỏng ngược lại. Hoặc sắp xếp và dùng Queue để lưu các index của bộ bài. Lấy index đầu ra đặt lá bài nhỏ nhất vào kết quả, rồi lấy index tiếp theo đẩy xuống cuối hàng đợi.

**Mã nguồn Java:**
```java
class Solution {
    public int[] deckRevealedIncreasing(int[] deck) {
        int n = deck.length;
        Arrays.sort(deck);
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < n; i++) q.add(i);
        
        int[] res = new int[n];
        for (int i = 0; i < n; i++) {
            res[q.poll()] = deck[i];
            if (!q.isEmpty()) {
                q.add(q.poll());
            }
        }
        return res;
    }
}
```
**Độ phức tạp:**
- Time: O(N log N) do thao tác sắp xếp.
- Space: O(N) để lưu trữ Queue và mảng kết quả.

---

## 8. Dota2 Senate (LeetCode 649)
**Đề bài chi tiết:**  
Hai phe trong Thượng viện Dota2: Radiant ('R') và Dire ('D'). Mỗi thượng nghị sĩ có thể cấm quyền bỏ phiếu của 1 nghị sĩ khác ở lượt tiếp theo. Các vòng lặp lại cho đến khi chỉ còn 1 phe. Trả về tên phe chiến thắng.

**Phân tích thuật toán:**  
Dùng 2 Queue lưu chỉ số của các thượng nghị sĩ Radiant và Dire. Ở mỗi vòng, so sánh chỉ số đầu của 2 Queue. Ai nhỏ hơn (đứng trước) sẽ cấm người kia và tiếp tục vào vòng sau bằng cách đẩy index + N vào Queue.

**Mã nguồn Java:**
```java
class Solution {
    public String predictPartyVictory(String senate) {
        Queue<Integer> rQueue = new LinkedList<>();
        Queue<Integer> dQueue = new LinkedList<>();
        int n = senate.length();
        
        for (int i = 0; i < n; i++) {
            if (senate.charAt(i) == 'R') rQueue.add(i);
            else dQueue.add(i);
        }
        
        while (!rQueue.isEmpty() && !dQueue.isEmpty()) {
            int rIndex = rQueue.poll();
            int dIndex = dQueue.poll();
            
            if (rIndex < dIndex) {
                rQueue.add(rIndex + n);
            } else {
                dQueue.add(dIndex + n);
            }
        }
        
        return rQueue.isEmpty() ? "Dire" : "Radiant";
    }
}
```
**Độ phức tạp:**
- Time: O(N) trung bình.
- Space: O(N) để lưu Queue.

---

## 9. Find the Winner of the Circular Game (LeetCode 1823)
**Đề bài chi tiết:**  
N người bạn đứng thành vòng tròn (1 đến n). Trò chơi đếm tới k, người thứ k bị loại. Đếm tiếp từ người tiếp theo. Trả về người chiến thắng cuối cùng. (Bài toán Josephus)

**Phân tích thuật toán:**  
Có thể giải bằng cách dùng Queue mô phỏng (push/pop k-1 lần rồi loại bỏ lần k) hoặc sử dụng đệ quy (quy hoạch động) của bài toán Josephus `f(n, k) = (f(n-1, k) + k) % n`. Mô phỏng Queue trực quan hơn cho bài học này.

**Mã nguồn Java:**
```java
class Solution {
    public int findTheWinner(int n, int k) {
        Queue<Integer> q = new LinkedList<>();
        for (int i = 1; i <= n; i++) {
            q.add(i);
        }
        while (q.size() > 1) {
            for (int i = 1; i < k; i++) {
                q.add(q.poll());
            }
            q.poll(); // Loại bỏ người thứ k
        }
        return q.poll();
    }
}
```
**Độ phức tạp:**
- Time: O(N * K) nếu mô phỏng (hoặc O(N) nếu dùng đệ quy/DP).
- Space: O(N) để lưu trữ các thành viên.

---

## 10. Task Scheduler (LeetCode 621)
**Đề bài chi tiết:**  
Cho mảng đại diện cho các CPU tasks và một giá trị làm mát (cooldown) `n`. Hai task cùng loại phải cách nhau tối thiểu `n` chu kỳ. Tính số thời gian tối thiểu để hoàn thành.

**Phân tích thuật toán:**  
Tính tần suất xuất hiện nhiều nhất của task (đặt là max_freq). Số lượng lỗ hổng (slots) tối thiểu cần lấp là `(max_freq - 1) * n`. Bài toán này có thể dùng tham lam: Công thức là `max(tasks.length, (max_freq - 1) * (n + 1) + count_max)`. Dù đây không dùng Queue truyền thống nhưng có thể mô phỏng bằng MaxHeap / Queue để học. Ở đây trình bày thuật toán toán học tối ưu.

**Mã nguồn Java:**
```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26];
        int maxFreq = 0;
        int maxCount = 0;
        
        for (char task : tasks) {
            counts[task - 'A']++;
            if (counts[task - 'A'] == maxFreq) {
                maxCount++;
            } else if (counts[task - 'A'] > maxFreq) {
                maxFreq = counts[task - 'A'];
                maxCount = 1;
            }
        }
        
        int intervals = (maxFreq - 1) * (n + 1) + maxCount;
        return Math.max(tasks.length, intervals);
    }
}
```
**Độ phức tạp:**
- Time: O(N) do phải duyệt mảng tasks 1 lần.
- Space: O(1) do sử dụng mảng cố định 26.

---

## 11. Design Circular Queue (LeetCode 622)
**Đề bài chi tiết:**  
Thiết kế cấu trúc dữ liệu Hàng đợi vòng (Circular Queue). Khác với hàng đợi thông thường, hàng đợi vòng kết nối vị trí cuối cùng trở lại vị trí đầu tiên để tận dụng không gian mảng đã giải phóng.
Hỗ trợ các thao tác: `MyCircularQueue(k)`, `Front()`, `Rear()`, `enQueue(value)`, `deQueue()`, `isEmpty()`, `isFull()`.

**Phân tích thuật toán:**  
Sử dụng một mảng có kích thước `k`. Dùng hai con trỏ `head` và `tail` để đánh dấu đầu và cuối. Biến `size` để theo dõi số lượng phần tử hiện tại. Khi `enQueue`, nếu chưa đầy, tăng `tail` lên `(tail + 1) % k` và tăng `size`. Khi `deQueue`, nếu không rỗng, tăng `head` lên `(head + 1) % k` và giảm `size`. 

**Mã nguồn Java:**
```java
class MyCircularQueue {
    private int[] q;
    private int head;
    private int tail;
    private int size;
    private int k;

    public MyCircularQueue(int k) {
        this.k = k;
        q = new int[k];
        head = 0;
        tail = -1;
        size = 0;
    }
    
    public boolean enQueue(int value) {
        if (isFull()) return false;
        tail = (tail + 1) % k;
        q[tail] = value;
        size++;
        return true;
    }
    
    public boolean deQueue() {
        if (isEmpty()) return false;
        head = (head + 1) % k;
        size--;
        return true;
    }
    
    public int Front() {
        return isEmpty() ? -1 : q[head];
    }
    
    public int Rear() {
        return isEmpty() ? -1 : q[tail];
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == k;
    }
}
```
**Độ phức tạp:**
- Time: O(1) cho tất cả các thao tác.
- Space: O(K) để lưu trữ mảng, với K là kích thước tối đa của hàng đợi vòng.

---

## 12. Design Circular Deque (LeetCode 641)
**Đề bài chi tiết:**  
Thiết kế cấu trúc dữ liệu Hàng đợi hai đầu dạng vòng (Circular Double-ended Queue).
Hỗ trợ các thao tác: `MyCircularDeque(k)`, `insertFront()`, `insertLast()`, `deleteFront()`, `deleteLast()`, `getFront()`, `getRear()`, `isEmpty()`, `isFull()`.

**Phân tích thuật toán:**  
Tương tự Hàng đợi vòng, ta dùng mảng kích thước `k` và các biến `head`, `tail`, `size`. `insertFront` sẽ lùi `head` về `(head - 1 + k) % k`, `deleteLast` sẽ lùi `tail` về `(tail - 1 + k) % k`. `insertLast` tiến `tail` và `deleteFront` tiến `head`. Lưu ý khởi tạo `head = 0`, `tail = k - 1` để khi `insertLast` lần đầu, `tail` trở về 0.

**Mã nguồn Java:**
```java
class MyCircularDeque {
    private int[] q;
    private int head, tail, size, k;

    public MyCircularDeque(int k) {
        this.k = k;
        q = new int[k];
        head = 0;
        tail = k - 1; 
        size = 0;
    }
    
    public boolean insertFront(int value) {
        if (isFull()) return false;
        head = (head - 1 + k) % k;
        q[head] = value;
        size++;
        return true;
    }
    
    public boolean insertLast(int value) {
        if (isFull()) return false;
        tail = (tail + 1) % k;
        q[tail] = value;
        size++;
        return true;
    }
    
    public boolean deleteFront() {
        if (isEmpty()) return false;
        head = (head + 1) % k;
        size--;
        return true;
    }
    
    public boolean deleteLast() {
        if (isEmpty()) return false;
        tail = (tail - 1 + k) % k;
        size--;
        return true;
    }
    
    public int getFront() {
        return isEmpty() ? -1 : q[head];
    }
    
    public int getRear() {
        return isEmpty() ? -1 : q[tail];
    }
    
    public boolean isEmpty() {
        return size == 0;
    }
    
    public boolean isFull() {
        return size == k;
    }
}
```
**Độ phức tạp:**
- Time: O(1) cho mỗi thao tác.
- Space: O(K) để lưu trữ mảng không gian vòng.

---

## 13. First Negative Integer in every Window of Size K
**Đề bài chi tiết:**  
Cho một mảng số nguyên và số `K`. Tìm và in ra số nguyên âm đầu tiên trong mỗi cửa sổ trượt kích thước `K`. Nếu một cửa sổ không có số nguyên âm, in ra `0`.

**Phân tích thuật toán:**  
Sử dụng một Queue để lưu trữ chỉ số (index) của các số nguyên âm trong mảng. Khi dịch chuyển cửa sổ trượt (từ `i` đến `i + K - 1`):
1. Đưa số âm mới vào Queue.
2. Loại bỏ các chỉ số ở đầu Queue nếu chúng rơi ra ngoài cửa sổ hiện tại (index < `i`).
3. Phần tử ở đầu Queue chính là số nguyên âm đầu tiên của cửa sổ hiện tại.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public long[] printFirstNegativeInteger(long A[], int N, int K) {
        long[] result = new long[N - K + 1];
        Queue<Integer> q = new LinkedList<>();
        
        for (int i = 0; i < N; i++) {
            if (A[i] < 0) {
                q.add(i);
            }
            if (i >= K - 1) {
                while (!q.isEmpty() && q.peek() <= i - K) {
                    q.poll();
                }
                result[i - K + 1] = q.isEmpty() ? 0 : A[q.peek()];
            }
        }
        return result;
    }
}
```
**Độ phức tạp:**
- Time: O(N) vì mỗi chỉ số được đưa vào và lấy ra khỏi Queue tối đa một lần.
- Space: O(K) vì Queue lưu tối đa K số nguyên âm tại cùng một thời điểm.

---

## 14. Sliding Window Maximum (LeetCode 239)
**Đề bài chi tiết:**  
Cho mảng số nguyên `nums` và một cửa sổ trượt kích thước `k`. Cửa sổ trượt từ trái sang phải, mỗi lần trượt 1 bước. Trả về mảng các giá trị lớn nhất trong từng cửa sổ.

**Phân tích thuật toán:**  
Dùng một Hàng đợi hai đầu giảm điệu (Monotonic Decreasing Deque) lưu trữ chỉ số các phần tử. Deque luôn duy trì các phần tử giảm dần, như vậy phần tử đầu Deque luôn là max của cửa sổ.
- Loại bỏ các phần tử khỏi đuôi Deque nếu chúng nhỏ hơn phần tử mới (vì chúng không thể là max được nữa).
- Đẩy chỉ số mới vào đuôi.
- Loại bỏ phần tử ở đầu Deque nếu nó nằm ngoài cửa sổ hiện tại.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || k <= 0) return new int[0];
        int n = nums.length;
        int[] result = new int[n - k + 1];
        Deque<Integer> deque = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            // Loại bỏ các phần tử ngoài cửa sổ
            while (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }
            // Loại bỏ các phần tử nhỏ hơn nums[i] từ đuôi
            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }
            deque.offerLast(i);
            
            // Cửa sổ đủ k phần tử, lưu giá trị max
            if (i >= k - 1) {
                result[i - k + 1] = nums[deque.peekFirst()];
            }
        }
        return result;
    }
}
```
**Độ phức tạp:**
- Time: O(N) vì mỗi phần tử push/pop tối đa 1 lần.
- Space: O(K) để lưu trữ deque.

---

## 15. Constrained Subsequence Sum (LeetCode 1425)
**Đề bài chi tiết:**  
Cho mảng `nums` và số nguyên `k`. Tìm tổng lớn nhất của một dãy con khác rỗng sao cho khoảng cách giữa hai phần tử liên tiếp trong dãy con không vượt quá `k`.

**Phân tích thuật toán:**  
Đây là bài toán Quy hoạch động (DP) kết hợp với Monotonic Deque. Gọi `dp[i]` là tổng lớn nhất kết thúc tại `i`. Ta có: `dp[i] = nums[i] + max(0, max(dp[i-k], ..., dp[i-1]))`. 
Để tối ưu việc tìm max trong phạm vi `k` phần tử trước, ta dùng Monotonic Decreasing Deque (tương tự bài Sliding Window Maximum) để duy trì max của `dp` trong cửa sổ `k`.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int constrainedSubsetSum(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        Deque<Integer> deque = new LinkedList<>();
        int maxSum = nums[0];
        
        for (int i = 0; i < n; i++) {
            // Lấy max dp trong khoảng i-k
            int maxPrev = deque.isEmpty() ? 0 : Math.max(0, dp[deque.peekFirst()]);
            dp[i] = nums[i] + maxPrev;
            maxSum = Math.max(maxSum, dp[i]);
            
            // Loại bỏ phần tử cũ quá k khoảng cách
            while (!deque.isEmpty() && deque.peekFirst() <= i - k) {
                deque.pollFirst();
            }
            // Duy trì Deque giảm dần
            while (!deque.isEmpty() && dp[deque.peekLast()] <= dp[i]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }
        return maxSum;
    }
}
```
**Độ phức tạp:**
- Time: O(N) nhờ tối ưu bằng Monotonic Deque, loại bỏ được vòng lặp con.
- Space: O(N) cho mảng DP và Deque.

---

## 16. Design Front Middle Back Queue (LeetCode 1670)
**Đề bài chi tiết:**  
Thiết kế hàng đợi cho phép đẩy (push) và lấy (pop) ở phía trước (front), giữa (middle) và cuối (back).

**Phân tích thuật toán:**  
Sử dụng hai Deque (`left` và `right`) để chia hàng đợi làm đôi. 
Quy tắc: `left` luôn có kích thước bằng hoặc ít hơn `right` đúng 1 phần tử (tức là `left.size() == right.size()` hoặc `left.size() + 1 == right.size()`). Khi insert/pop middle, ta sẽ tác động vào ranh giới giữa 2 deque, rồi thực hiện cân bằng lại kích thước (rebalance).

**Mã nguồn Java:**
```java
import java.util.*;

class FrontMiddleBackQueue {
    Deque<Integer> left, right;

    public FrontMiddleBackQueue() {
        left = new LinkedList<>();
        right = new LinkedList<>();
    }
    
    private void balance() {
        if (left.size() > right.size()) {
            right.offerFirst(left.pollLast());
        } else if (right.size() > left.size() + 1) {
            left.offerLast(right.pollFirst());
        }
    }
    
    public void pushFront(int val) {
        left.offerFirst(val);
        balance();
    }
    
    public void pushMiddle(int val) {
        if (left.size() < right.size()) {
            left.offerLast(val);
        } else {
            right.offerFirst(val);
        }
    }
    
    public void pushBack(int val) {
        right.offerLast(val);
        balance();
    }
    
    public int popFront() {
        if (right.isEmpty()) return -1;
        int res = left.isEmpty() ? right.pollFirst() : left.pollFirst();
        balance();
        return res;
    }
    
    public int popMiddle() {
        if (right.isEmpty()) return -1;
        int res = left.size() == right.size() ? left.pollLast() : right.pollFirst();
        balance();
        return res;
    }
    
    public int popBack() {
        if (right.isEmpty()) return -1;
        int res = right.pollLast();
        balance();
        return res;
    }
}
```
**Độ phức tạp:**
- Time: O(1) trung bình cho tất cả các thao tác đẩy và lấy.
- Space: O(N) để chứa các phần tử trong hai Deque.

---

## 17. Maximum Sum Circular Subarray (LeetCode 918)
**Đề bài chi tiết:**  
Tìm tổng lớn nhất của dãy con liên tiếp trong mảng số nguyên dạng vòng (nối đầu cuối). 

**Phân tích thuật toán:**  
Nếu dùng Queue, ta có thể nối mảng thành `2N` phần tử, chuyển bài toán thành tìm tổng lớn nhất của dãy con độ dài `<= N`. Dùng mảng Prefix Sum và Monotonic Deque để tìm khoảng cách tối đa N có hiệu (Prefix Sum) lớn nhất. Deque lưu chỉ số và duy trì Prefix Sum tăng dần.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int maxSubarraySumCircular(int[] nums) {
        int n = nums.length;
        int[] prefix = new int[2 * n + 1];
        for (int i = 0; i < 2 * n; i++) {
            prefix[i + 1] = prefix[i] + nums[i % n];
        }
        
        int ans = nums[0];
        Deque<Integer> deque = new LinkedList<>();
        deque.offerLast(0);
        
        for (int i = 1; i <= 2 * n; i++) {
            // Giữ cho cửa sổ giới hạn độ dài <= n
            if (deque.peekFirst() < i - n) {
                deque.pollFirst();
            }
            // Cập nhật kết quả max
            ans = Math.max(ans, prefix[i] - prefix[deque.peekFirst()]);
            // Monotonic Increasing Deque cho mảng prefix sum
            while (!deque.isEmpty() && prefix[i] <= prefix[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time: O(N) do duyệt qua mảng 2N và push/pop Deque mất O(1) mỗi phần tử.
- Space: O(N) cho mảng prefix và Deque.

---

## 18. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit (LeetCode 1438)
**Đề bài chi tiết:**  
Tìm độ dài lớn nhất của một dãy con liên tiếp mà chênh lệch tuyệt đối giữa phần tử lớn nhất và nhỏ nhất trong dãy con đó không vượt quá `limit`.

**Phân tích thuật toán:**  
Sử dụng kỹ thuật Hai Con Trỏ (Sliding Window) kết hợp 2 Deque:
- `maxDeque`: Monotonic Decreasing Deque để tìm giá trị Max trong cửa sổ.
- `minDeque`: Monotonic Increasing Deque để tìm giá trị Min trong cửa sổ.
Khi `max - min > limit`, ta phải thu hẹp cửa sổ (tăng con trỏ `left`) và cập nhật lại loại bỏ các giá trị cũ khỏi hai deque.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int longestSubarray(int[] nums, int limit) {
        Deque<Integer> maxDeque = new LinkedList<>();
        Deque<Integer> minDeque = new LinkedList<>();
        int left = 0, maxLength = 0;
        
        for (int right = 0; right < nums.length; right++) {
            while (!maxDeque.isEmpty() && maxDeque.peekLast() < nums[right]) {
                maxDeque.pollLast();
            }
            maxDeque.offerLast(nums[right]);
            
            while (!minDeque.isEmpty() && minDeque.peekLast() > nums[right]) {
                minDeque.pollLast();
            }
            minDeque.offerLast(nums[right]);
            
            while (maxDeque.peekFirst() - minDeque.peekFirst() > limit) {
                if (maxDeque.peekFirst() == nums[left]) maxDeque.pollFirst();
                if (minDeque.peekFirst() == nums[left]) minDeque.pollFirst();
                left++;
            }
            
            maxLength = Math.max(maxLength, right - left + 1);
        }
        return maxLength;
    }
}
```
**Độ phức tạp:**
- Time: O(N) do mỗi phần tử vào/ra deque nhiều nhất 1 lần.
- Space: O(N) không gian lưu trữ cho 2 deque trong trường hợp xấu nhất.

---

## 19. Continuous Subarray Sum (LeetCode 523)
**Đề bài chi tiết:**  
Cho mảng `nums` và số nguyên `k`. Kiểm tra xem có dãy con liên tiếp nào có độ dài ít nhất bằng 2 mà tổng của các phần tử trong dãy con đó chia hết cho `k` hay không.

**Phân tích thuật toán:**  
Sử dụng Prefix Sum kết hợp với HashMap (áp dụng tính chất xử lý luồng dọc theo mảng). Tổng của mảng con từ `i` đến `j` chia hết cho `k` khi `prefix[j] % k == prefix[i-1] % k`. Ta dùng Map để lưu trữ số dư và chỉ số xuất hiện đầu tiên của nó. Nếu một số dư lặp lại sau ít nhất 2 khoảng cách, ta tìm được dãy con hợp lệ.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> modMap = new HashMap<>();
        modMap.put(0, -1);
        int prefixSum = 0;
        
        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];
            int mod = k == 0 ? prefixSum : prefixSum % k;
            
            if (modMap.containsKey(mod)) {
                if (i - modMap.get(mod) > 1) {
                    return true;
                }
            } else {
                modMap.put(mod, i);
            }
        }
        return false;
    }
}
```
**Độ phức tạp:**
- Time: O(N) vì chỉ cần duyệt qua mảng đúng 1 lần.
- Space: O(min(N, K)) không gian HashMap lưu tối đa K các phần dư.

---

## 20. Shortest Subarray with Sum at Least K (LeetCode 862)
**Đề bài chi tiết:**  
Cho mảng nguyên `nums` (có thể chứa số âm) và số `K`. Tìm độ dài ngắn nhất của mảng con liên tiếp có tổng ít nhất bằng `K`. Trả về -1 nếu không có mảng con nào thỏa mãn.

**Phân tích thuật toán:**  
Do mảng có số âm, Sliding Window bằng 2 con trỏ cơ bản không hiệu quả (vì khi mở rộng tổng có thể giảm). Ta sử dụng mảng Prefix Sum kết hợp với Monotonic Deque lưu chỉ số.
- Nếu `prefix[i] - prefix[deque.peekFirst()] >= K`, ta cập nhật kết quả và gọi `pollFirst()` (vì chỉ số đầu tiên này đã tìm được dãy ngắn nhất có thể cho nó).
- Giữ Deque luôn tăng dần bằng cách xóa các phần tử ở đuôi có `prefix` lớn hơn hoặc bằng `prefix[i]` để tối ưu hoá khả năng tạo dãy có tổng lớn và độ dài ngắn hơn.

**Mã nguồn Java:**
```java
import java.util.*;

class Solution {
    public int shortestSubarray(int[] nums, int k) {
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
        
        int minLen = n + 1;
        Deque<Integer> deque = new LinkedList<>();
        
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
**Độ phức tạp:**
- Time: O(N) vì mỗi chỉ số vào/ra deque nhiều nhất 1 lần.
- Space: O(N) để lưu trữ mảng prefix và deque.

---

## 21. Kth Largest Element in a Stream (LeetCode 703)
**Đề bài chi tiết:**  
Thiết kế một lớp để tìm phần tử lớn thứ `k` trong một luồng số (stream). Lớp phải hỗ trợ phương thức `add(int val)` để thêm một giá trị mới và trả về phần tử lớn thứ `k` hiện tại.

**Phân tích thuật toán:**  
Sử dụng một Hàng đợi ưu tiên (Priority Queue - Min Heap) có kích thước tối đa là `k`. Min Heap sẽ giữ `k` phần tử lớn nhất đã từng xuất hiện. Phần tử ở gốc (top) của Min Heap luôn là phần tử nhỏ nhất trong `k` phần tử này, tức là phần tử lớn thứ `k` của cả luồng.
- Khi thêm phần tử mới, thêm nó vào Min Heap.
- Nếu kích thước của Heap lớn hơn `k`, ta sẽ loại bỏ gốc (`poll()`).

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        minHeap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        minHeap.offer(val);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
        return minHeap.peek();
    }
}
```
**Độ phức tạp:**
- Time: O(N log K) cho quá trình khởi tạo, O(log K) cho mỗi thao tác `add`.
- Space: O(K) để lưu Min Heap.

---

## 22. Find Median from Data Stream (LeetCode 295)
**Đề bài chi tiết:**  
Tính trung vị (median) của một luồng dữ liệu số nguyên. Hỗ trợ 2 thao tác: `addNum(int num)` để thêm số mới vào, và `findMedian()` để trả về giá trị trung vị của các số hiện tại.

**Phân tích thuật toán:**  
Sử dụng hai Priority Queues:
- `maxHeap`: Lưu nửa dưới của dữ liệu (các giá trị nhỏ hơn).
- `minHeap`: Lưu nửa trên của dữ liệu (các giá trị lớn hơn).
Để đảm bảo trung vị có thể lấy trong O(1):
1. `maxHeap` luôn chứa số phần tử bằng hoặc nhiều hơn `minHeap` 1 phần tử.
2. Mọi phần tử trong `maxHeap` phải nhỏ hơn hoặc bằng mọi phần tử trong `minHeap`.
Khi thêm một số, ta cho vào `maxHeap`, sau đó đưa phần tử lớn nhất của `maxHeap` sang `minHeap`. Nếu `minHeap` nhiều phần tử hơn `maxHeap`, ta đưa phần tử nhỏ nhất từ `minHeap` về lại `maxHeap`.

**Mã nguồn Java:**
```java
import java.util.Collections;
import java.util.PriorityQueue;

class MedianFinder {
    private PriorityQueue<Integer> maxHeap;
    private PriorityQueue<Integer> minHeap;

    public MedianFinder() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        maxHeap.offer(num);
        minHeap.offer(maxHeap.poll());
        
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek();
        }
        return (maxHeap.peek() + minHeap.peek()) / 2.0;
    }
}
```
**Độ phức tạp:**
- Time: O(log N) cho `addNum` và O(1) cho `findMedian`.
- Space: O(N) để lưu trữ toàn bộ các phần tử.

---

## 23. Jump Game VI (LeetCode 1696)
**Đề bài chi tiết:**  
Cho một mảng `nums` và một số nguyên `k`. Bạn xuất phát từ chỉ số 0 và muốn nhảy đến chỉ số `n - 1`. Trong mỗi bước, bạn có thể nhảy tối đa `k` bước về phía trước. Điểm số của đường đi là tổng các `nums[i]` đã nhảy vào. Hãy tối đa hóa điểm số đó.

**Phân tích thuật toán:**  
Sử dụng Quy hoạch động (DP) kết hợp với Monotonic Deque. Gọi `dp[i]` là điểm số lớn nhất khi đến `i`.
`dp[i] = nums[i] + max(dp[i-k] ... dp[i-1])`
Để tìm `max` trong cửa sổ kích thước `k` tối ưu, ta dùng Monotonic Decreasing Deque để giữ chỉ số các `dp` giảm dần, loại bỏ các giá trị cũ xa hơn `k` bước.

**Mã nguồn Java:**
```java
import java.util.Deque;
import java.util.LinkedList;

class Solution {
    public int maxResult(int[] nums, int k) {
        int n = nums.length;
        int[] dp = new int[n];
        dp[0] = nums[0];
        
        Deque<Integer> deque = new LinkedList<>();
        deque.offerLast(0);
        
        for (int i = 1; i < n; i++) {
            while (!deque.isEmpty() && deque.peekFirst() < i - k) {
                deque.pollFirst();
            }
            dp[i] = nums[i] + dp[deque.peekFirst()];
            
            while (!deque.isEmpty() && dp[deque.peekLast()] <= dp[i]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }
        
        return dp[n - 1];
    }
}
```
**Độ phức tạp:**
- Time: O(N) do mỗi chỉ số vào/ra deque đúng 1 lần.
- Space: O(N) cho mảng DP và Deque.

---

## 24. Max Value of Equation (LeetCode 1499)
**Đề bài chi tiết:**  
Cho mảng các điểm `points` đã được sắp xếp theo `x`, trong đó `points[i] = [xi, yi]`, và số nguyên `k`. Tìm giá trị lớn nhất của phương trình `yi + yj + |xi - xj|` với `|xi - xj| <= k` và `i < j`.

**Phân tích thuật toán:**  
Vì `xi < xj`, ta có `|xi - xj| = xj - xi`. Phương trình trở thành: `(yi - xi) + (yj + xj)`.
Để tối đa hóa giá trị này tại một `j` cố định, ta cần tìm một `i < j` thoả mãn `xj - xi <= k` sao cho `yi - xi` là lớn nhất.
Sử dụng Monotonic Decreasing Deque để lưu trữ các cặp `[xi, yi - xi]` sao cho `yi - xi` giảm dần. Ta loại bỏ khỏi đầu hàng đợi nếu khoảng cách `x` đã vượt quá `k`.

**Mã nguồn Java:**
```java
import java.util.Deque;
import java.util.LinkedList;

class Solution {
    public int findMaxValueOfEquation(int[][] points, int k) {
        Deque<int[]> deque = new LinkedList<>();
        int maxVal = Integer.MIN_VALUE;
        
        for (int[] point : points) {
            int x = point[0];
            int y = point[1];
            
            // Xóa các điểm quá xa (xj - xi > k)
            while (!deque.isEmpty() && x - deque.peekFirst()[0] > k) {
                deque.pollFirst();
            }
            
            if (!deque.isEmpty()) {
                maxVal = Math.max(maxVal, x + y + deque.peekFirst()[1]);
            }
            
            int diff = y - x;
            // Duy trì deque giảm dần theo giá trị (y - x)
            while (!deque.isEmpty() && deque.peekLast()[1] <= diff) {
                deque.pollLast();
            }
            deque.offerLast(new int[]{x, diff});
        }
        
        return maxVal;
    }
}
```
**Độ phức tạp:**
- Time: O(N) với N là số lượng điểm.
- Space: O(N) để lưu trữ deque.

---

## 25. Deliver Boxes from Storage to Ports (LeetCode 1687)
**Đề bài chi tiết:**  
Bạn có một xe tải cần giao các hộp đến các cảng biển. Xe có sức chứa `maxBoxes` và trọng tải `maxWeight`. Xe xuất phát từ kho (cảng 0), chở một phần các hộp theo đúng thứ tự mảng đầu vào, đi giao cho từng cảng rồi quay về kho. Việc di chuyển giữa kho đến cảng hoặc giữa 2 cảng khác nhau tính là 1 chuyến đi. Tính số chuyến đi tối thiểu để giao hết.

**Phân tích thuật toán:**  
Đây là bài DP kết hợp với tối ưu cửa sổ trượt (Sliding Window Queue). Gọi `dp[i]` là chi phí tối thiểu sau khi giao hộp thứ `i` và quay về kho. Cửa sổ bị giới hạn bởi `maxBoxes` và `maxWeight`. Khi mang thêm hộp thứ `i`, ta kiểm tra xem nó có cùng đích với hộp trước không (nếu khác thì chuyến đi tăng). Dùng phương pháp hai con trỏ trượt qua dãy để loại bỏ bớt phần tử nếu vượt sức chứa, luôn lấy giá trị `dp` nhỏ nhất trong cửa sổ hợp lệ.

**Mã nguồn Java:**
```java
class Solution {
    public int boxDelivering(int[][] boxes, int portsCount, int maxBoxes, int maxWeight) {
        int n = boxes.length;
        int[] dp = new int[n + 1];
        int trips = 2; 
        int weight = 0;
        int left = 0;
        
        for (int right = 0; right < n; right++) {
            weight += boxes[right][1];
            if (right > 0 && boxes[right][0] != boxes[right - 1][0]) {
                trips++;
            }
            
            while (right - left + 1 > maxBoxes || weight > maxWeight || 
                  (left < right && dp[left] == dp[left + 1])) {
                weight -= boxes[left][1];
                if (boxes[left][0] != boxes[left + 1][0]) {
                    trips--;
                }
                left++;
            }
            
            dp[right + 1] = dp[left] + trips;
        }
        
        return dp[n];
    }
}
```
**Độ phức tạp:**
- Time: O(N) do cửa sổ trượt chỉ tốn 1 lần mở và đóng mỗi phần tử.
- Space: O(N) cho mảng DP.

---

## 26. Minimum Number of K Consecutive Bit Flips (LeetCode 995)
**Đề bài chi tiết:**  
Cho mảng bit `nums` (chỉ chứa 0 và 1) và số `k`. Ta có thao tác: chọn một đoạn con kích thước `k` và lật tất cả các bit (0 thành 1, 1 thành 0). Tìm số lần lật tối thiểu để mảng không còn số 0. Trả về -1 nếu không thể.

**Phân tích thuật toán:**  
Dùng thuật toán Tham lam. Khi gặp số 0, ta bắt buộc phải lật đoạn `k` bit bắt đầu từ vị trí đó. Để không phải mất O(k) lật vật lý, ta dùng một Queue để lưu trữ vị trí kết thúc của các lần lật. Kích thước Queue chính là số lượng lần lật đang tác động lên bit hiện tại. 
- Xóa các lần lật đã hết tác dụng (vị trí kết thúc < i).
- Trạng thái thực sự của `nums[i]` = `nums[i] ^ (queue.size() % 2)`.

**Mã nguồn Java:**
```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public int minKBitFlips(int[] nums, int k) {
        Queue<Integer> q = new LinkedList<>();
        int flips = 0;
        
        for (int i = 0; i < nums.length; i++) {
            if (!q.isEmpty() && q.peek() < i) {
                q.poll();
            }
            
            // Nếu trạng thái bit hiện tại (sau các lần lật) là 0
            if (q.size() % 2 == nums[i]) {
                if (i + k - 1 >= nums.length) {
                    return -1;
                }
                q.offer(i + k - 1);
                flips++;
            }
        }
        return flips;
    }
}
```
**Độ phức tạp:**
- Time: O(N) do duyệt 1 vòng và mỗi phần tử vào Queue 1 lần.
- Space: O(K) vì Queue giữ nhiều nhất K giá trị.

---

## 27. Process Tasks Using Servers (LeetCode 1882)
**Đề bài chi tiết:**  
Bạn có một số server và một mảng các task chờ được xử lý. Khi một task xuất hiện ở thời điểm `j`, bạn sẽ giao nó cho một server rảnh rỗi có sức mạnh (`weight`) nhỏ nhất. Nếu không có server rảnh, task phải chờ. Trả về mảng lưu trữ server index được chỉ định cho mỗi task.

**Phân tích thuật toán:**  
Sử dụng hai Priority Queues:
- `freeServers` (ưu tiên weight, sau đó index): Lưu server đang rảnh `[weight, index]`.
- `busyServers` (ưu tiên thời gian hoàn thành): Lưu server đang bận `[thời_gian_rảnh, weight, index]`.
Khi duyệt qua các task, lấy server rảnh. Nếu không có server rảnh, ta tua thời gian đến khi server tiếp theo rảnh từ `busyServers`. Đẩy server từ `busyServers` sang `freeServers`.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class Solution {
    public int[] assignTasks(int[] servers, int[] tasks) {
        PriorityQueue<int[]> freeServers = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0]) return a[0] - b[0];
            return a[1] - b[1];
        });
        
        PriorityQueue<int[]> busyServers = new PriorityQueue<>((a, b) -> {
            if (a[0] != b[0]) return a[0] - b[0];
            if (a[1] != b[1]) return a[1] - b[1];
            return a[2] - b[2];
        });
        
        for (int i = 0; i < servers.length; i++) {
            freeServers.offer(new int[]{servers[i], i});
        }
        
        int[] ans = new int[tasks.length];
        for (int i = 0; i < tasks.length; i++) {
            int time = Math.max(i, busyServers.isEmpty() ? 0 : busyServers.peek()[0]);
            if (freeServers.isEmpty()) {
                time = busyServers.peek()[0];
            }
            
            while (!busyServers.isEmpty() && busyServers.peek()[0] <= time) {
                int[] server = busyServers.poll();
                freeServers.offer(new int[]{server[1], server[2]});
            }
            
            int[] server = freeServers.poll();
            ans[i] = server[1];
            busyServers.offer(new int[]{time + tasks[i], server[0], server[1]});
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time: O(M log N) với M là số task, N là số server.
- Space: O(N) để lưu 2 Priority Queues.

---

## 28. Single-Threaded CPU (LeetCode 1834)
**Đề bài chi tiết:**  
Cho một CPU đơn luồng và mảng các `tasks` với thời điểm xuất hiện và thời gian xử lý. Khi rảnh, CPU sẽ chọn task đang chờ có thời gian xử lý ngắn nhất (nếu bằng thì ưu tiên index nhỏ). Trả về thứ tự các task được xử lý.

**Phân tích thuật toán:**  
Dùng Priority Queue (`minHeap`) để chứa các task đang chờ.
Lưu lại mảng task ban đầu cùng index, rồi sắp xếp theo thời điểm xuất hiện.
Biến `time` duy trì thời gian hiện tại. Lần lượt đưa các task có `enqueueTime <= time` vào `minHeap`. Sau đó lấy task đầu tiên từ Heap để xử lý, cộng `processingTime` vào `time`.

**Mã nguồn Java:**
```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {
    public int[] getOrder(int[][] tasks) {
        int n = tasks.length;
        int[][] extTasks = new int[n][3];
        for (int i = 0; i < n; i++) {
            extTasks[i][0] = tasks[i][0];
            extTasks[i][1] = tasks[i][1];
            extTasks[i][2] = i;
        }
        
        Arrays.sort(extTasks, (a, b) -> a[0] - b[0]);
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
            if (a[1] != b[1]) return a[1] - b[1];
            return a[2] - b[2];
        });
        
        int[] ans = new int[n];
        int time = 0, i = 0, idx = 0;
        
        while (idx < n) {
            if (pq.isEmpty() && time < extTasks[i][0]) {
                time = extTasks[i][0];
            }
            while (i < n && extTasks[i][0] <= time) {
                pq.offer(extTasks[i]);
                i++;
            }
            int[] task = pq.poll();
            time += task[1];
            ans[idx++] = task[2];
        }
        return ans;
    }
}
```
**Độ phức tạp:**
- Time: O(N log N) cho sắp xếp và các thao tác của Priority Queue.
- Space: O(N) để lưu trữ mảng mở rộng và Heap.

---

## 29. Seat Reservation Manager (LeetCode 1845)
**Đề bài chi tiết:**  
Thiết kế một hệ thống đặt ghế. Khởi tạo với số ghế từ `1` đến `n`. `reserve()` lấy và trả về ghế nhỏ nhất đang còn trống. `unreserve(seatNumber)` trả ghế `seatNumber` về trạng thái trống.

**Phân tích thuật toán:**  
Sử dụng Min-Heap (Priority Queue) để lưu trữ danh sách các ghế trống.
Mỗi khi có người đặt, ta `poll()` (lấy phần tử nhỏ nhất). Mỗi khi có người trả, ta `offer()` (đưa vào lại). Để tối ưu không gian lúc khởi tạo, ta chỉ đẩy các ghế đã trả lại vào Queue, và duy trì một biến đếm `marker` cho các ghế chưa từng được sử dụng.

**Mã nguồn Java:**
```java
import java.util.PriorityQueue;

class SeatManager {
    private PriorityQueue<Integer> availableSeats;
    private int marker;

    public SeatManager(int n) {
        availableSeats = new PriorityQueue<>();
        marker = 1; 
    }
    
    public int reserve() {
        if (!availableSeats.isEmpty()) {
            return availableSeats.poll();
        }
        int seat = marker;
        marker++;
        return seat;
    }
    
    public void unreserve(int seatNumber) {
        availableSeats.offer(seatNumber);
    }
}
```
**Độ phức tạp:**
- Time: Khởi tạo O(1). `reserve` O(log K), `unreserve` O(log K) với K là số ghế nằm trong Heap.
- Space: O(N) không gian cho Heap trong trường hợp xấu nhất.

---

## 30. Design a Text Editor (LeetCode 2296)
**Đề bài chi tiết:**  
Thiết kế một Text Editor với con trỏ. Hỗ trợ thao tác thêm text, xóa text (Backspace), trượt con trỏ trái, phải và trả về 10 ký tự cuối cùng ngay trước con trỏ.

**Phân tích thuật toán:**  
Dùng 2 Hàng đợi hai đầu (Deque) để mô phỏng không gian xung quanh con trỏ:
- `left`: chứa chuỗi bên trái con trỏ.
- `right`: chứa chuỗi bên phải con trỏ.
Khi thêm ký tự, đưa vào đuôi `left`. Xóa ký tự, pop khỏi đuôi `left`. Dịch trái con trỏ: pop đuôi `left` đẩy vào đầu `right`. Dịch phải: pop đầu `right` đẩy vào đuôi `left`.

**Mã nguồn Java:**
```java
import java.util.Deque;
import java.util.LinkedList;

class TextEditor {
    private Deque<Character> left;
    private Deque<Character> right;

    public TextEditor() {
        left = new LinkedList<>();
        right = new LinkedList<>();
    }
    
    public void addText(String text) {
        for (char c : text.toCharArray()) {
            left.offerLast(c);
        }
    }
    
    public int deleteText(int k) {
        int count = 0;
        while (!left.isEmpty() && count < k) {
            left.pollLast();
            count++;
        }
        return count;
    }
    
    public String cursorLeft(int k) {
        while (!left.isEmpty() && k > 0) {
            right.offerFirst(left.pollLast());
            k--;
        }
        return getLeftText();
    }
    
    public String cursorRight(int k) {
        while (!right.isEmpty() && k > 0) {
            left.offerLast(right.pollFirst());
            k--;
        }
        return getLeftText();
    }
    
    private String getLeftText() {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        Deque<Character> temp = new LinkedList<>();
        while (!left.isEmpty() && count < 10) {
            temp.offerFirst(left.pollLast());
            count++;
        }
        while (!temp.isEmpty()) {
            char c = temp.pollFirst();
            left.offerLast(c);
            sb.append(c);
        }
        return sb.toString();
    }
}
```
**Độ phức tạp:**
- Time: `addText` O(L). `deleteText` O(k). `cursorLeft/Right` O(k). `getLeftText` O(1).
- Space: O(N) chiều dài văn bản hiện tại.
