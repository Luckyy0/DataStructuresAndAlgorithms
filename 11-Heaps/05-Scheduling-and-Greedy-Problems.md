# Lịch trình và Thuật toán Tham lam với Heap - Bài tập (Scheduling and Greedy with Heap Problems)

Tài liệu này cung cấp 30 bài tập ứng dụng Priority Queue (Heap) cho các chiến lược tham lam (Greedy) và lập lịch (Scheduling). 10 bài đầu tiên có lời giải mã nguồn Java chi tiết. 

---

## 1. Task Scheduler (LeetCode 621)
**Đề bài chi tiết**: Cho một mảng các ký tự đại diện cho các tác vụ CPU và một số nguyên `n` (thời gian làm mát - cooldown). Hai tác vụ giống nhau phải cách nhau ít nhất `n` đơn vị thời gian. Cần tìm tổng thời gian ngắn nhất để hoàn thành tất cả.
**Phân tích thuật toán**: Dùng mảng đếm tần suất. Max Heap lưu tần suất các task. Ở mỗi chu kỳ kích thước `n+1`, lấy ra tối đa `n+1` task khác nhau (ưu tiên task có tần suất cao nhất) và giảm tần suất. Task nào còn tần suất thì đẩy vào queue để vòng sau xét tiếp.
**Mã nguồn Java**:
```java
public int leastInterval(char[] tasks, int n) {
    int[] freq = new int[26];
    for (char c : tasks) freq[c - 'A']++;
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
    for (int f : freq) {
        if (f > 0) pq.offer(f);
    }
    int time = 0;
    while (!pq.isEmpty()) {
        List<Integer> waitList = new ArrayList<>();
        int cycle = n + 1;
        while (cycle > 0 && !pq.isEmpty()) {
            int current = pq.poll();
            if (current > 1) waitList.add(current - 1);
            time++;
            cycle--;
        }
        for (int w : waitList) pq.offer(w);
        if (!pq.isEmpty()) time += cycle; // Idle time
    }
    return time;
}
```
**Độ phức tạp**: $O(N)$ thời gian (Heap có tối đa 26 phần tử), $O(1)$ không gian.

---

## 2. Meeting Rooms II (LeetCode 253)
**Đề bài chi tiết**: Cho danh sách thời gian bắt đầu và kết thúc của các cuộc họp. Tìm số lượng phòng họp tối thiểu.
**Phân tích thuật toán**: Sort các meeting theo start time. Dùng Min Heap lưu các end time. Nếu start time của meeting tiếp theo >= đỉnh của Heap (end time sớm nhất), tái sử dụng phòng (poll Heap). Cuối cùng, số lượng phòng = kích thước của Heap.
**Mã nguồn Java**:
```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals == null || intervals.length == 0) return 0;
    Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    pq.offer(intervals[0][1]);
    for (int i = 1; i < intervals.length; i++) {
        if (intervals[i][0] >= pq.peek()) pq.poll();
        pq.offer(intervals[i][1]);
    }
    return pq.size();
}
```
**Độ phức tạp**: $O(N \log N)$ do sắp xếp và thao tác Heap, không gian $O(N)$.

---

## 3. Minimum Number of Refueling Stops (LeetCode 871)
**Đề bài chi tiết**: Từ điểm $0$ đến đích $target$. Xe có $startFuel$. Các trạm xăng được cho bởi khoảng cách từ $0$ và lượng xăng. Tìm số trạm đổ xăng ít nhất.
**Phân tích thuật toán**: Lái xe đi xa nhất có thể. Dùng Max Heap lưu lượng xăng ở các trạm đã đi qua (nhưng chưa đổ). Khi hết xăng, lấy trạm có nhiều xăng nhất từ Max Heap để đổ. Trả về -1 nếu Heap trống mà chưa tới đích.
**Mã nguồn Java**:
```java
public int minRefuelStops(int target, int startFuel, int[][] stations) {
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
    int fuel = startFuel, prev = 0, stops = 0;
    for (int[] station : stations) {
        int dist = station[0] - prev;
        fuel -= dist;
        while (fuel < 0 && !pq.isEmpty()) {
            fuel += pq.poll();
            stops++;
        }
        if (fuel < 0) return -1;
        pq.offer(station[1]);
        prev = station[0];
    }
    fuel -= (target - prev);
    while (fuel < 0 && !pq.isEmpty()) {
        fuel += pq.poll();
        stops++;
    }
    return fuel >= 0 ? stops : -1;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 4. Course Schedule III (LeetCode 630)
**Đề bài chi tiết**: Có $n$ khóa học với duration và lastDay. Tìm số lượng khóa học tối đa có thể hoàn thành.
**Phân tích thuật toán**: Sort các khóa học theo lastDay. Dùng Max Heap theo dõi duration của các khóa đã chọn. Nếu tổng thời gian vượt quá lastDay của khóa hiện tại, pop khóa có duration lớn nhất trong Heap.
**Mã nguồn Java**:
```java
public int scheduleCourse(int[][] courses) {
    Arrays.sort(courses, (a, b) -> a[1] - b[1]);
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
    int time = 0;
    for (int[] c : courses) {
        time += c[0];
        pq.offer(c[0]);
        if (time > c[1]) time -= pq.poll();
    }
    return pq.size();
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 5. Maximum Average Pass Ratio (LeetCode 1792)
**Đề bài chi tiết**: Cho danh sách các lớp học với số sinh viên qua môn và tổng số. Bạn được thêm $extraStudents$ sinh viên xuất sắc vào các lớp. Tối đa hóa tỷ lệ đậu trung bình.
**Phân tích thuật toán**: Lợi ích (gain) khi thêm 1 sinh viên vào lớp $(pass, total)$ là: $\frac{pass+1}{total+1} - \frac{pass}{total}$. Dùng Max Heap lưu các lớp dựa trên lợi ích này. Mỗi lần nạp 1 sinh viên, update lớp và ném lại vào Heap.
**Mã nguồn Java**:
```java
public double maxAverageRatio(int[][] classes, int extraStudents) {
    PriorityQueue<double[]> pq = new PriorityQueue<>(
        (a, b) -> Double.compare(gain(b[0], b[1]), gain(a[0], a[1]))
    );
    for (int[] c : classes) pq.offer(new double[]{c[0], c[1]});
    while (extraStudents-- > 0) {
        double[] curr = pq.poll();
        curr[0]++; curr[1]++;
        pq.offer(curr);
    }
    double sum = 0;
    while (!pq.isEmpty()) {
        double[] c = pq.poll();
        sum += c[0] / c[1];
    }
    return sum / classes.length;
}
private double gain(double p, double t) {
    return (p + 1) / (t + 1) - (p / t);
}
```
**Độ phức tạp**: $O(K \log N + N \log N)$, $K$ là extraStudents.

---

## 6. Seat Reservation Manager (LeetCode 1845)
**Đề bài chi tiết**: Thiết kế class quản lý ghế từ 1 đến $n$. `reserve()` đặt ghế có số nhỏ nhất còn trống. `unreserve(seatNumber)` trả ghế lại.
**Phân tích thuật toán**: Sử dụng Min Heap khởi tạo với số từ 1 đến $n$. Lấy ghế thì `poll()`, trả ghế thì `offer()`.
**Mã nguồn Java**:
```java
class SeatManager {
    PriorityQueue<Integer> pq;
    public SeatManager(int n) {
        pq = new PriorityQueue<>();
        for (int i = 1; i <= n; i++) pq.offer(i);
    }
    public int reserve() {
        return pq.poll();
    }
    public void unreserve(int seatNumber) {
        pq.offer(seatNumber);
    }
}
```
*(Có thể tối ưu bỏ đi việc add từ 1 tới $n$ bằng biến `marker`).*
**Độ phức tạp**: `reserve`/`unreserve` $O(\log N)$. Khởi tạo $O(N)$ hoặc $O(1)$ nếu tối ưu.

---

## 7. Single-Threaded CPU (LeetCode 1834)
**Đề bài chi tiết**: Có các CPU tasks với enqueueTime và processingTime. CPU thực hiện task có processing time ngắn nhất. Trả về thứ tự các task được xử lý.
**Phân tích thuật toán**: Sort mảng task theo enqueueTime. Duy trì một Min Heap sắp xếp theo processingTime (và index nếu tie). Dùng biến thời gian để mô phỏng CPU.
**Mã nguồn Java**:
```java
public int[] getOrder(int[][] tasks) {
    int n = tasks.length;
    int[][] extTasks = new int[n][3];
    for(int i=0; i<n; i++) {
        extTasks[i][0] = tasks[i][0];
        extTasks[i][1] = tasks[i][1];
        extTasks[i][2] = i;
    }
    Arrays.sort(extTasks, (a, b) -> a[0] - b[0]);
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> 
        a[1] == b[1] ? a[2] - b[2] : a[1] - b[1]
    );
    int[] ans = new int[n];
    int time = 0, idx = 0, ansIdx = 0;
    while(ansIdx < n) {
        while(idx < n && extTasks[idx][0] <= time) {
            pq.offer(extTasks[idx++]);
        }
        if(pq.isEmpty()) {
            time = extTasks[idx][0];
            continue;
        }
        int[] curr = pq.poll();
        time += curr[1];
        ans[ansIdx++] = curr[2];
    }
    return ans;
}
```
**Độ phức tạp**: $O(N \log N)$ cho thời gian, không gian $O(N)$.

---

## 8. Network Delay Time (Dijkstra overview) (LeetCode 743)
**Đề bài chi tiết**: Tính thời gian để tín hiệu truyền đến tất cả các node trong đồ thị mạng có hướng.
**Phân tích thuật toán**: Đây là dạng cơ bản của thuật toán Dijkstra. Dùng Min Heap để luôn thăm node có khoảng cách thời gian ngắn nhất.
**Mã nguồn Java**:
```java
public int networkDelayTime(int[][] times, int n, int k) {
    Map<Integer, List<int[]>> graph = new HashMap<>();
    for (int[] t : times) {
        graph.computeIfAbsent(t[0], x -> new ArrayList<>()).add(new int[]{t[1], t[2]});
    }
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
    pq.offer(new int[]{k, 0});
    boolean[] visited = new boolean[n + 1];
    int res = 0;
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int u = curr[0], dist = curr[1];
        if (visited[u]) continue;
        visited[u] = true;
        res = dist;
        n--;
        if (graph.containsKey(u)) {
            for (int[] next : graph.get(u)) {
                if (!visited[next[0]]) {
                    pq.offer(new int[]{next[0], dist + next[1]});
                }
            }
        }
    }
    return n == 0 ? res : -1;
}
```
**Độ phức tạp**: $O((V+E) \log V)$ thời gian. $O(V+E)$ không gian.

---

## 9. Cheapest Flights Within K Stops (LeetCode 787)
**Đề bài chi tiết**: Tìm vé máy bay rẻ nhất đi từ $src$ đến $dst$ mà chỉ dừng tối đa $K$ lần.
**Phân tích thuật toán**: Dijkstra có điều chỉnh. Thay vì chỉ tối ưu khoảng cách (chi phí vé), ta cần theo dõi số chặng bay (stops). Nếu một đường đi dài chi phí hơn nhưng số chặng ít hơn, nó vẫn hợp lệ. Do đó, state bao gồm cả $stops$. (Note: BFS/Bellman-Ford thực tế tối ưu hơn bài này, nhưng đây là demo Dijkstra state).
**Mã nguồn Java**:
```java
public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
    Map<Integer, List<int[]>> graph = new HashMap<>();
    for (int[] f : flights) {
        graph.computeIfAbsent(f[0], x -> new ArrayList<>()).add(new int[]{f[1], f[2]});
    }
    // [node, cost, stops]
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
    pq.offer(new int[]{src, 0, 0});
    int[] stopsCount = new int[n];
    Arrays.fill(stopsCount, Integer.MAX_VALUE);
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int u = curr[0], cost = curr[1], stops = curr[2];
        
        if (u == dst) return cost;
        if (stops > k || stops >= stopsCount[u]) continue;
        stopsCount[u] = stops;
        
        if (graph.containsKey(u)) {
            for (int[] next : graph.get(u)) {
                pq.offer(new int[]{next[0], cost + next[1], stops + 1});
            }
        }
    }
    return -1;
}
```
**Độ phức tạp**: $O(E \log (V \times K))$ thời gian.

---

## 10. Swim in Rising Water (LeetCode 778)
**Đề bài chi tiết**: Trận lũ tăng mức nước mỗi phút. Tìm mức thời gian nhỏ nhất để đi từ góc trái trên tới góc phải dưới, chỉ có thể bơi nếu mực nước >= độ cao ô vuông.
**Phân tích thuật toán**: Biến thể của Dijkstra trên Grid. Ta muốn đường đi sao cho "Max peak" (đỉnh lớn nhất trên đường) là nhỏ nhất. Dùng Min Heap theo chiều cao max.
**Mã nguồn Java**:
```java
public int swimInWater(int[][] grid) {
    int n = grid.length;
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
    pq.offer(new int[]{0, 0, grid[0][0]});
    boolean[][] visited = new boolean[n][n];
    visited[0][0] = true;
    int[][] dirs = {{0,1},{0,-1},{1,0},{-1,0}};
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int r = curr[0], c = curr[1], maxLevel = curr[2];
        if (r == n - 1 && c == n - 1) return maxLevel;
        for (int[] d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < n && nc >= 0 && nc < n && !visited[nr][nc]) {
                visited[nr][nc] = true;
                pq.offer(new int[]{nr, nc, Math.max(maxLevel, grid[nr][nc])});
            }
        }
    }
    return 0;
}
```
**Độ phức tạp**: $O(N^2 \log N)$ thời gian. $O(N^2)$ không gian.

---

## 11. Minimum Cost to Connect Sticks (LeetCode 1167)
**Đề bài chi tiết**: Bạn có một số thanh gỗ với độ dài khác nhau. Bạn có thể kết nối bất kỳ hai thanh nào với chi phí bằng tổng độ dài của chúng. Quá trình này lặp lại cho đến khi chỉ còn một thanh. Tìm chi phí tối thiểu để kết nối tất cả các thanh.
**Phân tích thuật toán**: Sử dụng thuật toán tham lam. Luôn luôn chọn hai thanh ngắn nhất để kết nối đầu tiên. Chi phí của chúng sẽ được cộng dồn vào các bước sau, nên ghép những thanh nhỏ sớm sẽ làm giảm tổng chi phí. Ta dùng Min Heap để liên tục lấy ra 2 phần tử nhỏ nhất, cộng lại rồi nhét ngược trở lại vào Heap.
**Mã nguồn Java**:
```java
public int connectSticks(int[] sticks) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    for (int stick : sticks) {
        pq.offer(stick);
    }
    int totalCost = 0;
    while (pq.size() > 1) {
        int cost = pq.poll() + pq.poll();
        totalCost += cost;
        pq.offer(cost);
    }
    return totalCost;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 12. Find Median from Data Stream (LeetCode 295)
**Đề bài chi tiết**: Thiết kế một cấu trúc dữ liệu hỗ trợ thêm số nguyên từ luồng dữ liệu vào và tìm giá trị trung vị (median) của tất cả các phần tử hiện có.
**Phân tích thuật toán**: Dùng hai Heaps. `Max Heap` chứa nửa nhỏ hơn của dãy số, và `Min Heap` chứa nửa lớn hơn. Kích thước của `Max Heap` có thể bằng hoặc lớn hơn `Min Heap` đúng 1 phần tử. Trung vị sẽ là đỉnh của `Max Heap` (nếu tổng số lượng lẻ) hoặc trung bình cộng hai đỉnh của hai Heap (nếu tổng số lượng chẵn).
**Mã nguồn Java**:
```java
class MedianFinder {
    PriorityQueue<Integer> maxHeap; // Nửa nhỏ
    PriorityQueue<Integer> minHeap; // Nửa lớn

    public MedianFinder() {
        maxHeap = new PriorityQueue<>((a, b) -> b - a);
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
**Độ phức tạp**: $O(\log N)$ cho mỗi lần `addNum`, $O(1)$ cho `findMedian`, không gian $O(N)$.

---

## 13. Maximum Performance of a Team (LeetCode 1383)
**Đề bài chi tiết**: Cho $n$ kỹ sư với `speed` và `efficiency`. Cần chọn tối đa $k$ kỹ sư để tối đa hóa hiệu suất = `(tổng speed) * (efficiency nhỏ nhất trong nhóm)`.
**Phân tích thuật toán**: Sắp xếp các kỹ sư theo thứ tự `efficiency` giảm dần. Duyệt qua từng kỹ sư, lúc này kỹ sư hiện tại luôn có `efficiency` nhỏ nhất trong nhóm đang xét. Sử dụng Min Heap để duy trì nhóm $k$ kỹ sư có `speed` cao nhất. Nếu Heap vượt quá $k$, loại bỏ người có `speed` thấp nhất.
**Mã nguồn Java**:
```java
public int maxPerformance(int n, int[] speed, int[] efficiency, int k) {
    int[][] engineers = new int[n][2];
    for (int i = 0; i < n; i++) {
        engineers[i] = new int[]{efficiency[i], speed[i]};
    }
    Arrays.sort(engineers, (a, b) -> b[0] - a[0]); // Sắp xếp efficiency giảm dần
    
    PriorityQueue<Integer> pq = new PriorityQueue<>(); // Min Heap lưu speed
    long totalSpeed = 0, maxPerf = 0;
    
    for (int[] eng : engineers) {
        pq.offer(eng[1]);
        totalSpeed += eng[1];
        if (pq.size() > k) {
            totalSpeed -= pq.poll();
        }
        maxPerf = Math.max(maxPerf, totalSpeed * eng[0]);
    }
    
    return (int) (maxPerf % 1000000007);
}
```
**Độ phức tạp**: $O(N \log N + N \log K)$ thời gian, $O(N + K)$ không gian.

---

## 14. Process Tasks Using Servers (LeetCode 1882)
**Đề bài chi tiết**: Có $n$ servers với `weight` và `capacity`, và $m$ tasks đến mỗi giây. Task được gán cho server rảnh có `weight` nhỏ nhất (nếu bằng thì index nhỏ nhất). Tìm mảng chỉ định server cho mỗi task.
**Phân tích thuật toán**: Dùng 2 Min Heaps: `freeServers` lưu server sẵn sàng theo (weight, index) và `busyServers` lưu server đang xử lý task theo (thời gian rảnh, weight, index). Ứng với mỗi task tại thời điểm $j$, giải phóng các server đã xong task từ `busyServers` qua `freeServers`. Nếu `freeServers` trống, lấy thời gian rảnh sớm nhất từ `busyServers` để gán task tiếp theo.
**Mã nguồn Java**:
```java
public int[] assignTasks(int[] servers, int[] tasks) {
    // {weight, index, availableTime}
    PriorityQueue<int[]> freeServers = new PriorityQueue<>((a, b) -> 
        a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]
    );
    PriorityQueue<int[]> busyServers = new PriorityQueue<>((a, b) -> 
        a[2] == b[2] ? (a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]) : a[2] - b[2]
    );
    
    for (int i = 0; i < servers.length; i++) {
        freeServers.offer(new int[]{servers[i], i, 0});
    }
    
    int[] ans = new int[tasks.length];
    for (int j = 0; j < tasks.length; j++) {
        while (!busyServers.isEmpty() && busyServers.peek()[2] <= j) {
            freeServers.offer(busyServers.poll());
        }
        if (freeServers.isEmpty()) {
            int[] curr = busyServers.poll();
            ans[j] = curr[1];
            curr[2] += tasks[j];
            busyServers.offer(curr);
        } else {
            int[] curr = freeServers.poll();
            ans[j] = curr[1];
            curr[2] = j + tasks[j];
            busyServers.offer(curr);
        }
    }
    return ans;
}
```
**Độ phức tạp**: $O((N + M) \log N)$ thời gian, $O(N)$ không gian.

---

## 15. Advantage Shuffle (LeetCode 870)
**Đề bài chi tiết**: Cho mảng $A$ và $B$ cùng kích thước. Hoán vị $A$ để tối đa hóa số lượng vị trí mà $A[i] > B[i]$ (Lợi thế - Advantage).
**Phân tích thuật toán**: Dùng tư tưởng Greedy (chiến thuật ngựa đua của Điền Kỵ). Dùng Max Heap để lưu các phần tử của $B$ cùng index. Sắp xếp $A$. Nếu phần tử lớn nhất hiện tại của $A$ thắng phần tử lớn nhất của $B$, gán nó. Nếu không, gán phần tử nhỏ nhất của $A$ cho phần tử lớn nhất của $B$ để "hy sinh".
**Mã nguồn Java**:
```java
public int[] advantageCount(int[] nums1, int[] nums2) {
    Arrays.sort(nums1);
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> b[0] - a[0]); // Max Heap {val, index}
    for (int i = 0; i < nums2.length; i++) {
        pq.offer(new int[]{nums2[i], i});
    }
    
    int[] res = new int[nums1.length];
    int left = 0, right = nums1.length - 1;
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int val = curr[0], idx = curr[1];
        if (nums1[right] > val) {
            res[idx] = nums1[right--];
        } else {
            res[idx] = nums1[left++];
        }
    }
    return res;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 16. Find the Kth Smallest Sum of a Matrix (LeetCode 1439)
**Đề bài chi tiết**: Cho ma trận kích thước $m \times n$ với các hàng đã được sắp xếp. Bạn được chọn đúng một phần tử ở mỗi hàng để tạo ra mảng có tổng nhỏ nhất. Tìm tổng nhỏ thứ $K$.
**Phân tích thuật toán**: Khởi tạo tổng nhỏ nhất là tổng cột đầu tiên. Dùng Min Heap để sinh các trạng thái tiếp theo tương tự như BFS / Dijkstra. Trạng thái gồm (tổng, mảng các chỉ số phần tử trong mỗi hàng). Tránh thăm lại bằng HashSet. Ở mỗi bước pop từ Heap, thử tăng chỉ số của một hàng bất kỳ lên 1.
**Mã nguồn Java**:
```java
public int kthSmallest(int[][] mat, int k) {
    int m = mat.length, n = mat[0].length;
    PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]); 
    Set<String> visited = new HashSet<>();
    
    int initialSum = 0;
    int[] initialPointers = new int[m + 1];
    for (int i = 0; i < m; i++) {
        initialSum += mat[i][0];
    }
    initialPointers[0] = initialSum;
    
    minHeap.offer(initialPointers);
    visited.add(Arrays.toString(initialPointers));
    
    while (k-- > 1) {
        int[] curr = minHeap.poll();
        for (int i = 0; i < m; i++) {
            if (curr[i + 1] + 1 < n) {
                int[] next = curr.clone();
                next[i + 1]++;
                next[0] = next[0] - mat[i][curr[i + 1]] + mat[i][next[i + 1]];
                String hash = Arrays.toString(next);
                if (!visited.contains(hash)) {
                    visited.add(hash);
                    minHeap.offer(next);
                }
            }
        }
    }
    return minHeap.poll()[0];
}
```
**Độ phức tạp**: $O(K \times M \log(\text{trạng thái}))$, $O(K \times M)$ không gian.

---

## 17. Reorganize String (LeetCode 767)
**Đề bài chi tiết**: Sắp xếp lại chuỗi sao cho không có 2 ký tự nào giống nhau đứng cạnh nhau.
**Phân tích thuật toán**: Dùng đếm tần suất và Max Heap để ưu tiên xếp các ký tự xuất hiện nhiều nhất. Pop ra 2 ký tự khác nhau có tần suất lớn nhất, nối vào chuỗi kết quả, giảm tần suất và đẩy lại vào Heap nếu còn lớn hơn 0. Nếu cuối cùng trong Heap còn 1 ký tự mà tần suất > 1 thì không thể thoả mãn.
**Mã nguồn Java**:
```java
public String reorganizeString(String s) {
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    
    PriorityQueue<Character> maxHeap = new PriorityQueue<>((a, b) -> freq[b - 'a'] - freq[a - 'a']);
    for (int i = 0; i < 26; i++) {
        if (freq[i] > 0) maxHeap.offer((char) (i + 'a'));
    }
    
    StringBuilder sb = new StringBuilder();
    while (maxHeap.size() > 1) {
        char current = maxHeap.poll();
        char next = maxHeap.poll();
        sb.append(current).append(next);
        freq[current - 'a']--;
        freq[next - 'a']--;
        
        if (freq[current - 'a'] > 0) maxHeap.offer(current);
        if (freq[next - 'a'] > 0) maxHeap.offer(next);
    }
    
    if (!maxHeap.isEmpty()) {
        char last = maxHeap.poll();
        if (freq[last - 'a'] > 1) return "";
        sb.append(last);
    }
    return sb.toString();
}
```
**Độ phức tạp**: $O(N \log 26) = O(N)$ thời gian, $O(26) = O(1)$ không gian.

---

## 18. Min Deletions to Make Frequencies Unique (LeetCode 1647)
**Đề bài chi tiết**: Bạn có thể xóa một số ký tự khỏi chuỗi để tất cả các tần suất xuất hiện của các ký tự còn lại là duy nhất. Tìm số lượng ký tự tối thiểu cần xoá.
**Phân tích thuật toán**: Đếm tần suất các ký tự và dùng Max Heap để lưu các tần suất này. Lấy ra tần suất lớn nhất, nếu trùng với tần suất ở đỉnh Heap tiếp theo, giảm tần suất của nó đi 1 (tức là xoá 1 ký tự) và đẩy lại vào Heap (nếu > 0). Đếm số lượng lần xoá.
**Mã nguồn Java**:
```java
public int minDeletions(String s) {
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    
    PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
    for (int f : freq) {
        if (f > 0) pq.offer(f);
    }
    
    int deletions = 0;
    while (pq.size() > 1) {
        int top = pq.poll();
        if (pq.peek() != null && pq.peek() == top) {
            if (top - 1 > 0) {
                pq.offer(top - 1);
            }
            deletions++;
        }
    }
    return deletions;
}
```
**Độ phức tạp**: $O(N)$ để đếm tần suất, $O(K \log K)$ với $K \le 26$ số phần tử Heap, tổng là $O(N)$. Không gian $O(1)$.

---

## 19. Meeting Rooms III (LeetCode 2402)
**Đề bài chi tiết**: Có $n$ phòng họp đánh số $0$ đến $n-1$. Phân bổ các cuộc họp (start, end) vào phòng có số nhỏ nhất còn trống. Nếu không có, cuộc họp phải đợi tới khi phòng sớm nhất rảnh (giữ nguyên duration). Trả về phòng tổ chức nhiều cuộc họp nhất.
**Phân tích thuật toán**: Sắp xếp cuộc họp theo start time. Dùng 2 Min Heaps: `freeRooms` lưu id phòng rảnh, `usedRooms` lưu cặp `[endTime, roomId]`. Mỗi khi có meeting mới, thả các phòng từ `usedRooms` có $endTime \le start$ vào `freeRooms`. Nếu `freeRooms` có sẵn thì dùng phòng id nhỏ nhất, nếu không lấy phòng rảnh sớm nhất từ `usedRooms`, cập nhật endTime.
**Mã nguồn Java**:
```java
public int mostBooked(int n, int[][] meetings) {
    Arrays.sort(meetings, (a, b) -> Integer.compare(a[0], b[0]));
    PriorityQueue<Integer> freeRooms = new PriorityQueue<>();
    // {endTime, roomIndex}
    PriorityQueue<long[]> usedRooms = new PriorityQueue<>((a, b) -> 
        a[0] == b[0] ? Long.compare(a[1], b[1]) : Long.compare(a[0], b[0])
    );
    int[] count = new int[n];
    for (int i = 0; i < n; i++) freeRooms.offer(i);
    
    for (int[] m : meetings) {
        int start = m[0], end = m[1];
        while (!usedRooms.isEmpty() && usedRooms.peek()[0] <= start) {
            freeRooms.offer((int) usedRooms.poll()[1]);
        }
        if (!freeRooms.isEmpty()) {
            int room = freeRooms.poll();
            count[room]++;
            usedRooms.offer(new long[]{end, room});
        } else {
            long[] roomData = usedRooms.poll();
            int room = (int) roomData[1];
            count[room]++;
            usedRooms.offer(new long[]{roomData[0] + (end - start), room});
        }
    }
    int maxCount = 0, bestRoom = 0;
    for (int i = 0; i < n; i++) {
        if (count[i] > maxCount) {
            maxCount = count[i];
            bestRoom = i;
        }
    }
    return bestRoom;
}
```
**Độ phức tạp**: $O(M \log M + M \log N)$ thời gian ($M$ là số cuộc họp).

---

## 20. Total Cost to Hire K Workers (LeetCode 2462)
**Đề bài chi tiết**: Cho mảng chi phí thuê workers. Mỗi lần bạn có thể chọn 1 worker trong $candidates$ workers ở đầu hoặc cuối mảng với chi phí rẻ nhất (nếu bằng thì ưu tiên index nhỏ). Tổng cộng chọn $k$ workers.
**Phân tích thuật toán**: Sử dụng hai Min Heaps, một cho $candidates$ phần tử đầu, một cho $candidates$ phần tử cuối, kết hợp hai con trỏ `left` và `right`. Lấy phần tử nhỏ hơn từ hai Heaps (chú ý index cho trường hợp cost bằng nhau) và bổ sung phần tử tiếp theo từ mảng vào Heap tương ứng.
**Mã nguồn Java**:
```java
public long totalCost(int[] costs, int k, int candidates) {
    PriorityQueue<Integer> pqLeft = new PriorityQueue<>();
    PriorityQueue<Integer> pqRight = new PriorityQueue<>();
    long totalCost = 0;
    int left = 0, right = costs.length - 1;
    
    for (int i = 0; i < candidates && left <= right; i++) {
        pqLeft.offer(costs[left++]);
        if (left <= right) {
            pqRight.offer(costs[right--]);
        }
    }
    
    while (k-- > 0) {
        int leftVal = pqLeft.isEmpty() ? Integer.MAX_VALUE : pqLeft.peek();
        int rightVal = pqRight.isEmpty() ? Integer.MAX_VALUE : pqRight.peek();
        
        if (leftVal <= rightVal) {
            totalCost += pqLeft.poll();
            if (left <= right) {
                pqLeft.offer(costs[left++]);
            }
        } else {
            totalCost += pqRight.poll();
            if (left <= right) {
                pqRight.offer(costs[right--]);
            }
        }
    }
    return totalCost;
}
```
**Độ phức tạp**: $O((K + C) \log C)$ thời gian, với $C$ là candidates.

---

## 21. IPO (LeetCode 502)
**Đề bài chi tiết**: Cho $k$ dự án tối đa có thể hoàn thành, với mảng $profits$ và $capital$. Bạn có một số vốn ban đầu là $w$. Để bắt đầu một dự án $i$, bạn cần có ít nhất $capital[i]$ vốn, và sau khi hoàn thành, bạn thu được $profits[i]$ lợi nhuận cộng thêm vào vốn của mình. Tìm số vốn tối đa cuối cùng.
**Phân tích thuật toán**: Tham lam bằng cách ở mỗi bước luôn chọn dự án có lợi nhuận cao nhất trong số các dự án ta có thể thực hiện (tức là $capital[i] \le w$). Sử dụng mảng/List để sắp xếp các dự án theo `capital` tăng dần. Dùng một con trỏ để duyệt qua mảng này, đẩy tất cả `profits` của các dự án có thể làm vào một Max Heap. Ở mỗi bước, lấy dự án ở đỉnh Max Heap ra để thực hiện và tăng $w$. Lặp lại $k$ lần hoặc đến khi Heap rỗng.
**Mã nguồn Java**:
```java
public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
    int n = profits.length;
    int[][] projects = new int[n][2];
    for (int i = 0; i < n; i++) {
        projects[i][0] = capital[i];
        projects[i][1] = profits[i];
    }
    
    // Sort projects by capital ascending
    Arrays.sort(projects, (a, b) -> a[0] - b[0]);
    
    // Max Heap for profits
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
    
    int ptr = 0;
    for (int i = 0; i < k; i++) {
        while (ptr < n && projects[ptr][0] <= w) {
            maxHeap.offer(projects[ptr][1]);
            ptr++;
        }
        
        if (maxHeap.isEmpty()) {
            break;
        }
        w += maxHeap.poll();
    }
    return w;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian (để sort và đẩy vào Heap), $O(N)$ không gian.

---

## 22. The Skyline Problem (LeetCode 218)
**Đề bài chi tiết**: Cho danh sách các toà nhà với $(left, right, height)$. Tìm danh sách các điểm "đường chân trời" (Skyline) là các điểm giao cắt ngoài cùng của các toà nhà.
**Phân tích thuật toán**: Dùng kĩ thuật Sweep Line (Đường quét). Tách mỗi toà nhà thành 2 sự kiện: điểm bắt đầu $(left, height)$ và điểm kết thúc $(right, height)$. Sắp xếp các sự kiện theo toạ độ $x$. Nếu cùng $x$, điểm bắt đầu ưu tiên toà nhà cao hơn, điểm kết thúc ưu tiên toà nhà thấp hơn. Sử dụng Max Heap để lưu chiều cao của các toà nhà đang cắt qua đường quét. Tại mỗi điểm $x$, thêm hoặc loại bỏ toà nhà khỏi Heap, sau đó xem xét chiều cao tối đa hiện tại có thay đổi không, nếu có thì tạo một điểm chân trời mới. (Ghi chú: dùng `TreeMap` thay cho `PriorityQueue` có hàm `remove()` để tối ưu thời gian xoá thành $O(\log N)$).
**Mã nguồn Java**:
```java
public List<List<Integer>> getSkyline(int[][] buildings) {
    List<List<Integer>> res = new ArrayList<>();
    List<int[]> events = new ArrayList<>();
    
    for (int[] b : buildings) {
        events.add(new int[]{b[0], -b[2]}); // Start point (negative height)
        events.add(new int[]{b[1], b[2]});  // End point (positive height)
    }
    
    Collections.sort(events, (a, b) -> {
        if (a[0] != b[0]) return a[0] - b[0];
        return a[1] - b[1];
    });
    
    // Use TreeMap as a Max Heap that supports O(log N) removal
    TreeMap<Integer, Integer> maxHeap = new TreeMap<>(Collections.reverseOrder());
    maxHeap.put(0, 1);
    int prevMaxHeight = 0;
    
    for (int[] event : events) {
        int x = event[0];
        int h = Math.abs(event[1]);
        boolean isStart = event[1] < 0;
        
        if (isStart) {
            maxHeap.put(h, maxHeap.getOrDefault(h, 0) + 1);
        } else {
            int count = maxHeap.get(h);
            if (count == 1) maxHeap.remove(h);
            else maxHeap.put(h, count - 1);
        }
        
        int currentMaxHeight = maxHeap.firstKey();
        if (currentMaxHeight != prevMaxHeight) {
            res.add(Arrays.asList(x, currentMaxHeight));
            prevMaxHeight = currentMaxHeight;
        }
    }
    return res;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian do dùng `TreeMap`, $O(N)$ không gian.

---

## 23. Path With Minimum Effort (LeetCode 1631)
**Đề bài chi tiết**: Bạn là người leo núi chuẩn bị đi từ ô trên cùng bên trái đến ô dưới cùng bên phải của một bản đồ độ cao. "Effort" của một đường đi là chênh lệch độ cao tuyệt đối lớn nhất giữa hai ô liên tiếp trên đường đi đó. Tìm đường đi có Effort nhỏ nhất.
**Phân tích thuật toán**: Áp dụng thuật toán Dijkstra để tìm "đường đi ngắn nhất" với hàm chi phí được định nghĩa là Effort. Sử dụng Min Heap lưu `[row, col, max_effort_so_far]`. Luôn thăm ô có Effort hiện tại là nhỏ nhất. Cập nhật mảng lưu Effort nhỏ nhất đến từng ô để tránh vòng lặp và tối ưu.
**Mã nguồn Java**:
```java
public int minimumEffortPath(int[][] heights) {
    int m = heights.length, n = heights[0].length;
    int[][] efforts = new int[m][n];
    for (int[] row : efforts) Arrays.fill(row, Integer.MAX_VALUE);
    efforts[0][0] = 0;
    
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
    pq.offer(new int[]{0, 0, 0});
    
    int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}};
    
    while (!pq.isEmpty()) {
        int[] curr = pq.poll();
        int r = curr[0], c = curr[1], currentEffort = curr[2];
        
        if (r == m - 1 && c == n - 1) return currentEffort;
        if (currentEffort > efforts[r][c]) continue;
        
        for (int[] d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                int nextEffort = Math.max(currentEffort, Math.abs(heights[r][c] - heights[nr][nc]));
                if (nextEffort < efforts[nr][nc]) {
                    efforts[nr][nc] = nextEffort;
                    pq.offer(new int[]{nr, nc, nextEffort});
                }
            }
        }
    }
    return 0;
}
```
**Độ phức tạp**: $O(M \times N \log(M \times N))$ thời gian, $O(M \times N)$ không gian.

---

## 24. Last Stone Weight (LeetCode 1046)
**Đề bài chi tiết**: Có một tập hợp các viên đá với trọng lượng khác nhau. Mỗi lượt, chọn hai viên đá nặng nhất đập vào nhau. Nếu bằng nhau, cả hai bị hủy. Nếu khác nhau, viên nhẹ bị hủy, viên nặng còn lại phần chênh lệch trọng lượng. Tìm trọng lượng viên đá cuối cùng (hoặc 0 nếu không còn viên nào).
**Phân tích thuật toán**: Đây là một bài toán mô phỏng sử dụng Max Heap hoàn hảo. Đưa tất cả các viên đá vào Max Heap. Lấy 2 phần tử lớn nhất ra so sánh, lấy hiệu số đẩy lại vào Heap nếu lớn hơn 0. Dừng khi Heap chỉ còn $\le 1$ viên.
**Mã nguồn Java**:
```java
public int lastStoneWeight(int[] stones) {
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
    for (int stone : stones) {
        pq.offer(stone);
    }
    
    while (pq.size() > 1) {
        int s1 = pq.poll();
        int s2 = pq.poll();
        if (s1 != s2) {
            pq.offer(Math.abs(s1 - s2));
        }
    }
    
    return pq.isEmpty() ? 0 : pq.poll();
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 25. Split Array into Consecutive Subsequences (LeetCode 659)
**Đề bài chi tiết**: Cho mảng số nguyên đã sắp xếp tăng dần. Xác định xem có thể chia mảng thành một hoặc nhiều chuỗi con liên tiếp, sao cho mỗi chuỗi có ít nhất 3 phần tử hay không.
**Phân tích thuật toán**: Sử dụng HashMap và Min Heap. Key của Map là số kết thúc một chuỗi con, Value là một Min Heap chứa độ dài của các chuỗi con kết thúc bằng số đó. Khi duyệt phần tử $x$, nếu có chuỗi kết thúc ở $x-1$, ta ghép $x$ vào chuỗi có độ dài ngắn nhất (nhờ Min Heap). Nếu không có, tạo một chuỗi mới bắt đầu bằng $x$ (độ dài 1). Cuối cùng kiểm tra xem có chuỗi nào độ dài $< 3$ không.
**Mã nguồn Java**:
```java
public boolean isPossible(int[] nums) {
    Map<Integer, PriorityQueue<Integer>> map = new HashMap<>();
    
    for (int x : nums) {
        if (!map.containsKey(x)) {
            map.put(x, new PriorityQueue<>());
        }
        
        if (map.containsKey(x - 1) && !map.get(x - 1).isEmpty()) {
            int length = map.get(x - 1).poll();
            map.get(x).offer(length + 1);
        } else {
            map.get(x).offer(1);
        }
    }
    
    for (PriorityQueue<Integer> pq : map.values()) {
        if (!pq.isEmpty() && pq.peek() < 3) {
            return false;
        }
    }
    return true;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian (do thao tác Heap), $O(N)$ không gian.

---

## 26. Minimum Cost to Hire K Workers (LeetCode 857)
**Đề bài chi tiết**: Cần thuê đúng $K$ workers từ $N$ workers. Mỗi người có `quality` và `wage` mong muốn tối thiểu. Khi thuê một nhóm, tất cả phải được trả lương theo cùng một tỉ lệ (`wage` thực tế / `quality`), và tỉ lệ này phải đủ thỏa mãn mong muốn tối thiểu của mọi người trong nhóm. Tìm chi phí tối thiểu.
**Phân tích thuật toán**: Tỉ lệ chung cho cả nhóm sẽ là $\max(wage[i] / quality[i])$ của các workers được chọn. Sắp xếp các worker theo tỉ lệ $wage/quality$ tăng dần. Khi xét đến một worker với tỉ lệ hiện tại (là lớn nhất trong nhóm tính đến thời điểm đó), để giảm chi phí, ta cần tối thiểu hóa tổng `quality` của $K$ người. Sử dụng Max Heap để duy trì $K$ người có `quality` nhỏ nhất.
**Mã nguồn Java**:
```java
public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
    int n = quality.length;
    double[][] workers = new double[n][2];
    for (int i = 0; i < n; i++) {
        workers[i] = new double[]{(double) wage[i] / quality[i], (double) quality[i]};
    }
    Arrays.sort(workers, (a, b) -> Double.compare(a[0], b[0]));
    
    PriorityQueue<Double> pq = new PriorityQueue<>((a, b) -> Double.compare(b, a)); // Max Heap
    double minCost = Double.MAX_VALUE;
    double totalQuality = 0;
    
    for (double[] worker : workers) {
        totalQuality += worker[1];
        pq.offer(worker[1]);
        
        if (pq.size() > k) {
            totalQuality -= pq.poll();
        }
        
        if (pq.size() == k) {
            minCost = Math.min(minCost, totalQuality * worker[0]);
        }
    }
    return minCost;
}
```
**Độ phức tạp**: $O(N \log N + N \log K)$ thời gian, $O(N)$ không gian.

---

## 27. Max Number of Events That Can Be Attended (LeetCode 1353)
**Đề bài chi tiết**: Cho mảng các sự kiện với `[startDay, endDay]`. Mỗi sự kiện bạn chỉ cần dự 1 ngày nằm trong khoảng thời gian đó. Bạn chỉ được dự 1 sự kiện mỗi ngày. Tìm số sự kiện tối đa có thể dự.
**Phân tích thuật toán**: Tham lam bằng cách quét qua từng ngày (Sweep Line) hoặc dùng sự kiện. Sắp xếp các event theo $startDay$. Mỗi ngày, đưa các event đã bắt đầu vào một Min Heap (được sắp xếp theo $endDay$). Bỏ các event đã quá hạn ($endDay < currentDay$). Tham lam dự event có $endDay$ sớm nhất (đỉnh của Heap). Tăng biến thời gian lên 1 ngày.
**Mã nguồn Java**:
```java
public int maxEvents(int[][] events) {
    Arrays.sort(events, (a, b) -> a[0] - b[0]);
    PriorityQueue<Integer> minHeap = new PriorityQueue<>(); // Lưu endDay
    int count = 0, i = 0, n = events.length, day = 0;
    
    while (i < n || !minHeap.isEmpty()) {
        if (minHeap.isEmpty()) {
            day = events[i][0]; // Tua nhanh đến ngày có sự kiện tiếp theo
        }
        while (i < n && events[i][0] <= day) {
            minHeap.offer(events[i][1]);
            i++;
        }
        while (!minHeap.isEmpty() && minHeap.peek() < day) {
            minHeap.poll(); // Loại bỏ sự kiện hết hạn
        }
        if (!minHeap.isEmpty()) { // Tham lam dự sự kiện cấp bách nhất
            minHeap.poll();
            count++;
            day++; // Chỉ dùng được 1 sự kiện mỗi ngày
        }
    }
    return count;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian, $O(N)$ không gian.

---

## 28. Reduce Array Size to The Half (LeetCode 1338)
**Đề bài chi tiết**: Bạn có thể chọn một số lượng tuỳ ý các số (set of numbers) và loại bỏ mọi lần xuất hiện của chúng khỏi mảng $arr$. Chọn tối thiểu bao nhiêu phần tử (về mặt giá trị) để chiều dài mảng bị giảm đi ít nhất một nửa?
**Phân tích thuật toán**: Đếm tần suất xuất hiện của các phần tử. Dùng Max Heap để chứa các tần suất này. Lấy tham lam từng tần suất lớn nhất ra cộng dồn vào `removedCount` cho tới khi $\ge N/2$.
**Mã nguồn Java**:
```java
public int minSetSize(int[] arr) {
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : arr) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }
    
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
    for (int freq : freqMap.values()) {
        maxHeap.offer(freq);
    }
    
    int removed = 0;
    int res = 0;
    int half = arr.length / 2;
    
    while (removed < half) {
        removed += maxHeap.poll();
        res++;
    }
    return res;
}
```
**Độ phức tạp**: $O(N \log N)$ thời gian (hoặc $O(N \log K)$ với $K$ là số phần tử unique), $O(N)$ không gian.

---

## 29. Construct Target Array With Multiple Sums (LeetCode 1354)
**Đề bài chi tiết**: Bạn bắt đầu với mảng $A$ chỉ toàn số 1. Trong mỗi bước, chọn 1 số bất kỳ, thay nó bằng tổng tất cả phần tử trong mảng hiện tại. Hỏi có thể tạo ra mảng $target$ hay không?
**Phân tích thuật toán**: Quá trình đi từ mảng 1 đến mảng target là sinh số tăng dần. Ta đi ngược lại từ $target$ về 1 bằng cách luôn lấy phần tử lớn nhất, trừ đi phần tổng của các số còn lại. Thay vì làm thủ công, ta dùng Max Heap và phép chia dư (`%`) để tối ưu khi số hiện tại lớn hơn tổng rất nhiều lần. Phần tử lớn nhất hiện tại = `maxVal`. Tổng các số còn lại = `sum - maxVal`. Số cũ tại vị trí của `maxVal` là `maxVal % (sum - maxVal)`. Đẩy lại số này vào Heap.
**Mã nguồn Java**:
```java
public boolean isPossible(int[] target) {
    if (target.length == 1) return target[0] == 1;
    PriorityQueue<Long> pq = new PriorityQueue<>(Collections.reverseOrder());
    long totalSum = 0;
    for (int num : target) {
        pq.offer((long) num);
        totalSum += num;
    }
    
    while (pq.peek() > 1) {
        long max = pq.poll();
        long remainSum = totalSum - max;
        
        // Cạnh biên: Nếu tổng số khác = 1, chắc chắn có thể giảm về 1
        if (remainSum == 1) return true;
        
        // Không hợp lệ: remainSum == 0, hoặc max không giảm được
        if (remainSum == 0 || max <= remainSum) return false;
        
        long prev = max % remainSum;
        if (prev == 0) return false;
        
        pq.offer(prev);
        totalSum = remainSum + prev;
    }
    return true;
}
```
**Độ phức tạp**: $O(N \log N \log(\max A))$ thời gian, $O(N)$ không gian.

---

## 30. Minimum Interval to Include Each Query (LeetCode 1851)
**Đề bài chi tiết**: Cho danh sách các đoạn khoảng (intervals) `[left, right]`. Chiều dài của nó là `right - left + 1`. Có danh sách các `queries`. Với mỗi truy vấn $q$, tìm chiều dài ngắn nhất của khoảng bao trùm điểm $q$ ($left \le q \le right$). Trả về -1 nếu không có.
**Phân tích thuật toán**: Dùng kỹ thuật Offline Queries. Sắp xếp lại `intervals` theo $left$ và sắp xếp lại các queries tăng dần (cần nhớ lại chỉ số gốc). Duyệt qua từng truy vấn. Tại mỗi $q$, thêm tất cả intervals có $left \le q$ vào Min Heap, Heap sắp xếp theo chiều dài (độ ưu tiên) và lưu $right$. Sau đó, pop tất cả các đỉnh trong Heap mà $right < q$ vì đã hết hạn. Đỉnh Heap hiện tại chính là đoạn ngắn nhất bao trùm $q$.
**Mã nguồn Java**:
```java
public int[] minInterval(int[][] intervals, int[] queries) {
    Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
    int n = queries.length;
    int[][] qWithIndex = new int[n][2];
    for (int i = 0; i < n; i++) {
        qWithIndex[i] = new int[]{queries[i], i};
    }
    Arrays.sort(qWithIndex, (a, b) -> Integer.compare(a[0], b[0]));
    
    // Min Heap theo chiều dài đoạn
    PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
    int[] ans = new int[n];
    int i = 0;
    
    for (int[] q : qWithIndex) {
        int queryVal = q[0];
        int originalIdx = q[1];
        
        while (i < intervals.length && intervals[i][0] <= queryVal) {
            minHeap.offer(new int[]{intervals[i][1] - intervals[i][0] + 1, intervals[i][1]});
            i++;
        }
        
        while (!minHeap.isEmpty() && minHeap.peek()[1] < queryVal) {
            minHeap.poll();
        }
        
        if (minHeap.isEmpty()) {
            ans[originalIdx] = -1;
        } else {
            ans[originalIdx] = minHeap.peek()[0];
        }
    }
    
    return ans;
}
```
**Độ phức tạp**: $O(N \log N + Q \log Q)$ thời gian, $O(N + Q)$ không gian.
